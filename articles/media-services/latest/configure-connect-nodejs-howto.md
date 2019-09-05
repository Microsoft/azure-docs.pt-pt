---
title: Conectar-se à API dos serviços de mídia do Azure v3-node. js
description: Saiba como se conectar à API dos serviços de mídia v3 com node. js.
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
ms.openlocfilehash: 2ab620ec456610029d699952bb9625abfe4c21dc
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307890"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Conectar-se à API dos serviços de mídia v3-node. js

Este artigo mostra como se conectar ao SDK do node. js dos serviços de mídia do Azure v3 usando o método de entrada da entidade de serviço.

## <a name="prerequisites"></a>Pré-requisitos

- Instale o [node. js](https://nodejs.org/en/download/).
- [Criar uma conta de Media Services](create-account-cli-how-to.md). Lembre-se de lembrar o nome do grupo de recursos e o nome da conta dos serviços de mídia.

> [!IMPORTANT]
> Examine as [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Criar Package. JSON

1. Crie um arquivo Package. JSON usando seu editor favorito.
1. Abra o arquivo e cole o seguinte código:

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

Os seguintes pacotes devem ser especificados:

|Pacote|Descrição|
|---|---|
|`azure-arm-mediaservices`|SDK dos serviços de mídia do Azure. <br/>Para verificar se você está usando o pacote mais recente dos serviços de mídia do Azure, marque [NPM instalar Azure-ARM-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|SDK de armazenamento. Usado ao carregar arquivos em ativos.|
|`ms-rest-azure`| Usado para entrar.|

Você pode executar o seguinte comando para verificar se está usando o pacote mais recente:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Conectar-se ao cliente do node. js

1. Crie um arquivo. js usando seu editor favorito.
1. Abra o ficheiro e cole o seguinte código.
1. Defina os valores na seção "configuração do ponto de extremidade" para os valores obtidos das [APIs de acesso](access-api-cli-how-to.md).

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

Abra uma linha de comandos. Navegue até o diretório do exemplo e execute os seguintes comandos:

```
npm install 
node index.js
```

## <a name="see-also"></a>Consulte também

- [Conceitos dos serviços de mídia](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/) (Instalação de azure-arm-mediaservices do NPM)

## <a name="next-steps"></a>Passos Seguintes

Explore a documentação de [referência do node. js](https://aka.ms/ams-v3-nodejs-ref) dos serviços de mídia e confira os [exemplos](https://github.com/Azure-Samples/media-services-v3-node-tutorials) que mostram como usar a API dos serviços de mídia com o Node. js.

