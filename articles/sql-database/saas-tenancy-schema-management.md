---
title: Gerenciar o esquema do banco de dados SQL do Azure em um aplicativo de locatário único | Microsoft Docs
description: Gerenciar o esquema para vários locatários em um aplicativo de locatário único que usa o banco de dados SQL do Azure
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
ms.openlocfilehash: 7b238044fd3795ae2f49c2fa21367e6499a65672
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570127"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Gerenciar o esquema em um aplicativo SaaS usando o padrão de banco de dados por locatário com o banco de dados SQL do Azure
 
À medida que um aplicativo de banco de dados evolui, as alterações inevitavelmente precisam ser feitas no esquema de banco de dados ou no dado de referência.  As tarefas de manutenção de banco de dados também são necessárias periodicamente. O gerenciamento de um aplicativo que usa o padrão de banco de dados por locatário exige que você aplique essas alterações ou tarefas de manutenção em uma frota de bancos de dados de locatário.

Este tutorial explora dois cenários – implantando atualizações de dados de referência para todos os locatários e recompilando um índice na tabela que contém os dados de referência. O recurso [trabalhos elásticos](elastic-jobs-overview.md) é usado para executar essas ações em todos os bancos de dados de locatário e no banco de dados de modelo usado para criar novos bancos de dados de locatário.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Criar um agente de trabalho
> * Fazer com que trabalhos T-SQL sejam executados em todos os bancos de dados de locatário
> * Atualizar dados de referência em todos os bancos de dado de locatário
> * Criar um índice numa tabela em todas as bases de dados de inquilinos


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* O aplicativo de banco de dados por locatário SaaS Wingtip tickets foi implantado. Para implantar em menos de cinco minutos, consulte [implantar e explorar o aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Está instalada a versão mais recente do SQL Server Management Studio (SSMS). [Transferir e instalar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Este tutorial usa recursos do serviço de banco de dados SQL que estão em uma versão prévia limitada (trabalhos de Banco de Dados Elástico). Se você quiser fazer este tutorial, forneça sua ID de assinatura para SaaSFeedback@microsoft.com com Subject = visualização de trabalhos elásticos. Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Essa visualização é limitada, portanto, SaaSFeedback@microsoft.com entre em contato com perguntas ou suporte relacionados.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gerenciamento de esquema SaaS

O padrão Database per locatário isola os dados do locatário com eficiência, mas aumenta o número de bancos de dado a serem gerenciados e mantidos. Os [trabalhos elásticos](elastic-jobs-overview.md) facilitam a administração e o gerenciamento de bancos de dados SQL. Os trabalhos permitem que você execute tarefas (scripts T-SQL) de forma segura e confiável em um grupo de bancos de dados. Os trabalhos podem implantar o esquema e as alterações de dados de referência comuns em todos os bancos de dado de locatário em um aplicativo. Os trabalhos elásticos também podem ser usados para manter um banco de dados de *modelo* usado para criar novos locatários, garantindo que ele sempre tenha o esquema e os dados de referência mais recentes.

![ecrã](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Pré-visualização limitada das Tarefas Elásticas

Há uma nova versão dos trabalhos elásticos que agora é um recurso integrado do banco de dados SQL do Azure. Esta nova versão das Tarefas Elásticas está atualmente em pré-visualização limitada. Essa versão prévia limitada atualmente dá suporte ao uso do PowerShell para criar um agente de trabalho e ao T-SQL para criar e gerenciar trabalhos.

> [!NOTE]
> Este tutorial usa recursos do serviço de banco de dados SQL que estão em uma versão prévia limitada (trabalhos de Banco de Dados Elástico). Se você quiser fazer este tutorial, forneça sua ID de assinatura para SaaSFeedback@microsoft.com com Subject = visualização de trabalhos elásticos. Após receber a confirmação indicando que a sua subscrição foi ativada, deverá [transferir e instalar os cmdlets mais recentes das tarefas da versão de pré-lançamento](https://github.com/jaredmoo/azure-powershell/releases). Essa visualização é limitada, portanto, SaaSFeedback@microsoft.com entre em contato com perguntas ou suporte relacionados.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de aplicativo de banco de dados por locatário SaaS Wingtip tickets

O código-fonte do aplicativo e os scripts de gerenciamento estão disponíveis no repositório GitHub [repositório wingtipticketssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip tickets.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Criar um banco de dados do agente de trabalho e um novo agente de trabalho

Este tutorial requer que você use o PowerShell para criar um agente de trabalho e seu banco de dados do agente de trabalho de backup. O banco de dados do agente de trabalho contém definições de trabalho, status do trabalho e histórico. Depois que o agente de trabalho e seu banco de dados são criados, você pode criar e monitorar trabalhos imediatamente.

1. **No ISE do PowerShell**, abra... Learning modules\\Schema\\Management*demo-schemamanagement. ps1.* \\
1. Prima **F5** para executar o script.

O script *demo-schemamanagement. ps1* chama o script *Deploy-schemamanagement. ps1* para criar um banco de dados SQL chamado *osagent* no servidor de catálogo. Em seguida, ele cria o agente de trabalho, usando o banco de dados como um parâmetro.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar uma tarefa para implementar novos dados de referência para todos os inquilinos

No aplicativo Wingtip tickets, cada banco de dados de locatário inclui um conjunto de tipos de local com suporte. Cada local é de um tipo específico de local, que define o tipo de eventos que podem ser hospedados e determina a imagem de plano de fundo usada no aplicativo. Para que o aplicativo dê suporte a novos tipos de eventos, esses dados de referência devem ser atualizados e novos tipos de local adicionados.  Neste exercício, você implantará uma atualização em todos os bancos de dados de locatário para adicionar dois tipos de local adicionais: *Corrida Motorcycle* e *clube nadare*.

Primeiro, examine os tipos de local incluídos em cada banco de dados de locatário. Conecte-se a um dos bancos de dados de locatário no SQL Server Management Studio (SSMS) e inspecione a tabela VenueTypes.  Você também pode consultar essa tabela no editor de consultas no portal do Azure, acessada na página do banco de dados. 

1. Abra o SSMS e conecte-se ao servidor de locatário: *tenants1&lt;-&gt;DPT-User. Database.Windows.net*
1. Para confirmar se o *Motorcycle de corrida* e o *clube nadare* **não estão** incluídos no momento, navegue até o banco de dados _contosoconcerthall_ no servidor *tenants1-&lt;DPT-user&gt;*  e consulte o  *Tabela VenueTypes* .

Agora, vamos criar um trabalho para atualizar a tabela *VenueTypes* em todos os bancos de dados de locatário para adicionar os novos tipos de local.

Para criar um novo trabalho, você usa um conjunto de trabalhos de procedimentos armazenados do sistema criados no banco de dados _jobagent_ quando o agente de trabalho foi criado.

1. No SSMS, conecte-se ao servidor de catálogo: *Catalog-&lt;DPT&gt;-User. Database.Windows.net* Server 
1. No SSMS, abra o arquivo... Gerenciamento de esquema de módulos\\de aprendizado DeployReferenceData. SQL\\ \\
1. Modifique a instrução: Definir @wtpUser = &lt;usuário&gt; e substituir o valor de usuário usado quando você implantou o aplicativo de banco de dados por locatário SaaS Wingtip tickets
1. Verifique se você está conectado ao banco de dados _jobagent_ e pressione **F5** para executar o script

Observe os seguintes elementos no script *DeployReferenceData. SQL* :
* **SP\_Add\_TargetGroup\_** cria o nome do grupo de destino DemoServerGroup.
* **\_SP\_adicionarmembrodo\_grupo de destino é usado para definir o conjunto de bancos de dados de destino.\_**  Primeiro, o servidor _tenants1-&lt;DPT&gt; -User_ é adicionado.  Adicionar o servidor como um destino faz com que os bancos de dados nesse servidor no momento da execução do trabalho sejam incluídos no trabalho. Em seguida, o banco de dados _basetenantdb_ e o banco de dados *adhocreporting* (usado em um tutorial posterior) são adicionados como destinos.
* **SP\_Adicionar\_trabalho** cria um trabalho chamado _implantação de dados de referência_.
* **SP\_Add\_JobStep** cria a etapa de trabalho que contém o texto do comando T-SQL para atualizar a tabela de referência, VenueTypes.
* As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Use essas consultas para examinar o valor de status na coluna **ciclo de vida** para determinar quando o trabalho foi concluído em todos os bancos de dados de destino.

Depois que o script for concluído, você poderá verificar se os dados de referência foram atualizados.  No SSMS, navegue até o banco de dados *contosoconcerthall* no servidor *tenants1-&lt;DPT&gt; -User* e consulte a tabela *VenueTypes* .  Verifique se a *corrida Motorcycle* e o *clube nadare* **estão** presentes agora.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar uma tarefa para gerir o índice de tabela de referência

Este exercício usa um trabalho para recriar o índice na chave primária da tabela de referência.  Essa é uma operação de manutenção de banco de dados típica que pode ser feita após o carregamento de grandes quantidades.

Crie uma tarefa com os mesmos procedimentos armazenados no “sistema” das tarefas.

1. Abra o SSMS e conecte-se ao servidor _Catalog&lt;-&gt;DPT-User. Database.Windows.net_
1. Abra o arquivo _... Gerenciamento de esquema de módulos\\de aprendizado OnlineReindex. SQL\\ \\_
1. Clique com o botão direito do mouse, selecione conexão e conecte-se ao servidor _Catalog-DPT-&lt;&gt;User. Database.Windows.net_ , se ainda não estiver conectado
1. Verifique se você está conectado ao banco de dados _jobagent_ e pressione **F5** para executar o script

Observe os seguintes elementos no script _OnlineReindex. SQL_ :
* **SP\_Add\_Job** cria um novo trabalho chamado "online REINDEX CP\_\_VenueTyp\_\_265E44FD7FD4C885"
* **SP\_Add\_JobStep** cria a etapa de trabalho que contém o texto do comando T-SQL para atualizar o índice
* As exibições restantes no script monitoram a execução do trabalho. Use essas consultas para examinar o valor de status na coluna **ciclo de vida** para determinar quando o trabalho foi concluído com êxito em todos os membros do grupo de destino.



## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Criar um agente de trabalho para executar em trabalhos do T-SQL vários bancos de dados
> * Atualizar dados de referência em todos os bancos de dado de locatário
> * Criar um índice numa tabela em todas as bases de dados de inquilinos

Em seguida, experimente o [tutorial de relatórios ad hoc](saas-tenancy-cross-tenant-reporting.md) para explorar a execução de consultas distribuídas em bancos de dados de locatário.


## <a name="additional-resources"></a>Recursos adicionais

* [TUTORIAIS adicionais que se baseiam na implantação do aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)](elastic-jobs-overview.md)