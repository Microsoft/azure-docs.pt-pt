---
title: Substituição futura de DR entre sites de Propriedade do cliente usando o Hyper-V e entre sites gerenciados pelo SCVMM para o Azure | Microsoft Docs
description: Detalhes sobre a futura substituição de DR entre sites de Propriedade do cliente usando o Hyper-V e entre sites gerenciados pelo SCVMM para o Azure e opções alternativas
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/13/2019
ms.author: rajanaki
ms.openlocfilehash: c126d72720c16d4ba869156e86a6e60110b2c31b
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847483"
---
# <a name="upcoming-deprecation-of-dr-between-customer-owned-sites-using-hyper-v-and-between-sites-managed-by-scvmm-to-azure"></a>Substituição futura de DR entre sites de Propriedade do cliente usando o Hyper-V e entre sites gerenciados pelo SCVMM para o Azure

Este artigo descreve o próximo plano de substituição, as implicações correspondentes e as opções alternativas disponíveis para os clientes para os cenários a seguir que foram suportados pelo Azure Site Recovery. 

- DR entre as VMs do Hyper-V gerenciadas pelo SCVMM entre os sites de Propriedade do cliente 
- DR de VMs do Hyper-V gerenciadas pelo SCVMM para o Azure

> [!IMPORTANT]
> Esses cenários estão atualmente marcados para substituição e os clientes devem tomar as medidas de correção no início mais cedo para evitar qualquer interrupção em seu ambiente. 
 

## <a name="what-changes-should-you-expect"></a>Quais alterações você deve esperar?

- A partir de novembro de 2019, nenhum novo usuário integrado será permitido para esses cenários. **As operações de gerenciamento e as replicações existentes** , incluindo failover, failover de teste, monitoramento etc., **não serão afetadas**.

- Depois que os cenários forem preteridos, haverá as seguintes implicações, a menos que o cliente siga as etapas recomendadas.

    - DR entre as VMs do Hyper-V gerenciadas pelo SCVMM entre os sites pertencentes ao cliente: as replicações continuarão a funcionar, pois o recurso subjacente da réplica do Hyper-V continuará a funcionar, mas os clientes não poderão exibir, gerenciar ou executar quaisquer operações relacionadas a DR por meio da experiência de recuperação do Azure Sire no portal do Azure. 
    - DR de VMs do Hyper-V gerenciadas pelo SCVMM para o Azure: as replicações existentes serão interrompidas e os clientes não poderão exibir, gerenciar ou executar quaisquer operações relacionadas a DR por meio do Azure Site Recovery


## <a name="recommended-actions-to-be-taken"></a>Ações recomendadas a serem tomadas

Abaixo estão as opções que o cliente tem para garantir que sua estratégia de DR não seja afetada quando o cenário for preterido. 

- Opte [por começar a usar o Azure como o destino de Dr para VMs em hosts Hyper-V](hyper-v-azure-tutorial.md).

> [!IMPORTANT]
> Observe que seu ambiente local ainda pode ter o SCVMM, mas você configurará o ASR com referências apenas aos hosts do Hyper-V.

- Opte por continuar com a replicação site a site, mas usando a [solução de réplica do Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)subjacente, mas não poderá gerenciar as configurações de DR usando Azure Site Recovery no portal do Azure. 


## <a name="next-steps"></a>Próximos Passos
Planeje a substituição e escolha uma opção alternativa que seja mais adequada para sua infraestrutura e negócios. Caso você tenha alguma consulta sobre isso, entre em contato com Suporte da Microsoft

