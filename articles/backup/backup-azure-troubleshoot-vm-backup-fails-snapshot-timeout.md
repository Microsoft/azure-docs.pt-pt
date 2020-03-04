---
title: Problemas de agente e questões de extensão
description: Sintomas, causas e resoluções de falhas de backup azure relacionadas com agente, extensão e discos.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: beb20518d1350335ceed285f4d5cd9da135132e5
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255738"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Falha de backup de Troubleshoot Azure: Problemas com o agente ou extensão

Este artigo fornece passos de resolução de problemas que podem ajudá-lo a resolver erros de backup azure relacionados com a comunicação com o agente VM e extensão.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusIn - Agente VM incapaz de comunicar com backup Azure

**Código de erro**: UserErrorGuestAgentStatusIn <br>
**Mensagem de erro**: Agente VM incapaz de comunicar com backup Azure<br>

O agente Azure VM pode ser detido, desatualizado, num estado inconsistente, ou não instalado. Estes estados impedem que o serviço de backup Azure desencadeie instantâneos.

- **Open Azure portal > VM > Definições > Properties pane** > certifique-se de que o **Estado** vM está **em execução** e o **estado** do agente está **pronto**. Se o agente VM for parado ou estiver em estado inconsistente, reinicie o agente<br>
  - Para vMs windows, siga estes [passos](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) para reiniciar o Agente Convidado.<br>
  - Para os VMs Linux, siga estes [passos](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) para reiniciar o Agente Convidado.
- **Open Azure portal > VM > Definições > Extensões** > Certifique-se de que todas as extensões estão em **estado de provisionamento.** Caso contrário, siga estes [passos](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) para resolver a questão.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - Não conseguiu comunicar com o agente VM para o estado de instantâneo

**Código de erro**: GuestAgentSnapshotTaskStatusError<br>
**Mensagem de erro**: Não consegui comunicar com o agente VM para o estado de instantâneo <br>

Depois de registar e agendar um VM para o serviço de backup Azure, o Backup inicia o trabalho comunicando com a extensão de backup VM para tirar uma foto pontual. Qualquer uma das seguintes condições pode impedir que o instantâneo seja acionado. Se o instantâneo não for acionado, pode ocorrer uma falha de reserva. Complete os seguintes passos de resolução de problemas na ordem listada e, em seguida, tente novamente a sua operação:  

**Causa 1: [O agente está instalado no VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Causa 2: [O agente instalado no VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Causa 3: [O estado do instantâneo não pode ser recuperado, ou uma foto não pode ser tirada](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Causa 4: [As opções de configuração vM-Agent não estão definidas (para VMs Linux)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed - O VM está em estado de provisionamento falhado

**Código de erro**: UserErrorVmProvisioningStateFailed<br>
**Mensagem de erro**: O VM está em estado de provisionamento falhado<br>

Este erro ocorre quando uma das falhas de extensão coloca o VM no estado falhado de provisionamento.<br>**Open Azure portal > VM > Definições > Extensões > Estado** de extensões e verificar se todas as extensões estão em **estado de provisionamento.**

- Se a extensão VMSnapshot estiver num estado falhado, clique à direita na extensão falhada e remova-a. Desencadeie um reforço a pedido. Esta ação reinstalará as extensões e executará o trabalho de backup.  <br>
- Se qualquer outra extensão estiver em estado falhado, então pode interferir com o backup. Certifique-se de que os problemas de extensão estão resolvidos e tente novamente a operação de backup.  

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - O limite máximo de recolha de Ponto de Restauro alcançou

**Código de erro**: UserErrorRpCollectionLimitReached <br>
**Mensagem de erro**: O limite máximo de recolha do Restore Point atingiu. <br>

- Esta questão pode acontecer se houver um bloqueio no grupo de recursos do ponto de recuperação que impede a limpeza automática dos pontos de recuperação.
- Este problema também pode acontecer se várias cópias de segurança forem acionadas por dia. Atualmente recomendamos apenas uma cópia de segurança por dia, uma vez que os pontos de restauro instantâneos são retidos por 1-5 dias por retenção de instantâneos configurados e apenas 18 RPs instantâneos podem ser associados a um VM em qualquer momento. <br>
- O número de pontos de restauro através de recolhas de pontos de restauração e grupos de recursos para um VM não pode exceder 18. Para criar um novo ponto de restauro, elimine os pontos de restauro existentes.

Ação Recomendada:<br>
Para resolver este problema, retire o bloqueio do grupo de recursos do VM e tente novamente a operação para desencadear a limpeza.
> [!NOTE]
> O serviço de backup cria um grupo de recursos separado do que o grupo de recursos do VM para armazenar a recolha de pontos de restauração. Os clientes são aconselhados a não bloquear o grupo de recursos criado para uso pelo serviço de backup. O formato de nomeação do grupo de recursos criado pelo serviço De backup é: AzureBackupRG_`<Geo>`_`<number>` Eg: AzureBackupRG_northeurope_1

**Passo 1: [Retire o bloqueio do grupo de recursos do ponto de restauro](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Passo 2: [Limpar a recolha do ponto de restauro](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - A cópia de segurança não tem permissões suficientes para o cofre da chave para cópia de segurança de VMs encriptados

**Código de erro**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Mensagem de erro**: A cópia de segurança não tem permissões suficientes para o cofre da chave para cópia de segurança de VMs encriptados. <br>

Para que uma operação de reserva tenha sucesso em VMs encriptados, deve ter permissões para aceder ao cofre chave. As permissões podem ser definidas através do [portal Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) ou através [da PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - A operação snapshot falhou devido a nenhuma conectividade de rede na máquina virtual

**Código de erro**: ExtensionSnapshotFailedNoNetwork<br>
**Error message**: A operação instantânea falhou devido a nenhuma conectividade de rede na máquina virtual<br>

Depois de registar e agendar um VM para o serviço de backup Azure, o Backup inicia o trabalho comunicando com a extensão de backup VM para tirar uma foto pontual. Qualquer uma das seguintes condições pode impedir que o instantâneo seja acionado. Se o instantâneo não for acionado, pode ocorrer uma falha de reserva. Complete o seguinte passo de resolução de problemas e, em seguida, tente novamente a sua operação:

**[O estado instantâneo não pode ser recuperado, ou uma foto não pode ser tirada](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks - Operação de extensão VMSnapshot falhou

**Código de erro**: Operação De extensãoFalhaforManagedDisks <br>
**Mensagem de erro**: Operação de extensão VMSnapshot falhou<br>

Depois de registar e agendar um VM para o serviço de backup Azure, o Backup inicia o trabalho comunicando com a extensão de backup VM para tirar uma foto pontual. Qualquer uma das seguintes condições pode impedir que o instantâneo seja acionado. Se o instantâneo não for acionado, pode ocorrer uma falha de reserva. Complete os seguintes passos de resolução de problemas na ordem listada e, em seguida, tente novamente a sua operação:  
**Causa 1: [O estado do instantâneo não pode ser recuperado, ou uma foto não pode ser tirada](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [O agente está instalado no VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 3: [O agente instalado no VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - Falha de backup, com um erro interno

**Código de erro**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Mensagem de erro**: Cópia de segurança falhou com um erro interno - Por favor, tente novamente a operação em poucos minutos <br>

Depois de registar e agendar um VM para o serviço de backup Azure, o Backup inicia o trabalho comunicando com a extensão de backup VM para tirar uma foto pontual. Qualquer uma das seguintes condições pode impedir que o instantâneo seja acionado. Se o instantâneo não for acionado, pode ocorrer uma falha de reserva. Complete os seguintes passos de resolução de problemas na ordem listada e, em seguida, tente novamente a sua operação:  
**Causa 1: [O agente instalado no VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [O agente instalado no VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [O estado do instantâneo não pode ser recuperado, ou uma foto não pode ser tirada](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 4: O serviço de [backup não tem permissão para apagar os antigos pontos de restauro por causa de um bloqueio de grupo de recursos](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsuportaedDiskSize - O tamanho ou s do disco configurado não é suportado atualmente por Backup Azure

**Código de erro**: UserErrorUnsuportadDiskSize <br>
**Mensagem de erro**: O tamanho do disco configurado não é suportado atualmente por Cópia de Segurança Azure. <br>

A sua operação de backup pode falhar ao apoiar um VM com um tamanho de disco superior a 32 TB. Além disso, a cópia de segurança de discos encriptados superiores a 4 TB em tamanho não é suportada atualmente. Certifique-se de que o tamanho ou os discos são inferiores ou iguais ao limite suportado, dividindo o ou o disco.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - Incapaz de iniciar a cópia de segurança, uma vez que outra operação de backup está em curso

**Código de erro**: UserErrorBackupOperationInProgress <br>
**Mensagem de erro**: Incapaz de iniciar backup, uma vez que outra operação de backup está em curso<br>

O teu recente trabalho de reserva falhou porque há um trabalho de reserva em andamento. Não podes começar um novo trabalho de reserva até que o trabalho atual termine. Certifique-se de que a operação de backup atualmente em curso está concluída antes de desencadear ou agendar outras operações de backup. Para verificar o estado dos trabalhos de backup, faça os seguintes passos:

1. Inscreva-se no portal Azure, clique em **Todos os serviços.** Escreva Serviços de Recuperação e clique em **Cofres dos Serviços de Recuperação**. É apresentada a lista dos cofres dos serviços de recuperação.
2. A partir da lista de cofres de serviços de recuperação, selecione um cofre no qual a cópia de segurança está configurada.
3. No menu do painel de instrumentos do cofre, clique em **Backup Jobs,** mostra todos os trabalhos de backup.
   - Se um trabalho de reserva estiver em andamento, espere que complete ou cancele o trabalho de reserva.
     - Para cancelar o trabalho de backup, clique no trabalho de backup e clique em **Cancelar** ou utilizar [powerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
   - Se reconfigurou o backup num cofre diferente, então certifique-se de que não há trabalhos de reserva no cofre antigo. Se existe, cancele o trabalho de reserva.
     - Para cancelar a tarefa de cópia de segurança, faça clique com o botão direito do rato na tarefa de cópia de segurança e clique em **Cancelar** ou utilize o [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. Retente a operação de reserva.

Se a operação de backup programada estiver a demorar mais tempo, entrando em conflito com a próxima configuração de backup, reveja as [Melhores Práticas,](backup-azure-vms-introduction.md#best-practices) [Desempenho de Backup](backup-azure-vms-introduction.md#backup-performance)e Restaurar a [consideração](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError - A cópia de segurança falhou devido a um erro. Para mais detalhes, consulte detalhes da mensagem de erro de trabalho

**Código de erro**: UserErrorCrpReportedUserError <br>
**Mensagem de erro**: A cópia de segurança falhou devido a um erro. Para mais detalhes, consulte os detalhes da mensagem de erro de trabalho.

Este erro é reportado a partir do IaaS VM. Para identificar a causa principal do problema, vá às definições do cofre dos Serviços de Recuperação. Na secção **de Monitorização,** selecione **trabalhos de backup** para filtrar e visualizar o estado. Clique em Falhas para rever os detalhes **subjacentes** à mensagem de erro. Tome mais ações de acordo com as recomendações na página de detalhes de erros.

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado no VM, mas não responde (para VMs do Windows)

#### <a name="solution"></a>Solução

O agente vm pode ter sido corrompido, ou o serviço pode ter sido interrompido. Reinstalar o agente VM ajuda a obter a versão mais recente. Também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço de Agente Convidado Windows Azure está a funcionar nos serviços VM (services.msc). Tente reiniciar o serviço de Agente Convidado windows Azure e inicie a cópia de segurança.
2. Se o serviço de Agente Convidado Windows Azure não estiver visível nos serviços, no Painel de Controlo, vá a **Programas e Funcionalidades** para determinar se o serviço de Agente Convidado do Windows Azure está instalado.
3. Se o Agente Convidado do Windows Azure aparecer em **Programas e Funcionalidades,** desinstale o Agente Convidado do Windows Azure.
4. Descarregue e instale a [versão mais recente do agente MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Deve ter direitos de administrador para completar a instalação.
5. Verifique se os serviços do Agente Convidado Do Windows Azure aparecem nos serviços.
6. Faça um backup a pedido:
   - No portal, selecione **Backup Now**.

Além disso, verifique se o [Microsoft .NET 4.5 está instalado](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) no VM. .NET 4.5 é necessário para que o agente VM comunique com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado no VM está desatualizado (para VMs Linux)

#### <a name="solution"></a>Solução

A maioria das falhas relacionadas com o agente ou as falhas relacionadas com a extensão dos VMs linux são causadas por problemas que afetam um agente VM desatualizado. Para resolver esta questão, siga estas orientações gerais:

1. Siga as instruções para [atualizar o agente Linux VM](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > *Recomendamos vivamente* que atualize o agente apenas através de um repositório de distribuição. Não recomendamos o download do código do agente diretamente do GitHub e a atualização do mesmo. Se o mais recente agente para a sua distribuição não estiver disponível, contacte o suporte de distribuição para obter instruções sobre como instalá-lo. Para verificar o agente mais recente, vá à página de [agente do Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) no repositório GitHub.

2. Certifique-se de que o agente Azure está a funcionar no VM executando o seguinte comando: `ps -e`

   Se o processo não estiver em execução, reinicie-o utilizando os seguintes comandos:

   - Para Ubuntu: `service walinuxagent start`
   - Para outras distribuições: `service waagent start`

3. [Configure o agente de reinício automático](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Faça um novo teste de reserva. Se a falha persistir, recolha os seguintes registos do VM:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Se necessitar de extração verbosa para agente, siga estes passos:

1. No ficheiro /etc/waagent.conf, localize a seguinte linha: Habilitar a **exploração madeireira verbosa (y,**
2. Altere o valor **Logs.Verbose** de *n* para *y*.
3. Guarde a alteração e, em seguida, reinicie o waagent completando os passos descritos anteriormente nesta secção.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>As opções de configuração VM-Agent não estão definidas (para VMs Linux)

Um ficheiro de configuração (/etc/waagent.conf) controla as ações do agente. Configuração De definição De Definição De Definição De **Extensões.Ativação** e **Provisionamento.O agente** deve ser definido para **y** para o backup funcionar.
Para obter a lista completa das opções de ficheiros de configuração do VM-Agent, consulte <https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>O estado instantâneo não pode ser recuperado, ou uma foto não pode ser tirada

A cópia de segurança VM baseia-se na emissão de um comando instantâneo para a conta de armazenamento subjacente. A cópia de segurança pode falhar quer porque não tem acesso à conta de armazenamento, quer porque a execução da tarefa instantânea está atrasada.

#### <a name="solution"></a>Solução

As seguintes condições podem fazer com que a tarefa instantânea falhe:

| Causa | Solução |
| --- | --- |
| O estado vM é reportado incorretamente porque o VM está desligado no Protocolo de Ambiente de Trabalho Remoto (RDP). | Se desligar o VM em RDP, verifique o portal para determinar se o estado vm está correto. Se não estiver correto, desligue o VM no portal utilizando a opção **Desativação** no painel vM. |
| O VM não consegue obter o hospedeiro ou endereço de tecido da DHCP. | O DHCP deve ser ativado dentro do hóspede para que o backup IaaS VM funcione. Se o VM não conseguir obter o endereço de anfitrião ou tecido da resposta DHCP 245, não pode descarregar ou executar quaisquer extensões. Se necessitar de um IP privado estático, deve configurá-lo através do **portal Azure** ou **PowerShell** e certificar-se de que a opção DHCP dentro do VM está ativada. [Saiba mais](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) sobre a criação de um endereço IP estático com a PowerShell.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Retire o bloqueio do grupo de recursos do ponto de recuperação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Vá à **opção All Resources**, selecione o grupo de recursos de recolha de pontos de restauro no formato seguinte AzureBackupRG_`<Geo>`_`<number>`.
3. Na secção **Definições,** selecione **Fechaduras** para visualizar as fechaduras.
4. Para remover o bloqueio, selecione a elipse e clique em **Eliminar**.

    ![Eliminar a fechadura](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a>Limpar a recolha de pontos de restauro

Depois de remover a fechadura, os pontos de restauro têm de ser limpos.

Se eliminar o Grupo de Recursos do VM, ou o próprio VM, as imagens instantâneas de restauro dos discos geridos permanecem ativas e expiram de acordo com o conjunto de retenção. Para eliminar as imagens instantâneas de restauro (se já não precisar delas) que estão armazenadas na Restore Point Collection, limpe a recolha do ponto de restauro de acordo com os passos indicados abaixo.

Para limpar os pontos de restauro, siga qualquer um dos métodos:<br>

- [Limpar a recolha de pontos de restauro executando backup a pedido](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Limpar a recolha de pontos de restauro do portal Azure](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Limpar a recolha de pontos de restauro executando backup a pedido

Depois de remover a fechadura, acione uma cópia de segurança a pedido. Esta ação assegurará que os pontos de restauro sejam automaticamente limpos. Espere que esta operação a pedido falhe pela primeira vez; no entanto, assegurará a limpeza automática em vez da eliminação manual dos pontos de restauro. Depois da limpeza, o seu próximo reforço programado deve ter sucesso.

> [!NOTE]
> A limpeza automática acontecerá após algumas horas de ativação da cópia de segurança a pedido. Se a sua cópia de segurança programada ainda falhar, tente apagar manualmente a recolha do ponto de restauro utilizando os passos listados [aqui](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Limpar a recolha de pontos de restauro do portal Azure <br>

Para limpar manualmente a recolha de pontos de restauro, que não está apurada devido ao bloqueio do grupo de recursos, experimente os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu **Hub,** clique em **Todos os recursos**, selecione o grupo Recursos com o seguinte formato AzureBackupRG_`<Geo>`_`<number>` onde o seu VM está localizado.

    ![Eliminar a fechadura](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Clique no grupo Resource, o painel de **visão geral** é apresentado.
4. Selecione **Mostrar tipos ocultos** para exibir todos os recursos escondidos. Selecione as coleções de pontos de restauro com o seguinte formato AzureBackupRG_`<VMName>`_`<number>`.

    ![Eliminar a fechadura](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Clique em **Apagar** para limpar a recolha do ponto de restauro.
6. Volte a tentar a operação de reserva.

> [!NOTE]
 >Se o recurso (RP Collection) tiver um grande número de Pontos de Restauro, então a sua aparação do portal pode esgotar-se e falhar. Trata-se de uma questão conhecida da CRP, em que todos os pontos de restauro não são suprimidos no tempo estipulado e nos prazos de funcionamento; no entanto, a operação de eliminação geralmente é bem sucedida após 2 ou 3 repetições.
