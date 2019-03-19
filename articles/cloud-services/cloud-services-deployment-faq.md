---
title: Problemas de implementação para perguntas frequentes do Microsoft Azure Cloud Services | Documentos da Microsoft
description: Este artigo apresenta uma lista de perguntas mais frequentes sobre a implantação para os serviços de Cloud do Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: adaed4c7af1d325f85d6fc349ac9a4faf73c1169
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078824"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de implementação de serviços Cloud do Azure: Perguntas mais frequentes (FAQ)

Este artigo contém perguntas freqüentes sobre problemas de implementação para [serviços Cloud do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Também pode consultar o [página de tamanho de VM de serviços Cloud](cloud-services-sizes-specs.md) para informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Por que, às vezes, a implementar um serviço em nuvem para o bloco de teste falhar com um erro de alocação de recursos se já existir uma implementação existente no bloco de produção?
Se um serviço em nuvem tem uma implementação em qualquer bloco, o serviço em nuvem inteira está afixado a um cluster específico. Isso significa que se uma implementação já existe no bloco de produção, uma nova implementação de teste pode apenas ser alocada no mesmo cluster como o bloco de produção.

Falhas de atribuição ocorrerem quando o cluster onde está localizado o seu serviço cloud não tem recursos suficientes de computação físisca para satisfazer o pedido de implementação.

Para ajudar a mitigar tais falhas de alocação, consulte [falha de alocação do serviço em nuvem: Soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Por que faz aumentar ou reduzir horizontalmente uma implementação do serviço cloud, às vezes, resultar numa falha de alocação?
Quando um serviço em nuvem é implementado, ele normalmente fixa-se um cluster específico. Isso significa aumentar verticalmente/horizontalmente um serviço cloud existente tem de alocar novas instâncias no mesmo cluster. Se o cluster está prestes a capacidade ou tamanho/tipo de VM pretendido não estiver disponível, o pedido pode falhar.

Para ajudar a mitigar tais falhas de alocação, consulte [falha de alocação do serviço em nuvem: Soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Por que, às vezes, a implementar um serviço em nuvem num grupo de afinidades resulta numa falha de alocação?
Pelos recursos de infraestrutura em qualquer cluster nessa região, que pode ser atribuída uma nova implementação num serviço cloud vazio, a menos que o serviço em nuvem está afixado a um grupo de afinidades. As implementações para o mesmo grupo de afinidade serão tentadas no mesmo cluster. Se o cluster está prestes a capacidade, o pedido pode falhar.

Para ajudar a mitigar tais falhas de alocação, consulte [falha de alocação do serviço em nuvem: Soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Por que motivo alterar tamanho da VM ou ao adicionar uma nova VM a um serviço cloud existente, por vezes, resultar numa falha de alocação?
Os clusters num centro de dados podem ter diferentes configurações de tipos de máquina (por exemplo, uma série, série Av2, série D, série Dv2, série G, série H, etc.). Mas não todos os clusters teria necessariamente todos os tipos de VMs. Por exemplo, se tentar adicionar uma VM da série D para um serviço cloud que já tenha sido implementado num cluster só de série A, terá uma falha de alocação. Isso também acontece se tentar altere que os tamanhos de SKU de VM (por exemplo, mudar de uma série de à uma série de D).

Para ajudar a mitigar tais falhas de alocação, consulte [falha de alocação do serviço em nuvem: Soluções](cloud-services-allocation-failures.md#solutions).

Para verificar os tamanhos disponíveis na sua região, consulte [Microsoft Azure: Produtos disponíveis por região](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Por que motivo algum tempo a implementar um serviço em nuvem falhar devido a quotas/limites/restrições na minha subscrição ou o serviço?
Implementação de um serviço cloud poderá falhar se os recursos que devem ser alocados excederem a quota máxima permitida para o seu serviço ao nível da região/datacenter ou padrão. Para obter mais informações, consulte [serviços Cloud limita](../azure-subscription-service-limits.md#azure-cloud-services-limits).

Também pode controlar a utilização/quota atual para a sua subscrição no portal do: Portal do Azure = > subscrições = > \<adequadas a subscrição > = > "Utilização + quotas".

Informações relacionadas com a utilização/consumo de recursos também podem ser obtidas por meio das APIs de faturação do Azure. Ver [API (pré-visualização) de utilização de recursos do Azure](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Como posso alterar o tamanho de um serviço cloud implementado VM sem voltar a implementar?
Não é possível alterar o tamanho da VM de um serviço cloud implementado sem voltar a implementar. O tamanho da VM é incorporado ao CSDEF, que só pode ser atualizada com uma voltar a implementar.

Para obter mais informações, consulte [como atualizar um serviço em nuvem](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Por que motivo não consigo implementar serviços Cloud através de APIs de gestão de serviço ou o PowerShell, ao utilizar a conta de armazenamento do Azure Resource Manager? 

Uma vez que o serviço em nuvem é um recurso de clássico que não é diretamente compatível com o modelo Azure Resource Manager, não pode associá-la com as contas de armazenamento do Azure Resource Manager. Aqui estão algumas opções: 
 
- Implementar através da REST API.

    Ao implementar através da API de REST de gestão de serviço, poderia contornar a limitação ao especificar um URL de SAS para o armazenamento de BLOBs, que irá funcionar com a conta clássica e o armazenamento do Azure Resource Manager. Saiba mais sobre a propriedade 'PackageUrl' [aqui](https://msdn.microsoft.com/library/azure/ee460813.aspx).
  
- Implementar através de [portal do Azure](https://portal.azure.com).

    Isso vai funcionar a partir da [portal do Azure](https://portal.azure.com) como a chamada passa por um proxy/shim que permite a comunicação entre os recursos do Azure Resource Manager e clássica. 
 
## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Por que motivo é que o portal do Azure-me fornecer uma conta de armazenamento para a implementação é necessário? 

No portal clássico, o pacote foi carregado para a camada de gestão de API diretamente e, em seguida, a camada de API temporariamente colocaria o pacote para uma conta de armazenamento interno.  Este processo faz com que os problemas de desempenho e a escalabilidade porque a camada de API não foi criada para ser um serviço de carregamento de ficheiros.  No portal do Azure (modelo de implementação do Resource Manager), podemos ter ignorados etapa intermediária carregar primeiro para a camada de API, resultando em implementações mais rápidas e confiáveis. 

Como o custo, é muito pequeno e pode reutilizar a mesma conta de armazenamento em todas as implementações. Pode utilizar o [Calculadora de custos de armazenamento](https://azure.microsoft.com/pricing/calculator/#storage1) para determinar o custo para carregar o pacote de serviço (. CSPKG), Baixe o CSPKG, em seguida, eliminar o CSPKG. 
