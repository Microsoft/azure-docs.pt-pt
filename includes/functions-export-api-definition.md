---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 49ac1a7585ddf2a6500c7e9382880109c3f7f431
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131737"
---
## <a name="export-an-api-definition"></a>Exportar uma definição de API
Tiver uma definição de OpenAPI para a função e do [criar uma definição de OpenAPI para uma função](../articles/azure-functions/functions-openapi-definition.md). A próxima etapa desse processo é exportar a definição de API para que o PowerApps e Microsoft Flow podem utilizá-lo numa API personalizada.

> [!IMPORTANT]
> Lembre-se de que tem de ser iniciada para o Azure com as mesmas credenciais que utiliza para o PowerApps e Microsoft Flow inquilinos. Isto permite que o Azure para criar a API personalizada e disponibilizá-lo para o PowerApps e Microsoft Flow.

1. Na [portal do Azure](https://portal.azure.com), clique no nome da aplicação de função (como **function-demo-energy**) > **recursos de plataforma** > **definição de API** .

    ![Definição da API](media/functions-export-api-definition/api-definition.png)

1. Clique em **exportar para PowerApps + Flow**.

    ![Origem de definição da API](media/functions-export-api-definition/export-api-1.png)

1. No painel da direita, utilize as definições conforme especificado na tabela.

    |Definição|Descrição|
    |--------|------------|
    |**Modo de exportação**|Selecione **Express** para gerar automaticamente a API personalizada. Selecionando **Manual** definição de exportações a API, mas, em seguida, deve importá-lo para PowerApps e Microsoft Flow manualmente. Para obter mais informações, consulte [exportar para PowerApps e Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Ambiente**|Selecione o ambiente em que a API personalizada deve ser guardada. Para obter mais informações, consulte [descrição geral de ambientes (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) ou [descrição geral de ambientes (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Nome da API personalizada**|Introduza um nome, como `Turbine Repair`.|
    |**Nome da chave de API**|Introduza o nome que os criadores de aplicações e fluxo deverá ver na interface de Usuário de API personalizada. Observe que o exemplo inclui informações úteis.|
 
    ![Exportar para as PowerApps e Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Clique em **OK**. A API personalizada é agora criada e adicionada para o ambiente especificado.