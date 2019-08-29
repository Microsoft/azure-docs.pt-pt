---
title: Exemplo de política de gerenciamento de API do Azure – usar OAuth2 para autorização entre o gateway e um back-end | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure – demonstra como usar o OAuth2 para autorização entre o gateway e um back-end. Mostra como obter um token de acesso do AAD e reencaminhá-lo para o back-end.
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
ms.openlocfilehash: fac10b728e4b7f09ec1019c3257f7c9e5d6e7714
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071866"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Usar OAuth2 para autorização entre o gateway e um back-end

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como usar o OAuth2 para autorização entre o gateway e um back-end. Mostra como obter um token de acesso do AAD e reencaminhá-lo para o back-end. 

Para definir ou editar um código de política, siga as etapas descritas em [definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

O script a seguir usa propriedades que aparecem em {{Property}}. Para saber mais sobre as propriedades e como usá-las em políticas de gerenciamento de API, consulte [este](../api-management-howto-properties.md) tópico.
 
## <a name="policy"></a>Política

Cole o código no bloco de **entrada** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

