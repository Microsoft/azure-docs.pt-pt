---
title: Preços & cobrança – aplicativos lógicos do Azure | Microsoft Docs
description: Saiba como os preços e as cobranças funcionam para os aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 62b96fa8722408aed81e87cb31218140fa4f5da8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099413"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modelo de preços para aplicativos lógicos do Azure

Os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) ajudam a criar e executar fluxos de trabalho de integração automatizados que podem ser dimensionados na nuvem. Este artigo descreve como a cobrança e os preços funcionam para os aplicativos lógicos do Azure. Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modelo de preços de consumo

Para novos aplicativos lógicos que são executados no serviço de aplicativos lógicos do Azure público ou "global", você paga apenas pelo que usar. Esses aplicativos lógicos usam um plano baseado em consumo e um modelo de preços. Em seu aplicativo lógico, cada etapa é uma ação, e os aplicativos lógicos do Azure medem todas as ações executadas em seu aplicativo lógico.

Por exemplo, as ações incluem:

* Gatilhos, que são ações especiais. Todos os aplicativos lógicos exigem um gatilho como a primeira etapa.
* [Ações "internas" ou nativas](../connectors/apis-list.md#built-in) como http, chamadas para Azure Functions e gerenciamento de API e assim por diante
* Chamadas para [conectores gerenciados](../connectors/apis-list.md##managed-connectors) , como o Outlook 365, Dropbox e assim por diante
* Etapas de fluxo de controle, como loops, instruções condicionais e assim por diante

Os [conectores padrão](../connectors/apis-list.md#managed-connectors) são cobrados com o [preço do conector padrão](https://azure.microsoft.com/pricing/details/logic-apps). Os [conectores empresariais](../connectors/apis-list.md#managed-connectors) geralmente disponíveis são cobrados pelo [preço do conector corporativo](https://azure.microsoft.com/pricing/details/logic-apps), enquanto os conectores corporativos de visualização pública são cobrados com o [preço do conector padrão](https://azure.microsoft.com/pricing/details/logic-apps).

Saiba mais sobre como a cobrança funciona [](#triggers) para gatilhos e [ações](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modelo de preços fixo

Um [ISE ( *ambiente do serviço de integração* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) fornece uma maneira privada, isolada e dedicada para você criar e executar aplicativos lógicos que podem acessar recursos em uma rede virtual do Azure. Para novos aplicativos lógicos que são executados dentro de um ISE, você paga um [preço mensal fixo](https://azure.microsoft.com/pricing/details/logic-apps) para esses recursos:

* [Gatilhos e ações internas](../connectors/apis-list.md#built-in)

* [Conectores Standard](../connectors/apis-list.md#managed-connectors)

* [Conectores Enterprise](../connectors/apis-list.md#enterprise-connectors) com tantas conexões quantas quiser

* Uso da [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) sem custo adicional, com base em seu [SKU do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

  * **SKU Premium**: Uma única conta de integração de [camada Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * **SKU do desenvolvedor**: Uma única conta de integração de [camada gratuita](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Cada SKU do ISE é limitado a 5 contas de integração totais. Para obter um custo adicional, você pode ter mais contas de integração, com base em seu SKU do ISE:

  * **SKU Premium**: Até quatro contas padrão. Nenhuma conta gratuita ou básica.

  * **SKU do desenvolvedor**: Até quatro contas padrão ou até 5 contas padrão... Nenhuma conta básica.

Para obter mais informações sobre limites de conta de integração, consulte [limites e configuração de aplicativos lógicos](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Você pode aprender mais sobre [as camadas de conta de integração e seu modelo de preços](#integration-accounts) posteriormente neste tópico.

Para o SKU do ISE Premium, a unidade base tem capacidade fixa, portanto, se você precisar de mais taxa de transferência, poderá [adicionar mais unidades de escala](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), seja durante a criação ou depois. A SKU do ISE do desenvolvedor não tem a capacidade de adicionar mais unidades de escala. Os aplicativos lógicos executados em um ISE não incorrem em custos de retenção de dados.

> [!NOTE]
> Em um ISE, gatilhos e ações internas exibem o rótulo **principal** e são executados no mesmo ISE que seus aplicativos lógicos. Conectores Standard e Enterprise que exibem o rótulo do **ISE** executados no mesmo ISE que seus aplicativos lógicos. Os conectores que não exibem a etiqueta do ISE são executados no serviço de aplicativos lógicos globais.

Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Conectores

Os conectores de aplicativos lógicos do Azure ajudam seu aplicativo lógico a acessar aplicativos, serviços e sistemas na nuvem ou [](#triggers)localmente, fornecendo gatilhos, [ações](#actions)ou ambos. Os conectores são classificados como Standard ou Enterprise. Para obter uma visão geral sobre esses conectores, consulte [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md). Se nenhum conector predefinido estiver disponível para as APIs REST que você deseja usar em seus aplicativos lógicos, você poderá criar [conectores personalizados](https://docs.microsoft.com/connectors/custom-connectors), que são apenas wrappers em volta dessas APIs REST. Os conectores personalizados são cobrados como conectores padrão. As seções a seguir fornecem mais informações sobre como a cobrança de gatilhos e ações funcionam.

<a name="triggers"></a>

## <a name="triggers"></a>Ativadores

Os gatilhos são ações especiais que criam uma instância de aplicativo lógico quando ocorre um evento específico. Os gatilhos atuam de maneiras diferentes, o que afeta como o aplicativo lógico é medido. Aqui estão os vários tipos de gatilhos que existem nos aplicativos lógicos do Azure:

* **Gatilho**de sondagem: Esse gatilho verifica continuamente um ponto de extremidade para mensagens que atendem aos critérios para criar uma instância de aplicativo lógico e iniciar o fluxo de trabalho. Mesmo quando nenhuma instância de aplicativo lógico é criada, os aplicativos lógicos medem cada solicitação de sondagem como uma execução. Para especificar o intervalo de sondagem, configure o gatilho por meio do designer de aplicativo lógico.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Gatilho**de webhook: Esse gatilho espera que um cliente envie uma solicitação para um ponto de extremidade específico. Cada solicitação enviada ao ponto de extremidade do webhook conta como uma execução de ação. Por exemplo, a solicitação e o gatilho de webhook HTTP são gatilhos de webhook.

* **Gatilho**de recorrência: Esse gatilho cria uma instância de aplicativo lógico com base no intervalo de recorrência que você configurou no gatilho. Por exemplo, você pode configurar um gatilho de recorrência que é executado a cada três dias ou em uma agenda mais complexa.

<a name="actions"></a>

## <a name="actions"></a>Ações

O aplicativo lógico do Azure monitora as ações "internas", como HTTP, como ações nativas. Por exemplo, ações internas incluem chamadas HTTP, chamadas de Azure Functions ou gerenciamento de API e etapas de fluxo de controle, como condições, loops e instruções switch. Cada ação tem seu próprio tipo de ação. Por exemplo, ações que chamam [conectores](https://docs.microsoft.com/connectors) têm o tipo "ApiConnection". Esses conectores são classificados como conectores Standard ou Enterprise, que são medidos com base em seus respectivos [preços](https://azure.microsoft.com/pricing/details/logic-apps). Conectores empresariais na versão *prévia* são cobrados como conectores padrão.

Os aplicativos lógicos do Azure medem todas as ações bem-sucedidas e malsucedidas como execuções. No entanto, os aplicativos lógicos não medim essas ações:

* Ações que são ignoradas devido a condições não atendidas
* Ações que não são executadas porque o aplicativo lógico parou antes de concluir

Para ações executadas dentro de loops, os aplicativos lógicos do Azure contam cada ação para cada ciclo no loop. Por exemplo, suponha que você tenha um loop "for each" que processa uma lista. Os aplicativos lógicos medem uma ação nesse loop multiplicando o número de itens de lista pelo número de ações no loop e adiciona a ação que inicia o loop. Portanto, o cálculo para uma lista de 10 itens é (10 * 1) + 1, o que resulta em 11 execuções de ação.

## <a name="disabled-logic-apps"></a>Aplicativos lógicos desabilitados

Os aplicativos lógicos desabilitados não são cobrados porque não podem criar novas instâncias enquanto estão desabilitados. Depois de desabilitar um aplicativo lógico, qualquer instância em execução no momento pode levar algum tempo antes de parar completamente.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Contas de integração

Um [modelo de preço fixo](https://azure.microsoft.com/pricing/details/logic-apps) se aplica a [contas de integração](logic-apps-enterprise-integration-create-integration-account.md) em que você pode explorar, desenvolver e testar os recursos [B2B e](logic-apps-enterprise-integration-b2b.md) de [processamento XML](logic-apps-enterprise-integration-xml.md) no aplicativo lógico do Azure sem custo adicional. Cada assinatura do Azure pode ter até um [limite específico de contas de integração](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Cada conta de integração pode armazenar até o [limite específico de artefatos](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), que incluem parceiros comerciais, contratos, mapas, esquemas, assemblies, certificados, configurações de lote e assim por diante.

O aplicativo lógico do Azure oferece contas de integração gratuitas, básicas e padrão. As camadas básica e Standard têm suporte do SLA (contrato de nível de serviço) dos aplicativos lógicos, enquanto a camada gratuita não tem suporte de um SLA e tem limites de taxa de transferência e uso. Exceto para contas de integração de camada gratuita, você pode ter mais de uma conta de integração em cada região do Azure. Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/).

Se você tiver um [ISE ( *ambiente do serviço de integração* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), [Premium ou desenvolvedor](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), o ISE poderá ter 5 contas de integração total. Para saber como o modelo de preços fixo funciona para um ISE, consulte a seção [modelo de preços fixos](#fixed-pricing) anteriores neste tópico. Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps).

Para escolher entre uma conta de integração gratuita, básica ou padrão, examine estas descrições de caso de uso:

* **Gratuito**: Para quando você quiser experimentar cenários exploratórios, não cenários de produção

* **Básico**: Para quando você quiser apenas a manipulação de mensagens ou para atuar como um pequeno parceiro comercial que tenha uma relação de parceiro comercial com uma entidade de negócios maior

* **Padrão**: Para quando você tem relações B2B mais complexas e um número maior de entidades que você deve gerenciar

<a name="data-retention"></a>

## <a name="data-retention"></a>Retenção de dados

Exceto para aplicativos lógicos que são executados em um ambiente do serviço de integração (ISE), todas as entradas e saídas armazenadas no histórico de execução do aplicativo lógico são cobradas com base em um [período de retenção de execução](logic-apps-limits-and-config.md#run-duration-retention-limits)do aplicativo lógico. Os aplicativos lógicos executados em um ISE não incorrem em custos de retenção de dados. Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps).

Para ajudá-lo a monitorar o consumo de armazenamento do aplicativo lógico, você pode:

* Exiba o número de unidades de armazenamento em GB que seu aplicativo lógico usa mensalmente.
* Exiba os tamanhos das entradas e saídas de uma ação específica no histórico de execução do aplicativo lógico.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Exibir consumo de armazenamento do aplicativo lógico

1. Na portal do Azure, localize e abra seu aplicativo lógico.

1. No menu do aplicativo lógico, em **monitoramento**, selecione **métricas**.

1. No painel direito, em **título do gráfico**, na lista **métrica** , selecione **uso de cobrança para execuções de consumo de armazenamento**.

   Essa métrica fornece o número de unidades de consumo de armazenamento em GB por mês que estão sendo cobradas.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Exibir os tamanhos de entrada e saída da ação

1. Na portal do Azure, localize e abra seu aplicativo lógico.

1. No menu do aplicativo lógico, selecione **visão geral**.

1. No painel direito, em **histórico**de execuções, selecione a execução que tem as entradas e saídas que você deseja verificar.

1. Em **execução do aplicativo lógico**, escolha **detalhes da execução**.

1. No painel **detalhes de execução do aplicativo lógico** , na tabela ações, que lista o status e a duração de cada ação, selecione a ação que você deseja exibir.

1. No painel de **ação do aplicativo lógico** , localize os tamanhos das entradas e saídas da ação, respectivamente, sob o link **entradas** e **saídas**.

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre os aplicativos lógicos do Azure](logic-apps-overview.md)
* [Criar a sua primeira aplicação lógica](quickstart-create-first-logic-app-workflow.md)
