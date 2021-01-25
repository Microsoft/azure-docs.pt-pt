---
title: Restaurar VMs utilizando o portal Azure
description: Restaurar uma máquina virtual Azure a partir de um ponto de recuperação utilizando o portal Azure, incluindo a função Cross Region Restore.
ms.reviewer: geg
ms.topic: conceptual
ms.date: 08/02/2020
ms.openlocfilehash: a82e8031f118f48f7c19cfc283c1be13d5d6f89d
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757598"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Como restaurar os dados do Azure VM no portal Azure

Este artigo descreve como restaurar os dados do Azure VM a partir dos pontos de recuperação armazenados nos cofres dos Serviços de Recuperação [de Backup Azure.](backup-overview.md)

## <a name="restore-options"></a>Opções de restauro

O Azure Backup fornece várias maneiras de restaurar uma VM.

**Opção de restauro** | **Detalhes**
--- | ---
**Criar uma nova VM** | Cria e coloca uma VM em execução rapidamente a partir de um ponto de restauro.<br/><br/> Pode especificar um nome para o VM, selecionar o grupo de recursos e a rede virtual (VNet) no qual será colocado e especificar uma conta de armazenamento para o VM restaurado. A nova VM tem de ser criada na mesma região que a VM de origem.<br><br>Se um restauro de VM falhar porque um Azure VM SKU não estava disponível na região especificada de Azure, ou devido a quaisquer outros problemas, o Azure Backup ainda restaura os discos no grupo de recursos especificado.
**Restaurar disco** | Restaura um disco da VM, que pode ser utilizado para criar uma nova VM.<br/><br/> O Azure Backup fornece um modelo para ajudar a personalizar e criar uma VM. <br/><br> O trabalho de restauro gera um modelo que pode descarregar e usar para especificar as definições de VM personalizadas e criar um VM.<br/><br/> Os discos são copiados para o Grupo de Recursos que especificar.<br/><br/> Em alternativa, pode ligar o disco a um VM existente ou criar um novo VM utilizando o PowerShell.<br/><br/> Esta opção é útil se quiser personalizar a VM, adicionar definições de configuração que esta não tinha quando foi criada a cópia de segurança ou adicionar definições que têm de ser configuradas através do modelo ou do PowerShell.
**Substituir existente** | Pode restaurar um disco e usá-lo para substituir um disco no VM existente.<br/><br/> A VM atual tem de existir. Se tiver sido apagada, esta opção não pode ser utilizada.<br/><br/> O Azure Backup tira uma fotografia do VM existente antes de substituir o disco e armazena-o no local de preparação que especifica. Os discos existentes e ligados à VM são substituídos pelo ponto de restauro selecionado.<br/><br/> O instantâneo é copiado para o cofre, e mantido de acordo com a política de retenção. <br/><br/> Após a operação do disco de substituição, o disco original é mantido no grupo de recursos. Pode optar por eliminar manualmente os discos originais se não forem necessários. <br/><br/>A substituição existente é suportada por VMs geridos não encriptados, incluindo VMs [criados com imagens personalizadas](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). Não é suportado para VMs clássicos.<br/><br/> Se o ponto de restauro tiver mais ou menos discos do que o VM atual, então o número de discos no ponto de restauração só refletirá a configuração VM.<br><br> A substituição dos atuais também é suportada por VMs por recursos ligados, como identidade gerida atribuída pelo utilizador ou [Cofre-Chave](../active-directory/managed-identities-azure-resources/overview.md) . [](../key-vault/general/overview.md)
**Entre regiões (região secundária)** | A restauração da Região Transversal pode ser usada para restaurar os VMs Azure na região secundária, que é uma [região emparelhada Azure.](../best-practices-availability-paired-regions.md#what-are-paired-regions)<br><br> Pode restaurar todos os VMs Azure para o ponto de recuperação selecionado se a cópia de segurança for feita na região secundária.<br><br> Durante o backup, os instantâneos não são replicados na região secundária. Apenas os dados armazenados no cofre são replicados. Assim, os restauros da região secundária são apenas restauros [de nível de abóbada.](about-azure-vm-restore.md#concepts) O tempo de restauro para a região secundária será quase o mesmo que o tempo de restauração do nível do cofre para a região primária.  <br><br> Esta funcionalidade está disponível para as seguintes opções:<br> <li> [Criar uma VM](#create-a-vm) <br> <li> [Restaurar discos](#restore-disks) <br><br> Atualmente, não apoiamos a opção [Substituir discos existentes.](#replace-existing-disks)<br><br> Permissões<br> A operação de restauro na região secundária pode ser realizada por administradores de backup e administradores de aplicações.

> [!NOTE]
> Também pode recuperar ficheiros e pastas específicos num VM Azure. [Saiba mais](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Contas de armazenamento

Alguns detalhes sobre contas de armazenamento:

- **Criar VM**: Quando criar um novo VM, o VM será colocado na conta de armazenamento que especifica.
- **Restaurar o disco**: Quando restaurar um disco, o disco é copiado para a conta de armazenamento que especifica. O trabalho de restauro gera um modelo que pode descarregar e usar para especificar as definições de VM personalizadas. Este modelo é colocado na conta de armazenamento especificada.
- **Substitua o disco**: Quando substitui um disco num VM existente, o Azure Backup tira uma imagem do VM existente antes de substituir o disco. O instantâneo também é copiado para o cofre dos Serviços de Recuperação através da transferência de dados, como um processo de fundo. No entanto, uma vez concluída a fase de instantâneo, a operação de substituição dos discos é acionada. Após a operação do disco de substituição, os discos da fonte Azure VM são deixados no grupo de Recursos especificado para o seu funcionamento e os VHDs são armazenados na conta de armazenamento especificada. Pode optar por eliminar ou reter estes VHDs e discos.
- **Localização da conta de** armazenamento : A conta de armazenamento deve estar na mesma região que o cofre. Apenas estas contas são apresentadas. Se não houver contas de armazenamento no local, precisa de criar uma.
- **Tipo de armazenamento**: O armazenamento de bolhas não é suportado.
- **Redundância de** armazenamento : O armazenamento redundante da zona (ZRS) não é suportado. A informação de replicação e redundância da conta é mostrada em parênteses após o nome da conta.
- **Armazenamento premium:**
  - Ao restaurar VMs não premium, as contas de armazenamento premium não são suportadas.
  - Ao restaurar os VM geridos, as contas de armazenamento premium configuradas com as regras de rede não são suportadas.

## <a name="before-you-start"></a>Antes de começar

Para restaurar um VM (criar um novo VM), certifique-se de que tem as [permissões corretas](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) de controlo de acesso baseado em funções (Azure RBAC) para a operação Restore VM.

Se não tiver permissões, pode [restaurar um disco](#restore-disks), e depois de o disco ser restaurado, pode utilizar o [modelo](#use-templates-to-customize-a-restored-vm) que foi gerado como parte da operação de restauro para criar um novo VM.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="select-a-restore-point"></a>Selecione um ponto de restauro

1. No cofre associado ao VM que pretende restaurar, selecione **itens de cópia de segurança**  >  **Azure Virtual Machine**.
1. Selecione um VM. Por predefinição no painel VM, são apresentados pontos de recuperação dos últimos 30 dias. Pode apresentar pontos de recuperação com mais de 30 dias ou filtrar para encontrar pontos de recuperação baseados em datas, intervalos de tempo e diferentes tipos de consistência instantânea.
1. Para restaurar o VM, selecione **Restaurar vM**.

    ![Ponto de restauro](./media/backup-azure-arm-restore-vms/restore-point.png)

1. Selecione um ponto de restauro a utilizar para a recuperação.

## <a name="choose-a-vm-restore-configuration"></a>Escolha uma configuração de restauro VM

1. Na **Máquina Virtual Restaurar,** selecione uma opção de restauro:
    - **Criar nova**: Utilize esta opção se quiser criar um novo VM. Pode criar um VM com configurações simples, ou restaurar um disco e criar um VM personalizado.
    - **Substitua a existência:** Utilize esta opção se pretender substituir discos num VM existente.

        ![Restaurar o assistente de configuração da máquina virtual](./media/backup-azure-arm-restore-vms/restore-configuration.png)

1. Especifique as definições para a opção de restauro selecionada.

## <a name="create-a-vm"></a>Criar uma VM

Como uma das opções de [restauro,](#restore-options)pode criar um VM rapidamente com definições básicas a partir de um ponto de restauro.

1. In **Restore Virtual Machine** Crie  >  **novo** Tipo de  >  **Restauro,** selecione Criar uma máquina **virtual**.
1. No **nome da máquina Virtual,** especifique um VM que não exista na subscrição.
1. No **grupo Resource**, selecione um grupo de recursos existente para o novo VM, ou crie um novo com um nome globalmente único. Se atribuir um nome que já existe, o Azure atribui ao grupo o mesmo nome que o VM.
1. Na **rede Virtual,** selecione o VNet no qual o VM será colocado. Todos os VNets associados à subscrição são apresentados. Selecione a sub-rede. A primeira sub-rede é selecionada por predefinição.
1. No **Local de Preparação,** especifique a conta de armazenamento do VM. [Saiba mais](#storage-accounts).

    ![Restaurar o assistente de configuração - escolha opções de restauro](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

1. **Selecione Restaurar** para ativar a operação de restauro.

## <a name="restore-disks"></a>Restaurar discos

Como uma das opções de [restauro,](#restore-options)pode criar um disco a partir de um ponto de restauração. Em seguida, com o disco, pode fazer uma das seguintes ações:

- Utilize o modelo gerado durante a operação de restauro para personalizar as definições e desencadear a implementação de VM. Edita as definições do modelo predefinido e submete o modelo para a implementação de VM.
- [Fixe os discos restaurados](../virtual-machines/windows/attach-managed-disk-portal.md) a um VM existente.
- [Crie um novo VM](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) a partir dos discos restaurados utilizando o PowerShell.

1. Na **configuração Restaurar**  >  **Crie novo** Tipo de  >  **Restauro,** selecione Discos De **restauro**.
1. No **grupo De recursos,** selecione um grupo de recursos existente para os discos restaurados, ou crie um novo com um nome globalmente único.
1. Na **localização de encenação**, especifique a conta de armazenamento para copiar os VHDs. [Saiba mais](#storage-accounts).

    ![Selecione grupo de recursos e localização de encenação](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. **Selecione Restaurar** para ativar a operação de restauro.

Quando a sua máquina virtual utiliza discos geridos e seleciona a opção **'Criar máquina virtual',** o Azure Backup não utiliza a conta de armazenamento especificada. No caso dos **discos Restore** e **Instant Restore,** a conta de armazenamento é utilizada apenas para armazenar o modelo. Os discos geridos são criados no grupo de recursos especificado.
Quando a sua máquina virtual utiliza discos não geridos, são restaurados como bolhas na conta de armazenamento.

### <a name="use-templates-to-customize-a-restored-vm"></a>Use modelos para personalizar um VM restaurado

Depois de restaurar o disco, utilize o modelo que foi gerado como parte da operação de restauro para personalizar e criar um novo VM:

1. Em **Backup Jobs,** selecione o trabalho de restauro relevante.

1. No **Restauro**, selecione **'Implementar' para** iniciar a implementação do modelo.

    ![Restaurar a perfuração de trabalho](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

1. Para personalizar a definição de VM fornecida no modelo, **selecione o modelo de edição**. Se quiser adicionar mais personalizações, **selecione Editar os parâmetros**.
    - [Saiba mais](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) sobre a implementação de recursos a partir de um modelo personalizado.
    - [Saiba mais](../azure-resource-manager/templates/template-syntax.md) sobre modelos de autoria.

   ![Implementação do modelo de carga](./media/backup-azure-arm-restore-vms/edit-template1.png)

1. Introduza os valores personalizados para o VM, aceite os **Termos e Condições** e selecione **Compra.**

   ![Submeter a implementação do modelo](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Substituir discos existentes

Como uma das opções de [restauro,](#restore-options)pode substituir um disco VM existente pelo ponto de restauro selecionado. [Reveja](#restore-options) todas as opções de restauro.

1. Na **configuração 'Restaurar'** selecione **Substitua a existência**.
1. No **Tipo De Restauro,** selecione **Substitua o disco/s**. Este é o ponto de restauro que será utilizado substituir os discos VM existentes.
1. Em **Localização de Preparação**, especifique onde devem ser guardadas imagens dos discos geridos atuais durante o processo de restauração. [Saiba mais](#storage-accounts).

   ![Restaurar o assistente de configuração Substituir o existente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Restauro da Região Transversal

Como uma das opções de [restauro,](#restore-options)o Cross Region Restore (CRR) permite restaurar os VMs Azure numa região secundária, que é uma região emparelhada Azure.

Para embarcar na funcionalidade durante a pré-visualização, leia a [secção Antes de Começar](./backup-create-rs-vault.md#set-cross-region-restore).

Para ver se a CRR está ativada, siga as instruções no [Configure Cross Region Restore](backup-create-rs-vault.md#configure-cross-region-restore).

### <a name="view-backup-items-in-secondary-region"></a>Ver itens de backup na região secundária

Se o CRR estiver ativado, pode ver os itens de reserva na região secundária.

1. A partir do portal, aceda a itens de backup de **cofres dos Serviços de Recuperação.**  >  
1. Selecione **Região Secundária** para ver os itens na região secundária.

>[!NOTE]
>Apenas os tipos de gestão de backup que suportam a função CRR serão apresentados na lista. Atualmente, apenas é permitido o apoio à reposição de dados da região secundária para uma região secundária.

![Máquinas virtuais na região secundária](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Selecione Região Secundária](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Restauro na região secundária

A região secundária restaurará a experiência do utilizador será semelhante à região primária restaurar a experiência do utilizador. Ao configurar detalhes no painel de configuração restaurar para configurar a sua restauração, será solicitado que forneça apenas parâmetros de região secundária.

![Escolha VM para restaurar](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Selecione ponto de restauro](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Restaurar a configuração](./media/backup-azure-arm-restore-vms/rest-config.png)

![Trigger restaurar na notificação de progresso](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Para restaurar e criar um VM, consulte a [Criação de um VM](#create-a-vm).
- Para restaurar como disco, consulte os [discos Restaurar](#restore-disks).

>[!NOTE]
>
>- Após o restauro ser desencadeado e na fase de transferência de dados, o trabalho de restauro não pode ser cancelado.
>- A função Cross Region Restore restaura os CMK (chaves geridas pelo cliente) ativados por VMs Azure, que não são apoiados num cofre de Serviços de Recuperação ativado pela CMK, uma vez que os VMs não habilitados pela CMK na região secundária.
>- Os papéis do Azure necessários para restaurar na região secundária são os mesmos que na região primária.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorização da região secundária restabelece postos de trabalho

1. A partir do portal, vá para o **cofre dos Serviços de Recuperação**  >  **trabalhos de backup**
1. Selecione **Região Secundária** para ver os itens na região secundária.

    ![Trabalhos de reserva filtrados](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restoring-unmanaged-vms-and-disks-as-managed"></a>Restaurar VMs e discos não geridos como geridos

É-lhe dada a opção de restaurar [discos não geridos](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks) como [discos geridos](../virtual-machines/managed-disks-overview.md) durante a restauração. Por predefinição, os VM/discos não geridos são restaurados como VMs/discos não geridos. No entanto, se optar por restaurar como VMs/discos geridos, agora é possível fazê-lo. Estes restauros não são desencadeados da fase instantânea, mas apenas da fase do cofre. Esta funcionalidade não está disponível para VMs encriptados não geridos.

![Restaurar como discos geridos](./media/backup-azure-arm-restore-vms/restore-as-managed-disks.png)

## <a name="restore-vms-with-special-configurations"></a>Restaurar VMs com configurações especiais

Há uma série de cenários comuns em que poderá ser necessário restaurar os VMs.

**Cenário** | **Orientação**
--- | ---
**Restaurar VMs usando benefício de uso híbrido** | Se um Windows VM utilizar [o licenciamento Hybrid Use Benefit (HUB),](../virtual-machines/windows/hybrid-use-benefit-licensing.md)restaure os discos e crie um novo VM utilizando o modelo fornecido (com o Tipo de **Licença** definido para **Windows_Server),** ou PowerShell.  Esta definição também pode ser aplicada após a criação do VM.
**Restaurar VMs durante um desastre do datacenter Azure** | Se o cofre utilizar GRS e o centro de dados primário para o VM diminuir, o Azure Backup suporta restaurar os VMs apoiados no centro de dados emparelhado. Selecione uma conta de armazenamento no datacenter emparelhado e restaure normalmente. A Azure Backup utiliza o serviço de computação na região emparelhada para criar o VM restaurado. [Saiba mais](/azure/architecture/resiliency/recovery-loss-azure-region) sobre a resiliência do datacenter.<br><br> Se o cofre utilizar GRS, pode escolher a nova funcionalidade, [Cross Region Restore](#cross-region-restore). Isto permite-lhe restaurar uma segunda região em cenários de paragem total ou parcial, ou mesmo que não haja nenhuma paragem.
**Restauro de metal nu** | A grande diferença entre VMs Azure e hipervisores no local é que não há consola VM disponível em Azure. É necessária uma consola para determinados cenários, como a recuperação utilizando uma cópia de segurança do tipo BMR de recuperação de metais nus (BMR). No entanto, a restauração de VM do cofre é um substituto completo para bMR.
**Restaurar VMs com configurações especiais de rede** | As configurações especiais de rede incluem VMs usando o equilíbrio de carga interna ou externa, usando vários NICS, ou vários endereços IP reservados. Restaura estes VM utilizando a [opção de disco de restauro.](#restore-disks) Esta opção faz uma cópia dos VHDs na conta de armazenamento especificada, podendo então criar um VM com um balançador de carga [interno](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) ou [externo,](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) [múltiplos NICS,](../virtual-machines/windows/multiple-nics.md)ou [vários endereços IP reservados,](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)de acordo com a sua configuração.
**Grupo de Segurança de Rede (NSG) em NIC/Subnet** | A cópia de segurança Azure VM suporta backup e restaurar informações NSG no nível Vnet, subnet e NIC.
**VMs de fixação de zona** | Se você apoiar um Azure VM que está preso a uma zona (com Azure Backup), então você pode restaurá-lo na mesma zona onde foi fixado. [Saiba mais](../availability-zones/az-overview.md)
**Restaurar VM em qualquer conjunto de disponibilidade** | Ao restaurar um VM do portal, não há opção para escolher um conjunto de disponibilidade. Um VM restaurado não tem um conjunto de disponibilidade. Se utilizar a opção de disco de restauro, poderá [especificar um conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) quando criar um VM a partir do disco utilizando o modelo ou PowerShell fornecido.
**Restaurar VMs especiais como SQL VMs** | Se estiver a apoiar um SQL VM utilizando a cópia de segurança Azure VM e, em seguida, utilizar a opção de restauro de VM ou criar um VM após a restauração dos discos, então o VM recém-criado deve ser registado com o fornecedor SQL como mencionado [aqui.](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%2cbash) Isto converterá o VM restaurado num SQL VM.

### <a name="restore-domain-controller-vms"></a>Restaurar vMs controladores de domínio

**Cenário** | **Orientação**
--- | ---
**Restaurar um único controlador de domínio VM em um único domínio** | Restaurar o VM como qualquer outro VM. Tenha em atenção que:<br/><br/> Do ponto de vista do Ative Directory, o Azure VM é como qualquer outro VM.<br/><br/> O Modo de Restauro de Serviços de Diretório (DSRM) também está disponível, pelo que todos os cenários de recuperação do Diretório Ativo são viáveis. [Saiba mais](#post-restore-steps) sobre backup e restaure considerações para controladores de domínio virtualizados.
**Restaurar vMs controladores de domínio múltiplos num único domínio** | Se outros controladores de domínio no mesmo domínio puderem ser alcançados ao longo da rede, o controlador de domínio pode ser restaurado como qualquer VM. Se for o último controlador de domínio restante no domínio, ou se for realizada uma recuperação numa rede isolada, utilize uma [recuperação florestal](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Restaurar um único controlador de domínio VM numa configuração de domínio múltiplo** |  Restaurar os discos e criar um VM [utilizando o PowerShell](backup-azure-vms-automation.md#restore-the-disks)  
**Restaurar vários domínios numa floresta** | Recomendamos uma [recuperação florestal.](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)

Para obter mais informações, consulte [Back up e restaure os controladores de domínio do Ative Directory](active-directory-backup-restore.md).

## <a name="track-the-restore-operation"></a>Acompanhe a operação de restauro

Depois de ativar a operação de restauro, o serviço de backup cria um trabalho para rastrear. O Azure Backup apresenta notificações sobre o trabalho no portal. Se não forem visíveis, selecione o símbolo **de Notificações** e, em seguida, selecione **Mais eventos no registo de atividade** para ver o Estado do Processo de Restauro.

![Restauro desencadeado](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Restauração da faixa da seguinte forma:

1. Para visualizar as operações para o trabalho, selecione a hiperligação de notificações. Em alternativa, no cofre, selecione **trabalhos de backup** e, em seguida, selecione os VM relevantes.

    ![Lista de VMs em um cofre](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

1. Para monitorizar o progresso, selecione qualquer trabalho de restauro com um estado **de progresso**. Isto mostra a barra de progresso, que exibe informações sobre o progresso do restauro:

    - **Tempo estimado de restauro**: Inicialmente fornece o tempo necessário para concluir a operação de restauro. À medida que a operação progride, o tempo desatar reduz-se e atinge zero quando a operação de restauro terminar.
    - **Percentagem de restauro**. Mostra a percentagem de operação de restauro que está feita.
    - **Número de bytes transferidos**: Se estiver a restaurar criando um novo VM, mostra os bytes que foram transferidos contra o número total de bytes a serem transferidos.

## <a name="post-restore-steps"></a>Passos pós-restauro

Há uma série de coisas a notar depois de restaurar um VM:

- As extensões presentes durante a configuração de backup são instaladas, mas não ativadas. Se vir algum problema, reinstale as extensões.
- Se o VM apoiado tiver um endereço IP estático, o VM restaurado terá um endereço IP dinâmico para evitar conflitos. Pode [adicionar um endereço IP estático ao VM restaurado](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description).
- Um VM restaurado não tem um conjunto de disponibilidade. Se utilizar a opção de disco de restauro, poderá [especificar um conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) quando criar um VM a partir do disco utilizando o modelo ou PowerShell fornecido.
- Se utilizar uma distribuição Linux baseada em nuvem, como ubuntu, por razões de segurança, a palavra-passe é bloqueada após a restauração. Utilize a extensão VMAccess no VM restaurado para [redefinir a palavra-passe](../virtual-machines/troubleshooting/reset-password.md). Recomendamos a utilização de teclas SSH nestas distribuições, para que não seja necessário redefinir a palavra-passe após a restauração.
- Se não conseguir aceder a um VM uma vez restaurado porque o VM tem uma relação quebrada com o controlador de domínio, então siga os passos abaixo para trazer o VM:
  - Anexe o disco de oss como um disco de dados a um VM recuperado.
  - Instale manualmente o agente VM se o Agente Azure não responder seguindo este [link](../virtual-machines/troubleshooting/install-vm-agent-offline.md).
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

- Uma vez que o VM seja desatado e reiniciado, poderá conseguir rdp para o VM com credenciais de administração local e voltar a juntar-se ao domínio com sucesso.

## <a name="backing-up-restored-vms"></a>Backup VMs restaurados

- Se restaurar um VM para o mesmo grupo de recursos com o mesmo nome que o VM originalmente apoiado, a cópia de segurança continua no VM após a restauração.
- Se restaurou o VM a um grupo de recursos diferente ou especificou um nome diferente para o VM restaurado, precisa de configurar a cópia de segurança para o VM restaurado.

## <a name="next-steps"></a>Próximos passos

- Se sentir dificuldades durante o processo de restauro, [reveja](backup-azure-vms-troubleshoot.md#restore) questões e erros comuns.
- Depois que o VM é restaurado, aprenda sobre [a gestão de máquinas virtuais](backup-azure-manage-vms.md)
