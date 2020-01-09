---
title: Política de gerenciamento de API de exemplo-adicionar recursos ao serviço de back-end
titleSuffix: Azure API Management
description: Exemplo de política de gerenciamento de API do Azure – demonstra como adicionar recursos a um serviço de back-end. Por exemplo, aceitar o nome de um local em vez de latitude e longitude numa API de previsão meteorológica.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442476"
---
# <a name="add-capabilities-to-a-backend-service"></a>Adicionar recursos a um serviço de back-end

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como adicionar recursos a um serviço de back-end. Por exemplo, aceitar o nome de um local em vez de latitude e longitude numa API de previsão meteorológica. Para definir ou editar um código de política, siga as etapas descritas em [definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

