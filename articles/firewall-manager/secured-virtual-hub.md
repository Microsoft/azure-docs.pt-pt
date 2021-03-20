---
title: O que é um hub virtual seguro?
description: Saiba mais sobre hubs virtuais seguros
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91948073"
---
# <a name="what-is-a-secured-virtual-hub"></a>O que é um hub virtual seguro?

Um hub virtual é uma rede virtual gerida pela Microsoft que permite a conectividade a partir de outros recursos. Quando um hub virtual é criado a partir de um WAN Virtual no portal Azure, um hub virtual VNet e gateways (opcional) são criados como seus componentes.

Um hub virtual *seguro* é um [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) com políticas de segurança e encaminhamento associadas configuradas pelo Azure Firewall Manager. Use centros virtuais seguros para criar facilmente arquiteturas hub-e-fala-faladas e transitivas com serviços de segurança nativo para governação e proteção de tráfego. 

Pode utilizar um hub virtual seguro para filtrar o tráfego entre redes virtuais (V2V), redes virtuais e sucursais (B2V) e tráfego para a Internet (B2I/V2I). Um hub virtual seguro fornece encaminhamento automatizado. Não há necessidade de configurar as suas próprias UDRs (rotas definidas pelo utilizador) para encaminhar o tráfego através da sua firewall.

Pode escolher os fornecedores de segurança necessários para proteger e governar o tráfego da sua rede, incluindo o Azure Firewall, a segurança de terceiros como prestadores de serviços (SECaaS) ou ambos. Atualmente, um hub seguro não suporta a filtragem e filtragem do Branch to Branch (B2B) em vários centros. Para saber mais, veja [o que é Azure Firewall Manager?](overview.md#known-issues) 

## <a name="create-a-secured-virtual-hub"></a>Criar um hub virtual seguro

Utilizando o Firewall Manager no portal Azure, pode criar um novo hub virtual seguro ou converter um hub virtual existente que criou anteriormente usando a Azure Virtual WAN.

## <a name="next-steps"></a>Passos seguintes

Para criar um hub virtual seguro e usá-lo para proteger e governar um hub e rede de fala, consulte [Tutorial: Fixe a sua rede de nuvem com o Azure Firewall Manager utilizando o portal Azure](secure-cloud-network.md).