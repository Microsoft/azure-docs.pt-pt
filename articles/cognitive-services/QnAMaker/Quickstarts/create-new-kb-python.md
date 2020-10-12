---
title: 'Início Rápido: criar base de dados de conhecimento - REST, Python - Criador de FAQ'
description: Este início rápido baseado em REST do Python descreve a criação programática de uma base de dados de conhecimento do Criador de FAQ de exemplo, que será apresentada no Dashboard do Azure da sua conta da API dos Serviços Cognitivos.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: 6155722b81b32e426a91a283910e8bdce926e342
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777644"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>Início Rápido: criar uma base de dados de conhecimento no Criador de FAQ com o Python

Este início rápido descreve a criação e publicação, através de programação, de uma base de dados de conhecimento do Criador de FAQ. O Criador de FAQ extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como FAQs, a partir de [origens de dados](../Concepts/knowledge-base.md). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo do pedido da API.

Este início rápido chama as API do Criador de FAQ:
* [Criar KB](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Obter Detalhes da operação](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Amostra de Python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.7](https://www.python.org/downloads/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave e ponto final (que inclui o nome do recurso), selecione **Quickstart** para o seu recurso no portal Azure.

## <a name="create-a-knowledge-base-python-file"></a>Criar um ficheiro do Python de base de dados de conhecimento

Crie um ficheiro com o nome `create-new-knowledge-base-3x.py`.

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do `create-new-knowledge-base-3x.py`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="dependencies":::

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias
Depois das dependências necessárias anteriores, adicione as constantes necessárias para aceder ao Criador de FAQ. Substitua o valor da `<your-qna-maker-subscription-key>` e pela sua própria chave e nome de recurso `<your-resource-name>` QnA Maker.

No topo da classe Program, adicione as constantes necessárias para aceder ao QnA Maker.

Defina os seguintes valores:

* `<your-qna-maker-subscription-key>` - A **chave** é uma cadeia de 32 caracteres e está disponível no portal Azure, no recurso QnA Maker, na página Quickstart. Isto não é o mesmo que a chave final de previsão.
* `<your-resource-name>` - O seu **nome de recurso** é utilizado para a construção do URL de ponta de autoria para autoria, no formato de `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Este não é o mesmo URL usado para consultar o ponto final de previsão.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="constants":::

## <a name="add-the-kb-model-definition"></a>Adicionar a definição de modelo de KB

Depois das constantes, adicione a seguinte definição de modelo de KB. Este modelo converte-se numa cadeia de carateres após a definição.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="model":::

## <a name="add-supporting-function"></a>Adicionar função de suporte

Adicione a seguinte função para imprimir JSON num formato legível:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="pretty":::

## <a name="add-function-to-create-kb"></a>Adicionar função para criar KB

Adicione a seguinte função para fazer um pedido POST de HTTP para criar a base de dados de conhecimento.
Esta chamada à API devolve uma resposta JSON, que inclui o ID da operação no campo do cabeçalho **Localização**. Utilize o ID da operação para determinar se a KB for criada com êxito. A `Ocp-Apim-Subscription-Key` é a chave de serviço do Criador de FAQ utilizada para autenticação.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="create_kb":::

Esta chamada à API devolve uma resposta JSON, que inclui o ID da operação. Utilize o ID da operação para determinar se a KB for criada com êxito.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-function-to-check-creation-status"></a>Adicionar função para verificar o estado de criação

A seguinte função verifica o estado de criação a enviar no ID de operação no fim da rota do URL. A chamada para `check_status` está dentro do ciclo principal _enquanto_.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="get_status":::

Esta chamada à API devolve uma resposta JSON que inclui o estado da operação:

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Repita a chamada até ter êxito ou falhar:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-main-code-block"></a>Adicionar bloco de código principal
O seguinte ciclo consulta o estado da operação de criação periodicamente até a operação estar concluída.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="main":::

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Introduza o seguinte comando numa linha de comandos para executar o programa. Enviará o pedido à API do Criador de FAQ para criar a KB e, em seguida, irá consultar para obter os resultados a cada 30 segundos. Cada resposta é impressa na janela da consola.

```bash
python create-new-knowledge-base-3x.py
```

Assim que a sua base de dados de conhecimento é criada, pode visualizá-la no seu Portal do Criador de FAQ, na página [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (As minhas bases de dados de conhecimento). Selecione o nome da sua base de dados de conhecimento, por exemplo, FAQ do Criador de FAQ, para a visualizar.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
