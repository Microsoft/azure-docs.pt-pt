---
title: Acesso a redes virtuais Azure
description: Visão geral sobre como os ambientes de serviços de integração (ISEs) ajudam as aplicações lógicas a aceder às redes virtuais Azure (VNETs)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 1f743384f467e4559412fa1a46d48011b568d249
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191595"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acesso aos recursos da Rede Virtual Azure a partir de Aplicações Lógicas Azure utilizando ambientes de serviçode integração (ISEs)

Por vezes, as suas aplicações lógicas e contas de integração precisam de acesso a recursos seguros, como máquinas virtuais (VMs) e outros sistemas ou serviços, que estão dentro de uma [rede virtual Azure.](../virtual-network/virtual-networks-overview.md) Para configurar este acesso, pode criar um ambiente de serviço de [ *integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) onde pode executar as suas aplicações lógicas e criar as suas contas de integração.

Ao criar um ISE, o Azure *injeta* esse ISE na sua rede virtual Azure, que depois implementa uma instância privada e isolada do serviço de Aplicações Lógicas na sua rede virtual Azure. Esta instância privada utiliza recursos dedicados, como armazenamento, e funciona separadamente do público, serviço "global", multi-inquilino logic Apps. Separar a sua instância privada isolada e a instância global pública também ajuda a reduzir o impacto que outros inquilinos do Azure podem ter no desempenho das suas apps, que também é conhecido como o [efeito "vizinhos barulhentos".](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) Um ISE também lhe fornece os seus próprios endereços IP estáticos. Estes endereços IP são separados dos endereços IP estáticos que são partilhados pelas aplicações lógicas no serviço público, multi-inquilino.

Depois de criar o seu ISE, quando for criar a sua app lógica ou conta de integração, pode selecionar o seu ISE como aplicação lógica ou localização da conta de integração:

![Selecione ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

A sua aplicação lógica pode agora aceder diretamente a sistemas que estão dentro ou ligados à sua rede virtual utilizando qualquer um destes itens, que funcionam dentro do mesmo ISE que a sua aplicação lógica:

* Um conector com etiqueta **ISE**para este sistema
* Umgatilho ou ação incorporado core-rotulado, como o gatilho ou ação HTTP
* Um conector personalizado

Esta visão geral descreve mais detalhes sobre como um ISE dá às suas aplicações lógicas e contas de integração acesso direto à sua rede virtual Azure e compara diferenças entre um serviço ISE e o serviço global de Aplicações Lógicas.

> [!IMPORTANT]
> Aplicativos lógicos, gatilhos incorporados, ações incorporadas e conectores que funcionam no seu ISE usam um plano de preços que difere do plano de preços baseado no consumo. Para saber como funcionam os preços e a faturação para os ISEs, consulte o modelo de preços das [Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Para preços, consulte [preços de Apps Lógicas.](../logic-apps/logic-apps-pricing.md)
>
> O ise também tem limites aumentados na duração do execução, retenção de armazenamento, saída, tempos de pedido e resposta HTTP, tamanhos de mensagem e pedidos de conector personalizados. 
> Para mais informações, consulte [Limites e configuração para Aplicações Lógicas Azure](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolado versus global

Quando criar um ambiente de serviço integrado (ISE) em Azure, pode selecionar a rede virtual Azure onde pretende *injetar* o seu ISE. Em seguida, o Azure injeta, ou implanta, uma instância privada do serviço De Aplicações Lógicas na sua rede virtual. Esta ação cria um ambiente isolado onde você pode criar e executar suas aplicações lógicas em recursos dedicados. Ao criar a sua aplicação lógica, selecione o seu ISE como localização da sua aplicação, o que dá à sua aplicação lógica acesso direto à sua rede virtual e aos recursos dessa rede.

As aplicações lógicas num ISE fornecem as mesmas experiências de utilizador e capacidades semelhantes ao serviço público global de Aplicações Lógicas. Você pode usar todos os mesmos gatilhos incorporados, ações e conectores geridos que estão disponíveis no serviço global de Aplicações Lógicas. Alguns conectores geridos oferecem versões ISE adicionais. Existe a diferença no local onde funcionam e nos rótulos que exibem no Logic App Designer quando se trabalha dentro de um ISE.

![Conectores com e sem rótulos num ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Os gatilhos e ações incorporados exibem a etiqueta **CORE,** e funcionam sempre no mesmo ISE que a sua aplicação lógica. Os conectores geridos que exibem a etiqueta **ISE** também funcionam no mesmo ISE que a sua aplicação lógica.

  Por exemplo, aqui estão alguns conectores que oferecem versões ISE:

  * Armazenamento de Blob Azure, Armazenamento de Ficheiros e Armazenamento de Mesa
  * Filas Azure, Azure Service Bus, Hubs de Eventos Azure e IBM MQ
  * FTP e SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 e EDIFACT

* Os conectores geridos que não apresentam etiquetas adicionais funcionam sempre no serviço público global de Aplicações Lógicas, mas ainda pode utilizar estes conectores numa aplicação lógica baseada no ISE.

Um ISE também fornece limites acrescidos para a duração do execução, retenção de armazenamento, entrada, tempos de pedido e resposta HTTP, tamanhos de mensagem e pedidos de conector personalizados. Para mais informações, consulte [Limites e configuração para Aplicações Lógicas Azure](logic-apps-limits-and-config.md).

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKUs

Quando criar o seu ISE, pode selecionar o Developer SKU ou Premium SKU. Aqui estão as diferenças entre estas UsK:

* **Programador**

  Fornece um ISE de baixo custo que pode usar para experimentação, desenvolvimento e teste, mas não para testes de produção ou desempenho. O Developer SKU inclui gatilhos e ações incorporados, conectores Standard, conectores Enterprise e uma única conta de integração de [nível Livre](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) por um preço mensal fixo. No entanto, este SKU não inclui qualquer acordo de nível de serviço (SLA), opções para aumentar a capacidade, ou redundância durante a reciclagem, o que significa que você pode experimentar atrasos ou tempo de inatividade.

* **Premium**

  Fornece um ISE que pode utilizar para produção e inclui suporte SLA, gatilhos e ações incorporados, conectores Standard, conectores da Enterprise, uma única conta de integração [de nível Standard,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) opções para aumentar a capacidade e redundância durante a reciclagem por um preço mensal fixo.

> [!IMPORTANT]
> A opção SKU está disponível apenas na criação do ISE e não pode ser alterada mais tarde.

Para preços, consulte [preços de Apps Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/) Para saber como funcionam os preços e a faturação para os ISEs, consulte o modelo de preços das [Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Acesso ao ponto final do ISE

Quando criar o seu ISE, pode optar por utilizar pontos finais de acesso internos ou externos. A sua seleção determina se o pedido ou webhook dispara em aplicações lógicas no seu ISE pode receber chamadas de fora da sua rede virtual.

Estes pontos finais também afetam a forma como pode aceder a inputs e saídas no histórico de execução das suas aplicações lógicas.

* **Interna**: Pontos finais privados que permitem chamadas para aplicações lógicas no seu ISE onde você pode ver e aceder às inputs e saídas das suas aplicações lógicas na história de execução *apenas a partir de dentro da sua rede virtual*

* **External**: Pontos finais públicos que permitem chamadas para aplicações lógicas no seu ISE onde você pode ver e aceder às inputs e saídas das suas aplicações lógicas em histórico de execução *de fora da sua rede virtual*. Se utilizar grupos de segurança de rede (NSGs), certifique-se de que são configurados com regras de entrada para permitir o acesso às inputs e saídas do histórico de execução. Para mais informações, consulte [Permitir o acesso ao ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> A opção de ponto final de acesso está disponível apenas na criação do ISE e não pode ser alterada mais tarde.

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>Acesso a fontes de dados no local

Para sistemas no local ligados a uma rede virtual Azure, injete um ISE nessa rede para que as suas aplicações lógicas possam aceder diretamente a esses sistemas utilizando qualquer um destes itens:

* Ação HTTP

* Conector com rótulo ISE para este sistema

  > [!NOTE]
  > Para utilizar a autenticação do Windows com o conector SQL Server num ambiente de serviço de [integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), utilize a versão não ISE do conector com a [porta de dados no local](../logic-apps/logic-apps-gateway-install.md). A versão com a etiqueta ISE não suporta a autenticação do Windows.

* Conector personalizado

  * Se tiver conectores personalizados que exijam a porta de dados no local, e criou esses conectores fora de um ISE, as aplicações lógicas num ISE também podem usar esses conectores.

  * Os conectores personalizados criados num ISE não funcionam com a porta de dados no local. No entanto, estes conectores podem aceder diretamente a fontes de dados no local que estão ligadas à rede virtual que acolhe o ISE. Assim, aplicações lógicas num ISE provavelmente não precisam da porta de dados quando comunicam com esses recursos.

Para sistemas no local que não estejam ligados a uma rede virtual ou que não tenham conectores com a etiqueta ISE, tem primeiro de [configurar a porta de dados no local](../logic-apps/logic-apps-gateway-install.md) antes que as suas aplicações lógicas possam ligar-se a esses sistemas.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com o ISE

Pode utilizar contas de integração com aplicações lógicas dentro de um ambiente de serviço de integração (ISE). No entanto, essas contas de integração devem usar o *mesmo ISE* que as aplicações lógicas ligadas. As aplicações lógicas num ISE só podem fazer referência às contas de integração que se encontram no mesmo ISE. Ao criar uma conta de integração, pode selecionar o seu ISE como local para a sua conta de integração. Para saber como funcionam os preços e a faturação para contas de integração com um ISE, consulte o modelo de preços das [Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Para preços, consulte [preços de Apps Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/)

## <a name="next-steps"></a>Passos seguintes

* [Ligue-se a redes virtuais Azure de aplicações lógicas isoladas](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Adicione artefactos aos ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerir ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md)
* Saiba mais sobre [a Rede Virtual Azure](../virtual-network/virtual-networks-overview.md)
* Conheça a [integração de redes virtuais para serviços Azure](../virtual-network/virtual-network-for-azure-services.md)
