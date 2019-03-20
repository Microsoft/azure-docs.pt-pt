---
title: Gerir o esquema da Base de Dados SQL numa aplicação multi-inquilino | Microsoft Docs
description: Gerir o Esquema para vários inquilinos numa aplicação multi-inquilino que utiliza a Base de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: c7c10608d90f7659b108d2d8c80038f59396de2d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878079"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Gerir esquema numa aplicação SaaS que utiliza bases de dados SQL em partição horizontal multi-inquilinos

Este tutorial examina os desafios na manutenção de uma frota de bases de dados num Software como um aplicativo de serviço (SaaS). Soluções são demonstradas para fanning as alterações de esquema toda a frota de bases de dados.

Como qualquer aplicativo, a aplicação Wingtip Tickets SaaS evoluirá ao longo do tempo e exigirão alterações no banco de dados. As alterações podem afetar os dados de esquema ou de referência ou aplicam-se tarefas de manutenção de base de dados. Com uma aplicação SaaS com uma base por padrão de inquilino, as alterações têm de ser coordenadas entre uma frota potencialmente massiva de bases de dados do inquilino. Além disso, tem de incorporar essas alterações no banco de dados, certifique-se de que estão incluídas nas novas bases de dados à medida que são criados do processo de aprovisionamento.

#### <a name="two-scenarios"></a>Dois cenários

Este tutorial explora dois cenários a seguir:
- Implemente atualizações de dados de referência para todos os inquilinos.
- Reconstrua um índice na tabela que contém os dados de referência.

O [tarefas elásticas](sql-database-elastic-jobs-overview.md) funcionalidade de base de dados SQL do Azure é utilizada para executar estas operações em bases de dados do inquilino. As tarefas também operam na base de dados do inquilino 'template'. A aplicação Wingtip Tickets de exemplo, esta base de dados do modelo é copiado para Aprovisionar uma nova base de dados do inquilino.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um agente de tarefa.
> * Execute uma consulta T-SQL em várias bases de dados do inquilino.
> * Atualize os dados de referência em todos os bancos de dados do inquilino.
> * Crie um índice numa tabela em todas as bases de dados de inquilinos.

## <a name="prerequisites"></a>Pré-requisitos

- A aplicação de base de dados do multi-inquilino Wingtip Tickets já tem de ser implementada:
    - Para obter instruções, veja o tutorial primeiro, o que introduz a aplicação de base de dados do multi-inquilino de Wingtip Tickets SaaS:<br />[Implementar e explorar uma aplicação em partição horizontal do multi-inquilino que utiliza a base de dados do Azure SQL](saas-multitenantdb-get-started-deploy.md).
        - O processo de implementação é executada durante menos de cinco minutos.
    - Tem de ter o *multi-inquilino em partição horizontal* versão do Wingtip instalado. As versões para *autónomo* e *base de dados por inquilino* não suportam este tutorial.

- Tem de ser instalada a versão mais recente do SQL Server Management Studio (SSMS). [Transferir e instalar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- O Azure PowerShell tem de ser instalado. Para obter detalhes, consulte [introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Este tutorial utiliza funcionalidades do serviço de base de dados do Azure SQL que estão em pré-visualização limitada ([tarefas de bases de dados elásticas](sql-database-elastic-database-client-library.md)). Se pretender realizar este tutorial, forneça o seu ID de subscrição para *SaaSFeedback\@microsoft.com* com assunto = Elastic de tarefas de pré-visualização. Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Esta pré-visualização é limitada, por isso, contacte *SaaSFeedback\@microsoft.com* para questões relacionadas ou suporte.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gestão de esquemas SaaS

O modelo de base de dados em partição horizontal multi-inquilino utilizado neste exemplo permite que uma base de dados de inquilinos conter um ou mais inquilinos. Este exemplo explora o potencial para utilizar uma combinação de um inquilino de muitos um inquilino bancos de dados e ativar um *híbrida* modelo de gestão do inquilino. Gerenciamento de alterações a estas bases de dados pode ser complicado. [Tarefas elásticas](sql-database-elastic-jobs-overview.md) facilitam a administração e gestão de grandes quantidades de base de dados. Tarefas permitem-lhe de forma segura e fiável executar scripts do Transact-SQL como tarefas, em relação a um grupo de bases de dados do inquilino. As tarefas são independentes da interação do utilizador ou de uma entrada. Este método pode ser utilizado para implementar alterações de esquema ou dados de referência comuns, em todos os inquilinos numa aplicação. Tarefas elásticas também podem ser utilizadas para manter uma cópia do modelo dourada da base de dados. O modelo é utilizado para criar novos inquilinos ao assegurar sempre o esquema mais recente e dados de referência estão em utilização.

![ecrã](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Pré-visualização limitada das Tarefas Elásticas

Há uma nova versão das tarefas elásticas, que agora é uma funcionalidade integrada da base de dados do Azure SQL. Esta nova versão das Tarefas Elásticas está atualmente em pré-visualização limitada. O limitado atualmente pré-visualização suporta a utilização do PowerShell para criar um agente de tarefa e T-SQL para criar e gerir tarefas.
> [!NOTE]
> Este tutorial utiliza funcionalidades do serviço de base de dados SQL que estão em pré-visualização limitada (tarefas de base de dados elástica). Se pretender realizar este tutorial, forneça o seu ID de subscrição para SaaSFeedback@microsoft.com com assunto = Elastic de tarefas de pré-visualização. Depois de receber a confirmação de que a sua subscrição foi ativada, transfira e instale os cmdlets de tarefas de pré-lançamento mais recente. Esta pré-visualização é limitada, portanto, entre em contato com SaaSFeedback@microsoft.com para questões relacionadas ou suporte.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obter o código de origem da aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino e os scripts

Os scripts de base de dados do Wingtip Tickets SaaS multi-inquilino e o código de origem da aplicação estão disponíveis no [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repositório no GitHub. Consulte a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip Tickets SaaS de desbloqueio.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Criar um agente de tarefa da base de dados e o novo agente de tarefa

Este tutorial requer a utilização do PowerShell para criar a base de dados de agente de tarefa e o agente de tarefa. Como a base de dados MSDB utilizada pelo agente do SQL, um agente de tarefa utiliza uma base de dados SQL do Azure para armazenar definições de tarefa, o estado da tarefa e o histórico. Depois do agente de tarefa é criado, pode criar e monitorizar tarefas de imediato.

1. Na **ISE do PowerShell**, abra *... \\Módulos de aprendizagem\\gestão de esquemas\\Demo-SchemaManagement.ps1*.
2. Prima **F5** para executar o script.

O *Demo-SchemaManagement.ps1* chamadas de script a *Deploy-SchemaManagement.ps1* script para criar uma base de dados com o nome _jobagent_ no servidor de catálogo. O script, em seguida, cria o agente de tarefa, passando a _jobagent_ base de dados como um parâmetro.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar uma tarefa para implementar novos dados de referência para todos os inquilinos

#### <a name="prepare"></a>Preparação

Base de dados de cada inquilino inclui um conjunto de tipos de local no **VenueTypes** tabela. Cada tipo de local define o tipo de eventos que podem ser hospedados num local. Estes tipos de local correspondem às imagens em segundo plano que verá na aplicação de eventos do inquilino.  Neste exercício, vai implementar uma atualização para todas as bases de dados para adicionar dois tipos de local adicionais: *Corrida de motos* e *natação*.

Em primeiro lugar, reveja os tipos de local incluídos em cada base de dados do inquilino. Ligar a uma das bases de dados do inquilino no SQL Server Management Studio (SSMS) e inspecionar a tabela de VenueTypes.  Também pode consultar essa tabela no editor de consultas no portal do Azure, acedido a partir da página base de dados.

1. Abra o SSMS e ligue-se para o servidor de inquilino: *tenants1-dpt -&lt;utilizador&gt;. database.windows.net*
1. Para confirmar que *corrida de motos* e *natação* **não estão** atualmente incluídos, navegue para o *contosoconcerthall* base de dados no *tenants1-dpt -&lt;usuário&gt;*  servidor e consulta o *VenueTypes* tabela.



#### <a name="steps"></a>Passos

Agora, criar uma tarefa para atualizar o **VenueTypes** tabela cada base de dados de inquilinos, adicionando dois novos tipos de local.

Para criar uma nova tarefa, utilize o conjunto de procedimentos armazenados do sistema tarefas que foram criados no _jobagent_ base de dados. Os procedimentos armazenados criados quando o agente de tarefa foi criado.

1. No SSMS, ligue ao servidor de inquilinos: tenants1-mt -&lt;utilizador&gt;. database.windows.net

2. Navegue para o *tenants1* base de dados.

3. Consulta a *VenueTypes* tabela para confirmar que *corrida de motos* e *natação* não estão ainda na lista de resultados.

4. Ligar ao servidor de catálogo, o que é *catálogo-mt -&lt;usuário&gt;. database.windows.net*.

5. Ligar para o _jobagent_ base de dados no servidor de catálogo.

6. No SSMS, abra o ficheiro *... \\Módulos de aprendizagem\\gestão de esquemas\\deployreferencedata. SQL*.

7. Modificar a instrução: defina @User = &lt;utilizador&gt; e substitua o valor de utilizador que usou quando implementou a aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino.

8. Prima **F5** para executar o script.

#### <a name="observe"></a>Observar

Observe os seguintes itens no *deployreferencedata. SQL* script:

- **SP\_adicione\_destino\_grupo** cria o nome do grupo de destino *DemoServerGroup*, e adiciona os membros de destino ao grupo.

- **SP\_adicione\_destino\_grupo\_membro** adiciona os seguintes itens:
    - R *servidor* tipo de membro de destino.
        - Este é o *tenants1-mt -&lt;usuário&gt;*  servidor que contém as bases de dados de inquilinos.
        - Incluindo o servidor inclui as bases de dados do inquilino que existem no momento que a tarefa é executada.
    - R *base de dados* tipo de membro para a base de dados do modelo de destino (*basetenantdb*) que reside no *catálogo-mt -&lt;utilizador&gt;*  servidor,
    - R *base de dados* direcionar o tipo de membro para incluir o *adhocreporting* base de dados que é utilizado um tutorial posterior.

- **SP\_adicione\_tarefa** cria uma tarefa denominada *implementação de dados de referência*.

- **SP\_adicione\_jobstep** cria o passo da tarefa que contém o texto do comando T-SQL para atualizar a tabela de referência, VenueTypes.

- As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Utilize estas consultas para consultar o valor de estado na **ciclo de vida** coluna para determinar quando a tarefa foi concluída. A tarefa atualiza a base de dados de inquilinos e atualiza os adicionais e duas bases de dados que contêm a tabela de referência.

No SSMS, navegue para a base de dados do inquilino no *tenants1-mt -&lt;usuário&gt;*  server. Consulta a *VenueTypes* tabela para confirmar que *corrida de motos* e *natação* agora são adicionados à tabela. A contagem total de tipos de local deve ter aumentado por dois.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar uma tarefa para gerir o índice de tabela de referência

Este exercício vai criar uma tarefa para reconstruir o índice na chave de principal de tabela de referência em todas as bases de dados de inquilino. Uma recompilação de índice é uma operação de gestão de base de dados típica que um administrador possa ser executadas depois de carregar uma grande quantidade de carregamento de dados, para melhorar o desempenho.

1. No SSMS, ligue ao _jobagent_ base de dados no *catálogo-mt -&lt;utilizador&gt;. database.windows.net* server.

2. No SSMS, abra *... \\Módulos de aprendizagem\\gestão de esquemas\\onlinereindex. SQL*.

3. Prima **F5** para executar o script.

#### <a name="observe"></a>Observar

Observe os seguintes itens no *onlinereindex. SQL* script:

* **SP\_adicione\_tarefa** cria uma nova tarefa denominada *reindexar Online PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **SP\_adicione\_jobstep** cria o passo da tarefa que contém o texto do comando T-SQL para atualizar o índice.

* As vistas restantes no script de monitorizar a execução de tarefa. Utilize estas consultas para consultar o valor de estado na **ciclo de vida** coluna para determinar quando a tarefa foi concluída com êxito em todos os membros do grupo de destino.

## <a name="additional-resources"></a>Recursos adicionais

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)](sql-database-elastic-jobs-overview.md)
* [Create and manage scaled-out cloud databases (Criar e gerir bases de dados de escalamento horizontal na cloud)](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um agente de tarefa para executar tarefas de T-SQL em várias bases de dados
> * Atualizar os dados de referência de todas as bases de dados de inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos

Em seguida, tente o [relatórios Ad hoc tutorial](saas-multitenantdb-adhoc-reporting.md) para explorar a execução de consultas distribuídas pelo inquilino bases de dados.

