---
title: Saber os termos de SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Sabe os termos de SAP HANA no Azure (instâncias grandes).
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
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a8131bc953c2aba3c8d33f866cbbe9b1e232e168
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819093"
---
# <a name="know-the-terms"></a>Saber os termos

Várias definições de comuns são amplamente utilizadas na arquitetura e guia de implementação técnica. Tenha em atenção os seguintes termos e seus significados:

- **IaaS**: Infraestrutura como serviço.
- **PaaS**: Plataforma como serviço.
- **SaaS**: Software como serviço.
- **Componente SAP**: Um SAP aplicativos individuais, como o componente Central de ERP (ECC), o Business Warehouse (BW), o Gestor de solução ou o Enterprise Portal (EP). Componentes SAP podem basear-se em tecnologias tradicionais de ABAP ou Java ou um aplicativo de não com base em NetWeaver, como objetos comerciais.
- **Ambiente de SAP**: Um ou mais componentes SAP logicamente agrupados para executar uma função de negócio, como o desenvolvimento, controle de qualidade, treinamento, recuperação após desastre ou de produção.
- **Ambiente SAP**: Refere-se para os ativos SAP inteiros em seu ambiente IT. A paisagem SAP inclui todos os ambientes de não produção e produção.
- **Sistema SAP**: A combinação de camada do DBMS e a camada de aplicativo de, por exemplo, um sistema de desenvolvimento SAP ERP, um SAP BW testar o sistema e um sistema de produção do SAP CRM. Implementações do Azure não suportam dividindo estas duas camadas entre no local e o Azure. Um sistema SAP é implementado no local ou é implementado no Azure. Pode implantar os sistemas diferentes de um ambiente SAP para o Azure ou no local. Por exemplo, pode implementar o desenvolvimento de CRM do SAP e testar sistemas no Azure, enquanto implementa o SAP CRM produção sistema no local. Para o SAP HANA no Azure (instâncias grandes), seu objetivo é que hospede a camada de aplicativo SAP de sistemas SAP em VMs e a instância do SAP HANA relacionada numa unidade do SAP HANA no carimbo de data / do Azure (instâncias grandes).
- **Carimbo de instância grande**: Uma pilha de infraestrutura de hardware que é dedicado para executar as instâncias do SAP HANA no Azure e com certificação TDI do SAP HANA.
- **SAP HANA no Azure (instâncias grandes):** Nome oficial para a oferta no Azure executar o HANA nas instâncias em hardware com certificação TDI do SAP HANA, o que é implementada nos carimbos de data / instância grande em diferentes regiões do Azure. O termo relacionado *instância grande do HANA* é a abreviação de *SAP HANA no Azure (instâncias grandes)* e é amplamente utilizada neste guia de implementação técnica.
- **Em vários locais**: Descreve um cenário em que as VMs são implementadas para uma subscrição do Azure que tem conectividade do ExpressRoute do Azure entre centros de dados no local e o Azure, múltiplos sites ou site a site. Documentação do Azure em comum, esses tipos de implementações também são descritos como cenários de vários locais. O motivo para a ligação é para expandir os domínios no local, no local do Azure Active Directory/OpenLDAP e DNS no local para o Azure. O Panorama de no local é expandido para recursos do Azure das subscrições do Azure. Com essa extensão, as VMs podem ser parte do domínio no local. 

   Os utilizadores de domínio do domínio no local podem aceder aos servidores e executam serviços nessas VMS (como serviços do DBMS). Resolução de nomes e de comunicação entre VMs implementadas no local e VMs implementadas no Azure, é possível. Este cenário é típico de forma em que a maioria dos ativos SAP são implementados. Para obter mais informações, consulte [Gateway de VPN do Azure](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [criar uma rede virtual com uma ligação site a site com o portal do Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Inquilino**: Um cliente implementado num carimbo de data / instância grande do HANA obtém isolado num *inquilino.* Um inquilino seja isolado no funcionamento em rede, armazenamento e a camada de computação de outros inquilinos. Unidades de armazenamento e computação atribuídas a diferentes inquilinos não é possível ver uns aos outros ou comunicar entre si no nível de carimbo de data / instância grande do HANA. Um cliente pode optar por implementações em inquilinos diferentes. Mesmo assim, não existe nenhuma comunicação entre inquilinos no nível de carimbo de data / instância grande do HANA.
- **Categoria SKU**: Para a instância grande do HANA, as seguintes duas categorias de SKUs são oferecidas:
    - **Tipo de classe eu**: S72, S72m, S96, S144, S144m, S192, S192m e S192xm
    - **Tipo de classe II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm e S960m


Uma variedade de recursos adicionais estão disponíveis sobre como implementar uma carga de trabalho SAP na cloud. Se pretender executar uma implementação do SAP HANA no Azure, terá de ser experientes com e em consideração os princípios de IaaS do Azure e a implementação de cargas de trabalho SAP no Azure IaaS. Antes de continuar, consulte [soluções SAP de utilização em máquinas virtuais do Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações. 

**Passos seguintes?**
- Consulte [HLI certificação](hana-certification.md)