---
title: Descrição geral do SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Descrição geral de como implementar o SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c177dbad1145dee6eda3202d8076997cc7673dfc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477805"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>O que é SAP HANA nas Instâncias Grandes do Azure?

SAP HANA no Azure (instâncias grandes) é uma solução única para o Azure. Além de fornecer máquinas virtuais para implementar e executar o SAP HANA, Azure oferece-lhe a possibilidade de executar e implementar o SAP HANA em servidores bare-metal dedicadas para. O SAP HANA na solução do Azure (instâncias grandes) baseia-se em hardware de bare-metal não partilhado/servidor de anfitrião que está atribuída a. O hardware de servidor é incorporado nos carimbos de data / maiores que contêm/servidor de computação, rede e infraestrutura de armazenamento. Como uma combinação é HANA adaptada center integração de dados (TDI) com certificação. SAP HANA no Azure (instâncias grandes) oferece os SKUs de servidor diferente ou tamanhos. Unidades podem ter 36 núcleos de Intel CPU e 768 GB de memória e vá até as unidades que tenham até 480 núcleos de Intel CPU e até 24 TB de memória.

O isolamento de cliente dentro de carimbo de data / infraestrutura é executado em inquilinos, que se parece com:

- **Funcionamento em rede**: Isolamento de clientes na pilha de infraestrutura através de redes virtuais por cliente atribuído inquilino. Um inquilino é atribuído a um único cliente. Um cliente pode ter vários inquilinos. O isolamento de rede de inquilinos proíbe a comunicação de rede entre inquilinos no nível de carimbo de data / infraestrutura, mesmo que os inquilinos de pertencer ao mesmo cliente.
- **Componentes de armazenamento**: Isolamento através de máquinas de virtuais de armazenamento que tenham volumes de armazenamento atribuídas. Volumes de armazenamento podem ser atribuídos a máquina de virtual de um armazenamento apenas. Uma máquina de virtual de armazenamento é atribuída exclusivamente para um único inquilino na pilha de infraestrutura de certificados de TDI do SAP HANA. Como resultado, os volumes de armazenamento atribuídos a uma máquina virtual de armazenamento podem ser acessados num específico e relacionado inquilino apenas. Não são visíveis entre os diferentes inquilinos implementados.
- **Servidor ou anfitrião**: Um servidor ou a unidade de anfitrião não é partilhada entre inquilinos ou clientes. Um servidor ou o anfitrião implementada para um cliente, é uma unidade de computação do bare-metal atômicas que é atribuída a um único inquilino. *Não* é utilizada o particionamento de hardware ou software de criação de partições que poderá resultar numa partilha de um anfitrião ou um servidor com outro cliente. Volumes de armazenamento que estão atribuídas à máquina virtual de armazenamento do inquilino específico são instalados em tal servidor. Um inquilino pode ter um para muitos unidades de servidores de SKUs diferentes atribuídas exclusivamente.
- Dentro de um SAP HANA no carimbo de infraestrutura do Azure (instâncias grandes), muitos inquilinos diferentes são implementados e isolados entre si por meio dos conceitos de inquilino na rede, armazenamento e nível de computação. 


Estas unidades de bare-metal server são suportadas para executar apenas o SAP HANA. A camada de aplicação SAP ou a camada intermediária ware de carga de trabalho é executado em máquinas virtuais. Os carimbos de infraestrutura que executam o SAP HANA nas unidades do Azure (instâncias grandes) estão ligados as backbones de serviços de rede do Azure. Dessa forma, a conectividade de baixa latência entre máquinas virtuais e SAP HANA nas unidades do Azure (instâncias grandes) é fornecida.

Este documento é um dos vários documentos que abrangem o SAP HANA no Azure (instâncias grandes). Este documento apresenta a arquitetura básica, responsabilidades e serviços fornecidos pela solução. Capacidades de alto nível da solução também são discutidas. Para a maioria das outras áreas, tais como o sistema de rede e conectividade, quatro outros documentos sobre detalhes e informações de desagregação. A documentação do SAP HANA no Azure (instâncias grandes) não abrange aspetos da instalação do SAP NetWeaver ou implementações de SAP NetWeaver em VMs. SAP NetWeaver no Azure é descrito no documento separado, foi encontrado no mesmo contentor de documentação do Azure. 


Os documentos diferentes de orientação de instância grande do HANA abrangem as seguintes áreas:

- [Descrição geral do SAP HANA (instâncias grandes) e a arquitetura no Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infraestrutura de SAP HANA (instâncias grandes) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instalar e configurar o SAP HANA (instâncias grandes) no Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (instâncias grandes) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Resolução de problemas do SAP HANA (instâncias grandes) e monitorização no Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Elevada disponibilidade, configure no SUSE, utilizando o STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [OS backup and restore for Type II SKUs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus) (Cópias de segurança e restauro de SO para SKUs do Tipo II)

**Passos seguintes?**
- Consulte [saber os termos](hana-know-terms.md)