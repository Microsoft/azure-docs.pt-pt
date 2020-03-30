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
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184095"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Para modificar prefixos de endereço de IP do gateway de rede local - sem ligação de gateway

#### <a name="to-add-additional-address-prefixes"></a>Para adicionar prefixos de endereços adicionais:

1. No recurso Gateway da rede local, na secção **Definições,** clique na **Configuração**.
2. Adicione o espaço de endereço IP na caixa de intervalo de *endereços adicional Adicionar.*
3. Clique em **Guardar** para guardar as suas definições.

#### <a name="to-remove-address-prefixes"></a>Para remover um prefixos de endereço:

1. No recurso Gateway da rede local, na secção **Definições,** clique na **Configuração**.
2. Clique no **'...'** na linha que contém o prefixo que pretende remover.
3. Clique em **Remover**.
4. Clique em **Guardar** para guardar as suas definições.

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Para modificar prefixos de endereço de IP do gateway de rede local - ligação de gateway existente

Se tiver uma ligação de gateway e pretender adicionar ou remover os prefixos de endereço IP contidos no gateway de rede local, tem de realizar os seguintes passos por ordem. Este procedimento resulta num período de indisponibilidade da ligação VPN. Ao modificar prefixos de endereços IP, não precisa de eliminar o gateway de VPN. Só tem de remover a ligação.

#### <a name="1-remove-the-connection"></a>1. Retire a ligação.

1. No recurso Gateway da rede local, na secção **Definições,** clique em **Ligações**.
2. Clique no **...** na linha para cada ligação e, em seguida, clique em **Apagar**.
3. Clique em **Guardar** para guardar as suas definições.

#### <a name="2-modify-the-address-prefixes"></a>2. Modificar os prefixos do endereço.

Para adicionar prefixos de endereços adicionais:

1. No recurso Gateway da rede local, na secção **Definições,** clique na **Configuração**.
2. Adicione o espaço de endereço IP.
3. Clique em **Guardar** para guardar as suas definições.

Para remover um prefixos de endereço:

1. No recurso Gateway da rede local, na secção **Definições,** clique na **Configuração**.
2. Clique no **...** na linha que contém o prefixo que pretende remover.
3. Clique em **Remover**.
4. Clique em **Guardar** para guardar as suas definições.

#### <a name="3-recreate-the-connection"></a>3. Recriar a ligação.

1. Navegue para o Portal da Rede Virtual para o seu VNet. (Não o Gateway da Rede Local.)
2. No Gateway da Rede Virtual, na secção **Definições,** clique em **Ligações**.
3. Clique no **+ Adicionar** para abrir a lâmina de **ligação Adicionar.**
4. Recrie a sua ligação.
5. Clique **em OK** para criar a ligação.