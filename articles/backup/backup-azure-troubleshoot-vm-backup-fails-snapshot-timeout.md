---
title: Problemas de agente de resolução de problemas e problemas de extensão
description: Sintomas, causas e resoluções de falhas de Backup Azure relacionadas com agente, extensão e discos.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: 55af4bddb5a963a831c1438400a7a243cca20573
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538824"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Falha de backup do Azure: Problemas com o agente ou extensão

Este artigo fornece medidas de resolução de problemas que podem ajudá-lo a resolver erros de Backup Azure relacionados com a comunicação com o agente VM e extensão.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - o agente da VM não consegue comunicar com o Azure Backup

**Código de erro**: UserErrorGuestAgentStatusUn disponível <br>
**Error message**: VM Agent incapaz de comunicar com Azure Backup<br>

O agente Azure VM pode ser detido, desatualizado, num estado inconsistente, ou não instalado. Estes estados impedem que o serviço de backup Azure desencadeie instantâneos.

- **O portal Open Azure > VM > Configurações > o painel de propriedades** > garantir que o estado **do** VM está **a funcionar** e o estado **do agente** está **pronto**. Se o agente VM for parado ou estiver num estado inconsistente, reinicie o agente<br>
  - Para os VMs do Windows, siga estes [passos](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) para reiniciar o Agente Convidado.<br>
  - Para os VMs Linux, siga estes [passos](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) para reiniciar o Agente Convidado.
- **O portal Open Azure > VM > Definições > extensões** > Garantir que todas as extensões estão em **estado de provisionamento bem sucedido.** Caso contrário, siga estes [passos](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) para resolver a questão.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - não foi possível comunicar com o agente da VM para obter o estado do instantâneo

**Código de erro**: GuestAgentSnapshotTaskStatusError<br>
**Error message**: Não foi possível comunicar com o agente VM para o estado do instantâneo <br>

Depois de registar e agendar um VM para o serviço Azure Backup, o Backup inicia o trabalho comunicando com a extensão de backup VM para tirar uma foto pontual. Qualquer uma das seguintes condições pode impedir que o instantâneo seja acionado. Se a foto não for ativada, pode ocorrer uma falha de reserva. Complete as seguintes etapas de resolução de problemas na ordem listada e, em seguida, re-tentar a sua operação:  

**Causa 1: [O agente está instalado no VM, mas não responde (para VMs windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Causa 2: [O agente instalado no VM está desatualizado (para Os VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Causa 3: [O estado do instantâneo não pode ser recuperado, ou um instantâneo não pode ser tirado](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Causa 4: As [opções de configuração do Agente VM não estão definidas (para Os VMs Linux)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**Causa 5: [A solução de controlo de aplicações está a bloquear IaaSBcdrExtension.exe](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed - O VM está em estado de provisionamento falhado

**Código de erro**: UserErrorVmProvisioningStateFailed<br>
**Error message**: The VM is in failed provisioning state<br>

Este erro ocorre quando uma das falhas de extensão coloca o VM em estado de provisão falhado.<br>**O portal Open Azure > VM > Definições > Extensões > Estado das Extensões** e verifique se todas as extensões estão em **disposição estão bem sucedidas.** Para saber mais, consulte [os Estados de Provisionamento.](../virtual-machines/windows/states-lifecycle.md#provisioning-states)

- Se a extensão VMSnapshot estiver num estado falhado, clique com o botão direito na extensão falhada e remova-a. Desencadeie uma cópia de segurança a pedido. Esta ação reinstalará as extensões e executará o trabalho de backup.  <br>
- Se qualquer outra extensão estiver em estado de falha, então pode interferir com a cópia de segurança. Certifique-se de que estas questões de extensão estão resolvidas e relemisse a operação de reserva.
- Se o estado de provisionamento de VM estiver em estado de atualização, pode interferir com a cópia de segurança. Certifique-se de que está saudável e relemissa a operação de reserva.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - atingiu o limite máximo da coleção de Pontos de Restauro

**Código de erro**: UserErrorRpCollectionLimitReached <br>
**Error message**: O limite máximo de recolha do Ponto de Restauração atingiu. <br>

- Este problema pode acontecer se houver um bloqueio no grupo de recursos do ponto de recuperação que impeça a limpeza automática de pontos de recuperação.
- Este problema também pode acontecer se várias cópias de segurança forem desencadeadas por dia. Atualmente recomendamos apenas uma cópia de segurança por dia, uma vez que os pontos de restauro instantâneos são mantidos por 1-5 dias por retenção de instantâneos configurados e apenas 18 RPs instantâneos podem ser associados a um VM em qualquer momento. <br>
- O número de pontos de restauro em todas as recolhas de pontos de restauração e grupos de recursos para um VM não pode exceder 18. Para criar um novo ponto de restauro, elimine os pontos de restauro existentes.

Ação recomendada:<br>
Para resolver este problema, retire o bloqueio do grupo de recursos do VM e revendo a operação para desencadear a limpeza.
> [!NOTE]
> O serviço de backup cria um grupo de recursos separado do que o grupo de recursos do VM para armazenar a recolha de pontos de restauração. Os clientes são aconselhados a não bloquear o grupo de recursos criado para utilização pelo serviço De backup. O formato de nomeação do grupo de recursos criado pelo serviço Backup é: AzureBackupRG_ `<Geo>` _ `<number>` Eg: AzureBackupRG_northeurope_1

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

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensãoOperaçãoFailedForManagedDisks - Operação de extensão VMSnapshot falhou

**Código de**erro : ExtensionOperationFailedForManagedDisks <br>
**Mensagem de erro**: A operação de extensão VMSnapshot falhou<br>

Depois de registar e agendar um VM para o serviço Azure Backup, o Backup inicia o trabalho comunicando com a extensão de backup VM para tirar uma foto pontual. Qualquer uma das seguintes condições pode impedir que o instantâneo seja acionado. Se a foto não for ativada, pode ocorrer uma falha de reserva. Complete as seguintes etapas de resolução de problemas na ordem listada e, em seguida, re-tentar a sua operação:  
**Causa 1: [O estado do instantâneo não pode ser recuperado, ou um instantâneo não pode ser tirado](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [O agente está instalado no VM, mas não responde (para VMs windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 3: [O agente instalado no VM está desatualizado (para Os VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - o Azure Backup falha com um erro interno

**Código de**erro : BackUpOperationFailed / BackUpOperationFailedV2 <br>
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

1. Inscreva-se no portal Azure, clique em **Todos os serviços**. Escreva Serviços de Recuperação e clique em **Cofres dos Serviços de Recuperação**. É apresentada a lista dos cofres dos serviços de recuperação.
2. Da lista de cofres dos serviços de recuperação, selecione um cofre no qual a cópia de segurança está configurada.
3. No menu do painel de instrumentos do cofre, clique **em Backup Jobs** exibe todos os trabalhos de backup.
   - Se um trabalho de reserva estiver em andamento, aguarde que complete ou cancele o trabalho de reserva.
     - Para cancelar o trabalho de backup, clique com o botão direito no trabalho de backup e clique em **Cancelar** ou utilizar [o PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob).
   - Se reconfiguraste o apoio num cofre diferente, então assegura-te que não há trabalhos de reserva a funcionar no velho cofre. Se existe, cancele o trabalho de reserva.
     - Para cancelar a tarefa de cópia de segurança, faça clique com o botão direito do rato na tarefa de cópia de segurança e clique em **Cancelar** ou utilize o [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob)
4. Re-tentar a operação de reserva.

Se a operação de backup programada estiver a demorar mais tempo, entrando em conflito com a próxima configuração de backup, em seguida, reveja as [melhores práticas,](backup-azure-vms-introduction.md#best-practices) [o desempenho de backup](backup-azure-vms-introduction.md#backup-performance)e a [consideração de Restauro](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError - A cópia de segurança falhou devido a um erro. Para mais detalhes, consulte detalhes da mensagem de erro de trabalho

**Código de erro**: UserErrrCrpReportedUserror <br>
**Error message**: A cópia de segurança falhou devido a um erro. Para mais detalhes, consulte os detalhes da mensagem de erro de trabalho.

Este erro é reportado a partir do IaaS VM. Para identificar a causa principal do problema, vá às definições do cofre dos Serviços de Recuperação. Na secção **de Monitorização,** selecione **trabalhos de backup** para filtrar e visualizar o estado. Clique em **Falhas** para rever os detalhes da mensagem de erro subjacente. Tome outras ações de acordo com as recomendações na página de detalhes de erro.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent - Cópia de segurança falhou: Esta máquina virtual não está (ativamente) protegida pela Azure Backup

**Código de erro**: UserErrorBcmDatasourceNotPresent <br>
**Error message**: Backup failed: This virtual machine is not (ativamente) protected by Azure Backup.

Verifique se a máquina virtual dada está ativamente protegida (não em estado de pausa) protegida pela Azure Backup. Para ultrapassar este problema, certifique-se de que a máquina virtual está ativa e, em seguida, relemissa a operação.

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado no VM, mas não responde (para VMs windows)

#### <a name="solution"></a>Solução

O agente VM pode ter sido corrompido, ou o serviço pode ter sido parado. Reinstalar o agente VM ajuda a obter a versão mais recente. Também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço de Agente Convidado Windows Azure está em execução nos serviços VM (services.msc). Tente reiniciar o serviço de Agente Convidado Do Windows Azure e inicie a cópia de segurança.
2. Se o serviço de Agente Convidado Do Windows Azure não estiver visível nos serviços, no Painel de Controlo, vá a **Programas e Funcionalidades** para determinar se o serviço de Agente Convidado Do Windows Azure está instalado.
3. Se o Windows Azure Guest Agent aparecer em **Programas e Funcionalidades, desinstale**o Windows Azure Guest Agent.
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
   > *Recomendamos vivamente* que atualize o agente apenas através de um repositório de distribuição. Não recomendamos o download do código de agente diretamente do GitHub e atualizá-lo. Se o último agente para a sua distribuição não estiver disponível, contacte o suporte de distribuição para obter instruções sobre como instalá-lo. Para verificar o agente mais recente, vá à página do [agente Do Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) no repositório do GitHub.

2. Certifique-se de que o agente Azure está a funcionar no VM executando o seguinte comando:`ps -e`

   Se o processo não estiver em curso, reinicie-o utilizando os seguintes comandos:

   - Para Ubuntu:`service walinuxagent start`
   - Para outras distribuições:`service waagent start`

3. [Configure o agente de reinício automático](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Faça um novo teste de reserva. Se a falha persistir, recolha os seguintes registos do VM:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Se necessitar de registo verboso para waagent, siga estes passos:

1. No ficheiro /etc/waagent.conf, localize a seguinte linha: **Ative a registo verboso (y/n)**
2. Altere o valor **Logs.Verbose** de *n* para *y*.
3. Guarde a alteração e, em seguida, reinicie o waagent completando os passos descritos anteriormente nesta secção.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>As opções de configuração do Agente VM não estão definidas (para Os VMs Linux)

Um ficheiro de configuração (/etc/waagent.conf) controla as ações do waagent. Configuração Extensões de opções **de ficheiro.O enable** deve ser definido para **y** e **Provisioning.Agent** deve ser configurado para **auto** para cópia de segurança funcionar.
Para obter a lista completa das opções de ficheiros de configuração de vídeo-agente VM, consulte<https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>A solução de controlo de aplicações está a bloquear IaaSBcdrExtension.exe

Se estiver a executar [o AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) (ou outra solução de controlo de aplicações) e as regras forem baseadas em editores ou caminhos, podem bloquear a execução da **IaaSBcdrExtension.exe.**

#### <a name="solution"></a>Solução

Excluir o `/var/lib` caminho ou a **IaaSBcdrExtension.exe** executável do AppLocker (ou de outro software de controlo de aplicações.)

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>O estado do instantâneo não pode ser recuperado, ou um instantâneo não pode ser tirado

A cópia de segurança VM baseia-se na emissão de um comando instantâneo para a conta de armazenamento subjacente. A cópia de segurança pode falhar quer porque não tem acesso à conta de armazenamento, quer porque a execução da tarefa instantânea está atrasada.

#### <a name="solution"></a>Solução

As seguintes condições podem fazer com que a tarefa do instantâneo falhe:

| Causa | Solução |
| --- | --- |
| O estado de VM é relatado incorretamente porque o VM é desligado no Protocolo de Ambiente de Trabalho Remoto (PDR). | Se desligar o VM em PDR, verifique se o portal está correto. Se não estiver correto, desligue o VM no portal utilizando a opção **de Encerramento** no painel VM. |
| O VM não consegue obter o endereço de hospedeiro ou tecido da DHCP. | O DHCP deve ser ativado dentro do hóspede para que o backup IaaS VM funcione. Se o VM não conseguir obter o endereço de anfitrião ou tecido da resposta DHCP 245, não pode descarregar ou executar quaisquer extensões. Se precisar de um IP estático privado, deverá configurá-lo através do **portal Azure** ou **PowerShell** e certificar-se de que a opção DHCP dentro do VM está ativada. [Saiba mais](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) sobre a configuração de um endereço IP estático com o PowerShell.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Remover o bloqueio do grupo de recursos do ponto de recuperação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Aceda à **opção Todos os Recursos**, selecione o grupo de recursos de recolha de pontos de restauração no seguinte formato AzureBackupRG_ `<Geo>` _ `<number>` .
3. Na secção **Definições,** selecione **Fechaduras** para visualizar as fechaduras.
4. Para remover a fechadura, selecione a elipse e clique em **Eliminar**.

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a>Limpe a coleção de pontos de restauro

Depois de retirar a fechadura, os pontos de restauro têm de ser limpos.

Se eliminar o Grupo de Recursos do VM, ou o próprio VM, as imagens de restauro instantâneo dos discos geridos permanecem ativas e expiram de acordo com o conjunto de retenção. Para eliminar as imagens de restauro instantânea (se já não precisar) que estão armazenadas na Coleção Ponto de Restauro, limpe a recolha do ponto de restauro de acordo com os passos abaixo indicados.

Para limpar os pontos de restauro, siga qualquer um dos métodos:<br>

- [Limpe a recolha do ponto de restauro executando backup a pedido](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Limpe a coleção de pontos de restauro do portal Azure](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Limpe a recolha do ponto de restauro executando backup a pedido

Depois de retirar a fechadura, desencadeie uma cópia de segurança a pedido. Esta ação assegurará que os pontos de restauro sejam automaticamente limpos. Esperemos que esta operação a pedido falhe pela primeira vez; no entanto, garantirá a limpeza automática em vez da eliminação manual dos pontos de restauro. Depois da limpeza, o seu próximo reforço deve ter sucesso.

> [!NOTE]
> A limpeza automática ocorrerá após algumas horas de ativação da cópia de segurança a pedido. Se a sua cópia de segurança programada ainda falhar, tente eliminar manualmente a recolha do ponto de restauro utilizando os passos [listados aqui](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Limpe a coleção de pontos de restauro do portal Azure <br>

Para limpar manualmente a recolha de pontos de restauro, que não está limpa devido à fechadura do grupo de recursos, experimente os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu **Hub,** clique em **Todos os recursos,** selecione o grupo De recursos com o seguinte formato AzureBackupRG_ `<Geo>` _ onde está localizado o seu `<number>` VM.

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Clique no grupo de recursos, o **painel de visão** geral é apresentado.
4. Selecione Mostrar a opção **de tipos ocultos** para exibir todos os recursos ocultos. Selecione as coleções de ponto de restauração com o seguinte formato AzureBackupRG_ `<VMName>` _ `<number>` .

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Clique **em Eliminar** para limpar a coleção de pontos de restauro.
6. Volte a tentar a operação de reserva.

> [!NOTE]
 >Se o recurso (RP Collection) tiver um grande número de Pontos de Restauro, então apagá-los do portal pode escamar e falhar. Trata-se de uma questão conhecida de CRP, em que todos os pontos de restauro não são suprimidos no tempo estipulado e nos tempos de funcionamento; no entanto, a operação de eliminação geralmente tem sucesso após 2 ou 3 retréis.
