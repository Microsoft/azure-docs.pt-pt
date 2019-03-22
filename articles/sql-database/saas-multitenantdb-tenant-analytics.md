---
title: Executar consultas de análise em bases de dados SQL do Azure | Documentos da Microsoft
description: Consultas de análise de entre inquilinos usando dados extraídos a partir de várias bases de dados do SQL Database do Azure numa aplicação multi-inquilino.
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
ms.date: 09/19/2018
ms.openlocfilehash: 340c08841b7bedc9f2453617aeff111beb810961
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888183"
---
# <a name="cross-tenant-analytics-using-extracted-data---multi-tenant-app"></a>Análise de entre inquilinos com dados extraídos - aplicação multi-inquilino
 
Neste tutorial, é necessário percorrer um cenário de análise completa para uma implementação multi-inquilino. O cenário demonstra como a análise pode ativar as empresas a tomarem decisões inteligentes. Usando dados extraídos da base de dados em partição horizontal, utilizar análise para obter informações sobre o comportamento de inquilino, incluindo o uso do exemplo de aplicação Wingtip Tickets SaaS. Este cenário envolve três etapas: 

1.  **Extrair dados** de cada base de dados do inquilino para um arquivo de análise.
2.  **Otimizar os dados extraídos** para processamento de análise.
3.  Uso **Business Intelligence** ferramentas para desenhar as informações úteis, que podem orientar a tomada de decisões. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Crie o inquilino análise armazenar para extrair os dados.
> - Utilize as tarefas elásticas para extrair dados de cada base de dados do inquilino para o arquivo de análise.
> - Otimize os dados extraídos (reorganize num esquema de estrela).
> - Consulte a base de dados de análise.
> - Utilize o Power BI para visualização de dados para destacar tendências em dados de inquilino e fazer recomendação para melhorias.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Padrão de análise de inquilino offline

Aplicações SaaS que desenvolver têm acesso a uma grande quantidade de dados armazenados na cloud do inquilino. Os dados fornecem uma fonte avançada de informações sobre a operação e a utilização da sua aplicação e sobre o comportamento dos inquilinos. Estas informações podem ajudá-desenvolvimento de funcionalidade, usabilidade e a outros investimentos na aplicação e na plataforma.

Acesso aos dados para todos os inquilinos é simples quando todos os dados são apenas uma base de dados do multi-inquilino. Mas o acesso é mais complexo quando distribuição à escala através de milhares de bases de dados. Uma forma de controle da complexidade é extrair os dados para uma base de dados de análise ou de um armazém de dados. Em seguida, consultar o armazém de dados para recolher informações a partir dos dados de bilhetes de todos os inquilinos.

Este tutorial apresenta um cenário de completas de análise para esta aplicação SaaS de exemplo. Primeiro, elásticas as tarefas são utilizadas para agendar a extração de dados de cada base de dados do inquilino. Os dados são enviados para um arquivo de análise. O arquivo de análise pode ser uma base de dados do SQL ou um SQL Data Warehouse. Para extração de dados em grande escala [do Azure Data Factory](../data-factory/introduction.md) é commended.

Em seguida, os dados agregados é shredded num conjunto de [esquema de estrela](https://www.wikipedia.org/wiki/Star_schema) tabelas. As tabelas são compostas por uma tabela de fatos central mais tabelas de dimensão relacionada:

- A tabela de fatos central no esquema de estrela contém dados de pedido de suporte.
- As tabelas de dimensões contêm dados sobre locais, eventos, os clientes e datas de compra.

Em conjunto a dimensão tabelas ativar eficiente processamento analítico e central. O esquema de estrela utilizado neste tutorial, é apresentado na imagem seguinte:
 
![StarSchema](media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Por fim, as tabelas do esquema de estrela são consultadas. Os resultados da consulta são apresentados visualmente para realçar as informações sobre o comportamento de inquilino e a sua utilização da aplicação. Com este esquema de estrela, pode executar consultas que o ajudam a detetar itens semelhante ao seguinte:

- Quem está comprando ingressos de qual local.
- Padrões ocultos e tendências nas seguintes áreas:
    - As vendas de pedidos de suporte.
    - A popularidade relativa de cada local.

Compreender como consistentemente cada inquilino está a utilizar o serviço fornece a oportunidade de criar planos de serviço para se adaptar às suas necessidades. Este tutorial fornece exemplos básicos de informações que podem ser compiladas a partir dos dados de inquilino.

## <a name="setup"></a>Configurar

### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

- A aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino é implementada. Para implementar em menos de cinco minutos, veja [implementar e explorar a aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino](saas-multitenantdb-get-started-deploy.md)
- Os scripts de Wingtip SaaS e aplicativo [código-fonte](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) são transferidos a partir do GitHub. Não se esqueça *desbloquear o ficheiro zip* antes de extrair o respetivo conteúdo. Veja a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip Tickets SaaS de desbloqueio.
- Power BI Desktop está instalado. [Transferir o Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Provisionamento do lote de inquilinos adicionais, consulte a [ **aprovisionar inquilinos tutorial**](saas-multitenantdb-provision-and-catalog.md).
- Um agente de tarefa e a base de dados de agente de tarefa tem sido criadas. Consulte as etapas apropriadas no [ **tutorial de gestão de esquema**](saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Criar dados para a demonstração

Neste tutorial, a análise é executada nos dados de vendas de pedido de suporte. O passo atual, vai gerar dados de pedido de suporte para todos os inquilinos.  Mais tarde, estes dados são extraídos para análise. *Certifique-se de ter aprovisionado o lote de inquilinos, tal como descrito anteriormente, para que tenha uma quantidade significativa de dados*. Uma suficientemente grande quantidade de dados pode expor uma variedade de diferentes padrões de compra de bilhetes.

1. Na **ISE do PowerShell**, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*e defina o valor seguinte:
    - **$DemoScenario** = **1** comprar bilhetes para eventos em todos os locais
2. Prima **F5** para executar o script e criar o histórico para cada evento em cada local de compra de bilhetes.  O script é executado durante vários minutos gerar dezenas de milhares de pedidos de suporte.

### <a name="deploy-the-analytics-store"></a>Implementar o armazenamento de análise
Muitas vezes, existem inúmeros transacional em partição horizontal bases de dados que em conjunto contêm todos os dados de inquilino. Tem de agregar os dados de inquilino da base de dados em partição horizontal para armazenamento de uma análise. A agregação permite que uma consulta eficiente dos dados. Neste tutorial, uma base de dados do SQL Database do Azure é utilizado para armazenar os dados agregados.

Os passos seguintes, vai implementar o armazenamento de análise, o que é chamado **tenantanalytics**. Também implementa tabelas predefinidas que são preenchidas mais tarde no tutorial:
1. No ISE do PowerShell, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Defina a variável de $DemoScenario no script para corresponder à sua escolha de arquivo de análise. Para efeitos de aprendizagem, recomenda-se sem columnstore de base de dados SQL.
    - Para utilizar a base de dados SQL sem columnstore, defina **$DemoScenario** = **2**
    - Para utilizar a base de dados SQL com columnstore, defina **$DemoScenario** = **3**  
3. Prima **F5** para executar o script de demonstração (que chama o *implementar TenantAnalytics<XX>. ps1* script) que cria o arquivo de análise de inquilino. 

Agora que implementou a aplicação e preenchido com dados interessantes de inquilino, utilize [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) liguem **tenants1-mt -\<utilizador\>**  e **catálogo-mt -\<usuário\>**  servidores com início de sessão = *desenvolvedor*, palavra-passe = *P\@ssword1*.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

No Object Explorer, execute os seguintes passos:

1. Expanda a *tenants1-mt -\<usuário\>*  server.
2. Expanda o nó de bases de dados e ver *tenants1* base de dados que contém vários inquilinos.
3. Expanda a *catálogo-mt -\<usuário\>*  server.
4. Certifique-se de que vê o armazenamento de análise e a base de dados jobaccount.

Consulte os seguintes itens de base de dados no Explorador de objeto do SSMS ao expandir o nó do arquivo de análise:

- Tabelas **TicketsRawData** e **EventsRawData** manter não processados dados extraídos dos bancos de dados de inquilino.
- As tabelas do esquema de estrela são **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, e **dim_Dates** .
- O **sp_ShredRawExtractedData** procedimento armazenado é utilizado para preencher as tabelas do esquema de estrela das tabelas de dados não processados.

![tenantAnalytics](media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extração de dados 

### <a name="create-target-groups"></a>Criar grupos de destino 

Antes de continuar, certifique-se de que implementou a base de dados do conta e jobaccount tarefa. O próximo conjunto de passos, as tarefas elásticas é utilizada para extrair dados da base de dados de inquilinos em partição horizontal e para armazenar os dados no arquivo de análise. Em seguida, a segunda tarefa distribui os dados e os armazena em tabelas no esquema de estrela. Estas duas tarefas são executadas em relação a dois grupos de destino diferente, ou seja **TenantGroup** e **AnalyticsGroup**. A tarefa de extração é executada relativamente a TenantGroup, que contém todas as bases de dados de inquilino. A tarefa shredding for executado relativamente a AnalyticsGroup, que contém apenas o arquivo de análise. Crie os grupos de destino, utilize os seguintes passos:

1. No SSMS, ligue para o **jobaccount** base de dados no catálogo-mt -\<utilizador\>.
2. No SSMS, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Modificar a @User variável na parte superior do script, substituindo <User> com o valor de utilizador que usou quando implementou a aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino.
4. Prima **F5** para executar o script que cria os grupos de destino de duas.

### <a name="extract-raw-data-from-all-tenants"></a>Extrair dados não processados de todos os inquilinos

Transações podem ocorrer com mais frequência para *pedido de suporte e de clientes* dados do que para *eventos e o foro* dados. Por conseguinte, considere a extração de dados de pedido de suporte e de clientes em separado e com mais frequência do que extrair dados de evento e o local. Nesta seção, define e agenda duas tarefas separadas:

- Extrair dados de pedido de suporte e de clientes.
- Extrair dados de evento e o local.

Cada tarefa extrai os respetivos dados e publica-o para o arquivo de análise. Existe uma tarefa separada distribui os dados extraídos para o esquema de estrela de análise.

1. No SSMS, ligue para o **jobaccount** base de dados no catálogo-mt -\<utilizador\> server.
2. No SSMS, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Modificar @User na parte superior do script e substitua <User> com o nome de utilizador que usou quando implementou a aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino. 
4. Prima **F5** para executar o script que cria e executa a tarefa que extrai dados de pedidos de suporte e os clientes de cada base de dados do inquilino. A tarefa salva os dados para o arquivo de análise.
5. Consulte a tabela de TicketsRawData na base de dados tenantanalytics, para garantir que a tabela é preenchida com as informações de bilhetes de todos os inquilinos.

![ticketExtracts](media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Repita os passos anteriores, exceto esta substituição de tempo **\ExtractTickets.sql** com **\ExtractVenuesEvents.sql** no passo 2.

Executar com êxito a tarefa preenche a tabela de EventsRawData no arquivo de análise com novos eventos e informações de locais de todos os inquilinos. 

## <a name="data-reorganization"></a>Reorganização de dados

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Dados extraídos que piquem preencher tabelas do esquema de estrela

A próxima etapa é que piquem os dados não processados extraídos para um conjunto de tabelas otimizadas para consultas de análise. É utilizado um esquema de estrela. Uma tabela de fatos central contém o pedido de suporte individual registros de vendas. Tabelas de dimensões são preenchidas com dados sobre locais, eventos, os clientes e datas de compra. 

Nesta secção do tutorial, definir e executar uma tarefa que une os dados não processados extraídos com os dados das tabelas do esquema de estrela. Depois de concluída a tarefa de intercalação, os dados não processados são eliminados, deixando as tabelas prontas para ser preenchido pelos dados de inquilino seguintes extrair a tarefa.

1. No SSMS, ligue para o **jobaccount** base de dados no catálogo-mt -\<utilizador\>.
2. No SSMS, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Prima **F5** executar o script para definir uma tarefa que chama o sp_ShredRawExtractedData procedimento armazenado no arquivo de análise.
4. Permita tempo suficiente para a tarefa seja executada com êxito.
    - Verifique os **ciclo de vida** coluna da tabela de jobs.jobs_execution para o estado de tarefa. Certifique-se de que a tarefa **bem-sucedido** antes de continuar. Uma execução com êxito apresenta dados semelhantes para o gráfico a seguir:

![shreddingJob](media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Exploração de dados

### <a name="visualize-tenant-data"></a>Visualizar dados de inquilino

Os dados da tabela do esquema de estrela fornecem todos os os pedido de suporte dados de vendas necessários para a sua análise. Para que seja mais fácil ver as tendências em grandes conjuntos de dados, terá de visualizá-la graficamente.  Nesta secção, irá aprender a utilizar **Power BI** para manipular e visualize os dados de inquilino precisa extraídos e organizados.

Utilize os seguintes passos para ligar ao Power BI e para importar as vistas que criou anteriormente:

1. Inicie o Power BI desktop.
2. No friso base, selecione **obter dados**e selecione **mais...** no menu.
3. Na **obter dados** janela, selecione Azure SQL Database.
4. Na janela de início de sessão da base de dados, introduza o nome do servidor (catálogo-mt -\<utilizador\>. database.windows.net). Selecione **importação** para **modo de conectividade de dados**e, em seguida, clique em OK. 

    ![powerBISignIn](media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. Selecione **base de dados** no painel esquerdo, em seguida, introduza o nome de utilizador = *programador*e introduza a palavra-passe = *P\@ssword1*. Clique em **Ligar**.  

    ![DatabaseSignIn](media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. Na **navegador** painel, sob a base de dados de análise, selecione as tabelas do esquema de estrela: fact_Tickets, dim_Events, dim_Venues, dim_Customers e dim_Dates. Em seguida, selecione **carga**. 

Parabéns! Carregou com êxito os dados para o Power BI. Agora pode começar a explorar visualizações interessantes para o ajudar a obter informações sobre os seus inquilinos. Em seguida, examinar como o analytics podem permitir que fornecer recomendações orientadas por dados para a equipa de empresariais Wingtip Tickets. As recomendações podem ajudar a otimizar a experiência de modelo e o cliente de negócios.

Comece por analisar dados de vendas de permissão para ver a variação na utilização entre os locais. Selecione as seguintes opções no Power BI para desenhar um gráfico de barras do número total de pedidos de suporte vendido por cada local. Devido a variação aleatória no gerador de pedido de suporte, os resultados podem ser diferentes.
 
![TotalTicketsByVenues](media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

O desenho anterior confirma que o número de pedidos de suporte vendido por cada local varia. Locais vendem bilhetes mais estão a utilizar o serviço mais pesada do que locais vendem bilhetes menos. Pode haver uma oportunidade para ajustar a alocação de recursos de acordo com as necessidades de inquilino diferente.

Pode analisar mais aprofundadamente os dados para ver como as vendas de bilhetes variam ao longo do tempo. Selecione as seguintes opções no Power BI para desenhar o número total de pedidos de suporte vendido por dia durante um período de 60 dias.
 
![SaleVersusDate](media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

O gráfico anterior mostra esse pico de vendas de pedido de suporte para alguns locais. Esse aumento de reforçar a ideia de que alguns locais poderão estar a consumir recursos do sistema desproporcional. Até agora não há nenhum padrão óbvio quando ocorrem os picos.

Em seguida queira continuar a investigar o significado nos dias de hoje de venda de pico. Quando esses picos ocorrem depois de pedidos de suporte, aceda à venda? Para representar os pedidos de suporte vendidos por dia, selecione as seguintes opções no Power BI.

![SaleDayDistribution](media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

O desenho anterior mostra que alguns locais vendem muitos pedidos de suporte no primeiro dia de venda. Assim que os pedidos de suporte aceda à venda nesses locais, parece haver apressem. Este fluxo de atividade por alguns locais pode afetar o serviço para os outros inquilinos.

Pode explorar os dados novamente para ver se este apressem é verdadeiro para todos os eventos hospedados por esses locais. Em gráficos anteriores, observou que a Contoso Concert Hall vende muitos pedidos de suporte e que Contoso também possui um pico no vendas de bilhetes em determinados dias. Experimente as opções do Power BI para desenhar as vendas de bilhetes cumulativa para Contoso Concert Hall, concentrando-se sobre as tendências de venda para cada um dos seus eventos. Todos os eventos seguem o mesmo padrão de venda?

![ContosoSales](media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

O desenho anterior para Contoso Concert Hall mostra que o apressem não acontece para todos os eventos. Experimente as opções de filtro para ver as tendências de venda para outros locais.

As informações sobre os padrões de venda de bilhetes podem levar a Wingtip Tickets para otimizar o seu modelo de negócio. Em vez de cobrança todos os inquilinos igualmente, talvez Wingtip deve apresentar o escalões de serviço com diferentes tamanhos de computação. Locais maiores que têm de vender bilhetes mais por dia podem ser oferecidos um escalão superior com um maior contrato de nível serviço (SLA). Esses locais podem ter seus bancos de dados colocados em conjunto com limites de recursos por base de dados mais elevados. Cada escalão de serviço pode ter uma alocação de vendas por hora, com taxas adicionais é cobradas que exceda a alocação. Locais maiores que têm picos periódicos de vendas seriam beneficiar dos escalões superiores e Wingtip Tickets pode monetize de forma mais eficiente seu serviço.

Enquanto isso, alguns clientes Wingtip Tickets reclamam que eles se para vender suficiente bilhetes para justificar o custo do serviço. Talvez dessas informações há uma oportunidade para aumentar as vendas de bilhetes para em execução locais. Vendas superior aumentará o valor percebido do serviço. Clique com o botão direito do rato em fact_Tickets e selecione **nova medida**. Introduza a seguinte expressão para a nova medida chamada **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selecione as seguintes opções de visualização para desenhar os tíquetes de percentagem vendidos por cada local para determinar seu sucesso relativo.

![analyticsViews](media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

O desenho anterior mostra que, embora a maioria dos locais vendem mais de 80% dos seus pedidos de suporte, alguns estão com problemas preencher a mais de metade postos de trabalho. Experimente os valores bem para selecionar o máxima ou mínima percentagem de pedidos de suporte vendido para cada local.

Anteriormente, deepened sua análise para detetar que vendas de bilhetes tendem a seguir padrões previsíveis. Esta deteção pode deixar a ajuda da Wingtip Tickets com um desempenho baixo vendas de bilhetes de aumento de locais ao recomendar preços dinâmico. Esta deteção poderá revelar uma oportunidade de empregam técnicas de aprendizagem de máquina para prever vendas de bilhetes para cada evento. Predições também poderiam ser feitas para o impacto na receita da oferta de descontos em vendas de bilhetes. Power BI Embedded pode ser integrado num aplicativo de gerenciamento de eventos. A integração pode ajudar a visualizar vendas previstas e o efeito de diferentes descontos. O aplicativo poderia ajudar a planejar um desconto ideal a ser aplicadas diretamente a partir da exibição de análise.

Observado tendências em dados de inquilino da aplicação Wingtip Tickets SaaS multi-inquilino base de dados. Pode parar outras formas que a aplicação pode informar decisões de negócios SaaS para fornecedores de aplicações. Fornecedores podem atender melhor às necessidades dos seus inquilinos. Espero que este tutorial tem equipado com ferramentas necessárias para realizar análises em dados de inquilino para capacitar as empresas para tomar decisões condicionadas por dados.

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

Adicionais [tutoriais que se baseiam na aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials). 
- [Tarefas elásticas](sql-database-elastic-jobs-overview.md).
- [Análise de entre inquilinos com extraídos dados - aplicação de inquilino único](saas-tenancy-tenant-analytics.md) 