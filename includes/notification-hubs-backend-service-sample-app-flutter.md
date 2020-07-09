---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 2f8cd5e554980225850780cc474106c3dc69288a
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147826"
---
### <a name="create-the-flutter-solution"></a>Criar a solução Flutter

1. Abra uma nova instância do **Código do Estúdio Visual.**

1. Abra a **paleta de comando** **(Comando de turno**  +  **Command**  +  **P).**

1. Selecione o **Flutter: Novo Comando do Projeto** e, em seguida, prima **Enter**.

1. Introduza *push_demo* para o **Nome** do Projeto e, em seguida, selecione uma **localização do Projeto**.

1. Quando for solicitado, escolha **Obter Pacotes**.

1. **Controlo**  +  **Clique** na pasta **kotlin** (em **app**  >  **src**  >  **main),** em seguida, escolha **Reveal in Finder**. Em seguida, mude o nome das pastas para crianças (sob a pasta **kotlin)** para ```com``` , e ```<your_organization>``` ```pushdemo``` respectivamente.

    > [!NOTE]
    > Ao utilizar o modelo **visual Studio Code** estas pastas predefinição para **com**, **exemplo,** **<project_name>**. Assumindo que **o mobcat** é utilizado para a **organização,** a estrutura da pasta deve aparecer indicativamente como:
    >
    > - kotlin
    >     - com
    >         - mobcat
    >             - pushdemo

1. De volta ao **Código do Estúdio Visual,** atualize o valor **da aplicaçãoId** em **android**  >  **app**  >  **build.gradle** to `com.<your_organization>.pushdemo` .

    > [!NOTE]
    > Devias usar o teu próprio nome de organização para o espaço reservado *<your_organization>.* Por exemplo, usar **o mobcat** como a organização resultará num valor de **nome de pacote** de *com.mobcat.pushdemo*.

1. Atualizar o atributo **pacote** nos ficheiros **AndroidManifest.xml,** sob o perfil **SRC**  >  **Debug**, **SRC**  >  **Main**e **SRC,**  >  **profile** respectivamente. Certifique-se de que os valores correspondem à **aplicaçãoId** utilizada no passo anterior.

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. Atualize o ```android:label``` atributo no ficheiro **AndroidManifest.xml** no **src**  >  **principal** para *PushDemo*. Em seguida, adicione o ```android:allowBackup``` atributo, diretamente em ```android:label``` , definindo o seu valor para **falso**.

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. Abra o ficheiro **build.gradle** **(android**  >  **app**  >  **build.gradle),** em seguida, atualize o *compileSdkVersion* (a partir da secção **android)** para utilizar a API **29**. Em seguida, atualize os *valores minSdkVersion* e *targetSdkVersion* (da secção **padrãoConfig),** para **26** e **29,** respectivamente.

    > [!NOTE]
    > Apenas os dispositivos com **nível API de nível 26 ou superior** são suportados para efeitos deste tutorial, no entanto pode alargá-lo para dispositivos de suporte que executam versões mais antigas.

1. **Controlo**  +  **Clique** na pasta **ios** e, em seguida, escolha **Abrir no Xcode**.

1. No **Xcode,** clique no **Runner** (o **xcodeproj** na parte superior, não na pasta). Em seguida, selecione o alvo **Runner** e selecione o **separador Geral.** Com a configuração de construção **All** selecionada, atualize o **Identificador de Pacote** para `com.<your_organization>.PushDemo` .

    > [!NOTE]
    > Devias usar o teu próprio nome de organização para o espaço reservado *<your_organization>.* Por exemplo, a utilização **de mobcat** como organização resultará num valor **de identificador de bundle identifier** de *com.mobcat.PushDemo*.

1. Clique em **Info.plist** e, em **seguida,** atualize o valor do nome do Pacote para **PushDemo**

1. Feche **o Xcode** e regresse ao **Código do Estúdio Visual.**

1. De volta ao **Visual Studio Code**, open **pubspec.yaml,** adicione os **pacotes** [http](https://pub.dev/packages/http) e [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) Dart como dependências. Em seguida, guarde o ficheiro e clique em **Obter Pacotes** quando solicitado para fazê-lo.

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. No **Terminal,** mude o diretório para a pasta **ios** (para o seu projeto Flutter). Em seguida, execute o comando [**de instalação da pod**](https://guides.cocoapods.org/using/getting-started.html#installation) para instalar novas cápsulas (exigidas pela embalagem [flutter_secure_storage).](https://pub.dev/packages/flutter_secure_storage)

1. **Controlo**  +  **Clique** na pasta **de lib** e, em seguida, escolha **Novo Ficheiro** no menu usando *main_page.dart* como nome de ficheiro. Em seguida, adicione o seguinte código.

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. Em **main.dart**, substitua o código modelo pelo seguinte.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. No **Terminal,** construa e execute a aplicação em cada plataforma-alvo para testar a aplicação modelo executada no(s) dos seus dispositivos. Certifique-se de que os dispositivos suportados estão ligados.

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>Implementar os componentes de plataforma cruzada

1. **Controlo**  +  **Clique** na pasta **lib** e, em seguida, escolha **Nova Pasta** no menu utilizando os *modelos* como nome de **pasta**.

1. **Controlo**  +  **Clique** na pasta dos **modelos** e, em seguida, escolha **Novo Ficheiro** no menu usando *device_installation.dart* como nome de ficheiro. Em seguida, adicione o seguinte código.

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. Adicione um novo ficheiro à pasta dos **modelos** chamada *push_demo_action.dart* que define a enumeração de ações que estão a ser apoiadas neste exemplo.

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. Adicione uma nova pasta aos **serviços chamados serviços** do projeto e adicione um novo ficheiro à pasta chamada *device_installation_service.dart* com a seguinte implementação.

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > Devias usar o teu próprio nome de organização para o espaço reservado *<your_organization>.* Por exemplo, a utilização **de mobcat** como organização resultará num nome [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) de *com.mobcat.pushdemo/deviceinstallation*.
    >
    > Esta classe encapsula trabalhar com a plataforma nativa subjacente para adquirir os detalhes necessários de instalação do dispositivo. Um [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) facilita a comunicação assíncrona bidirecional com as plataformas nativas subjacentes. A contrapartida específica da plataforma para este canal será criada nos passos posteriores.

1. Adicione outro ficheiro a essa pasta chamada *notification_action_service.dart* com a seguinte implementação.

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Isto é usado como um mecanismo simples para centralizar o manuseamento das ações de notificação para que possam ser manuseados de forma transversal usando uma enumeração fortemente dactilografada. O serviço permite que a plataforma nativa subjacente desencadeie uma ação, quando uma é especificada na carga útil de notificação. Também permite que o código comum verifique retrospetivamente se uma ação foi especificada durante o lançamento da aplicação uma vez que o Flutter esteja pronto para processá-lo. Por exemplo, quando a aplicação é lançada tocando numa notificação do centro de notificações.

1. Adicione um novo ficheiro à pasta **de serviços** chamada *notification_registration_service.dart* com a seguinte implementação.

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Esta classe engloba a utilização do **DeviceInstallationService** e os pedidos ao serviço de backend para executar as ações de registo, desregista e atualização necessárias. O argumento **apiKey** só é necessário se optar por completar os [clientes Authenticate utilizando uma secção chave API.](#authenticate-clients-using-an-api-key-optional)

1. Adicione um novo ficheiro à pasta **lib** chamada *config.dart* com a seguinte implementação.

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > Isto é usado como uma forma simples de definir segredos de aplicativos. Substitua os valores do espaço reservado pelos seus. Devia ter anotar isto quando construiu o serviço de backend. O **URL da App API** deve ser `https://<api_app_name>.azurewebsites.net/` . O membro **apiKey** só é necessário se optar por completar os [clientes Authenticate utilizando uma secção chave API.](#authenticate-clients-using-an-api-key-optional)
    >
    > Certifique-se de adicionar isto ao seu ficheiro gitignore para evitar que estes segredos sejam comprometidos com o controlo de fontes.

### <a name="implement-the-cross-platform-ui"></a>Implementar a UI transversal

1. Em **main_page.dart,** substitua a função **de construção** pelo seguinte.

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. Adicione as importações necessárias ao topo do ficheiro **main_page.dart.**

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. Adicione um campo à classe **_MainPageState** para armazenar uma referência ao **Serviço de NotificaçãoRegistration**.

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. Na aula **de _MainPageState,** implemente os manipuladores de eventos para os botões **Registar** e **Desregister** em Eventos **Comprimidos.** Ligue para **Register**os / métodos**correspondentes de registo Deregister** e, em seguida, apresente um alerta para indicar o resultado.

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. Agora, em **main.dart,** certifique-se de que as seguintes importações estão presentes no topo do processo.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. Declare uma variável para armazenar referência a um caso de **NotificationActionService** e inicializá-lo.

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. Adicione funções para lidar com a visualização de um alerta quando uma ação é acionada.

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. Atualize a função **principal** para observar o **actionSggered actionSse** **actionTriggered** e verificar se há ações capturadas durante o lançamento da aplicação.

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > Isto é simplesmente para demonstrar a receção e propagação de ações de notificação de push. Normalmente, estes seriam manuseados silenciosamente, por exemplo, navegando para uma visão específica ou refrescando alguns dados em vez de exibir um alerta neste caso.
