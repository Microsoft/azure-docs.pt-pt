---
title: Execute o relatório de consultas em várias bases de dados SQL do Azure | Documentos da Microsoft
description: Relatórios de utilização entre inquilinos distribuído consultas.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewers: billgib,ayolubek
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 9562d0cd1ad97a459c3630456a6070ac2b6e63f3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096032"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Consultas de distribuídas de relatórios usando entre inquilinos

Neste tutorial, executa consultas distribuídas em todo o conjunto de inquilino bases de dados de relatórios. Estas consultas podem extrair informações escondidas nos dados operacionais dos inquilinos Wingtip Tickets SaaS. Para fazer isso, implementar uma base de dados de relatórios adicional para o servidor de catálogo e utilizar a consulta elástica para ativar consultas distribuídas.


Neste tutorial, ficará a saber:

> [!div class="checklist"]
> 
> * Como implementar uma base de dados de relatórios
> * Como executar consultas distribuídas em todas as bases de dados de inquilinos
> * Como podem ativar a vistas globais em cada base de dados de uma consulta eficiente em inquilinos


Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:


* A aplicação Wingtip Tickets SaaS da base de dados por inquilino é implementada. Para implementar em menos de cinco minutos, veja [implementar e explorar a aplicação Wingtip Tickets SaaS da base de dados por inquilino](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) é instalado. Para transferir e instalar o SSMS, consulte [Baixe o SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Padrão de geração de relatórios entre inquilinos

![padrão de consulta distribuída entre inquilinos](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Uma oportunidade com aplicações SaaS consiste em utilizar grandes quantidades de dados de inquilino armazenados na cloud para obter informações sobre a operação e a utilização da sua aplicação. Estas informações podem ajudá-desenvolvimento de funcionalidade, usabilidade e a outros investimentos nas suas aplicações e serviços.

Aceder a estes dados numa única base de dados multi-inquilinos é fácil, mas não será tão fácil se houver uma distribuição à escala através de potencialmente milhares de bases de dados. Uma abordagem é usar [consulta elástica](sql-database-elastic-query-overview.md), que permite consultas entre um conjunto distribuído de bases de dados com esquema comum. Esses bancos de dados podem ser distribuídos por diferentes grupos de recursos e subscrições, mas precisam de partilhar um início de sessão comuns. Consulta elástica utiliza uma única *head* base de dados em que são definidas tabelas externas que espelham tabelas ou vistas nos bancos de dados distribuídas (inquilino). As consultas submetidas para esta base de dados “head” são compiladas para produzir um plano de consultas distribuídas, com partes das consultas enviadas para as bases de dados inquilinas, conforme necessário. Consulta elástica utiliza o mapa de partições horizontais da base de dados de catálogo para determinar a localização de todas as bases de dados do inquilino. Configuração e a consulta da base de dados principal são simples de utilizar o padrão [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)e oferecem suporte à consulta de ferramentas, como o Power BI e Excel.

Ao distribuir consultas entre bases de dados de inquilino, a consulta elástica fornece informações imediatas sobre os dados de produção em direto. Como a consulta elástica extrai dados da potencialmente muitas bases de dados, latência de consulta pode ser superior a consultas equivalentes submetidas para uma única base de dados do multi-inquilino. Criar consultas para minimizar os dados que são retornados para a base de dados principal. Consulta elástica seja muitas vezes, mais adequada para a consulta de pequenas quantidades de dados em tempo real, em vez de construção usados com freqüência ou consultas de análise complexas ou relatórios. Se as consultas não funcionam tão bem, observe a [plano de execução](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) para ver que parte da consulta é empurrada para a base de dados remoto e a quantidade de dados está a ser devolvido. Consultas que exigem a agregação complexa ou processamento analítico poderão ser melhor identificadores por extrair dados de inquilino para um armazém de dados ou base de dados otimizado para consultas de análise. Este padrão é explicado no [tutorial de análise de inquilinos](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de aplicativo da Wingtip Tickets SaaS da base de dados por inquilino

Os scripts de base de dados do Wingtip Tickets SaaS multi-inquilino e o código de origem da aplicação estão disponíveis no [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repositório do GitHub. Veja a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip Tickets SaaS de desbloqueio.

## <a name="create-ticket-sales-data"></a>Criar dados de vendas de pedido de suporte

Para executar consultas em relação a um conjunto de dados mais interessante, crie dados de vendas de pedido de suporte ao executar o gerador de pedido de suporte.

1. Na *ISE do PowerShell*, abra o... \\Módulos de aprendizagem\\análise operacional\\relatórios ad hoc\\*demonstração AdhocReporting.ps1* do script e defina o valor seguinte:
   * **$DemoScenario** = 1, **comprar bilhetes para eventos em todos os locais**.
2. Prima **F5** para executar o script e gerar vendas de bilhetes. Enquanto estiver a executar o script, continue os passos neste tutorial. Os dados de pedido de suporte são consultados no *execução ad hoc distribuído consultas* secção, por isso, aguarde que o gerador de pedido de suporte concluir.

## <a name="explore-the-global-views"></a>Explore as vistas globais

Na aplicação Wingtip Tickets SaaS da base de dados por inquilino, cada inquilino é atribuído uma base de dados. Portanto, os dados contidos nas tabelas da base de dados é o âmbito da perspetiva de um único inquilino. No entanto, ao consultar em todas as bases de dados, é importante que a consulta elástica pode tratar os dados, como se faz parte de um único banco de dados lógico em partição horizontal pelo inquilino. 

Para simular esse padrão, um conjunto de vistas 'globais' são adicionados à base de dados do inquilino desse projeto um ID de inquilino em cada uma das tabelas que são consultadas globalmente. Por exemplo, o *VenueEvents* vista adiciona um calculada *VenueId* às colunas previstas do *eventos* tabela. Da mesma forma, o *VenueTicketPurchases* e *VenueTickets* vistas de adicionar um calculada *VenueId* coluna previsto de suas respectivas tabelas. Estas vistas são utilizadas por consulta elástica a paralelização de consultas e envie-os para baixo para o inquilino remoto adequado da base de dados quando um *VenueId* coluna está presente. Isso reduz drasticamente a quantidade de dados que são devolvidos e resulta num aumento substancial no desempenho para muitas consultas. Estas vistas globais foram pré-criada em todas as bases de dados de inquilinos.

1. Abra o SSMS e [ligue-se ao tenants1 -&lt;USUÁRIO&gt; servidor](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Expanda **bases de dados**, clique com botão direito _contosoconcerthall_e selecione **nova consulta**.
1. Execute as seguintes consultas para explorar a diferença entre as tabelas de inquilino único e os modos de exibição global:

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

Destas vistas, o *VenueId* é computado como um hash do nome do local, mas qualquer abordagem poderia ser usado para introduzir um valor exclusivo. Essa abordagem é semelhante à forma como a chave de inquilino é calculada para utilização no catálogo.

Para examinar a definição do *locais* vista:

1. Na **Object Explorer**, expanda **contosoconcerthall** > **vistas**:

   ![visualizações](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Com o botão direito **dbo. Locais**.
3. Selecione **vista como de Script** > **criar para** > **nova janela do Editor de consultas**

Criar scripts qualquer uma das outras *foro* vistas para ver como adicionar a *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Implementar a base de dados utilizado para consultas distribuídas

Este exercício implementa a _adhocreporting_ base de dados. Esta é a base de dados principal que contém o esquema usado para consultar em todas as bases de dados do inquilino. A base de dados é implementada para o servidor de catálogo existente, o que é o servidor utilizado para todas as bases de dados relacionados com a gestão na aplicação de exemplo.

1. na *ISE do PowerShell*, abra... \\Módulos de aprendizagem\\análise operacional\\relatórios ad hoc\\*demonstração AdhocReporting.ps1*. 

1. Definir **$DemoScenario = 2**, _hoc Ad implementar base de dados relatórios_.

1. Prima **F5** para executar o script e criar o *adhocreporting* base de dados.

Na próxima seção, adicionar esquema para a base de dados para que possa ser utilizada para executar consultas distribuídas.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configurar a base de dados "head" para a execução de consultas distribuídas

Neste exercício adiciona esquema (a origem de dados externa e definições de tabela externa) para o _adhocreporting_ base de dados para ativar consultas entre todas as bases de dados de inquilinos.

1. Abra o SQL Server Management Studio e ligue à base de dados de relatórios ad hoc que criou no passo anterior. O nome da base de dados é *adhocreporting*.
2. Abrir ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _Initialize AdhocReportingDB.sql_ no SSMS.
3. Reveja o script SQL e tenha em atenção:

   Consulta elástica utiliza uma credencial com âmbito de base de dados para aceder a cada uma das bases de dados do inquilino. Esta credencial tem de estar disponíveis em todas as bases de dados e devem normalmente ser concedidos os direitos mínimo necessário para permitir estas consultas.

    ![Criar credencial](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Com a base de dados de catálogo como a origem de dados externas, as consultas são distribuídas para todas as bases de dados registadas no catálogo no momento que a consulta é executada. Como os nomes dos servidores são diferentes para cada implementação, este script obtém a localização da base de dados do catálogo do servidor atual (@@servername) em que o script é executado.

    ![Criar origem de dados externa](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   As tabelas externas que fazem referência as vistas globais descrito na secção anterior e definido com **distribuição = SHARDED(VenueId)**. Uma vez que cada *VenueId* é mapeado para uma base de dados individual, isso melhora o desempenho para muitos cenários, como mostrado na próxima seção.

    ![criar tabelas externas](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   A tabela local _VenueTypes_ que é criado e preenchido. Esta tabela de dados de referência é comum em todas as bases de dados de inquilinos, pelo que pode ser representado aqui como uma tabela local e preenchida com o common data. Para algumas consultas, ter nesta tabela são definidos na base de dados principal pode reduzir a quantidade de dados que precisam ser movidos para a base de dados principal.

    ![Criar tabela](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Se incluir as tabelas de referência desta forma, certifique-se de que atualizar o esquema de tabela e os dados sempre que atualiza os bancos de dados do inquilino.

4. Prima **F5** para executar o script e inicializar o *adhocreporting* base de dados. 

Agora pode executar consultas distribuídas e recolher informações de todos os inquilinos!

## <a name="run-distributed-queries"></a>Executar consultas distribuídas

Agora que o *adhocreporting* base de dados está a configurar, vá em frente e executar algumas consultas distribuídas. Incluem o plano de execução para uma melhor compreensão de onde o processamento de consulta está a acontecer. 

Quando inspecionar o plano de execução, Paire o rato sobre os ícones de plano para obter detalhes. 

Importante observar, é essa definição **distribuição = SHARDED(VenueId)** quando a origem de dados externa é definida melhora o desempenho para muitos cenários. À medida que cada *VenueId* é mapeado para uma base de dados individual, filtragem pode é feita facilmente remotamente, retornar apenas os dados necessários.

1. Abra... \\Módulos de aprendizagem\\análise operacional\\relatórios ad hoc\\*demonstração AdhocReportingQueries.sql* no SSMS.
2. Certifique-se de que está ligado para o **adhocreporting** base de dados.
3. Selecione o **consulta** menu e clique em **Include Actual Execution Plan**
4. Realce o *quais locais estão atualmente registados?* consulta e prima **F5**.

   A consulta devolve a lista de todo local, que ilustra quão rápido, e é fácil de consultar em todos os inquilinos e devolver dados de cada inquilino.

   Inspecione o plano e ver que o custo total é na consulta remota. Cada base de dados de inquilinos executa a consulta remotamente e devolve as informações do local para a base de dados principal.

   ![SELECT * FROM dbo.Venues](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Selecione a próxima consulta e prima **F5**.

   Esta consulta une dados de bases de dados inquilinas e local *VenueTypes* tabela (local, como ele é uma tabela *adhocreporting* base de dados).

   Inspecione o plano e ver que a maior parte do custo é a consulta remota. Cada base de dados de inquilinos devolve as respetivas informações do local e efetua uma associação ao local com o local *VenueTypes* tabela para exibir o nome amigável.

   ![Junte-se no dados remotos e locais](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Agora, selecione o *no qual o dia foram mais pedidos de suporte vendidos?* consulta e prima **F5**.

   Esta consulta faz associar um pouco mais complexo e a agregação. Ocorre a maior parte do processamento remotamente.  Apenas linhas únicas, que contém pedido venda contagem diárias cada local por dia, são devolvidas ao banco de dados principal.

   ![consulta](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Executar consultas distribuídas em todas as bases de dados do inquilino
> * Implementar uma base de dados de relatórios e definir o esquema necessário para executar consultas distribuídas.


Experimente agora o [tutorial de análise de inquilinos](saas-tenancy-tenant-analytics.md) para explorar a extração de dados para uma base de dados de análise separada para processamento de análises mais complexo.

## <a name="additional-resources"></a>Recursos adicionais

* Adicionais [tutoriais que são criados após a aplicação Wingtip Tickets SaaS da base de dados por inquilino](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Consulta Elástica](sql-database-elastic-query-overview.md)
