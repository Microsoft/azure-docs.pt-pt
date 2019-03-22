---
title: Configurar a replicação do sistema do SAP HANA em máquinas virtuais do Azure (VMs) | Documentos da Microsoft
description: Estabelece uma elevada disponibilidade do SAP HANA em máquinas virtuais do Azure (VMs).
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
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: b67a65bad06560a09d2ead88bd20f0568f749bb3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58082182"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Elevada disponibilidade do SAP HANA em VMs do Azure no Red Hat Enterprise Linux

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

Para o desenvolvimento no local, pode utilizar qualquer um dos HANA System Replication ou utilizar o armazenamento partilhado para estabelecer a elevada disponibilidade para SAP HANA.
Nas máquinas virtuais do Azure (VMs), HANA System Replication no Azure está atualmente que a única função de elevada disponibilidade de suporte.
SAP HANA replicação consiste num nó primário e pelo menos um nó secundário. As alterações dos dados no nó principal são replicadas para o nó secundário de forma síncrona ou assíncrona.

Este artigo descreve como implementar e configurar as máquinas virtuais, instalar o framework de cluster e instalar e configurar a replicação de sistema do SAP HANA.
As configurações de exemplo, comandos de instalação, o número de instâncias **03**e o ID de sistema HANA **HN1** são utilizados.

Leia primeiro o SAP Notes e os documentos seguintes:

* A nota SAP [1928533], que tem:
  * A lista de tamanhos de VM do Azure que são suportados para a implementação de software da SAP.
  * Informações de capacidade importante para os tamanhos de VM do Azure.
  * O software suportado do SAP e sistema operativo (SO) e combinações de base de dados.
  * A versão de kernel SAP necessária para Windows e Linux no Microsoft Azure.
* A nota SAP [2015553] apresenta uma lista de pré-requisitos para implementações de software SAP suportadas para SAP no Azure.
* A nota SAP [2002167] recomendado configurações do sistema operacional para Red Hat Enterprise Linux
* A nota SAP [2009879] tem diretrizes do SAP HANA para Red Hat Enterprise Linux
* A nota SAP [2178632] tem informações detalhadas sobre todas as monitorizações métricas comunicadas para o SAP no Azure.
* A nota SAP [2191498] tem a versão necessária do agente de anfitrião do SAP para o Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento de SAP no Linux no Azure.
* A nota SAP [1999351] tem informações adicionais de resolução de problemas avançada de monitorização a extensão do Azure para SAP.
* [WIKI de Comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as necessárias SAP Notes para o Linux.
* [Máquinas de virtuais de planeamento e implementação para o SAP no Linux do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP no Linux (Este artigo)][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [Replicação de sistema de SAP HANA num cluster de pacemaker](https://access.redhat.com/articles/3004101)
* Documentação geral RHEL
  * [Descrição geral do suplemento de elevada disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração de complemento de elevada disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de complemento de elevada disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Documentação do RHEL específica do Azure:
  * [Políticas de suporte para Clusters de elevada disponibilidade do RHEL - máquinas de virtuais do Microsoft Azure como membros do Cluster](https://access.redhat.com/articles/3131341)
  * [Instalar e configurar um Cluster de elevada disponibilidade Linux 7.4 (e posterior) de Enterprise do Red Hat no Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Instalar o SAP HANA no Red Hat Enterprise Linux para utilização no Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Descrição geral

Para assegurar elevada disponibilidade, o SAP HANA está instalado em duas máquinas virtuais. Os dados são replicados através do HANA System Replication.

![Descrição geral de elevada disponibilidade do SAP HANA](./media/sap-hana-high-availability-rhel/ha-hana.png)

Utiliza a configuração do SAP HANA System Replication endereços um nome de anfitrião virtual dedicado e virtual IP. No Azure, um balanceador de carga é necessário utilizar um endereço IP virtual. A lista seguinte mostra a configuração de Balanceador de carga:

* Configuração de front-end: Endereço IP 10.0.0.13 para hn1-db
* Configuração de back-end: Ligado a interfaces de rede primário de todas as máquinas virtuais que devem fazer parte do HANA System Replication
* Porta de pesquisa: Port 62503
* Regras de balanceamento de carga: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Implementar para Linux

O Azure Marketplace contém uma imagem do Red Hat Enterprise Linux 7.4 para o SAP HANA que pode utilizar para implementar novas máquinas virtuais.

### <a name="deploy-with-a-template"></a>Implementar com um modelo

Pode utilizar um dos modelos de início rápido estão disponíveis no GitHub para implementar todos os recursos necessários. O modelo implementa as máquinas virtuais, o Balanceador de carga, o conjunto de disponibilidade e assim por diante.
Para implementar o modelo, siga estes passos:

1. Abra o [modelo de base de dados] [ template-multisid-db] no portal do Azure.
1. Introduza os seguintes parâmetros:
    * **ID de sistema de SAP**: Introduza o ID de sistema SAP do sistema SAP que pretende instalar. O ID é utilizado como um prefixo para os recursos que são implementados.
    * **Tipo de SO**: Selecione uma das distribuições de Linux. Para este exemplo, selecione **RHEL 7**.
    * **Tipo de BD**: Selecione **HANA**.
    * **Tamanho do sistema de SAP**: Introduza o número de SAPS que irá fornecer o novo sistema. Se não tiver certeza SAPS quantos requer que o sistema, peça ao seu parceiro de tecnologia de SAP ou integrador de sistemas.
    * **Disponibilidade do sistema**: Selecione **HA**.
    * **Chave de nome de utilizador administrador, a palavra-passe de administrador ou SSH**: Um novo utilizador é criado que pode ser utilizado para iniciar sessão máquina.
    * **ID de sub-rede**: Se pretender implementar a VM para uma VNet já existente em que tem uma sub-rede definida a VM deve ser atribuída para nomear o ID dessa sub-rede. O ID, normalmente, é semelhante **/subscriptions/\<ID da subscrição > /resourceGroups/\<nome do grupo de recursos > /providers/Microsoft.Network/virtualNetworks/\<nome da rede virtual > /subnets/ \<nome da sub-rede >**. Deixe em branco, se quiser criar uma nova rede virtual

### <a name="manual-deployment"></a>Implementação manual

1. Crie um grupo de recursos.
1. Crie uma rede virtual
1. Crie um conjunto de disponibilidade.  
   Defina o domínio de atualização máx.
1. Crie um balanceador de carga (interno).
   * Selecione a rede virtual criada no passo 2.
1. Crie máquina virtual 1.  
   Utilize, pelo menos, Red Hat Enterprise Linux 7.4 para o SAP HANA. Este exemplo utiliza o Red Hat Enterprise Linux 7.4 para a imagem de SAP HANA <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> selecione conjunto criada no passo 3 de disponibilidade.
1. Crie a máquina virtual 2.  
   Utilize, pelo menos, Red Hat Enterprise Linux 7.4 para o SAP HANA. Este exemplo utiliza o Red Hat Enterprise Linux 7.4 para a imagem de SAP HANA <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> selecione conjunto criada no passo 3 de disponibilidade.
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

> [!IMPORTANT]
> Não ative carimbos de data / TCP em VMs do Azure colocadas atrás do Balanceador de carga do Azure. Ativar TCP carimbos fará com que as sondas de estado de funcionamento efetuar a ativação. Defina o parâmetro **net.ipv4.tcp_timestamps** ao **0**. Para obter detalhes, consulte [sondas de estado de funcionamento do Balanceador de carga](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview).
> A nota SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421) atualmente contém uma instrução contraditórios, aconselhá-lhe definir net.ipv4.tcp_timestamps como 1. Para as VMs do Azure colocadas atrás do Balanceador de carga do Azure, defina o parâmetro **net.ipv4.tcp_timestamps** ao **0**.

## <a name="install-sap-hana"></a>Instalar o SAP HANA

Os passos nesta secção, utilize os prefixos seguintes:

* **[A]**: A etapa se aplica a todos os nós.
* **[1]**: A etapa se aplica ao nó 1 apenas.
* **[2]**: A etapa se aplica a 2 de nó do cluster Pacemaker apenas.

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

1. **[A]**  RHEL para a configuração do HANA

   Configurar RHEL, conforme descrito na nota SAP [2292690] e [2455582] e <https://access.redhat.com/solutions/2447641>.

1. **[A]**  Instalar o SAP HANA

   Para instalar o SAP HANA System Replication, siga <https://access.redhat.com/articles/3004101>.

   * Executar o **hdblcm** programa a partir do DVD do HANA. Introduza os seguintes valores na linha de comandos:
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

1. **[A]**  Configurar a firewall

   Crie a regra de firewall para a porta de sonda de loadbalancer do Azure.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Configurar a replicação de sistema 2.0 do SAP HANA

Os passos nesta secção, utilize os prefixos seguintes:

* **[A]**: A etapa se aplica a todos os nós.
* **[1]**: A etapa se aplica ao nó 1 apenas.
* **[2]**: A etapa se aplica a 2 de nó do cluster Pacemaker apenas.

1. **[A]**  Configurar a firewall

   Crie regras de firewall para permitir o tráfego de replicação do sistema HANA e o cliente. As portas necessárias estão listadas na [portas TCP/IP de todos os produtos SAP](https://help.sap.com/viewer/ports). Os comandos seguintes são apenas um exemplo para permitir tráfego de replicação do sistema HANA 2.0 e o cliente para base de dados SYSTEMDB, HN1 e NW1.

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

1. **[1]**  Verificar estado de replicação

   Verifique o estado de replicação e aguarda até que todas as bases de dados estejam em sincronia. Se o estado permanece desconhecido, verifique as definições de firewall.

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

## <a name="configure-sap-hana-10-system-replication"></a>Configurar a replicação de sistema 1.0 do SAP HANA

Os passos nesta secção, utilize os prefixos seguintes:

* **[A]**: A etapa se aplica a todos os nós.
* **[1]**: A etapa se aplica ao nó 1 apenas.
* **[2]**: A etapa se aplica a 2 de nó do cluster Pacemaker apenas.

1. **[A]**  Configurar a firewall

   Crie regras de firewall para permitir o tráfego de replicação do sistema HANA e o cliente. As portas necessárias estão listadas na [portas TCP/IP de todos os produtos SAP](https://help.sap.com/viewer/ports). Os comandos seguintes são apenas um exemplo para permitir a replicação do sistema HANA 2.0. Adaptá-lo à sua instalação de SAP HANA 1.0.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

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

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Criar um cluster de Pacemaker

Siga os passos em [Pacemaker no Red Hat Enterprise Linux no Azure a configurar](high-availability-guide-rhel-pacemaker.md) para criar um cluster de Pacemaker básico para este servidor do HANA.

## <a name="create-sap-hana-cluster-resources"></a>Criar recursos de cluster do SAP HANA

Instalar os agentes de recursos do SAP HANA num **todos os nós**. Certifique-se a ativação de um repositório que contém o pacote.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Em seguida, crie a topologia do HANA. Execute os seguintes comandos em um de nós do cluster Pacemaker:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> --clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Em seguida, crie os recursos do HANA:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante no nó que os recursos estão em execução.

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

## <a name="test-the-cluster-setup"></a>Teste a configuração de cluster

Esta secção descreve como pode testar a configuração. Antes de iniciar um teste, certifique-se de que Pacemaker não tem qualquer ação com falhas (por meio de status de pcs), não há nenhuma restrição de localização inesperados (por exemplo sobras de um teste de migração) e que HANA é o estado de sincronização como, por exemplo, com systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>A migração de teste

Estado do recurso antes de iniciar o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Pode migrar do nó principal do SAP HANA, executando o seguinte comando:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Se definir `AUTOMATED_REGISTER="false"`, este comando deverá migrar do nó principal do SAP HANA e o grupo que contém o endereço IP virtual para hn1-db-1.

Depois de fazer a migração, a saída de "sudo pcs status" fica assim

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

O recurso de SAP HANA no hn1-db-0 está parado. Neste caso, configure a instância HANA como secundário ao executar este comando:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

A migração cria as restrições de localização têm de ser eliminadas novamente:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Monitorize o estado do recurso HANA a utilizar o "Estado de pcs". Depois de HANA é iniciado em hn1-db-0, o resultado deverá ser semelhante a esta

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testar o agente de delimitação por barreiras do Azure

Estado do recurso antes de iniciar o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Pode testar a configuração do agente do Azure de delimitação por barreiras, desativando a interface de rede no nó onde SAP HANA está em execução como modelo.
Ver [artigo da base de conhecimento do Red Hat 79523](https://access.redhat.com/solutions/79523) para obter uma descrição sobre como simular uma falha de rede. Neste exemplo utilizamos o script de net_breaker para bloquear todo o acesso à rede.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

Agora, a máquina virtual deve reiniciar ou parar consoante a configuração de cluster.
Se definir o `stonith-action` definir como desativado, a máquina virtual é parada e os recursos são migrados para a máquina virtual em execução.

> [!NOTE]
> Pode demorar até 15 minutos até que as máquinas virtuais esteja novamente online.

Depois de iniciar a máquina virtual novamente, o recurso de SAP HANA não for possível iniciar como secundária se definir `AUTOMATED_REGISTER="false"`. Neste caso, configure a instância HANA como secundário ao executar este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Estado do recurso depois do teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Testar uma ativação pós-falha manual

Estado do recurso antes de iniciar o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Pode testar uma ativação pós-falha manual ao parar o cluster no nó hn1-db-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Após a ativação pós-falha, pode iniciar o cluster novamente. Se definir `AUTOMATED_REGISTER="false"`, o recurso de SAP HANA no nó hn1-db-0 não consegue iniciar como secundária. Neste caso, configure a instância HANA como secundário ao executar este comando:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Estado do recurso depois do teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

## <a name="next-steps"></a>Passos Seguintes

* [Máquinas de virtuais de planeamento e implementação de SAP do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a elevada disponibilidade e o plano de recuperação após desastre do SAP HANA no Azure (instâncias grandes), veja [SAP HANA (instâncias grandes) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md)
