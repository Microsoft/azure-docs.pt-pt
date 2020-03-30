---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184094"
---
### <a name="to-modify-the-local-network-gateway-ip-address---no-gateway-connection"></a><a name="gwipnoconnection"></a>Para modificar o endereço IP de gateway da rede local - sem ligação gateway

Utilize o exemplo para modificar um gateway de rede local que não tem uma ligação de gateway. Ao modificar este valor, também pode modificar os prefixos de endereços em simultâneo.

1. No recurso Gateway da rede local, na secção **Definições,** clique na **Configuração**.
2. Na caixa de **endereços IP,** modifique o endereço IP.
3. Clique em **Guardar** para guardar as definições.

### <a name="to-modify-the-local-network-gateway-ip-address---existing-gateway-connection"></a><a name="gwipwithconnection"></a>Para modificar o endereço IP de gateway da rede local - conexão de gateway existente

Para modificar um portal de rede local que tenha uma ligação, é necessário remover primeiro a ligação. Depois de a ligação ser removida, pode modificar o endereço IP do gateway e recriar uma nova ligação. Também pode modificar os prefixos de endereços em simultâneo. Este procedimento resulta num período de indisponibilidade da ligação VPN. Ao modificar os endereços IP de gateway, não precisa de eliminar o gateway de VPN. Só tem de remover a ligação.
 
#### <a name="1-remove-the-connection"></a>1. Retire a ligação.

1. No recurso Gateway da rede local, na secção **Definições,** clique em **Ligações**.
2. Clique no **...** na linha para a ligação e, em seguida, clique em **Apagar**.
3. Clique em **Guardar** para guardar as suas definições.

#### <a name="2-modify-the-ip-address"></a>2. Modificar o endereço IP.

Também pode modificar os prefixos de endereços em simultâneo.

1. Na caixa de **endereços IP,** modifique o endereço IP.
2. Clique em **Guardar** para guardar as definições.

#### <a name="3-recreate-the-connection"></a>3. Recriar a ligação.

1. Navegue para o Portal da Rede Virtual para o seu VNet. (Não o Gateway da Rede Local.)
2. No Gateway da Rede Virtual, na secção **Definições,** clique em **Ligações**.
3. Clique no **+ Adicionar** para abrir a lâmina de **ligação Adicionar.**
4. Recrie a sua ligação.
5. Clique **em OK** para criar a ligação.