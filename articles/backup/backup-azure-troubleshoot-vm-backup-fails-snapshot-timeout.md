---
title: 'Resolver problemas de falhas de cópia de segurança do Azure: Estado do agente convidado não disponível'
description: Os sintomas, causas e resoluções de falhas de cópia de segurança do Azure relacionados com o agente de extensão e discos.
services: backup
author: genlin
manager: cshepard
keywords: Cópia de segurança do Azure; Agente da VM; Conectividade de rede
ms.service: backup
ms.topic: troubleshooting
ms.date: 12/03/2018
ms.author: genli
ms.openlocfilehash: 85dca677238070ded13b59faf9a13081c2409987
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890870"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Resolver problemas de falhas de cópia de segurança do Azure: Problemas com o agente ou a extensão

Este artigo fornece passos de resolução de problemas que podem ajudar a resolverem erros de cópia de segurança do Azure relacionados à comunicação com o agente da VM e a extensão.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]



## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - agente VM não é possível comunicar com o Azure Backup

**Código de erro**: UserErrorGuestAgentStatusUnavailable <br>
**Mensagem de erro**: Agente de VM não é possível comunicar com o Azure Backup<br>

Depois de registar e agendar uma VM para o serviço de cópia de segurança, cópia de segurança inicia a tarefa através da comunicação com o agente da VM para tirar um instantâneo de ponto no tempo. Qualquer uma das seguintes condições poderá impedir que o instantâneo que está a ser acionado. Quando um instantâneo não é acionado, a cópia de segurança poderá falhar. Conclua os seguintes passos de resolução de problemas na ordem listada e, em seguida, repita a operação:<br>
**Fazer com que 1: [O agente está instalado na VM, mas ele não está a responder (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [Não é possível obter o estado do instantâneo ou não pode ser criado um instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Fazer com que 4: [A extensão de cópia de segurança não consegue atualizar ou de carga](#the-backup-extension-fails-to-update-or-load)**  
**Fazer com que 5: [A VM não tem acesso à internet](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - não foi possível comunicar com o agente VM para o estado do instantâneo

**Código de erro**: GuestAgentSnapshotTaskStatusError<br>
**Mensagem de erro**: Não foi possível comunicar com o agente da VM para obter o estado do instantâneo <br>

Depois de registar e agendar uma VM para o serviço de cópia de segurança do Azure, a cópia de segurança inicia a tarefa através da comunicação com a extensão de cópia de segurança de VM para tirar um instantâneo de ponto no tempo. Qualquer uma das seguintes condições poderá impedir que o instantâneo que está a ser acionado. Se o instantâneo não é acionado, pode ocorrer uma falha de cópia de segurança. Conclua os seguintes passos de resolução de problemas na ordem listada e, em seguida, repita a operação:  
**Fazer com que 1: [O agente está instalado na VM, mas ele não está a responder (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [A VM não tem acesso à internet](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - atingiu o limite máximo da coleção de ponto de restauro

**Código de erro**: UserErrorRpCollectionLimitReached <br>
**Mensagem de erro**: Atingiu o limite máximo da coleção de ponto de restauro. <br>
* Este problema pode ocorrer se existir um bloqueio no grupo de recursos de ponto de recuperação que impede a limpeza automática de ponto de recuperação.
* Este problema também pode acontecer se várias cópias de segurança são acionadas por dia. Atualmente, recomendamos que apenas uma cópia de segurança por dia como instantâneas RPs são mantidas durante 7 dias e apenas 18 RPs instantâneas pode ser associado uma VM em qualquer momento. <br>

Ação recomendada:<br>
Para resolver este problema, remova o bloqueio do grupo de recursos da VM e repita a operação para acionar a limpeza.
> [!NOTE]
> Serviço de cópia de segurança cria um grupo de recursos separado que o grupo de recursos da VM para armazenar a coleção de ponto de restauro. Os clientes são aconselhados não para bloquear o grupo de recursos criado para utilização pelo serviço de cópia de segurança. O formato de nomenclatura do grupo de recursos criado pelo serviço de cópia de segurança é: AzureBackupRG_`<Geo>`_`<number>` Eg: AzureBackupRG_northeurope_1

**Passo 1: [Remova o bloqueio do grupo de recursos de ponto de restauro](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Passo 2: [Limpar a coleção de ponto de restauro](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - cópia de segurança não tem permissões suficientes para o Cofre de chaves para cópia de segurança de VMs encriptadas

**Código de erro**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Mensagem de erro**: Cópia de segurança não tem permissões suficientes para o Cofre de chaves para cópia de segurança de VMs encriptadas. <br>

Para a operação de cópia de segurança com êxito em VMs encriptadas, tem de ter permissões para aceder ao Cofre de chaves. Isso pode ser feito com o [portal do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) ou através de [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - operação de instantâneo falhou devido a nenhuma conectividade de rede na máquina virtual

**Código de erro**: ExtensionSnapshotFailedNoNetwork<br>
**Mensagem de erro**: A operação de instantâneo falhou devido à falta de conetividade de rede na máquina virtual<br>

Depois de registar e agendar uma VM para o serviço de cópia de segurança do Azure, a cópia de segurança inicia a tarefa através da comunicação com a extensão de cópia de segurança de VM para tirar um instantâneo de ponto no tempo. Qualquer uma das seguintes condições poderá impedir que o instantâneo que está a ser acionado. Se o instantâneo não é acionado, pode ocorrer uma falha de cópia de segurança. Conclua os seguintes passos de resolução de problemas na ordem listada e, em seguida, repita a operação:    
**Fazer com que 1: [Não é possível obter o estado do instantâneo ou não pode ser criado um instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [A extensão de cópia de segurança não consegue atualizar ou de carga](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [A VM não tem acesso à internet](#the-vm-has-no-internet-access)**

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtentionOperationFailedForManagedDisks - operação de extensão de VMSnapshot falhou

**Código de erro**: ExtentionOperationFailedForManagedDisks <br>
**Mensagem de erro**: Operação de extensão de VMSnapshot falhou<br>

Depois de registar e agendar uma VM para o serviço de cópia de segurança do Azure, a cópia de segurança inicia a tarefa através da comunicação com a extensão de cópia de segurança de VM para tirar um instantâneo de ponto no tempo. Qualquer uma das seguintes condições poderá impedir que o instantâneo que está a ser acionado. Se o instantâneo não é acionado, pode ocorrer uma falha de cópia de segurança. Conclua os seguintes passos de resolução de problemas na ordem listada e, em seguida, repita a operação:  
**Fazer com que 1: [Não é possível obter o estado do instantâneo ou não pode ser criado um instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [A extensão de cópia de segurança não consegue atualizar ou de carga](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [O agente está instalado na VM, mas ele não está a responder (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Fazer com que 4: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - cópia de segurança falha, com um erro interno

**Código de erro**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Mensagem de erro**: Cópia de segurança falhou com um erro interno - repita a operação dentro de alguns minutos <br>

Depois de registar e agendar uma VM para o serviço de cópia de segurança do Azure, a cópia de segurança inicia a tarefa através da comunicação com a extensão de cópia de segurança de VM para tirar um instantâneo de ponto no tempo. Qualquer uma das seguintes condições poderá impedir que o instantâneo que está a ser acionado. Se o instantâneo não é acionado, pode ocorrer uma falha de cópia de segurança. Conclua os seguintes passos de resolução de problemas na ordem listada e, em seguida, repita a operação:  
**Fazer com que 1: [O agente instalado na VM, mas não está a responder (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [Não é possível obter o estado do instantâneo ou não pode ser criado um instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Fazer com que 4: [A extensão de cópia de segurança não consegue atualizar ou de carga](#the-backup-extension-fails-to-update-or-load)**  
**Fazer com que 5: Serviço de cópia de segurança não tem permissão para eliminar os pontos de restauração antigos devido a um bloqueio de grupo de recursos** <br>
**Fazer com que 6: [A VM não tem acesso à internet](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-1023gb"></a>UserErrorUnsupportedDiskSize - atualmente o Azure Backup não suporta tamanhos de disco superiores a 1023GB

**Código de erro**: UserErrorUnsupportedDiskSize <br>
**Mensagem de erro**: O Azure Backup não suporta atualmente tamanhos de disco superiores a 1023 GB <br>

A operação de cópia de segurança poderá falhar quando a cópia de segurança de VM com o tamanho do disco superior a 1023GB, uma vez que o Cofre não é atualizado para restaurar instantânea. Atualizar para o restaurar instantâneas irá fornecer suporte a até 4TB, vê-lo [artigo](backup-instant-restore-capability.md#upgrading-to-instant-restore). Depois de atualizar, demorará até duas horas para a subscrição aproveitar essa funcionalidade. Forneça a memória intermédia suficiente antes de repetir a operação.  

## <a name="usererrorstandardssdnotsupported---currently-azure-backup-does-not-support-standard-ssd-disks"></a>UserErrorStandardSSDNotSupported - atualmente o Azure Backup não suporta discos de Standard SSD

**Código de erro**: UserErrorStandardSSDNotSupported <br>
**Mensagem de erro**: Atualmente, o Azure Backup não suporta discos Standard SSD <br>

Atualmente o Azure Backup suporta discos de Standard SSD apenas para os cofres que são atualizados para o [restaurar instantâneas](backup-instant-restore-capability.md).

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - não é possível iniciar a cópia de segurança como outra operação de cópia de segurança está atualmente em curso

**Código de erro**: UserErrorBackupOperationInProgress <br>
**Mensagem de erro**: Não é possível iniciar a cópia de segurança como outra operação de cópia de segurança está atualmente em curso<br>

Sua tarefa de cópia de segurança recente falhou porque existe uma tarefa de cópia de segurança existente em curso. Não é possível iniciar uma nova tarefa de cópia de segurança até que seja concluída a tarefa atual. Certifique-se de que a operação de cópia de segurança atualmente em curso é concluída antes de acionar ou outro operações de cópia de segurança de agendamento. Para verificar o estado de tarefas de cópia de segurança, execute os passos abaixo:

1. Inicie sessão no portal do Azure e clique em **All services** (Todos os serviços). Escreva Serviços de Recuperação e clique em **Cofres dos Serviços de Recuperação**. É apresentada a lista dos cofres dos serviços de recuperação.
2. Na lista de cofres dos serviços de recuperação, selecione um cofre no qual a cópia de segurança está configurada.
3. No menu do dashboard do cofre, clique em **tarefas de cópia de segurança** apresenta todas as tarefas de cópia de segurança.

    * Se uma tarefa de cópia de segurança está em curso, aguarde para concluir ou cancelar a tarefa de cópia de segurança.
        * Para cancelar o contexto da tarefa de cópia de segurança na tarefa de cópia de segurança e clique em **Cancelar** ou utilize [PowerShell](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
    * Se o ter de reconfigurar a cópia de segurança num cofre diferente, em seguida, certifique-se não há nenhuma tarefa de cópia de segurança em execução no cofre antigo. Se existir, em seguida, cancele a tarefa de cópia de segurança.
        * Para cancelar o contexto da tarefa de cópia de segurança na tarefa de cópia de segurança e clique em **Cancelar** ou utilize [PowerShell](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. Repita a operação de cópia de segurança.

Se a operação de cópia de segurança agendada está a demorar mais tempo em conflito com a seguinte configuração de cópia de segurança, em seguida, reveja os [melhores práticas](backup-azure-vms-introduction.md#best-practices), [desempenho de cópia de segurança](backup-azure-vms-introduction.md#backup-performance) e [restaurar consideração ](backup-azure-vms-introduction.md#backup-and-restore-considerations).


## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-vm-has-no-internet-access"></a>A VM não tem acesso à internet
Pelo requisito de implementação, a VM não tem acesso à internet. Em alternativa, poderá ter restrições que impedem o acesso para a infraestrutura do Azure.

Para funcionar corretamente, a extensão de cópia de segurança precisa de conectividade para os endereços IP públicos do Azure. A extensão envia comandos para um ponto final de armazenamento do Azure (HTTPs URL) de gerir os instantâneos da VM. Se a extensão não tiver acesso à internet pública, cópia de segurança, eventualmente, falha.

####  <a name="solution"></a>Solução
Para resolver o problema de rede, consulte [estabelecer conectividade de rede](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado na VM, mas ele não está a responder (para VMs do Windows)

#### <a name="solution"></a>Solução
O agente da VM pode ter sido corrompido ou o serviço pode ter sido parado. Reinstalar o agente da VM ajuda a obter a versão mais recente. Ele também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço de agente de convidado do Windows Azure está em execução nos serviços de VM (Services. msc). Tente reiniciar o serviço de agente de convidado do Windows Azure e iniciar a cópia de segurança.    
2. Se o serviço de agente de convidado do Windows Azure não está visível nos serviços, no painel de controlo, aceda a **programas e funcionalidades** para determinar se o serviço de agente de convidado do Windows Azure está instalado.
4. Se o agente de convidado do Windows Azure é apresentado na **programas e funcionalidades**, desinstale o agente de convidado do Windows Azure.
5. Transferir e instalar o [mais recente versão do agente do MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Tem de ter direitos de administrador para concluir a instalação.
6. Certifique-se de que os serviços do agente de convidado do Windows Azure são apresentados nos serviços.
7. Execute uma cópia de segurança a pedido:
    * No portal, selecione **cópia de segurança agora**.

Além disso, certifique-se de que [Microsoft .NET 4.5 está instalado](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na VM. .NET 4.5 é necessário para o agente da VM comunicar com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado na VM está desatualizado (para VMs do Linux)

#### <a name="solution"></a>Solução
Mais relacionados com o agente ou relacionados com a extensão de falhas para VMs do Linux são causadas por problemas que afetam um agente VM desatualizado. Para resolver este problema, siga estas Diretrizes gerais:

1. Siga as instruções para [atualizar o agente da VM do Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Estamos *altamente recomendável* que Atualize o agente apenas por meio de um repositório de distribuição. Não é recomendável baixar o código do agente diretamente a partir do GitHub e a atualizá-los. Se o agente mais recente para a sua distribuição não é suportada de distribuição disponíveis, contacte para obter instruções sobre como instalá-lo. Para verificar se o agente mais recente, vá para o [agente do Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) página no repositório do GitHub.

2. Certifique-se de que o agente do Azure está em execução a VM ao executar o seguinte comando: `ps -e`

   Se o processo não está em execução, reinicie-o usando os seguintes comandos:

   * Para o Ubuntu: `service walinuxagent start`
   * Para outras distribuições: `service waagent start`

3. [Configurar o agente de reinício automático](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Execute uma nova cópia de segurança de teste. Se a falha persistir, recolha os seguintes registos da VM:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Se é necessário o registo verboso para waagent, siga estes passos:

1. No ficheiro /etc/waagent.Conf., localize a seguinte linha: **Ativar o registo verboso (y | n)**
2. Alteração da **Logs.Verbose** partir da *n* para *y*.
3. Guardar a alteração e, em seguida, reinicie waagent, concluindo os passos descritos anteriormente nesta secção.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Não é possível obter o estado do instantâneo ou não pode ser criado um instantâneo
A cópia de segurança VM depende de emitir um comando de instantâneo para a conta de armazenamento subjacente. Cópia de segurança pode falhar porque ele não tem acesso à conta de armazenamento ou porque a execução da tarefa de instantâneo é atrasada.

#### <a name="solution"></a>Solução
As seguintes condições poderão provocar a falha na tarefa de instantâneo:

| Causa | Solução |
| --- | --- |
| O estado da VM é informado incorretamente, uma vez que a VM é encerrada no protocolo RDP (Remote Desktop). | Se encerrar a VM na RDP, consulte o portal para determinar se o estado da VM está correto. Se não estiver correto, encerre a VM no portal, utilizando o **encerramento** opção no dashboard de VM. |
| A VM não é possível obter o endereço de anfitrião ou de recursos de infraestrutura do DHCP. | DHCP tem de estar ativado no computador convidado para a cópia de segurança de VM de IaaS para trabalhar. Se a VM não é possível obter o endereço de anfitrião ou de recursos de infraestrutura da resposta DHCP 245, não é possível transferir ou executar quaisquer extensões. Se precisar de um IP privado estático, estes devem ser configurado através da **Portal do Azure** ou **PowerShell** e certifique-se a opção de DHCP no interior da VM é ativada. [Saiba mais](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) sobre como configurar um endereço IP estático com o PowerShell.

### <a name="the-backup-extension-fails-to-update-or-load"></a>A extensão de cópia de segurança não consegue atualizar ou de carga
Se não é possível carregar extensões, a cópia de segurança falha porque não pode ser criado um instantâneo.

#### <a name="solution"></a>Solução

Desinstale a extensão para forçar a extensão de VMSnapshot para recarregar. Da próxima tentativa de cópia de segurança recarrega a extensão.

Para desinstalar a extensão:

1. Na [portal do Azure](https://portal.azure.com/), vá para a VM que está com falhas de cópia de segurança.
2. Selecione **definições**.
3. Selecione **Extensions** (Extensões).
4. Selecione **extensão de Vmsnapshot**.
5. Selecione **desinstalar**.

Para VM do Linux, se a extensão do VMSnapshot não mostra no portal do Azure, [atualizar o agente do Linux do Azure](../virtual-machines/linux/update-agent.md), e, em seguida, execute a cópia de segurança.

Concluir estes passos, faz com que a extensão de ser reinstalados durante a próxima cópia de segurança.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Remova o bloqueio do grupo de recursos de ponto de recuperação
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Aceda a **opção de todos os recursos**, selecione o grupo de recursos de coleção de ponto de restauro no seguinte formato AzureBackupRG_`<Geo>`_`<number>`.
3. Na **configurações** secção, selecione **bloqueios** para apresentar os bloqueios.
4. Para remover o bloqueio, selecione as reticências e clique em **eliminar**.

    ![Bloqueio de eliminação](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> Limpar a coleção de ponto de restauro
Depois de remover o bloqueio, os pontos de restauro têm ser limpos. Para limpar os pontos de restauro, siga qualquer um dos métodos:<br>
* [Limpeza do wsu restauro coleção de ponto ao executar a cópia de segurança ad hoc](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Limpeza do wsu restauro do ponto de coleção a partir do portal do Azure](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Limpeza do wsu restauro coleção de ponto ao executar a cópia de segurança ad hoc
Depois de remover o bloqueio, acione uma cópia de segurança do ad hoc/manual. Isto irá garantir que os pontos de restauro são limpas automaticamente. Esperar que esta operação ad hoc/manual falhar pela primeira vez; No entanto, ele garantirá a limpeza automática em vez de eliminação manual de pontos de restauro. Após a limpeza deve ter êxito a cópia de segurança agendada seguinte.

> [!NOTE]
> A limpeza automática irão ocorrer após algumas horas de acionar a cópia de segurança do ad hoc/manual. Se continuar a falhar a cópia de segurança agendada, em seguida, tente eliminar manualmente a coleção do ponto de restauro utilizando os passos listados [aqui](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Limpeza do wsu restauro do ponto de coleção a partir do portal do Azure <br>

Para limpar manualmente o restauro pontos de coleção que não estão a ser desmarcadas devido ao bloqueio no grupo de recursos, tente os seguintes passos:
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Sobre o **Hub** menu, clique em **todos os recursos**, selecione o grupo de recursos com o seguinte formato AzureBackupRG_`<Geo>`_`<number>` onde está localizada a sua VM.

    ![Bloqueio de eliminação](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Clique em grupo de recursos, o **descrição geral** é apresentado o painel.
4. Selecione **mostrar tipos ocultos** opção para apresentar todos os recursos ocultos. Selecione as coleções do ponto de restauro com o seguinte formato AzureBackupRG_`<VMName>`_`<number>`.

    ![Bloqueio de eliminação](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Clique em **eliminar**, para limpar a coleção de ponto de restauro.
6. Repita a operação de cópia de segurança novamente.

> [!NOTE]
 >Se o recurso (coleção de RP) tem grande número de pontos de restauro, em seguida, eliminar o mesmo a partir do portal pode tempo limite e falha. Este é um problema de CRP conhecido, onde todos os pontos de restauro não são eliminados no período de tempo estipulado e a operação exceder o tempo limite; No entanto, a operação de eliminação é normalmente bem-sucedida depois de 2 ou 3 tentativas.
