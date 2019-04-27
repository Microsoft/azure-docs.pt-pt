---
title: Depurar definidas pelo utilizador código c# para tarefas de Azure Data Lake U-SQL com falhas
description: Este artigo descreve como depurar um vértice de falha de U-SQL com o Azure Data Lake Tools para Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 5417f66696191cebadc2af9c6d634419a0eb8e5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615290"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Depurar definidas pelo utilizador código c# para tarefas de U-SQL com falhas

U-SQL fornece um modelo de extensibilidade usando a linguagem c#. Em scripts U-SQL, é fácil chamar funções c# e realizar funções de análise que não oferece suporte a linguagem declarativa do tipo SQL. Para saber mais para extensibilidade U-SQL, veja [guia de programação para U-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

Na prática, qualquer código poderá ter de depuração, mas é difícil depurar um trabalho distribuído com código personalizado na cloud com ficheiros de registo limitado. [Azure Data Lake Tools para Visual Studio](https://aka.ms/adltoolsvs) fornece um recurso chamado **falha na depuração de vértice**, que ajuda a mais facilmente depurar as falhas que ocorrem no seu código personalizado. Quando a tarefa de U-SQL falha, o serviço mantém o estado de falha e a ferramenta ajuda a baixar o ambiente de falha de cloud para o computador local para depuração. O download local captura o ambiente de nuvem inteira, incluindo quaisquer dados de entrada e o código do usuário.

O vídeo seguinte demonstra a falha vértice de depuração no Azure Data Lake Tools para Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Visual Studio requer as seguintes duas atualizações para utilizar esta funcionalidade: [Microsoft Visual C++ 2015 redistribuível Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) e o [tempo de execução do Universal C para o Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Falha na transferência de vértice no computador local

Quando abre uma tarefa que falhou no Azure Data Lake Tools para Visual Studio, verá uma barra de alerta amarela com mensagens de erro detalhada no separador erro.

1. Clique em **transferir** para transferir todos os recursos necessários e fluxos de entrada. Se o download não concluir, clique em **repita**.

2. Clique em **aberto** depois de concluída a transferência para gerar um ambiente de depuração local. Será aberta uma nova solução de depuração e, se tiver existente solução aberta no Visual Studio,. Certifique-se de que guarde e feche-a antes de depuração.

![Azure vértice de download do visual studio de depuração do U-SQL do Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Configurar o ambiente de depuração

> [!NOTE]
> Antes de depurar, certifique-se de que consulta **exceções de tempo de execução de linguagem comuns** na janela definições de exceção (**Ctrl + Alt + E**).

![Definição do visual studio de depuração de U-SQL do Data Lake Analytics do Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

A nova instância do Visual Studio lançada, pode ou não pode localizar o definido pelo utilizador c# código-fonte:

1. [Pode localizar o meu código-fonte na solução](#source-code-is-included-in-debugging-solution)

2. [Não consigo encontrar meu código-fonte na solução](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Código-fonte está incluído na solução de depuração

Existem dois casos que o código-fonte c# é capturado:

1. O código de utilizador é definido no arquivo code-behind (normalmente, denominado `Script.usql.cs` num projeto U-SQL).

2. O código de utilizador é definido na classe biblioteca projeto c# para a aplicação de U-SQL e registado como assembly com **informações de depuração**.

Se o código-fonte é importado para a solução, pode usar as ferramentas de depuração do Visual Studio (veja, variáveis, etc.) para resolver o problema:

1. Prima **F5** para iniciar a depuração. O código é executado até que ele é interrompido por uma exceção.

2. Abra o ficheiro de código fonte e defina pontos de interrupção, em seguida, prima **F5** para depurar o código passo a passo.

    ![Exceção de depuração de U-SQL do Data Lake Analytics do Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Código-fonte não está incluído na solução de depuração

Se o código de utilizador não está incluído no arquivo code-behind, ou não a registrou o assembly com **informações de depuração**, em seguida, o código-fonte não é automaticamente incluído na solução de depuração. Neste caso, terá de passos adicionais para adicionar seu código-fonte:

1. Com o botão direito **solução 'VertexDebug' > Adicionar > projeto existente...**  para encontrar o assembly de código-fonte e adicione o projeto para a solução de depuração.

    ![Depuração de U-SQL do Data Lake Analytics do Azure adicionar projeto](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Obter o caminho da pasta de projeto para **FailedVertexDebugHost** projeto. 

3. Com o botão direito **o projeto de código de origem do assembly foi adicionado > propriedades**, selecione a **crie** separador à esquerda e cole o caminho copiado terminando \bin\debug como **saída > caminho de saída**. O caminho de saída final é como `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`.

    ![Caminho do pdb de conjunto de depuração do Azure U-SQL do Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Depois destas definições, iniciar a depuração com **F5** e pontos de interrupção. Também pode utilizar o Visual Studio (veja, variáveis, etc.) para resolver o problema de ferramentas de depuração.

> [!NOTE]
> Reconstrua o projeto de código de origem do assembly sempre que depois de modificar o código para gerar arquivos. pdb atualizado.

## <a name="resubmit-the-job"></a>Volte a submeter a tarefa

Depois de depuração, se o projeto for concluída com êxito a janela da saída mostra a mensagem seguinte:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Depuração de U-SQL do Data Lake Analytics do Azure com êxito](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

A submeter a tarefa falhada:

1. Para as tarefas com soluções de código-behind, copie o código do c# para o arquivo de origem do code-behind (normalmente `Script.usql.cs`).

2. Para as tarefas com assemblies, o projeto de código de origem do assembly na solução de depuração com o botão direito e registe os assemblies. dll atualizado para o catálogo de Azure Data Lake.

3. Volte a submeter a tarefa de U-SQL.

## <a name="next-steps"></a>Passos Seguintes

- [Guia de programação para U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Desenvolver operadores definidos pelo utilizador de U-SQL para tarefas de Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testar e depurar tarefas U-SQL utilizando a execução local e o SDK U-SQL do Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)
- [Como resolver problemas de uma tarefa periódica anormal](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
