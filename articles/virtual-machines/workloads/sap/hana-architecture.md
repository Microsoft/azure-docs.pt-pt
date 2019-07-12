---
title: Arquitetura do SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Arquitetura de como implementar o SAP HANA no Azure (instâncias grandes).
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
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f322ddab19a8c8635009d2e2b7e7e748fb1e73ab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709763"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Arquitetura do SAP HANA (instâncias grandes) no Azure

Num alto nível, o SAP HANA na solução do Azure (instâncias grandes) tem a camada de aplicativo SAP que residem nas VMs. A camada de base de dados reside no hardware configurada TDI do SAP localizado numa instância de gestão grande na mesma região do Azure que está ligada ao Azure IaaS.

> [!NOTE]
> Implemente a camada de aplicação SAP na mesma região do Azure como a camada do SAP DBMS. Esta regra é bem documentada na informações publicadas sobre cargas de trabalho SAP no Azure. 

A arquitetura geral do SAP HANA no Azure (instâncias grandes) fornece uma configuração de hardware com certificação TDI do SAP, o que é uma não virtualizados, bare-metal, o servidor de alto desempenho para a base de dados do SAP HANA. Ele também fornece a capacidade e a flexibilidade do Azure para dimensionar os recursos para a camada de aplicação SAP atender às suas necessidades.

![Descrição geral da arquitetura do SAP HANA no Azure (instâncias grandes)](./media/hana-overview-architecture/image1-architecture.png)

A arquitetura mostrada é dividida em três seções:

- **certo**: Mostra uma infraestrutura no local que executa aplicações de diferentes nos dados centra-se para que os utilizadores finais podem aceder LOB do departamento de aplicações, como o SAP. O ideal é que isso no local infraestrutura está ligada ao Azure com o [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Centro de**: Mostra o IaaS do Azure e, neste caso, utilize de VMs para alojar SAP ou outras aplicações que utilizam SAP HANA como um sistema do DBMS. Menor HANA nas instâncias que funcionam com a memória que fornecem VMs é implementada em VMs em conjunto com a sua camada de aplicativo. Para obter mais informações sobre as máquinas virtuais, consulte [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/).

   Serviços de rede do Azure são utilizados para agrupar sistemas SAP junto com outros aplicativos em redes virtuais. Nestas redes virtuais liguem a sistemas no local, bem como para o SAP HANA no Azure (instâncias grandes).

   Para aplicações SAP NetWeaver e bases de dados que são suportados para executar no Azure, consulte [1928533 de n. º de nota de suporte de SAP – aplicações SAP no Azure: Produtos suportados e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533). Para obter documentação sobre como implementar soluções SAP no Azure, consulte:

  -  [Utilizar o SAP em máquinas de virtuais do Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Utilize soluções SAP em máquinas virtuais do Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Left**: Mostra o hardware com certificação TDI do SAP HANA no carimbo de data / instâncias grandes do Azure. As unidades de instância grande do HANA são ligadas às redes virtuais da sua subscrição do Azure ao utilizar a mesma tecnologia como a conectividade no local para o Azure. A partir de Maio de 2019 tem introduziu uma otimização que permite a comunicação entre as unidades de instância grande do HANA e as VMs do Azure sem o envolvimento do ExpressRoute Gateway. Essa otimização chamada ExpressRoute caminho rápido é apresentada nesta arquitetura (linhas vermelhas). 

O carimbo de instâncias grandes do Azure em si combina os seguintes componentes:

- **Computação**: Servidores que se baseiam em diferente geração de processadores Intel Xeon que fornecem a capacidade de computação necessária e são a certificação do SAP HANA.
- **Rede**: Uma infraestrutura de rede de alta velocidade unificada que interliga a computação, armazenamento e componentes de rede local.
- **Armazenamento**: Uma infraestrutura de armazenamento que é acessada através de um recurso de infraestrutura de rede unificada. A capacidade de armazenamento específico que é fornecida depende do SAP HANA específicas sobre a configuração do Azure (instâncias grandes) que é implementada. Maior capacidade de armazenamento está disponível a um custo mensal adicional.

Dentro da infra-estrutura de multi-inquilino de carimbo de data / instância grande, os clientes são implementados como inquilinos isolados. Na implementação do inquilino, nome de uma subscrição do Azure dentro de sua inscrição do Azure. Esta subscrição do Azure é a que a instância grande do HANA é faturada relativamente. Estes inquilinos têm uma relação de 1:1 para a subscrição do Azure. Para uma rede, é possível aceder a uma unidade de instância grande do HANA implementada num inquilino numa única região do Azure a partir de diferentes redes virtuais que pertencem a diferentes subscrições do Azure. As subscrições do Azure têm de pertencer à mesma inscrição do Azure. 

Tal como acontece com VMs, o SAP HANA no Azure (instâncias grandes) é disponibilizada em várias regiões do Azure. Para oferecer capacidades de recuperação de desastre, pode optar por participar no. Carimbos de instância grande diferentes numa região geográfica político estão ligados entre si. Por exemplo, o HANA carimbos de instância grande no E.u.a. oeste e EUA Leste estão ligados através de uma ligação de rede dedicada para a replicação de recuperação após desastre. 

Assim como pode escolher entre diferentes tipos VM com máquinas virtuais do Azure, pode escolher entre diferentes SKUs de instância grande do HANA adaptadas para tipos diferentes de carga de trabalho do SAP HANA. SAP aplica-se em proporções de memória-para--socket de processador para diferentes cargas de trabalho com base nas gerações de processador Intel. A tabela seguinte mostra os tipos SKU oferecidos.

Pode encontrar os SKUs disponíveis [SKUs disponíveis para HLI](hana-available-skus.md).

**Passos seguintes?**
- Consulte [arquitetura de rede do SAP HANA (instâncias grandes)](hana-network-architecture.md)