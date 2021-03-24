---
title: 'Quickstart: Biblioteca de clientes de gestão Azure para Python'
description: Neste quickstart, começa com a biblioteca de clientes de gestão Azure para python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: fb908cdcf3e235654effc043de29e599a48179d4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879742"
---
[Documentação de referência](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices)  |  [Pacote (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/)  |  [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="python-prerequisites"></a>Pré-requisitos de Python

* Uma subscrição válida da Azure - [Crie uma gratuitamente.](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

Crie uma nova aplicação Python no seu editor preferido ou IDE e navegue para o seu projeto numa janela de consola.

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Pode instalar a biblioteca do cliente com:

```console
pip install azure-mgmt-cognitiveservices
```

Se estiver a utilizar o Visual Studio IDE, a biblioteca do cliente está disponível como um pacote NuGet transferível.

### <a name="import-libraries"></a>Importar bibliotecas

Abra o seu script Python e importe as seguintes bibliotecas.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione os seguintes campos à raiz do seu script e preencha os seus valores, utilizando o principal serviço que criou e as informações da sua conta Azure.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

Em seguida, adicione o seguinte código para construir um objeto **CognitiveServicesManagementClient.** Este objeto é necessário para todas as suas operações de gestão Azure.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource-python"></a>Criar um recurso de Serviços Cognitivos (Python)

Para criar e subscrever um novo recurso de Serviços Cognitivos, utilize a função **Criar.** Esta função adiciona um novo recurso faturado ao grupo de recursos em que passa. Quando criar o seu novo recurso, terá de conhecer o "tipo" de serviço que pretende utilizar, juntamente com o seu nível de preços (ou SKU) e uma localização Azure. A seguinte função pega em todos estes argumentos e cria um recurso.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Escolha um nível de serviço e preços

Quando criar um novo recurso, terá de conhecer o "tipo" de serviço que pretende utilizar, juntamente com o [nível de preços](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou SKU) que pretende. Usará esta e outras informações como parâmetros ao criar o recurso. A seguinte função lista os "tipos" do Serviço Cognitivo disponível.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Ver os seus recursos

Para visualizar todos os recursos na sua conta Azure (em todos os grupos de recursos), utilize a seguinte função:

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>Eliminar um recurso

A seguinte função elimina o recurso especificado do grupo de recursos dado.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>Funções de gestão de chamadas

Adicione o seguinte código na parte inferior do seu script para ligar para as funções acima. Este código lista os recursos disponíveis, cria um recurso de amostra, lista os seus recursos próprios e, em seguida, elimina o recurso da amostra.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>Executar a aplicação

Executar a sua aplicação a partir da linha de comando com o `python` comando.

```console
python <your-script-name>.py
```

## <a name="see-also"></a>Ver também

* Consulte **[pedidos autenticados aos Serviços Cognitivos da Azure](../../authentication.md)** sobre como trabalhar de forma segura com os Serviços Cognitivos.
* Veja **[o que são os Serviços Cognitivos Azure para](../../what-are-cognitive-services.md)** obter uma lista de diferentes categorias dentro dos Serviços Cognitivos.
* Consulte o **[suporte linguístico natural](../../language-support.md)** para ver a lista de línguas naturais que os Serviços Cognitivos suportam.
* Consulte **[o Uso de Serviços Cognitivos como recipientes](../../cognitive-services-container-support.md)** para entender como utilizar os Serviços Cognitivos no pré-pré-m.
* Consulte **[o Plano e gere os custos dos Serviços Cognitivos](../../plan-manage-costs.md)** para estimar o custo da utilização dos Serviços Cognitivos.
* Consulte **[a documentação de referência da Azure Management SDK](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices)** para obter mais detalhes sobre a gestão SDK.
