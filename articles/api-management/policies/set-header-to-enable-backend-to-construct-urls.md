---
title: Amostra de política de gestão da API Azure - Adicione um cabeçalho reencaminhado / Microsoft Docs
description: Amostra de política de gestão da API Azure - Demonstra como adicionar um cabeçalho reencaminhado no pedido de entrada para permitir que a API backend construa URLs adequados.
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
ms.openlocfilehash: e525029ae8eab086d11126a4e18958423e207aa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70067494"
---
# <a name="add-a-forwarded-header"></a>Adicione um cabeçalho para a frente

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como adicionar um cabeçalho reencaminhado no pedido de entrada para permitir que a API de backend construa URLs adequados. Para definir ou editar um código de política, siga os passos descritos no [set ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [amostras políticas.](../policy-samples.md)

## <a name="code"></a>Código

Cola o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Amostras políticas](../policy-samples.md)
