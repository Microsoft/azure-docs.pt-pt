---
title: Arquitetura de SAP HANA em Azure (Grandes Instâncias) / Microsoft Docs
description: Arquitetura de como implantar o SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 041da4198b0bdd040a4916008a1135aa2e2a5f7d
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614529"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Arquitetura SAP HANA (Grandes Instâncias) em Azure

A um nível elevado, a solução SAP HANA on Azure (Grandes Instâncias) tem a camada de aplicação SAP residente em VMs. A camada de base de dados reside em hardware configurado por SAP TDI localizado num carimbo de grande instância na mesma região do Azure que está ligado ao Azure IaaS.

> [!NOTE]
> Implante a camada de aplicação SAP na mesma região de Azure que a camada DBMS SAP. Esta regra está bem documentada em informações publicadas sobre cargas de trabalho sap no Azure. 

A arquitetura global do SAP HANA on Azure (Grandes Instâncias) fornece uma configuração de hardware certificada por SAP TDI, que é um servidor de alto desempenho não virtualizado, de metal nu para a base de dados SAP HANA. Também fornece a capacidade e flexibilidade do Azure para escalar recursos para a camada de aplicação SAP para atender às suas necessidades.

![Visão geral arquitetónica do SAP HANA em Azure (Grandes Instâncias)](./media/hana-overview-architecture/image1-architecture.png)

A arquitetura mostrada é dividida em três secções:

- **Direito**: Mostra uma infraestrutura no local que executa diferentes aplicações em centros de dados para que os utilizadores finais possam aceder a aplicações LOB, como o SAP. Idealmente, esta infraestrutura no local está ligada ao Azure com a [ExpressRoute.](https://azure.microsoft.com/services/expressroute/)

- **Centro**: Mostra O Azure IaaS e, neste caso, a utilização de VMs para acolher SAP ou outras aplicações que utilizam o SAP HANA como sistema DBMS. Casos MENORes de HANA que funcionam com a memória que os VMs fornecem são implantados em VMs juntamente com a sua camada de aplicação. Para obter mais informações sobre máquinas virtuais, consulte [máquinas virtuais.](https://azure.microsoft.com/services/virtual-machines/)

   Os serviços de rede Azure são utilizados para agrupar sistemas SAP juntamente com outras aplicações em redes virtuais. Estas redes virtuais ligam-se aos sistemas no local, bem como ao SAP HANA em Azure (Grandes Instâncias).

   Para aplicações e bases de dados SAP NetWeaver suportadas para funcionar em Azure, consulte [SAP Support Note #1928533 – Aplicações SAP no Azure: Produtos suportados e tipos de VM Azure.](https://launchpad.support.sap.com/#/notes/1928533) Para obter documentação sobre como implementar soluções SAP no Azure, consulte:

  -  [Utilizar SAP em máquinas virtuais do Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Utilize soluções SAP em máquinas virtuais Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Esquerda**: Mostra o hardware certificado por SAP HANA TDI no carimbo De Instância Grande Azure. As unidades HANA Large Instance estão ligadas às redes virtuais da sua subscrição Azure utilizando a mesma tecnologia que a conectividade das instalações para o Azure. A partir de maio de 2019 foi introduzida uma otimização que permite comunicar entre as unidades HANA Large Instance e os VMs Azure sem envolvimento do ExpressRoute Gateway. Esta otimização chamada Caminho Rápido ExpressRoute é exibida nesta arquitetura (linhas vermelhas). 

O carimbo de instância grande azure combina os seguintes componentes:

- **Computação**: Servidores que são baseados em diferentes gerações de processadores Intel Xeon que fornecem a capacidade de computação necessária e são certificados sAP HANA.
- **Rede**: Um tecido unificado de rede de alta velocidade que interliga os componentes de computação, armazenamento e LAN.
- **Armazenamento**: Uma infraestrutura de armazenamento que é acedida através de um tecido de rede unificado. A capacidade de armazenamento específica que é fornecida depende da configuração específica do SAP HANA sobre o Azure (Grandes Instâncias) que é implantada. Mais capacidade de armazenamento está disponível a um custo mensal adicional.

Dentro da infraestrutura multi-arrendatária do carimbo de Grande Instância, os clientes são destacados como inquilinos isolados. Na implantação do inquilino, você nomeia uma assinatura Azure dentro da sua matrícula Azure. Esta assinatura Azure é aquela contra a qual a Grande Instância HANA é cobrada. Estes inquilinos têm uma relação 1:1 com a assinatura Azure. Para uma rede, é possível aceder a uma unidade HANA Large Instance implantada num inquilino numa região do Azure de diferentes redes virtuais que pertencem a diferentes subscrições do Azure. Estas assinaturas Azure devem pertencer à mesma matrícula do Azure. 

Tal como acontece com os VMs, o SAP HANA on Azure (Grandes Instâncias) é oferecido em várias regiões azure. Para oferecer capacidades de recuperação de desastres, pode optar por optar. Selos de grandes instâncias diferentes dentro de uma região geopolítica estão ligados uns aos outros. Por exemplo, os selos de grande instância HANA no Oeste e Leste dos EUA estão ligados através de uma ligação de rede dedicada para a replicação de recuperação de desastres. 

Assim como pode escolher entre diferentes tipos de VM com Máquinas Virtuais Azure, pode escolher entre diferentes SKUs de HANA Large Instance que são adaptados para diferentes tipos de carga de trabalho de SAP HANA. O SAP aplica rácios memória-processador-tomada para cargas de trabalho variadas com base nas gerações do processador Intel. A tabela seguinte mostra os tipos SKU oferecidos.

Pode encontrar [SKUs disponíveis disponíveis para HLI](hana-available-skus.md).

**Passos seguintes?**
- Consulte a arquitetura de [rede SAP HANA (Grandes Instâncias)](hana-network-architecture.md)