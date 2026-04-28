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
