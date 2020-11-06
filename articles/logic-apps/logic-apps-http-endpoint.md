---
title: Aplicações lógicas de chamada, gatilho ou ninho utilizando gatilhos de pedido
description: Configurar pontos finais HTTPS para chamadas, desencadeações ou fluxos de trabalho de aplicações lógicas de nidificação em Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 08/27/2020
ms.openlocfilehash: 8a59b47dadd845f1a522854c503af11c8fff72fd
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331979"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-https-endpoints-in-azure-logic-apps"></a>Aplicações lógicas de chamada, gatilho ou ninho utilizando pontos finais HTTPS em Azure Logic Apps

Para tornar a sua aplicação lógica callable através de um URL e capaz de receber pedidos de entrada de outros serviços, pode desinsutilar um ponto final https sincronizado utilizando um gatilho baseado em pedidos na sua aplicação lógica. Com esta capacidade, pode ligar para a sua aplicação lógica a partir de outras aplicações lógicas e criar um padrão de pontos finais callable. Para configurar um ponto final chamado para o manuseamento de chamadas de entrada, pode utilizar qualquer um destes tipos de gatilho:

* [Pedir](../connectors/connectors-native-reqres.md)
* [Webhook de HTTP](../connectors/connectors-native-webhook.md)
* Triggers de conector geridos que têm o [tipo ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e podem receber pedidos HTTPS de entrada

Este artigo mostra como criar um ponto final chamado na sua aplicação lógica, utilizando o gatilho 'Pedido' e chamando esse ponto final de outra aplicação lógica. Todos os princípios aplicam-se de forma idêntica aos outros tipos de gatilho que pode utilizar para receber pedidos de entrada.


Para mais informações sobre segurança, autorização e encriptação para chamadas de entrada para a sua aplicação lógica, como [a Transport Layer Security (TLS),](https://en.wikipedia.org/wiki/Transport_Layer_Security)anteriormente conhecida como Secure Sockets Layer (SSL), [Azure Ative Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml), expondo a sua aplicação lógica com a Azure API Management, ou restringindo os endereços IP que originam chamadas de entrada, ver acesso seguro e dados - Acesso a chamadas de [entrada para pedidos baseados em gatilhos](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* A aplicação lógica onde pretende usar o gatilho para criar o ponto final chamado. Pode começar com uma aplicação lógica em branco ou com uma aplicação lógica existente onde pode substituir o gatilho atual. Este exemplo começa com uma aplicação lógica em branco. Se é novo em aplicações lógicas, consulte [O que é Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-callable-endpoint"></a>Criar um ponto final callable

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Crie e abra uma aplicação lógica em branco no Logic App Designer.

1. Sob a caixa de pesquisa, selecione **Built-in**. Na caixa de pesquisa, introduza `request` como filtro. A partir da lista de gatilhos, selecione **Quando receber um pedido HTTP**.

   ![Localizar e selecionar o gatilho 'Pedido'](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Opcionalmente, na caixa **de Esquema JSON do Corpo de Pedido,** pode introduzir um esquema JSON que descreve a carga útil ou dados que espera que o gatilho receba.

   O designer usa este esquema para gerar fichas que representam saídas de gatilho. Em seguida, pode facilmente referenciar estas saídas através do fluxo de trabalho da sua aplicação lógica. Saiba mais sobre [os tokens gerados a partir de esquemas JSON](#generated-tokens).

   Para este exemplo, insira este esquema:

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

   ![Fornecer esquema JSON para a ação Request](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Ou, pode gerar um esquema JSON fornecendo uma carga útil da amostra:

   1. No gatilho **'Pedido',** **selecione Utilize a carga útil da amostra para gerar esquema**.

   1. Na caixa de carga JSON de **amostra ou cole uma amostra,** introduza a carga útil da amostra, por exemplo:

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

   1. Quando estiver pronto, selecione **'Fazer'.**

      A caixa **de esquemas JSON do Corpo de Pedido** mostra agora o esquema gerado.

1. Guarde a sua aplicação lógica.

   A caixa **URL HTTP POST** mostra agora o URL de retorno gerado que outros serviços podem usar para ligar e desencadear a sua aplicação lógica. Este URL inclui parâmetros de consulta que especificam uma chave assinatura de acesso partilhado (SAS), que é usada para autenticação.

   ![URL de retorno de chamada gerado para ponto final](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

1. Para copiar o URL de retorno, tem estas opções:

   * À direita da caixa **URL HTTP POST,** selecione **Copy Url** (ícone de ficheiros de cópia).

   * Faça esta chamada POST:

     `POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01`

   * Copie o URL de retorno do painel de **visão geral** da sua aplicação lógica.

     1. No menu da sua aplicação lógica, selecione **Overview**.

     1. Na secção **Resumo,** **selecione Ver histórico do gatilho**.

        ![Obtenha URL de ponto final do portal Azure](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

     1. Em **url callback [POST]** , copiar o URL:

        ![Cópia URL de ponto final do portal Azure](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url-post.png)

<a name="select-method"></a>

## <a name="select-expected-request-method"></a>Selecione o método de pedido esperado

Por predefinição, o gatilho 'Pedido' espera um pedido DEM. Pode especificar um método diferente de esperar, mas apenas um único método.

1. No gatilho 'Pedido', abra a nova lista **de parâmetros e** selecione **Método,** que adiciona esta propriedade ao gatilho.

   ![Adicione a propriedade "Método" para desencadear](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Na lista **De Métodos,** selecione o método que o gatilho deve esperar. Ou, pode especificar um método personalizado.

   Por exemplo, selecione o método **GET** para que possa testar o URL do seu ponto final mais tarde.

   ![Selecione o método de pedido esperado pelo gatilho](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

<a name="endpoint-url-parameters"></a>

## <a name="pass-parameters-through-endpoint-url"></a>Passe parâmetros através do URL do ponto final

Quando pretende aceitar valores de parâmetros através do URL do ponto final, tem estas opções:

* [Aceite valores através de parâmetros GET](#get-parameters) ou URL.

  Estes valores são passados como pares de valor-nome na URL do ponto final. Para esta opção, tem de utilizar o método GET no seu gatilho Pedido. Numa ação posterior, pode obter os valores dos parâmetros como saídas de gatilho utilizando a `triggerOutputs()` função numa expressão.

* [Aceite valores através de um caminho relativo](#relative-path) para parâmetros no seu gatilho Pedido.

  Estes valores são passados por um caminho relativo na URL do ponto final. Também precisa de [selecionar](#select-method) explicitamente o método que o gatilho espera. Numa ação posterior, pode obter os valores dos parâmetros como saídas de gatilho, referindo-se diretamente a essas saídas.

<a name="get-parameters"></a>

### <a name="accept-values-through-get-parameters"></a>Aceitar valores através de parâmetros GET

1. No gatilho 'Pedido', abra a **nova lista de parâmetros adicionar,** adicione a propriedade **Method** ao gatilho e selecione o método **GET.**

   Para obter mais informações, consulte [Selecionar o método de pedido esperado.](#select-method)

1. No detonador 'Pedido', adicione a ação onde pretende utilizar o valor do parâmetro. Para este exemplo, adicione a ação **Resposta.**

   1. No âmbito do gatilho 'Pedido', selecione **Novo passo** Adicione  >  **uma ação**.
   
   1. Em **Escolha uma ação,** na caixa de pesquisa, introduza `response` como filtro. Na lista de ações, selecione a ação **Resposta.**

1. Para construir a `triggerOutputs()` expressão que recupera o valor do parâmetro, siga estes passos:

   1. Clique dentro da propriedade **'Corpo'** de ação resposta para que a lista de conteúdos dinâmicos apareça e selecione **Expressão**.

   1. Na caixa **Expressão,** introduza esta expressão, substituindo `parameter-name` o nome do seu parâmetro, e selecione **OK**.

      `triggerOutputs()['queries']['parameter-name']`

      ![Adicione a expressão "triggerOutputs()" para desencadear](./media/logic-apps-http-endpoint/trigger-outputs-expression.png)

      Na propriedade **Body,** a expressão `triggerOutputs()` resolve-se ao símbolo.

      ![Expressão "triggerOutputs()"](./media/logic-apps-http-endpoint/trigger-outputs-expression-token.png)

      Se guardar a aplicação lógica, navegar para longe do designer e regressar ao designer, o símbolo mostra o nome do parâmetro que especificou, por exemplo:

      ![Expressão resolvida para nome de parâmetro](./media/logic-apps-http-endpoint/resolved-expression-parameter-token.png)

      Na visão de código, a propriedade **Body** aparece na definição da ação resposta da seguinte forma:

      `"body": "@{triggerOutputs()['queries']['parameter-name']}",`

      Por exemplo, suponha que queira passar um valor para um parâmetro chamado `postalCode` . A propriedade **Body** especifica a cadeia, `Postal Code: ` com um espaço de fuga, seguido da expressão correspondente:

      ![Adicione o exemplo de expressão "triggerOutputs()" para desencadear](./media/logic-apps-http-endpoint/trigger-outputs-expression-postal-code.png)

1. Para testar o seu ponto final callable, copie o URL de retorno do gatilho 'Pedido' e cole o URL noutra janela do navegador. No URL, adicione o nome e o valor do parâmetro seguindo o ponto de interrogação `?` () ao URL no seguinte formato e prima Enter.

   `...?{parameter-name=parameter-value}&api-version=2016-10-01...`

   `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?{parameter-name=parameter-value}&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   O navegador devolve uma resposta com este texto: `Postal Code: 123456`

   ![Resposta do envio de pedido para URL de retorno](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

1. Para colocar o nome e o valor do parâmetro numa posição diferente dentro do URL, certifique-se de utilizar o ampersand `&` () como prefixo, por exemplo:

   `...?api-version=2016-10-01&{parameter-name=parameter-value}&...`

   Este exemplo mostra o URL de retorno com o nome do parâmetro da amostra e valor `postalCode=123456` em diferentes posições dentro do URL:

   * 1ª posição: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?postalCode=123456&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   * 2ª posição: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?api-version=2016-10-01&postalCode=123456&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

> [!NOTE]
> Se quiser incluir o símbolo de haxixe ou libra **#** () no URI, utilize esta versão codificada: `%25%23`

<a name="relative-path"></a>

### <a name="accept-values-through-a-relative-path"></a>Aceitar valores através de um caminho relativo

1. No gatilho 'Pedido', abra a nova lista **de parâmetros adicionar** e selecione **O caminho relativo,** que adiciona esta propriedade ao gatilho.

   ![Adicione a propriedade "Caminho Relativo" para desencadear](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. Na propriedade **relativa do caminho,** especifique o caminho relativo para o parâmetro no seu esquema JSON que deseja que o seu URL aceite, por exemplo, `/address/{postalCode}` .

   ![Especificar o caminho relativo para o parâmetro](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. No detonador 'Pedido', adicione a ação onde pretende utilizar o valor do parâmetro. Para este exemplo, adicione a ação **Resposta.**

   1. No âmbito do gatilho 'Pedido', selecione **Novo passo** Adicione  >  **uma ação**.

   1. Em **Escolha uma ação,** na caixa de pesquisa, introduza `response` como filtro. Na lista de ações, selecione a ação **Resposta.**

1. Na propriedade **Body** da ação resposta, inclua o token que representa o parâmetro especificado no caminho relativo do seu gatilho.

   Por exemplo, suponha que quer que a ação Resposta regresse `Postal Code: {postalCode}` .

   1. Na propriedade **Corpo,** entre `Postal Code: ` com um espaço de fuga. Mantenha o cursor dentro da caixa de edição para que a lista de conteúdos dinâmicos permaneça aberta.

   1. Na lista de conteúdos dinâmicos, a partir da secção **Quando é recebido um pedido HTTP,** selecione o **token postalCode.**

      ![Adicione o parâmetro especificado ao corpo de resposta](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

      A propriedade **Body** inclui agora o parâmetro selecionado:

      ![Corpo de resposta de exemplo com parâmetro](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Guarde a sua aplicação lógica.

   No gatilho do Pedido, o URL de retorno é atualizado e agora inclui o caminho relativo, por exemplo:

   `https://prod-07.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

1. Para testar o seu ponto final callable, copie o URL de retorno atualizado do gatilho 'Pedido', cole o URL noutra janela do navegador, substitua `{postalCode}` no URL com , e prima `123456` Enter.

   O navegador devolve uma resposta com este texto: `Postal Code: 123456`

   ![Resposta do envio de pedido para URL de retorno](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

> [!NOTE]
> Se quiser incluir o símbolo de haxixe ou libra **#** () no URI, utilize esta versão codificada: `%25%23`

## <a name="call-logic-app-through-endpoint-url"></a>App lógica de chamada através de URL de ponto final

Depois de criar o ponto final, pode desencadear a aplicação lógica enviando um pedido HTTPS `POST` para o URL completo do ponto final. As aplicações lógicas têm suporte integrado para pontos finais de acesso direto.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Fichas geradas a partir de esquema

Quando fornece um esquema JSON no gatilho Request, o Logic App Designer gera fichas para as propriedades nesse esquema. Em seguida, pode utilizar esses tokens para passar dados através do fluxo de trabalho da sua aplicação lógica.

Por exemplo, se adicionar mais propriedades, `"suite"` como, por exemplo, ao seu esquema JSON, os tokens para essas propriedades estão disponíveis para que possa utilizar nos passos posteriores para a sua aplicação lógica. Aqui está o esquema completo do JSON:

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

Pode nidificar fluxos de trabalho na sua aplicação lógica adicionando outras aplicações lógicas que podem receber pedidos. Para incluir estas aplicações lógicas, siga estes passos:

1. Sob o passo em que pretende chamar outra aplicação lógica, selecione **Novo passo** Adicione  >  **uma ação**.

1. Em **Escolha uma ação** , selecione **Built-in**. Na caixa de pesquisa, introduza `logic apps` como filtro. A partir da lista de ações, **selecione Escolha um fluxo de trabalho de Apps Lógicas.**

   ![App lógica ninho dentro da app lógica atual](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   O designer mostra as aplicações lógicas elegíveis para você selecionar.

1. Selecione a aplicação lógica para ligar a partir da sua aplicação lógica atual.

   ![Selecione app lógica para ligar a partir da app lógica atual](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Conteúdo de referência de um pedido de entrada

Se o tipo de conteúdo do pedido de entrada `application/json` for, pode referenciar as propriedades no pedido de entrada. Caso contrário, este conteúdo é tratado como uma única unidade binária que pode passar para outras APIs. Para fazer referência a este conteúdo dentro do fluxo de trabalho da sua aplicação lógica, é necessário converter primeiro esse conteúdo.

Por exemplo, se estiver a passar conteúdo que tenha `application/xml` tipo, pode usar a [ `@xpath()` expressão](../logic-apps/workflow-definition-language-functions-reference.md#xpath) para realizar uma extração de XPath, ou usar a [ `@json()` expressão](../logic-apps/workflow-definition-language-functions-reference.md#json) para converter XML em JSON. Saiba mais sobre trabalhar com tipos de [conteúdo](../logic-apps/logic-apps-content-type.md)suportados.

Para obter a saída de um pedido de entrada, pode utilizar a [ `@triggerOutputs` expressão](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Por exemplo, suponha que tem uma saída que se parece com este exemplo:

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

Para aceder especificamente à `body` propriedade, pode usar a [ `@triggerBody()` expressão](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) como atalho.

## <a name="respond-to-requests"></a>Responder aos pedidos

Por vezes, pretende responder a determinados pedidos que desencadeiam a sua aplicação lógica devolvendo o conteúdo ao chamador. Para construir o código de estado, cabeçalho e corpo para a sua resposta, utilize a ação Resposta. Esta ação pode aparecer em qualquer lugar da sua aplicação lógica, não apenas no final do seu fluxo de trabalho. Se a sua aplicação lógica não incluir uma ação de Resposta, o ponto final responde *imediatamente* com o estado **202 Aceite.**

Para que o chamador original obtenha com sucesso a resposta, todas as etapas necessárias para a resposta devem terminar dentro do prazo de tempo do [pedido,](./logic-apps-limits-and-config.md) a menos que a aplicação lógica desencadeada seja chamada como uma app lógica aninhada. Se nenhuma resposta for devolvida dentro deste limite, o pedido de entrada termina e recebe a resposta **de tempo limite do Cliente 408.**

Para aplicações lógicas aninhadas, a aplicação lógica dos pais continua a aguardar uma resposta até que todos os passos estejam concluídos, independentemente do tempo necessário.

### <a name="construct-the-response"></a>Construa a resposta

No corpo de resposta, pode incluir vários cabeçalhos e qualquer tipo de conteúdo. Por exemplo, o cabeçalho desta resposta especifica que o tipo de conteúdo da resposta é `application/json` e que o corpo contém valores para o e `town` `postalCode` propriedades, com base no esquema JSON descrito anteriormente neste tópico para o gatilho Request.

![Fornecer conteúdo de resposta para ação https response](./media/logic-apps-http-endpoint/content-for-response-action.png)

As respostas têm estas propriedades:

| Propriedade (Exibição) | Propriedade (JSON) | Descrição |
|--------------------|-----------------|-------------|
| **Código de Estado** | `statusCode` | O código de estado HTTPS a utilizar na resposta ao pedido de entrada. Este código pode ser qualquer código de estado válido que comece com 2xx, 4xx ou 5xx. No entanto, não são permitidos códigos de estado 3xx. |
| **Cabeçalhos** | `headers` | Um ou mais cabeçalhos para incluir na resposta |
| **Corpo** | `body` | Um objeto corporal que pode ser uma corda, um objeto JSON, ou mesmo conteúdo binário referenciado a partir de um passo anterior |
||||

Para ver a definição JSON para a ação Resposta e a definição completa de JSON da sua aplicação lógica, na barra de ferramentas Logic App Designer, selecione **a vista de código**.

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

## <a name="q--a"></a>Perguntas e Respostas

#### <a name="q-what-about-url-security"></a>P: E a segurança da URL?

**A** : Azure gera de forma segura URLs de callback de aplicações lógicas utilizando [a Assinatura de Acesso Partilhado (SAS)](/rest/api/storageservices/delegate-access-with-shared-access-signature). Esta assinatura passa como parâmetro de consulta e deve ser validada antes que a sua aplicação lógica possa ser executada. O Azure gera a assinatura usando uma combinação única de uma chave secreta por aplicação lógica, o nome do gatilho e a operação que é realizada. Portanto, a menos que alguém tenha acesso à chave de aplicações lógicas secretas, não pode gerar uma assinatura válida.

> [!IMPORTANT]
> Para a produção e sistemas de segurança mais elevados, aconselhamos veementemente que não chame a sua aplicação lógica diretamente do navegador por estas razões:
>
> * A chave de acesso partilhada aparece na URL.
> * Não é possível gerir políticas de conteúdo de segurança devido a domínios partilhados em todos os clientes da Azure Logic Apps.

Para mais informações sobre segurança, autorização e encriptação para chamadas de entrada para a sua aplicação lógica, como [a Transport Layer Security (TLS),](https://en.wikipedia.org/wiki/Transport_Layer_Security)anteriormente conhecida como Secure Sockets Layer (SSL), [Azure Ative Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml), expondo a sua aplicação lógica com a Azure API Management, ou restringindo os endereços IP que originam chamadas de entrada, ver acesso seguro e dados - Acesso a chamadas de [entrada para pedidos baseados em gatilhos](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

#### <a name="q-can-i-configure-callable-endpoints-further"></a>P: Posso configurar ainda mais os pontos finais callable?

**R:** Sim, os pontos finais HTTPS suportam uma configuração mais avançada através [da Azure API Management](../api-management/api-management-key-concepts.md). Este serviço também oferece a capacidade para gerir de forma consistente todas as suas APIs, incluindo aplicações lógicas, configurar nomes de domínio personalizados, usar mais métodos de autenticação, e muito mais, por exemplo:

* [Alterar o método de pedido](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Alterar os segmentos de URL do pedido](../api-management/api-management-transformation-policies.md#RewriteURL)
* Confle o seu domínio de Gestão de API no [portal Azure](https://portal.azure.com/)
* Definir política para verificar a autenticação básica

## <a name="next-steps"></a>Passos seguintes

* [Receber e responder às chamadas HTTPS recebidas utilizando apps Azure Logic](../connectors/connectors-native-reqres.md)
* [Acesso seguro e dados em Azure Logic Apps - Acesso a chamadas de entrada para gatilhos baseados em pedidos](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
