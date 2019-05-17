---
title: Acesso a redes virtuais do Azure a partir do Azure Logic Apps com ambientes de serviço de integração (ISEs)
description: Esta descrição geral descreve como ambientes de serviço de integração (ISEs) ajudam as aplicações lógicas, redes virtuais do Azure (VNETs) de acesso
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 1ef8c8eec3865f2a6e363e7da1dbda9504b81c05
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546404"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acesso a recursos de rede Virtual do Azure a partir do Azure Logic Apps, utilizando ambientes de serviço de integração (ISEs)

Às vezes, o logic apps e as contas de integração precisam de acesso a recursos protegidos, como as máquinas virtuais (VMs) e outros sistemas ou serviços, que estão dentro de um [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Para configurar este acesso, pode [crie uma *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) onde pode executar as aplicações lógicas e criar a sua integração de contas.

Quando cria um ISE, o Azure implementa uma instância privada e isolada do serviço de aplicações lógicas em sua rede virtual do Azure. Esta instância privada utiliza recursos dedicados, como o armazenamento e é executado separadamente do serviço de aplicações lógicas "global" público. Separar a sua instância privada isolada e a instância global pública também ajuda a reduzir o impacto que outros inquilinos do Azure podem ter sobre o desempenho das suas aplicações, que também é conhecido como o [efeito de "vizinhos ruidosos"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Depois de criar seu ISE, quando vai para criar a sua conta de integração ou da aplicação de lógica, pode selecionar o seu ISE como localização de lógica aplicação ou a integração da sua conta:

![Selecione o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

A aplicação lógica agora pode aceder diretamente a sistemas que estão dentro ou ligado à sua rede virtual, utilizando qualquer um desses itens:

* Uma **ISE**-rotulado como conector para esse sistema, como o SQL Server
* R **Core**-o nome interno acionador ou ação, tal como o acionador HTTP ou a ação
* Um conector personalizado

Esta descrição geral descreve mais detalhes sobre como a um ISE fornece as logic apps e integração de contas de acesso direto à rede virtual do Azure e compara as diferenças entre uma ISE e o serviço de aplicações lógicas global.

> [!NOTE]
> Aplicações lógicas, acionadores incorporados, ações incorporadas e conectores que executam no seu uso ISE um plano de preços diferente do plano de preços baseado no consumo. Para obter mais informações, consulte [preços de aplicações lógicas](../logic-apps/logic-apps-pricing.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Versus global em ambiente isolado

Quando cria um ambiente de serviço integrado (ISE) no Azure, pode selecionar a rede virtual do Azure onde pretende *injetar* seu ISE. Azure, em seguida, injeta ou implementa uma instância privada do serviço de aplicações lógicas na sua rede virtual. Esta ação cria um ambiente isolado, onde pode criar e executar as aplicações lógicas em recursos dedicados. Quando criar a sua aplicação lógica, selecione seu ISE como a localização da sua aplicação, o que lhe dá o seu acesso direto da aplicação lógica à sua rede virtual e os recursos dessa rede.

Aplicações lógicas num ISE fornecem as mesmas experiências de usuário e recursos semelhantes que o serviço de aplicações lógicas global. Não só pode utilizar o mesmo acionadores incorporados, ações incorporadas e conectores do serviço de aplicações lógicas global, mas também pode utilizar conectores específicos do ISE. Por exemplo, eis alguns conectores padrão que oferecem versões que são executados num ISE:

* Armazenamento de Blobs do Azure, armazenamento de ficheiros e armazenamento de tabelas
* As filas do Azure, o Service bus do Azure, o Hubs de eventos do Azure e o IBM MQ
* FTP e SFTP-SSH
* SQL Server, o SQL Data Warehouse, Azure Cosmos DB
* AS2, X12 e EDIFACT

A diferença entre os conectores ISE e não ISE é nos locais onde executar os acionadores e ações:

* No seu ISE, incorporados acionadores e ações, como HTTP, são sempre executam no ISE do mesmo, como a aplicação lógica e apresentar os **Core** etiqueta.

  ![Selecione "Principal" de acionadores incorporados e ações](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Os conectores que são executados num ISE publicamente tenham alojado versões disponíveis no serviço de aplicações lógicas global. Para os conectores que oferecem duas versões, conectores com o **ISE** sempre rotular execução no ISE do mesmo que a sua aplicação lógica. Conectores sem o **ISE** etiqueta em execução no serviço de aplicações lógicas global.

  ![Selecionar conectores do ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

### <a name="access-to-on-premises-data-sources"></a>Acesso às origens de dados no local

Para sistemas no local que estão ligados a uma rede virtual do Azure, injete um ISE nessa rede, para que as logic apps podem acessar diretamente desses sistemas, utilizando qualquer um destes itens:

* Conector de versão do ISE para esse sistema, por exemplo, o SQL Server
  
* Ação de HTTP
  
* Conector personalizado

  * Se tiver de conectores personalizados que exigem o gateway de dados no local e criou esses conectores fora um ISE, aplicações lógicas num ISE também podem utilizar os conectores.
  
  * Conectores personalizados criados num ISE não funcionam com o gateway de dados no local. No entanto, estes conectores podem aceder diretamente a origens de dados no local que estão ligadas à rede virtual que aloja o ISE. Então, aplicações lógicas num ISE é muito provável que não precisam o gateway de dados ao se comunicar com esses recursos.

Para sistemas no local que não estão ligados a uma rede virtual ou não tem conectores de versão do ISE, primeiro tem [configurar o gateway de dados no local](../logic-apps/logic-apps-gateway-install.md) antes de sua lógica de aplicações podem ligar a esses sistemas.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com o ISE

Pode utilizar contas de integração com logic apps dentro de um ambiente de serviço de integração (ISE). No entanto, essas contas de integração tem de utilizar o *ISE mesmo* como as aplicações de lógica ligada. Aplicações lógicas num ISE podem referenciar apenas as contas de integração que estão no ISE do mesmo. Quando cria uma conta de integração, pode selecionar o ISE como a localização para a sua conta de integração.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [ligar às redes virtuais do Azure a partir de aplicações lógicas isoladas](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Saiba mais sobre [rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre [integração da rede virtual para serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
