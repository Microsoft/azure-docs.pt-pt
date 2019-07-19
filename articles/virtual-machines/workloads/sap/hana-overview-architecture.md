---
title: Visão geral de SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Visão geral de como implantar SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b2076778751161d5763d7bd0643cfe8f71a5f522
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869203"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>O que é SAP HANA nas Instâncias Grandes do Azure?

SAP HANA no Azure (instâncias grandes) é uma solução exclusiva para o Azure. Além de fornecer máquinas virtuais para implantação e execução de SAP HANA, o Azure oferece a possibilidade de executar e implantar SAP HANA em servidores bare-metal dedicados a você. O SAP HANA na solução do Azure (instâncias grandes) baseia-se no hardware bare-metal de host/servidor não compartilhado que é atribuído a você. O hardware do servidor é inserido em carimbos maiores que contêm infraestrutura de computação/servidor, rede e armazenamento. Como uma combinação, ela é certificada pelo HANA com certificação de data center (TDI). SAP HANA no Azure (instâncias grandes) oferece diferentes SKUs ou tamanhos de servidor. As unidades podem ter 36 núcleos de CPU Intel e 768 GB de memória e vão até unidades com até 480 núcleos de CPU Intel e até 24 TB de memória.

O isolamento do cliente dentro do selo da infraestrutura é executado em locatários, que se parece com o seguinte:

- **Rede**: Isolamento de clientes na pilha de infraestrutura por meio de redes virtuais por locatário atribuído pelo cliente. Um locatário é atribuído a um único cliente. Um cliente pode ter vários locatários. O isolamento de rede de locatários proíbe a comunicação de rede entre locatários no nível de carimbo de infraestrutura, mesmo que os locatários pertençam ao mesmo cliente.
- **Componentes de armazenamento**: Isolamento por meio de máquinas virtuais de armazenamento que têm volumes de armazenamento atribuídos a elas. Os volumes de armazenamento podem ser atribuídos a apenas uma máquina virtual de armazenamento. Uma máquina virtual de armazenamento é atribuída exclusivamente a um único locatário na pilha de infraestrutura certificado SAP HANA TDI. Como resultado, os volumes de armazenamento atribuídos a uma máquina virtual de armazenamento podem ser acessados somente em um locatário específico e relacionado. Eles não são visíveis entre os diferentes locatários implantados.
- **Servidor ou host**: Uma unidade de host ou servidor não é compartilhada entre clientes ou locatários. Um servidor ou host implantado em um cliente é uma unidade de computação bare-metal atômica que é atribuída a um único locatário. *Não* é usado nenhum particionamento de hardware ou particionamento flexível que possa resultar no compartilhamento de um host ou de um servidor com outro cliente. Os volumes de armazenamento atribuídos à máquina virtual de armazenamento do locatário específico são montados em um servidor desse tipo. Um locatário pode ter uma ou várias unidades de servidor de SKUs diferentes atribuídas exclusivamente.
- Dentro de um SAP HANA no selo de infraestrutura do Azure (instâncias grandes), muitos locatários diferentes são implantados e isolados entre si por meio dos conceitos de locatário em nível de rede, armazenamento e computação. 


Essas unidades de servidor bare-metal têm suporte para execução somente SAP HANA. A camada de aplicativo SAP ou a camada de meio de carga de trabalho é executada em máquinas virtuais. Os carimbos de infraestrutura que executam o SAP HANA em unidades do Azure (instâncias grandes) são conectados aos backbones dos serviços de rede do Azure. Dessa forma, a conectividade de baixa latência entre SAP HANA em unidades do Azure (instâncias grandes) e máquinas virtuais é fornecida.

A partir de julho de 2019, diferenciamos entre duas revisões diferentes de carimbos de instância grande do HANA e localização de implantações:

- "Revisão 3" (Rev. 3): Os carimbos foram disponibilizados para que o cliente seja implantado antes de julho de 2019
- "Revisão 4" (Rev 4): Novo design de carimbo que é implantado em proximidade com os hosts de VM do Azure e que até agora são lançados nas regiões do Azure de:
    -  E.U.A. Oeste 2 
    -  East US 
    -  Europa Ocidental
    -  Europa do Norte


Este documento é um dos vários documentos que abrangem SAP HANA no Azure (instâncias grandes). Este documento apresenta a arquitetura básica, as responsabilidades e os serviços fornecidos pela solução. Os recursos de alto nível da solução também são discutidos. Para a maioria das outras áreas, como rede e conectividade, quatro outros documentos abrangem detalhes e informações de busca detalhada. A documentação do SAP HANA no Azure (instâncias grandes) não abrange aspectos da instalação do SAP NetWeaver ou das implantações do SAP NetWeaver em VMs. O SAP NetWeaver no Azure é abordado em documentos separados encontrados no mesmo contêiner de documentação do Azure. 


Os diferentes documentos das diretrizes do SAP HANA em instâncias grandes abrangem as seguintes áreas:

- [Visão geral e arquitetura do SAP HANA (instâncias grandes) no Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infraestrutura e conectividade do SAP HANA (instâncias grandes) no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instalar e configurar SAP HANA (instâncias grandes) no Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Alta disponibilidade e recuperação de desastres do SAP HANA (grandes instâncias) no Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Solução de problemas e monitoramento do SAP HANA (instâncias grandes) no Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Configuração de alta disponibilidade no SUSE usando o STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Backup e restauração do so para SKUs do tipo II de carimbos de revisão 3](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**Passos seguintes?**
- Consulte [conhecer os termos](hana-know-terms.md)