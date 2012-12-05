---
layout: post
title: Ruby on Rails Controller-1
---

## 1 Controller(Denetleyici) Ne Yapar?

Controller yani denetleyici, tarayıcıdan gelen isteği alır, Model'den ilgili veriyi çeker ve HTML çıktısı oluşturmak için View'ı kullanır.
Controller, View ve Model arasında bağlantıyı sağlar. Bu sayede Model, View'a veriler gönderir. View da kullanıcı verilerini görüntüler veya bilgiler üzerinde güncelleme yapar.

## 2 Yöntemler(Methods) ve Eylemler(Actions)

Controller, ApplicationController'dan devralan ve sınıf yöntemlerine sahip olan bir Ruby sınıfıdır. Application bir istek aldığında, yönlendirme(Routing) hangi denetleyici ve eylemin çalıştırılacağına karar verir. Sonra  Rails eylem olarak aynı adı taşıyan yöntemi çalıştırır ve bu denetleyicinin bir örneğini oluşturur.

Yönlendirme işlemi ile ilgili daha fazla bilgi için [tıkla](http://guides.rubyonrails.org/routing.html)

    class ClientsController < ApplicationController
        def new
        end
    end

Örneğin; kullanıcı yeni bir müşteri(client) eklemek için /clients/new giderse, Rails ClientsController'ın bir örneğini oluşturur ve bu yeni yöntemi çalıştırır. Yukarıdaki örnekte boş yöntem çok işe yarayabilir. Çünkü eylem akışını belirtmediği sürece, Rails varolan new.html.erb görünümünü verecektir. Bu yeni yöntem, yeni bir Client oluşturarak görünümü, @Client örnek değişkeni için kullanılabilir yapar:

    def new
        @client = Client.new
    end

Daha fazla bilgi için [Layouts & Rendering Guide](http://guides.rubyonrails.org/layouts_and_rendering.html)

ApplicationController, ActionController::Base 'den devralır. ActionController::Base bazı yardımcı yöntemler dizisi tanımlar. Ben bunların bazılarından bahsedeceğim. Daha fazla bilgi için API dokümanlarına bakabilirsiniz.

Sadece genel yöntemler, eylem olarak çağırılabilir. Yöntemlerin görünürlüğünü azaltmak için, yardımcı yöntemlerin ve filtrelerin eylem olmaması amaçlanmıştır. 

## 3 Parametreler

Muhtemelen denetleyici eylemlerini, kullanıcı veya diğer parametreler tarafından gönderilen verilere erişmek için isteyeceksiniz. Bir web uygulamasında iki olası parametre türü vardır:
* İlk parametre, sorgu dizesi parametresidir. Bu parametreler URL'nin parçası olarak gönderilen parametrelerdir. Sorgu dizesi sonunda '?' olan her şeydir. 
* İkinci tür parametreler genellikle POST veri olarak adlandırılır. Bu bilgiler genellikle kullanıcı tarafından doldurulan HTML formundan gelir. POST veri denilir, çünkü bu parametreler sadece HTTP POST isteklerinin bir parçası olarak gönderilir. 

Rails, sorgu dizesi parametreleri ve POST parametreleri arasında ayrım yapmaz ve denetleyicimizde her iki parametre de karışık bulunur.

    class ClientsController < ActionController::Base
      # This action uses query string parameters because it gets run
      # by an HTTP GET request, but this does not make any difference
      # to the way in which the parameters are accessed. The URL for
      # this action would look like this in order to list activated
      # clients: /clients?status=activated
      def index
        if params[:status] == "activated"
          @clients = Client.activated
        else
          @clients = Client.unactivated
        end
      end
 
      # This action uses POST parameters. They are most likely coming
      # from an HTML form which the user has submitted. The URL for
      # this RESTful request will be "/clients", and the data will be
      # sent as part of the request body.
      def create
        @client = Client.new(params[:client])
        if @client.save
          redirect_to @client
        else
          # This line overrides the default rendering behavior, which
          # would have been to render the "create" view.
          render :action => "new"
        end
      end
    end


### 3.1 Hash ve Dizi Parametreleri

Hash parametreleri tek boyutlu anahtarlar ve değerler ile sınırlı değildir. Web uygulamamız diziler ve iç içe parametreler içerebilir. Değerler dizisi göndermek için; anahtar adının yanına '[]' eklenir:

GET /clients?ids[]=1&ids[]=2&ids[]=3

params[:ids] değerleri ["1", "2", "3"] olacaktır. Parametre değerlerinin her zaman dizi olduğunu unutmayın. Çünkü Rails parametre değerlerinin türünü tahmin etmek için hiçbir şey yapmaz.

"[]" ' ler içinde anahtar adını içeren bir hash göndermek için ;

    <form accept-charset="UTF-8" action="/clients" method="post">
      <input type="text" name="client[name]" value="Acme" />
      <input type="text" name="client[phone]" value="12345" />
      <input type="text" name="client[address][postcode]" value="12345" />
      <input type="text" name="client[address][city]" value="Carrot City" />
    </form> 

Bu form gönderildiğinde; [:client] değeri {"name" => “Acme”, “phone” => “12345”, “address” => {"postcode" => “12345”, “city” => “Carrot City”}} şeklinde olacaktır.

**Not:** [:client]'ta [:address] iç içe parametredir.
**Not:** Hash parametresi, sembol ve dizeleri, anahtarlar gibi birbirleri yerine kullanılmasına izin verir.

### 3.2 JSON/XML Parametreleri 

Eğer bir web uygulaması yazıyorum diyorsanız, JSON veya XML formatındaki parametreleri tercih ederseniz daha rahat edersiniz. Rails, parametrelerinizi otomatik olarak Hash parametrelerine dönüştürecek ve böylece form verilerinde olduğu gibi erişmeniz mümkün olacaktır. 

Örneğin bir JSON parametresi gönderiyorsanız;

{ "company": { "name": "acme", "address": "123 Carrot Street" } }

params[:company] parametreniz { :name => “acme”, “address” => “123 Carrot Street” } gibi olur.

Ayrıca ilklendirici(initializer)'deki config.wrap_parameters veya denetleyicideki wrap_parameters'da açık verdiyseniz JSON/XML parametresinde kök ögeye güvenle atlayabilirsiniz. Parametreler varsayılan denetleyicinin adına göre anahtarda klonlanmış ve sarılmış olacak. Yani yukarıdaki parametrenin yazılışı;

{ "name": "acme", "address": "123 Carrot Street" } şeklinde olacaktır.

 CompaniesController'a veri gönderdiğimizi düşünelim, :company anahtarının içinde sarıldıktan sonra;

{ :name => "acme", :address => "123 Carrot Street", :company => { :name => "acme", :address => "123 Carrot Street" }} olur.

[API documentation](http://api.rubyonrails.org/classes/ActionController/ParamsWrapper.html) başvurarak sarmak istediğiniz belirli parametreleri veya anahtar adlarını özelleştirebilirsiniz.

### 3.3 Yönlendirme(Routing) Parametreleri

:controller ve :action anahtarları daima hash parametreleri içerir, fakat bu değerlere ulaşmak yerine controller_name ve action_name yöntemleri kullanılır. Yönlendirme tarafından tanımlanan diğer parametreler, :id gibi mevcut olacaktır.

Örneğin; bir müşteri listesini göz önüne alalım. Bu listede aktif ve pasif müşteriler gösterilebilir. Hoş bir URL'de :status parametresini yakalayıp bir yön ekleyebiliriz:

    match '/clients/:status' => 'clients#index', :foo => "bar"

Bu durumda bir kullanıcı, URL /clients/active açtığında, params[:status] aktif olarak ayarlanacaktır. Bu yön kullanıldığında params[:foo]'da sorgu dizesine geçilmiş gibi "bar" olarak ayarlanır. Aynı şekilde params[:actions], "index" 'i içerecektir. 

### 3.4 Varsayılan URL Ayarları(default_url_options)

Denetleyicinizde default_url_options adında bir yöntem tanımlayarak URL için global varsayılan parametreler ayarlayabilirsiniz. Böyle bir yöntem arzu edilen varsayılanlarla bir hash döndürmelidir. Arzu edilen varsayılanların anahtarları semboller olmalıdır:

    class ApplicationController < ActionController::Base
      def default_url_options
        {:locale => I18n.locale}
      end
    end

URL' leri oluşturuken, bu şeçenekler bir başlangıç noktası gibi kullanılacaktır. Bu yüzden mümkünse url_for çağrılarına geçirilen seçenekler ile onlar geçersiz olacaktır.

Eğer ApplicationController' da, default_url_options tanımlarsanız, yukarıdaki örnekte olduğu gibi, bu ayarlar tüm URL için kullanılacaktır. Bu yöntemi aynı zamanda belirli bir denetleyici tanımlayabilir. Bu durumda sadece orada oluşturulan URL' leri etkiler. 

