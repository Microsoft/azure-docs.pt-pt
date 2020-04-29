---
title: 'Tutorial: Use detetor de logotipo personalizado para reconhecer serviços Azure - Visão Personalizada'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai passar por uma aplicação de amostra que usa a Visão Personalizada como parte de um cenário de deteção de logotipo. Saiba como a Visão Personalizada é usada com outros componentes para fornecer uma aplicação de ponta a ponta.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 0962afb360df0ec6a414f676a2c280b3837c687d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81403673"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Tutorial: Reconhecer logotipos de serviço Azure em imagens de câmara

Neste tutorial, você vai explorar uma aplicação de amostra que usa a Visão Personalizada como parte de um cenário maior. A aplicação AI Visual Provision, uma aplicação Xamarin.Forms para plataformas móveis, analisa imagens de câmaras de logótipos de serviço sinuosos e, em seguida, implementa os serviços reais para a conta Azure do utilizador. Aqui você vai aprender como ele usa a Visão Personalizada em coordenação com outros componentes para fornecer uma aplicação útil de ponta a ponta. Você pode executar todo o cenário da aplicação para si mesmo, ou você pode completar apenas a parte Custom Vision da configuração e explorar como a aplicação a utiliza.

Este tutorial irá mostrar-lhe como:

> [!div class="checklist"]
> - Crie um detetor de objetos personalizado para reconhecer os logótipos do serviço Azure.
> - Ligue a sua aplicação à Azure Computer Vision e à Custom Vision.
> - Crie uma conta principal de serviço Azure para implementar serviços Azure a partir da app.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- [Estúdio Visual 2017 ou mais tarde](https://www.visualstudio.com/downloads/)
- A carga de trabalho xamarin para estúdio visual (ver [Instalação Xamarin)](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows)
- Um emulador iOS ou Android para Estúdio Visual
- O [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (opcional)

## <a name="get-the-source-code"></a>Obter o código-fonte

Se pretender utilizar a aplicação web fornecida, clonar ou descarregar o código fonte da aplicação a partir do repositório [de Fornecimento Visual AI](https://github.com/Microsoft/AIVisualProvision) no GitHub. Abra o ficheiro *Source/VisualProvision.sln* no Estúdio Visual. Mais tarde, vai editar alguns dos ficheiros do projeto para que possa executar a aplicação.

## <a name="create-an-object-detector"></a>Criar um detetor de objetos

Inscreva-se no site da [Visão Personalizada](https://customvision.ai/) e crie um novo projeto. Especificar um projeto de deteção de objetos e utilizar o domínio Logotipo; isto permitirá ao serviço utilizar um algoritmo otimizado para deteção de logotipo. 

![Janela de novo projeto no site da Custom Vision no navegador Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Em seguida, treine o algoritmo de deteção de logotipos carregando imagens de logotipos de serviço Azure e marcando-os manualmente. O repositório AIVisualProvision inclui um conjunto de imagens de treino que pode utilizar. No site, selecione o botão **Adicionar imagens** no separador **Imagens de Treino.** Em seguida, vá para a pasta **Documentos/Imagens/Training_DataSet** do repositório. Terá de marcar manualmente os logótipos em cada imagem, por isso, se estiver apenas a testar este projeto, poderá querer fazer o upload apenas de um subconjunto das imagens. Faça upload de pelo menos 15 instâncias de cada etiqueta que pretende utilizar.

Depois de fazer o upload das imagens de treino, selecione a primeira no visor. A janela de marcação aparecerá. Desenhe caixas e atribua etiquetas para cada logotipo em cada imagem. 

![Marcação de logotipo no site da Visão Personalizada](media/azure-logo-tutorial/tag-logos.png)

A aplicação está configurada para funcionar com cordas de etiquetas específicas. Encontrará as definições no ficheiro *Source\VisualProvision\Services\RecognitionService.cs:*

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?name=snippet_constants)]

Depois de marcar uma imagem, vá para a direita para marcar a próxima. Feche a janela de marcação quando terminar.

## <a name="train-the-object-detector"></a>Treine o detetor de objetos

No painel esquerdo, coloque o interruptor **de etiquetas** para **O Tagged** para exibir as suas imagens. Em seguida, selecione o botão verde na parte superior da página para treinar o modelo. O algoritmo vai treinar para reconhecer as mesmas etiquetas em novas imagens. Também irá testar o modelo em algumas das suas imagens existentes para gerar pontuações de precisão.

![O site Custom Vision, no separador Imagens de Formação. Nesta imagem, o botão Train está delineado](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Obtenha o URL de previsão

Depois de o seu modelo ser treinado, está pronto para integrá-lo na sua aplicação. Terá de obter o URL de ponto final (o endereço do seu modelo, que a app irá consultar) e a chave de previsão (para conceder acesso à app aos pedidos de previsão). No separador **Performance,** selecione o botão URL de **Previsão** na parte superior da página.

![O site Custom Vision, mostrando uma janela De previsão API que exibe um endereço URL e chave API](media/azure-logo-tutorial/cusvis-endpoint.png)

Copie o URL do ponto final e o valor da **chave de previsão** para os campos apropriados no ficheiro *Source\VisualProvision\AppSettings.cs:*

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_cusvis_keys)]

## <a name="examine-custom-vision-usage"></a>Examinar o uso da visão personalizada

Abra o ficheiro *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* para ver como a aplicação utiliza a chave Visão Personalizada e o URL final. O método **PredictImageContentsAsync** leva um fluxo de byte de um ficheiro de imagem juntamente com um símbolo de cancelamento (para gestão de tarefas assíncronas), chama a Previsão de Visão Personalizada API, e devolve o resultado da previsão. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?name=snippet_prediction)]

Este resultado assume a forma de uma instância **De previsãoResult,** que por si só contém uma lista de casos de **Previsão.** Uma **Previsão** contém uma etiqueta detetada e a sua localização da caixa de limitador na imagem.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?name=snippet_prediction_class)]

Para saber mais sobre como a aplicação lida com estes dados, comece com o método **GetResourcesAsync.** Este método é definido no ficheiro *Fonte/VisualProvision/Services/RecognitionService.cs.*  

## <a name="add-computer-vision"></a>Adicionar visão de computador

A parte da Visão Personalizada do tutorial está completa. Se quiser executar a aplicação, terá de integrar o serviço Computer Vision também. A aplicação utiliza a funcionalidade de reconhecimento de texto Computer Vision para complementar o processo de deteção do logotipo. Um logótipo Azure pode ser reconhecido pela sua aparência *ou* pelo texto impresso perto dele. Ao contrário dos modelos Custom Vision, a Computer Vision está pré-treinada para realizar determinadas operações em imagens ou vídeos.

Subscreva o serviço Computer Vision para obter um URL chave e ponto final. Para obter ajuda neste passo, consulte [Como obter chaves de subscrição](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![O serviço Computer Vision no portal Azure, com o menu Quickstart selecionado. Um link para teclas é delineado, assim como o URL final do ponto final da API](media/azure-logo-tutorial/comvis-keys.png)

Em seguida, abra o ficheiro *Source\VisualProvision\AppSettings.cs* e povoe as `ComputerVisionEndpoint` `ComputerVisionKey` e variáveis com os valores corretos.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_comvis_keys)]

## <a name="create-a-service-principal"></a>Criar um principal de serviço

A aplicação requer uma conta principal de serviço Azure para implementar serviços para a sua subscrição Azure. Um diretor de serviço permite-lhe delegar permissões específicas para uma aplicação utilizando o controlo de acesso baseado em papéis. Para saber mais, consulte o guia dos diretores de [serviço.](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals)

Pode criar um diretor de serviço utilizando a Azure Cloud Shell ou o Azure CLI, como mostrado aqui. Para começar, inicie o sessão e selecione a subscrição que pretende utilizar.

```azurecli
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Em seguida, crie o seu diretor de serviço. (Este processo pode levar algum tempo para terminar.)

```azurecli
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Após a conclusão bem sucedida, deverá ver a seguinte saída JSON, incluindo as credenciais necessárias.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Tome nota `clientId` `tenantId` dos valores. Adicione-os aos campos apropriados no ficheiro *Source\VisualProvision\AppSettings.cs.*

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_serviceprincipal)]

## <a name="run-the-app"></a>Executar a aplicação

Neste momento, deu à aplicação acesso a:

- Um modelo de Visão Personalizada treinado
- O serviço de Visão Computacional
- Uma conta principal de serviço

Siga estes passos para executar a aplicação:

1. No Visual Studio Solution Explorer, selecione o projeto **VisualProvision.Android** ou o projeto **VisualProvision.iOS.** Escolha um emulador correspondente ou dispositivo móvel conectado a partir do menu suspenso na barra de ferramentas principal. Em seguida, executar a aplicação.

    > [!NOTE]
    > Necessitará de um dispositivo MacOS para executar um emulador iOS.

1. No primeiro ecrã, introduza o seu serviço principal de identificação do cliente, identificação do inquilino e senha. Selecione o botão **Iniciar sessão.**

    > [!NOTE]
    > Em alguns emuladores, o botão **login** pode não ser ativado neste passo. Se isso acontecer, pare a aplicação, abra o ficheiro *Source/VisualProvision/Pages/LoginPage.xaml,* encontre o `Button` **botão LOGIN**do elemento rotulado, remova a seguinte linha e, em seguida, executar novamente a aplicação.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![O ecrã da aplicação, mostrando campos para credenciais principais de serviço](media/azure-logo-tutorial/app-credentials.png)

1. No ecrã seguinte, selecione a subscrição do Azure a partir do menu suspenso. (Este menu deve conter todas as subscrições a que o seu diretor de serviço tem acesso.) Selecione o botão **Continuar.** Neste ponto, a aplicação pode levar-lhe a conceder acesso à câmara do dispositivo e armazenamento de fotografias. Conceda as permissões de acesso.

    ![O ecrã da aplicação, mostrando um campo de drop-down para subscrição target Azure](media/azure-logo-tutorial/app-az-subscription.png)


1. A câmara do seu dispositivo será ativada. Tire uma foto de um dos logótipos do serviço Azure que treinou. Uma janela de implantação deve instá-lo a selecionar uma região e um grupo de recursos para os novos serviços (como faria se os estivesse a implantar no portal Azure). 

    ![Um ecrã de câmera de smartphone focado em dois recortes de papel de logotipos Do Azure](media/azure-logo-tutorial/app-camera-capture.png)

    ![Um ecrã de aplicativo mostrando campos para a região de implantação e grupo de recursos](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se seguiu todos os passos deste cenário e usou a app para implementar serviços Azure na sua conta, vá ao [portal Azure.](https://ms.portal.azure.com/) Lá, cancele os serviços que não quer usar.

Se planeia criar o seu próprio projeto de deteção de objetos com visão personalizada, talvez queira eliminar o projeto de deteção de logotipo que criou neste tutorial. Um teste gratuito para visão personalizada permite apenas dois projetos. Para eliminar o projeto de deteção de logotipos, no site da [Visão Personalizada,](https://customvision.ai)abra **Projetos** e, em seguida, selecione o ícone do lixo no âmbito do **My New Project**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você montou e explorou uma aplicação Xamarin.Forms completa que usa o serviço Custom Vision para detetar logotipos em imagens de câmaras móveis. Em seguida, aprenda as melhores práticas para construir um modelo Custom Vision para que quando criar um para a sua própria app, possa torná-lo poderoso e preciso.

> [!div class="nextstepaction"]
> [Como melhorar o seu classificador](getting-started-improving-your-classifier.md)
