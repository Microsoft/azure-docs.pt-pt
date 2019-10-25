---
title: Adicionar e chamar o Azure Functions de aplicativos lógicos do Azure
description: Adicionar e executar o Azure Functions de aplicativos lógicos
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: article
ms.date: 10/01/2019
ms.reviewer: klam, LADocs
ms.openlocfilehash: 275d02219087d07a058f486c263d1886839dd4cf
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72799898"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Chamar o Azure Functions de aplicativos lógicos do Azure

Quando você deseja executar o código que executa um trabalho específico em seus aplicativos lógicos, você pode criar sua própria função usando [Azure Functions](../azure-functions/functions-overview.md). Esse serviço ajuda a criar o Node. js C#, e F# as funções para que você não precise criar um aplicativo ou uma infraestrutura completa para executar o código. Você também pode [chamar aplicativos lógicos de dentro do Azure Functions](#call-logic-app). O Azure Functions fornece computação sem servidor na nuvem e é útil para executar tarefas como estes exemplos:

* Estenda o comportamento do aplicativo lógico com funções no node. js ou C#no.
* Executar cálculos no fluxo de trabalho do aplicativo lógico.
* Aplicar formatação avançada ou campos de computação em seus aplicativos lógicos.

Para executar trechos de código sem criar o Azure functions, saiba como [Adicionar e executar código embutido](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> A integração entre aplicativos lógicos e Azure Functions atualmente não funciona com slots habilitados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um aplicativo de funções do Azure, que é um contêiner do Azure functions, juntamente com sua função do Azure. Se você não tiver um aplicativo de funções, [crie seu aplicativo de funções primeiro](../azure-functions/functions-create-first-azure-function.md). Em seguida, você pode criar sua função fora do seu aplicativo lógico no portal do Azure ou [de dentro de seu aplicativo lógico](#create-function-designer) no designer de aplicativo lógico.

* Ao trabalhar com aplicativos lógicos, os mesmos requisitos se aplicam a aplicativos de função e funções, sejam eles existentes ou novos:

  * Seu aplicativo de funções e aplicativo lógico devem usar a mesma assinatura do Azure.

  * Novos aplicativos de funções devem usar o .NET ou JavaScript como a pilha de tempo de execução. Quando você adiciona uma nova função a aplicativos de funções existentes, você pode selecionar C# um ou JavaScript.

  * Sua função usa o modelo de **gatilho http** .

    O modelo de gatilho HTTP pode aceitar conteúdo que tem `application/json` tipo de seu aplicativo lógico. Quando você adiciona uma função do Azure ao seu aplicativo lógico, o designer do aplicativo lógico mostra as funções personalizadas que são criadas a partir desse modelo em sua assinatura do Azure.

  * Sua função não usa rotas personalizadas, a menos que você tenha definido uma [definição de openapi](../azure-functions/functions-openapi-definition.md) (anteriormente conhecida como um [arquivo do Swagger](https://swagger.io/)).

  * Se você tiver uma definição de OpenAPI para sua função, o designer de aplicativos lógicos fornecerá uma experiência mais rica quando seu trabalho com parâmetros de função. Antes que seu aplicativo lógico possa localizar e acessar funções que têm definições de OpenAPI, [configure seu aplicativo de funções seguindo estas etapas](#function-swagger).

* O aplicativo lógico no qual você deseja adicionar a função, incluindo um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa em seu aplicativo lógico

  Antes que você possa adicionar ações que executam funções, seu aplicativo lógico deve começar com um gatilho. Se você for novo em aplicativos lógicos, examine [o que é o início rápido e aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) [: Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Localizar funções que têm descrições de OpenAPI

Para uma experiência mais rica quando você trabalha com parâmetros de função no designer de aplicativos lógicos, [gere uma definição de openapi](../azure-functions/functions-openapi-definition.md), anteriormente conhecida como [arquivo do Swagger](https://swagger.io/), para sua função. Para configurar seu aplicativo de funções para que seu aplicativo lógico possa localizar e usar funções que tenham descrições do Swagger, siga estas etapas:

1. Verifique se seu aplicativo de funções está em execução ativamente.

1. Em seu aplicativo de funções, configure o [CORS (compartilhamento de recursos entre origens)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para que todas as origens sejam permitidas seguindo estas etapas:

   1. Na lista de **aplicativos de funções** , selecione seu aplicativo de funções. No painel à direita, selecione recursos da **plataforma** > **CORS**.

      ![Selecione seu aplicativo de funções > "recursos da plataforma" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. Em **CORS**, adicione o caractere curinga asterisco ( **`*`** ), mas remova todas as outras origens na lista e selecione **salvar**.

      ![Defina "CORS * para o caractere curinga" * "](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Acessar valores de propriedade dentro de solicitações HTTP

As funções de webhook podem aceitar solicitações HTTP como entradas e passar essas solicitações para outras funções. Por exemplo, embora os aplicativos lógicos tenham [funções que convertam valores de data e hora](../logic-apps/workflow-definition-language-functions-reference.md), essa função JavaScript de exemplo básica mostra como você pode acessar uma propriedade dentro de um objeto de solicitação que é passado para a função e executar operações nesse valor de propriedade. Para acessar propriedades dentro de objetos, este exemplo usa o [operador de ponto (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors):

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Veja o que acontece dentro dessa função:

1. A função cria uma variável `data` e atribui o objeto `body` dentro do objeto `request` para essa variável. A função usa o operador ponto (.) para fazer referência ao objeto `body` dentro do objeto `request`:

   ```javascript
   var data = request.body;
   ```

1. A função agora pode acessar a propriedade `date` por meio da variável `data` e converter o valor da Propriedade do tipo DateTime para o tipo DateString chamando a função `ToDateString()`. A função também retorna o resultado por meio da propriedade `body` na resposta da função:

   ```javascript
   body: data.date.ToDateString();
   ```

Agora que você criou sua função do Azure, siga as etapas de como [adicionar funções a aplicativos lógicos](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Criar funções dentro de aplicativos lógicos

Antes de criar uma função do Azure a partir de dentro de seu aplicativo lógico usando o designer de aplicativo lógico, você deve primeiro ter um aplicativo de funções do Azure, que é um contêiner para suas funções. Se você não tiver um aplicativo de funções, crie esse aplicativo de funções primeiro. Consulte [criar sua primeira função no portal do Azure](../azure-functions/functions-create-first-azure-function.md).

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer do aplicativo lógico.

1. Para criar e adicionar sua função, siga a etapa que se aplica ao seu cenário:

   * Na última etapa do fluxo de trabalho do aplicativo lógico, selecione **nova etapa**.

   * Entre as etapas existentes no fluxo de trabalho do aplicativo lógico, mova o mouse sobre a seta, selecione o sinal de adição (+) e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira "Azure Functions" como seu filtro. Na lista ações, selecione esta ação: **escolher uma função do Azure**

   ![Localizar "Azure Functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Na lista de aplicativos de funções, selecione seu aplicativo de funções. Depois que a lista de ações for aberta, selecione esta ação: **criar nova função**

   ![Selecione seu aplicativo de funções](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. No editor de definição de função, defina sua função:

   1. Na caixa **nome da função** , forneça um nome para a função.

   1. Na caixa **código** , adicione seu código ao modelo de função, incluindo a resposta e a carga que você deseja que sejam retornados ao seu aplicativo lógico após a conclusão da execução da função.

      ![Definir sua função](./media/logic-apps-azure-functions/function-definition.png)

      No código do modelo, o *objeto`context`* refere-se à mensagem que seu aplicativo lógico envia por meio do campo **corpo da solicitação** em uma etapa posterior. Para acessar as propriedades do objeto de `context` de dentro de sua função, use esta sintaxe:

      `context.body.<property-name>`

      Por exemplo, para fazer referência à propriedade `content` dentro do objeto `context`, use esta sintaxe:

      `context.body.content`

      O código do modelo também inclui uma variável `input`, que armazena o valor do parâmetro `data` para que sua função possa executar operações nesse valor. Dentro das funções JavaScript, a variável `data` também é um atalho para `context.body`.

      > [!NOTE]
      > A propriedade `body` aqui se aplica ao objeto `context` e não é igual ao token do **corpo** da saída de uma ação, que você também pode passar para sua função.

   1. Quando terminar, selecione **Criar**.

1. Na caixa **corpo da solicitação** , forneça a entrada da função, que deve ser formatada como um objeto JavaScript Object Notation (JSON).

   Essa entrada é o *objeto de contexto* ou a mensagem que seu aplicativo lógico envia para sua função. Quando você clica no campo **corpo da solicitação** , a lista de conteúdo dinâmico é exibida para que você possa selecionar tokens para saídas de etapas anteriores. Este exemplo especifica que a carga do contexto contém uma propriedade chamada `content` que tem o valor **do token from do gatilho de email** .

   ![Exemplo de "corpo da solicitação"-conteúdo do objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, o objeto de contexto não é convertido como uma cadeia de caracteres, portanto, o conteúdo do objeto é adicionado diretamente à carga JSON. No entanto, quando o objeto de contexto não é um token JSON que passa uma cadeia de caracteres, um objeto JSON ou uma matriz JSON, você recebe um erro. Portanto, se este exemplo usou o token de **tempo recebido** , você poderá converter o objeto de contexto como uma cadeia de caracteres adicionando aspas duplas.

   ![Converter objeto como cadeia de caracteres](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Para especificar outros detalhes, como o método a ser usado, os cabeçalhos de solicitação ou os parâmetros de consulta, ou a autenticação, abra a lista **Adicionar novo parâmetro** e selecione as opções desejadas. Para autenticação, suas opções são diferentes com base na função selecionada. Consulte [habilitar a autenticação do Azure Functions](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Adicionar funções existentes a aplicativos lógicos

Para chamar as Azure Functions existentes de seus aplicativos lógicos, você pode adicionar Azure Functions como qualquer outra ação no designer de aplicativo lógico.

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer do aplicativo lógico.

1. Na etapa em que você deseja adicionar a função, selecione **nova etapa**.

1. Em **escolher uma ação**, na caixa de pesquisa, insira "Azure Functions" como filtro. Na lista ações, selecione esta ação: **escolher uma função do Azure**

   ![Localizar "Azure Functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Na lista de aplicativos de funções, selecione seu aplicativo de funções. Depois que a lista de funções for exibida, selecione sua função.

   ![Selecione seu aplicativo de funções e função do Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Para funções que têm definições de API (descrições do Swagger) e são [configuradas para que seu aplicativo lógico possa localizar e acessar essas funções](#function-swagger), você pode selecionar **ações do Swagger**.

   ![Selecione seu aplicativo de funções, "ações do Swagger" e sua função do Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Na caixa **corpo da solicitação** , forneça a entrada da função, que deve ser formatada como um objeto JavaScript Object Notation (JSON).

   Essa entrada é o *objeto de contexto* ou a mensagem que seu aplicativo lógico envia para sua função. Quando você clica no campo **corpo da solicitação** , a lista de conteúdo dinâmico é exibida para que você possa selecionar tokens para saídas de etapas anteriores. Este exemplo especifica que a carga do contexto contém uma propriedade chamada `content` que tem o valor **do token from do gatilho de email** .

   ![Exemplo de "corpo da solicitação"-conteúdo do objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, o objeto de contexto não é convertido como uma cadeia de caracteres, portanto, o conteúdo do objeto é adicionado diretamente à carga JSON. No entanto, quando o objeto de contexto não é um token JSON que passa uma cadeia de caracteres, um objeto JSON ou uma matriz JSON, você recebe um erro. Portanto, se este exemplo usou o token de **tempo recebido** , você poderá converter o objeto de contexto como uma cadeia de caracteres adicionando aspas duplas:

   ![Converter objeto como cadeia de caracteres](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Para especificar outros detalhes, como o método a ser usado, os cabeçalhos de solicitação, os parâmetros de consulta ou a autenticação, abra a lista **Adicionar novo parâmetro** e selecione as opções desejadas. Para autenticação, suas opções são diferentes com base na função selecionada. Consulte [habilitar a autenticação no Azure Functions](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Chamar aplicativos lógicos do Azure Functions

Quando você deseja disparar um aplicativo lógico de dentro de uma função do Azure, o aplicativo lógico deve começar com um gatilho que fornece um ponto de extremidade que pode ser chamado. Por exemplo, você pode iniciar o aplicativo lógico com **http**, **solicitação**, **filas do Azure**ou gatilho de **grade de eventos** . Dentro de sua função, envie uma solicitação HTTP POST para a URL do gatilho e inclua a carga que você deseja que o aplicativo lógico processe. Para obter mais informações, consulte [chamar, disparar ou aninhar aplicativos lógicos](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Habilitar a autenticação do Azure Functions

Para autenticar o acesso a recursos em outros locatários do Azure Active Directory (AD do Azure) sem precisar entrar e fornecer credenciais ou segredos, seu aplicativo lógico pode usar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) (anteriormente conhecida como identidade de serviço gerenciada ou msi). O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais porque você não precisa fornecer ou girar segredos. Saiba mais sobre os [Serviços do Azure que dão suporte a identidades gerenciadas para autenticação do Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Se você configurar seu aplicativo lógico para usar a identidade gerenciada atribuída pelo sistema, o Azure Functions em seu aplicativo lógico também poderá usar essa mesma identidade para autenticação. Para obter mais informações sobre o suporte de autenticação do Azure Functions em aplicativos lógicos, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Para configurar e usar a identidade atribuída pelo sistema com sua função, siga estas etapas:

1. Habilite a identidade atribuída pelo sistema em seu aplicativo lógico e configure o acesso dessa identidade ao recurso de destino. Consulte [autenticar o acesso aos recursos do Azure usando identidades gerenciadas em aplicativos lógicos do Azure](../logic-apps/create-managed-service-identity.md).

1. Habilite a autenticação em seu aplicativo de funções e funções do Azure seguindo estas etapas:

   * [Configurar a autenticação anônima em sua função](#set-authentication-function-app)
   * [Configurar a autenticação do Azure AD em seu aplicativo de funções](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Configurar a autenticação anônima em sua função

Para usar a identidade atribuída pelo sistema de seu aplicativo lógico em sua função do Azure, você definiu o nível de autenticação da função como anônimo. Caso contrário, seu aplicativo lógico lançará um erro "BadRequest".

1. Na [portal do Azure](https://portal.azure.com), localize e selecione seu aplicativo de funções. Essas etapas usam "FabrikamFunctionApp" como o aplicativo de funções de exemplo.

1. No painel aplicativo de funções, selecione **recursos de plataforma**. Em **ferramentas de desenvolvimento**, selecione **ferramentas avançadas (kudu)** .

   ![Abrir ferramentas avançadas para kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Na barra de título do site do kudu, no menu do **console de depuração** , selecione **cmd**.

   ![No menu do console de depuração, selecione a opção "CMD"](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Depois que a próxima página for exibida, na lista pasta, selecione **site** > **wwwroot** > *sua função*. Essas etapas usam "FabrikamAzureFunction" como a função de exemplo.

   ![Selecione "site" > "wwwroot" > sua função](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Abra o arquivo `function.json` para edição.

   ![Clique em Editar para o arquivo "function. JSON"](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. No objeto `bindings`, verifique se a propriedade `authLevel` existe. Se a propriedade existir, defina o valor da propriedade como `anonymous`. Caso contrário, adicione essa propriedade e defina o valor.

   ![Adicione a propriedade "authLevel" e defina como "Anonymous"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Quando terminar, salve suas configurações e continue na próxima seção.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Configurar a autenticação do Azure AD para seu aplicativo de funções

Antes de iniciar essa tarefa, localize e coloque esses valores de lado para uso posterior:

* A ID de objeto que é gerada para a identidade atribuída pelo sistema que representa seu aplicativo lógico

  * Para gerar essa ID de objeto, [habilite a identidade atribuída pelo sistema do aplicativo lógico](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app).

  * Caso contrário, para localizar essa ID de objeto, abra seu aplicativo lógico no designer de aplicativo lógico. No menu do aplicativo lógico, em **configurações**, selecione **identidade** > **sistema atribuído**.

* A ID de diretório para seu locatário no Azure Active Directory (Azure AD)

  Para obter a ID do diretório do locatário, você pode executar o comando [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) PowerShell. Ou, no portal do Azure, siga estas etapas:

  1. Na [portal do Azure](https://portal.azure.com), localize e selecione seu aplicativo de funções.

  1. Localize e selecione seu locatário do Azure AD. Essas etapas usam "fabrikam" como o locatário de exemplo.

  1. No menu do locatário, em **gerenciar**, selecione **Propriedades**.

  1. Copie a ID do diretório do locatário, por exemplo, e salve essa ID para uso posterior.

     ![Localizar e copiar a ID de diretório do locatário do Azure AD](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* A ID de recurso para o recurso de destino que você deseja acessar

  * Para encontrar essas IDs de recurso, examine os [Serviços do Azure que dão suporte ao Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Essa ID de recurso deve corresponder exatamente ao valor esperado pelo Azure AD, incluindo as barras à direita necessárias.

  Essa ID de recurso também é o mesmo valor que você usa posteriormente na propriedade **Audience** quando você [configura sua ação de função para usar a identidade atribuída pelo sistema](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

Agora você está pronto para configurar a autenticação do Azure AD para seu aplicativo de funções.

1. Na [portal do Azure](https://portal.azure.com), localize e selecione seu aplicativo de funções.

1. No painel aplicativo de funções, selecione **recursos de plataforma**. Em **rede**, selecione **autenticação/autorização**.

   ![Exibir configurações de autenticação e autorização](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Altere a configuração de **autenticação do serviço de aplicativo** para **ativado**. Na lista **ação a ser tomada quando a solicitação não é autenticada** , selecione **fazer logon com Azure Active Directory**. Em **provedores de autenticação**, selecione **Azure Active Directory**.

   ![Ativar a autenticação com o Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. No painel **configurações de Azure Active Directory** , siga estas etapas:

   1. Defina o **modo de gerenciamento** como **avançado**.

   1. Na propriedade **ID do cliente** , insira a ID de objeto para a identidade atribuída pelo sistema do aplicativo lógico.

   1. Na propriedade **URL do emissor** , insira a url do `https://sts.windows.net/` e acrescente a ID do diretório do seu locatário do Azure AD.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Na propriedade **públicos de tokens permitidos** , insira a ID de recurso para o recurso de destino que você deseja acessar.

      Essa ID de recurso é o mesmo valor que você usa posteriormente na propriedade **Audience** ao [configurar sua ação de função para usar a identidade atribuída pelo sistema](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   Neste ponto, sua versão é semelhante a este exemplo:

   ![Azure Active Directory configurações de autenticação](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Quando tiver terminado, selecione **OK**.

1. Retorne ao designer de aplicativo lógico e siga as [etapas para autenticar o acesso com a identidade gerenciada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [conectores de aplicativos lógicos](../connectors/apis-list.md)
