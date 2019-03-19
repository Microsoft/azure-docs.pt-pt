---
title: Disponibilidade elevada para NFS nas VMs do Azure no SUSE Linux Enterprise Server | Documentos da Microsoft
description: Disponibilidade elevada para NFS nas VMs do Azure no SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
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
ms.openlocfilehash: da465fb9fe51b2be5ec90df1ac75c50271db87a9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992070"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Disponibilidade elevada para NFS nas VMs do Azure no SUSE Linux Enterprise Server

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

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Este artigo descreve como implementar as máquinas virtuais, configurar as máquinas virtuais, instalar o framework de cluster e instalar um servidor NFS elevada disponibilidade, que pode ser utilizado para armazenar os dados compartilhados de um sistema SAP elevada disponibilidade.
Este guia descreve como configurar um servidor NFS elevada disponibilidade, que é utilizado por dois sistemas SAP, NW1 e NW2. Os nomes dos recursos (por exemplo, as máquinas virtuais, redes virtuais) no exemplo partem do princípio de que utiliza a [modelo de servidor de ficheiros SAP] [ template-file-server] com o prefixo de recurso **prod**.

Leia as seguintes notas de SAP e documentos pela primeira vez

* A nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure que são suportados para a implementação de SAP software
  * Informações de capacidade importante para tamanhos de VM do Azure
  * Software SAP suportado e o sistema operativo (SO) e combinações de base de dados
  * Versão de kernel do SAP necessária para Windows e Linux no Microsoft Azure

* A nota SAP [2015553] apresenta uma lista de pré-requisitos para implementações de software SAP suportadas para SAP no Azure.
* A nota SAP [2205917] recomendado configurações de SO para o SUSE Linux Enterprise Server para aplicativos de SAP
* A nota SAP [1944799] tem diretrizes do SAP HANA para SUSE Linux Enterprise Server para aplicações SAP
* A nota SAP [2178632] tem informações detalhadas sobre todas as monitorizações métricas comunicadas para o SAP no Azure.
* A nota SAP [2191498] tem a versão necessária do agente de anfitrião do SAP para o Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento de SAP no Linux no Azure.
* A nota SAP [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A nota SAP [1999351] tem informações adicionais de resolução de problemas avançada de monitorização a extensão do Azure para SAP.
* [WIKI de Comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as necessárias SAP Notes para o Linux.
* [Máquinas de virtuais de planeamento e implementação para o SAP no Linux do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP no Linux (Este artigo)][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [SUSE Linux Enterprise elevada disponibilidade extensão 12 SP3 melhores práticas guias][sles-hae-guides]
  * Armazenamento NFS de elevada disponibilidade com DRBD e Pacemaker
* [SUSE Linux Enterprise Server para o SAP aplicativos 12 SP3 melhores práticas guias][sles-for-sap-bp]
* [Notas de versão do SP3 de extensão 12 do SUSE elevada disponibilidade][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Descrição geral

Para assegurar elevada disponibilidade, o SAP NetWeaver requer um servidor NFS. O servidor NFS está configurado num cluster separado e pode ser utilizado por vários sistemas SAP.

![Descrição geral do SAP NetWeaver elevada disponibilidade](./media/high-availability-guide-nfs/ha-suse-nfs.png)

O servidor NFS utiliza um nome de anfitrião virtual dedicado e endereços IP virtuais para todos os sistemas SAP que utilizam este servidor NFS. No Azure, um balanceador de carga é necessário utilizar um endereço IP virtual. A lista seguinte mostra a configuração de Balanceador de carga.        

* Configuração de front-end
  * Endereço IP 10.0.0.4 para NW1
  * Endereço IP 10.0.0.5 para NW2
* Configuração de back-end
  * Ligado a interfaces de rede primário de todas as máquinas virtuais que devem fazer parte do NFS cluster
* Porta de sonda
  * Porta 61000 para NW1
  * Porta 61001 para NW2
* Regras de balanceamento de carga
  * 2049 TCP para NW1
  * UDP 2049 para NW1
  * 2049 TCP para NW2
  * UDP 2049 para NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Configurar um servidor NFS elevada disponibilidade

Pode utilizar um modelo do Azure a partir do GitHub para implementar necessários de todos os recursos do Azure, incluindo as máquinas virtuais, conjunto de disponibilidade e Balanceador de carga ou pode implementar os recursos manualmente.

### <a name="deploy-linux-via-azure-template"></a>Implementar o Linux através do modelo do Azure

O Azure Marketplace contém uma imagem para o SUSE Linux Enterprise Server para 12 de aplicações SAP que pode utilizar para implementar novas máquinas virtuais.
Pode utilizar um dos modelos de início rápido no GitHub para implementar todos os recursos necessários. O modelo implementa as máquinas virtuais, o Balanceador de carga, disponibilidade definida etc. Siga estes passos para implementar o modelo:

1. Abra o [modelo de servidor de ficheiros SAP] [ template-file-server] no portal do Azure   
1. Introduza os seguintes parâmetros
   1. Prefixo de recursos  
      Introduza o prefixo que pretende utilizar. O valor é utilizado como um prefixo para os recursos que são implementados.
   2. Contagem de sistema do SAP  
      Introduza o número de sistemas SAP que irá utilizar este servidor de ficheiros. Isto irá implementar a quantidade necessária de configurações de front-end, regras de balanceamento de carga sonda portas, discos, etc.
   3. Tipo de SO  
      Selecione uma das distribuições de Linux. Para este exemplo, selecione o SLES 12
   4. Nome de utilizador administrador e a palavra-passe de administrador  
      Um novo utilizador é criado que pode ser utilizado para iniciar sessão máquina.
   5. ID de sub-rede  
      Se pretender implementar a VM para uma VNet já existente em que tem uma sub-rede definida a VM deve ser atribuída para nomear o ID dessa sub-rede. Normalmente, é o ID de /subscriptions/**&lt;ID da subscrição&gt;**/resourceGroups/**&lt;nome do grupo de recursos&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;nome da rede virtual&gt;**/subnets/**&lt;nome da sub-rede&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Implementar manualmente o Linux através do portal do Azure

Tem primeiro de criar as máquinas virtuais para este cluster NFS. Em seguida, pode criar um balanceador de carga e utilize as máquinas virtuais nos agrupamentos de back-end.

1. Criar um Grupo de Recursos
1. Criar uma rede Virtual
1. Criar um conjunto de disponibilidade  
   Domínio de atualização máx. de conjunto
1. Criar, pelo menos, o uso de 1 de Máquina Virtual SLES4SAP 12 SP3, neste exemplo BYOS de SP3 de 12 SLES4SAP de imagens SLES para SAP aplicativos 12 SP3 (BYOS) é utilizado  
   Selecione o conjunto de disponibilidade que criou anteriormente  
1. Criar o 2, Máquina Virtual utilize, pelo menos, SP3 de 12 SLES4SAP, neste exemplo, a imagem de SLES4SAP 12 SP3 BYOS  
   SLES para SAP aplicativos 12 SP3 (BYOS) é utilizado  
   Selecione o conjunto de disponibilidade que criou anteriormente  
1. Adicione um disco de dados para cada sistema SAP para ambas as máquinas virtuais.
1. Criar um balanceador de carga (interno)  
   1. Criar os endereços IP de front-end
      1. Endereço IP 10.0.0.4 para NW1
         1. Abra o Balanceador de carga, selecione o conjunto IP de front-end e clique em Adicionar
         1. Introduza o nome do novo conjunto IP de front-end (por exemplo **nw1-front-end**)
         1. Definir a atribuição para estático e introduza o endereço IP (por exemplo **10.0.0.4**)
         1. Clique em OK
      1. Endereço IP 10.0.0.5 para NW2
         * Repita os passos acima para NW2
   1. Criar os conjuntos de back-end
      1. Ligado a interfaces de rede primário de todas as máquinas virtuais que devem fazer parte do cluster NFS para NW1
         1. Abra o Balanceador de carga, selecione conjuntos de back-end e clique em Adicionar
         1. Introduza o nome do novo conjunto de back-end (por exemplo **nw1-back-end**)
         1. Clique em Adicionar uma máquina virtual
         1. Selecione o conjunto de disponibilidade que criou anteriormente
         1. Selecione as máquinas virtuais do NFS cluster
         1. Clique em OK
      1. Ligado a interfaces de rede primário de todas as máquinas virtuais que devem fazer parte do cluster NFS para NW2
         * Repita os passos acima para criar um conjunto de back-end para NW2
   1. Criar as sondas de estado de funcionamento
      1. Porta 61000 para NW1
         1. Abra o Balanceador de carga, selecione sondas de estado de funcionamento e clique em Adicionar
         1. Introduza o nome da sonda de estado de funcionamento novo (por exemplo **nw1 hp**)
         1. Selecione TCP como protocolo, porta 610**00**, manter intervalo de 5 e 2 de limiar com funcionamento incorreto
         1. Clique em OK
      1. Porta 61001 para NW2
         * Repita os passos acima para criar uma sonda de estado de funcionamento para NW2
   1. Regras de balanceamento de carga
      1. 2049 TCP para NW1
         1. Abra o Balanceador de carga, selecione as regras de balanceamento de carga e clique em Adicionar
         1. Introduza o nome da nova regra de Balanceador de carga (por exemplo **nw1-lb-2049**)
         1. Selecione o endereço IP de front-end, o conjunto de back-end e a sonda de estado de funcionamento que criou anteriormente (por exemplo **nw1-front-end**)
         1. Protocolo de manter **TCP**, introduza a porta **2049**
         1. Aumentar o tempo limite de inatividade para 30 minutos
         1. **Certifique-se de ativar o IP flutuante**
         1. Clique em OK
      1. UDP 2049 para NW1
         * Repita os passos acima para a porta 2049 e UDP para NW1
      1. 2049 TCP para NW2
         * Repita os passos acima para a porta 2049 e TCP para NW2
      1. UDP 2049 para NW2
         * Repita os passos acima para a porta 2049 e UDP para NW2

### <a name="create-pacemaker-cluster"></a>Criar Pacemaker cluster

Siga os passos em [Pacemaker no SUSE Linux Enterprise Server no Azure a configurar](high-availability-guide-suse-pacemaker.md) para criar um cluster de Pacemaker básico para este servidor NFS.

### <a name="configure-nfs-server"></a>Configurar o servidor NFS

Os seguintes itens são prefixados com ambos **[A]** - aplicáveis a todos os nós, **[1]** – apenas aplicável no nó 1 ou **[2]** – apenas aplicável a nó 2.

1. **[A]**  Configurar a resolução de nomes de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e o nome de anfitrião para corresponder ao seu ambiente
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]**  Servidor NFS ativar

   Criar a entrada de exportação NFS de raiz

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]**  Instalar componentes de drbd

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]**  Criar uma partição para os dispositivos de drbd

   Lista de todos os discos de dados disponíveis

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Exemplo de saída
   
   ```
   lun0  lun1
   ```

   Criar partições para cada disco de dados

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]**  Configurações de criar LVM

   Listar todas as partições disponíveis

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Exemplo de saída
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Criar volumes LVM para cada partição

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]**  Configurar drbd

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Certifique-se de que o ficheiro de drbd.conf contém as seguintes duas linhas

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Alterar a configuração de global drbd

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Adicione as seguintes entradas para a secção líquida e o manipulador.

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]**  Criar dispositivos de drbd NFS

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Inserir a configuração para o novo dispositivo drbd e saída

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Inserir a configuração para o novo dispositivo drbd e saída

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Criar o dispositivo drbd e iniciá-lo

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Ignorar sincronização inicial

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Definir o nó principal

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Esperar até que os novos dispositivos de drbd são sincronizados

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]**  Criar sistemas de ficheiros nos dispositivos drbd

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]**  Configurar a deteção de drbd dividido

   Quando utilizar drbd para sincronizar os dados de um anfitrião para outro, pode ocorrer um cérebro então chamado de divisão. Um cérebro de divisão é um cenário em que ambos os nós de cluster promovido o dispositivo drbd esteja primário e correu fora de sincronia. Pode ser uma rara situação, mas pretender continuar a processar e resolver um cérebro de dividir o mais rápido possível. Portanto, é importante ser notificado quando um cérebro de divisão aconteceu.

   Leia [a documentação oficial drbd](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) sobre como configurar uma notificação de cérebro de divisão.

   Também é possível recuperar automaticamente de um cenário de cérebro de divisão. Para obter mais informações, leia [políticas de recuperação automática de divisão cérebro](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Configurar a estrutura de Cluster

1. **[1]**  Adicionar dispositivos de drbd NFS para o sistema SAP NW1 à configuração do cluster

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]**  Adicionar dispositivos de drbd NFS para o sistema SAP NW2 à configuração do cluster

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]**  Desativar modo de manutenção
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Passos Seguintes

* [Instalar o SAP ASCS e a base de dados](high-availability-guide-suse.md)
* [Máquinas de virtuais de planeamento e implementação de SAP do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a elevada disponibilidade e o plano de recuperação após desastre do SAP HANA no Azure (instâncias grandes), veja [SAP HANA (instâncias grandes) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer a elevada disponibilidade e o plano de recuperação após desastre do SAP HANA em VMs do Azure, veja [disponibilidade elevada do SAP HANA em máquinas virtuais do Azure (VMs)][sap-hana-ha]
