---
title: Chamar, disparar ou aninhar aplicativos lógicos-aplicativos lógicos do Azure
description: Configurar pontos de extremidade HTTP para chamar, disparar ou aninhar fluxos de trabalho de aplicativo lógico em aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.workload: integration
author: ecfan
ms.author: klam
ms.reviewer: jehollan, klam, LADocs
manager: carmonm
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.openlocfilehash: 4fc20c4b1314d953ea979192c81b2c264292d3af
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73041971"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Chamar, disparar ou aninhar aplicativos lógicos usando pontos de extremidade HTTP em aplicativos lógicos do Azure

Você pode expor nativamente pontos de extremidade HTTP síncronos como gatilhos em aplicativos lógicos para que você possa disparar ou chamar seus aplicativos lógicos por meio de uma URL. Você também pode aninhar fluxos de trabalho em seus aplicativos lógicos usando um padrão de pontos de extremidade que podem ser chamados.

Para criar pontos de extremidade HTTP, você pode adicionar esses gatilhos para que seus aplicativos lógicos possam receber solicitações de entrada:

* [Pedido](../connectors/connectors-native-reqres.md)

* [Webhook de conexão de API](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [Webhook de HTTP](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Embora esses exemplos usem o gatilho de **solicitação** , você pode usar qualquer um dos gatilhos baseados em solicitação listados e todos os princípios se aplicam de forma idêntica aos outros tipos de gatilho.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Configurar um ponto de extremidade HTTP para seu aplicativo lógico

Para criar um ponto de extremidade HTTP, adicione um gatilho que possa receber solicitações de entrada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com "Portal do Azure"). Vá para seu aplicativo lógico e abra o designer de aplicativo lógico.

1. Adicione um gatilho que permite que seu aplicativo lógico receba solicitações de entrada. Por exemplo, adicione o gatilho de **solicitação** ao seu aplicativo lógico.

1. Em **esquema JSON do corpo da solicitação**, você pode opcionalmente inserir um esquema JSON para a carga (dados) que espera que o gatilho receba.

   O designer usa esse esquema para gerar tokens que seu aplicativo lógico pode usar para consumir, analisar e passar dados do gatilho por meio do fluxo de trabalho. Saiba mais sobre [tokens gerados a partir de esquemas JSON](#generated-tokens).

   Para este exemplo, insira este esquema, conforme mostrado no designer:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "string"
         }
      },
      "required": [
        "address"
      ]
    }
    ```

   ![Fornecer o esquema JSON para a ação de solicitação](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   > [!TIP]
   >
   > Você pode gerar um esquema para um conteúdo JSON de exemplo de uma ferramenta como [jsonschema.net](https://jsonschema.net/) ou no gatilho de **solicitação** escolhendo **usar o conteúdo de exemplo para gerar o esquema**. Insira seu conteúdo de exemplo e escolha **concluído**.

   Por exemplo, este conteúdo de exemplo:

   ```json
   {
      "address": "21 2nd Street, New York, New York"
   }
   ```

   gera este esquema:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "string"
         }
      }
   }
   ```

1. Guarde a aplicação lógica. Em **http post para esta URL**, agora você deve encontrar uma URL de retorno de chamada gerada, como neste exemplo:

   ![URL de retorno de chamada gerada para o ponto de extremidade](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Essa URL contém uma chave de assinatura de acesso compartilhado (SAS) nos parâmetros de consulta que são usados para autenticação. Você também pode obter a URL de ponto de extremidade HTTP de sua visão geral do aplicativo lógico no portal do Azure. Em **histórico de gatilho**, selecione seu gatilho:

   ![Obter URL de ponto de extremidade HTTP de portal do Azure](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   Ou você pode obter a URL fazendo esta chamada:

    ```http
    POST https://management.azure.com/{logic-app-resource-ID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Alterar o método HTTP para o gatilho

Por padrão, o gatilho de **solicitação** espera uma solicitação HTTP post, mas você pode usar um método http diferente.

> [!NOTE]
> Você pode especificar apenas um tipo de método.

1. Em seu gatilho de **solicitação** , escolha **Mostrar opções avançadas**.

2. Abra a lista de **métodos** . Para este exemplo, selecione **obter** para que você possa testar a URL do ponto de extremidade http mais tarde.

   > [!NOTE]
   > Você pode selecionar qualquer outro método HTTP ou especificar um método personalizado para seu próprio aplicativo lógico.

   ![Selecione o método HTTP a ser usado para a solicitação](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Aceitar parâmetros por meio da URL do ponto de extremidade HTTP

Quando desejar que a URL do ponto de extremidade HTTP aceite parâmetros, personalize o caminho relativo do gatilho.

1. Em seu gatilho de **solicitação** , escolha **Mostrar opções avançadas**. 

2. Em **método**, especifique o método http que você deseja que sua solicitação use. Para este exemplo, selecione o método **Get** , se ainda não tiver feito isso, para que você possa testar a URL do ponto de extremidade http.

   > [!NOTE]
   > Ao especificar um caminho relativo para o gatilho, você também deve especificar explicitamente um método HTTP para o gatilho.

3. Em **caminho relativo**, especifique o caminho relativo para o parâmetro que a URL deve aceitar, por exemplo, `customers/{customerID}`.

   ![Especifique o método HTTP e o caminho relativo para o parâmetro](./media/logic-apps-http-endpoint/relative-path-url-value.png)

4. Para usar o parâmetro, adicione uma ação de **resposta** ao seu aplicativo lógico. (Em seu gatilho, escolha **nova etapa**  > **adicionar uma ação**  > **resposta**) 

5. No **corpo**da resposta, inclua o token para o parâmetro que você especificou no caminho relativo do gatilho.

   Por exemplo, para retornar `Hello {customerID}`, atualize o **corpo** da resposta com `Hello {customerID token}`. A lista de conteúdo dinâmico deve aparecer e mostrar o token `customerID` para você selecionar.

   ![Adicionar parâmetro ao corpo da resposta](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   Seu **corpo** deve ser semelhante a este exemplo:

   ![Exemplo de corpo de resposta com parâmetro](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Guarde a aplicação lógica. 

    A URL do ponto de extremidade HTTP agora inclui o caminho relativo, por exemplo: 

    ```http
    https://prod-00.southcentralus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/customers/{customerID}...
    ```

7. Para testar o ponto de extremidade HTTP, copie e cole a URL atualizada em outra janela do navegador, mas substitua `{customerID}` por `123456` e pressione Enter.

   Seu navegador deve mostrar este texto: `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Tokens gerados a partir de esquemas JSON para seu aplicativo lógico

Quando você fornece um esquema JSON em seu gatilho de **solicitação** , o designer do aplicativo lógico gera tokens para propriedades nesse esquema. Você pode usar esses tokens para passar dados por meio do fluxo de trabalho do aplicativo lógico.

Para este exemplo, se você adicionar as propriedades `title` e `name` ao seu esquema JSON, seus tokens agora estarão disponíveis para uso em etapas posteriores do fluxo de trabalho. 

Aqui está o esquema JSON completo:

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>Criar fluxos de trabalho aninhados para aplicativos lógicos

Você pode aninhar fluxos de trabalho em seu aplicativo lógico adicionando outros aplicativos lógicos que podem receber solicitações. Para incluir esses aplicativos lógicos, adicione os **aplicativos lógicos do Azure-escolha uma ação de fluxo de trabalho de aplicativos lógicos** para seu gatilho. Em seguida, você pode selecionar aplicativos lógicos qualificados.

![Aninhar aplicativo lógico dentro do aplicativo lógico atual](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Chamar ou disparar aplicativos lógicos por meio de pontos de extremidade HTTP

Depois de criar seu ponto de extremidade HTTP, você pode disparar seu aplicativo lógico por meio de um método `POST` para a URL completa. Os aplicativos lógicos têm suporte interno para pontos de extremidade de acesso direto.

> [!NOTE] 
> Para executar manualmente um aplicativo lógico a qualquer momento, na barra de ferramentas do designer do aplicativo lógico ou do modo de exibição de código do aplicativo lógico, escolha **executar**.

## <a name="reference-content-from-an-incoming-request"></a>Conteúdo de referência de uma solicitação de entrada

Se o tipo do conteúdo for `application/json`, você poderá fazer referência a propriedades da solicitação de entrada. Caso contrário, o conteúdo é tratado como uma única unidade binária que você pode passar para outras APIs. Para fazer referência a esse conteúdo dentro do fluxo de trabalho, você deve converter esse conteúdo. Por exemplo, se você passar `application/xml` conteúdo, poderá usar `@xpath()` para uma extração de XPath ou `@json()` para converter XML em JSON. Saiba mais sobre como [trabalhar com tipos de conteúdo](../logic-apps/logic-apps-content-type.md).

Para obter a saída de uma solicitação de entrada, você pode usar a função `@triggerOutputs()`. A saída pode ser semelhante a este exemplo:

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

Para acessar a propriedade `body` especificamente, você pode usar o atalho `@triggerBody()`.

## <a name="respond-to-requests"></a>Responder a solicitações

Talvez você queira responder a determinadas solicitações que iniciam um aplicativo lógico retornando o conteúdo ao chamador. Para construir o código de status, o cabeçalho e o corpo da sua resposta, você pode usar a ação de **resposta** . Essa ação pode aparecer em qualquer lugar em seu aplicativo lógico, não apenas no final do fluxo de trabalho.

> [!NOTE] 
> Se seu aplicativo lógico não incluir uma **resposta**, o ponto de extremidade http responderá *imediatamente* com um status de **202 aceito** . Além disso, para a solicitação original obter a resposta, todas as etapas necessárias para a resposta devem ser concluídas dentro do [limite de tempo limite da solicitação](./logic-apps-limits-and-config.md) , a menos que você chame o fluxo de trabalho como um aplicativo lógico aninhado. Se nenhuma resposta ocorrer dentro desse limite, a solicitação de entrada atingirá o tempo limite e receberá o **tempo limite do cliente**de resposta http 408. Para aplicativos lógicos aninhados, o aplicativo lógico pai continua aguardando uma resposta até ser concluído, independentemente da quantidade de tempo necessária.

### <a name="construct-the-response"></a>Construir a resposta

Você pode incluir mais de um cabeçalho e qualquer tipo de conteúdo no corpo da resposta. Na resposta de exemplo, o cabeçalho especifica que a resposta tem o tipo de conteúdo `application/json`. e o corpo contém `title` e `name`, com base no esquema JSON atualizado anteriormente para o gatilho de **solicitação** .

![Fornecer conteúdo de resposta para ação de resposta HTTP](./media/logic-apps-http-endpoint/content-for-response-action.png)

As respostas têm estas propriedades:

| Propriedade | Descrição |
| --- | --- |
| statusCode |Especifica o código de status HTTP para responder à solicitação de entrada. Esse código pode ser qualquer código de status válido que comece com 2xx, 4xx ou 5xx. No entanto, os códigos de status 3xx não são permitidos. |
| conector |Define qualquer número de cabeçalhos a serem incluídos na resposta. |
| conteúdo |Especifica um objeto Body que pode ser uma cadeia de caracteres, um objeto JSON ou até mesmo conteúdo binário referenciado de uma etapa anterior. |

Esta é a aparência do esquema JSON agora para a ação de **resposta** :

``` json
"Response": {
   "type": "Response",
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

> [!TIP]
> Para exibir a definição de JSON completa para seu aplicativo lógico, no designer de aplicativo lógico, escolha **exibição de código**.

## <a name="q--a"></a>P&R

#### <a name="q-what-about-url-security"></a>P: e quanto à segurança de URL?

R: o Azure gera com segurança URLs de retorno de chamada do aplicativo lógico usando uma SAS (assinatura de acesso compartilhado). Essa assinatura passa como um parâmetro de consulta e deve ser validada antes que seu aplicativo lógico possa ser acionado. O Azure gera a assinatura usando uma combinação exclusiva de uma chave secreta por aplicativo lógico, o nome do gatilho e a operação que é executada. Portanto, a menos que alguém tenha acesso à chave do aplicativo lógico secreto, ele não pode gerar uma assinatura válida.

   > [!IMPORTANT]
   > Para sistemas seguros e de produção, é altamente recomendável chamar seu aplicativo lógico diretamente do navegador porque:
   > 
   > * A chave de acesso compartilhado aparece na URL.
   > * Você não pode gerenciar políticas de conteúdo seguro devido a domínios compartilhados entre clientes de aplicativos lógicos.

#### <a name="q-can-i-configure-http-endpoints-further"></a>P: posso configurar os pontos de extremidade HTTP ainda mais?

R: Sim, os pontos de extremidade HTTP dão suporte à configuração mais avançada por meio [do gerenciamento de API do Azure](../api-management/api-management-key-concepts.md). Esse serviço também oferece a capacidade de gerenciar consistentemente todas as suas APIs, incluindo aplicativos lógicos, configurar nomes de domínio personalizados, usar mais métodos de autenticação e muito mais, por exemplo:

* [Alterar o método de solicitação](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Alterar os segmentos de URL da solicitação](../api-management/api-management-transformation-policies.md#RewriteURL)
* Configurar seus domínios de gerenciamento de API no [portal do Azure](https://portal.azure.com/)
* Configurar a política para verificar a autenticação básica

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>P: o que mudou quando o esquema migrou da versão prévia de 1º de dezembro de 2014?

R: aqui está um resumo sobre essas alterações:

| Versão prévia de 1º de dezembro de 2014 | 1º de junho de 2016 |
| --- | --- |
| Clique em aplicativo de API de **escuta http** |Clique em **gatilho manual** (nenhum aplicativo de API é necessário) |
| Configuração "*envia resposta automaticamente*" do OUVInte http |Incluir uma ação de **resposta** ou não estar na definição do fluxo de trabalho |
| Configurar a autenticação básica ou OAuth |por meio do gerenciamento de API |
| Configurar o método HTTP |Em **Mostrar opções avançadas**, escolha um método http |
| Configurar caminho relativo |Em **Mostrar opções avançadas**, adicione um caminho relativo |
| Referencie o corpo de entrada por meio de `@triggerOutputs().body.Content` |Referência por meio de `@triggerOutputs().body` |
| **Enviar ação de resposta http** no OUVInte http |Clique em **responder à solicitação HTTP** (nenhum aplicativo de API é necessário) |

## <a name="next-steps"></a>Passos seguintes

* [Criar definições de aplicação lógica](logic-apps-author-definitions.md)
* [Lidar com erros e exceções](logic-apps-exception-handling.md)