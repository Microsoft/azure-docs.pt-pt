---
title: Exemplo de política de gerenciamento de API do Azure – definir duração do cache de resposta | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure – demonstra como definir a duração do cache de resposta usando o valor de maxAge no cabeçalho Cache-Control enviado pelo back-end.
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
ms.openlocfilehash: 67d2f334e2088d96543fabd3b99cac71d95630eb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071979"
---
# <a name="set-response-cache-duration"></a>Definir duração do cache de resposta

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como definir a duração do cache de resposta usando o valor de maxAge no cabeçalho Cache-Control enviado pelo back-end. Para definir ou editar um código de política, siga as etapas descritas em [definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

