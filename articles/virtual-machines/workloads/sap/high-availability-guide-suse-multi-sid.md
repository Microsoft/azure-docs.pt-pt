---
title: Alta disponibilidade de VMs do Azure para SAP NetWeaver em guia de vários SID do SLES | Microsoft Docs
description: Guia de alta disponibilidade de vários SIDs para SAP NetWeaver em SUSE Linux Enterprise Server para aplicativos SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/16/2020
ms.author: radeltch
ms.openlocfilehash: 94cf30d2d3650212707cf92db83236882fe5e49f
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169360"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications-multi-sid-guide"></a>Alta disponibilidade para SAP NetWeaver em VMs do Azure em SUSE Linux Enterprise Server para aplicativos SAP guia de vários SIDs

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Este artigo descreve como implantar vários sistemas SAP NetWeaver ou S4HANA altamente disponíveis (ou seja, vários SIDs) em um cluster de dois nós em VMs do Azure com SUSE Linux Enterprise Server para aplicativos SAP.  

Nas configurações de exemplo, comandos de instalação etc. três sistemas SAP NetWeaver 7,50 são implantados em um único cluster de alta disponibilidade de dois nós. Os SIDs de sistemas SAP são:
* **NW1**: número de instância de ASCS **00** e nome de host virtual **msnw1ascs**; Número de instância ERS **02** e nome de host virtual **msnw1ers**.  
* **NW2**: ASCS da instância número **10** e nome do host virtual **msnw2ascs**; ERS instância número **12** e nome de host virtual **msnw2ers**.  
* **NW3**: ASCS da instância número **20** e nome do host virtual **msnw3ascs**; Número de instância ERS **22** e nome de host virtual **msnw3ers**.  

O artigo não aborda a camada de banco de dados e a implantação dos compartilhamentos NFS do SAP. Nos exemplos deste artigo, estamos usando nomes virtuais NW2-NFS para os compartilhamentos NFS NW2 e NW3-NFS para os compartilhamentos NFS NW3, supondo que o cluster NFS foi implantado.  

Antes de começar, consulte as seguintes notas e documentos do SAP primeiro:

* Nota SAP [1928533][1928533], que tem:
  * Lista de tamanhos de VM do Azure com suporte para a implantação de software SAP
  * Informações de capacidade importantes para tamanhos de VM do Azure
  * Software SAP com suporte e combinações de so (sistema operacional) e banco de dados
  * Versão de kernel do SAP necessária para Windows e Linux em Microsoft Azure

* O SAP Note [2015553][2015553] lista os pré-requisitos para implantações de software SAP com suporte no SAP no Azure.
* A observação do SAP [2205917][2205917] tem as configurações do sistema operacional recomendadas para SuSE Linux Enterprise Server para aplicativos SAP
* O SAP Note [1944799][1944799] tem diretrizes SAP HANA para SuSE Linux Enterprise Server para aplicativos SAP
* A nota SAP [2178632][2178632] tem informações detalhadas sobre todas as métricas de monitoramento relatadas para SAP no Azure.
* A nota SAP [2191498][2191498] tem a versão do agente de host do SAP necessária para Linux no Azure.
* A nota SAP [2243692][2243692] tem informações sobre o licenciamento SAP no Linux no Azure.
* A nota SAP [1984787][1984787] tem informações gerais sobre o SuSE Linux Enterprise Server 12.
* A nota SAP [1999351][1999351] tem informações adicionais para solução de problemas para a extensão de monitoramento avançado do Azure para SAP.
* O [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [Planejamento e implementação de máquinas virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP no Linux][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [Guias de práticas recomendadas do SUSE SAP ha][suse-ha-guide] Os guias contêm todas as informações necessárias para configurar o NetWeaver HA e a replicação de sistema SAP HANA no local. Use esses guias como uma linha de base geral. Eles fornecem informações muito mais detalhadas.
* [Notas de versão da extensão de alta disponibilidade do SUSE 12 SP3][suse-ha-12sp3-relnotes]
* [Suporte do SUSE para cluster de vários SID](https://www.suse.com/c/sap-workloads-going-green/)

## <a name="overview"></a>Visão geral

As máquinas virtuais, que participam do cluster, devem ser dimensionadas para poder executar todos os recursos, caso ocorra um failover. Cada SID do SAP pode fazer failover de forma independente uma da outra no cluster de alta disponibilidade de vários SIDs.  Se estiver usando o isolamento SBD, os dispositivos SBD poderão ser compartilhados entre vários clusters.  

Para obter alta disponibilidade, o SAP NetWeaver requer compartilhamentos NFS altamente disponíveis. Neste exemplo, vamos supor que os compartilhamentos NFS do SAP sejam hospedados no [servidor de arquivos NFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)altamente disponível, que pode ser usado por vários sistemas SAP. Ou os compartilhamentos são implantados nos [volumes NFS dos arquivos do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  

![Visão geral de alta disponibilidade do SAP NetWeaver](./media/high-availability-guide-suse/ha-suse-multi-sid.png)

> [!IMPORTANT]
> O suporte para clustering de vários SIDs do SAP ASCS/ERS com o SUSE Linux como sistema operacional convidado em VMs do Azure é limitado a **cinco** SIDs do SAP no mesmo cluster. Cada novo SID aumenta a complexidade. **Não há suporte para**uma combinação do servidor de replicação de enfileiramento do SAP 1 e do servidor de replicação de enfileiramento 2 no mesmo cluster. O clustering de vários SID descreve a instalação de várias instâncias do SAP ASCS/ERS com SIDs diferentes em um cluster pacemaker. Atualmente, o clustering de vários SIDs só tem suporte para ASCS/ERS.  

> [!TIP]
> O clustering de vários SIDs do SAP ASCS/ERS é uma solução com maior complexidade. É mais complexo implementar. Ele também envolve um esforço administrativo maior, ao executar atividades de manutenção (como aplicação de patch de so). Antes de iniciar a implementação real, Reserve um tempo para planejar cuidadosamente a implantação e todos os componentes envolvidos, como VMs, montagens NFS, VIPs, configurações do balanceador de carga e assim por diante.  

O servidor NFS, o SAP NetWeaver ASCS, o SAP NetWeaver SCS, o SAP NetWeaver ERS e o banco de dados SAP HANA usam o nome de host virtual e endereços IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. É recomendável usar o [balanceador de carga padrão](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal).  

A lista a seguir mostra a configuração do (a) SCS e ERS Load Balancer para este exemplo de cluster de vários SID com três sistemas SAP. Será necessário um IP de front-end separado, investigações de integridade e regras de balanceamento de carga para cada instância de ASCS e ERS para cada um dos SIDs. Atribua todas as VMs que fazem parte do cluster ASCS/ASCS a um pool de back-end.  

### <a name="ascs"></a>Um SCS

* Configuração de front-end
  * Endereço IP para NW1:10.3.1.14
  * Endereço IP para NW2:10.3.1.16
  * Endereço IP para NW3:10.3.1.13
* Configuração de back-end
  * Conectado às interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte do cluster (A) SCS/ERS
* Portas de investigação
  * Porta 620<strong>&lt;nr&gt;</strong>, portanto para as portas de investigação NW1, NW2 e NW3 620**00**, 620**10** e 620**20**
* Regras de balanceamento de carga- 
* Crie um para cada instância, ou seja, NW1/ASCS, NW2/ASCS e NW3/ASCS.
  * Se estiver usando Standard Load Balancer, selecione **portas de alta disponibilidade**
  * Se estiver usando Load Balancer básica, crie regras de balanceamento de carga para as seguintes portas
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configuração de front-end
  * Endereço IP para NW1 10.3.1.15
  * Endereço IP para NW1 10.3.1.17
  * Endereço IP para NW1 10.3.1.19
* Configuração de back-end
  * Conectado às interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte do cluster (A) SCS/ERS
* Porta de investigação
  * Porta 621<strong>&lt;nr&gt;</strong>, portanto para NW1, NW2 e N # portas de investigação 621**02**, 621**12** e 621**22**
* Regras de balanceamento de carga – crie uma para cada instância, ou seja, NW1/ERS, NW2/ERS e NW3/ERS.
  * Se estiver usando Standard Load Balancer, selecione **portas de alta disponibilidade**
  * Se estiver usando Load Balancer básica, crie regras de balanceamento de carga para as seguintes portas
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP


> [!Note]
> Quando as VMs sem endereços IP públicos forem colocadas no pool de back-end do Azure Load Balancer padrão (sem endereço IP público), não haverá nenhuma conectividade com a Internet de saída, a menos que a configuração adicional seja executada para permitir o roteamento para pontos de extremidade públicos. Para obter detalhes sobre como obter conectividade de saída, consulte [conectividade de ponto de extremidade pública para máquinas virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Não habilite carimbos de data/hora TCP em VMs do Azure colocadas por trás Azure Load Balancer. Habilitar carimbos de data/hora TCP fará com que as investigações de integridade falhem. Defina o parâmetro **net. IPv4. tcp_timestamps** como **0**. Para obter detalhes, consulte [Load Balancer investigações de integridade](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

## <a name="sap-nfs-shares"></a>Compartilhamentos NFS SAP

O SAP NetWeaver requer armazenamento compartilhado para o transporte, o diretório de perfil e assim por diante. Para o sistema SAP altamente disponível, é importante ter compartilhamentos NFS altamente disponíveis. Você precisará decidir sobre a arquitetura de seus compartilhamentos NFS do SAP. Uma opção é criar um [cluster NFS altamente disponível em VMs do Azure no SUSE Linux Enterprise Server][nfs-ha], que pode ser compartilhado entre vários sistemas SAP. 

Outra opção é implantar os compartilhamentos nos [volumes NFS dos arquivos do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  Com o Azure NetApp Files, você terá alta disponibilidade interna para os compartilhamentos NFS do SAP.

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Implantar o primeiro sistema SAP no cluster

Agora que você decidiu a arquitetura para os compartilhamentos NFS do SAP, implante o primeiro sistema SAP no cluster, seguindo a documentação correspondente.

* Se estiver usando o servidor NFS altamente disponível, siga [a alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).  
* Se estiver usando Azure NetApp Files volumes de NFS, siga [a alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server com Azure NetApp Files para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)

Os documentos listados acima orientarão você pelas etapas para preparar as infraestruturas necessárias, criar o cluster, preparar o sistema operacional para executar o aplicativo SAP.  

> [!TIP]
> Sempre teste a funcionalidade de failover do cluster, depois que o primeiro sistema for implantado, antes de adicionar os SIDs SAP adicionais ao cluster. Dessa forma, você saberá que a funcionalidade do cluster funciona, antes de adicionar a complexidade de sistemas SAP adicionais ao cluster.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Implantar sistemas SAP adicionais no cluster

Neste exemplo, supomos que o System **NW1** já foi implantado no cluster. Mostraremos como implantar no cluster sistemas SAP **NW2** e **NW3**. 

Os seguintes itens são prefixados com ambos **[A]** - aplicáveis a todos os nós, **[1]** – apenas aplicável no nó 1 ou **[2]** – apenas aplicável a nó 2.

### <a name="prerequisites"></a>Pré-requisitos 

> [!IMPORTANT]
> Antes de seguir as instruções para implantar sistemas SAP adicionais no cluster, siga as instruções para implantar o primeiro sistema SAP no cluster, pois há etapas que são necessárias apenas durante a primeira implantação do sistema.  

Esta documentação pressupõe que:
* O cluster pacemaker já está configurado e em execução.  
* Pelo menos um sistema SAP (instância de ASCS/ERS) já está implantado e está em execução no cluster.  
* A funcionalidade de failover de cluster foi testada.  
* Os compartilhamentos NFS para todos os sistemas SAP são implantados.  

### <a name="prepare-for-sap-netweaver-installation"></a>Preparar para a instalação do SAP NetWeaver

1. Adicione a configuração para o sistema implantado recentemente (ou seja, **NW2**, **NW3**) ao Azure Load Balancer existente, seguindo as instruções [implantar Azure Load Balancer manualmente por meio de portal do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files#deploy-azure-load-balancer-manually-via-azure-portal). Ajuste os endereços IP, as portas de investigação de integridade e as regras de balanceamento de carga para sua configuração.  

2. **[A]** configurar a resolução de nomes para os sistemas SAP adicionais. Você pode usar o servidor DNS ou modificar `/etc/hosts` em todos os nós. Este exemplo mostra como usar o arquivo de `/etc/hosts`.  Adapte os endereços IP e os nomes de host ao seu ambiente. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.16 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.13 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.17 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.19 msnw3ers
    # IP address for virtual host name for the NFS server for NW2
    10.3.1.31 nw2-nfs
    # IP address for virtual host name for the NFS server for NW3
    10.3.1.32 nw3-nfs
   ```

3. **[A]** crie os diretórios compartilhados para os sistemas SAP **NW2** e **NW3** adicionais que você está implantando no cluster. 

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

4. **[A]** configurar `autofs` para montar os sistemas de arquivos/Sapmnt/Sid e/usr/SAP/Sid/sys para os sistemas SAP adicionais que você está implantando no cluster. Neste exemplo, **NW2** e **NW3**.  

   Atualize o arquivo `/etc/auto.direct` com os sistemas de arquivos para os sistemas SAP adicionais que você está implantando no cluster.  

   * Se estiver usando o servidor de arquivos NFS, siga as instruções [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#prepare-for-sap-netweaver-installation)
   * Se estiver usando Azure NetApp Files, siga as instruções [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files#prepare-for-sap-netweaver-installation) 

   Será necessário reiniciar o serviço de `autofs` para montar os compartilhamentos recém-adicionados.  

### <a name="install-ascs--ers"></a>Instalar o ASCS/ERS

1. Crie os recursos de cluster de investigação de integridade e IP virtual para a instância ASCS do sistema SAP adicional que você está implantando no cluster. O exemplo mostrado aqui é para **NW2** e **NW3** ASCS, usando o servidor NFS altamente disponível.  

   > [!IMPORTANT]
   > Testes recentes revelaram situações em que o netcat para de responder às solicitações devido à pendência e sua limitação de manipular apenas uma conexão. O recurso netcat para de escutar as solicitações do Azure Load Balancer e o IP flutuante fica indisponível.  
   > 
   > Para clusters pacemaker existentes, é recomendável substituir netcat por socat, seguindo as instruções em [proteção de detecção do balanceador de carga do Azure](https://www.suse.com/support/kb/doc/?id=7024128). Observe que a alteração exigirá um breve tempo de inatividade.  

    ```
      sudo crm configure primitive fs_NW2_ASCS Filesystem device='nw2-nfs:/NW2/ASCS' directory='/usr/sap/NW2/ASCS10' fstype='nfs4' \
       op start timeout=60s interval=0 \
       op stop timeout=60s interval=0 \
       op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW2_ASCS IPaddr2 \
        params ip=10.3.1.16 cidr_netmask=24 \
        op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW2_ASCS anything \
        params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:62010,backlog=10,fork,reuseaddr /dev/null" \
        op monitor timeout=20s interval=10 depth=0
   
      sudo crm configure group g-NW2_ASCS fs_NW2_ASCS nc_NW2_ASCS vip_NW2_ASCS \
         meta resource-stickiness=3000

      sudo crm configure primitive fs_NW3_ASCS Filesystem device='nw3-nfs:/NW3/ASCS' directory='/usr/sap/NW3/ASCS20' fstype='nfs4' \
        op start timeout=60s interval=0 \
        op stop timeout=60s interval=0 \
        op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW3_ASCS IPaddr2 \
       params ip=10.3.1.13 cidr_netmask=24 \
       op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW3_ASCS anything \
        params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:62020,backlog=10,fork,reuseaddr /dev/null" \
        op monitor timeout=20s interval=10 depth=0
   
      sudo crm configure group g-NW3_ASCS fs_NW3_ASCS nc_NW3_ASCS vip_NW3_ASCS \
        meta resource-stickiness=3000
    ```

   Conforme você cria os recursos, eles podem ser atribuídos a diferentes recursos de cluster. Ao agrupá-los, eles serão migrados para um dos nós de cluster. Verifique se o status do cluster é OK e se todos os recursos foram iniciados. Não é importante em qual nó os recursos estão em execução.

2. **[1]** instalar o ASCS do SAP NetWeaver  

   Instale o ASCS do SAP NetWeaver como raiz, usando um nome de host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o ASCS. Por exemplo, para System **NW2**, o nome de host virtual é <b>msnw2ascs</b>, <b>10.3.1.16</b> e o número de instância que você usou para a investigação do balanceador de carga, por exemplo <b>10</b>. para System **NW3**, o nome de host virtual é <b>msnw3ascs</b>, <b>10.3.1.13</b> e o número de instância que você usou para a investigação do balanceador de carga, por exemplo, <b>20</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst. Você pode usar o parâmetro SAPINST_USE_HOSTNAME para instalar o SAP, usando o nome do host virtual.  

     ```
      sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
     ```

   Se a instalação falhar ao criar uma subpasta no/usr/SAP/**Sid**/ASCS**Instance #** , tente definir o proprietário como **Sid**ADM e o grupo como SAPs da instância de ASCS **#** e tente novamente.

3. **[1]** crie um IP virtual e recursos de cluster de investigação de integridade para a instância ers do sistema SAP adicional que você está implantando no cluster. O exemplo mostrado aqui é para **NW2** e **NW3** ers, usando o servidor NFS altamente disponível. 

   ```
    sudo crm configure primitive fs_NW2_ERS Filesystem device='nw2-nfs:/NW2/ASCSERS' directory='/usr/sap/NW2/ERS12' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW2_ERS IPaddr2 \
      params ip=10.3.1.17 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW2_ERS anything \
     params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:62112,backlog=10,fork,reuseaddr /dev/null" \
     op monitor timeout=20s interval=10 depth=0
   
    # WARNING: Resources nc_NW2_ASCS,nc_NW2_ERS violate uniqueness for parameter "binfile": "/usr/bin/socat"
    # Do you still want to commit (y/n)? y
   
    sudo crm configure group g-NW2_ERS fs_NW2_ERS nc_NW2_ERS vip_NW2_ERS

    sudo crm configure primitive fs_NW3_ERS Filesystem device='nw3-nfs:/NW3/ASCSERS' directory='/usr/sap/NW3/ERS22' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW3_ERS IPaddr2 \
      params ip=10.3.1.19 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW3_ERS anything \
     params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:62122,backlog=10,fork,reuseaddr /dev/null" \
     op monitor timeout=20s interval=10 depth=0
   
    # WARNING: Resources nc_NW3_ASCS,nc_NW3_ERS violate uniqueness for parameter "binfile": "/usr/bin/socat"
    # Do you still want to commit (y/n)? y
   
    sudo crm configure group g-NW3_ERS fs_NW3_ERS nc_NW3_ERS vip_NW3_ERS
   ```

   Conforme você cria os recursos, eles podem ser atribuídos a nós de cluster diferentes. Ao agrupá-los, eles serão migrados para um dos nós de cluster. Verifique se o status do cluster é OK e se todos os recursos foram iniciados.  

   Em seguida, verifique se os recursos do grupo ERS recém-criado estão em execução no nó do cluster, oposto ao nó do cluster em que a instância ASCS do mesmo sistema SAP foi instalada.  Por exemplo, se NW2 ASCS tiver sido instalado em `slesmsscl1`, verifique se o grupo NW2 ERS está em execução `slesmsscl2`.  Você pode migrar o grupo NW2 ERS para `slesmsscl2` executando o seguinte comando: 

    ```
      crm resource migrate g-NW2_ERS slesmsscl2 force
    ```

4. **[2]** instalar o ers do SAP NetWeaver

   Instale o ERS do SAP NetWeaver como raiz no outro nó, usando um nome de host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o ERS. Por exemplo, para System **NW2**, o nome de host virtual será <b>msnw2ers</b>, <b>10.3.1.17</b> e o número de instância que você usou para a investigação do balanceador de carga, por exemplo <b>12</b>. Para System **NW3**, o nome de host virtual <b>msnw3ers</b>, <b>10.3.1.19</b> e o número de instância que você usou para a investigação do balanceador de carga, por exemplo <b>22</b>. 

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst. Você pode usar o parâmetro SAPINST_USE_HOSTNAME para instalar o SAP, usando o nome do host virtual.  

    ```
     sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Use SWPM SP 20 PL 05 ou superior. As versões inferiores não definem as permissões corretamente e a instalação falhará.

   Se a instalação falhar ao criar uma subpasta no/usr/SAP/**NW2**/ers**Instance #** , tente definir o proprietário como **Sid**ADM e o grupo como SAPs da pasta ers **# #** e tente novamente.

   Se fosse necessário migrar o grupo ERS do sistema SAP implantado recentemente para um nó de cluster diferente, não se esqueça de remover a restrição de local do grupo ERS. Você pode remover a restrição executando o comando a seguir (o exemplo é fornecido para os sistemas SAP **NW2** e **NW3**).  

    ```
      crm resource unmigrate g-NW2_ERS
      crm resource unmigrate g-NW3_ERS
    ```

5. **[1]** adaptar os perfis de instância ASCS/SCS e ers para os sistemas SAP recentemente instalados. O exemplo mostrado abaixo é para NW2. Você precisará adaptar os perfis ASCS/SCS e ERS para todas as instâncias SAP adicionadas ao cluster.  
 
 * Perfil do ASCS/SCS

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

 * Perfil de ERS

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```

6. **[A]** configurar os usuários do SAP para o sistema SAP implantado recentemente, neste exemplo, **NW2** e **NW3**. 

   ```
   # Add sidadm to the haclient group
   sudo usermod -aG haclient nw2adm
   sudo usermod -aG haclient nw3adm
   ```

7. Adicione os serviços SAP ASCS e ERS para o sistema SAP recentemente instalado ao arquivo `sapservice`. O exemplo mostrado abaixo é para sistemas SAP **NW2** e **NW3**.  

   Adicione a entrada de serviço ASCS ao segundo nó e copie a entrada de serviço ERS para o primeiro nó. Execute os comandos para cada sistema SAP no nó em que a instância de ASCS para o sistema SAP foi instalada.  

    ```
     # Execute the following commands on slesmsscl1,assuming the NW2 ASCS instance was installed on slesmsscl1
     cat /usr/sap/sapservices | grep ASCS10 | sudo ssh slesmsscl2 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl2 "cat /usr/sap/sapservices" | grep ERS12 | sudo tee -a /usr/sap/sapservices
     # Execute the following commands on slesmsscl2, assuming the NW3 ASCS instance was installed on slesmsscl2
     cat /usr/sap/sapservices | grep ASCS20 | sudo ssh slesmsscl1 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl1 "cat /usr/sap/sapservices" | grep ERS22 | sudo tee -a /usr/sap/sapservices
    ```

8. **[1]** crie os recursos de cluster do SAP para o sistema SAP recentemente instalado. 

   Se estiver usando a arquitetura do enqueue Server 1 (ENSA1), defina os recursos para os sistemas SAP **NW2** e **NW3** da seguinte maneira:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure location loc_sap_NW2_failover_to_ers rsc_sap_NW2_ASCS10 rule 2000: runs_ers_NW2 eq 1
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure location loc_sap_NW3_failover_to_ers rsc_sap_NW3_ASCS10 rule 2000: runs_ers_NW3 eq 1
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   O SAP introduziu o suporte para o enqueue Server 2, incluindo a replicação, a partir do SAP NW 7,52. A partir do ABAP Platform 1809, o enqueue Server 2 é instalado por padrão. Consulte o SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) para suporte ao servidor de enfileiramento 2.
   Se estiver usando a arquitetura do enqueue Server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), defina os recursos para os sistemas SAP **NW2** e **NW3** da seguinte maneira:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true 
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   Se você estiver atualizando de uma versão mais antiga e alternando para o servidor de enfileiramento 2, consulte a observação do SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Verifique se o status do cluster é OK e se todos os recursos foram iniciados. Não é importante em qual nó os recursos estão em execução.
   O exemplo a seguir mostra o status de recursos de cluster, depois que os sistemas SAP **NW2** e **NW3** foram adicionados ao cluster. 

    ```
     sudo crm_mon -r
    
    # Online: [ slesmsscl1 slesmsscl2 ]
    
    #Full list of resources:
    
    #stonith-sbd     (stonith:external/sbd): Started slesmsscl1
    # Resource Group: g-NW1_ASCS
    #     fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
    #     vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW1_ERS
    #     fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl1
    #     vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ASCS
    #     fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
    #     vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ERS
    #     fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl2
    #     vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW3_ASCS
    #     fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
    #     vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW3_ERS
    #     fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl2
    #     vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    ```

   A imagem a seguir mostra como os recursos se pareceriam no Konsole da Web de alta disponibilidade (Hawk), com os recursos do sistema SAP **NW2** expandidos.  

   [Visão geral de alta disponibilidade do SAP NetWeaver ![](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk.png)](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk-detail.png#lightbox)

### <a name="proceed-with-the-sap-installation"></a>Continuar com a instalação do SAP 

Conclua a instalação do SAP:

* [Preparando seus servidores de aplicativos SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Instalando uma instância do DBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#install-database)
* [Instalando um servidor de aplicativos SAP primário](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#sap-netweaver-application-server-installation)
* Instalando uma ou mais instâncias adicionais do aplicativo SAP

## <a name="test-the-multi-sid-cluster-setup"></a>Testar a configuração de cluster de vários SIDs

Os testes a seguir são um subconjunto dos casos de teste nos guias de práticas recomendadas do SUSE. Eles estão incluídos para sua conveniência. Para obter a lista completa de testes de cluster, consulte a seguinte documentação:

* Se estiver usando o servidor NFS altamente disponível, siga [a alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).  
* Se estiver usando Azure NetApp Files volumes de NFS, siga [a alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server com Azure NetApp Files para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)

Sempre Leia os guias de práticas recomendadas do SUSE e execute todos os testes adicionais que podem ter sido adicionados.  
Os testes apresentados estão em um cluster de dois nós, vários SID com três sistemas SAP instalados.  

1. Testar HAGetFailoverConfig e HACheckFailoverConfig

   Execute os comandos a seguir como <sapsid>ADM no nó em que a instância ASCS está em execução no momento. Se os comandos falharem com falha: memória insuficiente, isso pode ser causado por traços em seu nome de host. Esse é um problema conhecido e será corrigido pelo SUSE no pacote SAP-SuSE-cluster-Connector.

   ```
    slesmsscl1:nw1adm 57> sapcontrol -nr 00 -function HAGetFailoverConfig

   # 10.12.2019 21:33:08
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw1adm 53> sapcontrol -nr 00 -function HACheckFailoverConfig

    # 19.12.2019 21:19:58
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl2:nw2adm 35> sapcontrol -nr 10 -function HAGetFailoverConfig

   # 10.12.2019 21:37:09
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl2
   # HANodes: slesmsscl2, slesmsscl1

    slesmsscl2:nw2adm 52> sapcontrol -nr 10 -function HACheckFailoverConfig

    # 19.12.2019 21:17:39
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl1:nw3adm 49> sapcontrol -nr 20 -function HAGetFailoverConfig

   # 10.12.2019 23:35:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw3adm 52> sapcontrol -nr 20 -function HACheckFailoverConfig

    # 19.12.2019 21:10:42
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   ```

2. Migre manualmente a instância de ASCS. O exemplo mostra a migração da instância de ASCS para o sistema SAP NW2.  
   Estado do recurso, antes de iniciar o teste:
   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Execute os comandos a seguir como raiz para migrar a instância do NW2 ASCS.

   ```
    crm resource migrate rsc_sap_NW2_ASCS10 force
    # INFO: Move constraint created for rsc_sap_NW2_ASCS10
    
    crm resource unmigrate rsc_sap_NW2_ASCS10
   # INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   
   # Remove failed actions for the ERS that occurred as part of the migration
    crm resource cleanup rsc_sap_NW2_ERS12
   ```

   Estado do recurso após o teste:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Testar HAFailoverToNode. O teste apresentado aqui mostra a migração da instância de ASCS para o sistema SAP NW2.  

   Estado do recurso antes de iniciar o teste:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Execute os comandos a seguir como **NW2**ADM para migrar a instância NW2 ASCS.

   ```
    slesmsscl2:nw2adm 53> sapcontrol -nr 10 -host msnw2ascs -user nw2adm password -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   crm resource cleanup rsc_sap_NW2_ERS12
   # Remove migration constraints
   crm resource clear rsc_sap_NW2_ASCS10
   #INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   ```

   Estado do recurso após o teste:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Simular falha de nó

   Estado do recurso antes de iniciar o teste:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Execute o seguinte comando como raiz no nó em que pelo menos uma instância de ASCS está em execução. Neste exemplo, executamos o comando em `slesmsscl2`, em que as instâncias de ASCS para NW1 e NW3 estão em execução.  

   ```
    slesmsscl2:~ # echo b > /proc/sysrq-trigger
   ```

   Se você usar SBD, pacemaker não deverá iniciar automaticamente no nó eliminado. O status após o nó ser iniciado novamente deve ter a seguinte aparência.

   ```
    Online: [ slesmsscl1 ]
    OFFLINE: [ slesmsscl2 ]
    Full list of resources:

    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    
    Failed Resource Actions:
    * rsc_sap_NW1_ERS02_monitor_11000 on slesmsscl1 'not running' (7): call=125, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW2_ERS12_monitor_11000 on slesmsscl1 'not running' (7): call=126, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW3_ERS22_monitor_11000 on slesmsscl1 'not running' (7): call=127, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
   ```

   Use os comandos a seguir para iniciar o pacemaker no nó eliminado, limpar as mensagens SBD e limpar os recursos com falha.

   ```# run as root
   # list the SBD device(s)
   slesmsscl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   slesmsscl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message slesmsscl2 clear
   
   slesmsscl2:~ # systemctl start pacemaker
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW1_ERS02
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW2_ERS12
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW3_ERS22
   ```

   Estado do recurso após o teste:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
   ```

## <a name="next-steps"></a>Passos seguintes

* [Planejamento e implementação de máquinas virtuais do Azure para SAP][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA no Azure (instâncias grandes), consulte [alta disponibilidade e recuperação de desastre do SAP Hana (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA em VMs do Azure, consulte [alta disponibilidade de SAP Hana em VMS (máquinas virtuais) do Azure][sap-hana-ha]
