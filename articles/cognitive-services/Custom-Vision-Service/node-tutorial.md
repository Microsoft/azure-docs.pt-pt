---
title: 'Início rápido: criar um projeto de classificação de imagem com o SDK do Visão Personalizada para node. js'
titleSuffix: Azure Cognitive Services
description: Crie um projeto, adicione marcas, carregue imagens, treine seu projeto e faça uma previsão usando o SDK do node. js.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: 7490e1261262ff26eec48a691e22ec177954dcf3
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169459"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-nodejs-sdk"></a>Início rápido: criar um projeto de classificação de imagem com o Visão Personalizada SDK do node. js

Este artigo mostra como começar a usar o SDK do Visão Personalizada com node. js para criar um modelo de classificação de imagem. Depois de criado, você pode adicionar marcas, carregar imagens, treinar o projeto, obter a URL de ponto de extremidade de previsão publicada do projeto e usar o ponto de extremidade para testar programaticamente uma imagem. Use este exemplo como um modelo para criar seu próprio aplicativo node. js. Se quiser percorrer o processo de compilar e utilizar um modelo de classificação _sem_ recorrer a código, veja antes as [orientações baseadas no browser](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Node. js 8](https://www.nodejs.org/en/download/) ou posterior instalado.
- [NPM](https://www.npmjs.com/) instalado.
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>Instalar o SDK da Visão Personalizada

Para instalar o SDK do serviço de Visão Personalizada para node. js, execute o seguinte comando no PowerShell:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [node-get-images](includes/node-get-images.md)]

## <a name="add-the-code"></a>Adicionar o código

Crie um novo arquivo chamado *Sample. js* em seu diretório de projeto preferencial.

### <a name="create-the-custom-vision-service-project"></a>Criar o projeto do serviço de Visão Personalizada

Adicione o código seguinte ao seu script para criar um novo projeto do serviço de Visão Personalizada. Insira suas chaves de assinatura nas definições apropriadas e defina o valor do caminho sampleDataRoot para o caminho da pasta de imagem. Verifique se o valor do ponto de extremidade corresponde aos pontos de extremidades de treinamento e previsão que você criou em [Customvision.ai](https://www.customvision.ai/). Observe que a diferença entre criar um projeto de detecção de objetos e classificação de imagem é o domínio especificado na chamada **CreateProject** .

```javascript
const util = require('util');
const fs = require('fs');
const TrainingApiClient = require("@azure/cognitiveservices-customvision-training");
const PredictionApiClient = require("@azure/cognitiveservices-customvision-prediction");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const predictionResourceId = "<your prediction resource id>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://<my-resource-name>.cognitiveservices.azure.com/"

const publishIterationName = "classifyModel";

const trainer = new TrainingApiClient(trainingKey, endPoint);

(async () => {
    console.log("Creating project...");
    const sampleProject = await trainer.createProject("Sample Project")
```

### <a name="create-tags-in-the-project"></a>Criar etiquetas no projeto

Para criar marcas de classificação para seu projeto, adicione o seguinte código ao final do *exemplo. js*:

```javascript
const hemlockTag = await trainer.createTag(sampleProject.id, "Hemlock");
const cherryTag = await trainer.createTag(sampleProject.id, "Japanese Cherry");
```

### <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

Para adicionar as imagens de exemplo ao projeto, insira o seguinte código após a criação da etiqueta. Este código carrega cada imagem com a etiqueta correspondente. Você pode carregar até 64 imagens em um único lote.

> [!NOTE]
> Você precisará alterar *sampleDataRoot* para o caminho para as imagens com base em onde você baixou o projeto de exemplos do SDK do node. js dos serviços cognitivas anteriormente.

```javascript
console.log("Adding images...");
let fileUploadPromises = [];

const hemlockDir = `${sampleDataRoot}/Hemlock`;
const hemlockFiles = fs.readdirSync(hemlockDir);
hemlockFiles.forEach(file => {
    fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${hemlockDir}/${file}`), { tagIds: [hemlockTag.id] }));
});

const cherryDir = `${sampleDataRoot}/Japanese Cherry`;
const japaneseCherryFiles = fs.readdirSync(cherryDir);
japaneseCherryFiles.forEach(file => {
    fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${cherryDir}/${file}`), { tagIds: [cherryTag.id] }));
});

await Promise.all(fileUploadPromises);
```

### <a name="train-the-classifier-and-publish"></a>Treinar o classificador e publicar

Esse código cria a primeira iteração do modelo de previsão e, em seguida, publica essa iteração no ponto de extremidade de previsão. O nome fornecido para a iteração publicada pode ser usado para enviar solicitações de previsão. Uma iteração não está disponível no ponto de extremidade de previsão até que seja publicada.

```javascript
console.log("Training...");
let trainingIteration = await trainer.trainProject(sampleProject.id);

// Wait for training to complete
console.log("Training started...");
while (trainingIteration.status == "Training") {
    console.log("Training status: " + trainingIteration.status);
    await setTimeoutPromise(1000, null);
    trainingIteration = await trainer.getIteration(sampleProject.id, trainingIteration.id)
}
console.log("Training status: " + trainingIteration.status);

// Publish the iteration to the end point
await trainer.publishIteration(sampleProject.id, trainingIteration.id, publishIterationName, predictionResourceId);
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Obter e usar a iteração publicada no ponto de extremidade de previsão

Para enviar uma imagem para o ponto final de predição e obter a mesma, adicione o seguinte código no fim do ficheiro:

```javascript
    const predictor = new PredictionApiClient(predictionKey, endPoint);
    const testFile = fs.readFileSync(`${sampleDataRoot}/Test/test_image.jpg`);

    const results = await predictor.classifyImage(sampleProject.id, publishIterationName, testFile);

    // Step 6. Show results
    console.log("Results:");
    results.predictions.forEach(predictedResult => {
        console.log(`\t ${predictedResult.tagName}: ${(predictedResult.probability * 100.0).toFixed(2)}%`);
    });
})()
```

## <a name="run-the-application"></a>Executar a aplicação

Execute o *exemplo. js*.

```shell
node sample.js
```

O resultado da aplicação deverá ser semelhante ao seguinte texto:

```console
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

Depois, pode confirmar que a imagem de teste (disponível em **<base_image_url>/Images/Test/** ) é etiquetada adequadamente. Também pode regressar ao [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto criado recentemente.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passos seguintes

Viu como cada passo do processo de classificação de imagens pode ser feito no código. Este exemplo executa uma iteração de preparação individual, mas, muitas vezes, terá de preparar e testar o seu modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)
