---
title: Alta disponibilidade de SAP HANA em VMs Azure no RHEL Microsoft Docs
description: Estabelecer uma elevada disponibilidade de SAP HANA em máquinas virtuais Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/28/2020
ms.author: radeltch
ms.openlocfilehash: 5e3512ce86bdf96a5e6cfcf0e4459b656a5ac5bc
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565864"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Alta disponibilidade de SAP HANA em VMs Azure em Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533,]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[SAP 1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]: https://launchpad.support.sap.com/#/notes/2292690
[2455582]: https://launchpad.support.sap.com/#/notes/2455582
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

Para o desenvolvimento no local, pode utilizar a Replicação do Sistema HANA ou utilizar o armazenamento partilhado para estabelecer uma elevada disponibilidade para o SAP HANA.
Nas máquinas virtuais Azure (VMs), a replicação do sistema HANA no Azure é atualmente a única função de alta disponibilidade suportada.
A replicação do SAP HANA consiste num nó primário e pelo menos num nó secundário. As alterações aos dados do nó primário são replicadas ao nó secundário sincronicamente ou assíncronamente.

Este artigo descreve como implementar e configurar as máquinas virtuais, instalar a estrutura do cluster e instalar e configurar a Replicação do Sistema SAP HANA.
Nas configurações de exemplo, são utilizados comandos de instalação, instância número **03**e ID **HN1** do sistema HANA.

Leia primeiro as seguintes Notas e papéis SAP:

* Nota SAP [1928533,]que tem:
  * A lista dos tamanhos de VM Azure que são suportados para a implementação de software SAP.
  * Informações importantes sobre a capacidade para tamanhos de VM Azure.
  * O software SAP suportado e o sistema operativo (OS) e as combinações de bases de dados.
  * A versão necessária para o kernel SAP necessário para Windows e Linux no Microsoft Azure.
* O SAP Note [2015553] lista os pré-requisitos para implementações de software SAP suportadas pela SAP em Azure.
* SAP Note [2002167] recomendou definições de OS para Red Hat Enterprise Linux
* SAP Nota [2009879] tem Diretrizes SAP HANA para Red Hat Enterprise Linux
* O SAP Note [2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* O SAP Note [2191498] tem a versão necessária do Agente anfitrião SAP para o Linux em Azure.
* SAP Nota [2243692] tem informações sobre licenciamento SAP em Linux em Azure.
* A Nota [SAP 1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização avançada do Azure para sAP.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) exigiu todas as notas SAP para linux.
* [Planeamento e implementação de Máquinas Virtuais Azure para SAP em Linux][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP em Linux (este artigo)][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP em Linux][dbms-guide]
* [Replicação do sistema SAP HANA em cluster pacemaker](https://access.redhat.com/articles/3004101)
* Documentação Geral RHEL
  * [Visão geral de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração add-on de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência addon de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Documentação RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade RHEL - Máquinas Virtuais Microsoft Azure como Membros do Cluster](https://access.redhat.com/articles/3131341)
  * [Instalação e Configuração de um Cluster de Alta Disponibilidade do Red Hat Enterprise Linux 7.4 (e mais tarde) no Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Instale SAP HANA no Red Hat Enterprise Linux para utilização no Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Descrição geral

Para obter uma elevada disponibilidade, o SAP HANA está instalado em duas máquinas virtuais. Os dados são replicados utilizando a replicação do sistema HANA.

![Visão geral de alta disponibilidade do SAP HANA](./media/sap-hana-high-availability-rhel/ha-hana.png)

A configuração de replicação do sistema SAP HANA utiliza um nome de anfitrião virtual dedicado e endereços IP virtuais. No Azure, é necessário utilizar um endereço IP virtual. A lista que se segue mostra a configuração do equilibrista de carga:

* Configuração frontal: Endereço IP 10.0.0.13 para hn1-db
* Configuração de back-end: Ligada às interfaces de rede primária de todas as máquinas virtuais que devem fazer parte da Replicação do Sistema HANA
* Porta da sonda: Porto 62503
* Regras de equilíbrio de carga: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Implantação para Linux

O Azure Marketplace contém uma imagem para Red Hat Enterprise Linux 7.4 para SAP HANA que pode usar para implementar novas máquinas virtuais.

### <a name="deploy-with-a-template"></a>Implementar com um modelo

Você pode usar um dos modelos de arranque rápido que estão no GitHub para implementar todos os recursos necessários. O modelo implanta as máquinas virtuais, o equilibrador de carga, o conjunto de disponibilidade, e assim por diante.
Para implementar o modelo, siga estes passos:

1. Abra o modelo de base de [dados][template-multisid-db] no portal Azure.
1. Introduza os seguintes parâmetros:
    * **ID do sistema Sap**: Introduza o ID do sistema SAP do sistema SAP que pretende instalar. O ID é usado como prefixo para os recursos que são implantados.
    * **Tipo Os**: Selecione uma das distribuições linux. Para este exemplo, selecione **RHEL 7**.
    * **Tipo db**: Selecione **HANA**.
    * Tamanho do **sistema sap**: Introduza o número de SAPS que o novo sistema vai fornecer. Se não tem a certeza de quantos SAPS o sistema necessita, pergunte ao seu Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
    * **Disponibilidade do Sistema**: Selecione **HA**.
    * **Nome de utilizador, palavra-passe de administrador ou chave SSH**: É criado um novo utilizador que pode ser utilizado para iniciar sessão na máquina.
    * **ID sub-rede**: Se pretender implantar o VM numa VNet existente onde tenha uma sub-rede definida a VM deve ser atribuída, diga o nome da identificação dessa sub-rede específica. O ID geralmente parece **/subscrições/\<subscrição ID>/recursosGroups/\<nome do grupo de recursos>/providers/Microsoft.Network/virtualNetworks/\<nome de rede virtual>/subnets/\<subnet name>** . Deixe vazio, se quiser criar uma nova rede virtual

### <a name="manual-deployment"></a>Implantação manual

1. Crie um grupo de recursos.
1. Crie uma rede virtual
1. Crie um conjunto de disponibilidade.  
   Detete o domínio da atualização máxima.
1. Criar um equilibrador de carga (interno). Recomendamos um [equilíbrio de carga padrão.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)
   * Selecione a rede virtual criada no passo 2.
1. Criar a máquina virtual 1.  
   Utilize pelo menos a Red Hat Enterprise Linux 7.4 para sap HANA. Este exemplo utiliza o Red Hat Enterprise Linux 7.4 para a imagem sAP HANA <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Selecione o conjunto de disponibilidade criado no passo 3.
1. Criar a máquina virtual 2.  
   Utilize pelo menos a Red Hat Enterprise Linux 7.4 para sap HANA. Este exemplo utiliza o Red Hat Enterprise Linux 7.4 para a imagem sAP HANA <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Selecione o conjunto de disponibilidade criado no passo 3.
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
   1. Configure o equilibrador de carga. Primeiro, crie uma piscina IP frontal:

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

## <a name="install-sap-hana"></a>Instalar o SAP HANA

Os passos nesta secção utilizam os seguintes prefixos:

* **[A]** : O passo aplica-se a todos os nós.
* **[1]** : O passo aplica-se apenas ao nó 1.
* **[2]** : O passo aplica-se apenas ao nó 2 do cluster Pacemaker.

1. **[A]** Configurar o layout do disco: **Logical Volume Manager (LVM)** .

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

   Criar os volumes lógicos. É criado um volume linear quando se utiliza `lvcreate` sem o interruptor `-i`. Sugerimos que crie um volume listrado para um melhor desempenho em I/S, onde o argumento `-i` deve ser o número do volume físico subjacente. Neste documento, são utilizados dois volumes físicos para o volume de dados, pelo que o argumento `-i` switch está definido para **2**. Um volume físico é usado para o volume de registo, por isso nenhum interruptor de `-i` é explicitamente utilizado. Utilize o `-i` interruptor e detetete-o para o número do volume físico subjacente quando utilizar mais de um volume físico para cada dados, registo ou volumes partilhados.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
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

   Insira a seguinte linha no ficheiro `/etc/fstab`:

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

1. **[A]** RHEL para configuração HANA

   Configure o RHEL conforme descrito na Nota SAP [2292690] e [2455582] e <https://access.redhat.com/solutions/2447641>.

1. **[A]** Instalar o SAP HANA

   Para instalar a replicação do sistema SAP HANA, siga <https://access.redhat.com/articles/3004101>.

   * Execute o programa **hdblcm** do DVD HANA. Introduza os seguintes valores no momento:
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

1. **[A]** Configurar firewall

   Crie a regra da firewall para a porta de sonda de balanço de carga Azure.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Configure A Replicação do Sistema SAP HANA 2.0

Os passos nesta secção utilizam os seguintes prefixos:

* **[A]** : O passo aplica-se a todos os nós.
* **[1]** : O passo aplica-se apenas ao nó 1.
* **[2]** : O passo aplica-se apenas ao nó 2 do cluster Pacemaker.

1. **[A]** Configurar firewall

   Crie regras de firewall para permitir a replicação do sistema HANA e o tráfego do cliente. As portas necessárias estão listadas nas [portas TCP/IP de todos os produtos SAP](https://help.sap.com/viewer/ports). Os seguintes comandos são apenas um exemplo para permitir a replicação do sistema HANA 2.0 e o tráfego do cliente para a base de dados SYSTEMDB, HN1 e NW1.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** Criar a base de dados dos inquilinos.

   Se estiver a utilizar o SAP HANA 2.0 ou o MDC, crie uma base de dados de inquilinos para o seu sistema SAP NetWeaver. Substitua o **NW1** pelo SID do seu sistema SAP.

   Execute como <hanasid\>adm o seguinte comando:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Configurar a replicação do sistema no primeiro nó:

   Faça backup das bases de dados como <hanasid\>adm:

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
    
   Registe o segundo nó para iniciar a replicação do sistema. Executar o seguinte comando como <hanasid\>adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]** Verificar o estado da replicação

   Verifique o estado da replicação e aguarde até que todas as bases de dados estejam sincronizadas. Se o estado permanecer DESCONHECIDO, verifique as definições da firewall.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Configure A Replicação do Sistema SAP HANA 1.0

Os passos nesta secção utilizam os seguintes prefixos:

* **[A]** : O passo aplica-se a todos os nós.
* **[1]** : O passo aplica-se apenas ao nó 1.
* **[2]** : O passo aplica-se apenas ao nó 2 do cluster Pacemaker.

1. **[A]** Configurar firewall

   Crie regras de firewall para permitir a replicação do sistema HANA e o tráfego do cliente. As portas necessárias estão listadas nas [portas TCP/IP de todos os produtos SAP](https://help.sap.com/viewer/ports). Os seguintes comandos são apenas um exemplo para permitir a replicação do sistema HANA 2.0. Adapte-o à sua instalação SAP HANA 1.0.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

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

   Crie o site principal como <hanasid\>adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configurar a replicação do sistema no nó secundário.

   Registe o site secundário como <hanasid\>adm:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Criar um cluster Pacemaker

Siga os passos na configuração do [Pacemaker no Red Hat Enterprise Linux em Azure](high-availability-guide-rhel-pacemaker.md) para criar um cluster pacemaker básico para este servidor HANA.

## <a name="create-sap-hana-cluster-resources"></a>Criar recursos de cluster SAP HANA

Instale os agentes de recursos SAP HANA em **todos os nós**. Certifique-se de que ativa um repositório que contenha a embalagem.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Em seguida, crie a topologia HANA. Executar os seguintes comandos num dos nós do cluster Pacemaker:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
--clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Em seguida, crie os recursos HANA:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante em que nó dos recursos estão a funcionar.

> [!NOTE]
> Os intervalos na configuração acima são apenas exemplos e podem ter de ser adaptados à configuração específica da HANA. Por exemplo, pode ser necessário aumentar o tempo de início, se demorar mais tempo a iniciar a base de dados SAP HANA.  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta secção descreve como pode testar a sua configuração. Antes de iniciar um teste, certifique-se de que o Pacemaker não tem qualquer ação falhada (via estado do PC), não existem restrições de localização inesperadas (por exemplo, restos de um teste de migração) e que hana é estado de sincronização, por exemplo com sistemaReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Testar a migração

Estado de recurso antes de iniciar o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Pode migrar o nó mestre SAP HANA executando o seguinte comando:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Se definir `AUTOMATED_REGISTER="false"`, este comando deve migrar o nó principal SAP HANA e o grupo que contém o endereço IP virtual para hn1-db-1.

Uma vez que a migração é feita, a saída 'sudo pcs status' parece-se com esta

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

O recurso SAP HANA no hn1-db-0 está parado. Neste caso, configure a instância HANA como secundária executando este comando:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

A migração cria constrangimentos de localização que precisam de ser novamente eliminados:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Monitorize o estado do recurso HANA utilizando o 'estado do pcs'. Uma vez que HANA é iniciado em hn1-db-0, a saída deve ser assim

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Teste o agente de esgrima Azure

Estado de recurso antes de iniciar o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Pode testar a configuração do agente de esgrima Azure desativando a interface de rede no nó onde o SAP HANA está a funcionar como Mestre.
Consulte o [artigo 79523](https://access.redhat.com/solutions/79523) da Base de Conhecimento do Chapéu Vermelho para obter uma descrição sobre como simular uma falha de rede. Neste exemplo usamos o roteiro net_breaker para bloquear todos os acessos à rede.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

A máquina virtual deve agora reiniciar ou parar dependendo da configuração do seu cluster.
Se definir o `stonith-action` desligação, a máquina virtual é interrompida e os recursos são migrados para a máquina virtual em execução.

> [!NOTE]
> Pode levar até 15 minutos até as máquinas virtuais estarem novamente online.

Depois de relançar a máquina virtual, o recurso SAP HANA não começa como secundário se `AUTOMATED_REGISTER="false"`configurar . Neste caso, configure a instância HANA como secundária executando este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Estado de recurso após o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Teste uma falha manual

Estado de recurso antes de iniciar o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Pode testar uma falha manual parando o cluster no nó hn1-db-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Depois da falha, podes recomeçar o agrupamento. Se definir `AUTOMATED_REGISTER="false"`, o recurso SAP HANA no nó hn1-db-0 não começa como secundário. Neste caso, configure a instância HANA como secundária executando este comando:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Estado de recurso após o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

## <a name="next-steps"></a>Passos seguintes

* [Planeamento e implementação de Máquinas Virtuais Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP][dbms-guide]
* Para aprender como estabelecer alta disponibilidade e plano para recuperação de desastres de SAP HANA em Azure (grandes instâncias), consulte [SAP HANA (grandes instâncias) alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md)
