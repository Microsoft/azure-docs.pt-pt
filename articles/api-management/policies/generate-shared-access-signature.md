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
ms.openlocfilehash: 0f003bc268af6b7f8bd6b046ae84734dbefeac28
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75442467"
---
# <a name="generate-shared-access-signature"></a>Gerar assinatura de acesso compartilhado

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como gerar [Assinatura de Acesso Partilhado](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) usando expressões e encaminhar o pedido para o armazenamento Azure com a política de reescrita-uri. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-samples.md)

## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre as políticas da APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Amostras de política](../policy-samples.md)

