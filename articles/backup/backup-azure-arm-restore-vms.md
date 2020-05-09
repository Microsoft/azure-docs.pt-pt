---
title: Restaurar vMs utilizando o portal Azure
description: Restaurar uma máquina virtual Azure a partir de um ponto de recuperação utilizando o portal Azure
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 6a170755673c05448d1bb86af993cad929664949
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597778"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Como restaurar os dados da VM Azure no portal Azure

Este artigo descreve como restaurar os dados da Azure VM dos pontos de recuperação armazenados nos cofres dos Serviços de Recuperação de [Backup azure.](backup-overview.md)

## <a name="restore-options"></a>Opções de restauro

A Azure Backup fornece uma série de formas de restaurar um VM.

**Restaurar a opção** | **Detalhes**
--- | ---
**Crie uma nova VM** | Rapidamente cria e obtém um VM básico em funcionamento a partir de um ponto de restauro.<br/><br/> Pode especificar um nome para o VM, selecionar o grupo de recursos e a rede virtual (VNet) em que será colocado, e especificar uma conta de armazenamento para o VM restaurado. O novo VM deve ser criado na mesma região que a fonte VM.
**Restaurar disco** | Restaura um disco VM, que pode ser usado para criar um novo VM.<br/><br/> O Azure Backup fornece um modelo para o ajudar a personalizar e criar um VM. <br/><br> O trabalho de restauro gera um modelo que pode descarregar e usar para especificar as definições personalizadas de VM e criar um VM.<br/><br/> Os discos são copiados para o Grupo de Recursos que especifica.<br/><br/> Em alternativa, pode ligar o disco a um VM existente ou criar um novo VM utilizando o PowerShell.<br/><br/> Esta opção é útil se quiser personalizar o VM, adicionar configurações de configuração que não estavam lá no momento da cópia de segurança, ou adicionar configurações que devem ser configuradas usando o modelo ou PowerShell.
**Substituir os existentes** | Pode restaurar um disco e usá-lo para substituir um disco no VM existente.<br/><br/> O VM atual deve existir. Se foi apagado, esta opção não pode ser usada.<br/><br/> O Azure Backup tira uma foto do VM existente antes de substituir o disco e armazena-o no local de paragem que especifica. Os discos existentes ligados ao VM são substituídos pelo ponto de restauro selecionado.<br/><br/> O instantâneo é copiado para o cofre, e mantido de acordo com a política de retenção. <br/><br/> Após a operação de substituição do disco, o disco original é mantido no grupo de recursos. Pode optar por eliminar manualmente os discos originais se não forem necessários. <br/><br/>A substituição existente é suportada para VMs geridos não encriptados, incluindo VMs [criados usando imagens personalizadas](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). Não é suportado para VMs clássicos.<br/><br/> Se o ponto de restauro tiver discos mais ou menos do que o VM atual, então o número de discos no ponto de restauro apenas refletirá a configuração VM.<br><br> A substituição existente não é suportada por VMs com recursos ligados (como [identidade gerida atribuída](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pelo utilizador ou Key [Vault)](https://docs.microsoft.com/azure/key-vault/key-vault-overview)porque a aplicação de backup do cliente não tem permissões sobre estes recursos enquanto executa o restauro.
**Região Transversal (região secundária)** | A restauração da Região Transversal pode ser usada para restaurar os VMs azure na região secundária, que é uma [região de pares Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)<br><br> Você pode restaurar todos os VMs Azure para o ponto de recuperação selecionado se o backup for feito na região secundária.<br><br> Esta funcionalidade está disponível para as opções abaixo:<br> <li> [Criar uma VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> <li> [Restaurar discos](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Atualmente não apoiamos a opção De substituição dos [discos existentes.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)<br><br> Permissões<br> A operação de restauro na região secundária pode ser realizada por Administradores de Backup e administradores de aplicações.

> [!NOTE]
> Também pode recuperar ficheiros e pastas específicos num VM Azure. [Saiba mais](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Contas de armazenamento

Alguns detalhes sobre contas de armazenamento:

- **Criar VM**: Quando criar um novo VM, o VM será colocado na conta de armazenamento que especificar.
- **Restaurar**o disco : Quando restaurar um disco, o disco é copiado para a conta de armazenamento que especifica. O trabalho de restauro gera um modelo que pode descarregar e usar para especificar as definições personalizadas de VM. Este modelo é colocado na conta de armazenamento especificada.
- **Substitua o disco**: Quando substituir um disco num VM existente, o Azure Backup tira uma fotografia do VM existente antes de substituir o disco. O instantâneo é armazenado no local de paragem (conta de armazenamento) que especifica. Esta conta de armazenamento é usada para armazenar temporariamente o instantâneo durante o processo de restauro, e recomendamos que crie uma nova conta para o fazer, que possa ser facilmente removida depois.
- **Localização**da conta de armazenamento : A conta de armazenamento deve estar na mesma região que o cofre. Apenas estas contas são apresentadas. Se não houver contas de armazenamento no local, precisa de criar uma.
- **Tipo**de armazenamento : O armazenamento blob não é suportado.
- **Redundância**de armazenamento : O armazenamento redundante da zona (ZRS) não é suportado. A informação de replicação e despedimento da conta é mostrada nos parênteses após o nome da conta.
- **Armazenamento premium:**
  - Ao restaurar vMs não premium, as contas de armazenamento premium não são suportadas.
  - Ao restaurar os VMs geridos, as contas de armazenamento premium configuradas com regras de rede não são suportadas.

## <a name="before-you-start"></a>Antes de começar

Para restaurar um VM (crie um novo VM), certifique-se de que tem as [permissões corretas](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) de controlo de acesso baseados em funções (RBAC) para a operação Restore VM.

Se não tiver permissões, pode [restaurar um disco](#restore-disks), e depois de restaurado o disco, pode utilizar o [modelo](#use-templates-to-customize-a-restored-vm) que foi gerado como parte da operação de restauro para criar um novo VM.

## <a name="select-a-restore-point"></a>Selecione um ponto de restauro

1. No cofre associado ao VM que pretende restaurar, clique **em itens** > de backup**Máquina Virtual Azure**.
2. Clique num VM. Por padrão no painel vm, os pontos de recuperação dos últimos 30 dias são apresentados. Pode apresentar pontos de recuperação com mais de 30 dias, ou filtrar para encontrar pontos de recuperação com base em datas, intervalos de tempo e diferentes tipos de consistência instantânea.
3. Para restaurar o VM, clique em **Restaurar vM**.

    ![Ponto de restauro](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Selecione um ponto de restauro para a recuperação.

## <a name="choose-a-vm-restore-configuration"></a>Escolha uma configuração de restauro VM

1. Na **configuração restaurar,** selecione uma opção de restauro:
    - **Criar nova**: Utilize esta opção se quiser criar um novo VM. Pode criar um VM com configurações simples, ou restaurar um disco e criar um VM personalizado.
    - **Substitua a existência**: Utilize esta opção se pretender substituir os discos num VM existente.

        ![Restaurar o assistente de configuração](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Especifique as definições para a sua opção de restauro selecionada.

## <a name="create-a-vm"></a>Criar uma VM

Como uma das opções de [restauro,](#restore-options)pode criar um VM rapidamente com configurações básicas a partir de um ponto de restauro.

1. Na **configuração** > restaurar**Crie um novo** > **tipo de restauro,** selecione Criar uma máquina **virtual**.
2. No nome da **máquina Virtual,** especifique um VM que não exista na subscrição.
3. No **grupo Recursos**, selecione um grupo de recursos existente para o novo VM, ou crie um novo com um nome globalmente único. Se atribuir um nome que já existe, o Azure atribui ao grupo o mesmo nome que o VM.
4. Na **rede Virtual,** selecione o VNet no qual o VM será colocado. Todos os VNets associados à subscrição são apresentados. Selecione a sub-rede. A primeira sub-rede é selecionada por defeito.
5. No **Local de Armazenamento,** especifique a conta de armazenamento para o VM. [Saiba mais](#storage-accounts).

    ![Restaurar o assistente de configuração](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. Na **configuração restaurar,** selecione **OK**. Em **Restaurar,** clique em **Restaurar** para acionar a operação de restauro.

## <a name="restore-disks"></a>Restaurar discos

Como uma das opções de [restauro,](#restore-options)pode criar um disco a partir de um ponto de restauro. Depois, com o disco, pode fazer um dos seguintes:

- Utilize o modelo gerado durante a operação de restauro para personalizar as definições e acionar a implementação do VM. Edita as definições do modelo predefinido e submete o modelo para a implementação de VM.
- [Fixe os discos restaurados](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) a um VM existente.
- [Crie um novo VM](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) a partir dos discos restaurados utilizando o PowerShell.

1. Na **configuração** > **restaurar, crie um novo** > tipo de**restauro,** selecione discos de **restauração**.
2. No **grupo Recursos,** selecione um grupo de recursos existente para os discos restaurados, ou crie um novo com um nome globalmente único.
3. Na **conta de Armazenamento,** especifique a conta para a qual copiar os VHDs. [Saiba mais](#storage-accounts).

    ![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. Na **configuração restaurar,** selecione **OK**. Em **Restaurar,** clique em **Restaurar** para acionar a operação de restauro.

Quando a sua máquina virtual utiliza discos geridos e seleciona a opção Criar a **máquina virtual,** o Azure Backup não utiliza a conta de armazenamento especificada. No caso dos discos restaurar e **restaurar** **instantâneos,** a conta de armazenamento é utilizada apenas para armazenar o modelo. Os discos geridos são criados no grupo de recursos especificado.
Quando a sua máquina virtual utiliza discos não geridos, são restaurados como bolhas na conta de armazenamento.

### <a name="use-templates-to-customize-a-restored-vm"></a>Use modelos para personalizar um VM restaurado

Depois de restaurado o disco, utilize o modelo que foi gerado como parte da operação de restauro para personalizar e criar um novo VM:

1. Detalhes de trabalho de **restauro** aberto para o trabalho relevante.

2. Em **Restaurar detalhes de trabalho,** selecione modelo de **implementação** para iniciar a implementação do modelo.

    ![Restaurar a perfuração de trabalho](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Para personalizar a definição de VM fornecida no modelo, clique no **modelo editar**. Se quiser adicionar mais personalizações, clique em **parâmetros editar**.
    - [Saiba mais](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) sobre a implementação de recursos a partir de um modelo personalizado.
    - [Saiba mais](../azure-resource-manager/templates/template-syntax.md) sobre modelos de autoria.

   ![Implantação do modelo de carga](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Introduza os valores personalizados para o VM, aceite os **Termos e Condições** e clique em **Comprar**.

   ![Enviar a implementação do modelo](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Substituir os discos existentes

Como uma das opções de [restauro,](#restore-options)pode substituir um disco VM existente pelo ponto de restauro selecionado. [Reveja](#restore-options) todas as opções de restauro.

1. Na **configuração restaurar,** clique **em substituir o existente**.
2. No **Tipo restaurar,** selecione **Substitua o disco/s**. Este é o ponto de restauro que será usado substituir os discos VM existentes.
3. No **Local de Preparação,** especifique onde as imagens dos discos geridos atuais devem ser guardadas durante o processo de restauro. [Saiba mais](#storage-accounts).

   ![Restaurar o assistente de configuração Substitua o existente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Restauro da região transversal

Como uma das opções de [restauro,](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)cross region Restore (CRR) permite restaurar Os VMs Azure em uma região secundária, que é uma região em pares Azure.

Para bordo da funcionalidade durante a pré-visualização, leia a [secção Antes de Começar](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Para ver se o CRR está ativado, siga as instruções em [Configurar Cross Region Restore](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Ver itens de backup na região secundária

Se o CRR estiver ativado, pode visualizar os itens de backup na região secundária.

1. A partir do portal, vá a **cofre** > de serviços de recuperação itens de**backup**
2. Clique na **Região Secundária** para ver os itens na região secundária.

![Máquinas virtuais na região secundária](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Selecione Região Secundária](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Restaurar na região secundária

A experiência de repor a região secundária será semelhante à experiência do utilizador na região primária. Ao configurar detalhes na lâmina de configuração de configuração de restauro para configurar o seu restauro, será-lhe solicitado que forneça apenas parâmetros da região secundária.

![Escolha VM para restaurar](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Selecione ponto de restauro](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Restaurar a configuração](./media/backup-azure-arm-restore-vms/rest-config.png)

![Desativar a notificação de progresso](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Para restaurar e criar um VM, consulte criar [um VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm).
- Para restaurar como disco, consulte os [discos Restaurar](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks).

>[!NOTE]
>Após o restabelecimento ser acionado e na fase de transferência de dados, o trabalho de restauro não pode ser cancelado.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorização da região secundária restaura postos de trabalho

1. Do portal, vá ao **cofre** > dos Serviços de Recuperação Backup**Jobs**
2. Clique na **Região Secundária** para ver os itens na região secundária.

![Trabalhos de reserva filtrados](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>Restaurar VMs com configurações especiais

Há uma série de cenários comuns em que você pode precisar restaurar VMs.

**Cenário** | **Orientação**
--- | ---
**Restaurar VMs usando benefício de uso híbrido** | Se um VM windows utilizar [o licenciamento hybrid Use Benefit (HUB),](../virtual-machines/windows/hybrid-use-benefit-licensing.md)restaure os discos e crie um novo VM utilizando o modelo fornecido (com o Tipo de **Licença** definido para **Windows_Server),** ou PowerShell.  Esta definição também pode ser aplicada após a criação do VM.
**Restaurar VMs durante um desastre do datacenter azure** | Se o cofre utilizar GRS e o centro de dados primário para o VM descer, o Azure Backup suporta restaurar os VMs apoiados para o centro de dados emparelhado. Selecione uma conta de armazenamento no datacenter emparelhado e restaure normalmente. O Azure Backup utiliza o serviço de computação na região emparelhada para criar o VM restaurado. [Saiba mais](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) sobre a resiliência do datacenter.
**Restaurar o controlador de domínio único VM em domínio único** | Restaure o VM como qualquer outro VM. Tenha em atenção que:<br/><br/> Do ponto de vista do Diretório Ativo, o Azure VM é como qualquer outro VM.<br/><br/> O Modo de Restauro de Serviços de Diretório (DSRM) também está disponível, pelo que todos os cenários de recuperação do Diretório Ativo são viáveis. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) sobre backup e restaurar considerações para controladores de domínio virtualizados.
**Restaurar vários VMs controladores de domínio em domínio único** | Se outros controladores de domínio no mesmo domínio puderem ser alcançados sobre a rede, o controlador de domínio pode ser restaurado como qualquer VM. Se for o último controlador de domínio restante no domínio, ou se for realizada uma recuperação numa rede isolada, utilize uma [recuperação florestal](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Restaurar vários domínios numa floresta** | Recomendamos uma [recuperação florestal.](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)
**Restauro de metal nu** | A grande diferença entre Os VMs Azure e os hipervisores no local é que não há nenhuma consola VM disponível no Azure. É necessária uma consola para determinados cenários, tais como a recuperação utilizando uma cópia de segurança do tipo BMR (BMR). No entanto, o restauro vm do cofre é um substituto completo para bMR.
**Restaurar VMs com configurações especiais de rede** | Configurações especiais de rede incluem VMs usando o equilíbrio de carga interna ou externa, usando múltiplos NICS, ou vários endereços IP reservados. Restaure estes VMs utilizando a [opção restaurar](#restore-disks)o disco . Esta opção faz uma cópia dos VHDs na conta de armazenamento especificada, e pode então criar um VM com um equilibrador de carga [interna](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) ou [externa,](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell) [múltiplos NICS,](../virtual-machines/windows/multiple-nics.md)ou [múltiplos endereços IP reservados,](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)de acordo com a sua configuração.
**Grupo de Segurança de Rede (NSG) em NIC/Subnet** | A cópia de segurança Azure VM suporta backup e restauro de informações de NSG a nível vnet, subnet e NIC.
**VMs da zona** | Se você apoiar um VM Azure que está preso a uma zona (com Azure Backup), então você pode restaurá-lo na mesma zona onde foi fixado. [Mais informações](https://docs.microsoft.com/azure/availability-zones/az-overview)

## <a name="track-the-restore-operation"></a>Acompanhe a operação de restauro

Depois de acionar a operação de restauro, o serviço de reserva cria um trabalho de rastreio. O Azure Backup apresenta notificações sobre o trabalho no portal. Se não estiverem visíveis, selecione o símbolo **notificações** e, em seguida, selecione **Ver todos os Trabalhos** para ver o Estado do Processo de Restauro.

![Restaurar desencadeado](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Restauro da pista da seguinte forma:

1. Para visualizar as operações para o trabalho, clique na hiperligação de notificações. Em alternativa, no cofre, clique em **backups**e, em seguida, clique no VM relevante.

    ![Lista de VMs em um cofre](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Para monitorizar o progresso, clique em qualquer trabalho de restauro com um estado de **in-progresso**. Isto exibe a barra de progresso, que exibe informações sobre o progresso de restauro:

    - **Tempo estimado de restauro**: Inicialmente fornece o tempo necessário para completar a operação de restauro. À medida que a operação progride, o tempo devida reduz-se e chega a zero quando a operação de restauro termina.
    - **Percentagem de restauro.** Mostra a percentagem de operação de restauro que está feita.
    - **Número de bytes transferidos**: Se estiver a restaurar através da criação de um novo VM, mostra os bytes que foram transferidos contra o número total de bytes a transferir.

## <a name="post-restore-steps"></a>Etapas pós-restauro

Há uma série de coisas a notar depois de restaurar um VM:

- As extensões presentes durante a configuração de cópia de segurança são instaladas, mas não ativadas. Se vir algum problema, reinstale as extensões.
- Se o VM apoiado tivesse um endereço IP estático, o VM restaurado terá um endereço IP dinâmico para evitar conflitos. Pode [adicionar um endereço IP estático ao VM restaurado](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description).
- Um VM restaurado não tem um conjunto de disponibilidade. Se utilizar a opção restaurar o disco, pode [especificar um conjunto](../virtual-machines/windows/tutorial-availability-sets.md) de disponibilidade quando criar um VM a partir do disco utilizando o modelo fornecido ou powerShell.
- Se utilizar uma distribuição linux baseada em nuvem, como o Ubuntu, por razões de segurança, a palavra-passe é bloqueada após a restauração. Utilize a extensão VMAccess no VM restaurado para [redefinir a palavra-passe](../virtual-machines/linux/reset-password.md). Recomendamos a utilização de chaves SSH nestas distribuições, para que não seja necessário redefinir a palavra-passe após a restauração.
- Se não conseguir aceder a um VM uma vez restaurado devido ao VM ter uma relação quebrada com o controlador de domínio, siga os passos abaixo para elevar o VM:
  - Fixe o disco OS como um disco de dados a um VM recuperado.
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

  - Quando o VM é reconstruído, utilize o portal Azure para redefinir a conta de administrador local e a palavra-passe
  - Utilize o acesso à consola em série e o CMD para desjuntar o VM do domínio

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Assim que o VM for desunido e reiniciado, poderá sutificar com sucesso rdP para o VM com credenciais de administração locais e voltar a juntar-se à VM com sucesso.

## <a name="backing-up-restored-vms"></a>Backup de VMs restaurados

- Se restaurou um VM para o mesmo grupo de recursos com o mesmo nome que o VM originalmente apoiado, a cópia de segurança continua no VM após o restauro.
- Se restaurou o VM a um grupo de recursos diferente ou especificou um nome diferente para o VM restaurado, precisa de configurar a cópia de segurança para o VM restaurado.

## <a name="next-steps"></a>Passos seguintes

- Se tiver dificuldades durante o processo de restauro, [reveja](backup-azure-vms-troubleshoot.md#restore) questões e erros comuns.
- Depois do VM ser restaurado, aprenda sobre a gestão de [máquinas virtuais](backup-azure-manage-vms.md)
