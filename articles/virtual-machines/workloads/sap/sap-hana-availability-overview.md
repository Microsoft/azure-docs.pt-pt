---
title: "SAP HANA disponibilidade em VMs do Azure - descrição geral | Microsoft Docs"
description: "Operações de SAP HANA em VMs nativas do Azure"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: msjuergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b22f89750234a4715d2b7fd2df6ad8740b1d085
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-high-availability-guide-for-azure-virtual-machines"></a>Guia de SAP HANA elevada disponibilidade para máquinas virtuais do Azure
O Azure oferece várias capacidades que lhe permite implementar o missão críticos as bases de dados, como o SAP HANA em VMs do Azure. Este documento fornece orientações sobre como obter uma disponibilidade para as instâncias de SAP HANA estão alojados em Azure Virtual Machines. No documento, são descritos vários cenários que podem ser implementados na infraestrutura do Azure para aumentar a disponibilidade de SAP HANA no Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Este guia pressupõe que está familiarizado com essa infraestrutura como um Noções básicas de serviço (IaaS) no Azure como: 

- Como implementar máquinas virtuais e as redes virtuais através do portal do Azure ou do PowerShell.
- A interface do Azure plataforma da linha de comandos (CLI), incluindo a opção para utilizar modelos de JavaScript Object Notation (JSON).

Este guia também parte do princípio de que está familiarizado com a instalação instâncias de SAP HANA e administrating e a funcionar de instâncias de SAP HANA. Configurar especialmente e operações em torno da replicação do sistema HANA ou tarefas como a cópia de segurança e restauro de bases de dados SAP HANA.

Outros artigos que dê uma boa descrição geral de tópicos de SAP HANA no Azure são:

- [Instalação manual de instância única SAP HANA em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Configurar a replicação do sistema de SAP HANA em VMs do Azure](sap-hana-high-availability.md)
- [Cópia de segurança manual para SAP HANA em Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Artigo e deve estar familiarizado sobre SAP HANA de conteúdo podem ser apresentados como:

- [Elevada disponibilidade para SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [FAQ: Elevada disponibilidade para SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Como efetuar a replicação do sistema de SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 novidades: elevada disponibilidade](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Recomendações de rede para a replicação do sistema de SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replicação do sistema de SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA serviço reinício automático](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Configurar a replicação do sistema de SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)


Para além de ser familiarizado com a implementação de VMs no Azure, é também recomendado ler o artigo [gerir a disponibilidade das virtual machines Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) primeiro antes de continuar com a definição da arquitetura de disponibilidade no Azure.

## <a name="service-level-agreements-for-different-azure-components"></a>Contratos de nível de serviço de diferentes componentes do Azure
O Azure tem disponibilidade diferente SLAs de diferentes componentes do funcionamento em rede, armazenamento e VMs. Todas estas SLAs estão documentados e podem ser encontrados começando com o [contrato de nível de serviço do Microsoft Azure](https://azure.microsoft.com/support/legal/sla/) página. Se a verificação de a [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), tenha em atenção que o Azure oferece dois SLAs diferentes com duas configurações diferentes. Os SLAs são definidos como:

- Uma única VM utilizando [Premium Storage do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) para o SO disco e todos os discos de dados fornece uma percentagem de tempo da cópia de segurança mensal de 99,9%
- Várias VMs (pelo menos dois) que estão organizados por um [do conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) fornecem uma percentagem de tempo da cópia de segurança mensal de 99,95%

O requisito de disponibilidade contra os componentes de SLAs Azure pode fornecer e, em seguida, decidir sobre os diferentes cenários necessita de implementar com SAP HANA para conseguir a disponibilidade de medida necessitam para fornecer.

## <a name="next-steps"></a>Passos Seguintes
Continue para os documentos:

- [SAP HANA disponibilidade dentro de uma região do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [SAP HANA disponibilidade em regiões do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


