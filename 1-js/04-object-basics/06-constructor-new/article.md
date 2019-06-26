# Yapıcı, "new" operatörü

`{ ... }` yazımı bir objenin yaratılmasına yarar. Fakat bir objenin benzeri farklı objeler yaratmak istenebilir. Örneğin farklı kullanıcılar, farklı menü değerleri.

Bu yapıcı fonksiyon ve `"new"` operatörü ile yapılabilir.

[cut]

## Yapıcı fonksiyon

Yapıcı fonksiyonlar(Constructor) teknik olarak normal fonksiyonlardır. Tabi bazı farklılıkları vardır:

1. Büyük harfle başlayarak adlandırılırlar.
2. Sadece `"new"` operatörü kullanıldığında çalışırlar.

Örneğin:

```js run
function Kullanici(isim) {
  this.isim = isim;
  this.yoneticiMi = false;
}

*!*
let kullanici = new Kullanici("İhsan");
*/!*

alert(kullanici.isim); // İhsan
alert(kullanici.yoneticiMi); // false
```
Fonksiyon `new Kullanici(...)` şeklinde çalıştığında, aşağıdaki adımlar izlenir:

1. Yeni bir obje yaratılır ve `this` bu obje olur.
2. Fonksiyon gövdesi çalışır. Genelde `this` modifiye edilir ve yeni özellikler eklenir.
3. `this` değeri dönderilir.

Diğer bir deyişle, `new Kullanici(...)` şöyle yapar:

```js
function Kullanici(isim) {
*!*
  // this = {};  (üstü kapalı)
*/!*

  // bu objeye yeni özellikler ekle
  this.isim = isim;
  this.yoneticiMi = false;

*!*
  // return this;  (üstü kapalı)
*/!*
}
```

Öyleyse `new Kullanici("İhsan") objesi aşağıdaki gibidir:

```js
let kullanici = {
  isim: "Jack",
  yoneticiMi: false
};
```
Eğer başka bir kullanici oluşturmak istiyorsanız, yapmanız gereken `new Kullanici("Macide")`, `new Kullanici("Muzaffer")` gibi kullanmaktır. Doğrudan her defasında obje tanımlamaktan daha kısa ve anlaşılması kolaydır.

Yapıcı fonksiyonların amacı  -- tekrar kullanılabilecek objeleri yaratan kodun uygulanmasıdır.

Dikkat edecek olursanız, herhangi bir fonksiyon yapıcı fonksiyon olarabilir. Her fonksiyon `new`  ile çalıştırılabilir, ve yukarda anlatılan algoritmaya göre çalışır. "Yapıcı fonksiyon isimleri büyük harfle başlamalıdır" aslında genel bir ittifaktır, bunu daha da açıklayıcı yapmak için bu fonksiyonlar `new` ile çağırılmalıdır.

````smart header="new function() { ... }"
Eğer birçok satırdan oluşan kodda amacınız sadece karmaşık bir obje yapmak ise, bunları yapıcı fonksiyon içine aşağıdaki gibi almak mümkündür:

```js
let kullanici = new function() {
  this.isim = "İhsan";
  this.yonetici = false;

  // diğer karmaşık yapılar
  // mantıklar veya yerel değişkenler
};
```
Yapıcı fonksiyon tekrar çağırılamaz çünkü hiç bir yere kayıt edilmemiştir, sadece yaratılır ve çağırılır. Böylece yapıcı metod ilerde tekrar kullanılmayacağına garanti verir.
````

## Yapıcı modu testi: new.target

```smart header="Advanced stuff"
The syntax from this section is rarely used, skip it unless you want to know everything.
```

Fonksiyon içinde, bu fonksiyon `new` ile mi yoksa `new` olmadan mı çağırılmış bu `new.target` özelliği kullanılarak anlaşılabilir.

Normal çağrılarda bunun içerisi boştur fakat `new` ile çağrılırsa:

```js run
function Kullanici() {
  alert(new.target);
}

// new kullanılmadan:
User(); // new.target undefined döndürür.

// new kullanılarak:
new User(); // function Kullanici { ... } gibi ekrana fonksiyonu yazar
```

That can be used to allow both `new` and regular syntax to work the same:

```js run
function Kullanici(isim) {
  if (!new.target) { // eğer çart yerine getirilmezse
    return new Kullanici(isim); // ...yeni birisi eklenir.
  }

  this.isim = isim;
}

let ihsan = Kullanici("John"); // çağrıyı new Kullanici(isim) fonksiyonuna yönlendirir.
alert(ihsan.isim); // İhsan
```
Bu yaklaşım bazı kütüphanelerde yazımı daha esnek yapabilmek amacıyla kullanılır.  Her yerde kullanılması o kadar da iyi değildir. Çünkü `new` ne olup bittiği hakkında bilgi vermektedir. `new` ile yeni bir obje yaratıldığını anlayabiliyorsunuz ki bu da iyi birşeydir.

## Yapıcı metodun return sözcüğü

Genelde yapıcı metodların `return` sözcüğü yoktur. Amaçları gerekli olan bilgileri `this` içine yazmaktır ve bu da otomatik olarak sonuçtur.

Fakat `return` sözcüğü var ise kurallar basittir:

- Eğer `return` obje ile çağırılırsa `this` yerine bu obje döndürülür.
- Eğer `return` ilkel bir obje il çağırılırsa görmezden gelinir.

Diğer bir deyişle, obje ile `return` kullanıldığında obje döner, diğer tüm hallerde `this` döner.

Örneğin aşağıda `return` edilen obje `this` yerine dönderilir.

```js run
function BuyukKullanici() {

  this.isim = "İhsan";

  return { isim: "Muhsin" };  // <-- obje dönderir
}

alert( new BuyukKullanici().isim );  // Muhsin, objeyi aldık ^^
```
Şimdi ise boş bir `return` cümlesi yazalım( eğer ilkel bir tipte kullanılsa birşey değiştirmez)

```js run
function KucukKullanici() {

  this.isim = "İhsan";

  return; // çalışmayı bitirir ve `this`'i döndürür.

}

alert( new KucukKullanici().isim );  // İhsan
```
Genelde yapıcılar `return` sözcüğü kullanmazlar. Buarada amaç bütünlüğün sağlanması için geçerli olan özel bir davranıştır.

````smart header="Parantezlerin yazılmaması"

Bu arada `new`'den sonra eğer bir argüman yoksa parantez kullanmasanız da olur:

```js
let kullanici = new Kullanici; // <-- parantez yok
// aynı şey.
let kullanici = new Kullanici();
```
Parantezleri yazmamak "iyi yazım stili" değildir. Fakat bu şekilde yazım da mümkündür.
````

## Yapıcı içerisinde metod

Yapıcı fonksiyon kullanmak objeye mükemmel esneklik sağlar. Yapıcı fonksiyon parametreleri tanımlar ve objenin nasıl yapılacağını, ne konulması gerektiğini belirtir.

Tabiki `this`'e sadece özelliklerde değil metodlar içerisinde de ekleme yapılabilir.

Örneğin, aşağıdaki `new User(isim)` verilen `isim` ile yeni bir obje oluşturur. Bu obje `selamVer` metoduna sahiptir.

```js run
function Kullanici(isim) {
  this.isim = isim;

  this.selamVer = function() {
    alert( "Benim adım: " + this.isim );
  };
}

*!*
let ihsan = new User("İhsan");

ihsan.selamVer(); // Benim adım: İhsan
*/!*

/*
ihsan = {
   name: "İhsan",
   selamVer: function() { ... }
}
*/
```

## Özet

- Yapıcı fonksiyonlar, veya kısaca yapıcılar, normal fonksiyonlardır. Fakat baş haflerinin büyük olmasıyla ilgili ortak bir kullanım vardır.
- Bu fonksiyonlar sadece `new` kullanılarak çağırılmalıdır. Böyle çağrılar önce boş bir `this` yaratır ve buna değerler eklendikten sonra bu `this`'i geri gönderir.


Yapıcılar ile benzer objeler yapmak mümkündür.

JavaScript kendi içerisindeki objeler için yapıcı fonksiyon desteği verir. Örneğin: Tarihler için `Date`, setler için `Set` vs.

```smart header="Objelere tekrar dönülecektir!"
Bu bölümde basitçe yapıcılar nasıl yaratılır bunlar hakkında konuşuldu. Bir sonraki bölümde daha fazla veri tipi ve fonksiyonlar hakkında bilgi verilecektir.

<info:object-oriented-programming> bölümde objelere geri dönülmiş ve derinlemesine incelenmiştir.
```
