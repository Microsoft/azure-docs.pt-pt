---
title: Gerenciar o controle de acesso baseado em função do Azure no Azure Site Recovery
description: Este artigo descreve como aplicar o RBAC (controle de acesso baseado em função) para gerenciar o acesso ao Azure Site Recovery.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: ce389f9281b02662f87353f00c9bca92cdf86937
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083767"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Gerenciar o acesso de Site Recovery com o RBAC (controle de acesso baseado em função)

O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o Azure. Usando o RBAC, você pode separar as responsabilidades dentro de sua equipe e conceder apenas permissões de acesso específicas aos usuários conforme necessário para executar trabalhos específicos.

O Azure Site Recovery fornece 3 funções internas para controlar as operações de gerenciamento de Site Recovery. Obter mais informações sobre [Funções incorporadas do RBAC do Azure](../role-based-access-control/built-in-roles.md)

* [Contribuinte do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - esta função tem todas as permissões necessárias para gerir as operações do Azure Site Recovery num cofre dos Serviços de Recuperação. No entanto, um utilizador com esta função não consegue criar nem eliminar um cofre dos Serviços de Recuperação, nem atribuir direitos de acesso a outros utilizadores. Essa função é mais adequada para administradores de recuperação de desastres que podem habilitar e gerenciar a recuperação de desastres para aplicativos ou organizações inteiras, como o caso pode ser.
* [Operador do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) - esta função tem permissões para executar e gerir operações de Ativação Pós-falha e Reativação Pós-falha. Um usuário com essa função não pode habilitar ou desabilitar a replicação, criar ou excluir cofres, registrar a nova infraestrutura ou atribuir direitos de acesso a outros usuários. Essa função é mais adequada para um operador de recuperação de desastre que pode fazer failover de máquinas virtuais ou aplicativos quando instruído por proprietários de aplicativos e administradores de ti em uma situação de desastre real ou simulada, como uma análise de DR. Após a resolução do desastre, o operador de DR pode proteger novamente e failback as máquinas virtuais.
* [Leitor do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) - esta função tem permissões para ver todas as operações de gestão do Site Recovery. Essa função é mais adequada para um executivo de monitoramento de ti que pode monitorar o estado atual da proteção e gerar tíquetes de suporte, se necessário.

Se você estiver procurando definir suas próprias funções para obter ainda mais controle, consulte como [criar funções personalizadas](../role-based-access-control/custom-roles.md) no Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Permissões necessárias para habilitar a replicação para novas máquinas virtuais
Quando uma nova máquina virtual é replicada para o Azure usando Azure Site Recovery, os níveis de acesso do usuário associado são validados para garantir que o usuário tenha as permissões necessárias para usar os recursos do Azure fornecidos para Site Recovery.

Para habilitar a replicação para uma nova máquina virtual, um usuário deve ter:
* Permissão para criar uma máquina virtual no grupo de recursos selecionado
* Permissão para criar uma máquina virtual na rede virtual selecionada
* Permissão para gravar na conta de armazenamento selecionada

Um usuário precisa das seguintes permissões para concluir a replicação de uma nova máquina virtual.

> [!IMPORTANT]
>Verifique se as permissões relevantes foram adicionadas de acordo com o modelo de implantação (Resource Manager/clássico) usadas para a implantação de recursos.

> [!NOTE]
> Se você estiver habilitando a replicação para uma VM do Azure e quiser permitir que Site Recovery gerencie atualizações e, ao mesmo tempo, habilitar a replicação, convém criar uma nova conta de automação, caso em que você precisaria de permissão para criar uma conta de automação na mesma assinatura como o cofre também.

| **Tipo de Recurso** | **Modelo de implantação** | **Permissão** |
| --- | --- | --- |
| Computação | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Clássica | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Rede | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Clássica | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Storage | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Clássica | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Grupo de Recursos | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Considere o uso das [funções internas](../role-based-access-control/built-in-roles.md) ' colaborador da máquina virtual ' e ' colaborador da máquina virtual clássica ' para os modelos de implantação do Resource Manager e clássico, respectivamente.

## <a name="next-steps"></a>Passos seguintes
* [Controle de acesso baseado em função](../role-based-access-control/role-assignments-portal.md): introdução ao RBAC no portal do Azure.
* Saiba como gerenciar o acesso com:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Solução de problemas de controle de acesso baseado em função](../role-based-access-control/troubleshooting.md): Obtenha sugestões para corrigir problemas comuns.
