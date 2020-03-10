---
title: Gerir backups com controlo de acesso baseado em funções
description: Utilize o Controlo de Acesso baseado em Funções para gerir o acesso a operações de gestão de backup no cofre dos Serviços de Recuperação.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: e2e32ac6981635e3b9885119fdf397783ac32cc9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363764"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Utilize o Controlo de Acesso baseado em funções para gerir pontos de recuperação de backup do Azure

O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Ao utilizar o RBAC, pode segregar funções na sua equipa e conceder apenas a quantidade de acesso a utilizadores que precisam para desempenhar as suas funções.

> [!IMPORTANT]
> As funções fornecidas pela Azure Backup limitam-se a ações que podem ser executadas no portal Azure ou através do cofre de Serviços de Recuperação Da REST API ou dos Serviços de Recuperação PowerShell ou CLI. As ações realizadas em Azure backup Agent Client UI ou System center Data Protection Manager UI ou Azure Backup Server UI estão fora de controlo destas funções.

A Azure Backup fornece três funções incorporadas para controlar as operações de gestão de backup. Obter mais informações sobre [Funções incorporadas do RBAC do Azure](../role-based-access-control/built-in-roles.md)

* [Backup Contributor](../role-based-access-control/built-in-roles.md#backup-contributor) - Esta função tem todas as permissões para criar e gerir backup, exceto apagar o cofre dos Serviços de Recuperação e dar acesso a outros. Imagine este papel como administrador da gestão de backup que pode fazer todas as operações de gestão de backup.
* [Backup Operator](../role-based-access-control/built-in-roles.md#backup-operator) - Esta função tem permissões para tudo o que um colaborador faz, exceto remover as políticas de backup e gestão de políticas de backup. Esta função é equivalente ao contribuinte, exceto que não pode realizar operações destrutivas, tais como parar a cópia de segurança com eliminar dados ou remover o registo de recursos no local.
* [Backup Reader](../role-based-access-control/built-in-roles.md#backup-reader) - Este papel tem permissões para visualizar todas as operações de gestão de backup. Imagine este papel como uma pessoa de monitorização.

Se procura definir os seus próprios papéis para ainda mais controlo, veja como [construir papéis personalizados](../role-based-access-control/custom-roles.md) no Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapeando papéis incorporados de backup para ações de gestão de backup

O quadro seguinte captura as ações de gestão de Backup e a função rBAC mínima correspondente necessária para executar essa operação.

| Operação de Gestão | Função RBAC mínima necessária | Âmbito necessário |
| --- | --- | --- |
| Criar cofre dos Serviços de Recuperação | Colaborador de backup | Grupo de recursos contendo o cofre |
| Ativar backup de VMs Azure | Operador de backup | Grupo de recursos contendo o cofre |
| | Colaborador de Máquina Virtual | Recurso VM |
| Backup a pedido da VM | Operador de backup | Cofre dos Serviços de Recuperação |
| Restaurar vM | Operador de backup | Cofre dos Serviços de Recuperação |
| | Contribuinte | Grupo de recursos no qual a VM será implantada |
| | Colaborador de Máquina Virtual | Fonte VM que foi apoiada |
| Restaurar a cópia de segurança VM dos discos não geridos | Operador de backup | Cofre dos Serviços de Recuperação |
| | Colaborador de Máquina Virtual | Fonte VM que foi apoiada |
| | Contribuinte de Conta de Armazenamento | Recurso da conta de armazenamento onde os discos vão ser restaurados |
| Restaurar os discos geridos a partir de backup VM | Operador de backup | Cofre dos Serviços de Recuperação |
| | Colaborador de Máquina Virtual | Fonte VM que foi apoiada |
| | Contribuinte de Conta de Armazenamento | Conta de Armazenamento Temporário selecionada como parte da restauração para reter dados do cofre antes de convertê-los em discos geridos |
| | Contribuinte | Grupo de recursos para o qual o disco gerido será restaurado |
| Restaurar ficheiros individuais a partir de backup VM | Operador de backup | Cofre dos Serviços de Recuperação |
| | Colaborador de Máquina Virtual | Fonte VM que foi apoiada |
| Criar política de backup para backup Azure VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Modificar a política de backup do backup Azure VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Eliminar a política de backup do backup Azure VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Parar a cópia de segurança (com dados de retenção ou eliminar dados) na cópia de segurança vM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Registe-se no local Windows Server/cliente/SCDPM ou Azure Backup Server | Operador de backup | Cofre dos Serviços de Recuperação |
| Eliminar registado no local O Servidor/Cliente/Cliente/SCDPM ou o Servidor de Backup Azure | Colaborador de backup | Cofre dos Serviços de Recuperação |

> [!IMPORTANT]
> Se especificar o Colaborador VM num âmbito de recursos VM e clicar em Backup como parte das definições vM, abrirá o ecrã 'Enable Backup', mesmo que o VM já esteja apoiado, uma vez que a chamada para verificar se o estado de backup funciona apenas a nível de subscrição. Para evitar isto, vá ao cofre e abra a vista de cópia de segurança do VM ou especifique a função do Colaborador VM a um nível de subscrição.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Requisitos mínimos de função para o backup de ações do Ficheiro Azure

A tabela seguinte captura as ações de gestão de Backup e a função correspondente necessária para executar a operação de partilha de ficheiros Azure.

| Operação de Gestão | Papel necessário | Recursos |
| --- | --- | --- |
| Ativar a cópia de segurança das ações do Ficheiro Azure | Colaborador de backup |Cofre dos Serviços de Recuperação |
| |Conta de Armazenamento | Recurso da conta de armazenamento do contribuinte |
| Backup a pedido da VM | Operador de backup | Cofre dos Serviços de Recuperação |
| Restaurar a partilha de ficheiros | Operador de backup | Cofre dos Serviços de Recuperação |
| | Contribuinte de Conta de Armazenamento | Recursos da conta de armazenamento onde restaurar fonte e ações de ficheiros Target estão presentes |
| Restaurar ficheiros individuais | Operador de backup | Cofre dos Serviços de Recuperação |
| |Contribuinte de Conta de Armazenamento|Recursos da conta de armazenamento onde restaurar fonte e ações de ficheiros Target estão presentes |
| Parar a proteção |Colaborador de backup | Cofre dos Serviços de Recuperação |
| Desregistar conta de armazenamento a partir do cofre |Colaborador de backup | Cofre dos Serviços de Recuperação |
| |Contribuinte de Conta de Armazenamento | Recurso de conta de armazenamento|

## <a name="next-steps"></a>Passos Seguintes

* [Controlo de Acesso Baseado em Funções](../role-based-access-control/role-assignments-portal.md): Comece com o RBAC no portal Azure.
* Saiba como gerir o acesso com:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Resolução de problemas do Controlo de Acesso baseado em papéis](../role-based-access-control/troubleshooting.md): Obtenha sugestões para corrigir questões comuns.
