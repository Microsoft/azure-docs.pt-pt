---
title: Alta disponibilidade de SAP HANA em VMs do Azure no SUSE Linux Enterprise Server | Microsoft Docs
description: Alta disponibilidade de SAP HANA em VMs do Azure no SUSE Linux Enterprise Server
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
ms.openlocfilehash: 7b9d3791d44e9541df7fc95c34b5e8c83a4295b3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078396"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Alta disponibilidade de SAP HANA em VMs do Azure no SUSE Linux Enterprise Server

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
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]: https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

Para o desenvolvimento local, você pode usar a replicação de sistema do HANA ou usar o armazenamento compartilhado para estabelecer alta disponibilidade para SAP HANA.
Em VMs (máquinas virtuais) do Azure, a replicação de sistema do HANA no Azure é a única função de alta disponibilidade com suporte no momento. SAP HANA replicação consiste em um nó primário e pelo menos um nó secundário. As alterações nos dados no nó primário são replicadas para o nó secundário de forma síncrona ou assíncrona.

Este artigo descreve como implantar e configurar as máquinas virtuais, instalar a estrutura do cluster e instalar e configurar SAP HANA replicação do sistema.
No exemplo configurações, comandos de instalação, número de instância **03**e **HN1** de ID do sistema Hana são usados.

Leia as seguintes notas e documentos SAP primeiro:

* Nota SAP [1928533], que tem:
  * A lista de tamanhos de VM do Azure com suporte para a implantação do software SAP.
  * Informações de capacidade importantes para tamanhos de VM do Azure.
  * O software SAP com suporte e as combinações de sistema operacional e banco de dados.
  * A versão de kernel do SAP necessária para Windows e Linux em Microsoft Azure.
* Observação do SAP [2015553] lista os pré-requisitos para implantações de software SAP com suporte no SAP no Azure.
* A observação do SAP [2205917] tem as configurações do sistema operacional recomendadas para SuSE Linux Enterprise Server para aplicativos SAP.
* O SAP Note [1944799] tem diretrizes SAP HANA para SuSE Linux Enterprise Server para aplicativos SAP.
* O SAP Note [2178632] tem informações detalhadas sobre todas as métricas de monitoramento que são relatadas para SAP no Azure.
* A nota SAP [2191498] tem a versão do agente de host do SAP necessária para Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento SAP no Linux no Azure.
* A nota SAP [1984787] tem informações gerais sobre o SuSE Linux Enterprise Server 12.
* A nota SAP [1999351] tem informações adicionais para solução de problemas para a extensão de monitoramento avançado do Azure para SAP.
* A observação do SAP [401162] tem informações sobre como evitar "endereço já em uso" ao configurar a replicação do sistema Hana.
* O [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [SAP HANA plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Planejamento e implementação de máquinas virtuais do Azure para SAP no][planning-guide] guia do Linux.
* [Implantação de máquinas virtuais do Azure para SAP no Linux][deployment-guide] (este artigo).
* [Implantação de DBMS de máquinas virtuais do Azure para SAP no][dbms-guide] guia do Linux.
* [Guias de práticas recomendadas do SUSE Linux Enterprise Server para aplicativos SAP 12 SP3][sles-for-sap-bp]
  * Configuração de uma infraestrutura otimizada de desempenho do SR SAP HANA (SLES for SAP Applications 12 SP1). O guia contém todas as informações necessárias para configurar a replicação do sistema SAP HANA para o desenvolvimento local. Use este guia como uma linha de base.
  * Configurando uma infraestrutura de SAP HANA SR-Optimized (SLES for SAP Applications 12 SP1)

## <a name="overview"></a>Descrição geral

Para obter alta disponibilidade, o SAP HANA é instalado em duas máquinas virtuais. Os dados são replicados usando a replicação de sistema do HANA.

![Visão geral de SAP HANA alta disponibilidade](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA configuração de replicação do sistema usa um nome de host virtual dedicado e endereços IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. A lista a seguir mostra a configuração do balanceador de carga:

* Configuração de front-end: Endereço IP 10.0.0.13 para hn1-DB
* Configuração de back-end: Conectado a interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte da replicação do sistema HANA
* Porta de investigação: Porta 62503
* Regras de balanceamento de carga: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Implantar para Linux

O agente de recurso para SAP HANA está incluído no SUSE Linux Enterprise Server para aplicativos SAP.
O Azure Marketplace contém uma imagem para SUSE Linux Enterprise Server para aplicativos SAP 12 que você pode usar para implantar novas máquinas virtuais.

### <a name="deploy-with-a-template"></a>Implantar com um modelo

Você pode usar um dos modelos de início rápido que estão no GitHub para implantar todos os recursos necessários. O modelo implanta as máquinas virtuais, o balanceador de carga, o conjunto de disponibilidade e assim por diante.
Para implantar o modelo, siga estas etapas:

1. Abra o [modelo de banco de dados][template-multisid-db] ou o [modelo convergido][template-converged] no portal do Azure. 
    O modelo de banco de dados cria as regras de balanceamento de carga somente para um banco de dados. O modelo convergido também cria as regras de balanceamento de carga para uma instância do ASCS/SCS e ERS (somente Linux). Se você planeja instalar um sistema baseado no SAP NetWeaver e deseja instalar a instância do ASCS/SCS nos mesmos computadores, use o [modelo convergido][template-converged].

1. Insira os seguintes parâmetros:
    - **ID do sistema SAP**: Insira a ID do sistema SAP do sistema SAP que você deseja instalar. A ID é usada como um prefixo para os recursos que são implantados.
    - **Tipo de pilha**: (Esse parâmetro será aplicável somente se você usar o modelo convergido.) Selecione o tipo de pilha do SAP NetWeaver.
    - **Tipo de so**: Selecione uma das distribuições do Linux. Para este exemplo, selecione **SLES 12**.
    - **Tipo de BD**: Selecione **Hana**.
    - **Tamanho do sistema SAP**: Insira o número de SAPS que o novo sistema vai fornecer. Se você não tiver certeza de quantos SAPS o sistema precisará, pergunte ao seu parceiro de tecnologia SAP ou ao integrador de sistemas.
    - **Disponibilidade do sistema**: Selecione **ha**.
    - **Nome de usuário do administrador e senha do administrador**: Um novo usuário é criado e pode ser usado para entrar no computador.
    - **Sub-rede nova ou existente**: Determina se uma nova rede virtual e sub-rede devem ser criadas ou uma sub-rede existente usada. Se você já tiver uma rede virtual conectada à sua rede local, selecione **existente**.
    - **ID da sub-rede**: Se você deseja implantar a VM em uma VNet existente em que você tem uma sub-rede definida, a VM deve ser atribuída, nomear a ID dessa sub-rede específica. A ID geralmente se parece com a **ID de\<assinatura/subscriptions/> nome do grupo\<de recursos/resourceGroups/\<>/Providers/Microsoft.Network/virtualNetworks/nome da rede virtual >/Subnets/\<nome da sub-rede >** .

### <a name="manual-deployment"></a>Implantação manual

> [!IMPORTANT]
> Certifique-se de que o sistema operacional selecionado é certificado pelo SAP para SAP HANA nos tipos específicos de VM que você está usando. A lista de tipos de VM certificados SAP HANA e as versões do sistema operacional para aqueles podem ser pesquisadas em [plataformas IaaS SAP Hana certificados](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Certifique-se de clicar nos detalhes do tipo de VM listado para obter a lista completa de SAP HANA versões de sistema operacional com suporte para o tipo de VM específico
>  

1. Crie um grupo de recursos.
1. Crie uma rede virtual
1. Crie um conjunto de disponibilidade.
   - Defina o domínio de atualização máx.
1. Crie um balanceador de carga (interno).
   - Selecione a rede virtual criada na etapa 2.
1. Crie a máquina virtual 1.
   - Use uma imagem SLES4SAP na galeria do Azure com suporte para SAP HANA no tipo de VM selecionado.
   - Selecione o conjunto de disponibilidade criado na etapa 3.
1. Crie a máquina virtual 2.
   - Use uma imagem SLES4SAP na galeria do Azure com suporte para SAP HANA no tipo de VM selecionado.
   - Selecione o conjunto de disponibilidade criado na etapa 3. 
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

## <a name="create-a-pacemaker-cluster"></a>Criar um cluster pacemaker

Siga as etapas em [Configurando pacemaker em SuSE Linux Enterprise Server no Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster pacemaker básico para esse servidor Hana. Você pode usar o mesmo cluster pacemaker para o SAP HANA e o SAP NetWeaver (A) SCS.

## <a name="install-sap-hana"></a>Instalar o SAP HANA

As etapas nesta seção usam os seguintes prefixos:
- **[A]** : A etapa se aplica a todos os nós.
- **[1]** : A etapa se aplica somente ao nó 1.
- **[2]** : A etapa se aplica somente ao nó 2 do cluster pacemaker.

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

1. **[A]** instale o SAP Hana pacotes de alta disponibilidade:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Para instalar SAP HANA replicação do sistema, siga o capítulo 4 do [Guia de cenário otimizado para desempenho do SAP Hana Sr](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]** executar o programa **hdblcm** do DVD do Hana. Insira os seguintes valores no prompt:
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

## <a name="configure-sap-hana-20-system-replication"></a>Configurar a replicação do sistema SAP HANA 2,0

As etapas nesta seção usam os seguintes prefixos:

* **[A]** : A etapa se aplica a todos os nós.
* **[1]** : A etapa se aplica somente ao nó 1.
* **[2]** : A etapa se aplica somente ao nó 2 do cluster pacemaker.

1. **[1]** criar o banco de dados de locatário.

   Se você estiver usando SAP HANA 2,0 ou MDC, crie um banco de dados de locatário para seu sistema SAP NetWeaver. Substitua **NW1** pelo SID do seu sistema SAP.

   Execute o seguinte comando como < hanasid\>ADM:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** configurar a replicação do sistema no primeiro nó:

   Faça backup dos bancos de dados como < ADM\>hanasid:

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

## <a name="configure-sap-hana-10-system-replication"></a>Configurar a replicação do sistema SAP HANA 1,0

As etapas nesta seção usam os seguintes prefixos:

* **[A]** : A etapa se aplica a todos os nós.
* **[1]** : A etapa se aplica somente ao nó 1.
* **[2]** : A etapa se aplica somente ao nó 2 do cluster pacemaker.

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

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Criar SAP HANA recursos de cluster

Primeiro, crie a topologia do HANA. Execute os seguintes comandos em um dos nós de cluster pacemaker:

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

Verifique se o status do cluster é OK e se todos os recursos foram iniciados. Não é importante em qual nó os recursos estão em execução.

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

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta seção descreve como você pode testar sua configuração. Cada teste pressupõe que você é raiz e o SAP HANA mestre está em execução na máquina virtual **hn1-dB-0** .

### <a name="test-the-migration"></a>Testar a migração

Antes de iniciar o teste, certifique-se de que pacemaker não tenha nenhuma ação com falha (via crm_mon-r), não há restrições de local inesperadas (por exemplo, sobras de um teste de migração) e que o HANA é o estado de sincronização, por exemplo, com SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Você pode migrar o nó mestre do SAP HANA executando o seguinte comando:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Se você definir `AUTOMATED_REGISTER="false"`, essa sequência de comandos deverá migrar o nó mestre de SAP Hana e o grupo que contém o endereço IP virtual para hn1-DB-1.

Quando a migração for concluída, a saída do crm_mon-r terá esta aparência

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

O recurso SAP HANA em hn1-dB-0 falha ao iniciar como secundário. Nesse caso, configure a instância do HANA como secundária executando este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

A migração cria restrições de local que precisam ser excluídas novamente:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Você também precisa limpar o estado do recurso de nó secundário:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Monitore o estado do recurso do HANA usando crm_mon-r. Depois que o HANA é iniciado em hn1-dB-0, a saída deve ser parecida com esta

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

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testar o agente de isolamento do Azure (não SBD)

Você pode testar a configuração do agente de isolamento do Azure desabilitando a interface de rede no nó hn1-dB-0:

<pre><code>sudo ifdown eth0
</code></pre>

A máquina virtual agora deve ser reiniciada ou interrompida dependendo da configuração do cluster.
Se você definir a `stonith-action` configuração como off, a máquina virtual será interrompida e os recursos serão migrados para a máquina virtual em execução.

Depois de iniciar a máquina virtual novamente, o recurso SAP HANA falhará ao iniciar como secundário, se `AUTOMATED_REGISTER="false"`você definir. Nesse caso, configure a instância do HANA como secundária executando este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Testar isolamento SBD

Você pode testar a configuração do SBD finalizando o processo Inquisitor.

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

O nó de cluster hn1-dB-0 deve ser reinicializado. O serviço pacemaker pode não ser iniciado posteriormente. Certifique-se de iniciá-lo novamente.

### <a name="test-a-manual-failover"></a>Testar um failover manual

Você pode testar um failover manual interrompendo `pacemaker` o serviço no nó hn1-dB-0:

<pre><code>service pacemaker stop
</code></pre>

Após o failover, você pode iniciar o serviço novamente. Se você definir `AUTOMATED_REGISTER="false"`, o recurso SAP Hana no nó hn1-dB-0 falhará ao iniciar como secundário. Nesse caso, configure a instância do HANA como secundária executando este comando:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>Testes do SUSE

> [!IMPORTANT]
> Certifique-se de que o sistema operacional selecionado é certificado pelo SAP para SAP HANA nos tipos específicos de VM que você está usando. A lista de tipos de VM certificados SAP HANA e as versões do sistema operacional para aqueles podem ser pesquisadas em [plataformas IaaS SAP Hana certificados](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Certifique-se de clicar nos detalhes do tipo de VM listado para obter a lista completa de SAP HANA versões de sistema operacional com suporte para o tipo de VM específico

Execute todos os casos de teste listados no cenário otimizado para desempenho SAP HANA SR ou SAP HANA guia de cenário com otimização de custo do SR, dependendo do caso de uso. Você pode encontrar os guias na [página de práticas recomendadas do SLES for SAP][sles-for-sap-bp].

Os testes a seguir são uma cópia das descrições do teste do SAP HANA o cenário otimizado para desempenho do SR SUSE Linux Enterprise Server para o guia do SAP Applications 12 SP1. Para uma versão atualizada, sempre Leia o guia em si. Sempre verifique se o HANA está em sincronia antes de iniciar o teste e verifique se a configuração do pacemaker está correta.

Nas descrições de teste a seguir, presumimos PREFER_SITE_TAKEOVER = "true" e AUTOMATED_REGISTER = "false".
NOTA: Os testes a seguir são projetados para serem executados em sequência e dependem do estado de saída dos testes anteriores.

1. TESTE 1: PARAR O BANCO DE DADOS PRIMÁRIO NO NÓ 1

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

   Execute os comandos a seguir como <\>hanasid ADM no nó hn1-dB-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker deve detectar a instância do HANA interrompida e o failover para o outro nó. Depois que o failover for concluído, a instância do HANA no nó hn1-dB-0 será interrompida porque o pacemaker não registra automaticamente o nó como um secundário do HANA.

   Execute os comandos a seguir para registrar o nó hn1-dB-0 como secundário e limpar o recurso com falha.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TESTE 2: PARAR O BANCO DE DADOS PRIMÁRIO NO NÓ 2

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

   Execute os comandos a seguir como <\>hanasid ADM no nó hn1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker deve detectar a instância do HANA interrompida e o failover para o outro nó. Depois que o failover for concluído, a instância do HANA no nó hn1-DB-1 será interrompida porque o pacemaker não registra automaticamente o nó como um secundário do HANA.

   Execute os comandos a seguir para registrar o nó hn1-DB-1 como secundário e limpar o recurso com falha.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TESTE 3: BANCO DE DADOS PRIMÁRIO DE PANE NO NÓ

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

   Execute os comandos a seguir como <\>hanasid ADM no nó hn1-dB-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker deve detectar a instância do HANA eliminada e o failover para o outro nó. Depois que o failover for concluído, a instância do HANA no nó hn1-dB-0 será interrompida porque o pacemaker não registra automaticamente o nó como um secundário do HANA.

   Execute os comandos a seguir para registrar o nó hn1-dB-0 como secundário e limpar o recurso com falha.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TESTE 4: BANCO DE DADOS PRIMÁRIO DE PANE NO NÓ 2

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

   Execute os comandos a seguir como <\>hanasid ADM no nó hn1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker deve detectar a instância do HANA eliminada e o failover para o outro nó. Depois que o failover for concluído, a instância do HANA no nó hn1-DB-1 será interrompida porque o pacemaker não registra automaticamente o nó como um secundário do HANA.

   Execute os comandos a seguir para registrar o nó hn1-DB-1 como secundário e limpar o recurso com falha.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TESTE 5: NÓ DO SITE PRIMÁRIO DE FALHA (NÓ 1)

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

   Execute os seguintes comandos como root no nó hn1-dB-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker deve detectar o nó de cluster eliminado e cercar o nó. Depois que o nó estiver isolado, pacemaker disparará um tomada da instância do HANA. Quando o nó isolado for reinicializado, o pacemaker não será iniciado automaticamente.

   Execute os seguintes comandos para iniciar o pacemaker, limpe as mensagens SBD para o nó hn1-dB-0, registre o nó hn1-dB-0 como secundário e limpe o recurso com falha.

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

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TESTE 6: NÓ DE SITE SECUNDÁRIO DE FALHA (NÓ 2)

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

   Execute os seguintes comandos como root no nó hn1-DB-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker deve detectar o nó de cluster eliminado e cercar o nó. Depois que o nó estiver isolado, pacemaker disparará um tomada da instância do HANA. Quando o nó isolado for reinicializado, o pacemaker não será iniciado automaticamente.

   Execute os seguintes comandos para iniciar o pacemaker, limpe as mensagens SBD para o nó hn1-DB-1, registre o nó hn1-DB-1 como secundário e limpe o recurso com falha.

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

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TESTE 7: PARAR O BANCO DE DADOS SECUNDÁRIO NO NÓ 2

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

   Execute os comandos a seguir como <\>hanasid ADM no nó hn1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   O pacemaker detectará a instância do HANA interrompida e marcará o recurso como com falha no nó hn1-DB-1. Pacemaker deve reiniciar automaticamente a instância do HANA. Execute o comando a seguir para limpar o estado de falha.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TESTE 8: FALHAR O BANCO DE DADOS SECUNDÁRIO NO NÓ 2

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

   Execute os comandos a seguir como <\>hanasid ADM no nó hn1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker detectará a instância do HANA eliminada e marcará o recurso como com falha no nó hn1-DB-1. Execute o comando a seguir para limpar o estado de falha. Pacemaker deve reiniciar a instância do HANA automaticamente.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TESTE 9: NÓ DO SITE SECUNDÁRIO DE FALHA (NÓ 2) EXECUTANDO BANCO DE DADOS HANA SECUNDÁRIO

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

   Execute os seguintes comandos como root no nó hn1-DB-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker deve detectar o nó de cluster eliminado e cercar o nó. Quando o nó isolado for reinicializado, o pacemaker não será iniciado automaticamente.

   Execute os seguintes comandos para iniciar o pacemaker, limpe as mensagens SBD para o nó hn1-DB-1 e limpe o recurso com falha.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Passos seguintes

* [Planejamento e implementação de máquinas virtuais do Azure para SAP][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA no Azure (instâncias grandes), consulte [alta disponibilidade e recuperação de desastre do SAP Hana (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md)
