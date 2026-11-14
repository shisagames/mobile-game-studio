# Asset üretimi ve sanat yönü

> Kaynaklar: Lemonade Rush art pipeline'ı (`Tools/ArtSource`) ve kullanıcının claude.ai'deki "Oyun asset üretimi ve eklentiler" sohbetinin sonuçları.
> Hedef: daha profesyonel, animasyon kalitesi yüksek asset'ler.

## 1. Claude neyi iyi çizer, neyi çizemez

Claude görselleri **kodla** üretir: SVG, canvas, prosedürel geometri, Python ile katmanlanan gradyanlar.

| İyi | Zayıf |
|---|---|
| Low-poly, geometrik, flat ve vektör stiller | El boyaması illüstrasyon |
| Soft-3D cartoon (gradyan, ışık, yumuşak gölge; Lemonade Rush haritası ve ikonu) | Detaylı pixel art |
| UI elemanları, ikonlar, butonlar, 9-slice paneller | Karakter animasyonu (frame-by-frame, rig) |
| Tekrarlanabilir varyasyonlar (renk, desen, tint'lenen parçalar) | Fotogerçekçi doku |
| Mağaza görselleri, header'lar, ekran görüntüsü kompozisyonu, video kurgusu | Karmaşık 3D modelleme |

**Karar kuralı:** stil geometrik veya yumuşak-vektörse Claude'un pipeline'ı. Elle çizilmiş görünüm, pixel art veya animasyonlu karakter gerekiyorsa bir üretim eklentisi ya da hazır kütüphane.

## 2. Kodla çizim pipeline'ı (Lemonade Rush'ta çalışan)

```
Tools/ArtSource/
  defs.svg            ← temel sprite tanımları
  toon.py / clay.py / lowpoly.py / themes.py / icon_*.py   ← stil modülleri
  build_art.py        ← headless Chrome ile 3x render → sips ile kırp → Resources/Art/*.png + art_manifest.json
  build_icons.py      ← app ikonu (iOS, App Store 1024, Android adaptive: ön plan %62)
```
- `art_manifest.json`: 9-slice kenarları ve parça yerleşimi. Unity'de `ArtImporter` bunu uygular (PPU 300 → 1 tasarım px = 3 texture px).
- **PNG'leri elle düzenleme.** Kaynağı değiştir, yeniden render al: `python3 Tools/ArtSource/build_art.py --only a,b`.
- Garage'ın tint'lediği parçalar **gri tonlu** çizilir.
- Büyük gradyan arka planlar sıkıştırmasız import edilir (gerildiğinde bantlaşmasın).
- Kodla yazılan stil kuralları modül içinde sabitlenir. Böylece 30 bölge veya 70 kozmetik aynı dilde çıkar.

## 3. Mockup turu yöntemi (onay almadan oyuna koyma)

1. **Referans topla:** Unity Asset Store paketleri, Kenney, rakip mağaza ikonları/ekranları, Instagram hareket tasarımcıları, kullanıcının gönderdiği görseller.
2. **Tur:** 3–6 numaralı varyasyon (A/B/C → C1/C2…). Her biri büyük boyda, 60 px'te (ikon) ve gerçek bağlamında (ana ekran, oyun içi) gösterilir.
3. Kullanıcı seçer veya "X'in varyasyonları" der. Reddedilen yönü bir daha önerme.
4. Onaylanan stil **hafızaya** yazılır (renk, ışık yönü, çizgi var/yok, gölge, malzeme).
5. Mockup'lar git-ignored klasörde durur (`Builds/UIPolish/`). Onaydan sonra stil `Tools/ArtSource` modülüne taşınır ve oyuna uygulanır.
6. UI için: Figma denetimi → HTML mockup → kullanıcı Figma'ya yorum bırakır → yeni tur.

**Kullanıcının zevki (Lemonade Rush'tan):**
- Sevdiği: şirin, yumuşak, hyper-casual. Soft-3D: çizgisiz, ışık sol üstten, gölge sağ alttan, kalın kenar duvarları, 3 tonlu ağaç kümeleri, patikalar zeminden açık. İkonda yetişkinlere hitap eden şık bir ton (C2 "Spritz hour").
- Sevmediği: "Paint'te çizilmiş gibi" görünüm, kalın siyah outline, aşırı cıvıl cıvıl ve göz yoran arka plan, şirinliği kaybettiren sert low-poly, büyük ve hantal objeler (minimalist tercih eder).

## 4. Eklentiler ve kütüphaneler

| Araç | Ne üretir | Ne zaman | Not |
|---|---|---|---|
| **SpriteCook** (claude.ai plugin, hesapta açık) | 2D sprite, tileset, UI kit, kısa animasyon | Elle çizilmiş görünümlü 2D, animasyonlu sprite | Bu oturumda araçları görünmedi, kullanmadan önce connector'ü bağla. Skill'leri: generate-sprites, build-ui-kits, animate-assets, generate-tilesets, use-presets |
| **CellCog** | Görsel, 3D model, ses | 3D asset veya SFX/müzik gerektiğinde | Kurulu değil |
| **image-generation** (prompt rehberi) | Görsel üretim prompt'ları | Harici görsel üreticiyle çalışırken | Kurulu değil |
| **Figma** (plugin + MCP) | UI tasarım, design system, bileşenler | UI denetimi, mockup, stil sayfası | Kurulu |
| **231st UI Kit** (connector) | UI ilhamı, bileşen ve video | Ekran ve animasyon fikirleri | Kurulu |
| **Miro** (connector) | Board okuma ve yazma | Akış ve ekran haritası | Kurulu |
| **Polyfork** | 2.787 ücretsiz low-poly model, 26 kit (GLB + kod modülü) | 3D veya low-poly oyunlar | Eklenti gerekmiyor, Unity bağlayıcısı var. Ticari kullanım ve değiştirme serbest, **dosyaları yeniden dağıtmak yasak**. Oyuna koymadan önce lisans sayfasını oku. Karakterlerde rig ve animasyon olup olmadığı doğrulanmadı |
| **Kenney, Unity Asset Store** | Hazır paketler | Referans veya doğrudan kullanım | Lisansı kontrol et. Kullanıcının indirdiği paket `Assets/` altında olabilir |

## 5. Animasyon kalitesi için

- **UI ve "juice" animasyonları kodla:** tween, ölçek zıplaması, sparkle, kart açılışı, ödül uçuşu, sayfa swipe/snap, ekran geçişleri. Claude bunları iyi yapar; referans için hareket tasarımcılarının reels'leri incelenir.
- **Karakter animasyonu:** SpriteCook'un kısa animasyonları, rig'li hazır modeller (Polyfork rig'i doğrulanmalı) veya Unity 2D Animation (bone) ile parçalı sprite. Parçalı sprite için Claude parçaları ayrı ayrı çizebilir; rig'i Unity'de kullanıcı veya sonraki adım kurar.
- Ses: kodla sentez yapılabilir (Lemonade Rush'ta öyle), ama kalite için CellCog veya lisanslı paketler kullanılır. Ana ses seviyesine dokunma, yeni sesleri eskilerle aynı seviyede miksle.

## 6. Mağaza ve pazarlama görselleri (kodla)

`Tools/StoreListing/` altında: ekran görüntüleri (10 dil, iPhone/iPad/Duo), App Store header (4 eğik ekran), arama sonucu görseli (3840×2560), Play feature graphic, tablet görüntüleri, 24 sn tanıtım videosu (müzikli; Shorts, TikTok ve Instagram'da ortak), YouTube banner (2048×1152, ≤6 MB). Rakiplerin ilk 3 görseli incelenir: tahta tam ekran, havada dökülen büyük şişe, altta 2–3 kelimelik kalın şerit.
