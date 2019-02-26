---
title: Elevada disponibilidade do SAP HANA em VMs do Azure no SUSE Linux Enterprise Server | Documentos da Microsoft
description: Elevada disponibilidade do SAP HANA em VMs do Azure no SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 0f5de24d42ccc930a4746251b9f466f241c3508e
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806713"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Elevada disponibilidade do SAP HANA em VMs do Azure no SUSE Linux Enterprise Server

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
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

Para o desenvolvimento no local, pode utilizar qualquer um dos HANA System Replication ou utilizar o armazenamento partilhado para estabelecer a elevada disponibilidade para SAP HANA.
Nas máquinas virtuais do Azure (VMs), HANA System Replication no Azure está atualmente que a única função de elevada disponibilidade de suporte. SAP HANA replicação consiste num nó primário e pelo menos um nó secundário. As alterações dos dados no nó principal são replicadas para o nó secundário de forma síncrona ou assíncrona.

Este artigo descreve como implementar e configurar as máquinas virtuais, instalar o framework de cluster e instalar e configurar a replicação de sistema do SAP HANA.
As configurações de exemplo, comandos de instalação, o número de instâncias **03**e o ID de sistema HANA **HN1** são utilizados.

Leia primeiro o SAP Notes e os documentos seguintes:

* A nota SAP [1928533], que tem:
  * A lista de tamanhos de VM do Azure que são suportados para a implementação de software da SAP.
  * Informações de capacidade importante para os tamanhos de VM do Azure.
  * O software suportado do SAP e sistema operativo (SO) e combinações de base de dados.
  * A versão de kernel SAP necessária para Windows e Linux no Microsoft Azure.
* A nota SAP [2015553] lista os pré-requisitos para implementações de software SAP suportadas para SAP no Azure.
* A nota SAP [2205917] recomendado configurações de SO para o SUSE Linux Enterprise Server para aplicações SAP.
* A nota SAP [1944799] tem diretrizes do SAP HANA para SUSE Linux Enterprise Server para aplicações SAP.
* A nota SAP [2178632] tem informações detalhadas sobre todas as métricas de monitorização que são enviadas para o SAP no Azure.
* A nota SAP [2191498] tem a versão necessária do agente de anfitrião do SAP para o Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento de SAP no Linux no Azure.
* A nota SAP [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A nota SAP [1999351] tem informações adicionais de resolução de problemas avançada de monitorização a extensão do Azure para SAP.
* A nota SAP [401162] tem informações sobre como evitar "endereço já está em utilização" ao configurar o HANA System Replication.
* [WIKI de Comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas de SAP necessária para Linux.
* [SAP HANA certificadas plataformas IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Máquinas de virtuais de planeamento e implementação para o SAP no Linux do Azure] [ planning-guide] guia.
* [Implementação de máquinas virtuais do Azure para SAP no Linux] [ deployment-guide] (Este artigo).
* [Implementação de DBMS de máquinas virtuais do Azure para SAP no Linux] [ dbms-guide] guia.
* [SUSE Linux Enterprise Server para o SAP aplicativos 12 SP3 melhores práticas guias][sles-for-sap-bp]
  * Configurando um SAP HANA SR desempenho com otimização de infraestrutura (SLES para SAP aplicativos 12 SP1). O guia contém todas as informações necessárias para configurar a replicação do sistema do SAP HANA para desenvolvimento no local. Utilize este guia como uma linha de base.
  * Configurando um SAP HANA SR custo com otimização de infraestrutura (SLES para SAP aplicativos 12 SP1)

## <a name="overview"></a>Descrição geral

Para assegurar elevada disponibilidade, o SAP HANA está instalado em duas máquinas virtuais. Os dados são replicados através do HANA System Replication.

![Descrição geral de elevada disponibilidade do SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

Utiliza a configuração do SAP HANA System Replication endereços um nome de anfitrião virtual dedicado e virtual IP. No Azure, um balanceador de carga é necessário utilizar um endereço IP virtual. A lista seguinte mostra a configuração de Balanceador de carga:

* Configuração de front-end: Endereço IP 10.0.0.13 para hn1-db
* Configuração de back-end: Ligado a interfaces de rede primário de todas as máquinas virtuais que devem fazer parte do HANA System Replication
* Porta de pesquisa: Port 62503
* Regras de balanceamento de carga: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Implementar para Linux

O agente de recursos para o SAP HANA está incluído no SUSE Linux Enterprise Server para aplicações SAP.
O Azure Marketplace contém uma imagem para o SUSE Linux Enterprise Server para 12 de aplicações SAP que pode utilizar para implementar novas máquinas virtuais.

### <a name="deploy-with-a-template"></a>Implementar com um modelo

Pode utilizar um dos modelos de início rápido estão disponíveis no GitHub para implementar todos os recursos necessários. O modelo implementa as máquinas virtuais, o Balanceador de carga, o conjunto de disponibilidade e assim por diante.
Para implementar o modelo, siga estes passos:

1. Abra o [modelo de base de dados] [ template-multisid-db] ou o [convergido modelo] [ template-converged] no portal do Azure. 
    O modelo de base de dados cria as regras de balanceamento de carga para apenas uma base de dados. O modelo convergido também cria as regras de balanceamento de carga para um ASCS/SCS e a instância ERS (apenas Linux). Se planear instalar um sistema baseado em SAP NetWeaver e que pretende instalar a instância do ASCS/SCS nos mesmos computadores, utilize o [modelo de convergência][template-converged].

1. Introduza os seguintes parâmetros:
    - **ID de sistema de SAP**: Introduza o ID de sistema SAP do sistema SAP que pretende instalar. O ID é utilizado como um prefixo para os recursos que são implementados.
    - **Tipo de pilha**: (Este parâmetro só é aplicável se utilizar o modelo convergido.) Selecione o tipo de pilha do SAP NetWeaver.
    - **Tipo de SO**: Selecione uma das distribuições de Linux. Para este exemplo, selecione **SLES 12**.
    - **Tipo de BD**: Selecione **HANA**.
    - **Tamanho do sistema de SAP**: Introduza o número de SAPS que irá fornecer o novo sistema. Se não tiver certeza SAPS quantos requer que o sistema, peça ao seu parceiro de tecnologia de SAP ou integrador de sistemas.
    - **Disponibilidade do sistema**: Selecione **HA**.
    - **Nome de utilizador administrador e a palavra-passe de administrador**: Um novo utilizador é criado que pode ser utilizado para iniciar sessão máquina.
    - **Sub-rede nova ou existente**: Determina se devem ser criadas uma nova rede virtual e uma sub-rede ou uma sub-rede existente utilizado. Se já tiver uma rede virtual que está ligada à sua rede no local, selecione **existentes**.
    - **ID de sub-rede**: Se pretender implementar a VM para uma VNet já existente em que tem uma sub-rede definida a VM deve ser atribuída para nomear o ID dessa sub-rede. O ID, normalmente, é semelhante **/subscriptions/\<ID da subscrição > /resourceGroups/\<nome do grupo de recursos > /providers/Microsoft.Network/virtualNetworks/\<nome da rede virtual > /subnets/ \<nome da sub-rede >**.

### <a name="manual-deployment"></a>Implementação manual

> [!IMPORTANT]
> Certifique-se de que o sistema operacional seleciona é SAP certificado para SAP HANA em determinados tipos VM que está a utilizar. A lista de SAP HANA certificadas de tipos de VM e o sistema operacional libera para aqueles podem ser pesquisados [plataformas de IaaS com certificação do SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Certifique-se de clicar em detalhes sobre o tipo de VM listados para obter a lista completa de SAP HANA suportada versões do SO para o tipo VM específico
>  

1. Crie um grupo de recursos.
1. Crie uma rede virtual
1. Crie um conjunto de disponibilidade.
   - Defina o domínio de atualização máx.
1. Crie um balanceador de carga (interno).
   - Selecione a rede virtual criada no passo 2.
1. Crie máquina virtual 1.
   - Utilize uma imagem de SLES4SAP na galeria do Azure é suportada para o SAP HANA no tipo de VM que selecionou.
   - Selecione o conjunto de disponibilidade criado na etapa 3.
1. Crie a máquina virtual 2.
   - Utilize uma imagem de SLES4SAP na galeria do Azure é suportada para o SAP HANA no tipo de VM que selecionou.
   - Selecione o conjunto de disponibilidade criado na etapa 3. 
1. Adicione discos de dados.
1. Configure o Balanceador de carga. Primeiro, crie um conjunto IP de front-end:

   1. Abra o Balanceador de carga, selecione **conjunto IP de front-end**e selecione **Add**.
   1. Introduza o nome do novo conjunto IP Front-end (por exemplo, **hana-front-end**).
   1. Definir o **atribuição** ao **estático** e introduza o endereço IP (por exemplo, **10.0.0.13**).
   1. Selecione **OK**.
   1. Depois de criar o novo conjunto IP Front-end, tome nota do endereço IP do conjunto.

1. Em seguida, crie um conjunto de back-end:

   1. Abra o Balanceador de carga, selecione **conjuntos de back-end**e selecione **Add**.
   1. Introduza o nome do novo conjunto de back-end (por exemplo, **hana-back-end**).
   1. Selecione **adicionar uma máquina virtual**.
   1. Selecione o conjunto de disponibilidade criado na etapa 3.
   1. Selecione as máquinas virtuais do cluster de SAP HANA.
   1. Selecione **OK**.

1. Em seguida, crie uma sonda de estado de funcionamento:

   1. Abra o Balanceador de carga, selecione **sondas de estado de funcionamento**e selecione **Add**.
   1. Introduza o nome da sonda de estado de funcionamento novo (por exemplo, **hana hp**).
   1. Selecione **TCP** como o protocolo e porta 625**03**. Manter o **intervalo** valor definido para 5 e o **limiar de mau estado de funcionamento** valor definido como 2.
   1. Selecione **OK**.

1. Do SAP HANA 1.0, crie as regras de balanceamento de carga:

   1. Abra o Balanceador de carga, selecione **regras de balanceamento de carga**e selecione **Add**.
   1. Introduza o nome da nova regra de Balanceador de carga (por exemplo, hana-lb-3**03**15).
   1. Selecione o endereço IP Front-end, o conjunto de back-end e a sonda de estado de funcionamento que criou anteriormente (por exemplo, **hana-front-end**).
   1. Manter o **protocolo** definida como **TCP**e introduza a porta 3**03**15.
   1. Aumentar a **tempo limite de inatividade** como 30 minutos.
   1. Certifique-se de que **ativar o IP flutuante**.
   1. Selecione **OK**.
   1. Repita estes passos para a porta 3**03**17.

1. Para o SAP HANA 2.0, crie as regras de balanceamento de carga para a base de dados do sistema:

   1. Abra o Balanceador de carga, selecione **regras de balanceamento de carga**e selecione **Add**.
   1. Introduza o nome da nova regra de Balanceador de carga (por exemplo, hana-lb-3**03**13).
   1. Selecione o endereço IP Front-end, o conjunto de back-end e a sonda de estado de funcionamento que criou anteriormente (por exemplo, **hana-front-end**).
   1. Manter o **protocolo** definida como **TCP**e introduza a porta 3**03**13.
   1. Aumentar a **tempo limite de inatividade** como 30 minutos.
   1. Certifique-se de que **ativar o IP flutuante**.
   1. Selecione **OK**.
   1. Repita estes passos para a porta 3**03**14.

1. Para o SAP HANA 2.0, primeiro crie as regras de balanceamento de carga para a base de dados do inquilino:

   1. Abra o Balanceador de carga, selecione **regras de balanceamento de carga**e selecione **Add**.
   1. Introduza o nome da nova regra de Balanceador de carga (por exemplo, hana-lb-3**03**40).
   1. Selecione o endereço IP de front-end, o conjunto de back-end e a sonda de estado de funcionamento que criou anteriormente (por exemplo, **hana-front-end**).
   1. Manter o **protocolo** definida como **TCP**e introduza a porta 3**03**40.
   1. Aumentar a **tempo limite de inatividade** como 30 minutos.
   1. Certifique-se de que **ativar o IP flutuante**.
   1. Selecione **OK**.
   1. Repita estes passos para portas 3**03**41 e 3**03**42.

Para obter mais informações sobre as portas necessárias para o SAP HANA, leia o capítulo [ligações às bases de dados do inquilino](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) no [bases de dados do SAP HANA inquilinos](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) guia ou [2388694 de nota SAP][2388694].


## <a name="create-a-pacemaker-cluster"></a>Criar um cluster de Pacemaker

Siga os passos em [Pacemaker no SUSE Linux Enterprise Server no Azure a configurar](high-availability-guide-suse-pacemaker.md) para criar um cluster de Pacemaker básico para este servidor do HANA. Pode utilizar o mesmo cluster Pacemaker para SAP HANA e SAP NetWeaver (A) SCS.

## <a name="install-sap-hana"></a>Instalar o SAP HANA

Os passos nesta secção, utilize os prefixos seguintes:
- **[A]**: A etapa se aplica a todos os nós.
- **[1]**: A etapa se aplica ao nó 1 apenas.
- **[2]**: A etapa se aplica a 2 de nó do cluster Pacemaker apenas.

1. **[A]**  Configurar o layout de disco: **Gestor de volumes lógicos (LVM)**.

   Recomendamos que utilize LVM para volumes que armazenam dados e ficheiros de registo. O exemplo seguinte parte do princípio de que as máquinas virtuais tem quatro discos de dados que ligados, que são utilizados para criar dois volumes.

   Liste todos os discos disponíveis:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Exemplo de saída:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Crie volumes físicos para todos os discos que pretende utilizar:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Crie um grupo de volume para os ficheiros de dados. Utilize um grupo de volumes para os ficheiros de registo e outro para o diretório partilhado do SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Crie os volumes lógicos. Um volume linear é criado quando utiliza `lvcreate` sem o `-i` mudar. Sugerimos que crie um volume repartido para melhorar o desempenho de e/s, onde o `-i` argumento deve ser o número do volume físico subjacente. Neste documento, os dois volumes físicos são utilizados para o volume de dados, pelo que a `-i` argumento de comutador é definido como **2**. Um volume físico é utilizado para o volume de registo, por isso, não `-i` explicitamente é utilizado o comutador. Utilizar o `-i` mudar e defini-lo para o número do volume físico subjacente, quando usa mais de um volume físico para cada um dos dados, registo ou volumes partilhados.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Criar os diretórios de montagem e copie o UUID de todos os volumes de lógicos:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Criar `fstab` entradas para os volumes de lógicas de três:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Insira a seguinte linha no `/etc/fstab` ficheiro:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Os novos volumes de montagem:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]**  Configurar o layout de disco: **Discos simples**.

   Para sistemas de demonstração, pode colocar os ficheiros de dados e de registo HANA num disco. Cria uma partição em /dev/disk/azure/scsi1/lun0 e formatá-lo com xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Insira esta linha no ficheiro /etc/fstab.:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Criar o diretório de destino e Monte o disco:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]**  Configurar a resolução de nomes de anfitrião para todos os anfitriões.

   Pode utilizar um servidor DNS ou modificar o ficheiro /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Inserir as linhas seguintes no ficheiro /etc/hosts. Altere o endereço IP e o nome de anfitrião para corresponder ao seu ambiente:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]**  Instalar os pacotes de elevada disponibilidade do SAP HANA:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Para instalar o SAP HANA System Replication, siga o capítulo 4 o [Guia do cenário de otimização de desempenho do SAP HANA SR](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]**  Executar o **hdblcm** programa a partir do DVD do HANA. Introduza os seguintes valores na linha de comandos:
   * Escolha a instalação: Enter **1**.
   * Selecione os componentes adicionais para a instalação: Enter **1**.
   * Introduza o caminho de instalação [/ hana/partilhado]: Selecione introduzir.
   * Introduza o nome de anfitrião Local [.]: Selecione introduzir.
   * Deseja adicionar anfitriões adicionais no sistema? (y/n) [n]: Selecione introduzir.
   * Introduza o ID de sistema do SAP HANA: Introduza o SID do HANA, por exemplo: **HN1**.
   * Introduza o número de instância [00]: Introduza o número de instância HANA. Introduza **03** se utilizar o modelo do Azure ou seguido a secção de implementação manual deste artigo.
   * Selecione o modo de base de dados / introduza índice [1]: Selecione introduzir.
   * Selecionar a utilização do sistema / introduza índice [4]: Selecione o valor de utilização do sistema.
   * Introduza a localização de Volumes de dados [/ hana/dados/HN1]: Selecione introduzir.
   * Introduza a localização de Volumes de registo [/ hana/registo/HN1]: Selecione introduzir.
   * Restringir a alocação de memória máxima? [n]: Selecione introduzir.
   * Introduza o nome de anfitrião do certificado para o anfitrião "..." [...]: Selecione introduzir.
   * Introduza a palavra-passe (sapadm) de utilizador do agente do anfitrião do SAP: Introduza a palavra-passe de utilizador do agente de anfitrião.
   * Confirme palavra-passe (sapadm) de utilizador do agente do anfitrião do SAP: Digite a senha de usuário do agente de anfitrião novamente para confirmar.
   * Introduza o administrador de sistema (hdbadm) palavra-passe: Introduza a palavra-passe de administrador do sistema.
   * Confirme (hdbadm) palavra-passe de administrador de sistema: Introduza a palavra-passe de administrador do sistema novamente para confirmar.
   * Introduza o diretório de raiz de administrador de sistema [/ usr/sap/HN1/home]: Selecione introduzir.
   * Introduza o Shell de início de sessão de administrador de sistema [/ bin/sh]: Selecione introduzir.
   * Introduza o ID de utilizador de administrador de sistema [1001]: Selecione introduzir.
   * Introduza o ID do grupo de utilizadores (sapsys) [79]: Selecione introduzir.
   * Introduza a palavra-passe de utilizador (sistema) de base de dados: Introduza a palavra-passe de utilizador de base de dados.
   * Confirme palavra-passe de utilizador (sistema) de base de dados: Introduza a palavra-passe de utilizador de base de dados, novamente para confirmar.
   * Reiniciar o sistema após o reinício do computador? [n]: Selecione introduzir.
   * Pretende continuar? (y/n): Valide o resumo. Introduza **y** para continuar.

1. **[A]**  Atualize o agente de anfitrião do SAP.

   Baixe o arquivo de agente de anfitrião do SAP mais recentes a partir da [Centro de Software SAP] [ sap-swcenter] e execute o seguinte comando para atualizar o agente. Substitua o caminho para o arquivo para apontar para o ficheiro transferido:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Configurar a replicação de sistema 2.0 do SAP HANA

Os passos nesta secção, utilize os prefixos seguintes:

* **[A]**: A etapa se aplica a todos os nós.
* **[1]**: A etapa se aplica ao nó 1 apenas.
* **[2]**: A etapa se aplica a 2 de nó do cluster Pacemaker apenas.

1. **[1]**  Criar a base de dados do inquilino.

   Se estiver a utilizar o SAP HANA 2.0 ou MDC, crie uma base de dados do inquilino para o seu sistema SAP NetWeaver. Substitua **NW1** com o SID do seu sistema SAP.

   Inicie sessão como \<hanasid > adm e execute o seguinte comando:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Configurar replicação do sistema no primeiro nó:

   Inicie sessão como \<hanasid > adm e criar cópias de segurança de bases de dados:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Copie os ficheiros PKI do sistema para o site secundário:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Crie o site primário:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Configurar replicação do sistema no segundo nó:
    
   Registre-se o segundo nó para iniciar a replicação de sistema. Inicie sessão como \<hanasid > adm e execute o seguinte comando:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Configurar a replicação de sistema 1.0 do SAP HANA

Os passos nesta secção, utilize os prefixos seguintes:

* **[A]**: A etapa se aplica a todos os nós.
* **[1]**: A etapa se aplica ao nó 1 apenas.
* **[2]**: A etapa se aplica a 2 de nó do cluster Pacemaker apenas.

1. **[1]**  Criar usuários necessários.

   Inicie sessão como raiz e execute o seguinte comando. Certifique-se substituir as cadeias de caracteres em negrito (ID de sistema HANA **HN1** e o número de instâncias **03**) com os valores da sua instalação do SAP HANA:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]**  Criar a entrada de keystore.

   Inicie sessão como raiz e execute o seguinte comando para criar uma nova entrada de keystore:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]**  Cópia de segurança da base de dados.

   Inicie sessão como raiz e criar cópias de segurança de bases de dados:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Se utilizar uma instalação multi-inquilino, também fazer cópias de segurança da base de dados do inquilino:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Configurar replicação do sistema no primeiro nó.

   Inicie sessão como \<hanasid > adm e criar o site primário:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Configurar replicação do sistema no nó secundário.

   Inicie sessão como \<hanasid > adm e registe o site secundário:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Criar recursos de cluster do SAP HANA

Primeiro, crie a topologia do HANA. Execute os seguintes comandos em um de nós do cluster Pacemaker:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Em seguida, crie os recursos do HANA:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" is-managed="true" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
  params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
  op monitor timeout=20s interval=10 depth=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante no nó que os recursos estão em execução.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Teste a configuração de cluster

Esta secção descreve como pode testar a configuração. Cada teste pressupõe que está raiz e o mestre de SAP HANA está a ser executada a **hn1-db-0** máquina virtual.

### <a name="test-the-migration"></a>A migração de teste

Antes de começar o teste, certifique-se de que Pacemaker não tem qualquer ação com falhas (via crm_mon - r), não há nenhuma restrição de localização inesperados (por exemplo sobras de um teste de migração) e que HANA é o estado de sincronização, por exemplo, com SAPHanaSR showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Pode migrar do nó principal do SAP HANA, executando o seguinte comando:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Se definir `AUTOMATED_REGISTER="false"`, esta sequência de comandos deve migrar do nó principal do SAP HANA e o grupo que contém o endereço IP virtual para hn1-db-1.

Depois de fazer a migração, a saída de - r crm_mon fica assim

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

O recurso de SAP HANA no hn1-db-0 não consegue iniciar como secundária. Neste caso, configure a instância HANA como secundário ao executar este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

A migração cria as restrições de localização têm de ser eliminadas novamente:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Também terá de limpar o estado do recurso de nó secundário:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Monitorize o estado do recurso HANA usando crm_mon - r. Depois de HANA é iniciado em hn1-db-0, o resultado deverá ser semelhante a esta

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testar o agente de delimitação por barreiras do Azure (não SBD)

Pode testar a configuração do agente do Azure de delimitação por barreiras, desativando a interface de rede no nó hn1-db-0:

<pre><code>sudo ifdown eth0
</code></pre>

Agora, a máquina virtual deve reiniciar ou parar consoante a configuração de cluster.
Se definir o `stonith-action` definir como desativado, a máquina virtual é parada e os recursos são migrados para a máquina virtual em execução.

Depois de iniciar a máquina virtual novamente, o recurso de SAP HANA não for possível iniciar como secundária se definir `AUTOMATED_REGISTER="false"`. Neste caso, configure a instância HANA como secundário ao executar este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>A delimitação por barreiras SBD teste

Pode testar a configuração do SBD ao eliminar o processo de inquisitor.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

Nó de cluster hn1-db-0 deve ser reiniciado. O serviço de Pacemaker poderá não comece a utilizar, posteriormente. Certifique-se para iniciá-lo novamente.

### <a name="test-a-manual-failover"></a>Testar uma ativação pós-falha manual

Pode testar uma ativação pós-falha manual, parando o `pacemaker` serviço no nó hn1-db-0:

<pre><code>service pacemaker stop
</code></pre>

Após a ativação pós-falha, pode iniciar o serviço novamente. Se definir `AUTOMATED_REGISTER="false"`, o recurso de SAP HANA no nó hn1-db-0 não consegue iniciar como secundária. Neste caso, configure a instância HANA como secundário ao executar este comando:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>Testes SUSE

> [!IMPORTANT]
> Certifique-se de que o sistema operacional seleciona é SAP certificado para SAP HANA em determinados tipos VM que está a utilizar. A lista de SAP HANA certificadas de tipos de VM e o sistema operacional libera para aqueles podem ser pesquisados [plataformas de IaaS com certificação do SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Certifique-se de clicar em detalhes sobre o tipo de VM listados para obter a lista completa de SAP HANA suportada versões do SO para o tipo VM específico

Executa todos os casos de teste que estão listados no guia de cenário de otimização de desempenho do SAP HANA SR ou cenário de otimização de custos do SAP HANA SR, dependendo do seu caso de utilização. Pode encontrar os guias sobre o [SLES para SAP melhores página práticas][sles-for-sap-bp].

Os seguintes testes são uma cópia das descrições de teste do SAP HANA SR desempenho otimizado cenário SUSE Linux Enterprise Server para a guia SP1 de 12 de aplicações SAP. Para uma versão atualizada, leia sempre também o Guia em si. Sempre Certifique-se de que está em sincronização HANA antes de iniciar o teste e também certificar-se de que a configuração de Pacemaker está correta.

As seguintes descrições de teste partimos do pressuposto PREFER_SITE_TAKEOVER = "true" e AUTOMATED_REGISTER = "false".
NOTA: Os seguintes testes foram concebidos para ser executadas em seqüência e depende do Estado de saída dos testes anteriores.

1. TESTE 1: PARAR PRINCIPAL DA BASE DE DADOS NO NÓ 1

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Execute os seguintes comandos como \<hanasid > adm no nó hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker deve detetar a instância HANA de parada e a ativação pós-falha no outro nó. Depois de fazer a ativação pós-falha, a instância do HANA no nó hn1-db-0 está parada porque Pacemaker não registra automaticamente o nó como secundário do HANA.

   Execute os seguintes comandos para registar o nó hn1-db-0, como secundário e a limpeza do recurso falhou.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TESTE 2: PARAR PRINCIPAL DA BASE DE DADOS NO NÓ 2

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Execute os seguintes comandos como \<hanasid > adm no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker deve detetar a instância HANA de parada e a ativação pós-falha no outro nó. Depois de fazer a ativação pós-falha, a instância do HANA no nó hn1-db-1 está parada porque Pacemaker não registra automaticamente o nó como secundário do HANA.

   Execute os seguintes comandos para registar o nó hn1-db-1, como secundário e a limpeza do recurso falhou.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TESTE 3: PRIMÁRIO FALHA NO NÓ DE BASE DE DADOS

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Execute os seguintes comandos como \<hanasid > adm no nó hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker deve detetar a instância HANA e ativação pós-falha para outro nó cancelado. Depois de fazer a ativação pós-falha, a instância do HANA no nó hn1-db-0 está parada porque Pacemaker não registra automaticamente o nó como secundário do HANA.

   Execute os seguintes comandos para registar o nó hn1-db-0, como secundário e a limpeza do recurso falhou.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TESTE 4: FALHA PRINCIPAL DA BASE DE DADOS NO NÓ 2

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Execute os seguintes comandos como \<hanasid > adm no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker deve detetar a instância HANA e ativação pós-falha para outro nó cancelado. Depois de fazer a ativação pós-falha, a instância do HANA no nó hn1-db-1 está parada porque Pacemaker não registra automaticamente o nó como secundário do HANA.

   Execute os seguintes comandos para registar o nó hn1-db-1, como secundário e a limpeza do recurso falhou.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TESTE 5: FALHA DE NÓ DE SITE PRIMÁRIO (NÓ 1)

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Execute os seguintes comandos como raiz no nó hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker deve detetar o nó de cluster cancelado e fence o nó. Assim que o nó for bloqueado, Pacemaker irá disparar uma obtenção de controlo da instância do HANA. Quando o nó vedado for reinicializado, Pacemaker não será iniciada automaticamente.

   Execute os seguintes comandos para iniciar Pacemaker, limpeza as mensagens SBD para nó hn1-db-0, registe-se nó hn1-db-0 como secundário e a limpeza do recurso falhou.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. 6 DE TESTE: FALHA DE NÓ DE SITE SECUNDÁRIO (NÓ 2)

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Execute os seguintes comandos como raiz no nó hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker deve detetar o nó de cluster cancelado e fence o nó. Assim que o nó for bloqueado, Pacemaker irá disparar uma obtenção de controlo da instância do HANA. Quando o nó vedado for reinicializado, Pacemaker não será iniciada automaticamente.

   Execute os seguintes comandos para iniciar Pacemaker, limpeza as mensagens SBD para nó hn1-db-1, registe-se nó hn1-db-1 como secundário e a limpeza do recurso falhou.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. 7 DE TESTE: PARAR A SECUNDÁRIA DA BASE DE DADOS NO NÓ 2

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Execute os seguintes comandos como \<hanasid > adm no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker detectará a instância HANA parada e marcar o recurso como falhado no nó hn1-db-1. Pacemaker automaticamente deve reiniciar a instância do HANA. Execute o seguinte comando para limpar o estado de falha.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. 8 DE TESTE: FALHA SECUNDÁRIO DA BASE DE DADOS NO NÓ 2

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Execute os seguintes comandos como \<hanasid > adm no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker detectará a instância HANA cancelada e marcar o recurso como falhado no nó hn1-db-1. Execute o seguinte comando para limpar o estado de falha. Pacemaker automaticamente deve, em seguida, reinicie a instância do HANA.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. 9 DE TESTE: BASE DE DADOS DO SITE SECUNDÁRIO EM EXECUÇÃO DE NÓ (NÓ 2) HANA SECUNDÁRIO DE FALHAS

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Execute os seguintes comandos como raiz no nó hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker deve detetar o nó de cluster cancelado e fence o nó. Quando o nó vedado for reinicializado, Pacemaker não será iniciada automaticamente.

   Execute os seguintes comandos para iniciar Pacemaker, limpar as mensagens SBD para nó hn1-db-1 e limpeza do recurso falhou.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Passos Seguintes

* [Máquinas de virtuais de planeamento e implementação de SAP do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a elevada disponibilidade e o plano de recuperação após desastre do SAP HANA no Azure (instâncias grandes), veja [SAP HANA (instâncias grandes) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md)
