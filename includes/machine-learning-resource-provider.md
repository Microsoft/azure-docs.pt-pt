---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75893976"
---
Ao criar um espaço de trabalho Azure Machine Learning ou um recurso utilizado pelo espaço de trabalho, poderá receber um erro semelhante às seguintes mensagens:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

A maioria dos fornecedores de recursos estão automaticamente registados, mas não todos. Se receber esta mensagem, tem de registar o fornecedor mencionado.

Para obter informações sobre o registo de fornecedores de recursos, consulte [os erros de Resolução para o registo do fornecedor de recursos.](../articles/azure-resource-manager/templates/error-register-resource-provider.md)