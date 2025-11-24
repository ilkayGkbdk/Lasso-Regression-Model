# Bitcoin Fiyat Tahmini - Lasso Regression ile Feature Selection

**Veri Madenciliği Güz Dönemi Vize Ödevi**  
**Öğrenci:** İlkay Gökbudak

---

## 📌 Ödev Konusu

Bu projede Bitcoin (BTC-USD) fiyatlarını tahmin etmek için **Lasso Regression** algoritmasını kullandım. Ödevin ana amacı **Feature Selection** yöntemlerini uygulamak ve **L1 Regularization**'ın gücünü göstermektir.

## 🎯 Neden Lasso Regression?

Lasso Regression'ı seçmemin temel sebepleri:

1. **L1 Regularization (Lasso'nun Kalbi)**
   - Gereksiz özelliklerin katsayısını TAM OLARAK sıfıra indirir
   - L2'den farklı olarak özellikleri tamamen eleme yeteneği var
   - Maliyet fonksiyonu: `MSE + alpha * Σ|coefficients|`

2. **Otomatik Feature Selection**
   - Manuel seçim gerektirmez
   - Model eğitilirken aynı anda özellik seçimi yapar
   - Hangi özelliklerin önemli olduğunu katsayılardan anlayabiliriz

3. **Overfitting Önleme**
   - Düzenlileştirme sayesinde modeli genelleştirir
   - Çok fazla özellik kullanımından kaynaklanan ezberlemeyi önler

4. **Yorumlanabilirlik**
   - Sıfır olmayan katsayılar → önemli özellikler
   - Sıfır olan katsayılar → elenen özellikler
   - Modeli açıklamak kolay

## 📊 Kullanılan Feature Selection Yöntemleri

Projede 3 farklı feature selection yöntemi uyguladım:

### 1. **Correlation-Based Selection** (Ön Filtreleme)
- Hedef değişkenle korelasyonu düşük özellikleri eliyorum
- Eşik değer: 0.7
- Mantık: Fiyatla ilişkisi zayıf özellikler gereksizdir

### 2. **Variance Threshold** (İstatistiksel Filtreleme)
- Varyansı düşük özellikleri çıkarıyorum
- Mantık: Az değişen özellikler bilgi taşımaz
- Normalize edilmiş veriler üzerinde uygulanır

### 3. **Lasso (L1 Regularization)** ⭐ **ANA YÖNTEM**
- Model eğitimi sırasında otomatik feature selection
- Alpha (λ) parametresi: düzenlileştirme gücü
- Cross-validation ile en iyi alpha değeri bulunur
- Katsayısı 0 olan özellikler elenir

## 🗂️ Veri Seti

- **Kaynak:** Yahoo Finance API (yfinance kütüphanesi)
- **Veri:** BTC-USD günlük OHLCV verileri
- **Zaman Aralığı:** Son 2 yıl (730 gün)
- **Özellikler:** Lag değerleri (son 30 günün fiyatları)

### Neden Lag Özellikleri?
Time series tahmininde geçmiş değerler gelecek tahmin etmek için kullanılır:
- `lag_1` = Dünün fiyatı
- `lag_2` = 2 gün önceki fiyat
- ...
- `lag_30` = 30 gün önceki fiyat

## 📁 Proje Yapısı

```
model_2/
├── main.ipynb                    # Ana notebook (BURASI ÖNEMLİ!)
├── README.md                     # Bu dosya
├── btc_30_day_forecast.csv      # Gelecek tahminleri
├── model_performans_analizi.png # Performans grafikleri
└── future_forecast_plot.png     # Tahmin görselleştirmesi
```

## 🚀 Nasıl Çalıştırılır?

### Gerekli Kütüphaneler

```bash
pip install pandas yfinance numpy matplotlib scikit-learn seaborn
```

### Çalıştırma

1. Jupyter Notebook'u aç:
```bash
jupyter notebook main.ipynb
```

2. Tüm hücreleri sırayla çalıştır (Cell > Run All)

3. Sonuçları gözlemle!

## 📈 Adım Adım İş Akışı

### 1. Veri Çekme
- Yahoo Finance'ten son 2 yıllık Bitcoin verisi
- OHLCV (Open, High, Low, Close, Volume) verileri

### 2. Özellik Mühendisliği
- 30 adet lag özelliği oluşturma
- Her günün geçmiş 30 günlük fiyatlarını özellik olarak kullanma

### 3. Feature Selection Aşama 1: Correlation
- 30 özelliğin Close ile korelasyonunu hesapla
- Korelasyonu > 0.7 olanları seç
- Gereksiz özellikleri ele

### 4. Feature Selection Aşama 2: Variance Threshold
- Seçilen özellikler üzerinde varyans kontrolü
- Düşük varyanslı özellikleri çıkar

### 5. Train-Test Split
- %80 eğitim, %20 test (time series için uygun)
- Kronolojik sıra korunur (shuffle yok!)

### 6. Veri Ölçeklendirme
- StandardScaler ile normalize etme
- Ortalama=0, Standart Sapma=1

### 7. Lasso Model Eğitimi ⭐
- LassoCV ile en iyi alpha bulma
- TimeSeriesSplit cross-validation (5-fold)
- L1 regularization ile otomatik feature selection

### 8. Model Değerlendirme
- R², MAE, RMSE, MAPE metrikleri
- Train-Test karşılaştırması
- Overfitting kontrolü
- Naive baseline ile kıyaslama

### 9. Görselleştirme
- Gerçek vs Tahmin grafikleri
- Scatter plot (regresyon performansı)
- Residual (hata) dağılımı
- Lasso katsayıları (feature importance)

### 10. Gelecek Tahmini
- Recursive forecasting ile 30 gün tahmin
- Sliding window yaklaşımı

## 📊 Beklenen Sonuçlar

### Feature Selection Performansı
- **Başlangıç:** 30 özellik
- **Correlation sonrası:** ~20-25 özellik
- **Variance sonrası:** ~15-20 özellik
- **Lasso sonrası:** ~5-10 özellik (sıfır olmayan katsayılar)
- **İndirgeme oranı:** %60-70

### Model Performansı
- **R² Score:** 0.85-0.95 (yüksek açıklayıcılık)
- **MAE:** ~$1,500-3,000 (Bitcoin volatilitesine göre)
- **MAPE:** %2-5 (düşük yüzdesel hata)
- **Naive Baseline'dan iyi:** ✓

## 🔍 Önemli Çıktılar

### 1. Feature Selection Başarısı
- Lasso'nun L1 regularization ile hangi özellikleri seçtiği
- Gereksiz özelliklerin elenmesi
- Model karmaşıklığının azalması

### 2. Grafikler
- `model_performans_analizi.png`: 4 panel (tahmin, scatter, residual, katsayılar)
- `future_forecast_plot.png`: Son 90 gün + 30 günlük tahmin

### 3. Tahmin Dosyası
- `btc_30_day_forecast.csv`: Gelecek 30 günün tahminleri

## 💡 Öğrendiklerim

1. **L1 vs L2 Regularization Farkı**
   - L1 (Lasso): Katsayıları TAM sıfır yapar → Feature selection
   - L2 (Ridge): Katsayıları küçültür ama sıfır yapmaz

2. **Feature Selection'ın Önemi**
   - Daha basit model = daha iyi genelleme
   - Gereksiz özellikler overfitting'e sebep olur
   - Yorumlanabilirlik artar

3. **Time Series Split**
   - Zaman serilerinde rastgele split yapılmaz
   - Geçmiş → gelecek kronolojisi korunmalı
   - TimeSeriesSplit cross-validation şart

4. **Recursive Forecasting**
   - Uzun vadede hata birikir
   - Güven aralıkları genişler
   - Kısa vadeli tahminler daha güvenilir

## ⚠️ Sınırlamalar ve İyileştirmeler

### Sınırlamalar
- Sadece geçmiş fiyatlar kullanıldı (teknik göstergeler yok)
- External faktörler yok (haberler, ekonomik göstergeler)
- Recursive forecasting'de hata birikimi
- Market crash gibi beklenmedik olaylar tahmin edilemez

### Gelecekte Eklenebilecekler
- Daha fazla teknik gösterge (RSI, MACD, Bollinger Bands)
- Volume analizi
- Makro ekonomik veriler
- Sentiment analysis
- Ensemble modeller (birden fazla model kombinasyonu)

## 📚 Kaynaklar

- **Scikit-learn Dokumentasyonu:** [https://scikit-learn.org/](https://scikit-learn.org/)
- **Lasso Regression:** [https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.Lasso.html](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.Lasso.html)
- **Feature Selection:** [https://scikit-learn.org/stable/modules/feature_selection.html](https://scikit-learn.org/stable/modules/feature_selection.html)
- **Time Series Cross-Validation:** [https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.TimeSeriesSplit.html](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.TimeSeriesSplit.html)

## ⚠️ Önemli Not

**Bu proje sadece eğitim amaçlıdır!**

- Gerçek yatırım kararları için kullanılmamalıdır
- Kripto para piyasaları çok volatildir
- Model tahminleri garanti değildir
- Yatırım yapmadan önce profesyonel danışmanlık alın

---

**Son Güncelleme:** Kasım 2025  
**Ders:** Veri Madenciliği  
**Ödev Türü:** Vize Projesi
