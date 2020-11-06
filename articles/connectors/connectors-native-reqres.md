---
title: Receber e responder a chamadas utilizando HTTPS
description: Lidar com pedidos HTTPS de entrada de serviços externos utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: c0e8743d78c8eeafb5bdeb6ade783d5e75991f91
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94330993"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Receber e responder a pedidos HTTPS de entrada em Azure Logic Apps

Com [as Apps Azure Logic](../logic-apps/logic-apps-overview.md) e a ação de desencadeamento e resposta do Pedido incorporado, pode criar tarefas e fluxos de trabalho automatizados que podem receber pedidos de entrada através do HTTPS. Para enviar pedidos de saída, utilize o gatilho HTTP incorporado [ou a ação HTTP](../connectors/connectors-native-http.md).

Por exemplo, pode ter a sua aplicação lógica:

* Receber e responder a um pedido HTTPS de dados numa base de dados no local.

* Desencadeie um fluxo de trabalho quando um evento externo de webhook acontece.

* Receber e responder a uma chamada HTTPS de outra aplicação lógica.

Este artigo mostra como utilizar a ação de detonador e resposta do Pedido para que a sua aplicação lógica possa receber e responder a chamadas de entrada.

Para mais informações sobre segurança, autorização e encriptação para chamadas de entrada para a sua aplicação lógica, como [a Transport Layer Security (TLS),](https://en.wikipedia.org/wiki/Transport_Layer_Security)anteriormente conhecida como Secure Sockets Layer (SSL), [Azure Ative Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml), expondo a sua aplicação lógica com a Azure API Management, ou restringindo os endereços IP que originam chamadas de entrada, ver acesso seguro e dados - Acesso a chamadas de [entrada para pedidos baseados em gatilhos](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma subscrição, pode [inscrever-se numa conta Azure gratuita.](https://azure.microsoft.com/free/)

* Conhecimento básico sobre [como criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Adicionar gatilho de pedido

Este gatilho incorporado cria um ponto final manualmente chamado que *só* pode lidar com pedidos de entrada em HTTPS. Quando um chamador envia um pedido para este ponto final, o [Pedido dispara](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e executa a aplicação lógica. Para obter mais informações sobre como chamar este gatilho, consulte [fluxos de trabalho de Chamada, Gatilho ou ninho com pontos finais HTTPS em Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

A sua aplicação lógica mantém um pedido de entrada aberto apenas por um [tempo limitado](../logic-apps/logic-apps-limits-and-config.md#request-limits). Assumindo que a sua aplicação lógica inclui uma [ação De Resposta](#add-response), se a sua aplicação lógica não enviar uma resposta de volta ao chamador após o passar deste tempo, a sua aplicação lógica devolve um `504 GATEWAY TIMEOUT` estado ao chamador. Se a sua aplicação lógica não incluir uma ação de Resposta, a sua aplicação lógica devolve imediatamente um `202 ACCEPTED` estado ao chamador.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Criar uma aplicação lógica em branco.

1. Depois de o Logic App Designer abrir, na caixa de pesquisa, insira `http request` como filtro. A partir da lista de gatilhos, selecione o **detonador de um pedido HTTP.**

   ![Selecione Acionar o gatilho do pedido](./media/connectors-native-reqres/select-request-trigger.png)

   O gatilho do Pedido mostra estas propriedades:

   ![Pedido de gatilho](./media/connectors-native-reqres/request-trigger.png)

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {nenhum} | Sim | O URL de ponto final que é gerado depois de salvar a aplicação lógica e é usado para chamar a sua app lógica |
   | **Pedido corpo JSON Schema** | `schema` | Não | O esquema JSON que descreve as propriedades e valores no corpo de pedido de entrada |
   |||||

1. Na caixa **de esquema JSON do Corpo de Pedido,** insira opcionalmente um esquema JSON que descreve o corpo no pedido de entrada, por exemplo:

   ![Exemplo JSON schema](./media/connectors-native-reqres/provide-json-schema.png)

   O designer usa este esquema para gerar fichas para as propriedades no pedido. Dessa forma, a sua aplicação lógica pode analisar, consumir e transmitir dados do pedido através do gatilho para o seu fluxo de trabalho.

   Aqui está o esquema da amostra:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Quando introduz um esquema JSON, o designer mostra um lembrete para incluir o `Content-Type` cabeçalho no seu pedido e definir o valor do cabeçalho para `application/json` . Para obter mais informações, consulte [os tipos de conteúdo handle](../logic-apps/logic-apps-content-type.md).

   ![Lembrete para incluir cabeçalho "Tipo de conteúdo"](./media/connectors-native-reqres/include-content-type.png)

   Aqui está o que este cabeçalho parece no formato JSON:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Para gerar um esquema JSON que se baseia na carga útil esperada (dados), pode utilizar uma ferramenta como [JSONSchema.net,](https://jsonschema.net)ou pode seguir estes passos:

   1. No gatilho 'Pedido', **selecione Utilize a carga útil da amostra para gerar esquema**.

      ![Screenshot com "Use a carga útil da amostra para gerar esquema" selecionado](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Introduza a carga útil da amostra e selecione **Feito**.

      ![Introduza a carga útil da amostra para gerar esquema](./media/connectors-native-reqres/enter-payload.png)

      Aqui está a carga útil da amostra:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": {
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Para verificar se a chamada de entrada tem um corpo de pedido que corresponde ao seu esquema especificado, siga estes passos:

   1. Na barra de título do pedido, selecione o botão elipses **(...** ).

   1. Nas definições do gatilho, ligue **a Validação de Schema** e selecione **'Fazer'**

      Se o corpo de pedido da chamada de entrada não corresponder ao seu esquema, o gatilho retorna um `HTTP 400 Bad Request` erro.

1. Para especificar propriedades adicionais, abra a nova lista **de parâmetros** e selecione os parâmetros que pretende adicionar.

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **Método** | `method` | Não | O método que o pedido de entrada deve usar para ligar para a aplicação lógica |
   | **Caminho relativo** | `relativePath` | Não | O caminho relativo para o parâmetro que o URL de ponta da aplicação lógica pode aceitar |
   |||||

   Este exemplo adiciona a propriedade **Method:**

   ![Adicionar parâmetro de método](./media/connectors-native-reqres/add-parameters.png)

   A propriedade **Method** aparece no gatilho para que possa selecionar um método da lista.

   ![Selecione método](./media/connectors-native-reqres/select-method.png)

1. Agora, adicione outra ação como o próximo passo no seu fluxo de trabalho. Sob o gatilho, selecione **o próximo passo** para que possa encontrar a ação que pretende adicionar.

   Por exemplo, pode responder ao pedido [adicionando uma ação Resposta](#add-response), que pode utilizar para devolver uma resposta personalizada e é descrita mais tarde neste tópico.

   A sua aplicação lógica mantém o pedido de entrada aberto apenas por um [tempo limitado](../logic-apps/logic-apps-limits-and-config.md#request-limits). Assumindo que o fluxo de trabalho da sua aplicação lógica inclui uma ação de Resposta, se a aplicação lógica não devolver uma resposta após o passar deste tempo, a sua aplicação lógica devolve a `504 GATEWAY TIMEOUT` ao chamador. Caso contrário, se a sua aplicação lógica não incluir uma ação de Resposta, a sua aplicação lógica devolve imediatamente uma `202 ACCEPTED` resposta ao chamador.

1. Quando terminar, guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

   Este passo gera o URL para usar para o envio do pedido que desencadeia a aplicação lógica. Para copiar este URL, selecione o ícone de cópia ao lado do URL.

   ![URL para usar o gatilho da sua aplicação lógica](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > Se pretender incluir o símbolo de haxixe ou libra **#** () no URI ao escoar uma chamada para o gatilho Pedido, utilize esta versão codificada: `%25%23`

1. Para ativar a sua aplicação lógica, envie um HTTP POST para o URL gerado.

   Por exemplo, pode utilizar uma ferramenta como [o Carteiro](https://www.getpostman.com/) para enviar o HTTP POST. Para obter mais informações sobre a definição de JSON subjacente do gatilho e como chamar este gatilho, consulte estes tópicos, [Solicite tipo de gatilho](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e [Chamada, gatilho ou fluxos de trabalho de ninho com pontos finais HTTP em Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

Para mais informações sobre segurança, autorização e encriptação para chamadas de entrada para a sua aplicação lógica, como [a Transport Layer Security (TLS),](https://en.wikipedia.org/wiki/Transport_Layer_Security)anteriormente conhecida como Secure Sockets Layer (SSL), [Azure Ative Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml), expondo a sua aplicação lógica com a Azure API Management, ou restringindo os endereços IP que originam chamadas de entrada, ver acesso seguro e dados - Acesso a chamadas de [entrada para pedidos baseados em gatilhos](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="trigger-outputs"></a>Saídas de gatilho

Aqui está mais informações sobre as saídas do gatilho do Pedido:

| Nome da propriedade JSON | Tipo de dados | Descrição |
|--------------------|-----------|-------------|
| `headers` | Objeto | Um objeto JSON que descreve os cabeçalhos do pedido |
| `body` | Objeto | Um objeto JSON que descreve o conteúdo do corpo a partir do pedido |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Adicionar uma ação de resposta

Quando utilizar o gatilho 'Pedido' para lidar com pedidos de entrada, pode modelar a resposta e enviar os resultados da carga útil de volta para o chamador utilizando a ação [Resposta](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)incorporada . Só pode utilizar a *ação* Resposta com o gatilho 'Pedido'. Esta combinação com a ação Detonador e Resposta do Pedido cria o [padrão de resposta de pedido.](https://en.wikipedia.org/wiki/Request%E2%80%93response) Com exceção dos laços forecach e dos laços e dos ramos paralelos, pode adicionar a ação Resposta em qualquer lugar do seu fluxo de trabalho.

> [!IMPORTANT]
> Se uma ação de resposta incluir estes cabeçalhos, as Aplicações Lógicas removem estes cabeçalhos da mensagem de resposta gerada sem mostrar qualquer aviso ou erro:
>
> * `Allow`
> * `Content-*` com estas exceções: `Content-Disposition` `Content-Encoding` , e `Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Embora as Aplicações Lógicas não o impeçam de guardar aplicações lógicas que tenham uma ação de Resposta com estes cabeçalhos, as Aplicações Lógicas ignoram estes cabeçalhos.

1. No Logic App Designer, sob o passo em que pretende adicionar uma ação de Resposta, selecione **Novo passo**.

   Por exemplo, utilizando o gatilho do Pedido anteriormente:

   ![Adicionar novo passo](./media/connectors-native-reqres/add-response.png)

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre esses degraus. Selecione o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Em **Escolha uma ação,** na caixa de pesquisa, introduza `response` como filtro e selecione a ação **Resposta.**

   ![Selecione a ação Resposta](./media/connectors-native-reqres/select-response-action.png)

   O gatilho request é colapsado neste exemplo para a simplicidade.

1. Adicione todos os valores necessários para a mensagem de resposta.

   Em alguns campos, clicar dentro das suas caixas abre a lista de conteúdos dinâmicos. Em seguida, pode selecionar fichas que representam saídas disponíveis de etapas anteriores no fluxo de trabalho. As propriedades do esquema especificado no exemplo anterior aparecem agora na lista de conteúdos dinâmicos.

   Por exemplo, para a caixa **headers,** inclua `Content-Type` como o nome chave, e defina o valor chave para `application/json` o mencionado anteriormente neste tópico. Para a caixa **Body,** pode selecionar a saída do corpo do gatilho a partir da lista de conteúdos dinâmicos.

   ![Detalhes da ação de resposta](./media/connectors-native-reqres/response-details.png)

   Para ver os cabeçalhos no formato JSON, **selecione Switch para a vista de texto**.

   ![Cabeçalhos - Mudar para a vista de texto](./media/connectors-native-reqres/switch-to-text-view.png)

   Aqui está mais informações sobre as propriedades que pode definir na ação Resposta.

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **Código de Estado** | `statusCode` | Sim | O código de estado para devolver na resposta |
   | **Cabeçalhos** | `headers` | Não | Um objeto JSON que descreve um ou mais cabeçalhos para incluir na resposta |
   | **Corpo** | `body` | Não | O corpo de resposta |
   |||||

1. Para especificar propriedades adicionais, como um esquema JSON para o corpo de resposta, abra a nova lista **de parâmetros add** e selecione os parâmetros que pretende adicionar.

1. Quando terminar, guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

## <a name="next-steps"></a>Passos seguintes

* [Acesso seguro e dados - Acesso a chamadas de entrada para gatilhos baseados em pedidos](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Conectores para as Logic Apps](../connectors/apis-list.md)
