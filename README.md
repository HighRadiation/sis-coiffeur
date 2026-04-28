<div align="center">

[Turkce](#turkce) &nbsp;|&nbsp; [English](#english)

</div>

---

<a name="turkce"></a>

# Proje Modernizasyon ve Dinamik Mimari Rehberi

Bu dokümantasyon, statik bir Next.js projesini veritabanı destekli (Supabase) ve yönetim panelli (Admin Dashboard) bir yapıya dönüştürme sürecini teknik adımlarıyla kapsar.

---

## 1. Veritabanı ve Güvenlik Altyapısı (Supabase)

Sürecin ilk adımı verilerin güvenli ve ölçeklenebilir bir şekilde saklanmasıdır.

- **Tablo Tasarımı**: Projeler, Yetenekler, Zaman Çizelgesi ve Mesajlar modülleri için Supabase PostgreSQL tabloları oluşturulmuştur.
- **Row Level Security (RLS)**: Veritabanı güvenliği için kritik bir adımdır. Anonim kullanıcılar için sadece okuma (SELECT) izni verilirken, veri ekleme (INSERT) veya güncelleme yetkileri sadece doğrulanmış (Authenticated) admin rolüne atanmıştır.
- **Admin Erişimi**: Admin paneline erişim, Next.js Middleware kullanılarak oturum bazlı (Session-based) kontrol altına alınmıştır. Yetkisiz girişler otomatik olarak Login sayfasına yönlendirilir.

## 2. Backend Mimarisi (Server Actions ve API)

Performans ve güvenlik için Next.js'in modern sunucu tarafı özellikleri kullanılmıştır.

- **Server Actions**: Veri ekleme, silme ve güncelleme işlemleri için her modüle özel `actions.ts` dosyaları oluşturulmuştur. Bu yapı, istemci tarafında minimum JavaScript yükü oluşturur.
- **Revalidation**: Veritabanındaki bir değişiklikten sonra önbellekteki verilerin güncellenmesi için `revalidatePath` fonksiyonu kullanılarak sayfanın anında güncellenmesi sağlanmıştır.
- **Rate Limiting**: İletişim formu gibi dışa açık alanlarda spam saldırılarını önlemek amacıyla Upstash Redis entegrasyonu ile IP bazlı hız sınırı uygulanmıştır.

## 3. Admin Paneli Modülleri

Yönetim paneli, modüler ve genişletilebilir bir yapıda tasarlanmıştır.

- **Liste ve CRUD**: Mevcut verilerin listelendiği, yeni kayıtların eklendiği ve silme işlemlerinin yapıldığı standart modüller kurulmuştur.
- **Form Doğrulama**: Kullanıcıdan alınan veriler sunucu tarafına gönderilmeden önce Zod kütüphanesi ile doğrulanır.
- **Seeding Sistemi**: Geliştirme sürecini hızlandırmak amacıyla, yerel dosyalardaki verileri tek bir komutla veritabanına aktaran fonksiyonlar eklenmiştir.

## 4. Frontend Dinamizasyonu

Hardcoded (statik) verilerin yerini veritabanından gelen canlı veriler almıştır.

- **Async Server Components**: Sayfalar ve bölümler, sunucu tarafında doğrudan veritabanına sorgu atacak şekilde (async/await) refaktör edilmiştir.
- **Layout Integrity**: Veri yükleme sürelerinde veya boş veri durumlarında sayfa düzeninin bozulmaması için minimum yükseklik (min-height) ve yedek (fallback) yapılar kurgulanmıştır.

## 5. Kullanıcı Deneyimi (UX) ve Görsel İyileştirmeler

Kullanıcı etkileşimini artırmak için modern UI teknikleri uygulanmıştır.

- **Modal Contact Form**: İletişim formu, sayfa karmaşıklığını azaltmak için bir popup (modal) içine taşınmıştır.
- **Typewriter Animasyonu**: Hero bölümündeki isim alanı, sayfa yüklendikten sonra belli bir gecikmeyle yazılmaya başlayan bir daktilo efekti ile canlandırılmıştır.
- **Geri Bildirim Sistemi**: Hata durumlarında veya başarılı veri gönderimlerinde kullanıcıya teknik detaydan arındırılmış, açıklayıcı mesajlar gösterilir.

## 6. Geliştirme ve Kod Standartları

Projenin sürdürülebilirliği için katı standartlar uygulanmıştır.

- **TypeScript Standartları**: Tüm fonksiyonlarda açık geri dönüş tipi (explicit return type) belirtilmesi ve `any` tipinin kullanılmaması kuralı benimsenmiştir.
- **Prettier ve ESLint**: Kodun tutarlılığını sağlamak için otomatik formatlama ve lint kontrolleri her commit öncesinde zorunlu kılınmıştır.
- **Git Workflow**: Her bir geliştirme veya hata düzeltmesi anlamlı prefixler (feat, fix, style) ile ayrı commitler halinde versiyonlanmıştır.

---

<div align="right"><a href="#turkce">Basa Don</a></div>

---

<a name="english"></a>

# Project Modernization & Dynamic Architecture Guide

This documentation covers the technical steps for transforming a static Next.js project into a database-backed (Supabase) structure with an Admin Dashboard.

---

## 1. Database & Security Infrastructure (Supabase)

The first step of the process is storing data securely and scalably.

- **Table Design**: Supabase PostgreSQL tables have been created for the Projects, Skills, Timeline, and Messages modules.
- **Row Level Security (RLS)**: A critical step for database security. Anonymous users are granted read-only (SELECT) access, while data insertion (INSERT) or update privileges are restricted to the authenticated admin role.
- **Admin Access**: Access to the admin panel is controlled via session-based authentication using Next.js Middleware. Unauthorized requests are automatically redirected to the Login page.

## 2. Backend Architecture (Server Actions & API)

Next.js modern server-side features are used for performance and security.

- **Server Actions**: Dedicated `actions.ts` files have been created for each module to handle data insertion, deletion, and updates. This approach minimizes client-side JavaScript payload.
- **Revalidation**: After a database change, the `revalidatePath` function is used to invalidate the cache and ensure the page reflects the latest data instantly.
- **Rate Limiting**: To prevent spam attacks on publicly exposed endpoints such as the contact form, IP-based rate limiting has been implemented using Upstash Redis.

## 3. Admin Panel Modules

The admin panel is designed with a modular and extensible architecture.

- **List & CRUD**: Standard modules have been set up for listing existing records, adding new entries, and performing delete operations.
- **Form Validation**: Data received from users is validated with the Zod library before being sent to the server.
- **Seeding System**: To speed up the development process, functions have been added to import data from local files into the database with a single command.

## 4. Frontend Dynamization

Hardcoded (static) data has been replaced with live data fetched from the database.

- **Async Server Components**: Pages and sections have been refactored to query the database directly on the server side using `async/await`.
- **Layout Integrity**: Minimum height (`min-height`) constraints and fallback structures have been implemented to prevent layout breakage during data loading or when no data is available.

## 5. User Experience (UX) & Visual Improvements

Modern UI techniques have been applied to enhance user interaction.

- **Modal Contact Form**: The contact form has been moved into a popup (modal) to reduce page complexity.
- **Typewriter Animation**: The name field in the Hero section is animated with a typewriter effect that starts typing after a brief delay following page load.
- **Feedback System**: In error or successful submission scenarios, descriptive messages free of technical jargon are displayed to the user.

## 6. Development & Code Standards

Strict standards have been enforced to ensure project maintainability.

- **TypeScript Standards**: Explicit return types are required for all functions, and usage of the `any` type is prohibited.
- **Prettier & ESLint**: Automatic formatting and lint checks are enforced before every commit to maintain code consistency.
- **Git Workflow**: Every feature or bug fix is versioned as a separate commit with meaningful prefixes (feat, fix, style).

---

<div align="right"><a href="#english">Back to Top</a></div>
