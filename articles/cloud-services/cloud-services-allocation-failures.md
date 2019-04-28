---
title: Resolução de falhas de alocação do serviço em nuvem | Documentos da Microsoft
description: Resolução de problemas com a falha de atribuição quando implementar os Serviços Cloud no Azure
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: d1f24c3661a23496d1873f12ce46083bf5258269
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61435513"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Resolução de problemas com a falha de atribuição quando implementar os Serviços Cloud no Azure
## <a name="summary"></a>Resumo
Quando implementar instâncias de um serviço em nuvem ou adicionar novos web ou de instâncias de função de trabalho, o Microsoft Azure aloca recursos de computação. Ocasionalmente, poderá receber erros ao efetuar estas operações até mesmo antes de atingir os limites de subscrição do Azure. Este artigo explica as causas de algumas das falhas de alocação comuns e sugere possíveis remediação. As informações também podem ser úteis quando planear a implementação dos seus serviços.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Em segundo plano – como funciona a alocação
Os servidores nos datacenters do Azure são divididos em partições em clusters. Um novo pedido de alocação do serviço de nuvem é tentado em vários clusters. Quando a primeira instância for implementada para um serviço de cloud (no teste ou produção), que o serviço em nuvem fixa-se a um cluster. Todas as implementações adicionais para o serviço em nuvem irão acontecer no mesmo cluster. Neste artigo, faremos referência a este como "afixado a um cluster". Diagrama 1 abaixo ilustra o caso de uma alocação normal, que é tentada em vários clusters; Diagrama 2 ilustra o caso de uma alocação que tenha afixado ao Cluster 2 porque é onde está alojado o CS_1 de serviço Cloud existente.

![Diagrama de alocação](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Por que acontece a falha de alocação
Quando uma solicitação de alocação é afixada a um cluster, há uma chance maior de conseguir encontrar os recursos gratuitos, uma vez que o agrupamento de recursos disponíveis está limitado a um cluster. Além disso, se o seu pedido de alocação está afixado a um cluster, mas o tipo de recurso solicitado não é suportado por esse cluster, o pedido irá falhar, mesmo que o cluster tem o recurso gratuito. Diagrama 3 abaixo ilustra o caso em que uma alocação afixada falha porque o cluster apenas candidato não tem recursos gratuitos. Diagrama 4 ilustra o caso em que uma alocação afixada falha porque o cluster apenas candidato não suporta o tamanho da VM pedida, mesmo que o cluster tem recursos gratuitos.

![Falha de alocação afixado](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Resolução de falhas de alocação para serviços em nuvem
### <a name="error-message"></a>Mensagem de Erro
Pode ver a seguinte mensagem de erro:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Problemas Comuns
Aqui estão os cenários comuns de alocação que fazem com que uma solicitação de alocação ser afixadas a um único cluster.

* Implementar o bloco de teste - se um serviço em nuvem tem uma implementação em qualquer um dos ranhura, em seguida, o serviço em nuvem inteira está afixado a um cluster específico.  Isto significa que, se já existir uma implementação no bloco de produção, só será possível alocar uma implementação de teste nova no mesmo cluster que o bloco de produção. Se o cluster está prestes a capacidade, o pedido pode falhar.
* Dimensionamento – adicionar novas instâncias de um serviço cloud existente tem de alocar no mesmo cluster.  Regra geral, é possível alocar pequenos pedidos de dimensionamento, mas nem sempre. Se o cluster está prestes a capacidade, o pedido pode falhar.
* Grupo de afinidade - uma nova implementação num serviço cloud vazio pode ser alocada por recursos de infraestrutura em qualquer cluster nessa região, a menos que o serviço em nuvem está afixado a um grupo de afinidades. As implementações para o mesmo grupo de afinidade serão tentadas no mesmo cluster. Se o cluster está prestes a capacidade, o pedido pode falhar.
* Grupo de afinidade vNet - mais antiga de redes virtuais eram vinculados a grupos de afinidade em vez de regiões e serviços cloud nestas redes virtuais iriam ser afixados para o cluster de grupo de afinidade. As implementações para este tipo de rede virtual serão tentadas no cluster afixado. Se o cluster está prestes a capacidade, o pedido pode falhar.

## <a name="solutions"></a>Soluções
1. Voltar a implementar para um novo serviço cloud - essa solução é provável que seja mais bem-sucedidas, pois permite que a plataforma à sua escolha todos os clusters nessa região.

   * Implementar a carga de trabalho para um novo serviço cloud  
   * Atualizar um registo ou o CNAME para apontar o tráfego para o novo serviço cloud
   * Depois de zero tráfego será o site antigo, pode eliminar o serviço em nuvem antigo. Esta solução deve incorrer em nenhum tempo de inatividade.
2. Eliminar a produção e blocos de teste - essa solução irá preservar o nome DNS existente, mas fará com que o período de indisponibilidade para a sua aplicação.

   * Eliminar a produção e blocos de um serviço cloud existente de teste, para que o serviço em nuvem está vazio e, em seguida,
   * Crie uma nova implementação no serviço cloud existente. Isso tentará novamente para alocação em todos os clusters na região. Certifique-se de que o serviço em nuvem não está associado a um grupo de afinidades.
3. IP reservado - essa solução irá preservar o seu IP existente de endereços, mas fará com que o período de indisponibilidade para a sua aplicação.  

   * Criar um ReservedIP para a sua implementação existente com o Powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Siga #2 acima, é certificar-se de que especifique o ReservedIP novo em CSCFG do serviço.
4. Remova grupo de afinidade para novas implementações - grupos de afinidades já não são recomendadas. Siga os passos para #1 acima para implementar um novo serviço cloud. Certifique-se de que o serviço cloud não está num grupo de afinidades.
5. Converter para uma rede Virtual Regional - consulte [como migrar a partir de grupos de afinidades para uma rede Virtual Regional (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
