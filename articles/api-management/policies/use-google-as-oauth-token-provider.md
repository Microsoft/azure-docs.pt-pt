---
title: Política de gerenciamento de API de exemplo – autorizar o acesso usando o token OAuth do Google
titleSuffix: Azure API Management
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
ms.openlocfilehash: d606d29d84cd5917c74efe188ae02627ad55d4ab
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442380"
---
# <a name="authorize-access-using-google-oauth-token"></a>Autorizar o acesso usando o token OAuth do Google

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como autorizar o acesso a seus pontos de extremidade usando o Google como um provedor de token OAuth. Para definir ou editar um código de política, siga as etapas descritas em [definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

