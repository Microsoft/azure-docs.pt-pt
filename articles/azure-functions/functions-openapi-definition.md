---
title: Criar uma definição de OpenAPI para uma API sem servidor usando o gerenciamento de API do Azure
description: Crie uma definição de OpenAPI que permite que outras aplicações e serviços chamem a sua função no Azure.
keywords: OpenAPI, Swagger, aplicações na nuvem, serviços cloud,
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 54a4c6eba094231e8e73cdef87b911dfba20f657
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533537"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Criar uma definição de OpenAPI para uma API sem servidor usando o gerenciamento de API do Azure

As APIs REST geralmente são descritas usando uma definição de OpenAPI. Esta definição contém informações sobre as operações que estão disponíveis numa API e a forma como os dados de pedido e resposta para a API devem ser estruturados.

Neste tutorial, vai criar uma função que determina se uma reparação de emergência numa turbina eólica é rentável. Em seguida, você cria uma definição de OpenAPI para o aplicativo de funções usando o [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) para que a função possa ser chamada de outros aplicativos e serviços.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma função no Azure
> * Gerar uma definição de OpenAPI usando o gerenciamento de API do Azure
> * Testar a definição, chamando a função
> * Baixar a definição de OpenAPI

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. Um aplicativo de funções permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação, o dimensionamento e o compartilhamento de recursos.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Criar a função

Este tutorial usa uma função disparada por HTTP que utiliza dois parâmetros:

* O tempo estimado para fazer um reparo de turbina, em horas.
* A capacidade da turbina, em quilowatts. 

Em seguida, a função calcula o custo da reparação e a receita que a turbina poderá gerar num período de 24 horas. PARA criar a função disparada por HTTP no [portal do Azure](https://portal.azure.com).

1. Expanda a aplicação de funções e selecione o botão **+** junto a **Funções**. Selecione**continuar** **no portal** > .

1. Selecione **mais modelos...** e, em seguida, selecione **concluir e exibir modelos**

1. Selecione gatilho http, digite `TurbineRepair` para o **nome**da função, `Function` escolha para **[nível de autenticação](functions-bindings-http-webhook.md#http-auth)** e, em seguida, selecione **criar**.  

    ![Criar função HTTP para OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Substitua o conteúdo do arquivo de script Run C# . CSX pelo código a seguir e escolha **salvar**:

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

1. Para testar a função, clique em **Testar** na extremidade direita para expandir o separador de teste. Introduza o seguinte valor para o **Corpo do pedido** e, em seguida, clique em **Executar**.

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

Agora, tem uma função que determina a rentabilidade das reparações de emergência. Em seguida, você gera uma definição de OpenAPI para o aplicativo de funções.

## <a name="generate-the-openapi-definition"></a>Gerar a definição de OpenAPI

Agora, está pronto para gerar a definição de OpenAPI.

1. Selecione o aplicativo de funções, em **recursos de plataforma**, escolha **Gerenciamento de API** e selecione **criar novo** em **Gerenciamento de API**.

    ![Escolha o gerenciamento de API nos recursos da plataforma](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Use as configurações de gerenciamento de API conforme especificado na tabela abaixo da imagem.

    ![Criar novo serviço de gerenciamento de API](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | Nome globalmente exclusivo | Um nome é gerado com base no nome do seu aplicativo de funções. |
    | **Subscrição** | A sua subscrição | A assinatura sob a qual esse novo recurso é criado. |  
    | **[Grupo de Recursos](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | O mesmo recurso que seu aplicativo de funções, que deve ser definido para você. |
    | **Location** | EUA Oeste | Escolha o local oeste dos EUA. |
    | **Nome da organização** | Contoso | O nome da organização usada no portal do desenvolvedor e para notificações por email. |
    | **E-mail do administrador** | seu email | Email que recebeu notificações do sistema do gerenciamento de API. |
    | **Escalão de preço** | Consumo (visualização) | A camada de consumo está em visualização e não está disponível em todas as regiões. Para obter detalhes completos de preços, consulte a [página de preços do gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/) |

1. Escolha **criar** para criar a instância de gerenciamento de API, que pode levar vários minutos.

1. Selecione **habilitar Application insights** para enviar logs para o mesmo local que o aplicativo de funções, em seguida, aceite os padrões restantes e selecione **vincular API**.

1. O **Azure Functions de importação** é aberto com a função **TurbineRepair** realçada. Escolha **selecionar** para continuar.

    ![Importar Azure Functions para o gerenciamento de API](media/functions-openapi-definition/import-function-openapi.png)

1. Na página **criar de aplicativo de funções** , aceite os padrões e selecione **criar**

    ![Criar a partir da Aplicação de Função](media/functions-openapi-definition/create-function-openapi.png)

Agora, a API é criada para a função.

## <a name="test-the-api"></a>Testar a API

Antes de usar a definição de OpenAPI, você deve verificar se a API funciona.

1. Na guia **teste** da sua função, selecione **post** Operation.

1. Insira valores para **horas** e **capacidade**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Clique em **Enviar**e exiba a resposta http.

    ![API de função de teste](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>Baixar a definição de OpenAPI

Se sua API funcionar conforme o esperado, você poderá baixar a definição de OpenAPI.

1. Selecione **baixar definição de openapi** na parte superior da página.
   
   ![Transferir a definição OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Abra o arquivo JSON baixado e examine a definição.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Você usou a integração do gerenciamento de API para gerar uma definição de OpenAPI de suas funções. Agora você pode editar a definição no gerenciamento de API no Portal. Você também pode [aprender mais sobre o gerenciamento de API](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Editar a definição de OpenAPI no gerenciamento de API](../api-management/edit-api.md)
