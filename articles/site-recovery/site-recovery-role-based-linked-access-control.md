---
title: Gerir o acesso do Azure Site Recovery com controlo de acesso baseado em funções (RBAC) | Documentos da Microsoft
description: Este artigo descreve como aplicar controlo de acesso baseado em funções (RBAC) para gerir o acesso do Azure Site Recovery.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 33fc2cd19152fb6cbbffb106aa058948d39555f9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281714"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Gerir o acesso de recuperação de sites com o controlo de acesso baseado em funções (RBAC)

O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Utilizando o RBAC, pode separar responsabilidades dentro da sua equipa e conceder apenas permissões de acesso específico aos utilizadores, conforme necessário para executar tarefas específicas.

O Azure Site Recovery fornece 3 funções incorporadas para controlar as operações de gestão do Site Recovery. Obter mais informações sobre [Funções incorporadas do RBAC do Azure](../role-based-access-control/built-in-roles.md)

* [Contribuinte do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - esta função tem todas as permissões necessárias para gerir as operações do Azure Site Recovery num cofre dos Serviços de Recuperação. No entanto, um utilizador com esta função não consegue criar nem eliminar um cofre dos Serviços de Recuperação, nem atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para os administradores de recuperação de desastre que podem ativar e gerir a recuperação após desastre para aplicações ou organizações completas, como o caso may ter.
* [Operador do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) - esta função tem permissões para executar e gerir operações de Ativação Pós-falha e Reativação Pós-falha. Um utilizador com esta função não é possível ativar ou desativar a replicação, criar ou eliminar cofres, registar uma nova infraestrutura ou atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para um operador de recuperação após desastre que pode máquinas de virtuais de ativação pós-falha ou analise de aplicações quando indicado pelos proprietários da aplicação e os administradores de TI numa situação de desastre reais ou simulados, como um DR. Após a resolução do desastre, o operador de DR pode voltar a proteger e a reativação pós-falha as máquinas virtuais.
* [Leitor do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) - esta função tem permissões para ver todas as operações de gestão do Site Recovery. Esta função é mais adequada para um executivo de monitorização de IT que pode monitorizar o estado atual da proteção e emitir pedidos de suporte, se necessário.

Se estiver procurando para definir suas próprias funções para o controle ainda mais, veja como [criar funções personalizadas](../role-based-access-control/custom-roles.md) no Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Permissões necessárias para ativar a replicação para novas máquinas virtuais
Quando uma nova máquina Virtual é replicado para o Azure com o Azure Site Recovery, níveis de acesso do utilizador associado são validados para garantir que o utilizador tem as permissões necessárias para utilizar os recursos do Azure fornecidos para o Site Recovery.

Para ativar a replicação para uma nova máquina virtual, um utilizador tem de ter:
* Permissão para criar uma máquina virtual no grupo de recursos selecionado
* Permissão para criar uma máquina virtual na rede virtual selecionada
* Permissão de escrita para a conta de armazenamento selecionada

Um utilizador tem as seguintes permissões para concluir a replicação de uma nova máquina virtual.

> [!IMPORTANT]
>Certifique-se de que permissões relevantes são adicionadas pelo modelo de implementação (Gestor de recursos / clássico) utilizado para a implementação de recursos.

| **Tipo de Recurso** | **Modelo de implementação** | **Permissão** |
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
| Armazenamento | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Clássica | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Grupo de Recursos | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Considere a utilização de "Contribuinte de Máquina Virtual" e "Contribuinte de Máquina Virtual clássica" [funções incorporadas](../role-based-access-control/built-in-roles.md) para a implementação do Resource Manager e clássica modela, respetivamente.

## <a name="next-steps"></a>Passos Seguintes
* [Controlo de acesso baseado em funções](../role-based-access-control/role-assignments-portal.md): Comece com RBAC no portal do Azure.
* Saiba como gerir o acesso com:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Resolução de problemas de controlo de acesso baseado em funções](../role-based-access-control/troubleshooting.md): Obtenha sugestões para corrigir problemas comuns.
