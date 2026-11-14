# Mobil Oyun Geliştirme Kılavuzu (Lemonade Rush deneyiminden)

> **Bu dosya kimin için:** Yeni bir oyun projesinde çalışan Claude Code oturumları ve geliştirici (ShisaGames).
> **Kaynak:** *Lemonade Rush: Bottle Sort* (Unity 6, iOS + Android), 24 Ekim – 15 Kasım 2026 arası, 80 commit, iki uzun Claude Code oturumu.
> **Nasıl kullanılır:** Yeni projede bu dosyayı oku. Önce **§1 Çalışma kuralları**, sonra projenin bulunduğu aşamaya göre **§3**'teki ilgili faz. Bir işe başlamadan önce **§5 Tuzaklar** tablosuna bak: aynı hatayı ikinci kez yaşamamak için var.
> Projeye özel ayrıntılar (dosya adları, ürün kimlikleri) örnek olarak verilmiştir; yeni projede adlar değişir, süreç aynı kalır.

---

## 0. Bir bakışta: 22 günde sıfırdan mağazaya

| Gün | Tarih | Ne oldu | Faz |
|---|---|---|---|
| 1 | 24 Eki | Boş 2D URP projesi → CLAUDE.md → Miro tasarımı + özellik PDF'inden **oynanabilir oyun** (tek gün). Kazanma kuralı, eşzamanlı dökme, 6 renk, gizli katman, çözücü hataları, Truck Race, Road Pass, Passport, League | 1–3 |
| 2 | 25 Eki | Profil + UGS giriş + leaderboard, Game Center bağlama, harita/replay/Free Mode, reklam molası, kamyon → **karavan + ekip**, şehir temaları, ortak renk paleti | 3–6 |
| 3–5 | 26–28 Eki | Sekmeler arası swipe, **10 dil** (Unity Localization), şişe ekranı temaları, sparkle efektleri | 6–7 |
| 6 | 29 Eki | **Mobil sürüm hazırlığı:** safe area, bundle ID, imzalama, ikon, splash, Unity IAP 5, LevelPlay, gizlilik + hesap silme, isim → *Lemonade Rush*, ABD fiyatları, ilk internal test build'leri | 8–10 |
| 7 | 30 Eki | Proje harici diske taşındı, iOS pod/CDN, IAP idempotency, Play Games + GameKit eklentileri, **güvenlik denetimi**, Cloud Code eklendi → **geri alındı**, mağaza metinleri + ekran görüntüsü üretici (10 dil), şirket **websitesi** | 9–13, 16 |
| 8–11 | 31 Eki–3 Kas | App Store header/arama görseli, mağaza videosu, ASO anahtar kelimeleri, Unity analytics kapatıldı, **iOS satın alma hatası** (Unity Connect kapalı kalmış) | 13–14 |
| 12 | 4 Kas | Restore/Road Pass sorunları (build 10→13), iOS'ta non-renewing, Android'de prepaid plan, **iOS review'a gönderildi** | 9, 15 |
| 13 | 5 Kas | Play listesi, formlar, Data safety, closed test yayında; **Apple 2.1 reddi** → cihaz videosu ile yeniden gönderim | 13, 15 |
| 17 | 10 Kas | Unity Diagnostics incelemesi (sorunlar Editor aracından) | 17 |
| 19–21 | 12–14 Kas | **UI polish:** Figma denetimi → HTML mockup turları → 30 bölgelik biyom haritası → uygulama; FTUE (gizlilik 3. bölüm sonrasına), **Firebase**, yeni ikon, build 14 → TestFlight + closed test | 17 |

**Çıkarım:** Oynanabilir prototip 1 günde çıkıyor. Asıl zaman **mağaza/platform/gizlilik/satın alma** işlerinde gidiyor (≈ toplam sürenin %60'ı). Yeni projede Faz 8–15'i erkenden planla.

---

## 1. Çalışma kuralları (kullanıcının yerleşik tercihleri)

Bunlar kullanıcının açıkça söylediği, her projede geçerli kurallar:

1. **Dil:** Kullanıcı Türkçe yazıyorsa Türkçe cevap ver. Kod, commit, kod yorumu ve CLAUDE.md İngilizce.
2. **Önce açıkla, onay bekle.** "Fikir yürütüyoruz, aksiyon alma" dendiğinde hiçbir dosyaya dokunma. Tasarım işlerinde: *önce görsel mockup → onay → koda uygula*. "Kodda değişiklik yapma, sadece görsel ilet" sık bir talimat.
3. **Gereksiz kod değiştirme.** Özellikle build öncesi ve "yeni build almak istemiyorum" dendiğinde sadece istenen satırlara dokun.
4. **Sormadan dosya oluşturma.** Kullanıcı istemeden plan/.md dosyası üretme (bir kez açıkça şikâyet etti).
5. **Gizli bilgiler `.env`'de.** Token, anahtar, şifre, keystore yolu, takım kimliği asla repoya, ekrana ya da commit mesajına girmez. `.env.example` sadece anahtar adlarını tutar. `.p8`, keystore ve `google-services.json` gibi dosyalar repo dışında veya git-ignored.
6. **Kalıcı silme yok.** Silmek gerekirse önce sor ve Çöp Kutusu'nu kullan. Silinecek şeyi önce göster.
7. **Şifre girme yok.** Konsol girişlerini, ödeme ekranlarını ve 2FA'yı kullanıcı yapar. Tarayıcı oturumunda kullanıcı giriş yaptıktan sonra devam et.
8. **Commit/push sadece istendiğinde.** Commit'e girmeyecekler: `.env`, `.DS_Store`, `.idea`, kişisel klasörler (`İdea`, `_Recovery`, `Temp`), Editor'un kendiliğinden değiştirdiği font/LFS dosyaları, Firebase config dosyaları.
9. **Dışa açık işlemler onaylı.** Mağazaya yükleme, review'a gönderme, deploy, mesaj gönderme ve indirme işlemlerinin her biri için ayrı onay al. Kullanıcı "submit'i ben yaparım" diyorsa yapma.
10. **Projeler harici SSD'de** (`/Volumes/Yedek Disk/...`). iCloud Desktop asla kullanılmaz. Gradle, DerivedData, CocoaPods, AVD ve Archives klasörleri harici diske symlink'li.
11. **Play mode'daki kullanıcıyı ezme.** Test save'i yazmadan önce `editor status` ile Play mode'u kontrol et. Save'i yedekle, işin sonunda geri yükle. Disk üstündeki save değiştiyse dur.
12. **Rapor tarzı:** Kısa, maddeli, durum net olsun: ne bitti, ne doğrulanmadı, sırada ne var. Kullanıcı "rapor" dediğinde tam olarak bunu bekler.
13. **Cloud Code kullanılmaz.** Oyun sunucu tarafı koda bağımlı olmamalı. Kullanıcı bunu açıkça seçti: maliyet ve bağımlılık istemiyor.

---

## 2. Teknik temel (her yeni projede kopyalanacak mimari)

### 2.1 Proje yapısı
- **Unity 6 + URP 2D**, portrait, iOS + Android.
- Her şey `Assets/<Oyun>/` altında. Üç assembly var:
  - `<Oyun>.Core`: `noEngineReferences: true`, saf C#. Kurallar, çözücü, seviye üretici, ekonomi, meta sistemler burada. **EditMode testleriyle** doğrulanır (Lemonade Rush'ta 161 test).
  - `<Oyun>.Game`: MonoBehaviour'lar, UI, servisler.
  - `<Oyun>.Editor`: import kuralları, setup menüleri, build hook'ları.
- **Bütün UI runtime'da kodla kurulur.** Sahnelerde sadece kamera + controller bulunur, `SceneBuilder` sahneleri yeniden üretir. Sahneye elle UI eklenmez. Sebep: Claude YAML sahne düzenleyemez, ama kodu düzenleyebilir.
- **Tasarım koordinatları:** mockup'ların 375×667 tasarım pikseli, sol üst orijin. `SafeCanvasScale` bu kutuyu safe area içine sığdırır. Minimum dokunma alanı 44 px, otomatik metin boyutu en az 10.
- **Servis bulucu + event bus:** `Services.Locator`, `Services.Bus` (ör. `CoinsChanged`). `GameApp`, `RuntimeInitializeOnLoadMethod` ile kendini kurar, böylece her sahne doğrudan Play edilebilir.
- **Save:** tek `SaveData` (versiyonlu JSON, `persistentDataPath`) + `MarkDirty()`.
- **Sesler kodla sentezlenir** (ses dosyası yok). Master ses kanalı yarıya kısılmış durumda.
- **New Input System only.** `UnityEngine.Input` çağrısı exception fırlatır.

### 2.2 Claude ile Unity'yi sürmek
```bash
unity status                                   # Editor "ready" mi?
unity command script recompile                 # sonra: unity command script recompile_status
unity command console status                   # derleme hatası var mı
unity command test run --mode EditMode --filter <Oyun>
unity command editor invoke_menu --path "<Oyun>/Setup/Run All Setup Steps"
unity command script eval_file --file /path/x.cs   # tek seferlik C# (eval --code boş dönebiliyor)
unity command capture game_view --save_path /tmp/shot.png
unity command build run --target Android --outputPath ... --confirm true
unity command editor set_auto_tick --enable true   # Editor odakta değilken de çalışsın
```
- `unity test` / `unity build` (`command` olmadan) ikinci bir batch Editor açar ve proje açıkken başarısız olur. **Her zaman `unity command ...` kullan.**
- Build sırasında Editor komutlara cevap vermez. `Logs/Editor.log` dosyasını (proje içindeki) izle.
- Uzun işler (seviye üretimi) domain reload'da ölür. Bitti mesajını bekle, araya recompile sokma.
- Pipeline paketi: `com.unity.pipeline`. Derleme hatası varsa Editor Safe Mode'a düşer ve CLI bağlanamaz: önce hatayı düzelt.

### 2.3 Menü tabanlı setup (tekrarlanabilirlik)
`<Oyun>` menüsünde tek tıklık adımlar: TMP essentials, art reimport, fontlar, kozmetikler, localization, sahneler, player settings, ikonlar, achievement ID'leri, build numarası artırma. **Her elle yapılan ayar bir menü adımına dönüştürülür.** Claude bunları `invoke_menu` ile çağırır.

### 2.4 Art pipeline (kodla çizilen sprite'lar)
- Kaynak: `Tools/ArtSource/defs.svg` + Python modülleri (`toon.py`, `clay.py`, `lowpoly.py`, `themes.py`, `icon_*.py`).
- `build_art.py`, headless Chrome ile 3x render alır, `sips` ile kırpar, `Resources/Art/*.png` dosyalarını ve `art_manifest.json`'u yazar (9-slice kenarları, parça yerleşimi).
- `ArtImporter` manifest'i uygular (PPU 300 → 1 tasarım px = 3 texture px).
- **PNG'leri elle düzenleme**, SVG/Python'u düzenle ve yeniden render al.
- Garage'ın tint'lediği parçalar gri tonlu çizilir.
- Avantaj: Claude bütün görselleri kendisi üretip yineleyebiliyor. Mockup → onay → aynı kodla oyuna.

---

## 3. Fazlar: adımlar, araçlar, çıktılar

Her fazda: **Amaç → Adımlar → Çıktı → Dikkat.**

### Faz 0 — Proje kurulumu (½ gün)
1. Unity Hub'da yeni proje: 2D URP, **harici diskte**.
2. `git init`, Unity `.gitignore`, **Git LFS** (`*.png`, `*.psd`, `*.unitypackage`...). GitHub repo oluştur.
3. `com.unity.pipeline` kur (`unity pipeline install`), `unity status` ile doğrula.
4. Claude ile `/init` → **CLAUDE.md**. Proje büyüdükçe güncellenir: komutlar, mimari, tuzaklar. CLAUDE.md, oturumlar arası en değerli hafıza.
5. `.env.example` + `LocalEnv.cs` (Editor `.env` okuyucu) + `.gitignore`'da `.env`.
6. Branch düzeni: feature branch'leri → PR → `main`.

### Faz 1 — Tasarım girdisi → oynanabilir prototip (1 gün)
**Girdi:** Miro board (UI mockup'ları) + özellik listesi PDF'i (ID'li: `GP-04`, `LV-02`, `MO-05`...).
1. Claude'a Miro MCP ile board'u, PDF ile özellikleri okut. Kod yorumlarında özellik ID'lerini kullan.
2. Önce **Core kuralları** (saf C#) ve testler, sonra UI.
3. Ekranlar: Boot (splash, onay), Main (5 alt sekme + tam ekran sayfalar), Puzzle (intro, tahta, sonuç). Sahneler arası durum `Session` içinde.
4. İlk PR'ı aç, kullanıcı oynayıp geri bildirim verir.

**Dikkat:** Kullanıcının oyun kuralı beklentisini **kelimesi kelimesine** koda dök. Örnek: "Boş şişe serbest; dolu her şişe tam dolu ve tek renk olmalı; her geçerli dökmeden sonra kontrol et." Bu tanım testle sabitlenmeli.

### Faz 2 — Çekirdek mekanik ve seviyeler
1. **Kazanma kuralı** ve **dökme kuralları** `PuzzleRules` içinde, birim testli.
2. **Eşzamanlı animasyonlar:** model anında güncellenir, animasyon kuyruğa alınır. Oyuncu hiç beklemez, sadece dökmekte olan şişe kilitlenir. Kazanma ve çıkmaz kontrolleri animasyonlar bitince yapılır.
3. **Çözücü:** BFS (minimum hamle, state hashing) + yönlendirmeli DFS fallback + `IsDeadEnd`.
4. **Seviye üretici:** sabit seed (`level * 7919`). İlk 3 seviye elle yapılır. Zorluk skoru "testere dişi" eğri izler.
5. **Engeller tek tek tanıtılır** (5, 7, 9, 11, 13. seviyeler), sonra her 10 seviyenin 6'sında görünür. 2'den fazla düz seviye üst üste gelmez.
6. 300 seviye JSON olarak üretilir (worker thread'de, birkaç dakika). Sonrası runtime'da aynı seed'le üretilir. `level_summary.csv` zorluk raporunu tutar.
7. **Test seviyeleri** iste ve kullan: "her özelliği içeren seviye 61", "Free Mode testi için seviye 301".

**Dikkat (yaşandı):** Sahte "Out of moves!" hatası iki kez çıktı. Sebepleri: şişeyi rengin tamamını tutmadan "mühürlemek" ve gerekli hamleleri budamak. Çıkmaz tespiti **muhafazakâr** olmalı. Şüphe varsa "çıkmaz" deme.

### Faz 3 — Meta sistemler (retention)
Hepsi `Core/Meta` içinde, sunucuya taşınabilir şekilde yazılır:
- **Ekonomi:** coin, yıldız (seviye başına 3), ürünler.
- **Hot Streak:** x3'ten başlar, ödül olarak undo/shuffle verir. Başlangıçta 1 ücretsiz undo.
- **Daily Gift.**
- **Road Pass** (sezon kartı): yıldız eşiklerine göre (10, 21, 33…735) **elle claim edilir**. Premium ödüller pass ister.
- **Passport:** damgalar = platform achievement'ları (30 adet) + 100 coin.
- **Weekly League:** Iron → Diamond, 30 kişilik gruplar, ilk 5 yükselir, son 5 düşer. 7 gün offline olan çıkarılır, roster 30'da tutulur. Rakipler simüle edilir.
- **Truck Race:** 25 sürücü, 6 saatte kazanılan yıldıza göre sıralama, offline çalışır.
- **Harita:** 300 seviye, 30 bölge. Replay'de sadece önceki rekorun üstündeki yıldızlar sayılır. 300'den sonra Free Mode başlar (3 can).
- Hazır olmayan özellik "Coming Soon" olarak gösterilir (Sticker Album).

**Dikkat:** Her sistemi kullanıcıya önce **açıkla**, kullanıcı detay kuralı verir ("offline 7 gün olanı çıkar"), sonra uygula. Ödül tablolarını kullanıcıya liste olarak göster.

### Faz 4 — Profil ve online (UGS)
1. **Unity Gaming Services:** Authentication (herkes açılışta anonim giriş yapar), Cloud Save (en fazla dakikada bir ve pause'da yükleme), Leaderboards (`stars_all_time`, `league_weekly`, `free_mode_best`; config dosyaları repoda, *Services > Deployment* ile deploy edilir).
2. Paketler yoksa kod offline stub'a derlenir (asmdef `versionDefines`).
3. **Platform hesabı bağlama** (isteğe bağlı, misafir oynanabilir): Google Play Games (Android), Game Center (iOS). Eklentiler Assembly-CSharp'taki **bridge**'ler üzerinden, define'larla (`JUICE_PLAY_GAMES`, `JUICE_GAME_CENTER`) bağlanır.
4. **Profil:** isim + oyun içi avatar (gerçek hesap fotoğrafı kullanılmaz). `NameFilter` (uzunluk, karakter, EN/TR küfür + leetspeak), isim 24 saatte bir değişebilir. Ranking etiketleri rich text'siz.
5. **Skor güvenliği, Cloud Code olmadan:** skorlar ilerlemeyle sınırlanır (`ScoreLimits`). Hile yapanlar dashboard'dan silinir.
6. `Online.Start()` **Awake'te değil Start'ta** çağrılır (UGS paketleri kendi BeforeSceneLoad hook'larında kayıt oluyor).
7. Bir **hesap kurulum checklist'i** artifact'i hazırla: konsol adımları, credential'lar, sıra.

### Faz 5 — Sanat yönü (en çok yineleme yapılan alan)
Yaşanan sıra: düz SVG → **low-poly denemesi** (kullanıcı: "şirinliği kaybettirdi") → yumuşak low-poly → kullanıcının eklediği karavan asset'i → **toon karavan** (mürekkep çizgili) → **clay karakterler** → harita için **soft-3D, çizgisiz** (round 7'de onaylandı).
**Kurallar:**
1. Önce referans iste/topla (Unity Asset Store linkleri, Kenney, Instagram hesapları, rakip ikonlar).
2. **Mockup turları:** her turda 3–6 varyasyon, numaralı (A/B/C, C1/C2...). Kullanıcı birini seçer ya da "X'in varyasyonlarını üret" der.
3. Onaylanan stil hafızaya yazılır (ör. *soft-3D: çizgi yok, ışık sol üstten, gölge sağ alttan, 3 tonlu ağaç kümeleri*).
4. Mockup dosyaları git-ignored `Builds/UIPolish/` altında. Onaydan sonra stil `Tools/ArtSource` modülüne taşınır.
5. Garage parçaları değişirken **mevcut item ID'leri korunur**, sahipler item'larını kaybetmez. Road Pass ve Passport ödülleri dolaylı olarak güncellenir.

**Kullanıcının sevdiği:** şirin, yumuşak, hyper-casual. Sevmediği: "Paint'te çizilmiş gibi", kalın siyah çizgiler, aşırı cıvıl cıvıl arka plan (oyuncu geri bildirimi: göz yoruyor).

### Faz 6 — UI/UX tutarlılığı ve cila
1. **Tek palet:** 5 ana sayfa aynı arka planı (krem → şeftali), beyaz kartları ve aynı TopBar'ı kullanır. Turuncu = satın al, nane yeşili = başla.
2. Sekmeler arası **parmakla takip eden swipe + snap**.
3. Şişe ekranı **temaları** Garage kozmetiği olarak satılır (varsayılan sade gece teması).
4. Küçük ödüllendirici animasyonlar: sparkle, kart açılışı, ödül uçuşu.
5. **UI polish süreci (Kasım):** Figma MCP ile denetim (her ekran + bulgular, ör. Almanca/Türkçe taşma hataları) → stil sayfası (değişkenler, bileşenler) → HTML mockup'lar → kullanıcı Figma'ya yorum bırakır → yeni tur → onay → uygulama.
   - Kullanıcı bazı yeniden tasarımları reddedebilir ("Bottle sort sayfası orijinal kalsın"). Çekirdek oyun ekranını değiştirme önerisi temkinli olmalı.
6. Her uzun metin için **en uzun dilde** (DE/TR) taşma kontrolü yap. Uymayan buton etiketleri otomatik küçülür (yerel fiyatlar için gerekli).

### Faz 7 — Yerelleştirme
1. Dil seçimi **veriye dayalı:** App Store Puzzle kategorisinde gelire göre ilk 8 dil + Türkçe → en, ja, zh-Hans, ko, de, zh-Hant, fr, es, it, tr. Uygulamadan önce kaynağı kullanıcıya göster.
2. `com.unity.localization`, tablolar Addressables olarak gelir.
3. Kod içinde: `Loc.T("shop.coin_packs", "Coin Packs")`, `{0}` placeholder kullanılır, **string interpolation ve birleştirme yasak**. İngilizce düz literal yazılır (sync aracı regex ile okuyor).
4. *Sync English Strings* menüsü key çakışmalarını ve kullanılmayan key'leri raporlar.
5. Çeviriler `Tools/Localization/<code>.json` (düz key → metin). *Build Localization* bunları tabloya alır ve **sadece kullanılan karakterlerle** CJK font atlası üretir. CJK metin değişince yeniden çalıştır, yoksa eksik karakter kutu olarak görünür.
6. Lilita One gibi fontlarda ğ/ş/İ yok, fallback font gerekir.
7. Mağaza metinleri, sürüm notları ve websitesi de **aynı 10 dilde** hazırlanır.

### Faz 8 — Mobil sürüm hazırlığı
1. **Bundle ID** baştan doğru seçilir (sonradan değiştirmek mağaza kaydını bozar): `com.<şirket>.<oyunadi>`.
2. **İmzalama `.env`'den:** `ReleaseSigning.cs` build'den hemen önce Apple Team ID ve Android keystore bilgisini yazar, build sonrası temizler. `ProjectSettings.asset`'e asla gizli bilgi girmez.
3. *Apply Player Settings* menüsü: release ayarları, IL2CPP, ARM64, minimum OS, portrait.
4. **App ikonu** (`build_icons.py` + *Apply App Icons*): iOS, App Store 1024, Android adaptive (ön plan %62 ölçek, güvenli daire). Splash'te şirket logosu.
5. **Cihaz testi:** iOS Simulator (SE + Pro Max + iPad), Android emulator (yalnızca donanım GPU + emulator 37.2+ ile çalıştı; Unity Vulkan'da takılabiliyor, **gerçek telefon şart**).
6. Unity **Device Simulator** ve *Preview* menüsü ile safe area ve notch simülasyonu.
7. **Build numarası** her yüklemeden önce artırılır (*Bump Build Number*). Version name aynı kalabilir.
8. Mac aşırı yüklenirse bir seferde tek simülatör veya build çalıştır.

### Faz 9 — Para kazanma (IAP + reklam)
**IAP (Unity IAP 5, `IStore` arkasında):**
1. Önce `SimulatedStore` ile tüm akış kurulur, sonra gerçek mağaza aynı arayüzün arkasına takılır.
2. **Ürün listesi** (kullanıcıya tablo olarak ver: ID, tür, fiyat, açıklama, ikon):
   - Coin paketleri: **consumable**, $1.99–$49.99.
   - Starter Pack: non-consumable, bir kez alınır.
   - No Ads: **non-consumable**, $4.99. Her zaman kapsamını ve istisnalarını açıklayan bir popup'tan satılır.
   - Road Pass: aylık sezon. **iOS'ta non-renewing subscription** (`road_pass_season`). **Android'de prepaid plan** (1 ay, uzatmasız), böylece restore edilebilir.
3. **Fiyatlandırma:** ABD fiyatını ver, mağaza diğer ülkeleri otomatik dönüştürür. Türkiye için ayrıca bak.
4. **Restore** butonu (iOS zorunlu). Restore sonucunu her zaman bildir ("geri yüklendi" / "bulunamadı").
5. **Idempotency:** transaction ID yoksa da çift ödül verme (`PurchaseLedger`). Consumable'lar `FetchPurchases`'ta dönmez.
6. Mağazanın hata sebebini oyuncuya göster (kör "başarısız" mesajı yerine).
7. **Test parası:** Android'de test sırasında **gerçek karttan çekim yaşandı**. Önce Play Console > *License testing*'e test hesaplarını veya Google Group'u ekle. iOS'ta sandbox / TestFlight ücretsizdir.
   - ⚠️ License tester grubu production öncesinde **kaldırılmalı**, yoksa o kişiler canlıda da bedava alır.
8. Google Play lisans anahtarını Unity Dashboard'a gir.

**Reklam (LevelPlay):**
1. `IAds` arkasında `SimulatedAds` → `LevelPlayAds`. App key ve ad unit ID'leri `.env`'den, `LevelPlayKeysWriter` ile yazılır.
2. **Reklam politikası** saf kurallarla (`AdRules`): her 10. seviyede mola, 25. seviyeden sonra her 3 galibiyette bir, 10 seviyeden fazla reklamsız geçmez. Başarısızlık ve replay sonrasında reklam yok. **İlk 3 seviyede ve gizlilik onayı verilmeden reklam yok.**
3. Ödüllü video oyuncunun kendi başlattığı bir şeydir ve No Ads ile de kalır.
4. iOS'ta reklamdan önce **ATT** istenir (`NSUserTrackingUsageDescription`).
5. `app-ads.txt` websitesinde yayınlanır.
6. ILRD (gösterim başına gelir) Firebase'e `ad_impression` olarak gönderilir.

### Faz 10 — Gizlilik ve uyum
1. **İki ayrı onay:** kişiselleştirilmiş reklam ve analitik, ikisi de **varsayılan kapalı**. Bildirim metni ile onaylar ayrı tutulur (KVKK kararı). Hayır demek oyunu kısıtlamaz.
2. Bildirim versiyonlanır (`NoticeVersion`), metin değişince bildirim yeniden gösterilir.
3. **FTUE için:** yeni oyuncu doğrudan ana menüye düşer, gizlilik popup'ı **3. bölüm sonunda** gelir. Onay gelene kadar LevelPlay, online giriş, Firebase ve reklam beklemede kalır.
4. **Hesap silme** (Apple zorunlu): sadece save'in sahibi olan oyuncu silinir. Cloud Save + Unity Auth hesabı + ranking ismi + yerel save temizlenir. Oturum kaybolduysa oyuncu web'deki silme sayfasına yönlendirilir. Silme ekranı ücretli ürünlerin restore ile geri gelebileceğini söyler.
5. **Unity Analytics ve engine diagnostics kapalı** (onaydan önce hiçbir şey gönderilmemeli). Ama `UnityConnectSettings` → `m_Enabled: 1` **açık kalmalı** (bkz. §5).
6. Firebase: koleksiyon manifest ve plist'te kapalı başlar, onay gelince açılır (`SetConsent`).
7. Gizlilik politikası, kullanım şartları ve silme sayfası websitesinde **10 dilde**. `PrivacyInfo` sınıfı iletişim e-postasını ve URL'leri tutar.
8. Metinler hukuki inceleme ister. Kullanıcıya bunu belirt.

### Faz 11 — Platform servisleri (achievement, giriş)
1. **Google Play Games:** Play Console'da Games Services projesi, 3 credential (Android, web/OAuth...), Unity Dashboard'da provider. Achievement'lar **CSV/XML zip ile toplu import** edilir. Plugin v11+, ID'ler `Resources/AchievementIds`'e.
2. **Game Center:** App Store Connect API ile 30 achievement **otomatik** oluşturuldu (10 dil, ikonlar). ASC API anahtarı `.env`'de (`ASC_KEY_ID`, `ASC_ISSUER_ID`, `ASC_KEY_PATH`), `.p8` repo dışında. Apple GameKit plugin'i + Game Center capability.
3. Achievement'lar App Store sürüm taslağına eklenir.
4. Platform plugin'i yokken (Editor'da) unlock'lar yerelde kalır ve kuyrukta bekler.

### Faz 12 — Güvenlik denetimi
1. Build almadan önce **security-audit** skill'i ile tüm proje taranır: isim enjeksiyonu, hesap silme, profil değiştirme, onay atlatma, skor manipülasyonu.
2. Bulgular düzeltilir, ardından hızlı bir yeniden test yapılır.
3. Yayın öncesi kısa bir yeniden denetim yapılır (3 Kasım'da yapıldı).

### Faz 13 — Mağaza listesi ve ASO
1. **Tek kaynak:** `Tools/StoreListing/listing.json`. 10 dilde isim, alt başlık, promo metni, açıklama, anahtar kelimeler, Play başlığı, kısa açıklama ve caption'lar burada. `check.py` mağaza limitlerini doğrular (isim 30, keywords 100 byte, virgülden sonra boşluk yok...).
2. Açıklama **kanca cümlesiyle** başlar. Doğrulanamayan iddialar ("elle ayarlanmış") yazılmaz.
3. **Görseller kodla üretilir:** `render_screens.py` (10 dil ekran görüntüleri, iPhone + iPad + Duo boyutları), `render_header.py` (App Store header: 4 eğik ekran), arama sonucu görseli (3840×2560), `render_play_assets.py` (feature graphic, telefon/tablet), `render_video.py` (24 sn, müzikli; YouTube Shorts, TikTok ve Instagram için ortak uzunluk), YouTube banner.
4. Apple'ın asset best practice sayfasına uy.
5. Play: 7" ve 10" tablet ekran görüntüleri. PC, XR ve Desktop alanları opsiyonel, boş bırakılabilir.
6. 10 dilde sürüm notları.
7. In-App Events (App Store) sezon ve etkinlik tanıtımı için değerlendirilir.

### Faz 14 — Build ve yükleme
**Android:**
1. Editor Android hedefinde olmalı (platform değişimi = tam reimport, uzun sürer).
2. `unity command build run --target Android` → `.aab` + `*.symbols.zip`.
3. Doğrula: `jarsigner -verify`, versionCode.
4. Play Console'a kullanıcı yükler. Native debug symbols zip'i App bundle explorer'dan eklenir.

**iOS (çalışan tarif):**
1. Editor'un **iOS için derlediğinden** emin ol (`#if UNITY_IOS` kodlar). Gerekirse `CompilationPipeline.RequestScriptCompilation(CleanBuildCache)`.
2. Xcode kapalıyken `unity command build run --target iOS`.
3. `xcodebuild archive ... CODE_SIGNING_ALLOWED=NO`.
4. Arşivdeki `.app`'i **Apple Distribution** kimliği ve entitlements ile imzala: `com.apple.developer.game-center`, `beta-reports-active`, `get-task-allow=false`.
5. `xcodebuild -exportArchive` + `ExportOptions.plist` (method `app-store-connect`).
6. Kontrol: `codesign -d --entitlements - --xml`, build numarası, plist anahtarları.
7. `xcrun altool --upload-app --apiKey ... --apiIssuer ... --p8-file-path ...` (değerler `.env`'den).
8. ASC API ile build durumunu izle: `processingState VALID` → TestFlight internal grubu (tüm build'leri alır).
9. `ITSAppUsesNonExemptEncryption=false` → export compliance sorusu gelmez.

**Her build öncesi checklist:** §4.1.

### Faz 15 — Test kanalları ve review
**iOS:**
- **TestFlight internal:** anında, review yok. External grup kısa bir beta review ister.
- App Review: sürüm sayfasında *App Review Information* (ad, telefon, e-posta; bunlar herkese açık değil) ve demo notları bulunur. Release'i **manuel** seç.
- Sık ret sebebi **2.1 Information Needed:** gerçek cihazda **ekran kaydı videosu** + ayrıntılı review notu gönderilir (ASC API ile). Video kayda yeni hesapla, sıfırdan başlanır: onay ekranı, oynanış, satın alma, restore, hesap silme gösterilir.
- 40 IAP ürünü ilk sürümle birlikte review'a gider. Sonraki build'lerde sadece yeni build review edilir.
- Review sürerken yeni build yüklemek review'ı bozmaz. Seçili build değiştirilmedikçe sorun yok.

**Android:**
- Internal testing → **closed testing**: kişisel geliştirici hesaplarında production için **12 opt-in tester, 14 gün** şartı var. Tester'lar link veya Google Group ile davet edilir. Davet için e-posta şablonu hazırla.
- Closed test'e **yeni build eklemek 14 günlük sayacı sıfırlamaz**. Release'i oluştur, sonra *Publishing overview*'dan "Send for review" ile gönder.
- Formlar: Data safety, içerik derecelendirmesi (dikkat: yanıtlar yaş derecesini değiştirir), reklam beyanı, hedef kitle, **trader status** (tüccar = adres herkese açık; sanal ofis yoksa kişisel adres), AI etiketleri.
- Uyarılar: debug symbols ve deobfuscation dosyası. Bunlar engelleyici değil, ama symbols zip'i her build'de yükle.

### Faz 16 — Şirket varlığı (paralel iş)
- **Website:** Astro 5 + Tailwind 4, Netlify (GitHub bağlantılı otomatik deploy), alan adı + ücretsiz SSL + **HSTS** ve güvenlik header'ları (`netlify.toml`), 10 dil, gizlilik/şartlar/silme sayfaları, `app-ads.txt`, beehiiv bülten embed'i, sosyal medya linkleri. Kişisel isim **gösterilmez**, anonim şirket kimliği kullanılır.
- **Mail:** Zoho, alan adı üzerinden. İletişim formu ziyaretçinin dilini korumalı.
- **Sosyal:** YouTube (banner 2048×1152, ≤6 MB), Shorts, kanal açıklaması, topluluk anketleri. Facebook, TikTok, X, Instagram.
- Website değişiklikleri PR ile yapılır. Kullanıcı test eder, "merge et" der, sonra canlıya alınır.

### Faz 17 — Yayın sonrası / live ops
1. **Diagnostics:** Unity Dashboard'daki sorunları incele, önce kaynağı ayır: Editor mü, test telefonu mu, gerçek oyuncu mu (Lemonade Rush'taki sorunlar `DevicePreview` Editor aracındandı).
2. **Firebase (Analytics + Crashlytics), onay arkasında.** Firebase 14 iOS'ta **Swift Package** kullanır, eski MDR CocoaPods yazar → `FirebaseSwiftPackages.cs` post-process gerekir (bkz. §5).
3. **Soft launch planı:** 1 hafta, ~5000₺, Tier-2 İngilizce konuşan ülkeler. Ölçülecekler: D1/D7 retention, seviye funnel'ı, reklam ARPDAU, IAP dönüşümü.
4. **İkon yenileme:** rakip analizi → onlarca varyasyon → seçim (*C2 Spritz hour*) → `icon_spritz.py` → oyun + mağaza.
5. Her güncellemede: build numarası artır, iki mağazaya da yükle (iOS TestFlight → review; Android closed test release).

---

## 4. Checklist'ler

### 4.1 Build öncesi
- [ ] `unity command console status` temiz, testlerin hepsi geçiyor.
- [ ] *Bump Build Number* çalıştırıldı (iOS build + Android versionCode).
- [ ] Değişen metinler için *Sync English Strings*. CJK metin değiştiyse *Build Localization*.
- [ ] `UnityConnectSettings.asset` → `m_Enabled: 1` (`UnityServicesBuildCheck` bunu otomatik yapıyor). Unity Purchasing açık.
- [ ] iOS için: Editor iOS hedefinde derlendi, Device SDK seçili (Simulator SDK değil). Podfile'da iOS 15 satırı var.
- [ ] Android için: Editor Android hedefinde, keystore `.env`'de.
- [ ] Gizlilik metni değiştiyse `NoticeVersion` artırıldı.
- [ ] Değişiklikler commit'lendi (kullanıcı onayıyla).

### 4.2 Yükleme sonrası
- [ ] iOS: IPA entitlement'ları (game-center, get-task-allow=false), ASC'de `VALID`, internal grupta.
- [ ] Android: AAB imzası doğrulandı, symbols zip hazır, kullanıcıya yolu verildi.
- [ ] Kullanıcıya **test listesi** verildi (madde madde: ilk açılış, oyun, hesap, satın alma, restore, reklam, achievement, offline).

### 4.3 Yayın günü
- [ ] Leaderboard'lar sıfırlandı (test satırları kişisel isim içeriyor).
- [ ] Play License testing'den herkese açık tester grubu kaldırıldı.
- [ ] App Privacy (Apple) ve Data safety (Google) son SDK listesiyle uyumlu (Firebase eklendiyse güncellendi).
- [ ] Websitesindeki gizlilik politikası güncel.
- [ ] Manuel release butonu kullanıcıda.

---

## 5. Tuzaklar (yaşananlar → çözüm)

| Belirti | Sebep | Çözüm |
|---|---|---|
| Unity okumada donuyor, garip I/O hataları | Proje iCloud Desktop'ta | Projeyi harici diske taşı |
| Android IL2CPP build "No space left on device" | Dahili disk dolu | Gradle, DerivedData ve cache'leri harici diske symlink'le |
| Google `sdkmanager` çalışmıyor | Unity yolunda boşluk ("Yedek Disk") | External Tools'u `~/Library/Android/unity/{jdk,sdk,ndk}` symlink'lerine yönlendir |
| Gradle "Cannot convert URI file:////Volumes/Yedek Disk/..." | Resolver'ın local Maven yolunda boşluk | `GradleLocalRepoPath.cs` (IPostGenerateGradleAndroidProject) URI'yi encode eder |
| iOS archive: pod deployment target, `AppleCore_*` undefined | Editor en son Android için derlemiş | iOS hedefinde CleanBuildCache ile yeniden derle |
| iOS Firebase framework'leri yok, link hatası | Firebase 14 SPM kullanıyor, MDR sadece pod yazıyor | `FirebaseSwiftPackages.cs` PostProcessBuild ile Swift paketlerini UnityFramework'e ekler |
| Apple build'i reddediyor: `BUILD_INDICATES_GAME_CENTER_DISABLED` | `CODE_SIGNING_ALLOWED=NO` entitlement'ı düşürdü | `.app`'i entitlements ile yeniden imzala, sonra export et |
| iOS satın alma ödeme ekranı açılmadan başarısız | `UnityConnectSettings m_Enabled: 0` (analytics kapatırken kapanmış) | `m_Enabled: 1`, `UnityServicesBuildCheck` her build'de açar |
| Restore "nothing to restore" / Road Pass gelmiyor | Ürün türü ve restore mantığı | iOS: non-renewing + kendi takibi; Android: prepaid plan; restore sonucu her zaman raporlanır |
| "DuplicateTransaction", tekrar alınamıyor | Mağaza ürünü hesaba bağlı tutuyor | Non-consumable'ı restore yolu ile geri ver, kullanıcıya açıkla |
| Android testte gerçek karttan para çekildi | License testing ayarlı değildi | Test hesaplarını ve grubu License testing'e ekle, iadeyi kullanıcı yapar |
| Sahte "Out of moves!" | Çözücü fazla agresif budama yapıyor | Çıkmazı sadece kesinse bildir, testle sabitle |
| Ekran geçişinde kare görünüyor | İç içe canvas varsayılan 100×100 rect'te kalıyor | Nested canvas'ı stretch et |
| `UnityEngine.Input` exception | New Input System only | InputSystem API'si kullan |
| `PlayMode` isim çakışması | `UnityEngine.PlayMode` | Kendi enum'una farklı isim ver (`LevelMode`) |
| UGS Leaderboards initialize olmuyor | `Online.Start()` Awake'te çağrılmış | `GameApp.Start` içinde çağır |
| Play mode'da static cache bayat kalıyor | Domain reload kapalı | `SubsystemRegistration` reset'i ekle |
| Unity build: "Another bee process is already working" | Takılı `bee_backend` | Process'i öldür, Editor'u yeniden başlat |
| Diagnostics'te Editor hataları, 8,6 GB log | `DevicePreview` olmayan Game view boyutunu (-1) seçiyordu | Bulunamazsa seçme, uyarı yaz |
| Kullanıcının save'i kayboldu | Capture script'i Play mode sırasında eski yedeği geri yazdı | §1 madde 11 |
| `script eval --code` boş dönüyor | CLI davranışı | `eval_file` kullan |
| Background bekleme script'i bozuluyor | `wc -l` çıktısındaki boşluk | `tr -d ' '` kullan |
| Android emulator splash'te takılıyor | Mesa/MoltenVK Vulkan | Gerçek cihazda test et |
| Unity Analytics/diagnostics onaydan önce veri gönderiyor | Unity varsayılanları | Analytics ve diagnostics kapalı, Connect açık |

---

## 6. Yeni projeye taşınabilecek dosyalar ve araçlar

| Dosya | İşi |
|---|---|
| `Scripts/Editor/LocalEnv.cs`, `ReleaseSigning.cs` | `.env` okuma, build anında imzalama |
| `Scripts/Editor/UnityServicesBuildCheck.cs` | Connect ve IAP ayarını build öncesi açık tutar |
| `Scripts/Editor/IosBuildSettings.cs`, `IosPodfileSettings.cs` | Plist anahtarları (şifreleme, ATT, Firebase onayı), pod iOS 15 |
| `Scripts/Editor/GradleLocalRepoPath.cs`, `FirebaseSwiftPackages.cs` | Boşluklu yol ve Firebase SPM düzeltmeleri |
| `Scripts/Editor/PlatformAccountSetup.cs`, `AchievementIdsBuilder.cs` | Platform define'ları, Game Center capability, achievement ID'leri |
| `Scripts/Editor/AppIcons.cs`, `SplashSetup.cs`, `SceneBuilder.cs`, `DevicePreview.cs`, `LocalizationSetup.cs`, `FontSetup.cs` | Setup menüsü adımları |
| `Scripts/Game/UI/UIKit.cs` (SafeZone dahil), `Loc.cs` | Kodla UI, safe area, yerelleştirme yardımcıları |
| `Scripts/Game/Services/Monetization.cs`, `UnityIapStore.cs`, `LevelPlayAds.cs`, `Analytics.cs`, `Online.cs`, `PrivacyInfo.cs` | Arayüz arkasında IAP, reklam, analitik, UGS, gizlilik |
| `Scripts/Core/Meta/Ads.cs`, `PurchaseLedger.cs`, `Profile.cs` (NameFilter), `ScoreLimits.cs`, `League.cs` | Saf C# kurallar (test edilebilir) |
| `Tools/ArtSource/*` | SVG/Python sprite ve ikon pipeline'ı |
| `Tools/StoreListing/*` | listing.json + check.py + ekran görüntüsü, header, video ve Play asset üreticileri |
| `Tools/Localization/*.json` | Çeviri formatı |
| `.env.example` | Gerekli gizli anahtar listesi |
| Website repo (Astro + Netlify) | Şirket sitesi şablonu, gizlilik ve silme sayfaları |

---

## 7. Yeni bir oyun için önerilen sıra (hızlandırılmış)

1. **Gün 0:** Faz 0 + şirket hesapları paralelde açılır (Apple Developer, Play Console; kişisel hesapta 14 günlük closed test şartı var, erken başla). Bundle ID kararı. Website ve gizlilik URL'si hazırsa yeniden kullan.
2. **Gün 1:** Tasarım (Miro/Figma) + özellik listesi → Core kuralları + testler → oynanabilir prototip.
3. **Gün 2–3:** Meta sistemler (bu repodaki Core/Meta'dan uyarla) + sanat yönü mockup turları.
4. **Gün 4:** Yerelleştirme altyapısı (ilk günden `Loc.T` ile yaz, sonradan taşımak pahalı) + safe area UI.
5. **Gün 5:** Simüle IAP ve reklamla akış → gizlilik ve onay → **internal test build'leri** (iki platform). Closed test sayacını başlat.
6. **Gün 6–7:** Gerçek IAP + LevelPlay + UGS + platform girişleri + achievement'lar (ASC API ve toplu import ile) → güvenlik denetimi.
7. **Gün 8–9:** Mağaza listesi (`listing.json`, render script'leri), formlar, review'a gönderim (iOS'ta cihaz videosunu baştan hazırla).
8. **Sonra:** Firebase + soft launch + UI polish + live ops.

**Baştan yapılsaydı farklı yapılacaklar:**
- Firebase ve analitik onayını gizlilik akışıyla **birlikte** tasarla (sonradan NoticeVersion artırmak gerekti).
- Road Pass ürün türünü (iOS non-renewing / Android prepaid) baştan doğru seç. 4 build bu yüzden harcandı.
- License testing'i ilk Android IAP testinden **önce** ayarla.
- App Review için cihaz videosu ve demo notlarını ilk gönderimde ekle.
- Gizlilik popup'ını baştan FTUE'yi bozmayacak yere (3. bölüm sonu) koy.
- Sanat stilini (soft-3D, çizgisiz) erken kilitle, sonradan yeniden çizmek pahalı.

---

## 8. Araç katmanları ve eklentiler

Bütün süreci tek başına kapsayan hazır bir araç yok. Önerilen dört katman:

| Katman | Araç | Durum / not |
|---|---|---|
| 1. Unity sürme ve geliştirme | **Unity resmi plugin'i** (Unity 6+). Editor'ü Unity CLI ile kontrol eder, ayrı MCP gerekmez. Skill'leri: IAP, LevelPlay, UGS live game, localization, TMP, uGUI/UITK, sprite atlas, audio, 2D physics, package management, new project, craft-game-floors | Kurulu, Lemonade Rush'ta kullanıldı |
| 2. Stüdyo iş akışı | **Claude Code Game Studios** (ücretsiz, MIT; 49 agent, 74 skill, Unity seti var) | Kurulu değil. "minimal" modda başla, token tüketimini küçük bir prototipte ölç. Mağaza ve canlı operasyon kapsamı doğrulanmadı |
| 3. Mağaza | **app-publish-mcp** gibi topluluk araçları (App Store Connect + Google Play) | Kurulu değil. Yayın yetkili anahtar ister: kodu incele, en düşük yetkili anahtarla başla. Lemonade Rush'ta bunun yerine ASC API (JWT + `altool`) script'leri kullanıldı |
| 4. Yayın sonrası | **Firebase MCP** (Crashlytics) + Firebase Unity SDK, veya Unity Gaming Services | **Projenin başında** kur (onay akışıyla birlikte). Lemonade Rush'ta sonradan eklendi ve NoticeVersion artırmak gerekti |

**Diğer kurulu araçlar:** Figma (plugin + MCP), Miro, 231st UI Kit, Claude Docs, SpriteCook (claude.ai'de açık, connector bağlanmalı), PlayCanvas (web/Playables), security-audit, graphify, EGM (doküman inceleme agent'ları), skill-creator, deep-research, built-in browser (konsol işleri), iOS Simulator.

**Değerlendirilebilecekler:**
- **n360-app-release:** yayın öncesi test ve yayın sonrası doğrulama (oyuna özel değil).
- **Mobile Release Check:** target SDK, imzalama, izin metinleri, gömülü anahtar kontrolü (RN/Flutter/native odaklı).
- **AdCopilot / AdvisorPPC:** Google Ads + GA4 + YouTube, soft launch kampanyaları için. Hesapta değişiklik yapabilirler, onaylı modda kullan.
- **analytics-skills:** funnel ve A/B test okuma.
- **CellCog, image-generation:** asset üretimi (bkz. `asset-pipeline.md`).

Kural: üçüncü parti bir araca yetkili anahtar vermeden önce kodunu incele. En düşük yetkiyle başla. Anahtarlar `.env`'de durur.
