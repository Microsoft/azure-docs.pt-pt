---
title: Política de gerenciamento de API de exemplo-definir duração do cache de resposta
titleSuffix: Azure API Management
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
ms.openlocfilehash: 3101c5695272e8fa6b577ad313897cbc1fa29629
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442400"
---
# <a name="set-response-cache-duration"></a>Definir duração do cache de resposta

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como definir a duração do cache de resposta usando o valor de maxAge no cabeçalho Cache-Control enviado pelo back-end. Para definir ou editar um código de política, siga as etapas descritas em [definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

