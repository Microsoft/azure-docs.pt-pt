---
title: Gerir backups com controlo de acesso baseado em funções
description: Use o Control de Acesso baseado em funções para gerir o acesso a operações de gestão de backup no cofre dos Serviços de Recuperação.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 89e5cf7c6b46d63b10bd358fc078f8f87db689c8
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371707"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Use o Controlo de Acesso Baseado em Funções para gerir pontos de recuperação de backup do Azure

O controlo de acesso baseado em funções Azure (Azure RBAC) permite uma gestão de acesso de grãos finos para o Azure. Ao utilizar o RBAC, pode segregar funções na sua equipa e conceder apenas a quantidade de acesso a utilizadores que precisam para desempenhar as suas funções.

> [!IMPORTANT]
> As funções fornecidas pela Azure Backup estão limitadas a ações que podem ser executadas no portal Azure ou através de cofres de API ou Serviços de Recuperação powerShell ou CLI. As ações realizadas no Azure backup Agent Client UI ou System center Data Protection Manager UI ou Azure Backup Server UI estão fora de controlo destas funções.

O Azure Backup fornece três funções incorporadas para controlar as operações de gestão de backup. Saiba mais sobre [os papéis embutidos no Azure](../role-based-access-control/built-in-roles.md)

* [Backup Contributor](../role-based-access-control/built-in-roles.md#backup-contributor) - Esta função tem todas as permissões para criar e gerir backup, exceto eliminar o cofre dos Serviços de Recuperação e dar acesso a outros. Imagine este papel como administrador de gestão de backup que pode fazer todas as operações de gestão de backup.
* [Operador de Backup](../role-based-access-control/built-in-roles.md#backup-operator) - Esta função tem permissões para tudo o que um contribuinte faz exceto remover backup e gerir políticas de backup. Esta função é equivalente ao contribuinte, exceto que não pode realizar operações destrutivas, tais como parar a cópia de segurança com eliminar dados ou remover o registo de recursos no local.
* [Backup Reader](../role-based-access-control/built-in-roles.md#backup-reader) - Esta função tem permissões para visualizar todas as operações de gestão de backup. Imagine este papel como uma pessoa de monitorização.

Se procura definir os seus próprios papéis para ainda mais controlo, veja como [construir papéis personalizados](../role-based-access-control/custom-roles.md) no Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapeamento de papéis incorporados de Backup para ações de gestão de backup

O quadro seguinte captura as ações de gestão de backup e a correspondente função mínima de RBAC necessária para executar essa operação.

| Operação de Gestão | Papel mínimo rbac necessário | Âmbito necessário |
| --- | --- | --- |
| Criar cofre dos Serviços de Recuperação | Colaborador de backup | Grupo de recursos contendo o cofre |
| Ativar o backup dos VMs Azure | Operador de backup | Grupo de recursos contendo o cofre |
| | Contribuidor de Máquina Virtual | Recurso VM |
| Backup a pedido da VM | Operador de backup | Cofre dos Serviços de Recuperação |
| Restaurar VMs | Operador de backup | Cofre dos Serviços de Recuperação |
| | Contribuinte | Grupo de recursos no qual a VM será implantada |
| | Contribuidor de Máquina Virtual | Fonte VM que foi apoiado |
| Restaurar a cópia de segurança dos discos não geridos VM | Operador de backup | Cofre dos Serviços de Recuperação |
| | Contribuidor de Máquina Virtual | Fonte VM que foi apoiado |
| | Contribuidor de Conta de Armazenamento | Recurso de conta de armazenamento onde os discos vão ser restaurados |
| Restaurar discos geridos a partir de backup VM | Operador de backup | Cofre dos Serviços de Recuperação |
| | Contribuidor de Máquina Virtual | Fonte VM que foi apoiado |
| | Contribuidor de Conta de Armazenamento | Conta de Armazenamento Temporário selecionada como parte da restauração para reter dados do cofre antes de convertê-los em discos geridos |
| | Contribuinte | Grupo de recursos para o qual os discos geridos serão restaurados |
| Restaurar ficheiros individuais a partir de cópia de segurança VM | Operador de backup | Cofre dos Serviços de Recuperação |
| | Contribuidor de Máquina Virtual | Fonte VM que foi apoiado |
| Criar política de backup para backup Azure VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Modificar a política de backup da cópia de segurança da Azure VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Eliminar a política de backup do backup da Azure VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Parar a cópia de segurança (com retenção de dados ou apagar dados) na cópia de segurança do VM | Colaborador de backup | Cofre dos Serviços de Recuperação |
| Registar no local O Servidor/Cliente/CDPM ou O Servidor de Backup Azure | Operador de backup | Cofre dos Serviços de Recuperação |
| Eliminar o Windows Server/cliente/SCDPM ou O Azure Backup Server | Colaborador de backup | Cofre dos Serviços de Recuperação |

> [!IMPORTANT]
> Se especificar o VM Contributor num âmbito de recursos VM e clicar em Backup como parte das definições de VM, abrirá o ecrã 'Enable Backup', mesmo que o VM já esteja apoiado como a chamada para verificar o estado de backup funciona apenas ao nível da subscrição. Para evitar isto, vá ao cofre e abra a vista de ponto de cópia de segurança do VM ou especifique a função VM Contributor a nível de subscrição.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Requisitos mínimos de função para o backup de partilha de ficheiros Azure

A tabela seguinte captura as ações de gestão de Backup e o papel correspondente necessário para executar a operação de partilha do Ficheiro Azure.

| Operação de Gestão | Função necessária | Recursos |
| --- | --- | --- |
| Ativar a cópia de segurança das ações do Azure File | Colaborador de backup |Cofre dos Serviços de Recuperação |
| |Conta de Armazenamento | Recurso de conta de armazenamento de contribuinte |
| Backup a pedido da VM | Operador de backup | Cofre dos Serviços de Recuperação |
| Restaurar a partilha de ficheiros | Operador de backup | Cofre dos Serviços de Recuperação |
| | Contribuidor de Conta de Armazenamento | Recursos de conta de armazenamento onde restaurar fonte e ações de ficheiros Target estão presentes |
| Restaurar ficheiros individuais | Operador de backup | Cofre dos Serviços de Recuperação |
| |Contribuidor de Conta de Armazenamento|Recursos de conta de armazenamento onde restaurar fonte e ações de ficheiros Target estão presentes |
| Parar proteção |Colaborador de backup | Cofre dos Serviços de Recuperação |
| Conta de armazenamento não registro do cofre |Colaborador de backup | Cofre dos Serviços de Recuperação |
| |Contribuidor de Conta de Armazenamento | Recurso de conta de armazenamento|

## <a name="next-steps"></a>Passos seguintes

* [Controle de Acesso Baseado em Função](../role-based-access-control/role-assignments-portal.md): Começa com o RBAC no portal Azure.
* Saiba como gerir o acesso com:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Resolução de problemas do Controlo de Acesso Baseado em Função:](../role-based-access-control/troubleshooting.md)Obtenha sugestões para corrigir questões comuns.
