---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 3eadf556d467fb67be0b0fe3a83dfd4ed89e6463
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488130"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Um recurso de Serviços de Comunicação implantado. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- A `User Access Token` para ativar o cliente de chamada. Para mais informações sobre [como obter um `User Access Token` ](../../access-tokens.md)
- Opcional: Complete o quickstart para [começar com a adição de chamadas à sua aplicação](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configuração

### <a name="install-the-package"></a>Instale o pacote

> [!NOTE]
> Este documento utiliza a versão 1.0.0-beta.8 da biblioteca do cliente chamador.

<!-- TODO: update with instructions on how to download, install and add package to project -->
Localize o seu nível de projeto build.gradle e certifique-se de adicionar `mavenCentral()` à lista de repositórios em `buildscript` e `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Em seguida, no seu nível de módulo build.gradle adicionar as seguintes linhas à secção de dependências

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}

```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca de clientes Azure Communication Services Call:

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| O CallClient é o principal ponto de entrada para a biblioteca do cliente Call.|
| Callagent | O CallAgent é usado para iniciar e gerir chamadas. |
| ComunicadoTokenCredential | O CommunicationTokenCredential é usado como credencial simbólica para instantaneaizar o CallAgent.|
| Identificador de Comunicação | O Comunicador de Comunicação é usado como diferente tipo de participante que poderia fazer parte de uma chamada.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>Inicialize o CallClient, crie um CallAgent e aceda ao DeviceManager

Para criar um `CallAgent` caso, tem que chamar o `createCallAgent` método em um `CallClient` caso. Este assíncrona devolve um `CallAgent` objeto de exemplo.
O `createCallAgent` método toma como `CommunicationUserCredential` argumento, que encapsula um símbolo de [acesso](../../access-tokens.md).
Para aceder ao `DeviceManager` , deve ser criada uma instância callAgent primeiro, e depois pode utilizar o método para obter o `CallClient.getDeviceManager` DeviceManager.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent((appContext, tokenCredential).get();
DeviceManage deviceManager = callClient.getDeviceManager().get();
```
Para definir um nome de exibição para o chamador, utilize este método alternativo:

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
CallAgent callAgent = callClient.createCallAgent((appContext, tokenCredential, callAgentOptions).get();
DeviceManage deviceManager = callClient.getDeviceManager().get();
```


## <a name="place-an-outgoing-call-and-join-a-group-call"></a>Faça uma chamada de saída e junte-se a uma chamada de grupo

Para criar e iniciar uma chamada, precisa de ligar para o `CallAgent.startCall()` método e fornecer `Identifier` o(s) callee(s).
Para se juntar a uma chamada de grupo, precisa de ligar para o `CallAgent.join()` método e fornecer o groupId. Os IDs do grupo devem estar no formato GUID ou UUID.

A criação de chamadas e o início são sincronizados. A instância de chamada permite-lhe subscrever todos os eventos da chamada.

### <a name="place-a-11-call-to-a-user"></a>Coloque uma chamada 1:1 para um utilizador
Para fazer uma chamada a outro utilizador dos Serviços de Comunicação, invoque o `call` método e passe um objeto com `callAgent` `communicationUserId` chave.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Coloque uma chamada de 1:n com utilizadores e PSTN
> [!WARNING]
> Atualmente a chamada pstn não está disponível

Para escamar uma chamada 1:n para um utilizador e um número PSTN, tem de especificar o número de telefone do callee.
O seu recurso de Serviços de Comunicação deve ser configurado para permitir a chamada pstn:
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>Coloque uma chamada de 1:1 com câmara de vídeo
> [!WARNING]
> Atualmente apenas um stream de vídeo local de saída é suportado Para fazer uma chamada com vídeo você tem que enumerar câmaras locais usando a `deviceManager` `getCameras` API.
Uma vez selecionada uma câmara desejada, use-a para construir um `LocalVideoStream` caso e passá-la `videoOptions` como um item na matriz para um `localVideoStream` `call` método.
Assim que a chamada ligar, começará automaticamente a enviar um fluxo de vídeo da câmara selecionada para outros participantes.

> [!NOTE]
> Devido a questões de privacidade, o vídeo não será partilhado na chamada se não estiver a ser pré-visualizado localmente.
Consulte [a pré-visualização da câmara local](#local-camera-preview) para mais detalhes.
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameras().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.startCall(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>Junte-se a uma chamada de grupo
Para iniciar uma nova chamada de grupo ou aderir a uma chamada de grupo em curso, você tem que chamar o método de 'join' e passar um objeto com uma `groupId` propriedade. O valor tem de ser um GUID.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

### <a name="accept-a-call"></a>Aceite uma chamada
Para aceitar uma chamada, ligue para o método "aceitar" num objeto de chamada.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

Para aceitar uma chamada com câmara de vídeo em:

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

A chamada recebida pode ser obtida subscrevendo o `onIncomingCall` evento no `callAgent` objeto:

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```


## <a name="push-notifications"></a>Notificações push

### <a name="overview"></a>Descrição Geral
As notificações de push mobile são as notificações pop-up que vê nos dispositivos móveis. Para ligar, vamos focar-nos nas notificações push VoIP (Voice over Internet Protocol). Registaremos notificações push, lidaremos com notificações push e, em seguida, descamuramos notificações push.

### <a name="prerequisites"></a>Pré-requisitos

Uma conta Firebase criada com mensagens cloud (FCM) ativada e com o seu serviço de mensagens cloud firebase ligado a uma instância do Azure Notification Hub. Consulte [as notificações dos Serviços de Comunicação](../../../concepts/notifications.md) para obter mais informações.
Além disso, o tutorial assume que está a usar a versão 3.6 ou superior do Android Studio para construir a sua aplicação.

É necessário um conjunto de permissões para a aplicação Android para poder receber mensagens de notificações de Mensagens Cloud Firebase. No seu `AndroidManifest.xml` ficheiro, adicione o seguinte conjunto de permissões logo após o *manifesto<...>* ou abaixo da *</application>* etiqueta

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>Registe-se para notificações push

Para se registar para notificações push, o pedido precisa de recorrer a `registerPushNotification()` uma instância *CallAgent* com um token de registo do dispositivo.

Para obter o token de registo do dispositivo, adicione a biblioteca do cliente Firebase ao ficheiro *build.gradle* do seu módulo de aplicação, adicionando as seguintes linhas na `dependencies` secção se ainda não estiver lá:

```
    // Add the client library for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

No ficheiro *build.gradle* do seu nível de projeto, adicione o seguinte na `dependencies` secção se ainda não estiver lá:

```
    classpath 'com.google.gms:google-services:4.3.3'
```

Adicione o seguinte plugin ao início do ficheiro se ainda não estiver lá:

```
apply plugin: 'com.google.gms.google-services'
```

*Selecione Sync Now* na barra de ferramentas. Adicione o seguinte corte de código para obter o símbolo de registo do dispositivo gerado pela biblioteca de clientes de Mensagens Cloud Firebase para a instância de aplicação do cliente Certifique-se de adicionar as importações abaixo ao cabeçalho da atividade principal, por exemplo. São necessários para que o corte recupere o símbolo:

```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Adicione este corte para recuperar o símbolo:

```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w("PushNotification", "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d("PushNotification", "Device Registration token retrieved successfully");
                    }
                });
```
Registe o sinal de registo do dispositivo junto da biblioteca de clientes call services para notificações push de chamadas recebidas:

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>Tratamento de notificação push

Para receber notificações push de chamada recebida, ligue para *handlePushNotification()* numa instância *CallAgent* com uma carga útil.

Para obter a carga útil da Firebase Cloud Messaging, comece por criar um novo Serviço (Serviço de > New > Service >) que alarga a classe de biblioteca de clientes *FirebaseMessagingService* e substitui o `onMessageReceived` método. Este método é o manipulador de eventos chamado quando a Firebase Cloud Messaging entrega a notificação push à aplicação.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
Adicione a seguinte definição de serviço ao `AndroidManifest.xml` ficheiro, dentro da <application> etiqueta:

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- Uma vez recuperada a carga útil, pode ser transmitida para a biblioteca de clientes *dos Serviços de Comunicação* para ser analisada num objeto interno *de IncomingCallInformation* que será manuseado através da chamada do método *handlePushNotification* numa instância *CallAgent.* Um `CallAgent` caso é criado chamando o método na `createCallAgent(...)` `CallClient` classe.

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Quando o manuseamento da mensagem de notificação Push for bem sucedido e todos os manipuladores de eventos estiverem registados corretamente, a aplicação tocará.

### <a name="unregister-push-notifications"></a>Notificações push nãoregister

As aplicações podem não registar a notificação de push a qualquer momento. Chame o `unregisterPushNotification()` método de chamada Para não registar.

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>Gestão de Chamadas
Pode aceder às propriedades de chamadas e executar várias operações durante uma chamada para gerir as definições relacionadas com vídeo e áudio.

### <a name="call-properties"></a>Propriedades de chamada

Obtenha o ID único para esta chamada:

```java
String callId = call.getId();
```

Para conhecer outros participantes na recolha de chamadas, inspecione `remoteParticipant` a recolha sobre o `call` caso:

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

A identidade do chamador se a chamada estiver a chegar:

```java
CommunicationIdentifier callerId = call.getCallerId();
```

Obtenha o estado da chamada: 

```java
CallState callState = call.getState();
```

Devolve uma corda que representa o estado atual de uma chamada:
* 'Nenhum' - estado de chamada inicial
* 'Connecting' - estado de transição inicial uma vez que a chamada é feita ou aceite
* 'Ringing' - para uma chamada de saída - indica que a chamada está a tocar para participantes remotos
* 'EarlyMedia' - indica um estado em que um anúncio é reproduzido antes da chamada ser conectada
* 'Connected' - chamada está ligada
* 'LocalHold' - a chamada é colocada em espera pelo participante local, nenhum meio de comunicação está fluindo entre o ponto final local e os participantes remotos)
* 'RemoteHold' - a chamada é colocada em espera por um participante remoto, nenhum meio de comunicação flui entre o ponto final local e o(s) participantes remotos)
* 'Disconnecting' - estado de transição antes da chamada ir para o estado 'Desligado'
* 'Desligado' - estado de chamada final


Para saber por que uma ligação terminou, inspecione `callEndReason` a propriedade. Contém código/subcódigo: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Para ver se a chamada atual é uma entrada ou uma chamada de saída, inspecione `callDirection` a propriedade:

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.Incoming for incoming call
// callDirection == CallDirection.Outgoing for outgoing call
```

Para ver se o microfone atual está silenciado, inspecione a `muted` propriedade:

```java
boolean muted = call.getIsMicrophoneMuted();
```

Para ver se a chamada atual está a ser gravada, inspecione a `isRecordingActive` propriedade:

```java
boolean recordinggActive = call.getIsRecordingActive();
```

Para inspecionar os fluxos de vídeo ativos, verifique a `localVideoStreams` recolha:

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>Mudo e desajeitado

Para silenciar ou desativar o ponto final local, pode utilizar as `mute` `unmute` APIs assíncronos e assíncronos:

```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>Comece e pare de enviar vídeo local

Para iniciar um vídeo, tem de enumerar câmaras usando a `getCameraList` API no `deviceManager` objeto. Em seguida, crie um novo exemplo de `LocalVideoStream` passar a câmara desejada, e passá-la na `startVideo` API como argumento:

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);
Future startVideoFuture = call.startVideo(currentLocalVideoStream);
startVideoFuture.get();
```

Assim que começar a enviar vídeos com sucesso, será adicionado um `LocalVideoStream` caso à coleção na instância de `localVideoStreams` chamada.

```java
currentLocalVideoStream == call.getLocalVideoStreams().get(0);
```

Para parar o vídeo local, passe a `LocalVideoStream` instância disponível na `localVideoStreams` coleção:

```java
call.stopVideo(currentLocalVideoStream).get();
```

Pode mudar para um dispositivo de câmara diferente enquanto o vídeo é enviado invocando `switchSource` um `LocalVideoStream` exemplo:
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>Gestão de participantes remotos

Todos os participantes remotos são representados por `RemoteParticipant` tipo e estão disponíveis através da `remoteParticipants` coleção em uma instância de chamada.

### <a name="list-participants-in-a-call"></a>Listar os participantes numa chamada
A `remoteParticipants` coleção devolve uma lista de participantes remotos em chamada dada:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>Propriedades de participantes remotos
Qualquer participante remoto tem um conjunto de propriedades e coleções associadas:

* Pegue o identificador para este participante remoto.
A identidade é um dos tipos de 'Identificador'
```java
CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
```

* Obtenha o estado deste participante remoto.
```java
ParticipantState state = remoteParticipant.getState();
```
Estado pode ser um dos
* 'Ocioso' - estado inicial
* 'EarlyMedia' - o anúncio é jogado antes do participante estar ligado à chamada
* 'Ringing' - chamada do participante está a tocar
* 'Connecting' - estado de transição enquanto o participante está ligado à chamada
* 'Connected' - participante está ligado à chamada
* 'Hold' - participante está em espera
* 'InLobby' - participante está à espera no lobby para ser admitido. Atualmente apenas usado no cenário de interop de equipas
* 'Desligado' - estado final - participante é desligado da chamada


* Para saber por que um participante deixou a chamada, inspecione `callEndReason` a propriedade:
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* Para verificar se este participante remoto está ou não silenciado, inspecione a `isMuted` propriedade:
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* Para verificar se este participante remoto está ou não a falar, inspecione a `isSpeaking` propriedade:
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* Para inspecionar todos os streams de vídeo que um determinado participante está enviando nesta chamada, verifique a `videoStreams` coleção:
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>Adicione um participante a uma chamada

Para adicionar um participante a uma chamada (seja um utilizador ou um número de telefone) pode invocar `addParticipant` . Isto irá retornar sincronizadamente a instância do participante remoto.

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>Remova o participante de uma chamada
Para remover um participante de uma chamada (seja um utilizador ou um número de telefone) pode invocar `removeParticipant` .
Isto resolverá assíncronos assim que o participante for removido da chamada.
O participante também será removido da `remoteParticipants` coleção.
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

## <a name="render-remote-participant-video-streams"></a>Torne os streams de vídeo remotos dos participantes
Para listar os streams de vídeo e a partilha de ecrãs de participantes remotos, inspecione as `videoStreams` coleções:
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Para renderizar um `RemoteVideoStream` de um participante remoto, você tem que subscrever um `OnVideoStreamsUpdated` evento.

Dentro do evento, a mudança de `isAvailable` propriedade para verdadeiro indica que o participante remoto está atualmente a enviar um stream. Uma vez que isso aconteça, crie uma nova instância de um `Renderer` , em seguida, crie uma nova `RendererView` API assíncrono `createView` usando e anexe `view.target` em qualquer lugar na UI da sua aplicação.

Sempre que a disponibilidade de um fluxo remoto muda, pode optar por destruir todo o Renderer, um específico `RendererView` ou mantê-lo, mas isso resultará na exibição de uma moldura de vídeo em branco.

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Propriedades de fluxo de vídeo remoto
Fluxo de vídeo remoto tem algumas propriedades

* `Id` - ID de um fluxo de vídeo remoto
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` - Pode ser 'Video' ou 'ScreenSharing'
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` - Indica se o ponto final do participante remoto está a enviar corrente de fluxo
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>Métodos e propriedades do renderizador
Objeto de renderização após APIs

* Crie um `RendererView` caso que pode ser posteriormente anexado na UI da aplicação para tornar o fluxo de vídeo remoto.
```java
// Create a view for a video stream
renderer.createView()
```
* Elimine o renderizador e tudo `RendererView` o que está associado a este renderizador. Ser chamado quando tiver removido todas as opiniões associadas da UI.
```java
renderer.dispose()
```

* `StreamSize` - tamanho (largura/altura) de um fluxo de vídeo remoto
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>RendererVer métodos e propriedades
Ao criar `RendererView` um, pode especificar as `scalingMode` propriedades e propriedades que se `mirrored` aplicarão a esta vista: O modo de escala pode ser de 'Alongamento' | | 'Crop' 'Fit' Se `mirrored` estiver `true` programado, o fluxo renderizado será virado verticalmente.

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

O RendererView criado pode então ser anexado à UI da aplicação utilizando o seguinte corte:
```java
layout.addView(rendererView);
```

Pode atualizar mais tarde o modo de escala, invocando `updateScalingMode` API no objeto RendererView com um de ScalingMode.Stretch | | de escalamode.crop ScalingMode.Fit como argumento.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>Gestão de dispositivos

`DeviceManager` permite enumerar dispositivos locais que podem ser usados numa chamada para transmitir os seus streams de áudio/vídeo. Também permite solicitar permissão a um utilizador para aceder ao seu microfone e câmera usando a API do navegador nativo.

Você pode aceder `deviceManager` através do método de `callClient.getDeviceManager()` chamada.
> [!WARNING]
> Atualmente um `callAgent` objeto deve ser instantâneo primeiro para ter acesso a DeviceManager

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>Enumerar dispositivos locais

Para aceder a dispositivos locais, pode utilizar métodos de enumeração no Gestor de Dispositivos. A enumeração é uma ação sincronizada.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>Definir microfone/altifalante predefinido

O gestor de dispositivos permite-lhe configurar um dispositivo predefinido que será utilizado ao iniciar uma chamada.
Se os incumprimentos do cliente não forem definidos, os Serviços de Comunicação recorrerão aos padrãos do SISTEMA.

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophones().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakers().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>Pré-visualização da câmara local

Pode utilizar `DeviceManager` e começar a `Renderer` renderizar streams a partir da sua câmara local. Este fluxo não será enviado para outros participantes; É um feed de pré-visualização local. Esta é uma ação assíncronea.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>Modelo de eventos
Pode subscrever a maioria das propriedades e coleções para ser notificada quando os valores mudarem.

### <a name="properties"></a>Propriedades
Para subscrever `property changed` eventos:

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>Coleções
Para subscrever `collection updated` eventos:

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
