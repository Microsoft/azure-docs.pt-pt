---
title: Falha na atribuição do Serviço de Nuvem de Resolução de Problemas [ Falha na atribuição do Serviço de Nuvem ] Microsoft Docs
description: Resolução de problemas com a falha de atribuição quando implementar os Serviços Cloud no Azure
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 470778e5c441bb05ffc7c5e1c5ef97a6c30d3359
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395824"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Resolução de problemas com a falha de atribuição quando implementar os Serviços Cloud no Azure
## <a name="summary"></a>Resumo
Quando implementa instâncias num Serviço cloud ou adiciona novas instâncias de papel web ou de trabalhador, o Microsoft Azure atribui recursos computacionais. Pode ocasionalmente receber erros ao realizar estas operações mesmo antes de atingir os limites de subscrição do Azure. Este artigo explica as causas de algumas das falhas comuns de atribuição e sugere uma possível reparação. A informação também pode ser útil quando planeia a implantação dos seus serviços.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Antecedentes – Como funciona a atribuição
Os servidores nos datacenters do Azure são divididos em partições em clusters. Um novo pedido de atribuição de serviço sinuoso é tentado em vários clusters. Quando a primeira instância é implantada para um serviço de nuvem (em preparação ou produção), esse serviço de nuvem fica preso a um cluster. Quaisquer outras implementações para o serviço de nuvem ocorrerão no mesmo cluster. Neste artigo, vamos referir-nos a isto como "preso a um aglomerado". O diagrama 1 abaixo ilustra o caso de uma atribuição normal que é tentada em vários clusters; Diagrama 2 ilustra o caso de uma alocação que está fixada ao Cluster 2 porque é aí que está hospedado o CS_1 de Serviço de Nuvem existente.

![Diagrama de atribuição](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Por que falha na atribuição acontece
Quando um pedido de atribuição é fixado a um cluster, há uma maior chance de não encontrar recursos gratuitos, uma vez que o conjunto de recursos disponíveis está limitado a um cluster. Além disso, se o seu pedido de atribuição estiver fixado num cluster, mas o tipo de recurso que solicitou não for suportado por esse cluster, o seu pedido falhará mesmo que o cluster tenha recursos gratuitos. O diagrama 3 abaixo ilustra o caso em que uma atribuição fixada falha porque o único aglomerado de candidatos não tem recursos livres. O diagrama 4 ilustra o caso em que uma atribuição fixada falha porque o único cluster de candidatos não suporta a dimensão vm solicitada, mesmo que o cluster tenha recursos livres.

![Falha na atribuição de fixações](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Falha na atribuição de problemas para serviços na nuvem
### <a name="error-message"></a>Mensagem de Erro
Pode ver a seguinte mensagem de erro:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Problemas Comuns
Aqui estão os cenários comuns de atribuição que fazem com que um pedido de atribuição seja fixado a um único cluster.

* Implantação para A Ranhura de Encenação - Se um serviço de nuvem tiver uma implantação em qualquer uma das ranhuras, então todo o serviço de nuvem está preso a um cluster específico.  Isto significa que, se já existir uma implementação no bloco de produção, só será possível alocar uma implementação de teste nova no mesmo cluster que o bloco de produção. Se o cluster estiver perto da capacidade, o pedido pode falhar.
* Escalagem - Adicionar novos casos a um serviço de nuvem existente deve ser atribuído no mesmo cluster.  Regra geral, é possível alocar pequenos pedidos de dimensionamento, mas nem sempre. Se o cluster estiver perto da capacidade, o pedido pode falhar.
* Affinity Group - Uma nova implantação para um serviço de nuvem vazia pode ser atribuída pelo tecido em qualquer cluster daquela região, a menos que o serviço de nuvem esteja preso a um grupo de afinidade. As implantações para o mesmo grupo de afinidade serão tentadas no mesmo aglomerado. Se o cluster estiver perto da capacidade, o pedido pode falhar.
* Affinity Group vNet - Redes Virtuais Mais Antigas estavam ligadas a grupos de afinidade em vez de regiões, e os serviços em nuvem nestas Redes Virtuais seriam fixados ao cluster do grupo de afinidade. As implantações para este tipo de rede virtual serão tentadas no cluster fixado. Se o cluster estiver perto da capacidade, o pedido pode falhar.

## <a name="solutions"></a>Soluções
1. Reimplante-se para um novo serviço na nuvem - Esta solução é provável que seja mais bem sucedida, pois permite que a plataforma escolha entre todos os clusters daquela região.

   * Desloque a carga de trabalho para um novo serviço na nuvem  
   * Atualize o CNAME ou Um disco para apontar o tráfego para o novo serviço na nuvem
   * Uma vez que o tráfego zero vai para o antigo site, você pode apagar o antigo serviço de nuvem. Esta solução deve incorrer em tempo de inatividade zero.
2. Eliminar tanto as ranhuras de produção como de encenação - Esta solução preservará o nome DNS existente, mas causará tempo de inatividade na sua aplicação.

   * Elimine as ranhuras de produção e encenação de um serviço de nuvem existente para que o serviço de nuvem esteja vazio, e depois
   * Crie uma nova implementação no serviço de nuvem existente. Isto tentará novamente a atribuição de todos os agrupamentos da região. Certifique-se de que o serviço de nuvem não está ligado a um grupo de afinidade.
3. IP reservado - Esta solução irá preservar o seu endereço IP existente, mas causará tempo de inatividade na sua aplicação.  

   * Crie um IP Reservado para a sua implementação existente usando powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Siga #2 de cima, certificando-se de especificar o novo IP Reservado no CSCFG do serviço.
4. Remova o grupo de afinidade para novas implementações - Os Grupos affinity já não são recomendados. Siga os passos para o n.º 1 acima para implementar um novo serviço cloud. Certifique-se de que o serviço de nuvem não está num grupo de afinidade.
5. Converter-se numa Rede Virtual Regional - Ver [como migrar dos Grupos Affinity para uma Rede Virtual Regional (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
