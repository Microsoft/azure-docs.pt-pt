---
title: Gerir o controlo de acesso baseado em funções do Azure na Recuperação do Site Azure
description: Este artigo descreve como aplicar o controlo de acesso baseado em papéis (RBAC) para gerir o acesso à Recuperação do Site Azure.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: ce389f9281b02662f87353f00c9bca92cdf86937
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362973"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Gerir o acesso à recuperação do site com controlo de acesso baseado em funções (RBAC)

O Controlo de Acesso baseado em funções azure (RBAC) permite uma gestão de acesso de grãos finos para o Azure. Utilizando o RBAC, pode segregar responsabilidades dentro da sua equipa e conceder apenas permissões de acesso específicas aos utilizadores, conforme necessário para realizar trabalhos específicos.

A Azure Site Recovery fornece 3 funções incorporadas para controlar as operações de gestão de recuperação do site. Obter mais informações sobre [Funções incorporadas do RBAC do Azure](../role-based-access-control/built-in-roles.md)

* [Contribuinte do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - esta função tem todas as permissões necessárias para gerir as operações do Azure Site Recovery num cofre dos Serviços de Recuperação. No entanto, um utilizador com esta função não consegue criar nem eliminar um cofre dos Serviços de Recuperação, nem atribuir direitos de acesso a outros utilizadores. Este papel é mais adequado para administradores de recuperação de desastres que possam permitir e gerir a recuperação de desastres para aplicações ou organizações inteiras, como o caso pode ser.
* [Operador do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) - esta função tem permissões para executar e gerir operações de Ativação Pós-falha e Reativação Pós-falha. Um utilizador com esta função não pode ativar ou desativar a replicação, criar ou eliminar cofres, registar novas infraestruturas ou atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para um operador de recuperação de desastres que pode falhar em máquinas virtuais ou aplicações quando instruído pelos proprietários de aplicações e administradores de TI numa situação de desastre real ou simulada, como um berbequim DR. Após a resolução do desastre, o operador DR pode reproteger e falhar as máquinas virtuais.
* [Leitor do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) - esta função tem permissões para ver todas as operações de gestão do Site Recovery. Esta função é mais adequada para um executivo de monitorização de TI que possa monitorizar o estado atual de proteção e levantar bilhetes de apoio, se necessário.

Se procura definir os seus próprios papéis para ainda mais controlo, veja como [construir papéis personalizados](../role-based-access-control/custom-roles.md) em Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Permissões necessárias para permitir a replicação de novas máquinas virtuais
Quando uma nova Máquina Virtual é replicada para o Azure utilizando a Recuperação do Site Azure, os níveis de acesso do utilizador associado são validados para garantir que o utilizador tem as permissões necessárias para utilizar os recursos Azure fornecidos à Recuperação do Site.

Para permitir a replicação de uma nova máquina virtual, um utilizador deve ter:
* Permissão para criar uma máquina virtual no grupo de recursos selecionados
* Permissão para criar uma máquina virtual na rede virtual selecionada
* Permissão para escrever na conta de Armazenamento selecionada

Um utilizador precisa das seguintes permissões para completar a replicação de uma nova máquina virtual.

> [!IMPORTANT]
>Certifique-se de que as permissões relevantes são adicionadas de acordo com o modelo de implementação (Gestor de Recursos/ Clássico) utilizado para a implantação de recursos.

> [!NOTE]
> Se estiver a permitir a replicação de um VM Azure e pretender permitir que a Recuperação do Site gere atualizações, então, ao mesmo tempo que permite a replicação, poderá também querer criar uma nova conta Automation, caso em que precisaria de autorização para criar uma conta de automação na mesma assinatura como o cofre também.

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
| Armazenamento | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Clássica | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Grupo de Recursos | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Considere utilizar as [funções incorporadas](../role-based-access-control/built-in-roles.md) do "Virtual Machine Contributor" e do "Classic Virtual Machine Contributor" para os modelos de Gestor de Recursos e De implantação Clássica, respectivamente.

## <a name="next-steps"></a>Passos seguintes
* [Controlo de Acesso baseado em funções](../role-based-access-control/role-assignments-portal.md): Comece com o RBAC no portal Azure.
* Saiba como gerir o acesso com:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Resolução de problemas do Controlo de Acesso baseado em papéis](../role-based-access-control/troubleshooting.md): Obtenha sugestões para corrigir questões comuns.
