---
title: Visão geral do toque da rede virtual do Azure | Microsoft Docs
description: Saiba mais sobre o toque da rede virtual. O toque de rede virtual fornece uma cópia profunda do tráfego de rede da máquina virtual que pode ser transmitida para um coletor de pacotes.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 99cd9fc1da009660023a246c5210e7f54bdebcfd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177418"
---
# <a name="virtual-network-tap"></a>TAP de rede virtual

O TAP (ponto de acesso ao terminal) da rede virtual do Azure permite transmitir continuamente o tráfego de rede da máquina virtual para uma ferramenta de análise ou coletor de pacotes de rede. O coletor ou a ferramenta de análise é fornecida por um parceiro de [solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/) . Para obter uma lista de soluções de parceiros que são validadas para trabalhar com o toque de rede virtual, consulte [soluções de parceiros](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> O toque de rede virtual está atualmente em visualização em todas as regiões do Azure. Para usar o toque de rede virtual, você deve se registrar na versão prévia enviando um email para <azurevnettap@microsoft.com> com sua ID de assinatura. Receberá um e-mail assim que a sua subscrição tiver sido inscrita. Você não poderá usar a funcionalidade até receber um email de confirmação. Essa visualização é fornecida sem um contrato de nível de serviço e não deve ser usada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Consulte os [termos de uso complementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="virtual-network-tap-partner-solutions"></a>Redes virtuais toque em soluções de parceiros

### <a name="network-packet-brokers"></a>Agentes de pacotes de rede

- [Grande malha de monitoramento grande de switch](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva prisms](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Análise de segurança, gerenciamento de desempenho de aplicativos/rede

- [Segurança de ativação](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Nuvem do Cisco Stealthwatch](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis segurança cibernética](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [LANGuardian NetFort](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [VSTREAM do NetScout]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [Plataforma RSA nettestemunha®](https://www.rsa.com/azure)
- [Vectra cognito](https://vectra.ai/microsoftazure)

A imagem a seguir mostra como o toque da rede virtual funciona. Você pode adicionar uma configuração de toque em um [adaptador de rede](virtual-network-network-interface.md) que está anexado a uma máquina virtual implantada em sua rede virtual. O destino é um endereço IP de rede virtual na mesma rede virtual que a interface de rede monitorada ou uma rede [virtual emparelhada](virtual-network-peering-overview.md) . A solução do coletor para o toque da rede virtual pode ser implantada por trás de um [balanceador de carga interno do Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#concepts) para alta disponibilidade. Para avaliar as opções de implantação para a solução individual, consulte [soluções de parceiros](#virtual-network-tap-partner-solutions).

![Como o toque da rede virtual funciona](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um toque de rede virtual, você deve ter recebido um email de confirmação que você está inscrito na versão prévia e ter uma ou mais máquinas virtuais criadas usando [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) modelo de implantação e uma solução de parceiro para agregar o tráfego de toque na mesma região do Azure. Se você não tiver uma solução de parceiro em sua rede virtual, consulte [soluções de parceiros](#virtual-network-tap-partner-solutions) para implantar uma. Você pode usar o mesmo recurso de toque de rede virtual para agregar o tráfego de várias interfaces de rede na mesma assinatura ou em assinaturas diferentes. Se as interfaces de rede monitoradas estiverem em assinaturas diferentes, as assinaturas deverão ser associadas ao mesmo locatário Azure Active Directory. Além disso, as interfaces de rede monitoradas e o ponto de extremidade de destino para agregar o tráfego de toque podem estar em redes virtuais emparelhadas na mesma região. Se você estiver usando esse modelo de implantação, verifique se o [emparelhamento de rede virtual](virtual-network-peering-overview.md) está habilitado antes de configurar o toque da rede virtual.

## <a name="permissions"></a>Permissões

As contas usadas para aplicar a configuração TAP em interfaces de rede devem ser atribuídas à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações necessárias da tabela a seguir:

| Ação | Nome |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Necessário para criar, atualizar, ler e excluir um recurso de toque de rede virtual |
| Microsoft.Network/networkInterfaces/read | Necessário para ler o recurso de interface de rede no qual o toque será configurado |
| Microsoft.Network/tapConfigurations/* | Necessário para criar, atualizar, ler e excluir a configuração de toque em uma interface de rede |

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um toque de rede virtual](tutorial-tap-virtual-network-cli.md).
