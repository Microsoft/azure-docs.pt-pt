---
title: Adicione e ligue para as funções azure de aplicações lógicas azure
description: Ligue e execute código personalizado nas suas Funções Azure a partir de tarefas automatizadas e fluxos de trabalho em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.openlocfilehash: 68975f21ab810398da969384db4d3bddd22f1bd9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358945"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Ligue para as funções do Azure Logic Apps

Quando pretende executar um código que executa um trabalho específico nas suas aplicações lógicas, pode criar a sua própria função utilizando [funções Azure](../azure-functions/functions-overview.md). Este serviço ajuda-o a criar C#node.js, e F# funções para que não tenha de construir uma app completa ou infraestrutura para executar código. Também pode [ligar para aplicações lógicas de dentro das funções do Azure.](#call-logic-app) As Funções Azure fornecem computação sem servidor estonteante na nuvem e são úteis para executar tarefas como estes exemplos:

* Estenda o comportamento da sua aplicação lógica com C#funções no Node.js ou .
* Execute cálculos no fluxo de trabalho da sua aplicação lógica.
* Aplique campos avançados de formatação ou computação nas suas aplicações lógicas.

Para executar snippets de código sem criar funções Azure, aprenda a [adicionar e executar código inline](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> A integração entre apps lógicas e funções azure atualmente não funciona com slots habilitados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma aplicação de função Azure, que é um recipiente para funções Azure, juntamente com a sua função Azure. Se não tiver uma aplicação de função, crie primeiro a [sua aplicação de funções](../azure-functions/functions-create-first-azure-function.md). Em seguida, pode criar a sua função fora da sua aplicação lógica no portal Azure, ou [de dentro da sua aplicação lógica](#create-function-designer) no Logic App Designer.

* Ao trabalhar com aplicações lógicas, os mesmos requisitos aplicam-se às aplicações e funções de função, quer sejam existentes ou novas:

  * A sua aplicação de funções e a sua aplicação lógica devem utilizar a mesma subscrição do Azure.

  * As novas aplicações de função devem usar o .NET ou o JavaScript como a stack de tempo de execução. Quando adicionar uma nova função às aplicações C# de função existentes, pode selecionar ou JavaScript.

  * A sua função utiliza o modelo de **gatilho HTTP.**

    O modelo de gatilho HTTP pode aceitar conteúdo que `application/json` tipo a partir da sua aplicação lógica. Ao adicionar uma função Azure à sua aplicação lógica, o Logic App Designer mostra funções personalizadas que são criadas a partir deste modelo dentro da sua subscrição Azure.

  * A sua função não utiliza rotas personalizadas a menos que tenha definido uma [definição OpenAPI](../azure-functions/functions-openapi-definition.md) (anteriormente conhecida como [ficheiro Swagger).](https://swagger.io/)

  * Se tiver uma definição OpenAPI para a sua função, o Logic Apps Designer dá-lhe uma experiência mais rica quando o seu trabalho com parâmetros de função. Antes que a sua aplicação lógica possa encontrar e aceder a funções que tenham definições OpenAPI, [configurar a sua aplicação de funções seguindo estes passos](#function-swagger).

* A aplicação lógica onde pretende adicionar a função, incluindo um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como o primeiro passo na sua aplicação lógica

  Antes de poder adicionar ações que executam funções, a sua aplicação lógica deve começar com um gatilho. Se você é novo em aplicações lógicas, reveja [o que são As Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Encontre funções que tenham descrições openAPI

Para uma experiência mais rica quando trabalha com parâmetros de função no Logic Apps Designer, [gere uma definição OpenAPI](../azure-functions/functions-openapi-definition.md), anteriormente conhecida como [ficheiro Swagger](https://swagger.io/), para a sua função. Para configurar a sua aplicação de função para que a sua aplicação lógica possa encontrar e utilizar funções que tenham descrições de Swagger, siga estes passos:

1. Certifique-se de que a sua aplicação de funções está a funcionar ativamente.

1. Na sua aplicação de funções, instale a [Partilha de Recursos De Origem Cruzada (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para que todas as origens sejam permitidas seguindo estes passos:

   1. Na lista de Aplicações de **Funções,** selecione a sua aplicação de funções. No painel da direita, selecione **funcionalidades da Plataforma** > **CORS**.

      ![Selecione a sua app de funções > "Funcionalidades da Plataforma" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. Em **CORS,** adicione o caracteres wildcard asterisco ( **`*`),** mas remova todas as outras origens da lista e selecione **Guardar**.

      ![Definir "CORS* para o personagem wildcard "*"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Aceder a valores de propriedade dentro dos pedidos do HTTP

As funções webhook podem aceitar pedidos HTTP como inputs e passar esses pedidos para outras funções. Por exemplo, embora as Aplicações Lógicas tenham [funções que convertam valores DateTime,](../logic-apps/workflow-definition-language-functions-reference.md)esta função de javaScript de amostra básica mostra como pode aceder a uma propriedade dentro de um objeto de pedido que passou para a função e executar operações nesse valor de propriedade. Para aceder a propriedades dentro de objetos, este exemplo utiliza o operador do [ponto (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Eis o que acontece dentro desta função:

1. A função cria uma variável `data` e atribui o objeto `body` dentro do `request` objeto a essa variável. A função utiliza o operador do ponto (.) para fazer referência ao `body` objeto no interior do objeto `request`:

   ```javascript
   var data = request.body;
   ```

1. A função pode agora aceder à propriedade `date` através da variável `data`, e converter esse valor de propriedade do tipo DateTime para o tipo DateString, chamando a função `ToDateString()`. A função também devolve o resultado através da propriedade `body` na resposta da função:

   ```javascript
   body: data.date.ToDateString();
   ```

Agora que criou a sua função Azure, siga os passos para [adicionar funções a aplicações lógicas](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Criar funções dentro de aplicações lógicas

Antes de poder criar uma função Azure a partir de dentro da sua aplicação lógica utilizando o Logic App Designer, tem primeiro de ter uma aplicação de função Azure, que é um recipiente para as suas funções. Se não tiver uma aplicação de função, crie primeiro essa aplicação de função. Consulte [Criar a sua primeira função no portal Azure](../azure-functions/functions-create-first-azure-function.md).

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Para criar e adicionar a sua função, siga o passo que se aplica ao seu cenário:

   * Sob o último passo do fluxo de trabalho da sua aplicação lógica, selecione **Novo passo**.

   * Entre os passos existentes no fluxo de trabalho da sua aplicação lógica, mova o rato sobre a seta, selecione o sinal mais (+) e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, introduza "funções azuis" como filtro. Na lista de ações, selecione a ação **de função Do Azure,** por exemplo:

   ![Encontre "funções Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. A partir da lista de aplicações de funções, selecione a sua aplicação de funções. Após a abertura da lista de ações, selecione esta ação: **Criar nova função**

   ![Selecione a sua aplicação de funções](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. No editor de definição de função, defina a sua função:

   1. Na caixa de **nomes Função,** forneça um nome para a sua função.

   1. Na caixa **Código,** adicione o seu código ao modelo de função, incluindo a resposta e a carga útil que pretende devolver à sua aplicação lógica após a sua função terminar a funcionar. Quando terminar, selecione **Criar**.

   Por exemplo:

   ![Defina a sua função](./media/logic-apps-azure-functions/add-code-function-definition.png)

   No código do modelo, o *`context` objeto* refere-se à mensagem que a sua aplicação lógica envia através do campo **Request Body** num passo posterior. Para aceder às propriedades do objeto `context` a partir do interior da sua função, utilize esta sintaxe:

   `context.body.<property-name>`

   Por exemplo, para fazer referência à propriedade `content` dentro do objeto `context`, utilize esta sintaxe:

   `context.body.content`

   O código de modelo também inclui uma variável `input`, que armazena o valor do parâmetro `data` para que a sua função possa realizar operações com esse valor. Dentro das funções JavaScript, a variável `data` também é um atalho para `context.body`.

   > [!NOTE]
   > A propriedade `body` aqui aplica-se ao objeto `context` e não é o mesmo que o **símbolo do Corpo** a partir da saída de uma ação, que você também pode passar para a sua função.

1. Na caixa **Request Body,** forneça a entrada da sua função, que deve ser formatada como um objeto de notação de objetos JavaScript (JSON).

   Esta entrada é o objeto de *contexto* ou mensagem que a sua aplicação lógica envia para a sua função. Quando clica no campo **Request Body,** a lista de conteúdos dinâmicos aparece para que possa selecionar tokens para saídas a partir de passos anteriores. Este exemplo especifica que a carga útil do contexto contém uma propriedade chamada `content` que tem o valor do **Token** a partir do gatilho de e-mail.

   ![Exemplo "Request Body" - carga útil de objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, o objeto de contexto não é lançado como uma corda, por isso o conteúdo do objeto é adicionado diretamente à carga útil jSON. No entanto, quando o objeto de contexto não é um símbolo jSON que passa por uma corda, um objeto JSON, ou uma matriz JSON, você tem um erro. Assim, se este exemplo usou o símbolo **do Tempo Recebido,** pode lançar o objeto de contexto como uma corda adicionando marcas de dupla citação.

   ![Objeto fundido como corda](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Para especificar outros detalhes, como o método de utilização, os cabeçalhos de pedido ou os parâmetros de consulta, ou a autenticação, abra a **lista de novos parâmetros E** selecione as opções que deseja. Para autenticação, as suas opções diferem com base na sua função selecionada. Ver [Autenticação ativa para funções Azure](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Adicione as funções existentes a aplicações lógicas

Para chamar as funções azure existentes a partir das suas aplicações lógicas, pode adicionar funções azure como qualquer outra ação no Logic App Designer.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Sob o degrau onde pretende adicionar a função, selecione **Novo passo**.

1. Em **'Escolha uma ação**' na caixa de pesquisa, introduza "funções azure" como filtro. Na lista de ações, selecione a ação **de função Azure.**

   ![Encontre "funções Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. A partir da lista de aplicações de funções, selecione a sua aplicação de funções. Depois da lista de funções aparecer, selecione a sua função.

   ![Selecione a sua aplicação de função e função Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Para funções que tenham definições de API (descrições de Swagger) e são configuradas para que [a sua aplicação lógica possa encontrar e aceder a essas funções,](#function-swagger)pode selecionar **ações de Swagger**.

   ![Selecione a sua aplicação de função, "Ações Swagger", e a sua função Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Na caixa **Request Body,** forneça a entrada da sua função, que deve ser formatada como um objeto de notação de objetos JavaScript (JSON).

   Esta entrada é o objeto de *contexto* ou mensagem que a sua aplicação lógica envia para a sua função. Quando clica no campo **Request Body,** aparece a lista de conteúdos dinâmicos para que possa selecionar fichas para saídas a partir de passos anteriores. Este exemplo especifica que a carga útil do contexto contém uma propriedade chamada `content` que tem o valor do **Token** a partir do gatilho de e-mail.

   ![Exemplo "Request Body" - carga útil de objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, o objeto de contexto não é lançado como uma corda, por isso o conteúdo do objeto é adicionado diretamente à carga útil jSON. No entanto, quando o objeto de contexto não é um símbolo jSON que passa por uma corda, um objeto JSON, ou uma matriz JSON, você tem um erro. Assim, se este exemplo usou o símbolo **do Tempo Recebido,** pode lançar o objeto de contexto como uma corda adicionando marcas de dupla citação:

   ![Objeto fundido como corda](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Para especificar outros detalhes, como o método de utilização, os cabeçalhos de pedido, os parâmetros de consulta ou a autenticação, abra a **lista de novos parâmetros E** selecione as opções que deseja. Para autenticação, as suas opções diferem com base na sua função selecionada. Ver [Autenticação ativa nas funções Azure](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Chamadas de aplicativos lógicos de funções Azure

Quando pretende desencadear uma aplicação lógica a partir de dentro de uma função Azure, a aplicação lógica deve começar com um gatilho que fornece um ponto final calivel. Por exemplo, pode iniciar a aplicação lógica com o **HTTP,** **Request,** **Azure Queues**ou Gatilho da Grelha de **Eventos.** Dentro da sua função, envie um pedido HTTP POST para o URL do gatilho e inclua a carga útil que pretende que essa aplicação lógica seja processada. Para mais informações, consulte [call, trigger ou nest logic apps](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Ativar a autenticação para funções do Azure

Para autenticar o acesso a recursos em outros inquilinos do Azure Ative Directory (Azure AD) sem ter de assinar e fornecer credenciais ou segredos, a sua aplicação lógica pode usar uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) (anteriormente conhecida como Identidade de Serviço Gerida ou MSI). O Azure gere esta identidade para si e ajuda a garantir as suas credenciais porque não tem de fornecer ou rodar segredos. Saiba mais sobre [os serviços Azure que suportam identidades geridas para a autenticação da AD Azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

Se configurar a sua aplicação lógica para utilizar a identidade atribuída ao sistema ou uma identidade atribuída manualmente ao utilizador, as funções Azure na sua aplicação lógica também podem usar essa mesma identidade para autenticação. Para obter mais informações sobre o suporte à autenticação para funções do Azure em aplicações lógicas, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Para configurar e utilizar a identidade gerida com a sua função, siga estes passos:

1. Ative a identidade gerida na sua aplicação lógica e configura o acesso dessa identidade ao recurso-alvo. Consulte o [acesso authenticado aos recursos do Azure utilizando identidades geridas em Aplicações Lógicas Azure](../logic-apps/create-managed-service-identity.md).

1. Ativar a autenticação na sua função e aplicação de função Azure seguindo estes passos:

   * [Configurar a autenticação anónima na sua função](#set-authentication-function-app)
   * [Configurar a autenticação azure AD na sua aplicação de função](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Configurar a autenticação anónima na sua função

Para utilizar a identidade gerida da sua aplicação lógica na sua função Azure, definiu o nível de autenticação da sua função para anónimo. Caso contrário, a sua aplicação lógica lança um erro "BadRequest".

1. No [portal Azure,](https://portal.azure.com)encontre e selecione a sua aplicação de funções. Estes passos utilizam o "FabrikamFunctionApp" como aplicação de função de exemplo.

1. No painel de aplicações de funções, selecione **funcionalidades da Plataforma**. Sob **as ferramentas de Desenvolvimento,** selecione **ferramentas avançadas (Kudu)** .

   ![Abrir ferramentas avançadas para Kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Na barra de títulos do site kudu, a partir do menu **Debug Console,** selecione **CMD**.

   ![A partir do menu de consola de depuração, selecione a opção "CMD"](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Depois da página seguinte aparecer, a partir da lista de pastas, selecione site > **wwwroot** > *sua função*. Estes passos utilizam "FabrikamAzureFunction" como função de exemplo.

   ![Selecione "site" > "wwwroot" > a sua função](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Abra o ficheiro `function.json` para edição.

   ![Clique em editar para ficheiro "function.json"](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. No objeto `bindings`, verifique se a propriedade `authLevel` existe. Se o imóvel existir, detete o valor do imóvel para `anonymous`. Caso contrário, adicione esse imóvel e detete o valor.

   ![Adicione a propriedade "authLevel" e coloque-se em "anónimo"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Quando terminar, guarde as definições e continue para a secção seguinte.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Configurar a autenticação azure AD para a sua aplicação de função

Antes de iniciar esta tarefa, encontre e coloque estes valores de lado para posterior utilização:

* O ID do objeto que é gerado para a identidade atribuída pelo sistema que representa a sua aplicação lógica

  * Para gerar este ID de objeto, ative a identidade atribuída ao [sistema da sua aplicação lógica.](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)

  * Caso contrário, para encontrar este ID de objeto, abra a sua aplicação lógica no Logic App Designer. No menu de aplicações lógicas, em **Definições,** selecione **Identidade** > **Sistema atribuído**.

* O ID do diretório para o seu inquilino em Azure Ative Directory (Azure AD)

  Para obter a identificação do seu inquilino, pode executar o comando [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) Powershell. Ou, no portal Azure, siga estes passos:

  1. No [portal Azure,](https://portal.azure.com)encontre e selecione a sua aplicação de funções.

  1. Encontre e selecione o seu inquilino Azure AD. Estes passos usam "Fabrikam" como o inquilino exemplo.

  1. No menu do inquilino, em **Manage,** **selecione Propriedades.**

  1. Copie a identificação do seu insípido, por exemplo, e guarde essa identificação para posterior utilização.

     ![Encontre e copie id do diretório do inquilino da Azure AD](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* O ID de recurso para o recurso-alvo a que pretende aceder

  * Para encontrar estes IDs de recursos, reveja os [serviços Azure que suportam a Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

  > [!IMPORTANT]
  > Este ID de recurso deve corresponder exatamente ao valor que o Azure AD espera, incluindo quaisquer cortes de rasto necessários.

  Este ID de recurso também é o mesmo valor que mais tarde utiliza na propriedade **do Público** quando configura a sua ação [de função para utilizar a identidade atribuída ao sistema](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

Agora está pronto para configurar a autenticação Azure AD para a sua aplicação de função.

1. No [portal Azure,](https://portal.azure.com)encontre e selecione a sua aplicação de funções.

1. No painel de aplicações de funções, selecione **funcionalidades da Plataforma**. Em **Rede,** **selecione Autenticação / Autorização**.

   ![Ver definições de autenticação e autorização](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Altere a definição de autenticação do serviço de **aplicações** para **On**. Da Ação para tomar quando o **pedido não for autenticado,** selecione **Iniciar sessão com o Diretório Ativo Azure**. No âmbito dos **Fornecedores de Autenticação,** selecione **Diretório Ativo Azure**.

   ![Ativar a autenticação com a AD Azure](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. No painel **de definições de diretório ativo Azure,** siga estes passos:

   1. Definir **o modo de gestão** para **Avançado**.

   1. Na propriedade id do **cliente,** insira o ID do objeto para a identidade atribuída pelo sistema da sua aplicação lógica.

   1. Na propriedade **Emitente Url,** insira o URL `https://sts.windows.net/` e apreenda o seu ID de diretório do seu inquilino Azure AD.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Na propriedade **Permitida Token Audiences,** insira o ID de recurso para o recurso-alvo a que pretende aceder.

      Este ID de recurso é o mesmo valor que mais tarde utiliza na propriedade **do Público** quando configura a sua ação [de função para utilizar a identidade atribuída ao sistema](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   Neste ponto, a sua versão parece semelhante a este exemplo:

   ![Definições de autenticação do Diretório Ativo Azure](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Quando tiver terminado, selecione **OK**.

1. Volte ao Logic App Designer e siga os [passos para autenticar o acesso com a identidade gerida.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

## <a name="next-steps"></a>Passos seguintes

* Saiba sobre [conectores de Aplicações Lógicas](../connectors/apis-list.md)
