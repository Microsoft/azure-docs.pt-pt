---
title: Configurar Pacemaker no SUSE Linux Enterprise Server no Azure | Documentos da Microsoft
description: Como configurar Pacemaker no SUSE Linux Enterprise Server no Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 551f140c22677bea363ad5d8f43bf9670f783a1d
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725607"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Como configurar Pacemaker no SUSE Linux Enterprise Server no Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Existem duas opções para configurar um cluster de Pacemaker no Azure. Pode utilizar um agente de delimitação por barreiras, que trata da reinicialização de um nó com falha através das APIs do Azure ou pode utilizar um dispositivo SBD.

O dispositivo SBD requer pelo menos uma máquina virtual adicional que age como um servidor de destino iSCSI e fornece um dispositivo SBD. Estes servidores de destino iSCSI no entanto podem ser partilhado com outros clusters Pacemaker. A vantagem de usar um dispositivo SBD é um tempo de failover mais rápido e, se você estiver usando dispositivos SBD no local, o não exigirá nenhuma alteração na maneira como você opera o cluster pacemaker. Pode utilizar até três SBD dispositivos para um cluster de Pacemaker para permitir que um dispositivo SBD fique indisponível, por exemplo durante a aplicação de patches de SO de servidor de destino iSCSI. Se pretender utilizar mais do que um dispositivo SBD por Pacemaker, certifique-se implementar vários servidores de destino iSCSI e ligar um SBD de cada servidor de destino iSCSI. Recomendamos que utilize um dispositivo SBD ou três. Pacemaker não será capaz de fence automaticamente um nó de cluster, se configurar apenas dois dispositivos SBD e um deles não está disponível. Se quiser ser capaz de cerca de quando um servidor de destino iSCSI está inativa, terá de utilizar três dispositivos SBD e, portanto, servidores de destino iSCSI três.

Se você não quiser investir em uma máquina virtual adicional, também poderá usar o agente de limite do Azure. A desvantagem é que uma ativação pós-falha pode demorar entre 10 a 15 minutos, se falha de parar um recurso ou os nós do cluster não é possível comunicar que uns aos outros mais.

![Pacemaker no Descrição geral do SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Ao planejar e implantar o Linux Pacemaker em cluster os nós e dispositivos SBD, é essencial para a confiabilidade global da configuração do cluster completo que o encaminhamento entre as VMs envolvido e a VM (s) que aloja o SBD dispositivo (s) não está a passar através de todos os outros dispositivos, como [NVAs](https://azure.microsoft.com/solutions/network-appliances/). Caso contrário, problemas e eventos de manutenção com a NVA podem ter um impacto negativo sobre a estabilidade e a confiabilidade da configuração do cluster geral. Para evitar esses obstáculos, não defina regras de roteamento de NVAs ou [regras de roteamento definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) que roteiam o tráfego entre nós clusterizados e dispositivos SBD por meio de NVAs e dispositivos semelhantes ao planejar e implantar nós clusterizados do Linux pacemaker e Dispositivos SBD. 
>

## <a name="sbd-fencing"></a>A delimitação por barreiras SBD

Se pretender utilizar um dispositivo SBD para delimitação por barreiras, siga estes passos.

### <a name="set-up-iscsi-target-servers"></a>Configurar servidores de destino iSCSI

Tem primeiro de criar máquinas virtuais de destino de iSCSI. servidores de destino iSCSI podem ser compartilhados com vários clusters Pacemaker.

1. Implementar o novo SP1 de 12 do SLES ou máquinas de virtuais superior e ligar aos mesmos através de ssh. Os computadores não precisam ser grandes. Um tamanho de máquina virtual como Standard_E2s_v3 ou Standard_D2s_v3 é suficiente. Certifique-se utilizar o armazenamento Premium o disco do SO.

Execute os seguintes comandos em todos os **máquinas de virtuais de destino iSCSI**.

1. Atualizar SLES

   <pre><code>sudo zypper update
   </code></pre>

1. Remover pacotes

   Para evitar um problema conhecido com targetcli e SLES 12 SP3, desinstale os seguintes pacotes. Pode ignorar erros sobre os pacotes que não podem ser encontradas

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Instalar pacotes de destino iSCSI

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Ativar o serviço de destino iSCSI

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Criar dispositivo do iSCSI no servidor de destino iSCSI

Execute os seguintes comandos em todos os **máquinas de virtuais de destino iSCSI** para criar os discos iSCSI para os clusters utilizados pelos seus sistemas SAP. No exemplo a seguir, são criados dispositivos SBD para múltiplos clusters. Ele mostra como usaria um servidor de destino iSCSI para múltiplos clusters. Os dispositivos SBD são colocados no disco do SO. Certifique-se de que tem espaço suficiente.

**`nfs`** é usado para identificar o cluster NFS, **ascsnw1** é usado para identificar o cluster ASCS de **NW1**, **dbnw1** é usado para identificar o cluster de banco de dados de **NW1**, **NFS-0** e **NFS-1** são os nomes de host dos nós de cluster NFS,  **NW1-xscs-0** e **NW1-xscs-1** são os nomes de host dos nós de cluster NW1 ASCS e **NW1-dB-0** e **NW1-DB-1** são os nomes de host dos nós de cluster de banco de dados. Substituí-los com os nomes de anfitrião dos nós do cluster e o SID do seu sistema SAP.

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

Pode verificar se tudo o que foi configurado corretamente com

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

Ligar ao dispositivo iSCSI que foi criado no último passo do cluster.
Execute os seguintes comandos em nós do novo cluster que pretende criar.
Os seguintes itens são prefixados com ambos **[A]** - aplicáveis a todos os nós, **[1]** – apenas aplicável no nó 1 ou **[2]** – apenas aplicável a nó 2.

1. **[A]**  Ligar para os dispositivos de iSCSI

   Em primeiro lugar, ative os serviços SBD e iSCSI.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Alterar o nome de iniciador no primeiro nó

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Altere o conteúdo do ficheiro de acordo com as ACLs que utilizou quando criou o dispositivo de iSCSI no servidor de destino iSCSI, por exemplo, para o servidor NFS.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]**  Alterar o nome de iniciador para o segundo nó

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Alterar o conteúdo do ficheiro de acordo com as ACLs que utilizou quando criou o dispositivo de iSCSI no servidor de destino iSCSI

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]**  Reiniciar o serviço iSCSI

   Agora, reinicie o serviço iSCSI para aplicar a alteração

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Ligue os dispositivos de iSCSI. No exemplo abaixo, 10.0.0.17 é o endereço IP do servidor de destino iSCSI e 3260 é a porta predefinida. <b>iqn.2006 04.nfs.local:nfs</b> é um dos nomes de destino que está listado ao executar o primeiro comando abaixo (iscsiadm -m discovery).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Certifique-se de que os dispositivos de iSCSI estão disponíveis e tome nota do nome de dispositivo (no exemplo seguinte/desenvolvimento/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Agora, obter os IDs dos dispositivos iSCSI.

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

   O comando lista as três identificações de dispositivo para cada dispositivo SBD. Recomendamos que está a utilizar o ID que começa com scsi-3, no exemplo acima isso

   * **/Dev/Disk/by-ID/SCSI-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/Dev/Disk/by-ID/SCSI-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]**  Criar o dispositivo SBD

   Utilize o ID de dispositivo dos dispositivos iSCSI para criar os novos dispositivos SBD no primeiro nó de cluster.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]**  Adaptar-se a configuração SBD

   Abra o ficheiro de configuração SBD

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Alterar a propriedade do dispositivo SBD, permitir a integração de pacemaker e alterar o modo de início de SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   <b>SBD_WATCHDOG="yes"</b>
   </code></pre>

   Criar o `softdog` arquivo de configuração

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Agora, carregar o módulo

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalação de cluster

Os seguintes itens são prefixados com ambos **[A]** - aplicáveis a todos os nós, **[1]** – apenas aplicável no nó 1 ou **[2]** – apenas aplicável a nó 2.

1. **[A]**  Atualizar SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** configurar o sistema operacional

   Em alguns casos, o Pacemaker cria muitos processos e, deste modo, esgotar o número permitido de processos. Nesse caso, um heartbeat entre os nós de cluster poderá falhar e levar a ativação pós-falha dos seus recursos. Recomendamos que aumente os processos de permitido máximos definindo o parâmetro seguinte.

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

   Reduza o tamanho da cache modificado. Para obter mais informações, consulte [desempenho de escrita de baixa no SLES 11/12 servidores com grandes RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** configurar nuvem-netconfig-Azure para cluster de alta disponibilidade

   Altere o arquivo de configuração para a interface de rede, conforme mostrado abaixo para impedir que o plug-in de rede de nuvem remova o endereço IP virtual (pacemaker deve controlar a atribuição de VIP). Para obter mais informações, consulte [SuSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]**  Ativar o ssh acesso

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]**  Ativar o ssh acesso

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   sudo ssh-keygen

   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]**  Ativar o ssh acesso

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Agentes de cerca de instalar
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > Se estiver usando o SuSE Linux Enterprise Server para SAP 15, lembre-se de que você precisa ativar o módulo adicional e instalar o componente adicional, que é o pré-requisito para usar o agente de isolamento do Azure. Para saber mais sobre os módulos e extensões do SUSE, consulte [módulos e extensões explicadas](https://www.suse.com/documentation/sles-15/singlehtml/art_modules/art_modules.html). Siga as instruções abaixo para instalar o SDK do Python do Azure. 

   As instruções a seguir sobre como instalar o SDK do Python do Azure só são aplicáveis ao SuSE Enterprise Server para SAP **15**.  

    - Se você estiver usando o traga sua própria assinatura, siga estas instruções  

    <pre><code>
    #Activate module PackageHub/15/x86_64
    sudo SUSEConnect -p PackageHub/15/x86_64
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

     - Se você estiver usando a assinatura paga conforme o uso, siga estas instruções  

    <pre><code>#Activate module PackageHub/15/x86_64
    zypper ar https://download.opensuse.org/repositories/openSUSE:/Backports:/SLE-15/standard/ SLE15-PackageHub
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

1. **[A]**  Configurar a resolução de nomes de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos. A vantagem de utilizar /etc/hosts é que o seu cluster se torna independente de DNS, que também poderia ser um ponto único de falhas.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e o nome de anfitrião para corresponder ao seu ambiente   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]**  Instalar Cluster

   <pre><code>sudo ha-cluster-init
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Network address to bind to (e.g.: 192.168.1.0) [10.0.0.0] <b>Press ENTER</b>
   # Multicast address (e.g.: 239.x.x.x) [239.232.97.43] <b>Press ENTER</b>
   # Multicast port [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]**  Adicionar nó ao cluster

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]**  Alterar hacluster palavra-passe para a mesma palavra-passe

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]**  Configurar corosync para utilizar outro transporte e adicionar nodelist. Caso contrário, o cluster não funcionará.

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Adicione o seguinte conteúdo de negrito para o ficheiro se os valores não forem existe ou diferente. Certifique-se alterar o token para 30000 para permitir que a memória preservação da manutenção. Para obter mais informações, consulte [Este artigo para Linux][virtual-machines-linux-maintenance] ou [Windows][virtual-machines-windows-maintenance]. Além disso, certifique-se remover o parâmetro mcastaddr.

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
     # remove parameter mcastaddr
     <b># mcastaddr: IP</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
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

## <a name="create-azure-fence-agent-stonith-device"></a>Criar agente de cerca de Azure STONITH dispositivos

O dispositivo STONITH utiliza um Principal de serviço para autorizar com o Microsoft Azure. Siga estes passos para criar um Principal de serviço.

1. Ir para <https://portal.azure.com>
1. Abra o painel Azure Active Directory  
   Vá para propriedades e anote o ID de diretório. Este é o **ID de inquilino**.
1. Clique em registos de aplicações
1. Clique em novo registro
1. Insira um nome, selecione "contas somente neste diretório da organização" 
2. Selecione o tipo de aplicativo "Web", insira uma URL de logon (por exemplo,\/http:/localhost) e clique em Adicionar  
   O URL de início de sessão não é utilizado e pode ser qualquer URL válido
1. Selecione certificados e segredos e clique em novo segredo do cliente
1. Insira uma descrição para uma nova chave, selecione "nunca expira" e clique em Adicionar
1. Anote o valor. Ele é usado como o **palavra-passe** para o Principal de serviço
1. Selecione visão geral. Anote o ID da aplicação. Ele é usado como o nome de utilizador (**ID de início de sessão** nos passos abaixo) de Principal de serviço

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Criar uma função personalizada para o agente de cerca

A entidade de serviço não tem permissões para acessar os recursos do Azure por padrão. Tem de conceder as permissões do Principal de serviço para iniciar e parar (desaloque) todas as máquinas virtuais do cluster. Se já não tiver criado a função personalizada, pode criá-la utilizando [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) ou [da CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Utilize o seguinte conteúdo para o ficheiro de entrada. Precisa adaptar o conteúdo para as suas subscrições, substitua c276fc76-9cd4-44c9-99a7-4fd71546436e e e91d47c4-76f3-4271-a796-21b4ecfe3624 com os Ids da sua subscrição. Se tiver apenas uma subscrição, remova a segunda entrada assignablescopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** atribuir a função personalizada à entidade de serviço

Atribua a função personalizada "Linux cerca agente de função" que foi criado no último capítulo para o Principal de serviço. Não use mais a função de proprietário!

1. Ir para[https://portal.azure.com](https://portal.azure.com)
1. Abra o painel de todos os recursos
1. Selecione a máquina virtual do primeiro nó de cluster
1. Clique em controle de acesso (IAM)
1. Clique em Adicionar atribuição de função
1. Selecione a função de "Função de agente de cerca de Linux"
1. Introduza o nome da aplicação que criou acima
1. Clicar em Guardar

Repita os passos acima para o segundo nó de cluster.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Criar os dispositivos STONITH

Depois de editar as permissões para as máquinas virtuais, pode configurar os dispositivos STONITH no cluster.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Configuração para SBD Pacemaker

1. **[1]**  Permitem a utilização de um dispositivo STONITH e defina o atraso cerca

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

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Configuração do pacemaker para eventos agendados do Azure

O Azure oferece [eventos agendados](https://docs.microsoft.com/azure/virtual-machines/linux/scheduled-events). Os eventos agendados são fornecidos por meio do serviço de metadados e permitem tempo para o aplicativo se preparar para eventos como desligamento de VM, reimplantação de VM, etc. Agente de recursos **[Azure-](https://github.com/ClusterLabs/resource-agents/pull/1161)** monitores de eventos para eventos agendados do Azure. Se forem detectados eventos, o agente tentará interromper todos os recursos na VM afetada e movê-los para outro nó no cluster. Para obter os recursos adicionais do pacemaker, é necessário configurá-los. 

1. **[A]** instalar o agente **do Azure-Events** . 

<pre><code>sudo zypper install resource-agents
</code></pre>

2. **[1]** configurar os recursos no pacemaker. 

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
   > Depois de configurar os recursos do pacemaker para o agente do Azure-Events, ao colocar o cluster dentro ou fora do modo de manutenção, você poderá receber mensagens de aviso como:  
     Aviso: CIB-Bootstrap-Options: atributo desconhecido ' hostName_ <strong>hostname</strong>'  
     Aviso: CIB-Bootstrap-Options: atributo desconhecido ' Azure-events_globalPullState '  
     Aviso: CIB-Bootstrap-Options: atributo desconhecido ' hostName_ <strong>hostname</strong>'  
   > Essas mensagens de aviso podem ser ignoradas.

## <a name="next-steps"></a>Passos Seguintes

* [Planejamento e implementação de máquinas virtuais do Azure para SAP][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server][sles-nfs-guide]
* [Alta disponibilidade para SAP NetWeaver em VMs do Azure em SUSE Linux Enterprise Server para aplicativos SAP][sles-guide]
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA em VMs do Azure, consulte [alta disponibilidade de SAP Hana em VMS (máquinas virtuais) do Azure][sap-hana-ha]
