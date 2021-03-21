---
title: Falhas de alocação do Serviço de Nuvem (clássico) de resolução de problemas | Microsoft Docs
description: Resolver problemas com uma falha de atribuição quando implementar os Serviços Azure Cloud. Saiba como funciona a atribuição e por que a atribuição pode falhar.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 95fe4a8e1f6c6ee5f519311f8e756be89a09acf8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738315"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-classic-in-azure"></a>Falha na atribuição de problemas ao implementar serviços cloud (clássico) em Azure

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).


## <a name="summary"></a>Resumo

Quando implementa casos num Cloud Service ou adiciona novas instâncias de funções web ou de trabalhador, o Microsoft Azure atribui recursos de computação. Pode ocasionalmente receber erros ao realizar estas operações mesmo antes de atingir os limites de subscrição do Azure. Este artigo explica as causas de algumas falhas comuns de atribuição e sugere uma possível reparação. A informação também pode ser útil quando planeia a implantação dos seus serviços.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Fundo – Como funciona a atribuição

Os servidores nos datacenters do Azure são divididos em partições em clusters. Um novo pedido de atribuição de serviços em nuvem é tentado em vários clusters. Quando a primeira instância é implantada num serviço de nuvem (em encenação ou produção), esse serviço de nuvem fica preso a um cluster. Quaisquer outras implementações para o serviço de nuvem irão acontecer no mesmo cluster. Neste artigo, vamos referir-nos a isto como "preso a um aglomerado". O diagrama 1 abaixo ilustra o caso de uma dotação normal que é tentada em múltiplos aglomerados; O diagrama 2 ilustra o caso de uma alocação que está fixada ao Cluster 2 porque é aí que está hospedado o CS_1 do Serviço de Nuvem existente.

![Diagrama de atribuição](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Por que a falha de atribuição acontece

Quando um pedido de atribuição é fixado a um cluster, há uma maior probabilidade de não encontrar recursos gratuitos, uma vez que o conjunto de recursos disponíveis é limitado a um cluster. Além disso, se o seu pedido de atribuição for fixado a um cluster, mas o tipo de recurso que solicitou não for suportado por esse cluster, o seu pedido falhará mesmo que o cluster tenha recursos gratuitos. O diagrama 3 abaixo ilustra o caso em que uma dotação fixa falha porque o único cluster candidato não tem recursos livres. O diagrama 4 ilustra o caso em que uma dotação fixa falha porque o único cluster candidato não suporta a dimensão VM solicitada, mesmo que o cluster tenha recursos livres.

![Falha na atribuição de fixação](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Falha na atribuição de problemas para serviços na nuvem

### <a name="error-message"></a>Mensagem de Erro

No portal Azure, navegue para o seu serviço de nuvem e na barra lateral selecione *registos de operação (clássico)* para visualizar os registos.

Consulte mais soluções para as exceções abaixo:

|Tipo de Exceção  |Mensagem de Erro  |Solução  |
|---------|---------|---------|
|FabricInternalServerError     |A operação falhou com o código de erro 'InternalError' e o erroMessage 'O servidor encontrou um erro interno. Por favor, re-recandid recorde o pedido.|[Resolução de problemas FabricInternalServerError](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|ServiceAllocationFailure     |A operação falhou com o código de erro 'InternalError' e o erroMessage 'O servidor encontrou um erro interno. Por favor, re-recandid recorde o pedido.|[Serviço de resolução de problemasAllocationFailure](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|LocalizaçãoNotFoundForrolesize     |A operação `{Operation ID}` ' ' ' falhou: 'O nível VM solicitado não está atualmente disponível na Região ( `{Region ID}` ) para esta subscrição. Por favor, tente outro nível ou desloque-se para outro local.'.|[Localização de resolução de problemasNotFoundForrolesize](cloud-services-troubleshoot-location-not-found-for-role-size.md)|
|ConstrangimentoAllocationFailed     |A operação Azure `{Operation ID}` ' '' falhou com código Compute.ConstrainedAllocationFailed. Detalhes: A atribuição falhou; incapaz de satisfazer constrangimentos a pedido. A implementação do novo serviço solicitada está vinculada a um Grupo de Afinidade, tem como destino uma Rede Virtual ou há uma implementação existente neste serviço alojado. Qualquer uma destas condições limita a nova implantação a recursos específicos da Azure. Tente novamente mais tarde ou tente reduzir o tamanho da VM ou o número de instâncias de função. Em alternativa, se possível, remova as restrições mencionadas anteriormente ou tente implementar numa região diferente.|[Resolução de problemas ConstrangidosAfailed](cloud-services-troubleshoot-constrained-allocation-failed.md)|
|OverconstrainedAllocationRequest     |O tamanho VM (ou combinação de tamanhos VM) exigido por esta implantação não pode ser a provisionado devido a restrições de pedido de implantação. Se possível, tente relaxar constrangimentos, tais como encadernações de rede virtuais, implantar-se num serviço hospedado sem qualquer outra implantação no mesmo e para um grupo de afinidades diferente ou sem grupo de afinidade, ou tente implementar para uma região diferente.|[Resolução de problemas OverconstrainedAllocationRequest](cloud-services-troubleshoot-overconstrained-allocation-request.md)|

Mensagem de erro de exemplo:

> "A operação Azure '{operation id}' falhou com o código Compute.ConstrainedAllocationFailed. Detalhes: A atribuição falhou; incapaz de satisfazer constrangimentos a pedido. A implementação do novo serviço solicitada está vinculada a um Grupo de Afinidade, tem como destino uma Rede Virtual ou há uma implementação existente neste serviço alojado. Qualquer uma destas condições limita a nova implantação a recursos específicos da Azure. Tente novamente mais tarde ou tente reduzir o tamanho da VM ou o número de instâncias de função. Em alternativa, se possível, eliminar os constrangimentos acima referidos ou tentar implantar-se numa região diferente."

### <a name="common-issues"></a>Problemas Comuns

Eis os cenários comuns de atribuição que fazem com que um pedido de atribuição seja fixado a um único cluster.

* Implantação para a ranhura de paragem - Se um serviço de nuvem tiver uma implantação em qualquer uma das ranhuras, todo o serviço de nuvem está fixado a um cluster específico.  Isto significa que, se já existir uma implementação no bloco de produção, só será possível alocar uma implementação de teste nova no mesmo cluster que o bloco de produção. Se o cluster estiver próximo da capacidade, o pedido pode falhar.
* Dimensionamento - A adição de novas instâncias a um serviço cloud existente tem de realizar a alocação no mesmo cluster.  Regra geral, é possível alocar pequenos pedidos de dimensionamento, mas nem sempre. Se o cluster estiver próximo da capacidade, o pedido pode falhar.
* Affinity Group - Uma nova implantação para um serviço de nuvem vazia pode ser atribuída pelo tecido em qualquer cluster naquela região, a menos que o serviço de nuvem esteja preso a um grupo de afinidade. As implantações para o mesmo grupo de afinidade serão tentadas no mesmo aglomerado. Se o cluster estiver próximo da capacidade, o pedido pode falhar.
* Affinity Group vNet - Redes Virtuais mais antigas estavam ligadas a grupos de afinidade em vez de regiões, e os serviços em nuvem nestas Redes Virtuais seriam fixados ao cluster do grupo de afinidade. As implementações para este tipo de rede virtual serão tentadas no cluster fixado. Se o cluster estiver próximo da capacidade, o pedido pode falhar.

## <a name="solutions"></a>Soluções

1. Recolocar para um novo serviço em nuvem - Esta solução é provavelmente mais bem sucedida, pois permite que a plataforma escolha entre todos os clusters daquela região.

   * Implementar a carga de trabalho para um novo serviço de nuvem  
   * Atualizar o CNAME ou Um registo para apontar o tráfego para o novo serviço de nuvem
   * Uma vez que o tráfego zero vai para o site antigo, você pode apagar o antigo serviço de nuvem. Esta solução deve incorrer em tempo de inatividade zero.
2. Elimine as ranhuras de produção e de paragem - Esta solução irá preservar o nome DNS existente, mas causará tempo de inatividade à sua aplicação.

   * Elimine as ranhuras de produção e encenação de um serviço de nuvem existente para que o serviço de nuvem esteja vazio e, em seguida,
   * Criar uma nova implantação no serviço de nuvem existente. Isto tentará novamente alocatar em todos os aglomerados da região. Certifique-se de que o serviço de nuvem não está ligado a um grupo de afinidades.
3. IP Reservado - Esta solução irá preservar o seu endereço IP existente, mas causará tempo de inatividade à sua aplicação.  

   * Crie um ReservedIP para a sua implantação existente utilizando o PowerShell

     ```azurepowershell
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```

   * Siga #2 de cima, certificando-se de especificar o novo ReservedIP no CSCFG do serviço.
4. Remova o grupo de afinidade para novas implementações - Os Grupos affinity já não são recomendados. Siga os passos para o n.º 1 acima para implementar um novo serviço cloud. Certifique-se de que o serviço de nuvem não está num grupo de afinidades.
5. Converter-se numa Rede Virtual Regional - Ver [como migrar dos Grupos Affinity para uma Rede Virtual Regional (VNet)](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet).
