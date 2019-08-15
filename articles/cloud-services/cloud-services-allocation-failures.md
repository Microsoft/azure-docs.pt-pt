---
title: Solucionando problemas de falha de alocação do serviço de nuvem | Microsoft Docs
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
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 7830b2a5d065f54029839d250e35f3e1b3da2200
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945475"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Resolução de problemas com a falha de atribuição quando implementar os Serviços Cloud no Azure
## <a name="summary"></a>Resumo
Quando você implanta instâncias em um serviço de nuvem ou adiciona novas instâncias de função Web ou de trabalho, Microsoft Azure aloca recursos de computação. Ocasionalmente, você pode receber erros ao executar essas operações mesmo antes de alcançar os limites de assinatura do Azure. Este artigo explica as causas de algumas das falhas de alocação comuns e sugere a possível correção. As informações também podem ser úteis quando você planeja a implantação de seus serviços.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Plano de fundo – como funciona a alocação
Os servidores nos datacenters do Azure são divididos em partições em clusters. Uma nova solicitação de alocação de serviço de nuvem é tentada em vários clusters. Quando a primeira instância é implantada em um serviço de nuvem (em preparo ou produção), esse serviço de nuvem é fixado em um cluster. Todas as implantações adicionais para o serviço de nuvem ocorrerão no mesmo cluster. Neste artigo, vamos nos referir a isso como "fixado a um cluster". O diagrama 1 abaixo ilustra o caso de uma alocação normal que é tentada em vários clusters; O diagrama 2 ilustra o caso de uma alocação fixada no cluster 2 porque é aí que o serviço de nuvem existente CS_1 está hospedado.

![Diagrama de alocação](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Por que a falha de alocação ocorre
Quando uma solicitação de alocação é fixada em um cluster, há uma chance maior de falha na localização de recursos gratuitos, já que o pool de recursos disponível é limitado a um cluster. Além disso, se sua solicitação de alocação for fixada em um cluster, mas o tipo de recurso solicitado não for suportado por esse cluster, sua solicitação falhará mesmo que o cluster tenha recursos livres. O diagrama 3 abaixo ilustra o caso em que uma alocação fixa falha porque o único cluster candidato não tem recursos gratuitos. O diagrama 4 ilustra o caso em que uma alocação fixada falha porque o único cluster candidato não dá suporte ao tamanho de VM solicitado, mesmo que o cluster tenha recursos gratuitos.

![Falha de alocação fixada](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Solucionando problemas de falha de alocação para serviços de nuvem
### <a name="error-message"></a>Mensagem de Erro
Você pode ver a seguinte mensagem de erro:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Problemas Comuns
Aqui estão os cenários de alocação comuns que fazem com que uma solicitação de alocação seja fixada em um único cluster.

* Implantando no slot de preparo – se um serviço de nuvem tiver uma implantação em qualquer slot, o serviço de nuvem inteiro será fixado em um cluster específico.  Isto significa que, se já existir uma implementação no bloco de produção, só será possível alocar uma implementação de teste nova no mesmo cluster que o bloco de produção. Se o cluster estiver se aproximando da capacidade, a solicitação poderá falhar.
* Dimensionamento – adicionar novas instâncias a um serviço de nuvem existente deve alocar no mesmo cluster.  Regra geral, é possível alocar pequenos pedidos de dimensionamento, mas nem sempre. Se o cluster estiver se aproximando da capacidade, a solicitação poderá falhar.
* Grupo de afinidade – uma nova implantação em um serviço de nuvem vazio pode ser alocada pela malha em qualquer cluster nessa região, a menos que o serviço de nuvem seja fixado em um grupo de afinidade. As implantações no mesmo grupo de afinidade serão tentadas no mesmo cluster. Se o cluster estiver se aproximando da capacidade, a solicitação poderá falhar.
* Grupo de afinidade vNet-redes virtuais mais antigas foram ligadas a grupos de afinidade em vez de regiões, e os serviços de nuvem nessas redes virtuais seriam fixados no cluster do grupo de afinidade. As implantações para esse tipo de rede virtual serão tentadas no cluster fixado. Se o cluster estiver se aproximando da capacidade, a solicitação poderá falhar.

## <a name="solutions"></a>Soluções
1. Reimplantar em um novo serviço de nuvem-essa solução provavelmente será mais bem-sucedida, pois permitirá que a plataforma escolha entre todos os clusters nessa região.

   * Implantar a carga de trabalho em um novo serviço de nuvem  
   * Atualizar o CNAME ou um registro para apontar o tráfego para o novo serviço de nuvem
   * Uma vez que o tráfego zero vá para o site antigo, você poderá excluir o serviço de nuvem antigo. Essa solução deve incorrer em tempo de inatividade.
2. Excluir slots de produção e de preparo-essa solução preservará o nome DNS existente, mas causará tempo de inatividade para seu aplicativo.

   * Exclua os slots de produção e de preparo de um serviço de nuvem existente para que o serviço de nuvem esteja vazio e, em seguida,
   * Crie uma nova implantação no serviço de nuvem existente. Isso tentará realizar a alocação novamente em todos os clusters na região. Verifique se o serviço de nuvem não está vinculado a um grupo de afinidade.
3. IP Reservado-essa solução preservará seu endereço IP existente, mas causará tempo de inatividade para seu aplicativo.  

   * Criar um ReservedIP para sua implantação existente usando o PowerShell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Siga #2 acima, certificando-se de especificar o novo ReservedIP no CSCFG do serviço.
4. Remover grupo de afinidade para novas implantações – grupos de afinidades não são mais recomendados. Siga as etapas para #1 acima para implantar um novo serviço de nuvem. Verifique se o serviço de nuvem não está em um grupo de afinidade.
5. Converter em uma rede virtual regional – consulte [como migrar de grupos de afinidades para uma rede virtual regional (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
