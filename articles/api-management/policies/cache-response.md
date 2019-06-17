---
title: Política de gestão de API do Azure de exemplo - adicionar recursos a um serviço de back-end | Documentos da Microsoft
description: Exemplo de política do gestão de API do Azure - demonstra como adicionar recursos a um serviço de back-end. Por exemplo, aceitar o nome de um local em vez de latitude e longitude numa API de previsão meteorológica.
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
ms.openlocfilehash: 7c9edbf4b2d231453cd336521a04ba6b7714b696
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61062212"
---
# <a name="add-capabilities-to-a-backend-service"></a>Adicionar recursos a um serviço de back-end

Este artigo mostra um exemplo de política de gestão de API do Azure que demonstra como adicionar recursos a um serviço de back-end. Por exemplo, aceitar o nome de um local em vez de latitude e longitude numa API de previsão meteorológica. Para definir ou editar um código de política, siga os passos descritos em [conjunto ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código para o **entrada** bloco.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

