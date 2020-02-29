---
title: Configurações e operações de infraestruturas SAP HANA no Azure Microsoft Docs
description: Guia de operações para sistemas SAP HANA que são implantados em máquinas virtuais Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 07c8f84f2e37abd87953d8e4cb20b37258b25fda
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920516"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Configurações e operações de infraestrutura do SAP HANA no Azure
Este documento fornece orientações para configurar a infraestrutura Azure e operar sistemas SAP HANA que são implantados em máquinas virtuais nativas do Azure (VMs). O documento também inclui informações de configuração para a escala SAP HANA para o M128s VM SKU. Este documento não se destina a substituir a documentação padrão do SAP, que inclui o seguinte conteúdo:

- [Guia de administração SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guias de instalação SAP](https://service.sap.com/instguides)
- [Notas SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar este guia, necessita de conhecimentos básicos sobre os seguintes componentes Azure:

- [Máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Redes de networking e virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Armazenamento do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Para saber mais sobre o SAP NetWeaver e outros componentes SAP no Azure, consulte o [SAP na](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) secção Azure da [documentação Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerações básicas de configuração
As seguintes secções descrevem considerações básicas de configuração para a implementação de sistemas SAP HANA em VMs Azure.

### <a name="connect-into-azure-virtual-machines"></a>Ligue-se a máquinas virtuais Azure
Tal como documentado no guia de planeamento de [máquinas virtuais Azure,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)existem dois métodos básicos para a ligação aos VMs Azure:

- Conecte-se através da internet e pontos finais públicos num Jump VM ou no VM que está a executar o SAP HANA.
- Ligue-se através de uma [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

A conectividade local através da VPN ou expressRoute é necessária para cenários de produção. Este tipo de conexão também é necessário para cenários de não produção que se alimentam de cenários de produção onde o software SAP está a ser utilizado. A imagem que se segue mostra um exemplo de conectividade transversal:

![Conectividade transversal](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Escolha tipos de VM Azure
Os tipos de VM Azure que podem ser utilizados para cenários de produção estão listados na [documentação SAP para iAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, uma maior variedade de tipos de VM Azure nativos está disponível.

>[!NOTE]
> Para cenários de não produção, utilize os tipos vM listados na [nota SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Para a utilização de VMs Azure para cenários de produção, verifique se os VMs certificados sAP HANA na lista de [Plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)da SAP.

Implementar os VMs em Azure utilizando:

- O portal Azure.
- Cmdlets Azure PowerShell.
- O Azure CLI.

Também pode implementar uma plataforma SAP HANA instalada completa nos serviços Azure VM através da [plataforma SAP Cloud](https://cal.sap.com/). O processo de instalação é descrito em [Deploy SAP S/4HANA ou BW/4HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) ou com a automatização [aqui](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)lançada .

>[!IMPORTANT]
> Para utilizar M208xx_v2 VMs, tem de ter cuidado ao selecionar a sua imagem Linux a partir da galeria de imagens Azure VM. Para ler os detalhes, leia o artigo [Memória otimizada tamanhos de máquina virtual](../../mv2-series.md).
> 


### <a name="storage-configuration-for-sap-hana"></a>Configuração de armazenamento para SAP HANA
Para configurações de armazenamento e tipos de armazenamento a utilizar com SAP HANA em Azure, leia as configurações de [armazenamento de máquinas virtuais SAP HANA Azure](./hana-vm-operations-storage.md)


### <a name="set-up-azure-virtual-networks"></a>Criar redes virtuais Azure
Quando tiver conectividade site-a-site em Azure via VPN ou ExpressRoute, deve ter pelo menos uma rede virtual Azure que esteja ligada através de um Gateway Virtual ao circuito VPN ou ExpressRoute. Em implementações simples, o Gateway Virtual pode ser implantado numa subnet da rede virtual Azure (VNet) que acolhe também as instâncias SAP HANA. Para instalar o SAP HANA, cria duas subredes adicionais dentro da rede virtual Azure. Uma subnet acolhe os VMs para executar os casos SAP HANA. A outra subnet executa a Jumpbox ou Management VMs para hospedar o SAP HANA Studio, outro software de gestão ou o seu software de aplicação.

> [!IMPORTANT]
> Fora da funcionalidade, mas mais importante por razões de desempenho, não é suportado para configurar [os Aparelhos Virtuais](https://azure.microsoft.com/solutions/network-appliances/) da Rede Azure na via de comunicação entre a aplicação SAP e a camada DBMS de um sistema SAP NetWeaver, Hybris ou S/4HANA baseado em SAP. A comunicação entre a camada de aplicação SAP e a camada DBMS tem de ser direta. A restrição não inclui [as regras Azure ASG e NSG,](https://docs.microsoft.com/azure/virtual-network/security-overview) desde que as regras ASG e NSG permitam uma comunicação direta. Outros cenários em que os NVAs não são suportados estão em caminhos de comunicação entre Os VMs Azure que representam os nós do cluster Linux Pacemaker e dispositivos SBD, conforme descrito em [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Ou em caminhos de comunicação entre Os VMs Azure e o Windows Server SOFS configurados como descrito no [Cluster uma instância SAP ASCS/SCS num cluster de falhas do Windows utilizando uma quota de ficheiro no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). Os NVAs nos caminhos de comunicação podem facilmente duplicar a latência da rede entre dois parceiros de comunicação, podendo restringir a entrada em caminhos críticos entre a camada de aplicação SAP e a camada DBMS. Em alguns cenários observados com os clientes, os NVAs podem fazer com que os clusters Pacemaker Linux falhem nos casos em que as comunicações entre os nós do cluster Do Pacemaker Linux precisam de comunicar com o seu dispositivo SBD através de um NVA.  
> 

> [!IMPORTANT]
> Outro design que **não** é suportado é a segregação da camada de aplicação SAP e a camada DBMS em diferentes redes virtuais Azure que não são [espreitadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) umas com as outras. Recomenda-se segregar a camada de aplicação SAP e a camada DBMS utilizando subredes dentro de uma rede virtual Azure em vez de utilizar diferentes redes virtuais Azure. Se decidir não seguir a recomendação e, em vez disso, segregar as duas camadas em diferentes redes virtuais, as duas redes virtuais precisam de ser [espreitadas.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Esteja ciente de que o tráfego de rede entre duas redes virtuais [azure peered](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) está sujeito a custos de transferência. Com o enorme volume de dados em muitos Terabytes trocados entre a camada de aplicação SAP e os custos substanciais da camada DBMS podem ser acumulados se a camada de aplicação SAP e a camada DBMS estiverem segregadas entre duas redes virtuais azure peered. 

Quando instala os VMs para executar o SAP HANA, os VMs precisam:

- Dois NICs virtuais instalados: um NIC para ligar à sub-rede de gestão, e um NIC para ligar da rede no local ou de outras redes, à instância SAP HANA no VM Azure.
- Endereços IP privados estáticos que são implantados para ambos os NICs virtuais.

> [!NOTE]
> Deve atribuir endereços IP estáticos através de meios Azure para vNICs individuais. Não deve atribuir endereços IP estáticos dentro do sodo de hóspedes a um vNIC. Alguns serviços azure, como o Azure Backup Service, baseiam-se no facto de que pelo menos o vNIC primário está definido para o DHCP e não para endereços IP estáticos. Consulte também o documento [Troubleshoot Azure virtual machine backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Se necessitar de atribuir vários endereços IP estáticos a um VM, tem de atribuir vários vNICs a um VM.
>
>

No entanto, para implementações que são duradouras, é necessário criar uma arquitetura de rede de datacenter virtual em Azure. Esta arquitetura recomenda a separação do Azure VNet Gateway que se conecta às instalações a um Azure VNet separado. Este VNet separado deve alojar todo o tráfego que deixa no local ou na internet. Esta abordagem permite-lhe implementar software para auditoria e registo de tráfego que entra no centro de dados virtual em Azure neste hub separado VNet. Portanto, tem um VNet que acolhe todo o software e configurações que se relacionam com o tráfego dentro e de saída com a sua implementação azure.

Os artigos [Azure Virtual Datacenter: A Network Perspetive](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) e [Azure Virtual Datacenter e o Enterprise Control Plane](https://docs.microsoft.com/azure/architecture/vdc/) dão mais informações sobre a abordagem virtual do datacenter e o design relacionado azure VNet.


>[!NOTE]
>O tráfego que flui entre um hub VNet e o VNet falado usando [o pino Azure VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) está sujeito a [custos](https://azure.microsoft.com/pricing/details/virtual-network/)adicionais . Com base nesses custos, poderá ter de considerar fazer compromissos entre gerir um centro rigoroso e design de rede falado e executar [vários Gateways Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) que se conecta a 'porta-vozes' para contornar o peering vNet. No entanto, o Azure ExpressRoute Gateways também introduz [custos](https://azure.microsoft.com/pricing/details/vpn-gateway/) adicionais. Também pode encontrar custos adicionais para software de terceiros que utiliza para a exploração de tráfego de rede, auditoria e monitorização. Dependente dos custos de troca de dados através de VNet, espreitando de um lado e custos criados por gateways adicionais do Azure ExpressRoute Gateways e licenças adicionais de software, poderá decidir por microsegmentação dentro de uma VNet, utilizando subredes como unidade de isolamento em vez de VNets.


Para uma visão geral dos diferentes métodos de atribuição de endereços IP, consulte os tipos de [endereços IP e os métodos](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)de atribuição em Azure . 

Para vMs executando SAP HANA, você deve trabalhar com endereços IP estáticos atribuídos. A razão é que alguns atributos de configuração para endereços IP de referência HANA.

[Os Grupos de Segurança da Rede Azure (NSGs)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) são usados para direcionar o tráfego que é encaminhado para a instância SAP HANA ou a caixa de salto. Os NSGs e, [eventualmente, os Grupos](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) de Segurança de Aplicações estão associados à subnet SAP HANA e à sub-rede de gestão.

A imagem seguinte mostra uma visão geral de um esquema de implantação áspero para SAP HANA seguindo um hub e falou arquitetura VNet:

![Esquema de implantação áspero para SAP HANA](media/hana-vm-operations/hana-simple-networking-dmz.png)

Para implantar o SAP HANA em Azure sem uma ligação site-to-site, ainda pretende proteger a instância SAP HANA da internet pública e escondê-la atrás de um proxy avançado. Neste cenário básico, a implementação conta com serviços DeNs integrados azure para resolver nomes de anfitriões. Numa implantação mais complexa onde são utilizados endereços IP virados para o público, os serviços de DNS integrados azure são especialmente importantes. Utilize os NSGs Azure e [os Azure NVAs](https://azure.microsoft.com/solutions/network-appliances/) para controlar, monitorize o encaminhamento da internet para a sua arquitetura Azure VNet em Azure. A imagem seguinte mostra um esquema áspero para a implementação do SAP HANA sem uma ligação site-to-site em um hub e falou arquitetura VNet:
  
![Esquema de implantação áspero para SAP HANA sem uma ligação site-a-local](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

Outra descrição sobre como usar os NVAs Azure para controlar e monitorizar o acesso a partir da Internet sem o hub e a arquitetura VNet falada pode ser encontrada no artigo [Implementar aparelhos virtuais de rede altamente disponíveis.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Configurar a infraestrutura Azure para a escala SAP HANA
Para saber os tipos De VM Azure certificados para escala OLAP ou escala S/4HANA, consulte o diretório de [hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Uma marca de verificação na coluna 'Clustering' indica suporte scale-out. O tipo de aplicação indica se a escala OLAP ou a escala S/4HANA são suportadas. Para obter detalhes sobre os nós certificados em escala para cada um dos VMs, verifique os detalhes das entradas no VM SKU específico listado no diretório de hardware SAP HANA.

Os lançamentos mínimos de SO para a implementação de configurações de escala-out em VMs Azure, verifique os detalhes das entradas no VM SKU específico listado no diretório de hardware SAP HANA. De uma configuração de escala OLAP n-nó, um nó funciona como nó mestre. Os outros acenam até ao limite da lei de certificação como nó dos trabalhadores. Nós adicionais de espera não contam para o número de nós certificados

>[!NOTE]
> As implementações em escala Azure VM do SAP HANA com nó de espera só são possíveis utilizando o armazenamento de [Ficheiros Azure NetApp.](https://azure.microsoft.com/services/netapp/) Nenhum outro armazenamento Azure certificado SAP HANA permite a configuração de nódosos de espera SAP HANA
>

Para /hana/shared, recomendamos também a utilização de [Ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/). 

Um design básico típico para um único nó numa configuração de escala-out vai parecer:

![Básicos de escala de um único nó](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

A configuração básica de um nó VM para escala SAP HANA parece:

- Para **/hana/shared,** utiliza o serviço NFS nativo fornecido através do Azure NetApp Files. 
- Todos os outros volumes de disco não são partilhados entre os diferentes nós e não são baseados em NFS. As configurações e etapas de instalação para instalações HANA com **/hana/data** não partilhadas e **/hana/log** são fornecidas mais tarde neste documento. Para o armazenamento certificado hana que pode ser usado, consulte as configurações de [armazenamento de máquinas virtuais SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage).


Dimensionando os volumes ou discos, é necessário verificar o documento Requisitos de [Armazenamento SAP HANA TDI,](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)para o tamanho exigido dependente do número de nós dos trabalhadores. O documento lança uma fórmula que precisa de aplicar para obter a capacidade necessária do volume

Os outros critérios de design que são apresentados nos gráficos da configuração do nó único para um SAP HANA VM à escala é o VNet, ou melhor a configuração da sub-rede. A SAP recomenda vivamente a separação do cliente/aplicação que enfrenta o tráfego das comunicações entre os nós HANA. Como mostrado nos gráficos, este objetivo é alcançado por ter dois vNICs diferentes ligados ao VM. Ambos os vNICs estão em subredes diferentes, têm dois endereços IP diferentes. Em seguida, controla o fluxo de tráfego com regras de encaminhamento utilizando NSGs ou rotas definidas pelo utilizador.

Particularmente no Azure, não existem meios e métodos para impor a qualidade do serviço e as quotas em vNICs específicos. Como resultado, a separação entre cliente/aplicação e comunicação intra-nó não abre qualquer oportunidade de priorizar um fluxo de tráfego sobre o outro. Em vez disso, a separação continua a ser uma medida de segurança na proteção das comunicações intra-nó das configurações de escala para fora.  

>[!NOTE]
>A SAP recomenda a separação do tráfego de rede para o lado cliente/aplicação e tráfego intra-nó, conforme descrito neste documento. Portanto, é recomendado colocar uma arquitetura no lugar, como mostra o último gráfico. Consulte também a sua equipa de segurança e conformidade para obter requisitos que se desviem da recomendação 
>

Do ponto de vista da rede, a arquitetura de rede mínima exigida seria como:

![Básicos de escala de um único nó](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>Instalação de Escala SAP HANA n Azure
Instalando uma configuração SAP com escala, é necessário realizar passos difíceis de:

- Implantação de novas ou adaptação de uma infraestrutura Azure VNet existente
- Implantação dos novos VMs utilizando o Armazenamento Premium Gerido azure, volumes de discos Ultra e/ou volumes NFS com base na ANF
- - Adaptar o encaminhamento da rede para garantir que, por exemplo, a comunicação intra-nó entre VMs não seja encaminhada através de um [NVA](https://azure.microsoft.com/solutions/network-appliances/). 
- Instale o nó principal SAP HANA.
- Adaptar parâmetros de configuração do nó principal SAP HANA
- Continue com a instalação dos nódosos operários SAP HANA

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Instalação de SAP HANA em configuração scale-out
À medida que a sua infraestrutura Azure VM está implantada, e todos os outros preparativos estão feitos, você precisa instalar as configurações de escala SAP HANA nestas etapas:

- Instale o nó principal SAP HANA de acordo com a documentação da SAP
- Em caso de utilização do Armazenamento Azure Premium ou Ultra do disco com discos não partilhados de /hana/data e /hana/log, é necessário alterar o ficheiro global.ini e adicionar o parâmetro 'basepath_shared = não' ao ficheiro global.ini. Este parâmetro permite que o SAP HANA corra em escala para fora sem 'partilhado' **/hana/data** e **/hana/log** volumes entre os nós. Os detalhes estão documentados no [SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991). Se estiver a utilizar volumes NFS baseados em ANF para /hana/data e /hana/log, não precisa de fazer esta alteração
- Após a eventual mudança no parâmetro global.ini, reinicie a instância SAP HANA
- Adicione nódos os mais trabalhadores. Consulte também <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Especifique a rede interna para a comunicação inter-nó SAP HANA durante a instalação ou, posteriormente, utilizando, por exemplo, o hdblcm local. Para obter documentação mais detalhada, consulte também [a Nota SAP #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Os detalhes para configurar um sistema de escala SAP HANA com nó de espera no SUSE Linux são descritos em detalhe no [Deploy um sistema de escala SAP HANA com nó de espera em VMs Azure, utilizando ficheiros Azure NetApp no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse). Documentação equivalente para O Chapéu Vermelho pode ser encontrada no artigo [Implementar um sistema de escala SAP HANA com nó de espera em VMs Azure usando Ficheiros Azure NetApp em Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel). 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA Dynamic Tiering 2.0 para máquinas virtuais Azure

Além das certificações SAP HANA em VMs da série M, o SAP HANA Dynamic Tiering 2.0 também é suportado no Microsoft Azure (ver SAP HANA Dynamic Tiering links mais abaixo). Embora não haja diferença na instalação do produto ou na sua operação, por exemplo, através do Cockpit SAP HANA dentro de uma Máquina Virtual Azure, existem alguns itens importantes, que são obrigatórios para o suporte oficial no Azure. Estes pontos-chave são descritos abaixo. Ao longo do artigo, a abreviatura "DT 2.0" será usada em vez do nome completo Dynamic Tiering 2.0.

O SAP HANA Dynamic Tiering 2.0 não é suportado pela SAP BW ou pela S4HANA. Os principais casos de uso neste momento são aplicações nativas da HANA.


### <a name="overview"></a>Descrição geral

A imagem abaixo dá uma visão geral sobre o suporte dT 2.0 no Microsoft Azure. Existe um conjunto de requisitos obrigatórios, que devem ser seguidos para cumprir a certificação oficial:

- O DT 2.0 deve ser instalado num Azure VM dedicado. Pode não correr no mesmo VM onde o SAP HANA corre
- Os VMs SAP HANA e DT 2.0 devem ser implantados dentro do mesmo Azure Vnet
- Os VMs SAP HANA e DT 2.0 devem ser implantados com rede acelerada azure ativado
- Tipo de armazenamento para os DT 2.0 VMs deve ser Armazenamento Premium Azure
- Vários discos Azure devem ser ligados ao DT 2.0 VM
- É necessário criar uma raid de software / volume listrado (seja via lvm ou mdadm) usando striping através dos discos Azure

Mais detalhes serão explicados nas seguintes secções.

![Visão geral da arquitetura SAP HANA DT 2.0](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Azure Dedicado VM para SAP HANA DT 2.0

No Azure IaaS, o DT 2.0 só é suportado num VM dedicado. Não é permitido executar DT 2.0 no mesmo VM Azure onde a instância HANA está em execução. Inicialmente, dois tipos de VM podem ser utilizados para executar SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Consulte a descrição do tipo VM [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Dada a ideia básica de DT 2.0, que é sobre descarregar dados "quentes" para economizar custos, faz sentido utilizar os tamanhos de VM correspondentes. No entanto, não existe uma regra rigorosa relativamente às possíveis combinações. Depende da carga de trabalho específica do cliente.

As configurações recomendadas seriam:

| Tipo VM SAP HANA | Tipo DT 2.0 VM |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Todas as combinações de VMs certificados por SAP HANA com DT 2.0 VMs suportados (M64-32ms e E32sv3) são possíveis.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Rede Azure e SAP HANA DT 2.0

A instalação de DT 2.0 num VM dedicado requer a entrada da rede entre o DT 2.0 VM e o VM SAP HANA de 10 Gb mínimo. Portanto, é obrigatório colocar todos os VMs dentro do mesmo Azure Vnet e permitir o funcionamento acelerado do Azure.

Consulte informações adicionais sobre o Azure acelerado networking [aqui](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Armazenamento VM para SAP HANA DT 2.0

De acordo com a orientação de boas práticas dT 2.0, a entrada de IO do disco deve ser mínima de 50 MB/seg por núcleo físico. Olhando para as especificações para os dois tipos de VM Azure, que são suportados para DT 2.0 o limite máximo de entrada de IO do disco para o VM parece:

- E32sv3: 768 MB/seg (não cached), o que significa uma razão de 48 MB/seg por núcleo físico
- M64-32ms : 1000 MB/seg (não cached), o que significa uma razão de 62,5 MB/seg por núcleo físico

É necessário anexar vários discos Azure ao DT 2.0 VM e criar uma raid de software (striping) a nível de OS para atingir o limite máximo de entrada de disco por VM. Um único disco Azure não pode fornecer a entrada para atingir o limite máximo de VM a este respeito. O armazenamento Azure Premium é obrigatório para executar DT 2.0. 

- Detalhes sobre os tipos de discos azure disponíveis podem ser encontrados [aqui](../../windows/disks-types.md)
- Detalhes sobre a criação de raid de software via mdadm podem ser encontrados [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Detalhes sobre configurar LVM para criar um volume listrado para a entrada mál podem ser encontrados [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

Dependendo dos requisitos de tamanho, existem diferentes opções para alcançar a potência máxima de um VM. Aqui estão possíveis configurações de volume de dados para cada tipo DT 2.0 VM para alcançar o limite superior de saída de VM. O VM E32sv3 deve ser considerado como um nível de entrada para cargas de trabalho mais pequenas. No caso de não ser rápido o suficiente, pode ser necessário redimensionar o VM para M64-32ms.
Como o M64-32ms VM tem muita memória, a carga da OI pode não atingir o limite especialmente para a leitura de cargas de trabalho intensivas. Portanto, menos discos no conjunto de riscas podem ser suficientes dependendo da carga de trabalho específica do cliente. Mas para estar no lado seguro, as configurações do disco abaixo foram escolhidas para garantir a potência máxima:


| SKU da VM | Disco Config 1 | Config 2 do disco | Config de disco 3 | Disco Config 4 | Config 5 do disco | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3.5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2.5 TB | 6 x P15 -> 1.5 TB | 


Especialmente no caso de a carga de trabalho ser de leitura intensa, pode aumentar o desempenho da IO para ligar a cache do hospedeiro Azure "apenas para leitura", conforme recomendado para os volumes de dados do software de base de dados. Considerando que para o registo de transações o cache do disco de bordo Azure deve ser "nenhum". 

No que diz respeito ao tamanho do volume de registo, um ponto de partida recomendado é um heurístico de 15% do tamanho dos dados. A criação do volume de registo pode ser realizada utilizando diferentes tipos de discos Azure, dependendo dos requisitos de custo e de entrada. Para o volume de registo, é necessária uma alta entrada de I/S.  Em caso de utilização do tipo VM M64-32ms é obrigatório ativar o [Acelerador de Escrita](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). O Acelerador De Escrita Azure proporciona uma latência ótima de escrita de disco para o registo de transações (apenas disponível para série S). Existem alguns itens a considerar, embora como o número máximo de discos por tipo VM. Detalhes sobre o Acelerador de Escrita podem ser encontrados [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


Aqui estão alguns exemplos sobre dimensionar o volume de registo:

| tamanho do volume de dados e tipo de disco | volume de registo e config tipo disco 1 | volume de registo e config tipo disco 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2.5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Tal como para a escala SAP HANA, o diretório /hana/partilhado tem de ser partilhado entre o SAP HANA VM e o DT 2.0 VM. Recomenda-se a mesma arquitetura que para o SAP HANA usando VMs dedicados, que funcionam como um servidor NFS altamente disponível. Para fornecer um volume de cópia de segurança partilhado, o design idêntico pode ser utilizado. Mas cabe ao cliente se o HA seria necessário ou se bastasse apenas utilizar um VM dedicado com capacidade de armazenamento suficiente para funcionar como um servidor de backup.



### <a name="links-to-dt-20-documentation"></a>Links para a documentação DT 2.0 

- [SAP HANA Dynamic Tiering instalação e guia de atualização](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Tutoriais e recursos de tiering dinâmico SAP HANA](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [PoC de tiering dinâmico SAP HANA](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 melhorias dinâmicas de tiering](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operações para implantação de SAP HANA em VMs Azure
As seguintes secções descrevem algumas das operações relacionadas com a implantação de sistemas SAP HANA em VMs Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Back up e restaurar operações em VMs Azure
Os seguintes documentos descrevem como fazer cópias de tempo e restaurar a sua implantação SAP HANA:

- [Descrição geral da cópia de segurança do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Backup de nível de ficheiro SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Referência de instantâneo de armazenamento SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Iniciar e reiniciar VMs que contenham SAP HANA
Uma característica proeminente da nuvem pública de Azure é que você é cobrado apenas pelos seus minutos de computação. Por exemplo, quando se encerra um VM que está a executar o SAP HANA, só se cobra os custos de armazenamento durante esse período. Outra funcionalidade está disponível quando especifica endereços IP estáticos para os seus VMs na sua implementação inicial. Quando reinicia um VM que tem SAP HANA, o VM reinicia com os seus endereços IP anteriores. 


### <a name="use-saprouter-for-sap-remote-support"></a>Utilize o SAProuter para suporte remoto SAP
Se tem uma ligação site-a-local entre as suas localizações no local e o Azure, e está a executar componentes SAP, então provavelmente já está a executar o SAProuter. Neste caso, preencha os seguintes itens para suporte remoto:

- Mantenha o endereço IP privado e estático do VM que acolhe o SAP HANA na configuração SAProuter.
- Configure o NSG da subnet que acolhe o VM HANA para permitir o tráfego através da porta TCP/IP 3299.

Se estiver a ligar-se ao Azure através da internet e não tiver um router SAP para o VM com o SAP HANA, então precisa de instalar o componente. Instale o SAProuter num VM separado na sub-rede management. A imagem seguinte mostra um esquema áspero para a implementação do SAP HANA sem uma ligação site-a-local e com o SAProuter:

![Esquema de implantação áspero para SAP HANA sem uma ligação site-a-local e SAProuter](media/hana-vm-operations/hana-simple-networking-saprouter.png)

Certifique-se de instalar o SAProuter num VM separado e não no seu Jumpbox VM. O VM separado deve ter um endereço IP estático. Para ligar o seu SAProuter ao SAProuter que é hospedado pelo SAP, contacte o SAP para obter um endereço IP. (O SAProuter que é hospedado pela SAP é a contrapartida da instância SAProuter que instala no seu VM.) Utilize o endereço IP do SAP para configurar a sua instância SAProuter. Nas definições de configuração, a única porta necessária é a porta TCP 3299.

Para obter mais informações sobre como configurar e manter ligações de suporte remoto através do SAProuter, consulte a [documentação SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Alta disponibilidade com SAP HANA em VMs nativos de Azure
Se estiver a executar o SUSE Linux Enterprise Server ou o Red Hat, pode estabelecer um cluster Pacemaker com dispositivos STONITH. Pode utilizar os dispositivos para configurar uma configuração SAP HANA que utiliza replicação sincronizada com replicação do sistema HANA e falha automática. Para mais informações listadas na secção "próximos passos".

## <a name="next-steps"></a>Passos Seguintes
Conheça os artigos listados
- [Configurações de armazenamento de máquinas virtuais SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- [Implemente um sistema de escala SAP HANA com nó de standby em VMs Azure utilizando ficheiros Azure NetApp no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Implemente um sistema de escala SAP HANA com nó de standby em VMs Azure utilizando ficheiros Azure NetApp em Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [Alta disponibilidade de SAP HANA em VMs Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Alta disponibilidade de SAP HANA em VMs Azure em Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)

 

