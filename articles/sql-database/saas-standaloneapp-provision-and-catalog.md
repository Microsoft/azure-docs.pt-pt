---
title: Tutorial de SaaS de multi-inquilino - base de dados SQL do Azure | Documentos da Microsoft
description: Aprovisionar e catalogar novos inquilinos utilizando o padrão de aplicação autónoma
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
ms.date: 09/24/2018
ms.openlocfilehash: 28deb9b7ba15744b9bd3d273d02db4398d2b2ef3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896947"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Aprovisionar e catalogar novos inquilinos usando o aplicativo por inquilino padrão SaaS

Este artigo aborda o aprovisionamento e catalogação de novos inquilinos com a aplicação autónoma por padrão SaaS de inquilino.
Este artigo tem duas partes principais:
* Discussão conceitual de aprovisionamento e catalogação novos inquilinos
* Um tutorial que destaca o código do PowerShell de exemplo que realiza o aprovisionamento e catalogação
    * O tutorial utiliza a aplicação de SaaS Wingtip Tickets de exemplo, adaptada para a aplicação autónoma por padrão de inquilino.

## <a name="standalone-application-per-tenant-pattern"></a>Aplicativo autônomo, por padrão de inquilino

A aplicação autónoma por padrão de inquilino é um dos vários padrões para aplicações SaaS multi-inquilino.  Neste padrão, uma aplicação autónoma é aprovisionada para cada inquilino. O aplicativo é composto por componentes de nível de aplicativo e uma base de dados SQL.  Cada aplicação de inquilino pode ser implementada numa subscrição do fornecedor.  Em alternativa, o Azure oferece um [programa de aplicativos gerenciados](https://docs.microsoft.com/azure/managed-applications/overview) em que uma aplicação pode ser implementada numa subscrição de um inquilino e gerida pelo fornecedor em nome do inquilino. 

   ![padrão de aplicação por inquilino](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Ao implantar um aplicativo para um inquilino, a aplicação e a base de dados são provisionados num novo grupo de recursos criado para o inquilino.  Utilizar grupos de recursos separado isola os recursos de aplicativos de cada inquilino e permite que estas sejam geridas de forma independente. Dentro de cada grupo de recursos, cada instância da aplicação está configurada para aceder diretamente a respetiva base de dados correspondente.  Esse modelo de ligação contrasta com outros padrões que usam um catálogo para o Mediador de ligações entre a aplicação e a base de dados.  E porque não existe nenhuma partilha de recursos, cada base de dados de inquilinos têm de ser aprovisionado com recursos suficientes para processar o pico de carga. Este padrão tende a ser utilizado para aplicações SaaS com menos de inquilinos, onde há uma grande ênfase no inquilino, isolamento e menos ênfase nos custos de recursos.  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Utilizar um catálogo de inquilino com a aplicação por padrão de inquilino

Enquanto a aplicação e a base de dados de cada inquilino são totalmente isoladas, vários gestão e cenários de análise podem ser utilizada em inquilinos.  Por exemplo, aplicar uma alteração de esquema para uma nova versão do aplicativo requer alterações ao esquema de cada base de dados do inquilino. Cenários de análise e relatórios também podem exigir acesso a todas as bases de dados inquilinas, independentemente de onde estão implementadas.

   ![padrão de aplicação por inquilino](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

O catálogo de inquilino mantém um mapeamento entre um identificador de inquilino e uma base de dados do inquilino, permitindo que um identificador que seja resolvido para um servidor e nome de base de dados.  Na aplicação Wingtip SaaS, o identificador do inquilino é calculado como um hash do nome do inquilino, embora outros esquemas podem ser usadas.  Embora os aplicativos autônomos não precisam do catálogo para gerir as ligações, o catálogo pode ser utilizado para definir o âmbito outras ações para um conjunto de bases de dados do inquilino. Por exemplo, a consulta elástica pode utilizar o catálogo para determinar o conjunto de bases de dados em que as consultas são distribuídas para os relatórios entre inquilinos.

## <a name="elastic-database-client-library"></a>Biblioteca de Clientes da Base de Dados Elástica

No aplicativo de exemplo Wingtip, o catálogo é implementado pelos recursos de gerenciamento de partições horizontais do [biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md) (EDCL).  A biblioteca permite que uma aplicação criar, gerir e utilizar um mapa de partições horizontais que é armazenado numa base de dados. O exemplo de bilhetes Wingtip, o catálogo é armazenado na *catálogo de inquilino* base de dados.  A partição horizontal mapeia uma chave de inquilino para a partição horizontal (base de dados) no qual estão armazenados dados nesse inquilino.  Gerir funções EDCL uma *mapa de partições horizontais global* armazenados em tabelas no *catálogo de inquilino* base de dados e um *mapa de partições horizontais local* armazenados em cada partição horizontal.

Funções EDCL podem ser chamadas a partir de aplicações ou scripts do PowerShell para criar e gerir as entradas no mapa de partições horizontais. Outras funções EDCL podem ser utilizadas para recuperar o conjunto de partições horizontais ou ligar à base de dados correto para determinados chave de inquilino. 

> [!IMPORTANT]
> Não edite os dados na base de dados de catálogo ou o mapa de partições horizontais local dos bancos de dados do inquilino diretamente. Atualizações diretas não são suportadas devido ao elevado risco de danos nos dados. Em vez disso, edite os dados de mapeamento através de APIs de EDCL apenas.

## <a name="tenant-provisioning"></a>Aprovisionamento do inquilino 

Cada inquilino requer um novo grupo de recursos do Azure, que tem de ser criado antes de recursos podem ser aprovisionados dentro da mesma. Assim que o grupo de recursos existe, um modelo Azure Resource Manager pode servir-se para implementar os componentes da aplicação e a base de dados e, em seguida, configure a ligação de base de dados. Para inicializar o esquema de banco de dados, o modelo pode importar um ficheiro bacpac.  Em alternativa, a base de dados pode ser criado como uma cópia das bases de dados 'template'.  A base de dados é, em seguida, atualizado com dados do local inicial ainda mais e registada no catálogo.

## <a name="tutorial"></a>Tutorial

Neste tutorial, ficará a saber como:

* Aprovisionar um catálogo
* Registe-se as bases de dados de inquilinos de exemplo que implementou anteriormente no catálogo
* Aprovisionar um inquilino adicional e registe-o no catálogo

Um modelo Azure Resource Manager é utilizado para implementar e configurar o aplicativo, criar a base de dados do inquilino e, em seguida, importar um ficheiro bacpac para inicializá-la. O pedido de importação pode ser colocado em fila durante vários minutos antes de ser actioned.

No final deste tutorial, tem um conjunto de aplicações de inquilino autónomo, com cada base de dados registada no catálogo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes: 

* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* As três aplicações de inquilino de exemplo são implementadas. Para implementar estas aplicações em menos de cinco minutos, veja [implementar e explorar o padrão de aplicativo da Wingtip Tickets SaaS autónomo](saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>Aprovisionar o catálogo

Nesta tarefa, saiba como aprovisionar o catálogo utilizado para registar todas as bases de dados de inquilino. Irá: 

* **Aprovisionar a base de dados do catálogo** através de um modelo de gestão de recursos do Azure. A base de dados é inicializado ao importar um ficheiro bacpac.  
* **Registe-se as aplicações de inquilino de exemplo** que implementou anteriormente.  Cada inquilino está registado com uma chave construída a partir de um hash do nome do inquilino.  O nome de inquilino também é armazenado numa tabela de extensão no catálogo.

1. No ISE do PowerShell, abra *...\Learning Modules\UserConfig.psm* e atualizar a **\<utilizador\>** valor para o valor que utilizou quando implementar as três aplicações de exemplo.  **Guarde o ficheiro**.  
1. No ISE do PowerShell, abra *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* e defina **$Scenario = 1**. Implementar o catálogo de inquilino e registar os inquilinos predefinidos.

1. Adicionar um ponto de interrupção ao colocar o cursor em qualquer parte na linha que diz `& $PSScriptRoot\New-Catalog.ps1`e, em seguida, prima **F9**.

    ![definir um ponto de interrupção para o rastreio](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Execute o script, premindo **F5**. 
1.  Depois de parar a execução do script no ponto de interrupção, prima **F11** para avançar para o script New-Catalog.ps1.
1.  A execução do script utilizando as opções de menu de depuração, F10 e F11, ao passo através de ou para funções chamadas de rastreio.
    *   Para obter mais informações sobre a depuração de scripts do PowerShell, consulte [sugestões sobre como trabalhar e depurar scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

Assim que o script tiver concluído, o catálogo existirá e todos os inquilinos de exemplo serão registados. 

Agora, veja os recursos que criou.

1. Abra o [portal do Azure](https://portal.azure.com/) e procurar os grupos de recursos.  Abra o **wingtip-sa-catalog -\<usuário\>**  recursos de grupo e tenha em atenção o servidor de catálogo e a base de dados.
1. Abrir a base de dados no portal e selecione *Explorador de dados* no menu esquerdo.  Clique no comando de início de sessão e, em seguida, introduza a palavra-passe = **P\@ssword1**.


1. Explorar o esquema do *tenantcatalog* base de dados.  
   * Os objetos no `__ShardManagement` esquema são fornecidos pela biblioteca de cliente de base de dados elásticas.
   * O `Tenants` tabela e `TenantsExtended` vista são extensões adicionadas no exemplo que demonstram como é possível estender o catálogo para fornecer valor adicional.
1. Execute a consulta, `SELECT * FROM dbo.TenantsExtended`.          

   ![o Data explorer](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Como alternativa ao utilizar o Data Explorer pode ligar à base de dados do SQL Server Management Studio. Para tal, ligar para o servidor wingtip- 

    
    Tenha em atenção que não deve editar dados diretamente no catálogo - sempre usar as APIs de gestão de partições horizontais. 

## <a name="provision-a-new-tenant-application"></a>Aprovisionar uma nova aplicação de inquilino

Nesta tarefa, irá aprender a aprovisionar uma aplicação de inquilino único. Irá:  

* **Criar um novo grupo de recursos** para o inquilino. 
* **Aprovisionar a aplicação e a base de dados** para o novo grupo de recursos com um modelo de gestão de recursos do Azure.  Esta ação inclui a inicializar a base de dados com esquema comum e dados de referência através da importação de um ficheiro bacpac. 
* **Inicializar a base de dados com informações de inquilino básica**. Esta ação inclui a especificar o tipo de local, que determina a fotografia utilizada como o plano de fundo em seu site de eventos. 
* **Registe-se a base de dados na base de dados de catálogo**. 

1. No ISE do PowerShell, abra *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* e defina **$Scenario = 2**. Implementar o catálogo de inquilino e registar os inquilinos predefinidos

1. Adicionar um ponto de interrupção no script ao colocar o cursor em qualquer parte na linha 49, que diz `& $PSScriptRoot\New-TenantApp.ps1`e, em seguida, prima **F9**.
1. Execute o script, premindo **F5**. 
1.  Depois de parar a execução do script no ponto de interrupção, prima **F11** para avançar para o script New-Catalog.ps1.
1.  A execução do script utilizando as opções de menu de depuração, F10 e F11, ao passo através de ou para funções chamadas de rastreio.

Depois do inquilino tenha sido provisionado, o Web site de eventos do novo inquilino é aberto.

   ![maple Red motos](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Em seguida, pode inspecionar os novos recursos que criou no portal do Azure. 

   ![recursos de motos Red maple](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Para parar a faturação, elimine os grupos de recursos

Quando tiver terminado de explorar o exemplo, elimine todos os grupos de recursos que criou para parar a faturação associada.

## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre aplicativos de banco de dados de SaaS de multi-inquilino, veja [padrões de Design para aplicações SaaS multi-inquilino](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> * Como implementar a aplicação Wingtip Tickets SaaS autónomo.
> * Sobre os servidores e bases de dados que constituem a aplicação.
> * Como eliminar recursos de exemplo para parar a faturação relacionada.

Pode explorar como o catálogo é usado para oferecer suporte a vários cenários entre inquilinos a utilizar a versão da base de dados por inquilino dos [aplicação Wingtip Tickets SaaS](saas-dbpertenant-wingtip-app-overview.md).  
