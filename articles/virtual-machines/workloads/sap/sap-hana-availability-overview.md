---
title: Disponibilidade do SAP HANA em VMs Azure - Visão geral Microsoft Docs
description: Descreve as operações DA SAP HANA em VMs nativos de Azure.
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
ms.openlocfilehash: 3589b4af06bc27a79c429bb0ccd743857651db97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653550"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA alta disponibilidade para máquinas virtuais Azure

Você pode usar inúmeras capacidades Azure para implementar bases de dados críticas da missão como SAP HANA em VMs Azure. Este artigo fornece orientações sobre como alcançar a disponibilidade para os casos SAP HANA que são hospedados em VMs Azure. O artigo descreve vários cenários que pode implementar utilizando a infraestrutura Azure para aumentar a disponibilidade de SAP HANA em Azure. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que está familiarizado com a infraestrutura como um básico de serviço (IaaS) em Azure, incluindo: 

- Como implementar máquinas virtuais ou redes virtuais através do portal Azure ou PowerShell.
- Utilizando a interface de linha de comando cross-platform (Azure CLI), incluindo a opção de utilizar os modelos javaScript Object Notation (JSON).

Este artigo também assume que você está familiarizado com a instalação de casos SAP HANA, e com administração e funcionamento de casos SAP HANA. É especialmente importante estar familiarizado com a configuração e operações da replicação do sistema HANA. Isto inclui tarefas como backup e restauro para bases de dados SAP HANA.

Estes artigos fornecem uma boa visão geral da utilização do SAP HANA em Azure:

- [Instalação manual de SAP HANA de instância única em VMs Azure](./hana-get-started.md)
- [Configurar a replicação do sistema SAP HANA em VMs Azure](sap-hana-high-availability.md)
- [Fazer a cópia de segurança das VMs do SAP HANA no Azure](./sap-hana-backup-guide.md)

Também é uma boa ideia estar familiarizado com estes artigos sobre SAP HANA:

- [Alta disponibilidade para SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [FAQ: Alta disponibilidade para SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Executar a replicação do sistema para SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 Novidades: Alta disponibilidade](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Recomendações de rede para a replicação do sistema SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replicação do sistema SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [Serviço SAP HANA reinicia automaticamente](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Configurar a replicação do sistema SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Além de estar familiarizado com a implementação de VMs em Azure, antes de definir a sua arquitetura de disponibilidade em Azure, recomendamos que leia [Gerir a disponibilidade de máquinas virtuais windows em Azure.](../../windows/manage-availability.md)

## <a name="service-level-agreements-for-azure-components"></a>Acordos de nível de serviço para componentes Azure

O Azure tem diferentes SLAs de disponibilidade para diferentes componentes, como networking, armazenamento e VMs. Todos os SLAs estão documentados. Para obter mais informações, consulte [os Acordos de Nível de Serviço do Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). 

[SLA for Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) descreve três SLAs diferentes, para três configurações diferentes:

- Um único VM que utiliza [SSDs premium Azure](../../managed-disks-overview.md) para o disco de SO e todos os discos de dados. Esta opção proporciona um uptime mensal de 99,9%.
- Múltiplos (pelo menos dois) VMs que são organizados num [conjunto de disponibilidades Azure](../../windows/tutorial-availability-sets.md). Esta opção proporciona um uptime mensal de 99,95 por cento.
- Múltiplos (pelo menos dois) VMs que são organizados numa [Zona de Availablity](../../../availability-zones/az-overview.md). Esta opção proporcionou um aumento mensal de 99,99%.

Meça o seu requisito de disponibilidade contra os SLAs que os componentes Azure podem fornecer. Em seguida, escolha os seus cenários para SAP HANA para atingir o nível de disponibilidade exigido.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a disponibilidade do SAP HANA dentro de uma região de Azure.](./sap-hana-availability-one-region.md)
- Saiba mais sobre [a disponibilidade do SAP HANA nas regiões de Azure.](./sap-hana-availability-across-regions.md) 















  
