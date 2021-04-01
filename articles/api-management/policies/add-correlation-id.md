---
title: Política de gestão da Amostra API - Adicione um cabeçalho contendo id de correlação
titleSuffix: Azure API Management
description: Amostra de política de gestão Azure API - Demonstra como adicionar um cabeçalho contendo um id de correlação com o pedido de entrada.
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
ms.openlocfilehash: 922565d26274aee12c9397c08c19330b4fce00e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076305"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Adicionar um cabeçalho com um ID de correlação

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como adicionar um cabeçalho contendo um id de correlação ao pedido de entrada. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-reference.md)

## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de Políticas](../policy-reference.md)