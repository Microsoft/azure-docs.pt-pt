---
title: Executar consultas de análise
description: Consultas de análise entre locatários usando dados extraídos de vários bancos de dados de banco de dados SQL do Azure em um aplicativo multilocatário.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
ms.date: 09/19/2018
ms.openlocfilehash: 067afd09f942b8062825553a3cf90f715e8d3938
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822150"
---
# <a name="cross-tenant-analytics-using-extracted-data---multi-tenant-app"></a>Análise entre locatários usando dados extraídos-aplicativo de vários locatários
 
Neste tutorial, você percorre um cenário de análise completa para uma implementação multilocatário. O cenário demonstra como a análise pode permitir que as empresas tomem decisões inteligentes. Com o uso de dados extraídos do banco de dado fragmentado, você usa a análise para obter informações sobre o comportamento do locatário, incluindo o uso do aplicativo SaaS Wingtip tickets de exemplo. Esse cenário envolve três etapas: 

1.  **Extrair dados** de cada banco de dado de locatário para um repositório de análise.
2.  **Otimize os dados extraídos** para processamento de análise.
3.  Use as ferramentas de **Business Intelligence** para extrair informações úteis, que podem orientar a tomada de decisões. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Crie o repositório de análise de locatário para extrair os dados.
> - Use trabalhos elásticos para extrair dados de cada banco de dado de locatário para o repositório de análise.
> - Otimizar os dados extraídos (reorganizar em um esquema em estrela).
> - Consulte o banco de dados de análise.
> - Use Power BI para visualização de dados para realçar tendências em dados de locatário e fazer recomendações para melhorias.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Padrão de análise de locatário offline

Os aplicativos SaaS que você desenvolve têm acesso a uma vasta quantidade de dados de locatário armazenados na nuvem. Os dados fornecem uma fonte avançada de informações sobre a operação e o uso do seu aplicativo e sobre o comportamento dos locatários. Essas informações podem guiar o desenvolvimento de recursos, melhorias de usabilidade e outros investimentos no aplicativo e na plataforma.

O acesso aos dados para todos os locatários é simples quando todos os dados estão em apenas um banco de dado de vários locatários. Mas o acesso é mais complexo quando distribuído em escala em milhares de bancos de dados. Uma maneira de controlar a complexidade é extrair os dados para um banco de dado de análise ou um data warehouse. Em seguida, você consulta a data warehouse para coletar informações dos dados de tíquetes de todos os locatários.

Este tutorial apresenta um cenário de análise completa para este aplicativo SaaS de exemplo. Primeiro, os trabalhos elásticos são usados para agendar a extração de dados de cada banco de dado de locatário. Os dados são enviados para um repositório de análise. O repositório de análise pode ser um banco de dados SQL ou um SQL Data Warehouse. Para extração de dados em larga escala, [Azure data Factory](../data-factory/introduction.md) é recomendado.

Em seguida, os dados agregados são destruídos em um conjunto de tabelas de [esquema em estrela](https://www.wikipedia.org/wiki/Star_schema) . As tabelas consistem em uma tabela de fatos central mais tabelas de dimensões relacionadas:

- A tabela de fatos central no esquema em estrela contém dados de tíquete.
- As tabelas de dimensões contêm dados sobre locais, eventos, clientes e datas de compra.

Juntas, as tabelas central e de dimensão permitem um processamento analítico eficiente. O esquema em estrela usado neste tutorial é exibido na imagem a seguir:
 
![StarSchema](media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Por fim, as tabelas de esquema em estrela são consultadas. Os resultados da consulta são exibidos visualmente para realçar informações sobre o comportamento do locatário e seu uso do aplicativo. Com esse esquema em estrela, você pode executar consultas que ajudam a descobrir itens como o seguinte:

- Quem está comprando tíquetes e de qual local.
- Padrões e tendências ocultos nas seguintes áreas:
    - As vendas de tíquetes.
    - A popularidade relativa de cada local.

Entender o quão consistentemente cada locatário está usando o serviço oferece uma oportunidade de criar planos de serviço para atender às suas necessidades. Este tutorial fornece exemplos básicos de informações que podem ser obtidas de dados de locatário.

## <a name="setup"></a>Configurar

### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

- O aplicativo de banco de dados multilocatário do Wingtip tickets SaaS é implantado. Para implantar em menos de cinco minutos, consulte [implantar e explorar o aplicativo de banco de dados multilocatário do Wingtip tickets SaaS](saas-multitenantdb-get-started-deploy.md)
- Os scripts de SaaS do Wingtip e o [código-fonte](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) do aplicativo são baixados do github. Certifique-se de *desbloquear o arquivo zip* antes de extrair seu conteúdo. Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip tickets.
- O Power BI Desktop está instalado. [Baixar Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- O lote de locatários adicionais foi provisionado, consulte o [**tutorial provisionar locatários**](saas-multitenantdb-provision-and-catalog.md).
- Um agente de trabalho e um banco de dados do agente de trabalho foram criados. Consulte as etapas apropriadas no [**tutorial de gerenciamento de esquema**](saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Criar dados para a demonstração

Neste tutorial, a análise é executada em dados de vendas de tíquetes. Na etapa atual, você gera dados de tíquete para todos os locatários.  Posteriormente, esses dados são extraídos para análise. *Verifique se você provisionou o lote de locatários conforme descrito anteriormente, para que você tenha uma quantidade significativa de dados*. Uma quantidade suficientemente grande de dados pode expor um intervalo de diferentes padrões de compra de tíquetes.

1. No **ISE do PowerShell**, abra *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*e defina o seguinte valor:
    - **$DemoScenario** = **1** comprar tíquetes para eventos em todos os locais
2. Pressione **F5** para executar o script e criar o histórico de compras de tíquetes para cada evento em cada local.  O script é executado por vários minutos para gerar dezenas de milhares de tíquetes.

### <a name="deploy-the-analytics-store"></a>Implantar o repositório de análise
Muitas vezes, há vários bancos de dados fragmentados transacionais que juntos armazenam todo dado de locatário. Você deve agregar os dados de locatário do banco de dado fragmentado em um repositório de análise. A agregação permite uma consulta eficiente dos dados. Neste tutorial, um banco de dados SQL do Azure é usado para armazenar o dado agregado.

Nas etapas a seguir, você implanta o repositório de análise, que é chamado de **tenantanalytics**. Você também implanta tabelas predefinidas que são preenchidas posteriormente no tutorial:
1. No ISE do PowerShell, abra *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Defina a variável $DemoScenario no script para corresponder à sua escolha de repositório de análise. Para fins de aprendizado, o banco de dados SQL sem columnstore é recomendado.
    - Para usar o banco de dados SQL sem columnstore, defina **$DemoScenario** = **2**
    - Para usar o banco de dados SQL com columnstore, defina **$DemoScenario** = **3**  
3. Pressione **F5** para executar o script de demonstração (que chama o script *Deploy-TenantAnalytics\<XX >. ps1* ) que cria o repositório de análise de locatário. 

Agora que você implantou o aplicativo e o preencheu com dados de locatário interessantes, use [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para conectar o usuário do **tenants1-mt-\<\>** de usuários e os servidores de **\>do usuário do Catalog-MT-\<** usando Login = *Developer*, password = *P\@ssword1*.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

No Pesquisador de objetos, execute as seguintes etapas:

1. Expanda o servidor de *\>de usuário tenants1-MT-\<* .
2. Expanda o nó bancos de dados e consulte *tenants1* Database contendo vários locatários.
3. Expanda o servidor de *\>de usuário Catalog-MT-\<* .
4. Verifique se você vê o repositório de análise e o banco de dados jobaccount.

Consulte os seguintes itens de banco de dados no Pesquisador de objetos do SSMS expandindo o nó do repositório de análise:

- As tabelas **TicketsRawData** e **EventsRawData** mantêm dados extraídos brutos dos bancos de dado de locatário.
- As tabelas de esquema em estrela são **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**e **dim_Dates**.
- O procedimento armazenado **sp_ShredRawExtractedData** é usado para preencher as tabelas de esquema em estrela das tabelas de dados brutos.

![tenantAnalytics](media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extração de dados 

### <a name="create-target-groups"></a>Criar grupos de destino 

Antes de continuar, certifique-se de ter implantado a conta de trabalho e o banco de dados jobaccount. No próximo conjunto de etapas, os trabalhos elásticos são usados para extrair dados do banco de dados de locatários fragmentados e para armazenar os dados no repositório de análise. Em seguida, o segundo trabalho destrói os dados e os armazena em tabelas no esquema em estrela. Esses dois trabalhos são executados em dois grupos de destino diferentes, ou seja, o grupo de **locatários** e o grupo de **análise**. O trabalho de extração é executado em relação ao usertenant, que contém todos os bancos de dados de locatário. O trabalho de fragmentação é executado no The Analytics, que contém apenas o repositório de análise. Crie os grupos de destino usando as seguintes etapas:

1. No SSMS, conecte-se ao banco de dados **jobaccount** no\>de usuário Catalog-mt-\<.
2. No SSMS, abra *. ..\Learning Modules\Operational Analytics\Tenant Analytics \ TargetGroups. SQL* 
3. Modifique a variável @User na parte superior do script, substituindo `<User>` pelo valor de usuário usado quando você implantou o aplicativo de banco de dados multilocatário do Wingtip tickets SaaS.
4. Pressione **F5** para executar o script que cria os dois grupos de destino.

### <a name="extract-raw-data-from-all-tenants"></a>Extrair dados brutos de todos os locatários

As transações podem ocorrer com mais frequência para dados de *tíquete e de cliente* do que para dados de *eventos e locais* . Portanto, considere extrair dados de tíquetes e clientes separadamente e com mais frequência do que você extrai dados de eventos e locais. Nesta seção, você definirá e agendará dois trabalhos separados:

- Extraia dados de tíquete e cliente.
- Extrair dados de eventos e locais.

Cada trabalho extrai seus dados e os publica no repositório de análise. Lá, um trabalho separado destrói os dados extraídos no esquema do Analytics Star.

1. No SSMS, conecte-se ao banco de dados **jobaccount** em Catalog-mt-\<\> do usuário.
2. No SSMS, abra *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.SQL*.
3. Modifique @User na parte superior do script e substitua `<User>` pelo nome de usuário usado quando você implantou o aplicativo de banco de dados multilocatário do Wingtip tickets SaaS. 
4. Pressione **F5** para executar o script que cria e executa o trabalho que extrai os dados de tíquetes e clientes de cada banco de dado de locatário. O trabalho salva os dados no repositório de análise.
5. Consulte a tabela TicketsRawData no banco de dados tenantanalytics para garantir que a tabela seja populada com informações de tíquetes de todos os locatários.

![ticketExtracts](media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Repita as etapas anteriores, exceto que, desta vez, substitua **\ExtractTickets.SQL** por **\ExtractVenuesEvents.SQL** na etapa 2.

A execução bem-sucedida do trabalho popula a tabela EventsRawData no repositório de análise com novas informações de eventos e locais de todos os locatários. 

## <a name="data-reorganization"></a>Reorganização de dados

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Destruir dados extraídos para popular tabelas de esquema em estrela

A próxima etapa é destruir os dados brutos extraídos em um conjunto de tabelas otimizadas para consultas de análise. Um esquema em estrela é usado. Uma tabela de fatos central contém registros individuais de vendas de tíquetes. As tabelas de dimensões são preenchidas com dados sobre locais, eventos, clientes e datas de compra. 

Nesta seção do tutorial, você define e executa um trabalho que mescla os dados brutos extraídos com os dados nas tabelas de esquema em estrela. Depois que o trabalho de mesclagem for concluído, os dados brutos serão excluídos, deixando as tabelas prontas para serem preenchidas pelo próximo trabalho de extração de dados de locatário.

1. No SSMS, conecte-se ao banco de dados **jobaccount** no\>de usuário Catalog-mt-\<.
2. No SSMS, abra *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.SQL*.
3. Pressione **F5** para executar o script para definir um trabalho que chama o procedimento armazenado sp_ShredRawExtractedData no repositório de análise.
4. Aguarde tempo suficiente para que o trabalho seja executado com êxito.
    - Verifique a coluna **ciclo de vida** dos trabalhos. jobs_execution tabela para o status do trabalho. Verifique se o trabalho **foi bem-sucedido** antes de continuar. Uma execução bem-sucedida exibe dados semelhantes ao seguinte gráfico:

![shreddingJob](media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Exploração de dados

### <a name="visualize-tenant-data"></a>Visualizar dados de locatário

Os dados na tabela de esquema em estrela fornecem todos os dados de vendas de tíquetes necessários para sua análise. Para facilitar a visualização de tendências em grandes conjuntos de dados, você precisa visualizá-los graficamente.  Nesta seção, você aprenderá a usar **Power bi** para manipular e visualizar os dados de locatário que você extraiu e organizou.

Use as etapas a seguir para se conectar ao Power BI e importar os modos de exibição criados anteriormente:

1. Inicie o Power BI desktop.
2. Na faixa de opções página inicial, selecione **obter dados**e selecione **mais...** no menu.
3. Na janela **obter dados** , selecione Azure SQL Database.
4. Na janela de logon do banco de dados, insira o nome do servidor (Catalog-MT-\<usuário\>. database.windows.net). Selecione **importar** para o **modo de conectividade de dados**e clique em OK. 

    ![powerBISignIn](media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. Selecione **banco de dados** no painel esquerdo, digite user name = *Developer*e digite password = *P\@ssword1*. Clique em **Ligar**.  

    ![DatabaseSignIn](media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. No painel **navegador** , no banco de dados de análise, selecione as tabelas de esquema em estrela: fact_Tickets, dim_Events, dim_Venues, dim_Customers e dim_Dates. Em seguida, selecione **carregar**. 

Parabéns! Você carregou os dados com êxito em Power BI. Agora você pode começar a explorar visualizações interessantes para ajudar a obter informações sobre seus locatários. Em seguida, você percorre como a análise pode permitir que você forneça recomendações controladas por dados para a equipe de negócios da Wingtip tickets. As recomendações podem ajudar a otimizar o modelo de negócios e a experiência do cliente.

Você começa analisando dados de vendas de tíquetes para ver a variação no uso entre os locais. Selecione as opções a seguir em Power BI para plotar um gráfico de barras do número total de tíquetes vendidos por cada local. Devido à variação aleatória no gerador de tíquetes, os resultados podem ser diferentes.
 
![TotalTicketsByVenues](media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

A plotagem anterior confirma que o número de tíquetes vendidos por cada local varia. Os locais que vendem mais tíquetes estão usando seu serviço mais fortemente do que os locais que vendem menos tíquetes. Pode haver uma oportunidade para ajustar a alocação de recursos de acordo com as diferentes necessidades de locatário.

Você pode analisar os dados mais detalhadamente para ver como as vendas de tíquetes variam ao longo do tempo. Selecione as opções a seguir em Power BI para plotar o número total de tíquetes vendidos todos os dias por um período de 60 dias.
 
![SaleVersusDate](media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

O gráfico anterior exibe o aumento de vendas de tíquetes para alguns locais. Esses picos reforçam a ideia de que alguns locais podem consumir recursos do sistema desproporcionalmente. Até agora, não há nenhum padrão óbvio no momento em que os picos ocorrem.

Em seguida, você deseja investigar melhor o significado desses dias de venda de pico. Quando esses picos ocorrem depois que os tíquetes passam pela venda? Para plotar os tíquetes vendidos por dia, selecione as opções a seguir em Power BI.

![SaleDayDistribution](media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

A plotagem anterior mostra que alguns locais vendem muitos tíquetes no primeiro dia da venda. Assim que os tíquetes passam pela venda nesses locais, parece haver uma urgência de Mad. Essa intermitência de atividade por alguns locais pode afetar o serviço para outros locatários.

Você pode analisar os dados novamente para ver se essa urgência é verdadeira para todos os eventos hospedados por esses locais. Nas plotagens anteriores, você observou que a contoso Concert Hall vende muitos tíquetes e que a contoso também tem um pico de vendas de ingressos em determinados dias. Experimente as opções de Power BI para plotar vendas de tíquetes cumulativos para o contoso Concert Hall, concentrando-se em tendências de venda para cada um de seus eventos. Todos os eventos seguem o mesmo padrão de venda?

![ContosoSales](media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

O gráfico anterior da Contoso Concert Hall mostra que a urgência de Mad não acontece para todos os eventos. Experimente as opções de filtro para ver as tendências de venda de outros locais.

As informações sobre os padrões de venda de ingressos podem liderar os tíquetes Wingtip para otimizar seu modelo de negócios. Em vez de carregar todos os locatários igualmente, talvez a Wingtip deva introduzir camadas de serviço com diferentes tamanhos de computação. Locais maiores que precisam vender mais tíquetes por dia podem receber uma camada mais alta com um SLA (contrato de nível de serviço) mais alto. Esses locais podem ter seus bancos de dados colocados no pool com limites de recursos de banco de dados maiores. Cada camada de serviço pode ter uma alocação de vendas por hora, com taxas adicionais cobradas para exceder a alocação. Os locais maiores que têm picos de vendas periódicos se beneficiarão das camadas mais altas, e os tíquetes Wingtip podem monetizarr seu serviço com mais eficiência.

Enquanto isso, alguns clientes da Wingtip tickets reclamam que eles lutam para vender tíquetes suficientes para justificar o custo do serviço. Talvez, nessas informações, haja uma oportunidade de impulsionar as vendas de ingressos para a execução de locais. As vendas mais altas aumentarão o valor percebido do serviço. Clique com o botão direito do mouse em fact_Tickets e selecione **nova medida**. Insira a seguinte expressão para a nova medida chamada **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selecione as opções de visualização a seguir para plotar os tíquetes de porcentagem vendidos por cada local para determinar seu sucesso relativo.

![analyticsViews](media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

A plotagem anterior mostra que, embora a maioria dos locais venda mais de 80% de seus tíquetes, algumas estão lutando para preencher mais do que metade das estações. Experimente com os valores bem para selecionar o percentual máximo ou mínimo de tíquetes vendidos para cada local.

Anteriormente, você aprofundau sua análise para descobrir que as vendas de ingressos tendem a seguir padrões previsíveis. Essa descoberta pode permitir que os tíquetes Wingtip ajudem a executar locais a impulsionar as vendas de tíquetes, recomendando o preço dinâmico. Essa descoberta pode revelar a oportunidade de empregar técnicas de aprendizado de máquina para prever as vendas de ingressos para cada evento. As previsões também podem ser feitas para o impacto da receita de oferta de descontos em vendas de ingressos. Power BI Embedded pode ser integrado a um aplicativo de gerenciamento de eventos. A integração pode ajudar a visualizar as vendas previstas e o efeito de diferentes descontos. O aplicativo pode ajudar a desenvolver um desconto ideal para ser aplicado diretamente da exibição da análise.

Você observou tendências em dados de locatário do aplicativo de banco de dados multilocatário do Wingtip tickets SaaS. Você pode contemplar outras maneiras pelas quais o aplicativo pode informar decisões de negócios para fornecedores de aplicativos SaaS. Os fornecedores podem atender melhor às necessidades de seus locatários. Espero que este tutorial tenha equipado com ferramentas necessárias para executar a análise de dados de locatário para capacitar seus negócios a tomar decisões controladas por dados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> - Implantou um banco de dados de análise de locatário com tabelas de esquema de estrela predefinidas
> - Trabalhos elásticos usados para extrair dados de todo o banco de dado de locatário
> - Mesclar os dados extraídos em tabelas em um esquema em estrela projetado para análise
> - Consultar um banco de dados de análise 
> - Usar Power BI para visualização de dados para observar tendências em dados de locatário 

Parabéns!

## <a name="additional-resources"></a>Recursos adicionais

[Tutoriais adicionais que se baseiam no aplicativo SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials). 
- [Trabalhos elásticos](elastic-jobs-overview.md).
- [Análise entre locatários usando dados extraídos-aplicativo de locatário único](saas-tenancy-tenant-analytics.md) 