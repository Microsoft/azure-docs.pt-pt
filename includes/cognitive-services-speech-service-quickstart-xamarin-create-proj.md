---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0e4e67710c98b80dce2b0d55a86869625f3942d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837465"
---
Para criar um projeto do Visual Studio para desenvolvimento .NET de aplicativo móvel multiplataforma com o Xamarin, você precisa configurar opções de desenvolvimento do Visual Studio, criar o projeto, selecionar a arquitetura de destino e instalar o SDK de fala.

### <a name="set-up-visual-studio-development-options"></a>Configurar opções de desenvolvimento do Visual Studio

Para começar, verifique se você está configurado corretamente no Visual Studio para desenvolvimento móvel de plataforma cruzada com .NET:

1. Abra o Visual Studio 2019.

1. Na barra de menus do Visual Studio, selecione **ferramentas** > **obter ferramentas e recursos** para abrir instalador do Visual Studio e exibir a caixa de diálogo **modificando** .

   ![Guia cargas de trabalho, caixa de diálogo modificando Instalador do Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Na guia **cargas de trabalho** , em **Windows**, encontre o **desenvolvimento móvel com** carga de trabalho do .net. Se a caixa de seleção ao lado dessa carga de trabalho já estiver selecionada, feche a caixa de diálogo **modificando** e vá para a etapa 5.

1. Marque a caixa de seleção **desenvolvimento móvel com o .net** , selecione **Modificar**e, na caixa de diálogo **antes** de começar, selecione **continuar** para instalar o desenvolvimento móvel com carga de trabalho do .net. A instalação do novo recurso pode demorar um pouco.

1. Feche Instalador do Visual Studio.

### <a name="create-the-project"></a>Criar o projeto

1. Na barra de menus do Visual Studio, escolha **arquivo** > **novo** **projeto**  >  para exibir a janela **criar um novo projeto** .

   ![Criar um novo projeto-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Localize e selecione **aplicativo móvel (Xamarin Forms)** .

1. Selecione **Avançar** para exibir a tela **configurar seu novo projeto** . 

   ![Configurar seu novo projeto-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Em **nome do projeto**, digite `helloworld`.

1. Em **local**, navegue até e selecione ou crie a pasta na qual salvar o projeto.

1. Selecione **criar** para ir para a **nova janela do projeto de formulários do Xamarin de aplicativo móvel** .

   ![Caixa de diálogo novo projeto de Plataforma Universal do Windows-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Selecionar modelo **em branco**

1. Em **plataforma**, marque as caixas de seleção para **Android**, **Ios** e **Windows (UWP)** .

1. Selecione **OK**. Você é retornado para o IDE do Visual Studio, com o novo projeto criado e visível no painel de **Gerenciador de soluções** .

   ![projeto HelloWorld-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Agora, selecione a arquitetura da plataforma de destino e o projeto de inicialização. Na barra de ferramentas do Visual Studio, localize a caixa suspensa **plataformas de solução** . (Se você não o vir, escolha **Exibir** **barras de ferramentas** >   > **Standard** para exibir a barra de ferramentas que contém **plataformas de solução**.) Se você estiver executando o Windows de 64 bits, escolha **x64** na caixa suspensa. o Windows de 64 bits também pode executar aplicativos de 32 bits, para que você possa escolher **x86** se preferir. Para a caixa suspensa **projetos de inicialização** , defina HelloWorld. UWP (universal do Windows).

### <a name="install-the-speech-sdk"></a>Instalar o SDK de fala

Instale o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget)e REFERENCIE o SDK de fala em seu projeto:

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse em sua solução e escolha **gerenciar pacotes NuGet para solução** para ir para a janela **NuGet-solução** .

1. Selecione **Procurar**.

   ![Captura de ecrã da caixa de diálogo Gerir Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Em **origem do pacote**, escolha **NuGet.org**.

1. Na caixa de **pesquisa** , digite `Microsoft.CognitiveServices.Speech` e escolha o pacote após ele aparecer nos resultados da pesquisa.

   ![Captura de ecrã da caixa de diálogo Gerir Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)
   > Observação: a biblioteca do iOS dentro de Microsoft. Cognitivaservices. fala NuGet não tem BitCode habilitado. Caso você precise de uma biblioteca habilitada para o BitCode para seu aplicativo, use o NuGet Microsoft. Cognitivaservices. Speech. Xamarin. iOS para o projeto do iOS especificamente.

1. No painel status do pacote ao lado dos resultados da pesquisa, selecione todos os projetos. **HelloWorld**, **HelloWorld. Android**, **HelloWorld. Ios** e **HelloWorld. UWP**.

1. Selecione **Instalar**.

1. Na caixa de diálogo **Visualizar alterações** , selecione **OK**.

1. Na caixa de diálogo **aceitação da licença** , exiba a licença e, em seguida, selecione **aceito** e instale a referência do pacote do SDK de fala para todos os projetos. Depois que a instalação for concluída com êxito, você poderá ver o seguinte aviso para HelloWorld. iOS. Esse é um problema conhecido e não deve afetar a funcionalidade do aplicativo.

> Não foi possível resolver a referência "C:\Users\Default @ no__t-0nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Se essa referência for exigida pelo seu código, você poderá obter erros de compilação.
