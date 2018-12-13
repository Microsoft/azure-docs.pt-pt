---
title: Política de gestão de API do Azure de exemplo – autorizar o acesso com o token Google OAuth | Documentos da Microsoft
description: Exemplo de política do gestão de API do Azure - demonstra como autorizar o acesso aos seus pontos de extremidade usando o Google como um fornecedor de token de OAuth.
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
ms.openlocfilehash: 430f9e57df163ad345f0740e5bd5beca6e892a4c
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869310"
---
# <a name="authorize-access-using-google-oauth-token"></a>Autorizar o acesso com o token de OAuth do Google

Este artigo mostra um exemplo de política de gestão de API do Azure que demonstra como autorizar o acesso aos seus pontos de extremidade usando o Google como um fornecedor de token de OAuth. Para definir ou editar um código de política, siga os passos descritos em [conjunto ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código para o **entrada** bloco.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

