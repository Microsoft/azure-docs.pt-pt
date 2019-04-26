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
ms.openlocfilehash: acccc553c5b63b2acd0f9793b0397b25145449dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477441"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Configurações e operações de infraestrutura do SAP HANA no Azure
Este artigo fornece orientações sobre como configurar a infraestrutura do Azure e operar sistemas SAP HANA que estão implementados em máquinas de virtuais (VMs) nativas do Azure. Este artigo também contém informações de configuração para o SAP HANA aumentar horizontalmente para o SKU de VM M128s. Este artigo não se destina a substituir a documentação de SAP padrão, o que inclui o seguinte conteúdo:

- [Guia de administração do SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guias de instalação SAP](https://service.sap.com/instguides)
- [Notas SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar este guia, tem um conhecimento básico dos seguintes componentes do Azure:

- [Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Redes do Azure e redes virtuais](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Armazenamento do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Para saber mais sobre o SAP NetWeaver e outros componentes SAP no Azure, veja a [SAP no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) secção a [documentação do Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerações sobre a configuração básica
As secções seguintes descrevem as considerações de configuração básica para implementação de sistemas do SAP HANA em VMs do Azure.

### <a name="connect-to-azure-virtual-machines"></a>Ligar a máquinas virtuais do Azure
Conforme documentado no [máquinas virtuais do Azure, guia de planejamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), dois métodos básicos são utilizados para ligar a VMs do Azure:

- Ligar através da internet e os pontos finais públicos numa VM da JumpBox ou na VM que está a executar o SAP HANA.
- Ligar através de um [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou do Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Conectividade de site a site via VPN ou ExpressRoute é necessária para cenários de produção. Este tipo de ligação também é necessário para cenários de não produção esse feed em cenários de produção em que é usado SAP software. A imagem seguinte mostra um exemplo de conectividade entre sites:

![Conectividade entre sites](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Escolha os tipos de VM do Azure
Os tipos de VM do Azure a utilizar para cenários de produção estão listados na [documentação de SAP para o IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, uma maior variedade de tipos nativos de VM do Azure está disponível.

>[!NOTE]
> Para cenários de não produção, utilize os tipos VM que estão listados na [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533). A utilização de VMs do Azure para cenários de produção, verifique as VMs no SAP publicados com certificação SAP HANA [certified lista de plataformas IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Para implementar as VMs no Azure, utilize:

- O portal do Azure.
- Cmdlets do PowerShell do Azure.
- A CLI do Azure.

Também pode implementar uma plataforma completa de SAP HANA instalada nos serviços de VM do Azure através da [plataforma cloud do SAP](https://cal.sap.com/). Para obter informações sobre o processo de instalação, consulte [SAP implementar S/4HANA ou BW/4HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Para obter informações sobre a automação lançada, consulte [este artigo do GitHub](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-an-azure-storage-type"></a>Escolher um tipo de armazenamento do Azure
O Azure fornece dois tipos de armazenamento que são adequados para as VMs do Azure que executam o SAP HANA:  

- Padrão unidades de disco rígido (HDDs)
- Unidades de estado sólido Premium (SSD)

Para saber mais sobre estes tipos de discos, consulte [selecionar um tipo de disco](../../windows/disks-types.md).

O Azure oferece dois métodos de implantação para VHDs no armazenamento standard do Azure e o armazenamento premium. Se o cenário geral permite, aproveitar [disco gerido do Azure](https://azure.microsoft.com/services/managed-disks/) implementações.

Para obter uma lista de tipos de armazenamento e os respetivos SLAs no débito IOPS e o armazenamento, consulte [documentação do Azure para discos geridos](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configure-the-storage-for-azure-virtual-machines"></a>Configurar o armazenamento para máquinas virtuais do Azure

Provavelmente nunca se preocupa com subsistemas de e/s e as respetivas funcionalidades porque o fornecedor da aplicação certificou-se de que foram cumpridos os requisitos de armazenamento mínima para o SAP HANA. Quando cria a infraestrutura do Azure por conta própria, precisa estar ciente de alguns dos requisitos. Também deve compreender os requisitos de configuração sugeridos nas secções seguintes. Para casos em que configura as máquinas virtuais que pretende que o SAP HANA serem executadas, precisará de:

- Ative o volume de leitura/escrita no /hana/log pelo menos de 250 MB por segundo para tamanhos de e/s de 1 MB.
- Ative a leitura de atividade de, pelo menos, de 400 MB/seg para /hana/data para os tamanhos de e/s de 16 MB e de 64 MB.
- Ative a atividade de escrita de, pelo menos, 250 MB/seg para /hana/data com tamanhos de e/s de 16 MB e de 64 MB.

Armazenamento de baixa latência é fundamental para sistemas DBMS, mesmo que o DBMS, como o SAP HANA, mantém os dados na memória. O caminho crítico no armazenamento é normalmente em torno as gravações de log de transação dos sistemas DBMS. Mas gostam de operações de escrita de pontos de reposição ou carregamento de dados na memória depois de recuperação contra panes também pode ser considerado crítica. 

Utilização de discos premium do Azure para volumes /hana/data e /hana/log é obrigatória. Para alcançar o débito mínimo de /hana/log e /hana/data como pretendido, SAP, crie um RAID 0 utilizando mdadm ou um Gestor de Volume lógicos (LVM) através de vários discos de armazenamento premium do Azure. Também pode utilize os volumes de RAID como /hana/data e /hana/log volumes. Recomendamos que utilize os seguintes tamanhos do stripe para o RAID 0:

- 64 KB ou 128 KB de dados/hana /
- 32 KB para/hana/do registo

> [!NOTE]
> Não precisa de configurar qualquer nível de redundância ao utilizar volumes RAID, porque o armazenamento do Azure premium e standard mantêm três imagens de um VHD. A utilização de um volume RAID é puramente configurar volumes que fornecem o débito de e/s suficiente.

Acumular um número de VHDs do Azure por baixo de uma RAID é accumulative a partir de um lado de débito do armazenamento e de IOPS. Se colocar um RAID 0 sobre os discos de armazenamento premium do Azure x P30 3, ele fornece três vezes o IOPS e três vezes o débito de armazenamento de um disco de armazenamento P30 único premium do Azure.

As seguintes recomendações de colocação em cache assumem as características de e/s para o SAP HANA:

- Não há quase qualquer carga de trabalho de leitura nos arquivos de dados HANA. As exceções são a grande porte e/s após o reinício da instância do HANA ou quando dados são carregados em HANA. Outro caso de maior de leitura que e/s nos arquivos de dados pode ser backups de banco de dados do HANA. Como resultado, cache de leitura não faz sentido porque, na maioria dos casos, todos os volumes de dados de ficheiros tem de ser de leitura completamente.
- Escrever contra os ficheiros de dados se verificarem em picos com base em pontos de reposição do HANA e recuperação de falhas do HANA. Criação de pontos de reposição é assíncrona e não se mantém quaisquer transações de utilizador. Escrita de dados durante a recuperação de falhas é crítico de desempenho para o sistema a responder rapidamente novamente. Recuperação de falhas deve ser situações excecionais em vez disso.
- Há um mínimo de leituras dos ficheiros de Refazer do HANA. As exceções são grandes e/ss quando executar backups de log de transação, recuperação contra panes ou a fase de reinício de uma instância do HANA.  
- O principal de carga com o arquivo de log de retrabalho SAP HANA é escritas. Dependendo da natureza da carga de trabalho, pode ter e/ss tão pequena como 4 KB ou, em outros casos, a e/s o tamanho de 1 MB ou mais. Escreva a latência contra o log de retrabalho de SAP HANA é o desempenho crítico.
- Todas as gravações devem persistência no disco de forma fiável.

Como resultado desses padrões de e/s observados pelo SAP HANA, defina a colocação em cache para os volumes diferentes que utilizam o armazenamento premium do Azure para:

- **/hana/data**: Sem colocação em cache.
- **/hana/log**: Sem colocação em cache, com uma exceção para VMs de série M (ver mais licenças posteriormente neste artigo).
- **/hana/shared**: Leia a colocação em cache.


Além disso, tenha em atenção o débito de e/s VM global quando o tamanho ou decidir qual uma VM. Em geral o débito do armazenamento VM está documentado no [tamanhos de máquinas virtuais com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="linux-io-scheduler-mode"></a>Modo de agendador de e/s do Linux
Linux tem vários modos de agendamento de e/s diferentes. Uma recomendação comum de fornecedores de Linux e a SAP é definir o modo de agendador de e/s para volumes de disco distância, a partir do **cfq** modo para o **noop** modo. Para obter mais informações, consulte [SAP Note #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


#### <a name="storage-solution-with-write-accelerator-for-azure-m-series-virtual-machines"></a>Solução de armazenamento com acelerador de escrita para o Azure máquinas de virtuais de série M
 Escreva que Accelerator é uma funcionalidade do Azure que está a implementar para VMs do Azure de série M exclusivamente. O objetivo da funcionalidade é melhorar a latência de e/s de escrita no armazenamento premium do Azure. Para o SAP HANA, o acelerador de escrita é suposto a ser utilizado com o volume de /hana/log apenas. Por esse motivo, as configurações mostradas até agora tem de ser alteradas. A principal mudança é a divisão entre /hana/data e /hana/log para poder utilizar o acelerador de escrita com o volume de /hana/log apenas. 

> [!IMPORTANT]
> Certificação do SAP HANA para o Azure máquinas virtuais é exclusiva para o acelerador de escrita para o volume de registo/hana/de série M. Como resultado, as implementações do SAP HANA de cenário de produção no Azure volume de registo de série M máquinas virtuais devem ser configurados com acelerador de escrita para o hana /.

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo VM é suportado para o SAP HANA pela SAP no [documentação de SAP para o IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

A tabela seguinte mostra as configurações recomendadas.

| SKU DE VM | RAM | E/s de máximo de VMS<br /> Taxa de transferência | /hana/data | / hana/do registo | /hana/shared | volume de /Root | /usr/sap | cópia de segurança/Hana |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/seg | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/seg | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/seg | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 giB | 1000 MB/seg | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1,750 giB | 1000 MB/seg | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2.000 giB | 2.000 MB/seg |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3 800 giB | 2.000 MB/seg | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Verifique se o débito de armazenamento para os diferentes volumes sugeridos atende a carga de trabalho que pretende executar. Se a carga de trabalho requer volumes superiores para /hana/data e /hana/log, aumente o número de armazenamento premium do Azure VHDs. Dimensionar um volume com mais VHDs que listadas aumenta o IOPS e débito de e/s dentro dos limites do tipo de máquina virtual do Azure.

Escrever Accelerator só funciona em conjunto com [discos geridos do Azure](https://azure.microsoft.com/services/managed-disks/). Pelo menos, os discos de armazenamento premium do Azure que formam o volume de /hana/log tem de ser implementados como discos geridos.

Existem limites para o armazenamento premium do Azure VHDs por VM que suporte o acelerador de escrita. Os limites atuais são:

- 16 VHDs para um M128xx VM.
- 8 VHDs para um M64xx VM.
- 4 VHDs para um M32xx VM.

Para obter mais informações sobre como ativar o acelerador de escrita, consulte [acelerador de escrita](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Para obter mais informações sobre e restrições para o acelerador de escrita, consulte a documentação do mesmo.


#### <a name="cost-conscious-azure-storage-configuration"></a>Configuração de armazenamento do Azure econômico
A tabela seguinte mostra uma configuração de tipos VM que os clientes normalmente usam para alojar o SAP HANA em VMs do Azure. Pode haver alguns tipos VM que não correspondam a todos os critérios mínimos para o SAP HANA. Também pode haver alguns tipos VM que não são suportados oficialmente com o SAP HANA, SAP. Até agora, essas VMs tem realizado bem para cenários de não produção. 

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo VM é suportado para o SAP HANA pela SAP no [documentação de SAP para o IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| SKU DE VM | RAM | E/s de máximo de VMS<br /> Taxa de transferência | / hana/dados e/hana/do registo<br /> repartidos com LVM ou mdadm | /hana/shared | volume de /Root | /usr/sap | cópia de segurança/Hana |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/seg | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/seg | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/seg | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432 GiB | 1200 MB/seg | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2.000 MB/seg | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/seg | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/seg | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1000 MB/seg | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1000 giB | 1000 MB/seg | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1,750 giB | 1000 MB/seg | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2.000 giB | 2.000 MB/seg |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3 800 giB | 2.000 MB/seg | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Os discos que são recomendados para tipos de VM mais pequeno com 3 x P20 oversize os volumes em relação às recomendações de espaço que recebem o [TDI do SAP armazenamento white paper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). A opção apresentada na tabela foi efetuada a fornecer o débito de disco suficiente para o SAP HANA. Se precisar de alterar o **/hana/cópia de segurança** volume, o que foi dimensionado para manter as cópias de segurança que representam as duas vezes o volume de memória, fique à vontade fazer ajustes. 

Verifique se o débito de armazenamento para os diferentes volumes sugeridos atende a carga de trabalho que pretende executar. Se a carga de trabalho requer volumes superiores para /hana/data e /hana/log, aumente o número de armazenamento premium do Azure VHDs. Dimensionar um volume com mais VHDs que listadas aumenta o IOPS e débito de e/s dentro dos limites do tipo de máquina virtual do Azure. 

> [!NOTE]
> As configurações anteriores não beneficiam [máquina virtual do Azure única VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) porque utiliza uma combinação de armazenamento premium do Azure e o armazenamento standard do Azure. A seleção foi escolhida para otimizar os custos. Escolha o armazenamento premium para todos os discos na tabela que está listado como o armazenamento standard do Azure (Sxx) para fazer a configuração da VM em conformidade com o SLA de VM única do Azure.


> [!NOTE]
> As recomendações de configuração do disco de destino com os requisitos mínimos que SAP dá seus provedores de infra-estrutura. Em Implantações reais de clientes e cenários de carga de trabalho, estas recomendações não fornecem recursos suficientes em algumas situações. Por exemplo, um cliente precisou de uma recarga mais rápida dos dados após um reinício do HANA ou largura de banda superior para o armazenamento de necessárias de configurações de cópia de segurança. Noutros casos incluem /hana/log, onde 5000 IOPS não fosse suficientes para a carga de trabalho específica. Utilize estas recomendações como ponto de partida e adaptá-los com base nos requisitos da carga de trabalho.
>  

### <a name="set-up-azure-virtual-networks"></a>Configurar redes virtuais do Azure
Quando tiver conectividade de site a site para o Azure através de VPN ou ExpressRoute do Azure, tem de ter pelo menos uma rede virtual do Azure que está ligada através de um gateway virtual ao circuito VPN ou ExpressRoute. Em Implantações simples, o gateway virtual pode ser implementado numa sub-rede da rede virtual do Azure que aloja o SAP HANA nas instâncias demasiado. 

Para instalar o SAP HANA, crie duas sub-redes adicionais dentro da rede virtual do Azure. Uma sub-rede hospeda as VMs para executar as instâncias do SAP HANA. A outra sub-rede é executado JumpBox ou VMs de gestão para o anfitrião do SAP HANA Studio, gerenciamento de software ou o software de aplicação.

> [!IMPORTANT]
> Configurando [aplicações virtuais de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de NetWeaver um SAP-, Hybris- ou sistema baseado em S/4HANA SAP não é suportada. Esta restrição é por motivos de desempenho e funcionalidade. O caminho de comunicação entre a camada de aplicação SAP e a camada DBMS tem de ser um direto. A restrição não inclui [regras (NSG) de grupo do grupo de segurança de aplicação (ASG) e segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview) se essas regras ASG e NSG permitem um caminho de comunicação direta. 
>
> Outros cenários em que não são suportadas aplicações virtuais de rede estão em: 
>
> - Caminhos de comunicação entre as VMs do Azure que representam Pacemaker de Linux do cluster nós e dispositivos SBD conforme descrito em [elevada disponibilidade para SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> - Caminhos de comunicação entre as VMs do Azure e o servidor de ficheiros de escalamento horizontal do Windows Server definido até conforme descrito em [uma instância do SAP ASCS/SCS de Cluster num cluster de ativação pós-falha do Windows ao utilizar uma partilha de ficheiros no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Aplicações virtuais de rede em caminhos de comunicação podem facilmente double a latência de rede entre parceiros de comunicação de dois. Também pode restringir a taxa de transferência em caminhos críticas entre a camada de aplicação SAP e a camada do DBMS. Em alguns cenários de cliente, dispositivos de rede virtual podem fazer com que os clusters do Linux de Pacemaker falhe. Esses são casos em que as comunicações entre os nós de cluster do Linux Pacemaker comunicam para o respetivo dispositivo SBD através de uma aplicação virtual de rede.  
> 

> [!IMPORTANT]
> Outro que de design da *não* suportada é a diferenciação de camada de aplicação SAP e a camada do DBMS em diferentes redes virtuais do Azure que não são [em modo de peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre si. É recomendável separar a camada de aplicação SAP e a camada do DBMS utilizando sub-redes dentro de uma rede virtual do Azure em vez de através da utilização de diferentes redes virtuais do Azure. 
>
>Se decidir não seguir a recomendação e segregar em vez disso, as duas camadas em redes virtuais diferentes, as duas redes virtuais têm de ser [em modo de peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Tenha em atenção que o tráfego entre as duas de rede [em modo de peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) redes virtuais do Azure está sujeita a custos de transferência. Volume de dados grande que consiste em muitos terabytes é trocado entre a camada de aplicação SAP e a camada do DBMS. Pode acumular os custos substanciais se a camada de aplicação SAP e a camada do DBMS são separados entre duas redes virtuais do Azure em modo de peering. 

Ao instalar as VMs a executar o SAP HANA, tem das VMs:

- Duas NICs virtuais instalados. Uma NIC liga-se para a sub-rede de gestão. Outro NIC liga-se através da rede no local ou outras redes para a instância do SAP HANA na VM do Azure.
- Endereços IP privados estáticos que são implementados por ambas as NICs virtuais.

> [!NOTE]
> Atribuição de endereços IP estáticos através do Azure significa que para atribuí-las a NICs virtuais individuais. Não atribuir endereços IP estáticos no SO convidado a uma NIC virtual. Alguns serviços do Azure como o Azure Backup confiam no fato de que, no mínimo a NIC virtual principal está definida para DHCP e não para endereços IP estáticos. Para obter mais informações, consulte [cópia de segurança de máquina virtual de resolução de problemas do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Para atribuir vários endereços IP estáticos a uma VM, atribua vários NICs virtuais a uma VM.
>
>

Para implementações que são duradouros, crie uma arquitetura de rede do virtual datacenter no Azure. Esta arquitetura recomenda a separação do gateway de rede virtual do Azure que se liga a no local numa rede virtual do Azure separada. Esta rede virtual separado aloja todo o tráfego que sai no local ou na internet. Ao utilizar esta abordagem, pode implementar software para fazer auditoria e Registar tráfego que entra o datacenter virtual no Azure nesta rede virtual do hub separado. Dessa forma, terá uma rede virtual que aloja a todo o software e configurações que diz respeito ao tráfego ingoing e de saída para a implementação do Azure.


Para obter mais informações sobre a abordagem do virtual datacenter e o design de rede virtual do Azure relacionadas, consulte: 

- [Datacenter virtual do Azure: Uma perspectiva de rede](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter).
- [Datacenter virtual do Azure e o plano de controlo de enterprise](https://docs.microsoft.com/azure/architecture/vdc/).


>[!NOTE]
>O tráfego que flui entre uma rede virtual do concentrador e uma rede virtual do spoke usando [peering de rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) estão sujeitas a adicionais [custos](https://azure.microsoft.com/pricing/details/virtual-network/). Com base nesses custos, poderá ter que assumir compromissos entre a execução de um design de rede de hub-and-spoke restritas e várias [gateways do ExpressRoute do Azure](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) que estabeleça a ligação ao spokes para ignorar o peering de rede virtual. 
>
> Gateways do ExpressRoute do Azure apresentam adicionais [custos](https://azure.microsoft.com/pricing/details/vpn-gateway/) demasiado. Também poderá encontrar os custos adicionais para o software de terceiros que utilizar para iniciar sessão, auditar e monitorizar o tráfego de rede. Considere os custos para a troca de dados através de rede virtual peering em comparação com os custos criados pelos gateways do ExpressRoute adicionais e licenças de software. Poderá decidir microssegmentação na rede virtual com sub-redes como unidades de isolamento em vez de redes virtuais.


Para uma descrição geral de métodos diferentes que pode utilizar para atribuir endereços IP, consulte [tipos e métodos de alocação no Azure de endereços IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Para as VMs que executam o SAP HANA, trabalhe com os endereços IP estáticos que estão atribuídos. O motivo é que alguns atributos de configuração para o HANA referenciam endereços IP.

[Azure NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) são utilizadas para direcionar o tráfego é encaminhado para a instância do SAP HANA ou a JumpBox. Os NSGs e eventualmente [grupos de segurança de aplicativo](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) estão associadas a sub-rede do SAP HANA e a sub-rede de gestão.

A imagem seguinte mostra uma descrição geral de um esquema de implantação aproximada para o SAP HANA que se seguem uma arquitetura hub-and-spoke de rede virtual:

![Esquema de implantação aproximada para o SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

Para implementar o SAP HANA no Azure sem uma ligação site a site, proteger a instância do SAP HANA a partir da internet pública e ocultá-lo por trás de um proxy de encaminhamento. Neste cenário da básica, a implementação baseia-se em serviços DNS internos do Azure para resolver os nomes de anfitrião. Numa implantação mais complexa em que são utilizados os endereços IP destinado ao público, serviços incorporados do DNS do Azure são especialmente importantes. Utilizar o Azure NSGs e [aplicações virtuais de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) para monitorizar o encaminhamento da internet em sua arquitetura de rede virtual do Azure no Azure. 

A imagem seguinte mostra um esquema aproximado para saber como implementar o SAP HANA sem uma ligação site a site numa arquitetura hub-and-spoke de rede virtual:
  
![Esquema de implantação aproximada para o SAP HANA sem uma ligação site a site](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Para obter outra descrição de como utilizar aplicações virtuais de rede do Azure para controlar e monitorizar o acesso a partir da internet sem a arquitetura de rede virtual do hub-and-spoke, veja [implementar aplicações virtuais de rede de elevada disponibilidade](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configure-azure-infrastructure-for-sap-hana-scale-out"></a>Configurar a infraestrutura do Azure para SAP HANA aumentar horizontalmente
A Microsoft tem um SKU de VM da série M está certificado para uma configuração de escalamento horizontal do SAP HANA. O tipo de VM M128s está certificado para um Escalamento de até 16 nós. Para alterações em certificações de escalamento horizontal do SAP HANA em VMs do Azure, veja a [certified lista de plataformas IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

As versões de SO mínimas utilizadas para implementar as configurações de escalamento horizontal em VMs do Azure são:

- SUSE Linux 12 SP3.
- Red Hat Linux 7.4.

Para obter uma certificação de escalamento horizontal de 16 nós:

- Um nó é o nó principal.
- Um máximo de 15 nós são nós de trabalho.

>[!NOTE]
>Nas implementações de escalamento horizontal de VM do Azure, não é possível utilizar um nó em modo de espera.
>

Existem dois motivos por que motivo não é possível configurar um nó em modo de espera:

- Neste momento, o Azure não tem nenhum serviço NFS nativo. Como resultado, as partilhas NFS tem de ser configuradas com a ajuda da funcionalidade de terceiros.
- Nenhuma das configurações de NFS de terceiros podem preencher os critérios de latência de armazenamento para o SAP HANA com suas soluções implementadas no Azure.

Como resultado, não não possível partilhar /hana/data e /hana/log volumes. Estes volumes de nós únicos de partilha não impede a utilização de um nó em modo de espera de SAP HANA numa configuração de escalamento horizontal.

A imagem seguinte mostra a estrutura básica para um único nó numa configuração de escalamento horizontal:

![Noções básicas de escalamento horizontal de um único nó](media/hana-vm-operations/scale-out-basics.PNG)

A configuração básica de um nó VM para horizontal do SAP HANA é semelhante a:

- Para /hana/shared, criar um cluster NFS de elevada disponibilidade com base no SUSE Linux 12 SP3. Este cluster hospeda as partilhas NFS /hana/shared da sua configuração de escalamento horizontal e SAP NetWeaver ou BW/4HANA Central Services. Para obter informações sobre como criar esta configuração, consulte [elevada disponibilidade para NFS nas VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
- Todos os outros volumes de disco não são partilhados entre os nós de diferentes e não são baseados em NFS. Configurações de instalação e os passos para instalações de HANA de escalamento horizontal com /hana/data e não-compartilhada e /hana/log são fornecidos neste artigo.

>[!NOTE]
>O cluster de elevada disponibilidade NFS tal como apresentado até ao momento em que os elementos gráficos é suportado com SUSE Linux apenas. Uma solução NFS altamente disponível baseada em Red Hat irá ser aconselhada mais tarde.

Os volumes para os nós de tamanho é igual de aumentar verticalmente, exceto para /hana/shared. A tabela seguinte mostra os tamanhos sugeridos e tipos para o SKU de VM M128s.

| SKU DE VM | RAM | E/s de máximo de VMS<br /> Taxa de transferência | /hana/data | / hana/do registo | volume de /Root | /usr/sap | cópia de segurança/Hana |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2.000 giB | 2.000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Verifique se o débito de armazenamento para os diferentes volumes sugeridos atende a carga de trabalho que pretende executar. Se a carga de trabalho requer volumes superiores para /hana/data e /hana/log, aumente o número de armazenamento premium do Azure VHDs. Dimensionar um volume com mais VHDs que listadas aumenta o IOPS e débito de e/s dentro dos limites do tipo de máquina virtual do Azure. Também se aplicam acelerador de escrita para os discos que formam o volume de /hana/log.
 

Para uma fórmula que define o tamanho do volume/hana/partilhados para Escalamento horizontal como o tamanho da memória de um nó único do worker por quatro nós de trabalho, consulte [requisitos de armazenamento do SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Supondo que coloque a VM de Azure de M128s certificadas do SAP HANA Escalamento horizontal com aproximadamente 2 TB de memória, as recomendações de SAP são resumidas como:

- Para um nó principal e até quatro nós de trabalho, o tamanho do /hana/shared volume é 2 TB.
- Para um nó principal e cinco e oito nós de trabalho, o tamanho do /hana/shared volume é de 4 TB.
- Para um nó principal e nós de trabalho de 9 a 12, o tamanho do /hana/shared volume é 6 TB.
- Para um nó principal e nós de trabalho de 12 a 15, o tamanho do /hana/shared volume é de 8 TB.

O design importante que é apresentado em gráficos da configuração de nó único para uma VM do Escalamento SAP HANA é a rede virtual com a configuração de sub-rede. SAP recomenda uma separação do tráfego com clientes e aplicativos de comunicações entre os nós do HANA. 

Para atingir esse objetivo, anexe dois NICs virtuais diferentes para a VM, conforme mostrado nos elementos gráficos. Ambas as NICs virtuais estejam em sub-redes diferentes e tem dois endereços IP diferentes. Em seguida, controlar o fluxo de tráfego com regras de encaminhamento utilizando NSGs ou rotas definidas pelo utilizador.

Particularmente no Azure, não existem meios e métodos para impor a qualidade de serviço e quotas em NICs virtuais específicos. Como resultado, a separação de comunicação com clientes e do aplicativo e intra-nós não abre qualquer oportunidades para definir prioridades de um fluxo de tráfego em detrimento do outro. Em vez disso, a separação permanece uma medida de segurança na proteção das comunicações intra-nó as configurações de escalamento horizontal.  

>[!IMPORTANT]
>SAP recomenda enfaticamente que separar o tráfego de rede para o cliente e a aplicação do lado do tráfego e de intra-nós conforme descrito neste artigo. Recomendamos que coloque uma arquitetura em vigor, como mostra o gráfico anterior.
>

A imagem seguinte mostra a arquitetura de rede mínimas necessárias de um sistema de rede do ponto de vista:

![Noções básicas de escalamento horizontal de um único nó](media/hana-vm-operations/scale-out-networking-overview.PNG)

Os limites suportados até agora são 15 nós de trabalho, além de um nó principal.

A imagem seguinte mostra a arquitetura de armazenamento do armazenamento ponto de vista:


![Noções básicas de escalamento horizontal de um único nó](media/hana-vm-operations/scale-out-storage-overview.PNG)

O volume de /hana/shared encontra-se na configuração da partilha de elevada disponibilidade do NFS. Todas as outras unidades são montadas localmente às VMs individuais. 

### <a name="highly-available-nfs-share"></a>Partilhar de NFS de elevada disponibilidade
Até agora, o cluster NFS de elevada disponibilidade está a trabalhar com SUSE Linux apenas. Para informações de configuração, consulte [elevada disponibilidade para NFS nas VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Se não partilhar o cluster NFS com quaisquer outras configurações de HANA fora da rede virtual do Azure que executa o SAP HANA nas instâncias, instale-o na mesma rede virtual. Instale-o em sua própria sub-rede e certifique-se de que não todo o tráfego arbitrário pode aceder a sub-rede. Em vez disso, limitam o tráfego para essa sub-rede para os endereços IP da VM que executam o tráfego para o volume de /hana/shared.

As recomendações relacionadas com a NIC virtual de uma VM de escalamento horizontal do HANA que encaminha o tráfego de /hana/shared, são:

- Como o tráfego para /hana/shared é moderado, roteá-la por meio de NIC virtual que está atribuída à rede de cliente na configuração mínima.
- Por fim, para o tráfego para/hana/partilhado, implemente uma terceira sub-rede na rede virtual para implementar a configuração de escalamento horizontal do SAP HANA. Atribua uma terceira NIC virtual que está alojada nessa sub-rede. Utilize o NIC virtual terceiro e o endereço IP associado para o tráfego para a partilha NFS. Em seguida, pode aplicar acesso separado e regras de encaminhamento.

>[!IMPORTANT]
>Tráfego de rede entre as VMs que tenham o SAP HANA de uma forma de escalamento horizontal implementada e de elevada disponibilidade NFS sob nenhuma circunstância, pode ser encaminhado através de um [aplicação de rede virtual](https://azure.microsoft.com/solutions/network-appliances/) ou aplicações virtuais semelhantes. NSGs do Azure não são nenhum desses dispositivos. Verifique suas regras de encaminhamento para se certificar de que as aplicações virtuais de rede ou aparelhos virtuais semelhantes são detoured quando acedem o elevada disponibilidade NFS partilham a partir de VMs que executam o SAP HANA.
> 

Se quiser partilhar o cluster NFS altamente disponível entre as configurações de SAP HANA, mova todas essas configurações do HANA para a mesma rede virtual. 
 

### <a name="install-an-sap-hana-scale-out-in-azure"></a>Instalar um Escalamento de SAP HANA no Azure
Para instalar uma configuração de SAP de escalamento horizontal, siga estes passos gerais:

- Implementar novos ou adaptar-se a nova infra-estrutura de rede virtual do Azure.
- Implemente novas VMs ao utilizar volumes de armazenamento premium geridos pelo Azure.
- Implementar um novo ou adaptar um cluster de elevada disponibilidade existente NFS.
- Adaptar-se de que o encaminhamento de rede para se certificar de que, por exemplo, comunicação intra-nós entre VMs não é roteada para um [aplicação de rede virtual](https://azure.microsoft.com/solutions/network-appliances/). O mesmo é verdadeiro para o tráfego entre as VMs e o cluster NFS elevada disponibilidade.
- Instale o nó principal do SAP HANA.
- Adapte os parâmetros de configuração do nó principal do SAP HANA.
- Continue com a instalação de nós de trabalho de SAP HANA.

#### <a name="install-sap-hana-in-a-scale-out-configuration"></a>Instale o SAP HANA numa configuração de escalamento horizontal
A infraestrutura de VM do Azure é implementada e todas as outras preparações terminar. Agora, para instalar as configurações de escalamento horizontal do SAP HANA, siga estes passos:

- Instale o nó principal do SAP HANA, de acordo com documentação do SAP.
- *Após a instalação, altere o arquivo global.ini e adicione o parâmetro ' basepath_shared = não ' para o global.ini*. Este parâmetro permite que o SAP HANA executar na horizontal sem /hana/data partilhado e volumes de /hana/log entre os nós. Para obter mais informações, consulte [SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991).
- Depois de alterar o parâmetro global.ini, reinicie a instância do SAP HANA.
- Adicione nós de trabalho adicionais. Para obter mais informações, consulte [guia de administração do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html). Especifique a rede interna para comunicação entre nós de SAP HANA durante a instalação ou posteriormente, utilizando, por exemplo, o hdblcm local. Para obter mais informações, consulte [SAP Note #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Ao seguir esta rotina de instalação, a configuração de escalamento horizontal que instalou utiliza discos e não-compartilhados para executar /hana/data e /hana/log. O volume/hana/partilhado é colocado na elevada disponibilidade de partilha NFS.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA 2.0 camadas dinâmica para máquinas virtuais do Azure

Além das certificações de SAP HANA nas VMs de série M do Azure, o SAP HANA 2.0 camadas dinâmico (DT 2.0) também é suportado no Microsoft Azure. Para obter ligações para documentação de camadas dinâmica de SAP HANA, consulte a seção "Links para DT documentação 2.0" neste artigo. Não há diferença em instalar o produto ou operacional, por exemplo, através do SAP HANA Cockpit dentro de uma VM do Azure, mas alguns itens importantes são obrigatórios para oficial de suporte no Azure. Esses pontos-chave são descritos nas seções a seguir. 

SAP HANA DT 2.0 não é suportada pelo SAP BW ou S4HANA. Os casos de utilização principal agora são aplicativos nativos do HANA.


### <a name="overview"></a>Descrição geral

A imagem seguinte fornece uma visão geral do suporte de DT 2.0 no Microsoft Azure. Siga estes requisitos obrigatórios para estar em conformidade com a certificação oficial:

- DT 2.0 tem de ser instalado numa VM do Azure dedicado. Poderá não ser executada na mesma VM em que o SAP HANA é executado.
- SAP HANA e DT 2.0 VMs tem de ser implementadas dentro da mesma rede virtual do Azure.
- O SAP HANA e DT 2.0 VMs tem de ser implementadas com o Azure Accelerated Networking ativada.
- O tipo de armazenamento para as VMs de 2.0 DT deve ser o armazenamento premium do Azure.
- Vários discos do Azure devem ser ligados à DT 2.0 VM.
- Criação de um software de RAID ou repartido volume, por meio de LVM ou mdadm, é necessário, através de repartição em todos os discos do Azure.

As secções seguintes fornecem mais de explicação.

![Descrição geral da arquitetura do SAP HANA DT 2.0](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>VM do Azure dedicada para SAP HANA DT 2.0

No Azure IaaS, DT 2.0 é suportada apenas numa VM dedicada. DT 2.0 não é permitida a execução na mesma VM do Azure onde está a executar a instância do HANA. Inicialmente, os dois tipos VM podem ser utilizados para executar o SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Para obter descrições de tipo VM, veja [tamanhos de máquinas virtuais com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

Tendo em conta a idéia básica do DT 2.0, que é descartar a dados muito ou pouco para reduzir os custos, faz sentido usar tamanhos de VM correspondentes. Não há nenhuma regra rígida para as combinações possíveis. Depende da carga de trabalho do cliente específico.

A tabela seguinte mostra as configurações recomendadas.

| Tipo de VM do SAP HANA | Tipo de VM de 2.0 DT |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Todas as combinações de VMs de série M com certificação SAP HANA com VMs de 2.0 DT suportadas, como M64 32ms e E32sv3, são possíveis.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Redes do Azure e o SAP HANA DT 2.0

Instalar DT 2.0 numa VM dedicada requer o débito de rede entre a VM de 2.0 DT e SAP HANA VM com um mínimo de 10 GB. Como resultado, é obrigatório para colocar todas as VMs dentro da mesma rede virtual do Azure e ativar o Azure Accelerated Networking.

Para obter mais informações sobre o Azure Accelerated Networking, veja [criar uma máquina virtual Linux com redes aceleradas](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

### <a name="vm-storage-for-sap-hana-dt-20"></a>Armazenamento VM para SAP HANA DT 2.0

De acordo com o DT 2.0 práticas recomendadas, o débito de e/s de disco mínimo é de 50 MB por segundo por núcleo físico. Olhando para a especificação para os dois tipos de VM do Azure, que são suportadas para DT 2.0, o limite de taxa de transferência de e/s para a VM de disco máximo é:

- **E32sv3**:   768 MB/seg, não colocado em cache, que significa que um rácio de 48 MB/s por núcleo físico
- **M64-32ms**:  1000 MB/s, não colocado em cache, que significa que um rácio de 62.5 MB/s por núcleo físico

É necessário anexar vários discos do Azure para a VM de 2.0 DT e criação de um RAID de software com repartição no nível do SO para atingir o limite máximo de débito de disco por VM. Um único disco do Azure não é possível fornecer o débito para atingir o limite máximo de VM. Armazenamento premium do Azure é obrigatório para executar DT 2.0. 

- Para obter mais informações sobre os tipos de disco do Azure disponíveis, consulte [selecionar um tipo de disco para VMs do Azure IaaS Windows](../../windows/disks-types.md).
- Para obter mais informações sobre como criar um RAID de software por meio de mdadm, consulte [configurar uma RAID de software no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
- Para obter mais informações sobre como configurar LVM para criar um volume repartido para débito máximo, consulte [configurar LVM numa VM do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).

Dependendo dos requisitos de tamanho, existem diferentes opções para alcançar o débito máximo de uma VM. Seguem-se as configurações de disco do volume de dados para cada tipo de VM de 2.0 DT atingir o limite de taxa de transferência VM superior. A VM E32sv3 é considerada um nível de entrada para cargas de trabalho mais pequenas. Se não for rápida o suficiente, poderá ser necessário redimensionar a VM para M64 32ms.

Uma vez que a VM de M64 32ms tem uma grande quantidade de memória, a carga de e/s não atinja o limite, especialmente para cargas de trabalho de leitura intensiva. Menos discos no conjunto de stripe poderão ser suficientes consoante a carga de trabalho específicas do cliente. Para ser arriscar, as seguintes configurações de disco foram escolhidas para garantir débito máximo.


| SKU DE VM | Configuração de disco 1 | Configuração de disco 2 | Configuração de disco 3 | Configuração de disco 4 | Configuração de disco 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3.5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2.5 TB | 6 x P15 -> 1.5 TB | 


Especialmente se a carga de trabalho é de leitura intensiva, Ativando a definição de "só de leitura" de cache do anfitrião do Azure, tal como recomendado para os volumes de dados do software de base de dados pode aumentar o desempenho de e/s. Para o log de transação, o cache de disco do anfitrião do Azure tem de ser "none". 

O ponto de partida que recomendamos para o tamanho do volume de registo é uma heurística de 15% do tamanho dos dados. Para criar o volume de registo, utilize tipos de disco do Azure diferente dependendo dos requisitos de custo e o débito. Para o volume de registo, o alto débito de e/s é necessário. 

Se utilizar a VM escreva M64 32ms, recomendamos que ative [acelerador de escrita](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Acelerador de escrita é uma funcionalidade do Azure que permite a latência de escrita de disco ideal para o log de transação. Está disponível apenas para a série M. Existem alguns itens a ter em consideração, como o número máximo de discos por tipo de VM. Para obter mais informações sobre o acelerador de escrita, consulte [ativar o acelerador de escrita](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


A tabela seguinte mostra alguns exemplos para ajudar a dimensionar o volume de registo.

| tipo de disco e o tamanho do volume de dados | iniciar a configuração de tipo de disco e volume 1 | iniciar a configuração de tipo de disco e volume 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2.5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Assim como horizontal do SAP HANA, o diretório de /hana/shared deve ser compartilhado entre a VM do SAP HANA e a VM de 2.0 DT. Recomendamos que utilize a mesma arquitetura como para horizontal do SAP HANA através da utilização de VMs dedicadas, que funcionam como um servidor NFS elevada disponibilidade. Para fornecer um volume partilhado de cópia de segurança, utilize o design idêntico. Mas cabe a si decidir se elevada disponibilidade é necessária ou se é suficiente utilizar uma VM dedicada com capacidade de armazenamento suficiente para atuar como um servidor de cópia de segurança.



### <a name="links-to-dt-20-documentation"></a>Ligações para documentação DT 2.0 

- [Guia de atualização de instalação de camadas dinâmica de SAP HANA e](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Tutoriais de camadas dinâmicas de SAP HANA e recursos](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dinâmica camada uma prova de conceito](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
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
Se tiver uma ligação site a site entre as suas localizações no local e o Azure e que está a executar os componentes do SAP, provavelmente já está a executar SAProuter. Neste caso, siga estes passos para obter suporte remoto:

- Manter o endereço IP estático e privado da VM que aloja SAP HANA na configuração SAProuter.
- Configure o NSG da sub-rede que aloja a VM do HANA para permitir o tráfego através da porta de TCP/IP 3299.

Se ligar ao Azure através da internet e não tiver um router SAP para a VM com o SAP HANA, instale o componente. Instale SAProuter numa VM separada na sub-rede de gestão. 

A imagem seguinte mostra um esquema aproximado para implementar o SAP HANA sem uma ligação site a site e com SAProuter:

![Aproximada esquema de implementação para o SAP HANA sem uma ligação site a site e SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Certifique-se de que instalar SAProuter numa VM separada e não na sua VM da JumpBox. A VM separada tem de ter um endereço IP estático. Para ligar o seu SAProuter para o SAProuter que é alojado pela SAP, contacte o SAP para um endereço IP. O que é alojado pela SAP SAProuter é a contraparte da instância SAProuter que instalou na sua VM. Utilize o endereço IP do SAP para configurar a sua instância de SAProuter. As definições de configuração, a porta apenas necessária é a porta TCP 3299.

Para obter mais informações sobre como configurar e manter as ligações de suporte remoto através de SAProuter, consulte [documentação de SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Elevada disponibilidade com o SAP HANA em VMs nativas do Azure
Se executar SUSE Linux Enterprise Server para o SAP aplicativos 12 SP1 ou posterior, pode estabelecer um cluster de Pacemaker com dispositivos STONITH. Utilize os dispositivos para definir uma configuração de SAP HANA que utiliza a replicação síncrona com o HANA System Replication e ativação pós-falha automática. Para obter mais informações sobre o procedimento de configuração, consulte [guia de elevada disponibilidade do SAP HANA para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
