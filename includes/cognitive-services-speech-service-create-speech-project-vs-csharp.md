---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: afe6f1493c7fa8272c67f23d6708ad6e4eea9381
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184552"
---
1. Inicie o Visual Studio 2017.

1. Na barra de menus no Visual Studio, selecione **ferramentas > obter ferramentas** e certifique-se de que o **desenvolvimento de área de trabalho .NET** carga de trabalho está disponível. Se não tiver sido instalada a carga de trabalho, marque a caixa de verificação, em seguida, clique em **modificar** para iniciar a instalação. Pode demorar alguns minutos para transferir e instalar.

   Se a caixa de verificação junto a **desenvolvimento no ambiente de trabalho .NET** é selecionada, pode fechar a caixa de diálogo agora.

   ![Ativar o desenvolvimento do ambiente de trabalho .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Em seguida, vamos criar um projeto. Na barra de menus, selecione **ficheiro > novo > projeto**. Quando for apresentada a caixa de diálogo, do painel esquerdo expandir estas secções **instalada > Visual C# > área de trabalho do Windows** e selecione **a aplicação de consola (.NET Framework)** . Nome este projeto *helloworld*.

    ![Criar Uma Aplicação de Consola do Visual C# (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Criar Uma Aplicação de Consola do Visual C# (.NET Framework)")

1. Agora que o projeto estiver configurado, temos de instalar o [pacote NuGet do SDK de voz](https://aka.ms/csspeech/nuget) e referenciá-lo no nosso código. Localize o Explorador de soluções e com o botão direito no helloworld. No menu, selecione **gerir pacotes NuGet...** .

   ![Clique com o botão direito do rato em Gerir Pacotes NuGet para Solução](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Gerir Pacotes NuGet para Solução")

1. No canto superior direito do Gestor de pacotes NuGet, localize a **origem do pacote** lista pendente e certifique-se de que **nuget.org** está selecionada. Em seguida, selecione **navegue** e procure o `Microsoft.CognitiveServices.Speech` do pacote e instale a versão estável mais recente.

   ![Instalar o Pacote NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Instalar o Pacote NuGet")

1. Aceite todos os contratos e licenças para iniciar a instalação.

   ![Aceitar a licença](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Aceitar a licença")

    Depois de o pacote ser instalado, é apresentada uma mensagem de confirmação na consola do Gestor de Pacotes.

1. A próxima etapa é criar uma configuração de plataforma que corresponde à arquitetura do computador que estiver a utilizar para criar e executar a aplicação de consola. Na barra de menus, selecione **crie** > **do Configuration Manager...** .

    ![Iniciar o Configuration Manager](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Iniciar o Configuration Manager")

1. Na **Configuration Manager** caixa de diálogo caixa, localize a **plataforma de solução Active Directory** na lista pendente e selecione **New**.

    ![Adicionar uma nova plataforma na janela do Configuration Manager](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Adicionar uma nova plataforma na janela do Configuration Manager")

1. Se estiver a executar o Windows de 64 bits, quando solicitado pela **escreva ou selecione a nova plataforma**, `x64`. Se estiver a executar o Windows de 32 bits, selecione `x86`. Quando tiver terminado, clique em **OK**.

    ![No Windows de 64 bits, adicione uma nova plataforma com o nome "x64"](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Adicionar plataforma x64")
