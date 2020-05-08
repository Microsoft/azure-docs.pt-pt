---
title: Troca de mensagens para cenários de integração empresarial B2B
description: Receba e envie mensagens B2B entre parceiros comerciais em Aplicações Lógicas Azure utilizando o Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: b576fc99e2f203bb3d690a8135ee76cee26b3de8
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792365"
---
# <a name="receive-and-confirm--b2b-as2-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Receba e confirme mensagens B2B AS2 utilizando aplicações da Lógica Azure e pacote de integração empresarial

Quando você tem uma conta de integração que define parceiros comerciais e acordos, você pode criar um fluxo de trabalho automatizado para negócios (B2B) que troca mensagens entre parceiros comerciais usando [Aplicativos Azure Logic](../logic-apps/logic-apps-overview.md) com o Pacote de [Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md). As Aplicações Azure Logic funcionam com conectores que suportam protocolos as2, X12, EDIFACT e RosettaNet padrão da indústria. Também pode combinar estes conectores com outros [conectores disponíveis em Aplicações Lógicas](../connectors/apis-list.md), por exemplo, Salesforce e Office 365 Outlook.

Este artigo mostra como criar uma aplicação lógica que recebe um pedido HTTP utilizando um gatilho de Pedido, descodifica o conteúdo da mensagem utilizando as ações AS2 e X12 e, em seguida, devolve uma resposta usando a ação Resposta.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma subscrição, [inscreva-se numa conta Azure gratuita.](https://azure.microsoft.com/free/)

* Uma aplicação lógica em branco para que possa criar o fluxo de trabalho B2B utilizando o gatilho [Request](../connectors/connectors-native-reqres.md) que é seguido por estas ações:

  * [Descodificação AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Condição](../logic-apps/logic-apps-control-flow-conditional-statement.md), que envia uma [resposta](../connectors/connectors-native-reqres.md) com base no sucesso ou falha da ação AS2

  * [Descodificar mensagem X12](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Do Azure e](../logic-apps/logic-apps-overview.md) o [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) que está associada à sua subscrição Azure e ligada à sua aplicação lógica. Tanto a sua aplicação lógica como a sua conta de integração devem existir no mesmo local ou na região de Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que já definiu na sua conta de integração juntamente com [os acordos AS2 e X12](logic-apps-enterprise-integration-agreements.md) para esses parceiros.

## <a name="add-request-trigger"></a>Adicionar gatilho de pedido

Este exemplo utiliza o Logic App Designer no portal Azure, mas pode seguir passos semelhantes para o Logic App Designer em Visual Studio.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica em branco no Logic App Designer.

1. Na caixa de `when a http request`pesquisa, introduza , e selecione **Quando um pedido HTTP é recebido** para usar como gatilho.

   ![Selecione gatilho de Pedido para iniciar o fluxo de trabalho da sua aplicação lógica](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Deixe a caixa **JSON Schema** do corpo de pedido vazia porque a mensagem X12 é um ficheiro plano.

   ![Deixe "Request body JSON Schema" vazio](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Quando terminar, na barra de ferramentas de design, selecione **Save**.

   Este passo gera o **URL HTTP POST** a utilizar para o envio do pedido que desencadeia a aplicação lógica. Para copiar este URL, selecione o ícone da cópia ao lado do URL.

   ![URL gerado para gatilho de Pedido para receber chamadas](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>Adicione ação de descodificação AS2

Agora adicione as ações B2B que quer usar. Este exemplo utiliza ações AS2 e X12.

1. Sob o gatilho, selecione **Novo passo**. Para ocultar os detalhes do gatilho, clique na barra de título do gatilho.

   ![Adicione mais um passo ao fluxo de trabalho da sua aplicação lógica](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. Em **'Escolha uma ação**, `as2 decode`na caixa de pesquisa, introduza - e selecione **AS2 Descodificar (v2)**.

   ![Localizar e selecionar "AS2 Decode (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Para que a **Mensagem descodifique** a propriedade, insira a entrada `body` que pretende que a ação AS2 descodifique, que é o conteúdo que é recebido pelo gatilho de pedido http. Tem várias formas de especificar este conteúdo como entrada, quer a partir da lista de conteúdos dinâmicos quer como expressão:

   * Para selecionar a partir de uma lista que mostre as saídas de gatilho disponíveis, clique no interior da **Mensagem para descodificar** a caixa. Após a verificação da lista de conteúdos dinâmicos, em quando **um pedido HTTP é recebido,** selecione o valor da propriedade do **Corpo,** por exemplo:

     ![Selecione o valor "Body" a partir do gatilho](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Para introduzir uma expressão que refira a saída do `body` gatilho, clique no interior da Mensagem para **descodificar** a caixa. Depois da lista de conteúdos dinâmicos aparecer, selecione **Expression**. No editor de expressão, insira a expressão aqui, e selecione **OK:**

     `triggerOutputs()['body']`

     Ou, na **caixa mensagem para descodificar,** introduza diretamente esta expressão:

     `@triggerBody()`

     A expressão resolve-se com o símbolo do **Corpo.**

     ![Saída do corpo resolvida a partir do gatilho](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Para a propriedade **'Cabeçalhos mensagem',** introduza os cabeçalhos `headers` necessários para a ação AS2, que são descritos pelo conteúdo que é recebido pelo gatilho de pedido http.

   Para introduzir uma expressão que refira a saída do `headers` gatilho, clique no interior da caixa de **cabeçalhos da Mensagem.** Depois da lista de conteúdos dinâmicos aparecer, selecione **Expression**. No editor de expressão, insira a expressão aqui, e selecione **OK:**

   `triggerOutputs()['Headers']`

   Para que esta expressão se resolva como esta ficha, alterna entre o designer e a vista de código, por exemplo:

   ![Saída de cabeçalhos resolvidos a partir do gatilho](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Adicionar ação de resposta para notificação de recibo de mensagem

Para notificar o parceiro comercial de que a mensagem foi recebida, pode devolver uma resposta que contenha uma Notificação de Disposição de Mensagens AS2 (MDN) utilizando a ação **Resposta.** Ao adicionar esta ação imediatamente após a ação **AS2 Decode,** caso essa ação falhe, a aplicação lógica não continua a ser processada.

1. Sob a ação **AS2 Descodificar,** selecione **Novo passo**.

1. Em **'Escolha uma ação**, sob a caixa de pesquisa, selecione **Incorporado**. Na caixa de pesquisa, introduza `condition`. Na lista **de Ações,** selecione **Condição**.

   ![Adicione a ação "Condição"](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Agora a forma da condição aparece, incluindo os caminhos para se a condição é satisfeita ou não.

   ![Forma de condição com caminhos de decisão](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Agora especifique a condição para avaliar. Na **Escolha de uma** caixa de valor, introduza esta expressão:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   Na caixa intermédia, certifique-se de `is equal to`que a operação de comparação está definida para . Na caixa do lado direito, `Expected`introduza o valor . Para que a expressão se resolva como esta ficha, altere entre o designer e a vista de código.

   ![Forma de condição com caminhos de decisão](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Agora especifique as respostas para devolver se a ação **AS2 Decode** tem sucesso ou não.

   1. Para o caso em que a ação **AS2 Decode** for bem sucedida, na forma **Se verdadeiramente,** selecione **Adicionar uma ação**. Em **'Escolha uma ação**, `response`na caixa de pesquisa, introduza , e selecione **Resposta**.

      ![Localizar e selecionar a ação "Resposta"](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Para aceder ao AS2 MDN a partir da saída da ação **AS2 Decode,** especifique estas expressões:

      * Na propriedade da ação **de resposta,** insira esta expressão: **Headers**

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Na propriedade do **Corpo** da Ação **resposta,** insira esta expressão:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Para que as expressões se resolvam como fichas, alterna entre o designer e a vista de código:

      ![Expressão resolvida para aceder ao AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. Para o caso em que a ação **AS2 Decode** falha, na forma **Se falsa,** selecione **Adicionar uma ação**. Em **'Escolha uma ação**, `response`na caixa de pesquisa, introduza , e selecione **Resposta**. Configurar a ação **Resposta** para devolver o estado e o erro que deseja.

1. Guarde a aplicação lógica.

## <a name="add-decode-x12-message-action"></a>Adicionar ação de mensagem Decode X12

1. Adicione agora a ação **de mensagem Decode X12.** No âmbito da ação **Resposta,** selecione **Adicionar uma ação**.

1. Em **'Escolha uma ação**, `x12 decode`na caixa de pesquisa, introduza - e selecione **Decode X12 message**.

   ![Localizar e selecionar ação "Descodificar a mensagem X12"](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Se a ação X12 lhe solicitar informações sobre ligação, forneça o nome para a ligação, selecione a conta de integração que pretende utilizar e, em seguida, selecione **Criar**.

   ![Criar conexão X12 à conta de integração](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Agora especifique a entrada para a ação X12. Este exemplo utiliza a saída da ação AS2, que é o conteúdo da mensagem, mas note que este conteúdo está no formato de objeto JSON e é codificado pela base64. Então, tens de converter este conteúdo numa corda.

   Na mensagem de **ficheiro X12 Flat para descodificar** a caixa, introduza esta expressão para converter a saída AS2:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Para que a expressão se resolva como esta ficha, altere entre o designer e a vista de código.

    ![Converter conteúdo codificado base64 numa corda](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Guarde a aplicação lógica.

   Se precisar de passos adicionais para esta aplicação lógica, por exemplo, para descodificar o conteúdo e saída de mensagens que os conteúdos em formato de objeto JSON, continue a construir a sua aplicação lógica.

Já acabou de configurar a sua aplicação lógica B2B. Numa aplicação do mundo real, é melhor armazenar os dados X12 descodificados numa aplicação ou loja de dados de linha de negócios (LOB). Por exemplo, consulte estes artigos:

* [Ligar-se a sistemas SAP a partir de Azure Logic Apps](../logic-apps/logic-apps-using-sap-connector.md)
* [Monitorize, crie e gere os ficheiros SFTP utilizando aplicações lógicas SSH e Azure](../connectors/connectors-sftp-ssh.md)

Para ligar as suas próprias aplicações LOB e utilizar estas APIs na sua aplicação lógica, pode adicionar mais ações ou [escrever APIs personalizados](../logic-apps/logic-apps-create-api-app.md).

## <a name="next-steps"></a>Passos seguintes

* [Receber e responder às chamadas https recebidas](../connectors/connectors-native-reqres.md)
* [Troca de mensagens AS2 para integração empresarial B2B](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Trocar mensagens X12 para integração empresarial B2B](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Saiba mais sobre o [Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md)
