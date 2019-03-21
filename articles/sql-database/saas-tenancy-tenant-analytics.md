---
title: Executar análises de entre inquilinos usando dados extraídos | Documentos da Microsoft
description: Consultas de análise de entre inquilinos usando dados extraídos a partir de várias bases de dados de base de dados do Azure SQL num aplicativo de inquilino único.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 9f88314bbf507dc3b4e282532acfa079c2a63cce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890547"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>Análise de entre inquilinos com extraídos dados - aplicação de inquilino único
 
Neste tutorial, é necessário percorrer um cenário de análise completa para uma implementação de inquilino único. O cenário demonstra como a análise pode ativar as empresas a tomarem decisões inteligentes. Usando dados extraídos de cada base de dados de inquilinos, utilize o analytics para obter informações sobre o comportamento de inquilino, incluindo o uso do exemplo de aplicação Wingtip Tickets SaaS. Este cenário envolve três etapas: 

1.  **Extrair** dados de cada base de dados de inquilinos e **carga** para um arquivo de análise.
2.  **Transformar os dados extraídos** para processamento de análise.
3.  Uso **inteligência comercial** ferramentas para desenhar as informações úteis, que podem orientar a tomada de decisões. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Crie o inquilino análise armazenar para extrair os dados.
> - Utilize as tarefas elásticas para extrair dados de cada base de dados do inquilino para o arquivo de análise.
> - Otimize os dados extraídos (reorganize num esquema de estrela).
> - Consulte a base de dados de análise.
> - Utilize o Power BI para visualização de dados para destacar tendências em dados de inquilino e fazer recomendação para melhorias.

![architectureOverView](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Padrão de análise de inquilino offline

Aplicações SaaS multi-inquilino normalmente têm uma grande quantidade de dados armazenados na cloud do inquilino. Estes dados disponibilizam uma origem avançada de informações sobre a operação e utilização da sua aplicação e o comportamento dos seus inquilinos. Estas informações podem ajudá-desenvolvimento de funcionalidade, usabilidade e a outros investimentos na aplicação e na plataforma.

Aceder a dados para todos os inquilinos é simples quando todos os dados são apenas uma base de dados do multi-inquilino. Mas o acesso é mais complexo quando distribuição à escala através de potencialmente milhares de bases de dados. É uma forma domesticar a complexidade e para minimizar o impacto de consultas de análises em dados transacionais extrair dados para um armazém de base de dados ou dados de análise do objetivo projetado.

Este tutorial apresenta um cenário de completas de análise para a aplicação Wingtip Tickets SaaS. Primeiro, *tarefas elásticas* é utilizado para extrair dados de cada base de dados de inquilinos e carregá-los para tabelas num repositório de análise de testes. O arquivo de análise pode ser uma base de dados do SQL ou um SQL Data Warehouse. Para extração de dados em grande escala [do Azure Data Factory](../data-factory/introduction.md) é recomendado.

Em seguida, os dados agregados são transformados num conjunto de [esquema de estrela](https://www.wikipedia.org/wiki/Star_schema) tabelas. As tabelas são compostos por uma tabela de fatos central mais tabelas de dimensão relacionada.  Para a Wingtip Tickets:

- A tabela de fatos central no esquema de estrela contém dados de pedido de suporte.
- As tabelas de dimensões descrevem locais, eventos, os clientes e datas de compra.

Em conjunto, as tabelas de factos e dimensões centrais permitem processamento analítico eficiente. O esquema de estrela utilizado neste tutorial é mostrado na imagem seguinte:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/StarSchema.png)

Por fim, o arquivo de análise é consultado com **PowerBI** para realçar as informações sobre o comportamento de inquilino e a sua utilização da aplicação Wingtip Tickets. Executar consultas que:
 
- Mostrar a popularidade relativa de cada local
- Realçar os padrões de vendas de bilhetes para eventos diferentes
- Mostrar o sucesso relativo de diferentes locais em vender os seus eventos

Compreender como cada inquilino está a utilizar o serviço é utilizado para explorar as opções para o serviço de monetizando e melhorar o serviço para ajudar os inquilinos a terem mais êxito. Este tutorial fornece exemplos básicos dos tipos de informações que podem ser compiladas a partir dos dados de inquilino.

## <a name="setup"></a>Configurar

### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

- A aplicação Wingtip Tickets SaaS da base de dados por inquilino é implementada. Para implementar em menos de cinco minutos, veja [implementar e explorar a aplicação Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)
- Os scripts de Wingtip Tickets SaaS da base de dados por inquilino e o aplicativo [código-fonte](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) são transferidos a partir do GitHub. Ver instruções para download. Não se esqueça *desbloquear o ficheiro zip* antes de extrair o respetivo conteúdo. Veja a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip Tickets SaaS de desbloqueio.
- Power BI Desktop está instalado. [Transferir o Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Provisionamento do lote de inquilinos adicionais, consulte a [ **aprovisionar inquilinos tutorial**](saas-dbpertenant-provision-and-catalog.md).
- Uma conta de tarefa e a base de dados de conta de tarefa tem sido criadas. Consulte as etapas apropriadas no [ **tutorial de gestão de esquema**](saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Criar dados para a demonstração

Neste tutorial, a análise é executada nos dados de vendas de pedido de suporte. O passo atual, vai gerar dados de pedido de suporte para todos os inquilinos.  Mais tarde, estes dados são extraídos para análise. *Certifique-se de ter aprovisionado o lote de inquilinos, tal como descrito anteriormente, para que tenha uma quantidade significativa de dados*. Uma suficientemente grande quantidade de dados pode expor uma variedade de diferentes padrões de compra de bilhetes.

1. No ISE do PowerShell, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*e defina o valor seguinte:
    - **$DemoScenario** = **1** comprar bilhetes para eventos em todos os locais
2. Prima **F5** para executar o script e criar o histórico para cada evento em cada local de compra de bilhetes.  O script é executado durante vários minutos gerar dezenas de milhares de pedidos de suporte.

### <a name="deploy-the-analytics-store"></a>Implementar o armazenamento de análise
Muitas vezes há diversos bancos de dados transacionais que em conjunto contêm todos os dados de inquilino. Tem de agregar os dados de inquilino de muitas bases de dados transacionais no armazenamento de uma análise. A agregação permite que uma consulta eficiente dos dados. Neste tutorial, uma base de dados do SQL Database do Azure é utilizado para armazenar os dados agregados.

Os passos seguintes, vai implementar o armazenamento de análise, o que é chamado **tenantanalytics**. Também implementa tabelas predefinidas que são preenchidas mais tarde no tutorial:
1. No ISE do PowerShell, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Defina a variável de $DemoScenario no script para corresponder à sua escolha do arquivo de análise de:
    - Para utilizar a base de dados SQL sem o arquivo de colunas, defina **$DemoScenario** = **2**
    - Para utilizar a base de dados SQL com o arquivo de colunas, defina **$DemoScenario** = **3**  
3. Prima **F5** para executar o script de demonstração (que chama o *implementar TenantAnalytics<XX>. ps1* script) que cria o arquivo de análise de inquilino. 

Agora que implementou a aplicação e preenchido com dados interessantes de inquilino, utilize [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) liguem **tenants1-dpt -&lt;utilizador&gt;**  e **catálogo-dpt -&lt;usuário&gt;**  servidores com início de sessão = *desenvolvedor*, palavra-passe = *P\@ssword1*. Consulte a [introdutório](saas-dbpertenant-wingtip-app-overview.md) para obter mais orientações.

![architectureOverView](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

No Object Explorer, execute os seguintes passos:

1. Expanda a *tenants1-dpt -&lt;usuário&gt;*  server.
2. Expanda o nó de bases de dados e ver a lista de bases de dados do inquilino.
3. Expanda a *catálogo-dpt -&lt;usuário&gt;*  server.
4. Certifique-se de que vê o armazenamento de análise e a base de dados jobaccount.

Consulte os seguintes itens de base de dados no Explorador de objeto do SSMS ao expandir o nó do arquivo de análise:

- Tabelas **TicketsRawData** e **EventsRawData** manter não processados dados extraídos dos bancos de dados de inquilino.
- As tabelas do esquema de estrela são **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, e **dim_Dates** .
- O procedimento armazenado é utilizado para preencher as tabelas do esquema de estrela das tabelas de dados não processados.

![architectureOverView](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extração de dados 

### <a name="create-target-groups"></a>Criar grupos de destino 

Antes de continuar, certifique-se de que implementou a base de dados do conta e jobaccount tarefa. O próximo conjunto de passos, as tarefas elásticas é utilizada para extrair dados de cada base de dados de inquilinos e para armazenar os dados no arquivo de análise. Em seguida, a segunda tarefa distribui os dados e os armazena em tabelas no esquema de estrela. Estas duas tarefas são executadas em relação a dois grupos de destino diferente, ou seja **TenantGroup** e **AnalyticsGroup**. A tarefa de extração é executada relativamente a TenantGroup, que contém todas as bases de dados de inquilino. A tarefa shredding for executado relativamente a AnalyticsGroup, que contém apenas o arquivo de análise. Crie os grupos de destino, utilize os seguintes passos:

1. No SSMS, ligue para o **jobaccount** base de dados no catálogo-dpt -&lt;utilizador&gt;.
2. No SSMS, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Modificar a @User variável na parte superior do script, substituindo <User> com o valor de utilizador que usou quando implementou a aplicação Wingtip SaaS.
4. Prima **F5** para executar o script que cria os grupos de destino de duas.

### <a name="extract-raw-data-from-all-tenants"></a>Extrair dados não processados de todos os inquilinos

As modificações de dados extensa podem ocorrer com mais frequência para *pedido de suporte e de clientes* dados do que para *eventos e o foro* dados. Por conseguinte, considere a extração de dados de pedido de suporte e de clientes em separado e com mais frequência do que extrair dados de evento e o local. Nesta seção, define e agenda duas tarefas separadas:

- Extrair dados de pedido de suporte e de clientes.
- Extrair dados de evento e o local.

Cada tarefa extrai os respetivos dados e publica-o para o arquivo de análise. Existe uma tarefa separada distribui os dados extraídos para o esquema de estrela de análise.

1. No SSMS, ligue para o **jobaccount** base de dados no catálogo-dpt -&lt;utilizador&gt; server.
2. No SSMS, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Modificar @User na parte superior do script e substitua <User> com o nome de utilizador que usou quando implementou a aplicação Wingtip SaaS 
4. Prima F5 para executar o script que cria e executa a tarefa que extrai dados de pedidos de suporte e os clientes de cada base de dados do inquilino. A tarefa salva os dados para o arquivo de análise.
5. Consulte a tabela de TicketsRawData na base de dados tenantanalytics, para garantir que a tabela é preenchida com as informações de bilhetes de todos os inquilinos.

![ticketExtracts](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Repita os passos anteriores, exceto esta substituição de tempo **\ExtractTickets.sql** com **\ExtractVenuesEvents.sql** no passo 2.

Executar com êxito a tarefa preenche a tabela de EventsRawData no arquivo de análise com novos eventos e informações de locais de todos os inquilinos. 

## <a name="data-reorganization"></a>Reorganização de dados

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Dados extraídos que piquem preencher tabelas do esquema de estrela

A próxima etapa é que piquem os dados não processados extraídos para um conjunto de tabelas otimizadas para consultas de análise. É utilizado um esquema de estrela. Uma tabela de fatos central contém o pedido de suporte individual registros de vendas. Outras tabelas são preenchidas com dados relacionados sobre locais, eventos e os clientes. E existem tabelas de dimensões de tempo. 

Nesta secção do tutorial, definir e executar uma tarefa que une os dados não processados extraídos com os dados das tabelas do esquema de estrela. Depois de concluída a tarefa de intercalação, os dados não processados são eliminados, deixando as tabelas prontas para ser preenchido pelos dados de inquilino seguintes extrair a tarefa.

1. No SSMS, ligue para o **jobaccount** base de dados no catálogo-dpt -&lt;utilizador&gt;.
2. No SSMS, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Prima **F5** executar o script para definir uma tarefa que chama o sp_ShredRawExtractedData procedimento armazenado no arquivo de análise.
4. Permita tempo suficiente para a tarefa seja executada com êxito.
    - Verifique os **ciclo de vida** coluna da tabela de jobs.jobs_execution para o estado de tarefa. Certifique-se de que a tarefa **bem-sucedido** antes de continuar. Uma execução com êxito apresenta dados semelhantes para o gráfico a seguir:

![mudando](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Exploração de dados

### <a name="visualize-tenant-data"></a>Visualizar dados de inquilino

Os dados da tabela do esquema de estrela fornecem todos os os pedido de suporte dados de vendas necessários para a sua análise. Para que seja mais fácil ver as tendências em grandes conjuntos de dados, terá de visualizá-la graficamente.  Nesta secção, irá aprender a utilizar **Power BI** para manipular e visualize os dados de inquilino precisa extraídos e organizados.

Utilize os seguintes passos para ligar ao Power BI e para importar as vistas que criou anteriormente:

1. Inicie o Power BI desktop.
2. No friso base, selecione **obter dados**e selecione **mais...** no menu.
3. Na **obter dados** janela, selecione Azure SQL Database.
4. Na janela de início de sessão da base de dados, introduza o nome do servidor (catálogo-dpt -&lt;utilizador&gt;. database.windows.net). Selecione **importação** para **modo de conectividade de dados**e, em seguida, clique em OK. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Selecione **base de dados** no painel esquerdo, em seguida, introduza o nome de utilizador = *programador*e introduza a palavra-passe = *P\@ssword1*. Clique em **Ligar**.  

    ![databasesignin](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. Na **navegador** painel, sob a base de dados de análise, selecione as tabelas do esquema de estrela: fact_Tickets, dim_Events, dim_Venues, dim_Customers e dim_Dates. Em seguida, selecione **carga**. 

Parabéns! Carregou com êxito os dados para o Power BI. Agora pode começar a explorar visualizações interessantes para o ajudar a obter informações sobre os seus inquilinos. Em seguida, examinar como o analytics podem permitir que fornecer recomendações orientadas por dados para a equipa de empresariais Wingtip Tickets. As recomendações podem ajudar a otimizar a experiência de modelo e o cliente de negócios.

Comece por analisar dados de vendas de permissão para ver a variação na utilização entre os locais. Selecione as seguintes opções no Power BI para desenhar um gráfico de barras do número total de pedidos de suporte vendido por cada local. Devido a variação aleatória no gerador de pedido de suporte, os resultados podem ser diferentes.
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

O desenho anterior confirma que o número de pedidos de suporte vendido por cada local varia. Locais vendem bilhetes mais estão a utilizar o serviço mais pesada do que locais vendem bilhetes menos. Pode haver uma oportunidade para ajustar a alocação de recursos de acordo com as necessidades de inquilino diferente.

Pode analisar mais aprofundadamente os dados para ver como as vendas de bilhetes variam ao longo do tempo. Selecione as seguintes opções no Power BI para desenhar o número total de pedidos de suporte vendido por dia durante um período de 60 dias.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

O gráfico anterior mostra esse pico de vendas de pedido de suporte para alguns locais. Esse aumento de reforçar a ideia de que alguns locais poderão estar a consumir recursos do sistema desproporcional. Até agora não há nenhum padrão óbvio quando ocorrem os picos.

Em seguida queira continuar a investigar o significado nos dias de hoje de venda de pico. Quando esses picos ocorrem depois de pedidos de suporte, aceda à venda? Para representar os pedidos de suporte vendidos por dia, selecione as seguintes opções no Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

O desenho anterior mostra que alguns locais vendem muitos pedidos de suporte no primeiro dia de venda. Assim que os pedidos de suporte aceda à venda nesses locais, parece haver apressem. Este fluxo de atividade por alguns locais pode afetar o serviço para os outros inquilinos.

Pode explorar os dados novamente para ver se este apressem é verdadeiro para todos os eventos hospedados por esses locais. Em gráficos anteriores, observou que a Contoso Concert Hall vende muitos pedidos de suporte e que Contoso também possui um pico no vendas de bilhetes em determinados dias. Experimente as opções do Power BI para desenhar as vendas de bilhetes cumulativa para Contoso Concert Hall, concentrando-se sobre as tendências de venda para cada um dos seus eventos. Todos os eventos seguem o mesmo padrão de venda?

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

O desenho anterior para Contoso Concert Hall mostra que o apressem não acontece para todos os eventos. Experimente as opções de filtro para ver as tendências de venda para outros locais.

As informações sobre os padrões de venda de bilhetes podem levar a Wingtip Tickets para otimizar o seu modelo de negócio. Em vez de cobrança todos os inquilinos igualmente, talvez Wingtip deve apresentar o escalões de serviço com diferentes tamanhos de computação. Locais maiores que têm de vender bilhetes mais por dia podem ser oferecidos um escalão superior com um maior contrato de nível serviço (SLA). Esses locais podem ter seus bancos de dados colocados em conjunto com limites de recursos por base de dados mais elevados. Cada escalão de serviço pode ter uma alocação de vendas por hora, com taxas adicionais é cobradas que exceda a alocação. Locais maiores que têm picos periódicos de vendas seriam beneficiar dos escalões superiores e Wingtip Tickets pode monetize de forma mais eficiente seu serviço.

Enquanto isso, alguns clientes Wingtip Tickets reclamam que eles se para vender suficiente bilhetes para justificar o custo do serviço. Talvez dessas informações há uma oportunidade para aumentar as vendas de bilhetes para com um desempenho baixo locais. Vendas superior aumentará o valor percebido do serviço. Clique com o botão direito do rato em fact_Tickets e selecione **nova medida**. Introduza a seguinte expressão para a nova medida chamada **AverageTicketsSold**:

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

Selecione as seguintes opções de visualização para desenhar os tíquetes de percentagem vendidos por cada local para determinar seu sucesso relativo.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

O desenho anterior mostra que, embora a maioria dos locais vendem mais de 80% dos seus pedidos de suporte, alguns estão com problemas preencher a mais de metade postos de trabalho. Experimente os valores bem para selecionar o máxima ou mínima percentagem de pedidos de suporte vendido para cada local.

Anteriormente, deepened sua análise para detetar que vendas de bilhetes tendem a seguir padrões previsíveis. Esta deteção pode deixar a ajuda da Wingtip Tickets com um desempenho baixo vendas de bilhetes de aumento de locais ao recomendar preços dinâmico. Esta deteção poderá revelar uma oportunidade de empregam técnicas de aprendizagem de máquina para prever vendas de bilhetes para cada evento. Predições também poderiam ser feitas para o impacto na receita da oferta de descontos em vendas de bilhetes. Power BI Embedded pode ser integrado num aplicativo de gerenciamento de eventos. A integração pode ajudar a visualizar vendas previstas e o efeito de diferentes descontos. O aplicativo poderia ajudar a planejar um desconto ideal a ser aplicadas diretamente a partir da exibição de análise.

Observado tendências em dados de inquilino da aplicação WingTip. Pode parar outras formas que a aplicação pode informar decisões de negócios SaaS para fornecedores de aplicações. Fornecedores podem atender melhor às necessidades dos seus inquilinos. Espero que este tutorial tem equipado com ferramentas necessárias para realizar análises em dados de inquilino para capacitar as empresas para tomar decisões condicionadas por dados.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> - Implementar uma base de dados de análise de inquilinos com tabelas de esquema em estrela predefinido
> - Utilizado tarefas elásticas para extrair dados de todas as a base de dados de inquilinos
> - Intercalar os dados extraídos para tabelas num esquema de estrela concebido para análise
> - Consultar uma base de dados de análise 
> - Utilizar o Power BI para visualização de dados para observar as tendências em dados de inquilino 

Parabéns!

## <a name="additional-resources"></a>Recursos adicionais

- Adicionais [tutoriais que se baseiam na aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Tarefas elásticas](sql-database-elastic-jobs-overview.md).
- [Análise de entre inquilinos com dados extraídos - aplicação multi-inquilino](saas-multitenantdb-tenant-analytics.md)