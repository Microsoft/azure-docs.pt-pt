---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: de8ca0a9410479b4d166a47e5c56742955b7853f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725192"
---
Este guia fornece instruções e código de amostra para ajudá-lo a começar a usar a biblioteca de clientes Da Visão Personalizada para Node.js para construir um modelo de deteção de objetos. Você vai criar um projeto, adicionar tags, treinar o projeto, e usar o URL de previsão do projeto para testá-lo programáticamente. Use este exemplo como um modelo para construir a sua própria app de reconhecimento de imagem.

> [!NOTE]
> Se quiser construir e treinar um modelo de deteção de _objetos sem_ escrever código, consulte a [orientação baseada no navegador.](../../get-started-build-detector.md)

Utilize a biblioteca de clientes Custom Vision para .NET para:

* Criar um novo projeto de Visão Personalizada
* Adicione tags ao projeto
* Enviar e marcar imagens
* Treine o projeto
* Publique a iteração atual
* Teste o ponto final de previsão

Documentação de referência [(formação)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(previsão)](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | Código fonte da [biblioteca (formação)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(previsão)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | Amostras de pacote (npm) [(treino)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(previsão)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction)  |  [](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)


## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual de [Node.js](https://nodejs.org/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" Crie um recurso De Visão Personalizada crie um recurso De "  target="_blank"> Visão Personalizada no portal </a> Azure para criar um recurso de formação e previsão e obter as suas chaves e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final dos recursos que cria para ligar a sua aplicação à Visão Personalizada. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```

Executar o `npm init` comando para criar uma aplicação de nó com um `package.json` ficheiro. 

```console
npm init
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Para escrever uma aplicação de análise de imagem com visão personalizada para Node.js, você precisará dos pacotes NPM de visão personalizada. Para instalá-los, executar o seguinte comando em PowerShell:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

O ficheiro da sua aplicação `package.json` será atualizado com as dependências.

Criar um ficheiro com o nome `index.js` e importar as seguintes bibliotecas:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js)que contém os exemplos de código neste arranque rápido.

Crie variáveis para o ponto final e teclas Azure do seu recurso. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Aceda ao portal do Azure. Se os recursos de Visão Personalizada que criou na secção **Pré-Requisitos implementados** com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar as suas chaves e ponto final nas **páginas chave e ponto final** dos recursos, sob **gestão de recursos.** Terá de obter as chaves tanto para os seus recursos de treino como de previsão, juntamente com o ponto final da API para o seu recurso de treino.
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Para mais informações, consulte o artigo [de segurança](../../../cognitive-services-security.md) dos Serviços Cognitivos.

Adicione também campos para o nome do seu projeto e um parâmetro de tempo limite para chamadas assíncronos.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_vars)]

## <a name="object-model"></a>Modelo de objeto

|Nome|Descrição|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | Esta aula trata da criação, formação e publicação dos seus modelos. |
|[PrevisãoAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| Esta classe lida com a consulta dos seus modelos para previsões de deteção de objetos.|
|[Previsão](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| Esta interface define uma única previsão numa única imagem. Inclui propriedades para o iD e nome do objeto, e uma pontuação de confiança.|

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes De Visão Personalizada para JavaScript:

* [Autenticar o cliente](#authenticate-the-client)
* [Criar um novo projeto de Visão Personalizada](#create-a-new-custom-vision-project)
* [Adicione tags ao projeto](#add-tags-to-the-project)
* [Enviar e marcar imagens](#upload-and-tag-images)
* [Treine o projeto](#train-the-project)
* [Publique a iteração atual](#publish-the-current-iteration)
* [Teste o ponto final de previsão](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Objetos instantâneos do cliente com o seu ponto final e chave. Crie um objeto **ApiKeyCredentials** com a sua chave e use-o com o seu ponto final para criar um objeto [TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) e [PredictionAPIClient.](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="add-helper-function"></a>Adicionar função de ajudante

Adicione a seguinte função para ajudar a fazer várias chamadas assíncronos. Vais usar isto mais tarde.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Criar um novo projeto de Visão Personalizada

Inicie uma nova função para conter todas as suas chamadas de função De Visão Personalizada. Adicione o seguinte código para criar um novo projeto de serviço De Visão Personalizada.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Primeiro, descarregue as imagens da amostra para este projeto. Guarde o conteúdo da [pasta Imagens](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) da amostra para o seu dispositivo local.

> [!NOTE]
> Precisa de um conjunto mais amplo de imagens para completar o seu treino? O Trove, um projeto da Microsoft Garage, permite-lhe recolher e adquirir conjuntos de imagens para fins de treino. Uma vez recolhidas as suas imagens, pode descarregá-las e depois importá-las para o seu projeto De Visão Personalizada da forma habitual. Visite a [página do Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) para saber mais.

Para adicionar as imagens de exemplo ao projeto, insira o seguinte código após a criação da etiqueta. Este código carrega cada imagem com a etiqueta correspondente. Quando marca imagens em projetos de deteção de objetos, é necessário especificar a região de cada objeto marcado utilizando coordenadas normalizadas. Para este tutorial, as regiões são codificadas em linha com o código. As regiões especificam a caixa delimitadora em coordenadas normalizadas e as coordenadas são dadas pela ordem seguinte: esquerda, superior, largura, altura. Pode carregar até 64 imagens num único lote.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_upload)]


> [!IMPORTANT]
> Você precisará mudar o caminho para as imagens ( `sampleDataRoot` ) com base no local onde você descarregou o repo De Amostras De Python SDK dos Serviços Cognitivos.

> [!NOTE]
> Se não tiver um utilitário de clique e arrasto para marcar as coordenadas das regiões, pode utilizar a UI web em [Customvision.ai](https://www.customvision.ai/). Neste exemplo, as coordenadas já estão fornecidas.


## <a name="train-the-project"></a>Treine o projeto

Este código cria a primeira iteração do modelo de previsão. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Publique a iteração atual

Este código publica a iteração treinada para o ponto final da previsão. O nome dado à iteração publicada pode ser usado para enviar pedidos de previsão. Uma iteração não está disponível no ponto final da previsão até que seja publicada.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Teste o ponto final de previsão

Para enviar uma imagem para o ponto final da previsão e recuperar a previsão, adicione o seguinte código à sua função. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_test)]

Em seguida, feche a função Visão Personalizada e chame-a.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_function_close)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `node` comando no seu ficheiro quickstart.

```shell
node index.js
```

A saída da aplicação deverá aparecer na consola. Em seguida, pode verificar se a imagem de teste (encontrada em **<sampleDataRoot> /Teste)** está corretamente marcada e que a região de deteção está correta. Também pode regressar ao [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto criado recentemente.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Passos seguintes

Agora já fizeste todos os passos do processo de deteção de objetos em código. Esta amostra executa uma única iteração de treino, mas muitas vezes você precisa treinar e testar o seu modelo várias vezes para torná-lo mais preciso. O guia seguinte aborda a classificação de imagens, mas os seus princípios são semelhantes à deteção de objetos.

> [!div class="nextstepaction"]
> [Test and retrain a model](../../test-your-model.md) (Testar e voltar a preparar um modelo)

* [O que é a Visão Personalizada?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js)
* [Documentação de referência SDK (formação)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [Documentação de referência SDK (previsão)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
