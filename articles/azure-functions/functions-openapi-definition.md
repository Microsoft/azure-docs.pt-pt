---
title: Exponha as suas funções com a OpenAPI utilizando a Azure API Management
description: Crie uma definição de OpenAPI que permite que outras aplicações e serviços chamem a sua função no Azure.
ms.topic: tutorial
ms.date: 04/21/2020
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 7d63d5ea17184ffa6e456877079da0821a75d59e
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121499"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Criar uma definição OpenAPI para um API sem servidor usando a Gestão API Azure

AS APIs de REPOUSO são frequentemente descritas usando uma definição OpenAPI. Esta definição contém informações sobre as operações que estão disponíveis numa API e a forma como os dados de pedido e resposta para a API devem ser estruturados.

Neste tutorial, vai criar uma função que determina se uma reparação de emergência numa turbina eólica é rentável. Em seguida, cria-se uma definição OpenAPI para a aplicação de funções utilizando a [Azure API Management](../api-management/api-management-key-concepts.md) para que a função possa ser chamada de outras apps e serviços.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma função no Azure
> * Gerar uma definição OpenAPI usando a Gestão API Azure
> * Testar a definição, chamando a função
> * Descarregue a definição OpenAPI

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação, escalae e partilha de recursos.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Criar a função

Este tutorial utiliza uma função desencadeada em HTTP que leva dois parâmetros:

* O tempo estimado para fazer uma reparação de turbinas em horas.
* A capacidade da turbina, em kilowatts. 

A função calcula então quanto custará uma reparação e quanto receita a turbina poderia fazer num período de 24 horas. Para criar a função ativada em HTTP no [portal Azure:](https://portal.azure.com)

1. A partir do menu esquerdo da sua aplicação de funções, selecione **Funções**e, em seguida, selecione **Adicionar** a partir do menu superior.

1. Na janela **New Function,** selecione **O gatilho http**.

1. Para **nova função,** insira. `TurbineRepair` 

1. Escolha **função** na lista de desistência do **[nível](functions-bindings-http-webhook-trigger.md#http-auth)** de autorização e, em seguida, selecione **Criar Função**.

    :::image type="content" source="media/functions-openapi-definition/select-http-trigger-openapi.png" alt-text="Criar função HTTP para OpenAPI":::

1. Selecione **Código + Teste**, e, em seguida, selecione **run.csx** da lista de drop-down. Substitua o conteúdo do ficheiro script run.csx C# pelo seguinte código e, em seguida, escolha **Guardar:**

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Este código de função devolve uma mensagem ou `Yes` para indicar se uma `No` reparação de emergência é rentável. Também devolve a oportunidade de receita que a turbina representa e o custo para consertar a turbina.

1. Para testar a função, selecione **Test,** selecione o separador **de entrada,** introduza a seguinte entrada para o **Corpo**e, em seguida, selecione **Executar:**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    :::image type="content" source="media/functions-openapi-definition/test-function.png" alt-text="Testar a função no portal do Azure":::

    A seguinte saída é devolvida no separador **Saída:**

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Agora, tem uma função que determina a rentabilidade das reparações de emergência. Em seguida, gera uma definição OpenAPI para a aplicação de funções.

## <a name="generate-the-openapi-definition"></a>Gerar a definição de OpenAPI

Para gerar a definição OpenAPI:

1. Selecione a aplicação de funções, escolha **a Gestão API** a partir do menu esquerdo e, em seguida, selecione **Criar nova** sob **gestão de API**.

    :::image type="content" source="media/functions-openapi-definition/select-all-settings-openapi.png" alt-text="Escolha gestão da API":::


1. Utilize as definições de Gestão API conforme especificado no quadro seguinte:

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente exclusivo | Um nome é gerado com base no nome da sua aplicação de funções. |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual este novo recurso é criado. |  
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)** |  myResourceGroup | O mesmo recurso que a sua aplicação de funções, que deve ser definida para si. |
    | **Localização** | E.U.A. Oeste | Escolha a localização dos EUA Ocidentais. |
    | **Nome da organização** | Contoso | O nome da organização utilizada no portal de desenvolvimento e para notificações por e-mail. |
    | **E-mail do administrador** | seu e-mail | E-mail que recebeu notificações do sistema da API Management. |
    | **Nível de preços** | Consumo | O nível de consumo não está disponível em todas as regiões. Para obter detalhes completos sobre preços, consulte a página de preços da [API Management](https://azure.microsoft.com/pricing/details/api-management/) |

    ![Criar novo serviço de Gestão API](media/functions-openapi-definition/new-apim-service-openapi.png)

1. Selecione **Criar** para criar a instância da Gestão de API (pode demorar vários minutos).

1. Depois de o Azure criar a instância, permite a opção **Enable Application Insights** na página. Selecione-o para enviar registos para o mesmo local que a aplicação de funções e, em seguida, selecione **Link API**.

1. As **Funções Import Azure** abrem com a função **TurbineRepair** em destaque. Escolha **Selecionar** para continuar.

    ![Import Azure Funções em Gestão de API](media/functions-openapi-definition/import-function-openapi.png)

1. Na página **'Criar a partir da Função App',** aceite as predefinições e, em seguida, selecione **Criar**.

    :::image type="content" source="media/functions-openapi-definition/create-function-openapi.png" alt-text="Criar a partir da App função":::

    Azure cria a API para a função.

## <a name="test-the-api"></a>Testar a API

Antes de utilizar a definição OpenAPI, deve verificar se a API funciona.

1. Na página da sua aplicação de funções, selecione **API Management,** selecione o separador **Teste** e, em seguida, selecione **POST TurbineRepair**. 

1. Introduza o seguinte código no **organismo Solicitar:**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Selecione **Enviar**e, em seguida, ver a **resposta HTTP**.

    :::image type="content" source="media/functions-openapi-definition/test-function-api-openapi.png" alt-text="Função de teste API":::

## <a name="download-the-openapi-definition"></a>Descarregue a definição OpenAPI

Se a sua API funcionar como esperado, pode baixar a definição OpenAPI.

1. Selecione baixar a **definição OpenAPI** no topo da página.
   
   ![Transferir a definição OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Guarde o ficheiro JSON descarregado e abra-o. Reveja a definição.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Usou a integração da API Management para gerar uma definição OpenAPI das suas funções. Agora pode editar a definição em Gestão API no portal. Também pode [saber mais sobre a Gestão da API.](../api-management/api-management-key-concepts.md)

> [!div class="nextstepaction"]
> [Editar a definição OpenAPI na Gestão da API](../api-management/edit-api.md)
