---
title: Configurações e operações de infraestrutura SAP HANA no Azure | Microsoft Docs
description: Guia de operações para sistemas SAP HANA que são implantados em máquinas virtuais do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b55e979c3056f89eb76a1d2c86f9a770d2d3e05
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935399"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Configurações e operações de infraestrutura do SAP HANA no Azure
Este documento fornece diretrizes para configurar a infraestrutura do Azure e sistemas operacionais SAP HANA implantados em VMs (máquinas virtuais) nativas do Azure. O documento também inclui informações de configuração para SAP HANA escalar horizontalmente para o SKU da VM M128s. Este documento não pretende substituir a documentação padrão do SAP, que inclui o seguinte conteúdo:

- [Guia de administração do SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guias de instalação do SAP](https://service.sap.com/instguides)
- [Notas SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Pré-requisitos
Para usar este guia, você precisa de conhecimento básico dos seguintes componentes do Azure:

- [Máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Redes virtuais e rede do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Armazenamento do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Para saber mais sobre o SAP NetWeaver e outros componentes SAP no Azure, consulte a seção [SAP no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) da [documentação do Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerações sobre a configuração básica
As seções a seguir descrevem as considerações básicas de configuração para a implantação de sistemas SAP HANA em VMs do Azure.

### <a name="connect-into-azure-virtual-machines"></a>Conectar-se a máquinas virtuais do Azure
Conforme documentado no [Guia de planejamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), há dois métodos básicos para se conectar a VMs do Azure:

- Conecte-se pela Internet e por pontos de extremidade públicos em uma VM de salto ou na VM que está executando o SAP HANA.
- Conecte-se por meio de uma [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou do Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

A conectividade site a site via VPN ou ExpressRoute é necessária para cenários de produção. Esse tipo de conexão também é necessário para cenários de não produção que são alimentados em cenários de produção em que o software SAP está sendo usado. A imagem a seguir mostra um exemplo de conectividade entre sites:

![Conectividade entre sites](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Escolher tipos de VM do Azure
Os tipos de VM do Azure que podem ser usados para cenários de produção são listados na [documentação do SAP para IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, uma variedade maior de tipos de VM nativos do Azure está disponível.

>[!NOTE]
> Para cenários de não produção, use os tipos de VM listados no #1928533 de [Observação SAP](https://launchpad.support.sap.com/#/notes/1928533). Para o uso de VMs do Azure para cenários de produção, verifique SAP HANA VMs certificadas na [lista de plataformas de IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)SAP publicadas.

Implante as VMs no Azure usando:

- O portal do Azure.
- Azure PowerShell cmdlets.
- O CLI do Azure.

Você também pode implantar uma plataforma de SAP HANA instalada completa nos serviços de VM do Azure por meio da [plataforma de nuvem SAP](https://cal.sap.com/). O processo de instalação é descrito em [implantar SAP S/4HANA ou BW/4HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) ou com a automação liberada [aqui](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

>[!IMPORTANT]
> Para usar VMs M208xx_v2, você precisa ter cuidado ao selecionar a imagem do SUSE Linux na Galeria de imagens de VM do Azure. Para ler os detalhes, leia o artigo tamanhos de [máquina virtual com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series). O Red Hat ainda não tem suporte para usar o HANA em VMs da família Mv2. O planejamento atual é fornecer suporte para versões do Red Hat que executam o HANA na família de VMs Mv2 no T4/CY2019 
> 


### <a name="storage-configuration-for-sap-hana"></a>Configuração de armazenamento para SAP HANA
Para configurações de armazenamento e tipos de armazenamento a serem usados com SAP HANA no Azure, leia o documento [SAP Hana configurações de armazenamento de máquina virtual do Azure](./hana-vm-operations-storage.md)


### <a name="set-up-azure-virtual-networks"></a>Configurar redes virtuais do Azure
Quando houver conectividade site a site no Azure por meio de VPN ou ExpressRoute, você deverá ter pelo menos uma rede virtual do Azure conectada por meio de um gateway virtual para o circuito VPN ou ExpressRoute. Em implantações simples, o gateway virtual pode ser implantado em uma sub-rede da VNet (rede virtual) do Azure que hospeda as instâncias de SAP HANA também. Para instalar SAP HANA, você cria duas sub-redes adicionais na rede virtual do Azure. Uma sub-rede hospeda as VMs para executar as instâncias de SAP HANA. A outra sub-rede executa VMs de Jumpbox ou de gerenciamento para hospedar SAP HANA Studio, outro software de gerenciamento ou seu software de aplicativo.

> [!IMPORTANT]
> Fora de funcionalidade, mas mais importante por motivos de desempenho, não há suporte para configurar dispositivos de virtualização de [rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de um SAP NetWeaver, Hybris ou S/4HANA com base em SAP sistema. A comunicação entre a camada do aplicativo SAP e a camada do DBMS precisa ser direta. A restrição não inclui [as regras ASG e NSG do Azure](https://docs.microsoft.com/azure/virtual-network/security-overview) , contanto que essas regras ASG e NSG permitam uma comunicação direta. Outros cenários em que não há suporte para NVAs estão em caminhos de comunicação entre VMs do Azure que representam nós de cluster pacemaker do Linux e dispositivos SBD, conforme descrito em [alta disponibilidade para SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para SAP aplicativos](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Ou em caminhos de comunicação entre as VMs do Azure e o Windows Server SOFS configurados conforme descrito em [cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um compartilhamento de arquivos no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). O NVAs em caminhos de comunicação pode facilmente dobrar a latência de rede entre dois parceiros de comunicação, pode restringir a taxa de transferência em caminhos críticos entre a camada do aplicativo SAP e a camada do DBMS. Em alguns cenários observados com os clientes, o NVAs pode fazer com que os clusters do pacemaker Linux falhem nos casos em que as comunicações entre os nós do cluster Linux pacemaker precisam se comunicar com o dispositivo SBD por meio de um NVA.  
> 

> [!IMPORTANT]
> Outro design **sem** suporte é a diferenciação da camada do aplicativo SAP e da camada DBMS em diferentes redes virtuais do Azure que não são [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre si. É recomendável separar a camada do aplicativo SAP e a camada do DBMS usando sub-redes em uma rede virtual do Azure em vez de usar diferentes redes virtuais do Azure. Se você decidir não seguir a recomendação e, em vez disso, separar as duas camadas em uma rede virtual diferente, as duas redes virtuais precisarão ser [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Lembre-se de que o tráfego [](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) de rede entre duas redes virtuais emparelhadas do Azure é o assunto dos custos de transferência. Com o enorme volume de dados em muitos terabytes trocados entre a camada do aplicativo SAP e os custos substanciais da camada do DBMS podem ser acumulados se a camada do aplicativo SAP e a camada do DBMS estiverem segregas entre duas redes virtuais emparelhadas do Azure. 

Quando você instala as VMs para executar o SAP HANA, as VMs precisam de:

- Duas NICs virtuais instaladas: uma NIC para se conectar à sub-rede de gerenciamento e uma NIC para se conectar da rede local ou de outras redes, à instância de SAP HANA na VM do Azure.
- Endereços IP privados estáticos que são implantados para ambas as NICs virtuais.

> [!NOTE]
> Você deve atribuir endereços IP estáticos por meio do Azure significa para vNICs individuais. Você não deve atribuir endereços IP estáticos dentro do sistema operacional convidado a um vNIC. Alguns serviços do Azure, como o serviço de backup do Azure, dependem do fato de que pelo menos o vNIC primário está definido como DHCP e não para endereços IP estáticos. Consulte também o documento [solucionar problemas de backup de máquina virtual do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Se você precisar atribuir vários endereços IP estáticos a uma VM, será necessário atribuir vários vNICs a uma VM.
>
>

No entanto, para implantações que são desejadas, você precisa criar uma arquitetura de rede de datacenter virtual no Azure. Essa arquitetura recomenda a separação do gateway de VNet do Azure que se conecta ao local em uma VNet do Azure separada. Essa VNet separada deve hospedar todo o tráfego que deixa para o local ou para a Internet. Essa abordagem permite que você implante o software para auditoria e log de tráfego que entra no datacenter virtual no Azure nessa VNet de Hub separada. Portanto, você tem uma VNet que hospeda todo o software e as configurações relacionadas ao tráfego de entrada e saída para sua implantação do Azure.

Os artigos [datacenter virtual do Azure: Uma perspectiva](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) de rede e um datacenter [virtual do Azure e o plano de controle corporativo](https://docs.microsoft.com/azure/architecture/vdc/) fornecem mais informações sobre a abordagem de datacenter virtual e o design de VNet do Azure relacionado.


>[!NOTE]
>O tráfego que flui entre uma VNet de Hub e uma VNet spoke usando o [emparelhamento VNet do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) está sujeito a [custos](https://azure.microsoft.com/pricing/details/virtual-network/)adicionais. Com base nesses custos, talvez seja necessário considerar a realização de comprometeções entre executar um design de rede hub estrito e spoke e executar vários [gateways do Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) que você se conecte a ' spokes ' para ignorar o emparelhamento VNet. No entanto, os gateways do Azure ExpressRoute introduzem [custos](https://azure.microsoft.com/pricing/details/vpn-gateway/) adicionais também. Você também pode encontrar custos adicionais para o software de terceiros que você usa para registro em log de tráfego de rede, auditoria e monitoramento. Dependendo dos custos da troca de dados por meio do emparelhamento de VNet em um lado e dos custos criados por gateways adicionais do Azure ExpressRoute e por licenças de software adicionais, você pode decidir para a microsegmentação em uma VNet usando sub-redes como unidade de isolamento em vez de VNets.


Para obter uma visão geral dos diferentes métodos para atribuir endereços IP, consulte [tipos de endereço IP e métodos de alocação no Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Para VMs que executam SAP HANA, você deve trabalhar com endereços IP estáticos atribuídos. O motivo é que alguns atributos de configuração para os endereços IP de referência do HANA.

Os [NSGs (grupos de segurança de rede) do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) são usados para direcionar o tráfego que é roteado para a instância de SAP Hana ou o Jumpbox. O NSGs e eventualmente os [grupos de segurança de aplicativo](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) estão associados à sub-rede SAP Hana e à sub-rede de gerenciamento.

A imagem a seguir mostra uma visão geral de um esquema de implantação aproximado para SAP HANA seguindo uma arquitetura de VNet de Hub e spoke:

![Esquema de implantação aproximado para SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

Para implantar SAP HANA no Azure sem uma conexão site a site, você ainda deseja proteger a instância de SAP HANA da Internet pública e ocultá-la por trás de um proxy de encaminhamento. Neste cenário básico, a implantação depende dos serviços DNS internos do Azure para resolver nomes de host. Em uma implantação mais complexa em que os endereços IP voltados para o público são usados, os serviços DNS internos do Azure são especialmente importantes. Use o Azure NSGs e o [NVAs do Azure](https://azure.microsoft.com/solutions/network-appliances/) para controlar, monitorar o roteamento da Internet em sua arquitetura de VNet do Azure no Azure. A imagem a seguir mostra um esquema aproximado para a implantação de SAP HANA sem uma conexão site a site em uma arquitetura VNet de Hub e spoke:
  
![Esquema de implantação aproximado para SAP HANA sem uma conexão site a site](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Outra descrição sobre como usar o Azure NVAs para controlar e monitorar o acesso da Internet sem a arquitetura de VNet do Hub e do spoke pode ser encontrada no artigo implantar soluções de virtualização de [rede altamente disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Configurando a infraestrutura do Azure para SAP HANA escalar horizontalmente
A Microsoft tem uma SKU de VM da série M certificada para uma configuração de expansão SAP HANA. O tipo de VM M128s obteve certificado para uma expansão de até 16 nós. Para alterações em SAP HANA de certificação de expansão em VMs do Azure, verifique a [lista de plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

As versões mínimas do sistema operacional para implantar configurações de expansão nas VMs do Azure são:

- SUSE Linux 12 SP3
- Red Hat Linux 7,4

Da certificação de expansão de 16 nós

- Um nó é o nó mestre
- Um máximo de 15 nós são nós de trabalho

>[!NOTE]
>Em implantações de expansão de VM do Azure, não há possibilidade de usar um nó em espera
>

O motivo para não poder configurar um nó em espera é duplo:

- O Azure neste ponto não tem nenhum serviço NFS nativo. Como resultado, os compartilhamentos NFS precisam ser configurados com a ajuda de uma funcionalidade de terceiros.
- Nenhuma das configurações de NFS de terceiros é capaz de atender aos critérios de latência de armazenamento para SAP HANA com suas soluções implantadas no Azure.

Como resultado, os volumes **/Hana/data** e **/Hana/log** não podem ser compartilhados. Não compartilhar esses volumes de nós únicos impede o uso de um SAP HANA nó em espera em uma configuração de expansão.

Como resultado, o design básico de um único nó em uma configuração de expansão terá a seguinte aparência:

![Noções básicas de expansão de um único nó](media/hana-vm-operations/scale-out-basics.PNG)

A configuração básica de um nó de VM para SAP HANA expansão é semelhante a:

- Para o **/Hana/Shared**, você cria um cluster NFS altamente disponível com base no SUSE Linux 12 SP3. Esse cluster hospeda os compartilhamentos NFS **/Hana/Shared** de sua configuração de expansão e os serviços centrais do SAP NetWeaver ou BW/4HANA. A documentação para criar essa configuração está disponível no artigo [alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Todos os outros volumes de disco **não** são compartilhados entre os diferentes nós e **não** são baseados em NFS. As configurações de instalação e as etapas para instalações de expansão do HANA com **/Hana/data** e **/Hana/log** não compartilhados são fornecidas mais adiante neste documento.

>[!NOTE]
>O cluster NFS altamente disponível, conforme exibido nos gráficos até o momento, tem suporte apenas para o SUSE Linux. Uma solução NFS altamente disponível com base no Red Hat será aconselhada posteriormente.

O dimensionamento dos volumes para os nós é o mesmo para escalar verticalmente, exceto **/Hana/Shared**. Para o SKU da VM M128s, os tamanhos e tipos sugeridos são semelhantes a:

| SKU DA VM | RAM | Um máximo de E/S DE VM<br /> Débito | /Hana/data | /Hana/log | /root volume | /usr/sap | Hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Verifique se a taxa de transferência de armazenamento para os diferentes volumes sugeridos atende à carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para **/Hana/data** e **/Hana/log**, você precisará aumentar o número de VHDs de armazenamento Premium do Azure. O dimensionamento de um volume com mais VHDs do que o listado aumenta a taxa de transferência de IOPS e e/s dentro dos limites do tipo de máquina virtual do Azure. Também aplique o Azure Acelerador de Gravação aos discos que formam o volume **/Hana/log** .
 
No documento [SAP Hana requisitos de armazenamento TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), uma fórmula é chamada que define o tamanho do volume **/Hana/Shared** para escalar horizontalmente como o tamanho da memória de um único nó de trabalho por quatro nós de trabalho.

Supondo que você tenha o SAP HANA a VM do Azure com certificação de expansão M128s com aproximadamente 2 TB de memória, as recomendações do SAP podem ser resumidas como:

- Um nó mestre e até quatro nós de trabalho, o volume **/Hana/Shared** precisaria de 2 TB de tamanho. 
- Um nó mestre e cinco a oito nós de trabalho, o tamanho de **/Hana/Shared** deve ser 4 TB. 
- Um nó mestre e 9 a 12 nós de trabalho, um tamanho de 6 TB para **/Hana/Shared** seria necessário. 
- Um nó mestre e usando entre 12 e 15 nós de trabalho, você deve fornecer um volume **/Hana/Shared** que tenha 8 TB de tamanho.

O outro design importante que é exibido nos elementos gráficos da configuração de nó único para uma expansão SAP HANA VM é a VNet ou melhor a configuração de sub-rede. O SAP altamente recomenda uma separação do tráfego voltado para o cliente/aplicativo das comunicações entre os nós do HANA. Conforme mostrado nos gráficos, essa meta é obtida com dois vNICs diferentes anexados à VM. Ambos os vNICs estão em sub-redes diferentes, têm dois endereços IP diferentes. Em seguida, você controla o fluxo de tráfego com as regras de roteamento usando NSGs ou rotas definidas pelo usuário.

Particularmente no Azure, não há meios e métodos para impor a qualidade de serviço e cotas em vNICs específicas. Como resultado, a separação de comunicação de cliente/aplicativo voltada e de nó interno não abre nenhuma oportunidade para priorizar um fluxo de tráfego pelo outro. Em vez disso, a separação continua sendo uma medida de segurança na blindagem das comunicações intra-node das configurações de expansão.  

>[!IMPORTANT]
>O SAP altamente recomenda separar o tráfego de rede para o lado do cliente/aplicativo e o tráfego dentro do nó, conforme descrito neste documento. Portanto, colocar uma arquitetura em vigor, conforme mostrado nos últimos gráficos, é altamente recomendável.
>

De um ponto de vista de rede, a arquitetura de rede mínima necessária seria semelhante a:

![Noções básicas de expansão de um único nó](media/hana-vm-operations/scale-out-networking-overview.PNG)

Os limites com suporte até agora são 15 trabalhadores adicionais para o nó mestre.

De um ponto de armazenamento de exibição, a arquitetura de armazenamento ficaria assim:


![Noções básicas de expansão de um único nó](media/hana-vm-operations/scale-out-storage-overview.PNG)

O volume **/Hana/Shared** está localizado na configuração de compartilhamento NFS altamente disponível. Enquanto todas as outras unidades são "localmente" montadas nas VMs individuais. 

### <a name="highly-available-nfs-share"></a>Compartilhamento NFS altamente disponível
O cluster NFS altamente disponível até agora está trabalhando apenas com o SUSE Linux. O documento [alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) descreve como configurá-lo. Se você não compartilhar o cluster NFS com outras configurações do HANA fora da VNet do Azure que executa as instâncias de SAP HANA, instale-o na mesma VNet. Instale-o em sua própria sub-rede e certifique-se de que nem todo o tráfego arbitrário possa acessar a sub-rede. Em vez disso, você deseja limitar o tráfego para essa sub-rede aos endereços IP da VM que executa o tráfego para o volume **/Hana/Shared** .

Relacionado ao vNIC de uma VM de expansão do HANA que deve rotear o tráfego **/Hana/Shared** , as recomendações são:

- Como o tráfego para **/Hana/Shared** é moderado, encaminhe-o por meio do vNIC atribuído à rede do cliente na configuração mínima
- Eventualmente, para o tráfego para **/Hana/Shared**, implante uma terceira sub-rede na VNet você implanta a configuração de expansão SAP Hana e atribui um terceiro vNIC que está hospedado nessa sub-rede. Use o terceiro vNIC e o endereço IP associado para o tráfego para o compartilhamento NFS. Em seguida, você pode aplicar regras de roteamento e acesso separadas.

>[!IMPORTANT]
>O tráfego de rede entre as VMs que têm SAP HANA de uma maneira de expansão implantada e o NFS altamente disponível pode estar sob nenhuma circunstância por meio de um [NVA](https://azure.microsoft.com/solutions/network-appliances/) ou de dispositivos virtuais semelhantes. Enquanto o Azure NSGs não são esses dispositivos. Verifique suas regras de roteamento para certificar-se de que NVAs ou dispositivos virtuais semelhantes sejam desviados ao acessar o compartilhamento NFS altamente disponível das VMs em execução SAP HANA.
> 

Se você quiser compartilhar o cluster NFS altamente disponível entre configurações de SAP HANA, mova todas essas configurações do HANA para a mesma VNet. 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>Instalando o SAP HANA escalar horizontalmente n Azure
Instalando uma configuração SAP de expansão, você precisa executar as etapas aproximadas de:

- Implantando novas ou adaptando uma infraestrutura de VNet do Azure existente
- Implantando as novas VMs usando volumes de armazenamento Premium gerenciados do Azure
- Implantando um cluster NFS altamente disponível novo ou adaptado a um existente
- Adapte o roteamento de rede para garantir que, por exemplo, a comunicação entre nós entre as VMs não seja roteada por um [NVA](https://azure.microsoft.com/solutions/network-appliances/). O mesmo acontece com o tráfego entre as VMs e o cluster NFS altamente disponível.
- Instale o nó mestre de SAP HANA.
- Adaptar parâmetros de configuração do nó mestre de SAP HANA
- Continue com a instalação do SAP HANA nós de trabalho

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Instalação de SAP HANA na configuração de expansão
À medida que sua infraestrutura de VM do Azure é implantada e todas as outras preparações são feitas, você precisa instalar as SAP HANA configurações de expansão nestas etapas:

- Instalar o nó mestre de SAP HANA de acordo com a documentação da SAP
- **Após a instalação, você precisa alterar o arquivo global. ini e adicionar o parâmetro ' basepath_shared = no ' ao global. ini**. Esse parâmetro permite que SAP HANA seja executado em expansão sem os volumes ' Shared ' **/Hana/data** e **/Hana/log** entre os nós. Os detalhes estão documentados no [SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991).
- Depois de alterar o parâmetro global. ini, reinicie a instância de SAP HANA
- Adicione mais nós de trabalho. Consulte também <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Especifique a rede interna para SAP HANA comunicação entre nós durante a instalação ou posteriormente usando, por exemplo, o hdblcm local. Para obter uma documentação mais detalhada, consulte também [SAP Note #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Após essa rotina de instalação, a configuração de expansão que você instalou vai usar discos não compartilhados para executar **/Hana/data** e **/Hana/log**. Enquanto o volume **/Hana/Shared** será colocado no compartilhamento NFS altamente disponível.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA camadas dinâmicas 2,0 para máquinas virtuais do Azure

Além das certificações SAP HANA nas VMs da série M do Azure, também há suporte para SAP HANA camada dinâmica 2,0 em Microsoft Azure (consulte SAP HANA links de documentação de camadas dinâmicas mais abaixo). Embora não haja nenhuma diferença na instalação do produto ou na operação dele, por exemplo, via SAP HANA cockpit dentro de uma máquina virtual do Azure, há alguns itens importantes, que são obrigatórios para o suporte oficial no Azure. Esses pontos-chave são descritos abaixo. Em todo o artigo, a abreviação "DT 2,0" será usada em vez do nome completo camada dinâmica 2,0.

SAP HANA camada dinâmica 2,0 não tem suporte pelo SAP BW ou S4HANA. Os principais casos de uso agora são aplicativos nativos do HANA.


### <a name="overview"></a>Descrição geral

A figura abaixo fornece uma visão geral sobre o suporte a DT 2,0 em Microsoft Azure. Há um conjunto de requisitos obrigatórios, que deve ser seguido para estar em conformidade com a certificação oficial:

- DT 2,0 deve ser instalado em uma VM dedicada do Azure. Ele pode não ser executado na mesma VM em que SAP HANA é executado
- As VMs SAP HANA e DT 2,0 devem ser implantadas na mesma vnet do Azure
- As VMs SAP HANA e DT 2,0 devem ser implantadas com a rede acelerada do Azure habilitada
- O tipo de armazenamento para as VMs de DT 2,0 deve ser o armazenamento Premium do Azure
- Vários discos do Azure devem ser anexados à VM DT 2,0
- É necessário criar um volume RAID/distribuído de software (via LVM ou mdadm) usando a distribuição entre os discos do Azure

Mais detalhes serão explicados nas seções a seguir.

![Visão geral da arquitetura do SAP HANA DT 2,0](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>VM dedicada do Azure para SAP HANA DT 2,0

No Azure IaaS, o DT 2,0 só tem suporte em uma VM dedicada. Não é permitido executar DT 2,0 na mesma VM do Azure em que a instância do HANA está em execução. Inicialmente, dois tipos de VM podem ser usados para executar SAP HANA DT 2,0:

- M64-32ms 
- E32sv3 

Consulte a descrição do tipo de VM [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Considerando a ideia básica de DT 2,0, que é sobre o descarregamento de dados "quentes" para economizar custos, faz sentido usar tamanhos de VM correspondentes. No entanto, não há nenhuma regra estrita em relação às combinações possíveis. Depende da carga de trabalho específica do cliente.

As configurações recomendadas seriam:

| SAP HANA tipo de VM | Tipo de VM DT 2,0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Todas as combinações de VMs da série M com certificação SAP HANA com VMs de DT 2,0 com suporte (m64-32ms Standard e E32sv3) são possíveis.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Rede do Azure e SAP HANA DT 2,0

Instalar o DT 2,0 em uma VM dedicada requer a taxa de transferência de rede entre a VM de DT 2,0 e a VM de SAP HANA de 10 GB no mínimo. Portanto, é obrigatório posicionar todas as VMs na mesma vnet do Azure e habilitar a rede acelerada do Azure.

Consulte informações adicionais sobre a rede acelerada do Azure [aqui](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Armazenamento de VM para SAP HANA DT 2,0

De acordo com as diretrizes de práticas recomendadas de DT 2,0, a taxa de transferência de e/s de disco deve ser mínima 50 MB/s por núcleo físico. Examinando a especificação dos dois tipos de VM do Azure, que têm suporte para DT 2,0, o limite máximo de taxa de transferência de e/s de disco para a VM é semelhante a:

- E32sv3    :   768 MB/s (não armazenado em cache), o que significa uma taxa de 48 MB/s por núcleo físico
- M64-32ms Standard:  1000 MB/s (não armazenado em cache), o que significa uma taxa de 62,5 MB/s por núcleo físico

É necessário anexar vários discos do Azure à VM DT 2,0 e criar um RAID de software (distribuição) no nível do sistema operacional para atingir o limite máximo de taxa de transferência de disco por VM. Um único disco do Azure não pode fornecer a taxa de transferência para alcançar o limite máximo de VM nesse aspecto. O armazenamento Premium do Azure é obrigatório para executar DT 2,0. 

- Os detalhes sobre os tipos de disco disponíveis do Azure podem ser encontrados [aqui](../../windows/disks-types.md)
- Detalhes sobre a criação de RAID de software por meio do mdadm podem ser encontrados [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Detalhes sobre como configurar o LVM para criar um volume distribuído para obter a taxa de transferência máxima podem ser encontrados [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

Dependendo dos requisitos de tamanho, há opções diferentes para alcançar a taxa de transferência máxima de uma VM. Aqui estão as configurações de disco de volume de dados possíveis para cada tipo de VM DT 2,0 para atingir o limite de taxa de transferência de VM superior. A VM E32sv3 deve ser considerada um nível de entrada para cargas de trabalho menores. Caso seja preciso redimensionar que não é rápido o suficiente, pode ser necessário redimensionar a VM para m64-32ms Standard.
Como a VM m64-32ms Standard tem muita memória, a carga de e/s pode não alcançar o limite especialmente para cargas de trabalho com uso intensivo de leitura. Portanto, menos discos no conjunto de distribuição podem ser suficientes dependendo da carga de trabalho específica do cliente. Mas, para estar no lado seguro, as configurações de disco abaixo foram escolhidas para garantir a taxa de transferência máxima:


| SKU DA VM | Configuração de disco 1 | Configuração de disco 2 | Configuração de disco 3 | Configuração de disco 4 | Configuração de disco 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20-> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20-> 2,5 TB | 6 x P15-> 1,5 TB | 


Especialmente no caso de a carga de trabalho ser de leitura intensa, a ti pode aumentar o desempenho de e/s para ativar o cache de host do Azure "somente leitura", conforme recomendado para os volumes de dados do software de banco. Enquanto para o log de transações, o cache de disco do host do Azure deve ser "None". 

Em relação ao tamanho do volume do log, um ponto de partida recomendado é uma heurística de 15% do tamanho dos dados. A criação do volume de log pode ser realizada usando diferentes tipos de disco do Azure, dependendo dos requisitos de custo e de taxa de transferência. Para o volume de log, a taxa de transferência de e/s alta é necessária.  No caso de usar o tipo de VM m64-32ms Standard, é altamente recomendável habilitar [acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). O Azure Acelerador de Gravação fornece latência de gravação de disco ideal para o log de transações (disponível somente para a série M). Há alguns itens a considerar, embora como o número máximo de discos por tipo de VM. Detalhes sobre Acelerador de Gravação podem ser encontrados [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


Aqui estão alguns exemplos de como dimensionar o volume de log:

| tamanho do volume de dados e tipo de disco | volume de log e configuração de tipo de disco 1 | volume de log e configuração de disco 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20-> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15-> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Assim como para SAP HANA expansão, o diretório/Hana/Shared precisa ser compartilhado entre a VM SAP HANA e a VM DT 2,0. A mesma arquitetura do para SAP HANA expansão usando VMs dedicadas, que atuam como um servidor NFS altamente disponível, é recomendada. Para fornecer um volume de backup compartilhado, o design idêntico pode ser usado. Mas cabe ao cliente se a HA fosse necessária ou se é suficiente apenas usar uma VM dedicada com capacidade de armazenamento suficiente para atuar como um servidor de backup.



### <a name="links-to-dt-20-documentation"></a>Links para a documentação do DT 2,0 

- [Guia de instalação e atualização de camadas dinâmicas do SAP HANA](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Tutoriais e recursos de camadas dinâmicas do SAP HANA](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [VDC SAP HANA de camadas dinâmicas](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA os aprimoramentos de camadas dinâmicas do 2,0 SPS 02](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operações para implantação de SAP HANA em VMs do Azure
As seções a seguir descrevem algumas das operações relacionadas à implantação de sistemas SAP HANA em VMs do Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Operações de backup e restauração em VMs do Azure
Os documentos a seguir descrevem como fazer backup e restaurar sua implantação do SAP HANA:

- [Descrição geral da cópia de segurança do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA backup em nível de arquivo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Parâmetro de comparação de instantâneo de armazenamento SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Iniciar e reiniciar VMs que contêm SAP HANA
Um recurso proeminente da nuvem pública do Azure é que você será cobrado apenas pelos seus minutos de computação. Por exemplo, ao desligar uma VM que está executando SAP HANA, você será cobrado apenas pelos custos de armazenamento durante esse tempo. Outro recurso está disponível quando você especifica endereços IP estáticos para suas VMs em sua implantação inicial. Quando você reinicia uma VM que tem SAP HANA, a VM é reiniciada com seus endereços IP anteriores. 


### <a name="use-saprouter-for-sap-remote-support"></a>Usar o SAProuter para suporte remoto do SAP
Se você tiver uma conexão site a site entre seus locais e o Azure, e estiver executando componentes SAP, provavelmente já está executando o SAProuter. Nesse caso, conclua os seguintes itens para o suporte remoto:

- Mantenha o endereço IP estático e privado da VM que hospeda SAP HANA na configuração SAProuter.
- Configure o NSG da sub-rede que hospeda a VM do HANA para permitir o tráfego por meio da porta TCP/IP 3299.

Se você estiver se conectando ao Azure por meio da Internet e não tiver um roteador SAP para a VM com SAP HANA, será necessário instalar o componente. Instale o SAProuter em uma VM separada na sub-rede de gerenciamento. A imagem a seguir mostra um esquema aproximado para a implantação de SAP HANA sem uma conexão site a site e com o SAProuter:

![Esquema de implantação aproximado para SAP HANA sem uma conexão site a site e SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Certifique-se de instalar o SAProuter em uma VM separada e não em sua VM Jumpbox. A VM separada deve ter um endereço IP estático. Para conectar seu SAProuter ao SAProuter que é hospedado pela SAP, entre em contato com a SAP para obter um endereço IP. (O SAProuter que é hospedado pelo SAP é o equivalente da instância de SAProuter que você instala em sua VM.) Use o endereço IP do SAP para configurar sua instância do SAProuter. Nas definições de configuração, a única porta necessária é a porta TCP 3299.

Para obter mais informações sobre como configurar e manter conexões de suporte remoto por meio do SAProuter, consulte a [documentação do SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Alta disponibilidade com o SAP HANA em VMs nativas do Azure
Se estiver executando SUSE Linux Enterprise Server para aplicativos SAP 12 SP1 ou posterior, você poderá estabelecer um cluster pacemaker com dispositivos STONITH. Você pode usar os dispositivos para definir uma configuração de SAP HANA que usa replicação síncrona com replicação de sistema do HANA e failover automático. Para obter mais informações sobre o procedimento de instalação, consulte [SAP Hana guia de alta disponibilidade para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
