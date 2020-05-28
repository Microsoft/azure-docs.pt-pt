---
title: Executar consultas de análise contra bases de dados de inquilinos
description: Consultas de análise de inquilinos cruzados utilizando dados extraídos da Base de Dados Azure SQL, Azure Synapse Analytics, Azure Data Factory ou Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: fff308f241a29cbf40bf2884fc412acf5942497b
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048806"
---
# <a name="explore-saas-analytics-with-azure-sql-database-azure-synapse-analytics-data-factory-and-power-bi"></a>Explore a análise saaS com base de dados Azure SQL, Azure Synapse Analytics, Data Factory e Power BI
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você caminha por um cenário de análise de ponta a ponta. O cenário demonstra como a análise sobre os dados dos inquilinos pode capacitar os fornecedores de software a tomar decisões inteligentes. Utilizando dados extraídos de cada base de dados de inquilinos, utiliza aanálise para obter informações sobre o comportamento dos inquilinos, incluindo o uso da aplicação Wingtip Tickets SaaS. Este cenário envolve três etapas:

1. **Extrair dados** de cada base de dados de inquilinos numa loja de análise, neste caso, um Armazém de Dados SQL.
2. **Otimize os dados extraídos** para o processamento de análises.
3. Utilize ferramentas de **Inteligência Empresarial** para extrair insights úteis, que podem orientar a tomada de decisão.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Crie a loja de analítica para o carregamento.
> - Utilize a Azure Data Factory (ADF) para extrair dados de cada base de dados de inquilinos no armazém de dados de análise.
> - Otimize os dados extraídos (reorganize-se num esquema estelar).
> - Consultar o armazém de dados analíticos.
> - Use o Power BI para visualização de dados para destacar tendências nos dados dos inquilinos e fazer recomendação para melhorias.

![arquiteturaOverView](./media/saas-tenancy-tenant-analytics-adf/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Análise sobre dados extraídos do inquilino

As aplicações SaaS detêm uma quantidade potencialmente grande de dados de inquilinos na nuvem. Estes dados podem fornecer uma rica fonte de insights sobre o funcionamento e uso da sua aplicação, e o comportamento dos seus inquilinos. Estes insights podem orientar o desenvolvimento de funcionalidades, melhorias de usabilidade e outros investimentos nas apps e plataforma.

O acesso aos dados de todos os inquilinos é simples quando todos os dados estão numa base de dados multi-inquilinos. Mas o acesso é mais complexo quando distribuído à escala em milhares de bases de dados. Uma forma de domar a complexidade é extrair os dados para uma base de dados de análise ou um armazém de dados para consulta.

Este tutorial apresenta um cenário de análise de ponta a ponta para a aplicação Wingtip Tickets. Em primeiro lugar, a [Azure Data Factory (ADF)](../../data-factory/introduction.md) é usada como ferramenta de orquestração para extrair vendas de bilhetes e dados relacionados de cada base de dados de inquilinos. Estes dados são carregados em mesas de preparação numa loja de análise. A loja de análise pode ser uma Base de Dados SQL ou um Armazém de Dados SQL. Este tutorial utiliza o [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) como loja de análise.

Em seguida, os dados extraídos são transformados e carregados num conjunto de mesas [de esquemas estelares.](https://www.wikipedia.org/wiki/Star_schema) As tabelas consistem numa tabela central de factos e tabelas de dimensão relacionadas:

- A tabela central de factos no esquema estelar contém dados de bilhetes.
- As tabelas de dimensão contêm dados sobre locais, eventos, clientes e datas de compra.

Juntas, as tabelas centrais e dimensionais permitem um processamento analítico eficiente. O star-schema utilizado neste tutorial é exibido na seguinte imagem:

![arquiteturaOverView](./media/saas-tenancy-tenant-analytics-adf/starschematables.JPG)

Finalmente, as mesas de esquemas estelares são questionadas. Os resultados da consulta são exibidos visualmente usando o Power BI para destacar insights sobre o comportamento do inquilino e o seu uso da aplicação. Com este esquema estelar, faz consultas que expõem:

- Quem está comprando bilhetes e de que local.
- Padrões e tendências na venda de bilhetes.
- A relativa popularidade de cada local.

Este tutorial fornece exemplos básicos de insights que podem ser recolhidos a partir dos dados dos Bilhetes wingtip. Compreender como cada local utiliza o serviço pode fazer com que o vendedor de Bilhetes Wingtip pense em diferentes planos de serviço direcionados para locais mais ou menos ativos, por exemplo.

## <a name="setup"></a>Configuração

### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

- A aplicação De dados SaaS Database Per Tenant da Wingtip Tickets É implementada. Para implantar em menos de cinco minutos, consulte [O Deploy e explore a aplicação Wingtip SaaS](../../sql-database/saas-dbpertenant-get-started-deploy.md).
- Os bilhetes de base de dados SaaS Por Inquilino e o [código fonte](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) de aplicação são descarregados do GitHub. Consulte as instruções de descarregamento. Certifique-se de que *desbloqueia o ficheiro zip* antes de extrair o seu conteúdo.
- Está instalado o Power BI Desktop. [Baixar Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- O lote de inquilinos adicionais foi provisionado, ver o tutorial de [**inquilinos da Provision.**](../../sql-database/saas-dbpertenant-provision-and-catalog.md)

### <a name="create-data-for-the-demo"></a>Criar dados para a demonstração

Este tutorial explora a análise sobre os dados de venda de bilhetes. Neste passo, você gera dados de bilhetes para todos os inquilinos. Num passo posterior, estes dados são extraídos para análise. _Certifique-se de que forprovisionou o lote de inquilinos_ (como descrito anteriormente) para que tenha dados suficientes para expor uma série de diferentes padrões de compra de bilhetes.

1. No PowerShell ISE, abra *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*, e definir o seguinte valor:
    - **$DemoScenario**  =  **1** Comprar bilhetes para eventos em todos os locais
2. Pressione **f5** para executar o script e crie o histórico de compra de bilhetes para todos os locais. Com 20 inquilinos, o guião gera dezenas de milhares de bilhetes e pode demorar 10 minutos ou mais.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Implementar Armazém de Dados SQL, Fábrica de Dados e Armazenamento de Blob

Na aplicação Wingtip Tickets, os dados transacionais dos inquilinos são distribuídos por muitas bases de dados. A Azure Data Factory (ADF) é utilizada para orquestrar o Extrato, A Carga e Transformação (ELT) destes dados no armazém de dados. Para carregar os dados no SQL Data Warehouse de forma mais eficiente, a ADF extrai dados em ficheiros de blob intermédios e, em seguida, utiliza a [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) para carregar os dados no armazém de dados.

Neste passo, você implanta os recursos adicionais usados no tutorial: um Armazém de Dados SQL chamado _tenantanalytics_, uma Fábrica de Dados Azure chamada _ \<user\> dbtodwload-_ e uma conta de armazenamento Azure chamada _wingtipstaging \<user\> _. A conta de armazenamento é utilizada para reter temporariamente ficheiros de dados extraídos como bolhas antes de serem carregados no armazém de dados. Este passo também implanta o esquema do armazém de dados e define os oleodutos ADF que orquestram o processo ELT.

1. No PowerShell ISE, abra *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* e definido:
    - **$DemoScenario**  =  **2** Implementar armazém de dados de analítica de inquilinos, armazenamento de bolhas e fábrica de dados
1. Pressione **f5** para executar o roteiro de demonstração e implementar os recursos Azure.

Agora reveja os recursos Azure que implementou:

#### <a name="tenant-databases-and-analytics-store"></a>Bases de dados de inquilinos e loja de análise

Utilize o [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para se conectar aos **inquilinos1-dpt-user &lt; &gt; ** e servidores de utilizador ** &lt; &gt; de departamentode catálogo.** Substitua &lt; o utilizador pelo valor utilizado quando &gt; implementou a aplicação. Utilize login = *desenvolvedor* e palavra-passe = *P \@ ssword1*. Consulte o [tutorial introdutório](../../sql-database/saas-dbpertenant-wingtip-app-overview.md) para obter mais orientação.

![Ligar à Base de Dados SQL a partir de SSMS](./media/saas-tenancy-tenant-analytics-adf/ssmsSignIn.JPG)

No Explorador de Objetos:

1. Expanda o servidor *de &lt; utilizadores &gt; de inquilinos1-dpt.*
1. Expandir o nó de Bases de Dados e ver a lista de bases de dados de inquilinos.
1. Expandir o servidor de * &lt; &gt; utilizador do departamento de catálogo.*
1. Verifique se vê a loja de análise sintetizadora que contém os seguintes objetos:
    1. As tabelas **raw_Tickets,** **raw_Customers,** **raw_Events** e **raw_Venues** retêm dados extraídos brutos das bases de dados dos inquilinos.
    1. As mesas de esquemas estelares são **fact_Tickets,** **dim_Customers,** **dim_Venues,** **dim_Events**e **dim_Dates.**
    1. O procedimento armazenado, **sp_transformExtractedData** é usado para transformar os dados e carregá-los nas tabelas star-schema.

![DWtables](./media/saas-tenancy-tenant-analytics-adf/DWtables.JPG)

#### <a name="blob-storage"></a>Armazenamento de blobs

1. No [portal Azure,](https://ms.portal.azure.com)navegue para o grupo de recursos que utilizou para implementar a aplicação. Verifique se foi adicionada uma conta de armazenamento chamada **wingtipstaging. \<user\> **

   ![DWtables](./media/saas-tenancy-tenant-analytics-adf/adf-staging-storage.PNG)

1. Clique na conta de armazenamento de **asas \<user\> ** para explorar os objetos presentes.
1. Clique em azulejo **Blobs**
1. Clique no **configfile** do recipiente
1. Verifique se **o configfile** contém um ficheiro JSON chamado **TableConfig.json**. Este ficheiro contém os nomes de tabelas de origem e destino, nomes de colunas e nome sinuoso.

#### <a name="azure-data-factory-adf"></a>Fábrica de Dados Azure (ADF)

No [portal Azure](https://ms.portal.azure.com) do grupo de recursos, verifique se foi adicionada uma Fábrica de Dados Azure chamada _ \<user\> dbtodwload._

 ![adf_portal](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory-portal.png)

Esta secção explora a fábrica de dados criada.
Siga os passos abaixo para lançar a fábrica de dados:

1. No portal, clique na fábrica de dados chamada **dbtodwload. \<user\> **
2. Clique em **Author & Monitor** azulejo para lançar o designer data factory num separador.

## <a name="extract-load-and-transform-data"></a>Extrair, carregar e transformar dados

A Azure Data Factory é utilizada para orquestrar a extração, carregamento e transformação de dados. Neste tutorial, extrai dados de quatro diferentes vistas SQL de cada uma das bases de dados dos inquilinos: **RawTickets,** **rawCustomers,** **rawEvents**e **rawVenues**. Estas vistas incluem identificação do local, para que possa discriminar dados de cada local no armazém de dados. Os dados são carregados em tabelas de encenação correspondentes no armazém de dados: **raw_Tickets,** **raw_customers,** **raw_Events** e **raw_Venue.** Um procedimento armazenado transforma então os dados brutos e povoa as tabelas star-schema: **fact_Tickets,** **dim_Customers,** **dim_Venues,** **dim_Events**e **dim_Dates.**

Na secção anterior, implantou e ininicializou os recursos Azure necessários, incluindo a fábrica de dados. A fábrica de dados implantada inclui os oleodutos, conjuntos de dados, serviços ligados, etc., necessários para extrair, carregar e transformar os dados dos inquilinos. Vamos explorar ainda mais estes objetos e, em seguida, desencadear o oleoduto para mover dados das bases de dados dos inquilinos para o armazém de dados.

### <a name="data-factory-pipeline-overview"></a>Visão geral do gasoduto da fábrica de dados

Esta secção explora os objetos criados na fábrica de dados. O número seguinte descreve o fluxo de trabalho global do gasoduto ADF utilizado neste tutorial. Se preferir explorar o gasoduto mais tarde e ver os resultados primeiro, salte para a secção seguinte **Desencadear a execução do gasoduto**.

![adf_overview](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory.PNG)

Na página geral, mude para o separador **Autor** no painel esquerdo e observe que existem três [pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) e três conjuntos de [dados criados.](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)
![adf_author](./media/saas-tenancy-tenant-analytics-adf/adf_author_tab.JPG)

Os três oleodutos aninhados são: SQLDBToDW, DBCopy e TableCopy.

**Pipeline 1 - SQLDBToDW** analisa os nomes das bases de dados dos inquilinos armazenadas na base de dados do Catálogo (nome de mesa: [__ShardManagement].] A ShardsGlobal]) e para cada base de dados de inquilinos, executa o pipeline **DBCopy.** Após a conclusão, o **sp_TransformExtractedData** esquema de procedimento armazenado, é executado. Este procedimento armazenado transforma os dados carregados nas mesas de preparação e povoa as mesas star-schema.

**Pipeline 2 - DBCopy** procura os nomes das tabelas e colunas de origem a partir de um ficheiro de configuração armazenado no armazenamento de blob.  O pipeline **TableCopy** é então executado para cada uma das quatro tabelas: TicketFacts, CustomerFacts, EventFacts e VenueFacts. A atividade **[foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** executa em paralelo para todas as 20 bases de dados. A ADF permite que um máximo de 20 iterações em loop sejam executados em paralelo. Considere criar vários oleodutos para mais bases de dados.

**Pipeline 3 - TableCopy** utiliza números de versão de linha na Base de Dados SQL _(rowversion)_ para identificar linhas que tenham sido alteradas ou atualizadas. Esta atividade procura a versão inicial e final da fila para extrair linhas das tabelas de origem. A tabela **CopyTracker** armazenada em cada base de dados de inquilinos rastreia a última linha extraída de cada tabela de origem em cada execução. As filas novas ou alteradas são copiadas para as tabelas de preparação correspondentes no armazém de dados: **raw_Tickets,** **raw_Customers,** **raw_Venues**e **raw_Events.** Finalmente, a versão da última linha é guardada na tabela **CopyTracker** para ser utilizada como a versão inicial da linha para a próxima extração.

Existem também três serviços ligados parametrizados que ligam a fábrica de dados à fonte SQL Databases, ao Armazém de Dados SQL alvo e ao armazenamento intermédio de Blob. No separador **Autor,** clique em **Ligações** para explorar os serviços ligados, como mostra a seguinte imagem:

![adf_linkedservices](./media/saas-tenancy-tenant-analytics-adf/linkedservices.JPG)

Correspondente aos três serviços ligados, existem três conjuntos de dados que se referem aos dados que utiliza nas atividades do pipeline como inputs ou saídas. Explore cada um dos conjuntos de dados para observar ligações e parâmetros utilizados. _O AzureBlob_ aponta para o ficheiro de configuração que contém tabelas e colunas de origem e alvo, bem como a coluna de rastreador em cada fonte.
  
### <a name="data-warehouse-pattern-overview"></a>Visão geral do padrão do armazém de dados

O Azure Synapse (anteriormente Azure SQL Data Warehouse) é usado como loja de análise para realizar agregação nos dados do inquilino. Nesta amostra, a PolyBase é utilizada para carregar dados no armazém de dados. Os dados brutos são carregados em mesas de preparação que têm uma coluna de identidade para acompanhar as linhas que foram transformadas nas tabelas de esquemas estelares. A imagem que se segue mostra o padrão de carregamento: ![ padrão de carregamento](./media/saas-tenancy-tenant-analytics-adf/loadingpattern.JPG)

Lentamente mudando as tabelas de dimensão tipo 1 (SCD) são utilizadas neste exemplo. Cada dimensão tem uma chave de substituição definida usando uma coluna de identidade. Como uma boa prática, a tabela de dimensão da data é pré-povoada para economizar tempo. Para as tabelas de outra dimensão, uma TABELA CREATE AS SELECT... A declaração (CTAS) é utilizada para criar um quadro temporário que contenha as linhas modificadas e não modificadas existentes, juntamente com as chaves de substituição. Isto é feito com IDENTITY_INSERT=ON. Novas filas são então inseridas na tabela com IDENTITY_INSERT=OFF. Para uma retrocesso fácil, a tabela de dimensões existente é renomeada e a tabela temporária é renomeada para se tornar a nova tabela de dimensões. Antes de cada execução, a tabela de dimensões antigas é eliminada.

As tabelas de dimensão são carregadas antes da tabela de factos. Esta sequência garante que, para cada facto que chegue, já existem todas as dimensões referenciadas. À medida que os factos são carregados, a chave de negócio seleção para cada dimensão correspondente é correspondida e as chaves de substituição correspondentes são adicionadas a cada facto.

O passo final da transformação elimina os dados de encenação prontos para a próxima execução do oleoduto.

### <a name="trigger-the-pipeline-run"></a>Desencadear a execução do gasoduto

Siga os passos abaixo para executar o extrato completo, carga e transforme o gasoduto para todas as bases de dados dos inquilinos:

1. No separador **Autor** da interface de utilizador ADF, selecione o gasoduto **SQLDBToDW** a partir do painel esquerdo.
1. Clique no **Gatilho** e a partir do menu puxado para baixo clique **em "Trigger Now**" Esta ação dirige o oleoduto imediatamente. Num cenário de produção, definiria um calendário para executar o oleoduto para atualizar os dados num horário.
  ![adf_trigger](./media/saas-tenancy-tenant-analytics-adf/adf_trigger.JPG)
1. Na página **Pipeline Run,** clique em **Terminar**.

### <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Na interface de utilizador ADF, altere para o separador **Monitor** a partir do menu à esquerda.
1. Clique em **Refresh** até que o estado do gasoduto SQLDBToDW seja **bem sucedido**.
  ![adf_monitoring](./media/saas-tenancy-tenant-analytics-adf/adf_monitoring.JPG)
1. Ligue-se ao armazém de dados com SSMS e questione as tabelas star-schema para verificar se os dados foram carregados nestas tabelas.

Uma vez concluído o oleoduto, a tabela de factos contém dados de venda de bilhetes para todos os locais e as tabelas de dimensão são povoadas com os locais, eventos e clientes correspondentes.

## <a name="data-exploration"></a>Exploração de Dados

### <a name="visualize-tenant-data"></a>Visualizar os dados dos inquilinos

Os dados do star-schema fornecem todos os dados de venda de bilhetes necessários para a sua análise. Visualizar dados graficamente facilita a visualização de tendências em grandes conjuntos de dados. Nesta secção, utiliza o **Power BI** para manipular e visualizar os dados dos inquilinos no armazém de dados.

Utilize os seguintes passos para ligar ao Power BI e para importar as vistas que criou anteriormente:

1. Ambiente de trabalho do Launch Power BI.
2. A partir da fita Home, selecione **Obter Dados,** e selecione **Mais...** do menu.
3. Na janela **Obter Dados,** selecione Base de **Dados Azure SQL**.
4. Na janela de login da base de dados, introduza o nome do seu servidor **(catalog-dpt- &lt; Utilizador &gt; .database.windows.net**). Selecione **Importar** para **modo de conectividade de dados**e, em seguida, clique EM **OK**.

    ![sign-in-to-power-bi](./media/saas-tenancy-tenant-analytics-adf/powerBISignIn.PNG)

5. Selecione **Base de Dados** no painel esquerdo, introduza o nome do utilizador = *programador,* e introduza a palavra-passe = *P \@ ssword1*. Clique em **Ligar**.  

    ![base de dados-sign-in](./media/saas-tenancy-tenant-analytics-adf/databaseSignIn.PNG)

6. No painel **Navigator,** sob a base de dados de análise, selecione as tabelas star-schema: **fact_Tickets,** **dim_Events,** **dim_Venues,** **dim_Customers** e **dim_Dates.** Em seguida, selecione **Carregar**.

Parabéns! Carregou os dados com sucesso no Power BI. Agora explore visualizações interessantes para obter informações sobre os seus inquilinos. Vamos ver como a análise pode fornecer algumas recomendações baseadas em dados para a equipe de negócios wingtip Tickets. As recomendações podem ajudar a otimizar o modelo de negócio e a experiência do cliente.

Comece por analisar os dados de venda de bilhetes para ver a variação de utilização em todos os locais. Selecione as opções mostradas no Power BI para traçar um gráfico de barras do número total de bilhetes vendidos por cada local. (Devido à variação aleatória no gerador de bilhetes, os seus resultados podem ser diferentes.)

![TotalticketsByVenues](./media/saas-tenancy-tenant-analytics-adf/TotalTicketsByVenues-DW.PNG)

O enredo anterior confirma que o número de bilhetes vendidos por cada local varia. Os locais que vendem mais bilhetes estão a usar o seu serviço mais fortemente do que os locais que vendem menos bilhetes. Pode haver aqui uma oportunidade para adaptar a atribuição de recursos de acordo com as diferentes necessidades dos inquilinos.

Pode analisar ainda mais os dados para ver como as vendas de bilhetes variam ao longo do tempo. Selecione as opções mostradas na seguinte imagem no Power BI para traçar o número total de bilhetes vendidos todos os dias por um período de 60 dias.

![VendaVersusDate](./media/saas-tenancy-tenant-analytics-adf/SaleVersusDate-DW.PNG)

O gráfico anterior mostra que as vendas de bilhetes aumentam para alguns locais. Estes picos reforçam a ideia de que alguns locais podem estar a consumir desproporcionalmente os recursos do sistema. Até agora não há um padrão óbvio quando os picos ocorrem.

Em seguida, vamos investigar o significado destes dias de venda máxima. Quando é que estes picos ocorrem depois de os bilhetes irem à venda? Para traçar bilhetes vendidos por dia, selecione as opções mostradas na seguinte imagem no Power BI.

![Distribuição do Dia da Venda](./media/saas-tenancy-tenant-analytics-adf/SaleDistributionPerDay-DW.PNG)

Este lote mostra que alguns locais vendem um grande número de bilhetes no primeiro dia de venda. Assim que os bilhetes estão à venda nestes locais, parece haver uma correria louca. Esta explosão de atividade por alguns locais pode afetar o serviço para outros inquilinos.

Você pode perfurar os dados novamente para ver se esta corrida louca é verdadeira para todos os eventos hospedados por estes locais. Em parcelas anteriores, viu que o Junta de Concertos de Contoso vende muitos bilhetes, e que Contoso também tem um pico na venda de bilhetes em determinados dias. Brinque com opções de Power BI para traçar vendas cumulativas de bilhetes para a Sala de Concertos Contoso, focando-se nas tendências de venda para cada um dos seus eventos. Todos os eventos seguem o mesmo padrão de venda? Tente produzir um enredo como o de baixo.

![ContosoSales](./media/saas-tenancy-tenant-analytics-adf/EventSaleTrends.PNG)

Este lote de venda acumulada de bilhetes ao longo do tempo para A Sala de Concertos Contoso para cada evento mostra que a corrida louca não acontece para todos os eventos. Brinque com as opções de filtro para explorar as tendências de venda para outros locais.

As ideias sobre os padrões de venda de bilhetes podem levar a Wingtip Tickets a otimizar o seu modelo de negócio. Em vez de cobrar todos os inquilinos igualmente, talvez wingtip deva introduzir níveis de serviço com diferentes tamanhos de computação. Locais maiores que precisam de vender mais bilhetes por dia poderiam ser oferecidos um nível mais elevado com um acordo de nível de serviço mais elevado (SLA). Esses locais poderiam ter as suas bases de dados colocadas em conjunto com limites de recursos por base de dados mais elevados. Cada nível de serviço poderia ter uma atribuição de vendas por hora, com taxas adicionais cobradas por exceder a atribuição. Locais maiores que tenham explosões periódicas de vendas beneficiariam dos níveis mais altos, e os Bilhetes Wingtip podem rentabilizar o seu serviço de forma mais eficiente.

Entretanto, alguns clientes da Wingtip Tickets queixam-se de que lutam para vender bilhetes suficientes para justificar o custo do serviço. Talvez nestes insights haja uma oportunidade para impulsionar a venda de bilhetes para locais com baixo desempenho. Vendas mais elevadas aumentariam o valor percebido do serviço. Clique à direita fact_Tickets e selecione **Nova medida**. Introduza a seguinte expressão para a nova medida denominada **AverageTicketsSold:**

```sql
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selecione as seguintes opções de visualização para traçar os bilhetes percentuais vendidos por cada local para determinar o seu relativo sucesso.

![AvgTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/AvgTicketsByVenues-DW.PNG)

O enredo acima mostra que, embora a maioria dos locais venda mais de 80% dos seus bilhetes, alguns estão a lutar para preencher mais de metade dos seus lugares. Jogue com os Valores Bem para selecionar a percentagem máxima ou mínima de bilhetes vendidos para cada local.

## <a name="embedding-analytics-in-your-apps"></a>Incorporar análises nas suas apps

Este tutorial focou-se na análise de inquilinos cruzados usado para melhorar a compreensão do fornecedor de software dos seus inquilinos. A analytics também pode fornecer informações aos _inquilinos,_ para ajudá-los a gerir o seu negócio de forma mais eficaz.

No exemplo dos Bilhetes Wingtip, descobriu anteriormente que as vendas de bilhetes tendem a seguir padrões previsíveis. Esta visão pode ser usada para ajudar os locais de baixo desempenho a impulsionar a venda de bilhetes. Talvez haja uma oportunidade de utilizar técnicas de aprendizagem automática para prever a venda de bilhetes para eventos. Os efeitos das variações de preços também poderiam ser modelados, a favor da previsão do impacto da oferta de descontos. O Power BI Embedded poderia ser integrado numa aplicação de gestão de eventos para visualizar as vendas previstas, incluindo o impacto dos descontos no total de lugares vendidos e receitas em eventos de baixa venda. Com o Power BI Incorporado, pode até integrar a aplicação do desconto nos preços dos bilhetes, mesmo na experiência de visualização.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> - Crie a loja de analítica para o carregamento.
> - Utilize a Azure Data Factory (ADF) para extrair dados de cada base de dados de inquilinos no armazém de dados de análise.
> - Otimize os dados extraídos (reorganize-se num esquema estelar).
> - Consultar o armazém de dados analíticos.
> - Use o Power BI para visualização de dados para destacar tendências nos dados dos inquilinos e fazer recomendação para melhorias.

Parabéns!

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais adicionais que se baseiam na aplicação Wingtip SaaS.](../../sql-database/saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
