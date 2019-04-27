---
title: Certificações do Microsoft Azure para SAP | Documentos da Microsoft
description: Lista atualizada das configurações atuais e certificações de SAP na plataforma do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: de282041066e18a52d596f051def598eef3d458d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648929"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certificações SAP e configurações em execução no Microsoft Azure

SAP e a Microsoft tem uma longa história de trabalhar em conjunto numa forte parceria que tem benefícios mútuos para seus clientes. Microsoft está constantemente a atualizar a sua plataforma e submeter novos detalhes de certificação para o SAP para garantir que o Microsoft Azure é a melhor plataforma para executar as suas cargas de trabalho do SAP. Lista de certificações SAP a crescer e de configurações suportadas a seguinte estrutura de tabelas do Azure. 

## <a name="sap-hana-certifications"></a>Certificações SAP HANA
Referências:

- [Plataformas de IaaS com certificação SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) para suporte de SAP HANA para VMs do Azure nativo e instâncias grandes do HANA.

| Produto SAP | SO Suportados | Ofertas do Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (incluindo o software HANA client, constituído por SQLODBC, ODBO-Windows apenas, ODBC, controladores JDBC, HANA studio e HANA database) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Família de VM da série D |
| HANA num de negócios | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA certificadas plataformas IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Disponibilidade controlada para GS5. Suporte completo para M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, <br /> SAP HANA no Azure (instâncias grandes) [plataformas de IaaS com certificação do SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite no HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, SAP HANA no Azure (instâncias grandes) <br /> [SAP HANA certificadas plataformas IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise para BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, <br /> SAP HANA no Azure (instâncias grandes) [plataformas de IaaS com certificação do SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, <br /> SAP HANA no Azure (Instâncias Grandes) <br /> [SAP HANA certificadas plataformas IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Lembre-se de que o SAP utiliza o termo "clustering" na [plataformas de IaaS com certificação do SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) como sinónimos para 'Horizontal' e não para elevada disponibilidade "clustering"

## <a name="sap-netweaver-certifications"></a>Certificações SAP NetWeaver
O Microsoft Azure está certificado para os seguintes produtos SAP, com suporte total da Microsoft e da SAP.
Referências:

- [1928533 – SAP aplicações no Azure: Produtos suportados e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533) para todas as aplicações baseadas em SAP NetWeaver, incluindo SAP TREX, SAP LiveCache e servidor de conteúdo de SAP. E todas as bases de dados, excluindo o SAP HANA.


| Produto SAP | SO convidado | RDBMS | Tipos de Máquina Virtual |
| --- | --- | --- | --- |
| Software SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows e apenas o Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 para DS15_v2, GS1 a GS5, D2s_v3 para D64s_v3, E2s_v3 E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows e apenas o Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 para DS15_v2, GS1 a GS5, D2s_v3 para D64s_v3, E2s_v3 E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts |
| SAP BusinessObjects BI | Windows |N/A |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 para DS15_v2, GS1 a GS5, D2s_v3 para D64s_v3, E2s_v3 E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows e apenas o Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 para DS15_v2, GS1 a GS5, D2s_v3 para D64s_v3, E2s_v3 E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts |

## <a name="other-sap-workload-supported-on-azure"></a>Outras cargas de trabalho de SAP suportados no Azure

| Produto SAP | SO convidado | RDBMS | Tipos de Máquina Virtual |
| --- | --- | --- | --- |
| SAP Business um no SQL Server | Windows  | SQL Server | Certificação de NetWeaver todos os tipos de VM<br /> [Nota SAP #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows e Linux | | Todos os tipos de VM de Certified NetWeaver<br /> Nota SAP #2451795 |
| Plataforma de BI de objetos de negócios do SAP | Windows e Linux | | Nota SAP #2145537 |
| Serviços de dados SAP 4.2 | | | Nota SAP #2288344 |
| SAP Hybris Commerce Platform 5.x e 6.x | Windows | SQL Server, Oracle | Certificação de NetWeaver todos os tipos de VM<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
