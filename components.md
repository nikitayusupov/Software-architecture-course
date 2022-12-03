# Компонентная архитектура
<!-- Состав и взаимосвязи компонентов системы между собой и внешними системами с указанием протоколов, ключевые технологии, используемые для реализации компонентов.
Диаграмма контейнеров C4 и текстовое описание. 
-->
## Компонентная диаграмма

```plantuml
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

AddElementTag("microService", $shape=EightSidedShape(), $bgColor="CornflowerBlue", $fontColor="white", $legendText="microservice")
AddElementTag("storage", $shape=RoundedBoxShape(), $bgColor="lightSkyBlue", $fontColor="white")

Person(admin, "Администратор")
Person(user, "Пользователь")

System_Ext(package, "Посылки")
System_Ext(delivery, "Доставки")

System_Boundary(smart_house, "Сервис доставки") {
   Container(app, "Клиентское веб-приложение", "html, JavaScript", "Сайт сервиса доставки")
   Container(package_service, "Сервис посылок", "C++", "Сервис управлениям посылками", $tags = "microService") 
   Container(client_service, "Сервис авторизации", "C++", "Сервис управления пользователями", $tags = "microService")    
   Container(delivery_service, "Сервис доставок", "C++", "Сервис управления доставками", $tags = "microService")       
   ContainerDb(db, "Данные сервиса доставки", "MySQL", "Хранение данных сайта", $tags = "storage")
}

Rel_D(admin, app, "Добавление/просмотр информации о пользователях")
Rel_D(user, app, "Добавление посылки, оформление доставки")

Rel_D(app, client_service, "Создание пользователя", "http://localhost/person")
Rel_D(client_service, db, "INTSERT/SELECT/UPDATE", "SQL")

Rel_D(app, delivery_service, "Работа с доставками", "http://localhost/delivery")
Rel_L(delivery_service, delivery, "Запрос информации о доставках")
Rel_D(delivery_service, db, "INTSERT/SELECT/UPDATE","SQL")

Rel_D(app, package_service, "Работа с посылками", "http://localhost/package")
Rel(package_service, package, "Запрос информации о посылках")
Rel_D(package_service, db, "INTSERT/SELECT/UPDATE", "SQL")


@enduml
```
## Список компонентов
### Сервис авторизации

**API**:
-	Создание нового пользователя
      - входыне параметры: login, Имя, Фамилия, email, обращение (гн/гжа)
      - выходные параметры, отсутствуют
-	Поиск пользователя по логину
     - входные параметры:  login
     - выходные параметры: Имя, Фамилия, email, обращение (гн/гжа)
-	Поиск пользователя по маске имя и фамилии
     - входные параметры: маска фамилии, маска имени
     - выходные параметры: login, Имя, Фамилия, email, обращение (гн/гжа)

### Сервис посылок
**API**:
- Сохранение информации о новых посылках
  - Входные параметры: login, название посылки
  - Выходные параметры: отсутствуют
- Получение информации о посылках
  - Входные параметры: login
  - Выходные параметры: массив посылок данного пользователя


### Сервис доставок
**API**:
- Сохранение информации о новой доставки
  - Входные параметры: дата, login отправителя, login получателя, список посылок
  - Выходыне параметры: отсутствуют
- Получение информации о доставках отправителя
  - Входные параметры: login отправителя
  - Выходные параметры: массив всех доставок данного отправителя
- Получение информации о доставках получателя
  - Входные параметры: login получателя
  - Выходные параметры: массив всех доставок данного получателя
