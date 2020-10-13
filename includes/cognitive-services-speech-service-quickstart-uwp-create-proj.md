---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 134c9996690d629ad8acb348d1928904741b6b2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654386"
---
Para criar um projeto de Estúdio Visual para o desenvolvimento da Universal Windows Platform (UWP), é necessário configurar opções de desenvolvimento do Visual Studio, criar o projeto, selecionar a arquitetura-alvo, configurar a captura de áudio e instalar o Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Configurar opções de desenvolvimento do Estúdio Visual

Para começar, certifique-se de que está configurado corretamente no Visual Studio para o desenvolvimento da UWP:

1. Abra o Visual Studio 2019 para exibir a janela **Iniciar.**

   ![Screenshot que mostra a janela "Iniciar" com a ação "Continue sem código" realçada.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. **Selecione Continue sem código** para ir ao Visual Studio IDE.

1. A partir da barra de menus Visual Studio, selecione **Tools**  >  **Get Tools and Features** para abrir o Instalador de Estúdio Visual e ver a caixa de diálogo **modificante.**

   ![Screenshot que mostra o separador "Workloads" da caixa de diálogo "Modificar" com "Desenvolvimento universal da Plataforma Windows" em destaque.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. No **separador Workloads,** no **Windows,** encontre a carga de trabalho **de desenvolvimento da Plataforma Universal Windows.** Se a caixa de verificação ao lado dessa carga de trabalho já estiver selecionada, feche a caixa de diálogo **modificante** e vá para o passo 6.

1. Selecione a caixa **de verificação de desenvolvimento da Plataforma Universal do Windows,** selecione **Modificar**e, em seguida, na caixa de diálogo **Antes de começarmos** a iniciar, selecione **Continue** a instalar a carga de trabalho de desenvolvimento do UWP. A instalação da nova funcionalidade pode demorar algum tempo.

1. Feche o instalador do estúdio visual.

### <a name="create-the-project-and-select-the-target-architecture"></a>Crie o projeto e selecione a arquitetura-alvo

Em seguida, crie o seu projeto:

1. Na barra de menus Visual Studio, escolha **o File**  >  **New**  >  **Project** para exibir a **janela do projeto Create.**

   ![Screenshot que mostra a janela "Criar um novo projeto" com "Blank App (Universal Windows)" selecionada e o botão "Seguinte" realçado.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Encontre e selecione **App Blank (Universal Windows)**. Certifique-se de que seleciona a versão C# deste tipo de projeto (em oposição ao Visual Basic).

1. Selecione **Seguinte** para exibir o **Configure o seu novo** ecrã de projeto.

   ![Screenshot que mostra o ecrã "Configurar o seu novo projeto" com os campos "Nome do Projeto" e "Localização" e botão "Criar" em destaque.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. Em **nome do Projeto,** insira `helloworld` .

1. Em **Localização,** navegue para e selecione ou crie a pasta para guardar o seu projeto.

1. Selecione **Criar** para ir à janela **do Projeto Plataforma Do Novo Windows Universal.**

   ![Screenshot que mostra a caixa de diálogo "New Universal Windows Platform Project".](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Na **versão mínima** (a segunda caixa de entrega), escolha a atualização dos Criadores de outono do Windows **10 (10.0; Construa 16299)**, que é o requisito mínimo para o Discurso SDK.

1. Na **versão Target** (a primeira caixa de entrega), escolha um valor idêntico ou mais tarde ao valor na versão **Mínima.**

1. Selecione **OK**. Regressa ao Visual Studio IDE, com o novo projeto criado e visível no painel **Solution Explorer.**

   ![Projeto Helloworld - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Agora selecione a arquitetura da sua plataforma alvo. Na barra de ferramentas Visual Studio, encontre a caixa de lançamento das **Plataformas de Solução.** (Se não o vir, escolha **Ver**  >  **Barras de ferramentas**  >  **Standard** para exibir a barra de ferramentas que contém **plataformas de solução**.) Se estiver a executar o Windows de 64 bits, escolha **x64** na caixa de entrega. O Windows de 64 bits também pode executar aplicações de 32 bits, por isso pode escolher **x86** se preferir.

> [!NOTE]
> O Speech SDK suporta todos os processadores compatíveis com a Intel, mas **apenas versões x64** de processadores ARM.

### <a name="set-up-audio-capture"></a>Configurar a captura de áudio

Permitir que o projeto capture a entrada de áudio:

1. No **Solution Explorer,** clique duas vezes **em Package.appxmanifest** para abrir o manifesto de aplicação de pacote.

1. Selecione o separador **Capabilities** (Funcionalidades).

   ![Separador de capacidades, manifesto de aplicação de pacote - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Selecione a caixa para a capacidade **do microfone.**

1. Na barra de menus, escolha **File**  >  **Save Package.appxmanifest** para guardar as suas alterações.

### <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Por fim, instale o [pacote Speech SDK NuGet](https://aka.ms/csspeech/nuget)e refira o SDK de discurso no seu projeto:

1. No **Solution Explorer,** clique com o botão direito na sua solução e escolha **Gerir pacotes NuGet para solução** para ir à janela **NuGet - Solução.**

1. Selecione **Procurar**.

   ![Screenshot que mostra a caixa de diálogo "Gerir pacotes para solução" com o separador "Procurar", caixa "Procurar" e "Fonte de pacote" realçada.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Na **fonte do Pacote,** escolha **nuget.org**.

1. Na caixa **'Procurar',** `Microsoft.CognitiveServices.Speech` insira e, em seguida, escolha esse pacote depois de aparecer nos resultados da pesquisa.

   ![Screenshot que mostra "Microsoft.CognitiveServices.Speech" selecionado, com o projeto e o botão "Instalar" em destaque.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. No painel de estado do pacote ao lado dos resultados da pesquisa, selecione o seu projeto **Helloworld.**

1. Selecione **Instalar**.

1. Na caixa de diálogo **'Alterações de pré-visualização',** selecione **OK**.

1. Na caixa de diálogo **de aceitação da licença,** veja a licença e, em seguida, selecione **I Accept**. A instalação da embalagem começa e, quando a instalação estiver concluída, o painel **de saída** apresenta uma mensagem semelhante ao seguinte texto: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.13.0' to helloworld` .
