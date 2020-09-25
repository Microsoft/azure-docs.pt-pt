---
title: Arquitetura de conectividade SD-WAN
titleSuffix: Azure Virtual WAN
description: Saiba como interligar um SD-WAN privado com a Azure Virtual WAN
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: sukishen
ms.openlocfilehash: 87e9549419bccc36d743871755e782a71e93e5e0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267470"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>Arquitetura de conectividade SD-WAN com Azure Virtual WAN

Azure Virtual WAN é um serviço de networking que reúne muitos serviços de conectividade e segurança na nuvem com uma única interface operacional. Estes serviços incluem sucursal (via VPN site-to-site), utilizador remoto (VPN ponto a local), conectividade privada (ExpressRoute), conectividade transitiva intra-nuvem para Vnets, interconectividade VPN e ExpressRoute, encaminhamento, Firewall Azure e encriptação para conectividade privada.

Embora o Azure Virtual WAN seja um WAN definido por software (SD-WAN), também foi concebido para permitir uma interligação perfeita com as tecnologias e serviços SD-WAN baseados nas instalações. Muitos destes serviços são oferecidos pelo nosso ecossistema [Virtual WAN](virtual-wan-locations-partners.md) e parceiros de Serviços Geridos em Rede Azure [(MSPs)](../networking/networking-partners-msp.md). As empresas que estão a transformar o seu WAN privado em SD-WAN têm opções ao interligar o seu SD-WAN privado com o Azure Virtual WAN. As empresas podem escolher entre estas opções:

* Modelo de interligação direta
* Modelo de interligação indireto
* Modelo WAN Híbrido Gerido usando o seu fornecedor de serviços gerido favorito [MSP](../networking/networking-partners-msp.md)

Em todos estes casos, a interligação de VIRTUAL WAN com SD-WAN é semelhante do lado da conectividade, mas pode variar no lado da orquestração e operacional.

## <a name="direct-interconnect-model"></a><a name="direct"></a>Modelo de interligação direta

![Modelo de interligação direta](./media/sd-wan-connectivity-architecture/direct.png)

Neste modelo de arquitetura, o equipamento de instalações de clientes da sucursal SD-WAN (CPE) está diretamente ligado aos hubs VIRTUAIS WAN através de ligações IPsec. O cpe de ramo também pode ser ligado a outros ramos através do SD-WAN privado, ou alavancar o WAN Virtual para a conectividade do ramo. Os balcões que necessitem de aceder às suas cargas de trabalho em Azure poderão aceder direta e seguramente ao Azure através do(s) túnel(s) IPsec(s) que são encerrados no(s) hub(s) Virtual WAN.

Os parceiros SD-WAN CPE podem permitir a automatização de forma a automatizar a conectividade IPsec normalmente tediosa e propensa a erros a partir dos respetivos dispositivos CPE. A automatização permite que o controlador SD-WAN fale com o Azure através da API Virtual WAN para configurar os sites DE WAN Virtuais, bem como empurrar a configuração necessária do túnel IPsec para os CPEs de ramo. Consulte [as diretrizes](virtual-wan-configure-automation-providers.md) de automatização para a descrição da automatização de interconexão Virtual WAN por vários parceiros SD-WAN.

O SD-WAN CPE continua a ser o local onde a otimização de tráfego, bem como a seleção de caminhos são implementadas e aplicadas. 

Neste modelo, alguma otimização de tráfego proprietário do fornecedor com base nas características de tráfego em tempo real pode não ser suportada porque a conectividade com o WAN virtual é mais de IPsec e o IPsec VPN é terminado no gateway VPN Virtual WAN. Por exemplo, a seleção dinâmica de caminhos no cpe da sucursal é viável devido ao dispositivo de ramificação que troca várias informações de pacotes de rede com outro nó SD-WAN, identificando assim a melhor ligação a utilizar para vários tráfego prioritários dinamicamente no ramo. Esta funcionalidade pode ser útil em áreas onde é necessária otimização de última milha (ramo para o Microsoft POP mais próximo).

Com o VIRTUAL WAN, os utilizadores podem obter a Azure Path Selection, que é a seleção de caminhos baseada em políticas através de várias ligações ISP desde o cpe da filial até gateways de VPN NVPN virtual. O WAN virtual permite a configuração de múltiplos links (caminhos) a partir do mesmo CPE de filial SD-WAN; cada ligação representa uma ligação de túnel duplo de um IP público único do CPE SD-WAN a duas instâncias diferentes do gateway Azure Virtual WAN VPN. Os fornecedores SD-WAN podem implementar o caminho mais ideal para o Azure, com base nas políticas de tráfego definidas pelo seu motor de política nas ligações CPE. Na extremidade Azure, todas as ligações que entram são tratadas igualmente.

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>Modelo indireto de interligação

![Modelo de interligação indireto](./media/sd-wan-connectivity-architecture/indirect.png)

Neste modelo de arquitetura, os CPEs de filial SD-WAN estão indiretamente ligados aos centros VIRTUAIS WAN. Como mostra o número, um CPE virtual SD-WAN é implantado numa VNet da empresa. Este CPE virtual está, por sua vez, ligado ao hub(s) Virtual WAN usando o IPsec. O CPE virtual serve como uma porta de entrada SD-WAN para Azure. Os balcões que necessitem de aceder às suas cargas de trabalho em Azure poderão aceder-lhes através do portal v-CPE.

Uma vez que a conectividade com a Azure é através do gateway v-CPE (NVA), todo o tráfego de e de Azure vNets de carga de trabalho para outros ramos SD-WAN vão através do NVA. Neste modelo, o utilizador é responsável pela gestão e exploração do SD-WAN NVA, incluindo elevada disponibilidade, escalabilidade e encaminhamento.
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>Modelo HÍBRIDO GERIDO WAN

![Modelo WAN híbrido gerido](./media/sd-wan-connectivity-architecture/hybrid.png)

Neste modelo de arquitetura, as empresas podem alavancar um serviço gerido SD-WAN oferecido por um parceiro de Prestador de Serviços Gerido (MSP). Este modelo é semelhante aos modelos diretos ou indiretos acima descritos. No entanto, neste modelo, o design, orquestração e operações SD-WAN são entregues pelo Provedor SD-WAN.

[Os parceiros Azure Networking MSP](../networking/networking-partners-msp.md) podem usar o [Farol Azure](https://azure.microsoft.com/services/azure-lighthouse/) para implementar o serviço SD-WAN e Virtual WAN na subscrição Azure do cliente da empresa, bem como operar o WAN híbrido de ponta a ponta em nome do cliente. Estes MSPs também podem ser capazes de implementar o Azure ExpressRoute no WAN Virtual e operá-lo como um serviço gerido de ponta a ponta.

## <a name="additional-information"></a>Informações adicionais

* [Incluir FAQ](virtual-wan-faq.md)
* [Resolução da conectividade remota](work-remotely-support.md)
