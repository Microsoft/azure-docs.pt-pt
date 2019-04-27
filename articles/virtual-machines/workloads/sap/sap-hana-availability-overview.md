---
title: Disponibilidade do SAP HANA em VMs do Azure - descrição geral | Documentos da Microsoft
description: Descreve as operações de SAP HANA em VMs nativas do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1db56ad31991b85ffad415818c7c67f0ee30808d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708452"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>Elevada disponibilidade de SAP HANA para máquinas virtuais do Azure

Pode usar várias capacidades do Azure para implementar bases de dados vitais, como o SAP HANA em VMs do Azure. Este artigo fornece orientações sobre como alcançar a disponibilidade para SAP HANA nas instâncias que estão alojados em VMs do Azure. O artigo descreve os vários cenários que pode implementar utilizando a infraestrutura do Azure para aumentar a disponibilidade do SAP HANA no Azure. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que está familiarizado com a infraestrutura como um Noções básicas do serviço (IaaS) no Azure, incluindo: 

- Como implementar máquinas virtuais ou redes virtuais através do portal do Azure ou o PowerShell.
- Usando a interface do Azure para várias plataformas da linha de comandos (CLI) do Azure, incluindo a opção para utilizar modelos de JavaScript Object Notation (JSON).

Este artigo também presume que esteja familiarizado com a instalação de instâncias do SAP HANA e com administração e operação do SAP HANA nas instâncias. É especialmente importante estar familiarizado com a configuração e as operações de replicação do sistema HANA. Isto inclui tarefas como cópia de segurança e restauro para bases de dados do SAP HANA.

Estes artigos fornecem uma boa visão geral de como utilizar o SAP HANA no Azure:

- [Instalação manual de instância única SAP HANA em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Configurar a replicação de sistema de SAP HANA em VMs do Azure](sap-hana-high-availability.md)
- [Criar cópias de segurança SAP HANA em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Também é uma boa ideia estar familiarizado com estes artigos sobre o SAP HANA:

- [Elevada disponibilidade para SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Perguntas Frequentes: Elevada disponibilidade para SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Efetuar a replicação de sistema para o SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 que há de novo: elevada disponibilidade](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Recomendações de rede para replicação do sistema de SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replicação do sistema de SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [Auto-reiniciar o serviço do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Configurar a replicação de sistema do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Para além de se familiarizar com a implementação de VMs no Azure, antes de definir a arquitetura de disponibilidade no Azure, recomendamos que leia [gerir a disponibilidade das máquinas de virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="service-level-agreements-for-azure-components"></a>Contratos de nível de serviço para componentes do Azure

O Azure tem as SLAs de disponibilidade diferentes para diferentes componentes, como o sistema de rede, armazenamento e VMs. Todos os SLAs são documentados. Para obter mais informações, consulte [contratos de nível de serviço do Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). 

[SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) descreve dois SLAs diferentes, para duas configurações diferentes:

- Uma única VM que utiliza [premium do Azure SSDs](../../windows/disks-types.md) para o disco do SO e todos os discos de dados. Esta opção fornece um tempo de atividade mensal de 99,9 por cento.
- Várias VMs (pelo menos dois) que estão organizados numa [conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Esta opção fornece um tempo de atividade mensal de 99,95 por cento.

Meça seus requisitos de disponibilidade contra os SLAs que podem fornecer componentes do Azure. Em seguida, selecione os cenários para o SAP HANA alcançar o nível de disponibilidade.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [disponibilidade de SAP HANA dentro de uma região do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region).
- Saiba mais sobre [disponibilidade de SAP HANA em regiões do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions). 















  


