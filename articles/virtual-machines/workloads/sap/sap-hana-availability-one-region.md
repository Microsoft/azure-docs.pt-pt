---
title: Disponibilidade do SAP HANA dentro de uma região do Azure Microsoft Docs
description: Descreve as operações da SAP HANA em VMs nativos de Azure numa região de Azure.
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
ms.openlocfilehash: a5e4f9853a68b7b4d8b97cc76032cfa88708c097
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842687"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Disponibilidade do SAP HANA dentro de uma região de Azure
Este artigo descreve vários cenários de disponibilidade dentro de uma região de Azure. Azure tem muitas regiões, espalhadas por todo o mundo. Para a lista das regiões de Azure, consulte [as regiões de Azure.](https://azure.microsoft.com/regions/) Para a implementação de SAP HANA em VMs dentro de uma região do Azure, a Microsoft oferece a implementação de um único VM com uma instância HANA. Para uma maior disponibilidade, pode implementar dois VMs com duas instâncias HANA dentro de um conjunto de [disponibilidade Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) que utiliza a replicação do sistema HANA para disponibilidade. 

Atualmente, o Azure está a oferecer Zonas de [Disponibilidade Azure.](https://docs.microsoft.com/azure/availability-zones/az-overview) Este artigo não descreve em detalhe as Zonas de Disponibilidade. Mas inclui uma discussão geral sobre a utilização de Conjuntos de Disponibilidade versus Zonas de Disponibilidade.

As regiões azure onde são oferecidas Zonas de Disponibilidade têm múltiplos datacenters. Os datacenters são independentes no fornecimento de fonte de energia, arrefecimento e rede. A razão para oferecer diferentes zonas dentro de uma única região de Azure é implementar aplicações em duas ou três Zonas de Disponibilidade que são oferecidas. Implantando em zonas, problemas de energia e networking que afetam apenas uma infraestrutura da Zona de Disponibilidade Azure, a sua implementação de aplicações dentro de uma região do Azure ainda está funcional. Pode ocorrer alguma capacidade reduzida. Por exemplo, os VMs numa zona podem perder-se, mas os VMs nas outras duas zonas ainda estariam a funcionar. 
 
Um Conjunto de Disponibilidade Azure é uma capacidade de agrupamento lógica que ajuda a garantir que os recursos VM que coloca dentro do Conjunto de Disponibilidade são isolados de falhas uns dos outros quando são implantados dentro de um datacenter Azure. O Azure garante que as VMs que colocar num Conjunto de Disponibilidade são executadas em vários servidores físicos, suportes de computação, unidades de armazenamento e comutadores de rede. Em alguma documentação do Azure, esta configuração é referida como colocações em diferentes domínios de [atualização e falhas.](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) Estas colocações geralmente estão dentro de um datacenter Azure. Assumindo que a fonte de energia e os problemas de rede afetariam o datacenter que está a implantar, toda a sua capacidade numa região do Azure seria afetada.

A colocação de datacenters que representam as Zonas de Disponibilidade do Azure é um compromisso entre a prestação de latência aceitável da rede entre serviços implantados em diferentes zonas e uma distância entre centros de dados. As catástrofes naturais idealmente não afetariam a energia, o fornecimento de rede e a infraestrutura para todas as Zonas de Disponibilidade desta região. No entanto, como as catástrofes naturais monumentais têm demonstrado, as Zonas de Disponibilidade podem nem sempre fornecer a disponibilidade que você deseja dentro de uma região. Pense no furacão Maria que atingiu a ilha de Porto Rico em 20 de setembro de 2017. O furacão causou um apagão de quase 100% na ilha de 150 km de largura.

## <a name="single-vm-scenario"></a>Cenário de VM único

Num cenário de VM único, cria-se um VM Azure para a instância SAP HANA. Utiliza o Armazenamento Azure Premium para alojar o disco do sistema operativo e todos os seus discos de dados. O SLA de uptime Azure de 99,9 por cento e os SLAs de outros componentes Azure é suficiente para que você cumpra a sua disponibilidade SLAs para os seus clientes. Neste cenário, não é necessário alavancar um Conjunto de Disponibilidade Azure para VMs que executa a camada DBMS. Neste cenário, você confia em duas características diferentes:

- Reinício automático Azure VM (também referido como cura de serviço Azure)
- Reinício automático SAP HANA

O reinício automático Azure VM, ou a cura do serviço, é uma funcionalidade no Azure que funciona em dois níveis:

- O anfitrião do servidor Azure verifica a saúde de um VM que está hospedado no anfitrião do servidor.
- O controlador de tecido Azure monitoriza a saúde e disponibilidade do hospedeiro do servidor.

Uma funcionalidade de verificação de saúde monitoriza a saúde de cada VM que está hospedado num hospedeiro de servidor estoque Azure. Se um VM cair num estado não saudável, um reboot do VM pode ser iniciado pelo agente hospedeiro Azure que verifica a saúde do VM. O controlador de tecido verifica a saúde do hospedeiro verificando muitos parâmetros diferentes que podem indicar problemas com o hardware do hospedeiro. Verifica também a acessibilidade do hospedeiro através da rede. Uma indicação de problemas com o anfitrião pode levar aos seguintes eventos:

- Se o hospedeiro sinalizar um mau estado de saúde, é desencadeado um reinício do hospedeiro e um reinício dos VMs que estavam a decorrer no hospedeiro.
- Se o hospedeiro não estiver em estado saudável após o reboot bem sucedido, é iniciada uma redistribuição dos VMs que estavam originalmente no nó agora pouco saudável para um servidor hospedeiro saudável. Neste caso, o hospedeiro original é marcado como não saudável. Não será usado para mais destacamentos até que seja limpo ou substituído.
- Se o hospedeiro não saudável tiver problemas durante o processo de reinicialização, é desencadeado um reinício imediato dos VMs num hospedeiro saudável. 

Com o hospedeiro e a monitorização VM fornecidas pela Azure, os VMs Azure que experimentam problemas de hospedar são automaticamente reiniciados num anfitrião Azure saudável. 

>[!IMPORTANT]
>A cura do serviço Azure não reiniciará os VMs linux onde o oss o hóspede está em estado de pânico. As definições predefinidas das versões Linux comumente utilizadas não estão a reiniciar automaticamente Os VMs ou servidor onde o kernel Linux está em estado de pânico. Em vez disso, o padrão prevê manter o SO em estado de pânico de kernel para ser capaz de anexar um debugger kernel para analisar. O Azure está a honrar esse comportamento ao não reiniciar automaticamente um VM com o oss o de hóspedes num tal estado. Supõe-se que tais ocorrências são extremamente raras. Pode substituir o comportamento predefinido para permitir o reinício do VM. Para alterar o comportamento predefinido, ative o parâmetro 'kernel.panic' em /etc/sysctl.conf. O tempo definido para este parâmetro é em segundos. Os valores recomendados comuns devem esperar 20-30 segundos antes de desencadear o reinício através deste parâmetro. Consulte também <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>.

A segunda funcionalidade em que confia neste cenário é o facto de o serviço HANA que funciona num VM reiniciado começar automaticamente após o reboot do VM. Pode configurar o reinício automático do [serviço HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) através dos serviços de vigilância dos vários serviços HANA.

Você pode melhorar este cenário de VM único adicionando um nó de falha fria a uma configuração SAP HANA. Na documentação SAP HANA, esta configuração [chama-se auto-failover do anfitrião.](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html) Esta configuração pode fazer sentido numa situação de implementação no local em que o hardware do servidor é limitado, e dedica um nó de um único servidor como o nó de falha automática do anfitrião para um conjunto de anfitriões de produção. Mas em Azure, onde a infraestrutura subjacente do Azure fornece um servidor alvo saudável para um recomeço de VM bem sucedido, não faz sentido implementar o auto-failover do hospedeiro SAP HANA. Devido à cura do serviço Azure, não há arquitetura de referência que preveja um nó de espera para o anfitrião HANA auto-failover. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Caso especial de configurações de escala SAP HANA em Azure
A elevada disponibilidade para configurações de escala SAP HANA está a depender da cura de serviços dos VMs Azure e do reinício da instância SAP HANA, uma vez que o VM está a funcionar novamente. Arquiteturas de alta disponibilidade baseadas na replicação do sistema HANA serão introduzidas mais tarde. 


## <a name="availability-scenarios-for-two-different-vms"></a>Cenários de disponibilidade para dois VMs diferentes

Se utilizar dois VMs Azure dentro de um Conjunto de Disponibilidade Azure, pode aumentar o tempo de uptime entre estes dois VMs se forem colocados num Conjunto de Disponibilidade Azure dentro de uma região azure. A instalação da base em Azure seria como:

![Diagrama de dois VMs com todas as camadas](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Para ilustrar os diferentes cenários de disponibilidade, algumas das camadas do diagrama são omitidas. O diagrama mostra apenas camadas que retratam VMs, anfitriões, conjuntos de disponibilidade e regiões azure. As instâncias da Rede Virtual Azure, grupos de recursos e subscrições não desempenham um papel nos cenários descritos nesta secção.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replicar backups para uma segunda máquina virtual

Uma das configurações mais rudimentares é usar cópias de segurança. Em particular, pode ter cópias de segurança de registo de transações enviadas de um VM para outro VM Azure. Pode escolher o tipo de Armazenamento Azure. Nesta configuração, é responsável por escrever a cópia dos backups programados que são realizados no primeiro VM para o segundo VM. Se precisar de utilizar as segundas instâncias vm, tem de restaurar as cópias de segurança completas, incrementais/diferenciais e de registo de transações ao ponto de que necessita. 

A arquitetura parece:

![Diagrama de dois VMs com replicação de armazenamento](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Esta configuração não é adequada para alcançar os grandes tempos objetivos do ponto de recuperação (RPO) e do Objetivo do Tempo de Recuperação (RTO). Os tempos de RTO sofreriam especialmente devido à necessidade de restaurar totalmente a base de dados completa utilizando as cópias de segurança. No entanto, esta configuração é útil para recuperar da eliminação não intencional de dados nas instâncias principais. Com esta configuração, a qualquer momento, pode restaurar a um determinado ponto do tempo, extrair os dados e importar os dados eliminados para a sua instância principal. Assim, pode fazer sentido usar um método de cópia de cópia de cópia de cópia em combinação com outras funcionalidades de alta disponibilidade. 

Enquanto as cópias de cópias de cópias, poderá utilizar um VM menor do que o VM principal em que a instância SAP HANA está a decorrer. Tenha em mente que pode anexar um número menor de VHDs a VMs menores. Para obter informações sobre os limites dos tipos individuais de VM, consulte [tamanhos para máquinas virtuais Linux em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replicação do sistema SAP HANA sem falha automática

Os cenários descritos nesta secção utilizam a replicação do sistema SAP HANA. Para a documentação SAP, consulte [a replicação do Sistema](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Cenários sem falha automática não são comuns para configurações dentro de uma região do Azure. Uma configuração sem falha automática, embora evitando uma configuração pacemaker, obriga-o a monitorizar e falhar manualmente. Uma vez que isto leva e também os esforços, a maioria dos clientes está a contar com a cura do serviço Azure. Existem alguns casos de vantagem em que esta configuração pode ajudar em termos de cenários de falha. Ou, em alguns casos, um cliente pode querer perceber mais eficiência.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>Replicação do sistema SAP HANA sem falha automática e sem pré-carga de dados

Neste cenário, você usa a replicação do sistema SAP HANA para mover dados de forma sincronizada para obter um RPO de 0. Por outro lado, tem um RTO suficientemente comprido para não precisar de falhas ou de pré-carregamento de dados na cache da instância HANA. Neste caso, é possível alcançar uma economia adicional na sua configuração tomando as seguintes ações:

- Executar outra instância SAP HANA no segundo VM. O caso SAP HANA no segundo VM tira a maior parte da memória da máquina virtual. No caso de uma falha no segundo VM, é necessário desligar a instância SAP HANA em execução que tem os dados totalmente carregados no segundo VM, para que os dados replicados possam ser carregados na cache da instância HANA direcionada no segundo VM.
- Utilize um tamanho VM menor no segundo VM. Se ocorrer uma falha, terá um passo adicional antes da falha manual. Neste passo, redimensiona o VM ao tamanho da fonte VM. 
 
O cenário parece:

![Diagrama de dois VMs com replicação de armazenamento](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Mesmo que não utilize a pré-carga de dados no alvo de replicação do sistema HANA, precisa de pelo menos 64 GB de memória. Também precisa de memória suficiente para além de 64 GB para manter os dados da loja na memória da instância alvo.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>Replicação do sistema SAP HANA sem falha automática e com pré-carga de dados

Neste cenário, os dados que são replicados para a instância HANA no segundo VM estão pré-carregados. Isto elimina as duas vantagens de não pré-carregar dados. Neste caso, não pode executar outro sistema SAP HANA no segundo VM. Também não pode usar um tamanho VM menor. Assim, os clientes raramente implementam este cenário.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replicação do sistema SAP HANA com falha automática

Na configuração padrão e mais comum de disponibilidade dentro de uma região de Azure, dois VMs Azure que executam SLES Linux têm um cluster de failover definido. O cluster SLES Linux baseia-se na estrutura [do Pacemaker,](http://www.linux-ha.org/wiki/Pacemaker) em conjunto com um dispositivo [STONITH.](http://www.linux-ha.org/wiki/STONITH) 

Do ponto de vista do SAP HANA, o modo de replicação que é usado é sincronizado e uma falha automática é configurada. Na segunda VM, o caso SAP HANA atua como um nó de espera quente. O nó de espera recebe um fluxo sincronizado de registos de mudança saindo da instância principal sAP HANA. Como as transações são cometidas pelo pedido no nó primário hana, o nó principal HANA aguarda para confirmar o compromisso com o pedido até que o nó secundário SAP HANA confirme que recebeu o registo de compromisso. O SAP HANA oferece dois modos de replicação sincronizados. Para mais detalhes e para uma descrição das diferenças entre estes dois modos de replicação sincronizados, consulte os modos de replicação do artigo SAP para a replicação do [sistema SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

A configuração geral parece:

![Diagrama de dois VMs com replicação de armazenamento e failover](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Pode escolher esta solução porque lhe permite obter um RPO=0 e um RTO baixo. Configure a conectividade do cliente SAP HANA para que os clientes SAP HANA utilizem o endereço IP virtual para se ligarem à configuração de replicação do sistema HANA. Tal configuração elimina a necessidade de reconfigurar a aplicação se ocorrer uma falha no nó secundário. Neste cenário, as SKUs VM Azure para as VMprimárias e Secundárias devem ser as mesmas.

## <a name="next-steps"></a>Passos seguintes

Para obter orientações passo a passo sobre a configuração destas configurações em Azure, consulte:

- [Configurar a replicação do sistema SAP HANA em VMs Azure](sap-hana-high-availability.md)
- [Alta disponibilidade para SAP HANA utilizando replicação do sistema](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Para mais informações sobre a disponibilidade do SAP HANA em todas as regiões de Azure, consulte:

- [Disponibilidade de SAP HANA nas regiões do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

