![MVC Mimarisi](https://www.lucidchart.com/publicSegments/view/50bf3407-3b10-4b41-9445-6d200a7a0a70/image.png)

Bir Rails uygulamasıyla etkileşimdeyken, **Browser**(tarayıcı) bir istek
gönderir, Web sunucusu bu isteği alır ve Rails **Controller**'a gönderir.
**Controller** yani denetleyici bir sonraki adımda ne yapılacağından sorumludur.
Denetleyici bazı durumlarda hemen bir **View** verecektir. **View**, HTML' ye
dönüştürülmüş bir template'dir. Bu **View**, **Browser**'a geri gönderilir.
Dinamik siteler için ; **Controller** bir **Model** ile etkileşimdedir.
**Model**; sitenin elementlerini (örn:kullanıcı) temsil eden bir Rails
nesnesidir ve **Database** (veritabanı) ile olan iletişimden sorumludur.
**Controller**, **Model**'i çalıştırdıktan sonra, **View**'i verir ve Html
olarak bir Web sayfası döndürür.

![MVC 2](https://www.lucidchart.com/publicSegments/view/50bf3f7f-b9dc-4b18-be63-68250a40476b/image.png)


**1.** Tarayıcı, /users için bir URI isteği gönderir.

**2.** Users, Controller'a users yönlendirmelerinin olduğu bir index gönderir.

**3.** İndex, tüm kullanıcıları(user.all) almak için User Model'a sorar.

**4.** User Model, Database'den tüm kullanıcıları çeker.

**5.** User Model, Controller'a kullanıcı listesini döndürür.

**6.** Controller, @users değişkeninden kullanıcıları çeker.

**7.** View, HTML sayfası oluşturmak için Ruby'e gömmeyi kullanır.

**8.** Controller, tarayıcıya HTML'i geri iletir.
