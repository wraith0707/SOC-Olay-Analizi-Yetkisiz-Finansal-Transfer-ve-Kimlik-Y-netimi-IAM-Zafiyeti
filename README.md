# 🛡️ SOC Olay Analizi: Yetkisiz Finansal Transfer ve IAM Zafiyeti

> ⚠️ **UYARI / DISCLAIMER:** Bu çalışma, siber güvenlik eğitim ve uygulama senaryoları kapsamında hazırlanmış **örnek bir Blue Team lab analizi/raporudur**. Gerçek kişi, kurum veya canlı sistem verisi içermez.

---

## 📌 Proje Özeti (Executive Summary)

Bu çalışmada, büyümekte olan bir şirketin finans/maaş sistemine yetkisiz erişim sağlanarak sahte banka hesabı üzerinden finansal transfer yapılmaya çalışılması olayı (Incident) bir SOC Analisti gözüyle incelenmiştir.

Olayın kök nedeni; şirketten yıllar önce ayrılmış bir çalışana ait **Domain Admin / Privilege** yetkili hesabın pasife alınmaması (**Stale Account / Deprovisioning Failure**) ve Kimlik Yönetimi (IAM) süreçlerindeki eksikliklerdir.

---

## 🎯 Senaryo ve Problem Tanımı

Şirket tarafından işe alınan ilk Siber Güvenlik Uzmanı olarak, Finans Müdürlüğü'nden gelen bir ihbar üzerine inceleme başlatılmıştır. 

* **Olay:** İşletmeden bilinmeyen bir banka hesabına yetkisiz para transferi teşebbüsü gerçekleşmiş, finans ekipleri ödemeyi son anda durdurmuştur.
* **Amaç:** Erişim günlüklerinin (Access Logs) adli analizi, tehdit aktörünün tespiti, istismar edilen erişim kontrollerinin belirlenmesi ve tekrarlanmaması için IAM/PAM güvenlik tavsiyelerinin sunulması.

---

## 🔍 Olay & Günlük (Log) Bilgileri

| Parametre | Detay |
| :--- | :--- |
| **Olay Zamanı (Timestamp)** | `10/03/2023 - 08:29:57 AM` |
| **İlişkili Hesap (Domain\User)** | `Legal\Administrator` |
| **Kaynak IP Adresi** | `152.207.255.255` |
| **Terminal Hostname** | `Up2-NoGud` |
| **Tespit Edilen Şüpheli Kişi** | Robert Taylor Jr. (`rt.jr@erems.net`) |
| **Şahsın Şirketteki Sözleşme Süresi** | `09/04/2019 – 27/12/2019` |
| **Zafiyet Türü** | IAM - Deprovisioning Zafiyeti / Un-deprovisioned Privilege Account |

---

## 🕵️‍♂️ Teknik İnceleme ve Adli İnceleme Adımları

1. **Log Analizi:** `10/03/2023 08:29:57` zaman damgalı erişim logları incelendiğinde, `152.207.255.255` IP adresli `Up2-NoGud` hostname'li terminal üzerinden `Legal\Administrator` yetkileri kullanılarak sisteme giriş yapıldığı tespit edilmiştir.
2. **Eylemin Tespiti:** Hesabın yetkileri istismar edilerek finans/maaş sistemine sahte bir banka kaydı (ödeme hesabı) eklendiği saptanmıştır.
3. **Kullanıcı Hesabı Çapraz Sorgusu (HR Data Cross-Check):** İnsan Kaynakları (İK) kayıtları ile yapılan çapraz kontrolde, `Legal\Administrator` hesabıyla ilişkili e-posta adresinin (`rt.jr@erems.net`) 2019 yılında işten ayrılmış olan eski sözleşmeli personel **Robert Taylor Jr.**'a ait olduğu görülmüştür.

---

## ⚠️ Tespit Edilen Zafiyetler ve Güvenlik Açıkları

### 1. Etkisiz Kullanıcı Yaşam Döngüsü ve Yetkisiz Erişim (Stale / Dormant Account Management)
İşten ayrılış tarihi (2019) üzerinden **yaklaşık 4 yıl geçmesine rağmen** eski çalışana ait `Legal\Administrator` hesabının pasife alınmaması veya silinmemesi, kurum genelindeki kimlik ve erişim yönetimi (IAM) süreçlerinde kritik bir zafiyet olduğunu göstermektedir.

### 2. Ayrıcalıklı Hesapların Yetkisiz Kullanımı (Privilege Abuse & Deprovisioning Defect)
En üst seviye sistem yetkilerine (`Administrator`) sahip bir hesabın kontrolsüz şekilde aktif bırakılması; eski personelin veya hesabı ele geçiren 3. tarafların yetkisiz finansal işlemler gerçekleştirmesine doğrudan zemin hazırlamıştır.

### 3. Erişim Denetimi ve Periyodik Yetki Gözden Geçirme Yetersizliği
Sistemde aktif bulunan ayrıcalıklı hesapların düzenli aralıklarla denetlenmediği (**Access Review**) ve pasif hesap tespit mekanizmalarının çalışmadığı anlaşılmıştır.

---

## ⚙️ MITRE ATT&CK Eşleşmesi

* **Tactics:** Initial Access, Persistence, Privilege Escalation
* **Techniques:**
  * `T1078.002` - Valid Accounts: Domain Accounts
  * `T1078.003` - Valid Accounts: Local Accounts
  * `T1098` - Account Manipulation

---

## 🛠️ İyileştirme Tavsiyeleri ve Önleyici Aksiyonlar (Remediation Plan)

### 🚨 Kısa Vadeli (Acil) Aksiyonlar
* **Hesabın Kapatılması:** `Legal\Administrator` hesabı ve ilişkili tüm oturumlar derhal sonlandırılmalı, hesap pasife alınmalıdır.
* **Adli Bilişim & CCTV İncelemesi:** Olayın harici bir saldırgan mı yoksa içeriden iş birliği yapan başka bir çalışan tarafından mı gerçekleştirildiğini doğrulamak adına; log kayıtları ile birlikte kritik çalışma alanlarındaki kamera (CCTV) ve fiziksel giriş kayıtları adli bilişim incelemesine alınmalıdır.

### 🛡️ Orta ve Uzun Vadeli Aksiyonlar
* **Erişim ve Hesap Yaşam Döngüsü Yönetimi (IAM & Deprovisioning):** İK (Human Resources) ve IT sistemleri entegre edilmeli; iş akdi feshedilen personelin tüm erişim yetkileri **işten ayrıldığı gün Otomatik (Automated Deprovisioning)** olarak iptal edilmelidir.
* **Ayrıcalıklı Hesap Yönetimi (PAM):** `Administrator` seviyesindeki tüm kritik hesaplar PAM çözümleri altında toplanmalı, şifre kasaları (Vault) kullanılmalı ve periyodik erişim gözden geçirme (**Access Review**) süreçleri yürütülmelidir.
* **En Az Ayrıcalık İlkesi (PoLP) ve Görevler Ayrılığı (SoD):** Sistem yönetimi yetkileri ile finansal/bankacılık işlem yetkileri birbirinden kesin çizgilerle ayrılmalıdır. Bir sistem yöneticisinin doğrudan banka/finans işlemleri gerçekleştirmesi engellenmelidir.
* **Çok Faktörlü Kimlik Doğrulama (MFA):** Tüm ayrıcalıklı ve uzaktan erişim sağlayan hesaplarda MFA zorunlu hale getirilmelidir.

---

## 📁 Proje İçeriği ve Dosyalar

* `Erişim Kontrolleri Çalışma Sayfası.xlsx` - Olay ve erişim kontrolü analiz çalışma dosyası.
* `README.md` - Olay Analizi Detay Raporu.

---
*Bu rapor bir Siber Güvenlik Analisti Portföy Çalışmasıdır.*
