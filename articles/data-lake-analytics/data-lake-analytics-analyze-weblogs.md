---
title: Analisar registos de sites com o Azure Data Lake Analytics
description: Saiba como analisar registos de Web sites com o Data Lake Analytics.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 83742a4f82fb4d67fd258ff0d242847eab634c78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60334115"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Analisar registos de sites com o Azure Data Lake Analytics
Saiba como analisar registos de sites com o Data Lake Analytics, especialmente em descobrir que Referenciadores encontrou erros quando tentou visitar o Web site.

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2015 ou Visual Studio 2013**.
* **[Data Lake Tools para Visual Studio](https://aka.ms/adltoolsvs)**.

    Depois de instalar o Data Lake Tools para Visual Studio, verá uma **Data Lake** item no **ferramentas** menu no Visual Studio:

    ![Menu do Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Conhecimento básico do Data Lake Analytics e o Data Lake Tools para Visual Studio**. Para começar a utilizar, veja:

  * [Desenvolver scripts U-SQL com ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Uma conta do Data Lake Analytics.**  Ver [criar uma conta do Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
* **Instale os dados de exemplo.** No Portal do Azure, abra conta do Data Lake Analytics e clique em **Scripts de exemplo** no menu da esquerda, em seguida, clique em **copiar dados de exemplo**. 

## <a name="connect-to-azure"></a>Ligar ao Azure
Antes de poder criar e testar scripts U-SQL, primeiro tem de ligar para o Azure.

**Para ligar ao Data Lake Analytics**

1. Abra o Visual Studio.
2. Clique em **o Data Lake > Opções e definições**.
3. Clique em **iniciar sessão**, ou **alterar utilizador** se alguém iniciou sessão e siga as instruções.
4. Clique em **OK** para fechar a caixa de diálogo de opções e definições.

**Para procurar as suas contas do Data Lake Analytics**

1. A partir do Visual Studio, abra **Explorador de servidores** Press **CTRL + ALT + S**.
2. No **Explorador de Servidores**, expanda **Azure** e, em seguida, expanda **Data Lake Analytics**. Deve ver uma lista das suas contas do Data Lake Analytics, caso existam. Não é possível criar contas do Data Lake Analytics a partir do studio. Para criar uma conta, veja [Introdução ao Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md) ou [Introdução ao Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Desenvolver aplicações U-SQL
Um aplicativo de U-SQL é principalmente um script de U-SQL. Para saber mais sobre U-SQL, veja [começar com U-SQL](data-lake-analytics-u-sql-get-started.md).

Pode adicionar operadores definidos pelo utilizador de adição para o aplicativo.  Para obter mais informações, consulte [definido de utilizador de U-SQL desenvolver operadores para tarefas do Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Para criar e submeter uma tarefa do Data Lake Analytics**

1. Clique nas **ficheiro > novo > projeto**.
2. Selecione o tipo de projeto U-SQL.

    ![novo projeto Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Clique em **OK**. Visual studio cria uma solução com um ficheiro de script. usql.
4. Introduza o seguinte script para o ficheiro de script. usql:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            DISTRIBUTED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    Para compreender o U-SQL, veja [introdução à linguagem U-SQL do Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).    
5. Adicionar um novo script de U-SQL ao seu projeto e introduza o seguinte:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Alternar back para o primeiro script U-SQL e, junto aos **submeter** botão, especifique a conta do Analytics.
7. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Criar Script**. Verifique se os resultados no painel de saída.
8. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Submeter Script**.
9. Verifique se o **conta do Analytics** é aquele em que pretende executar a tarefa e, em seguida, clique em **submeter**. Os resultados da submissão e a ligação da tarefa ficam disponíveis na janela de Resultados das Ferramentas do Data Lake para Visual Studio quando a submissão estiver concluída.
10. Aguarde até que a tarefa é concluída com êxito.  Se a tarefa falhou, provavelmente está em falta o ficheiro de origem.  Consulte a secção pré-requisitos deste tutorial. Para informações adicionais de resolução de problemas, consulte [monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Quando a tarefa estiver concluída, deverá ver o ecrã seguinte:

    ![analisar os registos de Web sites de blogues do Data lake analytics](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Agora, repita os passos 7 a 10 para **Script1.usql**.

**Para ver o resultado da tarefa**

1. No **Explorador de Servidores**, expanda **Azure**, expanda **Data Lake Analytics**, expanda a sua conta do Data Lake Analytics, expanda **Contas de Armazenamento**, faça duplo clique na conta de Armazenamento do Data Lake predefinida e, em seguida, clique em **Explorador**.
2. Faça duplo clique em **amostras** para abrir a pasta e, em seguida, faça duplo clique em **saídas**.
3. Faça duplo clique em **UnsuccessfulResponses.log**.
4. Pode também clicar duas vezes no ficheiro de saída dentro da exibição de gráfico da tarefa para navegar diretamente para a saída.

## <a name="see-also"></a>Consulte também
Para iniciar o Data Lake Analytics com ferramentas diferentes, veja:

* [Introdução ao Data Lake Analytics com o Portal do Azure](data-lake-analytics-get-started-portal.md)
* [Introdução ao Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução ao Data Lake Analytics com o SDK do .NET](data-lake-analytics-get-started-net-sdk.md)
