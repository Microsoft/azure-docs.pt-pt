---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5a734963ced9daefda2b7b6f4a52fd9ef437eddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "71269269"
---
O gateway de rede virtual utiliza uma sub-rede específica denominada sub-rede do gateway. A sub-rede do gateway faz parte do intervalo de endereços IP da rede virtual que especificou quando configurar a rede virtual. Contém os endereços IP que utilizam os serviços e recursos de gateway de rede virtual. A sub-rede deve ter o nome "GatewaySubnet" por ordem para que o Azure possa implementar os recursos de gateway. Não é possível especificar uma sub-rede diferente para implementar os recursos de gateway. Se não tiver uma sub-rede com o nome "GatewaySubnet", quando criar o gateway de VPN este irá falhar.

Quando cria a sub-rede do gateway, especifica o número de endereços IP que a sub-rede contém. O número de endereços IP necessários depende da configuração do gateway VPN que pretende criar. Algumas configurações requerem mais endereços IP do que outras. Recomendamos que crie uma sub-rede de gateway que utilize /27 ou /28.

Se vir um erro que especifica que o espaço de endereços se sobrepõe a uma sub-rede ou se a sub-rede não se encontra dentro do espaço de endereços da rede virtual, verifique o intervalo de endereços da VNet. Não pode ter endereços IP suficientes disponíveis no intervalo de endereços que criou para a sua rede virtual. Por exemplo, se a sub-rede de predefinição abrange o intervalo de endereços completo, isso significa que não existem endereços IP restantes para criar sub-redes adicionais. Pode ajustar as sub-redes no espaço de endereço existente para libertar endereços IP ou especifique um intervalo de endereços adicionais e crie a sub-rede do gateway.