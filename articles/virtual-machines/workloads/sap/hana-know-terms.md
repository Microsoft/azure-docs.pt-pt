---
title: Conheça os termos da SAP HANA em Azure (Grandes Instâncias) | Microsoft Docs
description: Conheça os termos da SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92ef2e59dab1921eae8e7d88249e75116601c597
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670874"
---
# <a name="know-the-terms"></a>Conhecer os termos

Várias definições comuns são amplamente utilizadas no Guia de Arquitetura e Implantação Técnica. Note os seguintes termos e significados:

- **IaaS**: Infraestrutura como serviço.
- **PaaS**: Plataforma como serviço.
- **SaaS**: Software como serviço.
- **Componente SAP**: Uma aplicação SAP individual, como componente central do ERP (ECC), Business Warehouse (BW), Solution Manager ou Enterprise Portal (EP). Os componentes SAP podem basear-se em tecnologias tradicionais ABAP ou Java ou numa aplicação não baseada em NetWeaver, como Objetos Empresariais.
- **Ambiente SAP**: Um ou mais componentes SAP agrupados logicamente para desempenhar uma função de negócio, tais como desenvolvimento, garantia de qualidade, formação, recuperação de desastres ou produção.
- **Paisagem SAP**: Refere-se a todos os ativos SAP na sua paisagem de TI. A paisagem SAP inclui todos os ambientes de produção e não produção.
- **Sistema SAP**: A combinação da camada de DBMS e camada de aplicação de, por exemplo, um sistema de desenvolvimento ERP SAP, um sistema de teste SAP BW e um sistema de produção de CRM SAP. As implementações do Azure não suportam a divisão destas duas camadas entre as instalações e o Azure. Um sistema SAP é implantado no local ou é implantado em Azure. Você pode implementar os diferentes sistemas de uma paisagem SAP em Azure ou no local. Por exemplo, pode implantar os sistemas de desenvolvimento e teste SAP CRM em Azure enquanto implementa o sistema de produção de CRM SAP no local. Para o SAP HANA on Azure (Grandes Instâncias), pretende-se que acolha a camada de aplicação SAP dos sistemas SAP em VMs e a instância SAP HANA relacionada com uma unidade no carimbo SAP HANA on Azure (Grandes Instâncias).
- **Selo de Grande Instância**: Uma pilha de infraestrutura de hardware certificada pela SAP HANA TDI e dedicada a executar casos SAP HANA dentro de Azure.
- **SAP HANA em Azure (Grandes Instâncias):** Nome oficial para a oferta em Azure para executar instâncias HANA em hardware certificado SAP HANA TDI que é implantado em selos de Grande Instância em diferentes regiões de Azure. O termo relacionado *HANA Large Instance* é abreviatura para *SAP HANA on Azure (Grandes Instâncias)* e é amplamente utilizado neste guia de implementação técnica.
- **Premissas cruzadas**: Descreve um cenário em que os VMs são implantados numa subscrição do Azure que tem conectividade site-to-site, multi-site ou Azure ExpressRoute entre centros de dados no local e Azure. Em documentação comum do Azure, este tipo de implantações também são descritos como cenários de premissas cruzadas. A razão para a ligação é alargar os domínios no local, o Azure Ative Directory/OpenLDAP e o DNS no local para a Azure. A paisagem no local é estendida aos ativos da Azure das assinaturas Azure. Com esta extensão, os VMs podem fazer parte do domínio no local. 

   Os utilizadores de domínio do domínio no local podem aceder aos servidores e executar serviços nesses VMs (tais como serviços DBMS). É possível a resolução de comunicação e nome entre os VM implantados no local e os VM implantados em Azure. Este cenário é típico da forma como a maioria dos ativos SAP são implantados. Para obter mais informações, consulte [o Gateway Azure VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) e [Crie uma rede virtual com uma ligação site-to-site utilizando o portal Azure](../../../vpn-gateway/tutorial-site-to-site-portal.md).
- **Inquilino**: Um cliente implantado no carimbo HANA Large Instance fica isolado num *inquilino.* Um inquilino está isolado na camada de networking, armazenamento e computação de outros inquilinos. As unidades de armazenamento e computação atribuídas aos diferentes inquilinos não podem ver-se ou comunicar entre si no nível de selo HANA Large Instance. Um cliente pode optar por ter implementações em diferentes inquilinos. Mesmo assim, não há comunicação entre inquilinos no nível de selo HANA Large Instance.
- **Categoria SKU**: Para HANA Large Instance, são oferecidas as seguintes duas categorias de SKUs:
    - **Classe Tipo I**: S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 e S224m
    - **Classe tipo II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm e S960m
- **Carimbo**: Define o tamanho de implementação interna da Microsoft de HANA Large Instances. Antes de as unidades de HANA Large Instance poderem ser implantadas, um carimbo de grande instância HANA, composto por computação, rede e prateleiras de armazenamento, deve ser implantado num local de datacenter. Tal implantação é chamada de carimbo de grande instância HANA ou da Revisão 4 (ver abaixo) em usamos o resumo do termo de **Grande Instância Row**
- **Revisão**: Existem duas revisões de selos diferentes para os selos HANA Large Instance. Estes diferem na arquitetura e proximidade com os anfitriões de máquinas virtuais Azure
    - "Revisão 3" (Rev 3): é o design original que foi implementado a partir de meados do ano de 2016
    - "Revisão 4" (Rev 4): é um novo design que pode proporcionar uma proximidade mais próxima aos anfitriões de máquinas virtuais Azure e com essa latência de rede mais baixa entre os VMs Azure e as unidades HANA Large Instance 
    - "Revisão 4.2" (Rev 4.2): na Revisão 4 DCs existentes, os recursos são remarcados para a Infraestrutura BareMetal.  Os clientes podem aceder aos seus recursos como instâncias BareMetal a partir do portal Azure. 

Uma variedade de recursos adicionais estão disponíveis sobre como implementar uma carga de trabalho SAP na nuvem. Se planeia executar uma implantação de SAP HANA em Azure, precisa de ser experiente e consciente dos princípios do Azure IaaS e da implantação de cargas de trabalho SAP em Azure IaaS. Antes de continuar, consulte [soluções SAP em máquinas virtuais Azure](get-started.md) para obter mais informações. 

**Próximos passos**
- Consulte [a Certificação HLI](hana-certification.md)