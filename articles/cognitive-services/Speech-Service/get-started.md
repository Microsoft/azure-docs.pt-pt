---
title: Experimente o serviço de Voz gratuitamente
titleSuffix: Azure Cognitive Services
description: Começar com o serviço de Fala é fácil e acessível. Existem duas opções disponíveis gratuitamente para que possa descobrir o que o serviço pode fazer e decidir se é adequado para as suas necessidades.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 4604bfe7c815f79733f99a1a3727e4c68527e7ec
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656131"
---
# <a name="try-the-speech-service-for-free"></a>Experimente o serviço de Fala gratuitamente''

Neste artigo, você escolhe uma opção para testar facilmente o serviço de Fala gratuitamente para que possa descobrir o que o serviço pode fazer, e decidir se é adequado para as suas necessidades. Escolha uma das duas seguintes opções dependendo da sua situação e do seu caso de utilização:

- [Opção 1](#no-card): Obtenha imediatamente chaves API **de teste gratuitas** sem fornecer qualquer informação do cartão de crédito (precisa de ter uma conta Azure existente). O **teste gratuito** tem a duração de 30 dias e os dados são eliminados no final. Esta opção é melhor para uma experimentação rápida com o serviço.
- [Opção 2](#new-resource): Criar um novo recurso da Fala em Azure, gratuitamente utilizando uma **subscrição gratuita** (informações de cartão de crédito necessárias). Uma **subscrição gratuita** tem principalmente limites de tarifas mais rigorosos do que uma subscrição paga. Esta opção é melhor se quiser testar o serviço, mas também planeia fazer upgrade para uma subscrição paga no futuro, e não quer perder dados.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>Experimente o serviço de Fala sem informações sobre cartão de crédito

Complete os seguintes passos para ativar um teste gratuito de 30 dias e obtenha chaves API. O seu período experimental começa imediatamente quando os seguintes passos estiverem completos.

1. Vá experimentar o [Serviço Cognitivo.](https://azure.microsoft.com/try/cognitive-services/)
1. Selecione o separador APIs da **Fala.**
1. Escolha **obter a tecla API**.

Serão apresentadas com escolhas de faturação. Escolha a opção gratuita e, em seguida, leia e aprove o contrato de utilizador. Ser-lhe-á apresentadas as chaves que pode usar para experimentar o serviço de fala gratuitamente durante 30 dias.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Experimente o serviço de Fala criando um recurso Azure

Para os seguintes passos, precisa tanto de uma conta Microsoft como de uma conta Azure. Se não tiver uma conta Microsoft, pode inscrever-se gratuitamente no portal da conta da [Microsoft](https://account.microsoft.com/account). Selecione **Iniciar sessão com** a Microsoft e, quando for solicitado, selecione **Criar uma conta Microsoft**. Siga os passos para criar e verificar a sua nova conta Microsoft.

Assim que tiver uma conta Microsoft, vá à [página de inscrição](https://azure.microsoft.com/free/ai/)do Azure , selecione **'Iniciar grátis'** e criar uma nova conta Azure utilizando uma conta Microsoft.

> [!NOTE]
> O serviço Speech tem dois níveis de serviço: gratuito e subscrição, que têm diferentes limitações e benefícios. Quando se inscreve para uma conta Azure gratuita, vem com $200 em crédito de serviço que pode aplicar para uma subscrição de serviço de Fala paga, válida por até 30 dias.
>
> Se utilizar o nível de serviço gratuito e de baixo volume da Fala, pode manter esta subscrição gratuita mesmo depois de expirar o seu teste gratuito ou crédito de serviço.
>
> Para mais informações, consulte [os preços dos Serviços Cognitivos - Serviço de Fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-resource"></a>Criar o recurso

Para adicionar um recurso de serviço da Fala (nível gratuito ou pago) à sua conta Azure:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando a sua conta Microsoft.

1. Selecione **Criar um recurso** na parte superior esquerda do portal. Se não vir **Criar um recurso,** pode sempre encontrá-lo selecionando o menu colapsado na parte superior esquerda:

   ![botão de navegação colapsado](media/index/collapsed-nav.png)

1. Na **nova** janela, digite "discurso" na caixa de pesquisa e prima ENTER.

1. Nos resultados da pesquisa, selecione **Speech**.

   ![resultados de pesquisa de discurso](media/index/speech-search.png)

1. Selecione **Criar,** então:

   - Dê um nome único para o seu novo recurso. O nome ajuda a distinguir entre várias subscrições ligadas ao mesmo serviço.
   - Escolha a subscrição Azure a que o novo recurso está associado para determinar como as taxas são cobradas.
   - Escolha a [região](regions.md) onde o recurso será utilizado.
   - Escolha um nível de preços gratuito (F0) ou pago (S0). Para obter informações completas sobre preços e quotas de utilização para cada nível, selecione **Ver todos os detalhes**de preços .
   - Crie um novo grupo de recursos para esta subscrição do Discurso ou atribua a subscrição a um grupo de recursos existente. Os grupos de recursos ajudam-no a manter as suas várias subscrições Azure organizadas.
   - Selecione **Criar**. Isto irá levá-lo à visão geral de implementação e exibir mensagens de progresso de implementação.

> [!NOTE]
> Pode criar um número ilimitado de subscrições de nível padrão em uma ou várias regiões. No entanto, pode criar apenas uma subscrição de nível livre. As implementações do modelo no nível livre que permanecem por utilizar durante 7 dias serão automaticamente desativadas.

Leva alguns momentos para implementar o seu novo recurso discurso. Uma vez concluída a implementação, selecione **Vá para o recurso** e no painel de navegação esquerdo selecione **Keys** para exibir as teclas de subscrição do serviço Speech. Cada subscrição tem duas chaves; pode utilizar qualquer tecla na sua aplicação. Para copiar/colar rapidamente uma chave para o seu editor de código ou outro local, selecione o botão de cópia ao lado de cada tecla, troque as janelas para colar o conteúdo da pasta ao local pretendido.

> [!IMPORTANT]
> Estas chaves de subscrição são usadas para aceder à sua API do Serviço Cognitivo. Não partilhe as chaves. Guarde-os de forma segura, por exemplo, utilizando o Cofre de Chaves Azure. Recomendamos também a regeneração regular destas teclas. Só uma chave é necessária para fazer uma chamada da API. Ao regenerar a primeira tecla, pode utilizar a segunda tecla para o acesso continuado ao serviço.

## <a name="switch-to-a-new-subscription"></a>Mude para uma nova subscrição

Para mudar de uma subscrição para outra, por exemplo, quando o seu teste gratuito expirar ou quando publicar a sua aplicação, substitua a região e a chave de subscrição no seu código pela região e chave de subscrição do novo recurso Azure.

## <a name="about-regions"></a>Sobre regiões

- Se a sua aplicação utilizar um [SDK](speech-sdk.md)de `westus`Discurso, fornece o código da região, como, ao criar uma configuração de fala.
- Se a sua aplicação utilizar uma das [APIs](rest-apis.md)REST do serviço de Fala, a região faz parte do uri de ponta que utiliza ao fazer pedidos.
- As chaves criadas para uma região são válidas apenas naquela região. Tentar usá-los com outras regiões resultará em erros de autenticação.

## <a name="next-steps"></a>Passos seguintes

Complete um dos nossos quickstarts de 10 minutos ou confira as nossas amostras de SDK:

> [!div class="nextstepaction"]
> [Quickstart: Reconhecer discurso em](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [amostras de SDK de Discurso](speech-sdk.md#sample-source-code) C#
