---
title: Arquitetura de conectividade SD-WAN
titleSuffix: Azure Virtual WAN
description: Saiba sobre a interligação de um SD-WAN privado com o Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/08/2020
ms.author: cherylmc
ms.openlocfilehash: 15e44b9c048f167935fe8660228581e5bac0f43d
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006264"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>Arquitetura de conectividade SD-WAN com Bluee Virtual WAN

O Azure Virtual WAN é um serviço de networking que reúne muitos serviços de conectividade e segurança na nuvem com uma única interface operacional. Estes serviços incluem sucursal (via VPN site-to-site), utilizador remoto (VPN ponto-a-site), conectividade privada (ExpressRoute) bem como conectividade transitiva intra-nuvem para vnets, VPN e ExpressRoute interconectividade, Routing, Firewall Azure e encriptação para conectividade privada.

Embora o Próprio Bluee Virtual WAN seja um Wan definido por software (SD-WAN), também foi concebido para permitir uma interligação perfeita com as tecnologias e serviços SD-WAN baseados em instalações. Muitos desses serviços são oferecidos pelo nosso ecossistema [virtual WAN](virtual-wan-locations-partners.md) e pelos parceiros de Serviços Geridos em Rede Azure [(MSPs)](../networking/networking-partners-msp.md). As empresas que estão a transformar o seu WAN privado em SD-WAN têm opções ao interligar o seu SD-WAN privado com o Azure Virtual WAN. As empresas podem escolher entre estas opções:

* Modelo de Interligação Direta
* Modelo interligação indireto
* Modelo HÍBRIDO WAN gerido usando o seu fornecedor de serviços gerido favorito [MSP](../networking/networking-partners-msp.md)

Em todos estes casos, a interligação do WAN virtual com o SD-WAN é semelhante do lado da conectividade, mas pode variar no lado da orquestração e operacional.

## <a name="direct-interconnect-model"></a><a name="direct"></a>Modelo interligação direta

![Modelo de interligação direta](./media/sd-wan-connectivity-architecture/direct.png)

Neste modelo de arquitetura, o equipamento de instalações de cliente da sucursal SD-WAN (CPE) está diretamente ligado aos centros de WAN virtuais através de ligações IPsec. O CPE da sucursal também pode ser ligado a outros ramos através do SD-WAN privado, ou alavancar o WAN virtual para a conectividade da sucursal. Os balcões que necessitem de aceder às suas cargas de trabalho em Azure poderão aceder direta e seguramente ao Azure através do ou do túnel IPsec que são encerrados no centro(s) Virtual WAN.

Os parceiros SD-WAN CPE podem ativar a automatização de forma a automatizar a conectividade IPsec normalmente entediante e propensa a erros dos respetivos dispositivos CPE. A automatização permite que o controlador SD-WAN fale com o Azure através da API Virtual WAN para configurar os locais de WAN virtual, bem como empurrar a configuração necessária do túnel IPsec para os CPEs do ramo. Consulte [as diretrizes](virtual-wan-configure-automation-providers.md) de Automação para a descrição da automatização de interconexão Virtual WAN por vários parceiros SD-WAN.

O CPE SD-WAN continua a ser o local onde a otimização do tráfego, bem como a seleção de caminhos são implementadas e executadas. 

Neste modelo, alguma otimização de tráfego proprietária do fornecedor com base nas características de tráfego em tempo real pode não ser suportada porque a conectividade com o WAN virtual é sobre iPsec e o IPsec VPN é terminado no gateway Virtual WAN VPN. Por exemplo, a seleção dinâmica de caminhos no cpe da sucursal é viável devido ao dispositivo de ramificação que troca várias informações de pacotes de rede com outro nó SD-WAN, identificando assim a melhor ligação para usar para vários tráfegos priorizados dinamicamente no ramo. Esta funcionalidade pode ser útil em áreas onde é necessária a otimização da última milha (filial do Microsoft POP mais próximo).

Com o Virtual WAN, os utilizadores podem obter a Azure Path Selection, que é a seleção de caminhos baseado seleções baseadas em políticas em várias ligações ISP desde o CPE do ramo até aos gateways VVpN virtual WAN. O WAN virtual permite a configuração de múltiplas ligações (caminhos) para o mesmo CPE da filial SD-WAN, cada link terminando em diferentes interfaces IP públicas do CPE SD-WAN. Os fornecedores sD-WAN podem aproveitar esta funcionalidade para selecionar o caminho mais ideal para o Azure, com base em políticas de tráfego específicas para estes caminhos.

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>Modelo interligação indireta

![Modelo de interligação indireta](./media/sd-wan-connectivity-architecture/indirect.png)

Neste modelo de arquitetura, os CPEs da filial SD-WAN estão indiretamente ligados aos centros de WAN virtuais. Como mostra o número, um CPE virtual SD-WAN é implantado numa empresa VNet. Este CPE virtual está, por sua vez, ligado ao(s) hub(s) virtual WAN utilizando IPsec. O CPE virtual serve como uma porta de entrada SD-WAN para Azure. Os balcões que precisam de aceder às suas cargas de trabalho em Azure poderão aceder-lhes através do portal v-CPE.

Uma vez que a conectividade com o Azure é através do gateway v-CPE (NVA), todo o tráfego de e para vNets de carga azure para outras sucursais SD-WAN vão através da NVA. Neste modelo, o utilizador é responsável pela gestão e operação do SD-WAN NVA, incluindo elevada disponibilidade, escalabilidade e encaminhamento.
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>Modelo WAN híbrido gerido

![Modelo wan híbrido gerido](./media/sd-wan-connectivity-architecture/hybrid.png)

Neste modelo de arquitetura, as empresas podem aproveitar um serviço SD-WAN gerido oferecido por um parceiro de Prestador de Serviços Gerido (MSP). Este modelo é semelhante aos modelos diretos ou indiretos acima descritos. No entanto, neste modelo, o design, orquestração e operações SD-WAN são entregues pelo Provedor SD-WAN.

Os [parceiros da Azure Networking MSP](../networking/networking-partners-msp.md) podem utilizar o [Farol Azure](https://azure.microsoft.com/services/azure-lighthouse/) para implementar o serviço SD-WAN e Virtual WAN na subscrição azure do cliente empresarial, bem como operar o HÍBRIDO WAN de ponta a ponta em nome do cliente. Estes MSPs também podem ser capazes de implementar o Azure ExpressRoute no WAN virtual e operá-lo como um serviço gerido de ponta a ponta.

## <a name="additional-information"></a>Informações adicionais

* [Incluir FAQ](virtual-wan-faq.md)
* [Resolver a conectividade remota](work-remotely-support.md)