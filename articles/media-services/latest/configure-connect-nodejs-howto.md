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
ms.openlocfilehash: fcb9fd9f0539b42d9253db783fd5da840f358e66
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960729"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Ligue-se aos Serviços de Mídia v3 API - Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra-lhe como ligar-se ao Azure Media Services v3 node.js SDK utilizando o método principal de início de súpência do serviço. Trabalhará com ficheiros no *repositório de amostras de amostras de serviços de comunicação-v3-node-node.* A amostra *HelloWorld-ListAssets* contém o código para ligar e, em seguida, listar ativos na conta.

## <a name="prerequisites"></a>Pré-requisitos

- Uma instalação do Código do Estúdio Visual.
- Instale [Node.js](https://nodejs.org/en/download/).
- Instalar [a máquina de escrever](https://www.typescriptlang.org/download).
- [Criar uma conta de Serviços de Comunicação](./account-create-how-to.md)Social. Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Comunicação Social.
- Crie um principal de serviço para a sua aplicação. Ver [APIs de acesso](./access-api-howto.md).<br/>**Dica profissional!** Mantenha esta janela aberta ou copie tudo o que está no separador JSON para o Bloco de Notas. 
- Certifique-se de obter a versão mais recente do [AzureMediaServices SDK para JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices).

> [!IMPORTANT]
> Reveja as [convenções de nomeação](media-services-apis-overview.md#naming-conventions) da Azure Media Services para compreender as importantes restrições de nomeação às entidades.

## <a name="clone-the-nodejs-samples-repo"></a>Clone as amostras de Node.JS repo

Trabalhará com alguns ficheiros em Azure Samples. Clone o repositório de amostras Node.JS.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-packages"></a>Instalar as embalagens

### <a name="install-azurearm-mediaservices"></a>Instalar @azure/arm-mediaservices

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Instalar @azure/ms-rest-nodeauth

Por favor, instale a versão mínima de @azure/ms-rest-nodeauth ":^3.0.0".

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

Para este exemplo, utilizará as seguintes embalagens no `package.json` ficheiro.

|Pacote|Descrição|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK. <br/>Para se certificar de que está a utilizar o mais recente pacote Azure Media Services, consulte [a @azure/arm-mediaservices instalação npm ](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Requerido para a autenticação AAD utilizando o Principal de Serviço ou Identidade Gerida|
|`@azure/storage-blob`|SDK de armazenamento. Usado ao enviar ficheiros para ativos.|
|`@azure/ms-rest-js`| Costumava entrar.|
|`@azure/storage-blob` | Usado para carregar e transferir ficheiros em Ativos em Azure Media Services para codificação.|
|`@azure/abort-controller`| Usado juntamente com o cliente de armazenamento para cronometrar operações de descarregamento de longa duração|

### <a name="create-the-packagejson-file"></a>Criar o package.jsno ficheiro

1. Crie um `package.json` ficheiro usando o seu editor favorito.
1. Abra o ficheiro e cole o seguinte código:

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

## <a name="connect-to-nodejs-client-using-typescript"></a>Conecte-se a Node.js cliente usando o TypeScript



### <a name="sample-env-file"></a>Arquivo *amostra .env*

Copie o conteúdo deste ficheiro para um ficheiro chamado *.env*. Deve ser guardado na raiz do seu repositório de trabalho. Estes são os valores que obteve na página de Acesso da API para a sua conta de Serviços de Comunicação social no portal.

Depois de ter criado o ficheiro *.env,* pode começar a trabalhar com as amostras.

```nodejs
# Values from the API Access page in the portal
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

## <a name="run-the-sample-application-helloworld-listassets"></a>Executar a aplicação de amostra *HelloWorld-ListAssets*

1. Alterar o diretório na pasta *AMSv3Samples*

```bash
cd AMSv3Samples
```

2. Instale as embalagens utilizadas no *packages.jsno* ficheiro.

```
npm install 
```

3. Mude o diretório para a pasta *HelloWorld-ListAssets.*

```bash
cd HelloWorld-ListAssets
```

4. Lançar Código de Estúdio Visual a partir da Pasta AMSv3Samples. Isto é necessário para ser lançado a partir da pasta onde se encontra a pasta ".vscode" e tsconfig.jsnos ficheiros

```dotnetcli
cd ..
code .
```

Abra a pasta para *HelloWorld-ListAssets* e abra o ficheiro *index.ts* no editor visual Studio Code.

Enquanto está no ficheiro *index.ts,* prima F5 para lançar o depurar. Deverá ver uma lista de ativos apresentados se já tiver ativos na conta. Se a conta estiver vazia, verá uma lista vazia.  

Para ver rapidamente os ativos listados, utilize o portal para carregar alguns ficheiros de vídeo. Os ativos serão automaticamente criados cada um deles e executar este script novamente irá devolver os seus nomes.

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>Um olhar mais atento sobre a amostra *HelloWorld-ListAssets*

A amostra *HelloWorld-ListAssets mostra-lhe* como ligar ao cliente dos Serviços de Mídia com um Principal de Serviço e listar ativos na conta. Consulte os comentários no código para obter uma explicação detalhada do que faz.

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

## <a name="more-samples"></a>Mais amostras

As seguintes amostras estão disponíveis no [repositório](https://github.com/Azure-Samples/media-services-v3-node-tutorials)

|Nome do projeto|Caso de Utilização|
|---|---|
|Live/index.ts| Exemplo básico de streaming ao vivo. **ATENÇÃO**, certifique-se de verificar se todos os recursos são limpos e deixar de faturar no portal ao utilizar ao vivo|
|StreamFilesSample/index.ts| Exemplo básico para o upload de um ficheiro local ou codificação de um URL de origem. A amostra mostra como usar o armazenamento SDK para descarregar conteúdo, e mostra como transmitir para um jogador |
|StreamFilesWithDRMSample/index.ts| Demonstra como codificar e transmitir usando Widevine e PlayReady DRM |
|VideoIndexerSample/index.ts| Exemplo da utilização das predefinições do Analisador de Vídeo e Áudio para gerar metadados e insights a partir de um ficheiro de vídeo ou áudio |

## <a name="see-also"></a>Ver também

- [Documentação de referência para módulos Azure Media Services para Node.js](/javascript/api/overview/azure/media-services)
- [Azure para desenvolvedores de & Node.js JavaScript](/azure/developer/javascript/)
- [Código fonte dos Serviços de Mídia no @azure/azure-sdk-for-js Git Hub repo](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentação do pacote Azure para desenvolvedores de Node.js](/javascript/api/overview/azure/)
- [Conceitos de Serviços de Mídia](concepts-overview.md)
- [instalação npm @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure para desenvolvedores de & Node.js JavaScript](/azure/developer/javascript/)
- [Código fonte dos Serviços de Mídia no @azure/azure-sdk-for-js repo](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Passos seguintes

Explore a documentação [Node.js ref](/javascript/api/overview/azure/mediaservices/management) dos Serviços de Comunicação Social e confira [amostras](https://github.com/Azure-Samples/media-services-v3-node-tutorials) que mostram como utilizar a API dos Serviços de Comunicação social com node.js.
