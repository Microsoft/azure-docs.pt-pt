---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: 604af007e4549716edf8bac63b74351512b438a5
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445140"
---
Começa com a biblioteca de clientes Da Visão Personalizada para Python. Siga estes passos para instalar o pacote e experimente o código de exemplo para a construção de um modelo de deteção de objetos. Você vai criar um projeto, adicionar tags, treinar o projeto, e usar o URL de previsão do projeto para testá-lo programáticamente. Use este exemplo como um modelo para construir a sua própria app de reconhecimento de imagem.

> [!NOTE]
> Se quiser construir e treinar um modelo de deteção de _objetos sem_ escrever código, consulte a [orientação baseada no navegador.](../../get-started-build-detector.md)

Utilize a biblioteca de clientes Custom Vision para Python para:

* Criar um novo projeto de Visão Personalizada
* Adicione tags ao projeto
* Enviar e marcar imagens
* Treine o projeto
* Publique a iteração atual
* Teste o ponto final de previsão

[Documentação de referência](/python/api/overview/azure/cognitiveservices/customvision)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision)  |  [Pacote (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/)  |  [Amostras](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * A sua instalação Python deve incluir [pip](https://pip.pypa.io/en/stable/). Pode verificar se tem pip instalado em funcionamento `pip --version` na linha de comando. Obtenha pip instalando a versão mais recente de Python.
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" Crie um recurso De Visão Personalizada crie um recurso De "  target="_blank"> Visão Personalizada no portal </a> Azure para criar um recurso de formação e previsão e obter as suas chaves e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final dos recursos que cria para ligar a sua aplicação à Visão Personalizada. Colará as chaves e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Para escrever uma aplicação de análise de imagem com visão personalizada para python, você precisará da biblioteca de clientes Custom Vision. Depois de instalar python, executar o seguinte comando em PowerShell ou uma janela de consola:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie um novo ficheiro Python e importe as seguintes bibliotecas.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py)que contém os exemplos de código neste arranque rápido.

Crie variáveis para as teclas de base e subscrição Azure do seu recurso.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_creds)]


> [!IMPORTANT]
> Aceda ao portal do Azure. Se os recursos de Visão Personalizada que criou na secção **Pré-Requisitos implementados** com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar as suas chaves e ponto final nas **páginas chave e ponto final** dos recursos, sob **gestão de recursos.** Tens de ter as chaves de treino e previsão, juntamente com o ponto final dos recursos de treino.
>
> Pode encontrar o valor de ID do recurso de previsão no **separador Visão Geral** do recurso, listado como **ID de subscrição.**
>
> Lembre-se de remover as chaves do seu código quando terminar, e nunca postá-las publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](../../../../cognitive-services/cognitive-services-security.md) dos Serviços Cognitivos para obter mais informações.

## <a name="object-model"></a>Modelo de objeto

|Nome|Descrição|
|---|---|
|[Restrição personalizada DeTraining](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Esta aula trata da criação, formação e publicação dos seus modelos. |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Esta classe lida com a consulta dos seus modelos para previsões de deteção de objetos.|
|[ImagemPredicação](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Esta classe define uma única previsão de objeto numa única imagem. Inclui propriedades para o ID e nome do objeto, a localização da caixa de delimitação do objeto, e uma pontuação de confiança.|

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer o seguinte com a biblioteca de clientes Da Visão Personalizada para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Criar um novo projeto de Visão Personalizada](#create-a-new-custom-vision-project)
* [Adicione tags ao projeto](#add-tags-to-the-project)
* [Enviar e marcar imagens](#upload-and-tag-images)
* [Treine o projeto](#train-the-project)
* [Publique a iteração atual](#publish-the-current-iteration)
* [Teste o ponto final de previsão](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiar um cliente de treino e previsão com o seu ponto final e chaves. Crie **objetos ApiKeyServiceClientCredentials** com as suas teclas e use-os com o seu ponto final para criar um objeto [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) e [CustomVisionPredictionClient.](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>Criar um novo projeto de Visão Personalizada

Adicione o código seguinte ao seu script para criar um novo projeto do serviço de Visão Personalizada. 

Consulte o método [create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-&preserve-view=true) para especificar outras opções quando criar o seu projeto (explicado no guia do portal web [do detetor).](../../get-started-build-detector.md)  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Adicione tags ao projeto

Para criar tags de objetos no seu projeto, adicione o seguinte código:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Primeiro, descarregue as imagens da amostra para este projeto. Guarde o conteúdo da [pasta Imagens](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) da amostra para o seu dispositivo local.

> [!NOTE]
> Precisa de um conjunto mais amplo de imagens para completar o seu treino? O Trove, um projeto da Microsoft Garage, permite-lhe recolher e adquirir conjuntos de imagens para fins de treino. Uma vez recolhidas as suas imagens, pode descarregá-las e depois importá-las para o seu projeto De Visão Personalizada da forma habitual. Visite a [página do Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) para saber mais.

Quando marca imagens em projetos de deteção de objetos, é necessário especificar a região de cada objeto marcado utilizando coordenadas normalizadas. O código seguinte associa cada uma das imagens da amostra à sua região marcada. As regiões especificam a caixa delimitadora em coordenadas normalizadas e as coordenadas são dadas pela ordem seguinte: esquerda, superior, largura, altura.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tagging)]

> [!NOTE]
> Se não tiver um utilitário de clique e arrasto para marcar as coordenadas das regiões, pode utilizar a UI web em [Customvision.ai](https://www.customvision.ai/). Neste exemplo, as coordenadas já estão fornecidas.

Em seguida, utilize este mapa de associações para carregar cada imagem de amostra com as suas coordenadas de região (pode carregar até 64 imagens num único lote). Adicione o seguinte código.


[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> Você precisará mudar o caminho para as imagens com base no local onde você descarregou os Serviços Cognitivos Python SDK Samples repo mais cedo.

## <a name="train-the-project"></a>Treine o projeto

Este código cria a primeira iteração do modelo de previsão. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Comboio com etiquetas selecionadas
>
> Pode treinar opcionalmente apenas um subconjunto das suas etiquetas aplicadas. Podes querer fazer isto se ainda não aplicaste o suficiente de certas etiquetas, mas tens o suficiente de outras. Na **[chamada train_project,](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** desa um parâmetro opcional *selected_tags* a uma lista das cordas de ID das etiquetas que pretende utilizar. O modelo treinará para reconhecer apenas as etiquetas dessa lista.

## <a name="publish-the-current-iteration"></a>Publique a iteração atual

Uma iteração não está disponível no ponto final da previsão até que seja publicada. O seguinte código disponibiliza a iteração atual do modelo para consulta. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>Teste o ponto final de previsão

Para enviar uma imagem para o ponto final de predição e obter a mesma, adicione o seguinte código no fim do ficheiro:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_test)]


## <a name="run-the-application"></a>Executar a aplicação

Executar *CustomVisionQuickstart.py.*

```powershell
python CustomVisionQuickstart.py
```

A saída da aplicação deverá aparecer na consola. Em seguida, pode verificar se a imagem de teste (encontrada em **<base_image_location>/imagens/teste)** está marcada adequadamente e que a região de deteção está correta. Também pode regressar ao [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto criado recentemente.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Passos seguintes

Agora já fizeste todos os passos do processo de deteção de objetos em código. Esta amostra executa uma única iteração de treino, mas muitas vezes você precisa treinar e testar o seu modelo várias vezes para torná-lo mais preciso. O guia seguinte aborda a classificação de imagens, mas os seus princípios são semelhantes à deteção de objetos.

> [!div class="nextstepaction"]
> [Test and retrain a model](../../test-your-model.md) (Testar e voltar a preparar um modelo)

* [O que é a Visão Personalizada?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py)
* [Documentação de referência da SDK](/python/api/overview/azure/cognitiveservices/customvision)
