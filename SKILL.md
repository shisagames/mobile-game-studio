---
name: mobile-game-studio
description: End-to-end guide for building and shipping a Unity mobile game (iOS + Android) the way ShisaGames built Lemonade Rush - idea evaluation against hit games, GDD, economy and retention design, art and asset pipeline, monetization (IAP, LevelPlay ads), privacy, store listing and ASO, builds and uploads, store review, soft launch and live ops. Use when starting a new mobile game, evaluating a game idea, planning a production phase, designing an in-game economy, preparing a store release, or asking "what's next" in a mobile game project.
---

# Mobile Game Studio

Bu skill, *Lemonade Rush: Bottle Sort*'u fikirden mağazaya 22 günde götüren süreci yeni projelere taşır. Kullanıcı (ShisaGames, solo indie geliştirici) Unity ile iOS ve Android için oyun yapıyor. Fikirden yayın sonrasına kadar onu yönlendiren bir yardımcı istiyor.

## Önce bunları uygula
- **Çalışma kuralları:** `references/playbook.md` §1. Kısaca:
  - Türkçe konuş; kod ve commit İngilizce.
  - Önce açıkla, onay bekle. Tasarımda önce mockup göster.
  - Gereksiz kod değiştirme, sormadan dosya oluşturma.
  - Gizli bilgiler `.env`'de durur. Kalıcı silme yapma, şifre girme.
  - Commit, push, yükleme ve submit yalnızca açık talimatla.
  - Cloud Code kullanma. Projeleri harici diskte tut.
- Proje içindeki `CLAUDE.md` bu skill'den önce gelir.
- Lemonade Rush'a özel adlar (JuiceRush, Road Pass, karavan) **örnektir**. Yeni oyunda isimleri değiştir, iskeleti koru.

## Aşamaya göre ne okunur

| Kullanıcı ne istiyor / proje nerede | Oku | Yap |
|---|---|---|
| Yeni oyun fikri, "bu fikir tutar mı?" | `references/competitor-analysis.md` (Part F, H, I, J) | **Fikir değerlendirme** (aşağıda) |
| Proje kurulumu, CLAUDE.md, Unity CLI | `playbook.md` §2, Faz 0 | |
| Prototip, çekirdek mekanik, seviye üretimi | `playbook.md` Faz 1–2 | Önce saf C# kurallar + testler |
| Ekonomi, booster, retention, Pass, lig, açılma takvimi | `references/economy-template.md` | Lemonade Rush değerleriyle başla, oranları koru |
| Sanat yönü, sprite, ikon, animasyon, eklenti seçimi | `references/asset-pipeline.md` | Mockup turları → onay → pipeline |
| UI tutarlılığı, polish, yerelleştirme | `playbook.md` Faz 6–7 | |
| Sürüm hazırlığı, IAP, reklam, gizlilik, achievement | `playbook.md` Faz 8–11 | Önce simüle edilmiş mağaza ve reklam |
| Güvenlik, mağaza listesi, ASO, build, yükleme, review | `playbook.md` Faz 12–15, §4 checklist'leri | |
| Website, sosyal medya | `playbook.md` Faz 16 | |
| Yayın sonrası, soft launch, Firebase, live ops | `playbook.md` Faz 17, `competitor-analysis.md` live ops bölümleri | |
| Bir hata veya build sorunu | `playbook.md` §5 tuzaklar tablosu | Önce tabloya bak |
| Hangi araç veya eklenti? | `playbook.md` §8, `asset-pipeline.md` §4 | |
| "Sırada ne var?" | `playbook.md` §0 ve §7 | Mevcut fazı belirle, sonraki 3 adımı öner |

Reference dosyalarını sadece gerektiğinde ve gereken bölümüyle oku. `playbook.md` ve `competitor-analysis.md` uzun dosyalar, başlıklara göre (`grep -n "^#"`) git.

## Fikir değerlendirme (her yeni oyunun ilk adımı)
1. Kullanıcıdan fikri `competitor-analysis.md` **Part J şablonuyla** al. Eksik alanları sor, uydurma.
2. Hangi **strateji kalıbına** uyduğunu (Part F) ve en yakın hit oyunu (Magic Sort, Block Out, Gossip Harbor, Smash Fest) belirle.
3. **Part I checklist'iyle** puanla: 28 madde, her biri 0–2, toplam 56. 42 ve üstü güçlü, 30–41 umut verici ama iş istiyor, 30'un altında çekirdeği yeniden düşün. Her puanın gerekçesini tek cümleyle yaz.
4. Ödünç alınacak, uyarlanacak ve geliştirilecek özellikleri, ayrıca riskleri listele. Lemonade Rush'ın sistemlerinden hangilerinin doğrudan taşınabileceğini `economy-template.md` üzerinden belirt.
5. 3–5 reklam kreatifi konsepti öner (Part E): 3 saniyede görünen an, oynanabilir reklam fikri. Reklamlar gerçek oynanışa sadık kalmalı.
6. Hukuki ve IP kontrolü: kaynak oyunun adı, asset'leri ve markası kullanılmaz. Belirli bir oyunun kopyası yapılmaz, mekanik türü serbesttir.
7. Pazar verisi gerekiyorsa `deep-research` skill'ini kullan. Kaynakları kullanıcıya göster.

Çıktı: kısa bir değerlendirme raporu. Kullanıcı karar verince sıradaki adım GDD.

## GDD ve üretim planı (fikir onaylandıktan sonra)
Bir sayfalık GDD hazırla:
- çekirdek döngü
- takılma anı ve kurtarma
- booster'lar
- engel hattı (ilk 5 engel ve tanıtıldıkları seviyeler)
- meta (harita, koleksiyon, hikâye)
- retention sistemleri
- teklif merdiveni ve reklam zamanlaması
- hedef kitle ve pazarlar
- sanat yönü (referanslarla)

Özellik ID'leri ver (GP-, LV-, MO-, RE-, AD-…), kod yorumları bunlara bağlansın. Üretim planı için `playbook.md` §7'deki sırayı kullan. Apple ve Play hesaplarını, bundle ID'yi ve closed test sayacını (12 tester / 14 gün) **ilk günden** başlat.

## Her fazın sonunda
- Kullanıcıya kısa rapor ver: ne bitti, ne doğrulanmadı, sırada ne var.
- Yeni bir tuzak yaşandıysa projenin `CLAUDE.md` dosyasına ekle. Kalıcı ve genel bir dersse, kullanıcı onaylarsa bu skill'in `playbook.md` §5 tablosuna da ekle.
- Onaylanan sanat stili veya kullanıcı tercihi hafızaya yazılır.
