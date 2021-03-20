---
title: Consultas de reporte em várias bases de dados
description: Inquilino transversal reportando usando consultas distribuídas.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewers: ''
ms.date: 01/25/2019
ms.openlocfilehash: 18a02b81e459217ccca53d48a08e35a706b071b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793267"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Inter-inquilino reportando usando consultas distribuídas
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você executou consultas distribuídas em todo o conjunto de bases de dados de inquilinos para reportar. Estas consultas podem extrair insights enterrados nos dados operacionais do dia-a-dia dos inquilinos do Wingtip Tickets SaaS. Para tal, implementa uma base de dados de relatórios adicional no servidor do catálogo e utiliza a Consulta Elástica para permitir consultas distribuídas.


Neste tutorial, ficará a saber:

> [!div class="checklist"]
> 
> * Como implementar uma base de dados de relatórios
> * Como executar consultas distribuídas em todas as bases de dados de inquilinos
> * Como as opiniões globais em cada base de dados podem permitir consultas eficientes entre inquilinos


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:


* A aplicação Wingtip Tickets SaaS Database Per Tenant é implementada. Para implementar em menos de cinco minutos, consulte [Implementar e explorar a aplicação Wingtip Tickets SaaS Database per Tenant](./saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps)
* O SQL Server Management Studio (SSMS) está instalado. Para descarregar e instalar sSMS, consulte [o Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Padrão de reporte de inquilinos cruzados

![padrão de consulta distribuído cross-tenant](./media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Uma oportunidade com aplicações SaaS é usar a grande quantidade de dados de inquilinos armazenados na nuvem para obter informações sobre a operação e uso da sua aplicação. Estes insights podem orientar o desenvolvimento de funcionalidades, melhorias de usabilidade e outros investimentos nas suas apps e serviços.

Aceder a estes dados numa única base de dados multi-inquilinos é fácil, mas não será tão fácil se houver uma distribuição à escala através de potencialmente milhares de bases de dados. Uma abordagem é usar [a Consulta Elástica,](elastic-query-overview.md)que permite consultar um conjunto distribuído de bases de dados com esquema comum. Estas bases de dados podem ser distribuídas por diferentes grupos de recursos e subscrições, mas precisam de partilhar um login comum. A Consulta Elástica utiliza uma única base de dados de *cabeça* em que são definidas tabelas externas que espelham tabelas ou vistas nas bases de dados distribuídas (inquilinos). As consultas submetidas para esta base de dados “head” são compiladas para produzir um plano de consultas distribuídas, com partes das consultas enviadas para as bases de dados inquilinas, conforme necessário. A Consulta Elástica utiliza o mapa de fragmentos na base de dados do catálogo para determinar a localização de todas as bases de dados dos inquilinos. A configuração e consulta da base de dados da cabeça são simples usando a padrão [Transact-SQL,](/sql/t-sql/language-reference)e suportam consultas a partir de ferramentas como Power BI e Excel.

Ao distribuir consultas através das bases de dados dos inquilinos, a Elastic Query fornece informações imediatas sobre os dados de produção ao vivo. À medida que a Consulta Elástica retira dados de potencialmente muitas bases de dados, a latência de consulta pode ser superior às consultas equivalentes submetidas a uma única base de dados multi-inquilino. Consultas de design para minimizar os dados que são devolvidos à base de dados principal. A consulta elástica é frequentemente mais adequada para consultar pequenas quantidades de dados em tempo real, em oposição à construção de consultas ou relatórios de análise frequentemente utilizados ou complexos. Se as consultas não funcionarem bem, olhe para o plano de [execução](/sql/relational-databases/performance/display-an-actual-execution-plan) para ver que parte da consulta é empurrada para a base de dados remota e quantos dados estão sendo devolvidos. As consultas que requerem agregação complexa ou processamento analítico podem ser melhores manuseadas através da extração de dados dos inquilinos numa base de dados ou armazém de dados otimizado para consultas analíticas. Este padrão é explicado no tutorial de análise do [inquilino.](saas-tenancy-tenant-analytics.md) 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenha os scripts de aplicação de ingressos saas de ponta de asa por inquilino

Os scripts de base de dados de multi-inquilinos Wingtip SaaS e código fonte de aplicação estão disponíveis no [wingtipTicketsSaAS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para as etapas para descarregar e desbloquear os scripts SaaS dos Bilhetes Wingtip.

## <a name="create-ticket-sales-data"></a>Criar dados de venda de bilhetes

Para executar consultas contra um conjunto de dados mais interessante, crie dados de venda de bilhetes executando o gerador de bilhetes.

1. No *PowerShell ISE,* abra o ... \\ Módulos de Aprendizagem \\ Analytics \\ Adhoc Reportando \\ *Demo-AdhocReporting.ps1* script e definir o seguinte valor:
   * **$DemoScenario** = 1, **Compre bilhetes para eventos em todos os locais.**
2. Prima **F5** para executar o script e gerar venda de bilhetes. Enquanto o guião está em execução, continue os passos neste tutorial. Os dados dos bilhetes são consultados na secção *de consultas distribuídas ad hoc,* por isso aguarde que o gerador de bilhetes esteja completo.

## <a name="explore-the-global-views"></a>Explore as vistas globais

Na aplicação Wingtip Tickets SaaS Database Per Tenant, cada inquilino recebe uma base de dados. Assim, os dados contidos nas tabelas de bases de dados são traçados na perspetiva de um único inquilino. No entanto, ao consultar todas as bases de dados, é importante que a Consulta Elástica possa tratar os dados como se fizesse parte de uma única base de dados lógica, cacitada pelo inquilino. 

Para simular este padrão, um conjunto de pontos de vista 'globais' são adicionados à base de dados dos inquilinos que projetam uma identificação de inquilino em cada uma das tabelas que são consultadas globalmente. Por exemplo, a vista *VenueEvents* adiciona um *VenueId* computorizado às colunas projetadas a partir da tabela *Eventos.* Da mesma forma, as *vistas VenueTicketPurchases* e *VenueTickets* adicionam uma coluna *VenueId* computorizada projetada a partir das respetivas tabelas. Estas vistas são usadas pela Consulta Elástica para paralelizar consultas e empurrá-las para a base de dados de inquilinos remotos apropriado quando uma coluna *VenueId* está presente. Isto reduz drasticamente a quantidade de dados que são devolvidos e resulta num aumento substancial do desempenho para muitas consultas. Estas opiniões globais foram pré-criadas em todas as bases de dados de inquilinos.

1. Abra o SSMS e [ligue-se ao servidor user1- USER dos &lt; &gt; inquilinos.](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms)
1. Expandir **bases de dados,** clique à direita _contosoconcerthall,_ e selecione **Nova Consulta**.
1. Executar as seguintes consultas para explorar a diferença entre as mesas de inquilino único e as vistas globais:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

Nestas vistas, o *VenueId* é calculado como um haxixe do nome Local, mas qualquer abordagem poderia ser usada para introduzir um valor único. Esta abordagem é semelhante à forma como a chave do inquilino é calculada para uso no catálogo.

Para examinar a definição da vista *local:*

1. No **Object Explorer,** **expanda as vistas contosoconcerthall:**  >  

   ![A screenshot mostra o conteúdo do nó Views, incluindo quatro tipos de Venue d b o.](./media/saas-tenancy-cross-tenant-reporting/views.png)

2. Dbo de clique à **direita. Locais.**
3. Selecione **a visualização do script como** CREATE para  >  **a** nova janela do editor de  >  **consultas**

Script qualquer uma das outras vistas *do Local* para ver como eles adicionam o *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Implementar a base de dados utilizada para consultas distribuídas

Este exercício implementa a base de dados _de adhocreporting._ Esta é a base de dados da cabeça que contém o esquema usado para consulta em todas as bases de dados de inquilinos. A base de dados é implantada no servidor de catálogo existente, que é o servidor utilizado para todas as bases de dados relacionadas com a gestão na aplicação da amostra.

1. em *PowerShell ISE,* aberto... \\ Módulos de Aprendizagem \\ Analíticos Operacionais \\ Adhoc Reportando \\ *Demo-AdhocReporting.ps1*. 

1. Definir **$DemoScenario = 2**, _Implementar base de dados de relatórios ad hoc_.

1. Prima **F5** para executar o script e criar a base *de dados de adhocreporting.*

Na secção seguinte, adicione esquema à base de dados para que possa ser usado para executar consultas distribuídas.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configure a base de dados 'cabeça' para executar consultas distribuídas

Este exercício adiciona esquema (fonte de dados externos e definições de tabela externa) à base de dados _de adhocreporting_ para permitir a consulta em todas as bases de dados dos inquilinos.

1. Abra o SQL Server Management Studio e ligue-se à base de dados Adhoc Reporting que criou no passo anterior. O nome da base de dados é *adhocreporting*.
2. Abra ...\Módulos de aprendizagem\Análise Operacional\Adhoc Reporting\ _Initialize-AdhocReportingDB.sql_ em SSMS.
3. Reveja o script SQL e note:

   A Consulta Elástica utiliza uma credencial de base de dados para aceder a cada uma das bases de dados dos inquilinos. Esta credencial deve estar disponível em todas as bases de dados e deve normalmente ser concedida aos direitos mínimos necessários para permitir essas consultas.

    ![Criar credencial](./media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Com a base de dados do catálogo como fonte externa de dados, as consultas são distribuídas em todas as bases de dados registadas no catálogo no momento em que a consulta é executado. Como os nomes do servidor são diferentes para cada implementação, este script obtém a localização da base de dados do catálogo a partir do servidor atual (@ @servername ) onde o script é executado.

    ![criar fonte de dados externos](./media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   As tabelas externas que referenciam as vistas globais descritas na secção anterior, e definidas com **DISTRIBUIÇÃO = SHARDED(VenueId)**. Como cada *VenueId mapeia* para uma base de dados individual, isto melhora o desempenho para muitos cenários, como mostrado na secção seguinte.

    ![criar tabelas externas](./media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   A mesa local _VenueTypes_ que é criada e povoada. Esta tabela de dados de referência é comum em todas as bases de dados dos inquilinos, pelo que pode ser representada aqui como uma tabela local e povoada com os dados comuns. Para algumas consultas, ter esta tabela definida na base de dados da cabeça pode reduzir a quantidade de dados que precisa de ser movido para a base de dados principal.

    ![criar tabela](./media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Se incluir tabelas de referência desta forma, certifique-se de atualizar o esquema de tabela e os dados sempre que atualizar as bases de dados do inquilino.

4. Prima **F5** para executar o script e inicializar a base *de dados de adhocreporting.* 

Agora você pode executar consultas distribuídas, e recolher insights em todos os inquilinos!

## <a name="run-distributed-queries"></a>Executar consultas distribuídas

Agora que a base de dados *de adhocreporting* está configurada, vá em frente e execute algumas consultas distribuídas. Inclua o plano de execução para uma melhor compreensão de onde está a decorrer o processamento de consultas. 

Ao inspecionar o plano de execução, paire sobre os ícones do plano para obter detalhes. 

Importante notar, é que a definição **DE DISTRIBUIÇÃO = SHARDED (VenueId)** quando a fonte de dados externa é definida melhora o desempenho para muitos cenários. À medida que cada *VenueId* mapeia para uma base de dados individual, a filtragem é facilmente feita remotamente, devolvendo apenas os dados necessários.

1. Abrir... \\ Módulos \\ de Aprendizagem Analytics Operacional \\ Adhoc Reportando \\ *Demo-AdhocReportingQueries.sql* em SSMS.
2. Certifique-se de que está ligado à base de dados **de denúncias.**
3. Selecione o menu **de consulta** e clique **em Incluir Plano de Execução Real**
4. Destaque para quais os  *locais que estão registados atualmente?*

   A consulta devolve toda a lista de locais, ilustrando a rapidez e facilidade com que é consulta em todos os inquilinos e devolver dados de cada inquilino.

   Inspecione o plano e verifique se todo o custo está na consulta remota. Cada base de dados de inquilinos executa a consulta remotamente e devolve a informação do local à base de dados principal.

   ![SELECIONE * DO DBO. Locais](./media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Selecione a próxima consulta e prima **F5**.

   Esta consulta junta dados das bases de dados dos inquilinos e da tabela *Local VenueTypes* (local, uma vez que é uma tabela na base de dados *de adhocreporting).*

   Inspecione o plano e verifique se a maior parte do custo é a consulta remota. Cada base de dados de inquilinos devolve a informação do local e realiza uma junção local com a tabela *Local VenueTypes* para exibir o nome amigável.

   ![Junte-se a dados remotos e locais](./media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Agora selecione o *Em que dia foram mais vendidos?* Consulta, e pressione **F5**.

   Esta consulta faz uma união e agregação um pouco mais complexas. A maior parte do processamento ocorre remotamente.  Apenas linhas individuais, contendo a contagem diária de bilhetes de cada local por dia, são devolvidas à base de dados principal.

   ![consulta](./media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Executar consultas distribuídas em todas as bases de dados do inquilino
> * Implemente uma base de dados de relatórios e defina o esquema necessário para executar consultas distribuídas.


Agora experimente o [tutorial do Tenant Analytics](saas-tenancy-tenant-analytics.md) para explorar a extração de dados para uma base de dados de análise separada para um processamento de análise mais complexo.

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na base de dados saass database por inquilino](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Consulta elástica](elastic-query-overview.md)