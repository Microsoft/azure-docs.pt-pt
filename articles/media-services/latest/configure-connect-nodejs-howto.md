---
title: Ligue-se ao Azure Media Services v3 API - Node.js
description: Este artigo demonstra como ligar aos Serviços de Media v3 API com Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: d32f39daeb66990d99915e8bbcca5dd637d4f914
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91296268"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Ligue-se aos Serviços de Mídia v3 API - Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra-lhe como ligar-se ao Azure Media Services v3 node.js SDK utilizando o sinal principal de serviço no método.

## <a name="prerequisites"></a>Pré-requisitos

- Instale [Node.js](https://nodejs.org/en/download/).
- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social. Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Comunicação Social.

> [!IMPORTANT]
> Rever [convenções de nomeação](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Criar package.jsem

1. Crie uma package.jsno ficheiro utilizando o seu editor favorito.
1. Abra o ficheiro e cole o seguinte código:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

Devem ser especificadas as seguintes embalagens:

|Pacote|Descrição|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Para se certificar de que está a utilizar o mais recente pacote Azure Media Services, consulte a [NPM para instalar serviços de media-media-media-arm- azul](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|SDK de armazenamento. Usado ao enviar ficheiros para ativos.|
|`ms-rest-azure`| Costumava entrar.|

Pode executar o seguinte comando para se certificar de que está a utilizar o pacote mais recente:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Conecte-se com Node.js cliente

1. Crie um ficheiro .js utilizando o seu editor favorito.
1. Abra o ficheiro e cole o seguinte código.
1. Desafie os valores na secção "endpoint config" aos valores que obteve a partir de [APIs](./access-api-howto.md)de acesso .

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Executar a aplicação

Abra uma linha de comandos. Navegue no diretório da amostra e execute os seguintes comandos:

```
npm install 
node index.js
```

## <a name="see-also"></a>Consulte também

- [Conceitos de Serviços de Mídia](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/) (Instalação de azure-arm-mediaservices do NPM)

## <a name="next-steps"></a>Passos seguintes

Explore a documentação [Node.js ref](/javascript/api/overview/azure/mediaservices/management) dos Serviços de Comunicação Social e confira [amostras](https://github.com/Azure-Samples/media-services-v3-node-tutorials) que mostram como utilizar a API dos Serviços de Comunicação social com node.js.
