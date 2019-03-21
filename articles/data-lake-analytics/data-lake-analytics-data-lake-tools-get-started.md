---
title: Introdução ao Azure Data Lake Analytics com o Visual Studio
description: Saiba como instalar as Ferramentas do Data Lake para Visual Studio e como desenvolver e testar scripts U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/13/2018
ms.openlocfilehash: b463946402eee40d0de0942eeaf37a6f9ea59990
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083067"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

O Azure Data Lake e ferramentas do Stream Analytics incluem funcionalidades relacionadas aos dois serviços do Azure, Azure Data Lake Analytics e o Azure Stream Analytics. Para obter mais informações sobre os cenários do Azure Stream Analytics, consulte [ferramentas do Azure Stream Analytics para Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

Este artigo descreve como utilizar o Visual Studio para criar contas do Azure Data Lake Analytics, definir tarefas em [U-SQL](data-lake-analytics-u-sql-get-started.md)e submeter tarefas para o serviço Data Lake Analytics. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

> [!IMPORTANT]
> A Microsoft recomenda que atualize as Ferramentas do Azure Data Lake para a versão do Visual Studio 2.3.3000.4 ou posterior. As versões anteriores já não estão disponíveis para transferência e foram preteridas. 
> 
> **O que tenho de fazer?**
> 
> 1. Verifique se está a utilizar uma versão anterior à versão 2.3.3000.4 das Ferramentas do Azure Data Lake para o Visual Studio. 
> 
>    ![Verificar a versão da ferramenta](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
> 
> 2. Se a sua versão for anterior à versão 2.3.3000.4, atualize as Ferramentas do Azure Data Lake para o Visual Studio, visitando o centro de transferências: 
>    - [Para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Para Visual Studio 2013 e 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)


## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio**: São suportadas todas as edições, exceto Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* Versão 2.7.1 ou superior do **Microsoft Azure SDK para .NET**.  Instale-a através do [instalador de plataforma Web](https://www.microsoft.com/web/downloads/platform.aspx).
* A conta do **Data Lake Analytics**. Para criar uma conta, consulte [Introdução ao Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Instalar as Ferramentas do Azure Data Lake para Visual Studio

Este tutorial exige que as Ferramentas do Data Lake para Visual Studio estejam instaladas. Siga as [instruções de instalação](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Ligar-se a uma conta do Azure Data Lake Analytics

1. Abra o Visual Studio.

2. Abra o Server Explorer ao selecionar **Ver** > **Server Explorer**.

3. Clique com botão direito do rato em **Azure**. Em seguida, selecione **Ligar-se à subscrição do Microsoft Azure** e siga as instruções.

4. No Server Explorer, selecione **Azure** > **Data Lake Analytics**. Verá uma lista das suas contas do Data Lake Analytics.

## <a name="write-your-first-u-sql-script"></a>Escrever o seu primeiro script U-SQL

O texto apresentado em seguida é um script U-SQL simples. Este define um pequeno conjunto de dados e grava o conjunto de dados no Data Lake Store como um ficheiro com o nome `/data.csv`.

```
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Submeter uma tarefa do Data Lake Analytics

1. Selecione **Ficheiro** > **Novo** > **Projeto**.

2. Selecione o tipo **Projeto U-SQL** e clique em **OK**. O Visual Studio cria uma solução com um ficheiro **Script.usql**.

3. Cole o script anterior na janela **Script.usql**.

4. No canto superior esquerdo da janela **Script.usql**, especifique a conta do Data Lake Analytics.

    ![Submeter projeto do Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. No canto superior esquerdo da janela **Script.usql**, selecione **Submeter**.

6. Após a submissão da tarefa, o separador **Vista da tarefa** é aberto para mostrar o progresso da tarefa. Para ver o estado da tarefa mais recente e atualizar o ecrã, clique em **Atualizar**.

    ![Gráfico de desempenho da tarefa do Data Lake Analytics no Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * **Resumo da Tarefa** apresenta o resumo da tarefa.   
   * **Gráfico da Tarefa** apresenta o progresso da tarefa.
   * **Operações de Metadados** apresenta todas as ações que foram executadas no catálogo de U-SQL.
   * **Dados** apresenta todas as entradas e saídas.
   * O **Histórico de Estado** mostra os detalhes do estado e da linha do tempo.
   * A **Análise de AU** mostra quantas AUs foram utilizadas na tarefa e explora simulações de diferentes estratégias de alocação de AUs.
   * **Diagnóstico** oferece uma análise avançada para a otimização do desempenho e a execução da tarefa.

## <a name="check-job-status"></a>Verificar o estado da tarefa

1. No Server Explorer, selecione **Azure** > **Data Lake Analytics**.

2. Expanda o nome da conta do Data Lake Analytics.

3. Faça duplo clique em **Tarefas**.

4. Selecione a tarefa submetida anteriormente.

## <a name="see-the-job-output"></a>Ver o resultado da tarefa

1. No Server Explorer, navegue até à tarefa submetida.

2. Clique no separador **Dados**.

3. No separador **Saídas de Tarefas**, selecione o ficheiro `"/data.csv"`.

## <a name="next-steps"></a>Passos Seguintes

* [Run U-SQL scripts on your own workstation for testing and debugging](data-lake-analytics-data-lake-tools-local-run.md) (Executar scripts U-SQL na sua própria área de trabalho para testes e depuração)
* [Depurar o código C# em tarefas U-SQL com Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Utilizar as Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
