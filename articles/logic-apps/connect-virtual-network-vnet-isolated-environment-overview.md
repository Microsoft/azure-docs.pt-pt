---
title: Aceder às redes virtuais do Azure
description: Visão geral sobre como ambientes de serviços de integração (ISEs) ajudam aplicações lógicas a aceder a redes virtuais Azure (VNETs)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: b149757ccfc41587aa3ea6c5d18717fdecaba656
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84656643"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acesso aos recursos da Rede Virtual Azure a partir de Azure Logic Apps utilizando ambientes de serviços de integração (ISEs)

Por vezes, as suas aplicações lógicas precisam de acesso a recursos seguros, como máquinas virtuais (VMs) e outros sistemas ou serviços, que estão dentro de uma [rede virtual Azure.](../virtual-network/virtual-networks-overview.md) Para configurar este acesso, pode [criar um ambiente de serviço de *integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). Um ISE é uma instância dedicada do serviço Logic Apps que utiliza recursos dedicados e funciona separadamente do serviço "global" multi-inquilino Logic Apps.

Executar aplicações lógicas em seu próprio caso dedicado separado ajuda a reduzir o impacto que outros inquilinos do Azure podem ter no desempenho das suas apps, também conhecido como o [efeito "vizinhos barulhentos".](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) Um ISE também fornece estes benefícios:

* Os seus próprios endereços IP estáticos, separados dos endereços IP estáticos que são partilhados pelas aplicações lógicas no serviço multi-inquilino. Também pode configurar um único endereço IP de saída público, estático e previsível para comunicar com os sistemas de destino. Desta forma, não é preciso configurar aberturas de firewall adicionais nesses sistemas de destino para cada ISE.

* Limites acrescidos na duração de execução, retenção de armazenamento, produção, pedidos HTTP e intervalos de resposta, tamanhos de mensagem e pedidos de conector personalizado. Para obter mais informações, consulte [Limites e configuração para Aplicações Lógicas Azure](logic-apps-limits-and-config.md).

> [!NOTE]
> Algumas redes virtuais Azure utilizam pontos finais privados[(Azure Private Link](../private-link/private-link-overview.md)) para fornecer acesso a serviços Azure PaaS, tais como Azure Storage, Azure Cosmos DB, ou Azure SQL Database, serviços de parceiros ou serviços de clientes que estão hospedados no Azure. Se as suas aplicações lógicas precisam de acesso a redes virtuais que usam pontos finais privados, tem de criar, implementar e executar essas aplicações lógicas dentro de um ISE.

Quando cria um ISE, o Azure *injeta* ou implementa esse ISE na sua rede virtual Azure. Em seguida, pode utilizar este ISE como localização para as aplicações lógicas e contas de integração que precisam de acesso.

![Selecione ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

As aplicações lógicas podem aceder a recursos que estão dentro ou ligados à sua rede virtual utilizando estes itens, que funcionam no mesmo ISE que as suas aplicações lógicas:

* Um gatilho ou ação incorporado com rótulo **CORE,** como o gatilho HTTP ou ação
* Um conector com a etiqueta **ISE**para esse sistema ou serviço
* Um conector personalizado

Também pode utilizar conectores que não tenham o rótulo **CORE** ou **ISE** com as aplicações lógicas no seu ISE. Estes conectores funcionam no serviço multi-inquilinos Logic Apps. Para mais informações, consulte estas secções:

* [Dedicado contra multi-inquilino](#difference)
* [Conecte-se a partir de um ambiente de serviço de integração](../connectors/apis-list.md#integration-service-environment)
* [Conectores ISE](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Aplicações lógicas, gatilhos incorporados, ações incorporadas e conectores que funcionam no seu ISE usam um plano de preços que difere do plano de preços baseado no consumo. Para obter mais informações, consulte [o modelo de preços de Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Para obter detalhes sobre preços, consulte [os preços das Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md)

Esta visão geral descreve mais informações sobre como um ISE dá às suas aplicações lógicas acesso direto à sua rede virtual Azure e compara as diferenças entre um ISE e o serviço multi-inquilinos Logic Apps.

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Dedicado contra multi-inquilino

Quando cria e executa aplicações lógicas num ISE, obtém as mesmas experiências do utilizador e capacidades semelhantes às do serviço Multi-Tenant Logic Apps. Você pode usar todos os mesmos gatilhos incorporados, ações e conectores geridos que estão disponíveis no serviço multi-inquilinos Logic Apps. Alguns conectores geridos oferecem versões ISE adicionais. A diferença entre conectores ISE e conectores não ISE existe no local onde funcionam e nas etiquetas que têm no Logic App Designer quando trabalha dentro de um ISE.

![Conectores com e sem etiquetas num ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Os gatilhos e as ações incorporados exibem a etiqueta **CORE.** Funcionam sempre no mesmo ISE que a sua aplicação lógica. Os conectores geridos que exibem a etiqueta **ISE** também funcionam na mesma ise que a sua aplicação lógica.

  Por exemplo, aqui estão alguns conectores que oferecem versões ISE:

  * Armazenamento de blob Azure, armazenamento de arquivos e armazenamento de mesa
  * Azure Queues, Azure Service Bus, Azure Event Hubs e IBM MQ
  * FTP e SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 e EDIFACT

* Conectores geridos que não exibem etiquetas adicionais funcionam sempre no serviço multi-inquilinos Logic Apps, mas ainda pode usar estes conectores numa aplicação lógica hospedada pelo ISE.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Acesso aos sistemas no local

Para aceder a sistemas ou fontes de dados no local que estejam ligadas a uma rede virtual Azure, as aplicações lógicas num ISE podem utilizar estes itens:

* Ação HTTP

* Conector com rótulo ISE para este sistema

  > [!NOTE]
  > Para utilizar a autenticação do Windows com o conector SQL Server num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)utilize a versão não ISE do conector com o [gateway de dados no local](../logic-apps/logic-apps-gateway-install.md). A versão com a marca ISE não suporta a autenticação do Windows.

* Conector personalizado

  * Se tiver conectores personalizados que requerem o gateway de dados no local, e criou esses conectores fora de um ISE, as aplicações lógicas num ISE também podem usar esses conectores.

  * Os conectores personalizados criados num ISE não funcionam com o portal de dados no local. No entanto, estes conectores podem aceder diretamente a fontes de dados no local que estão ligadas à rede virtual que hospeda o ISE. Assim, as aplicações lógicas num ISE provavelmente não precisam da porta de dados quando comunicam com esses recursos.

Para sistemas no local que não estejam ligados a uma rede virtual ou que não tenham conectores com a marca ISE, deve primeiro [configurar o portal de dados no local](../logic-apps/logic-apps-gateway-install.md) antes que as suas aplicações lógicas possam ligar-se a esses sistemas.

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKUs

Quando criar o seu ISE, pode selecionar o Developer SKU ou o Premium SKU. Aqui estão as diferenças entre estes SKUs:

* **Programador**

  Fornece um ISE de baixo custo que pode usar para experimentação, desenvolvimento e testes, mas não para testes de produção ou desempenho. O Developer SKU inclui gatilhos e ações incorporados, conectores Standard, conectores Enterprise e uma única conta de integração [de nível livre](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) por um preço mensal fixo. No entanto, este SKU não inclui qualquer acordo de nível de serviço (SLA), opções para aumentar a capacidade, ou redundância durante a reciclagem, o que significa que você pode experimentar atrasos ou tempo de inatividade.

* **Premium**

  Fornece um ISE que pode usar para produção e inclui suporte SLA, gatilhos e ações incorporados, conectores standard, conectores Enterprise, uma única conta de integração [de nível Standard,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) opções para aumentar a capacidade e redundância durante a reciclagem por um preço mensal fixo.

> [!IMPORTANT]
> A opção SKU está disponível apenas na criação do ISE e não pode ser alterada mais tarde.

Para taxas de preços, consulte [os preços das Aplicações Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/) Para aprender como os preços e a faturação funcionam para as ISEs, consulte o [modelo de preços de Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Acesso ao ponto final do ISE

Ao criar o ise, pode optar por utilizar os pontos finais de acesso interno ou externo. A sua seleção determina se o pedido ou webhook dispara em aplicações lógicas no seu ISE pode receber chamadas de fora da sua rede virtual. Estes pontos finais também afetam a forma como pode aceder às entradas e saídas do histórico de execuções das suas apps lógicas.

> [!IMPORTANT]
> Pode selecionar o ponto final de acesso apenas durante a criação do ISE e não pode alterar esta opção mais tarde.

* **Interna**: Os pontos finais privados permitem chamadas para aplicações lógicas no seu ISE onde pode ver e aceder a entradas e saídas a partir de aplicações lógicas executa o histórico *apenas a partir de dentro da sua rede virtual.* Certifique-se de que tem conectividade de rede entre os pontos finais privados e o computador de onde pretende aceder ao histórico. Por exemplo, o seu computador cliente pode existir dentro da rede virtual do ISE ou dentro de uma rede virtual que está ligada à rede virtual do ISE, por exemplo, através de um espreitamento ou de uma rede privada virtual.

* **Externo**: Os pontos finais públicos permitem chamadas para aplicações lógicas no seu ISE onde pode ver e aceder a entradas e saídas a partir de aplicações lógicas executa o histórico *de fora da sua rede virtual.* Se utilizar grupos de segurança de rede (NSGs), certifique-se de que estão configurados com regras de entrada para permitir o acesso às entradas e saídas do histórico de execução. Para obter mais informações, consulte [Ativar o acesso ao ISE.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

Para determinar se o seu ISE utiliza um ponto final de acesso interno ou externo, no menu do ise, em **Definições**, selecione **Propriedades,** e encontre a propriedade **do ponto final access:**

![Localizar o ponto final de acesso do ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com o ISE

Pode utilizar contas de integração com aplicações lógicas dentro de um ambiente de serviço de integração (ISE). No entanto, essas contas de integração devem utilizar o *mesmo ISE* que as aplicações lógicas ligadas. As aplicações lógicas num ISE só podem referenciar as contas de integração que estão no mesmo ISE. Quando criar uma conta de integração, pode selecionar o ise como o local para a sua conta de integração. Para aprender como os preços e faturação funcionam para contas de integração com um ISE, consulte o [modelo de preços de Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Para taxas de preços, consulte [os preços das Aplicações Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/)

## <a name="next-steps"></a>Próximos passos

* [Ligue-se a redes virtuais Azure a partir de Apps Azure Logic](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Saiba mais sobre [a Rede Virtual Azure](../virtual-network/virtual-networks-overview.md)
* Conheça a [integração de redes virtuais para serviços Azure](../virtual-network/virtual-network-for-azure-services.md)
