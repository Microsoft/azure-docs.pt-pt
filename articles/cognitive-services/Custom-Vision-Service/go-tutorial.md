---
title: 'Início rápido: criar um projeto de classificação de imagem com o SDK do Visão Personalizada para go'
titleSuffix: Azure Cognitive Services
description: Crie um projeto, adicione marcas, carregue imagens, treine seu projeto e faça uma previsão usando o SDK do go.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: areddish
ms.openlocfilehash: 7f78230028b66fb12e52f7d5e96cbecbfe8b99a3
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "73519475"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-go-sdk"></a>Início rápido: criar um projeto de classificação de imagem com o SDK do Visão Personalizada go

Este artigo fornece informações e código de exemplo para ajudá-lo a começar a usar o SDK do Visão Personalizada com o Go para criar um modelo de classificação de imagem. Depois de criado, você pode adicionar marcas, carregar imagens, treinar o projeto, obter a URL de ponto de extremidade de previsão publicada do projeto e usar o ponto de extremidade para testar programaticamente uma imagem. Use este exemplo como um modelo para criar seu próprio aplicativo go. Se quiser percorrer o processo de compilar e utilizar um modelo de classificação _sem_ recorrer a código, veja antes as [orientações baseadas no browser](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Ir para 1,8 +](https://golang.org/doc/install)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>Instalar o SDK da Visão Personalizada

Para instalar o SDK do serviço de Visão Personalizada para o go, execute o seguinte comando no PowerShell:

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

ou, se você usar `dep`, em seu repositório, execute:
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Adicionar o código

Crie um novo arquivo chamado *Sample. vá* em seu diretório de projeto preferencial.

### <a name="create-the-custom-vision-service-project"></a>Criar o projeto do serviço de Visão Personalizada

Adicione o código seguinte ao seu script para criar um novo projeto do serviço de Visão Personalizada. Insira as chaves de subscrição nas definições apropriadas. Além disso, obtenha a URL do ponto de extremidade na página Configurações do site Visão Personalizada.

Consulte o método [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) para especificar outras opções ao criar seu projeto (explicado no guia [criar um](getting-started-build-a-classifier.md) portal da Web do classificador).

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    prediction_resource_id = "<your prediction resource id>"
    endpoint string = "<your endpoint URL>"
    project_name string = "Go Sample Project"
    iteration_publish_name = "classifyModel"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    project, err := trainer.CreateProject(ctx, project_name, "sample project", nil, string(training.Multilabel))
    if (err != nil) {
        log.Fatal(err)
    }
```

### <a name="create-tags-in-the-project"></a>Criar etiquetas no projeto

Para criar marcas de classificação para seu projeto, adicione o seguinte código ao final do *exemplo. go*:

```go
// Make two tags in the new project
hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

### <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

Para adicionar as imagens de exemplo ao projeto, insira o seguinte código após a criação da etiqueta. Este código carrega cada imagem com a etiqueta correspondente. Você pode carregar até 64 imagens em um único lote.

> [!NOTE]
> Você precisará alterar o caminho para as imagens, de acordo com o local em que você baixou o projeto de exemplos do SDK de serviços cognitivas go anteriormente.

```go
fmt.Println("Adding images...")
japaneseCherryImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Japanese Cherry"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

hemLockImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Hemlock"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

for _, file := range hemLockImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Hemlock", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))

    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ hemlockTag.ID.String() })
}

for _, file := range japaneseCherryImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Japanese Cherry", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))
    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ cherryTag.ID.String() })
}
```

### <a name="train-the-classifier-and-publish"></a>Treinar o classificador e publicar

Esse código cria a primeira iteração no projeto e, em seguida, publica essa iteração no ponto de extremidade de previsão. O nome fornecido para a iteração publicada pode ser usado para enviar solicitações de previsão. Uma iteração não está disponível no ponto de extremidade de previsão até que seja publicada.

```go
fmt.Println("Training...")
iteration, _ := trainer.TrainProject(ctx, *project.ID)
for {
    if *iteration.Status != "Training" {
        break
    }
    fmt.Println("Training status: " + *iteration.Status)
    time.Sleep(1 * time.Second)
    iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
}
fmt.Println("Training status: " + *iteration.Status)

trainer.PublishIteration(ctx, *project.ID, *iteration.ID, iteration_publish_name, prediction_resource_id))
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Obter e usar a iteração publicada no ponto de extremidade de previsão

Para enviar uma imagem para o ponto final de predição e obter a mesma, adicione o seguinte código no fim do ficheiro:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Executar a aplicação

Execute o *exemplo. go*.

```shell
go run sample.go
```

O resultado da aplicação deverá ser semelhante ao seguinte texto:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Depois, pode confirmar que a imagem de teste (disponível em **<base_image_url>/Images/Test/** ) é etiquetada adequadamente. Também pode regressar ao [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto criado recentemente.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passos seguintes

Viu como cada passo do processo de classificação de imagens pode ser feito no código. Este exemplo executa uma iteração de preparação individual, mas, muitas vezes, terá de preparar e testar o seu modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)