---
title: Analisar registos de sites com o Azure Data Lake Analytics
description: Saiba como analisar os registos do site usando o Azure Data Lake Analytics para executar funções e consultas U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: d2764e5e3994eb7b80a8e7cf62fb7f437323c97e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015303"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Analisar registos de sites com o Azure Data Lake Analytics
Saiba como analisar os registos do site usando data lake analytics, especialmente ao descobrir quais os remetentes que se depararam com erros quando tentaram visitar o site.

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2015 ou Visual Studio 2013**.
* **[Ferramentas do Data Lake para Visual Studio](https://aka.ms/adltoolsvs)**.

    Assim que as Ferramentas do Lago de Dados para o Estúdio Visual estiverem instaladas, verá um item do **Data Lake** no menu **Ferramentas** no Estúdio Visual:

    ![Menu U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Conhecimento básico de Data Lake Analytics e das Ferramentas do Lago de Dados para o Estúdio Visual.** Para começar, consulte:

  * [Desenvolver script U-SQL utilizando ferramentas data lake para estúdio visual](data-lake-analytics-data-lake-tools-get-started.md).
* **Uma conta data lake analytics.**  Consulte [criar uma conta Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
* **Instale os dados da amostra.** No Portal Azure, abra-lhe a conta Data Lake Analytics e clique em **Scripts de Amostra** no menu esquerdo e, em seguida, clique em Copiar **Dados de Amostra**. 

## <a name="connect-to-azure"></a>Ligar ao Azure
Antes de poder construir e testar quaisquer scripts U-SQL, tem primeiro de ligar-se ao Azure.

### <a name="to-connect-to-data-lake-analytics"></a>Para ligar ao Data Lake Analytics

1. Abra o Visual Studio.
2. Clique em **Opções e Configurações > do Lago de Dados**.
3. Clique **em Iniciar sção** ou **Altere o Utilizador** se alguém tiver assinado e siga as instruções.
4. Clique **em OK** para fechar o diálogo opções e definições.

### <a name="to-browse-your-data-lake-analytics-accounts"></a>Para navegar nas suas contas Data Lake Analytics

1. A partir do Estúdio Visual, abra **o Server Explorer** premindo **CTRL+ALT+S**.
2. No **Explorador de Servidores**, expanda **Azure** e, em seguida, expanda **Data Lake Analytics**. Deve ver uma lista das suas contas do Data Lake Analytics, caso existam. Não é possível criar contas data lake analytics a partir do estúdio. Para criar uma conta, veja [Introdução ao Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md) ou [Introdução ao Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Desenvolver aplicação U-SQL
Uma aplicação U-SQL é principalmente um script U-SQL. Para saber mais sobre o U-SQL, consulte [Começar com o U-SQL.](data-lake-analytics-u-sql-get-started.md)

Pode adicionar adição de operadores definidos pelo utilizador à aplicação.  Para obter mais informações, consulte [os operadores definidos pelo utilizador U-SQL para trabalhos](data-lake-analytics-u-sql-develop-user-defined-operators.md)de Data Lake Analytics .

### <a name="to-create-and-submit-a-data-lake-analytics-job"></a>Para criar e submeter tarefas de Data Lake Analytics

1. Clique no **Projeto > Nova >.**

2. Selecione o tipo Projeto U-SQL .

   ![novo projeto Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Clique em **OK**. O Visual Studio cria uma solução com um ficheiro Script.usql.

4. Introduza o seguinte script no ficheiro Script.usql:

   ```usql
   // Create a database for easy reuse, so you don't need to read from a file very time.
   CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

   // Create a Table valued function. TVF ensures that your jobs fetch data from he weblog file with the correct schema.
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
   ```

    Para entender o U-SQL, consulte [Começar com a linguagem U-SQL do Data Lake Analytics.](data-lake-analytics-u-sql-get-started.md)

5. Adicione um novo script U-SQL ao seu projeto e insira o seguinte:

   ```usql
   // Query the referrers that ran into errors
   @content =
       SELECT *
       FROM SampleDBTutorials.dbo.ReferrersPerDay
       WHERE sc_status >=400 AND sc_status < 500;

   OUTPUT @content
   TO @"/Samples/Outputs/UnsuccessfulResponses.log"
   USING Outputters.Tsv();
   ```

6. Volte para o primeiro script U-SQL e ao lado do botão **Enviar,** especifique a sua conta Analytics.

7. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Criar Script**. Verifique os resultados no painel de saída.

8. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Submeter Script**.

9. Verifique se a **Conta Analytics** é a que pretende gerir o trabalho e, em seguida, clique em **Enviar**. Os resultados da submissão e a ligação da tarefa ficam disponíveis na janela de Resultados das Ferramentas do Data Lake para Visual Studio quando a submissão estiver concluída.

10. Aguarde até que o trabalho esteja concluído com sucesso.  Se o trabalho falhou, é provável que esteja a perder o ficheiro de origem.  Consulte a secção Pré-Requisito deste tutorial. Para obter informações adicionais sobre resolução de problemas, consulte [monitor e resolução de problemas Azure Data Lake Analytics .](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

    Quando o trabalho estiver concluído, verá o seguinte ecrã:

    ![análise de lago de dados analisar registos de websites de weblogs](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. Repita agora os passos 7-10 para **script1.usql**.

### <a name="to-see-the-job-output"></a>Para ver o resultado da tarefa

1. No **Explorador de Servidores**, expanda **Azure**, expanda **Data Lake Analytics**, expanda a sua conta do Data Lake Analytics, expanda **Contas de Armazenamento**, faça duplo clique na conta de Armazenamento do Data Lake predefinida e, em seguida, clique em **Explorador**.
2. Clique **duas vezes** em amostras para abrir a pasta e, em seguida, clique **duas vezes** nas saídas .
3. Clique **duas vezes nas respostas sem sucesso.log**. .
4. Também pode clicar duas vezes no ficheiro de saída dentro da vista de gráfico da função para navegar diretamente para a saída.

## <a name="next-steps"></a>Passos seguintes
Para iniciar o Data Lake Analytics com ferramentas diferentes, veja:

* [Começar com data lake analytics usando o Portal Azure](data-lake-analytics-get-started-portal.md)
* [Introdução ao Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução ao Data Lake Analytics com o SDK .NET](./data-lake-analytics-get-started-cli.md)