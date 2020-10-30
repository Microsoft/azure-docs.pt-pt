---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: f37876a0b6b7e8b520b5b6428381ebaf7b5b5c24
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042591"
---
Pode utilizar o mesmo pacote de configuração de cliente VPN em cada computador cliente Windows, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operativos clientes suportados, consulte a secção Ponto-a-Local da [VPN Gateway FAQ](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Tem de ter direitos de administrador no computador cliente Windows a partir do qual pretende ligar.
>

Utilize os seguintes passos para configurar o cliente VPN do Windows nativo para autenticação de certificado:

1. Selecione os ficheiros de configuração do cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador "VpnClientSetupAmd64". Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador "VpnClientSetupX86". 
1. Faça duplo clique no pacote para instalá-lo. Se vir um popup smartScreen, clique em **Mais informações** e, em seguida, **corra de qualquer maneira** .
1. No computador cliente, navegue para **Definições de Rede** e clique em **VPN** . A ligação VPN mostra o nome da rede virtual à qual se liga.
1. Antes de tentar ligar, certifique-se de que instalou um certificado de cliente no computador cliente. Se utilizar o tipo de autenticação de certificados nativa do Azure, é preciso um certificado de cliente para a autenticação.