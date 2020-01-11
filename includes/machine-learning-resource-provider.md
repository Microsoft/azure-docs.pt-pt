---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893976"
---
Ao criar um espaço de trabalho Azure Machine Learning ou um recurso usado pelo espaço de trabalho, você pode receber um erro semelhante às seguintes mensagens:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

A maioria dos provedores de recursos são automaticamente registrados, mas não todos. Se você receber essa mensagem, precisará registrar o provedor mencionado.

Para obter informações sobre como registrar provedores de recursos, consulte [resolver erros de registro do provedor de recursos](../articles/azure-resource-manager/templates/error-register-resource-provider.md).