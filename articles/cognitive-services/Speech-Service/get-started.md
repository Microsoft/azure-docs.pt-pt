---
title: Experimente o Speech Service gratuitamente
titleSuffix: Azure Cognitive Services
description: A introdução ao serviço de fala é fácil e acessível. Uma avaliação gratuita de 30 dias permite que você descubra o que o serviço pode fazer e decida se ele é adequado para as necessidades do seu aplicativo.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: eb4478a435fbfc899055a60e13b318be771652f7
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72527531"
---
# <a name="try-speech-services-for-free"></a>Experimente os serviços de fala gratuitamente

A introdução aos serviços de fala é fácil e acessível. Uma avaliação gratuita de 30 dias permite que você descubra o que o serviço pode fazer e decida se ele é adequado para as necessidades do seu aplicativo.

Se você precisar de mais tempo, Inscreva-se em uma conta de Microsoft Azure — ele vem com $200 de crédito de serviço que você pode aplicar a uma assinatura de serviços de fala paga por até 30 dias.

Por fim, os serviços de fala oferecem uma camada gratuita de baixo volume que é adequada para o desenvolvimento de aplicativos. Você pode manter essa assinatura gratuita mesmo depois que seu crédito de serviço expirar.

## <a name="free-trial"></a>Avaliação gratuita

A avaliação gratuita de 30 dias fornece acesso ao tipo de preço Standard por um período limitado.

Para se inscrever para uma avaliação gratuita de 30 dias:

1. Vá para [experimentar serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/).

1. Selecione a guia **APIs de fala** .

   ![Guia Speech API-serviços de fala](media/index/cognitive-services-speech-api-tab.png)

1. Em **serviços de fala**, selecione **obter chave de API**.

   ![Speech API-obter chave de API](media/index/speech-api-get-api-key.png)

1. Concorde com os termos e selecione sua localidade no menu suspenso.

   ![Speech API-concordar com os termos](media/index/speech-api-agree-to-terms.png)

1. Entre usando sua conta Microsoft, Facebook, LinkedIn ou GitHub.

    Você pode se inscrever para obter uma conta Microsoft gratuita no [portal conta Microsoft](https://account.microsoft.com/account). Para começar, selecione **entrar com a conta da Microsoft** e, quando solicitado a entrar, selecione **criar um.** Siga as etapas para criar e verificar o novo conta Microsoft.

Depois de entrar para experimentar os serviços cognitivas, sua avaliação gratuita começa. A página da Web exibida lista todos os serviços de serviços cognitivas do Azure para os quais você atualmente tem assinaturas de avaliação. Duas chaves de assinatura são listadas ao lado de **serviços de fala**. Você pode usar qualquer chave em seus aplicativos.

> [!NOTE]
> Todas as assinaturas de avaliação gratuita estão na região oeste dos EUA. Ao fazer solicitações, certifique-se de usar o ponto de extremidade `westus`.

## <a name="new-azure-account"></a>Nova conta do Azure

Novas contas do Azure recebem um crédito de serviço $200 que está disponível por até 30 dias. Você pode usar esse crédito para explorar ainda mais os serviços de fala ou para iniciar o desenvolvimento de aplicativos.

Para se inscrever em uma nova conta do Azure, vá para a [página de inscrição do Azure](https://azure.microsoft.com/free/ai/), selecione **Iniciar gratuitamente** e crie uma nova conta do Azure usando seu conta Microsoft.

Você pode se inscrever para obter uma conta Microsoft gratuita no [portal conta Microsoft](https://account.microsoft.com/account). Para começar, selecione **entrar com a conta da Microsoft** e, quando solicitado a entrar, selecione **criar um.** Siga as etapas para criar e verificar o novo conta Microsoft.

Depois de criar sua conta do Azure, siga as etapas na próxima seção para iniciar uma assinatura para os serviços de fala.

## <a name="create-a-speech-resource-in-azure"></a>Criar um recurso de fala no Azure

Para adicionar um recurso de serviços de fala (camada gratuita ou paga) à sua conta do Azure:

1. Entre no [portal do Azure](https://portal.azure.com/) usando seu conta Microsoft.

1. Selecione **criar um recurso** na parte superior esquerda do Portal.

    ![Speech API-criar um recurso](media/index/speech-api-create-resource.png)

1. Na **nova** janela, procure por **fala**.

1. Nos resultados da pesquisa, selecione **fala**.

    ![Speech API-selecionar fala](media/index/speech-api-select-speech.png)

1. Em **fala**, selecione o botão **criar** .

    ![Botão Speech API-criar](media/index/speech-api-create-button.png)

1. Em **criar**, digite:

   * Um nome para o novo recurso. O nome ajuda você a distinguir entre várias assinaturas no mesmo serviço.
   * Escolha a assinatura do Azure à qual o novo recurso está associado para determinar como as tarifas são cobradas.
   * Escolha a [região](regions.md) em que o recurso será usado.
   * Escolha um tipo de preço gratuito ou pago. Selecione **Exibir detalhes de preços completos** para obter informações completas sobre cotas de preço e uso para cada camada.
   * Crie um novo grupo de recursos para esta assinatura de fala ou atribua a assinatura a um grupo de recursos existente. Os grupos de recursos ajudam a manter suas várias assinaturas do Azure organizadas.
   * Para ter acesso conveniente à sua assinatura no futuro, marque a caixa de seleção **fixar no painel** .
   * Selecione **criar.**

     ![Speech API-selecione criar](media/index/speech-api-select-create.png)

     Leva um tempo para criar e implantar seu novo recurso de fala. Selecione **início rápido** para ver informações sobre o novo recurso.

     ![Speech API-implantar o recurso](media/index/speech-api-deploy-resource.png)

1. Em **início rápido**, selecione o link **chaves** na etapa 1 para exibir as chaves de assinatura. Cada assinatura tem duas chaves; Você pode usar qualquer chave em seu aplicativo. Selecione o botão ao lado de cada chave para copiá-lo para a área de transferência para colar em seu código.

> [!NOTE]
> Você pode criar um número ilimitado de assinaturas de camada Standard em uma ou várias regiões. No entanto, você pode criar apenas uma assinatura de camada gratuita. Implantações de modelo na camada gratuita que permanecem sem uso por 7 dias serão desativados automaticamente.

## <a name="switch-to-a-new-subscription"></a>Alternar para uma nova assinatura

Para alternar de uma assinatura para outra, por exemplo, quando sua avaliação gratuita expirar ou quando você publicar seu aplicativo, substitua a região e a chave de assinatura em seu código pela chave de assinatura e região do novo recurso do Azure.

> [!NOTE]
> As chaves de avaliação gratuitas são criadas na região oeste dos EUA (`westus`). Uma assinatura criada por meio do painel do Azure pode estar em alguma outra região, se você preferir.

* Se seu aplicativo usar um [SDK de fala](speech-sdk.md), você fornecerá o código de região, como `westus`, ao criar uma configuração de fala.
* Se seu aplicativo usa uma das [APIs REST](rest-apis.md)dos serviços de fala, a região faz parte do URI do ponto de extremidade que você usa ao fazer solicitações.

As chaves criadas para uma região são válidas somente nessa região. A tentativa de usá-los com outras regiões resultará em erros de autenticação.

## <a name="next-steps"></a>Passos seguintes

Conclua um dos nossos guias de início rápido de 10 minutos ou Confira nossos exemplos de SDK:

> [!div class="nextstepaction"]
> [Início rápido: reconhecer a C# fala em exemplos do SDK do](quickstart-csharp-dotnet-windows.md) 
> [Speech](speech-sdk.md#get-the-samples)
