---
title: 'Quickstart: Biblioteca de clientes de gestão Azure para Node.js'
description: Neste quickstart, começa com a biblioteca de clientes de gestão Azure para Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: eddee6bc0fdcd636b7b9ffb81b121052499251b2
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97745744"
---
[Documentação de referência](/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices)  |  [Pacote (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices)  |  [Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="javascript-prerequisites"></a>JavaScript pré-requisitos

* Uma subscrição válida da Azure - [Crie uma gratuitamente.](https://azure.microsoft.com/free/)
* A versão atual de [Node.js](https://nodejs.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```

Executar o `npm init` comando para criar uma aplicação de nó com um `package.json` ficheiro. 

```console
npm init
```

Crie um ficheiro chamado _index.js_ antes de continuar.

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Instale os seguintes pacotes NPM:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

O ficheiro da sua aplicação `package.json` será atualizado com as dependências.

### <a name="import-libraries"></a>Importar bibliotecas

Abra o seu script _index.js_ e importe as seguintes bibliotecas.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione os seguintes campos à raiz do seu script e preencha os seus valores, utilizando o principal serviço que criou e as informações da sua conta Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Em seguida, adicione a seguinte `quickstart` função para lidar com o trabalho principal do seu programa. O primeiro bloco de código constrói um objeto **CognitiveServicesManagementClient** usando as variáveis credenciais que inseriu acima. Este objeto é necessário para todas as suas operações de gestão Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Funções de gestão de chamadas

Adicione o seguinte código ao fim da sua `quickstart` função para listar os recursos disponíveis, criar um recurso de amostra, listar os seus recursos próprios e, em seguida, eliminar o recurso da amostra. Definirá estas funções nos próximos passos.

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

Para criar e subscrever um novo recurso de Serviços Cognitivos, utilize a função **Criar.** Esta função adiciona um novo recurso faturado ao grupo de recursos em que passa. Quando criar o seu novo recurso, terá de conhecer o "tipo" de serviço que pretende utilizar, juntamente com o seu nível de preços (ou SKU) e uma localização Azure. A seguinte função pega em todos estes argumentos e cria um recurso.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Escolha um nível de serviço e preços

Quando criar um novo recurso, terá de conhecer o "tipo" de serviço que pretende utilizar, juntamente com o [nível de preços](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou SKU) que pretende. Usará esta e outras informações como parâmetros ao criar o recurso. A seguinte função lista os "tipos" do Serviço Cognitivo disponível.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Ver os seus recursos

Para visualizar todos os recursos na sua conta Azure (em todos os grupos de recursos), utilize a seguinte função:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Eliminar um recurso

A seguinte função elimina o recurso especificado do grupo de recursos dado.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>Executar a aplicação

Adicione o seguinte código na parte inferior do seu script para ligar para a sua função principal `quickstart` com o manuseamento de erros.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Em seguida, na janela da consola, execute a aplicação com o `node` comando.

```console
node index.js
```

## <a name="see-also"></a>Veja também

* [Documentação de referência da Azure Management SDK](/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [O que são os Serviços Cognitivos Azure?](../../what-are-cognitive-services.md)
* [Autenticar pedidos aos Serviços Cognitivos da Azure](../../authentication.md)
* [Criar um novo recurso utilizando o portal do Azure](../../cognitive-services-apis-create-account.md)