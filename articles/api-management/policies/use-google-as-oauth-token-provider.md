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
ms.openlocfilehash: d606d29d84cd5917c74efe188ae02627ad55d4ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442380"
---
# <a name="authorize-access-using-google-oauth-token"></a>Autorizar o acesso com o token de OAuth do Google

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como autorizar o acesso aos seus pontos finais usando o Google como fornecedor de fichas OAuth. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-samples.md)

## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Amostras de política](../policy-samples.md)

