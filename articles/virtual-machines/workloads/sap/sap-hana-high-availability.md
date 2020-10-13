---
title: Alta disponibilidade de SAP HANA em VMs Azure em SLES Microsoft Docs
description: Alta disponibilidade de SAP HANA em VMs Azure no SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/08/2020
ms.author: radeltch
ms.openlocfilehash: 1efa00962e63274c2cc02c8758725e5b11d70a9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89567831"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Alta disponibilidade de SAP HANA em VMs Azure no SUSE Linux Enterprise Server

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

Para o desenvolvimento no local, pode utilizar a replicação do sistema HANA ou usar armazenamento partilhado para estabelecer uma alta disponibilidade para o SAP HANA.
Nas máquinas virtuais Azure (VMs), a replicação do sistema HANA no Azure é atualmente a única função suportada de alta disponibilidade. A replicação DE SAP HANA consiste num nó primário e pelo menos um nó secundário. As alterações aos dados do nó primário são replicadas no nó secundário de forma sincronizada ou assíncronea.

Este artigo descreve como implantar e configurar as máquinas virtuais, instalar a estrutura do cluster e instalar e configurar a replicação do sistema SAP HANA.
Nas configurações de exemplo, são utilizados comandos de instalação, instância número **03**e ID **HN1** do sistema HANA.

Leia primeiro as seguintes notas e artigos SAP:

* Nota SAP [1928533,]que tem:
  * A lista de tamanhos Azure VM que são suportados para a implementação de software SAP.
  * Informações importantes sobre a capacidade dos tamanhos Azure VM.
  * O software SAP suportado, e as combinações de sistema operativo (OS) e base de dados.
  * A versão necessária para o kernel SAP para Windows e Linux no Microsoft Azure.
* O SAP Note [2015553] lista os pré-requisitos para implementações de software SAP suportadas pelo SAP em Azure.
* O SAP Note [2205917] recomendou as definições de SO para SUSE Linux Enterprise Server para aplicações SAP.
* SAP Nota [1944799] tem diretrizes SAP HANA para SUSE Linux Enterprise Server para aplicações SAP.
* A Nota [SAP 2178632] tem informações detalhadas sobre todas as métricas de monitorização que são reportadas para o SAP em Azure.
* Sap Note [2191498] tem a versão necessária do Agente anfitrião SAP para Linux em Azure.
* Sap Nota [2243692] tem informações sobre o licenciamento SAP em Linux em Azure.
* Sap Note [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* Sap Nota [1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização melhorada de Azure para o SAP.
* A NOTA [SAP 401162] tem informações sobre como evitar "endereço já em uso" ao configurar a Replicação do Sistema HANA.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [Plataformas IAAS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Azure Virtual Machines planejamento e implementação para SAP no guia Linux.][planning-guide]
* [Azure Virtual Machines implantação para SAP em Linux][deployment-guide] (este artigo).
* [Azure Virtual Machines DBMS implantação para SAP no guia Linux.][dbms-guide]
* [SUSE Linux Enterprise Server para aplicações SAP 12 GUIAs de boas práticas SP3][sles-for-sap-bp]
  * Criação de uma Infraestrutura Otimizada de Desempenho SAP HANA SR (SLES para aplicações SAP 12 SP1). O guia contém todas as informações necessárias para configurar a Replicação do Sistema SAP HANA para o desenvolvimento no local. Utilize este guia como base.
  * Criação de uma Infraestrutura Otimizada de Custo SAP HANA SR (SLES para aplicações SAP 12 SP1)

## <a name="overview"></a>Descrição geral

Para obter uma elevada disponibilidade, o SAP HANA está instalado em duas máquinas virtuais. Os dados são replicados utilizando a replicação do sistema HANA.

![Visão geral de alta disponibilidade SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

A configuração de replicação do sistema SAP HANA utiliza um nome de hospedeiro virtual dedicado e endereços IP virtuais. No Azure, é necessário um equilibrador de carga para utilizar um endereço IP virtual. A lista a seguir mostra a configuração do equilibrador de carga:

* Configuração frontal: endereço IP 10.0.0.13 para hn1-db
* Configuração traseira: Ligadas a interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte da Replicação do Sistema HANA
* Porta sonda: Porto 62503
* Regras de equilíbrio de cargas: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Implantação para Linux

O agente de recursos da SAP HANA está incluído no SUSE Linux Enterprise Server para aplicações SAP.
O Azure Marketplace contém uma imagem para SUSE Linux Enterprise Server para aplicações SAP 12 que pode utilizar para implantar novas máquinas virtuais.

### <a name="deploy-with-a-template"></a>Implementar com um modelo

Você pode usar um dos modelos de arranque rápido que estão no GitHub para implementar todos os recursos necessários. O modelo implementa as máquinas virtuais, o equilibrador de carga, o conjunto de disponibilidade, e assim por diante.
Para implementar o modelo, siga estes passos:

1. Abra o [modelo de base de dados][template-multisid-db] ou o modelo [convergente][template-converged] no portal Azure. 
    O modelo de base de dados cria as regras de equilíbrio de carga apenas para uma base de dados. O modelo convergente também cria as regras de equilíbrio de carga para uma instância ASCS/SCS e ERS (apenas Linux). Se pretender instalar um sistema baseado em SAP NetWeaver e pretender instalar a instância ASCS/SCS nas mesmas máquinas, utilize o [modelo convergente][template-converged].

1. Introduza os seguintes parâmetros:
    - **Tipo de identificação do sistema Sap**: Introduza o ID do sistema SAP do sistema SAP que pretende instalar. O ID é usado como um prefixo para os recursos que são implantados.
    - **Tipo de pilha**: (Este parâmetro só é aplicável se utilizar o modelo convergente.) Selecione o tipo de pilha SAP NetWeaver.
    - **Tipo de Os**: Selecione uma das distribuições linux. Para este exemplo, selecione **SLES 12**.
    - **Tipo Db**: Selecione **HANA**.
    - **Tamanho do sistema de**sap : Introduza o número de SAPS que o novo sistema vai fornecer. Se não tem a certeza de quantos SAPS o sistema necessita, pergunte ao seu Parceiro de Tecnologia SAP ou Integrador de Sistema.
    - **Disponibilidade do sistema**: Selecione **HA**.
    - **Nome de utilizador admin e senha de administração**: É criado um novo utilizador que pode ser utilizado para iniciar seduca na máquina.
    - **Sub-rede nova ou existente**: Determina se deve ser criada uma nova rede virtual e sub-rede ou se uma sub-rede existente. Se já tem uma rede virtual ligada à sua rede no local, selecione **Existing**.
    - **ID da sub-rede**: Se pretender colocar o VM num VNet existente onde tenha uma sub-rede definida, o VM deve ser atribuído, nomeie o ID dessa sub-rede específica. O ID geralmente parece **/subscrições/ \<subscription ID> /grupos de recursos/ \<resource group name> /fornecedores/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name> **.

### <a name="manual-deployment"></a>Implementação manual

> [!IMPORTANT]
> Certifique-se de que o sistema operativo selecionado é CERTIFICADO SAP para SAP HANA nos tipos de VM específicos que está a utilizar. A lista de tipos de VM certificados da SAP HANA e lançamentos de SO para estes podem ser analisados nas [plataformas IAAS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Certifique-se de clicar nos detalhes do tipo VM listado para obter a lista completa de versões DE OS suportadas pela SAP HANA para o tipo VM específico
>  

1. Crie um grupo de recursos.
1. Crie uma rede virtual
1. Crie um conjunto de disponibilidade.
   - Desave o domínio de atualização máxima.
1. Criar um equilibrador de carga (interno). Recomendamos o [balanceador de carga padrão.](../../../load-balancer/load-balancer-overview.md)
   - Selecione a rede virtual criada no passo 2.
1. Criar máquina virtual 1.
   - Utilize uma imagem SLES4SAP na galeria Azure que é suportada para SAP HANA no tipo VM que selecionou.
   - Selecione o conjunto de disponibilidade criado no passo 3.
1. Criar máquina virtual 2.
   - Utilize uma imagem SLES4SAP na galeria Azure que é suportada para SAP HANA no tipo VM que selecionou.
   - Selecione o conjunto de disponibilidade criado no passo 3. 
1. Adicione discos de dados.
1. Se utilizar o balanceador de carga padrão, siga estes passos de configuração:
   1. Primeiro, crie um pool IP frontal:
   
      1. Abra o balançador de carga, selecione **o pool IP frontend**e selecione **Adicionar**.
      1. Insira o nome do novo pool IP frontal (por exemplo, **hana-frontend).**
      1. Desaponda a **Estática** e introduza o endereço IP (por exemplo, **10.0.0.13**). **Static**
      1. Selecione **OK**.
      1. Após a criação do novo pool IP frontal, note o endereço IP do pool.
   
   1. Em seguida, crie uma piscina traseira:
   
      1. Abra o balançador de carga, selecione **piscinas de backend**e selecione **Adicionar**.
      1. Insira o nome da nova piscina traseira (por exemplo, **hana-backend).**
      1. Selecione **Rede Virtual**.
      1. **Selecione Adicionar uma máquina virtual**.
      1. Selecione ** Máquina virtual**.
      1. Selecione as máquinas virtuais do cluster SAP HANA e os seus endereços IP.
      1. Selecione **Adicionar**.
   
   1. Em seguida, criar uma sonda de saúde:
   
      1. Abra o equilibrador de carga, selecione **sondas de saúde**e selecione **Adicionar**.
      1. Insira o nome da nova sonda de saúde (por exemplo, **hana-hp).**
      1. Selecione **TCP** como protocolo e porta 625**03**. Mantenha o valor **de intervalo** definido para 5, e o valor do **limiar insalubre** definido para 2.
      1. Selecione **OK**.
   
   1. Em seguida, crie as regras de equilíbrio de carga:
   
      1. Abra o balançador de carga, selecione **as regras de equilíbrio de carga**e selecione **Adicionar**.
      1. Introduza o nome da nova regra do balançador de carga (por exemplo, **hana-lb**).
      1. Selecione o endereço IP frontal, o pool traseiro e a sonda de saúde que criou anteriormente (por exemplo, **hana-frontend,** **hana-backend** e **hana-hp).**
      1. Selecione **portas HA**.
      1. Aumente o **tempo de 30** minutos.
      1. Certifique-se de que ativa o **IP flutuante**.
      1. Selecione **OK**.

   > [!Note]
   > Quando os VMs sem endereços IP públicos forem colocados no pool de backend de saldos de carga standard Azure (sem endereço IP público), não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos. Para obter detalhes sobre como alcançar a conectividade de saída, consulte [a conectividade do ponto final público para máquinas virtuais utilizando o Azure Standard Load Balancer em cenários de alta disponibilidade SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

1. Em alternativa, se o seu cenário ditar a utilização do balanceador de carga básico, siga estes passos de configuração:
   1. Primeiro, crie um pool IP frontal:
   
      1. Abra o balançador de carga, selecione **o pool IP frontend**e selecione **Adicionar**.
      1. Insira o nome do novo pool IP frontal (por exemplo, **hana-frontend).**
      1. Desaponda a **Estática** e introduza o endereço IP (por exemplo, **10.0.0.13**). **Static**
      1. Selecione **OK**.
      1. Após a criação do novo pool IP frontal, note o endereço IP do pool.
   
   1. Em seguida, crie uma piscina traseira:
   
      1. Abra o balançador de carga, selecione **piscinas de backend**e selecione **Adicionar**.
      1. Insira o nome da nova piscina traseira (por exemplo, **hana-backend).**
      1. **Selecione Adicionar uma máquina virtual**.
      1. Selecione o conjunto de disponibilidade criado no passo 3.
      1. Selecione as máquinas virtuais do cluster SAP HANA.
      1. Selecione **OK**.
   
   1. Em seguida, criar uma sonda de saúde:
   
      1. Abra o equilibrador de carga, selecione **sondas de saúde**e selecione **Adicionar**.
      1. Insira o nome da nova sonda de saúde (por exemplo, **hana-hp).**
      1. Selecione **TCP** como protocolo e porta 625**03**. Mantenha o valor **de intervalo** definido para 5, e o valor do **limiar insalubre** definido para 2.
      1. Selecione **OK**.
   
   1. Para SAP HANA 1.0, crie as regras de equilíbrio de carga:
   
      1. Abra o balançador de carga, selecione **as regras de equilíbrio de carga**e selecione **Adicionar**.
      1. Introduza o nome da nova regra do balançador de carga (por exemplo, hana-lb-3**03**15).
      1. Selecione o endereço IP frontal, o pool traseiro e a sonda de saúde que criou anteriormente (por exemplo, **hana-frontend**).
      1. Mantenha o **Protocolo** definido para **TCP,** e entre na porta**3 03**15.
      1. Aumente o **tempo de 30** minutos.
      1. Certifique-se de que ativa o **IP flutuante**.
      1. Selecione **OK**.
      1. Repita estes passos para a porta**3 03**17.
   
   1. Para o SAP HANA 2.0, crie as regras de equilíbrio de carga para a base de dados do sistema:
   
      1. Abra o balançador de carga, selecione **as regras de equilíbrio de carga**e selecione **Adicionar**.
      1. Introduza o nome da nova regra do balançador de carga (por exemplo, hana-lb-3**03**13).
      1. Selecione o endereço IP frontal, o pool traseiro e a sonda de saúde que criou anteriormente (por exemplo, **hana-frontend**).
      1. Mantenha o **Protocolo** definido para **TCP,** e entre na porta**3 03**13.
      1. Aumente o **tempo de 30** minutos.
      1. Certifique-se de que ativa o **IP flutuante**.
      1. Selecione **OK**.
      1. Repita estes passos para a porta**3 03**14.
   
   1. Para o SAP HANA 2.0, primeiro crie as regras de equilíbrio de carga para a base de dados do arrendatário:
   
      1. Abra o balançador de carga, selecione **as regras de equilíbrio de carga**e selecione **Adicionar**.
      1. Introduza o nome da nova regra do balançador de carga (por exemplo, hana-lb-3**03**40).
      1. Selecione o endereço IP frontend, o backend pool e a sonda de saúde que criou anteriormente (por exemplo, **hana-frontend**).
      1. Mantenha o **Protocolo** definido para **TCP,** e entre na porta**3 03**40.
      1. Aumente o **tempo de 30** minutos.
      1. Certifique-se de que ativa o **IP flutuante**.
      1. Selecione **OK**.
      1. Repita estes passos para as portas**3 03**41 e 3**03**42.

   Para obter mais informações sobre as portas necessárias para o SAP HANA, leia o capítulo [Ligações às Bases de Dados de Inquilinos](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) no guia [sap hana de bases de dados](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) de inquilinos ou [sap Note 2388694][2388694].

> [!IMPORTANT]
> Não ative os cartas temporais TCP em VMs Azure colocados atrás do Balançador de Carga Azure. Permitir os tempos de TCP fará com que as sondas de saúde falhem. Definir o parâmetro **net.ipv4.tcp_timestamps** a **0**. Para mais detalhes consulte [as sondas de saúde load balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).
> Consulte também a nota [SAP 2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Criar um cluster pacemaker

Siga os passos na configuração do [Pacemaker no SUSE Linux Enterprise Server em Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster pacemaker básico para este servidor HANA. Pode utilizar o mesmo cluster Pacemaker para SAP HANA e SAP NetWeaver (A)SCS.

## <a name="install-sap-hana"></a>Instalar o SAP HANA

Os passos desta secção utilizam os seguintes prefixos:
- **[A]**: O passo aplica-se a todos os nós.
- **[1]**: O passo aplica-se apenas ao nó 1.
- **[2]**: O passo aplica-se apenas ao nó 2 do cluster Pacemaker.

1. **[A]** Configurar o esquema do disco: **Gestor de Volume Lógico (LVM)**.

   Recomendamos que utilize o LVM para volumes que armazenam dados e registam ficheiros. O exemplo a seguir pressupõe que as máquinas virtuais têm quatro discos de dados ligados que são usados para criar dois volumes.

   Listar todos os discos disponíveis:

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

   Criar um grupo de volume para os ficheiros de dados. Utilize um grupo de volume para os ficheiros de registo e outro para o diretório partilhado do SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Criar os volumes lógicos. Um volume linear é criado quando se utiliza `lvcreate` sem o `-i` interruptor. Sugerimos que crie um volume às riscas para um melhor desempenho de E/S e alinhe os tamanhos das listras com os valores documentados nas [configurações de armazenamento SAP HANA VM](./hana-vm-operations-storage.md). O `-i` argumento deve ser o número dos volumes físicos subjacentes e o argumento é o tamanho das `-I` listras. Neste documento, são utilizados dois volumes físicos para o volume de dados, pelo que o argumento do `-i` interruptor é definido para **2**. O tamanho da risca para o volume de dados é **de 256KiB**. Um volume físico é utilizado para o volume de registo, pelo que nenhum `-i` ou `-I` interruptores são explicitamente utilizados para os comandos de volume de registo.  

   > [!IMPORTANT]
   > Utilize o `-i` interruptor e desa cospe-o para o número do volume físico subjacente quando utilizar mais de um volume físico para cada dado, registo ou volumes partilhados. Utilize o `-I` interruptor para especificar o tamanho das listras, ao criar um volume às riscas.  
   > Consulte [as configurações de armazenamento SAP HANA VM](./hana-vm-operations-storage.md) para configurações de armazenamento recomendadas, incluindo tamanhos de listras e número de discos.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>
  
   Crie os diretórios de montagem e copie o UUID de todos os volumes lógicos:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Criar `fstab` entradas para os três volumes lógicos:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Insira a seguinte linha no `/etc/fstab` ficheiro:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Monte os novos volumes:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Configurar o esquema do disco: **Discos Simples.**

   Para sistemas de demonstração, pode colocar os seus dados HANA e registar ficheiros num disco. Crie uma divisória em /dev/disco/azure/scsi1/lun0 e formate-a com xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Insira esta linha no ficheiro /etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Crie o directório-alvo e monte o disco:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Configurar a resolução do nome do anfitrião para todos os anfitriões.

   Pode utilizar um servidor DNS ou modificar o ficheiro /etc/hosts em todos os nós. Este exemplo mostra-lhe como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas no ficheiro /etc/anfitriões. Altere o endereço IP e o nome de anfitrião para combinar com o seu ambiente:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Instale os pacotes de alta disponibilidade SAP HANA:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Para instalar a replicação do sistema SAP HANA, siga o capítulo 4 do guia de cenário otimizado para desempenho [SAP HANA SR](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]** Executar o programa **hdblcm** a partir do DVD HANA. Introduza os seguintes valores à medida:
   * Escolha a instalação: Introduza **1**.
   * Selecione componentes adicionais para a instalação: Introduza **1**.
   * Insira o Caminho de Instalação [/hana/shared]: Selecione Enter.
   * Insira o nome do anfitrião local [..]: Selecione Entrar.
   * Deseja adicionar anfitriões adicionais ao sistema? (y/n) [n]: Selecione Enter.
   * Insira o ID do sistema SAP HANA: Introduza o SID de HANA, por exemplo: **HN1**.
   * Inserir Número de Instância [00]: Introduza o número de instância HANA. Introduza **03** se utilizar o modelo Azure ou seguir a secção de implementação manual deste artigo.
   * Selecione modo de base de dados / Insira índice [1]: Selecione Introdução.
   * Selecione o Índice de Utilização do Sistema /Introdução [4]: Selecione o valor de utilização do sistema.
   * Introduza a localização dos volumes de dados [/hana/dados/HN1]: Selecione Enter.
   * Introduza a localização dos volumes de registo [/hana/log/HN1]: Selecione Enter.
   * Restringir a atribuição máxima de memória? [n]: Selecione Enter.
   * Insira o nome do anfitrião para o anfitrião '...' [...]: Selecione Entrar.
   * Insira o utilizador do agente anfitrião SAP (sapadm) Palavra-passe: Introduza a palavra-passe do utilizador do agente anfitrião.
   * Confirme o utilizador do agente anfitrião SAP (sapadm): Introduza novamente a palavra-passe do utilizador do agente anfitrião para confirmar.
   * Inserir Palavra-passe do Administrador do Sistema (hdbadm): Introduza a palavra-passe do administrador do sistema.
   * Confirmar Palavra-passe do Administrador do Sistema (hdbadm): Introduza novamente a palavra-passe do administrador do sistema para confirmar.
   * Insira o Diretório doméstico do administrador do sistema [/usr/seiva/HN1/home]: Selecione Enter.
   * Introduza a Shell de Login do Administrador do Sistema [/bin/sh]: Selecione Enter.
   * Introduza o ID do utilizador do administrador do sistema [1001]: Selecione introdução.
   * Introduza iD do Grupo de Utilizador (sapsys) [79]: Selecione Enter.
   * Introduza a palavra-passe do Utilizador (SISTEMA) da base de dados: Introduza a palavra-passe do utilizador da base de dados.
   * Confirmar palavra-passe do Utilizador da Base de Dados (SISTEMA): Introduza novamente a palavra-passe do utilizador da base de dados para confirmar.
   * Reiniciar o sistema após o reinício da máquina? [n]: Selecione Enter.
   * Quer continuar? (y/n): Validar o resumo. Entre **para** continuar.

1. **[A]** Atualize o agente anfitrião DA SAP.

   Descarregue o mais recente arquivo do Agente Anfitrião SAP do Centro de [Software SAP][sap-swcenter] e execute o seguinte comando para atualizar o agente. Substitua o caminho para o arquivo para apontar para o ficheiro que descarregou:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Configurar a replicação do sistema SAP HANA 2.0

Os passos desta secção utilizam os seguintes prefixos:

* **[A]**: O passo aplica-se a todos os nós.
* **[1]**: O passo aplica-se apenas ao nó 1.
* **[2]**: O passo aplica-se apenas ao nó 2 do cluster Pacemaker.

1. **[1]** Criar a base de dados dos inquilinos.

   Se estiver a utilizar o SAP HANA 2.0 ou o MDC, crie uma base de dados de inquilinos para o seu sistema SAP NetWeaver. Substitua **o NW1** pelo SID do seu sistema SAP.

   Execute o seguinte comando como <\> adm hanasid:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Replicação do sistema de configuração no primeiro nó:

   Ressou as bases de dados como <\> adm hanasid:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Copie os ficheiros PKI do sistema para o site secundário:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Criar o site principal:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Replicação do sistema de configuração no segundo nó:
    
   Registe o segundo nó para iniciar a replicação do sistema. Executar o seguinte comando como <\> adm hanasid:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Configurar a replicação do sistema SAP HANA 1.0

Os passos desta secção utilizam os seguintes prefixos:

* **[A]**: O passo aplica-se a todos os nós.
* **[1]**: O passo aplica-se apenas ao nó 1.
* **[2]**: O passo aplica-se apenas ao nó 2 do cluster Pacemaker.

1. **[1]** Criar os utilizadores necessários.

   Executar o seguinte comando como raiz. Certifique-se de que substitui as cordas arrojadas (HANA System ID **HN1** e o número **03**) com os valores da sua instalação SAP HANA:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Criar a entrada da loja de chaves.

   Executar o seguinte comando como raiz para criar uma nova entrada na teclas:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Re-aussi a base de dados.

   Ressou as bases de dados como raiz:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Se utilizar uma instalação multi-inquilino, faça também uma 24.000 para fazer uma insusição na base de dados dos inquilinos:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Configure Replicação do sistema no primeiro nó.

   Crie o local primário como <\> adm hanasid:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configure Replicação do sistema no nó secundário.

   Registe o site secundário como <\> adm hanasid:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Criar recursos de cluster SAP HANA

Primeiro, crie a topologia hana. Executar os seguintes comandos num dos nóns de cluster Pacemaker:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Em seguida, crie os recursos HANA:

> [!IMPORTANT]
> Testes recentes revelaram situações, em que o netcat deixa de responder aos pedidos devido a atrasos e à sua limitação de manuseamento de apenas uma ligação. O recurso netcat deixa de ouvir os pedidos do balançador Azure Load e o IP flutuante fica indisponível.  
> Para os aglomerados pacemaker existentes, recomendamos no passado a substituição do netcat por socat. Atualmente recomendamos a utilização de um agente de recursos azure-lb, que faz parte dos agentes de recursos do pacote, com os seguintes requisitos de versão pacote:
> - Para o SLES 12 SP4/SP5, a versão deve ser pelo menos agentes de recursos-4.3.018.a7fb5035-3.30.1.  
> - Para sLES 15/15 SP1, a versão deve ser pelo menos agentes de recursos-4.3.0184.6ee15eb2-4.13.1.  
>
> Note que a mudança requer breves tempos de inatividade.  
> Para os clusters pacemaker existentes, se a configuração já foi alterada para usar socat como descrito no [Azure Load-Balancer Detection Hardening](https://www.suse.com/support/kb/doc/?id=7024128), não existe qualquer requisito para mudar imediatamente para o agente de recursos azure-lb.

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
  meta notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b> \
  meta resource-stickiness=0

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

Certifique-se de que o estado do cluster está bem e que todos os recursos são iniciados. Não é importante em que nó os recursos estão a funcionar.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta secção descreve como pode testar a sua configuração. Todos os testes assumem que és uma raiz e o mestre SAP HANA está a funcionar na máquina virtual **hn1-db-0.**

### <a name="test-the-migration"></a>Testar a migração

Antes de iniciar o teste, certifique-se de que o Pacemaker não tem qualquer ação falhada (via crm_mon -r), não existem constrangimentos de localização inesperados (por exemplo, restos de um teste de migração) e que HANA é estado de sincronização, por exemplo com SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Pode migrar o nó mestre SAP HANA executando o seguinte comando:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Se `AUTOMATED_REGISTER="false"` definir, esta sequência de comandos deve migrar o nó mestre SAP HANA e o grupo que contém o endereço IP virtual para hn1-db-1.

Uma vez que a migração é feita, a saída crm_mon-r parece-se com esta

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
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

O recurso SAP HANA em hn1-db-0 não começa como secundário. Neste caso, configurar a instância HANA como secundária executando este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

A migração cria restrições de localização que precisam de ser eliminadas novamente:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Também precisa de limpar o estado do recurso de nó secundário:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Monitorize o estado do recurso HANA utilizando crm_mon -r. Uma vez que HANA é iniciado em hn1-db-0, a saída deve ser assim

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
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Teste o agente de esgrima Azure (não SBD)

Pode testar a configuração do agente de esgrima Azure desativando a interface de rede no nó hn1-db-0:

<pre><code>sudo ifdown eth0
</code></pre>

A máquina virtual deve agora reiniciar ou parar dependendo da configuração do cluster.
Se descolorê o `stonith-action` cenário, a máquina virtual fica parada e os recursos são migrados para a máquina virtual em funcionamento.

Depois de voltar a ligar a máquina virtual, o recurso SAP HANA não começa como secundário se estiver definido `AUTOMATED_REGISTER="false"` . Neste caso, configurar a instância HANA como secundária executando este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Esgrima SBD de teste

Pode testar a configuração do SBD matando o processo do inquisidor.

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

O nó de cluster hn1-db-0 deve ser reiniciado. O serviço pacemaker pode não começar depois. Certifique-se de começar de novo.

### <a name="test-a-manual-failover"></a>Teste um failover manual

Pode testar uma falha manual, interrompendo o `pacemaker` serviço no nó hn1-db-0:

<pre><code>service pacemaker stop
</code></pre>

Depois do fracasso, pode recomeçar o serviço. Se `AUTOMATED_REGISTER="false"` definir, o recurso SAP HANA no nó hn1-db-0 não começa como secundário. Neste caso, configurar a instância HANA como secundária executando este comando:

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
> Certifique-se de que o sistema operativo selecionado é CERTIFICADO SAP para SAP HANA nos tipos de VM específicos que está a utilizar. A lista de tipos de VM certificados da SAP HANA e lançamentos de SO para estes podem ser analisados nas [plataformas IAAS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Certifique-se de clicar nos detalhes do tipo VM listado para obter a lista completa de versões DE OS suportadas pela SAP HANA para o tipo VM específico

Executar todos os casos de teste listados no Cenário Otimizado de Desempenho SAP HANA SR ou no guia SAP HANA SR Cost Optimized Scenario, dependendo da sua caixa de utilização. Pode encontrar os guias na [página SLES para as melhores práticas sap.][sles-for-sap-bp]

Os seguintes testes são uma cópia das descrições de teste do sap HANA SR Performance Optimized Scenario SUSE Linux Enterprise Server para aplicações SAP 12 SP1 guide. Para uma versão atualizada, leia sempre o guia em si. Certifique-se sempre de que o HANA está sincronizado antes de iniciar o teste e certifique-se também de que a configuração do Pacemaker está correta.

Nas seguintes descrições de teste assumimos PREFER_SITE_TAKEOVER="verdadeiro" e AUTOMATED_REGISTER="falso".
NOTA: Os seguintes testes foram concebidos para serem executados em sequência e dependem do estado de saída dos testes anteriores.

1. TESTE 1: PARAR A BASE DE DADOS PRIMÁRIA NO NÓ 1

   Estado dos recursos antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Executar os seguintes comandos como <adm hanasid \> no nó hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   O pacemaker deve detetar a hana paragem e falhar no outro nó. Uma vez que o failover é feito, a instância HANA no nó hn1-db-0 é interrompida porque pacemaker não regista automaticamente o nó como HANA secundário.

   Executar os seguintes comandos para registar o nó hn1-db-0 como secundário e limpar o recurso falhado.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Estado de recursos após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TESTE 2: PARAR A BASE DE DADOS PRIMÁRIA NO NÓ 2

   Estado dos recursos antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Executar os seguintes comandos como <adm hanasid \> no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   O pacemaker deve detetar a hana paragem e falhar no outro nó. Uma vez que o failover é feito, a instância HANA no nó hn1-db-1 é interrompida porque pacemaker não regista automaticamente o nó como HANA secundário.

   Executar os seguintes comandos para registar o nó hn1-db-1 como secundário e limpar o recurso falhado.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado de recursos após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TESTE 3: BASE DE DADOS PRIMÁRIA DE COLISÃO NO NÓ

   Estado dos recursos antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Executar os seguintes comandos como <adm hanasid \> no nó hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   O Pacemaker deve detetar a instância hana morta e falhar com o outro nó. Uma vez que o failover é feito, a instância HANA no nó hn1-db-0 é interrompida porque pacemaker não regista automaticamente o nó como HANA secundário.

   Executar os seguintes comandos para registar o nó hn1-db-0 como secundário e limpar o recurso falhado.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Estado de recursos após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TESTE 4: BASE DE DADOS PRIMÁRIA DE COLISÃO NO NÓ 2

   Estado dos recursos antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Executar os seguintes comandos como <adm hanasid \> no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   O Pacemaker deve detetar a instância hana morta e falhar com o outro nó. Uma vez que o failover é feito, a instância HANA no nó hn1-db-1 é interrompida porque pacemaker não regista automaticamente o nó como HANA secundário.

   Executar os seguintes comandos para registar o nó hn1-db-1 como secundário e limpar o recurso falhado.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado de recursos após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TESTE 5: NÓ DO LOCAL PRIMÁRIO DE COLISÃO (NÓ 1)

   Estado dos recursos antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Executar os seguintes comandos como raiz no nó hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   O pacemaker deve detetar o nó de aglomerado morto e cercar o nó. Assim que o nó estiver vedado, o Pacemaker irá desencadear uma aquisição da instância HANA. Quando o nó vedado for reiniciado, o Pacemaker não arranca automaticamente.

   Executar os seguintes comandos para iniciar o Pacemaker, limpar as mensagens SBD para o nó hn1-db-0, registar o nó hn1-db-0 como secundário e limpar o recurso falhado.

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

   Estado de recursos após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TESTE 6: NÓ DO LOCAL SECUNDÁRIO DE COLISÃO (NÓ 2)

   Estado dos recursos antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Executar os seguintes comandos como raiz no nó hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   O pacemaker deve detetar o nó de aglomerado morto e cercar o nó. Assim que o nó estiver vedado, o Pacemaker irá desencadear uma aquisição da instância HANA. Quando o nó vedado for reiniciado, o Pacemaker não arranca automaticamente.

   Executar os seguintes comandos para iniciar o Pacemaker, limpar as mensagens SBD para o nó hn1-db-1, registar o nó hn1-db-1 como secundário e limpar o recurso falhado.

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

   Estado de recursos após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TESTE 7: PARAR A BASE DE DADOS SECUNDÁRIA NO NÓ 2

   Estado dos recursos antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Executar os seguintes comandos como <adm hanasid \> no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   O Pacemaker detetará a instância HANA parada e marcará o recurso como falhado no nó hn1-db-1. O pacemaker deve reiniciar automaticamente a instância HANA. Executar o seguinte comando para limpar o estado falhado.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado de recursos após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TESTE 8: BATER A BASE DE DADOS SECUNDÁRIA NO NÓ 2

   Estado dos recursos antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Executar os seguintes comandos como <adm hanasid \> no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   O Pacemaker detetará a instância hana morta e marcará o recurso como falhado no nó hn1-db-1. Executar o seguinte comando para limpar o estado falhado. O pacemaker deve então reiniciar automaticamente a instância HANA.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado de recursos após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TESTE 9: NÓ DO LOCAL SECUNDÁRIO DE COLISÃO (NÓ 2) QUE EXECUTA A BASE DE DADOS SECUNDÁRIA DE HANA

   Estado dos recursos antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Executar os seguintes comandos como raiz no nó hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   O pacemaker deve detetar o nó de aglomerado morto e cercar o nó. Quando o nó vedado for reiniciado, o Pacemaker não arranca automaticamente.

   Executar os seguintes comandos para iniciar o Pacemaker, limpar as mensagens SBD para o nó hn1-db-1 e limpar o recurso falhado.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado de recursos após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Passos seguintes

* [Azure Virtual Machines planejamento e implementação para SAP][planning-guide]
* [Implantação de máquinas virtuais Azure para SAP][deployment-guide]
* [Implantação DBMS de máquinas virtuais Azure para SAP][dbms-guide]
