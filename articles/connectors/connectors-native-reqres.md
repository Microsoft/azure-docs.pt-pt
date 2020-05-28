---
title: Receber e responder a chamadas utilizando HTTPS
description: Lidar com pedidos HTTPS de entrada de serviços externos utilizando aplicações da Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
tags: connectors
ms.openlocfilehash: 1e1a7f2e82ba2e90a641a6559062348f8d4d3aea
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142457"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Receber e responder a pedidos de entrada em HTTPS em Aplicações Lógicas Azure

Com [as Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e a ação de gatilho e resposta de pedido incorporado, pode criar tarefas automatizadas e fluxos de trabalho que recebem e respondem aos pedidos https recebidos. Por exemplo, pode ter a sua aplicação lógica:

* Receba e responda a um pedido https de dados numa base de dados no local.
* Desencadeie um fluxo de trabalho quando um evento externo de webhook acontece.
* Receba e responda a uma chamada HTTPS de outra aplicação lógica.

O gatilho do Pedido suporta a Autenticação Aberta do [Diretório Ativo Azure](../active-directory/develop/about-microsoft-identity-platform.md) (Azure AD OAuth) para autorizar chamadas de entrada para a sua aplicação lógica. Para obter mais informações sobre a ativação desta autenticação, consulte [o acesso seguro e os dados nas Aplicações lógicas do Azure - Enable Azure AD AD AUth autenticação](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth).

> [!NOTE]
> O gatilho do Pedido suporta *apenas* a Segurança da Camada de Transporte (TLS) 1.2 para chamadas recebidas. Chamadas de saída suportam TLS 1.0, 1.1 e 1.2. Para mais informações, consulte [A resolução do problema TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).
>
> Se tiver erros de aperto de mão TLS, certifique-se de que utiliza TLS 1.2. 
> Para chamadas recebidas, aqui estão as suítes de cifra suportadas:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [inscrever-se para uma conta Azure gratuita.](https://azure.microsoft.com/free/)

* Conhecimento básico sobre [aplicações lógicas.](../logic-apps/logic-apps-overview.md) Se você é novo em aplicativos lógicos, aprenda [a criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Adicionar gatilho de pedido

Este gatilho incorporado cria um ponto final HTTPS manualmente calivel que *só* pode receber pedidos HTTPS recebidos. Quando este evento acontece, o gatilho dispara e executa a aplicação lógica. Para obter mais informações sobre a definição json subjacente do gatilho e como chamar este gatilho, consulte o tipo de [gatilho do Pedido](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e [call, trigger ou nest workflows com pontos finais HTTPS em Aplicações Lógicas Azure](../logic-apps/logic-apps-http-endpoint.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Criar uma aplicação lógica em branco.

1. Depois da Logic App Designer abrir, na caixa de pesquisa, introduza `http request` como filtro. A partir da lista de gatilhos, selecione o **gatilho quando um pedido HTTP é recebido,** que é o primeiro passo no fluxo de trabalho da aplicação lógica.

   ![Selecione gatilho de pedido](./media/connectors-native-reqres/select-request-trigger.png)

   O gatilho do Pedido mostra estas propriedades:

   ![Gatilho de pedido](./media/connectors-native-reqres/request-trigger.png)

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {nenhum} | Sim | O URL de ponto final que é gerado depois de guardar a aplicação lógica e é usado para chamar a sua app lógica |
   | **Solicitar corpo JSON Schema** | `schema` | Não | O esquema JSON que descreve as propriedades e valores no corpo de pedido de entrada |
   |||||

1. Na caixa **Do Corpo de Pedido JSON Schema,** introduza opcionalmente um esquema JSON que descreve o corpo no pedido de entrada, por exemplo:

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

   Ao introduzir um esquema JSON, o designer mostra um lembrete para incluir o cabeçalho no seu pedido e definir esse valor de `Content-Type` cabeçalho para `application/json` . Para mais informações, consulte [os tipos de conteúdo de lidar](../logic-apps/logic-apps-content-type.md).

   ![Lembrete para incluir o cabeçalho "Content-Type"](./media/connectors-native-reqres/include-content-type.png)

   Aqui está o que este cabeçalho parece no formato JSON:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Para gerar um esquema JSON baseado na carga útil esperada (dados), pode utilizar uma ferramenta como [JSONSchema.net,](https://jsonschema.net)ou pode seguir estes passos:

   1. No gatilho Solicitar, selecione Utilize a carga útil da **amostra para gerar esquema**.

      ![Gerar esquema a partir da carga útil](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Introduza a carga útil da amostra e selecione **Done**.

      ![Gerar esquema a partir da carga útil](./media/connectors-native-reqres/enter-payload.png)

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

1. Para especificar propriedades adicionais, abra a **lista de novos parâmetros** e selecione os parâmetros que pretende adicionar.

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **Método** | `method` | Não | O método que o pedido de entrada deve usar para chamar a app lógica |
   | **Caminho relativo** | `relativePath` | Não | O caminho relativo para o parâmetro que o URL final da app lógica pode aceitar |
   |||||

   Este exemplo adiciona a propriedade **Método:**

   ![Adicionar parâmetro método](./media/connectors-native-reqres/add-parameters.png)

   A propriedade **Method** aparece no gatilho para que possa selecionar um método da lista.

   ![Método selecionado](./media/connectors-native-reqres/select-method.png)

1. Agora, adicione outra ação como o próximo passo no seu fluxo de trabalho. Sob o gatilho, selecione **Próximo passo** para que possa encontrar a ação que pretende adicionar.

   Por exemplo, pode responder ao pedido [adicionando uma ação resposta](#add-response), que pode usar para devolver uma resposta personalizada e é descrita mais tarde neste tópico.

   A sua aplicação lógica mantém o pedido de entrada aberto apenas por um [tempo limitado.](../logic-apps/logic-apps-limits-and-config.md#request-limits) Assumindo que o fluxo de trabalho da sua aplicação lógica inclui uma ação de Resposta, se a aplicação lógica não devolver uma resposta após este tempo passa, a sua aplicação lógica devolve uma `504 GATEWAY TIMEOUT` ao chamador. Caso contrário, se a sua aplicação lógica não incluir uma ação de Resposta, a sua aplicação lógica devolve imediatamente uma `202 ACCEPTED` resposta ao chamador.

1. Quando terminar, guarde a sua aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

   Este passo gera o URL a utilizar para o envio do pedido que desencadeia a aplicação lógica. Para copiar este URL, selecione o ícone da cópia ao lado do URL.

   ![URL para usar desencadeando a sua aplicação lógica](./media/connectors-native-reqres/generated-url.png)
   
   > [!NOTE]
   > O URL permite utilizar o símbolo "at" **@** (), mas não o símbolo hash **#** ().

1. Para desencadear a sua aplicação lógica, envie um POST HTTP para o URL gerado.

   Por exemplo, pode utilizar uma ferramenta como [o Carteiro](https://www.getpostman.com/) para enviar o HTTP POST. Se ativar a Autenticação Aberta do [Diretório Ativo Azure](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) (Azure AD OAuth) para autorizar chamadas de entrada no gatilho do Pedido, ligue para o gatilho utilizando um URL de Assinatura de [Acesso Partilhado (SAS)](../logic-apps/logic-apps-securing-a-logic-app.md#sas) ou utilizando um símbolo de autenticação, mas não pode utilizar ambos. O símbolo de autenticação deve especificar o `Bearer` tipo no cabeçalho de autorização. Para mais informações, consulte [O acesso seguro e os dados nas Aplicações lógicas do Azure - Acesso a triggers baseados em pedidos](../logic-apps/logic-apps-securing-a-logic-app.md#secure-triggers).

Para obter mais informações sobre a definição json subjacente do gatilho e como chamar este gatilho, consulte estes tópicos, tipo de [gatilho de pedido](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e [Call, trigger ou nest workflows com pontos finais HTTP em Aplicações Lógicas Azure](../logic-apps/logic-apps-http-endpoint.md).

### <a name="trigger-outputs"></a>Saídas de gatilho

Aqui está mais informações sobre as saídas do gatilho do Pedido:

| Nome da propriedade JSON | Tipo de dados | Descrição |
|--------------------|-----------|-------------|
| `headers` | Objeto | Um objeto JSON que descreve os cabeçalhos do pedido |
| `body` | Objeto | Um objeto JSON que descreve o conteúdo do corpo a partir do pedido |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Adicione uma ação de resposta

Pode utilizar a ação Resposta para responder com uma carga útil (dados) a um pedido HTTPS de entrada, mas apenas numa aplicação lógica que é desencadeada por um pedido HTTPS. Pode adicionar a ação Resposta em qualquer ponto do seu fluxo de trabalho. Para obter mais informações sobre a definição jSON subjacente para este gatilho, consulte o tipo de [ação Resposta](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

A sua aplicação lógica mantém o pedido de entrada aberto apenas por um [tempo limitado.](../logic-apps/logic-apps-limits-and-config.md#request-limits) Assumindo que o fluxo de trabalho da sua aplicação lógica inclui uma ação de Resposta, se a aplicação lógica não devolver uma resposta após este tempo passa, a sua aplicação lógica devolve uma `504 GATEWAY TIMEOUT` ao chamador. Caso contrário, se a sua aplicação lógica não incluir uma ação de Resposta, a sua aplicação lógica devolve imediatamente uma `202 ACCEPTED` resposta ao chamador.

> [!IMPORTANT]
> Se uma ação de Resposta incluir estes cabeçalhos, as Aplicações Lógicas removem estes cabeçalhos da mensagem de resposta gerada sem mostrar qualquer aviso ou erro:
>
> * `Allow`
> * `Content-*`com estas exceções: `Content-Disposition` , `Content-Encoding` e`Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Embora as Aplicações Lógicas não o impeçam de salvar aplicações lógicas que tenham uma ação de Resposta com estes cabeçalhos, as Aplicações Lógicas ignoram estes cabeçalhos.

1. No Logic App Designer, sob o passo em que pretende adicionar uma ação de Resposta, selecione **Novo passo**.

   Por exemplo, utilizando o gatilho pedido de anteriores:

   ![Adicione um novo passo](./media/connectors-native-reqres/add-response.png)

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre esses passos. Selecione o sinal de mais ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Em **'Escolha uma ação**' na caixa de pesquisa, introduza "resposta" como filtro e selecione a ação **Resposta.**

   ![Selecione a ação Resposta](./media/connectors-native-reqres/select-response-action.png)

   O gatilho do Pedido é colapsado neste exemplo para a simplicidade.

1. Adicione os valores necessários para a mensagem de resposta. 

   Em alguns campos, clicar dentro das suas caixas abre a lista de conteúdos dinâmicos. Em seguida, pode selecionar tokens que representam saídas disponíveis a partir de etapas anteriores no fluxo de trabalho. As propriedades do esquema especificada no exemplo anterior aparecem agora na lista de conteúdos dinâmicos.

   Por exemplo, para a caixa **headers,** inclua `Content-Type` como nome-chave, e definir o valor-chave para `application/json` como mencionado anteriormente neste tópico. Para a caixa **Body,** pode selecionar a saída do corpo do gatilho a partir da lista de conteúdos dinâmicos.

   ![Detalhes da ação de resposta](./media/connectors-native-reqres/response-details.png)

   Para ver os cabeçalhos no formato JSON, selecione **Switch para visualização**de texto .

   ![Cabeçalhos - Mude para a vista de texto](./media/connectors-native-reqres/switch-to-text-view.png)

   Aqui está mais informações sobre as propriedades que pode definir na ação Resposta. 

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **Código de Estado** | `statusCode` | Sim | O código de estado para devolver na resposta |
   | **Cabeçalhos** | `headers` | Não | Um objeto JSON que descreve um ou mais cabeçalhos para incluir na resposta |
   | **Corpo** | `body` | Não | O corpo de resposta |
   |||||

1. Para especificar propriedades adicionais, como um esquema JSON para o corpo de resposta, abra a **lista de novos parâmetros Adicionar** e selecione os parâmetros que pretende adicionar.

1. Quando terminar, guarde a sua aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**. 

## <a name="next-steps"></a>Próximos passos

* [Conectores para as Logic Apps](../connectors/apis-list.md)
