---
title: Criação do Pacemaker no SLES em Azure | Microsoft Docs
description: Configuração do Pacemaker no SUSE Linux Enterprise Server em Azure
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
ms.date: 02/03/2020
ms.author: radeltch
ms.openlocfilehash: ddee5edcf1d19af0fb088976c590b62866a1484e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674413"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Configuração do Pacemaker no SUSE Linux Enterprise Server em Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Existem duas opções para criar um cluster Pacemaker em Azure. Pode utilizar um agente de esgrima, que se ocupa de reiniciar um nó falhado através das APIs Azure ou pode utilizar um dispositivo SBD.

O dispositivo SBD requer pelo menos uma máquina virtual adicional que funciona como um servidor alvo iSCSI e fornece um dispositivo SBD. Estes servidores-alvo iSCSI podem, no entanto, ser partilhados com outros clusters pacemaker. A vantagem de utilizar um dispositivo SBD é que, se já estiver a utilizar dispositivos SBD no local, não necessita de alterações na forma como opera o cluster do pacemaker. Pode utilizar até três dispositivos SBD para um cluster Pacemaker para permitir que um dispositivo SBD fique indisponível, por exemplo durante a correção do sistema operativo do servidor alvo do iSCSI. Se pretender utilizar mais de um dispositivo SBD por Pacemaker, certifique-se de que implementa vários servidores-alvo do iSCSI e liga um SBD a cada servidor-alvo do iSCSI. Recomendamos a utilização de um dispositivo SBD ou três. O Pacemaker não será capaz de vedar automaticamente um nó de cluster se configurar apenas dois dispositivos SBD e um deles não estiver disponível. Se quiser ser capaz de vedar quando um servidor alvo do iSCSI estiver em baixo, tem de utilizar três dispositivos SBD e, portanto, três servidores-alvo iSCSI, que é a configuração mais resistente quando se utiliza SBDs.

O agente da Azure Fence não necessita de implantação de máquinas virtuais adicionais.   

![Pacemaker na visão geral do SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Ao planear e implantar os nós agrupados linux pacemaker e dispositivos SBD, é essencial para a fiabilidade global da configuração completa do cluster que o encaminhamento entre os VMs envolvidos e os VM(s) que hospedam os dispositivos SBD não está a passar por outros dispositivos como [os NVAs](https://azure.microsoft.com/solutions/network-appliances/). Caso contrário, problemas e eventos de manutenção com a NVA podem ter um impacto negativo na estabilidade e fiabilidade da configuração global do cluster. Para evitar tais obstáculos, não defina regras de encaminhamento de NVAs ou regras de [encaminhamento definidos](../../../virtual-network/virtual-networks-udr-overview.md) pelo utilizador que encaminham o tráfego entre nós agrupados e dispositivos SBD através de NVAs e dispositivos similares ao planear e implantar nós agrupados Linux Pacemaker e dispositivos SBD. 
>

## <a name="sbd-fencing"></a>Esgrima SBD

Siga estes passos se quiser utilizar um dispositivo SBD para esgrima.

### <a name="set-up-iscsi-target-servers"></a>Configurar servidores-alvo iSCSI

Primeiro, é necessário criar as máquinas virtuais-alvo iSCSI. Os servidores-alvo do iSCSI podem ser partilhados com vários clusters pacemaker.

1. Implemente novas máquinas virtuais SLES 12 SP1 ou superiores e ligue-as através do ssh. As máquinas não precisam de ser grandes. Um tamanho de máquina virtual como Standard_E2s_v3 ou Standard_D2s_v3 é suficiente. Certifique-se de que utiliza o armazenamento Premium do disco OS.

Executar os seguintes comandos em todas as **máquinas virtuais alvo iSCSI**.

1. Atualizar SLES

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > Poderá ter de reiniciar o sistema operativo depois de atualizar ou atualizar o SISTEMA. 

1. Remover pacotes

   Para evitar um problema conhecido com targetcli e SLES 12 SP3, desinstale os seguintes pacotes. Pode ignorar erros sobre pacotes que não podem ser encontrados

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Instalar pacotes-alvo iSCSI

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Ativar o serviço de destino iSCSI

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Criar dispositivo iSCSI no servidor alvo do iSCSI

Executar os seguintes comandos em todas as **máquinas virtuais iSCSI alvo** para criar os discos iSCSI para os clusters utilizados pelos seus sistemas SAP. No exemplo seguinte, são criados dispositivos SBD para vários clusters. Mostra como usaria um servidor-alvo iSCSI para vários clusters. Os dispositivos SBD são colocados no disco oss. Certifique-se de ter espaço suficiente.

**`nfs`** é utilizado para identificar o cluster NFS, **o ascsnw1** é utilizado para identificar o cluster ASCS de **NW1**, **dbnw1** é usado para identificar o cluster de base de dados de **NW1**, **nfs-0** e **nfs-1** são os anfitriões dos nosmes de cluster NFS, **nw1-xscs-0** e **nw1-xscs-1** são os números anfitriões dos nós de cluster **NW1** ASCS, e **nw1-db-0** e **nw1-db-1** são os números hospedeiros dos nós de cluster de base de dados. Substitua-os pelos números de anfitrião dos seus nós de cluster e pelo SID do seu sistema SAP.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Você pode verificar se tudo foi configurado corretamente com

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>Configurar o dispositivo SBD

Ligue-se ao dispositivo iSCSI que foi criado no último passo do cluster.
Execute os seguintes comandos nos nós do novo cluster que pretende criar.
Os seguintes itens são prefixados com **ambos [A]** - aplicável a todos os nós, **[1]** - apenas aplicável ao nó 1 ou **[2]** - apenas aplicáveis ao nó 2.

1. **[A]** Ligar-se aos dispositivos iSCSI

   Em primeiro lugar, ativar os serviços iSCSI e SBD.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** Alterar o nome de iniciador no primeiro nó

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Altere o conteúdo do ficheiro para corresponder aos ACLs utilizados ao criar o dispositivo iSCSI no servidor alvo do iSCSI, por exemplo para o servidor NFS.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** Alterar o nome de iniciador no segundo nó

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Altere o conteúdo do ficheiro para corresponder aos ACLs utilizados ao criar o dispositivo iSCSI no servidor alvo do iSCSI

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** Reiniciar o serviço iSCSI

   Agora reinicie o serviço iSCSI para aplicar a alteração

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Ligue os dispositivos iSCSI. No exemplo abaixo, 10.0.0.17 é o endereço IP do servidor alvo iSCSI e 3260 é a porta padrão. <b>iqn.2006-04.nfs.local:nfs</b> é um dos nomes-alvo que está listado quando você executar o primeiro comando abaixo (iscsiadm -m discovery).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Certifique-se de que os dispositivos iSCSI estão disponíveis e anota o nome do dispositivo (no seguinte exemplo /dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Agora, recupere as identificações dos dispositivos iSCSI.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   A lista de comandos identifica três dispositivos para cada dispositivo SBD. Recomendamos a utilização do ID que começa com scsi-3, no exemplo acima deste é

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** Criar o dispositivo SBD

   Utilize o ID do dispositivo dos dispositivos iSCSI para criar os novos dispositivos SBD no primeiro nó de cluster.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** Adaptar o config SBD

   Abra o ficheiro config SBD

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Altere a propriedade do dispositivo SBD, permita a integração do pacemaker e altere o modo de partida do SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   Criar o `softdog` ficheiro de configuração

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Agora carregue o módulo

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalação de cluster

Os seguintes itens são prefixados com **ambos [A]** - aplicável a todos os nós, **[1]** - apenas aplicável ao nó 1 ou **[2]** - apenas aplicáveis ao nó 2.

1. **[A]** Atualização SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** Componente de instalação, necessário para os recursos de cluster

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** Instalar componente azure-lb, necessário para os recursos de cluster

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > Verifique a versão dos agentes de recursos do pacote e certifique-se de que os requisitos mínimos de versão estão cumpridos:  
   > - Para o SLES 12 SP4/SP5, a versão deve ser pelo menos agentes de recursos-4.3.018.a7fb5035-3.30.1.  
   > - Para sLES 15/15 SP1, a versão deve ser pelo menos agentes de recursos-4.3.0184.6ee15eb2-4.13.1.  

1. **[A]** Configurar o sistema operativo

   Em alguns casos, o Pacemaker cria muitos processos e, assim, esgota o número permitido de processos. Nesse caso, um batimento cardíaco entre os nós do cluster pode falhar e levar ao fracasso dos seus recursos. Recomendamos o aumento dos processos máximos permitidos definindo o seguinte parâmetro.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Reduza o tamanho da cache suja. Para obter mais informações, consulte [o desempenho da gravação de Low em servidores SLES 11/12 com RAM grande](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Configurar cloud-netconfig-azure para o aglomerado HA

   >[!NOTE]
   > Verifique a versão instalada do pacote **cloud-netconfig-azure** executando **zypper info cloud-netconfig-azure**. Se a versão no seu ambiente for 1.3 ou superior, deixará de ser necessário suprimir a gestão das interfaces de rede pelo plugin da rede cloud. Se a versão for inferior a 1.3, sugerimos atualizar o pacote **cloud-netconfig-azure** para a versão mais recente disponível.  

   Altere o ficheiro de configuração para a interface de rede, tal como mostrado abaixo, para evitar que o plugin da rede de nuvem remove o endereço IP virtual (o Pacemaker deve controlar a atribuição VIP). Para mais informações, consulte [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** Permitir o acesso ao SSH

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** Permitir o acesso ao SSH

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Permitir o acesso ao SSH

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Instale o pacote de agentes da Cerca, se utilizar o dispositivo STONITH, com base no Agente da Cerca de Azure.  
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > A versão instalada dos agentes da **cerca** de pacote deve ser pelo menos **4.4.0**  para beneficiar dos tempos de execução mais rápidos com o Agente da Cerca de Azure, se um conjunto de nós precisar de ser vedado. Recomendamos que atualize o pacote, se estiver a executar uma versão mais baixa.  


1. **[A]** Instalar Azure Python SDK 
   - No SLES 12 SP4 ou SLES 12 SP5
   <pre><code>
    # You may need to activate the Public cloud extention first
    SUSEConnect -p sle-module-public-cloud/12/x86_64
    sudo zypper install python-azure-mgmt-compute
   </code></pre> 

   - No SLES 15 e superior 
   <pre><code>
    # You may need to activate the Public cloud extention first. In this example the SUSEConnect command is for SLES 15 SP1
    SUSEConnect -p sle-module-public-cloud/15.1/x86_64
    sudo zypper install python3-azure-mgmt-compute
   </code></pre> 
 
   >[!IMPORTANT]
   >Dependendo da sua versão e do tipo de imagem, poderá ter de ativar a extensão de nuvem pública para a sua versão OS, antes de poder instalar o Azure Python SDK.
   >Pode verificar a extensão, executando extensões ---list do SUSEConnect.  
   >Para alcançar os tempos de execução mais rápidos com o Agente da Cerca de Azure:
   > - no SLES 12 SP4 ou SLES 12 SP5 instalar a versão **4.6.2** ou superior ao pacote python-azure-mgmt-compute  
   > - no SLES 15 instalar a versão **4.6.2** ou superior do pacote python **3**-azure-mgmt-compute 

1. **[A]** Resolução do nome do anfitrião da configuração

   Pode utilizar um servidor DNS ou modificar os /etc/anfitriões em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos.

   >[!IMPORTANT]
   > Se utilizar nomes de anfitriões na configuração do cluster, é vital ter uma resolução fiável do nome do anfitrião. A comunicação do cluster falhará, se os nomes não estiverem disponíveis e isso pode levar a atrasos de falha no cluster.
   > O benefício da utilização /etc/anfitriões é que o seu cluster se torna independente do DNS, o que pode ser um único ponto de falhas também.  
     
   <pre><code>sudo vi /etc/hosts

   </code></pre>

   Insira as seguintes linhas para /etc/anfitriões. Altere o endereço IP e o nome de anfitrião para combinar com o seu ambiente   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Instalar Cluster
- se utilizar dispositivos SBD para esgrima
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

- se *não utilizar* dispositivos SBD para esgrima
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # Do you wish to use SBD (y/n)? <b>n</b>
   #WARNING: Not configuring SBD - STONITH will be disabled.
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Adicione nó ao cluster

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** Alterar a palavra-passe hacluster para a mesma senha

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Ajuste as definições de corosync.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Adicione o seguinte conteúdo arrojado ao ficheiro se os valores não estiverem lá ou diferentes. Certifique-se de que muda o token para 30000 para permitir a manutenção da conservação da memória. Para mais informações, consulte [este artigo para Linux][virtual-machines-linux-maintenance] ou [Windows][virtual-machines-windows-maintenance].

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Em seguida, reinicie o serviço de corosync

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Configuração padrão do Pacemaker para SBD

A configuração nesta secção só é aplicável se utilizar o SBD STONITH.  

1. **[1]** Permitir a utilização de um dispositivo STONITH e definir o atraso da vedação

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Criar dispositivo STONITH agente da cerca Azure

Esta secção da documentação só é aplicável, se utilizar o STONITH, com base no agente da Cerca de Azure.
O dispositivo STONITH utiliza um Diretor de Serviço para autorizar contra o Microsoft Azure. Siga estes passos para criar um Diretor de Serviço.

1. Ir para <https://portal.azure.com>
1. Abra a lâmina do Diretório Ativo Azure  
   Vá ao Properties e escreva o ID do Diretório. Esta é a identificação do **inquilino.**
1. Clique nos registos da App
1. Clique em Novo Registo
1. Insira um Nome, selecione "Contas apenas neste diretório de organização" 
2. Selecione o Tipo de Aplicação "Web", introduza um URL de entrada de sinal (por exemplo http: \/ /localhost) e clique em Adicionar  
   O URL de inscrição não é usado e pode ser qualquer URL válido
1. Selecione Certificados e Segredos e, em seguida, clique em Novo segredo de cliente
1. Introduza uma descrição para uma nova chave, selecione "Nunca expira" e clique em Adicionar
1. Anota o Valor. É usado como **a palavra-passe** para o diretor de serviço
1. Selecione Descrição geral. Escreva o ID da aplicação. É usado como nome de utilizador **(ID de login** nos passos abaixo) do Diretor de Serviço

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Criar um papel personalizado para o agente da cerca

O Diretor de Serviço não tem permissões para aceder aos seus recursos Azure por defeito. Você precisa dar ao Service Principal permissões para iniciar e parar (deallocate) todas as máquinas virtuais do cluster. Se ainda não criou o papel personalizado, pode criá-lo utilizando [o PowerShell](../../../role-based-access-control/custom-roles-powershell.md#create-a-custom-role) ou [o Azure CLI](../../../role-based-access-control/custom-roles-cli.md)

Utilize o seguinte conteúdo para o ficheiro de entrada. É necessário adaptar o conteúdo às suas subscrições, ou seja, substituir c276fc76-9cd4-44c9-99a7-4fd71546436e e e91d47c4-76f3-4271-a796-21b4ecfe3624 com os IDs da sua assinatura. Se tiver apenas uma subscrição, remova a segunda entrada em 'AtribuableScopes'.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Atribuir o papel personalizado ao Diretor de Serviços

Atribua o papel personalizado "Linux Fence Agent Role" que foi criado no último capítulo ao Diretor de Serviço. Não use mais o papel de Proprietário!

1. Aceda a [https://portal.azure.com](https://portal.azure.com)
1. Abra a lâmina de todos os recursos
1. Selecione a máquina virtual do primeiro nó de cluster
1. Clique no controlo de acesso (IAM)
1. Clique em Adicionar atribuição de função
1. Selecione o papel "Linux Fence Agent Role"
1. Insira o nome da aplicação que criou acima
1. Clicar em Guardar

Repita os passos acima para o segundo nó de cluster.

### <a name="1-create-the-stonith-devices"></a>**[1]** Criar os dispositivos STONITH

Depois de editar as permissões para as máquinas virtuais, pode configurar os dispositivos STONITH no cluster.

> [!NOTE]
> A opção 'pcmk_host_map' só é necessária no comando, se os nomes dos anfitriões e os nomes Azure VM não forem idênticos. Especificar o mapeamento no nome de anfitrião do **formato:vm-name**.
> Consulte a secção arrojada no comando.

<pre><code>sudo crm configure property stonith-enabled=true
crm configure property concurrent-fencing=true
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
  params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>" \
  pcmk_monitor_retries=4 pcmk_action_limit=3 power_timeout=240 pcmk_reboot_timeout=900 <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
  op monitor interval=3600 timeout=120

sudo crm configure property stonith-timeout=900

</code></pre>

> [!IMPORTANT]
> As operações de monitorização e esgrima são des serializadas. Como resultado, se houver uma operação de monitorização mais longa e evento de esgrima simultânea, não há atrasos no cluster failover, devido à operação de monitorização já em funcionamento.

> [!TIP]
>O Agente de Cercas Azure requer conectividade de saída para pontos finais públicos como documentado, juntamente com possíveis soluções, na [conectividade de ponto final público para VMs utilizando iLB padrão](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Configuração de Pacemaker para eventos agendados do Azure

A Azure oferece [eventos agendados.](../../linux/scheduled-events.md) Os eventos regulares são fornecidos através do serviço de meta-dados e permitem tempo para a aplicação se preparar para eventos como encerramento de VM, redistribuição de VM, etc. O agente de recursos **[azure-events](https://github.com/ClusterLabs/resource-agents/pull/1161)** monitores para eventos Azure programados. Se os eventos forem detetados, o agente tentará parar todos os recursos no VM impactado e movê-los para outro nó no cluster. Para conseguir esse tal, é necessário configurar os recursos adicionais do Pacemaker. 

1. **[A]** Certifique-se de que o pacote para o agente **de eventos azure** já está instalado e atualizado. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** Configure os recursos no Pacemaker. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > Depois de configurar os recursos do Pacemaker para agente de eventos azure, quando colocar o cluster dentro ou fora do modo de manutenção, poderá receber mensagens de aviso como:  
     AVISO: opções cib-bootstrap: atributo desconhecido 'hostName_ <strong>nome de anfitrião'</strong>  
     AVISO: opções cib-bootstrap: atributo desconhecido 'azure-events_globalPullState'  
     AVISO: opções cib-bootstrap: atributo desconhecido 'hostName_ <strong>nome de anfitrião'</strong>  
   > Estas mensagens de aviso podem ser ignoradas.

## <a name="next-steps"></a>Passos seguintes

* [Azure Virtual Machines planejamento e implementação para SAP][planning-guide]
* [Implantação de máquinas virtuais Azure para SAP][deployment-guide]
* [Implantação DBMS de máquinas virtuais Azure para SAP][dbms-guide]
* [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server][sles-nfs-guide]
* [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP][sles-guide]
* Para aprender a estabelecer uma elevada disponibilidade e plano para a recuperação de desastres da SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]
