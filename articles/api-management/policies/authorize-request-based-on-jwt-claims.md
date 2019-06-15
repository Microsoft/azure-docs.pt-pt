---
title: Política de gestão de API do Azure de exemplo – autorizar o acesso baseado em declarações do JWT | Documentos da Microsoft
description: Exemplo de política do gestão de API do Azure - demonstra como autorizar o acesso aos métodos HTTP específicos numa API com base em declarações do JWT.
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
ms.openlocfilehash: d656cf7c7bed1d40bbde654f9c2484efcc5df25d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61062161"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autorizar o acesso baseado em declarações do JWT

Este artigo mostra um exemplo de política de gestão de API do Azure que demonstra como autorizar o acesso aos métodos HTTP específicos numa API com base em declarações do JWT. Para definir ou editar um código de política, siga os passos descritos em [conjunto ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código para o **entrada** bloco.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

