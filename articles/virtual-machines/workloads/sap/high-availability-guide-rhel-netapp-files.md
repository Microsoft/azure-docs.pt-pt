---
title: Azure VMs alta disponibilidade para SAP NW na RHEL com ficheiros Azure NetApp Microsoft Docs
description: Estabeleça uma elevada disponibilidade para SAP NW em máquinas virtuais Azure (VMs) RHEL com ficheiros Azure NetApp.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/22/2020
ms.author: radeltch
ms.openlocfilehash: 0c5ebd3f7989458a0966fdc792cd3a8a9ea94acc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965277"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Azure Virtual Machines alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux com Ficheiros Azure NetApp para aplicações SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura do cluster e instalar um sistema SAP NetWeaver 7.50 altamente disponível, utilizando [ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-introduction.md).
Nas configurações de exemplo, comandos de instalação, etc. A instância ASCS é o número 00, a instância ERS é o número 01, a instância de aplicação primária (PAS) é 02 e a instância de aplicação (AAS) é 03. O SISTEMA SAP ID QAS é utilizado. 

A camada de base de dados não está coberta em detalhe neste artigo.  

Leia primeiro as seguintes notas e artigos SAP:

* [Documentação dos Ficheiros Azure NetApp][anf-azure-doc] 
* Nota SAP [1928533,]que tem:
  * Lista de tamanhos Azure VM que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade dos tamanhos Azure VM
  * Combinações suportadas de software SAP e sistema operativo (OS) e de base de dados
  * Versão kernel SAP necessária para Windows e Linux no Microsoft Azure

* O SAP Note [2015553] lista pré-requisitos para implementações de software SAP suportadas pelo SAP em Azure.
* SAP Note [2002167] recomendou definições de SO para Red Hat Enterprise Linux
* SAP Note [2009879] tem orientações SAP HANA para Red Hat Enterprise Linux
* A Nota [SAP 2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* Sap Note [2191498] tem a versão necessária do Agente anfitrião SAP para Linux em Azure.
* Sap Nota [2243692] tem informações sobre o licenciamento SAP em Linux em Azure.
* Sap Nota [1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização melhorada de Azure para o SAP.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [Azure Virtual Machines planejamento e implementação para SAP em Linux][planning-guide]
* [Implementação de Máquinas Virtuais Azure para SAP em Linux][deployment-guide]
* [Azure Virtual Machines DBMS implantação para SAP em Linux][dbms-guide]
* [SAP Netweaver em cluster pacemaker](https://access.redhat.com/articles/3150081)
* Documentação geral do RHEL
  * [Visão geral Add-On de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Alta Disponibilidade Add-On Administração](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de Add-On de Alta Disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configuração ASCS/ERS para SAP Netweaver com recursos autónomos em RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configure SAP S/4HANA ASCS/ERS com Standalone Enqueue Server 2 (ENSA2) em Pacemaker em RHEL ](https://access.redhat.com/articles/3974941)
* Documentação RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade RHEL - Microsoft Azure Virtual Machines como Membros do Cluster](https://access.redhat.com/articles/3131341)
  * [Instalar e Configurar um Red Hat Enterprise Linux 7.4 (e mais tarde) High-Availability Cluster no Microsoft Azure](https://access.redhat.com/articles/3252491)
* [Aplicações NETApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Descrição geral

A elevada disponibilidade (HA) para os serviços centrais DA NETWEaver requer armazenamento partilhado.
Para conseguir isso no Red Hat Linux até agora era necessário construir um cluster glusterfs altamente disponível. 

Agora é possível alcançar o SAP Netweaver HA utilizando o armazenamento partilhado, implantado em Ficheiros Azure NetApp. A utilização de ficheiros Azure NetApp para o armazenamento partilhado elimina a necessidade de [um cluster adicional de GlusterFS](./high-availability-guide-rhel-glusterfs.md). O Pacemaker ainda é necessário para a HA dos serviços centrais SAP Netweaver (ASCS/SCS).

![Visão geral de alta disponibilidade do SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e a base de dados SAP HANA utilizam o nome de anfitrião virtual e endereços IP virtuais. No Azure, é necessário um equilibrador de carga para utilizar um endereço IP virtual. Recomendamos a utilização [do balanceador de carga standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). A lista a seguir mostra a configuração do equilibrador de carga com IPs frontais separados para (A)SCS e ERS.

### <a name="ascs"></a>a (A) SCS

* Configuração frontend
  * Endereço IP 192.168.14.9
* Porto de Sonda
  * Porto 620<strong> &lt; nr &gt; </strong>
* Regras de equilíbrio de carga
  * Se utilizar o Balanceador de Carga Padrão, selecione **portas HA**
  * 32<strong> &lt; nr &gt; </strong> TCP
  * 36<strong> &lt; nr &gt; </strong> TCP
  * 39<strong> &lt; nr &gt; </strong> TCP
  * 81<strong> &lt; nr &gt; </strong> TCP
  * 5<strong> &lt; nr &gt; </strong>13 TCP
  * 5<strong> &lt; nr &gt; </strong>14 TCP
  * 5<strong> &lt; nr &gt; </strong>16 TCP

### <a name="ers"></a>ERS

* Configuração frontend
  * Endereço IP 192.168.14.10
* Porto de Sonda
  * Porto 621<strong> &lt; nr &gt; </strong>
* Regras de equilíbrio de carga
  * Se utilizar o Balanceador de Carga Padrão, selecione **portas HA**
  * 32<strong> &lt; nr &gt; </strong> TCP
  * 33<strong> &lt; nr &gt; </strong> TCP
  * 5<strong> &lt; nr &gt; </strong>13 TCP
  * 5<strong> &lt; nr &gt; </strong>14 TCP
  * 5<strong> &lt; nr &gt; </strong>16 TCP

* Configuração de backend
  * Ligado às interfaces de rede primária de todas as máquinas virtuais que devem fazer parte do cluster (A)SCS/ERS

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Criação da infraestrutura Azure NetApp Files 

O SAP NetWeaver requer armazenamento partilhado para o diretório de transporte e perfil.  Antes de prosseguir com a configuração da infraestrutura de ficheiros Azure NetApp, familiarize-se com a documentação do [Azure NetApp Files][anf-azure-doc]. Verifique se a região de Azure selecionada oferece ficheiros Azure NetApp. O seguinte link mostra a disponibilidade de Ficheiros Azure NetApp por região Azure: [Azure NetApp Files Availability by Azure Region][anf-avail-matrix].

Os ficheiros Azure NetApp estão disponíveis em [várias regiões do Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Antes de implementar ficheiros Azure NetApp, solicite a bordo para ficheiros Azure NetApp, seguindo as instruções dos [ficheiros Register for Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Implementar recursos de ficheiros Azure NetApp  

Os passos assumem que já implementou [a Rede Virtual Azure.](../../../virtual-network/virtual-networks-overview.md) Os recursos do Azure NetApp e dos VMs, onde serão montados os recursos do Azure NetApp Files, devem ser implantados na mesma Rede Virtual Azure ou em Redes Virtuais Azure.  

1. Se ainda não o fez, [solicite a bordo dos Ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-register.md).  
2. Crie a conta NetApp na região Azure selecionada, seguindo as [instruções para criar a Conta NetApp.](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)  
3. Configurar o pool de capacidade do Azure NetApp Files, seguindo as [instruções sobre como configurar o pool de capacidade do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  
A arquitetura SAP Netweaver apresentada neste artigo utiliza um único pool de capacidade Azure NetApp Files, Premium SKU. Recomendamos a Azure NetApp Files Premium SKU para a carga de trabalho da aplicação SAP Netweaver no Azure.  
4. Delege uma sub-rede para ficheiros Azure NetApp, conforme descrito nas [instruções Delegar uma sub-rede para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Implementar volumes de ficheiros Azure NetApp, seguindo as [instruções para criar um volume para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Implementar os volumes na [sub-rede](/rest/api/virtualnetwork/subnets)designada Azure NetApp Files . Os endereços IP dos volumes Azure NetApp são atribuídos automaticamente. Tenha em mente que os recursos do Azure NetApp Files e os VMs Azure devem estar na mesma Rede Virtual Azure ou em Redes Virtuais Azure. Neste exemplo utilizamos dois volumes Azure NetApp Files: sap<b>QAS</b> e transSAP. Os caminhos de arquivo que são montados nos pontos de montagem correspondentes são /usrsap<b>qas</b>/sapmnt<b>QAS,</b>/usrsap<b>qas</b>/usrsap<b>QAS</b>sys, etc.  

   1. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. volume transSAP (nfs://192.168.24.4/transSAP)
   6. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
Neste exemplo, utilizamos ficheiros Azure NetApp para todos os sistemas de ficheiros SAP Netweaver para demonstrar como os Ficheiros Azure NetApp podem ser utilizados. Os sistemas de ficheiros SAP que não precisam de ser montados através de NFS também podem ser implantados como [armazenamento de disco Azure](../../disks-types.md#premium-ssd) . Neste <b>exemplo, o A-E</b> deve estar nos Ficheiros Azure NetApp e <b>f-g</b> (isto é, /usr/seiva/<b>QAS</b>/D<b>02</b>, /usr/sap/<b>QAS</b>/D<b>03</b>) pode ser implantado como armazenamento de disco Azure. 

### <a name="important-considerations"></a>Considerações importantes

Ao considerar os ficheiros Azure NetApp para o SAP Netweaver na arquitetura SUSE High Availability, esteja ciente das seguintes considerações importantes:

- A capacidade mínima é de 4 TiB. O tamanho da piscina pode ser aumentado em 1 incrementos TiB.
- O volume mínimo é de 100 GiB
- Os ficheiros Azure NetApp e todas as máquinas virtuais, onde os volumes dos Ficheiros Azure NetApp serão montados, devem estar na mesma Rede Virtual Azure ou em [redes virtuais na](../../../virtual-network/virtual-network-peering-overview.md) mesma região. O acesso dos Ficheiros Azure NetApp sobre o observante VNET na mesma região é agora suportado. O acesso do Azure NetApp ao que está a ser olhando global ainda não está suportado.
- A rede virtual selecionada deve ter uma sub-rede delegada nos Ficheiros Azure NetApp.
- O Azure NetApp Files oferece [uma política de exportação:](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)pode controlar os clientes autorizados, o tipo de acesso (Ler&Escrever, Ler Apenas, etc.). 
- A funcionalidade Azure NetApp Files ainda não está ciente da zona. Atualmente, a funcionalidade Azure NetApp Files não está implantada em todas as zonas de Disponibilidade numa região do Azure. Esteja ciente das potenciais implicações de latência em algumas regiões de Azure. 
- Os volumes de Ficheiros Azure NetApp podem ser implantados como volumes NFSv3 ou NFSv4.1. Ambos os protocolos são suportados para a camada de aplicação SAP (ASCS/ERS, servidores de aplicações SAP). 

## <a name="setting-up-ascs"></a>Criação (A)SCS

Neste exemplo, os recursos foram utilizados manualmente através do [portal Azure.](https://portal.azure.com/#home)

### <a name="deploy-linux-manually-via-azure-portal"></a>Implementar o Linux manualmente através do portal Azure

Primeiro, tem de criar os volumes dos Ficheiros Azure NetApp. Desdobre os VMs. Em seguida, cria-se um equilibrador de carga e utiliza-se as máquinas virtuais na piscina de backend.

1. Criar balanceador de carga (interno, padrão):  
   1. Criar os endereços IP frontend
      1. Endereço IP 192.168.14.9 para o ASCS
         1. Abra o equilibrador de carga, selecione o pool IP frontal e clique em Adicionar
         1. Insira o nome do novo pool IP frontend (por **exemplo, frontend. O QAS. ASCS**)
         1. Desaponda a Estática e introduza o endereço IP (por **exemplo, 192.168.14.9**)
         1. Clique em OK
      1. Endereço IP 192.168.14.10 para o ASCS ERS
         * Repita os passos acima em "a" para criar um endereço IP para o ERS (por exemplo **192.168.14.10** e **frontend. O QAS. ERS**)
   1. Criar o conjunto de back-end
      1. Abra o balançador de carga, selecione piscinas de backend e clique em Adicionar
      1. Insira o nome da nova piscina de backend (por **exemplo, backend. QAS**
      1. Clique em Adicionar uma máquina virtual.
      1. Selecione Máquina virtual. 
      1. Selecione as máquinas virtuais do cluster (A)SCS e os seus endereços IP.
      1. Clique em Adicionar
   1. Criar as sondas de saúde
      1. Porto 620 **00** para ASCS
         1. Abra o equilibrador de carga, selecione sondas de saúde e clique em Adicionar
         1. Insira o nome da nova sonda de saúde (por **exemplo, saúde. O QAS. ASCS**)
         1. Selecione TCP como protocolo, porta 620 **00**, mantenha o Intervalo 5 e o limiar insalubre 2
         1. Clique em OK
      1. Porto 621 **01** para ASCS ERS
            * Repita os passos acima em "c" para criar uma sonda de saúde para o ERS (por exemplo 621 **01** e **saúde. O QAS. ERS**)
   1. Regras de equilíbrio de carga
      1. Regras de equilíbrio de carga para asCS
         1. Abra o equilibrador de carga, selecione regras de equilíbrio de carga e clique em Adicionar
         1. Insira o nome da nova regra do balançador de carga (por exemplo **lb. O QAS. ASCS**)
         1. Selecione o endereço IP frontend para ASCS, backend pool e sonda de saúde que criou anteriormente (por **exemplo, frontend. O QAS. ASCS,** **backend. QAS** e **saúde. O QAS. ASCS**)
         1. Selecione **portas HA**
         1. Aumente o tempo de inatividade para 30 minutos
         1. **Certifique-se de ativar o IP flutuante**
         1. Clique em OK
         * Repita os passos acima para criar regras de equilíbrio de carga para ERS (por exemplo **lb. O QAS. ERS**)
1. Em alternativa, se o seu cenário necessitar de um balanceador de carga básico (interno), siga estes passos:  
   1. Criar os endereços IP frontend
      1. Endereço IP 192.168.14.9 para o ASCS
         1. Abra o equilibrador de carga, selecione o pool IP frontal e clique em Adicionar
         1. Insira o nome do novo pool IP frontend (por **exemplo, frontend. O QAS. ASCS**)
         1. Desaponda a Estática e introduza o endereço IP (por **exemplo, 192.168.14.9**)
         1. Clique em OK
      1. Endereço IP 192.168.14.10 para o ASCS ERS
         * Repita os passos acima em "a" para criar um endereço IP para o ERS (por exemplo **192.168.14.10** e **frontend. O QAS. ERS**)
   1. Criar o conjunto de back-end
      1. Abra o balançador de carga, selecione piscinas de backend e clique em Adicionar
      1. Insira o nome da nova piscina de backend (por **exemplo, backend. QAS**
      1. Clique em Adicionar uma máquina virtual.
      1. Selecione o Conjunto de Disponibilidade que criou anteriormente para ASCS 
      1. Selecione as máquinas virtuais do cluster (A)SCS
      1. Clique em OK
   1. Criar as sondas de saúde
      1. Porto 620 **00** para ASCS
         1. Abra o equilibrador de carga, selecione sondas de saúde e clique em Adicionar
         1. Insira o nome da nova sonda de saúde (por **exemplo, saúde. O QAS. ASCS**)
         1. Selecione TCP como protocolo, porta 620 **00**, mantenha o Intervalo 5 e o limiar insalubre 2
         1. Clique em OK
      1. Porto 621 **01** para ASCS ERS
            * Repita os passos acima em "c" para criar uma sonda de saúde para o ERS (por exemplo 621 **01** e **saúde. O QAS. ERS**)
   1. Regras de equilíbrio de carga
      1. 32 **00** TCP para ASCS
         1. Abra o equilibrador de carga, selecione regras de equilíbrio de carga e clique em Adicionar
         1. Insira o nome da nova regra do balançador de carga (por exemplo **lb. O QAS. ASCS.3200**)
         1. Selecione o endereço IP frontend para ASCS, backend pool e sonda de saúde que criou anteriormente (por **exemplo, frontend. O QAS. ASCS**)
         1. Manter o protocolo **TCP,** inserir porta **3200**
         1. Aumente o tempo de inatividade para 30 minutos
         1. **Certifique-se de ativar o IP flutuante**
         1. Clique em OK
      1. Portas adicionais para o ASCS
         * Repita os passos acima em "d" para as portas 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14,**5 00** 16 e TCP para o ASCS
      1. Portos adicionais para o ASCS ERS
         * Repita os passos acima em "d" para as portas 32 **01**, 33 **01**, 5 **01** 13, 5 **01** 14, 5 **01** 16 e TCP para o ASCS ERS

      > [!IMPORTANT]
      > O IP flutuante não é suportado numa configuração IP secundária do NIC em cenários de equilíbrio de carga. Para mais detalhes consulte [as limitações do balançador de carga Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations). Se precisar de um endereço IP adicional para o VM, implante um segundo NIC.  

      > [!Note]
      > Quando os VMs sem endereços IP públicos forem colocados no pool de backend de saldos de carga standard Azure (sem endereço IP público), não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos. Para obter detalhes sobre como alcançar a conectividade de saída, consulte [a conectividade do ponto final público para máquinas virtuais utilizando o Azure Standard Load Balancer em cenários de alta disponibilidade SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

      > [!IMPORTANT]
      > Não ative os cartas temporais TCP em VMs Azure colocados atrás do Balançador de Carga Azure. Permitir os tempos de TCP fará com que as sondas de saúde falhem. Definir o parâmetro **net.ipv4.tcp_timestamps** a **0**. Para mais detalhes consulte [as sondas de saúde load balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="disable-id-mapping-if-using-nfsv41"></a>Desativar o mapeamento de ID (se utilizar o NFSv4.1)

As instruções nesta secção só são aplicáveis se utilizar volumes de Ficheiros Azure NetApp com o protocolo NFSv4.1. Execute a configuração em todos os VMs, onde os volumes NFSv4.1 dos Ficheiros Azure NetApp serão montados.  

1. Verifique a definição de domínio NFS. Certifique-se de que o domínio está configurado como o domínio padrão dos Ficheiros Azure NetApp, ou seja, **`defaultv4iddomain.com`** e o mapeamento não é definido para **ninguém**.  

    > [!IMPORTANT]
    > Certifique-se de que define o domínio NFS `/etc/idmapd.conf` no VM para corresponder à configuração de domínio predefinido nos Ficheiros Azure NetApp: **`defaultv4iddomain.com`** . Se houver um desfasamento entre a configuração de domínio no cliente NFS (ou seja, o VM) e o servidor NFS, ou seja, a configuração do Azure NetApp, então as permissões para ficheiros nos volumes Azure NetApp que são montados nos VMs serão exibidas como `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** Verificar `nfs4_disable_idmapping` . Deve ser definido para **Y.** Para criar a estrutura do diretório onde `nfs4_disable_idmapping` se encontra, execute o comando de montagem. Não poderá criar manualmente o diretório em /sys/módulos, porque o acesso é reservado para o núcleo/condutores.  

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

   Para obter mais detalhes sobre como alterar `nfs4_disable_idmapping` o parâmetro consulte https://access.redhat.com/solutions/1749883 .

### <a name="create-pacemaker-cluster"></a>Criar cluster pacemaker

Siga os passos na configuração do [Pacemaker no Red Hat Enterprise Linux em Azure](high-availability-guide-rhel-pacemaker.md) para criar um cluster pacemaker básico para este (A)SCS servidor.

### <a name="prepare-for-sap-netweaver-installation"></a>Preparar para a instalação SAP NetWeaver

Os seguintes itens são prefixados com **ambos [A]** - aplicável a todos os nós, **[1]** - apenas aplicável ao nó 1 ou **[2]** - apenas aplicáveis ao nó 2.

1. **[A]** Resolução do nome do anfitrião da configuração

   Pode utilizar um servidor DNS ou modificar os /etc/anfitriões em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos

    ```
    sudo vi /etc/hosts
    ```

   Insira as seguintes linhas para /etc/anfitriões. Altere o endereço IP e o nome de anfitrião para combinar com o seu ambiente

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

1. **[1]** Criar diretórios SAP no volume de ficheiros Azure NetApp.  
   Monte temporariamente o volume de Ficheiros Azure NetApp num dos VMs e crie os diretórios SAP (caminhos de ficheiro).  

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

1. **[A]** Versá versão de recursos-agentes-sap

   Certifique-se de que a versão do pacote de recursos-agentes-seiva instalado é pelo menos 3.9.5-124.el7
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

   Se utilizar o NFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Certifique-se de que corresponde à versão do protocolo NFS dos volumes Azure NetApp Files, ao montar os volumes. Se os volumes Azure NetApp Files forem criados como volumes NFSv3, utilize a configuração NFSv3 correspondente. Se os volumes Azure NetApp Files forem criados como volumes NFSv4.1, siga as instruções para desativar o mapeamento de ID e certifique-se de utilizar a configuração NFSv4.1 correspondente. Neste exemplo, os volumes Azure NetApp Files foram criados como volumes NFSv3.  

   Monte as novas ações

   ```
   sudo mount -a  
   ```

1. **[A]** Ficheiro SWAP de Configuração

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

   Reinicie o Agente para ativar a alteração

   ```
   sudo service waagent restart
   ```

1. **[A]** Configuração RHEL

   Configure RHEL conforme descrito no SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalação SAP NetWeaver ASCS/ERS

1. **[1]** Criar um recurso IP virtual e sonda de saúde para a instância ASCS

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

   Certifique-se de que o estado do cluster está bem e que todos os recursos são iniciados. Não é importante em que nó os recursos estão a funcionar.

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

1. **[1]** Instalar OSCS SAP NetWeaver  

   Instale o SAP NetWeaver ASCS como raiz no primeiro nó utilizando um nome de anfitrião virtual que mapeia para o endereço IP da configuração frontal do balançador de carga para o ASCS, por exemplo <b>anftstsapvh</b>, <b>192.168.14.9</b> e o número de exemplo que utilizou para a sonda do carregador, por exemplo <b>00</b>.

   Pode utilizar o parâmetro de seiva SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não-raiz se conecte ao sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Se a instalação não criar uma sub-dobragem em /usr/seiva/**QAS**/ASCS **00,** tente definir o proprietário e o grupo da pasta ASCS **00** e retentou novamente.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Criar um recurso IP virtual e sonda de saúde para a instância ERS

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
 
   Certifique-se de que o estado do cluster está bem e que todos os recursos são iniciados. Não é importante em que nó os recursos estão a funcionar.

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

   Instale o SAP NetWeaver ERS como raiz no segundo nó utilizando um nome de anfitrião virtual que mapeia para o endereço IP da configuração frontal do balançador de carga para o ERS, por exemplo <b>anftstsapers</b>, <b>192.168.14.10</b> e o número de instância que utilizou para a sonda do balançador de carga, por exemplo <b>01</b>.

   Pode utilizar o parâmetro de seiva SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não-raiz se conecte ao sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Se a instalação não criar uma sub-dobradura em /usr/seiva/**QAS**/ERS **01,** tente configurar o proprietário e o grupo da pasta ERS **01** e retentou novamente.

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
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   Tanto para a ENSA1 como para a ENSA2, certifique-se de que os `keepalive` parâmetros de SO estão definidos como descrito na nota [SAP 1410736](https://launchpad.support.sap.com/#/notes/1410736).  

   * Perfil ERS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** Configurar Manter Vivo

   A comunicação entre o servidor de aplicações SAP NetWeaver e o ASCS/SCS é encaminhada através de um equilibrador de carga de software. O balançador de carga desliga as ligações inativas após um tempo configurável. Para evitar isto, é necessário definir um parâmetro no perfil ASCS/SCS DO NETWeaver, se utilizar o ENSA1, e alterar as definições do sistema Linux `keepalive` em todos os servidores SAP para ambos OS ENSA1/ENSA2. Leia [a Nota SAP 1410736][1410736] para mais informações.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   ```

1. **[A]** Atualizar o ficheiro /usr/sap/sapservices

   Para evitar o início dos casos pelo script de arranque sapinit, todas as instâncias geridas pela Pacemaker devem ser comentadas a partir do ficheiro /usr/sap/sapservices. Não comente a instância SAP HANA se será utilizado com HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Criar os recursos de cluster SAP

   Se utilizar a arquitetura do servidor 1 do enqueue (ENSA1), defina os recursos da seguinte forma:

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

   A SAP introduziu suporte para o servidor 2, incluindo a replicação, a partir de SAP NW 7.52. A partir da Plataforma ABAP 1809, o servidor 2 do enqueue é instalado por padrão. Consulte a nota [SAP 2630416](https://launchpad.support.sap.com/#/notes/2630416) para obter suporte ao servidor 2.
   Se utilizar a arquitetura do servidor 2 do enqueue[(ENSA2),](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)instale agentes de recursos-agentes-sap-4.1.1-12.el7.x86_64 ou mais recentes e defina os recursos da seguinte forma:

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

   Se estiver a atualizar a partir de uma versão mais antiga e a mudar para o servidor 2, consulte a nota [SAP 2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Os intervalos de tempo na configuração acima são apenas exemplos e podem ter de ser adaptados à configuração ESPECÍFICA DOM. 

   Certifique-se de que o estado do cluster está bem e que todos os recursos são iniciados. Não é importante em que nó os recursos estão a funcionar.

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

1. **[A]** Adicione as regras de firewall para ASCS e ERS em ambos os nós Adicione as regras de firewall para ASCS e ERS em ambos os nós.
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

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparação do servidor de aplicações SAP NetWeaver

   Algumas bases de dados requerem que a instalação da instância da base de dados seja executada num servidor de aplicações. Prepare as máquinas virtuais do servidor de aplicações para poder usá-las nestes casos.  

   Os passos pressupõem que instala o servidor de aplicações num servidor diferente dos servidores ASCS/SCS e HANA. Caso contrário, não são necessários alguns dos passos abaixo (como configurar a resolução do nome do anfitrião).  

   Os seguintes itens são prefixados com **ambos [A]** - aplicáveis tanto a PAS como a AAS, **[P]** - apenas aplicáveis a PAS ou **[S]** - apenas aplicáveis à AAS.  

1. **[A]** Resolução de nome do anfitrião de configuração Pode utilizar um servidor DNS ou modificar os /etc/anfitriões em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos:  

   ```
   sudo vi /etc/hosts
   ```

   Insira as seguintes linhas para /etc/anfitriões. Altere o endereço IP e o nome de anfitrião para corresponder ao seu ambiente.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** Criar o diretório de sapmnt Criar o diretório de sapmnt.
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

   Se utilizar o NFSv4.1:
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

   Se utilizar o NFSv4.1:
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

   Se utilizar o NFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** Ficheiro SWAP de Configuração
 
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

   Reinicie o Agente para ativar a alteração

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Instalar base de dados

Neste exemplo, o SAP NetWeaver está instalado no SAP HANA. Pode utilizar todas as bases de dados suportadas para esta instalação. Para obter mais informações sobre como instalar o SAP HANA em Azure, consulte [Alta disponibilidade de SAP HANA em VMs Azure em Red Hat Enterprise Linux][sap-hana-ha] . For a list of supported databases, see [SAP Note 1928533][1928533] .

1. Executar a instalação de instância de caixa de dados SAP

   Instale a instância da base de dados SAP NetWeaver como raiz utilizando um nome de anfitrião virtual que mapeia para o endereço IP da configuração frontal do balançador de carga para a base de dados.

   Pode utilizar o parâmetro de seiva SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não-raiz se conecte ao sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Instalação do servidor de aplicações SAP NetWeaver

Siga estes passos para instalar um servidor de aplicação SAP.

1. Preparar servidor de aplicações

   Siga os passos no capítulo [SAP NetWeaver preparação](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) do servidor de aplicações acima para preparar o servidor de aplicações.

1. Instalar o servidor de aplicações SAP NetWeaver

   Instale um servidor de aplicações SAP NetWeaver primário ou adicional.

   Pode utilizar o parâmetro de seiva SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não-raiz se conecte ao sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Atualizar loja segura SAP HANA

   Atualize a loja segura SAP HANA para indicar o nome virtual da configuração de replicação do sistema SAP HANA.

   Executar o seguinte comando para listar as entradas como \<sapsid> adm

   ```
   hdbuserstore List
   ```

   Isto deve listar todas as entradas e deve parecer semelhante a
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   A saída mostra que o endereço IP da entrada predefinida está a apontar para a máquina virtual e não para o endereço IP do balançador de carga. Esta entrada precisa de ser alterada para apontar para o nome de hospedeiro virtual do equilibrador de carga. Certifique-se de que utiliza a mesma porta **(30313** na saída acima) e o nome da base de dados **(QAS** na saída acima)!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

1. Migrar manualmente a instância ASCS

   Estado dos recursos antes de iniciar o teste:

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

   Estado de recursos após o teste:

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

1. Simular queda de nó

   Estado dos recursos antes de iniciar o teste:

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

   O estado após o nó recomeçar deve ser assim.

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

   Estado de recursos após o teste:

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

1. Desa um processo de servidor de mensagens

   Estado dos recursos antes de iniciar o teste:

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

   Se só desligar o servidor de mensagens uma vez, será reiniciado por `sapstart` . Se o matares com frequência, o Pacemaker irá eventualmente mover a instância ASCS para o outro nó. Executar os seguintes comandos como raiz para limpar o estado de recursos da instância ASCS e ERS após o teste.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado de recursos após o teste:

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

1. Kill enqueue server process

   Estado dos recursos antes de iniciar o teste:

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

   Executar os seguintes comandos como raiz no nó onde a instância ASCS está em execução para matar o servidor de enqueue.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   A instância ASCS deve falhar imediatamente no outro nó. A instância ERS também deve ser chumbada após o início da instância ASCS. Executar os seguintes comandos como raiz para limpar o estado de recursos da instância ASCS e ERS após o teste.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado de recursos após o teste:

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

1. Kill enqueue processo de servidor de replicação

   Estado dos recursos antes de iniciar o teste:

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

   Executar o seguinte comando como raiz no nó onde a instância ERS está a correr para eliminar o processo do servidor de replicação enqueue.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Se executar apenas o comando uma vez, `sapstart` reiniciará o processo. Se o executar com frequência, `sapstart` não reiniciará o processo e o recurso estará num estado parado. Executar os seguintes comandos como raiz para limpar o estado de recursos da instância ERS após o teste.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado de recursos após o teste:

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

1. Kill enqueue processo sapstartsrv

   Estado dos recursos antes de iniciar o teste:

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

   Executar os seguintes comandos como raiz no nó onde o ASCS está em funcionamento.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   O processo de sapstartsrv deve ser sempre reiniciado pelo agente de recursos pacemaker como parte da monitorização. Estado de recursos após o teste:

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

* [HA para SAP NW em VMs Azure em RHEL para aplicações SAP multi-SID guia](./high-availability-guide-rhel-multi-sid.md)
* [Azure Virtual Machines planejamento e implementação para SAP][planning-guide]
* [Implantação de máquinas virtuais Azure para SAP][deployment-guide]
* [Implantação DBMS de máquinas virtuais Azure para SAP][dbms-guide]
* Para aprender a estabelecer uma elevada disponibilidade e plano para a recuperação de desastres da SAP HANA em Azure (grandes instâncias), ver [SAP HANA (grandes instâncias) alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md).
* Para aprender a estabelecer uma elevada disponibilidade e plano para a recuperação de desastres da SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]
