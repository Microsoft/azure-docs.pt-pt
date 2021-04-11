---
title: Configurações e operações de infraestrutura SAP HANA em Azure | Microsoft Docs
description: Guia de operações para sistemas SAP HANA que são implantados em máquinas virtuais Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4c27895c5163d59ca785aa15fa3739359e5be457
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666604"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Configurações e operações de infraestrutura do SAP HANA no Azure
Este documento fornece orientações para configurar a infraestrutura Azure e operar sistemas SAP HANA que são implantados em máquinas virtuais nativas do Azure (VMs). O documento também inclui informações de configuração para a escala SAP HANA para o M128s VM SKU. Este documento não se destina a substituir a documentação padrão SAP, que inclui o seguinte conteúdo:

- [Guia de administração da SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guias de instalação SAP](https://service.sap.com/instguides)
- [Notas SAP](https://service.sap.com/notes)

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar este guia, precisa de conhecimentos básicos dos seguintes componentes Azure:

- [Máquinas virtuais Azure](../../linux/tutorial-manage-vm.md)
- [Redes de rede Azure e redes virtuais](../../linux/tutorial-virtual-network.md)
- [Armazenamento do Azure](../../linux/tutorial-manage-disks.md)

Para saber mais sobre o SAP NetWeaver e outros componentes SAP em Azure, consulte a secção [SAP on Azure](./get-started.md) da [documentação Azure](../../../index.yml).

## <a name="basic-setup-considerations"></a>Considerações básicas de configuração
As secções seguintes descrevem considerações básicas de configuração para a implantação de sistemas SAP HANA em VMs Azure.

### <a name="connect-into-azure-virtual-machines"></a>Ligar-se às máquinas virtuais Azure
Conforme documentado no guia de planeamento de [máquinas virtuais Azure,](./planning-guide.md)existem dois métodos básicos de ligação aos VMs Azure:

- Conecte-se através da internet e dos pontos finais públicos num Jump VM ou no VM que está a executar SAP HANA.
- Ligue-se através de uma [VPN](../../../vpn-gateway/tutorial-site-to-site-portal.md) ou Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

A conectividade local-a-local via VPN ou ExpressRoute é necessária para cenários de produção. Este tipo de conexão também é necessário para cenários de não produção que se alimentam de cenários de produção onde o software SAP está a ser utilizado. A imagem a seguir mostra um exemplo de conectividade entre sites:

![Conectividade transversal](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Escolha os tipos de VM Azure
Os tipos Azure VM que podem ser utilizados para cenários de produção estão listados na [documentação SAP para a IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, está disponível uma maior variedade de tipos nativos de VM Azure.

>[!NOTE]
> Para cenários de não produção, utilize os tipos de VM listados na [nota SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Para a utilização de VMs Azure para cenários de produção, verifique se os VMs certificados SAP HANA na lista de [plataformas certificadas da SAP .](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

Implementar os VMs em Azure utilizando:

- O portal do Azure.
- Cmdlets Azure PowerShell.
- O Azure CLI.

Também pode implementar uma plataforma SAP HANA instalada completa nos serviços Azure VM através da [plataforma SAP Cloud](https://cal.sap.com/). O processo de instalação é descrito em [Deploy SAP S/4HANA ou BW/4HANA em Azure](./cal-s4h.md) ou com a automatização libertada [aqui](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

>[!IMPORTANT]
> Para utilizar M208xx_v2 VMs, é necessário ter cuidado ao selecionar a sua imagem Linux na galeria de imagens Azure VM. Para ler os detalhes, leia o artigo [Memória otimou tamanhos de máquina virtual.](../../mv2-series.md)
> 


### <a name="storage-configuration-for-sap-hana"></a>Configuração de armazenamento para SAP HANA
Para configurações de armazenamento e tipos de armazenamento a serem utilizados com SAP HANA em Azure, leia o documento [CONFIGURAÇÕES de armazenamento de máquinas virtuais SAP HANA Azure](./hana-vm-operations-storage.md)


### <a name="set-up-azure-virtual-networks"></a>Criar redes virtuais Azure
Quando tiver conectividade site-to-site em Azure via VPN ou ExpressRoute, deve ter pelo menos uma rede virtual Azure que esteja ligada através de um Gateway Virtual para o circuito VPN ou ExpressRoute. Em implementações simples, o Gateway Virtual pode ser implantado numa sub-rede da rede virtual Azure (VNet) que também acolhe as instâncias SAP HANA. Para instalar o SAP HANA, cria duas sub-redes adicionais dentro da rede virtual Azure. Uma sub-rede acolhe os VMs para executar as instâncias SAP HANA. A outra sub-rede executa VMs Jumpbox ou Management para hospedar o SAP HANA Studio, outro software de gestão ou o seu software de aplicação.

> [!IMPORTANT]
> Fora da funcionalidade, mas mais importante por razões de desempenho, não é suportado para configurar [aparelhos virtuais da rede Azure](https://azure.microsoft.com/solutions/network-appliances/) na via de comunicação entre a aplicação SAP e a camada DBMS de um sistema SAP NetWeaver, Hybris ou S/4HANA baseado em SAP. A comunicação entre a camada de aplicação SAP e a camada DBMS tem de ser direta. A restrição não inclui [as regras Azure ASG e NSG,](../../../virtual-network/network-security-groups-overview.md) desde que as regras ASG e NSG permitam uma comunicação direta. Outros cenários em que os NVAs não são suportados estão em vias de comunicação entre VMs Azure que representam os nós de cluster Linux Pacemaker e dispositivos SBD, conforme descrito em [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP](./high-availability-guide-suse.md). Ou em vias de comunicação entre VMs Azure e Windows Server SOFS configuradas como descrito no [Cluster uma instância SAP ASCS/SCS num cluster de failover do Windows utilizando uma partilha de ficheiros no Azure](./sap-high-availability-guide-wsfc-file-share.md). Os NVAs nas vias de comunicação podem facilmente duplicar a latência da rede entre dois parceiros de comunicação, podendo restringir a produção em caminhos críticos entre a camada de aplicação SAP e a camada DBMS. Em alguns cenários observados com os clientes, os NVAs podem fazer com que os clusters Pacemaker Linux falhem nos casos em que as comunicações entre os nós do cluster Linux Pacemaker precisam de comunicar ao seu dispositivo SBD através de um NVA.  
> 

> [!IMPORTANT]
> Outro design que **não** é suportado é a segregação da camada de aplicação SAP e a camada DBMS em diferentes redes virtuais Azure que não são [espreitadas umas](../../../virtual-network/virtual-network-peering-overview.md) com as outras. Recomenda-se segregar a camada de aplicação SAP e a camada DBMS utilizando sub-redes dentro de uma rede virtual Azure em vez de utilizar diferentes redes virtuais Azure. Se decidir não seguir a recomendação e, em vez disso, segregar as duas camadas em diferentes redes virtuais, as duas redes virtuais precisam de ser [espreitadas.](../../../virtual-network/virtual-network-peering-overview.md) Esteja ciente de que o tráfego de rede entre duas redes virtuais Azure [estão](../../../virtual-network/virtual-network-peering-overview.md) sujeitos a custos de transferência. Com o enorme volume de dados em muitos Terabytes trocados entre a camada de aplicação SAP e a camada DBMS, podem ser acumulados custos substanciais se a camada de aplicação SAP e a camada DBMS forem segregadas entre duas redes virtuais Azure. 

Quando instala os VMs para executar o SAP HANA, os VMs precisam:

- Dois NICs virtuais instalados: um NIC para ligar à sub-rede de gestão, e um NIC para ligar da rede no local ou outras redes, à instância SAP HANA no VM Azure.
- Endereços IP privados estáticos que são implantados para ambos os NICs virtuais.

> [!NOTE]
> Deve atribuir endereços IP estáticos através de meios Azure a vNICs individuais. Não deve atribuir endereços IP estáticos dentro do so do hóspede a um vNIC. Alguns serviços da Azure, como o Azure Backup Service, baseiam-se no facto de que pelo menos o vNIC primário está definido para DHCP e não para endereços IP estáticos. Consulte também o documento [Troubleshoot Azure virtual machine backup](../../../backup/backup-azure-vms-troubleshoot.md#networking). Se precisar de atribuir vários endereços IP estáticos a um VM, tem de atribuir vários vNICs a um VM.
>
>

No entanto, para implementações que são duradouras, é necessário criar uma arquitetura de rede de datacenter virtual em Azure. Esta arquitetura recomenda a separação do Azure VNet Gateway que se conecta às instalações num Azure VNet separado. Este VNet separado deve acolher todo o tráfego que deixa no local ou na internet. Esta abordagem permite-lhe implementar software para auditar e registar tráfego que introduz o datacenter virtual em Azure neste hub separado VNet. Portanto, tem um VNet que acolhe todo o software e configurações que se relacionam com o tráfego de entradas e saídas para a sua implementação Azure.

Os artigos [Azure Virtual Datacenter: A Network Perspetive](/azure/architecture/vdc/networking-virtual-datacenter) e [Azure Virtual Datacenter e o Enterprise Control Plane](/azure/architecture/vdc/) dão mais informações sobre a abordagem virtual do datacenter e o design relacionado Azure VNet.


>[!NOTE]
>O tráfego que flui entre um hub VNet e o VNet falado usando [o Azure VNet está](../../../virtual-network/virtual-network-peering-overview.md) sujeito a [custos](https://azure.microsoft.com/pricing/details/virtual-network/)adicionais . Com base nesses custos, poderá ter de considerar fazer compromissos entre executar um hub rigoroso e o design de rede de raios e executar [vários Gateways Azure ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md) que você conecta a 'spokes' para contornar o peering VNet. No entanto, a Azure ExpressRoute Gateways introduz [também custos](https://azure.microsoft.com/pricing/details/vpn-gateway/) adicionais. Também pode encontrar custos adicionais para software de terceiros que utiliza para registo de tráfego de rede, auditoria e monitorização. Dependente dos custos de troca de dados através do espremomento VNet de um lado e dos custos criados por gateways adicionais da Azure ExpressRoute e licenças de software adicionais, pode decidir a micro-segmentação dentro de um VNet utilizando sub-redes como unidade de isolamento em vez de VNets.


Para obter uma visão geral dos diferentes métodos de atribuição de endereços IP, consulte [os tipos de endereços IP e os métodos de atribuição em Azure](../../../virtual-network/public-ip-addresses.md). 

Para VMs que executam SAP HANA, deve trabalhar com endereços IP estáticos atribuídos. A razão é que alguns atributos de configuração para endereços IP de referência HANA.

[Os Grupos de Segurança da Rede Azure (NSGs)](../../../virtual-network/virtual-network-vnet-plan-design-arm.md) são utilizados para direcionar o tráfego que é encaminhado para a instância SAP HANA ou para a caixa de salto. Os NSGs e, [eventualmente, grupos de segurança de aplicações](../../../virtual-network/network-security-groups-overview.md#application-security-groups) estão associados à sub-rede SAP HANA e à sub-rede de Gestão.

A imagem a seguir mostra uma visão geral de um esquema de implantação áspera para SAP HANA seguindo um hub e falou arquitetura VNet:

![Esquema de implantação áspero para SAP HANA](media/hana-vm-operations/hana-simple-networking-dmz.png)

Para implantar o SAP HANA em Azure sem uma ligação site-to-site, ainda pretende proteger a instância SAP HANA da internet pública e escondê-la atrás de um representante avançado. Neste cenário básico, a implementação baseia-se nos serviços DNS incorporados da Azure para resolver os números de anfitriões. Numa implementação mais complexa onde são utilizados endereços IP virados para o público, os serviços de DNS incorporados são especialmente importantes. Utilize NSGs Azure e [Azure NVAs](https://azure.microsoft.com/solutions/network-appliances/) para controlar, monitorize o encaminhamento da internet para a sua arquitetura Azure VNet em Azure. A imagem a seguir mostra um esquema áspero para implantar SAP HANA sem uma ligação site-to-site num hub e falou arquitetura VNet:
  
![Esquema de implantação áspero para SAP HANA sem uma ligação site-to-site](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

Outra descrição sobre como usar Azure NVAs para controlar e monitorizar o acesso a partir da Internet sem o hub e a arquitetura VNet falada pode ser encontrada no artigo [Implementar aparelhos virtuais de rede altamente disponíveis](/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Configuração da infraestrutura Azure para a escala SAP HANA
Para saber os tipos Azure VM certificados para a escala OLAP ou para a escala S/4HANA, consulte o diretório de [hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Uma marca de verificação na coluna 'Clustering' indica suporte de escala. O tipo de aplicação indica se a escala de OLAP ou a escala S/4HANA são suportadas. Para obter mais informações sobre os nós certificados em escala para cada um dos VMs, verifique os detalhes das entradas no VM SKU particular listado no diretório de hardware SAP HANA.

Os desbloqueios mínimos de SO para a implementação de configurações de escala em VMs Azure, verifique os detalhes das entradas no VM SKU particular listado no diretório de hardware SAP HANA. De uma configuração de escala de escala de N-nó OLAP, um nó funciona como nó principal. Os outros nós até ao limite da lei de certificação como nó de trabalhador. Os nós de espera adicionais não contam para o número de nós certificados

>[!NOTE]
> As implementações de escala Azure VM de SAP HANA com nó de espera só são possíveis usando o armazenamento de [Ficheiros Azure NetApp.](https://azure.microsoft.com/services/netapp/) Nenhum outro armazenamento Azure certificado DA SAP HANA permite a configuração de nó de espera SAP HANA
>

Para /hana/compartilhado, recomendamos também a utilização de [Ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/). 

Um design básico típico para um único nó numa configuração de escala vai parecer:

![Diagrama que mostra um design básico típico para um único nó numa configuração de escala.](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

A configuração básica de um nó VM para a escala SAP HANA parece:

- Para **/hana/shared,** utiliza o serviço NFS nativo fornecido através dos Ficheiros Azure NetApp. 
- Todos os outros volumes de discos não são partilhados entre os diferentes nós e não são baseados em NFS. As configurações e etapas de instalação para instalações HANA de escala com **/hana/data** e **/hana/log são** fornecidos mais tarde neste documento. Para o armazenamento certificado HANA que pode ser usado, verifique o artigo [Configurações de armazenamento de máquinas virtuais SAP HANA Azure](./hana-vm-operations-storage.md).


Dimensionando os volumes ou discos, é necessário verificar o documento [REQUISITOS de Armazenamento SAP HANA TDI,](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)para o tamanho exigido, dependendo do número de nós de trabalhadores. O documento lança uma fórmula que precisa de aplicar para obter a capacidade necessária do volume

Os outros critérios de design que são apresentados nos gráficos da configuração do nó único para um SAP HANA VM de escala é o VNet, ou melhor a configuração da sub-rede. A SAP recomenda vivamente a separação do cliente/aplicação face ao tráfego das comunicações entre os nós HANA. Como mostrado nos gráficos, este objetivo é alcançado por ter dois vNICs diferentes ligados ao VM. Ambos os vNICs estão em sub-redes diferentes, têm dois endereços IP diferentes. Em seguida, controla o fluxo de tráfego com regras de encaminhamento utilizando NSGs ou rotas definidas pelo utilizador.

Nomeadamente no Azure, não existem meios e métodos para impor a qualidade do serviço e das quotas em vNICs específicos. Como resultado, a separação da comunicação cliente/aplicação virada para o cliente e para o nó não abre quaisquer oportunidades para priorizar um fluxo de tráfego sobre o outro. Em vez disso, a separação continua a ser uma medida de segurança na proteção das comunicações intra-nól das configurações de escala.  

>[!NOTE]
>A SAP recomenda a separação do tráfego de rede para o lado cliente/aplicação e o tráfego intra-nól, conforme descrito neste documento. Assim, é recomendada a colocação de uma arquitetura como mostrado nos últimos gráficos. Consulte também a sua equipa de segurança e conformidade para os requisitos que se desviam da recomendação 
>

Do ponto de vista em rede, a arquitetura de rede mínima exigida seria:

![Básicos de escala de um único nó](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>Instalação sap hana escala-out n Azure
Instalando uma configuração SAP de escala, é necessário executar passos ásperos de:

- Implantação de novas ou adaptação de uma infraestrutura Azure VNet existente
- Implantação dos novos VMs utilizando o armazenamento premium gerido Azure, volumes ultra-discos e/ou volumes NFS com base na ANF
- - Adaptar o encaminhamento de rede para garantir que, por exemplo, a comunicação intra-nó entre VMs não seja encaminhada através de uma [NVA](https://azure.microsoft.com/solutions/network-appliances/). 
- Instale o nó mestre SAP HANA.
- Adaptar parâmetros de configuração do nó mestre SAP HANA
- Continue com a instalação dos nosdes operários SAP HANA

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Instalação de SAP HANA em configuração de escala
À medida que a sua infraestrutura Azure VM é implantada, e todos os outros preparativos são feitos, você precisa instalar as configurações de escala SAP HANA nestes passos:

- Instale o nó principal SAP HANA de acordo com a documentação da SAP
- Em caso de utilização do armazenamento Azure Premium ou ultra disco com discos não partilhados de /hana/data e /hana/log, é necessário alterar o ficheiro global.ini e adicionar o parâmetro 'basepath_shared = não' ao ficheiro global.ini. Este parâmetro permite que o SAP HANA seja executado em escala sem volumes 'partilhados' **/hana/dados** e **/hana/log** volumes entre os nós. Os detalhes são documentados na [#2080991 da nota SAP](https://launchpad.support.sap.com/#/notes/2080991). Se estiver a utilizar volumes NFS com base em ANF para /hana/dados e /hana/log, não precisa de fazer esta alteração
- Após a eventual alteração do parâmetro global.ini, reinicie a instância SAP HANA
- Adicione nós de trabalhador adicionais. Consulte também <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Especificar a rede interna para comunicação inter-nólada SAP HANA durante a instalação ou posterior utilização, por exemplo, do hdblcm local. Para obter documentação mais detalhada, consulte também [a SAP Note #2183363.](https://launchpad.support.sap.com/#/notes/2183363) 

Os detalhes para configurar um sistema de escala SAP HANA com nó de espera no SUSE Linux são descritos em detalhe no [Implementar um sistema de escala SAP HANA com nó de espera nos VMs Azure utilizando ficheiros Azure NetApp no SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md). Documentação equivalente para chapéu vermelho pode ser encontrada no artigo [Implementar um sistema de escala SAP HANA com nó de espera em VMs Azure usando ficheiros Azure NetApp em Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md). 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA Dynamic Tiering 2.0 para máquinas virtuais Azure

Para além das certificações SAP HANA em VMs da série Azure M, o SAP HANA Dynamic Tiering 2.0 também é suportado no Microsoft Azure (ver SAP HANA Dynamic Tiering documentation links mais abaixo). Embora não haja diferença na instalação do produto ou no seu funcionamento, por exemplo, através do COCKPIT SAP HANA dentro de uma Máquina Virtual Azure, existem alguns itens importantes, que são obrigatórios para suporte oficial no Azure. Estes pontos-chave são descritos abaixo. Ao longo do artigo, a abreviatura "DT 2.0" vai ser usada em vez do nome completo Dynamic Tiering 2.0.

SAP HANA Dynamic Tiering 2.0 não é suportado por SAP BW ou S4HANA. Os principais casos de uso neste momento são aplicações HANA nativas.


### <a name="overview"></a>Descrição Geral

A imagem abaixo dá uma visão geral sobre o suporte DT 2.0 no Microsoft Azure. Existe um conjunto de requisitos obrigatórios, que devem ser seguidos para cumprir a certificação oficial:

- O DT 2.0 deve ser instalado num Azure VM dedicado. Não pode funcionar no mesmo VM onde o SAP HANA corre
- OS SAP HANA e DT 2.0 VMs devem ser implantados dentro da mesma Azure Vnet
- Os HANA SAP e DT 2.0 VMs devem ser implantados com rede acelerada Azure habilitada
- O tipo de armazenamento para os DT 2.0 VMs deve ser Azure Premium Storage
- Vários discos Azure devem ser ligados ao DT 2.0 VM
- É necessário criar um raid de software / volume listrado (seja via LVM ou mdadm) usando striping através dos discos Azure

Mais detalhes serão explicados nas seguintes secções.

![Visão geral da arquitetura SAP HANA DT 2.0](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Dedicado Azure VM para SAP HANA DT 2.0

Em Azure IaaS, DT 2.0 só é suportado num VM dedicado. Não é permitido executar DT 2.0 no mesmo Azure VM onde está a decorrer a instância HANA. Inicialmente podem ser utilizados dois tipos de VM para executar SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Consulte a descrição do tipo VM [aqui](../../sizes-memory.md)

Dada a ideia básica de DT 2.0, que tem a ver com a descarga de dados "quentes" para poupar custos, faz sentido utilizar os tamanhos VM correspondentes. Não há nenhuma regra estrita no que diz respeito às combinações possíveis. Depende da carga de trabalho específica do cliente.

As configurações recomendadas seriam:

| Tipo SAP HANA VM | Tipo DT 2.0 VM |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Todas as combinações de VMs da série M certificadas pela SAP HANA com DT 2.0 VMs suportados (M64-32ms e E32sv3) são possíveis.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Rede Azure e SAP HANA DT 2.0

A instalação do DT 2.0 num VM dedicado requer a produção de rede entre o DT 2.0 VM e o SAP HANA VM de 10 Gb mínimo. Portanto, é obrigatório colocar todos os VMs dentro do mesmo Azure Vnet e ativar a rede acelerada do Azure.

Consulte informações adicionais sobre a rede acelerada do Azure [aqui](../../../virtual-network/create-vm-accelerated-networking-cli.md)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Armazenamento VM para SAP HANA DT 2.0

De acordo com a orientação de boas práticas DT 2.0, a produção de IO do disco deve ser mínima de 50 MB/seg por núcleo físico. Olhando para a especificação dos dois tipos Azure VM, que são suportados para DT 2.0, o limite máximo de produção de IO do disco para o VM parece:

- E32sv3 : 768 MB/seg (sem cocó), o que significa uma relação de 48 MB/seg por núcleo físico
- M64-32ms: 1000 MB/seg (sem cocó), o que significa uma relação de 62,5 MB/seg por núcleo físico

É necessário anexar vários discos Azure ao DT 2.0 VM e criar uma raid de software (striping) ao nível de SO para atingir o limite máximo de produção de disco por VM. Um único disco Azure não pode fornecer a produção para atingir o limite máximo de VM a este respeito. O armazenamento Azure Premium é obrigatório para executar DT 2.0. 

- Detalhes sobre os tipos de discos Azure disponíveis podem ser encontrados [aqui](../../disks-types.md)
- Detalhes sobre a criação de raid de software via mdadm podem ser encontrados [aqui](/previous-versions/azure/virtual-machines/linux/configure-raid)
- Detalhes sobre a configuração da LVM para criar um volume listrado para a produção máxima podem ser encontrados [aqui](/previous-versions/azure/virtual-machines/linux/configure-lvm)

Dependendo dos requisitos de tamanho, existem diferentes opções para alcançar o rendimento máximo de um VM. Aqui estão possíveis configurações de disco de volume de dados para cada tipo DT 2.0 VM para atingir o limite superior de produção de VM. O E32sv3 VM deve ser considerado como um nível de entrada para cargas de trabalho mais pequenas. No caso de se revelar que não é rápido o suficiente, pode ser necessário redimensionar o VM para M64-32ms.
Como o VM M64-32ms tem muita memória, a carga de IO pode não atingir o limite especialmente para a leitura de cargas de trabalho intensivas. Portanto, menos discos no conjunto de listras podem ser suficientes dependendo da carga de trabalho específica do cliente. Mas para estar no lado seguro as configurações do disco abaixo foram escolhidas para garantir a máxima produção:


| SKU da VM | Disco Config 1 | Disco Config 2 | Disco Config 3 | Disco Config 4 | Disco Config 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


Especialmente no caso de a carga de trabalho ser intensa de leitura, poderia aumentar o desempenho do IO para ligar a cache do anfitrião Azure "read-only", como recomendado para os volumes de dados do software de base de dados. Considerando que para o registo de transações a cache do disco de anfitrião Azure deve ser "nenhuma". 

No que diz respeito ao tamanho do volume de registo, um ponto de partida recomendado é um heurístico de 15% do tamanho dos dados. A criação do volume de registo pode ser realizada utilizando diferentes tipos de discos Azure, dependendo dos requisitos de custo e de produção. Para o volume de registo, é necessária uma produção de E/S elevada.  Em caso de utilização do tipo VM M64-32ms, é obrigatório ativar o [Acelerador de Escrita](../../how-to-enable-write-accelerator.md). O Acelerador Azure Write fornece uma latência de escrita de disco ideal para o registo de transações (apenas disponível para série M). Existem alguns itens a considerar, embora como o número máximo de discos por tipo VM. Detalhes sobre o Acelerador de Escrita podem ser encontrados [aqui](../../how-to-enable-write-accelerator.md)


Aqui estão alguns exemplos sobre o dimensionamento do volume de registo:

| tamanho do volume de dados e tipo de disco | volume de log e tipo de disco config 1 | volume de log e tipo de disco config 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Tal como para a escala SAP HANA, o diretório /hana/partilhado tem de ser partilhado entre o SAP HANA VM e o DT 2.0 VM. Recomenda-se a mesma arquitetura que para a escala SAP HANA utilizando VMs dedicados, que funcionam como um servidor NFS altamente disponível. Para fornecer um volume de backup partilhado, o design idêntico pode ser usado. Mas cabe ao cliente se ha seria necessário ou se é suficiente apenas usar um VM dedicado com capacidade de armazenamento suficiente para funcionar como um servidor de backup.



### <a name="links-to-dt-20-documentation"></a>Links para documentação DT 2.0 

- [Sap HANA Dynamic Tiering instalação e guia de atualização](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Tutoriais e recursos dinâmicos SAP HANA](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [POC dinâmico de tiering SAP HANA](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 melhorias dinâmicas do tiering](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operações de implantação da SAP HANA em VMs Azure
As seguintes secções descrevem algumas das operações relacionadas com a implantação de sistemas SAP HANA em VMs Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Apoiar e restaurar as operações em VMs Azure
Os seguintes documentos descrevem como fazer o back up e restaurar a sua implantação SAP HANA:

- [Descrição geral da cópia de segurança do SAP HANA](./sap-hana-backup-guide.md)
- [Backup de nível de ficheiro SAP HANA](./sap-hana-backup-file-level.md)
- [Referência de instantâneo de armazenamento SAP HANA](./sap-hana-backup-guide.md)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Iniciar e reiniciar VMs que contenham SAP HANA
Uma característica proeminente da nuvem pública Azure é que você é cobrado apenas para os seus minutos de computação. Por exemplo, quando desligas um VM que está a funcionar o SAP HANA, só és cobrado pelos custos de armazenamento durante esse tempo. Outra funcionalidade está disponível quando especifica endereços IP estáticos para os seus VMs na sua implementação inicial. Quando reinicia um VM que tem SAP HANA, o VM reinicia com os seus endereços IP anteriores. 


### <a name="use-saprouter-for-sap-remote-support"></a>Utilize SAProuter para suporte remoto SAP
Se tem uma ligação site-to-site entre as suas localizações no local e Azure, e está a executar componentes SAP, então provavelmente já está a executar SAProuter. Neste caso, preencha os seguintes itens para suporte remoto:

- Mantenha o endereço IP privado e estático do VM que acolhe o SAP HANA na configuração SAProuter.
- Configure o NSG da sub-rede que acolhe o HANA VM para permitir o tráfego através da porta TCP/IP 3299.

Se estiver a ligar-se ao Azure através da internet, e não tiver um router SAP para o VM com SAP HANA, então tem de instalar o componente. Instale o SAProuter num VM separado na sub-rede De Gestão. A imagem a seguir mostra um esquema áspero para a implantação do SAP HANA sem uma ligação site-to-site e com o SAProuter:

![Esquema de implantação áspero para SAP HANA sem uma ligação site-to-site e SAProuter](media/hana-vm-operations/hana-simple-networking-saprouter.png)

Certifique-se de que instala o SAProuter num VM separado e não no seu VM jumpbox. O VM separado deve ter um endereço IP estático. Para ligar o SAProuter ao SAProuter que é hospedado pela SAP, contacte o SAP para obter um endereço IP. (O SAProuter que é hospedado pela SAP é a contrapartida da instância SAProuter que instala no seu VM.) Utilize o endereço IP da SAP para configurar a sua instância SAProuter. Nas definições de configuração, a única porta necessária é a porta TCP 3299.

Para obter mais informações sobre como configurar e manter ligações de suporte remoto através do SAProuter, consulte a [documentação SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Alta disponibilidade com SAP HANA em VMs nativos Azure
Se estiver a executar o SUSE Linux Enterprise Server ou o Red Hat, pode estabelecer um cluster Pacemaker com dispositivos STONITH. Pode utilizar os dispositivos para configurar uma configuração SAP HANA que utiliza replicação sincronizada com replicação do sistema HANA e falha automática. Para mais informações listadas na secção "próximos passos".

## <a name="next-steps"></a>Passos Seguintes
Familiarize-se com os artigos listados
- [Configurações de armazenamento da máquina virtual do Azure do SAP HANA](./hana-vm-operations-storage.md)
- [Implementar um sistema de escala SAP HANA com nó de espera em VMs Azure utilizando ficheiros Azure NetApp no SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Implementar um sistema de escala SAP HANA com nó de espera em VMs Azure utilizando ficheiros Azure NetApp no Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [Alta disponibilidade de SAP HANA em VMs Azure no SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
- [Alta disponibilidade de SAP HANA em VMs Azure em Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)