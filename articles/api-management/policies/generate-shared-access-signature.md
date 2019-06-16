---
title: Exemplo de Azure política de gestão do API - gerar assinatura de acesso partilhado | Documentos da Microsoft
description: Exemplo de política do gestão de API do Azure - demonstra como gerar a assinatura de acesso partilhado com expressões e reencaminhar o pedido para o armazenamento do Azure com a política de reescrita-uri....
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
ms.openlocfilehash: 2c3adaa6f4e113f09e676583c2c35b5f1fbdb622
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61062195"
---
# <a name="generate-shared-access-signature"></a>Gerar a assinatura de acesso partilhado

Este artigo mostra um exemplo de política de gestão de API do Azure que demonstra como gerar [assinatura de acesso partilhado](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) usando expressões e reencaminhar o pedido para o armazenamento do Azure com a política de reescrita-uri. Para definir ou editar um código de política, siga os passos descritos em [conjunto ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código para o **entrada** bloco.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

