---
title: Modelo de faturação de & de preços
description: Visão geral sobre como o modelo de preços e faturação funciona para Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: a5511d7cd4b5bb0f3fe901a735535f8db9036ee7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87078161"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modelo de preços para apps Azure Logic

[As Azure Logic Apps](../logic-apps/logic-apps-overview.md) ajudam-no a criar e executar fluxos de trabalho de integração automatizados que podem escalar na nuvem. Este artigo descreve como a faturação e os preços funcionam para a Azure Logic Apps. Para taxas de preços, consulte o Preço das [Aplicações Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps)

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modelo de preços de consumo

Para novas aplicações lógicas que funcionam no público, serviço "global", multi-inquilino Azure Logic Apps, você paga apenas pelo que você usa. Estas aplicações lógicas usam um plano baseado no consumo e um modelo de preços. Na sua aplicação lógica, cada passo é uma ação, e a Azure Logic Apps medi todas as ações que funcionam na sua aplicação lógica.

Por exemplo, as ações incluem:

* [Gatilhos,](#triggers)que são ações especiais. Todas as aplicações lógicas requerem um gatilho como primeiro passo.

* ["Built-in" ou ações nativas,](../connectors/apis-list.md#built-in) tais como HTTP, chamadas para Funções Azure e Gestão de API, e assim por diante

* Chamadas para [conectores geridos](../connectors/apis-list.md#managed-connectors) como Outlook 365, Dropbox, e assim por diante

* [Controle ações de fluxo de](../connectors/apis-list.md#control-workflow) trabalho tais como loops, declarações condicionais, e assim por diante

[Os conectores standard](../connectors/apis-list.md#managed-connectors) são carregados ao preço do [conector Standard](https://azure.microsoft.com/pricing/details/logic-apps). Os [conectores empresariais](../connectors/apis-list.md#managed-connectors) geralmente disponíveis são cobrados ao preço do [conector Enterprise,](https://azure.microsoft.com/pricing/details/logic-apps)enquanto os conectores enterprise de pré-visualização pública são cobrados ao preço do [conector Standard](https://azure.microsoft.com/pricing/details/logic-apps).

Saiba mais sobre como a faturação funciona nos níveis [de gatilhos](#triggers) e [ações.](#actions) Ou, para obter informações sobre limites, consulte [Limites e configuração para Aplicações LógicaS Azure](logic-apps-limits-and-config.md).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modelo de preços fixos

Um ambiente de serviço de [ *integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) fornece uma forma isolada de criar e executar aplicações lógicas que possam aceder a recursos numa rede virtual Azure. As aplicações lógicas que funcionam num ISE não incorrem em custos de retenção de dados. Quando cria um ISE, e só durante a criação, pode escolher um [nível ISE ou "SKU",](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)que têm [diferentes taxas de preços:](https://azure.microsoft.com/pricing/details/logic-apps)

* **Premium** ISE: A unidade base deste SKU tem capacidade fixa, mas se precisar de mais produção, pode [adicionar mais unidades de escala](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) durante a criação do ISE ou depois. Para limites ISE, consulte [limites e configuração para Aplicações Lógicas Azure](logic-apps-limits-and-config.md#integration-service-environment-ise).

* **Desenvolvedor** ISE: Este SKU não tem capacidade para aumentar, nenhum acordo de nível de serviço (SLA) e nenhum limite publicado. Utilize este SKU apenas para experimentar, desenvolvimento e testes, não testes de produção ou desempenho.

Para aplicações lógicas que cria e funciona num ISE, paga-se um [preço fixo](https://azure.microsoft.com/pricing/details/logic-apps) (versus pagamento por utilização) por estas capacidades:

* [Gatilhos](../connectors/apis-list.md#built-in) e ações incorporados

  Dentro de um ISE, os gatilhos e ações incorporados exibem a etiqueta **Core** e funcionam na mesma ISE que as suas aplicações lógicas.

* [Conectores](../connectors/apis-list.md#managed-connectors) standard e conectores [Enterprise,](../connectors/apis-list.md#enterprise-connectors) que lhe permitem ter quantas ligações da Enterprise quiser

   Os conectores Standard e Enterprise que exibem a etiqueta **ISE** funcionam na mesma ISE que as suas aplicações lógicas. Os conectores que não exibem a etiqueta ISE funcionam em público, serviço "global", multi-inquilino Logic Apps. O preço fixo também se aplica aos conectores que funcionam no serviço multi-inquilino quando os utiliza com aplicações lógicas que funcionam num ISE.

* [Utilização](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) da conta de integração sem custos adicionais, com base no seu [ISE SKU:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

  * **Premium** ISE SKU: Uma única conta de integração [de nível Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * **Desenvolvedor** ISE SKU: Uma única conta de integração [de nível livre](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Cada SKU ISE está limitado a 5 contas de integração total. Por um custo adicional, pode ter mais contas de integração, com base no seu ISE SKU:

  * **Premium** ISE SKU: Até mais quatro contas Standard. Sem contas gratuitas ou básicas.

  * **Desenvolvedor** ISE SKU: Ou até 4 contas Standard ou até 5 contas Standard totais. Sem contas básicas.

  Para obter mais informações sobre os limites da conta de integração, consulte [Limites e configuração para Apps Azure Logic](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Pode saber mais sobre [os níveis de conta de integração e o seu modelo de preços](#integration-accounts) mais tarde neste tópico.

<a name="connectors"></a>

## <a name="connectors"></a>Conectores

Os conectores Azure Logic Apps ajudam a sua aplicação lógica a aceder a apps, serviços e sistemas na nuvem ou nas instalações, fornecendo [gatilhos,](#triggers) [ações](#actions)ou ambos. Os conectores são classificados como Standard ou Enterprise. Para uma visão geral sobre estes conectores, consulte [Conectors para Aplicações Lógicas Azure](../connectors/apis-list.md). Se não houver conectores pré-construídos para as APIs REST que pretende utilizar nas suas aplicações lógicas, pode criar [conectores personalizados](/connectors/custom-connectors)– que são apenas invólucros em torno dessas APIs REST. Os conectores personalizados são faturados como conectores Standard. As secções seguintes fornecem mais informações sobre como a faturação para gatilhos e ações funcionam.

<a name="triggers"></a>

## <a name="triggers"></a>Acionadores

Um gatilho é sempre o primeiro passo num fluxo de trabalho de aplicações lógicas e é uma ação especial que cria e executa uma instância de aplicação lógica quando critérios específicos são cumpridos ou um evento específico acontece. Os gatilhos atuam de formas diferentes, que afetam a forma como a aplicação lógica é medido. Aqui estão os vários tipos de gatilhos que existem em Azure Logic Apps:

* **Trigger de recorrência**: Pode utilizar este gatilho genérico, que não é específico de qualquer serviço ou sistema, para iniciar qualquer fluxo de trabalho de aplicações lógicas e criar uma instância lógica de aplicação que funciona com base no intervalo de recorrência que configura no gatilho. Por exemplo, pode configurar um gatilho de Recorrência que é executado a cada três dias ou num horário mais complexo.

* **Gatilho de sondagens**: Pode utilizar este gatilho de recorrência mais especializado, que normalmente está associado ao conector gerido para um serviço ou sistema específico, para verificar eventos ou mensagens que satisfaçam os critérios de criação e execução de instâncias lógicas com base no intervalo de recorrência que configura no gatilho. Mesmo quando nenhuma instância de aplicação lógica é criada, por exemplo, quando os gatilhos são ignorados, o serviço Desío de Aplicações Lógicas medi cada pedido de sondagem como uma execução. Para especificar o intervalo de votação, censuuse o gatilho através do Logic App Designer.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook trigger**: Em vez de usar um gatilho de sondagens, pode usar um gatilho webhook para esperar que o cliente envie um pedido para a sua aplicação lógica num URL específico do ponto final. Cada pedido enviado para o ponto final do webhook conta como uma execução de ação. Por exemplo, o gatilho Request e HTTP Webhook são ambos gatilhos genéricos webhook. Alguns conectores para serviços ou sistemas também têm gatilhos webhook.

<a name="actions"></a>

## <a name="actions"></a>Ações

A Azure Logic Apps medi ações "incorporadas", como HTTP, como ações nativas. Por exemplo, as ações incorporadas incluem chamadas HTTP, chamadas de Funções Azure ou Gestão de API, e passos de fluxo de controlo tais como condições, loops e declarações de switch. Cada ação tem o seu próprio tipo de ação. Por exemplo, as ações que chamam [conectores](/connectors) têm o tipo "ApiConnection". Estes conectores são classificados como conectores Standard ou Enterprise, que são medidos com base nos respetivos [preços.](https://azure.microsoft.com/pricing/details/logic-apps) Os conectores da empresa em *Pré-visualização* são carregados como conectores Standard.

A Azure Logic Apps medi todas as ações bem sucedidas e mal sucedidas como execuções. No entanto, as Aplicações Lógicas não são paritórias destas ações:

* Ações que são ignoradas devido a condições insatisfeitas
* Ações que não funcionam porque a app lógica parou antes de terminar

Para ações que correm dentro de loops, a Azure Logic Apps conta cada ação para cada ciclo no loop. Por exemplo, suponha que tem um loop "para cada" que processa uma lista. A Logic Apps medi uma ação nesse ciclo multiplicando o número de itens de lista com o número de ações no loop, e adiciona a ação que inicia o loop. Assim, o cálculo para uma lista de 10 artigos é (10 * 1) + 1, o que resulta em 11 execuções de ação.

## <a name="disabled-logic-apps"></a>Aplicativos de lógica desativados

As aplicações lógicas desativadas não são cobradas porque não podem criar novos casos enquanto estão desativadas. Depois de desativar uma aplicação lógica, quaisquer casos em execução podem demorar algum tempo até que parem completamente.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Contas de integração

Um [modelo de preços fixos](https://azure.microsoft.com/pricing/details/logic-apps) aplica-se às contas de integração onde pode explorar, desenvolver e testar as [funcionalidades](logic-apps-enterprise-integration-create-integration-account.md) de processamento [B2B e EDI](logic-apps-enterprise-integration-b2b.md) e [XML](logic-apps-enterprise-integration-xml.md) em Azure Logic Apps sem custos adicionais. Cada subscrição do Azure pode ter até um [limite específico de contas de integração.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) Cada conta de integração pode armazenar até um limite específico [de artefactos](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), que incluem parceiros comerciais, acordos, mapas, esquemas, conjuntos, certificados, configurações de lote, e assim por diante.

A Azure Logic Apps oferece contas de integração gratuitas, básicas e standard. Os níveis Básico e Standard são suportados pelo acordo de nível de serviço das Apps Lógicas (SLA), enquanto o nível Free não é suportado por um SLA e tem limites na disponibilidade, produção e utilização da região. Com exceção das contas de integração de nível gratuito, pode ter mais do que uma conta de integração em cada região do Azure. Para taxas de preços, consulte [os preços das Aplicações Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/)

Se tiver um ambiente de serviço de [ *integração* (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) [Premium ou Developer,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)o seu ISE pode ter 5 contas de integração total. Para saber como funciona o modelo de preços fixos para um ISE, consulte a secção de [modelos de preços fixos](#fixed-pricing) anteriores neste tópico. Para taxas de preços, consulte [os preços das Aplicações Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps)

Para escolher entre uma conta de integração gratuita, básica ou standard, reveja estas descrições de casos de uso:

* **Grátis**: Para quando quiser experimentar cenários exploratórios, não cenários de produção. Este nível só está disponível para regiões públicas em Azure, por exemplo, eua ocidental ou sudeste asiático, mas não para [o Azure China 21Vianet](/azure/china/overview-operations) ou [para o Governo Azure.](../azure-government/documentation-government-welcome.md)

* **Básico**: Para quando só pretender o tratamento de mensagens ou agir como um parceiro de pequena empresa que tenha uma relação de parceiro comercial com uma entidade de negócio maior

* **Norma:** Para quando tiver relações B2B mais complexas e um maior número de entidades que deve gerir

<a name="data-retention"></a>

## <a name="data-retention"></a>Retenção de dados

Com exceção das aplicações lógicas que funcionam num ambiente de serviço de integração (ISE), todas as entradas e saídas que são armazenadas no histórico de execução da sua aplicação lógica são faturadas com base no período de [retenção](logic-apps-limits-and-config.md#run-duration-retention-limits)de uma aplicação lógica. As aplicações lógicas que funcionam num ISE não incorrem em custos de retenção de dados. Para taxas de preços, consulte [os preços das Aplicações Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps)

Para ajudá-lo a monitorizar o consumo de armazenamento da sua aplicação lógica, pode:

* Veja o número de unidades de armazenamento em GB que a sua aplicação lógica utiliza mensalmente.
* Veja os tamanhos para as entradas e saídas de uma ação específica no histórico de execução da sua aplicação lógica.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Ver consumo de armazenamento de aplicativos lógico

1. No portal Azure, encontre e abra a sua aplicação lógica.

1. A partir do menu da sua aplicação lógica, em **Monitorização,** selecione **Metrics**.

1. No painel direito, no **âmbito**do Chart Title , da lista **métrica,** selecione **Billing Usage for Storage Consumption Executions**.

   Esta métrica dá-lhe o número de unidades de consumo de armazenamento em GB por mês que estão sendo cobradas.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Ver tamanhos de entrada e saída de ação

1. No portal Azure, encontre e abra a sua aplicação lógica.

1. No menu da sua aplicação lógica, selecione **Overview**.

1. No painel direito, no **histórico de Runs,** selecione a execução que tem as entradas e saídas que pretende verificar.

1. No **âmbito da aplicação Logic,** escolha **Detalhes de Execução.**

1. Na **aplicação Logic execute detalhes** de detalhes, na tabela de ações, que lista o estado e a duração de cada ação, selecione a ação que pretende ver.

1. No painel de ação da **aplicação Logic,** encontre os tamanhos para as entradas e saídas dessa ação aparecem respectivamente no **link inputs** e **na ligação Outputs**.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre a Azure Logic Apps](logic-apps-overview.md)
* [Criar a sua primeira aplicação lógica](quickstart-create-first-logic-app-workflow.md)
