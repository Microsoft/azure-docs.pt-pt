---
title: Amostra de política de gestão da Azure API - Adicione um cabeçalho reencaminhado | Microsoft Docs
description: Amostra de política de gestão Azure API - Demonstra como adicionar um cabeçalho reencaminhado no pedido de entrada para permitir que a API de backend construa URLs adequados.
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
ms.openlocfilehash: df72ae5e9a1471e1387539d2c89a1eca0b09d866
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078617"
---
# <a name="add-a-forwarded-header"></a>Adicione um cabeçalho reencaminhado

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como adicionar um cabeçalho reencaminhado no pedido de entrada para permitir que a API de backend construa URLs adequados. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-reference.md)

## <a name="code"></a>Código

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de Políticas](../policy-reference.md)