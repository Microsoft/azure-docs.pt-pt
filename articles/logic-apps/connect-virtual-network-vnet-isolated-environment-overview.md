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
ms.date: 02/24/2019
ms.openlocfilehash: ed172db6aaa064cfed319a4190306d91aa846c48
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806832"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acesso a recursos de rede Virtual do Azure a partir do Azure Logic Apps, utilizando ambientes de serviço de integração (ISEs)

> [!NOTE]
> Esta capacidade está em *pré-visualização pública*.

Às vezes, o logic apps e as contas de integração precisam de acesso a recursos protegidos, como máquinas virtuais (VMs) e outros sistemas ou serviços, num [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Para configurar este acesso, pode [crie uma *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) para executar as suas aplicações lógicas e as contas de integração. Quando cria um ISE, o Azure implementa uma instância privada e isolada do serviço de aplicações lógicas em sua rede virtual do Azure. Esta instância privada utiliza recursos dedicados, como o armazenamento e é executado separadamente do serviço de aplicações lógicas "global" público. Separar a sua instância privada isolada e a instância global pública também ajuda a reduzir o impacto que outros inquilinos do Azure podem ter sobre o desempenho das suas aplicações, que também é conhecido como o [efeito de "vizinhos ruidosos"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Depois de criar seu ISE, quando vai para criar a sua conta de integração ou da aplicação de lógica, pode selecionar o seu ISE como localização de lógica aplicação ou a integração da sua conta:

![Selecione o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

A aplicação lógica agora pode aceder diretamente a sistemas que estão dentro ou ligado à sua rede virtual, utilizando qualquer um desses itens:

* Um conector com versão do ISE para esse sistema, por exemplo, o SQL Server
* Um acionador incorporada ou uma ação, tal como o acionador HTTP ou a ação
* Um conector personalizado

Esta descrição geral descreve mais detalhes sobre como a um ISE fornece as logic apps e integração de contas de acesso direto à rede virtual do Azure e compara as diferenças entre uma ISE e o serviço de aplicações lógicas global.
Para sistemas no local que não estão ligados a uma rede virtual ou não tem conectores de versão do ISE, pode ligar a esses sistemas por [configurar e utilizar o gateway de dados no local](../logic-apps/logic-apps-gateway-install.md).

> [!IMPORTANT]
> Logic apps, ações incorporadas e conectores que executar no seu ISE utilizam um plano de preços diferente, não o consumo baseado no plano de preços. Para obter mais informações, consulte [preços de aplicações lógicas](../logic-apps/logic-apps-pricing.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Versus global em ambiente isolado

Quando cria um ambiente de serviço integrado (ISE) no Azure, pode selecionar a rede virtual do Azure onde pretende *injetar* seu ISE. Azure, em seguida, injeta ou implementa uma instância privada do serviço de aplicações lógicas na sua rede virtual. Esta ação cria um ambiente isolado, onde pode criar e executar as aplicações lógicas em recursos dedicados. Quando criar a sua aplicação lógica, selecione seu ISE como a localização da sua aplicação, o que lhe dá o seu acesso direto da aplicação lógica à sua rede virtual e os recursos dessa rede.

Aplicações lógicas num ISE fornecem as mesmas experiências de usuário e recursos semelhantes que o serviço de aplicações lógicas global. Não só pode usar as mesmas ações incorporadas e conectores no serviço de aplicações lógicas global, mas também pode utilizar conectores específicos do ISE. Por exemplo, eis alguns conectores padrão que oferecem versões que são executados num ISE:

* Armazenamento de Blobs do Azure, armazenamento de ficheiros e armazenamento de tabelas
* As filas do Azure, o Service bus do Azure, o Hubs de eventos do Azure e o IBM MQ
* FTP e SFTP-SSH
* SQL Server, o SQL Data Warehouse, Azure Cosmos DB
* AS2, X12 e EDIFACT

A diferença entre os conectores ISE e não ISE é nos locais onde executar os acionadores e ações:

* No seu ISE, incorporados acionadores e ações, como HTTP sempre executam no ISE do mesmo que a sua aplicação lógica.

* Para os conectores que oferecem duas versões, executa uma versão no ISE do, enquanto a outra versão é executada no serviço de aplicações lógicas global.  

  Os conectores que tenham o **ISE** sempre rotular execução no ISE do mesmo que a sua aplicação lógica. Conectores sem o **ISE** etiqueta em execução no serviço de aplicações lógicas global.

  ![Selecionar conectores do ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Os conectores que são executados num ISE também estão disponíveis no serviço de aplicações lógicas global.

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Permissões para acesso à rede virtual

Pode selecionar uma rede virtual do Azure para injetar seu ambiente, tem de configurar permissões de controlo de acesso baseado em funções (RBAC) na sua rede virtual para o serviço do Azure Logic Apps. Esta tarefa requer que atribua os **contribuinte de rede** e **contribuinte clássico** funções para o serviço do Azure Logic Apps.
Para configurar estas permissões, consulte [ligar a redes virtuais do Azure a partir de aplicações lógicas](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#vnet-access).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com o ISE

Pode utilizar contas de integração com logic apps dentro de um ambiente de serviço de integração (ISE). No entanto, essas contas de integração tem de utilizar o *ISE mesmo* como as aplicações de lógica ligada. Aplicações lógicas num ISE podem referenciar apenas as contas de integração que estão no ISE do mesmo. Quando cria uma conta de integração, pode selecionar o ISE como a localização para a sua conta de integração.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">fórum do Azure Logic Apps</a>.
* Para submeter ou votar em ideias para funcionalidades, visite o <a href="https://aka.ms/logicapps-wish" target="_blank">site de comentários dos utilizadores do Logic Apps</a>.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [ligar às redes virtuais do Azure a partir de aplicações lógicas isoladas](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Saiba mais sobre [rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre [integração da rede virtual para serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
