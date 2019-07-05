---
title: Descrição geral das etiquetas de serviço de Firewall do Azure
description: Este artigo é uma descrição geral das etiquetas de serviço de Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 6/27/2019
ms.author: victorh
ms.openlocfilehash: d0ac36e415c056dffc9c75d00968ff74c2156e63
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450174"
---
# <a name="azure-firewall-service-tags"></a>Etiquetas de serviço de Firewall do Azure

As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança. Não pode criar as suas próprias etiquetas de serviço nem especificar que endereços IP estão incluídos nas mesmas. A Microsoft gere os prefixos de endereços que as etiquetas abrangem e atualiza-as automaticamente à medida que os endereços são alterados.

Etiquetas de serviço de Firewall do Azure podem ser utilizadas no campo de destino de regras de rede. Pode usá-los em vez de endereços IP específicos.

## <a name="supported-service-tags"></a>Etiquetas de serviço de suporte

Ver [grupos de segurança](../virtual-network/security-overview.md#service-tags) para obter uma lista de etiquetas de serviço que estão disponíveis para utilização nas regras de rede de firewall do Azure.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre regras de Firewall do Azure, veja [lógica de processamento de regra de Firewall do Azure](rule-processing.md).