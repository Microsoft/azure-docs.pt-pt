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
ms.date: 04/22/2020
ms.author: radeltch
ms.openlocfilehash: e04b37d0c95f2176581c7d13f3641a13ecddfd8f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101217"
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

Para o desenvolvimento no local, pode utilizar a Replicação do Sistema HANA ou utilizar o armazenamento partilhado para estabelecer uma elevada disponibilidade para o SAP HANA.
Nas máquinas virtuais Azure (VMs), a replicação do sistema HANA no Azure é atualmente a única função de alta disponibilidade suportada. A replicação do SAP HANA consiste num nó primário e pelo menos num nó secundário. As alterações aos dados do nó primário são replicadas ao nó secundário sincronicamente ou assíncronamente.

Este artigo descreve como implementar e configurar as máquinas virtuais, instalar a estrutura do cluster e instalar e configurar a Replicação do Sistema SAP HANA.
Nas configurações de exemplo, são utilizados comandos de instalação, instância número **03**e ID **HN1** do sistema HANA.

Leia primeiro as seguintes Notas e papéis SAP:

* Nota SAP [1928533,]que tem:
  * A lista dos tamanhos de VM Azure que são suportados para a implementação de software SAP.
  * Informações importantes sobre a capacidade para tamanhos de VM Azure.
  * O software SAP suportado e o sistema operativo (OS) e as combinações de bases de dados.
  * A versão necessária para o kernel SAP necessário para Windows e Linux no Microsoft Azure.
* O SAP Note [2015553] lista os pré-requisitos para implementações de software SAP suportadas pelo SAP em Azure.
* O SAP Note [2205917] recomendou as definições de OS para o Servidor Empresarial SUSE Linux para aplicações SAP.
* SAP Nota [1944799] tem Diretrizes SAP HANA para SUSE Linux Enterprise Server para Aplicações SAP.
* O SAP Note [2178632] tem informações detalhadas sobre todas as métricas de monitorização que são reportadas para o SAP em Azure.
* O SAP Note [2191498] tem a versão necessária do Agente anfitrião SAP para o Linux em Azure.
* SAP Nota [2243692] tem informações sobre licenciamento SAP em Linux em Azure.
* SAP Note [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A Nota [SAP 1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização avançada do Azure para sAP.
* O SAP Note [401162] tem informações sobre como evitar "endereço já em uso" ao configurar a Replicação do Sistema HANA.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para linux.
* [Plataformas IaaS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* Planeamento e implementação de [Máquinas Virtuais Azure para SAP no guia Linux.][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP em Linux][deployment-guide] (este artigo).
* [Implantação DE DBMS de Máquinas Virtuais Azure para SAP no guia Linux.][dbms-guide]
* [SUSE Linux Enterprise Server para Aplicações SAP 12 Guias de boas práticas SP3][sles-for-sap-bp]
  * Criação de uma Infraestrutura Otimizada de Desempenho SAP HANA SR (SLES para Aplicações SAP 12 SP1). O guia contém todas as informações necessárias para configurar a replicação do sistema SAP HANA para o desenvolvimento no local. Utilize este guia como base.
  * Criação de uma Infraestrutura Otimizada de Custos SAP HANA SR (SLES para Aplicações SAP 12 SP1)

## <a name="overview"></a>Descrição geral

Para obter uma elevada disponibilidade, o SAP HANA está instalado em duas máquinas virtuais. Os dados são replicados utilizando a replicação do sistema HANA.

![Visão geral de alta disponibilidade do SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

A configuração de replicação do sistema SAP HANA utiliza um nome de anfitrião virtual dedicado e endereços IP virtuais. No Azure, é necessário utilizar um endereço IP virtual. A lista que se segue mostra a configuração do equilibrista de carga:

* Configuração frontal: Endereço IP 10.0.0.13 para hn1-db
* Configuração de back-end: Ligada às interfaces de rede primária de todas as máquinas virtuais que devem fazer parte da Replicação do Sistema HANA
* Porta da sonda: Porto 62503
* Regras de equilíbrio de carga: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Implantação para Linux

O agente de recursos para o SAP HANA está incluído no SUSE Linux Enterprise Server para aplicações SAP.
O Azure Marketplace contém uma imagem para o SUSE Linux Enterprise Server para aplicações SAP 12 que pode utilizar para implementar novas máquinas virtuais.

### <a name="deploy-with-a-template"></a>Implementar com um modelo

Você pode usar um dos modelos de arranque rápido que estão no GitHub para implementar todos os recursos necessários. O modelo implanta as máquinas virtuais, o equilibrador de carga, o conjunto de disponibilidade, e assim por diante.
Para implementar o modelo, siga estes passos:

1. Abra o modelo de base de [dados][template-multisid-db] ou o [modelo convergente][template-converged] no portal Azure. 
    O modelo de base de dados cria as regras de equilíbrio de carga apenas para uma base de dados. O modelo convergente também cria as regras de equilíbrio de carga para uma instância ASCS/SCS e ERS (apenas Linux). Se planeia instalar um sistema baseado em SAP NetWeaver e pretende instalar a instância ASCS/SCS nas mesmas máquinas, utilize o [modelo convergente][template-converged].

1. Introduza os seguintes parâmetros:
    - **ID do sistema Sap**: Introduza o ID do sistema SAP do sistema SAP que pretende instalar. O ID é usado como prefixo para os recursos que são implantados.
    - **Tipo de pilha:**(Este parâmetro só é aplicável se utilizar o modelo convergente.) Selecione o tipo de pilha SAP NetWeaver.
    - **Tipo Os**: Selecione uma das distribuições linux. Para este exemplo, selecione **SLES 12**.
    - **Tipo db**: Selecione **HANA**.
    - Tamanho do **sistema sap**: Introduza o número de SAPS que o novo sistema vai fornecer. Se não tem a certeza de quantos SAPS o sistema necessita, pergunte ao seu Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
    - **Disponibilidade do Sistema**: Selecione **HA**.
    - **Nome**de utilizador e senha de administrador : É criado um novo utilizador que pode ser utilizado para iniciar sessão na máquina.
    - **Subnet nova ou existente**: Determina se deve ser criada uma nova rede virtual e uma sub-rede ou uma sub-rede existente. Se já tiver uma rede virtual ligada à sua rede no local, selecione **Existino**.
    - **ID sub-rede**: Se pretender implantar o VM numa VNet existente onde tenha uma sub-rede definida a VM deve ser atribuída, diga o nome da identificação dessa sub-rede específica. O ID geralmente se parece com **/subscrições/\<ID de\<subscrição>/recursosGroups/\<nome de grupo de\<recursos>/fornecedores/Microsoft.Network/virtualNetworks/ nome de rede virtual>/subnets/ nome de sub-rede>**.

### <a name="manual-deployment"></a>Implementação manual

> [!IMPORTANT]
> Certifique-se de que o SISTEMA selecionado é certificado sAP para SAP HANA nos tipos de VM específicos que está a utilizar. A lista dos tipos vM certificados sAP HANA e os lançamentos de SOs para aqueles podem ser analisados nas [Plataformas IaaS Certificadas SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Certifique-se de clicar nos detalhes do tipo VM listado para obter a lista completa de lançamentos sap HANA suportados para o tipo de VM específico
>  

1. Crie um grupo de recursos.
1. Crie uma rede virtual
1. Crie um conjunto de disponibilidade.
   - Detete o domínio da atualização máxima.
1. Criar um equilibrador de carga (interno). Recomendamos um [equilíbrio de carga padrão.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)
   - Selecione a rede virtual criada no passo 2.
1. Criar a máquina virtual 1.
   - Utilize uma imagem SLES4SAP na galeria Azure que é suportada para SAP HANA no tipo VM que selecionou.
   - Selecione o conjunto de disponibilidade criado no passo 3.
1. Criar a máquina virtual 2.
   - Utilize uma imagem SLES4SAP na galeria Azure que é suportada para SAP HANA no tipo VM que selecionou.
   - Selecione o conjunto de disponibilidade criado no passo 3. 
1. Adicione discos de dados.
1. Se utilizar um equilibrador de carga padrão, siga estes passos de configuração:
   1. Primeiro, crie uma piscina IP frontal:
   
      1. Abra o equilibrador de carga, selecione **pool IP frontend,** e selecione **Adicionar**.
      1. Introduza o nome da nova piscina IP frontal (por exemplo, **hana-frontend).**
      1. Defino a **Atribuição** à **Estática** e introduza o endereço IP (por exemplo, **10.0.0.13**).
      1. Selecione **OK**.
      1. Depois de criado o novo pool IP frontal, note o endereço IP do pool.
   
   1. Em seguida, crie uma piscina de back-end:
   
      1. Abra o equilibrador de carga, selecione **piscinas de backend,** e selecione **Adicionar**.
      1. Introduza o nome da nova piscina traseira (por exemplo, **hana-backend).**
      1. Selecione **Rede Virtual**.
      1. **Selecione Adicionar uma máquina virtual**.
      1. Selecione ** Máquina virtual**.
      1. Selecione as máquinas virtuais do cluster SAP HANA e os seus endereços IP.
      1. Selecione **Adicionar**.
   
   1. Em seguida, crie uma sonda de saúde:
   
      1. Abra o equilibrador de carga, selecione sondas de **saúde,** e selecione **Adicionar**.
      1. Introduza o nome da nova sonda de saúde (por exemplo, **hana-hp).**
      1. Selecione **TCP** como protocolo e porta 625**03**. Mantenha o valor **de intervalo** definido para 5 e o valor limiar **não saudável** definido para 2.
      1. Selecione **OK**.
   
   1. Em seguida, crie as regras de equilíbrio de carga:
   
      1. Abra o equilibrador de **carga,** selecione regras de equilíbrio de carga, e selecione **Adicionar**.
      1. Introduza o nome da nova regra do equilibrador de carga (por exemplo, **hana-lb**).
      1. Selecione o endereço IP frontal, a piscina traseira e a sonda de saúde que criou anteriormente (por exemplo, **hana-frontend,** **hana-backend** e **hana-hp).**
      1. Selecione **Portas HA**.
      1. Aumente o **tempo de inatividade** para 30 minutos.
      1. Certifique-se de **ativar**o IP flutuante .
      1. Selecione **OK**.

   > [!Note]
   > Quando os VMs sem endereços IP públicos forem colocados no conjunto de backend do interno (sem endereço IP público) O equilíbrio de carga Standard Azure não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos. Para mais detalhes sobre como alcançar a conectividade de saída, consulte [a conectividade do ponto final público para máquinas virtuais utilizando o Equilíbrio de Carga Padrão Azure em cenários de alta disponibilidade SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

1. Alternativamente, se o seu cenário ditar a utilização de um equilíbrio básico de carga, siga estes passos de configuração:
   1. Primeiro, crie uma piscina IP frontal:
   
      1. Abra o equilibrador de carga, selecione **pool IP frontend,** e selecione **Adicionar**.
      1. Introduza o nome da nova piscina IP frontal (por exemplo, **hana-frontend).**
      1. Defino a **Atribuição** à **Estática** e introduza o endereço IP (por exemplo, **10.0.0.13**).
      1. Selecione **OK**.
      1. Depois de criado o novo pool IP frontal, note o endereço IP do pool.
   
   1. Em seguida, crie uma piscina de back-end:
   
      1. Abra o equilibrador de carga, selecione **piscinas de backend,** e selecione **Adicionar**.
      1. Introduza o nome da nova piscina traseira (por exemplo, **hana-backend).**
      1. **Selecione Adicionar uma máquina virtual**.
      1. Selecione o conjunto de disponibilidade criado no passo 3.
      1. Selecione as máquinas virtuais do cluster SAP HANA.
      1. Selecione **OK**.
   
   1. Em seguida, crie uma sonda de saúde:
   
      1. Abra o equilibrador de carga, selecione sondas de **saúde,** e selecione **Adicionar**.
      1. Introduza o nome da nova sonda de saúde (por exemplo, **hana-hp).**
      1. Selecione **TCP** como protocolo e porta 625**03**. Mantenha o valor **de intervalo** definido para 5 e o valor limiar **não saudável** definido para 2.
      1. Selecione **OK**.
   
   1. Para o SAP HANA 1.0, crie as regras de equilíbrio de carga:
   
      1. Abra o equilibrador de **carga,** selecione regras de equilíbrio de carga, e selecione **Adicionar**.
      1. Introduza o nome da nova regra do equilibrador de carga (por exemplo, hana-lb-3**03**15).
      1. Selecione o endereço IP frontal, a piscina traseira e a sonda de saúde que criou anteriormente (por exemplo, **hana-frontend**).
      1. Mantenha o **Protocolo** definido para a **TCP**e entre na porta 3**03**15.
      1. Aumente o **tempo de inatividade** para 30 minutos.
      1. Certifique-se de **ativar**o IP flutuante .
      1. Selecione **OK**.
      1. Repita estes passos para a porta 3**03**17.
   
   1. Para o SAP HANA 2.0, crie as regras de equilíbrio de carga para a base de dados do sistema:
   
      1. Abra o equilibrador de **carga,** selecione regras de equilíbrio de carga, e selecione **Adicionar**.
      1. Introduza o nome da nova regra do equilibrador de carga (por exemplo, hana-lb-3**03**13).
      1. Selecione o endereço IP frontal, a piscina traseira e a sonda de saúde que criou anteriormente (por exemplo, **hana-frontend**).
      1. Mantenha o **Protocolo** definido para a **TCP**e entre na porta 3**03**13.
      1. Aumente o **tempo de inatividade** para 30 minutos.
      1. Certifique-se de **ativar**o IP flutuante .
      1. Selecione **OK**.
      1. Repita estes passos para a porta 3**03**14.
   
   1. Para o SAP HANA 2.0, crie primeiro as regras de equilíbrio de carga para a base de dados dos inquilinos:
   
      1. Abra o equilibrador de **carga,** selecione regras de equilíbrio de carga, e selecione **Adicionar**.
      1. Introduza o nome da nova regra do equilibrador de carga (por exemplo, hana-lb-3**03**40).
      1. Selecione o endereço IP frontal, a piscina de backend e a sonda de saúde que criou anteriormente (por exemplo, **hana-frontend**).
      1. Mantenha o **Protocolo** definido para a **TCP**e entre na porta 3**03**40.
      1. Aumente o **tempo de inatividade** para 30 minutos.
      1. Certifique-se de **ativar**o IP flutuante .
      1. Selecione **OK**.
      1. Repita estes passos para as portas 3**03**41 e 3**03**42.

   Para obter mais informações sobre as portas necessárias para o SAP HANA, leia o capítulo [Ligações às Bases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) de Dados de Inquilinos no guia [SAP HANA Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) ou [SAP Nota 2388694][2388694].

> [!IMPORTANT]
> Não permita os carimbos de tempo da TCP em VMs Azure colocados atrás do Equilíbrio de Carga Azure. Permitir os selos temporais da TCP fará com que as sondas de saúde falhem. Definir parâmetro **net.ipv4.tcp_timestamps** a **0**. Para mais detalhes consulte as sondas de [saúde load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).
> Consulte também a nota SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Criar um cluster Pacemaker

Siga os passos na configuração do [Pacemaker no SUSE Linux Enterprise Server em Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster pacemaker básico para este servidor HANA. Pode utilizar o mesmo cluster Pacemaker para SAP HANA e SAP NetWeaver (A)SCS.

## <a name="install-sap-hana"></a>Instalar o SAP HANA

Os passos nesta secção utilizam os seguintes prefixos:
- **[A]**: O passo aplica-se a todos os nós.
- **[1]**: O passo aplica-se apenas ao nó 1.
- **[2]**: O passo aplica-se apenas ao nó 2 do cluster Pacemaker.

1. **[A]** Configurar o layout do disco: **Logical Volume Manager (LVM)**.

   Recomendamos que utilize o LVM para volumes que armazenem dados e ficheiros de registo. O exemplo seguinte pressupõe que as máquinas virtuais têm quatro discos de dados ligados que são usados para criar dois volumes.

   Enumerar todos os discos disponíveis:

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

   Crie um grupo de volume para os ficheiros de dados. Utilize um grupo de volume para os ficheiros de registo e um para o diretório partilhado do SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Criar os volumes lógicos. Um volume linear é `lvcreate` criado `-i` quando se utiliza sem o interruptor. Sugerimos que crie um volume listrado para um melhor desempenho em I/S e alinhe os tamanhos das riscas com os valores documentados nas configurações de [armazenamento VM SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). O `-i` argumento deve ser o número dos `-I` volumes físicos subjacentes e o argumento é o tamanho das riscas. Neste documento, são utilizados dois volumes físicos `-i` para o volume de dados, pelo que o argumento da comutação está definido para **2**. O tamanho das riscas para o volume de dados é **de 256KiB**. Um volume físico é utilizado para o `-i` `-I` volume de registo, pelo que nenhum ou interruptor é explicitamente utilizado para os comandos de volume de registo.  

   > [!IMPORTANT]
   > Utilize `-i` o interruptor e detetetete-o para o número do volume físico subjacente quando utilizar mais de um volume físico para cada dados, registo ou volumes partilhados. Utilize `-I` o interruptor para especificar o tamanho das riscas, quando criar um volume listrado.  
   > Consulte as configurações de [armazenamento VM SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) para configurações de armazenamento recomendadas, incluindo tamanhos de listras e número de discos.  

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

   Insira a `/etc/fstab` seguinte linha no ficheiro:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Monte os novos volumes:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Configurar o layout do disco: **Discos simples**.

   Para sistemas de demonstração, pode colocar os seus dados HANA e ficheiros de registo num disco. Criar uma partição em /dev/disk/azure/scsi1/lun0 e forme-a com xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Insira esta linha no ficheiro /etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Crie o diretório-alvo e monte o disco:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Configurar a resolução de nomes de anfitrião para todos os anfitriões.

   Pode utilizar um servidor DNS ou modificar o ficheiro /etc/anfitriões em todos os nós. Este exemplo mostra-lhe como usar o ficheiro /etc/anfitriões.
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

Para instalar a Replicação do Sistema SAP HANA, siga o capítulo 4 do [guia de cenário otimizado de desempenho SAP HANA SR](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]** Executar o programa **hdblcm** do DVD HANA. Introduza os seguintes valores no momento:
   * Escolha a instalação: Insira **1**.
   * Selecione componentes adicionais para instalação: Insira **1**.
   * Introduza o Caminho de Instalação [/hana/partilhado]: Selecione Entrar.
   * Introduza o nome do anfitrião local [..]: Selecione Entrar.
   * Deseja adicionar mais anfitriões ao sistema? (y/n) [n]: Selecione Entrar.
   * Introduza o ID do sistema SAP HANA: Introduza o SID de HANA, por exemplo: **HN1**.
   * Insira o número da instância [00]: Introduza o número da instância HANA. Introduza **03** se usou o modelo Azure ou seguiu a secção de implantação manual deste artigo.
   * Selecione Modo base de dados / Introduza o Índice [1]: Selecione Introduzir.
   * Selecione a utilização do sistema / Insira o Índice [4]: Selecione o valor de utilização do sistema.
   * Introduza a localização dos volumes de dados [/hana/data/HN1]: Selecione Entrar.
   * Introduza a localização dos volumes de registo [/hana/log/HN1]: Selecione Entrar.
   * Restringir a atribuição máxima de memória? [n]: Selecione Entrar.
   * Insira o nome do anfitrião do certificado para o anfitrião '...' [...]: Selecione Entrar.
   * Introduza o Utilizador do Agente anfitrião SAP (sapadm) Palavra-passe: Introduza a palavra-passe do utilizador do agente anfitrião.
   * Confirme o Utilizador do Agente anfitrião SAP (sapadm) Palavra-passe: Introduza novamente a palavra-passe do utilizador do agente anfitrião para confirmar.
   * Introduza o Administrador do Sistema (hdbadm) Palavra-passe: Introduza a palavra-passe do administrador do sistema.
   * Confirmar Administrador do Sistema (hdbadm) Palavra-passe: Introduza novamente a palavra-passe do administrador do sistema para confirmar.
   * Insira o Diretório Inicial do Administrador do Sistema [/usr/sap/HN1/home]: Selecione Entrar.
   * Introduza a concha de login do administrador do sistema [/bin/sh]: Selecione Entrar.
   * Introduza o ID do utilizador do administrador do sistema [1001]: Selecione Entrar.
   * Introduza id do Grupo utilizador (sapsys) [79]: Selecione Entrar.
   * Introduza a palavra-passe do utilizador (SISTEMA) da base de dados: Introduza a palavra-passe do utilizador da base de dados.
   * Confirmar Palavra-passe do Utilizador da Base de Dados (SISTEMA): Introduza novamente a palavra-passe do utilizador da base de dados para confirmar.
   * Reiniciar o sistema após a reinicialização da máquina? [n]: Selecione Entrar.
   * Quer continuar? (y/n): Validar o resumo. Insira **y** para continuar.

1. **[A]** Atualizar o Agente Hospedeiro SAP.

   Descarregue o mais recente arquivo do Agente Anfitrião SAP do Centro de [Software SAP][sap-swcenter] e execute o seguinte comando para atualizar o agente. Substitua o caminho para o arquivo para apontar para o ficheiro que descarregou:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Configure A Replicação do Sistema SAP HANA 2.0

Os passos nesta secção utilizam os seguintes prefixos:

* **[A]**: O passo aplica-se a todos os nós.
* **[1]**: O passo aplica-se apenas ao nó 1.
* **[2]**: O passo aplica-se apenas ao nó 2 do cluster Pacemaker.

1. **[1]** Criar a base de dados dos inquilinos.

   Se estiver a utilizar o SAP HANA 2.0 ou o MDC, crie uma base de dados de inquilinos para o seu sistema SAP NetWeaver. Substitua o **NW1** pelo SID do seu sistema SAP.

   Executar o seguinte comando como\><hanasid adm:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Configurar a replicação do sistema no primeiro nó:

   Volte a fazer as bases\>de dados como <hanasid adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Copie os ficheiros PKI do sistema para o local secundário:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Criar o local principal:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configurar a replicação do sistema no segundo nó:
    
   Registe o segundo nó para iniciar a replicação do sistema. Executar o seguinte comando como\><hanasid adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Configure A Replicação do Sistema SAP HANA 1.0

Os passos nesta secção utilizam os seguintes prefixos:

* **[A]**: O passo aplica-se a todos os nós.
* **[1]**: O passo aplica-se apenas ao nó 1.
* **[2]**: O passo aplica-se apenas ao nó 2 do cluster Pacemaker.

1. **[1]** Criar os utilizadores necessários.

   Executar o seguinte comando como raiz. Certifique-se de substituir as cordas arrojadas (HANA System ID **HN1** e a instância número **03**) com os valores da sua instalação SAP HANA:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Criar a entrada na loja de chaves.

   Executar o seguinte comando como raiz para criar uma nova entrada na keystore:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Volte a subir na base de dados.

   Volte a fazer as bases de dados como raiz:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Se utilizar uma instalação multi-arrendatária, também faça o back up da base de dados de inquilinos:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Configurar a replicação do sistema no primeiro nó.

   Crie o local principal como\><hanasid adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configurar a replicação do sistema no nó secundário.

   Registe o local secundário como\><hanasid adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Criar recursos de cluster SAP HANA

Primeiro, crie a topologia HANA. Executar os seguintes comandos num dos nós do cluster Pacemaker:

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
> Testes recentes revelaram situações, em que o netcat deixa de responder aos pedidos devido a atrasos e à sua limitação de manuseamento apenas uma ligação. O recurso netcat para de ouvir os pedidos do balanceador de carga Azure e o IP flutuante fica indisponível.  
> Para os aglomerados Pacemaker existentes, recomendamos no passado substituir o netcat por socat. Atualmente recomendamos a utilização de um agente de recursos azure-lb, que faz parte dos agentes de recursos do pacote, com os seguintes requisitos de versão de pacote:
> - Para o SLES 12 SP4/SP5, a versão deve ser pelo menos agentes de recursos-4.3.018.a7fb5035-3.30.1.  
> - Para o SLES 15/15 SP1, a versão deve ser pelo menos agentes de recursos-4.3.0184.6ee15eb2-4.13.1.  
>
> Note que a alteração exigirá um breve tempo de inatividade.  
> Para os clusters Pacemaker existentes, se a configuração já foi alterada para utilizar o socat conforme descrito no Endurecimento de Deteção de Equilíbrio [de Carga azure,](https://www.suse.com/support/kb/doc/?id=7024128)não há necessidade de mudar imediatamente para o agente de recursos azure-lb.

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

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b>

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

Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante em que nó dos recursos estão a funcionar.

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
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta secção descreve como pode testar a sua configuração. Todos os testes assumem que você é raiz e o mestre SAP HANA está correndo na máquina virtual **hn1-db-0.**

### <a name="test-the-migration"></a>Testar a migração

Antes de iniciar o teste, certifique-se de que o Pacemaker não tem qualquer ação falhada (via crm_mon -r), não existem restrições de localização inesperadas (por exemplo, restos de um teste de migração) e que hana é estado de sincronização, por exemplo com SAPHanaSR-showAttr:

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

Se definir, `AUTOMATED_REGISTER="false"`esta sequência de comandos deve migrar o nó principal SAP HANA e o grupo que contém o endereço IP virtual para hn1-db-1.

Uma vez que a migração é feita, a saída crm_mon -r parece-se com esta

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

O recurso SAP HANA no hn1-db-0 não começa como secundário. Neste caso, configure a instância HANA como secundária executando este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

A migração cria constrangimentos de localização que precisam de ser novamente eliminados:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Você também precisa limpar o estado do recurso do nó secundário:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Monitorize o estado do recurso HANA utilizando crm_mon-r. Uma vez que HANA é iniciado em hn1-db-0, a saída deve ser assim

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

A máquina virtual deve agora reiniciar ou parar dependendo da configuração do seu cluster.
Se definir `stonith-action` a configuração para o desligado, a máquina virtual é parada e os recursos são migrados para a máquina virtual em execução.

Depois de relançar a máquina virtual, o recurso SAP HANA `AUTOMATED_REGISTER="false"`não começa como secundário se definir . Neste caso, configure a instância HANA como secundária executando este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Esgrima SBD de teste

Pode testar a configuração da DSB matando o processo do inquisidor.

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

O nó de aglomerado hn1-db-0 deve ser reiniciado. O serviço Pacemaker pode não começar depois. Certifique-se de começar de novo.

### <a name="test-a-manual-failover"></a>Teste uma falha manual

Pode testar uma falha manual `pacemaker` interrompendo o serviço no nó hn1-db-0:

<pre><code>service pacemaker stop
</code></pre>

Depois da falha, pode recomeçar o serviço. Se definir, `AUTOMATED_REGISTER="false"`o recurso SAP HANA no nó hn1-db-0 não começa como secundário. Neste caso, configure a instância HANA como secundária executando este comando:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>Testes de sUSE

> [!IMPORTANT]
> Certifique-se de que o SISTEMA selecionado é certificado sAP para SAP HANA nos tipos de VM específicos que está a utilizar. A lista dos tipos vM certificados sAP HANA e os lançamentos de SOs para aqueles podem ser analisados nas [Plataformas IaaS Certificadas SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Certifique-se de clicar nos detalhes do tipo VM listado para obter a lista completa de lançamentos sap HANA suportados para o tipo de VM específico

Execute todos os casos de teste listados no Cenário Otimizado de Desempenho SAP HANA SR ou no guia SAP HANA SR Custo Optimized Scenario, dependendo do seu caso de utilização. Pode encontrar os guias na [página SLES para as melhores práticas do SAP][sles-for-sap-bp].

Os seguintes testes são uma cópia das descrições do teste do sap HANA SR Performance Optimized Scenario SUSE Linux Enterprise Server para aplicações SAP 12 SP1 guia. Para uma versão atualizada, leia sempre o guia em si. Certifique-se sempre de que a HANA está sincronizada antes de iniciar o teste e certifique-se também de que a configuração do Pacemaker está correta.

Nas seguintes descrições do teste assumimos PREFER_SITE_TAKEOVER="verdade" e AUTOMATED_REGISTER="falso".
NOTA: Os seguintes ensaios foram concebidos para serem executados em sequência e dependem do estado de saída dos testes anteriores.

1. TESTE 1: PARAR BASE DE DADOS PRIMÁRIA NO NÓ 1

   Estado de recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Executar os seguintes comandos\>como <hanasid adm no nó hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   O pacemaker deve detetar a instância HANA parada e falhar com o outro nó. Uma vez que a falha é feita, a instância HANA no nó hn1-db-0 é interrompida porque pacemaker não regista automaticamente o nó como hana secundário.

   Executar os seguintes comandos para registar o nó hn1-db-0 como secundário e limpar o recurso falhado.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Estado de recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TESTE 2: PARAR BASE DE DADOS PRIMÁRIA NO NÓ 2

   Estado de recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Executar os seguintes comandos\>como <hanasid adm no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   O pacemaker deve detetar a instância HANA parada e falhar com o outro nó. Uma vez que a falha é feita, a instância HANA no nó hn1-db-1 é interrompida porque o Pacemaker não regista automaticamente o nó como hana secundário.

   Executar os seguintes comandos para registar o nó hn1-db-1 como secundário e limpar o recurso falhado.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado de recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TESTE 3: BASE DE DADOS PRIMÁRIA DE ACIDENTE NO NÓ

   Estado de recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Executar os seguintes comandos\>como <hanasid adm no nó hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   O pacemaker deve detetar a instância hana morta e falhar com o outro nó. Uma vez que a falha é feita, a instância HANA no nó hn1-db-0 é interrompida porque pacemaker não regista automaticamente o nó como hana secundário.

   Executar os seguintes comandos para registar o nó hn1-db-0 como secundário e limpar o recurso falhado.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Estado de recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TESTE 4: BASE DE DADOS PRIMÁRIA DE ACIDENTE NO NÓ 2

   Estado de recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Executar os seguintes comandos\>como <hanasid adm no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   O pacemaker deve detetar a instância hana morta e falhar com o outro nó. Uma vez que a falha é feita, a instância HANA no nó hn1-db-1 é interrompida porque o Pacemaker não regista automaticamente o nó como hana secundário.

   Executar os seguintes comandos para registar o nó hn1-db-1 como secundário e limpar o recurso falhado.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado de recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TESTE 5: NÓ DO LOCAL PRIMÁRIO DE ACIDENTE (NÓ 1)

   Estado de recurso antes de iniciar o teste:

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

   O pacemaker deve detetar o nó de aglomerado morto e cercar o nó. Assim que o nó estiver vedado, o Pacemaker irá desencadear uma aquisição da instância HANA. Quando o nó vedado for reiniciado, o Pacemaker não iniciará automaticamente.

   Executar os seguintes comandos para iniciar o Pacemaker, limpar as mensagens SBD para o nó hn1-db-0, registar o nó hn1-db-0 como secundário, e limpar o recurso falhado.

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

   Estado de recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TESTE 6: NÓ SECUNDÁRIO DO LOCAL DO ACIDENTE (NÓ 2)

   Estado de recurso antes de iniciar o teste:

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

   O pacemaker deve detetar o nó de aglomerado morto e cercar o nó. Assim que o nó estiver vedado, o Pacemaker irá desencadear uma aquisição da instância HANA. Quando o nó vedado for reiniciado, o Pacemaker não iniciará automaticamente.

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

   Estado de recurso após o teste:

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

   Estado de recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Executar os seguintes comandos\>como <hanasid adm no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   O pacemaker detetará a instância HANA parada e marcará o recurso como falhado no nó hn1-db-1. O pacemaker deve reiniciar automaticamente a instância HANA. Executar o seguinte comando para limpar o estado falhado.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado de recurso após o teste:

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

   Estado de recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Executar os seguintes comandos\>como <hanasid adm no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   O pacemaker detetará a instância HANA morta e marcará o recurso como falhado no nó hn1-db-1. Executar o seguinte comando para limpar o estado falhado. O pacemaker deve então reiniciar automaticamente a instância HANA.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado de recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TESTE 9: CRASH SecondARY SITE NÓDE (NÓ 2) EXECUÇÃO SEGUNDA BASE DE DADOS HANA

   Estado de recurso antes de iniciar o teste:

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

   O pacemaker deve detetar o nó de aglomerado morto e cercar o nó. Quando o nó vedado for reiniciado, o Pacemaker não iniciará automaticamente.

   Executar os seguintes comandos para iniciar o Pacemaker, limpe as mensagens SBD para o nó hn1-db-1 e limpe o recurso falhado.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado de recurso após o teste:

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

* [Planeamento e implementação de Máquinas Virtuais Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP][dbms-guide]

