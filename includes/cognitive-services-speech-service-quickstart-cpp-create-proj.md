---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 72e9334b4df58b1b90288cb7363a7d94bb531661
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80659608"
---
Para criar um projeto de Estúdio Visual para desenvolvimento de desktop C++, você precisa configurar opções de desenvolvimento do Estúdio Visual, criar o projeto, selecionar a arquitetura-alvo e instalar o Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Configurar opções de desenvolvimento do Estúdio Visual

Para começar, certifique-se de que está configurado corretamente no Visual Studio para desenvolvimento de ambiente de trabalho C++:

1. Open Visual Studio 2019 para exibir a janela **Iniciar.**

   ![Janela de início - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. Selecione **Continuar sem código** para ir ao Estúdio Visual IDE.

1. A partir da barra de menus do Estúdio Visual, selecione **Tools** > **Get Tools and Features** para abrir o Instalador de Estúdios Visuais e ver a caixa de diálogo **Modificador.**

   ![Separador de cargas de trabalho, caixa de diálogo modificador, Instalador de Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. No separador **Workloads,** no **Windows,** encontre o desenvolvimento do Ambiente de Trabalho com carga de trabalho **C+++** Se a caixa de verificação ao lado da carga de trabalho ainda não estiver selecionada, selecione-a.

1. No separador **de componentes Individuais,** encontre a caixa de verificação do gestor do **pacote Nuget.** Se a caixa de verificação ainda não estiver selecionada, selecione-a.

1. Selecione o botão no canto rotulado **perto** ou **modifique**. (O nome do botão varia consoante tenha selecionado alguma funcionalidade para a instalação.) Se selecionar **Modificar,** a instalação começa, o que pode demorar algum tempo.

1. Instalador de estúdio visual próximo.

### <a name="create-the-project-and-select-the-target-architecture"></a>Crie o projeto e selecione a arquitetura alvo

Em seguida, crie o seu projeto:

1. Na barra de menus do Estúdio Visual, escolha **o File** > **New** > **Project** para exibir a janela criar uma nova janela de **projeto.**

   ![Criar um novo projeto, C++ - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Encontre e selecione **App consola**. Certifique-se de que seleciona a versão C++ deste tipo de projeto (em oposição a C# ou Visual Basic).

1. Selecione **Next** para exibir o seu novo ecrã de **projeto.**

   ![Configure o seu novo projeto, C++ - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. No nome do `helloworld` **Projeto,** insira .

1. No **Local,** navegue e selecione ou crie a pasta para guardar o seu projeto.

Agora selecione a arquitetura da plataforma alvo. Na barra de ferramentas do Estúdio Visual, encontre a caixa de drop-down das **Plataformas Solutions.** (Se não o vir, escolha **'Ver** > **Toolbars** > **Standard'** para exibir a barra de ferramentas que contém plataformas de **soluções**.) Se estiver a executar o Windows de 64 bits, escolha **x64** na caixa de lançamento. O Windows de 64 bits também pode executar aplicações de 32 bits, pelo que pode escolher **x86,** se preferir.

### <a name="install-the-speech-sdk"></a>Instale o SDK do Discurso

Por fim, instale o [pacote NuGet Speech SDK](https://aka.ms/csspeech/nuget)e faça referência ao SDK de Discurso no seu projeto:

1. No **Solution Explorer,** clique à direita na sua solução e escolha **pacotes De NuGet para solução** para ir à janela **Nuget - Solução.**

1. Selecione **Procurar**.

   ![NuGet - Separador de solução, Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Na **fonte do pacote,** escolha **nuget.org**.

1. Na caixa de `Microsoft.CognitiveServices.Speech` **pesquisa,** introduza , e depois escolha esse pacote depois de aparecer nos resultados da pesquisa.

   ![Microsoft.CognitiveServices.Speech C++ instalação de pacote - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. No painel de estado do pacote ao lado dos resultados da pesquisa, selecione o seu projeto **Helloworld.**

1. Selecione **Instalar**.

1. Na caixa de diálogo **'Alterações de visualização',** selecione **OK**.

1. Na caixa de diálogo de **aceitação** de licença, veja a licença e, em seguida, selecione **I Aceito**. A instalação da embalagem começa e, quando a instalação estiver concluída, o painel **de saída** apresenta uma mensagem semelhante ao seguinte texto: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.11.0' to helloworld`.
