---
title: Restaurar VMs usando o portal do Azure
description: Restaurar uma máquina virtual do Azure de um ponto de recuperação usando o portal do Azure
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: d0b2e85fa3dfb0168c40c6b8838c7b9890c92ab6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844012"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Como restaurar dados de VM do Azure no portal do Azure

Este artigo descreve como restaurar os dados da Azure VM dos pontos de recuperação armazenados nos cofres dos Serviços de Recuperação de [Backup azure.](backup-overview.md)

## <a name="restore-options"></a>Restaurar opções

O backup do Azure fornece várias maneiras de restaurar uma VM.

**Restaurar a opção** | **Detalhes**
--- | ---
**Criar um novo VM** | Cria e obtém rapidamente uma VM básica em funcionamento a partir de um ponto de restauração.<br/><br/> Você pode especificar um nome para a VM, selecionar o grupo de recursos e a rede virtual (VNet) na qual ele será colocado e especificar uma conta de armazenamento para a VM restaurada. A nova VM deve ser criada na mesma região que a VM de origem.
**Restaurar o disco** | Restaura um disco de VM, que pode ser usado para criar uma nova VM.<br/><br/> O backup do Azure fornece um modelo para ajudá-lo a personalizar e criar uma VM. <br/><br> O trabalho de restauração gera um modelo que você pode baixar e usar para especificar configurações de VM personalizadas e criar uma VM.<br/><br/> Os discos são copiados para o Grupo de Recursos que especifica.<br/><br/> Como alternativa, você pode anexar o disco a uma VM existente ou criar uma nova VM usando o PowerShell.<br/><br/> Essa opção será útil se você quiser personalizar a VM, adicionar definições de configuração que não estavam lá no momento do backup ou adicionar configurações que devem ser configuradas usando o modelo ou o PowerShell.
**Substituir os existentes** | Você pode restaurar um disco e usá-lo para substituir um disco na VM existente.<br/><br/> A VM atual deve existir. Se ela tiver sido excluída, essa opção não poderá ser usada.<br/><br/> O backup do Azure tira um instantâneo da VM existente antes de substituir o disco e a armazena no local de preparo que você especificar. Os discos existentes conectados à VM são substituídos pelo ponto de restauração selecionado.<br/><br/> O instantâneo é copiado para o cofre e mantido de acordo com a política de retenção. <br/><br/> Após a operação de substituição do disco, o disco original é mantido no grupo de recursos. Pode optar por eliminar manualmente os discos originais se não forem necessários. <br/><br/>A substituição existente tem suporte para VMs gerenciadas não criptografadas. Não é suportado para discos não geridos, [VMs generalizados,](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)ou para VMs [criados usando imagens personalizadas](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Se o ponto de restauração tiver mais ou menos discos que a VM atual, o número de discos no ponto de restauração refletirá apenas a configuração da VM.
**Entre regiões (região secundária)** | A restauração entre regiões pode ser usada para restaurar VMs do Azure na região secundária, que é uma [região emparelhada do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).<br><br> Você pode restaurar todas as VMs do Azure para o ponto de recuperação selecionado se o backup for feito na região secundária.<br><br> Esse recurso está disponível para as opções abaixo:<br> * [criar uma VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [restaurar discos](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Atualmente, não há suporte para a opção [substituir discos existentes](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) .<br><br> Permissões<br> A operação de restauração na região secundária pode ser executada por administradores de backup e administradores de aplicativos.

> [!NOTE]
> Você também pode recuperar arquivos e pastas específicas em uma VM do Azure. [Saiba mais](backup-azure-restore-files-from-vm.md).
>
> Se estiver a executar a [versão mais recente](backup-instant-restore-capability.md) do Azure Backup para VMs Azure (conhecido como Instant Restore), as fotos são mantidas por até sete dias, e pode restaurar um VM a partir de instantâneos antes que os dados de backup sejam enviados para o cofre. Se você quiser restaurar uma VM de um backup dos últimos sete dias, será mais rápido restaurar a partir do instantâneo e não do cofre.

## <a name="storage-accounts"></a>Contas de armazenamento

Alguns detalhes sobre contas de armazenamento:

- **Criar VM**: Quando criar um novo VM, o VM será colocado na conta de armazenamento que especificar.
- **Restaurar**o disco : Quando restaurar um disco, o disco é copiado para a conta de armazenamento que especifica. O trabalho de restauração gera um modelo que você pode baixar e usar para especificar configurações de VM personalizadas. Esse modelo é colocado na conta de armazenamento especificada.
- **Substitua o disco**: Quando substituir um disco num VM existente, o Azure Backup tira uma fotografia do VM existente antes de substituir o disco. O instantâneo é armazenado no local de preparo (conta de armazenamento) que você especificar. Essa conta de armazenamento é usada para armazenar temporariamente o instantâneo durante o processo de restauração e recomendamos que você crie uma nova conta para fazer isso, que pode ser facilmente removida posteriormente.
- **Localização**da conta de armazenamento : A conta de armazenamento deve estar na mesma região que o cofre. Somente essas contas são exibidas. Se não houver nenhuma conta de armazenamento no local, você precisará criar uma.
- **Tipo**de armazenamento : O armazenamento blob não é suportado.
- **Redundância**de armazenamento : O armazenamento redundante da zona (ZRS) não é suportado. As informações de replicação e redundância para a conta são mostradas entre parênteses após o nome da conta.
- **Armazenamento premium:**
  - Ao restaurar VMs não Premium, as contas de armazenamento Premium não têm suporte.
  - Ao restaurar VMs gerenciadas, não há suporte para contas de armazenamento Premium configuradas com regras de rede.

## <a name="before-you-start"></a>Antes de começar

Para restaurar um VM (crie um novo VM), certifique-se de que tem as [permissões corretas](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) de controlo de acesso baseados em funções (RBAC) para a operação Restore VM.

Se não tiver permissões, pode [restaurar um disco](#restore-disks), e depois de restaurado o disco, pode utilizar o [modelo](#use-templates-to-customize-a-restored-vm) que foi gerado como parte da operação de restauro para criar um novo VM.

## <a name="select-a-restore-point"></a>Selecionar um ponto de restauração

1. No cofre associado ao VM que pretende restaurar, clique **em itens de backup** > Máquina Virtual **Azure**.
2. Clique em uma VM. Por padrão, no painel da VM, os pontos de recuperação dos últimos 30 dias são exibidos. Você pode exibir pontos de recuperação com mais de 30 dias ou filtrar para localizar pontos de recuperação com base em datas, intervalos de tempo e tipos diferentes de consistência de instantâneo.
3. Para restaurar o VM, clique em **Restaurar vM**.

    ![Ponto de restauro](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Selecione um ponto de restauração a ser usado para a recuperação.

## <a name="choose-a-vm-restore-configuration"></a>Escolher uma configuração de restauração de VM

1. Na **configuração restaurar,** selecione uma opção de restauro:
    - **Criar nova**: Utilize esta opção se quiser criar um novo VM. Você pode criar uma VM com configurações simples ou restaurar um disco e criar uma VM personalizada.
    - **Substitua a existência**: Utilize esta opção se pretender substituir os discos num VM existente.

        ![Assistente de restauração de configuração](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Especifique as configurações para a opção de restauração selecionada.

## <a name="create-a-vm"></a>Criar uma VM

Como uma das opções de [restauro,](#restore-options)pode criar um VM rapidamente com configurações básicas a partir de um ponto de restauro.

1. Na **configuração restaurar** > **Criar um novo** > Restaurar **o tipo,** selecione **Criar uma máquina virtual**.
2. No nome da **máquina Virtual,** especifique um VM que não exista na subscrição.
3. No **grupo Recursos**, selecione um grupo de recursos existente para o novo VM, ou crie um novo com um nome globalmente único. Se você atribuir um nome que já existe, o Azure atribuirá ao grupo o mesmo nome da VM.
4. Na **rede Virtual,** selecione o VNet no qual o VM será colocado. Todos os VNets associados à assinatura são exibidos. Selecione a sub-rede. A primeira sub-rede é selecionada por padrão.
5. No **Local de Armazenamento,** especifique a conta de armazenamento para o VM. [Saiba mais](#storage-accounts).

    ![Assistente de restauração de configuração](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. Na **configuração restaurar,** selecione **OK**. Em **Restaurar,** clique em **Restaurar** para acionar a operação de restauro.

## <a name="restore-disks"></a>Restaurar discos

Como uma das opções de [restauro,](#restore-options)pode criar um disco a partir de um ponto de restauro. Em seguida, com o disco, você pode executar um dos seguintes procedimentos:

- Use o modelo gerado durante a operação de restauração para personalizar as configurações e disparar a implantação da VM. Você edita as configurações de modelo padrão e envia o modelo para implantação de VM.
- [Fixe os discos restaurados](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) a um VM existente.
- [Crie um novo VM](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) a partir dos discos restaurados utilizando o PowerShell.

1. Na **configuração restaurar** > **Criar novos** > Restaurar o **tipo,** selecione discos de **restauração**.
2. No **grupo Recursos,** selecione um grupo de recursos existente para os discos restaurados, ou crie um novo com um nome globalmente único.
3. Na **conta de Armazenamento,** especifique a conta para a qual copiar os VHDs. [Saiba mais](#storage-accounts).

    ![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. Na **configuração restaurar,** selecione **OK**. Em **Restaurar,** clique em **Restaurar** para acionar a operação de restauro.

Quando a sua máquina virtual utiliza discos geridos e seleciona a opção Criar a **máquina virtual,** o Azure Backup não utiliza a conta de armazenamento especificada. No caso dos discos restaurar e **restaurar** **instantâneos,** a conta de armazenamento é utilizada apenas para armazenar o modelo. Os discos gerenciados são criados no grupo de recursos especificado.
Quando sua máquina virtual usa discos não gerenciados, eles são restaurados como BLOBs para a conta de armazenamento.

### <a name="use-templates-to-customize-a-restored-vm"></a>Usar modelos para personalizar uma VM restaurada

Depois que o disco for restaurado, use o modelo que foi gerado como parte da operação de restauração para personalizar e criar uma nova VM:

1. Detalhes de trabalho de **restauro** aberto para o trabalho relevante.

2. Em **Restaurar detalhes de trabalho,** selecione modelo de **implementação** para iniciar a implementação do modelo.

    ![Detalhar o trabalho de restauração](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Para personalizar a definição de VM fornecida no modelo, clique no **modelo editar**. Se quiser adicionar mais personalizações, clique em **parâmetros editar**.
    - [Saiba mais](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) sobre a implementação de recursos a partir de um modelo personalizado.
    - [Saiba mais](../azure-resource-manager/templates/template-syntax.md) sobre modelos de autoria.

   ![Carregar implantação de modelo](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Introduza os valores personalizados para o VM, aceite os **Termos e Condições** e clique em **Comprar**.

   ![Enviar implantação de modelo](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Substituir discos existentes

Como uma das opções de [restauro,](#restore-options)pode substituir um disco VM existente pelo ponto de restauro selecionado. [Reveja](#restore-options) todas as opções de restauro.

1. Na **configuração restaurar,** clique **em substituir o existente**.
2. No **Tipo restaurar,** selecione **Substitua o disco/s**. Esse é o ponto de restauração que será usado para substituir os discos de VM existentes.
3. No **Local de Preparação,** especifique onde as imagens dos discos geridos atuais devem ser guardadas durante o processo de restauro. [Saiba mais](#storage-accounts).

   ![Assistente para restaurar configuração substituir existente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Restauração entre regiões

Como uma das [Opções de restauração](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options), a CRR (restauração entre regiões) permite que você restaure as VMs do Azure em uma região secundária, que é uma região emparelhada do Azure.

Para carregar o recurso durante a versão prévia, leia a [seção antes de começar](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Para ver se a CRR está habilitada, siga as instruções em [Configurar a restauração entre regiões](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Exibir itens de backup na região secundária

Se a CRR estiver habilitada, você poderá exibir os itens de backup na região secundária.

1. No portal, vá para **cofre dos serviços de recuperação** > **itens de backup**
2. Clique em **região secundária** para exibir os itens na região secundária.

![Máquinas virtuais na região secundária](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Selecionar região secundária](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Restaurar na região secundária

A experiência do usuário de restauração da região secundária será semelhante à experiência do usuário de restauração da região primária. Ao configurar detalhes na folha restaurar configuração para configurar a restauração, você será solicitado a fornecer apenas os parâmetros secundários da região.

![Escolher a VM a ser restaurada](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Selecione ponto de restauro](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Restaurar a configuração](./media/backup-azure-arm-restore-vms/rest-config.png)

![Notificação de disparo de restauração em andamento](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Para restaurar e criar uma VM, consulte [criar uma VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm).
- Para restaurar como um disco, consulte [restaurar discos](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks).

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorando trabalhos de restauração de região secundária

1. No portal, vá para **cofre dos serviços de recuperação** > **trabalhos de backup**
2. Clique em **região secundária** para exibir os itens na região secundária.

![Trabalhos de backup filtrados](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>Restaurar VMs com configurações especiais

Há vários cenários comuns em que talvez seja necessário restaurar as VMs.

**Cenário** | **Orientação**
--- | ---
**Restaurar VMs usando benefício de uso híbrido** | Se um VM windows utilizar [o licenciamento hybrid Use Benefit (HUB),](../virtual-machines/windows/hybrid-use-benefit-licensing.md)restaure os discos e crie um novo VM utilizando o modelo fornecido (com o Tipo de **Licença** definido para **Windows_Server),** ou PowerShell.  Essa configuração também pode ser aplicada após a criação da VM.
**Restaurar VMs durante um desastre do datacenter azure** | Se o cofre usar GRS e o datacenter primário para a VM falhar, o backup do Azure dará suporte à restauração de VMs de backup para o datacenter emparelhado. Você seleciona uma conta de armazenamento no datacenter emparelhado e restaura como normal. O backup do Azure usa o serviço de computação na região emparelhada para criar a VM restaurada. [Saiba mais](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) sobre a resiliência do datacenter.
**Restaurar o controlador de domínio único VM em domínio único** | Restaure a VM como qualquer outra VM. Tenha em atenção que:<br/><br/> De uma perspectiva Active Directory, a VM do Azure é como qualquer outra VM.<br/><br/> O Modo de Restauração dos Serviços de Diretório (DSRM) também está disponível, de modo que todos os Active Directory cenários de recuperação são viáveis. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) sobre backup e restaurar considerações para controladores de domínio virtualizados.
**Restaurar vários VMs controladores de domínio em domínio único** | Se outros controladores de domínio no mesmo domínio puderem ser acessados pela rede, o controlador de domínio poderá ser restaurado como qualquer VM. Se for o último controlador de domínio restante no domínio, ou se for realizada uma recuperação numa rede isolada, utilize uma [recuperação florestal](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Restaurar vários domínios numa floresta** | Recomendamos uma [recuperação florestal.](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)
**Restauro de metal nu** | A principal diferença entre as VMs do Azure e os hipervisores locais é que não há nenhum console de VM disponível no Azure. Um console do é necessário para determinados cenários, como a recuperação usando um backup do tipo BMR (recuperação bare-metal). No entanto, a restauração da VM do cofre é uma substituição completa para BMR.
**Restaurar VMs com configurações especiais de rede** | As configurações de rede especiais incluem VMs que usam balanceamento de carga interno ou externo, usando várias NICS ou vários endereços IP reservados. Restaure estes VMs utilizando a [opção restaurar](#restore-disks)o disco . Esta opção faz uma cópia dos VHDs na conta de armazenamento especificada, e pode então criar um VM com um equilibrador de carga [interna](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) ou [externa,](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/) [múltiplos NICS,](../virtual-machines/windows/multiple-nics.md)ou [múltiplos endereços IP reservados,](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)de acordo com a sua configuração.
**Grupo de Segurança de Rede (NSG) em NIC/Subnet** | O backup de VM do Azure dá suporte a backup e restauração de informações de NSG em nível de rede virtual, sub-rede e NIC.
**VMs da zona** | O backup do Azure dá suporte ao backup e à restauração de VMs fixadas de zona. [Saiba mais](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>Acompanhar a operação de restauração

Depois de disparar a operação de restauração, o serviço de backup cria um trabalho para acompanhamento. O backup do Azure exibe notificações sobre o trabalho no Portal. Se não estiverem visíveis, selecione o símbolo **notificações** e, em seguida, selecione **Ver todos os Trabalhos** para ver o Estado do Processo de Restauro.

![Restauração disparada](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Acompanhe a restauração da seguinte maneira:

1. Para exibir as operações do trabalho, clique no hiperlink notificações. Em alternativa, no cofre, clique em **backups**e, em seguida, clique no VM relevante.

    ![Lista de VMs em um cofre](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Para monitorizar o progresso, clique em qualquer trabalho de restauro com um estado de **in-progresso**. Isso exibe a barra de progresso, que exibe informações sobre o progresso da restauração:

    - **Tempo estimado de restauro**: Inicialmente fornece o tempo necessário para completar a operação de restauro. À medida que a operação progride, o tempo gasto reduz e atinge zero quando a operação de restauração é concluída.
    - **Percentagem de restauro.** Mostra o percentual de operação de restauração que é feito.
    - **Número de bytes transferidos**: Se estiver a restaurar através da criação de um novo VM, mostra os bytes que foram transferidos contra o número total de bytes a transferir.

## <a name="post-restore-steps"></a>Etapas de pós-restauração

Há várias coisas a serem observadas após a restauração de uma VM:

- As extensões presentes durante a configuração de backup são instaladas, mas não habilitadas. Se você vir um problema, reinstale as extensões.
- Se a VM com backup tiver um endereço IP estático, a VM restaurada terá um endereço IP dinâmico para evitar conflitos. Pode [adicionar um endereço IP estático ao VM restaurado](/previous-versions/azurevirtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
- Uma VM restaurada não tem um conjunto de disponibilidade. Se utilizar a opção restaurar o disco, pode [especificar um conjunto](../virtual-machines/windows/tutorial-availability-sets.md) de disponibilidade quando criar um VM a partir do disco utilizando o modelo fornecido ou powerShell.
- Se você usar uma distribuição Linux baseada em inicialização de nuvem, como Ubuntu, por motivos de segurança, a senha será bloqueada após a restauração. Utilize a extensão VMAccess no VM restaurado para [redefinir a palavra-passe](../virtual-machines/linux/reset-password.md). É recomendável usar chaves SSH nessas distribuições para que você não precise redefinir a senha após a restauração.
- Se não conseguir aceder ao VM uma vez restaurado devido à relação com o controlador de domínio, siga os passos abaixo para elevar o VM:
  - Anexe o disco do sistema operacional como um disco de dados a uma VM recuperada.
  - Instale manualmente o agente VM se se constatar que o Agente Azure não responde seguindo este [link](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
  - Ativar o acesso da Consola em Série em VM para permitir o acesso da linha de comando ao VM

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - Quando a VM é recriada, use portal do Azure para redefinir a conta de administrador local e a senha
  - Use Console serial Access e CMD para desassociar a VM do domínio

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Depois que a VM for desassociada e reiniciada, você poderá usar o RDP com êxito para a VM com credenciais de administrador local e reingressar a VM de volta ao domínio com êxito.

## <a name="backing-up-restored-vms"></a>Fazendo backup de VMs restauradas

- Se você restaurou uma VM para o mesmo grupo de recursos com o mesmo nome da VM com backup originalmente, o backup continua na VM após a restauração.
- Se você restaurou a VM para um grupo de recursos diferente ou especificou um nome diferente para a VM restaurada, precisará configurar o backup para a VM restaurada.

## <a name="next-steps"></a>Passos seguintes

- Se tiver dificuldades durante o processo de restauro, [reveja](backup-azure-vms-troubleshoot.md#restore) questões e erros comuns.
- Depois do VM ser restaurado, aprenda sobre a gestão de [máquinas virtuais](backup-azure-manage-vms.md)
