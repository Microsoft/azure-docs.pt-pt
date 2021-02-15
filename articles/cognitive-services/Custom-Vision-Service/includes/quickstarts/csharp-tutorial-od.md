---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 03c78922d5e8299840812446ba07ccc84af29d04
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100105994"
---
Começa com a biblioteca de clientes Da Visão Personalizada para .NET. Siga estes passos para instalar o pacote e experimente o código de exemplo para a construção de um modelo de deteção de objetos. Você vai criar um projeto, adicionar tags, treinar o projeto em imagens de amostra, e usar o URL de previsão do projeto para testá-lo programáticamente. Use este exemplo como um modelo para construir a sua própria app de reconhecimento de imagem.

> [!NOTE]
> Se quiser construir e treinar um modelo de deteção de _objetos sem_ escrever código, consulte a [orientação baseada no navegador.](../../get-started-build-detector.md)

Utilize a biblioteca de clientes Custom Vision para .NET para:

* Criar um novo projeto de Visão Personalizada
* Adicione tags ao projeto
* Enviar e marcar imagens
* Treine o projeto
* Publique a iteração atual
* Teste o ponto final de previsão

[documentação de referência](/dotnet/api/overview/azure/cognitiveservices/client/customvision) | Código fonte da [biblioteca (formação)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(previsão)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | Amostras de pacote (NuGet) [(treino)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [(previsão)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)  |  [](/samples/browse/?products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* O [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) ou a versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" Crie um recurso De Visão Personalizada crie um recurso De "  target="_blank"> Visão Personalizada no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para criar um recurso de formação e previsão e obter as suas chaves e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final dos recursos que cria para ligar a sua aplicação à Visão Personalizada. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Utilizando o Visual Studio, crie uma nova aplicação .NET Core. 

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente 

Depois de criar um novo projeto, instale a biblioteca do cliente clicando corretamente na solução de projeto no **Solution Explorer** e selecione **Gerir pacotes NuGet**. No gestor de pacotes que abre **selecione Navegar,** verificar **Incluir pré-relançar,** e procurar `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` e `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction` . Selecione a versão mais recente e, em seguida, **instale**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `custom-vision-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: *program.cs*. 

```console
dotnet new console -n custom-vision-quickstart
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```console
dotnet build
```

A saída de construção não deve conter avisos ou erros. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente 

Dentro do diretório de aplicações, instale a biblioteca cliente Custom Vision para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)que contém os exemplos de código neste arranque rápido.

A partir do diretório do projeto, abra o ficheiro *program.cs* e adicione as `using` seguintes diretivas:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_imports)]

No método **principal** da aplicação, crie variáveis para a chave e ponto final do seu recurso.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Aceda ao portal do Azure. Se os recursos de Visão Personalizada que criou na secção **Pré-Requisitos implementados** com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar as suas chaves e ponto final nas **páginas chave e ponto final** dos recursos, sob **gestão de recursos.** Tens de ter as chaves de treino e previsão, juntamente com o ponto final dos recursos de treino.
>
> Lembre-se de remover as chaves do seu código quando terminar, e nunca postá-las publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](../../../cognitive-services-security.md) dos Serviços Cognitivos para obter mais informações.

No método **principal** da aplicação, adicione chamadas para os métodos utilizados neste arranque rápido. Implementará isto mais tarde.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Modelo de objeto

|Nome|Descrição|
|---|---|
|[Restrição personalizada DeTraining](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Esta aula trata da criação, formação e publicação dos seus modelos. |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Esta classe lida com a consulta dos seus modelos para previsões de deteção de objetos.|
|[Modelo de Previsão](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel)| Esta classe define uma única previsão de objeto numa única imagem. Inclui propriedades para o ID e nome do objeto, a localização da caixa de delimitação do objeto, e uma pontuação de confiança.|

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes De Visão Personalizada para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Criar um novo projeto de Visão Personalizada](#create-a-new-custom-vision-project)
* [Adicione tags ao projeto](#add-tags-to-the-project)
* [Enviar e marcar imagens](#upload-and-tag-images)
* [Treine o projeto](#train-the-project)
* [Publique a iteração atual](#publish-the-current-iteration)
* [Teste o ponto final de previsão](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Num novo método, os clientes de treino e previsão instantâneos usando o seu ponto final e chaves.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Criar um novo projeto de Visão Personalizada

Este próximo método cria um projeto de deteção de objetos. O projeto criado aparecerá no site da [Visão Personalizada.](https://customvision.ai/) Consulte o método [CreateProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) para especificar outras opções quando criar o seu projeto (explicado no guia do portal web [do detetor).](../../get-started-build-detector.md)  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Adicione tags ao projeto

Este método define as etiquetas em que vai treinar o modelo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Primeiro, descarregue as imagens da amostra para este projeto. Guarde o conteúdo da [pasta Imagens](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) da amostra para o seu dispositivo local.

> [!NOTE]
> Precisa de um conjunto mais amplo de imagens para completar o seu treino? O Trove, um projeto da Microsoft Garage, permite-lhe recolher e adquirir conjuntos de imagens para fins de treino. Uma vez recolhidas as suas imagens, pode descarregá-las e depois importá-las para o seu projeto De Visão Personalizada da forma habitual. Visite a [página do Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) para saber mais.

Quando marca imagens em projetos de deteção de objetos, é necessário especificar a região de cada objeto marcado utilizando coordenadas normalizadas. O código seguinte associa cada uma das imagens da amostra à sua região marcada.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

> [!NOTE]
> Para os seus próprios projetos, se não tiver um utilitário de clique e arrasto para marcar as coordenadas das regiões, pode utilizar a UI web no site da [Visão Personalizada.](https://www.customvision.ai/) Neste exemplo, as coordenadas já estão fornecidas.

Depois, utilize este mapa de associações para carregar cada imagem de exemplo com as respetivas coordenadas de região. Pode carregar até 64 imagens num único lote. Pode ser necessário alterar o `imagePath` valor para indicar as localizações corretas da pasta.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

Neste ponto, você carregou todas as amostras imagens e marcou cada uma **(garfo** ou **tesoura**) com um retângulo de pixel associado.

## <a name="train-the-project"></a>Treine o projeto

Este método cria a primeira iteração de formação no projeto. Consulta o serviço até que o treino esteja concluído.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> Comboio com etiquetas selecionadas
>
> Pode treinar opcionalmente apenas um subconjunto das suas etiquetas aplicadas. Podes querer fazer isto se ainda não aplicaste o suficiente de certas etiquetas, mas tens o suficiente de outras. Na chamada [TrainProject,](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) utilize o parâmetro *de treino Parameters.* Construa um [TrainingParameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters) e coloque a sua propriedade **SelectedTags** numa lista de IDs das etiquetas que pretende utilizar. O modelo treinará para reconhecer apenas as etiquetas dessa lista.

## <a name="publish-the-current-iteration"></a>Publique a iteração atual

Este método disponibiliza a iteração atual do modelo para consulta. Pode utilizar o nome do modelo como referência para enviar pedidos de previsão. Tens de introduzir o teu próprio valor `predictionResourceId` para. Pode encontrar o ID do recurso de previsão no **separador Visão Geral** do recurso no portal Azure, listado como **ID de subscrição**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Teste o ponto final de previsão

Este método carrega a imagem de teste, consulta o ponto final do modelo e produz dados de previsão para a consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Executar a aplicação

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Executar a aplicação clicando no botão **Debug** na parte superior da janela IDE.

#### <a name="cli"></a>[CLI](#tab/cli)

Executar o pedido do seu diretório de candidaturas com o `dotnet run` comando.

```dotnet
dotnet run
```

---

À medida que a aplicação corre, deve abrir uma janela da consola e escrever a seguinte saída:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Pode então verificar se a imagem de teste (encontrada em **Imagens/Teste/** ) está devidamente marcada e que a região de deteção está correta. Neste ponto, pode premir qualquer tecla para sair da aplicação.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Passos seguintes

Agora já fizeste todos os passos do processo de deteção de objetos em código. Esta amostra executa uma única iteração de treino, mas muitas vezes você precisa treinar e testar o seu modelo várias vezes para torná-lo mais preciso. O guia seguinte aborda a classificação de imagens, mas os seus princípios são semelhantes à deteção de objetos.

> [!div class="nextstepaction"]
> [Test and retrain a model](../../test-your-model.md) (Testar e voltar a preparar um modelo)

* [O que é a Visão Personalizada?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)
* [Documentação de referência da SDK](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
