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
ms.openlocfilehash: 743b05b38eddc80ce7462a3439613fc767d91daa
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607796"
---
[Documentação de referência](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices)  |  [Pacote (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/)  |  [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição válida da Azure - [Crie uma gratuitamente.](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="create-an-azure-service-principal"></a>Criar um diretor de serviço Azure

Para que a sua aplicação interaja com a sua conta Azure, precisa de um responsável de serviço da Azure para gerir permissões. Siga as instruções na [Criar um chefe de serviço Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Quando criar um diretor de serviço, verá que tem um valor secreto, uma identificação e uma identificação de aplicação. Guarde o ID da aplicação e o segredo para um local temporário para etapas posteriores.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar um recurso de Serviços Cognitivos, a sua conta deve ter um grupo de recursos Azure para conter o recurso. Se ainda não tiver um grupo de recursos, crie um no [portal Azure](https://ms.portal.azure.com/).

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

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

### <a name="choose-a-service-and-pricing-tier"></a>Escolha um nível de serviço e preços

Quando criar um novo recurso, terá de conhecer o "tipo" de serviço que pretende utilizar, juntamente com o [nível de preços](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou SKU) que pretende. Usará esta e outras informações como parâmetros ao criar o recurso. A seguinte função lista os "tipos" do Serviço Cognitivo disponível.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

Consulte a lista de SKUs e informações sobre preços abaixo. 

#### <a name="multi-service"></a>Multi-serviço

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Vários serviços. Para mais informações, consulte a página [de preços.](https://azure.microsoft.com/pricing/details/cognitive-services/)            | `CognitiveServices`     |


#### <a name="vision"></a>Visão

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Imagem Digitalizada            | `ComputerVision`          |
| Visão Personalizada - Previsão | `CustomVision.Prediction` |
| Visão Personalizada - Treino   | `CustomVision.Training`   |
| Rostos                       | `Face`                    |
| Reconhecedor de Formato            | `FormRecognizer`          |
| Reconhecedor de Tinta Digital             | `InkRecognizer`           |

#### <a name="search"></a>Pesquisa

| Serviço            | Tipo                  |
|--------------------|-----------------------|
| Sugestão Automática do Bing   | `Bing.Autosuggest.v7` |
| Pesquisa Personalizada do Bing | `Bing.CustomSearch`   |
| Pesquisa de Entidades do Bing | `Bing.EntitySearch`   |
| Pesquisa do Bing        | `Bing.Search.v7`      |
| Verificação Ortográfica do Bing   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Voz

| Serviço            | Tipo                 |
|--------------------|----------------------|
| Serviços de Voz    | `SpeechServices`     |
| Reconhecimento de Voz | `SpeakerRecognition` |

#### <a name="language"></a>Linguagem

| Serviço            | Tipo                |
|--------------------|---------------------|
| Compreensão de formulários | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Análise de Texto     | `TextAnalytics`     |
| Tradução de Texto   | `TextTranslation`   |

#### <a name="decision"></a>Decisão

| Serviço           | Tipo               |
|-------------------|--------------------|
| Detetor de Anomalias  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizador      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Níveis de preços e faturação

Os níveis de preços (e o valor que recebe faturado) baseiam-se no número de transações que envia usando as suas informações de autenticação. Cada nível de preços especifica:
* número máximo de transações permitidas por segundo (TPS).
* funcionalidades de serviço ativadas dentro do nível de preços.
* custo para um número pré-refinado de transações. Ir acima deste número irá causar uma taxa extra, conforme especificado nos detalhes de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) do seu serviço.

> [!NOTE]
> Muitos dos Serviços Cognitivos têm um nível gratuito que pode usar para experimentar o serviço. Para utilizar o nível livre, utilize `F0` como SKU para o seu recurso.

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

Para criar e subscrever um novo recurso de Serviços Cognitivos, utilize a função **Criar.** Esta função adiciona um novo recurso faturado ao grupo de recursos em que passa. Quando criar o seu novo recurso, terá de conhecer o "tipo" de serviço que pretende utilizar, juntamente com o seu nível de preços (ou SKU) e uma localização Azure. A seguinte função pega em todos estes argumentos e cria um recurso.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

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

* [Documentação de referência da Azure Management SDK](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)
* [O que são os Serviços Cognitivos Azure?](../../Welcome.md)
* [Autenticar pedidos aos Serviços Cognitivos da Azure](../../authentication.md)
* [Criar um novo recurso utilizando o portal do Azure](../../cognitive-services-apis-create-account.md)