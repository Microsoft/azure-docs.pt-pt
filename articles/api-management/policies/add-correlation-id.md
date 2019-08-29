---
title: Exemplo de política de gerenciamento de API do Azure – adicionar um cabeçalho contendo uma ID de correlação | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure – demonstra como adicionar um cabeçalho que contém uma ID de correlação à solicitação de entrada.
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
ms.openlocfilehash: 9f091345a4eaf174c47959cad3cb4525fd926689
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074218"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Adicionar um cabeçalho que contém uma ID de correlação

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como adicionar um cabeçalho que contém uma ID de correlação à solicitação de entrada. Para definir ou editar um código de política, siga as etapas descritas em [definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

