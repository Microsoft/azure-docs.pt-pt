---
title: Aceder às redes virtuais do Azure
description: Visão geral sobre como os ambientes de serviços de integração (ISEs) ajudam as aplicações lógicas a aceder às redes virtuais Azure (VNETs)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: d74303df74a1e877645b333fa0726a68055c819b
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734926"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acesso aos recursos da Rede Virtual Azure a partir de Aplicações Lógicas Azure utilizando ambientes de serviçode integração (ISEs)

Por vezes, as suas aplicações lógicas precisam de acesso a recursos seguros, como máquinas virtuais (VMs) e outros sistemas ou serviços, que estão dentro de uma [rede virtual Azure.](../virtual-network/virtual-networks-overview.md) Para configurar este acesso, pode criar um ambiente de serviço de [ *integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). Um ISE é uma instância isolada do serviço De Aplicações Lógicas que utiliza recursos dedicados e funciona separadamente do serviço "global" de Aplicações Lógicas multi-inquilinos.

Executar aplicações lógicas em seu próprio caso isolado separado ajuda a reduzir o impacto que outros inquilinos do Azure podem ter no desempenho das suas apps, também conhecido como o [efeito "vizinhos barulhentos".](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) Um ISE também fornece estes benefícios:

* Os seus próprios endereços IP estáticos, separados dos endereços IP estáticos que são partilhados pelas aplicações lógicas no serviço multi-inquilinos. Também pode configurar um único endereço IP público, estático e previsível para comunicar com os sistemas de destino. Dessa forma, não é preciso configurar aberturas adicionais de firewall nesses sistemas de destino para cada ISE.

* Limites acrescidos na duração do percurso, retenção de armazenamento, entrada, tempos de pedido e resposta http, tamanhos de mensagem e pedidos de conector personalizados. Para mais informações, consulte [Limites e configuração para Aplicações Lógicas Azure](logic-apps-limits-and-config.md).

> [!NOTE]
> Algumas redes virtuais Azure utilizam pontos finais privados ([Azure Private Link)](../private-link/private-link-overview.md)para fornecer acesso aos serviços Azure PaaS, tais como O Armazenamento Azure, Azure Cosmos DB ou Azure SQL Database, serviços parceiros ou serviços de clientes que estão hospedados no Azure. Se as suas aplicações lógicas precisam de acesso a redes virtuais que utilizam pontos finais privados, tem de criar, implementar e executar essas aplicações lógicas dentro de um ISE.

Quando cria um ISE, o Azure *injeta* ou implanta o ISE na sua rede virtual Azure. Em seguida, pode utilizar este ISE como local para as aplicações lógicas e contas de integração que precisam de acesso.

![Selecione ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

As aplicações lógicas podem aceder a recursos que estão dentro ou ligados à sua rede virtual utilizando estes itens, que funcionam no mesmo ISE que as suas aplicações lógicas:

* Um **CORE**gatilho ou ação incorporado core-rotulado, como o gatilho ou ação HTTP
* Um conector com etiqueta **ISE**para esse sistema ou serviço
* Um conector personalizado

Também pode utilizar conectores que não tenham a etiqueta **CORE** ou **ISE** com as aplicações lógicas no seu ISE. Estes conectores funcionam no serviço de Aplicações Lógicas multi-inquilinos. Para mais informações, consulte estas secções:

* [Isolado contra multi-inquilino](#difference)
* [Conecte-se a partir de um ambiente de serviço de integração](../connectors/apis-list.md#integration-service-environment)
* [Conectores ISE](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Aplicativos lógicos, gatilhos incorporados, ações incorporadas e conectores que funcionam no seu ISE usam um plano de preços que difere do plano de preços baseado no consumo. Para mais informações, consulte o modelo de preços das [Apps Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Para obter detalhes sobre preços, consulte [os preços das Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md)

Esta visão geral descreve mais informações sobre como um ISE dá às suas aplicações lógicas acesso direto à sua rede virtual Azure e compara as diferenças entre um ise e o serviço de Aplicações Lógicas multi-inquilinos.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Isolado contra multi-inquilino

Ao criar e executar aplicações lógicas num ISE, obtém-se as mesmas experiências de utilizador e capacidades semelhantes ao do serviço de Aplicações Lógicas multi-inquilinos. Você pode usar todos os mesmos gatilhos incorporados, ações e conectores geridos que estão disponíveis no serviço de Aplicações Lógicas multi-inquilinos. Alguns conectores geridos oferecem versões ISE adicionais. A diferença entre conectores ISE e conectores não ISE existe no local onde funcionam e as etiquetas que têm no Logic App Designer quando se trabalha dentro de um ISE.

![Conectores com e sem rótulos num ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Gatilhos e ações incorporados exibem a etiqueta **CORE.** Eles sempre correm no mesmo ISE que a sua aplicação lógica. Os conectores geridos que exibem a etiqueta **ISE** também funcionam no mesmo ISE que a sua aplicação lógica.

  Por exemplo, aqui estão alguns conectores que oferecem versões ISE:

  * Armazenamento de Blob Azure, Armazenamento de Ficheiros e Armazenamento de Mesa
  * Filas Azure, Azure Service Bus, Hubs de Eventos Azure e IBM MQ
  * FTP e SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 e EDIFACT

* Os conectores geridos que não apresentam etiquetas adicionais funcionam sempre no serviço de Aplicações Lógicas multi-inquilinos, mas ainda pode utilizar estes conectores numa aplicação lógica hospedada pelo ISE.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Acesso a sistemas no local

Para aceder a sistemas no local ou fontes de dados ligadas a uma rede virtual Azure, as aplicações lógicas num ISE podem usar estes itens:

* Ação HTTP

* Conector com rótulo ISE para este sistema

  > [!NOTE]
  > Para utilizar a autenticação do Windows com o conector SQL Server num ambiente de serviço de [integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), utilize a versão não ISE do conector com a [porta de dados no local](../logic-apps/logic-apps-gateway-install.md). A versão com a etiqueta ISE não suporta a autenticação do Windows.

* Conector personalizado

  * Se tiver conectores personalizados que exijam a porta de dados no local, e criou esses conectores fora de um ISE, as aplicações lógicas num ISE também podem usar esses conectores.

  * Os conectores personalizados criados num ISE não funcionam com a porta de dados no local. No entanto, estes conectores podem aceder diretamente a fontes de dados no local que estão ligadas à rede virtual que acolhe o ISE. Assim, aplicações lógicas num ISE provavelmente não precisam da porta de dados quando comunicam com esses recursos.

Para sistemas no local que não estejam ligados a uma rede virtual ou que não tenham conectores com a etiqueta ISE, tem primeiro de [configurar a porta de dados no local](../logic-apps/logic-apps-gateway-install.md) antes que as suas aplicações lógicas possam ligar-se a esses sistemas.

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

Quando criar o seu ISE, pode optar por utilizar pontos finais de acesso internos ou externos. A sua seleção determina se o pedido ou webhook dispara em aplicações lógicas no seu ISE pode receber chamadas de fora da sua rede virtual. Estes pontos finais também afetam a forma como pode aceder às inputs e saídas a partir do histórico de execuções das suas aplicações lógicas.

> [!IMPORTANT]
> Só pode selecionar o ponto final de acesso durante a criação do ISE e não pode alterar esta opção mais tarde.

* **Interna**: Os pontos finais privados permitem chamadas para aplicações lógicas no seu ISE onde você pode ver e aceder a inputs e saídas a partir de aplicações lógicas corre o histórico *apenas a partir de dentro da sua rede virtual*. Certifique-se de que tem conectividade de rede entre os pontos finais privados e o computador de onde pretende aceder ao histórico de execuções. Por exemplo, o seu computador cliente pode existir dentro da rede virtual do ISE ou dentro de uma rede virtual que esteja ligada à rede virtual do ISE, por exemplo, através de um peering ou de uma rede privada virtual.

* **External**: Os pontos finais do público permitem chamadas para aplicações lógicas no seu ISE onde pode ver e aceder a inputs e saídas de aplicações lógicas que executam o histórico *de aplicações de fora da sua rede virtual.* Se utilizar grupos de segurança de rede (NSGs), certifique-se de que são configurados com regras de entrada para permitir o acesso às inputs e saídas do histórico de execução. Para mais informações, consulte [Permitir o acesso ao ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

Para determinar se o seu ISE utiliza um ponto final de acesso interno ou externo, no menu do ISE, em **Definições,** selecione **Propriedades,** e encontre a propriedade de ponto final de **acesso:**

![Encontre o ponto final de acesso ise](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com o ISE

Pode utilizar contas de integração com aplicações lógicas dentro de um ambiente de serviço de integração (ISE). No entanto, essas contas de integração devem usar o *mesmo ISE* que as aplicações lógicas ligadas. As aplicações lógicas num ISE só podem fazer referência às contas de integração que se encontram no mesmo ISE. Ao criar uma conta de integração, pode selecionar o seu ISE como local para a sua conta de integração. Para saber como funcionam os preços e a faturação para contas de integração com um ISE, consulte o modelo de preços das [Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Para preços, consulte [preços de Apps Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/)

## <a name="next-steps"></a>Passos seguintes

* [Ligue-se a redes virtuais Azure a partir de Aplicações Lógicas Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Saiba mais sobre [a Rede Virtual Azure](../virtual-network/virtual-networks-overview.md)
* Conheça a [integração de redes virtuais para serviços Azure](../virtual-network/virtual-network-for-azure-services.md)
