---
title: Exemplo de política de gerenciamento de API do Azure – filtrar conteúdo de resposta | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure – demonstra como filtrar elementos de dados do conteúdo de resposta com base no produto associado à solicitação.
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
ms.openlocfilehash: 462db76b06e5071571ae475d420a627e57dad92e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067760"
---
# <a name="filter-response-content"></a>Filtrar conteúdo da resposta

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como filtrar elementos de dados do conteúdo de resposta com base no produto associado à solicitação. Para definir ou editar um código de política, siga as etapas descritas em [definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **saída** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

