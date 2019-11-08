---
title: Conectar-se a pontos de extremidade REST de aplicativos lógicos do Azure
description: Monitorar pontos de extremidade REST em tarefas, processos e fluxos de trabalho automatizados usando aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: 030401623a61e7fcff40187f522309255482647f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824808"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Chamar pontos de extremidade REST usando aplicativos lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o conector http + Swagger interno, você pode automatizar fluxos de trabalho que chamam regularmente qualquer ponto de extremidade REST por meio de um [arquivo do Swagger](https://swagger.io) criando aplicativos lógicos. O gatilho e a ação HTTP + Swagger funcionam da mesma forma que o [gatilho e a ação do http](connectors-native-http.md) , mas fornecem uma experiência melhor no designer do aplicativo lógico expondo a estrutura da API e as saídas descritas pelo arquivo Swagger. Para implementar um gatilho de sondagem, siga o padrão de sondagem descrito em [criar APIs personalizadas para chamar outras APIs, serviços e sistemas de aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* A URL para o arquivo Swagger (não OpenAPI) que descreve o ponto de extremidade REST de destino

  Normalmente, o ponto de extremidade REST deve atender a esses critérios para que o conector funcione:

  * O arquivo do Swagger deve ser hospedado em uma URL HTTPS que é acessível publicamente.

  * O arquivo Swagger deve ter [CORS (compartilhamento de recursos entre origens)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) habilitado.

  Para fazer referência a um arquivo do Swagger que não está hospedado ou que não atende aos requisitos de segurança e entre origens, você pode [carregar o arquivo do Swagger para um contêiner de BLOB em uma conta de armazenamento do Azure](#host-swagger)e habilitar o CORS nessa conta de armazenamento para que você possa fazer referência o arquivo.

  Os exemplos neste tópico usam o [API de detecção facial de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/face/overview), que requer uma [conta de serviços cognitivas e uma chave de acesso](../cognitive-services/cognitive-services-apis-create-account.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

* O aplicativo lógico do qual você deseja chamar o ponto de extremidade de destino. Para começar com o gatilho HTTP + Swagger, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar a ação HTTP + Swagger, inicie seu aplicativo lógico com qualquer gatilho desejado. Este exemplo usa o gatilho HTTP + Swagger como a primeira etapa.

## <a name="add-an-http--swagger-trigger"></a>Adicionar um gatilho HTTP + Swagger

Esse gatilho interno envia uma solicitação HTTP para uma URL para um arquivo do Swagger que descreve uma API REST e retorna uma resposta que contém o conteúdo desse arquivo.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico em branco no designer de aplicativo lógico.

1. No designer, na caixa de pesquisa, digite "Swagger" como filtro. Na lista de **gatilhos** , selecione o gatilho **http + Swagger** .

   ![Selecionar gatilho HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Na caixa **URL do ponto de extremidade do SWAGGER** , insira a URL para o arquivo SWAGGER e selecione **Avançar**.

   Este exemplo usa a URL do Swagger localizada na região oeste dos EUA para o [API de detecção facial de serviços cognitivas](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Insira a URL para o ponto de extremidade do Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Quando o designer mostra as operações descritas pelo arquivo Swagger, selecione a operação que você deseja usar.

   ![Operações no arquivo do Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Forneça os valores para os parâmetros de gatilho, que variam de acordo com a operação selecionada, que você deseja incluir na chamada do ponto de extremidade. Configure a recorrência para a frequência com que você deseja que o gatilho chame o ponto de extremidade.

   Este exemplo renomeia o gatilho para "HTTP + Swagger Trigger: facial-Detect" para que a etapa tenha um nome mais descritivo.

   ![Detalhes da operação](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros desejados.

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP + Swagger, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Continue criando o fluxo de trabalho do aplicativo lógico com ações que são executadas quando o gatilho é acionado.

1. Quando tiver terminado, lembre-se de salvar seu aplicativo lógico. Na barra de ferramentas do designer, selecione **salvar**.

## <a name="add-an-http--swagger-action"></a>Adicionar uma ação de HTTP + Swagger

Essa ação interna faz uma solicitação HTTP para a URL do arquivo Swagger que descreve uma API REST e retorna uma resposta que contém o conteúdo desse arquivo.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico no designer de aplicativo lógico.

1. Na etapa em que você deseja adicionar a ação HTTP + Swagger, selecione **nova etapa**.

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. No designer, na caixa de pesquisa, digite "Swagger" como filtro. Na lista **ações** , selecione a ação **http + Swagger** .

    ![Selecionar ação HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Na caixa **URL do ponto de extremidade do SWAGGER** , insira a URL para o arquivo SWAGGER e selecione **Avançar**.

   Este exemplo usa a URL do Swagger localizada na região oeste dos EUA para o [API de detecção facial de serviços cognitivas](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Insira a URL para o ponto de extremidade do Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Quando o designer mostra as operações descritas pelo arquivo Swagger, selecione a operação que você deseja usar.

   ![Operações no arquivo do Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Forneça os valores para os parâmetros de ação, que variam de acordo com a operação selecionada, que você deseja incluir na chamada do ponto de extremidade.

   Este exemplo não tem parâmetros, mas renomeia a ação como "HTTP + Swagger ação: face para identificação" para que a etapa tenha um nome mais descritivo.

   ![Detalhes da operação](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros desejados.

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP + Swagger, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Quando tiver terminado, lembre-se de salvar seu aplicativo lógico. Na barra de ferramentas do designer, selecione **salvar**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Hospedar o Swagger no armazenamento do Azure

Você pode fazer referência a um arquivo do Swagger que não está hospedado ou que não atende aos requisitos de segurança e entre origens carregando esse arquivo no contêiner de BLOB em uma conta de armazenamento do Azure e habilitando o CORS nessa conta de armazenamento. Para criar, configurar e armazenar arquivos do Swagger no armazenamento do Azure, siga estas etapas:

1. [Criar uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md).

1. Agora habilite o CORS para o blob. No menu da sua conta de armazenamento, selecione **CORS**. Na guia **serviço blob** , especifique esses valores e, em seguida, selecione **salvar**.

   | Propriedade | Valor |
   |----------|-------|
   | **Origens permitidas** | `*` |
   | **Métodos permitidos** | `GET`, `HEAD`, `PUT` |
   | **Cabeçalhos permitidos** | `*` |
   | **Cabeçalhos expostos** | `*` |
   | **Idade máxima** (em segundos) | `200` |
   |||

   Embora este exemplo use a [portal do Azure](https://portal.azure.com), você pode usar uma ferramenta como [Gerenciador de armazenamento do Azure](https://storageexplorer.com/)ou definir essa configuração automaticamente usando este [script do PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)de exemplo.

1. [Crie um contêiner de BLOBs](../storage/blobs/storage-quickstart-blobs-portal.md). No painel **visão geral** do contêiner, selecione **alterar nível de acesso**. Na lista **nível de acesso público** , selecione **BLOB (acesso de leitura anônimo somente para BLOBs)** e selecione **OK**.

1. [Carregue o arquivo do Swagger no contêiner de BLOBs](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), por meio do [portal do Azure](https://portal.azure.com) ou [Gerenciador de armazenamento do Azure](https://storageexplorer.com/).

1. Para fazer referência ao arquivo no contêiner de BLOB, use um link HTTPS que segue esse formato, que diferencia maiúsculas de minúsculas:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Referência do conector

Aqui estão mais informações sobre as saídas de um gatilho ou ação HTTP + Swagger. A chamada HTTP + Swagger retorna estas informações:

| Nome da propriedade | Tipo | Descrição |
|---------------|------|-------------|
| conector | objeto | Os cabeçalhos da solicitação |
| conteúdo | objeto | Objeto JSON | O objeto com o conteúdo do corpo da solicitação |
| código de status | int | O código de status da solicitação |
|||

| Código de estado | Descrição |
|-------------|-------------|
| 200 | OK |
| 202 | Aceitar |
| 400 | Solicitação inadequada |
| 401 | Não autorizado |
| 403 | Proibido |
| 404 | Não encontrado |
| 500 | Erro interno do servidor. Ocorreu um erro desconhecido. |
|||

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
