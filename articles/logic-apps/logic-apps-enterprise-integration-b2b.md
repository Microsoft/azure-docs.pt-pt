---
title: Trocar mensagens para cenários de integração empresarial B2B
description: Receba e envie mensagens B2B entre parceiros comerciais em Azure Logic Apps utilizando o Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/02/2020
ms.openlocfilehash: e16cc8934407a5c54c84fd045c99e28116e656c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93310501"
---
# <a name="receive-and-confirm--b2b-as2-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Receba e confirme as mensagens B2B AS2 utilizando apps Azure Logic e Pacote de Integração Empresarial

Quando tem uma conta de integração que define parceiros e acordos comerciais, pode criar um fluxo de trabalho automatizado para negócios (B2B) que troca mensagens entre parceiros comerciais utilizando [apps da Azure Logic](../logic-apps/logic-apps-overview.md) com o Pacote de [Integração Empresarial.](../logic-apps/logic-apps-enterprise-integration-overview.md) A Azure Logic Apps trabalha com conectores que suportam protocolos padrão da indústria AS2, X12, EDIFACT e RosettaNet. Também pode combinar estes conectores com outros [conectores disponíveis em Aplicações Lógicas](../connectors/apis-list.md), por exemplo, Salesforce e Office 365 Outlook.

Este artigo mostra como criar uma aplicação lógica que recebe um pedido HTTP utilizando um gatilho Pedido, descodifica o conteúdo da mensagem utilizando as ações AS2 e X12 e, em seguida, devolve uma resposta utilizando a ação Resposta.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma subscrição, [inscreva-se para uma conta Azure gratuita.](https://azure.microsoft.com/free/)

* Uma aplicação lógica em branco para que possa criar o fluxo de trabalho B2B utilizando o gatilho [Request](../connectors/connectors-native-reqres.md) que é seguido por estas ações:

  * [Descodificar AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Condição](../logic-apps/logic-apps-control-flow-conditional-statement.md), que envia uma [resposta](../connectors/connectors-native-reqres.md) baseada em se a ação as2 Descodificar tem sucesso ou falha

  * [Descodificar mensagem X12](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Se é novo em aplicações lógicas, [reveja O que é Azure Logic Apps e](../logic-apps/logic-apps-overview.md) [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma conta de [integração](./logic-apps-enterprise-integration-create-integration-account.md) que está associada à sua subscrição Azure e ligada à sua aplicação lógica. Tanto a sua aplicação lógica como a sua conta de integração devem existir no mesmo local ou na região de Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que já definiu na sua conta de integração, juntamente com [os acordos AS2 e X12](logic-apps-enterprise-integration-agreements.md) para esses parceiros.

## <a name="add-the-request-trigger"></a>Adicione o gatilho do pedido

Este exemplo utiliza o Logic App Designer no portal Azure, mas pode seguir passos semelhantes para o Logic App Designer em Visual Studio.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica em branco no Logic App Designer.

1. Na caixa de pesquisa, insira `when a http request` e selecione **Quando um pedido HTTP for recebido** para ser utilizado como gatilho.

   ![Selecione Acionar o gatilho para iniciar o fluxo de trabalho da aplicação lógica](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Deixe a caixa **de esquema JSON do corpo do Pedido** vazia porque a mensagem X12 é um ficheiro plano.

   ![Deixe "Request body JSON Schema" vazio](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Quando terminar, na barra de ferramentas do designer, **selecione Save**.

   Este passo gera o **URL HTTP POST** para usar para o envio do pedido que desencadeia a aplicação lógica. Para copiar este URL, selecione o ícone de cópia ao lado do URL.

   ![URL gerado para pedido de gatilho para receber chamadas](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-the-as2-decode-action"></a>Adicione a ação de descodificar AS2

Adicione agora as ações B2B que pretende utilizar. Este exemplo utiliza ações AS2 e X12.

1. Sob o gatilho, selecione **Novo passo**. Para ocultar os detalhes do gatilho, clique na barra de título do gatilho.

   ![Adicione mais um passo no seu fluxo de trabalho de aplicações lógicas](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. Em **Escolha uma ação,** na caixa de pesquisa, insira `as2 decode` e selecione **AS2 Decode (v2)**.

   ![Localizar e selecionar "AS2 Descodificar (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Para que **a Mensagem descodifice** a propriedade, insira a entrada que pretende que a ação AS2 descodifique, que é o `body` conteúdo que é recebido pelo gatilho do pedido HTTP. Tem várias formas de especificar este conteúdo como entrada, quer a partir da lista de conteúdos dinâmicos, quer como expressão:

   * Para selecionar a partir de uma lista que mostra as saídas disponíveis do gatilho, clique no interior da **caixa 'Mensagem' para descodificar** a caixa. Após a apresentação da lista de conteúdos dinâmicos, em **Quando um pedido HTTP for recebido,** selecione o valor da propriedade do **Corpo,** por exemplo:

     ![Selecione o valor "Corpo" do gatilho](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Para introduzir uma expressão que faz referência à saída do `body` gatilho, clique no interior da **Caixa de Descodificar.** Depois de aparecer a lista de conteúdos dinâmicos, selecione **Expression**. No editor de expressão, insira a expressão aqui, e selecione **OK**:

     `triggerOutputs()['body']`

     Ou, na **Mensagem para descodificar** a caixa, introduza diretamente esta expressão:

     `@triggerBody()`

     A expressão resolve-se com o **símbolo do corpo.**

     ![Saída do corpo resolvida do gatilho](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Para a propriedade **cabeçalhos de mensagem,** insira os cabeçalhos necessários para a ação AS2, que são descritos pelo `headers` conteúdo recebido pelo gatilho de pedido HTTP.

   1. Para introduzir uma expressão que faz referência à saída do `headers` gatilho, selecione **os cabeçalhos da mensagem switch para o modo de texto**.

      ![Screenshot que mostra "Cabeçalhos de mensagem de switch para modo de texto" selecionado.](./media/logic-apps-enterprise-integration-b2b/as2-decode-switch-text-mode.png)

   1. Clique dentro da caixa **de cabeçalhos de mensagem.** Depois de aparecer a lista de conteúdos dinâmicos, selecione **Expression**. No editor de expressão, insira a expressão aqui, e selecione **OK**:

      `triggerOutputs()['Headers']`

      Na ação as2 Decode, a expressão aparece agora como um símbolo:

      ![Screenshot que mostra o símbolo " @triggerOutputs ()['Headers']" na caixa "Cabeçalhos de mensagem".](./media/logic-apps-enterprise-integration-b2b/as2-decode-message-header-expression.png)

   1. Para obter a expressão token para resolver no token **headers,** altere entre o designer e a vista de código. Após este passo, a ação de descodificar AS2 parece este exemplo:

      ![Saída de cabeçalhos resolvidos do gatilho](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Adicionar ação de resposta para notificação de receção de mensagens

Para notificar o parceiro comercial de que a mensagem foi recebida, pode retornar uma resposta que contenha uma Notificação de Disposição de Mensagens AS2 (MDN) utilizando a ação **Resposta.** Ao adicionar esta ação imediatamente após a ação **de Descodificar o AS2,** caso essa ação falhe, a aplicação lógica não continua a ser processada.

1. Sob a ação **AS2 Descodificar,** selecione **Novo passo**.

1. Em **Seleção de uma ação,** sob a caixa de pesquisa, selecione **Built-in**. Na caixa de pesquisa, introduza `condition`. Na lista **Ações**, selecione **Condição**.

   ![Adicione a ação "Condição"](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Agora a forma da condição aparece, incluindo os caminhos para se a condição é cumprida ou não.

   ![A imagem mostra a forma da condição com caminhos vazios.](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Agora especifique a condição para avaliar. Na caixa **de valor Escolha,** introduza esta expressão:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   Na caixa média, certifique-se de que a operação de comparação está definida para `is equal to` . Na caixa do lado direito, insira o valor `Expected` . Para obter a expressão para resolver como este símbolo, altere entre o designer e a visão de código.

   ![A screenshot mostra a forma da condição com uma condição adicionada.](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Agora especifique as respostas para devolver se a ação **de Decode AS2** tem sucesso ou não.

   1. Para o caso de a ação **de descodificar** as AS2 ter sucesso, na forma **"Se verdadeira",** selecione **Adicione uma ação**. Em **Selecione uma ação,** na caixa de pesquisa, insira `response` e selecione **Resposta**.

      ![Localizar e selecionar a ação "Resposta"](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Para aceder ao AS2 MDN a partir da saída da ação **dedescodificar as AS2,** especifique estas expressões:

      * Na propriedade headers da ação **Resposta,** **insira** esta expressão:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Na propriedade **Body** da Ação **Resposta,** insira esta expressão:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Para que as expressões se resolvam como símbolos, altere entre o designer e a visão de código:

      ![Expressão resolvida para aceder a AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. Para o caso de a ação **de descodificar** as AS2 falhar, na forma **Se for falsa,** selecione **Adicione uma ação**. Em **Selecione uma ação,** na caixa de pesquisa, insira `response` e selecione **Resposta**. Configurar a ação **Resposta** para devolver o estado e o erro que deseja.

1. Guarde a sua aplicação lógica.

## <a name="add-decode-x12-message-action"></a>Adicionar ação de mensagem Decode X12

1. Adicione agora a ação da **mensagem Decode X12.** No âmbito da ação **Resposta,** **selecione Adicionar uma ação**.

1. Em **Selecione uma ação,** na caixa de pesquisa, insira `x12 decode` e selecione **Decode X12 message**.

   ![Localizar e selecionar ação "Descodificar a mensagem X12"](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Se a ação X12 lhe solicitar informações de ligação, fornecer o nome da ligação, selecionar a conta de integração que pretende utilizar e, em seguida, selecionar **Criar**.

   ![Criar ligação X12 à conta de integração](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Agora especifique a entrada para a ação X12. Este exemplo utiliza a saída da ação AS2, que é o conteúdo da mensagem, mas note que este conteúdo está no formato de objeto JSON e está codificado com base64. Então, tens de converter este conteúdo numa corda.

   Na **mensagem de ficheiro plano X12 para descodificar** a caixa, introduza esta expressão para converter a saída AS2:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Para obter a expressão para resolver como este símbolo, altere entre o designer e a visão de código.

    ![Converter conteúdo codificado de base64 para uma cadeia](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Guarde a sua aplicação lógica.

   Se precisar de passos adicionais para esta aplicação lógica, por exemplo, para descodificar o conteúdo da mensagem e a saída desse conteúdo no formato de objeto JSON, continue a construir a sua aplicação lógica.

Já acabou de configurar a sua aplicação lógica B2B. Numa aplicação do mundo real, é melhor armazenar os dados X12 descodificados numa aplicação de linha de negócios (LOB) ou na loja de dados. Por exemplo, consulte estes artigos:

* [Ligar-se a sistemas SAP a partir de Azure Logic Apps](../logic-apps/logic-apps-using-sap-connector.md)
* [Monitorizar, criar e gerir ficheiros SFTP com o SSH e o Azure Logic Apps](../connectors/connectors-sftp-ssh.md)

Para ligar as suas próprias aplicações LOB e utilizar estas APIs na sua aplicação lógica, pode adicionar mais ações ou [escrever APIs personalizados.](../logic-apps/logic-apps-create-api-app.md)

## <a name="next-steps"></a>Passos seguintes

* [Receber e responder às chamadas HTTPS recebidas](../connectors/connectors-native-reqres.md)
* [Trocar mensagens AS2 para integração empresarial B2B](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Trocar mensagens X12 para integração empresarial B2B](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Saiba mais sobre o [Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md)
