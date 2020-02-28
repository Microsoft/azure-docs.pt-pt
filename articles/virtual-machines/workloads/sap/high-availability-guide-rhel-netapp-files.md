---
title: VMs Azure alta disponibilidade para SAP NW em RHEL com Ficheiros Azure NetApp! Microsoft Docs
description: Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.openlocfilehash: b58c24fdd7912b3e424a493932fe09b1a1f058c5
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661282"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux com Ficheiros Azure NetApp para aplicações SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533,]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[SAP 1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Este artigo descreve como implementar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um sistema SAP NetWeaver 7.50 altamente disponível, utilizando [ficheiros Azure NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)
Nas configurações de exemplo, comandos de instalação etc. A instância ASCS é o número 00, a instância DA é o número 01, a instância de aplicação primária (PAS) é 02 e a instância de aplicação (AAS) é 03. É utilizado o QAS de ID do sistema SAP. 

A camada de base de dados não está coberta de detalhes neste artigo.  

Leia primeiro as seguintes Notas e papéis SAP:

* [Documentação de Ficheiros Azure NetApp][anf-azure-doc] 
* Nota SAP [1928533,]que tem:
  * Lista de tamanhos De VM Azure que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade para tamanhos de VM Azure
  * Software SAP suportado e sistema operativo (OS) e combinações de bases de dados
  * Versão necessária do kernel SAP para Windows e Linux no Microsoft Azure

* O SAP Note [2015553] lista os pré-requisitos para implementações de software SAP suportadas pela SAP em Azure.
* SAP Note [2002167] recomendou definições de OS para Red Hat Enterprise Linux
* SAP Nota [2009879] tem Diretrizes SAP HANA para Red Hat Enterprise Linux
* O SAP Note [2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* O SAP Note [2191498] tem a versão necessária do Agente anfitrião SAP para o Linux em Azure.
* SAP Nota [2243692] tem informações sobre licenciamento SAP em Linux em Azure.
* A Nota [SAP 1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização avançada do Azure para sAP.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) exigiu todas as notas SAP para linux.
* [Planeamento e implementação de Máquinas Virtuais Azure para SAP em Linux][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP em Linux][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP em Linux][dbms-guide]
* [SAP Netweaver em cluster pacemaker](https://access.redhat.com/articles/3150081)
* Documentação Geral RHEL
  * [Visão geral de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração add-on de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência addon de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configurar ASCS/ERS para SAP Netweaver com recursos autónomos em RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configure SAP S/4HANA ASCS/ERS com O Servidor Enfila Autónomo 2 (ENSA2) em Pacemaker no RHEL](https://access.redhat.com/articles/3974941)
* Documentação RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade RHEL - Máquinas Virtuais Microsoft Azure como Membros do Cluster](https://access.redhat.com/articles/3131341)
  * [Instalação e Configuração de um Cluster de Alta Disponibilidade do Red Hat Enterprise Linux 7.4 (e mais tarde) no Microsoft Azure](https://access.redhat.com/articles/3252491)
* [Aplicações NetApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Descrição geral

A elevada disponibilidade (HA) para os serviços centrais SAP Netweaver requer armazenamento partilhado.
Para conseguir isso no Red Hat Linux até agora, era necessário construir um cluster glusterFS altamente disponível. 

Agora é possível alcançar o SAP Netweaver HA utilizando armazenamento partilhado, implantado em Ficheiros Azure NetApp. A utilização de Ficheiros Azure NetApp para o armazenamento partilhado elimina a necessidade de [um cluster glusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)adicional . O pacemaker ainda é necessário para o HA dos serviços centrais SAP Netweaver (ASCS/SCS).

![Visão geral de alta disponibilidade da SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e a base de dados SAP HANA usam o nome de anfitrião virtual e endereços IP virtuais. No Azure, é necessário utilizar um endereço IP virtual. Recomendamos a utilização do [equilíbrio de carga Standard](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). A lista seguinte mostra a configuração do equilibrador de carga com IPs frontais separados para (A)SCS e ERS.

### <a name="ascs"></a>A SCS

* Configuração frontend
  * Endereço IP 192.168.14.9
* Configuração de backend
  * Ligado às interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte do cluster (A)SCS/ERS
* Porta sonda
  * Porto 620<strong>&lt;nr&gt;</strong>
* Regras de equilíbrio de carga
  * Se utilizar o Balancer de Carga Padrão, selecione **portas HA**
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configuração frontend
  * Endereço IP 192.168.14.10
* Configuração de backend
  * Ligado às interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte do cluster (A)SCS/ERS
* Porta sonda
  * Porto 621<strong>&lt;nr&gt;</strong>
* Regras de equilíbrio de carga
  * Se utilizar o Balancer de Carga Padrão, selecione **portas HA**
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

5. Implemente volumes de Ficheiros Azure NetApp, seguindo as [instruções para criar um volume para ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Implementar os volumes na [sub-rede](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)de Ficheiros Azure NetApp designados . Tenha em mente que os recursos do Azure NetApp Files e os VMs Azure devem estar na mesma Rede Virtual Azure ou em redes virtuais azure peered. Neste exemplo utilizamos dois volumes de Ficheiros Azure NetApp: seiva<b>QAS</b> e transSAP. Os caminhos de arquivo que são montados nos pontos de montagem correspondentes são /usrsap<b>qas</b>/sapmnt<b>QAS,</b>/usrsap<b>qas</b>/usrsap<b>QAS</b>sys, etc.  

   1. volume seiva<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b><b>/sapmnt QAS</b>)
   2. volume seiva<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. volume seiva<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. volume seiva<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. volume transSAP (nfs://192.168.24.4/transSAP)
   6. volume seiva<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. volume seiva<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
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

## <a name="setting-up-ascs"></a>Configuração (A)SCS

Neste exemplo, os recursos foram implantados manualmente através do [portal Azure.](https://portal.azure.com/#home)

### <a name="deploy-linux-manually-via-azure-portal"></a>Implementar o Linux manualmente através do portal Azure

Primeiro, é necessário criar os volumes de Ficheiros Azure NetApp. Implante os VMs. Depois, cria-se um equilibrador de carga e utiliza as máquinas virtuais nas piscinas de backend.

1. Criar um equilibrador de carga (interno, standard):  
   1. Crie os endereços IP frontend
      1. Endereço IP 192.168.14.9 para o ASCS
         1. Abra o equilibrador de carga, selecione pool IP frontend e clique em Adicionar
         1. Introduza o nome da nova piscina IP frontend (por exemplo **frontend. QAS. ASCS)**
         1. Definir a Atribuição à Estática e introduzir o endereço IP (por exemplo **192.168.14.9**)
         1. Clique em OK
      1. Endereço IP 192.168.14.10 para a ASCS ERS
         * Repita os passos acima em "a" para criar um endereço IP para a ERS (por exemplo **192.168.14.10** e **frontend. QAS. ERS**)
   1. Crie as piscinas de backend
      1. Crie uma piscina de backend para o ASCS
         1. Abra o equilibrador de carga, selecione piscinas de backend e clique em Adicionar
         1. Introduza o nome da nova piscina de backend (por **exemplo, backend. QAS)**
         1. Clique em Adicionar uma máquina virtual.
         1. Selecione máquina Virtual. 
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
      1. Regras de equilíbrio de carga para asCS
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
      1. Endereço IP 192.168.14.9 para o ASCS
         1. Abra o equilibrador de carga, selecione pool IP frontend e clique em Adicionar
         1. Introduza o nome da nova piscina IP frontend (por exemplo **frontend. QAS. ASCS)**
         1. Definir a Atribuição à Estática e introduzir o endereço IP (por exemplo **192.168.14.9**)
         1. Clique em OK
      1. Endereço IP 192.168.14.10 para a ASCS ERS
         * Repita os passos acima em "a" para criar um endereço IP para a ERS (por exemplo **192.168.14.10** e **frontend. QAS. ERS**)
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
         * Repita os passos acima em "d" para os portos 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 e TCP para a ASCS ERS

      > [!Note]
      > Quando os VMs sem endereços IP públicos forem colocados no conjunto de backend do interno (sem endereço IP público) O equilíbrio de carga Standard Azure não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos. Para mais detalhes sobre como alcançar a conectividade de saída, consulte [a conectividade do ponto final público para máquinas virtuais utilizando o Equilíbrio de Carga Padrão Azure em cenários de alta disponibilidade SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

      > [!IMPORTANT]
      > Não permita os carimbos de tempo da TCP em VMs Azure colocados atrás do Equilíbrio de Carga Azure. Permitir os selos temporais da TCP fará com que as sondas de saúde falhem. Definir parâmetro **net.ipv4.tcp_timestamps** a **0**. Para mais detalhes consulte as sondas de [saúde load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

## <a name="disable-id-mapping-if-using-nfsv41"></a>Desativar o mapeamento de ID (se utilizar NFSv4.1)

As instruções nesta secção só são aplicáveis, se utilizarem volumes de Ficheiros Azure NetApp com protocolo NFSv4.1. Execute a configuração em todos os VMs, onde serão montados volumes NFSv4.1 do Azure NetApp.  

1. Verifique a definição de domínio NFS. Certifique-se de que o domínio está configurado como o domínio padrão dos Ficheiros Azure NetApp, ou seja, **`defaultv4iddomain.com`** e o mapeamento não está definido para **ninguém**.  

    > [!IMPORTANT]
    > Certifique-se de que define o domínio NFS em `/etc/idmapd.conf` no VM para corresponder à configuração de domínio predefinido nos Ficheiros Azure NetApp: **`defaultv4iddomain.com`** . Se houver uma incompatibilidade entre a configuração de domínio no cliente NFS (isto é, o VM) e o servidor NFS, ou seja, a configuração Azure NetApp, então as permissões para ficheiros nos volumes Azure NetApp que são montados nos VMs serão apresentadas como `nobody`.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
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
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Para obter mais detalhes sobre como alterar `nfs4_disable_idmapping` parâmetro ver https://access.redhat.com/solutions/1749883.

### <a name="create-pacemaker-cluster"></a>Criar cluster Pacemaker

Siga os passos na configuração do [Pacemaker no Red Hat Enterprise Linux em Azure](high-availability-guide-rhel-pacemaker.md) para criar um cluster pacemaker básico para este servidor (A)SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Prepare-se para a instalação SAP NetWeaver

Os seguintes itens são pré-fixados com **[A]** - aplicável a todos os nós, **[1]** - apenas aplicável ao nó 1 ou **[2]** - apenas aplicável ao nó 2.

1. **[A]** Configuração resolução de nome de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos

    ```
    sudo vi /etc/hosts
    ```

   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e o nome de anfitrião para corresponder ao seu ambiente

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** Criar diretórios SAP no volume de Ficheiros Azure NetApp.  
   Monte temporariamente o volume de Ficheiros Azure NetApp num dos VMs e crie os diretórios SAP (caminhos de ficheiros).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp
    ``` 

1. **[A]** Criar os diretórios partilhados

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** Instalar cliente NFS e outros requisitos

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Ver versão dos agentes de recursos-seiva

   Certifique-se de que a versão do pacote instalado de agentes de recursos-seiva é de, pelo menos, 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** Adicionar entradas de montagem

   Se utilizar o NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Se utilizar nFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Certifique-se de que corresponde à versão protocolar NFS dos volumes de Ficheiros Azure NetApp, ao montar os volumes. Se os volumes de Ficheiros Azure NetApp forem criados como volumes NFSv3, utilize a configuração NFSv3 correspondente. Se os volumes de Ficheiros Azure NetApp forem criados como volumes NFSv4.1, siga as instruções para desativar o mapeamento de ID e certifique-se de utilizar a configuração NFSv4.1 correspondente. Neste exemplo, os volumes de Ficheiros Azure NetApp foram criados como volumes NFSv3.  

   Monte as novas ações

   ```
   sudo mount -a  
   ```

1. **[A]** Arquivo SWAP configurar

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Reiniciar o Agente para ativar a alteração

   ```
   sudo service waagent restart
   ```

1. **[A]** Configuração RHEL

   Configure RHEL conforme descrito na Nota SAP [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalação SAP NetWeaver ASCS/ERS

1. **[1]** Criar um recurso IP virtual e uma sonda de saúde para a instância ASCS

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante em que nó dos recursos estão a funcionar.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** Instalar asCS SAP NetWeaver  

   Instale o SAP NetWeaver ASCS como raiz no primeiro nó utilizando um nome de anfitrião virtual que mapeie para o endereço IP da configuração frontal do equilíbrio de carga para o ASCS, por exemplo, <b>anftstsapvh</b>, <b>192.168.14.9</b> e o número de instância que utilizou para a sonda do equilibrista de carga, por exemplo <b>00</b>.

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Se a instalação não criar uma subpasta em /usr/seap/**QAS**/ASCS**00,** tente configurar o proprietário e o grupo da pasta ASCS**00** e voltar a tentar.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Criar um recurso IP virtual e uma sonda de saúde para a instância DA

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante em que nó dos recursos estão a funcionar.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** Instalar SAP NetWeaver ERS  

   Instale o SAP NetWeaver ERS como raiz no segundo nó utilizando um nome de anfitrião virtual que mapeie para o endereço IP da configuração frontal do equilíbrio de carga para o ERS, por exemplo <b>anftstsapersapers</b>, <b>192.168.14.10</b> e o número de instância que utilizou para a sonda do equilibrista de carga, por exemplo <b>01</b>.

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Se a instalação não criar uma subpasta em /usr/seap/**QAS/ERS** **01**, tente configurar o proprietário e o grupo da pasta ERS**01** e voltar a tentar.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adaptar os perfis de instância ASCS/SCS e ERS

   * Perfil ASCS/SCS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * Perfil ERS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** Configure Manter Vivo

   A comunicação entre o servidor de aplicação SAP NetWeaver e o ASCS/SCS é encaminhada através de um equilíbriode carga de software. O equilibrador de carga desliga ligações inativas após um tempo de tempo configurável. Para evitar isto, é necessário definir um parâmetro no perfil SAP NetWeaver ASCS/SCS e alterar as definições do sistema Linux. Leia [a Nota SAP 1410736][1410736] para mais informações.

   O parâmetro de perfil ASCS/SCS enque/ncni/set_so_keepalive já foi adicionado no último passo.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Atualizar o ficheiro /usr/sap/sapservices

   Para evitar o início das ocorrências pelo script de arranque sapinit, todas as instâncias geridas pela Pacemaker devem ser comentadas a partir do ficheiro /usr/sap/sapservices. Não comente a instância SAP HANA se será utilizada com a HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Criar os recursos de cluster SAP

   Se utilizar a arquitetura enqueue server 1 (ENSA1), defina os recursos da seguinte forma:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   O SAP introduziu suporte para o servidor de fila 2, incluindo replicação, a partir de SAP NW 7.52. A partir da Plataforma ABAP 1809, o servidor enfila 2 é instalado por padrão. Consulte a nota SAP [2630416](https://launchpad.support.sap.com/#/notes/2630416) para obter suporte ao servidor 2.
   Se utilizar a arquitetura enqueue server 2[(ENSA2),](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)instale o agente de recursos-agentes-agentes-sap-4.1.1-12.el7.x86_64 ou mais recentes e defina os recursos da seguinte forma:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Se estiver a atualizar a partir de uma versão mais antiga e a mudar para o servidor de fila 2, consulte a nota [SAP 2641322 .](https://launchpad.support.sap.com/#/notes/2641322) 

   > [!NOTE]
   > Os intervalos na configuração acima são apenas exemplos e podem ter de ser adaptados à configuração específica do SAP. 

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante em que nó dos recursos estão a funcionar.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** Adicione regras de firewall para ASCS e ERS em ambos os nós Adicione as regras de firewall para ASCS e ERS em ambos os nós.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparação do servidor de aplicações SAP NetWeaver

   Algumas bases de dados requerem que a instalação da instância de base de dados seja executada num servidor de aplicações. Prepare as máquinas virtuais do servidor de aplicação para poder utilizá-las nestes casos.  

   Os passos assumem que instala o servidor de aplicação num servidor diferente dos servidores ASCS/SCS e HANA. Caso contrário, alguns dos passos abaixo (como configurar a resolução de nome sinuoso) não são necessários.  

   Os seguintes itens são pré-fixados com **[A]** - aplicável tanto ao PAS como ao AAS, **[P]** - apenas aplicáveis ao PAS ou **[S]** - apenas aplicáveis à AAS.  

1. **[A]** Configurar resolução de nome de anfitrião Pode utilizar um servidor DNS ou modificar os /etc/anfitriões em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos:  

   ```
   sudo vi /etc/hosts
   ```

   Insira as seguintes linhas ao /etc/hosts. Altere o endereço IP e o nome de anfitrião para combinar com o seu ambiente.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** Criar o diretório sapmnt Criar o diretório sapmnt.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** Instalar cliente NFS e outros requisitos  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Adicionar entradas de montagem  
   Se utilizar o NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Se utilizar nFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Monte as novas ações

   ```
   sudo mount -a
   ```

1. **[P]** Criar e montar o diretório PAS  
   Se utilizar o NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Se utilizar nFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** Criar e montar o diretório AAS  
   Se utilizar o NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Se utilizar nFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** Arquivo SWAP configurar
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Reiniciar o Agente para ativar a alteração

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Instalar base de dados

Neste exemplo, o SAP NetWeaver está instalado no SAP HANA. Pode utilizar todas as bases de dados suportadas para esta instalação. Para obter mais informações sobre como instalar o SAP HANA em Azure, consulte [A Alta Disponibilidade de SAP HANA em VMs Azure em Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Executar a instalação de instância de base de dados SAP

   Instale a instância de base de dados SAP NetWeaver como raiz utilizando um nome de anfitrião virtual que mapeie para o endereço IP da configuração frontal do equilíbrio de carga para a base de dados.

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Instalação do servidor de aplicações SAP NetWeaver

Siga estes passos para instalar um servidor de aplicação SAP.

1. Preparar servidor de aplicações

   Siga os passos na preparação do servidor de [aplicações SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) acima para preparar o servidor de aplicação.

1. Instalar servidor de aplicações SAP NetWeaver

   Instale um servidor de aplicações SAP NetWeaver primário ou adicional.

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Atualizar loja segura SAP HANA

   Atualize a loja segura SAP HANA para apontar para o nome virtual da configuração de replicação do sistema SAP HANA.

   Executar o seguinte comando para listar as entradas como \<sapsid>adm

   ```
   hdbuserstore List
   ```

   Isto deve listar todas as entradas e deve ser semelhante a
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   A saída mostra que o endereço IP da entrada predefinida está a apontar para a máquina virtual e não para o endereço IP do equilibrador de carga. Esta entrada tem de ser alterada para indicar o nome de anfitrião virtual do equilibrador de carga. Certifique-se de utilizar a mesma porta **(30313** na saída acima) e nome de base de dados **(QAS** na saída acima)!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

1. Migrar manualmente a instância ASCS

   Estado de recurso antes de iniciar o teste:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Executar os seguintes comandos como raiz para migrar a instância ASCS.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado de recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Simular a queda do nó

   Estado de recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Executar o seguinte comando como raiz no nó onde a instância ASCS está em execução

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   O estado após o nó ser reiniciado deve ser assim.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Utilize o seguinte comando para limpar os recursos falhados.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado de recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Matar o processo do servidor de mensagens

   Estado de recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Executar os seguintes comandos como raiz para identificar o processo do servidor de mensagem e matá-lo.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   Se só matar o servidor de mensagens uma vez, será reiniciado por `sapstart`. Se o matarcom frequência, o Pacemaker irá eventualmente mover a instância ASCS para o outro nó. Executar os seguintes comandos como raiz para limpar o estado de recurso da instância ASCS e ERS após o teste.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado de recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Matar o processo do servidor enfila

   Estado de recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Executar os seguintes comandos como raiz no nó onde a instância ASCS está a correr para matar o servidor de fila.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   A instância ASCS deve falhar imediatamente no outro nó. A instância ERS também deve falhar depois de iniciado o caso ASCS. Executar os seguintes comandos como raiz para limpar o estado de recurso da instância ASCS e ERS após o teste.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado de recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Matar processo de servidor de replicação de enfila

   Estado de recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Executar o seguinte comando como raiz no nó onde a instância ERS está a correr para matar o processo do servidor de replicação de fila.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Se só executar o comando uma vez, `sapstart` reiniciará o processo. Se o executar com frequência, `sapstart` não reiniciará o processo e o recurso estará num estado de paragem. Executar os seguintes comandos como raiz para limpar o estado de recurso da instância ERS após o teste.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado de recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Kill enqueue sapstartsrv processo

   Estado de recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Executar os seguintes comandos como raiz no nó onde o ASCS está em execução.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   O processo sapstartsrv deve ser sempre reiniciado pelo agente de recursos Pacemaker como parte da monitorização. Estado de recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Passos seguintes

* [HA para SAP NW em VMs Azure em RHEL para aplicações SAP guia multi-SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [Planeamento e implementação de Máquinas Virtuais Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP][dbms-guide]
* Para aprender a estabelecer alta disponibilidade e plano para a recuperação de desastres do SAP HANA em Azure (grandes instâncias), consulte a alta disponibilidade do [SAP HANA (grandes instâncias) e a recuperação de desastres em Azure.](hana-overview-high-availability-disaster-recovery.md)
* Para aprender como estabelecer alta disponibilidade e plano para a recuperação de desastres de SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]
