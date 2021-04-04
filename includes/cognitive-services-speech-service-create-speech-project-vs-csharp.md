---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001112"
---
Para criar um projeto visual Studio para desenvolvimento do Windows, você precisa criar o projeto, configurar o Visual Studio para o desenvolvimento de desktop .NET, instalar o Speech SDK e escolher a arquitetura-alvo.

### <a name="create-the-project-and-add-the-workload"></a>Crie o projeto e adicione a carga de trabalho

Para começar, crie o projeto no Visual Studio e certifique-se de que o Visual Studio está configurado para o desenvolvimento do ambiente de trabalho .NET:

1. Open Visual Studio 2019.

1. Na janela Iniciar, **selecione Criar um novo projeto.** 

1. Na **janela criar uma nova** janela de projeto, escolha a App consola **(.NET Framework)** e, em seguida, selecione **Next**.

1. Na **configuração** da sua nova janela do projeto, insira *helloworld* em **nome do Projeto,** escolha ou crie o caminho do diretório em **Localização,** e, em seguida, selecione **Criar**.

1. A partir da barra de menus Visual Studio, selecione **Tools**  >  **Get Tools and Features**, que abre o Instalador de Estúdio Visual e exibe a caixa de diálogo **modificante.**

1. Verifique se está disponível a carga de trabalho **de desenvolvimento do ambiente de trabalho .NET.** Se a carga de trabalho não tiver sido instalada, selecione a caixa de verificação ao lado e, em seguida, selecione **Modificar** para iniciar a instalação. Pode levar alguns minutos para descarregar e instalar.

   Se a caixa de verificação ao lado **do desenvolvimento do ambiente de trabalho .NET** já estiver selecionada, selecione **Close** to exit the dialog box.

   ![Ativar o desenvolvimento do ambiente de trabalho .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Feche o instalador do estúdio visual.

### <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

O próximo passo é instalar o [pacote Speech SDK NuGet,](https://aka.ms/csspeech/nuget)para que possa fazê-lo referência no código.

1. No Solution Explorer, clique com o botão direito no projeto **Helloworld** e, em seguida, **selecione Gerir Pacotes NuGet** para mostrar o Gestor de Pacotes NuGet.

   ![Gestor de Pacotes NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. No canto superior direito, encontre a caixa de entrega **de Fonte** de Pacote e certifique-se de que **nuget.org** está selecionada.

1. No canto superior esquerdo, **selecione Procurar**.

1. Na caixa de pesquisa, digite *Microsoft.CognitiveServices.Speech* e selecione **Enter**.

1. A partir dos resultados da pesquisa, selecione o pacote **Microsoft.CognitiveServices.Speech** e, em seguida, **selecione Instalar** para instalar a versão mais recente estável.

   ![Instale o pacote Microsoft.CognitiveServices.Speech NuGet](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Aceite todos os contratos e licenças para iniciar a instalação.

   Após a instalação da embalagem, aparece uma confirmação na janela **da consola do gestor de pacotes.**

### <a name="choose-the-target-architecture"></a>Escolha a arquitetura-alvo

Agora, para construir e executar a aplicação da consola, crie uma configuração de plataforma que combine com a arquitetura do seu computador.

1. A partir da barra de menu, selecione **Build**  >  **Configuration Manager**. Aparece a caixa de diálogo **do Gestor de Configuração.**

   ![Caixa de diálogo do gestor de configuração](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Na caixa de lançamento da plataforma de **solução Ative,** selecione **New**. Aparece a caixa de diálogo **da Nova Plataforma de Solução.**

1. No **Tipo ou selecione a nova** caixa de entrega da plataforma:
   - Se estiver a executar o Windows de 64 bits, selecione **x64**.
   - Se estiver a executar o Windows de 32 bits, selecione **x86**.

1. Selecione **OK** e, em seguida, **Feche**.
