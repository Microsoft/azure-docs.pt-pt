---
title: Quickstart - Obtenha um número de telefone dos Serviços de Comunicação Azure
description: Saiba como comprar um número de telefone dos Serviços de Comunicação utilizando o portal Azure.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/09/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: f5cf8f8ef004dacc9fe2bbdd1b815f2ae5275311
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298121"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Quickstart: Obtenha um número de telefone usando o portal Azure

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Inicie-se com os Serviços de Comunicação Azure utilizando o portal Azure para comprar um número de telefone.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Um recurso ativo dos Serviços de Comunicação.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Obtenha um número de telefone

Para começar a aprovisionar os números, aceda ao seu recurso de Serviços de Comunicação no [portal Azure](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Screenshot mostrando a página principal de um recurso dos Serviços de Comunicação.":::

### <a name="getting-new-phone-numbers"></a>Obtenção de novos números de telefone

Navegue para a lâmina números de telefone no menu de recursos.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Screenshot mostrando a página de telefone de um recurso de serviços de comunicação.":::

Pressione o `Get` botão para lançar o assistente. O assistente na `Phone numbers` lâmina irá acompanhá-lo através de uma série de perguntas que o ajudam a escolher o número de telefone que melhor se adequa ao seu cenário. 

Primeiro terá de escolher `Country/region` onde pretender o número de telefone. Depois de selecionar o País/região, terá então de selecionar as `phone plan` melhores suites que necessita. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Screenshot mostrando a vista de números de telefone Get.":::

### <a name="select-a-phone-plan"></a>Selecione um plano de telefone

A seleção de um plano telefónico é dividida em dois passos: 

1. A seleção do [tipo Número](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. A seleção do [Plano](../../concepts/telephony-sms/plan-solution.md#plans)

Oferecemos dois tipos de números atualmente: `Geographic` e `Toll-free` . Quando tiver selecionado um tipo de Número, ser-lhe-á oferecido vários Planos a partir dos quais pode escolher.

> [!NOTE]
> Atualmente, apenas apoiamos a seleção de números de telefone com chamada de entrada ou saída. No entanto, pode adquirir um número de telefone com chamada de entrada ativada e, em seguida, configurar o ID do chamador de saída para corresponder ao número do número de telefone ativado por chamadas de entrada (o que os utilizadores vêem quando os ligam a partir da sua aplicação de Serviços de Comunicação).
> Isto aplica-se apenas a chamadas bidirecionais. O SMS bidireccionano é suportado de forma nativa.

No nosso exemplo, escolhemos um `Toll-free` tipo de número com o `Outbound calling` Plano.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Screenshot mostrando a vista de planos Select.":::

### <a name="declare-purpose"></a>Declarar propósito

Em seguida, o assistente irá pedir-lhe para o propósito de usar o número. Recolhemos esta informação para aplicar os regulamentos corretos de chamada de emergência e impostos.

:::image type="content" source="../media/quickstart-search-and-acquire-bot-or-human.png" alt-text="Screenshot mostrando a seleção de um bot ou humano dentro do processo de aquisição do número de telefone.":::

A partir daqui, clique `Next: Numbers` no botão na parte inferior da página para personalizar o número de telefone que pretende providenciar.

### <a name="customizing-phone-numbers"></a>Personalizar números de telefone

Na `Numbers` página, irá personalizar os números de telefone que pretende prever.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Screenshot mostrando a página de seleção de números.":::

> [!NOTE]
> Este quickstart mostra o `Toll-free` fluxo de personalização do tipo Número. A experiência pode ser ligeiramente diferente se tiver escolhido o `Geographic` tipo Número, mas o resultado final será o mesmo.

Escolha a `Area code` lista dos códigos de Área disponíveis e introduza a quantidade que pretende disponibilizar e clique para `Search` encontrar números que satisfaçam os requisitos selecionados. Os números de telefone que satisfaçam as suas necessidades serão mostrados juntamente com o seu custo mensal.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Screenshot mostrando a página de seleção de números com números reservados.":::

> [!NOTE]
> A disponibilidade depende do tipo, localização e plano que selecionou.
> Os números são reservados por um curto período de tempo antes de a transação expirar. Se a transação expirar, terá de reescorê-lo.

Para ver o resumo da compra e fazer o seu pedido, clique `Next: Summary` no botão na parte inferior da página.

### <a name="place-order"></a>Fazer encomenda

A página de resumo irá rever o tipo de número, funcionalidades, números de telefone e custo mensal total para a disponibilização dos números de telefone.

> [!NOTE]
> Os preços apresentados são os **encargos mensais recorrentes** que cobrem o custo de locação do número de telefone selecionado para si. Não estão incluídos nesta visão os **custos pay-as-you-go** que são incorridos quando faz ou recebe chamadas. As listas de preços estão [disponíveis aqui.](../../concepts/pricing.md) Estes custos dependem do tipo de número e dos destinos chamados. Por exemplo, o preço por minuto para uma chamada de um número regional de Seattle para um número regional em Nova Iorque e uma chamada do mesmo número para um número de telemóvel do Reino Unido pode ser diferente.

Finalmente, clique `Place order` na parte inferior da página para confirmar.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Screenshot mostrando a página Resumo com o tipo número, funcionalidades, números de telefone e custo mensal total mostrado.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Encontre os seus números de telefone no portal Azure

Navegue para o seu Recurso de Comunicação Azure no [portal Azure:](https://portal.azure.com)

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Screenshot mostrando a página principal de um recurso de serviços de comunicação.":::

Selecione o separador Números de Telefone no menu para gerir os seus números de telefone.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Screenshot mostrando a página de número de telefone de um recurso de comunicação.":::

> [!NOTE]
> Pode levar alguns minutos para que os números apresentados sejam mostrados nesta página.

## <a name="troubleshooting"></a>Resolução de problemas

Questões e questões comuns:

- Apenas os EUA são apoiados para comprar números de telefone neste momento. Isto baseia-se no endereço de faturação da subscrição a que o recurso está associado. Neste momento, não é possível mover um recurso para outra subscrição.

- Quando um número de telefone é apagado, o número de telefone não será libertado ou poderá ser recomprado até ao final do ciclo de faturação.

- Quando um recurso de Serviços de Comunicação é eliminado, os números de telefone associados a esse recurso serão automaticamente libertados ao mesmo tempo.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido aprendeu a:

> [!div class="checklist"]
> * Comprar um número de telefone
> * Gerencie o seu número de telefone
> * Liberte um número de telefone

> [!div class="nextstepaction"]
> [Enviar um SMS](../telephony-sms/send.md) 
>  [Começa com a chamada](../voice-video-calling/getting-started-with-calling.md)
