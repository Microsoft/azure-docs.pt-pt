---
title: Azure VMs HA para SAP NW no Windows com Ficheiros Azure NetApp (SMB); Microsoft Docs
description: Alta disponibilidade para SAP NetWeaver em VMs Azure no Windows com Ficheiros Azure NetApp (SMB) para aplicações SAP
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
ms.date: 10/29/2019
ms.author: radeltch
ms.openlocfilehash: b41db629c5308348f632b3dc51c75822ba361c60
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77591358"
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

Este artigo descreve como implementar, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um sistema SAP NetWeaver 7.50 altamente disponível em VMs windows, utilizando [SMB](https://docs.microsoft.com/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) em [Ficheiros Azure NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)  

A camada de base de dados não está coberta de detalhes neste artigo. Assumimos que a [rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Azure já foi criada.  

Leia primeiro as seguintes Notas e papéis SAP:

* [Documentação de Ficheiros Azure NetApp][anf-azure-doc] 
* Nota SAP [1928533,][1928533]que contém:  
  * Uma lista de tamanhos De VM Azure que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade para tamanhos de VM Azure
  * Software SAP suportado e sistema operativo (OS) e combinações de bases de dados
  * Versão necessária do kernel SAP para windows no Microsoft Azure
* O SAP Note [2015553][2015553] lista os pré-requisitos para implementações de software SAP suportadas pela SAP em Azure.
* O SAP Note [2178632][2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* A Nota [SAP 1999351][1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização avançada do Azure para sAP.
* SAP Nota [2287140](https://launchpad.support.sap.com/#/notes/2287140) lista pré-requisitos para recurso CA suportado por SAP do protocolo SMB 3.x.
* O SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770) tem informações de resolução de problemas para a lenta transação SAP AL11 no Windows 2012 e 2016.
* O SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507) possui informações sobre funcionalidade seletiva transparente para uma partilha de ficheiros no Windows Server com o protocolo SMB 3.0.
* SAP Nota [662452](https://launchpad.support.sap.com/#/notes/662452) tem recomendação (desativar a geração de nomes de 8,3) para abordar o desempenho/erros do sistema de ficheiros deficientes durante os acessos de dados.
* [Instale a alta disponibilidade do SAP NetWeaver num cluster de falhas do Windows e partilha de ficheiros para instâncias SAP ASCS/SCS no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share) 
* [Arquitetura e cenários de alta disponibilidade das Máquinas Virtuais Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
* [Adicione a porta da sonda na configuração do cluster ASCS](sap-high-availability-installation-wsfc-file-share.md)
* [Instalação de uma instância (A)SCS num cluster de failover](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Criar um volume SMB para Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)
* [Aplicações NetApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Descrição geral

A SAP desenvolveu uma nova abordagem, e uma alternativa aos discos partilhados do cluster, para agrupar uma instância SAP ASCS/SCS num cluster de falhas do Windows. Em vez de utilizar discos partilhados em cluster, pode-se usar uma partilha de ficheiroS SMB para implementar ficheiros anfitriões globais SAP. O Azure NetApp Files suporta o SMBv3 (juntamente com o NFS) com o NTFS ACL utilizando o Ative Directory. Os Ficheiros Azure NetApp estão automaticamente altamente disponíveis (uma vez que é um serviço PaaS). Estas funcionalidades tornam o Azure NetApp Files uma excelente opção para hospedar a quota de ficheiroS SMB para o Global SAP.  
Ambos os Serviços de [Domínio do Diretório Ativo Azure (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/overview) e os Serviços de Domínio de [Diretório Ativo (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) são apoiados. Pode utilizar controladores de domínio Ative Diretório existentes com Ficheiros Azure NetApp. Os controladores de domínio podem estar em Azure como máquinas virtuais, ou nas instalações via ExpressRoute ou S2S VPN. Neste artigo, usaremos o controlador de domínio num VM Azure.  
A elevada disponibilidade (HA) para os serviços centrais SAP Netweaver requer armazenamento partilhado. Para isso no Windows, até agora era necessário construir o cluster SOFS ou utilizar o cluster partilhado de discos como o SIOS. Agora é possível alcançar o SAP Netweaver HA utilizando armazenamento partilhado, implantado em Ficheiros Azure NetApp. A utilização de Ficheiros Azure NetApp para o armazenamento partilhado elimina a necessidade de SOFS ou SIOS.  

> [!NOTE]
> Clustering SAP ASCS/SCS, utilizando uma parte de ficheiro, é suportado para SAP NetWeaver 7.40 (e mais tarde), com SAP Kernel 7.49 (e mais tarde).  

![Arquitetura SAP ASCS/SCS HA com quota SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Os pré-requisitos para uma parte de ficheiro SMB são:
* Protocolo SMB 3.0 (ou posterior).
* Capacidade de definir listas de controlo de acesso ao Diretório Ativo (ACLs) para grupos de utilizadores de Diretório Ativo e o objeto de computador$.
* A parte do ficheiro deve ser ativada por HA.

A participação dos serviços SAP Central nesta arquitetura de referência é oferecida pelos Ficheiros Azure NetApp:

![Arquitetura SAP ASCS/SCS HA com quota SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Criar e montar volume SMB para Ficheiros Azure NetApp

Execute os seguintes passos, como preparação para a utilização de Ficheiros Azure NetApp.  

1. Siga os passos para [registar ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  
2. Criar conta Azure NetApp, seguindo os passos descritos na [Create a NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)  
3. Configurar o conjunto de capacidades, seguindo as instruções em [Configurar um conjunto de capacidades](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)
4. Os recursos do Azure NetApp Files devem residir na sub-rede delegada. Siga as instruções em [Delegar uma sub-rede para ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) para criar uma sub-rede delegada.  

> [!IMPORTANT]
> Você precisa criar conexões de Diretório Ativo antes de criar um volume SMB. Reveja os [requisitos relativos às ligações de Diretório Ativo](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections).  

5. Criar conexão Diretório Ativo, conforme descrito na [Create a Ative Directy connection](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#create-an-active-directory-connection)  
6. Criar volume SMB Azure NetApp Ficheiros SMB, seguindo as instruções em [Adicionar um volume SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#add-an-smb-volume)  
7. Monte o volume SMB na sua Máquina Virtual Windows.

> [!TIP]
> Pode encontrar as instruções sobre como montar o volume de Ficheiros Azure NetApp, se navegar no [Portal Azure](https://portal.azure.com/#home) para o objeto De ficheiros Azure NetApp, clique na lâmina **Volumes,** em **seguida, monte instruções**.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Prepare a infraestrutura para o SAP HA utilizando um cluster de falhas do Windows 

1. [Definir os endereços IP dNS necessários](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#b22d7b3b-4343-40ff-a319-097e13f62f9e)  
2. [Detete os endereços IP estáticos para as máquinas virtuais SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#84c019fe-8c58-4dac-9e54-173efd4b2c30).
3. [Defino um endereço IP estático para o equilibrador de carga interna Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#7a8f3e9b-0624-4051-9e41-b73fff816a9e).
4. Defino regras padrão de equilíbrio de [carga ASCS/SCS para o equilíbrio](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#f19bd997-154d-4583-a46e-7f5a69d0153c)de carga interna Azure .
5. Alterar as regras de [equilíbrio da carga padrão ASCS/SCS para o equilibrador de carga interna Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#fe0bd8b5-2b43-45e3-8295-80bee5415716).
6. [Adicione as máquinas virtuais do Windows ao domínio](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
7. [Adicione entradas de registo em ambos os nós de cluster da instância SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#661035b2-4d0f-4d31-86f8-dc0a50d78158)
8. [Configurar um cluster de falha do Windows Server para uma instância SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#0d67f090-7928-43e0-8772-5ccbf8f59aab)
9. Se estiver a utilizar o Windows Server 2016, recomendamos que configure a [Testemunha de Nuvem Azure](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Instale a instância AsCS SAP em ambos os nós

Precisa do seguinte software da SAP:
   * SAP Software Provisioning Manager (SWPM) versão de ferramenta de instalação SPS25 ou posterior.
   * SAP Kernel 7.49 ou mais tarde
   * Crie um nome de anfitrião virtual (nome de rede cluster) para a instância SAP ASCS/SCS agrupada, tal como descrito na [Create um nome de hospedeiro virtual para a instância SAP ASCS/SCS agrupada](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> Clustering SAP ASCS/SCS, utilizando uma parte de ficheiro, é suportado para SAP NetWeaver 7.40 (e mais tarde), com SAP Kernel 7.49 (e mais tarde).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Instale uma instância ASCS/SCS no primeiro nó de cluster ASCS/SCS

1. Instale uma instância SAP ASCS/SCS no nó do primeiro cluster. Inicie a ferramenta de instalação SAP SWPM e, em seguida, navegue para: **Produto** > **DBMS** > Instalação > Application Server ABAP (ou Java) > Sistema de Alta Disponibilidade > ASCS/SCS instância > Primeiro nó de cluster.  

2. Selecione Cluster de Partilha de **Ficheiros** como a configuração de partilha de cluster em SWPM.  
3. Quando solicitado na etapa **SAP System Cluster Parameters**, insira o nome de anfitrião para a partilha de Ficheiros SMB do Azure NetApp que já criou como **Nome de Anfitrião de Partilha**de Ficheiros .  Neste exemplo, o nome de anfitrião da partilha SMB é **anfsmb-9562**. 

> [!IMPORTANT]
> Se o verificador pré-requisito Os resultados em SWPM mostrarem a condição de funcionalidade de disponibilidade contínua não cumprida, pode ser abordado seguindo as instruções na mensagem de [erro retardada quando tentar aceder a uma pasta partilhada que já não existe no Windows](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

> [!TIP]
> Se o verificador pré-requisito Resultados em SWPM mostrar a condição de Swap Size não cumprida, pode ajustar o tamanho SWAP navegando para My Computer>System Properties>Performance Settings> Advanced> Virtual memory> Change.  

4. Configure um recurso de cluster SAP, a porta de sonda `SAP-SID-IP`, utilizando o PowerShell. Execute esta configuração num dos nós de cluster SAP ASCS/SCS, conforme descrito na porta de [sonda Configure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#10822f4f-32e7-4871-b63a-9b86c76ce761).

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Instale uma instância ASCS/SCS no segundo nó de cluster ASCS/SCS

1. Instale uma instância SAP ASCS/SCS no segundo nó de cluster. Inicie a ferramenta de instalação SAP SWPM e, em seguida, navegue para **produto** > **DBMS** > Instalação > Application Server ABAP (ou Java) > Sistema de Alta Disponibilidade > ASCS/SCS instância > Nó de cluster adicional.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Instale uma instância DBMS e servidores de aplicações SAP

Complete a instalação SAP, instalando:

   * Um caso DBMS  
   * Um servidor de aplicação SAP primário  
   * Um servidor adicional de aplicações SAP  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Teste a falha da instância SAP ASCS/SCS 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Falhar do nó de cluster A ao nó de cluster B e de volta
Neste cenário de teste, vamos referir-nos ao nó de cluster sapascs1 como nó A, e ao nó de aglomerado sapascs2 como nó B.

1. Verifique se os recursos do cluster estão a funcionar no nó A. ![Figura 1: O Windows Server falha os recursos de cluster que funciona no nó A antes do teste de falha](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Reinicie o nó de cluster A. Os recursos do cluster SAP passarão para o nó de cluster B. ![Figura 2: Os recursos de cluster do Windows Server falham a execução no nó B após o teste de falha](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Teste de entrada de bloqueio

1.Verifique se o Servidor de Replicação sap Enqueue (ERS) está ativo  
2. Inicie sessão no sistema SAP, execute a transação SU01 e abra um ID do utilizador no modo de mudança. Isso gerará entrada de bloqueio SAP.  
3. À medida que estiver registado no sistema SAP, exiba a entrada de bloqueio, navegando até à transação ST12.  
4. Falhar sobre os recursos ASCS do nó de cluster A ao nó de cluster B.  
5. Verifique se a entrada de bloqueio, gerada antes da falha dos recursos de cluster SAP ASCS/SCS.  

![Figura 3: A entrada do bloqueio é retida após teste de failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Para mais informações, consulte [Troubleshooting for Enqueue Failover in ASCS with ERS](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>Passos seguintes

* [Planeamento e implementação de Máquinas Virtuais Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP][dbms-guide]
* Para aprender como estabelecer alta disponibilidade e plano para recuperação de desastres de SAP 
* HANA on Azure (grandes instâncias), veja [SAP HANA (grandes instâncias) alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md).
* Para aprender como estabelecer alta disponibilidade e plano para a recuperação de desastres de SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]
