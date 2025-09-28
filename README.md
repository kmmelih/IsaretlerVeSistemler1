# İşaretler Ve Sistemler Hafta 1 Notlar

### CT-benzeri temsil ne demek?

- CT = Continuous Time yani sürekli zamanlı sinyal.

- Bilgisayarda gerçekten sürekli çizgi çizmek mümkün değil. Onun yerine çok sık aralıklarla örnek alırsak, gözümüze sürekliymiş gibi görünür.

- Bu yüzden "CT-benzeri" deniyor → yani gerçek sürekli sinyalin sahte ama çok yakın bir kopyası.

```py
import numpy as np # matematiksel işlemler (mesela sin, pi, linspace gibi).
import matplotlib.pyplot as plt # grafik çizdirmek için.

def newfig():
    plt.figure() # yeni bir boş grafik penceresi açar.
```

```py
# "CT-benzeri" temsil
f = 440.0 # Sinyalimizin frekansı: 440 Hz (saniyede 440 titreşim)
T = 0.02 # Toplam süre: 0.02 saniye = 20 milisaniye.
fs_ct = 100_000 # Örnekleme frekansı: 100.000 Hz. Yani bilgisayar, bu sinyali göstermek için saniyede 100.000 tane nokta kullanacak.
t_ct = np.linspace(0, T, int(T*fs_ct), endpoint=False) # Burada zaman ekseni oluşturuluyor. 0 ile 0.02 saniye arası, toplam T * fs_ct kadar nokta var.
# endpoint=False -> son noktayı dahil etme (teknik sebep: düzgün tekrar için).
x_ct = np.sin(2*np.pi*f*t_ct) # Bu satır asıl sinüs dalgasını üretiyor. 2π*f*t → sinyalin formülü.

newfig() # yepyeni bir boş grafik açar.
plt.plot(t_ct, x_ct) # sinüs dalgasını çizer.

#Alttaki 3 satırda başlık ekleniyor, eksenlere isim veriliyor, kareli kılavuz çiziliyor.
plt.title("CT-benzeri temsil (yüksek örnekleme) — 440 Hz sinüs")
plt.xlabel("Zaman (s)"); plt.ylabel("Genlik")
plt.grid(True);

plt.show() # grafiği ekranda gösteriyor
```
> Sonuç: 20 ms boyunca 440 Hz sinüs dalgası.

### DT Temsiller (Discrete-Time Sinyaller)

- Önceki örneklerde CT-benzeri (Continuous-Time) sinyal çizmiştik: Çok yüksek örnekleme ile sinyal neredeyse sürekli görünüyordu.
- Şimdi DT (Discrete-Time) sinyaller çiziyoruz: Yani sinyal sadece belirli zaman noktalarında tanımlı.
- Bu, bilgisayarda gerçek sinyalleri işlemek için temel bir yaklaşımdır.

### Nyquist Teoremi

- Bir sinyali dijital olarak doğru şekilde temsil etmek istiyorsan, örnekleme frekansının sinyalin en yüksek frekansından en az iki kat yüksek olması gerekir.
- fs ​≥ 2fmax​
- fs → örnekleme frekansı (saniyede kaç örnek alıyoruz)
- fmax → sinyalin içerdiği en yüksek frekans
- Eğer sinyalin frekansı 440 Hz ise, örnekleme frekansı en az 2×440=880 Hz olmalı.

### Aliasing (Örnekleme Hatası)

- Eğer örnekleme frekansı yetersizse: fs​ < 2fmax​
- Sinyal yanlış temsil edilir, yüksek frekanslar daha düşük frekanslı bir sinyalmiş gibi görünür.
- Bu olaya aliasing denir.

> Nyquist → doğru temsil için minimum örnekleme frekansı.

> Aliasing → Nyquist koşulu sağlanmazsa ortaya çıkan “yanlış frekans görünümü”.

```py
# DT temsiller
for fs in [8000, 2000, 1200, 900]: # Bu döngü sinyali farklı örnekleme frekanslarıyla (8000 Hz, 2000 Hz, 1200 Hz, 900 Hz) oluşturup sırayla gösterecek.
    n = np.arange(0, int(T*fs)) # n = örnek indeksleri (0, 1, 2, 3, …) Kaç tane örnek alacağımızı T*fs belirliyor (süre × örnekleme hızı).
    t = n/fs # t = zaman eksenimiz. Örnek indekslerini, örnekleme frekansına bölersek zaman karşılıklarını buluruz.
    x = np.sin(2*np.pi*f*t) # Asıl sinyalimiz: sinüs dalgası. Bu sefer “sürekli gibi” değil, sadece n noktalarında tanımlı.
    newfig()
    plt.stem(t, x) # ayrık zaman sinyallerini çizer.
    plt.title(f"DT temsil — fs = {fs} Hz, f = {f} Hz")
    plt.xlabel("Zaman (s)"); plt.ylabel("Genlik")
    plt.grid(True); plt.show()
```
```
Sonuç: Dört ayrı grafik çıkacak, her biri farklı fs ile:
fs = 8000 Hz → 440 Hz sinüs çok net ve düzgün görünecek.
fs = 2000 Hz → hâlâ düzgün görünüyor, çünkü Nyquist kuralına göre (2×f = 880 Hz’ten büyük).
fs = 1200 Hz → sınırda, biraz bozulma hissedilmeye başlanır.
fs = 900 Hz → artık 440 Hz’i doğru temsil edemiyor → aliasing (yanlış frekans görünümü) ortaya çıkıyor.
```

### PPG nedir, neden benzetim yapıyoruz?

- PPG = Photoplethysmogram.

- Parmak ucuna taktığımız oksimetre cihazlarında veya akıllı saatlerde ölçülen kalp atışı sinyali.

- Temel mantık: Deriden geçen ışığın damarlardaki kan akışına göre değişmesi. Her kalp atışı → kısa süreli bir tepe (peak) oluşturur.

- PPG sinyali sürekli bir dalga gibi değil, belirgin darbeler (kalp atışları) şeklindedir.

### Zaman kaydırma (delay) nedir?

- Matematikte bir sinyal x(t) olsun. Eğer biz bunu D kadar geciktirirsek yeni sinyalimiz:​ (t)=x(t−D)

- Bu, grafikte sinyalin sağa kayması demektir.

- Eğer ileriye değil geriye kaydırırsak x(t+D) → bu da sinyalin sola kayması olur.

- Yani gecikme, sinyalin yapısını bozmadan sadece zaman ekseninde ötelenmesidir.

```py
# PPG-benzeri dalga
fs = 100.0
T = 6.0
t = np.arange(0, T, 1/fs)

hr_hz = 1.1 # bu yaklaşık olarak dakikada 66 kalp atışına karşılık geliyor (1.1 × 60 ≈ 66 BPM).
base = 0.1*np.sin(2*np.pi*0.2*t) # Küçük bir düşük frekanslı sinüs
ppg = np.zeros_like(t) # Başlangıçta sıfırlardan oluşan bir dizi (üzerine kalp atışlarını ekleyeceğiz).
for k in range(int(T*hr_hz)+5):
    tc = k/hr_hz # atışın zamanı.
    ppg += np.exp(-0.5*((t-tc)/(0.05))**2) # dar, ani bir tepe (kalbin atışı → Gaussian şekli).
    ppg += 0.2*np.exp(-(t-tc-0.03)/0.2)*(t>tc) # küçük bir kuyruk
# t>tc -> bu eklenme sadece kalp atışından sonra başlıyor.

x = 0.3 + base + 0.7*ppg # sinyalin son hali

# Orijinal
newfig(); plt.plot(t, x); plt.title("PPG-benzeri sinyal (orijinal)")
plt.xlabel("Zaman (s)"); plt.ylabel("Genlik"); plt.grid(True); plt.show()

# Zaman kaydırma (gecikme)
D = 0.4 # Burada sinyal 0.4 saniye geciktiriliyor.
x_delayed = np.interp(t-D, t, x, left=0, right=0) # enterpolasyon yaparak yeni değerleri hesaplıyor.
# left=0, right=0 -> gecikme yüzünden boş kalan baş/kuyruk değerleri sıfırla dolduruyor.
newfig(); plt.plot(t, x, label="orijinal"); plt.plot(t, x_delayed, label="gecikmiş")
plt.title("Zaman kaydırma örneği"); plt.xlabel("Zaman (s)"); plt.ylabel("Genlik")
plt.legend(); plt.grid(True); plt.show()
```

```
Sonuç:
İlk grafikte: Kalp atışına benzer PPG sinyali.
İkinci grafikte: Aynı sinyalin zaman kaydırılmış (gecikmiş) hali.
İkinci grafik: hem orijinal sinyal hem de 0.4 saniye gecikmiş hali aynı anda çiziliyor.
Gözle bakınca iki sinyalin aynı formda olduğunu ama biri ötekinden biraz sağa kaydığını görürsün.
Sinyali zaman ekseninde kaydırmak, sadece grafiğin sağa (veya sola) ötelenmesi demek.
```

### Genlik Ölçekleme nedir?

- Matematiksel olarak elimizde bir sinyal 𝑥(𝑡) olsun.
- Eğer biz bu sinyali bir katsayı 𝑎 ile çarparsak: 𝑦(𝑡)=𝑎⋅𝑥(𝑡)
- Buna genlik ölçekleme denir.
- Yani sinyalin şekli değişmez, sadece yüksekliği (genliği) değişir.
- Eğer 𝑎 > 1 → sinyal büyür (genliği artar).
- Eğer 0 < 𝑎 < 1 → sinyal küçülür (genliği azalır).
- Eğer 𝑎 < 0 → sinyal hem ters döner hem de ölçeklenir.

```py
def amp_scale(sig, a): # sinyali a katsayısı ile çarpıyor ve sonucu return ediyor.
    return a * sig

for a in [0.5, 1.0, 1.5]: # Üç farklı katsayı deniyoruz:
    newfig()
    plt.plot(t, x, label="orijinal")
    plt.plot(t, amp_scale(x, a), label=f"genlik a={a}")
    plt.title("Genlik ölçekleme örneği")
    plt.xlabel("Zaman (s)"); plt.ylabel("Genlik")
    plt.legend(); plt.grid(True); plt.show()
```

```
Sonuç:
a = 0.5 → sinyalin tepe noktaları daha düşük → sönmüş gibi.
a = 1.0 → sinyal tam olarak orijinal.
a = 1.5 → sinyal daha yüksek tepelere ulaşıyor → güçlenmiş gibi.
```

### Zaman ölçekleme nedir?

- Bir sürekli zamanlı sinyal 𝑥(𝑡) verildiğinde, zaman ölçekleme şu şekilde tanımlanır: 𝑦(𝑡)=𝑥(𝑎𝑡)
- Eğer 𝑎 > 1 → sinyal sıkıştırılır (daha hızlı görünür).
- Eğer 0 < 𝑎 < 1 → sinyal genişletilir (daha yavaş görünür).
- Eğer 𝑎 < 0 → sinyal zaman ekseninde ters çevrilir (aynadaki gibi).

```py

def time_scale(sig, t, a):
    # y(t) = x(a t). t' = a t. t üzerinde örnek almak için interpolasyon.
    if a == 1:
        return sig
    t_prime = a * t # zaman eksenini ölçekliyor
    return np.interp(t, t_prime, sig, left=0.0, right=0.0) # sinyalin yeni zaman eksenine göre örneklenmesini sağlıyor. Çünkü ölçekleme sonrası noktalar orijinal örnekleme ile tam çakışmaz. interpolasyon gerekir.
# left=0.0, right=0.0 -> dışarıya taşan değerler sıfır yapılır (sinyalin sınırları dışında “0” kabul edilir).
for a in [0.7, 1.0, 2.4]:
    newfig()
    plt.plot(t, x, label="orijinal")
    plt.plot(t, time_scale(x, t, a), label=f"zaman ölçek a={a}")
    plt.title("Zaman ölçekleme — PPG-benzeri sinyal")
    plt.xlabel("Zaman (s)"); plt.ylabel("Genlik")
    plt.legend(); plt.grid(True); plt.show()
```

```
Sonuç:
a = 0.7 → genişletme (sinyal yavaşlamış gibi.)
a = 1.0 → hiçbir değişiklik yok (orijinal sinyal).
a = 2.4 → sıkıştırma (sinyal hızlanmış gibi.)
```

### Çift ve tek bileşenler

- Bir sinyal 𝑥[𝑛] genellikle çift (even) ve tek (odd) bileşenlere ayrılabilir: 𝑥[𝑛]=𝑥𝑒​[𝑛]+𝑥𝑜​[𝑛]
- Çift bileşen (𝑥𝑒[𝑛]) simetriktir: 𝑥𝑒​[𝑛]=(𝑥[𝑛]+𝑥[−𝑛])/2​
- Tek bileşen (𝑥𝑜[𝑛]) antisimetri gösterir: 𝑥𝑜[𝑛]=(𝑥[𝑛]-𝑥[−𝑛])/2​​
- 𝑥𝑒[−𝑛]=𝑥𝑒[𝑛]
- 𝑥𝑜[−𝑛]=−𝑥𝑜[𝑛]
- Her sinyalin 𝑥[𝑛] toplamı bu iki bileşenle bulunur.

```py

N = 10
n = np.arange(-N, N+1) # sinyalin indeksi -10’dan 10’a kadar.
xseq = np.sin(0.4*np.pi*n) + 0.3*(n==0) # örnek sinyal: Temel sinüs dalgası: sin(0.4𝜋𝑛)
# + 0.3*(n==0) → sadece n=0’da 0.3 ekleniyor

def x_of(idx):
    # dışındaki değerlere 0 uzatma
    if -N <= idx <= N: # sinyalin dışına çıkınca 0 döndürüyor.
        return float(xseq[idx+N])
    return 0.0

xe = np.array([0.5*(x_of(int(ni)) + x_of(int(-ni))) for ni in n]) # Çift bileşen
xo = np.array([0.5*(x_of(int(ni)) - x_of(int(-ni))) for ni in n]) # Tek bileşen

newfig(); plt.stem(n, xseq); plt.title("Orijinal dizi x[n]")
plt.xlabel("n"); plt.ylabel("Genlik"); plt.grid(True); plt.show()

newfig(); plt.stem(n, xe); plt.title("Çift bileşen x_e[n]")
plt.xlabel("n"); plt.ylabel("Genlik"); plt.grid(True); plt.show()

newfig(); plt.stem(n, xo); plt.title("Tek bileşen x_o[n]")
plt.xlabel("n"); plt.ylabel("Genlik"); plt.grid(True); plt.show()

newfig(); plt.stem(n, xseq - (xe+xo)); plt.title("Doğrulama: x - (x_e + x_o)")
plt.xlabel("n"); plt.ylabel("Hata"); plt.grid(True); plt.show()
```
```
Sonuç:
x[n] → karma sinyal
x_e[n] → simetrik, yansıdığı zaman aynı kalır
x_o[n] → yansıdığı zaman işaret değiştirir (ters döner)
Orijinal = çift + tek
```

### Kısa Değerlendirme Soruları
1. **CT ve DT temsillerinde temel fark nedir?**

- CT (Continuous-Time): Sinyal sürekli zaman fonksiyonudur, her an için bir değeri vardır.
- DT (Discrete-Time): Sinyal sadece belirli zaman noktalarında tanımlıdır (örneklenmiş).
- Özet: CT → sürekli, DT → örneklenmiş ve ayrık.

2. **PPG-benzeri sinyalde zaman ölçekleme (a) kalp hızı yorumunu nasıl etkiler?**

- Zaman ölçekleme: y(t)=x(at)
- a > 1 → sinyal sıkışır (kalp hızı artmış gibi görünür)
- 0 < a < 1 → sinyal genişler (kalp hızı azalmış gibi görünür)
- Yani zaman ölçekleme, kalp atışının hızını simüle eder.

3. **Zaman kaydırmasının matematiksel karşılığı nedir (DT için)?**

- T sinyali 𝑥[𝑛] için gecikme 𝐷 birim: 𝑦[𝑛]=𝑥[𝑛−𝐷]
- Grafiksel olarak sinyal sağa kaymış olur.
- Negatif gecikme olursa sola kayar.

4. **Tek/çift ayrımında x[n] = x_e[n] + x_o[n] özelliğini nasıl test ettik?**

- Hesaplama: 𝑥𝑒​[𝑛]=(𝑥[𝑛]+𝑥[−𝑛])/2 ve 𝑥𝑜[n]=(𝑥[𝑛]-𝑥[−𝑛])/2​​
- Doğrulama: hata = 𝑥[𝑛]−(𝑥e​[𝑛]+𝑥𝑜​[𝑛])≈0
- Grafikte bu hata sıfıra yakın çıktı (test başarılı)

5. **Ses örneğinde aliasing’i gözlemlemek için neler yapabilirsiniz?**

- Örnekleme frekansını düşürüp grafiği çizmek.
- Yüksek frekanslı sinüsleri düşük örnekleme ile çizmek → dalga yanlış frekansta görünür.
- Alias oluşursa sinyalin “daha yavaş veya ters yönlü” hareket ettiğini görürsünüz.
- Nyquist kriterine uymayan durumlarda aliasing gözlemlenir.
