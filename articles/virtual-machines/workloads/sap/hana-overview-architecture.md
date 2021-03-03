---
title: Visão geral do SAP HANA em Azure (Grandes Instâncias) | Microsoft Docs
description: Visão geral de como implantar o SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/04/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c1cd4e7d65897634b5a8a8fa8be46275bbd4b88
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676876"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>O que é SAP HANA nas Instâncias Grandes do Azure?

SAP HANA on Azure (Grandes Instâncias) é uma solução única para a Azure. Além de fornecer máquinas virtuais para implantar e executar o SAP HANA, o Azure oferece-lhe a possibilidade de executar e implantar o SAP HANA em servidores de metal nu que lhe são dedicados. A solução SAP HANA on Azure (Large Instances) baseia-se num hardware de suporte de metal não partilhado de anfitriões/servidor que lhe é atribuído. O hardware do servidor está incorporado em selos maiores que contêm computação/servidor, rede e infraestrutura de armazenamento. O SAP HANA on Azure (Grandes Instâncias) oferece diferentes SKUs ou tamanhos de servidor. As unidades podem ter 36 núcleos intel CPU e 768 GB de memória e ir até unidades que têm até 480 núcleos intel CPU e até 24 TB de memória.

O isolamento do cliente dentro do carimbo da infraestrutura é realizado em inquilinos, que parece:

- **Networking**: Isolamento dos clientes dentro da pilha de infraestruturas através de redes virtuais por cliente designado inquilino. Um inquilino é designado para um único cliente. Um cliente pode ter vários inquilinos. O isolamento da rede de inquilinos proíbe a comunicação em rede entre inquilinos ao nível do carimbo da infraestrutura, mesmo que os inquilinos pertençam ao mesmo cliente.
- **Componentes de armazenamento**: Isolamento através de máquinas virtuais de armazenamento que tenham volumes de armazenamento atribuídos a eles. Os volumes de armazenamento só podem ser atribuídos a uma máquina virtual de armazenamento. Uma máquina virtual de armazenamento é atribuída exclusivamente a um único inquilino na pilha de infraestruturas. Como resultado, os volumes de armazenamento atribuídos a uma máquina virtual de armazenamento podem ser acedidos apenas em um inquilino específico e relacionado. Não são visíveis entre os diferentes inquilinos.
- **Servidor ou anfitrião**: Um servidor ou unidade de anfitrião não é partilhado entre clientes ou inquilinos. Um servidor ou hospedeiro implantado para um cliente é uma unidade de computação atómica de metal nu que é atribuída a um único inquilino. *Não* é utilizada nenhuma partição de hardware ou divisórias suaves que possam resultar na partilha de um hospedeiro ou de um servidor com outro cliente. Os volumes de armazenamento atribuídos à máquina virtual de armazenamento do inquilino específico são montados num tal servidor. Um inquilino pode ter uma para muitas unidades de servidor de diferentes SKUs exclusivamente atribuídas.
- Dentro de um carimbo de infraestrutura SAP HANA em Azure (Grandes Instâncias), muitos inquilinos diferentes são implantados e isolados uns contra os outros através dos conceitos de inquilino em networking, armazenamento e nível de cálculo. 


Estas unidades de servidor de metal nu são suportadas apenas para executar SAP HANA. A camada de aplicação SAP ou a camada de trabalho de ware médio funciona em máquinas virtuais. Os carimbos de infraestrutura que executam as unidades SAP HANA em Azure (Grandes Instâncias) estão ligados às espinhas traseiras dos serviços de rede Azure. Desta forma, é fornecida uma conectividade de baixa latência entre unidades SAP HANA em Azure (Grandes Instâncias) e máquinas virtuais.

A partir de janeiro de 2021, diferenciamos entre duas revisões diferentes de selos HANA Large Instance e localização de implantações:

- "Revisão 3" (Rev 3): São os selos que foram disponibilizados para o cliente implementar antes de julho de 2019
- "Revisão 4" (Rev 4): Novo desenho de selos que é implantado nas proximidades dos anfitriões Azure VM e que até agora são lançados nas regiões de Azure de:
    -  E.U.A. Oeste 2 
    -  E.U.A. Leste
    -  East US2 (em duas zonas de disponibilidade)
    -  South Central US (em duas zonas de disponibilidade)
    -  Europa Ocidental
    -  Europa do Norte


Este documento é um dos vários documentos que cobrem o SAP HANA em Azure (Grandes Instâncias). Este documento introduz a arquitetura, responsabilidades e serviços básicos prestados pela solução. São também discutidas capacidades de alto nível da solução. Para a maioria das outras áreas, como o networking e a conectividade, quatro outros documentos cobrem detalhes e informações de perfuração. A documentação do SAP HANA on Azure (Grandes Instâncias) não abrange aspetos da instalação ou implantação do SAP NetWeaver em VMs. O SAP NetWeaver on Azure está coberto por documentos separados encontrados no mesmo recipiente de documentação Azure. 


Os diferentes documentos da orientação hana large instance abrangem as seguintes áreas:

- [Visão geral e arquitetura em Azure](hana-overview-architecture.md)
- [Infraestrutura e conectividade SAP HANA (Grandes Instâncias) em Azure](hana-overview-infrastructure-connectivity.md)
- [Instalar e configurar SAP HANA (Grandes Instâncias) em Azure](hana-installation.md)
- [SAP HANA (Grandes Instâncias) alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md)
- [SAP HANA (Grandes Instâncias) resolução de problemas e monitorização em Azure](troubleshooting-monitoring.md)
- [Alta disponibilidade configurada em SUSE utilizando o STONITH](./ha-setup-with-stonith.md)
- [Backup de SO e restauro para SKUs tipo II de revisão 3 selos](./os-backup-type-ii-skus.md)
- [Poupe no SAP HANA nas Instâncias Grandes com uma reserva do Azure](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md)

**Próximos passos**
- Consulte [Conheça os termos](hana-know-terms.md)
