---
title: Planear a capacidade e dimensionamento para a recuperação de desastre do VMware para o Azure ao utilizar o Azure Site Recovery | Documentos da Microsoft
description: Este artigo pode ajudá-lo a planear a capacidade e dimensionamento ao configurar a recuperação após desastre de VMs de VMware para o Azure com o Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 8325e2d1dccf1184c5297a60161200b41fc1d412
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338285"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Planear a capacidade e dimensionamento para recuperação de desastre do VMware para o Azure

Utilize este artigo para planear a capacidade e dimensionamento ao replicar VMs de VMware no local e servidores físicos para o Azure, utilizando [do Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Como iniciar planeamento de capacidade?

Para saber mais sobre os requisitos de infraestrutura do Azure Site Recovery, recolha informações sobre o ambiente de replicação, executando [do Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) para a replicação de VMware. Para obter mais informações, consulte [sobre Site Recovery Deployment Planner, de VMware para o Azure](site-recovery-deployment-planner.md). 

Site Recovery Deployment Planner fornece um relatório que tenha informações completas sobre VMs compatíveis e incompatíveis, discos por VM, e alterações a dados por disco. A ferramenta também resume os requisitos de largura de banda de rede para satisfazer o RPO de destino e a infraestrutura do Azure necessários para ativação pós-falha com êxito de replicação e teste.

## <a name="capacity-considerations"></a>Considerações sobre capacidade

Componente | Detalhes
--- | ---
**Replicação** | **Taxa de alteração diária máximo**: Uma máquina protegida, pode utilizar apenas um servidor de processos. Um servidor de processos único pode lidar com um valor diário máximo de 2 TB de taxa de alteração. Por isso, a 2 TB é que os máxima diários de dados alterar a velocidade a que é suportada para uma máquina protegida.<br /><br /> **Débito máximo**: Uma máquina replicada pode pertencer a uma conta de armazenamento no Azure. Uma conta de armazenamento do Azure standard pode processar um máximo de 20 000 pedidos por segundo. Recomendamos que limite o número de operações de entrada/saída por segundo (IOPS) numa máquina de origem a 20 000. Por exemplo, se tiver uma máquina de origem que tenha cinco discos e cada disco gera 120 IOPS (8 K de tamanho) na máquina de origem, a máquina de origem é dentro do limite IOPS por disco do Azure de 500. (O número de contas de armazenamento necessárias é igual à máquina de origem total IOPS dividido por 20.000.)
**Servidor de configuração** | O servidor de configuração tem de ser capaz de lidar com a capacidade de taxa de alteração diária em todas as cargas de trabalho em execução nas máquinas protegidas. A máquina de configuração tem de ter largura de banda suficiente para replicar continuamente os dados ao armazenamento do Azure.<br /><br /> Uma prática recomendada é colocar o servidor de configuração na mesma rede e segmento de LAN que as máquinas que pretende proteger. Pode colocar o servidor de configuração numa rede diferente, mas as máquinas que pretende proteger devem ter visibilidade da camada 3 de rede.<br /><br /> Recomendações de tamanho para o servidor de configuração estão resumidas na tabela na secção seguinte.
**Servidor de processos** | O primeiro servidor de processos está instalado por predefinição no servidor de configuração. Pode implementar servidores de processos adicionais para dimensionar o seu ambiente. <br /><br /> O servidor de processos recebe dados de replicação de máquinas protegidas. O servidor de processos otimiza os dados ao utilizar a colocação em cache, compressão e encriptação. Em seguida, o servidor de processos envia os dados para o Azure. A máquina do servidor de processo tem de ter recursos suficientes para executar estas tarefas.<br /><br /> O servidor de processo utiliza um cache em disco. Utilize um disco de cache separado de 600 GB ou mais para processar alterações de dados que estão armazenadas se ocorrer um afunilamento de rede ou de indisponibilidade.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Servidor de processos de recomendações de tamanho para o servidor de configuração e incorporadas

Um servidor de configuração que utiliza um servidor de processos incorporadas para proteger a carga de trabalho pode processar até 200 máquinas virtuais com base nas seguintes configurações:

CPU | Memória | Tamanho do disco de cache | Taxa de alteração de dados | Máquinas protegidas
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 núcleos \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | Utilize para replicar máquinas menos de 100.
12 vCPUs (2 sockets * 6 núcleos \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB a 1 TB | Utilize para replicar máquinas de 100 a 150.
16 vCPUs (2 sockets * 8 núcleos \@ 2,5 GHz) | 32 GB | 1 TB | > 1 TB para 2 TB | Utilize para replicar máquinas 151 e 200.
Implementar noutro servidor de configuração com um [o modelo OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). | | | | Implemente um novo servidor de configuração, se estiver a replicar mais de 200 máquinas.
Implementar outro [servidor de processos](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Implemente um novo servidor de processos de escalamento horizontal, se a taxa de alteração de dados diária geral for superior a 2 TB.

Nestas configurações:

* Cada máquina de origem tem três discos de 100 GB cada.
* Nós usamos armazenamento benchmark de oito unidades de assinatura de acesso partilhado de 10 mil RPM com RAID 10 para medidas de disco de cache.

## <a name="size-recommendations-for-the-process-server"></a>Recomendações de tamanho para o servidor de processos

O servidor de processos é o componente que processa a replicação de dados no Azure Site Recovery. Se a taxa de alteração diária for maior que 2 TB, tem de adicionar servidores de processos de escalamento horizontal para processar a carga de replicação. Para aumentar horizontalmente, pode:

* Aumentar o número de servidores de configuração ao implementar através de um [o modelo OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). Por exemplo, pode proteger até 400 máquinas ao utilizar dois servidores de configuração.
* Adicione [servidores de processos de escalamento horizontal](vmware-azure-set-up-process-server-scale.md#download-installation-file). Utilizar os servidores de processos de escalamento horizontal para processar o tráfego de replicação em vez de (ou além de) o servidor de configuração.

A tabela seguinte descreve este cenário:

* Configurar um servidor de processos de escalamento horizontal.
* Configurar máquinas virtuais protegidas para utilizar o servidor de processos de escalamento horizontal.
* Cada máquina de origem protegida tem três discos de 100 GB cada.

Servidor de processos adicionais | Tamanho do disco de cache | Taxa de alteração de dados | Máquinas protegidas
--- | --- | --- | ---
4 vCPUs (2 sockets * 2 núcleos \@ 2.5ghz), 8 GB de memória | 300 GB | 250 GB ou menos | Utilize para replicar máquinas 85 ou menos.
8 vCPUs (2 sockets * 4 núcleos \@ 2.5ghz), 12 GB de memória | 600 GB | 251 GB a 1 TB | Utilize para replicar máquinas 86 a 150.
12 vCPUs (2 sockets * 6 núcleos \@ 2.5ghz) 24 GB de memória | 1 TB | > 1 TB para 2 TB | Utilize para replicar máquinas 151 a 225.

Como dimensionar os seus servidores depende da sua preferência num modelo de aumento vertical ou horizontal. Para aumentar verticalmente, implementar alguns servidores de configuração de ponta e processar os servidores. Para aumentar horizontalmente, implemente mais servidores que têm menos recursos. Por exemplo, se pretender proteger de 200 máquinas com um geral taxa de 1,5 TB de alteração de dados diária, pode demorar uma das seguintes ações:

* Configure um servidor de processo único (16 vCPU, 24 GB de RAM).
* Configure dois servidores de processos (2 x 8 vCPU, 2 * 12 GB de RAM).

## <a name="control-network-bandwidth"></a>Largura de banda de rede de controlo

Depois de utilizar [Site Recovery Deployment Planner](site-recovery-deployment-planner.md) para calcular a largura de banda precisa para a replicação (replicação inicial e, em seguida, o delta), tem duas opções para controlar a quantidade de largura de banda que é utilizada para replicação:

* **Limitar largura de banda**: Tráfego de VMware que é replicado para o Azure vai através de um servidor de processo específico. Pode limitar a largura de banda nas máquinas que estão sendo executados como servidores de processos.
* **Influenciar a largura de banda**: Pode influenciar a largura de banda que é utilizada para replicação por através de dois chaves do Registro:
  * O **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** valor de registo Especifica o número de threads que são utilizados para a transferência de dados (replicação inicial ou delta) de um disco. Um valor mais alto aumenta a largura de banda de rede que é utilizada para replicação.
  * O **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** valor de registo Especifica o número de threads que são utilizados para transferência de dados durante a reativação pós-falha.

### <a name="throttle-bandwidth"></a>Limitar largura de banda

1. Abra o snap-in MMC de cópia de segurança do Azure na máquina que é usar como o servidor de processos. Por predefinição, um atalho para cópia de segurança está disponível no ambiente de trabalho ou na seguinte pasta: C:\Program Files\Microsoft Agent\bin de serviços de recuperação do Azure.
2. No snap-in, selecione **alterar propriedades**.

    ![Captura de ecrã da opção de snap-in do MMC de cópia de segurança do Azure para alterar as propriedades](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Sobre o **limitação** separador, selecione **ativar a limitação para operações de cópia de segurança de utilização de largura de banda de internet**. Defina os limites para o trabalho e de descanso. Intervalos válidos são entre 512 Kbps 1,023 Mbps.

    ![Captura de ecrã da caixa de diálogo de propriedades de cópia de segurança do Azure](./media/site-recovery-vmware-to-azure/throttle2.png)

Também pode utilizar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para configurar a limitação. Segue-se um exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que não é necessária nenhuma limitação.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Alterar a largura de banda de rede para uma VM

1. No registo da VM, aceda a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Para alterar o tráfego de largura de banda num disco de replicação, modifique o valor de **UploadThreadsPerVM**. Se não existir, crie a chave.
   * Para alterar a largura de banda para o tráfego de reativação pós-falha do Azure, modifique o valor de **DownloadThreadsPerVM**.
2. O valor predefinido para cada chave é **4**. Numa rede “sobreaprovisionada”, os valores predefinidos destas chaves de registo devem ser alterados. O que pode utilizar o valor máximo é **32**. Monitorize o tráfego para otimizar o valor.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Configurar a infraestrutura de recuperação de sites para proteger mais de 500 VMs

Antes de configurar a infraestrutura de recuperação de sites, aceder ao ambiente para medir os seguintes fatores: máquinas virtuais compatíveis, os dados diários de alterar a velocidade, a largura de banda de rede necessária para que o RPO que quer atingir o número de recuperação de Site componentes necessários e o tempo que demora a concluir a replicação inicial. Conclua os passos seguintes para recolher as informações necessárias:

1. Para medir esses parâmetros, execute o Site Recovery Deployment Planner no seu ambiente. Para obter diretrizes úteis, consulte [sobre Site Recovery Deployment Planner, de VMware para o Azure](site-recovery-deployment-planner.md).
2. Implementar um servidor de configuração que cumpra os [recomendações de tamanho de servidor de configuração](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Se a carga de trabalho de produção exceder 650 máquinas de virtuais, implemente noutro servidor de configuração.
3. Com base na taxa de medida alterações de dados diária, implemente [servidores de processos de escalamento horizontal](vmware-azure-set-up-process-server-scale.md#download-installation-file) com a ajuda do [diretrizes de tamanho](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Se pretender que os dados alterados tarifa para uma máquina virtual de disco exceda 2 MBps, certifique-se de que utiliza discos geridos premium. Site Recovery Deployment Planner é executado durante um período de tempo específico. Não podem ser capturados picos na taxa de alteração de dados noutras alturas no relatório.
5. [Definir a largura de banda de rede](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) com base no RPO quer atingir.
6. Quando a infraestrutura estiver configurada, ative a recuperação após desastre para a sua carga de trabalho. Para saber como, veja [configurar o ambiente de origem para VMware para replicação do Azure](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Implementar servidores de processos adicionais

Se aumentar horizontalmente a implementação, além de 200 máquinas de origem, ou se tiver um total de alterações diariamente a taxa de mais de 2 TB, tem de adicionar servidores de processos para processar o volume de tráfego. Para saber como configurar o servidor de processos, veja [escala para reativação pós-falha ao utilizar servidores de processos adicionais](vmware-azure-set-up-process-server-scale.md). Depois de configurar o servidor de processos, pode migrar máquinas de origem para utilizá-lo.

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar máquinas para utilizar o novo servidor de processos

1. Selecione **configurações** > **servidores de Site Recovery**. Selecione o servidor de configuração e, em seguida, expanda **processar servidores**.

    ![Captura de ecrã da caixa de diálogo de servidor de processos](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. O servidor de processos atualmente em utilização com o botão direito e, em seguida, selecione **comutador**.

    ![Captura de ecrã da caixa de diálogo de servidor de configuração](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. Na **servidor de processos de destino selecione**, selecione o novo servidor de processos que pretende utilizar. Em seguida, selecione as máquinas virtuais que o servidor irá processar. Para obter informações sobre o servidor, selecione o ícone de informações. Para ajudar a tomar decisões de carga, o espaço médio é necessária para replicar a cada máquina virtual selecionada para o novo servidor de processo é mostrado. Selecione a marca de verificação para começar a replicar para o novo servidor de processo.

## <a name="deploy-additional-master-target-servers"></a>Implementar servidores de destino mestre adicionais

Nos seguintes cenários, mais de um servidor de destino mestre é necessário:

*   Que pretende proteger uma máquina de virtual baseado em Linux.
*   O servidor de destino principal disponível no servidor de configuração não tem acesso para o arquivo de dados da VM.
*   O número total de discos no servidor de destino mestre (o número de discos locais no servidor) mais o número de discos para ser protegida é superior a 60 discos.

Para saber como adicionar um servidor de destino mestre para uma máquina de virtual baseado em Linux, veja [instalar um servidor de destino principal do Linux para reativação pós-falha](vmware-azure-install-linux-master-target.md).

Para adicionar um servidor de destino mestre para uma máquina de virtual baseado no Windows:

1. Aceda a **cofre dos serviços de recuperação** > **infraestrutura do Site Recovery** > **servidores de configuração**.
2. Selecione o servidor de configuração necessárias e, em seguida, selecione **servidor de destino mestre**.

    ![Captura de ecrã que mostra o botão Adicionar servidor de destino mestre](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Transfira o ficheiro de configuração unificada e, em seguida, execute o ficheiro na VM para configurar o servidor de destino mestre.
4. Selecione **instalação de destino mestre** > **seguinte**.

    ![Captura de ecrã que mostra a seleção da opção de destino mestre de instalação](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Selecione a localização de instalação predefinida e, em seguida, selecione **instalar**.

     ![Captura de ecrã que mostra a localização de instalação predefinida](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Para registar o destino principal com o servidor de configuração, selecione **continuar a configuração**.

    ![Captura de ecrã que mostra o continue para o botão de configuração](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Introduza o endereço IP do servidor de configuração e, em seguida, introduza a frase de acesso. Para saber como gerar uma frase de acesso, veja [gerar uma frase de acesso do servidor de configuração](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Captura de ecrã que mostra onde pode introduzir o endereço IP e a frase de acesso para o servidor de configuração](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Selecione **Registar**. Quando o registo estiver concluído, selecione **concluir**.

Quando concluir o registo com êxito, o servidor está listado no portal do Azure em **cofre dos serviços de recuperação** > **infraestrutura do Site Recovery**  >   **Servidores de configuração**, nos servidores de destino principal do servidor de configuração.

 > [!NOTE]
 > Baixe a versão mais recente dos [ficheiro de configuração unificada do servidor de destino mestre para Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Passos Seguintes

Transfira e execute [Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).
