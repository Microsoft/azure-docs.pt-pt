---
title: 'Tutorial: Utilize o detetor do logotipo personalizado para reconhecer os serviços do Azure - visão personalizada'
titlesuffix: Azure Cognitive Services
description: Neste tutorial, irá percorrer uma aplicação de exemplo que utiliza a imagem digitalizada personalizado do Azure como parte de um cenário de deteção de logótipo. Saiba como a visão personalizada com outros componentes é usado para fornecer um aplicativo ponto a ponto.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pafarley
ms.openlocfilehash: 8866dc44c8e7ec3e0f3ed23178741535006855cd
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650331"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Tutorial: Reconhecer logótipos de serviço do Azure nas imagens da câmara

Neste tutorial, vou explorar uma aplicação de exemplo que utiliza a imagem digitalizada personalizado do Azure como parte de um cenário maior. A aplicação de aprovisionamento de IA Visual, uma aplicação xamarin. Forms para plataformas móveis, analisa imagens da câmara de logotipos do serviço do Azure e, em seguida, implementa o serviço real para a conta de utilizador do Azure. Aqui, vai aprender como ele usa visão personalizada em coordenação com outros componentes para fornecer um aplicativo útil de ponto-a-ponto. Executar o cenário de aplicação inteira para si próprio, ou simplesmente preencha a parte de visão personalizada da configuração e explore como a aplicação utiliza-o.

Este tutorial irá mostrar-lhe como:

> [!div class="checklist"]
> - Crie um detector de objeto personalizado para reconhecer logotipos do serviço do Azure.
> - Ligar a sua aplicação de imagem digitalizada do Azure e de visão personalizada.
> - Crie uma conta do principal de serviço do Azure para implementar serviços do Azure a partir da aplicação.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- A carga de trabalho do Xamarin para Visual Studio (veja [instalar o Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- IOS ou emulador do Android para o Visual Studio
- O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (opcional)

## <a name="get-the-source-code"></a>Obter o código-fonte

Se pretender utilizar a aplicação web fornecido, clonar ou transferir o código-fonte da aplicação do [aprovisionar de IA Visual](https://github.com/Microsoft/AIVisualProvision) repositório no GitHub. Abra o *Source/VisualProvision.sln* ficheiro no Visual Studio. Mais tarde em diante, irá editar alguns dos ficheiros de projeto para que possa executar a aplicação.

## <a name="create-an-object-detector"></a>Criar um detector de objeto

Inicie sessão para o [Web site de visão personalizada](https://customvision.ai/) e crie um novo projeto. Especifique um projeto de deteção de objetos e utilizar o domínio de logótipo; Isso permitirá que o serviço a utilizar um algoritmo otimizado para a deteção de logótipo. 

![Janela novo projeto no site da visão personalizada no Chrome browser](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

Em seguida, prepare o algoritmo de deteção de logótipo ao carregar imagens de logotipos do serviço do Azure e marcá-los manualmente. O repositório de AIVisualProvision inclui um conjunto de imagens de formação que pode utilizar. No Web site, selecione o **adicionar imagens** botão a **imagens de formação** separador. Em seguida, vá para o **documentos/imagens/Training_DataSet** pasta do repositório. Precisará marcar manualmente logótipos em cada imagem, então, se estiver apenas testando este projeto, é recomendável carregar apenas um subconjunto das imagens. Carregar, pelo menos, 15 instâncias de cada etiqueta que pretende utilizar.

Depois de carregar as imagens de formação, selecione a primeira na exibição. Será apresentada a janela de etiquetagem. Desenhar caixas e atribuir etiquetas para cada logótipo em cada imagem. 

![Logótipo de marcação no site da visão personalizada](media/azure-logo-tutorial/tag-logos.png)

A aplicação está configurada para trabalhar com cadeias de caracteres de etiqueta específica. Encontrará as definições do *Source\VisualProvision\Services\Recognition\RecognitionService.cs* ficheiro:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

Depois de marcar uma imagem, vá para a direita para marcar a próxima Sílaba. Feche a janela de etiquetagem quando terminar.

## <a name="train-the-object-detector"></a>Preparar o detetor de objeto

No painel da esquerda, defina o **etiquetas** mudar para **Tagged** para exibir as imagens. Em seguida, selecione o botão verde na parte superior da página para preparar o modelo. Isso irá ensiná-o algoritmo para reconhecer as etiquetas mesmo em novas imagens. Ele também irá testar o modelo em algumas das suas imagens existentes para gerar as pontuações de precisão.

![O site de visão personalizada, no separador imagens de formação. Nesta captura de ecrã, o botão de comboio é descrito](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Obter o URL de predição

Depois do seu modelo é preparado, está pronto para integrá-la na sua aplicação. Para tal, terá de obter o URL de ponto de extremidade (o endereço do seu modelo, o que a aplicação irá consultar) e a chave de previsão (para conceder o acesso de aplicação para pedidos de predição). Sobre o **desempenho** separador, selecione a **URL de predição** botão na parte superior da página.

![O site de visão personalizada, que mostra uma janela de API de predição, que apresenta um endereço de URL e a chave de API](media/azure-logo-tutorial/cusvis-endpoint.png)

Copie o URL do ficheiro de imagem e o **chave de predição** valor para os campos adequados na *Source\VisualProvision\AppSettings.cs* ficheiro:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Examine a utilização de visão personalizada

Abra o *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* arquivo para ver como a aplicação utiliza o URL de chave e o ponto final de visão personalizada. O **PredictImageContentsAsync** método assume um fluxo de bytes de um ficheiro de imagem, juntamente com um cancelamento token (para gestão de tarefa assíncrona), chama a API de predição de visão personalizada e devolve o resultado da predição. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Este resultado assume a forma de um **PredictionResult** instância, que por si só contém uma lista de **predição** instâncias. R **predição** contém uma etiqueta detetada e a localização da caixa delimitadora na imagem.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Para saber mais sobre como a aplicação processa esses dados, comece com o **GetResourcesAsync** método. Este método é definido no *Source/VisualProvision/Services/Recognition/RecognitionService.cs* ficheiro.  

## <a name="add-computer-vision"></a>Adicionar imagem digitalizada

A parte de visão personalizada do tutorial foi concluída. Se quiser executar a aplicação, terá de integrar o serviço de visão do computador também. A aplicação utiliza a funcionalidade de reconhecimento de texto de imagem digitalizada para complementar o processo de deteção de logótipo. Um logótipo do Azure pode ser reconhecido pela sua aparência *ou* pelo texto impresso perto do telefone. Ao contrário dos modelos de visão personalizada, de imagem digitalizada é pré-preparadas com para executar determinadas operações em imagens ou vídeos.

Subscreva o serviço de visão do computador para obter um URL de chave e o ponto final. Para obter ajuda sobre este passo, consulte [como obter chaves de subscrição](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![O serviço de visão do computador no portal do Azure, com o menu de início rápido selecionado. Uma ligação para as chaves é descrita como é o URL de ponto final de API](media/azure-logo-tutorial/comvis-keys.png)

Em seguida, abra a *Source\VisualProvision\AppSettings.cs* de ficheiros e preencher o `ComputerVisionEndpoint` e `ComputerVisionKey` variáveis com os valores corretos.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]


## <a name="create-a-service-principal"></a>Criar um principal de serviço

A aplicação requer uma conta do principal de serviço do Azure para implementar serviços à sua subscrição do Azure. Um principal de serviço permite-lhe delegar permissões específicas a uma aplicação com o controlo de acesso baseado em funções. Para obter mais informações, consulte a [guiam de principais de serviço](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals).

Pode criar um principal de serviço com o Azure Cloud Shell ou a CLI do Azure, como mostrado aqui. Para começar, inicie sessão e selecione a subscrição que pretende utilizar.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Em seguida, crie o seu principal de serviço. (Este processo poderá demorar algum tempo a concluir.)

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Após a conclusão com êxito, deverá ver o seguinte JSON de saída, incluindo as credenciais necessárias.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```
Anote o `clientId` e `tenantId` valores. Adicioná-los para os campos adequados na *Source\VisualProvision\AppSettings.cs* ficheiro.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Executar a aplicação

Neste momento, que forneceu o acesso de aplicação para:
* um modelo de visão personalizada preparado
* o serviço de visão do computador
* uma conta do principal de serviço 

Siga estes passos para executar a aplicação:

1. No Explorador de soluções do Visual Studio, selecione o **VisualProvision.Android** projeto ou o **VisualProvision.iOS** projeto. Escolha um emulador correspondente ou o dispositivo móvel ligado a partir do menu pendente na barra de ferramentas principal. Em seguida, execute a aplicação.

    > [!NOTE]
    > Precisará de um dispositivo MacOS para executar um emulador de iOS.

1. No primeiro ecrã, introduza o seu ID de cliente do principal de serviço, o ID de inquilino e a palavra-passe. Selecione o **início de sessão** botão.

    > [!NOTE]
    > Em alguns emuladores, o **início de sessão** botão não pode ser ativado nesta etapa. Se isto acontecer, pare a aplicação, abra a *Source/VisualProvision/Pages/LoginPage.xaml* do ficheiro, encontre o `Button` com o nome de elemento **botão de início de sessão**, remova a linha seguinte e, em seguida, executar a aplicação novamente.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![O ecrã da aplicação, que mostra os campos para credenciais do principal de serviço](media/azure-logo-tutorial/app-credentials.png)

1. No ecrã seguinte, selecione a sua subscrição do Azure no menu pendente. (Esse menu deve conter todas as subscrições a que o seu principal de serviço tem acesso.) Selecione o **continuar** botão. Neste momento, a aplicação poderá pedir-lhe conceder acesso à câmara do dispositivo e o armazenamento de fotografias. Conceda as permissões de acesso.

    ![O ecrã da aplicação, que mostra um campo de lista pendente de subscrição do Azure de destino](media/azure-logo-tutorial/app-az-subscription.png)

    

1. A câmara no seu dispositivo será ativada. Tire uma fotografia de um dos logótipos de serviço do Azure que treinados. Uma janela de implantação deve pedir-lhe para selecionar um região e grupo de recursos para os novos serviços (como faria se estivesse implantando-las no portal do Azure). 

    ![Uma tela de câmera do smartphone se concentrou em duas cutouts de documento de logotipos do Azure](media/azure-logo-tutorial/app-camera-capture.png)

    ![Um ecrã de aplicação que mostra os campos para o grupo de recursos e região de implementação](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Limpar recursos 

Se tiver seguido todos os passos deste cenário e utilizado a aplicação para implementar serviços do Azure à sua conta, vá para o [portal do Azure](https://ms.portal.azure.com/). Cancele lá, os serviços que não pretende utilizar.

Se quiser criar seu próprio projeto de deteção de objeto com visão personalizada, poderá eliminar o projeto de deteção do logótipo que criou neste tutorial. Uma avaliação gratuita de visão personalizada permite apenas dois projetos. Para eliminar o projeto de deteção de logotipo, na [Web site de visão personalizada](https://customvision.ai), abra **projetos** e, em seguida, selecione o ícone de caixote do lixo sob **meu novo projeto**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, vai configurar e explorou uma aplicação xamarin. Forms com todas as funcionalidades que utiliza o serviço de visão personalizada para detetar logótipos nas imagens da câmara móveis. Em seguida, conheça as práticas recomendadas para a criação de um modelo de visão personalizada para que quando cria uma para a sua própria aplicação, pode torná-lo poderosa e precisas.

> [!div class="nextstepaction"]
> [Como melhorar o seu classificador](getting-started-improving-your-classifier.md)