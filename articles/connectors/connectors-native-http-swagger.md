---
title: Ligue-se aos pontos finais rest de Aplicações Lógicas Azure
description: Monitorize pontos finais REST em tarefas automatizadas, processos e fluxos de trabalho utilizando aplicações lógicas azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: b34fdc36bd0b1ce294a92b2ae8fa5da01568e5a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787374"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Ligue para os pontos finais do REST utilizando aplicações da Lógica Azure

Com [as Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e o conector HTTP + Swagger incorporado, pode automatizar fluxos de trabalho que regularmente chamam qualquer ponto final REST através de um [ficheiro Swagger](https://swagger.io) através da construção de aplicações lógicas. O gatilho HTTP + Swagger e o trabalho de ação funcionam da mesma forma que o [gatilho e ação HTTP,](connectors-native-http.md) mas proporcionam uma melhor experiência no Logic App Designer expondo a estrutura e saídas da API descritas pelo ficheiro Swagger. Para implementar um gatilho de sondagens, siga o padrão de sondagens descrito na [Create CUSTOM APIs para chamar outras APIs, serviços e sistemas de aplicações lógicas](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O URL para o ficheiro Swagger (não OpenAPI) que descreve o ponto final do REST alvo

  Normalmente, o ponto final do REST deve satisfazer estes critérios para que o conector funcione:

  * O ficheiro Swagger deve ser hospedado num URL HTTPS acessível ao público.

  * O ficheiro Swagger deve ter ativado a [Partilha de Recursos De Origem Cruzada (CORS).](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)

  Para fazer referência a um ficheiro Swagger que não seja hospedado ou que não cumpra os requisitos de segurança e origem cruzada, pode [enviar o ficheiro Swagger para um contentor blob numa conta](#host-swagger)de armazenamento Azure , e ativar o CORS nessa conta de armazenamento para que possa fazer referência ao ficheiro.

  Os exemplos neste tópico utilizam a [API face dos Serviços Cognitivos,](https://docs.microsoft.com/azure/cognitive-services/face/overview)que requer uma [conta de Serviços Cognitivos e chave](../cognitive-services/cognitive-services-apis-create-account.md)de acesso.

* Conhecimento básico sobre [como criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

* A aplicação lógica de onde pretende chamar o ponto final do alvo. Para começar com o gatilho HTTP + Swagger, [crie uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para utilizar a ação HTTP + Swagger, inicie a sua aplicação lógica com qualquer gatilho que pretenda. Este exemplo utiliza o gatilho HTTP + Swagger como primeiro passo.

## <a name="add-an-http--swagger-trigger"></a>Adicione um gatilho HTTP + Swagger

Este gatilho incorporado envia um pedido http para um URL para um ficheiro Swagger que descreve uma API REST e devolve uma resposta que contém o conteúdo desse ficheiro.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Abra a sua aplicação lógica em branco no Logic App Designer.

1. No designer, na caixa de pesquisa, introduza "swagger" como filtro. A partir da lista **de Gatilhos,** selecione o gatilho **HTTP + Swagger.**

   ![Selecione HTTP + Gatilho Swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Na caixa **DEURL SWAGGER ENDPOINT,** introduza o URL para o ficheiro Swagger e selecione **Next**.

   Este exemplo usa o URL Swagger que está localizado na região dos EUA Ocidentais para a [API dos Serviços Cognitivos:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Introduza URL para o ponto final de Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Quando o designer mostrar as operações descritas pelo ficheiro Swagger, selecione a operação que pretende utilizar.

   ![Operações no ficheiro Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Forneça os valores para os parâmetros do gatilho, que variam em função da operação selecionada, que pretende incluir na chamada final. Configurar a recorrência para quantas vezes quer que o gatilho ligue para o ponto final.

   Este exemplo renomeia o gatilho para "HTTP + Swagger trigger: Face - Detect" para que o passo tenha um nome mais descritivo.

   ![Detalhes da operação](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra a **lista de novos parâmetros E** selecione os parâmetros que deseja.

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP + Swagger, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Continue a construir o fluxo de trabalho da sua aplicação lógica com ações que funcionam quando o gatilho dispara.

1. Quando terminar, lembre-se de guardar a sua aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

## <a name="add-an-http--swagger-action"></a>Adicione uma ação HTTP + Swagger

Esta ação incorporada faz um pedido http para o URL para o ficheiro Swagger que descreve uma API REST e devolve uma resposta que contém o conteúdo desse ficheiro.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Abra a sua aplicação lógica no Logic App Designer.

1. Sob o degrau onde pretende adicionar a ação HTTP + Swagger, selecione **Novo passo**.

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre os degraus. Selecione o**+** sinal de mais ( ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. No designer, na caixa de pesquisa, introduza "swagger" como filtro. Na lista **de Ações,** selecione a ação **HTTP + Swagger.**

    ![Selecione http + ação swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Na caixa **DEURL SWAGGER ENDPOINT,** introduza o URL para o ficheiro Swagger e selecione **Next**.

   Este exemplo usa o URL Swagger que está localizado na região dos EUA Ocidentais para a [API dos Serviços Cognitivos:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Introduza URL para o ponto final de Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Quando o designer mostrar as operações descritas pelo ficheiro Swagger, selecione a operação que pretende utilizar.

   ![Operações no ficheiro Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Forneça os valores para os parâmetros de ação, que variam em função da operação selecionada, que pretende incluir na chamada final.

   Este exemplo não tem parâmetros, mas renomea a ação para "HTTP + Swagger action: Face - Identify" para que o passo tenha um nome mais descritivo.

   ![Detalhes da operação](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra a **lista de novos parâmetros E** selecione os parâmetros que deseja.

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP + Swagger, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Quando terminar, lembre-se de guardar a sua aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Anfitrião Swagger no Armazenamento Azure

Pode fazer referência a um ficheiro Swagger que não esteja hospedado ou que não cumpra os requisitos de segurança e origem cruzada, carregando esse ficheiro para o contentor blob numa conta de armazenamento Azure e permitindo o CORS nessa conta de armazenamento. Para criar, configurar e armazenar ficheiros Swagger no Armazenamento Azure, siga estes passos:

1. [Crie uma conta de armazenamento Azure.](../storage/common/storage-create-storage-account.md)

1. Agora, ative o CORS para a bolha. No menu da sua conta de armazenamento, selecione **CORS**. No separador **de serviço Blob,** especifique estes valores e, em seguida, selecione **Guardar**.

   | Propriedade | Valor |
   |----------|-------|
   | **Origens permitidas** | `*` |
   | **Métodos permitidos** | `GET`, `HEAD`, `PUT` |
   | **Cabeçalhos permitidos** | `*` |
   | **Cabeçalhos expostos** | `*` |
   | **Idade máxima** (em segundos) | `200` |
   |||

   Embora este exemplo utilize o [portal Azure,](https://portal.azure.com)pode utilizar uma ferramenta como o [Azure Storage Explorer,](https://storageexplorer.com/)ou configurar automaticamente esta definição utilizando esta amostra de [script PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Crie um recipiente de bolhas.](../storage/blobs/storage-quickstart-blobs-portal.md) No painel de **visão geral** do recipiente, selecione Alterar o **nível de acesso**. A partir da lista de níveis de **acesso público,** selecione **Blob (acesso de leitura anónimo apenas para bolhas)**, e selecione **OK**.

1. [Faça upload do ficheiro Swagger para o recipiente blob,](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)seja através do [portal Azure](https://portal.azure.com) ou [do Azure Storage Explorer](https://storageexplorer.com/).

1. Para fazer referência ao ficheiro no recipiente blob, utilize uma ligação HTTPS que siga este formato, que é sensível a casos:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Referência do conector

Aqui está mais informações sobre as saídas de um gatilho OU ação HTTP + Swagger. A chamada HTTP + Swagger devolve esta informação:

| Nome da propriedade | Tipo | Descrição |
|---------------|------|-------------|
| cabeçalhos | objeto | Os cabeçalhos do pedido |
| body | objeto | Objeto JSON | O objeto com o conteúdo do corpo a partir do pedido |
| código de estado | int | O código de estado do pedido |
|||

| Código de estado | Descrição |
|-------------|-------------|
| 200 | OK |
| 202 | Aceite |
| 400 | Mau pedido |
| 401 | Não autorizado |
| 403 | Proibido |
| 404 | Não encontrado |
| 500 | Erro interno do servidor. Erro desconhecido ocorreu. |
|||

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
