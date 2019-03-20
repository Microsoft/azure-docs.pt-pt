---
title: Chamar, acionar ou aninhar fluxos de trabalho com pontos de extremidade HTTP - Azure Logic Apps | Documentos da Microsoft
description: Configurar pontos finais HTTP para chamar, acionar, ou aninhar fluxos de trabalho para o Azure Logic Apps
services: logic-apps
keywords: fluxos de trabalho, pontos de extremidade HTTP
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: klam; LADocs
ms.openlocfilehash: c58b39f8e2d49eeb3e64c7ffce1d34d7a7b7b780
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904376"
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>Chamar, acionar, ou aninhar fluxos de trabalho com pontos de extremidade HTTP no logic apps

Nativamente podem expor pontos de extremidade HTTP síncronos como disparadores sobre as aplicações lógicas, para que possa acionar ou chamar as aplicações lógicas através de um URL. Também pode aninhar fluxos de trabalho nas suas aplicações lógicas através de um padrão de pontos finais disponíveis.

Para criar pontos de extremidade HTTP, pode adicionar estes acionadores, para que as aplicações lógicas podem receber pedidos de entrada:

* [Pedido](../connectors/connectors-native-reqres.md)

* [Webhook de ligação de API](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [Webhook de HTTP](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Embora estes exemplos utilizam o **pedir** acionador, pode usar qualquer um dos acionadores HTTP listados e todos os princípios aplicam-se idêntico para os outros tipos de Acionador.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Configurar um ponto de final HTTP para a aplicação lógica

Para criar um ponto final HTTP, adicione um acionador que pode receber pedidos recebidos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com "portal do Azure"). Aceda à sua aplicação lógica e abrir o Estruturador da aplicação lógica.

2. Adicione um acionador que permite que a sua aplicação lógica recebe as solicitações de entrada. Por exemplo, adicione a **pedir** acionador à sua aplicação lógica.

3.  Sob **pedir esquema JSON do corpo**, opcionalmente, pode introduzir um esquema JSON para o payload (dados) que pretende que o acionador para receber.

    O designer usa esse esquema para gerar tokens que a aplicação lógica pode usar para consumir, analisar e transmitir dados a partir do acionador por meio de seu fluxo de trabalho. 
    Saiba mais sobre [tokens gerados a partir de esquemas JSON](#generated-tokens).

    Neste exemplo, introduza o esquema mostrado ao designer:

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

    ![Adicione a ação de pedido][1]

    > [!TIP]
    > 
    > Pode gerar um esquema para um payload JSON de exemplo a partir de uma ferramenta como o [jsonschema.net](https://jsonschema.net/), ou no **pedir** acionador ao escolher **utilizar payload de amostra para gerar esquema**. 
    > Introduzir o payload de exemplo e escolha **feito**.

    Por exemplo, este payload de exemplo:

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    gera esse esquema:

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

4.  Guarde a aplicação lógica. Sob **HTTP POST para este URL**, agora deve encontrar um URL de chamada de retorno gerado, como neste exemplo:

    ![URL de chamada de retorno gerado para o ponto final](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Este URL contém uma chave de assinatura de acesso partilhado (SAS) os parâmetros de consulta que são utilizados para autenticação. 
    Também pode obter o URL de ponto final HTTP da sua descrição geral da aplicação lógica no portal do Azure. Sob **histórico de Acionadores**, selecione o acionador:

    ![Obter URL de ponto final HTTP a partir do portal do Azure][2]

    Em alternativa, pode obter o URL ao fazer esta chamada:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Alterar o método HTTP para o acionador

Por predefinição, o **pedido** acionador espera um pedido HTTP POST, mas pode usar um método diferente de HTTP. 

> [!NOTE]
> Pode especificar o tipo de apenas um método.

1. No seu **pedir** acionador, escolha **Mostrar opções avançadas**.

2. Abra o **método** lista. Para este exemplo, selecione **obter** para que pode testar URL seu ponto final HTTP mais tarde.

    > [!NOTE]
    > Pode selecionar qualquer outro método HTTP, ou especificar um método personalizado para a sua própria aplicação lógica.

    ![Alterar o método HTTP](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Aceitar parâmetros por meio de seu URL de ponto final HTTP

Quando pretender que o seu URL de ponto final HTTP para aceitar parâmetros, personalize o caminho relativo do acionador.

1. No seu **pedir** acionador, escolha **Mostrar opções avançadas**. 

2. Sob **método**, especifique o método HTTP que pretende que o seu pedido para utilizar. Para este exemplo, selecione o **obter** método, se ainda não o fez, para que pode testar o URL do seu ponto final HTTP.

      > [!NOTE]
      > Quando especificar um caminho relativo para o acionador, tem de especificar também explicitamente um método HTTP para o acionador.

3. Sob **caminho relativo**, especifique o caminho relativo para o parâmetro que deve aceitar o URL, por exemplo, `customers/{customerID}`.

    ![Especifique o método HTTP e o caminho relativo para o parâmetro](./media/logic-apps-http-endpoint/relativeurl.png)

4. Para utilizar o parâmetro, adicione uma **resposta** ação à sua aplicação lógica. (No seu acionador, escolha **novo passo** > **adicionar uma ação** > **resposta**) 

5. Em sua resposta **corpo**, incluem o token para o parâmetro que especificou no caminho relativo do seu acionador.

    Por exemplo, para retornar `Hello {customerID}`, atualize a sua resposta **corpo** com `Hello {customerID token}`. 
    Lista de conteúdo dinâmico deve aparecer e mostrar o `customerID` token para o selecionar.

    ![Adicionar parâmetros ao corpo da resposta](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    Sua **corpo** deve ter um aspeto semelhante a este exemplo:

    ![Corpo de resposta com o parâmetro](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Guarde a aplicação lógica. 

    O URL de ponto final HTTP agora inclui o caminho relativo, por exemplo: 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. Para testar o seu ponto final de HTTP, copie e cole o URL atualizado na outra janela do browser, mas substitua `{customerID}` com `123456`, e prima Enter.

    O browser deve mostrar este texto: 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Tokens gerados a partir de esquemas JSON para a sua aplicação lógica

Quando fornecer um esquema JSON no seu **pedir** acionador, o Estruturador da aplicação lógica gera tokens para propriedades nesse esquema. Em seguida, pode usar esses tokens para transmitir dados através de seu fluxo de trabalho de aplicação lógica.

Neste exemplo, se adicionar o `title` e `name` propriedades para o seu esquema JSON, os tokens estão agora disponíveis para utilizar em passos posteriores do fluxo de trabalho. 

Este é o esquema JSON completo:

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

## <a name="create-nested-workflows-for-logic-apps"></a>Criar fluxos de trabalho aninhados para aplicações lógicas

Pode aninhar fluxos de trabalho na sua aplicação lógica, adicionando outras aplicações lógicas que podem receber pedidos. Para incluir estas aplicações lógicas, adicione a **do Azure Logic Apps - escolher um fluxo de trabalho do Logic Apps** ação para o acionador. Em seguida, pode selecionar a partir das aplicações lógicas elegíveis.

![Adicionar outra aplicação de lógica](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Chamar ou acionar aplicações lógicas através de pontos finais HTTP

Depois de criar o ponto final HTTP, pode acionar a sua aplicação lógica através de um `POST` método para o URL completo. Aplicações lógicas têm suporte incorporado para pontos finais de acesso direto.

> [!NOTE] 
> Para executar manualmente uma aplicação lógica em qualquer altura, na barra de ferramentas da Estruturador da aplicação lógica ou a vista de código de aplicação lógica, escolha **executar**.

## <a name="reference-content-from-an-incoming-request"></a>Conteúdo de referência de uma solicitação de entrada

Se de tipo o conteúdo for `application/json`, pode referenciar propriedades da solicitação recebida. Caso contrário, o conteúdo é tratado como uma unidade de binária única que pode passar para outras APIs. Para fazer referência a este conteúdo dentro do fluxo de trabalho, tem de converter esse conteúdo. Por exemplo, se passar `application/xml` conteúdo, pode utilizar `@xpath()` para uma extração de XPath, ou `@json()` para a conversão XML para JSON. Saiba mais sobre [trabalhar com tipos de conteúdo](../logic-apps/logic-apps-content-type.md).

Para obter o resultado de uma solicitação de entrada, pode usar o `@triggerOutputs()` função. A saída pode ser semelhante a este exemplo:

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

Para aceder a `body` propriedade especificamente, pode usar o `@triggerBody()` atalho. 

## <a name="respond-to-requests"></a>Responder a pedidos

Pode querer responder a pedidos de determinados que iniciar uma aplicação lógica, retornando o conteúdo para o chamador. Para construir o código de estado, o cabeçalho e o corpo da sua resposta, pode utilizar o **resposta** ação. Esta ação pode aparecer em qualquer local na sua aplicação lógica, não apenas no final do seu fluxo de trabalho.

> [!NOTE] 
> Se a sua aplicação lógica não inclui um **resposta**, o ponto de extremidade HTTP responde *imediatamente* com um **aceite 202** estado. Além disso, para a solicitação original obter a resposta, todas as etapas necessárias para a resposta deverá ser concluída no [limite de tempo limite do pedido](./logic-apps-limits-and-config.md) , a menos que chamar o fluxo de trabalho como uma aplicação lógica aninhada. Se nenhuma resposta acontecer dentro deste limite, a solicitação de entrada exceder o tempo limite e recebe a resposta HTTP **408 tempo limite do cliente**. Para aplicações de lógica aninhada, a aplicação de lógica principal continua a aguardar por uma resposta até concluídos, independentemente de quanto tempo é necessário.

### <a name="construct-the-response"></a>Construir a resposta

Pode incluir mais de um cabeçalho e de qualquer tipo de conteúdo no corpo da resposta. A resposta de exemplo, o cabeçalho Especifica que a resposta tem o tipo de conteúdo `application/json`. e o corpo contém `title` e `name`, com base no esquema JSON atualizado anteriormente para o **pedir** acionador.

![Ação de resposta de HTTP][3]

As respostas têm estas propriedades:

| Propriedade | Descrição |
| --- | --- |
| statusCode |Especifica o código de estado HTTP para responder à solicitação de entrada. Esse código pode ser qualquer código de estado válido que comece com 2xx, 4xx ou 5xx. No entanto, os códigos de estado de 3xx não são permitidos. |
| Cabeçalhos |Define a qualquer número de cabeçalhos para incluir na resposta. |
| corpo |Especifica um objeto de corpo que pode ser uma cadeia de caracteres, um objeto JSON ou até mesmo binário conteúdo referenciado a partir de um passo anterior. |

Eis o que o esquema JSON aparência agora para o **resposta** ação:

``` json
"Response": {
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
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> Para ver a definição de JSON completa para a sua aplicação lógica no Estruturador da aplicação lógica, escolha **exibição de código**.

## <a name="q--a"></a>P&R

#### <a name="q-what-about-url-security"></a>P: E quanto a segurança de URL?

R: Azure gera em segurança os URLs de retorno de chamada de aplicação lógica com uma assinatura de acesso partilhado (SAS). Essa assinatura passa por meio de como um parâmetro de consulta e têm de ser validada antes de pode acionar a sua aplicação lógica. Azure gera a assinatura usando uma combinação única de uma chave secreta por aplicação lógica, o nome do acionador e a operação que é executada. Portanto, a menos que alguém tem acesso para a chave da aplicação lógica secreta, não é possível gerar uma assinatura válida.

   > [!IMPORTANT]
   > Para a produção e sistemas de segurança, recomendamos vivamente em relação a sua aplicação lógica ao chamar diretamente a partir do navegador porque:
   > 
   > * A chave de acesso partilhado é apresentado no URL.
   > * Não é possível gerir políticas de conteúdo seguras devido a domínios compartilhadas entre os clientes de aplicação lógica.

#### <a name="q-can-i-configure-http-endpoints-further"></a>P: Pode configurar pontos finais HTTP ainda mais?

R: Sim, pontos de extremidade HTTP suportam configurações mais avançadas através de [ **gestão de API**](../api-management/api-management-key-concepts.md). Este serviço também oferece a capacidade para gerir todas as suas APIs, incluindo aplicações lógicas consistentemente, configurar nomes de domínio personalizado, utilize os métodos de autenticação mais e muito mais, por exemplo:

* [O método de pedido de alteração](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Alterar os segmentos de URL do pedido](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Configurar seus domínios de gestão de API no [portal do Azure](https://portal.azure.com/ "portal do Azure")
* Configurar a política para verificar a existência de autenticação básica

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>P: O que foi alterado quando o esquema migrada a partir da pré-visualização do dia 1 de Dezembro de 2014?

R: Aqui está um resumo sobre estas alterações:

| Pré-visualização de 1 de Dezembro de 2014 | 1 de Junho de 2016 |
| --- | --- |
| Clique em **serviço de escuta de HTTP** aplicação API |Clique em **acionador Manual** (nenhuma aplicação de API obrigatório) |
| Definição de serviço de escuta de HTTP "*envia automaticamente a resposta*" |Qualquer um incluem uma **resposta** ação ou não na definição do fluxo de trabalho |
| Configurar autenticação básica ou do OAuth |através da gestão de API |
| Configurar o método HTTP |Sob **Mostrar opções avançadas**, escolha um método de HTTP |
| Configurar o caminho relativo |Sob **Mostrar opções avançadas**, adicione um caminho relativo |
| O corpo de entrada através de referência `@triggerOutputs().body.Content` |Referência através de `@triggerOutputs().body` |
| **Enviar resposta HTTP** ação no serviço de escuta de HTTP |Clique em **responder ao pedido HTTP** (nenhuma aplicação de API obrigatório) |

## <a name="get-help"></a>Obter ajuda

Para fazer perguntas, responder a perguntas e ver o que os outros utilizadores do Azure Logic Apps estão a fazer, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar o Azure Logic Apps e os conectores, vote ou submeta ideais no [site de comentários dos utilizadores do Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* [Criar definições de aplicação lógica](./logic-apps-author-definitions.md)
* [Lidar com erros e exceções](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png