---
title: Visão geral da rede virtual Azure TAP  Microsoft Docs
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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 47db03460ad3c5194a5445f0b25cb8e742e60c21
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279614"
---
# <a name="virtual-network-tap"></a>TAP de rede virtual

A rede virtual Azure TAP (Terminal Access Point) permite-lhe transmitir continuamente o tráfego da sua rede virtual de máquinas para uma ferramenta de coleção de pacotes de rede ou de análise. A ferramenta de coleção ou análise é fornecida por um parceiro de aparelho virtual de [rede.](https://azure.microsoft.com/solutions/network-appliances/) Para obter uma lista de soluções parceiras validadas para trabalhar com a rede virtual TAP, consulte [soluções parceiras.](#virtual-network-tap-partner-solutions)

> [!IMPORTANT]
> A rede virtual TAP encontra-se atualmente em pré-visualização em todas as regiões do Azure. Para utilizar a rede virtual TAP, tem de se inscrever na pré-visualização enviando um e-mail para <azurevnettap@microsoft.com> com o seu ID de subscrição. Receberá um e-mail assim que a sua subscrição tiver sido inscrita. Não poderá utilizar a capacidade até receber um e-mail de confirmação. Esta pré-visualização é fornecida sem um acordo de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Consulte os [Termos De Utilização Suplementares para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) do Microsoft Azure para obter mais detalhes.

## <a name="virtual-network-tap-partner-solutions"></a>Soluções de parceiros tap de rede virtual

### <a name="network-packet-brokers"></a>Corretores de pacotes de rede

- [Tecido de monitorização big switch grande](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Prismas de Nubeva](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Análise de segurança, gestão de desempenho de rede/aplicação

- [Segurança Acordada](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Nuvem de stealthwatch cisco](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal (x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Cibersegurança fidelis](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Rio Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [Plataforma RSA NetWitness®](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

A imagem que se segue mostra como funciona a rede virtual TAP. Pode adicionar uma configuração TAP numa interface de [rede](virtual-network-network-interface.md) que está ligada a uma máquina virtual implantada na sua rede virtual. O destino é um endereço IP de rede virtual na mesma rede virtual que a interface de rede monitorizada ou uma rede [virtual peered.](virtual-network-peering-overview.md) A solução de coleção para rede virtual TAP pode ser implantada por trás de um equilíbrio de Carga Interna Azure para alta disponibilidade. Para avaliar as opções de implementação para solução individual, consulte [soluções parceiras.](#virtual-network-tap-partner-solutions)

![Como funciona a rede virtual TAP](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar uma rede virtual TAP, deve ter recebido um correio de confirmação que está matriculado na pré-visualização, e ter uma ou mais máquinas virtuais criadas utilizando o modelo de implementação do Gestor de [Recursos Azure](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e uma solução parceira para agregar o tráfego da TAP na mesma região azul. Se não tiver uma solução parceira na sua rede virtual, consulte soluções de [parceiropara](#virtual-network-tap-partner-solutions) implementar uma. Pode utilizar o mesmo recurso TAP da rede virtual para agregar o tráfego de várias interfaces de rede nas mesmas ou diferentes subscrições. Se as interfaces de rede monitorizadas estiverem em assinaturas diferentes, as subscrições devem ser associadas ao mesmo inquilino do Diretório Ativo Azure. Além disso, as interfaces de rede monitorizadas e o ponto final de destino para agregação do tráfego da TAP podem estar em redes virtuais espreitadas na mesma região. Se estiver a utilizar este modelo de implementação, certifique-se de que o [epeering de rede virtual](virtual-network-peering-overview.md) está ativado antes de configurar a rede virtual TAP.

## <a name="permissions"></a>Permissões

As contas que utiliza para aplicar a configuração TAP nas interfaces de rede devem ser atribuídas à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações necessárias a partir do quadro seguinte:

| Ação | Nome |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Necessário para criar, atualizar, ler e eliminar um recurso TAP de rede virtual |
| Microsoft.Network/networkInterfaces/read | Necessário para ler o recurso de interface de rede em que a TAP será configurada |
| Microsoft.Network/tapConfigurations/* | Necessário para criar, atualizar, ler e eliminar a configuração TAP numa interface de rede |

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar uma rede virtual TAP.](tutorial-tap-virtual-network-cli.md)
