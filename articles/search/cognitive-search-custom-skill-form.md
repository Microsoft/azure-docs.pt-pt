---
title: Habilidade personalizada do Reconhecimento de Formulários (C#)
titleSuffix: Azure Cognitive Search
description: Saiba como criar uma habilidade personalizada do Form Recogniser utilizando O Estúdio C# e Visual.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: a3b073cdb90e0c427bfbca15c1440b9122672610
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880140"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Exemplo: Criar uma habilidade personalizada do Reconhecimento de Formulários

Neste exemplo de skillset de pesquisa cognitiva Azure, você aprenderá a criar uma habilidade personalizada de Form Recogniser usando C# e Visual Studio. O Reconhecimento de Formulários analisa documentos e extrai pares chave/valor e dados de tabela. Ao embrulhar o Form Recogniser na [interface de habilidade personalizada,](cognitive-search-custom-skill-interface.md)pode adicionar esta capacidade como um passo num pipeline de enriquecimento de ponta a ponta. O gasoduto pode então carregar os documentos e fazer outras transformações.

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (qualquer edição).
- Pelo menos cinco formas do mesmo tipo. Pode utilizar os dados da amostra fornecidos com este guia.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulários

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Preparar o seu modelo

Terá de treinar um modelo de Reconhecimento de Formulários com os seus formulários de entrada antes de utilizar esta habilidade. Siga o arranque rápido da [cURL](../cognitive-services/form-recognizer/quickstarts/client-library.md?pivots=programming-language-rest-api) para aprender a treinar um modelo. Pode utilizar os formulários de amostra fornecidos nesse arranque rápido, ou pode utilizar os seus próprios dados. Uma vez treinado o modelo, copie o seu valor de ID para um local seguro.

## <a name="set-up-the-custom-skill"></a>Configurar a habilidade personalizada

Este tutorial utiliza o projeto [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) no repositório [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) GitHub. Clone este repositório para a sua máquina local e navegue para **Vision/AnalyzeForm/** para aceder ao projeto. Em _seguida, abra AnalyzeForm.csproj_ em Estúdio Visual. Este projeto cria um recurso Azure Function que cumpre a [interface de habilidade personalizada](cognitive-search-custom-skill-interface.md) e pode ser usado para enriquecimento de Pesquisa Cognitiva Azure. Toma os documentos de forma como entradas, e produz (como texto) os pares chave/valor que especifica.

Primeiro, adicione variáveis ambientais ao nível do projeto. Localize o projeto **AnalyzeForm** no painel esquerdo, clique com o botão direito e selecione **Propriedades**. Na janela **Propriedades,** clique no separador **Debug** e, em seguida, encontre o campo **de variáveis Ambiente.** Clique **em Adicionar** para adicionar as seguintes variáveis:
* `FORMS_RECOGNIZER_ENDPOINT_URL` com o valor definido para o seu URL de ponto final.
* `FORMS_RECOGNIZER_API_KEY` com o valor definido para a sua chave de subscrição.
* `FORMS_RECOGNIZER_MODEL_ID` com o valor definido para o ID do modelo que treinou.
* `FORMS_RECOGNIZER_RETRY_DELAY` com o valor definido para 1000. Este valor é o tempo em milissegundos que o programa vai esperar antes de voltar a tentar a consulta.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS` com o valor definido para 100. Este valor é o número de vezes que o programa irá consultar o serviço enquanto tenta obter uma resposta bem sucedida.

Em seguida, abra _o AnalyzeForm.cs_ e encontre a `fieldMappings` variável, que faz referência ao *field-mappings.jsno* ficheiro. Este ficheiro (e a variável que o referencia) define a lista de chaves que pretende extrair dos seus formulários e uma etiqueta personalizada para cada tecla. Por exemplo, um valor de `{ "Address:", "address" }, { "Invoice For:", "recipient" }` meios o script apenas irá guardar os valores para os `Address:` campos e campos `Invoice For:` detetados, e irá rotular esses valores com `"address"` `"recipient"` e, respectivamente.

Finalmente, note a `contentType` variável. Este script executa o modelo de Reconhecimento de Formulários em documentos remotos que são referenciados por URL, pelo que o tipo de conteúdo é `application/json` . Se pretender analisar os ficheiros locais, incluindo os seus streams de byte nos pedidos HTTP, terá de alterar o `contentType` [tipo MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) apropriado para o seu ficheiro.

## <a name="test-the-function-from-visual-studio"></a>Teste a função do Visual Studio

Depois de editar o seu projeto, guarde-o e desemalte o projeto **AnalyzeForm** como o projeto de arranque no Visual Studio (se ainda não estiver definido). Em seguida, prima **F5** para executar a função no ambiente local. Use um serviço REST como [o Carteiro](https://www.postman.com/) para ligar para a função.

### <a name="http-request"></a>Pedido HTTP

Fará o seguinte pedido para ligar para a função.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Corpo do pedido

Comece com o modelo do corpo do pedido abaixo.

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

Aqui você precisará fornecer o URL de um formulário que tem o mesmo tipo que os formulários com que treinou. Para efeitos de teste, pode utilizar um dos seus formulários de treino. Se seguiu o quickstart da CURL, os seus formulários estarão localizados numa conta de armazenamento de bolhas Azure. Abra o Azure Storage Explorer, localize um ficheiro de formulário, clique com o botão direito e selecione **Obter Assinatura de Acesso Partilhado**. A próxima janela de diálogo fornecerá um sinal de URL e SAS. Insira estas cordas nos `"formUrl"` campos e campos do seu corpo `"formSasToken"` solicitado, respectivamente.

> [!div class="mx-imgBorder"]
> ![Explorador de armazenamento Azure; um documento pdf é selecionado](media/cognitive-search-skill-form/form-sas.png)

Se quiser analisar um documento remoto que não esteja no armazenamento de blob Azure, cole o URL no `"formUrl"` campo e deixe o campo em `"formSasToken"` branco.

> [!NOTE]
> Quando a habilidade é integrada num skillset, o URL e o token serão fornecidos pela Cognitive Search.

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

1. No **Solution Explorer** em Visual Studio, clique com o botão direito no projeto e selecione **Publicar.** Escolha **Criar Nova**  >  **Publicação.**
1. Se ainda não ligou o Visual Studio à sua conta Azure, selecione **Adicionar uma conta....**
1. Siga as instruções no ecrã. Especifique um nome único para o seu serviço de aplicações, a subscrição Azure, o grupo de recursos, o plano de hospedagem e a conta de armazenamento que pretende utilizar. Você pode criar um novo grupo de recursos, um novo plano de hospedagem, e uma nova conta de armazenamento se você não tiver isso. Quando terminar, **selecione Criar**.
1. Depois da implementação estar concluída, note o URL do site. Este URL é o endereço da sua aplicação de função em Azure. Guarde-o para um local temporário.
1. No [portal Azure,](https://portal.azure.com)navegue para o Grupo de Recursos e procure a `AnalyzeForm` Função que publicou. Sob a secção **'Gerir',** deverá ver as Teclas do anfitrião. Copie a chave de anfitrião *predefinido* e guarde-a para um local temporário.

## <a name="connect-to-your-pipeline"></a>Ligue-se ao seu oleoduto

Para utilizar esta habilidade num pipeline de Pesquisa Cognitiva, terá de adicionar uma definição de habilidade ao seu skillset. O bloco JSON a seguir é uma definição de habilidade de amostra (deve atualizar as entradas e saídas para refletir o seu cenário e ambiente de skillset particulares). `AzureFunctionEndpointUrl`Substitua-o pelo URL de função e `AzureFunctionDefaultHostKey` substitua-o pela tecla de anfitrião.

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

Neste guia, criou uma habilidade personalizada a partir do serviço Azure Form Recogniser. Para saber mais sobre habilidades personalizadas, consulte os seguintes recursos. 

* [Azure Search Power Skills: um repositório de habilidades personalizadas](https://github.com/Azure-Samples/azure-search-power-skills)
* [Adicione uma habilidade personalizada a um oleoduto de enriquecimento de IA](cognitive-search-custom-skill-interface.md)
* [Definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
* [Criar um skillset (REST)](/rest/api/searchservice/create-skillset)
* [Mapa de campos enriquecidos](cognitive-search-output-field-mapping.md)