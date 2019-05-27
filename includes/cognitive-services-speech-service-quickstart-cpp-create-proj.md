---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: af61d975b540787b9b54d9fdea66773c10aeb6ac
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145451"
---
1. Inicie o Visual Studio 2017.

1. Certifique-se de que a carga de trabalho de **Desenvolvimento de ambiente de trabalho com C++** está disponível. Escolha **Ferramentas** > **Obter Ferramentas e Funcionalidades** na barra de menus do Visual Studio para abrir o instalador do Visual Studio. Se esta carga de trabalho já estiver ativada, avance para o passo seguinte.

    ![Captura de ecrã do separador Cargas de Trabalho do Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

    Caso contrário, assinale a caixa junto a **Desenvolvimento de ambiente de trabalho com C++**.

1. Certifique-se de que o componente **Gestor de pacotes NuGet** está disponível. Mude para o separador **Componentes individuais** da caixa de diálogo do instalador do Visual Studio e selecione **Gestor de pacotes NuGet**, se não estiver já ativado.

      ![Captura de ecrã do separador Componentes individuais do Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-nuget-package-manager.png)

1. Se precisar de ativar a carga de trabalho C++ ou o NuGet, selecione **Modificar** (no canto inferior direito da caixa de diálogo). A instalação das novas funcionalidades demora alguns minutos. Se as duas funcionalidades já estiverem ativadas, feche a caixa de diálogo em vez disso.

1. Crie uma nova Aplicação de Consola do Ambiente de Trabalho do Windows com o Visual C++. Em primeiro lugar, escolha **Ficheiro** > **Novo** > **Projeto** no menu. Na caixa de diálogo **Novo Projeto**, expanda **Instalado** > **Visual C++** > **Ambiente de Trabalho do Windows** no painel esquerdo. Em seguida, selecione **Aplicação de Consola do Windows**. No nome do projeto, introduza *helloworld*.

    ![Captura de ecrã da caixa de diálogo Novo Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Se estiver a executar o Windows de 64 bits, poderá alternar a sua plataforma de compilação para `x64`, com o menu de lista pendente da barra de ferramentas do Visual Studio. (As versões de 64 bits do Windows podem executar aplicações de 32 bits, pelo que não é um requisito.)

    ![Captura de ecrã da barra de ferramentas do Visual Studio, com a opção x64 realçada](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. No Explorador de Soluções, clique com o botão direito do rato na solução e, em seguida, escolha **Gerir Pacotes NuGet para Solução**.

    ![Captura de ecrã do Explorador de Soluções, com a opção Gerir Pacotes NuGet para Solução realçada](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. No canto superior direito, no campo **Origem do Pacote**, selecione **nuget.org**. Procure o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **helloworld**.

    ![Captura de ecrã da caixa de diálogo Gerir Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > A versão atual do SDK de Voz dos Serviços Cognitivos é `1.5.0`.

1. Aceite a licença apresentada para iniciar a instalação do pacote NuGet.

    ![Captura de ecrã da caixa de diálogo Aceitação da Licença](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-05-nuget-license.png)

Depois de o pacote ser instalado, é apresentada uma mensagem de confirmação na consola do Gestor de Pacotes.
