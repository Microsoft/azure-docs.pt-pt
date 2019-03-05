---
title: 'Cópia de segurança do Azure: Restaurar máquinas virtuais com o portal do Azure'
description: Restaurar uma máquina virtual do Azure a partir de um ponto de recuperação com o portal do Azure
services: backup
author: geethalakshmig
manager: vijayts
keywords: restaurar cópia de segurança; como restaurar; ponto de recuperação;
ms.service: backup
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: geg
ms.openlocfilehash: 0140cbc440e6098290a81f7ae61aa99e36da7d80
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57312040"
---
# <a name="restore-azure-vms"></a>Restaurar as VMs do Azure

Este artigo descreve como restaurar dados de VM do Azure a partir dos pontos de recuperação armazenados no [Azure Backup](backup-overview.md) cofres dos serviços de recuperação.

### <a name="restore-options"></a>Opções de restauro

Cópia de segurança do Azure fornece várias formas para restaurar uma VM.

**Opção de restaurar** | **Detalhes**
--- | ---
**Criar uma nova VM** | Cria e obtém uma VM básica em execução de um ponto de restauro rapidamente.<br/><br/> Pode especificar um nome para a VM, selecione o grupo de recursos e a rede virtual (VNet) no qual será efetuada e especificar um tipo de armazenamento.
**Restaurar disco** | Restaura um disco VM que, em seguida, pode ser utilizado para criar uma nova VM.<br/><br/> Cópia de segurança do Azure fornece um modelo para o ajudar a personalizar e criar uma VM. <br/><br/> Esta opção copia o VHD para a conta de armazenamento especificada. A tarefa de restauro gera um modelo que pode transferir e utilizar para especificar definições de VM personalizadas e criar uma VM.<br/><br/> -A conta de armazenamento deve estar na mesma localização que o cofre. Se não tiver uma, crie uma conta de armazenamento.<br/><br/> O tipo de replicação de conta de armazenamento é apresentado. Armazenamento com redundância de zona (ZRS) não é suportado.<br/><br/> Em alternativa, pode anexar o disco a uma VM existente ou crie uma nova VM com o PowerShell.<br/><br/> Esta opção é útil se pretender personalizar a VM, adicionar as definições de configuração que não estavam lá no momento da cópia de segurança ou adicionar as definições que tem de ser configuradas com o modelo ou o PowerShell.
**Substituir a existente** | Pode restaurar um disco e utilizá-lo para substituir um disco na VM existente.<br/><br/> A VM atual tem de existir. Se é eliminado esta opção não pode ser utilizada.<br/><br/> O Azure Backup tira um instantâneo da VM existente antes de substituir o disco. O instantâneo é armazenado no que especifique a localização de transição. Discos existentes ligados à VM, em seguida, são substituídos com o ponto de restauro selecionado. O instantâneo que foi tirado é copiado para o Cofre e mantido de acordo com a política de retenção especificado.<br/><br/> Substituir existente é suportada para VMs geridas não encriptadas. Não é suportado para discos não geridos, [generalizada VMs](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource), ou para VMs [criado usando imagens personalizadas](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Se o ponto de restauro mais ou menos tem discos que a VM atual, o número de discos no ponto de restauro apenas irá refletir a configuração da VM.

> [!NOTE]
> Também pode recuperar arquivos específicos e pastas numa VM do Azure. [Saiba mais](backup-azure-restore-files-from-vm.md).
>
> Se estiver a executar o [versão mais recente](backup-instant-restore-capability.md) de Azure Backup para VMs do Azure (conhecido como restaurar instantânea), os instantâneos são mantidos até sete dias e pode restaurar uma VM a partir de instantâneos antes dos dados de cópia de segurança são enviados para o cofre. Se quiser restaurar uma VM a partir de uma cópia de segurança dos últimos sete dias, é mais rápido restaurar a partir do instantâneo e não a partir do cofre.


## <a name="select-a-restore-point"></a>Selecione um ponto de restauro

1. No cofre associado à VM que pretende restaurar, clique em **itens de cópia de segurança** > **Máquina Virtual do Azure**.
2. Clique numa VM. Por predefinição no dashboard de VM, são apresentados os pontos de recuperação dos últimos 30 dias. Pode exibir os pontos de recuperação com mais de 30 dias, ou um filtro para encontrar pontos de recuperação com base nas datas, intervalos de tempo e diferentes tipos de consistência de instantâneo.
3. Para restaurar a VM, clique em **restaurar a VM**.

    ![Ponto de restauro](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Selecione um ponto de restauro para utilização na recuperação.

## <a name="choose-a-vm-restore-configuration"></a>Escolha uma configuração de restauro de VM

1. Na **restauro da configuração**, selecione uma opção de restauro:
    - **Crie um novo**. Utilize esta opção se pretender criar uma nova VM. Pode criar uma VM com definições simples, ou restaurar um disco e criar uma VM personalizada.
    - **Substituir a existente**: Utilize esta opção se pretender substituir discos numa VM existente.

        ![Restaurar o Assistente de configuração](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Especifica definições para a sua opção de restaurar selecionado.

## <a name="create-new-create-a-vm"></a>Criar novo-criar uma VM

Como um da [opções de restauro](#restore-options), pode criar rapidamente uma VM com as definições básicas de um ponto de restauro.

1. Na **configuração do restauro** > **criar novo** > **restaurar tipo**, selecione **criar uma máquina virtual** .
2. Na **nome da Máquina Virtual**, especifique uma VM que não existe na subscrição.
3. Na **grupo de recursos**, selecione o grupo de recursos existente para a nova VM, ou criar um novo com um nome globalmente exclusivo. Se atribuir um nome que já existe, o Azure atribui o grupo do mesmo nome que a VM.
4. Na **rede Virtual**, selecione a VNet na qual a VM será realizada. São apresentadas todas as VNets associadas à subscrição. Selecione a sub-rede. A primeira sub-rede está selecionada por predefinição.
5. Na **localização de armazenamento**, especifique o tipo de armazenamento para a VM.

    ![Restaurar o Assistente de configuração](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. Na **configuração do restauro**, selecione **OK**. Na **restaurar**, clique em **restaurar** para acionar a operação de restauro.


## <a name="create-new-restore-disks"></a>Criar novo restaurar discos

Como um da [opções de restauro](#restore-options), é possível criar um disco a partir de um ponto de restauro. Em seguida, com o disco, pode efetuar um dos seguintes:

- Utilize o modelo que é gerado durante a operação de restauro para personalizar as definições e acionar a implementação da VM. Editar as definições de modelo padrão e submeta o modelo de implementação da VM.
- [Anexar discos restaurados](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) a uma VM existente.
- [Criar uma nova VM](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) dos discos restaurados com o PowerShell.

1. Na **configuração do restauro** > **criar novo** > **restaurar tipo**, selecione **restaurar discos**.
2. Na **grupo de recursos**, selecione um grupo de recursos existente para os discos restaurados ou criar um novo com um nome globalmente exclusivo.
3. Na **conta de armazenamento**, especifique a conta ao qual pretende copiar os VHDs. Certifique-se que a conta está na mesma região que o cofre.

    ![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. Na **configuração do restauro**, selecione **OK**. Na **restaurar**, clique em **restaurar** para acionar a operação de restauro.

### <a name="use-templates-to-customize-a-restored-vm"></a>Utilize modelos para personalizar uma VM restaurada

Depois de restaurar o disco, utilize o modelo que foi gerado como parte da operação de restauro para personalizar e criar uma nova VM:

1. Open **detalhes da tarefa de restauro** para a tarefa relevante.

2. Na **detalhes da tarefa de restauro**, selecione **para implementar o modelo** para iniciar a implementação do modelo.

    ![Restaurar a desagregação da tarefa](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Para personalizar a definição de VM fornecida no modelo, clique em **Editar modelo**. Se quiser adicionar personalizações mais, clique em **Editar parâmetros**.
    - [Saiba mais](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) sobre a implementação de recursos a partir de um modelo personalizado.
    - [Saiba mais](../azure-resource-manager/resource-group-authoring-templates.md) sobre a criação de modelos.

  ![Implementação do modelo de carga](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Introduza os valores personalizados para a VM, aceite o **termos e condições** e clique em **Compra**.

  ![Submeter a implementação de modelo](./media/backup-azure-arm-restore-vms/submitting-template1.png)


## <a name="replace-existing-disks"></a>Substituir discos existentes

Como um da [opções de restauro](#restore-options), pode substituir um disco VM existente com o ponto de restauro selecionado. [Revisão](#restore-options) todas as opções de restauro.

1. Na **configuração do restauro**, clique em **substituir a existente**.
2. Na **restaurar tipo**, selecione **substitua/s de disco**. Este é o ponto de restauro que serão utilizados substituir discos de VM existentes.
3. Na **localização de transição**, especifique onde os instantâneos dos discos geridos atuais deverá ser guardados.

   ![Restaurar o Assistente de configuração substituir existente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="restore-vms-with-special-configurations"></a>Restaurar VMs com configurações especiais

Existem vários cenários comuns em que poderá ter de restaurar VMs.

**Cenário** | **Orientação**
--- | ---
**Restaurar VMs com o benefício de utilização híbrida** | Se uma VM do Windows utiliza [benefício de utilização híbrida (HUB) licenciamento](../virtual-machines/windows/hybrid-use-benefit-licensing.md), restaurar os discos e criar uma nova VM com o modelo fornecido (com **tipo de licença** definido como **Windows_Server**) , ou o PowerShell.  Também pode ser aplicada esta definição depois de criar a VM.
**Restaurar VMs durante um desastre de datacenter do Azure** | Se o Cofre utiliza GRS e o datacenter primário para a VM ficar inativo, o Azure Backup suporta restauro de uma cópia de segurança de VMs para o datacenter emparelhado. Selecione uma conta de armazenamento no Centro de dados emparelhado e restaurar normalmente. O Azure Backup utiliza o serviço de computação na localização emparelhada para criar a VM restaurada. [Saiba mais](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) sobre resiliência do Centro de dados.
**Restaurar a VM num único domínio do controlador de domínio único** | Restaure a VM, tal como qualquer outra VM. Tenha em atenção que:<br/><br/> ROM uma perspectiva do Active Directory, a VM do Azure é como qualquer outra VM.<br/><br/> Modo de restauro serviços de diretório (DSRM) também está disponível, portanto, todos os cenários de recuperação do Active Directory são viáveis. [Saiba mais](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v) sobre as considerações de cópia de segurança e restauro para controladores de domínio virtualizados.
**Restaurar o controlador de domínio de várias VMs num único domínio** | f outros controladores de domínio no mesmo domínio podem ser contatados através da rede, o controlador de domínio pode ser restaurado como qualquer VM. Se é o último controlador de domínio restantes no domínio ou uma recuperação numa rede isolada é executada, utilize um [recuperação de floresta](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Restaurar vários domínios numa floresta** | Recomendamos um [recuperação de floresta](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Restauração bare-metal** | A principal diferença entre as VMs do Azure e hipervisores no local é que não existe nenhuma consola da VM disponíveis no Azure. Uma consola é necessária para determinados cenários, tais como de recuperação por meio de uma recuperação bare-metal (BMR)-cópia de segurança de tipo. No entanto, o restauro de VMS a partir do Cofre é uma substituição completa para a BMR.
**Restaurar VMs com configurações de rede especiais** | Configurações de rede especiais incluem VMs com interna ou externa balanceamento de carga, com várias NICS ou vários endereços IP reservados. Restaurar estas VMs utilizando o [restaurar a opção de disco](#create-new-restore-disks). Esta opção faz uma cópia dos VHDs para a conta de armazenamento especificada e, em seguida, pode criar uma VM com uma [interno](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) ou [externos](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/) Balanceador de carga [várias NICS](../virtual-machines/windows/multiple-nics.md), ou [múltiplo de endereços IP reservados](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md), de acordo com sua configuração.
**Grupo de segurança de rede (NSG) na sub-rede/NIC** | Cópia de segurança VM do Azure suporta a cópia de segurança e restauro do NSG informações na vnet, sub-rede e ao nível do NIC.

## <a name="track-the-restore-operation"></a>Controlar a operação de restauro
Depois de acionar a operação de restauro, o serviço de cópia de segurança cria uma tarefa para acompanhamento. Cópia de segurança do Azure apresenta notificações sobre a tarefa no portal. Se não forem visíveis, clique nas **notificações** símbolo para vê-los.

![Restauro acionado](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Controle o restauro da seguinte forma:

1. Para ver operações para a tarefa, clique na hiperligação de notificações. Em alternativa, no cofre, clique em **tarefas de cópia de segurança**e, em seguida, clique na VM de relevante.

    ![Lista de VMs num cofre](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Para monitorizar o progresso de restauro, clique em qualquer tarefa de restauro com o estado **em curso**. Esta ação apresenta a barra de progresso que exibe informações sobre o progresso de restauro:

    - **Tempo de restauro estimado**: Inicialmente, fornece o tempo necessário para concluir a operação de restauro. À medida que a operação avança, o tempo decorrido reduz e chegue a zero quando a operação de restauro estiver concluída.
    - **Percentagem de restauro**. Mostra a percentagem de operação de restauro que tenha feito.
    - **Número de bytes transferidos**: Se estiver a restaurar através da criação de uma nova VM, ela mostra os bytes que foram transferidos com o número total de bytes a serem transferidos.

## <a name="post-restore-steps"></a>Passos de pós-restauro

Há uma série de coisas a salientar depois de restaurar uma VM:

- As extensões estão presentes durante a configuração de cópia de segurança são instaladas, mas não ativadas. Se vir um problema, reinstale as extensões.
- Se a VM de cópia de segurança tinha um endereço IP estático, a VM restaurada terão um endereço IP dinâmico para evitar conflitos. Pode [adicionar um endereço IP estático para a VM restaurada](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
- Uma VM restaurada não tem um disponibilidade definida. Se a UO utilize a opção de disco de restauro para que pode [especificar um conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) quando criar uma VM a partir do disco com o modelo fornecido ou o PowerShell.
- Se usar uma distribuição do Linux com base na cloud-init, como o Ubuntu, por motivos de segurança, a palavra-passe é bloqueada após o restauro. Utilizar a extensão VMAccess à VM restaurada para [repor a palavra-passe](../virtual-machines/linux/reset-password.md). Recomendamos que utilize chaves SSH nessas distribuições, por isso não terá de repor a palavra-passe após o restauro.


## <a name="backing-up-restored-vms"></a>Cópias de segurança restauradas VMs

- Se restaurar uma VM para o mesmo grupo de recursos com o mesmo nome da VM de cópia de segurança originalmente, o backup continua na VM depois de restauro.
- Se tiver restaurado a VM a um grupo de recursos diferente ou que especificou um nome diferente para a VM restaurada, terá de configurar a cópia de segurança para a VM restaurada.


## <a name="next-steps"></a>Passos Seguintes

- Se tiver dificuldades durante o processo de restauração [rever](backup-azure-vms-troubleshoot.md#restore) problemas e erros comuns.
- Depois de restaurar a VM, saiba mais sobre [gerir máquinas virtuais](backup-azure-manage-vms.md)
