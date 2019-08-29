---
title: Microsoft Azure certificações para SAP | Microsoft Docs
description: Lista atualizada de configurações e certificações atuais do SAP na plataforma Azure.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: 38e63d8ca7d9db5247bd5fe07fbe59452a106cf6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098657"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certificações e configurações do SAP em execução no Microsoft Azure

A SAP e a Microsoft têm uma longa história de trabalhar em conjunto em uma parceria forte que tem benefícios mútuos para seus clientes. A Microsoft está constantemente atualizando sua plataforma e enviando novos detalhes de certificação para o SAP a fim de garantir Microsoft Azure é a melhor plataforma na qual executar suas cargas de trabalho do SAP. As tabelas a seguir descrevem as configurações com suporte do Azure e a lista de certificações SAP crescentes. 

## <a name="sap-hana-certifications"></a>Certificações SAP HANA
Referências

- [SAP Hana plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) para SAP Hana suporte para VMs do Azure nativas e instâncias grandes do Hana.

| Produto SAP | SO Suportados | Ofertas do Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (incluindo o software cliente HANA composto por SQLODBC, ODBO -Windows somente, ODBC, drivers JDBC, HANA Studio e banco de dados HANA) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Família de VMs da série D |
| Business One no HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Disponibilidade controlada para GS5. Suporte completo para M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA no Azure (instâncias grandes) [SAP Hana plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite no HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, SAP HANA no Azure (instâncias grandes) <br /> [SAP HANA plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise para BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA no Azure (instâncias grandes) [SAP Hana plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA no Azure (Instâncias Grandes) <br /> [SAP HANA plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Lembre-se de que o SAP usa o termo "clustering" em [SAP Hana plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) como sinônimo para "expansão" e não para "clustering" de alta disponibilidade

## <a name="sap-netweaver-certifications"></a>Certificações SAP NetWeaver
O Microsoft Azure está certificado para os seguintes produtos SAP, com suporte total da Microsoft e da SAP.
Referências

- [1928533-aplicativos SAP no Azure: Produtos com suporte e tipos](https://launchpad.support.sap.com/#/notes/1928533) de VM do Azure para todos os aplicativos baseados no SAP NetWeaver, incluindo SAP Trex, SAP LiveCache e SAP Content Server. E todos os bancos de dados, exceto SAP HANA.


| Produto SAP | SO convidado | RDBMS | Tipos de Máquina Virtual |
| --- | --- | --- | --- |
| Software SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (somente Windows e Oracle Linux), DB2, SAP ASE |A5 a a11, D11 a D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, D2s_v3 para D64s_v3, E2s_v3 para E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (somente Windows e Oracle Linux), DB2, SAP ASE |A5 a a11, D11 a D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, D2s_v3 para D64s_v3, E2s_v3 para E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP BusinessObjects BI | Windows |N/A |A5 a a11, D11 a D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, D2s_v3 para D64s_v3, E2s_v3 para E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (somente Windows e Oracle Linux), DB2, SAP ASE |A5 a a11, D11 a D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, D2s_v3 para D64s_v3, E2s_v3 para E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Outras cargas de trabalho do SAP com suporte no Azure

| Produto SAP | SO convidado | RDBMS | Tipos de Máquina Virtual |
| --- | --- | --- | --- |
| SAP Business One em SQL Server | Windows  | SQL Server | Todos os tipos de VM certificados NetWeaver<br /> [Observação SAP #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10, 1 MS SP08 | Windows e Linux | | Todos os tipos de VM certificados NetWeaver<br /> Observação SAP #2451795 |
| Plataforma de BI do SAP Business Objects | Windows e Linux | | Observação SAP #2145537 |
| Serviços de dados do SAP 4,2 | | | Observação SAP #2288344 |
| Plataforma SAP Hybris Commerce 5. x e 6. x | Windows | SQL Server, Oracle | Todos os tipos de VM certificados NetWeaver<br /> [Hybris wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
