---
title: Política de gestão de API do Azure de exemplo - autorizar a solicitação usando authorizer externo | Documentos da Microsoft
description: Exemplo de política do gestão de API do Azure - demonstra como autorizar os pedidos com authorizer externo, encapsulando uma lógica de autenticação/autorização legados ou personalizados.
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
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 65ea8622187d0665e4680f4162ddff0bc01e6eb9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60306771"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorizar pedidos utilizando authorizer externo

Este artigo mostra um exemplo de política de gestão de API do Azure que demonstra como proteger o acesso à API utilizando um authorizer externo encapsular a lógica de autenticação/autorização personalizadas. Para definir ou editar um código de política, siga os passos descritos em [conjunto ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código para o **entrada** bloco.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas APIM:

+ [Políticas de restrições de acesso](../api-management-access-restriction-policies.md)
+ [Exemplos de política](../policy-samples.md)
