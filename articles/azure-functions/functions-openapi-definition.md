---
title: Exponha as suas funções com OpenAPI utilizando a Azure API Management
description: Crie uma definição de OpenAPI que permite que outras aplicações e serviços chamem a sua função no Azure.
ms.topic: tutorial
ms.date: 04/21/2020
ms.reviewer: sunayv
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47, references_regions
ms.openlocfilehash: 9083ff7d8f65c68ce8d173973a4eda650ac355aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88212910"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Crie uma definição OpenAPI para uma API sem servidor usando a Azure API Management

As APIs de REPOUSO são frequentemente descritas usando uma definição OpenAPI. Esta definição contém informações sobre as operações que estão disponíveis numa API e a forma como os dados de pedido e resposta para a API devem ser estruturados.

Neste tutorial, vai criar uma função que determina se uma reparação de emergência numa turbina eólica é rentável. Em seguida, cria uma definição OpenAPI para a aplicação de função usando [a Azure API Management](../api-management/api-management-key-concepts.md) para que a função possa ser chamada de outras aplicações e serviços.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma função no Azure
> * Gerar uma definição OpenAPI usando a Azure API Management
> * Testar a definição, chamando a função
> * Descarregue a definição OpenAPI

## <a name="create-a-function-app"></a>Criar uma aplicação de funções

Precisa de uma aplicação Function App para alojar a execução das suas funções. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para facilitar a gestão, implementação, escala e partilha de recursos.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Criar a função

Este tutorial utiliza uma função desencadeada HTTP que leva dois parâmetros:

* O tempo estimado para fazer uma reparação de turbinas, em horas.
* A capacidade da turbina, em kilowatts. 

A função calcula então quanto uma reparação vai custar, e quanto receita a turbina poderia fazer num período de 24 horas. Para criar a função HTTP desencadeada no [portal Azure:](https://portal.azure.com)

1. A partir do menu esquerdo da aplicação funções, selecione **Funções** e, em seguida, **selecione Adicionar** no menu superior.

1. Na janela **Nova Função,** selecione **'Despoletá-lo'.**

1. Para **Nova Função,** insira `TurbineRepair` . 

1. Escolha **a Função** a partir da lista de abandono **[do nível](functions-bindings-http-webhook-trigger.md#http-auth)** de autorização e, em seguida, selecione **Criar Função**.

    :::image type="content" source="media/functions-openapi-definition/select-http-trigger-openapi.png" alt-text="Criar função HTTP para OpenAPI":::

1. Selecione **Code + Test** e, em seguida, selecione **run.csx** da lista de drop-down. Substitua o conteúdo do ficheiro de script run.csx C# pelo seguinte código e, em seguida, escolha **Guardar**:

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

    Este código de função devolve uma mensagem `Yes` de ou para indicar se uma `No` reparação de emergência é rentável. Também devolve a oportunidade de receita que a turbina representa e o custo para fixar a turbina.

1. Para testar a função, selecione **Teste**, selecione o separador **Entrada,** introduza a seguinte entrada para o **Corpo** e, em seguida, selecione **Executar**:

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    :::image type="content" source="media/functions-openapi-definition/test-function.png" alt-text="Testar a função no portal do Azure":::

    A seguinte saída é devolvida no **separador Saída:**

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Agora, tem uma função que determina a rentabilidade das reparações de emergência. Em seguida, gera uma definição OpenAPI para a aplicação de função.

## <a name="generate-the-openapi-definition"></a>Gerar a definição de OpenAPI

Para gerar a definição OpenAPI:

1. Selecione a aplicação de função, escolha **API Management** a partir do menu esquerdo e, em seguida, selecione **Criar novo** em **Gestão de API.**

    :::image type="content" source="media/functions-openapi-definition/select-all-settings-openapi.png" alt-text="Escolha a gestão da API":::


1. Utilize as definições de Gestão da API conforme especificado no quadro seguinte:

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente exclusivo | Um nome é gerado com base no nome da sua aplicação de função. |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual este novo recurso é criado. |  
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)** |  myResourceGroup | O mesmo recurso que a sua aplicação de função, que deve ser configurada para si. |
    | **Localização** | E.U.A. Oeste | Escolha a localização do Oeste dos EUA. |
    | **Nome da organização** | Contoso | O nome da organização utilizada no portal do desenvolvedor e para notificações por e-mail. |
    | **E-mail do administrador** | seu e-mail | E-mail que recebeu notificações do sistema da API Management. |
    | **Escalão de preço** | Consumo | O nível de consumo não está disponível em todas as regiões. Para obter detalhes completos sobre preços, consulte a [página de preços da API Management](https://azure.microsoft.com/pricing/details/api-management/) |

    ![Criar novo serviço de Gestão API](media/functions-openapi-definition/new-apim-service-openapi.png)

1. Selecione **Criar** para criar a instância da Gestão de API (pode demorar vários minutos).

1. Depois de O Azure criar o caso, permite a opção **De Enable Application Insights** na página. Selecione-o para enviar registos para o mesmo local que a aplicação de função e, em seguida, selecione **Link API**.

1. As **Funções Import Azure** abre com a função **TurbineRepair** realçada. Selecione **Selecionar** para continuar.

    ![Import Azure Funções na Gestão da API](media/functions-openapi-definition/import-function-openapi.png)

1. Na página **'Criar a partir da aplicação de funções',** aceite as predefinições e, em seguida, selecione **Criar**.

    :::image type="content" source="media/functions-openapi-definition/create-function-openapi.png" alt-text="Criar a partir de App de Função":::

    Azure cria a API para a função.

## <a name="test-the-api"></a>Testar a API

Antes de utilizar a definição OpenAPI, deverá verificar se a API funciona.

1. Na sua página de aplicações de função, selecione **API Management,** selecione o separador **Teste** e, em seguida, selecione **POST TurbineRepair**. 

1. Introduza o seguinte código no **organismo Request:**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Selecione **Enviar** e, em seguida, ver a **resposta HTTP**.

    :::image type="content" source="media/functions-openapi-definition/test-function-api-openapi.png" alt-text="Função de teste API":::

## <a name="download-the-openapi-definition"></a>Descarregue a definição OpenAPI

Se a sua API funcionar como esperado, pode descarregar a definição OpenAPI.

1. Selecione **Baixar a definição OpenAPI** no topo da página.
   
   ![Transferir a definição OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Guarde o ficheiro JSON descarregado e, em seguida, abra-o. Reveja a definição.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Utilizou a integração da API Management para gerar uma definição OpenAPI das suas funções. Pode agora editar a definição na Gestão da API no portal. Também pode [saber mais sobre a API Management.](../api-management/api-management-key-concepts.md)

> [!div class="nextstepaction"]
> [Editar a definição OpenAPI na Gestão de API](../api-management/edit-api.md)
