# Java 8 Language Features For Android
Java 8 မှာ developer တွေအတွက် အလွန်အသုံးဝင်တဲ့ feature အသစ်တွေပါလာပါတယ်(တစ်ချို့ တွေက အခြား language တွေမှာပါပြီးသားပါ)။ ဒါပေမဲ့ Java 8 ရဲ့ feature တွေအကုန်လုံးကို Android မှာ အထောက်အပံ့မပေးသေးပါဘူး။ Android မှာအထောက်အပံ့ပေးထားတဲ့ Java 8 feature တွေကတော့
* [Default and static interface methods](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html) (only for API level 24 and higher)
* [Lambda expressions](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html) (also available on API level 23 and lower)
* [Repeatable annotations](https://docs.oracle.com/javase/tutorial/java/annotations/repeating.html) (only for API level 24 and higher)
* [Method References](https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html) (also available on API level 23 and lower)
* [Type Annotations](https://docs.oracle.com/javase/tutorial/java/annotations/type_annotations.html) (also available on API level 23 and lower)

တွေပါ။ Android မှာ အဲ့ဒီ feature တွေကိုသုံးနိုင်ဖို့အတွက် [Jack](https://source.android.com/source/jack.html) လို့ခေါ်တဲ့ compiler အသစ်လိုပါတယ်။ Jack ကို သုံးဖို့အတွက် Android Studio 2.1 ဒါမှမဟုတ် အဲ့ဒီထက်မြင့်တဲ့ version လိုပါတယ်။ ပြီးရင် Jack compiler နဲ့ Java 8 အတွက် module-level *build.gradle* file မှာ

```java
android {
  ...
  defaultConfig {
    ...
    jackOptions {
      enabled true
    }
  }
  compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
  }
}
```

ဆိုပြီး configure လုပ်ပေးရပါမယ်။

## Default And Static Interface Methods
Java 8 မတိုင်ခင်က Interface အတွင်းမှာ static final variable နဲ့ abstract method တွေပဲရေးလို့ရပါတယ်။ ပြဿနာက implement လုပ်မဲ့ class တွေအတွက် override မလုပ်ပဲသုံးလို့ရမဲ့ common function တွေ ထည့်ပေးလိုက်လို့မရတော့ပါဘူး။ method တစ်ခုထည့်မယ်ဆိုတာနဲ့ abstract method အနေနဲ့ပဲထည့်လို့ရတဲ့အတွက် implenent လုပ်မဲ့ class တိုင်းက အဲ့ဒီ method ကို redefine ပြန်လုပ်ရပါမယ်။ implement လုပ်တဲ့ class တိုင်းက မလိုလားအပ်ပဲ  same code ကိုပဲ ထပ်ခါထပ်ခါရေးနေရမှာပါ။ Abstract class ကို သုံးရင်ရတာပဲဆိုပေမည့် Java မှာက single inheritance ပဲရတော့ အခြား class တစ်ခုကို extend လုပ်လို့မရတော့ပါဘူး။ Misleading လည်းဖြစ်တဲ့အတွက် မလုပ်သင့်ပါဘူး။

Java 8 မှာတော့ Interface ထဲမှာ static method နဲ့ default method ထည့်ရေးလို့ရလာပါပြီ။ Static method ကတော့ ရိုးရိုး static method နဲ့အတူတူပဲဖြစ်ပြီး helper function အနေနဲ့သုံးလို့ရမှာပါ။ Default method ကလည်း class member function လိုပါပဲ၊ implement လုပ်တဲ့ class တိုင်းမှာ ယူသုံးလို့ရမှာပါ။

```java
interface SampleInterface {

    static int add(int first, int second) {

        return first + second;
    }

    @RequiresApi(api = Build.VERSION_CODES.N)
    default int getAddition(int first, int second) {

        return DefaultAndStaticInterfaceMethodInterface
            .add(first, second);
    }
}
```

Default method ပါတဲ့ interface ကို extends လုပ်တဲ့ interface မှာလည်း super ရဲ့ default method ကို inherit လုပ်မှာဖြစ်ပြီး၊ redeclare ပြန်လုပ်ရင်တော့ abstract method ဖြစ်သွားမှာပါ။ Redefine လုပ်ပြီး override လုပ်လိုက်လို့လဲရပါတယ်။

## Lambda Expression
[Objects collection](https://docs.oracle.com/javase/7/docs/api/java/util/Collections.html) တစ်ခုကို sorting စီဖို့အတွက် [Comparable<T>](https://docs.oracle.com/javase/7/docs/api/java/lang/Comparable.html) interface နဲ့ [Comparator<T>](https://docs.oracle.com/javase/7/docs/api/java/util/Comparator.html) interface ထဲကတစ်ခုခုကိုသုံးပါတယ်။ ဒါပေမဲ့ Comparator<T> interface ကိုပိုပြီးအသုံးများပါတယ်၊ criteria အမျိုးမျိုးအတွက် comparator အမျိုးမျိုး သတ်မှတ်ထားနိုင်လို့ပါ။ ဥပမာ

```java
public class Person {
    private String name;
    private int age;
    ...
}
List<Person> personList = new ArrayList();
personList.add(new Person("Person A", 20));
personList.add(new Person("Person B", 12));
personList.add(new Person("Person C", 26));
personList.add(new Person("Person D", 43));
personList.add(new Person("Person E", 16));
```

*personList* collection ကို *age* member variable ပေါ်မူတည်ပြီး sorting စီဖို့ဆိုရင်

```java
Collections.sort(
        personList,
        new Comparator<Person>() {
            
            @Override
            public int compare(Person firstPerson, 
                               Person secondPerson) {
                
                return firstPerson.getAge() - 
                    secondPerson.getAge();
            }
        });
```

Comparator interface ကို [anonymous class](https://docs.oracle.com/javase/tutorial/java/javaOO/anonymousclasses.html) အနေနဲ့ implement လုပ်ပြီး *Collections.sort()* ထဲကို ထည့်ပေးလိုက်တာပါ။ *compare()* function က person object နှစ်ခုရဲ့ *age* variableကို နှိုင်းယှဉ်ရုံသက်သက်ပါ။ အဲဒီ anonymous class ကို lambda expressionသုံးပြီး ဒီလို simplified လုပ်လို့ရပါတယ်။

```java
Collections.sort(
        personList, 
        (p1, p2) -> p1.getAge() - p2.getAge());
```

**->** ရဲ့အရှေ့ပိုင်းက parameter ဖြစ်ပြီး အနောက်ပိုင်းက expression ပါ။ Interface ရဲ့ abstract method က return ပြန်ဖို့လိုတယ်ဆိုရင် expression ရဲ့ result က return value ဖြစ်သွားမှာပါ။

Functional interface ကို anonymous class အနေနဲ့ implement လုပ်တဲ့နေရာမှာ lambda expression ကိုသုံးလို့ရပါတယ်။ Functional interface ဆိုတာ abstract method တစ်ခုထဲပါတဲ့ interface ကိုပြောတာပါ။ Default method နဲ့ abstract method တွေကတော့ ပါကောင်းပါနိုင်ပါတယ်။

Lambda expression က code ကို simplified ဖြစ်စေတာ တစ်ခုထဲမဟုတ်ပါဘူး။ Inner class တွေရဲ့ [memory leak](https://blogs.oracle.com/olaf/entry/memory_leaks_made_easy) ဖြစ်တဲ့ ပြဿနာကိုလဲ ဖြေရှင်းနိုင်ပါတယ်။ Inner class instance က enclosing class instance ကို strong reference လုပ်ထားတဲ့အတွက် inner class reference ကို garbage collect မလုပ်နိုင်သေးသ၍ enclosing class instance ကလည်း garbage collect မလုပ်နိုင်တဲ့အတွက် memory leak ဖြစ်နိုင်ပါတယ်။ Lambda expression ကတော့ enclosing class instance ရဲ့ member variable ကို reference မလုပ်သ၍ enclosing class instance ကိုလည်း reference မလုပ်ပါဘူး။

Android မှာ Java 8 ကို enable မလုပ်ပဲနဲ့ lambda expression ကိုသုံးမယ်ဆိုရင်တော့ [Retrolambda](https://github.com/evant/gradle-retrolambda) ကိုသုံးလို့ရပါတယ်။

## Method References
Lambda expression ကနေ existing method တစ်ခုကို ခေါ်တဲ့အခါမှာ ကို သုံးလို့ရပါတယ်။

```java
class PersonCompareHelper {
    public static int compareByAge(Person a, Person b) {
        return a.birthday.compareTo(b.birthday);
    }
}
```

*compareByAge()* static method ကို lambda expression ထဲမှာသုံးမယ်ဆိုရင်

```java
(p1, p2) -> PersonCompareHelper.compareByAge(p1, p2);
```

အစား

```java
PersonCompareHelper::compareByAge
```
ဆိုပြီး သုံးလို့ရပါတယ်။

*(p1, p2) -> PersonCompareHelper.compareByAge(p1, p2)* နဲ့ *PersonCompareHelper::compareByAge* က semantic အရအတူတူပါပဲ။
* *Comparator<Person>.compare()* ကနေရတဲ့ parameter list က (Person, Person) ပါ။
* Body မှာ *PersonCompareHelper.compareByAge(Person, Person)* static method ကို ခေါ်ထားပါတယ်။

Method reference (၄)မျိုးရှိပါတယ်။
* Reference to a static method (ContainingClass::staticMethodName)
* Reference to an instance method of a particular object (containingObject::instanceMethodName)
* Reference to an instance method of an arbitrary object of a particular (ContainingType::methodName)
* Reference to a constructor (ClassName::new)

တို့ပါ။

## Last But Not Least
Android DEX file ရအောင် javac toolchain က build လုပ်တဲ့ပုံစံနဲ့ Jack toolchain က build လုပ်တဲ့ပုံစံက မတူပါဘူး။

* Legacy javac toolchain က
**javac** (.java → .class) → **dx** (.class → .dex)
* New Jack toolchain က
**Jack** (.java → .jack → .dex) ဆိုပြီး build လုပ်ပါတယ်။

Intermediate class file ကို generate မလုပ်တော့တဲ့အတွက် အခု Android Studio 2.2.2 နဲ့ Android Gralde Plugin 2.2.0 ထွက်ပြီးချိန်ထိ [Instant Run](https://developer.android.com/tools/building/building-studio.html#instant-run) အလုပ်မလုပ်ပါဘူး။ Class file တွေကို detect လုပ်တဲ့ lint detector တွေ အလုပ်မလုပ်ပါဘူး။ Class file တွေကိုလိုတဲ့ tool တွေ library တွေ အလုပ်လုပ်မှာမဟုတ်ပါဘူး။ အဆိုးဆုံးက [Android Data Binding Library](https://developer.android.com/topic/libraries/data-binding/index.html) ကို သုံးလို့မရတော့တာပါ။ build.gradle file မှာ data binding ကို config လုပ်လိုက်တာနဲ့
```
Error:Data Binding does not support Jack builds yet
```
ဆိုတဲ့ error တက်ပါလိမ့်မယ်။ နောက်လာမည့် Gradle version တွေမှာတော့ အဲ့ဒီပြဿနာတွေကို ဖြေရှင်းနိုင်မယ်လို့ မျှော်လင့်ရပါတယ်။

## References
* https://developer.android.com/guide/platform/j8-jack.html
* https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html
* https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html#approach1
* http://stackoverflow.com/questions/28446626/do-java8-lambdas-maintain-a-reference-to-their-enclosing-instance-like-anonymous\
* http://cr.openjdk.java.net/~briangoetz/lambda/lambda-state-final.html
* https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html