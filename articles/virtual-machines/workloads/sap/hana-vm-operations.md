---
title: Configurações de infraestrutura de SAP HANA e operações no Azure | Documentos da Microsoft
description: Guia de operações para os sistemas SAP HANA que são implementados em máquinas virtuais do Azure.
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
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 74b1ed79e04bcca05dcb5308b844622e4dd413ce
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410303"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Configurações de infraestrutura de SAP HANA e operações no Azure
Este documento fornece orientações para configurar a infraestrutura do Azure e operar sistemas SAP HANA que estão implementados em máquinas de virtuais (VMs) nativas do Azure. O documento também inclui informações de configuração para o SAP HANA aumentar horizontalmente para o SKU de VM M128s. Este documento não se destina a substituir a documentação de SAP padrão, o que inclui o seguinte conteúdo:

- [Guia de administração do SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guias de instalação SAP](https://service.sap.com/instguides)
- [Notas SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar este guia, tem um conhecimento básico dos seguintes componentes do Azure:

- [Máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Redes do Azure e redes virtuais](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Armazenamento do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Para saber mais sobre o SAP NetWeaver e outros componentes SAP no Azure, veja a [SAP no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) secção a [documentação do Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerações sobre a configuração básica
As secções seguintes descrevem as considerações de configuração básica para implementação de sistemas do SAP HANA em VMs do Azure.

### <a name="connect-into-azure-virtual-machines"></a>Ligue-se em máquinas virtuais do Azure
Conforme documentado no [máquinas virtuais do Azure, guia de planejamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), existem dois métodos básicos para ligar em VMs do Azure:

- Ligar através da internet e os pontos finais públicos numa VM atalhos ou na VM que está a executar o SAP HANA.
- Ligar através de um [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou do Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Conectividade de site a site via VPN ou ExpressRoute é necessária para cenários de produção. Este tipo de ligação também é necessário para cenários de não produção esse feed em cenários de produção em que está a ser utilizado SAP software. A imagem seguinte mostra um exemplo de conectividade entre sites:

![Conectividade entre sites](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Escolha os tipos de VM do Azure
Os tipos de VM do Azure que podem ser utilizados para cenários de produção estão listados na [documentação de SAP para o IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, uma maior variedade de tipos nativos de VM do Azure está disponível.

>[!NOTE]
> Para cenários de não produção, utilize os tipos VM que estão listados na [a nota SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). A utilização de VMs do Azure para cenários de produção, verifique as VMs no SAP publicados com certificação SAP HANA [lista de plataformas de IaaS Certified](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Implemente as VMs no Azure ao utilizar:

- O portal do Azure.
- Cmdlets do PowerShell do Azure.
- A CLI do Azure.

Também pode implementar uma plataforma completa de SAP HANA instalada nos serviços de VM do Azure através da [plataforma na Cloud de SAP](https://cal.sap.com/). O processo de instalação é descrito em [SAP implementar S/4HANA ou BW/4HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) ou com a automação lançado [aqui](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Escolha o tipo de armazenamento do Azure
O Azure fornece dois tipos de armazenamento que são adequados para as VMs do Azure que estejam a executar o SAP HANA: Unidades de disco rígido Standard (HDD) e unidades de estado sólido (SSD) do premium. Para saber mais sobre estes tipos de discos, consulte nosso artigo [selecione um tipo de disco](../../windows/disks-types.md)

- Unidades de disco rígido Standard (HDD)
- Unidades de estado sólido (SSD) do Premium

O Azure oferece dois métodos de implantação para VHDs no padrão do Azure e o armazenamento Premium. Se o cenário geral permite, aproveitar [disco gerido do Azure](https://azure.microsoft.com/services/managed-disks/) implementações.

Para obter uma lista de tipos de armazenamento e os respetivos SLAs no débito IOPS e o armazenamento, reveja os [documentação do Azure para discos geridos](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Configurar o armazenamento para máquinas virtuais do Azure

Até agora nunca teve que se preocupa com os subsistemas de e/s e as respetivas capacidades. Motivo era que o fornecedor da aplicação necessária para se certificar de que são cumpridos os requisitos de armazenamento mínima para o SAP HANA. À medida que cria a infraestrutura do Azure por conta própria, também deve estar ciente de alguns dos requisitos. E também a compreender os requisitos de configuração sugeridos nas secções seguintes. Ou, para casos em que está a configurar as máquinas virtuais que pretende executar SAP HANA no. Algumas das características que são solicitadas são resultando na necessidade de:

- Ative o volume de leitura/escrita no **/hana/do registo** de 250 MB/seg no mínimo com tamanhos de e/s de 1 MB
- Ativar ler a atividade de, pelo menos, de 400 MB/seg para **dados/hana/** para os tamanhos de e/s de 16 MB e de 64 MB
- Ativar a atividade de escrita de, pelo menos, 250 MB/seg para **dados/hana/** com tamanhos de e/s de 16 MB e de 64 MB

Dado que o armazenamento baixo latência é fundamental para sistemas DBMS, mesmo quando o DBMS, como o SAP HANA, manter os dados na memória. O caminho crítico no armazenamento é normalmente em torno as gravações de log de transação dos sistemas DBMS. Mas também gostam de operações de escrita de pontos de reposição ou carregamento de dados na memória depois de recuperação contra panes pode ser considerado crítica. Por conseguinte, é obrigatório para tirar partido dos discos Premium do Azure **/hana/data** e **/hana/do registo** volumes. Para alcançar o débito mínimo de **/hana/do registo** e **/hana/dados** como pretendido, SAP, tem de criar um RAID 0 usando MDADM ou LVM sobre vários discos de armazenamento Premium do Azure. E utilizar os volumes RAID como **/hana/data** e **/hana/do registo** volumes. Como o stripe tamanhos para o RAID 0, a recomendação é usar:

- 64 KB ou 128 KB para   **/hana/dados**
- 32 KB para   **/hana/do registo**

> [!NOTE]
> Não precisa de configurar qualquer nível de redundância através de volumes de RAID, uma vez que o armazenamento do Azure Premium e Standard mantêm três imagens de um VHD. A utilização de um volume RAID é puramente configurar volumes que fornecem o débito de e/s suficiente.

Acumular um número de VHDs do Azure por baixo de uma RAID, é accumulative a partir de um lado de débito do armazenamento e de IOPS. Então, se colocar um RAID 0 através de discos de Premium Storage do Azure P30 x 3, ele deverá dar-lhe três vezes o IOPS e três vezes o débito de armazenamento de um único disco P30 de armazenamento do Azure Premium.

As recomendações de colocação em cache abaixo são supondo que as características de e/s para o SAP HANA essa lista, como:

- Há quase qualquer carga de trabalho de leitura nos arquivos de dados HANA. As exceções são grandes e/SS de tamanho após o reinício da instância do HANA ou quando dados são carregados em HANA. Outro caso de maior de leitura que e/s nos arquivos de dados pode ser backups de banco de dados do HANA. Como resultado principalmente a cache de leitura não faz sentido desde na maioria dos casos, todos os volumes de dados de ficheiros tem de ser totalmente lidas.
- Escrever contra os ficheiros de dados se verificarem em picos com base em pontos de reposição do HANA e recuperação de falhas do HANA. Pontos de reposição de escrita é assíncrono e não estão obstruindo de quaisquer transações de utilizador. Escrita de dados durante a recuperação de falhas é crítico de desempenho para o sistema a responder rapidamente novamente. No entanto, a recuperação de falhas deve ser situações excecionais em vez disso
- Há um mínimo de leituras dos ficheiros de Refazer do HANA. As exceções são grandes e/ss ao realizar backups de log de transação, recuperação de falha, ou na fase de reinício de uma instância do HANA.  
- Principal de carga com o arquivo de log de retrabalho SAP HANA é escritas. Depende da natureza da carga de trabalho, pode ter e/ss tão pequena como 4 KB ou em outros casos de tamanhos de e/s de 1 MB ou mais. Escreva a latência contra o log de retrabalho de SAP HANA é o desempenho crítico.
- Todas as gravações precisam ser mantidos no disco de forma fiável

Como resultado desses padrões de e/s observados pelo SAP HANA, deve ser definida a colocação em cache para os diferentes volumes com o armazenamento Premium do Azure, como:

- **dados/hana/** -sem colocação em cache
- **/ hana/do registo** - sem colocação em cache - exceção para a série M (ver mais tarde neste documento)
- **/ hana/partilhado** - leitura a colocação em cache


Tenha também o débito de e/s VM global em mente ao dimensionamento ou decidir para uma VM. Em geral o débito do armazenamento VM está documentado no artigo [tamanhos de máquinas virtuais com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="linux-io-scheduler-mode"></a>Modo de agendador de e/s do Linux
Linux tem vários modos de agendamento de e/s diferentes. Uma recomendação comum através de fornecedores de Linux e SAP é definir o modo de agendador de e/s para volumes de disco do **cfq** modo para o **noop** modo. Detalhes são referenciados numa [SAP Note #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Solução de armazenamento com acelerador de escrita do Azure para máquinas de virtuais de série M do Azure
Acelerador de escrita do Azure é uma funcionalidade que é obter implementada para VMs de série M Azure exclusivamente. Como o nome indica, a finalidade da funcionalidade é melhorar a latência de e/s de escrita contra o armazenamento Premium do Azure. Para o SAP HANA, acelerador de escrita deve ser utilizado em relação a **/hana/do registo** apenas o volume. Por conseguinte, as configurações mostradas até agora precisam de ser alteradas. A principal mudança é a divisão entre o **/hana/data** e **/hana/do registo** para poder utilizar o acelerador de escrita do Azure em relação a **/hana/do registo** apenas o volume. 

> [!IMPORTANT]
> A certificação do SAP HANA para máquinas de virtuais de série M do Azure é exclusivamente com acelerador de escrita do Azure para o **/hana/do registo** volume. Como resultado, implementações de SAP HANA de cenário de produção em máquinas de virtuais de série M Azure devem ser configurados com acelerador de escrita do Azure para o **/hana/do registo** volume.  

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo VM é suportado para o SAP HANA pela SAP no [documentação de SAP para o IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

As configurações recomendadas ter o seguinte aspeto:

| SKU DE VM | RAM | Um máximo de E/S DE VM<br /> Débito | /hana/data | / hana/do registo | /hana/shared | volume de /Root | /usr/sap | cópia de segurança/Hana |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Verifique se o débito de armazenamento para os diferentes volumes sugeridos será para satisfazer a carga de trabalho que pretende executar. Se a carga de trabalho requer mais altos volumes para **/hana/data** e **/hana/do registo**, terá de aumentar o número de VHDs de armazenamento Premium do Azure. Dimensionar um volume com mais VHDs que listados irá aumentar o débito IOPS e e/s dentro dos limites do tipo de máquina virtual do Azure.

Acelerador de escrita do Azure só funciona em conjunto com [discos geridos do Azure](https://azure.microsoft.com/services/managed-disks/). Então, pelo menos, os discos de armazenamento Premium do Azure que formam o **/hana/do registo** volume tenha de ser implementado como discos geridos.

Existem limites de VHDs de armazenamento Premium do Azure por VM, que pode ser suportado pelo Azure acelerador de escrita. Os limites atuais são:

- 16 VHDs para um M128xx VM
- 8 VHDs para um M64xx VM
- 4 VHDs para um M32xx VM

Instruções mais detalhadas sobre como ativar o acelerador de escrita do Azure podem ser encontradas no artigo [acelerador de escrita](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Detalhes e restrições para o acelerador de escrita do Azure podem ser encontradas na documentação do mesmo.


#### <a name="cost-conscious-azure-storage-configuration"></a>Custo consciente configuração de armazenamento do Azure
A tabela seguinte mostra uma configuração de tipos VM que os clientes normalmente usam para alojar o SAP HANA em VMs do Azure. Pode haver alguns tipos VM que poderão não cumprir todos os critérios mínimos para o SAP HANA ou não serem suportados oficialmente com o SAP HANA, SAP. Mas até agora essas VMs pareciam executar bem para cenários de não produção. 

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo VM é suportado para o SAP HANA pela SAP no [documentação de SAP para o IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| SKU DE VM | RAM | Um máximo de E/S DE VM<br /> Débito | / hana/dados e/hana/do registo<br /> repartidos com LVM ou MDADM | /hana/shared | volume de /Root | /usr/sap | cópia de segurança/Hana |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Os discos recomendados para tipos de VM mais pequeno com 3 x P20 oversize os volumes sobre as recomendações de espaço de acordo com o [White Paper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No entanto, a escolha, tal como apresentado na tabela foi efetuada a fornecer o débito de disco suficiente para o SAP HANA. Se precisar de alterações para o **/hana/cópia de segurança** volume, o que foi dimensionado para manter as cópias de segurança que representam as duas vezes o volume de memória, fique à vontade ajustar.   
Verifique se o débito de armazenamento para os diferentes volumes sugeridos será para satisfazer a carga de trabalho que pretende executar. Se a carga de trabalho requer mais altos volumes para **/hana/data** e **/hana/do registo**, terá de aumentar o número de VHDs de armazenamento Premium do Azure. Dimensionar um volume com mais VHDs que listados irá aumentar o débito IOPS e e/s dentro dos limites do tipo de máquina virtual do Azure. 

> [!NOTE]
> As configurações acima não se beneficiaria partir [máquina virtual do Azure única SLA de VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) , uma vez que o que ele usa uma combinação de armazenamento Premium do Azure e o armazenamento padrão do Azure. No entanto, a seleção foi escolhida para otimizar os custos. Terá de escolher o armazenamento Premium para todos os discos em cima listado como o armazenamento padrão do Azure (Sxx) para fazer a configuração da VM em conformidade com o SLA de VM única do Azure.


> [!NOTE]
> As recomendações de configuração de disco declaradas visam os requisitos mínimos de que SAP expressa para seus provedores de infra-estrutura. Em cenários de carga de trabalho e de implementações de clientes reais, situações foram encontradas onde estas recomendações ainda não forneceu recursos suficientes. Estes podem ser situações em que um cliente necessária uma recarga mais rápida dos dados após um reinício do HANA ou onde configurações maior largura de banda necessária para o armazenamento de cópia de segurança. Noutros casos incluídos **/hana/do registo** onde 5000 IOPS não eram suficientes para a carga de trabalho específica. Então, fazer estas recomendações como um partir do ponto e adaptar-se com base nos requisitos da carga de trabalho.
>  

### <a name="set-up-azure-virtual-networks"></a>Configurar redes virtuais do Azure
Quando tiver conectividade de site a site para o Azure através de VPN ou ExpressRoute, tem de ter pelo menos uma rede virtual do Azure que está ligada através de um Gateway Virtual ao circuito VPN ou ExpressRoute. Em Implantações simples, o Gateway Virtual podem ser implementado numa sub-rede da rede virtual do Azure (VNet) que aloja o SAP HANA nas instâncias também. Para instalar o SAP HANA, crie duas sub-redes adicionais dentro da rede virtual do Azure. Uma sub-rede hospeda as VMs para executar as instâncias do SAP HANA. A outra sub-rede executa Jumpbox ou VMs de gestão para alojar o SAP HANA Studio, o gerenciamento de software ou o seu software de aplicação.

> [!IMPORTANT]
> Fora de funcionalidade, mas mais importante fora de motivos de desempenho, não é suportada para configurar [aplicações virtuais de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de um SAP NetWeaver, Baseados em sistema SAP Hybris ou S/4HANA. A comunicação entre a camada de aplicação SAP e a camada do DBMS tem de ser um direto. A restrição não inclui [regras ASG do Azure e no NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) , desde que essas regras ASG e NSG permitem uma comunicação direta. Ainda mais cenários em que não são suportadas NVAs estão em caminhos de comunicação entre as VMs do Azure que representam nós de cluster do Linux Pacemaker e dispositivos SBD conforme descrito em [elevada disponibilidade para SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Ou na comunicação de caminhos entre as VMs do Azure e de SOFS do Windows Server é definida conforme descrito em [uma instância do SAP ASCS/SCS de Cluster num cluster de ativação pós-falha do Windows ao utilizar uma partilha de ficheiros no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). NVAs em comunicação caminhos podem facilmente duas vezes a latência de rede entre dois parceiros de comunicação, pode restringir a taxa de transferência em caminhos críticas entre a camada de aplicação SAP e a camada do DBMS. Em alguns cenários observados com os clientes, NVAs podem fazer com que os clusters do Linux de Pacemaker falhe em casos em que as comunicações entre os nós de cluster do Linux Pacemaker têm de comunicar com o respetivo dispositivo SBD através de uma NVA.  
> 

> [!IMPORTANT]
> Design outro que seja **não** suportada é a diferenciação de camada de aplicação SAP e a camada do DBMS em diferentes redes virtuais do Azure que não são [em modo de peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre si. Recomenda-se para separar a camada de aplicação SAP e a camada DBMS com sub-redes dentro de uma rede virtual do Azure em vez de usar diferentes redes virtuais do Azure. Se decidir não seguir a recomendação e, em vez disso, separar as duas camadas numa rede virtual diferente, as duas redes virtuais tem de ser [em modo de peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Tenha em atenção que o tráfego entre as duas de rede [em modo de peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) redes virtuais do Azure são o assunto dos custos de transferência. Com o volume de dados grandes em muitos Terabytes trocados entre a camada de aplicação SAP e a camada do DBMS custos substanciais podem ser acumulados se a camada de aplicação SAP e o DBMS camada é segregada entre duas redes virtuais do Azure em modo de peering. 

Ao instalar as VMs a executar o SAP HANA, tem das VMs:

- Duas NICs virtuais instalados: uma NIC para estabelecer ligação à sub-rede de gestão e uma NIC para ligar a partir da rede no local ou a outras redes, para a instância do SAP HANA na VM do Azure.
- Endereços IP privados estáticos que são implementados por ambas as NICs virtuais.

> [!NOTE]
> Deve atribuir endereços IP estáticos através do Azure meios para vNICs individuais. Não deve atribuir endereços IP estáticos no SO convidado para um vNIC. Alguns serviços do Azure como o serviço de cópia de segurança do Azure confiam no fato de que, no mínimo a vNIC principal está definida para DHCP e não para endereços IP estáticos. Consulte também o documento [cópia de segurança de máquina virtual de resolução de problemas do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Se tiver de atribuir vários endereços IP estáticos a uma VM, terá de atribuir múltiplos vNICs a uma VM.
>
>

No entanto, para implementações que são duradouros, terá de criar uma arquitetura de rede do virtual datacenter no Azure. Esta arquitetura recomenda a separação de Gateway de VNet do Azure que liga ao local para uma VNet do Azure separada. Esta VNet separada deve hospedar todo o tráfego que sai para no local ou na internet. Esta abordagem permite-lhe implementar software para auditoria e registo de tráfego que entra o datacenter virtual no Azure deste hub separado de VNet. Portanto, tem uma VNet que aloja o software e configurações que diz respeito ao tráfego-in - e de saída para a implementação do Azure.

Os artigos [Datacenter Virtual do Azure: Uma perspectiva de rede](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) e [Datacenter Virtual do Azure e o plano de controlo de Enterprise](https://docs.microsoft.com/azure/architecture/vdc/) dar mais informações sobre a abordagem do virtual datacenter e o design de VNet do Azure relacionado.


>[!NOTE]
>O tráfego que flui entre uma VNet do hub e, em seguida, através de VNet spoke [peering de VNet do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) é o assunto do adicionais [custos](https://azure.microsoft.com/pricing/details/virtual-network/). Com base nesses custos, poderá ter de considere fazer compromissos entre a execução de um design de rede hub- and -spoke rígido e várias [Gateways do ExpressRoute do Azure](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) que estabeleça a ligação ao 'spokes' para ignorar o VNet peering. No entanto, os Gateways do ExpressRoute do Azure apresentam adicionais [custos](https://azure.microsoft.com/pricing/details/vpn-gateway/) também. Também pode encontrar os custos adicionais para utilizar para tráfego de rede, o registo, auditoria e monitorização de software de terceiros. Depende dos custos para a troca de dados através de VNet peering no lado e os custos criados pelos Gateways do ExpressRoute do Azure adicionais e licenças de software adicional, pode decidir para microssegmentação na VNet com sub-redes como unidade de isolamento em vez de VNets.


Para uma descrição geral dos métodos diferentes para atribuição de endereços IP, consulte [tipos e métodos de alocação no Azure de endereços IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Para VMs que executam o SAP HANA, deve trabalhar com endereços IP estáticos atribuídos. Motivo é que alguns atributos de configuração para o HANA referenciam endereços IP.

[Grupos de segurança de rede (NSGs) do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) são utilizadas para direcionar o tráfego é encaminhado para a instância do SAP HANA ou a jumpbox. Os NSGs e eventualmente [grupos de segurança de aplicativo](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) são associados a sub-rede do SAP HANA e a sub-rede de gestão.

A imagem seguinte mostra uma descrição geral de um esquema de implantação aproximada para o SAP HANA, uma arquitetura de VNet do hub- and -spoke a seguir:

![Esquema de implantação aproximada para o SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

Para implementar o SAP HANA no Azure sem uma ligação site a site, pretender continuar a proteger a instância do SAP HANA a partir da internet pública e ocultá-lo por trás de um proxy de encaminhamento. Neste cenário da básica, a implementação baseia-se em serviços DNS internos do Azure para resolver os nomes de anfitrião. Numa implantação mais complexa em que são utilizados os endereços IP destinado ao público, serviços incorporados do DNS do Azure são especialmente importantes. Utilizar o Azure NSGs e [Azure NVAs](https://azure.microsoft.com/solutions/network-appliances/) para controlar, monitorizar o encaminhamento da internet em sua arquitetura de VNet do Azure no Azure. A imagem seguinte mostra um esquema aproximado para implementar o SAP HANA sem uma ligação site a site num hub e spoke arquitetura de VNet:
  
![Esquema de implantação aproximada para o SAP HANA sem uma ligação site a site](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Outra descrição sobre como utilizar o Azure NVAs para controlar e monitorizar o acesso da Internet sem o hub-and-spoke VNet arquitetura pode ser encontrada no artigo [implementar aplicações virtuais de rede de elevada disponibilidade](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Configurar a infraestrutura do Azure para SAP HANA aumentar horizontalmente
A Microsoft tem um SKU de VM da série M está certificado para uma configuração de escalamento horizontal do SAP HANA. O tipo VM que m128s tem certificação para um Escalamento de até 16 nós. Para alterações no certificações de escalamento horizontal do SAP HANA em VMs do Azure, consulte [lista de plataformas de IaaS Certified](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

As versões de SO mínimo para implantação de configurações de escalamento horizontal em VMs do Azure são:

- SUSE Linux 12 SP3
- Red hat Linux 7.4

Da certificação de escalamento horizontal de 16 nós

- Um nó é o nó principal
- Um máximo de 15 nós são nós de trabalho

>[!NOTE]
>Em implementações de escalamento horizontal de VM do Azure significa que não existe nenhuma possibilidade de utilizar um nó em modo de espera
>

O motivo para não ser capaz de configurar um nó em modo de espera são dois:

- Neste momento, o Azure não tem nenhum serviço NFS nativo. Como resultado partilhas NFS tem de ser configurado com a ajuda da funcionalidade de terceiros.
- Nenhuma das configurações de NFS de terceiros são capazes de satisfazer os critérios de latência de armazenamento para o SAP HANA com suas soluções implementadas no Azure.

Como resultado, **/hana/data** e **/hana/do registo** volumes não podem ser partilhados. Não partilhar estes volumes de nós únicos, evita a utilização de um nó em modo de espera do SAP HANA numa configuração de escalamento horizontal.

Como resultado o projeto básico para um único nó numa configuração de escalamento horizontal será o seguinte aspeto:

![Noções básicas de escalamento horizontal de um único nó](media/hana-vm-operations/scale-out-basics.PNG)

A configuração básica de um nó VM para horizontal do SAP HANA é semelhante a:

- Para **/hana/partilhado**, está a desenvolver um cluster NFS de elevada disponibilidade com base no SUSE Linux 12 SP3. Este cluster irá alojar o **/hana/partilhado** partilhas NFS da sua configuração de escalamento horizontal e SAP NetWeaver ou BW/4HANA Central Services. Documentação para criar esta configuração está disponível no artigo [disponibilidade elevada para NFS nas VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Todos os outros volumes de disco são **não** compartilhados entre os nós de diferentes e estão **não** com base no NFS. Configurações de instalação e os passos para instalações de HANA de escalamento horizontal com não partilhado **/hana/data** e **/hana/do registo** é fornecido adiante, neste documento.

>[!NOTE]
>O cluster de elevada disponibilidade NFS tal como apresentado até ao momento em que os elementos gráficos é suportado com SUSE Linux apenas. Uma solução NFS altamente disponível baseada em Red Hat irá ser aconselhada mais tarde.

Os volumes para os nós de tamanho é igual de aumentar verticalmente, exceto **/hana/partilhado**. Para o SKU de VM M128s, os tamanhos de sugeridos e os tipos de ter o seguinte aspeto:

| SKU DE VM | RAM | Um máximo de E/S DE VM<br /> Débito | /hana/data | / hana/do registo | volume de /Root | /usr/sap | cópia de segurança/Hana |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Verifique se o débito de armazenamento para os diferentes volumes sugeridos será para satisfazer a carga de trabalho que pretende executar. Se a carga de trabalho requer mais altos volumes para **/hana/data** e **/hana/do registo**, terá de aumentar o número de VHDs de armazenamento Premium do Azure. Dimensionar um volume com mais VHDs que listados irá aumentar o débito IOPS e e/s dentro dos limites do tipo de máquina virtual do Azure. Também se aplicam a acelerador de escrita do Azure para os discos que compõem a **/hana/do registo** volume.
 
No documento [requisitos de armazenamento TDI do SAP HANA](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), uma fórmula com o nome que define o tamanho do **/hana/partilhado** volume de escalamento horizontal como o tamanho da memória de um nó único do worker por quatro nós de trabalho.

Partindo do princípio de que coloque a VM de Azure de M128s certificadas do SAP HANA Escalamento horizontal com cerca de 2 TB de memória, as recomendações de SAP podem ser resumidas como:

- Um nó principal e até o nó de trabalho de quatro, a **/hana/partilhado** volume precisariam ser 2 TB de tamanho. 
- Um nó principal e nós de trabalho cinco e oito, o tamanho dos **/hana/partilhado** deve ser de 4 TB. 
- Um nó principal e nós de trabalho de 9 a 12, um tamanho de 6 TB para **/hana/partilhado** seria necessário. 
- Um nó principal e a utilizar entre 12 e nós de trabalho de 15, é necessário para fornecer uma **/hana/partilhado** volume que é de 8 TB de tamanho.

O design importante que é apresentado em gráficos da configuração de nó único para uma VM do Escalamento SAP HANA é a VNet ou melhor a configuração de sub-rede. SAP recomenda uma separação do tráfego destinado ao cliente/aplicação das comunicações entre os nós do HANA. Como mostra os elementos gráficos, essa meta é conseguida fazendo com que dois vNICs diferentes ligados à VM. Ambas as vNICs estão em sub-redes diferentes, dispõe de dois endereços IP diferentes. Em seguida, pode controlar o fluxo de tráfego com regras de encaminhamento utilizando NSGs ou rotas definidas pelo utilizador.

Particularmente no Azure, não existem meios e métodos para impor a qualidade de serviço e quotas em vNICs específico. Como resultado, a separação de comunicação de cliente/aplicação com acesso à e intra-nós não abre qualquer oportunidades para definir prioridades de um fluxo de tráfego em detrimento do outro. Em vez disso, a separação permanece uma medida de segurança na proteção das comunicações intra-nó as configurações de escalamento horizontal.  

>[!IMPORTANT]
>SAP recomenda enfaticamente a separação do tráfego de rede para o lado do cliente/aplicação e tráfego de intra-nós, conforme descrito neste documento. É altamente recomendável, por conseguinte, colocar uma arquitetura em vigor conforme mostrado nos gráficos de última.
>

De um sistema de rede do ponto de vista da arquitetura de rede mínimas necessárias teria o seguinte aspeto:

![Noções básicas de escalamento horizontal de um único nó](media/hana-vm-operations/scale-out-networking-overview.PNG)

Os limites suportados até agora são 15 trabalho adicional para o nó principal.

Um ponto de vista do armazenamento da arquitetura de armazenamento teria o seguinte aspeto:


![Noções básicas de escalamento horizontal de um único nó](media/hana-vm-operations/scale-out-storage-overview.PNG)

O **/hana/partilhado** volume está localizado na configuração da partilha de elevada disponibilidade do NFS. Ao passo que todas as outras unidades "localmente" está montados às VMs individuais. 

### <a name="highly-available-nfs-share"></a>Partilhar de NFS de elevada disponibilidade
O cluster de elevada disponibilidade NFS até agora está trabalhando com SUSE Linux apenas. O documento [elevada disponibilidade para NFS nas VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) descreve como configurá-la. Se não partilhar o cluster NFS com quaisquer outras configurações de HANA fora da VNet do azure que executa o SAP HANA nas instâncias, instale-o na mesma VNet. Instale-o em sua própria sub-rede e certifique-se de que não todo o tráfego arbitrário pode aceder a sub-rede. Em vez disso, pretende limitar o tráfego para essa sub-rede para os endereços IP da VM que execute o tráfego para **/hana/partilhado** volume.

Relacionados com a vNIC de uma VM de escalamento horizontal do HANA que deve encaminhar os **/hana/partilhado** tráfego, as recomendações são:

- Desde o tráfego para **/hana/partilhado** é moderado, roteá-la por meio da vNIC atribuído para a rede de cliente na configuração do mínimo
- Por fim, para o tráfego para **/hana/partilhado**, implementar uma terceira sub-rede na VNet em modo de implementar a configuração de escalamento horizontal do SAP HANA e a vNIC de atribuir um terceiro que está alojado nessa sub-rede. Utilize o vNIC terceiro e o endereço IP associado para o tráfego para a partilha NFS. Em seguida, pode aplicar acesso separado e regras de encaminhamento.

>[!IMPORTANT]
>Sob nenhuma circunstância, tráfego de rede entre as VMs que tenham o SAP HANA de uma forma de escalamento horizontal implementada e de elevada disponibilidade NFS pode ser encaminhado através de um [NVA](https://azure.microsoft.com/solutions/network-appliances/) ou aplicações virtuais semelhantes. Enquanto os NSGs do Azure são sem esses dispositivos. Verifique as suas regras de encaminhamento para certificar-se de que NVAs ou aplicações virtuais semelhantes são detoured quando aceder à partilha NFS altamente disponível das VMs em execução do SAP HANA.
> 

Se quiser partilhar o cluster NFS altamente disponível entre as configurações de SAP HANA, mova todas essas configurações do HANA para a mesma VNet. 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>Instalar o Azure de n de escalamento horizontal do SAP HANA
Instalar uma configuração de SAP de escalamento horizontal, terá de efetuar passos aproximados de:

- Implementa novas ou adaptar a nova infra-estrutura de VNet do Azure
- Implementar novas VMs através de volumes de armazenamento do Azure geridos Premium
- Implementar um novo ou adaptar um cluster de elevada disponibilidade existente NFS
- Adaptar-se de que o encaminhamento de rede para se certificar de que, por exemplo, comunicação intra-nós entre VMs não é encaminhada através de um [NVA](https://azure.microsoft.com/solutions/network-appliances/). Mesmo se aplica para o tráfego entre as VMs e o cluster NFS elevada disponibilidade.
- Instale o nó principal do SAP HANA.
- Adaptar os parâmetros de configuração do nó principal do SAP HANA
- Continuar com a instalação de nós de trabalho de SAP HANA

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Instalação do SAP HANA na configuração de escalamento horizontal
À medida que sua infraestrutura de VM do Azure é implementada e todas as outras preparações terminadas, terá de instalar as configurações de escalamento horizontal do SAP HANA nestes passos:

- Instalar o nó principal do SAP HANA, de acordo com documentação do SAP
- **Após a instalação, terá de alterar o arquivo global.ini e adicione o parâmetro ' basepath_shared = não ' para o global.ini**. Este parâmetro permitirá que o SAP HANA executar na horizontal "partilhado" sem **/hana/data** e **/hana/do registo** volumes entre os nós. Detalhes estão documentados em [SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991).
- Depois de alterar o parâmetro global.ini, reiniciar a instância do SAP HANA
- Adicione nós de trabalho adicionais. Consulte também <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Especifique a rede interna para comunicação entre nós de SAP HANA durante a instalação ou posteriormente usando, por exemplo, o hdblcm local. Para obter mais documentação, consulte também [SAP Note #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Ao seguir esta rotina de instalação, a configuração de escalamento horizontal instalou vai utilizar discos não partilhado para em execução **/hana/data** e **/hana/do registo**. Ao passo que o **/hana/partilhado** volume será colocado na elevada disponibilidade de partilha NFS.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA dinâmica disposição em camadas 2.0 para máquinas virtuais do Azure

Além das certificações de SAP HANA nas VMs de série M do Azure, o SAP HANA dinâmica disposição em camadas 2.0 também é suportado no Microsoft Azure (consulte a documentação do SAP HANA dinâmica disposição em camadas liga mais baixo). Embora não haja nenhuma diferença em instalar o produto ou operacional-lo, por exemplo, através do SAP HANA Cockpit dentro de uma Máquina Virtual, existem alguns itens importantes, o que são obrigatórios para oficial de suporte no Azure. Esses pontos-chave são descritos abaixo. Em todo o artigo, a abreviatura de "DT 2.0" será utilizada em vez do nome completo dinâmica 2.0 disposição em camadas.

SAP HANA dinâmica 2.0 de disposição em camadas não é suportada pelo SAP BW ou S4HANA. Casos de utilização principal agora são aplicativos nativos do HANA.


### <a name="overview"></a>Descrição geral

A figura a seguir fornece uma descrição geral sobre o suporte de DT 2.0 no Microsoft Azure. Existe um conjunto de requisitos obrigatórios, que deve ser seguido para estar em conformidade com a certificação oficial:

- DT 2.0 tem de ser instalado numa VM do Azure dedicado. Poderá não ser executada na mesma VM em que executa o SAP HANA
- SAP HANA e DT 2.0 VMs tem de ser implementadas na mesma Vnet do Azure
- O SAP HANA e DT 2.0 VMs tem de ser implementadas com redes aceleradas do Azure ativado
- Tipo de armazenamento para as VMs de 2.0 DT tem de ser armazenamento Premium do Azure
- Vários discos do Azure devem ser anexados à DT 2.0 VM
- É obrigatório para criar um raid de software / volumes repartidos (quer através de lvm ou mdadm) usando a repartição em todos os discos do Azure

Serão explicados mais detalhes nas seções a seguir.

![Descrição geral do SAP HANA DT arquitetura 2.0](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>VM do Azure dedicada para SAP HANA DT 2.0

No Azure IaaS, DT 2.0 só é suportado numa VM dedicada. Não é permitido executar DT 2.0 na mesma VM do Azure onde está a executar a instância do HANA. Inicialmente dois tipos VM podem ser utilizados para executar o SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Consulte a descrição do tipo de VM [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Tendo em conta a idéia básica do DT 2.0, que é acerca da descarga da "quente" dados para reduzir os custos, faz sentido utilizar tamanhos VM correspondentes. Não há nenhuma regra rígida, embora sobre as combinações possíveis. Depende da carga de trabalho do cliente específico.

Configurações recomendadas seria:

| Tipo de VM do SAP HANA | Tipo de VM de 2.0 DT |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Todas as combinações de VMs de série M com certificação SAP HANA com VMs de 2.0, suportadas DT (M64 32ms e E32sv3) são possíveis.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Redes do Azure e o SAP HANA DT 2.0

Instalar DT 2.0 numa VM dedicada requer o débito de rede entre a VM de 2.0 DT e SAP HANA VM de 10 Gb no mínimo. Por isso é obrigatório para colocar todas as VMs na mesma Vnet do Azure e ativar as redes aceleradas do Azure.

Ver informações adicionais sobre as redes aceleradas do Azure [aqui](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Armazenamento VM para SAP HANA DT 2.0

De acordo com diretrizes de práticas recomendadas DT 2.0, o débito de e/s do disco deve ser a 50 MB/seg mínima por núcleo físico. Olhando para a especificação para os dois tipos de VM do Azure, que são suportadas para 2.0 DT um Verão o limite de taxa de transferência de e/s para a VM de disco máximo:

- E32sv3    :   768 MB/seg (não colocado em cache) que significa que um rácio de 48 MB/s por núcleo físico
- M64-32ms  :  1000 MB/seg (não colocado em cache) que significa que um rácio de 62.5 MB/s por núcleo físico

É necessário para anexar vários discos do Azure para a VM de 2.0 DT e criar um raid de software (repartição) no nível do SO para atingir o limite máximo de débito de disco por VM. Um único disco do Azure não é possível fornecer o débito para atingir o limite máximo de VM nesse aspecto. Armazenamento Premium do Azure é obrigatório para executar DT 2.0. 

- Pode encontrar detalhes sobre os tipos de disco do Azure disponível [aqui](../../windows/disks-types.md)
- Pode encontrar detalhes sobre a criação de raid de software por meio de mdadm [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Detalhes sobre como configurar LVM para criar um volume repartido para débito máximo que pode ser encontrado [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

Dependendo dos requisitos de tamanho, existem diferentes opções para alcançar o débito máximo de uma VM. Seguem-se as configurações de disco do volume de dados para cada tipo de VM de 2.0 DT atingir o limite de taxa de transferência VM superior. A VM E32sv3 deve ser considerada como um nível de entrada para cargas de trabalho mais pequenas. No caso de ele deve se tornar que não é rápido suficientemente poderá ser necessário redimensionar a VM para M64 32ms.
Que a VM de M64 32ms tem tanta memória, a carga de e/s não atinja o limite, especialmente para cargas de trabalho de leitura intensivas. Por conseguinte menos discos no conjunto de stripe poderão ser suficientes consoante a carga de trabalho específica do cliente. Mas para ser arriscar o disco configurações abaixo foram escolhidas para garantir o débito máximo:


| SKU DE VM | Configuração de disco 1 | Configuração de disco 2 | Configuração de disco 3 | Configuração de disco 4 | Configuração de disco 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3.5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2.5 TB | 6 x P15 -> 1.5 TB | 


Especialmente no caso da carga de trabalho com uso intenso de leitura pode aumentar o desempenho de e/s para ativar a cache do anfitrião do Azure "só de leitura" tal como recomendado para os volumes de dados do software de base de dados. Enquanto que para a transação registo cache de disco do anfitrião do Azure tem de ser "none". 

Sobre o tamanho do volume de registo de um ponto de partida recomendado é uma heurística de 15% do tamanho dos dados. A criação do volume de registo pode ser feita ao utilizar tipos de disco do Azure diferente dependendo dos requisitos de custo e o débito. Para o log de débito de e/s de elevado volume, é necessário.  Em caso de a utilizar a VM escreva M64-32ms recomenda vivamente que ative [acelerador de escrita](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Acelerador de escrita do Azure permite a latência de escrita de disco ideal para o log de transação (apenas disponível para a série M). Existem alguns itens a considerar, embora, como o número máximo de discos por tipo de VM. Pode encontrar detalhes sobre o acelerador de escrita [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


Aqui estão alguns exemplos, sobre o volume de registo de dimensionamento:

| tipo de disco e o tamanho do volume de dados | iniciar a configuração de tipo de disco e volume 1 | iniciar a configuração de tipo de disco e volume 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2.5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Como para SAP HANA Escalamento horizontal, o diretório de /hana/shared tem de ser compartilhados entre a VM do SAP HANA e a VM de 2.0 DT. A mesma arquitetura quanto ao uso de escalamento horizontal do SAP HANA dedicada VMs, que atuam como um servidor NFS elevada disponibilidade é recomendável. Para fornecer um volume partilhado de cópia de segurança, pode ser utilizado o design idêntico. Mas é responsabilidade do cliente se HA seria necessária ou se é suficiente usar simplesmente uma VM dedicada com capacidade de armazenamento suficiente para atuar como um servidor de cópia de segurança.



### <a name="links-to-dt-20-documentation"></a>Ligações para documentação DT 2.0 

- [Guia de instalação e atualização camadas dinâmico do SAP HANA](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Camadas dinâmico do SAP HANA tutoriais e recursos](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dinâmica camada prova de conceito](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [Aprimoramentos do SAP HANA 2.0 SPS 02 dinâmicos disposição em camadas](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operações para a implementação de SAP HANA em VMs do Azure
As secções seguintes descrevem algumas das operações relacionadas com a implementação de sistemas do SAP HANA em VMs do Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Criar cópias de segurança e restaurar as operações em VMs do Azure
Os seguintes documentos descrevem como efetuar cópias de segurança e restaurar a implementação de SAP HANA:

- [Descrição geral da cópia de segurança do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Cópia de segurança do SAP HANA ao nível do ficheiro](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Referência de instantâneo de armazenamento do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Iniciar e reiniciar as VMs que contêm o SAP HANA
Um recurso proeminente da cloud pública do Azure é o que é cobrado apenas para os minutos de computação. Por exemplo, quando encerrar uma VM que está a executar o SAP HANA, é cobrado apenas para os custos de armazenamento durante esse período. Outro recurso está disponível quando especificar endereços IP estáticos para as VMs na implementação inicial. Quando reiniciar uma VM com o SAP HANA, a VM é reiniciada com seus endereços IP anteriores. 


### <a name="use-saprouter-for-sap-remote-support"></a>Utilizar SAProuter para remota de suporte do SAP
Se tiver uma ligação site a site entre as suas localizações no local e o Azure e que está a executar os componentes do SAP, em seguida, provavelmente já estiver a executar SAProuter. Neste caso, conclua os seguintes itens para suporte remoto:

- Manter o endereço IP estático e privado da VM que aloja SAP HANA na configuração SAProuter.
- Configure o NSG da sub-rede que aloja a VM do HANA para permitir o tráfego através da porta de TCP/IP 3299.

Se estiver a ligar ao Azure através da internet e não tiver um router SAP para a VM com o SAP HANA, em seguida, terá de instalar o componente. Instale SAProuter numa VM separada na sub-rede de gestão. A imagem seguinte mostra um esquema aproximado para implementar o SAP HANA sem uma ligação site a site e com SAProuter:

![Aproximada esquema de implementação para o SAP HANA sem uma ligação site a site e SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Certifique-se de que instalar SAProuter numa VM separada e não na sua VM da Jumpbox. A VM separada tem de ter um endereço IP estático. Para ligar o seu SAProuter para o SAProuter que é alojado pela SAP, contacte o SAP para um endereço IP. (O que é alojado pela SAP SAProuter é a contraparte da instância SAProuter que instalou na sua VM.) Utilize o endereço IP do SAP para configurar a sua instância de SAProuter. As definições de configuração, a porta apenas necessária é a porta TCP 3299.

Para obter mais informações sobre como configurar e manter as ligações de suporte remoto através de SAProuter, consulte a [documentação de SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Elevada disponibilidade com o SAP HANA em VMs nativas do Azure
Se estiver a executar o SUSE Linux Enterprise Server para o SAP aplicativos 12 SP1 ou posterior, pode estabelecer um cluster de Pacemaker com dispositivos STONITH. Pode utilizar os dispositivos para definir uma configuração de SAP HANA que utiliza a replicação síncrona com o HANA System Replication e ativação pós-falha automática. Para obter mais informações sobre o procedimento de configuração, consulte [guia de SAP HANA de elevada disponibilidade para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
