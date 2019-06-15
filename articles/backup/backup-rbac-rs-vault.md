---
title: Gerir cópias de segurança com controlo de acesso baseado em funções do Azure "
description: Utilize o controlo de acesso baseado em funções para gerir o acesso às operações de gestão de cópia de segurança no cofre dos serviços de recuperação.
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: trinadhk
ms.openlocfilehash: ed3797183e13a00d2c5381fa6449c111c3bc9ab9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60253734"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Utilizar o controlo de acesso baseado em funções para gerir pontos de recuperação de cópia de segurança do Azure
O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Ao utilizar o RBAC, pode segregar funções na sua equipa e conceder apenas a quantidade de acesso a utilizadores que precisam para desempenhar as suas funções.

> [!IMPORTANT]
> Funções fornecidas pelo Azure Backup estão limitadas a ações que podem ser executadas no portal do Azure ou através da REST API ou cmdlets do PowerShell ou a CLI do cofre dos serviços de recuperação. Ações executadas no Azure cópia de segurança Centro de interface do Usuário do agente de cliente ou sistema da IU do Gestor de proteção de dados ou IU de servidor de cópia de segurança do Azure estão fora do controlo de uma destas funções.

Cópia de segurança do Azure fornece três funções incorporadas para controlar as operações de gestão de cópia de segurança. Obter mais informações sobre [Funções incorporadas do RBAC do Azure](../role-based-access-control/built-in-roles.md)

* [Contribuidor de cópia de segurança](../role-based-access-control/built-in-roles.md#backup-contributor) -esta função tem todas as permissões para criar e gerir a cópia de segurança, exceto criar cofre dos serviços de recuperação e fornecer acesso a outras pessoas. Imagine esta função como administrador da gestão de cópia de segurança que pode fazer todas as operações de gestão de cópia de segurança.
* [Operador de cópia de segurança](../role-based-access-control/built-in-roles.md#backup-operator) -esta função tem permissões para tudo o que um contribuinte exceto de remoção de cópia de segurança e a gerir políticas de cópia de segurança. Esta função é equivalente ao Contribuidor, exceto que ele não é possível efetuar operações destrutivas como parar cópia de segurança com dados de eliminar ou remover registo de recursos no local.
* [Leitor de cópia de segurança](../role-based-access-control/built-in-roles.md#backup-reader) -esta função tem permissões para ver todas as operações de gestão de cópia de segurança. Imagine esta função para ser uma pessoa de monitorização.

Se estiver procurando para definir suas próprias funções para o controle ainda mais, veja como [criar funções personalizadas](../role-based-access-control/custom-roles.md) no RBAC do Azure.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>O mapeamento de funções de cópia de segurança incorporadas para ações de gestão de cópia de segurança
A tabela seguinte mostra as ações de gestão de cópia de segurança e correspondente função RBAC mínimo necessário para executar essa operação.

| Operação de gestão | Função RBAC mínima necessária | Âmbito necessário |
| --- | --- | --- |
| Criar cofre dos Serviços de Recuperação | Contribuinte | Grupo de recursos com o Cofre |
| Ativar cópia de segurança de VMs do Azure | Operador de cópia de segurança | Grupo de recursos com o Cofre |
| | Contribuinte de máquina virtual | Recurso de VM |
| Cópia de segurança a pedido da VM | Operador de cópia de segurança | Recurso do Cofre de recuperação |
| Restaurar VM | Operador de cópia de segurança | Cofre dos Serviços de Recuperação |
| | Contribuinte | Grupo de recursos em que a VM irá ser implementada |
| | Contribuinte de máquina virtual | Origem de VM da qual foi feita uma cópia de segurança |
| Restaurar cópia de segurança VM de discos não geridos | Operador de cópia de segurança | Recurso do Cofre de recuperação |
| | Contribuinte de máquina virtual | Origem de VM da qual foi feita uma cópia de segurança |
| | Contribuinte de conta de armazenamento | Recurso de conta de armazenamento onde for possível restaurar discos |
| Restaurar discos geridos a partir de cópia de segurança VM | Operador de cópia de segurança | Recurso do Cofre de recuperação |
| | Contribuinte de máquina virtual | Origem de VM da qual foi feita uma cópia de segurança |
| | Contribuinte de conta de armazenamento | Conta de armazenamento temporária selecionada como parte do restauro para armazenar os dados do cofre antes de convertê-los para discos geridos |
| | Contribuinte | Grupo de recursos para o qual vão ser restaurados discos geridos |
| Restaurar ficheiros individuais a partir de cópia de segurança VM | Operador de cópia de segurança | Recurso do Cofre de recuperação |
| | Contribuinte de máquina virtual | Origem de VM da qual foi feita uma cópia de segurança |
| Criar política de cópia de segurança para cópia de segurança de VM do Azure | Contribuidor de cópia de segurança | Recurso do Cofre de recuperação |
| Modificar a política de cópia de segurança da cópia de segurança de VM do Azure | Contribuidor de cópia de segurança | Recurso do Cofre de recuperação |
| Eliminar política de cópia de segurança da cópia de segurança de VM do Azure | Contribuidor de cópia de segurança | Recurso do Cofre de recuperação |
| Parar cópia de segurança (mantendo os dados ou eliminar dados) na cópia de segurança VM | Contribuidor de cópia de segurança | Recurso do Cofre de recuperação |
| Registre-se no local Windows servidor SCDPM/cliente ou servidor de cópia de segurança do Azure | Operador de cópia de segurança | Recurso do Cofre de recuperação |
| Eliminar registados no local Windows servidor SCDPM/cliente ou servidor de cópia de segurança do Azure | Contribuidor de cópia de segurança | Recurso do Cofre de recuperação |

> [!IMPORTANT]
> Se especifica Contribuidor de VM com um âmbito de recursos VM e clique em cópia de segurança como parte das definições de VM, abrirá a tela "Ativar cópia de segurança", mesmo que a VM já foi efetuada como a chamada para verificar o estado cópia de segurança funciona apenas no nível de subscrição. Para evitar isto, ou aceda ao Cofre e abrir a vista de item de cópia de segurança da VM ou especificar a função de Contribuidor de VM ao nível da subscrição.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Requisitos mínimos de função para a cópia de segurança de partilha de ficheiros do Azure
A tabela seguinte mostra as ações de gestão de cópia de segurança e a função correspondente necessário para efetuar a operação de partilha de ficheiros do Azure.

| Operação de gestão | Função necessária | Recursos |
| --- | --- | --- |
| Ativar cópia de segurança de partilhas de ficheiros do Azure | Contribuidor de cópia de segurança | Cofre dos Serviços de Recuperação |
| | Conta de Armazenamento | Recurso de conta de armazenamento do contribuinte |
| Cópia de segurança a pedido da VM | Operador de cópia de segurança | Cofre dos Serviços de Recuperação |
| Restaurar a partilha de ficheiros | Operador de cópia de segurança | Cofre dos Serviços de Recuperação |
| | Contribuinte de conta de armazenamento | Recursos de conta de armazenamento em que a origem de restauro e partilhas de ficheiros de destino estão presentes |
| Restaurar ficheiros individuais | Operador de cópia de segurança | Cofre dos Serviços de Recuperação |
| | Contribuinte de conta de armazenamento |   Recursos de conta de armazenamento em que a origem de restauro e partilhas de ficheiros de destino estão presentes |
| Parar proteção | Contribuidor de cópia de segurança | Cofre dos Serviços de Recuperação |      
| Anular o registo de conta de armazenamento de cofre |   Contribuidor de cópia de segurança | Cofre dos Serviços de Recuperação |
| | Contribuinte de conta de armazenamento | Recurso de conta de armazenamento|


## <a name="next-steps"></a>Passos Seguintes
* [Controlo de acesso baseado em funções](../role-based-access-control/role-assignments-portal.md): Comece com RBAC no portal do Azure.
* Saiba como gerir o acesso com:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Resolução de problemas de controlo de acesso baseado em funções](../role-based-access-control/troubleshooting.md): Obtenha sugestões para corrigir problemas comuns.
