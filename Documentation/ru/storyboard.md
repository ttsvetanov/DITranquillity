# Storyboard

## Объявление зависимостей для ViewController
Для объявления viewController в который будут внедрятся зависимости, регистрируем тип с временем жизни `instancePerRequest()`. Выглядит это так:

```Swift
class YourViewController: UIViewController {
  internal var inject: Inject?

  override func viewDidLoad() {
    super.viewDidLoad()
    print("Inject: \(inject)")
  }
}
```

```Swift
builder.register(YourViewController.self)
  .instancePerRequest()
  .dependency { (scope, viewController) in viewController.inject = *!scope }
```

## Создание Storyboard
Чтобы подобные зависимости и внедрения работали автоматически надо создать одну из реализации сторибоарда, которая реализована в библиотеке. Можно сделать так:
```Swift
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
  window = UIWindow(frame: UIScreen.mainScreen().bounds)


  let storyboard = try! DIStoryboard(name: "Main", bundle: nil, container: builder.build())
  window!.rootViewController = storyboard.instantiateInitialViewController()
  window!.makeKeyAndVisible()

  return true
}
```

Для интеграции со сборками, нужно сам Storyboard зарегестрировать в сборке, к примеру вот так:
```Swift
builder.register(UIStoryboard.self)
  .instanceSingle()
  .initializer { scope in DIStoryboard(name: ..., bundle: ..., container: scope) }
```