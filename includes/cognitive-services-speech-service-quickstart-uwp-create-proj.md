---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/19/2019
ms.author: erhopf
ms.openlocfilehash: 64c93e5c8e139dbdcfd3faad3a9ac4328fb01972
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391743"
---
Para criar um projeto do Visual Studio Project for Plataforma Universal do Windows (UWP), você precisa configurar opções de desenvolvimento do Visual Studio, criar o projeto, selecionar a arquitetura de destino, configurar a captura de áudio e instalar o SDK de fala.

### <a name="set-up-visual-studio-development-options"></a>Configurar opções de desenvolvimento do Visual Studio

Para começar, verifique se você está configurado corretamente no Visual Studio para o desenvolvimento de UWP:

1. Abra o Visual Studio 2019 para exibir a janela **Iniciar** .

   ![Janela inicial – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Selecione **continuar sem código** para ir para o IDE do Visual Studio.

1. Na barra de menus do Visual Studio, selecione **ferramentas** > **obter ferramentas e recursos** para abrir instalador do Visual Studio e exibir a caixa de diálogo **modificando** .

   ![Guia cargas de trabalho, caixa de diálogo modificando Instalador do Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Na guia **cargas de trabalho** , em **Windows**, encontre a carga de trabalho de **desenvolvimento plataforma universal do Windows** . Se a caixa de seleção ao lado dessa carga de trabalho já estiver selecionada, feche a caixa de diálogo **modificando** e vá para a etapa 6.

1. Marque a caixa de seleção **plataforma universal do Windows Desenvolvimento** , selecione **Modificar**e, na caixa de diálogo **antes** de começar, selecione **continuar** para instalar a carga de trabalho de desenvolvimento UWP. A instalação do novo recurso pode demorar um pouco.

1. Feche Instalador do Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Criar o projeto e selecionar a arquitetura de destino

Em seguida, crie seu projeto:

1. Na barra de menus do Visual Studio, escolha **arquivo** > **novo** **projeto**  >  para exibir a janela **criar um novo projeto** .

   ![Criar um novo projeto-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Localize e selecione **aplicativo em branco (universal do Windows)** . Certifique-se de selecionar a C# versão desse tipo de projeto (em vez de Visual Basic).

1. Selecione **Avançar** para exibir a tela **configurar seu novo projeto** . 

   ![Configurar seu novo projeto-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. Em **nome do projeto**, digite `helloworld`.

1. Em **local**, navegue até e selecione ou crie a pasta na qual salvar o projeto.

1. Selecione **criar** para ir para a janela **novo projeto de plataforma universal do Windows** .

   ![Caixa de diálogo novo projeto de Plataforma Universal do Windows-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Na **versão mínima** (a segunda caixa suspensa), escolha atualização para **criadores de outono do Windows 10 (10,0; Build 16299)** , que é o requisito mínimo para o SDK de fala.

1. Em **versão de destino** (a primeira caixa suspensa), escolha um valor idêntico a ou posterior ao valor na **versão mínima**.

1. Selecione **OK**. Você é retornado para o IDE do Visual Studio, com o novo projeto criado e visível no painel de **Gerenciador de soluções** .

   ![projeto HelloWorld-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Agora, selecione a arquitetura da plataforma de destino. Na barra de ferramentas do Visual Studio, localize a caixa suspensa **plataformas de solução** . (Se você não o vir, escolha **Exibir** **barras de ferramentas** >   > **Standard** para exibir a barra de ferramentas que contém **plataformas de solução**.) Se você estiver executando o Windows de 64 bits, escolha **x64** na caixa suspensa. o Windows de 64 bits também pode executar aplicativos de 32 bits, para que você possa escolher **x86** se preferir.

> [!NOTE]
> O SDK de fala só dá suporte a processadores compatíveis com Intel. No momento, não há suporte para processadores ARM.

### <a name="set-up-audio-capture"></a>Configurar a captura de áudio

Em seguida, permita que o projeto Capture a entrada de áudio:

1. Em **Gerenciador de soluções**, clique duas vezes em **Package. appxmanifest** para abrir o manifesto do aplicativo de pacote.

1. Selecione a guia **recursos** .

   ![Guia recursos, manifesto de aplicativo de pacote-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Selecione a caixa do recurso de **microfone** .

1. Na barra de menus, escolha **arquivo** > **salvar pacote. appxmanifest** para salvar suas alterações.

### <a name="install-the-speech-sdk"></a>Instalar o SDK de fala

Por fim, instale o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget)e REFERENCIE o SDK de fala em seu projeto:

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse em sua solução e escolha **gerenciar pacotes NuGet para solução** para ir para a janela **NuGet-solução** .

1. Selecione **Procurar**.

   ![Captura de ecrã da caixa de diálogo Gerir Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Em **origem do pacote**, escolha **NuGet.org**.

1. Na caixa de **pesquisa** , digite `Microsoft.CognitiveServices.Speech` e escolha o pacote após ele aparecer nos resultados da pesquisa.

   ![Captura de ecrã da caixa de diálogo Gerir Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. No painel status do pacote ao lado dos resultados da pesquisa, selecione seu projeto **HelloWorld** .

1. Selecione **Instalar**.

1. Na caixa de diálogo **Visualizar alterações** , selecione **OK**.

1. Na caixa de diálogo **aceitação da licença** , exiba a licença e, em seguida, selecione **aceito**. A instalação do pacote começa e, quando a instalação é concluída, o painel **saída** exibe uma mensagem semelhante ao seguinte texto: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.7.0' to helloworld`.
