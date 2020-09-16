## Keras ile Nesne Tanıma Uygulamaları

<span class="s"></span>

# Keras ile Nesne Tanıma Uygulamaları


Elinizdeki görsellerden nesne tanıma yapmak istiyorsunuz fakat görüntü işleme tekniklerine aşina değil misiniz? Gelin şimdi çok basit ve pratik bir şekilde görsellerimizdeki nesneleri tanımlamayı görelim.

<noscript><img alt="Image for post" class="t u v ck ak" src="https://miro.medium.com/max/1500/1*v64BvvzbeJvhSen1X7IPNA.jpeg" width="750" height="300" srcSet="https://miro.medium.com/max/552/1*v64BvvzbeJvhSen1X7IPNA.jpeg 276w, https://miro.medium.com/max/1104/1*v64BvvzbeJvhSen1X7IPNA.jpeg 552w, https://miro.medium.com/max/1280/1*v64BvvzbeJvhSen1X7IPNA.jpeg 640w, https://miro.medium.com/max/1400/1*v64BvvzbeJvhSen1X7IPNA.jpeg 700w" sizes="700px"/></noscript>

Kaynak: [http://www.image-net.org](http://www.image-net.org)

Gerçekleştireceğimiz proje nesne tanıma çalışmalarımız için süreci o kadar kolaylaştıracak ki, proje aşamasına geçmeden vereceğim bilgilere dahi göz atmadan uygulamayı kullanabileceksiniz, fakat ne yaptığımızı veya nasıl yaptığımızı görmek adına gerekli tüm bilgileri aktaracağım.

Projenin tamamlanmasından sonra diğer ağ yapılarını da nasıl sadece birkaç ufak değişiklikle çalıştırabileceğinizi de anlatacağım.


<noscript><img alt="Image for post" class="t u v ck ak" src="https://miro.medium.com/max/2400/1*wwnExqe720PPHykHhs5Hqw.png" width="1200" height="348" srcSet="https://miro.medium.com/max/552/1*wwnExqe720PPHykHhs5Hqw.png 276w, https://miro.medium.com/max/1104/1*wwnExqe720PPHykHhs5Hqw.png 552w, https://miro.medium.com/max/1280/1*wwnExqe720PPHykHhs5Hqw.png 640w, https://miro.medium.com/max/1400/1*wwnExqe720PPHykHhs5Hqw.png 700w" sizes="700px"/></noscript>


Keras, en kısa tanımıyla bir Derin Öğrenme kütüphanesidir. Yapı olarak TensorFlow ve Theano kütüphanelerini kullanarak çalışır, bu kütüphanelerin zorlayıcı yazım biçimlerine nazaran işlemlerinizi daha hızlı ve basitçe gerçekleştirmeye yardımcı fonksiyonları içerir.

Keras’ın uzun zamandır “Derin Öğrenme” uygulamalarında hem modellerimizin prototiplerini gerçekleştirirken hem de öğrenme aşamasında bize ne kadar yardımcı olduğunu göz ardı etmek imkansız bir hale geldi. Katmanların ince ayarlarını, hiper-parametrelerini, modelin oluşturulması ve veri setlerinin indirilmesinden tutun girdileri sinir ağına uygulayacağımız hale getirilmesine dek her an yanımızda. Bugün sizlere dünya çapında ve akademik alanda büyük başarılar sağlamış yapıları nasıl basitçe kullanabileceğinizi anlatacağım.

Çalışmaya geçmeden önce bildirmek isterim ki -siz de bunu rahatlıkla göreceksiniz- Python bilginizin temel düzeyde olmasına bile gerek duymayacaksınız.

# Görüntü İşleme Teknikleri

Deep Learning Türkiye takipçilerinin bildiği gibi “Görüntü İşleme” ve “Bilgisayarlı Görü” sistemleri, her geçen gün popülaritesi katlanarak artan alanlardan biri. Kamera sistemlerimizin, depolama alanlarımızın ve donanımlarımızın teknolojisi arttıkça, dijital gözlerimizin yeteneklerini optimize etmek gerçekten harika işlere imza atmamızı sağlıyor.

Projemize geçmeden önce bu alandaki bilgi birikimimizi artırmak adına, hem de okurlarımızı hazır yakalamışken onlara bu alandan daha detaylı bahsetmek istiyorum fakat hemen projeye geçmek istiyorum diyorsanız bu bölümü hızlıca geçebilirsiniz.

## Kim Bu Adam?

![Image for post](https://miro.medium.com/max/44/1*yZJkoC2KCJuUHACmsbkh6g.jpeg?q=20)

<noscript><img alt="Image for post" class="t u v ck ak" src="https://miro.medium.com/max/552/1*yZJkoC2KCJuUHACmsbkh6g.jpeg" width="276" height="371"/></noscript>

Alfred Haar

Alfred Haar, görüntü işleme tekniklerinin önünün açılması konusunda belki de en büyük matematikçilerden biri.

Görüntü işleme tekniklerine aşinaysanız Haar-Cascade veya diğer algoritmalarda “Haar-like Features” olarak duyduğunuz Haar aslında Alfred Haar.

Mükemmel, şimdi biraz daha derine inerek anlaşılır bir bağlantı kuralım.

Derin öğrenme algoritmalarını görüntü işleme tekniklerinde kullanmadan önce, bu problemi çözmek adına uğraşan birçok bilim adamı vardı. Belki de Alfred bu bulduğu tekniğin ne kadar büyük çalışmalara yahut bugün geldiğimiz düzeye erişebileceğini hayal dahi etmemişti fakat bildiğimiz bir şey var ki o da, teknik bir kez ortaya atıldığında “Homo-Sapiens” bilineni korumayı ve bunu ilerletmeyi çok iyi beceriyor.

Haar Hocamızın, Haar Dalgacık Dönüşümü (Haar Wavelets) yöntemini, kısacık 48 yıllık hayatında bize bahşettikten sonra 2001 yılında bu özellikleri kullanarak iki büyük bilim adamı Paul Viola ve Michael Jones ilk yüz tanıma algoritmasını geliştirdiler.

## Haar Benzeri Özellikler (Haar-like Features)

Haar benzeri özellikleri kabaca 3 ana bölgeye ayırabiliriz.

*   Kenar Özellikleri
*   Çizgi Özellikleri
*   Dört Kare Özellikleri

![Image for post](https://miro.medium.com/max/60/1*hbFPsfsCqV8rf1MV8b8p5w.jpeg?q=20)

<noscript><img alt="Image for post" class="t u v ck ak" src="https://miro.medium.com/max/640/1*hbFPsfsCqV8rf1MV8b8p5w.jpeg" width="320" height="271" srcSet="https://miro.medium.com/max/552/1*hbFPsfsCqV8rf1MV8b8p5w.jpeg 276w, https://miro.medium.com/max/640/1*hbFPsfsCqV8rf1MV8b8p5w.jpeg 320w" sizes="320px"/></noscript>

OpenCV Documentation — Face Detection

Evet, şimdi kurmak istediğim bağlantıya geri dönelim. Burada bağlantı kurmanız açısından insan yüzü tanımlamak istediğimizi düşünelim, Haar benzeri özellikler gördüğünüz gibi bir insan yüzü tanımlamak istediğimizde bize bu özelliklerin olduğu bölgelerin tespitini yapmamızı kolaylaştırıyor. Bu sayede aradığımız görselde bir insan yüzü olup olmadığını anlayabiliyoruz.

![Image for post](https://miro.medium.com/max/60/1*64MTUF8nuEvSgBvYmOfhKA.png?q=20)

<noscript><img alt="Image for post" class="t u v ck ak" src="https://miro.medium.com/max/668/1*64MTUF8nuEvSgBvYmOfhKA.png" width="334" height="203" srcSet="https://miro.medium.com/max/552/1*64MTUF8nuEvSgBvYmOfhKA.png 276w, https://miro.medium.com/max/668/1*64MTUF8nuEvSgBvYmOfhKA.png 334w" sizes="334px"/></noscript>

OpenCV Documentation — Face Detection

Belki dikkatinizi çekmiştir, bu teknikler bazı dezavantajlara sebep olabiliyor, her ne kadar halen güçlü ve kullanılabilir bir algoritma olsa da, insan yüzünün bulunduğu görsel üzerinde gölge oluşması, yüzün ilgili görselde farklı açılarda olması gibi durumlarda algoritmamız pek de iyi sonuç vermeyebilir. Şimdi birlikte bu sorunu nasıl çözdüğümüze bakalım. Artık işin eğlenceli kısmına yavaş yavaş yaklaşmaya başladık. Daha da derine inip “Derin Ağları” göreve çağırıyoruz.

# Evrişimsel Sinir Ağları (Convolutional Neural Networks)

Bu bölümde projemizde kullanacağımız ağın yapısından bahsedeceğim, bunun dışında kullanılan birçok farklı yöntem var, aynı zamanda çalışmada Keras içerisinde bulunan diğer yöntemleri nasıl kolayca çağırabileceğinizi ve kullanabileceğinizi de anlatacağım. Evrişimsel Sinir Ağlarında daha da fazla bilgi almak istiyorsanız Deep Learning Türkiye Medium sayfamızda [Ayyüce Kızrak](/@ayyucekizrak)’ın mükemmel bir [yazısı](/deep-learning-turkiye/deri̇ne-daha-deri̇ne-evrişimli-sinir-ağları-2813a2c8b2a9) var.

# Tarihi 1998 Yılına Alalım

Yann LeCun ve ekibi 1998 yılında yayınladığı makale, görüntü işleme yöntemlerinin nasıl Derin Ağlar üzerinde kullanabileceğini anlattı. LeNet-5 olarak adlandırılan bu yapı ilk başarılı sonucu veren evrişimli sinir ağı olmuştur.

![Image for post](https://miro.medium.com/max/60/1*nQ9oyvewy_c5-2bK9-UtOg.png?q=20)

<noscript><img alt="Image for post" class="t u v ck ak" src="https://miro.medium.com/max/4000/1*nQ9oyvewy_c5-2bK9-UtOg.png" width="2000" height="553" srcSet="https://miro.medium.com/max/552/1*nQ9oyvewy_c5-2bK9-UtOg.png 276w, https://miro.medium.com/max/1104/1*nQ9oyvewy_c5-2bK9-UtOg.png 552w, https://miro.medium.com/max/1280/1*nQ9oyvewy_c5-2bK9-UtOg.png 640w, https://miro.medium.com/max/1400/1*nQ9oyvewy_c5-2bK9-UtOg.png 700w" sizes="700px"/></noscript>

Gradient-Based Learning Applied to Document Recognition , Yann LeCun — [http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf](http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf)

Sonrasında durmayıp tekniklerimizi geliştirmeye devam ettik ve bugün size projemizde kullanacağımız yapıya ve gayet iyi sonuçlar veren modele nihayet geldik.

# VGG-16

Burada anlatmak üzere olduğumuz model anlaşılması diğer modellere nazaran daha basit bir yapı, görselin uzun olması umarım sizi korkutmamıştır. Dikkat ederseniz bloklar kendilerini birkaç kez tekrar ediyor. Bunu yapmamızdaki en büyük amaç girişimizdeki görselimizi daha küçük boyutlu matrislere küçültmek.

![Image for post](https://miro.medium.com/max/42/1*9pjrPQgh7d4ogTHHrWUI_w.png?q=20)

<noscript><img alt="Image for post" class="t u v ck ak" src="https://miro.medium.com/max/1800/1*9pjrPQgh7d4ogTHHrWUI_w.png" width="900" height="1260" srcSet="https://miro.medium.com/max/552/1*9pjrPQgh7d4ogTHHrWUI_w.png 276w, https://miro.medium.com/max/1000/1*9pjrPQgh7d4ogTHHrWUI_w.png 500w" sizes="500px"/></noscript>

VGG-16 Mimarisi — [https://arxiv.org/abs/1409.1556](https://arxiv.org/abs/1409.1556)

Giriş katmanımızda görselimizi aldıktan sonra, bol bol evrişimsel (Convolutional) katmandan geçiriyoruz.

Sonrasında maksimum ortaklama (Max-Pooling) yapıyoruz ve bu döngüyü tekrar ediyoruz.

Elde ettiğimiz daha küçük matrisimizi düzleştirdikten sonra, iki kez tam bağlantı katmanından (Fully-Connected Layer) geçirip, çıkış katmanımıza bağlıyoruz.

Fakat yapacağımız çalışmada bunlara hiç ihtiyacımız olmayacağından emin olabilirsiniz.

# Başlayalım!

O halde başlayalım, önceden bahsettiğim gibi hemen hemen hiçbir şeye ihtiyacınız yok, bilgisayarınızda Python ve Keras kurulu olduğunu kabul ederek devam edelim.

Tüm yapacağımız işlem bu kod blokunu çalıştırmak olacak. Gelin şimdi burada ne yapıyoruz inceleyelim.

## Kütüphanelerin Eklenmesi

Her zaman olduğu gibi ilk önce kullanacağımız kütüphaneler ile başlıyoruz.

Öncelikle Keras’a VGG16 modülünü ekleyeceğimizi söyleyelim. Sonrasında görüntümüz üzerinde ön işlem yapacağımızı ve tahminlerini basitçe yazdırmamız için gerekli fonksiyonları da ekliyoruz. Giriş verimiz üzerine modele uygulamak adına basit matris işlemleri yapmak için NumPy modülünü de çağırıyoruz.

Modeli yeniden eğiterek vakit kaybetmemek adına, daha öncesinde eğitilmiş modelin ağırlıklarını alıyoruz. Görselimizin adresini img_path adlı değişkene aktardıktan sonra, boyutlarını VGG16 modeline uygun hale getiriyoruz. Eksenlerimizi ayarladıktan sonra süreç tamamlanmış oluyor. Artık tahmin aşamasına geçebiliriz.

# Tahmin (Prediction)

Bu zamana kadar kodumuzu bilgisayarımıza kopyalayıp, bulunduğu dizine tahmin yapmak istediğimiz görselleri eklemek dışında hiçbir şey yapmadık. Keras’ın sürecimizi ne kadar kolaylaştırdığını ve ne kadar hız kazandırdığını gördük, pekala şimdi kodumuzu çalıştıralım ve sonuçlarına bir göz atalım.

Son satırda en iyi 3 tahmini yazdırmasını istedik. İlk testi internet üzerinden bulduğum bir kuş fotoğrafıyla yapıyorum.

![Image for post](https://miro.medium.com/max/60/1*kB3AxETvdKQZ47HhHxSMLA.jpeg?q=20)

<noscript><img alt="Image for post" class="t u v ck ak" src="https://miro.medium.com/max/2400/1*kB3AxETvdKQZ47HhHxSMLA.jpeg" width="1200" height="635" srcSet="https://miro.medium.com/max/552/1*kB3AxETvdKQZ47HhHxSMLA.jpeg 276w, https://miro.medium.com/max/1104/1*kB3AxETvdKQZ47HhHxSMLA.jpeg 552w, https://miro.medium.com/max/1280/1*kB3AxETvdKQZ47HhHxSMLA.jpeg 640w, https://miro.medium.com/max/1400/1*kB3AxETvdKQZ47HhHxSMLA.jpeg 700w" sizes="700px"/></noscript>

![Image for post](https://miro.medium.com/max/60/1*WqMpGSV3dA-t7h-UrTzBxw.jpeg?q=20)

<noscript><img alt="Image for post" class="t u v ck ak" src="https://miro.medium.com/max/1574/1*WqMpGSV3dA-t7h-UrTzBxw.jpeg" width="787" height="85" srcSet="https://miro.medium.com/max/552/1*WqMpGSV3dA-t7h-UrTzBxw.jpeg 276w, https://miro.medium.com/max/1104/1*WqMpGSV3dA-t7h-UrTzBxw.jpeg 552w, https://miro.medium.com/max/1280/1*WqMpGSV3dA-t7h-UrTzBxw.jpeg 640w, https://miro.medium.com/max/1400/1*WqMpGSV3dA-t7h-UrTzBxw.jpeg 700w" sizes="700px"/></noscript>

## Farklı Bir Görsel Üzerine Deneyelim

Burada tek yapmamız gereken modele göndereceğimiz görseli değiştirmek olacak.


```
img_path = ‘images/bird.jpg’</span>
```


olarak yazdığım satırı,


```
img_path = ‘images/cup.jpg’</span>
```


şeklinde değiştiriyorum ve kodumu tekrar çalıştırıyorum.

![Image for post](https://miro.medium.com/max/60/1*IedZ4jLbIduhHw0YVYR9LQ.jpeg?q=20)

<noscript><img alt="Image for post" class="t u v ck ak" src="https://miro.medium.com/max/3934/1*IedZ4jLbIduhHw0YVYR9LQ.jpeg" width="1967" height="1772" srcSet="https://miro.medium.com/max/552/1*IedZ4jLbIduhHw0YVYR9LQ.jpeg 276w, https://miro.medium.com/max/1104/1*IedZ4jLbIduhHw0YVYR9LQ.jpeg 552w, https://miro.medium.com/max/1280/1*IedZ4jLbIduhHw0YVYR9LQ.jpeg 640w, https://miro.medium.com/max/1400/1*IedZ4jLbIduhHw0YVYR9LQ.jpeg 700w" sizes="700px"/></noscript>

![Image for post](https://miro.medium.com/max/60/1*JrZom2FfLNmrNk6i6BapmQ.jpeg?q=20)

<noscript><img alt="Image for post" class="t u v ck ak" src="https://miro.medium.com/max/1578/1*JrZom2FfLNmrNk6i6BapmQ.jpeg" width="789" height="76" srcSet="https://miro.medium.com/max/552/1*JrZom2FfLNmrNk6i6BapmQ.jpeg 276w, https://miro.medium.com/max/1104/1*JrZom2FfLNmrNk6i6BapmQ.jpeg 552w, https://miro.medium.com/max/1280/1*JrZom2FfLNmrNk6i6BapmQ.jpeg 640w, https://miro.medium.com/max/1400/1*JrZom2FfLNmrNk6i6BapmQ.jpeg 700w" sizes="700px"/></noscript>

Öncesinde eğitilmiş modelleri Keras ile nasıl kolayca kullanabileceğimizi gördük. Fakat başka bir model kullanmak istiyorsak ne yapacağız hiç vakit kaybetmeden devam edelim.

# Farklı Modeller ile Çalışmak

Keras’ın yetenekleri burada bitmiyor, içerisinde farklı modelleri kullanabileceğimiz fonksiyonları var. Keras’ın dokümantasyonuna giderek neler varmış bakalım.

Keras/Applications bölümüne gidelim. [Buradan](https://keras.io/applications/) hızlıca ilgili bölüme gidebilirsiniz.

![Image for post](https://miro.medium.com/max/60/1*DNrFJ12scEVf48VTUW8uWg.jpeg?q=20)

<noscript><img alt="Image for post" class="t u v ck ak" src="https://miro.medium.com/max/1232/1*DNrFJ12scEVf48VTUW8uWg.jpeg" width="616" height="352" srcSet="https://miro.medium.com/max/552/1*DNrFJ12scEVf48VTUW8uWg.jpeg 276w, https://miro.medium.com/max/1104/1*DNrFJ12scEVf48VTUW8uWg.jpeg 552w, https://miro.medium.com/max/1232/1*DNrFJ12scEVf48VTUW8uWg.jpeg 616w" sizes="616px"/></noscript>

[https://keras.io/applications/#available-models](https://keras.io/applications/#available-models)

Gördüğünüz gibi farklı modellere sahibiz, dokümantasyona giderek daha ayrıntılı şekilde bakabilirsiniz. Her model için boyut, doğruluk ve parametre sayısı gibi bilgiler verilmiş.

![Image for post](https://miro.medium.com/max/60/1*0yqfNJXflniYqTsayF_8FQ.jpeg?q=20)

<noscript><img alt="Image for post" class="t u v ck ak" src="https://miro.medium.com/max/1166/1*0yqfNJXflniYqTsayF_8FQ.jpeg" width="583" height="527" srcSet="https://miro.medium.com/max/552/1*0yqfNJXflniYqTsayF_8FQ.jpeg 276w, https://miro.medium.com/max/1104/1*0yqfNJXflniYqTsayF_8FQ.jpeg 552w, https://miro.medium.com/max/1166/1*0yqfNJXflniYqTsayF_8FQ.jpeg 583w" sizes="583px"/></noscript>

[https://keras.io/applications/](https://keras.io/applications/)

Çalışmamızı VGG16 üzerinde yapmıştık, şimdi bir de hızlıca ResNet üzerinden nasıl yapacağımıza bakalım.

## ResNet ile Çalışmak

Süreç çok basit kodumuzda birkaç ufak değişiklik yapalım ve yeniden çalıştıralım.

Neleri değiştirdiğimize hızlıca bakalım, endişe etmeye gerek yok sadece üç satır üzerinde VGG16 yazdığımız yerlere ResNet yazacağız.


```
from keras.applications.vgg16 import VGG16            #VGG16
from keras.applications.resnet50 import ResNet50      #ResNet50</span>
```





```
#VGG16
from keras.applications.resnet50 import preprocess_input, decode_predictions</span><span id="e4e9" class="dq ks jw fg mp b gh nq nr ns nt nu mr s ms">#ResNet50
from keras.applications.vgg16 import preprocess_input, decode_predictions</span>
```


* * *


```
model = VGG16(weights='imagenet')        #VGG16
model = ResNet50(weights='imagenet')     #ResNet50</span>
```


# Sonuç

Bu kadar basit ve hızlı bir çalışmayı bu kadar uzun bir yazıda anlatacağımı hiç düşünmezdim fakat programları çalıştırmaktan ziyade içeride neler döndüğünü ve bu teknolojiyi geliştirirken geçilen yolları öğrenmeyi seviyorum, umarım siz de keyif almışsınızdır.

Daha önce eğitilen ve güzel sonuçlar veren yöntemleri kullanmayı öneriyorum. Modelinizi sıfırdan eğitmek yerine büyük veri setlerinde ve güçlü donanımlar ile uzun sürelerde eğitilmiş modellerin ağırlıklarını alarak, çalışmanız dahilinde kendi veri setiniz ile yeniden eğitebilirsiniz.

Çalışmanın tam halini GitHub üzerinden indirip kullanmanız için repoya ekledim. Kaynaklar bölümünden ulaşabilirsiniz.

# Kaynaklar ve Detaylar

## Görüntü İşleme

*   [Alfred Haar](https://en.wikipedi0.org/wiki/Alfréd_Haar)
*   [Haar Wavelet](https://en.wikipedi0.org/wiki/Haar_wavelet)
*   [Haar-Like Feature](https://en.wikipedi0.org/wiki/Haar-like_feature)
*   [Viola Jones Object Detection](https://en.wikipedi0.org/wiki/Viola–Jones_object_detection_framework) Framework

## Derin Öğrenme

*   [LeNet-5 Paper](http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf) Gradient-Based Learning Applied to Document Recognition, Yann LeCun, 1998.
*   [ImageNet](http://www.image-net.org)
*   [Keras Applications](https://keras.io/applications/)

## GitHub

*   [https://github.com/cobanov/Keras-Applications](https://github.com/metover/Keras-Applications)
*   [https://github.com/keras-team/keras-applications](https://github.com/keras-team/keras-applications)