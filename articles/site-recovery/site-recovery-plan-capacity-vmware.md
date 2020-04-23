---
title: Capacidade de plano para recuperação de desastres vMware com recuperação do site Azure
description: Este artigo pode ajudá-lo a planear a capacidade e a escala quando configura a recuperação de VMware VMs para Azure utilizando a Recuperação do Site Azure.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 467c70a722b8a243be6ac2826188a4ba3459aa06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257618"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Capacidade de plano e escala para recuperação de desastres vMware para Azure

Utilize este artigo para planear a capacidade e a escala quando replicar vMware VMs e servidores físicos no Local para o Azure utilizando a Recuperação do [Site Azure](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Como começo a planear a capacidade?

Para conhecer os requisitos de infraestrutura de recuperação de sítios do Azure, recolha informações sobre o seu ambiente de replicação, executando o Planejador de Implementação de Recuperação de [Sítios Azure](https://aka.ms/asr-deployment-planner-doc) para a replicação de VMware. Para mais informações, consulte sobre o Planejador de Implementação de Recuperação do [Site para VMware para O Azure](site-recovery-deployment-planner.md). 

O Planificador de Implementação de Recuperação do Site fornece um relatório que tem informações completas sobre VMs compatíveis e incompatíveis, discos por VM e churn de dados por disco. A ferramenta também resume os requisitos de largura de banda da rede para atender o RPO-alvo e a infraestrutura Azure que é necessária para a replicação bem sucedida e falha de teste.

## <a name="capacity-considerations"></a>Considerações de capacidade

Componente | Detalhes
--- | ---
**Replicação** | **Taxa máxima de variação diária**: Uma máquina protegida pode utilizar apenas um servidor de processo. Um único servidor de processo é capaz de gerir uma taxa de alteração diária de até 2 TB. Então, 2 TB é a taxa máxima diária de mudança de dados que é suportada para uma máquina protegida.<br /><br /> **Potência máxima**: Uma máquina replicada pode pertencer a uma conta de armazenamento em Azure. Uma conta de armazenamento azure padrão pode lidar com um máximo de 20.000 pedidos por segundo. Recomendamos que limite o número de operações de entrada/saída por segundo (IOPS) através de uma máquina de origem para 20.000. Por exemplo, se tiver uma máquina de origem que tenha cinco discos e cada disco gere 120 IOPS (8 K de tamanho) na máquina de origem, a máquina de origem está dentro do limite de IOPS Azure por disco de 500. (O número de contas de armazenagem exigidas é igual ao total da máquina de origem IOPS dividida por 20.000.)
**Servidor de configuração** | O servidor de configuração deve ser capaz de lidar com a capacidade diária de mudança em todas as cargas de trabalho em máquinas protegidas. A máquina de configuração deve ter largura de banda suficiente para replicar continuamente os dados para o Armazenamento Azure.<br /><br /> A melhor prática é colocar o servidor de configuração na mesma rede e segmento LAN que as máquinas que pretende proteger. Pode colocar o servidor de configuração numa rede diferente, mas as máquinas que pretende proteger devem ter visibilidade de rede da camada 3.<br /><br /> As recomendações de tamanho para o servidor de configuração são resumidas na tabela na seguinte secção.
**Servidor de processos** | O primeiro servidor de processo é instalado por padrão no servidor de configuração. Pode implementar servidores de processos adicionais para escalar o seu ambiente. <br /><br /> O servidor de processos recebe dados de replicação de máquinas protegidas. O servidor de processo otimiza os dados utilizando o cache, a compressão e a encriptação. Em seguida, o servidor de processo envia os dados para o Azure. A máquina do servidor de processos deve dispor de recursos suficientes para executar estas tarefas.<br /><br /> O servidor de processos utiliza uma cache baseada em disco. Utilize um disco de cache separado de 600 GB ou mais para lidar com as alterações de dados que são armazenadas se ocorrer um estrangulamento ou falha de rede.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Recomendações de tamanho para o servidor de configuração e servidor de processo incorporado

Um servidor de configuração que utiliza um servidor de processo incorporado para proteger a carga de trabalho pode lidar com até 200 máquinas virtuais com base nas seguintes configurações:

CPU | Memória | Tamanho do disco cache | Taxa de alteração de dados | Máquinas protegidas
--- | --- | --- | --- | ---
8 vCPUs (2 tomadas \@ * 4 núcleos 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | Use para replicar menos de 100 máquinas.
12 vCPUs (2 tomadas \@ * 6 núcleos 2,5 GHz) | 18 GB | 600 GB | 501 GB a 1 TB | Utilize para replicar 100 a 150 máquinas.
16 vCPUs (2 tomadas \@ * 8 núcleos 2,5 GHz) | 32 GB | 1 TB | >1 TB a 2 TB | Utilize para replicar 151 a 200 máquinas.
Implemente outro servidor de configuração utilizando um [modelo OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). | | | | Implemente um novo servidor de configuração se estiver a replicar mais de 200 máquinas.
Implementar outro servidor de [processo](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Implemente um novo servidor de processo sem escala se a taxa global de mudança de dados diária for superior a 2 TB.

Nestas configurações:

* Cada máquina de origem tem três discos de 100 GB cada.
* Usamos o armazenamento de benchmarking de oito unidades de assinatura de acesso partilhado de 10 K RPM com RAID 10 para medições de cache disk.

## <a name="size-recommendations-for-the-process-server"></a>Recomendações de tamanho para o servidor de processos

O servidor de processo é o componente que trata a replicação de dados na Recuperação do Site Azure. Se a taxa de variação diária for superior a 2 TB, deve adicionar servidores de processo saldados para lidar com a carga de replicação. Para escalar, pode:

* Aumente o número de servidores de configuração implementando utilizando um [modelo OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). Por exemplo, pode proteger até 400 máquinas utilizando dois servidores de configuração.
* Adicione [servidores de processo sintetização.](vmware-azure-set-up-process-server-scale.md#download-installation-file) Utilize os servidores de processo saldados para lidar com o tráfego de replicação em vez de (ou além de) o servidor de configuração.

A tabela seguinte descreve este cenário:

* Criaste um servidor de processo supérno.
* Configurou máquinas virtuais protegidas para utilizar o servidor de processo sem escalas.
* Cada máquina de origem protegida tem três discos de 100 GB cada.

Servidor de processo adicional | Tamanho do disco cache | Taxa de alteração de dados | Máquinas protegidas
--- | --- | --- | ---
4 vCPUs (2 tomadas \@ * 2 núcleos 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Utilize para replicar 85 ou menos máquinas.
8 vCPUs (2 tomadas \@ * 4 núcleos 2,5 GHz), 12 GB de memória | 600 GB | 251 GB a 1 TB | Utilize para replicar 86 a 150 máquinas.
12 vCPUs (2 tomadas \@ * 6 núcleos 2,5 GHz) 24 GB de memória | 1 TB | >1 TB a 2 TB | Utilize para replicar 151 a 225 máquinas.

A forma como escala os seus servidores depende da sua preferência por um modelo de escala ou escala. Para aumentar a escala, implemente alguns servidores de configuração topo de gama e servidores de processos. Para escalar, implementar mais servidores com menos recursos. Por exemplo, se quiser proteger 200 máquinas com uma taxa global diária de alteração de dados de 1,5 TB, poderá tomar uma das seguintes ações:

* Instale um único servidor de processo (16 vCPU, 24 GB de RAM).
* Configurar dois servidores de processo (2 x 8 vCPU, 2* 12 GB de RAM).

## <a name="control-network-bandwidth"></a>Largura de banda da rede de controlo

Depois de utilizar o [Planificador](site-recovery-deployment-planner.md) de Implantação de Recuperação do Site para calcular a largura de banda de que necessita para a replicação (replicação inicial e depois o delta), tem algumas opções para controlar a quantidade de largura de banda que é usada para a replicação:

* **Largura de banda do acelerador**: O tráfego VMware que se replica para o Azure passa por um servidor de processo específico. Pode acelerar a largura de banda nas máquinas que estão a funcionar como servidores de processos.
* **Largura de banda de influência:** Pode influenciar a largura de banda que é usada para a replicação utilizando um par de teclas de registo:
  * The **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** registry value specifies the number of threads that are used for data transfer (initial or delta replication) of a disk. Um valor mais elevado aumenta a largura de banda da rede que é usada para a replicação.
  * The **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** registry value specifies the number of threads that are used for data transfer during failback.

### <a name="throttle-bandwidth"></a>Limitar largura de banda

1. Abra o encaixe de MMC de backup Azure na máquina que utiliza como servidor de processo. Por predefinição, um atalho para Backup está disponível no ambiente de trabalho ou na seguinte pasta: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. No snap-in, selecione **Change Properties**.

    ![Screenshot da opção de snap-in do Azure Backup MMC para alterar propriedades](./media/site-recovery-vmware-to-azure/throttle1.png)
3. No separador **Throttling,** **selecione Ativar**o estrangulamento da largura de banda da Internet para operações de backup . Estabeleça os limites para o trabalho e o horário de trabalho. As gamas válidas são de 512 Kbps a 1.023 Mbps.

    ![Screenshot da caixa de diálogo Azure Backup Properties](./media/site-recovery-vmware-to-azure/throttle2.png)

Também pode utilizar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para configurar a limitação. Segue-se um exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que não é necessária nenhuma limitação.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Altere a largura de banda da rede para um VM

1. No registo do VM, vá a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Para alterar o tráfego de largura de banda num disco de replicação, modifique o valor do **UploadThreadsPerVM**. Crie a chave se não existir.
   * Para alterar a largura de banda para o tráfego de failback do Azure, modifique o valor do **DownloadThreadsPerVM**.
2. O valor predefinido para cada tecla é de **4**. Numa rede “sobreaprovisionada”, os valores predefinidos destas chaves de registo devem ser alterados. O valor máximo que pode utilizar é **de 32**. Monitorize o tráfego para otimizar o valor.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Criar a infraestrutura de recuperação do site para proteger mais de 500 VMs

Antes de configurar a infraestrutura de recuperação do site, aceda ao ambiente para medir os seguintes fatores: máquinas virtuais compatíveis, a taxa diária de alteração de dados, a largura de banda de rede necessária para o RPO que pretende alcançar, o número de componentes de Recuperação do Site que são necessários e o tempo necessário para completar a replicação inicial. Complete os seguintes passos para recolher as informações necessárias:

1. Para medir estes parâmetros, execute o Planejador de Implantação de Recuperação do Local no seu ambiente. Para obter diretrizes úteis, consulte sobre o Planejador de Implementação de Recuperação do [Site para VMware para O Azure](site-recovery-deployment-planner.md).
2. Implemente um servidor de configuração que satisfaça as recomendações de [tamanho para o servidor de configuração](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Se a sua carga de trabalho de produção exceder 650 máquinas virtuais, implante outro servidor de configuração.
3. Com base na taxa de alteração diária de dados medida, implemente [servidores de processos de escala](vmware-azure-set-up-process-server-scale.md#download-installation-file) com a ajuda de [diretrizes](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)de tamanho .
4. Se espera que a taxa de alteração de dados para uma máquina virtual de disco exceda 2 MBps, certifique-se de que utiliza discos geridos por prémios. O Planejador de Implantação de Recuperação do Site funciona por um período de tempo específico. Os picos na taxa de variação de dados noutras alturas podem não ser captados no relatório.
5. [Defina a largura de banda da rede](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) com base no RPO que pretende alcançar.
6. Quando a infraestrutura estiver configurada, permita a recuperação de desastres para a sua carga de trabalho. Para saber como, consulte [Configurar o ambiente de origem para vMware para a replicação do Azure](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Implementar servidores de processos adicionais

Se dimensionar a sua implementação para além de 200 máquinas de origem ou se tiver uma taxa diária total de mais de 2 TB, deve adicionar servidores de processo para lidar com o volume de tráfego. Melhorámos o produto na versão 9.24 para fornecer alertas de [servidor estoma](vmware-physical-azure-monitor-process-server.md#process-server-alerts) do processo sobre quando configurar um servidor de processo sem escala. [Instale o servidor de processo](vmware-azure-set-up-process-server-scale.md) para proteger novas máquinas de origem ou equilibrar a [carga](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar máquinas para usar o novo servidor de processos

1. Selecione os servidores de**recuperação**do site de **definições** > . Selecione o servidor de configuração e, em seguida, expandir **os servidores de processo**.

    ![Screenshot da caixa de diálogo do Servidor de Processos](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Clique no servidor de processo sintetizador atualmente em uso e, em seguida, selecione **Switch**.

    ![Screenshot da caixa de diálogo do servidor de configuração](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. No servidor de **processo de alvo Selecione**o novo servidor de processo que pretende utilizar. Em seguida, selecione as máquinas virtuais que o servidor irá manusear. Para obter informações sobre o servidor, selecione o ícone de informação. Para ajudá-lo a tomar decisões de carga, é mostrado o espaço médio necessário para replicar cada máquina virtual selecionada para o novo servidor de processos. Selecione a marca de verificação para começar a replicar-se no novo servidor de processos.

## <a name="deploy-additional-master-target-servers"></a>Implementar servidores de alvo principal adicionais

Nos seguintes cenários, é necessário mais de um servidor-alvo principal:

*   Queres proteger uma máquina virtual baseada em Linux.
*   O servidor-alvo principal disponível no servidor de configuração não tem acesso à loja de dados do VM.
*   O número total de discos no servidor alvo principal (o número de discos locais no servidor mais o número de discos a proteger) é superior a 60 discos.

Para aprender a adicionar um servidor de alvo principal para uma máquina virtual baseada em Linux, consulte [Instalar um servidor-alvo principal do Linux para o failback](vmware-azure-install-linux-master-target.md).

Para adicionar um servidor de alvo principal para uma máquina virtual baseada no Windows:

1. Vá para os servidores de configuração de configuração de**infraestrutura** > de recuperação do site > de recuperação de**serviços**de **recuperação.**
2. Selecione o servidor de configuração necessário e, em seguida, selecione **Master Target Server**.

    ![Screenshot que mostra o botão Adicionar Master Target Server](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Descarregue o ficheiro de configuração unificado e, em seguida, execute o ficheiro no VM para configurar o servidor alvo principal.
4. **Selecione Instalar o alvo** > principal**A seguir**.

    ![Screenshot que mostra a seleção da opção de alvo principal de instalação](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Selecione a localização de instalação predefinida e, em seguida, **selecione Instalar**.

     ![Screenshot que mostra a localização padrão de instalação](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Para registar o alvo principal com o servidor de configuração, selecione **'Proceder à Configuração**' .

    ![Screenshot que mostra o botão "Proceder à Configuração"](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Introduza o endereço IP do servidor de configuração e introduza a frase de passe. Para aprender a gerar uma frase de passe, consulte Gerar uma frase de passe do servidor de [configuração](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Screenshot que mostra onde introduzir o endereço IP e a frase-passe para o servidor de configuração](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Selecione **Registar**. Quando a inscrição estiver terminada, selecione **Terminar**.

Quando o registo termina com sucesso, o servidor está listado no portal Azure nos servidores de configuração de configuração de**infraestruturade recuperação** > do **site de** > recuperação de**serviços**de recuperação, nos servidores-alvo principal do servidor de configuração.

 > [!NOTE]
 > Descarregue a versão mais recente do [ficheiro de configuração unificado](https://aka.ms/latestmobsvc)do servidor alvo principal para windows .

## <a name="next-steps"></a>Passos seguintes

Descarregue e execute [o planejador](https://aka.ms/asr-deployment-planner)de implementação de recuperação do site.
