---
title: Alta disponibilidade para NFS em VMs Azure em SLES Microsoft Docs
description: Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 6b0504f5e4199ee3cd8e86660b866fddf2568485
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608576"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server

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

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura do cluster e instalar um servidor NFS altamente disponível que pode ser usado para armazenar os dados partilhados de um sistema SAP altamente disponível.
Este guia descreve como configurar um servidor NFS altamente disponível que é utilizado por dois sistemas SAP, NW1 e NW2. Os nomes dos recursos (por exemplo, máquinas virtuais, redes virtuais) no exemplo assumem que utilizou o modelo do [servidor de ficheiros SAP][template-file-server] com **prefixo** de recursos .


> [!NOTE]
> Este artigo contém referências aos termos *escravo* e *mestre,* termos que a Microsoft já não utiliza. Quando os termos forem removidos do software, removê-los-emos deste artigo.

Leia primeiro as seguintes notas e artigos SAP

* Nota SAP [1928533,]que tem:
  * Lista de tamanhos Azure VM que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade dos tamanhos Azure VM
  * Combinações suportadas de software SAP e sistema operativo (OS) e de base de dados
  * Versão kernel SAP necessária para Windows e Linux no Microsoft Azure

* O SAP Note [2015553] lista pré-requisitos para implementações de software SAP suportadas pelo SAP em Azure.
* SAP Nota [2205917] recomendou definições de SO para SUSE Linux Enterprise Server para aplicações SAP
* SAP Nota [1944799] tem Diretrizes SAP HANA para SUSE Linux Enterprise Server para aplicações SAP
* A Nota [SAP 2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* Sap Note [2191498] tem a versão necessária do Agente anfitrião SAP para Linux em Azure.
* Sap Nota [2243692] tem informações sobre o licenciamento SAP em Linux em Azure.
* Sap Note [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* Sap Nota [1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização melhorada de Azure para o SAP.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [Azure Virtual Machines planejamento e implementação para SAP em Linux][planning-guide]
* [Azure Virtual Machines implantação para SAP em Linux (este artigo)][deployment-guide]
* [Azure Virtual Machines DBMS implantação para SAP em Linux][dbms-guide]
* [SUSE Linux Enterprise Extensão de Alta Disponibilidade 12 GUIAs de boas práticas SP3][sles-hae-guides]
  * Armazenamento NFS altamente disponível com DRBD e Pacemaker
* [SUSE Linux Enterprise Server para aplicações SAP 12 GUIAs de boas práticas SP3][sles-for-sap-bp]
* [SUSE Extensão de Alta Disponibilidade 12 Notas de lançamento SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Descrição Geral

Para obter uma elevada disponibilidade, o SAP NetWeaver necessita de um servidor NFS. O servidor NFS está configurado num cluster separado e pode ser utilizado por vários sistemas SAP.

![Visão geral de alta disponibilidade do SAP NetWeaver](./media/high-availability-guide-nfs/ha-suse-nfs.png)

O servidor NFS utiliza um nome de anfitrião virtual dedicado e endereços IP virtuais para todos os sistemas SAP que utilizam este servidor NFS. No Azure, é necessário um equilibrador de carga para utilizar um endereço IP virtual. A lista a seguir mostra a configuração do equilibrador de carga.        

* Configuração frontend
  * Endereço IP 10.0.0.4 para NW1
  * Endereço IP 10.0.0.5 para NW2
* Configuração de backend
  * Ligado às interfaces de rede primária de todas as máquinas virtuais que devem fazer parte do cluster NFS
* Porto de Sonda
  * Porto 61000 para NW1
  * Porto 61001 para NW2
* Regras de equilíbrio de carga (se utilizar o balanceador de carga de base)
  * TCP de 2049 para NW1
  * UDP 2049 para NW1
  * TCP de 2049 para NW2
  * UDP 2049 para NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Configurar um servidor NFS altamente disponível

Você pode usar um modelo Azure do GitHub para implementar todos os recursos Azure necessários, incluindo as máquinas virtuais, conjunto de disponibilidade e balanceador de carga ou você pode implementar os recursos manualmente.

### <a name="deploy-linux-via-azure-template"></a>Implementar Linux via Modelo Azure

O Azure Marketplace contém uma imagem para SUSE Linux Enterprise Server para aplicações SAP 12 que pode utilizar para implantar novas máquinas virtuais.
Você pode usar um dos modelos de arranque rápido no GitHub para implementar todos os recursos necessários. O modelo implementa as máquinas virtuais, o balanceador de carga, o conjunto de disponibilidade, etc. Siga estes passos para implementar o modelo:

1. Abra o modelo do [servidor de ficheiros SAP][template-file-server] no portal Azure   
1. Introduza os seguintes parâmetros
   1. Prefixo de recursos  
      Introduza o prefixo que pretende utilizar. O valor é usado como prefixo para os recursos que são implantados.
   2. Contagem do Sistema SAP  
      Introduza o número de sistemas SAP que utilizarão este servidor de ficheiros. Isto irá implementar a quantidade necessária de configurações frontend, regras de equilíbrio de carga, portas de sonda, discos, etc.
   3. Tipo de Os  
      Selecione uma das distribuições do Linux. Para este exemplo, selecione SLES 12
   4. Nome de utilizador admin e senha de administração  
      É criado um novo utilizador que pode ser utilizado para iniciar sessão na máquina.
   5. ID da sub-rede  
      Se pretender colocar o VM num VNet existente onde tenha uma sub-rede definida, o VM deve ser atribuído, nomeie o ID dessa sub-rede específica. O ID geralmente parece /subscrições/**&lt; subscrição &gt; ID**/resourceGroups/**&lt; &gt; nome do grupo de recursos**/fornecedores/Microsoft.Network/virtualNetworks/ virtual **&lt; Network/virtual Network name &gt;**/subnets/**&lt; subnets/ subnet name &gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Implementar o Linux manualmente através do portal Azure

Primeiro, é necessário criar as máquinas virtuais para este cluster NFS. Em seguida, cria-se um equilibrador de carga e utiliza-se as máquinas virtuais nas piscinas de backend.

1. Criar um Grupo de Recursos
1. Criar uma Rede Virtual
1. Criar um conjunto de disponibilidade  
   Definir domínio de atualização máxima
1. Criar Máquina Virtual 1 Utilize pelo menos sLES4SAP 12 SP3, neste exemplo é utilizada a imagem SLES4SAP 12 SP3 BYOS SLES For SAP Applications 12 SP3 (BYOS)  
   Selecione Conjunto de Disponibilidade criado anteriormente  
1. Criar Máquina Virtual 2 Utilize pelo menos sLES4SAP 12 SP3, neste exemplo a imagem SLES4SAP 12 SP3 BYOS  
   SLES Para Aplicações SAP 12 SP3 (BYOS) é usado  
   Selecione Conjunto de Disponibilidade criado anteriormente  
1. Adicione um disco de dados para cada sistema SAP em ambas as máquinas virtuais.
1. Criar um equilibrador de carga (interno). Recomendamos o [balanceador de carga padrão.](../../../load-balancer/load-balancer-overview.md)  
   1. Siga estas instruções para criar o balanceador de carga padrão:
      1. Criar os endereços IP frontend
         1. Endereço IP 10.0.0.4 para NW1
            1. Abra o equilibrador de carga, selecione o pool IP frontal e clique em Adicionar
            1. Insira o nome do novo pool IP frontend (por **exemplo, nw1-frontend**)
            1. Desaponda a Estática e introduza o endereço IP (por exemplo **10.0.0.4**)
            1. Clique em OK
         1. Endereço IP 10.0.0.5 para NW2
            * Repita os passos acima para NW2
      1. Criar as piscinas de backend
         1. Ligado às interfaces de rede primária de todas as máquinas virtuais que devem fazer parte do cluster NFS
            1. Abra o balançador de carga, selecione piscinas de backend e clique em Adicionar
            1. Insira o nome da nova piscina de backend (por **exemplo, nw-backend)**
            1. Selecione Rede Virtual
            1. Clique Em Adicionar uma máquina virtual
            1. Selecione as máquinas virtuais do cluster NFS e os seus endereços IP.
            1. Clique em Adicionar.
      1. Criar as sondas de saúde
         1. Porto 61000 para NW1
            1. Abra o equilibrador de carga, selecione sondas de saúde e clique em Adicionar
            1. Insira o nome da nova sonda de saúde (por **exemplo, nw1-hp)**
            1. Selecione TCP como protocolo, porta 610 **00**, mantenha o Intervalo 5 e o limiar insalubre 2
            1. Clique em OK
         1. Porto 61001 para NW2
            * Repita os passos acima para criar uma sonda de saúde para NW2
      1. Regras de equilíbrio de carga
         1. Abra o balançador de carga, selecione regras de equilíbrio de carga e clique em Adicionar
         1. Introduza o nome da nova regra do balançador de carga (por **exemplo, nw1-lb)**
         1. Selecione o endereço IP frontend, o backend pool e a sonda de saúde que criou anteriormente (por **exemplo, o frontend de 1** frente . **nw-backend** e **nw1-hp**)
         1. Selecione **portas HA**.
         1. Aumente o tempo de inatividade para 30 minutos
         1. **Certifique-se de ativar o IP flutuante**
         1. Clique em OK
         * Repita os passos acima para criar regra de equilíbrio de carga para NW2
   1. Em alternativa, se o seu cenário necessitar de um equilibrador de carga básico, siga estas instruções:
      1. Criar os endereços IP frontend
         1. Endereço IP 10.0.0.4 para NW1
            1. Abra o equilibrador de carga, selecione o pool IP frontal e clique em Adicionar
            1. Insira o nome do novo pool IP frontend (por **exemplo, nw1-frontend**)
            1. Desaponda a Estática e introduza o endereço IP (por exemplo **10.0.0.4**)
            1. Clique em OK
         1. Endereço IP 10.0.0.5 para NW2
            * Repita os passos acima para NW2
      1. Criar as piscinas de backend
         1. Ligado às interfaces de rede primária de todas as máquinas virtuais que devem fazer parte do cluster NFS
            1. Abra o balançador de carga, selecione piscinas de backend e clique em Adicionar
            1. Insira o nome da nova piscina de backend (por **exemplo, nw-backend)**
            1. Clique Em Adicionar uma máquina virtual
            1. Selecione o Conjunto de Disponibilidade que criou anteriormente
            1. Selecione as máquinas virtuais do cluster NFS
            1. Clique em OK
      1. Criar as sondas de saúde
         1. Porto 61000 para NW1
            1. Abra o equilibrador de carga, selecione sondas de saúde e clique em Adicionar
            1. Insira o nome da nova sonda de saúde (por **exemplo, nw1-hp)**
            1. Selecione TCP como protocolo, porta 610 **00**, mantenha o Intervalo 5 e o limiar insalubre 2
            1. Clique em OK
         1. Porto 61001 para NW2
            * Repita os passos acima para criar uma sonda de saúde para NW2
      1. Regras de equilíbrio de carga
         1. TCP de 2049 para NW1
            1. Abra o equilibrador de carga, selecione regras de equilíbrio de carga e clique em Adicionar
            1. Insira o nome da nova regra do balançador de carga (por **exemplo, nw1-lb-2049)**
            1. Selecione o endereço IP frontend, o backend pool e a sonda de saúde que criou anteriormente (por **exemplo, o frontend de 1** frente)
            1. Manter o protocolo **TCP,** entrar na porta **2049**
            1. Aumente o tempo de inatividade para 30 minutos
            1. **Certifique-se de ativar o IP flutuante**
            1. Clique em OK
         1. UDP 2049 para NW1
            * Repita os passos acima para o porto 2049 e UDP para NW1
         1. TCP de 2049 para NW2
            * Repita os passos acima para a porta 2049 e TCP para NW2
         1. UDP 2049 para NW2
            * Repita os passos acima para o porto 2049 e UDP para NW2

> [!IMPORTANT]
> O IP flutuante não é suportado numa configuração IP secundária do NIC em cenários de equilíbrio de carga. Para mais detalhes consulte [as limitações do balançador de carga Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se precisar de um endereço IP adicional para o VM, implante um segundo NIC.  

> [!Note]
> Quando os VMs sem endereços IP públicos forem colocados no pool de backend de saldos de carga standard Azure (sem endereço IP público), não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos. Para obter detalhes sobre como alcançar a conectividade de saída, consulte [a conectividade do ponto final público para máquinas virtuais utilizando o Azure Standard Load Balancer em cenários de alta disponibilidade SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Não ative os cartas temporais TCP em VMs Azure colocados atrás do Balançador de Carga Azure. Permitir os tempos de TCP fará com que as sondas de saúde falhem. Definir o parâmetro **net.ipv4.tcp_timestamps** a **0**. Para mais detalhes consulte [as sondas de saúde load balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Criar cluster pacemaker

Siga os passos na configuração do [Pacemaker no SUSE Linux Enterprise Server em Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster pacemaker básico para este servidor NFS.

### <a name="configure-nfs-server"></a>Configurar servidor NFS

Os seguintes itens são prefixados com **ambos [A]** - aplicável a todos os nós, **[1]** - apenas aplicável ao nó 1 ou **[2]** - apenas aplicáveis ao nó 2.

1. **[A]** Resolução do nome do anfitrião da configuração

   Pode utilizar um servidor DNS ou modificar os /etc/anfitriões em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Insira as seguintes linhas para /etc/anfitriões. Altere o endereço IP e o nome de anfitrião para combinar com o seu ambiente
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** Ativar o servidor NFS

   Criar a raiz entrada de exportação NFS

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** Instalar componentes drbd

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Criar uma partição para os dispositivos drbd

   Listar todos os discos de dados disponíveis

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Saída de exemplo
   
   ```
   lun0  lun1
   ```

   Criar divisórias para cada disco de dados

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** Criar configurações LVM

   Listar todas as divisórias disponíveis

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Saída de exemplo
   
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

1. **[A]** Configure drbd

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Certifique-se de que o ficheiro drbd.conf contém as seguintes duas linhas

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Alterar a configuração global do drbd

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Adicione as seguintes entradas na secção de manipulador e rede.

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

1. **[A]** Criar os dispositivos drbd NFS

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Insira a configuração para o novo dispositivo drbd e saída

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

   Insira a configuração para o novo dispositivo drbd e saída

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

   Crie o dispositivo drbd e inicie-o

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Ignorar a sincronização inicial

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Definir o nó primário

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Aguarde até que os novos dispositivos drbd sejam sincronizados

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** Criar sistemas de ficheiros nos dispositivos drbd

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

1. **[A]** Configuração drbd deteção de cérebro dividido

   Ao usar drbd para sincronizar dados de um hospedeiro para outro, um cérebro chamado split pode ocorrer. Um cérebro dividido é um cenário em que ambos os nós de cluster promoveram o dispositivo drbd para ser o principal e ficaram dessincronizados. Pode ser uma situação rara, mas ainda quer lidar e resolver um cérebro dividido o mais rápido possível. Portanto, é importante ser notificado quando um cérebro dividido aconteceu.

   Leia [a documentação oficial do DRBD](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) sobre como configurar uma notificação de cérebro dividido.

   Também é possível recuperar automaticamente de um cenário cerebral dividido. Para mais informações, leia [as políticas automáticas de recuperação do cérebro dividido](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Configurar o quadro do cluster

1. **[1]** Adicione os dispositivos drbd NFS para o sistema SAP NW1 à configuração do cluster

   > [!IMPORTANT]
   > Testes recentes revelaram situações, em que o netcat deixa de responder aos pedidos devido a atrasos e à sua limitação de manuseamento de apenas uma ligação. O recurso netcat deixa de ouvir os pedidos do balançador Azure Load e o IP flutuante fica indisponível.  
   > Para os aglomerados pacemaker existentes, recomendamos no passado a substituição do netcat por socat. Atualmente recomendamos a utilização de um agente de recursos azure-lb, que faz parte dos agentes de recursos do pacote, com os seguintes requisitos de versão pacote:
   > - Para o SLES 12 SP4/SP5, a versão deve ser pelo menos agentes de recursos-4.3.018.a7fb5035-3.30.1.  
   > - Para sLES 15/15 SP1, a versão deve ser pelo menos agentes de recursos-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Note que a mudança requer breves tempos de inatividade.  
   > Para os clusters pacemaker existentes, se a configuração já foi alterada para usar socat como descrito no [Azure Load-Balancer Detection Hardening](https://www.suse.com/support/kb/doc/?id=7024128), não existe qualquer requisito para mudar imediatamente para o agente de recursos azure-lb.

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
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** Adicione os dispositivos drbd NFS para o sistema SAP NW2 à configuração do cluster

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
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   A `crossmnt` opção nos recursos de `exportfs` cluster está presente na nossa documentação para retrocompatibilidade com versões SLES mais antigas.  

1. **[1] Desativar** o modo de manutenção
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Passos seguintes

* [Instalar o SAP ASCS e a base de dados](high-availability-guide-suse.md)
* [Azure Virtual Machines planejamento e implementação para SAP][planning-guide]
* [Implantação de máquinas virtuais Azure para SAP][deployment-guide]
* [Implantação DBMS de máquinas virtuais Azure para SAP][dbms-guide]
* Para aprender a estabelecer uma elevada disponibilidade e plano para a recuperação de desastres da SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]
