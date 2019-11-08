---
title: Tutorial de SaaS de vários locatários
description: Provisionar e catalogar novos locatários usando o padrão de aplicativo autônomo
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/24/2018
ms.openlocfilehash: c4859554f361b262366bc2442d3819e2a029aa85
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822100"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Provisionar e catalogar novos locatários usando o aplicativo por padrão de SaaS de locatário

Este artigo aborda o provisionamento e a catalogação de novos locatários usando o aplicativo autônomo por padrão de SaaS de locatário.
Este artigo tem duas partes principais:
* Discussão conceitual sobre provisionamento e catalogação de novos locatários
* Um tutorial que realça o código do PowerShell de exemplo que realiza o provisionamento e a catalogação
    * O tutorial usa o aplicativo SaaS de exemplo Wingtip tickets, adaptado para o aplicativo autônomo por padrão de locatário.

## <a name="standalone-application-per-tenant-pattern"></a>Aplicativo autônomo por padrão de locatário

O padrão aplicativo autônomo por locatário é um dos vários padrões para aplicativos SaaS multilocatários.  Nesse padrão, um aplicativo autônomo é provisionado para cada locatário. O aplicativo consiste em componentes de nível de aplicativo e um banco de dados SQL.  Cada aplicativo de locatário pode ser implantado na assinatura do fornecedor.  Como alternativa, o Azure oferece um [programa de aplicativos gerenciados](https://docs.microsoft.com/azure/managed-applications/overview) no qual um aplicativo pode ser implantado na assinatura de um locatário e gerenciado pelo fornecedor em nome do locatário. 

   ![padrão de aplicativo por locatário](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Ao implantar um aplicativo para um locatário, o aplicativo e o banco de dados são provisionados em um novo grupo de recursos criado para o locatário.  O uso de grupos de recursos separados isola os recursos do aplicativo de cada locatário e permite que eles sejam gerenciados de forma independente. Dentro de cada grupo de recursos, cada instância de aplicativo é configurada para acessar o banco de dados correspondente diretamente.  Esse modelo de conexão contrasta com outros padrões que usam um catálogo para conexões de agente entre o aplicativo e o banco de dados.  E como não há compartilhamento de recursos, cada banco de dados de locatário deve ser provisionado com recursos suficientes para lidar com sua carga de pico. Esse padrão tende a ser usado para aplicativos SaaS com menos locatários, onde há uma forte ênfase no isolamento do locatário e menos ênfase nos custos de recursos.  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Usando um catálogo de locatários com o padrão de aplicativo por locatário

Embora o aplicativo e o banco de dados de cada locatário sejam totalmente isolados, vários cenários de gerenciamento e análise podem operar entre locatários.  Por exemplo, a aplicação de uma alteração de esquema para uma nova versão do aplicativo requer alterações no esquema de cada banco de dados de locatário. Cenários de relatório e análise também podem exigir acesso a todos os bancos de dados de locatário, independentemente de onde eles são implantados.

   ![padrão de aplicativo por locatário](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

O catálogo de locatários contém um mapeamento entre um identificador de locatário e um banco de dados de locatário, permitindo que um identificador seja resolvido para um nome de servidor e de banco de dados.  No aplicativo SaaS Wingtip, o identificador do locatário é calculado como um hash do nome do locatário, embora outros esquemas possam ser usados.  Embora os aplicativos autônomos não precisem do catálogo para gerenciar conexões, o catálogo pode ser usado para definir o escopo de outras ações para um conjunto de bancos de dados de locatário. Por exemplo, a consulta elástica pode usar o catálogo para determinar o conjunto de bancos de dados em que as consultas são distribuídas para relatórios entre locatários.

## <a name="elastic-database-client-library"></a>Biblioteca de Clientes da Base de Dados Elástica

No aplicativo de exemplo Wingtip, o catálogo é implementado pelos recursos de gerenciamento de fragmentos da EDCL ( [biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md) ).  A biblioteca permite que um aplicativo crie, gerencie e use um mapa de fragmentos que é armazenado em um banco de dados. No exemplo de tíquetes Wingtip, o catálogo é armazenado no banco de dados do *Catálogo de locatários* .  O fragmento mapeia uma chave de locatário para o fragmento (banco de dados) no qual são armazenados o dado do locatário.  As funções EDCL gerenciam um *mapa de fragmentos global* armazenado em tabelas no banco de dados do *Catálogo de locatários* e um *mapa de fragmentos local* armazenado em cada fragmento.

As funções EDCL podem ser chamadas de aplicativos ou scripts do PowerShell para criar e gerenciar as entradas no mapa de fragmentos. Outras funções EDCL podem ser usadas para recuperar o conjunto de fragmentos ou conectar-se ao banco de dados correto para determinada chave de locatário. 

> [!IMPORTANT]
> Não edite os dados no banco de dados de catálogo ou no mapa de fragmentos local nos bancos de dados de locatário diretamente. Não há suporte para atualizações diretas devido ao alto risco de corrupção de dados. Em vez disso, edite os dados de mapeamento usando apenas APIs EDCL.

## <a name="tenant-provisioning"></a>Provisionamento de locatário 

Cada locatário requer um novo grupo de recursos do Azure, que deve ser criado antes que os recursos possam ser provisionados dentro dele. Depois que o grupo de recursos existir, um modelo de gerenciamento de recursos do Azure poderá ser usado para implantar os componentes do aplicativo e o banco de dados e, em seguida, configurar a conexão do banco de dados. Para inicializar o esquema de banco de dados, o modelo pode importar um arquivo bacpac.  Como alternativa, o banco de dados pode ser criado como uma cópia de um banco de dados de "modelo".  O banco de dados é então atualizado mais detalhadamente com o local inicial e registrado no catálogo.

## <a name="tutorial"></a>Tutorial

Neste tutorial, ficará a saber como:

* Provisionar um catálogo
* Registrar os bancos de dados de locatário de exemplo que você implantou anteriormente no catálogo
* Provisionar um locatário adicional e registrá-lo no catálogo

Um modelo de Azure Resource Manager é usado para implantar e configurar o aplicativo, criar o banco de dados de locatário e, em seguida, importar um arquivo bacpac para inicializá-lo. A solicitação de importação pode ser enfileirada por vários minutos antes de ser executada.

No final deste tutorial, você tem um conjunto de aplicativos de locatário autônomos, com cada banco de dados registrado no catálogo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes: 

* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Os três aplicativos de locatário de exemplo são implantados. Para implantar esses aplicativos em menos de cinco minutos, consulte [implantar e explorar o padrão de aplicativo do Wingtip tickets SaaS autônomo](saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>Provisionar o catálogo

Nesta tarefa, você aprenderá a provisionar o catálogo usado para registrar todos os bancos de dados de locatário. Irá: 

* **Provisione o banco de dados de catálogo** usando um modelo de gerenciamento de recursos do Azure. O banco de dados é inicializado importando um arquivo bacpac.  
* **Registre os aplicativos de locatário de exemplo** que você implantou anteriormente.  Cada locatário é registrado usando uma chave construída com base em um hash do nome do locatário.  O nome do locatário também é armazenado em uma tabela de extensão no catálogo.

1. No ISE do PowerShell, abra *. ..\Learning Modules\UserConfig.PSM* e atualize a **\<usuário\>** valor para o valor usado ao implantar os três aplicativos de exemplo.  **Salve o arquivo**.  
1. No ISE do PowerShell, abra *. ..\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* e defina **$Scenario = 1**. Implante o catálogo de locatários e registre os locatários predefinidos.

1. Adicione um ponto de interrupção colocando o cursor em qualquer lugar na linha que diz, `& $PSScriptRoot\New-Catalog.ps1`e pressione **F9**.

    ![definindo um ponto de interrupção para rastreamento](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Execute o script pressionando **F5**. 
1.  Após a execução do script parar no ponto de interrupção, pressione **F11** para entrar no script New-Catalog. ps1.
1.  Rastreie a execução do script usando as opções do menu Depurar, F10 e F11, para depurar ou entrar em funções chamadas.
    *   Para obter mais informações sobre como depurar scripts do PowerShell, consulte [dicas sobre como trabalhar com e depurar scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

Quando o script for concluído, o catálogo existirá e todos os locatários de exemplo serão registrados. 

Agora, examine os recursos que você criou.

1. Abra o [portal do Azure](https://portal.azure.com/) e procure os grupos de recursos.  Abra o grupo de recursos do **usuário do Wingtip-SA-Catalog-\<\>** e anote o servidor de catálogo e o banco de dados.
1. Abra o banco de dados no portal e selecione *Data Explorer* no menu à esquerda.  Clique no comando de logon e digite a senha = **P\@ssword1**.


1. Explore o esquema do banco de dados *tenantcatalog* .  
   * Os objetos no esquema de `__ShardManagement` são todos fornecidos pela biblioteca de cliente do banco de dados elástico.
   * A tabela de `Tenants` e `TenantsExtended` exibição são extensões adicionadas no exemplo que demonstram como você pode estender o catálogo para fornecer valor adicional.
1. Execute a consulta `SELECT * FROM dbo.TenantsExtended`.          

   ![Data Explorer](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Como alternativa ao uso do Data Explorer você pode se conectar ao banco de dados do SQL Server Management Studio. Para fazer isso, conecte-se ao servidor Wingtip- 

    
    Observe que você não deve editar dados diretamente no catálogo – sempre use as APIs de gerenciamento de fragmentos. 

## <a name="provision-a-new-tenant-application"></a>Provisionar um novo aplicativo de locatário

Nesta tarefa, você aprenderá a provisionar um aplicativo de locatário único. Irá:  

* **Crie um novo grupo de recursos** para o locatário. 
* **Provisione o aplicativo e o banco de dados** no novo grupo de recursos usando um modelo de gerenciamento de recursos do Azure.  Essa ação inclui a inicialização do banco de dados com o esquema comum e o dado de referência importando um arquivo bacpac. 
* **Inicialize o banco de dados com informações básicas do locatário**. Essa ação inclui especificar o tipo de local, que determina a fotografia usada como plano de fundo em seu site de eventos. 
* **Registre o banco de dados no banco de dados do catálogo**. 

1. No ISE do PowerShell, abra *. ..\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* e defina **$Scenario = 2**. Implantar o catálogo de locatários e registrar os locatários predefinidos

1. Adicione um ponto de interrupção no script, colocando o cursor em qualquer lugar na linha 49 que diz, `& $PSScriptRoot\New-TenantApp.ps1`e pressione **F9**.
1. Execute o script pressionando **F5**. 
1.  Após a execução do script parar no ponto de interrupção, pressione **F11** para entrar no script New-Catalog. ps1.
1.  Rastreie a execução do script usando as opções do menu Depurar, F10 e F11, para depurar ou entrar em funções chamadas.

Depois que o locatário tiver sido provisionado, o site de eventos do novo locatário será aberto.

   ![corrida de bordo vermelha](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Em seguida, você pode inspecionar os novos recursos criados no portal do Azure. 

   ![recursos de corrida de bordo vermelha](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Para interromper a cobrança, excluir grupos de recursos

Quando terminar de explorar o exemplo, exclua todos os grupos de recursos que você criou para interromper a cobrança associada.

## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre os aplicativos de banco de dados SaaS multilocatário, consulte [padrões de design para aplicativos SaaS multilocatários](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> * Como implantar o aplicativo autônomo SaaS Wingtip tickets.
> * Sobre os servidores e bancos de dados que compõem o aplicativo.
> * Como excluir recursos de exemplo para interromper a cobrança relacionada.

Você pode explorar como o catálogo é usado para dar suporte a vários cenários de locatário cruzado usando a versão de banco de dados por locatário do [aplicativo SaaS Wingtip tickets](saas-dbpertenant-wingtip-app-overview.md).  
