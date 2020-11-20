---
title: Arquitetura da SAP HANA em Azure (Grandes Instâncias) Microsoft Docs
description: Arquitetura de como implantar SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a23a177945316bca89aa5cbcc46f840213dbe832
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964818"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Sap HANA (Grandes Instâncias) arquitetura em Azure

A um nível elevado, a solução SAP HANA on Azure (Grandes Instâncias) tem a camada de aplicação SAP residente em VMs. A camada de base de dados reside em hardware configurado por TDI SAP localizado num carimbo de Grande Instância na mesma região de Azure que está ligada ao Azure IaaS.

> [!NOTE]
> Coloque a camada de aplicação SAP na mesma região azul que a camada SAP DBMS. Esta regra está bem documentada em informações publicadas sobre cargas de trabalho SAP em Azure. 

A arquitetura geral do SAP HANA em Azure (Grandes Instâncias) fornece uma configuração de hardware certificada pela SAP TDI, que é um servidor de metal não virtualizado, de metal nu e de alto desempenho para a base de dados SAP HANA. Também fornece a capacidade e flexibilidade do Azure para escalar recursos para a camada de aplicação SAP para atender às suas necessidades.

![Visão geral arquitetónica do SAP HANA em Azure (Grandes Instâncias)](./media/hana-overview-architecture/image1-architecture.png)

A arquitetura mostrada é dividida em três secções:

- **Direito**: Mostra uma infraestrutura no local que executa diferentes aplicações em centros de dados para que os utilizadores finais possam aceder a aplicações LOB, como o SAP. Idealmente, esta infraestrutura no local está ligada ao Azure com [o ExpressRoute.](https://azure.microsoft.com/services/expressroute/)

- **Centro**: Mostra Azure IaaS e, neste caso, utilização de VMs para hospedar SAP ou outras aplicações que utilizem o SAP HANA como sistema DBMS. Casos menores de HANA que funcionam com a memória que os VMs fornecem são implantados em VMs juntamente com a sua camada de aplicação. Para obter mais informações sobre máquinas virtuais, consulte [máquinas Virtuais.](https://azure.microsoft.com/services/virtual-machines/)

   Os serviços de rede Azure são utilizados para agrupar sistemas SAP juntamente com outras aplicações em redes virtuais. Estas redes virtuais ligam-se aos sistemas no local, bem como ao SAP HANA em Azure (Grandes Instâncias).

   Para aplicações e bases de dados SAP NetWeaver suportadas para funcionar em Azure, consulte [a Nota de Suporte SAP #1928533 – aplicações SAP no Azure: Produtos suportados e tipos de VM Azure](https://launchpad.support.sap.com/#/notes/1928533). Para documentação sobre como implementar soluções SAP no Azure, consulte:

  -  [Utilize SAP em máquinas virtuais do Windows](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [Utilize soluções SAP em máquinas virtuais Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Esquerda**: Mostra o hardware certificado SAP HANA TDI no carimbo Azure Large Instance. As unidades HANA Large Instance estão ligadas às redes virtuais da sua subscrição Azure utilizando a mesma tecnologia que a conectividade das instalações para o Azure. A partir de maio de 2019 foi introduzida uma otimização que permite comunicar entre as unidades HANA Large Instance e os VMs Azure sem o envolvimento do Portal ExpressRoute. Esta otimização chamada ExpressRoute Fast Path é exibida nesta arquitetura (linhas vermelhas). 

O carimbo Azure Large Instance combina os seguintes componentes:

- **Computação**: Servidores baseados em diferentes gerações de processadores Intel Xeon que fornecem a capacidade de computação necessária e são certificados PELA SAP HANA.
- **Rede**: Um tecido de rede unificado de alta velocidade que interliga os componentes da computação, armazenamento e LAN.
- **Armazenamento**: Uma infraestrutura de armazenamento que é acessada através de um tecido de rede unificado. A capacidade de armazenamento específica fornecida depende da configuração específica SAP HANA em Azure (Grandes Instâncias) que é implantada. Mais capacidade de armazenamento está disponível a um custo mensal adicional.

Dentro da infraestrutura multi-arrendatário do carimbo de Grande Instância, os clientes são implantados como inquilinos isolados. Na implantação do inquilino, você nomeia uma assinatura Azure dentro da sua inscrição no Azure. Esta assinatura Azure é a que a HANA Large Instance é faturada contra. Estes inquilinos têm uma relação 1:1 com a assinatura Azure. Para uma rede, é possível aceder a uma unidade HANA Large Instance implantada num inquilino de uma região de Azure a partir de diferentes redes virtuais que pertencem a diferentes subscrições do Azure. Essas assinaturas Azure devem pertencer à mesma matrícula do Azure. 

Tal como acontece com os VMs, o SAP HANA on Azure (Grandes Instâncias) é oferecido em várias regiões do Azure. Para oferecer capacidades de recuperação de desastres, pode optar por optar por participar. Os selos de grande instância diferentes dentro de uma região geopolítica estão ligados entre si. Por exemplo, os selos de grande instância HANA no Oeste dos EUA e no Leste dos EUA estão ligados através de uma ligação de rede dedicada para a replicação da recuperação de desastres. 

Assim como pode escolher entre diferentes tipos de VM com Máquinas Virtuais Azure, pode escolher entre diferentes SKUs de HANA Large Instance que são adaptados para diferentes tipos de carga de trabalho de SAP HANA. O SAP aplica rácios de tomada de memória para processador para cargas de trabalho variadas com base nas gerações do processador Intel. A tabela seguinte mostra os tipos SKU oferecidos.

Pode encontrar SKUs [disponíveis para HLI.](hana-available-skus.md)

**Próximos passos**
- Consulte [a arquitetura da rede SAP HANA (Grandes Instâncias)](hana-network-architecture.md)
