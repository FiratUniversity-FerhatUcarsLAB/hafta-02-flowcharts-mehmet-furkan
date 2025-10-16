ATM Para Çekme Sistemi - Pseudocode
BAŞLA

TANIMLA dogruPIN = "1234" // Banka sisteminden gelen doğru PIN TANIMLA bakiye = 5000 // Banka sisteminden gelen bakiye (Örnek) TANIMLA gunlukLimit = 2500 // Kullanıcının kalan günlük limiti (Örnek) TANIMLA pinHakki = 3 TANIMLA kartBloke = YANLIS TANIMLA pinDogrulandi = YANLIS

YAZ "Hoş geldiniz! Lütfen PIN kodunuzu giriniz."

// --- 1. Adım: PIN Doğrulama Döngüsü --- DÖNGÜ (pinHakki > 0) YAZ "PIN:" OKU girilenPIN

`EGER` girilenPIN == dogruPIN `İSE`
  `pinDogrulandi = DOGRU`
  `DÖNGÜDEN ÇIK` // PIN doğru, döngü sonlanır.
`DEGILSE`
  `pinHakki = pinHakki - 1`
  `EGER` pinHakki > 0 `İSE`
    `YAZ` "Hatalı PIN. Kalan deneme hakkınız: " + pinHakki
  `DEGILSE`
    `kartBloke = DOGRU`
    `YAZ` "3 kez hatalı giriş yaptınız. Kartınız güvenlik amacıyla bloke edilmiştir."
  `SON-EGER`
`SON-EGER`
DÖNGÜ-SONU

// --- 2. Adım: Ana İşlem Döngüsü --- EGER pinDogrulandi == DOGRU İSE TANIMLA devamEt = 'E' DÖNGÜ (devamEt == 'E' veya devamEt == 'e') YAZ "---------------------------------" YAZ "Mevcut Bakiyeniz: " + bakiye + " TL" YAZ "Kalan Günlük Limitiniz: " + gunlukLimit + " TL" YAZ "Lütfen çekmek istediğiniz tutarı giriniz:" OKU cekilecekTutar

  `// --- 3. Adım: Koşul Kontrolleri ---`
  `EGER` (cekilecekTutar % 20) != 0 `İSE`
    `YAZ` "HATA: Girilen tutar 20 TL ve katları olmalıdır."
  `DEGILSE-EGER` cekilecekTutar > bakiye `İSE`
    `YAZ` "HATA: Yetersiz bakiye."
  `DEGILSE-EGER` cekilecekTutar > gunlukLimit `İSE`
    `YAZ` "HATA: Günlük para çekme limitinizi aşıyorsunuz."
  `DEGILSE`
    `// --- 4. Adım: Para Çekme İşlemi ---`
    `bakiye = bakiye - cekilecekTutar`
    `gunlukLimit = gunlukLimit - cekilecekTutar`
    `YAZ` "İşlem başarılı. Lütfen paranızı alınız."
    `YAZ` "Yeni Bakiyeniz: " + bakiye + " TL"
    `YAZ` "Fiş almak ister misiniz? (E/H)"
    `OKU` fisCevap
    `EGER` fisCevap == 'E' veya fisCevap == 'e' `İSE`
      `YAZ` "Fişiniz yazdırılıyor."
    `SON-EGER`
  `SON-EGER`

  `// --- 5. Adım: Başka İşlem Sorgulama ---`
  `YAZ` "---------------------------------"
  `YAZ` "Başka bir işlem yapmak ister misiniz? (E/H)"
  `OKU` devamEt
`DÖNGÜ-SONU`
SON-EGER

YAZ "İyi günler dileriz. Kartınızı almayı unutmayınız."

BİTİR
