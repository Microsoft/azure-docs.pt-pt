---
title: Gerenciar backups com o controle de acesso baseado em função
description: Use o controle de acesso baseado em função para gerenciar o acesso às operações de gerenciamento de backup no cofre dos serviços de recuperação.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: afb8067f2547e3a26a505bb5ec8063dd340e8500
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172193"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Usar o controle de acesso baseado em função para gerenciar pontos de recuperação do backup do Azure

O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Ao utilizar o RBAC, pode segregar funções na sua equipa e conceder apenas a quantidade de acesso a utilizadores que precisam para desempenhar as suas funções.

> [!IMPORTANT]
> As funções fornecidas pelo backup do Azure são limitadas a ações que podem ser executadas em portal do Azure ou por meio da API REST ou dos cmdlets do PowerShell ou do cofre dos serviços de recuperação. As ações executadas na interface do usuário do cliente do agente de backup do Azure ou na interface do usuário do System Center Data Protection Manager ou da interface do Servidor de Backup do Azure estão fora do controle dessas funções.

O backup do Azure fornece três funções internas para controlar as operações de gerenciamento de backup. Obter mais informações sobre [Funções incorporadas do RBAC do Azure](../role-based-access-control/built-in-roles.md)

* [Colaborador de backup](../role-based-access-control/built-in-roles.md#backup-contributor) -essa função tem todas as permissões para criar e gerenciar o backup, exceto excluir o cofre dos serviços de recuperação e conceder acesso a outras pessoas. Imagine essa função como administrador do gerenciamento de backup que pode fazer todas as operações de gerenciamento de backup.
* [Operador de backup](../role-based-access-control/built-in-roles.md#backup-operator) : essa função tem permissões para tudo o que um colaborador faz, exceto remover backup e gerenciar políticas de backup. Essa função é equivalente ao colaborador, exceto que não é possível executar operações destrutivas, como parar o backup com excluir dados ou remover o registro de recursos locais.
* [Leitor de backup](../role-based-access-control/built-in-roles.md#backup-reader) -essa função tem permissões para exibir todas as operações de gerenciamento de backup. Imagine essa função como uma pessoa de monitoramento.

Se você estiver procurando definir suas próprias funções para obter ainda mais controle, consulte como [criar funções personalizadas](../role-based-access-control/custom-roles.md) no RBAC do Azure.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapeando funções internas de backup para ações de gerenciamento de backup

A tabela a seguir captura as ações de gerenciamento de backup e a função RBAC mínima correspondente necessária para executar essa operação.

| Operação de gerenciamento | Função RBAC mínima necessária | Escopo necessário |
| --- | --- | --- |
| Criar cofre dos Serviços de Recuperação | Colaborador de backup | Grupo de recursos que contém o cofre |
| Habilitar o backup de VMs do Azure | Operador de backup | Grupo de recursos que contém o cofre |
| | Colaborador da máquina virtual | Recurso da VM |
| Backup sob demanda da VM | Operador de backup | Recurso do cofre de recuperação |
| Restaurar VM | Operador de backup | Cofre dos Serviços de Recuperação |
| | Contribuinte | Grupo de recursos no qual a VM será implantada |
| | Colaborador da máquina virtual | VM de origem que foi submetida a backup |
| Restaurar backup de VM de discos não gerenciados | Operador de backup | Recurso do cofre de recuperação |
| | Colaborador da máquina virtual | VM de origem que foi submetida a backup |
| | Colaborador da conta de armazenamento | Recurso de conta de armazenamento em que os discos serão restaurados |
| Restaurar discos gerenciados do backup da VM | Operador de backup | Recurso do cofre de recuperação |
| | Colaborador da máquina virtual | VM de origem que foi submetida a backup |
| | Colaborador da conta de armazenamento | Conta de armazenamento temporário selecionada como parte da restauração para armazenar dados do cofre antes de convertê-los em discos gerenciados |
| | Contribuinte | Grupo de recursos para o qual os discos gerenciados serão restaurados |
| Restaurar arquivos individuais do backup da VM | Operador de backup | Recurso do cofre de recuperação |
| | Colaborador da máquina virtual | VM de origem que foi submetida a backup |
| Criar política de backup para backup de VM do Azure | Colaborador de backup | Recurso do cofre de recuperação |
| Modificar a política de backup do backup de VM do Azure | Colaborador de backup | Recurso do cofre de recuperação |
| Excluir a política de backup do backup de VM do Azure | Colaborador de backup | Recurso do cofre de recuperação |
| Parar o backup (com reter dados ou excluir dados) no backup da VM | Colaborador de backup | Recurso do cofre de recuperação |
| Registrar o Windows Server/Client/SCDPM ou Servidor de Backup do Azure local | Operador de backup | Recurso do cofre de recuperação |
| Excluir o Windows Server/Client/SCDPM ou Servidor de Backup do Azure local registrado | Colaborador de backup | Recurso do cofre de recuperação |

> [!IMPORTANT]
> Se você especificar colaborador de VM em um escopo de recurso de VM e clicar em backup como parte das configurações de VM, ele abrirá a tela ' habilitar backup ' mesmo que a VM já tenha sido submetida a backup, pois a chamada para verificar o status de backup funciona apenas no nível de assinatura. Para evitar isso, vá para o cofre e abra a exibição do item de backup da VM ou especifique a função colaborador de VM no nível de assinatura.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Requisitos mínimos de função para o backup do compartilhamento de arquivos do Azure

A tabela a seguir captura as ações de gerenciamento de backup e a função correspondente necessária para executar a operação de compartilhamento de arquivos do Azure.

| Operação de gerenciamento | Função necessária | Recursos |
| --- | --- | --- |
| Habilitar o backup de compartilhamentos de arquivos do Azure | Colaborador de backup |Cofre dos Serviços de Recuperação |
| |Conta de Armazenamento | Recurso de conta de armazenamento do colaborador |
| Backup sob demanda da VM | Operador de backup | Cofre dos Serviços de Recuperação |
| Restaurar compartilhamento de arquivos | Operador de backup | Cofre dos Serviços de Recuperação |
| | Colaborador da conta de armazenamento | Recursos da conta de armazenamento em que os compartilhamentos de arquivos de origem e de destino são presentes |
| Restaurar arquivos individuais | Operador de backup | Cofre dos Serviços de Recuperação |
| |Colaborador da conta de armazenamento|Recursos da conta de armazenamento em que os compartilhamentos de arquivos de origem e de destino são presentes |
| Interromper proteção |Colaborador de backup | Cofre dos Serviços de Recuperação |
| Cancelar o registro da conta de armazenamento do cofre |Colaborador de backup | Cofre dos Serviços de Recuperação |
| |Colaborador da conta de armazenamento | Recurso de conta de armazenamento|

## <a name="next-steps"></a>Passos seguintes

* [Controle de acesso baseado em função](../role-based-access-control/role-assignments-portal.md): introdução ao RBAC no portal do Azure.
* Saiba como gerenciar o acesso com:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Solução de problemas de controle de acesso baseado em função](../role-based-access-control/troubleshooting.md): Obtenha sugestões para corrigir problemas comuns.
