---
title: Tutorial Multi-inquilino SaaS
description: Provisão e catalogar novos inquilinos usando o padrão de aplicação autónoma
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
ms.openlocfilehash: 02682a18f14e7ecbf5b42783ab84a1b55a4bb77b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133126"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Provisão e catalogar novos inquilinos utilizando a aplicação por padrão SaaS inquilino

Este artigo abrange o fornecimento e catalogação de novos inquilinos usando a app autónoma por padrão SaaS inquilino.
Este artigo tem duas partes principais:
* Discussão conceptual de provisionamento e catalogação de novos inquilinos
* Um tutorial que destaca a amostra de código PowerShell que realiza o fornecimento e catalogação
    * O tutorial utiliza a aplicação SaaS da amostra de Bilhetes Wingtip, adaptada à aplicação autónoma por padrão de inquilino.

## <a name="standalone-application-per-tenant-pattern"></a>Aplicação autónoma por padrão de inquilino

A aplicação autónoma por padrão de inquilino é um dos vários padrões para aplicações SaaS multi-inquilinos.  Neste padrão, uma aplicação autónoma é disponibilizada para cada inquilino. A aplicação compreende componentes de nível de aplicação e uma base de dados SQL.  Cada aplicação de inquilino pode ser implementada na subscrição do fornecedor.  Em alternativa, o Azure oferece um programa de [aplicações geridos](https://docs.microsoft.com/azure/managed-applications/overview) no qual uma aplicação pode ser implementada na subscrição de um inquilino e gerida pelo fornecedor em nome do inquilino.

   ![padrão app-por-inquilino](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Ao implementar um pedido para um inquilino, a app e a base de dados são aprovisionadas num novo grupo de recursos criado para o inquilino.  A utilização de grupos de recursos separados isola os recursos de cada inquilino e permite que sejam geridos de forma independente. Dentro de cada grupo de recursos, cada instância de aplicação está configurada para aceder diretamente à sua base de dados correspondente.  Este modelo de ligação contrasta com outros padrões que utilizam um catálogo para intermediar ligações entre a app e a base de dados.  E como não há partilha de recursos, cada base de dados de inquilinos deve ser aprovisionada com recursos suficientes para lidar com a sua carga máxima. Este padrão tende a ser usado para aplicações SaaS com menos inquilinos, onde há uma forte ênfase no isolamento dos inquilinos e menos ênfase nos custos de recursos.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Usando um catálogo de inquilinos com a aplicação por padrão de inquilino

Embora a aplicação e a base de dados de cada inquilino estejam totalmente isoladas, vários cenários de gestão e análise podem funcionar entre os inquilinos.  Por exemplo, a aplicação de uma alteração de esquema para uma nova versão do pedido requer alterações ao esquema de cada base de dados de inquilinos. Os cenários de reporte e análise também podem exigir acesso a todas as bases de dados dos inquilinos, independentemente do local onde são implantados.

   ![padrão app-por-inquilino](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

O catálogo de inquilinos detém um mapeamento entre um identificador de inquilinos e uma base de dados de inquilinos, permitindo que um identificador seja resolvido para um servidor e nome de base de dados.  Na app Wingtip SaaS, o identificador de inquilino é calculado como um hash do nome do inquilino, embora outros esquemas possam ser usados.  Embora as aplicações autónomas não precisem do catálogo para gerir ligações, o catálogo pode ser usado para definir outras ações para um conjunto de bases de dados de inquilinos. Por exemplo, a Elástico Consulta pode usar o catálogo para determinar o conjunto de bases de dados através das quais as consultas são distribuídas para reporte de inquilinos transversais.

## <a name="elastic-database-client-library"></a>Biblioteca de clientes de base de dados elástica

Na aplicação da amostra Wingtip, o catálogo é implementado pelas funcionalidades de gestão de fragmentos da Biblioteca de Clientes da Base de [Dados Elástica](sql-database-elastic-database-client-library.md) (EDCL).  A biblioteca permite que uma aplicação crie, gere e utilize um mapa de fragmentos que é armazenado numa base de dados. Na amostra de Bilhetes Wingtip, o catálogo está armazenado na base de dados do catálogo de *inquilinos.*  O fragmento mapeia uma chave de inquilino para o fragmento (base de dados) em que os dados desse inquilino são armazenados.  As funções EDCL gerem um mapa global de *fragmentos* armazenado em tabelas na base de dados do catálogo de *inquilinos* e um *mapa local* armazenado em cada fragmento.

As funções EDCL podem ser chamadas a partir de aplicações ou scripts PowerShell para criar e gerir as entradas no mapa do fragmento. Outras funções EDCL podem ser usadas para recuperar o conjunto de fragmentos ou ligar-se à base de dados correta para determinada chave de inquilino.

> [!IMPORTANT]
> Não editar os dados na base de dados do catálogo ou no mapa local nas bases de dados dos inquilinos diretamente. As atualizações diretas não são suportadas devido ao elevado risco de corrupção de dados. Em vez disso, edite os dados de mapeamento usando apenas APIs EDCL.

## <a name="tenant-provisioning"></a>Prestação de inquilinos

Cada inquilino requer um novo grupo de recursos Azure, que deve ser criado antes de os recursos poderem ser aprovisionados no seu interior. Uma vez que o grupo de recursos existe, um modelo de Gestão de Recursos Azure pode ser usado para implementar os componentes da aplicação e a base de dados, e, em seguida, configurar a ligação da base de dados. Para inicializar o esquema da base de dados, o modelo pode importar um ficheiro bacpac.  Em alternativa, a base de dados pode ser criada como cópia de uma base de dados 'modelo'.  A base de dados é então atualizada com os dados iniciais do local e registadas no catálogo.

## <a name="tutorial"></a>Tutorial

Neste tutorial, ficará a saber como:

* Fornecer um catálogo
* Registe as bases de dados dos inquilinos que implementou anteriormente no catálogo
* Provisão de um inquilino adicional e registá-lo no catálogo

Um modelo de Gestor de Recursos Azure é usado para implementar e configurar a aplicação, criar a base de dados do inquilino e, em seguida, importar um ficheiro bacpac para inicializá-lo. O pedido de importação pode ficar em fila durante vários minutos antes de ser apresentado.

No final deste tutorial, tem um conjunto de aplicações autónomas de inquilinos, com cada base de dados registada no catálogo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* As três aplicações de inquilinos de amostra estão implantadas. Para implementar estas aplicações em menos de cinco minutos, consulte Implementar e explorar o padrão de aplicação autónoma de [bilhetes SaaS](saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>Provisionar o catálogo

Nesta tarefa, aprende-se a fornecer o catálogo utilizado para registar todas as bases de dados dos inquilinos. Irá:

* **Fornecer a base de dados do catálogo** utilizando um modelo de gestão de recursos Azure. A base de dados é inicializada através da importação de um ficheiro bacpac.
* **Registe as aplicações de inquilinos** de amostra que implementou anteriormente.  Cada inquilino está registado usando uma chave construída a partir de um hash do nome do inquilino.  O nome do inquilino também está guardado numa mesa de extensão no catálogo.

1. No PowerShell ISE, abra *...\Learning Modules\UserConfig.psm* e atualize o valor do ** \<utilizador\> ** ao valor utilizado ao implementar as três aplicações de amostra.  **Guarde o ficheiro.**
1. No PowerShell ISE, abra *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* e definido **$Scenario = 1**. Implante o catálogo de inquilinos e registe os inquilinos pré-definidos.

1. Adicione um ponto de rutura colocando o cursor `& $PSScriptRoot\New-Catalog.ps1`em qualquer lugar da linha que diz, e, em seguida, pressione **F9**.

    ![definição de um ponto de rutura para rastrear](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Executar o guião premindo **F5**.
1.  Depois de a execução do guião parar no ponto de rutura, prima **F11** para entrar no script New-Catalog.ps1.
1.  Trace a execução do script usando as opções do menu Debug, F10 e F11, para passar ou entrar em funções chamadas.
    *   Para obter mais informações sobre depuração de scripts PowerShell, consulte [Dicas sobre o trabalho e depurando scripts PowerShell](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

Assim que o script estiver concluído, o catálogo existirá e todos os inquilinos da amostra serão registados.

Agora olhe para os recursos que criou.

1. Abra o [portal Azure](https://portal.azure.com/) e navegue pelos grupos de recursos.  Abra o grupo de recursos do **\<\> utilizador do catálogo wingtip-sa** e anote o servidor de catálogo e a base de dados.
1. Abra a base de dados no portal e selecione *Data explorer* a partir do menu à esquerda.  Clique no comando iniciar sessão e introduza a Palavra-passe = **P\@ssword1**.


1. Explore o esquema da base de dados do catálogo de *inquilinos.*
   * Os objetos `__ShardManagement` no esquema são todos fornecidos pela Biblioteca de Clientes da Base de Dados Elástica.
   * A `Tenants` tabela `TenantsExtended` e a vista são extensões adicionadas na amostra que demonstram como pode estender o catálogo para fornecer valor adicional.
1. Executa a `SELECT * FROM dbo.TenantsExtended`consulta, .

   ![explorador de dados](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Como alternativa à utilização do Data Explorer, pode ligar-se à base de dados do Estúdio de Gestão de Servidores SQL. Para isso, ligue-se à ponta da asa do servidor...


    Note que não deve editar dados diretamente no catálogo - utilize sempre as APIs de gestão de fragmentos.

## <a name="provision-a-new-tenant-application"></a>Provisão de um novo pedido de inquilino

Nesta tarefa, aprende-se a fornecer um único pedido de inquilino. Irá:

* **Crie um novo grupo de recursos** para o inquilino.
* **Fornecer a aplicação e a base de dados** para o novo grupo de recursos utilizando um modelo de gestão de recursos Azure.  Esta ação inclui a inicialização da base de dados com esquemas comuns e dados de referência através da importação de um ficheiro bacpac.
* **Inicializar a base de dados com informações básicas sobre o arrendatário.** Esta ação inclui especificar o tipo de local, que determina a fotografia usada como pano de fundo no seu site de eventos.
* **Registe a base de dados na base de dados do catálogo**.

1. No PowerShell ISE, abra *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* e definir **$Scenario = 2**. Implementar o catálogo de inquilinos e registar os inquilinos pré-definidos

1. Adicione um ponto de rutura no script colocando o cursor `& $PSScriptRoot\New-TenantApp.ps1`em qualquer lugar na linha 49 que diz, e depois prima **F9**.
1. Executar o guião premindo **F5**.
1.  Depois de a execução do guião parar no ponto de rutura, prima **F11** para entrar no script New-Catalog.ps1.
1.  Trace a execução do script usando as opções do menu Debug, F10 e F11, para passar ou entrar em funções chamadas.

Depois de o inquilino ter sido provisionado, o site de eventos do novo inquilino é aberto.

   ![corrida de bordo vermelho](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Em seguida, pode inspecionar os novos recursos criados no portal Azure.

   ![recursos vermelhos de corrida de bordo](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Para parar de faturar, apague os grupos de recursos

Quando terminar de explorar a amostra, elimine todos os grupos de recursos que criou para parar a faturação associada.

## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre aplicações de base de dados multi-inquilinos SaaS, consulte [padrões de design para aplicações SaaS multi-inquilinos.](saas-tenancy-app-design-patterns.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> * Como implementar a aplicação autónoma de bilhetes SaaS.
> * Sobre os servidores e bases de dados que compõem a aplicação.
> * Como eliminar os recursos da amostra para parar a faturação relacionada.

Você pode explorar como o catálogo é usado para apoiar vários cenários de cross-tenant usando a versão base de dados/inquilino da [aplicação Wingtip Tickets SaaS](saas-dbpertenant-wingtip-app-overview.md).
