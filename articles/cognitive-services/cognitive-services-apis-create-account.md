---
title: Criar um recurso de serviços cognitivas no portal do Azure
titleSuffix: Azure Cognitive Services
description: Introdução aos serviços cognitivas do Azure criando e assinando um recurso no portal do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: dd4444bf42bcc8dda95f8fa37b42a365538efa85
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482876"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Criar um recurso de serviços cognitivas usando o portal do Azure

Use este guia de início rápido para começar a usar os serviços cognitivas do Azure. Depois de criar um recurso de serviço cognitiva no portal do Azure, você obterá um ponto de extremidade e uma chave para autenticar seus aplicativos.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura válida do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Criar um novo recurso de serviços cognitivas do Azure

1. Criar um recurso.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Recurso de vários serviços](#tab/multiservice)
    
    O recurso de vários serviços é chamado de **Serviços cognitivas** no Portal. [Crie um recurso de serviços cognitivas](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    Neste momento, o recurso de vários serviços permite o acesso aos seguintes serviços cognitivas:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Imagem Digitalizada  | Content Moderator                                    | Rostos               | Compreensão de Idiomas (LUIS) | Análise de Texto   |
    | Texto do Tradutor  | Pesquisa do Bing v7 <br>(Web, imagem, notícias, vídeo, Visual) | Pesquisa Personalizada do Bing | Pesquisa de Entidades do Bing            | Sugestão Automática do Bing |
    | Verificação Ortográfica do Bing |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resourcetabsingleservice"></a>[Recurso de serviço único](#tab/singleservice)

    Use os links abaixo para criar um recurso para os serviços cognitivas disponíveis:

    | Visão                      | Voz                  | Idioma                          | Decisão             | Pesquisa                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Visão do computador](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Serviços de Voz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Leitor de imersão](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Detector de anomalias](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [API de Pesquisa do Bing v7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Serviço de visão personalizada](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Reconhecimento do Locutor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Pesquisa Personalizada do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Sorridente](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [Criador de FAQ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizador](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Pesquisa de Entidades do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Reconhecedor de tinta](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Análise de Texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Verificação de Ortografia do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Tradução de Texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Sugestão Automática do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. Na página **criar** , forneça as seguintes informações:

    #### <a name="multi-service-resourcetabmultiservice"></a>[Recurso de vários serviços](#tab/multiservice)

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para seu recurso de serviços cognitivas. Por exemplo, *MyCognitiveServicesResource*. |
    | **Subscrição** | Selecione uma das suas assinaturas do Azure disponíveis. |
    | **Localização** | O local de sua instância de serviço cognitiva. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de tempo de execução do recurso. |
    | **Escalão de preço** | O custo de sua conta de serviços cognitivas depende das opções que você escolher e do uso. Para obter mais informações, consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/)da API.
    | **Grupo de recursos** | O grupo de recursos do Azure que conterá seu recurso de serviços cognitivas. Você pode criar um novo grupo ou adicioná-lo a um grupo já existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Clique em **Criar**.

    #### <a name="single-service-resourcetabsingleservice"></a>[Recurso de serviço único](#tab/singleservice)

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para seu recurso de serviços cognitivas. Por exemplo, *TextAnalyticsResource*. |
    | **Subscrição** | Selecione uma das suas assinaturas do Azure disponíveis. |
    | **Localização** | O local de sua instância de serviço cognitiva. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de tempo de execução do recurso. |
    | **Escalão de preço** | O custo de sua conta de serviços cognitivas depende das opções que você escolher e do uso. Para obter mais informações, consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/)da API.
    | **Grupo de recursos** | O grupo de recursos do Azure que conterá seu recurso de serviços cognitivas. Você pode criar um novo grupo ou adicioná-lo a um grupo já existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Clique em **Criar**.

    ***


## <a name="get-the-keys-for-your-resource"></a>Obter as chaves para seu recurso

1. Depois que o recurso for implantado com êxito, clique em **ir para o recurso** em **próximas etapas**.

    ![Pesquisar serviços cognitivas](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. No painel de início rápido que é aberto, você pode acessar sua chave e ponto de extremidade.

    ![Obter chave e ponto de extremidade](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos contidos no grupo.

1. No portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e escolha **Grupos de Recursos**, para apresentar a lista dos seus grupos de recursos.
2. Localize o grupo de recursos que contém o recurso a ser excluído
3. Clique com o botão direito do mouse na listagem do grupo de recursos. Selecione **Eliminar grupo de recursos** e confirme.

## <a name="see-also"></a>Consulte também

* [Autenticar solicitações para serviços cognitivas do Azure](authentication.md)
* [O que são os serviços cognitivas do Azure?](Welcome.md)
* [Suporte a idioma natural](language-support.md)
* [Suporte a contêiner do Docker](cognitive-services-container-support.md)
