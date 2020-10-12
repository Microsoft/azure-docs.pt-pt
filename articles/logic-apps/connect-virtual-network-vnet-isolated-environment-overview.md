---
title: Aceder às redes virtuais do Azure
description: Visão geral sobre como ambientes de serviços de integração (ISEs) ajudam aplicações lógicas a aceder a redes virtuais Azure (VNETs)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 07/05/2020
ms.openlocfilehash: 86d647a79b7babc2780cb0db904e689f3916673f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89500390"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acesso aos recursos da Rede Virtual Azure a partir de Azure Logic Apps utilizando ambientes de serviços de integração (ISEs)

Por vezes, as suas aplicações lógicas precisam de acesso a recursos seguros, como máquinas virtuais (VMs) e outros sistemas ou serviços, que estão dentro ou ligados a uma [rede virtual Azure.](../virtual-network/virtual-networks-overview.md) Para configurar este acesso, pode [criar um ambiente de serviço de *integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). Um ISE é uma instância do serviço Logic Apps que utiliza recursos dedicados e funciona separadamente do serviço "global" multi-inquilino Logic Apps.

Por exemplo, alguma rede virtual Azure utiliza pontos finais privados, que pode configurar através do [Azure Private Link,](../private-link/private-link-overview.md)para fornecer acesso aos serviços Azure PaaS, tais como Azure Storage, Azure Cosmos DB, ou Azure SQL Database, serviços de parceiros ou serviços ao cliente que estão hospedados no Azure. Se as suas aplicações lógicas precisam de acesso a redes virtuais que usam pontos finais privados, tem de criar, implementar e executar essas aplicações lógicas dentro de um ISE.

Quando cria um ISE, o Azure *injeta* ou implementa esse ISE na sua rede virtual Azure. Em seguida, pode utilizar este ISE como localização para as aplicações lógicas e contas de integração que precisam de acesso.

![Selecione ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Esta visão geral fornece mais informações sobre [o porquê de você querer usar um ISE](#benefits), as [diferenças entre o serviço de Aplicações Lógicas dedicadas e multi-arrendantes](#difference), e como você pode aceder diretamente a recursos que estão dentro ou conectado a sua rede virtual Azure.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Por que usar um ISE?

Executar aplicações lógicas em seu próprio caso dedicado separado ajuda a reduzir o impacto que outros inquilinos do Azure podem ter no desempenho das suas apps, também conhecido como o [efeito "vizinhos barulhentos".](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) Um ISE também fornece estes benefícios:

* Acesso direto a recursos que estão dentro ou ligados à sua rede virtual

  As aplicações lógicas que cria e executa num ISE podem utilizar [conectores especificamente concebidos para o seu ISE.](../connectors/apis-list.md#ise-connectors) Se existir um conector ISE para um sistema no local ou fonte de dados, pode [ligar-se](../logic-apps/logic-apps-gateway-connection.md)diretamente sem ter de utilizar o gateway de dados no local . Para mais informações, consulte [Dedicado contra multi-inquilino](#difference) e [Acesso aos sistemas de acesso aos sistemas no local](#on-premises) mais tarde neste tópico.

* Acesso continuado a recursos que estão fora ou não ligados à sua rede virtual

  As aplicações lógicas que cria e executa num ISE ainda podem usar conectores que funcionam no serviço multi-inquilinos Logic Apps quando um conector específico do ISE não está disponível. Para mais informações, consulte [Dedicado contra multi-inquilino.](#difference)

* Os seus próprios endereços IP estáticos, separados dos endereços IP estáticos que são partilhados pelas aplicações lógicas no serviço multi-inquilino. Também pode configurar um único endereço IP de saída público, estático e previsível para comunicar com os sistemas de destino. Desta forma, não é preciso configurar aberturas de firewall adicionais nesses sistemas de destino para cada ISE.

* Limites acrescidos na duração de execução, retenção de armazenamento, produção, pedidos HTTP e intervalos de resposta, tamanhos de mensagem e pedidos de conector personalizado. Para obter mais informações, consulte [Limites e configuração para Aplicações Lógicas Azure](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Dedicado contra multi-inquilino

Quando cria e executa aplicações lógicas num ISE, obtém as mesmas experiências do utilizador e capacidades semelhantes às do serviço Multi-Tenant Logic Apps. Você pode usar todos os mesmos gatilhos incorporados, ações e conectores geridos que estão disponíveis no serviço multi-inquilinos Logic Apps. Alguns conectores geridos oferecem versões ISE adicionais. A diferença entre conectores ISE e conectores não ISE existe no local onde funcionam e nas etiquetas que têm no Logic App Designer quando trabalha dentro de um ISE.

![Conectores com e sem etiquetas num ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Os gatilhos e ações incorporados, tais como HTTP, exibem a etiqueta **CORE** e funcionam na mesma ISE que a sua aplicação lógica.

* Os conectores geridos que exibem a etiqueta **ISE** são especialmente concebidos para ISEs e *funcionam sempre na mesma ISE que a sua aplicação lógica.* Por exemplo, aqui estão [alguns conectores que oferecem versões ISE](../connectors/apis-list.md#ise-connectors):<p>

  * Armazenamento de blob Azure, armazenamento de arquivos e armazenamento de mesa
  * Ônibus de serviço Azure, Azure Queues, Azure Event Hubs
  * Azure Automation, Azure Key Vault, Azure Event Grid e Azure Monitor Logs
  * FTP, SFTP-SSH, Sistema de Ficheiros e SMTP
  * SAP, IBM MQ, IBM DB2 e IBM 3270
  * SQL Server, Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 e EDIFACT

  Com raras exceções, se um conector ISE estiver disponível para um sistema no local ou fonte de dados, pode ligar-se diretamente sem utilizar o [gateway de dados no local.](../logic-apps/logic-apps-gateway-connection.md) Para mais informações, consulte [o Acesso aos sistemas no local](#on-premises) mais tarde neste tópico.

* Os conectores geridos que não exibem a etiqueta **ISE** continuam a funcionar para aplicações lógicas dentro de um ISE. Estes conectores *funcionam sempre no serviço multi-inquilinos Logic Apps*, não no ISE.

* Os conectores personalizados que cria *fora de um ISE*– quer necessitem ou não do [portal de dados no local](../logic-apps/logic-apps-gateway-connection.md), continuam a trabalhar para aplicações lógicas dentro de um ISE. No entanto, os conectores personalizados que cria *dentro de um ISE* não funcionarão com o portal de dados no local. Para obter mais informações, consulte [o Acesso aos sistemas de acesso ao local.](#on-premises)

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Acesso aos sistemas no local

As aplicações lógicas que funcionam dentro de um ISE podem aceder diretamente a sistemas e fontes de dados no local que estão dentro ou ligados a uma rede virtual Azure utilizando estes itens:<p>

* O gatilho ou ação HTTP, que exibe a etiqueta **CORE**

* O conector **ISE,** se disponível, para um sistema no local ou fonte de dados

  Se um conector ISE estiver disponível, pode aceder diretamente ao sistema ou fonte de dados sem o [gateway de dados no local.](../logic-apps/logic-apps-gateway-connection.md) No entanto, se necessitar de aceder ao SQL Server a partir de um ISE e utilizar a autenticação do Windows, deve utilizar a versão não ISE do conector e o portal de dados no local. A versão ISE do conector não suporta a autenticação do Windows. Para obter mais informações, consulte [os conectores ISE](../connectors/apis-list.md#ise-connectors) e [Ligue-se a partir de um ambiente de serviço de integração.](../connectors/apis-list.md#integration-service-environment)

* Um conector personalizado

  * Os conectores personalizados que cria *fora de um ISE*– quer necessitem ou não do [portal de dados no local](../logic-apps/logic-apps-gateway-connection.md), continuam a trabalhar para aplicações lógicas dentro de um ISE.

  * Os conectores personalizados que cria *dentro de um ISE* não funcionam com o portal de dados no local. No entanto, estes conectores podem aceder diretamente aos sistemas e fontes de dados no local que se encontrem dentro ou ligados à rede virtual que acolhe o seu ISE. Assim, as aplicações lógicas que estão dentro de um ISE geralmente não precisam da porta de dados ao aceder a esses recursos.

Para aceder a sistemas e fontes de dados no local que não possuam conectores ISE, estão fora da sua rede virtual ou não estão ligados à sua rede virtual, ainda tem de utilizar o portal de dados no local. As aplicações lógicas dentro de um ISE podem continuar a usar conectores que não têm a etiqueta **CORE** ou **ISE.** Esses conectores funcionam apenas no serviço multi-inquilinos Logic Apps, em vez de no seu ISE. 

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

* **Interna**: Os pontos finais privados permitem chamadas para aplicações lógicas no seu ISE onde pode ver e aceder a entradas e saídas a partir de aplicações lógicas executa o histórico *apenas a partir de dentro da sua rede virtual.*

  > [!IMPORTANT]
  > Certifique-se de que tem conectividade de rede entre os pontos finais privados e o computador de onde pretende aceder ao histórico de execução. Caso contrário, quando tenta ver o histórico de execução da sua aplicação lógica, obtém-se um erro que diz "Erro inesperado. Falhou em conseguir".
  >
  > ![Erro de ação do Azure Storage resultante da incapacidade de enviar tráfego através de firewall](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Por exemplo, o seu computador cliente pode existir dentro da rede virtual do ISE ou dentro de uma rede virtual que está ligada à rede virtual do ISE através de um esprevamento ou de uma rede privada virtual. 

* **Externo**: Os pontos finais públicos permitem chamadas para aplicações lógicas no seu ISE onde pode ver e aceder a entradas e saídas a partir de aplicações lógicas executa o histórico *de fora da sua rede virtual.* Se utilizar grupos de segurança de rede (NSGs), certifique-se de que estão configurados com regras de entrada para permitir o acesso às entradas e saídas do histórico de execução. Para obter mais informações, consulte [Ativar o acesso ao ISE.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

Para determinar se o seu ISE utiliza um ponto final de acesso interno ou externo, no menu do ise, em **Definições**, selecione **Propriedades,** e encontre a propriedade **do ponto final access:**

![Localizar o ponto final de acesso do ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modelo preços

Aplicações lógicas, gatilhos incorporados, ações incorporadas e conectores que funcionam no seu ISE usam um plano de preços fixo que difere do plano de preços baseado no consumo. Para obter mais informações, consulte [o modelo de preços de Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Para taxas de preços, consulte [os preços das Aplicações Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/)

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com o ISE

Pode utilizar contas de integração com aplicações lógicas dentro de um ambiente de serviço de integração (ISE). No entanto, essas contas de integração devem utilizar o *mesmo ISE* que as aplicações lógicas ligadas. As aplicações lógicas num ISE só podem referenciar as contas de integração que estão no mesmo ISE. Quando criar uma conta de integração, pode selecionar o ise como o local para a sua conta de integração. Para aprender como os preços e faturação funcionam para contas de integração com um ISE, consulte o [modelo de preços de Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Para taxas de preços, consulte [os preços das Aplicações Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/) Para obter informações sobre limites, consulte [os limites da conta de integração.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="next-steps"></a>Passos seguintes

* [Ligue-se a redes virtuais Azure a partir de Apps Azure Logic](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Saiba mais sobre [a Rede Virtual Azure](../virtual-network/virtual-networks-overview.md)
* Conheça a [integração de redes virtuais para serviços Azure](../virtual-network/virtual-network-for-azure-services.md)
