---
title: Exportação para SQL a partir de Insights de Aplicação Azure [ Microsoft Docs
description: Exporte continuamente dados de Insights de Aplicação para ASQL utilizando o Stream Analytics.
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: e67365038b9a481bc0cacf079e5d197cc3139a5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536918"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Walkthrough: Export para SQL a partir de Application Insights usando Stream Analytics
Este artigo mostra como mover os seus dados de telemetria a partir de Insights de [Aplicação Azure][start] para uma base de dados Azure SQL utilizando a [Continuous Export][export] e [o Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

A exportação contínua move os seus dados de telemetria para o Armazenamento Azure em formato JSON. Vamos analisar os objetos JSON usando o Azure Stream Analytics e criar linhas numa tabela de bases de dados.

(De uma forma mais geral, a Exportação Contínua é a forma de fazer a sua própria análise da telemetria que as suas apps enviam para Application Insights. Pode adaptar esta amostra de código para fazer outras coisas com a telemetria exportada, como a agregação de dados.)

Começaremos com a suposição de que já tem a app que pretende monitorizar.

Neste exemplo, vamos usar os dados de visualização da página, mas o mesmo padrão pode facilmente ser estendido a outros tipos de dados, tais como eventos personalizados e exceções. 

## <a name="add-application-insights-to-your-application"></a>Adicione insights de aplicação à sua aplicação
Para começar:

1. [Configurar insights de aplicação para as suas páginas web](../../azure-monitor/app/javascript.md). 
   
    (Neste exemplo, vamos focar-nos no processamento de dados de visualização de páginas dos navegadores de clientes, mas também pode configurar Insights de Aplicação para o lado do servidor da sua [java](../../azure-monitor/app/java-get-started.md) ou [ASP.NET](../../azure-monitor/app/asp-net.md) app e pedido de processo, dependência e outra telemetria do servidor.)
2. Publique a sua aplicação e veja os dados de telemetria que aparecem no seu recurso Application Insights.

## <a name="create-storage-in-azure"></a>Criar armazenamento em Azure
A exportação contínua produz sempre dados para uma conta de Armazenamento Azure, pelo que é necessário criar primeiro o armazenamento.

1. Crie uma conta de armazenamento na sua subscrição no [portal Azure.][portal]
   
    ![No portal Azure, escolha New, Data, Storage. Selecione Classic, escolha Criar. Forneça um nome de armazenamento.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Criar um contentor
   
    ![No novo armazenamento, selecione Recipientes, clique no azulejo dos recipientes e, em seguida, adicione](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Copiar a chave de acesso ao armazenamento
   
    Em breve, precisará que seja configurado a entrada para o serviço de análise de fluxos.
   
    ![No armazenamento, abra Definições, Teclas e pegue uma cópia da Chave de Acesso Primário](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Inicie a exportação contínua para o armazenamento do Azure
1. No portal Azure, navegue para o recurso Application Insights que criou para a sua aplicação.
   
    ![Escolha Navegar, Informações de Aplicação, a sua aplicação](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Criar uma exportação contínua.
   
    ![Escolha configurações, exportação contínua, adicionar](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Selecione a conta de armazenamento que criou anteriormente:

    ![Definir o destino de exportação](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Detete os tipos de eventos que pretende ver:

    ![Escolha tipos de eventos](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Deixe acumular alguns dados. Senta-te e deixa que as pessoas usem a tua candidatura por uns tempos. A telemetria chegará e verá gráficos estatísticos em [exploradores métricos](../../azure-monitor/platform/metrics-charts.md) e eventos individuais em [pesquisa de diagnóstico.](../../azure-monitor/app/diagnostic-search.md) 
   
    E também, os dados serão exportados para o seu armazenamento. 
2. Inspecione os dados exportados, quer no portal - escolha **Navegar,** selecione a sua conta de armazenamento, e depois **contentores** - ou no Estúdio Visual. No Estúdio Visual, escolha **View / Cloud Explorer,** e abra O Azure/Armazenamento. (Se não tiver esta opção de menu, precisa de instalar o Azure SDK: Abra o diálogo do Novo Projeto e abra o Visual C# / Cloud / Obtenha o Microsoft Azure SDK para .NET.)
   
    ![No Estúdio Visual, abra o Navegador server, Azure, Armazenamento](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Tome nota da parte comum do nome do caminho, que é derivado do nome da aplicação e da chave de instrumentação. 

Os eventos são escritos para ficheiros blob em formato JSON. Cada ficheiro pode conter um ou mais eventos. Gostaríamos de ler os dados do evento e filtrar os campos que queremos. Há todo o tipo de coisas que podemos fazer com os dados, mas o nosso plano de hoje é usar o Stream Analytics para mover os dados para uma base de dados SQL. Isso vai facilitar a execução de muitas perguntas interessantes.

## <a name="create-an-azure-sql-database"></a>Criar uma Base de Dados SQL do Azure
Mais uma vez a partir da sua subscrição no [portal Azure, crie][portal]a base de dados (e um novo servidor, a menos que já tenha um) para o qual irá escrever os dados.

![Novo, Dados, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Certifique-se de que o servidor de base de dados permite o acesso aos serviços Do Mato:

![Navegue, Servidores, seu servidor, Definições, Firewall, Permitir acesso ao Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Crie uma tabela em Azure SQL DB
Ligue-se à base de dados criada na secção anterior com a sua ferramenta de gestão preferida. Neste passeio, estaremos a utilizar ferramentas de gestão de [servidores SQL](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Crie uma nova consulta e execute o seguinte T-SQL:

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

Nesta amostra, estamos a utilizar dados a partir de visualizações de páginas. Para ver os outros dados disponíveis, inspecione a sua saída JSON e consulte o modelo de dados de [exportação](../../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Criar uma instância azure stream analytics
A partir do [portal Azure,](https://portal.azure.com/)selecione o serviço Azure Stream Analytics e crie um novo trabalho stream analytics:

![Configurações de análise de fluxo](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

Quando o novo trabalho for criado, selecione **Ir para o recurso**.

![Configurações de análise de fluxo](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Adicione uma nova entrada

![Configurações de análise de fluxo](./media/code-sample-export-sql-stream-analytics/SA004.png)

Desloque-o para receber a entrada da sua bolha de Exportação Contínua:

![Configurações de análise de fluxo](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Agora vai precisar da chave de acesso primário da sua conta de armazenamento, que observou anteriormente. Delineie isto como a chave da conta de armazenamento.

#### <a name="set-path-prefix-pattern"></a>Definir padrão de prefixo de caminho

**Certifique-se de definir o formato de data para YYYY-MM-DD (com traços).**

O Padrão de Prefixo path especifica como o Stream Analytics encontra os ficheiros de entrada no armazenamento. É necessário defini-lo para corresponder à forma como a Exportação Contínua armazena os dados. Coloque assim:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Neste exemplo:

* `webapplication27`é o nome do recurso Application Insights, **tudo em caso inferior**. 
* `1234...`é a chave de instrumentação do recurso Application Insights **com traços removidos.** 
* `PageViews`é o tipo de dados que queremos analisar. Os tipos disponíveis dependem do filtro definido na Exportação Contínua. Examinar os dados exportados para ver os outros tipos disponíveis e ver o modelo de dados de [exportação.](../../azure-monitor/app/export-data-model.md)
* `/{date}/{time}`é um padrão escrito literalmente.

Para obter o nome e iKey do seu recurso Application Insights, abra o Essentials na sua página de visão geral ou abra as Definições.

> [!TIP]
> Utilize a função Amostra para verificar se definiu corretamente o caminho de entrada. Se falhar: Verifique se existem dados no armazenamento para o intervalo de tempo de amostra que escolheu. Editar a definição de entrada e verificar se definiu corretamente a conta de armazenamento, prefixo de caminho e formato de data.

 
## <a name="set-query"></a>Definir consulta
Abra a secção de consulta:

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

Note que as primeiras propriedades são específicas para ver dados de visualização de página. As exportações de outros tipos de telemetria terão propriedades diferentes. Consulte a referência detalhada do modelo de [dados para os tipos e valores de propriedade.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Configurar a saída para a base de dados
Selecione SQL como saída.

![Na análise de fluxo, selecione Saídas](./media/code-sample-export-sql-stream-analytics/SA006.png)

Especifique a base de dados SQL.

![Preencha os detalhes da sua base de dados](./media/code-sample-export-sql-stream-analytics/SA007.png)

Feche o assistente e aguarde uma notificação de que a saída foi configurada.

## <a name="start-processing"></a>Iniciar o processamento
Inicie o trabalho a partir da barra de ação:

![Na análise do stream, clique em Iniciar](./media/code-sample-export-sql-stream-analytics/SA008.png)

Pode escolher se deve começar a processar os dados a partir de agora, ou começar com dados anteriores. Este último é útil se já tiver exportação contínua em execução há algum tempo.

Após alguns minutos, volte às Ferramentas de Gestão de Servidores Da SQL e veja os dados a fluir. Por exemplo, use uma consulta como esta:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Artigos relacionados
* [Exportação para Power BI usando Stream Analytics](../../azure-monitor/app/export-power-bi.md )
* [Referência detalhada do modelo de dados para os tipos e valores de propriedade.](../../azure-monitor/app/export-data-model.md)
* [Exportação Contínua em Insights de Aplicação](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

