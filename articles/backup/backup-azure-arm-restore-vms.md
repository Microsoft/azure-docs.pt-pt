---
title: Restaurar VMs utilizando o portal Azure
description: Restaurar uma máquina virtual Azure a partir de um ponto de recuperação usando o portal Azure
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 00b0f7313ba77037d90dcdb8ed04e5f61e335c55
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86023334"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Como restaurar os dados do Azure VM no portal Azure

Este artigo descreve como restaurar os dados do Azure VM a partir dos pontos de recuperação armazenados nos cofres dos Serviços de Recuperação [de Backup Azure.](backup-overview.md)

## <a name="restore-options"></a>Opções de restauro

A Azure Backup fornece uma série de formas de restaurar um VM.

**Opção de restauro** | **Detalhes**
--- | ---
**Crie uma nova VM** | Rapidamente cria e obtém um VM básico a funcionar a partir de um ponto de restauro.<br/><br/> Pode especificar um nome para o VM, selecionar o grupo de recursos e a rede virtual (VNet) no qual será colocado e especificar uma conta de armazenamento para o VM restaurado. O novo VM deve ser criado na mesma região que a VM de origem.
**Restaurar disco** | Restaura um disco VM, que pode ser usado para criar um novo VM.<br/><br/> O Azure Backup fornece um modelo para ajudá-lo a personalizar e criar um VM. <br/><br> O trabalho de restauro gera um modelo que pode descarregar e usar para especificar as definições de VM personalizadas e criar um VM.<br/><br/> Os discos são copiados para o Grupo de Recursos que especifica.<br/><br/> Em alternativa, pode ligar o disco a um VM existente ou criar um novo VM utilizando o PowerShell.<br/><br/> Esta opção é útil se pretender personalizar o VM, adicionar definições de configuração que não estavam lá no momento da cópia de segurança, ou adicionar definições que devem ser configuradas usando o modelo ou PowerShell.
**Substituir os existentes** | Pode restaurar um disco e usá-lo para substituir um disco no VM existente.<br/><br/> O VM atual deve existir. Se for apagado, esta opção não pode ser usada.<br/><br/> O Azure Backup tira uma fotografia do VM existente antes de substituir o disco e armazena-o no local de preparação que especifica. Os discos existentes ligados ao VM são substituídos pelo ponto de restauro selecionado.<br/><br/> O instantâneo é copiado para o cofre, e mantido de acordo com a política de retenção. <br/><br/> Após a operação do disco de substituição, o disco original é mantido no grupo de recursos. Pode optar por eliminar manualmente os discos originais se não forem necessários. <br/><br/>A substituição existente é suportada por VMs geridos não encriptados, incluindo VMs [criados com imagens personalizadas](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). Não é suportado para VMs clássicos.<br/><br/> Se o ponto de restauro tiver mais ou menos discos do que o VM atual, então o número de discos no ponto de restauração só refletirá a configuração VM.<br><br> Substituir os VM existentes por recursos ligados (como [identidade gerida atribuída pelo utilizador](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ou Key [Vault)](https://docs.microsoft.com/azure/key-vault/key-vault-overview)porque a aplicação de clientes de backup não tem permissões sobre estes recursos enquanto executa a restauração.
**Região Transversal (região secundária)** | A restauração da Região Transversal pode ser usada para restaurar os VMs Azure na região secundária, que é uma [região emparelhada Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)<br><br> Pode restaurar todos os VMs Azure para o ponto de recuperação selecionado se a cópia de segurança for feita na região secundária.<br><br> Esta funcionalidade está disponível para as opções abaixo:<br> <li> [Criar uma VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> <li> [Restaurar Discos](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Atualmente, não apoiamos a opção [Substituir discos existentes.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)<br><br> Permissões<br> A operação de restauro na região secundária pode ser realizada por administradores de backup e administradores de aplicações.

> [!NOTE]
> Também pode recuperar ficheiros e pastas específicos num VM Azure. [Saiba mais](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Contas de armazenamento

Alguns detalhes sobre contas de armazenamento:

- **Criar VM**: Quando criar um novo VM, o VM será colocado na conta de armazenamento que especifica.
- **Restaurar o disco**: Quando restaurar um disco, o disco é copiado para a conta de armazenamento que especifica. O trabalho de restauro gera um modelo que pode descarregar e usar para especificar as definições de VM personalizadas. Este modelo é colocado na conta de armazenamento especificada.
- **Substitua o disco**: Quando substitui um disco num VM existente, o Azure Backup tira uma imagem do VM existente antes de substituir o disco. O instantâneo é armazenado no local de preparação (conta de armazenamento) que especifica. Esta conta de armazenamento é usada para armazenar temporariamente o instantâneo durante o processo de restauro, e recomendamos que crie uma nova conta para o fazer, que pode ser facilmente removida depois.
- **Localização da conta de**armazenamento : A conta de armazenamento deve estar na mesma região que o cofre. Apenas estas contas são apresentadas. Se não houver contas de armazenamento no local, precisa de criar uma.
- **Tipo de armazenamento**: O armazenamento de bolhas não é suportado.
- **Redundância de**armazenamento : O armazenamento redundante da zona (ZRS) não é suportado. A informação de replicação e redundância da conta é mostrada em parênteses após o nome da conta.
- **Armazenamento premium:**
  - Ao restaurar VMs não premium, as contas de armazenamento premium não são suportadas.
  - Ao restaurar os VM geridos, as contas de armazenamento premium configuradas com as regras de rede não são suportadas.

## <a name="before-you-start"></a>Antes de começar

Para restaurar um VM (criar um novo VM), certifique-se de que tem as [permissões corretas](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) de controlo de acesso baseado em funções (RBAC) para a operação Restore VM.

Se não tiver permissões, pode [restaurar um disco](#restore-disks), e depois de o disco ser restaurado, pode utilizar o [modelo](#use-templates-to-customize-a-restored-vm) que foi gerado como parte da operação de restauro para criar um novo VM.

## <a name="select-a-restore-point"></a>Selecione um ponto de restauro

1. No cofre associado ao VM que pretende restaurar, clique em **itens de cópia de segurança**  >  **Azure Virtual Machine**.
2. Clique num VM. Por predefinição no painel VM, são apresentados pontos de recuperação dos últimos 30 dias. Pode apresentar pontos de recuperação com mais de 30 dias ou filtrar para encontrar pontos de recuperação baseados em datas, intervalos de tempo e diferentes tipos de consistência instantânea.
3. Para restaurar o VM, clique em **Restaurar VM**.

    ![Ponto de restauro](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Selecione um ponto de restauro a utilizar para a recuperação.

## <a name="choose-a-vm-restore-configuration"></a>Escolha uma configuração de restauro VM

1. Na **configuração Restaurar,** selecione uma opção de restauro:
    - **Criar nova**: Utilize esta opção se quiser criar um novo VM. Pode criar um VM com configurações simples, ou restaurar um disco e criar um VM personalizado.
    - **Substitua a existência:** Utilize esta opção se pretender substituir discos num VM existente.

        ![Restaurar o assistente de configuração](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Especifique as definições para a opção de restauro selecionada.

## <a name="create-a-vm"></a>Criar uma VM

Como uma das opções de [restauro,](#restore-options)pode criar um VM rapidamente com definições básicas a partir de um ponto de restauro.

1. Na **configuração Restaurar**  >  **Crie novo**Tipo de  >  **Restauro,** selecione Criar uma máquina **virtual**.
2. No **nome da máquina Virtual,** especifique um VM que não exista na subscrição.
3. No **grupo Resource**, selecione um grupo de recursos existente para o novo VM, ou crie um novo com um nome globalmente único. Se atribuir um nome que já existe, o Azure atribui ao grupo o mesmo nome que o VM.
4. Na **rede Virtual,** selecione o VNet no qual o VM será colocado. Todos os VNets associados à subscrição são apresentados. Selecione a sub-rede. A primeira sub-rede é selecionada por predefinição.
5. No **Local de Armazenamento,** especifique a conta de armazenamento do VM. [Saiba mais](#storage-accounts).

    ![Restaurar o assistente de configuração](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. Na **configuração 'Restaurar'** selecione **OK**. In **Restore,** clique **em Restaurar** para ativar a operação de restauro.

## <a name="restore-disks"></a>Restaurar discos

Como uma das opções de [restauro,](#restore-options)pode criar um disco a partir de um ponto de restauração. Em seguida, com o disco, pode fazer um dos seguintes:

- Utilize o modelo gerado durante a operação de restauro para personalizar as definições e desencadear a implementação de VM. Edita as definições do modelo predefinido e submete o modelo para a implementação de VM.
- [Fixe os discos restaurados](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) a um VM existente.
- [Crie um novo VM](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) a partir dos discos restaurados utilizando o PowerShell.

1. Na **configuração Restaurar**  >  **Crie novo**Tipo de  >  **Restauro,** selecione Discos De **restauro**.
2. No **grupo De recursos,** selecione um grupo de recursos existente para os discos restaurados, ou crie um novo com um nome globalmente único.
3. Na **conta de Armazenamento,** especifique a conta para copiar os VHDs. [Saiba mais](#storage-accounts).

    ![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. Na **configuração 'Restaurar'** selecione **OK**. In **Restore,** clique **em Restaurar** para ativar a operação de restauro.

Quando a sua máquina virtual utiliza discos geridos e seleciona a opção **'Criar máquina virtual',** o Azure Backup não utiliza a conta de armazenamento especificada. No caso dos **discos Restore** e **Instant Restore,** a conta de armazenamento é utilizada apenas para armazenar o modelo. Os discos geridos são criados no grupo de recursos especificado.
Quando a sua máquina virtual utiliza discos não geridos, são restaurados como bolhas na conta de armazenamento.

### <a name="use-templates-to-customize-a-restored-vm"></a>Use modelos para personalizar um VM restaurado

Depois de restaurar o disco, utilize o modelo que foi gerado como parte da operação de restauro para personalizar e criar um novo VM:

1. Abra **detalhes de trabalho de restauração** para o trabalho relevante.

2. In **Restore Job Details**, selecione **'Implementar' para** iniciar a implementação do modelo.

    ![Restaurar a perfuração de trabalho](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Para personalizar a definição de VM fornecida no modelo, clique no **modelo de Edição**. Se quiser adicionar mais personalizações, clique em **Editar os parâmetros**.
    - [Saiba mais](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) sobre a implementação de recursos a partir de um modelo personalizado.
    - [Saiba mais](../azure-resource-manager/templates/template-syntax.md) sobre modelos de autoria.

   ![Implementação do modelo de carga](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Introduza os valores personalizados para o VM, aceite os **Termos e Condições** e clique em **Comprar.**

   ![Submeter a implementação do modelo](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Substituir discos existentes

Como uma das opções de [restauro,](#restore-options)pode substituir um disco VM existente pelo ponto de restauro selecionado. [Reveja](#restore-options) todas as opções de restauro.

1. Na **configuração 'Restaurar'** clique em **Substituir a existência.**
2. No **Tipo De Restauro,** selecione **Substitua o disco/s**. Este é o ponto de restauro que será utilizado substituir os discos VM existentes.
3. Em **Localização de Preparação**, especifique onde devem ser guardadas imagens dos discos geridos atuais durante o processo de restauração. [Saiba mais](#storage-accounts).

   ![Restaurar o assistente de configuração Substituir o existente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Restauro da Região Transversal

Como uma das opções de [restauro,](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)o Cross Region Restore (CRR) permite restaurar os VMs Azure numa região secundária, que é uma região emparelhada Azure.

Para embarcar na funcionalidade durante a pré-visualização, leia a [secção Antes de Começar](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Para ver se a CRR está ativada, siga as instruções no [Configure Cross Region Restore](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Ver itens de backup na região secundária

Se o CRR estiver ativado, pode ver os itens de reserva na região secundária.

1. A partir do portal, vá para **os serviços de recuperação de**  >  **itens de backup**
2. Clique **na Região Secundária** para ver os itens na região secundária.

![Máquinas virtuais na região secundária](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Selecione Região Secundária](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Restauro na região secundária

A região secundária restaurará a experiência do utilizador será semelhante à região primária restaurar a experiência do utilizador. Ao configurar detalhes na lâmina de configuração de restauro para configurar a sua restauração, será solicitado que forneça apenas parâmetros da região secundária.

![Escolha VM para restaurar](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Selecione ponto de restauro](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Restaurar a configuração](./media/backup-azure-arm-restore-vms/rest-config.png)

>[!NOTE]
>A rede virtual na região secundária precisa de ser atribuída de forma única e não pode ser utilizada para quaisquer outros VMs nesse grupo de recursos.

![Trigger restaurar na notificação de progresso](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Para restaurar e criar um VM, consulte a [Criação de um VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm).
- Para restaurar como disco, consulte os [discos Restaurar](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks).

>[!NOTE]
>Após o restauro ser desencadeado e na fase de transferência de dados, a função de restauro não pode ser cancelada.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorização da região secundária restabelece postos de trabalho

1. A partir do portal, vá para o **cofre dos Serviços de Recuperação**  >  **trabalhos de backup**
2. Clique **na Região Secundária** para ver os itens na região secundária.

![Trabalhos de reserva filtrados](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>Restaurar VMs com configurações especiais

Há uma série de cenários comuns em que poderá ser necessário restaurar os VMs.

**Cenário** | **Orientação**
--- | ---
**Restaurar VMs usando benefício de uso híbrido** | Se um Windows VM utilizar [o licenciamento Hybrid Use Benefit (HUB),](../virtual-machines/windows/hybrid-use-benefit-licensing.md)restaure os discos e crie um novo VM utilizando o modelo fornecido (com o Tipo de **Licença** definido para **Windows_Server),** ou PowerShell.  Esta definição também pode ser aplicada após a criação do VM.
**Restaurar VMs durante um desastre do datacenter Azure** | Se o cofre utilizar GRS e o centro de dados primário para o VM diminuir, o Azure Backup suporta restaurar os VMs apoiados no centro de dados emparelhado. Selecione uma conta de armazenamento no datacenter emparelhado e restaure normalmente. A Azure Backup utiliza o serviço de computação na região emparelhada para criar o VM restaurado. [Saiba mais](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) sobre a resiliência do datacenter.<br><br> Se o cofre utilizar GRS, pode escolher a nova funcionalidade, [Cross Region Restore](#cross-region-restore). Isto permite-lhe restaurar uma segunda região em cenários de paragem total ou parcial, ou mesmo que não haja nenhuma paragem.
**Restaurar o único controlador de domínio VM em domínio único** | Restaurar o VM como qualquer outro VM. Tenha em atenção que:<br/><br/> Do ponto de vista do Ative Directory, o Azure VM é como qualquer outro VM.<br/><br/> O Modo de Restauro de Serviços de Diretório (DSRM) também está disponível, pelo que todos os cenários de recuperação do Diretório Ativo são viáveis. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) sobre backup e restaure considerações para controladores de domínio virtualizados.
**Restaurar vários VMs controladores de domínio em domínio único** | Se outros controladores de domínio no mesmo domínio puderem ser alcançados ao longo da rede, o controlador de domínio pode ser restaurado como qualquer VM. Se for o último controlador de domínio restante no domínio, ou se for realizada uma recuperação numa rede isolada, utilize uma [recuperação florestal](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Restaurar vários domínios numa floresta** | Recomendamos uma [recuperação florestal.](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)
**Restauro de metal nu** | A grande diferença entre VMs Azure e hipervisores no local é que não há consola VM disponível em Azure. É necessária uma consola para determinados cenários, como a recuperação utilizando uma cópia de segurança do tipo BMR de recuperação de metais nus (BMR). No entanto, a restauração de VM do cofre é um substituto completo para bMR.
**Restaurar VMs com configurações especiais de rede** | As configurações especiais de rede incluem VMs usando o equilíbrio de carga interna ou externa, usando vários NICS, ou vários endereços IP reservados. Restaura estes VM utilizando a [opção de disco de restauro.](#restore-disks) Esta opção faz uma cópia dos VHDs na conta de armazenamento especificada, podendo então criar um VM com um balançador de carga [interno](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) ou [externo,](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell) [múltiplos NICS,](../virtual-machines/windows/multiple-nics.md)ou [vários endereços IP reservados,](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)de acordo com a sua configuração.
**Grupo de Segurança de Rede (NSG) em NIC/Subnet** | A cópia de segurança Azure VM suporta backup e restaurar informações NSG no nível Vnet, subnet e NIC.
**VMs de fixação de zona** | Se você apoiar um Azure VM que está preso a uma zona (com Azure Backup), então você pode restaurá-lo na mesma zona onde foi fixado. [Saiba mais](https://docs.microsoft.com/azure/availability-zones/az-overview)
**Restaurar VM em qualquer conjunto de disponibilidade** | Ao restaurar um VM do portal, não há opção para escolher um conjunto de disponibilidade. Um VM restaurado não tem um conjunto de disponibilidade. Se utilizar a opção de disco de restauro, poderá [especificar um conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) quando criar um VM a partir do disco utilizando o modelo ou PowerShell fornecido.
**Restaurar VMs especiais como SQL VMs** | Se você fizer o backup de um SQL VM usando a cópia de segurança Azure VM e, em seguida, utilizar a opção de restauro VM ou criar um VM após restaurar os discos, então o VM recém-criado deve ser registado com o fornecedor SQL como mencionado [aqui.](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-vm-resource-provider-register?tabs=azure-cli%2Cbash) Isto converterá o VM restaurado num SQL VM.

## <a name="track-the-restore-operation"></a>Acompanhe a operação de restauro

Depois de ativar a operação de restauro, o serviço de backup cria um trabalho para rastrear. O Azure Backup apresenta notificações sobre o trabalho no portal. Se não forem visíveis, selecione o símbolo **de Notificações** e, em seguida, selecione **Ver todos os Jobs** para ver o Estado do Processo de Restauro.

![Restauro desencadeado](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Restauração da faixa da seguinte forma:

1. Para visualizar as operações para o trabalho, clique na hiperligação de notificações. Em alternativa, no cofre, clique em **Backup jobs**e, em seguida, clique no VM relevante.

    ![Lista de VMs em um cofre](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Para monitorizar o progresso, clique em qualquer trabalho de restauro com um estado **de progresso**. Isto mostra a barra de progresso, que exibe informações sobre o progresso do restauro:

    - **Tempo estimado de restauro**: Inicialmente fornece o tempo necessário para concluir a operação de restauro. À medida que a operação progride, o tempo desatar reduz-se e atinge zero quando a operação de restauro terminar.
    - **Percentagem de restauro**. Mostra a percentagem de operação de restauro que está feita.
    - **Número de bytes transferidos**: Se estiver a restaurar criando um novo VM, mostra os bytes que foram transferidos contra o número total de bytes a serem transferidos.

## <a name="post-restore-steps"></a>Passos pós-restauro

Há uma série de coisas a notar depois de restaurar um VM:

- As extensões presentes durante a configuração de backup são instaladas, mas não ativadas. Se vir algum problema, reinstale as extensões.
- Se o VM apoiado tiver um endereço IP estático, o VM restaurado terá um endereço IP dinâmico para evitar conflitos. Pode [adicionar um endereço IP estático ao VM restaurado](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description).
- Um VM restaurado não tem um conjunto de disponibilidade. Se utilizar a opção de disco de restauro, poderá [especificar um conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) quando criar um VM a partir do disco utilizando o modelo ou PowerShell fornecido.
- Se utilizar uma distribuição Linux baseada em nuvem, como ubuntu, por razões de segurança, a palavra-passe é bloqueada após a restauração. Utilize a extensão VMAccess no VM restaurado para [redefinir a palavra-passe](../virtual-machines/linux/reset-password.md). Recomendamos a utilização de teclas SSH nestas distribuições, para que não seja necessário redefinir a palavra-passe após a restauração.
- Se não conseguir aceder a um VM uma vez restaurado devido ao VM ter uma relação quebrada com o controlador de domínio, siga os passos abaixo para elevar o VM:
  - Anexe o disco de oss como um disco de dados a um VM recuperado.
  - Instale manualmente o agente VM se o Agente Azure não responder seguindo este [link](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
  - Ativar o acesso à Consola em Série em VM para permitir o acesso da linha de comando ao VM

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - Quando o VM é reconstruído use o portal Azure para redefinir a conta de administrador local e a palavra-passe
  - Use o acesso à consola serial e o CMD para desafetar vM do domínio

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Uma vez que o VM seja desatado e reiniciado, poderá conseguir pDR para o VM com credenciais de administração local e voltar a juntar-se ao domínio com sucesso.

## <a name="backing-up-restored-vms"></a>Backup VMs restaurados

- Se restaurar um VM para o mesmo grupo de recursos com o mesmo nome que o VM originalmente apoiado, a cópia de segurança continua no VM após a restauração.
- Se restaurou o VM a um grupo de recursos diferente ou especificou um nome diferente para o VM restaurado, precisa de configurar a cópia de segurança para o VM restaurado.

## <a name="next-steps"></a>Próximos passos

- Se sentir dificuldades durante o processo de restauro, [reveja](backup-azure-vms-troubleshoot.md#restore) questões e erros comuns.
- Depois que o VM é restaurado, aprenda sobre [a gestão de máquinas virtuais](backup-azure-manage-vms.md)
