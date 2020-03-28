---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0bcdd315fe11b7472166a5a9ad4f7395e22d2126
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72675599"
---
Para criar um projeto do Visual Studio para aplicações móveis de plataforma cruzada .NET desenvolvimento com Xamarin, você precisa:
- Configurar opções de desenvolvimento do Estúdio Visual.
- Crie o projeto e selecione a arquitetura alvo. 
- Instale o SDK do Discurso.

### <a name="set-up-visual-studio-development-options"></a>Configurar opções de desenvolvimento do Estúdio Visual

Para começar, certifique-se de que está configurado corretamente no Estúdio Visual para desenvolvimento móvel de plataformas cruzadas com .NET:

1. Open Visual Studio 2019.

1. A partir da barra de menus do Estúdio Visual, selecione **Tools** > **Get Tools and Features** para abrir o Instalador de Estúdios Visuais e ver a caixa de diálogo **Modificador.**

   ![Separador de cargas de trabalho, caixa de diálogo modificador, Instalador de Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. No separador **Workloads,** no **Windows,** encontre o desenvolvimento móvel com carga de trabalho **.NET.** Se a caixa de verificação ao lado dessa carga de trabalho já estiver selecionada, feche a caixa de diálogo **modificadora** e vá para o passo 5.

1. Selecione o **desenvolvimento móvel com .NET** check box e selecione **Modificar**. Na caixa de diálogo **Antes de começarmos,** selecione **Continuar** a instalar o desenvolvimento móvel com carga de trabalho .NET. A instalação da nova funcionalidade pode demorar algum tempo.

1. Instalador de estúdio visual próximo.

### <a name="create-the-project"></a>Criar o projeto

1. Na barra de menus do Estúdio Visual, selecione **File** > **New** > **Project** para exibir a janela criar uma nova janela **de projeto.**

   ![Criar um novo projeto - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Localizar e selecionar **aplicativo móvel (Xamarin.Forms)**.

1. Selecione **Next** para exibir o seu novo ecrã de **projeto.**

   ![Configure o seu novo projeto - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Em **nome do Projeto,** insira *helloworld.*

1. No **Local,** vá e selecione ou crie a pasta para guardar o seu projeto.

1. Selecione **Criar** para ir à nova janela do Projeto De Formas de **Aplicação Móvel Xamarin.**

   ![Nova caixa de diálogo universal windows platform project - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Selecione o modelo **Em branco.**

1. Na **Plataforma**, selecione as caixas para **Android,** **iOS**e **Windows (UWP)**.

1. Selecione **OK**. Está de volta ao Visual Studio IDE, com o novo projeto criado e visível no painel **Solution Explorer.**

   ![O projeto Helloworld - Estúdio Visual](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Agora selecione a arquitetura da plataforma alvo e o projeto de startup. Na barra de ferramentas do Estúdio Visual, encontre a caixa de drop-down das **Plataformas Solutions.** (Se não o vir, selecione **'Ver** > **Toolbars** > **Standard'** para exibir a barra de ferramentas que contém plataformas de **soluções**.) Se estiver a executar o Windows de 64 bits, selecione **x64** na caixa de lançamento. Pode selecionar **x86** se quiser, porque o Windows de 64 bits também pode executar aplicações de 32 bits. Na caixa de lançamento dos **Projetos de Arranque,** coloque **o Helloworld. UWP (Janelas Universais)**.

### <a name="install-the-speech-sdk"></a>Instale o SDK do Discurso

Instale o [pacote NuGet Speech SDK](https://aka.ms/csspeech/nuget)e faça referência ao SDK de Discurso no seu projeto.

1. No **Solution Explorer,** clique à direita na sua solução. Selecione **Gerir pacotes NuGet para solução** para ir à janela **NuGet - Solução.**

1. Selecione **Procurar**.

   ![Captura de ecrã da caixa de diálogo Gerir Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Na **fonte do pacote,** selecione nuget.org.

1. Na caixa **de pesquisa,** introduza *Microsoft.CognitiveServices.Speech*. Em seguida, selecione esse pacote depois de aparecer nos resultados da pesquisa.

   ![Captura de ecrã da caixa de diálogo Gerir Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > A biblioteca iOS dentro `Microsoft.CognitiveServices.Speech` do NuGet não tem bitcode ativado. Se precisar da biblioteca bitcode ativada `Microsoft.CognitiveServices.Speech.Xamarin.iOS` para a sua aplicação, utilize o NuGet especificamente para o projeto iOS.

1. No painel de estado do pacote ao lado dos resultados da pesquisa, selecione todos os projetos: **helloworld,** **helloworld. Android,** **helloworld.iOS**e **helloworld. UWP**.

1. Selecione **Instalar**.

1. Na caixa de diálogo **'Alterações de visualização',** selecione **OK**.

1. Na caixa de diálogo de **aceitação** de licença, veja a licença e, em seguida, selecione **I Aceito**. Instale a referência do pacote Speech SDK a todos os projetos. Após a instalação ser concluída com sucesso, poderá ver o seguinte aviso para helloworld.iOS. Este é um problema conhecido e não deve afetar a funcionalidade da sua aplicação.

   > Não foi possível resolver a\.referência "C:\Users\Default nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Se esta referência for exigida pelo seu código, poderá obter erros de compilação.
