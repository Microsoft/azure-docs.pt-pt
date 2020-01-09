---
title: Política de gerenciamento de API de exemplo – solicitação de rota com base no tamanho do corpo da mensagem
titleSuffix: Azure API Management
description: Exemplo de política de gerenciamento de API do Azure – demonstra como rotear solicitações com base no tamanho de seus corpos.
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
ms.openlocfilehash: 8cee2c13386a076f0321619754468cfc1e9fb31c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442434"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Encaminhar a solicitação com base no tamanho do corpo

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como rotear solicitações com base no tamanho de seus corpos. Para definir ou editar um código de política, siga as etapas descritas em [definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

