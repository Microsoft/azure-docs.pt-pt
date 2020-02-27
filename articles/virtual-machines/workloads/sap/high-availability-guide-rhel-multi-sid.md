---
title: VMs azure alta disponibilidade para SAP NW no guia RHEL multi-SID  Microsoft Docs
description: Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.openlocfilehash: 1c52e7e30ac02b14356284f0506824b5a7d0188a
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652051"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Alta disponibilidade para SAP NetWeaver em VMs Azure em Red Hat Enterprise Linux para aplicações SAP guia multi-SID

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533,]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[SAP 1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Este artigo descreve como implementar vários sistemas SAP NetWeaver altamente disponíveis (isto é, multi-SID) num cluster de dois nós em VMs Azure com Red Hat Enterprise Linux para aplicações SAP.  

Nas configurações de exemplo, comandos de instalação etc. três sistemas SAP NetWeaver 7.50 são implantados num único aglomerado de alta disponibilidade de dois nós. Os Sistemas SAP São:
* **NW1**: Ascs exemplo número **00** e nome de hospedeiro virtual **msnw1ascs;** Ers exemplo **número 02** e nome de anfitrião virtual **msnw1ers**.  
* **NW2**: Exemplo ASCS número **10** e **msnw2ascs**de nome de anfitrião virtual; Ers exemplo número **12** e nome de anfitrião virtual **msnw2ers**.  
* **NW3**: Exemplo ASCS número **20** e nome de anfitrião virtual **msnw3ascs;** Ers exemplo número **22** e nome de anfitrião virtual **msnw3ers**.  

O artigo não cobre a camada de base de dados e a implantação das ações da SAP NFS. Nos exemplos deste artigo, estamos a utilizar o volume de ficheiros [Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) **sapMSID** para as ações da NFS, assumindo que o volume já está implementado. Estamos também assumindo que o volume de Ficheiros Azure NetApp é implementado com o protocolo NFSv3 e que existem os seguintes caminhos de ficheiros para os recursos de cluster para as instâncias ASCS e ERS dos sistemas SAP NW1, NW2 e NW3:  

* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW1)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW2)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW3)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

Antes de começar, consulte primeiro as seguintes Notas E papéis SAP:

* Nota SAP [1928533,]que tem:
  * Lista de tamanhos De VM Azure que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade para tamanhos de VM Azure
  * Software SAP suportado e sistema operativo (OS) e combinações de bases de dados
  * Versão necessária do kernel SAP para Windows e Linux no Microsoft Azure
* [Documentação de Ficheiros Azure NetApp][anf-azure-doc]
* O SAP Note [2015553] lista os pré-requisitos para implementações de software SAP suportadas pela SAP em Azure.
* SAP Note [2002167] recomendou definições de OS para Red Hat Enterprise Linux
* SAP Nota [2009879] tem Diretrizes SAP HANA para Red Hat Enterprise Linux
* O SAP Note [2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* O SAP Note [2191498] tem a versão necessária do Agente anfitrião SAP para o Linux em Azure.
* SAP Nota [2243692] tem informações sobre licenciamento SAP em Linux em Azure.
* A Nota [SAP 1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização avançada do Azure para sAP.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) exigiu todas as notas SAP para linux.
* [Planeamento e implementação de Máquinas Virtuais Azure para SAP em Linux][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP em Linux][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP em Linux][dbms-guide]
* [SAP Netweaver em cluster pacemaker](https://access.redhat.com/articles/3150081)
* Documentação Geral RHEL
  * [Visão geral de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração add-on de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência addon de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configurar ASCS/ERS para SAP Netweaver com recursos autónomos em RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configure SAP S/4HANA ASCS/ERS com O Servidor Enfila Autónomo 2 (ENSA2) em Pacemaker no RHEL](https://access.redhat.com/articles/3974941)
* Documentação RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade RHEL - Máquinas Virtuais Microsoft Azure como Membros do Cluster](https://access.redhat.com/articles/3131341)
  * [Instalação e Configuração de um Cluster de Alta Disponibilidade do Red Hat Enterprise Linux 7.4 (e mais tarde) no Microsoft Azure](https://access.redhat.com/articles/3252491)
* [Aplicações NetApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Descrição geral

As máquinas virtuais, que participam no cluster, devem ser dimensionadas para poderem executar todos os recursos, caso ocorra falhas. Cada SID SAP pode falhar sobre-independente si no cluster de alta disponibilidade multi-SID.  

Para alcançar uma elevada disponibilidade, a SAP NetWeaver requer ações altamente disponíveis. Nesta documentação, apresentamos os exemplos com as ações SAP implantadas nos [volumes NFS do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)Files . Também é possível alojar as ações no [cluster GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)altamente disponível, que pode ser usado por vários sistemas SAP.  

![Visão geral de alta disponibilidade da SAP NetWeaver](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> O suporte para o agrupamento multi-SID do SAP ASCS/ERS com o Red Hat Linux como sistema operativo convidado em VMs Azure está limitado a **cinco** SIDs SAP no mesmo cluster. Cada novo SID aumenta a complexidade. Não é **suportada**uma mistura de SAP Enqueue Replication Server 1 e Enqueue Replication Server 2 no mesmo cluster . O clustering multi-SID descreve a instalação de múltiplas instâncias SAP ASCS/ERS com diferentes SIDs num cluster Pacemaker. Atualmente, o clustering multi-SID é suportado apenas para ASCS/ERS.  

> [!TIP]
> O agrupamento multi-SID do SAP ASCS/ERS é uma solução com maior complexidade. É mais complexo de implementar. Envolve também um maior esforço administrativo, na execução de atividades de manutenção (como remendos de Os). Antes de iniciar a implementação real, tenha tempo para planear cuidadosamente a implementação e todos os componentes envolvidos como VMs, suportes NFS, VIPs, configurações de equilíbrio de carga e assim por diante.  

SAP NetWeaver ASCS, SAP NetWeaver SCS e SAP NetWeaver ERS usam o nome de anfitrião virtual e endereços IP virtuais. No Azure, é necessário utilizar um endereço IP virtual. Recomendamos a utilização do [equilíbrio de carga Standard](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal).  

A lista que se segue mostra a configuração do (A)SCS e do equilibrador de carga ERS para este exemplo de cluster multi-SID com três sistemas SAP. Você precisará de IP frontend separado, sondas de saúde e regras de equilíbrio de carga para cada ascs e ERS por exemplo para cada um dos SIDs. Atribuir todos os VMs, que fazem parte do cluster ASCS/ASCS para um pool de backend de um único ILB.  

### <a name="ascs"></a>A SCS

* Configuração frontend
  * Endereço IP para NW1: 10.3.1.50
  * Endereço IP para NW2: 10.3.1.52
  * Endereço IP para NW3: 10.3.1.54

* Portas de Sonda
  * Porto 620<strong>&lt;nr&gt;</strong>, portanto para portas de sonda NW1, NW2 e NW3 620**00**, 620**10** e 620**20**
* Regras de equilíbrio de carga - crie uma para cada instância, isto é, NW1/ASCS, NW2/ASCS e NW3/ASCS.
  * Se utilizar o Balancer de Carga Padrão, selecione **portas HA**
  * Se utilizar o Equilíbrio de Carga Básico, crie regras de equilíbrio de carga para as seguintes portas
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configuração frontend
  * Endereço IP para NW1 10.3.1.51
  * Endereço IP para NW2 10.3.1.53
  * Endereço IP para NW3 10.3.1.55

* Porta sonda
  * Porto 621<strong>&lt;nr&gt;</strong>, portanto para portas de sonda NW1, NW2 e N3 621**02**, 621**12** e 621**22**
* Regras de equilíbrio de carga - crie uma para cada instância, isto é, NW1/ERS, NW2/ERS e NW3/ERS.
  * Se utilizar o Balancer de Carga Padrão, selecione **portas HA**
  * Se utilizar o Equilíbrio de Carga Básico, crie regras de equilíbrio de carga para as seguintes portas
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

* Configuração de backend
  * Ligado às interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte do cluster (A)SCS/ERS

> [!Note]
> Quando os VMs sem endereços IP públicos forem colocados no conjunto de backend do interno (sem endereço IP público) O equilíbrio de carga Standard Azure não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos. Para mais detalhes sobre como alcançar a conectividade de saída, consulte [a conectividade do ponto final público para máquinas virtuais utilizando o Equilíbrio de Carga Padrão Azure em cenários de alta disponibilidade SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Não permita os carimbos de tempo da TCP em VMs Azure colocados atrás do Equilíbrio de Carga Azure. Permitir os selos temporais da TCP fará com que as sondas de saúde falhem. Definir parâmetro **net.ipv4.tcp_timestamps** a **0**. Para mais detalhes consulte as sondas de [saúde load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

## <a name="sap-shares"></a>Ações da SAP

A SAP NetWeaver requer armazenamento partilhado para o transporte, diretório de perfis, e assim por diante. Para o sistema SAP altamente disponível, é importante ter ações altamente disponíveis. Você precisará decidir sobre a arquitetura para as suas ações SAP. Uma opção é implementar as ações em [volumes NFS de ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  Com ficheiros Azure NetApp, obterá uma alta disponibilidade incorporada para as ações da SAP NFS.

Outra opção é construir [GlusterFS em VMs Azure em Red Hat Enterprise Linux para SAP NetWeaver,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)que pode ser partilhado entre vários sistemas SAP. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Implementar o primeiro sistema SAP no cluster

Agora que decidiu a arquitetura para as ações da SAP, implemente o primeiro sistema SAP no cluster, seguindo a documentação correspondente.

* Se utilizar volumes NFS de Ficheiros Azure NetApp, siga os [VMs Azure com elevada disponibilidade para SAP NetWeaver no Red Hat Enterprise Linux com Ficheiros Azure NetApp para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)  
* Se utilizar o cluster GlusterFS, siga [o GlusterFS em VMs Azure em Red Hat Enterprise Linux para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs).  

Os documentos acima indicados irão guiá-lo através dos passos para preparar a infraestrutura necessária, construir o cluster, preparar o SISTEMA para executar a aplicação SAP.  

> [!TIP]
> Teste sempre a falha sobre a funcionalidade do cluster, depois de implementado o primeiro sistema, antes de adicionar os SIDs SAP adicionais ao cluster. Dessa forma saberá que a funcionalidade do cluster funciona, antes de adicionar a complexidade de sistemas SAP adicionais ao cluster.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Implementar sistemas SAP adicionais no cluster

Neste exemplo, assumimos que o sistema **NW1** já estava implantado no cluster. Mostraremos como implantar nos sistemas SAP do cluster **NW2** e **NW3**. 

Os seguintes itens são pré-fixados com **[A]** - aplicável a todos os nós, **[1]** - apenas aplicável ao nó 1 ou **[2]** - apenas aplicável ao nó 2.

### <a name="prerequisites"></a>Pré-requisitos 

> [!IMPORTANT]
> Antes de seguir as instruções para a instalação de sistemas SAP adicionais no cluster, siga as instruções para implantar o primeiro sistema SAP no cluster, uma vez que existem passos que só são necessários durante a primeira implementação do sistema.  

Esta documentação pressupõe que:
* O cluster Pacemaker já está configurado e em execução.  
* Pelo menos um sistema SAP (instância ASCS/ERS) já está implantado e está em execução no cluster.  
* A funcionalidade de failover do cluster foi testada.  
* As ações da NFS para todos os sistemas SAP estão implantadas.  

### <a name="prepare-for-sap-netweaver-installation"></a>Prepare-se para a instalação SAP NetWeaver

1. Adicione a configuração para o sistema recém-implantado (isto é, **NW2,** **NW3**) ao existente Equilíbrio de Carga Azure, seguindo as instruções Desdobrar o Equilíbrio de [Carga Azure manualmente através](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#deploy-linux-manually-via-azure-portal)do portal Azure . Ajuste os endereços IP, portas de sonda de saúde, regras de equilíbrio de carga para a sua configuração.  

2. **[A]** Resolução de nomes de configuração para os sistemas SAP adicionais. Pode utilizar o servidor DNS ou modificar `/etc/hosts` em todos os nós. Este exemplo mostra como usar o ficheiro `/etc/hosts`.  Adapte os endereços IP e os nomes do anfitrião para o seu ambiente. 

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

3. **[A]** Crie os diretórios partilhados para os sistemas **nw2** e **NW3** sAP adicionais que está a implementar para o cluster. 

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

4. **[A]** Adicione as entradas de montagem para os sistemas de ficheiros /usr/sap/SID/SYS para os sistemas sAP adicionais que está a implantar para o cluster. Neste exemplo **NW2** e **NW3**.  

   Atualizar o ficheiro `/etc/fstab` com os sistemas de ficheiros para os sistemas SAP adicionais que está a implementar para o cluster.  

   * Se utilizar ficheiros Azure NetApp, siga as instruções [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#prepare-for-sap-netweaver-installation)  
   * Se utilizar o cluster GlusterFS, siga as instruções [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel#prepare-for-sap-netweaver-installation)  

### <a name="install-ascs--ers"></a>Instalar ASCS/ERS

1. Crie os recursos de cluster de IP e sonda de saúde virtuais para as instâncias ASCS dos sistemas SAP adicionais que está a implementar para o cluster. O exemplo mostrado aqui é para **NW2** e **NW3** ASCS, usando NFS em volumes de Ficheiros Azure NetApp com protocolo NFSv3.  

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

   Certifique-se de que o estado do cluster está ok e que todos os recursos estão iniciados. Não é importante em que nó dos recursos estão a funcionar.  

2. **[1]** Instalar asCS SAP NetWeaver  

   Instale o SAP NetWeaver ASCS como raiz, utilizando um nome de anfitrião virtual que mapeie para o endereço IP da configuração frontal do equilíbrio de carga para o ASCS. Por exemplo, para o sistema **NW2**, o nome de anfitrião virtual é <b>msnw2ascs</b>, <b>10.3.1.52</b> e o número de exemplo que usou para a sonda do equilibrista de carga, por exemplo <b>10</b>. Para o sistema **NW3**, o nome de anfitrião virtual é <b>msnw3ascs</b>, <b>10.3.1.54</b> e o número de exemplo que usou para a sonda do equilibrista de carga, por exemplo <b>20</b>. Note em que nó de cluster instalou ASCS para cada SID SAP.  

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst. Pode utilizar SAPINST_USE_HOSTNAME de parâmetros para instalar o SAP, utilizando o nome de hospedeiro virtual.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Se a instalação não criar uma subpasta em /usr/seap/**SID**/ASCS**Instance#** , tente configurar o proprietário para **sid**adm e agrupar para sapsys da instância ASCS# e retentar.

3. **[1]** Criar um IP virtual e recursos de cluster de sonda de saúde para a instância DEERS do sistema SAP adicional que está a implementar para o cluster. O exemplo aqui mostrado é para **NW2** e **NW3** ERS, usando NFS em volumes de Ficheiros Azure NetApp com protocolo NFSv3.  

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

   Certifique-se de que o estado do cluster está ok e que todos os recursos estão iniciados.  

   Em seguida, certifique-se de que os recursos do recém-criado grupo ERS, estão em execução no nó cluster, em frente ao nó de cluster onde foi instalado o ascs por exemplo para o mesmo sistema SAP.  Por exemplo, se o NW2 ASCS foi instalado no `rhelmsscl1`, certifique-se de que o grupo NW2 ERS está a funcionar em `rhelmsscl2`.  Pode migrar o grupo NW2 ERS para `rhelmsscl2` executando o seguinte comando para um dos recursos de cluster do grupo: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** Instalar SAP NetWeaver ERS

   Instale o SAP NetWeaver ERS como raiz no outro nó, utilizando um nome de anfitrião virtual que mapeie para o endereço IP da configuração frontal do equilíbrio de carga para o ERS. Por exemplo, para o sistema **NW2**, o nome de hospedeiro virtual será <b>msnw2ers</b>, <b>10.3.1.53</b> e o número de exemplo que usou para a sonda do equilibrador de carga, por exemplo <b>12</b>. Para o sistema **NW3**, o nome de hospedeiro virtual <b>msnw3ers</b>, <b>10.3.1.55</b> e o número de instância que usou para a sonda do equilibrador de carga, por exemplo <b>22</b>. 

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst. Pode utilizar SAPINST_USE_HOSTNAME de parâmetros para instalar o SAP, utilizando o nome de hospedeiro virtual.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Utilize SWPM SP 20 PL 05 ou superior. Versões inferiores não fixam corretamente as permissões e a instalação falhará.

   Se a instalação não criar uma subpasta em /usr/seap/**NW2**/ERS**Instance#** , tente colocar o proprietário na **sid**adm e no grupo para sapsys da pasta ERS**Instance#** e retentar.

   Se fosse necessário migrar o grupo ERS do sistema SAP recentemente implantado para um nó de cluster diferente, não se esqueça de remover a restrição de localização para o grupo ERS. Pode remover a restrição executando o seguinte comando (o exemplo é dado para os sistemas SAP **NW2** e **NW3**). Certifique-se de remover os constrangimentos temporários para o mesmo recurso utilizado no comando para mover o grupo de cluster ERS.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** Adaptar os perfis de instância ASCS/SCS e ERS para os sistemas SAP recentemente instalados. O exemplo abaixo mostrado é para NW2. Terá de adaptar os perfis ASCS/SCS e ERS para todas as instâncias SAP adicionadas ao cluster.  
 
   * Perfil ASCS/SCS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter
      enque/encni/set_so_keepalive = true
      ```

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

   Para evitar o início das ocorrências pelo script de arranque sapinit, todas as instâncias geridas pelo Pacemaker devem ser comentadas a partir de `/usr/sap/sapservices` ficheiro.  O exemplo abaixo indicado é para os sistemas SAP **NW2** e **NW3**.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** Criar os recursos de cluster SAP para o sistema SAP recentemente instalado.  

   Se utilizar a arquitetura enqueue server 1 (ENSA1), defina os recursos para os sistemas SAP **NW2** e **NW3** da seguinte forma:

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

   O SAP introduziu suporte para o servidor de fila 2, incluindo replicação, a partir de SAP NW 7.52. A partir da Plataforma ABAP 1809, o servidor enfila 2 é instalado por padrão. Consulte a nota SAP [2630416](https://launchpad.support.sap.com/#/notes/2630416) para obter suporte ao servidor 2.
   Se utilizar a arquitetura enqueue server 2[(ENSA2),](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)defina os recursos para os sistemas SAP **NW2** e **NW3** da seguinte forma:

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

   Se estiver a atualizar a partir de uma versão mais antiga e a mudar para o servidor de fila 2, consulte a nota [SAP 2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   > [!NOTE]
   > Os intervalos na configuração acima são apenas exemplos e podem ter de ser adaptados à configuração específica do SAP. 

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante em que nó dos recursos estão a funcionar.
   O exemplo seguinte mostra o estado dos recursos de cluster, depois de os sistemas SAP **NW2** e **NW3** terem sido adicionados ao cluster. 

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

8. **[A]** Adicione regras de firewall para ASCS e ERS em ambos os nós.  O exemplo abaixo mostra as regras de firewall para ambos os sistemas SAP **NW2** e **NW3**.  

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
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Prossiga com a instalação SAP 

Complete a sua instalação SAP por:

* [Preparação dos seus servidores de aplicação SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Instalação de uma instância DBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#install-database)
* [Instalação de um servidor de aplicação SAP primário](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#sap-netweaver-application-server-installation)
* Instalação de um ou mais casos adicionais de aplicações SAP

## <a name="test-the-multi-sid-cluster-setup"></a>Testar a configuração do cluster multi-SID

Os seguintes testes são um subconjunto dos casos de teste nos guias de boas práticas do Chapéu Vermelho. Estão incluídos para sua conveniência. Para a lista completa dos testes de cluster, consulte a seguinte documentação:

* Se utilizar volumes NFS de Ficheiros Azure NetApp, siga os [VMs Azure com elevada disponibilidade para SAP NetWeaver no RHEL com Ficheiros Azure NetApp para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* Se utilizar `GlusterFS`altamente disponíveis, siga os [VMs Azure com elevada disponibilidade para o SAP NetWeaver no RHEL para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel).  

Leia sempre os guias de boas práticas do Chapéu Vermelho e efetue todos os testes adicionais que possam ter sido adicionados.  
Os testes apresentados estão num cluster de dois nós, multi-SID com três sistemas SAP instalados.  

1. Migrar manualmente a instância ASCS. O exemplo mostra a migração da instância ASCS para o sistema SAP NW3.

   Estado de recurso antes de iniciar o teste:

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

   Estado de recurso após o teste:

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

1. Simular a queda do nó

   Estado de recurso antes de iniciar o teste:

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

   Executar o seguinte comando como raiz num nó, onde pelo menos uma instância ASCS está em execução. Neste exemplo, executamos o comando em `rhelmsscl1`, onde estão a decorrer os casos ASCS para NW1, NW2 e NW3.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   O estado após o teste, e depois do nó, que se despenhou, recomeçou, deve ser assim.

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

* [Planeamento e implementação de Máquinas Virtuais Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP][dbms-guide]
* Para aprender como estabelecer alta disponibilidade e plano para a recuperação de desastres de SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]
