# aglomeratif
C # 
using System;
using System.Collections.Generic;

namespace AgglomerativeClustering
{
    class Program
    {
        static void Main(string[] args)
        {
            // Kullanıcıdan veri noktalarını aldık
            List<Nokta> noktalar = KullanicidanNoktalariAl();

            // Aglomeratif kümelemeyi tanımladık
            List<Kume> kumeler = AglomeratifKumelenme(noktalar);

            // Kümeleme sonuçlarını yazdırdık
            Console.WriteLine("Kümeleme Sonuçları:");
            for (int i = 0; i < kumeler.Count; i++)
            {
                Console.WriteLine("Küme {0}:", i + 1);
                foreach (Nokta nokta in kumeler[i].Noktalar)
                {
                    Console.WriteLine("({0}, {1})", nokta.X, nokta.Y);
                }
                Console.WriteLine();
            }

            Console.ReadLine();
        }

        static List<Nokta> KullanicidanNoktalariAl()
        {
            List<Nokta> noktalar = new List<Nokta>();

            Console.WriteLine("Veri noktalarını girin (x,y). Çıkmak için 'q' tuşuna basın.");

            while (true)
            {
                Console.Write("Veri noktası: ");
                string input = Console.ReadLine();

                if (input.ToLower() == "q")
                    break;

                string[] koordinatlar = input.Split(',');

                if (koordinatlar.Length != 2 || !double.TryParse(koordinatlar[0], out double x) || !double.TryParse(koordinatlar[1], out double y))
                {
                    Console.WriteLine("Geçersiz giriş. Lütfen geçerli bir veri noktası girin.");
                    continue;
                }

                noktalar.Add(new Nokta(x, y));
            }

            return noktalar;
        }

        static List<Kume> AglomeratifKumelenme(List<Nokta> noktalar)
        {
            List<Kume> kumeler = BaslangicKumeleriniOlustur(noktalar);

            while (kumeler.Count > 1)
            {
                int kumeIndeks1 = 0;
                int kumeIndeks2 = 1;
                double minUzaklik = UzaklikHesapla(kumeler[kumeIndeks1].Merkez, kumeler[kumeIndeks2].Merkez);

                // En yakın iki kumenin ve aralarındaki minimum uzaklığın bulunması için döngü ve koşul ifadelerini kullandık
                for (int i = 0; i < kumeler.Count - 1; i++)
                {
                    for (int j = i + 1; j < kumeler.Count; j++)
                    {
                        double uzaklik = UzaklikHesapla(kumeler[i].Merkez, kumeler[j].Merkez);
                        if (uzaklik < minUzaklik)
                        {
                            minUzaklik = uzaklik;
                            kumeIndeks1 = i;
                            kumeIndeks2 = j;
                        }
                    }
                }

                // İki kumenin birleştirilmesini sağladık
                Kume birlesikKume = KumeleriBirlestir(kumeler[kumeIndeks1], kumeler[kumeIndeks2]);
                kumeler.RemoveAt(kumeIndeks2);
                kumeler[kumeIndeks1] = birlesikKume;
            }

            return kumeler;
        }

        static List<Kume> BaslangicKumeleriniOlustur(List<Nokta> noktalar)
        {
            List<Kume> kumeler = new List<Kume>();
            foreach (Nokta nokta in noktalar)
            {
                Kume kume = new Kume();
                kume.Noktalar.Add(nokta);
                kume.Merkez = nokta;
                kumeler.Add(kume);
            }
            return kumeler;
        }

        static double UzaklikHesapla(Nokta nokta1, Nokta nokta2)
        {
            // Öklidyen uzaklık hesaplaması
            double uzaklik = Math.Sqrt(Math.Pow((nokta2.X - nokta1.X), 2) + Math.Pow((nokta2.Y - nokta1.Y), 2));
            return uzaklik;
        }

        static Kume KumeleriBirlestir(Kume kume1, Kume kume2)
        {
            Kume birlesikKume = new Kume();
            birlesikKume.Noktalar.AddRange(kume1.Noktalar);
            birlesikKume.Noktalar.AddRange(kume2.Noktalar);
            birlesikKume.Merkez.X = (kume1.Merkez.X + kume2.Merkez.X) / 2;
            birlesikKume.Merkez.Y = (kume1.Merkez.Y + kume2.Merkez.Y) / 2;
            return birlesikKume;
        }
    }

    class Nokta
    {
        public double X { get; set; }
        public double Y { get; set; }

        public Nokta(double x, double y)
        {
            X = x;
            Y = y;
        }
    }

    class Kume
    {
        public List<Nokta> Noktalar { get; set; }
        public Nokta Merkez { get; set; }

        public Kume()
        {
            Noktalar = new List<Nokta>();
            Merkez = new Nokta(0, 0);
        }
    }
}
