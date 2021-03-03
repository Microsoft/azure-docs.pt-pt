---
title: Azure VMs HA para SAP NW no Windows com Ficheiros Azure NetApp (SMB)| Microsoft Docs
description: Alta disponibilidade para SAP NetWeaver em VMs Azure no Windows com Ficheiros Azure NetApp (SMB) para aplicações SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.openlocfilehash: e8d58a74f9ce8489465934398014dd4af3309a9e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669831"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Alta disponibilidade para SAP NetWeaver em VMs Azure no Windows com Ficheiros Azure NetApp (SMB) para aplicações SAP

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

Este artigo descreve como implantar, configurar as máquinas virtuais, instalar a estrutura do cluster e instalar um sistema SAP NetWeaver 7.50 altamente disponível nos VMs do Windows, utilizando [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) em [Ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

A camada de base de dados não está coberta em detalhe neste artigo. Assumimos que a [rede virtual](../../../virtual-network/virtual-networks-overview.md) Azure já foi criada.  

Leia primeiro as seguintes notas e artigos SAP:

* [Documentação dos Ficheiros Azure NetApp][anf-azure-doc] 
* Nota SAP [1928533,][1928533]que contém:  
  * Uma lista de tamanhos Azure VM que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade dos tamanhos Azure VM
  * Combinações suportadas de software SAP e sistema operativo (OS) e de base de dados
  * Versão kernel SAP necessária para Windows no Microsoft Azure
* O SAP Note [2015553][2015553] lista pré-requisitos para implementações de software SAP suportadas pelo SAP em Azure.
* A Nota [SAP 2178632][2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* Sap Nota [1999351][1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização melhorada de Azure para o SAP.
* O SAP Note [2287140](https://launchpad.support.sap.com/#/notes/2287140) lista pré-requisitos para a funcionalidade CA suportada pelo SAP do protocolo SMB 3.x.
* O SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770) tem informações de resolução de problemas para a transação SAP AL11 no Windows 2012 e 2016.
* O SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507) tem informações sobre a funcionalidade de failover transparente para uma partilha de ficheiros no Windows Server com o protocolo SMB 3.0.
* SAP Nota [662452](https://launchpad.support.sap.com/#/notes/662452) tem recomendação (desativar 8.3 geração de nomes) para abordar o desempenho/erros do sistema de ficheiros Pobres durante os acessos aos dados.
* [Instale a alta disponibilidade do SAP NetWeaver num cluster de failover do Windows e partilhe a partilha de ficheiros para as instâncias SAP ASCS/SCS no Azure](./sap-high-availability-installation-wsfc-file-share.md) 
* [Azure Virtual Machines arquitetura de alta disponibilidade e cenários para SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
* [Adicione a porta da sonda na configuração do cluster ASCS](sap-high-availability-installation-wsfc-file-share.md)
* [Instalação de uma (A)ScS Instance num cluster de falha](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Criar um volume SMB para o Azure NetApp Files](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections)
* [Aplicações NETApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Descrição Geral

A SAP desenvolveu uma nova abordagem, e uma alternativa aos discos partilhados do cluster, para agrupar uma instância SAP ASCS/SCS num cluster de falha do Windows. Em vez de usar discos partilhados de cluster, pode-se usar uma partilha de ficheiros SMB para implementar ficheiros de anfitriões globais SAP. O Azure NetApp Files suporta o SMBv3 (juntamente com o NFS) com a NTFS ACL utilizando o Ative Directory. O Azure NetApp Files está automaticamente altamente disponível (uma vez que é um serviço PaaS). Estas funcionalidades tornam a Azure NetApp Files uma ótima opção para hospedar a partilha de ficheiros SMB para a SAP global.  
Tanto os [Serviços de Domínio Azure Ative (AD)](../../../active-directory-domain-services/overview.md) como [os Serviços de Domínio do Diretório Ativo (DS AD)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) são suportados. Pode utilizar controladores de domínio ative diretório existentes com ficheiros Azure NetApp. Os controladores de domínio podem estar em Azure como máquinas virtuais, ou nas instalações via ExpressRoute ou S2S VPN. Neste artigo, usaremos o controlador de domínio num VM Azure.  
A elevada disponibilidade (HA) para os serviços centrais DA NETWEaver requer armazenamento partilhado. Para o conseguir no Windows, até agora era necessário construir um cluster SOFS ou utilizar o cluster de discos partilhados s/w como SIOS. Agora é possível alcançar o SAP Netweaver HA utilizando o armazenamento partilhado, implantado em Ficheiros Azure NetApp. A utilização de Ficheiros Azure NetApp para o armazenamento partilhado elimina a necessidade de SOFS ou SIOS.  

> [!NOTE]
> O agrupamento de casos SAP ASCS/SCS utilizando uma partilha de ficheiros é suportado por SAP NetWeaver 7.40 (e mais tarde), com SAP Kernel 7.49 (e mais tarde).  

![SAP ASCS/SCS HA Arquitetura com quota SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Os pré-requisitos para uma partilha de ficheiros SMB são:
* Protocolo SMB 3.0 (ou posterior).
* Capacidade de definir listas de controlo de acesso ao Ative Directory (ACLs) para grupos de utilizadores do Ative Directory e o objeto do computador computer$.
* A partilha de ficheiros deve estar ativada pelo HA.

A quota para os serviços SAP Central nesta arquitetura de referência é oferecida pela Azure NetApp Files:

![SAP ASCS/SCS HA Arquitetura com detalhes de partilha SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Criar e montar volume SMB para ficheiros Azure NetApp

Execute os seguintes passos, como preparação para a utilização de Ficheiros Azure NetApp.  

1. Siga os passos para [registar os ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-register.md)  
2. Criar conta Azure NetApp, seguindo os passos descritos na  [Criar uma conta NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)  
3. Configurar o pool de capacidade, seguindo as instruções em [Configurar um pool de capacidade](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
4. Os recursos do Azure NetApp Files devem residir na sub-rede delegada. Siga as instruções em [Delegado uma sub-rede para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) para criar sub-rede delegada.  

   > [!IMPORTANT]
   > É necessário criar ligações ative diretoria antes de criar um volume SMB. Reveja os [requisitos para ligações de Diretório Ativo](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections).  

5. Criar ligação ative directy, conforme descrito na [Criar uma ligação ative directory](../../../azure-netapp-files/create-active-directory-connections.md#create-an-active-directory-connection)  
6. Criar SMB Azure NetApp Ficheiros SMB volume SMB, seguindo as instruções em [Adicionar um volume SMB](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)  
7. Monte o volume SMB na sua Máquina Virtual Windows.

> [!TIP]
> Pode encontrar as instruções sobre como montar o volume de Ficheiros Azure NetApp, se navegar no [Portal Azure](https://portal.azure.com/#home) para o objeto Azure NetApp Files, clicar na lâmina **volumes** e, em seguida, **montar instruções**.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Prepare a infraestrutura para SAP HA utilizando um cluster de failover do Windows 

1. [Desloje as regras de equilíbrio de carga ASCS/SCS para o balançador interno de carga Azure](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716).
2. [Adicione máquinas virtuais do Windows ao domínio.](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c)
3. [Adicionar entradas de registo em ambos os nós de cluster da instância SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [Configurar um cluster de failover do Windows Server para uma instância SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. Se estiver a utilizar o Windows Server 2016, recomendamos que configuure [Azure Cloud Witness](/windows-server/failover-clustering/deploy-cloud-witness).


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Instale a instância SAP ASCS em ambos os nós

Precisa do seguinte software da SAP:
   * Versão de ferramenta de instalação SPS25 ou mais tarde do GESTOR DE Provisão de Software SAP (SWPM).
   * Kernel SAP 7.49 ou mais tarde
   * Crie um nome de hospedeiro virtual (nome de rede de cluster) para a instância SAP ASCS/SCS agrupada, conforme descrito na [Criar um nome de hospedeiro virtual para a instância SAP ASCS/SCS agrupada](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> O agrupamento de casos SAP ASCS/SCS utilizando uma partilha de ficheiros é suportado por SAP NetWeaver 7.40 (e mais tarde), com SAP Kernel 7.49 (e mais tarde).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Instale uma instância ASCS/SCS no primeiro nó de cluster ASCS/SCS

1. Instale uma instância SAP ASCS/SCS no primeiro nó de cluster. Inicie a ferramenta de instalação SAP SWPM e, em seguida, navegue para:  >  **DBMS** > Instalação > Servidor de Aplicação ABAP (ou Java) > High-Availability Sistema > instância ASCS/SCS > primeiro nó de cluster.  

2. Selecione **o Cluster de Partilha de Ficheiros** como a Configuração de partilha de cluster em SWPM.  
3. Quando solicitado no passo OS Parâmetros de Cluster do Sistema SAP , **insira** o nome de anfitrião para a partilha de ficheiros Azure NetApp que já criou como **Nome do Anfitrião da Partilha de Ficheiros**.  Neste exemplo, o nome de anfitrião da SMB é **anfsmb-9562**. 

   > [!IMPORTANT]
   > Se o check-in pré-requisito Resultados em SWPM mostrar a condição de funcionalidade de disponibilidade contínua não cumprida, pode ser endereçada seguindo as instruções na [mensagem de erro retardada quando tenta aceder a uma pasta partilhada que já não existe no Windows](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

   > [!TIP]
   > Se os resultados do verificador pré-requisito em SWPM mostrarem a condição de swap size não cumprida, pode ajustar o tamanho SWAP navegando para My Computer>System Properties>Performance Configurações> Alteração de> de memória virtual> Avançada.  

4. Configure um recurso de cluster SAP, a porta da `SAP-SID-IP` sonda, utilizando o PowerShell. Execute esta configuração num dos nós de cluster SAP ASCS/SCS, conforme descrito na [porta da sonda Configure](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761).

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Instale uma instância ASCS/SCS no segundo nó de cluster ASCS/SCS

1. Instale uma instância SAP ASCS/SCS no segundo nó de cluster. Inicie a ferramenta de instalação SAP SWPM e, em seguida, navegue para **o produto**  >  **DBMS** > Instalação > Servidor de Aplicação ABAP (ou Java) sistema > High-Availability > instância ASCS/SCS > nó adicional de cluster.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Instale uma instância DBMS e servidores de aplicações SAP

Complete a sua instalação SAP, instalando:

   * Um exemplo de DBMS  
   * Um servidor de aplicação PRINCIPAL SAP  
   * Um servidor adicional de aplicação SAP  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testar a falha da instância SAP ASCS/SCS 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Falha do nó de cluster A para o nó de cluster B e volta
Neste cenário de teste, referimo-nos ao nó de aglomerado sapascs1 como nó A, e ao nó de aglomerado sapascs2 como nó B.

1. Verifique se os recursos do cluster estão a funcionar no nó A. ![ Figura 1: Falha nos recursos de cluster do Windows Server que estão a funcionar no nó A antes do teste de failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Reinicie o nó de cluster A. Os recursos do cluster SAP passarão para o nó de cluster B. ![ Figura 2: Falha nos recursos de cluster do Windows Server que estão a funcionar no nó B após o teste de failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Teste de entrada de bloqueio

1.Verifique se o Servidor de Replicação de Enques (ERS) da SAP Enqueue está ativo  
2. Inicie sessão no sistema SAP, execute a transação SU01 e abra um ID do utilizador no modo de alteração. Isso gerará entrada de bloqueio SAP.  
3. Como você está registado no sistema SAP, apresente a entrada de bloqueio, navegando para a transação ST12.  
4. Falha nos recursos ASCS do nó de cluster A ao nó de cluster B.  
5. Verifique se a entrada de bloqueio, gerada antes da falha dos recursos do cluster SAP ASCS/SCS, é mantida.  

![Figura 3: A entrada de bloqueio é mantida após o teste de failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Para mais informações, consulte [a Resolução de Problemas da Enqueue Failover na ASCS com a ERS](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>Passos seguintes

* [Azure Virtual Machines planejamento e implementação para SAP][planning-guide]
* [Implantação de máquinas virtuais Azure para SAP][deployment-guide]
* [Implantação DBMS de máquinas virtuais Azure para SAP][dbms-guide]
* Para aprender a estabelecer alta disponibilidade e plano para a recuperação de desastres do SAP 
* HANA on Azure (grandes instâncias), ver [SAP HANA (grandes instâncias) alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md).
* Para aprender a estabelecer uma elevada disponibilidade e plano para a recuperação de desastres da SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]
