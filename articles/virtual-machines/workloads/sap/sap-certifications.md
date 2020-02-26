---
title: Certificações Microsoft Azure para SAP / Microsoft Docs
description: Lista atualizada das configurações e certificações atuais do SAP na plataforma Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: 43fc2e9369b9d11b1ac0205beddea01b633fb633
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598379"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certificações e configurações SAP em execução no Microsoft Azure

A SAP e a Microsoft têm um longo historial de trabalhar em conjunto numa parceria forte que tem benefícios mútuos para os seus clientes. A Microsoft está constantemente a atualizar a sua plataforma e a submeter novos detalhes de certificação ao SAP de forma a garantir que o Microsoft Azure é a melhor plataforma para executar as suas cargas de trabalho SAP. As tabelas seguintes descrevem configurações apoiadas pelo Azure e lista de crescentes certificações SAP. 

## <a name="sap-hana-certifications"></a>Certificações SAP HANA
Referências:

- [As plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) sAP HANA para suporte SAP HANA para VMs azure nativos e GRANDES Instâncias HANA.

| Produto SAP | SO Suportados | Ofertas Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (incluindo o software cliente HANA composto por SQLODBC, ODBO-Windows apenas, ODBC, controladores JDBC, estúdio HANA e base de dados HANA) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Família VM série D |
| Business One em HANA | Empresa SUSE Linux | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [Plataformas IaaS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Disponibilidade Controlada para GS5. Suporte total para M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> SAP HANA em Azure (Grandes instâncias) [Plataformas IaaS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suíte em HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA em Azure (Grandes instâncias) [Plataformas IaaS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise para BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA em Azure (Grandes instâncias) [Plataformas IaaS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA em Azure (Grandes instâncias) <br /> [Plataformas IaaS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Esteja ciente de que o SAP utiliza o termo 'clustering' nas [Plataformas IaaS certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) como sinónimo para 'scale-out' e NÃO para 'clustering' de alta disponibilidade

## <a name="sap-netweaver-certifications"></a>Certificações SAP NetWeaver
O Microsoft Azure está certificado para os seguintes produtos SAP, com suporte total da Microsoft e SAP.
Referências:

- [1928533 - Aplicações SAP em Azure: Produtos suportados e tipos de VM Azure](https://launchpad.support.sap.com/#/notes/1928533) para todas as aplicações baseadas em SAP NetWeaver, incluindo SAP TREX, SAP LiveCache e SAP Content Server. E todas as bases de dados, excluindo o SAP HANA.


| Produto SAP | SO convidado | RDBMS | Tipos de máquinas virtuais |
| --- | --- | --- | --- |
| Software de suite de negócios SAP | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (apenas Windows e Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, D2s_v3 a D64s_v3, D2as_v4 a D64as_v4, E2s_v3 a E64s_v3, E2as_v4 a E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 M208ms_v2, M416s_v2, M416ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (apenas Windows e Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, D2s_v3 a D64s_v3, D2as_v4 a D64as_v4, E2s_v3 a E64s_v3, E2as_v4 a E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 M208ms_v2, M416s_v2, M416ms_v2 |
| SAP BusinessObjects BI | Windows |N/D |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, D2s_v3 a D64s_v3, D2as_v4 a D64as_v4, E2s_v3 a E64s_v3, E2as_v4 a E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 M208ms_v2, M416s_v2, M416ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (apenas Windows e Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, D2s_v3 a D64s_v3, D2as_v4 a D64as_v4, E2s_v3 a E64s_v3, E2as_v4 a E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 M208ms_v2, M416s_v2, M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Outracarga de trabalho SAP suportada no Azure

| Produto SAP | SO convidado | RDBMS | Tipos de máquinas virtuais |
| --- | --- | --- | --- |
| SAP Business One no Servidor SQL | Windows  | SQL Server | Todos os tipos de VM certificados netWeaver<br /> [Nota SAP #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows e Linux | | Todos os tipos de VM certificados NetWeaver<br /> Nota SAP #2451795 |
| Plataforma BI de Objetos Empresariais SAP | Windows e Linux | | Nota SAP #2145537 |
| Serviços de Dados SAP 4.2 | | | Nota SAP #2288344 |
| Plataforma de Comércio SAP Hybris 5.x e 6.x | Windows | SQL Server, Oracle | Todos os tipos de VM certificados netWeaver<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
