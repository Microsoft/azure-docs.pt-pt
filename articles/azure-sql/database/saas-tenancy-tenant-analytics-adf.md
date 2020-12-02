---
title: Executar consultas de análise contra bases de dados de inquilinos
description: Consultas de análise de inquilinos cruzados usando dados extraídos da Base de Dados Azure SQL, Azure Synapse Analytics, Azure Data Factory ou Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: dc47c996748b126841cbeff1ea3f6f18f423951f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457642"
---
# <a name="explore-saas-analytics-with-azure-sql-database-azure-synapse-analytics-data-factory-and-power-bi"></a>Explore a análise do SaaS com a Azure SQL Database, Azure Synapse Analytics, Data Factory e Power BI
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você anda por um cenário de análise de ponta a ponta. O cenário demonstra como a análise sobre os dados dos inquilinos pode capacitar os fornecedores de software a tomar decisões inteligentes. Utilizando dados extraídos de cada base de dados de inquilinos, você usa analítica para obter informações sobre o comportamento do inquilino, incluindo a sua utilização da aplicação De Ingressos De Ponta de Amostra SaaS. Este cenário envolve três etapas:

1. **Extrair dados** de cada base de dados de inquilinos numa loja de análise, neste caso, uma piscina DE SQL dedicada.
2. **Otimize os dados extraídos** para o processamento de análise.
3. Use ferramentas **de Business Intelligence** para extrair informações úteis, que podem orientar a tomada de decisão.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Crie a loja de análise de inquilinos para carregamento.
> - Utilize a Azure Data Factory (ADF) para extrair dados de cada base de dados de inquilinos no armazém de dados analíticos.
> - Otimize os dados extraídos (reorganize-se num esquema estelar).
> - Consultar o armazém de dados de análise.
> - Use o Power BI para visualização de dados para destacar as tendências dos dados dos inquilinos e fazer recomendações para melhorias.

![arquiteturaOverView](./media/saas-tenancy-tenant-analytics-adf/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Análise sobre dados de inquilinos extraídos

As aplicações saaS possuem uma quantidade potencialmente vasta de dados de inquilinos na nuvem. Estes dados podem fornecer uma rica fonte de informações sobre a operação e utilização da sua aplicação, e o comportamento dos seus inquilinos. Estes insights podem orientar o desenvolvimento de funcionalidades, melhorias de usabilidade e outros investimentos nas apps e plataforma.

O acesso aos dados para todos os inquilinos é simples quando todos os dados estão numa base de dados multi-inquilinos. Mas o acesso é mais complexo quando distribuído em escala por milhares de bases de dados. Uma forma de domar a complexidade é extrair os dados para uma base de dados de análise ou um armazém de dados para consulta.

Este tutorial apresenta um cenário de análise de ponta a ponta para a aplicação de Ingressos wingtip. Em primeiro lugar, [a Azure Data Factory (ADF)](../../data-factory/introduction.md) é usada como ferramenta de orquestração para extrair bilhetes de venda e dados relacionados de cada base de dados de inquilinos. Estes dados são carregados em mesas de preparação numa loja de análise. A loja de análise pode ser uma Base de Dados SQL ou uma piscina SQL dedicada. Este tutorial usa [a Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) como loja de análise.

Em seguida, os dados extraídos são transformados e carregados em um conjunto de tabelas [de esquemas estelares.](https://www.wikipedia.org/wiki/Star_schema) As tabelas consistem num quadro central de factos mais tabelas de dimensão relacionadas:

- A tabela central de factos no esquema estelar contém dados de bilhetes.
- As tabelas de dimensão contêm dados sobre locais, eventos, clientes e datas de compra.

Juntas as tabelas central e de dimensão permitem um processamento analítico eficiente. O esquema de estrelas utilizado neste tutorial é exibido na seguinte imagem:

![Diagrama que mostra o esquema estelar que é usado neste tutorial.](./media/saas-tenancy-tenant-analytics-adf/starschematables.JPG)

Finalmente, as mesas de esquema de estrelas são questionadas. Os resultados da consulta são apresentados visualmente usando o Power BI para destacar insights sobre o comportamento do inquilino e o seu uso da aplicação. Com este esquema de estrelas, você corre consultas que expõem:

- Quem está a comprar bilhetes e de que local.
- Padrões e tendências na venda de bilhetes.
- A relativa popularidade de cada local.

Este tutorial fornece exemplos básicos de insights que podem ser recolhidos a partir dos dados dos Bilhetes Wingtip. Compreender como cada local utiliza o serviço pode fazer com que o vendedor de Bilhetes Wingtip pense em diferentes planos de serviço direcionados para locais mais ou menos ativos, por exemplo.

## <a name="setup"></a>Configuração

### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

- A aplicação Wingtip Tickets SaaS Database per Tenant é implementada. Para implementar em menos de cinco minutos, consulte [Implementar e explorar a aplicação Wingtip SaaS](./saas-dbpertenant-get-started-deploy.md).
- Os scripts de base de dados saas de bilhetes de ponta do Wingtip por inquilino e [código fonte](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) de aplicação são descarregados a partir do GitHub. Consulte as instruções de descarregamento. Certifique-se de *desbloquear o ficheiro zip* antes de extrair o seu conteúdo.
- O Power BI Desktop está instalado. [Baixar Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- O lote de inquilinos adicionais foi a provisionado, ver o [**tutorial de inquilinos da Provisão.**](./saas-dbpertenant-provision-and-catalog.md)

### <a name="create-data-for-the-demo"></a>Criar dados para a demonstração

Este tutorial explora a análise sobre os dados de venda de bilhetes. Neste passo, gera dados de bilhetes para todos os inquilinos. Posteriormente, estes dados são extraídos para análise. _Certifique-se de que fornece o lote de inquilinos_ (como descrito anteriormente) para que tenha dados suficientes para expor uma gama de diferentes padrões de compra de bilhetes.

1. No PowerShell ISE, abra *...\Módulos de aprendizagem\Analytics operacional\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*, e definir o seguinte valor:
    - **$DemoScenario**  =  **1** Compre bilhetes para eventos em todos os locais
2. Pressione **F5** para executar o script e criar histórico de compra de bilhetes para todos os locais. Com 20 inquilinos, o roteiro gera dezenas de milhares de bilhetes e pode demorar 10 minutos ou mais.

### <a name="deploy-azure-synapse-analytics-data-factory-and-blob-storage"></a>Implementar Azure Synapse Analytics, Data Factory e Blob Storage

Na aplicação Wingtip Tickets, os dados transacionais dos inquilinos são distribuídos por muitas bases de dados. A Azure Data Factory (ADF) é usada para orquestrar o Extrato, Carga e Transformação (ELT) destes dados no armazém de dados. Para carregar os dados no Azure Synapse Analytics de forma mais eficiente, a ADF extrai dados em ficheiros de bolhas intermédias e, em seguida, utiliza [a PolyBase](../../synapse-analytics/sql-data-warehouse/design-elt-data-loading.md) para carregar os dados no armazém de dados.

Neste passo, você implanta os recursos adicionais utilizados no tutorial: uma piscina de SQL dedicada chamada _tenantanalytics_, uma Fábrica de Dados Azure chamada _dbtodwload- \<user\>_ e uma conta de armazenamento Azure chamada _wingtipstaging \<user\>_. A conta de armazenamento é usada para reter temporariamente ficheiros de dados extraídos como bolhas antes de serem carregados no armazém de dados. Este passo também implanta o esquema de armazém de dados e define os oleodutos ADF que orquestram o processo ELT.

1. No PowerShell ISE, abra *...\Módulos de aprendizagem\Analytics operacional\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* e definido:
    - **$DemoScenario**  =  **2** Implementar armazém de dados de análise de inquilino, armazenamento de bolhas e fábrica de dados
1. Pressione **f5** para executar o script de demonstração e implementar os recursos Azure.

Agora reveja os recursos do Azure que implementou:

#### <a name="tenant-databases-and-analytics-store"></a>Bases de dados de inquilinos e loja de analítica

Utilize [o SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) para ligar aos **inquilinos utilizadores de 1 dpt- &lt; utilizador &gt;** e **servidores de &lt; utilizadores &gt; de catálogos.** Substitua &lt; o utilizador pelo valor utilizado quando &gt; implementou a aplicação. Utilizar Login = *programador* e palavra-passe = *P \@ ssword1*. Consulte o [tutorial introdutório](./saas-dbpertenant-wingtip-app-overview.md) para obter mais orientação.

![Ligue à Base de Dados SQL a partir de SSMS](./media/saas-tenancy-tenant-analytics-adf/ssmsSignIn.JPG)

No Explorador de Objetos:

1. Expandir o servidor *de &lt; utilizadores &gt; de 1-dpt dos inquilinos.*
1. Expanda o nó de bases de dados e consulte a lista de bases de dados de inquilinos.
1. Expandir o servidor *de &lt; &gt; utilizador do catálogo-dpt.*
1. Verifique se vê a loja de análise contendo os seguintes objetos:
    1. As **tabelas raw_Tickets,** **raw_Customers,** **raw_Events** e **raw_Venues** detêm dados extraídos em bruto das bases de dados dos inquilinos.
    1. As mesas de esquema de estrelas são **fact_Tickets,** **dim_Customers,** **dim_Venues,** **dim_Events** e **dim_Dates.**
    1. O procedimento armazenado, **sp_transformExtractedData** é usado para transformar os dados e carregá-lo nas tabelas de esquemas estelares.

![O Screenshot mostra o Object Explorer com tabelas expandidas para mostrar vários objetos de base de dados.](./media/saas-tenancy-tenant-analytics-adf/DWtables.JPG)

#### <a name="blob-storage"></a>Armazenamento de blobs

1. No [portal Azure,](https://ms.portal.azure.com)navegue para o grupo de recursos que utilizou para implementar a aplicação. Verifique se foi adicionada uma conta de armazenamento chamada **wingtipstaging. \<user\>**

   ![DWtables](./media/saas-tenancy-tenant-analytics-adf/adf-staging-storage.PNG)

1. Clique na conta de armazenamento **de pontas \<user\> de asas** para explorar os objetos presentes.
1. Clique **em azulejo blobs**
1. Clique no **recipiente configfile**
1. Verifique se **o configfile** contém um ficheiro JSON chamado **TableConfig.js.** Este ficheiro contém os nomes das tabelas de origem e destino, nomes de colunas e nome da coluna de rastreador.

#### <a name="azure-data-factory-adf"></a>Fábrica de Dados Azure (ADF)

No [portal Azure](https://ms.portal.azure.com) do grupo de recursos, verifique se foi adicionada uma Fábrica de Dados Azure chamada _\<user\> dbtodwload._

 ![adf_portal](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory-portal.png)

Esta secção explora a fábrica de dados criada.
Siga os passos abaixo para lançar a fábrica de dados:

1. No portal, clique na fábrica de dados chamada **dbtodwload. \<user\>**
2. Clique **em & Monitor** de Autor para lançar o designer da Data Factory num separador.

## <a name="extract-load-and-transform-data"></a>Extrair, Carregar e Transformar dados

A Azure Data Factory é utilizada para orquestrar a extração, carregamento e transformação de dados. Neste tutorial, você extrai dados de quatro diferentes vistas SQL de cada uma das bases de dados dos inquilinos: **rawTickets,** **rawCustomers,** **rawEvents,** e **rawVenues.** Estas vistas incluem iD do local, para que você possa discriminar dados de cada local no armazém de dados. Os dados são carregados em tabelas de preparação correspondentes no armazém de dados: **raw_Tickets,** **raw_customers,** **raw_Events** e **raw_Venue**. Um procedimento armazenado transforma então os dados brutos e povoa as tabelas star-schema: **fact_Tickets,** **dim_Customers,** **dim_Venues,** **dim_Events** e **dim_Dates**.

Na secção anterior, implementou e iniciais os recursos Azure necessários, incluindo a fábrica de dados. A fábrica de dados implantada inclui os oleodutos, conjuntos de dados, serviços ligados, etc., necessários para extrair, carregar e transformar os dados do arrendatário. Vamos explorar mais estes objetos e depois desencadear o oleoduto para mover dados das bases de dados dos inquilinos para o armazém de dados.

### <a name="data-factory-pipeline-overview"></a>Visão geral do gasoduto de fábrica de dados

Esta secção explora os objetos criados na fábrica de dados. O seguinte número descreve o fluxo de trabalho global do gasoduto ADF utilizado neste tutorial. Se preferir explorar o gasoduto mais tarde e ver os resultados primeiro, salte para a secção seguinte **Desencadeie a conduta .**

![adf_overview](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory.PNG)

Na página geral, mude para o **separador Autor** no painel esquerdo e observe que existem três [oleodutos](../../data-factory/concepts-pipelines-activities.md) e três [conjuntos de dados criados.](../../data-factory/concepts-datasets-linked-services.md)
![adf_author](./media/saas-tenancy-tenant-analytics-adf/adf_author_tab.JPG)

Os três oleodutos aninhados são: SQLDBToDW, DBCopy e TableCopy.

**Pipeline 1 - SQLDBToDW** procura os nomes das bases de dados dos inquilinos armazenadas na base de dados do Catálogo (nome de tabela: [__ShardManagement].. ShardsGlobal]) e para cada base de dados de inquilinos, executa o pipeline **DBCopy.** Após a conclusão, o esquema de procedimento armazenado **sp_TransformExtractedData** fornecido é executado. Este procedimento armazenado transforma os dados carregados nas tabelas de preparação e povoa as tabelas de esquemas estelares.

**Pipeline 2 - A DBCopy** procura os nomes das tabelas de origem e colunas de um ficheiro de configuração armazenado no armazenamento de bolhas.  O pipeline **TableCopy** é então executado para cada uma das quatro tabelas: TicketFacts, CustomerFacts, EventFacts e VenueFacts. A atividade **[foreach](../../data-factory/control-flow-for-each-activity.md)** executa em paralelo para todas as 20 bases de dados. A ADF permite que um máximo de 20 iterações em loop seja executado em paralelo. Considere criar vários oleodutos para mais bases de dados.

**Pipeline 3 - O TableCopy** utiliza números de versão de linha na Base de Dados SQL _(versão de linha)_ para identificar linhas que foram alteradas ou atualizadas. Esta atividade procura a versão inicial e final para extrair linhas das tabelas de origem. A tabela **CopyTracker** armazenada em cada base de dados de inquilinos rastreia a última linha extraída de cada tabela de origem em cada corrida. As linhas novas ou alteradas são copiadas para as correspondentes tabelas de preparação no armazém de dados: **raw_Tickets,** **raw_Customers,** **raw_Venues** e **raw_Events**. Finalmente, a versão da última linha é guardada na tabela **CopyTracker** para ser usada como a versão inicial da linha para a próxima extração.

Existem também três serviços interligados parametrizados que ligam a fábrica de dados à fonte SQL Databases, o pool SQL dedicado ao alvo e o armazenamento intermédio blob. No **separador Autor,** clique em **Ligações** para explorar os serviços ligados, como mostra a seguinte imagem:

![adf_linkedservices](./media/saas-tenancy-tenant-analytics-adf/linkedservices.JPG)

Correspondentes aos três serviços ligados, existem três conjuntos de dados que se referem aos dados que utiliza nas atividades do pipeline como entradas ou saídas. Explore cada um dos conjuntos de dados para observar ligações e parâmetros utilizados. _O AzureBlob_ aponta para o ficheiro de configuração que contém tabelas e colunas de origem e alvo, bem como para a coluna de rastreador em cada fonte.
  
### <a name="data-warehouse-pattern-overview"></a>Visão geral do padrão do armazém de dados

A Azure Synapse é usada como a loja de análise para realizar agregação nos dados do inquilino. Nesta amostra, a PolyBase é utilizada para carregar dados no armazém de dados. Os dados brutos são carregados em tabelas de encenação que têm uma coluna de identidade para acompanhar as linhas que foram transformadas nas tabelas de esquemas estelares. A imagem a seguir mostra o padrão de carregamento: ![ O diagrama mostra o padrão de carregamento das tabelas de bases de dados.](./media/saas-tenancy-tenant-analytics-adf/loadingpattern.JPG)

Neste exemplo são utilizadas tabelas de dimensão tipo 1 (SCD) de mudança lenta . Cada dimensão tem uma chave de substituição definida usando uma coluna de identidade. Como uma boa prática, a tabela de dimensão da data é pré-povoada para economizar tempo. Para as outras tabelas de dimensão, uma tabela CREATE AS SELECT... (CTAS) é utilizada para criar uma tabela temporária contendo as linhas modificadas e não modificadas existentes, juntamente com as chaves de substituição. Isto é feito com IDENTITY_INSERT=ON. As novas linhas são então inseridas na tabela com IDENTITY_INSERT=OFF. Para um retrocesso fácil, a tabela de dimensões existente é renomeada e a tabela temporária é renomeada para se tornar a nova tabela de dimensões. Antes de cada execução, a tabela de dimensão antiga é apagada.

As tabelas de dimensão são carregadas antes da tabela de factos. Esta sequência garante que, para cada facto que chega, já existem todas as dimensões referenciadas. À medida que os factos são carregados, a chave de negócio para cada dimensão correspondente é correspondida e as chaves de substituição correspondentes são adicionadas a cada facto.

O passo final da transformação elimina os dados de encenação prontos para a próxima execução do gasoduto.

### <a name="trigger-the-pipeline-run"></a>Desencadear a corrida do gasoduto

Siga os passos abaixo para executar o extrato completo, carga e transformar o gasoduto para todas as bases de dados do arrendatário:

1. No **separador Autor** da interface de utilizador ADF, selecione o gasoduto **SQLDBToDW** a partir do painel esquerdo.
1. Clique **em Trigger** e no menu premido clique em Trigger **Now**. Esta ação corre o oleoduto imediatamente. Num cenário de produção, definiria um calendário para a execução do oleoduto para atualizar os dados num horário.
  ![O Screenshot mostra os Recursos de Fábrica para um oleoduto chamado S Q L D B A D W com a opção Trigger expandida e Trigger Now selecionado.](./media/saas-tenancy-tenant-analytics-adf/adf_trigger.JPG)
1. Na página **Pipeline Run,** clique **em Terminar**.

### <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Na interface de utilizador ADF, mude para o **separador Monitor** a partir do menu à esquerda.
1. Clique **em Atualizar** até que o estado do gasoduto SQLDBToDW seja bem **sucedido**.
  ![A screenshot mostra o gasoduto S Q L D B A D W com um estado de Sucesso.](./media/saas-tenancy-tenant-analytics-adf/adf_monitoring.JPG)
1. Ligue-se ao armazém de dados com sSMS e consultar as tabelas star-schema para verificar se os dados foram carregados nestas tabelas.

Uma vez concluído o pipeline, a tabela de factos contém dados de venda de bilhetes para todos os locais e as tabelas de dimensão são povoadas com os locais, eventos e clientes correspondentes.

## <a name="data-exploration"></a>Exploração de Dados

### <a name="visualize-tenant-data"></a>Visualizar os dados dos inquilinos

Os dados no esquema estelar fornecem todos os dados de venda de bilhetes necessários para a sua análise. Visualizar dados graficamente facilita a visualização de tendências em grandes conjuntos de dados. Nesta secção, você usa **o Power BI** para manipular e visualizar os dados do inquilino no armazém de dados.

Use os seguintes passos para ligar ao Power BI e importar as vistas que criou anteriormente:

1. Lançar power bi desktop.
2. A partir da fita Home, **selecione Obter Dados,** e selecione **Mais...** do menu.
3. Na janela **Obter Dados,** selecione **Azure SQL Database**.
4. Na janela de início de sessão de dados, insira o nome do seu servidor **(catalog-dpt- &lt; User &gt; .database.windows.net**). Selecione **Importar** para **o Modo de Conectividade de Dados** e, em seguida, clique em **OK**.

    ![sign-in-to-power-bi](./media/saas-tenancy-tenant-analytics-adf/powerBISignIn.PNG)

5. Selecione **a Base de Dados** no painel esquerdo e, em seguida, introduza o nome do utilizador = *desenvolvedor*, e introduza a palavra-passe = *P \@ ssword1*. Clique em **Ligar**.  

    ![registo de bases de dados](./media/saas-tenancy-tenant-analytics-adf/databaseSignIn.PNG)

6. No painel **Do Navegador,** sob a base de dados de análise, selecione as tabelas star-schema: **fact_Tickets**, **dim_Events,** **dim_Venues,** **dim_Customers** e **dim_Dates**. Em seguida, **selecione Carregar**.

Parabéns! Carregou os dados com sucesso no Power BI. Agora explore visualizações interessantes para obter informações sobre os seus inquilinos. Vamos percorrer como a análise pode fornecer algumas recomendações baseadas em dados para a equipa de negócios wingtip tickets. As recomendações podem ajudar a otimizar o modelo de negócio e a experiência do cliente.

Comece por analisar os dados de venda de bilhetes para ver a variação no uso em todos os locais. Selecione as opções mostradas no Power BI para traçar um gráfico de barras do número total de bilhetes vendidos por cada local. (Devido à variação aleatória no gerador de bilhetes, os seus resultados podem ser diferentes.)

![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/TotalTicketsByVenues-DW.PNG)

O lote anterior confirma que o número de bilhetes vendidos por cada local varia. Os locais que vendem mais bilhetes estão a usar o seu serviço mais fortemente do que os locais que vendem menos bilhetes. Pode haver aqui uma oportunidade de adaptar a alocação de recursos de acordo com as diferentes necessidades do inquilino.

Pode analisar ainda mais os dados para ver como as vendas de bilhetes variam ao longo do tempo. Selecione as opções mostradas na imagem seguinte no Power BI para traçar o número total de bilhetes vendidos por dia por um período de 60 dias.

![SaleVersusDate](./media/saas-tenancy-tenant-analytics-adf/SaleVersusDate-DW.PNG)

O gráfico anterior mostra que a venda de bilhetes aumenta para alguns locais. Estes picos reforçam a ideia de que alguns locais podem estar a consumir desproporcionalmente os recursos do sistema. Até agora não há nenhum padrão óbvio quando os picos ocorrem.

Em seguida, vamos investigar o significado destes dias de pico de venda. Quando é que estes picos ocorrem depois de os bilhetes irem à venda? Para traçar bilhetes vendidos por dia, selecione as opções mostradas na imagem seguinte no Power BI.

![Distribuição do SaleDayD](./media/saas-tenancy-tenant-analytics-adf/SaleDistributionPerDay-DW.PNG)

Este enredo mostra que alguns locais vendem um grande número de bilhetes no primeiro dia de venda. Assim que os bilhetes estão à venda nestes locais, parece haver uma corrida louca. Esta explosão de atividade por alguns locais pode afetar o serviço para outros inquilinos.

Você pode perfurar os dados novamente para ver se esta corrida louca é verdade para todos os eventos hospedados por estes locais. Em parcelas anteriores, viu que o Contoso Concert Hall vende muitos bilhetes, e que o Contoso também tem um pico na venda de bilhetes em determinados dias. Brinque com as opções power bi para traçar vendas cumulativas de bilhetes para a Contoso Concert Hall, focando-se nas tendências de venda para cada um dos seus eventos. Todos os eventos seguem o mesmo padrão de venda? Tente produzir um enredo como o de baixo.

![ContosoSales](./media/saas-tenancy-tenant-analytics-adf/EventSaleTrends.PNG)

Este lote de venda cumulativa de bilhetes ao longo do tempo para a Salas de Concertos Contoso para cada evento mostra que a corrida louca não acontece para todos os eventos. Brinque com as opções de filtro para explorar tendências de venda para outros locais.

As ideias sobre os padrões de venda de bilhetes podem levar os Bilhetes Wingtip a otimizar o seu modelo de negócio. Em vez de cobrar a todos os inquilinos de forma igual, talvez wingtip deva introduzir níveis de serviço com diferentes tamanhos de cálculo. Locais maiores que precisam de vender mais bilhetes por dia poderiam ser oferecidos um nível mais alto com um acordo de nível de serviço mais elevado (SLA). Esses locais poderiam ter as suas bases de dados colocadas em piscina com limites de recursos por base de dados mais elevados. Cada nível de serviço poderia ter uma atribuição de vendas por hora, com taxas adicionais cobradas por exceder a atribuição. Locais maiores que tenham explosões periódicas de vendas beneficiariam dos escalões mais altos, e os Bilhetes Wingtip podem rentabilizar o seu serviço de forma mais eficiente.

Entretanto, alguns clientes da Wingtip Tickets queixam-se de que lutam para vender bilhetes suficientes para justificar o custo do serviço. Talvez nestes conhecimentos haja uma oportunidade de impulsionar a venda de bilhetes para locais com baixo desempenho. As vendas mais elevadas aumentariam o valor percebido do serviço. Clique fact_Tickets à direita e selecione **Nova medida.** Introduza a seguinte expressão para a nova medida **denominada AverageTicketsSold:**

```sql
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selecione as seguintes opções de visualização para traçar a percentagem de bilhetes vendidos por cada local para determinar o seu sucesso relativo.

![AvgTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/AvgTicketsByVenues-DW.PNG)

O enredo acima mostra que, embora a maioria dos locais venda mais de 80% dos seus bilhetes, alguns estão lutando para preencher mais de metade dos seus lugares. Brinque com o Values Well para selecionar a percentagem máxima ou mínima de bilhetes vendidos para cada local.

## <a name="embedding-analytics-in-your-apps"></a>Incorporação de análises nas suas apps

Este tutorial tem-se centrado na análise de inquilinos cruzados usados para melhorar a compreensão do fornecedor de software dos seus inquilinos. A Analytics também pode fornecer informações aos _inquilinos,_ para ajudá-los a gerir os seus negócios de forma mais eficaz.

No exemplo dos Bilhetes Wingtip, descobriu anteriormente que as vendas de bilhetes tendem a seguir padrões previsíveis. Esta perspicácia pode ser usada para ajudar os locais com baixo desempenho a impulsionar a venda de bilhetes. Talvez haja uma oportunidade de utilizar técnicas de machine learning para prever a venda de bilhetes para eventos. Os efeitos das variações de preços também poderiam ser modelados, de modo a permitir a previsível o impacto da oferta de descontos. O Power BI Embedded poderia ser integrado numa aplicação de gestão de eventos para visualizar as vendas previstas, incluindo o impacto dos descontos no total de lugares vendidos e receitas em eventos de baixa venda. Com o Power BI Embedded, pode até integrar a aplicação efetiva do desconto aos preços dos bilhetes, mesmo na experiência de visualização.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> - Crie a loja de análise de inquilinos para carregamento.
> - Utilize a Azure Data Factory (ADF) para extrair dados de cada base de dados de inquilinos no armazém de dados analíticos.
> - Otimize os dados extraídos (reorganize-se num esquema estelar).
> - Consultar o armazém de dados de análise.
> - Use o Power BI para visualização de dados para destacar as tendências dos dados dos inquilinos e fazer recomendações para melhorias.

Parabéns!

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais adicionais que se baseiam na aplicação Wingtip SaaS](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).