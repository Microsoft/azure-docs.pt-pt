---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 2960a4f55997c0db60c22e7841b4ba82708dd4e1
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99215177"
---
Para criar um projeto visual Studio para desenvolvimento de desktop C++, você precisa configurar opções de desenvolvimento do Visual Studio, criar o projeto, selecionar a arquitetura-alvo e instalar o Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Configurar opções de desenvolvimento do Estúdio Visual

Para começar, certifique-se de que está configurado corretamente no Estúdio Visual para o desenvolvimento do ambiente de trabalho C+++:

1. Abra o Visual Studio 2019 para exibir a janela **Iniciar.**

   ![Screenshot que mostra a janela "Start" do Visual Studio 2019.](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. **Selecione Continue sem código** para ir ao Visual Studio IDE.

1. A partir da barra de menus Visual Studio, selecione **Tools**  >  **Get Tools and Features** para abrir o Instalador de Estúdio Visual e ver a caixa de diálogo **modificante.**

   ![Screenshot que mostra o separador "Workloads" da caixa de diálogo "Modificar" para o "Instalador de Estúdio Visual".](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. No **separador Workloads,** no **Windows,** encontre o desenvolvimento do Ambiente de Trabalho com carga de trabalho **C++.** Se a caixa de verificação ao lado da carga de trabalho ainda não estiver selecionada, selecione-a.

1. No **separador De componentes Individuais,** encontre a caixa de verificação **do gestor de pacotes NuGet.** Se a caixa de verificação ainda não estiver selecionada, selecione-a.

1. Selecione o botão no canto com a etiqueta **De perto** ou **modificar**. (O nome do botão varia consoante tenha selecionado quaisquer funcionalidades para a instalação.) Se selecionar **Modificar,** começa a instalação, o que pode demorar algum tempo.

1. Feche o instalador do estúdio visual.

### <a name="create-the-project-and-select-the-target-architecture"></a>Crie o projeto e selecione a arquitetura-alvo

Em seguida, crie o seu projeto:

1. Na barra de menus Visual Studio, escolha **o File**  >  **New**  >  **Project** para exibir a **janela do projeto Create.**

   ![Criar um novo projeto, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Localizar e selecionar **app consola**. Certifique-se de que seleciona a versão C++ deste tipo de projeto (ao contrário de C# ou Visual Basic).

1. Selecione **Seguinte** para exibir o **Configure o seu novo** ecrã de projeto.

   ![Configure o seu novo projeto, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. Em **nome do Projeto,** insira `helloworld` .

1. Em **Localização,** navegue para e selecione ou crie a pasta para guardar o seu projeto.

Agora selecione a arquitetura da sua plataforma alvo. Na barra de ferramentas Visual Studio, encontre a caixa de lançamento das **Plataformas de Solução.** (Se não o vir, escolha **Ver**  >  **Barras de ferramentas**  >  **Standard** para exibir a barra de ferramentas que contém **plataformas de solução**.) Se estiver a executar o Windows de 64 bits, escolha **x64** na caixa de entrega. O Windows de 64 bits também pode executar aplicações de 32 bits, por isso pode escolher **x86** se preferir.

### <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Por fim, instale o [pacote Speech SDK NuGet](https://aka.ms/csspeech/nuget)e refira o SDK de discurso no seu projeto:

1. No **Solution Explorer,** clique com o botão direito na sua solução e escolha **Gerir pacotes NuGet para solução** para ir à janela **NuGet - Solução.**

1. Selecione **Procurar**.

   ![NuGet - Separador de soluções, Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Na **fonte do Pacote,** escolha **nuget.org**.

1. Na caixa **'Procurar',** `Microsoft.CognitiveServices.Speech` insira e, em seguida, escolha esse pacote depois de aparecer nos resultados da pesquisa.

   ![Microsoft.CognitiveServices.Speech C++ instalação de pacote - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. No painel de estado do pacote ao lado dos resultados da pesquisa, selecione o seu projeto **Helloworld.**

1. Selecione **Instalar**.

1. Na caixa de diálogo **'Alterações de pré-visualização',** selecione **OK**.

1. Na caixa de diálogo **de aceitação da licença,** veja a licença e, em seguida, selecione **I Accept**. A instalação da embalagem começa e, quando a instalação estiver concluída, o painel **de saída** apresenta uma mensagem semelhante ao seguinte texto: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.15.0' to helloworld` .
