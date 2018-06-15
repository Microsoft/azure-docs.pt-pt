---
title: Azure API política de amostra de gestão - OAuth2 de utilização para autorização entre o gateway e um back-end | Microsoft Docs
description: Exemplo de política de gestão de Azure API - demonstra como utilizar o OAuth2 para autorização entre o gateway e um back-end. Mostra como obter um token de acesso do AAD e reencaminhá-lo para o back-end.
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
ms.openlocfilehash: 240f78bc66af681d0089c45229ab142adf515e60
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933323"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Utilizar o OAuth2 para autorização entre o gateway e um back-end

Este artigo mostra um exemplo de política de gestão de API do Azure que demonstra como utilizar o OAuth2 para autorização entre o gateway e um back-end. Mostra como obter um token de acesso do AAD e reencaminhá-lo para o back-end. 

Para definir ou editar um código de política, siga os passos descritos no [conjunto ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [amostras de política](../policy-samples.md).

O script seguinte utiliza as propriedades que aparecem em {{propriedade}}. Para saber mais sobre as propriedades e como utilizá-los nas políticas de API Management, consulte [isto](../api-management-howto-properties.md) tópico.
 
## <a name="policy"></a>Política

Cole o código para o **entrada** bloco.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

