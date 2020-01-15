---
title: Política de gerenciamento de API de exemplo – filtro no endereço IP ao usar o gateway de aplicativo
titleSuffix: Azure API Management
description: Exemplo de política de gerenciamento de API do Azure – demonstra como filtrar por endereço IP de solicitação ao usar um gateway de aplicativo.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942480"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filtrar por endereço IP de solicitação ao usar um gateway de aplicativo

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como filtrar o endereço IP da solicitação quando a instância de gerenciamento de API é acessada por meio de um gateway de aplicativo ou outro intermediário. Para definir ou editar um código de política, siga as etapas descritas em [definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas de APIM:

+ [Políticas de restrições de acesso](../api-management-access-restriction-policies.md)
+ [Exemplos de política](../policy-samples.md)
