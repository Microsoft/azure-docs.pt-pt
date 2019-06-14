---
title: Exemplo de política de gestão de API do Azure - adicionar um cabeçalho que contém um id de correlação | Documentos da Microsoft
description: Exemplo de política do gestão de API do Azure - demonstra como adicionar um cabeçalho que contém um id de correlação para a solicitação de entrada.
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
ms.openlocfilehash: 126701674b6dc529404f5a7854cda9b31c336170
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60306788"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Adicionar um cabeçalho que contém um id de correlação

Este artigo mostra um exemplo de política de gestão de API do Azure que demonstra como adicionar um cabeçalho que contém um id de correlação para a solicitação de entrada. Para definir ou editar um código de política, siga os passos descritos em [conjunto ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código para o **entrada** bloco.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

