# Objelerin metodları ve "this" kelimesi.

Objeler genelde dünyada var olan şeyler gibidirler, kullanıcılar, emirler, vs.

```js
let kullanici = {
  isim: "İhsan",
  yas: 30
};
```

Kullanıcıların *işlem* yapma yetenekleri vardır: alışveriş sepeti, giriş, çıkış vs.

Bu aksiyonlar Javascript'te özellikler için fonksiyon kullanarak çözülür.


[cut]

## Metod Örnekleri

Başlangıç olarak `kullanici` merhaba desin:

```js run
let kullanici = {
  isim: "İhsan",
  yas: 30
};

*!*
kullanici.selamVer = function() {
  alert("Merhaba");
};
*/!*

kullanici.selamVer(); // Merhaba
```
Burada Fonksiyon ifadesi ile fonksiyon yaratıldı ve `kullanici.selamVer` özelliğine atandı.

Ardından bu metod çağırıldı ve kullanıcı selam verdi.

Bir objenin özelliği olan fonksiyona *metod* denir.

Öyleyse `kullanici` objesinin `selamVer` metodu bulunmaktadır.

Tabii ki metodları önceden tanımlanmış fonksiyonlarla da oluşturabilirsiniz. Örneğin:

```js run
let kullanici = {
  // ...
};

*!*
// önce tanımla
function selamVer() {
  alert("Merhaba!");
};

// Sonra bunu metod olarak objeye ekle
kullanici.selamVer = selamVer;
*/!*

kullanici.selamVer(); // Merhaba!
```

```smart header="Nesne Tabanlı Programlama"

Varlıkların obje olarak tanımlandığı dillere  [obje tabanlı diller](https://en.wikipedia.org/wiki/Object-oriented_programming), kısaca: "OOP"(Objet-Oriented Programming)

OOP kendi başına kitaplar dolusu anlatılacak bir konudur. Nasıl doğru varlıklar seçilmeli? Bu varlıklar arasında nasıl bir iletişim olmalı? Mimarisi nasıl olmalı, gibi konuların her birisi ile ilgili ayrı ayrı kitaplar bulunmaktadır. Örneğin "Design Patterns: Elements of Reusable Object-Oriented Software" by E.Gamma, R.Helm, R.Johnson, J.Vissides or "Object-Oriented Analysis and Design with Applications" by G.Booch, vs. Bu kitapta <info:object-oriented-programming> sadece başlangıç seviyesinde anlatılacaktır.
```
### Metod Kısayolu

Metodları yaratmak için daha kolay bir kullanım mevcuttur:

```js
// aşağıdaki objeler aynı işleri yapar.

let kullanici = {
  selamVer: function() {
    alert("Merhaba");
  }
};

// kısa yolu daha güzel görünüyor değil mi?
let kullanici = {
*!*
  selamVer() { // "selamVer: function()" ile aynı
*/!*
    alert("Merhaba");
  }
};
```

Yukarıda da gösterildiği gibi `"function"` pas geçilerek sadece `selamVer()` yazılabilir.

Doğrusunu söylemek gerekirse iki fonksiyonda birbiri ile aynı. Altta yatan farklılık ise kalıtım ile alakalı ki bu da şimdilik bir sorun teşkil etmiyor. Çoğu durumda kısa yazım tercih edilmektedir.


## Metodlarda `this` kullanımı

Obje metodlarının objelerde bulunan diğer bilgilere ulaşması çok büyük bir gerekliliktir. 
Örneğin `kullanici.selamVer()` `kullanici` ismine ihtiyaç duyar.


**Objeye ulaşabilmek içim metod `this` kelimesine ihtiyaç duyar.**

"noktadan önce" yazılan `this` o objeye referans verir. Örneğin:

```js run
let kullanici = {
  isim: "İhsan",
  yas: 30,

  selamVer() {
*!*
    alert(this.isim);
*/!*
  }

};

kullanici.selamVer(); // İhsan
```

Yukarıda `kullanici.selamVer()` fonksiyonu çalıştırılırken `this` `kullanici` olacaktır.

Teknik olarak `this` olmadan da objenin özelliklerine erişmek mümkündür. Bu dıştaki değişkene referans vererek yapılabilir:

```js
let kullanici = {
  isim: "İhsan",
  yas: 30,

  selamVer() {
*!*
    alert(kullanici.isim); // "this" yerine "kullanici" kullanılmıştır.
*/!*
  }

};
```
... Fakat böyle bir koda güvenilez. Diyelim ki `kullanici` objesini kopyaladınız ve `yonetici = kullanici` yaptınız. Sonra `kullanici` objesinin üzerine yazdınız bu durumda yanlış objeye erişmiş olacaksınız. Bir örnekle açıklamak gerekirse:

```js run
let kullanici = {
  isim: "İhsan",
  yas: 30,

  selamVer() {
*!*
    alert( kullanici.isim ); // hataya neden olur
*/!*
  }

};


let yonetici = kullanici;
kullanici = null; 

yonetici.selamVer(); // `selamVer()` içerisinde `kullanici` kullanıldığından dolayı hata verecektir.
```

Eğer `kullanici.isim` yerine `this.isim` yazmış olsaydınız kod çalışacaktı.


## "this" bağımsız bir şekilde kullanılabilir.

Diğer dillerden farklı olarak "this" kelimesi yer gözetmeksizin kullanılabilir. Her fonksiyonun içinde kullanılabilir.

Aşağıdaki kodda bir yazım hatası yoktur:

```js
function selamVer() {
  alert( *!*this*/!*.isim );
}
```

`this`'in değeri çalışma anında değerlendirilir. Herşey olabilir.

Örneğin `this` farklı objelerden çağırıldıklarında değerler alabilirler:

```js run
let kullanici = { isim: "İhsan" };
let yonetici = { isim: "Macide" };

function selamVer() {
  alert( this.isim );
}

*!*
// aynı fonksiyonu iki farklı objeye atandı.
kullanici.f = selamVer;
yonetici.f = selamVer;
*/!*

// iki fonksiyon da farklı `this` e sahip.
// "noktadan" önceki "this" objeye referans verir.
kullanici.f(); // İhsan  (this == kullanici)
yonetici.f(); // Yonetici  (this == yonetici)

yonetici['f'](); // Köşeli parantez veya noktalı yazım farketmez, her ikisi de çalışır.
```

Aslında fonksiyonu obje olmadan da çağırmak mümkündür.

```js run
function selamVer() {
  alert(this);
}

selamVer(); // tanımsız
```
Sıkı modda `this` `undefined` döndürür. Eğer `this.isim` yazılırsa hata verir.

Normal modda ise ( `use strict` unutulursa) `this` değeri *global obje* olur. Tarayıcı için bu `window`dur. Bu konuya daha sonra değinilecektir.


Obje olmadan `this` çağırmak normal değildir, bir programlama hatasıdır. Eğer fonksiyon `this` içeriyorsa, o objenin dahilinde çağırılabileceği anlamı çıkar.
````

```smart header="Sınırsız `this` kullanmanın yan etkileri"

Diğer programlama dillerinden geliyorsanız, "bağımlı `this`" kullanımına alışmış olmalısınız. Metod içerisinde kullanılan `this`  her zaman o objeye referans olur.

JavaScript'te `this` bağımsızdır. Değeri çalışma anında belirlenir, hangi metodda yazıldığı önemli değildir, önemli olan "noktadan önceki" objedir.

Çalışma anında `this` kullanılabilmesinin artıları ve eksileri vardır. Bir taraftan fonksiyonlar diğer objelerde de kullanılabilirken, diğer yönden bu kadar esneklik hatalara neden olabilmektedir.

Burada amacımız programlama dilininin dizaynının kötü veya iyiliği değildir. Amaç nasıl çalıştığını anlayıp, nerelerde yarar nerelerde zarar getirileceğini bilmektir.

```

## Dahili Özellikler: Referans Tipleri

```warn header="Derinlemesine dil özellikleri"
Bu bölüm daha derinlemesine konuları içermektedir. 

Daha hızlı ilerlemek istiyorsanız bu bölümü geçebilir veya daha sonraya erteleyebilirsiniz.
```
Girift metod çağrıları `this` kelimesini kaybedebilir, örneğin:

```js run
let kullanici = {
  isim: "İhsan",
  selamVer() { alert(this.isim); },
  yolcuEt() { alert("Güle Güle"); }
};

kullanici.selamVer(); // Basit metod beklendiği gibi çalışır

*!*
// Şimdi isme göre selamVersin veya yolcuEt'sin.
(kullanici.isim == "İhsan" ? kullanici.selamVer : kullanici.yolcuEt)(); // Hata!
*/!*
```

Son satırda kullanıcı ismine göre `kullanici.selamVer` veya `kullanici.yolcuEt` cagrilir. `kullanici.selamVer` `()` ile çağrıldığında çalışmaz. 

Bunun nedeni çağrı içerisinde `this`'in `undefined` olmasıdır.

Aşağıdaki çalışır:
```js
kullanici.selamVer();
```
Aşağıdaki kod metodu çalıştırmaz:
```js
(kullanici.isim == "İhsan" ? kullanici.selamVer : kullanici.yolcuEt)(); // Hata!
```

Neden? Eğer bunun derinliklerine inmek isterseniz öncelikle bakmanız gereken yer `obj.method()` çağrısı olmalıdır.

Peki bu `obj.method()` cümlesi ne yapar:

1. `'.'` özelliği alır
2. `()` bu özelliği çalıştırır.

Peki `this` ilk bölümden ikincisine nasıl geçer?

Eğer bu olayı iki farklı satırda gösterecek olursak, `this` bu durumda kaybolacaktır:

```js run
let kullanici = {
  isim: "İhsan",
  selamVer() { alert(this.isim); }
}

*!*
// metodu alma ve çağırma iki satırda gösterilecek olursa
let selamVer = kullanici.selamVer;
selamVer(); // hata!, tanımsız
*/!*
```
Burada `selamVer = kullanici.selamVer` fonksiyonu değişkene atar, sonra son satırdaki yapılan ise tamamen ilkinden farklıdı. Bundan dolayı `this` bulunmamaktadır.

**`kullanici.selamVer()` çağrısının çalışabilmesi için bir çözüm bulunmaktadır. `'.'` fonksiyon değil [Referans Tipi] döndürmektedir.(https://tc39.github.io/ecma262/#sec-reference-specification-type)**

Referans Tipi "şartname tipidi". Doğrudan kullanılamaz, dil kendince kullanabilir bu tipleri.

Referans Tipi üç değerin birleşmesi ile oluşur `(base, name, strict)`:

- `base` objedir.
- `name` özelliktir.
- `strict` eğer `use strict` kullanılmışsa `true` olur.

`kullanici.selamVer` erişimi fonksiyon değil Referans Tipi döndürür. Sıkı mod kullanıldığında `kullanici.selamVer` aşağıdaki gibi döner:

```js
// Referans Tipi değeri
(kullanici, "selamVer", true)
```

Referans Tipinde `()` çağrıldığında obje ve onun metodu hakkında tüm bilgileri alınır ve `this` (bizim durumumuzda `kullanici` ) belirlenir.

Atama gibi işlemler `selamVer = kullanici.selamVer` referans tipini tamamen iptal eder, `kullanici.selamVer`(fonksiyon) değerini alır ve bunu paslar. Bu şekilde de `this` tanımsız kalır.

Bundan dolayı `this` in çalışabilmesi için metodun doğrudan `obj.metod()` şeklinde veya `obj[metod]()` şeklinde çalıştırılması gerekmektedir.

## Ok fonksiyonlarında "this" bulunmamaktadır.

Ok fonksiyonları özeldir: Kendilerinin `this`'i bulunmaz. Eğer yine de `this` kullanırsanız ok fonksiyonu dışındaki bölümü `this` olarak alır.

Örneğin aşağıdaki `ok()` dışarıda bulunan `kullanici.selamVer()` metodunu kullanmaktadır:


```js run
let kullanici = {
  isim: "İhsan",
  selamVer() {
    let ok = () => alert(this.isim);
    ok();
  }
};

kullanici.selamVer(); // İhsan
```
Bu ok fonksiyonlarının bir özelliğidir. Ayrı bir `this` kullanmak yerine her zaman bir üstteki bölümden `this` i alması baya kullanışlıdır. <info:arrow-functions> bölümü içerisinde bu konu derinlemesine incelenecektir.

## Özet

- Objeler içerisinde saklanan fonksiyonlara "metod" denir.
- Metodlar objelerin `obje.biseylerYap()` seklinde çalışabilmesini sağlar.
- Metodlar objelere `this` şekline referans verebilir.

`this`'in değeri çalışma zamanında tanımlanır.
- Fonksiyon tanımlanırken `this` kullanabilir, fakat `this` bu metod çalışmadığı müddetçe bir anlam ifade etmez.
- O fonksiyon objeler arasında kopyalanabilir.
- Fonksiyon metod yazım şekliyle çağırıldığında `obje.metod()`, `this`'in değeri bu çağrı boyunca `obje`'dir.

Ok fonksiyonlarında `this` bulunmamaktadır. Eğer bu fonksiyonlar içerisinde `this` çağırılırsa bunun değeri dışarıdan alınır.