---
title: Azure VMs alta disponibilidade para SAP NW em guia RHEL multi-SID | Microsoft Docs
description: Estabeleça uma elevada disponibilidade para SAP NW em máquinas virtuais Azure (VMs) RHEL multi-SID.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/11/2021
ms.author: radeltch
ms.openlocfilehash: ec2121754a24a44288c158e630a4e84219c744e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676920"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Alta disponibilidade para SAP NetWeaver em VMs Azure em Red Hat Enterprise Linux para aplicações SAP multi-SID guide

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Este artigo descreve como implementar vários sistemas SAP NetWeaver altamente disponíveis (isto é, multi-SID) num cluster de dois nós em VMs Azure com Red Hat Enterprise Linux para aplicações SAP.  

Nas configurações de exemplo, os comandos de instalação, etc. três sistemas SAP NetWeaver 7.50 são implantados num único cluster de alta disponibilidade de dois nós. Os SIDs dos sistemas SAP são:
* **NW1**: Instância ASCS número **00** e nome de anfitrião virtual **msnw1ascs;** ERS instância número **02** e nome de anfitrião virtual **msnw1ers**.  
* **NW2**: Instância ASCS número **10** e nome de anfitrião virtual **msnw2ascs;** ERS instância número **12** e nome de anfitrião virtual **msnw2ers**.  
* **NW3**: Instância ASCS número **20** e nome de anfitrião virtual **msnw3ascs;** ERS instância número **22** e nome de anfitrião virtual **msnw3ers**.  

O artigo não cobre a camada de base de dados e a implantação das ações sap NFS. Nos exemplos deste artigo, estamos a utilizar o volume **sapMSID** dos [Ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) para as ações da NFS, assumindo que o volume já está implantado. Assumimos também que o volume de Ficheiros Azure NetApp é implementado com o protocolo NFSv3 e que existem os seguintes caminhos de ficheiros para os recursos de cluster para as instâncias ASCS e ERS dos sistemas SAP NW1, NW2 e NW3:  

* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>ASCs NW1)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap sys<b>NW1)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW2)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap<b>ascs NW2)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap sys<b>NW2)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW3)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap<b>ascs NW3)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap<b>sys NW3)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

Antes de começar, consulte primeiro as seguintes notas e documentos SAP:

* Nota SAP [1928533], que tem:
  * Lista de tamanhos Azure VM que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade dos tamanhos Azure VM
  * Combinações suportadas de software SAP e sistema operativo (OS) e de base de dados
  * Versão kernel SAP necessária para Windows e Linux no Microsoft Azure
* [Documentação dos Ficheiros Azure NetApp][anf-azure-doc]
* O SAP Note [2015553] lista pré-requisitos para implementações de software SAP suportadas pelo SAP em Azure.
* SAP Note [2002167] recomendou definições de SO para Red Hat Enterprise Linux
* SAP Note [2009879] tem orientações SAP HANA para Red Hat Enterprise Linux
* A Nota [SAP 2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* Sap Note [2191498] tem a versão necessária do Agente anfitrião SAP para Linux em Azure.
* Sap Nota [2243692] tem informações sobre o licenciamento SAP em Linux em Azure.
* Sap Nota [1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização melhorada de Azure para o SAP.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [Azure Virtual Machines planejamento e implementação para SAP em Linux][planning-guide]
* [Implementação de Máquinas Virtuais Azure para SAP em Linux][deployment-guide]
* [Azure Virtual Machines DBMS implantação para SAP em Linux][dbms-guide]
* [SAP Netweaver em cluster pacemaker](https://access.redhat.com/articles/3150081)
* Documentação geral do RHEL
  * [Visão geral Add-On de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Alta Disponibilidade Add-On Administração](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de Add-On de Alta Disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configuração ASCS/ERS para SAP Netweaver com recursos autónomos em RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configure SAP S/4HANA ASCS/ERS com Standalone Enqueue Server 2 (ENSA2) em Pacemaker em RHEL](https://access.redhat.com/articles/3974941)
* Documentação RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade RHEL - Microsoft Azure Virtual Machines como Membros do Cluster](https://access.redhat.com/articles/3131341)
  * [Instalar e Configurar um Red Hat Enterprise Linux 7.4 (e mais tarde) High-Availability Cluster no Microsoft Azure](https://access.redhat.com/articles/3252491)
* [Aplicações NETApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Descrição geral

As máquinas virtuais, que participam no cluster, devem ser dimensionadas para poderem executar todos os recursos, caso ocorram falhas. Cada SAP SID pode falhar independentemente uns dos outros no cluster de alta disponibilidade multi-SID.  

Para obter uma elevada disponibilidade, o SAP NetWeaver requer ações altamente disponíveis. Nesta documentação, apresentamos os exemplos com as ações SAP implantadas nos [volumes NFS dos Ficheiros Azure NetApp.](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) Também é possível hospedar as ações em [cluster GlusterFS](./high-availability-guide-rhel-glusterfs.md)altamente disponível, que pode ser usado por vários sistemas SAP.  

![Visão geral de alta disponibilidade do SAP NetWeaver](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> O suporte para o agrupamento multi-SID do SAP ASCS/ERS com o Red Hat Linux como sistema operativo convidado em VMs Azure está limitado a **cinco** SIDs SAP no mesmo cluster. Cada novo SID aumenta a complexidade. Não é **suportada** uma mistura de Servidor de Replicação de Enqueue SAP 1 e Do Servidor de Replicação enqueue 2 no mesmo cluster . O agrupamento multi-SID descreve a instalação de múltiplos casos SAP ASCS/ERS com diferentes SIDs num cluster pacemaker. Atualmente, o clustering multi-SID só é suportado para ASCS/ERS.  

> [!TIP]
> O agrupamento multi-SID do SAP ASCS/ERS é uma solução com maior complexidade. É mais complexo de implementar. Envolve também um maior esforço administrativo, na execução de atividades de manutenção (como remendos de SO). Antes de iniciar a implementação real, desloque-se para planear cuidadosamente a implementação e todos os componentes envolvidos como VMs, suportes NFS, VIPs, configurações do balançador de carga e assim por diante.  

SAP NetWeaver ASCS, SAP NetWeaver SCS e SAP NetWeaver ERS utilizam nome de hospedeiro virtual e endereços IP virtuais. No Azure, é necessário um equilibrador de carga para utilizar um endereço IP virtual. Recomendamos a utilização [do balanceador de carga standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

A lista a seguir mostra a configuração do balançador de carga (A)SCS e ERS para este exemplo de cluster multi-SID com três sistemas SAP. Você precisará de IP frontend separado, sondas de saúde e regras de equilíbrio de carga para cada instância ASCS e ERS para cada um dos SIDs. Atribua todos os VMs, que fazem parte do cluster ASCS/ASCS a um pool de backend de um único ILB.  

### <a name="ascs"></a>a (A) SCS

* Configuração frontend
  * Endereço IP para NW1: 10.3.1.50
  * Endereço IP para NW2: 10.3.1.52
  * Endereço IP para NW3: 10.3.1.54

* Portas de sonda
  * Porto 620 <strong> &lt; nr &gt;</strong>, portanto para as portas de sonda NW1, NW2 e NW3 620 **00**, 620 **10** e 620 **20**
* Regras de equilíbrio de carga - crie uma para cada instância, isto é, NW1/ASCS, NW2/ASCS e NW3/ASCS.
  * Se utilizar o Balanceador de Carga Padrão, selecione **portas HA**
  * Se utilizar o Balanceador de Carga Básica, crie regras de equilíbrio de carga para as seguintes portas
    * 32<strong> &lt; nr &gt; </strong> TCP
    * 36<strong> &lt; nr &gt; </strong> TCP
    * 39<strong> &lt; nr &gt; </strong> TCP
    * 81<strong> &lt; nr &gt; </strong> TCP
    * 5<strong> &lt; nr &gt; </strong>13 TCP
    * 5<strong> &lt; nr &gt; </strong>14 TCP
    * 5<strong> &lt; nr &gt; </strong>16 TCP

### <a name="ers"></a>ERS

* Configuração frontend
  * Endereço IP para NW1 10.3.1.51
  * Endereço IP para NW2 10.3.1.53
  * Endereço IP para NW3 10.3.1.55

* Porto de Sonda
  * Porta 621 <strong> &lt; nr &gt;</strong>, portanto, para as portas de sonda NW1, NW2 e N3 621 **02**, 621 **12** e 621 **22**
* Regras de equilíbrio de carga - crie uma para cada instância, isto é, NW1/ERS, NW2/ERS e NW3/ERS.
  * Se utilizar o Balanceador de Carga Padrão, selecione **portas HA**
  * Se utilizar o Balanceador de Carga Básica, crie regras de equilíbrio de carga para as seguintes portas
    * 32<strong> &lt; nr &gt; </strong> TCP
    * 33<strong> &lt; nr &gt; </strong> TCP
    * 5<strong> &lt; nr &gt; </strong>13 TCP
    * 5<strong> &lt; nr &gt; </strong>14 TCP
    * 5<strong> &lt; nr &gt; </strong>16 TCP

* Configuração de backend
  * Ligado às interfaces de rede primária de todas as máquinas virtuais que devem fazer parte do cluster (A)SCS/ERS

> [!IMPORTANT]
> O IP flutuante não é suportado numa configuração IP secundária do NIC em cenários de equilíbrio de carga. Para mais detalhes consulte [as limitações do balançador de carga Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se precisar de um endereço IP adicional para o VM, implante um segundo NIC.  

> [!Note]
> Quando os VMs sem endereços IP públicos forem colocados no pool de backend de saldos de carga standard Azure (sem endereço IP público), não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos. Para obter detalhes sobre como alcançar a conectividade de saída, consulte [a conectividade do ponto final público para máquinas virtuais utilizando o Azure Standard Load Balancer em cenários de alta disponibilidade SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Não ative os cartas temporais TCP em VMs Azure colocados atrás do Balançador de Carga Azure. Permitir os tempos de TCP fará com que as sondas de saúde falhem. Definir o parâmetro **net.ipv4.tcp_timestamps** a **0**. Para mais detalhes consulte [as sondas de saúde load balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="sap-shares"></a>Ações da SAP

O SAP NetWeaver requer armazenamento partilhado para o transporte, diretório de perfis, e assim por diante. Para um sistema SAP altamente disponível, é importante ter ações altamente disponíveis. Terá de decidir sobre a arquitetura das suas ações SAP. Uma opção é implementar as ações nos [volumes NFS dos Ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  Com os Ficheiros Azure NetApp, obterá alta disponibilidade para as ações SAP NFS.

Outra opção é construir [GlusterFS em VMs Azure em Red Hat Enterprise Linux para SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md), que pode ser partilhado entre vários sistemas SAP. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Implementar o primeiro sistema SAP no cluster

Agora que decidiu sobre a arquitetura das ações SAP, implemente o primeiro sistema SAP no cluster, seguindo a documentação correspondente.

* Se utilizar volumes NFS de Ficheiros Azure NetApp, siga [a alta disponibilidade de VMs Azure para SAP NetWeaver no Red Hat Enterprise Linux com ficheiros Azure NetApp para aplicações SAP](./high-availability-guide-rhel-netapp-files.md)  
* Se utilizar o cluster GlusterFS, siga [o GlusterFS em VMs Azure na Red Hat Enterprise Linux para SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md).  

Os documentos acima mencionados irão guiá-lo através dos passos para preparar a infraestrutura necessária, construir o cluster, preparar o SO para executar a aplicação SAP.  

> [!TIP]
> Teste sempre a falha sobre a funcionalidade do cluster, depois de o primeiro sistema ser implantado, antes de adicionar os SIDs SAP adicionais ao cluster. Desta forma saberá que a funcionalidade do cluster funciona, antes de adicionar a complexidade de sistemas SAP adicionais ao cluster.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Implementar sistemas SAP adicionais no cluster

Neste exemplo, assumimos que o sistema **NW1** já estava implantado no cluster. Mostraremos como implantar nos sistemas DE SAP de cluster **NW2** e **NW3**. 

Os seguintes itens são prefixados com **ambos [A]** - aplicável a todos os nós, **[1]** - apenas aplicável ao nó 1 ou **[2]** - apenas aplicáveis ao nó 2.

### <a name="prerequisites"></a>Pré-requisitos 

> [!IMPORTANT]
> Antes de seguir as instruções para colocar sistemas SAP adicionais no cluster, siga as instruções para implantar o primeiro sistema SAP no cluster, uma vez que existem passos que só são necessários durante a primeira implantação do sistema.  

Esta documentação pressupõe que:
* O cluster Pacemaker já está configurado e a funcionar.  
* Pelo menos um sistema SAP (instância ASCS/ERS) já está implantado e está a funcionar no cluster.  
* A funcionalidade de failover do cluster foi testada.  
* As ações da NFS para todos os sistemas SAP são implantadas.  

### <a name="prepare-for-sap-netweaver-installation"></a>Preparar para instalação SAP NetWeaver

1. Adicione a configuração para o sistema recém-implantado (isto é, **NW2**, **NW3**) ao equilibrador de carga Azure existente, seguindo as instruções Implementar manualmente o [Balançador de Carga Azure através do portal Azure](./high-availability-guide-rhel-netapp-files.md#deploy-linux-manually-via-azure-portal). Ajuste os endereços IP, portas de sonda de saúde, regras de equilíbrio de carga para a sua configuração.  

2. **[A]** Resolução de nome de configuração para os sistemas SAP adicionais. Pode utilizar o servidor DNS ou modificar `/etc/hosts` todos os nós. Este exemplo mostra como utilizar o `/etc/hosts` ficheiro.  Adapte os endereços IP e os nomes dos anfitriões ao seu ambiente. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** Crie os diretórios partilhados para os sistemas **ADICIONAIS NW2** e **NW3** SAP que está a implantar no cluster. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** Adicione as entradas de montagem para os sistemas de ficheiros /sapmnt/SID/SYS para os sistemas SAP adicionais que está a implantar no cluster. Neste exemplo **NW2** e **NW3**.  

   Atualize o ficheiro `/etc/fstab` com os sistemas de ficheiros para os sistemas SAP adicionais que está a implementar no cluster.  

   * Se utilizar ficheiros Azure NetApp, siga as instruções [aqui](./high-availability-guide-rhel-netapp-files.md#prepare-for-sap-netweaver-installation)  
   * Se utilizar o cluster GlusterFS, siga as instruções [aqui](./high-availability-guide-rhel.md#prepare-for-sap-netweaver-installation)  

### <a name="install-ascs--ers"></a>Instalar ASCS / ERS

1. Crie os recursos virtuais de IP e sonda de saúde para os casos ASCS dos sistemas SAP adicionais que está a implantar no cluster. O exemplo mostrado aqui é para **NW2** e **NW3** ASCS, usando volumes NFS em Azure NetApp Files com protocolo NFSv3.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Certifique-se de que o estado do cluster está bem e que todos os recursos são iniciados. Não é importante em que nó os recursos estão a funcionar.  

2. **[1]** Instalar OSCS SAP NetWeaver  

   Instale o ASCS SAP NetWeaver como raiz, utilizando um nome de anfitrião virtual que mapeia para o endereço IP da configuração frontal do balançador de carga para o ASCS. Por exemplo, para o sistema **NW2**, o nome de hospedeiro virtual é <b>msnw2ascs</b>, <b>10.3.1.52</b> e o número de exemplo que usou para a sonda do equilibrador de carga, por exemplo <b>10</b>. Para o sistema **NW3**, o nome de hospedeiro virtual é <b>msnw3ascs</b>, <b>10.3.1.54</b> e o número de instância que usou para a sonda do equilibrador de carga, por exemplo <b>20</b>. Note em que nó de cluster instalou ASCS para cada SAP SID.  

   Pode utilizar o parâmetro de seiva SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não-raiz se conecte ao sapinst. Pode utilizar SAPINST_USE_HOSTNAME de parâmetros para instalar o SAP, utilizando o nome de anfitrião virtual.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Se a instalação não criar uma sub-dobradeira em /usr/seiva/**SID**/ASCS **Instance#**, tente configurar o proprietário para **sid** adm e grupo para sapsys da **ASCS Instance#** e re-tentar.

3. **[1]** Crie um cluster virtual de IP e sonda de saúde para a instância ERS do sistema SAP adicional que está a implantar no cluster. O exemplo mostrado aqui é para **NW2** e **NW3** ERS, utilizando volumes NFS em Azure NetApp Files com protocolo NFSv3.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Certifique-se de que o estado do cluster está bem e que todos os recursos são iniciados.  

   Em seguida, certifique-se de que os recursos do recém-criado grupo ERS, estão a funcionar no nó de cluster, em frente ao nó de cluster onde foi instalada a instância ASCS para o mesmo sistema SAP.  Por exemplo, se o ASCS NW2 foi `rhelmsscl1` instalado, certifique-se de que o grupo NW2 ERS está em funcionamento `rhelmsscl2` .  Pode migrar o grupo NW2 ERS para `rhelmsscl2` executar o seguinte comando para um dos recursos de cluster do grupo: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** Instalar SAP NetWeaver ERS

   Instale o SAP NetWeaver ERS como raiz no outro nó, utilizando um nome de anfitrião virtual que mapeia para o endereço IP da configuração frontal do balançador de carga para o ERS. Por exemplo, para o sistema **NW2**, o nome de anfitrião virtual será <b>msnw2ers</b>, <b>10.3.1.53</b> e o número de exemplo que usou para a sonda do equilibrador de carga, por exemplo <b>12</b>. Para o sistema **NW3**, o nome de anfitrião virtual <b>msnw3ers</b>, <b>10.3.1.55</b> e o número de instância que usou para a sonda do esquilibrador de carga, por exemplo <b>22</b>. 

   Pode utilizar o parâmetro de seiva SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não-raiz se conecte ao sapinst. Pode utilizar SAPINST_USE_HOSTNAME de parâmetros para instalar o SAP, utilizando o nome de anfitrião virtual.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Utilize SWPM SP 20 PL 05 ou superior. As versões inferiores não definem corretamente as permissões e a instalação falhará.

   Se a instalação não criar uma sub-dobragem em /usr/seiva/**NW2**/ERS **Instance#**, tente configurar o proprietário **para** sid adm e o grupo para sapsys da pasta ERS **Instance#** e re-tentar.

   Se foi necessário migrar o grupo ERS do recém-implantado sistema SAP para um nó de cluster diferente, não se esqueça de remover a restrição de localização para o grupo ERS. Pode remover a restrição executando o seguinte comando (o exemplo é dado para os sistemas SAP **NW2** e **NW3).** Certifique-se de remover os constrangimentos temporários para o mesmo recurso utilizado no comando para mover o grupo de cluster ERS.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** Adaptar os perfis de instância ASCS/SCS e ERS para o ou os sistemas SAP recentemente instalados. O exemplo apresentado abaixo é para NW2. Terá de adaptar os perfis ASCS/SCS e ERS para todos os casos SAP adicionados ao cluster.  
 
   * Perfil ASCS/SCS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter, if using ENSA1
      enque/encni/set_so_keepalive = true
      ```

     Tanto para a ENSA1 como para a ENSA2, certifique-se de que os `keepalive` parâmetros de SO estão definidos como descrito na nota [SAP 1410736](https://launchpad.support.sap.com/#/notes/1410736).    

   * Perfil ERS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** Atualizar o ficheiro /usr/sap/sapservices

   Para evitar o início dos casos pelo script de arranque sapinit, todas as instâncias geridas pela Pacemaker devem ser comentadas a partir do `/usr/sap/sapservices` ficheiro.  O exemplo apresentado abaixo é para os sistemas SAP **NW2** e **NW3**.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** Criar os recursos de cluster SAP para o sistema SAP recentemente instalado.  

   Se utilizar a arquitetura do servidor 1 do enqueue (ENSA1), defina os recursos para os sistemas SAP **NW2** e **NW3** da seguinte forma:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   A SAP introduziu suporte para o servidor 2, incluindo a replicação, a partir de SAP NW 7.52. A partir da Plataforma ABAP 1809, o servidor 2 do enqueue é instalado por padrão. Consulte a nota [SAP 2630416](https://launchpad.support.sap.com/#/notes/2630416) para obter suporte ao servidor 2.
   Se utilizar a arquitetura do servidor 2 do enqueue [(ENSA2),](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)defina os recursos para os sistemas SAP **NW2** e **NW3** da seguinte forma:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Se estiver a atualizar a partir de uma versão mais antiga e a mudar para o servidor 2, consulte a nota [SAP 2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   > [!NOTE]
   > Os intervalos de tempo na configuração acima são apenas exemplos e podem ter de ser adaptados à configuração ESPECÍFICA DOM. 

   Certifique-se de que o estado do cluster está bem e que todos os recursos são iniciados. Não é importante em que nó os recursos estão a funcionar.
   O exemplo a seguir mostra o estado dos recursos de cluster, depois dos sistemas SAP **NW2** e **NW3** terem sido adicionados ao cluster. 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** Adicione as regras de firewall para ASCS e ERS em ambos os nós.  O exemplo abaixo mostra as regras de firewall tanto para os sistemas SAP **NW2 como** **PARA NW3**.  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3212/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3212/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3222/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3222/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Proceda à instalação SAP 

Complete a sua instalação SAP por:

* [Preparando os seus servidores de aplicações SAP NetWeaver](./high-availability-guide-rhel-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Instalação de uma instância DBMS](./high-availability-guide-rhel-netapp-files.md#install-database)
* [Instalação de um servidor de aplicação PRINCIPAL SAP](./high-availability-guide-rhel-netapp-files.md#sap-netweaver-application-server-installation)
* Instalação de uma ou mais instâncias adicionais de aplicação SAP

## <a name="test-the-multi-sid-cluster-setup"></a>Teste a configuração do cluster multi-SID

Os seguintes testes são um subconjunto dos casos de teste nos guias de boas práticas do Chapéu Vermelho. Estão incluídos para sua conveniência. Para a lista completa dos testes de agrupamento, consulte a seguinte documentação:

* Se utilizar volumes NFS de Ficheiros Azure NetApp, siga a alta disponibilidade de [VMs Azure para SAP NetWeaver no RHEL com ficheiros Azure NetApp para aplicações SAP](./high-availability-guide-rhel-netapp-files.md)
* Se utilizar uma alta disponibilidade, siga a disponibilidade elevada de `GlusterFS` [VMs Azure para SAP NetWeaver no RHEL para aplicações SAP](./high-availability-guide-rhel.md).  

Leia sempre os guias de boas práticas do Chapéu Vermelho e realize todos os testes adicionais que possam ter sido adicionados.  
Os testes apresentados estão num cluster multi-SID de dois nó, com três sistemas SAP instalados.  

1. Migrar manualmente a instância ASCS. O exemplo mostra a migração da instância ASCS para o sistema SAP NW3.

   Estado dos recursos antes de iniciar o teste:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Executar os seguintes comandos como raiz para migrar a instância NW3 ASCS.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Estado de recursos após o teste:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Simular queda de nó

   Estado dos recursos antes de iniciar o teste:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Executar o seguinte comando como raiz num nó, onde pelo menos uma instância ASCS está em execução. Neste exemplo, executamos o comando em `rhelmsscl1` , onde estão a decorrer os casos ASCS para NW1, NW2 e NW3.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   O estado após o teste, e depois do nó, que foi batido, recomeçou, deve ser assim.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Se houver mensagens para recursos falhados, limpe o estado dos recursos falhados. Por exemplo:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Passos seguintes

* [Azure Virtual Machines planejamento e implementação para SAP][planning-guide]
* [Implantação de máquinas virtuais Azure para SAP][deployment-guide]
* [Implantação DBMS de máquinas virtuais Azure para SAP][dbms-guide]
* Para aprender a estabelecer uma elevada disponibilidade e plano para a recuperação de desastres da SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]