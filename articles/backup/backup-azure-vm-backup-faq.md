---
title: Cópia de segurança VM do Azure FAQ
description: Respostas a perguntas comuns sobre como funcionam as cópias de segurança de VMs do Azure, as limitações e o que ocorre quando há alterações a políticas
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: 10b49c5ebcd73010a52da1fada32ba55198b287a
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961538"
---
# <a name="frequently-asked-questions-azure-backup"></a>Cópia de segurança do Azure de perguntas mais frequentes

Este artigo responde a perguntas comuns sobre o [Azure Backup](backup-introduction-to-azure-backup.md) serviço.

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-azure-vms-can-you-back-up-using-azure-backup"></a>Quais as VMs do Azure pode lhe fazer cópias de segurança utilizando o Azure Backup?
[Revisão](backup-azure-arm-vms-prepare.md#before-you-start) sistemas operativos suportados e limitações.


## <a name="backup"></a>Cópia de segurança

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Uma tarefa de cópia de segurança a pedido utiliza a mesma agenda de retenção como cópias de segurança agendadas?
Não. Deve especificar o período de retenção para uma tarefa de cópia de segurança a pedido. Por predefinição, são retido durante 30 dias quando acionado a partir do portal.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ativei recentemente o Azure Disk Encryption em algumas VMs. As minhas cópias de segurança vão continuar a funcionar?
Tem de fornecer permissões para o Azure Backup aceder ao Key Vault. Especifique as permissões no PowerShell, conforme descrito no **ativar cópia de segurança** secção a [PowerShell de cópia de segurança do Azure](backup-azure-vms-automation.md) documentação.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Migrei discos VM para discos geridos. As minhas cópias de segurança vão continuar a funcionar?
Sim, as cópias de segurança funcionam perfeitamente. Não é necessário reconfigurar a nada.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Por que motivo não vejo a minha VM no Assistente para configurar a cópia de segurança?
O assistente só apresenta VMs na mesma região que o Cofre e que não estão já a cópia de segurança.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>A minha VM está a ser encerrado. Será uma demanda ou de um trabalho de cópia de segurança agendado?
Sim. Executam cópias de segurança quando uma máquina é encerrada. O ponto de recuperação é marcado como falhas consistente.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Pode cancelar uma tarefa de cópia de segurança em curso?
Sim. Pode cancelar a tarefa de cópia de segurança num **tirar instantâneo** estado. Não é possível cancelar uma tarefa, se a transferência de dados a partir do instantâneo está em curso.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie--azurebackuprggeonumber-will-my-backups-continue-to-work"></a>Eu habilitei o bloqueio no grupo de recursos criado pelo serviço de cópia de segurança do Azure (ou seja ` AzureBackupRG_<geo>_<number>`), minhas cópias de segurança continuarão a funcionar?
Se bloquear o grupo de recursos criado pelo serviço de cópia de segurança do Azure, cópias de segurança irão começar a falhar, uma vez que existe um limite máximo de 18 pontos de restauro.

O utilizador tem de remover o bloqueio e desmarque a coleção de ponto de restauro do grupo de recursos para fazer cópias de segurança futuras com êxito, [siga estes passos](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) para remover a coleção de ponto de restauro.

### <a name="does-the-backup-policy-consider-daylight-saving-time-dst"></a>A política de cópia de segurança considerar o horário de Verão (horário de Verão)?
Não. A data e hora no seu computador local é local com o atual Verão aplicada. O tempo definido para cópias de segurança agendadas pode diferir da hora local devido ao horário de Verão.

### <a name="how-many-data-disks-can-i-attach-to-a-vm-backed-up-by-azure-backup"></a>Quantos discos de dados pode anexar a uma VM de uma cópia de segurança do Azure Backup?
Cópia de segurança do Azure pode fazer uma cópia de segurança de VMs com até 16 discos. Suporte para 16 discos é fornecido no [restaurar instantâneas](backup-instant-restore-capability.md).

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Faz o suporte de cópia de segurança do Azure SSD discos geridos standard?
O Azure Backup suporta [discos geridos de standard SSD](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/). SSD gerido os discos fornecem um novo tipo de armazenamento durável para VMs do Azure. Suporte para discos gerido de SSD é fornecido no [restaurar instantâneas](backup-instant-restore-capability.md).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Vamos criar uma VM com um disco WA do acelerador de escrita ativado?
Não não possível efetuar instantâneos no disco habilitados para WA. No entanto, o serviço de cópia de segurança do Azure pode excluir o disco habilitados para WA da cópia de segurança. Exclusão de disco para VMs com discos habilitados para WA só é suportada para subscrições atualizadas para restaurar instantânea.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Tenho uma VM com discos de acelerador de escrita (WA) e SAP HANA instalado. Como posso fazer cópia de segurança?
O Azure Backup não é possível criar cópias de segurança do disco habilitados para WA, mas pode excluir da cópia de segurança. No entanto, a cópia de segurança não fornece consistência da base de dados porque as informações no disco WA-ativada não não uma cópia de segurança. Pode fazer backup de discos com esta configuração se pretender que o disco do sistema de operativo cópia de segurança e a cópia de segurança de discos que não são habilitados para WA.

Temos uma pré-visualização privada para uma cópia de segurança do SAP HANA com um RPO de 15 minutos. Baseia-se de forma semelhante, a cópia de segurança de BD SQL e utiliza a interface de backInt para soluções de terceiros certificadas pelo SAP HANA. Se estiver interessado na pré-visualização privada, envie um e-mail para ` AskAzureBackupTeam@microsoft.com ` com o assunto **Inscreva-se para a pré-visualização privada para cópia de segurança do SAP HANA em VMs do Azure**.


## <a name="restore"></a>Restauro

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Como posso decidir se pretende restaurar discos apenas ou uma VM completa?
Considere um restauro de VM como uma opção de criação rápida para uma VM do Azure. Esta opção alterações de nomes de disco, contentores utilizados pelos discos, endereços IP públicos e os nomes de interface de rede. A alteração mantém recursos exclusivos, quando é criada uma VM. A VM não é adicionada a um conjunto de disponibilidade.

Pode utilizar a opção de disco de restauro para:
  * Personalize as VMS que são criadas. Por exemplo, altere o tamanho.
  * Adicionar definições de configuração que não estavam lá no momento da cópia de segurança
  * Controle a Convenção de nomenclatura para recursos que são criados.
  * Adicione a VM a um conjunto de disponibilidade.
  * Adicione qualquer outra definição tem de ser configurada com o PowerShell ou um modelo.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Pode restaurar cópias de segurança de discos VM não geridos depois de atualizar para o managed disks?
Sim, pode utilizar cópias de segurança criadas antes de discos foram migrados do não gerido para gerido.
- Por predefinição, um trabalho VM de restauro cria uma VM não gerida.
- No entanto, pode restaurar discos e usá-los para criar uma VM gerida.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Como posso restaurar uma VM para um ponto de restauro antes da VM foi migrada para o managed disks?
Por predefinição, um trabalho VM de restauro cria uma VM com discos não geridos. Para criar uma VM com discos geridos:
1. [Restaurar para discos não geridos](tutorial-restore-disk.md#restore-a-vm-disk).
2. [Converta os discos restaurados em discos geridos](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [Criar uma VM com discos geridos](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Saiba mais](backup-azure-vms-automation.md#restore-an-azure-vm) sobre como fazer isso no PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Pode restaurar a VM que é eliminada?
Sim. Mesmo que a VM, pode aceder à cópia de segurança correspondente do item no cofre e restaurar a partir de um ponto de recuperação.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Como restaurar uma VM para os conjuntos de disponibilidade mesmo?
Restauro para os conjuntos de disponibilidade está ativada para a VM de Azure de disco gerido, ao fornecer uma opção no modelo enquanto restaurava como discos geridos. Este modelo tem o parâmetro de entrada chamado **conjuntos de disponibilidade**.

### <a name="how-do-we-get-faster-restore-performances"></a>Como podemos obter os desempenhos de restauro mais rápidos?
Para acelerar o desempenho de restauro, vamos mudar para o [restaurar instantâneas](backup-instant-restore-capability.md) capacidade.

## <a name="manage-vm-backups"></a>Gerir cópias de segurança de VMs

### <a name="what-happens-if-i-modify-a-backup-policy"></a>O que acontece se eu modificar uma política de cópia de segurança?
A VM é apoiada utilizando as definições de agendamento e retenção na política de nova ou modificada.

- Se a retenção for estendida, pontos de recuperação existentes são marcados e mantidos em conformidade com a nova política.
- Se for reduzida, pontos de recuperação são marcados para eliminação na tarefa de limpeza seguinte e, posteriormente, eliminados.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Como faço para mover uma VM de uma cópia de segurança do Azure Backup para um grupo de recursos diferente?

1. Temporariamente parar a cópia de segurança e manter os dados de cópia de segurança.
2. Mova a VM para o grupo de recursos de destino.
3. Cópia de segurança reenabled no mesmo ou novo cofre.

Pode restaurar a VM a partir de pontos de restauração disponíveis que foram criados antes da operação de movimentação.
