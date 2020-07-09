---
title: Política de gestão da Amostra API - Adicionar capacidades ao serviço backend
titleSuffix: Azure API Management
description: Amostra de política de gestão Azure API - Demonstra como adicionar capacidades a um serviço de backend. Por exemplo, aceitar o nome de um local em vez de latitude e longitude numa API de previsão meteorológica.
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
ms.openlocfilehash: e286f77009a17870332ed5caf1c66c18dd21d020
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75442476"
---
# <a name="add-capabilities-to-a-backend-service"></a>Adicionar capacidades a um serviço de backend

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como adicionar capacidades a um serviço de backend. Por exemplo, aceitar o nome de um local em vez de latitude e longitude numa API de previsão meteorológica. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-samples.md)

## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Amostras de política](../policy-samples.md)

