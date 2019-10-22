---
title: Exportar para o SQL do Aplicativo Azure insights | Microsoft Docs
description: Exportar dados Application Insights continuamente para o SQL usando Stream Analytics.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/11/2017
ms.openlocfilehash: 41efcbc7b70395302858638a9f44f3cbba27bf9a
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678269"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Walkthrough: exportar para o SQL de Application Insights usando Stream Analytics
Este artigo mostra como mover os dados de telemetria de [aplicativo Azure informações][start] para um banco de dado SQL do Azure usando a exportação e a [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) [contínuas][export] . 

A exportação contínua move os dados de telemetria para o armazenamento do Azure no formato JSON. Analisaremos os objetos JSON usando Azure Stream Analytics e criaremos linhas em uma tabela de banco de dados.

(Em geral, a exportação contínua é a maneira de fazer sua própria análise da telemetria que seus aplicativos enviam para Application Insights. Você pode adaptar este exemplo de código para fazer outras coisas com a telemetria exportada, como a agregação de dados.)

Vamos começar com a suposição de que você já tenha o aplicativo que deseja monitorar.

Neste exemplo, usaremos os dados de exibição de página, mas o mesmo padrão pode ser facilmente estendido para outros tipos de dados, como eventos personalizados e exceções. 

## <a name="add-application-insights-to-your-application"></a>Adicionar Application Insights ao seu aplicativo
Para começar:

1. [Configure Application insights para suas páginas da Web](../../azure-monitor/app/javascript.md). 
   
    (Neste exemplo, vamos nos concentrar no processamento de dados de exibição de página dos navegadores cliente, mas você também pode configurar Application Insights para o lado do servidor de seu aplicativo [Java](../../azure-monitor/app/java-get-started.md) ou [ASP.net](../../azure-monitor/app/asp-net.md) e solicitações de processo, dependência e outras telemetrias de servidor.)
2. Publique seu aplicativo e Assista aos dados de telemetria que aparecem no recurso Application Insights.

## <a name="create-storage-in-azure"></a>Criar armazenamento no Azure
A exportação contínua sempre gera dados para uma conta de armazenamento do Azure, portanto, você precisa criar o armazenamento primeiro.

1. Crie uma conta de armazenamento em sua assinatura no [portal do Azure][portal].
   
    ![Em portal do Azure, escolha novo, dados, armazenamento. Selecione clássico, escolha criar. Forneça um nome de armazenamento.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Criar um contentor
   
    ![No novo armazenamento, selecione contêineres, clique no bloco contêineres e, em seguida, adicione](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Copiar a chave de acesso de armazenamento
   
    Você precisará dela em breve para configurar a entrada para o serviço do Stream Analytics.
   
    ![No armazenamento, abra configurações, chaves e faça uma cópia da chave de acesso primária](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Iniciar exportação contínua para o armazenamento do Azure
1. Na portal do Azure, navegue até o recurso de Application Insights que você criou para seu aplicativo.
   
    ![Escolha procurar, Application Insights, seu aplicativo](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Crie uma exportação contínua.
   
    ![Escolha configurações, exportação contínua, adicionar](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Selecione a conta de armazenamento que você criou anteriormente:

    ![Definir o destino de exportação](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Defina os tipos de eventos que você deseja ver:

    ![Escolher tipos de evento](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Permitir que alguns dados sejam acumulados. Retorne e deixe que as pessoas usem seu aplicativo por algum tempo. A telemetria entrará e você verá gráficos estatísticos no [Gerenciador de métricas](../../azure-monitor/app/metrics-explorer.md) e eventos individuais na [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md). 
   
    Além disso, os dados serão exportados para o armazenamento. 
2. Inspecione os dados exportados, no portal-escolha **procurar**, selecione sua conta de armazenamento e, em seguida, **contêineres** -ou no Visual Studio. No Visual Studio, escolha **Exibir/Cloud Explorer**e abra Azure/armazenamento. (Se você não tiver essa opção de menu, precisará instalar o SDK do Azure: Abra a caixa de diálogo novo projeto C# e abra Visual/Cloud/obter Microsoft Azure SDK para .net.)
   
    ![No Visual Studio, abra o navegador do servidor, Azure, armazenamento](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Anote a parte comum do nome do caminho, que é derivada do nome do aplicativo e da chave de instrumentação. 

Os eventos são gravados em arquivos de blob no formato JSON. Cada arquivo pode conter um ou mais eventos. Portanto, gostaríamos de ler os dados do evento e filtrar os campos que desejamos. Há todos os tipos de coisas que poderíamos fazer com os dados, mas nosso plano hoje é usar Stream Analytics para mover os dados para um banco de dado SQL. Isso facilitará a execução de muitas consultas interessantes.

## <a name="create-an-azure-sql-database"></a>Criar um banco de dados SQL do Azure
Mais uma vez, a partir de sua assinatura no [portal do Azure][portal], crie o banco de dados (e um novo servidor, a menos que você já tenha um) para o qual você escreverá os mesmos.

![Novo, dados, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Verifique se o servidor de banco de dados permite acesso aos serviços do Azure:

![Procurar, servidores, seu servidor, configurações, firewall, permitir acesso ao Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Criar uma tabela no banco de BD SQL do Azure
Conecte-se ao banco de dados criado na seção anterior com sua ferramenta de gerenciamento preferida. Neste tutorial, usaremos [ferramentas de gerenciamento do SQL Server](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Crie uma nova consulta e execute o T-SQL a seguir:

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

Neste exemplo, estamos usando dados de exibições de página. Para ver os outros dados disponíveis, inspecione a saída JSON e veja o [modelo de dados de exportação](../../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Criar uma instância de Azure Stream Analytics
No [portal do Azure](https://portal.azure.com/), selecione o serviço Azure Stream Analytics e crie um novo trabalho de Stream Analytics:

![Configurações do Stream Analytics](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

Quando o novo trabalho for criado, selecione **ir para o recurso**.

![Configurações do Stream Analytics](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Adicionar uma nova entrada

![Configurações do Stream Analytics](./media/code-sample-export-sql-stream-analytics/SA004.png)

Defina-a para obter entrada do seu blob de exportação contínua:

![Configurações do Stream Analytics](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Agora você precisará da chave de acesso primária da sua conta de armazenamento, que você anotou anteriormente. Defina como a chave da conta de armazenamento.

#### <a name="set-path-prefix-pattern"></a>Definir padrão de prefixo de caminho

**Certifique-se de definir o formato de data como aaaa-MM-DD (com traços).**

O padrão de prefixo de caminho Especifica como Stream Analytics localiza os arquivos de entrada no armazenamento. Você precisa defini-lo para corresponder à forma como a exportação contínua armazena os dados. Defina-o da seguinte forma:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Neste exemplo:

* `webapplication27` é o nome do recurso de Application Insights, **tudo em letras**minúsculas. 
* `1234...` é a chave de instrumentação do recurso de Application Insights **com traços removidos**. 
* `PageViews` é o tipo de dados que queremos analisar. Os tipos disponíveis dependem do filtro que você definiu na exportação contínua. Examine os dados exportados para ver os outros tipos disponíveis e veja o [modelo de dados de exportação](../../azure-monitor/app/export-data-model.md).
* `/{date}/{time}` é um padrão escrito literalmente.

Para obter o nome e iKey de seu recurso de Application Insights, abra o Essentials em sua página de visão geral ou abra configurações.

> [!TIP]
> Use a função de exemplo para verificar se você definiu o caminho de entrada corretamente. Se falhar: Verifique se há dados no armazenamento para o intervalo de tempo de exemplo escolhido. Edite a definição de entrada e verifique se você definiu a conta de armazenamento, o prefixo do caminho e o formato de data corretamente.

 
## <a name="set-query"></a>Definir consulta
Abra a seção de consulta:

Substitua a consulta padrão por:

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

Observe que as primeiras propriedades são específicas para dados de exibição de página. As exportações de outros tipos de telemetria terão propriedades diferentes. Consulte a [referência detalhada do modelo de dados para os tipos de propriedade e valores.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Configurar saída para o banco de dados
Selecione SQL como a saída.

![No Stream Analytics, selecione saídas](./media/code-sample-export-sql-stream-analytics/SA006.png)

Especifique o banco de dados SQL.

![Preencha os detalhes do seu banco de dados](./media/code-sample-export-sql-stream-analytics/SA007.png)

Feche o assistente e aguarde uma notificação informando que a saída foi configurada.

## <a name="start-processing"></a>Iniciar processamento
Inicie o trabalho na barra de ação:

![No Stream Analytics, clique em iniciar](./media/code-sample-export-sql-stream-analytics/SA008.png)

Você pode escolher se deseja começar a processar os dados a partir de agora ou iniciar com os dados anteriores. O último será útil se você já tiver a exportação contínua em execução por um tempo.

Após alguns minutos, volte para Ferramentas de Gerenciamento do SQL Server e veja os dados que fluem. Por exemplo, use uma consulta como esta:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Artigos relacionados
* [Exportar para o PowerBI usando o Stream Analytics](../../azure-monitor/app/export-power-bi.md )
* [Referência de modelo de dados detalhado para os tipos de propriedade e valores.](../../azure-monitor/app/export-data-model.md)
* [Exportação contínua no Application Insights](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

