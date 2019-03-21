---
title: Executar consultas de análise contra inquilino bases de dados com o Azure SQL Data Warehouse | Documentos da Microsoft
description: Consultas de análise de entre inquilinos usando dados extraídos da base de dados do Azure SQL, o SQL Data Warehouse, o Azure Data Factory ou o Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: a658e2fe32ec95dfabad54684a0c9095af7a341d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850297"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Explore a análise de SaaS com o SQL Database do Azure, o SQL Data Warehouse, o Data Factory e o Power BI

Neste tutorial, é necessário percorrer um cenário de análise de ponto-a-ponto. O cenário demonstra como a análise de dados de inquilino pode aumentar a produtividade dos fornecedores de software para tomar decisões inteligentes. Usando dados extraídos de cada base de dados de inquilinos, utilize o analytics para obter informações sobre o comportamento de inquilino, incluindo o uso do exemplo de aplicação Wingtip Tickets SaaS. Este cenário envolve três etapas: 

1.  **Extrair dados** cada inquilino da base de dados para um arquivo de análise, neste caso, um SQL Data Warehouse.
2.  **Otimizar os dados extraídos** para processamento de análise.
3.  Uso **Business Intelligence** ferramentas para desenhar as informações úteis, que podem orientar a tomada de decisões. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Crie o inquilino a análise armazenar para carregamento.
> - Utilize o Azure Data Factory (ADF) para extrair dados de cada base de dados do inquilino para o armazém de dados de análise.
> - Otimize os dados extraídos (reorganize num esquema de estrela).
> - Consulte o armazém de dados de análise.
> - Utilize o Power BI para visualização de dados para destacar tendências em dados de inquilino e fazer recomendação para melhorias.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Análise de dados de inquilino extraído

Aplicações SaaS contêm uma potencialmente grande quantidade de dados de inquilinos na nuvem. Estes dados podem fornecer uma origem avançada de informações sobre a operação e utilização da sua aplicação e o comportamento dos seus inquilinos. Estas informações podem ajudá-desenvolvimento de funcionalidade, usabilidade e a realizar outros investimentos na plataforma de aplicações e.

Acesso aos dados para todos os inquilinos é simples quando todos os dados são apenas uma base de dados do multi-inquilino. Mas o acesso é mais complexo quando distribuição à escala através de milhares de bases de dados. Uma forma de controle da complexidade é extrair os dados para uma base de dados de análise ou de um armazém de dados para consulta.

Este tutorial apresenta um cenário de análise de ponto-a-ponto para a aplicação Wingtip Tickets. Primeiro, [do Azure Data Factory (ADF)](../data-factory/introduction.md) é usada como a ferramenta de orquestração para extrair dados de vendas e relacionados de pedidos de suporte de cada base de dados do inquilino. Estes dados são carregados em tabelas de testes num repositório de análise. O arquivo de análise pode ser uma base de dados do SQL ou um SQL Data Warehouse. Este tutorial utiliza [o SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) como a análise de armazenar.

Em seguida, os dados extraídos é transformados e carregados para um conjunto de [esquema de estrela](https://www.wikipedia.org/wiki/Star_schema) tabelas. As tabelas são compostas por uma tabela de fatos central mais tabelas de dimensão relacionada:

- A tabela de fatos central no esquema de estrela contém dados de pedido de suporte.
- As tabelas de dimensões contêm dados sobre locais, eventos, os clientes e datas de compra.

Em conjunto a dimensão tabelas ativar eficiente processamento analítico e central. O esquema de estrela utilizado neste tutorial, é apresentado na imagem seguinte:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Por fim, as tabelas do esquema de estrela são consultadas. Resultados da consulta são exibidos visualmente com o Power BI para realçar as informações sobre o comportamento de inquilino e a sua utilização da aplicação. Com este esquema de estrela, execute consultas que expõem:

- Quem está comprando ingressos de qual local.
- Padrões e tendências na venda de bilhetes.
- A popularidade relativa de cada local.

Este tutorial fornece exemplos básicos de informações que podem ser compiladas a partir dos dados Wingtip Tickets. Compreender como cada local utiliza o serviço pode fazer com que o fornecedor de Wingtip Tickets pensar sobre os planos de serviço diferentes direcionados para os locais mais ou menos Active Directory, por exemplo. 

## <a name="setup"></a>Configurar

### <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> Este tutorial utiliza funcionalidades do Azure Data Factory, que estão atualmente em pré-visualização limitada (parametrização de serviço ligado). Se pretender realizar este tutorial, forneça o seu ID de subscrição [aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Nós lhe enviaremos uma mensagem de confirmação assim que a sua subscrição foi ativada.

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:
- A aplicação Wingtip Tickets SaaS da base de dados por inquilino é implementada. Para implementar em menos de cinco minutos, veja [implementar e explorar a aplicação Wingtip SaaS](saas-dbpertenant-get-started-deploy.md).
- Os scripts de Wingtip Tickets SaaS da base de dados por inquilino e o aplicativo [código-fonte](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) são transferidos a partir do GitHub. Ver instruções para download. Não se esqueça *desbloquear o ficheiro zip* antes de extrair o respetivo conteúdo.
- Power BI Desktop está instalado. [Transferir o Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- Provisionamento do lote de inquilinos adicionais, consulte a [ **aprovisionar inquilinos tutorial**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Criar dados para a demonstração

Este tutorial explora análises sobre os dados de vendas de pedido de suporte. Neste passo, vai gerar dados de pedido de suporte para todos os inquilinos. Num passo posterior, estes dados são extraídos para análise. _Certifique-se de que aprovisionou o lote de inquilinos_ (conforme descrito anteriormente), para que tenha dados suficientes para expor uma variedade de diferentes permissão padrões de compra.

1. No ISE do PowerShell, abra *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*e defina o valor seguinte:
    - **$DemoScenario** = **1** comprar bilhetes para eventos em todos os locais
2. Prima **F5** para executar o script e criar o histórico para todos os locais de compra de bilhetes. Com 20 inquilinos, o script gera dezenas de milhares de pedidos de suporte e pode demorar 10 minutos ou mais.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Implementar o SQL Data Warehouse, fábrica de dados e armazenamento de BLOBs 
Na aplicação Wingtip Tickets, os dados dos inquilinos de transacional são distribuídos por muitas bases de dados. O Azure Data Factory (ADF) é utilizado para orquestrar a extração, carregamento e transformação (ELT) destes dados para o armazém de dados. Para carregar dados para o SQL Data Warehouse com mais eficiência, o ADF extrai dados para ficheiros de blob intermediário e, em seguida, usa [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) para carregar os dados no armazém de dados.   

Neste passo, vai implementar os recursos adicionais que utilizou no tutorial: chamada de um SQL Data Warehouse _tenantanalytics_, uma fábrica de dados do Azure chamado _dbtodwload -\<utilizador\>_  , e uma conta de armazenamento do Azure designada _wingtipstaging\<usuário\>_. A conta de armazenamento é utilizada para armazenar temporariamente os ficheiros de dados extraídos como blobs de antes de serem carregados para o armazém de dados. Este passo também implementa o esquema do armazém de dados e define os pipelines do ADF que orquestrar o processo ELT.
1. No ISE do PowerShell, abra *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* e defina:
    - **$DemoScenario** = **2** implementar o armazém de dados de análise de inquilino, o armazenamento de BLOBs e o data factory 
1. Prima **F5** para executar o script de demonstração e implementar os recursos do Azure. 

Agora, reveja os recursos do Azure que implementou:
#### <a name="tenant-databases-and-analytics-store"></a>Armazenamento de bases de dados e análise de inquilino
Uso [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para ligar ao **tenants1-dpt -&lt;utilizador&gt;**  e **catálogo-dpt -&lt;utilizador&gt;**  servidores. Substitua &lt;utilizador&gt; com o valor que usou quando implementou a aplicação. Utilizar o início de sessão = *desenvolvedor* e a palavra-passe = *P\@ssword1*. Consulte a [introdutório](saas-dbpertenant-wingtip-app-overview.md) para obter mais orientações.

![Ligar ao servidor de base de dados SQL do SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

No Object Explorer:

1. Expanda a *tenants1-dpt -&lt;usuário&gt;*  server.
1. Expanda o nó de bases de dados e ver a lista de bases de dados do inquilino.
1. Expanda a *catálogo-dpt -&lt;usuário&gt;*  server.
1. Certifique-se de que vê a análise de arquivo que contém os seguintes objetos:
    1. Tabelas **raw_Tickets**, **raw_Customers**, **raw_Events** e **raw_Venues** manter não processados dados extraídos dos bancos de dados de inquilino.
    1. As tabelas do esquema de estrela são **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, e **dim_Dates** .
    1. O procedimento armazenado, **sp_transformExtractedData** é usado para transformar os dados e carregá-los para as tabelas do esquema de estrela.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Armazenamento de blobs
1. Na [Portal do Azure](https://ms.portal.azure.com), navegue para o grupo de recursos que utilizou para implantar o aplicativo. Certifique-se de que uma conta de armazenamento denominada **wingtipstaging\<usuário\>**  foi adicionado.

   ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Clique em **wingtipstaging\<usuário\>**  conta de armazenamento para explorar os objetos presentes.
1. Clique em **Blobs** mosaico
1. Clique em contentor **configfile**
1. Certifique-se de que **configfile** contém um ficheiro JSON chamado **TableConfig.json**. Este ficheiro contém os nomes de tabela de origem e de destino, os nomes das colunas e nome da coluna de controlador.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)
Na [Portal do Azure](https://ms.portal.azure.com) no grupo de recursos, certifique-se de que uma fábrica de dados do Azure chamado _dbtodwload -\<utilizador\>_  foi adicionado. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Essa seção explora a fábrica de dados criada. Siga os passos abaixo para iniciar a fábrica de dados:
1. No portal, clique na fábrica de dados chamada **dbtodwload -\<usuário\>**.
2. Clique em **criar e monitorizar** mosaico para iniciar o designer de Data Factory num separador à parte. 

## <a name="extract-load-and-transform-data"></a>Extrair, carga e transformar dados
O Azure Data Factory é utilizado para orquestrar a extração, carregamento e transformação de dados. Neste tutorial, extrair dados de quatro diferentes vistas SQL de cada uma das bases de dados do inquilino: **rawTickets**, **rawCustomers**, **rawEvents**, e  **rawVenues**. Estas vistas incluem foro Id, portanto, pode distinguir dados a partir de cada local no armazém de dados. Os dados são carregados em tabelas de teste correspondentes no armazém de dados: **raw_Tickets**, **raw_customers**, **raw_Events** e **raw_Venue**. Um procedimento armazenado, em seguida, transforma os dados não processados e preenche as tabelas do esquema de estrela: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** , e **dim_Dates**.

Na secção anterior, implementou e inicializado os recursos do Azure necessários, incluindo a fábrica de dados. A fábrica de dados implementados inclui os pipelines, conjuntos de dados, serviços ligados, etc., necessário para extrair, carregar e transformar os dados de inquilino. Vamos explorar esses objetos ainda mais e, em seguida, acionar o pipeline para mover dados de bases de dados do inquilino para o armazém de dados.

### <a name="data-factory-pipeline-overview"></a>Descrição geral de pipeline do Data factory
Essa seção explora os objetos criados na fábrica de dados. A figura a seguir descreve o fluxo de trabalho geral de pipeline do ADF utilizado neste tutorial. Se preferir explorar o pipeline mais tarde e ver os resultados em primeiro lugar, avance para a próxima seção **acionar o execução de pipeline**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Na página Descrição geral, mude para **autor** separador no painel esquerdo e observe que existem três [pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) e três [conjuntos de dados](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) criado.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

Os três pipelines aninhados são: SQLDBToDW DBCopy e TableCopy.

**O pipeline 1 - SQLDBToDW** procura os nomes das bases de dados de inquilino armazenados na base de dados de catálogo (nome da tabela: [__ShardManagement]. [ ShardsGlobal]) e cada base de dados do inquilino, executa o **DBCopy** pipeline. Após a conclusão, fornecido **sp_TransformExtractedData** esquema de procedimento armazenado, é executado. Este procedimento armazenado transforma os dados carregados em tabelas de testes e preenche as tabelas do esquema de estrela.

**O pipeline 2 - DBCopy** verifica os nomes das tabelas de origem e colunas a partir de um ficheiro de configuração armazenado no armazenamento de Blobs.  O **TableCopy** pipeline, em seguida, é executada para cada uma das quatro tabelas: TicketFacts, CustomerFacts, EventFacts e VenueFacts. O **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** atividade é executada em paralelo para 20 bases de dados. ADF permite um máximo de iterações de 20 loop para ser executadas em paralelo. Considere a criação de vários pipelines para obter mais bases de dados.    

**Pipeline de 3 - TableCopy** usa números de versão de linha na base de dados SQL (_rowversion_) para identificar as linhas que foi alteradas ou atualizadas. Esta atividade procura o início e a versão de linha de fim para extrair as linhas das tabelas de origem. O **CopyTracker** tabela armazenada em cada base de dados de inquilinos controla a última linha extraída de cada tabela de origem em cada execução. Linhas de novas ou alteradas são copiadas para as tabelas de teste correspondentes no armazém de dados: **raw_Tickets**, **raw_Customers**, **raw_Venues**, e **raw_ Eventos**. Por fim a última versão de linha é guardada na **CopyTracker** tabela para ser utilizado como a versão de linha inicial para a próxima extração. 

Existem também três parametrizados serviços ligados, essa ligação a fábrica de dados para a origem de bases de dados SQL, o SQL Data Warehouse de destino e o armazenamento de BLOBs intermediário. Na **autor** separador, clique em **ligações** para explorar os serviços ligados, conforme mostrado na imagem seguinte:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Correspondente para os três serviços ligados, existem três conjuntos de dados que se referem aos dados que utilize as atividades do pipeline como entradas ou saídas. Explore cada um dos conjuntos de dados para observar as ligações e os parâmetros utilizados. _AzureBlob_ aponta para o ficheiro de configuração que contém as tabelas de origem e de destino e colunas, bem como a coluna de controlador em cada origem.
  
### <a name="data-warehouse-pattern-overview"></a>Visão geral de padrão do armazém de dados
O SQL Data Warehouse é utilizado como o arquivo de análise para executar a agregação dos dados de inquilino. Neste exemplo, é utilizar o PolyBase para carregar dados para o armazém de dados SQL. Dados não processados são carregados em tabelas de testes que têm uma coluna de identidade para controlar as linhas que foram transformadas para as tabelas do esquema de estrela. A imagem seguinte mostra o padrão de carregamento: ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Lentamente tabelas de dimensões 1 do tipo de alteração de dimensão (SCD) são utilizadas neste exemplo. Cada dimensão tem uma chave de substituição definida usando uma coluna de identidade. Como melhor prática, a tabela de dimensão de data é preenchida previamente para economizar tempo. Para as outras tabelas de dimensões, uma criação de tabela como SELECT... Instrução de (CTAS) é usada para criar uma tabela temporária que contém as linhas de modificação e não modificado existentes, juntamente com as chaves de substituição. Isso é feito com IDENTITY_INSERT = ON. Novas linhas são inseridas na tabela com IDENTITY_INSERT = OFF. Para fácil reversão, a tabela de dimensão existente for renomeada e a tabela temporária foi mudada para se tornar a nova tabela de dimensão. Antes de cada execução, a tabela de dimensão antigo é eliminada.

Tabelas de dimensões são carregadas antes da tabela de fatos. Esse sequenciamento garante que cada evento que aconteça de fato, todas as dimensões referenciadas já existam. Como os fatos são carregados, a chave comercial para cada dimensão correspondente é correspondida e as correspondentes chaves de substituição são adicionadas ao fato de cada.

A etapa final da transformação elimina os dados de testes prontos para a próxima execução de pipeline.
   
### <a name="trigger-the-pipeline-run"></a>Acionar o execução de pipeline
Siga os passos abaixo para executar o completa extrair, transformação e carga pipeline para todas as bases de dados do inquilino:
1. Na **autor** separador da interface de usuário ADF, selecione **SQLDBToDW** pipeline no painel à esquerda.
1. Clique em **acionador** e o recebidos do menu pendente, clique em **acionar agora**. Esta ação executa o pipeline imediatamente. Num cenário de produção, definiria uma Cronograma para a execução de pipeline para atualizar os dados com base numa agenda.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. No **execução de Pipeline** página, clique em **concluir**.
 
### <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.
1. Na interface do usuário ADF, mude para o **Monitor** separador no menu à esquerda.
1. Clique em **Atualize** até que o estado do pipeline SQLDBToDW é **Succeeded**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Ligar ao armazém de dados com o SSMS e consultar as tabelas de esquema de estrela para verificar que os dados foram carregados nestas tabelas.

Depois de concluída a pipeline, a tabela de fatos contém dados de vendas de pedido de suporte para todos os locais e as tabelas de dimensões são preenchidas com os locais correspondentes, eventos e os clientes.

## <a name="data-exploration"></a>Exploração de dados

### <a name="visualize-tenant-data"></a>Visualizar dados de inquilino

Os dados no esquema de estrela fornecem todos os os pedido de suporte dados de vendas necessários para a sua análise. Visualizar dados graficamente torna mais fácil de ver as tendências em grandes conjuntos de dados. Nesta secção, vai utilizar **Power BI** para manipular e visualize os dados de inquilino no armazém de dados.

Utilize os seguintes passos para ligar ao Power BI e para importar as vistas que criou anteriormente:

1. Inicie o Power BI desktop.
2. No friso base, selecione **obter dados**e selecione **mais...** no menu.
3. Na **obter dados** janela, selecione **base de dados do Azure SQL**.
4. Na janela de início de sessão da base de dados, introduza o nome do servidor (**catálogo-dpt -&lt;usuário&gt;. database.windows.net**). Selecione **importação** para **modo de conectividade de dados**e, em seguida, clique em **OK**. 

    ![início de sessão-no-para-power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Selecione **base de dados** no painel esquerdo, em seguida, introduza o nome de utilizador = *programador*e introduza a palavra-passe = *P\@ssword1*. Clique em **Ligar**.  

    ![base de dados-início de sessão](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. Na **Navigator** painel, sob a base de dados de análise, selecione as tabelas do esquema de estrela: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** e **dim_Dates**. Em seguida, selecione **carga**. 

Parabéns! Carregado com êxito os dados para o Power BI. Agora, explore visualizações interessantes para obter informações sobre os seus inquilinos. Vamos examinar como a análise pode fornecer algumas recomendações orientadas por dados para a equipa de empresariais Wingtip Tickets. As recomendações podem ajudar a otimizar a experiência de modelo e o cliente de negócios.

Comece por analisar dados de vendas de permissão para ver a variação na utilização entre os locais. Selecione as opções disponíveis no Power BI para desenhar um gráfico de barras do número total de pedidos de suporte vendido por cada local. (Devido a variação aleatória no gerador de pedido de suporte, os resultados podem ser por diferentes.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

O desenho anterior confirma que o número de pedidos de suporte vendido por cada local varia. Locais vendem bilhetes mais estão a utilizar o serviço mais pesada do que locais vendem bilhetes menos. Pode haver uma oportunidade para ajustar a alocação de recursos de acordo com as necessidades de inquilino diferente.

Pode analisar mais aprofundadamente os dados para ver como as vendas de bilhetes variam ao longo do tempo. Selecione as opções mostradas na imagem seguinte no Power BI para desenhar o número total de pedidos de suporte vendido por dia durante um período de 60 dias.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

O gráfico anterior mostra esse pico de vendas de pedido de suporte para alguns locais. Esse aumento de reforçar a ideia de que alguns locais poderão estar a consumir recursos do sistema desproporcional. Até agora não há nenhum padrão óbvio quando ocorrem os picos.

Próxima vamos investigar o significado nos dias de hoje de venda de pico. Quando esses picos ocorrem depois de pedidos de suporte, aceda à venda? Para representar os pedidos de suporte vendidos por dia, selecione as opções mostradas na imagem seguinte no Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Este gráfico mostra que alguns locais vendem um grande número de pedidos de suporte no primeiro dia de venda. Assim que os pedidos de suporte aceda à venda nesses locais, parece haver apressem. Este fluxo de atividade por alguns locais pode afetar o serviço para os outros inquilinos.

Pode explorar os dados novamente para ver se este apressem é verdadeiro para todos os eventos hospedados por esses locais. Gráficos anteriores, vimos que a Contoso Concert Hall vende muitos pedidos de suporte e que Contoso também possui um pico no vendas de bilhetes em determinados dias. Experimente as opções do Power BI para desenhar as vendas de bilhetes cumulativa para Contoso Concert Hall, concentrando-se sobre as tendências de venda para cada um dos seus eventos. Todos os eventos seguem o mesmo padrão de venda? Tente produzir um desenho como o mostrado abaixo.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Essa plotagem de bilhetes cumulativa ao longo do tempo para Contoso Concert Hall para cada evento mostra que o apressem não acontece para todos os eventos. Experimente as opções de filtro para explorar tendências de venda para outros locais.

As informações sobre os padrões de venda de bilhetes podem levar a Wingtip Tickets para otimizar o seu modelo de negócio. Em vez de cobrança todos os inquilinos igualmente, talvez Wingtip deve apresentar o escalões de serviço com diferentes tamanhos de computação. Locais maiores que têm de vender bilhetes mais por dia podem ser oferecidos um escalão superior com um maior contrato de nível serviço (SLA). Esses locais podem ter seus bancos de dados colocados em conjunto com limites de recursos por base de dados mais elevados. Cada escalão de serviço pode ter uma alocação de vendas por hora, com taxas adicionais é cobradas que exceda a alocação. Locais maiores que têm picos periódicos de vendas seriam beneficiar dos escalões superiores e Wingtip Tickets pode monetize de forma mais eficiente seu serviço.

Enquanto isso, alguns clientes Wingtip Tickets reclamam que eles se para vender suficiente bilhetes para justificar o custo do serviço. Talvez dessas informações há uma oportunidade para aumentar as vendas de bilhetes para com um desempenho baixo locais. Vendas superior aumentará o valor percebido do serviço. Clique com o botão direito do rato em fact_Tickets e selecione **nova medida**. Introduza a seguinte expressão para a nova medida chamada **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selecione as seguintes opções de visualização para desenhar os tíquetes de percentagem vendidos por cada local para determinar seu sucesso relativo.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

O desenho acima mostra que, embora a maioria dos locais vendem mais de 80% dos seus pedidos de suporte, alguns estão com problemas preencher a mais de metade as vagas. Experimente os valores bem para selecionar o máxima ou mínima percentagem de pedidos de suporte vendido para cada local.

## <a name="embedding-analytics-in-your-apps"></a>Incorporar análises nas suas aplicações 
Neste tutorial tem se concentrado na análise entre inquilinos utilizado para melhorar a compreensão do fornecedor do software dos seus inquilinos. Analytics também pode fornecer informações para o _inquilinos_, para os ajudar a gerenciar seus negócios com mais eficiência propriamente ditas. 

No exemplo a Wingtip Tickets, anteriormente detetados que vendas de bilhetes tendem a seguir padrões previsíveis. Esta informação pode ser usada para ajudar a com um desempenho baixo vendas de bilhetes de aumento de locais. Talvez seja uma oportunidade de empregam técnicas de aprendizagem de máquina para prever vendas de bilhetes para eventos. Os efeitos das alterações de preços podem também ser modelados para permitir que o impacto da oferta de descontos a ser prevista. Power BI Embedded pode ser integrado num aplicativo de gerenciamento de eventos para visualizar as vendas previstas, incluindo o impacto de descontos em postos de trabalho total vendidos e a receita em eventos de baixa de vendas. Com o Power BI Embedded, pode até mesmo integrar, na verdade, aplicar o desconto para os preços de pedido de suporte, com o botão direito na experiência de visualização.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Implemente um SQL Data Warehouse preenchido com um esquema em estrela para análise de inquilinos.
> * Utilize o Azure Data Factory para extrair dados de cada base de dados do inquilino para o armazém de dados de análise.
> * Otimize os dados extraídos (reorganize num esquema de estrela).
> * Consulte o armazém de dados de análise. 
> * Utilize o Power BI para visualizar tendências em dados em todos os inquilinos.

Parabéns!

## <a name="additional-resources"></a>Recursos adicionais

- Adicionais [tutoriais que se baseiam na aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
