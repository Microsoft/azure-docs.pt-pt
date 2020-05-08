---
title: Habilidade personalizada do Reconhecimento de Formulário (C#)
titleSuffix: Azure Cognitive Search
description: Aprenda a criar uma habilidade personalizada do Reconhecimento de Formulários usando C# e Visual Studio.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: 050848b0bff65b19e2b17bd170e1d3e9ff0176f1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792008"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Exemplo: Criar uma habilidade personalizada de Reconhecimento de Formulários

Neste exemplo de habilidade seletiva de Pesquisa Cognitiva Azure, você vai aprender a criar uma habilidade personalizada de Reconhecimento de Formulário usando C# e Visual Studio. O Reconhecimento de Formulárioanalisa documentos e extrai os pares chave/valor e os dados da tabela. Ao embrulhar o Reconhecimento de Formulários na interface de [habilidades personalizadas,](cognitive-search-custom-skill-interface.md)pode adicionar esta capacidade como um passo num pipeline de enriquecimento de ponta a ponta. O gasoduto pode então carregar os documentos e fazer outras transformações.

## <a name="prerequisites"></a>Pré-requisitos

- [Estúdio Visual 2019](https://visualstudio.microsoft.com/downloads/) (qualquer edição).
- Pelo menos cinco formas do mesmo tipo. Pode utilizar os dados da amostra fornecidos com este guia.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulário

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Preparar o seu modelo

Terá de treinar um modelo 'Reconhecimento de Formulários' com os seus formulários de entrada antes de utilizar esta habilidade. Siga o [cURL quickstart](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/curl-train-extract) para aprender a treinar um modelo. Pode utilizar os formulários de amostra fornecidos nesse arranque rápido, ou pode utilizar os seus próprios dados. Uma vez treinado o modelo, copie o seu valor de identificação para um local seguro.

## <a name="set-up-the-custom-skill"></a>Configurar a habilidade personalizada

Este tutorial utiliza o projeto [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) no repositório [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) GitHub. Clone este repositório à sua máquina local e navegue até à **Vision/AnalyzeForm/** para aceder ao projeto. Em seguida, abra _o AnalyzeForm.csproj_ no Estúdio Visual. Este projeto cria um recurso Azure Function que cumpre a [interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md) e pode ser usado para enriquecimento de Pesquisa Cognitiva Azure. Assume documentos de formulário como entradas, e produz (como texto) os pares chave/valor que especifica.

Em primeiro lugar, adicione variáveis ambientais ao nível do projeto. Localize o projeto **AnalyzeForm** no painel esquerdo, clique à direita e selecione **Propriedades**. Na janela **Propriedades,** clique no separador **Debug** e, em seguida, encontre o campo **de variáveis Ambiente.** Clique em **Adicionar** para adicionar as seguintes variáveis:
* `FORMS_RECOGNIZER_ENDPOINT_URL`com o valor definido para o seu URL de ponto final.
* `FORMS_RECOGNIZER_API_KEY`com o valor definido para a sua chave de subscrição.
* `FORMS_RECOGNIZER_MODEL_ID`com o valor definido para a identificação do modelo que treinou.
* `FORMS_RECOGNIZER_RETRY_DELAY`com o valor definido para 1000. Este valor é o tempo em milissegundos que o programa vai esperar antes de voltar a experimentar a consulta.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS`com o valor definido para 100. Este valor é o número de vezes que o programa irá consultar o serviço enquanto tenta obter uma resposta bem sucedida.

Em seguida, abrir `fieldMappings` _AnalyzeForm.cs_ e encontrar a variável, que refere o ficheiro *field-mappings.json.* Este ficheiro (e a variável que o refere) define a lista de teclas que pretende extrair dos seus formulários e uma etiqueta personalizada para cada tecla. Por exemplo, um `{ "Address:", "address" }, { "Invoice For:", "recipient" }` valor de meios o script apenas `Invoice For:` irá guardar os valores `"address"` `"recipient"`para os campos e campos detetados, `Address:` e irá rotular esses valores com e, respectivamente.

Finalmente, note `contentType` a variável. Este script executa o modelo de reconhecimento de formulário dado em documentos remotos que são referenciados por URL, pelo que o tipo de conteúdo é `application/json`. Se pretender analisar os ficheiros locais, incluindo os seus fluxos de `contentType` byte nos pedidos HTTP, terá de alterar o [tipo MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) apropriado para o seu ficheiro.

## <a name="test-the-function-from-visual-studio"></a>Teste a função do Estúdio Visual

Depois de editar o seu projeto, guarde-o e desloque o projeto **AnalyzeForm** como o projeto de arranque no Visual Studio (se ainda não estiver definido). Em seguida, pressione **F5** para executar a função no seu ambiente local. Use um serviço REST como [o Carteiro](https://www.postman.com/) para ligar para a função.

### <a name="http-request"></a>Pedido http

Fará o seguinte pedido para ligar para a função.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Corpo do pedido

Comece com o modelo de corpo de pedido abaixo.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

Aqui terá de fornecer o URL de um formulário que tenha o mesmo tipo que os formulários com que treinou. Para efeitos de teste, pode usar um dos seus formulários de treino. Se seguiu o arranque rápido do cURL, os seus formulários estarão localizados numa conta de armazenamento de blob Azure. Abra o Explorador de Armazenamento Azure, localize um ficheiro de formulário, clique-o à direita e selecione Obter Assinatura de **Acesso Partilhado**. A próxima janela de diálogo fornecerá um URL e um token SAS. Introduza estas cordas `"formUrl"` `"formSasToken"` nos campos e campos do seu corpo de pedido, respectivamente.

> [!div class="mx-imgBorder"]
> ![Explorador de armazenamento azure; um documento pdf é selecionado](media/cognitive-search-skill-form/form-sas.png)

Se quiser analisar um documento remoto que não esteja no armazenamento de blob Azure, colhe o `"formUrl"` seu URL no campo e deixe o `"formSasToken"` campo em branco.

> [!NOTE]
> Quando a habilidade é integrada num conjunto de habilidades, o URL e o token serão fornecidos pela Cognitive Search.

### <a name="response"></a>Resposta

Deve ver uma resposta semelhante ao seguinte exemplo:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicar a função no Azure

Quando estiver satisfeito com o comportamento da função, pode publicá-lo.

1. No **Solution Explorer** em Visual Studio, clique no projeto e selecione **Publicar**. Escolha **criar nova** > **publicação.**
1. Se ainda não ligou o Visual Studio à sua conta Azure, selecione **Adicionar uma conta....**
1. Siga as instruções no ecrã. Especifique um nome único para o seu serviço de aplicações, a subscrição Do Azure, o grupo de recursos, o plano de hospedagem e a conta de armazenamento que pretende utilizar. Você pode criar um novo grupo de recursos, um novo plano de hospedagem, e uma nova conta de armazenamento se você ainda não tiver estes. Quando terminar, selecione **Criar**.
1. Depois de concluída a implementação, note o URL do Site. Este URL é o endereço da sua aplicação de funções em Azure. Guarde-o para um local temporário.
1. No [portal Azure,](https://portal.azure.com)navegue para o Grupo `AnalyzeForm` de Recursos e procure a Função que publicou. Na secção **Gerir,** deve ver as Teclas do Anfitrião. Copie a chave de hospedar *padrão* e guarde-a para um local temporário.

## <a name="connect-to-your-pipeline"></a>Ligue-se ao seu oleoduto

Para utilizar esta habilidade num oleoduto de Pesquisa Cognitiva, terá de adicionar uma definição de habilidade à sua habilidade. O seguinte bloco JSON é uma definição de habilidade de amostra (deve atualizar as inputs e saídas para refletir o seu cenário particular e o ambiente de habilidades). Substitua-a `AzureFunctionEndpointUrl` com o `AzureFunctionDefaultHostKey` URL da sua função e substitua-a pela chave do hospedeiro.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

Neste guia, criou uma habilidade personalizada a partir do serviço De Reconhecimento de Formulários Azure. Para saber mais sobre habilidades personalizadas, consulte os seguintes recursos. 

* [Azure Search Power Skills: um repositório de habilidades personalizadas](https://github.com/Azure-Samples/azure-search-power-skills)
* [Adicione uma habilidade personalizada a um oleoduto de enriquecimento de IA](cognitive-search-custom-skill-interface.md)
* [Definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
* [Criar uma habilidade (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
* [Mapa campos enriquecidos](cognitive-search-output-field-mapping.md)
