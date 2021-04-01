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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
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
+ [Exemplos de Políticas](../policy-reference.md)