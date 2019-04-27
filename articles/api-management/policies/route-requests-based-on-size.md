---
title: API management política exemplo do Azure - encaminhar o pedido com base no tamanho do seu corpo | Documentos da Microsoft
description: Exemplo de política do gestão de API do Azure - demonstra como rotear solicitações com base no tamanho de seus corpos.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 7656401115cc370d6eee60fb9bddb9bcd92e4201
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860949"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Encaminhar o pedido com base no tamanho do seu corpo

Este artigo mostra um exemplo de política de gestão de API do Azure que demonstra como rotear solicitações com base no tamanho de seus corpos. Para definir ou editar um código de política, siga os passos descritos em [conjunto ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código para o **entrada** bloco.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

