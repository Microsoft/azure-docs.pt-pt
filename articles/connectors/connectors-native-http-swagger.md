---
title: Chamar pontos finais REST a partir do Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que se comunicam com pontos finais REST utilizando o HTTP + Swagger connector no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
tags: connectors
ms.topic: article
ms.date: 07/18/2016
ms.openlocfilehash: de61443da41f1015b2890e9052f38e25d3bed86a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551981"
---
# <a name="call-rest-endpoints-with-http--swagger-connector-in-azure-logic-apps"></a>Chamar pontos finais REST com HTTP + Swagger connector no Azure Logic Apps

Pode criar um conector de primeira classe para qualquer ponto final REST através de um [documento Swagger](https://swagger.io) quando utiliza o HTTP + Swagger ação no seu fluxo de trabalho de aplicação lógica. Pode ainda expandir o logic apps para chamar qualquer ponto final REST com uma experiência de Estruturador da aplicação lógica de primeira classe.

Para saber como criar aplicações lógicas com conectores, veja [criar uma nova aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Utilizar HTTP + Swagger como um acionador ou uma ação

O HTTP + Swagger acionar e ação funcionam da mesma forma como o [ação de HTTP](connectors-native-http.md) mas proporcionar uma melhor experiência no Estruturador da aplicação lógica ao expor a estrutura de API e saídas da [metadados do Swagger](https://swagger.io). Também pode utilizar o HTTP + Swagger connector como acionador. Se pretender implementar um acionador de consulta, siga o padrão de consulta que está descrito em [criar APIs personalizadas para chamar outros APIs, serviços e sistemas de aplicações lógicas](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

Saiba mais sobre [ações e acionadores da aplicação lógica](connectors-overview.md).

Eis um exemplo de como a utilização de HTTP + Swagger operação como uma ação num fluxo de trabalho numa aplicação lógica.

1. Selecione o **novo passo** botão.
2. Selecione **adicionar uma ação**.
3. Na caixa de pesquisa de ação, escreva **swagger** a lista a HTTP + Swagger ação.
   
    ![Selecione HTTP + Swagger ação](./media/connectors-native-http-swagger/using-action-1.png)
4. Escreva o URL para um documento Swagger:
   
   * O URL tem de ser um ponto final HTTPS para trabalhar a partir do Estruturador da aplicação lógica, e tiver ativado o CORS.
   * Se o documento Swagger não cumpre este requisito, pode utilizar o armazenamento do Azure com a CORS ativada para armazenar o documento.
5. Clique em **seguinte** ler e processar do documento Swagger.
6. Adicione todos os parâmetros que são necessários para a chamada HTTP.
   
    ![Ação de HTTP completa](./media/connectors-native-http-swagger/using-action-2.png)
7. Para guardar e publicar a aplicação lógica, clique em **guardar** na barra de ferramentas de designer.

### <a name="host-swagger-from-azure-storage"></a>Swagger de anfitrião do armazenamento do Azure
Pode querer fazer referência a um documento do Swagger que não está alojada, ou que não cumpre os requisitos de várias origens para o designer e segurança. Para resolver este problema, pode armazenar o documento Swagger no armazenamento do Azure e ativar o CORS para o documento de referência.  

Eis os passos para criar, configurar e armazenar documentos de Swagger no armazenamento do Azure:

1. [Criar uma conta de armazenamento do Azure com o armazenamento de Blobs do Azure](../storage/common/storage-create-storage-account.md). Para realizar este passo, defina permissões para **acesso público**.

2. Ative o CORS no blob. 

   Para configurar automaticamente esta definição, pode utilizar [este script do PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

3. Carregue o ficheiro de Swagger para o blob. 

   Pode efetuar este passo do [portal do Azure](https://portal.azure.com) ou a partir de uma ferramenta como [Explorador de armazenamento do Azure](https://storageexplorer.com/).

4. Fazer referência a uma ligação HTTPS para o documento no armazenamento de Blobs do Azure. 

   A ligação utiliza este formato:

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Detalhes técnicos
Seguem-se os detalhes para os acionadores e ações que este HTTP + Swagger connector suporta.

## <a name="http--swagger-triggers"></a>HTTP + Swagger triggers
Um acionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho que está definido numa aplicação lógica. [Saiba mais sobre os acionadores.](connectors-overview.md) O HTTP + Swagger connector tem um acionador.

| Acionamento | Descrição |
| --- | --- |
| HTTP + Swagger |Efetuar uma chamada HTTP e retornar o conteúdo de resposta |

## <a name="http--swagger-actions"></a>HTTP + Swagger ações
Uma ação é uma operação que é executada pelo fluxo de trabalho que está definido numa aplicação lógica. [Saiba mais sobre as ações.](connectors-overview.md) O HTTP + Swagger connector tem uma ação possível.

| Ação | Descrição |
| --- | --- |
| HTTP + Swagger |Efetuar uma chamada HTTP e retornar o conteúdo de resposta |

### <a name="action-details"></a>Detalhes da ação
O HTTP + Swagger connector vem com uma ação possível. Segue-se informações sobre cada uma das ações, seus campos de entrada necessários e opcionais e os detalhes de resultado correspondente, que estão associados a utilização das mesmas.

#### <a name="http--swagger"></a>HTTP + Swagger
Faça uma solicitação de saída HTTP com o auxílio de metadados Swagger.
Um asterisco (*) significa que um campo obrigatório.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| Método * |método |Verbo HTTP para utilizar. |
| URI* |uri |URI do pedido HTTP. |
| Cabeçalhos |Cabeçalhos |Um objeto JSON de cabeçalhos HTTP para incluir. |
| Mensagem |corpo |O corpo do pedido HTTP. |
| Authentication |autenticação |Autenticação a utilizar para o pedido. Para obter mais informações, consulte a [conector HTTP](connectors-native-http.md#authentication). |

**Detalhes de saída**

Resposta HTTP

| Nome da Propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| Cabeçalhos |objeto |Cabeçalhos de resposta |
| Mensagem |objeto |Objeto de resposta |
| Código de Estado |int |Código de estado de HTTP |

### <a name="http-responses"></a>Respostas HTTP
Ao fazer chamadas para várias ações, poderá obter respostas de determinados. Segue-se uma tabela que descreve as respostas correspondentes e descrições.

| Name | Descrição |
| --- | --- |
| 200 |OK |
| 202 |Aceite |
| 400 |Pedido incorreto |
| 401 |Não autorizado |
| 403 |Proibido |
| 404 |Não Encontrado |
| 500 |Erro interno de servidor. Ocorreu um erro desconhecido. |

- - -
## <a name="next-steps"></a>Passos Seguintes

* [Criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Encontre outros conectores](apis-list.md)
