---
title: SAP HANA disponibilidade em uma região do Azure | Microsoft Docs
description: Descreve SAP HANA operações em VMs nativas do Azure em uma região do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c5b4904419af1fe86e43dc2f781ef43ce8dd762
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078776"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA disponibilidade em uma região do Azure
Este artigo descreve vários cenários de disponibilidade em uma região do Azure. O Azure tem muitas regiões, espalhadas em todo o mundo. Para obter a lista de regiões do Azure, consulte [regiões do Azure](https://azure.microsoft.com/regions/). Para implantar SAP HANA em VMs em uma região do Azure, a Microsoft oferece a implantação de uma única VM com uma instância do HANA. Para aumentar a disponibilidade, você pode implantar duas VMs com duas instâncias do HANA em um [conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) que usa a replicação de sistema do Hana para disponibilidade. 

Atualmente, o Azure está oferecendo [zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Este artigo não descreve Zonas de Disponibilidade em detalhes. Mas inclui uma discussão geral sobre o uso de conjuntos de disponibilidade versus Zonas de Disponibilidade.

As regiões do Azure nas quais Zonas de Disponibilidade são oferecidas têm vários data centers. Os data centers são independentes no fornecimento de fonte de energia, resfriamento e rede. O motivo para oferecer diferentes zonas em uma única região do Azure é implantar aplicativos em dois ou três Zonas de Disponibilidade que são oferecidos. Implantando entre zonas, problemas de energia e rede que afetam apenas uma infraestrutura de zona de disponibilidade do Azure, a implantação de seu aplicativo em uma região do Azure ainda é funcional. Pode ocorrer alguma capacidade reduzida. Por exemplo, as VMs em uma zona podem ser perdidas, mas as VMs nas outras duas zonas ainda estarão ativas e em execução. 
 
Um conjunto de disponibilidade do Azure é um recurso de agrupamento lógico que ajuda a garantir que os recursos de VM que você coloca dentro do conjunto de disponibilidade sejam isolados de falhas uns dos outros quando forem implantados em um datacenter do Azure. O Azure garante que as VMs que colocar num Conjunto de Disponibilidade são executadas em vários servidores físicos, suportes de computação, unidades de armazenamento e comutadores de rede. Em algumas documentações do Azure, essa configuração é conhecida como posicionamento em diferentes [domínios de falha e atualização](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Esses posicionamentos geralmente estão dentro de um datacenter do Azure. Supondo que os problemas de fonte de alimentação e de rede afetem o datacenter que você está implantando, toda a sua capacidade em uma região do Azure seria afetada.

O posicionamento de data centers que representam Zonas de Disponibilidade do Azure é um comprometimento entre a entrega de latência de rede aceitável entre os serviços implantados em diferentes zonas e uma distância entre data centers. As catástrofes naturais idealmente não afetariam a energia, a fonte de rede e a infraestrutura para todos os Zonas de Disponibilidade nessa região. No entanto, como mostrados de catástrofes naturais foram mostradas, Zonas de Disponibilidade nem sempre pode fornecer a disponibilidade desejada em uma região. Pense no furacão Maria que atingiu a ilha de Porto Rico em 20 de setembro de 2017. O furacão basicamente causou um blecaute de aproximadamente 100% na ilha de 90 km.

## <a name="single-vm-scenario"></a>Cenário de VM única

Em um cenário de VM única, você cria uma VM do Azure para a instância de SAP HANA. Use o armazenamento Premium do Azure para hospedar o disco do sistema operacional e todos os discos de dados. O SLA de tempo de atividade do Azure de 99,9% e os SLAs de outros componentes do Azure são suficientes para que você atenda aos seus SLAs de disponibilidade para seus clientes. Nesse cenário, você não precisa aproveitar um conjunto de disponibilidade do Azure para VMs que executam a camada DBMS. Nesse cenário, você conta com dois recursos diferentes:

- Reinicialização automática de VM do Azure (também conhecida como recuperação de serviço do Azure)
- SAP HANA reiniciar automaticamente

A reinicialização automática de VM do Azure ou recuperação de serviço é uma funcionalidade no Azure que funciona em dois níveis:

- O host do servidor do Azure verifica a integridade de uma VM hospedada no host do servidor.
- O controlador de malha do Azure monitora a integridade e a disponibilidade do host do servidor.

Uma funcionalidade de verificação de integridade monitora a integridade de todas as VMs hospedadas em um host do servidor do Azure. Se uma VM se enquadrar em um estado não íntegro, uma reinicialização da VM poderá ser iniciada pelo agente de host do Azure que verifica a integridade da VM. O controlador de malha verifica a integridade do host verificando vários parâmetros diferentes que podem indicar problemas com o hardware do host. Ele também verifica a acessibilidade do host por meio da rede. Uma indicação de problemas com o host pode levar aos seguintes eventos:

- Se o host sinalizar um estado de integridade inadequado, uma reinicialização do host e uma reinicialização das VMs que estavam em execução no host serão disparadas.
- Se o host não estiver em um estado íntegro após a reinicialização bem-sucedida, uma reimplantação das VMs que estavam originalmente no nó não íntegro em um servidor de host íntegro será iniciada. Nesse caso, o host original é marcado como não íntegro. Ele não será usado para implantações adicionais até que seja apagado ou substituído.
- Se o host não íntegro tiver problemas durante o processo de reinicialização, uma reinicialização imediata das VMs em um host íntegro será disparada. 

Com o monitoramento de host e VM fornecido pelo Azure, as VMs do Azure que apresentam problemas de host são reiniciadas automaticamente em um host do Azure íntegro. 

>[!IMPORTANT]
>A recuperação de serviço do Azure não reiniciará as VMs do Linux em que o sistema operacional convidado está em um estado de pane do kernel. As configurações padrão das versões mais usadas do Linux não estão reiniciando automaticamente as VMs ou o servidor onde o kernel do Linux está em estado de pane. Em vez disso, o padrão é previsto para manter o sistema operacional no estado de pane do kernel para poder anexar um depurador de kernel a ser analisado. O Azure está respeitando esse comportamento não reiniciando automaticamente uma VM com o SO convidado em um estado como esse. Suposição que essas ocorrências sejam extremamente raras. Você pode substituir o comportamento padrão para habilitar uma reinicialização da VM. Para alterar o comportamento padrão, habilite o parâmetro ' kernel. pânico ' em/etc/sysctl.conf. A hora que você definir para esse parâmetro é em segundos. Os valores comuns recomendados devem aguardar 20-30 segundos antes de disparar a reinicialização por esse parâmetro. Consulte também <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>.

O segundo recurso que você depende nesse cenário é o fato de que o serviço HANA executado em uma VM reiniciada é iniciado automaticamente após a reinicialização da VM. Você pode configurar a [reinicialização automática do serviço Hana](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) por meio dos serviços de Watchdog dos vários serviços do Hana.

Você pode melhorar esse cenário de VM única adicionando um nó de failover frio a uma configuração de SAP HANA. Na documentação do SAP HANA, essa configuração é chamada de [failover de host automático](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Essa configuração pode fazer sentido em uma situação de implantação local em que o hardware do servidor é limitado e você dedicar um nó de servidor único como o nó de failover automático do host para um conjunto de hosts de produção. Mas no Azure, em que a infraestrutura subjacente do Azure fornece um servidor de destino íntegro para uma reinicialização de VM bem-sucedida, não faz sentido implantar SAP HANA failover automático do host. Devido à recuperação de serviço do Azure, não há nenhuma arquitetura de referência que preveja um nó em espera para o failover automático do host do HANA. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Caso especial de SAP HANA configurações de expansão no Azure
A alta disponibilidade para SAP HANA configurações de expansão está contando com a recuperação de serviço das VMs do Azure e a reinicialização da instância de SAP HANA, já que a VM está em funcionamento novamente. Arquiteturas de alta disponibilidade baseadas na replicação do sistema HANA serão introduzidas posteriormente. 


## <a name="availability-scenarios-for-two-different-vms"></a>Cenários de disponibilidade para duas VMs diferentes

Se você usar duas VMs do Azure em um conjunto de disponibilidade do Azure, poderá aumentar o tempo de atividade entre essas duas VMs se elas forem colocadas em um conjunto de disponibilidade do Azure dentro de uma região do Azure. A configuração básica no Azure teria a seguinte aparência:

![Diagrama de duas VMs com todas as camadas](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Para ilustrar os diferentes cenários de disponibilidade, algumas das camadas no diagrama são omitidas. O diagrama mostra apenas as camadas que descrevem as VMs, os hosts, os conjuntos de disponibilidade e as regiões do Azure. As instâncias de rede virtual do Azure, os grupos de recursos e as assinaturas não desempenham uma função nos cenários descritos nesta seção.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replicar backups para uma segunda máquina virtual

Uma das configurações mais rudimentares é usar backups. Em particular, você pode ter backups de log de transações enviados de uma VM para outra VM do Azure. Você pode escolher o tipo de armazenamento do Azure. Nessa configuração, você é responsável por gerar scripts da cópia de backups agendados que são realizados na primeira VM para a segunda VM. Se você precisar usar as segunda instâncias de VM, deverá restaurar os backups completos, incrementais/diferenciais e de log de transações para o ponto de necessidade. 

A arquitetura é semelhante a:

![Diagrama de duas VMs com replicação de armazenamento](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Essa configuração não é adequada para alcançar ótimos tempos de RPO (objetivo de ponto de recuperação) e RTO (objetivo de tempo de recuperação). Os tempos de RTO, especialmente, seriam prejudicados devido à necessidade de restaurar totalmente o banco de dados completo usando os backups copiados. No entanto, essa configuração é útil para recuperação de exclusão de dados indesejada nas instâncias principais. Com essa configuração, a qualquer momento, você pode restaurar para um determinado ponto no tempo, extrair os dados e importar os dados excluídos para sua instância principal. Portanto, pode fazer sentido usar um método de cópia de backup em combinação com outras funcionalidades de alta disponibilidade. 

Enquanto os backups estão sendo copiados, você pode usar uma VM menor do que a VM principal na qual a instância do SAP HANA está sendo executada. Tenha em mente que você pode anexar um número menor de VHDs a VMs menores. Para obter informações sobre os limites de tipos de VM individuais, consulte [tamanhos de máquinas virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>SAP HANA a replicação do sistema sem failover automático

Os cenários descritos nesta seção usam SAP HANA replicação do sistema. Para obter a documentação do SAP, consulte [replicação do sistema](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Cenários sem failover automático não são comuns para configurações dentro de uma região do Azure. Uma configuração sem failover automático, embora Evite uma configuração pacemaker, obriga você a monitorar e fazer failover manualmente. Como isso também leva e esforços, a maioria dos clientes está contando com a recuperação de serviço do Azure em vez disso. Há alguns casos de borda em que essa configuração pode ajudar em termos de cenários de falha. Ou, em alguns casos, um cliente pode querer obter mais eficiência.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>SAP HANA a replicação do sistema sem failover automático e sem pré-carregamento de dados

Nesse cenário, você usa SAP HANA replicação do sistema para mover dados de forma síncrona para obter um RPO de 0. Por outro lado, você tem um RTO longo o suficiente que não precisa de failover ou de pré-carregamento de dados no cache de instância do HANA. Nesse caso, é possível obter mais economia em sua configuração executando as seguintes ações:

- Execute outra instância de SAP HANA na segunda VM. A instância de SAP HANA na segunda VM leva a maior parte da memória da máquina virtual. No caso de um failover para a segunda VM, você precisa desligar a instância de SAP HANA em execução que tem os dados totalmente carregados na segunda VM, para que os dados replicados possam ser carregados no cache da instância do HANA de destino na segunda VM.
- Use um tamanho de VM menor na segunda VM. Se ocorrer um failover, você terá uma etapa adicional antes do failover manual. Nesta etapa, você redimensiona a VM para o tamanho da VM de origem. 
 
O cenário é semelhante a:

![Diagrama de duas VMs com replicação de armazenamento](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Mesmo que você não use o pré-carregamento de dados no destino de replicação do sistema HANA, será necessário pelo menos 64 GB de memória. Você também precisa de memória suficiente além de 64 GB para manter os dados de armazenamento na memória da instância de destino.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>SAP HANA a replicação do sistema sem failover automático e com o pré-carregamento de dados

Nesse cenário, os dados replicados para a instância do HANA na segunda VM são pré-carregados. Isso elimina as duas vantagens de não carregar dados. Nesse caso, você não pode executar outro sistema de SAP HANA na segunda VM. Você também não pode usar um tamanho de VM menor. Portanto, os clientes raramente implementam esse cenário.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replicação de sistema SAP HANA com failover automático

Na configuração de disponibilidade padrão e mais comum dentro de uma região do Azure, duas VMs do Azure que executam o SLES Linux têm um cluster de failover definido. O cluster SLES Linux é baseado na estrutura [pacemaker](http://www.linux-ha.org/wiki/Pacemaker) , em conjunto com um dispositivo [STONITH](http://linux-ha.org/wiki/STONITH) . 

De uma perspectiva SAP HANA, o modo de replicação usado é sincronizado e um failover automático é configurado. Na segunda VM, a instância de SAP HANA atua como um nó em espera ativa. O nó em espera recebe um fluxo síncrono de registros de alteração da instância de SAP HANA primária. À medida que as transações são confirmadas pelo aplicativo no nó primário do HANA, o nó principal do HANA espera para confirmar a confirmação para o aplicativo até que o nó de SAP HANA secundário confirme que recebeu o registro de confirmação. O SAP HANA oferece dois modos de replicação síncrona. Para obter detalhes e para obter uma descrição das diferenças entre esses dois modos de replicação síncrona, consulte o artigo do SAP [modos de replicação para replicação de sistema do SAP Hana](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

A configuração geral é semelhante a:

![Diagrama de duas VMs com failover e replicação de armazenamento](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Você pode escolher essa solução porque ela permite que você alcance um RPO = 0 e um RTO baixo. Configure o SAP HANA conectividade de cliente para que os SAP HANA clientes usem o endereço IP virtual para se conectar à configuração de replicação do sistema do HANA. Essa configuração elimina a necessidade de reconfigurar o aplicativo se ocorrer um failover para o nó secundário. Nesse cenário, os SKUs de VM do Azure para as VMs primária e secundária devem ser iguais.

## <a name="next-steps"></a>Passos Seguintes

Para obter orientações passo a passo sobre como configurar essas configurações no Azure, consulte:

- [Configurar a replicação do sistema SAP HANA em VMs do Azure](sap-hana-high-availability.md)
- [Alta disponibilidade para SAP HANA usando a replicação do sistema](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Para obter mais informações sobre a disponibilidade de SAP HANA nas regiões do Azure, consulte:

- [Disponibilidade de SAP HANA nas regiões do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

