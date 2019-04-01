---
title: Ligar à API de v3 dos serviços de multimédia do Azure - node. js
description: Saiba como ligar a serviços de multimédia v3 API com node. js.
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
ms.openlocfilehash: fbea3daec3d9ccac81f246d3dc762fc80cb4b18a
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759533"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Ligar à API de v3 de serviços de multimédia - node. js

Este artigo mostra-lhe como ligar a serviços de multimédia do Azure v3 node. js SDK com o início de sessão de principal de serviço no método.

## <a name="prerequisites"></a>Pré-requisitos

- Instale [node. js](https://nodejs.org/en/download/).
- [Criar uma conta de Media Services](create-account-cli-how-to.md). Certifique-se de que não se esqueça de que o nome do grupo de recursos e o nome da conta dos serviços de multimédia.

## <a name="create-packagejson"></a>Criar Package. JSON

1. Crie um ficheiro Package. JSON com o seu editor favorito.
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

Os seguintes pacotes devem ser especificados:

|Pacote|Descrição|
|---|---|
|`azure-arm-mediaservices`|SDK dos serviços de multimédia do Azure. <br/>Para certificar-se de que está a utilizar o pacote de serviços de multimédia do Azure mais recente, verifique [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|SDK de armazenamento. Utilizado quando carregar ficheiros para ativos.|
|`ms-rest-azure`| Utilizado para iniciar sessão.|

Pode executar o comando seguinte para se certificar de que está a utilizar o pacote mais recente:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Ligar ao cliente node. js

1. Crie um ficheiro. js com o seu editor favorito.
1. Abra o ficheiro e cole o seguinte código.
1. Defina os valores na secção "configuração de ponto final" para valores de obteve da [aceder a APIs](access-api-cli-how-to.md).

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

Abra uma linha de comandos. Navegar para de diretório o exemplo e execute os seguintes comandos:

```
npm install 
node index.js
```

## <a name="see-also"></a>Consulte também

[.NET reference](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet) (Referência de .NET)

## <a name="next-steps"></a>Passos Seguintes

- [Conceitos de serviços de multimédia](concepts-overview.md)
- [Início rápido]()
- [Node.js reference](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/?view=azure-node-latest) (Referência de Node.js)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/) (Instalação de azure-arm-mediaservices do NPM)<br>
