---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908693"
---
Se estiver a trabalhar com o modelo de implementação do Resource Manager, pode alterar para o novo gateway SKUs. Quando altera a partir de um SKU de gateway legados para um novo SKU, elimine o gateway VPN existente e criar um novo gateway VPN.

Fluxo de trabalho:

1. Remova várias ligações a um gateway de rede virtual.
2. Elimine o gateway de VPN antigo.
3. Crie o gateway de VPN novo.
4. Atualize os seus dispositivos VPN no local com o novo endereço IP do gateway do VPN (para ligações de rede).
5. Atualize o valor do endereço IP do gateway para gateways de rede local VNet para VNet que se ligam a este gateway.
6. Transfira novos pacotes de configuração do cliente VPN para clientes de P2S que se ligam à rede virtual por meio deste gateway VPN.
7. Volte a criar as ligações a um gateway de rede virtual.

Considerações:

* Para mover para os novos SKU, o gateway de VPN tem de ser no modelo de implementação do Resource Manager.
* Se tiver um gateway de VPN clássico, deve continuar a utilizar os SKUs legados mais antigos para esse gateway, no entanto, pode redimensionar entre os SKUs legados. Não é possível alterar para os novos SKU.
* Terá períodos de indisponibilidade da conectividade quando altera de um SKU de legado para um novo SKU.
* Quando mudar para um novo SKU de gateway, o endereço IP público para o seu gateway VPN será alterado. Isto acontece mesmo que especifique o mesmo objeto de endereço IP público que utilizou anteriormente.