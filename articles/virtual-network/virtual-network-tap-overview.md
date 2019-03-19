---
title: Descrição geral de TOQUE de rede virtual do Azure | Documentos da Microsoft
description: Saiba mais sobre o teste de rede virtual. A rede virtual TOQUE fornece uma cópia em profundidade de tráfego de rede de máquina virtual que possam ser transmitido a um recoletor de pacote.
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
ms.date: 03/18/2019
ms.author: kaanan
ms.openlocfilehash: 45224b1b0ec4a4b3c93393c178f1f03baa58e10b
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189143"
---
# <a name="virtual-network-tap"></a>TAP de rede virtual

TOQUE (ponto de acesso de Terminal) de rede virtual do Azure permite-lhe para o stream continuamente o tráfego de rede de máquina virtual para uma ferramenta de recoletor ou análise da pacotes de rede. A ferramenta de recoletor ou análise é fornecida por um [aplicação de rede virtual](https://azure.microsoft.com/solutions/network-appliances/) parceiro. Para obter uma lista das soluções de parceiros são validadas para trabalhar com o teste de rede virtual, consulte [soluções de parceiros](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Rede virtual TOQUE são está atualmente em pré-visualização em todas as regiões do Azure. Para usar o teste de rede virtual, tem de se inscrever na pré-visualização, enviando um e-mail para <azurevnettap@microsoft.com> com o ID da subscrição. Receberá um e-mail assim que a sua subscrição tiver sido inscrita. Não possa utilizar a capacidade de até receber um e-mail de confirmação. Esta pré-visualização é fornecida sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Consulte a [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="virtual-network-tap-partner-solutions"></a>Soluções de parceiros de TOQUE de rede virtual

### <a name="network-packet-brokers"></a>Mediadores de pacotes de rede

- [Grande comutador de grandes volumes de monitorização de recursos de infraestrutura](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>Análise de segurança, gestão de desempenho de rede/aplicação

- [Segurança ativo](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch Cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [ExtraHop Reveal(x)](https://www.extrahop.com/company/tech-partners/microsoft/)
- [Fidelis Cibersegurança](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)
- [Plataforma de NetWitness® RSA](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

A seguinte imagem mostra como virtual de rede funciona de TOQUE. Pode adicionar uma configuração de TOQUE de um [interface de rede](virtual-network-network-interface.md) que está ligado a uma máquina virtual implementada na sua rede virtual. O destino é um endereço IP de rede virtual na mesma rede virtual que a interface de rede monitorizados ou uma [em modo de peering virtual](virtual-network-peering-overview.md) rede. A solução de recoletor para teste de rede virtual pode ser implementada por trás de um [Balanceador de carga interno do Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#concepts) para elevada disponibilidade. Para avaliar as opções de implementação para a solução individual, consulte [soluções de parceiros](#virtual-network-tap-partner-solutions).

![TOQUE de rede virtual como funciona](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um teste de rede virtual, deve receber um e-mail de confirmação que estão inscritos na pré-visualização e ter um ou mais máquinas virtuais criadas com [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) modelo de implementação e de um parceiro solução para agregar o tráfego de TOQUE na região EUA. Se não tiver uma solução de parceiro na sua rede virtual, veja [soluções de parceiros](#virtual-network-tap-partner-solutions) para implementar uma. Pode utilizar a mesma rede virtual recursos de TOQUE para agregar o tráfego de várias interfaces de rede nas subscrições idêntica ou diferentes. Se as interfaces de rede monitorizada estão em subscrições diferentes, as subscrições têm de estar associadas ao mesmo inquilino do Azure Active Directory. Além disso, as interfaces de rede monitorizada e o ponto de extremidade de destino para agregar o tráfego de TOQUE podem ser em redes virtuais em modo de peering na mesma região. Se estiver a utilizar este modelo de implementação Certifique-se de que o [peering de rede virtual](virtual-network-peering-overview.md) está ativada antes de configurar o teste de rede virtual.

## <a name="permissions"></a>Permissões

As contas que utilizar para aplicar a configuração de TOQUE em interfaces de rede tem de ser atribuídas para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuída as ações necessárias a partir da seguinte tabela:

| Ação | Name |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Necessário para criar, atualizar, ler e eliminar uma rede virtual do recurso de TOQUE |
| Microsoft.Network/networkInterfaces/read | Necessário para o recurso de interface de rede em que será configurado o TOQUE de leitura |
| Microsoft.Network/tapConfigurations/* | Necessário para criar, atualizar, ler e eliminar a configuração de TOQUE numa interface de rede |

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar uma rede virtual TOQUE](tutorial-tap-virtual-network-cli.md).
