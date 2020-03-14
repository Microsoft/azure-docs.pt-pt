---
title: Começar com sAP em VMs Azure  Microsoft Docs
description: Conheça as soluções SAP que funcionam em máquinas virtuais (VMs) no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27fdf966f99ac2cba394515cb654ed74178d6673
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137650"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Use o Azure para hospedar e executar cenários de carga de trabalho SAP

Quando utilizar o Microsoft Azure, pode executar de forma fiável as suas cargas de trabalho e cenários SAP críticos de missão numa plataforma escalável, compatível e comprovada pela empresa. Obtém-se a escalabilidade, flexibilidade e economia de custos do Azure. Com a parceria alargada entre a Microsoft e o SAP, pode executar aplicações SAP em cenários de desenvolvimento e teste e produção em Azure e ser totalmente suportado. De SAP NetWeaver a SAP S/4HANA, SAP BI no Linux a Windows, e SAP HANA a SQL, temos-te coberto.

Além de hospedar cenários SAP NetWeaver com os diferentes DBMS em Azure, você pode hospedar outros cenários de carga de trabalho SAP, como SAP BI em Azure. 

A singularidade do Azure para o SAP HANA é uma oferta que distingue o Azure. Para permitir o hospedar mais memória e cenários SAP exigentes com recursos CPU que envolvam sAP HANA, o Azure oferece o uso de hardware de metal nu dedicado ao cliente. Utilize esta solução para executar implementações SAP HANA que requerem até 24 TB (escala de 120 TB) de memória para S/4HANA ou outra carga de trabalho SAP HANA. 

Hospedar cenários de carga de trabalho SAP em Azure também pode criar requisitos de integração de identidade e inscrição única. Esta situação pode ocorrer quando utiliza o Azure Ative Directory (Azure AD) para ligar diferentes componentes SAP e software SAP como serviço (SaaS) ou plataforma-as-a-service (PaaS). Uma lista de tais integrações e cenários de inscrição única com entidades Azure AD e SAP é descrita e documentada na secção "Integração de identidade AAD SAP e inscrição única".

## <a name="changes-to-the-sap-workload-section"></a>Alterações na secção de carga de trabalho do SAP
As alterações aos documentos na secção de carga de trabalho do SAP sobre o Azure estão listadas no final deste artigo. As entradas no registo de alterações são mantidas durante cerca de 180 dias.

## <a name="you-want-to-know"></a>Quer saber.
Se tiver perguntas específicas, vamos apontá-lo para documentos ou fluxos específicos nesta secção da página inicial. Quer saber:

- As unidades Azure VMs e HANA Large Instance são suportadas para as quais o software SAP lança e quais as versões do sistema operativo. Leia o documento [O software SAP suportado para a implementação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) do Azure para obter respostas e o processo para encontrar a informação
- Os cenários de implantação do SAP são suportados com VMs Azure e GRANDES Instâncias HANA. Informações sobre os cenários suportados podem ser encontradas nos documentos:
    - [Carga de trabalho da SAP em cenários de suporte à máquina virtual azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [Cenários suportados para hana grande instância](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- O que os Serviços Azure, os tipos de VM Azure e os serviços de armazenamento Azure estão disponíveis nas diferentes regiões do Azure, consulte o site [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA no Azure (Instâncias Grandes)

Uma série de documentos leva-o através de SAP HANA em Azure (Grandes Instâncias), ou para curto, HANA Large Instances. Para obter informações sobre as grandes instâncias hana começa com o documento [Visão Geral e arquitetura de SAP HANA on Azure (Grandes Instâncias)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) e passar pela documentação relacionada na secção HANA Large Instance



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA em máquinas virtuais Azure
Esta secção da documentação abrange diferentes aspetos do SAP HANA. Como pré-requisito, deve estar familiarizado com os principais serviços do Azure que prestam serviços elementares do Azure IaaS. Então, precisas de conhecimento da computação Azure, armazenamento e networking. Muitos destes assuntos são tratados no guia de [planeamento Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)relacionado com o SAP NetWeaver. 

Para obter informações sobre a HANA sobre o Azure, consulte os seguintes artigos e respetivos subartigos:

- [Configurações e operações de infraestrutura do SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA alta disponibilidade para máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Alta disponibilidade de SAP HANA em máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Guia de backup para SAP HANA em máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver implantado em máquinas virtuais Azure
Esta secção lista documentação de planeamento e implementação para sap NetWeaver e Business One no Azure. A documentação centra-se no básico e na utilização de bases de dados não HANA com uma carga de trabalho SAP no Azure. Os documentos e artigos de elevada disponibilidade são também a base para a elevada disponibilidade da HANA em Azure, tais como:

- [Guia de planeamento azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) 
- [SAP Business One em máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Proteja uma implementação de aplicação Multitier SAP NetWeaver utilizando a Recuperação do Site](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Conector de SAP LaMa para o Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Para obter informações sobre bases de dados não HANA sob uma carga de trabalho SAP em Azure, consulte:

- [Considerações para a implantação de DBMS de Máquinas Virtuais Azure para carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SQL Server Azure Virtual Machines DBMS implementação para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Implementação em Oracle do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Implementação em IBM DB2 do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB, Live Cache e Implementação de Servidor de Conteúdo em VMs Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Para obter informações sobre as bases de dados SAP HANA no Azure, consulte a secção "SAP HANA on Azure virtual machines".

Para obter informações sobre a elevada disponibilidade de uma carga de trabalho SAP no Azure, consulte:

- [Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Este documento aponta para vários outros documentos de arquitetura e cenário. Em documentos posteriores, são fornecidas ligações a documentos técnicos detalhados que explicam a implementação e configuração dos diferentes métodos de alta disponibilidade. Os diferentes documentos que mostram como estabelecer e configurar alta disponibilidade para uma carga de trabalho SAP NetWeaver cobrem sistemas operativos Linux e Windows.


Para obter informações sobre a integração entre os serviços Azure Ative Directory (Azure AD) e SAP e o único sinal, consulte:

- [Tutorial: Integração de Diretório Ativo Azure com SAP Cloud para Cliente](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração de Diretório Ativo Azure com Autenticação de Identidade da Plataforma SAP Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração de Diretório Ativo Azure com plataforma SAP Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração de Diretório Ativo Azure com sAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração de Diretório Ativo Azure com a SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Diretório Ativo Azure com a SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [O seu ambiente S/4HANA: Fiori Launchpad SAML single sign-on with Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Para obter informações sobre a integração dos serviços Azure nos componentes SAP, consulte:

- [Utilizar o SAP HANA no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery e SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Utilizar o conector SAP BW no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [O Azure Data Factory oferece integração de dados do SAP HANA e Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Registo de Alterações
- 03/11/2020: Alteração da [carga de trabalho sap em cenários suportados por máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) para clarificar várias bases de dados por suporte de instância DBMS
- 03/11/2020: Alteração no planeamento e implementação de [Máquinas Virtuais Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) explicando Geração 1 e Geração 2 VMs
- 03/10/2020: Alteração nas configurações de [armazenamento de máquinas virtuais SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) para clarificar os limites reais de entrada existentes da ANF
- 03/09/2020: Alteração da [alta disponibilidade para SAP NetWeaver em VMs Azure em SUSE Linux Enterprise Server para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server com Ficheiros Azure NetApp para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) [configuração do Pacemaker no SUSE Linux Enterprise Server em Azure,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) [Alta disponibilidade de IBM Db2 LUW em VMs Azure no SUSE Linux Enterprise Server com Pacemaker,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) [Alta disponibilidade de SAP HANA em VMs Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) e [alta disponibilidade para SAP NetWeaver em VMs Azure no guia Multi-SID RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) para atualizar recursos de cluster com o agente de recursos azure-lb 
- 03/05/2020: Alterações de estrutura e alterações de conteúdos para as regiões de Azure e máquinas Azure Virtual em planeamento e implementação de [Máquinas Virtuais Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- 03/03/2020: Alteração da [elevada disponibilidade para SAP NW em VMs Azure em SLES com ANF para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) para alterar para layout de volume ANF mais eficiente
- 03/01/2020: Guia de backup reformulado [para SAP HANA em Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) para incluir o serviço de backup Azure. Conteúdo reduzido e condensado no [SAP HANA Azure Backup a nível de ficheiro](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) e eliminado um terceiro documento que lida com cópia de segurança através de instantâneo de disco. Conteúdo é tratado em guia de backup para SAP HANA em Máquinas Virtuais Azure 
- 02/27/2020: Alteração da [elevada disponibilidade para SAP NW em VMs Azure em SLES para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Alta disponibilidade para SAP NW em VMs Azure em SLES com ANF para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) e [Alta disponibilidade para SAP NetWeaver em VMs Azure em guia multi-SID SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) para ajustar parâmetro de cluster "on fail"
- 02/26/2020: Alteração nas configurações de [armazenamento de máquinas virtuais SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) para clarificar a escolha do sistema de ficheiros para a HANA no Azure
- 02/26/2020: Alteração da [arquitetura de alta disponibilidade e cenários para](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) o SAP incluir a ligação ao HA para SAP NetWeaver em VMs Azure no guia Multi-SID RHEL
- 02/26/2020: Alteração da [elevada disponibilidade para SAP NW em VMs Azure em SLES para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Alta disponibilidade para SAP NW em VMs Azure em SLES com ANF para aplicações SAP,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [Azure VMs alta disponibilidade para SAP NetWeaver em RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) e [VMs Azure alta disponibilidade para SAP NetWeaver em RHEL com Ficheiros Azure NetApp](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) para remover a declaração de que o cluster multi-SID ASCS/ERS não é suportado
- 02/26/2020: Lançamento de [alta disponibilidade para SAP NetWeaver em VMs Azure no guia Multi-SID RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) para adicionar um link ao guia de cluster sUSE multi-SID
- 02/25/2020: Alteração da [arquitetura de alta disponibilidade e cenários para](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) o SAP adicionar links para os mais recentes artigos ha
- 02/25/2020: Alteração da [alta disponibilidade do IBM Db2 LUW em VMs Azure no SUSE Linux Enterprise Server com Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) para apontar para documentar que descreve acesso ao ponto final público com o equilíbrio de Carga Standard Azure
- 02/21/2020: Revisão completa do artigo [Implantação dbms das máquinas virtuais SAP ASE Azure para carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 02/21/2020: Alteração na configuração de armazenamento virtual de [máquinas SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) para representar uma nova recomendação em tamanho de listrapara /hana/dados e adição de definição de programador de I/O
- 02/21/2020: Alterações nos documentos de grande instância hana para representar as SKUs recém-certificadas de S224 e S224m
- 02/21/2020: Alteração nos [VMs Azure alta disponibilidade para SAP NetWeaver em RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) e [VMs Azure alta disponibilidade para SAP NetWeaver no RHEL com Ficheiros Azure NetApp](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) para ajustar os constrangimentos de cluster para a replicação do servidor de enfila2 arquitetura (ENSA2)
- 02/20/2020: Alteração da [alta disponibilidade para SAP NetWeaver em VMs Azure no guia Multi-SID SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) para adicionar um link ao guia de cluster sUSE multi-SID
- 02/13/2020: Alterações ao planeamento e implementação de [Máquinas Virtuais Azure para a SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) para implementar ligações a novos documentos
- 02/13/2020: Novo documento [SAP sobre cenário suportado](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) por máquina virtual Azure
- 02/13/2020: Acrescentou novo documento [O software SAP é suportado para a implementação do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020: Alteração da [alta disponibilidade do IBM Db2 LUW em VMs Azure no Red Hat Enterprise Linux Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) para apontar para um documento que descreve o acesso ao ponto final público com o equilíbrio da Carga Standard Azure
- 02/13/2020: Adicione os novos tipos de VM às [certificações e configurações SAP em execução no Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 02/13/2020: Adicione novas notas de suporte [SAP Cargas de trabalho SAP no Azure: lista de verificação de planeamento e implementação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020: Alteração dos [VMs Azure alta disponibilidade para SAP NetWeaver em RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) e [VMs Azure alta disponibilidade para SAP NetWeaver em RHEL com Ficheiros Azure NetApp](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) para alinhar os prazos dos recursos de cluster com as recomendações de timeout do Chapéu Vermelho
- 02/11/2020: Lançamento do [SAP HANA sobre migração de grandes instâncias azure para máquinas virtuais azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020: Alteração da [conectividade de ponto final público para VMs utilizando O ILB padrão Azure em cenários SAP HA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) para atualizar a amostra de imagens NSG
- 02/03/2020: Alteração da [elevada disponibilidade para SAP NW em VMs Azure em SLES para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) e [Alta disponibilidade para SAP NW em VMs Azure em SLES com APLICAÇÕES ANF para](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) remover o aviso sobre a utilização do traço nos nomes de acolhimento de nós de cluster em SLES
- 01/28/2020: Alteração [da elevada disponibilidade do SAP HANA em VMs Azure na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) para alinhar os prazos dos recursos do cluster SAP HANA com as recomendações de timeout do Chapéu Vermelho
- 01/17/2020: Alteração dos grupos de colocação de proximidade do [Azure para uma ótima latência da rede com aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) para alterar a secção de deslocação dos VM existentes para um grupo de colocação de proximidade
- 01/17/2020: Alteração das configurações de carga de [trabalho do SAP com Zonas](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) de Disponibilidade Azure para apontar ao procedimento que automatiza medições de latência entre zonas de disponibilidade
- 01/16/2020: Alterar [como instalar e configurar SAP HANA (Grandes Instâncias) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) para adaptar os lançamentos do OS ao diretório de hardware HANA IaaS
- 01/16/2020: Alterações na [elevada disponibilidade para SAP NetWeaver em VMs Azure no guia Multi-SID SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) para adicionar instruções para sistemas SAP, utilizando a arquitetura enqueue server 2 (ENSA2)
- 01/10/2020: Alterações na [escala SAP HANA com nó de standby em VMs Azure com Ficheiros Azure NetApp em SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) e em [escala SAP HANA com nó de standby em VMs Azure com Ficheiros Azure NetApp no RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) para adicionar instruções sobre como tornar as alterações `nfs4_disable_idmapping` permanentes.
- 01/10/2020: Alterações na [alta disponibilidade para SAP NetWeaver em VMs Azure em SLES com Ficheiros Azure NetApp para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) e em [Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver em RHEL com Ficheiros Azure NetApp para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) para adicionar instruções como montar volumes Azure NetApp Files NFSv4.
- 12/23/2019: Lançamento de [alta disponibilidade para SAP NetWeaver em VMs Azure no guia Multi-SID SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 12/18/2019: Lançamento da [escala SAP HANA com nó de standby em VMs Azure com Ficheiros Azure NetApp no RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 11/21/2019: Alterações na [escala SAP HANA com nó de standby em VMs Azure com Ficheiros Azure NetApp no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) para simplificar a configuração para mapeamento de ID NFS e alterar a interface de rede primária recomendada para simplificar o encaminhamento.
- 11/15/2019: Pequenas alterações na [alta disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server com Ficheiros Azure NetApp para aplicações SAP](high-availability-guide-suse-netapp-files.md) e [Alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux com Ficheiros Azure NetApp para aplicações SAP](high-availability-guide-rhel-netapp-files.md) para clarificar restrições de tamanho de piscina de capacidade e remover declaração de que apenas a versão NFSv3 é suportada.
- 11/12/2019: Lançamento de [alta disponibilidade para SAP NetWeaver no Windows com Ficheiros Azure NetApp (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: Alterações na [alta disponibilidade de SAP HANA em VMs Azure no SUSE Linux Enterprise Server](sap-hana-high-availability.md), Configurar a replicação do sistema [SAP HANA em máquinas virtuais Azure (VMs)](sap-hana-high-availability-rhel.md), [Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server para aplicações SAP](high-availability-guide-suse.md), [Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server com Ficheiros Azure NetApp](high-availability-guide-suse-netapp-files.md), [Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux ](high-availability-guide-rhel.md), [Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux com Ficheiros Azure NetApp](high-availability-guide-rhel-netapp-files.md), [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server,](high-availability-guide-suse-nfs.md) [GlusterFS em VMs Azure em Red Hat Enterprise Linux para SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) para recomendar o equilíbrio de carga padrão Azure  
- 11/08/2019: Alterações na lista de verificação de [planeamento e implementação](sap-deployment-checklist.md) de carga sap para clarificar recomendação de encriptação  
- 11/04/2019: Alterações na configuração do [Pacemaker no SUSE Linux Enterprise Server em Azure](high-availability-guide-suse-pacemaker.md) para criar o cluster diretamente com configuração unicast  
- 10/29/2019: Lançamento da [conectividade de ponto final público para máquinas virtuais utilizando o Equilíbrio de Carga Padrão Azure em cenários de alta disponibilidade do SAP](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: Alterações nas configurações de armazenamento de [máquinas virtuais SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) e [escala SAP HANA com nó de standby em VMs Azure com Ficheiros Azure NetApp no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) para clarificar o protocolo NFS para /hana/volume partilhado
- 10/22/2019: Alteração da [alta disponibilidade para SAP NetWeaver em VMs Azure em SUSE Linux Enterprise Server para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server com Ficheiros Azure NetApp para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) [configuração do Pacemaker no SUSE Linux Enterprise Server em Azure,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) [Alta disponibilidade de IBM Db2 LUW em VMs Azure no SUSE Linux Enterprise Server com Pacemaker,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) e [Alta disponibilidade de SAP HANA em VMs Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) para o endurecimento de deteção de equilíbrio de carga azure
- Altera a secção ANF e a secção de cabeçalho nas configurações de [armazenamento de máquinas virtuais SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/21/2019: Lançamento da [escala SAP HANA com nó de standby em VMs Azure com Ficheiros Azure NetApp em SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: Fixar ligações partidas em [Backup e restaurar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 10/16/2019: Alterar o sistema mínimo recomendado de SLES 12 SP3 para SLES 12 SP4 em [alta disponibilidade de IBM Db2 LUW em VMs Azure no SUSE Linux Enterprise Server com Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 10/11/2019: Alterações às configurações de armazenamento de discos Ultra e introdução de ANF nas configurações de [armazenamento de máquinas virtuais SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: Alteração dos gráficos dos grupos de colocação de proximidade do [Azure para uma latência ótima da rede com aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) para obter mais clareza
- 10/01/2019: Alteração das [configurações e operações de infraestruturas SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) para corrigir declarações em torno da quota nfs altamente disponível para /hana/shared. 
- 09/28/2019: Alteração na criação do [Pacemaker na Red Hat Enterprise Linux em Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) para clarificar a SBD como um mecanismo de esgrima não é suportado em clusters RHEL  
- 09/17/2019: Alteração do Guia de Planeamento e Implementação da NetWeaver para unificar os termos em torno da extensão VM para SAP  
- 08/22/2019: Alterações na configuração do [Pacemaker no SUSE Linux Enterprise Server em Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) para atualizar os URLs para criação de papéis personalizados  
- 08/16/2019: Alterações na criação do [Pacemaker na Red Hat Enterprise Linux em Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) para lembrar os clientes de atualizarem as ações no papel personalizado, se atualizarem para a nova versão do agente da cerca azure  
- 08/15/2019: Alterações nas configurações de [armazenamento de máquinas virtuais SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) para refletir a Disponibilidade Geral do Disco Ultra (ex-Ultra SSD)
- 08/01/2019: Alterações à configuração do [Pacemaker no SUSE Linux Enterprise Server em Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) para integrar alterações especificamente para sles 15 


