---
title: Código Debug C# para empregos do Lago De dados Azure U-SQL
description: Este artigo descreve como depurar um vértice falhado u-SQL usando ferramentas de lago de dados Azure para estúdio visual.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 72239fc1679d2ebbfd9c9b5be6b79b58efb760cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71315801"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Código C# definido pelo utilizador para empregos falhados da U-SQL

A U-SQL fornece um modelo de extensibility usando C#. Nos scripts U-SQL, é fácil ligar para funções C# e executar funções analíticas que a linguagem declarativa semelhante ao SQL não suporta. Para saber mais sobre a extebilidade U-SQL, consulte o [guia de programabilidade U-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

Na prática, qualquer código pode necessitar de depuração, mas é difícil depurar um trabalho distribuído com código personalizado na nuvem com ficheiros de registo limitados. [O Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) fornece uma funcionalidade chamada **Failed Vertex Debug,** que o ajuda a desmarcar mais facilmente as falhas que ocorrem no seu código personalizado. Quando o trabalho da U-SQL falha, o serviço mantém o estado de falha e a ferramenta ajuda-o a descarregar o ambiente de falha na nuvem para a máquina local para depuração. O download local captura todo o ambiente da nuvem, incluindo quaisquer dados de entrada e código de utilizador.

O vídeo seguinte demonstra o Debug Vertex Falhado em Ferramentas de Lago de Dados Azure para Estúdio Visual.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> O Visual Studio requer as seguintes duas atualizações para a utilização desta funcionalidade: [Microsoft Visual C++ 2015 Redistribuable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) e o Universal C [Runtime for Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Baixar vértice falhado para máquina local

Ao abrir um trabalho falhado no Azure Data Lake Tools for Visual Studio, vê uma barra de alerta amarelo com mensagens de erro detalhadas no separador de erros.

1. Clique em **Baixar** para descarregar todos os recursos necessários e fluxos de entrada. Se o download não estiver concluído, clique em **Retry**.

2. Clique em **Abrir** depois de o download completar para gerar um ambiente de depuração local. Será aberta uma nova solução de depuração, e se tiver uma solução existente aberta no Estúdio Visual, certifique-se de guardar e fechá-la antes de depurar.

![Azure Data Lake Analytics U-SQL debug visual debug debug estúdio descarregamento vertex](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Configure o ambiente de depuração

> [!NOTE]
> Antes de depurar, certifique-se de verificar exceções de tempo de execução de **linguagem comum** na janela Definições de Exceção **(CTRL + Alt + E**).

![Azure Data Lake Analytics U-SQL debug visual definição de estúdio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

Na nova instância do Estúdio Visual lançado, pode ou não encontrar o código fonte C# definido pelo utilizador:

1. [Posso encontrar o meu código de origem na solução](#source-code-is-included-in-debugging-solution)

2. [Não consigo encontrar o meu código de origem na solução.](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>O código fonte está incluído na solução de depuração

Há dois casos em que o código fonte C# é capturado:

1. O código de utilizador é definido em `Script.usql.cs` ficheiro seletiva (tipicamente nomeado num projeto U-SQL).

2. O código de utilizador é definido no projeto da biblioteca da classe C# para aplicação U-SQL, e registado como montagem com **informações de depuração**.

Se o código fonte for importado para a solução, pode utilizar as ferramentas de depuração do Estúdio Visual (relógio, variáveis, etc.) para resolver o problema:

1. Prima **F5** para iniciar a depuração. O código funciona até ser interrompido por uma exceção.

2. Abra o ficheiro de código de origem e desloque os pontos de rutura e, em seguida, prima **F5** para depurar o código passo a passo.

    ![Exceção de depuração do Lago Dedados Azure Data Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Código fonte não está incluído na solução de depuração

Se o código de utilizador não estiver incluído no ficheiro por trás do código, ou não tiver registado o conjunto com informações de **depuração,** então o código fonte não está incluído automaticamente na solução de depuração. Neste caso, precisa de passos adicionais para adicionar o seu código fonte:

1. Clique à direita **Solução 'VertexDebug' > Adicionar > Projeto Existente...** para encontrar o código fonte de montagem e adicionar o projeto à solução de depuração.

    ![Projeto de adição de debug i-SQL do Lago Debug Azure Data Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Obtenha o caminho da pasta do projeto para o projeto **FailedVertexDebugHost.** 

3. Clique à direita no projeto de código de origem de **montagem adicionado > Propriedades**, selecione o separador **Build** à esquerda e colhe o caminho copiado terminando com \bin\debug como Output > Output **Output path**. O caminho final `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`de saída é como.

    ![Azure Data Lake Analytics U-SQL debug set pdb path](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Depois destas definições, comece a depurar-se com **F5** e pontos de rutura. Também pode utilizar as ferramentas de depuração do Estúdio Visual (relógio, variáveis, etc.) para resolver o problema.

> [!NOTE]
> Reconstrua o projeto do código fonte de montagem cada vez que modificar o código para gerar ficheiros .pdb atualizados.

## <a name="resubmit-the-job"></a>Reenvie o trabalho

Após a depuração, se o projeto completar com sucesso, a janela de saída mostra a seguinte mensagem:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Depuração do Lago De dados Azure Analytics U-SQL tem sucesso](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Para reenviar o trabalho falhado:

1. Para trabalhos com soluções code-behind, copie o código C# `Script.usql.cs`no ficheiro fonte code-behind (tipicamente).

2. Para trabalhos com assembléias, clique no projeto de código fonte de montagem na solução de depuração e registe as assemblagens atualizadas .dll no seu catálogo do Lago de Dados Azure.

3. Reenvie o trabalho da U-SQL.

## <a name="next-steps"></a>Passos seguintes

- [Guia de programabilidade U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Desenvolver operadores definidos pelo utilizador U-SQL para trabalhos azure data lake analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testar e depurar tarefas U-SQL utilizando a execução local e o SDK U-SQL do Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)
- [Como resolver um trabalho anormal e recorrente](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
