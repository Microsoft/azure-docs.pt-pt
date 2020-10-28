---
title: Conecte-se aos pontos finais do REST a partir de Apps Azure Logic
description: Monitorize pontos finais do REST em tarefas, processos e fluxos de trabalho automatizados utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: af98811e158b9613e41389e08e19cb36797aa272
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790598"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Ligue para os pontos finais do REST utilizando apps Azure Logic

Com [as Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o conector HTTP + Swagger incorporado, é possível automatizar fluxos de trabalho que regularmente chamem qualquer ponto final DE REST através de um ficheiro [Swagger](https://swagger.io) construindo aplicações lógicas. O gatilho HTTP + Swagger e a ação funcionam da mesma forma que o [gatilho e ação HTTP,](connectors-native-http.md) mas proporcionam uma melhor experiência no Logic App Designer expondo a estrutura da API e as saídas descritas pelo ficheiro Swagger. Para implementar um gatilho de sondagens, siga o padrão de sondagem descrito na [Create CUSTOM APIs para chamar outras APIs, serviços e sistemas de aplicações lógicas.](../logic-apps/logic-apps-create-api-app.md#polling-triggers)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O URL para o ficheiro Swagger (não OpenAPI) que descreve o ponto final do REST alvo

  Normalmente, o ponto final REST deve satisfazer estes critérios para que o conector funcione:

  * O ficheiro Swagger deve ser hospedado num URL HTTPS acessível ao público.
  
  * O ficheiro Swagger deve conter uma `operationID` operação para cada operação na definição. Caso contrário, o conector só mostra a última operação no ficheiro Swagger. 

  * O ficheiro Swagger deve ter [a Partilha de Recursos de Origem Cruzada (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) ativada.

  Para fazer referência a um ficheiro Swagger que não esteja hospedado ou que não satisfaça os requisitos de segurança e origem cruzada, pode [enviar o ficheiro Swagger para um recipiente de bolhas numa conta de armazenamento Azure](#host-swagger), e ativar o CORS nessa conta de armazenamento para que possa fazer referência ao ficheiro.

  Os exemplos neste tópico utilizam a API dos [Serviços Cognitivos](../cognitive-services/face/overview.md)Face, que requer uma [conta de Serviços Cognitivos e uma chave de acesso.](../cognitive-services/cognitive-services-apis-create-account.md)

* Conhecimento básico sobre [como criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* A aplicação lógica de onde pretende chamar o ponto final do alvo. Para começar com o gatilho HTTP + Swagger, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar a ação HTTP + Swagger, inicie a sua aplicação lógica com o gatilho que pretende. Este exemplo utiliza o gatilho HTTP + Swagger como primeiro passo.

## <a name="add-an-http--swagger-trigger"></a>Adicione um gatilho HTTP + Swagger

Este gatilho incorporado envia um pedido HTTP a um URL para um ficheiro Swagger que descreve uma API REST e devolve uma resposta que contém o conteúdo desse ficheiro.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Abra a sua aplicação lógica em branco no Logic App Designer.

1. No designer, na caixa de pesquisa, introduza "swagger" como filtro. Na lista **De gatilhos,** selecione o gatilho **HTTP + Swagger.**

   ![Selecione HTTP + Gatilho swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Na caixa **URL SWAGGER ENDPOINT,** introduza o URL para o ficheiro Swagger e selecione **Next** .

   Este exemplo utiliza o URL Swagger que está localizado na região oeste dos EUA para a [API de Serviços Cognitivos:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Screenshot que mostra o Logic App Designer com o gatilho "H T T P + Swagger" e a propriedade "Swagger Endpoint U R L" definida para um valor U R L.](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Quando o designer mostrar as operações descritas pelo ficheiro Swagger, selecione a operação que pretende utilizar.

   ![Screenshot que mostra o Logic App Designer com o gatilho "H T T + Swagger" e uma lista que exibe operações de Swagger.](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Forneça os valores para os parâmetros do gatilho, que variam em função da operação selecionada, que pretende incluir na chamada de ponto final. Encete a recorrência para a frequência com que pretende que o gatilho chame o ponto final.

   Este exemplo renomea o gatilho para "HTTP + Swagger trigger: Face - Detect" para que o passo tenha um nome mais descritivo.

   ![Screenshot que mostra o Designer de Aplicações Lógica com o gatilho "H T T + Swagger" que exibe a operação "Face - Detect".](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra a nova lista **de parâmetros add** e selecione os parâmetros que deseja.

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP + Swagger, consulte [adicionar autenticação a chamadas de saída.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

1. Continue a construir o fluxo de trabalho da sua aplicação lógica com ações que funcionam quando o gatilho dispara.

1. Quando terminar, lembre-se de guardar a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save** .

## <a name="add-an-http--swagger-action"></a>Adicione uma ação HTTP + Swagger

Esta ação incorporada faz um pedido HTTP ao URL para o ficheiro Swagger que descreve uma API REST e devolve uma resposta que contém o conteúdo desse ficheiro.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Abra a sua aplicação lógica no Logic App Designer.

1. Sob o passo em que pretende adicionar a ação HTTP + Swagger, selecione **Novo passo** .

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre os degraus. Selecione o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação** .

1. No designer, na caixa de pesquisa, introduza "swagger" como filtro. Na lista **de Ações,** selecione a ação **HTTP + Swagger.**

    ![Selecione HTTP + Ação swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Na caixa **URL SWAGGER ENDPOINT,** introduza o URL para o ficheiro Swagger e selecione **Next** .

   Este exemplo utiliza o URL Swagger que está localizado na região oeste dos EUA para a [API de Serviços Cognitivos:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Insira URL para o ponto final de Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Quando o designer mostrar as operações descritas pelo ficheiro Swagger, selecione a operação que pretende utilizar.

   ![Operações em ficheiro Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Forneça os valores para os parâmetros de ação, que variam em função da operação selecionada, que pretende incluir na chamada de ponto final.

   Este exemplo não tem parâmetros, mas rebatiza a ação para "HTTP + Ação swagger: Face - Identifique" para que o passo tenha um nome mais descritivo.

   ![Detalhes da operação](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra a nova lista **de parâmetros add** e selecione os parâmetros que deseja.

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP + Swagger, consulte [adicionar autenticação a chamadas de saída.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

1. Quando terminar, lembre-se de guardar a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save** .

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Host Swagger no Armazenamento Azure

Pode fazer referência a um ficheiro Swagger que não esteja hospedado ou que não satisfaça os requisitos de segurança e origem cruzada, enviando esse ficheiro para o recipiente blob numa conta de armazenamento Azure e permitindo que o CORS fique nessa conta de armazenamento. Para criar, configurar e armazenar ficheiros Swagger no Azure Storage, siga estes passos:

1. [Criar uma conta de armazenamento Azure](../storage/common/storage-account-create.md).

1. Agora ative o CORS para a bolha. No menu da sua conta de armazenamento, selecione **CORS** . No **separador de serviço Blob,** especifique estes valores e, em seguida, **selecione Guardar** .

   | Propriedade | Valor |
   |----------|-------|
   | **Origens permitidas** | `*` |
   | **Métodos permitidos** | `GET`, `HEAD`, `PUT` |
   | **Cabeçalhos permitidos** | `*` |
   | **Cabeçalhos expostos** | `*` |
   | **Idade máxima** (em segundos) | `200` |
   |||

   Embora este exemplo utilize o [portal Azure,](https://portal.azure.com)pode utilizar uma ferramenta como [o Azure Storage Explorer,](https://storageexplorer.com/)ou configurar automaticamente esta definição utilizando esta amostra [de script PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Criar um recipiente blob](../storage/blobs/storage-quickstart-blobs-portal.md). No painel de **visão geral** do recipiente, selecione Alterar o **nível de acesso** . A partir da lista de **níveis de acesso do Público,** selecione **Blob (acesso anónimo apenas para bolhas)** e selecione **OK** .

1. [Faça o upload do ficheiro Swagger para o recipiente blob](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), seja através do [portal Azure](https://portal.azure.com) ou [do Azure Storage Explorer](https://storageexplorer.com/).

1. Para fazer referência ao ficheiro no recipiente blob, obtenha o URL HTTPS que segue este formato, que é sensível a casos, do Azure Storage Explorer:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>Referência do conector

Aqui está mais informações sobre as saídas de um disparador http + swagger ou ação. A chamada HTTP + Swagger devolve esta informação:

| Nome da propriedade | Tipo | Descrição |
|---------------|------|-------------|
| cabeçalhos | objeto | Os cabeçalhos do pedido |
| body | objeto | Objeto JSON | O objeto com o conteúdo do corpo do pedido |
| código de estado | int | O código de estado do pedido |
|||

| Código de estado | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Aceite |
| 400 | Mau pedido |
| 401 | Não autorizado |
| 403 | Proibido |
| 404 | Não encontrado |
| 500 | Erro interno do servidor. Ocorreu um erro desconhecido. |
|||

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
