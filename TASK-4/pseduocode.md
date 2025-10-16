BAŞLA

  // --- Değişken Tanımlamaları ---
  DEĞİŞKEN öğrenci_no, şifre
  DEĞİŞKEN oturum_açık = YANLIŞ
  DEĞİŞKEN öğrenci_bilgileri // (GPA, tamamlanan dersler vb. bilgileri içerir)
  DEĞİŞKEN seçilen_dersler_listesi = BOŞ LİSTE
  DEĞİŞKEN toplam_kredi = 0
  SABİT MAKSIMUM_KREDI = 35

  // --- 1. Adım: Öğrenci Girişi ---
  YAZ "Öğrenci No Girin:"
  OKU öğrenci_no
  YAZ "Şifre Girin:"
  OKU şifre

  EĞER (KullanıcıDoğrula(öğrenci_no, şifre) == DOĞRU) İSE
    oturum_açık = DOĞRU
    öğrenci_bilgileri = ÖğrenciVerisiniGetir(öğrenci_no)
    YAZ "Giriş başarılı!"
  DEĞİLSE
    YAZ "HATA: Geçersiz öğrenci no veya şifre."
    ÇIKIŞ
  SONA_ERDİR

  // --- 2. Adım: Ders Seçim Döngüsü ---
  EĞER (oturum_açık == DOĞRU) İSE
    DEĞİŞKEN kayıt_tamamlandı = YANLIŞ
    DÖNGÜ (kayıt_tamamlandı == YANLIŞ)
      YAZ "--- Ders Kayıt Ekranı ---"
      YAZ "Seçili Dersler: ", seçilen_dersler_listesi
      YAZ "Toplam Kredi: ", toplam_kredi
      YAZ "Açılabilecek tüm dersleri listele..."
      YAZ "İşlem Seçin: 1-Ders Ekle, 2-Ders Çıkar, 3-Kaydı Bitir"
      OKU kullanıcı_seçimi

      // --- Ders Ekleme Bloğu ---
      EĞER (kullanıcı_seçimi == 1) İSE
        YAZ "Eklenecek dersin kodunu girin:"
        OKU eklenecek_ders_kodu
        ders = DersVerisiniGetir(eklenecek_ders_kodu)

        // --- KONTROL MEKANİZMALARI (İÇ İÇE EĞER-İSE) ---
        EĞER (toplam_kredi + ders.kredi <= MAKSIMUM_KREDI) İSE
          EĞER (ders.kontenjan > 0) İSE
            EĞER (ÖnKoşulKontrolü(ders, öğrenci_bilgileri.tamamlanan_dersler) == DOĞRU) İSE
              EĞER (ZamanÇakışmasıKontrolü(ders, seçilen_dersler_listesi) == YANLIŞ) İSE
                // Tüm kontroller başarılı
                seçilen_dersler_listesi.Ekle(ders)
                toplam_kredi = toplam_kredi + ders.kredi
                YAZ ders.ad, " başarıyla eklendi."
              DEĞİLSE
                YAZ "HATA: Zaman çakışması!"
              SONA_ERDİR
            DEĞİLSE
              YAZ "HATA: Ön koşul dersi alınmamış!"
            SONA_ERDİR
          DEĞİLSE
            YAZ "HATA: Kontenjan dolu!"
          SONA_ERDİR
        DEĞİLSE
          YAZ "HATA: Maksimum kredi limiti aşıldı!"
        SONA_ERDİR
      SONA_ERDİR

      // --- Ders Çıkarma Bloğu ---
      DEĞİLSE EĞER (kullanıcı_seçimi == 2) İSE
        YAZ "Çıkarılacak dersin kodunu girin:"
        OKU çıkarılacak_ders_kodu
        // (Çıkarma işlemleri burada yapılır)
        // ...
      SONA_ERDİR

      // --- Kaydı Bitirme Bloğu ---
      DEĞİLSE EĞER (kullanıcı_seçimi == 3) İSE
        YAZ "--- KAYIT ÖZETİ ---"
        YAZ "Seçilen Dersler: ", seçilen_dersler_listesi
        YAZ "Toplam Kredi: ", toplam_kredi
        
        // Danışman Onayı Kontrolü
        EĞER (öğrenci_bilgileri.gpa < 2.5) İSE
          YAZ "DİKKAT: Not ortalamanız 2.5'in altında olduğu için kaydınız danışman onayına gönderilecektir."
        DEĞİLSE
          YAZ "Kaydınız alınmıştır."
        SONA_ERDİR
        
        YAZ "Onaylıyor musunuz? (E/H)"
        OKU onay
        EĞER (onay == 'E') İSE
          kayıt_tamamlandı = DOĞRU
          YAZ "Ders kaydınız tamamlandı. Sistemden çıkılıyor."
        SONA_ERDİR
      SONA_ERDİR
    SONA_ERDİR DÖNGÜ
  SONA_ERDİR

SON
