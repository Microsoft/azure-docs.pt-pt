---
title: Gerir backups com controlo de acesso baseado em funções Azure
description: Use o controlo de acesso baseado em funções Azure para gerir o acesso a operações de gestão de backup no cofre dos Serviços de Recuperação.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 0b321a5f33bd75ce8615d6d2a90442a83d9fff67
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613447"
---
# <a name="use-azure-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Use o controlo de acesso baseado em funções Azure para gerir os pontos de recuperação do Backup Azure

O controlo de acesso baseado em funções Azure (Azure RBAC) permite uma gestão de acesso de grãos finos para o Azure. Com o RBAC do Azure, pode fazer a segregação de deveres na sua equipa e conceder aos utilizadores apenas a quantidade de acesso de que precisam para desempenhar as suas funções.

> [!IMPORTANT]
> As funções fornecidas pela Azure Backup estão limitadas a ações que podem ser executadas no portal Azure ou através de cofres de API ou Serviços de Recuperação powerShell ou CLI. As ações realizadas no UI do agente de backup Azure ou no System center Data Protection Manager UI ou no Azure Backup Server UI estão fora de controlo destas funções.

O Azure Backup fornece três funções incorporadas para controlar as operações de gestão de backup. Saiba mais sobre [os papéis embutidos no Azure](../role-based-access-control/built-in-roles.md)

* [Backup Contributor](../role-based-access-control/built-in-roles.md#backup-contributor) - Esta função tem todas as permissões para criar e gerir backup, exceto eliminar o cofre dos Serviços de Recuperação e dar acesso a outros. Imagine este papel como administrador de gestão de backup que pode fazer todas as operações de gestão de backup.
* [Operador de Backup](../role-based-access-control/built-in-roles.md#backup-operator) - Esta função tem permissões para tudo o que um contribuinte faz exceto remover backup e gerir políticas de backup. Esta função é equivalente ao contribuinte, exceto que não pode realizar operações destrutivas, tais como parar a cópia de segurança com eliminar dados ou remover o registo de recursos no local.
* [Backup Reader](../role-based-access-control/built-in-roles.md#backup-reader) - Esta função tem permissões para visualizar todas as operações de gestão de backup. Imagine este papel como uma pessoa de monitorização.

Se procura definir os seus próprios papéis para ainda mais controlo, veja como [construir papéis personalizados](../role-based-access-control/custom-roles.md) no Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapeamento de papéis incorporados de Backup para ações de gestão de backup

### <a name="minimum-role-requirements-for-azure-vm-backup"></a>Requisitos mínimos de função para backup Azure VM

O quadro seguinte captura as ações de gestão de Backup e a correspondente função mínima de Azure necessária para executar essa operação.

| Operação de Gestão | Função mínima de Azure necessária | Âmbito necessário | Alternativa |
| --- | --- | --- | --- |
| Criar cofre dos Serviços de Recuperação | Colaborador de backup | Grupo de recursos contendo o cofre |   |
| Ativar o backup dos VMs Azure | Operador de Cópia de Segurança | Grupo de recursos contendo o cofre |   |
| | Contribuidor de Máquina Virtual | Recurso VM |  Alternativamente, em vez de um papel incorporado, pode considerar um papel personalizado que tem as seguintes permissões: Microsoft.Compute/virtualMachines/write |
| Backup a pedido da VM | Operador de Cópia de Segurança | Cofre dos Serviços de Recuperação |   |
| Restaurar VMs | Operador de Cópia de Segurança | Cofre dos Serviços de Recuperação |   |
| | Contribuinte | Grupo de recursos no qual a VM será implantada |   Em alternativa, em vez de uma função incorporada, pode considerar uma função personalizada que tenha as seguintes permissões: Microsoft.Resources/subscrições/resourceGroups/write Microsoft.DomainRegistration/domains/write, Microsoft.Compute/virtualMachines/write Microsoft.Network/virtualNetworks/read Microsoft.Network/virtualNetworks/subnets/join/action |
| | Contribuidor de Máquina Virtual | Fonte VM que foi apoiado |   Alternativamente, em vez de um papel incorporado, pode considerar um papel personalizado que tem as seguintes permissões: Microsoft.Compute/virtualMachines/write |
| Restaurar a cópia de segurança dos discos não geridos VM | Operador de Cópia de Segurança | Cofre dos Serviços de Recuperação |
| | Contribuidor de Máquina Virtual | Fonte VM que foi apoiado | Alternativamente, em vez de um papel incorporado, pode considerar um papel personalizado que tem as seguintes permissões: Microsoft.Compute/virtualMachines/write |
| | Contribuidor de Conta de Armazenamento | Recurso de conta de armazenamento onde os discos vão ser restaurados |   Em alternativa, em vez de uma função incorporada, pode considerar um papel personalizado que tem as seguintes permissões: Microsoft.Storage/storageAccounts/write |
| Restaurar discos geridos a partir de backup VM | Operador de Cópia de Segurança | Cofre dos Serviços de Recuperação |
| | Contribuidor de Máquina Virtual | Fonte VM que foi apoiado |    Alternativamente, em vez de um papel incorporado, pode considerar um papel personalizado que tem as seguintes permissões: Microsoft.Compute/virtualMachines/write |
| | Contribuidor de Conta de Armazenamento | Conta de Armazenamento Temporário selecionada como parte da restauração para reter dados do cofre antes de convertê-los em discos geridos |   Em alternativa, em vez de uma função incorporada, pode considerar um papel personalizado que tem as seguintes permissões: Microsoft.Storage/storageAccounts/write |
| | Contribuinte | Grupo de recursos para o qual os discos geridos serão restaurados | Em alternativa, em vez de uma função incorporada, pode considerar uma função personalizada que tem as seguintes permissões: Microsoft.Resources/subscrições/resourceGroups/write|
| Restaurar ficheiros individuais a partir de cópia de segurança VM | Operador de Cópia de Segurança | Cofre dos Serviços de Recuperação |
| | Contribuidor de Máquina Virtual | Fonte VM que foi apoiado | Alternativamente, em vez de um papel incorporado, pode considerar um papel personalizado que tem as seguintes permissões: Microsoft.Compute/virtualMachines/write |
| Restauro da região transversal | Operador de Cópia de Segurança | Assinatura do cofre dos Serviços de Recuperação | Isto é além das permissões de restauro mencionadas acima. Especificamente para a CRR, em vez de um papel embutido, pode considerar uma função personalizada que tem as seguintes permissões: "Microsoft.RecoveryServices/locations/backupAadProperties/read" "Microsoft.RecoveryServices/locations/backupCrrJobs/action" "Microsoft.RecoveryServices/locations/backupCrrJob/ação" "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action" "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read" "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read" |
| Criar política de backup para backup Azure VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Modificar a política de backup da cópia de segurança da Azure VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Eliminar a política de backup do backup da Azure VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Parar a cópia de segurança (com retenção de dados ou apagar dados) na cópia de segurança do VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Registar no local O Servidor/Cliente/CDPM ou O Servidor de Backup Azure | Operador de Cópia de Segurança | Cofre dos Serviços de Recuperação |
| Eliminar o Windows Server/cliente/SCDPM ou O Azure Backup Server | Colaborador de backup | Cofre dos Serviços de Recuperação |

> [!IMPORTANT]
> Se especificar o VM Contributor num âmbito de recursos VM e selecionar **o Backup** como parte das definições de VM, abrirá o ecrã de **Backup Ativa,** mesmo que o VM já esteja apoiado. Isto porque a chamada para verificar o estado de backup funciona apenas ao nível da subscrição. Para evitar isto, vá ao cofre e abra a vista de ponto de cópia de segurança do VM ou especifique a função de Contribuinte VM a nível de subscrição.

### <a name="minimum-role-requirements-for-azure-workload-backups-sql-and-hana-db-backups"></a>Requisitos mínimos de função para backups de carga de trabalho Azure (backups DB SQL e HANA)

O quadro seguinte captura as ações de gestão de Backup e a correspondente função mínima de Azure necessária para executar essa operação.

| Operação de Gestão | Função mínima de Azure necessária | Âmbito necessário | Alternativa |
| --- | --- | --- | --- |
| Criar cofre dos Serviços de Recuperação | Colaborador de backup | Grupo de recursos contendo o cofre |   |
| Ativar a cópia de segurança das bases de dados SQL e/ou HANA | Operador de Cópia de Segurança | Grupo de recursos contendo o cofre |   |
| | Contribuidor de Máquina Virtual | Recurso VM onde a DB está instalada |  Alternativamente, em vez de um papel incorporado, pode considerar um papel personalizado que tem as seguintes permissões: Microsoft.Compute/virtualMachines/write |
| Backup a pedido da DB | Operador de Cópia de Segurança | Cofre dos Serviços de Recuperação |   |
| Restaurar a base de dados ou restaurar como ficheiros | Operador de Cópia de Segurança | Cofre dos Serviços de Recuperação |   |
| | Contribuidor de Máquina Virtual | Fonte VM que foi apoiado |   Alternativamente, em vez de um papel incorporado, pode considerar um papel personalizado que tem as seguintes permissões: Microsoft.Compute/virtualMachines/write |
| | Contribuidor de Máquina Virtual | VM-alvo em que DB será restaurado ou ficheiros são criados |   Alternativamente, em vez de um papel incorporado, pode considerar um papel personalizado que tem as seguintes permissões: Microsoft.Compute/virtualMachines/write |
| Criar política de backup para backup Azure VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Modificar a política de backup da cópia de segurança da Azure VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Eliminar a política de backup do backup da Azure VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Parar a cópia de segurança (com retenção de dados ou apagar dados) na cópia de segurança do VM | Colaborador de backup | Cofre dos Serviços de Recuperação |

### <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Requisitos mínimos de função para o backup de partilha de ficheiros Azure

A tabela seguinte captura as ações de gestão de Backup e o papel correspondente necessário para executar a operação de partilha do Ficheiro Azure.

| Operação de Gestão | Função necessária | Recursos |
| --- | --- | --- |
| Ativar a cópia de segurança das ações do Azure File | Colaborador de backup |Cofre dos Serviços de Recuperação |
| |Conta de Armazenamento | Recurso de conta de armazenamento de contribuinte |
| Backup a pedido da VM | Operador de Cópia de Segurança | Cofre dos Serviços de Recuperação |
| Restaurar a partilha de ficheiros | Operador de Cópia de Segurança | Cofre dos Serviços de Recuperação |
| | Contribuidor de Conta de Armazenamento | Recursos de conta de armazenamento onde restaurar fonte e ações de ficheiros Target estão presentes |
| Restaurar ficheiros individuais | Operador de Cópia de Segurança | Cofre dos Serviços de Recuperação |
| |Contribuidor de Conta de Armazenamento|Recursos de conta de armazenamento onde restaurar fonte e ações de ficheiros Target estão presentes |
| Parar proteção |Colaborador de backup | Cofre dos Serviços de Recuperação |
| Conta de armazenamento não registro do cofre |Colaborador de backup | Cofre dos Serviços de Recuperação |
| |Contribuidor de Conta de Armazenamento | Recurso de conta de armazenamento|

## <a name="next-steps"></a>Passos seguintes

* [Controlo de acesso baseado em funções (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): Começa com o Azure RBAC no portal Azure.
* Saiba como gerir o acesso com:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Azure controlo de acesso baseado em funções resolução de problemas](../role-based-access-control/troubleshooting.md): Obtenha sugestões para corrigir questões comuns.
