---
title: Quickstart - Adicione a chamada de vídeo à sua aplicação (JavaScript)
titleSuffix: An Azure Communication Services quickstart
description: Neste arranque rápido, você vai aprender a adicionar capacidades de chamada de vídeo à sua app usando os Serviços de Comunicação Azure.
author: xumo-95
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 82f4d9028fa94d4df0ff089fda213d64e13d56ec
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487875"
---
# <a name="quickstart-add-11-video-calling-to-your-app-javascript"></a>QuickStart: Adicione 1:1 chamada de vídeo para a sua aplicação (JavaScript)

## <a name="download-code"></a>Código de Descarregamento

Encontre o código finalizado para este arranque rápido no [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling)

## <a name="prerequisites"></a>Pré-requisitos
- Obtenha uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/en/) Versões LTS e Manutenção Ativas LTS (8.11.1 e 10.14.1)
- Criar um recurso ativo de Serviços de Comunicação. [Criar um recurso de Serviços de Comunicação.](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp)
- Crie um Token de Acesso ao Utilizador para instantaneamente o cliente da chamada. [Saiba como criar e gerir fichas de acesso ao utilizador.](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-csharp)

## <a name="setting-up"></a>Configuração
### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js
Abra o seu terminal ou janela de comando crie um novo diretório para a sua aplicação e navegue até ele.
```console
mkdir calling-quickstart && cd calling-quickstart
```
### <a name="install-the-package"></a>Instale o pacote
Utilize o `npm install` comando para instalar a biblioteca de clientes Azure Communication Services Call para JavaScript.

Este quickstart usou a Azure Communication Call Client Library `1.0.0.beta-6` . 

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Este quickstart usa webpack para agregar os ativos da aplicação. Executar o seguinte comando para instalar os `webpack` pacotes , `webpack-cli` e `webpack-dev-server` npm e listá-los como dependências de desenvolvimento em `package.json` seu :

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```
Crie um `index.html` ficheiro no diretório de raiz do seu projeto. Usaremos este ficheiro para configurar um layout básico que permitirá ao utilizador fazer uma chamada de vídeo 1:1.

Aqui está o código:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - 1:1 Video Calling Sample</title>
</head>

<body>
    <h4>Azure Communication Services</h4>
    <h1>1:1 Video Calling Quickstart</h1>
    <input 
    id="callee-id-input"
    type="text"
    placeholder="Who would you like to call?"
    style="margin-bottom:1em; width: 200px;"
    />

    <div>
    <button id="call-button" type="button" disabled="true">
        start call
    </button>
        &nbsp;
    <button id="hang-up-button" type="button" disabled="true">
        hang up
    </button>
        &nbsp;
    <button id="start-Video" type="button" disabled="true">
        start video
    </button>
        &nbsp;
    <button id="stop-Video" type="button" disabled="true">
        stop video
    </button>     
    </div>

    <div>Local Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;">
      <div id="myVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>     
    </div>
    <div>Remote Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;"> 
      <div id="remoteVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>
    </div>

    <script src="./bundle.js"></script>
</body>
</html>
```

Crie um ficheiro no diretório de raiz do seu projeto chamado `client.js` para conter a lógica de aplicação para este arranque rápido. Adicione o seguinte código para importar o cliente de chamada e obter referências aos elementos DOM.

```JavaScript
import { CallClient, CallAgent, Renderer, LocalVideoStream } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
const stopVideoButton = document.getElementById("stop-Video");
const startVideoButton = document.getElementById("start-Video");

let placeCallOptions;
let deviceManager;
let localVideoStream;
let rendererLocal;
let rendererRemote;
```
## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca de clientes Azure Communication Services Call:

| Nome      | Descrição | 
| :---        |    :----   |
| CallClient  | O CallClient é o principal ponto de entrada para a biblioteca do cliente Call.      |
| Callagent  | O CallAgent é usado para iniciar e gerir chamadas.        |
| Homem de Dispositivos | O DeviceManager é utilizado para gerir dispositivos de mídia.    |
| AzureCommunicationTokenCredential | A classe AzureCommunicationTokenCredential implementa a interface CommunicationTokenCredential que é usada para instantaneaizar o CallAgent.        |

## <a name="authenticate-the-client-and-access-devicemanager"></a>Autenticar o cliente e aceder a DeviceManager

Tem de substituir <USER_ACCESS_TOKEN> por um sinal de acesso ao utilizador válido para o seu recurso. Consulte a documentação do token de acesso ao utilizador se ainda não tiver um token disponível. Utilizando o CallClient, inicialize uma instância CallAgent com um CommunicationUserCredential que nos permitirá fazer e receber chamadas. Para aceder ao DeviceManager deve primeiro ser criada uma instância callAgent. Em seguida, pode utilizar o `getDeviceManager` método no caso para obter o `CallClient` `DeviceManager` .

Adicione o seguinte código a `client.js`:

```JavaScript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
    
    deviceManager = await callClient.getDeviceManager();
    callButton.disabled = false;
}
init();
```
## <a name="place-a-11-outgoing-video-call-to-a-user"></a>Coloque uma chamada de vídeo de saída 1:1 para um utilizador

Adicione um ouvinte de eventos para iniciar uma chamada quando o `callButton` clicado:

Primeiro tem de enumerar as câmaras locais utilizando o dispositivoManager obter APICameraList. Neste quickstart estamos a usar a primeira câmara da coleção. Uma vez selecionada a câmara desejada, será construída e passada uma instância LocalVideoStream dentro de videoOptions como um item dentro da matriz local DevóStream ao método de chamada. Assim que a chamada ligar, começará automaticamente a enviar um fluxo de vídeo para o outro participante. 

```JavaScript
callButton.addEventListener("click", async () => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};

    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;

    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        placeCallOptions
    );

    subscribeToRemoteParticipantInCall(call);

    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```  
Para tornar um `LocalVideoStream` , é necessário criar uma nova instância de , `Renderer` e, em seguida, criar uma nova instância RendererView utilizando o método assíncronos. `createView` Em seguida, pode `view.target` ligar-se a qualquer elemento UI. 

```JavaScript
async function localVideoView() {
    rendererLocal = new Renderer(localVideoStream);
    const view = await rendererLocal.createView();
    document.getElementById("myVideo").appendChild(view.target);
}
```
Todos os participantes remotos estão disponíveis através da `remoteParticipants` coleção numa instância de chamada. Você precisa subscrever os participantes remotos da chamada atual e ouvir o evento `remoteParticipantsUpdated` para subscrever participantes remotos adicionados.

```JavaScript
function subscribeToRemoteParticipantInCall(callInstance) {
    callInstance.remoteParticipants.forEach( p => {
        subscribeToRemoteParticipant(p);
    })
    callInstance.on('remoteParticipantsUpdated', e => {
        e.added.forEach( p => {
            subscribeToRemoteParticipant(p);
        })
    });   
}
```
Pode subscrever a `remoteParticipants` recolha da chamada atual e inspecionar as `videoStreams` coleções para listar os fluxos de cada participante. Também precisa subscrever o evento remoteParticipantsUpdated para lidar com participantes remotos adicionados. 

```JavaScript
function subscribeToRemoteParticipant(remoteParticipant) {
    remoteParticipant.videoStreams.forEach(v => {
        handleVideoStream(v);
    });
    remoteParticipant.on('videoStreamsUpdated', e => {
        e.added.forEach(v => {
            handleVideoStream(v);
        })
    });
}
```
Tem de subscrever um `isAvailableChanged` evento para tornar o `remoteVideoStream` . Se a `isAvailable` propriedade mudar `true` para, um participante remoto está enviando um fluxo. Sempre que a disponibilidade de um fluxo remoto for alterada, pode optar por destruir o todo `Renderer` , um específico ou `RendererView` mantê-los, mas isso resultará na exibição de uma moldura de vídeo em branco.
```JavaScript
function handleVideoStream(remoteVideoStream) {
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            remoteVideoView(remoteVideoStream);
        } else {
            rendererRemote.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        remoteVideoView(remoteVideoStream);
    }
}
```
Para tornar um `RemoteVideoStream` , você precisa criar um novo exemplo de , `Renderer` e, em seguida, criar um novo `RendererView` exemplo usando o método assíncronos. `createView` Em seguida, pode `view.target` ligar-se a qualquer elemento UI. 

```JavaScript
async function remoteVideoView(remoteVideoStream) {
    rendererRemote = new Renderer(remoteVideoStream);
    const view = await rendererRemote.createView();
    document.getElementById("remoteVideo").appendChild(view.target);
}
```
## <a name="receive-an-incoming-call"></a>Receber uma chamada recebida
Para lidar com chamadas recebidas, precisa de ouvir o `incomingCall` evento de `callAgent` . Uma vez que haja uma chamada recebida, você precisa enumerar as câmaras locais e construir um `LocalVideoStream` caso para enviar um stream de vídeo para o outro participante. Também precisa subscrever para `remoteParticipants` lidar com streams de vídeo remotos. Pode aceitar ou rejeitar a chamada através do `incomingCall` caso. 

Coloque a implementação `init()` para lidar com as chamadas recebidas. 

```JavaScript
callAgent.on('incomingCall', async e => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    localVideoView();

    stopVideoButton.disabled = false;
    callButton.disabled = true;
    hangUpButton.disabled = false;

    const addedCall = await e.incomingCall.accept({videoOptions: {localVideoStreams:[localVideoStream]}});
    call = addedCall;

    subscribeToRemoteParticipantInCall(addedCall);   
});
```
## <a name="end-the-current-call"></a>Termine a chamada atual
Adicione um ouvinte de eventos para terminar a chamada atual quando o `hangUpButton` clicado:
```JavaScript
hangUpButton.addEventListener("click", async () => {
    // dispose of the renderers
    rendererLocal.dispose();
    rendererRemote.dispose();
    // end the current call
    await call.hangUp();
    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
## <a name="subscribe-to-call-updates"></a>Subscreva atualizações de chamadas
É necessário subscrever o evento quando o participante remoto terminar a chamada para eliminar os renderizadores de vídeo e alternar os estados dos botões. 

Coloque a implementação no mesmo para subscrever o `callsUpdated` evento. 
 ```JavaScript 
callAgent.on('callsUpdated', e => {
    e.removed.forEach(removedCall => {
        // dispose of video renders
        rendererLocal.dispose();
        rendererRemote.dispose();
        // toggle button states
        hangUpButton.disabled = true;
        callButton.disabled = false;
        stopVideoButton.disabled = true;
    })
})
```

## <a name="start-and-end-video-during-the-call"></a>Iniciar e terminar o vídeo durante a chamada
Pode parar o vídeo durante a chamada atual adicionando um ouvinte do evento ao botão Stop Video para eliminar o renderizador de `localVideoStream` . 
 ```JavaScript       
stopVideoButton.addEventListener("click", async () => {
    await call.stopVideo(localVideoStream);
    rendererLocal.dispose();
    startVideoButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
Pode adicionar um ouvinte do evento ao botão Iniciar vídeo para voltar a ligar o vídeo quando este foi parado durante a chamada atual. 
```JavaScript
startVideoButton.addEventListener("click", async () => {
    await call.startVideo(localVideoStream);
    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;
});
```
## <a name="run-the-code"></a>Executar o código
Use o `webpack-dev-server` para construir e executar a sua aplicação. Executar o seguinte comando para agregar anfitrião de aplicações em um webserver local:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Abra o seu navegador e navegue para http://localhost:8080/ . Deverá ver o seguinte:

:::image type="content" source="./media/javascript/1-on-1-video-calling.png" alt-text="1 em 1 página de chamada de vídeo":::

Pode fazer uma chamada de vídeo de saída 1:1 fornecendo um ID do utilizador no campo de texto e clicando no botão Iniciar Chamada. 

## <a name="sample-code"></a>Código de Exemplo
Pode transferir a aplicação de exemplo do [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling).

## <a name="clean-up-resources"></a>Limpar os recursos
Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp#clean-up-resources)

## <a name="next-steps"></a>Próximos passos
Para obter mais informações, veja os seguintes artigos:
- Confira a nossa [amostra de chamada web](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Saiba mais [sobre chamar as capacidades da biblioteca do cliente](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- Saiba mais sobre [como funciona a chamada](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)
