<div align="center">

![ExWDIT – PE analizi ve dijital adli bilişim için çalıştırılabilir dosya inceleme araç seti](assets/ExWDIT_Banner.svg)

# ExWDIT

**Çalıştırılabilir Dosya / Dijital İnceleme ve Davranış Analizi Araç Seti**

*Statik PE analizi, çalışma zamanı davranış izleme, delile dayalı risk skoru ve soruşturma raporlaması için C++ masaüstü adli analiz paketi.*

[English](README.md) · [Özellikler](#özellikler) · [Yol Haritası](#yol-haritası) · [Güvenlik](SECURITY.md)

![Durum](https://img.shields.io/badge/durum-erken%20geli%C5%9Ftirme-orange)
![Dil](https://img.shields.io/badge/C%2B%2B-20-blue)
![Arayüz](https://img.shields.io/badge/aray%C3%BCz-Dear%20ImGui-informational)
![Platform](https://img.shields.io/badge/platform-Windows%20x64-lightgrey)
![Lisans](https://img.shields.io/badge/lisans-MIT-green)

</div>

---

## ExWDIT nedir?

**ExWDIT**, **C++20** ve **Dear ImGui** ile geliştirilen, açık kaynaklı bir **dijital adli bilişim ve çalıştırılabilir dosya davranış analizi araç setidir.** Şüpheli bir Windows çalıştırılabilir dosyasını (PE) anlamak isteyen **siber güvenlik araştırmacıları, zararlı yazılım analistleri, tersine mühendisler, anti-cheat ekipleri ve adli bilişim uzmanları** için tasarlanmıştır.

ExWDIT tek bir soruya odaklanır:

> *Bu dosya ne, içinde ne var, neyi yüklüyor, nelere erişiyor, kiminle iletişim kuruyor ve çalışırken ne yapıyor?*

**Statik PE analizini** (dosyayı çalıştırmadan inceleme) ve **dinamik davranış analizini** (çalışırken gözlem) tek bir **delil → bulgu → risk skoru → rapor** akışında birleştirir.

> **Proje durumu:** ExWDIT **erken geliştirme** aşamasındadır. Mimari ve özellik seti ayrıntılı olarak tanımlanmıştır, uygulama fazlar hâlinde ilerler ([Yol Haritası](#yol-haritası)). Aşağıdaki özellikler, yol haritasında aksi belirtilmedikçe tasarım hedefleridir.

## Neden ExWDIT?

Çoğu analiz aracı birbirinden bağımsızdır: bir yerde PE görüntüleyici, başka yerde süreç izleyici, başka yerde string çıkarıcı. ExWDIT, her özelliğin aynı delil/olay/bulgu modelini beslediği **tek bir soruşturma platformu** olarak tasarlanır. Analist bir bulgudan kanıtına, çalışma zamanı olayına, sürece ve etkilenen modül, dosya, registry anahtarı veya ağ uç noktasına tıklayarak ulaşır.

**Temel ilkeler**

- **Hüküm değil, delil.** ExWDIT `Indicator`, `Suspicious`, `Heuristic` ve `Requires Investigation` gibi ifadeler kullanır. "Kesinlikle zararlı" demez.
- **Şeffaf risk skoru.** Ağırlıklar ayarlanabilir ve skordaki her değişiklik açıklanır.
- **Delil bütünlüğü.** Orijinal örnek salt okunur kabul edilir ve analizden önce hash'lenir.
- **Statik ve dinamik analiz ayrıdır.** Dosyayı açmak onu çalıştırmaz. Çalıştırma, analistin bilinçli eylemidir.
- **Yalnızca savunma amaçlı.** Anti-cheat veya güvenlik ürünlerini atlatmaya, devre dışı bırakmaya ya da kandırmaya yönelik özellik içermez.

## Özellikler

### Statik analiz (çalıştırmadan)

- PE32 / PE32+ ayrıştırma (x86, x64, mümkün olduğunda ARM/ARM64)
- DOS başlığı, NT başlıkları, Optional Header ve tüm Data Directory'ler
- RVA ↔ dosya ofseti ↔ sanal adres dönüşümü
- Entropi görselleştirmeli bölüm analizi, RWX / W+X / yüksek entropi sezgileri
- API kategorizasyonlu import ve export analizi (süreç, bellek, dosya, registry, ağ, enjeksiyon ilişkili, kriptografi vb.)
- ASCII, UTF-8 ve UTF-16LE string çıkarımı ve kategoriler (URL, domain, IP, dosya yolu, registry yolu, komut, PowerShell izleri)
- Resource, TLS callback, relocation, debug, load config ve overlay analizi
- Arka plan iş parçacıklarında MD5, SHA-1, SHA-256 hesaplama
- Authenticode imza incelemesi (*imza var* ile *imza güvenilir* ayrımı)
- Packer, derleyici ve linker göstergeleri
- İsteğe bağlı **YARA** kural taraması

### Dinamik analiz (açık ve kontrollü çalıştırma)

- Süreç oluşturma, sonlandırma ve tıklanabilir **süreç ağacı**
- İmza, hash, yol ve mimari kontrollü **modül / DLL** yükleme izleme
- **Dosya sistemi** izleme (oluşturma, açma, okuma, yazma, yeniden adlandırma, silme)
- Kalıcılık konumu sezgileriyle **registry** izleme
- **Ağ** izleme (TCP, UDP, DNS, süreç-IP/domain eşleşmesi, bağlantı zaman çizelgesi)
- **Bellek haritası** incelemesi (RWX, yürütülebilir özel bellek, dosyaya bağlı olmayan yürütülebilir bölgeler)
- Thread ve handle etkinliği

### Soruşturma katmanı

- Birleşik, aranabilir, filtrelenebilir **davranış zaman çizelgesi**
- Yalnızca gözlenen delilden üretilen **davranış grafiği**
- Önem, kategori, açıklama, kanıt, güven seviyesi, kaynak ve zaman damgası taşıyan **bulgu motoru**
- Ayarlanabilir ağırlıklı, **açıklanabilir risk motoru**
- Süreç, modül, dosya, registry, ağ, string, import/export, olay ve bulgular genelinde **global arama**
- Analist notları ve etiketleriyle **vaka yönetimi**
- **JSON ve HTML raporları**

### Örnek bulgu

```text
⚠ Suspicious Executable Memory

Özel bir bellek bölgesi hem yürütülebilir hem yazılabilir.

Kanıt:
  Protection: RWX
  Backing:    Private
  Size:       64 KB

Güven: Orta
```

## Mimari

ExWDIT, arayüzü yeniden yazmadan yeni analiz motorları eklenebilmesi için katmanlı ve modüler bir mimari kullanır.

```text
Delil
   ├── Statik Analiz ──┐
   └── Dinamik Analiz ─┴─► Delil Modeli ─► Bulgu Motoru
                                              ├─► Risk Motoru ─┐
                                              └─► Zaman Çizelgesi ┴─► Soruşturma Arayüzü ─► Raporlar
```

**Teknoloji:** C++20 · Dear ImGui (docking) · CMake · vcpkg · fmt · spdlog · nlohmann/json · Windows kriptografi API'leri veya OpenSSL · isteğe bağlı YARA.

## Yol Haritası

| Faz | Kapsam | Durum |
|----:|--------|-------|
| 1 | Temel: CMake, C++20, Dear ImGui kabuğu, docking, koyu tema, loglama | Planlandı |
| 2 | PE motoru: doğrulama, başlıklar, bölümler, data directory, RVA dönüşümü | Planlandı |
| 3 | Statik analiz: hash, string, entropi, import/export, resource, imza | Planlandı |
| 4 | Bulgu motoru ve açıklanabilir risk skoru | Planlandı |
| 5 | Çalışma zamanı motoru: süreç ağacı, modül, dosya ve ağ etkinliği | Planlandı |
| 6 | Ağ / dosya / registry telemetrisi | Planlandı |
| 7 | Bellek analizi | Planlandı |
| 8 | YARA entegrasyonu | Planlandı |
| 9 | JSON / HTML raporlama ve vaka dışa aktarma | Planlandı |
| 10 | Davranış grafiği, global arama, soruşturma çalışma alanı | Planlandı |

## Güvenli analiz önerileri

- **Statik analiz** örneği çalıştırmaz, ancak analiz edilen her dosyayı güvenilmez kabul edin.
- **Dinamik analiz** hedefi çalıştırır. Yalnızca sanal makine veya Windows Sandbox gibi **izole bir ortamda**, snapshot alarak, ağı kısıtlayarak veya kapatarak, paylaşılan klasör ve kişisel veri olmadan çalıştırın.
- İşlem düzeyi sandbox (job object, restricted token, AppContainer) ek bir katmandır, VM'in **yerine geçmez.**

ExWDIT, açık bir kullanıcı eylemi olmadan hiçbir örneği dışarıya yüklemez.

## Kullanım amacı

ExWDIT; **savunma amaçlı güvenlik araştırması, zararlı yazılım analizi, olay müdahalesi, dijital adli bilişim ve anti-cheat incelemesi** için tasarlanmıştır. Sorumlu kullanım ve açık bildirimi için [SECURITY.md](SECURITY.md) dosyasına bakın.

## Sıkça sorulan sorular

**ExWDIT nedir?**
ExWDIT; statik PE analizi, çalışma zamanı davranış izleme, delile dayalı risk skoru ve adli raporlama sunan, açık kaynaklı bir C++ masaüstü araç setidir.

**ExWDIT bir antivirüs veya zararlı yazılım tarayıcısı mı?**
Hayır. Bir inceleme ve analiz aracıdır. Kesin hüküm değil, açıklanabilir gösterge ve kanıt üretir.

**Anti-cheat ekiplerine yardımcı olur mu?**
Evet. Yüklü modüllere, beklenmeyen DLL'lere, süreç ağacına, bellek haritasına, yürütülebilir özel belleğe ve imzasız bileşenlere odaklanır. Anti-cheat atlatma işlevi içermez.

**Hangi platformlar destekleniyor?**
Önce Windows x64. Platforma özel kod arayüzlerin arkasında tutulur, böylece ileride Linux ve macOS desteği mümkündür.

**Kullanıma hazır mı?**
Henüz değil. Proje erken geliştirme aşamasında, bkz. [Yol Haritası](#yol-haritası).

## Anahtar kelimeler

`dijital adli bilişim` · `zararlı yazılım analizi` · `PE analizi` · `statik analiz` · `dinamik analiz` · `davranış analizi` · `tersine mühendislik` · `anti-cheat` · `süreç izleme` · `YARA` · `olay müdahalesi` · `C++20` · `Dear ImGui` · `Windows güvenliği`

## Katkı

Katkılar memnuniyetle karşılanır. Lütfen önce [CONTRIBUTING.md](CONTRIBUTING.md) dosyasını okuyun.

## Lisans

[MIT Lisansı](LICENSE) ile yayımlanmıştır.
