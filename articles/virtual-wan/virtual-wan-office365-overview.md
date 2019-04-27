---
title: Plano de controlo do Office 365 na WAN Virtual do Azure
description: Saiba mais sobre o plano de controlo do Office 365 na Virtual WAN.
author: cherylmc
ms.service: virtual-wan
services: virtual-wan
ms.topic: article
ms.date: 9/24/2018
ms.author: cherylmc
ms.openlocfilehash: cb91c1364a91c101ecf8362acd7aab01440143fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60458607"
---
# <a name="office-365-control-plane-in-virtual-wan"></a>Plano de controlo do Office 365 em WAN Virtual

Os clientes WAN virtual com determinados dispositivos SDWAN podem configurar políticas de abertas de Internet do Office 365 para o tráfego fidedigno no portal do Azure. Isso permite:
- Tráfego do O365 para introduzir a rede da Microsoft perto de utilizador, dando a experiência de usuário ideal.
- Evita hauling-back de tráfego e de cabelo pining, poupando assim os custos da WAN.
- A distribuição nos princípios de conectividade do Office 365.

## <a name="faqs"></a>FAQs
### <a name="what-is-the-customer-benefit"></a>O que é o benefício de cliente?
Utilizar esta funcionalidade em Virtual WAN, os clientes podem agora especificar as categorias de tráfego do Office 365 que confiam para abertas de internet direta. Este fidedigno proxies do O365 tráfego irá ignorar e a rota diretamente a partir da localização de utilizador para o POP de Microsoft mais próximo. Isso evita hauling-back de tráfego e de cabelo pining, assim, fornecer a experiência de usuário ideal e guardar os custos da WAN. 

### <a name="what-are-the-office-365-traffic-categories"></a>Quais são as categorias de tráfego do Office 365?
Pontos finais do Office 365 representam os endereços de rede e sub-redes. Pontos de extremidade podem ser URLs, intervalos de endereços IP ou IP. URLs podem ser um FQDN como *account.office.net*, ou como um URL de caráter universal **. office365.com*. Pontos finais são separados em três categorias - **otimizar**, **permitir**, e **predefinido**, consoante o nível de importância. Mais detalhes sobre as categorias de ponto final [aqui](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles#BKMK_Categories).

### <a name="which-office-365-traffic-category-is-recommended-by-microsoft-for-direct-internet-breakout"></a>Que categoria de tráfego do Office 365 é recomendada pela Microsoft para direto abertas de internet?
O **otimizar** categoria é os pontos de extremidade de rede mais críticos e é necessária para ignorar a quebra SSL e inspecionar e outros dispositivos de segurança de rede. Ele deve ter a saída de Internet direta perto dos utilizadores. Estes pontos finais representam cenários do Office 365, o mais sensíveis a desempenho, latência e disponibilidade de rede. Esta categoria inclui um conjunto pequeno (na ordem de ~ 10) de chave de um conjunto definido de sub-redes IP e URLs dedicadas às cargas de trabalho principal do Office 365 como o Exchange Online, SharePoint Online, Skype para empresas Online e Microsoft Teams. 

O **permitir** categoria é recomendada para a saída de Internet direta também. Permita o tráfego de rede pode tolerar entanto alguma latência de rede. Pontos finais nas categorias a otimizar e permitir que todos alojados em datacenters da Microsoft e geridos como parte do Office 365. A categoria de padrão pode ser direcionada para uma localização de saída de Internet predefinido e não necessitam de saída de Internet direta ou ignorar de quebra SSL e inspecionar os dispositivos.

### <a name="how-do-i-set-my-o365-policies-via-virtual-wan"></a>Como posso definir minha políticas do Office 365 através de Virtual WAN?
Pode ativar políticas através de **Virtual WAN** -> **definições** -> **Configuration** separador. Aqui, pode especificar seu categorias preferenciais de tráfego do Office 365 para abertas de internet direta.

![Configurar o plano de controlo do Office 365 na Virtual WAN](media/virtual-wan-office365-overview/configure-office365-control-plane.png)

### <a name="how-does-this-work"></a>Como isso funciona?

1.  Tráfego do Office 365 insere a rede da Microsoft perto de usuário, proporcionando uma experiência ideal.
2.  Políticas de rota são consumidas por SDWAN. Em seguida, ignora os proxies de segurança para as categorias fidedignos e executa local abertas direta para essas categorias.
3.  Back-hauling e pining de cabelo de tráfego são evitadas a guardar os custos da WAN.

### <a name="which-partner-devices-support-this-via-virtual-wan"></a>Os dispositivos que parceiro suportam isso através de Virtual WAN?
Atualmente, o Citrix suporta estas políticas através de Virtual WAN.

### <a name="what-happens-to-the-remaining-categories-of-untrusted-o365-traffic"></a>O que acontece às categorias restantes de tráfego (não fidedigno) do Office 365?
Restantes do Office 365 tráfego seguirá o caminho de tráfego de internet de padrão de clientes.

### <a name="what-if-i-have-already-specified-my-o365-policies-via-my-sdwan-provider"></a>E se eu já especificou meu políticas do Office 365 através do meu provedor de SDWAN?
Se especificar políticas através de SDWAN UX e WAN Virtual do Azure, as políticas definidas no Virtual WAN irão ter precedência.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [Virtual WAN](virtual-wan-about.md).