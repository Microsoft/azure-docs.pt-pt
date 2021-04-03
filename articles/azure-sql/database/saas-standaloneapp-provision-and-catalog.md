---
title: Tutorial saas multi-inquilino
description: Provisão e catálogo de novos inquilinos usando o padrão de aplicação autónomo
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 2343800f8801105ca75f285972b441ecb027d1a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793250"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Provisão e catálogo de novos inquilinos usando a aplicação por padrão SaaS inquilino
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo abrange o provisionamento e catalogação de novos inquilinos utilizando a app autónoma por padrão SaaS inquilino.
Este artigo tem duas partes principais:
* Discussão conceptual sobre o provisionamento e catalogação de novos inquilinos
* Um tutorial que destaca a amostra do código PowerShell que realiza o fornecimento e catalogação
    * O tutorial usa a aplicação SaaS de exemplo de Ingressos Wingtip, adaptada à app autónoma por padrão de inquilino.

## <a name="standalone-application-per-tenant-pattern"></a>Aplicação autónoma por padrão de inquilino

A app autónoma por padrão de inquilino é um dos vários padrões para aplicações SaaS multi-inquilinos.  Neste padrão, é disponibilizada uma app autónoma para cada inquilino. A aplicação compreende componentes de nível de aplicação e uma Base de Dados Azure SQL.  Cada aplicação de inquilino pode ser implantada na subscrição do fornecedor.  Em alternativa, o Azure oferece um [programa de aplicações gerido](../../azure-resource-manager/managed-applications/overview.md) no qual uma aplicação pode ser implantada na subscrição de um inquilino e gerida pelo vendedor em nome do inquilino.

   ![padrão app-por-inquilino](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Ao implementar uma aplicação para um inquilino, a app e a base de dados são aprovisionadas num novo grupo de recursos criado para o arrendatário.  A utilização de grupos de recursos separados isola os recursos de cada inquilição de cada inquilino e permite que sejam geridos de forma independente. Dentro de cada grupo de recursos, cada instância de aplicação é configurada para aceder diretamente à base de dados correspondente.  Este modelo de ligação contrasta com outros padrões que utilizam um catálogo para intermediar ligações entre a app e a base de dados.  E como não há partilha de recursos, cada base de dados de inquilinos deve ser aprovisionada com recursos suficientes para lidar com a sua carga máxima. Este padrão tende a ser utilizado para aplicações SaaS com menos inquilinos, onde há uma forte ênfase no isolamento dos inquilinos e menos ênfase nos custos de recursos.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Usando um catálogo de inquilinos com a aplicação por padrão de inquilino

Embora a app e base de dados de cada inquilino estejam totalmente isoladas, vários cenários de gestão e análise podem funcionar entre inquilinos.  Por exemplo, a aplicação de uma alteração de esquema para uma nova versão do pedido requer alterações no esquema de cada base de dados de inquilinos. Os cenários de reporte e análise podem também exigir acesso a todas as bases de dados dos inquilinos, independentemente do local onde são implantados.

   ![Diagrama que mostra como usar um catálogo de inquilinos com a aplicação por padrão de inquilino.](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

O catálogo de inquilinos contém um mapeamento entre um identificador de inquilinos e uma base de dados de inquilinos, permitindo que um identificador seja resolvido para um servidor e nome de base de dados.  Na app Wingtip SaaS, o identificador de inquilinos é calculado como um haxixe do nome do inquilino, embora outros esquemas possam ser usados.  Embora as aplicações autónomas não precisem do catálogo para gerir ligações, o catálogo pode ser usado para estender outras ações a um conjunto de bases de dados de inquilinos. Por exemplo, a Consulta Elástica pode usar o catálogo para determinar o conjunto de bases de dados através das quais as consultas são distribuídas para reporte de inquilinos cruzados.

## <a name="elastic-database-client-library"></a>Biblioteca de clientes de base de dados elástica

Na aplicação da amostra Wingtip, o catálogo é implementado pelas funcionalidades de gestão de fragmentos da Biblioteca de [Clientes de Base](elastic-database-client-library.md) de Dados Elástica (EDCL).  A biblioteca permite que uma aplicação crie, gerencie e utilize um mapa de fragmentos que esteja armazenado numa base de dados. Na amostra de Bilhetes Wingtip, o catálogo está guardado na base de dados do catálogo de *inquilinos.*  O fragmento mapeia uma chave do inquilino para o fragmento (base de dados) em que os dados do inquilino são armazenados.  As funções EDCL gerem um *mapa global de fragmentos* armazenado em tabelas na base de dados do catálogo de *inquilinos* e um *mapa de fragmentos local* armazenado em cada fragmento.

As funções EDCL podem ser chamadas a partir de aplicações ou scripts PowerShell para criar e gerir as entradas no mapa de fragmentos. Outras funções EDCL podem ser usadas para recuperar o conjunto de fragmentos ou ligar à base de dados correta para uma chave de inquilino dada.

> [!IMPORTANT]
> Não edite os dados na base de dados do catálogo ou o mapa de fragmentos local nas bases de dados dos inquilinos diretamente. As atualizações diretas não são suportadas devido ao elevado risco de corrupção de dados. Em vez disso, edite os dados de mapeamento utilizando apenas as APIs EDCL.

## <a name="tenant-provisioning"></a>Provisionamento de inquilinos

Cada inquilino requer um novo grupo de recursos Azure, que deve ser criado antes de os recursos poderem ser aprovisionados dentro dele. Uma vez que o grupo de recursos exista, um modelo de Gestão de Recursos Azure pode ser usado para implantar os componentes da aplicação e a base de dados e, em seguida, configurar a ligação de base de dados. Para inicializar o esquema da base de dados, o modelo pode importar um ficheiro bacpac.  Em alternativa, a base de dados pode ser criada como uma cópia de uma base de dados de 'modelo'.  A base de dados é então atualizada com os dados iniciais do local e registada no catálogo.

## <a name="tutorial"></a>Tutorial

Neste tutorial, ficará a saber como:

* Provisionar um catálogo
* Registe as bases de dados de inquilinos que implementou anteriormente no catálogo
* Provisionar um inquilino adicional e registá-lo no catálogo

Um modelo de Gestor de Recursos Azure é usado para implementar e configurar a aplicação, criar a base de dados do inquilino e, em seguida, importar um ficheiro bacpac para inicializá-lo. O pedido de importação pode ser feito em fila por alguns minutos antes de ser adotado.

No final deste tutorial, tem um conjunto de pedidos de inquilino autónomo, com cada base de dados registada no catálogo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps)
* As três aplicações de inquilinos estão implantadas. Para implementar estas aplicações em menos de cinco minutos, consulte [implementar e explorar o padrão de Aplicação Autónoma de Bilhetes SaaS Da Ponta da Asa.](./saas-standaloneapp-get-started-deploy.md)

## <a name="provision-the-catalog"></a>Provisionar o catálogo

Nesta tarefa, você aprende a providenciar o catálogo usado para registar todas as bases de dados de inquilinos. Irá:

* **Provisione a base de dados do catálogo** utilizando um modelo de gestão de recursos Azure. A base de dados é inicializada importando um ficheiro bacpac.
* **Registe as aplicações de inquilinos** que implementou anteriormente.  Cada inquilino é registado usando uma chave construída a partir de um haxixe do nome do inquilino.  O nome do inquilino também está guardado numa mesa de extensão do catálogo.

1. No PowerShell ISE, abra *...\Módulos de aprendizagem\UserConfig.psm* e atualize o **\<user\>** valor utilizado ao implementar as três aplicações de amostra.  **Guarde o ficheiro.**
1. No PowerShell ISE, abra *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* e definir **$Scenario = 1**. Desdobre o catálogo de inquilinos e registe os inquilinos pré-definidos.

1. Adicione um breakpoint colocando o cursor em qualquer lugar da linha que `& $PSScriptRoot\New-Catalog.ps1` diga, e, em seguida, pressione **F9**.

    ![definindo um ponto de rutura para o rastreio](./media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Executar o script pressionando **F5**.
1.  Após a execução do guião parar no ponto de rutura, prima **F11** para entrar no New-Catalog.ps1 script.
1.  Trace a execução do script usando as opções de menu Debug, F10 e F11, para passar por cima ou para funções chamadas.
    *   Para obter mais informações sobre a depuragem dos scripts PowerShell, consulte [dicas sobre trabalhar e depurar scripts PowerShell](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

Uma vez que o roteiro esteja concluído, o catálogo existirá e todos os inquilinos da amostra serão registados.

Agora olhe para os recursos que criou.

1. Abra o [portal Azure](https://portal.azure.com/) e navegue pelos grupos de recursos.  Abra o grupo de recursos **wingtip-sa-catalog \<user\>** e note o servidor e base de dados do catálogo.
1. Abra a base de *dados* no portal e selecione Data Explorer a partir do menu da esquerda.  Clique no comando Iniciar sessão e, em seguida, introduza a Palavra-passe = **\@ Ssword1**.


1. Explore o esquema da base de dados de *inquilinos.*
   * Os objetos no `__ShardManagement` esquema são todos fornecidos pela Biblioteca de Clientes de Base de Dados Elástica.
   * A `Tenants` tabela e a vista são `TenantsExtended` extensões adicionadas na amostra que demonstram como pode estender o catálogo para fornecer valor adicional.
1. Executar a consulta, `SELECT * FROM dbo.TenantsExtended` .

   ![explorador de dados](./media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Como alternativa à utilização do Data Explorer, pode ligar-se à base de dados do SQL Server Management Studio. Para isso, ligue-se à ponta da asa do servidor...


    Note que não deve editar dados diretamente no catálogo - use sempre as APIs de gestão de fragmentos.

## <a name="provision-a-new-tenant-application"></a>Disposição de um novo pedido de inquilino

Nesta tarefa, aprende-se a providenciar um único pedido de inquilino. Irá:

* **Criar um novo grupo de recursos** para o inquilino.
* **Disponibilização da aplicação e da base de dados** no novo grupo de recursos utilizando um modelo de gestão de recursos Azure.  Esta ação inclui a inicialização da base de dados com esquemas comuns e dados de referência, importando um ficheiro bacpac.
* **Inicialize a base de dados com informações básicas do inquilino.** Esta ação inclui especificar o tipo de local, que determina a fotografia usada como pano de fundo no seu site de eventos.
* **Registe a base de dados na base de dados do catálogo.**

1. No PowerShell ISE, abra *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* e definir **$Scenario = 2**. Desdobre o catálogo de inquilinos e registe os inquilinos pré-definidos

1. Adicione um ponto de rutura no script colocando o seu cursor em qualquer lugar da linha 49 que diz, e, em `& $PSScriptRoot\New-TenantApp.ps1` seguida, pressione **F9**.
1. Executar o script pressionando **F5**.
1.  Após a execução do guião parar no ponto de rutura, prima **F11** para entrar no New-Catalog.ps1 script.
1.  Trace a execução do script usando as opções de menu Debug, F10 e F11, para passar por cima ou para funções chamadas.

Depois de o arrendatário ter sido a provisionado, o site de eventos do novo inquilino é aberto.

   ![corrida de bordo vermelho](./media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Em seguida, pode inspecionar os novos recursos criados no portal Azure.

   ![recursos de corrida de bordo vermelho](./media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Para parar a faturação, elimine grupos de recursos

Quando terminar de explorar a amostra, elimine todos os grupos de recursos que criou para parar a faturação associada.

## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre aplicações de base de dados SaaS multi-arrendatários, consulte [padrões de design para aplicações SaaS multi-arrendantes.](saas-tenancy-app-design-patterns.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> * Como implementar a Aplicação Autónoma de Bilhetes Wingtip SaaS.
> * Sobre os servidores e bases de dados que compõem a aplicação.
> * Como eliminar recursos de amostra para parar a faturação relacionada.

Você pode explorar como o catálogo é usado para apoiar vários cenários de inquilinos cruzados usando a versão base de dados por inquilino da [aplicação Wingtip Tickets SaaS](./saas-dbpertenant-wingtip-app-overview.md).