---
title: 'Solucionar problemas de falha de backup do Azure: Status do agente convidado não disponível'
description: Sintomas, causas e resoluções de falhas de backup do Azure relacionadas ao agente, à extensão e aos discos.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
keywords: Backup do Azure; Agente de VM; Conectividade de rede;
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 391ad5c6535d457c2df988cd29d21e481310b17f
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061769"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Solucionar problemas de falha de backup do Azure: Problemas com o agente ou extensão

Este artigo fornece etapas de solução de problemas que podem ajudá-lo a resolver erros de backup do Azure relacionados à comunicação com o agente e a extensão da VM.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable-o agente de VM não pode se comunicar com o backup do Azure

**Código de erro**: UserErrorGuestAgentStatusUnavailable <br>
**Mensagem de erro**: O agente de VM não pode se comunicar com o backup do Azure<br>

O agente de VM do Azure pode ser interrompido, desatualizado, em um estado inconsistente ou não instalado e impedir que o serviço de backup do Azure dispare os instantâneos.  

- Se o agente de VM for interrompido ou estiver em um estado inconsistente, **reinicie o agente** e repita a operação de backup (experimente um backup ad hoc). Para obter as etapas para reiniciar o agente, consulte [VMs do Windows](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) ou VMS do [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent).
- Se o agente de VM não estiver instalado ou estiver desatualizado, Instale/atualize o agente de VM e repita a operação de backup. Para obter as etapas para instalar/atualizar o agente, consulte [VMs do Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou [VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent).  

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError-não foi possível se comunicar com o agente de VM para o status do instantâneo

**Código de erro**: GuestAgentSnapshotTaskStatusError<br>
**Mensagem de erro**: Não foi possível comunicar com o agente da VM para obter o estado do instantâneo <br>

Depois de registrar e agendar uma VM para o serviço de backup do Azure, o backup inicia o trabalho comunicando-se com a extensão de backup da VM para obter um instantâneo pontual. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e repita a operação:  

**Motivo 1: [O agente está instalado na VM, mas não está respondendo (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Motivo 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Causa 3: [O status do instantâneo não pode ser recuperado ou um instantâneo não pode ser obtido](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Causa 4: [A extensão de backup não é atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached-o limite máximo da coleção de pontos de restauração foi atingido

**Código de erro**: UserErrorRpCollectionLimitReached <br>
**Mensagem de erro**: O limite máximo da coleção de pontos de restauração foi atingido. <br>

- Esse problema pode ocorrer se houver um bloqueio no grupo de recursos do ponto de recuperação, impedindo a limpeza automática de pontos de recuperação.
- Esse problema também pode ocorrer se vários backups forem disparados por dia. Atualmente, é recomendável apenas um backup por dia, pois os pontos de restauração instantâneas são mantidos por 1-5 dias de acordo com a retenção de instantâneo configurada e somente 18 RPs podem ser associados a uma VM em um determinado momento. <br>

Ação recomendada:<br>
Para resolver esse problema, remova o bloqueio no grupo de recursos da VM e repita a operação para disparar a limpeza.
> [!NOTE]
> O serviço de backup cria um grupo de recursos separado do que o grupo de recursos da VM para armazenar a coleção de pontos de restauração. Os clientes são aconselhados a não bloquear o grupo de recursos criado para uso pelo serviço de backup. O formato de nomenclatura do grupo de recursos criado pelo serviço de backup é: AzureBackupRG_`<Geo>`_`<number>` ex: AzureBackupRG_northeurope_1

**Passo 1: [Remover o bloqueio do grupo de recursos do ponto de restauração](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Passo 2: [Limpar coleção de pontos de restauração](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured-o backup não tem permissões suficientes para o cofre de chaves para backup de VMs criptografadas

**Código de erro**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Mensagem de erro**: O backup não tem permissões suficientes para o cofre de chaves para o backup de VMs criptografadas. <br>

Para que uma operação de backup tenha sucesso em VMs criptografadas, ela deve ter permissões para acessar o cofre de chaves. Isso pode ser feito usando o [portal do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) ou por meio do [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Falha na operação ExtensionSnapshotFailedNoNetwork-snapshot devido a nenhuma conectividade de rede na máquina virtual

**Código de erro**: ExtensionSnapshotFailedNoNetwork<br>
**Mensagem de erro**: A operação de instantâneo falhou devido à falta de conetividade de rede na máquina virtual<br>

Depois de registrar e agendar uma VM para o serviço de backup do Azure, o backup inicia o trabalho comunicando-se com a extensão de backup da VM para obter um instantâneo pontual. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e repita a operação:

**Motivo 1: [O status do instantâneo não pode ser recuperado ou um instantâneo não pode ser obtido](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Motivo 2: [A extensão de backup não é atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [A VM não tem acesso à Internet](#the-vm-has-no-internet-access)**

## <a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>Falha na operação de extensão ExtensionOperationFailedForManagedDisks-VMSnapshot

**Código de erro**: ExtensionOperationFailedForManagedDisks <br>
**Mensagem de erro**: Falha na operação de extensão do VMSnapshot<br>

Depois de registrar e agendar uma VM para o serviço de backup do Azure, o backup inicia o trabalho comunicando-se com a extensão de backup da VM para obter um instantâneo pontual. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e repita a operação:  
**Motivo 1: [O status do instantâneo não pode ser recuperado ou um instantâneo não pode ser obtido](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Motivo 2: [A extensão de backup não é atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [O agente está instalado na VM, mas não está respondendo (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 4: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>Falha de BackUpOperationFailed/BackUpOperationFailedV2-backup, com um erro interno

**Código de erro**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Mensagem de erro**: O backup falhou com um erro interno-tente novamente a operação em alguns minutos <br>

Depois de registrar e agendar uma VM para o serviço de backup do Azure, o backup inicia o trabalho comunicando-se com a extensão de backup da VM para obter um instantâneo pontual. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e repita a operação:  
**Motivo 1: [O agente instalado na VM, mas não está respondendo (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Motivo 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [O status do instantâneo não pode ser recuperado ou um instantâneo não pode ser obtido](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 4: [A extensão de backup não é atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)**  
**Causa 5: O serviço de backup não tem permissão para excluir os pontos de restauração antigos devido a um bloqueio de grupo de recursos** <br>
**Causa 6: [A VM não tem acesso à Internet](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-4095-gb"></a>UserErrorUnsupportedDiskSize-o backup do Azure no momento não dá suporte a tamanhos de disco maiores que 4095 GB

**Código de erro**: UserErrorUnsupportedDiskSize <br>
**Mensagem de erro**: Atualmente, o backup do Azure não dá suporte a tamanhos de disco maiores que 4095 GB <br>

Sua operação de backup pode falhar ao fazer backup de uma VM com um tamanho de disco maior que 4095 GB. Para se inscrever em uma visualização pública limitada de suporte de disco grande do backup do Azure para discos com mais de 4 TB e até 30 TB de tamanho, consulte este [artigo](backup-azure-vms-introduction.md#limited-public-preview-backup-of-vm-with-disk-sizes-up-to-30tb).

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress-não é possível iniciar o backup, já que outra operação de backup está em andamento

**Código de erro**: UserErrorBackupOperationInProgress <br>
**Mensagem de erro**: Não é possível iniciar o backup, pois outra operação de backup está em andamento<br>

O trabalho de backup recente falhou porque há um trabalho de backup existente em andamento. Você não pode iniciar um novo trabalho de backup até que o trabalho atual seja concluído. Verifique se a operação de backup atualmente em andamento foi concluída antes de disparar ou agendar outras operações de backup. Para verificar o status dos trabalhos de backup, execute as etapas a seguir:

1. Entre no portal do Azure, clique em **todos os serviços**. Escreva Serviços de Recuperação e clique em **Cofres dos Serviços de Recuperação**. É apresentada a lista dos cofres dos serviços de recuperação.
2. Na lista de cofres dos serviços de recuperação, selecione um cofre no qual o backup está configurado.
3. No menu do painel do cofre, clique em **trabalhos de backup** ele exibe todos os trabalhos de backup.

- Se um trabalho de backup estiver em andamento, aguarde a conclusão ou cancele o trabalho de backup.
  - Para cancelar o trabalho de backup, clique com o botão direito do mouse no trabalho de backup e clique em **Cancelar** ou use o [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
- Se você tiver reconfigurado o backup em um cofre diferente, verifique se não há nenhum trabalho de backup em execução no cofre antigo. Se existir, cancele o trabalho de backup.
  - Para cancelar a tarefa de cópia de segurança, faça clique com o botão direito do rato na tarefa de cópia de segurança e clique em **Cancelar** ou utilize o [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)

4. Repita a operação de backup.

Se a operação de backup agendado estiver demorando mais, em conflito com a próxima configuração de backup, revise as [práticas recomendadas](backup-azure-vms-introduction.md#best-practices), [desempenho de backup](backup-azure-vms-introduction.md#backup-performance)e [consideração de restauração](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-vm-has-no-internet-access"></a>A VM não tem acesso à Internet

De acordo com o requisito de implantação, a VM não tem acesso à Internet. Ou pode ter restrições que impeçam o acesso à infraestrutura do Azure.

Para funcionar corretamente, a extensão de backup requer conectividade com os endereços IP públicos do Azure. A extensão envia comandos para um ponto de extremidade de armazenamento do Azure (URL HTTPs) para gerenciar os instantâneos da VM. Se a extensão não tiver acesso à Internet pública, o backup eventualmente falhará.

#### <a name="solution"></a>Solução

Para resolver o problema de rede, consulte [estabelecer conectividade de rede](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado na VM, mas não está respondendo (para VMs do Windows)

#### <a name="solution"></a>Solução

O agente de VM pode ter sido corrompido ou o serviço pode ter sido interrompido. A reinstalação do agente de VM ajuda a obter a versão mais recente. Ele também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço de agente convidado do Windows Azure está em execução nos serviços de VM (Services. msc). Tente reiniciar o serviço do agente convidado do Windows Azure e iniciar o backup.
2. Se o serviço do agente convidado do Windows Azure não estiver visível em serviços, no painel de controle, vá para **programas e recursos** para determinar se o serviço do agente convidado do Windows Azure está instalado.
3. Se o agente convidado do Windows Azure aparecer em **programas e recursos**, desinstale o agente convidado do Windows Azure.
4. Baixe e instale a [versão mais recente do MSI do agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você deve ter direitos de administrador para concluir a instalação.
5. Verifique se os serviços de agente convidado do Windows Azure aparecem nos serviços.
6. Executar um backup sob demanda:

- No portal, selecione **fazer backup agora**.

Além disso, verifique se o [Microsoft .NET 4,5 está instalado](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na VM. O .NET 4,5 é necessário para que o agente de VM se comunique com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado na VM está desatualizado (para VMs do Linux)

#### <a name="solution"></a>Solução

A maioria das falhas relacionadas ao agente ou relacionadas à extensão para VMs do Linux são causadas por problemas que afetam um agente de VM desatualizado. Para solucionar esse problema, siga estas diretrizes gerais:

1. Siga as instruções para [atualizar o agente de VM do Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > É *altamente recomendável* que você atualize o agente somente por meio de um repositório de distribuição. Não é recomendável baixar o código do agente diretamente do GitHub e atualizá-lo. Se o agente mais recente para sua distribuição não estiver disponível, entre em contato com o suporte de distribuição para obter instruções sobre como instalá-lo. Para verificar o agente mais recente, acesse a página do [agente Linux do Windows Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório github.

2. Verifique se o agente do Azure está em execução na VM executando o seguinte comando:`ps -e`

   Se o processo não estiver em execução, reinicie-o usando os seguintes comandos:

   - Para o Ubuntu:`service walinuxagent start`
   - Para outras distribuições:`service waagent start`

3. [Configure o agente de reinicialização automática](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Execute um novo backup de teste. Se a falha persistir, colete os seguintes logs da VM:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Se você precisar de log detalhado para waagent, siga estas etapas:

1. No arquivo/etc/waagent.conf, localize a seguinte linha: **Habilitar log detalhado (y | n)**
2. Altere o valor **logs. Verbose** de *n* para *y*.
3. Salve a alteração e reinicie o waagent concluindo as etapas descritas anteriormente nesta seção.

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>O status do instantâneo não pode ser recuperado ou um instantâneo não pode ser obtido

O backup da VM depende de emitir um comando de instantâneo para a conta de armazenamento subjacente. O backup pode falhar porque não tem acesso à conta de armazenamento ou porque a execução da tarefa de instantâneo está atrasada.

#### <a name="solution"></a>Solução

As seguintes condições podem causar falha na tarefa de instantâneo:

| Causa | Solução |
| --- | --- |
| O status da VM é relatado incorretamente porque a VM está desligada no protocolo RDP (RDP). | Se você desligar a VM no RDP, verifique o portal para determinar se o status da VM está correto. Se não estiver correto, desligue a VM no portal usando a opção de desligamento no painel da VM. |
| A VM não pode obter o host ou o endereço de malha do DHCP. | O DHCP deve ser habilitado dentro do convidado para o backup da VM IaaS funcionar. Se a VM não puder obter o host ou o endereço de malha da resposta do DHCP 245, ele não poderá baixar nem executar nenhuma extensão. Se você precisar de um IP privado estático, deverá configurá-lo por meio do **portal do Azure** ou do **PowerShell** e certificar-se de que a opção DHCP dentro da VM esteja habilitada. [Saiba mais](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) sobre como configurar um endereço IP estático com o PowerShell.

### <a name="the-backup-extension-fails-to-update-or-load"></a>A extensão de backup não é atualizada ou carregada

Se as extensões não puderem ser carregadas, o backup falhará porque um instantâneo não pode ser obtido.

#### <a name="solution"></a>Solução

Desinstale a extensão para forçar a recarga da extensão VMSnapshot. A próxima tentativa de backup recarrega a extensão.

Para desinstalar a extensão:

1. Na [portal do Azure](https://portal.azure.com/), vá para a VM que está apresentando falha de backup.
2. Selecione **definições**.
3. Selecione **Extensions** (Extensões).
4. Selecione a **extensão Vmsnapshot**.
5. Selecione **desinstalar**.

Para a VM do Linux, se a extensão VMSnapshot não for mostrada no portal do Azure, [atualize o agente Linux do Azure](../virtual-machines/linux/update-agent.md)e execute o backup.

A conclusão dessas etapas faz com que a extensão seja reinstalada durante o próximo backup.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Remover o bloqueio do grupo de recursos do ponto de recuperação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Opção ir para **todos os recursos**, selecione o grupo de recursos coleção de pontos de restauração no`<Geo>`seguinte`<number>`formato AzureBackupRG_ _.
3. Na seção **configurações** , selecione **bloqueios** para exibir os bloqueios.
4. Para remover o bloqueio, selecione as reticências e clique em **excluir**.

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a>Limpar coleção de pontos de restauração

Depois de remover o bloqueio, os pontos de restauração precisam ser limpos. Para limpar os pontos de restauração, siga qualquer um dos métodos:<br>

- [Limpar a coleção de pontos de restauração executando backup ad hoc](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
- [Limpar coleção de pontos de restauração de portal do Azure](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Limpar a coleção de pontos de restauração executando backup ad hoc

Depois de remover o bloqueio, dispare um backup ad hoc/manual. Isso garantirá que os pontos de restauração sejam limpos automaticamente. Esperar que essa operação ad hoc/manual falhe na primeira vez; no entanto, ele garantirá a limpeza automática em vez da exclusão manual de pontos de restauração. Após a limpeza, o próximo backup agendado deverá ter sucesso.

> [!NOTE]
> A limpeza automática ocorrerá após algumas horas de acionamento do backup ad hoc/manual. Se o backup agendado ainda falhar, tente excluir manualmente a coleção de pontos de restauração usando as etapas listadas [aqui](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Limpar coleção de pontos de restauração de portal do Azure <br>

Para limpar manualmente a coleção de pontos de restauração, que não é limpa devido ao bloqueio no grupo de recursos, tente as seguintes etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu **Hub** , clique em **todos os recursos**, selecione o grupo de recursos com o seguinte`<Geo>`formato`<number>` AzureBackupRG_ _ onde sua VM está localizada.

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Clique em grupo de recursos, a folha **visão geral** é exibida.
4. Selecione a opção **Mostrar tipos ocultos** para exibir todos os recursos ocultos. Selecione as coleções de pontos de restauração com o seguinte`<VMName>`formato`<number>`AzureBackupRG_ _.

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Clique em **excluir** para limpar a coleção de pontos de restauração.
6. Repita a operação de backup novamente.

> [!NOTE]
 >Se o recurso (coleção de RP) tiver um grande número de pontos de restauração, a exclusão deles do portal poderá expirar e falhar. Trata-se de um problema de CRP conhecido, em que todos os pontos de restauração não são excluídos no tempo estipulado e a operação atinge o tempo limite; no entanto, a operação de exclusão geralmente é realizada com sucesso após 2 ou 3 tentativas.
