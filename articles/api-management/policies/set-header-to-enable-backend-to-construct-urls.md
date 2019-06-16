---
title: Exemplo de política de gestão de API do Azure - adicionar um cabeçalho de Forwarded | Documentos da Microsoft
description: Exemplo de política do gestão de API do Azure - demonstra como adicionar um cabeçalho de Forwarded a solicitação de entrada para permitir que a API para construir URLs adequadas de back-end.
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
ms.openlocfilehash: b857d1780e9734ce891ce2c0ce4bedf50dfe13e9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859499"
---
# <a name="add-a-forwarded-header"></a>Adicionar um cabeçalho de Forwarded

Este artigo mostra um exemplo de política de gestão de API do Azure que demonstra como adicionar um cabeçalho de Forwarded a solicitação de entrada para permitir que a API para construir URLs adequadas de back-end. Para definir ou editar um código de política, siga os passos descritos em [conjunto ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="code"></a>Código

Cole o código para o **entrada** bloco.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)
