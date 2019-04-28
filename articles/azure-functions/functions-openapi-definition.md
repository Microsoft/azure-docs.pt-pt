---
title: Criar uma definição de OpenAPI para uma função | Microsoft Docs
description: Crie uma definição de OpenAPI que permite que outras aplicações e serviços chamem a sua função no Azure.
services: functions
keywords: OpenAPI, Swagger, aplicações na nuvem, serviços cloud,
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 6daa29b4e8f09a4f8a40c3b92d2e2e86a5dea6aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026701"
---
# <a name="create-an-openapi-definition-for-a-function"></a>Criar uma definição de OpenAPI para uma função

As APIs REST são muitas vezes descritas com uma definição de OpenAPI (anteriormente conhecida como um ficheiro [Swagger](https://swagger.io/)). Esta definição contém informações sobre as operações que estão disponíveis numa API e a forma como os dados de pedido e resposta para a API devem ser estruturados.

Neste tutorial, vai criar uma função que determina se uma reparação de emergência numa turbina eólica é rentável. Em seguida, vai criar uma definição de OpenAPI para a aplicação de funções para que a função possa ser chamada a partir de outras aplicações e serviços.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma função no Azure
> * Gerar uma definição de OpenAPI com ferramentas OpenAPI
> * Modificar a definição para fornecer metadados adicionais
> * Testar a definição, chamando a função

> [!IMPORTANT]
> A funcionalidade de OpenAPI está atualmente em pré-visualização e só está disponível para a versão 1.x do runtime das funções do Azure.

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. Uma aplicação Function App permite agrupar funções como uma unidade lógica para uma maior facilidade de gestão, implementação, dimensionamento e partilha de recursos. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="set-the-functions-runtime-version"></a>Definir a versão de runtime de funções

Por predefinição, a aplicação de função que cria utiliza a versão 2.x do runtime. Tem de definir a versão de runtime para 1.x antes de criar a sua função.

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

## <a name="create-the-function"></a>Criar a função

Este tutorial utiliza uma função acionada por HTTP que utiliza dois parâmetros: o tempo estimado para fazer uma reparação na turbina (em horas) e a capacidade da turbina (em quilowatts). Em seguida, a função calcula o custo da reparação e a receita que a turbina poderá gerar num período de 24 horas.

1. Expanda a aplicação de funções e selecione o botão **+** junto a **Funções**. Se esta for a primeira função na sua aplicação de funções, selecione **Função personalizada**. É apresentado o conjunto completo de modelos de função. 

    ![Página de início rápido das funções no portal do Azure](media/functions-openapi-definition/add-first-function.png)

1. No campo de pesquisa, escreva `http` e escolha **C#** para o modelo de acionador HTTP. 

    ![Escolher o acionador HTTP](./media/functions-openapi-definition/select-http-trigger-portal.png)

1. Escreva `TurbineRepair` para o **Nome** da função, escolha `Function` para **[Nível de autenticação](functions-bindings-http-webhook.md#http-auth)** e, em seguida, selecione **Criar**.  

    ![Criar a função acionada por HTTP](./media/functions-openapi-definition/select-http-trigger-portal-2.png)

1. Substitua o conteúdo do ficheiro run.csx pelo código seguinte e clique em **Guardar**:

    ```csharp
    using System.Net;

    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        //Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();
        int hours = data.hours;
        int capacity = data.capacity;

        //Formulas to calculate revenue and cost
        double revenueOpportunity = capacity * revenuePerkW * 24;  
        double costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;

        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        }

        return req.CreateResponse(HttpStatusCode.OK, new{
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

Agora, tem uma função que determina a rentabilidade das reparações de emergência. Em seguida, vai gerar e modificar uma definição de OpenAPI para a aplicação de funções.

## <a name="generate-the-openapi-definition"></a>Gerar a definição de OpenAPI

Agora, está pronto para gerar a definição de OpenAPI. Esta definição pode ser utilizada por outras tecnologias Microsoft, como Aplicações API, [PowerApps](functions-powerapps-scenario.md) e [Microsoft Flow](../azure-functions/app-service-export-api-to-powerapps-and-flow.md), bem como por ferramentas de programadores de terceiros, como o [Postman](https://www.getpostman.com/docs/importing_swagger) e [muitos mais pacotes](https://swagger.io/tools/).

1. Selecione apenas os *verbos* suportados pela sua API (neste caso, POST). Isto torna a definição da API gerada mais clara.

    1. No separador **Integrar** da nova função de Acionador HTTP, altere **Métodos HTTP permitidos** para **Métodos selecionados**

    1. Em **Métodos HTTP selecionados**, desmarque todas as opções, exceto **POST** e, em seguida, clique em **Guardar**.

        ![Métodos HTTP selecionados](media/functions-openapi-definition/selected-http-methods.png)

1. Clique no nome da aplicação de funções (como **function-demo-energy**) > **Funcionalidades de plataforma** > **Definição da API**.

    ![Definição da API](media/functions-openapi-definition/api-definition.png)

1. No separador **Definição da API**, clique em **Função**.

    ![Origem de definição da API](media/functions-openapi-definition/api-definition-source.png)

    Este passo ativa um conjunto de opções de OpenAPI para a sua aplicação de funções, incluindo um ponto final para alojar um ficheiro de OpenAPI do domínio da sua aplicação de funções, uma cópia inline do [OpenAPI Editor](https://editor.swagger.io) e um gerador de modelos de definição de API.

1. Clique em **Gerar modelo de definição de API** > **Guardar**.

    ![Gerar modelo de definição de API](media/functions-openapi-definition/generate-template.png)

    O Azure analisa a sua aplicação de funções relativamente à existência de funções de Acionador HTTP e utiliza as informações do ficheiro functions.json para gerar uma definição de OpenAPI. Eis a definição que é gerada:

    ```yaml
    swagger: '2.0'
    info:
    title: function-demo-energy.azurewebsites.net
    version: 1.0.0
    host: function-demo-energy.azurewebsites.net
    basePath: /
    schemes:
    - https
    - http
    paths:
    /api/TurbineRepair:
        post:
        operationId: /api/TurbineRepair/post
        produces: []
        consumes: []
        parameters: []
        description: >-
            Replace with Operation Object
            #https://swagger.io/specification/#operationObject
        responses:
            '200':
            description: Success operation
        security:
            - apikeyQuery: []
    definitions: {}
    securityDefinitions:
    apikeyQuery:
        type: apiKey
        name: code
        in: query
    ```

    Esta definição é descrita como um _modelo_ porque requer mais metadados para ser uma definição de OpenAPI completa. No próximo passo, vai modificar a definição.

## <a name="modify-the-openapi-definition"></a>Modificar a definição de OpenAPI

Agora que tem uma definição de modelo, modificá-la para fornecer metadados adicionais sobre as operações de API e as estruturas de dados. Em **Definição da API**, elimine a definição gerada a partir de `post` na parte inferior da definição, cole no conteúdo abaixo e clique em **Guardar**.

```yaml
    post:
      operationId: CalculateCosts
      description: Determines if a technician should be sent for repair
      summary: Calculates costs
      x-ms-summary: Calculates costs
      x-ms-visibility: important
      produces:
        - application/json
      consumes:
        - application/json
      parameters:
        - name: body
          in: body
          description: Hours and capacity used to calculate costs
          x-ms-summary: Hours and capacity
          x-ms-visibility: important
          required: true
          schema:
            type: object
            properties:
              hours:
                description: The amount of effort in hours required to conduct repair
                type: number
                x-ms-summary: Hours
                x-ms-visibility: important
              capacity:
                description: The max output of a turbine in kilowatts
                type: number
                x-ms-summary: Capacity
                x-ms-visibility: important
      responses:
        200:
          description: Message with cost and revenue numbers
          x-ms-summary: Message
          schema:
           type: object
           properties:
            message:
              type: string
              description: Returns Yes or No depending on calculations
              x-ms-summary: Message 
            revenueOpportunity:
              type: string
              description: The revenue opportunity cost
              x-ms-summary: RevenueOpportunity 
            costToFix:
              type: string
              description: The cost in $ to fix the turbine
              x-ms-summary: CostToFix
      security:
        - apikeyQuery: []
definitions: {}
securityDefinitions:
  apikeyQuery:
    type: apiKey
    name: code
    in: query
```

Neste caso, pode colar apenas nos metadados atualizados, mas é importante compreender os tipos de modificações que fizemos ao modelo predefinido:

* Especificámos que a API produz e consome dados num formato JSON.

* Especificámos os parâmetros necessários, com os respetivos nomes e tipos de dados.

* Especificámos os valores devolvidos para uma resposta com êxito, com os respetivos nomes e tipos de dados.

* Fornecemos resumos e descrições amigáveis para a API e as respetivas operações e parâmetros. Isto é importante para as pessoas que vão utilizar esta função.

* Adicionámos as extensões x-ms-summary e x-ms-visibility, que são utilizadas na IU para o Microsoft Flow e Logic Apps. Para obter mais informações, veja [Extensões OpenAPI para conectores personalizados no Microsoft Flow](https://preview.flow.microsoft.com/documentation/customapi-how-to-swagger/).

> [!NOTE]
> Deixámos a definição de segurança com o método de autenticação predefinido da chave de API. Deverá alterar esta secção da definição se tiver utilizado um tipo diferente de autenticação.

Para obter mais informações sobre a definição de operações de API, veja a [especificação de Open API](https://swagger.io/specification/#operationObject).

## <a name="test-the-openapi-definition"></a>Testar a definição de OpenAPI

Antes de utilizar a definição da API, é boa ideia testá-la na IU de Funções do Azure.

1. No separador **Gerir** da sua função, em **Chaves de Anfitrião**, copie a chave **predefinida**.

    ![Copiar a chave de API](media/functions-openapi-definition/copy-api-key.png)

    > [!NOTE]
    >Utilize esta chave para testes e também pode utilizá-la quando chama a API a partir de uma aplicação ou serviço.

1. Volte à definição de API: **function-demo-energy** > **Funcionalidades de plataforma** > **Definição da API**.

1. No painel da direita, clique em **Autenticar**, introduza a chave de API que copiou e clique em **Autenticar**.

    ![Autenticar com a chave de API](media/functions-openapi-definition/authenticate-api-key.png)

1. Desloque para baixo e clique em **Tentar esta operação**.

    ![Tentar esta operação](media/functions-openapi-definition/try-operation.png)

1. Introduza os valores para **horas** e **capacidade**.

    ![Introduzir parâmetros](media/functions-openapi-definition/parameters.png)

    Repare como a IU utiliza as descrições da definição de API.

1. Clique em **Enviar Pedido** e, em seguida, clique no separador **Estilos de formatação** para ver o resultado.

    ![Enviar um pedido](media/functions-openapi-definition/send-request.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma função no Azure
> * Gerar uma definição de OpenAPI com ferramentas OpenAPI
> * Modificar a definição para fornecer metadados adicionais
> * Testar a definição, chamando a função

Avance para o tópico seguinte para aprender a criar uma aplicação do PowerApps que utiliza a definição de OpenAPI que criou.

> [!div class="nextstepaction"]
> [Chamar uma função a partir do PowerApps](functions-powerapps-scenario.md)
