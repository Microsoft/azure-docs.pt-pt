---
title: VMs Azure alta disponibilidade para SAP NW em SLES com Ficheiros Azure NetApp. Microsoft Docs
description: Guia de alta disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server com Ficheiros Azure NetApp para aplicações SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: radeltch
ms.openlocfilehash: 18aecfc5ea40c8368fbf4d4a07f86e71047265f7
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598651"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server com Ficheiros Azure NetApp para aplicações SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
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

Este artigo descreve como implementar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um sistema SAP NetWeaver 7.50 altamente disponível, utilizando [ficheiros Azure NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)
Nas configurações de exemplo, comandos de instalação etc., a instância ASCS é o número 00, a instância ERS número 01, a instância de Aplicação Primária (PAS) é 02 e a instância de aplicação (AAS) é 03. É utilizado o QAS de ID do sistema SAP. 

Este artigo explica como alcançar uma elevada disponibilidade para a aplicação SAP NetWeaver com Ficheiros Azure NetApp. A camada de base de dados não está coberta de detalhes neste artigo.

Leia primeiro as seguintes Notas e papéis SAP:

* [Documentação de Ficheiros Azure NetApp][anf-azure-doc] 
* Nota SAP [1928533,][1928533]que tem:  
  * Lista de tamanhos De VM Azure que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade para tamanhos de VM Azure
  * Software SAP suportado e sistema operativo (OS) e combinações de bases de dados
  * Versão necessária do kernel SAP para Windows e Linux no Microsoft Azure
* O SAP Note [2015553][2015553] lista os pré-requisitos para implementações de software SAP suportadas pela SAP em Azure.
* SAP Nota [2205917][2205917] recomendou definições de OS para SUSE Linux Enterprise Server para Aplicações SAP
* SAP Nota [1944799][1944799] tem Diretrizes SAP HANA para SUSE Linux Enterprise Server para Aplicações SAP
* O SAP Note [2178632][2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* O SAP Note [2191498][2191498] tem a versão necessária do Agente anfitrião SAP para o Linux em Azure.
* SAP Nota [2243692][2243692] tem informações sobre licenciamento SAP em Linux em Azure.
* SAP Note [1984787][1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A Nota [SAP 1999351][1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização avançada do Azure para sAP.
* SAP Community WIKI.(https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) exigiu todas as notas SAP para linux.
* [Planeamento e implementação de Máquinas Virtuais Azure para SAP em Linux][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP em Linux][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP em Linux][dbms-guide]
* [Guias de boas práticas SUSE SAP HA][suse-ha-guide] Os guias contêm todas as informações necessárias para configurar a Replicação do Sistema Netweaver HA e SAP HANA no local. Utilize estes guias como base geral. Fornecem informações muito mais detalhadas.
* [Extensão de alta disponibilidade sUSE 12 Notas de lançamento SP3][suse-ha-12sp3-relnotes]
* [Aplicações NetApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Descrição geral

A elevada disponibilidade (HA) para os serviços centrais SAP Netweaver requer armazenamento partilhado.
Para isso, no SUSE Linux, até agora, era necessário construir um cluster NFS altamente disponível. 

Agora é possível alcançar o SAP Netweaver HA utilizando armazenamento partilhado, implantado em Ficheiros Azure NetApp. A utilização de Ficheiros Azure NetApp para o armazenamento partilhado elimina a necessidade de [um cluster NFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)adicional . O pacemaker ainda é necessário para o HA dos serviços centrais SAP Netweaver (ASCS/SCS).


![Visão geral de alta disponibilidade da SAP NetWeaver](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e a base de dados SAP HANA usam o nome de anfitrião virtual e endereços IP virtuais. No Azure, [é](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) necessário utilizar um endereço IP virtual. Recomendamos a utilização do [equilíbrio de carga Standard](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). A lista seguinte mostra a configuração do (A)SCS e do equilibrador de carga ERS.

> [!IMPORTANT]
> O agrupamento multi-SID do SAP ASCS/ERS com o SUSE Linux como sistema operativo convidado em VMs Azure NÃO é **suportado**. O clustering multi-SID descreve a instalação de múltiplas instâncias SAP ASCS/ERS com diferentes SIDs num cluster Pacemaker


### <a name="ascs"></a>A SCS

* Configuração frontend
  * Endereço IP 10.1.1.20
* Configuração de backend
  * Ligado às interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte do cluster (A)SCS/ERS
* Porta sonda
  * Porto 620<strong>&lt;nr&gt;</strong>
* Regras de equilíbrio de carga
  * Se utilizar o Balancer de Carga Padrão, selecione **portas HA**
  * Se utilizar o Equilíbrio de Carga Básico, crie regras de equilíbrio de carga para as seguintes portas
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configuração frontend
  * Endereço IP 10.1.1.21
* Configuração de backend
  * Ligado às interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte do cluster (A)SCS/ERS
* Porta sonda
  * Porto 621<strong>&lt;nr&gt;</strong>
* Regras de equilíbrio de carga
  * Se utilizar o Balancer de Carga Padrão, selecione **portas HA**
  * Se utilizar o Equilíbrio de Carga Básico, crie regras de equilíbrio de carga para as seguintes portas
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Criação da infraestrutura de ficheiros Azure NetApp 

A SAP NetWeaver requer armazenamento partilhado para o diretório de transporte e perfil.  Antes de prosseguir com a configuração da infraestrutura de ficheiros Azure NetApp, familiarize-se com a documentação dos [Ficheiros Azure NetApp.][anf-azure-doc] Verifique se a sua região azure selecionada oferece ficheiros Azure NetApp. O seguinte link mostra a disponibilidade de Ficheiros Azure NetApp pela região do Azure: Disponibilidade de [ficheiros Azure NetApp pela Região do Azure][anf-avail-matrix].

Os ficheiros Azure NetApp estão disponíveis em [várias regiões do Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Antes de implementar ficheiros Azure NetApp, solicite o embarque nos Ficheiros Azure NetApp, seguindo as instruções do [Registo de Ficheiros Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Implementar recursos de Ficheiros Azure NetApp  

Os passos assumem que já implantou a [Rede Virtual Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Os recursos do Azure NetApp Files e os VMs, onde serão montados os recursos dos Ficheiros Azure NetApp devem ser implantados na mesma Rede Virtual Azure ou em Redes Virtuais Azure.  

1. Se ainda não o fez, solicite [o embarque nos Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Crie a conta NetApp na região do Azure selecionada, seguindo as [instruções para criar a Conta NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Configurar o conjunto de capacidades dos Ficheiros Azure NetApp, seguindo as [instruções sobre como configurar o conjunto de capacidades dos Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
A arquitetura SAP Netweaver apresentada neste artigo utiliza um único pool de capacidade sku do Azure NetApp Files, Premium SKU. Recomendamos o Azure NetApp Files Premium SKU para a carga de trabalho da aplicação SAP Netweaver no Azure.  

4. Delege uma subnet para ficheiros Azure NetApp, conforme descrito nas [instruções, delege uma subnet a Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Implemente volumes de Ficheiros Azure NetApp, seguindo as [instruções para criar um volume para ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Implementar os volumes na [sub-rede](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)de Ficheiros Azure NetApp designados . Tenha em mente que os recursos do Azure NetApp Files e os VMs Azure devem estar na mesma Rede Virtual Azure ou em redes virtuais azure peered. Por exemplo, sapmnt<b>QAS</b>, usrsap<b>QAS</b>, etc. são os nomes de volume e<b>qas</b>sapmnt, usrsap<b>qas</b>, etc. são os arquivos para os volumes de Ficheiros Azure NetApp.  

   1. volume sapmnt<b>QAS</b> (nfs://10.1.0.4/sapmnt<b>qas)</b>
   2. volume usrsap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas)</b>
   3. sys<b>qAS</b>de volume usrsap (nfs://10.1.0.5/usrsap<b>qas</b>sys)
   4. volume usrsap<b>QAS</b>ers (nfs://10.1.0.4/usrsap<b>qas</b>ers)
   5. volume trans (nfs://10.1.0.4/trans)
   6. volume usrsap<b>QAS</b>pas (nfs://10.1.0.5/usrsap<b>qas</b>pas)
   7. volume usrsap<b>QAS</b>aas (nfs://10.1.0.4/usrsap<b>qas aas)</b>
   
Neste exemplo, usamos ficheiros Azure NetApp para todos os sistemas de ficheiros SAP Netweaver para demonstrar como os Ficheiros Azure NetApp podem ser usados. Os sistemas de ficheiroS SAP que não necessitam de ser montados via NFS também podem ser implantados como armazenamento de [disco Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . Neste exemplo, <b>a-e</b> deve estar nos Ficheiros Azure NetApp e <b>f-g</b> (isto é, /usr/seiva/<b>QAS</b>/D<b>02,</b>/usr/seiva/<b>QAS</b>/D<b>03</b>) poderia ser implantado como armazenamento de disco Azure. 

### <a name="important-considerations"></a>Considerações importantes

Ao considerar os Ficheiros Azure NetApp para o SAP Netweaver na arquitetura de alta disponibilidade da SUSE, esteja ciente das seguintes considerações importantes:

- A piscina de capacidade mínima é 4 TiB. O tamanho da piscina de capacidade pode ser aumentado em incrementos de 1 TiB.
- O volume mínimo é de 100 GiB
- Os Ficheiros Azure NetApp e todas as máquinas virtuais, onde serão montados volumes de Ficheiros Azure NetApp, devem estar na mesma Rede Virtual Azure ou em [redes virtuais na](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mesma região. O acesso do Azure NetApp Files ao VNET na mesma região é agora suportado. O acesso do Azure NetApp ao que o peering global ainda não é suportado.
- A rede virtual selecionada deve ter uma subnet, delegada nos Ficheiros Azure NetApp.
- O Azure NetApp Files oferece política de [exportação:](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)pode controlar os clientes permitidos, o tipo de acesso (Read&Write, Read Only, etc.). 
- A funcionalidade De Ficheiros Azure NetApp ainda não está ciente da zona. Atualmente, a funcionalidade Azure NetApp Files não está implementada em todas as zonas de disponibilidade de uma região do Azure. Esteja ciente das potenciais implicações de latência em algumas regiões de Azure. 
- Os volumes de ficheiros Azure NetApp podem ser implementados como volumes NFSv3 ou NFSv4.1. Ambos os protocolos são suportados para a camada de aplicação SAP (ASCS/ERS, servidores de aplicações SAP). 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Implementar VMs Linux manualmente através do portal Azure

Primeiro, é necessário criar os volumes de Ficheiros Azure NetApp. Implante os VMs. Depois, cria-se um equilibrador de carga e utiliza as máquinas virtuais nas piscinas de backend.

1. Criar um Grupo de Recursos
1. Criar uma Rede Virtual
1. Criar um conjunto de disponibilidade para ASCS  
   Definir domínio de atualização max
1. Criar máquina virtual 1  
   Utilize pelo menos SLES4SAP 12 SP3, neste exemplo é utilizada a imagem SLES4SAP 12 SP3  
   Selecione Conjunto de Disponibilidade criado anteriormente para ASCS  
1. Criar máquina virtual 2  
   Utilize pelo menos SLES4SAP 12 SP3, neste exemplo é utilizada a imagem SLES4SAP 12 SP3  
   Selecione Conjunto de Disponibilidade criado anteriormente para ASCS  
1. Criar um Conjunto de Disponibilidade para as instâncias de aplicação SAP (PAS, AAS)    
   Definir domínio de atualização max
1. Criar máquina virtual 3  
   Utilize pelo menos SLES4SAP 12 SP3, neste exemplo é utilizada a imagem SLES4SAP 12 SP3  
   Selecione Conjunto de Disponibilidade criado anteriormente para PAS/AAS   
1. Criar máquina virtual 4  
   Utilize pelo menos SLES4SAP 12 SP3, neste exemplo é utilizada a imagem SLES4SAP 12 SP3  
   Selecione Conjunto de Disponibilidade criado anteriormente para PAS/AAS  

## <a name="disable-id-mapping-if-using-nfsv41"></a>Desativar o mapeamento de ID (se utilizar NFSv4.1)

As instruções nesta secção só são aplicáveis, se utilizarem volumes de Ficheiros Azure NetApp com protocolo NFSv4.1. Execute a configuração em todos os VMs, onde serão montados volumes NFSv4.1 do Azure NetApp.  

1. Verifique a definição de domínio NFS. Certifique-se de que o domínio está configurado como o domínio padrão dos Ficheiros Azure NetApp, ou seja, **`defaultv4iddomain.com`** e o mapeamento não está definido para **ninguém**.  

    > [!IMPORTANT]
    > Certifique-se de que define o domínio NFS em `/etc/idmapd.conf` no VM para corresponder à configuração de domínio predefinido nos Ficheiros Azure NetApp: **`defaultv4iddomain.com`** . Se houver uma incompatibilidade entre a configuração de domínio no cliente NFS (isto é, o VM) e o servidor NFS, ou seja, a configuração Azure NetApp, então as permissões para ficheiros nos volumes Azure NetApp que são montados nos VMs serão apresentadas como `nobody`.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** Verificar `nfs4_disable_idmapping`. Deve ser definido para **Y.** Para criar a estrutura do diretório onde está localizada `nfs4_disable_idmapping`, execute o comando do suporte. Não poderá criar manualmente o diretório em /sys/módulos, porque o acesso é reservado para o núcleo/controladores.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.1.0.4:/sapmnt/<b>qas</b> /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>


## <a name="setting-up-ascs"></a>Configuração (A)SCS

Neste exemplo, os recursos foram implantados manualmente através do [portal Azure.](https://portal.azure.com/#home)

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Implementar o Equilíbrio de Carga Azure manualmente através do portal Azure

Primeiro, é necessário criar os volumes de Ficheiros Azure NetApp. Implante os VMs. Depois, cria-se um equilibrador de carga e utiliza as máquinas virtuais nas piscinas de backend.

1. Criar um equilibrador de carga (interno, standard):  
   1. Crie os endereços IP frontend
      1. Endereço IP 10.1.1.20 para o ASCS
         1. Abra o equilibrador de carga, selecione pool IP frontend e clique em Adicionar
         1. Introduza o nome da nova piscina IP frontend (por exemplo **frontend. QAS. ASCS)**
         1. Definir a Atribuição à Estática e introduzir o endereço IP (por exemplo **10.1.1.20**)
         1. Clique em OK
      1. Endereço IP 10.1.1.21 para a ASCS ERS
         * Repita os passos acima em "a" para criar um endereço IP para o ERS (por exemplo **10.1.1.21** e **frontend. QAS. ERS**)
   1. Crie as piscinas de backend
      1. Crie uma piscina de backend para o ASCS
         1. Abra o equilibrador de carga, selecione piscinas de backend e clique em Adicionar
         1. Introduza o nome da nova piscina de backend (por **exemplo, backend. QAS)**
         1. Clique em Adicionar uma máquina virtual.
         1. Selecione máquina Virtual
         1. Selecione as máquinas virtuais do cluster (A)SCS e os seus endereços IP.
         1. Clique em Adicionar
   1. Criar as sondas de saúde
      1. Porta 620**00** para ASCS
         1. Abra o equilibrador de carga, selecione sondas de saúde e clique em Adicionar
         1. Introduza o nome da nova sonda de saúde (por **exemplo, saúde. QAS. ASCS)**
         1. Selecione TCP como protocolo, porta 620**00,** mantenha intervalo 5 e limiar insalubre 2
         1. Clique em OK
      1. Porto 621**01** para ASCS ERS
            * Repita os passos acima em "c" para criar uma sonda de saúde para a ERS (por exemplo 621**01** e **saúde. QAS. ERS**)
   1. Regras de equilíbrio de carga
      1. Crie uma piscina de backend para o ASCS
         1. Abra o equilibrador de carga, selecione regras de equilíbrio de carga e clique em Adicionar
         1. Introduza o nome da nova regra do equilibrador de carga (por **exemplo, lb. QAS. ASCS)**
         1. Selecione o endereço IP frontend para ASCS, piscina de backend e sonda de saúde que criou anteriormente (por **exemplo, frontend. QAS. ASCS,** **backend. QAS** e **saúde. QAS. ASCS)**
         1. Selecione **portas HA**
         1. Aumente o tempo inativo para 30 minutos
         1. **Certifique-se de ativar o IP flutuante**
         1. Clique em OK
         * Repita os passos acima para criar regras de equilíbrio de carga para ERS (por **exemplo, lb. QAS. ERS**)
1. Alternativamente, se o seu cenário requer um equilíbrio básico de carga (interno), siga estes passos:  
   1. Crie os endereços IP frontend
      1. Endereço IP 10.1.1.20 para o ASCS
         1. Abra o equilibrador de carga, selecione pool IP frontend e clique em Adicionar
         1. Introduza o nome da nova piscina IP frontend (por exemplo **frontend. QAS. ASCS)**
         1. Definir a Atribuição à Estática e introduzir o endereço IP (por exemplo **10.1.1.20**)
         1. Clique em OK
      1. Endereço IP 10.1.1.21 para a ASCS ERS
         * Repita os passos acima em "a" para criar um endereço IP para o ERS (por exemplo **10.1.1.21** e **frontend. QAS. ERS**)
   1. Crie as piscinas de backend
      1. Crie uma piscina de backend para o ASCS
         1. Abra o equilibrador de carga, selecione piscinas de backend e clique em Adicionar
         1. Introduza o nome da nova piscina de backend (por **exemplo, backend. QAS)**
         1. Clique em Adicionar uma máquina virtual.
         1. Selecione o Conjunto de Disponibilidade que criou anteriormente para ASCS 
         1. Selecione as máquinas virtuais do cluster (A)SCS
         1. Clique em OK
   1. Criar as sondas de saúde
      1. Porta 620**00** para ASCS
         1. Abra o equilibrador de carga, selecione sondas de saúde e clique em Adicionar
         1. Introduza o nome da nova sonda de saúde (por **exemplo, saúde. QAS. ASCS)**
         1. Selecione TCP como protocolo, porta 620**00,** mantenha intervalo 5 e limiar insalubre 2
         1. Clique em OK
      1. Porto 621**01** para ASCS ERS
            * Repita os passos acima em "c" para criar uma sonda de saúde para a ERS (por exemplo 621**01** e **saúde. QAS. ERS**)
   1. Regras de equilíbrio de carga
      1. 32**00** TCP para ASCS
         1. Abra o equilibrador de carga, selecione regras de equilíbrio de carga e clique em Adicionar
         1. Introduza o nome da nova regra do equilibrador de carga (por **exemplo, lb. QAS. ASCS.3200**)
         1. Selecione o endereço IP frontend para ASCS, piscina de backend e sonda de saúde que criou anteriormente (por **exemplo, frontend. QAS. ASCS)**
         1. Manter o protocolo **TCP,** entrar na porta **3200**
         1. Aumente o tempo inativo para 30 minutos
         1. **Certifique-se de ativar o IP flutuante**
         1. Clique em OK
      1. Portas adicionais para o ASCS
         * Repita os passos acima em "d" para os portos 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 e TCP para o ASCS
      1. Portas adicionais para a ASCS ERS
         * Repita os passos acima em "d" para os portos 33**01**, 5**01**13, 5**01**14, 5**01**16 e TCP para as ASCS ERS

      > [!Note]
      > Quando os VMs sem endereços IP públicos forem colocados no conjunto de backend do interno (sem endereço IP público) O equilíbrio de carga Standard Azure não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos. Para mais detalhes sobre como alcançar a conectividade de saída, consulte [a conectividade do ponto final público para máquinas virtuais utilizando o Equilíbrio de Carga Padrão Azure em cenários de alta disponibilidade SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

      > [!IMPORTANT]
      > Não permita os carimbos de tempo da TCP em VMs Azure colocados atrás do Equilíbrio de Carga Azure. Permitir os selos temporais da TCP fará com que as sondas de saúde falhem. Definir parâmetro **net.ipv4.tcp_timestamps** a **0**. Para mais detalhes consulte as sondas de [saúde load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Criar cluster Pacemaker

Siga os passos na configuração do [Pacemaker no SUSE Linux Enterprise Server em Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster pacemaker básico para este servidor (A)SCS.

### <a name="installation"></a>Instalação

Os seguintes itens são pré-fixados com **[A]** - aplicável a todos os nós, **[1]** - apenas aplicável ao nó 1 ou **[2]** - apenas aplicável ao nó 2.

1. **[A]** Instalar conector SUSE

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > O problema conhecido com a utilização de um traço nos nomes do anfitrião é fixado com a versão **3.1.1** do **conector de conjunto sap-suse-suse.** Certifique-se de que está a utilizar pelo menos a versão 3.1.1 do conector de aglomerado de seiva-suse,se utilizar nós de cluster com traço no nome do hospedeiro. Caso contrário, o seu agrupamento não funcionará. 

   Certifique-se de que instalou a nova versão do conector de cluster SAP SUSE. O antigo chamava-se sap_suse_cluster_connector e o novo **chama-se conector de aglomerado de sáb.**

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
    # Information for package sap-suse-cluster-connector:
    # ---------------------------------------------------
    # Repository     : SLE-12-SP3-SAP-Updates
    # Name           : sap-suse-cluster-connector
    # Version        : 3.1.0-8.1
    # Arch           : noarch
    # Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
    # Support Level  : Level 3
    # Installed Size : 45.6 KiB
    # Installed      : Yes
    # Status         : up-to-date
    # Source package : sap-suse-cluster-connector-3.1.0-8.1.src
    # Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]** Atualizar agentes de recursos SAP  
   
   Um patch para o pacote de agentes de recursos é necessário para usar a nova configuração, que é descrita neste artigo. Pode verificar se o patch já está instalado com o seguinte comando

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   A saída deve ser semelhante a

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Se o comando grep não encontrar o parâmetro IS_ERS, tem de instalar o patch listado na página de [descarregamento sUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]** Configuração resolução de nome de anfitrião

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

## <a name="prepare-for-sap-netweaver-installation"></a>Prepare-se para a instalação SAP NetWeaver

1. **[A]** Criar os diretórios partilhados

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

2. **Configurar** `autofs`

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Se utilizar o NFSv3, crie um ficheiro com:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   Se utilizar o NFSv4.1, crie um ficheiro com:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sync,sec=sys 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sync,sec=sys 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=4.1,nobind,sync,sec=sys 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > Certifique-se de que corresponde à versão protocolar NFS dos volumes de Ficheiros Azure NetApp, ao montar os volumes. Se os volumes de Ficheiros Azure NetApp forem criados como volumes NFSv3, utilize a configuração NFSv3 correspondente. Se os volumes de Ficheiros Azure NetApp forem criados como volumes NFSv4.1, siga as instruções para desativar o mapeamento de ID e certifique-se de utilizar a configuração NFSv4.1 correspondente. Neste exemplo, os volumes de Ficheiros Azure NetApp foram criados como volumes NFSv3.  
   
   Reiniciar `autofs` para montar as novas ações
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]** Arquivo SWAP configurar

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reiniciar o Agente para ativar a alteração

   <pre><code>sudo service waagent restart
   </code></pre>

### <a name="installing-sap-netweaver-ascsers"></a>Instalação SAP NetWeaver ASCS/ERS

1. **[1]** Criar um recurso IP virtual e uma sonda de saúde para a instância ASCS

   > [!IMPORTANT]
   > Testes recentes revelaram situações, em que o netcat deixa de responder aos pedidos devido a atrasos e à sua limitação de manuseamento apenas uma ligação. O recurso netcat para de ouvir os pedidos do balanceador de carga Azure e o IP flutuante fica indisponível.  
   > Para os aglomerados Pacemaker existentes, recomendamos a substituição do netcat por socat, seguindo as instruções em Deteção de [Equilíbrio de Carga Azure](https://www.suse.com/support/kb/doc/?id=7024128). Note que a alteração exigirá um breve tempo de inatividade.  

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:620<b>00</b>,backlog=10,fork,reuseaddr /dev/null" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante em que nó dos recursos estão a funcionar.

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
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** Instalar asCS SAP NetWeaver  

   Instale o SAP NetWeaver ASCS como raiz no primeiro nó utilizando um nome de anfitrião virtual que mapeie para o endereço IP da configuração frontal do equilíbrio de carga para o ASCS, por exemplo <b>anftstsapvh</b>, <b>10.1.1.20</b> e o número de instância que utilizou para a sonda do equilibrista de carga, por exemplo <b>00</b>.

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst. Pode utilizar SAPINST_USE_HOSTNAME de parâmetros para instalar o SAP, utilizando o nome de anfitrião virtual.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Se a instalação não criar uma subpasta em /usr/seap/**QAS**/ASCS**00,** tente configurar o proprietário e o grupo da pasta ASCS**00** e voltar a tentar. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** Criar um recurso IP virtual e uma sonda de saúde para a instância DA

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' options='sec=sys,vers=4.1'\
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:621<b>01</b>,backlog=10,fork,reuseaddr /dev/null" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/socat"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante em que nó dos recursos estão a funcionar.

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

4. **[2]** Instalar SAP NetWeaver ERS

   Instale o SAP NetWeaver ERS como raiz no segundo nó utilizando um nome de anfitrião virtual que mapeie para o endereço IP da configuração frontal do equilíbrio de carga para o ERS, por exemplo <b>anftstsapersapers</b>, <b>10.1.1.21</b> e o número de instância que usou para a sonda do equilibrista de carga, por exemplo <b>01</b>.

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst. Pode utilizar SAPINST_USE_HOSTNAME de parâmetros para instalar o SAP, utilizando o nome de anfitrião virtual.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Utilize SWPM SP 20 PL 05 ou superior. Versões inferiores não fixam corretamente as permissões e a instalação falhará.

   Se a instalação não criar uma subpasta em /usr/seap/**QAS/ERS** **01**, tente configurar o proprietário e o grupo da pasta ERS**01** e voltar a tentar.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]** Adaptar os perfis de instância ASCS/SCS e ERS
 
   * Perfil ASCS/SCS

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

6. **[A]** Configure Manter Vivo

   A comunicação entre o servidor de aplicação SAP NetWeaver e o ASCS/SCS é encaminhada através de um equilíbriode carga de software. O equilibrador de carga desliga ligações inativas após um tempo de tempo configurável. Para evitar isto, é necessário definir um parâmetro no perfil SAP NetWeaver ASCS/SCS e alterar as definições do sistema Linux. Leia [a Nota SAP 1410736][1410736] para mais informações.

   O parâmetro de perfil ASCS/SCS enque/ncni/set_so_keepalive já foi adicionado no último passo.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. **[A]** Configurar os utilizadores do SAP após a instalação

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** Adicione os serviços ASCS e ERS SAP ao ficheiro `sapservice`

   Adicione a entrada de serviço ASCS ao segundo nó e copie a entrada de serviço ERS no primeiro nó.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** Criar os recursos de cluster SAP

Se utilizar a arquitetura enqueue server 1 (ENSA1), defina os recursos da seguinte forma:

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

   O SAP introduziu suporte para o servidor de fila 2, incluindo replicação, a partir de SAP NW 7.52. A partir da Plataforma ABAP 1809, o servidor enfila 2 é instalado por padrão. Consulte a nota SAP [2630416](https://launchpad.support.sap.com/#/notes/2630416) para obter suporte ao servidor 2.
Se utilizar a arquitetura enqueue server 2[(ENSA2),](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)defina os recursos da seguinte forma:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Se estiver a atualizar a partir de uma versão mais antiga e a mudar para o servidor de fila 2, consulte a nota [SAP 2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante em que nó dos recursos estão a funcionar.

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

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparação do servidor de aplicações SAP NetWeaver 

Algumas bases de dados requerem que a instalação da instância de base de dados seja executada num servidor de aplicações. Prepare as máquinas virtuais do servidor de aplicação para poder utilizá-las nestes casos.

Os passos assumem que instala o servidor de aplicação num servidor diferente dos servidores ASCS/SCS e HANA. Caso contrário, alguns dos passos abaixo (como configurar a resolução de nome sinuoso) não são necessários.

Os seguintes itens são pré-fixados com **[A]** - aplicável tanto ao PAS como ao AAS, **[P]** - apenas aplicáveis ao PAS ou **[S]** - apenas aplicáveis à AAS.


1. **[A]** Configurar o sistema operativo

   Reduza o tamanho da cache modificado. Para mais informações, consulte [o desempenho de Baixa escrita nos servidores SLES 11/12 com RAM grande](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Configuração resolução de nome de anfitrião

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

1. **[A]** Criar o diretório sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]** Criar o diretório PAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]** Criar o diretório AAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]** Configurar `autofs` em PAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Se utilizar o NFSv3, crie um novo ficheiro com:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/usrsap<b>qas</b>pas
   </code></pre>

   Se utilizar o NFSv4.1, crie um novo ficheiro com:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.5</b>:/usrsap<b>qas</b>pas
   </code></pre>

   Reiniciar `autofs` para montar as novas ações

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]** Configurar `autofs` na AAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Se utilizar o NFSv3, crie um novo ficheiro com:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   Se utilizar o NFSv4.1, crie um novo ficheiro com:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   Reiniciar `autofs` para montar as novas ações

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** Arquivo SWAP configurar

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

   Reiniciar o Agente para ativar a alteração

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalar base de dados

Neste exemplo, o SAP NetWeaver está instalado no SAP HANA. Pode utilizar todas as bases de dados suportadas para esta instalação. Para obter mais informações sobre como instalar o SAP HANA em Azure, consulte [A Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]. Para obter uma lista de bases de dados suportadas, consulte [SAP Nota 1928533][1928533].

* Executar a instalação de instância de base de dados SAP

   Instale a instância de base de dados SAP NetWeaver como raiz utilizando um nome de anfitrião virtual que mapeie para o endereço IP da configuração frontal do equilíbrio de carga para a base de dados.

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalação do servidor de aplicações SAP NetWeaver

Siga estes passos para instalar um servidor de aplicação SAP.

1. **[A]** Prepare o servidor de aplicações Siga os passos na preparação do servidor de [aplicações SAP NetWeaver](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) acima para preparar o servidor de aplicações.

2. **[A]** Instale o servidor de aplicações SAP NetWeaver Instale um servidor de aplicações SAP NetWeaver primário ou adicional.

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]** Loja segura Update SAP HANA

   Atualize a loja segura SAP HANA para apontar para o nome virtual da configuração de replicação do sistema SAP HANA.

   Executar o seguinte comando para listar as entradas
   <pre><code>
   hdbuserstore List
   </code></pre>

   Isto deve listar todas as entradas e deve ser semelhante a
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   A saída mostra que o endereço IP da entrada predefinida está a apontar para a máquina virtual e não para o endereço IP do equilibrador de carga. Esta entrada tem de ser alterada para indicar o nome de anfitrião virtual do equilibrador de carga. Certifique-se de utilizar a mesma porta **(30313** na saída acima) e nome de base de dados **(QAS** na saída acima)!

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Os seguintes ensaios são uma cópia dos casos de teste nos [guias de boas práticas da SUSE][suse-ha-guide]. São copiados para sua conveniência. Leia sempre também os guias de boas práticas e efetue todos os testes adicionais que possam ter sido adicionados.

1. Teste HAGetFailoverConfig, HACheckConfig e HACheckFailoverConfig

   Execute os seguintes comandos como \<sapsid>adm no nó onde a instância ASCS está atualmente em execução. Se os comandos falharem com FAIL: Memória insuficiente, pode ser causada por traços no seu nome de anfitrião. Esta é uma questão conhecida e será corrigida pela SUSE no pacote de conector de sáb-suse-cluster.

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

2. Migrar manualmente a instância ASCS

   Estado de recurso antes de iniciar o teste:

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

   Executar os seguintes comandos como raiz para migrar a instância ASCS.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado de recurso após o teste:

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

3. Teste HAFailoverToNode

   Estado de recurso antes de iniciar o teste:

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

   Executar os seguintes comandos como \<sapsid>adm para migrar a instância ASCS.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   # Remove migration constraints
   anftstsapcl1:~ # crm resource clear rsc_sap_QAS_ASCS00
   #INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   </code></pre>

   Estado de recurso após o teste:

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

4. Simular a queda do nó 

   Estado de recurso antes de iniciar o teste:

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

   Executar o seguinte comando como raiz no nó onde a instância ASCS está em execução

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Se utilizar o SBD, o Pacemaker não deve iniciar automaticamente o nó morto. O estado após o nó ser reiniciado deve ser assim.

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

   Use os seguintes comandos para iniciar o Pacemaker no nó morto, limpe as mensagens SBD e limpe os recursos falhados.

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

   Estado de recurso após o teste:

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

5. Reiniciar manual de teste da instância ASCS

   Estado de recurso antes de iniciar o teste:

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

   Crie um bloqueio de fila, por exemplo, editar um utilizador em transações su01. Execute os seguintes comandos como <sapsid\>adm no nó onde a instância ASCS está em execução. Os comandos pararão a instância ASCS e reiniciá-lo-ão. Se utilizar a arquitetura enqueue server 1, espera-se que o bloqueio de fila se perca neste teste. Se utilizar a arquitetura enqueue server 2, a fila será mantida. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   A instância ASCS deve agora ser desativada no Pacemaker

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Reinicie a instância ASCS no mesmo nó.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   O bloqueio de fila de transações su01 deve ser perdido, se a utilização da replicação do servidor de fila 1 e a parte de trás deveriam ter sido redefinidas. Estado de recurso após o teste:

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

6. Matar o processo do servidor de mensagens

   Estado de recurso antes de iniciar o teste:

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

   Executar os seguintes comandos como raiz para identificar o processo do servidor de mensagem e matá-lo.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Se só matar o servidor de mensagens uma vez, será reiniciado por `sapstart`. Se o matarcom frequência, o Pacemaker irá eventualmente mover a instância ASCS para o outro nó. Executar os seguintes comandos como raiz para limpar o estado de recurso da instância ASCS e ERS após o teste.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado de recurso após o teste:

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

7. Matar o processo do servidor enfila

   Estado de recurso antes de iniciar o teste:

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

   Executar os seguintes comandos como raiz no nó onde a instância ASCS está a correr para matar o servidor de fila.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   A instância ASCS deve falhar imediatamente no outro nó. A instância ERS também deve falhar depois de iniciado o caso ASCS. Executar os seguintes comandos como raiz para limpar o estado de recurso da instância ASCS e ERS após o teste.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado de recurso após o teste:

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

8. Matar processo de servidor de replicação de enfila

   Estado de recurso antes de iniciar o teste:

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

   Executar o seguinte comando como raiz no nó onde a instância ERS está a correr para matar o processo do servidor de replicação de fila.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Se só executar o comando uma vez, `sapstart` reiniciará o processo. Se o executar com frequência, `sapstart` não reiniciará o processo e o recurso estará num estado de paragem. Executar os seguintes comandos como raiz para limpar o estado de recurso da instância ERS após o teste.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado de recurso após o teste:

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

9. Kill enqueue sapstartsrv processo

   Estado de recurso antes de iniciar o teste:

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

   Executar os seguintes comandos como raiz no nó onde o ASCS está em execução.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   O processo sapstartsrv deve ser sempre reiniciado pelo agente de recursos Pacemaker. Estado de recurso após o teste:

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

## <a name="next-steps"></a>Passos seguintes

* [Planeamento e implementação de Máquinas Virtuais Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP][dbms-guide]
* Para aprender como estabelecer alta disponibilidade e plano para recuperação de desastres de SAP 
* HANA on Azure (grandes instâncias), veja [SAP HANA (grandes instâncias) alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md).
* Para aprender como estabelecer alta disponibilidade e plano para a recuperação de desastres de SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]
