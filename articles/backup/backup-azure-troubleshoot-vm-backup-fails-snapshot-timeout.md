---
title: Problemas de agente de resolução de problemas e problemas de extensão
description: Sintomas, causas e resoluções de falhas de Backup Azure relacionadas com agente, extensão e discos.
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: 0313394ad149460f82c98c63cab95b922b4a3da2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519610"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Falha de backup do Azure: Problemas com o agente ou extensão

Este artigo fornece medidas de resolução de problemas que podem ajudá-lo a resolver erros de Backup Azure relacionados com a comunicação com o agente VM e extensão.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="step-by-step-guide-to-troubleshoot-backup-failures"></a>Guia passo a passo para resolução de falhas de backup

As falhas de backup mais comuns podem ser auto-resolvidas seguindo as etapas de resolução de problemas listadas abaixo:

### <a name="step-1-check-azure-vm-health"></a>Passo 1: Verifique a saúde do Azure VM

- **Certifique-se de que o estado de provisionamento Azure VM está em "funcionamento"**: Se o [estado de provisionamento VM](../virtual-machines/states-billing.md) estiver no estado **de paragem/deallocated/atualização,** então interferirá com a operação de backup. O portal Open *Azure > VM > visão geral >* e verifique o estado de VM para garantir que está **a funcionar**  e a tentar novamente a operação de backup.
- **Reveja as atualizações ou reboots do SO pendentes**: Certifique-se de que não existem atualizações de SO pendentes ou reinicializações pendentes no VM.

### <a name="step-2-check-azure-vm-guest-agent-service-health"></a>Passo 2: Verifique a saúde do serviço de agente convidado Azure VM

- **Certifique-se de que o serviço de Agente Convidado Azure VM é iniciado e atualizado:**
  - Num VM do Windows:
    - Navegue para **serviços.msc** e certifique-se de que **o serviço de Agente Convidado VM do Windows Azure** está em funcionamento. Além disso, certifique-se de que a [versão mais recente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) está instalada. Para saber mais, consulte [os problemas dos agentes convidados do Windows VM](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms).
    - O Agente VM Azure é instalado por predefinição em qualquer VM do Windows implantado a partir de uma imagem do Mercado Azure a partir do portal, PowerShell, Interface de Linha de Comando ou um modelo de Gestor de Recursos Azure. Uma [instalação manual do Agente](../virtual-machines/extensions/agent-windows.md#manual-installation) pode ser necessária quando criar uma imagem VM personalizada que é implantada para Azure.
    - Reveja a matriz de suporte para verificar se o VM funciona no [sistema operativo Windows suportado](backup-support-matrix-iaas.md#operating-system-support-windows).
  - Em Linux VM,
    - Certifique-se de que o serviço de Agente Convidado Azure VM está a funcionar executando o comando `ps-e` . Além disso, certifique-se de que a [versão mais recente](../virtual-machines/extensions/update-linux-agent.md) está instalada. Para saber mais, consulte [os problemas do agente convidado Linux VM](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms).
    - Certifique-se de que as dependências do [agente Linux VM nos pacotes do sistema](../virtual-machines/extensions/agent-linux.md#requirements) têm a configuração suportada. Por exemplo: A versão Python suportada é 2.6 ou superior.
    - Reveja a matriz de suporte para verificar se o VM funciona no [sistema operativo Linux suportado.](backup-support-matrix-iaas.md#operating-system-support-linux)

### <a name="step-3-check-azure-vm-extension-health"></a>Passo 3: Verifique a saúde da extensão Azure VM

- **Certifique-se de que todas as extensões Azure VM estão em estado de "provisão bem sucedida":** Se qualquer extensão estiver num estado falhado, então pode interferir com a cópia de segurança.
- *O portal Open Azure > VM > Definições > Extensões > Estado das Extensões* e verificar se todas as extensões estão em **disposição estão bem sucedidas.**
- Certifique-se de que todos os [problemas de extensão](../virtual-machines/extensions/overview.md#troubleshoot-extensions) estão resolvidos e relemisse a operação de backup.
- **Certifique-se de que a Aplicação do Sistema COM+** está a funcionar. Além disso, o **serviço de Coordenador de Transações Distribuídas** deve funcionar como conta de Serviço de **Rede.** Siga os passos deste artigo para [resolver problemas com as questões COM+ e MSDTC](backup-azure-vms-troubleshoot.md#extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error).

### <a name="step-4-check-azure-backup-vm-extension-health"></a>Passo 4: Verifique a saúde da extensão VM de backup Azure

O Azure Backup utiliza a Extensão de Instantâneo VM para obter uma cópia de segurança consistente da máquina virtual Azure. O Azure Backup instalará a extensão como parte da primeira cópia de segurança programada acionada depois de ativar a cópia de segurança.

- **Certifique-se de que a extensão VMSnapshot não está num estado falhado**: Siga os passos indicados nesta [secção](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) para verificar e garantir que a extensão de Backup Azure está saudável.

- **Verifique se o antivírus está a bloquear a extensão:** Determinado software antivírus pode impedir a execução de extensões.
  
  No momento da falha de backup, verifique se existem entradas de registo em ***Registos de aplicação do espectador de evento _** com o nome da aplicação de falha _*_falhando: IaaSBcdrExtension.exe_**. Se vir entradas, então pode ser o antivírus configurado no VM a restringir a execução da extensão de backup. Teste excluindo os seguintes diretórios na configuração antivírus e reagem à operação de backup.
  - `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
  - `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

- **Verifique se o acesso à rede é necessário:** As embalagens de extensão são descarregadas do repositório de extensão de armazenamento Azure e os uploads do estado de extensão são enviados para o Azure Storage. [Saiba mais](../virtual-machines/extensions/features-windows.md#network-access).
  - Se estiver numa versão não suportada do agente, tem de permitir o acesso de saída ao armazenamento Azure naquela região a partir do VM.
  - Se tiver bloqueado o acesso à `168.63.129.16` utilização da firewall do hóspede ou com um representante, as extensões falharão independentemente do que precede. São necessárias as portas 80, 443 e 32526, [Saiba mais.](../virtual-machines/extensions/features-windows.md#network-access)

- **Certifique-se de que o DHCP está ativado dentro do VM do hóspede:** Isto é necessário para obter o endereço de anfitrião ou tecido da DHCP para que a cópia de segurança IaaS VM funcione. Se precisar de um IP estático privado, deverá configurá-lo através do portal Azure ou PowerShell e certificar-se de que a opção DHCP dentro do VM está ativada, [Saiba mais](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken).

- **Certifique-se de que o serviço de escritores VSS está a funcionar**: Siga estes passos Para [resolver problemas com os escritores vss](backup-azure-vms-troubleshoot.md#extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state).
- **Siga as diretrizes de boas práticas** de backup : Reveja as [melhores práticas para permitir o backup do Azure VM](backup-azure-vms-introduction.md#best-practices).
- **Rever as diretrizes para discos encriptados**: Se estiver a ativar a cópia de segurança para VMs com disco encriptado, certifique-se de que forneceu todas as permissões necessárias. Para saber mais, consulte [Back up e restaure O VM encriptado](backup-azure-vms-encryption.md).

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable – o agente da VM não consegue comunicar com o Azure Backup

**Código de erro**: UserErrorGuestAgentStatusUn disponível <br>
**Error message**: VM Agent incapaz de comunicar com Azure Backup<br>

O agente Azure VM pode ser detido, desatualizado, num estado inconsistente, ou não instalado. Estes estados impedem que o serviço de backup Azure desencadeie instantâneos.

- **O portal Open Azure > VM > Configurações > o painel de propriedades** > garantir que o estado **do** VM está **a funcionar** e o estado **do agente** está **pronto**. Se o agente VM for parado ou estiver num estado inconsistente, reinicie o agente<br>
  - Para os VMs do Windows, siga estes [passos](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) para reiniciar o Agente Convidado.<br>
  - Para os VMs Linux, siga estes [passos](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) para reiniciar o Agente Convidado.
- **O portal Open Azure > VM > Definições > extensões** > Garantir que todas as extensões estão em **estado de provisionamento bem sucedido.** Caso contrário, siga estes [passos](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) para resolver a questão.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – não foi possível comunicar com o agente da VM para obter o estado do instantâneo

**Código de erro**: GuestAgentSnapshotTaskStatusError<br>
**Error message**: Não foi possível comunicar com o agente VM para o estado do instantâneo <br>

Depois de registar e agendar um VM para o serviço Azure Backup, o Backup inicia o trabalho comunicando com a extensão de backup VM para tirar uma foto pontual. Qualquer uma das seguintes condições pode impedir que o instantâneo seja acionado. Se a foto não for ativada, pode ocorrer uma falha de reserva. Complete as seguintes etapas de resolução de problemas na ordem listada e, em seguida, re-tentar a sua operação:  

**Causa 1: [O agente está instalado no VM, mas não responde (para VMs windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Causa 2: [O agente instalado no VM está desatualizado (para Os VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Causa 3: [O estado do instantâneo não pode ser recuperado, ou um instantâneo não pode ser tirado](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Causa 4: As [opções de configuração do Agente VM não estão definidas (para Os VMs Linux)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**Causa 5: [A solução de controlo de aplicações está a bloquear IaaSBcdrExtension.exe](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed – a VM está no estado de aprovisionamento com falhas

**Código de erro**: UserErrorVmProvisioningStateFailed<br>
**Error message**: The VM is in failed provisioning state<br>

Este erro ocorre quando uma das falhas de extensão coloca o VM em estado de provisão falhado.<br>**O portal Open Azure > VM > Definições > Extensões > Estado das Extensões** e verifique se todas as extensões estão em **disposição estão bem sucedidas.** Para saber mais, consulte [os Estados de Provisionamento.](../virtual-machines/states-billing.md)

- Se alguma extensão estiver num estado falhado, então pode interferir com a cópia de segurança. Certifique-se de que estas questões de extensão estão resolvidas e relemisse a operação de reserva.
- Se o estado de provisionamento de VM estiver em estado de atualização, pode interferir com a cópia de segurança. Certifique-se de que está saudável e relemissa a operação de reserva.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – atingiu o limite máximo da coleção de Pontos de Restauro

**Código de erro**: UserErrorRpCollectionLimitReached <br>
**Error message**: O limite máximo de recolha do Ponto de Restauração atingiu. <br>

- Este problema poderá ocorrer se existir um bloqueio no grupo de recursos dos pontos de recuperação a impedir a limpeza automática dos pontos de recuperação.
- Este problema também poderá ocorrer se forem acionadas várias cópias de segurança por dia. Atualmente, recomendamos apenas uma cópia de segurança por dia, uma vez que os pontos de restauro instantâneos são mantidos durante 1 a 5 dias conforme a retenção de instantâneos configurada e apenas podem ser associados 18 RPs instantâneos a uma VM num dado momento. <br>
- O número de pontos de restauro em todas as recolhas de pontos de restauração e grupos de recursos para um VM não pode exceder 18. Para criar um novo ponto de restauro, elimine os pontos de restauro existentes.

Ação Recomendada:<br>
Para resolver este problema, retire o bloqueio do grupo de recursos do VM e revendo a operação para desencadear a limpeza.
> [!NOTE]
> O serviço de backup cria um grupo de recursos separado do que o grupo de recursos do VM para armazenar a recolha de pontos de restauração. É aconselhável não bloquear o grupo de recursos criado para utilização pelo serviço de backup. O formato de nomeação do grupo de recursos criado pelo serviço De backup é: AzureBackupRG_ `<Geo>` _ `<number>` . Por exemplo: *AzureBackupRG_northeurope_1*

**Passo 1: [Remover o bloqueio do grupo de recursos pontos de restauração](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Passo 2: [Limpar a recolha do ponto de restauração](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNot Configurado - A cópia de segurança não tem permissões suficientes para o cofre de chaves para cópia de segurança de VMs encriptados

**Código de erro**: UserErrorKeyvaultPermissionsNot Configurado <br>
**Error message**: Backup não tem permissões suficientes para o cofre de chaves para cópia de segurança de VMs encriptados. <br>

Para que uma operação de backup tenha sucesso em VMs encriptados, deve ter permissões para aceder ao cofre da chave. As permissões podem ser definidas através do [portal Azure](./backup-azure-vms-encryption.md) ou através [do PowerShell](./backup-azure-vms-automation.md#enable-protection).

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - a operação de instantâneo falhou devido à falta de conectividade de rede na máquina virtual

**Código de erro**: ExtensõesSnapshotFailedNoNetwork<br>
**Error message**: A operação snapshot falhou devido à falta de conectividade da rede na máquina virtual<br>

Depois de registar e agendar um VM para o serviço Azure Backup, o Backup inicia o trabalho comunicando com a extensão de backup VM para tirar uma foto pontual. Qualquer uma das seguintes condições pode impedir que o instantâneo seja acionado. Se a foto não for ativada, pode ocorrer uma falha de reserva. Complete o seguinte passo de resolução de problemas e, em seguida, recandidante a sua operação:

**[O estado do instantâneo não pode ser recuperado, ou um instantâneo não pode ser tirado](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks – a operação da extensão VMSnapshot falhou

**Código de** erro : ExtensionOperationFailedForManagedDisks <br>
**Mensagem de erro**: A operação de extensão VMSnapshot falhou<br>

Depois de registar e agendar um VM para o serviço Azure Backup, o Backup inicia o trabalho comunicando com a extensão de backup VM para tirar uma foto pontual. Qualquer uma das seguintes condições pode impedir que o instantâneo seja acionado. Se a foto não for ativada, pode ocorrer uma falha de reserva. Complete as seguintes etapas de resolução de problemas na ordem listada e, em seguida, re-tentar a sua operação:  
**Causa 1: [O estado do instantâneo não pode ser recuperado, ou um instantâneo não pode ser tirado](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [O agente está instalado no VM, mas não responde (para VMs windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 3: [O agente instalado no VM está desatualizado (para Os VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - o Azure Backup falha com um erro interno

**Código de** erro : BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Error message**: Backup failed with a internal error - Please retry the operation in few minutes <br>

Depois de registar e agendar um VM para o serviço Azure Backup, o Backup inicia o trabalho comunicando com a extensão de backup VM para tirar uma foto pontual. Qualquer uma das seguintes condições pode impedir que o instantâneo seja acionado. Se a foto não for ativada, pode ocorrer uma falha de reserva. Complete as seguintes etapas de resolução de problemas na ordem listada e, em seguida, re-tentar a sua operação:  
**Causa 1: [O agente instalado no VM, mas não responde (para VMs windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [O agente instalado no VM está desatualizado (para Os VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [O estado do instantâneo não pode ser recuperado, ou um instantâneo não pode ser tirado](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 4: [O serviço de backup não tem permissão para apagar os antigos pontos de restauro por causa de um bloqueio de grupo de recursos](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize - O tamanho do disco configurado não é suportado por Azure Backup

**Código de erro**: UserErrorUnsupportedDiskSize <br>
**Mensagem de erro**: O tamanho do disco configurado não é suportado por Azure Backup. <br>

A sua operação de backup pode falhar ao fazer o backup de um VM com um tamanho de disco superior a 32 TB. Além disso, a cópia de segurança de discos encriptados com mais de 4 TB de tamanho não é suportada atualmente. Certifique-se de que o tamanho do disco é inferior ou igual ao limite suportado dividindo o ou os discos.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - Incapaz de iniciar backup como outra operação de backup está em andamento

**Código de erro**: UserErrorBackupOperationInProgress <br>
**Error message**: Incapaz de iniciar o backup como outra operação de backup está em andamento<br>

O teu recente trabalho de reserva falhou porque há um trabalho de reserva em andamento. Não podes começar um novo trabalho de reserva até que o trabalho atual termine. Certifique-se de que a operação de backup em curso está concluída antes de ativar ou agendar outras operações de backup. Para verificar o estado dos trabalhos de reserva, faça os seguintes passos:

1. Inscreva-se no portal Azure, selecione **Todos os serviços**. Serviços de recuperação de tipo e cofres de **serviços de recuperação selecionados.** É apresentada a lista dos cofres dos Serviços de Recuperação.
2. Da lista de cofres dos Serviços de Recuperação, selecione um cofre no qual a cópia de segurança está configurada.
3. No menu do painel de instrumentos do cofre, selecione **Backup Jobs** exibe todos os trabalhos de backup.
   - Se um trabalho de reserva estiver em andamento, aguarde que complete ou cancele o trabalho de reserva.
     - Para cancelar o trabalho de backup, clique com o botão direito no trabalho de backup e selecione **Cancelar** ou utilizar [o PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob).
   - Se reconfiguraste o apoio num cofre diferente, então assegura-te que não há trabalhos de reserva a funcionar no velho cofre. Se existe, cancele o trabalho de reserva.
     - Para cancelar o trabalho de backup, clique com o botão direito no trabalho de backup e selecione **Cancelar** ou usar [o PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob)
4. Repita a operação de cópia de segurança.

Se a operação de backup programada estiver a demorar mais tempo, entrando em conflito com a próxima configuração de backup, em seguida, reveja as [melhores práticas,](backup-azure-vms-introduction.md#best-practices) [o desempenho de backup](backup-azure-vms-introduction.md#backup-performance)e a [consideração de Restauro](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError – falha na cópia de segurança devido a um erro. Para obter mais informações, veja os Detalhes da Mensagem de Erro do Trabalho

**Código de erro**: UserErrrCrpReportedUserror <br>
**Error message**: A cópia de segurança falhou devido a um erro. Para mais detalhes, consulte os detalhes da mensagem de erro de trabalho.

Este erro é reportado a partir do IaaS VM. Para identificar a causa principal do problema, vá às definições do cofre dos Serviços de Recuperação. Na secção **de Monitorização,** selecione **trabalhos de backup** para filtrar e visualizar o estado. Selecione **Falhas** para rever os detalhes da mensagem de erro subjacentes. Tome outras ações de acordo com as recomendações na página de detalhes de erro.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent - Cópia de segurança falhou: Esta máquina virtual não está (ativamente) protegida pela Azure Backup

**Código de erro**: UserErrorBcmDatasourceNotPresent <br>
**Error message**: Backup failed: This virtual machine is not (ativamente) protected by Azure Backup.

Verifique se a máquina virtual dada está ativamente protegida (não em estado de pausa) protegida pela Azure Backup. Para ultrapassar este problema, certifique-se de que a máquina virtual está ativa e, em seguida, relemissa a operação.

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado no VM, mas não responde (para VMs windows)

#### <a name="solution-for-this-error"></a>Solução para este erro

O agente VM pode ter sido corrompido, ou o serviço pode ter sido parado. Reinstalar o agente VM ajuda a obter a versão mais recente. Também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço de Agente Convidado Windows Azure está em execução nos serviços VM (services.msc). Tente reiniciar o serviço de Agente Convidado Do Windows Azure e inicie a cópia de segurança.
2. Se o serviço de Agente Convidado Do Windows Azure não estiver visível nos serviços, no Painel de Controlo, vá a **Programas e Funcionalidades** para determinar se o serviço de Agente Convidado Do Windows Azure está instalado.
3. Se o Windows Azure Guest Agent aparecer em **Programas e Funcionalidades, desinstale** o Windows Azure Guest Agent.
4. Descarregue e instale a [versão mais recente do agente MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Tem de ter direitos de administrador para completar a instalação.
5. Verifique se os serviços de Agente Convidado Do Windows Azure aparecem nos serviços.
6. Faça uma cópia de segurança a pedido:
   - No portal, **selecione Backup Now**.

Além disso, verifique se [o Microsoft .NET 4.5 está instalado](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) no VM. .NET 4.5 é necessário para que o agente VM comunique com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado no VM está desatualizado (para Os VMs Linux)

#### <a name="solution"></a>Solução

A maioria das falhas relacionadas com o agente ou relacionadas com extensões para os VMs do Linux são causadas por problemas que afetam um agente VM desatualizado. Para resolver este problema, siga estas orientações gerais:

1. Siga as instruções de [atualização do agente Linux VM](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > *Recomendamos vivamente* que atualize o agente apenas através de um repositório de distribuição. Não recomendamos baixar o código do agente diretamente do GitHub e atualizá-lo. Se o último agente para a sua distribuição não estiver disponível, contacte o suporte de distribuição para obter instruções sobre como instalá-lo. Para verificar o agente mais recente, vá à página do [agente Do Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) no repositório do GitHub.

2. Certifique-se de que o agente Azure está a funcionar no VM executando o seguinte comando: `ps -e`

   Se o processo não estiver em curso, reinicie-o utilizando os seguintes comandos:

   - Para Ubuntu: `service walinuxagent start`
   - Para outras distribuições: `service waagent start`

3. [Configure o agente de reinício automático](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Faça um novo teste de reserva. Se a falha persistir, recolha os seguintes registos do VM:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Se necessitar de registo verboso para waagent, siga estes passos:

1. No ficheiro /etc/waagent.conf, localize a seguinte linha: **Ative a registo verboso (y|n)**
2. Altere o valor **Logs.Verbose** de *n* para *y*.
3. Guarde a alteração e, em seguida, reinicie o waagent completando os passos descritos anteriormente nesta secção.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>VM-Agent opções de configuração não estão definidas (para Os VMs Linux)

Um ficheiro de configuração (/etc/waagent.conf) controla as ações do waagent. Configuração Extensões de opções **de ficheiro.O enable** deve ser definido para **y** e **Provisioning.Agent** deve ser configurado para **auto** para cópia de segurança funcionar.
Para obter a lista completa das opções de ficheiros de configuração VM-Agent, consulte <https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>A solução de controlo de aplicações está a bloquear IaaSBcdrExtension.exe

Se estiver a executar [o AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) (ou outra solução de controlo de aplicações), e as regras forem baseadas em editores ou caminhos, podem bloquear a execução da **IaaSBcdrExtension.exe.**

#### <a name="solution-to-this-issue"></a>Solução para esta questão

Excluir o `/var/lib` caminho ou a **IaaSBcdrExtension.exe** executável do AppLocker (ou de outro software de controlo de aplicações.)

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>O estado do instantâneo não pode ser recuperado, ou um instantâneo não pode ser tirado

A cópia de segurança VM baseia-se na emissão de um comando instantâneo para a conta de armazenamento subjacente. A cópia de segurança pode falhar quer porque não tem acesso à conta de armazenamento, quer porque a execução da tarefa instantânea está atrasada.

#### <a name="solution-for-this-issue"></a>Solução para esta questão

As seguintes condições podem fazer com que a tarefa do instantâneo falhe:

| Causa | Solução |
| --- | --- |
| O estado de VM é relatado incorretamente porque o VM é desligado no Protocolo de Ambiente de Trabalho Remoto (PDR). | Se desligar o VM em PDR, verifique se o portal está correto. Se não estiver correto, desligue o VM no portal utilizando a opção **de Encerramento** no painel VM. |
| O VM não consegue obter o endereço de hospedeiro ou tecido da DHCP. | O DHCP tem de estar ativado no convidado para que a cópia de segurança da VM IaaS funcione. Se o VM não conseguir obter o endereço de anfitrião ou tecido da resposta DHCP 245, não pode descarregar ou executar quaisquer extensões. Se precisar de um IP estático privado, deverá configurá-lo através do **portal Azure** ou **PowerShell** e certificar-se de que a opção DHCP dentro do VM está ativada. [Saiba mais](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) sobre a configuração de um endereço IP estático com o PowerShell.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Remover o bloqueio do grupo de recursos do ponto de recuperação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Aceda à **opção Todos os Recursos**, selecione o grupo de recursos de recolha de pontos de restauração no seguinte formato AzureBackupRG_ `<Geo>` _ `<number>` .
3. Na secção **Definições,** selecione **Fechaduras** para visualizar as fechaduras.
4. Para remover a fechadura, selecione a elipse e selecione **Delete**.

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a> Limpe a coleção de pontos de restauro

Depois de retirar a fechadura, os pontos de restauro têm de ser limpos.

Se eliminar o Grupo de Recursos do VM, ou o próprio VM, as imagens de restauro instantâneo dos discos geridos permanecem ativas e expiram de acordo com o conjunto de retenção. Para eliminar as imagens de restauro instantânea (se já não precisar) que estão armazenadas na Coleção Ponto de Restauro, limpe a recolha do ponto de restauro de acordo com os passos abaixo indicados.

Para limpar os pontos de restauro, siga qualquer um dos métodos:<br>

- [Limpe a recolha do ponto de restauro executando backup a pedido](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Limpe a coleção de pontos de restauro do portal Azure](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Limpe a recolha do ponto de restauro executando backup a pedido

Depois de retirar a fechadura, desencadeie uma cópia de segurança a pedido. Esta ação assegurará que os pontos de restauro sejam automaticamente limpos. Espere que esta operação a pedido falhe da primeira vez. No entanto, garantirá uma limpeza automática em vez de eliminação manual dos pontos de restauro. Depois da limpeza, o seu próximo reforço deve ter sucesso.

> [!NOTE]
> A limpeza automática ocorrerá após algumas horas de ativação da cópia de segurança a pedido. Se a sua cópia de segurança programada ainda falhar, tente eliminar manualmente a recolha do ponto de restauro utilizando os passos [listados aqui](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Limpe a coleção de pontos de restauro do portal Azure <br>

Para limpar manualmente a recolha de pontos de restauro, que não está limpa devido à fechadura do grupo de recursos, experimente os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu **Hub,** selecione **Todos os recursos,** selecione o grupo De recursos com o seguinte formato AzureBackupRG_ `<Geo>` _ onde está localizado o seu `<number>` VM.

    ![Selecione o grupo de recursos](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Selecione grupo de recursos, o **painel de visão** geral é exibido.
4. Selecione Mostrar a opção **de tipos ocultos** para exibir todos os recursos ocultos. Selecione as coleções de ponto de restauração com o seguinte formato AzureBackupRG_ `<VMName>` _ `<number>` .

    ![Selecione a coleção de pontos de restauro](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. **Selecione Eliminar** para limpar a coleção de pontos de restauro.
6. Volte a tentar a operação de reserva.

> [!NOTE]
 >Se o recurso (RP Collection) tiver um grande número de Pontos de Restauro, então apagá-los do portal pode escamar e falhar. Esta é uma questão conhecida de CRP, onde todos os pontos de restauro não são eliminados no tempo estipulado e os tempos de funcionamento para fora. No entanto, a operação de eliminação geralmente tem sucesso após duas ou três retrações.
