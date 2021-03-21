---
title: O que é O Azure Route Server (Pré-visualização)?
description: Saiba como o Azure Route Server pode simplificar o encaminhamento entre o seu aparelho virtual de rede (NVA) e a sua rede virtual.
services: route-server
author: duongau
ms.service: route-server
ms.topic: overview
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: d868c064b96f58ab3febc1fd3b7f20b74d507cb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437166"
---
# <a name="what-is-azure-route-server-preview"></a>O que é O Azure Route Server (Pré-visualização)? 

O Azure Route Server simplifica o encaminhamento dinâmico entre o seu aparelho virtual de rede (NVA) e a sua rede virtual. Permite-lhe trocar informações de encaminhamento diretamente através do protocolo de encaminhamento do Border Gateway Protocol (BGP) entre qualquer NVA que suporte o protocolo de encaminhamento BGP e a Rede Definida de Software Azure (SDN) na Rede Virtual Azure (VNET) sem a necessidade de configurar manualmente ou manter as tabelas de rota. O Azure Route Server é um serviço totalmente gerido e configurado com alta disponibilidade.

> [!IMPORTANT]
> O Azure Route Server (Preview) encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Como funciona?

O diagrama seguinte ilustra como o Azure Route Server funciona com um SDWAN NVA e um NVA de segurança numa rede virtual. Uma vez estabelecido o presalto BGP, o Azure Route Server receberá uma rota no local (10.250.0.0/16) do aparelho SDWAN e uma rota predefinida (0.0.0.0/0) da firewall. Estas rotas são então configuradas automaticamente nos VMs na rede virtual. Como resultado, todo o tráfego destinado à rede no local será enviado para o aparelho SDWAN. Enquanto todo o tráfego ligado à Internet será enviado para a firewall. Na direção oposta, o Azure Route Server enviará o endereço de rede virtual (10.1.0.0/16) para ambos os NVAs. O aparelho SDWAN pode propagou-lo ainda mais para a rede no local.

![Diagrama mostrando O Servidor de Rota Azure configurado numa rede virtual.](./media/overview/route-server-overview.png)

## <a name="key-benefits"></a>Principais vantagens 

O Azure Route Server simplifica a configuração, gestão e implementação do seu NVA na sua rede virtual.  

* Já não precisa de atualizar manualmente a tabela de encaminhamento no seu NVA sempre que os seus endereços de rede virtuais forem atualizados. 

* Já não precisa de atualizar manualmente [as Rotas Definidas](../virtual-network/virtual-networks-udr-overview.md) pelo Utilizador sempre que o seu NVA anuncia novas rotas ou retira as antigas. 

* Pode consultar várias instâncias do seu NVA com o Azure Route Server. Pode configurar os atributos BGP no seu NVA e, dependendo do seu design (por exemplo, ativo para desempenho ou active-passivo para a resiliência), deixe o Azure Route Server saber qual é o caso NVA ativo ou qual é passivo. 

* A interface entre o NVA e o Azure Route Server baseia-se num protocolo padrão comum. Desde que o seu NVA suporte o BGP, pode espreitá-lo com o Azure Route Server. Para obter mais informações, consulte [os protocolos de encaminhamento suportados pelo Route Server](route-server-faq.md#protocol).

* Pode implementar o Azure Route Server em qualquer uma das suas redes virtuais novas ou existentes. 

## <a name="faq"></a>FAQ

Para perguntas frequentes sobre O Servidor de Rota Azure, consulte [O Azure Route Server FAQ](route-server-faq.md).

## <a name="next-steps"></a>Passos seguintes

- [Saiba como configurar o Azure Route Server](quickstart-configure-route-server-powershell.md)
- [Saiba como funciona o Azure Route Server com a Azure ExpressRoute e a Azure VPN](expressroute-vpn-support.md)
