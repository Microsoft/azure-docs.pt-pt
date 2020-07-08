---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67184094"
---
### <a name="to-modify-the-local-network-gateway-ip-address---no-gateway-connection"></a><a name="gwipnoconnection"></a>Para modificar o endereço IP do gateway de rede local - sem ligação gateway

Utilize o exemplo para modificar um gateway de rede local que não tem uma ligação de gateway. Ao modificar este valor, também pode modificar os prefixos de endereços em simultâneo.

1. No recurso Gateway de rede local, na secção **Definições,** clique em **Configuração**.
2. Na caixa **de endereços IP,** modifique o endereço IP.
3. Clique em **Guardar** para guardar as definições.

### <a name="to-modify-the-local-network-gateway-ip-address---existing-gateway-connection"></a><a name="gwipwithconnection"></a>Para modificar o endereço IP do gateway de rede local - ligação de gateway existente

Para modificar um gateway de rede local que tenha uma ligação, é necessário remover primeiro a ligação. Depois de a ligação ser removida, pode modificar o endereço IP do gateway e recriar uma nova ligação. Também pode modificar os prefixos de endereços em simultâneo. Este procedimento resulta num período de indisponibilidade da ligação VPN. Ao modificar os endereços IP de gateway, não precisa de eliminar o gateway de VPN. Só tem de remover a ligação.
 
#### <a name="1-remove-the-connection"></a>1. Retire a ligação.

1. No recurso Gateway de rede local, na secção **Definições,** clique em **'Ligações'.**
2. Clique na **...** na linha para a ligação e, em seguida, clique em **Eliminar**.
3. Clique **em Guardar** para guardar as suas definições.

#### <a name="2-modify-the-ip-address"></a>2. Modificar o endereço IP.

Também pode modificar os prefixos de endereços em simultâneo.

1. Na caixa **de endereços IP,** modifique o endereço IP.
2. Clique em **Guardar** para guardar as definições.

#### <a name="3-recreate-the-connection"></a>3. Recrie a ligação.

1. Navegue para o Gateway de Rede Virtual para o seu VNet. (Não a rede local Gateway.)
2. Na porta de entrada de rede virtual, na secção **Definições,** clique em **'Ligações'.**
3. Clique no **+ Adicione** para abrir a lâmina **de ligação Adicionar.**
4. Recrie a sua ligação.
5. Clique **em OK** para criar a ligação.