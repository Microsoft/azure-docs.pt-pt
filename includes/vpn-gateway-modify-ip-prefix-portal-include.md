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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121013"
---
### <a name="noconnection"></a>Para modificar prefixos de endereço de IP do gateway de rede local - sem ligação de gateway

#### <a name="to-add-additional-address-prefixes"></a>Para adicionar prefixos de endereços adicionais:

1. No recurso de Gateway de rede Local, na **configurações** secção, clique em **configuração**.
2. Adicione o espaço de endereços IP na *adicionar intervalo de endereços adicionais* caixa.
3. Clique em **guardar** para guardar as definições.

#### <a name="to-remove-address-prefixes"></a>Para remover um prefixos de endereço:

1. No recurso de Gateway de rede Local, na **configurações** secção, clique em **configuração**.
2. Clique nas **"..."** na linha que contém o prefixo que pretende remover.
3. Clique em **remover**.
4. Clique em **guardar** para guardar as definições.

### <a name="withconnection"></a>Para modificar prefixos de endereço de IP do gateway de rede local - ligação de gateway existente

Se tiver uma ligação de gateway e pretender adicionar ou remover os prefixos de endereço IP contidos no gateway de rede local, tem de realizar os seguintes passos por ordem. Este procedimento resulta num período de indisponibilidade da ligação VPN. Ao modificar prefixos de endereços IP, não precisa de eliminar o gateway de VPN. Só tem de remover a ligação.

#### <a name="1-remove-the-connection"></a>1. Remova a ligação.

1. No recurso de Gateway de rede Local, na **configurações** secção, clique em **ligações**.
2. Clique no **...**  na linha para cada ligação, em seguida, clique em **eliminar**.
3. Clique em **guardar** para guardar as definições.

#### <a name="2-modify-the-address-prefixes"></a>2. Modificar os prefixos de endereço.

Para adicionar prefixos de endereços adicionais:

1. No recurso de Gateway de rede Local, na **configurações** secção, clique em **configuração**.
2. Adicione o espaço de endereços IP.
3. Clique em **guardar** para guardar as definições.

Para remover um prefixos de endereço:

1. No recurso de Gateway de rede Local, na **configurações** secção, clique em **configuração**.
2. Clique no **...**  na linha que contém o prefixo que pretende remover.
3. Clique em **remover**.
4. Clique em **guardar** para guardar as definições.

#### <a name="3-recreate-the-connection"></a>3. Recrie a ligação.

1. Navegue para o Gateway de rede Virtual para a sua VNet. (Não o Gateway de rede Local.)
2. No Gateway de rede Virtual, na **configurações** secção, clique em **ligações**.
3. Clique nas **+ adicionar** para abrir o **adicionar ligação** painel.
4. Recrie a ligação.
5. Clique em **OK** para criar a ligação.