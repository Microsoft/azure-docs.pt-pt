---
title: Chamar, acionar ou aninhar aplicações lógicas
description: Configurar pontos finais HTTPS para ligar, desencadear ou nest logic app workflows em Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 6c6191936f76431bd4e7b6f1d4eff2074ce4b04d
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141794"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-https-endpoints-in-azure-logic-apps"></a>Aplicativos de call, trigger ou nest logic usando pontos finais HTTPS em Aplicações Lógicas Azure

Para tornar a sua aplicação lógica chamada através de um URL para que a sua aplicação lógica possa receber pedidos de entrada de outros serviços, pode expor de forma nativa um ponto final de HTTPS sincronizado como um gatilho nessa aplicação lógica. Ao configurar esta capacidade, também pode nidificar a sua aplicação lógica dentro de outras aplicações lógicas, o que permite criar um padrão de pontos finais callable.

Para configurar um ponto final calivel, pode utilizar qualquer um destes tipos de gatilho, que permitem que as aplicações lógicas recebam pedidos de entrada:

* [Pedir](../connectors/connectors-native-reqres.md)
* [Webhook de HTTP](../connectors/connectors-native-webhook.md)
* Gatilhos de conector geridos que têm o [tipo ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e podem receber pedidos https recebidos

> [!NOTE]
> Estes exemplos utilizam o gatilho do Pedido, mas pode utilizar qualquer gatilho baseado em pedidos HTTPS que esteja na lista anterior. Todos os princípios se aplicam de forma idêntica a estes outros tipos de gatilho.

Se você é novo em aplicativos lógicos, veja [What is Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* A aplicação lógica onde pretende utilizar o gatilho para criar o ponto final calivel . Pode começar com uma aplicação lógica em branco ou com uma aplicação lógica existente onde pretende substituir o gatilho atual. Este exemplo começa com uma aplicação lógica em branco.

## <a name="create-a-callable-endpoint"></a>Criar um ponto final calivel

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Crie e abra uma aplicação lógica em branco no Logic App Designer.

   Este exemplo utiliza o gatilho Do Pedido, mas pode utilizar qualquer gatilho que possa receber pedidos HTTPS recebidos. Todos os princípios se aplicam de forma idêntica a estes gatilhos. Para mais informações sobre o gatilho do Pedido, consulte [Receber e responder a chamadas HTTPS de entrada utilizando aplicações lógicas do Azure](../connectors/connectors-native-reqres.md).

1. Sob a caixa **de**pesquisa, selecione Incorporado . Na caixa de pesquisa, introduza `request` como filtro. A partir da lista de gatilhos, selecione **Quando um pedido HTTP for recebido**.

   ![Localizar e selecionar o gatilho do Pedido](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Opcionalmente, na caixa **Do Corpo de Pedido JSON Schema,** pode introduzir um esquema JSON que descreve a carga útil ou os dados que espera que o gatilho receba.

   O designer usa este esquema para gerar tokens que representam saídas de gatilho. Em seguida, pode facilmente referenciar estas saídas através do fluxo de trabalho da sua aplicação lógica. Saiba mais sobre [fichas geradas a partir de schemas JSON](#generated-tokens).

   Para este exemplo, introduza este esquema:

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

   ![Fornecer schema JSON para a ação De Pedido](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Ou, pode gerar um esquema JSON fornecendo uma carga útil de amostra:

   1. No gatilho **Solicitar,** selecione Utilize a carga útil da **amostra para gerar esquema**.

   1. No Enter ou pasta uma caixa de **carga json** da amostra, introduza a sua carga útil da amostra, por exemplo:

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

   1. Quando estiver pronto, selecione **Done**.

      A caixa **Do Corpo de Pedido JSON Schema** mostra agora o esquema gerado.

1. Guarde a aplicação lógica.

   A caixa **DEURL HTTP POST** mostra agora o URL de callback gerado que outros serviços podem usar para ligar e acionar a sua aplicação lógica. Este URL inclui parâmetros de consulta que especificam uma chave De Acesso Partilhado (SAS), que é usada para autenticação.

   ![URL de callback gerado para ponto final](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

1. Para copiar o URL de callback, tem estas opções:

   * À direita da caixa **URL HTTP POST,** selecione **Copy Url** (ícone de ficheiros de cópia).

   * Faça esta chamada POST:

     `POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01`

   * Copie o URL de callback do painel de **visão geral** da sua aplicação lógica.

     1. No menu da sua aplicação lógica, selecione **Visão Geral**.

     1. Na secção **Resumo,** selecione **Ver histórico**do gatilho .

        ![Obtenha URL de ponto final do portal Azure](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

     1. Sob **url de Callback [POST],** copie o URL:

        ![URL de ponto final de cópia do portal Azure](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url-post.png)

<a name="select-method"></a>

## <a name="select-expected-request-method"></a>Selecione o método de pedido esperado

Por defeito, o gatilho do Pedido espera um pedido de CORREIO. Pode especificar um método diferente a esperar, mas apenas um método único.

1. No gatilho Request, abra a **lista de novos parâmetros** e selecione **Método,** que adiciona esta propriedade ao gatilho.

   ![Adicione a propriedade "Método" para desencadear](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Na lista **método,** selecione o método que o gatilho deve esperar. Ou, pode especificar um método personalizado.

   Por exemplo, selecione o método **GET** para que possa testar o URL do seu ponto final mais tarde.

   ![Selecione o método de pedido esperado pelo gatilho](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

<a name="endpoint-url-parameters"></a>

## <a name="pass-parameters-through-endpoint-url"></a>Passar parâmetros através de URL de ponto final

Quando pretende aceitar valores de parâmetros através do URL do ponto final, tem estas opções:

* [Aceite valores através](#get-parameters) de parâmetros GET ou URL.

  Estes valores são passados como pares de valor de nome no URL do ponto final. Para esta opção, tem de utilizar o método GET no gatilho 'Pedido'. Numa ação posterior, pode obter os valores do parâmetro como saídas de gatilho utilizando a `triggerOutputs()` função numa expressão.

* [Aceite valores através de um caminho relativo](#relative-path) para parâmetros no gatilho Do seu Pedido.

  Estes valores são passados por um caminho relativo no URL do ponto final. Também precisa selecionar explicitamente [o método](#select-method) que o gatilho espera. Numa ação posterior, pode obter os valores do parâmetro como saídas de gatilho, referenciando essas saídas diretamente.

> [!NOTE]
> O URL permite utilizar o símbolo "at" **@** (), mas não o símbolo hash **#** ().

<a name="get-parameters"></a>

### <a name="accept-values-through-get-parameters"></a>Aceitar valores através dos parâmetros GET

1. No gatilho Request, abra a **lista de parâmetros Adicionar novos,** adicione a propriedade **Método** ao gatilho e selecione o método **GET.**

   Para mais informações, consulte O método de [pedido esperado .](#select-method)

1. Sob o gatilho Solicitar, adicione a ação onde pretende utilizar o valor do parâmetro. Para este exemplo, adicione a ação **Resposta.**

   1. Sob o gatilho pedido, selecione **Novo passo**Adicione  >  **uma ação**.
   
   1. Em **'Escolha uma ação**' na caixa de pesquisa, introduza como `response` filtro. Na lista de ações, selecione a ação **Resposta.**

1. Para construir a `triggerOutputs()` expressão que recupera o valor do parâmetro, siga estes passos:

   1. Clique na propriedade **body** da ação resposta para que a lista de conteúdos dinâmicos apareça e selecione **Expression**.

   1. Na caixa **'Expressão',** introduza esta expressão, substituindo `parameter-name` pelo nome do parâmetro e selecione **OK**.

      `triggerOutputs()['queries']['parameter-name']`

      ![Adicione a expressão "triggerOutputs()" para desencadear](./media/logic-apps-http-endpoint/trigger-outputs-expression.png)

      Na propriedade **do Corpo,** a expressão resolve-se ao `triggerOutputs()` símbolo.

      ![Expressão resolvida "triggerOutputs()"](./media/logic-apps-http-endpoint/trigger-outputs-expression-token.png)

      Se guardar a aplicação lógica, navegar para longe do designer e regressar ao designer, o símbolo mostra o nome do parâmetro que especificou, por exemplo:

      ![Expressão resolvida para nome de parâmetro](./media/logic-apps-http-endpoint/resolved-expression-parameter-token.png)

      Em vista de código, a propriedade **do Corpo** aparece na definição da ação resposta da seguinte forma:

      `"body": "@{triggerOutputs()['queries']['parameter-name']}",`

      Por exemplo, suponha que quer passar um valor para um parâmetro chamado `postalCode` . A propriedade **do Corpo** especifica a corda, com um espaço `Postal Code: ` de trailing, seguida da expressão correspondente:

      ![Adicione a expressão "triggerOutputs()" para desencadear](./media/logic-apps-http-endpoint/trigger-outputs-expression-postal-code.png)

1. Para testar o seu ponto final calivel, copie o URL de chamada do gatilho do Pedido e cole o URL noutra janela do navegador. No URL, adicione o nome e o valor do parâmetro seguindo o ponto de interrogação `?` () ao URL no seguinte formato e prima Enter.

   `...?{parameter-name=parameter-value}&api-version=2016-10-01...`

   `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?{parameter-name=parameter-value}&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   O navegador devolve uma resposta com este texto:`Postal Code: 123456`

   ![Resposta do envio de pedido para URL de callback](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

1. Para colocar o nome e o valor do parâmetro numa posição diferente dentro do URL, certifique-se de utilizar a ampersand ( `&` ) como prefixo, por exemplo:

   `...?api-version=2016-10-01&{parameter-name=parameter-value}&...`

   Este exemplo mostra o URL de chamada com o nome e valor do parâmetro da amostra `postalCode=123456` em diferentes posições dentro do URL:

   * 1ª posição:`https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?postalCode=123456&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   * Segunda posição:`https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?api-version=2016-10-01&postalCode=123456&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

<a name="relative-path"></a>

### <a name="accept-values-through-a-relative-path"></a>Aceitar valores através de um caminho relativo

1. No gatilho Pedido, abra a **lista adicionar novo parâmetro** e selecione caminho **relativo,** que adiciona esta propriedade ao gatilho.

   ![Adicione a propriedade "Caminho Relativo" para desencadear](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. Na propriedade **relativa,** especifique o caminho relativo para o parâmetro no seu esquema JSON que pretende que o seu URL aceite, por exemplo, `/address/{postalCode}` .

   ![Especificar o caminho relativo para o parâmetro](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Sob o gatilho Solicitar, adicione a ação onde pretende utilizar o valor do parâmetro. Para este exemplo, adicione a ação **Resposta.**

   1. Sob o gatilho pedido, selecione **Novo passo**Adicione  >  **uma ação**.

   1. Em **'Escolha uma ação**' na caixa de pesquisa, introduza como `response` filtro. Na lista de ações, selecione a ação **Resposta.**

1. Na propriedade do **Corpo** da Ação resposta, inclua o símbolo que representa o parâmetro que especificou no caminho relativo do seu gatilho.

   Por exemplo, suponha que quer que a ação de resposta `Postal Code: {postalCode}` regresse.

   1. Na propriedade **Do Corpo,** entre `Postal Code: ` com um espaço de trailing. Mantenha o cursor dentro da caixa de edição para que a lista de conteúdos dinâmicos permaneça aberta.

   1. Na lista de conteúdos dinâmicos, a partir da secção **Quando um pedido HTTP é recebido,** selecione o token **postalCode.**

      ![Adicione o parâmetro especificado ao corpo de resposta](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

      A propriedade **Body** agora inclui o parâmetro selecionado:

      ![Corpo de resposta de exemplo com parâmetro](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Guarde a aplicação lógica.

   No gatilho do Pedido, o URL de retorno de chamada é atualizado e agora inclui o caminho relativo, por exemplo:

   `https://prod-07.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

1. Para testar o seu ponto final calivel, copie o URL de callback atualizado do gatilho do Pedido, cole o URL noutra janela do navegador, substitua `{postalCode}` no URL com , e prima `123456` Enter.

   O navegador devolve uma resposta com este texto:`Postal Code: 123456`

   ![Resposta do envio de pedido para URL de callback](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

## <a name="call-logic-app-through-endpoint-url"></a>App lógica de chamada através de URL de ponto final

Depois de criar o ponto final, pode acionar a aplicação lógica enviando um pedido HTTPS `POST` para o URL completo do ponto final. As aplicações lógicas têm suporte incorporado para pontos finais de acesso direto.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Fichas geradas a partir de esquema

Quando fornece um esquema JSON no gatilho request, o Logic App Designer gera fichas para as propriedades nesse esquema. Em seguida, pode utilizar esses tokens para passar dados através do fluxo de trabalho da sua aplicação lógica.

Por exemplo, se adicionar mais propriedades, `"suite"` como, ao seu esquema JSON, os tokens para essas propriedades estão disponíveis para que possa usar nos passos posteriores para a sua aplicação lógica. Aqui está o completo esquema jSON:

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

## <a name="create-nested-logic-apps"></a>Criar aplicações lógicas aninhadas

Pode nidificar fluxos de trabalho na sua aplicação lógica adicionando outras aplicações lógicas que podem receber pedidos. Para incluir estas aplicações lógicas, siga estes passos:

1. Sob o passo onde você quer chamar outra aplicação lógica, selecione **Novo passo**Adicione  >  **uma ação**.

1. Em **'Escolha uma ação**', selecione **Incorporado**' . Na caixa de pesquisa, introduza `logic apps` como filtro. Na lista de ações, selecione Escolha um fluxo de trabalho de **Aplicações Lógicas**.

   ![App lógica nest dentro da aplicação lógica atual](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   O designer mostra as aplicações lógicas elegíveis para selecionar.

1. Selecione a aplicação lógica para ligar a partir da sua aplicação lógica atual.

   ![Selecione app lógica para ligar da aplicação lógica atual](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Conteúdo de referência a partir de um pedido de entrada

Se o tipo de conteúdo do pedido de entrada `application/json` for, pode fazer referência às propriedades no pedido de entrada. Caso contrário, este conteúdo é tratado como uma única unidade binária que pode passar para outras APIs. Para fazer referência a este conteúdo dentro do fluxo de trabalho da sua aplicação lógica, é necessário converter primeiro esse conteúdo.

Por exemplo, se estiver a passar conteúdo que tenha `application/xml` tipo, pode usar a [ `@xpath()` expressão](../logic-apps/workflow-definition-language-functions-reference.md#xpath) para realizar uma extração XPath, ou usar a [ `@json()` expressão](../logic-apps/workflow-definition-language-functions-reference.md#json) para converter xML para JSON. Saiba mais sobre trabalhar com tipos de [conteúdo](../logic-apps/logic-apps-content-type.md)suportados.

Para obter a saída a partir de um pedido de entrada, pode usar a [ `@triggerOutputs` expressão](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Por exemplo, suponha que tem saída que se parece com este exemplo:

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

## <a name="respond-to-requests"></a>Responder a pedidos

Por vezes, pretende responder a certos pedidos que desencadeiam a sua aplicação lógica devolvendo conteúdo ao chamador. Para construir o código de estado, cabeçalho e corpo para a sua resposta, use a ação Resposta. Esta ação pode aparecer em qualquer lugar da sua aplicação lógica, não apenas no final do seu fluxo de trabalho. Se a sua aplicação lógica não incluir uma ação de Resposta, o ponto final responde *imediatamente* com o estatuto aceite em **202.**

Para que o chamador original obtenha com sucesso a resposta, todos os passos necessários para a resposta devem terminar dentro do prazo de [pedido,](./logic-apps-limits-and-config.md) a menos que a aplicação lógica desencadeada seja chamada de uma aplicação lógica aninhada. Se nenhuma resposta for devolvida dentro deste limite, o pedido de entrada é de saem e recebe a resposta de timeout do **Cliente 408.**

Para aplicações lógicas aninhadas, a aplicação lógica dos pais continua à espera de uma resposta até que todos os passos estejam concluídos, independentemente do tempo necessário.

### <a name="construct-the-response"></a>Construir a resposta

No corpo de resposta, pode incluir vários cabeçalhos e qualquer tipo de conteúdo. Por exemplo, o cabeçalho desta resposta especifica que o tipo de conteúdo da resposta é `application/json` e que o corpo contém valores para o e `town` `postalCode` propriedades, com base no esquema JSON descrito anteriormente neste tópico para o gatilho do Pedido.

![Fornecer conteúdo de resposta para a ação de resposta HTTPS](./media/logic-apps-http-endpoint/content-for-response-action.png)

As respostas têm estas propriedades:

| Propriedade (Exibição) | Propriedade (JSON) | Descrição |
|--------------------|-----------------|-------------|
| **Código de Estado** | `statusCode` | O código de estado HTTPS a utilizar na resposta para o pedido de entrada. Este código pode ser qualquer código de estado válido que comece com 2xx, 4xx ou 5xx. No entanto, não são permitidos códigos de estado 3xx. |
| **Cabeçalhos** | `headers` | Um ou mais cabeçalhos para incluir na resposta |
| **Corpo** | `body` | Um objeto corporal que pode ser uma corda, um objeto JSON, ou mesmo conteúdo binário referenciado a partir de um passo anterior |
||||

Para ver a definição JSON para a ação Resposta e a definição completa jSON da sua aplicação lógica, na barra de ferramentas Logic App Designer, selecione **a visão de Código**.

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

#### <a name="q-what-about-url-security"></a>P: E a segurança url?

**R:** O Azure gera de forma segura URLs de callback de aplicações lógicas utilizando a Assinatura de [Acesso Partilhado (SAS)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature). Esta assinatura passa como parâmetro de consulta e deve ser validada antes que a sua aplicação lógica possa ser executada. O Azure gera a assinatura usando uma combinação única de uma chave secreta por aplicação lógica, o nome do gatilho e a operação que é realizada. Portanto, a menos que alguém tenha acesso à chave de aplicações lógica secreta, não pode gerar uma assinatura válida.

> [!IMPORTANT]
> Para a produção e sistemas de segurança mais elevados, aconselhamos veementemente não ligar diretamente para a sua aplicação lógica a partir do navegador por estas razões:
>
> * A chave de acesso partilhada aparece no URL.
> * Não é possível gerir as políticas de conteúdos de segurança devido a domínios partilhados entre os clientes da Azure Logic Apps.

#### <a name="q-can-i-configure-callable-endpoints-further"></a>P: Posso configurar ainda mais os pontos finais reputáveis?

**R:** Sim, os pontos finais HTTPS suportam uma configuração mais avançada através da [Gestão API Azure.](../api-management/api-management-key-concepts.md) Este serviço também oferece a capacidade para você gerir consistentemente todas as suas APIs, incluindo aplicações lógicas, configurar nomes de domínio personalizados, usar mais métodos de autenticação, e muito mais, por exemplo:

* [Alterar o método de pedido](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Alterar os segmentos de URL do pedido](../api-management/api-management-transformation-policies.md#RewriteURL)
* Instale os seus domínios de Gestão API no [portal Azure](https://portal.azure.com/)
* Criar política para verificar se há autenticação básica

## <a name="next-steps"></a>Próximos passos

* [Receba e responda às chamadas https recebidas utilizando aplicações da Azure Logic](../connectors/connectors-native-reqres.md)
