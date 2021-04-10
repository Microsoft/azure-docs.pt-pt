---
title: 'Tutorial: Use detetor de logotipo personalizado para reconhecer serviços Azure - Visão Personalizada'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai passar por uma app de amostra que usa a Visão Personalizada como parte de um cenário de deteção de logotipo. Saiba como a Visão Personalizada é usada com outros componentes para entregar uma aplicação de ponta a ponta.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 76e7b061670f603007cefd6d7aed503e0e0d3d6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98941778"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Tutorial: Reconhecer logotipos de serviço Azure em imagens de câmara

Neste tutorial, você vai explorar uma aplicação de amostra que usa a Visão Personalizada como parte de um cenário maior. A aplicação AI Visual Provision, uma aplicação Xamarin.Forms para plataformas móveis, analisa imagens de câmaras de logótipos de serviço Azure e, em seguida, implementa os serviços reais na conta Azure do utilizador. Aqui você vai aprender como ele usa a Visão Personalizada em coordenação com outros componentes para fornecer uma aplicação útil de ponta a ponta. Você pode executar todo o cenário da aplicação para si mesmo, ou você pode completar apenas a parte visão personalizada da configuração e explorar como a aplicação a utiliza.

Este tutorial irá mostrar-lhe como:

> [!div class="checklist"]
> - Crie um detetor de objetos personalizado para reconhecer logotipos de serviço Azure.
> - Ligue a sua aplicação à Azure Computer Vision e à Custom Vision.
> - Crie uma conta principal de serviço Azure para implementar os serviços Azure a partir da aplicação.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- [Estúdio Visual 2017 ou mais tarde](https://www.visualstudio.com/downloads/)
- A carga de trabalho de Xamarin para Estúdio Visual (ver [Instalação de Xamarin)](/xamarin/cross-platform/get-started/installation/windows)
- Um emulador iOS ou Android para Estúdio Visual
- O [Azure CLI](/cli/azure/install-azure-cli-windows) (opcional)

## <a name="get-the-source-code"></a>Obter o código-fonte

Se quiser utilizar a aplicação web fornecida, clone ou descarregue o código fonte da aplicação a partir do repositório de [Provisão Visual](https://github.com/Microsoft/AIVisualProvision) da IA no GitHub. Abra o ficheiro *.sln Fonte/VisualProvision* no Estúdio Visual. Mais tarde, irá editar alguns dos ficheiros do projeto para que possa executar a aplicação.

## <a name="create-an-object-detector"></a>Criar um detetor de objetos

Inscreva-se no site da [Visão Personalizada](https://customvision.ai/) e crie um novo projeto. Especifique um projeto de deteção de objetos e utilize o domínio do logotipo; isto permitirá que o serviço utilize um algoritmo otimizado para deteção de logotipo. 

![Janela de novo projeto no site da Visão Personalizada no navegador Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Em seguida, treine o algoritmo de deteção do logotipo carregando imagens de logótipos de serviço Azure e marcando-os manualmente. O repositório AIVisualProvision inclui um conjunto de imagens de treino que pode usar. No site, selecione o botão **adicionar imagens** no **separador Imagens de Treino.** Em seguida, aceda à pasta **Documentos/Imagens/Training_DataSet** do repositório. Terá de marcar manualmente os logótipos de cada imagem, por isso, se estiver apenas a testar este projeto, talvez queira carregar apenas um subconjunto das imagens. Faça upload de pelo menos 15 instâncias de cada etiqueta que pretende utilizar.

Depois de fazer o upload das imagens de treino, selecione a primeira no visor. A janela de marcação aparecerá. Desenhe caixas e atribua etiquetas para cada logotipo em cada imagem. 

![Marcação de logotipo no site da Visão Personalizada](media/azure-logo-tutorial/tag-logos.png)

A aplicação está configurada para trabalhar com cordas de etiqueta específicas. Encontrará as definições no ficheiro *Source\VisualProvision\Services\Recognition\Recognition\RecognitionService.cs* ficheiro:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?name=snippet_constants)]

Depois de marcar uma imagem, vá para a direita para marcar a próxima. Feche a janela de marcação quando terminar.

## <a name="train-the-object-detector"></a>Treine o detetor de objetos

No painel esquerdo, desaceia o interruptor **Tags** para **Tagged** para exibir as suas imagens. Em seguida, selecione o botão verde na parte superior da página para treinar o modelo. O algoritmo vai treinar para reconhecer as mesmas etiquetas em novas imagens. Também irá testar o modelo em algumas das suas imagens existentes para gerar pontuações de precisão.

![O site Da Visão Personalizada, no separador Imagens de Treino. Nesta imagem, o botão Train é delineado](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Obter o URL de predição

Depois de o seu modelo ser treinado, está pronto para integrá-lo na sua aplicação. Terá de obter o URL do ponto final (o endereço do seu modelo, que a aplicação irá consultar) e a chave de previsão (para conceder à app acesso aos pedidos de previsão). No **separador Desempenho,** selecione o botão **URL de previsão** no topo da página.

![O site Da Visão Personalizada, mostrando uma janela de Previsão API que exibe um endereço URL e chave API](media/azure-logo-tutorial/cusvis-endpoint.png)

Copie o URL do ponto final e o valor **de Chave de Previsão** para os campos apropriados no ficheiro *Source\VisualProvision\AppSettings.cs:*

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_cusvis_keys)]

## <a name="examine-custom-vision-usage"></a>Examine o uso da Visão Personalizada

Abra o ficheiro *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* para ver como a aplicação utiliza a sua chave de visão personalizada e URL de ponto final. O método **PredictImageContentsAsync** requer um fluxo byte de um ficheiro de imagem juntamente com um token de cancelamento (para gestão de tarefas assíncrona), chama a API de previsão de visão personalizada e devolve o resultado da previsão. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?name=snippet_prediction)]

Este resultado assume a forma de uma **instância PredictionResult,** que por si só contém uma lista de casos de **Previsão.** Uma **previsão** contém uma etiqueta detetada e a sua localização da caixa de delimitação na imagem.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?name=snippet_prediction_class)]

Para saber mais sobre como a aplicação lida com estes dados, comece com o método **GetResourcesAsync.** Este método é definido no ficheiro *Fonte/VisualProvision/Services/Recognition/RecognitionService.cs.*  

## <a name="add-computer-vision"></a>Adicionar visão de computador

A parte da Visão Personalizada do tutorial está completa. Se quiser executar a aplicação, também terá de integrar o serviço de Visão de Computador. A aplicação utiliza a funcionalidade de reconhecimento de texto da Visão de Computador para complementar o processo de deteção do logotipo. Um logótipo Azure pode ser reconhecido pela sua aparência *ou* pelo texto impresso perto dele. Ao contrário dos modelos Custom Vision, a Visão Computacional está pré-treinada para realizar determinadas operações em imagens ou vídeos.

Subscreva o serviço de Visão De Computador para obter uma URL de chave e ponto final. Para obter ajuda neste passo, consulte [como obter as teclas de subscrição](../cognitive-services-apis-create-account.md?tabs=singleservice%2Cwindows).

![O serviço de Visão de Computador no portal Azure, com o menu Quickstart selecionado. Um link para chaves é delineado, assim como o URL de ponto final da API](media/azure-logo-tutorial/comvis-keys.png)

Em seguida, abra o *ficheiro Source\VisualProvision\AppSettings.cs* e povoe as `ComputerVisionEndpoint` `ComputerVisionKey` variáveis e variáveis com os valores corretos.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_comvis_keys)]

## <a name="create-a-service-principal"></a>Criar um principal de serviço

A aplicação requer uma conta principal do serviço Azure para implementar serviços na sua subscrição Azure. Um diretor de serviço permite-lhe delegar permissões específicas numa aplicação usando o controlo de acesso baseado em funções Azure. Para saber mais, consulte o [guia dos principais serviços.](/azure-stack/operator/azure-stack-create-service-principals)

Pode criar um principal de serviço utilizando a Azure Cloud Shell ou o Azure CLI, como mostrado aqui. Para começar, inicie sinsutil e selecione a subscrição que pretende utilizar.

```azurecli
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Em seguida, crie o seu diretor de serviço. (Este processo pode levar algum tempo a terminar.)

```azurecli
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Após a conclusão com sucesso, deverá ver a seguinte saída JSON, incluindo as credenciais necessárias.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Tome nota dos `clientId` `tenantId` valores. Adicione-os aos campos apropriados no ficheiro *Source\VisualProvision\AppSettings.cs.*

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_serviceprincipal)]

## <a name="run-the-app"></a>Executar a aplicação

Neste momento, deu acesso à aplicação a:

- Um modelo de visão personalizada treinado
- O serviço de Visão Computacional
- Uma conta principal de serviço

Siga estes passos para executar a aplicação:

1. No Visual Studio Solution Explorer, selecione o projeto **VisualProvision.Android** ou o projeto **VisualProvision.iOS.** Escolha um emulador correspondente ou um dispositivo móvel ligado a partir do menu suspenso na barra de ferramentas principal. Em seguida, executar a aplicação.

    > [!NOTE]
    > Necessitará de um dispositivo MacOS para executar um emulador iOS.

1. No primeiro ecrã, insira o seu iD principal cliente de serviço, iD do inquilino e senha. Selecione o botão **Iniciar sessão.**

    > [!NOTE]
    > Em alguns emuladores, o botão **de Login** pode não ser ativado neste passo. Se isso acontecer, parar a aplicação, abrir o ficheiro *Source/VisualProvision/Pages/LoginPage.xaml,* encontrar o `Button` elemento marcado como **BOTÃO DE LOGIN**, remover a seguinte linha e, em seguida, executar novamente a aplicação.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![O ecrã da aplicação, mostrando campos para credenciais principais de serviço](media/azure-logo-tutorial/app-credentials.png)

1. No ecrã seguinte, selecione a subscrição do Azure a partir do menu suspenso. (Este menu deve conter todas as subscrições a que o seu diretor de serviço tem acesso.) Selecione o botão **Continuar.** Neste momento, a aplicação poderá levar-lhe a conceder acesso à câmara do dispositivo e ao armazenamento de fotografias. Conceda as permissões de acesso.

    ![O ecrã da aplicação, mostrando um campo de drop-down para a subscrição target Azure](media/azure-logo-tutorial/app-az-subscription.png)


1. A câmara do seu dispositivo será ativada. Tire uma foto de um dos logótipos de serviço Azure que treinou. Uma janela de implantação deve levá-lo a selecionar uma região e um grupo de recursos para os novos serviços (como faria se os estivesse a implantar a partir do portal Azure). 

    ![Um ecrã de câmera de smartphone focado em dois recortes de papel de logotipos Azure](media/azure-logo-tutorial/app-camera-capture.png)

    ![Um ecrã de aplicativo mostrando campos para a região de implementação e grupo de recursos](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se seguiu todos os passos deste cenário e utilizou a app para implantar os serviços Azure na sua conta, vá ao [portal Azure.](https://ms.portal.azure.com/) Aí, cancele os serviços que não quer usar.

Se planeia criar o seu próprio projeto de deteção de objetos com a Visão Personalizada, é melhor eliminar o projeto de deteção de logotipo que criou neste tutorial. Uma subscrição gratuita para a Custom Vision permite apenas dois projetos. Para eliminar o projeto de deteção de logotipo, no site da [Visão Personalizada,](https://customvision.ai)abra **Projetos** e, em seguida, selecione o ícone do lixo no **âmbito do My New Project**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você montou e explorou uma aplicação full-featured Xamarin.Forms que usa o serviço Custom Vision para detetar logotipos em imagens de câmaras móveis. Em seguida, aprenda as melhores práticas para construir um modelo de Visão Personalizada para que quando criar um para a sua própria app, possa torná-lo poderoso e preciso.

> [!div class="nextstepaction"]
> [Como melhorar o seu classificador](getting-started-improving-your-classifier.md)
