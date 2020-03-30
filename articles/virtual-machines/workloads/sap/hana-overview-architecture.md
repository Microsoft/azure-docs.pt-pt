---
title: Visão geral do SAP HANA em Azure (Grandes Instâncias) / Microsoft Docs
description: Visão geral de como implantar o SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 39fcf5d0fe2273c4debd3ae5ebe5fd1190ddc959
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616959"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>O que é SAP HANA nas Instâncias Grandes do Azure?

SAP HANA on Azure (Grandes Instâncias) é uma solução única para o Azure. Além de fornecer máquinas virtuais para a implementação e execução do SAP HANA, o Azure oferece-lhe a possibilidade de executar e implantar o SAP HANA em servidores de metal nu que lhe são dedicados. A solução SAP HANA on Azure (Grandes Instâncias) baseia-se em hardware de suporte/servidor não partilhado que lhe é atribuído. O hardware do servidor está incorporado em selos maiores que contêm computação/servidor, networking e infraestrutura de armazenamento. Como combinação, é certificada a integração de centros de dados (TDI) à medida da HANA. O SAP HANA no Azure (Grandes Instâncias) oferece diferentes SKUs ou tamanhos do servidor. As unidades podem ter 36 núcleos intel CPU e 768 GB de memória e ir até unidades que têm até 480 núcleos de CPU Intel e até 24 TB de memória.

O isolamento do cliente dentro do carimbo de infraestrutura é realizado em inquilinos, que parece:

- **Networking**: Isolamento dos clientes dentro da pilha de infraestruturas através de redes virtuais por cliente designado inquilino. Um inquilino é designado para um único cliente. Um cliente pode ter vários inquilinos. O isolamento da rede de inquilinos proíbe a comunicação em rede entre inquilinos ao nível do selo de infraestrutura, mesmo que os inquilinos pertençam ao mesmo cliente.
- **Componentes de armazenamento**: Isolamento através de máquinas virtuais de armazenamento que tenham volumes de armazenamento atribuídos a eles. Os volumes de armazenamento só podem ser atribuídos a uma máquina virtual de armazenamento. Uma máquina virtual de armazenamento é atribuída exclusivamente a um único inquilino na pilha de infraestrutura certificada SAP HANA TDI. Como resultado, os volumes de armazenamento atribuídos a uma máquina virtual de armazenamento podem ser acedidos apenas num inquilino específico e relacionado. Não são visíveis entre os diferentes inquilinos destacados.
- **Servidor ou anfitrião**: Um servidor ou unidade de hospedar não é partilhado entre clientes ou inquilinos. Um servidor ou hospedeiro implantado num cliente, é uma unidade de computação de metal nu atómico que é atribuída a um único inquilino. *Não* é utilizada qualquer partilha de hardware ou divisória seletiva que possa resultar na partilha de um anfitrião ou de um servidor com outro cliente. Os volumes de armazenamento atribuídos à máquina virtual de armazenamento do inquilino específico são montados a tal servidor. Um inquilino pode ter uma a muitas unidades de servidorde diferentes SKUs exclusivamente atribuídos.
- Dentro de um selo de infraestrutura SAP HANA em Azure (Grandes Instâncias), muitos inquilinos diferentes são implantados e isolados uns contra os outros através dos conceitos de inquilino em rede, armazenamento e nível de computação. 


Estas unidades de servidores de metal são suportadas apenas para executar SAP HANA. A camada de aplicação SAP ou a camada de utensílios médios de carga funciona em máquinas virtuais. Os selos de infraestrutura que gerem as unidades SAP HANA em Azure (Grandes Instâncias) estão ligados às espinhas dos serviços da rede Azure. Desta forma, é fornecida conectividade de baixa latência entre unidades SAP HANA em Unidades Azure (Grandes Instâncias) e máquinas virtuais.

A partir de julho de 2019, diferenciamos entre duas revisões diferentes dos selos de grande instância hana e a localização das implantações:

- "Revisão 3" (Rev 3): São os selos que foram disponibilizados para o cliente a implantar antes de julho de 2019
- "Revisão 4" (Rev 4): Novo design de selos que é implantado nas proximidades dos anfitriões Azure VM e que até agora são lançados nas regiões de Azure de:
    -  E.U.A. Oeste 2 
    -  E.U.A. Leste 
    -  Europa ocidental
    -  Europa do Norte


Este documento é um dos vários documentos que abrangem a SAP HANA em Azure (Grandes Instâncias). Este documento introduz a arquitetura básica, responsabilidades e serviços prestados pela solução. Também são discutidas capacidades de alto nível da solução. Para a maioria das outras áreas, como a rede e a conectividade, quatro outros documentos cobrem detalhes e informações de perfuração. A documentação do SAP HANA sobre o Azure (Grandes Instâncias) não abrange aspetos da instalação ou implantação da SAP NetWeaver em VMs. O SAP NetWeaver em Azure está coberto por documentos separados encontrados no mesmo recipiente de documentação Azure. 


Os diferentes documentos de orientação de grandes instâncias hana abrangem as seguintes áreas:

- [Visão geral e arquitetura SAP HANA (Grandes Instâncias) em Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infraestrutura e conectividade SAP HANA (Grandes Instâncias) em Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instale e configure SAP HANA (Grandes Instâncias) no Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (Grandes Instâncias) alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (Grandes Instâncias) resolução e monitorização de problemas em Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Elevada disponibilidade criada em SUSE utilizando o STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Backup osS e restauro para SKUs tipo II de carimbos de Revisão 3](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**Passos seguintes**
- Consulte [os termos](hana-know-terms.md)