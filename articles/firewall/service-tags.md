---
title: Visão geral das tags de serviço Azure Firewall
description: As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 83e9a96573bbc72e0afff61cc0f151f95b081e30
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97031584"
---
# <a name="azure-firewall-service-tags"></a>Tags de serviço Azure Firewall

As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança. Não pode criar as suas próprias etiquetas de serviço nem especificar que endereços IP estão incluídos nas mesmas. A Microsoft gere os prefixos de endereços que as etiquetas abrangem e atualiza-as automaticamente à medida que os endereços são alterados.

As tags de serviço Azure Firewall podem ser usadas no campo de destino das regras de rede. Pode usá-los no lugar de endereços IP específicos.

## <a name="supported-service-tags"></a>Etiquetas de serviço suportadas

Consulte [as etiquetas](../virtual-network/service-tags-overview.md#available-service-tags) de serviço de rede virtual para obter uma lista de tags de serviço que estão disponíveis para utilização nas regras da rede de firewall Azure.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as regras do Azure Firewall, consulte [a lógica de processamento de regras do Azure Firewall](rule-processing.md).
