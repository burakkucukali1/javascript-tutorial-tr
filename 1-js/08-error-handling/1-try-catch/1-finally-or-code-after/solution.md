Bir fonksiyon içerisinden baktığımızda farklılıklar daha aşikar olmakta.

"dışarı sıçra" seçeneği olduğunda `try..catch` davranışı daha farklı olmaktadır.

Örneğin `try..catch` içerieinde bir `return` olduğunda. `try..catch` bloğunun sonunda her türlü `finally`'e uğramak zorunludur, bu `return` bile olsa.

```js run
function f() {
  try {
    alert('start');
*!*
    return "result";
*/!*
  } catch (e) {
    /// ...
  } finally {
    alert('cleanup!');
  }
}

f(); // cleanup!
```
...Veya aşağıdaki gibi `throw` varsa:

```js run
function f() {
  try {
    alert('start');
    throw new Error("an error");
  } catch (e) {
    // ...
    if("can't handle the error") {
*!*
      throw e;
*/!*
    }

  } finally {
    alert('cleanup!')
  }
}

f(); // cleanup!
```
Burada `finally` temizliğin yapılacağının garantisini verir. Eğer temizlik kodunu `f`'in sonuna koyarsanız çalışmayabilir.