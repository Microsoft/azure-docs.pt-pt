---
title: GlusterFS em VMs do Azure no Red Hat Enterprise Linux para SAP NetWeaver | Microsoft Docs
description: GlusterFS nas VMs do Azure no Red Hat Enterprise Linux para o SAP NetWeaver
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 2ae9a1419232cca051f7cab4e9bd8c70f885df73
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749043"
---
# <a name="glusterfs-on-azure-vms-on-red-hat-enterprise-linux-for-sap-netweaver"></a>GlusterFS nas VMs do Azure no Red Hat Enterprise Linux para o SAP NetWeaver

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais e instalar um cluster GlusterFS que pode ser usado para armazenar os dados compartilhados de um sistema SAP altamente disponível.
Este guia descreve como configurar o GlusterFS que é usado por dois sistemas SAP, NW1 e NW2. Os nomes dos recursos (por exemplo, máquinas virtuais, redes virtuais) no exemplo pressupõem que você usou o [modelo de servidor de arquivos SAP][template-file-server] com o prefixo de recurso **glust**.

Leia as seguintes notas e documentos SAP primeiro

* Nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure com suporte para a implantação de software SAP
  * Informações de capacidade importantes para tamanhos de VM do Azure
  * Software SAP com suporte e combinações de so (sistema operacional) e banco de dados
  * Versão de kernel do SAP necessária para Windows e Linux em Microsoft Azure

* O SAP Note [2015553] lista os pré-requisitos para implantações de software SAP com suporte no SAP no Azure.
* A observação do SAP [2002167] tem as configurações do sistema operacional recomendadas para Red Hat Enterprise Linux
* A observação do SAP [2009879] tem diretrizes SAP HANA para Red Hat Enterprise Linux
* A nota SAP [2178632] tem informações detalhadas sobre todas as métricas de monitoramento relatadas para SAP no Azure.
* A nota SAP [2191498] tem a versão do agente de host do SAP necessária para Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento SAP no Linux no Azure.
* A nota SAP [1999351] tem informações adicionais para solução de problemas para a extensão de monitoramento avançado do Azure para SAP.
* O [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [Planejamento e implementação de máquinas virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP no Linux (este artigo)][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [Documentação do produto para o armazenamento Red Hat Gluster](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* Documentação geral do RHEL
  * [Visão geral do complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Documentação do RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade RHEL-Máquinas Virtuais do Microsoft Azure como membros do cluster](https://access.redhat.com/articles/3131341)
  * [Instalando e configurando um cluster de alta disponibilidade Red Hat Enterprise Linux 7,4 (e posterior) no Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Descrição geral

Para obter alta disponibilidade, o SAP NetWeaver requer armazenamento compartilhado. O GlusterFS é configurado em um cluster separado e pode ser usado por vários sistemas SAP.

![Visão geral de alta disponibilidade do SAP NetWeaver](./media/high-availability-guide-rhel-glusterfs/rhel-glusterfs.png)

## <a name="set-up-glusterfs"></a>Configurar o GlusterFS

Você pode usar um modelo do Azure do GitHub para implantar todos os recursos do Azure necessários, incluindo as máquinas virtuais, o conjunto de disponibilidade e as interfaces de rede, ou você pode implantar os recursos manualmente.

### <a name="deploy-linux-via-azure-template"></a>Implantar o Linux por meio do modelo do Azure

O Azure Marketplace contém uma imagem para Red Hat Enterprise Linux que você pode usar para implantar novas máquinas virtuais.
Você pode usar um dos modelos de início rápido no GitHub para implantar todos os recursos necessários. O modelo implanta as máquinas virtuais, o conjunto de disponibilidade, etc. Siga estas etapas para implantar o modelo:

1. Abra o [modelo de servidor de arquivos SAP][template-file-server] no portal do Azure
1. Insira os seguintes parâmetros
   1. Prefixo de recurso  
      Insira o prefixo que você deseja usar. O valor é usado como um prefixo para os recursos que são implantados.
   2. Contagem de sistema SAP Insira o número de sistemas SAP que usarão esse servidor de arquivos. Isso implantará o número necessário de discos, etc.
   3. Tipo de so  
      Selecione uma das distribuições do Linux. Para este exemplo, selecione RHEL 7
   4. Nome de usuário de administrador, senha de administrador ou chave SSH  
      Um novo usuário é criado e pode ser usado para fazer logon no computador.
   5. ID da sub-rede  
      Se você deseja implantar a VM em uma VNet existente em que você tem uma sub-rede definida, a VM deve ser atribuída, nomear a ID dessa sub-rede específica. A ID geralmente é semelhante a/subscriptions/ **&lt;ID da assinatura&gt;** /resourceGroups/ **&lt;nome do grupo de recursos&gt;** /Providers/Microsoft.Network/virtualNetworks/ **&lt;nome da rede virtual&gt;** /subnets/ **&lt;nome da sub-rede&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Implantar o Linux manualmente por meio de portal do Azure

Primeiro, você precisa criar as máquinas virtuais para esse cluster. Posteriormente, você criará um balanceador de carga e usará as máquinas virtuais nos pools de back-end. Recomendamos o [balanceador de carga padrão](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).  

1. Criar um Grupo de Recursos
1. Criar uma rede virtual
1. Criar um conjunto de disponibilidade  
   Definir domínio de atualização máx.
1. Criar máquina virtual 1  
   Use pelo menos RHEL 7, neste exemplo, a imagem Red Hat Enterprise Linux 7,4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Selecionar conjunto de disponibilidade criado anteriormente  
1. Criar máquina virtual 2  
   Use pelo menos RHEL 7, neste exemplo, a imagem Red Hat Enterprise Linux 7,4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Selecionar conjunto de disponibilidade criado anteriormente  
1. Adicione um disco de dados para cada sistema SAP a ambas as máquinas virtuais.

### <a name="configure-glusterfs"></a>Configurar o GlusterFS

Os itens a seguir são prefixados com **[A]** -aplicável a todos os nós **[1]** -aplicável somente ao nó 1, **[2]** – aplicável somente ao nó 2, **[3]** – aplicável somente ao nó 3.

1. **[A]**  Configurar a resolução de nomes de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e o nome de anfitrião para corresponder ao seu ambiente

   <pre><code># IP addresses of the Gluster nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   </code></pre>

1. **[A]** registrar

   Registrar suas máquinas virtuais e anexá-la a um pool que contém repositórios para RHEL 7 e GlusterFS

   <pre><code>sudo subscription-manager register
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

1. **[A]** habilitar GlusterFS repositórios

   Para instalar os pacotes necessários, habilite os seguintes repositórios.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rh-gluster-3-for-rhel-7-server-rpms
   </code></pre>
  
1. **[A]** instalar pacotes do GlusterFS

   Instalar esses pacotes em todos os nós do GlusterFS

   <pre><code>sudo yum -y install redhat-storage-server
   </code></pre>

   Reinicialize os nós após a instalação.

1. **[A]** modificar firewall

   Adicione regras de firewall para permitir o tráfego de cliente para os nós GlusterFS.

   <pre><code># list the available zones
   firewall-cmd --get-active-zones
   
   sudo firewall-cmd --zone=public --add-service=glusterfs --permanent
   sudo firewall-cmd --zone=public --add-service=glusterfs
   </code></pre>

1. **[A]** habilitar e iniciar o serviço GlusterFS

   Inicie o serviço GlusterFS em todos os nós.

   <pre><code>sudo systemctl start glusterd
   sudo systemctl enable glusterd
   </code></pre>

1. **[1]** criar GluserFS

   Execute os comandos a seguir para criar o cluster GlusterFS

   <pre><code>sudo gluster peer probe glust-1
   sudo gluster peer probe glust-2
   
   # Check gluster peer status
   sudo gluster peer status
   
   # Number of Peers: 2
   # 
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Accepted peer request (Connected)
   # 
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Accepted peer request (Connected)
   </code></pre>

1. **[2]** status do par de teste

   Testar o status do par no segundo nó

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[3]** status do par de teste

   Testar o status do par no terceiro nó

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[A]** criar LVM

   Neste exemplo, o GlusterFS é usado para dois sistemas SAP, NW1 e NW2. Use os comandos a seguir para criar configurações LVM para esses sistemas SAP.

   Use estes comandos para NW1

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun0
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW1</b> /dev/disk/azure/scsi1/lun0
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW1</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW1</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW1</b>/aers
   sudo lvscan
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/aers
   
   sudo mkdir -p /rhs/<b>NW1</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW1</b>/trans
   sudo mkdir -p /rhs/<b>NW1</b>/sys
   sudo mkdir -p /rhs/<b>NW1</b>/ascs
   sudo mkdir -p /rhs/<b>NW1</b>/aers
   
   sudo chattr +i /rhs/<b>NW1</b>/sapmnt
   sudo chattr +i /rhs/<b>NW1</b>/trans
   sudo chattr +i /rhs/<b>NW1</b>/sys
   sudo chattr +i /rhs/<b>NW1</b>/ascs
   sudo chattr +i /rhs/<b>NW1</b>/aers

   echo -e "/dev/rhgs-<b>NW1</b>/sapmnt\t/rhs/<b>NW1</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/trans\t/rhs/<b>NW1</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/sys\t/rhs/<b>NW1</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/ascs\t/rhs/<b>NW1</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/aers\t/rhs/<b>NW1</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

   Use estes comandos para NW2

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun1
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW2</b> /dev/disk/azure/scsi1/lun1
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW2</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW2</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW2</b>/aers
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/aers
   
   sudo mkdir -p /rhs/<b>NW2</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW2</b>/trans
   sudo mkdir -p /rhs/<b>NW2</b>/sys
   sudo mkdir -p /rhs/<b>NW2</b>/ascs
   sudo mkdir -p /rhs/<b>NW2</b>/aers
   
   sudo chattr +i /rhs/<b>NW2</b>/sapmnt
   sudo chattr +i /rhs/<b>NW2</b>/trans
   sudo chattr +i /rhs/<b>NW2</b>/sys
   sudo chattr +i /rhs/<b>NW2</b>/ascs
   sudo chattr +i /rhs/<b>NW2</b>/aers
   sudo lvscan
   
   echo -e "/dev/rhgs-<b>NW2</b>/sapmnt\t/rhs/<b>NW2</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/trans\t/rhs/<b>NW2</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/sys\t/rhs/<b>NW2</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/ascs\t/rhs/<b>NW2</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/aers\t/rhs/<b>NW2</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

1. **[1]** criar o volume distribuído

   Use os comandos a seguir para criar o volume GlusterFS para NW1 e iniciá-lo.

   <pre><code>sudo gluster vol create <b>NW1</b>-sapmnt replica 3 glust-0:/rhs/<b>NW1</b>/sapmnt glust-1:/rhs/<b>NW1</b>/sapmnt glust-2:/rhs/<b>NW1</b>/sapmnt force
   sudo gluster vol create <b>NW1</b>-trans replica 3 glust-0:/rhs/<b>NW1</b>/trans glust-1:/rhs/<b>NW1</b>/trans glust-2:/rhs/<b>NW1</b>/trans force
   sudo gluster vol create <b>NW1</b>-sys replica 3 glust-0:/rhs/<b>NW1</b>/sys glust-1:/rhs/<b>NW1</b>/sys glust-2:/rhs/<b>NW1</b>/sys force
   sudo gluster vol create <b>NW1</b>-ascs replica 3 glust-0:/rhs/<b>NW1</b>/ascs glust-1:/rhs/<b>NW1</b>/ascs glust-2:/rhs/<b>NW1</b>/ascs force
   sudo gluster vol create <b>NW1</b>-aers replica 3 glust-0:/rhs/<b>NW1</b>/aers glust-1:/rhs/<b>NW1</b>/aers glust-2:/rhs/<b>NW1</b>/aers force
   
   sudo gluster volume start <b>NW1</b>-sapmnt
   sudo gluster volume start <b>NW1</b>-trans
   sudo gluster volume start <b>NW1</b>-sys
   sudo gluster volume start <b>NW1</b>-ascs
   sudo gluster volume start <b>NW1</b>-aers
   </code></pre>

   Use os comandos a seguir para criar o volume GlusterFS para NW2 e iniciá-lo.

   <pre><code>sudo gluster vol create <b>NW2</b>-sapmnt replica 3 glust-0:/rhs/<b>NW2</b>/sapmnt glust-1:/rhs/<b>NW2</b>/sapmnt glust-2:/rhs/<b>NW2</b>/sapmnt force
   sudo gluster vol create <b>NW2</b>-trans replica 3 glust-0:/rhs/<b>NW2</b>/trans glust-1:/rhs/<b>NW2</b>/trans glust-2:/rhs/<b>NW2</b>/trans force
   sudo gluster vol create <b>NW2</b>-sys replica 3 glust-0:/rhs/<b>NW2</b>/sys glust-1:/rhs/<b>NW2</b>/sys glust-2:/rhs/<b>NW2</b>/sys force
   sudo gluster vol create <b>NW2</b>-ascs replica 3 glust-0:/rhs/<b>NW2</b>/ascs glust-1:/rhs/<b>NW2</b>/ascs glust-2:/rhs/<b>NW2</b>/ascs force
   sudo gluster vol create <b>NW2</b>-aers replica 3 glust-0:/rhs/<b>NW2</b>/aers glust-1:/rhs/<b>NW2</b>/aers glust-2:/rhs/<b>NW2</b>/aers force
   
   sudo gluster volume start <b>NW2</b>-sapmnt
   sudo gluster volume start <b>NW2</b>-trans
   sudo gluster volume start <b>NW2</b>-sys
   sudo gluster volume start <b>NW2</b>-ascs
   sudo gluster volume start <b>NW2</b>-aers
   </code></pre>

## <a name="next-steps"></a>Passos seguintes

* [Instalar o SAP ASCS e o banco de dados](high-availability-guide-rhel.md)
* [Planejamento e implementação de máquinas virtuais do Azure para SAP][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA no Azure (instâncias grandes), consulte [alta disponibilidade e recuperação de desastre do SAP Hana (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA em VMs do Azure, consulte [alta disponibilidade de SAP Hana em VMS (máquinas virtuais) do Azure][sap-hana-ha]
