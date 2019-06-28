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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184094"
---
### <a name="gwipnoconnection"></a> Para modificar o endereço IP de gateway de rede local - sem ligação de gateway

Utilize o exemplo para modificar um gateway de rede local que não tem uma ligação de gateway. Ao modificar este valor, também pode modificar os prefixos de endereços em simultâneo.

1. No recurso de Gateway de rede Local, na **configurações** secção, clique em **configuração**.
2. Na **endereço IP** caixa, modifique o endereço IP.
3. Clique em **Guardar** para guardar as definições.

### <a name="gwipwithconnection"></a>Para modificar o endereço IP de gateway rede local - ligação de gateway existente

Para modificar um gateway de rede local que tenha uma ligação, tem primeiro de remover a ligação. Depois de a ligação ser removida, pode modificar o endereço IP do gateway e recriar uma nova ligação. Também pode modificar os prefixos de endereços em simultâneo. Este procedimento resulta num período de indisponibilidade da ligação VPN. Ao modificar os endereços IP de gateway, não precisa de eliminar o gateway de VPN. Só tem de remover a ligação.
 
#### <a name="1-remove-the-connection"></a>1. Remova a ligação.

1. No recurso de Gateway de rede Local, na **configurações** secção, clique em **ligações**.
2. Clique no **...**  na linha para a ligação, em seguida, clique em **eliminar**.
3. Clique em **guardar** para guardar as definições.

#### <a name="2-modify-the-ip-address"></a>2. Modificar o endereço IP.

Também pode modificar os prefixos de endereços em simultâneo.

1. Na **endereço IP** caixa, modifique o endereço IP.
2. Clique em **Guardar** para guardar as definições.

#### <a name="3-recreate-the-connection"></a>3. Recrie a ligação.

1. Navegue para o Gateway de rede Virtual para a sua VNet. (Não o Gateway de rede Local.)
2. No Gateway de rede Virtual, na **configurações** secção, clique em **ligações**.
3. Clique nas **+ adicionar** para abrir o **adicionar ligação** painel.
4. Recrie a ligação.
5. Clique em **OK** para criar a ligação.