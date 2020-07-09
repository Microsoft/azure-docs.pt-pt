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
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67184095"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Para modificar prefixos de endereço de IP do gateway de rede local - sem ligação de gateway

#### <a name="to-add-additional-address-prefixes"></a>Para adicionar prefixos de endereços adicionais:

1. No recurso Gateway de rede local, na secção **Definições,** clique em **Configuração**.
2. Adicione o espaço de endereço IP na caixa *de intervalo de endereços adicional.*
3. Clique **em Guardar** para guardar as suas definições.

#### <a name="to-remove-address-prefixes"></a>Para remover um prefixos de endereço:

1. No recurso Gateway de rede local, na secção **Definições,** clique em **Configuração**.
2. Clique no **'...'** na linha que contém o prefixo que pretende remover.
3. Clique em **Remover**.
4. Clique **em Guardar** para guardar as suas definições.

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Para modificar prefixos de endereço de IP do gateway de rede local - ligação de gateway existente

Se tiver uma ligação de gateway e pretender adicionar ou remover os prefixos de endereço IP contidos no gateway de rede local, tem de realizar os seguintes passos por ordem. Este procedimento resulta num período de indisponibilidade da ligação VPN. Ao modificar prefixos de endereços IP, não precisa de eliminar o gateway de VPN. Só tem de remover a ligação.

#### <a name="1-remove-the-connection"></a>1. Retire a ligação.

1. No recurso Gateway de rede local, na secção **Definições,** clique em **'Ligações'.**
2. Clique na **...** na linha para cada ligação e, em seguida, clique em **Eliminar**.
3. Clique **em Guardar** para guardar as suas definições.

#### <a name="2-modify-the-address-prefixes"></a>2. Modificar os prefixos do endereço.

Para adicionar prefixos de endereços adicionais:

1. No recurso Gateway de rede local, na secção **Definições,** clique em **Configuração**.
2. Adicione o espaço de endereço IP.
3. Clique **em Guardar** para guardar as suas definições.

Para remover um prefixos de endereço:

1. No recurso Gateway de rede local, na secção **Definições,** clique em **Configuração**.
2. Clique na **...** na linha que contém o prefixo que pretende remover.
3. Clique em **Remover**.
4. Clique **em Guardar** para guardar as suas definições.

#### <a name="3-recreate-the-connection"></a>3. Recrie a ligação.

1. Navegue para o Gateway de Rede Virtual para o seu VNet. (Não a rede local Gateway.)
2. Na porta de entrada de rede virtual, na secção **Definições,** clique em **'Ligações'.**
3. Clique no **+ Adicione** para abrir a lâmina **de ligação Adicionar.**
4. Recrie a sua ligação.
5. Clique **em OK** para criar a ligação.