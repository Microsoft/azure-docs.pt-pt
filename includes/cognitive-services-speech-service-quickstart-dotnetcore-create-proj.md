---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 3f1a24db459cabd65d9ce17b89105c3b9ab8abb7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188495"
---
1. Inicie o Visual Studio 2019.

1. Certifique-se de que a carga de trabalho de **desenvolvimento para várias plataformas .NET** está disponível. Escolha **ferramentas**  >  **obter ferramentas e funcionalidades** da barra de menus Visual Studio para abrir o instalador do Estúdio Visual. Se esta carga de trabalho já estiver ativada, feche a caixa de diálogo.

   ![Captura de ecrã do instalador do Visual Studio, com o separador Cargas de Trabalho realçado](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   Caso contrário, selecione a caixa junto a **desenvolvimento para várias plataformas .NET Core,** e selecione **Modificar** no canto inferior direito da caixa de diálogo. A instalação da nova funcionalidade demora alguns minutos.

1. Crie uma nova Aplicação de Consola de Visual C# .NET Core. Na caixa de diálogo **Novo Projeto**, no painel à esquerda, expanda **Instalado** > **Visual C#** > **.NET Core**. Em seguida, selecione **Aplicação de Consola (.NET Core)**. No nome do projeto, introduza *helloworld*.

   ![Captura de ecrã da caixa de diálogo Novo Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Criar aplicativo de consola Visual C# (.NET Core)")

1. Instale e faça referência ao [pacote NuGet do SDK de Voz](https://aka.ms/csspeech/nuget). No Solution Explorer, clique com o botão direito na solução e **selecione Gerir pacotes nuget para solução.**

   ![Screenshot do Solution Explorer, com a Gestão de Pacotes NuGet para solução em destaque](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Gerir pacotes nuget para solução")

1. No canto superior direito, no campo **Fonte de Pacote,** selecione **nuget.org**. Procure o `Microsoft.CognitiveServices.Speech` pacote e instale-o no projeto **Helloworld.**

   ![Screenshot que mostra a caixa de diálogo "Gerir pacotes para solução".](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Instalar pacote NuGet")

1. Aceite a licença apresentada para iniciar a instalação do pacote NuGet.

   ![Captura de ecrã da caixa de diálogo Aceitação da Licença](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Aceite a licença")

Depois de o pacote ser instalado, será apresentada uma mensagem de confirmação na consola do Gestor de Pacotes.
