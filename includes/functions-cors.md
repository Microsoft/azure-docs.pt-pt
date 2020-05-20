---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648264"
---
A Azure Functions suporta a partilha de recursos de origem cruzada (CORS). O CORS está configurado [no portal](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors) e através do [Azure CLI](/cli/azure/functionapp/cors). A lista de origens permitida seleções do CORS aplica-se ao nível da aplicação de funções. Com cors ativado, as respostas incluem o `Access-Control-Allow-Origin` cabeçalho. Para obter mais informações, consulte [Partilha de recursos de várias origens](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors). 