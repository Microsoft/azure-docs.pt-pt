---
title: Criar um ponto de final de voz personalizada com os serviços de voz no Azure | Documentos da Microsoft
description: Saiba como criar um ponto final de voz em texto personalizado usando os serviços de voz do Azure.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 03/12/2018
ms.author: panosper
ms.openlocfilehash: 1f7a84d187ba6279caad4926d54bfc56254152af
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57863003"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Criar um ponto final de conversão de voz em texto personalizado

Depois de criar modelos acústicos personalizados ou modelos de linguagem, pode implementá-las num ponto de final de voz em texto personalizado.

## <a name="create-an-endpoint"></a>Criar um ponto final
Para criar um novo ponto final personalizado, selecione **pontos de extremidade** sobre o **voz personalizada** menu na parte superior da página. Esta ação leva-o para o **pontos de extremidade** página, que contém uma tabela de pontos finais personalizados atuais. Se ainda não criou quaisquer pontos de extremidade, a tabela estiver vazia. A região atual está refletida no título da tabela.

Para criar uma implementação para um idioma diferente, selecione **localidade de alteração**. Para obter mais informações sobre os idiomas suportados.

Para criar um novo ponto final, selecione **criar novo**. Na **Create Endpoint** painel, introduza as informações no **nome** e **Descrição** caixas de implantação personalizada.

Na **subscrição** combinação caixa, selecione a subscrição que pretende utilizar. Se for uma subscrição (tendo) S0, pode selecionar pedidos em simultâneo.

Também pode selecionar se o registo de conteúdo é mudado ou desativar. Ou seja, que está selecionando se o tráfego de ponto final é armazenado. Se não estiver selecionada, armazenar o tráfego será suprimido. Para todos os conteúdos com sessão iniciada pode encontrar ligações de transferência no ponto de extremidade -> Vista de detalhes

Na **modelo acústico** , selecione o modelo acústico que pretende, e, no **modelo de idioma** , selecione o modelo de idioma que pretende. As opções para modelos de acústica e idioma incluem sempre os modelos bases do Microsoft. A seleção do modelo de base limita as combinações. Não é possível misturar os modelos bases conversacionais com pesquisa e estabelecer modelos de bases.

> [!NOTE]
> Certifique-se de que aceite os termos de utilização e obter informações sobre preços, selecionando a caixa de verificação.
>

Depois de selecionar os modelos de acústica e idioma, selecione **criar**. Esta ação devolve-lhe a **implementações** página. A tabela inclui agora uma entrada que corresponde ao seu novo ponto de extremidade. Estado do ponto de extremidade reflete o estado atual enquanto ele está a ser criado. Pode demorar até 30 minutos para instanciar um novo ponto final com os seus modelos personalizados. Quando o estado da implementação estiver *Complete*, o ponto final está pronto para ser utilizado.

Quando a implementação estiver pronta, o nome do ponto final torna-se uma ligação. Selecionar a ligação apresenta os **informações do ponto final** página, que apresenta os URLs do seu ponto final personalizado para utilizar com a um pedido de HTTP ou a Microsoft cognitivos serviços de voz biblioteca de cliente, que utiliza web sockets.

## <a name="next-steps"></a>Passos Seguintes

Para mais tutoriais, consulte:
- [Obter a subscrição de avaliação dos Serviços de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Criar um modelo acústico personalizado](how-to-customize-acoustic-models.md)
- [Criar modelo de idioma personalizado](how-to-customize-language-model.md)
