---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 04/16/2021
ms.author: vlvinogr
ms.openlocfilehash: 329ea156b296810395eb7b8e8310bed5ee0ee4c9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601940"
---
## <a name="append-other-apis"></a>Anexar outras APIs

Pode compor uma API de APIs exposta por diferentes serviços, incluindo:
* A Especificação OpenAPI
* UM SABÃO API
* A funcionalidade de Apps API do Azure App Service
* App de função Azure
* Azure Logic Apps
* Azure Service Fabric

Anexar uma API diferente à sua API existente utilizando os seguintes passos. 

>[!NOTE] 
> Quando importar outra API, as operações são anexadas à API atual.

1. Vá para a instância de Gestão de API do Azure no portal do Azure.

    :::image type="content" source="./media/api-management-append-apis/service-page.png" alt-text="Vá ao exemplo da Azure API Mgmt":::

1. Selecione **APIs** no menu à esquerda.

    :::image type="content" source="./media/api-management-append-apis/api-select.png" alt-text="Selecione APIs":::

1. Clique em **...** junto à API à qual quer anexar outra API.
1. Selecione **Importar** no menu pendente.

    :::image type="content" source="./media/api-management-append-apis/append-01.png" alt-text="Selecione importar":::

1. Selecione um serviço a partir do qual quer importar uma API.

    :::image type="content" source="./media/api-management-append-apis/select-to-import.png" alt-text="Selecione serviço":::