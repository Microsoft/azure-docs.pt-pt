---
title: Política de gestão da Amostra API - Autorize o pedido utilizando o autoria externo
titleSuffix: Azure API Management
description: Amostra de política de gestão da API - Demonstra como autorizar pedidos utilizando um autor externo que engloba uma lógica de autenticação/autorização personalizada ou legado.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: e38d92a13c9a66defc2d5090990b44a889cfd21c
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076237"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorizar pedidos com um autorizador externo

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como garantir o acesso da API utilizando um autor externo que encapsule a lógica de autenticação/autorização personalizada. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-reference.md)

## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de restrições de acesso](../api-management-access-restriction-policies.md)
+ [Amostras de política](../policy-reference.md)