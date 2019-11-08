---
title: Planejar a capacidade e o dimensionamento para a recuperação de desastres do VMware no Azure usando Azure Site Recovery | Microsoft Docs
description: Este artigo pode ajudá-lo a planejar a capacidade e o dimensionamento ao configurar a recuperação de desastre de VMs VMware no Azure usando Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 0bf1b34295d827124198206e743bc21d5f7eb904
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747898"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Planejar a capacidade e o dimensionamento para a recuperação de desastres do VMware no Azure

Use este artigo para planejar a capacidade e o dimensionamento ao replicar VMs VMware locais e servidores físicos para o Azure usando [Azure site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Como fazer iniciar o planejamento de capacidade?

Para saber mais sobre os requisitos de infraestrutura do Azure Site Recovery, reúna informações sobre o ambiente de replicação executando o [planejador de implantações do Azure site Recovery](https://aka.ms/asr-deployment-planner-doc) para replicação do VMware. Para obter mais informações, consulte [sobre Site Recovery planejador de implantações para VMware no Azure](site-recovery-deployment-planner.md). 

Site Recovery Planejador de Implantações fornece um relatório que tem informações completas sobre VMs compatíveis e incompatíveis, discos por VM e variação de dados por disco. A ferramenta também resume os requisitos de largura de banda de rede para atender ao RPO de destino e à infraestrutura do Azure necessária para replicação e failover de teste bem-sucedidos.

## <a name="capacity-considerations"></a>Considerações sobre capacidade

Componente | Detalhes
--- | ---
**Replicação** | **Taxa de alteração diária máxima**: um computador protegido pode usar apenas um servidor de processo. Um único servidor de processo é capaz de gerir uma taxa de alteração diária de até 2 TB. Portanto, 2 TB é a taxa de alteração de dados diária máxima com suporte para um computador protegido.<br /><br /> **Taxa de transferência máxima**: uma máquina replicada pode pertencer a uma conta de armazenamento no Azure. Uma conta de armazenamento padrão do Azure pode lidar com um máximo de 20.000 solicitações por segundo. Recomendamos que você limite o número de IOPS (operações de entrada/saída por segundo) em um computador de origem para 20.000. Por exemplo, se você tiver um computador de origem que tenha cinco discos e cada disco gerar 120 IOPS (8 K de tamanho) no computador de origem, o computador de origem estará dentro do limite de 500 de IOPS por disco do Azure. (O número de contas de armazenamento necessárias é igual ao IOPS de máquina de origem total dividido por 20.000.)
**Servidor de configuração** | O servidor de configuração deve ser capaz de lidar com a capacidade de taxa de alteração diária em todas as cargas de trabalho em execução em computadores protegidos. O computador de configuração deve ter largura de banda suficiente para replicar continuamente os dados para o armazenamento do Azure.<br /><br /> Uma prática recomendada é posicionar o servidor de configuração na mesma rede e no mesmo segmento de LAN que os computadores que você deseja proteger. Você pode posicionar o servidor de configuração em uma rede diferente, mas as máquinas que você deseja proteger devem ter visibilidade de rede de camada 3.<br /><br /> As recomendações de tamanho para o servidor de configuração são resumidas na tabela na seção a seguir.
**Servidor de processos** | O primeiro servidor de processo é instalado por padrão no servidor de configuração. Você pode implantar servidores de processo adicionais para dimensionar seu ambiente. <br /><br /> O servidor de processo recebe dados de replicação de computadores protegidos. O servidor de processo otimiza os dados usando caching, compactação e criptografia. Em seguida, o servidor de processo envia os dados para o Azure. O computador do servidor de processo deve ter recursos suficientes para executar essas tarefas.<br /><br /> O servidor de processo usa um cache baseado em disco. Use um disco de cache separado de 600 GB ou mais para lidar com alterações de dados que são armazenadas se ocorrer um afunilamento de rede ou uma interrupção.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Recomendações de tamanho para o servidor de configuração e o servidor de processo embutido

Um servidor de configuração que usa um servidor de processo embutido para proteger a carga de trabalho pode lidar com até 200 máquinas virtuais com base nas seguintes configurações:

CPU | Memória | Tamanho do disco de cache | Taxa de alteração de dados | Máquinas protegidas
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | Use para replicar menos de 100 computadores.
12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB a 1 TB | Use para replicar 100 para computadores 150.
16 vCPUs (2 soquetes * 8 núcleos \@ 2,5 GHz) | 32 GB | 1 TB | > 1 TB a 2 TB | Use para replicar 151 para computadores 200.
Implante outro servidor de configuração usando um [modelo OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). | | | | Implante um novo servidor de configuração se você estiver replicando mais de 200 computadores.
Implante outro [servidor de processo](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | > 2 TB| Implante um novo servidor de processo de expansão se a taxa diária geral de alteração de dados for maior que 2 TB.

Nessas configurações:

* Cada computador de origem tem três discos de 100 GB cada.
* Usamos o armazenamento de benchmark de oito unidades de assinatura de acesso compartilhado de 10 K RPM com RAID 10 para medições de disco de cache.

## <a name="size-recommendations-for-the-process-server"></a>Recomendações de tamanho para o servidor de processo

O servidor de processo é o componente que manipula a replicação de dados em Azure Site Recovery. Se a taxa de alteração diária for maior que 2 TB, você deverá adicionar servidores de processo de expansão para lidar com a carga de replicação. Para escalar horizontalmente, você pode:

* Aumente o número de servidores de configuração implantando usando um [modelo OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). Por exemplo, você pode proteger até 400 computadores usando dois servidores de configuração.
* Adicionar [servidores de processo de expansão](vmware-azure-set-up-process-server-scale.md#download-installation-file). Use os servidores de processo de expansão para lidar com o tráfego de replicação em vez de (ou além de) o servidor de configuração.

A tabela a seguir descreve esse cenário:

* Você configura um servidor de processo de expansão.
* Você configurou máquinas virtuais protegidas para usar o servidor de processo de expansão.
* Cada computador de origem protegido tem três discos de 100 GB cada.

Servidor de processo adicional | Tamanho do disco de cache | Taxa de alteração de dados | Máquinas protegidas
--- | --- | --- | ---
4 vCPUs (2 soquetes * 2 núcleos \@ 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Use para replicar 85 ou menos computadores.
8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz), 12 GB de memória | 600 GB | 251 GB a 1 TB | Use para replicar 86 para computadores 150.
12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz) 24 GB de memória | 1 TB | > 1 TB a 2 TB | Use para replicar 151 para computadores 225.

A maneira como você dimensiona seus servidores depende de sua preferência por um modelo de expansão ou expansão. Para escalar verticalmente, implante alguns servidores de configuração de ponta e servidores de processo. Para escalar horizontalmente, implante mais servidores que tenham menos recursos. Por exemplo, se você quiser proteger computadores 200 com uma taxa de alteração diária geral de dados de 1,5 TB, poderá executar uma das seguintes ações:

* Configure um único servidor de processo (16 vCPU, 24 GB de RAM).
* Configure dois servidores de processo (2 x 8 vCPU, 2 * 12 GB de RAM).

## <a name="control-network-bandwidth"></a>Controlar largura de banda da rede

Depois de usar [Site Recovery planejador de implantações](site-recovery-deployment-planner.md) para calcular a largura de banda necessária para a replicação (replicação inicial e, em seguida, o Delta), você tem algumas opções para controlar a quantidade de largura de banda usada para replicação:

* **Limitação de largura de banda**: o tráfego do VMware que Replica para o Azure passa por um servidor de processo específico. Você pode limitar a largura de banda nos computadores que estão sendo executados como servidores de processo.
* **Influenciar a largura de banda**: você pode influenciar a largura de banda usada para replicação usando algumas chaves do registro:
  * O valor do registro **HKEY_LOCAL_MACHINE \Software\microsoft\windows do Backup\Replication\UploadThreadsPerVM do Azure** especifica o número de threads usados para transferência de dados (replicação inicial ou Delta) de um disco. Um valor mais alto aumenta a largura de banda de rede usada para replicação.
  * O valor do registro **HKEY_LOCAL_MACHINE \Software\microsoft\windows do Backup\Replication\DownloadThreadsPerVM do Azure** especifica o número de threads usados para transferência de dados durante o failback.

### <a name="throttle-bandwidth"></a>Limitar largura de banda

1. Abra o snap-in MMC do backup do Azure no computador que você usa como servidor de processo. Por padrão, um atalho para o backup está disponível na área de trabalho ou na seguinte pasta: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. No snap-in, selecione **alterar propriedades**.

    ![Captura de tela da opção de snap-in do MMC do backup do Azure para alterar propriedades](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na guia **limitação** , selecione **habilitar limitação de uso de largura de banda da Internet para operações de backup**. Defina os limites de horas de trabalho e de folga. Intervalos válidos são de 512 kbps a 1.023 Mbps.

    ![Captura de tela da caixa de diálogo Propriedades do backup do Azure](./media/site-recovery-vmware-to-azure/throttle2.png)

Também pode utilizar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para configurar a limitação. Segue-se um exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que não é necessária nenhuma limitação.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Alterar a largura de banda de rede para uma VM

1. No registro da VM, vá para **HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Replication**.
   * Para alterar o tráfego de largura de banda em um disco de replicação, modifique o valor de **UploadThreadsPerVM**. Crie a chave se ela não existir.
   * Para alterar a largura de banda para o tráfego de failback do Azure, modifique o valor de **DownloadThreadsPerVM**.
2. O valor padrão de cada chave é **4**. Numa rede “sobreaprovisionada”, os valores predefinidos destas chaves de registo devem ser alterados. O valor máximo que você pode usar é **32**. Monitorize o tráfego para otimizar o valor.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Configurar a infraestrutura de Site Recovery para proteger mais de 500 VMs

Antes de configurar a infraestrutura de Site Recovery, acesse o ambiente para medir os seguintes fatores: máquinas virtuais compatíveis, a taxa diária de alteração de dados, a largura de banda de rede necessária para o RPO que você deseja obter, o número de Site Recovery componentes que são necessários e o tempo necessário para concluir a replicação inicial. Conclua as etapas a seguir para coletar as informações necessárias:

1. Para medir esses parâmetros, execute Site Recovery Planejador de Implantações em seu ambiente. Para obter diretrizes úteis, consulte [sobre Site Recovery planejador de implantações para VMware no Azure](site-recovery-deployment-planner.md).
2. Implante um servidor de configuração que atenda às [recomendações de tamanho para o servidor de configuração](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Se sua carga de trabalho de produção exceder 650 máquinas virtuais, implante outro servidor de configuração.
3. Com base na taxa de alteração de dados diária medida, implante os [servidores de processo de expansão](vmware-azure-set-up-process-server-scale.md#download-installation-file) com a ajuda de diretrizes de [tamanho](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Se você esperar que a taxa de alteração de dados de uma máquina virtual de disco exceda 2 MBps, certifique-se de usar discos gerenciados Premium. Site Recovery Planejador de Implantações é executado por um período de tempo específico. Os picos na taxa de alteração de dados em outras ocasiões podem não ser capturados no relatório.
5. [Defina a largura de banda da rede](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) com base no RPO que você deseja obter.
6. Quando a infraestrutura é configurada, habilite a recuperação de desastre para sua carga de trabalho. Para saber como, consulte [Configurar o ambiente de origem para replicação do VMware para o Azure](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Implantar servidores de processo adicionais

Se você expandir sua implantação para além de 200 computadores de origem ou se tiver uma taxa de rotatividade diária total de mais de 2 TB, será necessário adicionar servidores de processo para lidar com o volume de tráfego. Aprimoramos o produto na versão 9,24 para fornecer [alertas do servidor de processo](vmware-physical-azure-monitor-process-server.md#process-server-alerts) sobre quando configurar um servidor de processo de expansão. [Configure o servidor de processo](vmware-azure-set-up-process-server-scale.md) para proteger novos computadores de origem ou [Equilibre a carga](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar computadores para usar o novo servidor de processo

1. Selecione **configurações** > **servidores de site Recovery**. Selecione o servidor de configuração e expanda **servidores de processo**.

    ![Captura de tela da caixa de diálogo servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Clique com o botão direito do mouse no servidor de processo em uso no momento e selecione **alternar**.

    ![Captura de tela da caixa de diálogo servidor de configuração](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. Em **selecionar servidor de processo de destino**, selecione o novo servidor de processo que você deseja usar. Em seguida, selecione as máquinas virtuais que o servidor vai manipular. Para obter informações sobre o servidor, selecione o ícone de informações. Para ajudá-lo a tomar decisões de carga, é mostrado o espaço médio necessário para replicar cada máquina virtual selecionada para o novo servidor de processo. Selecione a marca de seleção para começar a replicar para o novo servidor de processo.

## <a name="deploy-additional-master-target-servers"></a>Implantar servidores de destino mestre adicionais

Nos cenários a seguir, mais de um servidor de destino mestre é necessário:

*   Você deseja proteger uma máquina virtual baseada em Linux.
*   O servidor de destino mestre disponível no servidor de configuração não tem acesso ao repositório de armazenamento da VM.
*   O número total de discos no servidor de destino mestre (o número de discos locais no servidor mais o número de discos a serem protegidos) é maior que 60 discos.

Para saber como adicionar um servidor de destino mestre para uma máquina virtual baseada em Linux, consulte [instalar um servidor de destino mestre do Linux para failback](vmware-azure-install-linux-master-target.md).

Para adicionar um servidor de destino mestre a uma máquina virtual baseada no Windows:

1. Vá para **cofre dos serviços de recuperação** > **site Recovery infraestrutura** > **servidores de configuração**.
2. Selecione o servidor de configuração necessário e selecione **servidor de destino mestre**.

    ![Captura de tela que mostra o botão Adicionar servidor de destino mestre](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Baixe o arquivo de instalação unificada e, em seguida, execute o arquivo na VM para configurar o servidor de destino mestre.
4. Selecione **instalar destino mestre** > **Avançar**.

    ![Captura de tela que mostra a seleção da opção instalar destino mestre](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Selecione o local de instalação padrão e, em seguida, selecione **instalar**.

     ![Captura de tela que mostra o local de instalação padrão](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Para registrar o destino mestre no servidor de configuração, selecione **prosseguir para a configuração**.

    ![Captura de tela que mostra o botão prosseguir com a configuração](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Insira o endereço IP do servidor de configuração e, em seguida, insira a frase secreta. Para saber como gerar uma senha, consulte [gerar uma senha do servidor de configuração](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Captura de tela que mostra onde inserir o endereço IP e a senha para o servidor de configuração](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Selecione **Registar**. Quando o registro for concluído, selecione **concluir**.

Quando o registro for concluído com êxito, o servidor será listado no portal do Azure no **cofre dos serviços de recuperação** > **site Recovery infraestrutura** > **servidores de configuração**, nos servidores de destino mestre do servidor de configuração.

 > [!NOTE]
 > Baixe a versão mais recente do [arquivo de configuração unificada do servidor de destino mestre para Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Passos seguintes

Baixe e execute [Site Recovery planejador de implantações](https://aka.ms/asr-deployment-planner).
