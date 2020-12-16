---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 27eb4383084e9a782e0c2cb8ff734fba8496c9f1
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582562"
---
Este guia fornece instruções e código de amostra para ajudá-lo a começar a usar a biblioteca de clientes Custom Vision para Ir para construir um modelo de classificação de imagem. Você vai criar um projeto, adicionar tags, treinar o projeto, e usar o URL de previsão do projeto para testá-lo programáticamente. Use este exemplo como um modelo para construir a sua própria app de reconhecimento de imagem.

> [!NOTE]
> Se quiser construir e treinar um modelo de classificação _sem_ escrever código, consulte a [orientação baseada no navegador.](../../getting-started-build-a-classifier.md)

## <a name="prerequisites"></a>Pré-requisitos

- [Ir 1.8+](https://golang.org/doc/install)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Instale a biblioteca de clientes Custom Vision

Para escrever uma aplicação de análise de imagem com visão personalizada para ir, você precisará da biblioteca de clientes de serviço de Visão Personalizada. Executar o seguinte comando em PowerShell:

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

ou se `dep` utilizar, dentro da sua corrida de repo:
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [python-get-images](../../includes/python-get-images.md)]

## <a name="add-the-code"></a>Adicionar o código

Crie um novo ficheiro chamado *sample.go* no seu diretório de projeto preferido.

## <a name="create-the-custom-vision-project"></a>Criar o projeto Visão Personalizada

Adicione o código seguinte ao seu script para criar um novo projeto do serviço de Visão Personalizada. Insira as chaves de subscrição nas definições apropriadas. Além disso, obtenha o url do Ponto Final na página de Definições do website Da Visão Personalizada.

Consulte o método [CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) para especificar outras opções quando criar o seu projeto (explicado no guia do portal do [web classificador).](../../getting-started-build-a-classifier.md)

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

## <a name="create-tags-in-the-project"></a>Criar etiquetas no projeto

Para criar etiquetas de classificação ao seu projeto, adicione o seguinte código ao fim da *amostra.go*:

```go
// Make two tags in the new project
hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Para adicionar as imagens de exemplo ao projeto, insira o seguinte código após a criação da etiqueta. Este código carrega cada imagem com a etiqueta correspondente. Pode carregar até 64 imagens num único lote.

> [!NOTE]
> Você precisará mudar o caminho para as imagens com base no local onde você descarregou o projeto Cognitive Services Go SDK Samples mais cedo.

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

## <a name="train-and-publish-the-project"></a>Treine e publique o projeto

Este código cria a primeira iteração do modelo de previsão e, em seguida, publica essa iteração para o ponto final de previsão. O nome dado à iteração publicada pode ser usado para enviar pedidos de previsão. Uma iteração não está disponível no ponto final da previsão até que seja publicada.

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

## <a name="use-the-prediction-endpoint"></a>Use o ponto final da previsão

Para enviar uma imagem para o ponto final de predição e obter a mesma, adicione o seguinte código no fim do ficheiro:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions    {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Executar a aplicação

Executar *amostra.go*.

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

Depois, pode confirmar que a imagem de teste (disponível em **<base_image_url>/Images/Test/**) é etiquetada adequadamente. Também pode regressar ao [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto criado recentemente.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passos seguintes

Agora já viste como cada passo do processo de deteção de objetos pode ser feito em código. Esta amostra executa uma única iteração de treino, mas muitas vezes você precisa treinar e testar o seu modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Test and retrain a model](../../test-your-model.md) (Testar e voltar a preparar um modelo)

* [O que é a Visão Personalizada?](../../overview.md)
* [Documentação de referência SDK (formação)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/customvision/training)
* [Documentação de referência SDK (previsão)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.1/customvision/prediction)