---
title: Empregos Debug U-SQL - Ferramentas de Lago de Dados Azure para Código de Estúdio Visual
description: Aprenda a usar ferramentas de lago de dados Azure para o Código de Estúdio Visual para executar e depurar empregos u-SQL localmente.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: e51b5640163546c673a1b0f61da47ccd992f27ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72030031"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Executar U-SQL e depurar localmente em Código de Estúdio Visual
Este artigo descreve como executar trabalhos U-SQL numa máquina de desenvolvimento local para acelerar as fases de codificação precoceou para depurar código localmente no Código do Estúdio Visual. Para obter instruções sobre a Ferramenta do Lago de Dados Azure para código de estúdio visual, consulte [Utilize ferramentas de lago de dados azure para código de estúdio visual](data-lake-analytics-data-lake-tools-for-vscode.md).

Apenas as instalações do Windows das Ferramentas do Lago de Dados Azure para o Estúdio Visual suportam a ação para executar u-SQL localmente e depurar u-SQL localmente. As instalações em sistemas operativos baseados em macOS e Linux não suportam esta funcionalidade.

## <a name="set-up-the-u-sql-local-run-environment"></a>Criar o ambiente de corrida local U-SQL

1. Selecione Ctrl+Shift+P para abrir a paleta de comando e, em seguida, introduza **a ADL: Descarregue** o Pacote local de execução para descarregar os pacotes.  

   ![Descarregue os pacotes de dependência adL localrun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Localize os pacotes de dependência a partir do caminho mostrado no painel **de saída** e, em seguida, instale BuildTools e Win10SDK 10240. Aqui está um caminho de exemplo:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Localizar os pacotes de dependência](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 Para instalar **BuildTools,** clique em visualcppbuildtools_full.exe na pasta LocalRunDependency e siga as instruções do assistente.   

    ![Instalar BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 Para instalar **win10SDK 10240,** clique em sdksetup.exe na pasta LocalRunDependency/Win10SDK_10.0.10240_2 e, em seguida, siga as instruções do assistente.  

    ![Instale win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Configurar a variável ambiental. Definir a variável **ambiente SCOPE_CPP_SDK** para:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Inicie o serviço de execução local e submeta o trabalho U-SQL a uma conta local 
Para o utilizador pela primeira vez, utilize **o ADL: Download Local Run Package** para descarregar pacotes locais de execução, se não tiver configurado o ambiente de [execução local U-SQL](#set-up-the-u-sql-local-run-environment).

1. Selecione Ctrl+Shift+P para abrir a paleta de comando e, em seguida, introduza **a ADL: Iniciar**o Serviço de Execução Local .   
2. Selecione **Aceitar** aceitar os Termos da Licença de Software da Microsoft pela primeira vez. 

   ![Aceite os Termos da Licença de Software da Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. A consola cmd abre. Para os utilizadores pela primeira vez, é necessário introduzir **3**, e depois localizar o caminho da pasta local para a sua entrada e saída de dados. Se não conseguir definir o caminho com costas, tente cortar para a frente. Para outras opções, pode utilizar os valores predefinidos.

   ![Ferramentas do Lago de Dados para Código de Estúdio Visual local executar cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Selecione Ctrl+Shift+P para abrir a paleta de comando, insira **ADL: Enviar a Sujeite**, e, em seguida, selecione **Local** para submeter o trabalho à sua conta local.

   ![Ferramentas do Lago de Dados para Código de Estúdio Visual selecionam localmente](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Depois de submeter o trabalho, pode ver os detalhes da submissão. Para visualizar os detalhes de submissão, selecione **jobUrl** na janela **Saída.** Também pode visualizar o estado de submissão de trabalho da consola cmd. Introduza **7** na consola cmd se quiser saber mais detalhes sobre o trabalho.

   ![Ferramentas de Data Lake para](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Visual Studio Code saída local Data Lake Tools for Visual Studio Code local executar o estado cmd local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Inicie um depurado local para o trabalho da U-SQL  
Para o primeiro utilizador:

1. Use **ADL: Descarregue** o Pacote local de execução para descarregar pacotes locais de execução, se não tiver configurado o [ambiente de execução local U-SQL](#set-up-the-u-sql-local-run-environment).
2. Instale .NET Core SDK 2.0 conforme sugerido na caixa de mensagens, se não for instalado.
 
  ![lembrete instala Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Instale C# para Visual Studio Code, conforme sugerido na caixa de mensagens, se não for instalado.Clique em **Instalar** para continuar e, em seguida, reiniciar o VSCode.

    ![Lembrete para instalar C #](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Siga os passos abaixo para realizar o depurado local:
  
1. Selecione Ctrl+Shift+P para abrir a paleta de comando e, em seguida, introduza **a ADL: Iniciar**o Serviço de Execução Local . A consola cmd abre. Certifique-se de que o **DataRoot** está definido.
2. Delicie um ponto de rutura no seu código C# atrás.
3. De volta ao editor de scripts, clique à direita e selecione **ADL: Debug Local**.
    
   ![Ferramentas do Lago de Dados para o resultado local do depuração do Código do Estúdio Visual](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Passos seguintes
* [Utilizar as Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Desenvolva U-SQL com Python, R e CSharp para Azure Data Lake Analytics em VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Começar com data lake analytics usando PowerShell](data-lake-analytics-get-started-powershell.md)
* [Começar com data lake analytics usando o portal Azure](data-lake-analytics-get-started-portal.md)
* [Utilize ferramentas de data lake para estúdio visual para desenvolver aplicações U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilize o catálogo data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
