---
title: 'Tutorial: Usar o detector de logotipo personalizado para reconhecer os serviços do Azure-Visão Personalizada'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você passará por um aplicativo de exemplo que usa o Azure Visão Personalizada como parte de um cenário de detecção de logotipo. Saiba como Visão Personalizada é usado com outros componentes para fornecer um aplicativo de ponta a ponta.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: b0a5b110951d7b13110fab935d5ca1333f7f8c1e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564200"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Tutorial: Reconheça os logotipos de serviço do Azure em imagens da câmera

Neste tutorial, você explorará um aplicativo de exemplo que usa o Azure Visão Personalizada como parte de um cenário maior. O aplicativo de provisionamento visual de ia, um aplicativo Xamarin. Forms para plataformas móveis, analisa fotos de câmera de logotipos de serviço do Azure e, em seguida, implanta os serviços reais na conta do Azure do usuário. Aqui você aprenderá como ele usa Visão Personalizada em coordenação com outros componentes para fornecer um aplicativo útil de ponta a ponta. Você pode executar todo o cenário do aplicativo para você mesmo ou pode concluir apenas a parte Visão Personalizada da instalação e explorar como o aplicativo o utiliza.

Este tutorial irá mostrar-lhe como:

> [!div class="checklist"]
> - Crie um detector de objeto personalizado para reconhecer logotipos de serviço do Azure.
> - Conecte seu aplicativo ao Azure Pesquisa Visual Computacional e Visão Personalizada.
> - Crie uma conta de entidade de serviço do Azure para implantar serviços do Azure do aplicativo.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2017 ou posterior](https://www.visualstudio.com/downloads/)
- A carga de trabalho do Xamarin para Visual Studio (consulte Instalando o [Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Um emulador do iOS ou Android para Visual Studio
- O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (opcional)

## <a name="get-the-source-code"></a>Obter o código-fonte

Se você quiser usar o aplicativo Web fornecido, clone ou baixe o código-fonte do aplicativo do repositório de [provisionamento visual do ia](https://github.com/Microsoft/AIVisualProvision) no github. Abra o arquivo *Source/VisualProvision. sln* no Visual Studio. Posteriormente, você editará alguns dos arquivos de projeto para que possa executar o aplicativo.

## <a name="create-an-object-detector"></a>Criar um detector de objeto

Entre no site do [visão personalizada](https://customvision.ai/) e crie um novo projeto. Especifique um projeto de detecção de objeto e use o domínio de logotipo; Isso permitirá que o serviço use um algoritmo otimizado para detecção de logotipo. 

![Janela New-Project no site Visão Personalizada no navegador Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

Em seguida, treine o algoritmo de detecção de logotipo carregando imagens de logotipos de serviço do Azure e marcando-as manualmente. O repositório AIVisualProvision inclui um conjunto de imagens de treinamento que você pode usar. No site, selecione o botão **Adicionar imagens** na guia **imagens de treinamento** . Em seguida, vá para a pasta Documents/ **images/Training_DataSet** do repositório. Você precisará marcar manualmente os logotipos em cada imagem, portanto, se estiver apenas testando esse projeto, talvez você queira carregar apenas um subconjunto das imagens. Carregue pelo menos 15 instâncias de cada marca que você planeja usar.

Depois de carregar as imagens de treinamento, selecione a primeira na exibição. A janela de marcação será exibida. Desenhe caixas e atribua marcas para cada logotipo em cada imagem. 

![Marcação de logotipo no site Visão Personalizada](media/azure-logo-tutorial/tag-logos.png)

O aplicativo está configurado para trabalhar com cadeias de caracteres de marca específicas. Você encontrará as definições no arquivo *Source\VisualProvision\Services\Recognition\RecognitionService.cs* :

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

Depois de marcar uma imagem, acesse a direita para marcar a próxima. Feche a janela de marcação quando terminar.

## <a name="train-the-object-detector"></a>Treinar o detector de objeto

No painel esquerdo, defina a opção **marcas** como **marcada** para exibir suas imagens. Em seguida, selecione o botão verde na parte superior da página para treinar o modelo. O algoritmo treinará o reconhecimento das mesmas marcas em novas imagens. Ele também testará o modelo em algumas de suas imagens existentes para gerar pontuações de precisão.

![O site Visão Personalizada, na guia imagens de treinamento. Nesta captura de tela, o botão treinar é descrito](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Obter a URL de previsão

Depois que o modelo for treinado, você estará pronto para integrá-lo ao seu aplicativo. Você precisará obter a URL do ponto de extremidade (o endereço do seu modelo, que o aplicativo consultará) e a chave de previsão (para conceder ao aplicativo acesso a solicitações de previsão). Na guia **desempenho** , selecione o botão **URL de previsão** na parte superior da página.

![O site Visão Personalizada, mostrando uma janela API de previsão que exibe um endereço URL e uma chave de API](media/azure-logo-tutorial/cusvis-endpoint.png)

Copie a URL do arquivo de imagem e o valor da **chave de previsão** para os campos apropriados no arquivo *Source\VisualProvision\AppSettings.cs* :

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Examinar o uso de Visão Personalizada

Abra o arquivo *Source/VisualProvision/Services/rerecognition/CustomVisionService. cs* para ver como o aplicativo usa sua chave de visão personalizada e a URL do ponto de extremidade. O método **PredictImageContentsAsync** usa um fluxo de bytes de um arquivo de imagem junto com um token de cancelamento (para gerenciamento de tarefas assíncronas), chama a API de previsão de visão personalizada e retorna o resultado da previsão. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Esse resultado assume a forma de uma instância de **PredictionResult** , que contém uma lista de instâncias de **previsão** . Uma **previsão** contém uma marca detectada e seu local de caixa delimitadora na imagem.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Para saber mais sobre como o aplicativo lida com esses dados, comece com o método **GetResourcesAsync** . Esse método é definido no arquivo *Source/VisualProvision/Services/rerecognition/RecognitionService. cs* .  

## <a name="add-computer-vision"></a>Adicionar Pesquisa Visual Computacional

A parte Visão Personalizada do tutorial foi concluída. Se desejar executar o aplicativo, você também precisará integrar o serviço de Pesquisa Visual Computacional. O aplicativo usa o recurso de reconhecimento de texto Pesquisa Visual Computacional para complementar o processo de detecção de logotipo. Um logotipo do Azure pode ser reconhecido por sua aparência *ou* pelo texto impresso perto dele. Ao contrário dos modelos de Visão Personalizada, Pesquisa Visual Computacional é pretreinado para executar determinadas operações em imagens ou vídeos.

Assine o serviço de Pesquisa Visual Computacional para obter uma URL de chave e ponto de extremidade. Para obter ajuda sobre esta etapa, consulte [como obter chaves de assinatura](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![O serviço de Pesquisa Visual Computacional no portal do Azure, com o menu de início rápido selecionado. Um link para chaves é descrito, como é a URL do ponto de extremidade da API](media/azure-logo-tutorial/comvis-keys.png)

Em seguida, abra o arquivo *Source\VisualProvision\AppSettings.cs* e popule `ComputerVisionKey` as `ComputerVisionEndpoint` variáveis e com os valores corretos.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]

## <a name="create-a-service-principal"></a>Criar um principal de serviço

O aplicativo requer uma conta de entidade de serviço do Azure para implantar serviços em sua assinatura do Azure. Uma entidade de serviço permite delegar permissões específicas a um aplicativo usando o controle de acesso baseado em função. Para saber mais, confira o [Guia de entidades de serviço](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals).

Você pode criar uma entidade de serviço usando Azure Cloud Shell ou a CLI do Azure, conforme mostrado aqui. Para começar, entre e selecione a assinatura que você deseja usar.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Em seguida, crie sua entidade de serviço. (Esse processo pode levar algum tempo para ser concluído.)

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Após a conclusão bem-sucedida, você deverá ver a saída JSON a seguir, incluindo as credenciais necessárias.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Anote os `clientId` valores e `tenantId` . Adicione-os aos campos apropriados no arquivo *Source\VisualProvision\AppSettings.cs* .

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Executar a aplicação

Neste ponto, você concedeu ao aplicativo acesso a:

- Um modelo de Visão Personalizada treinado
- O serviço Pesquisa Visual Computacional
- Uma conta de entidade de serviço

Siga estas etapas para executar o aplicativo:

1. No Gerenciador de Soluções do Visual Studio, selecione o projeto **VisualProvision. Android** ou o projeto **VisualProvision. Ios** . Escolha um emulador correspondente ou dispositivo móvel conectado no menu suspenso na barra de ferramentas principal. Em seguida, execute o aplicativo.

    > [!NOTE]
    > Você precisará de um dispositivo MacOS para executar um emulador do iOS.

1. Na primeira tela, insira a ID do cliente da entidade de serviço, a ID do locatário e a senha. Selecione o botão **logon** .

    > [!NOTE]
    > Em alguns emuladores, o botão de **logon** pode não ser ativado nesta etapa. Se isso acontecer, pare o aplicativo, abra o arquivo *Source/VisualProvision/Pages/LoginPage. XAML* , localize o `Button` botão rotulado **login**do elemento, remova a linha a seguir e execute o aplicativo novamente.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![A tela do aplicativo, mostrando os campos para as credenciais da entidade de serviço](media/azure-logo-tutorial/app-credentials.png)

1. Na próxima tela, selecione sua assinatura do Azure no menu suspenso. (Esse menu deve conter todas as assinaturas às quais sua entidade de serviço tem acesso.) Selecione o botão **continuar** . Neste ponto, o aplicativo pode solicitar que você conceda acesso à câmera do dispositivo e ao armazenamento de fotos. Conceda as permissões de acesso.

    ![A tela do aplicativo, mostrando um campo suspenso para a assinatura de destino do Azure](media/azure-logo-tutorial/app-az-subscription.png)


1. A câmera em seu dispositivo será ativada. Tire uma foto de um dos logotipos de serviço do Azure que você treinou. Uma janela de implantação deve solicitar que você selecione uma região e um grupo de recursos para os novos serviços (como faria se estivesse implantando-os do portal do Azure). 

    ![Uma tela de câmera do smartphone concentrada em duas recortes de papel dos logotipos do Azure](media/azure-logo-tutorial/app-camera-capture.png)

    ![Uma tela de aplicativo mostrando campos para a região de implantação e o grupo de recursos](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você seguiu todas as etapas deste cenário e usou o aplicativo para implantar os serviços do Azure em sua conta, vá para a [portal do Azure](https://ms.portal.azure.com/). Lá, cancele os serviços que você não deseja usar.

Se você planeja criar seu próprio projeto de detecção de objetos com Visão Personalizada, talvez queira excluir o projeto de detecção de logotipo criado neste tutorial. Uma avaliação gratuita para Visão Personalizada permite apenas dois projetos. Para excluir o projeto de detecção de logotipo, no [site visão personalizada](https://customvision.ai), abra **projetos** e, em seguida, selecione o ícone de lixeira em **meu novo projeto**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura e explorou um aplicativo Xamarin. Forms com recursos completos que usa o serviço de Visão Personalizada para detectar logotipos em imagens de câmera móvel. Em seguida, Aprenda as práticas recomendadas para criar um modelo de Visão Personalizada para que, ao criar um para seu próprio aplicativo, você possa torná-lo poderoso e preciso.

> [!div class="nextstepaction"]
> [Como melhorar seu classificador](getting-started-improving-your-classifier.md)
