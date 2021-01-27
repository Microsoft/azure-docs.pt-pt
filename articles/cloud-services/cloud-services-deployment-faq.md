---
title: Problemas de implementação para o Microsoft Azure Cloud Services FAQ| Microsoft Docs
description: Este artigo lista as perguntas frequentes sobre a implementação dos Serviços cloud do Microsoft Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: dd7b19a2c9e872b811c1aab6e504accb7de383b2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896482"
---
# <a name="deployment-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Problemas de implantação para Azure Cloud Services (clássico): Perguntas frequentes (PERGUNTAS Frequentes)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).
Este artigo inclui perguntas frequentes sobre problemas de implementação para [o Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Também pode consultar a [página de tamanho VM dos Serviços cloud](cloud-services-sizes-specs.md) para obter informações sobre o tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Porque é que a implantação de um serviço de nuvem na ranhura de paragem por vezes falha com um erro de atribuição de recursos se já existe uma implantação existente na ranhura de produção?
Se um serviço de nuvem tiver uma implantação em qualquer uma das ranhuras, todo o serviço de nuvem está preso a um cluster específico. Isto significa que, se já existir uma implantação na ranhura de produção, uma nova implantação de encenação só pode ser atribuída no mesmo cluster que a ranhura de produção.

Falhas de atribuição ocorrem quando o cluster onde o seu serviço de nuvem está localizado não tem recursos físicos suficientes para satisfazer o seu pedido de implantação.

Para ajudar a mitigar tais falhas de atribuição, consulte [a falha de atribuição do Serviço cloud: Soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Por que escalonamento ou escalonamento de uma implementação de serviço em nuvem às vezes resulta em falha de atribuição?
Quando um serviço de nuvem é implantado, geralmente é fixado a um cluster específico. Isto significa que a ampliação/saída de um serviço de nuvem existente deve afetar novas instâncias no mesmo cluster. Se o cluster estiver próximo da capacidade ou o tamanho/tipo VM pretendido não estiver disponível, o pedido pode falhar.

Para ajudar a mitigar tais falhas de atribuição, consulte [a falha de atribuição do Serviço cloud: Soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Porque é que a implantação de um serviço em nuvem num grupo de afinidades resulta, por vezes, em falhas de alocação?
Uma nova implantação para um serviço de nuvem vazia pode ser atribuída pelo tecido em qualquer cluster naquela região, a menos que o serviço de nuvem esteja preso a um grupo de afinidade. As implantações para o mesmo grupo de afinidade serão tentadas no mesmo aglomerado. Se o cluster estiver próximo da capacidade, o pedido pode falhar.

Para ajudar a mitigar tais falhas de atribuição, consulte [a falha de atribuição do Serviço cloud: Soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Por que mudar o tamanho do VM ou adicionar um novo VM a um serviço de nuvem existente às vezes resulta em falha de alocação?
Os clusters num datacenter podem ter diferentes configurações de tipos de máquinas (por exemplo, série A, série Av2, série D, série Dv2, série G, sérieS H, etc.). Mas nem todos os aglomerados teriam necessariamente todos os tipos de VMs. Por exemplo, se tentar adicionar uma série D VM a um serviço de nuvem que já está implantado num cluster exclusivo da série A, sentirá uma falha de alocação. Isto também acontecerá se tentar alterar os tamanhos VM SKU (por exemplo, mudar de uma série A para uma série D).

Para ajudar a mitigar tais falhas de atribuição, consulte [a falha de atribuição do Serviço cloud: Soluções](cloud-services-allocation-failures.md#solutions).

Para verificar os tamanhos disponíveis na sua região, consulte [o Microsoft Azure: Produtos disponíveis por região.](https://azure.microsoft.com/regions/services)

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Por que razão a implantação de um serviço em nuvem falha por vezes devido a limites/quotas/restrições na minha subscrição ou serviço?
A implantação de um serviço em nuvem pode falhar se os recursos que são necessários para serem atribuídos excederem o contingente padrão ou máximo permitido para o seu serviço ao nível da região/datacenter. Para mais informações, consulte [os limites dos Serviços cloud.](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits)

Também pode acompanhar a utilização/quota atual da sua subscrição no portal: Azure portal => Subscrições => \<appropriate subscription>   => "Utilização + quota".

As informações relacionadas com a utilização/consumo de recursos também podem ser recuperadas através das APIs de faturação do Azure. Consulte [a visão geral da API de consumo azul](../cost-management-billing/manage/consumption-api-overview.md).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Como posso alterar o tamanho de um VM de serviço de nuvem implantado sem recolocar?
Não é possível alterar o tamanho VM de um serviço de nuvem implantado sem o recolocar. O tamanho VM é incorporado no CSDEF, que só pode ser atualizado com uma redistribuição.

Para mais informações, consulte [Como atualizar um serviço de cloud](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Por que não posso implementar serviços cloud através de APIs de Gestão de Serviços ou PowerShell ao utilizar a conta de Armazenamento de Gestores de Recursos Azure? 

Uma vez que o Cloud Service é um recurso Clássico que não é diretamente compatível com o modelo Azure Resource Manager, não é possível associá-lo às contas de Armazenamento do Gestor de Recursos Azure. Aqui estão poucas opções: 

- Implantação através da REST API.

    Quando implementar através da API rest de gestão de serviços, poderá contornar a limitação especificando um URL SAS para o armazenamento de bolhas, que funcionará com a conta de Armazenamento de Gestores de Recursos Clássico e Azure. Leia mais sobre a propriedade 'PackageUrl' [aqui.](/previous-versions/azure/reference/ee460813(v=azure.100))

- Implantação através do [portal Azure](https://portal.azure.com).

    Isto funcionará a partir do [portal Azure](https://portal.azure.com) à medida que a chamada passa por um proxy/shim que permite a comunicação entre o Azure Resource Manager e os recursos clássicos. 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Porque é que o portal Azure me obriga a fornecer uma conta de armazenamento para a implantação?

No portal clássico, o pacote foi enviado diretamente para a camada de API de gestão, e então a camada API colocaria temporariamente o pacote numa conta de armazenamento interno.  Este processo causa problemas de desempenho e escalabilidade porque a camada API não foi concebida para ser um serviço de upload de ficheiros.  No portal Azure (modelo de implementação do Gestor de Recursos), ignoramos o passo provisório do primeiro upload para a camada API, resultando em implementações mais rápidas e fiáveis.

Quanto ao custo, é muito pequeno e pode reutilizar a mesma conta de armazenamento em todas as implementações. Pode utilizar a [calculadora de custos de armazenamento](https://azure.microsoft.com/pricing/calculator/#storage1) para determinar o custo de carregar o pacote de serviço (CSPKG), descarregar o CSPKG e, em seguida, eliminar o CSPKG.
