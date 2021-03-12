---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: e013765579fd560952172166b24f898b354c1d17
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021473"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Um recurso de Serviços de Comunicação implantado. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- A `User Access Token` para ativar o cliente de chamada. Para mais informações sobre [como obter um `User Access Token` ](../../access-tokens.md)
- Opcional: Complete o quickstart para [começar com a adição de chamadas à sua aplicação](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configuração

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

> [!NOTE]
> Este documento utiliza a versão 1.0.0-beta.6 da biblioteca do cliente chamador.

Utilize o `npm install` comando para instalar as bibliotecas de serviços de comunicação Azure e as bibliotecas de clientes comuns para o JavaScript.
Este documento refere-se a tipos na versão 1.0.0-beta.5 da biblioteca de chamadas.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca de clientes Azure Communication Services Call:

| Nome                             | Descrição                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | O CallClient é o principal ponto de entrada para a biblioteca do cliente Call.                                                                       |
| Callagent                        | O CallAgent é usado para iniciar e gerir chamadas.                                                                                            |
| Homem de Dispositivos                    | O DeviceManager é usado para gerir dispositivos de mídia                                                                                           |
| AzureCommunicationTokenCredential | A classe AzureCommunicationTokenCredential implementa a interface CommunicationTokenCredential que é usada para instantaneaizar o CallAgent. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>Inicialize o CallClient, crie CallAgent e aceda a DeviceManager

Instantaneamente um novo `CallClient` caso. Você pode configubá-lo com opções personalizadas como um caso Logger.
Uma vez que um `CallClient` é instantâneo, você pode criar um `CallAgent` caso chamando o método sobre o `createCallAgent` `CallClient` caso. Este assíncrona devolve um `CallAgent` objeto de exemplo.
O `createCallAgent` método toma como `CommunicationTokenCredential` argumento, que aceita um símbolo de acesso [ao utilizador](../../access-tokens.md).
Para aceder a `DeviceManager` uma instância callAgent deve primeiro ser criado. Em seguida, pode utilizar o `getDeviceManager` método no caso para obter o `CallClient` DeviceManager.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Fazer uma chamada de saída

Para criar e iniciar uma chamada, precisa de utilizar uma das APIs na CallAgent e fornecer um utilizador que criou através da biblioteca de clientes de identidade dos Serviços de Comunicação.

A criação de chamadas e o início são sincronizados. A instância de Chamada permite-lhe subscrever eventos de chamadas.

## <a name="place-a-call"></a>Fazer uma chamada

### <a name="place-a-11-call-to-a-user-or-pstn"></a>Coloque uma chamada de 1:1 para um utilizador ou PSTN
Para fazer uma chamada a outro utilizador dos Serviços de Comunicação, invoque o `startCall` método e passe o `callAgent` identificador de comunicação do callee que criou com a biblioteca de [identidade dos serviços de comunicação.](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens)

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Para fazer uma chamada para um PSTN, invoque o `startCall` método e passe o `callAgent` PhoneNumberIdentifier da callee.
O seu recurso de Serviços de Comunicação deve ser configurado para permitir a chamada pstn.
Ao ligar para um número PSTN, tem de especificar o seu ID de chamada alternativo. Um ID de chamada alternativo refere-se a um número de telefone (baseado na norma E.164) que identifica o chamador numa chamada PSTN. Por exemplo, quando fornecer um ID de chamada alternativo à chamada PSTN, esse número de telefone será o mostrado ao callee quando a chamada estiver a chegar.

> [!WARNING]
> A chamada da PSTN está atualmente em pré-visualização privada. Para acesso, [aplique no programa de adoção precoce.](https://aka.ms/ACS-EarlyAdopter)
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Coloque uma chamada de 1:n com utilizadores e PSTN
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Coloque uma chamada de 1:1 com câmara de vídeo
> [!WARNING]
> Atualmente, não pode haver mais do que um fluxo de vídeo local de saída.
Para fazer uma chamada de vídeo, tem de enumerar as câmaras locais utilizando o dispositivo `getCameras()` API.
Uma vez selecionada a câmara desejada, utilize-a para construir um `LocalVideoStream` caso e passe-a para dentro `videoOptions` como um item dentro da matriz para o `localVideoStream` `startCall` método.
Assim que a chamada se ligar, começará automaticamente a enviar um fluxo de vídeo da câmara selecionada para o outro participante. Isto também se aplica às opções de vídeo Call.Accept() e opções de vídeo CallAgent.join..
```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
videoDeviceInfo = cameras[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>Junte-se a uma chamada de grupo
Para iniciar uma nova chamada de grupo ou aderir a uma chamada de grupo em curso, use o método 'join' e passe um objeto com uma `groupId` propriedade. O valor tem de ser um GUID.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```
### <a name="join-a-teams-meeting"></a>Participe de um Encontro de Equipas
Para participar de uma reunião de equipas, use o método de "juntar" e passe um link de reunião ou as coordenadas de uma reunião
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receiving-an-incoming-call"></a>Receber uma chamada recebida

O `CallAgent` caso emite um `incomingCall` evento quando o registrado na identidade está recebendo uma chamada recebida. Para ouvir este evento, inscreva-se da seguinte forma:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //Get information about caller
    var callerInfo = incomingCall.callerInfo
    
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

O `incomingCall` evento fornecerá uma instância `IncomingCall` na qual você pode aceitar ou rejeitar uma chamada.


## <a name="call-management"></a>Gestão de Chamadas

Pode aceder às propriedades de chamadas e executar várias operações durante uma chamada para gerir as definições relacionadas com vídeo e áudio.

### <a name="call-properties"></a>Propriedades de chamada
* Obtenha o ID único (string) para esta Chamada.
```js

const callId: string = call.id;

```

* Para saber mais sobre outros participantes na chamada, inspecione a `remoteParticipant` coleção sobre o `call` caso. Array contém objetos de lista `RemoteParticipant`
```js
const remoteParticipants = call.remoteParticipants;
```

* O identificador de quem ligou se a chamada estiver a chegar. O identificador é um dos `CommunicationIdentifier` tipos
```js

const callerIdentity = call.callerInfo.identifier;

* Get the state of the Call.
```js

const callState = call.state;

```
Isto devolve uma corda que representa o estado atual de uma chamada:
* 'Nenhum' - estado de chamada inicial
* 'Incoming' - indica que uma chamada está a chegar, tem de ser aceite ou rejeitada
* 'Connecting' - estado de transição inicial uma vez que a chamada é feita ou aceite
* 'Ringing' - para uma chamada de saída - indica que a chamada está a tocar para os participantes remotos, é 'Incoming' do seu lado
* 'EarlyMedia' - indica um estado em que um anúncio é reproduzido antes da chamada ser conectada
* 'Connected' - chamada está ligada
* 'LocalHold' - a chamada é colocada em espera pelo participante local, nenhum meio de comunicação está fluindo entre o ponto final local e os participantes remotos)
* 'RemoteHold' - a chamada é colocada em espera por participante remoto, nenhum meio de comunicação está fluindo entre o ponto final local e o(s) participante remoto(s)
* 'Disconnecting' - estado de transição antes da chamada ir para o estado 'Desligado'
* 'Desligado' - estado de chamada final
  * Se a ligação à rede for perdida, o estado vai para 'Desligado' após cerca de 2 minutos.

* Para ver por que uma chamada terminou, inspecione a `callEndReason` propriedade.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Para saber se a chamada atual é uma chamada de entrada ou saída, inspecione a `direction` propriedade, ela devolve `CallDirection` .
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

*  Para verificar se o microfone atual está silenciado, inspecione a `muted` propriedade, ela devolve `Boolean` .
```js

const muted = call.isMicrophoneMuted;

```

* Para ver se o fluxo de partilha de ecrã está a ser enviado a partir de um determinado ponto final, verifique a `isScreenSharingOn` propriedade, ele devolve `Boolean` .
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* Para inspecionar os fluxos de vídeo ativos, verifique a `localVideoStreams` coleção, contém `LocalVideoStream` objetos
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="call-ended-event"></a>Evento encerrado de chamada

O `Call` caso emite um `callEnded` evento quando a chamada termina. Para ouvir este evento inscreva-se da seguinte forma:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>Mudo e desajeitado

Para silenciar ou desativar o ponto final local, pode utilizar as `mute` `unmute` APIs assíncronos e assíncronos:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Comece e pare de enviar vídeo local


Para iniciar um vídeo, tem de enumerar câmaras utilizando o `getCameras` método no `deviceManager` objeto. Em seguida, crie um novo exemplo de `LocalVideoStream` passar a câmara desejada para o método `startVideo` como argumento:


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

Assim que começar a enviar vídeos com sucesso, será adicionado um `LocalVideoStream` caso à coleção numa instância de `localVideoStreams` chamada.

```js

call.localVideoStreams[0] === localVideoStream;

```

Para parar o vídeo local, passe a `localVideoStream` instância disponível na `localVideoStreams` coleção:

```js

await call.stopVideo(localVideoStream);

```

Pode mudar para um dispositivo de câmara diferente enquanto o vídeo é enviado invocando `switchSource` um `localVideoStream` exemplo:

```js
const cameras = await callClient.getDeviceManager().getCameras();
localVideoStream.switchSource(cameras[1]);

```

## <a name="remote-participants-management"></a>Gestão de participantes remotos

Todos os participantes remotos são representados por `RemoteParticipant` tipo e disponíveis através da `remoteParticipants` recolha numa instância de chamada.

### <a name="list-participants-in-a-call"></a>Listar os participantes numa chamada
A `remoteParticipants` coleção devolve uma lista de participantes remotos em chamada dada:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Propriedades de participantes remotos
O participante remoto tem um conjunto de propriedades e coleções associadas a ele
#### <a name="communicationidentifier"></a>Identificador de Comunicação
Pegue o identificador para este participante remoto.
A identidade é um dos tipos de "Identificador de Comunicação":
```js
const identifier = remoteParticipant.identifier;
```
Pode ser um dos tipos de "CommunicationIdentifier":
  * { communicationUserId: '<ACS_USER_ID'> } - objeto que representa o Utilizador ACS
  * { phoneNumber: '<E.164>' } - objeto que representa o número de telefone em formato E.164
  * { microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" } - objeto que representa o utilizador das Equipas

#### <a name="state"></a>Estado
Obtenha o estado deste participante remoto.
```js

const state = remoteParticipant.state;
```
Estado pode ser um dos
* 'Ocioso' - estado inicial
* 'Connecting' - estado de transição enquanto o participante está ligado à chamada
* 'Ringing' - participante está a tocar
* 'Connected' - participante está ligado à chamada
* 'Hold' - participante está em espera
* 'EarlyMedia' - o anúncio é jogado antes do participante estar ligado à chamada
* 'Desligado' - estado final - participante é desligado da chamada
  * Se o participante remoto perder a conectividade da rede, o estado do participante remoto vai para 'Desligado' após cerca de 2 minutos.

#### <a name="call-end-reason"></a>Chamada Por motivo
Para saber por que o participante deixou a chamada, inspecione `callEndReason` a propriedade:
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>É silenciado
Para verificar se este participante remoto é silenciado ou não, inspecione `isMuted` a propriedade, devoluções `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>Está a falar
Para verificar se este participante remoto está ou não a falar, inspecione `isSpeaking` a propriedade que devolve `Boolean`
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>Streams de Vídeo
Para inspecionar todos os streams de vídeo que um determinado participante está enviando nesta chamada, verifique `videoStreams` a recolha, contém `RemoteVideoStream` objetos
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Adicione um participante a uma chamada

Para adicionar um participante a uma chamada (seja um utilizador ou um número de telefone) pode invocar `addParticipant` .
Forneça um dos tipos de "Identificador".
Isto irá retornar sincronizadamente a instância do participante remoto.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>Remova o participante de uma chamada

Para remover um participante de uma chamada (seja um utilizador ou um número de telefone) pode invocar `removeParticipant` .
Tem de passar por um dos tipos de 'Identifier' Isto resolverá assíncronos assim que o participante for removido da chamada.
O participante também será removido da `remoteParticipants` coleção.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Torne os streams de vídeo remotos dos participantes

Para listar os streams de vídeo e a partilha de ecrãs de participantes remotos, inspecione as `videoStreams` coleções:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

Para fazer `RemoteVideoStream` um, tem de subscrever um `isAvailableChanged` evento.
Se a `isAvailable` propriedade mudar `true` para, um participante remoto está enviando um fluxo.
Uma vez que isso aconteça, crie uma nova instância de `Renderer` , e, em seguida, crie um novo `RendererView` exemplo usando o método assíncronos. `createView`  Em seguida, pode `view.target` ligar-se a qualquer elemento UI.
Sempre que a disponibilidade de um fluxo remoto muda, pode optar por destruir todo o Renderer, um específico `RendererView` ou mantê-lo, mas isso resultará na exibição de uma moldura de vídeo em branco.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let renderer: Renderer = new Renderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            renderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Propriedades de fluxo de vídeo remoto
Os streams de vídeo remotos têm as seguintes propriedades:

* `Id` - ID de um fluxo de vídeo remoto
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` - tamanho (largura/altura) de um fluxo de vídeo remoto
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` - pode ser 'Video' ou 'ScreenSharing'
```js
const type: MediaStreamType = remoteVideoStream.mediaStreamType;
```
* `isAvailable` - Indica se o ponto final do participante remoto está a enviar corrente de fluxo
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Métodos e propriedades do renderizador

* Crie um `RendererView` caso que possa ser posteriormente anexado na UI da aplicação para tornar o fluxo de vídeo remoto.
```js
renderer.createView()
```

* Elimine o renderizador e todas as `RendererView` instâncias associadas.
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>RendererVer métodos e propriedades
Ao criar um `RendererView` pode especificar `scalingMode` e `isMirrored` propriedades.
O modo de escala pode ser 'Alongamento', 'Crop', ou 'Fit' Se `isMirrored` for especificado, o fluxo renderizado será virado verticalmente.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, isMirrored });
```
Qualquer `RendererView` caso tem uma `target` propriedade que representa a superfície de renderização. Isto tem de ser anexado na UI da aplicação:
```js
document.body.appendChild(rendererView.target);
```

Pode atualizar mais tarde o modo de escala, invocando o `updateScalingMode` método.
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Gestão de dispositivos

`DeviceManager` permite enumerar dispositivos locais que podem ser usados numa chamada para transmitir os seus streams de áudio/vídeo. Também permite solicitar permissão a um utilizador para aceder ao seu microfone e câmera usando a API do navegador nativo.

Pode aceder ao `deviceManager` método de `callClient.getDeviceManager()` chamada.
> [!WARNING]
> Atualmente um `callAgent` objeto deve ser instantâneo primeiro para ter acesso a DeviceManager

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Enumerar dispositivos locais

Para aceder a dispositivos locais, pode utilizar métodos de enumeração no Gestor de Dispositivos. A enumeração é uma ação assíncronea.

```js

//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Definir microfone/altifalante predefinido

O gestor de dispositivos permite-lhe configurar um dispositivo predefinido que será utilizado ao iniciar uma chamada.
Se os incumprimentos do cliente não forem definidos, os Serviços de Comunicação recorrerão aos padrãos do SISTEMA.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Pré-visualização da câmara local

Pode utilizar `DeviceManager` e começar a `Renderer` renderizar streams a partir da sua câmara local. Este fluxo não será enviado para outros participantes; É um feed de pré-visualização local. Esta é uma ação assíncronea.

```js
const cameras = await deviceManager.getCameras();
const localVideoDevice = cameras[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Pedir permissão para câmera/microfone

Insto um utilizador a conceder permissões de câmara/microfone com o seguinte:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```
Isto resolverá assíncronosamente com um objeto que indique se `audio` e `video` foram concedidas permissões:
```js
console.log(result.audio);
console.log(result.video);
```


## <a name="call-recording-management"></a>Gestão de gravação de chamadas

A gravação de chamadas é uma característica estendida da `Call` API central. Primeiro, tem de obter o objeto API da funcionalidade de gravação:

```js
const callRecordingApi = call.api(Features.Recording);
```

Em seguida, para verificar se a chamada está a ser gravada, inspecione a `isRecordingActive` propriedade `callRecordingApi` de, retorna `Boolean` .

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

Também pode subscrever alterações de gravação:

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="call-transfer-management"></a>Gestão de Transferência de Chamadas

A transferência de chamadas é uma característica estendida da `Call` API central. Primeiro tem de obter o objeto API da funcionalidade de transferência:

```js
const callTransferApi = call.api(Features.Transfer);
```

A transferência de chamadas envolve três partes *transferoradoras,* *transferee,* e *meta de transferência.* O fluxo de transferência está a funcionar como seguinte:

1. Já existe uma chamada conectada entre *o cedente* e *o cessor*
2. *transferor* decide transferir a chamada (meta *de*  ->  *transferência)*
3. *transferor* chamar `transfer` API
4. *transferee* decidir se `accept` ou o pedido de transferência para o alvo de `reject` *transferência* através do `transferRequested` evento.
5. *alvo de transferência* só receberá uma chamada recebida se *o transferee* fez `accept` o pedido de transferência

### <a name="transfer-terminology"></a>Terminologia de transferência

- Cededor - Aquele que inicia o pedido de transferência
- Transferee - Aquele que está a ser transferido pelo cedente para o alvo de transferência
- Meta de transferência - Aquele que é o alvo que está sendo transferido para

Para transferir a chamada atual, pode utilizar `transfer` API sincronizada. `transfer` toma `TransferCallOptions` opcional, o que lhe permite definir a `disableForwardingAndUnanswered` bandeira:

- `disableForwardingAndUnanswered` = falso - se o *alvo de transferência* não responder à chamada de transferência, seguirá o encaminhamento do alvo de *transferência* e as definições sem resposta
- `disableForwardingAndUnanswered` = verdadeiro - se *o alvo de transferência* não responder à chamada de transferência, então a tentativa de transferência terminará

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

A transferência permite-lhe subscrever `transferStateChanged` e `transferRequested` eventos. `transferRequsted` evento vem de `call` instância, `transferStateChanged` evento e transferência e vem de `state` `error` `transfer` exemplo

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

A Transferee pode aceitar ou rejeitar o pedido de transferência iniciado pelo cedente em `transferRequested` caso de via ou em `accept()` `reject()` `transferRequestedEventArgs` . Pode aceder à `targetParticipant` `accept` informação, `reject` métodos em `transferRequestedEventArgs` .

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="eventing-model"></a>Modelo de eventos
Tem de inspecionar os valores atuais e subscrever para atualizar eventos para valores futuros.

### <a name="properties"></a>Propriedades

```js
// Inspect current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Coleções
```js
// Inspect current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});



// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```
