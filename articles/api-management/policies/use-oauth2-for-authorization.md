---
title: Política de gestão da AZure Azure - Use OAuth2 para autorização entre gateway e backend
titleSuffix: Azure API Management
description: Amostra de política de gestão da AZure API - Demonstra como utilizar o OAuth2 para autorização entre o gateway e um backend. Mostra como obter um token de acesso do AAD e reencaminhá-lo para o back-end.
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
ms.openlocfilehash: 0f5a10eb2ebc3b3a7c527dd718e37faf03c927bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076101"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Use o OAuth2 para autorização entre o portal e um backend

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como usar o OAuth2 para autorização entre o gateway e um backend. Mostra como obter um token de acesso do AAD e reencaminhá-lo para o back-end. 

Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-reference.md)

O seguinte script utiliza propriedades que aparecem em {{property}}} Para conhecer os imóveis e como usá-los nas políticas de Gestão da API, consulte [este](../api-management-howto-properties.md) tópico.
 
## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de Políticas](../policy-reference.md)