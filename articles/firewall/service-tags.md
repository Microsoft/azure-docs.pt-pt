---
title: Visão geral das etiquetas de serviço azure firewall
description: As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168685"
---
# <a name="azure-firewall-service-tags"></a>Etiquetas de serviço Azure Firewall

As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança. Não pode criar as suas próprias etiquetas de serviço nem especificar que endereços IP estão incluídos nas mesmas. A Microsoft gere os prefixos de endereços que as etiquetas abrangem e atualiza-as automaticamente à medida que os endereços são alterados.

As etiquetas de serviço Azure Firewall podem ser utilizadas no campo de destino das regras da rede. Pode usá-los no lugar de endereços IP específicos.

## <a name="supported-service-tags"></a>Etiquetas de serviço suportadas

Consulte [os grupos de segurança](../virtual-network/security-overview.md#service-tags) para obter uma lista de etiquetas de serviço disponíveis para utilização nas regras da rede de firewall Azure.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as regras da Firewall Azure, consulte a lógica de processamento de [regras da Firewall Azure.](rule-processing.md)