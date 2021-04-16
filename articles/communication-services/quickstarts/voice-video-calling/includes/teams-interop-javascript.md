---
title: Quickstart - Junte-se a uma reunião de equipas a partir de uma aplicação web
description: Neste tutorial, você aprende a se juntar a uma reunião de equipas usando os Serviços de Comunicação Azure Chamando SDK para JavaScript
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6747d1d3cfba1c9e2bee7a8a7a48d67d6bed9f8e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564727"
---
Neste arranque rápido, você aprenderá a se juntar a uma reunião de equipas usando os Serviços de Comunicação Azure Chamando SDK para JavaScript.

## <a name="prerequisites"></a>Pré-requisitos

- Um serviço de comunicação em [funcionamento chamando web app](../getting-started-with-calling.md).
- Uma [colocação de equipas.](/deployoffice/teams-install)


## <a name="add-the-teams-ui-controls"></a>Adicione os controlos de UI das equipas

Substitua o código em index.html por seguinte corte.
A caixa de texto será usada para entrar no contexto de reunião das Equipas e o botão será utilizado para se juntar à reunião especificada:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
        <p><span style="font-weight: bold" id="recording-state"></span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>Ativar os controlos de UI das equipas

Substitua o conteúdo do ficheiro client.js por um corte seguinte.

```javascript
import { CallClient } from "@azure/communication-calling";
import { Features } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');
const recordingStateElement = document.getElementById('recording-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('stateChanged', () => {
        callStateElement.innerText = call.state;
    })

    call.api(Features.Recording).on('isRecordingActiveChanged', () => {
        if (call.api(Features.Recording).isRecordingActive) {
            recordingStateElement.innerText = "This call is being recorded";
        }
        else {
            recordingStateElement.innerText = "";
        }
    });
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Obtenha o link de reunião das equipas

O link de encontro das equipas pode ser recuperado usando APIs de gráfico. Isto é detalhado na [documentação do Gráfico.](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)
Os Serviços de Comunicação Que Ligam a SDK aceitam uma ligação completa de reuniões de equipas. Este link é devolvido como parte do `onlineMeeting` recurso, acessível sob a [ `joinWebUrl` propriedade](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) Também pode obter as informações de reunião necessárias do URL de Encontro de **Adesão** na reunião das Equipas.

## <a name="run-the-code"></a>Executar o código

Os utilizadores da Webpack podem usar `webpack-dev-server` a para construir e executar a sua aplicação. Executar o seguinte comando para agregar o seu anfitrião de aplicações num webserver local:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Abra o seu navegador e navegue para http://localhost:8080/ . Deverá ver o seguinte:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Screenshot da aplicação JavaScript concluída.":::

Insira o contexto das Equipas na caixa de texto e pressione o Encontro de Equipas de *Adesão* para se juntar à reunião das Equipas a partir da sua aplicação de Serviços de Comunicação.
