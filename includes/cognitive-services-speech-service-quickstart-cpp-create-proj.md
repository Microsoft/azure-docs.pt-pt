---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: erhopf
ms.openlocfilehash: 99a7dec6936b86af4ab9b80d266cd886dae66d12
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381860"
---
Para criar um projeto do Visual Studio C++ para desenvolvimento de área de trabalho, você precisa configurar opções de desenvolvimento do Visual Studio, criar o projeto, selecionar a arquitetura de destino e instalar o SDK de fala. 

### <a name="set-up-visual-studio-development-options"></a>Configurar opções de desenvolvimento do Visual Studio

Para começar, verifique se você está configurado corretamente no Visual Studio para desenvolvimento C++ para área de trabalho:

1. Abra o Visual Studio 2019 para exibir a janela **Iniciar** .

   ![Janela inicial – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png) 

1. Selecione **continuar sem código** para ir para o IDE do Visual Studio.

1. Na barra de menus do Visual Studio, selecione **ferramentas** > **obter ferramentas e recursos** para abrir instalador do Visual Studio e exibir a caixa de diálogo **modificando** .

   ![Guia cargas de trabalho, caixa de diálogo modificando Instalador do Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. Na guia **cargas** de trabalho, em **Windows**, encontre o **desenvolvimento de desktop C++ com** carga de trabalho. Se a caixa de seleção ao lado dessa carga de trabalho ainda não estiver selecionada, selecione-a.

1. Na guia **componentes individuais** , localize a caixa de seleção **Gerenciador de pacotes NuGet** . Se a caixa de seleção ainda não estiver selecionada, selecione-a.

1. Selecione o botão no canto rotulado como **fechar** ou **Modificar**. (O nome do botão varia dependendo se você selecionou os recursos para instalação.) Se você selecionar **Modificar**, a instalação começará, o que pode demorar um pouco.

1. Feche Instalador do Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Criar o projeto e selecionar a arquitetura de destino

Em seguida, crie seu projeto:

1. Na barra de menus do Visual Studio, escolha **arquivo** > **novo** > **projeto** para exibir a janela **criar um novo projeto** .

   ![Criar um novo projeto, C++ -Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Localize e selecione **aplicativo de console**. Certifique-se de selecionar a C++ versão desse tipo de projeto (em oposição a C# ou Visual Basic).

1. Selecione **Avançar** para exibir a tela **configurar seu novo projeto** .

   ![Configurar seu novo projeto, C++ -Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. Em **nome do projeto**, `helloworld`digite.

1. Em **local**, navegue até e selecione ou crie a pasta na qual salvar o projeto.

Agora, selecione a arquitetura da plataforma de destino. Na barra de ferramentas do Visual Studio, localize a caixa suspensa **plataformas de solução** . (Se você não o vir, escolha **Exibir** > **barras de ferramentas** > **Standard** para exibir a barra de ferramentas que contém **plataformas de solução**.) Se você estiver executando o Windows de 64 bits, escolha **x64** na caixa suspensa. o Windows de 64 bits também pode executar aplicativos de 32 bits, para que você possa escolher **x86** se preferir.

### <a name="install-the-speech-sdk"></a>Instalar o SDK de fala

Por fim, instale o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget)e REFERENCIE o SDK de fala em seu projeto:

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse em sua solução e escolha **gerenciar pacotes NuGet para solução** para ir para a janela **NuGet-solução** .

1. Selecione **Procurar**.

   ![NuGet – guia solução, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Em **origem do pacote**, escolha **NuGet.org**.

1. Na caixa de **pesquisa** , insira `Microsoft.CognitiveServices.Speech`e escolha o pacote depois que ele aparece nos resultados da pesquisa.

   ![Instalação do pacote Microsoft. Cognitivaservices. fala C++ -Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. No painel status do pacote ao lado dos resultados da pesquisa, selecione seu projeto **HelloWorld** .

1. Selecione **Instalar**.

1. Na caixa de diálogo **Visualizar alterações** , selecione **OK**.

1. Na caixa de diálogo **aceitação da licença** , exiba a licença e, em seguida, selecione **aceito**. A instalação do pacote começa e, quando a instalação é concluída, o painel **saída** exibe uma mensagem semelhante ao seguinte texto `Successfully installed 'Microsoft.CognitiveServices.Speech 1.6.0' to helloworld`:. 
