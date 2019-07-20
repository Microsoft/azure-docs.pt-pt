---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 308ee2ef121648cb45152948926c5fd7fb934744
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362590"
---
1. Abra o Visual Studio 2019.

1. Na janela iniciar, selecione **criar um novo projeto**. 

1. Selecione **aplicativo de console (.NET Framework)** e, em seguida, selecione **Avançar**.

1. Em **nome do projeto**, `helloworld`digite e, em seguida, selecione **criar**.

1. Na barra de menus no Visual Studio, selecione **ferramentas** > **obter ferramentas e recursos**e verifique se a carga de **trabalho de desenvolvimento do .net desktop** está disponível. Se a carga de trabalho não tiver sido instalada, marque a caixa de seleção e, em seguida, selecione **Modificar** para iniciar a instalação. Pode levar alguns minutos para baixar e instalar.

   Se a caixa de seleção ao lado de **desenvolvimento de área de trabalho do .net** estiver selecionada, você poderá fechar a caixa de diálogo agora.

   ![Ativar o desenvolvimento do ambiente de trabalho .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

A próxima etapa é instalar o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget), para que você possa referenciá-lo no código.

1. Na Gerenciador de soluções, clique `helloworld`com o botão direito do mouse e selecione **gerenciar pacotes NuGet** para mostrar o Gerenciador de pacotes NuGet.

   ![Gerenciador de pacotes NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. No canto superior direito, localize a caixa suspensa **origem do pacote** e verifique se **NuGet.org** está selecionado.

1. No canto superior esquerdo, selecione **procurar**.

1. Na caixa de pesquisa, digite `Microsoft.CognitiveServices.Speech` pacote e pressione Enter.

1. Selecione `Microsoft.CognitiveServices.Speech`e, em seguida, selecione **instalar** para instalar a versão estável mais recente.

   ![Instale o pacote NuGet Microsoft. Cognitivaservices. fala](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Aceite todos os contratos e licenças para iniciar a instalação.

   Depois que o pacote é instalado, uma confirmação é exibida na janela do **console do Gerenciador de pacotes** .

Agora, para compilar e executar o aplicativo de console, crie uma configuração de plataforma correspondente à arquitetura do seu computador.

1. Na barra de menus, selecione **criar** > **Configuration Manager**. O **Configuration Manager** é apresentada a caixa de diálogo.

   ![Caixa de diálogo Configuration Manager](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Na caixa suspensa **plataforma de solução ativa** , selecione **novo**. A caixa de diálogo **nova plataforma de solução** é exibida.

1. Na caixa suspensa **digite ou selecione a nova plataforma** :
   - Se você estiver executando o Windows de 64 bits, selecione **x64**.
   - Se você estiver executando o Windows de 32 bits, selecione **x86**.

1. Selecione **OK** e, em seguida, **fechar**.
