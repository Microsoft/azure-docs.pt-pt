---
title: Problemas de implantação para Serviços de Nuvem do Microsoft Azure perguntas frequentes | Microsoft Docs
description: Este artigo lista as perguntas frequentes sobre a implantação para Serviços de Nuvem do Microsoft Azure.
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
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: fff70cc0c80d26d5454e54e43a6ef6c0b39b5cac
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68941731"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de implantação para os serviços de nuvem do Azure: Perguntas frequentes (FAQs)

Este artigo inclui perguntas frequentes sobre problemas de implantação para [serviços de nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Você também pode consultar a [página tamanho da VM dos serviços de nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Por que implantar um serviço de nuvem no slot de preparo às vezes falha com um erro de alocação de recursos se já houver uma implantação existente no slot de produção?
Se um serviço de nuvem tiver uma implantação em qualquer slot, o serviço de nuvem inteiro será fixado em um cluster específico. Isso significa que, se uma implantação já existir no slot de produção, uma nova implantação de preparo só poderá ser alocada no mesmo cluster que o slot de produção.

Falhas de alocação ocorrem quando o cluster no qual o serviço de nuvem está localizado não tem recursos de computação física suficientes para atender à sua solicitação de implantação.

Para obter ajuda para atenuar essas falhas de [alocação, consulte falha de alocação do serviço de nuvem: Soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Por que expandir ou escalar horizontalmente uma implantação de serviço de nuvem às vezes resulta em falha de alocação?
Quando um serviço de nuvem é implantado, ele geralmente é fixado em um cluster específico. Isso significa Expandir/reduzir um serviço de nuvem existente deve alocar novas instâncias no mesmo cluster. Se o cluster estiver se aproximando da capacidade ou o tipo/tamanho da VM desejado não estiver disponível, a solicitação poderá falhar.

Para obter ajuda para atenuar essas falhas de [alocação, consulte falha de alocação do serviço de nuvem: Soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Por que implantar um serviço de nuvem em um grupo de afinidades às vezes resulta em falha de alocação?
Uma nova implantação em um serviço de nuvem vazio pode ser alocada pela malha em qualquer cluster nessa região, a menos que o serviço de nuvem seja fixado em um grupo de afinidade. As implantações no mesmo grupo de afinidade serão tentadas no mesmo cluster. Se o cluster estiver se aproximando da capacidade, a solicitação poderá falhar.

Para obter ajuda para atenuar essas falhas de [alocação, consulte falha de alocação do serviço de nuvem: Soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Por que alterar o tamanho da VM ou adicionar uma nova VM a um serviço de nuvem existente às vezes resulta em falha de alocação?
Os clusters em um datacenter podem ter diferentes configurações de tipos de computador (por exemplo, uma série, série Av2, série D, série Dv2, série G, série H, etc.). Mas nem todos os clusters teriam necessariamente todos os tipos de VMs. Por exemplo, se você tentar adicionar uma VM da série D a um serviço de nuvem que já está implantado em um cluster somente série, ocorrerá uma falha de alocação. Isso também ocorrerá se você tentar alterar os tamanhos de SKU da VM (por exemplo, alternando de uma série a para uma série D).

Para obter ajuda para atenuar essas falhas de [alocação, consulte falha de alocação do serviço de nuvem: Soluções](cloud-services-allocation-failures.md#solutions).

Para verificar os tamanhos disponíveis em sua região, consulte [Microsoft Azure: Produtos disponíveis por região](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Por que implantar um serviço de nuvem em algum momento falha devido a limites/cotas/restrições em minha assinatura ou serviço?
A implantação de um serviço de nuvem poderá falhar se os recursos necessários para serem alocados excederem a cota padrão ou máxima permitida para seu serviço no nível de região/datacenter. Para obter mais informações, consulte [limites de serviços de nuvem](../azure-subscription-service-limits.md#azure-cloud-services-limits).

Você também pode acompanhar o uso/cota atual de sua assinatura no Portal: Portal do Azure = assinaturas > = > \<assinatura apropriada > = > "uso + cota".

Informações relacionadas ao uso/consumo de recursos também podem ser recuperadas por meio do APIs de Cobrança do Azure. Consulte [API de uso de recursos do Azure (versão prévia)](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Como posso alterar o tamanho de uma VM do serviço de nuvem implantada sem reimplantá-la?
Você não pode alterar o tamanho da VM de um serviço de nuvem implantado sem reimplantá-lo. O tamanho da VM é criado no CSDEF, que só pode ser atualizado com uma reimplantação.

Para obter mais informações, consulte [como atualizar um serviço de nuvem](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Por que não consigo implantar serviços de nuvem por meio de APIs de gerenciamento de serviços ou do PowerShell ao usar Azure Resource Manager conta de armazenamento? 

Como o serviço de nuvem é um recurso clássico que não é compatível diretamente com o modelo de Azure Resource Manager, você não pode associá-lo com as contas de armazenamento de Azure Resource Manager. Aqui estão algumas opções: 
 
- Implantando por meio da API REST.

    Ao implantar por meio da API REST do gerenciamento de serviços, você pode contornar a limitação especificando uma URL SAS para o armazenamento de BLOBs, que funcionará com a conta de armazenamento clássica e Azure Resource Manager. Leia mais sobre a propriedade ' PackageUrl ' [aqui](/previous-versions/azure/reference/ee460813(v=azure.100)).
  
- Implantando por meio do [portal do Azure](https://portal.azure.com).

    Isso funcionará da [portal do Azure](https://portal.azure.com) à medida que a chamada passar por um proxy/Shim, que permite a comunicação entre Azure Resource Manager e recursos clássicos. 
 
## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Por que portal do Azure exige que eu forneça uma conta de armazenamento para implantação? 

No portal clássico, o pacote foi carregado diretamente na camada de API de gerenciamento e, em seguida, a camada de API colocaria temporariamente o pacote em uma conta de armazenamento interno.  Esse processo causa problemas de desempenho e escalabilidade porque a camada de API não foi projetada para ser um serviço de carregamento de arquivo.  No portal do Azure (modelo de implantação do Gerenciador de recursos), ignoramos a etapa provisória do primeiro carregamento para a camada de API, resultando em implantações mais rápidas e confiáveis. 

Quanto ao custo, é muito pequeno e você pode reutilizar a mesma conta de armazenamento em todas as implantações. Você pode usar a [calculadora de custo de armazenamento](https://azure.microsoft.com/pricing/calculator/#storage1) para determinar o custo para carregar o pacote de serviço (CSPKG), baixar o CSPKG e, em seguida, excluir o CSPKG. 
