---
title: O que é um hub virtual seguro?
description: Saiba mais sobre hubs virtuais seguros
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518440"
---
# <a name="what-is-a-secured-virtual-hub"></a>O que é um hub virtual seguro?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Um hub virtual é uma rede virtual gerida pela Microsoft que permite a conectividade de outros recursos. Quando um hub virtual é criado a partir de um WAN virtual no portal Azure, um hub virtual VNet e gateways (opcional) são criados como seus componentes.

Um hub virtual *seguro* é um [Hub Wan Virtual Azur e Azure](../virtual-wan/virtual-wan-about.md#resources) com políticas de segurança e encaminhamento associadas configuradas pelo Azure Firewall Manager. Utilize centros virtuais seguros para criar facilmente arquiteturas hub-and-spoke e transitivas com serviços de segurança nativa para governação e proteção do tráfego. 

Você pode usar um hub virtual seguro como um VNet central gerido sem conectividade on-prem. Substitui o VNet central que era anteriormente necessário para uma implantação de Firewall Azure. Uma vez que o centro virtual seguro fornece encaminhamento automatizado, não há necessidade de configurar os seus próprios UDRs (rotas definidas pelo utilizador) para encaminhar o tráfego através da sua firewall.

Também é possível usar centros virtuais seguros como parte de uma arquitetura completa do WAN virtual. Esta arquitetura proporciona conectividade de filial segura, otimizada e automatizada de e através do Azure. Pode escolher os serviços para proteger e governar o tráfego da sua rede, incluindo o Azure Firewall e outros fornecedores de segurança de terceiros como prestadores de serviços (SECaaS).

## <a name="create-a-secured-virtual-hub"></a>Criar um centro virtual seguro

Utilizando o Firewall Manager no portal Azure, pode criar um novo hub virtual seguro ou converter um hub virtual existente que criou anteriormente usando o Azure Virtual WAN.

## <a name="next-steps"></a>Passos seguintes

Para criar um hub virtual seguro e usá-lo para proteger e governar um hub e uma rede falada, consulte Tutorial: Proteja a [sua rede de nuvem com o Azure Firewall Manager utilizando o portal Azure](secure-cloud-network.md).