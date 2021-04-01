---
title: Política de gestão da Amostra API - Autorizar o acesso usando o token do Google OAuth
titleSuffix: Azure API Management
description: A amostra de política de gestão da API - Demonstra como autorizar o acesso aos seus pontos finais usando o Google como fornecedor de token OAuth.
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
ms.openlocfilehash: 0f6c9fe2146414f78e90d6ade1a00045cdf3a04f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92078022"
---
# <a name="authorize-access-using-google-oauth-token"></a>Autorizar o acesso com o token de OAuth do Google

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como autorizar o acesso aos seus pontos finais usando o Google como fornecedor de fichas OAuth. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-reference.md)

## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de Políticas](../policy-reference.md)