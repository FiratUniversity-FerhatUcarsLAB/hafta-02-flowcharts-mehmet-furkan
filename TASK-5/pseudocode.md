// BAŞLANGIÇ: Değişkenleri ve durumları ayarla
DEĞİŞKEN sistem_durumu = AKTİF
DEĞİŞKEN alarm_durumu = PASİF
DEĞİŞKEN tehdit_seviyesi = 0 // 0: Tehdit yok, 1: Düşük, 2: Orta, 3: Yüksek

// Ana Döngü: Sistem 7/24 sürekli çalışır
DÖNGÜ SÜRECE (DOĞRU):

    // Sadece sistem aktifken kontrolleri yap
    EĞER (sistem_durumu == AKTİF):

        // 1. SENSÖR OKUMA SÜRECİ
        // Her döngüde tüm sensörler kontrol edilir
        hareket_algilandi = OkuHareketSensörü()
        kapi_pencere_acik = OkuKapiPencereSensörü()
        ev_sahibi_evde = KontrolEtEvSahibiKonumu() // Yanlış alarm kontrolü için

        // 2. TEHDİT ALGILAMA VE SEVİYE BELİRLEME SÜRECİ
        tehdit_seviyesi = 0 // Her döngü başında tehdidi sıfırla

        // Eğer ev sahibi evde değilse ve bir sensör tetiklenmişse tehdit vardır
        EĞER (ev_sahibi_evde == HAYIR) VE (hareket_algilandi == EVET VEYA kapi_pencere_acik == EVET):
            
            // Tehdit seviyesini belirle
            EĞER (hareket_algilandi == EVET) VE (kapi_pencere_acik == EVET):
                tehdit_seviyesi = 3 // Yüksek Tehdit
            DEĞİLSE EĞER (hareket_algilandi == EVET):
                tehdit_seviyesi = 2 // Orta Tehdit
            DEĞİLSE: // Sadece kapı/pencere açıksa
                tehdit_seviyesi = 1 // Düşük Tehdit
            SON EĞER

        SON EĞER

        // 3. ALARM VERME VE BİLDİRİM GÖNDERME SÜRECİ
        // Algılanan tehdit seviyesine göre aksiyon al
        EĞER (tehdit_seviyesi > 0):
            
            // Tehdit algılandığı anda alarm durumunu aktif et
            alarm_durumu = AKTİF

            // Seviyeye göre farklı aksiyonlar
            İŞLEM (tehdit_seviyesi):
                DURUM 1 (Düşük):
                    // Sadece bildirim gönder
                    BildirimGonder("Düşük seviye tehdit: Kapı/pencere sensörü tetiklendi.", App)
                    break

                DURUM 2 (Orta):
                    KamerayiAktifEt()
                    AlarmCal(Düşük_Ses_Seviyesi)
                    BildirimGonder("Orta seviye tehdit: Hareket algılandı.", App, SMS)
                    break
                
                DURUM 3 (Yüksek):
                    KamerayiAktifEt()
                    AlarmCal(Yüksek_Ses_Seviyesi)
                    BildirimGonder("YÜKSEK TEHDİT: Ev ihlali algılandı!", App, SMS, Email)
                    break
            SON İŞLEM

            // ALARM SIFIRLAMA KONTROLÜ
            // Alarm, sıfırlama komutu gelene kadar devam eder
            DÖNGÜ SÜRECE (alarm_durumu == AKTİF):
                alarm_sifirlama_komutu = OkuAlarmSifirlamaKomutu()
                EĞER (alarm_sifirlama_komutu == GELDİ):
                    AlarmiSustur()
                    KamerayiKapat()
                    alarm_durumu = PASİF
                    // Bu iç döngüden çık
                SON EĞER
                // Sistemi yormamak için kısa bir bekleme
                Bekle(1 saniye) 
            SON DÖNGÜ

        SON EĞER
        
    SON EĞER

    // Ana döngünün her adımı arasında kısa bir bekleme ekleyerek işlemciyi verimli kullan
    Bekle(2 saniye)

SON DÖNGÜ
// SON
