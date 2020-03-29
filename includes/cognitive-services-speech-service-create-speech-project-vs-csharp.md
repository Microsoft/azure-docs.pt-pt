---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "71327038"
---
Para criar um projeto de Estúdio Visual para o desenvolvimento do Windows, é necessário criar o projeto, configurar o Visual Studio para o desenvolvimento do ambiente de trabalho .NET, instalar o Speech SDK e escolher a arquitetura-alvo.

### <a name="create-the-project-and-add-the-workload"></a>Criar o projeto e adicionar a carga de trabalho

Para começar, crie o projeto no Visual Studio e certifique-se de que o Visual Studio está configurado para o desenvolvimento do ambiente de trabalho .NET:

1. Open Visual Studio 2019.

1. Na janela Iniciar, selecione **Criar um novo projeto**. 

1. Na **janela Criar uma nova** janela de projeto, escolha **a App consola (.NET Framework)** e, em seguida, selecione **Next**.

1. Na **configuração** da sua nova janela de projeto, insira *o Helloworld* em **nome do Projeto,** escolha ou crie o caminho de diretório no **Local**, e, em seguida, selecione **Criar**.

1. A partir da barra de menus do Estúdio Visual, selecione **Tools** > **Get Tools and Features**, que abre o Instalador de Estúdios Visuais e exibe a caixa de diálogo **Modificador.**

1. Verifique se a carga de trabalho de desenvolvimento do ambiente de **trabalho .NET** está disponível. Se a carga de trabalho não tiver sido instalada, selecione a caixa de verificação ao lado e, em seguida, selecione **Modificar** para iniciar a instalação. Pode levar alguns minutos para descarregar e instalar.

   Se a caixa de verificação ao lado do desenvolvimento do ambiente de **trabalho .NET** já estiver selecionada, selecione **Close** to exit the dialog box.

   ![Ativar o desenvolvimento do ambiente de trabalho .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Instalador de estúdio visual próximo.

### <a name="install-the-speech-sdk"></a>Instale o SDK do Discurso

O próximo passo é instalar o [pacote NuGet Speech SDK,](https://aka.ms/csspeech/nuget)para que possa referenciar no código.

1. No Solution Explorer, clique no projeto **Helloworld** e, em seguida, selecione **Gerir pacotes NuGet** para mostrar o NuGet Package Manager.

   ![Gestor de Pacotes NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. No canto superior direito, encontre a caixa de entrega da Fonte de **Embalagem** e certifique-se de que **nuget.org** é selecionada.

1. No canto superior esquerdo, **selecione Browse**.

1. Na caixa de pesquisa, digite *Microsoft.CognitiveServices.Speech* e selecione **Enter**.

1. A partir dos resultados da pesquisa, selecione o pacote **Microsoft.CognitiveServices.Speech** e, em seguida, selecione **Instalar** para instalar a versão mais recente estável.

   ![Instale o pacote Microsoft.CognitiveServices.Speech NuGet](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Aceite todos os acordos e licenças para iniciar a instalação.

   Após a instalação da embalagem, aparece uma confirmação na janela da Consola do Gestor de **Pacotes.**

### <a name="choose-the-target-architecture"></a>Escolha a arquitetura-alvo

Agora, para construir e executar a aplicação da consola, crie uma configuração de plataforma que combine com a arquitetura do seu computador.

1. A partir da barra de menus, selecione **Build** > **Configuration Manager**. Aparece a caixa de diálogo do Gestor de **Configuração.**

   ![Caixa de diálogo do Gestor de Configuração](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Na caixa de drop-down da plataforma ative **solution,** selecione **New**. Aparece a caixa de diálogo new **solution Platform.**

1. No **Tipo ou selecione a nova** caixa de lançamento da plataforma:
   - Se estiver a executar o Windows de 64 bits, selecione **x64**.
   - Se estiver a executar o Windows de 32 bits, selecione **x86**.

1. Selecione **OK** **e,** em seguida, Feche .
