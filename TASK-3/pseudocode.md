FONKSİYON RandevuAl
  YAZ "Lütfen TC Kimlik Numaranızı giriniz:"
  OKU tckn

  EĞER KimlikDoğrula(tckn) == BAŞARILI İSE
    YAZ "Mevcut Poliklinikler:"
    GÖSTER PoliklinikListesi()
    OKU secilenPoliklinik

    YAZ "Seçilen poliklinikteki doktorlar:"
    GÖSTER DoktorListesi(secilenPoliklinik)
    OKU secilenDoktor

    // Uygun saat seçimi için döngü
    DEĞİŞKEN randevuOnaylandi = YANLIŞ
    SÜRECE randevuOnaylandi == YANLIŞ
      YAZ "Doktor için uygun saatler:"
      GÖSTER UygunSaatler(secilenDoktor)
      OKU secilenSaat

      YAZ secilenPoliklinik, secilenDoktor, secilenSaat " için randevuyu onaylıyor musunuz? (E/H)"
      OKU onay

      EĞER onay == 'E' İSE
        RandevuyuKaydet(tckn, secilenDoktor, secilenSaat)
        SMS_Gonder(tckn, "Randevunuz başarıyla oluşturulmuştur.")
        YAZ "Randevunuz başarıyla oluşturulmuştur. Bilgilendirme SMS'i gönderilmiştir."
        randevuOnaylandi = DOĞRU // Döngüden çıkmak için
      DEĞİLSE
        YAZ "Randevu işlemi iptal edildi. Başka bir saat seçmek ister misiniz? (E/H)"
        OKU tekrarDene
        EĞER tekrarDene == 'H' İSE
          KIR // Döngüyü sonlandır
        SON EĞER
      SON EĞER
    DÖNGÜ SONU

  DEĞİLSE
    YAZ "Kimlik doğrulama başarısız. Lütfen bilgilerinizi kontrol ediniz."
  SON EĞER
SON FONKSİYON





FONKSİYON TahlilSonucuGor
  YAZ "Lütfen TC Kimlik Numaranızı giriniz:"
  OKU tckn

  EĞER KimlikDoğrula(tckn) == BAŞARILI İSE
    // Tahlil varlığı koşulu
    EĞER TahlilVarMi(tckn) == DOĞRU İSE
      // Sonuç hazır mı koşulu
      EĞER SonucHazirMi(tckn) == DOĞRU İSE
        YAZ "Tahlil Sonuçlarınız:"
        GÖSTER TahlilSonuclari(tckn)
        
        YAZ "Sonuçları PDF olarak indirmek ister misiniz? (E/H)"
        OKU pdfIstek
        EĞER pdfIstek == 'E' İSE
          PDF_Indir(TahlilSonuclari(tckn))
          YAZ "Sonuçlar başarıyla indirildi."
        SON EĞER
      DEĞİLSE
        YAZ "Tahlil sonuçlarınız henüz hazır değil. Lütfen daha sonra tekrar deneyiniz."
      SON EĞER
    DEĞİLSE
      YAZ "Sistemde adınıza kayıtlı bir tahlil bulunmamaktadır."
    SON EĞER
  DEĞİLSE
    YAZ "Kimlik doğrulama başarısız. Lütfen bilgilerinizi kontrol ediniz."
  SON EĞER
SON FONKSİYON





BAŞLA
  DEĞİŞKEN devamEt = 'E'

  // Ana işlem döngüsü
  SÜRECE devamEt == 'E'
    YAZ "Hastane Randevu ve Tahlil Sistemine Hoş Geldiniz!"
    YAZ "1- Randevu Al"
    YAZ "2- Tahlil Sonucu Görüntüle"
    YAZ "Lütfen yapmak istediğiniz işlemi seçiniz:"
    OKU secim

    SEÇİM secim DURUMUNA GÖRE
      DURUM 1:
        ÇAĞIR RandevuAl()
        KIR
      DURUM 2:
        ÇAĞIR TahlilSonucuGor()
        KIR
      VARSAYILAN:
        YAZ "Geçersiz bir seçim yaptınız."
        KIR
    SON SEÇİM

    YAZ "Başka bir işlem yapmak ister misiniz? (E/H)"
    OKU devamEt
  DÖNGÜ SONU

  YAZ "Sistemden çıkış yapıldı. İyi günler dileriz."
SON
