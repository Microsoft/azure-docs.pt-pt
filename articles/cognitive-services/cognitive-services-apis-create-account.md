---
title: Criar um recurso de Serviços Cognitivos no portal Azure
titleSuffix: Azure Cognitive Services
description: Começar com a Azure Cognitive Services criando e subscrevendo um recurso no portal Azure.
services: cognitive-services
author: aahill
manager: nitinme
keywords: serviços cognitivos, inteligência cognitiva, soluções cognitivas, serviços ai
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 0ad2d0a10b20aa049147d61bd4e8168ea956392c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031927"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>Quickstart: Criar um recurso de Serviços Cognitivos utilizando o portal Azure

Utilize este quickstart para começar a utilizar os Serviços Cognitivos Azure. Depois de criar um recurso de Serviço Cognitivo no portal Azure, você receberá um ponto final e uma chave para autenticar as suas aplicações.

Os Azure Cognitive Services são serviços baseados na nuvem com APIs rest, e SDKs da biblioteca de clientes disponíveis para ajudar os desenvolvedores a construir inteligência cognitiva em aplicações sem ter inteligência artificial direta (IA) ou habilidades ou conhecimentos de ciência de dados. O Azure Cognitive Services permite que os desenvolvedores adicionem facilmente funcionalidades cognitivas nas suas aplicações com soluções cognitivas que podem ver, ouvir, falar, entender e até começar a raciocinar.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição válida da Azure - [Crie uma gratuitamente.](https://azure.microsoft.com/free/cognitive-services/)

## <a name="create-a-new-azure-cognitive-services-resource"></a>Criar um novo recurso Azure Cognitive Services

1. Criar um recurso.

    #### <a name="multi-service-resource"></a>[Recurso multi-serviço](#tab/multiservice)

    O recurso multi-serviço chama-se **Serviços Cognitivos** no portal. [Criar um recurso de Serviços Cognitivos.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)

    Neste momento, o recurso multi-serviço permite o acesso aos seguintes Serviços Cognitivos:

    - Imagem Digitalizada
    - Content Moderator
    - Face
    - Compreensão de Idiomas (LUIS)
    - Análise de Texto
    - Tradutor
    - Bing Search v7 <br>(Web, Imagem, Notícias, Vídeo, Visual)
    - Pesquisa Personalizada do Bing
    - Pesquisa de Entidades do Bing
    - Sugestão Automática do Bing
    - Verificação Ortográfica do Bing

    #### <a name="single-service-resource"></a>[Recurso de serviço único](#tab/singleservice)

    Utilize os links abaixo para criar um recurso para os Serviços Cognitivos disponíveis:

    | Visão                      | Voz                  | Idioma                          | Decisão             | Pesquisar                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Visão computacional](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Serviços de Fala](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Leitor imersivo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Detetor de Anomalias](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing Search API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Serviço de visão personalizado](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Reconhecimento de Orador](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Compreensão de Idiomas (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Pesquisa Personalizada do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [Criador de FAQ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizador](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Pesquisa de Entidades do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Reconhecedor de Tinta Digital](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Análise de Texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Assistente de Métricas](https://go.microsoft.com/fwlink/?linkid=2142156)                    | [Verificação Ortográfica do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Tradutor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Sugestão Automática do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |

    ***

3. Na página **Criar,** forneça as seguintes informações:

    #### <a name="multi-service-resource"></a>[Recurso multi-serviço](#tab/multiservice)

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o seu recurso de serviços cognitivos. Por exemplo, *MyCognitiveServicesResource*. |
    | **Subscrição** | Selecione uma das suas subscrições Azure disponíveis. |
    | **Localização** | A localização do seu serviço cognitivo. Diferentes localizações podem introduzir latência, mas não têm impacto na disponibilidade de tempo de execução do seu recurso. |
    | **Escalão de preço** | O custo da sua conta de Serviços Cognitivos depende das opções que escolher e da sua utilização. Para mais informações, consulte os [detalhes dos preços da](https://azure.microsoft.com/pricing/details/cognitive-services/)API.
    | **Grupo de recursos** | O grupo de recursos Azure que conterá o seu recurso de Serviços Cognitivos. Pode criar um novo grupo ou adicioná-lo a um grupo pré-existente. |

    ![Tela de criação de recursos multi-serviço](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Clique em **Criar**.

    #### <a name="single-service-resource"></a>[Recurso de serviço único](#tab/singleservice)

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o seu recurso de serviços cognitivos. Por exemplo, *TextAnalyticsResource*. |
    | **Subscrição** | Selecione uma das suas subscrições Azure disponíveis. |
    | **Localização** | A localização do seu serviço cognitivo. Diferentes localizações podem introduzir latência, mas não têm impacto na disponibilidade de tempo de execução do seu recurso. |
    | **Escalão de preço** | O custo da sua conta de Serviços Cognitivos depende das opções que escolher e da sua utilização. Para mais informações, consulte os [detalhes dos preços da](https://azure.microsoft.com/pricing/details/cognitive-services/)API.
    | **Grupo de recursos** | O grupo de recursos Azure que conterá o seu recurso de Serviços Cognitivos. Pode criar um novo grupo ou adicioná-lo a um grupo pré-existente. |

    ![Tela de criação de recursos de serviço único](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Clique em **Criar**.

    ***

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Obtenha as chaves do seu recurso

1. Depois de o seu recurso ter sido implementado com sucesso, clique em **Ir para o recurso** sob os **Próximos Passos**.

    ![Pesquisa de Serviços Cognitivos](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. A partir do painel de arranque rápido que se abre, pode aceder à sua chave e ponto final.

    ![Obtenha chave e ponto final](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos contidos no grupo.

1. No portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e escolha **Grupos de Recursos**, para apresentar a lista dos seus grupos de recursos.
2. Localizar o grupo de recursos que contém o recurso a eliminar
3. Clique com o botão direito na listagem do grupo de recursos. Selecione **Eliminar grupo de recursos** e confirme.

## <a name="see-also"></a>Ver também

* [Autenticar pedidos aos Serviços Cognitivos da Azure](authentication.md)
* [O que é a Azure Cognitive Services?](./what-are-cognitive-services.md)
* [Criar um novo recurso utilizando a biblioteca de clientes da Azure Management](.\cognitive-services-apis-create-account-client-library.md)
* [Suporte linguístico natural](language-support.md)
* [Suporte de contentor estivador](cognitive-services-container-support.md)