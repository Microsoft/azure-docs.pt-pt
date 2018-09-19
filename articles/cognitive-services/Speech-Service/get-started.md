---
title: Experimente o serviço de Voz gratuitamente
description: Descubra como pode experimentar o serviço de voz, sem qualquer custo.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: cbb73b199255af7e7a91b1586b331acde035bfd7
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124517"
---
# <a name="try-the-speech-service-for-free"></a>Experimente o serviço de Voz gratuitamente

Começar a utilizar com o serviço de voz é fácil e económica. Uma avaliação gratuita de 30 dias permite-lhe detetar o que o serviço pode fazer e decidir se é adequada para as necessidades da sua aplicação.

Se precisar de mais tempo, inscreva-se para uma conta do Microsoft Azure — ele vem com 200 $ em crédito de serviço que pode aplicar no sentido de uma subscrição do serviço de voz paga durante 30 dias.

Por fim, o serviço de voz oferece um escalão gratuito de baixo volume que é adequado para o desenvolvimento de aplicativos. Pode manter esta subscrição gratuita mesmo depois de expira o seu crédito de serviço.

## <a name="free-trial"></a>Avaliação gratuita

A avaliação gratuita de 30 dias dá-lhe acesso ao padrão de escalão de preço por um período limitado.

Para se inscrever numa avaliação gratuita de 30 dias:

1. Aceda a [experimente os serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/).

1. Selecione o **APIs de voz** separador.

   ![Separador de serviços de voz](media/index/try-speech-api-free-trial1.png)
   
1. Sob **serviços de voz <sup>pré-visualização</sup>**, selecione o **obter chave de API** botão.

   ![Chave de API](media/index/try-speech-api-free-trial2.png)

1. Aceitar os termos e selecione sua localidade no menu pendente.

   ![Aceitar termos](media/index/try-speech-api-free-trial3.png)

1. Inicie sessão com a sua conta Microsoft, Facebook, LinkedIn ou do GitHub.

    Pode inscrever-se numa conta gratuita do Microsoft com o [portal de contas Microsoft](https://account.microsoft.com/account). Para começar a utilizar, clique em **iniciar sessão com a Microsoft** e, em seguida, quando lhe for pedido para iniciar sessão, clique em **criá-lo.** Siga os passos para criar e certifique-se a sua nova conta Microsoft.

Depois de iniciar sessão para experimentar os serviços cognitivos, começa a sua avaliação gratuita. A página Web apresentada apresenta uma lista de todos os serviços de serviços cognitivos do Azure para os quais tem atualmente subscrições de avaliação. Duas chaves de subscrição são apresentadas ao lado **serviços de voz**. Pode usar qualquer uma das chaves nas suas aplicações.

> [!NOTE]
> Todas as subscrições de avaliação gratuitas são na região E.U.A. oeste. Quando efetuar pedidos, certifique-se de que usar o `westus` ponto final.

## <a name="new-azure-account"></a>Nova conta do Azure

Novas contas do Azure recebem um crédito de serviço de US $200 que está disponível até 30 dias. Pode utilizar este crédito para explorar ainda mais o serviço de voz ou para iniciar o desenvolvimento de aplicativos.

Para se inscrever para uma nova conta do Azure, vá para o [página de inscrição do Azure](https://azure.microsoft.com/free/ai/), clique em **começar gratuitamente,** e criar uma nova conta do Azure com a sua conta Microsoft.

Pode inscrever-se numa conta gratuita do Microsoft com o [portal de contas Microsoft](https://account.microsoft.com/account). Para começar a utilizar, clique em **iniciar sessão com a Microsoft** e, em seguida, quando lhe for pedido para iniciar sessão, clique em **criá-lo.** Siga os passos para criar e certifique-se a sua nova conta Microsoft.

Depois de criar a sua conta do Azure, siga os passos na secção seguinte para iniciar uma subscrição para o serviço de voz.

## <a name="create-a-speech-resource-in-azure"></a>Criar um recurso de fala no Azure

Para adicionar um recurso de serviço de voz (escalão gratuito ou pago) à sua conta do Azure:

1. Inicie sessão para o [portal do Azure](https://ms.portal.azure.com/) com a sua conta Microsoft.

1. Selecione **criar um recurso** na parte superior esquerda do portal.

    ![Criar um recurso](media/index/try-speech-api-create-speech1.png)

1. Na **New** janela, procure **voz**.

1. Nos resultados da pesquisa, selecione **voz (pré-visualização)**.

    ![Selecione a conversão de voz (pré-visualização)](media/index/try-speech-api-create-speech2.png)

1. Sob **voz (pré-visualização)**, clique nas **criar** botão.

    ![Selecione o botão Criar](media/index/try-speech-api-create-speech3.png)

1. Sob **criar**, introduza:

    * Um nome para o novo recurso. O nome auxilia a distinguir entre várias subscrições para o mesmo serviço.
    * Escolha a subscrição do Azure que o novo recurso está associado para determinar como são cobradas as taxas.
    * Selecione a região onde o recurso será utilizado. Atualmente, o serviço de voz está disponível nas regiões E.U.A. oeste, Europa do Norte e Ásia Oriental.
    * Escolha a um escalão de preço gratuito ou pago. Clique em **vista de detalhes de preço completos** para obter informações completas sobre as quotas de utilização e de preços para cada camada.
    * Criar um novo grupo de recursos para esta subscrição de voz ou o atribuir a subscrição a um grupo de recursos existente. Os grupos de recursos ajudam a que manter suas várias subscrições do Azure, organizadas.
    * Para obter acesso conveniente a sua subscrição no futuro, selecione o **afixar ao dashboard** caixa de verificação.
    * Selecione **criar.**

    ![Selecione o botão Criar](media/index/try-speech-api-create-speech4.png)

    Ele demora alguns minutos a criar e implementar o novo recurso de voz. Selecione **guia de introdução** para ver informações sobre o novo recurso.

    ![Painel de início rápido](media/index/try-speech-api-create-speech5.png)

1. Sob **início rápido**, clique nas **chaves** link no passo 1 para exibir as chaves de subscrição. Cada subscrição tem duas chaves; Pode utilizar qualquer um dos chave em seu aplicativo. Selecione o botão junto a cada chave para copiá-lo para a área de transferência para colar no seu código.

> [!NOTE]
> Pode criar um número ilimitado de escalão standard subscrições numa ou em várias regiões. No entanto, pode criar apenas uma subscrição do escalão gratuito. Implementações de modelos no escalão gratuito, que permanecem não utilizadas durante sete dias será automaticamente desativado.

## <a name="switch-to-a-new-subscription"></a>Mudar para uma nova subscrição

Para mudar de uma subscrição para outro, por exemplo, quando a avaliação gratuita expira ou quando publicar a sua aplicação, substitua a chave de subscrição e região no seu código com a chave de subscrição e região do novo recurso do Azure.

> [!NOTE]
> As chaves de avaliação gratuitas são criadas no Oeste dos E.U.A. (`westus`) região. Uma subscrição criada através do dashboard do Azure pode ser em alguma outra região, se assim o desejar.

* Se o aplicativo usar um [SDK de voz](speech-sdk.md), indique o código de região, como `westus`, ao instanciar `RecognizerFactory`. 
* Se seu aplicativo usa um do serviço de voz [REST APIs](rest-apis.md), a região é a parte do ponto final do URI a utilizar quando são efetuados pedidos.

Criado para uma região de chaves são válidas apenas nessa região. A tentar usá-los com outras regiões irá resultar em erros de autenticação.

## <a name="next-steps"></a>Passos Seguintes

Execute um dos nossos inícios rápidos de 10 minutos ou consulte as nossas amostras SDK:

> [!div class="nextstepaction"]
> [Início rápido: Reconhecer voz em c#](quickstart-csharp-dotnet-windows.md)
> [amostras do SDK de voz](speech-sdk.md#get-the-samples)
