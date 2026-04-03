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

*Bu belge Yalova Üniversitesi Çınarcık MYO — Ağ Tasarımı ve Yönetimi bölümü ders materyali olarak hazırlanmıştır.*
