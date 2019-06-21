---
title: Preços e faturação - Azure Logic Apps | Documentos da Microsoft
description: Saiba como funciona o preços e faturação no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 05/22/2019
ms.openlocfilehash: 04b1d0eda85972517155f80488ad590fb56619ab
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190687"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modelo de preços do Azure Logic Apps

[O Azure Logic Apps](../logic-apps/logic-apps-overview.md) ajuda a criar e executar fluxos de trabalho de integração automatizada que pode dimensionar na cloud. Este artigo descreve como a faturação e preços funcionam para o Azure Logic Apps. Para obter informações de preços específicas, consulte [preços de aplicações lógicas do Azure](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modelo de preços de consumo

Para novas aplicações de lógica que são executados no serviço de Azure Logic Apps "global" ou público, paga apenas aquilo que utiliza. Estas aplicações de lógica utilizam um plano com base no consumo e o modelo de preços. Em sua definição da aplicação lógica, cada passo é uma ação. Por exemplo, ações incluem:

* Acionadores, que são ações especiais. Todas as aplicações de lógica requerem um acionador, como o primeiro passo.
* "Internas" ou nativas ações como HTTP, chamadas para as funções do Azure e a gestão de API e assim por diante
* Chamadas em conectores como o Outlook 365, Dropbox e assim por diante
* Controlar os passos de fluxo, como loops, instruções condicionais e assim por diante

O Azure Logic Apps medidores de todas as ações que executar na sua aplicação lógica. Saiba mais sobre como funciona a faturação para [acionadores](#triggers) e [ações](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modelo de preços fixo

Uma [ *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) fornece uma maneira privada, isolada e dedicada para que possa criar e executar aplicações lógicas que podem aceder aos recursos numa rede virtual do Azure. Para novas aplicações de lógica que são executados dentro de um ISE, paga um [preço mensal fixo](https://azure.microsoft.com/pricing/details/logic-apps) para ações incorporadas e acionadores e também para conectores padrão.

O ISE também inclui um conector empresarial gratuito, que inclui o máximo *ligações* conforme desejado. Utilização de conectores de empresa adicionais é cobrada com base no [preço de consumo de Enterprise](https://azure.microsoft.com/pricing/details/logic-apps). Apenas geralmente conectores de Enterprise disponíveis são cobradas ao preço de consumo Enterprise. Conectores empresariais de pré-visualização pública são cobrados a [taxa de conector Standard](https://azure.microsoft.com/pricing/details/logic-apps).

> [!NOTE]
> Dentro de um ISE, incorporados acionadores e ações apresentar os **Core** Etiquetar e executar no ISE do mesmo, como as aplicações lógicas. Standard e conectores empresariais que apresentam os **ISE** etiqueta executar no ISE do mesmo, como as aplicações lógicas. Conectores que não apresentam a etiqueta ISE executada no serviço de aplicações lógicas global.

Sua unidade de base do ISE corrigiu capacidade, portanto, se precisar de mais débito, pode [adiciona mais unidades de escala](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), qualquer um durante a criação ou posteriormente. Aplicações lógicas que são executados num ISE não incorrem em custos de retenção de dados.

Para obter informações de preços específicas, consulte [preços de aplicações lógicas do Azure](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Conectores

Conectores do Logic Apps do Azure ajudam as suas aplicações de acesso de aplicação lógica, serviços e sistemas na cloud ou no local, fornecendo [acionadores](#triggers), [ações](#actions), ou ambos. Conectores estão classificados como Standard ou Enterprise. Para uma descrição geral sobre estes conectores, consulte [conectores para o Azure Logic Apps](../connectors/apis-list.md). Se não existem conectores criados previamente estão disponíveis para as APIs REST que pretende utilizar nas suas aplicações lógicas, pode criar [conectores personalizados](https://docs.microsoft.com/connectors/custom-connectors), que são apenas wrappers em torno das REST APIs. Conectores personalizados são faturados como conectores padrão. As secções seguintes fornecem mais informações sobre como a faturação de acionadores e ações funcionam.

<a name="triggers"></a>

## <a name="triggers"></a>Acionadores

Os acionadores são ações especiais que criar uma instância da aplicação lógica quando ocorre um evento específico. Acionadores agirem de diversas formas, que afetam a como a aplicação lógica é limitada. Aqui estão os vários tipos de acionadores que existem no Azure Logic Apps:

* **Acionador de consulta**: Este acionador verifica continuamente um ponto final para mensagens que satisfazem os critérios para criar uma instância da aplicação lógica e iniciar o fluxo de trabalho. Até mesmo quando nenhuma instância da aplicação lógica é criada, o Logic Apps medidores cada pedido de consulta como uma execução. Para especificar o intervalo de consulta, configure o acionador através do Estruturador da aplicação lógica.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook trigger**: Este acionador é aguarda por um cliente para enviar um pedido para um ponto de extremidade específico. Cada pedido enviado para o ponto final do webhook é contabilizado como uma execução da ação. Por exemplo, o acionador de pedido e Webhook de HTTP são ambos os acionadores de webhook.

* **Acionador de periodicidade**: Este acionador cria uma instância da aplicação lógica com base no intervalo de periodicidade que configurou no acionador. Por exemplo, pode definir um acionador de periodicidade, que é executado a cada três dias ou com base numa agenda mais complexa.

<a name="actions"></a>

## <a name="actions"></a>Ações

O Azure Logic Apps medidores "internas" ações, como HTTP, como ações nativas. Por exemplo, ações incorporadas incluem chamadas HTTP, chamadas de funções do Azure ou gestão de API e controlam os passos de fluxo, como condições, ciclos e declarações de comutador. Cada ação tem seu próprio tipo de ação. Por exemplo, as ações que chamam [conectores](https://docs.microsoft.com/connectors) tem o tipo "ApiConnection". Estes conectores são classificados como padrão ou conectores empresariais, o que são medidas com base em seus respectivos [preços](https://azure.microsoft.com/pricing/details/logic-apps). Conectores empresariais na *pré-visualização* é cobrada conforme os conectores padrão.

O Azure Logic Apps medidores de todas as ações de com e sem êxito como execuções. No entanto, o Logic Apps não medir essas ações:

* Ações que obterem ignoradas devido a condições por cumprir
* Ações que não são executados uma vez que a aplicação lógica parado antes de terminar

Para ações que são executados dentro de loops, o Azure Logic Apps conta cada ação para cada ciclo no loop. Por exemplo, suponhamos que tenha um loop "for each" que processa uma lista. Medidores de aplicações lógicas uma ação no loop ao multiplicar o número da lista de itens com o número de ações no loop e adiciona a ação que inicia o loop. Por isso, o cálculo para obter uma lista de item de 10 é (10 * 1) + 1, o que resulta em 11 execuções de ação.

## <a name="disabled-logic-apps"></a>Aplicações lógicas desativado

Desativada a lógica de aplicações não são cobradas porque eles não é possível criar novas instâncias enquanto eles estão desativados.
Depois de desativar uma aplicação lógica, quaisquer instâncias em execução podem demorar algum tempo antes que eles parem completamente.

## <a name="integration-accounts"></a>Contas de integração

O modelo de preço fixo aplica-se ao [contas de integração](logic-apps-enterprise-integration-create-integration-account.md) onde pode explorar, desenvolver e testar o [B2B e EDI](logic-apps-enterprise-integration-b2b.md) e [processamento XML](logic-apps-enterprise-integration-xml.md) recursos no Azure Logic Apps em não custos adicionais.
Pode ter uma conta de integração em cada região do Azure. Cada conta de integração pode armazenar até específicas [números dos artefactos](../logic-apps/logic-apps-limits-and-config.md), que incluem o comércio de parceiros, contratos, mapas, esquemas, assemblies, certificados, configurações de lote e assim por diante.

O Azure Logic Apps oferece contas de integração gratuito, Basic e Standard. Os escalões básico e Standard são compatíveis com o contrato de nível de serviço de aplicações lógicas (SLA), enquanto o escalão gratuito não é suportado por um SLA e tem limites relativamente ao débito e de utilização.

Escolher entre uma conta de integração gratuito, básico ou Standard:

* **Gratuito**: Para que deseja tentar cenários exploratórios, não os cenários de produção.

* **Básico**: Para quando pretende que o processamento de mensagens única ou para agir como um parceiro de pequenas empresas que possua uma relação de parceiro comercial com uma entidade de negócio maior.

* **Padrão**: Para quando tem relações de B2B mais complexas e maior número de entidades que tem de gerir.

Para obter informações de preços específicas, consulte [preços do Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="data-retention"></a>

## <a name="data-retention"></a>Retenção de dados

Exceto para aplicações lógicas que executar num ambiente de serviço de integração (ISE), todas as entradas e saídas que estão armazenadas no histórico de execução da sua aplicação lógica serão cobradas com base numa aplicação lógica [executar o período de retenção](logic-apps-limits-and-config.md#run-duration-retention-limits). Aplicações lógicas que são executados num ISE não incorrem em custos de retenção de dados. Para obter informações de preços específicas, consulte [preços do Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

Para ajudar a monitorizar o consumo de armazenamento da sua aplicação lógica, pode:

* Ver o número de unidades de armazenamento em GB que a aplicação lógica utiliza mensalmente.
* Veja os tamanhos para entradas e saídas de uma ação específica no histórico de execução da sua aplicação lógica.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Consumo de armazenamento de aplicação de lógica de exibição

1. No portal do Azure, localize e abra a aplicação lógica.

1. No menu da sua aplicação lógica, sob **monitorização**, selecione **métricas**.

1. No painel da direita, em **título do gráfico**, da **métrica** lista, selecione **utilização de faturação para execuções de consumo de armazenamento**.

   Esta métrica fornece o número de consumo unidades de armazenamento em GB por mês que são faturadas.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Ver a ação de entrada e saída tamanhos

1. No portal do Azure, localize e abra a aplicação lógica.

1. No menu da sua aplicação lógica, selecione **descrição geral**.

1. No painel da direita, em **histórico de execuções**, selecione a execução que tem as entradas e saídas que pretende verificar.

1. Sob **execução da aplicação lógica**, escolha **detalhes da execução**.

1. Na **detalhes de execução da aplicação lógica** painel, na tabela de ações, que apresenta uma lista de estado e a duração de cada ação, selecione a ação que pretende visualizar.

1. Na **ação da aplicação lógica** painel, localizar os tamanhos para entradas e saídas da ação que aparecem em respectivamente **ligação de entradas** e **ligação de saídas**.

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre o Azure Logic Apps](logic-apps-overview.md)
* [Criar a sua primeira aplicação lógica](quickstart-create-first-logic-app-workflow.md)
