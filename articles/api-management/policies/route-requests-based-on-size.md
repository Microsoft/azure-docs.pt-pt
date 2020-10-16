---
title: Política de gestão da amostra da API - Pedido de rota baseado no tamanho do corpo de mensagens
titleSuffix: Azure API Management
description: Amostra de política de gestão da API - Demonstra como encaminhar os pedidos com base no tamanho dos seus corpos.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: e59aece0be39bf671e3d1e62f1a0e4c2a42a1ec8
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076475"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Encaminhar o pedido com base no tamanho do respetivo corpo

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como encaminhar pedidos com base no tamanho dos seus corpos. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-reference.md)

## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Amostras de política](../policy-reference.md)