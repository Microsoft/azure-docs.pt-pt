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
ms.openlocfilehash: 99bf1068042eb7ab0c43e2a683ca7116d2e426f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442489"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorizar pedidos com um autorizador externo

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como garantir o acesso da API utilizando um autor externo que encapsule a lógica de autenticação/autorização personalizada. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-samples.md)

## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de restrições de acesso](../api-management-access-restriction-policies.md)
+ [Amostras de política](../policy-samples.md)
