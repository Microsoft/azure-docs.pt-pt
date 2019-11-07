---
title: Gerenciar o esquema do banco de dados SQL do Azure em um aplicativo multilocatário
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
ms.openlocfilehash: a4838e571c6dc678fba470ef7f1026388f55d444
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691976"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Gerenciar o esquema em um aplicativo SaaS que usa bancos de dados SQL com vários locatários fragmentados

Este tutorial examina os desafios de manter uma frota de bancos de dados em um aplicativo SaaS (software como serviço). As soluções são demonstradas para Fan as alterações de esquema em toda a frota de bancos de dados.

Como qualquer aplicativo, o aplicativo SaaS Wingtip tickets se desenvolverá ao longo do tempo e exigirá alterações no banco de dados. As alterações podem afetar o esquema ou os dados de referência ou aplicar tarefas de manutenção de banco de dado. Com um aplicativo SaaS usando um padrão de banco de dados por locatário, as alterações devem ser coordenadas em uma frota potencialmente massiva de bancos de dados de locatário. Além disso, você deve incorporar essas alterações no processo de provisionamento de banco de dados para garantir que elas sejam incluídas em novos bancos que forem criadas.

#### <a name="two-scenarios"></a>Dois cenários

Este tutorial explora os dois cenários a seguir:
- Implantar atualizações de dados de referência para todos os locatários.
- Recriar um índice na tabela que contém os dados de referência.

O recurso de [trabalhos elásticos](elastic-jobs-overview.md) do banco de dados SQL do Azure é usado para executar essas operações em bancos de dados de locatário. Os trabalhos também operam no banco de dados de locatário "modelo". No aplicativo de exemplo Wingtip tickets, esse banco de dados de modelo é copiado para provisionar um novo banco de dados de locatário.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um agente de trabalho.
> * Executar uma consulta T-SQL em vários bancos de dados de locatário.
> * Atualizar dados de referência em todos os bancos de dado de locatário.
> * Crie um índice em uma tabela em todos os bancos de dados de locatário.

## <a name="prerequisites"></a>Pré-requisitos

- O aplicativo de banco de dados de vários locatários Wingtip tickets já deve estar implantado:
    - Para obter instruções, consulte o primeiro tutorial, que apresenta o aplicativo de banco de dados multilocatário SaaS Wingtip tickets:<br />[Implante e explore um aplicativo multilocatário fragmentado que usa o banco de dados SQL do Azure](saas-multitenantdb-get-started-deploy.md).
        - O processo de implantação é executado por menos de cinco minutos.
    - Você deve ter a versão *multilocatário fragmentada* do Wingtip instalada. As versões para *autônomo* e *banco de dados por locatário* não dão suporte a este tutorial.

- A versão mais recente do SQL Server Management Studio (SSMS) deve ser instalada. [Baixe e instale o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell deve ser instalado. Para obter detalhes, consulte [introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Este tutorial usa recursos do serviço de banco de dados SQL do Azure que estão em uma versão prévia limitada ([trabalhos de banco de dados elástico](sql-database-elastic-database-client-library.md)). Se você quiser fazer este tutorial, forneça sua ID de assinatura para *SaaSFeedback\@Microsoft.com* com Subject = trabalhos elásticos Preview. Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Essa visualização é limitada, portanto, entre em contato com *SaaSFeedback\@Microsoft.com* para questões ou suporte relacionados.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gerenciamento de esquema SaaS

O modelo de banco de dados multilocatário fragmentado usado neste exemplo permite que um banco de dados de locatários contenha um ou mais locatários. Este exemplo explora o potencial de usar uma combinação de bancos de dados de muitos locatários e de um locatário, habilitando um modelo de gerenciamento de locatário *híbrido* . O gerenciamento de alterações nesses bancos de dados pode ser complicado. Os [trabalhos elásticos](elastic-jobs-overview.md) facilitam a administração e o gerenciamento de um grande número de banco de dados. Os trabalhos permitem a execução segura e confiável de scripts Transact-SQL como tarefas, em relação a um grupo de bancos de dados de locatário. As tarefas são independentes de interação ou entrada do usuário. Esse método pode ser usado para implantar alterações no esquema ou em dados de referência comuns, em todos os locatários em um aplicativo. Os trabalhos elásticos também podem ser usados para manter uma cópia do modelo de ouro do banco de dados. O modelo é usado para criar novos locatários, sempre garantindo que o esquema e os dados de referência mais recentes estejam em uso.

![ecrã](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Pré-visualização limitada das Tarefas Elásticas

Há uma nova versão de trabalhos elásticos que agora é um recurso integrado do banco de dados SQL do Azure. Esta nova versão das Tarefas Elásticas está atualmente em pré-visualização limitada. A versão prévia limitada atualmente dá suporte ao uso do PowerShell para criar um agente de trabalho e ao T-SQL para criar e gerenciar trabalhos.
> [!NOTE]
> Este tutorial usa recursos do serviço de banco de dados SQL que estão em uma versão prévia limitada (trabalhos de Banco de Dados Elástico). Se você quiser fazer este tutorial, forneça sua ID de assinatura para SaaSFeedback@microsoft.com com Subject = trabalho de elástico Preview. Depois de receber a confirmação de que sua assinatura foi habilitada, baixe e instale os cmdlets de trabalhos de pré-lançamento mais recentes. Essa visualização é limitada, portanto, entre em contato com SaaSFeedback@microsoft.com para perguntas ou suporte relacionados.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obtenha o código-fonte e os scripts do aplicativo de banco de dados multilocatário SaaS Wingtip tickets

Os scripts de banco de dados multilocatário do Wingtip tickets SaaS e o código-fonte do aplicativo estão disponíveis no repositório [repositório wingtipticketssaas-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) no github. Consulte as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip tickets.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Criar um banco de dados do agente de trabalho e um novo agente de trabalho

Este tutorial requer que você use o PowerShell para criar o banco de dados do agente de trabalho e o agente de trabalho. Como o banco de dados MSDB usado pelo SQL Agent, um agente de trabalho usa um banco de dados SQL do Azure para armazenar definições de trabalho, status do trabalho e histórico. Depois que o agente de trabalho é criado, você pode criar e monitorar trabalhos imediatamente.

1. No **ISE do PowerShell**, abra *...\\módulos de aprendizado\\gerenciamento de esquema\\demo-schemamanagement. ps1*.
2. Prima **F5** para executar o script.

O script *demo-schemamanagement. ps1* chama o script *Deploy-schemamanagement. ps1* para criar um banco de dados chamado _jobagent_ no servidor de catálogo. Em seguida, o script cria o agente de trabalho, passando o banco de dados _jobagent_ como um parâmetro.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar uma tarefa para implementar novos dados de referência para todos os inquilinos

#### <a name="prepare"></a>Preparação

O banco de dados de cada locatário inclui um conjunto de tipos de local na tabela **VenueTypes** . Cada tipo de local define o tipo de eventos que podem ser hospedados em um local. Esses tipos de local correspondem às imagens de plano de fundo que você vê no aplicativo de eventos de locatário.  Neste exercício, você implanta uma atualização para todos os bancos de dados para adicionar dois tipos de local adicionais: *corrida de Motorcycle* e clube de *nada*.

Primeiro, examine os tipos de local incluídos em cada banco de dados de locatário. Conecte-se a um dos bancos de dados de locatário no SQL Server Management Studio (SSMS) e inspecione a tabela VenueTypes.  Você também pode consultar essa tabela no editor de consultas no portal do Azure, acessada na página do banco de dados.

1. Abra o SSMS e conecte-se ao servidor de locatário: *tenants1-DPT-&lt;usuário&gt;. Database.Windows.net*
1. Para confirmar se *o Motorcycle de corrida* e o *clube* de Altova **não estão** incluídos no momento, navegue até o banco de dados *contosoconcerthall* no servidor do *tenants1-DPT-&lt;&gt;do usuário* e consulte o *VenueTypes* tabela.



#### <a name="steps"></a>Passos

Agora, você cria um trabalho para atualizar a tabela **VenueTypes** em cada banco de dados de locatários, adicionando os dois novos tipos de local.

Para criar um novo trabalho, use o conjunto de trabalhos que os procedimentos armazenados do sistema criaram no banco de dados _jobagent_ . Os procedimentos armazenados foram criados quando o agente de trabalho foi criado.

1. No SSMS, conecte-se ao servidor de locatário: tenants1-MT-&lt;usuário&gt;. database.windows.net

2. Navegue até o banco de dados *tenants1* .

3. Consulte a tabela *VenueTypes* para confirmar se o *Motorcycle de corrida* e o *clube nadare* ainda estão na lista de resultados.

4. Conecte-se ao servidor de catálogo, que é o *usuário Catalog-MT-&lt;&gt;. Database.Windows.net*.

5. Conecte-se ao banco de dados _jobagent_ no servidor de catálogo.

6. No SSMS, abra o arquivo *...\\módulos de aprendizado\\gerenciamento de esquema\\DeployReferenceData. SQL*.

7. Modifique a instrução: Set @User = &lt;usuário&gt; e substitua o valor de usuário usado quando você implantou o aplicativo de banco de dados multilocatário do Wingtip tickets SaaS.

8. Prima **F5** para executar o script.

#### <a name="observe"></a>Corretamente

Observe os seguintes itens no script *DeployReferenceData. SQL* :

- o **sp\_adicionar\_\_grupo de destino** cria o nome do grupo de destino *DemoServerGroup*e adiciona membros de destino ao grupo.

- **sp\_adicionar\_destino\_grupo\_membro** adiciona os seguintes itens:
    - Um tipo de membro de destino de *servidor* .
        - Esse é o servidor de *&gt;de usuário tenants1-MT-&lt;* que contém os bancos de dados de locatários.
        - A inclusão do servidor inclui os bancos de dados de locatário existentes no momento em que o trabalho é executado.
    - Um tipo de membro de destino de *banco de dados* para o banco de dados de modelo (*basetenantdb*) que reside em *Catalog-MT-&lt;usuário&gt;* Server,
    - Um tipo de membro de destino de *banco de dados* para incluir o banco de dados *adhocreporting* que é usado em um tutorial posterior.

- o **sp\_adicionar\_trabalho** cria um trabalho chamado *implantação de dados de referência*.

- **sp\_add\_JobStep** cria a etapa de trabalho que contém o texto do comando t-SQL para atualizar a tabela de referência, VenueTypes.

- As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Use essas consultas para examinar o valor de status na coluna **Lifecycle** para determinar quando o trabalho foi concluído. O trabalho atualiza o banco de dados de locatários e atualiza os dois bancos de dados adicionais que contêm a tabela de referência.

No SSMS, navegue até o banco de dados de locatário no servidor de *&gt;de usuário tenants1-MT-&lt;* . Consulte a tabela *VenueTypes* para confirmar se a *corrida de Motorcycle* e o clube de *nada* de que agora estão adicionados à tabela. A contagem total de tipos de local deve ter aumentado em dois.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar uma tarefa para gerir o índice de tabela de referência

Este exercício cria um trabalho para recriar o índice na chave primária da tabela de referência em todos os bancos de dados de locatário. Uma recompilação de índice é uma operação de gerenciamento de banco de dados típica que um administrador pode executar após o carregamento de uma grande quantidade de carregamento, para melhorar o desempenho.

1. No SSMS, conecte-se ao banco de dados _jobagent_ no *Catalog-MT-&lt;usuário&gt;. Database.Windows.net* Server.

2. No SSMS, abra *...\\módulos de aprendizado\\gerenciamento de esquema\\OnlineReindex. SQL*.

3. Prima **F5** para executar o script.

#### <a name="observe"></a>Corretamente

Observe os seguintes itens no script *OnlineReindex. SQL* :

* o **sp\_adicionar\_trabalho** cria um novo trabalho denominado *CP Online reindex\_\_VENUETYP\_\_265E44FD7FD4C885*.

* **sp\_add\_JobStep** cria a etapa de trabalho que contém o texto do comando t-SQL para atualizar o índice.

* As exibições restantes no script monitoram a execução do trabalho. Use essas consultas para examinar o valor de status na coluna **ciclo de vida** para determinar quando o trabalho foi concluído com êxito em todos os membros do grupo de destino.

## <a name="additional-resources"></a>Recursos adicionais

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)](elastic-jobs-overview.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um agente de trabalho para executar trabalhos do T-SQL em vários bancos de dados
> * Atualizar dados de referência em todos os bancos de dado de locatário
> * Criar um índice numa tabela em todas as bases de dados de inquilinos

Em seguida, experimente o [tutorial de relatórios ad hoc](saas-multitenantdb-adhoc-reporting.md) para explorar a execução de consultas distribuídas em bancos de dados de locatário.

