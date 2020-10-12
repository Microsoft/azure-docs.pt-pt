---
title: O que é um hub virtual seguro?
description: Saiba mais sobre hubs virtuais seguros
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 56e0d40bcbfb97f57b63dc82da1a6604f83dffad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85563633"
---
# <a name="what-is-a-secured-virtual-hub"></a>O que é um hub virtual seguro?

Um hub virtual é uma rede virtual gerida pela Microsoft que permite a conectividade a partir de outros recursos. Quando um hub virtual é criado a partir de um WAN Virtual no portal Azure, um hub virtual VNet e gateways (opcional) são criados como seus componentes.

Um hub virtual *seguro* é um [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) com políticas de segurança e encaminhamento associadas configuradas pelo Azure Firewall Manager. Use centros virtuais seguros para criar facilmente arquiteturas hub-e-fala-faladas e transitivas com serviços de segurança nativo para governação e proteção de tráfego. 

Você pode usar um hub virtual seguro como um VNet central gerido sem conectividade on-prem. Substitui o VNet central que era anteriormente necessário para uma implantação do Azure Firewall. Uma vez que o hub virtual seguro fornece um encaminhamento automatizado, não há necessidade de configurar os seus próprios UDRs (rotas definidas pelo utilizador) para encaminhar o tráfego através da sua firewall.

Também é possível usar centros virtuais seguros como parte de uma arquitetura virtual WAN completa. Esta arquitetura proporciona conectividade de ramo seguro, otimizado e automatizado de e através de Azure. Pode escolher os serviços para proteger e governar o tráfego da sua rede, incluindo a Azure Firewall e outros fornecedores de segurança de terceiros como prestadores de serviços (SECaaS).

## <a name="create-a-secured-virtual-hub"></a>Criar um hub virtual seguro

Utilizando o Firewall Manager no portal Azure, pode criar um novo hub virtual seguro ou converter um hub virtual existente que criou anteriormente usando a Azure Virtual WAN.

## <a name="next-steps"></a>Passos seguintes

Para criar um hub virtual seguro e usá-lo para proteger e governar um hub e rede de fala, consulte [Tutorial: Fixe a sua rede de nuvem com o Azure Firewall Manager utilizando o portal Azure](secure-cloud-network.md).