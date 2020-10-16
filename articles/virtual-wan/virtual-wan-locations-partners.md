---
title: Parceiros e localizações Azure Virtual WAN Microsoft Docs
description: Este artigo contém uma lista de parceiros Azure Virtual WAN e localizações do hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: c689c83e50a42885900f62d1a65d0aa75f36f2ec
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014036"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Parceiros virtuais wan e localizações de hub virtual

Este artigo fornece informações sobre regiões e parceiros suportados por WAN virtual para a conectividade no Virtual Hub.

A WAN Virtual do Azure é um serviço de rede que fornece conectividade ramo a ramo otimizada e automatizada através do Azure. A WAN Virtual permite-lhe ligar e configurar dispositivos de ramo para comunicar com o Azure. Isto pode ser feito manualmente, ou utilizando dispositivos de fornecedor através de um parceiro Virtual WAN. A utilização de dispositivos parceiros permite-lhe facilitar a utilização, a simplificação da conectividade e a gestão da configuração.

A conectividade a partir do dispositivo no local é estabelecida de forma automatizada para o Centro Virtual. Um hub virtual é uma rede virtual gerida pela Microsoft. O hub contém vários pontos finais de serviço para ativar a conectividade da rede no local (vpnsite). Só se pode ter um centro por região.

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>Automatização de conectividade IPSec de filiais de parceiros

Os dispositivos que se ligam ao Azure Virtual WAN têm automatização incorporada para se conectarem. Isto é tipicamente configurado na UI de gestão de dispositivos (ou equivalente), que configura a conectividade e a gestão de configuração entre o dispositivo de filial VPN para um ponto final Azure Virtual Hub VPN (gateway VPN).

A seguinte automatização de alto nível é criada no centro de consola/gestão do dispositivo:

* Permissões adequadas para o dispositivo aceder ao Grupo de Recursos Azure Virtual WAN
* Upload do dispositivo branch para Azure Virtual WAN
* Download automático de informações de conectividade Azure
* Configuração do dispositivo de filial no local 

Alguns parceiros de conectividade podem estender a automatização para incluir a criação do Azure Virtual Hub VNet e VPN Gateway. Se quiser saber mais sobre automação, consulte [as diretrizes de Automação para parceiros VIRTUAIS WAN.](virtual-wan-configure-automation-providers.md)

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>Parceiros de conectividade IPSec de filial

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Os seguintes parceiros estão agendados no nosso roteiro com base numa folha de termos assinada entre as empresas que indica o âmbito de trabalho para automatizar a conectividade IPsec entre o dispositivo parceiro e a Azure Virtual WAN VPN Gateways : 128 Technologies, Arista, F5 Networks, Oracle SD-WAN (Talari) e SharpLink.

## <a name="partners-with-integrated-virtual-hub-offerings"></a>Parceiros com ofertas integradas de Hub Virtual
Além de ter conectividade IPSec de filial automatizada, alguns parceiros oferecem **Aparelhos Virtuais de Rede (NVAs)** que podem ser integrados diretamente no hub Azure Virtual WAN.  Isto permite aos clientes a opção de terminar as suas ligações de filial num aparelho de terceiros compatível no Centro Virtual.  

Os parceiros que oferecem NVA no Centro Virtual WAN devem:

* Implementou a IPSec Connectivity Automation a partir do seu dispositivo de filial e inserei a sua oferta NVA ao hub Azure Virtual WAN.
* Tenha uma oferta de aparelho virtual de rede disponível no Azure Marketplace.

Se você é um parceiro e tem dúvidas sobre o NVA Gerido na oferta do Centro Virtual, por favor contacte-nos enviando e-mail para vwannvaonboarding@microsoft.com

## <a name="integrated-virtual-hub-nva-partners"></a>Parceiros NVA integrados do Hub Virtual
Estes parceiros têm ofertas **de Aplicação Gerida** que estão agora disponíveis para implementar no hub virtual WAN.

|Parceiros|Guia de configuração/como/implantação|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Guia de implantação de Barracuda CloudGen WAN](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Cisco Cloud Service Router (CSR) VWAN](https://aka.ms/ciscoMarketPlaceOffer)| Durante a pré-visualização pública da Cisco Cloud Services (CSR) WAN no hub da VWAN, a Cisco exige que o cliente final se registe como cliente Cisco EFT (Early Field Trial), enviando um vwan_public_preview@external.cisco.com e-mail e solicitando o vManage Deployment Guide. |

Os seguintes parceiros estão programados para trazer a NVA nas ofertas do Virtual Hub num futuro próximo: Citrix, Versa Networks e VeloCloud.

## <a name="locations"></a><a name="locations"></a>Localizações

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o VIRTUAL WAN, consulte o [VIRTUAL WAN FAQ](virtual-wan-faq.md).

* Para obter mais informações sobre como automatizar a conectividade com a Azure Virtual WAN, consulte [as diretrizes de Automação para parceiros VIRTUAIS WAN](virtual-wan-configure-automation-providers.md).
