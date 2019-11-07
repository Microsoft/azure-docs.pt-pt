---
title: Executar consultas de relatório em vários bancos de dados SQL do Azure
description: Relatório entre locatários usando consultas distribuídas.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewers: billgib,ayolubek
ms.date: 01/25/2019
ms.openlocfilehash: f9af2af7893bd908988ee45476ce14a56f9768a9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691900"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Relatório entre locatários usando consultas distribuídas

Neste tutorial, você executa consultas distribuídas em todo o conjunto de bancos de dados de locatário para relatórios. Essas consultas podem extrair informações enterradas nos dados operacionais do dia a dia dos locatários SaaS Wingtip tickets. Para fazer isso, você implanta um banco de dados de relatório adicional no servidor de catálogo e usa a consulta elástica para habilitar consultas distribuídas.


Neste tutorial, ficará a saber:

> [!div class="checklist"]
> 
> * Como implantar um banco de dados de relatórios
> * Como executar consultas distribuídas em todos os bancos de dados de locatário
> * Como as exibições globais em cada banco de dados podem habilitar a consulta eficiente entre locatários


Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:


* O aplicativo de banco de dados por locatário SaaS Wingtip tickets foi implantado. Para implantar em menos de cinco minutos, consulte [implantar e explorar o aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* O SQL Server Management Studio (SSMS) está instalado. Para baixar e instalar o SSMS, consulte [baixar SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Padrão de relatório entre locatários

![padrão de consulta distribuída entre locatários](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Uma oportunidade com aplicativos SaaS é usar a grande quantidade de dados de locatário armazenados na nuvem para obter informações sobre a operação e o uso do seu aplicativo. Essas informações podem guiar o desenvolvimento de recursos, melhorias de usabilidade e outros investimentos em seus aplicativos e serviços.

Aceder a estes dados numa única base de dados multi-inquilinos é fácil, mas não será tão fácil se houver uma distribuição à escala através de potencialmente milhares de bases de dados. Uma abordagem é usar a [consulta elástica](sql-database-elastic-query-overview.md), que permite a consulta em um conjunto distribuído de bancos de dados com esquema comum. Esses bancos de dados podem ser distribuídos em diferentes grupos de recursos e assinaturas, mas precisam compartilhar um logon comum. A consulta elástica usa um único banco de dados *principal* no qual são definidas tabelas externas que espelham tabelas ou exibições nos bancos de dados distribuídos (locatário). As consultas submetidas para esta base de dados “head” são compiladas para produzir um plano de consultas distribuídas, com partes das consultas enviadas para as bases de dados inquilinas, conforme necessário. A consulta elástica usa o mapa de fragmentos no banco de dados de catálogo para determinar o local de todos os bancos de dados de locatário. A instalação e a consulta do banco de dados principal são simples usando o [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)padrão e dão suporte à consulta de ferramentas como Power bi e Excel.

Ao distribuir consultas entre os bancos de dados de locatários, a consulta elástica fornece informações imediatas sobre o Live Production Data. Como a consulta elástica efetua pull de dados de potencialmente muitos bancos, a latência de consulta pode ser maior do que as consultas equivalentes enviadas a um único banco de dados de vários locatários. Crie consultas para minimizar os dados que são retornados para o banco de dado principal. A consulta elástica geralmente é mais adequada para a consulta de pequenas quantidades de dados em tempo real, em oposição à criação de consultas ou relatórios de análise complexos ou usados com frequência. Se as consultas não tiverem um bom desempenho, examine o [plano de execução](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) para ver qual parte da consulta é empurrada para o banco de dados remoto e a quantidade de dado que está sendo retornada. As consultas que exigem agregação complexa ou processamento analítico podem ser identificadores melhores por meio da extração de dados de locatário em um banco de dado ou data warehouse otimizadas para consultas de análise. Esse padrão é explicado no [tutorial análise de locatário](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de aplicativo de banco de dados por locatário SaaS Wingtip tickets

Os scripts de banco de dados multilocatário do Wingtip tickets SaaS e o código-fonte do aplicativo estão disponíveis no repositório GitHub [repositório wingtipticketssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip tickets.

## <a name="create-ticket-sales-data"></a>Criar dados de vendas de tíquetes

Para executar consultas em um conjunto de dados mais interessante, crie dados de vendas de tíquetes executando o gerador de tíquetes.

1. No *ISE do PowerShell*, abra os módulos de aprendizagem...\\\\análise operacional\\relatórios ad hoc\\script *demo-adhocreporting. ps1* e defina o seguinte valor:
   * **$DemoScenario** = 1, **comprar tíquetes para eventos em todos os locais**.
2. Pressione **F5** para executar o script e gerar vendas de tíquetes. Enquanto o script estiver em execução, continue as etapas neste tutorial. Os dados do tíquete são consultados na seção *executar consultas ad hoc distribuídas* , portanto, aguarde até que o gerador de tíquetes seja concluído.

## <a name="explore-the-global-views"></a>Explorar as exibições globais

No aplicativo de banco de dados por locatário SaaS Wingtip tickets, cada locatário recebe um banco de dados. Assim, os dados contidos nas tabelas de banco de dado são incluídos no escopo da perspectiva de um único locatário. No entanto, ao consultar em todos os bancos de dados, é importante que a consulta elástica possa tratar os dados como se ele fosse parte de um único banco de dado lógico fragmentado por locatário. 

Para simular esse padrão, um conjunto de exibições ' globais ' é adicionado ao banco de dados de locatário que projeta uma ID de locatário em cada uma das tabelas que são consultadas globalmente. Por exemplo, a exibição *VenueEvents* adiciona uma *foroid* computada às colunas projetadas na tabela de *eventos* . Da mesma forma, as exibições *VenueTicketPurchases* e *VenueTickets* adicionam uma coluna de *foroid* computada de suas respectivas tabelas. Essas exibições são usadas pela consulta elástica para paralelizar consultas e enviá-las por push para o banco de dados de locatário remoto apropriado quando uma coluna do *LocalId* estiver presente. Isso reduz drasticamente a quantidade de dados retornados e resulta em um aumento significativo no desempenho de muitas consultas. Essas exibições globais foram criadas previamente em todos os bancos de dados de locatário.

1. Abra o SSMS e [Conecte-se ao servidor de&gt; de usuário do tenants1-&lt;](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Expanda **bancos de dados**, clique com o botão direito do mouse em _Contosoconcerthall_e selecione **nova consulta**.
1. Execute as consultas a seguir para explorar a diferença entre as tabelas de locatário único e as exibições globais:

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

Nesses modos de exibição, o *foroid* é calculado como um hash do nome do local, mas qualquer abordagem pode ser usada para introduzir um valor exclusivo. Essa abordagem é semelhante à forma como a chave de locatário é computada para uso no catálogo.

Para examinar a definição da exibição *locais* :

1. No Pesquisador de **objetos**, expanda **contosoconcerthall** > **exibições**:

   ![Modos de exibição](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Clique com o botão direito do mouse em **dbo. Locais**.
3. Selecione **o modo de exibição de script como** > **criar para** > **nova janela do editor de consultas**

Gerar script de qualquer uma das outras exibições de *local* para ver como elas adicionam o *foroid*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Implantar o banco de dados usado para consultas distribuídas

Este exercício implanta o banco de dados _adhocreporting_ . Esse é o banco de dados principal que contém o esquema usado para consultar em todos os bancos de dados de locatário. O banco de dados é implantado no servidor de catálogo existente, que é o servidor usado para todos os bancos de dados relacionados ao gerenciamento no aplicativo de exemplo.

1. no *ISE do PowerShell*, abra...\\módulos de aprendizado\\análise operacional\\relatórios ad hoc\\*demo-adhocreporting. ps1*. 

1. Defina **$DemoScenario = 2**, _implantar banco de dados de relatórios ad hoc_.

1. Pressione **F5** para executar o script e criar o banco de dados *adhocreporting* .

Na próxima seção, você adiciona o esquema ao banco de dados para que ele possa ser usado para executar consultas distribuídas.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configurar o banco de dados ' Head ' para executar consultas distribuídas

Este exercício adiciona o esquema (a fonte de dados externa e as definições de tabela externa) ao _adhocreporting_ para habilitar a consulta em todos os bancos de dados de locatário.

1. Abra SQL Server Management Studio e conecte-se ao banco de dados de relatórios ad hoc criado na etapa anterior. O nome do banco de dados é *adhocreporting*.
2. Abra. ..\Learning Modules\Operational Analytics\Adhoc Reporting \ _Initialize-AdhocReportingDB. SQL_ no SSMS.
3. Examine o script SQL e observe:

   A consulta elástica usa uma credencial no escopo do banco de dados para acessar cada um dos bancos de dados de locatário. Essa credencial precisa estar disponível em todos os bancos de dados e normalmente deve receber os direitos mínimos necessários para habilitar essas consultas.

    ![Criar credencial](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Com o banco de dados de catálogo como a fonte externa, as consultas são distribuídas para todos os bancos de dado registrados no catálogo no momento em que a consulta é executada. Como os nomes de servidor são diferentes para cada implantação, esse script Obtém o local do banco de dados de catálogo do servidor atual (@@servername) em que o script é executado.

    ![criar fonte de dados externa](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   As tabelas externas que fazem referência às exibições globais descritas na seção anterior e definidas com **Distribution = fragmented (foroid)** . Como cada *foroid* é mapeado para um banco de dados individual, isso melhora o desempenho para muitos cenários, conforme mostrado na próxima seção.

    ![criar tabelas externas](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   A tabela local _VenueTypes_ que é criada e populada. Essa tabela de dados de referência é comum em todos os bancos de dado de locatário, para que possa ser representada aqui como uma tabela local e preenchida com os dados comuns. Para algumas consultas, ter essa tabela definida no banco de dados principal pode reduzir a quantidade de dado que precisa ser movida para o banco de dados principal.

    ![criar tabela](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Se você incluir tabelas de referência dessa maneira, certifique-se de atualizar o esquema de tabela e os dados sempre que atualizar os bancos de dado de locatário.

4. Pressione **F5** para executar o script e inicializar o banco de dados *adhocreporting* . 

Agora você pode executar consultas distribuídas e coletar informações em todos os locatários!

## <a name="run-distributed-queries"></a>Executar consultas distribuídas

Agora que o banco de dados *adhocreporting* está configurado, vá em frente e execute algumas consultas distribuídas. Inclua o plano de execução para uma melhor compreensão de onde o processamento de consulta está ocorrendo. 

Ao inspecionar o plano de execução, passe o mouse sobre os ícones de plano para obter detalhes. 

É importante observar que a configuração de **Distribution = fragmented (foroid)** quando a fonte de dados externa é definida melhora o desempenho para muitos cenários. Como cada *LocalId* é mapeado para um banco de dados individual, a filtragem é facilmente feita remotamente, retornando apenas os dados necessários.

1. Abra...\\módulos de aprendizado\\análise operacional\\relatórios ad hoc\\*demo-adhocreportingqueries. SQL* no SSMS.
2. Verifique se você está conectado ao banco de dados **adhocreporting** .
3. Selecione o menu **consulta** e clique em **incluir plano de execução real**
4. Realce os *locais que estão registrados atualmente?* consulte e pressione **F5**.

   A consulta retorna toda a lista de locais, ilustrando quão rápido e fácil é consultar em todos os locatários e retornar dados de cada locatário.

   Inspecione o plano e veja que todo o custo está na consulta remota. Cada banco de dados de locatário executa a consulta remotamente e retorna suas informações de local para o banco de dados principal.

   ![Selecione * de dbo. Locais](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Selecione a próxima consulta e pressione **F5**.

   Essa consulta une dados dos bancos de dado de locatário e da tabela *VenueTypes* local (local, pois é uma tabela no banco de dados *adhocreporting* ).

   Inspecione o plano e veja que a maior parte do custo é a consulta remota. Cada banco de dados de locatário retorna suas informações de local e executa uma junção local com a tabela *VenueTypes* local para exibir o nome amigável.

   ![Ingressar em dados remotos e locais](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Agora, selecione o *em que dia foram os mais ingressos vendidos?* e pressione **F5**.

   Essa consulta faz uma junção e agregação mais complexas. A maior parte do processamento ocorre remotamente.  Somente linhas únicas, que contêm a contagem diária de vendas de tíquetes por dia de cada local, são retornadas ao banco de dados principal.

   ![consulta](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Executar consultas distribuídas em todas as bases de dados do inquilino
> * Implante um banco de dados de relatórios e defina o esquema necessário para executar consultas distribuídas.


Agora experimente o [tutorial de análise de locatário](saas-tenancy-tenant-analytics.md) para explorar a extração de dados para um banco de dado de análise separado para um processamento de análise mais complexo.

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam no aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Consulta Elástica](sql-database-elastic-query-overview.md)
