---
title: Empregos de Debug U-SQL - Azure Data Lake Tools for Visual Studio Code
description: Aprenda a usar ferramentas do Lago de Dados Azure para o Código do Estúdio Visual para executar e depurar empregos U-SQL localmente.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 07/14/2017
ms.openlocfilehash: 194fe3494915be9f6784e1596e647885634817ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97969015"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Executar U-SQL e depurar localmente no Código do Estúdio Visual
Este artigo descreve como executar trabalhos U-SQL numa máquina de desenvolvimento local para acelerar as fases de codificação precoce ou depurar código localmente no Código do Estúdio Visual. Para obter instruções sobre a ferramenta Azure Data Lake Tool para código de estúdio visual, consulte [Azure Data Lake Tools para Código de Estúdio Visual](data-lake-analytics-data-lake-tools-for-vscode.md).

Apenas as instalações do Windows das Ferramentas do Lago de Dados Azure para o Estúdio Visual suportam a ação para executar u-SQL localmente e depurar u-SQL localmente. As instalações nos sistemas operativos macOS e Linux não suportam esta funcionalidade.

## <a name="set-up-the-u-sql-local-run-environment"></a>Configurar o ambiente de funcionação local U-SQL

1. Selecione Ctrl+Shift+P para abrir a paleta de comando e, em seguida, introduza **ADL: Descarregue** o Pacote de Execução Local para descarregar os pacotes.  

   ![Descarregue os pacotes de dependência local da ADL](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Localize os pacotes de dependência do caminho mostrado no painel **de saída** e, em seguida, instale BuildTools e Win10SDK 10240. Aqui está um caminho de exemplo:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Localizar os pacotes de dependência](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 Para instalar **os BuildTools,** clique visualcppbuildtools_full.exe na pasta LocalRunDependency e siga as instruções do assistente.   

    ![Instalar BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 Para instalar **o Win10SDK 10240,** clique sdksetup.exe na pasta LocalRunDependency/Win10SDK_10.0.10240_2 e siga as instruções do assistente.  

    ![Instalar Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Configurar a variável ambiental. Definir a variável **ambiente SCOPE_CPP_SDK** para:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Inicie o serviço de execução local e submeta o trabalho U-SQL a uma conta local 
Para o utilizador pela primeira vez, utilize **o ADL: Download Local Run Package** para descarregar pacotes locais de funcionaamento, caso não tenha [configurado o ambiente de funcionaamento local U-SQL](#set-up-the-u-sql-local-run-environment).

1. Selecione Ctrl+Shift+P para abrir a paleta de comando e, em seguida, introduza **ADL: Iniciar o Serviço de Execução Local**.   
2. Selecione **Aceitar** os Termos da Licença de Software da Microsoft pela primeira vez. 

   ![Aceitar os Termos de Licença de Software da Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. A consola cmd abre. Para os utilizadores pela primeira vez, é necessário introduzir **3**, e depois localizar o caminho da pasta local para a entrada e saída dos seus dados. Se não conseguir definir o caminho com as costas, tente cortar para a frente. Para outras opções, pode utilizar os valores predefinidos.

   ![Ferramentas do Lago de Dados para Código de Estúdio Visual executar cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Selecione Ctrl+Shift+P para abrir a paleta de comando, **introduza ADL: Submeter Job** e, em seguida, selecione **Local** para submeter o trabalho à sua conta local.

   ![Ferramentas do Lago de Dados para Código do Estúdio Visual selecionam local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Depois de submeter o trabalho, pode ver os detalhes da submissão. Para ver os detalhes da submissão, selecione **jobUrl** na janela **Saída.** Também pode ver o estado de submissão de emprego a partir da consola CMD. Introduza **7** na consola CMD se quiser saber mais detalhes do trabalho.

   ![Ferramentas do Lago de Dados para código de estúdio visual local executar ](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
    ![ data lake tools for Visual Studio Code local run cmd status](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Inicie um depurg local para o trabalho U-SQL  
Para o utilizador pela primeira vez:

1. Use **ADL: Descarregue o Pacote Local run** para descarregar pacotes locais de execução, se não tiver [configurado o ambiente de execução local U-SQL](#set-up-the-u-sql-local-run-environment).
2. Instale .NET Core SDK 2.0 como sugerido na caixa de mensagens, se não for instalado.
 
  ![lembrete instala Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Instale C# para Código de Estúdio Visual, conforme sugerido na caixa de mensagens se não for instalado. Clique **em Instalar** para continuar e, em seguida, reiniciar o VSCode.

![Lembrete para instalar C #](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Siga os passos abaixo para realizar o depurg local:
  
1. Selecione Ctrl+Shift+P para abrir a paleta de comando e, em seguida, introduza **ADL: Iniciar o Serviço de Execução Local**. A consola cmd abre. Certifique-se de que o **DataRoot** está definido.
2. Desaponte um ponto de rutura no seu código C# para trás.
3. De volta ao editor de scripts, clique à direita e selecione **ADL: Local Debug**.
    
   ![Ferramentas do Lago de Dados para o resultado do depurg local do Código do Estúdio Visual](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Passos seguintes
* [Utilizar as Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Desenvolver U-SQL com Python, R e CSharp para Azure Data Lake Analytics em VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Começar com data lake analytics usando PowerShell](data-lake-analytics-get-started-powershell.md)
* [Começar com data lake analytics usando o portal Azure](data-lake-analytics-get-started-portal.md)
* [Utilize ferramentas do Data Lake para o Estúdio Visual para o desenvolvimento de aplicações U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Use o catálogo Data Lake Analytics (U-SQL)](./data-lake-analytics-u-sql-get-started.md)
