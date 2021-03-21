---
title: Disponibilidade de SAP HANA dentro de uma região de Azure | Microsoft Docs
description: Descreve as operações da SAP HANA em VMs nativos de Azure numa região de Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 845cecfb6b09591b10de30267b31e6e1a80a7482
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504325"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Disponibilidade de SAP HANA dentro de uma região de Azure
Este artigo descreve vários cenários de disponibilidade dentro de uma região de Azure. Azure tem muitas regiões, espalhadas por todo o mundo. Para a lista das regiões de Azure, consulte as [regiões de Azure.](https://azure.microsoft.com/regions/) Para a implementação do SAP HANA em VMs dentro de uma região de Azure, a Microsoft oferece a implementação de um único VM com uma instância HANA. Para uma maior disponibilidade, pode implementar dois VMs com duas instâncias HANA dentro de um [conjunto de disponibilidades Azure](../../windows/tutorial-availability-sets.md) que utiliza a replicação do sistema HANA para disponibilidade. 

Atualmente, o Azure está a oferecer [Zonas de Disponibilidade Azure.](../../../availability-zones/az-overview.md) Este artigo não descreve as Zonas de Disponibilidade em detalhe. Mas, inclui uma discussão geral sobre a utilização de Conjuntos de Disponibilidade versus Zonas de Disponibilidade.

As regiões de Azure onde as Zonas de Disponibilidade são oferecidas têm vários centros de dados. Os datacenters são independentes no fornecimento de fonte de energia, arrefecimento e rede. A razão para oferecer diferentes zonas dentro de uma única região de Azure é implementar aplicações em duas ou três Zonas de Disponibilidade que são oferecidas. Implantando em zonas, problemas de energia e networking que afetam apenas uma infraestrutura da Zona de Disponibilidade Azure, a sua implementação de aplicações dentro de uma região do Azure ainda está funcional. Pode ocorrer alguma capacidade reduzida. Por exemplo, os VMs numa zona podem perder-se, mas os VM nas outras duas zonas ainda estariam em funcionamento. 
 
Um Conjunto de Disponibilidade Azure é uma capacidade lógica de agrupamento que ajuda a garantir que os recursos VM que coloca dentro do Conjunto de Disponibilidade são isolados por falhas uns dos outros quando são implantados dentro de um datacenter Azure. O Azure garante que as VMs que colocar num Conjunto de Disponibilidade são executadas em vários servidores físicos, suportes de computação, unidades de armazenamento e comutadores de rede. Em alguma documentação Azure, esta configuração é referida como colocações em diferentes [domínios de atualização e avaria .](../../availability.md) Estas colocações geralmente estão dentro de um datacenter Azure. Assumindo que a fonte de energia e os problemas de rede afetariam o datacenter que está a implementar, toda a sua capacidade numa região de Azure seria afetada.

A colocação de datacenters que representam zonas de disponibilidade do Azure é um compromisso entre a prestação de latência de rede aceitável entre serviços implantados em diferentes zonas, e uma distância entre centros de dados. As catástrofes naturais idealmente não afetariam a energia, o fornecimento de rede e as infraestruturas para todas as Zonas de Disponibilidade desta região. No entanto, como as catástrofes naturais monumentais têm demonstrado, as Zonas de Disponibilidade nem sempre podem fornecer a disponibilidade que deseja dentro de uma região. Pense no furacão Maria que atingiu a ilha de Porto Rico em 20 de setembro de 2017. O furacão causou um apagão de quase 100% na ilha de 150 km de largura.

## <a name="single-vm-scenario"></a>Cenário single-VM

Num cenário único de VM, cria-se um VM Azure para a instância SAP HANA. Utiliza o Azure Premium Storage para hospedar o disco do sistema operativo e todos os seus discos de dados. O Azure uptime SLA de 99,9 por cento e os SLAs de outros componentes Azure são suficientes para que você cumpra a sua disponibilidade SLAs para os seus clientes. Neste cenário, não tem necessidade de alavancar um Conjunto de Disponibilidade de Azure para VMs que executam a camada DBMS. Neste cenário, conta com duas características diferentes:

- Azure VM auto-reinicialização (também referida como cura do serviço Azure)
- REINICIE-SE HANA automaticamente

Azure VM auto restart, ou cura de serviço, é uma funcionalidade em Azure que funciona em dois níveis:

- O anfitrião do servidor Azure verifica a saúde de um VM que está hospedado no anfitrião do servidor.
- O controlador de tecido Azure monitoriza a saúde e disponibilidade do anfitrião do servidor.

Uma funcionalidade de verificação de saúde monitoriza a saúde de todos os VM que estão hospedados num servidor Azure. Se um VM cair num estado não saudável, um reinício do VM pode ser iniciado pelo agente anfitrião Azure que verifica a saúde do VM. O controlador de tecido verifica a saúde do hospedeiro verificando vários parâmetros diferentes que podem indicar problemas com o hardware do anfitrião. Também verifica a acessibilidade do hospedeiro através da rede. Uma indicação de problemas com o hospedeiro pode levar aos seguintes eventos:

- Se o hospedeiro sinalizar um mau estado de saúde, é desencadeado um reinício do hospedeiro e um reinício dos VMs que estavam a funcionar no hospedeiro.
- Se o hospedeiro não estiver em estado saudável após o reboot bem sucedido, é iniciada uma redistribuição dos VMs que estavam originalmente no nó agora insalubre para um servidor de hospedeiro saudável. Neste caso, o hospedeiro original é marcado como não saudável. Não será usado para mais implementações até que seja limpo ou substituído.
- Se o hospedeiro não saudável tiver problemas durante o processo de reinicialização, é desencadeado um reinício imediato dos VMs num hospedeiro saudável. 

Com o hospedeiro e a monitorização VM fornecidas pela Azure, os VMs Azure que experimentam problemas de hospedeiro são automaticamente reiniciados num hospedeiro saudável do Azure. 

>[!IMPORTANT]
>A cura do serviço Azure não reiniciará os VMs Linux onde o so convidado está em estado de pânico de kernel. As definições predefinidos das versões Linux geralmente utilizadas não estão a reiniciar automaticamente VMs ou servidor onde o kernel Linux está em estado de pânico. Em vez disso, o padrão prevê manter o SO em estado de pânico do núcleo para ser capaz de anexar um depurar kernel para analisar. O Azure está a honrar esse comportamento ao não reiniciar automaticamente um VM com o so convidado num estado como este. Suposição é que tais ocorrências são extremamente raras. Pode substituir o comportamento predefinido para permitir o reinício do VM. Para alterar o comportamento predefinido, ative o parâmetro 'kernel.panic' em /etc/sysctl.conf. O tempo que define para este parâmetro é em segundos. Os valores recomendados comuns são esperar 20-30 segundos antes de ativar o reboot através deste parâmetro. Consulte também <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>.

A segunda funcionalidade em que confia neste cenário é o facto de o serviço HANA que funciona num VM reiniciado começar automaticamente após o reboot do VM. Pode configurar o [serviço HANA para reiniciar automaticamente](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) através dos serviços de vigilância dos vários serviços HANA.

Você pode melhorar este cenário single-VM adicionando um nó de falha fria a uma configuração SAP HANA. Na documentação SAP HANA, esta configuração é chamada [de auto-failover hospedeiro](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Esta configuração pode fazer sentido numa situação de implementação no local em que o hardware do servidor é limitado, e você dedica um nó de um único servidor como o nó de falha automática do anfitrião para um conjunto de anfitriões de produção. Mas em Azure, onde a infraestrutura subjacente do Azure fornece um servidor alvo saudável para um reinício bem sucedido do VM, não faz sentido implementar o hospedeiro SAP HANA auto-failover. Devido à cura do serviço Azure, não há nenhuma arquitetura de referência que preveja um nó de espera para o hospedeiro HANA auto-failover. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Caso especial de configurações de escala SAP HANA em Azure
A elevada disponibilidade para configurações de escala SAP HANA está a depender da cura do serviço dos VMs Azure e do reinício da instância SAP HANA, uma vez que o VM está a funcionar novamente. Arquiteturas de alta disponibilidade baseadas na replicação do sistema HANA serão introduzidas mais tarde. 


## <a name="availability-scenarios-for-two-different-vms"></a>Cenários de disponibilidade para dois VMs diferentes

Se utilizar dois VMs Azure dentro de um Conjunto de Disponibilidade Azure, pode aumentar o tempo de uptime entre estes dois VMs se forem colocados num Conjunto de Disponibilidade Azure dentro de uma região Azure. A instalação base em Azure seria como:

![Diagrama de dois VMs com todas as camadas](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Para ilustrar os diferentes cenários de disponibilidade, algumas das camadas do diagrama são omitidas. O diagrama mostra apenas camadas que retratam VMs, anfitriões, Conjuntos de Disponibilidade e Regiões Azure. As instâncias da Rede Virtual Azure, grupos de recursos e subscrições não desempenham um papel nos cenários descritos nesta secção.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replicar backups para uma segunda máquina virtual

Uma das configurações mais rudimentares é usar cópias de segurança. Em particular, pode ter cópias de segurança de registo de transações enviadas de um VM para outro Azure VM. Pode escolher o tipo de Armazenamento Azure. Nesta configuração, é responsável pela escrita da cópia das cópias de backups agendadas que são conduzidas no primeiro VM para o segundo VM. Se precisar de utilizar as segundas instâncias em VM, tem de restaurar as cópias de segurança completas, incrementais/diferenciais e de registo de transações ao ponto de que necessita. 

A arquitetura parece:

![Diagrama que mostra a arquitetura de dois VMs com replicação de armazenamento.](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Esta configuração não é adequada para alcançar grandes tempos de Objetivo de Ponto de Recuperação (RPO) e Objetivo do Tempo de Recuperação (RTO). Os tempos de RTO seriam especialmente sofridos devido à necessidade de restaurar totalmente a base de dados completa utilizando as cópias de segurança copiadas. No entanto, esta configuração é útil para a recuperação de eliminação de dados não intencionais nos principais casos. Com esta configuração, a qualquer momento, pode restaurar a qualquer ponto no tempo, extrair os dados e importar os dados eliminados no seu caso principal. Por isso, pode fazer sentido utilizar um método de cópia de backup em combinação com outras funcionalidades de alta disponibilidade. 

Enquanto as cópias de segurança estão a ser copiadas, poderás utilizar um VM menor do que o VM principal em que a instância SAP HANA está a funcionar. Tenha em mente que pode anexar um número menor de VHDs a VMs menores. Para obter informações sobre os limites dos tipos de VM individuais, consulte [tamanhos para máquinas virtuais Linux em Azure](../../sizes.md).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replicação do sistema SAP HANA sem falha automática

Os cenários descritos nesta secção utilizam a replicação do sistema SAP HANA. Para obter a documentação SAP, consulte [a replicação do Sistema.](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) Cenários sem falha automática não são comuns para configurações dentro de uma região de Azure. Uma configuração sem falha automática, embora evitando uma configuração do Pacemaker, obriga-o a monitorizar e a falhar manualmente. Uma vez que isto requer e esforços também, a maioria dos clientes está a contar com a cura do serviço Azure. Existem alguns casos de borda em que esta configuração pode ajudar em termos de cenários de falha. Ou, em alguns casos, um cliente pode querer perceber mais eficiência.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>Replicação do sistema SAP HANA sem falha automática e sem pré-carga de dados

Neste cenário, utiliza-se a replicação do sistema SAP HANA para mover dados de forma sincronizada para obter um RPO de 0. Por outro lado, você tem um RTO suficientemente longo que você não precisa de failover ou dados pré-carregados na cache de instância HANA. Neste caso, é possível alcançar mais economia na sua configuração, tomando as seguintes ações:

- Executar outro caso SAP HANA no segundo VM. A instância SAP HANA no segundo VM tira a maior parte da memória da máquina virtual. Em caso de falha no segundo VM, é necessário desligar a instância SAP HANA em execução que tem os dados totalmente carregados no segundo VM, para que os dados replicados possam ser carregados na cache da instância HANA visada no segundo VM.
- Utilize um tamanho VM menor no segundo VM. Se ocorrer uma falha, tem um passo adicional antes de o manual falhar. Neste passo, redimensione o VM para o tamanho da fonte VM. 
 
O cenário parece:

![Diagrama de dois VMs com replicação de armazenamento](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Mesmo que não utilize a pré-carga de dados no alvo de replicação do sistema HANA, precisa de pelo menos 64 GB de memória. Também precisa de memória suficiente para além de 64 GB para manter os dados da loja na memória da instância-alvo.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>Replicação do sistema SAP HANA sem falha automática e com pré-carga de dados

Neste cenário, os dados replicados para a instância HANA no segundo VM são pré-carregados. Isto elimina as duas vantagens de não pré-carregar dados. Neste caso, não pode executar outro sistema SAP HANA no segundo VM. Também não pode usar um tamanho VM menor. Por isso, os clientes raramente implementam este cenário.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replicação do sistema SAP HANA com falha automática

Na configuração de disponibilidade padrão e mais comum dentro de uma região de Azure, dois VMs Azure que executam SLES Linux têm um cluster de failover definido. O cluster SLES Linux baseia-se na estrutura do [Pacemaker,](./high-availability-guide-suse-pacemaker.md) em conjunto com um dispositivo [STONITH.](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) 

Do ponto de vista SAP HANA, o modo de replicação utilizado é sincronizado e uma falha automática é configurada. No segundo VM, o caso SAP HANA funciona como um nó quente de espera. O nó de espera recebe um fluxo sincronizado de registos de mudança saindo da primeira instância SAP HANA. Uma vez que as transações são cometidas pelo pedido no nó primário HANA, o nó hana primário aguarda para confirmar o compromisso com o pedido até que o nó sap hana secundário confirme que recebeu o registo do compromisso. O SAP HANA oferece dois modos de replicação sincronizado. Para obter mais detalhes e para uma descrição das diferenças entre estes dois modos de replicação sincronizada, consulte os modos de replicação do artigo SAP [para a replicação do sistema SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

A configuração geral parece:

![Diagrama de dois VMs com replicação de armazenamento e failover](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Pode escolher esta solução porque lhe permite obter um RPO=0 e um RTO baixo. Configurar a conectividade do cliente SAP HANA para que os clientes SAP HANA utilizem o endereço IP virtual para se ligarem à configuração de replicação do sistema HANA. Tal configuração elimina a necessidade de reconfigurar a aplicação se ocorrer uma falha no nó secundário. Neste cenário, os SKUs VM Azure para os VM primários e secundários devem ser os mesmos.

## <a name="next-steps"></a>Passos seguintes

Para obter orientações passo a passo sobre a configuração destas configurações em Azure, consulte:

- [Configurar a replicação do sistema SAP HANA em VMs Azure](sap-hana-high-availability.md)
- [Alta disponibilidade para SAP HANA utilizando a replicação do sistema](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Para obter mais informações sobre a disponibilidade do SAP HANA nas regiões de Azure, consulte:

- [Disponibilidade de SAP HANA em todas as regiões de Azure](./sap-hana-availability-across-regions.md)