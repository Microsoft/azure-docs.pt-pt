---
title: Exportação para SQL a partir de Azure Application Insights / Microsoft Docs
description: Exportar continuamente dados de Insights de Aplicação para SQL usando Stream Analytics.
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 90aab1794a9b412de2498edcc4d221f4bcc86968
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90979445"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Walkthrough: Exportação para SQL a partir de Insights de Aplicação usando Stream Analytics
Este artigo mostra como mover os seus dados de telemetria da [Azure Application Insights][start] para a Base de Dados Azure SQL utilizando [a Continuação exportação][export] e [o Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

A exportação contínua move os seus dados de telemetria para o Azure Storage no formato JSON. Analisaremos os objetos JSON usando a Azure Stream Analytics e criaremos linhas numa tabela de bases de dados.

(De uma forma mais geral, a Exportação Contínua é a forma de fazer a sua própria análise da telemetria que as suas apps enviam para o Application Insights. Pode adaptar esta amostra de código para fazer outras coisas com a telemetria exportada, como a agregação de dados.)

Começaremos com o pressuposto de que já tem a app que pretende monitorizar.

Neste exemplo, vamos usar os dados de visualização da página, mas o mesmo padrão pode ser facilmente estendido a outros tipos de dados, como eventos personalizados e exceções. 

## <a name="add-application-insights-to-your-application"></a>Adicionar Insights de Aplicação à sua aplicação
Para começar:

1. [Configurar Insights de Aplicação para as suas páginas web.](./javascript.md) 
   
    (Neste exemplo, vamos focar-nos no processamento de dados de visualização de página a partir dos navegadores do cliente, mas também pode configurar o Application Insights para o lado do servidor do seu [Java](./java-get-started.md) ou [ASP.NET](./asp-net.md) app e pedido de processo, dependência e outra telemetria do servidor.)
2. Publique a sua aplicação e assista a dados de telemetria que aparecem no seu recurso Application Insights.

## <a name="create-storage-in-azure"></a>Criar armazenamento em Azure
A exportação contínua produz sempre dados para uma conta de Armazenamento Azure, por isso é necessário criar o armazenamento primeiro.

1. Crie uma conta de armazenamento na sua subscrição no [portal Azure][portal].
   
    ![No portal Azure, escolha Novos, Dados, Armazenamento. Selecione Classic, escolha Criar. Forneça um nome de armazenamento.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Criar um contentor
   
    ![No novo armazenamento, selecione Recipientes, clique no azulejo de Recipientes e, em seguida, Adicione](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Copie a chave de acesso ao armazenamento
   
    Em breve, vai precisar dele para configurar a entrada para o serviço de análise de fluxo.
   
    ![No armazenamento, abra as definições, chaves e pegue uma cópia da chave de acesso primário](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Inicie a exportação contínua para o armazenamento da Azure
1. No portal Azure, consulte o recurso Application Insights que criou para a sua aplicação.
   
    ![Escolha Procurar, Insights de Aplicação, a sua aplicação](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Criar uma exportação contínua.
   
    ![Escolha Configurações, Exportação Contínua, Adicionar](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Selecione a conta de armazenamento que criou anteriormente:

    ![Definir o destino de exportação](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Desação os tipos de eventos que pretende ver:

    ![Escolha tipos de eventos](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Deixe alguns dados acumularem-se. Sente-se e deixe as pessoas usarem a sua aplicação por um tempo. A telemetria chegará e verá gráficos estatísticos em [exploradores métricos](../platform/metrics-charts.md) e eventos individuais em [pesquisa de diagnóstico.](./diagnostic-search.md) 
   
    Além disso, os dados serão exportadas para o seu armazenamento. 
2. Inspecione os dados exportados, seja no portal - escolha **procurar, selecione**a sua conta de armazenamento e, em seguida, **Contentores** - ou no Estúdio Visual. No Estúdio Visual, escolha **View / Cloud Explorer,** e abra O Azure / Storage. (Se não tiver esta opção de menu, tem de instalar o Azure SDK: Abra o diálogo do Novo Projeto e abra o Visual C# / Cloud / Obtenha o Microsoft Azure SDK para .NET.)
   
    ![No Estúdio Visual, abra o Browser do Servidor, Azure, Armazenamento](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Tome nota da parte comum do nome do caminho, que é derivado do nome da aplicação e da chave de instrumentação. 

Os eventos são escritos para ficheiros blob no formato JSON. Cada ficheiro pode conter um ou mais eventos. Então gostaríamos de ler os dados do evento e filtrar os campos que queremos. Há todo o tipo de coisas que poderíamos fazer com os dados, mas o nosso plano hoje é usar o Stream Analytics para mover os dados para a Base de Dados SQL. Isso vai facilitar a execução de muitas consultas interessantes.

## <a name="create-an-azure-sql-database"></a>Criar uma Base de Dados SQL do Azure
Mais uma vez a partir da sua subscrição no [portal Azure,][portal]crie a base de dados (e um novo servidor, a não ser que já tenha um) ao qual irá escrever os dados.

![Novo, Dados, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Certifique-se de que o servidor permite o acesso aos serviços Azure:

![Procurar, Servidores, o seu servidor, Definições, Firewall, Permitir acesso ao Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-database"></a>Criar uma tabela na Base de Dados Azure SQL
Ligue à base de dados criada na secção anterior com a sua ferramenta de gestão preferida. Nesta passagem, estaremos a utilizar [ferramentas de gestão de servidores SQL](/sql/ssms/sql-server-management-studio-ssms?view=sql-server-ver15) (SSMS).

![Ligar à Base de Dados SQL do Azure](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

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

![Criar PageViewsTable](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

Nesta amostra, estamos a usar dados a partir de visualizações de página. Para ver os outros dados disponíveis, inspecione a sua saída JSON e consulte o modelo de dados de [exportação.](./export-data-model.md)

## <a name="create-an-azure-stream-analytics-instance"></a>Criar um exemplo de Azure Stream Analytics
A partir do [portal Azure,](https://portal.azure.com/)selecione o serviço Azure Stream Analytics e crie um novo trabalho stream Analytics:

![A screenshot mostra a página de trabalho de análise do Stream com o botão Criar realçado.](./media/code-sample-export-sql-stream-analytics/SA001.png)

![Novo trabalho de análise de fluxo](./media/code-sample-export-sql-stream-analytics/SA002.png)

Quando o novo trabalho for criado, selecione **Ir para o recurso**.

![A screenshot mostra a mensagem de implementação e vai para o botão de recursos.](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Adicione uma nova entrada

![A screenshot mostra a página Entradas com o botão Adicionar selecionado.](./media/code-sample-export-sql-stream-analytics/SA004.png)

Desacalça-o para obter a entrada da sua bolha de exportação contínua:

![O Screenshot mostra a nova janela de entrada com as opções de menu de entrada, fonte e armazenamento selecionadas.](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Agora vai precisar da chave de acesso primário da sua conta de armazenamento, que observou anteriormente. Desa esta medida como a chave de conta de armazenamento.

#### <a name="set-path-prefix-pattern"></a>Definir padrão de prefixo de caminho

**Certifique-se de que define o formato de data para YYYY-MM-DD (com traços).**

O Padrão do Prefixo do Caminho especifica como o Stream Analytics encontra os ficheiros de entrada no armazenamento. É necessário defini-lo para corresponder à forma como a Exportação Contínua armazena os dados. Definir assim:

```sql
webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}
```

Neste exemplo:

* `webapplication27`é o nome do recurso Application Insights, **tudo em minúsculas.** 
* `1234...` é a chave de instrumentação do recurso Application Insights **com traços removidos**. 
* `PageViews` é o tipo de dados que queremos analisar. Os tipos disponíveis dependem do filtro que definiu na Exportação Contínua. Examinar os dados exportados para ver os outros tipos disponíveis e ver o modelo de dados de [exportação.](./export-data-model.md)
* `/{date}/{time}` é um padrão escrito literalmente.

Para obter o nome e iKey do seu recurso Application Insights, abra o Essencial na sua página geral ou abra Definições.

> [!TIP]
> Utilize a função Amostra para verificar se definiu corretamente o caminho de entrada. Se falhar: Verifique se existem dados no armazenamento para o intervalo de tempo de amostra que escolheu. Edite a definição de entrada e verifique se define corretamente a conta de armazenamento, o prefixo do caminho e o formato da data.

 
## <a name="set-query"></a>Definir consulta
Abra a secção de consulta:

Substitua a consulta por defeito por:

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

Note que as primeiras propriedades são específicas para visualizar dados de página. As exportações de outros tipos de telemetria terão propriedades diferentes. Consulte a referência detalhada do [modelo de dados para os tipos e valores da propriedade.](./export-data-model.md)

## <a name="set-up-output-to-database"></a>Configurar saída para base de dados
Selecione SQL como saída.

![Na análise de streaming, selecione Outputs](./media/code-sample-export-sql-stream-analytics/SA006.png)

Especifique a base de dados.

![Preencha os detalhes da sua base de dados](./media/code-sample-export-sql-stream-analytics/SA007.png)

Feche o assistente e aguarde uma notificação de que a saída foi configurada.

## <a name="start-processing"></a>Começar a processar
Comece o trabalho a partir da barra de ação:

![Na análise de streaming, clique em Iniciar](./media/code-sample-export-sql-stream-analytics/SA008.png)

Pode escolher se deve começar a processar os dados a partir de agora ou começar com dados anteriores. Este último é útil se já tiver exportação contínua já em execução há algum tempo.

Após alguns minutos, volte às Ferramentas de Gestão do Servidor SQL e veja os dados a fluir. Por exemplo, use uma consulta como esta:

```sql
SELECT TOP 100 *
FROM [dbo].[PageViewsTable]
```

## <a name="related-articles"></a>Artigos relacionados
* [Exportação para Power BI usando Stream Analytics](./export-power-bi.md)
* [referência detalhada do modelo de dados para os tipos e valores de propriedade.](./export-data-model.md)
* [Exportação Contínua em Insights de Aplicação](./export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[export]: ./export-telemetry.md
[metrics]: ../platform/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md

