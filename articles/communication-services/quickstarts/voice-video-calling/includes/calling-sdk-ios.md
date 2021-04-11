---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 479aa522462d14f295177e6b2d2fcc4707657760
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498795"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Um recurso dos Serviços de Comunicação Azure implantado. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um token de acesso do utilizador para ativar o cliente da chamada. [Obtenha um token de acesso ao utilizador](../../access-tokens.md).
- Opcional: Complete a chamada de voz Add para o arranque rápido da [sua aplicação.](../getting-started-with-calling.md)

## <a name="set-up-your-system"></a>Configurar o seu sistema

### <a name="create-the-xcode-project"></a>Criar o projeto Xcode

No Xcode, crie um novo projeto iOS e selecione o modelo **de Aplicação Single View.** Este quickstart utiliza a [estrutura SwiftUI,](https://developer.apple.com/xcode/swiftui/)por isso deve definir o **Idioma** para **Swift** e **User Interface** para **SwiftUI**. 

Não vai criar testes de unidade ou testes de UI durante este arranque rápido. Sinta-se livre para limpar os **testes de unidade incluir** e incluir caixas de texto **testes de UI.**

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Screenshot que mostra a janela para criar um projeto dentro do Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instale o pacote e dependências com cacau

1. Crie um Podfile para a sua aplicação, assim:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

2. Execute `pod install`.
3. Abra `.xcworkspace` com Xcode.

### <a name="request-access-to-the-microphone"></a>Solicitar acesso ao microfone

Para aceder ao microfone do dispositivo, é necessário atualizar a lista de propriedades de informação da sua aplicação com `NSMicrophoneUsageDescription` . Definiu o valor associado a um `string` que será incluído no diálogo que o sistema utiliza para solicitar o acesso do utilizador.

Clique com o botão direito na `Info.plist` entrada da árvore do projeto e selecione Open **As**  >  **Source Code**. Adicione as seguintes linhas na secção de nível superior e, em `<dict>` seguida, guarde o ficheiro.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Abra o ficheiro *ContentView.swift* do seu projeto e adicione uma `import` declaração ao topo do ficheiro para importar a `AzureCommunicationCalling` biblioteca. Além disso, `AVFoundation` importa. Vai precisar para pedidos de autorização de áudio no código.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="learn-the-object-model"></a>Aprenda o modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características dos Serviços de Comunicação Azure Chamando SDK para iOS.

> [!NOTE]
> Este quickstart utiliza a versão 1.0.0-beta.8 do Call SDK.


| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `CallClient` | `CallClient` é o principal ponto de entrada para o Call SDK.|
| `CallAgent` | `CallAgent` é usado para iniciar e gerir chamadas. |
| `CommunicationTokenCredential` | `CommunicationTokenCredential` é usado como credencial simbólica para `CallAgent` instantaneamente.| 
| `CommunicationIdentifier` | `CommunicationIdentifier` é usado para representar a identidade do utilizador. A identidade pode `CommunicationUserIdentifier` `PhoneNumberIdentifier` ser, ou `CallingApplication` . . |

> [!NOTE]
> Quando a aplicação implementa delegados de eventos, tem de ter uma forte referência aos objetos que requerem subscrições de eventos. Por exemplo, quando um `RemoteParticipant` objeto é devolvido ao invocar o método e a `call.addParticipant` aplicação define o delegado para `RemoteParticipantDelegate` ouvir, a aplicação deve ter uma forte referência ao `RemoteParticipant` objeto. Caso contrário, se este objeto for recolhido, o delegado lançará uma exceção fatal quando o Call SDK tentar invocar o objeto.

## <a name="initialize-callagent"></a>Inicialize CallAgent

Para criar um `CallAgent` exemplo a partir de , você tem que usar um método que `CallClient` `callClient.createCallAgent` assíncronamente devolve um `CallAgent` objeto após a sua inicial.

Para criar um cliente de chamada, tem que passar um `CommunicationTokenCredential` objeto.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
var userCredential: CommunicationTokenCredential?
   do {
       userCredential = try CommunicationTokenCredential(with: CommunicationTokenRefreshOptions(initialToken: token, 
                                                                     refreshProactively: true,
                                                                     tokenRefresher: self.fetchTokenSync))
   } catch {
       return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Passe o `CommunicationTokenCredential` objeto que criou para , e `CallClient` desaje o nome de exibição.

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()!
options.displayName = " iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Fazer uma chamada de saída

Para criar e iniciar uma chamada, precisa de ligar para uma das APIs `CallAgent` e fornecer a identidade dos Serviços de Comunicação de um utilizador que forneceu utilizando o SDK de Gestão de Serviços de Comunicação.

A criação de chamadas e o início são sincronizados. Receberá uma instância de chamada que lhe permite subscrever todos os eventos da chamada.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Coloque uma chamada 1:1 para um utilizador ou uma chamada 1:n com utilizadores e PSTN

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Coloque uma chamada de 1:n com utilizadores e PSTN
Para colocar a chamada para a PSTN, tem de especificar um número de telefone adquirido com os Serviços de Comunicação.

```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier(identifier: 'UserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-video"></a>Coloque uma chamada de 1:1 com vídeo
Para obter uma instância do gestor de dispositivos, consulte a secção sobre [a gestão de dispositivos](#manage-devices).

```swift

let camera = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUserIdentifier(identifier: 'UserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Junte-se a uma chamada de grupo
Para se juntar a uma chamada, você precisa ligar para um dos APIs on `CallAgent` .

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string"))!
let call = self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions())

```

### <a name="subscribe-to-an-incoming-call"></a>Subscreva uma chamada recebida
Subscreva um evento de chamada recebida.

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func onIncomingCall(_ callAgent: CallAgent!, incomingcall: IncomingCall!) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func onCallEnded(_ incomingCall: IncomingCall!, args: PropertyChangedEventArgs!) {
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>Aceite uma chamada recebida
Para aceitar uma chamada, ligue para o `accept` método num objeto de chamada. Definir um delegado para `CallAgent` .

```swift
final class CallHandler: NSObject, CallAgentDelegate
{
    public var incomingCall: Call?
 
    public func onCallsUpdated(_ callAgent: CallAgent!, args: CallsUpdatedEventArgs!) {
        if let incomingCall = args.addedCalls?.first(where: { $0.isIncoming }) {
            self.incomingCall = incomingCall
        }
    }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions) { (call, error) in
               if error == nil {
                   print("Incoming call accepted")
               } else {
                   print("Failed to accept incoming call")
               }
           }
} else {
   print("No incoming call found to accept")
}
```

## <a name="set-up-push-notifications"></a>Configurar notificações push

Uma notificação de push móvel é a notificação pop-up que obtém no dispositivo móvel. Para ligar, vamos focar-nos nas notificações push VoIP (voice over Internet Protocol). 

As seguintes secções descrevem como se registar, manusear e não registar notificações push. Antes de iniciar essas tarefas, complete estes pré-requisitos:

1. Em Xcode, vá para **assinar & Capacidades**. Adicione uma capacidade selecionando **+ capacidade** e, em seguida, selecione **Notificações push**.
2. Adicione outra capacidade selecionando **+ Capacidade** e, em seguida, selecione **Modos de Fundo**.
3. Nos **modos de fundo**, selecione as caixas de verificação de voz sobre **IP** e **remote.**

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Screenshot que mostra como adicionar capacidades no Xcode." lightbox="../media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>Registe-se para notificações push

Para se registar para notificações push, ligue `registerPushNotification()` para um caso com um `CallAgent` token de registo do dispositivo.

O registo de notificações push tem de ser registado após a inicialização bem sucedida. Quando o `callAgent` objeto for destruído, `logout` será chamado, o que irá automaticamente não registar notificações push.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

### <a name="handle-push-notifications"></a>Lidar com notificações push
Para receber notificações push para chamadas recebidas, ligue `handlePushNotification()` para um caso com uma carga útil do `CallAgent` dicionário.

```swift

let callNotification = IncomingCallInformation.from(payload: pushPayload?.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
}

```
### <a name="unregister-push-notifications"></a>Notificações push nãoregister

As aplicações podem não registar a notificação de push a qualquer momento. Basta ligar para o `unregisterPushNotification` `CallAgent` método.

> [!NOTE]
> As aplicações não são automaticamente registadas a partir de notificações push em logout.

```swift

callAgent.unregisterPushNotifications { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
}

```

## <a name="perform-mid-call-operations"></a>Realizar operações de chamada a meio

Pode efetuar várias operações durante uma chamada para gerir definições relacionadas com vídeo e áudio.

### <a name="mute-and-unmute"></a>Mudo e desajeitado

Para silenciar ou desativar o ponto final local, pode utilizar as `mute` `unmute` APIs assíncronos e assíncronos.

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

Utilize o seguinte código para descodificá-lo de forma assíncronea.

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

### <a name="start-and-stop-sending-local-video"></a>Comece e pare de enviar vídeo local

Para começar a enviar vídeo local para outros participantes numa chamada, use a `startVideo` API e passe `localVideoStream` com `camera` .

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

Depois de começar a enviar vídeo, a `LocalVideoStream` informação é adicionada à `localVideoStreams` coleção numa instância de chamada.

```swift

call.localVideoStreams[0]

```

Para parar o vídeo local, passe o `localVideoStream` caso devolvido da invocação de `call.startVideo` . Esta é uma ação assíncronea.

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="manage-remote-participants"></a>Gerir participantes remotos

Todos os participantes remotos são representados pelo `RemoteParticipant` tipo e estão disponíveis através da `remoteParticipants` coleção numa instância de chamada.

### <a name="list-participants-in-a-call"></a>Listar os participantes numa chamada

```swift

call.remoteParticipants

```

### <a name="get-remote-participant-properties"></a>Obtenha propriedades de participantes remotos

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision a token for another user
var identity = remoteParticipant.identity

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Adicione um participante a uma chamada

Para adicionar um participante a uma chamada (seja um utilizador ou um número de telefone), pode invocar `addParticipant` . Este comando irá retornar sincronizadamente uma instância de participante remoto.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Remova um participante de uma chamada
Para remover um participante de uma chamada (seja um utilizador ou um número de telefone), pode invocar a `removeParticipant` API. Isto resolverá assíncronos.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Torne os streams de vídeo remotos dos participantes

Os participantes remotos podem iniciar a partilha de vídeos ou ecrãs durante uma chamada.

### <a name="handle-video-sharing-or-screen-sharing-streams-of-remote-participants"></a>Lidar com fluxos de partilha de vídeo ou partilha de ecrãs de participantes remotos

Para listar os fluxos de participantes remotos, inspecione as `videoStreams` coleções.

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="get-remote-video-stream-properties"></a>Obtenha propriedades de fluxo de vídeo remoto

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-streams"></a>Torne os fluxos de participantes remotos

Para começar a fazer streams remotos de participantes, utilize o seguinte código.

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="get-remote-video-renderer-methods-and-properties"></a>Obtenha métodos e propriedades de renderizador de vídeo remoto

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="manage-devices"></a>Gerir dispositivos

`DeviceManager` permite enumerar dispositivos locais que podem ser usados numa chamada para transmitir streams de áudio ou vídeo. Também lhe permite solicitar permissão a um utilizador para aceder a um microfone ou câmara. Pode aceder `deviceManager` ao `callClient` objeto.

```swift

self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>Enumerar dispositivos locais

Para aceder a dispositivos locais, pode utilizar métodos de enumeração no gestor do dispositivo. A enumeração é uma ação sincronizada.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras! // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.microphones! // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.speakers! // [AudioDeviceInfo, AudioDeviceInfo...]
``` 

### <a name="set-the-default-microphone-or-speaker"></a>Desafine o microfone ou o altifalante predefinidos

Pode utilizar o gestor do dispositivo para definir um dispositivo predefinido que será utilizado quando uma chamada for iniciada. Se os predefinições da pilha não forem definidos, os Serviços de Comunicação recorrerão aos padrãos de SISTEMA.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.cameras!.first
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.speakers!
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="get-a-local-camera-preview"></a>Obtenha uma pré-visualização da câmara local

Pode usar `Renderer` para começar a fazer um fluxo a partir da sua câmara local. Este fluxo não será enviado para outros participantes; É um feed de pré-visualização local. Esta é uma ação assíncronea.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = try renderer!.createView()

```

### <a name="get-local-camera-preview-properties"></a>Obtenha propriedades de pré-visualização de câmaras locais

O renderizador tem um conjunto de propriedades e métodos que lhe permitem controlar a renderização.

```swift

// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [RendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="subscribe-to-notifications"></a>Subscrever as notificações

Pode subscrever a maioria das propriedades e coleções para ser notificada quando os valores mudarem.

### <a name="properties"></a>Propriedades
Para subscrever `property changed` eventos, utilize o seguinte código.

```swift
call.delegate = self
// Get the property of the call state by getting on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Coleções
Para subscrever `collection updated` eventos, utilize o seguinte código.

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
