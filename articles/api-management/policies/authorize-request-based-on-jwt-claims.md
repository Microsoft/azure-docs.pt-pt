---
title: Política de gestão da Amostra API - Autorizar o acesso com base em reivindicações da JWT
titleSuffix: Azure API Management
description: Amostra de política de gestão da API Azure - Demonstra como autorizar o acesso a métodos HTTP específicos numa API com base em alegações de JWT.
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
ms.openlocfilehash: 1b84854688fbdcc017b16698dfebfe54a7210110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422186"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autorizar o acesso com base em afirmações JWT

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como autorizar o acesso a métodos HTTP específicos numa API com base em alegações de JWT. Para definir ou editar um código de política, siga os passos descritos no [set ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [amostras políticas.](../policy-samples.md)

## <a name="policy"></a>Política

Cola o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Amostras políticas](../policy-samples.md)

