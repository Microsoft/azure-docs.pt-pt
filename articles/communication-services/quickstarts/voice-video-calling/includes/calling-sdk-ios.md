---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 7ca15baffd3fac4a1f3635ac7377bac620673446
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91451626"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Um recurso de Serviços de Comunicação implantado. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- A `User Access Token` para ativar o cliente de chamada. Para mais informações sobre [como obter um `User Access Token` ](../../access-tokens.md)
- Opcional: Complete o quickstart para [começar com a adição de chamadas à sua aplicação](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configuração

### <a name="creating-the-xcode-project"></a>Criar o projeto Xcode

No Xcode, crie um novo projeto iOS e selecione o modelo **de Aplicação Single View.** Este quickstart utiliza a [estrutura SwiftUI,](https://developer.apple.com/xcode/swiftui/)por isso deve definir o **Idioma** para **Swift** e a Interface do **Utilizador** para **SwiftUI**. Não vai criar testes de unidade ou testes de UI durante este arranque rápido. Sinta-se livre para desmarcar **Inclua testes de unidade** e também desmarque **inclua testes de UI**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Screenshot mostrando a nova janela do Novo Projeto dentro do Xcode.":::

### <a name="install-the-package"></a>Instale o pacote

Adicione os Serviços de Comunicação Azure Chamando a biblioteca do cliente e suas dependências (AzureCore.framework e AzureCommunication.framework) ao seu projeto.

> [!NOTE]
> Com o lançamento de AzureCommunicationCalling SDK encontrará um roteiro de bash `BuildAzurePackages.sh` . O script quando é executado `sh ./BuildAzurePackages.sh` vai dar-lhe o caminho para os pacotes-quadro gerados que precisa ser importado na aplicação da amostra no passo seguinte. Note que terá de configurar ferramentas de linha de comando Xcode se não o tiver feito antes de executar o script: Iniciar o Xcode, selecione "Preferências -> Localizações". Escolha a sua versão Xcode para as Ferramentas da Linha de Comando. **Note que o BuildAzurePackages.sh script funciona apenas com Xcode 11.5 ou superior.**

1. Descarregue os Serviços de Comunicação Azure Chamando a biblioteca de clientes para iOS.
2. No Xcode, clique no seu ficheiro do projeto e selecione o alvo de construção para abrir o editor de definições do projeto.
3. No separador **Geral,** percorra a secção **Quadros, Bibliotecas e Conteúdo Incorporado** e clique no ícone **"+".**
4. No canto inferior esquerdo do diálogo, escolheu **Add Files,** navegue para o **AzureCommunicationCalling.framework** directy do pacote de biblioteca de clientes sem fecho.
    1. Repita o último passo para adicionar **AzureCore.framework** e **AzureCommunication.framework**.
5. Abra o separador **Definições** de Construção do editor de definições do projeto e percorra para a secção **Caminhos de Busca.** Adicione uma nova entrada **de Caminhos de Busca-Quadro** para o diretório contendo o **AzureCommunicationCalling.framework**.
    1. Adicione outra entrada dos Caminhos de Busca-Quadro apontando para a pasta que contém as dependências.

:::image type="content" source="../media/ios/xcode-framework-search-paths.png" alt-text="Screenshot mostrando a nova janela do Novo Projeto dentro do Xcode.":::

### <a name="request-access-to-the-microphone"></a>Solicitar acesso ao microfone

Para aceder ao microfone do dispositivo, é necessário atualizar a Lista de Propriedades de Informação da sua aplicação com um `NSMicrophoneUsageDescription` . Definiu o valor associado a um `string` que será incluído no diálogo que o sistema utiliza para solicitar o acesso ao utilizador.

Clique com o botão direito na `Info.plist` entrada da árvore do projeto e selecione Open **As**  >  **Source Code**. Adicione as seguintes linhas na secção de nível superior `<dict>` e, em seguida, guarde o ficheiro.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Abra o ficheiro **ContentView.swift** do seu projeto e adicione uma `import` declaração ao topo do ficheiro para importar o `AzureCommunicationCalling library` . Além disso, `AVFoundation` importe, vamos precisar disto para pedido de permissão áudio no código.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características dos Serviços de Comunicação Azure Chamando a biblioteca de clientes para iOS.


| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | O ACSCallClient é o principal ponto de entrada para a biblioteca do cliente Call.|
| ACSCallagent | O ACSCallAgent é utilizado para iniciar e gerir chamadas. |
| ComunicadoUserCredential | O CommunicationUserCredential é usado como credencial simbólica para instantaneaizar o CallAgent.| 
| Identificador de Comunicação | O CommunicationIndentifier é utilizado para representar a identidade do utilizador que pode ser uma das seguintes: CommunicationUser/PhoneNumber/CallApplication. |

> [!NOTE]
> Ao implementar delegados de eventos, a aplicação tem de realizar uma forte referência aos objetos que requerem subscrições de eventos. Por exemplo, quando um `ACSRemoteParticipant` objeto é devolvido ao invocar o método e a `call.addParticipant` aplicação define o delegado para `ACSRemoteParticipantDelegate` ouvir, a aplicação deve ter uma forte referência ao `ACSRemoteParticipant` objeto. Caso contrário, se este objeto for recolhido, o delegado lançará uma exceção fatal quando a chamada SDK tentar invocar o objeto.

## <a name="initialize-the-acscallagent"></a>Inicializar o ACSCallAgent

Para criar um `ACSCallAgent` exemplo a partir de você tem que usar o método que `ACSCallClient` `callClient.createCallAgent` assíncronamente devolve um `ACSCallAgent` objeto uma vez inicializado

Para criar um cliente de chamada, tem que passar um `CommunicationUserCredential` objeto.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(
        initialToken: tokenString, refreshProactively: true,
        tokenRefresher: self.fetchTokenSync
    )
} catch {
    print("Failed to create CommunicationCredential object")
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Passe ComunicaçãoUser Objeto cedente acima para ACSCallClient

```swift

callClient = ACSCallClient()
callClient?.createCallAgent(userCredential!,
    withCompletionHandler: { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Fazer uma chamada de saída

Para criar e iniciar uma chamada, precisa de ligar para uma das APIs `ACSCallAgent` e fornecer a Identidade dos Serviços de Comunicação de um utilizador que forneceu utilizando a biblioteca de clientes de Gestão de Serviços de Comunicação.

A criação de chamadas e o início são sincronizados. Receberá uma chamada que lhe permite subscrever todos os eventos da chamada.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Coloque uma chamada 1:1 para um utilizador ou uma chamada 1:n com utilizadores e PSTN

```swift

let callees = [CommunicationUser(identifier: 'acsUserId')]
let oneToOneCall = self.CallingApp.callAgent.call(participants: callees, options: ACSStartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Coloque uma chamada de 1:n com utilizadores e PSTN
Para colocar a chamada para a PSTN tem de especificar o número de telefone adquirido com os Serviços de Comunicação
```swift

let pstnCallee = PhoneNumber('+1999999999')
let callee = CommunicationUser(identifier: 'acsUserId')
let groupCall = self.CallingApp.callAgent.call(participants: [pstnCallee, callee], options: ACSStartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Coloque uma chamada 1:1 com vídeo
Para obter uma instância de gestor de dispositivos por favor consulte [aqui](#device-management)

```swift

let camera = self.deviceManager!.getCameraList()![0]
let localVideoStream = ACSLocalVideoStream(camera)
let videoOptions = ACSVideoOptions(localVideoStream)

let startCallOptions = ACSStartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUser(identifier: 'acsUserId')
let call = self.callAgent?.call([callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Junte-se a uma chamada de grupo
Para se juntar a uma chamada, precisa ligar para um dos APIs na *CallAgent*

```swift

let groupCallContext = ACSGroupCallContext()
groupCallContext?.groupId = UUID(uuidString: "uuid_string")!
let call = self.callAgent?.join(with: groupCallContext, joinCallOptions: ACSJoinCallOptions())

```

## <a name="push-notification"></a>Notificações push

A notificação de push móvel é a notificação pop-up que obtém no dispositivo móvel. Para chamadas, vamos focar-nos nas notificações push VoIP (Voice over Internet Protocol). Vamos oferecer-lhe as capacidades de se registar para notificação push, para lidar com a notificação push e para não registar a notificação push.

### <a name="prerequisite"></a>Pré-requisito

- Passo 1: > Capacidades de & de assinatura -& de assinatura -> adicionar capacidade -> "Notificações push"
- Passo 2: Capacidades de & de assinatura de > Xcode -> adicionar capacidade -> "Modos de fundo"
- Passo 3: "Modos de fundo" -> Selecione "Voice over IP" e "Remote Notifications"

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Screenshot mostrando a nova janela do Novo Projeto dentro do Xcode." lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>Registar-se para notificações push

Para se registar para notificação push, ligue para o registoPushNotification() numa instância *CallAgent* com um token de registo do dispositivo.

O registo para notificação push deve ser chamado após a inicialização bem sucedida. Quando o `callAgent` objeto for destruído, `logout` serão chamadas notificações de push automaticamente.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken,
                withCompletionHandler: { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
})

```

#### <a name="push-notification-handling"></a>Tratamento de notificações push
Para receber chamadas recebidas notificações push, ligue para *o handlePushNotification()* numa instância *CallAgent* com uma carga útil do dicionário.

```swift

let dictionaryPayload = pushPayload?.dictionaryPayload
callAgent.handlePushNotification(dictionaryPayload, withCompletionHandler: { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
})

```
#### <a name="unregister-push-notification"></a>Notificação push não registro

As aplicações podem não registar a notificação de push a qualquer momento. Basta chamar o `unRegisterPushNotification` método de *CallAgent.*
> [!NOTE]
> As aplicações não são automaticamente registadas a partir da notificação push no logout.

```swift

callAgent.unRegisterPushNotifications(completionHandler: { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
})

```

## <a name="mid-call-operations"></a>Operações de chamada intercalar

Pode efetuar várias operações durante uma chamada para gerir definições relacionadas com vídeo e áudio.

### <a name="mute-and-unmute"></a>Mudo e desajeitado

Para silenciar ou desativar o ponto final local, pode utilizar as `mute` `unmute` APIs assíncronos e assíncronos:

```swift
call.mute(completionHandler: { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
})

```

[Assíncronos] Local unmute

```swift
call.unmute(completionHandler:{ (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
})
```

### <a name="start-and-stop-sending-local-video"></a>Comece e pare de enviar vídeo local

Para começar a enviar vídeo local para outros participantes na chamada, use `startVideo` a API e passe `localVideoStream` com `camera`

```swift

let firstCamera: ACSVideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = ACSLocalVideoStream(firstCamera)

call.startVideo(localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    }
    else {
        print("Local video failed to start")
    }
}

```

Assim que começar a enviar vídeo, a `ACSLocalVideoStream` informação é adicionada à `localVideoStreams` coleção numa instância de chamada:

```swift

call.localVideoStreams[0]

```

[Assíncronos] Para parar o vídeo local, passe o `localVideoStream` devolvido da invocação `call.startVideo` de:

```swift

call.stopVideo(localVideoStream,{ (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    }
    else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>Gestão de participantes remotos

Todos os participantes remotos são representados pelo `ACSRemoteParticipant` tipo e estão disponíveis através da `remoteParticipants` coleção numa instância de chamada:

### <a name="list-participants-in-a-call"></a>Listar os participantes numa chamada

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Propriedades de participantes remotos

```swift

// [ACSRemoteParticipantDelegate] delegate - an object you provide to receive events from this ACSRemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ACSParticipantStateIdle = 0, ACSParticipantStateEarlyMedia = 1, ACSParticipantStateConnecting = 2, ACSParticipantStateConnected = 3, ACSParticipantStateOnHold = 4, ACSParticipantStateInLobby = 5, ACSParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [ACSError] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// ACSRemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [ACSRemoteVideoStream, ACSRemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Adicione um participante a uma chamada

Para adicionar um participante a uma chamada (seja um utilizador ou um número de telefone) pode invocar `addParticipant` . Isto irá retornar sincronizadamente uma instância de participante remoto.

```swift

let remoteParticipantAdded: ACSRemoteParticipant = call.addParticipant(CommunicationUser(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Remova um participante de uma chamada
Para remover um participante de uma chamada (seja um utilizador ou um número de telefone) pode invocar a  `removeParticipant` API. Isto resolverá assíncronos.

```swift

call!.remove(remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Torne os streams de vídeo remotos dos participantes

Os participantes remotos podem iniciar a partilha de vídeos ou ecrãs durante uma chamada.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>Lidar com fluxos de partilha de vídeo/ecrã de participante remoto

Para listar os fluxos de participantes remotos, inspecione as `videoStreams` coleções:

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>Propriedades de fluxo de vídeo remoto

```swift

var type: ACSMediaStreamType = remoteParticipantVideoStream.type // 'ACSMediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Torne o fluxo de participante remoto

Para começar a tornar os fluxos de participantes remotos:

```swift

let renderer: ACSRenderer? = ACSRenderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: ACSRendererView? = renderer?.createView(ACSRenderingOptions(ACSScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(ACSScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Métodos e propriedades de renderizador de vídeo remoto

```swift
// [Bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering()
```

## <a name="device-management"></a>Gestão de dispositivos

`DeviceManager` permite enumerar dispositivos locais que podem ser usados numa chamada para transmitir streams de áudio/vídeo. Também lhe permite solicitar permissão de um utilizador para aceder ao microfone/câmara. Pode aceder `deviceManager` ao `callClient` objeto:

```swift

self.callClient!.getDeviceManager(
    completionHandler: { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    })
```

### <a name="enumerate-local-devices"></a>Enumerar dispositivos locais

Para aceder a dispositivos locais, pode utilizar métodos de enumeração no Gestor de Dispositivos. A enumeração é uma ação sincronizada.

```swift
// enumerate local cameras
var localCameras = deviceManager.getCameraList() // [ACSVideoDeviceInfo, ACSVideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.getMicrophoneList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.getSpeakerList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Definir microfone/altifalante predefinido

O gestor de dispositivos permite-lhe configurar um dispositivo predefinido que será utilizado ao iniciar uma chamada. Se os predefinições da pilha não forem definidos, os Serviços de Comunicação recorrerão aos padrãos de SISTEMA.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.getMicrophoneList()![0]
// [Synchronous] set microphone
deviceManager.setMicrophone(ACSAudioDeviceInfo())
// get first speaker
var firstSpeaker = self.deviceManager!.getSpeakerList()![0]
// [Synchronous] set speaker
deviceManager.setSpeakers(ACSAudioDeviceInfo())
```

### <a name="local-camera-preview"></a>Pré-visualização da câmara local

Pode usar `ACSRenderer` para começar a fazer um fluxo a partir da sua câmara local. Este fluxo não será enviado para outros participantes; É um feed de pré-visualização local. Esta é uma ação assíncronea.

```swift

let camera: ACSVideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: ACSLocalVideoStream = ACSLocalVideoStream(camera)
let renderer: ACSRenderer = ACSRenderer(localVideoStream: localVideoStream)
self.view = renderer!.createView(ACSRenderingOptions())

```

### <a name="local-camera-preview-properties"></a>Propriedades de pré-visualização de câmaras locais

O renderizador tem um conjunto de propriedades e métodos que lhe permitem controlar a renderização:

```swift

// Constructor can take in ACSLocalVideoStream or ACSRemoteVideoStream
let localRenderer = ACSRenderer(localVideoStream:localVideoStream)
let remoteRenderer = ACSRenderer(remoteVideoStream:remoteVideoStream)

// [ACSStreamSize] size of the rendering view
localRenderer.size

// [ACSRendererDelegate] an object you provide to receive events from this ACSRenderer instance
localRenderer.delegate

// [Synchronous] create view with rendering options
localRenderer.createView(options:ACSRenderingOptions())
// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>Modelo de eventos

Pode subscrever a maioria das propriedades e coleções para ser notificada quando os valores mudarem.

### <a name="properties"></a>Propriedades
Para subscrever `property changed` eventos:

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: ACSCall!,
                               _ args: ACSPropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Coleções
Para subscrever `collection updated` eventos:

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: ACSCall!,
                                       _ args: ACSLocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
