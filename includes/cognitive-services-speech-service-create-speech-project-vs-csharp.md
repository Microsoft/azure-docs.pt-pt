---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327038"
---
Para criar um projeto do Visual Studio para o desenvolvimento do Windows, você precisa criar o projeto, configurar o Visual Studio para desenvolvimento de área de trabalho do .NET, instalar o SDK de fala e escolher a arquitetura de destino.

### <a name="create-the-project-and-add-the-workload"></a>Criar o projeto e adicionar a carga de trabalho

Para começar, crie o projeto no Visual Studio e verifique se o Visual Studio está configurado para desenvolvimento de área de trabalho do .NET:

1. Abra o Visual Studio 2019.

1. Na janela iniciar, selecione **criar um novo projeto**. 

1. Na janela **criar um novo projeto** , escolha **aplicativo de console (.NET Framework)** e, em seguida, selecione **Avançar**.

1. Na janela **configurar seu novo projeto** , digite *HelloWorld* no **nome do projeto**, escolha ou crie o caminho do diretório em **local**e, em seguida, selecione **criar**.

1. Na barra de menus do Visual Studio, selecione **ferramentas** > **obter ferramentas e recursos**, que abre instalador do Visual Studio e exibe a caixa de diálogo **modificando** .

1. Verifique se a carga de **trabalho do .net desktop Development** está disponível. Se a carga de trabalho não tiver sido instalada, marque a caixa de seleção ao lado dela e, em seguida, selecione **Modificar** para iniciar a instalação. Pode levar alguns minutos para baixar e instalar.

   Se a caixa de seleção ao lado de **desenvolvimento de área de trabalho .net** já estiver selecionada, selecione **fechar** para sair da caixa de diálogo.

   ![Ativar o desenvolvimento do ambiente de trabalho .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Feche Instalador do Visual Studio.

### <a name="install-the-speech-sdk"></a>Instalar o SDK de fala

A próxima etapa é instalar o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget), para que você possa referenciá-lo no código.

1. Na Gerenciador de Soluções, clique com o botão direito do mouse no projeto **HelloWorld** e selecione **gerenciar pacotes NuGet** para mostrar o Gerenciador de pacotes NuGet.

   ![Gerenciador de pacotes NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. No canto superior direito, localize a caixa suspensa **origem do pacote** e verifique se **NuGet.org** está selecionado.

1. No canto superior esquerdo, selecione **procurar**.

1. Na caixa de pesquisa, digite *Microsoft. cognitivaservices. Speech* e selecione **Enter**.

1. Nos resultados da pesquisa, selecione o pacote **Microsoft. cognitivaservices. fala** e, em seguida, selecione **instalar** para instalar a versão estável mais recente.

   ![Instale o pacote NuGet Microsoft. Cognitivaservices. fala](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Aceite todos os contratos e licenças para iniciar a instalação.

   Depois que o pacote é instalado, uma confirmação é exibida na janela do **console do Gerenciador de pacotes** .

### <a name="choose-the-target-architecture"></a>Escolher a arquitetura de destino

Agora, para compilar e executar o aplicativo de console, crie uma configuração de plataforma correspondente à arquitetura do seu computador.

1. Na barra de menus, selecione **criar** > **Configuration Manager**. O **Configuration Manager** é apresentada a caixa de diálogo.

   ![Caixa de diálogo Configuration Manager](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Na caixa suspensa **plataforma de solução ativa** , selecione **novo**. A caixa de diálogo **nova plataforma de solução** é exibida.

1. Na caixa suspensa **digite ou selecione a nova plataforma** :
   - Se você estiver executando o Windows de 64 bits, selecione **x64**.
   - Se você estiver executando o Windows de 32 bits, selecione **x86**.

1. Selecione **OK** e, em seguida, **fechar**.
