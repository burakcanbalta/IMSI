![facebas](https://github.com/user-attachments/assets/8dc667f3-982c-4392-9d5a-7a9a49a0137f)
# IMSI Catcher’lar ve Türkiye’de RF Tabanlı Güvenlik Açıkları: MIT Çetesinin Çöküşü ve Operasyonel Detaylar

## Özet / Abstract

Bu makale, IMSI Catcher saldırılarının teknik detaylarını, Türkiye’deki MIT çetesinin operasyonlarını ve KEC cihazları ile entegrasyon senaryolarını ele almaktadır. SDR tabanlı RF manipülasyonları, baseband açıkları ve 2G downgrade saldırıları üzerinden veri sızdırma mekanizmaları detaylı olarak incelenmektedir. Ayrıca, pasif ve aktif tespit yöntemleri ile hukuki ve etik çerçeveye dair bilgiler sunulmaktadır.

---

## 1. Giriş

IMSI (International Mobile Subscriber Identity) Catcher’lar, mobil ağlarda sahte baz istasyonu işlevi görerek; kullanıcıların IMSI/IMEI verilerini toplar, konum takibi yapar ve GSM/3G/4G iletişimini manipüle edebilir. Modern saldırı senaryolarında, IMSI catcher’lar yalnızca sinyal bozmakla kalmaz, aynı zamanda **baseband işlemci açıklarını**, **2G downgrade saldırılarını** ve **KEC cihazları gibi kritik altyapıları** hedef alabilir.

Türkiye’de 2022’de MIT tarafından çökertilen organize çete, yargı mensupları ve kritik kurum personeli hedef alınarak **IMSI sızıntısı ve konum takibi** yapmış, operasyonel olarak RF ve KEC cihazlarını entegre şekilde kullanmıştır.

<img width="833" height="522" alt="Ekran görüntüsü 2025-08-15 145719" src="https://github.com/user-attachments/assets/0f53b561-27d6-4bf2-b8b0-27a4e385b12a" />


---

## 2. IMSI Catcher Çalışma Prensibi ve RF Mekanizmaları

### 2.1 Sinyal Manipülasyonu

- **Sinyal bastırma (Jamming):** Kurbanın mobil cihazını gerçek baz istasyonundan kopartır.
- **Güçlü sahte baz yayını:** Cihaz sahte istasyona bağlanır.
- **Downgrade ve şifre çözme:** 2G’ye düşürülen cihazlar A5/1, A5/2 gibi zayıf algoritmalar üzerinden veri sızdırır.

### 2.2 Baseband ve Protokol Açıkları

- IMSI catcher’lar RFC 3588 (AAA) ve GSM/LTE kimlik doğrulama protokollerini manipüle eder.
- Türkiye’deki MIT çetesi, **KEC cihazları üzerinden APDU komutlarını** kullanarak IMSI bilgisini sızdırmıştır.

*Görsel önerisi:* `Baseband_APDU_Flow.png` — Baseband işlemci + APDU veri akışı şeması.

---

## 3. Türkiye’de RF Güvenlik Mitlerinin Çöküşü

### 3.1 “Şifreleme Yeterlidir” Yanılgısı

- GSM A5/1 ve A5/2 şifrelemeleri 2003’te kırılmıştır (Karsten Nohl).
- OsmocomBB ile 2G downgrade saldırıları yaygınlaşmıştır.
- Türkiye’de tespit edilen 15 farklı IMSI catcher modeli (Krackin, StingRay varyantları) ile aktif testler yapılmıştır.

### 3.2 Fiziksel Erişim Gerekir Yanılgısı

- SDR tabanlı sistemler (HackRF One, BladeRF) ile 5 km’ye kadar erişim.
- UAV üzerine monte edilmiş IMSI catcher’lar (2021 İstanbul örneği).

### 3.3 Yasal Dinleme Dışında Mümkün Değil Yanılgısı

- KEC cihazları ve NFC üzerinden IMSI sızıntısı.
- Baseband backdoor’lar (Qualcomm CVE-2018-11279) ile operatör içi veri sızması.

---

## 4. MIT Çetesi Operasyonunun Teknik Analizi

### 4.1 Saldırı Mimari Yapısı

1. **KEC cihazları:** NFC/APDU ile IMSI sızıntısı.
2. **IMSI Catcher:** Sahte baz istasyonu ile cihazları zorla bağlatma.
3. **RF Spektrum Analizi:** SDR ile gerçek zamanlı RF tarama.

*Görsel önerisi:* `MIT_Cete_Operasyon_Akış.png`

### 4.2 Kullanılan Açıklar

- TS 13583 acil durum modu kötüye kullanımı.
- Baseband backdoor’lar (Qualcomm, MediaTek).
- KEC cihazı sürücü zafiyetleri (PC/SC imzasız komut işleme).

### 4.3 Operasyon Adımları

1. Hedef belirleme (yargı mensupları, kritik personel)
2. RF ortam keşfi ve sinyal bastırma
3. Sahte baz istasyonu ile downgrade + IMSI toplama
4. KEC cihazlarından APDU komutları ile veri sızıntısı
5. Elde edilen verilerin merkezi sunucuda toplanması

---

## 5. Yapısal ve Görsel Geliştirmeler

### 5.1 IMSI Catcher Sinyal Manipülasyonu ve KEC Veri Akışı

```
Gerçek Baz İstasyonu  --->  RF Sinyal Bastırma
                           |
                           v
                     Sahte Baz İstasyonu
                           |
                           v
                   IMSI ve Konum Toplama
                           |
                           v
                        KEC Cihazı
                           |
                           v
                    Merkezi Veri Sunucusu
```

*Görsel Yer Tutucu:* `IMSI_Catcher_KEC_Akış.png`

### 5.2 SDR Laboratuvar Simülasyonu

- **Donanım:** HackRF One + LNA + Bandpass filtre (880-915 MHz)
- **Sinyal Yakalama Komutları:**

```bash
grsgem_scanner -b GSM900 -v
grsm_livemon -f 942.6M -g 40 -v
```

- **Dekodlama ve Analiz:** Airprobe ile FCCH, SCH, BCCH analizi; IMSI-Catcher Detector ile sahte MCC/MNC tespiti.

*Görsel Yer Tutucu:* `SDR_Lab_Simülasyonu.png`

---

## 6. Teknik Detayları Derinleştirme

### 6.1 Baseband İşlemci Açıkları ve APDU Exploit Mantığı

- Qualcomm & MediaTek CVE Detayları: CVE-2018-11279 ve benzer backdoor’lar
- APDU Komut Setleri: KEC cihazları ile IMSI sızdırma adımları
- Exploit Zinciri: SDR sinyal bastırma → downgrade → IMSI sızdırma → KEC cihazı → merkezi sunucu

*Görsel Yer Tutucu:* `Baseband_APDU_Flow.png`

### 6.2 Downgrade ve Ciphering Manipülasyonu

- 2G/3G downgrade senaryoları ile A5/1 ve A5/2 şifreleme bypass adımları
- Gerçek RF parametreleri ile örnek sinyal analizleri

*Görsel Yer Tutucu:* `Downgrade_Scenario.png`

### 6.3 KEC + IMSI Catcher Entegrasyonu

- KEC cihazı APDU komutları ile IMSI toplar ve sahte baz ile konum takibi yapılır
- Zafiyet zinciri görselleştirilmiş şekilde sunulmalı

*Görsel Yer Tutucu:* `KEC_IMSI_Zinciri.png`

---

## 7. Tespit ve Savunma Mekanizmaları

### 7.1 Pasif Tespit

- Timing Advance analizi
- MCC/MNC çakışması kontrolü
- Ciphering indicator analizi (Android: ##4636## → "Ciphering: Off")

### 7.2 Aktif Savunma

- SDR tabanlı spektrum taraması (GQRX + GNU Radio)
- Secure Element sertifika zorunluluğu (TS 13584)
- AIMSICD ve benzeri açık kaynaklı IMSI catcher detektörleri

### 7.3 Politik Önlemler

- 2G şebekelerin kapatılması
- Lawful Interception şifreleme standartlarının güncellenmesi (TS 101 671)

*Görsel Yer Tutucu:* `Tespit_Savunma.png`

---

## 8. Operasyonel Senaryolar ve MIT Çetesi Örneği

- Hedef belirleme, RF keşfi, sahte baz kurulumu, IMSI toplama ve KEC ile veri sızıntısı
- Her adımın detaylı akış diyagramları ile gösterimi

*Görsel Yer Tutucu:* `MIT_Cete_Senaryo.png`

---

## 9. Hukuki ve Etik Boyut

- **TCK 245:** Yetkisiz elektronik iletişim dinlemesi (3-7 yıl)
- **BTK 2013/5:** RF spektrumunun izinsiz kullanımı (500.000 TL’ye kadar ceza)
- **KVKK 12:** Kişisel verilerin güvenliğinin ihlali

---

## 10. Sonuç ve Öneriler

- Kritik altyapılarda sürekli RF izleme
- TS 13585 standardına "RF hardening" maddeleri eklenmeli
- Açık kaynaklı SDR eğitimleri yaygınlaştırılmalı
- 5G SA mimarisine geçiş ile 2G/3G’den kopuş sağlanmalı

*Görsel Yer Tutucu:* `RF_Hardening_5G_Roadmap.png`

---

## 11. Referanslar

1. Karsten Nohl, GSM Security Research (2003-2022)
2. TS 13583 / TS 13584 / TS 13585
3. Qualcomm CVE-2018-11279
4. Türkiye MIT operasyon raporları (kamuya açık özetler)
5. AIMSICD ve GR-GSM dokümantasyonu

---

