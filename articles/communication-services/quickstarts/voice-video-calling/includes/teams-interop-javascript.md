---
title: Quickstart - Junte-se a um encontro de equipas
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0559a9b763d273579373bb57922606f14ff6e6db
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94574381"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma [aplicação de chamada de Serviços de Comunicação em funcionamento.](../getting-started-with-calling.md)
- Uma [colocação de equipas.](https://docs.microsoft.com/deployoffice/teams-install)

## <a name="enable-teams-interoperability"></a>Ativar a Interoperabilidade das Equipas

A funcionalidade de interoperabilidade das Equipas encontra-se atualmente em pré-visualização privada. Para ativar esta funcionalidade para o seu recurso serviços de comunicação, por favor envie um e-mail [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) com:

1. O ID de subscrição da subscrição Azure que contém o seu recurso de Serviços de Comunicação.
2. A identificação do inquilino da equipa. A forma mais fácil de o obter é [obter e partilhar um link com a Equipa.](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f#:~:text=Create%20a%20link%20If%20you%E2%80%99re%20a%20team%20owner%2C,link%20into%20any%20browser%20to%20join%20the%20team)

Deve ser membro da organização proprietária de ambas as entidades para utilizar esta funcionalidade.

## <a name="add-the-teams-ui-controls"></a>Adicione os controlos de UI das equipas

Adicione uma nova caixa de texto e um botão dentro do seu HTML. A caixa de texto será usada para entrar no contexto de reunião das Equipas e o botão será utilizado para se juntar à reunião especificada:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Ativar os controlos de UI das equipas

Podemos agora ligar o botão **de Reunião de Equipas** de Junção ao código que se junta à reunião das equipas fornecida:

```javascript
meetingButton.addEventListener("click", () => {
    
    // set display name in the meeting
    callAgent.updateDisplayName('YOUR_NAME');
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>Obtenha o contexto de reunião

O contexto de Equipas pode ser recuperado usando APIs de gráfico. Isto é detalhado na [documentação do Gráfico.](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?view=graph-rest-beta&tabs=http)

Você também pode obter as informações de reunião necessárias da URL **de reunião** de encontro na reunião convidar-se a si mesmo.

## <a name="run-the-code"></a>Executar o código

Os utilizadores da Webpack podem usar `webpack-dev-server` a para construir e executar a sua aplicação. Executar o seguinte comando para agregar o seu anfitrião de aplicações num webserver local:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Abra o seu navegador e navegue para http://localhost:8080/ . Deverá ver o seguinte:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Screenshot da aplicação JavaScript concluída.":::

Insira o contexto das Equipas na caixa de texto e pressione o Encontro de Equipas de *Adesão* para se juntar à reunião das Equipas a partir da sua aplicação de Serviços de Comunicação.

