---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75893976"
---
Ao criar um espaço de trabalho azure machine learning, ou um recurso utilizado pelo espaço de trabalho, poderá receber um erro semelhante ao seguinte:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

A maioria dos fornecedores de recursos são automaticamente registados, mas não todos. Se receber esta mensagem, tem de registar o fornecedor mencionado.

Para obter informações sobre o registo de fornecedores de recursos, consulte Resolver erros para o registo do [fornecedor de recursos.](../articles/azure-resource-manager/templates/error-register-resource-provider.md)