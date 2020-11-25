---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 2bfa356deeede1c16bd5a464ea7081132a67faf6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994715"
---
## <a name="append-other-apis"></a>Anexar outras APIs

Uma API pode ser constituída por APIs expostas por diferentes serviços, incluindo a Especificação OpenAPI, uma API SOAP, a funcionalidade Aplicações API do Serviço de Aplicações do Azure, o Azure Function App, o Azure Logic Apps e o Azure Service Fabric.

![Importar uma API](./media/api-management-append-apis/import.png)

Para anexar uma API diferente à sua API existente, conclua os passos seguintes. Quando importar outra API, as operações são anexadas à API atual.

1. Vá para a instância de Gestão de API do Azure no portal do Azure.
2. Selecione **APIs** no menu à esquerda.
3. Clique em **...** junto à API à qual quer anexar outra API.
4. Selecione **Importar** no menu pendente.
5. Selecione um serviço a partir do qual quer importar uma API.