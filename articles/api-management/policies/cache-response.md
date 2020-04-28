---
title: Política de gestão da Amostra API - Adicionar capacidades para apoiar o serviço
titleSuffix: Azure API Management
description: Amostra de política de gestão da API Azure - Demonstra como adicionar capacidades a um serviço de backend. Por exemplo, aceitar o nome de um local em vez de latitude e longitude numa API de previsão meteorológica.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75442476"
---
# <a name="add-capabilities-to-a-backend-service"></a>Adicione capacidades a um serviço de backend

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como adicionar capacidades a um serviço de backend. Por exemplo, aceitar o nome de um local em vez de latitude e longitude numa API de previsão meteorológica. Para definir ou editar um código de política, siga os passos descritos no [set ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [amostras políticas.](../policy-samples.md)

## <a name="policy"></a>Política

Cola o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Amostras políticas](../policy-samples.md)

