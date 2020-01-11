---
title: Acesso às redes virtuais do Azure
description: Visão geral sobre como os ambientes de serviço de integração (ISEs) ajudam os aplicativos lógicos a acessar redes virtuais do Azure (VNETs)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 12/16/2019
ms.openlocfilehash: d8d57c15fffaa6a9d18ad3c83716f99247512c15
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860759"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acesso aos recursos de rede virtual do Azure de aplicativos lógicos do Azure usando ambientes de serviço de integração (ISEs)

Às vezes, seus aplicativos lógicos e contas de integração precisam acessar recursos protegidos, como VMs (máquinas virtuais) e outros sistemas ou serviços, que estão dentro de uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Para configurar esse acesso, você pode [criar um *ambiente do serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , no qual você pode executar seus aplicativos lógicos e criar suas contas de integração.

Quando você cria um ISE, o Azure *injeta* esse ISE em sua rede virtual do Azure, que implanta uma instância privada e isolada do serviço de aplicativos lógicos em sua rede virtual do Azure. Essa instância privada usa recursos dedicados, como armazenamento, e é executada separadamente do serviço de aplicativos lógicos de vários locatários público, "global". Separar sua instância privada isolada e a instância global pública também ajuda a reduzir o impacto que outros locatários do Azure podem ter no desempenho de seus aplicativos, que também é conhecido como [efeito "vizinhos com ruído"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Um ISE também fornece seus próprios endereços IP estáticos. Esses endereços IP são separados dos endereços IP estáticos que são compartilhados pelos aplicativos lógicos no serviço público e de vários locatários.

Depois de criar o ISE, quando você for criar seu aplicativo lógico ou conta de integração, poderá selecionar o ISE como o seu aplicativo lógico ou o local da conta de integração:

![Selecione o ambiente do serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Seu aplicativo lógico agora pode acessar diretamente os sistemas que estão dentro ou conectados à sua rede virtual usando qualquer um desses itens, que são executados dentro do mesmo ISE que seu aplicativo lógico:

* Um conector com rótulo do **ISE**para esse sistema
* Um gatilho ou ação interna de rótulo de **núcleo**, como o gatilho ou a ação de http
* Um conector personalizado

Esta visão geral descreve mais detalhes sobre como um ISE fornece aos seus aplicativos lógicos e contas de integração acesso direto à sua rede virtual do Azure e compara as diferenças entre um ISE e o serviço de aplicativos lógicos globais.

> [!IMPORTANT]
> Os aplicativos lógicos, os gatilhos internos, as ações internas e os conectores executados no ISE usam um plano de preços diferente do plano de preços baseado em consumo. Para saber como o preço e a cobrança funcionam para o ISEs, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter taxas de preços, consulte [preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md).
>
> O ISE também aumentou os limites de duração da execução, retenção de armazenamento, taxa de transferência, tempos limite de resposta e solicitação HTTP, tamanhos de mensagem e solicitações de conector personalizado. 
> Para obter mais informações, consulte [limites e configuração para aplicativos lógicos do Azure](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolado versus global

Ao criar um ambiente de serviço integrado (ISE) no Azure, você pode selecionar a rede virtual do Azure na qual deseja *injetar* o ISE. Em seguida, o Azure injeta ou implanta uma instância privada do serviço de aplicativos lógicos em sua rede virtual. Essa ação cria um ambiente isolado no qual você pode criar e executar seus aplicativos lógicos em recursos dedicados. Ao criar seu aplicativo lógico, você seleciona o ISE como o local do seu aplicativo, o que dá ao seu aplicativo lógico acesso direto à sua rede virtual e aos recursos nessa rede.

Os aplicativos lógicos em um ISE fornecem as mesmas experiências de usuário e recursos semelhantes que o serviço de aplicativos lógicos globais públicos. Você pode usar todos os mesmos gatilhos internos, ações e conectores gerenciados que estão disponíveis no serviço de aplicativos lógicos globais. Alguns conectores gerenciados oferecem versões adicionais do ISE. A diferença existe no local em que elas são executadas e os rótulos que eles exibem no designer de aplicativo lógico quando você trabalha em um ISE.

![Conectores com e sem rótulos em um ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-built-in-actions-triggers-managed-connectors.png)

* Gatilhos e ações internos exibem o rótulo **principal** e sempre são executados no mesmo ISE que seu aplicativo lógico. Os conectores gerenciados que exibem o rótulo do **ISE** também são executados no mesmo ISE que seu aplicativo lógico.

  Por exemplo, aqui estão alguns conectores que oferecem versões do ISE:

  * Armazenamento de BLOBs, armazenamento de arquivos e armazenamento de tabelas do Azure
  * Filas do Azure, barramento de serviço do Azure, hubs de eventos do Azure e IBM MQ
  * FTP e SFTP-SSH
  * SQL Server, SQL Data Warehouse do Azure, Azure Cosmos DB
  * AS2, X12 e EDIFACT

* Os conectores gerenciados que não exibem rótulos adicionais sempre são executados no serviço de aplicativos lógicos globais públicos, mas você ainda pode usar esses conectores em um aplicativo lógico baseado em ISE.

Um ISE também fornece limites maiores para duração da execução, retenção de armazenamento, taxa de transferência, tempos limite de resposta e solicitação HTTP, tamanhos de mensagem e solicitações de conector personalizado. Para obter mais informações, consulte [limites e configuração para aplicativos lógicos do Azure](logic-apps-limits-and-config.md).

<a name="ise-level"></a>

## <a name="ise-skus"></a>SKUs do ISE

Ao criar o ISE, você pode selecionar a SKU do desenvolvedor ou a SKU Premium. Estas são as diferenças entre estas SKUs:

* **Programador**

  Fornece um ISE de menor custo que você pode usar para experimentação, desenvolvimento e teste, mas não para teste de produção ou de desempenho. A SKU do desenvolvedor inclui gatilhos e ações internos, conectores padrão, conectores corporativos e uma única conta de integração de [camada gratuita](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) para um preço mensal fixo. No entanto, esse SKU não inclui nenhum SLA (contrato de nível de serviço), opções para escalar verticalmente a capacidade ou redundância durante a reciclagem, o que significa que você pode enfrentar atrasos ou tempo de inatividade.

* **Premium**

  Fornece um ISE que você pode usar para produção e inclui suporte a SLA, gatilhos e ações internos, conectores padrão, conectores corporativos, uma única conta de integração de [camada Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , opções para escalar verticalmente a capacidade e redundância durante a reciclagem de um preço mensal fixo.

> [!IMPORTANT]
> A opção SKU está disponível apenas na criação do ISE e não pode ser alterada posteriormente.

Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). Para saber como o preço e a cobrança funcionam para o ISEs, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Acesso ao ponto de extremidade do ISE

Ao criar o ISE, você pode optar por usar pontos de extremidade de acesso internos ou externos. Sua seleção determina se os gatilhos de solicitação ou webhook em aplicativos lógicos no ISE podem receber chamadas de fora de sua rede virtual.

Esses pontos de extremidade também afetam a maneira como você pode acessar entradas e saídas no histórico de execução de seus aplicativos lógicos.

* **Interno**: pontos de extremidade privados que permitem chamadas para aplicativos lógicos no ISE, onde você pode exibir e acessar as entradas e saídas dos seus aplicativos lógicos no histórico *de execução somente de dentro de sua rede virtual*

* **External**: pontos de extremidade públicos que permitem chamadas para aplicativos lógicos no ISE, onde você pode exibir e acessar as entradas e saídas dos seus aplicativos lógicos no histórico *de execução de fora da sua rede virtual*. Se você usar NSGs (grupos de segurança de rede), verifique se eles estão configurados com regras de entrada para permitir o acesso às entradas e saídas do histórico de execuções. Para obter mais informações, consulte [habilitar o acesso para ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> A opção de ponto de extremidade de acesso só está disponível na criação do ISE e não pode ser alterada posteriormente.

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>Acesso a fontes de dados locais

Para sistemas locais conectados a uma rede virtual do Azure, insira um ISE nessa rede para que seus aplicativos lógicos possam acessar diretamente esses sistemas usando qualquer um destes itens:

* Ação HTTP

* ISE-conector rotulado para esse sistema

  > [!NOTE]
  > Para usar a autenticação do Windows com o conector de SQL Server em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), use a versão sem ISE do conector com o [Gateway de dados local](../logic-apps/logic-apps-gateway-install.md). A versão com rótulo de ISE não dá suporte à autenticação do Windows.

* Conector personalizado

  * Se você tiver conectores personalizados que exigem o gateway de dados local e tiver criado esses conectores fora de um ISE, os aplicativos lógicos em um ISE também poderão usar esses conectores.
  
  * Os conectores personalizados criados em um ISE não funcionam com o gateway de dados local. No entanto, esses conectores podem acessar diretamente fontes de dados locais que estão conectadas à rede virtual que hospeda o ISE. Portanto, os aplicativos lógicos em um ISE provavelmente não precisam do gateway de dados ao se comunicar com esses recursos.

Para sistemas locais que não estão conectados a uma rede virtual ou que não têm conectores do ISE, você deve primeiro [Configurar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md) antes que seus aplicativos lógicos possam se conectar a esses sistemas.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com ISE

Você pode usar contas de integração com aplicativos lógicos dentro de um ambiente do serviço de integração (ISE). No entanto, essas contas de integração devem usar o *mesmo ISE* que os aplicativos lógicos vinculados. Os aplicativos lógicos em um ISE podem referenciar somente as contas de integração que estão no mesmo ISE. Ao criar uma conta de integração, você pode selecionar o ISE como o local para sua conta de integração. Para saber como o preço e a cobrança funcionam para contas de integração com um ISE, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Passos seguintes

* [Conectar-se a redes virtuais do Azure de aplicativos lógicos isolados](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Adicionar artefatos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerenciar ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md)
* Saiba mais sobre a [rede virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre [a integração de rede virtual para serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
