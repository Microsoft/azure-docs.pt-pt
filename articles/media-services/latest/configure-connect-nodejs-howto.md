---
title: Ligue-se ao Azure Media Services v3 API - Node.js
description: Este artigo demonstra como ligar-se à Media Services v3 API com o Node.js.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 0381a2e2b8fd2a8b60e7cb702e0336a5678df057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896106"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Ligação aos Media Services v3 API - Node.js

Este artigo mostra-lhe como se conectar ao Azure Media Services v3 node.js SDK utilizando o sinal principal do serviço no método.

## <a name="prerequisites"></a>Pré-requisitos

- Instale [o Nó.js](https://nodejs.org/en/download/).
- [Criar uma conta de Media Services.](create-account-cli-how-to.md) Lembre-se do nome do grupo de recursos e do nome da conta Media Services.

> [!IMPORTANT]
> Rever convenções de [nomeação.](media-services-apis-overview.md#naming-conventions)

## <a name="create-packagejson"></a>Criar pacote.json

1. Crie um ficheiro package.json usando o seu editor favorito.
1. Abra o ficheiro e colá-lo o seguinte código:

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

Devem ser especificados os seguintes pacotes:

|Pacote|Descrição|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Para se certificar de que está a utilizar o mais recente pacote azure Media Services, verifique a [instalação de serviços de comunicação azure-arm-arm .](https://www.npmjs.com/package/azure-arm-mediaservices/)|
|`azure-storage`|Armazenamento SDK. Usado ao enviar ficheiros para ativos.|
|`ms-rest-azure`| Costumava assinar.|

Pode executar o seguinte comando para se certificar de que está a utilizar o pacote mais recente:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Ligue-se ao cliente Node.js

1. Crie um ficheiro .js utilizando o seu editor favorito.
1. Abra o ficheiro e colhe o seguinte código.
1. Detete os valores na secção "endpoint config" para valores que obteve a partir de [APIs](access-api-cli-how-to.md)de acesso .

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

- [Conceitos de Serviços de Media](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/) (Instalação de azure-arm-mediaservices do NPM)

## <a name="next-steps"></a>Passos seguintes

Explore a documentação do Media Services [Node.js ref](/javascript/api/overview/azure/mediaservices/management) e confira [amostras](https://github.com/Azure-Samples/media-services-v3-node-tutorials) que mostram como usar a API dos Serviços de Media com nó.js.

