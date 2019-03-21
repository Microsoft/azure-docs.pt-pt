---
title: Introdução ao SAP em VMs do Azure | Documentos da Microsoft
description: Saiba mais sobre soluções SAP em execução em máquinas virtuais (VMs) no Microsoft Azure
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
ms.date: 03/015/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 91228074e13453ce53c0b4a7606479d45e8a90ac
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182783"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>Utilizar o Azure para alojar e executar cenários de carga de trabalho SAP

Escolhendo o Microsoft Azure, é possível execute com fiabilidade a sua cenários e SAP cargas de trabalho críticas numa plataforma dimensionável, em conformidade e empresariais comprovadas.  Obtenha a escalabilidade, a flexibilidade e a redução de custos do Azure. Com a parceria entre a Microsoft e a SAP expandida, pode executar aplicações SAP em cenários de desenvolvimento/teste e produção no Azure - e suporte completo. Do SAP NetWeaver ao SAP S/4HANA, BI do SAP, Linux para Windows, o SAP HANA ao SQL, temos de que necessita.

Além de alojar cenários SAP NetWeaver, com diferentes DBMS no Azure, pode alojar diferentes outros cenários de carga de trabalho do SAP, como o BI do SAP no Azure. 

A exclusividade do Azure para o SAP HANA é uma oferta que define o Azure para além de competição. Para permitir o alojamento de mais memória e recursos de CPU, exigência de cenários SAP que envolvem o SAP HANA, o Azure oferece a utilização de cliente dedicado hardware bare-metal com o objetivo de implementações do SAP HANA que necessitem de até 24 TB (Escalamento 120 TB) de memória em execução para S/4HANA ou outras cargas de trabalho do SAP HANA. Esta solução do Azure exclusiva de SAP HANA no Azure (instâncias grandes) permite-lhe executar o SAP HANA no hardware dedicado de bare-metal com a camada de aplicação SAP ou a camada de meio ware de carga de trabalho alojadas no nativo máquinas de virtuais do Azure. Esta solução está documentada em vários documentos na secção "SAP HANA no Azure (instâncias grandes)".   

Cenários de carga de trabalho SAP no Azure de alojamento também podem criar os requisitos de integração de identidade e o início de sessão único com o diretório de atividade do Azure para diferentes componentes do SAP e SAP SaaS ou a PaaS oferece. Uma lista de tais integração e cenários de início de sessão único com entidades do Azure Active Directory (AAD) e a SAP é descrita e documentada na secção "integração de identidade do AAD SAP e início de sessão único."

## <a name="latest-changes"></a>Alterações mais recentes

Versão do [configurações de carga de trabalho SAP com zonas de disponibilidade do Azure](sap-ha-availability-zones.md)

Versão do [SAP carga de trabalho de planejamento e implantação lista de verificação](sap-deployment-checklist.md)

Esclarecimento em torno de utilização e a configuração do [aplicações virtuais de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) em caminhos de comunicação crítica de latência dos sistemas SAP em:

- [Considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Configurações e operações de infraestrutura do SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)



## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA no SAP HANA no Azure (instâncias grandes)

Uma série de documentação orienta-o através de SAP HANA no Azure (instâncias grandes) ou em breve instâncias grandes do HANA. Os documentos abrangem as áreas listadas de instâncias grandes do HANA:

- [Descrição geral do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Arquitetura do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infraestrutura e a conectividade ao SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Instalar o SAP HANA no SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Elevada disponibilidade e recuperação após desastre do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Resolução de problemas e monitorização de SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Passos seguintes:

- Leitura [descrição geral e arquitetura do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA nas Máquinas Virtuais do Azure
Esta secção da documentação abrange a diferentes aspetos do SAP HANA. Como pré-requisito, deve estar familiarizado com os serviços principais do Azure que fornecem serviços elementares de IaaS do Azure, por isso, principalmente o conhecimento de computação do Azure, armazenamento e rede. Muitos desses tópicos são processados no relacionados com o SAP NetWeaver [guia de planejamento de Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

A documentação específica para HANA no Azure é composta por esta lista de artigos e seus subarticles:

- [Quickstart: Instalação manual de instância única SAP HANA em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Implementar o SAP S/4HANA ou BW/4HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Configurações e operações de infraestrutura do SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Elevada disponibilidade de SAP HANA para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Disponibilidade do SAP HANA dentro de uma região do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Disponibilidade do SAP HANA em regiões do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Elevada disponibilidade do SAP HANA em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Guia de segurança para SAP HANA em máquinas de virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Cópia de segurança do SAP HANA do Azure no nível de ficheiro](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Cópia de segurança do SAP HANA com base nos instantâneos de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver implementado em máquinas de virtuais do Azure
Nesta secção, encontrará documentação de planeamento e implementação do SAP NetWeaver e Business One no Azure. A documentação neste capítulo se concentra principalmente em torno das noções básicas e a utilização de bases de dados não HANA com carga de trabalho SAP no Azure. Ao passo que os documentos e artigos para HA são a base para HANA de elevada disponibilidade no Azure também. a lista de artigos, como:

- [SAP Business One em Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Implementar o SAP IDES EHP7 SP3 para SAP ERP 6.0 no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Executar o SAP NetWeaver em VMs do Linux do Microsoft Azure SUSE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Máquinas de virtuais de planeamento e implementação para o SAP NetWeaver do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Implementação de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Proteger uma implementação de aplicação SAP NetWeaver com várias camada com o Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Conector de SAP LaMa para o Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Em relação a bases de dados não HANA sob carga de trabalho SAP no azure, como a lista de documentos:

- [Considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Implementação de DBMS de máquinas virtuais do SQL Server do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Implementação em Oracle do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Implementação em IBM DB2 do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB liveCache e implementação de servidor de conteúdo em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Bases de dados do SAP HANA no Azure, consulte a secção de SAP HANA em máquinas de virtuais do Azure.

Para elevada disponibilidade da carga de trabalho SAP no Azure, o documento de entrada é:

- [Azure máquinas virtuais elevada disponibilidade para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

O documento de entrada aponta para vários outros documentos de arquitetura e o cenário. Em documentos de cenário subsequentes, são fornecidas hiperligações para documentos técnicos detalhadas explicando a implementação e configuração dos métodos diferentes de elevada disponibilidade. Os documentos diferentes de estabelecer e configurar a elevada disponibilidade para a carga de trabalho do SAP NetWeaver são abrangendo Linux, bem como os sistemas operativos do Windows.


Para a integração entre o Azure Active Directory (AAD) e serviços SAP e início de sessão único, os documentos, lista, como:

- [Tutorial: Integração do Active Directory do Azure com o SAP Cloud para o cliente](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Active Directory do Azure com a autenticação de identidade da plataforma de Cloud de SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Active Directory do Azure com o SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Active Directory do Azure com o SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Active Directory do Azure com o SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [O ambiente de S/4HANA – Fiori Launchpad SAML início de sessão único com o Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Para a integração dos serviços do Azure em componentes SAP a lista de documentos é semelhante a:

- [Utilizar o SAP HANA no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery e SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Utilizar o conector SAP BW no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [O Azure Data Factory oferece integração de dados do SAP HANA e Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




