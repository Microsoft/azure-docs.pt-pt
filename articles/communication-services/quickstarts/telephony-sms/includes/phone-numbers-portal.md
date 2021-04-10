---
ms.openlocfilehash: 82e475c97aa2c68a33a48b04fe3e45fb13728b88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629404"
---

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Um recurso ativo dos Serviços de Comunicação.](../../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Obtenha um número de telefone

Para começar a aprovisionar os números, aceda ao seu recurso de Serviços de Comunicação no [portal Azure](https://portal.azure.com).

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Screenshot mostrando a página principal de um recurso dos Serviços de Comunicação.":::

### <a name="search-for-available-phone-numbers"></a>Pesquisa rumo a números de telefone disponíveis

Navegue para a lâmina **números de telefone** no menu de recursos.

:::image type="content" source="../../media/manage-phone-azure-portal-phone-page.png" alt-text="Screenshot mostrando a página de telefone de um recurso de serviços de comunicação.":::

Prima o botão **Obter** para lançar o assistente. O assistente na lâmina dos **números de telefone** irá acompanhá-lo através de uma série de perguntas que o ajudam a escolher o número de telefone que melhor se adequa ao seu cenário. 

Primeiro terá de escolher o **País/região** onde pretende provisionar o número de telefone. Depois de selecionar o País/região, terá então de selecionar o **caso Utilização,** que melhor acomoda as suas necessidades. 

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers.png" alt-text="Screenshot mostrando a vista de números de telefone Get.":::

### <a name="select-your-phone-number-features"></a>Selecione as funcionalidades do seu número de telefone

Configurar o seu número de telefone é dividido em dois passos: 

1. A seleção do [tipo número](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. A seleção das [capacidades numerais](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)

Pode selecionar entre dois tipos de números de telefone: **Geográfico** e **Gratuito**. Quando tiver selecionado um tipo de número, pode então escolher a funcionalidade.

No nosso exemplo, selecionamos um tipo de número **gratuito** com as **funcionalidades** de chamada de saída e **de entrada e saída.**

:::image type="content" source="../../media/manage-phone-azure-portal-select-plans.png" alt-text="Screenshot mostrando a visualização de funcionalidades Select.":::

A partir daqui, clique no botão **Seguinte: Números** na parte inferior da página para personalizar o número de telefone(s) que pretende providenciar.

### <a name="customizing-phone-numbers"></a>Personalizar números de telefone

Na página **Números,** irá personalizar os números de telefone que pretende prever.

:::image type="content" source="../../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Screenshot mostrando a página de seleção de números.":::

> [!NOTE]
> Este quickstart mostra o fluxo de personalização do tipo número **gratuito.** A experiência pode ser ligeiramente diferente se tiver escolhido o tipo de Número **Geográfico,** mas o resultado final será o mesmo.

Escolha o **código De área** na lista dos códigos de Área disponíveis e introduza a quantidade que pretende disponibilizar e, em seguida, clique em **Procurar** para encontrar números que satisfaçam os requisitos selecionados. Os números de telefone que satisfaçam as suas necessidades serão mostrados juntamente com o seu custo mensal.

:::image type="content" source="../../media/manage-phone-azure-portal-found-numbers.png" alt-text="Screenshot mostrando a página de seleção de números com números reservados.":::

> [!NOTE]
> A disponibilidade depende do tipo, localização e funcionalidades que selecionou.
> Os números são reservados por um curto período de tempo antes de a transação expirar. Se a transação expirar, terá de reescorê-lo.

Para ver o resumo da compra e fazer o seu pedido, clique no botão **Seguinte: Resumo** na parte inferior da página.

### <a name="purchase-phone-numbers"></a>Comprar Números de Telefone

A página de resumo irá rever o tipo de número, funcionalidades, números de telefone e custo mensal total para a disponibilização dos números de telefone.

> [!NOTE]
> Os preços apresentados são os **encargos mensais recorrentes** que cobrem o custo de locação do número de telefone selecionado para si. Não estão incluídos nesta visão os **custos pay-as-you-go** que são incorridos quando faz ou recebe chamadas. As listas de preços estão [disponíveis aqui.](../../../concepts/pricing.md) Estes custos dependem do tipo de número e dos destinos chamados. Por exemplo, o preço por minuto para uma chamada de um número regional de Seattle para um número regional em Nova Iorque e uma chamada do mesmo número para um número de telemóvel do Reino Unido pode ser diferente.

Por fim, clique **em Colocar** o pedido na parte inferior da página para confirmar.

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Screenshot mostrando a página Resumo com o tipo número, funcionalidades, números de telefone e custo mensal total mostrado.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Encontre os seus números de telefone no portal Azure

Navegue para o seu Recurso de Comunicação Azure no [portal Azure:](https://portal.azure.com)

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Screenshot mostrando a página principal de um recurso de serviços de comunicação.":::

Selecione a lâmina números de telefone no menu para gerir os seus números de telefone.

:::image type="content" source="../../media/manage-phone-azure-portal-phones.png" alt-text="Screenshot mostrando a página de número de telefone de um recurso de comunicação.":::

> [!NOTE]
> Pode levar alguns minutos para que os números apresentados sejam mostrados nesta página.


### <a name="update-phone-number-capabilities"></a>Atualizar capacidades de número de telefone

Na página **Números,** pode selecionar um número de telefone para o configurar.

:::image type="content" source="../../media/manage-phone-azure-portal-capability-update.png" alt-text="Screenshot mostrando a página de funcionalidades da atualização.":::

Selecione as funcionalidades das opções disponíveis e, em seguida, clique em **Confirmar** para aplicar a sua seleção.

### <a name="release-phone-number"></a>Número de telefone de lançamento

Na página **Números,** pode divulgar números de telefone.

:::image type="content" source="../../media/manage-phone-azure-portal-release-number.png" alt-text="Screenshot mostrando a página de números de telefone de lançamento.":::

Selecione o número de telefone que pretende soltar e, em seguida, clique no botão **'Desbloquear'.**