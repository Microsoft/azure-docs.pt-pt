---
title: Azure máquinas virtuais elevada disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux | Documentos da Microsoft
description: Azure máquinas virtuais elevada disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: 931727eff0de104ea57930abb1d3739fa086967a
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226662"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Azure máquinas virtuais elevada disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

Este artigo descreve como implementar as máquinas virtuais, configurar as máquinas virtuais, instalar o framework de cluster e instalar um sistema SAP NetWeaver 7.50 elevada disponibilidade.
As configurações de exemplo, comandos de instalação etc. Número de instância do ASCS 00, 02 de número de instâncias de ERS e NW1 de ID de sistema SAP é utilizado. Os nomes dos recursos (por exemplo, as máquinas virtuais, redes virtuais) no exemplo partem do princípio de que utiliza a [modelo ASCS/SCS] [ template-multisid-xscs] com recursos do prefixo NW1 para criar os recursos.

Leia as seguintes notas de SAP e documentos pela primeira vez

* A nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure que são suportados para a implementação de SAP software
  * Informações de capacidade importante para tamanhos de VM do Azure
  * Software SAP suportado e o sistema operativo (SO) e combinações de base de dados
  * Versão de kernel do SAP necessária para Windows e Linux no Microsoft Azure

* A nota SAP [2015553] apresenta uma lista de pré-requisitos para implementações de software SAP suportadas para SAP no Azure.
* A nota SAP [2002167] recomendado configurações do sistema operacional para Red Hat Enterprise Linux
* A nota SAP [2009879] tem diretrizes do SAP HANA para Red Hat Enterprise Linux
* A nota SAP [2178632] tem informações detalhadas sobre todas as monitorizações métricas comunicadas para o SAP no Azure.
* A nota SAP [2191498] tem a versão necessária do agente de anfitrião do SAP para o Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento de SAP no Linux no Azure.
* A nota SAP [1999351] tem informações adicionais de resolução de problemas avançada de monitorização a extensão do Azure para SAP.
* [WIKI de Comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as necessárias SAP Notes para o Linux.
* [Máquinas de virtuais de planeamento e implementação para o SAP no Linux do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP no Linux][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [Documentação do produto para Red Hat Gluster Storage](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [SAP Netweaver no cluster de pacemaker](https://access.redhat.com/articles/3150081)
* Documentação geral RHEL
  * [Descrição geral do suplemento de elevada disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração de complemento de elevada disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de complemento de elevada disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configurar ASCS/ERS para SAP Netweaver com recursos autônomos no RHEL 7.5](https://access.redhat.com/articles/3569681)
* Documentação do RHEL específica do Azure:
  * [Políticas de suporte para Clusters de elevada disponibilidade do RHEL - máquinas de virtuais do Microsoft Azure como membros do Cluster](https://access.redhat.com/articles/3131341)
  * [Instalar e configurar um Cluster de elevada disponibilidade Linux 7.4 (e posterior) de Enterprise do Red Hat no Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Descrição geral

Para assegurar elevada disponibilidade, o SAP NetWeaver requer armazenamento partilhado. GlusterFS está configurado num cluster separado e pode ser utilizado por vários sistemas SAP.

![Descrição geral do SAP NetWeaver elevada disponibilidade](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e a base de dados do SAP HANA, utilizam o nome de anfitrião virtual e endereços IP virtuais. No Azure, um balanceador de carga é necessário utilizar um endereço IP virtual. A lista seguinte mostra a configuração do (A) SCS e ERS Balanceador de carga.

### <a name="ascs"></a>(A)SCS

* Configuração de front-end
  * Endereço IP 10.0.0.7
* Configuração de back-end
  * Ligado a interfaces de rede primário de todas as máquinas virtuais que devem fazer parte do (A) cluster SCS/ERS
* Porta de sonda
  * Port 620<strong>&lt;nr&gt;</strong>
* Regras de balanceamento de carga
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configuração de front-end
  * Endereço IP 10.0.0.8
* Configuração de back-end
  * Ligado a interfaces de rede primário de todas as máquinas virtuais que devem fazer parte do (A) cluster SCS/ERS
* Porta de sonda
  * Port 621<strong>&lt;nr&gt;</strong>
* Regras de balanceamento de carga
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-glusterfs"></a>Como configurar GlusterFS

SAP NetWeaver requer armazenamento partilhado para o diretório de transporte e perfil. Leia [GlusterFS em VMs do Azure no Red Hat Enterprise Linux para SAP NetWeaver] [ glusterfs-ha] sobre como configurar a GlusterFS para SAP NetWeaver.

## <a name="setting-up-ascs"></a>Configurar a (A) SCS

Pode utilizar um modelo do Azure a partir do github para implementar todos os recursos do Azure necessários, incluindo as máquinas virtuais, conjunto de disponibilidade e o Balanceador de carga ou pode implementar os recursos manualmente.

### <a name="deploy-linux-via-azure-template"></a>Implementar o Linux através do modelo do Azure

O Azure Marketplace contém uma imagem do Red Hat Enterprise Linux que pode utilizar para implementar novas máquinas virtuais. Pode utilizar um dos modelos de início rápido no github para implementar todos os recursos necessários. O modelo implementa as máquinas virtuais, o Balanceador de carga, disponibilidade definida etc. Siga estes passos para implementar o modelo:

1. Abra o [modelo ASCS/SCS] [ template-multisid-xscs] no portal do Azure  
1. Introduza os seguintes parâmetros
   1. Prefixo de recursos  
      Introduza o prefixo que pretende utilizar. O valor é utilizado como um prefixo para os recursos que são implementados.
   1. Tipo de pilha  
      Selecione o tipo de pilha do SAP NetWeaver
   1. Tipo de SO  
      Selecione uma das distribuições de Linux. Para este exemplo, selecione RHEL 7
   1. Tipo de BD  
      Selecione o HANA
   1. Contagem de sistema do SAP  
      O número de sistema SAP que executar neste cluster. Selecione 1.
   1. Disponibilidade do sistema  
      Selecione HA
   1. Chave de nome de utilizador administrador, a palavra-passe de administrador ou SSH  
      Um novo utilizador é criado que pode ser utilizado para iniciar sessão máquina.
   1. ID de sub-rede  
   Se pretender implementar a VM para uma VNet já existente em que tem uma sub-rede definida a VM deve ser atribuída para nomear o ID dessa sub-rede. Normalmente, é o ID de /subscriptions/**&lt;ID da subscrição&gt;**/resourceGroups/**&lt;nome do grupo de recursos&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;nome da rede virtual&gt;**/subnets/**&lt;nome da sub-rede&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Implementar manualmente o Linux através do portal do Azure

Tem primeiro de criar as máquinas virtuais para este cluster. Em seguida, pode criar um balanceador de carga e utilize as máquinas virtuais nos agrupamentos de back-end.

1. Criar um Grupo de Recursos
1. Criar uma rede Virtual
1. Criar um conjunto de disponibilidade  
   Domínio de atualização máx. de conjunto
1. Criar Máquina Virtual 1  
   Utilize, pelo menos, 7 do RHEL, nesta imagem de exemplo do Red Hat Enterprise Linux 7.4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Selecione o conjunto de disponibilidade que criou anteriormente  
1. Criar a Máquina Virtual 2  
   Utilize, pelo menos, 7 do RHEL, nesta imagem de exemplo do Red Hat Enterprise Linux 7.4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Selecione o conjunto de disponibilidade que criou anteriormente  
1. Adicionar pelo menos um disco de dados para ambas as máquinas virtuais  
   Os discos de dados são utilizados para/usr/sap/`<SAPSID`> diretório
1. Criar um balanceador de carga (interno)  
   1. Criar os endereços IP de front-end
      1. Endereço IP 10.0.0.7 para o ASCS
         1. Abra o Balanceador de carga, selecione o conjunto IP de front-end e clique em Adicionar
         1. Introduza o nome do novo conjunto IP de front-end (por exemplo **nw1-ascs-front-end**)
         1. Definir a atribuição para estático e introduza o endereço IP (por exemplo **10.0.0.7**)
         1. Clique em OK
      1. Endereço IP 10.0.0.8 para o ERS ASCS
         * Repita os passos acima para criar um endereço IP para o ERS (por exemplo **10.0.0.8** e **nw1-aers-back-end**)
   1. Criar os conjuntos de back-end
      1. Criar um conjunto de back-end para o ASCS
         1. Abra o Balanceador de carga, selecione conjuntos de back-end e clique em Adicionar
         1. Introduza o nome do novo conjunto de back-end (por exemplo **nw1-ascs-back-end**)
         1. Clique em Adicionar uma máquina virtual.
         1. Selecione o conjunto de disponibilidade que criou anteriormente
         1. Selecione as máquinas virtuais de (A) SCS cluster
         1. Clique em OK
      1. Criar um conjunto de back-end para o ERS ASCS
         * Repita os passos acima para criar um conjunto de back-end para o ERS (por exemplo **nw1-aers-back-end**)
   1. Criar as sondas de estado de funcionamento
      1. Porta 620**00** para ASCS
         1. Abra o Balanceador de carga, selecione sondas de estado de funcionamento e clique em Adicionar
         1. Introduza o nome da sonda de estado de funcionamento novo (por exemplo **nw1-ascs-hp**)
         1. Selecione TCP como protocolo, porta 620**00**, manter intervalo de 5 e 2 de limiar com funcionamento incorreto
         1. Clique em OK
      1. Porta 621**02** para ASCS ERS
         * Repita os passos acima para criar uma sonda de estado de funcionamento para o ERS (por exemplo 621**02** e **nw1-aers-hp**)
   1. Regras de balanceamento de carga
      1. 32**00** TCP para ASCS
         1. Abra o Balanceador de carga, selecione as regras de balanceamento de carga e clique em Adicionar
         1. Introduza o nome da nova regra de Balanceador de carga (por exemplo **nw1-lb-3200**)
         1. Selecione o endereço IP de front-end, o conjunto de back-end e a sonda de estado de funcionamento que criou anteriormente (por exemplo **nw1-ascs-front-end**)
         1. Protocolo de manter **TCP**, introduza a porta **3200**
         1. Aumentar o tempo limite de inatividade para 30 minutos
         1. **Certifique-se de ativar o IP flutuante**
         1. Clique em OK
      1. Portas adicionais para o ASCS
         * Repita os passos acima para portas 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 e TCP para o ASCS
      1. Portas adicionais para o ERS ASCS
         * Repita os passos acima para portas 33**02**, 5**02**13, 5**02**14, 5**02**16 e TCP para o ERS ASCS

> [!IMPORTANT]
> Não ative carimbos de data / TCP em VMs do Azure colocadas atrás do Balanceador de carga do Azure. Ativar TCP carimbos fará com que as sondas de estado de funcionamento efetuar a ativação. Defina o parâmetro **net.ipv4.tcp_timestamps** ao **0**. Para obter detalhes, consulte [sondas de estado de funcionamento do Balanceador de carga](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Criar Pacemaker cluster

Siga os passos em [Pacemaker no Red Hat Enterprise Linux no Azure a configurar](high-availability-guide-rhel-pacemaker.md) para criar um basic Pacemaker cluster para este servidor (A) SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Preparar para a instalação do SAP NetWeaver

Os seguintes itens são prefixados com ambos **[A]** - aplicáveis a todos os nós, **[1]** – apenas aplicável no nó 1 ou **[2]** – apenas aplicável a nó 2.

1. **[A]**  Configurar a resolução de nomes de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e o nome de anfitrião para corresponder ao seu ambiente

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.11 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.12 nw1-aers</b>
   </code></pre>

1. **[A]**  Criar os diretórios partilhados

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]**  GlusterFS instalar o cliente e outros requisitos

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]**  Verificação de versão dos agentes-resource-sap

   Certifique-se de que a versão do pacote de recursos-agentes-sap instalado é, pelo menos, 3.9.5-124.el7
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]**  Adicionar entradas de montagem

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Montar as partilhas de novo

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]**  Configurar TROCAR ficheiros

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reinicie o agente para ativar a alteração

   <pre><code>sudo service waagent restart
   </code></pre>

1. **[A]**  Configuração RHEL

   Configurar RHEL, conforme descrito na nota SAP [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalar o SAP NetWeaver ASCS/ERS

1. **[1]**  Criar um recurso IP virtual e uma sonda de estado de funcionamento para a instância do ASCS

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.11</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante no nó que os recursos estão em execução.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** Install SAP NetWeaver ASCS  

   Instalar o SAP NetWeaver ASCS como raiz no primeiro nó com um nome de anfitrião virtual que mapeia para o endereço IP da configuração de front-end de Balanceador de carga para ASCS, por exemplo <b>nw1 ascs</b>, <b>10.0.0.11</b> e o número que utilizou para a sonda do Balanceador de carga, por exemplo de instâncias <b>00</b>.

   Pode utilizar o parâmetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não raiz ligar ao sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Se a instalação falha criar uma subpasta no /usr/sap/**NW1**/ASCS**00**, tente definir o proprietário e o grupo do ASCS**00** pasta e tente novamente.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]**  Criar um recurso IP virtual e uma sonda de estado de funcionamento para a instância ERS

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.12</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante no nó que os recursos estão em execução.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]**  Instalar ERS do SAP NetWeaver  

   Instalar o SAP NetWeaver ERS como raiz no segundo nó com um nome de anfitrião virtual que mapeia para o endereço IP da configuração de front-end de Balanceador de carga para ERS, por exemplo <b>nw1 aers</b>, <b>10.0.0.12</b> e o número que utilizou para a sonda do Balanceador de carga, por exemplo de instâncias <b>02</b>.

   Pode utilizar o parâmetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não raiz ligar ao sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Se a instalação falha criar uma subpasta no /usr/sap/**NW1**/ERS**02**, tente definir o proprietário e o grupo do ERS**02** pasta e tente novamente.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]**  Adapt ASCS/SCS e ERS perfis de instância

   * Perfil do ASCS/SCS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Perfil ERS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]**  Configurar Keep Alive de

   A comunicação entre o servidor de aplicações SAP NetWeaver e ASCS/SCS é encaminhada através de um balanceador de carga de software. O Balanceador de carga desliga ligações inativas após um tempo limite configurável. Para impedir que isto terá de definir um parâmetro no perfil do SAP NetWeaver ASCS/SCS e alterar as definições do sistema Linux. Leia [1410736 de nota SAP] [ 1410736] para obter mais informações.

   O ASCS/SCS perfil parâmetro colocar/encni/set_so_keepalive já foi adicionado no último passo.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]**  Atualizar o ficheiro de /usr/sap/sapservices

   Para impedir o início das instâncias, o script de inicialização sapinit, todas as instâncias geridas pelo Pacemaker devem ser comentadas do ficheiro de /usr/sap/sapservices. Não comente a instância do SAP HANA se for utilizado com o HANA sênior

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]**  Criam os recursos de cluster SAP

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante no nó que os recursos estão em execução.

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]**  Adicionar regras de firewall para ASCS e ERS em ambos os nós

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparação do SAP NetWeaver application server

Algumas bases de dados requerem que a instalação de instância de base de dados é executada num servidor de aplicações. Prepare as máquinas de virtuais de servidor de aplicativo para poder utilizá-las nestes casos.

O passos abaixo partem do princípio de que instalou o servidor de aplicações num servidor diferente do que os servidores do ASCS/SCS e HANA. Caso contrário, alguns dos passos abaixo (como configurar a resolução de nomes de anfitrião) não são necessários.

1. Configurar a resolução de nomes de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e o nome de anfitrião para corresponder ao seu ambiente

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.11 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.12 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.8 nw1-di-0</b>
   <b>10.0.0.7 nw1-di-1</b>
   </code></pre>

1. Criar o diretório de /sapmnt

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Instalar o cliente de GlusterFS e outros requisitos

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Adicionar entradas de montagem

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Montar as partilhas de novo

   <pre><code>sudo mount -a
   </code></pre>

1. Configurar o ficheiro de troca
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reinicie o agente para ativar a alteração

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalar base de dados

Neste exemplo, SAP NetWeaver está instalado no SAP HANA. Pode usar cada base de dados suportado para esta instalação. Para obter mais informações sobre como instalar o SAP HANA no Azure, consulte [elevada disponibilidade do SAP HANA em VMs do Azure no Red Hat Enterprise Linux][sap-hana-ha]. Para obter uma lista de bases de dados suportados, consulte [SAP 1928533 de observação][1928533].

1. Executar a instalação de instância de base de dados do SAP

   Instalar a instância de base de dados do SAP NetWeaver como raiz com um nome de anfitrião virtual que mapeia para o endereço IP da configuração de front-end de Balanceador de carga para a base de dados, por exemplo <b>nw1-db</b> e <b>10.0.0.13</b>.

   Pode utilizar o parâmetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não raiz ligar ao sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalação de servidor de aplicativos do SAP NetWeaver

Siga estes passos para instalar um servidor de aplicações SAP.

1. Preparar o servidor de aplicações

   Siga os passos no capítulo [preparação de servidor de aplicação SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) acima para preparar o servidor de aplicações.

1. Instalar o servidor de aplicações SAP NetWeaver

   Instale um servidor de aplicações SAP NetWeaver primário ou adicional.

   Pode utilizar o parâmetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não raiz ligar ao sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Atualizar o arquivo seguro do SAP HANA

   Atualize o arquivo seguro do SAP HANA para apontar para o nome virtual da configuração do SAP HANA System Replication.

   Execute o seguinte comando para listar as entradas como \<sapsid > adm

   <pre><code>hdbuserstore List
   </code></pre>

   Isso deve listar todas as entradas e deve ter um aspeto semelhante a
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   O resultado mostra que o endereço IP da entrada predefinida está a apontar para a máquina virtual e não para o endereço IP do Balanceador de carga. Esta entrada tem de ser alterado para apontar para o nome de anfitrião virtual do Balanceador de carga. Lembre-se de que utilizam a mesma porta (**30313** no resultado acima) e o nome de base de dados (**HN1** no resultado acima)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Teste a configuração de cluster

1. Migrar manualmente a instância do ASCS

   Estado do recurso antes de iniciar o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Execute os seguintes comandos como raiz para migrar a instância do ASCS.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Simular falhas de nó

   Estado do recurso antes de iniciar o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Execute o seguinte comando como raiz no nó onde a instância do ASCS está em execução

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   O estado após o nó é iniciado novamente deve ter um aspeto semelhante a esta.

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   Utilize o seguinte comando para limpar os recursos com falha.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Interrompe o processo de servidor de mensagens

   Estado do recurso antes de iniciar o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Execute os seguintes comandos como raiz para identificar o processo do servidor de mensagens e vai encerrá-lo.

   <pre><code>[root@nw1-cl-0 ~]# pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Se apenas eliminar o servidor de mensagens uma vez, ele será reiniciado pelo sapstart. Se vai encerrá-lo com frequência que suficiente, Pacemaker acabará mova a instância do ASCS no outro nó. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância do ASCS e ERS após o teste.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Finalizar processo do servidor de colocar em fila

   Estado do recurso antes de iniciar o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Execute os seguintes comandos como raiz no nó onde a instância do ASCS está em execução para eliminar o servidor de colocar em fila.

   <pre><code>[root@nw1-cl-1 ~]# pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   A instância do ASCS deve imediatamente a ativação pós-falha no outro nó. A instância ERS também deve efetuar a ativação pós-falha depois da instância do ASCS é iniciada. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância do ASCS e ERS após o teste.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Interrompe o processo de servidor de replicação de colocar em fila

   Estado do recurso antes de iniciar o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Execute o seguinte comando como raiz no nó onde a instância ERS está em execução para interromper o processo de servidor de replicação de colocar em fila.

   <pre><code>[root@nw1-cl-1 ~]# pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Se apenas executar o comando uma vez, sapstart reiniciará o processo. Se executá-lo com frequência suficiente, irá sapstart não reiniciar o processo e o recurso vai estar no estado parado. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância ERS após o teste.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Finalizar processo sapstartsrv de colocar em fila

   Estado do recurso antes de iniciar o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Execute os seguintes comandos como raiz no nó onde o ASCS está em execução.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   O processo de sapstartsrv sempre deve ser reiniciado pelo agente de recurso Pacemaker como parte da monitorização. Estado do recurso depois do teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>Passos Seguintes

* [Máquinas de virtuais de planeamento e implementação de SAP do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a elevada disponibilidade e o plano de recuperação após desastre do SAP HANA no Azure (instâncias grandes), veja [SAP HANA (instâncias grandes) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer a elevada disponibilidade e o plano de recuperação após desastre do SAP HANA em VMs do Azure, veja [disponibilidade elevada do SAP HANA em máquinas virtuais do Azure (VMs)][sap-hana-ha]
