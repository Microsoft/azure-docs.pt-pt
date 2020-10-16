---
title: Política de gestão da Amostra API - Autorizar o acesso com base em reclamações do JWT
titleSuffix: Azure API Management
description: Amostra de política de gestão da AZure API - Demonstra como autorizar o acesso a métodos HTTP específicos numa API com base em alegações do JWT.
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
ms.openlocfilehash: b5b0c2feb31eab5e39bbebd7108b3ec8504769d4
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076288"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autorizar o acesso com base em afirmações JWT

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como autorizar o acesso a métodos HTTP específicos numa API com base em alegações do JWT. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-reference.md)

## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Amostras de política](../policy-reference.md)