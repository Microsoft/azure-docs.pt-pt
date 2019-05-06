---
title: Introdução ao SAP em VMs do Azure | Documentos da Microsoft
description: Saiba mais sobre soluções SAP, que são executados em máquinas virtuais (VMs) no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/30/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e637b52489bb92b66e185d37db16f7004bf47537
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147752"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Utilize o Azure para alojar e executar cenários de carga de trabalho SAP

Quando utiliza o Microsoft Azure, pode execute com fiabilidade a suas cargas de trabalho SAP e cenários fundamentais numa plataforma dimensionável, em conformidade e empresariais comprovadas. Obtenha a escalabilidade, flexibilidade e redução de custos do Azure. Com a parceria entre a Microsoft e a SAP expandida, pode executar aplicações SAP em cenários de desenvolvimento e teste e produção no Azure e obtenha suporte total. Do SAP NetWeaver ao SAP S/4HANA, BI de SAP em Linux para Windows e SAP HANA ao SQL, temos que necessita.

Além de alojar cenários SAP NetWeaver, com diferentes DBMS no Azure, pode alojar outros cenários de carga de trabalho do SAP, como o BI do SAP no Azure. 

A exclusividade do Azure para o SAP HANA é uma oferta que define-o Azure. Para permitir o alojamento de mais memória e CPU mais exigentes recursos SAP cenários que envolvem o SAP HANA, o Azure oferece a utilização de hardware dedicado de cliente de bare-metal. Utilize esta solução para executar as implementações do SAP HANA que necessitem de até 24 TB (Escalamento 120 TB) de memória para S/4HANA ou outra carga de trabalho do SAP HANA. 

Cenários de carga de trabalho SAP no Azure de alojamento também podem criar requisitos de integração de identidade e início de sessão único. Esta situação pode ocorrer ao utilizar o Azure Active Directory (Azure AD) para ligar diferentes componentes do SAP e SAP software-como-serviço (SaaS) ou ofertas do plataforma-como-serviço (PaaS). Uma lista de tais integração e cenários de início de sessão únicos com o Azure AD e entidades SAP é descrito e documentadas na secção "integração de identidade do AAD SAP e início de sessão único."

## <a name="latest-changes"></a>Alterações mais recentes

- Versão do [instâncias grandes do HANA Azure controle por meio do portal do Azure](hana-li-portal.md)

- Versão do [elevada disponibilidade para SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server com o NetApp serviço ficheiros do Azure para aplicações SAP](high-availability-guide-suse-netapp-files.md)

- Esclarecimentos adicionais relativamente **net.ipv4.tcp_timestamps de parâmetro de SO Linux** Balanceador de carga de definições juntamente com do Azure

- Versão do [configurações de carga de trabalho SAP com zonas de disponibilidade do Azure](sap-ha-availability-zones.md)

- Versão do [SAP carga de trabalho de planejamento e implantação lista de verificação](sap-deployment-checklist.md)




## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA no Azure (Instâncias Grandes)

Uma série de documentos servem como orientação SAP HANA no Azure (instâncias grandes) ou, abreviadamente, instâncias grandes do HANA. Para informações sobre as seguintes áreas de instâncias grandes do HANA, consulte:

- [Descrição geral do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Arquitetura do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infraestrutura e a conectividade ao SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Instalar o SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Elevada disponibilidade e recuperação após desastre do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Resolver problemas e monitorizar o SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Passos seguintes:

- Leitura [descrição geral e arquitetura do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA em máquinas virtuais do Azure
Esta secção da documentação abrange a diferentes aspetos do SAP HANA. Como pré-requisito, deve estar familiarizado com os principais serviços do Azure, que fornecem serviços elementares de IaaS do Azure. Por isso, precisa de conhecimento de computação do Azure, armazenamento e rede. Muitos dos seguintes assuntos são processados no relacionados com SAP NetWeaver [guia de planeamento do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Para informações sobre o HANA no Azure, consulte os artigos seguintes e os respetivos subarticles:

- [Quickstart: Instalação manual de instância única SAP HANA em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Implementar o SAP S/4HANA ou BW/4HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Configurações e operações de infraestrutura do SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Elevada disponibilidade de SAP HANA para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Disponibilidade do SAP HANA dentro de uma região do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Disponibilidade do SAP HANA em regiões do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Elevada disponibilidade do SAP HANA em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Guia de cópia de segurança para SAP HANA em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Cópia de segurança do SAP HANA do Azure no nível de ficheiro](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Cópia de segurança do SAP HANA com base nos instantâneos de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver implementado em máquinas virtuais do Azure
Esta seção apresenta uma lista de documentação de planeamento e implementação do SAP NetWeaver e Business One no Azure. A documentação concentra-se sobre as noções básicas e a utilização de bases de dados não HANA com uma carga de trabalho SAP no Azure. Os documentos e artigos para elevada disponibilidade também são a base para o HANA de elevada disponibilidade no Azure, tais como:

- [SAP Business One em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Implementar o SAP IDES EHP7 SP3 para SAP ERP 6.0 no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Execute o SAP NetWeaver em VMs do Linux do Microsoft Azure SUSE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Máquinas de virtuais de planeamento e implementação para o SAP NetWeaver do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Implementação de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Proteger uma implementação de aplicação SAP NetWeaver com várias camadas com o Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Conector de SAP LaMa para o Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Para obter informações sobre bases de dados não HANA sob uma carga de trabalho SAP no Azure, consulte:

- [Considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Implementação de DBMS de máquinas virtuais do SQL Server do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Implementação em Oracle do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Implementação em IBM DB2 do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Implementação de SAP MaxDB, o Cache em direto e o servidor de conteúdo em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Para obter informações sobre bases de dados do SAP HANA no Azure, consulte a secção "SAP HANA em máquinas virtuais do Azure."

Para obter informações sobre a disponibilidade elevada de uma carga de trabalho SAP no Azure, consulte:

- [Azure máquinas virtuais elevada disponibilidade para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Este documento aponta para vários outros documentos de arquitetura e o cenário. Em documentos do cenário mais adiante, são fornecidas hiperligações para documentos técnicos detalhados que explicam a implementação e configuração de diferentes métodos de elevada disponibilidade. Os documentos diferentes que mostram como estabelecer e configurar a elevada disponibilidade para uma carga de trabalho do SAP NetWeaver sobre sistemas operativos Linux e Windows.


Para obter informações sobre a integração entre o Azure Active Directory (Azure AD) e serviços SAP e início de sessão único, consulte:

- [Tutorial: Integração do Active Directory do Azure com o SAP Cloud para o cliente](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Active Directory do Azure com a autenticação de identidade da plataforma de Cloud de SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Active Directory do Azure com o SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Active Directory do Azure com o SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Active Directory do Azure com o SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [O ambiente de S/4HANA: Fiori Launchpad SAML início de sessão único com o Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Para obter informações sobre a integração de serviços do Azure em componentes SAP, consulte:

- [Utilizar o SAP HANA no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery e SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Utilizar o conector SAP BW no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [O Azure Data Factory oferece integração de dados do SAP HANA e Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




