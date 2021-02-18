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
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 9628e46281e267bd1c1fd8277a3a975bc338c6dc
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096059"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Ligue-se aos Serviços de Mídia v3 API - Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra-lhe como ligar-se ao Azure Media Services v3 node.js SDK utilizando o método principal de início de súpência do serviço.

## <a name="prerequisites"></a>Pré-requisitos

- Instale [Node.js](https://nodejs.org/en/download/).
- Instalar [a máquina de escrever](https://www.typescriptlang.org/download).
- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social. Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Comunicação Social.

> [!IMPORTANT]
> Reveja as [convenções de nomeação](media-services-apis-overview.md#naming-conventions) da Azure Media Services para compreender as importantes restrições de nomeação às entidades. 

## <a name="reference-documentation-for-azurearm-mediaservices"></a>Documentação de referência para @Azure/arm-mediaservices
- [Documentação de referência para módulos Azure Media Services para Node.js](https://docs.microsoft.com/javascript/api/overview/azure/media-services?view=azure-node-latest)

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Mais documentação do programador para Node.js em Azure
- [Azure para desenvolvedores de & Node.js JavaScript](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Código fonte dos Serviços de Mídia no @azure/azure-sdk-for-js Git Hub repo](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentação do pacote Azure para desenvolvedores de Node.js](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)

## <a name="install-the-packages"></a>Instalar os Pacotes

1. Crie uma package.jsno ficheiro utilizando o seu editor favorito.
1. Abra o ficheiro e cole o seguinte código:

   Certifique-se de obter a versão mais recente do [AzureMediaServices SDK para JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices).

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

Devem ser especificadas as seguintes embalagens:

|Pacote|Descrição|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK. <br/>Para se certificar de que está a utilizar o mais recente pacote Azure Media Services, consulte [a @azure/arm-mediaservices instalação npm ](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Requerido para a autenticação AAD utilizando o Principal de Serviço ou Identidade Gerida|
|`@azure/storage-blob`|SDK de armazenamento. Usado ao enviar ficheiros para ativos.|
|`@azure/ms-rest-js`| Costumava entrar.|
|`@azure/storage-blob` | Usado para carregar e transferir ficheiros em Ativos em Azure Media Services para codificação.|
|`@azure/abort-controller`| Usado juntamente com o cliente de armazenamento para cronometrar operações de descarregamento de longa duração|


Pode executar o seguinte comando para se certificar de que está a utilizar o pacote mais recente:

### <a name="install-azurearm-mediaservices"></a>Instalar @azure/arm-mediaservices
```
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Instalar @azure/ms-rest-nodeauth

Por favor, instale a versão mínima de @azure/ms-rest-nodeauth ":^3.0.0".

```
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

## <a name="connect-to-nodejs-client-using-typescript"></a>Conecte-se a Node.js cliente usando o TypeScript

1. Crie um ficheiro TypeScript .ts utilizando o seu editor favorito.
1. Abra o ficheiro e cole o seguinte código.
1. Crie um ficheiro .env e preencha os detalhes do portal Azure. Ver [APIs de acesso](./access-api-howto.md).

### <a name="sample-env-file"></a>Arquivo amostra .env
```
# copy the content of this file to a file named ".env". It should be stored at the root of the repo.
# The values can be obtained from the API Access page for your Media Services account in the portal.
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="typescript---hello-world---list-assets"></a>Typescript - Hello World - List Assets
Esta amostra mostra como ligar ao cliente dos Serviços de Comunicação social com um Diretor de Serviços e listar Ativos na conta. Se estiver a usar uma conta nova, a lista voltará vazia. Pode fazer o upload de alguns ativos no portal para ver os resultados.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="run-the-sample-application-helloworld-listassets"></a>Executar o pedido de amostra HelloWorld-ListAssets

Clone o repositório para as amostras de Node.js

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

Alterar o diretório na pasta AMSv3Samples
```bash
cd AMSv3Samples
```

Instale as embalagens utilizadas no packages.js
```
npm install 
```

Mude o diretório para a pasta HelloWorld-ListAssets
```bash
cd HelloWorld-ListAssets
```

Lançar Código de Estúdio Visual a partir da Pasta AMSv3Samples. Isto é necessário para ser lançado a partir da pasta onde se encontra a pasta ".vscode" e tsconfig.jsnos ficheiros
```dotnetcli
cd ..
code .
```

Abra a pasta para HelloWorld-ListAssets e abra o ficheiro index.ts no editor visual Studio Code.
Enquanto está no ficheiro index.ts, prima F5 para lançar o depurar. Deverá ver uma lista de ativos apresentados se já tiver ativos na conta. Se a conta estiver vazia, verá uma lista vazia.  Faça upload de alguns ativos no portal para ver os resultados.

## <a name="more-samples"></a>Mais amostras

As seguintes amostras estão disponíveis no [repositório](https://github.com/Azure-Samples/media-services-v3-node-tutorials)

|Nome do projeto|Caso de Utilização|
|---|---|
|Live/index.ts| Exemplo básico de streaming ao vivo. **ATENÇÃO**, certifique-se de verificar se todos os recursos são limpos e deixar de faturar no portal ao utilizar ao vivo|
|StreamFilesSample/index.ts| Exemplo básico para o upload de um ficheiro local ou codificação de um URL de origem. A amostra mostra como usar o armazenamento SDK para descarregar conteúdo, e mostra como transmitir para um jogador |
|StreamFilesWithDRMSample/index.ts| Demonstra como codificar e transmitir usando Widevine e PlayReady DRM |
|VideoIndexerSample/index.ts| Exemplo da utilização das predefinições do Analisador de Vídeo e Áudio para gerar metadados e insights a partir de um ficheiro de vídeo ou áudio |

## <a name="see-also"></a>Ver também

- [Conceitos de Serviços de Mídia](concepts-overview.md)
- [instalação npm @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure para desenvolvedores de & Node.js JavaScript](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Código fonte dos Serviços de Mídia no @azure/azure-sdk-for-js repo](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Passos seguintes

Explore a documentação [Node.js ref](/javascript/api/overview/azure/mediaservices/management) dos Serviços de Comunicação Social e confira [amostras](https://github.com/Azure-Samples/media-services-v3-node-tutorials) que mostram como utilizar a API dos Serviços de Comunicação social com node.js.
