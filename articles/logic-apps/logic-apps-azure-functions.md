---
title: Adicione e ligue para as funções Azure de Azure Logic Apps
description: Ligue e execute código personalizado nas suas Funções Azure a partir de tarefas automatizadas e fluxos de trabalho em Apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.custom: devx-track-js
ms.openlocfilehash: 75693c57a8d120aad53a15d03ae4054bac8262af
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023062"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Chamar as funções do Azure a partir do Azure Logic Apps

Quando pretende executar um código que executa um trabalho específico nas suas aplicações lógicas, pode criar a sua própria função utilizando [funções Azure](../azure-functions/functions-overview.md). Este serviço ajuda-o a criar as funções Node.js, C# e F# para que não tenha de criar uma aplicação ou infraestrutura completa para executar código. Também pode [chamar aplicativos lógicos de funções Azure](#call-logic-app). As Funções Azure fornecem computação sem servidor na nuvem e é útil para executar tarefas como estes exemplos:

* Estenda o comportamento da sua aplicação lógica com funções em Node.js ou C#.
* Execute cálculos no seu fluxo de trabalho de aplicações lógicas.
* Aplicar campos de formatação ou computação avançados nas suas aplicações lógicas.

Para executar os cortes de código sem criar funções Azure, aprenda a [adicionar e executar código inline](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> A integração entre apps lógicas e funções Azure atualmente não funciona com Slots ativados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma aplicação de função Azure, que é um recipiente para funções Azure, juntamente com a sua função Azure. Se não tiver uma aplicação de função, [crie primeiro a sua aplicação de função](../azure-functions/functions-create-first-azure-function.md). Em seguida, pode criar a sua função fora da sua aplicação lógica no portal Azure, ou [de dentro da sua aplicação lógica](#create-function-designer) no Logic App Designer.

* Ao trabalhar com aplicações lógicas, os mesmos requisitos aplicam-se às aplicações e funções de função, quer sejam existentes ou novas:

  * A sua aplicação de função e aplicação lógica devem utilizar a mesma subscrição Azure.

  * As novas aplicações de função devem utilizar o .NET ou o JavaScript como a pilha de tempo de execução. Quando adicionar uma nova função às aplicações de função existentes, pode selecionar C# ou JavaScript.

  * A sua função utiliza o modelo **de gatilho HTTP.**

    O modelo de gatilho HTTP pode aceitar conteúdo que tenha `application/json` tipo a partir da sua aplicação lógica. Quando adiciona uma função Azure à sua aplicação lógica, o Logic App Designer mostra funções personalizadas que são criadas a partir deste modelo dentro da sua subscrição Azure.

  * A sua função não utiliza rotas personalizadas a menos que tenha definido uma [definição OpenAPI](../azure-functions/functions-openapi-definition.md) (anteriormente conhecida como [ficheiro Swagger).](https://swagger.io/)

  * Se tiver uma definição OpenAPI para a sua função, o Logic Apps Designer dá-lhe uma experiência mais rica quando o seu trabalho com parâmetros de função. Antes que a sua aplicação lógica possa encontrar e aceder a funções que tenham definições OpenAPI, [crie a sua aplicação de função seguindo estes passos.](#function-swagger)

* A aplicação lógica onde pretende adicionar a função, incluindo um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como o primeiro passo na sua aplicação lógica

  Antes de poder adicionar ações que executam funções, a sua aplicação lógica deve começar com um gatilho. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Encontre funções que tenham descrições do OpenAPI

Para uma experiência mais rica quando trabalhar com parâmetros de função no Logic Apps Designer, [gere uma definição OpenAPI](../azure-functions/functions-openapi-definition.md), anteriormente conhecida como [ficheiro Swagger,](https://swagger.io/)para a sua função. Para configurar a sua aplicação de função para que a sua aplicação lógica possa encontrar e usar funções que tenham descrições de Swagger, siga estes passos:

1. Certifique-se de que a sua aplicação de função está a funcionar ativamente.

1. Na sua aplicação de função, crie [a Partilha de Recursos de Origem Cruzada (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para que todas as origens sejam permitidas seguindo estes passos:

   1. A partir da lista **de Aplicações de Função,** selecione a sua aplicação de função. No painel direito, selecione **Plataforma com**  >  **CORS**.

      ![Selecione a sua aplicação de função > "Funcionalidades da plataforma" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. Em **CORS**, adicione o asterisco ( **`*`** ) caracter wildcard, mas remova todas as outras origens da lista, e selecione **Save**.

      ![Desaveja "CORS* ao personagem wildcard "*"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Aceda aos valores de propriedade dentro dos pedidos HTTP

As funções webhook podem aceitar pedidos HTTP como entradas e passar esses pedidos para outras funções. Por exemplo, embora as Aplicações Lógicas tenham [funções que convertam valores de DateTime,](../logic-apps/workflow-definition-language-functions-reference.md)esta função de amostra básica JavaScript mostra como pode aceder a uma propriedade dentro de um objeto de pedido que é passado para a função e executar operações nesse valor de propriedade. Para aceder a propriedades dentro de objetos, este exemplo utiliza o [operador ponto (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Eis o que acontece dentro desta função:

1. A função cria uma `data` variável e atribui o `body` objeto dentro do objeto a essa `request` variável. A função utiliza o operador ponto (.) para fazer referência ao `body` objeto no interior do `request` objeto:

   ```javascript
   var data = request.body;
   ```

1. A função pode agora aceder à `date` propriedade através da `data` variável, e converter esse valor de propriedade do tipo DateTime para o tipo DateString, chamando a `ToDateString()` função. A função também devolve o resultado através da `body` propriedade na resposta da função:

   ```javascript
   body: data.date.ToDateString();
   ```

Agora que criou a sua função Azure, siga os passos para [adicionar funções a aplicações lógicas.](#add-function-logic-app)

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Criar funções dentro de apps lógicas

Pode criar funções Azure diretamente do fluxo de trabalho da sua aplicação lógica utilizando a ação de Azure Functions incorporada no Logic App Designer, mas só pode utilizar este método para funções Azure escritas em JavaScript. Para outras línguas, pode criar funções Azure através da experiência Azure Functions no portal Azure. Para obter mais informações, consulte [Criar a sua primeira função no portal Azure](../azure-functions/functions-create-first-azure-function.md).

No entanto, antes de poder criar qualquer função Azure, já deve ter uma aplicação de função Azure, que é um recipiente para as suas funções. Se não tiver uma aplicação de função, crie primeiro essa aplicação de função. Ver [Criar a sua primeira função no portal Azure](../azure-functions/functions-create-first-azure-function.md).

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Para criar e adicionar a sua função, siga o passo que se aplica ao seu cenário:

   * Sob o último passo no fluxo de trabalho da sua aplicação lógica, selecione **Novo passo**.

   * Entre os passos existentes no fluxo de trabalho da sua aplicação lógica, mova o rato sobre a seta, selecione o sinal mais (+) e, em seguida, **selecione Adicione uma ação**.

1. Na caixa de pesquisa, introduza "funções azuis" como filtro. Na lista de ações, selecione a ação **de função Escolha um Azure,** por exemplo:

   ![Encontre "Funções Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. A partir da lista de aplicações de funções, selecione a sua aplicação de função. Após a abertura da lista de ações, selecione esta ação: **Criar nova função**

   ![Selecione a sua aplicação de função](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. No editor de definição de funções, defina a sua função:

   1. Na **caixa de nomes funções,** forneça um nome para a sua função.

   1. Na caixa **Código,** adicione o seu código ao modelo de função, incluindo a resposta e a carga útil que deseja devolvida à sua aplicação lógica após o final da sua função. Quando concluir, selecione **Criar**.

   Por exemplo:

   ![Defina a sua função](./media/logic-apps-azure-functions/add-code-function-definition.png)

   No código do modelo, o *`context` objeto* refere-se à mensagem que a sua aplicação lógica envia através do campo **'Corpo pedido'** num passo posterior. Para aceder às propriedades do objeto a `context` partir de dentro da sua função, utilize esta sintaxe:

   `context.body.<property-name>`

   Por exemplo, para fazer referência à `content` propriedade dentro do `context` objeto, utilize esta sintaxe:

   `context.body.content`

   O código de modelo também inclui uma `input` variável, que armazena o valor a partir do parâmetro para que a `data` sua função possa executar operações nesse valor. Dentro das funções JavaScript, a `data` variável também é um atalho para `context.body` .

   > [!NOTE]
   > A `body` propriedade aqui se aplica ao objeto e não é o mesmo que o símbolo do `context` **Corpo** a partir da saída de uma ação, que você também pode passar para a sua função.

1. Na caixa **'Corpo pedido',** forneça a entrada da sua função, que deve ser formatada como um objeto de Notação de Objetos JavaScript (JSON).

   Esta entrada é o objeto de *contexto* ou mensagem que a sua aplicação lógica envia para a sua função. Quando clica no campo **'Escolha',** aparece a lista de conteúdos dinâmicos para que possa selecionar fichas para saídas a partir de etapas anteriores. Este exemplo especifica que a carga útil de contexto contém uma propriedade nomeada `content` que tem o valor do **Token** a partir do gatilho do e-mail.

   ![Exemplo "Request Body" - carga útil de objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, o objeto de contexto não é lançado como uma corda, por isso o conteúdo do objeto é adicionado diretamente à carga útil do JSON. No entanto, quando o objeto de contexto não é um símbolo JSON que passa uma corda, um objeto JSON ou uma matriz JSON, obtém-se um erro. Assim, se este exemplo usou o token **do Tempo Recebido** em vez disso, você pode lançar o objeto de contexto como uma corda adicionando marcas de aspas duplas.

   ![Objeto fundido como corda](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Para especificar outros detalhes, como o método a utilizar, solicitar cabeçalhos ou parâmetros de consulta, ou autenticação, abra a nova lista **de parâmetros Add** e selecione as opções que pretende. Para a autenticação, as suas opções diferem em função da sua função selecionada. Ver [Ativar a autenticação para as funções Azure](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Adicionar funções existentes a aplicações lógicas

Para chamar as funções Azure existentes a partir das suas aplicações lógicas, pode adicionar funções Azure como qualquer outra ação no Logic App Designer.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Sob o passo onde pretende adicionar a função, selecione **Novo passo**.

1. Em **Escolha uma ação,** na caixa de pesquisa, introduza "funções azuis" como filtro. Na lista de ações, selecione a ação **de função Escolha um Azure.**

   ![Encontre "Funções Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. A partir da lista de aplicações de funções, selecione a sua aplicação de função. Depois de aparecer a lista de funções, selecione a sua função.

   ![Selecione a sua app de função e função Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Para funções que tenham definições de API (descrições de Swagger) e sejam configuradas para que [a sua aplicação lógica possa encontrar e aceder a essas funções,](#function-swagger)pode selecionar **ações da Swagger**.

   ![Selecione a sua aplicação de função, "Ações Swagger", e a sua função Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Na caixa **'Corpo pedido',** forneça a entrada da sua função, que deve ser formatada como um objeto de Notação de Objetos JavaScript (JSON).

   Esta entrada é o objeto de *contexto* ou mensagem que a sua aplicação lógica envia para a sua função. Quando clica no campo **'Escolha',** aparece a lista de conteúdos dinâmicos para que possa selecionar fichas para saídas a partir de etapas anteriores. Este exemplo especifica que a carga útil de contexto contém uma propriedade nomeada `content` que tem o valor do **Token** a partir do gatilho do e-mail.

   ![Exemplo "Request Body" - carga útil de objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, o objeto de contexto não é lançado como uma corda, por isso o conteúdo do objeto é adicionado diretamente à carga útil do JSON. No entanto, quando o objeto de contexto não é um símbolo JSON que passa uma corda, um objeto JSON ou uma matriz JSON, obtém-se um erro. Assim, se este exemplo usou o token **do Tempo Recebido,** pode lançar o objeto de contexto como uma corda adicionando marcas de aspas duplas:

   ![Objeto fundido como corda](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Para especificar outros detalhes, como o método a utilizar, solicite cabeçalhos, parâmetros de consulta ou autenticação, abra a nova lista **de parâmetros Add** e selecione as opções que pretende. Para a autenticação, as suas opções diferem em função da sua função selecionada. Ver [Ativar a autenticação nas funções Azure](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Chame aplicativos de lógica de funções Azure

Quando pretende acionar uma aplicação lógica a partir de uma função Azure, a aplicação lógica deve começar com um gatilho que forneça um ponto final chamado. Por exemplo, pode iniciar a aplicação lógica com o **http**, **Request**, **Azure Queues** ou **Detonador de Grelha de Eventos.** Dentro da sua função, envie um pedido HTTP POST para o URL do gatilho e inclua a carga útil que pretende que a aplicação lógica processe. Para obter mais informações, consulte [aplicações lógicas Call, Trigger ou Nest.](../logic-apps/logic-apps-http-endpoint.md)

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Ativar a autenticação para funções Azure

Para autenticar facilmente o acesso a outros recursos protegidos pelo Azure Ative Directory (Azure AD) sem ter de iniciar seduções e fornecer credenciais ou segredos, a sua aplicação lógica pode utilizar uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) (anteriormente conhecida como Identidade de Serviço Gerido ou MSI). O Azure gere esta identidade automaticamente e ajuda a proteger as credenciais, uma vez que não precisa de introduzir segredos nem proceder a sua rotação. Saiba mais sobre [os serviços Azure que suportam identidades geridas para a autenticação AD Azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

Se configurar a sua aplicação lógica para utilizar a identidade atribuída ao sistema ou uma identidade atribuída manualmente ao utilizador, as funções Azure na sua aplicação lógica também podem usar essa mesma identidade para autenticação. Para obter mais informações sobre o suporte à autenticação das funções Azure em aplicações lógicas, consulte [Adicionar autenticação a chamadas de saída.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

Para configurar e utilizar a identidade gerida com a sua função, siga estes passos:

1. Ativar a identidade gerida na sua aplicação lógica e configurar o acesso dessa identidade ao recurso-alvo. Consulte [autenticar o acesso aos recursos do Azure utilizando identidades geridas em Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

1. Ativar a autenticação na sua aplicação de função e função Azure seguindo estes passos:

   * [Configurar a autenticação anónima na sua função](#set-authentication-function-app)
   * [Configurar a autenticação AZure AD na sua app de função](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Configurar a autenticação anónima na sua função

Para utilizar a identidade gerida da sua aplicação lógica na sua função Azure, definiu o nível de autenticação da sua função para anónimo. Caso contrário, a sua aplicação lógica lança um erro "BadRequest".

1. No [portal Azure,](https://portal.azure.com)encontre e selecione a sua aplicação de função. Estes passos utilizam o "FabrikamFunctionApp" como aplicação de função de exemplo.

1. No painel de aplicações de função, selecione **funcionalidades da Plataforma**. Sob **ferramentas de desenvolvimento,** selecione **Ferramentas Avançadas (Kudu)**.

   ![Abra ferramentas avançadas para Kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Na barra de título do site da Kudu, a partir do menu **Consola Debug,** selecione **CMD**.

   ![A partir do menu de consola de depurado, selecione a opção "CMD"](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Depois da página seguinte aparecer, a partir da lista de pastas, selecione **o site**  >  **wwwroot**  >  *a sua função*. Estes passos usam "FabrikamAzureFunction" como função de exemplo.

   ![Selecione "site" > "wwwroot" > a sua função](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Abra o `function.json` ficheiro para edição.

   ![Clique em editar para o ficheiro "function.json"](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. No `bindings` objeto, verifique se a `authLevel` propriedade existe. Se o imóvel existir, desa um valor de propriedade para `anonymous` . Caso contrário, adicione essa propriedade e desateia o valor.

   ![Adicione propriedade "authLevel" e definido para "anónimo"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Quando terminar, guarde as suas definições e continue para a secção seguinte.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Configurar a autenticação AZure AD para a sua aplicação de função

Antes de iniciar esta tarefa, encontre e coloque estes valores de lado para posterior utilização:

* O ID do objeto que é gerado para a identidade atribuída ao sistema que representa a sua aplicação lógica

  * Para gerar este ID de objeto, [ative a identidade atribuída ao sistema da sua aplicação lógica.](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)

  * Caso contrário, para encontrar este ID de objeto, abra a sua aplicação lógica no Logic App Designer. No menu de aplicativos logico, em **Definições,** selecione **Identity**  >  **Identity System atribuído**.

* O diretório para o seu inquilino em Azure Ative Directory (Azure AD)

  Para obter a identificação do diretório do seu inquilino, pode dirigir o [`Get-AzureAccount`](/powershell/module/servicemanagement/azure.service/get-azureaccount) comando de Powershell. Ou, no portal Azure, siga estes passos:

  1. No [portal Azure,](https://portal.azure.com)encontre e selecione a sua aplicação de função.

  1. Encontre e selecione o seu inquilino Azure AD. Estes passos usam "Fabrikam" como o inquilino exemplo.

  1. No menu do arrendatário, em **Manage,** select **Properties**.

  1. Copie o iD do diretório do seu inquilino, por exemplo, e guarde a identificação para uso posterior.

     ![Encontre e copie o ID do diretório do inquilino da Azure AD](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* O ID de recursos para o recurso-alvo a que pretende aceder

  * Para encontrar estes IDs de recursos, reveja os [serviços Azure que suportam a Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

  > [!IMPORTANT]
  > Este ID de recurso deve corresponder exatamente ao valor que a Azure AD espera, incluindo quaisquer cortes de rasto necessários.

  Este ID de recurso é também o mesmo valor que mais tarde utiliza na propriedade **do Audience** quando configura a sua ação [de função para utilizar a identidade atribuída ao sistema.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

Agora está pronto para configurar a autenticação Azure AD para a sua aplicação de função.

1. No [portal Azure,](https://portal.azure.com)encontre e selecione a sua aplicação de função.

1. No painel de aplicações de função, selecione **funcionalidades da Plataforma**. Em **Rede,** **selecione Autenticação/Autorização**.

   ![Ver definições de autenticação e autorização](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Alterar a **definição de autenticação do serviço de aplicações** para **On**. A partir da Ação a tomar quando o **pedido não for autenticado,** selecione **Iniciar sessão com o Azure Ative Directory**. No âmbito **dos Fornecedores de Autenticação**, selecione **Azure Ative Directory**.

   ![Ligue a autenticação com Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. No painel **de Definições de Diretório Ativo Azure,** siga estes passos:

   1. Definir **o modo de Gestão** para **Avançado**.

   1. Na propriedade **de ID** do Cliente, introduza o ID do objeto para a identidade atribuída ao sistema da sua aplicação lógica.

   1. Na propriedade **Emitente Url,** insira o `https://sts.windows.net/` URL e adira o iD do seu inquilino Azure.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Na propriedade **Allowed Token Audiences,** insira o ID de recurso para o recurso-alvo a que pretende aceder.

      Este ID de recurso é o mesmo valor que mais tarde utiliza na propriedade **do Audience** quando configura a sua ação [de função para utilizar a identidade atribuída ao sistema.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

   Neste momento, a sua versão parece semelhante a este exemplo:

   ![Definições de autenticação do Diretório Ativo Azure](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Quando tiver terminado, selecione **OK**.

1. Volte ao Designer de Aplicações Lógica e siga os [passos para autenticar o acesso com a identidade gerida.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais [sobre os conectores de Aplicações Lógicas](../connectors/apis-list.md)
