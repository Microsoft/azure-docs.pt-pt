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
ms.date: 08/30/2019
ms.openlocfilehash: 37fc469e8b7f6cd765a841409a7226346dd21a2d
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914222"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

As Ferramentas Azure Data Lake e Stream Analytics incluem a funcionalidade relacionada a dois serviços do Azure, Azure Data Lake Analytics e Azure Stream Analytics. Para obter mais informações sobre os cenários de Azure Stream Analytics, consulte [Azure Stream Analytics Tools for Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

Este artigo descreve como usar o Visual Studio para criar contas de Azure Data Lake Analytics. Você pode definir trabalhos no [U-SQL](data-lake-analytics-u-sql-get-started.md)e enviar trabalhos para o serviço de data Lake Analytics. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Recomendamos que você atualize para Ferramentas do Azure Data Lake para Visual Studio versão 2.3.3000.4 ou posterior. As versões anteriores já não estão disponíveis para transferência e foram preteridas.
>
> 1. Verifique se está a utilizar uma versão anterior à versão 2.3.3000.4 das Ferramentas do Azure Data Lake para o Visual Studio.
>
>    ![Verificar a versão da ferramenta](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Se a sua versão for anterior à versão 2.3.3000.4, atualize as Ferramentas do Azure Data Lake para o Visual Studio, visitando o centro de transferências:
>    - [Para o Visual Studio 2017 e 2019](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Para Visual Studio 2013 e 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio**: Todas as edições, exceto Express, têm suporte.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* Versão 2.7.1 ou superior do **Microsoft Azure SDK para .NET**. Instale-a através do [instalador de plataforma Web](https://www.microsoft.com/web/downloads/platform.aspx).
* A conta do **Data Lake Analytics**. Para criar uma conta, consulte [Introdução ao Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Instalar as Ferramentas do Azure Data Lake para Visual Studio

Este tutorial exige que as Ferramentas do Data Lake para Visual Studio estejam instaladas. Para obter mais informações, consulte [instalar o data Lake Tools para Visual Studio](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Ligar-se a uma conta do Azure Data Lake Analytics

1. Abra o Visual Studio.

1. Abra **Gerenciador de servidores** selecionando **Exibir** > **Gerenciador de servidores**.

1. Clique com o botão direito do mouse em **Azure**e selecione **conectar-se a Microsoft Azure assinatura**. Em **entrar em sua conta**, siga as instruções.

1. Em **Gerenciador de servidores**, selecione**Data Lake Analytics** **do Azure** > . Verá uma lista das suas contas do Data Lake Analytics.

## <a name="write-your-first-u-sql-script"></a>Escrever o seu primeiro script U-SQL

O texto apresentado em seguida é um script U-SQL simples. Este define um pequeno conjunto de dados e grava o conjunto de dados no Data Lake Store como um ficheiro com o nome `/data.csv`.

```sql
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

1. No Visual Studio, selecione **arquivo** > **novo** > **projeto**.

1. Selecione o tipo de **projeto U-SQL** e, em seguida, selecione **Avançar**. Em **configurar seu novo projeto**, selecione **criar**.

   O Visual Studio cria uma solução que contém um arquivo **script. usql** .

1. Cole o script de [escrever seu primeiro script U-SQL](#write-your-first-u-sql-script) na janela **script. usql** .

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse em **script. Usql**e selecione **Enviar script**.

1. Em **Enviar trabalho**, escolha sua conta de data Lake Analytics e selecione **Enviar**.

   ![Submeter projeto do Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

Após a submissão da tarefa, o separador **Vista da tarefa** é aberto para mostrar o progresso da tarefa.

* **Resumo da Tarefa** apresenta o resumo da tarefa.
* **Gráfico da Tarefa** apresenta o progresso da tarefa.
* **Operações de Metadados** apresenta todas as ações que foram executadas no catálogo de U-SQL.
* **Dados** apresenta todas as entradas e saídas.
* O **Histórico de Estado** mostra os detalhes do estado e da linha do tempo.
* A **análise de au** mostra quantas AUS foram usadas no trabalho e exploram simulações de diferentes estratégias de alocação de au.
* **Diagnóstico** oferece uma análise avançada para a otimização do desempenho e a execução da tarefa.

![Gráfico de desempenho da tarefa do Data Lake Analytics no Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

Para ver o status do trabalho mais recente e atualizar a tela, selecione **Atualizar**.

## <a name="check-job-status"></a>Verificar o estado da tarefa

1. Em **Gerenciador de servidores**, selecione**Data Lake Analytics** **do Azure** > .

1. Expanda o nome da conta do Data Lake Analytics.

1. Faça duplo clique em **Tarefas**.

1. Selecione a tarefa submetida anteriormente.

## <a name="see-the-job-output"></a>Ver o resultado da tarefa

1. Em **Gerenciador de servidores**, navegue até o trabalho que você enviou.

1. Clique no separador **Dados**.

1. No separador **Saídas de Tarefas**, selecione o ficheiro `"/data.csv"`.

## <a name="next-steps"></a>Passos Seguintes

* [Run U-SQL scripts on your own workstation for testing and debugging](data-lake-analytics-data-lake-tools-local-run.md) (Executar scripts U-SQL na sua própria área de trabalho para testes e depuração)
* [Depurar o código C# em tarefas U-SQL com Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Utilizar as Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
