## TensorBoard Başlangıç Rehberi


TensorBoard’un harika yeteneklerini Keras ile yeniden keşfedelim.

![TensorBoard](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255712135/4fc0cOKDJ.gif)*TensorBoard*

## TensorBoard

Google, [TensorFlow](https://www.tensorflow.org)’u bize armağan ettikten sonra [2017 TensorFlow Geliştiriciler Zirvesinde](https://www.youtube.com/watch?v=eBbEDRsCmv4) TensorBoard adlı yeni aracını tanıttı. TensorBoard’un inşa ettiğimiz sinir ağlarına ışık tutup, ağımızın yapısını ve birçok parametreyi bize nasıl gösterebileceğinden de -aynı zamanda interaktif grafikler çizebiliyor- bahsedildi. Bu yeni aracın büyük heyecan uyandıran bir özellik olduğunu düşünüyorum. Belki küçük sistemlerde çok ihtiyaç duymasak da büyük sistemler kurduğumuzda ayrık olarak her parçasına bakmak işimize gerçekten yarayacaktır.

![Tensorflow Dev Summit 2017 — Dandelion Mané](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255714914/WR3xlaC_-.jpeg)*Tensorflow Dev Summit 2017 — Dandelion Mané*

Bu rehber eşliğinde basit bir evrişimli sinir ağı (Convolutional Neural Networks) inşa ederek MNIST veri setinde oluşturduğumuz sistemi inceleyeceğiz. Özellikle bu yeni diyebileceğimiz teknolojilerde Türkçe kaynak azlığı sebebiyle bu rehberi yazmaya başlarken gördüm ki, TensorFlow üzerinden birkaç anlatımla karşılaşsam da Keras üzerinden anlatımı global olarak dahi çok kısıtlıydı, hem kullanım kolaylığı hem de Keras’ın anlaşılması kolay ve birçok harikulade yeteneği nedeniyle çalışmayı Keras üzerinden yapmaya karar verdim. Öncelikle bir sinir ağı oluşturup sonrasında TensorBoard’u bu ağ üzerinden nasıl çalıştırabileceğimizi inceleyeceğiz.

## Keras

Çalışmaya başlamadan önce rehberin kapsamını arttırmak adına kısa bir Keras anlatımı yapmayı uygun görüyorum. Keras en genel tabir ile TensorFlow üzerine kurulmuş bir Python modülüdür. TensorFlow üzerinde yapacağımız işlemleri basitleştirmek ve hızlandırmak için kullanılıyor. Daha derinlemesine öğrenmek isterseniz [buradan](https://www.fast.ai/2017/01/03/keras/) bu konuyla alakalı yazılmış habere bakabilirsiniz. Burada başka bir modül ismi verip sizi korkutmadan önce söylemek isterim ki artık TensorFlow, Keras’ı kendi bünyesine dahil etti, bu da demek oluyor ki hali hazırda TensorFlow’unuz varsa Keras’ınız da var demektir. Her ne kadar TensorFlow ile yazmak size daha iyi bir programcı hissi yaşatsa da Keras’a bir kez alıştığınızda bırakmak istemeyeceksiniz.

## Başlayalım!

*Arkadaşlar daha spesifik bir konuya geçtiğimiz için, TensorFlow ve Keras kurulumlarını geçmekte bir mazur görmüyorum, bu kurulumların daha detaylı anlatımlarını içeren yazıları aşağıda kaynaklar bölümüne bırakırım. Aynı zamanda hatırlatmak isterim TensorBoard için ayrı bir kurulum yapmıyoruz, TensorFlow kurulumunu yaptıysanız TensorBoard kurulmuş demektir.*

### Sinir Ağını Kuralım

Her zamanki gibi öncelikle importlarımızla başlıyoruz. Bu importlar; model için katmanlar, etkilenim fonksiyonları (Activation Functions), MNIST veri seti, ve iyileştiricileri (Optimizers) çağırmak için kullanılacak.

*Ayriyeten `time` modülünü eklediğimi de görmüşsünüzdür ilerleyen kısımlarda bundan bahsedeceğim.*

```python
import keras
from keras.datasets import mnist
from keras.models import Sequential
from keras.layers import Dense, Dropout, Flatten
from keras.layers import Conv2D, MaxPooling2D
from keras.layers import Activation
from keras.optimizers import Adam
from keras.callbacks import TensorBoard
import time
```

Şimdi ileride kullanacağımız parametreleri değişkenlere aktarabiliriz. Sonrasında değerleri değiştirmek istersek bize kolaylık sağlamasını amaçlıyoruz.

* batch_size: Veri setimizi küçük gruplar ayırıyoruz ve veriyi kaçlı olarak alacağımızı burada seçiyoruz.

* num_classes: MNIST için sınıflandırma yaparken biliyoruz ki toplamda 10 sınıfımız var, bunlar bizim 10 rakamımızı temsil ediyor.

* epochs: Eğitime verdiğimiz örneklerin bir kez forward ve bir kez de backward propagation sonrasında bir eğitim turu tamamlanır, her tur için 1 epoch diyoruz. Burada verdiğimiz değer eğitimin kaç tur yapılacağını belirtiyor.

* time: Logları alırken bir zaman parametresi de alıyoruz ki program her çalıştığında hangi tarih ve saatte oluşturduysak klasörün ismini o tarih olarak kaydederek kolaylık sağlasın.

Bu parametreler için daha derine inmek isterseniz Deep Learning Türkiye — Necmettin Çarkacı’nın [Derin Öğrenme Uygulamalarında En Sık kullanılan Hiper-parametreler](https://medium.com/deep-learning-turkiye/derin-ogrenme-uygulamalarinda-en-sik-kullanilan-hiper-parametreler-ece8e9125c4) yazısına bakabilirsiniz.

```python
batch_size = 128
num_classes = 10
epochs = 10
time = time.strftime("%Y_%m_%d_%H_%M_%S")
```

Verimizi çekip, kullanıma hazır hale getirelim. Kod bloğunda verilen işlemler kısaca matris işlemlerini içeriyor, bunları ayarladıktan sonra sinir ağımızı inşa edebiliriz.

```python
(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train = x_train.reshape(60000,28,28,1)
x_test = x_test.reshape(10000,28,28,1)
x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255

print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

```

Sequential *(Ardışıl)* ile katmanlarımızı sıralı modelde inşa edeceğimizi söylüyoruz, sonrasında evrişimli (Convolutional) bir katman, bir kez örnekleme (Pooling) ve aşırı öğrenme (Overfitting) problemini ortadan kaldırmak amacıyla seyreltme (Dropout) uyguladık.

Flatten *(Düzleştirme)* ile son katmanın çıkışından elde ettiğimiz veriyi düzleştirip çıkışı 128 norön olan bir katmana sokuyoruz ve bir kez daha seyreltme (Dropout) uygulayarak çıkış katmanına bağlıyoruz. Çıkış katmanımız önceden bahsettiğimiz gibi rakamları temsil edecekti bu yüzden sınıf sayımızı katmanın çıkış sayısı olarak ayarladık.

```python
model = Sequential()

model.add(Conv2D(32, (3,3), padding="same", input_shape=(28,28,1), activation= "relu"))
model.add(MaxPooling2D(pool_size=(3, 3)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(128, activation="relu"))
model.add(Dropout(0.5))

model.add(Dense(num_classes, activation='softmax'))
```

## Keras ile TensorBoard Kullanımı

Bu zamana kadar yaptıklarımız her zamanki gibi evrişimli sinir ağları (Convolutional Neural Networks-CNN) ile MNIST sınıflandırmasıydı, buraya kadar yaptığımız şeyleri çok farklı biçimlerde internette ve çeşitli yerlerde görebilirsiniz.

Burada yapacağımız yöntemi öğrendikten sonra kendi çalışmalarınıza da veya herhangi bir çalışmaya TensorBoard’u nasıl entegre edeceğinizi öğrenmiş olacaksınız.

Import’ları yaptığımız bölümde `from keras.callbacks import TensorBoard` diyerek TensorBoard’u kullanacağımızı belirtmiştik şimdi fonksiyonumuzu çağırarak bunu bir değişkene aktarabiliriz. Burada fonksiyona çeşitli parametreleri göndermemiz gerekiyor.

* `log_dir=loglarımızı nereye göndereceğimizi burada bildiriyoruz`

* `batch_size=batch boyutumuzu buraya bırakabiliriz, default olarak 32 geliyor, bizim batch boyutumuz bu çalışma için 128 olduğundan değişkenimizi buraya yazabiliriz.`

* `histogram_freq=eğer katmanların weight ve bias değerlerini de görselleştirmek istiyorsak bu değeri değiştireceğiz. default 0 olarak "0" geliyor, bir epoch içerisinde kaç kez değer toplamasını istiyorsak integer bir değer veriyoruz.`

* `write_grads=eğer gradientları da incelemek istiyorsak True diyebiliriz, ön koşul olarak histogram_freq &gt; 0 olmalı.`

```python
kerasboard = TensorBoard(log_dir="/tmp/tensorboard/{}".format(time),
                        batch_size=batch_size,
                        histogram_freq=1,
                        write_grads=False)
```

Artık sinir ağı işlemlerinin sonuna gelmek üzereyiz. Modelimizin nasıl eğitileceğini de yazabiliriz. Compile* (Derleme) *bölümünde hangi iyileme (Optimization) algoritmasını ve yitim (Loss) fonksiyonunu kullanacağımızı seçiyoruz, değerlerimizi doğruluk oranı olarak alacağımızı da ekliyoruz.

Modele artık örneklerimizi verebiliriz, batch ve epoch değerlerimizi veriyoruz, doğrulamayı test için ayrılan bölüm ile yapacaktık, ilgili parametrelere bunları gönderiyoruz.

`callbacks=[kerasboard]` ile callback olarak öncesinde oluşturduğumuz kerasboard değişkenini çağıracağımızı söylüyoruz. Bu şekilde sinir ağımızın inşasını bitirmiş olduk.

Projenin sonuna bir print satırı daha ekledim. Proje çalıştığında verdiğimiz hedef yoluna loglarımız kaydedilecek, komut satırından TensorBoard’u çalıştırıp bu log dosyasını çağıracağız, kolaylık olması açısından projemiz çıktı olarak hedef yoluyla birlikte bu komutu bize verirse komut satırına kopyala yapıştır yapabiliriz.

```python
model.compile(loss="categorical_crossentropy",
              optimizer="adam",
              metrics=['accuracy'])

model.fit(x_train, y_train,
          batch_size=batch_size,
          epochs=epochs,
          validation_split=0.3,
          validation_data=(x_test, y_test),
          callbacks=[kerasboard])


print("tensorboard --logdir="+kerasboard.log_dir)
```

## Tamamladık!

Artık yapmamız gereken komut satırından TensorBoard’u hedef dizinden çağırmak, zaten yazdığımız proje bunu bize çıktı olarak verecekti. TensorFlow’un yüklü olduğu ortam’ı (Environment)aktif ederek çıktımızı yapıştırıyoruz. (eğer TensorFlow için farklı bir ortam (environment) oluşturmadıysanız direkt çalıştırabilirsiniz.)

![TensorBoard’ı Çalıştırmak](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255716781/NNEenHo6Y.gif)*TensorBoard’ı Çalıştırmak*
> **Opsiyonel:** Ben TensorFlow’u `tensorflow` adında bir ortam oluşturarak oraya kurmuştum, `activate tensorflow` diyerek o ortam’ı aktif ediyorum, eğer siz başka bir ortama kurduysanız o isimle çalıştırarak aktif hale getireceksiniz, keza kurmadıysanız çıktıyı direkt yapıştırabilirsiniz.

`Path error alındığı takdirde C: dizini altında tmp adlı bir klasör oluşturun.`

Artık her şey hazır, `localhost:6006` adresinde grafiklerimiz bizi bekliyor olacak. İnternet tarayıcınızı açarak adres çubuğuna `localhost:6006` yazarak ilgili sayfaya ulaşabilirsiniz.

![TensorBoard Ekranı — localhost:6006](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255718352/MrXiASLgu.png)*TensorBoard Ekranı — localhost:6006*

Hep birlikte Keras ile TensorBoard’un nasıl kullanılacağını görmüş olduk. Bu rehberin yakın zamanda diğer parçalarını da yazacağım. Grafiklerin incelenmesi, embedding kavramı, örneklerin görselleştirilmesi ve parametrelerin derinlemesine incelenmesi gibi konu başlıkları olacak. Herkese iyi çalışmalar dilerim arkadaşlar.

## Kaynaklar

* Projenin tam hali Github adresime ekledim [buradan ](https://github.com/cobanov/Keras-Tensorboard)ulaşabilirsiniz.

* Keras Callbacks TensorBoard dökümantasyonuna [buradan](https://keras.io/callbacks/#tensorboard) ulaşabilirsiniz.

* Kurulumlar ve daha fazlası için Deep Learning Türkiye — Türkçe Yapay Zeka Kaynaklarına [buradan](https://github.com/deeplearningturkiye/turkce-yapay-zeka-kaynaklari) ulaşabilirsiniz.