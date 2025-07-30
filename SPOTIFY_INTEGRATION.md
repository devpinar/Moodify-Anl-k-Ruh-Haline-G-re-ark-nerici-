
# 🎧 Spotify API Entegrasyon Kılavuzu (Moodify Uygulaması İçin)

## 📌 Amaç

Bu doküman, **Moodify** uygulamasına **Spotify Web API entegrasyonunun** nasıl yapıldığını, adım adım ve detaylı şekilde açıklar. Spotify API ile uygulama ruh haline uygun şarkıları gerçek zamanlı olarak çeker.

---

## 1️⃣ Spotify Geliştirici Hesabı Oluştur

1. Spotify Developer’a git:  
   👉 https://developer.spotify.com/dashboard

2. Giriş yap veya kayıt ol.

3. “**Create An App**” butonuna tıkla.

4. Uygulama bilgilerini gir (örnek: “Moodify”)

5. Uygulama oluşturulduktan sonra:
   - **Client ID**’ni al
   - **Client Secret**’i al  
   (bunları gizli tut! kimseyle paylaşma)

---

## 2️⃣ Access Token (Erişim Anahtarı) Al

Spotify Web API'yi kullanmak için bir erişim anahtarı gerekir. Moodify gibi “sunucusuz” uygulamalarda **Client Credentials Flow** kullanılır.

### 🔒 Token Alma Endpoint’i:

```
POST https://accounts.spotify.com/api/token
```

### 🔧 Header:

```
Authorization: Basic <base64(client_id:client_secret)>
Content-Type: application/x-www-form-urlencoded
```

👉 `client_id:client_secret` ikilisini https://www.base64encode.org üzerinden Base64 ile kodla.

### 📨 Body:

```
grant_type=client_credentials
```

### ✅ Cevap:

Başarılıysa sana şöyle bir JSON döner:

```json
{
  "access_token": "BQD...xyz",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

`access_token`'ı tüm diğer isteklerde kullanacağız.

---

## 3️⃣ Ruh Haline Göre Şarkı Arama

Moodify, kullanıcıdan ruh halini alır (örneğin: "happy", "sad", "angry") ve bu kelimeyi Spotify'da arar.

### 🔍 Search Endpoint:

```
GET https://api.spotify.com/v1/search?q=happy&type=track&limit=5
```

### 🔧 Header:

```
Authorization: Bearer <access_token>
```

### 💡 Not:

- `q=happy` kısmını dinamik yap: kullanıcı ne seçtiyse oraya onu koy.
- `limit=5` → sadece 5 şarkı döner (isteğe göre artırılabilir).

### 🔁 Örnek Yanıt:

```json
{
  "tracks": {
    "items": [
      {
        "name": "Happy",
        "artists": [{ "name": "Pharrell Williams" }],
        "external_urls": {
          "spotify": "https://open.spotify.com/track/abc123"
        },
        "album": {
          "images": [
            { "url": "https://i.scdn.co/image/abc" }
          ]
        }
      }
    ]
  }
}
```

Bu veriyi uygulamada şöyle göster:
- 🎵 Şarkı adı: `name`
- 👤 Sanatçı: `artists[0].name`
- 🖼️ Kapak: `album.images[0].url`
- 🔗 Spotify Linki: `external_urls.spotify`

---

## 4️⃣ Ready AI'de API’yi Bağlamak

### 🔗 1. API ekle

Ready AI içinde:
- **API Actions** bölümüne git
- Yeni API tanımla

### 🔗 2. Access Token alma işlemi için POST request ayarla:
- URL: `https://accounts.spotify.com/api/token`
- Method: `POST`
- Header:
  - `Authorization: Basic <base64_encoded_credentials>`
  - `Content-Type: application/x-www-form-urlencoded`
- Body:
  - `grant_type=client_credentials`

🟡 Bu işlemden sonra `access_token`’ı bir değişkene kaydet.

### 🔗 3. Şarkı arama işlemi için GET request ayarla:
- URL: `https://api.spotify.com/v1/search?q={{mood}}&type=track&limit=5`
- Method: `GET`
- Header:
  - `Authorization: Bearer {{access_token}}`

📌 `{{mood}}` = Kullanıcının seçtiği ruh hali  
📌 `{{access_token}}` = Bir önceki adımda aldığın token

---

## 5️⃣ UI’ye Veriyi Gösterme

Ready AI’de gelen her şarkı için:
- Bir kart oluştur
- Kartın içinde:
  - Şarkı adı
  - Sanatçı adı
  - Kapak görseli
  - Spotify’a git butonu (`external_urls.spotify`)

Kartlar yatay kaydırmalı veya dikey liste olabilir.

---

## 🧪 Test Et

- Tüm ruh halleri için şarkı çekiliyor mu?
- Spotify linki çalışıyor mu?
- Token süresi dolunca yeni token alınıyor mu?

---

## ✅ Bonus İpuçları

- Token 3600 saniye (1 saat) geçerli → otomatik yenileyen sistem önerilir
- Kullanıcı arayüzünde hata mesajlarını kullanıcı dostu yap
- Ruh hali → İngilizce kelimeler olarak Spotify’a gönderilmeli (`mutlu` değil, `happy`)

---

## 🧾 Özet

| Adım                  | Açıklama                                         |
|-----------------------|--------------------------------------------------|
| Spotify App Oluştur   | Client ID / Secret alınır                        |
| Token Al              | `api/token` endpoint’i ile                       |
| Şarkı Ara             | `search` endpoint’i, ruh haline göre             |
| Header Kullan         | `Authorization: Bearer <token>`                 |
| UI’ye Göster          | Şarkı bilgileri + Spotify linki + kapak görseli |
