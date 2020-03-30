---
title: Política de gestão da Amostra API - Filtrar no endereço IP ao utilizar o Gateway da Aplicação
titleSuffix: Azure API Management
description: Amostra de política de gestão da API Azure - Demonstra como filtrar o endereço IP solicitado ao utilizar um Gateway de aplicação.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942480"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filtrar no endereço IP pedido ao utilizar um Gateway de Aplicação

Este artigo mostra uma amostra de política de gestão da API Azure que demonstra como filtra o endereço IP solicitado quando a instância de Gestão da API é acedida através de um Gateway de Aplicação ou de outro intermediário. Para definir ou editar um código de política, siga os passos descritos no [set ou edite uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [amostras políticas.](../policy-samples.md)

## <a name="policy"></a>Política

Cola o código no bloco **de entrada.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas da APIM:

+ [Políticas de restrições de acesso](../api-management-access-restriction-policies.md)
+ [Amostras políticas](../policy-samples.md)
