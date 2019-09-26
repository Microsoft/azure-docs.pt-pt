---
title: Código C# de depuração para Azure data Lake trabalhos U-SQL
description: Este artigo descreve como depurar um vértice com falha do U-SQL usando Ferramentas do Azure Data Lake para Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 72239fc1679d2ebbfd9c9b5be6b79b58efb760cb
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315801"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Depurar o código definido C# pelo usuário para trabalhos de U-SQL com falha

O U-SQL fornece um modelo de C#extensibilidade usando. Em scripts U-SQL, é fácil chamar C# funções e executar funções analíticas que não dão suporte ao idioma declarativo do SQL. Para saber mais sobre a extensibilidade do U-SQL, consulte [Guia de programação do u-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

Na prática, qualquer código pode precisar de depuração, mas é difícil depurar um trabalho distribuído com código personalizado na nuvem com arquivos de log limitados. [Ferramentas do Azure data Lake para Visual Studio](https://aka.ms/adltoolsvs) fornece um recurso chamado **falha na depuração de vértice**, que ajuda você a depurar com mais facilidade as falhas que ocorrem em seu código personalizado. Quando o trabalho do U-SQL falha, o serviço mantém o estado de falha e a ferramenta ajuda você a baixar o ambiente de falha de nuvem para o computador local para depuração. O download local captura todo o ambiente de nuvem, incluindo qualquer dado de entrada e código de usuário.

O vídeo a seguir demonstra falha na depuração de vértice no Ferramentas do Azure Data Lake para Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> O Visual Studio requer as duas atualizações a seguir para usar esse recurso: [Microsoft Visual C++ 2015 redistribuível atualização 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) e o [tempo de execução universal C para Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Baixar o vértice com falha para o computador local

Ao abrir um trabalho com falha no Ferramentas do Azure Data Lake para Visual Studio, você verá uma barra de alerta amarela com mensagens de erro detalhadas na guia erro.

1. Clique em **baixar** para baixar todos os recursos e fluxos de entrada necessários. Se o download não for concluído, clique em **repetir**.

2. Clique em **abrir** depois que o download for concluído para gerar um ambiente de depuração local. Uma nova solução de depuração será aberta e, se você tiver uma solução existente aberta no Visual Studio, certifique-se de salvá-la e fechá-la antes da depuração.

![Azure Data Lake Analytics o vértice de download do Visual Studio para depuração U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Configurar o ambiente de depuração

> [!NOTE]
> Antes da depuração, certifique-se de verificar **exceções do Common Language Runtime** na janela configurações de exceção (**Ctrl + Alt + E**).

![Configuração do Visual Studio para depuração U-SQL do Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

Na nova instância iniciada do Visual Studio, você pode ou não encontrar o código-fonte C# definido pelo usuário:

1. [Posso encontrar meu código-fonte na solução](#source-code-is-included-in-debugging-solution)

2. [Não consigo encontrar meu código-fonte na solução](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>O código-fonte está incluído na solução de depuração

Há dois casos em que o C# código-fonte é capturado:

1. O código do usuário é definido no arquivo code-behind (normalmente `Script.usql.cs` nomeado em um projeto do U-SQL).

2. O código do usuário é definido C# no projeto de biblioteca de classes para o aplicativo U-SQL e registrado como assembly com **informações de depuração**.

Se o código-fonte for importado para a solução, você poderá usar as ferramentas de depuração do Visual Studio (Watch, Variables, etc.) para solucionar o problema:

1. Prima **F5** para iniciar a depuração. O código é executado até ser interrompido por uma exceção.

2. Abra o arquivo de código-fonte e defina pontos de interrupção e, em seguida, pressione **F5** para depurar o código passo a passo.

    ![Exceção de depuração U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>O código-fonte não está incluído na solução de depuração

Se o código do usuário não estiver incluído no arquivo code-behind ou se você não tiver registrado o assembly com as **informações de depuração**, o código-fonte não será incluído automaticamente na solução de depuração. Nesse caso, você precisa de etapas adicionais para adicionar o código-fonte:

1. Clique com o botão direito do mouse em **solução ' VertexDebug ' > adicionar > projeto existente...** para localizar o código-fonte do assembly e adicionar o projeto à solução de depuração.

    ![Adicionar projeto de depuração U-SQL do Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Obtenha o caminho da pasta do projeto para o projeto **FailedVertexDebugHost** . 

3. Clique com o botão direito **do mouse no projeto de código-fonte do assembly adicionado > Propriedades**, selecione a guia **Compilar** à esquerda e cole o caminho copiado que termina com \bin\Debug como **saída > caminho de saída**. O caminho de saída final é `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`como.

    ![Caminho do PDB do conjunto de depuração U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Após essas configurações, inicie a depuração com **F5** e pontos de interrupção. Você também pode usar as ferramentas de depuração do Visual Studio (Watch, Variables, etc.) para solucionar o problema.

> [!NOTE]
> Recompile o projeto de código-fonte do assembly a cada vez depois de modificar o código para gerar arquivos. pdb atualizados.

## <a name="resubmit-the-job"></a>Reenviar o trabalho

Após a depuração, se o projeto for concluído com êxito, a janela saída mostrará a seguinte mensagem:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Depuração do U-SQL Azure Data Lake Analytics com sucesso](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Para reenviar o trabalho com falha:

1. Para trabalhos com soluções code-behind, copie o C# código no arquivo de origem code-behind (normalmente `Script.usql.cs`).

2. Para trabalhos com assemblies, clique com o botão direito do mouse no projeto de código-fonte do assembly na solução de depuração e registre os assemblies. dll atualizados em seu catálogo de Azure Data Lake.

3. Reenvie o trabalho do U-SQL.

## <a name="next-steps"></a>Passos seguintes

- [Guia de programação do U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Desenvolver operadores do U-SQL definidos pelo usuário para trabalhos de Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testar e depurar tarefas U-SQL utilizando a execução local e o SDK U-SQL do Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)
- [Como solucionar problemas de um trabalho recorrente anormal](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
