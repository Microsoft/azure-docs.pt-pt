---
title: Adicionar e executar código personalizado no Azure Logic Apps com as funções do Azure | Documentos da Microsoft
description: Saiba como adicionar e executar fragmentos de código personalizado no Azure Logic Apps com as funções do Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 08/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2bec33a4a8540f9599cf1d479f1f59c4cde39bd2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861592"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Adicionar e executar fragmentos de código personalizado no Azure Logic Apps com as funções do Azure

Quando quiser executar apenas suficiente código que executa uma tarefa específica nas suas aplicações lógicas, pode criar suas próprias funções com [as funções do Azure](../azure-functions/functions-overview.md). Este serviço ajuda-o a criar o node. js, C#, e F# fragmentos de código para que não tenha de criar uma aplicação completa ou a infraestrutura para a execução do seu código. As funções do Azure fornece computação sem servidor na cloud e é útil para realizar tarefas como nestes exemplos:

* Estenda o comportamento da sua aplicação lógica com as funções em node. js ou c#.
* Realizar cálculos em seu fluxo de trabalho de aplicação lógica.
* Aplicar a formatação avançada ou campos nas suas aplicações lógicas de computação.

Também pode [chamar aplicações lógicas do dentro das funções do Azure](#call-logic-app).

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este artigo, precisa destes itens:

* Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">Inscreva-se uma conta gratuita do Azure</a>. 

* Uma aplicação de função do Azure, que é um contentor para as funções do Azure e a função do Azure. Se não tiver uma aplicação de funções [primeiro a criar a sua aplicação function app](../azure-functions/functions-create-first-azure-function.md). Pode, em seguida, criar a sua função seja [separadamente fora da sua aplicação lógica](#create-function-external), ou [de dentro da sua aplicação lógica](#create-function-designer) no Estruturador da aplicação lógica.

  Existentes e novas aplicações de funções e as funções têm os mesmos requisitos para trabalhar com o logic apps:

  * A aplicação de função tem de ter a mesma subscrição do Azure que a sua aplicação lógica.

  * A função utiliza um acionador HTTP, por exemplo, o **acionador HTTP** modelo de função para **JavaScript** ou **c#**. 

    O modelo de Acionador HTTP pode aceitar o conteúdo que tenha `application/json` tipo da sua aplicação lógica. 
    Quando adiciona uma função do Azure à sua aplicação lógica, o Estruturador da aplicação lógica mostra as funções personalizadas criadas através deste modelo na sua subscrição do Azure. 

  * A função não usa rotas personalizadas, a menos que definiu uma [definição de OpenAPI](../azure-functions/functions-openapi-definition.md), anteriormente conhecido como um [ficheiro Swagger](https://swagger.io/). 
  
  * Se definiu uma definição de OpenAPI para a função, o Designer de aplicações lógicas dá-lhe uma experiência mais rica para trabalhar com parâmetros da função. Antes da aplicação lógica pode localizar e acessar as funções com as definições de OpenAPI [configure a sua aplicação de função, seguindo estes passos](#function-swagger).

* A aplicação de lógica onde pretende adicionar a função, incluindo uma [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa na sua aplicação lógica 

  Antes de poder adicionar ações que podem executar as funções, tem de iniciar a aplicação lógica com um acionador.

  Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>Criar aplicações lógicas fora de funções

Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, criar a sua aplicação de função do Azure, que tem de ter a mesma subscrição do Azure que a sua aplicação lógica e, em seguida, criar a sua função do Azure.
Se estiver familiarizado com a criação de funções do Azure, saiba como [criar a primeira função no portal do Azure](../azure-functions/functions-create-first-azure-function.md), mas tenha em atenção estes requisitos para a criação de funções que pode chamar a partir das aplicações lógicas:

* Certifique-se de que seleciona os **acionador HTTP** modelo de função por qualquer **JavaScript** ou **c#**.

  ![Acionador HTTP - JavaScript ou em C#](./media/logic-apps-azure-functions/http-trigger-function.png)

<a name="function-swagger"></a>

* Opcionalmente, se [gerar uma definição de API](../azure-functions/functions-openapi-definition.md), anteriormente conhecido como um [ficheiro Swagger](https://swagger.io/), por sua função, pode obter uma experiência mais rica ao trabalhar com parâmetros de função no Designer de aplicações lógicas. Para configurar a sua aplicação de funções para que a aplicação lógica pode encontrar e utilizar as funções que tenham uma descrição do Swagger, siga estes passos:

  1. Certifique-se de que a sua aplicação function app está ativamente em execução.

  2. Na sua aplicação de função, configure [Cross-Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para que todas as origens são permitidas ao seguir estes passos:

     1. Do **aplicações Function App** , selecione a aplicação de função > **recursos da plataforma** > **CORS**.

        ![Selecione a sua aplicação de função > "Funcionalidades de plataforma" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

     2. Sob **CORS**, adicione o `*` caráter universal de caracteres, mas remova todas as outras origens na lista e escolha **guardar**.

        ![Definir "CORS * para o caráter universal" * "](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>Valores de propriedade de acesso no interior de pedidos de HTTP

Funções de Webhook podem aceitar pedidos HTTP como entradas e passar esses pedidos para outras funções. Por exemplo, embora tenha de Logic Apps [funções que converter valores de DateTime](../logic-apps/workflow-definition-language-functions-reference.md), esta função de JavaScript de exemplo básico mostra como pode acessar uma propriedade dentro de um objeto de pedido que é passado para a função e executar operações no esse valor de propriedade. Para aceder às propriedades dentro de objetos, este exemplo utiliza a [operador de ponto (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors): 

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Eis o que acontece dentro desta função:

1. A função cria um `data` variável e atribui o `body` objeto dentro de `request` objeto para essa variável. A função usa o operador de ponto (.) para fazer referência a `body` dentro de objeto a `request` objeto: 

   ```javascript
   var data = request.body;
   ```

2. A função agora pode aceder a `date` propriedade através do `data` variável e converter valor de propriedade do tipo DateTime DateString escreva chamando o `ToDateString()` função. A função também devolve o resultado através do `body` propriedade na resposta da função: 

   ```javascript
   body: data.date.ToDateString();
   ```

Agora que criou sua função do Azure, siga os passos para saber como [adicione funções para aplicações lógicas](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Crie funções dentro de aplicações lógicas

Antes de poder criar uma função do Azure a partir de dentro da sua aplicação lógica no Estruturador da aplicação lógica, primeiro tem de ter uma aplicação de função do Azure, o que é um contentor para as suas funções. Se não tiver uma aplicação de funções, primeiro a criar essa aplicação de função. Ver [criar a primeira função no portal do Azure](../azure-functions/functions-create-first-azure-function.md). 

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra a aplicação lógica no Estruturador da aplicação lógica. 

2. Para criar e adicionar a sua função, siga o passo que se aplica ao seu cenário:

   * No último passo no fluxo de trabalho da sua aplicação lógica, escolha **novo passo**.

   * Entre os passos existentes no fluxo de trabalho da sua aplicação lógica, mova o rato por cima da seta, selecione o sinal de adição (+) iniciar sessão e, em seguida, selecione **adicionar uma ação**.

3. Na caixa de pesquisa, introduza "azure functions" como o filtro.
Na lista de ações, selecione a ação: **Escolha uma função do Azure - as funções do Azure** 

   ![Localizar "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Na lista de aplicações de função, selecione a sua aplicação de função. Depois de abre a lista de ações, selecione a ação: **As funções do Azure - criar nova função**

   ![Selecione a sua aplicação de função](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. No editor de definição de função, defina sua função:

   1. Na **nome da função** caixa, indique um nome para a função. 

   2. Na **código** caixa, adicione o seu código para o modelo de função, incluindo a resposta e a carga que pretende devolvido à sua aplicação lógica após a conclusão da sua função em execução. 

      ![Definir a sua função](./media/logic-apps-azure-functions/function-definition.png)

      No código do modelo, o  *`context` objeto* refere-se para a mensagem que a aplicação lógica envia através da **corpo do pedido** campo num passo posterior. 
      Para o acesso a `context` do objeto de propriedades a partir de dentro da sua função, utilize esta sintaxe: 

      `context.body.<property-name>`

      Por exemplo, para referência a `content` propriedade dentro do `context` de objeto, utilize esta sintaxe: 

      `context.body.content`

      O código de modelo também inclui uma `input` variável, que armazena o valor da `data` parâmetro para que sua função pode executar operações nesse valor. 
      Dentro de funções de JavaScript, o `data` variável também é um atalho para `context.body`.

      > [!NOTE]
      > O `body` propriedade aqui aplica-se para o `context` objeto e não é igual a **corpo** token a partir de uma ação de saída do, que também poderá passar para a sua função. 
 
   3. Quando tiver terminado, escolha **Create** (Criar).

6. Na **corpo do pedido** caixa, forneça a sua função entrada do, que tem de ser formatado como um objeto JavaScript Object Notation (JSON). 

   Esta entrada é o *objeto context* ou mensagem de que a aplicação lógica envia para a sua função. Ao clicar no **corpo do pedido** campo, é apresentada uma lista de conteúdo dinâmico pelo que pode selecionar tokens para saídas dos passos anteriores. Este exemplo Especifica que a carga de contexto contém uma propriedade chamada `content` que tenha o **partir** token do valor do acionador de e-mail:

   ![Exemplo de "Corpo do pedido" - payload de objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, não é converter o objeto de contexto como uma cadeia de caracteres, para que o conteúdo do objeto é adicionado diretamente para o payload JSON. No entanto, quando o objeto de contexto não é um token JSON que transmite uma cadeia de caracteres, um objeto JSON ou uma matriz JSON, receberá um erro. Deste modo, se este exemplo utilizou os **hora de receção** token em vez disso, pode converter o objeto de contexto como uma cadeia de caracteres, adicionando as aspas:  

   ![Converter o objeto como cadeia de caracteres](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. Para especificar outros detalhes, como o método para utilização, cabeçalhos de pedido ou parâmetros de consulta, escolha **Mostrar opções avançadas**.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Adicionar funções existentes para aplicações lógicas

Para chamar as funções do Azure existentes a partir de aplicações lógicas, pode adicionar as funções do Azure, como qualquer outra ação no Estruturador da aplicação lógica. 

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra a aplicação lógica no Estruturador da aplicação lógica. 

2. No passo em que pretende adicionar a função, escolha **novo passo** > **adicionar uma ação**. 

3. Na caixa de pesquisa, introduza "azure functions" como o filtro.
Na lista de ações, selecione a ação: **Escolha uma função do Azure - as funções do Azure** 

   ![Localizar "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Na lista de aplicações de função, selecione a sua aplicação de função. Depois de aparece na lista de funções, selecione a sua função. 

   ![Selecione a aplicação de funções e a função do Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Para as funções que têm definições de API (Swagger descrições) e são [para que a sua aplicação lógica pode encontrar e aceder essas funções](#function-swagger), pode selecionar **ações de Swagger**:

   ![Selecione a aplicação de funções, "Swagger ações" "e sua função do Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. Na **corpo do pedido** caixa, forneça a sua função entrada do, que tem de ser formatado como um objeto JavaScript Object Notation (JSON). 

   Esta entrada é o *objeto context* ou mensagem de que a aplicação lógica envia para a sua função. Ao clicar no **corpo do pedido** campo, é apresentada uma lista de conteúdo dinâmico pelo que pode selecionar tokens para saídas dos passos anteriores. Este exemplo Especifica que a carga de contexto contém uma propriedade chamada `content` que tenha o **partir** token do valor do acionador de e-mail:

   ![Exemplo de "Corpo do pedido" - payload de objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, não é converter o objeto de contexto como uma cadeia de caracteres, para que o conteúdo do objeto é adicionado diretamente para o payload JSON. No entanto, quando o objeto de contexto não é um token JSON que transmite uma cadeia de caracteres, um objeto JSON ou uma matriz JSON, receberá um erro. Deste modo, se este exemplo utilizou os **hora de receção** token em vez disso, pode converter o objeto de contexto como uma cadeia de caracteres, adicionando as aspas: 

   ![Converter o objeto como cadeia de caracteres](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. Para especificar outros detalhes, como o método para utilização, cabeçalhos de pedido ou parâmetros de consulta, escolha **Mostrar opções avançadas**.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Chamar aplicações lógicas a partir de funções

Quando deseja acionar uma aplicação lógica a partir de dentro de uma função do Azure, tem de iniciar a aplicação lógica com um acionador que fornece um ponto final que pode ser chamado. Por exemplo, pode iniciar a aplicação lógica com o **HTTP**, **pedir**, **filas do Azure**, ou **Event Grid** acionador. Dentro da sua função, enviar um pedido HTTP POST para o URL do acionador e incluem o payload que desejar essa aplicação lógica para processar. Para obter mais informações, consulte [chamar, acionar, ou aninhar aplicações lógicas](../logic-apps/logic-apps-http-endpoint.md). 

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [conectores do Logic Apps](../connectors/apis-list.md)
