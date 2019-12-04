---
title: Chamar, acionar ou aninhar aplicações lógicas
description: Configurar pontos de extremidade HTTP para chamar, disparar ou aninhar fluxos de trabalho de aplicativo lógico em aplicativos lógicos do Azure
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: dbb91106ad00e1a82e2e6e9c470e61764a4ad4c4
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792025"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Chamar, disparar ou aninhar aplicativos lógicos usando pontos de extremidade HTTP em aplicativos lógicos do Azure

Para tornar seu aplicativo lógico chamável por meio de uma URL para que seu aplicativo lógico possa receber solicitações de entrada de outros serviços, você pode expor nativamente um ponto de extremidade HTTP síncrono como um gatilho nesse aplicativo lógico. Ao configurar esse recurso, você também pode aninhar seu aplicativo lógico dentro de outros aplicativos lógicos, o que permite criar um padrão de pontos de extremidade que podem ser chamados.

Para configurar um ponto de extremidade HTTP, você pode usar qualquer um desses tipos de gatilho, o que permite que os aplicativos lógicos recebam solicitações de entrada:

* [Pedido](../connectors/connectors-native-reqres.md)
* [Webhook de HTTP](../connectors/connectors-native-webhook.md)
* Gatilhos de conector gerenciado que têm o [tipo ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e podem receber solicitações HTTP de entrada

> [!NOTE]
> Esses exemplos usam o gatilho de solicitação, mas você pode usar qualquer gatilho baseado em solicitação HTTP que esteja na lista anterior. Todos os princípios se aplicam de forma idêntica a esses outros tipos de gatilho.

Se você for novo em aplicativos lógicos, consulte [o que é o início rápido e aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) [: Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* O aplicativo lógico no qual você deseja configurar o ponto de extremidade HTTP como o gatilho. Você pode começar com um aplicativo lógico em branco ou um aplicativo lógico existente no qual você deseja substituir o gatilho atual. Este exemplo começa com um aplicativo lógico em branco.

## <a name="create-a-callable-endpoint"></a>Criar um ponto de extremidade que possa ser chamado

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Crie e abra um aplicativo lógico em branco no designer de aplicativo lógico.

   Este exemplo usa o gatilho Request, mas você pode usar qualquer gatilho que possa receber solicitações HTTP de entrada. Todos os princípios se aplicam de forma idêntica a esses gatilhos. Para obter mais informações sobre o gatilho de solicitação, consulte [receber e responder a chamadas HTTPS de entrada usando o aplicativo lógico do Azure](../connectors/connectors-native-reqres.md).

1. Na caixa de pesquisa, selecione **interno**. Na caixa de pesquisa, insira `request` como seu filtro. Na lista de gatilhos, selecione **quando uma solicitação HTTP é recebida**.

   ![Localizar e selecionar o gatilho de solicitação](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Opcionalmente, na caixa **esquema JSON do corpo da solicitação** , você pode inserir um esquema JSON que descreve a carga ou os dados que você espera que o gatilho receba.

   O designer usa esse esquema para gerar tokens que representam saídas de gatilho. Você pode fazer referência facilmente a essas saídas em todo o fluxo de trabalho do aplicativo lógico. Saiba mais sobre [tokens gerados a partir de esquemas JSON](#generated-tokens).

   Para este exemplo, digite este esquema:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![Fornecer o esquema JSON para a ação de solicitação](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Ou, você pode gerar um esquema JSON fornecendo um conteúdo de exemplo:

   1. No gatilho de **solicitação** , selecione **usar conteúdo de exemplo para gerar o esquema**.

   1. Na caixa **Inserir ou colar um conteúdo JSON de exemplo** , insira seu conteúdo de exemplo, por exemplo:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. Quando estiver pronto, selecione **concluído**.

      A caixa **esquema JSON do corpo da solicitação** agora mostra o esquema gerado.

1. Guarde a aplicação lógica.

   A caixa **http post para esta URL** agora mostra a URL de retorno de chamada gerada que outros serviços podem usar para chamar e disparar seu aplicativo lógico. Essa URL inclui uma chave de assinatura de acesso compartilhado (SAS), que é usada para autenticação, nos parâmetros de consulta, por exemplo:

   ![URL de retorno de chamada gerada para o ponto de extremidade](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Você também pode obter a URL de ponto de extremidade HTTP no painel de **visão geral** do seu aplicativo lógico.

   1. No menu do aplicativo lógico, selecione **visão geral**.

   1. Na seção **Resumo** , selecione **Ver Histórico de gatilho**.

      ![Obter URL de ponto de extremidade HTTP de portal do Azure](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. Em **URL de retorno de chamada [post]** , copie a URL:

      ![Copiar URL de ponto de extremidade HTTP de portal do Azure](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Ou você pode obter a URL fazendo esta chamada:

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>Definir o método HTTP esperado

Por padrão, o gatilho de solicitação espera uma solicitação HTTP POST. No entanto, você pode especificar um método diferente para esperar, mas apenas um método.

1. No gatilho de solicitação, abra a lista **Adicionar novo parâmetro** e selecione o **método**, que adiciona essa propriedade ao gatilho.

   ![Adicionar a propriedade "Method" para disparar](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Na lista **método** , selecione outro método que o gatilho espera em vez disso. Ou, você pode especificar um método personalizado.

   Por exemplo, selecione o método **Get** para que você possa testar a URL do ponto de extremidade http mais tarde.

   ![Selecione o método HTTP a ser usado para o gatilho](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Aceitar parâmetros na URL do ponto de extremidade

Quando desejar que a URL do ponto de extremidade aceite parâmetros, especifique o caminho relativo no gatilho. Você também precisa definir explicitamente [o método](#set-method) que sua solicitação HTTP espera.

1. No gatilho de solicitação, abra a lista **Adicionar novo parâmetro** e selecione **caminho relativo**, que adiciona essa propriedade ao gatilho.

   ![Adicionar a propriedade "caminho relativo" para disparar](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. Na propriedade **caminho relativo** , especifique o caminho relativo para o parâmetro no esquema JSON que você deseja que sua URL aceite, por exemplo, `address/{postalCode}`.

   ![Especifique o caminho relativo para o parâmetro](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Para usar o parâmetro, localize e adicione uma ação de **resposta** ao seu aplicativo lógico.

   1. No gatilho de solicitação, selecione **nova etapa** > **Adicionar uma ação**.

   1. Em **escolher uma ação**, na caixa de pesquisa, insira `response` como filtro.

   1. Na lista ações, selecione a ação **resposta** .

1. Na propriedade **Body** da ação de resposta, inclua o token que representa o parâmetro que você especificou no caminho relativo do gatilho.

   Por exemplo, suponha que você deseja que a ação de resposta retorne `Postal Code: {postalCode}`.

   Na propriedade **corpo** , insira `Postal Code: ` com um espaço à direita. Na lista de conteúdo dinâmico que aparece, selecione o token de **PostalCode** .

   ![Adicionar o parâmetro especificado ao corpo da resposta](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   A propriedade **Body** agora inclui o parâmetro selecionado:

   ![Exemplo de corpo de resposta com parâmetro](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Guarde a aplicação lógica.

    A URL do ponto de extremidade HTTP agora inclui o caminho relativo, por exemplo:

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. Para testar o ponto de extremidade HTTP, copie e cole a URL atualizada em outra janela do navegador, mas substitua `{postalCode}` por `123456`e pressione Enter.

   Seu navegador mostra este texto: `Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>Chamar aplicativo lógico por meio do ponto de extremidade HTTP

Depois de criar o ponto de extremidade HTTP, você pode disparar o aplicativo lógico enviando uma solicitação HTTP `POST` para a URL completa do ponto de extremidade. Os aplicativos lógicos têm suporte interno para pontos de extremidade de acesso direto.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Tokens gerados a partir do esquema

Quando você fornece um esquema JSON no gatilho de solicitação, o designer do aplicativo lógico gera tokens para as propriedades nesse esquema. Você pode usar esses tokens para passar dados por meio do fluxo de trabalho do aplicativo lógico.

Por exemplo, se você adicionar mais propriedades, como `"suite"`, ao esquema JSON, os tokens para essas propriedades estarão disponíveis para uso nas etapas posteriores para seu aplicativo lógico. Aqui está o esquema JSON completo:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Criar aplicativos lógicos aninhados

Você pode aninhar fluxos de trabalho em seu aplicativo lógico adicionando outros aplicativos lógicos que podem receber solicitações. Para incluir esses aplicativos lógicos, siga estas etapas:

1. Na etapa em que você deseja chamar outro aplicativo lógico, selecione **nova etapa** > **Adicionar uma ação**.

1. Em **escolher uma ação**, selecione **interno**. Na caixa de pesquisa, insira `logic apps` como seu filtro. Na lista ações, selecione **escolher um fluxo de trabalho de aplicativos lógicos**.

   ![Aninhar aplicativo lógico dentro do aplicativo lógico atual](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   O designer mostra os aplicativos lógicos qualificados para você selecionar.

1. Selecione o aplicativo lógico a ser chamado do seu aplicativo lógico atual.

   ![Selecionar aplicativo lógico para chamar do aplicativo lógico atual](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Conteúdo de referência de uma solicitação de entrada

Se o tipo de conteúdo da solicitação de entrada for `application/json`, você poderá fazer referência às propriedades na solicitação de entrada. Caso contrário, esse conteúdo é tratado como uma única unidade binária que você pode passar para outras APIs. Para fazer referência a esse conteúdo dentro do fluxo de trabalho do aplicativo lógico, primeiro você precisa converter esse conteúdo.

Por exemplo, se você estiver passando o conteúdo que tem `application/xml` tipo, poderá usar a [expressão`@xpath()`](../logic-apps/workflow-definition-language-functions-reference.md#xpath) para executar uma extração de XPath ou usar a [expressão`@json()`](../logic-apps/workflow-definition-language-functions-reference.md#json) para converter XML em JSON. Saiba mais sobre como trabalhar com [tipos de conteúdo](../logic-apps/logic-apps-content-type.md)com suporte.

Para obter a saída de uma solicitação de entrada, você pode usar a [expressão`@triggerOutputs`](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Por exemplo, suponha que você tenha uma saída semelhante a este exemplo:

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

Para acessar especificamente a propriedade `body`, você pode usar a [expressão`@triggerBody()`](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) como um atalho.

## <a name="respond-to-requests"></a>Responder a solicitações

Às vezes, você deseja responder a determinadas solicitações que disparam o aplicativo lógico retornando o conteúdo ao chamador. Para construir o código de status, o cabeçalho e o corpo da sua resposta, use a ação de resposta. Essa ação pode aparecer em qualquer lugar em seu aplicativo lógico, não apenas no final do fluxo de trabalho. Se seu aplicativo lógico não incluir uma ação de resposta, o ponto de extremidade HTTP responderá *imediatamente* com o status de **202 aceito** .

Para que o chamador original obtenha a resposta com êxito, todas as etapas necessárias para a resposta devem ser concluídas dentro do [limite de tempo limite da solicitação](./logic-apps-limits-and-config.md) , a menos que o aplicativo lógico disparado seja chamado como um aplicativo lógico aninhado. Se nenhuma resposta for retornada dentro desse limite, a solicitação de entrada atingirá o tempo limite e receberá a resposta de **tempo limite do cliente 408** .

Para aplicativos lógicos aninhados, o aplicativo lógico pai continua aguardando uma resposta até que todas as etapas sejam concluídas, independentemente da quantidade de tempo necessária.

### <a name="construct-the-response"></a>Construir a resposta

No corpo da resposta, você pode incluir vários cabeçalhos e qualquer tipo de conteúdo. Por exemplo, o cabeçalho dessa resposta especifica que o tipo de conteúdo da resposta é `application/json` e que o corpo contém valores para as propriedades `town` e `postalCode`, com base no esquema JSON descrito anteriormente neste tópico para o gatilho de solicitação.

![Fornecer conteúdo de resposta para ação de resposta HTTP](./media/logic-apps-http-endpoint/content-for-response-action.png)

As respostas têm estas propriedades:

| Propriedade (exibição) | Propriedade (JSON) | Descrição |
|--------------------|-----------------|-------------|
| **Código de Estado** | `statusCode` | O código de status HTTP a ser usado na resposta para a solicitação de entrada. Esse código pode ser qualquer código de status válido que comece com 2xx, 4xx ou 5xx. No entanto, os códigos de status 3xx não são permitidos. |
| **Headers** (Cabeçalhos) | `headers` | Um ou mais cabeçalhos a serem incluídos na resposta |
| **Corpo** | `body` | Um objeto Body que pode ser uma cadeia de caracteres, um objeto JSON ou até mesmo conteúdo binário referenciado de uma etapa anterior |
||||

Para exibir a definição de JSON para a ação de resposta e a definição JSON completa do aplicativo lógico, na barra de ferramentas do designer do aplicativo lógico, selecione **exibição de código**.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>P&R

#### <a name="q-what-about-url-security"></a>P: e quanto à segurança de URL?

**R: o**Azure gera com segurança URLs de retorno de chamada de aplicativo lógico usando [SAS (assinatura de acesso compartilhado)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature). Essa assinatura passa como um parâmetro de consulta e deve ser validada antes que seu aplicativo lógico possa ser executado. O Azure gera a assinatura usando uma combinação exclusiva de uma chave secreta por aplicativo lógico, o nome do gatilho e a operação que é executada. Portanto, a menos que alguém tenha acesso à chave do aplicativo lógico secreto, ele não pode gerar uma assinatura válida.

> [!IMPORTANT]
> Para sistemas seguros e de produção, aconselhamos fortemente a chamar seu aplicativo lógico diretamente do navegador por esses motivos:
>
> * A chave de acesso compartilhado aparece na URL.
> * Você não pode gerenciar políticas de conteúdo seguro devido a domínios compartilhados em clientes de aplicativos lógicos do Azure.

#### <a name="q-can-i-configure-http-endpoints-further"></a>P: posso configurar os pontos de extremidade HTTP ainda mais?

**R**: Sim, os pontos de extremidade http dão suporte à configuração mais avançada por meio [do gerenciamento de API do Azure](../api-management/api-management-key-concepts.md). Esse serviço também oferece a capacidade de gerenciar consistentemente todas as suas APIs, incluindo aplicativos lógicos, configurar nomes de domínio personalizados, usar mais métodos de autenticação e muito mais, por exemplo:

* [Alterar o método de solicitação](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Alterar os segmentos de URL da solicitação](../api-management/api-management-transformation-policies.md#RewriteURL)
* Configurar seus domínios de gerenciamento de API no [portal do Azure](https://portal.azure.com/)
* Configurar a política para verificar a autenticação básica

## <a name="next-steps"></a>Passos seguintes

* [Receber e responder a chamadas HTTPS de entrada usando aplicativos lógicos do Azure](../connectors/connectors-native-reqres.md)