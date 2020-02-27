---
title: Conheça os termos do SAP HANA em Azure (Grandes Instâncias) / Microsoft Docs
description: Conheça os termos do SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6785ae821f701121185f0064c6317c69d50191ab
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617055"
---
# <a name="know-the-terms"></a>Conhecer os termos

Várias definições comuns são amplamente utilizadas no Guia de Arquitetura e Implantação Técnica. Note os seguintes termos e significados:

- **IaaS**: Infraestrutura como serviço.
- **PaaS**: Plataforma como serviço.
- **SaaS**: Software como serviço.
- **Componente SAP**: Uma aplicação SAP individual, como a Componente Central ERP (ECC), O Armazém de Negócios (BW), O Gestor de Soluções ou o Portal da Empresa (EP). Os componentes SAP podem basear-se nas tecnologias tradicionais ABAP ou Java ou numa aplicação não baseada em NetWeaver, como Objetos Empresariais.
- **Ambiente SAP**: Um ou mais componentes SAP agrupados logicamente para desempenhar uma função de negócio, tais como desenvolvimento, garantia de qualidade, formação, recuperação de desastres ou produção.
- **Paisagem SAP**: Refere-se a todos os ativos SAP na sua paisagem de TI. A paisagem SAP inclui todos os ambientes de produção e não produção.
- **Sistema SAP**: A combinação da camada DBMS e da camada de aplicação de, por exemplo, um sistema de desenvolvimento SAP ERP, um sistema de teste SAP BW e um sistema de produção de CRM SAP. As implantações azure não suportam dividir estas duas camadas entre as instalações e o Azure. Um sistema SAP é implantado no local ou está implantado em Azure. Você pode implantar os diferentes sistemas de uma paisagem SAP em Azure ou no local. Por exemplo, pode implantar os sistemas de desenvolvimento e teste de CRM SAP em Azure enquanto implementa o sistema de produção de CRM SAP no local. Para o SAP HANA on Azure (Grandes Instâncias), pretende-se que você acolhe a camada de aplicação SAP de sistemas SAP em VMs e a instância sAP HANA relacionada em uma unidade no carimbo SAP HANA em Azure (Grandes Instâncias).
- **Carimbo de grande instância**: Uma pilha de infraestrutura de hardware certificada pela SAP HANA TDI e dedicada a executar instâncias SAP HANA dentro do Azure.
- **SAP HANA em Azure (Grandes Instâncias):** Nome oficial para a oferta em Azure para executar instâncias HANA em hardware certificado SAP HANA TDI que é implantado em selos de Grande Instância em diferentes regiões de Azure. O termo relacionado *HANA Large Instance* é curto para *SAP HANA em Azure (Grandes Instâncias)* e é amplamente utilizado neste guia de implantação técnica.
- **Cross-premises**: Descreve um cenário em que os VMs são implantados para uma subscrição Azure que tem conectividade local-a-local, multi-site ou Azure ExpressRoute entre centros de dados no local e Azure. Na documentação comum do Azure, este tipo de implantações também são descritos como cenários transversais. A razão da ligação é o alargamento dos domínios no local, no local Azure Ative Directory/OpenLDAP e no local DNS para azure. A paisagem no local é estendida aos ativos azure das assinaturas Azure. Com esta extensão, os VMs podem fazer parte do domínio no local. 

   Os utilizadores de domínio do domínio no local podem aceder aos servidores e executar serviços nesses VMs (como serviços DBMS). A resolução de comunicação e nome entre VMs implantados no local e VMs implantados em Azure é possível. Este cenário é típico da forma como a maioria dos ativos da SAP são implantados. Para mais informações, consulte [o Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [crie uma rede virtual com uma ligação site-a-site utilizando o portal Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Inquilino**: Um cliente implantado no carimbo HANA Large Instance é isolado num *inquilino.* Um inquilino está isolado na camada de networking, armazenamento e computação de outros inquilinos. Unidades de armazenamento e computação atribuídas aos diferentes inquilinos não podem ver-se ou comunicar entre si no nível de selo hana Large Instance. Um cliente pode optar por ter implantações em diferentes inquilinos. Mesmo assim, não existe comunicação entre inquilinos no nível de selo hana large instância.
- **Categoria SKU**: Para a HANA Large Instance, são oferecidas as duas categorias seguintes de SKUs:
    - **Classe tipo I**: S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 e S224m
    - **Classe Tipo II**: S384, S384m, S384xxm, S576m, S576xm, S768m, S768xm e S960m
- **Carimbo**: Define o tamanho de implementação interna da Microsoft de grandes instâncias HANA. Antes que as unidades hana Large Instance possam ser implantadas, um carimbo HANA Large Instance composto por computação, rede e racks de armazenamento precisa de ser implantado num local de datacenter. Tal implantação é chamada de carimbo de instância HANA Grande ou da Revisão 4 (ver abaixo) em que usamos o termo alternativo de **Large Instance Row**
- **Revisão**: Existem duas revisões diferentes de selos de grande instância HANA. Estes diferem na arquitetura e proximidade com os anfitriões de máquinas virtuais Azure
    - "Revisão 3" (Rev 3): é o desenho original que foi implantado a partir de meados do ano de 2016
    - "Revisão 4" (Rev 4): é um novo design que pode proporcionar uma proximidade mais próxima aos anfitriões de máquinas virtuais Azure e com essa menor latência de rede entre as Unidades Azure VMs e HANA Large Instance 

Uma variedade de recursos adicionais estão disponíveis sobre como implementar uma carga de trabalho SAP na nuvem. Se planeia executar uma implantação do SAP HANA em Azure, tem de ser experiente e consciente dos princípios do Azure IaaS e da implantação de cargas de trabalho SAP no Azure IaaS. Antes de continuar, consulte [soluções SAP use em máquinas virtuais Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações. 

**Passos seguintes?**
- Consulte a [certificação HLI](hana-certification.md)