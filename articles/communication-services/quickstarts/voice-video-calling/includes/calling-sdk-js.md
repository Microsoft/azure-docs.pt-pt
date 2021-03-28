---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 49054d9bbde67dc3670ec444e4b60c3ddf503db5
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645426"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Um recurso de Serviços de Comunicação implantado. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um token de acesso do utilizador para ativar o cliente chamador. Para mais informações, consulte [Criar e gerir fichas de acesso.](../../access-tokens.md)
- Opcional: Complete o quickstart para [adicionar chamada de voz à sua aplicação](../getting-started-with-calling.md).

## <a name="install-the-sdk"></a>Instalar o SDK

> [!NOTE]
> Este documento utiliza a versão 1.0.0-beta.10 do Call SDK.

Utilize o `npm install` comando para instalar os Serviços de Comunicação Azure e SDKs comuns para JavaScript.
Este documento refere tipos na versão 1.0.0-beta.10 da biblioteca de chamadas.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características dos Serviços de Comunicação Azure Chamando SDK:

| Nome                             | Descrição                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | O principal ponto de entrada para o Call SDK.                                                                       |
| `CallAgent`                        | Costumava começar e gerir chamadas.                                                                                            |
| `DeviceManager`                    | Usado para gerir dispositivos de mídia.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementa a `CommunicationTokenCredential` interface, que é usada para instantaneaizar `callAgent` . |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Inicialize uma instância callClient, crie uma instância CallAgent e aceda o dispositivoManager

Criar um novo `CallClient` exemplo. Você pode configubá-lo com opções personalizadas como um caso Logger.

Quando se tem um `CallClient` caso, pode criar um `CallAgent` caso chamando o `createCallAgent` método sobre o `CallClient` caso. Este assíncrona devolve um `CallAgent` objeto de exemplo.

O `createCallAgent` método usa como `CommunicationTokenCredential` argumento. Aceita um [símbolo de acesso ao utilizador.](../../access-tokens.md)

Depois de criar um `callAgent` caso, pode utilizar o `getDeviceManager` método no caso para aceder `CallClient` `deviceManager` .

```js
// Set the logger's log level
setLogLevel('verbose');
// Redirect logger output to wherever desired. By default it logs to console
AzureLogger.log = (...args) => { console.log(...args) };
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Fazer uma chamada

Para criar e iniciar uma chamada, utilize uma das APIs `callAgent` e forneça um utilizador que criou através da identidade dos Serviços de Comunicação SDK.

A criação de chamadas e o início são sincronizados. A instância de chamada permite-lhe subscrever eventos de chamadas.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Coloque uma chamada de 1:n para um utilizador ou PSTN

Para chamar outro utilizador dos Serviços de Comunicação, utilize o `startCall` método e passe o destinatário que criou com a biblioteca de `callAgent` `CommunicationUserIdentifier` [administração dos Serviços de Comunicação.](../../access-tokens.md)

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Para estoque uma chamada para uma rede telefónica comutado (PSTN), utilize o `startCall` método e passe o do destinatário `callAgent` `PhoneNumberIdentifier` . O seu recurso de Serviços de Comunicação deve ser configurado para permitir a chamada pstn.

Quando ligar para um número PSTN, especifique o seu ID de chamada alternativo. Um ID de chamada alternativa é um número de telefone (baseado na norma E.164) que identifica o chamador numa chamada PSTN. É o número de telefone que o destinatário da chamada vê para uma chamada recebida.

> [!NOTE]
> A chamada da PSTN está atualmente em pré-visualização privada. Para acesso, [aplique no programa de adoção precoce](https://aka.ms/ACS-EarlyAdopter).

Para uma chamada 1:1, utilize o seguinte código:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Para uma chamada 1:n, utilize o seguinte código:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Coloque uma chamada de 1:1 com câmara de vídeo

> [!IMPORTANT]
> Atualmente, não pode haver mais do que um fluxo de vídeo local de saída.

Para fazer uma chamada de vídeo, tem de especificar as suas câmaras utilizando o `getCameras()` método em `deviceManager` .

Depois de selecionar uma câmara, use-a para construir um `LocalVideoStream` caso. Passe-o para dentro `videoOptions` como um item dentro da matriz para o `localVideoStream` `startCall` método.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Quando a chamada se liga, começa automaticamente a enviar um fluxo de vídeo da câmara selecionada para o outro participante. Isto também se aplica às `Call.Accept()` opções de vídeo e `CallAgent.join()` vídeo.

### <a name="join-a-group-call"></a>Junte-se a uma chamada de grupo

> [!NOTE]
> O `groupId` parâmetro é considerado metadados do sistema e pode ser utilizado pela Microsoft para operações que sejam necessárias para executar o sistema. Não inclua dados pessoais no `groupId` valor. A Microsoft não trata este parâmetro como dados pessoais e o seu conteúdo pode ser visível para os colaboradores da Microsoft ou armazenado a longo prazo.
>
> O `groupId` parâmetro requer que os dados estejam no formato GUID. Recomendamos a utilização de GUIDs gerados aleatoriamente que não sejam considerados dados pessoais nos seus sistemas.
>

Para iniciar uma nova chamada de grupo ou aderir a uma chamada de grupo em curso, use o `join` método e passe um objeto com uma `groupId` propriedade. O `groupId` valor tem de ser um GUID.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Junte-se a uma reunião de equipas

Para participar de uma reunião de equipas, utilize o `join` método e passe um link de reunião ou coordenadas.

Junte-se usando um link de reunião:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Junte-se usando as coordenadas de reunião:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Receber uma chamada recebida

O `callAgent` caso emite um `incomingCall` evento quando a identidade registada recebe uma chamada recebida. Para ouvir este evento, subscreva utilizando uma destas opções:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {

    //Get incoming call ID
    var incomingCallId = incomingCall.id

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

O `incomingCall` evento inclui um caso que pode aceitar ou `incomingCall` rejeitar.

## <a name="manage-calls"></a>Gerir chamadas

Durante uma chamada, pode aceder às propriedades de chamadas e gerir as definições de vídeo e áudio.

### <a name="check-call-properties"></a>Verifique propriedades de chamadas

Obtenha o ID único (string) para uma chamada:

   ```js
    const callId: string = call.id;
   ```

Saiba mais sobre outros participantes na chamada inspecionando a `remoteParticipants` coleção na instância 'call':

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Identifique o chamador de uma chamada recebida:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` é um dos `CommunicationIdentifier` tipos.

Receba o estado de uma chamada:

   ```js
   const callState = call.state;
   ```

   Isto devolve uma corda que representa o estado atual de uma chamada:

  - `None`: Estado de chamada inicial.
  - `Connecting`: Estado de transição inicial quando uma chamada é feita ou aceite.
  - `Ringing`: Para uma chamada de saída, indica que uma chamada está a tocar para participantes remotos. Está do `Incoming` lado deles.
  - `EarlyMedia`: Indica um estado em que um anúncio é reproduzido antes da chamada ser ligada.
  - `Connected`: Indica que a chamada está ligada.
  - `LocalHold`: Indica que a chamada é colocada em espera por um participante local. Nenhum meio de comunicação está a fluir entre o ponto final local e os participantes remotos.
  - `RemoteHold`: Indica que a chamada foi colocada em espera por um participante remoto. Nenhum meio de comunicação está a fluir entre o ponto final local e os participantes remotos.
  - `Disconnecting`: Estado de transição antes da chamada ir para um `Disconnected` estado.
  - `Disconnected`: Estado de chamada final. Se a ligação da rede for perdida, o estado muda para `Disconnected` depois de dois minutos.

Descubra por que uma chamada terminou inspecionando a `callEndReason` propriedade:

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

Saiba se a chamada atual está a chegar ou a sair inspecionando a `direction` propriedade. Volta. `CallDirection`

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Verifique se o microfone atual está silenciado. Volta. `Boolean`

   ```js
   const muted = call.isMuted;
   ```

Descubra se o fluxo de partilha de ecrã está a ser enviado a partir de um determinado ponto final, verificando a `isScreenSharingOn` propriedade. Volta. `Boolean`

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Inspecione os fluxos de vídeo ativos verificando a `localVideoStreams` recolha. Devolve `LocalVideoStream` objetos.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```

### <a name="check-a-callended-event"></a>Verifique um evento callEnded

O `call` caso emite um `callEnded` evento quando a chamada termina. Para ouvir este evento, subscreva utilizando o seguinte código:

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

Para iniciar um vídeo, tem de especificar as câmaras utilizando o `getCameras` método no `deviceManager` objeto. Em seguida, crie um novo exemplo de `LocalVideoStream` passar a câmara desejada para o método `startVideo` como argumento:

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

Depois de começar a enviar vídeos com sucesso, é adicionado um `LocalVideoStream` exemplo à coleção numa instância de `localVideoStreams` chamada.

```js
call.localVideoStreams[0] === localVideoStream;
```

Para parar o vídeo local, passe o `localVideoStream` caso disponível na `localVideoStreams` coleção:

```js
await call.stopVideo(localVideoStream);
```

Pode mudar para um dispositivo de câmara diferente enquanto um vídeo está a enviar invocando `switchSource` uma `localVideoStream` instância:

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>Gerir participantes remotos

Todos os participantes remotos são representados por `RemoteParticipant` tipo e disponíveis através da `remoteParticipants` recolha numa instância de chamada.

### <a name="list-the-participants-in-a-call"></a>Listar os participantes numa chamada

A `remoteParticipants` coleção devolve uma lista de participantes remotos numa chamada:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Aceda a propriedades remotas dos participantes

Os participantes remotos têm um conjunto de propriedades e coleções associadas:

- `CommunicationIdentifier`: Obter o identificador para um participante remoto. Identidade é um dos `CommunicationIdentifier` tipos:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Pode ser um dos `CommunicationIdentifier` seguintes tipos:

  - `{ communicationUserId: '<ACS_USER_ID'> }`: Objeto que representa o utilizador ACS.
  - `{ phoneNumber: '<E.164>' }`: Objeto que represente o número de telefone no formato E.164.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Objeto que represente o utilizador das Equipas.
  - `{ id: string }`: identificador de repreensição de objetos que não se enquadre em nenhum dos outros tipos de identificador

- `state`: Obtenha o estado de um participante remoto.

  ```js
  const state = remoteParticipant.state;
  ```

  O Estado pode ser:

  - `Idle`: Estado inicial.
  - `Connecting`: Estado de transição enquanto um participante está ligado à chamada.
  - `Ringing`: O participante está a tocar.
  - `Connected`: O participante está ligado à chamada.
  - `Hold`: O participante está em espera.
  - `EarlyMedia`: Anúncio que reproduz antes de um participante ligar à chamada.
  - `Disconnected`: Estado final. O participante está desligado da chamada. Se o participante remoto perder a conectividade da rede, o seu estado muda para `Disconnected` depois de dois minutos.

- `callEndReason`: Para saber por que um participante deixou a chamada, verifique a `callEndReason` propriedade:

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted` estado: Para saber se um participante remoto é silenciado, verifique a `isMuted` propriedade. Volta. `Boolean`

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` estado: Para saber se um participante remoto está a falar, verifique a `isSpeaking` propriedade. Volta. `Boolean`

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Para inspecionar todos os streams de vídeo que um determinado participante está a enviar nesta chamada, verifique a `videoStreams` recolha. Contém `RemoteVideoStream` objetos.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`: Para obter o nome de exibição deste participante remoto, inspecione `displayName` a propriedade que devolva a cadeia. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>Adicione um participante a uma chamada

Para adicionar um participante (um utilizador ou um número de telefone) a uma chamada, pode utilizar `addParticipant` . Forneça um dos `Identifier` tipos. Devolve o `remoteParticipant` caso.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Remova um participante de uma chamada

Para remover um participante (um utilizador ou um número de telefone) de uma chamada, pode invocar `removeParticipant` . Tem que passar por um dos `Identifier` tipos. Isto resolve assíncronos após o participante ser removido da chamada. O participante também é removido da `remoteParticipants` coleção.

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

Para `RemoteVideoStream` renderizar, tem de subscrever um `isAvailableChanged` evento. Se a `isAvailable` propriedade mudar `true` para, um participante remoto está enviando um fluxo. Depois disso, crie um novo exemplo de `VideoStreamRenderer` , e, em seguida, crie um novo `VideoStreamRendererView` exemplo usando o método assíncronos. `createView`  Em seguida, pode `view.target` ligar-se a qualquer elemento de UI.

Sempre que a disponibilidade de um fluxo remoto for alterada, pode optar por destruir o todo `VideoStreamRenderer` , um específico ou `VideoStreamRendererView` mantê-los, mas isso resultará na exibição de uma moldura de vídeo em branco.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let videoStreamRenderer: VideoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await videoStreamRenderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            videoStreamRenderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Propriedades de fluxo de vídeo remoto

Os streams de vídeo remotos têm as seguintes propriedades:

- `id`: A identificação de um fluxo de vídeo remoto.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType`: Pode ser `Video` ou `ScreenSharing` . .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Se um ponto final de participante remoto está a enviar ativamente um fluxo.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>Métodos e propriedades do VideoStreamRenderer

Crie um `VideoStreamRendererView` caso que possa ser anexado na UI da aplicação para tornar o fluxo de vídeo remoto, usar o método assíncronos, resolve quando o fluxo está pronto para `createView()` renderizar e devolve um objeto com propriedade `target` que representa elemento que pode ser `video` anexado em qualquer lugar da árvore DOM

  ```js
  videoStreamRenderer.createView()
  ```

Eliminação `videoStreamRenderer` de e de todas as `VideoStreamRendererView` instâncias associadas:

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>VídeoStreamRendererVerdes métodos e propriedades

Quando criar `VideoStreamRendererView` um, pode especificar as `scalingMode` `isMirrored` propriedades e propriedades. `scalingMode` pode `Stretch` `Crop` ser, ou `Fit` . . Se `isMirrored` for especificado, o fluxo renderizado é virado verticalmente.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

Cada `VideoStreamRendererView` instância tem uma propriedade que representa a superfície de `target` renderização. Anexar esta propriedade na UI de aplicação:

```js
htmlElement.appendChild(view.target);
```

Pode atualizar `scalingMode` invocando o `updateScalingMode` método:

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Gestão de dispositivos

Em `deviceManager` , pode especificar dispositivos locais que podem transmitir os seus streams de áudio e vídeo numa chamada. Também ajuda a solicitar permissão para aceder ao microfone e à câmara de outro utilizador utilizando a API do navegador nativo.

Pode aceder `deviceManager` chamando o `callClient.getDeviceManager()` método:

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Obter dispositivos locais

Para aceder a dispositivos locais, pode utilizar métodos de enumeração em `deviceManager` .

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Desafine o microfone e o altifalante predefinidos

Em `deviceManager` , pode configurar um dispositivo predefinido que utilizará para iniciar uma chamada. Se os incumprimentos do cliente não forem definidos, os Serviços de Comunicação utilizam os predefinidos do sistema operativo.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>Pré-visualização da câmara local

Pode utilizar `deviceManager` e começar a `VideoStreamRenderer` renderizar streams a partir da sua câmara local. Este fluxo não será enviado para outros participantes; É um feed de pré-visualização local.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Pedir permissão para câmera e microfone

Insumia o utilizador a conceder permissões de câmara e microfone:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Isto resolve com um objeto que indica se `audio` e `video` permissões foram concedidas:

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Chamadas de registo

[!INCLUDE [Private Preview Notice](../../../includes/private-preview-include-section.md)]

A gravação de chamadas é uma característica estendida da `Call` API central. Primeiro, tem de obter o objeto API da funcionalidade de gravação:

```js
const callRecordingApi = call.api(Features.Recording);
```

Em seguida, para verificar se a chamada está a ser gravada, inspecione a `isRecordingActive` propriedade de `callRecordingApi` . Volta. `Boolean`

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

## <a name="transfer-calls"></a>Chamadas de transferência

A transferência de chamadas é uma característica estendida da `Call` API central. Primeiro precisa de obter o objeto API da funcionalidade de transferência:

```js
const callTransferApi = call.api(Features.Transfer);
```

As transferências de chamadas envolvem três partes:

- *Transferor*: A pessoa que inicia o pedido de transferência.
- *Transferee*: A pessoa que está a ser transferida.
- *Alvo de transferência*: A pessoa que está a ser transferida para.

As transferências seguem estes passos:

1. Já há uma ligação entre o *cedente* e o *cessor.* O *cedente* decide transferir a chamada do *cessionário* para o *alvo de transferência.*
1. O *cedente* chama a `transfer` API.
1. O *cessionário* decide se `accept` deve ou não o pedido de transferência para o alvo de `reject` *transferência* através de um `transferRequested` evento.
1. O *alvo de transferência* só recebe uma chamada recebida se o *cessionário* aceitar o pedido de transferência.

Para transferir uma chamada atual, pode utilizar a `transfer` API. `transfer` toma o `transferCallOptions` opcional, que lhe permite definir uma `disableForwardingAndUnanswered` bandeira:

- `disableForwardingAndUnanswered = false`: Se o alvo de *transferência* não responder à chamada de transferência, a transferência segue o *encaminhamento* do alvo de transferência e as definições sem resposta.
- `disableForwardingAndUnanswered = true`: Se o alvo de *transferência* não responder à chamada de transferência, termina a tentativa de transferência.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

A `transfer` API permite-lhe subscrever `transferStateChanged` e `transferRequested` eventos. Um `transferRequested` evento vem de um `call` exemplo; um evento e transferência e `transferStateChanged` vem de um `state` `error` `transfer` exemplo.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

O *cessor* pode aceitar ou rejeitar o pedido de transferência iniciado pelo *cedente* no `transferRequested` caso de utilizar ou entrar em `accept()` `reject()` `transferRequestedEventArgs` . Pode aceder `targetParticipant` a informações e `accept` `reject` métodos em `transferRequestedEventArgs` .

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

## <a name="learn-about-eventing-models"></a>Conheça os modelos de eventos

Inspecione os valores atuais e subscreva eventos de atualização para valores futuros.

### <a name="properties"></a>Propriedades

```js
// Inspect the current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting a call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Coleções

```js
// Inspect the current collection
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
