# Introduction to ReactiveX(RxJava)
[ReactiveX](http://reactivex.io/) ဆိုတာ [Observer pattern](https://en.wikipedia.org/wiki/Observer_pattern)၊ [Iterator pattern](https://en.wikipedia.org/wiki/Iterator_pattern) နဲ့ [Functional Programming](https://en.wikipedia.org/wiki/Functional_programming) တို့ရဲ့အားသာချက်တွေကို ပေါင်းစပ်ထားတာပါ။ သူ့ရဲ့ idea ကရိုးရှင်းပါတယ်၊ အရာရာတိုင်းက stream ပါ။ Variable ပဲဖြစ်ဖြစ်၊ Object ပဲဖြစ်ဖြစ်ပါ။ 

Observable က stream တွေကို emit လုပ်မယ်၊ Subscriber က Observable emit လုပ်လိုက်တဲ့ stream ကို subscribe လုပ်မှာပါ။ Emit လုပ်မဲ့ item တိုင်းအတွက် `subscriber.onNext()` method နဲ့ emit လုပ်ပြီး `subscriber.onComplete()` ဒါမှမဟုတ် `subscriber.onError()` method နဲ့အဆုံးသတ်မှာပါ။

## Hello World!
အကောင်းဆုံးနည်းလမ်းကတော့ **Hello World!** နဲ့စတာပါ။ :D
```java
final Observable<String> observable = Observable.create(
        new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> emitter) throws Exception {

                Log.d("Observable", "OnSubscribe.call()");
                emitter.onNext("Hello World!");
                emitter.onComplete();
            }
        }
);
```

Observable က **Hello World!**  ကို emit လုပ်ပြီးတော့ complete ဖြစ်သွားပါတယ်။

```java
final Observer<String> subscriber = new Observer<String>() {
    @Override
    public void onSubscribe(Disposable d) {
                
        Log.d("Subscriber", "onSubscribe()");
    }

    @Override
    public void onNext(String value) {

        Log.d("Subscriber", "onNext()");
        Log.i("Subscriber", value);
    }

    @Override
    public void onError(Throwable e) {}

    @Override
    public void onComplete() {

        Log.d("Subscriber", "onCompleted()");
    }
};
```

**observable** object ကို subscribe လုပ်ပါမယ်။

```java
Log.d("MainActivity", "before subscribe");
observable.subscribe(subscribe);
```

Subscribe လုပ်လိုက်တဲ့အခါမှာ observable က subscriber ရဲ့  `onNext()` နဲ့ `onComplete()` method တွေကိုခေါ်ပြီး **Hello World!** ကို emit လုပ်ပေးမှာပါ။
ရလဒ်အနေနဲ့
```
MainActivity: before subscribe
Subscriber: onSubscribe()
Observable: OnSubscribe.call()
Subscriber: onNext()
Subscriber: Hello World!
Subscriber: onCompleted()
```
ဆိုပြီးရမှာပါ။

## Make It Simple
အပေါ်မှာရေးထားတာက observable ရဲ့အလုပ်လုပ်ပုံကို မြင်သာအောင်ရေးပြတာပါ။ တစ်ကယ်ရေးရင် အဲဒီလို boilorplate code တွေမလိုပါဘူး။ Single item emitting အတွက် `Observable.just()` နဲ့ sequential item emitting အတွက် `Observable.fromArray()` နဲ့ `Observable.fromIterable()` ဆိုပြီးသုံးလို့ရပါတယ်။
```java
Observable.just("Hello World!");

Observable.fromArray("Hello World!", "Welcome to Rx...");
// or
Observable.fromIterable(
    new ArrayList<String>() {{
        add("Hello World!"); 
        add("Welcome to Rx");
    }});
```

Subscriber ရဲ့ `onNext()` နဲ့ `onError()` ကိုလည်း `Consumer<T>` functional interface တစ်ခုထဲနဲ့ရေးလို့ရပါတယ်။ Functional interface အကြောင်းကိုတော့ [ဒီ](https://medium.com/@pyaethuaung/java-8-language-features-for-android-b7315b017dc7#.knov3bx6b)မှာဖတ်လို့ရပါတယ်။ `observable.subscribe(onNextConsumer, onErrorConsumer, onCompleteAction)` ဆိုတဲ့ပုံစံနဲ့ပါ။ `onError()` နဲ့ `onComplete()` ကိုမစဉ်းစားပဲ subscribe လုပ်ဖို့အတွက်ဆိုရင် `observable.subscribe(onNextAction)` ဆိုပြီးရေးလို့ရပါတယ်။
```java
Observable.just("Hello World!")
    .subscribe(new Consumer<String>() {
        @Override
        public void accept(String s) throws Exception {
        
            Log.d("Only onNext", s);
        }
    });
```

Java 8 lambdas နဲ့ဆိုရင်
```java
Observable.just("Hello World")
    .subscribe(s -> Log.d("Only onNext", s));
```

## Operator
Observable က emit လုပ်လိုက်တဲ့ stream ကို subscriber ဆီမရောက်သေးခင်မှာ [operator](http://reactivex.io/documentation/operators.html) တွေကိုသုံးပြီး အမျိုးမျိုးပြောင်းလဲလို့ရပါတယ်။
```java
Observable.just("Hello, World!")
        .map(new Function<String, String>() {
            @Override
            public String apply(String s) throws Exception {

                return s.toUpperCase();
            }
        })
        .subscribe(new Consumer<String>() {
            @Override
            public void accept(String s) throws Exception {

                Log.d("Upper Case", s);
            }
        });
```
`map()` operator ကိုသုံးပြီး "Hello, World!" ကို uppercase ပြောင်းလိုက်တာပါ။ Result က 
```
Upper Case: HELLO WORLD!
```
ဆိုပြီးရမှာပါ။

Operator တွေကို ပေါင်းပြီးလဲသုံးလို့ရပါတယ်။
```java
Observable.just("Hello, World")
        .map(new Function<String, String>() {
            @Override
            public String apply(String s) throws Exception {
                return s.toUpperCase();
            }
        })
        .map(new Function<String, Integer>() {
            @Override
            public Integer apply(String s) throws Exception {
                return s.hashCode();
            }
        })
        .subscribe(new Consumer<Integer>() {
            @Override
            public void accept(Integer i) throws Exception {
                Log.d("Upper Case", String.valueOf(i));
            }
        });
```
ဒီလိုဆိုရင် *Hello, World* ရဲ့  hash code ရမှာပါ။

Lambda expression နဲ့ဆိုရင်
```java
Observable.just("Hello, World")
        .map(s -> s.toUpperCase())
        .map(s -> s.hashCode())
        .subscribe(i -> Log.d("Upper Case", String.valueOf(i)));
```
ဆိုပြီးရေးလို့ရပါတယ်။ ဖတ်ရတာလည်းလွယ်သလို operator တွေကိုလည်း ပြန်သုံးလို့ရပါတယ်။

ReactiveX ရဲ့ အားသာချက်တွေကတော့
- Functional ဖြစ်တဲ့အတွက် input ကို ဘယ်လို output အဖြစ်ပြောင်းပေးမလဲဆိုတဲ့ ရိုးရှင်းတဲ့ function တွေကိုသုံးပြီး ရှုပ်ထွေးတဲ့ stateful code တွေကိုရှောင်လို့ရပါတယ်။
- Operator function တွေက manually ရေးနေရတဲ့ code တွေကို လျှော့ချပေးနိုင်တယ်။
- Asynchronous method တွေက exception ကို traditional try/catch နဲ့ဖမ်းလို့မရပါဘူး၊ ဖမ်းချင်ရင် callback method ထဲမှာဖမ်းရပါတယ်။ ReactiveX ရဲ့ error handling operator တွေက ဒီနေရာမှာ အတော်အသုံးဝင်ပါတယ်။
- Concurrency အတွက် manually ရေးနေဖို့မလိုတော့ပါဘူး။
- Observable, subscriber နဲ့ operator က independent ဖြစ်တဲ့အတွက် reusable ဖြစ်ပါတယ်။။

မကောင်းတာကတော့  reactive style ဖြစ်တဲ့အတွက်  imperative style ပဲရေးနေကျ ကျွန်တော်တို့အတွက် စစချင်းမှာနားလည်ရခက်ပါတယ်။ :P
