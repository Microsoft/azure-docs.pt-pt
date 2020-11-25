---
title: Gerir o esquema numa app multi-inquilino
description: Gerir o Esquema para vários inquilinos numa aplicação multi-inquilino que utiliza a Base de Dados SQL do Azure
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
ms.openlocfilehash: d222234cd6ff3d910e6dbc51a394695ce467edce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011864"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-databases"></a>Gerir o esquema numa aplicação SaaS que utiliza bases de dados de vários inquilinos com oncimado
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este tutorial examina os desafios na manutenção de uma frota de bases de dados numa aplicação de Software como serviço (SaaS). São demonstradas soluções para eliminar as alterações de esquemas em toda a frota de bases de dados.

Como qualquer aplicação, a aplicação Wingtip Tickets SaaS irá evoluir ao longo do tempo, e irá exigir alterações na base de dados. As alterações podem ter impacto no esquema ou nos dados de referência ou aplicar tarefas de manutenção de bases de dados. Com uma aplicação SaaS utilizando uma base de dados por padrão de inquilino, as alterações devem ser coordenadas através de uma frota potencialmente massiva de bases de dados de inquilinos. Além disso, deve incorporar estas alterações no processo de provisionamento da base de dados para garantir que estas sejam incluídas em novas bases de dados à medida que são criadas.

#### <a name="two-scenarios"></a>Dois cenários

Este tutorial explora os seguintes dois cenários:
- Implemente atualizações de dados de referência para todos os inquilinos.
- Reconstruir um índice na tabela que contenha os dados de referência.

A funcionalidade [De Empregos Elásticos](./elastic-jobs-overview.md) da Base de Dados Azure SQL é usada para executar estas operações através das bases de dados dos inquilinos. Os trabalhos também funcionam na base de dados de inquilinos "modelo". Na aplicação de amostra de Bilhetes Wingtip, esta base de dados de modelos é copiada para a disponibilização de uma nova base de dados de inquilinos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um agente de trabalho.
> * Execute uma consulta T-SQL em várias bases de dados de inquilinos.
> * Atualizar dados de referência em todas as bases de dados dos inquilinos.
> * Crie um índice numa tabela em todas as bases de dados de inquilinos.

## <a name="prerequisites"></a>Pré-requisitos

- A aplicação de base de dados multi-inquilinos Wingtip Tickets já deve ser implementada:
    - Para obter instruções, consulte o primeiro tutorial, que introduz a aplicação de base de dados multi-inquilinos Wingtip Tickets SaaS:<br />[Implemente e explore uma aplicação de vários inquilinos que utiliza a Base de Dados Azure SQL.](./saas-multitenantdb-get-started-deploy.md)
        - O processo de implantação dura menos de cinco minutos.
    - Deve ter a versão *de vários inquilinos do* Wingtip instalada. As versões para *Standalone* e *Database por inquilino* não suportam este tutorial.

- A versão mais recente do SQL Server Management Studio (SSMS) deve ser instalada. [Descarregue e instale SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell deve ser instalado. Para mais detalhes, consulte [Começar com a Azure PowerShell](/powershell/azure/get-started-azureps).

> [!NOTE]
> Este tutorial utiliza funcionalidades do serviço de base de dados Azure SQL que estão numa pré-visualização limitada[(trabalhos de Base de Dados Elásticas).](elastic-database-client-library.md) Se desejar fazer este tutorial, forneça o seu ID de subscrição ao *SaaSFeedback \@ microsoft.com* com subject=Elastic Jobs Preview. Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Esta pré-visualização é limitada, por isso contacte *o SaaSFeedback \@ microsoft.com* para questões relacionadas ou suporte.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gestão de esquemas SaaS

O modelo de base de dados de vários inquilinos em pedaços utilizado nesta amostra permite que uma base de dados de inquilinos contenha um ou mais inquilinos. Esta amostra explora o potencial de usar uma mistura de uma base de dados de muitos inquilinos e um inquilino, permitindo um modelo de gestão de inquilinos *híbridos.* Gerir as alterações nestas bases de dados pode ser complicado. [A Elastic Jobs](./elastic-jobs-overview.md) facilita a administração e gestão de um grande número de bases de dados. Os empregos permitem-lhe executar de forma segura e fiável scripts Transact-SQL como tarefas, contra um grupo de bases de dados de inquilinos. As tarefas são independentes da interação ou entrada do utilizador. Este método pode ser usado para implementar alterações no esquema ou em dados de referência comuns, em todos os inquilinos numa aplicação. O Elastic Jobs também pode ser usado para manter uma cópia de modelo dourado da base de dados. O modelo é usado para criar novos inquilinos, garantindo sempre que os mais recentes esquemas e dados de referência estão em uso.

![ecrã](./media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Pré-visualização limitada das Tarefas Elásticas

Existe uma nova versão da Elastic Jobs que é agora uma característica integrada da Azure SQL Database. Esta nova versão das Tarefas Elásticas está atualmente em pré-visualização limitada. A pré-visualização limitada suporta atualmente a utilização do PowerShell para criar um agente de trabalho, e t-SQL para criar e gerir postos de trabalho.
> [!NOTE]
> Este tutorial utiliza funcionalidades do serviço de Base de Dados SQL que estão numa pré-visualização limitada (tarefas da Base de Dados Elástica). Se desejar fazer este tutorial, forneça o seu ID de subscrição SaaSFeedback@microsoft.com com subject=Elastic Jobs Preview. Após receber a confirmação indicando que a sua subscrição foi ativada, deverá transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento. Esta pré-visualização é limitada, por isso contacte SaaSFeedback@microsoft.com para questões relacionadas ou suporte.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obtenha o código fonte de origem de aplicação de aplicação de pedidos de bases de dados de ponta da Wingtip SaaS Multi-inquilinos

Os scripts de base de dados de multi-inquilinos Wingtip SaaS e código fonte de aplicação estão disponíveis no [repositório WingtipTicketsSA-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) no GitHub. Consulte as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para os passos para descarregar e desbloquear os scripts Wingtip Tickets SaaS.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Criar uma base de dados de agentes de emprego e um novo agente de emprego

Este tutorial requer que utilize o PowerShell para criar a base de dados de agentes de trabalho e o agente de trabalho. Tal como a base de dados MSDB utilizada pelo SqL Agent, um agente de trabalho utiliza uma base de dados na Base de Dados Azure SQL para armazenar definições de emprego, estado de trabalho e histórico. Após a criação do agente de trabalho, pode criar e monitorizar os trabalhos imediatamente.

1. Em **PowerShell ISE,** *aberto... \\Demo-SchemaManagement.ps1\\ de Gestão \\ de Módulos de Aprendizagem.*
2. Prima **F5** para executar o script.

O *Demo-SchemaManagement.ps1* script chama *o* Deploy-SchemaManagement.ps1script para criar uma base de dados chamada _jobagent_ no servidor do catálogo. O guião cria então o agente de trabalho, passando a base _de dados de trabalho_ como parâmetro.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar uma tarefa para implementar novos dados de referência para todos os inquilinos

#### <a name="prepare"></a>Preparação

A base de dados de cada inquilino inclui um conjunto de tipos de locais na tabela **VenueTypes.** Cada tipo de local define o tipo de eventos que podem ser hospedados em um local. Estes tipos de locais correspondem às imagens de fundo que vê na aplicação de eventos de inquilinos.  Neste exercício, você implementa uma atualização para todas as bases de dados para adicionar dois tipos adicionais de locais: *Motociclismo* e *Clube de Natação.*

Em primeiro lugar, reveja os tipos de locais incluídos em cada base de dados de inquilinos. Ligue-se a uma das bases de dados dos inquilinos no SQL Server Management Studio (SSMS) e inspecione a tabela VenueTypes.  Pode ainda consultar esta tabela no editor de Consultas no portal Azure, acedido a partir da página de base de dados.

1. Abra sSMS e ligue-se ao servidor do inquilino: *inquilinos1-dpt-user &lt; &gt; .database.windows.net*
1. Para confirmar que *o Clube de Motociclismo* e *Natação* **não estão** atualmente incluídos, consulte a base de dados *contosoconcerthall* no servidor do *&lt; utilizador &gt; de 1-dpt dos inquilinos* e consulte a tabela *VenueTypes.*



#### <a name="steps"></a>Passos

Agora cria um trabalho para atualizar a tabela **VenueTypes** em cada base de dados de inquilinos, adicionando os dois novos tipos de locais.

Para criar um novo emprego, utiliza-se o conjunto de procedimentos de armazenação do sistema de empregos que foram criados na base _de dados do jobagent._ Os procedimentos armazenados foram criados quando o agente de trabalho foi criado.

1. No SSMS, ligue-se ao servidor do inquilino: inquilinos1-mt- &lt; utilizador &gt; .database.windows.net

2. Procure na base *de dados dos inquilinos1.*

3. Consultar a tabela *VenueTypes* para confirmar que *o Clube de Motociclismo* e *Natação* ainda não está na lista de resultados.

4. Ligue-se ao servidor de catálogo, que é *o utilizador do catálogo-mt-database.windows.net &lt; &gt;*.

5. Ligue-se à base _de dados jobagent_ no servidor do catálogo.

6. Na SSMS, abra o *ficheiro... \\ Módulos de Aprendizagem \\ Schema Management \\ ImplementarReferenceData.sql*.

7. Modifique a declaração: set @User = &lt; utilizador e &gt; substitua o valor do Utilizador utilizado quando implementou a aplicação Wingtip Tickets SaaS Multi-tenant Database.

8. Prima **F5** para executar o script.

#### <a name="observe"></a>Observar

Observe os seguintes itens no *script .sql ImplementarReferenceData:*

- **sp \_ add target \_ \_ group** cria o nome de grupo alvo *DemoServerGroup*, e adiciona membros-alvo ao grupo.

- **sp \_ adicionar membro \_ \_ \_ do grupo alvo** adiciona os seguintes itens:
    - Um tipo de membro alvo do *servidor.*
        - Este é o servidor *de &lt; &gt; utilizadores de inquilinos de 1 mt* que contém as bases de dados dos inquilinos.
        - Incluindo o servidor inclui as bases de dados dos inquilinos que existem no momento em que o trabalho executa.
    - Um tipo de membro alvo *de base de dados* para a base de dados de modelos *(basetenantdb)* que reside no servidor *do &lt; utilizador &gt; do catálogo-mt,*
    - Um tipo de membro alvo *de base de dados* para incluir a base de dados *de adhocreporting* que é usada num tutorial posterior.

- **sp \_ add \_ job** cria um trabalho chamado *Reference Data Deployment*.

- **sp \_ add \_ jobstep** cria o passo de trabalho contendo texto de comando T-SQL para atualizar a tabela de referência, VenueTypes.

- As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Utilize estas consultas para rever o valor de estado na coluna do **ciclo de vida** para determinar quando o trabalho terminou. O trabalho atualiza a base de dados dos inquilinos e atualiza as duas bases de dados adicionais que contêm a tabela de referência.

Em SSMS, consulte a base de dados do inquilino no servidor *de &lt; utilizadores &gt; de inquilinos1-mt.* Consultar a tabela *VenueTypes* para confirmar que *o Clube de Motociclismo* e *Natação* são agora adicionados à mesa. A contagem total de tipos de locais deveria ter aumentado em dois.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar uma tarefa para gerir o índice de tabela de referência

Este exercício cria um trabalho para reconstruir o índice na chave primária da tabela de referência em todas as bases de dados dos inquilinos. Uma reconstrução de índices é uma operação típica de gestão de bases de dados que um administrador pode executar depois de carregar uma grande quantidade de carga de dados, para melhorar o desempenho.

1. No SSMS, ligue-se à base _de dados jobagent_ no servidor *catalog-mt-User &lt; &gt; .database.windows.net.*

2. Em SSMS, *aberto... \\ Módulos de Aprendizagem \\ Schema Management \\ OnlineReindex.sql*.

3. Prima **F5** para executar o script.

#### <a name="observe"></a>Observar

Observe os seguintes itens no script *.sql OnlineReindex:*

* **sp \_ add \_ job** cria um novo emprego chamado *Online Reindex PK \_ \_ VenueTyp \_ \_ 265E44FD7FD4C885*.

* **sp \_ add \_ jobstep** cria o passo de trabalho contendo texto de comando T-SQL para atualizar o índice.

* As restantes opiniões no script monitorizam a execução do trabalho. Utilize estas consultas para rever o valor de estado na coluna do **ciclo de vida** para determinar quando o trabalho terminou com sucesso em todos os membros do grupo alvo.

## <a name="additional-resources"></a>Recursos adicionais

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)](./elastic-jobs-overview.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um agente de trabalho para executar empregos T-SQL em várias bases de dados
> * Atualizar dados de referência em todas as bases de dados de inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos

Em seguida, experimente o [tutorial de reportagem ad hoc](./saas-multitenantdb-adhoc-reporting.md) para explorar consultas distribuídas em bases de dados de inquilinos.