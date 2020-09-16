## Veri Görselleştirme 4.0


Zaman bazlı ve anlık verilerinizi sıkıcı görsellerden kurtararak projenizde fütürist bir yaklaşım sağlayabilirsiniz. Hem de bu zamana kadarki tüm rehberlerim gibi oldukça basit ve hızlı bir şekilde.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255678579/JzPCGKGaz.gif)

IoT projelerinizde sensörleriniz zaman bazlı olarak sizin için veri üretirler veya makine öğrenmesi uygulamalarınız için yeni bir model oluştururken birçok metrik değer elde edersiniz, belki de sadece elinizdeki bir veri setini görselleştirmek istiyorsunuzdur.

Sizin için hazırladığım bu rehberde aslında sürecin ne kadar kolay olduğunu göreceksiniz. Komut pencereleri ve SQL sorguları ne kadar heyecan verici gözükmese de bu sistem arka planda sizin için her şeyi hallediyor olacak. Dilerseniz hemen proje hakkında biraz ön bilgi verip çalışmaya başlayalım.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255680292/s4GHus6y-.png)

Bu frameworkler ile nasıl işler yapılabildiğini ve frameworkleri nerelerde kullanabileceğinizi daha iyi anlamak adına benim nerede kullandığımı anlatabilirim.

Bulunduğum şirkette veri mühendisi olarak çalışıyorum. Burada üretim ve test aşamalarında makinelerin ürettiği verileri Grafana’nın canlı panellerinde görselleştirerek olası problemleri erken safhalarda tespit etmeyi amaçlıyoruz. Grafana’nın kendi içerisinde bir alarm mekanizması olduğunu da şimdiden belirtmekte fayda var. Bu sayede olası problemleri izleyip kişileri haberdar eden ufak botlara sahip oluyoruz.

![Grafana Logo](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255681809/A4K5Xb6vp.png)*Grafana Logo*

**Grafana: **Genel amaçlı bir görselleştirme arayüzüdür. Web uygulaması olması gerçekten harika. Paneller gerçekten güzel gözüküyor ve birçok farklı sistemle entegre olarak çalışabiliyor.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255683602/SG9tUBauQ.png)

**InfluxDB:** IoT sensör verileri ve gerçek zamanlı veriler için optimize edilmiş bir veri tabanı formu, gerçekten de kendi sınıfında en hızlısı olması tercih edilme sebebi.

## Veri Seti

Öncelikle çalışacağımız veri setine bir göz atalım, farklı dosya formatlarında çalışabilirsiniz. Bu çalışma özelinde popülerliği açısından “.CSV" uzantılı bir dosya kullanacağım.

### Örnek Veri Seti

Kaggle üzerinden avokado fiyatlarını içeren örnek bir veri seti kullanacağım. Veri setine aşağıdaki link üzerinden ulaşabilirsiniz.
> [Avocado Prices](https://www.kaggle.com/neuromusic/avocado-prices/downloads/avocado-prices.zip/1)

### Gerekli Dosyalar

### Telegraf
> [Telegraf Windows Link](https://dl.influxdata.com/telegraf/releases/telegraf-1.11.4_windows_amd64.zip)

### InfluxDB
> [InfluxDB Windows Link](https://dl.influxdata.com/influxdb/releases/influxdb-1.7.7_windows_amd64.zip)

### Grafanas
> [Grafana Standalone Windows Binaries Link](https://dl.grafana.com/oss/release/grafana-6.3.2.windows-amd64.zip)

## Başlayalım

Ben öncelikle indirdiğim tüm dosyaları bir klasör içerisine çıkartıyorum ve veri setini telegraf klasörünün içine atıyorum. Dosyaları çıkarttıktan sonra dosya isimlerini sadeleştirmek, komut penceresinden kolayca ulaşmak için yararlı olacaktır.
> Not: İndirdiğiniz klasörleri çıkartırken klasöre çıkart yerine buraya çıkart derseniz, klasörlere daha hızlı erişebilirsiniz.

Yapılanları tamamladığımızda klasörün içerisi bu şekilde görünüyor.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255685008/voIYK7DvN.png)

## Ayarlarımızı Yapalım

Burada biraz config dosyalarını editleyeceğiz, çalışmamıza uygun olması için bazı bilgileri vermemiz gerekiyor.

### Telegraf Config

Telegrafın doğru şekilde mesajlaşması için çalışmamıza uygun yeni bir config dosyasını aşağıda verdiğim kod ile oluşturabilirsiniz. Telegraf klasörünün içerisinde komut satırı açarak bunu çalıştıralım.

```
telegraf -sample-config -input-filter file -output-filter influxdb > csv.conf
```


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255686517/2lHejU0BD.png)

Bu adımdan sonra telegraf klasörünün içerisinde csv.conf adında yeni bir dosya oluşmuş olması gerekiyor. Şimdi içerisine veri setimizi okuyabilmesi için gerekli ayarları ekleyelim. Bunun için veri setimize ufak bir göz atmakta fayda var.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255688186/d4dJpzHHB.png)

* ilk kolon index numarası içeriyor bu bizim için önemli değil.

* ikinci kolon tarih kolonu burayı özel olarak belirteceğiz.

Telegraf için yeni oluşturduğumuz csv.conf dosyasında 467. satırdan itibaren gelen komutları temizleyerek aşağıda verdiğim ayarları yapacağız. Siz kendi çalışmalarınızda burada yaptıklarımıza benzer olarak değişiklik yapabilirsiniz. Bu ayarlara nereden baktığımı merak ediyorsanız telegrafın github reposunda csv parser bölümüne [buradan](https://github.com/influxdata/telegraf/tree/master/plugins/parsers/csv) ulaşabilirsiniz.

```
[[inputs.file]]
  files = ["avocado.csv"]
  data_format = "csv"
  csv_header_row_count = 1
  csv_column_names = []
  csv_column_types = []
  csv_skip_rows = 0
  csv_skip_columns = 1
  csv_delimiter = ","
  csv_comment = ""
  csv_trim_space = false
  csv_tag_columns = []
  csv_measurement_column = ""
  csv_timestamp_column = "Date"
  csv_timestamp_format = "2006-01-02"
```


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255689596/4dK85OMVR.png)

### Grafana ve InfluxDB Config

Grafana ve InfluxDB config dosyalarında bir şey yapmamıza gerek yok. Burada config dosyalarının içerisine bakarak servislerin hangi portta çalıştığına veya kullanıcı ismi, parola gibi bilgileri değiştirmek isteyenler için gerekli satırları inceleyebilirsiniz.

## Çalıştıralım

Her biri kuruluma ihtiyaç duymadan standalone olarak çalışabiliyor, bu sayede bir servis kullanmak zorunda kalmayacaksınız.

Telegraf servisi, adresini verdiğiniz CSV dosyasını satır satır okumaya başlayacak ve bunu Influx database’ine yazacak.

* Grafana klasörüne girin ve bin klasörü altındaki grafana-server.exe dosyasını çalıştırın.

* InfluxDB klasörü altındaki influxd.exe dosyasını çalıştırın.

Son olarak telegraf klasörüne giderek bir komut ekranı açın ve aşağıda verdiğim kodu çalıştırarak başlatın.

`telegraf --config csv.conf`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255691090/WYN3Y2Pe8.png)

### Database Bağlantısını Yapalım

Grafana servisi localhost:3000 üzerinde çalışmaya başlayacak, tarayıcımızı açalım ve adrese gidelim.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255692436/HTND1mh0Z.png)

Harikulade, eğer ayarlarda değişiklik yapmadıysanız default olarak “***username: admin, password: admin”*** olarak oturum açabilirsiniz.

Şimdi ilk canlı dashboard’umuzu oluşturmak için Grafana bizden bir şeyler istiyor hızlıca tamamlayabiliriz.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255693911/EOcpuyoem.png)

“Add data source” diyerek InfluxDB’i bağlıyoruz.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255695432/NooCjXp0b.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255697003/BW4DwoLCn.png)
> ## HTTP Basic Auth
> #URL = [http://localhost:8086](http://localhost:8086)
> #Database = telegraf
 # username = “telegraf”
 # password = “metricsmetricsmetricsmetrics

Aşağıdaki görüntüyü elde ettiyseniz her şey olmuş demektir.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255698402/v7mbc4ZQU.png)

## Görselleştirelim

İşin eğlenceli kısmında geldik.

Şu anda verimiz servislere bağlanmış oldu. Veri setinin içerisine bakacak olursak veriler 2015 tarihine ait bu tarz zaman bazlı servisleri incelerken zaman aralıklarına dikkat etmemiz gerekiyor.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255699569/hPZMXFETo.png)

New dashboard sekmesine tıklayalım ve sonrasında add query butonuna basalım.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255701055/y3BIaLBbe.png)

Şimdi SQL sorgumuzu verelim ve veriyi görselleştirelim. Bunun için “select measurement” yazan bölüme tıklayarak seçim yapıyoruz. Hemen altındaki SELECT alanındaki field bölümünden parametremizi seçiyoruz. Ben örnek olması açısından şöyle bir görsel oluşturuyorum, siz verinize göre ayarları değiştirerek en uygun olanı seçebilirsiniz.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255702627/SFxCWgrBG.png)

Veriniz görünmediyse öncesinde anlattığım gibi tarihi ayarlamadığınız için olabilir. Ekranın sol üst tarafındaki zaman çubuğuna verinizin zaman aralığını girin, şu anda kullandığımız veri 2015 yılındandı.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255704230/OUnvpUn7P.png)

Tarihi ayarlıyorum ve işlemlerim tamamlandı. Kaydet butonuna basarak ilk panelimi eklemiş oluyorum. Grafana içerisinde birçok farklı panel içeriyor. Bunları deneyerek istediğiniz panelleri dashboard’a ekleyebilirsiniz.

Birkaç dakika içerisinde örnek amaçlı bir dashboard hazırlamış oluyorum.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1600255705701/NfbZV-1_f.png)

Gitmeden size birkaç püf nokta bırakayım, telegraf için oluşturduğumuz csv dosyasındaki parametreleri ayarlayarak oluşturduğunuz veritabanını projenize uygun özelleştirin. Parametreleri, tarihi, başlıkları ve tabloları tanımlayarak daha efektif çalışabilirsiniz. Grafana gerçekten çok fazla özelleştirilebilir, eklenti içeren ve birçok ayar bulunan bir arayüz. Ne kadar deneme yaparsanız o kadar güçlü bir şekilde kullanabilirsiniz. Ayrıca Grafana’nın alert kısımlarını, canlı akışlarını ve diğer özelliklerini kesinlikle incelemenizi öneririm.

Her türlü sorunuzu LinkedIn ve mail ile bana iletebilirsiniz. Projeyi GitHub’a ekliyorum oradan da ilgili dosyaları bulabilirsiniz.

## Proje Reposu

[https://github.com/cobanov/Data-Visualization](https://github.com/cobanov/Data-Visualization)