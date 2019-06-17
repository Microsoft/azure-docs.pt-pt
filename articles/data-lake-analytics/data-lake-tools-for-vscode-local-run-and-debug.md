---
title: Executar o U-SQL e depurar localmente no Azure Data Lake Tools para Visual Studio Code
description: Saiba como utilizar o Azure Data Lake Tools para Visual Studio Code para executar e depurar tarefas de U-SQL localmente.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: 765bcaab0f91e097be827bfa6e8f505ef5330d57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814397"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Executar o U-SQL e depurar localmente no Visual Studio Code
Este artigo descreve como executar tarefas de U-SQL numa máquina de desenvolvimento local para acelerar as fases iniciais de codificação ou para depurar código localmente no Visual Studio Code. Para obter instruções sobre a ferramenta do Azure Data Lake para Visual Studio Code, consulte [Use Azure Data Lake Tools para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Apenas Windows instalações das ferramentas do Azure Data Lake para Visual Studio suportam a ação a executar o U-SQL localmente e depurar o U-SQL localmente. As instalações no macOS e sistemas operacionais baseados em Linux não suportam esta funcionalidade.

## <a name="set-up-the-u-sql-local-run-environment"></a>Configurar o ambiente de execução local do U-SQL

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos e, em seguida, introduza **ADL: Transferir o pacote de execução Local** para transferir os pacotes.  

   ![Transfira os pacotes de dependência de LocalRun do ADL](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Localize os pacotes de dependência do caminho de mostra os **saída** painel e, em seguida, instale o BuildTools e Win10SDK 10240. Este é um caminho de exemplo:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Localize os pacotes de dependência](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 para instalar **BuildTools**, clique em visualcppbuildtools_full.exe na pasta LocalRunDependency, em seguida, siga as instruções do assistente.   

    ![Instalar BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 para instalar **Win10SDK 10240**, clique em sdksetup.exe na pasta LocalRunDependency/Win10SDK_10.0.10240_2, em seguida, siga as instruções do assistente.  

    ![Instalar Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Defina a variável de ambiente. Definir o **SCOPE_CPP_SDK** variável de ambiente para:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Inicie o serviço de execução local e submeter a tarefa de U-SQL de uma conta local 
Para o utilizador de iniciantes, utilize **ADL: Transferir o pacote de execução Local** para transferir pacotes de execução locais, se não o tiver [configurar o ambiente de execução local do U-SQL](#set-up-the-u-sql-local-run-environment).

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos e, em seguida, introduza **ADL: Iniciar o serviço de execução Local**.   
2. Selecione **Accept** para aceitar os termos de licença de Software da Microsoft pela primeira vez. 

   ![Aceite os termos de licença de Software da Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Abre a consola do cmd. Para iniciantes utilizadores, tem de introduzir **3**e, em seguida, localize o caminho da pasta local para os seus dados de entrada e saída. Para outras opções, pode utilizar os valores predefinidos. 

   ![Ferramentas do Data Lake para Visual Studio Code cmd de execução local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Selecione Ctrl + Shift + P para abrir a paleta de comandos, introduza **ADL: Submeter tarefa**e, em seguida, selecione **Local** para submeter a tarefa para a sua conta local.

   ![Ferramentas do Data Lake para Visual Studio Code, selecione local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Depois de submeter a tarefa, pode ver os detalhes de envio. Para ver os detalhes de envio, selecione **jobUrl** no **saída** janela. Também pode ver o estado da submissão de tarefa da consola do cmd. Introduza **7** na consola do cmd, se quiser saber mais detalhes da tarefa.

   ![Data Lake Tools para execução a saída de local de Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools para o local do Visual Studio Code cmd estado de execução](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Iniciar uma depuração local para a tarefa de U-SQL  
Para o utilizador de iniciantes:

1. Utilize **ADL: Transferir o pacote de execução Local** para transferir pacotes de execução locais, se não o tiver [configurar o ambiente de execução local do U-SQL](#set-up-the-u-sql-local-run-environment).
2. Instale .NET Core SDK 2.0 conforme sugerido na caixa de mensagem, se não instalado.
 
  ![lembrete instala Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Instalar o C# para Visual Studio Code, como sugerido na caixa de mensagem, se não instalado. Clique em **instale** para continuar e, em seguida, reinicie o VSCode.

    ![Lembrete para instalar o c#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Siga os passos abaixo para realizar a depuração local:
  
1. Selecione Ctrl + Shift + P para abrir a paleta de comandos e, em seguida, introduza **ADL: Iniciar o serviço de execução Local**. Abre a consola do cmd. Certifique-se de que o **DataRoot** está definido.
2. Defina um ponto de interrupção em seu c# code-behind.
3. Voltar ao editor de scripts, o botão direito do mouse e selecione **ADL: Depuração local**.
    
   ![Data Lake Tools para o resultado de depuração local do Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Passos Seguintes
* [Utilizar as Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Desenvolver o U-SQL com o Python, R e CSharp para o Azure Data Lake Analytics no VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Introdução ao Data Lake Analytics com o PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução ao Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Utilizar ferramentas do Data Lake para Visual Studio para desenvolver aplicações U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Catálogo de Analytics(U-SQL) utilizar Data Lake](data-lake-analytics-use-u-sql-catalog.md)
