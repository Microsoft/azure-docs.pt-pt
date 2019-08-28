---
title: Arquitetura de SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Arquitetura de como implantar SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1373221502db5b2d511bc6f32bd529090caa9e60
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101287"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Arquitetura SAP HANA (instâncias grandes) no Azure

Em um alto nível, o SAP HANA na solução do Azure (instâncias grandes) tem a camada de aplicativo SAP que reside em VMs. A camada de banco de dados reside no hardware configurado por TDI do SAP localizado em um carimbo de instância grande na mesma região do Azure que está conectada ao Azure IaaS.

> [!NOTE]
> Implante a camada de aplicativo SAP na mesma região do Azure que a camada do DBMS do SAP. Essa regra está bem documentada em informações publicadas sobre cargas de trabalho do SAP no Azure. 

A arquitetura geral do SAP HANA no Azure (instâncias grandes) fornece uma configuração de hardware certificado por TDI do SAP, que é um servidor de alto desempenho não virtualizado e bare-metal para o banco de dados de SAP HANA. Ele também fornece a capacidade e a flexibilidade do Azure para dimensionar os recursos para a camada de aplicativo SAP para atender às suas necessidades.

![Visão geral da arquitetura de SAP HANA no Azure (instâncias grandes)](./media/hana-overview-architecture/image1-architecture.png)

A arquitetura mostrada é dividida em três seções:

- **À direita**: Mostra uma infraestrutura local que executa diferentes aplicativos em data centers para que os usuários finais possam acessar aplicativos LOB, como SAP. O ideal é que essa infraestrutura local esteja conectada ao Azure com o [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Centro**: Mostra o IaaS do Azure e, nesse caso, o uso de VMs para hospedar SAP ou outros aplicativos que usam SAP HANA como um sistema DBMS. Instâncias do HANA menores que funcionam com a memória que as VMs fornecem são implantadas em VMs junto com sua camada de aplicativo. Para obter mais informações sobre máquinas virtuais, consulte [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/).

   Os serviços de rede do Azure são usados para agrupar sistemas SAP junto com outros aplicativos em redes virtuais. Essas redes virtuais se conectam a sistemas locais, bem como a SAP HANA no Azure (instâncias grandes).

   Para aplicativos e bancos de dados do SAP NetWeaver com suporte para execução no Azure, confira [nota de suporte SAP #1928533 – aplicativos SAP no Azure: Produtos com suporte e tipos](https://launchpad.support.sap.com/#/notes/1928533)de VM do Azure. Para obter a documentação sobre como implantar soluções SAP no Azure, consulte:

  -  [Usar o SAP em máquinas virtuais do Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Usar soluções SAP em máquinas virtuais do Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **À esquerda**: Mostra o SAP HANA hardware certificado por TDI no carimbo de instância grande do Azure. As unidades de instância grande do HANA estão conectadas às redes virtuais da sua assinatura do Azure usando a mesma tecnologia que a conectividade do local para o Azure. A partir de maio de 2019, foi introduzida uma otimização que permite a comunicação entre as unidades de instância grande do HANA e as VMs do Azure sem envolvimento do gateway de ExpressRoute. Essa otimização chamada caminho rápido do ExpressRoute é exibida nessa arquitetura (linhas vermelhas). 

O selo de instância grande do Azure em si combina os seguintes componentes:

- **Computação**: Servidores baseados em diferentes gerações de processadores Intel Xeon que fornecem o recurso de computação necessário e que são SAP HANA certificados.
- **Rede**: Uma malha de rede unificada de alta velocidade que interconecta os componentes de computação, armazenamento e LAN.
- **Armazenamento**: Uma infraestrutura de armazenamento que é acessada por meio de uma malha de rede unificada. A capacidade de armazenamento específica que é fornecida depende do SAP HANA específico na configuração do Azure (instâncias grandes) que é implantada. Mais capacidade de armazenamento está disponível a um custo mensal adicional.

Na infraestrutura multilocatário do carimbo de instância grande, os clientes são implantados como locatários isolados. Na implantação do locatário, você nomeou uma assinatura do Azure em seu registro do Azure. Essa assinatura do Azure é aquela na qual a instância grande do HANA é cobrada. Esses locatários têm uma relação de 1:1 com a assinatura do Azure. Para uma rede, é possível acessar uma unidade de instância grande do HANA implantada em um locatário em uma região do Azure de redes virtuais diferentes que pertencem a assinaturas diferentes do Azure. Essas assinaturas do Azure devem pertencer ao mesmo registro do Azure. 

Assim como acontece com as VMs, SAP HANA no Azure (instâncias grandes) é oferecido em várias regiões do Azure. Para oferecer recursos de recuperação de desastres, você pode optar por aceitar. Carimbos de instância grande diferentes em uma região de política geográfica são conectados entre si. Por exemplo, os carimbos de instância grande do HANA no oeste dos EUA e no leste dos EUA são conectados por meio de um link de rede dedicado para replicação de recuperação de desastre. 

Assim como você pode escolher entre diferentes tipos de VM com máquinas virtuais do Azure, você pode escolher entre diferentes SKUs da instância grande do HANA que são adaptadas para tipos de carga de trabalho diferentes de SAP HANA. O SAP aplica as taxas de soquete de memória para processador para cargas de trabalho variadas com base nas gerações de processador Intel. A tabela a seguir mostra os tipos de SKU oferecidos.

Você pode encontrar SKUs disponíveis [de](hana-available-skus.md)SKUs disponíveis para o HLI.

**Passos seguintes?**
- Consulte a [arquitetura de rede SAP Hana (instâncias grandes)](hana-network-architecture.md)