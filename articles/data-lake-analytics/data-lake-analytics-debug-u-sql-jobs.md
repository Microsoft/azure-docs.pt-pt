---
title: Código Debug C# para empregos U-SQL do Azure Data Lake
description: Este artigo descreve como depurar um vértice falhado U-SQL usando Azure Data Lake Tools para o Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: how-to
ms.date: 11/30/2017
ms.openlocfilehash: ca6fe4ad35e59472e8cf8f3b8476417e01c2668f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87131876"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Código C# definido pelo utilizador para trabalhos falhados de U-SQL

U-SQL fornece um modelo de extensibilidade usando C#. Nos scripts U-SQL, é fácil chamar funções C# e executar funções analíticas que a linguagem declarativa semelhante ao SQL não suporta. Para saber mais sobre a extensibilidade da U-SQL, consulte o [guia de programabilidade U-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

Na prática, qualquer código pode necessitar de depurar, mas é difícil depurar um trabalho distribuído com código personalizado na nuvem com ficheiros de registo limitados. [O Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) fornece uma funcionalidade chamada **Failed Vertex Debug**, que o ajuda a depurar mais facilmente as falhas que ocorrem no seu código personalizado. Quando o trabalho U-SQL falha, o serviço mantém o estado de falha e a ferramenta ajuda-o a descarregar o ambiente de falha de nuvem para a máquina local para depurar. O download local captura todo o ambiente em nuvem, incluindo quaisquer dados de entrada e código do utilizador.

O vídeo que se segue demonstra o Depurg Debug Falhado em Azure Data Lake Tools for Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> O Visual Studio requer as seguintes duas atualizações para a utilização desta funcionalidade: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) e o [Universal C Runtime for Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Descarregue o vértice falhado para a máquina local

Quando abre um trabalho falhado em Azure Data Lake Tools for Visual Studio, vê uma barra de alerta amarelo com mensagens de erro detalhadas no separador erro.

1. Clique **em Baixar** para descarregar todos os recursos e streams de entrada necessários. Se o download não estiver completo, clique em **Retry**.

2. Clique **em Abrir** depois de o download estar concluído para gerar um ambiente local de depuragem. Será aberta uma nova solução de depuragem e, se tiver a solução existente aberta no Visual Studio, certifique-se de a guardar e fechar antes de depurar.

![Azure Data Lake Analytics U-SQL depurar estúdio visual descarregar vértice](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Configure o ambiente de depuração

> [!NOTE]
> Antes de depurar, certifique-se de verificar **as exceções comuns** de tempo de execução da linguagem na janela Definições de Exceção **(Ctrl + Alt + E**).

![Azure Data Lake Analytics U-SQL depurar estúdio visual](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

Na nova instância do Estúdio Visual lançada, pode ou não encontrar o código fonte C# definido pelo utilizador:

1. [Posso encontrar o meu código-fonte na solução](#source-code-is-included-in-debugging-solution)

2. [Não consigo encontrar o meu código-fonte na solução.](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>O código-fonte está incluído na solução de depuragem

Há dois casos que o código fonte C# é capturado:

1. O código do utilizador é definido em ficheiro por trás do código (normalmente nomeado `Script.usql.cs` num projeto U-SQL).

2. O código do utilizador é definido no projeto da biblioteca da classe C# para aplicação U-SQL e registado como montagem com **informações de depuragem**.

Se o código fonte for importado para a solução, pode utilizar as ferramentas de depuração do Visual Studio (relógio, variáveis, etc.) para resolver o problema:

1. Prima **F5** para iniciar a depuração. O código funciona até ser interrompido por uma exceção.

2. Abra o ficheiro de código de origem e desfaça os pontos de rutura e, em seguida, pressione **F5** para depurar o código passo a passo.

    ![Azure Data Lake Analytics U-SQL depuração](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Código fonte não está incluído na solução de depuragem

Se o código de utilizador não estiver incluído no ficheiro code-behind ou se não tiver registado o conjunto com **informações de depuragem**, então o código fonte não está incluído automaticamente na solução de depuragem. Neste caso, precisa de passos adicionais para adicionar o seu código fonte:

1. Clique à direita **Solução 'VertexDebug' > Adicione > Projeto Existente...** para encontrar o código fonte de montagem e adicionar o projeto à solução de depurar.

    ![Azure Data Lake Analytics U-SQL depurar projeto](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Obtenha o caminho da pasta do projeto para o projeto **FailedVertexDebugHost.** 

3. Right-Click **o projeto de código fonte de montagem adicionado > Properties**, selecione o **separador Construir** à esquerda e cole o caminho copiado que termina com \bin\debug como saída > caminho de **saída**. O caminho de saída final é `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\` como.

    ![Azure Data Lake Analytics U-SQL depurg set pdb path](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Depois destas definições, comece a depurar com **F5** e breakpoints. Também pode utilizar as ferramentas de depuração do Estúdio Visual (relógio, variáveis, etc.) para resolver problemas.

> [!NOTE]
> Reconstrua o projeto de código fonte de montagem cada vez depois de modificar o código para gerar ficheiros .pdb atualizados.

## <a name="resubmit-the-job"></a>Reenviar o trabalho

Após a depuragem, se o projeto concluir com sucesso, a janela de saída mostra a seguinte mensagem:

`The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).`

![Azure Data Lake Analytics U-SQL depurador tem sucesso](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Para reenviar o trabalho falhado:

1. Para trabalhos com soluções por detrás de códigos, copie o código C# no ficheiro de origem por trás do código `Script.usql.cs` (normalmente).

2. Para trabalhos com conjuntos, clique com razão no projeto de código fonte de montagem na solução de depuração e registe as assembléias .dll atualizadas no seu catálogo Azure Data Lake.

3. Reenviar o trabalho da U-SQL.

## <a name="next-steps"></a>Passos seguintes

- [Guia de programabilidade U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Desenvolver operadores definidos pelo utilizador U-SQL para empregos Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testar e depurar tarefas U-SQL utilizando a execução local e o SDK U-SQL do Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)
- [Como resolver um trabalho anormal recorrente](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
