# 🛡️ Siber Güvenlik Olay Müdahalesi ve Erişim Kontrolü Analizi (DFIR)
> **Konu:** Yetkisiz Finansal Transfer ve Ayrıcalıklı Hesap İstismarı Analizi  
> **Rol:** Kıdemli Siber Güvenlik Analisti / Olay Müdahale Uzmanı (Incident Responder)

![Cyber Security](https://img.shields.io/badge/Focus-Incident%20Response%20%26%20IAM-blue?style=for-the-badge&logo=cyberdefenders)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)
![Severity](https://img.shields.io/badge/Severity-CRITICAL-red?style=for-the-badge)

---

## 📌 1. Senaryo Özeti ve Senaryo Arka Planı

Büyüyen bir işletmede göreve başlayan ilk **Siber Güvenlik Uzmanı** olarak, şirket hesaplarından bilinmeyen ve yetkisiz bir banka hesabına para transferi yapıldığı tespit edilmiştir. Finans departmanı kendi taraflarında bir işlem hatası olmadığını belirtmiş ve şans eseri ödeme son anda durdurulabilmiştir. 

Şirket yönetimi, olayın arka planının araştırılması, tehdit aktörünün tespiti, istismar edilen güvenlik zafiyetlerinin çıkarılması ve gelecekteki benzer saldırıları önleyici tedbirlerin alınması amacıyla bir adli inceleme ve erişim kontrolü analizi başlatılmasını talep etmiştir.

---

## 🔍 2. Olay Günlüğü ve Adli İnceleme Bulguları

Sistem ve erişim loglarının (Access Logs) analizi sonucunda elde edilen adli kanıtlar aşağıdaki gibidir:

| İnceleme Parametresi | Tespit Edilen Değer / Bilgi |
| :--- | :--- |
| **Olay Zamanı (Timestamp)** | `10/03/2023 - 08:29:57 AM` |
| **İlişkili Hesap (Domain\User)** | `Legal\Administrator` |
| **Ağ ve Cihaz Bilgisi** | **IP:** `152.207.255.255` \| **Hostname:** `Up2-NoGud` |
| **Kimlik / Kullanıcı Bilgisi** | **Robert Taylor Jr.** (`rt.jr@erems.net`) |
| **İstihdam / Sözleşme Süresi** | `09/04/2019 – 27/12/2019` |
| **Saldırı / Olay Tipi** | Sahte Banka Kaydı Ekleme / Yetkisiz Finansal Transfer Girişimi |

> [!WARNING]
> **Kritik Tespit:** 10/03/2023 tarihinde `Up2-NoGud` terminali üzerinden `Legal\Administrator` yetkileri kullanılarak maaş/ödeme sistemine sahte banka hesabı eklenmiştir. Hesabı kullanan şahsın, **iş akdi 2019 yılında sonlandırılmış** eski bir sözleşmeli personel olduğu anlaşılmıştır.

---

## 🚨 3. Tespit Edilen Güvenlik Zafiyetleri ve Root Cause (Kök Neden) Analizi

Olayın ana nedeni, personelin işten ayrılış tarihi üzerinden **yaklaşık 4 yıl geçmesine rağmen** domain seviyesinde kritik yetkilere sahip bir hesabın kapatılmamasıdır.

### 🔴 Sorunlar (Issues Identified)

1. **Etkisiz Kullanıcı Yaşam Döngüsü ve Atıl Hesap Yönetimi (Stale/Dormant Account Management)**
   * İşten ayrılış tarihi (2019) üzerinden yıllar geçmesine rağmen eski çalışana ait `Legal\Administrator` hesabının pasife alınmaması veya silinmemesi, Kimlik ve Erişim Yönetimi (IAM) süreçlerinde kritik bir boşluk olduğunu göstermektedir.

2. **Ayrıcalıklı Hesapların İstismarı (Privilege Abuse & Deprovisioning Yetersizliği)**
   * En üst seviye sistem yönetimi yetkilerine (`Administrator`) sahip bir hesabın kontrolsüz şekilde açık bırakılması; eski çalışanın veya hesabı ele geçiren 3. taraf tehdit aktörlerinin yetkisiz finansal işlemler gerçekleştirmesine doğrudan zemin hazırlamıştır.

3. **Periyodik Erişim Denetimi ve Gözden Geçirme Yetersizliği (Lack of Access Review)**
   * Sistemdeki aktif/pasif hesapların, yetki seviyelerinin ve atıl duruma düşmüş erişimlerin düzenli aralıklarla denetlenmediği (Access Review) tespiti yapılmıştır.

---

## 🛡️ 4. Önleyici ve İyileştirici Tavsiyeler (Recommendations)

Gelecekte benzer olayların yaşanmaması ve IAM mimarisinin olgunlaştırılması için aşağıdaki aksiyonlar alınmalıdır:

### 1. Kimlik ve İşten Ayrılış Yönetimi (IAM & Deprovisioning)
* İnsan Kaynakları (İK) ve BT/Güvenlik birimleri arasında otomatik entegrasyon sağlanmalıdır. İş akdi sonlanan veya bölüm değiştiren personelin tüm erişim yetkileri **işten ayrıldığı gün ve saat itibarıyla** otomatik olarak iptal edilmeli ve hesapları pasife alınmalıdır.
* Sistemde atıl (dormant/stale) kalan hesapların otomatik tespiti için periyodik **Erişim Gözden Geçirme (Access Review)** süreçleri işletilmelidir.

### 2. Ayrıcalıklı Hesap Yönetimi (PAM) ve En Az Ayrıcalık İlkesi (PoLP / SoD)
* **Görevler Ayrılığı (Segregation of Duties - SoD):** Sistem yönetimi yetkileri ile finansal/bankacılık işlem yetkileri kesin çizgilerle ayrılmalıdır. Bir `Administrator` hesabının doğrudan banka veya ödeme sistemlerine müdahale etmesi engellenmelidir.
* **PAM Uygulaması:** Yönetici seviyesindeki tüm hesaplar Privileged Access Management (PAM) çözümleri ile kasa (vault) altına alınmalı ve oturumlar kayıt altına alınmalıdır.

### 3. Çok Faktörlü Kimlik Doğrulama (MFA)
* Tüm ayrıcalıklı hesaplara, VPN bağlantılarına ve finansal sistemlere erişimde **MFA (Multi-Factor Authentication)** kullanımı zorunlu hale getirilmelidir.

### 4. Adli Bilişim ve Fiziksel Security Entegrasyonu (Forensics & Physical Review)
* Olayın eski çalışan tarafından uzaktan mı, kurum içerisinden mi yoksa iş birliği yapan başka bir personel aracılığıyla mı gerçekleştirildiğini netleştirmek adına; log kayıtları ile birlikte **CCTV (kamera)** ve **fiziksel kartlı geçiş sistemleri** adli bilişim incelemesine dahil edilmelidir.

---

## 📂 5. Depo Yapısı (Repository Structure)

```text
.
├── README.md                                # Proje açıklama ve analiz raporu
├── docs/
│   ├── Şirkette Finans Saldırısı Loglar.xlsx # Olay ve erişim kontrolü analiz tablosu
│   └── Şirkette Finans Saldırısı Senaryo.png # Olay senaryosu ve detay görseli
└── src/
    └── log_parser_helper.py                 # (Opsiyonel) Log ayrıştırma betikleri
