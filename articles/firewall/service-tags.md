---
title: Visão geral das marcas de serviço do firewall do Azure
description: As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168685"
---
# <a name="azure-firewall-service-tags"></a>Marcas de serviço do firewall do Azure

As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança. Não pode criar as suas próprias etiquetas de serviço nem especificar que endereços IP estão incluídos nas mesmas. A Microsoft gere os prefixos de endereços que as etiquetas abrangem e atualiza-as automaticamente à medida que os endereços são alterados.

As marcas de serviço do firewall do Azure podem ser usadas no campo destino de regras de rede. Você pode usá-los no lugar de endereços IP específicos.

## <a name="supported-service-tags"></a>Marcas de serviço com suporte

Consulte [grupos de segurança](../virtual-network/security-overview.md#service-tags) para obter uma lista de marcas de serviço que estão disponíveis para uso em regras de rede do firewall do Azure.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as regras de firewall do Azure, consulte [lógica de processamento de regra de firewall do Azure](rule-processing.md).