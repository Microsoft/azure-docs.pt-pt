---
title: Modelos de preços & de faturação
description: Visão geral sobre como os modelos de preços e faturação funcionam em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 2b37308bcbcd489876c21dce56878de7e0daf545
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699033"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Modelos de preços e faturação para apps Azure Logic

[As Azure Logic Apps](../logic-apps/logic-apps-overview.md) ajudam-no a criar e executar fluxos de trabalho de integração automatizados que podem escalar na nuvem. Este artigo descreve como os modelos de faturação e preços funcionam para o serviço De Aplicações Lógicas e recursos relacionados. Para taxas de preços específicas, consulte [o Preço das Aplicações Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps) Para saber como pode planear, gerir e monitorizar custos, consulte [o Plano e gere os custos das Apps Azure Logic.](plan-manage-costs.md)

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Preços de vários inquilinos

Um modelo de preços de consumo pay-for-use aplica-se a aplicações lógicas que funcionam no público, serviço "global", multi-inquilino Logic Apps. Todas as corridas bem sucedidas e mal sucedidas são medidos e faturados.

Por exemplo, um pedido que um gatilho de sondagens faz ainda é medido como uma execução, mesmo que esse gatilho seja ignorado, e nenhuma instância lógica de fluxo de trabalho é criada.

| Itens | Descrição |
|-------|-------------|
| [Gatilhos](../connectors/apis-list.md#built-in) e ações incorporados | Executar de forma nativa no serviço De aplicações lógicas e é medido usando o preço [ **de Ações**](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>Por exemplo, o gatilho HTTP e o gatilho pedido são gatilhos incorporados, enquanto a ação HTTP action and Response são ações incorporadas. As operações de dados, as operações de lote, as operações variáveis e [as ações](../connectors/apis-list.md#control-workflow)de controlo do fluxo de trabalho , tais como loops, condições, interruptor, ramos paralelos, e assim por diante, são também ações integradas. |
| Triggers e ações [de conector padrão](../connectors/apis-list.md#managed-connectors) <p><p>[Gatilhos](../connectors/apis-list.md#custom) e ações de conector personalizado | Medido com o preço do [conector Standard](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [Gatilhos](../connectors/apis-list.md#managed-connectors) e ações do conector da empresa | Medido usando o preço do [conector Enterprise](https://azure.microsoft.com/pricing/details/logic-apps/). No entanto, durante a pré-visualização pública, os conectores da Enterprise são medidos utilizando o preço do [conector *Standard*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Ações dentro [de loops](logic-apps-control-flow-loops.md) | Cada ação que corre em loop é medido para cada ciclo de ciclo que corre. <p><p>Por exemplo, suponha que tem um loop "para cada" que inclui ações que processam uma lista. O serviço De Aplicações Lógicas medi cada ação que funciona nesse ciclo multiplicando o número de itens de lista com o número de ações no loop, e adiciona a ação que inicia o loop. Assim, o cálculo para uma lista de 10 artigos é (10 * 1) + 1, o que resulta em 11 execuções de ação. |
| Tentativas de repetição | Para lidar com as exceções e erros mais básicos, pode configurar uma [política de repetição](logic-apps-exception-handling.md#retry-policies) sobre gatilhos e ações sempre que suportados. Estas retrígios juntamente com o pedido original são cobradas a taxas baseadas no tipo incorporado, Standard ou Enterprise. Por exemplo, uma ação que executa com 2 retréis é cobrada por 3 execuções de ação. |
| [Conservação de dados e consumo de armazenamento](#data-retention) | Medido usando o preço de retenção de dados, que pode encontrar na página de preços de [Aplicações Lógicas,](https://azure.microsoft.com/pricing/details/logic-apps/)na tabela de detalhes de **Preços.** |
|||

Para obter mais informações, consulte o seguinte:

* [Ver métricas para execuções e consumo de armazenamento](plan-manage-costs.md#monitor-billing-metrics)
* [Limites em Azure Logic Apps](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Não medido

* Gatilhos que são ignorados devido a condições insatisfeitas
* Ações que não funcionaram porque a app lógica parou antes de terminar
* [Aplicativos de lógica desativados](#disabled-apps)

### <a name="other-related-resources"></a>Outros recursos relacionados

As aplicações lógicas funcionam com outros recursos relacionados, tais como contas de integração, gateways de dados no local e ambientes de serviços de integração (ISEs). Para conhecer os preços desses recursos, reveja estas secções mais tarde neste tópico:

* [On-premises data gateway (Gateway de dados no local)](#data-gateway)
* [Modelo de preços da conta de integração](#integration-accounts)
* [Modelo de preços ISE](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Dicas para estimar os custos de consumo

Para ajudá-lo a estimar custos de consumo mais precisos, reveja estas dicas:

* Considere o possível número de mensagens ou eventos que possam chegar num determinado dia, em vez de basear os seus cálculos apenas no intervalo de votação.

* Quando um evento ou mensagem satisfaz os critérios do gatilho, muitos gatilhos tentam imediatamente ler todos e quaisquer outros eventos de espera ou mensagens que satisfaçam os critérios. Este comportamento significa que mesmo quando se seleciona um intervalo de votação mais longo, o gatilho dispara com base no número de eventos de espera ou mensagens que se qualificam para iniciar fluxos de trabalho. Os gatilhos que seguem este comportamento incluem o Azure Service Bus e o Azure Event Hub.

  Por exemplo, suponha que instale o gatilho que verifica um ponto final todos os dias. Quando o gatilho verifica o ponto final e encontra 15 eventos que satisfazem os critérios, o gatilho dispara e executa o fluxo de trabalho correspondente 15 vezes. O serviço De Aplicações Lógicas medi todas as ações que esses 15 fluxos de trabalho realizam, incluindo os pedidos de gatilho.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>Preços ise

Um modelo de preços fixos aplica-se a aplicações lógicas que funcionam num [ *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Um ISE é faturado usando o [preço do Ambiente do Serviço de Integração,](https://azure.microsoft.com/pricing/details/logic-apps)que depende do [nível ISE ou *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) que você cria. Este preço difere dos preços de vários inquilinos, uma vez que está a pagar pela capacidade reservada e recursos dedicados, quer os utilize ou não.

| ISE SKU | Descrição |
|---------|-------------|
| **Premium** | A unidade base tem [capacidade fixa](logic-apps-limits-and-config.md#integration-service-environment-ise) e é [faturada a uma taxa horária para o SKU Premium.](https://azure.microsoft.com/pricing/details/logic-apps) Se precisar de mais produção, pode [adicionar mais unidades de escala](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) quando criar o ise ou depois. Cada unidade de escala é faturada a uma [taxa horária que é cerca de metade da taxa de unidade base.](https://azure.microsoft.com/pricing/details/logic-apps) <p><p>Para obter informações sobre capacidade e limites, consulte [os limites ise nas Aplicações Lógicas Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Programador** | A unidade base tem [capacidade fixa](logic-apps-limits-and-config.md#integration-service-environment-ise) e é [faturada a uma taxa horária para o Desenvolvedor SKU](https://azure.microsoft.com/pricing/details/logic-apps). No entanto, este SKU não tem nenhum acordo de nível de serviço (SLA), capacidade de aumento de escala, ou redundância durante a reciclagem, o que significa que você pode experimentar atrasos ou tempo de inatividade. As atualizações de backend podem interromper intermitentemente o serviço. <p><p>**Importante**: Certifique-se de que utiliza este SKU apenas para exploração, experiências, desenvolvimento e testes - não para testes de produção ou desempenho. <p><p>Para obter informações sobre capacidade e limites, consulte [os limites ise nas Aplicações Lógicas Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

### <a name="included-at-no-extra-cost"></a>Incluído sem custo extra

| Itens | Descrição |
|-------|-------------|
| [Gatilhos](../connectors/apis-list.md#built-in) e ações incorporados | Exiba a etiqueta **Core** e corra no mesmo ISE que as suas aplicações lógicas. |
| [Conetores padrão](../connectors/apis-list.md#managed-connectors) <p><p>[Conectores empresariais](../connectors/apis-list.md#enterprise-connectors) | - Os conectores geridos que exibem a etiqueta **ISE** são especialmente concebidos para funcionar sem o gateway de dados no local e executados no mesmo ISE que as suas aplicações lógicas. Os preços do ISE incluem as ligações da Enterprise que quiser. <p><p>- Os conectores que não exibem a etiqueta ISE funcionam no serviço Multi-Tenant Logic Apps. No entanto, os preços do ISE incluem estas execuções para aplicações lógicas que funcionam numa ISE. |
| Ações dentro [de loops](logic-apps-control-flow-loops.md) | Os preços do ISE incluem cada ação que funciona em loop para cada ciclo de loop que funciona. <p><p>Por exemplo, suponha que tem um loop "para cada" que inclui ações que processam uma lista. Para obter o número total de execuções de ação, multiplique o número de itens de lista com o número de ações no loop, e adicione a ação que inicia o loop. Assim, o cálculo para uma lista de 10 artigos é (10 * 1) + 1, o que resulta em 11 execuções de ação. |
| Tentativas de repetição | Para lidar com as exceções e erros mais básicos, pode configurar uma [política de repetição](logic-apps-exception-handling.md#retry-policies) sobre gatilhos e ações sempre que suportados. Os preços do ISE incluem retrótares juntamente com o pedido original. |
| [Conservação de dados e consumo de armazenamento](#data-retention) | As aplicações lógicas num ISE não incorrem em custos de retenção e armazenamento. |
| [Contas de integração](#integration-accounts) | Inclui a utilização de um único nível de conta de integração, com base no ISE SKU, sem custos adicionais. |
|||

Para obter informações sobre limites, consulte [os limites ise nas Aplicações Lógicas Azure](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Contas de integração

Uma [conta de integração](../logic-apps/logic-apps-pricing.md#integration-accounts) é um recurso separado que cria e liga a aplicações lógicas para que possa explorar, construir e testar soluções de integração B2B que utilizam capacidades de processamento [de EDI](logic-apps-enterprise-integration-b2b.md) e [XML.](logic-apps-enterprise-integration-xml.md)

A Azure Logic Apps oferece estes níveis ou níveis de conta de integração que [variam no](https://azure.microsoft.com/pricing/details/logic-apps/) modelo de preços e [faturação,](logic-apps-pricing.md#integration-accounts)com base no facto de as suas aplicações lógicas serem baseadas em consumo ou baseadas em ISE:

| Escalão de serviço | Descrição |
|------|-------------|
| **Básica** | Para cenários em que só quer lidar com mensagens ou agir como um parceiro de pequena empresa que tenha uma relação de parceiro comercial com uma entidade de negócio maior. <p><p>Apoiado pelo Logic Apps SLA. |
| **Standard** | Para cenários onde você tem relações B2B mais complexas e um número aumentado de entidades que você deve gerir. <p><p>Apoiado pelo Logic Apps SLA. |
| **Gratuito** | Para cenários exploratórios, não cenários de produção. Este nível tem limites na disponibilidade, produção e utilização da região. Por exemplo, o free tier está disponível apenas para regiões públicas em Azure, por exemplo, Eua Ocidental ou Sudeste Asiático, mas não para [Azure China 21Vianet](/azure/china/overview-operations) ou [Governo Azure.](../azure-government/documentation-government-welcome.md) <p><p>**Nota:** Não suportado pelo Logic Apps SLA. |
|||

Para obter informações sobre os limites da conta de integração, consulte [limites e configuração para Aplicações Lógicas Azure,](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)tais como:

* [Limites nas contas de integração por subscrição do Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Limites em vários artefactos por conta de integração.](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) Os artefactos incluem parceiros comerciais, acordos, mapas, esquemas, conjuntos, certificados, configurações de lotes, e assim por diante.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Contas de integração para aplicações lógicas baseadas no consumo

As contas de integração são faturadas utilizando um preço fixo [de conta de integração](https://azure.microsoft.com/pricing/details/logic-apps/) que se baseia no nível de conta que utiliza.

### <a name="ise-based-logic-apps"></a>Aplicativos lógicos baseados no ISE

Sem custos adicionais, o seu ISE inclui uma única conta de integração, com base no seu ISE SKU. Por um custo extra, pode criar mais contas de integração para o seu ISE utilizar até ao [limite total do ISE.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) Saiba mais sobre o [modelo de preços ise](#fixed-pricing) mais cedo neste tópico.

| ISE SKU | Conta de integração incluída | Custo adicional |
|---------|------------------------------|-----------------|
| **Premium** | Uma única conta de integração [Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Até mais 19 contas Standard. Não são permitidas contas Gratuitas ou Básicas. |
| **Programador** | Uma única conta de integração [gratuita](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Até mais 19 contas Standard se já tiver uma conta Standard ou 20 contas Standard totais se não tiver uma conta Grátis. Não são permitidas contas básicas. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Conservação de dados e consumo de armazenamento

Todas as entradas e saídas do histórico de execução da sua aplicação lógica são armazenadas e medidos com base na duração da [aplicação e no período de retenção](logic-apps-limits-and-config.md#run-duration-retention-limits)de histórico.

* Para aplicações lógicas no serviço Multi-inquilinos Logic Apps, o consumo de armazenamento é faturado a um preço fixo, que você pode encontrar na página de preços de [Aplicações Lógicas](https://azure.microsoft.com/pricing/details/logic-apps), na tabela de detalhes de **preços.**

* Para aplicações lógicas em ISEs, o consumo de armazenamento não incorre em custos de retenção de dados.

Para monitorizar o uso do consumo de armazenamento, consulte [as métricas de visualização para execuções e consumo de armazenamento.](plan-manage-costs.md#monitor-billing-metrics)

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Gateway de dados no local

Um [gateway de dados no local](../logic-apps/logic-apps-gateway-install.md) é um recurso separado que cria para que as suas aplicações lógicas possam aceder aos dados no local utilizando conectores específicos suportados por gateways. As operações de conector que passam pelo portal incorrem em cargas, mas a porta de entrada em si não incorre em cargas.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Aplicativos de lógica desativados

As aplicações lógicas desativadas não são cobradas porque não podem criar novos casos enquanto estão desativadas. Depois de desativar uma aplicação lógica, quaisquer casos em execução podem demorar algum tempo até que parem completamente.

## <a name="next-steps"></a>Passos seguintes

* [Planear e gerir custos para apps Azure Logic](plan-manage-costs.md)
