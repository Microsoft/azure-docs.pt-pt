---
title: Configurar a replicação do sistema SAP HANA em VMs (máquinas virtuais) do Azure | Microsoft Docs
description: Estabeleça alta disponibilidade de SAP HANA em VMs (máquinas virtuais) do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: f51870fb8f6ed71aab2558099c2361bf6e340493
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078505"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Alta disponibilidade de SAP HANA em VMs do Azure no Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]: https://launchpad.support.sap.com/#/notes/2292690
[2455582]: https://launchpad.support.sap.com/#/notes/2455582
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

Para o desenvolvimento local, você pode usar a replicação de sistema do HANA ou usar o armazenamento compartilhado para estabelecer alta disponibilidade para SAP HANA.
Em VMs (máquinas virtuais) do Azure, a replicação de sistema do HANA no Azure é a única função de alta disponibilidade com suporte no momento.
SAP HANA replicação consiste em um nó primário e pelo menos um nó secundário. As alterações nos dados no nó primário são replicadas para o nó secundário de forma síncrona ou assíncrona.

Este artigo descreve como implantar e configurar as máquinas virtuais, instalar a estrutura do cluster e instalar e configurar SAP HANA replicação do sistema.
No exemplo configurações, comandos de instalação, número de instância **03**e **HN1** de ID do sistema Hana são usados.

Leia as seguintes notas e documentos SAP primeiro:

* Nota SAP [1928533], que tem:
  * A lista de tamanhos de VM do Azure com suporte para a implantação do software SAP.
  * Informações de capacidade importantes para tamanhos de VM do Azure.
  * O software SAP com suporte e as combinações de sistema operacional e banco de dados.
  * A versão de kernel do SAP necessária para Windows e Linux em Microsoft Azure.
* O SAP Note [2015553] lista os pré-requisitos para implantações de software SAP com suporte no SAP no Azure.
* A observação do SAP [2002167] tem as configurações do sistema operacional recomendadas para Red Hat Enterprise Linux
* A observação do SAP [2009879] tem diretrizes SAP HANA para Red Hat Enterprise Linux
* A nota SAP [2178632] tem informações detalhadas sobre todas as métricas de monitoramento relatadas para SAP no Azure.
* A nota SAP [2191498] tem a versão do agente de host do SAP necessária para Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento SAP no Linux no Azure.
* A nota SAP [1999351] tem informações adicionais para solução de problemas para a extensão de monitoramento avançado do Azure para SAP.
* O [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [Planejamento e implementação de máquinas virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP no Linux (este artigo)][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [SAP HANA a replicação do sistema no cluster pacemaker](https://access.redhat.com/articles/3004101)
* Documentação geral do RHEL
  * [Visão geral do complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Documentação do RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade RHEL-Máquinas Virtuais do Microsoft Azure como membros do cluster](https://access.redhat.com/articles/3131341)
  * [Instalando e configurando um cluster de alta disponibilidade Red Hat Enterprise Linux 7,4 (e posterior) no Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Instalar o SAP HANA no Red Hat Enterprise Linux para uso no Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Descrição geral

Para obter alta disponibilidade, o SAP HANA é instalado em duas máquinas virtuais. Os dados são replicados usando a replicação de sistema do HANA.

![Visão geral de SAP HANA alta disponibilidade](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA configuração de replicação do sistema usa um nome de host virtual dedicado e endereços IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. A lista a seguir mostra a configuração do balanceador de carga:

* Configuração de front-end: Endereço IP 10.0.0.13 para hn1-DB
* Configuração de back-end: Conectado a interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte da replicação do sistema HANA
* Porta de investigação: Porta 62503
* Regras de balanceamento de carga: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Implantar para Linux

O Azure Marketplace contém uma imagem para Red Hat Enterprise Linux 7,4 para SAP HANA que você pode usar para implantar novas máquinas virtuais.

### <a name="deploy-with-a-template"></a>Implantar com um modelo

Você pode usar um dos modelos de início rápido que estão no GitHub para implantar todos os recursos necessários. O modelo implanta as máquinas virtuais, o balanceador de carga, o conjunto de disponibilidade e assim por diante.
Para implantar o modelo, siga estas etapas:

1. Abra o [modelo de banco de dados][template-multisid-db] no portal do Azure.
1. Insira os seguintes parâmetros:
    * **ID do sistema SAP**: Insira a ID do sistema SAP do sistema SAP que você deseja instalar. A ID é usada como um prefixo para os recursos que são implantados.
    * **Tipo de so**: Selecione uma das distribuições do Linux. Para este exemplo, selecione **RHEL 7**.
    * **Tipo de BD**: Selecione **Hana**.
    * **Tamanho do sistema SAP**: Insira o número de SAPS que o novo sistema vai fornecer. Se você não tiver certeza de quantos SAPS o sistema precisará, pergunte ao seu parceiro de tecnologia SAP ou ao integrador de sistemas.
    * **Disponibilidade do sistema**: Selecione **ha**.
    * **Nome de usuário de administrador, senha de administrador ou chave SSH**: Um novo usuário é criado e pode ser usado para entrar no computador.
    * **ID da sub-rede**: Se você deseja implantar a VM em uma VNet existente em que você tem uma sub-rede definida, a VM deve ser atribuída, nomear a ID dessa sub-rede específica. A ID geralmente se parece com a **ID de\<assinatura/subscriptions/> nome do grupo\<de recursos/resourceGroups/\<>/Providers/Microsoft.Network/virtualNetworks/nome da rede virtual >/Subnets/\<nome da sub-rede >** . Deixe em branco, se você quiser criar uma nova rede virtual

### <a name="manual-deployment"></a>Implantação manual

1. Crie um grupo de recursos.
1. Crie uma rede virtual
1. Crie um conjunto de disponibilidade.  
   Defina o domínio de atualização máx.
1. Crie um balanceador de carga (interno).
   * Selecione a rede virtual criada na etapa 2.
1. Crie a máquina virtual 1.  
   Use pelo menos Red Hat Enterprise Linux 7,4 para SAP HANA. Este exemplo usa o Red Hat Enterprise Linux 7,4 para a imagem <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> SAP Hana selecione o conjunto de disponibilidade criado na etapa 3.
1. Crie a máquina virtual 2.  
   Use pelo menos Red Hat Enterprise Linux 7,4 para SAP HANA. Este exemplo usa o Red Hat Enterprise Linux 7,4 para a imagem <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> SAP Hana selecione o conjunto de disponibilidade criado na etapa 3.
1. Adicionar discos de dados.
1. Configure o balanceador de carga. Primeiro, crie um pool de IPS de front-end:

   1. Abra o balanceador de carga, selecione **pool de IPS de front-end**e selecione **Adicionar**.
   1. Insira o nome do novo pool de IPS de front-end (por exemplo, **Hana-frontend**).
   1. Defina a **atribuição** como **estática** e insira o endereço IP (por exemplo, **10.0.0.13**).
   1. Selecione **OK**.
   1. Depois que o novo pool de IPS de front-end for criado, observe o endereço IP do pool.

1. Em seguida, crie um pool de back-ends:

   1. Abra o balanceador de carga, selecione pools de **back-end**e selecione **Adicionar**.
   1. Insira o nome do novo pool de back-end (por exemplo, **Hana-backend**).
   1. Selecione **Adicionar uma máquina virtual**.
   1. Selecione o conjunto de disponibilidade criado na etapa 3.
   1. Selecione as máquinas virtuais do cluster de SAP HANA.
   1. Selecione **OK**.

1. Em seguida, crie uma investigação de integridade:

   1. Abra o balanceador de carga, selecione **investigações de integridade**e selecione **Adicionar**.
   1. Insira o nome da nova investigação de integridade (por exemplo, **Hana-HP**).
   1. Selecione **TCP** como o protocolo e a porta 625**03**. Mantenha o valor de **intervalo** definido como 5 e o valor de **limite não íntegro** definido como 2.
   1. Selecione **OK**.

1. Para SAP HANA 1,0, crie as regras de balanceamento de carga:

   1. Abra o balanceador de carga, selecione **regras de balanceamento de carga**e selecione **Adicionar**.
   1. Insira o nome da nova regra do balanceador de carga (por exemplo, Hana-lb-3**03**15).
   1. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **Hana-frontend**).
   1. Mantenha o **protocolo** definido como **TCP**e insira a porta 3**03**15.
   1. Aumente o **tempo limite de ociosidade** para 30 minutos.
   1. Certifique-se de **habilitar o IP flutuante**.
   1. Selecione **OK**.
   1. Repita essas etapas para a porta 3**03**17.

1. Para SAP HANA 2,0, crie as regras de balanceamento de carga para o banco de dados do sistema:

   1. Abra o balanceador de carga, selecione **regras de balanceamento de carga**e selecione **Adicionar**.
   1. Insira o nome da nova regra do balanceador de carga (por exemplo, Hana-lb-3**03**13).
   1. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **Hana-frontend**).
   1. Mantenha o **protocolo** definido como **TCP**e insira a porta 3**03**13.
   1. Aumente o **tempo limite de ociosidade** para 30 minutos.
   1. Certifique-se de **habilitar o IP flutuante**.
   1. Selecione **OK**.
   1. Repita essas etapas para a porta 3**03**14.

1. Para SAP HANA 2,0, primeiro crie as regras de balanceamento de carga para o banco de dados de locatário:

   1. Abra o balanceador de carga, selecione **regras de balanceamento de carga**e selecione **Adicionar**.
   1. Insira o nome da nova regra do balanceador de carga (por exemplo, Hana-lb-3**03**40).
   1. Selecione o endereço IP de front-end, o pool de back-ends e a investigação de integridade que você criou anteriormente (por exemplo, **Hana-frontend**).
   1. Mantenha o **protocolo** definido como **TCP**e insira a porta 3**03**40.
   1. Aumente o **tempo limite de ociosidade** para 30 minutos.
   1. Certifique-se de **habilitar o IP flutuante**.
   1. Selecione **OK**.
   1. Repita essas etapas para as portas 3**03**41 e 3**03**42.

Para obter mais informações sobre as portas necessárias para SAP HANA, leia o capítulo [conexões a bancos de dados de locatário](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) no guia [SAP Hana bancos de dados](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) de locatários ou [SAP Note 2388694][2388694].

> [!IMPORTANT]
> Não habilite carimbos de data/hora TCP em VMs do Azure colocadas por trás Azure Load Balancer. Habilitar carimbos de data/hora TCP fará com que as investigações de integridade falhem. Defina o parâmetro **net. IPv4. TCP _timestamps** como **0**. Para obter detalhes, consulte [Load Balancer investigações de integridade](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).
> Consulte também SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>Instalar o SAP HANA

As etapas nesta seção usam os seguintes prefixos:

* **[A]** : A etapa se aplica a todos os nós.
* **[1]** : A etapa se aplica somente ao nó 1.
* **[2]** : A etapa se aplica somente ao nó 2 do cluster pacemaker.

1. **[A]** configurar o layout do disco: **LVM (Gerenciador de volumes lógicos)** .

   Recomendamos que você use LVM para volumes que armazenam dados e arquivos de log. O exemplo a seguir pressupõe que as máquinas virtuais têm quatro discos de dados anexados que são usados para criar dois volumes.

   Listar todos os discos disponíveis:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Exemplo de saída:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Crie volumes físicos para todos os discos que você deseja usar:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Crie um grupo de volumes para os arquivos de dados. Use um grupo de volumes para os arquivos de log e um para o diretório compartilhado do SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Crie os volumes lógicos. Um volume linear é criado quando você usa `lvcreate` o sem `-i` a opção. Sugerimos que você crie um volume distribuído para melhorar o desempenho de e/s, em `-i` que o argumento deve ser o número do volume físico subjacente. Neste documento, dois volumes físicos são usados para o volume de dados, portanto, `-i` o argumento switch é definido como **2**. Um volume físico é usado para o volume de log, portanto `-i` , nenhuma opção é usada explicitamente. Use a `-i` opção e defina-a como o número do volume físico subjacente quando você usar mais de um volume físico para cada dados, log ou volumes compartilhados.

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

   Crie `fstab` entradas para os três volumes lógicos:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Insira a seguinte linha no `/etc/fstab` arquivo:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Monte os novos volumes:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** configurar o layout do disco: **Discos simples**.

   Para sistemas de demonstração, você pode posicionar os arquivos de log e dados do HANA em um disco. Crie uma partição em/dev/disk/Azure/scsi1/lun0 e formate-a com xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Insira esta linha no arquivo/etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Crie o diretório de destino e monte o disco:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** configurar a resolução de nomes de host para todos os hosts.

   Você pode usar um servidor DNS ou modificar o arquivo/etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo/etc/hosts.
   Substitua o endereço IP e o nome do host nos seguintes comandos:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as linhas a seguir no arquivo/etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL para configuração do Hana

   Configure o RHEL conforme descrito em SAP Note [2292690] e [2455582] e <https://access.redhat.com/solutions/2447641>.

1. **[A]** instalar o SAP Hana

   Para instalar SAP HANA replicação do sistema, <https://access.redhat.com/articles/3004101>siga.

   * Execute o programa **hdblcm** do DVD do Hana. Insira os seguintes valores no prompt:
   * Escolha a instalação: Insira **1**.
   * Selecione componentes adicionais para instalação: Insira **1**.
   * Insira o caminho de instalação [/Hana/Shared]: Selecione Enter.
   * Insira o nome do host local [..]: Selecione Enter.
   * Deseja adicionar hosts adicionais ao sistema? (s/n) [n]: Selecione Enter.
   * Insira SAP HANA ID do sistema: Insira o SID do HANA, por exemplo: **HN1**.
   * Insira o número da instância [00]: Insira o número da instância do HANA. Insira **03** se você usou o modelo do Azure ou seguiu a seção implantação manual deste artigo.
   * Selecione o modo de banco de dados/insira o índice [1]: Selecione Enter.
   * Selecione uso do sistema/inserir índice [4]: Selecione o valor uso do sistema.
   * Insira o local dos volumes de dados [/hana/data/HN1]: Selecione Enter.
   * Insira o local dos volumes de log [/hana/log/HN1]: Selecione Enter.
   * Restringir a alocação máxima de memória? [n]: Selecione Enter.
   * Insira o nome do host do certificado para o host '... ' [...]: Selecione Enter.
   * Insira a senha do usuário do agente de host SAP (sapadm): Insira a senha de usuário do agente do host.
   * Confirmar senha do usuário do agente de host SAP (sapadm): Insira a senha de usuário do agente de host novamente para confirmar.
   * Insira a senha do administrador do sistema (hdbadm): Insira a senha de administrador do sistema.
   * Confirmar senha do administrador do sistema (hdbadm): Digite a senha do administrador do sistema novamente para confirmar.
   * Insira o diretório base do administrador do sistema [/usr/sap/HN1/home]: Selecione Enter.
   * Insira o Shell de logon do administrador do sistema [/bin/sh]: Selecione Enter.
   * Insira a ID de usuário do administrador do sistema [1001]: Selecione Enter.
   * Inserir ID do grupo de usuários (SAPs) [79]: Selecione Enter.
   * Insira a senha do usuário do banco de dados (sistema): Insira a senha de usuário do banco de dados.
   * Confirmar senha do usuário do banco de dados (sistema): Insira a senha de usuário do banco de dados novamente para confirmar.
   * Reiniciar o sistema após a reinicialização do computador? [n]: Selecione Enter.
   * Deseja continuar? (s/n): Valide o resumo. Digite **y** para continuar.

1. **[A]** atualizar o agente de host do SAP.

   Baixe o arquivo mais recente do agente de host do SAP no [centro de software SAP][sap-swcenter] e execute o comando a seguir para atualizar o agente. Substitua o caminho do arquivo morto para apontar para o arquivo que você baixou:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]** configurar o firewall

   Crie a regra de firewall para a porta de investigação do Azure Load Balancer.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Configurar a replicação do sistema SAP HANA 2,0

As etapas nesta seção usam os seguintes prefixos:

* **[A]** : A etapa se aplica a todos os nós.
* **[1]** : A etapa se aplica somente ao nó 1.
* **[2]** : A etapa se aplica somente ao nó 2 do cluster pacemaker.

1. **[A]** configurar o firewall

   Crie regras de firewall para permitir a replicação de sistema do HANA e o tráfego do cliente. As portas necessárias são listadas nas [portas TCP/IP de todos os produtos SAP](https://help.sap.com/viewer/ports). Os comandos a seguir são apenas um exemplo para permitir a replicação do sistema HANA 2,0 e o tráfego do cliente para o banco de dados SYSTEMDB, HN1 e NW1.

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

1. **[1]** criar o banco de dados de locatário.

   Se você estiver usando SAP HANA 2,0 ou MDC, crie um banco de dados de locatário para seu sistema SAP NetWeaver. Substitua **NW1** pelo SID do seu sistema SAP.

   Execute as < hanasid\>ADM o seguinte comando:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** configurar a replicação do sistema no primeiro nó:

   Faça backup dos bancos de dados como <\>hanasid ADM:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Copie os arquivos de PKI do sistema para o site secundário:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Criar o site primário:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** configurar a replicação do sistema no segundo nó:
    
   Registre o segundo nó para iniciar a replicação do sistema. Execute o seguinte comando como < hanasid\>ADM:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]** verificar status de replicação

   Verifique o status de replicação e aguarde até que todos os bancos de dados estejam em sincronia. Se o status permanecer desconhecido, verifique as configurações do firewall.

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

## <a name="configure-sap-hana-10-system-replication"></a>Configurar a replicação do sistema SAP HANA 1,0

As etapas nesta seção usam os seguintes prefixos:

* **[A]** : A etapa se aplica a todos os nós.
* **[1]** : A etapa se aplica somente ao nó 1.
* **[2]** : A etapa se aplica somente ao nó 2 do cluster pacemaker.

1. **[A]** configurar o firewall

   Crie regras de firewall para permitir a replicação de sistema do HANA e o tráfego do cliente. As portas necessárias são listadas nas [portas TCP/IP de todos os produtos SAP](https://help.sap.com/viewer/ports). Os comandos a seguir são apenas um exemplo para permitir a replicação do sistema HANA 2,0. Adapte-o à instalação do SAP HANA 1,0.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** crie os usuários necessários.

   Execute o comando a seguir como raiz. Certifique-se de substituir cadeias de caracteres em negrito (ID do sistema HANA **HN1** e número de instância **03**) pelos valores de sua instalação do SAP Hana:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** criar a entrada do repositório de chaves.

   Execute o seguinte comando como raiz para criar uma nova entrada de keystore:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** fazer backup do banco de dados.

   Fazer backup dos bancos de dados como raiz:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Se você usar uma instalação multilocatário, também faça backup do banco de dados de locatário:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** configurar a replicação do sistema no primeiro nó.

   Crie o site primário como < ADM\>hanasid:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** configurar a replicação do sistema no nó secundário.

   Registre o site secundário como < ADM\>hanasid:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Criar um cluster pacemaker

Siga as etapas em [Configurando pacemaker em Red Hat Enterprise Linux no Azure](high-availability-guide-rhel-pacemaker.md) para criar um cluster pacemaker básico para esse servidor Hana.

## <a name="create-sap-hana-cluster-resources"></a>Criar SAP HANA recursos de cluster

Instale os agentes de recurso SAP HANA em **todos os nós**. Certifique-se de habilitar um repositório que contém o pacote.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Em seguida, crie a topologia do HANA. Execute os seguintes comandos em um dos nós de cluster pacemaker:

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

Verifique se o status do cluster é OK e se todos os recursos foram iniciados. Não é importante em qual nó os recursos estão em execução.

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

Esta seção descreve como você pode testar sua configuração. Antes de iniciar um teste, verifique se o pacemaker não tem nenhuma ação com falha (por meio do status de PCs), se não há nenhuma restrição de local inesperada (por exemplo, sobras de um teste de migração) e se o HANA é o estado de sincronização, por exemplo, com systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Testar a migração

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

Você pode migrar o nó mestre do SAP HANA executando o seguinte comando:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Se você definir `AUTOMATED_REGISTER="false"`, esse comando deverá migrar o nó mestre de SAP Hana e o grupo que contém o endereço IP virtual para hn1-DB-1.

Quando a migração for concluída, a saída ' status dos PCs sudo ' será parecida com esta

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

O recurso SAP HANA em hn1-dB-0 está parado. Nesse caso, configure a instância do HANA como secundária executando este comando:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

A migração cria restrições de local que precisam ser excluídas novamente:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Monitore o estado do recurso HANA usando ' PCs status '. Depois que o HANA é iniciado em hn1-dB-0, a saída deve ser parecida com esta

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testar o agente de isolamento do Azure

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

Você pode testar a configuração do agente de isolamento do Azure desabilitando a interface de rede no nó em que SAP HANA está sendo executado como mestre.
Consulte o [artigo 79523 da base de conhecimento do Red Hat](https://access.redhat.com/solutions/79523) para obter uma descrição sobre como simular uma falha de rede. Neste exemplo, usamos o script net_breaker para bloquear todo o acesso à rede.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

A máquina virtual agora deve ser reiniciada ou interrompida dependendo da configuração do cluster.
Se você definir a `stonith-action` configuração como off, a máquina virtual será interrompida e os recursos serão migrados para a máquina virtual em execução.

> [!NOTE]
> Pode levar até 15 minutos até que as máquinas virtuais estejam online novamente.

Depois de iniciar a máquina virtual novamente, o recurso SAP HANA falhará ao iniciar como secundário, se `AUTOMATED_REGISTER="false"`você definir. Nesse caso, configure a instância do HANA como secundária executando este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Estado do recurso após o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Testar um failover manual

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

Você pode testar um failover manual interrompendo o cluster no nó hn1-dB-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Após o failover, você pode iniciar o cluster novamente. Se você definir `AUTOMATED_REGISTER="false"`, o recurso SAP Hana no nó hn1-dB-0 falhará ao iniciar como secundário. Nesse caso, configure a instância do HANA como secundária executando este comando:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Estado do recurso após o teste:

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

* [Planejamento e implementação de máquinas virtuais do Azure para SAP][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA no Azure (instâncias grandes), consulte [alta disponibilidade e recuperação de desastre do SAP Hana (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md)
