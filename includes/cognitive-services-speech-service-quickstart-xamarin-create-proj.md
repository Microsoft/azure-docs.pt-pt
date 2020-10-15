---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 6d20df031633df4642ce9fb5cbbc469fd7f0a5da
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097240"
---
Para criar um projeto visual Studio para o desenvolvimento de aplicativos móveis cross-platform .NET com Xamarin, você precisa:
- Configurar opções de desenvolvimento do Estúdio Visual.
- Crie o projeto e selecione a arquitetura-alvo. 
- Instale o SDK de discurso.

### <a name="set-up-visual-studio-development-options"></a>Configurar opções de desenvolvimento do Estúdio Visual

Para começar, certifique-se de que está configurado corretamente no Visual Studio para desenvolvimento móvel de plataformas cruzadas com .NET:

1. Open Visual Studio 2019.

1. A partir da barra de menus Visual Studio, selecione **Tools**  >  **Get Tools and Features** para abrir o Instalador de Estúdio Visual e ver a caixa de diálogo **modificante.**

   ![Screenshot que mostra o separador Workloads, modificando a caixa de diálogo e o instalador de estúdio visual](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. No **separador Workloads,** no **Windows,** encontre o desenvolvimento móvel com carga de trabalho **.NET.** Se a caixa de verificação ao lado dessa carga de trabalho já estiver selecionada, feche a caixa de diálogo **modificante** e vá para o passo 5.

1. Selecione o desenvolvimento móvel com caixa de verificação **.NET** e selecione **Modificar**. Na caixa de diálogo **Antes de começarmos,** selecione **Continue** a instalar o desenvolvimento móvel com carga de trabalho .NET. A instalação da nova funcionalidade pode demorar algum tempo.

1. Feche o instalador do estúdio visual.

### <a name="create-the-project"></a>Criar o projeto

1. Na barra de menus Visual Studio, selecione **File**  >  **New**  >  **Project** para exibir a **janela do projeto Create.**

   ![Screenshot que mostra como criar um novo projeto no Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Localizar e selecionar **Aplicativo Móvel (Xamarin.Forms)**.

1. Selecione **Seguinte** para exibir o **Configure o seu novo** ecrã de projeto.

   ![Screenshot que mostra como configurar o seu novo projeto no Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Em **nome do Projeto,** insira *helloworld*.

1. Em **Localização,** vá e selecione ou crie a pasta para guardar o seu projeto.

1. Selecione **Criar** para ir à janela do **Projeto New Mobile App Xamarin Forms.**

   ![Screenshot que mostra a nova caixa de diálogo do Projeto New Mobile App Xamarin Forms Project em Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Selecione o modelo **Blank.**

1. Na **Plataforma**, selecione as caixas para **Android,** **iOS**e **Windows (UWP)**.

1. Selecione **OK**. Regressa ao Visual Studio IDE, com o novo projeto criado e visível no painel **Solution Explorer.**

   ![O projeto Helloworld - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Agora selecione a arquitetura da plataforma-alvo e o projeto de arranque. Na barra de ferramentas Do Estúdio Visual, encontre a caixa de lançamento das **Plataformas de Solução.** (Se não o vir, selecione **Ver**  >  **Barras de ferramentas**  >  **Standard** para exibir a barra de ferramentas que contém **Plataformas de Solução**.) Se estiver a executar o Windows de 64 bits, selecione **x64** na caixa de entrega. Pode selecionar **x86** se quiser, porque o Windows de 64 bits também pode executar aplicações de 32 bits. Na caixa de lançamento de **Projetos de Arranque,** set **helloworld. UWP (Universal Windows)**.

### <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Instale o [pacote Speech SDK NuGet](https://aka.ms/csspeech/nuget)e refira o SDK de discurso no seu projeto.

1. No **Solution Explorer,** clique com o botão direito na sua solução. **Selecione Gerir pacotes NuGet para solução** para ir à janela **NuGet - Solução.**

1. Selecione **Procurar**.

   ![Screenshot de Gerir pacotes para caixa de diálogo de solução ao instalar o SDK de voz.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Na **fonte package,** selecione nuget.org.

1. Na caixa **de pesquisa,** insira *Microsoft.CognitiveServices.Speech*. Em seguida, selecione o pacote depois de aparecer nos resultados da pesquisa.

   ![Screenshot que destaca o pacote Microsoft.CognitiveServices.Speech.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > A biblioteca iOS dentro `Microsoft.CognitiveServices.Speech` do NuGet não tem bitcode ativado. Se precisar da biblioteca bitcode ativada para a sua aplicação, utilize `Microsoft.CognitiveServices.Speech.Xamarin.iOS` o NuGet especificamente para o projeto iOS.

1. No painel de estado do pacote ao lado dos resultados da pesquisa, selecione todos os projetos: **helloworld,** **helloworld. Android**, **helloworld.iOS**e **helloworld. UWP**.

1. Selecione **Instalar**.

1. Na caixa de diálogo **'Alterações de pré-visualização',** selecione **OK**.

1. Na caixa de diálogo **de aceitação da licença,** veja a licença e, em seguida, selecione **I Accept**. Instale a referência do pacote Speech SDK a todos os projetos. Após a instalação ter sido concluída com sucesso, poderá ver o seguinte aviso para helloworld.iOS. Este é um problema conhecido e não deve afetar a funcionalidade da sua aplicação.

   > Não foi possível resolver a referência "C:\Users\Default \. nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Se esta referência for exigida pelo seu código, poderá obter erros de compilação.
