---
title: Gerir o esquema numa aplicação de um único inquilino
description: Gerencie a Schema para vários inquilinos numa aplicação de um único inquilino que utiliza a Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/19/2018
ms.openlocfilehash: b6802d97b964b8863f6c2fce0cebfe16782b46fe
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397166"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Gerir o esquema numa aplicação SaaS utilizando o padrão base de dados por inquilino com base de dados Azure SQL
 
À medida que uma aplicação de base de dados evolui, é necessário inevitavelmente fazer alterações no esquema da base de dados ou nos dados de referência.  As tarefas de manutenção da base de dados também são necessárias periodicamente. Gerir uma aplicação que utiliza a base de dados por padrão de inquilino requer que aplique estas alterações ou tarefas de manutenção em uma frota de bases de dados de inquilinos.

Este tutorial explora dois cenários - implementando atualizações de dados de referência para todos os inquilinos, e reconstruindo um índice na tabela contendo os dados de referência. A funcionalidade [de trabalhos elásticos](elastic-jobs-overview.md) é usada para executar estas ações em todas as bases de dados dos inquilinos, e na base de dados de modelos utilizada para criar novas bases de dados de inquilinos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Criar um agente de trabalho
> * Fazer com que os empregos da T-SQL sejam executados em todas as bases de dados dos inquilinos
> * Atualizar dados de referência em todas as bases de dados dos inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação Wingtip Tickets SaaS Database Per Tenant está implementada. Para implantar em menos de cinco minutos, consulte Implementar e explorar a base de [dados Wingtip Tickets SaaS por aplicação de inquilino](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Está instalada a versão mais recente do SQL Server Management Studio (SSMS). [Transferir e instalar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Este tutorial utiliza funcionalidades do serviço De base de dados SQL que se encontram numa pré-visualização limitada (trabalhos de base de dados elásticos). Se desejar fazer este tutorial, forneça o seu ID de subscrição para SaaSFeedback@microsoft.com com subject=Elastic Jobs Preview. Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Esta pré-visualização é limitada, por isso contacte SaaSFeedback@microsoft.com para questões ou suporte relacionados.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gestão de esquemas SaaS

A base de dados por padrão de inquilino isola os dados dos inquilinos de forma eficaz, mas aumenta o número de bases de dados para gerir e manter. [A Elastic Jobs](elastic-jobs-overview.md) facilita a administração e gestão das bases de dados SQL. Os trabalhos permitem-lhe executar tarefas (scripts T-SQL) de forma segura e fiável contra um grupo de bases de dados. Os empregos podem implementar alterações de dados de referência comuns em todas as bases de dados dos inquilinos numa aplicação. O Trabalho Elástico também pode ser usado para manter uma base de dados de *modelos* usada para criar novos inquilinos, garantindo que tem sempre os dados mais recentes de esquemas e referências.

![ecrã](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Pré-visualização limitada das Tarefas Elásticas

Há uma nova versão de Elastic Jobs que é agora uma característica integrada da Base de Dados Azure SQL. Esta nova versão das Tarefas Elásticas está atualmente em pré-visualização limitada. Esta pré-visualização limitada suporta atualmente a utilização da PowerShell para criar um agente de trabalho, e a T-SQL para criar e gerir empregos.

> [!NOTE]
> Este tutorial utiliza funcionalidades do serviço De base de dados SQL que se encontram numa pré-visualização limitada (trabalhos de base de dados elásticos). Se desejar fazer este tutorial, forneça o seu ID de subscrição para SaaSFeedback@microsoft.com com subject=Elastic Jobs Preview. Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Esta pré-visualização é limitada, por isso contacte SaaSFeedback@microsoft.com para questões ou suporte relacionados.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenha a base de dados De Bilhetes Wingtip SaaS por scripts de candidatura de inquilino

O código fonte da aplicação e scripts de gestão estão disponíveis no repo [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para os passos para descarregar e desbloquear os scripts Wingtip Tickets SaaS.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Criar uma base de dados de agente de emprego e um novo agente de trabalho

Este tutorial requer que use o PowerShell para criar um agente de trabalho e a sua base de dados de agente de trabalho de apoio. A base de dados do agente de trabalho tem definições de emprego, estatuto profissional e história. Assim que o agente de trabalho e a sua base de dados forem criados, pode criar e monitorizar os empregos imediatamente.

1. **No PowerShell ISE,** aberto...\\Módulos de Aprendizagem\\Schema Management\\*Demo-SchemaManagement.ps1*.
1. Prima **F5** para executar o script.

O script *Demo-SchemaManagement.ps1* chama o script *Deploy-SchemaManagement.ps1* para criar uma base de dados SQL chamada *osagent* no servidor de catálogo. Cria então o agente de trabalho, utilizando a base de dados como parâmetro.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar uma tarefa para implementar novos dados de referência para todos os inquilinos

Na aplicação Wingtip Tickets, cada base de dados de inquilinos inclui um conjunto de tipos de locais suportados. Cada local é de um tipo específico de local, que define o tipo de eventos que podem ser hospedados, e determina a imagem de fundo usada na app. Para a aplicação de apoio a novos tipos de eventos, estes dados de referência devem ser atualizados e novos tipos de locais adicionados.  Neste exercício, vai implementar uma atualização para todas as bases de dados de inquilinos para adicionar dois tipos de local adicionais: *Corrida de Motos* e *Clube de Natação*.

Primeiro, reveja os tipos de locais incluídos em cada base de dados de inquilinos. Ligue-se a uma das bases de dados dos inquilinos no SQL Server Management Studio (SSMS) e inspecione a tabela VenueTypes.  Pode ainda consultar esta tabela no editor da Consulta no portal Azure, acedido a partir da página de base de dados. 

1. Abra o SSMS e ligue-se ao servidor de *inquilinos: os inquilinos1-dpt-&lt;utilizador&gt;.database.windows.net*
1. Para confirmar que o Clube de *Motociclismo* e *Natação* **não está** atualmente incluído, navegue na base de dados _contosoconcerthall_ no *inquilins1-dpt-&lt;utilizador&gt;* servidor e consultar a tabela *VenueTypes.*

Agora vamos criar um trabalho para atualizar a tabela *VenueTypes* em todas as bases de dados de inquilinos para adicionar os novos tipos de locais.

Para criar um novo emprego, você usa um conjunto de procedimentos armazenados no sistema de emprego criados na base de dados de _agente de emprego_ quando o agente de trabalho foi criado.

1. No SSMS, ligue-se ao servidor de catálogo: *&lt;utilizador de catálogo&gt;servidor .database.windows.net* 
1. No SSMS, abra o ficheiro ...\\Módulos de Aprendizagem\\Schema Management\\DeployReferenceData.sql
1. Modificar a declaração: SET @wtpUser = &lt;utilizador&gt; e substituir o valor do Utilizador utilizado quando implementou a base de dados saaS de bilhetes de asa por inquilino
1. Certifique-se de que está ligado à base de dados do _agente de trabalho_ e prima **F5** para executar o script

Observe os seguintes elementos no script *DeployReferenceData.sql:*
* **sp\_adicionar\_alvo\_grupo** cria o nome de grupo alvo DemoServerGroup.
* **sp\_adicionar\_alvo\_grupo\_membro** é usado para definir o conjunto de bases de dados de alvos.  Primeiro é adicionado o _&lt;de inquilinos do servidor&gt;._  A adição do servidor como alvo faz com que as bases de dados desse servidor no momento da execução do trabalho sejam incluídas no trabalho. Em seguida, a base de dados _basetenantdb_ e a base de dados de *adhocreporting* (utilizada num tutorial posterior) são adicionadas como alvos.
* **sp\_adicionar\_trabalho** cria um trabalho chamado _Implantação_de Dados de Referência .
* **sp\_adicionar\_passo** de trabalho cria o passo de trabalho contendo texto de comando T-SQL para atualizar a tabela de referência, VenueTypes.
* As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Utilize estas consultas para rever o valor de estado na coluna do ciclo de **vida** para determinar quando o trabalho terminou em todas as bases de dados-alvo.

Uma vez concluída a script, pode verificar se os dados de referência foram atualizados.  No SSMS, navegue na base de dados *contosoconcerthall* no *utilizador&lt;inquilinos 1-dpt-  servidor*&gt;e consulte a tabela *VenueTypes.*  Verifique se o Clube de *Motociclismo* e *Natação* **está** presente.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar uma tarefa para gerir o índice de tabela de referência

Este exercício usa um trabalho para reconstruir o índice na chave primária da tabela de referência.  Esta é uma operação típica de manutenção de bases de dados que pode ser feita após o carregamento de grandes quantidades de dados.

Crie uma tarefa com os mesmos procedimentos armazenados no “sistema” das tarefas.

1. Abra o SSMS e ligue&lt;-se ao servidor&gt; .database.windows.net do _utilizador do catálogo.database.windows.net_
1. Abra o ficheiro _...\\Módulos de Aprendizagem\\Schema Management\\OnlineReindex.sql_
1. Clique à direita, selecione Ligação e ligue-se ao _utilizador&lt;&gt;.database.windows.net_ do utilizador do catálogo, se ainda não estiver ligado
1. Certifique-se de que está ligado à base de dados do _agente de trabalho_ e prima **F5** para executar o script

Observe os seguintes elementos no script _OnlineReindex.sql:_
* **sp\_adicionar\_emprego** cria um novo emprego chamado "Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885"
* **sp\_adicionar\_passo** de trabalho cria o passo de trabalho contendo texto de comando T-SQL para atualizar o índice
* As restantes opiniões no script monitor execução de trabalho. Utilize estas consultas para rever o valor de estado na coluna do ciclo de **vida** para determinar quando o trabalho terminou com sucesso em todos os membros do grupo alvo.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Criar um agente de trabalho para passar por empregos T-SQL várias bases de dados
> * Atualizar dados de referência em todas as bases de dados dos inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos

Em seguida, experimente o tutorial de [reportagem ad hoc](saas-tenancy-cross-tenant-reporting.md) para explorar consultas distribuídas em todas as bases de dados dos inquilinos.


## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na base de dados SaaS dos bilhetes de asa por inquilino](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)](elastic-jobs-overview.md)