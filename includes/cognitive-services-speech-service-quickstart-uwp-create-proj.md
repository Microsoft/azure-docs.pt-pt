---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: 2e495f9aed18ebc4007e63b8ef03641a72d21079
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56963344"
---
1. Inicie o Visual Studio 2017.

1. Certifique-se de que a carga de trabalho de **desenvolvimento da Plataforma Universal do Windows** está disponível. Escolha **Ferramentas** > **Obter Ferramentas e Funcionalidades** na barra de menus do Visual Studio para abrir o instalador do Visual Studio. Se esta carga de trabalho já estiver ativada, feche a caixa de diálogo.

    ![Captura de ecrã do instalador do Visual Studio, com o separador Cargas de Trabalho realçado](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

    Caso contrário, selecione a caixa junto a **desenvolvimento para várias plataformas .NET,** e selecione **Modificar** no canto inferior direito da caixa de diálogo. A instalação das novas funcionalidades demora alguns minutos.

1. Crie uma aplicação Universal do Windows do Visual C#. Em primeiro lugar, escolha **Ficheiro** > **Novo** > **Projeto** no menu. Na caixa de diálogo **Novo Projeto**, expanda **Instalado** > **Visual C#** > **Windows Universal** no painel esquerdo. Em seguida, selecione **Aplicação em branco (Universal Windows)**. No nome do projeto, introduza *helloworld*.

    ![Captura de ecrã da caixa de diálogo Novo Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. O SDK de Velocidade exige que a sua aplicação seja criada para a Atualização do Windows 10 Fall Creators ou posterior. Na janela **Novo Projeto da Plataforma Universal do Windows** que é apresentada, escolha **Atualização do Windows 10 Fall Creators (10.0; Compilação 16299)** como **Versão mínima**. Na caixa **Versão de destino**, selecione esta ou qualquer versão posterior e, em seguida, clique em **OK**.

    ![Captura de ecrã da janela Novo Projeto de Plataforma Universal do Windows](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Se estiver a executar o Windows de 64 bits, pode alternar a sua plataforma de compilação para `x64`, com o menu de lista pendente da barra de ferramentas do Visual Studio. (O Windows de 64 bits pode executar aplicações de 32 bits, por isso pode deixá-lo definir para `x86`, se preferir.)

   ![Captura de ecrã da barra de ferramentas do Visual Studio, com x64 realçado](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > O SDK de voz só suporta processadores compatíveis com o Intel. O ARM não é atualmente suportado.

1. Instale e faça referência ao [pacote NuGet do SDK de Voz](https://aka.ms/csspeech/nuget). No Explorador de Soluções, clique com o botão direito do rato na solução e selecione **Gerir Pacotes NuGet para Solução**.

    ![Captura de ecrã do Explorador de Soluções, com a opção Gerir Pacotes NuGet para Solução realçada](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. No canto superior direito, no campo **Origem do Pacote**, selecione **nuget.org**. Procure o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **helloworld**.

    ![Captura de ecrã da caixa de diálogo Gerir Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Instalar pacote NuGet")

1. Aceite a licença apresentada para iniciar a instalação do pacote NuGet.

    ![Captura de ecrã da caixa de diálogo Aceitação da Licença](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-06-nuget-license.png "Aceitar a Licença")

1. A seguinte linha de saída aparece na consola do Gestor de Pacotes.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.3.1' to helloworld
   ```

1. Uma vez que a aplicação utiliza o microfone para entrada de voz, adicione a capacidade **Microfone** ao projeto. No Explorador de Soluções, faça duplo clique em **Package.appxmanifest** para editar o manifesto de aplicação. Em seguida, mude para o separador **Capacidades**, selecione a caixa para a capacidade **Microfone** e guarde as alterações.

   ![Captura de ecrã do manifesto de aplicação de Visual Studio com Capacidades e Microfone realçado](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)
