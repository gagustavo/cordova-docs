---
license: >
    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

        http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.

title: Cordova для Windows 10
---

# Cordova для Windows 10

Возможно вы вместо этого названия могли бы назвать продукт «Windows 10 для Cordova». У Windows 10 платформа разработки приложений HTML и JavaScript была переработана чтобы принести поддержку Cordova в Интернет и убрать ограничения безопасности платформы с вашего пути.

## Начало работы с Windows 10

Добавление поддержки Windows 10 в ваше приложение настолько же просто, насколько установка вашего целевой платформы Windows значению 10.0:

    <preference name="windows-target-version" value="10.0" />
    <preference name="windows-phone-target-version" value="10.0" />
    

Когда вы собираете с обоими этими параметрами, будет построен только один .appx (и .appxupload). Они потребуют как минимум Windows 10.

### Различия между удаленным режимом и локальным режимом

Удаленный режим — Новая возможность платформы HTML приложений для Windows в Windows 10. В Windows 8 и 8.1 HTML-приложения работали способом, что сейчас называется «Локальный режим» в Windows 10. В локальном режиме HTML приложения имеют полный доступ к Windows API и возможностям платформы. Для того, чтобы предотвратить атаки путем внедрения скриптов, которые могут привести к утечки персональной информации благодаря вредоносному коду, локальный режим запрещает встроенные скрипты и требует от разработчиков, которые выполняют манипуляции над DOM, делать это в контексте (`MSApp.execUnsafeLocalFunction`).

Удаленный режим устраняет эти требования, что делает возможным использовать неизмененные библиотеки такие как jQuery или AngularJS непосредственно в вашем коде, без каких-либо изменений. Для этого, он удаляет вашу способность объявлять определенные возможности при сертификации вашего приложения в магазине Windows. Удаление этих возможностей обычно не мешает получать определенные функции, но может потребоваться использовать сочетание различных API или тактик.

### Влияние удаленного режима на возможности

При развертывании удаленного режима приложения в магазин Windows недоступны следующие возможности:

  * Корпоративная аутентификация (`enterpriseAuthentication`)
  * Общие сертификаты пользователей (`sharedUserCertificates`)
  * Библиотека документов (`documentsLibrary`)
  * Музыкальная библиотека (`musicLibrary`)
  * Библиотека изображений (`picturesLibrary`)
  * Видео библиотека (`videosLibrary`)
  * Съемные ЗУ (`removableStorage`)
  * Интернет клиент/сервер (`internetClientClientServer`) - Обратите внимание, что `internetClient` все еще разрешено
  * Частная сеть клиент/сервер (`privateNetworkClientServer`)

Ограничения на доступ к библиотеке может быть обойдено если пользователь будет взаимодействовать с файловой системой через средство [Выбора файлов](https://msdn.microsoft.com/en-us/library/windows/apps/windows.storage.pickers.fileopenpicker.aspx). Это предотвращает внедренный вредоносный кода от произвольного доступа к файловой системе.

Ограничения, связанные с работой сети могут быть обойдены, используя интерфейс API, который не использует возможность проверки или промежуточного соединения через стандартные каналы связи Интернет, таких как `XMLHttpRequest` или веб-сокеты.

Возможности корпоративной аутентификации и Общие сертификаты пользователей специально ориентированы на корпоративных сценариях. Эти возможности поддерживаются для частных предприятий с поддержкой магазинов приложений, так что если вы создаете приложения, которые будут развертываться с помощью механизм внутреннего развертывания, вы все еще можете поддержать этот функционал. Однако они не поддерживаются для приложений удаленного режима в общедоступном магазине Windows. Когда вы собираете проект ориентируюясь на Windows 10, если одна из этих возможностей будет обнаружена в манифесте вашего приложения, будет отображаться предупреждение.

## Справка

### Параметры config.xml

#### windows-target-version, windows-phone-target-version

    <preference name="windows-target-version" value="10.0" />
    <preference name="windows-phone-target-version" value="10.0" />
    

*По крайней мере один параметр требуется.*

Эти параметры определяют версию Windows или Windows Phone которые вы хотели бы использовать как цель для пакета приложения.

**Допустимые значения**

  * `10.0`, `UAP`: собирает для универсальной платформы приложений Windows 10
  * `8.1`: собирает для Windows 8.1 или Windows Phone 8.1
  * `8.0`: собирает для Windows 8.0. Не действительно для Windows Phone (используйте платформу Cordova **wp8**)

**Сценарии**

Если вы используете только Windows 10, вам только нужно иметь единственный параметр `windows-target-version` в вашем файле config.xml.

#### WindowsDefaultUriPrefix

    <preference name="WindowsDefaultUriPrefix" value="ms-appx://|ms-appx-web://" />
    

Этот параметр определяет, будет ли ваше приложение работать в **локаьльном контексте** или **удаленном** при запуска его стартового URI. При построении для Windows 10, по умолчанию используется удаленный контекст (`ms-appx-web://`).

Для того, чтобы у создать приложение локального режима, которое не затронуто ограничениями удаленного режима, необходимо установить этому параметру значение `ms-appx://` и не объявлять любые элементы `<access>` с удаленным URI.

**Допустимые значения**

  * `ms-appx://` (По умолчанию для Windows 8.0, 8.1): Начальная страница выполняется в локальном контексте
  * `ms-appx-web://` (По умолчанию для Windows 10): Начальная страница выполняется в удаленном контексте

#### {SDK}-MinVersion, {SDK}-MaxVersionTested

*Необязательный*

    <preference name="Windows.Universal-MinVersion" value="10.0.0.0" />
    <preference name="Windows.Mobile-MinVersion" value="10.0.9927.0" />
    <preference name="Windows.Mobile-MaxVersionTested" value="10.0.10031.0" />
    <preference name="Microsoft.Band-MinVersion" value="10.0.11.0" />
    

Эти предпочтения определить с какими экосистемами (включая но не ограничиваясь универсальным Windows, Windows Mobile или Xbox) и с какими их минимальные и максимальные версии обеспечивается совместимость. Все еще требуются, чтобы платформы обладали поддержкой платформы универсальных приложений (таким образом Windows 10 в качестве базовой ОС). Однако это может указывать, что приложение осознает особую функциональность, которая может быть доступна только на некоторых устройствах (например, потоковые игры на Xbox).

**Допустимые значения**

Есть три части для каждого значения: **SDK**, **ограничение версии**и **значение версии**. Эти параметры определяются по значениям начинающимся с `Windows` или `Microsoft` и заканчивающимся ан `-MinVersion` или `-MaxVersionTested`:

  * **SDK** определяет на какие специализированные платформы, вы хотите ориентироваться. Значение по умолчанию — `Windows.Universal`. Допустимые значения для них определены в схеме AppxManifest в элементах `Package/Depednencies/TargetPlatform` .
  * В **ограничении версии** определяются правила совместимости приложений. Например если `-MinVersion` установлено в 10.1.0.0, то те версий ОС, которые не поддерживают по крайней мере версию 10.1.0.0 соответствующего SDK не сможет загрузить его. 
      * `-MinVersion` Указывает минимальную требуемую версию SDK
      * `-MaxVersionTested` указывает наибольшую версию SDK в которой проводилось тестирование. Если выпущена новая версия соответствующего пакета SDK, он будет работать в режиме совместимости для заданной версии.
  * **Значение версии** — кортеж из 4-целых чисел в виде *major.minor.build.qfe*. 

Если никаких параметров данного типа не указано в файле config.xml, то Windows.Universal версии 10.0.0.0 будет выбрано по умолчанию.