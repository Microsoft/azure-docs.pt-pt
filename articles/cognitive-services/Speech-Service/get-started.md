---
title: Experimente o serviço de Voz gratuitamente
titleSuffix: Azure Cognitive Services
description: Começar com o serviço de fala é fácil e acessível. Existem duas opções disponíveis gratuitamente para que possa descobrir o que o serviço pode fazer e decidir se é adequado para as suas necessidades.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: c15601b4c58ce072881b3c244f10bc196bceb853
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167815"
---
# <a name="try-the-speech-service-for-free"></a>Experimente o serviço de Voz gratuitamente

Neste artigo, você escolhe uma opção para testar facilmente o serviço de Fala gratuitamente para que você possa descobrir o que o serviço pode fazer, e decidir se é adequado para as suas necessidades. Escolha uma das duas opções seguintes dependendo da sua situação e da sua caixa de utilização:

- [Opção 1](#no-card): Obtenha imediatamente as chaves API **de teste gratuito** sem fornecer qualquer informação do cartão de crédito (precisa de ter uma conta Azure existente). O **teste gratuito** tem a duração de 30 dias e os dados são eliminados no final. Esta opção é melhor para uma rápida experimentação com o serviço.
- [Opção 2](#new-resource): Criar um novo recurso speech em Azure, sem custos, utilizando uma **subscrição gratuita** (informações do cartão de crédito necessárias). Uma **subscrição gratuita** tem principalmente limites de taxa mais rigorosos do que uma subscrição paga. Esta opção é melhor se quiser testar o serviço, mas também planeia fazer upgrade para uma subscrição paga no futuro, e não quer perder dados.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>Experimente o serviço De discurso sem informações sobre cartão de crédito

Complete os seguintes passos para ativar um teste gratuito de 30 dias e obter chaves API. O seu período experimental começa imediatamente quando os seguintes passos estiverem completos.

1. Vá para [tentar o Serviço Cognitivo.](https://azure.microsoft.com/try/cognitive-services/)
1. Selecione o **separador APIs de discurso.**
1. Escolha **Obter API Key**.

Serão apresentadas escolhas de faturação. Escolha a opção gratuita e depois leia e aprove o contrato de utilizador. Serão-lhe apresentadas as chaves que pode usar para experimentar o serviço de Discurso gratuitamente durante 30 dias.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Experimente o serviço Speech criando um recurso Azure

Para os seguintes passos, precisa tanto de uma conta Microsoft como de uma conta Azure. Se não tiver uma conta Microsoft, pode inscrever-se gratuitamente no portal da conta da [Microsoft.](https://account.microsoft.com/account) Selecione **Iniciar sessão com** a Microsoft e, quando lhe pedirem para iniciar sessão, selecione **Criar uma conta Microsoft**. Siga os passos para criar e verificar a sua nova conta Microsoft.

Assim que tiver uma conta Microsoft, vá à página de inscrição do [Azure](https://azure.microsoft.com/free/ai/), selecione **Iniciar gratuitamente**e crie uma nova conta Azure utilizando uma conta Microsoft.

> [!NOTE]
> O serviço Discurso tem dois níveis de serviço: gratuito e subscrição, que têm diferentes limitações e benefícios. Quando se inscreve numa conta Azure gratuita, vem com 200 dólares no crédito de serviço que pode candidatar-se a uma subscrição de serviço de Discurso pago, válida até 30 dias.
>
> Se utilizar o nível de serviço de fala gratuito e de baixo volume, pode manter esta subscrição gratuita mesmo depois de expirar o seu crédito gratuito de teste ou serviço.
>
> Para mais informações, consulte [os preços dos Serviços Cognitivos - Serviço de Fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-resource"></a>Criar o recurso

Para adicionar um recurso de serviço de discurso (nível gratuito ou pago) à sua conta Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta Microsoft.

1. **Selecione Criar um recurso** na parte superior esquerda do portal. Se não vir **Criar um recurso,** pode sempre encontrá-lo selecionando o menu colapsado na parte superior esquerda:

   ![botão de navegação colapsado](media/index/collapsed-nav.png)

1. Na **nova** janela, escreva "discurso" na caixa de pesquisa e prima ENTER.

1. Nos resultados da pesquisa, selecione **Speech**.

   ![resultados da pesquisa de discursos](media/index/speech-search.png)

1. Selecione **Criar,** em seguida:

   - Dê um nome único para o seu novo recurso. O nome ajuda-o a distinguir entre várias subscrições ligadas ao mesmo serviço.
   - Escolha a subscrição Azure a que o novo recurso está associado para determinar como as taxas são cobradas.
   - Escolha a [região](regions.md) onde o recurso será utilizado.
   - Escolha um preço gratuito (F0) ou pago (S0). Para obter informações completas sobre preços e quotas de utilização para cada nível, **selecione Ver todos os detalhes dos preços**.
   - Crie um novo grupo de recursos para esta subscrição do Discurso ou atribua a subscrição a um grupo de recursos existente. Os grupos de recursos ajudam-no a manter as suas várias subscrições Azure organizadas.
   - Selecione **Criar**. Isto irá levá-lo à visão geral da implementação e exibir mensagens de progresso de implantação.

> [!NOTE]
> Pode criar um número ilimitado de subscrições de nível padrão em uma ou várias regiões. No entanto, pode criar apenas uma subscrição de nível livre. As implementações de modelos no nível livre que permanecem sem utilização durante 7 dias serão automaticamente desativadas.

Leva alguns momentos para implementar o seu novo recurso speech. Assim que a implementação estiver concluída, selecione **Vá para o recurso** e no painel de navegação esquerdo selecione **Chaves** para exibir as teclas de subscrição do serviço Desíproca. Cada subscrição tem duas chaves; pode utilizar qualquer uma das chaves na sua aplicação. Para copiar/colar rapidamente uma chave para o seu editor de código ou outro local, selecione o botão de cópia ao lado de cada tecla, altere as janelas para colar o conteúdo da área de transferência para o local pretendido.

> [!IMPORTANT]
> Estas chaves de subscrição são usadas para aceder à sua API de Serviço Cognitivo. Não partilhem as chaves. Guarde-os de forma segura, por exemplo, utilizando o Cofre da Chave Azure. Recomendamos também a regeneração regular destas teclas. Apenas uma chave é necessária para fazer uma chamada da API. Ao regenerar a primeira chave, pode utilizar a segunda chave para o acesso continuado ao serviço.

## <a name="switch-to-a-new-subscription"></a>Mude para uma nova subscrição

Para mudar de uma subscrição para outra, por exemplo, quando o seu teste gratuito expirar ou quando publicar a sua aplicação, substitua a região e a chave de subscrição no seu código pela região e chave de subscrição do novo recurso Azure.

## <a name="about-regions"></a>Sobre as regiões

- Se a sua aplicação utilizar um [Speech SDK,](speech-sdk.md)forneça o identificador da região, por `westus` exemplo, ao criar uma configuração de fala.
- Se a sua aplicação utilizar uma das [APIs rest](rest-apis.md)do serviço de fala, a região faz parte do ponto final que utiliza ao fazer pedidos.
- As chaves criadas para uma região são válidas apenas naquela região. Tentar usá-los com outras regiões resultará em erros de autenticação.

## <a name="next-steps"></a>Passos seguintes

Complete um dos nossos quickstarts de 10 minutos ou confira as nossas amostras SDK:

> [!div class="nextstepaction"]
> [Quickstart: Reconhecer o discurso com o Discurso CLI](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programmer-tool-spx) 
>  [Amostras de SDK de fala](speech-sdk.md#sample-source-code)
