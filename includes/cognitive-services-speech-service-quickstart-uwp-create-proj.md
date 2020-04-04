---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 5db99a9d500b05fa6886dce2f29087920f8a1790
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658655"
---
Para criar um projeto de Estúdio Visual para o desenvolvimento da Universal Windows Platform (UWP), é necessário configurar opções de desenvolvimento do Estúdio Visual, criar o projeto, selecionar a arquitetura-alvo, configurar a captura de áudio e instalar o Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Configurar opções de desenvolvimento do Estúdio Visual

Para começar, certifique-se de que está configurado corretamente no Estúdio Visual para desenvolvimento uWP:

1. Open Visual Studio 2019 para exibir a janela **Iniciar.**

   ![Janela de início - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Selecione **Continuar sem código** para ir ao Estúdio Visual IDE.

1. A partir da barra de menus do Estúdio Visual, selecione **Tools** > **Get Tools and Features** para abrir o Instalador de Estúdios Visuais e ver a caixa de diálogo **Modificador.**

   ![Separador de cargas de trabalho, caixa de diálogo modificador, Instalador de Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. No separador **Workloads,** no **Windows,** encontre a carga de trabalho de desenvolvimento da **Plataforma Universal do Windows.** Se a caixa de verificação ao lado dessa carga de trabalho já estiver selecionada, feche a caixa de diálogo **modificadora** e vá para o passo 6.

1. Selecione a caixa de verificação de desenvolvimento da **Plataforma Universal do Windows,** selecione **Modificar**, e depois na caixa de diálogo Antes de **começarmos,** selecione **Continuar** a instalar a carga de trabalho de desenvolvimento do UWP. A instalação da nova funcionalidade pode demorar algum tempo.

1. Instalador de estúdio visual próximo.

### <a name="create-the-project-and-select-the-target-architecture"></a>Crie o projeto e selecione a arquitetura alvo

Em seguida, crie o seu projeto:

1. Na barra de menus do Estúdio Visual, escolha **o File** > **New** > **Project** para exibir a janela criar uma nova janela de **projeto.**

   ![Criar um novo projeto - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Encontre e selecione **App Em Branco (Universal Windows)**. Certifique-se de que seleciona a versão C# deste tipo de projeto (em oposição ao Visual Basic).

1. Selecione **Next** para exibir o seu novo ecrã de **projeto.**

   ![Configure o seu novo projeto - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. No nome do `helloworld` **Projeto,** insira .

1. No **Local,** navegue e selecione ou crie a pasta para guardar o seu projeto.

1. Selecione **Criar** para ir à janela **do Projeto New Universal Windows Platform.**

   ![Nova caixa de diálogo universal windows platform project - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Na **versão Mínima** (segunda caixa de drop-down), escolha a Atualização dos Criadores de outono do Windows **10 (10.0; Construir 16299)**, que é o requisito mínimo para o SDK do discurso.

1. Na **versão Target** (a primeira caixa de entrega), escolha um valor idêntico ou posterior ao valor na versão **Mínima**.

1. Selecione **OK**. Está de volta ao Visual Studio IDE, com o novo projeto criado e visível no painel **Solution Explorer.**

   ![Projeto Helloworld - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Agora selecione a arquitetura da plataforma alvo. Na barra de ferramentas do Estúdio Visual, encontre a caixa de drop-down das **Plataformas Solutions.** (Se não o vir, escolha **'Ver** > **Toolbars** > **Standard'** para exibir a barra de ferramentas que contém plataformas de **soluções**.) Se estiver a executar o Windows de 64 bits, escolha **x64** na caixa de lançamento. O Windows de 64 bits também pode executar aplicações de 32 bits, pelo que pode escolher **x86,** se preferir.

> [!NOTE]
> O SDK de Fala apenas suporta processadores compatíveis com a Intel. Atualmente, os processadores ARM não são suportados.

### <a name="set-up-audio-capture"></a>Configurar a captura de áudio

Em seguida, permitir que o projeto capture a entrada de áudio:

1. No **Solution Explorer,** clique duas vezes **em Pacote.appxmanifest** para abrir o manifesto de aplicação do pacote.

1. Selecione o separador **Capabilities** (Funcionalidades).

   ![Separador de capacidades, manifesto de aplicação de pacote - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Selecione a caixa para a capacidade do **microfone.**

1. A partir da barra de menus, escolha **File** > **Save Package.appxmanifest** para guardar as suas alterações.

### <a name="install-the-speech-sdk"></a>Instale o SDK do Discurso

Por fim, instale o [pacote NuGet Speech SDK](https://aka.ms/csspeech/nuget)e faça referência ao SDK de Discurso no seu projeto:

1. No **Solution Explorer,** clique à direita na sua solução e escolha **pacotes De NuGet para solução** para ir à janela **NuGet - Solução.**

1. Selecione **Procurar**.

   ![Captura de ecrã da caixa de diálogo Gerir Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Na **fonte do pacote,** escolha **nuget.org**.

1. Na caixa de `Microsoft.CognitiveServices.Speech` **pesquisa,** introduza , e depois escolha esse pacote depois de aparecer nos resultados da pesquisa.

   ![Captura de ecrã da caixa de diálogo Gerir Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. No painel de estado do pacote ao lado dos resultados da pesquisa, selecione o seu projeto **Helloworld.**

1. Selecione **Instalar**.

1. Na caixa de diálogo **'Alterações de visualização',** selecione **OK**.

1. Na caixa de diálogo de **aceitação** de licença, veja a licença e, em seguida, selecione **I Aceito**. A instalação da embalagem começa e, quando a instalação estiver concluída, o painel **de saída** apresenta uma mensagem semelhante ao seguinte texto: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.11.0' to helloworld`.
