---
title: Política de gestão da API da amostra - Gerar Assinatura de Acesso Partilhado
titleSuffix: Azure API Management
description: Amostra de política de gestão da AZure API - Demonstra como gerar Assinatura de Acesso Partilhado usando expressões e encaminhar o pedido para armazenamento Azure com política de reescrita-uri..
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
ms.openlocfilehash: 62a6e8c9fdec3b30bd193e9887d7e0cb7926e73e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86243006"
---
# <a name="generate-shared-access-signature"></a>Gerar assinatura de acesso compartilhado

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como gerar [Assinatura de Acesso Partilhado](../../storage/common/storage-sas-overview.md) usando expressões e encaminhar o pedido para o armazenamento Azure com a política de reescrita-uri. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-samples.md)

## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Amostras de política](../policy-samples.md)
