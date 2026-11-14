# Ekonomi ve mekanik şablonu (Lemonade Rush'ın gerçek değerleri)

> Kaynak: `Assets/JuiceRush/Scripts/Core/Meta/*.cs`, `Core/Puzzle/Difficulty.cs` ve `LevelGenerator.cs`, `Editor/CosmeticsBuilder.cs` (Kasım 2026, build 14).
> Yeni bir hyper/hybrid-casual bulmaca oyununda **başlangıç değerleri** olarak kullanılır. Rakamlar soft launch verisiyle (D1/D7 retention, seviye funnel'ı, ARPDAU) ayarlanmalı.
> Kural: bütün sayılar tek bir saf C# dosyasında (`GameRules.cs`) durur, UI kodunun içine dağılmaz. Her kural EditMode testiyle sabitlenir.

## 1. Çekirdek döngü

```
Seviye oyna → kazan (coin + 1–3 yıldız) → yıldızlar: Road Pass, League, Truck Race, harita ilerlemesi
           → takıl → booster (coin) / rescue (coin veya ödüllü video) / IAP teklifi
Her 10 seviye: şehre varış → damga (achievement + 100 coin) + sandık + reklam molası
```

## 2. Para birimleri ve kazanç

| Değer | Rakam | Not |
|---|---|---|
| Başlangıç coin'i | 500 | |
| Seviye galibiyeti | 40 coin | Ödüllü video ile ×2 (`double_level_coins`) |
| Replay galibiyeti | 10 coin | Yalnızca önceki rekorun üstündeki yıldızlar sayılır |
| Free Mode galibiyeti | 20 coin | |
| Şehir sandığı (her 10 seviyede) | 200 coin, %30 ihtimalle kozmetik | |
| Passport damgası | 100 coin, bir kez | Platform achievement'ı ile eşleşir (30 adet) |
| **Yıldız** | 3 = booster'sız, 2 = booster'lı, 1 = rescue ile | Seviye başına en fazla 3 |

**Akış kontrolü:** 10 seviyelik bir trip ≈ 400 coin + 200 sandık ≈ 600 coin eder. En ucuz kozmetik 200–300, booster paketi 250, rescue 900 coin. Oyuncu her trip'te bir şey alabilir, ama rescue'yu sık alamaz. IAP için doğal baskı noktası burası.

## 3. Booster'lar ve takılma anı (para kazanmanın kalbi)

| Booster | Fiyat | Açıldığı seviye | Not |
|---|---|---|---|
| Undo | 250 coin / 3 adet | 3 | Her seviyede 1 ücretsiz. Ödüllü video ile dolum (`undo_refill`) |
| +1 Şişe (rescue) | 900 coin | 8 | Seviye başına en fazla 2. Ödüllü video alternatifi (`rescue_bottle`) |
| İkinci rescue | 1.500 coin | | Aynı seviyede ikinci kurtarma |
| Shuffle | 450 coin | 20 | |
| Kolaylaştırma | 2 başarısızlıktan sonra | | Seviye yardımı (LV-09) |

İlke (handbook'tan): **tek güçlü takılma anı tasarla ve kurtarmayı sat.** Ödüllü video her zaman ücretsiz ve daha görünür seçenektir. Coin pahalı seçenektir.

## 4. Özellik açılma takvimi (FTUE)

| Seviye | Açılan |
|---|---|
| 1–3 | Sadece oyun. Reklam yok, gizlilik popup'ı yok (3. bölüm sonunda gelir) |
| 3 | Undo |
| 5 | Daily Gift. İlk özel şişe |
| 8 | +1 Şişe |
| 10 | Passport, Starter Pack teklifi |
| 11 | Gizli katmanlar |
| 18 | Events, Hot Streak |
| 20 | Shuffle |
| 25 | Interstitial'lar başlar |
| 35 | Weekly League |
| 45 | Truck Race |
| 60 | Sticker Album (Coming Soon) |

İlke: her birkaç seviyede **yeni bir şey açılsın**. İlk 20 seviyede menü kalabalık olmamalı.

## 5. Seviye ve zorluk

- **Renk sayısı:** sv 1–2: 2 renk, ≤5: 3, ≤12: 4, ≤30: 5, sonrası 6. Kolay seviyede 1 eksik, süper zor seviyede 1 fazla. Kapasite 4; 150. seviyeden sonra zor seviyelerde ⅓ ihtimalle 5.
- **Testere dişi eğri:** ilk 5 seviye kolay. Sonra kolay, kolay, orta, zor tekrar eder. Her 10. seviye süper zor, ardından gelen seviye kolay.
- **Zorluk skoru (0–100):** renk ×7, fazla şişe ×1,5, boş şişe −6, kapasite ×6, engel ×7, minimum hamle ×1,1, dallanma ×1,2. Etiket eşikleri: 34 orta, 54 zor, 72 süper zor.
- **Engeller:** her iki seviyede bir yenisi tanıtılır (5, 7, 9, 11, 13). Sonra her 10 seviyenin 6'sında bulunur. Gizli katman her 10 seviyenin 3'ünde. Her 10'luk blokta ana engel türü döner. 2'den fazla düz seviye üst üste gelmez.
- **Üretim:** seed `level * 7919`, ilk 3 seviye elle yapılır. Çözücü minimum hamleyi bulur, çözümsüz üretim reddedilir.
- Handbook ilkesi: **yaklaşık 2 haftada bir yeni engel** (canlı operasyon içerik hattı).

## 6. Retention sistemleri

### Hot Streak (ilk denemede üst üste galibiyet)
x1–x5. Görünür olması ve ödül vermesi **x3'ten başlar**: x3 ve x4'te +1 undo, x5'te +1 undo + 1 shuffle. Ödüller seviyeyle birlikte sona erer.

### Daily Gift (7 günlük takvim, gün kaçırınca başa döner)
| Gün | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
|---|---|---|---|---|---|---|---|
| Ödül | 50 coin | 1 şişe | 100 coin | 2 undo | 150 coin | Kozmetik (jant) | Mega sandık 500 |

Ödüllü video ile ×2 (`daily_gift_double`).

### Road Pass (30 günlük sezon, 30 tier, elle claim)
- Tier eşiği: `10·t + t(t−1)/2` yıldız. Sırasıyla 10, 21, 33, 46… 30. tier 735 yıldız. İlk ödüller çabuk gelir.
- **Ücretsiz hat:** 5'in katı tier'larda 300'lük hediye, 3'ün katlarında 2 undo, 4'ün katlarında 1 şişe, kalanlarda 100–300 coin.
- **Premium hat ($4.99):** 3, 8, 10, 13, 15, 17 ve 25. tier'larda kozmetik (tema, boya, jant, korna, tente, çatı). 5'in katlarında 1.000'lik hediye, çift tier'larda 2.000 coin, kalanlarda 3 şişe.
- Premium ödülü claim etmek pass gerektirir. Pass olmadan ödüller kilitli görünür (satış motivasyonu).
- Mağaza türü: iOS'ta **non-renewing subscription**, Android'de **prepaid plan (1 ay)**. Böylece ikisi de restore edilebilir.

### Weekly League (yıldızla puanlanır)
- Iron → Bronze → Silver → Gold → Platinum → Diamond. Gruplar 30 kişilik. Hafta sonunda ilk 5 yükselir, son 5 düşer. Diamond'da ilk 5 elite ödülünü alır.
- 7 gün üst üste offline olan roster'dan çıkar (tier'ı korunur), yeri aktif oyuncuyla doldurulur.
- Haftalık ödüller (bitirilen lige göre): Iron 100, Bronze 200, Silver 300 + 1 undo, Gold 500 + 1 shuffle, Platinum 800 + 1 şişe, Diamond 1.200 + 1 şişe + 1 shuffle. **Elite:** 2.500 + 2 şişe + 2 shuffle.
- Kurallar sunucuda çalıştırılabilir şekilde yazılır. Bugün rakipler simüle, oyuncunun adı ve avatarı gerçek.

### Truck Race (offline etkinlik)
25 sürücü (oyuncu + 24 seed'li rakip), 6 saat, kazanılan yıldıza göre sıralanır. Ödüller: 1. 1.000 coin + parça, 2. 600 + parça, 3. 300 + parça.

### Harita ve Free Mode
300 seviye, 30 bölge (6 şehir × 5 alt-biyom), her bölge 10 seviye. 300'den sonra Free Mode: 60–300 arasındaki seviyelerden, giderek zorlaşan sonsuz bir koşu. 3 can var, pes etmek veya baştan başlamak 1 can götürür. Koşuda kazanılan yıldızlar League, Pass ve Race'e sayılır. En iyi koşu leaderboard'a gider.

## 7. Kozmetikler (coin harcama yeri)

- Slotlar: Boya, Jant, Tente, Çatı, Korna, Ön yüz, Şişe ekranı teması. Karavanın ekibi ayrı.
- **Fiyat merdiveni:** 200 → 300 → 500 → 800 → 1.200 → 1.500–2.000 (Rare) → 2.200–3.200 (Epic). Tema 2.500.
- **Kaynak çeşitliliği:** Default, Coins, Level (25/30/40/50/60. seviyede açılır), DailyGift, CityChest, RoadPass (Epic), Passport (Golden).
- **Ekip karakterleri:** ilk 3 ücretsiz. 2'si Passport sayfası ödülü. Kalan 6'sı 1.000, 1.200, 1.500, 1.800, 2.200 ve 2.500 coin. Oyuncu karakteri + en fazla 2 arkadaş karavanda görünür.
- Tint'lenen parçalar gri tonlu çizilir. Sonradan yeniden çizimde **item ID'leri korunur**.

## 8. IAP kataloğu (ABD fiyatları, mağaza diğer ülkelere dönüştürür)

| Ürün | Tür | Fiyat | İçerik |
|---|---|---|---|
| coins_1000 | Consumable | $1.99 | 1.000 |
| coins_3000 | Consumable | $4.99 | 3.000 (+%20) |
| coins_6500 | Consumable | $9.99 | 6.500 (+%30) |
| coins_14000 | Consumable | $19.99 | 14.000 (+%40) |
| coins_40000 | Consumable | $49.99 | 40.000 (+%60) |
| starter_pack | Non-consumable (bir kez) | $1.99 | 1.000 coin + 3 undo + 3 şişe + 3 shuffle, 10. seviyeden itibaren |
| no_ads | Non-consumable | $4.99 | Interstitial'ları kaldırır + 1.000 coin. Ödüllü videolar kalır |
| rescue_pack | Consumable | $4.99 | 2.000 coin + 3 şişe |
| road_pass / road_pass_season | Bkz. Road Pass | $4.99 | Premium hat |

Teklif merdiveni (handbook): starter → paketler → no-ads → pass → etkinlik paketleri.

## 9. Reklam kuralları (`AdRules`)

- **Interstitial:** her etap sonunda (her 10. seviye ve Free Mode'da her 10 seviyede bir), 25. seviyeden sonra her 3 galibiyette bir, 10 seviyeden fazla reklamsız geçilmez.
- Başarısızlık ve replay sonrasında reklam yok. No Ads varken interstitial yok. İlk 3 seviyede ve gizlilik onayı verilmeden reklam yok.
- **Ödüllü video yerleri:** rescue şişesi, seviye coin'ini ×2 yapmak, daily gift ×2, undo dolumu. Hepsini oyuncu kendisi başlatır.
- Handbook: interstitial'ları ~20. seviyeye kadar geciktir, bir kısmını "No Ads" satmak için kullan.

## 10. Profil ve skor güvenliği

- İsim 3–16 karakter, 24 saatte bir değişebilir. Küfür filtresi (EN/TR + leetspeak) ve rezerve kelimeler var (admin, official…). Avatar olarak 8 meyve + ekip karakterleri kullanılır.
- Cloud Code yok: client skorları ilerlemeyle sınırlanır (seviye başına en fazla 3 yıldız, oynanan seviyeler ve Free Mode toplamı).
- Satın almalarda idempotency: `PurchaseLedger`, transaction ID yoksa da çift ödülü engeller.

## Yeni oyuna uyarlarken
1. Döngüyü yeni temaya çevir (yıldız → kupa, karavan → ev vb.). Sistemlerin iskeleti aynı kalır.
2. Rakamları değiştir, oranları koru: 1 seviye ≈ en ucuz booster'ın ⅙'sı; rescue ≈ 20 galibiyet.
3. Soft launch'ta ölç: hangi seviyede rescue satılıyor, D1 öncesi bırakma noktası nerede, Pass dönüşümü ne kadar.
