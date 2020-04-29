---
title: Gerir o esquema numa aplicação multi-inquilino
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
ms.date: 12/18/2018
ms.openlocfilehash: 6f660426c41b37dd27438c28cbf603bdbf1e58b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79269201"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Gerir o esquema numa aplicação SaaS que utiliza bases de dados SQL multi-inquilinos

Este tutorial examina os desafios na manutenção de uma frota de bases de dados numa aplicação de Software como Serviço (SaaS). As soluções são demonstradas para apagar alterações de esquemas em toda a frota de bases de dados.

Como qualquer aplicação, a app Wingtip Tickets SaaS evoluirá ao longo do tempo, e exigirá alterações na base de dados. As alterações podem ter impacto em schema ou dados de referência, ou aplicar tarefas de manutenção da base de dados. Com uma aplicação SaaS utilizando uma base de dados por padrão de inquilino, as alterações devem ser coordenadas através de uma frota potencialmente massiva de bases de dados de inquilinos. Além disso, deve incorporar estas alterações no processo de fornecimento de bases de dados para garantir que estão incluídas em novas bases de dados à medida que são criadas.

#### <a name="two-scenarios"></a>Dois cenários

Este tutorial explora os dois seguintes cenários:
- Implemente atualizações de dados de referência para todos os inquilinos.
- Reconstruir um índice sobre a tabela que contém os dados de referência.

A funcionalidade ["Trabalhos Elásticos"](elastic-jobs-overview.md) da Base de Dados Azure SQL é utilizada para executar estas operações através das bases de dados dos inquilinos. Os trabalhos também funcionam na base de dados de inquilinos 'modelo'. Na aplicação de amostra sacar bilhetes wingtip, esta base de dados de modelos é copiada para fornecer uma nova base de dados de inquilinos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um agente de trabalho.
> * Execute uma consulta T-SQL em várias bases de dados de inquilinos.
> * Atualizar dados de referência em todas as bases de dados dos inquilinos.
> * Crie um índice sobre uma mesa em todas as bases de dados de inquilinos.

## <a name="prerequisites"></a>Pré-requisitos

- A aplicação de base de dados multi-inquilinos wingtip Tickets já deve ser implementada:
    - Para obter instruções, consulte o primeiro tutorial, que introduz a aplicação de base de dados multi-inquilinos Wingtip Tickets SaaS:<br />[Implemente e explore uma aplicação de multi-inquilinos espumosa que utiliza a Base de Dados Azure SQL.](saas-multitenantdb-get-started-deploy.md)
        - O processo de implantação dura menos de cinco minutos.
    - Você deve ter a versão *de vários inquilinos de* Wingtip instalado. As versões de *Autónomo* e Base de *Dados por inquilino* não suportam este tutorial.

- A versão mais recente do SQL Server Management Studio (SSMS) deve ser instalada. [Descarregue e instale SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- O Azure PowerShell tem de ser instalado. Para mais detalhes, consulte [Getting started with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Este tutorial utiliza funcionalidades do serviço de base de dados Azure SQL que se encontram numa pré-visualização limitada[(trabalhos de base de dados elásticos).](sql-database-elastic-database-client-library.md) Se desejar fazer este tutorial, forneça o seu ID de subscrição ao *\@SaaSFeedback microsoft.com* com subject=Elastic Jobs Preview. Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Esta pré-visualização é limitada, por isso contacte o *\@SaaSFeedback microsoft.com* para perguntas ou suporterelacionados.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gestão de esquemas SaaS

O modelo de base de dados multi-inquilinos usado nesta amostra permite que uma base de dados de inquilinos contenha um ou mais inquilinos. Esta amostra explora o potencial de usar uma mistura de bases de dados de muitos inquilinos e um inquilino, permitindo um modelo *híbrido* de gestão de inquilinos. Gerir alterações nestas bases de dados pode ser complicado. [A Elastic Jobs](elastic-jobs-overview.md) facilita a administração e gestão de um grande número de bases de dados. Os empregos permitem-lhe executar de forma segura e fiável scripts Transact-SQL como tarefas, contra um grupo de bases de dados de inquilinos. As tarefas são independentes da interação ou entrada do utilizador. Este método pode ser utilizado para implementar alterações ao esquema ou a dados comuns de referência, em todos os inquilinos de uma aplicação. Os Trabalhos Elásticos também podem ser usados para manter uma cópia do modelo dourado da base de dados. O modelo é usado para criar novos inquilinos, garantindo sempre que os dados mais recentes de esquemas e referências estão em uso.

![ecrã](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Pré-visualização limitada das Tarefas Elásticas

Existe uma nova versão de Elastic Jobs que é agora uma característica integrada da Base de Dados Azure SQL. Esta nova versão das Tarefas Elásticas está atualmente em pré-visualização limitada. A pré-visualização limitada suporta atualmente a utilização da PowerShell para criar um agente de trabalho, e a T-SQL para criar e gerir empregos.
> [!NOTE]
> Este tutorial utiliza funcionalidades do serviço de Base de Dados SQL que estão numa pré-visualização limitada (tarefas da Base de Dados Elástica). Se desejar fazer este tutorial, forneça SaaSFeedback@microsoft.com o seu ID de subscrição com subject=Elastic Jobs Preview. Após receber a confirmação indicando que a sua subscrição foi ativada, deverá transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento. Esta pré-visualização é SaaSFeedback@microsoft.com limitada, por isso contacte para questões relacionadas ou suporte.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obtenha os bilhetes De acesso saaS Multi-inquilinoCódigo fonte de aplicação e scripts

Os bilhetes Wingtip SaaS Scripts de base de dados multi-inquilinos e código fonte de aplicação estão disponíveis no repositório [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) no GitHub. Consulte a [orientação geral](saas-tenancy-wingtip-app-guidance-tips.md) para passos para descarregar e desbloquear os scripts Wingtip Tickets SaaS.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Criar uma base de dados de agente de emprego e um novo agente de trabalho

Este tutorial requer que utilize o PowerShell para criar a base de dados do agente de trabalho e o agente de trabalho. Tal como a base de dados MSDB utilizada pelo Agente SQL, um agente de trabalho usa uma base de dados Azure SQL para armazenar definições de emprego, estado de trabalho e histórico. Após a criação do agente de trabalho, pode criar e monitorizar os empregos imediatamente.

1. No **PowerShell ISE,** *aberto... Módulos de Aprendizagem\\Schema Management Demo-SchemaManagement.ps1 .\\ \\*
2. Prima **F5** para executar o script.

O script *Demo-SchemaManagement.ps1* chama o script *Deploy-SchemaManagement.ps1* para criar uma base de dados nomeada _agente de trabalho_ no servidor de catálogo. O guião cria então o agente de trabalho, passando a base de dados do _agente de trabalho_ como parâmetro.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar uma tarefa para implementar novos dados de referência para todos os inquilinos

#### <a name="prepare"></a>Preparação

A base de dados de cada inquilino inclui um conjunto de tipos de locais na tabela **VenueTypes.** Cada tipo de local define o tipo de eventos que podem ser hospedados num local. Estes tipos de locais correspondem às imagens de fundo que você vê na app de eventos de inquilinos.  Neste exercício, você implementa uma atualização para todas as bases de dados para adicionar dois tipos adicionais de local: *MotoRacing* e Clube de *Natação*.

Primeiro, reveja os tipos de locais incluídos em cada base de dados de inquilinos. Ligue-se a uma das bases de dados dos inquilinos no SQL Server Management Studio (SSMS) e inspecione a tabela VenueTypes.  Pode ainda consultar esta tabela no editor da Consulta no portal Azure, acedido a partir da página de base de dados.

1. Abra o SSMS e ligue-se ao servidor de *inquilinos: inquilinos1-dpt-user&lt;&gt;.database.windows.net*
1. Para confirmar que o Clube de *Motociclismo* e *Natação* **não está** atualmente incluído, navegue na base de dados *contosoconcerthall* no servidor de *&lt;utilizador&gt; dos inquilinos 1-dpt* e consulte a tabela *VenueTypes.*



#### <a name="steps"></a>Passos

Agora cria um trabalho para atualizar a tabela **VenueTypes** em cada base de dados de inquilinos, adicionando os dois novos tipos de locais.

Para criar um novo emprego, você usa o conjunto de procedimentos armazenados pelo sistema de empregos que foram criados na base de dados de _agentes de trabalho._ Os procedimentos armazenados foram criados quando o agente de trabalho foi criado.

1. No SSMS, ligue-se ao servidor de inquilinos: inquilinos1-mt-user&lt;&gt;.database.windows.net

2. Navegue na base de dados dos *inquilinos1.*

3. Consulta à tabela *VenueTypes* para confirmar que o Clube de *Motociclismo* e *Natação* ainda não está na lista de resultados.

4. Ligue-se ao servidor de catálogo, que é *o&lt;utilizador&gt;do catálogo .database.windows.net*.

5. Ligue-se à base de dados do _agente de trabalho_ no servidor de catálogo.

6. No SSMS, abra o *ficheiro... Módulos de Aprendizagem\\Schema Management DeployReferenceData.sql .\\ \\*

7. Modifique a @User declaração: definir = &lt;utilizador&gt; e substituir o valor do Utilizador utilizado quando implementou a aplicação de base de dados de bilhetes De vários inquilinos SaaS.

8. Prima **F5** para executar o script.

#### <a name="observe"></a>Observar

Observe os seguintes itens no script *DeployReferenceData.sql:*

- **sp\_\_adicionar\_grupo alvo** cria o nome de grupo alvo *DemoServerGroup*, e adiciona membros-alvo ao grupo.

- **Sp\_\_adicionar\_\_membro do grupo alvo** adiciona os seguintes itens:
    - Um tipo de membro alvo *do servidor.*
        - Este é o servidor de *&lt;utilizador&gt; de inquilinos 1 mt* que contém as bases de dados dos inquilinos.
        - Incluindo o servidor inclui as bases de dados dos inquilinos que existem no momento em que o trabalho executa.
    - Um tipo de membro-alvo de base de *dados* para a base de dados do modelo *(basetenantdb)* que reside no servidor de *utilizador&lt;&gt; de catálogo-mt,,*
    - Um tipo de membro alvo de base de *dados* para incluir a base de dados de *adhocreporting* que é usada num tutorial posterior.

- **sp\_\_adicionar trabalho** cria um trabalho chamado *Implantação*de Dados de Referência .

- **sp\_\_adicionar jobstep** cria o passo de trabalho contendo texto de comando T-SQL para atualizar a tabela de referência, VenueTypes.

- As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Utilize estas consultas para rever o valor de estado na coluna do ciclo de **vida** para determinar quando o trabalho está terminado. O trabalho atualiza a base de dados dos inquilinos e atualiza as duas bases de dados adicionais que contêm a tabela de referência.

No SSMS, navegue na base de dados dos inquilinos no servidor de *&lt;&gt; utilizadores de inquilinos1 mt.* Consulta à mesa *VenueTypes* para confirmar que o Clube de *Motociclismo* e *Natação* está agora adicionado à mesa. A contagem total de tipos de locais deveria ter aumentado em dois.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar uma tarefa para gerir o índice de tabela de referência

Este exercício cria um trabalho para reconstruir o índice na chave primária da tabela de referência em todas as bases de dados dos inquilinos. Uma reconstrução de índice é uma operação típica de gestão da base de dados que um administrador pode executar depois de carregar uma grande quantidade de carga de dados, para melhorar o desempenho.

1. No SSMS, ligue-se à base de dados de _agentes de trabalho_ no servidor de *catálogo-mt-utilizador&lt;&gt;.database.windows.net.*

2. No SSMS, *aberto... Módulos de Aprendizagem\\Schema Management OnlineReindex.sql .\\ \\*

3. Prima **F5** para executar o script.

#### <a name="observe"></a>Observar

Observe os seguintes itens no script *OnlineReindex.sql:*

* **sp\_\_add job** cria um novo trabalho chamado *Reindex Online\_\_\_\_PK VenueTyp 265E44FD4C885*.

* **sp\_\_adicionar jobstep** cria o passo de trabalho contendo texto de comando T-SQL para atualizar o índice.

* As restantes opiniões no script monitor execução de trabalho. Utilize estas consultas para rever o valor de estado na coluna do ciclo de **vida** para determinar quando o trabalho terminou com sucesso em todos os membros do grupo alvo.

## <a name="additional-resources"></a>Recursos adicionais

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)](elastic-jobs-overview.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um agente de trabalho para executar empregos T-SQL em várias bases de dados
> * Atualizar dados de referência em todas as bases de dados dos inquilinos
> * Criar um índice numa tabela em todas as bases de dados de inquilinos

Em seguida, experimente o tutorial de [reportagem ad hoc](saas-multitenantdb-adhoc-reporting.md) para explorar consultas distribuídas em todas as bases de dados dos inquilinos.

