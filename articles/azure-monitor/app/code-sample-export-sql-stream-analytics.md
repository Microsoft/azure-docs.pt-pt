---
title: Exportar para o SQL do Azure Application Insights | Documentos da Microsoft
description: Exporte continuamente os dados do Application Insights para o SQL com o Stream Analytics.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48903032-2c99-4987-9948-d6e4559b4a63
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mbullwin
ms.openlocfilehash: 0166622539a439a99aef7e476519881518baa0b0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58106343"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Descrição Passo a Passo: Exportar para o SQL a partir do Application Insights com o Stream Analytics
Este artigo mostra como mover os dados de telemetria dos [do Azure Application Insights] [ start] numa base de dados SQL do Azure utilizando [exportação contínua] [ export] e [do Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

A exportação contínua move os dados de telemetria para o armazenamento do Azure no formato JSON. Vamos analisar os objetos JSON com o Azure Stream Analytics e criar linhas de uma tabela de base de dados.

(Geralmente, a exportação contínua é a maneira de fazer sua própria análise de telemetria as suas aplicações enviam para o Application Insights. Pode adaptar este exemplo de código para fazer outras coisas com a telemetria exportada, como a agregação de dados.)

Vamos começar com a suposição de que já tenha a aplicação que pretende monitorizar.

Neste exemplo, utilizaremos os dados de exibição de página, mas o mesmo padrão pode ser facilmente estendido para outros tipos de dados, tais como exceções e eventos personalizados. 

## <a name="add-application-insights-to-your-application"></a>Adicionar o Application Insights à sua aplicação
Para começar:

1. [Configurar o Application Insights para páginas da web](../../azure-monitor/app/javascript.md). 
   
    (Neste exemplo, nos concentraremos no processamento de dados de exibição de página a partir de browsers do cliente, mas também pode configurar o Application Insights para o lado do servidor da sua [Java](../../azure-monitor/app/java-get-started.md) ou [ASP.NET](../../azure-monitor/app/asp-net.md) pedido de aplicação e processo, dependência e outra telemetria do servidor.)
2. Publique a sua aplicação e veja os dados de telemetria que aparecem no seu recurso do Application Insights.

## <a name="create-storage-in-azure"></a>Criar armazenamento no Azure
A exportação contínua sempre produz dados para uma conta de armazenamento do Azure, por isso terá de criar primeiro o armazenamento.

1. Criar uma conta de armazenamento na sua subscrição no [portal do Azure][portal].
   
    ![No portal do Azure, escolha novo, dados, armazenamento. Selecione o Classic, escolha criar. Forneça um nome de armazenamento.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Criar um contentor
   
    ![No novo armazenamento, selecionar contentores, clique no mosaico de contentores e, em seguida, adicionar](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Copie a chave de acesso de armazenamento
   
    Precisará dela em breve para configurar a entrada para o serviço do stream analytics.
   
    ![No armazenamento, abra as definições, chaves e fazer uma cópia da chave de acesso primária](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Iniciar a exportação contínua ao armazenamento do Azure
1. No portal do Azure, navegue para o recurso do Application Insights que criou para a sua aplicação.
   
    ![Selecione procurar, Application Insights, seu aplicativo](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Crie uma exportação contínua.
   
    ![Escolha as definições, a exportação contínua, adicionar](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Selecione a conta de armazenamento que criou anteriormente:

    ![Definir o destino de exportação](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Defina os tipos de eventos que pretende ver:

    ![Escolha os tipos de eventos](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Permitir que alguns dados a acumularem. Relaxe e permitir que as pessoas utilizam a sua aplicação durante algum tempo. Telemetria chegarão e verá a gráficos de estatísticos [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md) e eventos individuais no [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md). 
   
    E, além disso, os dados vão exportar para o armazenamento. 
2. Inspecionar os dados exportados, no portal - escolher **navegue**, selecione a sua conta de armazenamento e, em seguida **contentores** - ou no Visual Studio. No Visual Studio, escolha **ver / na Cloud Explorer**e abra o Azure / armazenamento. (Se não tiver esta opção de menu, tem de instalar o SDK do Azure: Abra a caixa de diálogo novo projeto e abra o elemento Visual C# / na Cloud / obter o Microsoft Azure SDK para .NET.)
   
    ![No Visual Studio, abra o Browser do servidor, Azure, armazenamento](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Tome nota da parte do nome do caminho, o que é derivado da chave de instrumentação e o nome de aplicação comuns. 

Os eventos são escritos para o blob de arquivos no formato JSON. Cada ficheiro pode conter um ou mais eventos. Portanto, gostaríamos de ler os dados de eventos e filtrar os campos que queremos. Existem todos os tipos de coisas que podemos fazer com os dados, mas o nosso plano é hoje a utilizar o Stream Analytics para mover os dados para uma base de dados SQL. Que irá facilitar a execução de muitas consultas interessantes.

## <a name="create-an-azure-sql-database"></a>Criar uma base de dados SQL do Azure
Mais uma vez a partir da sua subscrição no [portal do Azure][portal], criar a base de dados (e um novo servidor, a menos que já tem um) para o qual irá escrever os dados.

![Novo, dados, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Certifique-se de que o servidor de base de dados permite o acesso aos serviços do Azure:

![Procurar, servidores, seu servidor, definições, Firewall, permitir o acesso ao Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Criar uma tabela na BD SQL do Azure
Ligar à base de dados criada na secção anterior com a sua ferramenta de gestão preferenciais. Nestas instruções, usaremos [ferramentas de gerenciamento do SQL Server](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Criar uma nova consulta e executar o T-SQL seguinte:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

Neste exemplo, estamos a utilizar dados de vistas de página. Para ver os dados disponíveis, inspecionar sua saída JSON e consulte a [exportar o modelo de dados](../../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Criar uma instância do Azure Stream Analytics
Partir do [portal do Azure](https://portal.azure.com/), selecione o serviço Azure Stream Analytics e criar uma nova tarefa de Stream Analytics:

![Definições do Stream analytics](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

Quando a nova tarefa é criada, selecione **Ir para recurso**.

![Definições do Stream analytics](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Adicionar uma nova entrada

![Definições do Stream analytics](./media/code-sample-export-sql-stream-analytics/SA004.png)

Defina-o para tomar a entrada do seu blob de exportação contínua:

![Definições do Stream analytics](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Agora terá a chave de acesso primária da conta de armazenamento, o que anotou anteriormente. Defina esta opção como a chave de conta de armazenamento.

#### <a name="set-path-prefix-pattern"></a>Padrão de prefixo do caminho de conjunto

**Não se esqueça de definir o formato de data como aaaa-MM-DD (com travessões).**

O padrão do prefixo do caminho Especifica como o Stream Analytics encontra os ficheiros de entrada no armazenamento. Tem de defini-lo para corresponder à forma como a exportação contínua armazena os dados. Defini-lo assim:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Neste exemplo:

* `webapplication27` é o nome do recurso do Application Insights, **tudo em minúsculas**. 
* `1234...` é a chave de instrumentação do recurso do Application Insights **com travessões removidos**. 
* `PageViews` é o tipo de dados que queremos analisar. Os tipos disponíveis dependem do filtro definido na exportação contínua. Examinar os dados exportados para ver os outros tipos disponíveis e veja a [exportar o modelo de dados](../../azure-monitor/app/export-data-model.md).
* `/{date}/{time}` um padrão é escrito literalmente.

Para obter o nome e a iKey do recurso do Application Insights, abra o Essentials na sua página de descrição geral ou abrir as definições.

> [!TIP]
> Use a função de exemplo para verificar que definiu o caminho de entrada corretamente. Se falhar: Verifique o que há dados no armazenamento para o intervalo de tempo de exemplo que escolheu. Editar a definição de entrada e verificar a definir a conta de armazenamento, o prefixo do caminho e formato de data corretamente.
> 
> 
> ## <a name="set-query"></a>Consulta de conjunto
> Abra a secção de consulta:

Substitua a consulta predefinida com:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Tenha em atenção que as primeiras propriedades são específicas para dados de exibição de página. Exportações de outros tipos de telemetria tem propriedades diferentes. Consulte o [detalhadas a referência de modelo de dados para os tipos de propriedade e valores.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Configurar a saída para a base de dados
Selecione o SQL como a saída.

![No stream analytics, selecione saídas](./media/code-sample-export-sql-stream-analytics/SA006.png)

Especifique a base de dados SQL.

![Preencha os detalhes da base de dados](./media/code-sample-export-sql-stream-analytics/SA007.png)

Fechar o assistente e aguardar que uma notificação a indicar que a saída foi configurada.

## <a name="start-processing"></a>Iniciar o processamento
Inicie a tarefa a partir da barra de ação:

![No stream analytics, clique em Iniciar](./media/code-sample-export-sql-stream-analytics/SA008.png)

Pode escolher se pretende começar a processar os dados a partir de agora, ou em dados anteriores para começar. A última opção é útil caso tenha tido a exportação contínua já em execução há algum tempo.

Após alguns minutos, volte a ferramentas de gestão do SQL Server e ver os fluxo de dados. Por exemplo, use uma consulta como esta:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Artigos relacionados
* [Exportar para o Power BI com o Stream Analytics](../../azure-monitor/app/export-power-bi.md )
* [Referência para os tipos de propriedade e os valores do modelo de dados detalhados.](../../azure-monitor/app/export-data-model.md)
* [Exportação contínua no Application Insights](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

