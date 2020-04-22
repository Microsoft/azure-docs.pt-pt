---
title: FAQ - Backup Up Azure VMs
description: Neste artigo, descubra respostas a perguntas comuns sobre o backup de VMs Azure com o serviço de backup Azure.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: accfc57055f70254814c889de875f5360878bcd9
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757468"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Perguntas frequentes -Back up Azure VMs

Este artigo responde a perguntas comuns sobre o backup de VMs Azure com o serviço [de backup Azure.](backup-introduction-to-azure-backup.md)

## <a name="backup"></a>Cópia de segurança

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Que imagens vm podem ser ativadas para backup quando as crio?

Quando cria um VM, pode ativar a cópia de segurança para os VMs que executam [sistemas operativos suportados](backup-support-matrix-iaas.md#supported-backup-actions).

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>O custo de reserva está incluído no custo da VM?

Não. Os custos de reserva são separados dos custos de um VM. Saiba mais sobre os preços de [Backup Azure.](https://azure.microsoft.com/pricing/details/backup/)

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Que permissões são necessárias para permitir o backup de um VM?

Se for um colaborador vm, pode ativar o backup no VM. Se estiver a usar uma função personalizada, precisa das seguintes permissões para permitir a cópia de segurança no VM:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContentors/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContentors/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContentors/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Se o seu cofre de Serviços de Recuperação e VM tiverem diferentes grupos de recursos, certifique-se de que tem permissões de escrita no grupo de recursos para o cofre dos Serviços de Recuperação.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Um trabalho de reserva a pedido usa o mesmo horário de retenção que os backups programados?

Não. Especifique o intervalo de retenção para um trabalho de reserva a pedido. Por padrão, é retido por 30 dias quando disparado do portal.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ativei recentemente o Azure Disk Encryption em algumas VMs. As minhas cópias de segurança vão continuar a funcionar?

Forneça permissões para que o Azure Backup aceda ao Cofre chave. Especifique as permissões no PowerShell conforme descrito na secção **de backup Enable** na documentação PowerShell de backup [Azure.](backup-azure-vms-automation.md)

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Emigrei discos VM para gerir discos. As minhas cópias de segurança vão continuar a funcionar?

Sim, os reforços funcionam perfeitamente. Não há necessidade de reconfigurar nada.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Por que motivo não vejo a minha VM no assistente de Configuração de Cópias de Segurança?

O feiticeiro só lista VMs na mesma região que o cofre, e que ainda não estão a ser apoiados.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>O meu VM está desligado. Um trabalho de reserva a pedido ou um trabalho de reserva programado?

Sim. As cópias de segurança funcionam quando uma máquina é desligada. O ponto de recuperação é marcado como consistente em acidentes.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Posso cancelar um trabalho de reserva em andamento?

Sim. Pode cancelar o trabalho de reserva num estado de **tirar fotografias.** Não pode cancelar um trabalho se a transferência de dados do instantâneo estiver em andamento.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Ativei um bloqueio no grupo de recursos criado pelo `AzureBackupRG_<geo>_<number>`Serviço de Backup Azure (por exemplo, ). As minhas cópias de segurança vão continuar a funcionar?

Se bloquear o grupo de recursos criado pelo Serviço de Backup Azure, as cópias de segurança começarão a falhar, uma vez que existe um limite máximo de 18 pontos de restauro.

Retire a fechadura e limpe a recolha do ponto de restauro desse grupo de recursos para que as futuras cópias de segurança possam ter sucesso. [Siga estes passos](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) para remover a recolha do ponto de restauro.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Os discos de backup azure suportam discos standard geridos pela SSD?

Sim, o Azure Backup suporta [discos geridos padrão sSD](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Podemos fazer um VM com um disco ativado por um acelerador de escrita (WA)?

As fotos não podem ser tiradas no disco ativado pela WA. No entanto, o serviço de backup Azure pode excluir o disco ativado por WA de cópia de segurança.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Tenho um VM com discos de Acelerador de Escrita (WA) e sAP HANA instalados. Como posso recuar?

O Azure Backup não pode fazer backup no disco ativado pela WA, mas pode excluí-lo da cópia de segurança. No entanto, a cópia de segurança não fornecerá consistência na base de dados porque a informação sobre o disco ativado pela WA não está apoiada. Pode fazer backup de discos com esta configuração se pretender a cópia de segurança do disco do sistema operativo e a cópia de segurança de discos que não estejam ativados por WA.

O Azure Backup fornece uma solução de backup de streaming para bases de dados SAP HANA com um RPO de 15 minutos. É backint certificado pela SAP para fornecer um suporte de apoio nativo alavancando as API nativas de SAP HANA. Saiba mais sobre o backup das bases de [dados SAP HANA em VMs Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-about).

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Qual é o atraso máximo que posso esperar no tempo de arranque do tempo de reserva programado que dei na minha política de backup vm?

O backup programado será acionado dentro de 2 horas após o tempo de reserva programado. Por exemplo, se 100 VMs tiverem o seu horário de arranque agendado às 2:00 da manhã, então às 4:00 da manhã, o mais tardar, todos os 100 VMs terão o seu trabalho de backup em andamento. Se as cópias de segurança programadas tiverem sido interrompidas devido a uma paragem e retomadas ou retomadas, então o backup pode começar fora desta janela programada de duas horas.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Qual é o intervalo mínimo de retenção permitido para um ponto de reserva diário?

A política de backup da Máquina Virtual Azure suporta um intervalo mínimo de retenção de sete dias a 9999 dias. Qualquer modificação a uma política de backup VM existente com menos de sete dias exigirá uma atualização para cumprir o intervalo mínimo de retenção de sete dias.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>O que acontece se eu mudar o caso do meu VM ou do meu grupo de recursos VM?

Se alterar o caso (para cima ou para baixo) do seu grupo de recursos VM ou VM, o caso do nome do item de reserva não se altera. No entanto, espera-se que este comportamento de Backup Azure. A mudança de caso não aparecerá no item de reserva, mas é atualizada no backend.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>Posso recuar ou restaurar discos seletivos ligados a um VM?

O Azure Backup suporta agora a cópia de segurança seletiva do disco e restaura utilizando a solução de backup da Máquina Virtual Azure.

Hoje em dia, o Azure Backup suporta o backup de todos os discos (sistema operativo e dados) num VM juntos utilizando a solução de backup da Máquina Virtual. Com a funcionalidade de exclusão de disco, obtém-se a opção de fazer o back-up de um ou alguns dos muitos discos de dados de um VM. Isto fornece uma solução eficiente e rentável para as suas necessidades de backup e restauro. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite ainda ter um subconjunto de discos restaurado sabotado a partir do ponto de recuperação dado durante a operação de restauro. Isto aplica-se para restaurar tanto a partir do instantâneo como do cofre.

Para se inscrever para a pré-visualização, escreva-nos emAskAzureBackupTeam@microsoft.com

## <a name="restore"></a>Restauro

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Como decido se restaurei apenas discos ou um VM completo?

Pense num vM restaurado como uma opção de criação rápida para um VM Azure. Esta opção altera nomes de discos, contentores utilizados pelos discos, endereços IP públicos e nomes de interface de rede. A mudança mantém recursos únicos quando um VM é criado. O VM não é adicionado a um conjunto de disponibilidade.

Pode utilizar a opção restaurar o disco se quiser:

- Personalize o VM que é criado. Por exemplo, mude o tamanho.
- Adicione configurações de configuração que não estavam lá no momento da cópia de segurança.
- Controle a convenção de nomeação dos recursos criados.
- Adicione o VM a um conjunto de disponibilidade.
- Adicione qualquer outra definição que deve ser configurada usando powerShell ou um modelo.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Posso restaurar cópias de segurança de discos VM não geridos depois de fazer upgrade para discos geridos?

Sim, pode usar cópias de segurança tiradas antes de os discos serem migrados de sem gestão.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Como posso restaurar uma VM para um ponto de restauro antes de a VM ter sido migrada para discos geridos?

O processo de restauro continua o mesmo. Se o ponto de recuperação for de um ponto-a-tempo em que a VM tinha discos não geridos, pode [restaurar os discos como não geridos](tutorial-restore-disk.md#unmanaged-disks-restore). Se o VM tivesse gerido discos, então pode [restaurar os discos como discos geridos](tutorial-restore-disk.md#managed-disk-restore). Depois pode [criar um VM a partir desses discos.](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)

[Saiba mais](backup-azure-vms-automation.md#restore-an-azure-vm) sobre fazer isto na PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Posso restaurar o VM que foi apagado?

Sim. Mesmo que apague o VM, pode ir ao item de reserva correspondente no cofre e restaurar a partir de um ponto de recuperação.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Como posso restaurar um VM para os mesmos conjuntos de disponibilidade?

Para VMs De Disquetes Geridos, restaurar os conjuntos de disponibilidade está ativado fornecendo uma opção no modelo enquanto restaura como discos geridos. Este modelo tem o parâmetro de entrada chamado **conjuntos**de disponibilidade .

### <a name="how-do-we-get-faster-restore-performances"></a>Como é que conseguimos restaurar as performances mais rápidas?

A capacidade [de restauro instantâneo](backup-instant-restore-capability.md) ajuda com backups mais rápidos e restauros instantâneos a partir dos instantâneos.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>O que acontece quando mudamos as definições do cofre para o VM encriptado?

Depois de alterar as definições do cofre de chaves para o VM encriptado, as cópias de segurança continuarão a funcionar com o novo conjunto de detalhes. No entanto, após o restauro de um ponto de recuperação antes da mudança, terá de restaurar os segredos num cofre chave antes de poder criar o VM a partir dele. Para mais informações, consulte este [artigo.](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret)

Operações como o roll-over secreto/chave não requerem este passo e o mesmo KeyVault pode ser usado após o restauro.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Posso aceder ao VM uma vez restaurado devido a um VM ter quebrado a relação com o controlador de domínio?

Sim, você acede ao VM uma vez restaurado devido a um VM ter quebrado a relação com o controlador de domínio. Para mais informações, consulte este [artigo](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps)

## <a name="manage-vm-backups"></a>Gerir cópias de segurança de VMs

### <a name="what-happens-if-i-modify-a-backup-policy"></a>O que acontece se eu modificar uma política de reserva?

O VM é apoiado utilizando as definições de horário e retenção na política modificada ou nova.

- Se a retenção for alargada, os pontos de recuperação existentes são marcados e mantidos de acordo com a nova política.
- Se a retenção for reduzida, os pontos de recuperação são marcados para poda no próximo trabalho de limpeza e posteriormente eliminados.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Como posso mover um VM apoiado pelo Azure Backup para um grupo de recursos diferente?

1. Pare temporariamente a cópia de segurança e guarde os dados de backup.
2. Mova o VM para o grupo de recursos alvo.
3. Reativar a cópia de segurança no mesmo cofre ou no cofre novo.

Pode restaurar o VM dos pontos de restauro disponíveis que foram criados antes da operação de mudança.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>Existe um limite para o número de VMs que podem ser associados à mesma política de backup?

Sim, há um limite de 100 VMs que podem ser associados à mesma política de backup do portal. Recomendamos que para mais de 100 VMs, crie múltiplas políticas de backup com o mesmo horário ou horário diferente.
