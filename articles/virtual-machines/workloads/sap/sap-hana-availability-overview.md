---
title: Disponibilidade de SAP HANA em VMs do Azure – visão geral | Microsoft Docs
description: Descreve SAP HANA operações em VMs nativas do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bccaf45cf617bd31a584b6c73f3dd08877bc8587
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266066"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA alta disponibilidade para máquinas virtuais do Azure

Você pode usar vários recursos do Azure para implantar bancos de dados de missão crítica, como SAP HANA em VMs do Azure. Este artigo fornece orientação sobre como obter disponibilidade para SAP HANA instâncias que são hospedadas em VMs do Azure. O artigo descreve vários cenários que você pode implementar usando a infraestrutura do Azure para aumentar a disponibilidade de SAP HANA no Azure. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com noções básicas de IaaS (infraestrutura como serviço) no Azure, incluindo: 

- Como implantar máquinas virtuais ou redes virtuais por meio do portal do Azure ou do PowerShell.
- Usando a interface de linha de comando de plataforma cruzada do Azure (CLI do Azure), incluindo a opção de usar os modelos de JavaScript Object Notation (JSON).

Este artigo também pressupõe que você esteja familiarizado com a instalação de instâncias do SAP HANA e com a administração e a operação de SAP HANA instâncias. É especialmente importante estar familiarizado com a instalação e as operações da replicação do sistema HANA. Isso inclui tarefas como backup e restauração para bancos de dados SAP HANA.

Esses artigos fornecem uma boa visão geral do uso de SAP HANA no Azure:

- [Instalação manual de SAP HANA de instância única em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Configurar a replicação do sistema SAP HANA em VMs do Azure](sap-hana-high-availability.md)
- [Fazer backup de SAP HANA em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Também é uma boa ideia estar familiarizado com estes artigos sobre SAP HANA:

- [Alta disponibilidade para SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Perguntas Frequentes: Alta disponibilidade para SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Executar a replicação do sistema para SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2,0 SPS 01 o que há de novo: Alta disponibilidade](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Recomendações de rede para replicação de sistema SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replicação do sistema SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [Reinicialização automática do serviço de SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Configurar a replicação do sistema SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Além de estar familiarizado com a implantação de VMs no Azure, antes de definir sua arquitetura de disponibilidade no Azure, recomendamos que você leia [gerenciar a disponibilidade de máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="service-level-agreements-for-azure-components"></a>Contratos de nível de serviço para componentes do Azure

O Azure tem diferentes SLAs de disponibilidade para diferentes componentes, como rede, armazenamento e VMs. Todos os SLAs são documentados. Para obter mais informações, consulte [Microsoft Azure contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/). 

O [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) descreve três diferentes SLAs, para três configurações diferentes:

- Uma única VM que usa o [SSDs Premium do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) para o disco do sistema operacional e todos os discos de dados. Essa opção fornece um tempo de atividade mensal de 99,9%.
- Várias VMs (pelo menos duas) que são organizadas em um [conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Essa opção fornece um tempo de atividade mensal de 99,95%.
- Várias VMs (pelo menos duas) que são organizadas em uma [zona de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Essa opção forneceu um tempo de atividade mensal de 99,99%.

Meça seu requisito de disponibilidade em relação aos SLAs que os componentes do Azure podem fornecer. Em seguida, escolha seus cenários para SAP HANA obter o nível de disponibilidade necessário.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [disponibilidade de SAP Hana em uma região do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region).
- Saiba mais sobre a [disponibilidade de SAP Hana nas regiões do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions). 















  


