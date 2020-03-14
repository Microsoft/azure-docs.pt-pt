---
title: Modelo de preços e faturação
description: Visão geral sobre como o modelo de preços e faturação funciona para apps lógicas azure
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 795acd67a8d4a9f8b8b7d78799a92134f249cf8d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270462"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modelo de preços para aplicações lógicas azure

[As Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) ajudam-no a criar e executar fluxos de trabalho de integração automatizada que podem escalar na nuvem. Este artigo descreve como a faturação e os preços funcionam para as Aplicações Lógicas Azure. Para preços, consulte Preços [de Aplicações Lógicas](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modelo de preços de consumo

Para novas aplicações lógicas que funcionam no público ou no serviço "global" Azure Logic Apps, você paga apenas pelo que usa. Estas aplicações lógicas utilizam um plano baseado no consumo e modelo de preços. Na sua aplicação lógica, cada passo é uma ação, e as Aplicações Lógicas Azure medem todas as ações que correm na sua aplicação lógica.

Por exemplo, as ações incluem:

* Gatilhos, que são ações especiais. Todas as aplicações lógicas requerem um gatilho como primeiro passo.
* [Ações "incorporadas" ou nativas,](../connectors/apis-list.md#built-in) tais como HTTP, chamadas para funções Azure e Gestão de API, e assim por diante
* Chamadas para [conectores geridos](../connectors/apis-list.md#managed-connectors) como Outlook 365, Dropbox, e assim por diante
* Passos de fluxo de controlo, tais como loops, declarações condicionais, e assim por diante

[Os conectores standard](../connectors/apis-list.md#managed-connectors) são cobrados ao preço do [conector Standard](https://azure.microsoft.com/pricing/details/logic-apps). Os [conectores Enterprise](../connectors/apis-list.md#managed-connectors) geralmente disponíveis são cobrados ao preço do [conector Enterprise,](https://azure.microsoft.com/pricing/details/logic-apps)enquanto os conectores da Enterprise de pré-visualização pública são cobrados ao preço do [conector Standard](https://azure.microsoft.com/pricing/details/logic-apps).

Saiba mais sobre como a faturação funciona para [gatilhos](#triggers) e [ações.](#actions)

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modelo de preços fixos

Um ambiente de serviço de [ *integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) fornece uma forma isolada de criar e executar aplicações lógicas que podem aceder a recursos numa rede virtual Azure. Para novas aplicações lógicas que funcionam dentro de um ISE, você paga um [preço mensal fixo](https://azure.microsoft.com/pricing/details/logic-apps) por estas capacidades:

* Gatilhos e ações [incorporados](../connectors/apis-list.md#built-in)

  Dentro de um ISE, gatilhos e ações incorporados exibem a etiqueta **Core** e funcionam no mesmo ISE que as suas aplicações lógicas.

* Conectores [padrão](../connectors/apis-list.md#managed-connectors) e conectores [da Enterprise](../connectors/apis-list.md#enterprise-connectors) (quantas ligações da Enterprise quiser)

   Os conectores Standard e Enterprise que exibem a etiqueta **ISE** funcionam no mesmo ISE que as suas aplicações lógicas. Conectores que não apresentam a etiqueta ISE executada no serviço global de Aplicações Lógicas. Os preços mensais fixos também se aplicam aos conectores que funcionam no serviço global quando os utiliza com aplicações lógicas que funcionam num ISE.

* [Utilização da conta](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) de integração sem custos adicionais, com base no seu [ISE SKU:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

  * **Premium SKU**: Uma única conta de integração [de nível Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * **Developer SKU**: Uma única conta de integração [de nível livre](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Cada ISE SKU está limitado a 5 contas de integração total. Por um custo adicional, pode ter mais contas de integração, com base no seu ISE SKU:

  * **Premium SKU**: Até mais quatro contas Standard. Sem contas grátis ou básicas.

  * **Developer SKU**: Ou até mais 4 contas Standard, ou até 5 contas Standard totais. Sem contas básicas.

  Para obter mais informações sobre os limites da conta de integração, consulte [os limites e configuração das Aplicações Lógicas.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) Você pode aprender mais sobre os níveis de conta de [integração e seu modelo](#integration-accounts) de preços mais tarde neste tópico.

Se escolher o Premium ISE SKU, a unidade base tem capacidade fixa. Se precisar de mais entrada, pode [adicionar mais unidades](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)de escala, seja durante a criação ou depois. O Developer ISE SKU não tem a capacidade de adicionar unidades de maior escala. Aplicações lógicas que funcionam num ISE não incorrem em custos de retenção de dados.

Para preços, consulte [preços de Apps Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps)

<a name="connectors"></a>

## <a name="connectors"></a>Conectores

Os conectores de Aplicações Azure Logic ajudam as aplicações lógicas de acesso a apps, serviços e sistemas na nuvem ou nas instalações, fornecendo [gatilhos](#triggers), [ações,](#actions)ou ambos. Os conectores são classificados como Standard ou Enterprise. Para uma visão geral sobre estes conectores, consulte [Conectores para Aplicações Lógicas Azure](../connectors/apis-list.md). Se não houver conectores pré-construídos disponíveis para as APIs rest que pretende utilizar nas suas aplicações lógicas, pode criar [conectores personalizados](https://docs.microsoft.com/connectors/custom-connectors), que são apenas invólucros em torno dessas APIs REST. Os conectores personalizados são faturados como conectores Standard. As seguintes secções fornecem mais informações sobre como a faturação para gatilhos e ações funcionam.

<a name="triggers"></a>

## <a name="triggers"></a>Acionadores

Os gatilhos são ações especiais que criam uma instância de aplicação lógica quando um evento específico acontece. Os gatilhos agem de diferentes formas, o que afeta a forma como a aplicação lógica é medido. Aqui estão os vários tipos de gatilhos que existem em Aplicações Lógicas Azure:

* **Gatilho de sondagens**: Este gatilho verifica continuamente um ponto final para mensagens que satisfazem os critérios para criar uma instância de aplicação lógica e iniciar o fluxo de trabalho. Mesmo quando nenhuma instância de aplicação lógica é criada, as Aplicações Lógicas míseros cada pedido de votação como uma execução. Para especificar o intervalo de votação, instale o gatilho através do Logic App Designer.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Gatilho Webhook**: Este gatilho aguarda que um cliente envie um pedido para um ponto final específico. Cada pedido enviado para o ponto final do webhook conta como uma execução de ação. Por exemplo, o gatilho Request e HTTP Webhook são ambos gatilhos de webhook.

* **Gatilho de recorrência**: Este gatilho cria uma instância lógica de aplicação com base no intervalo de recorrência que configura no gatilho. Por exemplo, pode configurar um gatilho de Recorrência que funciona de três em três dias ou com um horário mais complexo.

<a name="actions"></a>

## <a name="actions"></a>Ações

As Aplicações Lógicas Azure míseras ações "incorporadas", como http, como ações nativas. Por exemplo, as ações incorporadas incluem chamadas HTTP, chamadas de Funções Azure ou Gestão API, e passos de fluxo de controlo, tais como condições, loops e declarações de switch. Cada ação tem o seu próprio tipo de ação. Por exemplo, as ações que ligam [os conectores](https://docs.microsoft.com/connectors) têm o tipo "ApiConnection". Estes conectores são classificados como conectores Standard ou Enterprise, que são medidos com base nos respetivos [preços.](https://azure.microsoft.com/pricing/details/logic-apps) Os conectores da enterprise em *Preview* são carregados como conectores Standard.

As Aplicações Lógicas Azure têm todas as ações bem sucedidas e mal sucedidas como execuções. No entanto, as Aplicações Lógicas não medem estas ações:

* Ações que são ignoradas devido a condições não cumpridas
* Ações que não funcionam porque a aplicação lógica parou antes de terminar

Para ações que funcionam dentro de loops, as Aplicações Lógicas Azure contam cada ação para cada ciclo em loop. Por exemplo, suponha que tem um loop "para cada" que processa uma lista. As Aplicações Lógicas mímicas uma ação nesse ciclo multiplicando o número de itens de lista com o número de ações no loop, e adiciona a ação que inicia o loop. Assim, o cálculo para uma lista de 10 item é (10 * 1) + 1, o que resulta em 11 execuções de ação.

## <a name="disabled-logic-apps"></a>Aplicativos lógicos para deficientes

As aplicações lógicas desativadas não são carregadas porque não podem criar novos casos enquanto estão desativadas. Depois de desativar uma aplicação lógica, quaisquer casos atualmente em execução podem demorar algum tempo até que parem completamente.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Contas de integração

Um modelo de [preços fixos](https://azure.microsoft.com/pricing/details/logic-apps) aplica-se a contas de [integração](logic-apps-enterprise-integration-create-integration-account.md) onde pode explorar, desenvolver e testar as funcionalidades de processamento [B2B e EDI](logic-apps-enterprise-integration-b2b.md) e [XML](logic-apps-enterprise-integration-xml.md) em Aplicações Lógicas Azure sem custos adicionais. Cada subscrição do Azure pode ter até um [limite específico de contas de integração.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) Cada conta de integração pode armazenar até ao limite específico [de artefactos](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), que incluem parceiros comerciais, acordos, mapas, schemas, conjuntos, certificados, configurações de lotes, e assim por diante.

As Aplicações Lógicas Azure oferecem contas de integração gratuitas, básicas e standard. Os níveis Basic e Standard são suportados pelo acordo de nível de serviço de Aplicações Lógicas (SLA), enquanto o nível Livre não é suportado por um SLA e tem limites de entrada e utilização. Com exceção das contas de integração a nível livre, pode ter mais do que uma conta de integração em cada região do Azure. Para preços, consulte [preços de Apps Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/)

Se tiver um ambiente de serviço de [ *integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), [premium ou desenvolvedor,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)o ise pode ter 5 contas de integração total. Para saber como funciona o modelo de preços fixos para um ISE, consulte a secção de modelos de [preços fixos](#fixed-pricing) anteriores neste tópico. Para preços, consulte [preços de Apps Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps)

Para escolher entre uma conta de integração gratuita, básica ou standard, reveja estas descrições do caso de utilização:

* **Grátis**: Para quando se quer experimentar cenários exploratórios, não cenários de produção

* **Básico**: Para quando se quer apenas o manuseamento de mensagens ou a agir como um pequeno parceiro de negócios que tenha uma relação de parceiro comercial com uma entidade empresarial maior

* **Padrão**: Para quando tiver relações B2B mais complexas e um maior número de entidades que deve gerir

<a name="data-retention"></a>

## <a name="data-retention"></a>Retenção de dados

Exceto para aplicações lógicas que funcionam em um ambiente de serviço de integração (ISE), todas as inputs e saídas que são armazenadas no histórico de execução da sua aplicação lógica são faturadas com base no período de [retenção](logic-apps-limits-and-config.md#run-duration-retention-limits)de uma aplicação lógica. Aplicações lógicas que funcionam num ISE não incorrem em custos de retenção de dados. Para preços, consulte [preços de Apps Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps)

Para ajudá-lo a monitorizar o consumo de armazenamento da sua aplicação lógica, pode:

* Veja o número de unidades de armazenamento em GB que a sua aplicação lógica utiliza mensalmente.
* Veja os tamanhos para as inputs e saídas de uma ação específica no histórico de execução da sua aplicação lógica.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Ver armazenamento de aplicativológico lógica

1. No portal Azure, encontre e abra a sua aplicação lógica.

1. A partir do menu da sua aplicação lógica, sob **monitorização,** selecione **Métricas**.

1. No painel da direita, em **título**gráfico, da lista **métrica,** selecione **a utilização da faturação para execuções**de consumo de armazenamento .

   Esta métrica dá-lhe o número de unidades de consumo de armazenamento em GB por mês que estão a ser faturadas.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Ver tamanhos de entrada e saída de ação

1. No portal Azure, encontre e abra a sua aplicação lógica.

1. No menu da sua aplicação lógica, selecione **Visão Geral**.

1. No painel da direita, sob **o histórico de Runs,** selecione a execução que tem as inputs e saídas que pretende verificar.

1. Sob **a execução da aplicação Logic,** escolha **Detalhes de Execução**.

1. Na **aplicação Logic executa detalhes** painel, na tabela de ações, que lista o estado e duração de cada ação, selecione a ação que pretende ver.

1. No painel de ação da **aplicação Logic,** encontre os tamanhos para as inputs e saídas dessa ação aparecerem respectivamente sob a ligação de **inputs** e **saídas**.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre as aplicações da Lógica Azure](logic-apps-overview.md)
* [Criar a sua primeira aplicação lógica](quickstart-create-first-logic-app-workflow.md)
