---
title: Depurar trabalhos do U-SQL-código de Ferramentas do Azure Data Lake para Visual Studio
description: Saiba como usar Ferramentas do Azure Data Lake para Visual Studio código para executar e depurar trabalhos do U-SQL localmente.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: 7c425ee8c9fd99420bbcbba31c7a74c360938aef
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338254"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Executar o U-SQL e depurar localmente no Visual Studio Code
Este artigo descreve como executar trabalhos do U-SQL em um computador de desenvolvimento local para acelerar as fases de codificação iniciais ou para depurar código localmente no Visual Studio Code. Para obter instruções sobre Azure Data Lake ferramenta para Visual Studio Code, consulte [usar código de ferramentas do Azure data Lake para Visual Studio](data-lake-analytics-data-lake-tools-for-vscode.md).

Somente instalações do Windows do Ferramentas do Azure Data Lake para Visual Studio dão suporte à ação para executar o U-SQL localmente e depurar o U-SQL localmente. As instalações em sistemas operacionais com macOS e Linux não oferecem suporte a esse recurso.

## <a name="set-up-the-u-sql-local-run-environment"></a>Configurar o ambiente de execução local do U-SQL

1. Selecione CTRL + SHIFT + P para abrir a paleta de comandos e, em **seguida, digite ADL: Baixe o pacote** de execução local para baixar os pacotes.  

   ![Baixar os pacotes de dependência do ADL LocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Localize os pacotes de dependência do caminho mostrado no painel de **saída** e, em seguida, instale BuildTools e Win10SDK 10240. Aqui está um exemplo de caminho:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Localizar os pacotes de dependência](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2,1 para instalar o **BuildTools**, clique em visualcppbuildtools_full. exe na pasta LocalRunDependency e siga as instruções do assistente.   

    ![Instalar o BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2,2 para instalar o **Win10SDK 10240**, clique em sdksetup. exe na pasta LocalRunDependency/win10sdk_ 10.0.10240 _2 e siga as instruções do assistente.  

    ![Instalar o Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Configure a variável de ambiente. Defina a variável de ambiente **SCOPE_CPP_SDK** como:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Iniciar o serviço de execução local e enviar o trabalho do U-SQL para uma conta local 
Para o usuário da primeira vez, use **ADL: Baixe o pacote** de execução local para baixar pacotes de execução local, se você não tiver configurado o ambiente de [execução local do U-SQL](#set-up-the-u-sql-local-run-environment).

1. Selecione CTRL + SHIFT + P para abrir a paleta de comandos e, em **seguida, digite ADL: Inicie o serviço**de execução local.   
2. Selecione **aceitar** para aceitar os termos de licença para software Microsoft pela primeira vez. 

   ![Aceitar os termos de licença para software Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. O console cmd é aberto. Para usuários de primeira hora, você precisa inserir **3**e, em seguida, localizar o caminho da pasta local para a entrada e saída de dados. Se você não conseguir definir o caminho com barras invertidas, tente barras invertidas. Para outras opções, você pode usar os valores padrão.

   ![Data Lake ferramentas para Visual Studio Code executar cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Selecione CTRL + SHIFT + P para abrir a paleta de comandos, **digite ADL: Envie o**trabalho e selecione **local** para enviar o trabalho para sua conta local.

   ![Ferramentas de Data Lake para Visual Studio Code selecione local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Depois de enviar o trabalho, você poderá exibir os detalhes de envio. Para exibir os detalhes de envio, selecione **jobUrl** na janela **saída** . Você também pode exibir o status de envio do trabalho no console cmd. Digite **7** no console do cmd se você quiser saber mais detalhes do trabalho.

   ![Ferramentas de data Lake para Visual Studio Code saída](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![de execução local data Lake ferramentas para Visual Studio Code status cmd de execução local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Iniciar uma depuração local para o trabalho do U-SQL  
Para o usuário da primeira vez:

1. Usar **ADL: Baixe o pacote** de execução local para baixar pacotes de execução local, se você não tiver configurado o ambiente de [execução local do U-SQL](#set-up-the-u-sql-local-run-environment).
2. Instale SDK do .NET Core 2,0 como sugerido na caixa de mensagem, se não estiver instalado.
 
  ![o lembrete instala dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Instale C# o para Visual Studio Code conforme sugerido na caixa de mensagem se não estiver instalado. Clique em **instalar** para continuar e reinicie o VSCode.

    ![Lembrete para instalarC#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Siga as etapas abaixo para executar a depuração local:
  
1. Selecione CTRL + SHIFT + P para abrir a paleta de comandos e, em **seguida, digite ADL: Inicie o serviço**de execução local. O console cmd é aberto. Verifique se a **DataRoot** está definida.
2. Defina um ponto de interrupção C# no code-behind.
3. Volte ao editor de scripts, clique com o botão **direito do mouse e selecione ADL: Depuração**local.
    
   ![Data Lake de ferramentas para Visual Studio Code resultado de depuração local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Passos seguintes
* [Utilizar as Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Desenvolva o U-SQL com Python, R e CSharp para Azure Data Lake Analytics no VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Introdução ao Data Lake Analytics usando o PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução ao Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Use as ferramentas de Data Lake para o Visual Studio para desenvolver aplicativos U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Usar catálogo Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
