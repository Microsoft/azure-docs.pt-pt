---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c8271cc4de558ec5c97f41d6a070f71a9fc49cd2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060499"
---
### <a name="create-the-react-native-solution"></a>Criar a solução Reagir Nativa

1. Em `Terminal` , atualizar as suas ferramentas ambientais, necessárias para trabalhar com a React Native utilizando os seguintes comandos:

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. Em `Terminal` , executar o seguinte comando, se tiver `React Native` CLI instalado para desinstalá-lo. Utilize `npx` para aceder automaticamente à versão CLI nativa mais recente disponível:

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > React Native tem uma interface de linha de comando incorporada. Em vez de instalar e gerir uma versão específica do CLI a nível global, recomendamos que aceda à versão atual em tempo de execução utilizando `npx` , que navios com Node.js. Com `npx react-native <command>` , a versão estável atual do CLI será descarregada e executada no momento em que o comando é executado.

1. Navegue para a pasta de projetos onde pretende criar a nova aplicação. Utilize o modelo baseado em Typescript especificando o `--template` parâmetro:

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. Executar o servidor do metrô, que constrói pacotes JavaScript e monitoriza quaisquer atualizações de código para refrescar os pacotes em tempo real:

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. Execute a aplicação iOS para verificar a configuração. Certifique-se de que iniciou um simulador iOS ou ligou um dispositivo iOS, antes de executar o seguinte comando:

    ```bash
    npx react-native run-ios
    ```

1. Execute a aplicação Android para verificar a configuração. Requer alguns passos adicionais para configurar um emulador ou dispositivo Android para poder aceder ao servidor de metrô React Native. Os seguintes comandos geram o pacote JavaScript inicial para Android e colocam-no na pasta de ativos.

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    Este script será pré-implementado com a versão inicial da app. Uma vez implantado, configufique o seu emulador ou dispositivo para aceder ao servidor do metrô especificando o endereço IP do servidor. Execute o seguinte comando para construir e executar a aplicação Android:

    ```bash
    npx react-native run-android
    ```

    Uma vez na aplicação, bata `CMD+M` (emulador) ou agite o dispositivo para povoar as definições do desenvolvedor, navegue `Settings`  >  `Change Bundle Location` para , e especifique o endereço ip do servidor do metrô com a porta predefinido: `<metro-server-ip-address>:8081` .

1. No `App.tsx` ficheiro, aplique qualquer alteração no layout da página, guarde-a e faça com que a alteração seja automaticamente refletida tanto nas aplicações iOS como android.

    > [!NOTE]
    > Guia de configuração detalhada do ambiente de desenvolvimento está disponível na [documentação oficial](https://reactnative.dev/docs/environment-setup)

### <a name="install-required-packages"></a>Instalar pacotes necessários

Precisa dos seguintes três pacotes para que esta amostra funcione:

1. [Reagir notificações de push nativo iOS](https://www.npmjs.com/package/@react-native-community/push-notification-ios)  -  [Projeto GitHub](https://github.com/react-native-community/push-notification-ios)

    Este pacote foi criado quando o PushNotificationIOS foi separado do núcleo de React Native. O pacote implementa de forma nativa notificações push para iOS e fornece interface React Native para aceder ao mesmo. Executar o seguinte comando para instalar a embalagem:

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [Reagir Notificações de Push Nativo Plataforma transversal](https://www.npmjs.com/package/react-native-push-notification)

    Este pacote implementa notificações locais e remotas no iOS e Android de forma transversal. Executar o seguinte comando para instalar a embalagem:

    ```bash
    yarn add react-native-push-notification
    ```

1. [Pacote de informações do dispositivo](https://www.npmjs.com/package/react-native-device-info) O pacote fornece informações sobre um dispositivo em tempo de execução. Utilize-o para definir um identificador de dispositivo, que é utilizado para se registar para notificação push. Executar o seguinte comando para instalar a embalagem:

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>Implementar os componentes de plataforma cruzada

1. Criar e `DemoNotificationHandler` implementar:

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. Criar e `DemoNotificationService` implementar:

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. Criar e `DemoNotificationRegistrationService` implementar:

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. Configure a aplicação. Abra `package.json` e adicione a seguinte definição de script:

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    Em seguida, execute este script, que copiará o config padrão para a `config` pasta.

    ```bash
    yarn configure
    ```

    O passo final é atualizar o ficheiro de configuração copiado na etapa anterior com as informações de acesso da API. Especificar `apiKey` e `apiUrl` parâmetros:

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>Implementar a UI transversal

1. Definir layout de página

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. Aplicar estilos

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. Inicialize o componente da página

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. Defina manipuladores de cliques de botão

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. Cabo recebeu registos simbólicos e notificações push

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```