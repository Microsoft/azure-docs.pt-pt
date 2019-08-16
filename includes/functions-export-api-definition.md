---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 49ac1a7585ddf2a6500c7e9382880109c3f7f431
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534267"
---
## <a name="export-an-api-definition"></a>Exportar uma definição de API
Você tem uma definição de OpenAPI para sua função, desde [criar uma definição de openapi para uma função](../articles/azure-functions/functions-openapi-definition.md). A próxima etapa nesse processo é exportar a definição de API para que o PowerApps e Microsoft Flow possam usá-la em uma API personalizada.

> [!IMPORTANT]
> Lembre-se de que você deve estar conectado ao Azure com as mesmas credenciais que você usa para seus locatários do PowerApps e do Microsoft Flow. Isso permite que o Azure crie a API personalizada e disponibilize-a para o PowerApps e o Microsoft Flow.

1. No [portal do Azure](https://portal.azure.com), clique no nome do aplicativo de funções (como **Function-demo-Energy**) >**definição de API**de recursos > de **plataforma**.

    ![Definição API](media/functions-export-api-definition/api-definition.png)

1. Clique em **exportar para PowerApps + Flow**.

    ![Origem de definição de API](media/functions-export-api-definition/export-api-1.png)

1. No painel direito, use as configurações conforme especificado na tabela.

    |Definição|Descrição|
    |--------|------------|
    |**Modo de exportação**|Selecione **expressa** para gerar automaticamente a API personalizada. A seleção **manual** exporta a definição de API, mas, em seguida, você deve importá-la para o PowerApps e Microsoft Flow manualmente. Para obter mais informações, consulte [exportar para o PowerApps e Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Ambiente**|Selecione o ambiente no qual a API personalizada deve ser salva. Para obter mais informações, consulte Visão [geral de ambientes (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) ou [geral de ambientes (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Nome da API personalizada**|Insira um nome, como `Turbine Repair`.|
    |**Nome da chave de API**|Insira o nome que o aplicativo e os construtores de fluxo devem ver na interface do usuário da API personalizada. Observe que o exemplo inclui informações úteis.|
 
    ![Exportar para as PowerApps e Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Clique em **OK**. A API personalizada agora é compilada e adicionada ao ambiente especificado.