---
title: Política de gestão da amostra da API - Definir duração da cache de resposta
titleSuffix: Azure API Management
description: Amostra de política de gestão Azure API - Demonstra como definir a duração da cache de resposta utilizando o valor maxAge em Cache-Control cabeçalho enviado pelo backend..
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
ms.openlocfilehash: 3101c5695272e8fa6b577ad313897cbc1fa29629
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442400"
---
# <a name="set-response-cache-duration"></a>Definir a duração da cache de resposta

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como definir a duração da cache de resposta usando o valor maxAge em Cache-Control cabeçalho enviado pelo backend. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-samples.md)

## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Amostras de política](../policy-samples.md)

