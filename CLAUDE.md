# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Proje

Sakarya Üniversitesi dersleri için test ve alıştırma sayfaları. `main` dalı GitHub Pages ile doğrudan yayınlanır: https://kadir-y.github.io/ders-calisma/ (`.nojekyll` var, Jekyll işlemesi yok). Sayfaların dili Türkçe, commit mesajları da Türkçe ve `Ders adı: ne yapıldı` biçiminde.

Build, lint, test ya da paket yöneticisi yok. Her sayfa tek dosyalık HTML: CSS ve JS dosyanın içinde, dışarıdan yalnızca Google Fonts yükleniyor. Önizlemek için dosyayı tarayıcıda açmak ya da kökte `python -m http.server` çalıştırmak yeterli.

## Yapı

- Kökteki `index.html` ders listesi, her ders klasörünün kendi `index.html` dizini var. Yeni ders: klasör aç, kök `index.html` listesine satır ekle, `README.md` tablosunu güncelle. Soru sayıları hem kök dizinde hem README'de elle yazılı; soru eklenip çıkarılınca ikisi de güncellenmeli.
- `ag-programlama/` (BSM 451): `*-test.html` çoktan seçmeli testler, `bosluk-N-*.html` C kodu boşluk doldurma atölyeleri.
- `finansal-okuryazarlik/` (SAU 751): `oran-analizi-deneme.html` deneme sınavı.
- `ileri_mikro/` (İleri Mikroişlemciler, TM4C123): `kod-okuma-testi.html`, `adres-atolyesi.html`, `kod-atolyesi.html`. Kaynaklar aynı klasörde: derste yazdırılan kodlar `code.txt`–`code9.txt`, eski vizeler `ileri2026_paylasim/` (2021.pdf, ilerimikro2024.docx; gitignore'da). Bu ders için başka kaynak kullanılmıyor.

Sayfalar bir artifact şablonundan geliyor: `<title>` ve font linkleri `<body>` içinde duruyor. Renkler `:root` üzerinde token olarak tanımlı; koyu tema hem `@media (prefers-color-scheme: dark)` altında `:root:not([data-theme="light"])` ile hem de `:root[data-theme="dark"]` ile iki kez tanımlanıyor. Yeni renk eklerken üç bloğa da ekle.

## Sayfa motorları

**Ağ Programlama testleri** (`*-test.html`): Sorular `const Q = [...]` dizisinde, alanlar `{sec, p, s, q, o, quote, exp, k?}`. `o[0]` her zaman doğru cevap; şıklar `seeded()` ile soru sırasına göre sabit karıştırılıyor. `p`/`s` hocanın PDF sayfası ve slayt numarası, `quote` slayttan birebir alıntı, `k` "klasiğe de yarar" rozeti. Soru sayısı `grep -c '^{sec:' dosya.html` ile sayılabilir.

**Boşluk atölyeleri** (`bosluk-*.html`): Altı dosya birebir aynı; yalnızca `<title>`, `<h1>` ve `const MODULES=[...]` satırı farklı. Her dosyada altı modülün (ARP, ICMP, ping server/client, TCP sunucu/istemci) tanımı ve tüm kod metni bulunuyor. Ortak koddaki bir düzeltme altı dosyaya da aynen uygulanmalı; uyguladıktan sonra `diff bosluk-1-arp.html bosluk-2-icmp.html` yalnızca o üç satırı göstermeli.

**Oran analizi deneme sınavı**: Sorular `const Q` dizisinde, alanlar `{s, t, tb, q, ...}`; `a` doğru şıkkın karıştırılmadan önceki sırası, şıklar her açılışta rastgele karıştırılıyor. Tablo 1 ve el yazısı not ipuçları base64 görsel olarak dosyaya gömülü (dosya bu yüzden ~1 MB; tamamını okuma, `grep -n` ile ilgili satıra git). İpucu verisi soru dizisinden ayrı tutuluyor:
- `QH[i]`: i. sorunun el yazısı not anahtarları → `HL` (①–⑮ etiketleri) ve `HI` (kırpılmış not görselleri).
- `QS[i]`: i. sorunun slayt alıntısı anahtarları → `SL` (`d` sunum, `n` slayt no, `f` formül, `q` alıntı). `!` ile başlayan anahtarlar `SLN`'deki "slaytta geçmiyor" notlarına gider.

`QH` ve `QS` soru sırasına göre indeksli paralel diziler. Soru eklerken, silerken ya da sıralarken ikisi de aynı şekilde güncellenmeli, uzunlukları `Q` ile eşit kalmalı.

**İleri Mikro sayfaları**:
- `kod-okuma-testi.html`, Ağ Programlama test motorunun bir türevi. Alanlar `{sec, src, q, c?, o, quote, exp}`: `src` kaynak dosya ya da sınav sorusu, `c` soruya eklenen kod parçası. `q` ve `o` içinde `ters tırnak` kod olarak gösterilir.
- `adres-atolyesi.html` soruları `GEN.m1`–`GEN.m5` ile her seferinde üretir.
- `kod-atolyesi.html`'de görevler `TASKS` dizisinde; kodda `⟦n⟧` işaretleri `blanks[n]` ile eşleşir. Bir boşluk şu alanlarla kontrol edilir: `a` kabul edilen yazımlar (büyük/küçük harf duyarlı), `num` sayısal değer(ler), `chk` özel kontrol, `g` sırası önemsiz grup. Sayılar `evalNum` ile çözülür: `0x`, `0b`, onluk ve `GPIO_PIN_n|…` yazımlarının hepsi eşdeğer kabul edilir. Bir görevi değiştirdikten sonra her boşluğun `show` değeri `judgeTask` ile "ok" dönmeli.

## Kayıt

Cevaplar yalnızca `localStorage`'da tutuluyor, sunucu yok. Anahtarlar: testlerde `kN-test-v1`, boşluk atölyelerinde `bosluk-satir-<modül>-stats`, deneme sınavında `oran-deneme-v4`, İleri Mikro'da `mikro-okuma-v1`, `mikro-adres-v1`, `mikro-atolye-v1`. Kaydedilen veri soru sırasına bağlı; soru seti değişince eski cevapların yanlış sorulara eşlenmemesi için anahtardaki sürüm numarasını artır.

## İçerik kaynağı

Sorular hocanın slaytlarına ve öğrencinin el yazısı notlarına dayanıyor; alıntılar ve sayfa/slayt numaraları bu kaynaklardan birebir alınmış. Kaynakta olmayan bilgi uydurma: daha önce kurgusal bir tablo bölümü bu yüzden kaldırıldı. `.gitignore` ders materyallerini (pdf, pptx, docx, jpg) repoya almıyor ve bu kopyada da yoklar; kaynakla karşılaştırma gerekiyorsa kullanıcıdan istenmeli. Ancak deneme sınavına gömülü görseller sitede herkese açık yayınlanıyor.
