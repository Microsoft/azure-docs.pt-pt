---
title: Certificações Microsoft Azure para | SAP Microsoft Docs
description: Lista atualizada das configurações e certificações atuais do SAP na plataforma Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: 76f67204b41a36d10541974e78a22f7d065e144b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669699"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certificações e configurações SAP em execução no Microsoft Azure

A SAP e a Microsoft têm um longo historial de trabalhar em conjunto numa parceria forte que tem benefícios mútuos para os seus clientes. A Microsoft está constantemente a atualizar a sua plataforma e a enviar novos detalhes de certificação para o SAP, de forma a garantir que o Microsoft Azure é a melhor plataforma para executar as suas cargas de trabalho SAP. As tabelas que se seguem descrevem as configurações suportadas pelo Azure e a lista de certificações SAP em crescimento. Esta lista é uma lista geral que pode desviar-se aqui e ali das listas oficiais do SAP. Como chegar aos dados detalhados é documentado no artigo [O software SAP é suportado para implementações do Azure](./sap-supported-product-on-azure.md)

## <a name="sap-hana-certifications"></a>Certificações SAP HANA
Referências:

- [A SAP HANA certificou as plataformas IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) para suporte SAP HANA para VMs nativos Azure e HANA Large Instances.

| Produto SAP | SOs Suportados | Ofertas Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (incluindo o software do cliente HANA composto por SQLODBC, ODBO-Windows apenas, ODBC, motoristas JDBC, estúdio HANA e base de dados HANA) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Família VM série D |
| Negócio Um em HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [Plataformas IAAS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Disponibilidade controlada para GS5. Suporte total para M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> SAP HANA em Azure (Grandes instâncias) [PLATAFORMAS IAAS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suíte em HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA em Azure (Grandes instâncias) [Plataformas IAAS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise para BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA em Azure (Grandes instâncias) [Plataformas IAAS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA em Azure (Grandes instâncias) <br /> [Plataformas IAAS Certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Esteja ciente de que o SAP utiliza o termo 'clustering' nas [plataformas IAAS certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) como sinónimo de 'scale-out' e NÃO para "clustering" de alta disponibilidade

## <a name="sap-netweaver-certifications"></a>Certificações SAP NetWeaver
O Microsoft Azure é certificado para os seguintes produtos SAP, com suporte total da Microsoft e SAP.
Referências:

- [1928533 - Aplicações SAP em Azure: Produtos Suportados e Tipos de VM Azure](https://launchpad.support.sap.com/#/notes/1928533) para todas as aplicações baseadas em SAP NetWeaver, incluindo SAP TREX, SAP LiveCache e SAP Content Server. E todas as bases de dados, excluindo a SAP HANA.


| Produto SAP | SO convidado | RDBMS | Tipos de Máquinas Virtuais |
| --- | --- | --- | --- |
| SAP Business Suite Software | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oráculo (apenas Windows e Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, D2s_v3 a D64s_v3, D2as_v4 a D64as_v4, E2s_v3 a E64s_v3, E2as_v4 a E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 M416s_v2, M416s_v2 M416ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oráculo (apenas Windows e Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, D2s_v3 a D64s_v3, D2as_v4 a D64as_v4, E2s_v3 a E64s_v3, E2as_v4 a E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 M416s_v2, M416s_v2 M416ms_v2 |
| SAP BusinessObjects BI | Windows |N/D |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, D2s_v3 a D64s_v3, D2as_v4 a D64as_v4, E2s_v3 a E64s_v3, E2as_v4 a E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 M416s_v2, M416s_v2 M416ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oráculo (apenas Windows e Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, D2s_v3 a D64s_v3, D2as_v4 a D64as_v4, E2s_v3 a E64s_v3, E2as_v4 a E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 M416s_v2, M416s_v2 M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Outras cargas de trabalho da SAP suportadas no Azure

| Produto SAP | SO convidado | RDBMS | Tipos de Máquinas Virtuais |
| --- | --- | --- | --- |
| SAP Business One no SQL Server | Windows  | SQL Server | Todos os tipos de VM certificados da NetWeaver<br /> [Nota SAP #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows e Linux | | Todos os tipos de VM certificados netWeaver<br /> #2451795 de nota SAP |
| Plataforma BI DE Objetos Empresariais SAP | Windows e Linux | | Nota SAP #2145537 |
| Serviços de Dados SAP 4.2 | | | #2288344 de nota SAP |
| Plataforma de Comércio SAP Hybris  | Windows | SQL Server | Todos os tipos de VM certificados da NetWeaver <br /> [Documentação hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| Plataforma de Comércio SAP Hybris  | SLES 12 ou mais recente | SAP HANA | Todos os tipos de VM certificados da NetWeaver <br /> [Documentação hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| Plataforma de Comércio SAP Hybris  | RHEL 7 ou mais recente | SAP HANA | Todos os tipos de VM certificados da NetWeaver <br /> [Documentação Hybris]https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| PLATAFORMA DE Comércio SAP (Hybris) 1811 e mais tarde  | Windows, SleS ou RHEL | SQL Azure DB | Todos os tipos de VM certificados da NetWeaver <br /> [Documentação hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
