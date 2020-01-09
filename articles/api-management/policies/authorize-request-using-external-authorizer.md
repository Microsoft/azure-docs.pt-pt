---
title: Política de gerenciamento de API de exemplo – autorizar solicitação usando o autorizador externo
titleSuffix: Azure API Management
description: Exemplo de política de gerenciamento de API do Azure – demonstra como autorizar solicitações usando o autorizador externo encapsulando uma lógica de autenticação/autorização personalizada ou herdada.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 99bf1068042eb7ab0c43e2a683ca7116d2e426f3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442489"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorizar solicitações usando o autorizador externo

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como proteger o acesso à API usando um autorizador externo que encapsula a lógica de autenticação/autorização personalizada. Para definir ou editar um código de política, siga as etapas descritas em [definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas de APIM:

+ [Políticas de restrições de acesso](../api-management-access-restriction-policies.md)
+ [Exemplos de política](../policy-samples.md)
