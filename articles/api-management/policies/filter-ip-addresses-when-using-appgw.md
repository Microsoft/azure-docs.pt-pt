---
title: Política de gestão da amostra da API - Filtrar no endereço IP ao utilizar o Gateway de Aplicação
titleSuffix: Azure API Management
description: Amostra de política de gestão Azure API - Demonstra como filtrar no endereço IP do pedido quando se utiliza um Gateway de aplicação.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75942480"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filtrar a pedido endereço IP ao utilizar um Gateway de aplicação

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como o filtro no endereço IP do pedido quando a instância de Gestão da API é acedida através de um Gateway de Aplicação ou de outro intermediário. Para definir ou editar um código de política, siga os passos descritos no [Conjunto ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [as amostras de política.](../policy-samples.md)

## <a name="policy"></a>Política

Cole o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de restrições de acesso](../api-management-access-restriction-policies.md)
+ [Amostras de política](../policy-samples.md)
