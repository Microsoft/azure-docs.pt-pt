---
title: Ligar a pontos finais REST a partir do Azure Logic Apps
description: Monitorizar pontos finais REST em tarefas automatizadas, processos e fluxos de trabalho com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: f0410ed7a98e4838e41407868cf26b5254811ae3
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541619"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Chamar pontos finais REST com o Azure Logic Apps

Com o [Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o incorporada HTTP + Swagger connector, pode automatizar fluxos de trabalho que regularmente chamam qualquer ponto final REST através de um [ficheiro Swagger](https://swagger.io) com a criação de aplicações lógicas. O HTTP + Swagger acionar e ação funcionam da mesma forma como o [acionador HTTP e ação](connectors-native-http.md) mas proporcionar uma melhor experiência no Estruturador da aplicação lógica ao expor a estrutura de API e saídas descritas pelo ficheiro Swagger. Para implementar um acionador de consulta, siga o padrão de consulta que está descrito em [criar APIs personalizadas para chamar outros APIs, serviços e sistemas de aplicações lógicas](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O URL para o ficheiro Swagger que descreve o ponto de extremidade REST de destino

  Normalmente, o ponto final REST tem de cumprir estes critérios para o conector funcione:

  * O ficheiro Swagger tem de estar alojado num URL HTTPS que está acessível ao público.

  * Tem de ter o ficheiro Swagger [Cross-Origin Resource Sharing (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) ativada.

  Para fazer referência a um ficheiro Swagger que não é alojada ou que não cumpre os requisitos de várias origens e a segurança, pode [carregar o ficheiro de Swagger para um contentor de BLOBs numa conta de armazenamento do Azure](#host-swagger)e ativar o CORS nessa conta de armazenamento por isso, que pode referenciar o ficheiro.

  Os exemplos deste tópico de utilização a [API Face dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/face/overview), que requer uma [chave de acesso e de conta de serviços cognitivos](../cognitive-services/cognitive-services-apis-create-account.md).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* A aplicação de lógica de onde deseja chamar o ponto de extremidade de destino. Para começar a HTTP + Swagger acionar, [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar o HTTP + Swagger ação, inicie a aplicação lógica com qualquer acionador que pretende. Este exemplo utiliza HTTP + Swagger acionador como a primeira etapa.

## <a name="add-an-http--swagger-trigger"></a>Adicionar um HTTP + Swagger acionador

Este acionador interno envia um pedido HTTP para um URL para um ficheiro Swagger que descreve uma API REST e retorna uma resposta que contém o conteúdo desse arquivo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a aplicação lógica em branco no Estruturador da aplicação lógica.

1. No estruturador, na caixa de pesquisa, introduza "swagger" como o filtro. Partir do **Acionadores** lista, selecione a **HTTP + Swagger** acionador.

   ![Selecione HTTP + Swagger acionar](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Na **URL de ponto final de SWAGGER** caixa, introduza o URL para o ficheiro Swagger e selecione **próxima**.

   Este exemplo utiliza o URL do Swagger que está localizado na região E.U.A. oeste para o [API Face dos serviços cognitivos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Introduza o URL para o ponto final de Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Quando o estruturador mostra as operações descritas pelo ficheiro Swagger, selecione a operação que pretende utilizar.

   ![Operações de ficheiro Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Forneça os valores para os parâmetros do acionador, que variam com base na operação selecionada, o que deseja incluir na chamada de ponto final. Configure a periodicidade para a frequência com que pretende que o acionador para chamar o ponto final.

   Este exemplo muda o nome o acionador "HTTP + Swagger acionar: Se deparam - detetar", para que a etapa tem um nome mais descritivo.

   ![Detalhes da operação](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra a **adicione o novo parâmetro** lista e selecione os parâmetros que pretende.

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP + Swagger, consulte [autenticar HTTP acionadores e ações](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Continue a criar o fluxo de trabalho da sua aplicação lógica com ações executadas quando o acionador é acionado.

1. Quando tiver terminado, lembre-se de guardar a aplicação lógica. Na barra de ferramentas da estruturador, selecione **guardar**.

## <a name="add-an-http--swagger-action"></a>Adicionar um HTTP + Swagger ação

Esta ação incorporada faz uma solicitação HTTP para o URL para o ficheiro Swagger que descreve uma API REST e retorna uma resposta que contém o conteúdo desse arquivo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a aplicação lógica no Estruturador da aplicação lógica.

1. No passo em que pretende adicionar o HTTP + Swagger ação, selecione **novo passo**.

   Para adicionar uma ação entre passos, mova o ponteiro do mouse sobre a seta entre passos. Selecione o sinal ( **+** ) que é apresentada e, em seguida, selecione **adicionar uma ação**.

1. No estruturador, na caixa de pesquisa, introduza "swagger" como o filtro. Partir do **ações** lista, selecione a **HTTP + Swagger** ação.

    ![Selecione HTTP + Swagger ação](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Na **URL de ponto final de SWAGGER** caixa, introduza o URL para o ficheiro Swagger e selecione **próxima**.

   Este exemplo utiliza o URL do Swagger que está localizado na região E.U.A. oeste para o [API Face dos serviços cognitivos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Introduza o URL para o ponto final de Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Quando o estruturador mostra as operações descritas pelo ficheiro Swagger, selecione a operação que pretende utilizar.

   ![Operações de ficheiro Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Forneça os valores para os parâmetros de ação, que variam com base na operação selecionada, o que deseja incluir na chamada de ponto final.

   Neste exemplo não tem parâmetros, mas muda o nome da ação como "HTTP + Swagger ação: Se deparam - identificar", para que a etapa tem um nome mais descritivo.

   ![Detalhes da operação](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra a **adicione o novo parâmetro** lista e selecione os parâmetros que pretende.

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP + Swagger, consulte [autenticar HTTP acionadores e ações](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Quando tiver terminado, lembre-se de guardar a aplicação lógica. Na barra de ferramentas da estruturador, selecione **guardar**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Swagger de anfitrião no armazenamento do Azure

Pode referenciar um ficheiro Swagger que não é alojada ou que não cumpre os requisitos de várias origens e a segurança ao carregar esse ficheiro para o contentor de BLOBs numa conta de armazenamento do Azure e ativar o CORS nessa conta de armazenamento. Para criar, configurar e armazenar os ficheiros Swagger no armazenamento do Azure, siga estes passos:

1. [Criar uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md).

1. Agora, ative CORS para o blob. No menu da conta de armazenamento, selecione **CORS**. Sobre o **serviço Blob** separador, especifique esses valores e, em seguida, selecione **guardar**.

   | Propriedade | Value |
   |----------|-------|
   | **Origens permitidas** | `*` |
   | **Métodos permitidos** | `GET`, `HEAD`, `PUT` |
   | **Cabeçalhos permitidos** | `*` |
   | **Cabeçalhos expostos** | `*` |
   | **Idade máxima** (em segundos) | `200` |
   |||

   Embora este exemplo utiliza a [portal do Azure](https://portal.azure.com), pode utilizar uma ferramenta como [Explorador de armazenamento do Azure](https://storageexplorer.com/), ou configurar automaticamente esta definição utilizando este exemplo [descriptdoPowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Criar um contentor de BLOBs](../storage/blobs/storage-quickstart-blobs-portal.md). Do contentor **descrição geral** painel, selecione **nível de acesso de alteração**. Partir do **nível de acesso público** lista, selecione **Blob (acesso de leitura anónimo apenas para blobs)** e selecione **OK**.

1. [Carregar o ficheiro de Swagger para o contentor de BLOBs](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), seja por meio do [portal do Azure](https://portal.azure.com) ou [Explorador de armazenamento do Azure](https://storageexplorer.com/).

1. Para referenciar o ficheiro no contentor de BLOBs, utilize uma ligação HTTPS que se segue este formato, o que diferencia maiúsculas de minúsculas:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Referência do conector

Aqui estão mais algumas informações sobre as saídas de um HTTP + Swagger acionador ou ação. O HTTP + Swagger chamada retorna essas informações:

| Nome da propriedade | Tipo | Descrição |
|---------------|------|-------------|
| Cabeçalhos | object | Os cabeçalhos do pedido |
| Corpo | object | Objeto JSON | O objeto com o conteúdo do corpo do pedido |
| Código de estado | int | O código de estado do pedido |
|||

| Código de estado | Descrição |
|-------------|-------------|
| 200 | OK |
| 202 | Aceite |
| 400 | Pedido incorreto |
| 401 | Não autorizado |
| 403 | Proibido |
| 404 | Não foi encontrado |
| 500 | Erro de servidor interno. Ocorreu um erro desconhecido. |
|||

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)