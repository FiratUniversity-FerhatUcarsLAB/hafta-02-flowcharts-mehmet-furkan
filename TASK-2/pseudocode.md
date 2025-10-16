BAŞLA: SİSTEM BAŞLATMA

// Adım 1: Kullanıcı Giriş Kontrolü
BAŞLA: KULLANICI GİRİŞ KONTROLÜ
  EGER (kullanıcı oturum açmamış) ISE
    Kullanıcıyı giriş sayfasına yönlendir.
    Süreci durdur.
  BİTİR KONTROL
BİTİR: KULLANICI GİRİŞ KONTROLÜ

// Adım 2: Ürün Kategorilerinde Gezinme ve Seçim
BAŞLA: ÜRÜN İŞLEMLERİ
  DÖNGÜ (kullanıcı alışverişe devam etmek istediği sürece)
    Ürün kategorilerini listele.
    Kullanıcıdan kategori veya ürün seçmesini iste.
    
    // Adım 3: Ürün Sepete Ekleme ve Stok Kontrolü
    BAŞLA: ÜRÜN SEPETE EKLEME
      Kullanıcı bir ürün seçtiğinde:
      BAŞLA: STOK KONTROLÜ
        EGER (seçilen ürünün stoku > 0) ISE
          Ürünü sepete ekle.
          Kullanıcıya "Ürün sepete eklendi" mesajı göster.
        DEGILSE
          Kullanıcıya "Ürün stokta yok" uyarısı ver.
        BİTİR KONTROL
      BİTİR: STOK KONTROLÜ
    BİTİR: ÜRÜN SEPETE EKLEME
    
    Kullanıcıya sor: "Alışverişe devam mı, sepete mi gitmek istersin?"
  BİTİR DÖNGÜ
BİTİR: ÜRÜN İŞLEMLERİ

// Adım 4: Sepeti Görüntüleme ve Düzenleme
BAŞLA: SEPET YÖNETİMİ
  DÖNGÜ (kullanıcı sepet sayfasında olduğu sürece)
    Sepetteki ürünleri, adetleri ve toplam tutarı göster.
    Kullanıcıya seçenek sun: "Adet Güncelle", "Ürün Sil", "Siparişi Tamamla".
    Kullanıcının seçimine göre sepeti güncelle.
    EGER (kullanıcı "Siparişi Tamamla" seçerse) ISE
      Döngüden çık.
    BİTİR KONTROL
  BİTİR DÖNGÜ
BİTİR: SEPET YÖNETİMİ

// Adım 5: Siparişi Tamamlama (Checkout) Süreci
BAŞLA: SİPARİŞİ TAMAMLAMA
  Sepet ara toplamını hesapla.
  
  // Adım 6: İndirim Kodu Uygulama
  BAŞLA: İNDİRİM KODU KONTROLÜ
    Kullanıcıya indirim kodu sor.
    EGER (kullanıcı kod girdiyse VE kod geçerliyse) ISE
      İndirimi uygula.
      Sepet toplamını yeniden hesapla.
    DEGILSE ISE (kullanıcı kod girdiyse VE kod geçersizse)
      Kullanıcıya "Geçersiz indirim kodu" uyarısı ver.
    BİTİR KONTROL
  BİTİR: İNDİRİM KODU KONTROLÜ
  
  // Adım 7: Minimum Sepet Tutarı Kontrolü
  BAŞLA: MİNİMUM TUTAR KONTROLÜ
    EGER (sepet toplamı < 50 TL) ISE
      Kullanıcıya "Sipariş verebilmek için sepet tutarı en az 50 TL olmalıdır" uyarısı ver.
      BİTİR: SİPARİŞİ TAMAMLAMA // Süreci sonlandır.
    BİTİR KONTROL
  BİTİR: MİNİMUM TUTAR KONTROLÜ
  
  // Adım 8: Kargo Ücreti Hesaplama
  BAŞLA: KARGO HESAPLAMA KONTROLÜ
    EGER (sepet toplamı >= 200 TL) ISE
      kargo_ucreti = 0
    DEGILSE
      kargo_ucreti = standart_kargo_ucreti
    BİTİR KONTROL
    genel_toplam = sepet toplamı + kargo_ucreti
  BİTİR: KARGO HESAPLAMA KONTROLÜ
  
  // Adım 9: Ödeme Yöntemi Seçimi
  BAŞLA: ÖDEME YÖNTEMİ KONTROLÜ
    Kullanıcıya ödeme yöntemlerini (Kredi Kartı, Havale vb.) listele.
    Kullanıcıdan bir yöntem seçmesini iste.
    EGER (geçerli bir yöntem seçildi) ISE
      Gerekli ödeme bilgilerini al (kart numarası vb.).
    DEGILSE
      Kullanıcıya "Lütfen geçerli bir ödeme yöntemi seçin" uyarısı ver.
      Süreci bu adıma geri döndür.
    BİTİR KONTROL
  BİTİR: ÖDEME YÖNTEMİ KONTROLÜ
  
  // Adım 10: Sipariş Onayı
  BAŞLA: SİPARİŞ ONAYI
    Kullanıcıya sipariş özetini (ürünler, toplam tutar, adres, ödeme bilgisi) göster.
    Kullanıcıdan onayı iste.
    EGER (kullanıcı siparişi onaylarsa) ISE
      Ödeme işlemini gerçekleştir.
      Siparişi veritabanına kaydet.
      Stok adedini güncelle.
      Kullanıcıya "Siparişiniz başarıyla alınmıştır" mesajı göster.
    DEGILSE
      Siparişi iptal et.
      Kullanıcıyı sepet sayfasına geri yönlendir.
    BİTİR KONTROL
  BİTİR: SİPARİŞ ONAYI
BİTİR: SİPARİŞİ TAMAMLAMA

BİTİR: SİSTEM SONU
