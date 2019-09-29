---
title: Analisar registos de sites com o Azure Data Lake Analytics
description: Saiba como analisar os logs do site usando Azure Data Lake Analytics para executar funções e consultas do U-SQL.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 04c6d4c74a82ccfbcbb0faecb0dca5ec495f6663
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672884"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Analisar registos de sites com o Azure Data Lake Analytics
Saiba como analisar os logs do site usando Data Lake Analytics, especialmente sobre como descobrir quais referenciadores tiveram erros ao tentar visitar o site.

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2015 ou Visual Studio 2013**.
* **[Data Lake Tools para Visual Studio](https://aka.ms/adltoolsvs)** .

    Quando as ferramentas do Data Lake para o Visual Studio estiverem instaladas, você verá um item de **Data Lake** no menu **ferramentas** no Visual Studio:

    ![Menu do Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Conhecimento básico de data Lake Analytics e as ferramentas de data Lake para o Visual Studio**. Para começar, consulte:

  * [Desenvolva o script U-SQL usando as ferramentas de data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Uma conta de Data Lake Analytics.**  Consulte [criar uma conta de Azure data Lake Analytics](data-lake-analytics-get-started-portal.md).
* **Instale os dados de exemplo.** No portal do Azure, abra você Data Lake Analytics conta e clique em **scripts de exemplo** no menu à esquerda e, em seguida, clique em **copiar dados de exemplo**. 

## <a name="connect-to-azure"></a>Ligar ao Azure
Antes de criar e testar scripts U-SQL, primeiro você deve se conectar ao Azure.

**Para ligar ao Data Lake Analytics**

1. Abra o Visual Studio.
2. Clique em **Data Lake > opções e configurações**.
3. Clique em **entrar**ou **Alterar usuário** se alguém tiver entrado e siga as instruções.
4. Clique em **OK** para fechar a caixa de diálogo opções e configurações.

**Para procurar suas contas de Data Lake Analytics**

1. No Visual Studio, abra **Gerenciador de servidores** pressionando **Ctrl + Alt + S**.
2. No **Explorador de Servidores**, expanda **Azure** e, em seguida, expanda **Data Lake Analytics**. Deve ver uma lista das suas contas do Data Lake Analytics, caso existam. Você não pode criar contas de Data Lake Analytics no estúdio. Para criar uma conta, veja [Introdução ao Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md) ou [Introdução ao Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Desenvolver aplicativo U-SQL
Um aplicativo U-SQL é basicamente um script U-SQL. Para saber mais sobre o U-SQL, consulte Introdução [ao u-SQL](data-lake-analytics-u-sql-get-started.md).

Você pode adicionar operadores definidos pelo usuário adicionais ao aplicativo.  Para obter mais informações, consulte [desenvolver operadores do U-SQL definidos pelo usuário para trabalhos de data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Para criar e submeter uma tarefa do Data Lake Analytics**

1. Clique no **arquivo > novo projeto de >** .
2. Selecione o tipo de projeto U-SQL.

    ![novo projeto Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Clique em **OK**. O Visual Studio cria uma solução com um arquivo script. usql.
4. Insira o script a seguir no arquivo script. usql:

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

    Para entender o U-SQL, consulte Introdução [à linguagem u-sql data Lake Analytics](data-lake-analytics-u-sql-get-started.md).    
5. Adicione um novo script U-SQL ao seu projeto e insira o seguinte:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Volte para o primeiro script U-SQL e, ao lado do botão **Enviar** , especifique sua conta de análise.
7. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Criar Script**. Verifique os resultados no painel de saída.
8. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Submeter Script**.
9. Verifique se a **conta de análise** é aquela em que você deseja executar o trabalho e clique em **Enviar**. Os resultados da submissão e a ligação da tarefa ficam disponíveis na janela de Resultados das Ferramentas do Data Lake para Visual Studio quando a submissão estiver concluída.
10. Aguarde até que o trabalho seja concluído com êxito.  Se o trabalho falhou, provavelmente falta o arquivo de origem.  Consulte a seção pré-requisito deste tutorial. Para obter informações adicionais de solução de problemas, consulte [monitorar e solucionar problemas de trabalhos de Azure data Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Quando o trabalho for concluído, você deverá ver a tela a seguir:

    ![logs do site analisar weblogs do data Lake Analytics](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Agora, repita as etapas 7-10 para **Script1. usql**.

**Para ver o resultado da tarefa**

1. No **Explorador de Servidores**, expanda **Azure**, expanda **Data Lake Analytics**, expanda a sua conta do Data Lake Analytics, expanda **Contas de Armazenamento**, faça duplo clique na conta de Armazenamento do Data Lake predefinida e, em seguida, clique em **Explorador**.
2. Clique duas vezes em **exemplos** para abrir a pasta e clique duas vezes em **saídas**.
3. Clique duas vezes em **UnsuccessfulResponses. log**.
4. Você também pode clicar duas vezes no arquivo de saída dentro do modo de exibição de gráfico do trabalho para navegar diretamente até a saída.

## <a name="see-also"></a>Consulte também
Para iniciar o Data Lake Analytics com ferramentas diferentes, veja:

* [Introdução ao Data Lake Analytics com o Portal do Azure](data-lake-analytics-get-started-portal.md)
* [Introdução ao Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução ao Data Lake Analytics com o SDK do .NET](data-lake-analytics-get-started-net-sdk.md)
