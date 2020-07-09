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
ms.openlocfilehash: 1b84854688fbdcc017b16698dfebfe54a7210110
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75422186"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autorizar o acesso com base em afirmações JWT

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como autorizar o acesso a métodos HTTP específicos numa API com base em alegações do JWT. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-samples.md)

## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Amostras de política](../policy-samples.md)

