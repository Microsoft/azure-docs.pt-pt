---
title: Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server | Microsoft Docs
description: Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: 0e4daaa3417ce349111fbc811be36a4615058c76
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791729"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um servidor NFS altamente disponível que pode ser usado para armazenar os dados compartilhados de um sistema SAP altamente disponível.
Este guia descreve como configurar um servidor NFS altamente disponível que é usado por dois sistemas SAP, NW1 e NW2. Os nomes dos recursos (por exemplo, máquinas virtuais, redes virtuais) no exemplo pressupõem que você usou o [modelo de servidor de arquivos SAP][template-file-server] com o prefixo de recurso **prod**.

Leia as seguintes notas e documentos SAP primeiro

* Nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure com suporte para a implantação de software SAP
  * Informações de capacidade importantes para tamanhos de VM do Azure
  * Software SAP com suporte e combinações de so (sistema operacional) e banco de dados
  * Versão de kernel do SAP necessária para Windows e Linux em Microsoft Azure

* O SAP Note [2015553] lista os pré-requisitos para implantações de software SAP com suporte no SAP no Azure.
* A observação do SAP [2205917] tem as configurações do sistema operacional recomendadas para SuSE Linux Enterprise Server para aplicativos SAP
* O SAP Note [1944799] tem diretrizes SAP HANA para SuSE Linux Enterprise Server para aplicativos SAP
* A nota SAP [2178632] tem informações detalhadas sobre todas as métricas de monitoramento relatadas para SAP no Azure.
* A nota SAP [2191498] tem a versão do agente de host do SAP necessária para Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento SAP no Linux no Azure.
* A nota SAP [1984787] tem informações gerais sobre o SuSE Linux Enterprise Server 12.
* A nota SAP [1999351] tem informações adicionais para solução de problemas para a extensão de monitoramento avançado do Azure para SAP.
* O [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [Planejamento e implementação de máquinas virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP no Linux (este artigo)][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [Guias de práticas recomendadas da extensão de alta disponibilidade do SUSE Linux Enterprise 12 SP3][sles-hae-guides]
  * Armazenamento NFS altamente disponível com DRBD e pacemaker
* [Guias de práticas recomendadas do SUSE Linux Enterprise Server para aplicativos SAP 12 SP3][sles-for-sap-bp]
* [Notas de versão da extensão de alta disponibilidade do SUSE 12 SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Visão geral

Para obter alta disponibilidade, o SAP NetWeaver requer um servidor NFS. O servidor NFS é configurado em um cluster separado e pode ser usado por vários sistemas SAP.

![Visão geral de alta disponibilidade do SAP NetWeaver](./media/high-availability-guide-nfs/ha-suse-nfs.png)

O servidor NFS usa um nome de host virtual dedicado e endereços IP virtuais para cada sistema SAP que usa esse servidor NFS. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. A lista a seguir mostra a configuração do balanceador de carga.        

* Configuração de front-end
  * Endereço IP 10.0.0.4 para NW1
  * Endereço IP 10.0.0.5 para NW2
* Configuração de back-end
  * Conectado às interfaces de rede primárias de todas as máquinas virtuais que devem ser parte do cluster NFS
* Porta de investigação
  * Porta 61000 para NW1
  * Porta 61001 para NW2
* Regras de balanceamento de carga
  * 2049 TCP para NW1
  * 2049 UDP para NW1
  * 2049 TCP para NW2
  * 2049 UDP para NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Configurar um servidor NFS altamente disponível

Você pode usar um modelo do Azure do GitHub para implantar todos os recursos do Azure necessários, incluindo as máquinas virtuais, o conjunto de disponibilidade e o balanceador de carga, ou você pode implantar os recursos manualmente.

### <a name="deploy-linux-via-azure-template"></a>Implantar o Linux por meio do modelo do Azure

O Azure Marketplace contém uma imagem para SUSE Linux Enterprise Server para aplicativos SAP 12 que você pode usar para implantar novas máquinas virtuais.
Você pode usar um dos modelos de início rápido no GitHub para implantar todos os recursos necessários. O modelo implanta as máquinas virtuais, o balanceador de carga, o conjunto de disponibilidade, etc. Siga estas etapas para implantar o modelo:

1. Abra o [modelo de servidor de arquivos SAP][template-file-server] no portal do Azure   
1. Insira os seguintes parâmetros
   1. Prefixo de recurso  
      Insira o prefixo que você deseja usar. O valor é usado como um prefixo para os recursos que são implantados.
   2. Contagem de sistema SAP  
      Insira o número de sistemas SAP que usarão esse servidor de arquivos. Isso implantará a quantidade necessária de configurações de front-end, regras de balanceamento de carga, portas de investigação, discos, etc.
   3. Tipo de so  
      Selecione uma das distribuições do Linux. Para este exemplo, selecione SLES 12
   4. Nome de usuário do administrador e senha do administrador  
      Um novo usuário é criado e pode ser usado para fazer logon no computador.
   5. ID da sub-rede  
      Se você deseja implantar a VM em uma VNet existente em que você tem uma sub-rede definida, a VM deve ser atribuída, nomear a ID dessa sub-rede específica. A ID geralmente é semelhante a/subscriptions/ **&lt;ID da assinatura&gt;** /resourceGroups/ **&lt;nome do grupo de recursos&gt;** /Providers/Microsoft.Network/virtualNetworks/ **&lt;nome da rede virtual&gt;** /subnets/ **&lt;nome da sub-rede&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Implantar o Linux manualmente por meio de portal do Azure

Primeiro, você precisa criar as máquinas virtuais para esse cluster NFS. Posteriormente, você criará um balanceador de carga e usará as máquinas virtuais nos pools de back-end.

1. Criar um Grupo de Recursos
1. Criar uma rede virtual
1. Criar um conjunto de disponibilidade  
   Definir domínio de atualização máx.
1. Criar máquina virtual 1 Use pelo menos o SLES4SAP 12 SP3, neste exemplo, a imagem do SLES4SAP 12 SP3 BYOS do SLES for SAP Applications 12 SP3 (BYOS) é usada  
   Selecionar conjunto de disponibilidade criado anteriormente  
1. Criar máquina virtual 2 Use pelo menos SLES4SAP 12 SP3, neste exemplo, a imagem de BYOS do SLES4SAP 12 SP3  
   SLES for SAP Applications 12 SP3 (BYOS) é usado  
   Selecionar conjunto de disponibilidade criado anteriormente  
1. Adicione um disco de dados para cada sistema SAP a ambas as máquinas virtuais.
1. Criar um Load Balancer (interno)  
   1. Criar os endereços IP de front-end
      1. Endereço IP 10.0.0.4 para NW1
         1. Abra o balanceador de carga, selecione pool de IPS de front-end e clique em Adicionar
         1. Insira o nome do novo pool de IPS de front-end (por exemplo **NW1-frontend**)
         1. Defina a atribuição como estática e insira o endereço IP (por exemplo **10.0.0.4**)
         1. Clique em OK
      1. Endereço IP 10.0.0.5 para NW2
         * Repita as etapas acima para NW2
   1. Criar os pools de back-end
      1. Conectado a interfaces de rede primárias de todas as máquinas virtuais que devem ser parte do cluster NFS para NW1
         1. Abra o balanceador de carga, selecione pools de back-end e clique em Adicionar
         1. Insira o nome do novo pool de back-end (por exemplo **, NW1-backend**)
         1. Clique em adicionar uma máquina virtual
         1. Selecione o conjunto de disponibilidade que você criou anteriormente
         1. Selecionar as máquinas virtuais do cluster NFS
         1. Clique em OK
      1. Conectado a interfaces de rede primárias de todas as máquinas virtuais que devem ser parte do cluster NFS para NW2
         * Repita as etapas acima para criar um pool de back-end para NW2
   1. Criar as investigações de integridade
      1. Porta 61000 para NW1
         1. Abra o balanceador de carga, selecione investigações de integridade e clique em Adicionar
         1. Insira o nome da nova investigação de integridade (por exemplo **NW1-HP**)
         1. Selecione TCP como protocolo, porta 610**00**, manter intervalo 5 e limite não íntegro 2
         1. Clique em OK
      1. Porta 61001 para NW2
         * Repita as etapas acima para criar uma investigação de integridade para NW2
   1. Regras de balanceamento de carga
      1. 2049 TCP para NW1
         1. Abra o balanceador de carga, selecione regras de balanceamento de carga e clique em Adicionar
         1. Insira o nome da nova regra do balanceador de carga (por exemplo **, NW1-lb-2049**)
         1. Selecione o endereço IP de front-end, o pool de back-ends e a investigação de integridade que você criou anteriormente (por exemplo **NW1-frontend**)
         1. Mantenha o protocolo **TCP**, insira a porta **2049**
         1. Aumentar o tempo limite de ociosidade para 30 minutos
         1. **Certifique-se de habilitar o IP flutuante**
         1. Clique em OK
      1. 2049 UDP para NW1
         * Repita as etapas acima para a porta 2049 e UDP para NW1
      1. 2049 TCP para NW2
         * Repita as etapas acima para a porta 2049 e TCP para NW2
      1. 2049 UDP para NW2
         * Repita as etapas acima para a porta 2049 e UDP para NW2

> [!IMPORTANT]
> Não habilite carimbos de data/hora TCP em VMs do Azure colocadas por trás Azure Load Balancer. Habilitar carimbos de data/hora TCP fará com que as investigações de integridade falhem. Defina o parâmetro **net. IPv4. TCP _timestamps** como **0**. Para obter detalhes, consulte [Load Balancer investigações de integridade](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Criar cluster pacemaker

Siga as etapas em [Configurando pacemaker em SuSE Linux Enterprise Server no Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster pacemaker básico para este servidor NFS.

### <a name="configure-nfs-server"></a>Configurar servidor NFS

Os itens a seguir são prefixados com **[A]** -aplicável a todos os nós **[1]** -aplicável somente ao nó 1 ou **[2]** – aplicável somente ao nó 2.

1. **[A]** configurar resolução de nome de host

   Você pode usar um servidor DNS ou modificar o/etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo/etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Insira as linhas a seguir para/etc/hosts. Alterar o endereço IP e o nome do host para corresponder ao seu ambiente
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** habilitar servidor NFS

   Criar a entrada de exportação do NFS raiz

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** instalar componentes do DRBD

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** criar uma partição para os dispositivos DRBD

   Listar todos os discos de dados disponíveis

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

1. **[A]** criar configurações LVM

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

1. **[A]** configurar o DRBD

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Verifique se o arquivo DRBD. conf contém as duas linhas a seguir

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Alterar a configuração global de DRBD

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Adicione as entradas a seguir à seção Handler e net.

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

1. **[A]** criar os dispositivos NFS DRBD

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Insira a configuração para o novo dispositivo DRBD e saia

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

   Insira a configuração para o novo dispositivo DRBD e saia

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

   Criar o dispositivo DRBD e iniciá-lo

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** ignorar sincronização inicial

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** definir o nó primário

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** aguarde até que os novos dispositivos DRBD sejam sincronizados

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** criar sistemas de arquivos nos dispositivos DRBD

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

1. **[A]** instalação DRBD de detecção de divisão-Brain

   Ao usar DRBD para sincronizar dados de um host para outro, é possível que a chamada de divisão Brain possa ocorrer. Uma divisão Brain é um cenário em que ambos os nós de cluster promoveram o dispositivo DRBD para ser o primário e não foram sincronizados. Pode ser uma situação rara, mas você ainda deseja manipular e resolver uma divisão de cabeça o mais rápido possível. Portanto, é importante ser notificado quando ocorreu um cérebro de divisão.

   Leia [a documentação oficial do DRBD](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) sobre como configurar uma notificação de divisão Brain.

   Também é possível recuperar automaticamente de um cenário de divisão Brain. Para obter mais informações, leia [políticas de recuperação de divisão Brain automáticas](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Configurar a estrutura do cluster

1. **[1]** adicionar os dispositivos NFS DRBD para o sistema SAP NW1 à configuração do cluster

   > [!IMPORTANT]
   > Testes recentes revelaram situações em que o netcat para de responder às solicitações devido à pendência e sua limitação de manipular apenas uma conexão. O recurso netcat para de escutar as solicitações do Azure Load Balancer e o IP flutuante fica indisponível.  
   > Para clusters pacemaker existentes, é recomendável substituir netcat por socat, seguindo as instruções em [proteção de detecção do balanceador de carga do Azure](https://www.suse.com/support/kb/doc/?id=7024128). Observe que a alteração exigirá um breve tempo de inatividade.  

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
     params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:<b>61000</b>,backlog=10,fork,reuseaddr /dev/null" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** adicionar os dispositivos NFS DRBD para o sistema SAP NW2 à configuração do cluster

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
     params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:<b>61001</b>,backlog=10,fork,reuseaddr /dev/null" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]** desabilitar o modo de manutenção
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Passos seguintes

* [Instalar o SAP ASCS e o banco de dados](high-availability-guide-suse.md)
* [Planejamento e implementação de máquinas virtuais do Azure para SAP][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA no Azure (instâncias grandes), consulte [alta disponibilidade e recuperação de desastre do SAP Hana (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA em VMs do Azure, consulte [alta disponibilidade de SAP Hana em VMS (máquinas virtuais) do Azure][sap-hana-ha]
