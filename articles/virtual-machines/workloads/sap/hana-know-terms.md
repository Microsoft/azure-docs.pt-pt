---
title: Conhecer os termos de SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Conheça os termos de SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fec9f18404fc45f4cf69cc13b1602f818dbddfaf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099745"
---
# <a name="know-the-terms"></a>Conhecer os termos

Várias definições comuns são amplamente usadas no guia de arquitetura e implantação técnica. Observe os seguintes termos e seus significados:

- **IaaS**: Infraestrutura como serviço.
- **PaaS**: Plataforma como serviço.
- **SaaS**: Software como serviço.
- **Componente SAP**: Um aplicativo SAP individual, como o ECC (componente central de ERP), o Business Warehouse (BW), o Solution Manager ou o Enterprise Portal (EP). Os componentes SAP podem ser baseados em tecnologias tradicionais de ABAP ou Java ou em um aplicativo não baseado em NetWeaver, como objetos comerciais.
- **Ambiente SAP**: Um ou mais componentes SAP agrupados logicamente para executar uma função comercial, como desenvolvimento, garantia de qualidade, treinamento, recuperação de desastres ou produção.
- **Cenário SAP**: Refere-se a todos os ativos do SAP em seu cenário de ti. A estrutura SAP inclui todos os ambientes de produção e de não produção.
- **Sistema SAP**: A combinação de camada de DBMS e camada de aplicativo de, por exemplo, um sistema de desenvolvimento SAP ERP, um sistema de teste SAP BW e um sistema de produção SAP CRM. As implantações do Azure não dão suporte à divisão dessas duas camadas entre o local e o Azure. Um sistema SAP é implantado localmente ou implantado no Azure. Você pode implantar os diferentes sistemas de uma estrutura SAP no Azure ou no local. Por exemplo, você pode implantar os sistemas de desenvolvimento e teste do SAP CRM no Azure enquanto implanta o sistema de produção do SAP CRM local. Para SAP HANA no Azure (instâncias grandes), é pretendido que você hospede a camada de aplicativo SAP de sistemas SAP em VMs e a instância SAP HANA relacionada em uma unidade no carimbo SAP HANA no Azure (instâncias grandes).
- **Carimbo de instância grande**: Uma pilha de infraestrutura de hardware SAP HANA certificados por TDI e dedicados para executar SAP HANA instâncias no Azure.
- **SAP HANA no Azure (instâncias grandes):** Nome oficial da oferta no Azure para executar instâncias do HANA em SAP HANA hardware certificado por TDI implantado em carimbos de instância grande em diferentes regiões do Azure. O termo relacionado do *Hana em instâncias grandes* é curto para *SAP Hana no Azure (instâncias grandes)* e é amplamente usado neste guia de implantação técnica.
- **Entre instalações**: Descreve um cenário em que as VMs são implantadas em uma assinatura do Azure que tem conectividade site a site, multissite ou Azure ExpressRoute entre data centers locais e o Azure. Na documentação comum do Azure, esses tipos de implantações também são descritos como cenários entre instalações. O motivo para a conexão é estender os domínios locais, Azure Active Directory/OpenLDAP locais e o DNS local para o Azure. O cenário local é estendido para os ativos do Azure das assinaturas do Azure. Com essa extensão, as VMs podem fazer parte do domínio local. 

   Os usuários de domínio do domínio local podem acessar os servidores e executar serviços nessas VMs (como serviços DBMS). A comunicação e a resolução de nomes entre VMs implantadas localmente e VMs implantadas no Azure são possíveis. Esse cenário é típico da forma como a maioria dos ativos SAP são implantados. Para obter mais informações, consulte [Gateway de VPN do Azure](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [criar uma rede virtual com uma conexão site a site usando o portal do Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Locatário**: Um cliente implantado no carimbo de instância grande do HANA é isolado em um *locatário.* Um locatário é isolado na camada de rede, armazenamento e computação de outros locatários. As unidades de armazenamento e de computação atribuídas aos diferentes locatários não podem ver um ao outro ou se comunicar umas com as outras no nível de carimbo de instância grande do HANA. Um cliente pode optar por ter implantações em locatários diferentes. Mesmo assim, não há nenhuma comunicação entre locatários no nível de carimbo de instância grande do HANA.
- **Categoria de SKU**: Para o HANA em instâncias grandes, as duas categorias de SKUs a seguir são oferecidas:
    - **Classe do tipo I**: S72, S72m, S96, S144, S144m, S192, S192m e S192xm
    - **Classe do tipo II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm e S960m
- **Carimbo**: Define o tamanho da implantação interna da Microsoft de instâncias grandes do HANA. Antes que as unidades de instância grande do HANA possam ser implantadas, um carimbo de instância grande do HANA que consiste em racks de computação, rede e armazenamento precisa ser implantado em um local de datacenter. Essa implantação é chamada de carimbo de instância grande do HANA ou da revisão 4 (veja abaixo) em nós usamos a alternativa do termo da **linha de instância grande**
- **Revisão**: Há duas revisões de carimbo diferentes para carimbos de instância grande do HANA. Eles diferem na arquitetura e na proximidade com os hosts da máquina virtual do Azure
    - "Revisão 3" (Rev. 3): é o design original que foi implantado a partir do meio do ano 2016
    - "Revisão 4" (Rev 4): é um novo design que pode fornecer proximidade com os hosts de máquina virtual do Azure e com essa latência de rede menor entre as VMs do Azure e as unidades de instância grande do HANA 

Uma variedade de recursos adicionais está disponível em como implantar uma carga de trabalho do SAP na nuvem. Se você planeja executar uma implantação de SAP HANA no Azure, precisará ter conhecimento dos princípios do Azure IaaS e da implantação de cargas de trabalho do SAP no Azure IaaS. Antes de continuar, consulte [usar soluções SAP em máquinas virtuais do Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações. 

**Passos seguintes?**
- Consulte a [certificação da HLI](hana-certification.md)