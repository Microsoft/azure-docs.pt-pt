---
title: Executar consultas de análise em bancos de dados de locatário
description: Consultas de análise entre locatários usando dados extraídos do banco de dados SQL do Azure, SQL Data Warehouse, Azure Data Factory ou Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 4791cd3a6b6f72c5d9ee4ca828d66b0d361f356c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816773"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Explore a análise de SaaS com o banco de dados SQL do Azure, SQL Data Warehouse, Data Factory e Power BI

Neste tutorial, você percorre um cenário de análise de ponta a ponta. O cenário demonstra como a análise em dados de locatário pode capacitar os fornecedores de software a tomar decisões inteligentes. Usando dados extraídos de cada banco de dado de locatário, você usa a análise para obter informações sobre o comportamento do locatário, incluindo o uso do aplicativo SaaS Wingtip tickets de exemplo. Esse cenário envolve três etapas: 

1.  **Extrair dados** de cada banco de dado de locatário para um repositório de análise, nesse caso, um SQL data warehouse.
2.  **Otimize os dados extraídos** para processamento de análise.
3.  Use as ferramentas de **Business Intelligence** para extrair informações úteis, que podem orientar a tomada de decisões. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Crie o repositório de análise de locatário para carregar.
> - Use Azure Data Factory (ADF) para extrair dados de cada banco de dado de locatário para o data warehouse de análise.
> - Otimizar os dados extraídos (reorganizar em um esquema em estrela).
> - Consulte o data warehouse de análise.
> - Use Power BI para visualização de dados para realçar tendências em dados de locatário e fazer recomendações para melhorias.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Análise sobre dados extraídos de locatário

Os aplicativos SaaS mantêm uma grande quantidade de dados de locatário potencialmente na nuvem. Esses dados podem fornecer uma fonte avançada de informações sobre a operação e o uso do seu aplicativo e o comportamento de seus locatários. Essas informações podem guiar o desenvolvimento de recursos, melhorias de usabilidade e outros investimentos nos aplicativos e na plataforma.

O acesso aos dados para todos os locatários é simples quando todos os dados estão em apenas um banco de dado de vários locatários. Mas o acesso é mais complexo quando distribuído em grande escala em milhares de bancos de dados. Uma maneira de controlar a complexidade é extrair os dados para um banco de dado de análise ou um data warehouse para consulta.

Este tutorial apresenta um cenário de análise de ponta a ponta para o aplicativo Wingtip tickets. Primeiro, [Azure data Factory (ADF)](../data-factory/introduction.md) é usado como a ferramenta de orquestração para extrair as vendas de ingressos e os dados relacionados de cada banco de dado de locatário. Esses dados são carregados em tabelas de preparo em um repositório de análise. O repositório de análise pode ser um banco de dados SQL ou um SQL Data Warehouse. Este tutorial usa [SQL data warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) como o repositório de análise.

Em seguida, os dados extraídos são transformados e carregados em um conjunto de tabelas de [esquema em estrela](https://www.wikipedia.org/wiki/Star_schema) . As tabelas consistem em uma tabela de fatos central mais tabelas de dimensões relacionadas:

- A tabela de fatos central no esquema em estrela contém dados de tíquete.
- As tabelas de dimensões contêm dados sobre locais, eventos, clientes e datas de compra.

Juntas, as tabelas central e de dimensão permitem um processamento analítico eficiente. O esquema em estrela usado neste tutorial é exibido na imagem a seguir:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Por fim, as tabelas de esquema em estrela são consultadas. Os resultados da consulta são exibidos visualmente usando Power BI para realçar informações sobre o comportamento do locatário e seu uso do aplicativo. Com esse esquema em estrela, você executa consultas que expõem:

- Quem está comprando tíquetes e de qual local.
- Padrões e tendências na venda de tíquetes.
- A popularidade relativa de cada local.

Este tutorial fornece exemplos básicos de informações que podem ser obtidas dos dados da Wingtip tickets. Entender como cada local usa o serviço pode fazer com que o fornecedor da Wingtip tickets pense em diferentes planos de serviço direcionados a locais mais ou menos ativos, por exemplo. 

## <a name="setup"></a>Configurar

### <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> Este tutorial usa recursos do Azure Data Factory que estão atualmente em uma versão prévia limitada (parametrização de serviço vinculado). Se você quiser fazer este tutorial, forneça sua ID de assinatura [aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Nós lhe enviaremos uma confirmação assim que sua assinatura tiver sido habilitada.

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:
- O aplicativo de banco de dados por locatário SaaS Wingtip tickets foi implantado. Para implantar em menos de cinco minutos, consulte [implantar e explorar o aplicativo SaaS Wingtip](saas-dbpertenant-get-started-deploy.md).
- Os scripts de banco de dados por locatário do Wingtip tickets SaaS e o [código-fonte](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) do aplicativo são baixados do github. Consulte as instruções de download. Certifique-se de *desbloquear o arquivo zip* antes de extrair seu conteúdo.
- O Power BI Desktop está instalado. [Baixar Power bi desktop](https://powerbi.microsoft.com/downloads/).
- O lote de locatários adicionais foi provisionado, consulte o [**tutorial provisionar locatários**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Criar dados para a demonstração

Este tutorial explora a análise sobre dados de vendas de tíquetes. Nesta etapa, você gera dados de tíquete para todos os locatários. Em uma etapa posterior, esses dados são extraídos para análise. _Certifique-se de que você provisionou o lote de locatários_ (conforme descrito anteriormente) para que você tenha dados suficientes para expor um intervalo de diferentes padrões de compra de tíquetes.

1. No ISE do PowerShell, abra *. ..\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*e defina o seguinte valor:
    - **$DemoScenario** = **1** comprar tíquetes para eventos em todos os locais
2. Pressione **F5** para executar o script e criar o histórico de compras de tíquetes para todos os locais. Com 20 locatários, o script gera dezenas de milhares de tíquetes e pode levar 10 minutos ou mais.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Implantar SQL Data Warehouse, Data Factory e armazenamento de BLOBs 
No aplicativo Wingtip tickets, os dados transacionais dos locatários são distribuídos por vários bancos de dados. Azure Data Factory (ADF) é usado para orquestrar o ELT (extração, carregamento e transformação) desses dados no data warehouse. Para carregar dados em SQL Data Warehouse com mais eficiência, o ADF extrai dados em arquivos de blob intermediários e, em seguida, usa o [polybase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) para carregar os dados no data warehouse.   

Nesta etapa, você implantará os recursos adicionais usados no tutorial: um SQL Data Warehouse chamado _tenantanalytics_, um Azure data Factory chamado _de\>de usuário dbtodwload-\<_ e uma conta de armazenamento do Azure chamada _wingtipstaging\<\>de usuário_ . A conta de armazenamento é usada para manter temporariamente os arquivos de dados extraídos como BLOBs antes que eles sejam carregados no data warehouse. Esta etapa também implanta o esquema de data warehouse e define os pipelines do ADF que orquestram o processo ELT.
1. No ISE do PowerShell, abra *. ..\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* e defina:
    - **$DemoScenario** = **2** implante data warehouse de análise de locatários, armazenamento de BLOBs e data Factory 
1. Pressione **F5** para executar o script de demonstração e implantar os recursos do Azure. 

Agora, examine os recursos do Azure que você implantou:
#### <a name="tenant-databases-and-analytics-store"></a>Bancos de dados de locatário e repositório de análise
Use o [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para se conectar ao **usuário do tenants1-dpt-&lt;&gt;** e aos servidores de **&gt;do usuário do DPT&lt;de catálogo** . Substitua &lt;&gt; de usuário pelo valor usado quando você implantou o aplicativo. Use login = *Developer* e password = *P\@ssword1*. Consulte o [tutorial introdutório](saas-dbpertenant-wingtip-app-overview.md) para obter mais diretrizes.

![Conectar-se ao servidor do banco de dados SQL do SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

No Pesquisador de objetos:

1. Expanda o servidor de *&gt;de usuário tenants1-DPT-&lt;* .
1. Expanda o nó bancos de dados e veja a lista de bancos de dados de locatário.
1. Expanda o servidor de *&gt;de usuário DPT-&lt;* .
1. Verifique se você vê o repositório de análise que contém os seguintes objetos:
    1. As tabelas **raw_Tickets**, **raw_Customers**, **raw_Events** e **raw_Venues** retêm dados brutos extraidos dos bancos de dado de locatário.
    1. As tabelas de esquema em estrela são **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**e **dim_Dates**.
    1. O procedimento armazenado, **sp_transformExtractedData** é usado para transformar os dados e carregá-los nas tabelas de esquema em estrela.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Armazenamento de blobs
1. No [portal do Azure](https://ms.portal.azure.com), navegue até o grupo de recursos que você usou para implantar o aplicativo. Verifique se uma conta de armazenamento chamada **wingtipstaging\<usuário\>** foi adicionada.

   ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Clique em **wingtipstaging\<usuário\>** conta de armazenamento para explorar os objetos presentes.
1. Clique no bloco **BLOBs**
1. Clique no **arquivo ConfigFile**
1. Verifique se **ConfigFile** contém um arquivo JSON chamado **TableConfig. JSON**. Esse arquivo contém os nomes de tabela de origem e de destino, nomes de coluna e nome de coluna do rastreador.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)
No [portal do Azure](https://ms.portal.azure.com) , no grupo de recursos, verifique se um Azure Data Factory chamado _\<de usuário dbtodwload\>_ foi adicionado. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Esta seção explora o data factory criado. Siga as etapas abaixo para iniciar o data factory:
1. No portal, clique no data factory chamado **usuário do dbtodwload\<\>** .
2. Clique em **criar &** bloco de monitor para iniciar o designer de data Factory em uma guia separada. 

## <a name="extract-load-and-transform-data"></a>Extrair, carregar e transformar dados
Azure Data Factory é usado para orquestrar extração, carregamento e transformação de dados. Neste tutorial, você extrai dados de quatro exibições SQL diferentes de cada um dos bancos de dado de locatário: **rawTickets**, **rawCustomers**, **rawEvents**e **rawVenues**. Esses modos de exibição incluem a ID de local, para que você possa discriminar várias dados de cada local na data warehouse. Os dados são carregados em tabelas de preparo correspondentes no data warehouse: **raw_Tickets**, **raw_customers**, **raw_Events** e **raw_Venue**. Em seguida, um procedimento armazenado transforma os dados brutos e popula as tabelas de esquema em estrela: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**e **dim_Dates**.

Na seção anterior, você implantou e inicializou os recursos do Azure necessários, incluindo o data factory. O data factory implantado inclui os pipelines, conjuntos de dados, serviços vinculados, etc., necessários para extrair, carregar e transformar os dados do locatário. Vamos explorar mais esses objetos e, em seguida, disparar o pipeline para mover dados de bancos de dado de locatário para o data warehouse.

### <a name="data-factory-pipeline-overview"></a>Visão geral do pipeline do data Factory
Esta seção explora os objetos criados no data factory. A figura a seguir descreve o fluxo de trabalho geral do pipeline do ADF usado neste tutorial. Se você preferir explorar o pipeline posteriormente e ver os resultados primeiro, pule para a próxima seção **disparar a execução do pipeline**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Na página Visão geral, alterne para a guia **autor** no painel esquerdo e observe que há três [pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) e três [conjuntos de valores](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) criados.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

Os três pipelines aninhados são: SQLDBToDW, DBCopy e TableCopy.

**Pipeline 1-SQLDBToDW** pesquisa os nomes dos bancos de dados de locatário armazenados no banco de dados do catálogo (nome da tabela: [__ShardManagement]. [ ShardsGlobal]) e para cada banco de dados de locatário, o executa o pipeline **DBCopy** . Após a conclusão, o esquema de procedimento armazenado **sp_TransformExtractedData** fornecido, é executado. Esse procedimento armazenado transforma os dados carregados nas tabelas de preparo e popula as tabelas de esquema em estrela.

**Pipeline 2 – DBCopy** pesquisa os nomes das tabelas de origem e colunas de um arquivo de configuração armazenado no armazenamento de BLOBs.  Em seguida, o pipeline **TableCopy** é executado para cada uma das quatro tabelas: TicketFacts, CustomerFacts, EventFacts e VenueFacts. A atividade **[foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** é executada em paralelo para todos os 20 bancos de dados. O ADF permite que um máximo de 20 iterações de loop seja executado em paralelo. Considere a criação de vários pipelines para mais bancos de dados.    

**Pipeline 3-o TableCopy** usa números de versão de linha no_banco de dados SQL (rowgroup_) para identificar linhas que foram alteradas ou atualizadas. Essa atividade pesquisa o início e a versão de linha final para extrair linhas das tabelas de origem. A tabela **CopyTracker** armazenada em cada banco de dados de locatário rastreia a última linha extraída de cada tabela de origem em cada execução. Linhas novas ou alteradas são copiadas para as tabelas de preparo correspondentes no data warehouse: **raw_Tickets**, **raw_Customers**, **raw_Venues**e **raw_Events**. Por fim, a última versão de linha é salva na tabela **CopyTracker** para ser usada como a versão de linha inicial para a próxima extração. 

Também há três serviços vinculados com parâmetros que vinculam o data factory aos bancos de dados SQL de origem, o SQL Data Warehouse de destino e o armazenamento de blob intermediário. Na guia **autor** , clique em **conexões** para explorar os serviços vinculados, conforme mostrado na imagem a seguir:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Correspondendo aos três serviços vinculados, há três conjuntos de dados que se referem ao que você usa nas atividades do pipeline como entradas ou saídas. Explore cada um dos conjuntos de valores para observar as conexões e os parâmetros usados. _AzureBlob_ aponta para o arquivo de configuração que contém tabelas e colunas de origem e de destino, bem como a coluna de rastreador em cada fonte.
  
### <a name="data-warehouse-pattern-overview"></a>Visão geral do padrão de data warehouse
SQL Data Warehouse é usado como o repositório de análise para executar a agregação nos dados do locatário. Neste exemplo, o polybase é usado para carregar dados no SQL data warehouse. Os dados brutos são carregados em tabelas de preparo que têm uma coluna de identidade para manter o controle das linhas que foram transformadas nas tabelas de esquema em estrela. A imagem a seguir mostra o padrão de carregamento: ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

As tabelas de dimensões da SCD (dimensão de alteração lenta) tipo 1 são usadas neste exemplo. Cada dimensão tem uma chave substituta definida usando uma coluna de identidade. Como prática recomendada, a tabela de dimensão de data é preenchida previamente para economizar tempo. Para as outras tabelas de dimensões, uma CREATE TABLE como SELECT... (CTAS) é usada para criar uma tabela temporária contendo as linhas existentes modificadas e não modificadas, juntamente com as chaves substitutas. Isso é feito com IDENTITY_INSERT = ON. As novas linhas são inseridas na tabela com IDENTITY_INSERT = OFF. Para reversão fácil, a tabela de dimensões existente é renomeada e a tabela temporária é renomeada para se tornar a nova tabela de dimensões. Antes de cada execução, a tabela de dimensões antiga é excluída.

As tabelas de dimensões são carregadas antes da tabela de fatos. Esse sequenciamento garante que, para cada fato de verdade, todas as dimensões referenciadas já existam. Conforme os fatos são carregados, a chave de negócio de cada dimensão correspondente é correspondida e as chaves substitutas correspondentes são adicionadas a cada fato.

A etapa final da transformação exclui os dados de preparo prontos para a próxima execução do pipeline.
   
### <a name="trigger-the-pipeline-run"></a>Disparar a execução do pipeline
Siga as etapas abaixo para executar o pipeline de extração, carregamento e transformação completo para todos os bancos de dados de locatário:
1. Na guia **autor** da interface do usuário do ADF, selecione pipeline **SQLDBToDW** no painel esquerdo.
1. Clique em **gatilho** e, no menu suspenso, clique em **disparar agora**. Essa ação executa o pipeline imediatamente. Em um cenário de produção, você definiria um timetable para a execução do pipeline para atualizar os dados em uma agenda.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. Na página **execução do pipeline** , clique em **concluir**.
 
### <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.
1. Na interface do usuário do ADF, alterne para a guia **Monitor** no menu à esquerda.
1. Clique em **Atualizar** até que o status do pipeline de SQLDBToDW seja **bem-sucedido**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Conecte-se ao data warehouse com o SSMS e consulte as tabelas de esquema em estrela para verificar se os dados foram carregados nessas tabelas.

Depois que o pipeline for concluído, a tabela de fatos conterá dados de vendas de ingressos para todos os locais e as tabelas de dimensões serão preenchidas com os locais, eventos e clientes correspondentes.

## <a name="data-exploration"></a>Exploração de dados

### <a name="visualize-tenant-data"></a>Visualizar dados de locatário

Os dados no esquema em estrela fornecem todos os dados de vendas de tíquetes necessários para sua análise. A visualização de dados graficamente torna mais fácil a visualização de tendências em grandes conjuntos de dados. Nesta seção, você usará **Power bi** para manipular e visualizar os dados do locatário no data warehouse.

Use as etapas a seguir para se conectar ao Power BI e importar os modos de exibição criados anteriormente:

1. Inicie o Power BI desktop.
2. Na faixa de opções página inicial, selecione **obter dados**e selecione **mais...** no menu.
3. Na janela **obter dados** , selecione **Azure SQL Database**.
4. Na janela de logon do banco de dados, insira o nome do servidor (**Catalog-DPT-&lt;usuário&gt;. Database.Windows.net**). Selecione **importar** para o **modo de conectividade de dados**e clique em **OK**. 

    ![entrar em energia-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Selecione **banco de dados** no painel esquerdo, digite user name = *Developer*e digite password = *P\@ssword1*. Clique em **Ligar**.  

    ![banco de dados-entrada](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. No painel **navegador** , no banco de dados de análise, selecione as tabelas de esquema em estrela: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** e **dim_Dates**. Em seguida, selecione **carregar**. 

Parabéns! Você carregou os dados com êxito em Power BI. Agora Explore visualizações interessantes para obter informações sobre seus locatários. Vamos examinar como a análise pode fornecer algumas recomendações controladas por dados para a equipe de negócios da Wingtip tickets. As recomendações podem ajudar a otimizar o modelo de negócios e a experiência do cliente.

Comece analisando dados de vendas de tíquetes para ver a variação no uso entre os locais. Selecione as opções mostradas em Power BI para plotar um gráfico de barras do número total de tíquetes vendidos por cada local. (Devido à variação aleatória no gerador de tíquetes, os resultados podem ser diferentes.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

A plotagem anterior confirma que o número de tíquetes vendidos por cada local varia. Os locais que vendem mais tíquetes estão usando seu serviço mais fortemente do que os locais que vendem menos tíquetes. Pode haver uma oportunidade para ajustar a alocação de recursos de acordo com as diferentes necessidades de locatário.

Você pode analisar os dados mais detalhadamente para ver como as vendas de tíquetes variam ao longo do tempo. Selecione as opções mostradas na imagem a seguir em Power BI para plotar o número total de tíquetes vendidos todos os dias por um período de 60 dias.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

O gráfico anterior mostra o aumento de vendas de tíquetes para alguns locais. Esses picos reforçam a ideia de que alguns locais podem consumir recursos do sistema desproporcionalmente. Até agora, não há nenhum padrão óbvio no momento em que os picos ocorrem.

Em seguida, vamos investigar o significado desses dias de venda de pico. Quando esses picos ocorrem depois que os tíquetes passam pela venda? Para plotar os tíquetes vendidos por dia, selecione as opções mostradas na imagem a seguir em Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Essa plotagem mostra que alguns locais vendem grandes números de tíquetes no primeiro dia da venda. Assim que os tíquetes passam pela venda nesses locais, parece haver uma urgência de Mad. Essa intermitência de atividade por alguns locais pode afetar o serviço para outros locatários.

Você pode analisar os dados novamente para ver se essa urgência é verdadeira para todos os eventos hospedados por esses locais. Nos gráficos anteriores, você viu que a contoso Concert Hall vende muitos tíquetes e que a contoso também tem um pico de vendas de ingressos em determinados dias. Experimente as opções de Power BI para plotar vendas de tíquetes cumulativos para o contoso Concert Hall, concentrando-se em tendências de venda para cada um de seus eventos. Todos os eventos seguem o mesmo padrão de venda? Tente produzir uma plotagem como a abaixo.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Esse gráfico de vendas de tíquete cumulativo ao longo do tempo para o contoso Concert Hall para cada evento mostra que a urgência de Mad não acontece para todos os eventos. Jogue com as opções de filtro para explorar as tendências de venda de outros locais.

As informações sobre os padrões de venda de ingressos podem liderar os tíquetes Wingtip para otimizar seu modelo de negócios. Em vez de carregar todos os locatários igualmente, talvez a Wingtip deva introduzir camadas de serviço com diferentes tamanhos de computação. Locais maiores que precisam vender mais tíquetes por dia podem receber uma camada mais alta com um SLA (contrato de nível de serviço) mais alto. Esses locais podem ter seus bancos de dados colocados no pool com limites de recursos de banco de dados maiores. Cada camada de serviço pode ter uma alocação de vendas por hora, com taxas adicionais cobradas para exceder a alocação. Os locais maiores que têm picos de vendas periódicos se beneficiarão das camadas mais altas, e os tíquetes Wingtip podem monetizarr seu serviço com mais eficiência.

Enquanto isso, alguns clientes da Wingtip tickets reclamam que eles lutam para vender tíquetes suficientes para justificar o custo do serviço. Talvez, nessas informações, haja uma oportunidade de impulsionar as vendas de ingressos para a execução de locais. As vendas mais altas aumentarão o valor percebido do serviço. Clique com o botão direito do mouse em fact_Tickets e selecione **nova medida**. Insira a seguinte expressão para a nova medida chamada **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selecione as opções de visualização a seguir para plotar os tíquetes de porcentagem vendidos por cada local para determinar seu sucesso relativo.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

A plotagem acima mostra que, embora a maioria dos locais venda mais de 80% de seus tíquetes, algumas estão lutando para preencher mais da metade de suas estações. Experimente com os valores bem para selecionar o percentual máximo ou mínimo de tíquetes vendidos para cada local.

## <a name="embedding-analytics-in-your-apps"></a>Inserindo análises em seus aplicativos 
Este tutorial se concentrou na análise entre locatários usada para melhorar a compreensão do fornecedor de software de seus locatários. A análise também pode fornecer informações aos _locatários_para ajudá-los a gerenciar seus negócios com mais eficácia. 

No exemplo de tíquetes Wingtip, você descobriu anteriormente que as vendas de ingressos tendem a seguir padrões previsíveis. Essa percepção pode ser usada para ajudar a aumentar os locais de vendas de tíquetes. Talvez haja uma oportunidade de empregar técnicas de aprendizado de máquina para prever as vendas de ingressos para eventos. Os efeitos das alterações de preço também podem ser modelados para permitir o impacto de oferecer descontos a serem previstos. Power BI Embedded pode ser integrado a um aplicativo de gerenciamento de eventos para visualizar as vendas previstas, incluindo o impacto de descontos no total de estações vendidas e receita em eventos de vendas baixas. Com Power BI Embedded, você pode até mesmo integrar realmente a aplicação do desconto aos preços do tíquete, diretamente na experiência de visualização.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Implante um SQL Data Warehouse populado com um esquema em estrela para análise de locatário.
> * Use Azure Data Factory para extrair dados de cada banco de dado de locatário para o data warehouse de análise.
> * Otimizar os dados extraídos (reorganizar em um esquema em estrela).
> * Consulte o data warehouse de análise. 
> * Use Power BI para visualizar tendências em dados em todos os locatários.

Parabéns!

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais adicionais que se baseiam no aplicativo SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
