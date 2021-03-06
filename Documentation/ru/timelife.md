# Время жизни

## Зачем?
У любого объекта в программе есть время жизни. Для упрощения работы с объектами, выделено несколько сценариев работы с объектами. Это дает возможность не следить самому за временем жизни объекта, а отдать эту ответственно модулю.

## Стандартное (Default)
В DITranquillity если не объявлено время жизни то используется Scope. Более подробное описание можно найти ниже.

## Одиночки (Single)
Если мы хотим чтобы объект с момента его создания существовал в единственном экземпляре во всей программе, то в этом нам поможет время жизни "Одиночка". Для того чтобы объявить что объект данного типа должен быть единственным в программе, можно написать следующий код:
```Swift
builder.register(Cat.self).instanceSingle()...
```
В данном коде мы говорим, что существует всего единственный "кот" в программе, и он будет создан в момент "сборки".

## Отложенные одиночки (LazySingle)
Еслиже мы хотим чтобы объект создал по первому обращению, то тут нам поможет LazySingle. Для его объявления можно написать следующий код:
```Swift
builder.register(Cat.self).instanceLazySingle()...
```
В данном коде мы говорим, что существует всего единственный "кот" в программе, и будет он создан в момент первого обращения (первого разрешение зависимостей) для кота.

## Область видимости (Scope)
Если мы хотим чтобы объект существовал в единственном экземпляре но пока существует scope, а в момент разрушения scope он былбы уничтожен, то для этого можно написать следующий код:
```Swift
builder.register(Cat.self).instancePerScope()...
```
После сборки приложения мы имеем всегда один scope. Этот scope передается автоматически в инициализаторы объекта. Если же мы хотим породить новый scope, то мы может написать так: `let newscope = scope.newLifeTimeScope()` - мы создали новый scope внутри которого нету никакой информации о созданных объектах. К примеру для нашего кота мы можем написать следующий код:
```Swift
let cat1: Cat = scope.resolve()
let cat1e: Cat = scope.resolve()
let newscope = scope.newLifeTimeScope()
let cat2: Cat = newscope.resolve()
let cat2e: Cat = newscope.resolve()
```
В данном коде объекты cat1 и cat1e, а также cat2 и cat2e будут попарно равны, но cat1 не будет равен cat2, так как они были созданны в разных scope. 
При этом пока существует scope объект точно не будет уничтожен, но после того как scope был удален, то объект не обязательно уничтожится, если на него есть ссылки в программе.

## Всегда новый (Dependency)
Пожалую самый простой вид времени жизни - каждый раз когда мы запрашиваем объект создается новый. Для объявления такого времени жизни можно написать следующий код:
```Swift
builder.register(Cat.self).instancePerDependency()...
```
Теперь каждый раз когда мы будет запрашивать кота, у нас будет выдаваться новый объект.

## За создание отвечает кто-то другой (Request)
Данный вид жизни означает следующее: сам объект создается кемто другим, но после его создания в него внедряются зависимости. Соответственно время его жизни похоже на время жизни Dependency, но начинается оно слегка раньше. Объекты с таким временем жизни могут не иметь инициализаторов. За частую такое время жизни надо объявлять для всех ViewController, так-как создает их storyboard. Чтобы объявить такое время жизни, можно написать следующий код:
```Swift
builder.register(MyViewController.self).instancePerRequest()
```
