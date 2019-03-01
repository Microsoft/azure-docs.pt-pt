---
title: Preços e faturação - Azure Logic Apps | Documentos da Microsoft
description: Saiba como funciona o preços e faturação no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
manager: carmonm
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 02/26/2019
ms.openlocfilehash: 9b5452f112c6325dafd5edbe693b90ec2a94abc0
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990242"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modelo de preços do Azure Logic Apps

Pode criar e executar fluxos de trabalho de integração automatizada que podem ser dimensionada na cloud ao utilizar o Azure Logic Apps. Aqui estão os detalhes sobre como funcionam de faturação e preços para o Logic Apps. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modelo de preços de consumo

Para novas aplicações de lógica que são executados no serviço de aplicações lógicas "global" ou público, paga apenas aquilo que utiliza. Estas aplicações de lógica utilizam um plano com base no consumo e o modelo de preços. Em sua definição da aplicação lógica, cada passo é uma ação. As ações incluem o acionador, quaisquer passos de fluxo de controle, ações internas e chamadas de conector. O Logic Apps medidores de todas as ações que são executados na sua aplicação lógica.  
Para obter mais informações, consulte [preços de aplicações lógicas](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modelo de preços fixo

Para novas aplicações de lógica que são executados dentro de um [ *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), paga um preço mensal fixo ações incorporadas e conectores padrão. Um ISE fornece uma forma de criar e executar aplicações lógicas isoladas que podem aceder aos recursos numa rede virtual do Azure. 

Sua unidade de base do ISE corrigiu capacidade, portanto, se precisar de mais débito, pode [adiciona mais unidades de escala](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), qualquer um durante a criação ou posteriormente. O ISE inclui um conector empresarial gratuito, que inclui ligações tantas quanto quiser. Utilização para a empresa adicional conectores são cobrados com base no preço de consumo Enterprise. 

> [!NOTE]
> O ISE encontra-se no [ *pré-visualização pública*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, consulte [preços de aplicações lógicas](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Acionadores

Os acionadores são ações especiais que criar uma instância da aplicação lógica quando ocorre um evento específico. Acionadores agirem de diversas formas, que afetam a como a aplicação lógica é limitada.

* **Acionador de consulta** – este acionador verifica continuamente um ponto final para mensagens que satisfazem os critérios para criar uma instância da aplicação lógica e iniciar o fluxo de trabalho. Até mesmo quando nenhuma instância da aplicação lógica é criada, o Logic Apps medidores cada pedido de consulta como uma execução. Para especificar o intervalo de consulta, configure o acionador através do Estruturador da aplicação lógica.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Acionador de Webhook** – este acionador aguarda que um cliente para enviar um pedido para um ponto de extremidade específico. Cada pedido enviado para o ponto final do webhook é contabilizado como uma execução da ação. Por exemplo, o acionador de pedido e Webhook de HTTP são ambos os acionadores de webhook.

* **Acionador de periodicidade** – este acionador cria uma instância da aplicação lógica com base no intervalo de periodicidade que configurou no acionador. Por exemplo, pode definir um acionador de periodicidade, que é executado a cada três dias ou com base numa agenda mais complexa.

## <a name="actions"></a>Ações

O Logic Apps medidores ações incorporadas como ações nativas. Por exemplo, ações incorporadas incluem chamadas através de HTTP, chamadas de funções do Azure ou gestão de API e passos de fluxo de controlo, tais como ciclos e condições 
- cada um com seu próprio tipo de ação. Ações que chamam [conectores](https://docs.microsoft.com/connectors) tem o tipo "ApiConnection". Estes conectores são classificados como standard ou enterprise de conectores, que são medidas com base em seus respectivos [preços][pricing]. Conectores empresariais na *pré-visualização* é cobrada conforme os conectores padrão.

Medidores de aplicações de lógica de contas com êxito e sem êxito executam ações como execuções de ação. O Logic Apps não medir essas ações: 

* Ações que obterem ignoradas devido a condições por cumprir
* Ações que não são executados uma vez que a aplicação lógica parado antes de terminar

Aplicações lógicas desativado não são cobradas ao desativado porque não podem criar novas instâncias.

> [!NOTE]
> Depois de desativar uma aplicação lógica, quaisquer instâncias em execução podem demorar algum tempo antes que eles parem completamente.

Para ações que são executados dentro de loops, o Logic Apps conta cada ação por ciclo no loop. Por exemplo, suponhamos que tenha um loop "for each" que processa uma lista. Medidores de aplicações lógicas uma ação no loop ao multiplicar o número da lista de itens com o número de ações no loop e adiciona a ação que inicia o loop. O cálculo para obter uma lista de item de 10 é (10 * 1) + 1, o que resulta em 11 execuções de ação.

## <a name="integration-account-usage"></a>Utilização da conta de integração

Baseado no consumo de utilização aplica-se ao [contas de integração](logic-apps-enterprise-integration-create-integration-account.md) onde pode explorar, desenvolver e testar o [B2B/EDI](logic-apps-enterprise-integration-b2b.md) e [processamento XML](logic-apps-enterprise-integration-xml.md) funcionalidades nas aplicações lógicas ao não custos adicionais. Pode ter uma conta de integração por região. Cada conta de integração pode armazenar até específicas [números dos artefactos](../logic-apps/logic-apps-limits-and-config.md), que incluem o comércio de parceiros, contratos, mapas, esquemas, assemblies, certificados, configurações de lote e assim por diante.

O Logic Apps também oferece a contas de integração de básico e standard com um SLA de aplicações lógicas suportados. Pode utilizar contas de integração básica quando apenas pretende o processamento de mensagens ou atuar como um parceiro de pequenas empresas que possua uma relação de parceiro comercial com uma entidade de negócio maior. As contas de integração Standard suportam relações de B2B mais complexas e aumentam o número de entidades que pode gerir. Para obter mais informações, consulte [os preços do Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre o Logic Apps][whatis]
* [Criar a sua primeira aplicação lógica][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

