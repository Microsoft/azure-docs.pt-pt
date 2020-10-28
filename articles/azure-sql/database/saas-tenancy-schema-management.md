---
title: Gerir o esquema numa aplicação de um único inquilino
description: Gerencie o Schema para vários inquilinos numa app de inquilino único que utiliza a Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/19/2018
ms.openlocfilehash: e4328be0aade0658dedb034dbbb6980b810f771a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793199"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Gerir o esquema numa aplicação SaaS utilizando o padrão de base de dados por inquilino com base de dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
À medida que uma aplicação de base de dados evolui, é necessário fazer alterações inevitavelmente ao esquema da base de dados ou aos dados de referência.  As tarefas de manutenção da base de dados também são necessárias periodicamente. Gerir uma aplicação que utilize a base de dados por padrão de inquilino requer que você aplique estas alterações ou tarefas de manutenção em uma frota de bases de dados de inquilinos.

Este tutorial explora dois cenários - implementando atualizações de dados de referência para todos os inquilinos, e reconstruindo um índice na tabela contendo os dados de referência. A funcionalidade [de empregos elásticos](./elastic-jobs-overview.md) é usada para executar estas ações em todas as bases de dados de inquilinos, e na base de dados de modelos usada para criar novas bases de dados de inquilinos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Criar um agente de trabalho
> * Porque os empregos T-SQL devem ser executados em todas as bases de dados de inquilinos
> * Atualizar dados de referência em todas as bases de dados de inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação Wingtip Tickets SaaS Database Per Tenant é implementada. Para implementar em menos de cinco minutos, consulte [Implementar e explorar a base de dados de Bilhetes SaaS wingtip por aplicação de inquilino](./saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps)
* Está instalada a versão mais recente do SQL Server Management Studio (SSMS). [Descarregar e Instalar SSMS](/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gestão de esquemas SaaS

A base de dados por padrão de inquilino isola os dados dos inquilinos de forma eficaz, mas aumenta o número de bases de dados para gerir e manter. [A Elastic Jobs](./elastic-jobs-overview.md) facilita a administração e gestão de várias bases de dados. Os trabalhos permitem-lhe executar tarefas (scripts T-SQL) de forma segura e fiável (scripts T-SQL) contra um grupo de bases de dados. Os postos de trabalho podem implementar esquemas e alterações comuns de dados de referência em todas as bases de dados dos inquilinos numa aplicação. O Elastic Jobs também pode ser usado para manter uma base de dados *de modelos* usada para criar novos inquilinos, garantindo que tem sempre os mais recentes esquemas e dados de referência.

![ecrã](./media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-public-preview"></a>Pré-visualização pública de Empregos Elásticos

Há uma nova versão da Elastic Jobs que é agora uma característica integrada da Azure SQL Database. Esta nova versão da Elastic Jobs está atualmente em pré-visualização pública. Esta pré-visualização pública suporta atualmente o uso do PowerShell para criar um agente de emprego, e t-SQL para criar e gerir postos de trabalho.
Consulte o artigo sobre [o Elastic Database Jobs](./elastic-jobs-overview.md) para obter mais informações.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenha a base de dados De Ingressos SaaS de Wingtip por scripts de aplicação de inquilinos

O código fonte de aplicação e os scripts de gestão estão disponíveis no [wingtipTicketsSaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para as etapas para descarregar e desbloquear os scripts SaaS dos Bilhetes Wingtip.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Criar uma base de dados de agentes de emprego e um novo agente de emprego

Este tutorial requer que utilize o PowerShell para criar um agente de trabalho e a sua base de dados de agente de emprego. A base de dados de agentes de emprego tem definições de emprego, estatuto profissional e história. Uma vez criado o agente de trabalho e a sua base de dados, pode criar e monitorizar imediatamente os trabalhos.

1. <bpt id="p1">**</bpt>In PowerShell ISE<ept id="p1">**</ept>, open …<ph id="ph1">\\</ph>Learning Modules<ph id="ph2">\\</ph>Schema Management<ph id="ph3">\\</ph><bpt id="p2">*</bpt>Demo-SchemaManagement.ps1<ept id="p2">*</ept>.
1. Prima **F5** para executar o script.

O *Demo-SchemaManagement.ps1* script chama *o* Deploy-SchemaManagement.ps1script para criar uma base de dados chamada *osagent* no servidor do catálogo. Em seguida, cria o agente de trabalho, usando a base de dados como parâmetro.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar uma tarefa para implementar novos dados de referência para todos os inquilinos

Na aplicação Wingtip Tickets, cada base de dados de inquilinos inclui um conjunto de tipos de locais suportados. Cada local é de um tipo específico de local, que define o tipo de eventos que podem ser hospedados, e determina a imagem de fundo usada na app. Para a aplicação de suporte a novos tipos de eventos, estes dados de referência devem ser atualizados e novos tipos de locais adicionados.  Neste exercício, vai implementar uma atualização para todas as bases de dados de inquilinos para adicionar dois tipos de local adicionais: *Corrida de Motos* e *Clube de Natação* .

Em primeiro lugar, reveja os tipos de locais incluídos em cada base de dados de inquilinos. Ligue-se a uma das bases de dados dos inquilinos no SQL Server Management Studio (SSMS) e inspecione a tabela VenueTypes.  Pode ainda consultar esta tabela no editor de Consultas no portal Azure, acedido a partir da página de base de dados. 

1. Abra sSMS e ligue-se ao servidor do inquilino: *inquilinos1-dpt-user &lt; &gt; .database.windows.net*
1. Para confirmar que *o Clube de Motociclismo* e *Natação* **não estão** atualmente incluídos, consulte a base de dados _contosoconcerthall_ no servidor do *&lt; utilizador &gt; de 1-dpt dos inquilinos* e consulte a tabela *VenueTypes.*

Agora vamos criar um trabalho para atualizar a tabela *VenueTypes* em todas as bases de dados de inquilinos para adicionar os novos tipos de locais.

Para criar um novo emprego, utiliza-se um conjunto de procedimentos de criação de sistemas de emprego criados na base de dados _jobagent_ quando o agente de trabalho foi criado.

1. No SSMS, ligue-se ao servidor de catálogo: *catálogo-dpt-user &lt; &gt; .database.windows.net* servidor 
1. Na SSMS, abra o ficheiro... \\ Módulos de Aprendizagem \\ Schema Management \\ ImplementarReferenceData.sql
1. Modifique a declaração: SET @wtpUser = &lt; utilizador e &gt; substitua o valor do Utilizador utilizado quando implementou a aplicação Wingtip Tickets SaaS Database Per Tenant
1. Certifique-se de que está ligado à base _de dados jobagent_ e prima **F5** para executar o script

Observe os seguintes elementos no script *DeployReferenceData.sql:*
* **sp \_ add \_ \_ grupo alvo** cria o nome de grupo alvo DemoServerGroup.
* **sp \_ adicionar membro \_ do \_ grupo \_ alvo** é usado para definir o conjunto de bases de dados-alvo.  Primeiro, o servidor _de &lt; &gt; utilizadores de 1-dpt-dpt dos inquilinos_ é adicionado.  Adicionar o servidor como alvo faz com que as bases de dados desse servidor no momento da execução do trabalho sejam incluídas no trabalho. Em seguida, a base de dados _basetenantdb_ e a base de dados *de adhocreporting* (usada num tutorial posterior) são adicionadas como alvos.
* **sp \_ add \_ job** cria um trabalho chamado _Reference Data Deployment_ .
* **sp \_ add \_ jobstep** cria o passo de trabalho contendo texto de comando T-SQL para atualizar a tabela de referência, VenueTypes.
* As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Utilize estas consultas para rever o valor de estado na coluna do **ciclo de vida** para determinar quando o trabalho terminou em todas as bases de dados-alvo.

Uma vez concluído o script, pode verificar se os dados de referência foram atualizados.  Em SSMS, consulte a base de dados *contosoconcerthall* no servidor do *&lt; &gt; utilizador dos inquilinos1-dpt* e consulte a tabela *VenueTypes.*  Verifique se *o Clube de Motociclismo* e *Natação* **estão presentes.**


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar uma tarefa para gerir o índice de tabela de referência

Este exercício utiliza um trabalho para reconstruir o índice na chave primária da tabela de referência.  Esta é uma operação típica de manutenção de bases de dados que pode ser feita depois de carregar grandes quantidades de dados.

Crie uma tarefa com os mesmos procedimentos armazenados no “sistema” das tarefas.

1. Abra sSMS e _ligue-se ao servidor de catálogo-dpt-user &lt; &gt; .database.windows.net_
1. Abra o _arquivo... \\ Módulos de Aprendizagem \\ Schema Management \\ OnlineReindex.sql_
1. Clique no botão direito, selecione Connection e _ligue-se ao servidor do utilizador de catálogo-dpt.database.windows.net, &lt; &gt;_ se ainda não estiver ligado
1. Certifique-se de que está ligado à base _de dados jobagent_ e prima **F5** para executar o script

Observe os seguintes elementos no script _OnlineReindex.sql:_
* **sp \_ add \_ job** cria um novo emprego chamado "Online Reindex PK \_ \_ VenueTyp \_ \_ 265E44FD7FD4C885"
* **sp \_ adicionar \_ jobstep** cria o passo de trabalho contendo texto de comando T-SQL para atualizar o índice
* As restantes opiniões no script monitorizam a execução do trabalho. Utilize estas consultas para rever o valor de estado na coluna do **ciclo de vida** para determinar quando o trabalho terminou com sucesso em todos os membros do grupo alvo.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Criar um agente de trabalho para correr através de empregos T-SQL várias bases de dados
> * Atualizar dados de referência em todas as bases de dados de inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos

Em seguida, experimente o [tutorial de reportagem ad hoc](./saas-tenancy-cross-tenant-reporting.md) para explorar consultas distribuídas em bases de dados de inquilinos.


## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na base de dados saass database por inquilino](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)](./elastic-jobs-overview.md)