---
title: Disponibilidade do SAP HANA dentro de uma região do Azure | Documentos da Microsoft
description: Descreve as operações de SAP HANA em VMs nativas do Azure numa região do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 687012e73b4b0c869b491ac1c9ea128662b23510
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64680880"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Disponibilidade do SAP HANA dentro de uma região do Azure
Este artigo descreve os vários cenários de disponibilidade numa região do Azure. O Azure tem muitas regiões, espalhados por todo o mundo. Para obter a lista de regiões do Azure, veja [regiões do Azure](https://azure.microsoft.com/regions/). Para implementar o SAP HANA nas VMs dentro de uma região do Azure, a Microsoft oferece a implementação de uma única VM com uma instância do HANA. Para maior disponibilidade, pode implementar duas VMs com duas instâncias do HANA dentro de um [conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) que utiliza a replicação de sistema do HANA para disponibilidade. 

Atualmente, o Azure está oferecendo [zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Este artigo descreve as zonas de disponibilidade em detalhes. No entanto, ele inclui uma discussão geral sobre como utilizar conjuntos de disponibilidade em comparação com as zonas de disponibilidade.

Regiões do Azure em que as zonas de disponibilidade são oferecidas tem vários datacenters. Os datacenters são independentes na prestação de fonte de energia, refrigeração e rede. É o motivo para a oferta zonas diferentes numa única região do Azure implementar aplicações em duas ou três zonas de disponibilidade que são oferecidos. Implementar entre zonas, emite no Power BI e rede que afetam apenas uma infraestrutura de zona de disponibilidade do Azure, a implementação da aplicação dentro de uma região do Azure ainda está funcional. Poderão ocorrer alguma capacidade reduzida. Por exemplo, VMs numa zona poderão perder-se, mas as VMs em duas outras zonas ainda estaria em execução. 
 
Um conjunto de disponibilidade do Azure é uma capacidade de agrupamento lógico que o ajuda a garantir que os recursos VM que colocar num conjunto de disponibilidade estão falha isoladas entre si quando são implementados num datacenter do Azure. O Azure garante que as VMs que colocar num Conjunto de Disponibilidade são executadas em vários servidores físicos, suportes de computação, unidades de armazenamento e comutadores de rede. Em alguma documentação do Azure, esta configuração é referida como posicionamentos em diferentes [domínios de atualização e com falha](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Normalmente, são estes posicionamentos dentro de um datacenter do Azure. Supondo que os problemas de rede e fonte de alimentação afetaria o Centro de dados que estão sendo implantados, todos os seus capacidade numa única região do Azure é afetada.

A colocação dos centros de dados que representam as zonas de disponibilidade do Azure é um compromisso entre a entrega de latência de rede aceitável entre serviços implementados em diferentes zonas e uma distância entre os datacenters. Catástrofes naturais ideal é que não afetam o poder, o fornecimento de rede e a infraestrutura para todas as zonas de disponibilidade nesta região. No entanto, como tem mostrado monumental catástrofes naturais, zonas de disponibilidade poderá não fornecer sempre a disponibilidade que pretende numa região. Considere a Maria furacão que atingem ilha de Porto Rico, 20 de Setembro de 2017. O furacão basicamente causou uma indisponibilidade quase 100 por cento na ilha quilómetro-toda a 90.

## <a name="single-vm-scenario"></a>Cenário de VM única

Um cenário de VM única, vai criar uma VM do Azure para a instância do SAP HANA. Utilize o armazenamento Premium do Azure para alojar o disco do sistema operativo e todos os seus discos de dados. O tempo de atividade do Azure, o SLA de 99,9 por cento e os SLAs de outros componentes do Azure é suficiente para que atenda a suas SLAs de disponibilidade para os seus clientes. Neste cenário, precisa sem a necessidade de tirar partido de um conjunto de disponibilidade do Azure para VMs que executam a camada do DBMS. Neste cenário, contar com duas funcionalidades diferentes:

- Azure VM reinício automático (também referido como a recuperação do serviço do Azure)
- Reinício automático do SAP HANA

Reinício de automática VM do Azure, ou a recuperação do serviço, é uma funcionalidade do Azure que funciona em dois níveis:

- O anfitrião do servidor do Azure verifica o estado de funcionamento de uma VM que está alojado no anfitrião do servidor.
- O controlador de malha do Azure monitoriza o estado de funcionamento e a disponibilidade do anfitrião do servidor.

Uma funcionalidade de verificação de estado de funcionamento monitoriza o estado de funcionamento de cada VM que está alojado num anfitrião server do Azure. Se uma VM pertence a um mau estado, um reinício da VM pode ser iniciado pelo agente de anfitrião do Azure que verifica o estado de funcionamento da VM. O controlador de malha verifica o estado de funcionamento do anfitrião, a verificação de muitos parâmetros diferentes que podem indicar problemas com o hardware anfitrião. Ele também verifica a acessibilidade do anfitrião através da rede. Uma indicação de problemas com o host pode levar aos seguintes eventos:

- Se o anfitrião sinaliza um Estado de funcionamento incorreto, serão disparados um reinício do anfitrião e um reinício de VMs que estavam em execução no anfitrião.
- Se o anfitrião não está em bom estado de funcionamento após a reinicialização bem sucedida, é iniciada uma nova implementação de VMs que faziam originalmente no nó agora mau estado de funcionamento para um servidor de anfitrião em bom estado. Neste caso, o anfitrião original é marcado como estado de funcionamento incorreto. Isso não será usado para implementações mais até que ele tem limpos ou substituído.
- Se o host problemático tem problemas durante o processo de reinicialização, é acionado um reinício imediato de VMs num anfitrião em bom estado. 

Com o anfitrião e a monitorização de VM fornecida pelo Azure, as VMs do Azure que ocorrerem problemas de anfitrião são reiniciadas automaticamente num anfitrião do Azure em bom estado. 

>[!IMPORTANT]
>Recuperação do serviço do Azure não irá reiniciar as VMs do Linux em que o SO convidado é num estado entre em pânico do kernel. As predefinições das versões de Linux frequentemente utilizadas, não são reiniciadas automaticamente VMs ou servidor em que o kernel de Linux está no estado entre em pânico. Em vez disso, a predefinição foresees para manter o sistema operacional no estado entre em pânico do kernel para conseguir anexar um depurador de kernel para analisar. Azure está a respeitar esse comportamento reiniciando automaticamente uma VM com o SO convidado num de um Estado. Suposição é que tais ocorrências são extremamente raras. Poderia substituir o comportamento predefinido para permitir um reinício da VM. Para alterar o padrão de comportamento ativar o parâmetro kernel.panic no /etc/sysctl.conf. O tempo definido para este parâmetro é em segundos. Valores recomendados comuns estão a aguardar 20 a 30 segundos antes de acionar o reinício por este parâmetro. Consulte também <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>.

O segundo recurso que se baseiam neste cenário é o fato de que o serviço HANA que é executado numa VM reiniciada inicia automaticamente após a VM é reiniciada. Pode configurar [auto-reiniciar o serviço do HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) através dos serviços de watchdog dos vários serviços do HANA.

Poderá melhorar neste cenário de VM única ao adicionar um nó de ativação pós-falha frio para uma configuração de SAP HANA. Na documentação do SAP HANA, esta configuração denomina [alojar a ativação pós-falha automática](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Esta configuração poderá fazer sentido numa situação de implementação no local onde o hardware de servidor é limitado e que dedica um nó de servidor único, como o anfitrião do nó de ativação pós-falha automática para um conjunto de anfitriões de produção. Mas, no Azure, onde a infra-estrutura subjacente do Azure fornece um servidor de destino de bom estado de funcionamento para um reinício VM com êxito, não faz sentido para implementar o SAP HANA anfitrião-ativação pós-falha automática. Por motivo de recuperação do serviço do Azure, não existe nenhum arquitetura de referência que foresees um nó em modo de espera para HANA anfitrião-ativação pós-falha automática. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Caso especial de configurações de escalamento horizontal do SAP HANA no Azure
Elevada disponibilidade para as configurações de escalamento horizontal do SAP HANA é a entidade confiadora no serviço de recuperação de VMs do Azure e o reinício da instância do SAP HANA, como a VM está a funcionar e executar novamente. Arquiteturas de alta disponibilidade com base no HANA System Replication vão ser introduzido numa altura posterior. 


## <a name="availability-scenarios-for-two-different-vms"></a>Cenários de disponibilidade para duas VMs diferentes

Se utilizar duas VMs do Azure dentro de um conjunto de disponibilidade do Azure, pode aumentar o tempo de atividade entre estas duas VMs se estas são colocadas num conjunto de disponibilidade do Azure numa região do Azure. A configuração base no Azure teria o seguinte aspeto:

![Diagrama de duas VMs com todas as camadas](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Para ilustrar os cenários de disponibilidade diferentes, algumas das camadas no diagrama são omitidas. O diagrama mostra apenas as camadas que retratar VMs, anfitriões, conjuntos de disponibilidade e regiões do Azure. Instâncias, grupos de recursos e subscrições de rede Virtual do Azure não têm uma função nos cenários descritos nesta secção.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replicar as cópias de segurança para uma segunda máquina virtual

Um das configurações mais rudimentares é utilizar cópias de segurança. Em particular, talvez tenha backups de log de transação enviados a partir de uma VM para outra VM do Azure. Pode escolher o tipo de armazenamento do Azure. Nessa configuração, é responsável por scripts a cópia de cópias de segurança agendadas realizadas na primeira VM para a segunda VM. Se precisar de utilizar as segundo instâncias VM, tem de restaurar o completas, incrementais/diferenciais e backups de log de transação para o ponto de que precisa. 

A arquitetura é semelhante a:

![Diagrama de duas VMs com a replicação de armazenamento](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Esta configuração não é adequada para alcançar o excelentes horas de objetivo de ponto de recuperação (RPO) e objetivo de tempo de recuperação (RTO). RTO vezes especialmente seriam afetado negativamente devido à necessidade de restaurar completamente o banco de dados completo com as cópias de segurança copiadas. No entanto, esta configuração é útil para recuperar a partir de eliminação de dados não-intencionais nas instâncias da principais. Com esta configuração, em qualquer altura, pode restaurar para um determinado ponto no tempo, extrair os dados e importar os dados eliminados para a instância principal. Por conseguinte, poderá fazer sentido usar um método de cópia de segurança em combinação com outras funcionalidades de elevada disponibilidade. 

Enquanto as cópias de segurança estão a ser copiadas, poderá conseguir utilizar uma VM mais pequena do que a VM principal que a instância do SAP HANA está a executar. Tenha em atenção que pode anexar um número menor de VHDs para VMs mais pequenas. Para obter informações sobre os limites de tipos VM individuais, consulte [tamanhos de máquinas de virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replicação do sistema de SAP HANA sem failover automático

Os cenários descritos nesta secção utilizam a replicação de sistema do SAP HANA. Para a documentação do SAP, consulte [replicação de sistema](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Cenários sem failover automático não são comuns para configurações dentro de uma região do Azure. Uma configuração sem failover automático, embora evitando uma configuração de Pacemaker, obligates-na monitorizar e a ativação pós-falha manualmente. Como este demora e esforços também, a maioria dos clientes confiam no serviço do Azure em vez disso, a recuperação. Existem alguns casos extremos em que esta configuração pode ajudar em termos de cenários de falha. Em alternativa, em alguns casos, um cliente pode perceber mais eficiência.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>Replicação do sistema de SAP HANA sem ativação pós-falha automática e sem o pré-carregamento de dados

Neste cenário, vai utilizar a replicação de sistema do SAP HANA para mover dados de forma síncrona para alcançar um RPO de 0. Por outro lado, tem um longa o suficiente RTO que não precisa de ativação pós-falha ou dados pré-carregamento para a cache de instância do HANA. Neste caso, é possível obter ainda mais economia em sua configuração, efetuando as seguintes ações:

- Execute outra instância do SAP HANA na VM segundo. A instância do SAP HANA na VM segundo leva a maior parte da memória da máquina virtual. No caso de uma ativação pós-falha para a segunda VM, terá de encerrar a instância do SAP HANA em execução que tem os dados totalmente carregados na segunda VM, para que os dados replicados podem ser carregados para a cache da instância HANA direcionada na segunda VM.
- Utilize um tamanho VM mais pequeno noutra. Se ocorrer uma ativação pós-falha, terá uma etapa adicional antes da ativação pós-falha manual. Neste passo, redimensione a VM para o tamanho da VM de origem. 
 
O cenário é semelhante a:

![Diagrama de duas VMs com a replicação de armazenamento](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Mesmo se não utilizar o pré-carregamento de dados no destino de replicação de sistema HANA, tem de, pelo menos, 64 GB de memória. Também tem memória suficiente para além de 64 GB para manter os dados de rowstore na memória da instância de destino.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>Replicação do sistema de SAP HANA sem ativação pós-falha automática e com o pré-carregamento de dados

Neste cenário, os dados que são replicados para a instância HANA na segunda VM é pré-carregado. Isso elimina duas vantagens de pré-carregamento não de dados. Neste caso, não é possível executar outro sistema de SAP HANA noutra. Também não é possível utilizar um tamanho VM mais pequeno. Por conseguinte, os clientes raramente implementar este cenário.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replicação do sistema de SAP HANA com ativação pós-falha automática

No padrão e mais comuns a configuração de disponibilidade numa região do Azure, duas VMs do Azure em execução no Linux do SLES tem um cluster de ativação pós-falha definido. O cluster do Linux do SLES se baseia a [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) framework, em conjunto com um [STONITH](http://linux-ha.org/wiki/STONITH) dispositivo. 

A partir de um ponto de vista do SAP HANA, o modo de replicação que é utilizado está sincronizado e uma ativação pós-falha automática está configurada. Na segunda VM, a instância do SAP HANA atua como um nó em modo de espera ativo. O nó em modo de espera recebe um fluxo síncrono de registos de alteração da instância principal do SAP HANA. Como as transações são confirmadas pela aplicação no nó principal do HANA, o nó principal do HANA aguarda para confirmar a consolidação para o aplicativo até que o nó secundário do SAP HANA confirma que ele recebeu o registo de consolidação. SAP HANA oferece dois modos de replicação síncrona. Para obter detalhes e uma descrição das diferenças entre esses dois modos de replicação síncrona, consulte o artigo SAP [modos de replicação para a replicação de sistema do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

A configuração geral é semelhante a:

![Diagrama de duas VMs com a replicação de armazenamento e de ativação pós-falha](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Poderá escolher esta solução porque ela permite-lhe alcançar um RPO = 0 e um RTO baixo. Configure a conectividade de cliente do SAP HANA, para que os clientes de SAP HANA utilizam o endereço IP virtual para ligar à configuração de replicação de sistema do HANA. Essa configuração elimina a necessidade de reconfigurar o aplicativo se ocorrer uma ativação pós-falha para o nó secundário. Neste cenário, os SKUs de VM do Azure para a VM principal e secundária tem de ser o mesmo.

## <a name="next-steps"></a>Passos Seguintes

Para obter orientações passo a passo sobre como configurar estas configurações no Azure, consulte:

- [Configurar a replicação de sistema de SAP HANA em VMs do Azure](sap-hana-high-availability.md)
- [Elevada disponibilidade para SAP HANA através de replicação de sistema](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Para obter mais informações sobre a disponibilidade do SAP HANA em regiões do Azure, consulte:

- [Disponibilidade do SAP HANA em regiões do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

