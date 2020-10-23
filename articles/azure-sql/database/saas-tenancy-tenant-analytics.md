---
title: Análise de inquilinos cruzados usando dados extraídos
description: Consultas de análise de inquilinos cruzados usando dados extraídos de várias bases de dados Azure SQL em uma única aplicação de inquilino.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 8076b417c8043a4f6796ccca0e67db79360ede73
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331673"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>Análise de inquilinos cruzados usando dados extraídos - app de inquilino único
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
Neste tutorial, você percorre um cenário de análise completa para uma implementação de um único inquilino. O cenário demonstra como a análise pode permitir às empresas tomarem decisões inteligentes. Utilizando dados extraídos de cada base de dados de inquilinos, você usa analítica para obter informações sobre o comportamento do inquilino, incluindo a sua utilização da aplicação De Ingressos De Ponta de Amostra SaaS. Este cenário envolve três etapas: 

1.  **Extrair** dados de cada base de dados de **inquilinos** e carregar numa loja de análise.
2.  **Transforme os dados extraídos** para o processamento de análise.
3.  Use ferramentas **de inteligência empresarial** para extrair informações úteis, que podem orientar a tomada de decisão. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Crie a loja de análise de inquilinos para extrair os dados.
> - Utilize trabalhos elásticos para extrair dados de cada base de dados de inquilinos na loja de análise.
> - Otimize os dados extraídos (reorganize-se num esquema estelar).
> - Consulta a base de dados de análise.
> - Use o Power BI para visualização de dados para destacar as tendências dos dados dos inquilinos e fazer recomendações para melhorias.

![O diagrama mostra uma visão geral da arquitetura usada para este artigo.](./media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Padrão de análise de inquilino offline

As aplicações SaaS multi-arrendatários normalmente têm uma grande quantidade de dados de inquilinos armazenados na nuvem. Estes dados fornecem uma rica fonte de informações sobre a operação e utilização da sua aplicação, e o comportamento dos seus inquilinos. Estes insights podem orientar o desenvolvimento de funcionalidades, melhorias de usabilidade e outros investimentos na app e plataforma.

O acesso aos dados para todos os inquilinos é simples quando todos os dados estão numa base de dados multi-inquilinos. Mas o acesso é mais complexo quando distribuído em escala por potencialmente milhares de bases de dados. Uma forma de domar a complexidade e minimizar o impacto das consultas de análise em dados transacionais é extrair dados numa base de dados ou armazém de dados para fins específicos.

Este tutorial apresenta um cenário de análise completo para a aplicação Wingtip Tickets SaaS. Em primeiro lugar, *a Elastic Jobs* é usada para extrair dados de cada base de dados de inquilinos e carregá-lo em mesas de paragem numa loja de análise. A loja de análise pode ser uma Base de Dados SQL ou uma piscina SQL. Para a extração de dados em larga escala, recomenda-se [a Azure Data Factory.](../../data-factory/introduction.md)

Em seguida, os dados agregados são transformados em um conjunto de tabelas [de esquemas estelares.](https://www.wikipedia.org/wiki/Star_schema) As tabelas consistem numa tabela central de factos mais tabelas de dimensão relacionadas.  Para bilhetes wingtip:

- A tabela central de factos no esquema estelar contém dados de bilhetes.
- As tabelas de dimensões descrevem locais, eventos, clientes e datas de compra.

Juntas, as tabelas centrais de factos e dimensões permitem um processamento analítico eficiente. O esquema de estrelas utilizado neste tutorial é mostrado na seguinte imagem:
 
![arquiteturaOverView](./media/saas-tenancy-tenant-analytics/StarSchema.png)

Finalmente, a loja de análise é questionada usando **o PowerBI** para destacar insights sobre o comportamento do inquilino e o seu uso da aplicação Wingtip Tickets. Faz perguntas que:
 
- Mostrar a popularidade relativa de cada local
- Destacar padrões na venda de bilhetes para diferentes eventos
- Mostrar o relativo sucesso de diferentes locais na venda do seu evento

Compreender como cada inquilino está a usar o serviço é usado para explorar opções para rentabilizar o serviço e melhorar o serviço para ajudar os inquilinos a serem mais bem sucedidos. Este tutorial fornece exemplos básicos dos tipos de insights que podem ser recolhidos a partir de dados do inquilino.

## <a name="setup"></a>Configuração

### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

- A aplicação Wingtip Tickets SaaS Database per Tenant é implementada. Para implementar em menos de cinco minutos, consulte [Implementar e explorar a aplicação Wingtip SaaS](../../sql-database/saas-dbpertenant-get-started-deploy.md)
- Os scripts de base de dados saas de bilhetes de ponta do Wingtip por inquilino e [código fonte](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) de aplicação são descarregados a partir do GitHub. Consulte as instruções de descarregamento. Certifique-se de *desbloquear o ficheiro zip* antes de extrair o seu conteúdo. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para as etapas para descarregar e desbloquear os scripts SaaS dos Bilhetes Wingtip.
- O Power BI Desktop está instalado. [Transferir o Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- O lote de inquilinos adicionais foi a provisionado, ver o [**tutorial de inquilinos da Provisão.**](../../sql-database/saas-dbpertenant-provision-and-catalog.md)
- Foi criada uma conta de trabalho e uma base de dados de contas de trabalho. Consulte os passos adequados no tutorial de [**gestão de Schema.**](../../sql-database/saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent)

### <a name="create-data-for-the-demo"></a>Criar dados para a demonstração

Neste tutorial, a análise é feita nos dados de venda de bilhetes. No passo atual, gera dados de bilhetes para todos os inquilinos.  Posteriormente, estes dados são extraídos para análise. *Certifique-se de que fornece o lote de inquilinos como descrito anteriormente, para que tenha uma quantidade significativa de dados*. Uma quantidade suficientemente grande de dados pode expor uma gama de diferentes padrões de compra de bilhetes.

1. No PowerShell ISE, abra *...\Módulos de aprendizagem\Analytics Operacional\TenantAnalytics\Demo-TenantAnalytics.ps1*, e definir o seguinte valor:
    - **$DemoScenario**  =  **1** Compre bilhetes para eventos em todos os locais
2. Prima **F5** para executar o script e criar histórico de compra de bilhetes para cada evento em cada local.  O guião dura vários minutos para gerar dezenas de milhares de bilhetes.

### <a name="deploy-the-analytics-store"></a>Implementar a loja de análise
Muitas vezes existem numerosas bases de dados transacionais que, em conjunto, detêm todos os dados dos inquilinos. Deve agregar os dados do inquilino das muitas bases de dados transacionais numa única loja de análise. A agregação permite uma consulta eficiente dos dados. Neste tutorial, uma Base de Dados Azure SQL é usada para armazenar os dados agregados.

Nos passos seguintes, você implanta a loja de análise, que é chamada **de tenantanalytics.** Você também implanta tabelas predefinidas que são povoadas mais tarde no tutorial:
1. No PowerShell ISE, abra *...\Módulos de aprendizagem\Analytics Operacional\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Desa esta medida a variável $DemoScenario no script para corresponder à sua escolha de loja de análise:
    - Para utilizar a Base de Dados SQL sem loja de colunas, desempenhe **$DemoScenario**  =  **2**
    - Para utilizar a Base de Dados SQL com a loja de colunas, desempenhe **$DemoScenario**  =  **3**  
3. Prima **F5** para executar o roteiro de demonstração (que chama o roteiro *Deploy-TenantAnalytics \<XX> .ps1)* que cria a loja de análise do inquilino. 

Agora que implementou a aplicação e a preencheu com dados interessantes do inquilino, utilize [o SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para ligar **os inquilinos1-dpt- &lt; Utilizador &gt; ** e ** &lt; &gt; servidores de utilizadores de catálogo** usando Login = *developer*, Password = *P \@ ssword1*. Consulte o [tutorial introdutório](../../sql-database/saas-dbpertenant-wingtip-app-overview.md) para obter mais orientação.

![Screenshot que mostra as informações necessárias para ligar ao SQL Server.](./media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

No Explorador de Objetos, execute os seguintes passos:

1. Expandir o servidor *de utilizadores de 1-dpt- &lt; &gt; inquilinos.*
2. Expanda o nó de bases de dados e consulte a lista de bases de dados de inquilinos.
3. Expandir o *servidor de utilizador do catálogo-dpt. &lt; &gt; *
4. Verifique se vê a loja de análise e a base de dados do jobaccount.

Consulte os seguintes itens de base de dados no SSMS Object Explorer expandindo o nó da loja de análise:

- Os **bilhetes de mesaRawData** e **EventsRawData** detêm dados extraídos em bruto das bases de dados dos inquilinos.
- As mesas de esquema de estrelas são **fact_Tickets,** **dim_Customers,** **dim_Venues,** **dim_Events**e **dim_Dates.**
- O procedimento armazenado é utilizado para povoar as tabelas de esquemas estelares das tabelas de dados brutos.

![Screenshot dos itens de base de dados mostrados no Explorador de Objetos SSMS.](./media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extração de dados 

### <a name="create-target-groups"></a>Criar grupos-alvo 

Antes de prosseguir, certifique-se de que implementou a conta de trabalho e a base de dados de contas de emprego. No próximo conjunto de passos, a Elastic Jobs é usada para extrair dados de cada base de dados de inquilinos e para armazenar os dados na loja de análise. Em seguida, o segundo trabalho destrói os dados e armazena-os em mesas no esquema das estrelas. Estes dois postos de trabalho são contra dois grupos-alvo diferentes, nomeadamente **TenantGroup** e **AnalyticsGroup.** O trabalho de extração é contra o Grupo de Inquilinos, que contém todas as bases de dados dos inquilinos. O trabalho de trituração vai contra o AnalyticsGroup, que contém apenas a loja de análise. Criar os grupos-alvo utilizando os seguintes passos:

1. No SSMS, ligue-se à base **de dados de contas** de trabalho no catálogo-dpt-User &lt; &gt; .
2. Em SSMS, abra *...\Módulos de aprendizagem\Analytics Operacional\Tenant Analytics\ TargetGroups.sql* 
3. Modifique a @User variável no topo do script, substituindo `<User>` pelo valor do utilizador utilizado quando implementou a aplicação Wingtip SaaS.
4. Prima **F5** para executar o script que cria os dois grupos-alvo.

### <a name="extract-raw-data-from-all-tenants"></a>Extrair dados brutos de todos os inquilinos

Podem ocorrer modificações de dados extensivas com mais frequência para os dados dos *bilhetes e clientes* do que para os dados *de eventos e locais.* Por isso, considere extrair os dados dos bilhetes e dos clientes separadamente e com mais frequência do que extrair dados de eventos e locais. Nesta secção, você define e agenda dois trabalhos separados:

- Extrair bilhete e dados do cliente.
- Extrair dados do evento e do local.

Cada trabalho extrai os seus dados e coloca-os na loja de análise. Há um trabalho separado que destrói os dados extraídos no esquema de estrelas analítica.

1. No SSMS, ligue-se à base **de dados de contas** de trabalho no servidor de utilizador do catálogo-dpt. &lt; &gt;
2. No SSMS, abra *...\Módulos de aprendizagem\Analytics Operacional\Tenant Analytics\ExtractTickets.sql*.
3. Modifique @User no topo do script e substitua-o pelo nome de utilizador utilizado quando `<User>` implementou a aplicação Wingtip SaaS 
4. Prima f5 para executar o script que cria e executa o trabalho que extrai bilhetes e dados de clientes de cada base de dados de inquilinos. O trabalho guarda os dados na loja de análise.
5. Consultar a tabela TicketsRawData na base de dados de inquilinos, para garantir que a mesa é preenchida com informações de bilhetes de todos os inquilinos.

![A screenshot mostra a base de dados ExtractTickets com os TicketsRawData d b o selecionados no Object Explorer.](./media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Repita os passos anteriores, exceto que desta vez **substitua \ExtractTickets.sql** por **\ExtractVenuesEvents.sql** no passo 2.

Gerir com sucesso o trabalho povoa a tabela EventsRawData na loja de análise com novos eventos e informações de locais de todos os inquilinos. 

## <a name="data-reorganization"></a>Reorganização de dados

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Triturar dados extraídos para povoar tabelas star-schema

O próximo passo é destruir os dados brutos extraídos num conjunto de tabelas otimizadas para consultas analíticas. Um esquema estelar é usado. Uma tabela central de factos contém registos individuais de venda de bilhetes. Outras tabelas são preenchidas com dados relacionados sobre locais, eventos e clientes. E há tabelas de dimensão do tempo. 

Nesta secção do tutorial, você define e executou um trabalho que funde os dados brutos extraídos com os dados nas tabelas de esquemas estelares. Após o fim do trabalho de fusão, os dados brutos são eliminados, deixando as tabelas prontas a serem povoadas pelo próximo trabalho de extrato de dados do inquilino.

1. No SSMS, ligue-se à base **de dados de contas** de trabalho no catálogo-dpt-User &lt; &gt; .
2. No SSMS, abra *...\Módulos de aprendizagem\Analytics operacional\Tenant Analytics\ShredRawExtractedData.sql*.
3. Prima **F5** para executar o script para definir um trabalho que ligue para o sp_ShredRawExtractedData procedimento armazenado na loja de análise.
4. Dê tempo suficiente para que o trabalho corra com sucesso.
    - Verifique a coluna **lifecycle** da tabela jobs.jobs_execution para obter o estado de trabalho. Certifique-se **de** que o trabalho foi bem sucedido antes de prosseguir. Uma execução bem sucedida exibe dados semelhantes ao seguinte gráfico:

![trituração](./media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Exploração de dados

### <a name="visualize-tenant-data"></a>Visualizar os dados dos inquilinos

Os dados na tabela star-schema fornecem todos os dados de venda de bilhetes necessários para a sua análise. Para facilitar a visualização das tendências em grandes conjuntos de dados, é necessário visualizá-la graficamente.  Nesta secção, aprende-se a usar o **Power BI** para manipular e visualizar os dados do inquilino que extraiu e organizou.

Use os seguintes passos para ligar ao Power BI e importar as vistas que criou anteriormente:

1. Lançar power bi desktop.
2. A partir da fita Home, **selecione Obter Dados,** e selecione **Mais...** do menu.
3. Na janela **Obter Dados,** selecione Azure SQL Database.
4. Na janela de início de sessão de dados, insira o nome do servidor (catalog-dpt- &lt; User &gt; .database.windows.net). Selecione **Importar** para **o Modo de Conectividade de Dados**e, em seguida, clique em OK. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Selecione **a Base de Dados** no painel esquerdo e, em seguida, introduza o nome do utilizador = *desenvolvedor*, e introduza a palavra-passe = *P \@ ssword1*. Clique em **Ligar**.  

    ![A screenshot mostra o diálogo de base de dados SQL Server onde pode introduzir um nome de utilizador e palavra-passe.](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. No painel **Do Navegador,** sob a base de dados de análise, selecione as tabelas star-schema: fact_Tickets, dim_Events, dim_Venues, dim_Customers e dim_Dates. Em seguida, **selecione Carregar**. 

Parabéns! Você carregou os dados com sucesso no Power BI. Agora você pode começar a explorar visualizações interessantes para ajudar a obter insights sobre seus inquilinos. Em seguida, você percorre como a análise pode permitir-lhe fornecer recomendações baseadas em dados para a equipe de negócios Wingtip Tickets. As recomendações podem ajudar a otimizar o modelo de negócio e a experiência do cliente.

Começa por analisar os dados de venda de bilhetes para ver a variação do uso nos locais. Selecione as seguintes opções no Power BI para traçar um gráfico de barras do número total de bilhetes vendidos por cada local. Devido à variação aleatória no gerador de bilhetes, os seus resultados podem ser diferentes.
 
![A screenshot mostra uma visualização e controlos de Power B I para a visualização de dados no lado direito.](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

O lote anterior confirma que o número de bilhetes vendidos por cada local varia. Os locais que vendem mais bilhetes estão a usar o seu serviço mais fortemente do que os locais que vendem menos bilhetes. Pode haver aqui uma oportunidade de adaptar a alocação de recursos de acordo com as diferentes necessidades do inquilino.

Pode analisar ainda mais os dados para ver como as vendas de bilhetes variam ao longo do tempo. Selecione as seguintes opções no Power BI para traçar o número total de bilhetes vendidos por dia por um período de 60 dias.
 
![A screenshot mostra power B I visualização intitulada Distribuição de Venda de Bilhetes versus Dia de Venda.](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

O gráfico anterior mostra o pico de venda de bilhetes para alguns locais. Estes picos reforçam a ideia de que alguns locais podem estar a consumir desproporcionalmente os recursos do sistema. Até agora não há nenhum padrão óbvio quando os picos ocorrem.

Em seguida, você quer investigar o significado destes dias de pico de venda. Quando é que estes picos ocorrem depois de os bilhetes irem à venda? Para traçar bilhetes vendidos por dia, selecione as seguintes opções no Power BI.

![Distribuição do SaleDayD](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

O lote anterior mostra que alguns locais vendem muitos bilhetes no primeiro dia de venda. Assim que os bilhetes estão à venda nestes locais, parece haver uma corrida louca. Esta explosão de atividade por alguns locais pode afetar o serviço para outros inquilinos.

Você pode perfurar os dados novamente para ver se esta corrida louca é verdade para todos os eventos hospedados por estes locais. Em parcelas anteriores, observou que a Salas de Concertos Contoso vende muitos bilhetes, e que o Contoso também tem um pico na venda de bilhetes em determinados dias. Brinque com as opções power bi para traçar vendas cumulativas de bilhetes para a Contoso Concert Hall, focando-se nas tendências de venda para cada um dos seus eventos. Todos os eventos seguem o mesmo padrão de venda?

![ContosoSales](./media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

O enredo anterior para o Contoso Concert Hall mostra que a corrida louca não acontece para todos os eventos. Brinque com as opções do filtro para ver as tendências de venda para outros locais.

As ideias sobre os padrões de venda de bilhetes podem levar os Bilhetes Wingtip a otimizar o seu modelo de negócio. Em vez de cobrar a todos os inquilinos de forma igual, talvez wingtip deva introduzir níveis de serviço com diferentes tamanhos de cálculo. Locais maiores que precisam de vender mais bilhetes por dia poderiam ser oferecidos um nível mais alto com um acordo de nível de serviço mais elevado (SLA). Esses locais poderiam ter as suas bases de dados colocadas em piscina com limites de recursos por base de dados mais elevados. Cada nível de serviço poderia ter uma atribuição de vendas por hora, com taxas adicionais cobradas por exceder a atribuição. Locais maiores que tenham explosões periódicas de vendas beneficiariam dos escalões mais altos, e os Bilhetes Wingtip podem rentabilizar o seu serviço de forma mais eficiente.

Entretanto, alguns clientes da Wingtip Tickets queixam-se de que lutam para vender bilhetes suficientes para justificar o custo do serviço. Talvez nestes conhecimentos haja uma oportunidade de impulsionar a venda de bilhetes para locais com baixo desempenho. As vendas mais elevadas aumentariam o valor percebido do serviço. Clique fact_Tickets à direita e selecione **Nova medida.** Introduza a seguinte expressão para a nova medida **denominada AverageTicketsSold:**

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

Selecione as seguintes opções de visualização para traçar a percentagem de bilhetes vendidos por cada local para determinar o seu sucesso relativo.

![A screenshot mostra a visualização power B I intitulada Average Tickets Sold By Each Venue.](./media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

O lote anterior mostra que, embora a maioria dos locais venda mais de 80% dos seus bilhetes, alguns estão a ter dificuldades em preencher mais de metade dos lugares. Brinque com o Values Well para selecionar a percentagem máxima ou mínima de bilhetes vendidos para cada local.

Mais cedo aprofundou a sua análise para descobrir que as vendas de bilhetes tendem a seguir padrões previsíveis. Esta descoberta pode permitir que os Bilhetes Wingtip ajudem os locais com baixo desempenho a impulsionar a venda de bilhetes, recomendando preços dinâmicos. Esta descoberta pode revelar uma oportunidade de empregar técnicas de machine learning para prever a venda de bilhetes para cada evento. Poderiam também ser feitas previsões para o impacto nas receitas da oferta de descontos na venda de bilhetes. Power BI Embedded poderia ser integrado numa aplicação de gestão de eventos. A integração poderia ajudar a visualizar as vendas previstas e o efeito de diferentes descontos. A aplicação pode ajudar a conceber um desconto ideal para ser aplicado diretamente a partir do visor de análise.

Observou tendências nos dados dos inquilinos a partir da aplicação WingTip. Pode contemplar outras formas de a aplicação informar as decisões empresariais dos fornecedores de aplicações SaaS. Os vendedores podem melhor atender às necessidades dos seus inquilinos. Esperemos que este tutorial lhe tenha equipado as ferramentas necessárias para realizar análises em dados dos inquilinos para capacitar os seus negócios a tomar decisões baseadas em dados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> - Implantado uma base de dados de análise de inquilinos com tabelas de esquemas de estrelas pré-definidas
> - Trabalhos elásticos usados para extrair dados de toda a base de dados de inquilinos
> - Fundir os dados extraídos em tabelas num esquema estelar concebido para análise
> - Consulta de uma base de dados de análise 
> - Use Power BI para visualização de dados para observar tendências nos dados dos inquilinos 

Parabéns!

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais adicionais que se baseiam na aplicação Wingtip SaaS](../../sql-database/saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Trabalhos Elásticos](../../sql-database/elastic-jobs-overview.md).
- [Análise de inquilinos cruzados usando dados extraídos - app multi-inquilino](../../sql-database/saas-multitenantdb-tenant-analytics.md)