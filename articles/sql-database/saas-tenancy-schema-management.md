---
title: Gerir esquema de base de dados do Azure SQL num aplicativo de inquilino único | Documentos da Microsoft
description: Gerir esquema para vários inquilinos numa aplicação de inquilino único, que utiliza a base de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: b2aa3eb6a117bbbdcf9c4aa44161dc25ddea2f1a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081224"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Gerir esquema de uma aplicação SaaS com o padrão de base de dados por inquilino com a base de dados do Azure SQL
 
À medida que um aplicativo de banco de dados evolui, as alterações inevitavelmente tem de ser feitas nos dados de referência ou de esquema de base de dados.  Tarefas de manutenção de base de dados também são necessários periodicamente. Gerir uma aplicação que utiliza a base de dados por padrão de inquilino exige que aplicar estas alterações ou as tarefas de manutenção numa frota de bases de dados do inquilino.

Este tutorial explora dois cenários – implementar atualizações de dados de referência para todos os inquilinos e recompilar um índice na tabela que contém os dados de referência. O [tarefas elásticas](sql-database-elastic-jobs-overview.md) funcionalidade é utilizada para executar essas ações em todas as bases de dados do inquilino e na base de dados modelo utilizado para criar o novo inquilino bases de dados.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Criar um agente de tarefa
> * Fazer com que as tarefas de T-SQL ser executado em todas as bases de dados do inquilino
> * Atualizar os dados de referência de todas as bases de dados de inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação Wingtip Tickets SaaS da base de dados por inquilino é implementada. Para implementar em menos de cinco minutos, veja [implementar e explorar a base de dados de Wingtip Tickets SaaS por aplicação de inquilino](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Está instalada a versão mais recente do SQL Server Management Studio (SSMS). [Transferir e instalar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Este tutorial utiliza funcionalidades do serviço de base de dados SQL que estão em pré-visualização limitada (tarefas de base de dados elástica). Se pretender realizar este tutorial, forneça o seu ID de subscrição para SaaSFeedback@microsoft.com com assunto = Elastic de tarefas de pré-visualização. Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Esta pré-visualização é limitada, portanto, entre em contato com SaaSFeedback@microsoft.com para questões relacionadas ou suporte.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gestão de esquemas SaaS

A base de dados por padrão de inquilino isola efetivamente os dados de inquilino, mas aumenta o número de bases de dados para gerir e manter. [Tarefas elásticas](sql-database-elastic-jobs-overview.md) facilitam a administração e gestão de bases de dados SQL. As tarefas permitem-lhe de forma segura e fiável, executar tarefas (scripts T-SQL) em relação a um grupo de bases de dados. Tarefas podem implementar o esquema e alterações de dados de referência comuns em todas as bases de dados de inquilinos numa aplicação. Tarefas elásticas também podem ser utilizadas para manter uma *modelo* base de dados utilizado para criar novos inquilinos ao assegurar que sempre tem os dados de referência e esquema mais recente.

![ecrã](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Pré-visualização limitada das Tarefas Elásticas

Há uma nova versão das tarefas elásticas, que agora é uma funcionalidade integrada da base de dados do Azure SQL. Esta nova versão das Tarefas Elásticas está atualmente em pré-visualização limitada. Esta pré-visualização limitada suporta atualmente a utilizar o PowerShell para criar um agente de tarefa e T-SQL para criar e gerir tarefas.

> [!NOTE]
> Este tutorial utiliza funcionalidades do serviço de base de dados SQL que estão em pré-visualização limitada (tarefas de base de dados elástica). Se pretender realizar este tutorial, forneça o seu ID de subscrição para SaaSFeedback@microsoft.com com assunto = Elastic de tarefas de pré-visualização. Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Esta pré-visualização é limitada, portanto, entre em contato com SaaSFeedback@microsoft.com para questões relacionadas ou suporte.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter a base de dados de Wingtip Tickets SaaS por scripts de aplicações do inquilino

Os scripts de código e gestão da origem de aplicação estão disponíveis no [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repositório do GitHub. Veja a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip Tickets SaaS de desbloqueio.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Criar um agente de tarefa da base de dados e o novo agente de tarefa

Este tutorial requer a que utilizar o PowerShell para criar um agente de tarefa e a respetiva base de dados do agente de tarefa do backup. A base de dados do agente de tarefa contém definições de tarefa, o estado da tarefa e o histórico. Assim que o agente de tarefa e a respetiva base de dados são criados, pode criar e monitorizar tarefas de imediato.

1. **No ISE do PowerShell**, abra... \\Módulos de aprendizagem\\gestão de esquemas\\*Demo-SchemaManagement.ps1*.
1. Prima **F5** para executar o script.

O *Demo-SchemaManagement.ps1* chamadas de script a *Deploy-SchemaManagement.ps1* script para criar uma base de dados SQL com o nome *osagent* no servidor de catálogo. Em seguida, cria o agente de tarefa, utilizando a base de dados como um parâmetro.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar uma tarefa para implementar novos dados de referência para todos os inquilinos

Na aplicação Wingtip Tickets, cada base de dados de inquilinos inclui um conjunto de tipos de suporte local. Cada local é de um tipo de local específico, que define o tipo de eventos que podem ser hospedados e determina a imagem de fundo utilizada na aplicação. Para o aplicativo oferecer suporte a novos tipos de eventos, estes dados de referência tem de ser adicionados de tipos de local de novos e atualizados.  Neste exercício, vai implementar uma atualização para todas as bases de dados inquilinas para adicionar dois tipos de local adicionais: *Corrida de motos* e *natação*.

Em primeiro lugar, reveja os tipos de local incluídos em cada base de dados do inquilino. Ligar a uma das bases de dados do inquilino no SQL Server Management Studio (SSMS) e inspecionar a tabela de VenueTypes.  Também pode consultar essa tabela no editor de consultas no portal do Azure, acedido a partir da página base de dados. 

1. Abra o SSMS e ligue-se para o servidor de inquilino: *tenants1-dpt -&lt;utilizador&gt;. database.windows.net*
1. Para confirmar que *corrida de motos* e *natação* **não estão** atualmente incluídos, navegue para o _contosoconcerthall_ base de dados no *tenants1-dpt -&lt;usuário&gt;*  servidor e consulta o *VenueTypes* tabela.

Agora vamos criar uma tarefa para atualizar o *VenueTypes* tabela em todas as bases de dados inquilinas para adicionar novos tipos de local.

Para criar uma nova tarefa, utilize um conjunto de sistema de tarefas que criar procedimentos armazenados no _jobagent_ da base de dados quando o agente de tarefa foi criado.

1. No SSMS, ligue ao servidor de catálogo: *catálogo-dpt -&lt;usuário&gt;. database.windows.net* server 
1. No SSMS, abra o ficheiro... \\Módulos de aprendizagem\\gestão de esquemas\\deployreferencedata. SQL
1. Modifique a instrução: DEFINIR @wtpUser = &lt;utilizador&gt; e substitua o valor de utilizador que usou quando implementou a aplicação Wingtip Tickets SaaS da base de dados por inquilino
1. Certifique-se de que está ligado para o _jobagent_ base de dados e prima **F5** para executar o script

Observe os elementos seguintes a *deployreferencedata. SQL* script:
* **SP\_adicione\_destino\_grupo** cria o nome do grupo DemoServerGroup.
* **SP\_adicione\_destino\_grupo\_membro** é usado para definir o conjunto de bases de dados de destino.  Primeiro o _tenants1-dpt -&lt;usuário&gt;_  servidor é adicionado.  Adicionar o servidor como um destino faz com que as bases de dados em que o servidor no momento da execução de tarefas a serem incluídos na tarefa. Em seguida, o _basetenantdb_ base de dados e o *adhocreporting* base de dados (que é utilizada num tutorial posterior) são adicionados como destinos.
* **SP\_adicione\_tarefa** cria uma tarefa designada _implementação de dados de referência_.
* **SP\_adicione\_jobstep** cria o passo da tarefa que contém o texto do comando T-SQL para atualizar a tabela de referência, VenueTypes.
* As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Utilize estas consultas para consultar o valor de estado na **ciclo de vida** coluna para determinar quando a tarefa foi concluída em todos os bancos de dados de destino.

Quando o script estiver concluído, pode verificar que os dados de referência foi atualizados.  No SSMS, navegue para o *contosoconcerthall* da base de dados no *tenants1-dpt -&lt;utilizador&gt;*  servidor e consulta o *VenueTypes* tabela.  Verifique se *corrida de motos* e *natação* **são** apresentam agora.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar uma tarefa para gerir o índice de tabela de referência

Este exercício utiliza uma tarefa para reconstruir o índice na chave primária da tabela de referência.  Esta é uma operação de manutenção de banco de dados típico que pode ser feita depois de carregar grandes quantidades de dados.

Crie uma tarefa com os mesmos procedimentos armazenados no “sistema” das tarefas.

1. Abra o SSMS e ligue-se para o _catálogo-dpt -&lt;usuário&gt;. database.windows.net_ server
1. Abra o ficheiro _... \\Módulos de aprendizagem\\gestão de esquemas\\onlinereindex. SQL_
1. Com o botão direito, selecione a ligação e ligue-se para o _catálogo-dpt -&lt;usuário&gt;. database.windows.net_ servidor, se ainda não estiver ligado
1. Certifique-se de que está ligado para o _jobagent_ base de dados e prima **F5** para executar o script

Observe os elementos seguintes a _onlinereindex. SQL_ script:
* **SP\_adicione\_tarefa** cria uma nova tarefa denominada "Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885"
* **SP\_adicione\_jobstep** cria o passo da tarefa que contém o texto do comando T-SQL para atualizar o índice
* As vistas restantes no script de monitorizar a execução de tarefa. Utilize estas consultas para consultar o valor de estado na **ciclo de vida** coluna para determinar quando a tarefa foi concluída com êxito em todos os membros do grupo de destino.



## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Criar um agente de tarefa para serem executadas tarefas T-SQL várias bases de dados
> * Atualizar os dados de referência de todas as bases de dados de inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos

Em seguida, tente o [relatórios Ad hoc tutorial](saas-tenancy-cross-tenant-reporting.md) para explorar a execução de consultas distribuídas pelo inquilino bases de dados.


## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais criados após a implementação da aplicação Wingtip Tickets SaaS da base de dados por inquilino](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)](sql-database-elastic-jobs-overview.md)
* [Create and manage scaled-out cloud databases (Criar e gerir bases de dados de escalamento horizontal na cloud)](sql-database-elastic-jobs-create-and-manage.md)