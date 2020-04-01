---
title: Exponha as suas funções com a OpenAPI utilizando a Azure API Management
description: Crie uma definição de OpenAPI que permite que outras aplicações e serviços chamem a sua função no Azure.
ms.topic: tutorial
ms.date: 05/08/2019
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9465209467c83f7de075d16e724459c307d55bd3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77210213"
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

Em seguida, a função calcula o custo da reparação e a receita que a turbina poderá gerar num período de 24 horas. Para criar a função ativada em HTTP no [portal Azure:](https://portal.azure.com)

1. Expanda a sua **+** aplicação de funções e selecione o botão ao lado das **Funções**. Selecione **In-portal** > **Continue**.

1. Selecione **mais modelos...** em seguida, selecione **modelos** de acabamento e ver

1. Selecione o `TurbineRepair` gatilho HTTP, `Function` escreva para a função **Nome,** escolha para **[o nível de autenticação,](functions-bindings-http-webhook-trigger.md#http-auth)** e, em seguida, selecione **Criar**.  

    ![Criar função HTTP para OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Substitua o conteúdo do ficheiro script run.csx C# pelo seguinte código e, em seguida, escolha **Guardar:**

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

    Este código de função devolve uma mensagem `Yes` ou `No` para indicar se uma reparação de emergência é rentável, bem como a oportunidade de receita que a turbina representa e o custo para reparar a turbina.

1. Para testar a função, clique em **Testar** na extrema direita para expandir o separador de teste. Introduza o seguinte valor para o **corpo de Pedido,** e, em seguida, clique em **Executar**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Testar a função no portal do Azure](media/functions-openapi-definition/test-function.png)

    O seguinte valor é devolvido no corpo da resposta.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Agora, tem uma função que determina a rentabilidade das reparações de emergência. Em seguida, gera uma definição OpenAPI para a aplicação de funções.

## <a name="generate-the-openapi-definition"></a>Gerar a definição de OpenAPI

Agora, está pronto para gerar a definição de OpenAPI.

1. Selecione a aplicação de funções, em seguida, nas **funcionalidades da Plataforma,** escolha **Gestão API** e selecione **Criar nova** sob **gestão de API**.

    ![Escolha gestão api em funcionalidades da plataforma](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Utilize as definições de Gestão API conforme especificado na tabela abaixo da imagem.

    ![Criar novo serviço de Gestão API](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente exclusivo | Um nome é gerado com base no nome da sua aplicação de funções. |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual este novo recurso é criado. |  
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  myResourceGroup | O mesmo recurso que a sua aplicação de funções, que deve ser definida para si. |
    | **Localização** | E.U.A. Oeste | Escolha a localização dos EUA Ocidentais. |
    | **Nome da organização** | Contoso | O nome da organização utilizada no portal de desenvolvimento e para notificações por e-mail. |
    | **E-mail do administrador** | seu e-mail | E-mail que recebeu notificações do sistema da API Management. |
    | **Nível de preços** | Consumo (pré-visualização) | O nível de consumo está em pré-visualização e não está disponível em todas as regiões. Para obter detalhes completos sobre preços, consulte a página de preços da [API Management](https://azure.microsoft.com/pricing/details/api-management/) |

1. Selecione **Criar** para criar a instância da Gestão de API (pode demorar vários minutos).

1. Selecione **Ativar insights** de aplicação para enviar registos para o mesmo local que a aplicação de função, em seguida, aceitar as restantes predefinições e selecionar **Link API**.

1. As **Funções Import Azure** abrem com a função **TurbineRepair** em destaque. Escolha **Selecionar** para continuar.

    ![Import Azure Funções em Gestão de API](media/functions-openapi-definition/import-function-openapi.png)

1. Na página Criar a **partir da App função,** aceite as predefinições e selecione **Criar**

    ![Criar a partir da App função](media/functions-openapi-definition/create-function-openapi.png)

A API é agora criada para a função.

## <a name="test-the-api"></a>Testar a API

Antes de utilizar a definição OpenAPI, deve verificar se a API funciona.

1. No separador **Teste** da sua função, selecione o funcionamento **DO POST.**

1. Insira valores por **horas** e **capacidade**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Clique em **Enviar**e, em seguida, veja a resposta HTTP.

    ![Função de teste API](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>Descarregue a definição OpenAPI

Se a sua API funcionar como esperado, pode baixar a definição OpenAPI.

1. Selecione baixar a **definição OpenAPI** no topo da página.
   
   ![Transferir a definição OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Abra o ficheiro JSON descarregado e reveja a definição.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Usou a integração da API Management para gerar uma definição OpenAPI das suas funções. Agora pode editar a definição em Gestão API no portal. Também pode [saber mais sobre a Gestão da API.](../api-management/api-management-key-concepts.md)

> [!div class="nextstepaction"]
> [Editar a definição OpenAPI na Gestão da API](../api-management/edit-api.md)
