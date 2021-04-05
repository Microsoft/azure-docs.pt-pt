---
title: Gerir o controlo de acesso baseado em funções Azure na Recuperação do Local de Azure
description: Este artigo descreve como aplicar o controlo de acesso baseado em funções Azure (Azure RBAC) para gerir o acesso à Recuperação do Local de Azure.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: d3e1334f513e8ac587d639758d83ce080c5b4ab9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516907"
---
# <a name="manage-site-recovery-access-with-azure-role-based-access-control-azure-rbac"></a>Gerir o acesso à recuperação do local com o controlo de acesso baseado em funções Azure (Azure RBAC)

O controlo de acesso baseado em funções Azure (Azure RBAC) permite uma gestão de acesso de grãos finos para o Azure. Utilizando o Azure RBAC, pode segregar responsabilidades dentro da sua equipa e conceder apenas permissões de acesso específicas aos utilizadores, conforme necessário para realizar trabalhos específicos.

A Azure Site Recovery fornece 3 funções incorporadas para controlar as operações de gestão da Recuperação do Local. Saiba mais sobre [os papéis embutidos no Azure](../role-based-access-control/built-in-roles.md)

* [Contribuinte do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - esta função tem todas as permissões necessárias para gerir as operações do Azure Site Recovery num cofre dos Serviços de Recuperação. No entanto, um utilizador com esta função não consegue criar nem eliminar um cofre dos Serviços de Recuperação, nem atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para administradores de recuperação de desastres que podem permitir e gerir a recuperação de desastres para aplicações ou organizações inteiras, como pode ser o caso.
* [Operador do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) - esta função tem permissões para executar e gerir operações de Ativação Pós-falha e Reativação Pós-falha. Um utilizador com esta função não pode ativar ou desativar a replicação, criar ou apagar cofres, registar novas infraestruturas ou atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para um operador de recuperação de desastres que pode falhar com máquinas virtuais ou aplicações quando instruído pelos proprietários de aplicações e administradores de TI em uma situação de desastre real ou simulado, como um broca DR. Após a resolução do desastre, o operador DR pode re-proteger e falhar as máquinas virtuais.
* [Leitor do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) - esta função tem permissões para ver todas as operações de gestão do Site Recovery. Este papel é mais adequado para um executivo de monitorização de TI que possa monitorizar o estado atual de proteção e aumentar os bilhetes de apoio, se necessário.

Se procura definir os seus próprios papéis para ainda mais controlo, veja como [construir papéis personalizados](../role-based-access-control/custom-roles.md) em Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Permissões necessárias para permitir a replicação de novas máquinas virtuais
Quando uma nova Máquina Virtual é replicada para o Azure utilizando a Recuperação do Site Azure, os níveis de acesso do utilizador associado são validados para garantir que o utilizador tem as permissões necessárias para utilizar os recursos Azure fornecidos à Recuperação do Site.

Para permitir a replicação de uma nova máquina virtual, um utilizador deve ter:
* Permissão para criar uma máquina virtual no grupo de recursos selecionado
* Permissão para criar uma máquina virtual na rede virtual selecionada
* Permissão para escrever na conta de Armazenamento selecionada

Um utilizador precisa das seguintes permissões para completar a replicação de uma nova máquina virtual.

> [!IMPORTANT]
>Certifique-se de que as permissões relevantes são adicionadas de acordo com o modelo de implementação (Gestor de Recursos/ Classic) utilizado para a implementação de recursos.

> [!NOTE]
> Se estiver a permitir a replicação de um VM Azure e pretender permitir a Recuperação do Site para gerir as atualizações, então, ao mesmo tempo que permite a replicação, poderá também querer criar uma nova conta Demôm, caso em que necessitaria de permissão para criar uma conta de automação na mesma subscrição que o cofre.

| **Tipo de Recurso**: | **Modelo de Implementação** | **Permissão** |
| --- | --- | --- |
| Computação | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/ler |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Clássico | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/ler |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Rede | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Clássico | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Armazenamento | Resource Manager | Microsoft.Storage/storageAcontas/ler |
|  |  | Microsoft.Storage/storageAcontas/listkeys/ação |
|  | Clássico | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Grupo de Recursos | Resource Manager | Microsoft.Resources/implementações/* |
|  |  | Microsoft.Resources/subscrições/resourceGroups/read |

Considere utilizar as [funções incorporadas](../role-based-access-control/built-in-roles.md) 'Virtual Machine Contributor' e 'Classic Virtual Machine Contributor' para o Gestor de Recursos e modelos de implantação Clássicos, respectivamente.

## <a name="next-steps"></a>Passos seguintes
* [Controlo de acesso baseado em funções (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): Começa com o Azure RBAC no portal Azure.
* Saiba como gerir o acesso com:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Resolução de problemas do Azure RBAC](../role-based-access-control/troubleshooting.md): Obtenha sugestões para corrigir questões comuns.
