---
title: Azure máquinas virtuais elevada disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server com o NetApp serviço ficheiros do Azure | Documentos da Microsoft
description: Guia de elevada disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server com o NetApp serviço ficheiros do Azure para aplicações SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/015/2019
ms.author: radeltch
ms.openlocfilehash: 862e99b1ab44c097429dcf015b6f8c8ae023e6bc
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225506"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Elevada disponibilidade para SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server com o NetApp serviço ficheiros do Azure para aplicações SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/en-us/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Este artigo descreve como implementar as máquinas virtuais, configurar as máquinas virtuais, instalar o framework de cluster e instalar um sistema SAP NetWeaver 7.50 elevada disponibilidade, utilizando [ficheiros do Azure NetApp](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-introduction/).
As configurações de exemplo, os comandos de instalação etc., a instância do ASCS é número 00, o número de instância ERS 01, a instância da aplicação principal (PAS) é 02 e a instância da aplicação (AAS) é 03. É utilizado o QAS de ID de sistema SAP. 

Este artigo explica como alcançar a elevada disponibilidade para a aplicação SAP NetWeaver com ficheiros de NetApp do Azure. A camada de base de dados não é abordada em detalhes neste artigo.

Leia primeiro o SAP Notes e os documentos seguintes:

* [Documentação do Azure de ficheiros NetApp][anf-azure-doc] 
* A nota SAP [1928533], que tem:  
  * Lista de tamanhos de VM do Azure que são suportados para a implementação de SAP software
  * Informações de capacidade importante para tamanhos de VM do Azure
  * Software SAP suportado e o sistema operativo (SO) e combinações de base de dados
  * Versão de kernel do SAP necessária para Windows e Linux no Microsoft Azure
* A nota SAP [2015553] apresenta uma lista de pré-requisitos para implementações de software SAP suportadas para SAP no Azure.
* A nota SAP [2205917] recomendado configurações de SO para o SUSE Linux Enterprise Server para aplicativos de SAP
* A nota SAP [1944799] tem diretrizes do SAP HANA para SUSE Linux Enterprise Server para aplicações SAP
* A nota SAP [2178632] tem informações detalhadas sobre todas as monitorizações métricas comunicadas para o SAP no Azure.
* A nota SAP [2191498] tem a versão necessária do agente de anfitrião do SAP para o Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento de SAP no Linux no Azure.
* A nota SAP [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A nota SAP [1999351] tem informações adicionais de resolução de problemas avançada de monitorização a extensão do Azure para SAP.
* [WIKI de Comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as necessárias SAP Notes para o Linux.
* [Máquinas de virtuais de planeamento e implementação para o SAP no Linux do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP no Linux][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [SUSE SAP HA guias das melhores práticas] [ suse-ha-guide] os guias contêm todas as informações necessárias para configurar a HA do Netweaver e SAP HANA System Replication no local. Utilize estes guias como uma linha de base geral. Eles fornecem informações muito mais detalhadas.
* [Notas de versão do SP3 de extensão 12 do SUSE elevada disponibilidade][suse-ha-12sp3-relnotes]
* [NetApp aplicações de SAP no Microsoft Azure utilizando o serviço ficheiros do Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Descrição geral

Availability(HA) elevada para os serviços de centrais de SAP Netweaver requer armazenamento partilhado.
Até agora para esse efeito no SUSE Linux era necessário criar o cluster NFS de elevada disponibilidade separado. 

Agora, é possível obter SAP Netweaver HA ao utilizar o armazenamento partilhado, implementado no Azure NetApp ficheiros. Utilização de ficheiros de NetApp do Azure para o armazenamento partilhado elimina a necessidade de adicionais [NFS cluster](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Pacemaker ainda é necessária para HA do services(ASCS/SCS) central do SAP Netweaver.


![Descrição geral do SAP NetWeaver elevada disponibilidade](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e a base de dados do SAP HANA, utilizam o nome de anfitrião virtual e endereços IP virtuais. No Azure, um [Balanceador de carga](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview) é necessária para utilizar um endereço IP virtual. A lista seguinte mostra a configuração do (A) SCS e ERS Balanceador de carga.

### <a name="ascs"></a>(A)SCS

* Configuração de front-end
  * Endereço IP 10.1.1.20
* Configuração de back-end
  * Ligado a interfaces de rede primário de todas as máquinas virtuais que devem fazer parte do (A) cluster SCS/ERS
* Porta de sonda
  * Port 620<strong>&lt;nr&gt;</strong>
* Regras de balanceamento de carga
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configuração de front-end
  * Endereço IP 10.1.1.21
* Configuração de back-end
  * Ligado a interfaces de rede primário de todas as máquinas virtuais que devem fazer parte do (A) cluster SCS/ERS
* Porta de sonda
  * Port 621<strong>&lt;nr&gt;</strong>
* Regras de balanceamento de carga
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Configurando a infraestrutura de ficheiros do Azure NetApp 

SAP NetWeaver requer armazenamento partilhado para o diretório de transporte e perfil.  Antes de continuar com a configuração para a infraestrutura de ficheiros do Azure NetApp, familiarize-se com o [documentação de ficheiros do Azure NetApp][anf-azure-doc]. Verifique se a região do Azure selecionada oferece NetApp serviço ficheiros do Azure. A seguinte hiperligação mostra a disponibilidade de arquivos de NetApp do Azure por região do Azure: [Disponibilidade de ficheiros NetApp do Azure por região do Azure][anf-avail-matrix].

A funcionalidade de ficheiros do Azure NetApp está em pré-visualização pública em várias regiões do Azure. Antes de implementar o serviço ficheiros do Azure NetApp, inscreva-se na pré-visualização de ficheiros do Azure NetApp, seguindo a [registar para obter instruções de ficheiros do Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Implementar recursos de ficheiros do Azure NetApp  

Os passos partem do princípio de que já tiver implementado [rede Virtual do Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview). Tenha em atenção que os recursos de ficheiros de NetApp do Azure e as VMs, onde os recursos de ficheiros de NetApp do Azure serão montados tem de ser implementadas na mesma rede Virtual do Azure.  

1. Se ainda não tiver feito isso já, pedir [inscrever na pré-visualização do Azure NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register).  

2. Criar a conta de NetApp na região do Azure selecionada, a seguir a [instruções para criar a conta de NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Configurar o conjunto de capacidade de ficheiros do Azure NetApp, seguindo a [obter instruções sobre como configurar o conjunto de capacidade de ficheiros do Azure NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
A arquitetura do SAP Netweaver apresentada neste artigo usa o agrupamento de capacidade de ficheiros do Azure NetApp único, Premium SKU. Recomendamos que SKU Premium do Azure NetApp ficheiros para a carga de trabalho de aplicações de SAP Netweaver no Azure.  

4. Delegar uma sub-rede para ficheiros do Azure NetApp, conforme descrito no [instruções delegar uma sub-rede para ficheiros do Azure NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Implementar os volumes de ficheiros do Azure NetApp, seguindo a [instruções para criar um volume para os ficheiros do Azure NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-volumes). Implementar os volumes nos ficheiros de NetApp do Azure designada [sub-rede](https://docs.microsoft.com/en-us/rest/api/virtualnetwork/subnets). Tenha em atenção que os recursos de ficheiros de NetApp do Azure e as VMs do Azure têm de estar na mesma rede Virtual do Azure. Por exemplo /sapmnt<b>QAS</b>, usrsap<b>QAS</b>, etc. são os nomes de volume e /sapmnt<b>qas</b>, usrsap<b>qas</b>, etc. são o filepaths para o Azure Volumes de ficheiros NetApp.  

   1. volume /sapmnt<b>QAS</b> (nfs://10.1.0.4/sapmnt<b>qas</b>)
   2. volume usrsap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>)
   3. volume usrsap<b>QAS</b>sys (nfs://10.1.0.5/usrsap<b>qas</b>sys)
   4. volume usrsap<b>QAS</b>ers (nfs://10.1.0.4/usrsap<b>qas</b>ers)
   5. trans de volume (nfs://10.1.0.4/trans)
   6. volume usrsap<b>QAS</b>pas (nfs://10.1.0.5/usrsap<b>qas</b>pas)
   7. volume usrsap<b>QAS</b>aas (nfs://10.1.0.4/usrsap<b>qas</b>aas)
   
Neste exemplo, utilizámos NetApp serviço ficheiros do Azure para todos os sistemas de ficheiros do SAP Netweaver para demonstrar como os ficheiros de NetApp do Azure pode ser utilizados. Os sistemas de arquivos SAP que não tem de ser montado por meio de NFS também podem ser implantados como [armazenamento de disco do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#premium-ssd) . Neste exemplo <b>-e</b> tem de ser ficheiros do Azure NetApp e <b>f-g</b> (ou seja, /usr/sap/<b>QAS</b>/D<b>02</b>,/usr/sap/<b>QAS </b>/D<b>03</b>) poderia ser implementado como armazenamento de disco do Azure. 

### <a name="important-considerations"></a>Considerações importantes

Ao considerar os ficheiros de NetApp do Azure para o SAP Netweaver em arquitetura de elevada disponibilidade de SUSE, lembre-se de que as seguintes considerações importantes:

- O agrupamento de capacidade mínima é 4 TiB. O tamanho do conjunto de capacidade tem de ser em múltiplos de 4 TiB.
- O volume mínimo é 100 GiB
- Os ficheiros NetApp do Azure e todas as máquinas virtuais, em que os volumes de ficheiros de NetApp do Azure serão montados têm de estar na mesma rede Virtual do Azure. [Peering de rede virtual](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview) ainda não é suportado pelos ficheiros do Azure NetApp.
- A rede virtual selecionada tem de ter uma sub-rede, delegada aos ficheiros do Azure NetApp.
- Os ficheiros do Azure NetApp atualmente suporta apenas NFSv3 
- Os ficheiros NetApp do Azure oferece [Exportar política](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): pode controlar os clientes permitidos, o tipo de acesso (ler e escrever, só de leitura, etc.). 
- Funcionalidade de ficheiros NetApp do Azure ainda não está ciente de zona. A funcionalidade de ficheiros do Azure NetApp não está implementada em todas as zonas de disponibilidade numa região do Azure. Esteja ciente das implicações de latência de potenciais em algumas regiões do Azure. 

   > [!NOTE]
   > Lembre-se de que o serviço ficheiros do Azure NetApp não suporta ainda a peering da rede Virtual. Implemente as VMs e os volumes de ficheiros do Azure NetApp na mesma rede virtual.

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Implementar VMs do Linux manualmente através do portal do Azure

Primeiro tem de criar os volumes de ficheiros do Azure NetApp. Implemente VMs. Em seguida, pode criar um balanceador de carga e utilize as máquinas virtuais nos agrupamentos de back-end.

1. Criar um Grupo de Recursos
1. Criar uma rede Virtual
1. Criar um conjunto de disponibilidade para ASCS  
   Domínio de atualização máx. de conjunto
1. Criar Máquina Virtual 1  
   Utilize, pelo menos, SP3 de 12 SLES4SAP, neste exemplo é usada a imagem de SLES4SAP 12 SP3  
   Selecione o conjunto de disponibilidade criado anteriormente para ASCS  
1. Criar a Máquina Virtual 2  
   Utilize, pelo menos, SP3 de 12 SLES4SAP, neste exemplo é usada a imagem de SLES4SAP 12 SP3  
   Selecione o conjunto de disponibilidade criado anteriormente para ASCS  
1. Criar um conjunto de disponibilidade para as instâncias de aplicação SAP (PAS, AAS)    
   Domínio de atualização máx. de conjunto
1. Criar Máquina Virtual 3  
   Utilize, pelo menos, SP3 de 12 SLES4SAP, neste exemplo é usada a imagem de SLES4SAP 12 SP3  
   Selecione o conjunto de disponibilidade criado anteriormente para PAS/AAS   
1. Criar Máquina Virtual 4  
   Utilize, pelo menos, SP3 de 12 SLES4SAP, neste exemplo é usada a imagem de SLES4SAP 12 SP3  
   Selecione o conjunto de disponibilidade criado anteriormente para PAS/AAS  

## <a name="setting-up-ascs"></a>Configurar a (A) SCS

Neste exemplo, os recursos foram implementados manualmente através da [portal do Azure](https://portal.azure.com/#home) .

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Implementar o Balanceador de carga do Azure manualmente através do portal do Azure

Primeiro tem de criar os volumes de ficheiros do Azure NetApp. Implemente VMs. Em seguida, pode criar um balanceador de carga e utilize as máquinas virtuais nos agrupamentos de back-end.

1. Criar um balanceador de carga (interno)  
   1. Criar os endereços IP de front-end
      1. Endereço IP 10.1.1.20 para o ASCS
         1. Abra o Balanceador de carga, selecione o conjunto IP de front-end e clique em Adicionar
         1. Introduza o nome do novo conjunto IP de front-end (por exemplo **front-end. QAS. ASCS**)
         1. Definir a atribuição para estático e introduza o endereço IP (por exemplo **10.1.1.20**)
         1. Clique em OK
      1. Endereço IP 10.1.1.21 para o ERS ASCS
         * Repita os passos acima em "a" para criar um endereço IP para o ERS (por exemplo **10.1.1.21** e **front-end. QAS. ERS**)
   1. Criar os conjuntos de back-end
      1. Criar um conjunto de back-end para o ASCS
         1. Abra o Balanceador de carga, selecione conjuntos de back-end e clique em Adicionar
         1. Introduza o nome do novo conjunto de back-end (por exemplo **back-end. QAS**)
         1. Clique em Adicionar uma máquina virtual.
         1. Selecione o conjunto de disponibilidade que criou anteriormente para ASCS 
         1. Selecione as máquinas virtuais de (A) SCS cluster
         1. Clique em OK
   1. Criar as sondas de estado de funcionamento
      1. Porta 620**00** para ASCS
         1. Abra o Balanceador de carga, selecione sondas de estado de funcionamento e clique em Adicionar
         1. Introduza o nome da sonda de estado de funcionamento novo (por exemplo **estado de funcionamento. QAS. ASCS**)
         1. Selecione TCP como protocolo, porta 620**00**, manter intervalo de 5 e 2 de limiar com funcionamento incorreto
         1. Clique em OK
      1. Porta 621**01** para ASCS ERS
            * Repita os passos acima em "c" para criar uma sonda de estado de funcionamento para o ERS (por exemplo 621**01** e **estado de funcionamento. QAS. ERS**)
   1. Regras de balanceamento de carga
      1. 32**00** TCP para ASCS
         1. Abra o Balanceador de carga, selecione as regras de balanceamento de carga e clique em Adicionar
         1. Introduza o nome da nova regra de Balanceador de carga (por exemplo **lb. QAS. ASCS.3200**)
         1. Selecione o endereço IP de front-end para ASCS, o conjunto de back-end e sonda de estado de funcionamento que criou anteriormente (por exemplo **front-end. QAS. ASCS**)
         1. Protocolo de manter **TCP**, introduza a porta **3200**
         1. Aumentar o tempo limite de inatividade para 30 minutos
         1. **Certifique-se de ativar o IP flutuante**
         1. Clique em OK
      1. Portas adicionais para o ASCS
         * Repita os passos acima em "d" para portas 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 e TCP para o ASCS
      1. Portas adicionais para o ERS ASCS
         * Repita os passos acima em "d" para portas 33**01**, 5**01**13, 5**01**14, 5**01**16 e TCP para o ERS ASCS

> [!IMPORTANT]
> Não ative carimbos de data / TCP em VMs do Azure colocadas atrás do Balanceador de carga do Azure. Ativar TCP carimbos fará com que as sondas de estado de funcionamento efetuar a ativação. Defina o parâmetro **net.ipv4.tcp_timestamps** ao **0**. Para obter detalhes, consulte [sondas de estado de funcionamento do Balanceador de carga](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Criar Pacemaker cluster

Siga os passos em [Pacemaker no SUSE Linux Enterprise Server no Azure a configurar](high-availability-guide-suse-pacemaker.md) para criar um basic Pacemaker cluster para este servidor (A) SCS.

### <a name="installation"></a>Instalação

Os seguintes itens são prefixados com ambos **[A]** - aplicáveis a todos os nós, **[1]** – apenas aplicável no nó 1 ou **[2]** – apenas aplicável a nó 2.

1. **[A]**  Instalar o conector SUSE

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Não utilize travessões os nomes de anfitrião dos nós do cluster. Caso contrário, o cluster não funcionará. Esta é uma limitação conhecida e SUSE está trabalhando numa correção. A correção será lançada como um patch do pacote suse-cloud-conector do sap.

   Certifique-se de que instalou a nova versão do conector de cluster do SAP SUSE. Antigo foi chamado sap_suse_cluster_connector e novo é chamado **-suse-cluster-conector do sap**.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
      Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   Version        : 3.1.0-8.1
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 45.6 KiB
   Installed      : Yes
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.1.0-8.1.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]**  Agentes de recursos de atualização SAP  
   
   Um patch para o pacote de agentes do recurso é necessário para utilizar a nova configuração, o que é descrita neste artigo. Pode verificar, se o patch já estiver instalado com o seguinte comando

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   O resultado deve ser semelhante a

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Se o comando de grep não encontrar o parâmetro IS_ERS, tem de instalar o patch listado no [página de transferência do SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]**  Configurar a resolução de nomes de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e o nome de anfitrião para corresponder ao seu ambiente   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Preparar para a instalação do SAP NetWeaver

1. **[A]**  Criar os diretórios partilhados

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]**  Configurar autofs

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Crie um ficheiro com o

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > Atualmente, os ficheiros do Azure NetApp suportam apenas NFSv3. Não omita o nfsvers = 3 comutador.
   
   Reiniciar autofs montar as partilhas de novo
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]**  Configurar TROCAR ficheiros

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reinicie o agente para ativar a alteração

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Instalar o SAP NetWeaver ASCS/ERS

1. **[1]**  Criar um recurso IP virtual e uma sonda de estado de funcionamento para a instância do ASCS

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante no nó que os recursos estão em execução.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #     rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** Install SAP NetWeaver ASCS  

   Instalar o SAP NetWeaver ASCS como raiz no primeiro nó com um nome de anfitrião virtual que mapeia para o endereço IP da configuração de front-end de Balanceador de carga para ASCS, por exemplo <b>anftstsapvh</b>, <b>10.1.1.20</b> e o número de instância que utilizou para a sonda do Balanceador de carga, por exemplo <b>00</b>.

   Pode utilizar o parâmetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não raiz ligar ao sapinst. Pode utilizar o parâmetro SAPINST_USE_HOSTNAME para instalar o SAP, com o nome de anfitrião virtual.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Se a instalação falha criar uma subpasta no /usr/sap/**QAS**/ASCS**00**, tente definir o proprietário e o grupo do ASCS**00** pasta e tente novamente. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]**  Criar um recurso IP virtual e uma sonda de estado de funcionamento para a instância ERS

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>01</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante no nó que os recursos estão em execução.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]**  Instalar ERS do SAP NetWeaver

   Instalar o SAP NetWeaver ERS como raiz no segundo nó com um nome de anfitrião virtual que mapeia para o endereço IP da configuração de front-end de Balanceador de carga para ERS, por exemplo <b>anftstsapers</b>, <b>10.1.1.21</b> e o número de instância que utilizou para a sonda do Balanceador de carga, por exemplo <b>01</b>.

   Pode utilizar o parâmetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não raiz ligar ao sapinst. Pode utilizar o parâmetro SAPINST_USE_HOSTNAME para instalar o SAP, com o nome de anfitrião virtual.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Utilize SWPM SP 20 PL 05 ou superior. Versões menores não defina as permissões corretamente e a instalação irá falhar.

   Se a instalação falha criar uma subpasta no /usr/sap/**QAS**/ERS**01**, tente definir o proprietário e o grupo do ERS**01** pasta e tente novamente.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]**  Adapt ASCS/SCS e ERS perfis de instância
 
   * Perfil do ASCS/SCS

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Perfil ERS

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]**  Configurar Keep Alive de

   A comunicação entre o servidor de aplicações SAP NetWeaver e ASCS/SCS é encaminhada através de um balanceador de carga de software. O Balanceador de carga desliga ligações inativas após um tempo limite configurável. Para impedir que isto terá de definir um parâmetro no perfil do SAP NetWeaver ASCS/SCS e alterar as definições do sistema Linux. Leia [1410736 de nota SAP] [ 1410736] para obter mais informações.

   O ASCS/SCS perfil parâmetro colocar/encni/set_so_keepalive já foi adicionado no último passo.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. **[A]**  Configurar os utilizadores SAP após a instalação

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]**  Adicionar os serviços do ASCS e ERS SAP para o ficheiro de sapservice

   Adicione os ASCS entrada para o segundo nó de serviço e copiar a entrada do serviço ERS para o primeiro nó.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]**  Criam os recursos de cluster SAP

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante no nó que os recursos estão em execução.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparação do SAP NetWeaver application server 

Algumas bases de dados requerem que a instalação de instância de base de dados é executada num servidor de aplicações. Prepare as máquinas de virtuais de servidor de aplicativo para poder utilizá-las nestes casos.

O passos abaixo partem do princípio de que instalou o servidor de aplicações num servidor diferente do que os servidores do ASCS/SCS e HANA. Caso contrário, alguns dos passos abaixo (como configurar a resolução de nomes de anfitrião) não são necessários.

Os seguintes itens são prefixados com ambos **[A]** - aplicável para PAS e AAS, **[P]** – apenas aplicável a PAS ou **[S]** – apenas aplicável a AAS.


1. **[A]**  Configurar sistema operativo

   Reduza o tamanho da cache modificado. Para obter mais informações, consulte [desempenho de escrita de baixa no SLES 11/12 servidores com grandes RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]**  Configurar a resolução de nomes de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos

   ```bash
   sudo vi /etc/hosts
   ```

   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e o nome de anfitrião para corresponder ao seu ambiente

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]**  Criar o diretório de /sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]**  Criar o diretório PAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]**  Criar o diretório de AAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]**  Configurar autofs em PAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Criar um novo arquivo com

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/ursap<b>qas</b>pas
   </code></pre>

   Reiniciar autofs montar as partilhas de novo

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]**  Configurar autofs em AAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Criar um novo arquivo com

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   Reiniciar autofs montar as partilhas de novo

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]**  Configurar TROCAR ficheiros

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

   Reinicie o agente para ativar a alteração

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalar base de dados

Neste exemplo, SAP NetWeaver está instalado no SAP HANA. Pode usar cada base de dados suportado para esta instalação. Para obter mais informações sobre como instalar o SAP HANA no Azure, consulte [disponibilidade elevada do SAP HANA em máquinas virtuais do Azure (VMs)][sap-hana-ha]. Para obter uma lista de bases de dados suportados, consulte [SAP 1928533 de observação][1928533].

* Executar a instalação de instância de base de dados do SAP

   Instale a instância de base de dados do SAP NetWeaver como raiz com um nome de anfitrião virtual que mapeia para o endereço IP da configuração de front-end de Balanceador de carga para a base de dados.

   Pode utilizar o parâmetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não raiz ligar ao sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalação de servidor de aplicativos do SAP NetWeaver

Siga estes passos para instalar um servidor de aplicações SAP.

1. **[A]**  Servidor de aplicativos de preparação, siga os passos no capítulo [preparação de servidor de aplicação SAP NetWeaver](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) acima para preparar o servidor de aplicações.

2. **[A]**  Servidor de aplicativos de instalar o SAP NetWeaver instalar um servidor de aplicações SAP NetWeaver primário ou adicional.

   Pode utilizar o parâmetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não raiz ligar ao sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]**  Arquivo seguro do SAP HANA de atualização

   Atualize o arquivo seguro do SAP HANA para apontar para o nome virtual da configuração do SAP HANA System Replication.

   Execute o seguinte comando para listar as entradas
   <pre><code>
   hdbuserstore List
   </code></pre>

   Isso deve listar todas as entradas e deve ter um aspeto semelhante a
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   O resultado mostra que o endereço IP da entrada predefinida está a apontar para a máquina virtual e não para o endereço IP do Balanceador de carga. Esta entrada tem de ser alterado para apontar para o nome de anfitrião virtual do Balanceador de carga. Lembre-se de que utilizam a mesma porta (**30313** no resultado acima) e o nome de base de dados (**QAS** no resultado acima)!

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Teste a configuração de cluster

Os seguintes testes são uma cópia dos casos de teste nos [melhores guias de práticas de SUSE][suse-ha-guide]. São copiados para sua comodidade. Sempre também leia os guias de práticas recomendados e executar todos os testes adicionais que podem ter sido adicionados.

1. Testar HAGetFailoverConfig, HACheckConfig e HACheckFailoverConfig

   Execute os seguintes comandos como \<sapsid > adm no nó onde a instância do ASCS está em execução. Se os comandos falharem com falha: Memória insuficiente, ele poderá ser causado por travessões em seu nome de anfitrião. Este é um problema conhecido e será corrigida através da SUSE no pacote suse-cluster-conector do sap.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. Migrar manualmente a instância do ASCS

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Execute os seguintes comandos como raiz para migrar a instância do ASCS.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. HAFailoverToNode de teste

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Execute os seguintes comandos como <sapsid>adm para migrar a instância do ASCS.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Simular falhas de nó 

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Execute o seguinte comando como raiz no nó onde a instância do ASCS está em execução

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Se usar SBD, Pacemaker não deve iniciar automaticamente no nó cancelado. O estado após o nó é iniciado novamente deve ter um aspeto semelhante a esta.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Utilize os seguintes comandos para iniciar Pacemaker no nó cancelado, limpar as mensagens SBD e limpar os recursos com falha.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Reinício manual do teste da instância do ASCS

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Crie um bloqueio de colocar em fila, para a edição de exemplo num utilizador su01 de transação. Execute os seguintes comandos como < sapsid\>adm no nó onde a instância do ASCS está em execução. Os comandos irão parar a instância do ASCS e iniciá-lo novamente. O bloqueio de colocar em fila é esperado que sejam perdidos nesse teste.

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   A instância do ASCS agora deve ser desativada no Pacemaker

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Inicie novamente a instância do ASCS no mesmo nó.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   O bloqueio de colocar em fila de transação su01 deve ser perdido e o back-end deve ter sido redefinido. Estado do recurso depois do teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Interrompe o processo de servidor de mensagens

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Execute os seguintes comandos como raiz para identificar o processo do servidor de mensagens e vai encerrá-lo.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Se apenas eliminar o servidor de mensagens uma vez, ele será reiniciado pelo sapstart. Se vai encerrá-lo com frequência que suficiente, Pacemaker acabará mova a instância do ASCS no outro nó. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância do ASCS e ERS após o teste.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Finalizar processo do servidor de colocar em fila

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Execute os seguintes comandos como raiz no nó onde a instância do ASCS está em execução para eliminar o servidor de colocar em fila.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   A instância do ASCS deve imediatamente a ativação pós-falha no outro nó. A instância ERS também deve efetuar a ativação pós-falha depois da instância do ASCS é iniciada. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância do ASCS e ERS após o teste.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Interrompe o processo de servidor de replicação de colocar em fila

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Execute o seguinte comando como raiz no nó onde a instância ERS está em execução para interromper o processo de servidor de replicação de colocar em fila.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Se apenas executar o comando uma vez, sapstart reiniciará o processo. Se executá-lo com frequência suficiente, irá sapstart não reiniciar o processo e o recurso vai estar no estado parado. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância ERS após o teste.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado do recurso depois do teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Finalizar processo sapstartsrv de colocar em fila

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Execute os seguintes comandos como raiz no nó onde o ASCS está em execução.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   O processo de sapstartsrv sempre deve ser reiniciado pelo agente de recurso Pacemaker. Estado do recurso depois do teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Passos Seguintes

* [Máquinas de virtuais de planeamento e implementação de SAP do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a elevada disponibilidade e o plano de recuperação após desastre de SAP 
* HANA no Azure (instâncias grandes), consulte [SAP HANA (instâncias grandes) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer a elevada disponibilidade e o plano de recuperação após desastre do SAP HANA em VMs do Azure, veja [disponibilidade elevada do SAP HANA em máquinas virtuais do Azure (VMs)][sap-hana-ha]
