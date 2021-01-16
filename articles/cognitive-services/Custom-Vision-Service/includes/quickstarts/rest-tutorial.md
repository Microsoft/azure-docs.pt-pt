---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: 8890a0132e5f510a0af2862c483206fd025a68d8
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256447"
---
Começa com a API De Visão Personalizada. Siga estes passos para chamar a API e construir um modelo de classificação de imagem. Você vai criar um projeto, adicionar tags, treinar o projeto, e usar o URL de previsão do projeto para testá-lo programáticamente. Use este exemplo como um modelo para construir a sua própria app de reconhecimento de imagem.

> [!NOTE]
> A Visão Personalizada é mais facilmente utilizada através de uma biblioteca de clientes SDK ou através da [orientação baseada no navegador.](../../get-started-build-detector.md)

Utilize a biblioteca de clientes Custom Vision para .NET para:

* Criar um novo projeto de Visão Personalizada
* Adicione tags ao projeto
* Enviar e marcar imagens
* Treine o projeto
* Publique a iteração atual
* Teste o ponto final de previsão

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" Crie um recurso De Visão Personalizada crie um recurso De "  target="_blank"> Visão Personalizada no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para criar um recurso de formação e previsão e obter as suas chaves e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final dos recursos que cria para ligar a sua aplicação à Visão Personalizada. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.


## <a name="create-a-new-custom-vision-project"></a>Criar um novo projeto de Visão Personalizada

Usará um comando como o seguinte para criar um projeto de classificação de imagem. O projeto criado aparecerá no site da [Visão Personalizada.](https://customvision.ai/)


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

Copie o comando a um editor de texto e faça as seguintes alterações:

* `{subscription key}`Substitua-a pela sua tecla de subscrição Face válida.
* `{endpoint}`Substitua-a pelo ponto final que corresponda à sua chave de subscrição.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* `{name}`Substitua-o pelo nome do seu projeto.
* Configurar opcionalmente outros parâmetros de URL para configurar que tipo de modelo o seu projeto irá utilizar. Consulte a API do [CreatProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) para obter opções.

Receberá uma resposta JSON como a seguinte. Guarde o `"id"` valor do seu projeto para um local temporário.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="add-tags-to-the-project"></a>Adicione tags ao projeto

Utilize o seguinte comando para definir as etiquetas em que irá treinar o modelo.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* Mais uma vez, insira a sua própria chave e URL de ponto final.
* `{projectId}`Substitua-se pelo seu próprio ID do projeto.
* `{name}`Substitua-o pelo nome da etiqueta que pretende utilizar.

Repita este processo para todas as etiquetas que gostaria de usar no seu projeto. Se estiver a utilizar as imagens de exemplo fornecidas, adicione as etiquetas `"Hemlock"` e `"Japanese Cherry"` .

Receberá uma resposta JSON como a seguinte. Guarde o `"id"` valor de cada etiqueta para um local temporário.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Em seguida, descarregue as imagens de amostra para este projeto. Guarde o conteúdo da [pasta Imagens](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) da amostra para o seu dispositivo local.

> [!NOTE]
> O Trove, um projeto da Microsoft Garage, permite-lhe recolher e adquirir conjuntos de imagens para fins de treino. Uma vez recolhidas as suas imagens, pode descarregá-las e depois importá-las para o seu projeto De Visão Personalizada da forma habitual. Visite a [página do Trove](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) para saber mais.

Utilize o seguinte comando para fazer o upload das imagens e aplicar tags; uma vez para as imagens "Cicuta", e separadamente para as imagens "Cereja Japonesa". Consulte as [imagens Create API de dados](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5) para obter mais opções.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* Mais uma vez, insira a sua própria chave e URL de ponto final.
* `{projectId}`Substitua-se pelo seu próprio ID do projeto.
* `{tagArray}`Substitua-a pela identificação de uma etiqueta.
* Em seguida, povoar o corpo do pedido com os dados binários das imagens que pretende marcar.

## <a name="train-the-project"></a>Treine o projeto

Este método treina o modelo nas imagens marcadas que carregou e devolve um ID para a iteração do projeto atual.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* Mais uma vez, insira a sua própria chave e URL de ponto final.
* `{projectId}`Substitua-se pelo seu próprio ID do projeto.
* `{tagArray}`Substitua-a pela identificação de uma etiqueta.
* Em seguida, povoar o corpo do pedido com os dados binários das imagens que pretende marcar.
* Utilize opcionalmente outros parâmetros URL. Consulte o [Projeto API do Projeto Comboio](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) para obter opções.

> [!TIP]
> Comboio com etiquetas selecionadas
>
> Pode treinar opcionalmente apenas um subconjunto das suas etiquetas aplicadas. Podes querer fazer isto se ainda não aplicaste o suficiente de certas etiquetas, mas tens o suficiente de outras. Adicione o conteúdo opcional do JSON ao corpo do seu pedido. Povoar a `"selectedTags"` matriz com os IDs das etiquetas que pretende utilizar.
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

A resposta JSON contém informações sobre o seu projeto treinado, incluindo o ID de iteração ( `"id"` ). Guarde este valor para o próximo passo.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>Publique a iteração atual

Este método disponibiliza a iteração atual do modelo para consulta. Utiliza o nome do modelo devolvido como referência para enviar pedidos de previsão. 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Mais uma vez, insira a sua própria chave e URL de ponto final.
* `{projectId}`Substitua-se pelo seu próprio ID do projeto.
* `{iterationId}`Substitua-a pelo ID devolvido no passo anterior.
* `{publishedName}`Substitua-o pelo nome que pretende atribuir ao seu modelo de previsão.
* `{predictionId}`Substitua-o pelo seu próprio ID de recursos de previsão. Pode encontrá-lo no **separador Visão Geral** do seu recurso de previsão no portal Azure, listado como **ID de subscrição**.
* Utilize opcionalmente outros parâmetros URL. Consulte a [API de Iteração De Publicação.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5)

## <a name="test-the-prediction-endpoint"></a>Teste o ponto final de previsão

Por fim, utilize este comando para testar o seu modelo treinado, enviando uma nova imagem para que possa classificar com etiquetas. Pode utilizar a imagem na pasta "Teste" dos ficheiros de amostra que descarregou anteriormente.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Mais uma vez, insira a sua própria chave e URL de ponto final.
* `{projectId}`Substitua-se pelo seu próprio ID do projeto.
* `{publishedName}`Substitua-o pelo nome utilizado no passo anterior.
* Adicione os dados binários da sua imagem local ao organismo de pedido.
* Utilize opcionalmente outros parâmetros URL. Consulte a API [de Imagem Classificada.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)

A resposta JSON devolvida irá pelo menos cada uma das tags que o modelo aplicou à sua imagem, juntamente com pontuações de probabilidade para cada tag. 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passos seguintes

Agora já fez todos os passos do processo de classificação de imagem usando a API REST. Esta amostra executa uma única iteração de treino, mas muitas vezes você precisa treinar e testar o seu modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Test and retrain a model](../../test-your-model.md) (Testar e voltar a preparar um modelo)

* [O que é a Visão Personalizada?](../../overview.md)
* [Documentação de referência da API (formação)](/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)
* [Documentação de referência da API (previsão)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)
