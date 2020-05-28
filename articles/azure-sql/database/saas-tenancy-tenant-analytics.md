---
title: Análise de inquilinos cruzados utilizando dados extraídos
description: Consultas de análise de inquilinos cruzados usando dados extraídos de várias bases de dados Azure SQL numa única aplicação de inquilino.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 3078cf7a7b16d5932e861f28a352002aa01cea45
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041995"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>Análise de inquilinos cruzados usando dados extraídos - app de inquilino único
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
Neste tutorial, você percorre um cenário de análise completo para uma implementação de um único inquilino. O cenário demonstra como a análise pode permitir às empresas tomarem decisões inteligentes. Utilizando dados extraídos de cada base de dados de inquilinos, utiliza aanálise para obter informações sobre o comportamento dos inquilinos, incluindo o uso da aplicação Wingtip Tickets SaaS. Este cenário envolve três etapas: 

1.  **Extrair** dados de cada base de dados de inquilinos e **carregar** numa loja de análise.
2.  **Transforme os dados extraídos** para o processamento de análise.
3.  Utilize ferramentas de **inteligência empresarial** para extrair informações úteis, que podem orientar a tomada de decisões. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Crie a loja de análise de inquilinos para extrair os dados.
> - Utilize trabalhos elásticos para extrair dados de cada base de dados de inquilinos na loja de análises.
> - Otimize os dados extraídos (reorganize-se num esquema estelar).
> - Consultar a base de dados de análise.
> - Use o Power BI para visualização de dados para destacar tendências nos dados dos inquilinos e fazer recomendação para melhorias.

![arquiteturaOverView](./media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Padrão de análise de inquilino offline

Aplicações SaaS multi-inquilinas normalmente têm uma grande quantidade de dados de inquilinos armazenados na nuvem. Estes dados fornecem uma rica fonte de insights sobre o funcionamento e uso da sua aplicação, e o comportamento dos seus inquilinos. Estes insights podem orientar o desenvolvimento de funcionalidades, melhorias de usabilidade e outros investimentos na app e plataforma.

O acesso aos dados de todos os inquilinos é simples quando todos os dados estão numa base de dados multi-inquilinos. Mas o acesso é mais complexo quando distribuído à escala através de milhares de bases de dados. Uma forma de domar a complexidade e minimizar o impacto das consultas de análise nos dados transacionais é extrair dados numa base de dados ou armazém de dados de análise de propósito.

Este tutorial apresenta um cenário de análise completo para a aplicação Wingtip Tickets SaaS. Em primeiro lugar, a *Elastic Jobs* é usada para extrair dados de cada base de dados de inquilinos e carregá-la em mesas de preparação numa loja de análise. A loja de análise pode ser uma Base de Dados SQL ou um Armazém de Dados SQL. Para a extração de dados em larga escala, recomenda-se a [Azure Data Factory.](../../data-factory/introduction.md)

Em seguida, os dados agregados são transformados num conjunto de tabelas [star-schema.](https://www.wikipedia.org/wiki/Star_schema) As tabelas consistem numa tabela central de factos e tabelas de dimensão relacionadas.  Para bilhetes wingtip:

- A tabela central de factos no esquema estelar contém dados de bilhetes.
- As tabelas de dimensões descrevem locais, eventos, clientes e datas de compra.

Em conjunto, os quadros centrais de factos e dimensões permitem um processamento analítico eficiente. O star-schema usado neste tutorial é mostrado na seguinte imagem:
 
![arquiteturaOverView](./media/saas-tenancy-tenant-analytics/StarSchema.png)

Finalmente, a loja de análise é consultada usando **o PowerBI** para destacar insights sobre o comportamento do inquilino e o seu uso da aplicação Wingtip Tickets. Faz consultas que:
 
- Mostre a relativa popularidade de cada local
- Realce padrões na venda de bilhetes para diferentes eventos
- Mostre o relativo sucesso de diferentes locais na venda do seu evento

Compreender como cada inquilino está a usar o serviço é usado para explorar opções para rentabilizar o serviço e melhorar o serviço para ajudar os inquilinos a serem mais bem sucedidos. Este tutorial fornece exemplos básicos dos tipos de insights que podem ser recolhidos a partir de dados de inquilinos.

## <a name="setup"></a>Configuração

### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

- A aplicação De dados SaaS Database Per Tenant da Wingtip Tickets É implementada. Para implantar em menos de cinco minutos, consulte [Deploy e explore a aplicação Wingtip SaaS](../../sql-database/saas-dbpertenant-get-started-deploy.md)
- Os bilhetes de base de dados SaaS Por Inquilino e o [código fonte](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) de aplicação são descarregados do GitHub. Consulte as instruções de descarregamento. Certifique-se de que *desbloqueia o ficheiro zip* antes de extrair o seu conteúdo. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para os passos para descarregar e desbloquear os scripts Wingtip Tickets SaaS.
- Está instalado o Power BI Desktop. [Transferir o Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- O lote de inquilinos adicionais foi provisionado, ver o tutorial de [**inquilinos da Provision.**](../../sql-database/saas-dbpertenant-provision-and-catalog.md)
- Foi criada uma base de dados de conta de emprego e de conta de emprego. Consulte os passos adequados no tutorial de [**gestão da Schema.**](../../sql-database/saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent)

### <a name="create-data-for-the-demo"></a>Criar dados para a demonstração

Neste tutorial, a análise é feita nos dados de venda de bilhetes. No passo atual, você gera dados de bilhetes para todos os inquilinos.  Mais tarde, estes dados são extraídos para análise. *Certifique-se de que disponibilizou o lote de inquilinos como descrito anteriormente,* para que tenha uma quantidade significativa de dados. Uma quantidade suficientemente grande de dados pode expor uma gama de diferentes padrões de compra de bilhetes.

1. No PowerShell ISE, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*, e definir o seguinte valor:
    - **$DemoScenario**  =  **1** Comprar bilhetes para eventos em todos os locais
2. Pressione **f5** para executar o script e crie o histórico de compra de bilhetes para cada evento em cada local.  O guião dura vários minutos para gerar dezenas de milhares de bilhetes.

### <a name="deploy-the-analytics-store"></a>Implementar a loja de análise
Muitas vezes existem numerosas bases de dados transacionais que, em conjunto, detêm todos os dados dos inquilinos. Deve agregar os dados dos inquilinos das muitas bases de dados transacionais numa única loja de análises. A agregação permite uma consulta eficiente dos dados. Neste tutorial, uma Base de Dados Azure SQL é usada para armazenar os dados agregados.

Nos seguintes passos, você implanta a loja de análise, que é chamada **de inquilinoanalytics**. Também implanta tabelas pré-definidas que são povoadas mais tarde no tutorial:
1. No PowerShell ISE, aberto *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Desloque a variável $DemoScenario no script para corresponder à sua escolha de loja de análises:
    - Para utilizar a base de dados SQL sem loja de colunas, desempor **$DemoScenario**  =  **2**
    - Para utilizar a base de dados **$DemoScenario**SQL com loja de colunas, der$DemoScenario  =  **3**  
3. Pressione **f5** para executar o script de demonstração (que chama o script *Deploy-TenantAnalytics \<XX> .ps1)* que cria a loja de analítica de inquilinos. 

Agora que implementou a aplicação e encheu-a com dados interessantes do inquilino, utilize o [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para ligar **os inquilinos1-dpt- &lt; &gt; ** ** &lt; &gt; ** User servers usando Login = *developer*, Password = *P \@ ssword1*. Consulte o [tutorial introdutório](../../sql-database/saas-dbpertenant-wingtip-app-overview.md) para obter mais orientação.

![arquiteturaOverView](./media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

No Explorador de Objetos, execute os seguintes passos:

1. Expanda o servidor de * &lt; utilizador &gt; dos inquilinos1-dpt.*
2. Expandir o nó de Bases de Dados e ver a lista de bases de dados de inquilinos.
3. Expandir o servidor de * &lt; utilizador &gt; do catálogo dpt.*
4. Verifique se vê a loja de análisee a base de dados da conta de trabalho.

Consulte os seguintes itens de base de dados no SSMS Object Explorer expandindo o nó da loja de análises:

- Tabelas **TicketsRawData** e **EventsRawData** detêm dados extraídos brutos das bases de dados dos inquilinos.
- As mesas de esquemas estelares são **fact_Tickets,** **dim_Customers,** **dim_Venues,** **dim_Events**e **dim_Dates.**
- O procedimento armazenado é utilizado para povoar as tabelas de esquemas estelares das tabelas de dados brutos.

![arquiteturaOverView](./media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extração de dados 

### <a name="create-target-groups"></a>Criar grupos-alvo 

Antes de prosseguir, certifique-se de que implementou a conta de emprego e a base de dados de conta de emprego. Nos próximos passos, a Elastic Jobs é usada para extrair dados de cada base de dados de inquilinos e para armazenar os dados na loja de análises. Em seguida, o segundo trabalho rasga os dados e armazena-os em mesas no esquema estelar. Estes dois postos de trabalho são contra dois grupos-alvo diferentes, nomeadamente **o TenantGroup** e o **AnalyticsGroup.** O trabalho de extrato é contra o TenantGroup, que contém todas as bases de dados dos inquilinos. O trabalho de trituração vai contra o AnalyticsGroup, que contém apenas a loja de análise. Crie os grupos-alvo utilizando os seguintes passos:

1. No SSMS, ligue-se à base de dados da **conta de trabalho** no catálogo-dpt- Utilizador &lt; &gt; .
2. Em SSMS, aberto *...\Módulos de Aprendizagem\Análise Operacional\Tenant Analytics\ TargetGroups.sql* 
3. Modifique a @User variável no topo do script, substituindo `<User>` pelo valor do utilizador utilizado quando implementou a aplicação Wingtip SaaS.
4. Pressione **F5** para executar o script que cria os dois grupos alvo.

### <a name="extract-raw-data-from-all-tenants"></a>Extrair dados brutos de todos os inquilinos

Modificações extensivas de dados podem ocorrer com mais frequência para os dados dos *bilhetes e do cliente* do que para os dados de *eventos e locais.* Por isso, considere extrair os dados dos bilhetes e dos clientes separadamente e com mais frequência do que extrai dados de eventos e locais. Nesta secção, define e age dois trabalhos separados:

- Extrair bilhete e dados do cliente.
- Extrair dados do evento e do local.

Cada trabalho extrai os seus dados e coloca-os na loja de análises. Há um trabalho separado que destrói os dados extraídos no esquema de estrelas analíticos.

1. No SSMS, ligue-se à base de dados da **conta de trabalho** no servidor de utilizador do &lt; &gt; catálogo-dpt.
2. No SSMS, aberto *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Modifique @User no topo do script e substitua-o pelo nome de utilizador utilizado quando `<User>` implementou a app Wingtip SaaS 
4. Pressione f5 para executar o script que cria e executa o trabalho que extrai bilhetes e dados de clientes de cada base de dados de inquilinos. O trabalho guarda os dados na loja de análises.
5. Consulta da tabela TicketsRawData na base de dados do tenantanalytics, para garantir que a mesa é povoada com informações de bilhetes de todos os inquilinos.

![bilheteExtras](./media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Repita os passos anteriores, exceto que desta vez substitua **\ExtractTickets.sql** por **\ExtractVenuesEvents.sql** no passo 2.

Gerir com sucesso o trabalho povoa a tabela EventsRawData na loja de análise com novos eventos e informações de locais de todos os inquilinos. 

## <a name="data-reorganization"></a>Reorganização de dados

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Triturar dados extraídos para povoar mesas de esquemas estelares

O próximo passo é rasgar os dados brutos extraídos num conjunto de tabelas otimizadas para consultas de análise. Um esquema estelar é usado. Uma tabela de factos central detém registos individuais de venda de bilhetes. Outras tabelas são povoadas com dados relacionados sobre locais, eventos e clientes. E há tabelas de dimensão do tempo. 

Nesta secção do tutorial, define-se e executa um trabalho que une os dados brutos extraídos com os dados nas tabelas star-schema. Após a fusão do trabalho, os dados brutos são eliminados, deixando as tabelas prontas para serem povoadas pelo próximo trabalho de extração de dados do inquilino.

1. No SSMS, ligue-se à base de dados da **conta de trabalho** no catálogo-dpt- Utilizador &lt; &gt; .
2. Em SSMS, aberto *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtraedData.sql*.
3. Pressione **f5** para executar o script para definir um trabalho que chama o sp_ShredRawExtractedData procedimento armazenado na loja de análise.
4. Dê tempo suficiente para que o trabalho corra com sucesso.
    - Verifique a coluna **lifecycle** de empregos.jobs_execution tabela para o estado de trabalho. Certifique-se de que o trabalho **foi bem sucedido** antes de prosseguir. Uma execução bem sucedida apresenta dados semelhantes ao seguinte gráfico:

![trituração](./media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Exploração de dados

### <a name="visualize-tenant-data"></a>Visualizar os dados dos inquilinos

Os dados na tabela star-schema fornecem todos os dados de venda de bilhetes necessários para a sua análise. Para facilitar a visualização das tendências em grandes conjuntos de dados, é necessário visualizá-la graficamente.  Nesta secção, aprende-se a usar o **Power BI** para manipular e visualizar os dados do inquilino que extraiu e organizou.

Utilize os seguintes passos para ligar ao Power BI e para importar as vistas que criou anteriormente:

1. Ambiente de trabalho do Launch Power BI.
2. A partir da fita Home, selecione **Obter Dados,** e selecione **Mais...** do menu.
3. Na janela **Obter Dados,** selecione Base de Dados Azure SQL.
4. Na janela de login da base de dados, introduza o nome do seu servidor (catalog-dpt- &lt; Utilizador &gt; .database.windows.net). Selecione **Import** para **modo de conectividade**de dados e, em seguida, clique EM OK. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Selecione **Base de Dados** no painel esquerdo, introduza o nome do utilizador = *programador,* e introduza a palavra-passe = *P \@ ssword1*. Clique em **Ligar**.  

    ![basede de dadossignina](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. No painel **navigator,** sob a base de dados de análise, selecione as tabelas star-schema: fact_Tickets, dim_Events, dim_Venues, dim_Customers e dim_Dates. Em seguida, selecione **Carregar**. 

Parabéns! Conseguiu carregar os dados para o Power BI. Agora pode começar a explorar visualizações interessantes para ajudar a obter informações sobre os seus inquilinos. Em seguida, você passa por como a análise pode permitir-lhe fornecer recomendações baseadas em dados para a equipe de negócios Wingtip Tickets. As recomendações podem ajudar a otimizar o modelo de negócio e a experiência do cliente.

Começa-se por analisar os dados de venda de bilhetes para ver a variação de utilização em todos os locais. Selecione as seguintes opções no Power BI para traçar um gráfico de barras do número total de bilhetes vendidos por cada local. Devido à variação aleatória no gerador de bilhetes, os seus resultados podem ser diferentes.
 
![TotalticketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

O enredo anterior confirma que o número de bilhetes vendidos por cada local varia. Os locais que vendem mais bilhetes estão a usar o seu serviço mais fortemente do que os locais que vendem menos bilhetes. Pode haver aqui uma oportunidade para adaptar a atribuição de recursos de acordo com as diferentes necessidades dos inquilinos.

Pode analisar ainda mais os dados para ver como as vendas de bilhetes variam ao longo do tempo. Selecione as seguintes opções no Power BI para traçar o número total de bilhetes vendidos todos os dias por um período de 60 dias.
 
![VendaVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

O gráfico anterior mostra que as vendas de bilhetes aumentam para alguns locais. Estes picos reforçam a ideia de que alguns locais podem estar a consumir desproporcionalmente os recursos do sistema. Até agora não há um padrão óbvio quando os picos ocorrem.

Em seguida, você quer investigar ainda mais o significado destes dias de pico de venda. Quando é que estes picos ocorrem depois de os bilhetes irem à venda? Para traçar bilhetes vendidos por dia, selecione as seguintes opções no Power BI.

![Distribuição do Dia da Venda](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

O enredo anterior mostra que alguns locais vendem muitos bilhetes no primeiro dia de venda. Assim que os bilhetes estão à venda nestes locais, parece haver uma correria louca. Esta explosão de atividade por alguns locais pode afetar o serviço para outros inquilinos.

Você pode perfurar os dados novamente para ver se esta corrida louca é verdadeira para todos os eventos hospedados por estes locais. Em parcelas anteriores, observou que o Sala de Concertos Contoso vende muitos bilhetes, e que Contoso também tem um pico na venda de bilhetes em determinados dias. Brinque com opções de Power BI para traçar vendas cumulativas de bilhetes para a Sala de Concertos Contoso, focando-se nas tendências de venda para cada um dos seus eventos. Todos os eventos seguem o mesmo padrão de venda?

![ContosoSales](./media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

O enredo anterior para a Sala de Concertos de Contoso mostra que a corrida louca não acontece para todos os eventos. Brinque com as opções de filtro para ver as tendências de venda para outros locais.

As ideias sobre os padrões de venda de bilhetes podem levar a Wingtip Tickets a otimizar o seu modelo de negócio. Em vez de cobrar todos os inquilinos igualmente, talvez wingtip deva introduzir níveis de serviço com diferentes tamanhos de computação. Locais maiores que precisam de vender mais bilhetes por dia poderiam ser oferecidos um nível mais elevado com um acordo de nível de serviço mais elevado (SLA). Esses locais poderiam ter as suas bases de dados colocadas em conjunto com limites de recursos por base de dados mais elevados. Cada nível de serviço poderia ter uma atribuição de vendas por hora, com taxas adicionais cobradas por exceder a atribuição. Locais maiores que tenham explosões periódicas de vendas beneficiariam dos níveis mais altos, e os Bilhetes Wingtip podem rentabilizar o seu serviço de forma mais eficiente.

Entretanto, alguns clientes da Wingtip Tickets queixam-se de que lutam para vender bilhetes suficientes para justificar o custo do serviço. Talvez nestes insights haja uma oportunidade para impulsionar a venda de bilhetes para locais com baixo desempenho. Vendas mais elevadas aumentariam o valor percebido do serviço. Clique à direita fact_Tickets e selecione **Nova medida**. Introduza a seguinte expressão para a nova medida denominada **AverageTicketsSold:**

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

Selecione as seguintes opções de visualização para traçar os bilhetes percentuais vendidos por cada local para determinar o seu relativo sucesso.

![AvgTicketsByVenues](./media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

O enredo anterior mostra que, embora a maioria dos locais venda mais de 80% dos seus bilhetes, alguns estão a ter dificuldades em preencher mais de metade dos lugares. Jogue com os Valores Bem para selecionar a percentagem máxima ou mínima de bilhetes vendidos para cada local.

Mais cedo aprofundou a sua análise para descobrir que a venda de bilhetes tende a seguir padrões previsíveis. Esta descoberta pode permitir que os Wingtip Tickets ajudem os locais a impulsionar a venda de bilhetes, recomendando preços dinâmicos. Esta descoberta pode revelar uma oportunidade de utilizar técnicas de machine learning para prever a venda de bilhetes para cada evento. Poderiam também ser feitas previsões para o impacto nas receitas da oferta de descontos na venda de bilhetes. Power BI Embedded poderia ser integrado numa aplicação de gestão de eventos. A integração poderia ajudar a visualizar as vendas previstas e o efeito de diferentes descontos. A aplicação poderia ajudar a conceber um desconto ideal para ser aplicado diretamente a partir do ecrã de análise.

Observou tendências nos dados dos inquilinos a partir da aplicação WingTip. Pode contemplar outras formas de informar as decisões empresariais dos fornecedores de aplicações SaaS. Os vendedores podem atender melhor às necessidades dos seus inquilinos. Esperemos que este tutorial o tenha equipado com ferramentas necessárias para realizar análises em dados de inquilinos para capacitar os seus negócios a tomarem decisões baseadas em dados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> - Implementou uma base de dados de análise de inquilinos com mesas de esquemas de estrelas pré-definidas
> - Trabalhos elásticos usados para extrair dados de toda a base de dados de inquilinos
> - Fundir os dados extraídos em tabelas num star-schema concebido para análise
> - Consulta de uma base de dados de análise 
> - Use o Power BI para visualização de dados para observar tendências nos dados dos inquilinos 

Parabéns!

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais adicionais que se baseiam na aplicação Wingtip SaaS.](../../sql-database/saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- [Trabalhos Elásticos.](../../sql-database/elastic-jobs-overview.md)
- [Análise de inquilinos cruzados usando dados extraídos - app multi-inquilino](../../sql-database/saas-multitenantdb-tenant-analytics.md)