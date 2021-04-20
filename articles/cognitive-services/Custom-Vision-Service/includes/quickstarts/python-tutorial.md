---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: 2ac00ddf419dc06ff2b330423cd4025499329b33
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725343"
---
Começa com a biblioteca de clientes Da Visão Personalizada para Python. Siga estes passos para instalar o pacote e experimente o código de exemplo para a construção de um modelo de classificação de imagem. Você vai criar um projeto, adicionar tags, treinar o projeto, e usar o URL de previsão do projeto para testá-lo programáticamente. Use este exemplo como um modelo para construir a sua própria app de reconhecimento de imagem.

> [!NOTE]
> Se quiser construir e treinar um modelo de classificação _sem_ escrever código, consulte a [orientação baseada no navegador.](../../getting-started-build-a-classifier.md)

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

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py)que contém os exemplos de código neste arranque rápido.

Crie variáveis para as teclas de base e subscrição Azure do seu recurso.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_creds)]

> [!IMPORTANT]
> Aceda ao portal do Azure. Se os recursos de Visão Personalizada que criou na secção **Pré-Requisitos implementados** com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar as suas chaves e ponto final nas **páginas chave e ponto final** dos recursos. Terá de obter as chaves tanto para os seus recursos de treino como de previsão, juntamente com o ponto final da API para o seu recurso de treino.
>
> Pode encontrar o valor de ID do recurso de previsão no **separador Propriedades** do recurso, listado como **ID de assinatura.**
>
> Lembre-se de remover as chaves do seu código quando terminar, e nunca postá-las publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Para mais informações, consulte o artigo [de segurança](../../../cognitive-services-security.md) dos Serviços Cognitivos.

## <a name="object-model"></a>Modelo de objeto

|Nome|Descrição|
|---|---|
|[Restrição personalizada DeTraining](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Esta aula trata da criação, formação e publicação dos seus modelos. |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Esta classe lida com a consulta dos seus modelos para previsões de classificação de imagem.|
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

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Criar um novo projeto de Visão Personalizada

Adicione o código seguinte ao seu script para criar um novo projeto do serviço de Visão Personalizada. 

Consulte o método [create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-) para especificar outras opções quando criar o seu projeto (explicado no guia do portal do [classificador Build a).](../../getting-started-build-a-classifier.md)  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Adicione tags ao projeto

Para adicionar etiquetas de classificação ao seu projeto, adicione o seguinte código:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Primeiro, descarregue as imagens da amostra para este projeto. Guarde o conteúdo da [pasta Imagens](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) da amostra para o seu dispositivo local.

> [!NOTE]
> Precisa de um conjunto mais amplo de imagens para completar o seu treino? O Trove, um projeto da Microsoft Garage, permite-lhe recolher e adquirir conjuntos de imagens para fins de treino. Uma vez recolhidas as suas imagens, pode descarregá-las e depois importá-las para o seu projeto De Visão Personalizada da forma habitual. Visite a [página do Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) para saber mais.

Para adicionar as imagens de exemplo ao projeto, insira o seguinte código após a criação da etiqueta. Este código carrega cada imagem com a etiqueta correspondente. Pode carregar até 64 imagens num único lote.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> Você precisará mudar o caminho para as imagens com base no local onde você descarregou o repo de Amostras python dos serviços cognitivos Python.

## <a name="train-the-project"></a>Treine o projeto

Este código cria a primeira iteração do modelo de previsão. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Comboio com etiquetas selecionadas
>
> Pode treinar opcionalmente apenas um subconjunto das suas etiquetas aplicadas. Podes querer fazer isto se ainda não aplicaste o suficiente de certas etiquetas, mas tens o suficiente de outras. Na **[chamada train_project,](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** desa um parâmetro opcional *selected_tags* a uma lista das cordas de ID das etiquetas que pretende utilizar. O modelo treinará para reconhecer apenas as etiquetas dessa lista.

## <a name="publish-the-current-iteration"></a>Publique a iteração atual

Uma iteração não está disponível no ponto final da previsão até que seja publicada. O seguinte código disponibiliza a iteração atual do modelo para consulta. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Teste o ponto final de previsão

Para enviar uma imagem para o ponto final de predição e obter a mesma, adicione o seguinte código no fim do ficheiro:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_test)]

## <a name="run-the-application"></a>Executar a aplicação

Executar *CustomVisionQuickstart.py.*

```powershell
python CustomVisionQuickstart.py
```

O resultado da aplicação deverá ser semelhante ao seguinte texto:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Pode verificar se a imagem de teste (encontrada em **<base_image_location>/imagens/Teste/**) é marcada adequadamente. Também pode regressar ao [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto criado recentemente.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passos seguintes

Agora já viu como cada passo do processo de classificação de imagem pode ser feito em código. Esta amostra executa uma única iteração de treino, mas muitas vezes você precisa treinar e testar o seu modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Test and retrain a model](../../test-your-model.md) (Testar e voltar a preparar um modelo)

* [O que é a Visão Personalizada?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py)
* [Documentação de referência da SDK](/python/api/overview/azure/cognitiveservices/customvision)
