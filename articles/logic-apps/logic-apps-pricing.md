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
ms.date: 03/25/2019
ms.openlocfilehash: 4e5b63642b5090b50d29d6d3adc49ea683b90faa
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437718"
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

Para novas aplicações de lógica que são executados dentro de um [ *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), paga um preço mensal fixo ações incorporadas e conectores padrão. Um ISE fornece uma forma de criar e executar aplicações lógicas isoladas que podem aceder aos recursos numa rede virtual do Azure.

> [!NOTE]
> O ISE encontra-se no [ *pré-visualização pública*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter informações de preços específicas, consulte [preços de aplicações lógicas do Azure](https://azure.microsoft.com/pricing/details/logic-apps).

Sua unidade de base do ISE corrigiu capacidade, portanto, se precisar de mais débito, pode [adiciona mais unidades de escala](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), qualquer um durante a criação ou posteriormente. O ISE inclui um conector empresarial gratuito, que inclui ligações tantas quanto quiser. Utilização de conectores de empresa adicionais é cobrada com base no preço de consumo Enterprise.

<a name="connectors"></a>

## <a name="connectors"></a>Conectores

Conectores do Logic Apps do Azure ajudam as suas aplicações de acesso de aplicação lógica, serviços e sistemas na cloud ou no local, fornecendo [acionadores](#triggers), [ações](#actions), ou ambos. Conectores estão classificados como Standard ou Enterprise. Para uma descrição geral sobre estes conectores, consulte [conectores para o Azure Logic Apps](../connectors/apis-list.md). As secções seguintes fornecem mais informações sobre como a faturação de acionadores e ações funcionam.

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

Preços de consumo aplica-se ao [contas de integração](logic-apps-enterprise-integration-create-integration-account.md) onde pode explorar, desenvolver e testar o [B2B e EDI](logic-apps-enterprise-integration-b2b.md) e [processamento XML](logic-apps-enterprise-integration-xml.md) recursos no Azure Logic Apps em não custos adicionais.
Pode ter uma conta de integração em cada região do Azure. Cada conta de integração pode armazenar até específicas [números dos artefactos](../logic-apps/logic-apps-limits-and-config.md), que incluem o comércio de parceiros, contratos, mapas, esquemas, assemblies, certificados, configurações de lote e assim por diante.

O Azure Logic Apps também oferece a contas de integração de básico e Standard com um SLA de aplicações lógicas suportados. Seguem-se que pode escolher se pretende utilizar uma conta de integração do básico ou Standard de formas:

* Utilize contas de integração básica quando apenas pretende o processamento de mensagens ou atuar como um parceiro de pequenas empresas que possua uma relação de parceiro comercial com uma entidade de negócio maior.

* Utilize contas de integração Standard quando tem relações de B2B mais complexas e quer aumentar o número de entidades que pode gerir.

Para obter informações de preços específicas, consulte [preços do Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="data-retention"></a>

## <a name="data-retention"></a>Retenção de dados

Todas as entradas e saídas que estão armazenadas no histórico de execução da sua aplicação lógica serão cobradas com base numa aplicação lógica [executar o período de retenção](logic-apps-limits-and-config.md#run-duration-retention-limits). Para obter informações de preços específicas, consulte [preços do Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

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