---
title: Visão geral da rede virtual Azure TAP | Microsoft Docs
description: Conheça a rede virtual TAP. A rede virtual TAP fornece-lhe uma cópia profunda do tráfego de rede de máquinas virtuais que pode ser transmitido para um colecionador de pacotes.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 6160dd09edc57f2f52306d4dad0dde413fff0616
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617187"
---
# <a name="virtual-network-tap"></a>TAP de rede virtual
> [!IMPORTANT]
> A rede virtual TAP Preview encontra-se atualmente em espera em todas as regiões do Azure. Pode enviar-nos um e-mail <azurevnettap@microsoft.com> com o seu ID de subscrição e iremos notificá-lo com futuras atualizações sobre a pré-visualização. Entretanto, pode utilizar soluções baseadas em agentes ou NVA que forneçam funcionalidades de visibilidade TAP/Rede através das [nossas soluções de parceiros Packet Broker](#virtual-network-tap-partner-solutions) disponíveis nas [Ofertas do Mercado Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners)

A rede virtual Azure TAP (Terminal Access Point) permite-lhe transmitir continuamente o tráfego da rede de máquinas virtuais para um coletor de pacotes de rede ou uma ferramenta de análise. A ferramenta de coletor ou de análise é fornecida por um parceiro [de aparelho virtual de rede.](https://azure.microsoft.com/solutions/network-appliances/) Para obter uma lista de soluções parceiras validadas para trabalhar com a rede virtual TAP, consulte [soluções parceiras.](#virtual-network-tap-partner-solutions)
A imagem que se segue mostra como funciona a rede virtual TAP. Pode adicionar uma configuração TAP numa interface de [rede](virtual-network-network-interface.md) que esteja ligada a uma máquina virtual implantada na sua rede virtual. O destino é um endereço IP de rede virtual na mesma rede virtual que a interface de rede monitorizada ou uma rede [virtual vigiada.](virtual-network-peering-overview.md) A solução de coletor para a rede virtual TAP pode ser implantada atrás de um equilibrador de carga interna Azure para uma elevada disponibilidade.
![Como funciona a rede virtual TAP](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar uma rede virtual TAP, deve ter recebido um e-mail de confirmação de que está inscrito na pré-visualização, e ter uma ou mais máquinas virtuais criadas utilizando o modelo de implementação [do Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e uma solução parceira para agregar o tráfego tap na mesma região do Azure. Se não tiver uma solução parceira na sua rede virtual, consulte [soluções parceiras](#virtual-network-tap-partner-solutions) para implementar uma. Pode utilizar o mesmo recurso de rede virtual TAP para agregar tráfego de várias interfaces de rede nas mesmas ou diferentes subscrições. Se as interfaces de rede monitorizadas estiverem em diferentes subscrições, as subscrições devem ser associadas ao mesmo inquilino do Azure Ative Directory. Além disso, as interfaces de rede monitorizadas e o ponto final de destino para agregação do tráfego TAP podem estar em redes virtuais vigiadas na mesma região. Se estiver a utilizar este modelo de implementação, certifique-se de que o [espremiamento da rede virtual](virtual-network-peering-overview.md) está ativado antes de configurar a rede virtual TAP.

## <a name="permissions"></a>Permissões

As contas que utiliza para aplicar a configuração TAP nas interfaces de rede devem ser atribuídas à função [de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída a partir do quadro seguinte:

| Ação | Name |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Necessário para criar, atualizar, ler e apagar um recurso TAP de rede virtual |
| Microsoft.Network/networkInterfaces/read | Obrigado a ler o recurso de interface de rede em que a TAP será configurada |
| Microsoft.Network/tapConfigurations/* | Necessário para criar, atualizar, ler e eliminar a configuração TAP numa interface de rede |


## <a name="virtual-network-tap-partner-solutions"></a>Soluções parceiras da rede virtual TAP

### <a name="network-packet-brokers"></a>Corretores de pacotes de rede

- [GigaVUE Cloud Suite para Azure](https://www.gigamon.com/solutions/cloud/public-cloud/gigavue-cloud-suite-azure.html)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Prismas de Nubeva](https://www.nubeva.com/azurevtap)
- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>Análise de segurança, gestão do desempenho da rede/aplicação

- [Segurança Acordada](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Nuvem de Stealthwatch Cisco](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cibersegurança](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Rio Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/marketplace-azure)
- [Segurança Noname](https://nonamesecurity.com/)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA NetWitness® Plataforma](https://www.rsa.com/content/dam/en/solution-brief/rsa-netwitness-platform-overview-for-federal-agencies.pdf)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar uma rede virtual TAP.](tutorial-tap-virtual-network-cli.md)
