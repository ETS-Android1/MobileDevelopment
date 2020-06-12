## Разработка мобильных приложений

Вне зависимости от используемой технологии (Android Studio/Xcode, React Native/Flutter, Qt, Xamarin), существуют общие принципы разработки мобильных приложений.

Начинать разработку приложения следует с планирования навигации, учитывающей модель информационной безопасности, хранилища состояния приложения и определения API для взаимодействия с внешними серверными компонентами.

## Навигация

Современное приложение состоит из ряда отдельных экранов (Activities), переходы между которыми позволяют получать доступ к функциям приложения. Экраны группируются некоторым способом. Под навигацией подразумеваются переходы как внутри группы, так и между группами.
Чаще всего применяются три навигационных компонента: Side Drawer, Stack и Bottom Tab (или просто Tab).

**Stack Navigator** определяет связь, в которой есть родительские и дочерние экраны. Дочерние экраны всегда располагаются поверх родительских и это обеспечивает возможность можно вернуться к родительскому экрану (например, используя кнопку «Back»). В Header-е Stack-а часть размещается иконка "Back" для возврата к предыдущему экрану (с поясняющим текстом), а также дополнительные иконки-кнопки, такие как "Confirm" и "Save."

**Tab Navigator** определяет группу экранов одного уровня между которыми можно свободно перемещаться посредством жеста swipe, либо щелчком на закладку (Tab).

**Side Drawer** определяем меню верхнего уровня, позволяя выполнить переходы на функционально не связанные, либо слабо-связанные экраны. Обычно именно Side Drawer является компонентом верхнего уровня.

Также может применяться **Switch Navigator** – особый тип экрана, который проверяет, была ли выполнена аутентификация пользователем и в зависимости от результата, перенаправляет пользователя в главное меню, либо на экран аутентификации. Впрочем, в React Navigation v5 компонент Switch Navigator уже не используется.

## Хранилище состояния

Состояние – это любые данные, которые разделяются между различными экранами, из которых состоит приложение. Состояние может хранить *JSON Web Token* для доступа к внешним ресурсам, список валют, которыми оперирует приложение, корзина с товарами, и т.д. Данные в хранилище сохраняются при переходе от экрана к экрану.

Часто, работа с хранилищем предполагает возникновение некоторого действия пользователя, которое транслируется в слой бизнес логики, сохраняется глобально, а затем сообщение об изменении транслируется подписчиках. Например, в модели **Redux Store** действие пользователя приводит к возникновению действия (**Action**), которое может выполнить асинхронный запрос на сервер (например, с использованием [Redux-Thunk](https://github.com/reduxjs/redux-thunk)), результат действия попадает в **Reducer**-ы (при вызове функции **dispatch**()), каждый из которых может зафиксировать результат, или часть результата в свой контейнер данных, а затем подписчики (экраны) получают сообщение об изменении данных в хранилище, посредством хука **useSelector**().

Можно считать, что глобальное хранилище это и Model, и Controller в модели MVC – оно выполняет некоторую логику и хранит данные не зависимо от представления (View).

В React Native чаще всего применяется компонент [Redux Store](https://github.com/reduxjs/react-redux), но его постепенно заменяет [Context API](https://ru.reactjs.org/docs/context.html), который считается более простым для понимания и более совершенным.
 
## Пользовательский интерфейс

Для мобильных телефонов определены специальные «правила хорошего тона», к которым, например, относится *pull to update* – жест «перемещение пальца с верхней части экрана вертикально вниз» приводит к обновлению данных в списке (они повторно загружаются с сервера).

Для обеспечения удобного доступа к органам управления, большинство форм поддерживает скроллирование.

Для того, чтобы избежать перекрытия строки ввода, или другого органа управления системными элементами (чёлка, камера) используются специальные вспомогательные компоненты, такие как **SafeAreaView** в React Native.

## Доступ к Native-функциями

По мере появления новых возможностей в операционных системах, возникает необходимость доступа к этим функциями из «*not native code*». Например, для управления вибрацией и работой с биометрическим сенсором может потребоваться доступ к Native API.

В случае Qt, доступ к Android API осуществляется посредством **Java Native Interface**.

### Специализированные протоколы

Потенциально, большей проблемой для гибридных приложений (React Native, Flutter, Qt), чем доступ к Native Functions операционной системы, может являеться возможность использования специализированных протоколов асинхронного взаимодействия, таких как [SignalR](https://docs.microsoft.com/ru-ru/aspnet/core/signalr/java-client?view=aspnetcore-3.1) и [gRPC](https://grpc.io/docs/quickstart/android/). Для обоих протоколов есть библиотеки для Java-клиента, которые работают "из коробки". Для обоих протоколов есть прекрасные JavaScript-реализации для использования в браузере, но далеко не факт, что эти библиотеки можно использовать без адаптации в React Native. Вопрос их поддержки в Flutter и Qt - отдельная исследовательская задача.

## Серверные serverless технологии

Во многих случаях, разработчикам мобильных приложений не требуется сложный backend. Может оказаться, что вполне достаточно возможности аутентификации пользователей, возможности сохранить какие-то структрурированный данные в базу (иногда достаточно пары ключ-значение), а также сохранения данных в файловое хранилище (типа S3). В подобных ситуациях разрабатывать специализированное серверное решение может оказаться изыточным.

Решением задачи может быть **serverless backend**. Наиболее типовыми решениями являются: [Google Firebase](https://firebase.google.com/), [Parse Server](https://parseplatform.org/), [AWS Lambda](https://aws.amazon.com/ru/lambda/).

Для Firebase реализовано множество библиотек подключения к серверу для разных инструментальных средств. Ещё одна исключительно важная особенность - интеграция с Google Analytics, что позволяет, например, транслировать из приложения в облако информацию о возникших ошибках в приложении, что влияет на оперативность поиска и устранения сбоев.

Parse Server - разработка, которая была куплена Facebook, но затем "отпущена" в open-source. В [AWS](https://aws.amazon.com/marketplace/pp/Bitnami-Parse-Server-Certified-by-Bitnami/B01BLQ17TO) есть образ Parse Server от [Bitnami](https://bitnami.com/), который може6т быть развёрнут в облаке AWS за пару минут.

AWS Lambda - промежуточный уровень, описываемый как набор функций в облаке AWS, разработанных на разных языках программирования и подходит для потоковой обработки данных.

Похожая на Firebase платформа с поддержкой GraphQL- [8Base](https://www.8base.com/). Доступна [статья](https://www.8base.com/blog/5-reasons-why-developers-are-choosing-8base-over-firebase) о сравнении Firebase и 8Base.

### Основные проблемы serverless backend

К основным проблемам можно отнести REST API и NoSQL.

REST API создаёт жёсткие связи требуя разные endpoints (шаблоны http-запросов) для разных запросов. Если используются разные команды для разработки front-end и back-end, то необходимо координировать любые изменения. Ответы на запросы в REST API часто содержат избыточные данные.

GraphQL имеет ряд преимуществ, например, допускает любые запросы и *mutations* в endpoint. Запросы формируются на клиентской стороне и, следовательно, зависимость от разработчиков back-end значительно снижена. GraphQL возвращает только те данные, которые действительно нужно при выполнении конкретной операции.

NoSQL действительно работают очень быстро при получении данных, но у NoSQL есть огромные проблемы с поддержкой релационной модели и многие вещи, такие как группировка с коррелирующими запросами в NoSQL выполнить нельзя. Для бизнес-проектов это представляет существенный риск.

## Лучшие/худшие свойства инструментальных средств

### Android Studio

Очень удобный IDE. Превосходные возможности отладки кода. IDE позволяет решить, буквально, любые задачи - от локализации до анализа производительности. Прекрасная реализация IntelliSense.

Можно выбрать основной язык программирования: Java/Kotlin.

Высокропроизводительный код можно разработать на C++ 14/17.

Великоленый выбор вспомогательных библиотек: [Square Retrofit](https://square.github.io/retrofit/), [SignalR](https://docs.microsoft.com/ru-ru/aspnet/core/signalr/java-client?view=aspnetcore-3.1).

Порог вхождения - очень высокий. Количество работы выполняемое вручную - большое.

### Qt/QML

Ориентирован на разработчиков C++.

Потенциально - очень высокая скорость скомпилированного C++ 14/17 кода. QML разработан с использованием OpenGL, т.е. работа тоже должна быть очень производительной.

Явным образом добавляется сборка openSSL, что позволяет утверждать, что в случае сетевых уязвимостей меньше, чем у других подходах.

Большой набор вспомогательных библиотек: работа с hardware, WiFi/Bluetooth, SQLite, и т.д.

Интеграция кода C++ с Java/Swift работает надёжно, но [требует использования](https://github.com/Kerminator1973/Qt5JNI2Android) **JNI** (для Android). В худшем случае, приложение требует хороших навыков программирования на C++, Java/Kotlin/Swift и JavaScript.

В случае запуска на микропроцессорах с разной архитектурой (x86 и ARM - сейчас, а в будущем - ARM, RISC-V, MIPS) - нужно делать bundle под каждую платформу, либо super-bundle, в котором есть скомпилированный код для всех платформ.

Сложность разработки - высокая.

### React Native

Низкий порог вхождения на React-программистов и программистов для которых JavaScript-родной. Порог вхождения для программистов на практикующих JavaScript - высокий.

Парадигма программирования очень похожа на разработку web-приложения на React.

Огромное количество готовых компонентов.

Удобный вспомогательный инструментарий, например, "горячая" перезагрузка приложения в эмуляторе при изменении кода - эта возможность доступна в [Expo](https://expo.io/).

При использовании **Expo Managed Workflow**, дистрибьюция приложений возможно без оплаты аккаунта разработчика Google Play Market, или Apple AppStore. Разработка под iOS возможна без компьютера с macOS и без iPhone, см.: [Snack](https://snack.expo.io/).

Высокая скорость разработки пользовательского интерфейса, но инфраструктурые задачи (например, использование SQLite, или многопоточность) - в зачаточном состоянии.

Относится к типу **Hybrid Mobile Application** - приложение может работать медленнее, чем **Native App**.

Отладка кода довольно сложная и неудобная. Добавление Native-функций возможно в **React Native CLI** и **Expo Bare Workflow** - это требует отличного знания Android Studio/Java/Kotlin и/или Xcode/Swift.

## Типовая последовательность разработки приложения

1. Проектирование экранов и создание их прототипов
2. Разработка прототипа навигационной системы
3. Разработка класса "глобальное состояние приложения"
4. Реализация коммуникационного взаимодействия и offline-кода
5. Допиливание пользовательского интерфейса
6. Встаривание Native-функций платформы
7. Подготовка к дистрибьюции

## Ближайшее будущее

Начиная с версии 0.62 в эко-системе React Native появился новый инструмент отладки кода, под названием [Flipper](https://fbflipper.com/).

В конце 2020 года, React Native перейдёт на новую архитектуру - об этом статья Rémi Gallego под названием [React Native's re-architecture in 2020](https://medium.com/swlh/react-natives-re-architecture-in-2020-9bb82659792c). Ключевые улучшения:

1. Render Engine (JSI) может быть заменён на более производительный, например, на **V8**
2. Появилась возможность вызывать native С++ код из основного потока JavaScript
3. Появится гораздо больше возможностей для использоавния многопоточного кода

В конце 2020 года планируется выпуск [новой версии Qt 6](https://www.qt.io/blog/qt-roadmap-for-2020). В числе прочего - поддержка C++ 20. Расширенная поддержка QML и Python. Значительно улучшается поддержка CMake.
