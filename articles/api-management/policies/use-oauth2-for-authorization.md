---
title: Exemplo de Azure política de gestão do API - OAuth2 de utilização para autorização entre o gateway e um back-end | Documentos da Microsoft
description: Exemplo de política do gestão de API do Azure - demonstra como utilizar o OAuth2 para autorização entre o gateway e um back-end. Mostra como obter um token de acesso do AAD e reencaminhá-lo para o back-end.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 519233cb9e77bf48f67d869a54af771c17c7827e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859095"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Utilizar o OAuth2 para autorização entre o gateway e um back-end

Este artigo mostra um exemplo de política de gestão de API do Azure que demonstra como utilizar o OAuth2 para autorização entre o gateway e um back-end. Mostra como obter um token de acesso do AAD e reencaminhá-lo para o back-end. 

Para definir ou editar um código de política, siga os passos descritos em [conjunto ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

O script seguinte utiliza as propriedades que são apresentados no {{propriedade}}. Para saber mais sobre as propriedades e como usá-las nas políticas de gestão de API, veja [isso](../api-management-howto-properties.md) tópico.
 
## <a name="policy"></a>Política

Cole o código para o **entrada** bloco.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

