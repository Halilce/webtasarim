# HTML & CSS Stil Rehberi
### Temel Kavramlardan Tema Mirasına — Blade Entegrasyonuyla

---

## 1. CSS Temel Kavramlar

### 1.1 Cascade (Şelale) — Stil Öncelik Sırası

CSS'de birden fazla kural aynı elemente uygulandığında **öncelik sırası** şu şekildedir:

```
1. !important bildirimleri
2. Inline stiller   → style="color: red"
3. ID seçiciler     → #baslik { }
4. Class seçiciler  → .kart { }
5. Element seçiciler → h1, p, div { }
6. Kalıtım (inheritance)
7. Tarayıcı varsayılanları
```

**Özgüllük (Specificity) Hesabı:**

| Seçici          | (a, b, c) | Puan  |
|-----------------|-----------|-------|
| `#nav .link`    | (0, 1, 1) | 11    |
| `.btn.aktif`    | (0, 2, 0) | 20    |
| `h1`            | (0, 0, 1) | 1     |
| `style="..."`   | (1, 0, 0) | 1000  |

---

### 1.2 Box Model

Her HTML elementi bir kutu modelinden oluşur:

```
┌──────────────────────────────────┐
│            MARGIN                │
│  ┌────────────────────────────┐  │
│  │          BORDER            │  │
│  │  ┌──────────────────────┐  │  │
│  │  │       PADDING        │  │  │
│  │  │  ┌────────────────┐  │  │  │
│  │  │  │    CONTENT     │  │  │  │
│  │  │  └────────────────┘  │  │  │
│  │  └──────────────────────┘  │  │
│  └────────────────────────────┘  │
└──────────────────────────────────┘
```

```css
/* Varsayılan: content-box */
.kutu {
    width: 300px;       /* sadece içerik genişliği */
    padding: 20px;      /* içeriğe eklenir → toplam: 340px */
    border: 2px solid;  /* ona da eklenir  → toplam: 344px */
    margin: 10px;       /* dış boşluk, toplam genişliğe dahil değil */
}

/* Önerilen: border-box */
* {
    box-sizing: border-box;
    /* width: 300px → padding + border bu 300px içinde kalır */
}
```

---

### 1.3 Display Özellikleri

```css
/* Blok elementi — tam genişlik alır */
.blok    { display: block; }

/* Satır içi — sadece içerik kadar yer */
.satir   { display: inline; }

/* Her ikisi — width/height ayarlanabilir, akış bozulmaz */
.ikisi   { display: inline-block; }

/* Flexbox — tek eksen düzeni */
.flex    { display: flex; }

/* Grid — iki eksen düzeni */
.grid    { display: grid; }

/* Gizle — DOM'da var ama görünmez */
.gizli   { display: none; }
```

---

### 1.4 Position Türleri

```css
/* Varsayılan — normal akışta */
.statik    { position: static; }

/* Normal akışta KALIR ama top/left çalışır */
.goreceli  { position: relative; top: 10px; left: 5px; }

/* Nearest positioned ataya göre konumlanır */
.mutlak    { position: absolute; top: 0; right: 0; }

/* Viewport'a göre sabitlenir, scroll'da kayar */
.sabit     { position: fixed; bottom: 20px; right: 20px; }

/* Relative + Fixed karışımı */
.yapıskan  { position: sticky; top: 0; }
```

---

## 2. CSS Değişkenleri (Custom Properties)

CSS değişkenleri, yeniden kullanılabilir değerler tanımlamanızı ve **tema sistemi** kurmanızı sağlar.

### 2.1 Temel Kullanım

```css
/* Tanımlama: :root = global kapsam */
:root {
    --birincil-renk: #2563eb;
    --ikincil-renk: #7c3aed;
    --metin-rengi: #1e293b;
    --arka-plan: #f8fafc;
    --kenar-radius: 8px;
    --gecis-hizi: 0.3s ease;
}

/* Kullanım */
.buton {
    background-color: var(--birincil-renk);
    color: white;
    border-radius: var(--kenar-radius);
    transition: all var(--gecis-hizi);
}

/* Yedek değer ile kullanım */
.metin {
    color: var(--ozel-renk, #333);
    /* --ozel-renk tanımlı değilse #333 kullanılır */
}
```

---

### 2.2 Tema Değiştirme (Dark / Light Mode)

```css
/* Açık tema — varsayılan */
:root {
    --bg-ana:        #ffffff;
    --bg-kart:       #f1f5f9;
    --metin-ana:     #0f172a;
    --metin-ikincil: #64748b;
    --sinir:         #e2e8f0;
    --golge:         0 4px 6px rgba(0,0,0,0.07);
    --vurgu:         #2563eb;
}

/* Koyu tema */
[data-tema="koyu"] {
    --bg-ana:        #0f172a;
    --bg-kart:       #1e293b;
    --metin-ana:     #f1f5f9;
    --metin-ikincil: #94a3b8;
    --sinir:         #334155;
    --golge:         0 4px 6px rgba(0,0,0,0.4);
    --vurgu:         #60a5fa;
}

/* Sistem tercihini otomatik algıla */
@media (prefers-color-scheme: dark) {
    :root {
        --bg-ana:    #0f172a;
        --metin-ana: #f1f5f9;
    }
}

/* Bileşenler değişkeni kullanır, temayı bilmez */
body {
    background-color: var(--bg-ana);
    color: var(--metin-ana);
}

.kart {
    background: var(--bg-kart);
    border: 1px solid var(--sinir);
    box-shadow: var(--golge);
}
```

---

### 2.3 Kapsamlı Değişken Gruplaması

```css
:root {
    /* Renkler */
    --renk-birincil-50:  #eff6ff;
    --renk-birincil-100: #dbeafe;
    --renk-birincil-500: #3b82f6;
    --renk-birincil-600: #2563eb;
    --renk-birincil-900: #1e3a8a;

    /* Yazı Tipleri */
    --font-sans:  'Inter', system-ui, sans-serif;
    --font-mono:  'JetBrains Mono', monospace;
    --font-kucuk: 0.75rem;   /* 12px */
    --font-kucuk-: 0.875rem; /* 14px */
    --font-normal: 1rem;     /* 16px */
    --font-buyuk: 1.125rem;  /* 18px */
    --font-2xl:   1.5rem;    /* 24px */
    --font-3xl:   1.875rem;  /* 30px */

    /* Boşluklar */
    --bosluk-1: 0.25rem;  /* 4px  */
    --bosluk-2: 0.5rem;   /* 8px  */
    --bosluk-3: 0.75rem;  /* 12px */
    --bosluk-4: 1rem;     /* 16px */
    --bosluk-6: 1.5rem;   /* 24px */
    --bosluk-8: 2rem;     /* 32px */

    /* Gölgeler */
    --golge-sm: 0 1px 2px rgba(0,0,0,0.05);
    --golge-md: 0 4px 6px rgba(0,0,0,0.07);
    --golge-lg: 0 10px 15px rgba(0,0,0,0.1);

    /* Geçişler */
    --gecis-hizli: 0.15s ease;
    --gecis-normal: 0.3s ease;
    --gecis-yavas: 0.5s ease;
}
```

---

## 3. Kalıtım (Inheritance)

Bazı CSS özellikleri otomatik olarak çocuk elementlere **miras** geçer, bazıları geçmez.

### 3.1 Miras Geçen Özellikler

```css
/* Bu özellikler çocuklara otomatik yayılır */
body {
    color: #333;            /* ✅ miras geçer */
    font-family: sans-serif;/* ✅ miras geçer */
    font-size: 16px;        /* ✅ miras geçer */
    font-weight: normal;    /* ✅ miras geçer */
    line-height: 1.6;       /* ✅ miras geçer */
    text-align: left;       /* ✅ miras geçer */
    cursor: pointer;        /* ✅ miras geçer */
    visibility: visible;    /* ✅ miras geçer */

    /* Bunlar MIRAS GEÇMEZ */
    background-color: white; /* ❌ */
    border: 1px solid red;   /* ❌ */
    padding: 20px;           /* ❌ */
    margin: 10px;            /* ❌ */
    width: 100%;             /* ❌ */
    display: block;          /* ❌ */
}
```

### 3.2 Kalıtım Kontrol Anahtar Kelimeleri

```css
.eleman {
    /* Üst elementin değerini zorla miras al */
    color: inherit;

    /* Tarayıcı varsayılanına dön */
    color: initial;

    /* Tarayıcı varsayılanı yoksa inherit, varsa initial gibi davran */
    color: unset;

    /* Cascade'i sıfırla + kalıtımı uygula */
    color: revert;
}

/* Tüm özellikleri sıfırla */
.tamamen-sifirla {
    all: unset;
}
```

---

## 4. Flexbox Düzeni

```css
.konteyner {
    display: flex;

    /* Ana eksen yönü */
    flex-direction: row;           /* yatay (varsayılan) */
    flex-direction: column;        /* dikey */
    flex-direction: row-reverse;   /* ters yatay */

    /* Satır kaydırma */
    flex-wrap: nowrap;  /* kaydırma yok (varsayılan) */
    flex-wrap: wrap;    /* taşarsa alta geç */

    /* Ana eksende hizalama */
    justify-content: flex-start;    /* sola yasla */
    justify-content: center;        /* ortala */
    justify-content: flex-end;      /* sağa yasla */
    justify-content: space-between; /* araya eşit boşluk */
    justify-content: space-around;  /* çevresine eşit boşluk */
    justify-content: space-evenly;  /* tamamen eşit boşluk */

    /* Çapraz eksende hizalama */
    align-items: stretch;     /* uzat (varsayılan) */
    align-items: flex-start;  /* üste yasla */
    align-items: center;      /* ortala */
    align-items: flex-end;    /* alta yasla */

    gap: 1rem;         /* elemanlar arası boşluk */
    gap: 1rem 2rem;    /* dikey | yatay */
}

/* Flex çocuk özellikleri */
.cocuk {
    flex-grow: 1;    /* kalan alanı kapla */
    flex-shrink: 0;  /* küçülme */
    flex-basis: auto;/* başlangıç boyutu */
    flex: 1;         /* grow:1, shrink:1, basis:0 kısayolu */

    align-self: center; /* sadece bu elementin çapraz hizalaması */
    order: 2;           /* sıra değiştir */
}
```

---

## 5. CSS Grid Düzeni

```css
.grid-konteyner {
    display: grid;

    /* Sütun tanımı */
    grid-template-columns: 200px 1fr 1fr;
    grid-template-columns: repeat(3, 1fr);
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));

    /* Satır tanımı */
    grid-template-rows: auto 1fr auto;

    /* İsimli alanlar */
    grid-template-areas:
        "header  header  header"
        "sidebar icerik  icerik"
        "footer  footer  footer";

    gap: 1rem;
    column-gap: 2rem;
    row-gap: 0.5rem;
}

/* Alanlara yerleştirme */
.baslik  { grid-area: header; }
.kenar   { grid-area: sidebar; }
.icerik  { grid-area: icerik; }
.altbilgi{ grid-area: footer; }

/* Manuel yerleştirme */
.ozel {
    grid-column: 1 / 3;   /* 1. çizgiden 3. çizgiye */
    grid-row: 2 / span 2; /* 2. satırdan 2 satır kapla */
}
```

---

## 6. Medya Sorguları (Responsive Design)

```css
/* Mobile-first yaklaşım (önerilen) */

/* Temel stiller: mobil */
.kart {
    padding: var(--bosluk-4);
    font-size: var(--font-normal);
}

/* Tablet ve üzeri */
@media (min-width: 768px) {
    .kart {
        padding: var(--bosluk-6);
        font-size: var(--font-buyuk);
    }
}

/* Masaüstü ve üzeri */
@media (min-width: 1024px) {
    .kart {
        padding: var(--bosluk-8);
    }
}

/* Desktop-first yaklaşım */
@media (max-width: 767px) { /* mobil */ }
@media (max-width: 1023px) { /* tablet */ }

/* Karanlık mod */
@media (prefers-color-scheme: dark) { }

/* Yüksek çözünürlük */
@media (min-resolution: 2dppx) { }

/* Yazdırma */
@media print { }
```

---

## 7. Animasyonlar ve Geçişler

```css
/* Transition — hover/durum değişimi için */
.buton {
    background-color: var(--vurgu);
    transform: translateY(0);
    box-shadow: var(--golge-sm);
    transition:
        background-color var(--gecis-normal),
        transform        var(--gecis-hizli),
        box-shadow       var(--gecis-normal);
}

.buton:hover {
    background-color: var(--renk-birincil-600);
    transform: translateY(-2px);
    box-shadow: var(--golge-lg);
}

/* Keyframe animasyonu */
@keyframes kaydir-asagi {
    from {
        opacity: 0;
        transform: translateY(-20px);
    }
    to {
        opacity: 1;
        transform: translateY(0);
    }
}

@keyframes nabiz {
    0%, 100% { transform: scale(1); }
    50%       { transform: scale(1.05); }
}

/* Animasyon uygulama */
.bildirim {
    animation: kaydir-asagi 0.4s ease forwards;
}

.canli-badge {
    animation: nabiz 2s ease infinite;
}

/* Gecikmeli animasyonlar (stagger effect) */
.liste-oge:nth-child(1) { animation-delay: 0.0s; }
.liste-oge:nth-child(2) { animation-delay: 0.1s; }
.liste-oge:nth-child(3) { animation-delay: 0.2s; }
```

---

## 8. BLADE + CSS Stil Mirası Uygulaması

Laravel Blade şablon sistemi, CSS değişkenleri ve kalıtım kavramlarıyla mükemmel uyum içinde çalışır. Aşağıdaki örnekler tam bir tema miras zinciri oluşturur.

---

### 8.1 Ana Layout — Tema Değişkenleri Merkezi

```blade
{{-- resources/views/layouts/app.blade.php --}}
<!DOCTYPE html>
<html lang="tr" data-tema="{{ session('tema', 'acik') }}">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@yield('baslik', config('app.name'))</title>

    {{-- Global tema değişkenleri --}}
    <style>
        /* ============================================
           TEMA DEĞİŞKENLERİ — tüm çocuklar miras alır
           ============================================ */
        :root {
            /* Birincil Palette */
            --renk-birincil:     #2563eb;
            --renk-birincil-ag:  #1d4ed8;
            --renk-birincil-ac:  #60a5fa;
            --renk-ikincil:      #7c3aed;

            /* Yüzey Renkleri */
            --bg-sayfa:   #f8fafc;
            --bg-kart:    #ffffff;
            --bg-kenar:   #e2e8f0;

            /* Metin */
            --metin-1:    #0f172a;
            --metin-2:    #475569;
            --metin-3:    #94a3b8;
            --metin-ters: #ffffff;

            /* Geri Bildirim */
            --renk-basari:  #16a34a;
            --renk-hata:    #dc2626;
            --renk-uyari:   #d97706;
            --renk-bilgi:   #0284c7;

            /* Boyutlar */
            --radius-sm: 4px;
            --radius-md: 8px;
            --radius-lg: 12px;
            --radius-xl: 16px;
            --radius-tam: 9999px;

            /* Gölgeler */
            --golge-sm: 0 1px 3px rgba(0,0,0,0.08);
            --golge-md: 0 4px 12px rgba(0,0,0,0.1);
            --golge-lg: 0 8px 24px rgba(0,0,0,0.12);

            /* Yazı */
            --font-govde: 'Inter', system-ui, sans-serif;
            --font-kod:   'JetBrains Mono', monospace;
        }

        /* Koyu tema override'ı */
        [data-tema="koyu"] {
            --bg-sayfa:   #0f172a;
            --bg-kart:    #1e293b;
            --bg-kenar:   #334155;
            --metin-1:    #f1f5f9;
            --metin-2:    #94a3b8;
            --metin-3:    #64748b;
            --golge-sm:   0 1px 3px rgba(0,0,0,0.4);
            --golge-md:   0 4px 12px rgba(0,0,0,0.5);
        }

        /* Temel kalıtım zinciri başlatılır */
        body {
            font-family: var(--font-govde);
            background-color: var(--bg-sayfa);
            color: var(--metin-1);     /* ← tüm metin elementlerine miras geçer */
            line-height: 1.6;          /* ← tüm paragraflara miras geçer */
            margin: 0;
        }
    </style>

    {{-- Sayfa başına ek stil --}}
    @stack('stiller')
</head>
<body>
    @include('layouts.partials.navbar')

    <main class="icerik-alani">
        @yield('icerik')
    </main>

    @include('layouts.partials.footer')

    @stack('scriptler')
</body>
</html>
```

---

### 8.2 Bileşen Tabanlı Stil Mirası

```blade
{{-- resources/views/components/kart.blade.php --}}
@props([
    'baslik'   => null,
    'renk'     => 'varsayilan',   // varsayilan | birincil | basari | hata
    'dolgu'    => 'normal',       // dar | normal | genis
    'yukseklik' => false,
])

@php
$renkSinifi = match($renk) {
    'birincil' => 'kart--birincil',
    'basari'   => 'kart--basari',
    'hata'     => 'kart--hata',
    default    => '',
};

$dolguSinifi = match($dolgu) {
    'dar'   => 'kart--dar',
    'genis' => 'kart--genis',
    default => '',
};
@endphp

<div {{ $attributes->merge(['class' => "kart $renkSinifi $dolguSinifi " . ($yukseklik ? 'kart--yuksek' : '')]) }}>
    @if($baslik)
        <div class="kart__baslik">
            <h3>{{ $baslik }}</h3>
        </div>
    @endif

    <div class="kart__govde">
        {{ $slot }}
    </div>
</div>

<style>
/* Kart bileşeni — CSS değişkenlerinden miras alır */
.kart {
    background-color: var(--bg-kart);      /* temadan miras */
    border: 1px solid var(--bg-kenar);     /* temadan miras */
    border-radius: var(--radius-lg);       /* temadan miras */
    box-shadow: var(--golge-sm);           /* temadan miras */
    padding: 1.5rem;
    transition: box-shadow 0.2s ease, transform 0.2s ease;

    /* color ve font-family burada YOK — body'den miras geliyor */
}

.kart:hover {
    box-shadow: var(--golge-md);
    transform: translateY(-1px);
}

/* Renk varyantları — sadece ilgili değişkeni override eder */
.kart--birincil {
    border-color: var(--renk-birincil);
    background: color-mix(in srgb, var(--renk-birincil) 5%, var(--bg-kart));
}

.kart--basari {
    border-color: var(--renk-basari);
    background: color-mix(in srgb, var(--renk-basari) 5%, var(--bg-kart));
}

.kart--hata {
    border-color: var(--renk-hata);
    background: color-mix(in srgb, var(--renk-hata) 5%, var(--bg-kart));
}

/* Dolgu varyantları */
.kart--dar   { padding: 0.75rem 1rem; }
.kart--genis { padding: 2rem 2.5rem; }

/* Yüksek öncelikli gölge */
.kart--yuksek { box-shadow: var(--golge-lg); }

/* Başlık alanı */
.kart__baslik {
    margin-bottom: 1rem;
    padding-bottom: 0.75rem;
    border-bottom: 1px solid var(--bg-kenar);
}

.kart__baslik h3 {
    margin: 0;
    font-size: 1rem;
    font-weight: 600;
    color: var(--metin-1); /* inherit de yazılabilirdi, açık belirtmek daha güvenli */
}
</style>
```

---

### 8.3 Buton Bileşeni — Stil Zinciri

```blade
{{-- resources/views/components/buton.blade.php --}}
@props([
    'varyant' => 'birincil',   // birincil | ikincil | hayalet | tehlikeli
    'boyut'   => 'normal',     // kucuk | normal | buyuk
    'ikon'    => null,
    'yukleniyor' => false,
    'href'    => null,
])

@php
$etiket = $href ? 'a' : 'button';
$siniflar = "btn btn--{$varyant} btn--{$boyut}";
if ($yukleniyor) $siniflar .= ' btn--yukleniyor';
@endphp

<{{ $etiket }}
    {{ $href ? "href=$href" : '' }}
    {{ $attributes->merge(['class' => $siniflar]) }}
    {{ !$href ? 'type=button' : '' }}
>
    @if($yukleniyor)
        <span class="btn__yukleniyor-simge" aria-hidden="true"></span>
    @elseif($ikon)
        <span class="btn__ikon">{{ $ikon }}</span>
    @endif

    <span>{{ $slot }}</span>
</{{ $etiket }}>

<style>
/* ===== BUTON TEMEL STİL — değişken zinciri =====
   Renk değişkenleri :root'tan (veya [data-tema]'dan) geliyor.
   Buton bunları tüketir, kendi override'larını tanımlamaz.   */

.btn {
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    font-family: inherit;        /* body'den miras */
    font-size: var(--font-govde, 1rem);
    font-weight: 500;
    line-height: 1;
    border: 2px solid transparent;
    border-radius: var(--radius-md);
    cursor: pointer;
    text-decoration: none;
    transition:
        background-color 0.15s ease,
        border-color     0.15s ease,
        color            0.15s ease,
        box-shadow       0.15s ease,
        transform        0.1s ease;
    user-select: none;
}

.btn:hover  { transform: translateY(-1px); }
.btn:active { transform: translateY(0); }

.btn:focus-visible {
    outline: 3px solid var(--renk-birincil-ac);
    outline-offset: 2px;
}

/* ----- Varyantlar ----- */
.btn--birincil {
    background-color: var(--renk-birincil);
    color: var(--metin-ters);
    border-color: var(--renk-birincil);
}
.btn--birincil:hover {
    background-color: var(--renk-birincil-ag);
    border-color: var(--renk-birincil-ag);
    box-shadow: 0 4px 12px color-mix(in srgb, var(--renk-birincil) 40%, transparent);
}

.btn--ikincil {
    background-color: var(--bg-kart);
    color: var(--renk-birincil);
    border-color: var(--renk-birincil);
}
.btn--ikincil:hover {
    background-color: color-mix(in srgb, var(--renk-birincil) 8%, var(--bg-kart));
}

.btn--hayalet {
    background-color: transparent;
    color: var(--metin-2);
    border-color: var(--bg-kenar);
}
.btn--hayalet:hover {
    background-color: var(--bg-kenar);
    color: var(--metin-1);
}

.btn--tehlikeli {
    background-color: var(--renk-hata);
    color: white;
    border-color: var(--renk-hata);
}

/* ----- Boyutlar ----- */
.btn--kucuk  { padding: 0.375rem 0.75rem;  font-size: 0.8125rem; }
.btn--normal { padding: 0.625rem 1.25rem;  font-size: 0.9375rem; }
.btn--buyuk  { padding: 0.875rem 1.75rem;  font-size: 1.0625rem; }

/* ----- Yüklenme durumu ----- */
.btn--yukleniyor { pointer-events: none; opacity: 0.75; }

@keyframes dondur {
    to { transform: rotate(360deg); }
}

.btn__yukleniyor-simge {
    display: inline-block;
    width: 1em; height: 1em;
    border: 2px solid currentColor;
    border-top-color: transparent;
    border-radius: 50%;
    animation: dondur 0.7s linear infinite;
}
</style>
```

---

### 8.4 Gerçek Kullanım — Sayfa Görünümü

```blade
{{-- resources/views/makaleler/index.blade.php --}}
@extends('layouts.app')

@section('baslik', 'Makaleler — ' . config('app.name'))

@push('stiller')
{{-- Sadece bu sayfaya özel ek stiller --}}
<style>
    .makale-izgara {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
        gap: 1.5rem;
        padding: 2rem;
    }

    /* Sayfa düzeyi değişken override —
       bu sayfadaki tüm kartlar farklı radius kullanır */
    .makale-izgara {
        --radius-lg: 4px; /* keskin köşe tercihi */
    }
</style>
@endpush

@section('icerik')
<div class="makale-izgara">
    @forelse($makaleler as $makale)

        {{-- Bileşene veri geçilir, stil mantığı bileşende kalır --}}
        <x-kart
            :baslik="$makale->baslik"
            :renk="$makale->durum === 'reddedildi' ? 'hata' : 'varsayilan'"
        >
            <p style="color: var(--metin-2); font-size: 0.875rem;">
                {{ Str::limit($makale->ozet, 120) }}
            </p>

            <div style="
                display: flex;
                justify-content: space-between;
                align-items: center;
                margin-top: 1rem;
                padding-top: 0.75rem;
                border-top: 1px solid var(--bg-kenar);
            ">
                <span style="color: var(--metin-3); font-size: 0.8125rem;">
                    {{ $makale->created_at->diffForHumans() }}
                </span>

                <x-buton
                    varyant="ikincil"
                    boyut="kucuk"
                    :href="route('makaleler.goster', $makale)"
                >
                    İncele
                </x-buton>
            </div>
        </x-kart>

    @empty
        <x-kart renk="bilgi" class="col-span-full">
            <p style="text-align: center; color: var(--metin-2); margin: 0;">
                Henüz makale bulunmuyor.
            </p>
        </x-kart>
    @endforelse
</div>
@endsection
```

---

### 8.5 Tema Değiştirici — JavaScript + Blade

```blade
{{-- resources/views/components/tema-degistirici.blade.php --}}
<button
    id="tema-btn"
    class="btn btn--hayalet btn--kucuk"
    aria-label="Tema değiştir"
    title="Açık / Koyu tema"
>
    <span id="tema-simge">🌙</span>
</button>

<script>
(function () {
    const html   = document.documentElement;
    const btn    = document.getElementById('tema-btn');
    const simge  = document.getElementById('tema-simge');
    const ANAHTAR = 'tema-tercihi';

    function temaUygula(tema) {
        html.setAttribute('data-tema', tema);
        simge.textContent = tema === 'koyu' ? '☀️' : '🌙';
        localStorage.setItem(ANAHTAR, tema);

        // Sunucuya bildir (isteğe bağlı)
        fetch('/tema', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'X-CSRF-TOKEN': '{{ csrf_token() }}'
            },
            body: JSON.stringify({ tema })
        });
    }

    // Sayfa yüklenince kayıtlı temayı uygula
    const kaydedilen = localStorage.getItem(ANAHTAR)
        || (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'koyu' : 'acik');
    temaUygula(kaydedilen);

    // Değiştir
    btn.addEventListener('click', () => {
        const mevcutTema = html.getAttribute('data-tema');
        temaUygula(mevcutTema === 'koyu' ? 'acik' : 'koyu');
    });
})();
</script>
```

---

### 8.6 Form Bileşeni — Kapsamlı Stil Mirası

```blade
{{-- resources/views/components/forms/giris.blade.php --}}
@props([
    'etiket'    => null,
    'tur'       => 'text',
    'ad'        => '',
    'hata'      => null,
    'ipucu'     => null,
    'zorunlu'   => false,
])

@php
$girisId = $ad . '_' . uniqid();
@endphp

<div class="form-grup {{ $hata ? 'form-grup--hatali' : '' }}">

    @if($etiket)
        <label for="{{ $girisId }}" class="form-etiket">
            {{ $etiket }}
            @if($zorunlu)
                <span class="form-zorunlu" aria-hidden="true">*</span>
            @endif
        </label>
    @endif

    <input
        id="{{ $girisId }}"
        name="{{ $ad }}"
        type="{{ $tur }}"
        {{ $attributes->class(['form-giris', 'form-giris--hatali' => $hata]) }}
        @if($zorunlu) required @endif
        aria-describedby="{{ $hata ? $girisId.'_hata' : ($ipucu ? $girisId.'_ipucu' : '') }}"
    >

    @if($hata)
        <p id="{{ $girisId }}_hata" class="form-hata-mesaji" role="alert">
            {{ $hata }}
        </p>
    @elseif($ipucu)
        <p id="{{ $girisId }}_ipucu" class="form-ipucu">
            {{ $ipucu }}
        </p>
    @endif

</div>

<style>
.form-grup {
    display: flex;
    flex-direction: column;
    gap: 0.375rem;
    margin-bottom: 1.25rem;
}

.form-etiket {
    font-size: 0.875rem;
    font-weight: 500;
    color: var(--metin-1);  /* temadan geliyor */
}

.form-zorunlu {
    color: var(--renk-hata);
    margin-left: 2px;
}

.form-giris {
    width: 100%;
    padding: 0.625rem 0.875rem;
    font-family: inherit;           /* body'den miras */
    font-size: 0.9375rem;
    color: var(--metin-1);          /* temadan */
    background-color: var(--bg-kart); /* temadan */
    border: 1.5px solid var(--bg-kenar); /* temadan */
    border-radius: var(--radius-md); /* temadan */
    outline: none;
    transition: border-color 0.15s, box-shadow 0.15s;
    box-sizing: border-box;
}

.form-giris:focus {
    border-color: var(--renk-birincil);
    box-shadow: 0 0 0 3px color-mix(in srgb, var(--renk-birincil) 20%, transparent);
}

.form-giris--hatali,
.form-grup--hatali .form-giris {
    border-color: var(--renk-hata);
}

.form-giris--hatali:focus,
.form-grup--hatali .form-giris:focus {
    box-shadow: 0 0 0 3px color-mix(in srgb, var(--renk-hata) 20%, transparent);
}

.form-hata-mesaji {
    font-size: 0.8125rem;
    color: var(--renk-hata);
    margin: 0;
}

.form-ipucu {
    font-size: 0.8125rem;
    color: var(--metin-3);
    margin: 0;
}
</style>
```

---

## 9. Stil Mirası Özet Şeması

```
:root (CSS değişkenleri)
│
├─► body { color, font-family, line-height }
│       │
│       ├─► layouts/app.blade.php   ← tema switchini dinler
│       │       │
│       │       ├─► x-kart          ← var(--bg-kart), var(--golge-*)
│       │       │       │
│       │       │       └─► Slot içeriği    ← color inherit eder
│       │       │
│       │       ├─► x-buton         ← var(--renk-birincil), var(--radius-*)
│       │       │
│       │       └─► x-forms.giris   ← var(--bg-kenar), var(--renk-hata)
│       │
│       └─► [data-tema="koyu"]      ← değişkenleri override eder,
│                                      bileşenler OTOMATIK güncellenir
│
└─► @stack('stiller')               ← sayfa düzeyinde yerel override
```

---

## 10. Best Practices Özeti

| Kural | Açıklama |
|-------|----------|
| `box-sizing: border-box` herkese uygula | Boyut hesaplamalarını öngörülebilir kılar |
| CSS değişkenlerini `:root`'ta tanımla | Tek yerden tema yönetimi |
| Bileşenler `var()` kullanır, sabit renk yazmaz | Tema geçişleri otomatik çalışır |
| `inherit` yerine değişkeni açık yaz | Hata ayıklamayı kolaylaştırır |
| Mobile-first `min-width` kullan | CSS giderek karmaşıklaşmaz |
| Blade `@stack` ile sayfa stili izole et | Global kirliliği önler |
| `:root` override için `[data-tema]` seçicisi | JS sadece attribute değiştirir, CSS yapmaz |
| `transition` kısa tut (\< 0.3s) | Kullanıcı etkileşimini hızlı hissettirir |

---

# Laravel Eğitimi — Menü & İçerik Yönetimi Projesi
## Adım Adım Öğrenme Rehberi | Sıfırdan Tamamlanmış Proje

> Bu rehber, Laravel'i **hiç bilmeyenler** için tasarlanmıştır.
> Her komutun **ne işe yaradığı**, **neden kullanıldığı** ve **nasıl çalıştığı** adım adım açıklanmıştır.
> Proje sonunda: Menü yönetimi, sayfa yönetimi ve basit admin paneli tamamlanmış olacak.

---

## İÇİNDEKİLER

```
1. Laravel Nedir? Temel Kavramlar
2. Proje Kurulumu
3. Ortam (.env) Ayarları
4. Veritabanı Migrasyonları
5. Modeller
6. Controller'lar
7. Route (Yönlendirme)
8. Blade View (Şablonlar)
9. Seeder (Test Verisi)
10. Middleware
11. Tüm Komutların Açıklaması
```

---

## BÖLÜM 1 — LARAVEL NEDİR? TEMEL KAVRAMLAR

### Laravel Nedir?

Laravel, PHP programlama diliyle yazılmış, **MVC (Model-View-Controller)** mimarisini kullanan
açık kaynaklı bir web uygulama çerçevesidir (framework). Tekrar eden işleri otomatikleştirir,
güvenli ve düzenli kod yazmayı kolaylaştırır.

### MVC Mimarisi Nedir?

```
Kullanıcı isteği (URL)
        ↓
   ROUTE (Yönlendirici)
   "Bu URL'e kim bakacak?" diye sorar.
        ↓
   CONTROLLER (Kontrolcü)
   İş mantığını yürütür: "Ne yapılacak?"
        ↓
   MODEL (Model)
   Veritabanı ile konuşur: "Veriyi getir/kaydet."
        ↓
   VIEW (Görünüm)
   HTML sayfasını hazırlar: "Kullanıcıya ne gösterilecek?"
        ↓
   Kullanıcıya cevap döner
```

| Katman | Dosya Yeri | Görevi |
|--------|-----------|--------|
| Model | `app/Models/` | Veritabanı tabloları ile eşleşir |
| View | `resources/views/` | HTML şablonları (`.blade.php`) |
| Controller | `app/Http/Controllers/` | İş mantığı, model ve view'ı birleştirir |
| Route | `routes/web.php` | URL → Controller eşlemesi |
| Migration | `database/migrations/` | Veritabanı tablo şeması |

### Artisan Nedir?

Artisan, Laravel'in komut satırı aracıdır. `php artisan` ile çalıştırılır.
Dosya oluşturmak, veritabanını yönetmek, önbelleği temizlemek gibi onlarca işlemi
tek satır komutla yapar.

---

## BÖLÜM 2 — PROJE KURULUMU

### Adım 2.1 — Laravel Yükleyiciyi Kur

```bash
composer global require laravel/installer
```

**Ne yapar?**
`laravel` komutunu bilgisayarına genel olarak yükler. Bir kez yapılır, tekrar gerekmez.
Artık `laravel new proje-adi` komutu her yerden çalışır.

---

### Adım 2.2 — Yeni Proje Oluştur

**Yöntem 1 — Laravel Installer ile (önerilen):**
```bash
laravel new menu-icerik-yonetimi
```

**Yöntem 2 — Composer ile (installer yoksa):**
```bash
composer create-project --prefer-dist laravel/laravel menu-icerik-yonetimi
```

**Ne yapar?**
`menu-icerik-yonetimi` adında yeni bir klasör oluşturur.
İçine Laravel'in tüm dosyaları indirilir ve hazır hale getirilir.

**`--prefer-dist` ne demek?**
Composer'a "paketi kaynak kod olarak değil, derlenmş (dist) paket olarak indir" der.
Daha hızlı ve daha az yer kaplar.

---

### Adım 2.3 — Proje Klasörüne Gir

```bash
cd menu-icerik-yonetimi
```

**Ne yapar?**
Terminal/komut satırını yeni oluşturulan proje klasörüne taşır.
Bundan sonraki tüm komutlar bu klasör içinde çalıştırılır.

---

### Adım 2.4 — Uygulama Anahtarını Oluştur

```bash
php artisan key:generate
```

**Ne yapar?**
`.env` dosyasındaki `APP_KEY=` değerini rastgele, benzersiz bir şifreleme anahtarıyla doldurur.
Bu anahtar; oturum verilerini, şifreli çerezleri ve şifreleme işlemlerini güvenli kılar.

**Neden zorunlu?**
Anahtar olmadan Laravel çalışmaz. Güvenlik için her projede farklı bir anahtar olmalıdır.

**Çıktı örneği:**
```
Application key set successfully.
APP_KEY=base64:xK3mP8nQ2rT5vW7yZ9aB1cD4eF6gH8iJ0kL2mN4=
```

---

### Adım 2.5 — Geliştirme Sunucusunu Başlat

```bash
php artisan serve
php artisan serve --port=8000
```

**Ne yapar?**
Bilgisayarında geçici bir web sunucusu başlatır.
`http://localhost:8000` adresinden projeye erişilebilir.

**Neden kullanılır?**
Gerçek bir sunucu kurmadan (Apache/Nginx gibi) projeyi hemen test edebilirsin.

**Başka port kullanmak için:**
```bash
php artisan serve --port=8080
```

**Sunucuyu durdurmak için:**
Terminal'de `Ctrl + C` basılır.

---

### Adım 2.6 — Gerekli Paketleri Yükle

```bash
# Yetki yönetimi (rol ve izin sistemi)
composer require spatie/laravel-permission

# Excel / CSV export
composer require maatwebsite/excel
composer remove maatwebsite/excel
composer update maatwebsite/excel

# PDF oluşturma
composer require barryvdh/laravel-dompdf

# Aktivite kaydı (kim ne yaptı)
composer require spatie/laravel-activitylog
```

**`composer require` ne yapar?**
İnternetten belirtilen paketi indirir, projeye ekler ve `composer.json` dosyasını günceller.
Paket bir kez yüklenir, sonra otomatik olarak kullanılabilir hale gelir.

**Paketler nereye yüklenir?**
`vendor/` klasörüne. Bu klasör `.gitignore` içindedir, Git'e eklenmez.
Başka biri projeyi klonladığında `composer install` ile tüm paketleri tekrar indirebilir.

---

### Adım 2.7 — Composer Autoload'u Yenile

```bash
composer dump-autoload
```

**Ne yapar?**
Composer'ın "sınıf haritasını" (autoload map) yeniden oluşturur.
Yeni bir model, sınıf veya paket ekledikten sonra Laravel'in bunları bulabilmesi için çalıştırılır.

**Ne zaman çalıştırılır?**
- Yeni PHP sınıfı oluşturduktan sonra
- `composer.json` değiştirildikten sonra
- "Class not found" hatası alındığında

---

## BÖLÜM 3 — ORTAM AYARLARI (.env)

`.env` dosyası, projenin ortam değişkenlerini tutar. Sunucu, veritabanı,
e-posta gibi hassas bilgiler burada saklanır. `.gitignore` ile Git'e eklenmez,
her ortamda (lokal, sunucu) ayrı bir `.env` dosyası olur.

```env
# =============================================
# UYGULAMA AYARLARI
# =============================================

APP_NAME="Menü & İçerik Yönetimi"
# Uygulamanın adı. Sayfa başlıklarında ve bildirimlerde kullanılır.

APP_ENV=local
# Çalışma ortamı: local (geliştirme), staging (test), production (yayın).
# production'da hata detayları gösterilmez.

APP_KEY=
# Şifreleme anahtarı. php artisan key:generate ile doldurulur. Boş bırakılmaz!

APP_DEBUG=true
# true: Hataların detayı ekranda görünür (sadece local için).
# production'da mutlaka false yapılmalı.

APP_URL=http://localhost:8000
# Uygulamanın tam URL'i. storage:link ve mail gibi işlemlerde kullanılır.

APP_LOCALE=tr
# Varsayılan dil. Hata mesajları ve tarih formatları bu dile göre gösterilir.

APP_FALLBACK_LOCALE=en
# APP_LOCALE'de çeviri bulunamazsa bu dil kullanılır.

# =============================================
# VERİTABANI AYARLARI
# =============================================

DB_CONNECTION=mysql
# Veritabanı sürücüsü: mysql, sqlite, pgsql, sqlsrv

DB_HOST=127.0.0.1
# Veritabanı sunucu adresi. Lokal geliştirmede 127.0.0.1 veya localhost.

DB_PORT=3306
# MySQL varsayılan portu. PostgreSQL için 5432.

DB_DATABASE=menu_icerik_db
# Veritabanı adı. MySQL'de önceden oluşturulmuş olmalı.

DB_USERNAME=root
# Veritabanı kullanıcı adı.

DB_PASSWORD=
# Veritabanı şifresi. Boş olabilir (lokal geliştirmede).

# =============================================
# ÖNBELLEK & KUYRUK
# =============================================

CACHE_DRIVER=file
# Önbellek sürücüsü: file (dosyaya), redis, memcached, database.
# file: Basit projeler için yeterli, cache/ klasörüne yazar.

SESSION_DRIVER=file
# Oturum (session) sürücüsü: file, cookie, database, redis.

SESSION_LIFETIME=120
# Oturumun kaç dakika sonra sona ereceği.

QUEUE_CONNECTION=sync
# Kuyruk sürücüsü: sync (anlık çalışır), database, redis.
# sync: Kuyruk işlemleri hemen çalışır, arka plan işlemi olmaz.

# =============================================
# E-POSTA AYARLARI
# =============================================

MAIL_MAILER=smtp
# E-posta gönderme yöntemi: smtp, sendmail, mailgun, ses, log.
# log: E-posta göndermez, log dosyasına yazar (geliştirme için ideal).

MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=ornek@gmail.com
MAIL_PASSWORD=uygulama-sifresi
MAIL_ENCRYPTION=tls

MAIL_FROM_ADDRESS="ornek@gmail.com"
MAIL_FROM_NAME="${APP_NAME}"
# Gönderen adı ve e-posta adresi.
```

**Veritabanını oluştur (MySQL):**
```sql
CREATE DATABASE menu_icerik_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

---

## BÖLÜM 4 — VERİTABANI MİGRASYONLARI

### Migration Nedir?

Migration, veritabanı tablo yapısını PHP kodu ile tanımlayan dosyalardır.
SQL yazmak yerine PHP ile tablo oluşturulur, güncellenir veya silinir.
En büyük avantajı: Takım çalışmasında herkes `php artisan migrate` ile
aynı veritabanı yapısına sahip olur.

### Migration Dosyası Oluşturma Komutu

```bash
php artisan make:migration MigrationAdı
```

**Komut seçenekleri:**
```bash
php artisan make:migration create_menus_table
# "create_*_table" ifadesi otomatik olarak Schema::create() kodu oluşturur.

php artisan make:migration add_order_to_menus_table
# "add_*_to_*_table" ifadesi mevcut tabloya kolon ekler.

php artisan make:migration create_menus_table --table=menus
# --table: İlgili tabloyu belirtir.
```

---

### Adım 4.1 — Menü Tablosu

```bash
php artisan make:migration create_menus_table
```

```php
// database/migrations/xxxx_xx_xx_create_menus_table.php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * up() — Tabloyu OLUŞTURUR.
     * php artisan migrate çalıştırıldığında bu metod çalışır.
     */
    public function up(): void
    {
        Schema::create('menus', function (Blueprint $table) {
            $table->id();
            // id(): Otomatik artan birincil anahtar (BIGINT UNSIGNED AUTO_INCREMENT).
            // Her tabloda mutlaka olmalı.

            $table->string('name');
            // string(): VARCHAR(255). Kısa metin için kullanılır.
            // Menü adı: "Ana Menü", "Footer Menü" gibi.

            $table->string('slug')->unique();
            // unique(): Bu kolondaki değerler tekrar edemez.
            // slug: URL-dostu isim. "ana-menu", "footer-menu" gibi.

            $table->string('location')->default('header');
            // default(): Değer girilmezse bu değer kullanılır.
            // Menünün sitede nerede görüneceği: "header", "footer", "sidebar".

            $table->boolean('is_active')->default(true);
            // boolean(): true/false değer tutar (TINYINT(1)).
            // Menü aktif mi değil mi?

            $table->integer('order')->default(0);
            // integer(): Tam sayı (INT).
            // Menülerin sıralanma değeri.

            $table->timestamps();
            // timestamps(): created_at ve updated_at kolonlarını ekler.
            // Laravel bunları otomatik doldurur.
        });
    }

    /**
     * down() — Tabloyu SİLER.
     * php artisan migrate:rollback çalıştırıldığında bu metod çalışır.
     * up() metodunun tam tersi yapılır.
     */
    public function down(): void
    {
        Schema::dropIfExists('menus');
        // dropIfExists(): Tablo varsa sil, yoksa hata verme.
    }
};
```

---

### Adım 4.2 — Menü Öğeleri Tablosu

```bash
php artisan make:migration create_menu_items_table
```

```php
<?php
// database/migrations/xxxx_xx_xx_create_menu_items_table.php

public function up(): void
{
    Schema::create('menu_items', function (Blueprint $table) {
        $table->id();

        $table->foreignId('menu_id')->constrained()->cascadeOnDelete();
        // foreignId(): Yabancı anahtar (BIGINT UNSIGNED).
        // constrained(): menus tablosundaki id ile ilişkilendirir.
        // cascadeOnDelete(): Menü silinince bu menüye ait tüm öğeler de silinir.

        $table->foreignId('parent_id')->nullable()->constrained('menu_items')->nullOnDelete();
        // nullable(): Bu kolon boş olabilir.
        // Üst menü öğesi. null ise ana öğedir, değer varsa alt öğedir.
        // nullOnDelete(): Üst öğe silinirse bu alanı null yap (öğeyi silme).

        $table->string('title');
        // Menüde görünen yazı: "Anasayfa", "Hakkımızda".

        $table->string('url')->nullable();
        // Dış URL için: "https://example.com". İç sayfa ise null olabilir.

        $table->foreignId('page_id')->nullable()->constrained('pages')->nullOnDelete();
        // İç sayfaya bağlıysa hangi sayfa? Dış URL varsa null.

        $table->string('target')->default('_self');
        // Linkin nasıl açılacağı:
        // _self: Aynı sekmede aç (varsayılan).
        // _blank: Yeni sekmede aç.

        $table->string('icon')->nullable();
        // Opsiyonel ikon: "fa-home", "heroicon-home" gibi CSS sınıfı.

        $table->integer('order')->default(0);
        // Sıralama değeri.

        $table->boolean('is_active')->default(true);
        // Bu öğe gösterilsin mi?

        $table->timestamps();
    });
}

public function down(): void
{
    Schema::dropIfExists('menu_items');
}
```

---

### Adım 4.3 — Sayfalar Tablosu

```bash
php artisan make:migration create_pages_table
```

```php
// database/migrations/xxxx_xx_xx_create_pages_table.php

public function up(): void
{
    Schema::create('pages', function (Blueprint $table) {
        $table->id();

        $table->string('title');
        // Sayfa başlığı: "Hakkımızda", "İletişim".

        $table->string('slug')->unique();
        // URL için: "hakkimizda", "iletisim".
        // unique(): Aynı slug iki sayfada olamaz.

        $table->longText('content')->nullable();
        // longText(): Uzun HTML/Markdown içerik için (TEXT yerine LONGTEXT kullanılır).
        // Sayfanın ana içeriği.

        $table->string('meta_title')->nullable();
        // SEO için sayfa başlığı (tarayıcı sekmesinde görünür).

        $table->text('meta_description')->nullable();
        // text(): Orta uzunlukta metin (TEXT). SEO özeti, 150-160 karakter.

        $table->string('featured_image')->nullable();
        // Öne çıkan görsel dosya yolu: "images/hakkimizda.jpg".

        $table->enum('status', ['published', 'draft', 'archived'])->default('draft');
        // enum(): Sadece belirtilen değerleri kabul eder.
        // published: Yayında, draft: Taslak, archived: Arşiv.

        $table->boolean('show_in_menu')->default(false);
        // Bu sayfa menüde görünsün mü?

        $table->foreignId('author_id')->constrained('users');
        // Sayfayı kim oluşturdu? users tablosuna bağlı.

        $table->integer('order')->default(0);
        // Sıralama.

        $table->timestamp('published_at')->nullable();
        // timestamp(): Tarih + saat bilgisi. Ne zaman yayınlandı?

        $table->timestamps();
        // created_at ve updated_at.

        $table->softDeletes();
        // softDeletes(): deleted_at kolonu ekler.
        // Kayıt silinmez, sadece deleted_at doldurulur (geri alınabilir silme).
    });
}

public function down(): void
{
    Schema::dropIfExists('pages');
}
```

---

### Adım 4.4 — Migrasyon Çalıştırma Komutları

```bash
# Tüm bekleyen migrasyonları çalıştır
php artisan migrate
```
**Ne yapar?**
`database/migrations/` klasöründeki henüz çalışmamış migration dosyalarını sırayla çalıştırır.
Her başarılı migrasyon `migrations` tablosuna kaydedilir.

```bash
# Son migration grubunu geri al
php artisan migrate:rollback
```
**Ne yapar?**
En son çalıştırılan migration grubundaki `down()` metodlarını çalıştırır.
Yanlış bir tablo oluşturduysan ve düzeltmek istiyorsan kullanılır.

```bash
# Tüm migrasyonları geri al ve baştan çalıştır
php artisan migrate:refresh
```
**Ne yapar?**
Tüm `down()` metodlarını sırayla çalıştırır (tüm tabloları siler),
sonra tüm `up()` metodlarını çalıştırır (tabloları yeniden oluşturur).
Geliştirme aşamasında şema değişince kullanılır. **Tüm veri silinir!**

```bash
# Tabloları komple sil ve yeniden oluştur (daha hızlı)
php artisan migrate:fresh
```
**Ne yapar?**
`migrate:refresh`'ten farkı: `down()` metodlarını çalıştırmaz, tabloları direkt `DROP TABLE` ile siler.
Daha hızlıdır. **Tüm veri silinir!**

```bash
# Fresh + seeder çalıştır (en çok kullanılan geliştirme komutu)
php artisan migrate:fresh --seed
```
**Ne yapar?**
Tabloları siler, yeniden oluşturur ve `DatabaseSeeder`'ı çalıştırır.
Geliştirme sırasında temiz başlangıç yapmak için en pratik komuttur.

```bash
# Tüm migrasyonları geri al (tabloları sil, yeniden oluşturma)
php artisan migrate:reset
```
**Ne yapar?**
Tüm `down()` metodlarını çalıştırır. Tüm tablolar silinir ama yeniden oluşturulmaz.

---

## BÖLÜM 5 — MODELLER

### Model Nedir?

Model, bir veritabanı tablosunu PHP sınıfı olarak temsil eder.
Tablo ile konuşmak için SQL yazmak yerine PHP metotları kullanılır.
Laravel'in ORM (Object-Relational Mapper) aracı olan **Eloquent** bunu sağlar.

### Model Oluşturma Komutu

```bash
php artisan make:model ModelAdı
```

**Seçenekler:**
```bash
php artisan make:model Menu
# Sadece model oluşturur: app/Models/Menu.php

php artisan make:model Menu -m
# -m (--migration): Model + migration dosyası oluşturur.

php artisan make:model Menu -mc
# -m: migration, -c: controller oluşturur.

php artisan make:model Menu -mcr
# -m: migration, -c: controller, -r: resource controller (CRUD hazır metotlar).

php artisan make:model Menu --all
# Migration + factory + seeder + controller + policy + resource hepsini oluşturur.
```

---

### Adım 5.1 — Menu Modeli

```bash
php artisan make:model Menu
```

```php
// app/Models/Menu.php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;

class Menu extends Model
{
    use HasFactory;
    // HasFactory: Bu model için factory kullanılabilir (test verisi üretmek için).

    /**
     * $fillable — Toplu atama (mass assignment) için izin verilen kolonlar.
     * Güvenlik için: Sadece buradaki kolonlar create() ve update() ile doldurulabilir.
     * Buraya eklenmemiş bir kolon form üzerinden doldurulamaz.
     */
    protected $fillable = [
        'name',
        'slug',
        'location',
        'is_active',
        'order',
    ];

    /**
     * $casts — Kolon değerlerini otomatik dönüştürür.
     * Veritabanından gelen değeri PHP'de doğru tipte kullanmak için.
     */
    protected $casts = [
        'is_active' => 'boolean',
        // Veritabanındaki 1/0 değerini PHP'de true/false olarak kullanırız.
    ];

    /**
     * İLİŞKİLER (Relationships)
     * Eloquent ile tablolar arası ilişkileri PHP metoduyla tanımlarız.
     */

    // Bir menünün birden fazla öğesi olabilir (hasMany = "bir'e çok")
    public function items()
    {
        return $this->hasMany(MenuItem::class)
                    ->whereNull('parent_id')  // Sadece ana öğeler (üst menü)
                    ->orderBy('order');        // Sıralı getir
    }

    // Tüm öğeleri getir (alt öğeler dahil)
    public function allItems()
    {
        return $this->hasMany(MenuItem::class)->orderBy('order');
    }

    /**
     * SCOPE — Sık kullanılan sorgu filtrelerini kısayol olarak tanımlar.
     * $query->active() yerine Model::active() yazılabilir.
     */
    public function scopeActive($query)
    {
        return $query->where('is_active', true);
    }

    // Belirli bir konumdaki menüleri getir: Menu::atLocation('header')
    public function scopeAtLocation($query, string $location)
    {
        return $query->where('location', $location);
    }
}
```

---

### Adım 5.2 — MenuItem Modeli

```bash
php artisan make:model MenuItem
```

```php
// app/Models/MenuItem.php

class MenuItem extends Model
{
    use HasFactory;

    protected $fillable = [
        'menu_id', 'parent_id', 'title', 'url',
        'page_id', 'target', 'icon', 'order', 'is_active',
    ];

    protected $casts = [
        'is_active' => 'boolean',
    ];

    // Bu öğe hangi menüye ait? (belongsTo = "ait olma")
    public function menu()
    {
        return $this->belongsTo(Menu::class);
    }

    // Alt menü öğeleri (hasMany — özyinelemeli ilişki)
    public function children()
    {
        return $this->hasMany(MenuItem::class, 'parent_id')->orderBy('order');
    }

    // Üst menü öğesi (belongsTo — özyinelemeli ilişki)
    public function parent()
    {
        return $this->belongsTo(MenuItem::class, 'parent_id');
    }

    // Bu öğe hangi sayfaya bağlı?
    public function page()
    {
        return $this->belongsTo(Page::class);
    }

    // Öğenin gerçek URL'ini döndür
    public function getFullUrlAttribute(): string
    {
        // Sayfaya bağlıysa o sayfanın URL'ini döndür
        if ($this->page_id && $this->page) {
            return route('page.show', $this->page->slug);
        }
        // Dış URL varsa onu döndür
        return $this->url ?? '#';
    }

    public function scopeActive($query)
    {
        return $query->where('is_active', true);
    }
}
```

---

### Adım 5.3 — Page Modeli

```bash
php artisan make:model Page
```

```php
// app/Models/Page.php

use Illuminate\Database\Eloquent\SoftDeletes;

class Page extends Model
{
    use HasFactory, SoftDeletes;
    // SoftDeletes: Kayıt silindiğinde deleted_at doldurulur.
    // Model::all() sorgularına silinmiş kayıtlar otomatik dahil edilmez.

    protected $fillable = [
        'title', 'slug', 'content', 'meta_title', 'meta_description',
        'featured_image', 'status', 'show_in_menu', 'author_id', 'order', 'published_at',
    ];

    protected $casts = [
        'show_in_menu' => 'boolean',
        'published_at' => 'datetime',
        // datetime: Veritabanındaki tarih string'ini Carbon nesnesine dönüştürür.
        // Carbon ile: $page->published_at->format('d.m.Y') kullanılabilir.
    ];

    // Sayfayı kim yazdı?
    public function author()
    {
        return $this->belongsTo(User::class, 'author_id');
    }

    // Yayınlanmış sayfaları filtrele
    public function scopePublished($query)
    {
        return $query->where('status', 'published')
                     ->whereNotNull('published_at')
                     ->where('published_at', '<=', now());
        // now(): Şu anki tarih-saatten önce yayınlanmış olanları getir.
    }

    // Silinmiş sayfaları dahil et (SoftDeletes ile silinenleri görmek için)
    // Page::withTrashed()->get();

    // Sadece silinmiş sayfalar
    // Page::onlyTrashed()->get();

    // Silmeyi geri al
    // $page->restore();

    // Kalıcı olarak sil
    // $page->forceDelete();
}
```

---

## BÖLÜM 6 — CONTROLLER'LAR

### Controller Nedir?

Controller, kullanıcının isteğini alır, gerekli modelleri çağırır
ve sonucu view'e gönderir. İş mantığının toplandığı yerdir.

### Controller Oluşturma Komutu

```bash
php artisan make:controller ControllerAdı
```

**Seçenekler:**
```bash
php artisan make:controller MenuController
# Boş controller oluşturur.

php artisan make:controller MenuController --resource
# --resource: index, create, store, show, edit, update, destroy
# adlı 7 metodu hazır olarak oluşturur. CRUD için standarttır.

php artisan make:controller MenuController --api
# --api: API için 5 metot oluşturur (create ve edit hariç).

php artisan make:controller Admin/MenuController --resource
# Admin/ klasörü altına oluşturur (yönetim paneli için).
```

**Resource Controller Metodları:**

| Metot | HTTP | URL | İşlev |
|-------|------|-----|-------|
| `index()` | GET | `/menus` | Liste göster |
| `create()` | GET | `/menus/create` | Oluşturma formu göster |
| `store()` | POST | `/menus` | Form verisini kaydet |
| `show($id)` | GET | `/menus/{id}` | Tek kayıt göster |
| `edit($id)` | GET | `/menus/{id}/edit` | Düzenleme formu göster |
| `update($id)` | PUT/PATCH | `/menus/{id}` | Güncellenmiş veriyi kaydet |
| `destroy($id)` | DELETE | `/menus/{id}` | Sil |

---

### Adım 6.1 — Sayfa Controller (Ziyaretçi tarafı)

```bash
php artisan make:controller PageController
```

```php
// app/Http/Controllers/PageController.php

namespace App\Http\Controllers;

use App\Models\Page;
use App\Models\Menu;
use Illuminate\Http\Request;

class PageController extends Controller
{
    /**
     * Anasayfayı göster
     */
    public function home()
    {
        // Yayınlanmış ve sıralı sayfaları getir
        $pages = Page::published()->orderBy('order')->get();

        // Header menüsünü getir (ilişkili öğeleriyle birlikte)
        // with(): Eager loading — N+1 sorgu sorununu önler.
        // N+1: Her menü öğesi için ayrı SQL çekmek yerine tek sorguda hepsini getirir.
        $headerMenu = Menu::active()
                          ->atLocation('header')
                          ->with(['items' => function ($query) {
                              $query->active()->with('children', 'page');
                              // children: Alt menü öğelerini de yükle
                              // page: Bağlı sayfayı da yükle
                          }])
                          ->first();

        // view('dosya-yolu', ['değişken' => değer])
        // İkinci parametre: View'da kullanılacak değişkenler.
        return view('home', compact('pages', 'headerMenu'));
        // compact(): ['pages' => $pages, 'headerMenu' => $headerMenu] kısayolu
    }

    /**
     * Slug ile sayfa göster (/sayfa/hakkimizda)
     */
    public function show(string $slug)
    {
        // firstOrFail(): Bulunamazsa 404 sayfası göster.
        // findOrFail($id) ile aynı mantıkta.
        $page = Page::published()->where('slug', $slug)->firstOrFail();

        $headerMenu = Menu::active()
                          ->atLocation('header')
                          ->with('items.children')
                          ->first();

        return view('page', compact('page', 'headerMenu'));
    }
}
```

---

### Adım 6.2 — Admin Menü Controller

```bash
php artisan make:controller Admin/MenuController --resource
```

```php
// app/Http/Controllers/Admin/MenuController.php

namespace App\Http\Controllers\Admin;

use App\Http\Controllers\Controller;
use App\Models\Menu;
use Illuminate\Http\Request;
use Illuminate\Support\Str;

class MenuController extends Controller
{
    // Constructor: Her controller metodundan önce çalışır.
    // Burada yetki kontrolü veya middleware eklenebilir.
    public function __construct()
    {
        $this->middleware('auth');
        // auth middleware: Giriş yapmamış kullanıcıyı login sayfasına yönlendirir.

        $this->middleware('can:manage-menus');
        // can: Spatie Permission ile tanımlı izni kontrol eder.
    }

    /**
     * Tüm menüleri listele
     * GET /admin/menus
     */
    public function index()
    {
        // paginate(): Sonuçları sayfalara böler. Parametre: sayfa başına kayıt sayısı.
        // Otomatik olarak ?page=2 gibi sorgu parametresini tanır.
        $menus = Menu::orderBy('order')->paginate(15);

        return view('admin.menus.index', compact('menus'));
    }

    /**
     * Yeni menü oluşturma formunu göster
     * GET /admin/menus/create
     */
    public function create()
    {
        return view('admin.menus.create');
    }

    /**
     * Formu kaydet
     * POST /admin/menus
     */
    public function store(Request $request)
    {
        // validate(): Gelen veriyi doğrular.
        // Kural ihlali olursa otomatik olarak forma geri döner ve hataları gösterir.
        $validated = $request->validate([
            'name'     => 'required|string|max:100',
            // required: Boş olamaz
            // string: String olmalı
            // max:100: En fazla 100 karakter

            'location' => 'required|in:header,footer,sidebar',
            // in: Sadece bu değerlerden biri olabilir

            'is_active'=> 'boolean',
            // boolean: true/false/1/0/"1"/"0" kabul eder

            'order'    => 'integer|min:0',
            // integer: Tam sayı olmalı
            // min:0: Sıfırdan küçük olamaz
        ]);

        // Slug otomatik oluştur
        $validated['slug'] = Str::slug($validated['name']);
        // Str::slug("Ana Menü") => "ana-menu"

        Menu::create($validated);
        // create(): $fillable kolonlarına veri yazar ve kaydeder.

        // redirect()->route(): İsimlendirilmiş rotaya yönlendir.
        // ->with('success', '...'): Yönlendirmeye flash mesaj ekle.
        // Flash mesaj: Sadece bir sonraki istekte görünür.
        return redirect()->route('admin.menus.index')
                         ->with('success', 'Menü başarıyla oluşturuldu!');
    }

    /**
     * Düzenleme formunu göster
     * GET /admin/menus/{menu}/edit
     */
    public function edit(Menu $menu)
    {
        // Route Model Binding: Laravel, URL'deki {menu} değeriyle
        // otomatik olarak Menu modelini bulup $menu'ya atar.
        // Menu::findOrFail($id) yazmak gerekmez.

        return view('admin.menus.edit', compact('menu'));
    }

    /**
     * Güncellemeyi kaydet
     * PUT /admin/menus/{menu}
     */
    public function update(Request $request, Menu $menu)
    {
        $validated = $request->validate([
            'name'      => 'required|string|max:100',
            'location'  => 'required|in:header,footer,sidebar',
            'is_active' => 'boolean',
            'order'     => 'integer|min:0',
        ]);

        $menu->update($validated);
        // update(): Modeli günceller. Sadece $fillable kolonları güncellenir.

        return redirect()->route('admin.menus.index')
                         ->with('success', 'Menü güncellendi!');
    }

    /**
     * Menüyü sil
     * DELETE /admin/menus/{menu}
     */
    public function destroy(Menu $menu)
    {
        $menu->delete();
        // Modelde SoftDeletes yoksa kalıcı siler.
        // SoftDeletes varsa deleted_at doldurulur.

        return redirect()->route('admin.menus.index')
                         ->with('success', 'Menü silindi!');
    }
}
```

---

### Adım 6.3 — Admin Sayfa Controller

```bash
php artisan make:controller Admin/PageController --resource
```

```php
// app/Http/Controllers/Admin/PageController.php

namespace App\Http\Controllers\Admin;

use App\Http\Controllers\Controller;
use App\Models\Page;
use Illuminate\Http\Request;
use Illuminate\Support\Str;

class PageController extends Controller
{
    public function index()
    {
        // withTrashed(): Silinmiş kayıtları da dahil et (SoftDeletes varsa).
        // Sadece silinmiş olanlar: onlyTrashed()
        $pages = Page::with('author')
                     ->orderByDesc('created_at')
                     ->paginate(20);

        return view('admin.pages.index', compact('pages'));
    }

    public function create()
    {
        return view('admin.pages.create');
    }

    public function store(Request $request)
    {
        $validated = $request->validate([
            'title'            => 'required|string|max:200',
            'content'          => 'nullable|string',
            'meta_title'       => 'nullable|string|max:200',
            'meta_description' => 'nullable|string|max:300',
            'status'           => 'required|in:published,draft,archived',
            'show_in_menu'     => 'boolean',
            'featured_image'   => 'nullable|image|mimes:jpg,jpeg,png,webp|max:2048',
            // image: Resim dosyası olmalı
            // mimes: Sadece bu uzantılar
            // max:2048: Maksimum 2MB (kilobyte cinsinden)
        ]);

        // Dosya yükleme
        if ($request->hasFile('featured_image')) {
            // store(): Dosyayı storage/app/public/pages/ klasörüne kaydeder.
            // Geri dönüş değeri: Dosyanın yolu ("pages/abc123.jpg" gibi).
            $validated['featured_image'] = $request->file('featured_image')
                                                   ->store('pages', 'public');
        }

        $validated['slug']       = Str::slug($validated['title']);
        $validated['author_id']  = auth()->id();
        // auth()->id(): Giriş yapan kullanıcının ID'si.

        $validated['published_at'] = $validated['status'] === 'published' ? now() : null;

        Page::create($validated);

        return redirect()->route('admin.pages.index')
                         ->with('success', 'Sayfa oluşturuldu!');
    }

    public function edit(Page $page)
    {
        return view('admin.pages.edit', compact('page'));
    }

    public function update(Request $request, Page $page)
    {
        $validated = $request->validate([
            'title'            => 'required|string|max:200',
            'content'          => 'nullable|string',
            'meta_title'       => 'nullable|string|max:200',
            'meta_description' => 'nullable|string|max:300',
            'status'           => 'required|in:published,draft,archived',
            'show_in_menu'     => 'boolean',
        ]);

        // Durum published'a değiştiyse yayın tarihini şimdiye ayarla
        if ($validated['status'] === 'published' && !$page->published_at) {
            $validated['published_at'] = now();
        }

        $page->update($validated);

        return redirect()->route('admin.pages.index')
                         ->with('success', 'Sayfa güncellendi!');
    }

    public function destroy(Page $page)
    {
        // SoftDeletes ile sadece deleted_at doldurulur
        $page->delete();

        return redirect()->route('admin.pages.index')
                         ->with('success', 'Sayfa arşivlendi!');
    }

    // Geri yükleme (SoftDeletes)
    public function restore(int $id)
    {
        $page = Page::onlyTrashed()->findOrFail($id);
        $page->restore();

        return back()->with('success', 'Sayfa geri yüklendi!');
    }
}
```

---

## BÖLÜM 7 — ROUTE (YÖNLENDİRME)

### Route Nedir?

Route, belirli bir URL'e gelen isteğin hangi controller metoduna gideceğini tanımlar.
`routes/web.php` dosyasında yazılır.

```php
// routes/web.php

use App\Http\Controllers\PageController;
use App\Http\Controllers\Admin\MenuController  as AdminMenuController;
use App\Http\Controllers\Admin\PageController  as AdminPageController;
use Illuminate\Support\Facades\Route;

// =============================================
// ZİYARETÇİ ROTALARI
// =============================================

// GET / → PageController@home metodunu çalıştır
Route::get('/', [PageController::class, 'home'])->name('home');
// .name('home'): Bu rotaya isim verilir. route('home') ile URL oluşturulabilir.

// GET /sayfa/{slug} → PageController@show
Route::get('/sayfa/{slug}', [PageController::class, 'show'])->name('page.show');
// {slug}: URL parametresi. Controller'da string $slug olarak alınır.

// =============================================
// ADMIN ROTALARI (auth middleware korumalı)
// =============================================

Route::prefix('admin')          // Tüm URL'ler /admin/ ile başlar
     ->name('admin.')           // Tüm rota isimleri admin. ile başlar
     ->middleware(['auth', 'verified'])  // Giriş ve e-posta doğrulama zorunlu
     ->group(function () {

    // /admin → Dashboard
    Route::get('/', function () {
        return view('admin.dashboard');
    })->name('dashboard');

    // Menü CRUD (7 rota otomatik oluşturulur)
    Route::resource('menus', AdminMenuController::class);
    // Oluşturulan rotalar:
    // GET    /admin/menus              → index    → admin.menus.index
    // GET    /admin/menus/create       → create   → admin.menus.create
    // POST   /admin/menus              → store    → admin.menus.store
    // GET    /admin/menus/{menu}       → show     → admin.menus.show
    // GET    /admin/menus/{menu}/edit  → edit     → admin.menus.edit
    // PUT    /admin/menus/{menu}       → update   → admin.menus.update
    // DELETE /admin/menus/{menu}       → destroy  → admin.menus.destroy

    // Sayfa CRUD
    Route::resource('pages', AdminPageController::class);

    // Silinmiş sayfayı geri yükle (custom rota)
    Route::post('pages/{id}/restore', [AdminPageController::class, 'restore'])
         ->name('pages.restore');
     // Özel rota: resource dışında tanımlanmış.
});

// =============================================
// ROUTE LİSTESİNİ GÖRÜNTÜLE
// =============================================
// php artisan route:list
// Tüm tanımlı rotaları tablo olarak listeler.
// Hangi URL, hangi controller@metod, middleware bilgisini gösterir.
```

**Rota önbelleği:**
```bash
php artisan route:cache
# Tüm rotaları tek dosyaya derler. Uygulama hızlanır.
# Production ortamında kullanılır.

php artisan route:clear
# Rota önbelleğini temizler. Rota değişikliklerinin etkili olması için.
```

---

## BÖLÜM 8 — BLADE VIEW (ŞABLONLAR)

### Blade Nedir?

Blade, Laravel'in şablon motorudur. `.blade.php` uzantılı dosyalarda kullanılır.
HTML içine PHP kodu eklemek için `{{ }}`, `@if`, `@foreach` gibi yönergeler kullanılır.
`resources/views/` klasöründe tutulur.

### Temel Blade Sözdizimi

```blade
{{-- Bu bir Blade yorumudur (HTML çıktısına yansımaz) --}}

{{-- Değişken yazdırma (XSS güvenliği: HTML karakterleri kaçırılır) --}}
{{ $degisken }}

{{-- Ham HTML yazdırma (Dikkat: XSS riski!) --}}
{!! $htmlIcerik !!}

{{-- Koşul --}}
@if($durum)
    <p>Aktif</p>
@elseif($baskaDurum)
    <p>Beklemede</p>
@else
    <p>Pasif</p>
@endif

{{-- Döngü --}}
@foreach($ogeler as $oge)
    <li>{{ $oge->title }}</li>
@endforeach

{{-- Boşsa farklı içerik göster --}}
@forelse($ogeler as $oge)
    <li>{{ $oge->title }}</li>
@empty
    <p>Hiç öğe bulunamadı.</p>
@endforelse

{{-- CSRF Token (form güvenliği, her POST formunda zorunlu) --}}
<form method="POST">
    @csrf
    {{-- Gizli _token alanı ekler. Olmadan 419 hatası alınır. --}}
</form>

{{-- PUT/DELETE metodu taklit etme (HTML form sadece GET/POST destekler) --}}
<form method="POST">
    @csrf
    @method('PUT')    {{-- veya @method('DELETE') --}}
</form>

{{-- Route URL oluşturma --}}
<a href="{{ route('page.show', $page->slug) }}">{{ $page->title }}</a>

{{-- Koşullu CSS sınıfı --}}
<div class="{{ $aktif ? 'bg-green-500' : 'bg-gray-200' }}">
```

---

### Adım 8.1 — Ana Layout

```bash
php artisan make:view layouts.app
```

```blade
{{-- resources/views/layouts/app.blade.php --}}
<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    {{-- @yield: Alt sayfalarda @section ile doldurulur --}}
    <title>@yield('title', config('app.name'))</title>
    {{-- config('app.name'): .env dosyasındaki APP_NAME değerini okur --}}

    @yield('meta')
    {{-- Her sayfanın kendi meta etiketlerini eklemesi için --}}

    @vite(['resources/css/app.css', 'resources/js/app.js'])
    {{-- @vite: Vite ile derlenen CSS ve JS dosyalarını sayfaya ekler --}}
</head>
<body>

    {{-- ===== HEADER ===== --}}
    <header>
        @if($headerMenu)
        <nav>
            @foreach($headerMenu->items as $item)
                @if($item->children->isEmpty())
                    {{-- Alt öğesi olmayan menü öğesi --}}
                    <a href="{{ $item->full_url }}"
                       target="{{ $item->target }}"
                       class="{{ request()->url() === $item->full_url ? 'aktif' : '' }}">
                       {{-- request()->url(): Şu anki sayfanın URL'i --}}
                        {{ $item->title }}
                    </a>
                @else
                    {{-- Alt öğesi olan menü öğesi (dropdown) --}}
                    <div class="dropdown">
                        <span>{{ $item->title }}</span>
                        <ul>
                            @foreach($item->children as $child)
                            <li>
                                <a href="{{ $child->full_url }}">{{ $child->title }}</a>
                            </li>
                            @endforeach
                        </ul>
                    </div>
                @endif
            @endforeach
        </nav>
        @endif
    </header>

    {{-- ===== SAYFA İÇERİĞİ ===== --}}
    <main>
        {{-- @yield: Bu alanı alt sayfalardaki @section('content') doldurur --}}
        @yield('content')
    </main>

    {{-- ===== FOOTER ===== --}}
    <footer>
        <p>© {{ date('Y') }} {{ config('app.name') }}</p>
    </footer>

    {{-- Sayfaya özel JavaScript için yer --}}
    @stack('scripts')
    {{-- Alt sayfalar @push('scripts') ile buraya JS ekleyebilir --}}

</body>
</html>
```

---

### Adım 8.2 — Anasayfa View

```bash
php artisan make:view home
```

```blade
{{-- resources/views/home.blade.php --}}

{{-- @extends: Hangi layoutu kullanacağımızı söyleriz --}}
@extends('layouts.app')

{{-- @section('title'): Layouttaki @yield('title') alanını doldurur --}}
@section('title', 'Ana Sayfa')

{{-- @section('content'): Layouttaki @yield('content') alanını doldurur --}}
@section('content')

<div class="container">
    <h1>Hoş Geldiniz</h1>

    {{-- Flash mesajı göster (controller'dan ->with() ile gönderilen) --}}
    @if(session('success'))
    <div class="alert alert-success">
        {{ session('success') }}
    </div>
    @endif

    {{-- Sayfa listesi --}}
    <div class="sayfalar">
        @forelse($pages as $page)
        <article>
            @if($page->featured_image)
            {{-- asset(): public/ klasöründeki dosyanın URL'ini oluşturur --}}
            {{-- Storage::url(): storage/app/public/ içindeki dosyanın URL'i --}}
            <img src="{{ Storage::url($page->featured_image) }}" alt="{{ $page->title }}">
            @endif

            <h2>
                <a href="{{ route('page.show', $page->slug) }}">{{ $page->title }}</a>
            </h2>

            @if($page->meta_description)
            <p>{{ $page->meta_description }}</p>
            @endif

            {{-- Carbon ile tarih formatlama --}}
            <small>{{ $page->published_at->format('d.m.Y') }}</small>
        </article>
        @empty
        <p>Henüz yayınlanmış sayfa bulunmuyor.</p>
        @endforelse
    </div>
</div>

@endsection

{{-- @push: Layouttaki @stack('scripts') alanına ekler --}}
@push('scripts')
<script>
    console.log('Anasayfa yüklendi');
</script>
@endpush
```

---

### Adım 8.3 — Admin Menü Listesi

```bash
php artisan make:view admin.menus.index
```

```blade
{{-- resources/views/admin/menus/index.blade.php --}}
@extends('layouts.admin')

@section('title', 'Menü Yönetimi')

@section('content')
<div class="admin-sayfa">

    <div class="baslik-satiri">
        <h1>Menüler</h1>
        <a href="{{ route('admin.menus.create') }}" class="buton buton-birincil">
            + Yeni Menü
        </a>
    </div>

    {{-- Başarı mesajı --}}
    @if(session('success'))
    <div class="alert alert-success">{{ session('success') }}</div>
    @endif

    {{-- Hata mesajları (validasyon) --}}
    @if($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach($errors->all() as $hata)
            <li>{{ $hata }}</li>
            @endforeach
        </ul>
    </div>
    @endif

    {{-- Menü Tablosu --}}
    <table>
        <thead>
            <tr>
                <th>#</th>
                <th>Menü Adı</th>
                <th>Konum</th>
                <th>Öğe Sayısı</th>
                <th>Durum</th>
                <th>İşlemler</th>
            </tr>
        </thead>
        <tbody>
            @forelse($menus as $menu)
            <tr>
                <td>{{ $menu->id }}</td>
                <td>{{ $menu->name }}</td>
                <td>{{ ucfirst($menu->location) }}</td>
                <td>{{ $menu->allItems->count() }}</td>
                <td>
                    @if($menu->is_active)
                    <span class="rozet yesil">Aktif</span>
                    @else
                    <span class="rozet kirmizi">Pasif</span>
                    @endif
                </td>
                <td>
                    <a href="{{ route('admin.menus.edit', $menu) }}">Düzenle</a>

                    {{-- DELETE isteği için form (HTML'de DELETE desteklenmez) --}}
                    <form method="POST"
                          action="{{ route('admin.menus.destroy', $menu) }}"
                          onsubmit="return confirm('Emin misiniz?')">
                        @csrf
                        @method('DELETE')
                        <button type="submit">Sil</button>
                    </form>
                </td>
            </tr>
            @empty
            <tr>
                <td colspan="6">Henüz menü oluşturulmamış.</td>
            </tr>
            @endforelse
        </tbody>
    </table>

    {{-- Sayfalama --}}
    {{ $menus->links() }}
    {{-- links(): Tailwind CSS stilinde sayfalama bağlantılarını çizer --}}

</div>
@endsection
```

---

## BÖLÜM 9 — SEEDER (TEST VERİSİ)

### Seeder Nedir?

Seeder, veritabanına test veya başlangıç verisi ekleyen PHP sınıflarıdır.
`database/seeders/` klasöründe tutulur.

### Seeder Oluşturma Komutu

```bash
php artisan make:seeder SeederAdı
```

---

### Adım 9.1 — Kullanıcı Seeder

```bash
php artisan make:seeder UserSeeder
```

```php
// database/seeders/UserSeeder.php

namespace Database\Seeders;

use App\Models\User;
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\Hash;

class UserSeeder extends Seeder
{
    public function run(): void
    {
        // Admin kullanıcısı oluştur
        User::create([
            'name'     => 'Site Yöneticisi',
            'email'    => 'admin@site.com',
            'password' => Hash::make('Admin123!'),
            // Hash::make(): Şifreyi güvenli şekilde hashler. Asla düz metin kaydedilmez!
            'email_verified_at' => now(),
        ]);
    }
}
```

---

### Adım 9.2 — Menü Seeder

```bash
php artisan make:seeder MenuSeeder
```

```php
// database/seeders/MenuSeeder.php

namespace Database\Seeders;

use App\Models\Menu;
use App\Models\MenuItem;
use App\Models\Page;
use Illuminate\Database\Seeder;

class MenuSeeder extends Seeder
{
    public function run(): void
    {
        // Header menüsü oluştur
        $headerMenu = Menu::create([
            'name'      => 'Ana Menü',
            'slug'      => 'ana-menu',
            'location'  => 'header',
            'is_active' => true,
            'order'     => 1,
        ]);

        // Menü öğeleri ekle
        $anasayfaOge = MenuItem::create([
            'menu_id'   => $headerMenu->id,
            'title'     => 'Anasayfa',
            'url'       => '/',
            'order'     => 1,
            'is_active' => true,
        ]);

        $hakkimizdaOge = MenuItem::create([
            'menu_id'   => $headerMenu->id,
            'title'     => 'Kurumsal',
            'url'       => null,   // Alt menü grubu, URL yok
            'order'     => 2,
            'is_active' => true,
        ]);

        // Alt menü öğesi
        MenuItem::create([
            'menu_id'   => $headerMenu->id,
            'parent_id' => $hakkimizdaOge->id,  // Üst öğeye bağlı
            'title'     => 'Hakkımızda',
            'url'       => '/sayfa/hakkimizda',
            'order'     => 1,
            'is_active' => true,
        ]);

        MenuItem::create([
            'menu_id'   => $headerMenu->id,
            'parent_id' => $hakkimizdaOge->id,
            'title'     => 'Misyon & Vizyon',
            'url'       => '/sayfa/misyon-vizyon',
            'order'     => 2,
            'is_active' => true,
        ]);
    }
}
```

---

### Adım 9.3 — Sayfa Seeder

```bash
php artisan make:seeder PageSeeder
```

```php
// database/seeders/PageSeeder.php

class PageSeeder extends Seeder
{
    public function run(): void
    {
        $adminId = \App\Models\User::where('email', 'admin@site.com')->value('id');
        // value('id'): Sadece id kolonunu döndürür (tüm modeli yüklemez, daha hızlı).

        $sayfalar = [
            [
                'title'       => 'Anasayfa',
                'slug'        => 'anasayfa',
                'content'     => '<h2>Hoş Geldiniz</h2><p>Sitemize hoş geldiniz...</p>',
                'status'      => 'published',
                'published_at'=> now(),
                'author_id'   => $adminId,
                'order'       => 1,
            ],
            [
                'title'       => 'Hakkımızda',
                'slug'        => 'hakkimizda',
                'content'     => '<p>Şirketimiz hakkında bilgi...</p>',
                'status'      => 'published',
                'published_at'=> now(),
                'author_id'   => $adminId,
                'order'       => 2,
            ],
            [
                'title'       => 'İletişim',
                'slug'        => 'iletisim',
                'content'     => '<p>Bize ulaşın...</p>',
                'status'      => 'draft',   // Taslak, yayında değil
                'author_id'   => $adminId,
                'order'       => 3,
            ],
        ];

        foreach ($sayfalar as $sayfa) {
            \App\Models\Page::create($sayfa);
        }
    }
}
```

---

### Adım 9.4 — DatabaseSeeder (Ana Seeder)

```php
// database/seeders/DatabaseSeeder.php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    /**
     * Sıra önemlidir: Bağımlı tablolar sonra çalıştırılmalı.
     * User → Page → Menu → MenuItem (menu_items users'a ve pages'e bağlı)
     */
    public function run(): void
    {
        $this->call([
            UserSeeder::class,    // Önce kullanıcılar
            PageSeeder::class,    // Sonra sayfalar
            MenuSeeder::class,    // Son olarak menüler
        ]);
    }
}
```

### Seeder Çalıştırma Komutları

```bash
# Tüm seeder'ı çalıştır
php artisan db:seed

# Belirli bir seeder'ı çalıştır
php artisan db:seed --class=MenuSeeder

# Veritabanını sıfırla ve seeder çalıştır (en çok kullanılan)
php artisan migrate:fresh --seed
```

---

## BÖLÜM 10 — MIDDLEWARE

### Middleware Nedir?

Middleware, HTTP isteği controller'a ulaşmadan önce veya cevap kullanıcıya
gitmeden önce araya giren katmanlardır. Kimlik doğrulama, yetki kontrolü,
log tutma gibi işler için kullanılır.

### Middleware Oluşturma Komutu

```bash
php artisan make:middleware MiddlewareAdı
```

---

### Adım 10.1 — Admin Kontrolü Middleware

```bash
php artisan make:middleware AdminMiddleware
```

```php
// app/Http/Middleware/AdminMiddleware.php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class AdminMiddleware
{
    /**
     * $request: Gelen HTTP isteği (URL, form verisi, header'lar)
     * $next: Bir sonraki middleware veya controller
     */
    public function handle(Request $request, Closure $next)
    {
        // Giriş yapılmamışsa login sayfasına yönlendir
        if (!auth()->check()) {
            return redirect()->route('login');
        }

        // Admin rolü yoksa ana sayfaya yönlendir
        if (!auth()->user()->hasRole('admin')) {
            abort(403, 'Bu sayfaya erişim yetkiniz yok.');
            // abort(403): 403 Forbidden HTTP hatası döndürür.
        }

        // Kontrol geçti, isteği bir sonraki adıma ilet
        return $next($request);
    }
}
```

**Middleware'i kaydet (`bootstrap/app.php`):**
```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->alias([
        'admin' => \App\Http\Middleware\AdminMiddleware::class,
    ]);
})
```

**Route'da kullan:**
```php
Route::prefix('admin')->middleware('admin')->group(function () {
    // Admin rotaları
});
```

---

## BÖLÜM 11 — TÜM KOMUTLARIN AÇIKLAMASI

### Proje Yönetimi

```bash
php artisan list
# Projede kullanılabilir tüm Artisan komutlarını tablo olarak listeler.
# Hangi komutların var olduğunu öğrenmek için ilk bakılacak yer.

php artisan --version
# Projenin kullandığı Laravel sürümünü gösterir.
# Örnek: Laravel Framework 11.0.0

php artisan serve
# Yerleşik PHP geliştirme sunucusunu başlatır (http://localhost:8000).
# Gerçek sunucu kurmadan hızlı test için kullanılır.

php artisan serve --host=0.0.0.0 --port=8080
# Ağdaki diğer cihazlardan erişim için farklı host ve port.

php artisan down
# Uygulamayı bakım moduna alır.
# Ziyaretçilere "503 Service Unavailable" sayfası gösterilir.
# Sunucu güncellemesi yaparken kullanılır.

php artisan down --message="Kısa bakım çalışması" --retry=60
# --message: Bakım mesajını özelleştirir.
# --retry: Kaç saniye sonra tekrar denenebileceğini belirtir.

php artisan up
# Bakım modundan çıkar, site ziyaretçilere tekrar açılır.

php artisan env
# .env dosyasındaki APP_ENV değerini gösterir.
# Çıktı: "The application environment is local"
```

---

### Veritabanı Komutları

```bash
php artisan migrate
# database/migrations/ klasöründeki çalışmamış migration dosyalarını çalıştırır.
# migrations tablosuna hangi dosyaların çalıştığını kaydeder.

php artisan migrate --pretend
# Gerçekten çalıştırmadan, çalıştırılacak SQL'leri ekrana yazar.
# Neyin değişeceğini önceden görmek için.

php artisan migrate:status
# Her migration dosyasının çalışıp çalışmadığını (Yes/No) listeler.

php artisan migrate:rollback
# Son "batch" (grup) migration'ları geri alır (down() metodlarını çalıştırır).
# Son eklenen tabloları kaldırmak için.

php artisan migrate:rollback --step=2
# Son 2 migration grubunu geri al.

php artisan migrate:refresh
# Tüm migration'ları geri alır sonra baştan çalıştırır.
# Şema değiştiyse sıfırdan oluşturmak için. VERİ SİLİNİR!

php artisan migrate:refresh --seed
# Refresh + otomatik seeder çalıştırma.

php artisan migrate:fresh
# Tüm tabloları DROP TABLE ile siler, migration'ları baştan çalıştırır.
# migrate:refresh'ten farkı: down() metodlarını çalıştırmaz, direkt siler.
# Daha hızlı. VERİ SİLİNİR!

php artisan migrate:fresh --seed
# Geliştirmede en sık kullanılan: temiz DB + test verisi.

php artisan migrate:reset
# Tüm migration'ları geri alır. Tablolar silinir, yeniden oluşturulmaz.

php artisan db:seed
# DatabaseSeeder'ı çalıştırır, veritabanını test verisiyle doldurur.

php artisan db:seed --class=MenuSeeder
# Sadece belirtilen seeder sınıfını çalıştırır.

php artisan db:wipe
# Tüm tabloları, view'ları ve sequence'ları siler. Migration dışında.
```

---

### Kod Üretme (Make) Komutları

```bash
php artisan make:model ModelAdı
# app/Models/ altına Eloquent model sınıfı oluşturur.

php artisan make:model ModelAdı -m
# Model + migration dosyası oluşturur.

php artisan make:model ModelAdı -mc
# Model + migration + controller oluşturur.

php artisan make:model ModelAdı -mcr
# Model + migration + resource controller oluşturur.
# Resource controller: index, create, store, show, edit, update, destroy metotları hazır gelir.

php artisan make:model ModelAdı --all
# Migration + factory + seeder + controller + policy + resource — hepsini oluşturur.

php artisan make:controller ControllerAdı
# app/Http/Controllers/ altına boş controller oluşturur.

php artisan make:controller ControllerAdı --resource
# CRUD için 7 metodu hazır controller oluşturur.

php artisan make:controller ControllerAdı --api
# API için 5 metotlu controller (create ve edit metotları olmadan).

php artisan make:controller Admin/ControllerAdı
# Klasör içinde controller oluşturur. Klasör yoksa otomatik oluşturur.

php artisan make:migration MigrationAdı
# database/migrations/ altına migration dosyası oluşturur.
# Dosya adı otomatik timestamp alır: 2024_01_01_000000_migration_adi.php

php artisan make:migration create_tablo_adi_table
# "create_" ön eki: Schema::create() kodu otomatik yazılır.

php artisan make:migration add_kolon_to_tablo_table
# "add_" ön eki: Schema::table() kodu otomatik yazılır.

php artisan make:seeder SeederAdı
# database/seeders/ altına seeder sınıfı oluşturur.

php artisan make:middleware MiddlewareAdı
# app/Http/Middleware/ altına middleware sınıfı oluşturur.

php artisan make:request RequestAdı
# app/Http/Requests/ altına form request sınıfı oluşturur.
# Validasyon kurallarını controller'dan ayırmak için kullanılır.

php artisan make:view sayfaAdi
# resources/views/ altına .blade.php dosyası oluşturur.
# Alt klasör: php artisan make:view admin.menus.index
# → resources/views/admin/menus/index.blade.php

php artisan make:policy PolicyAdı
# app/Policies/ altında yetkilendirme policy sınıfı oluşturur.
# Belirli bir model için erişim kuralları tanımlanır.

php artisan make:event EventAdı
# app/Events/ altına olay sınıfı oluşturur.

php artisan make:listener ListenerAdı
# app/Listeners/ altına olay dinleyici sınıfı oluşturur.

php artisan make:job JobAdı
# app/Jobs/ altına kuyruk işi sınıfı oluşturur.

php artisan make:notification NotificationAdı
# app/Notifications/ altına bildirim sınıfı oluşturur.

php artisan make:mail MailAdı
# app/Mail/ altına mail sınıfı oluşturur.

php artisan make:component BilesenAdı
# app/View/Components/ ve resources/views/components/ altına bileşen oluşturur.
```

---

### Cache ve Optimizasyon Komutları

```bash
php artisan cache:clear
# Uygulama önbelleğini (storage/framework/cache/) temizler.
# Cache::put() ile kaydedilen veriler silinir.

php artisan config:cache
# Tüm config dosyalarını tek dosyaya derler.
# Uygulama her istekte config dosyalarını tekrar okumaz → hızlanır.
# Production ortamında kullanılır.

php artisan config:clear
# config:cache ile oluşturulan önbellek dosyasını siler.
# Config değişikliğinin etkili olması için.

php artisan route:cache
# Tüm rotaları tek dosyaya derler, rota yükleme hızlanır.
# Kapatma (closure) route varsa çalışmaz.

php artisan route:clear
# Rota önbelleğini temizler.

php artisan route:list
# Tüm tanımlı rotaları: URL, HTTP metodu, isim, controller, middleware ile listeler.
# Route sorunlarını debug etmek için vazgeçilmez.

php artisan view:cache
# Tüm Blade şablonlarını PHP'ye derler.
# İlk istek hızlanır.

php artisan view:clear
# Derlenmiş Blade şablon önbelleğini temizler.
# Şablon değişikliklerinin yansıması için.

php artisan optimize
# config:cache + route:cache + view:cache işlemlerini tek seferde yapar.
# Production ortamına deploy ederken kullanılır.

php artisan optimize:clear
# Tüm önbellek türlerini temizler (config + route + view + cache + bootstrap).
# Geliştirmede sorun yaşandığında ilk başvurulacak komut.

php artisan clear-compiled
# Derlenmiş sınıf dosyasını (bootstrap/cache/compiled.php) siler.
# optimize:clear ile aynı işi yapar, eski Laravel sürümlerinden kalan komut.
```

---

### Geliştirme Yardımcı Komutları

```bash
php artisan tinker
# Laravel'i interaktif REPL (Read-Eval-Print Loop) ortamında açar.
# Model sorguları, test kodları denemek için.
# Örnek:
#   User::all()
#   User::find(1)->name
#   Menu::count()
#   Ctrl+C ile çıkılır.

php artisan storage:link
# storage/app/public/ klasörünü public/storage/ ile sembolik link kurar.
# Yüklenen dosyalara tarayıcıdan erişmek için zorunludur.
# Storage::url() ve asset('storage/...') bu link sayesinde çalışır.
# Bir kez çalıştırılır.

php artisan key:generate
# .env dosyasındaki APP_KEY değerini rastgele oluşturur.
# Şifreleme, oturum ve çerez güvenliği için zorunludur.
# Yeni projede bir kez çalıştırılır.

composer dump-autoload
# Composer'ın sınıf haritasını (autoload_classmap.php) yeniden oluşturur.
# "Class not found" hatası aldığında çalıştırılır.
# Yeni sınıf dosyası eklenince gerekebilir.

composer require paket/adi
# Belirtilen paketi indirir, vendor/ klasörüne ekler, composer.json günceller.

composer install
# composer.json ve composer.lock dosyasına göre tüm paketleri indirir.
# Projeyi klonladıktan sonra ilk çalıştırılacak komut.

composer update
# Tüm paketleri en güncel sürüme günceller ve composer.lock günceller.
# Dikkatli kullanılmalı: Büyük güncellemeler uyumsuzluk yaratabilir.
```

---

## BÖLÜM 12 — PROJE ÖZET TABLOSU

### Dosya Yapısı

```
menu-icerik-yonetimi/
├── app/
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── PageController.php          ← Ziyaretçi sayfaları
│   │   │   └── Admin/
│   │   │       ├── MenuController.php      ← Admin menü yönetimi
│   │   │       └── PageController.php      ← Admin sayfa yönetimi
│   │   └── Middleware/
│   │       └── AdminMiddleware.php
│   └── Models/
│       ├── Menu.php
│       ├── MenuItem.php
│       └── Page.php
├── database/
│   ├── migrations/
│   │   ├── ..._create_menus_table.php
│   │   ├── ..._create_menu_items_table.php
│   │   └── ..._create_pages_table.php
│   └── seeders/
│       ├── DatabaseSeeder.php
│       ├── UserSeeder.php
│       ├── MenuSeeder.php
│       └── PageSeeder.php
├── resources/views/
│   ├── layouts/
│   │   ├── app.blade.php                  ← Ziyaretçi layout
│   │   └── admin.blade.php                ← Admin layout
│   ├── home.blade.php                     ← Anasayfa
│   ├── page.blade.php                     ← Sayfa detay
│   └── admin/
│       ├── dashboard.blade.php
│       ├── menus/
│       │   ├── index.blade.php
│       │   ├── create.blade.php
│       │   └── edit.blade.php
│       └── pages/
│           ├── index.blade.php
│           ├── create.blade.php
│           └── edit.blade.php
└── routes/
    └── web.php
```

---

### Sıfırdan Çalışır Hale Getirme

```bash
# 1. Proje oluştur
composer create-project --prefer-dist laravel/laravel menu-icerik-yonetimi
cd menu-icerik-yonetimi

# 2. Uygulama anahtarı
php artisan key:generate

# 3. .env düzenle (DB bilgileri)
# DB_DATABASE=menu_icerik_db ayarla

# 4. Paketleri yükle
composer require spatie/laravel-permission
composer require barryvdh/laravel-dompdf

# 5. Migration oluştur ve çalıştır
php artisan make:migration create_menus_table
php artisan make:migration create_menu_items_table
php artisan make:migration create_pages_table
php artisan migrate

# 6. Model ve Controller oluştur
php artisan make:model Menu
php artisan make:model MenuItem
php artisan make:model Page
php artisan make:controller PageController
php artisan make:controller Admin/MenuController --resource
php artisan make:controller Admin/PageController --resource

# 7. View'ları oluştur
php artisan make:view layouts.app
php artisan make:view home
php artisan make:view admin.menus.index

# 8. Seeder oluştur ve çalıştır
php artisan make:seeder UserSeeder
php artisan make:seeder MenuSeeder
php artisan make:seeder PageSeeder
php artisan db:seed

# 9. Storage bağla
php artisan storage:link

# 10. Geliştirme sunucusunu başlat
php artisan serve
```

---

### Sık Kullanılan Geliştirme Akışı

```bash
# Şema değiştiyse → sıfırla ve test verisini yükle
php artisan migrate:fresh --seed

# Önbellekle ilgili sorun yaşıyorsan → hepsini temizle
php artisan optimize:clear

# Yeni sınıf ekledikten sonra
composer dump-autoload

# Route sorunları için
php artisan route:list

# Hızlı DB sorgusu test etmek için
php artisan tinker
```



*Bu belge Yalova Üniversitesi Çınarcık MYO — Ağ Tasarımı ve Yönetimi bölümü ders materyali olarak hazırlanmıştır.*


