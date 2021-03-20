---
title: Capacidade do plano para recuperação de desastres VMware com recuperação do local de Azure
description: Este artigo pode ajudá-lo a planear a capacidade e a escala quando configurar a recuperação de VMware VMs para Azure utilizando a Recuperação do Site Azure.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 4b86d0c189bcf0687a703f2338188df2090feaf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368031"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Capacidade do plano e escala para recuperação de desastres VMware para Azure

Utilize este artigo para planear a capacidade e a escala quando replicar no local VMware VMs e servidores físicos para Azure utilizando [a Recuperação do Site Azure](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Como começo a planear capacidades?

Para saber mais sobre os requisitos de infraestrutura de recuperação do local de Azure, recolha informações sobre o seu ambiente de replicação executando [o Planejador de Implementação de Recuperação do Site Azure](./site-recovery-deployment-planner.md) para a replicação de VMware. Para obter mais informações, consulte [sobre o Planejador de Implementação de Recuperação de Sítios para VMware a Azure](site-recovery-deployment-planner.md). 

O Planejador de Implementação de Recuperação de Sítios fornece um relatório que dispõe de informações completas sobre VMs compatíveis e incompatíveis, discos por VM e dados por disco. A ferramenta também resume os requisitos de largura de banda de rede para cumprir o RPO alvo e a infraestrutura Azure que é necessária para replicação bem sucedida e falha de teste.

## <a name="capacity-considerations"></a>Considerações de capacidade

Componente | Detalhes
--- | ---
**Replicação** | **Taxa máxima de variação diária**: Uma máquina protegida só pode utilizar um servidor de processo. Um único servidor de processo é capaz de gerir uma taxa de alteração diária de até 2 TB. Assim, 2 TB é a taxa máxima de mudança de dados diária que é suportada para uma máquina protegida.<br /><br /> **Produção máxima**: Uma máquina replicada pode pertencer a uma conta de armazenamento em Azure. Uma conta padrão de Armazenamento Azure pode suportar um máximo de 20.000 pedidos por segundo. Recomendamos que limite o número de operações de entrada/saída por segundo (IOPS) através de uma máquina de origem para 20.000. Por exemplo, se tiver uma máquina de origem que tenha cinco discos e cada disco gerar 120 IOPS (8 K de tamanho) na máquina de origem, a máquina de origem está dentro do limite de IOPS por disco Azure de 500. (O número de contas de armazenamento exigidas é igual ao total da máquina de origem IOPS dividida por 20.000.)
**Servidor de configuração** | O servidor de configuração deve ser capaz de lidar com a capacidade de variação diária em todas as cargas de trabalho em funcionamento em máquinas protegidas. A máquina de configuração deve ter largura de banda suficiente para replicar continuamente os dados para o Azure Storage.<br /><br /> Uma boa prática é colocar o servidor de configuração na mesma rede e segmento LAN que as máquinas que pretende proteger. Pode colocar o servidor de configuração numa rede diferente, mas as máquinas que pretende proteger devem ter visibilidade de rede de camada 3.<br /><br /> As recomendações de tamanho para o servidor de configuração são resumidas na tabela na seguinte secção.
**Servidor de processo** | O primeiro servidor de processo é instalado por predefinição no servidor de configuração. Pode implementar servidores de processo adicionais para escalar o seu ambiente. <br /><br /> O servidor de processo recebe dados de replicação de máquinas protegidas. O servidor de processo otimiza os dados utilizando o caching, a compressão e a encriptação. Em seguida, o servidor de processo envia os dados para Azure. A máquina do servidor de processos deve ter recursos suficientes para executar estas tarefas.<br /><br /> O servidor de processo utiliza uma cache baseada em disco. Utilize um disco de cache separado de 600 GB ou mais para lidar com as alterações de dados que são armazenadas se ocorrer um estrangulamento ou falha de rede.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Recomendações de tamanho para o servidor de configuração e servidor de processo incorporado

Um servidor de configuração que utiliza um servidor de processo incorporado para proteger a carga de trabalho pode manusear até 200 máquinas virtuais com base nas seguintes configurações:

CPU | Memória | Tamanho do disco cache | Taxa de alteração de dados | Máquinas protegidas
--- | --- | --- | --- | ---
8 vCPUs (2 tomadas * 4 núcleos \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | Use para replicar menos de 100 máquinas.
12 vCPUs (2 tomadas * 6 núcleos \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB a 1 TB | Utilize para replicar 100 a 150 máquinas.
16 vCPUs (2 tomadas * 8 núcleos \@ 2,5 GHz) | 32 GB | 1 TB | >1 TB a 2 TB | Utilize para replicar 151 a 200 máquinas.
Implemente outro servidor de configuração utilizando um [modelo OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). | | | | Implemente um novo servidor de configuração se estiver a replicar mais de 200 máquinas.
Implementar outro [servidor de processo](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Implemente um novo servidor de processo de escala se a taxa de alteração global dos dados diário for superior a 2 TB.

Nestas configurações:

* Cada máquina de origem tem três discos de 100 GB cada.
* Usamos armazenamento de benchmarking de oito unidades de assinatura de acesso compartilhado de 10 K RPM com RAID 10 para medições de disco de cache.

## <a name="size-recommendations-for-the-process-server"></a>Recomendações de tamanho para o servidor de processo

O servidor de processo é o componente que trata a replicação de dados na Recuperação do Site Azure. Se a taxa de variação diária for superior a 2 TB, deve adicionar servidores de processo de escala para lidar com a carga de replicação. Para escalar, pode:

* Aumente o número de servidores de configuração através da implementação através de um [modelo OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). Por exemplo, pode proteger até 400 máquinas utilizando dois servidores de configuração.
* Adicionar [servidores de processo de escala](vmware-azure-set-up-process-server-scale.md#download-installation-file). Utilize os servidores de processo de escala para lidar com o tráfego de replicação em vez de (ou além de) o servidor de configuração.

A tabela a seguir descreve este cenário:

* Configura um servidor de processo de escala.
* Configuraste máquinas virtuais protegidas para utilizar o servidor de processo de escala.
* Cada máquina de origem protegida tem três discos de 100 GB cada.

Servidor de processo adicional | Tamanho do disco cache | Taxa de alteração de dados | Máquinas protegidas
--- | --- | --- | ---
4 vCPUs (2 tomadas * 2 núcleos \@ 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Utilize para replicar 85 ou menos máquinas.
8 vCPUs (2 tomadas * 4 núcleos \@ 2,5 GHz), 12 GB de memória | 600 GB | 251 GB a 1 TB | Utilize para replicar 86 a 150 máquinas.
12 vCPUs (2 tomadas * 6 núcleos \@ 2,5 GHz) 24 GB de memória | 1 TB | >1 TB a 2 TB | Utilize para replicar 151 a 225 máquinas.

A forma como escala os seus servidores depende da sua preferência por um modelo de escala ou escala. Para escalar, implemente alguns servidores de configuração de alta qualidade e servidores de processo. Para escalar, implementar mais servidores que tenham menos recursos. Por exemplo, se quiser proteger 200 máquinas com uma taxa de alteração global de dados diária de 1,5 TB, poderá tomar uma das seguintes ações:

* Configurar um único servidor de processo (16 vCPU, 24 GB de RAM).
* Configurar dois servidores de processo (2 x 8 vCPU, 2* 12 GB de RAM).

## <a name="control-network-bandwidth"></a>Largura de banda da rede de controlo

Depois de utilizar [o Planificador de Implementação de Recuperação](site-recovery-deployment-planner.md) do Site para calcular a largura de banda de que necessita para a replicação (replicação inicial e, em seguida, o delta), tem algumas opções para controlar a quantidade de largura de banda que é usada para a replicação:

* **Largura de banda de aceleração**: O tráfego de VMware que se replica para Azure passa por um servidor de processo específico. Pode acelerar a largura de banda nas máquinas que estão a funcionar como servidores de processo.
* **Influência largura de banda**: Pode influenciar a largura de banda que é usada para a replicação usando um par de chaves de registo:
  * O valor de registo **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** especifica o número de fios que são utilizados para transferência de dados (replicação inicial ou delta) de um disco. Um valor mais elevado aumenta a largura de banda da rede que é usada para a replicação.
  * O **valor de** registoHKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVMespecifica o número de fios que são utilizados para transferência de dados durante o failback.

### <a name="throttle-bandwidth"></a>Limitar largura de banda

1. Abra o encaixe MMC de backup Azure na máquina que utiliza como servidor de processo. Por predefinição, está disponível um atalho para cópia de segurança no ambiente de trabalho ou na seguinte pasta: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. No snap-in, selecione **Change Properties**.

    ![Screenshot da opção snap-in do Azure Backup MMC para alterar propriedades](./media/site-recovery-vmware-to-azure/throttle1.png)
3. No **separador Throttling,** selecione **Ativar o estrangulamento da largura de banda da Internet para operações de backup**. Definir os limites para o horário de trabalho e não-trabalho. Os intervalos válidos são de 512 Kbps a 1.023 Mbps.

    ![Screenshot da caixa de diálogo Azure Backup Properties](./media/site-recovery-vmware-to-azure/throttle2.png)

Também pode utilizar o cmdlet [Set-OBMachineSetting](/previous-versions/windows/powershell-scripting/hh770409(v=wps.640)) para configurar a limitação. Eis um exemplo:

```azurepowershell-interactive
$mon = [System.DayOfWeek]::Monday
$tue = [System.DayOfWeek]::Tuesday
Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)
```

**Set-OBMachineSetting -NoThrottle** indica que não é necessária nenhuma limitação.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Altere a largura de banda da rede para um VM

1. No registo do VM, vá a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication.**
   * Para alterar o tráfego de largura de banda num disco de replicação, modifique o valor do **UploadThreadsPerVM**. Crie a chave se não existir.
   * Para alterar a largura de banda para tráfego de recuo da Azure, modifique o valor do **DownloadThreadsPerVM**.
2. O valor predefinido para cada tecla é **4**. Numa rede “sobreaprovisionada”, os valores predefinidos destas chaves de registo devem ser alterados. O valor máximo que pode utilizar é **de 32**. Monitorize o tráfego para otimizar o valor.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Criar a infraestrutura de Recuperação de Sítios para proteger mais de 500 VMs

Antes de configurar a infraestrutura de Recuperação de Sítios, aceda ao ambiente para medir os seguintes fatores: máquinas virtuais compatíveis, a taxa de alteração diária de dados, a largura de banda de rede necessária para o RPO que pretende alcançar, o número de componentes de Recuperação de Sítio que são necessários e o tempo necessário para completar a replicação inicial. Preencha as seguintes etapas para recolher as informações necessárias:

1. Para medir estes parâmetros, execute o Planejador de Implementação de Recuperação de Locais no seu ambiente. Para obter orientações úteis, consulte [sobre o Planejador de Implementação de Recuperação de Sítios para VMware a Azure](site-recovery-deployment-planner.md).
2. Implemente um servidor de configuração que satisfaça as recomendações de [tamanho para o servidor de configuração](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Se a sua carga de trabalho de produção exceder 650 máquinas virtuais, implante outro servidor de configuração.
3. Com base na taxa de alteração diária de dados medida, implemente [servidores de processo de escala](vmware-azure-set-up-process-server-scale.md#download-installation-file) com a ajuda de [diretrizes](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)de tamanho .
4. Se espera que a taxa de alteração de dados para uma máquina virtual de disco exceda 2 MBps, certifique-se de que utiliza discos geridos premium. O Planejador de Implementação de Recuperação de Sítio funciona por um período de tempo específico. Os picos na taxa de mudança de dados noutras alturas podem não ser capturados no relatório.
5. [Desaça a largura de banda da rede](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) com base no RPO que pretende alcançar.
6. Quando a infraestrutura estiver configurada, permita a recuperação de desastres para a sua carga de trabalho. Para saber como, consulte [Configurar o ambiente de origem para a replicação de VMware para Azure](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Implementar servidores de processos adicionais

Se reduzir a sua implementação para além de 200 máquinas de origem ou se tiver uma taxa diária total de churn de mais de 2 TB, deve adicionar servidores de processo para lidar com o volume de tráfego. Melhorámos o produto na versão 9.24 para fornecer [alertas de servidor de processo](vmware-physical-azure-monitor-process-server.md#process-server-alerts) sobre quando configurar um servidor de processo de escala. [Configurar o servidor de processo](vmware-azure-set-up-process-server-scale.md) para proteger novas máquinas de origem ou equilibrar a [carga](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar máquinas para usar o novo servidor de processo

1. Selecione **servidores**  >  **de recuperação do site** de definições . Selecione o servidor de configuração e, em seguida, expanda **os servidores de processo**.

    ![Screenshot da caixa de diálogo do Servidor de Processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Clique com o botão direito no servidor de processo atualmente em uso e, em seguida, **selecione Switch**.

    ![Screenshot da caixa de diálogo do servidor de configuração](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. No **servidor de processo de destino Select,** selecione o novo servidor de processo que pretende utilizar. Em seguida, selecione as máquinas virtuais que o servidor irá manusear. Para obter informações sobre o servidor, selecione o ícone de informação. Para ajudá-lo a tomar decisões de carga, é mostrado o espaço médio necessário para replicar cada máquina virtual selecionada para o novo servidor de processo. Selecione a marca de verificação para começar a replicar-se no novo servidor de processo.

## <a name="deploy-additional-master-target-servers"></a>Implementar servidores-alvo principais adicionais

Nos seguintes cenários, é necessário mais de um servidor-alvo principal:

*   Quer proteger uma máquina virtual baseada em Linux.
*   O servidor alvo principal disponível no servidor de configuração não tem acesso à datastore do VM.
*   O número total de discos no servidor alvo principal (o número de discos locais no servidor mais o número de discos a proteger) é superior a 60 discos.

Para aprender a adicionar um servidor-alvo principal para uma máquina virtual baseada em Linux, consulte [instalar um servidor-alvo principal do Linux para falha](vmware-azure-install-linux-master-target.md).

Para adicionar um servidor-alvo principal para uma máquina virtual baseada no Windows:

1. Aceda aos servidores de configuração da infraestrutura do local do **cofre dos serviços de recuperação dos**  >    >  **serviços de recuperação dos serviços de recuperação** do cofre .
2. Selecione o servidor de configuração necessário e, em seguida, selecione **Master Target Server**.

    ![Screenshot que mostra o botão adicionar o servidor do alvo principal](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Descarregue o ficheiro de configuração unificado e, em seguida, execute o ficheiro no VM para configurar o servidor alvo principal.
4. Selecione **Instalar o alvo principal** Em  >  **seguida**.

    ![Screenshot que mostra a seleção da opção principal de instalação](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Selecione a localização de instalação predefinitiva e, em seguida, **selecione Instalar**.

     ![Screenshot que mostra a localização de instalação padrão](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Para registar o alvo principal com o servidor de configuração, selecione **Proceder à Configuração**.

    ![Screenshot que mostra o botão Proceder à Configuração](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Introduza o endereço IP do servidor de configuração e, em seguida, introduza a palavra-passe. Para aprender a gerar uma palavra-passe, consulte [gerar uma palavra-passe do servidor de configuração](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Screenshot que mostra onde introduzir o endereço IP e a frase de passagem para o servidor de configuração](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Selecione **Registar**. Quando a inscrição estiver concluída, **selecione Terminar**.

Quando o registo termina com sucesso, o servidor é listado no portal Azure nos **servidores**  >  de Configuração **de Infraestruturas de Recuperação do Local de Recuperação** de  >  **Serviços** de Recuperação de Serviços de Recuperação de Serviços de Recuperação , nos servidores-alvo principais do servidor de configuração.

 > [!NOTE]
 > Descarregue a versão mais recente do [ficheiro de configuração unificado](https://aka.ms/latestmobsvc)do servidor alvo principal para o Windows .

## <a name="next-steps"></a>Passos seguintes

Descarregue e execute [o Planejador de Implementação de Recuperação de Sítios](https://aka.ms/asr-deployment-planner).