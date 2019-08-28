---
title: Exemplo de política de gerenciamento de API do Azure – autorizar o acesso usando o token OAuth do Google | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure – demonstra como autorizar o acesso a seus pontos de extremidade usando o Google como um provedor de token OAuth.
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
ms.openlocfilehash: 7acc9071008937cd85c628878b385f1f53707e53
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071914"
---
# <a name="authorize-access-using-google-oauth-token"></a>Autorizar o acesso usando o token OAuth do Google

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como autorizar o acesso a seus pontos de extremidade usando o Google como um provedor de token OAuth. Para definir ou editar um código de política, siga as etapas descritas em [definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

