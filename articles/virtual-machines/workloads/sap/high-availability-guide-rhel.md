---
title: Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux | Microsoft Docs
description: Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: sedusch
ms.openlocfilehash: a618a2cb976c90174125e54af645123c6b0a9dcd
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905024"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um sistema SAP NetWeaver 7,50 altamente disponível.
Nas configurações de exemplo, comandos de instalação etc. O número da instância ASCS 00, o número da instância ERS 02 e a ID do sistema SAP NW1 são usados. Os nomes dos recursos (por exemplo, máquinas virtuais, redes virtuais) no exemplo pressupõem que você usou o [modelo ASCS/SCS][template-multisid-xscs] com o prefixo de recurso NW1 para criar os recursos.

Leia as seguintes notas e documentos SAP primeiro

* Nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure com suporte para a implantação de software SAP
  * Informações de capacidade importantes para tamanhos de VM do Azure
  * Software SAP com suporte e combinações de so (sistema operacional) e banco de dados
  * Versão de kernel do SAP necessária para Windows e Linux em Microsoft Azure

* O SAP Note [2015553] lista os pré-requisitos para implantações de software SAP com suporte no SAP no Azure.
* A observação do SAP [2002167] tem as configurações do sistema operacional recomendadas para Red Hat Enterprise Linux
* A observação do SAP [2009879] tem diretrizes SAP HANA para Red Hat Enterprise Linux
* A nota SAP [2178632] tem informações detalhadas sobre todas as métricas de monitoramento relatadas para SAP no Azure.
* A nota SAP [2191498] tem a versão do agente de host do SAP necessária para Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento SAP no Linux no Azure.
* A nota SAP [1999351] tem informações adicionais para solução de problemas para a extensão de monitoramento avançado do Azure para SAP.
* O [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [Planejamento e implementação de máquinas virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP no Linux][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [Documentação do produto para o armazenamento Red Hat Gluster](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [SAP NetWeaver no cluster pacemaker](https://access.redhat.com/articles/3150081)
* Documentação geral do RHEL
  * [Visão geral do complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configurando o ASCS/ERS para SAP NetWeaver com recursos autônomos no RHEL 7,5](https://access.redhat.com/articles/3569681)
  * [Configurar o SAP S/4HANA ASCS/ERS com o servidor de enfileiramento autônomo 2 (ENSA2) no pacemaker no RHEL](https://access.redhat.com/articles/3974941)
* Documentação do RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade RHEL-Máquinas Virtuais do Microsoft Azure como membros do cluster](https://access.redhat.com/articles/3131341)
  * [Instalando e configurando um cluster de alta disponibilidade Red Hat Enterprise Linux 7,4 (e posterior) no Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Descrição geral

Para obter alta disponibilidade, o SAP NetWeaver requer armazenamento compartilhado. O GlusterFS é configurado em um cluster separado e pode ser usado por vários sistemas SAP.

![Visão geral de alta disponibilidade do SAP NetWeaver](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e o banco de dados SAP HANA usar o nome de host virtual e endereços IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. É recomendável usar o [balanceador de carga padrão](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). A lista a seguir mostra a configuração do balanceador de carga (a) SCS e ERS.

> [!IMPORTANT]
> **Não há suporte para**clustering de vários SIDs do SAP ASCS/ers com Red Hat Linux como sistema operacional convidado em VMs do Azure. O clustering de vários SID descreve a instalação de várias instâncias do SAP ASCS/ERS com SIDs diferentes em um cluster pacemaker.

### <a name="ascs"></a>Um SCS

* Configuração de front-end
  * 10.0.0.7 de endereço IP
* Configuração de back-end
  * Conectado às interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte do cluster (A) SCS/ERS
* Porta de investigação
  * Porta 620<strong>&lt;nr&gt;</strong>
* Regras de balanceamento de carga
  * Se estiver usando Standard Load Balancer, selecione **portas de alta disponibilidade**
  * Se estiver usando Load Balancer básica, crie regras de balanceamento de carga para as seguintes portas
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configuração de front-end
  * 10.0.0.8 de endereço IP
* Configuração de back-end
  * Conectado às interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte do cluster (A) SCS/ERS
* Porta de investigação
  * Porta 621<strong>&lt;nr&gt;</strong>
* Regras de balanceamento de carga
  * Se estiver usando Standard Load Balancer, selecione **portas de alta disponibilidade**
  * Se estiver usando Load Balancer básica, crie regras de balanceamento de carga para as seguintes portas
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-glusterfs"></a>Configurando o GlusterFS

O SAP NetWeaver requer armazenamento compartilhado para o diretório de transporte e de perfil. Leia [GlusterFS em VMs do Azure no Red Hat Enterprise Linux para SAP NetWeaver][glusterfs-ha] sobre como configurar o GLUSTERFS para SAP NetWeaver.

## <a name="setting-up-ascs"></a>Configurando o (A) SCS

Você pode usar um modelo do Azure do GitHub para implantar todos os recursos do Azure necessários, incluindo as máquinas virtuais, o conjunto de disponibilidade e o balanceador de carga, ou você pode implantar os recursos manualmente.

### <a name="deploy-linux-via-azure-template"></a>Implantar o Linux por meio do modelo do Azure

O Azure Marketplace contém uma imagem para Red Hat Enterprise Linux que você pode usar para implantar novas máquinas virtuais. Você pode usar um dos modelos de início rápido no GitHub para implantar todos os recursos necessários. O modelo implanta as máquinas virtuais, o balanceador de carga, o conjunto de disponibilidade, etc. Siga estas etapas para implantar o modelo:

1. Abra o [modelo ASCS/SCS][template-multisid-xscs] no portal do Azure  
1. Insira os seguintes parâmetros
   1. Prefixo de recurso  
      Insira o prefixo que você deseja usar. O valor é usado como um prefixo para os recursos que são implantados.
   1. Tipo de pilha  
      Selecione o tipo de pilha do SAP NetWeaver
   1. Tipo de so  
      Selecione uma das distribuições do Linux. Para este exemplo, selecione RHEL 7
   1. Tipo de BD  
      Selecionar HANA
   1. Contagem de sistema SAP  
      O número de sistemas SAP que são executados neste cluster. Selecione 1.
   1. Disponibilidade do sistema  
      Selecionar HA
   1. Nome de usuário de administrador, senha de administrador ou chave SSH  
      Um novo usuário é criado e pode ser usado para entrar no computador.
   1. ID da sub-rede  
   Se você deseja implantar a VM em uma VNet existente em que você tem uma sub-rede definida, a VM deve ser atribuída, nomear a ID dessa sub-rede específica. A ID geralmente é semelhante a/subscriptions/ **&lt;ID da assinatura&gt;** /resourceGroups/ **&lt;nome do grupo de recursos&gt;** /Providers/Microsoft.Network/virtualNetworks/ **&lt;nome da rede virtual&gt;** /subnets/ **&lt;nome da sub-rede&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Implantar o Linux manualmente por meio de portal do Azure

Primeiro, você precisa criar as máquinas virtuais para esse cluster. Posteriormente, você criará um balanceador de carga e usará as máquinas virtuais nos pools de back-end.

1. Criar um Grupo de Recursos
1. Criar uma rede virtual
1. Criar um conjunto de disponibilidade  
   Definir domínio de atualização máx.
1. Criar máquina virtual 1  
   Use pelo menos RHEL 7, neste exemplo, a imagem Red Hat Enterprise Linux 7,4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Selecionar conjunto de disponibilidade criado anteriormente  
1. Criar máquina virtual 2  
   Use pelo menos RHEL 7, neste exemplo, a imagem Red Hat Enterprise Linux 7,4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Selecionar conjunto de disponibilidade criado anteriormente  
1. Adicionar pelo menos um disco de dados a ambas as máquinas virtuais  
   Os discos de dados são usados para o/usr/SAP/`<SAPSID`> Directory
1. Criar balanceador de carga (interno, padrão):  
   1. Criar os endereços IP de front-end
      1. Endereço IP 10.0.0.7 para o ASCS
         1. Abra o balanceador de carga, selecione pool de IPS de front-end e clique em Adicionar
         1. Insira o nome do novo pool de IPS de front-end (por exemplo **, NW1-ASCs-frontend**)
         1. Defina a atribuição como estática e insira o endereço IP (por exemplo **10.0.0.7**)
         1. Clique em OK
      1. Endereço IP 10.0.0.8 para ASCS ERS
         * Repita as etapas acima para criar um endereço IP para o ERS (por exemplo **10.0.0.8** e **NW1-AERS-backend**)
   1. Criar os pools de back-end
      1. Criar um pool de back-end para o ASCS
         1. Abra o balanceador de carga, selecione pools de back-end e clique em Adicionar
         1. Insira o nome do novo pool de back-end (por exemplo **, NW1-ASCs-backend**)
         1. Clique em adicionar uma máquina virtual.
         1. Selecione máquina virtual.
         1. Selecione as máquinas virtuais do cluster (A) SCS e seus endereços IP.
         1. Clique em Adicionar
      1. Criar um pool de back-end para o ASCS ERS
         * Repita as etapas acima para criar um pool de back-end para o ERS (por exemplo **NW1-AERS-backend**)
   1. Criar as investigações de integridade
      1. Porta 620**00** para ASCS
         1. Abra o balanceador de carga, selecione investigações de integridade e clique em Adicionar
         1. Insira o nome da nova investigação de integridade (por exemplo **NW1-ASCs-HP**)
         1. Selecione TCP como protocolo, porta 620**00**, manter intervalo 5 e limite não íntegro 2
         1. Clique em OK
      1. Porta 621**02** para ASCS ers
         * Repita as etapas acima para criar uma investigação de integridade para o ERS (por exemplo, 621**02** e **NW1-AERS-HP**)
   1. Regras de balanceamento de carga
      1. Regras de balanceamento de carga para ASCS
         1. Abra o balanceador de carga, selecione regras de balanceamento de carga e clique em Adicionar
         1. Insira o nome da nova regra do balanceador de carga (por exemplo **NW1-lb-ASCs**)
         1. Selecione o endereço IP de front-end, o pool de back-ends e a investigação de integridade que você criou anteriormente (por exemplo **NW1-ASCs-frontend**, **NW1-ASCs-backend** e **NW1-ASCs-HP**)
         1. Selecionar **portas de alta disponibilidade**
         1. Aumentar o tempo limite de ociosidade para 30 minutos
         1. **Certifique-se de habilitar o IP flutuante**
         1. Clique em OK
         * Repita as etapas acima para criar regras de balanceamento de carga para ERS (por exemplo **NW1-lb-ers**)
1. Como alternativa, se seu cenário exigir o Load Balancer básico (interno), siga estas etapas:  
   1. Criar os endereços IP de front-end
      1. Endereço IP 10.0.0.7 para o ASCS
         1. Abra o balanceador de carga, selecione pool de IPS de front-end e clique em Adicionar
         1. Insira o nome do novo pool de IPS de front-end (por exemplo **, NW1-ASCs-frontend**)
         1. Defina a atribuição como estática e insira o endereço IP (por exemplo **10.0.0.7**)
         1. Clique em OK
      1. Endereço IP 10.0.0.8 para ASCS ERS
         * Repita as etapas acima para criar um endereço IP para o ERS (por exemplo **10.0.0.8** e **NW1-AERS-backend**)
   1. Criar os pools de back-end
      1. Criar um pool de back-end para o ASCS
         1. Abra o balanceador de carga, selecione pools de back-end e clique em Adicionar
         1. Insira o nome do novo pool de back-end (por exemplo **, NW1-ASCs-backend**)
         1. Clique em adicionar uma máquina virtual.
         1. Selecione o conjunto de disponibilidade que você criou anteriormente
         1. Selecione as máquinas virtuais do cluster (A) SCS
         1. Clique em OK
      1. Criar um pool de back-end para o ASCS ERS
         * Repita as etapas acima para criar um pool de back-end para o ERS (por exemplo **NW1-AERS-backend**)
   1. Criar as investigações de integridade
      1. Porta 620**00** para ASCS
         1. Abra o balanceador de carga, selecione investigações de integridade e clique em Adicionar
         1. Insira o nome da nova investigação de integridade (por exemplo **NW1-ASCs-HP**)
         1. Selecione TCP como protocolo, porta 620**00**, manter intervalo 5 e limite não íntegro 2
         1. Clique em OK
      1. Porta 621**02** para ASCS ers
         * Repita as etapas acima para criar uma investigação de integridade para o ERS (por exemplo, 621**02** e **NW1-AERS-HP**)
   1. Regras de balanceamento de carga
      1. 32**00** TCP para ASCS
         1. Abra o balanceador de carga, selecione regras de balanceamento de carga e clique em Adicionar
         1. Insira o nome da nova regra do balanceador de carga (por exemplo **, NW1-lb-3200**)
         1. Selecione o endereço IP de front-end, o pool de back-ends e a investigação de integridade que você criou anteriormente (por exemplo **NW1-ASCs-frontend**)
         1. Mantenha o protocolo **TCP**, insira a porta **3200**
         1. Aumentar o tempo limite de ociosidade para 30 minutos
         1. **Certifique-se de habilitar o IP flutuante**
         1. Clique em OK
      1. Portas adicionais para o ASCS
         * Repita as etapas acima para as portas**36 00**,**39 00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 e TCP para o ASCS
      1. Portas adicionais para o ASCS ERS
         * Repita as etapas acima para as portas 33**02**, 5**02**13, 5**02**14, 5**02**16 e TCP para ASCS ers

> [!Note]
> Quando as VMs sem endereços IP públicos forem colocadas no pool de back-end do Azure Load Balancer padrão (sem endereço IP público), não haverá nenhuma conectividade com a Internet de saída, a menos que a configuração adicional seja executada para permitir o roteamento para pontos de extremidade públicos. Para obter detalhes sobre como obter conectividade de saída, consulte [conectividade de ponto de extremidade pública para máquinas virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Não habilite carimbos de data/hora TCP em VMs do Azure colocadas por trás Azure Load Balancer. Habilitar carimbos de data/hora TCP fará com que as investigações de integridade falhem. Defina o parâmetro **net. IPv4. tcp_timestamps** como **0**. Para obter detalhes, consulte [Load Balancer investigações de integridade](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Criar cluster pacemaker

Siga as etapas em [Configurando pacemaker em Red Hat Enterprise Linux no Azure](high-availability-guide-rhel-pacemaker.md) para criar um cluster pacemaker básico para esse servidor (a) SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Preparar para a instalação do SAP NetWeaver

Os seguintes itens são prefixados com ambos **[A]** - aplicáveis a todos os nós, **[1]** – apenas aplicável no nó 1 ou **[2]** – apenas aplicável a nó 2.

1. **[A]**  Configurar a resolução de nomes de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e o nome de anfitrião para corresponder ao seu ambiente

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

1. **[A]** criar os diretórios compartilhados

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** instalar o cliente do GlusterFS e outros requisitos

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]** verificar a versão do recurso-agentes-SAP

   Certifique-se de que a versão do pacote Resource-Agents-SAP está no mínimo 3.9.5 -124. EL7
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]** adicionar entradas de montagem

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Montar os novos compartilhamentos

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** configurar arquivo de permuta

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reiniciar o agente para ativar a alteração

   <pre><code>sudo service waagent restart
   </code></pre>

1. Configuração **de [A]** RHEL

   Configure o RHEL conforme descrito em SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalando o SAP NetWeaver ASCS/ERS

1. **[1]** criar um recurso de IP virtual e uma investigação de integridade para a instância de ASCS

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Verifique se o status do cluster é OK e se todos os recursos foram iniciados. Não é importante em qual nó os recursos estão em execução.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** instalar o ASCS do SAP NetWeaver  

   Instale o ASCS do SAP NetWeaver como raiz no primeiro nó usando um nome de host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o ASCS, por exemplo, <b>NW1-ASCS</b>, <b>10.0.0.7</b> e o número da instância que você usou para a investigação de o balanceador de carga, por exemplo <b>00</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Se a instalação não conseguir criar uma subpasta no/usr/SAP/**NW1**/ASCS**00**, tente definir o proprietário e o grupo da pasta ASCS**00** e tente novamente.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** criar um recurso de IP virtual e uma investigação de integridade para a instância de ers

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Verifique se o status do cluster é OK e se todos os recursos foram iniciados. Não é importante em qual nó os recursos estão em execução.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]** instalar o ers do SAP NetWeaver  

   Instale o ERS do SAP NetWeaver como raiz no segundo nó usando um nome de host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o ERS, por exemplo, <b>NW1-AERS</b>, <b>10.0.0.8</b> e o número da instância que você usou para a investigação de o balanceador de carga, por exemplo <b>02</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Se a instalação não conseguir criar uma subpasta no/usr/SAP/**NW1**/ers**02**, tente definir o proprietário e o grupo da pasta ers**02** e tente novamente.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]** adaptar os perfis de instância ASCS/SCS e ers

   * Perfil do ASCS/SCS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Perfil de ERS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]** configurar Keep Alive

   A comunicação entre o servidor de aplicativos SAP NetWeaver e o ASCS/SCS é roteada por meio de um balanceador de carga de software. O balanceador de carga desconecta conexões inativas após um tempo limite configurável. Para evitar isso, você precisa definir um parâmetro no perfil do SAP NetWeaver ASCS/SCS e alterar as configurações do sistema Linux. Leia a [Observação do SAP 1410736][1410736] para obter mais informações.

   O parâmetro de perfil ASCS/SCS enfileirar/encni/set_so_keepalive já foi adicionado na última etapa.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** atualizar o arquivo/usr/SAP/sapservices

   Para evitar o início das instâncias pelo script de inicialização sapinit, todas as instâncias gerenciadas pelo pacemaker devem ser comentadas no arquivo/usr/SAP/sapservices. Não comente a SAP HANA instância se ela será usada com o HANA SR.

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]** criar os recursos de cluster do SAP

  Se estiver usando a arquitetura do enqueue Server 1 (ENSA1), defina os recursos da seguinte maneira:

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   O SAP introduziu o suporte para o enqueue Server 2, incluindo a replicação, a partir do SAP NW 7,52. A partir do ABAP Platform 1809, o enqueue Server 2 é instalado por padrão. Consulte o SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) para suporte ao servidor de enfileiramento 2.
   Se estiver usando[ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)(Enqueue Server 2 Architecture), instale o agente de recurso Resource-Agents-SAP-4.1.1 12. el7. x86_64 ou mais recente e defina os recursos da seguinte maneira:

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Se você estiver atualizando de uma versão mais antiga e alternando para o servidor de enfileiramento 2, consulte a observação do SAP [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Verifique se o status do cluster é OK e se todos os recursos foram iniciados. Não é importante em qual nó os recursos estão em execução.

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]** adicionar regras de firewall para ASCS e ers em ambos os nós

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparação do servidor de aplicativos do SAP NetWeaver

Alguns bancos de dados exigem que a instalação da instância do banco de dados seja executada em um servidor de aplicativos. Prepare as máquinas virtuais do servidor de aplicativos para poder usá-las nesses casos.

As etapas abaixo pressupõem que você instale o servidor de aplicativos em um servidor diferente dos servidores ASCS/SCS e HANA. Caso contrário, algumas das etapas abaixo (como configurar a resolução de nome de host) não são necessárias.

1. Configurar resolução de nome de host

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e o nome de anfitrião para corresponder ao seu ambiente

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

1. Criar o diretório sapmnt

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Instalar o cliente do GlusterFS e outros requisitos

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Adicionar entradas de montagem

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Montar os novos compartilhamentos

   <pre><code>sudo mount -a
   </code></pre>

1. Configurar arquivo de permuta
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reiniciar o agente para ativar a alteração

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalar banco de dados

Neste exemplo, o SAP NetWeaver está instalado em SAP HANA. Você pode usar cada banco de dados com suporte para esta instalação. Para obter mais informações sobre como instalar SAP HANA no Azure, consulte [alta disponibilidade de SAP Hana em VMs do Azure no Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Executar a instalação da instância do banco de dados SAP

   Instale a instância de banco de dados do SAP NetWeaver como raiz usando um nome de host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o banco de dados, por exemplo <b>, NW1-DB</b> e <b>10.0.0.13</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalação do servidor de aplicativos SAP NetWeaver

Siga estas etapas para instalar um servidor de aplicativos SAP.

1. Preparar servidor de aplicativos

   Siga as etapas no capítulo [preparação do servidor de aplicativos SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) acima para preparar o servidor de aplicativos.

1. Instalar o servidor de aplicativos SAP NetWeaver

   Instale um servidor de aplicativos SAP NetWeaver primário ou adicional.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Atualizar SAP HANA repositório seguro

   Atualize o SAP HANA repositório seguro para apontar para o nome virtual da configuração de replicação do sistema do SAP HANA.

   Execute o seguinte comando para listar as entradas como \<sapsid > ADM

   <pre><code>hdbuserstore List
   </code></pre>

   Isso deve listar todas as entradas e deve ser semelhante a
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   A saída mostra que o endereço IP da entrada padrão está apontando para a máquina virtual e não para o endereço IP do balanceador de carga. Essa entrada precisa ser alterada para apontar para o nome de host virtual do balanceador de carga. Certifique-se de usar a mesma porta (**30313** na saída acima) e o nome do banco de dados (**HN1** na saída acima)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

1. Migrar manualmente a instância de ASCS

   Estado do recurso antes de iniciar o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Execute os comandos a seguir como raiz para migrar a instância de ASCS.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Simular falha de nó

   Estado do recurso antes de iniciar o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Execute o seguinte comando como raiz no nó em que a instância ASCS está em execução

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   O status após o nó ser iniciado novamente deve ter a seguinte aparência.

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   Use o comando a seguir para limpar os recursos com falha.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Eliminar processo do servidor de mensagens

   Estado do recurso antes de iniciar o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Execute os seguintes comandos como raiz para identificar o processo do servidor de mensagens e desmatar.

   <pre><code>[root@nw1-cl-0 ~]# pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Se você eliminar apenas o servidor de mensagens uma vez, ele será reiniciado pelo sapstart. Se você o eliminar com frequência suficiente, o pacemaker eventualmente moverá a instância ASCS para o outro nó. Execute os comandos a seguir como raiz para limpar o estado do recurso da instância ASCS e ERS após o teste.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Processo do servidor de eliminação de enfileiramento

   Estado do recurso antes de iniciar o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Execute os comandos a seguir como raiz no nó em que a instância ASCS está sendo executada para eliminar o servidor de enfileiramento.

   <pre><code>[root@nw1-cl-1 ~]# pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   A instância ASCS deve fazer failover imediatamente para o outro nó. A instância ERS também deve fazer failover depois que a instância ASCS é iniciada. Execute os comandos a seguir como raiz para limpar o estado do recurso da instância ASCS e ERS após o teste.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Processo do servidor de replicação do Kill enqueue

   Estado do recurso antes de iniciar o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Execute o seguinte comando como raiz no nó em que a instância ERS está em execução para eliminar o processo do servidor de replicação de enfileiramento.

   <pre><code>[root@nw1-cl-1 ~]# pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Se você executar o comando apenas uma vez, sapstart irá reiniciar o processo. Se você executá-lo com frequência suficiente, o sapstart não reiniciará o processo e o recurso estará em um estado parado. Execute os comandos a seguir como raiz para limpar o estado do recurso da instância ERS após o teste.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Eliminar enqueue sapstartsrv processo

   Estado do recurso antes de iniciar o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Execute os comandos a seguir como raiz no nó em que o ASCS está em execução.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   O processo sapstartsrv sempre deve ser reiniciado pelo agente de recursos do pacemaker como parte do monitoramento. Estado do recurso após o teste:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>Passos seguintes

* [Planejamento e implementação de máquinas virtuais do Azure para SAP][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA no Azure (instâncias grandes), consulte [alta disponibilidade e recuperação de desastre do SAP Hana (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA em VMs do Azure, consulte [alta disponibilidade de SAP Hana em VMS (máquinas virtuais) do Azure][sap-hana-ha]
