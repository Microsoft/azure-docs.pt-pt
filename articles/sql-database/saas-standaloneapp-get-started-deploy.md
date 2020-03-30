---
title: Tutorial SaaS de inquilino único
description: Implemente e explore uma aplicação autónoma de inquilino único SaaS, que utiliza a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 11/07/2018
ms.openlocfilehash: e3afc8aa58551b995070ffaca978c8e7c8454da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822123"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Implementar e explorar uma aplicação autónoma de inquilino único que utiliza a Base de Dados Azure SQL

Neste tutorial, você implanta e explora a aplicação de amostra SaaS de Bilhetes Wingtip desenvolvida usando a aplicação autónoma, ou app-per-tenant, padrão.  A aplicação foi concebida para apresentar funcionalidades da Base de Dados Azure SQL que simplificam a ativação de cenários SaaS multi-inquilinos.

A aplicação autónoma ou padrão app-per-tenant implementa uma instância de aplicação para cada inquilino.  Cada aplicação é configurada para um inquilino específico e implantada num grupo de recursos Azure separado. Várias instâncias do pedido são previstas para fornecer uma solução multi-inquilino. Este padrão é mais adequado para números menores, de inquilinos onde o isolamento dos inquilinos é uma prioridade máxima. A Azure tem programas parceiros que permitem que os recursos sejam implantados na subscrição de um inquilino e geridos por um prestador de serviços em nome do inquilino. 

Neste tutorial, irá implementar três aplicações autónomas para três inquilinos na sua subscrição Azure.  Tem acesso total para explorar e trabalhar com os componentes de aplicação individuais.

O código fonte da aplicação e scripts de gestão estão disponíveis no [repo WingtipTicketsSaa-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub. A aplicação foi criada com recurso ao Visual Studio 2015 e não consegue abrir e compilar com sucesso no Visual Studio 2019 sem atualizar.


Neste tutorial, ficará a saber:

> [!div class="checklist"]
> * Como implementar a aplicação autónoma de bilhetes SaaS.
> * Onde obter o código fonte da aplicação e scripts de gestão.
> * Sobre os servidores e bases de dados que compõem a aplicação.

Serão divulgados tutoriais adicionais. Eles vão permitir-lhe explorar uma série de cenários de gestão com base neste padrão de aplicação.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Implementar a aplicação autónoma de bilhetes SaaS

Implementar a aplicação para os três inquilinos fornecidos:

1. Clique em cada botão azul **Desdobrar para o** botão Azure para abrir o modelo de implementação no [portal Azure](https://portal.azure.com). Cada modelo requer dois valores de parâmetro; um nome para um novo grupo de recursos, e um nome de utilizador que distingue esta implementação de outras implementações da app. O próximo passo fornece detalhes para a definição destes valores.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Sala de Concertos Contoso**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Dogwood Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Fabrikam Jazz Club**

2. Introduza os valores de parâmetro necessários para cada implantação.

    > [!IMPORTANT]
    > Algumas firewalls de autenticação e servidor não estão intencionalmente seguras para fins de demonstração. **Crie um novo grupo de recursos** para cada implementação de aplicações.  Não utilize um grupo de recursos existente. Não utilize esta aplicação, nem quaisquer recursos que crie, para produção. Elimine todos os grupos de recursos quando terminar com as aplicações para parar a faturação relacionada.

    É melhor usar apenas letras minúsculas, números e hífenes nos vossos nomes de recursos.
    * Para **o grupo Recursos,** selecione Criar novo e, em seguida, fornecer um nome minúsculo para o grupo de recursos. **\<wingtip-sa-venueName\>-\<\> user** é o padrão recomendado.  Para \<localName,\>substitua o nome do local sem espaços. Para \<\>o utilizador, substitua o valor do utilizador a partir de baixo.  Com este padrão, os nomes do grupo de recursos podem ser *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Selecione um **Local** a partir da lista de lançamentos.

    * Para **utilizador** - Recomendamos um curto valor de utilizador, como as suas iniciais mais um dígito: por exemplo, *af1*.


3. **Implemente a aplicação**.

    * Clique para concordar com os termos e condições.
    * Clique em **Comprar**.

4. Monitorize o estado das três implementações clicando em **Notificações** (o ícone do sino à direita da caixa de pesquisa). A implementação das aplicações demora cerca de cinco minutos.


## <a name="run-the-applications"></a>Executar as aplicações

A aplicação apresenta locais que acolhem eventos.  Os locais são os inquilinos da candidatura. Cada local recebe um site personalizado para listar os seus eventos e vender bilhetes. Os tipos de locais incluem salas de concertos, clubes de jazz e clubes desportivos. Na amostra, o tipo de local determina a fotografia de fundo mostrada no site do local do local.   No modelo de aplicação autónoma, cada local tem uma instância de aplicação separada com a sua própria base de dados SQL autónoma.

1. Abra a página de eventos para cada um dos três inquilinos em separados separados:

   - http://events.contosoconcerthall.&lt;utilizador&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;utilizador&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;utilizador&gt;.trafficmanager.net

     (Em cada URL, substitua &lt;o utilizador&gt; pelo valor de utilizador da sua implementação.)

   ![Eventos](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Para controlar a distribuição de pedidos de entrada, a aplicação utiliza o [*Azure Traffic Manager.*](../traffic-manager/traffic-manager-overview.md) Cada instância de aplicação específica do inquilino inclui o nome do inquilino como parte do nome de domínio no URL. Todos os URLs de inquilino incluem o seu valor de **utilizador** específico. Os URLs seguem o seguinte formato:
- http://events.&lt;nome&gt;do local. &lt;utilizador&gt;.trafficmanager.net

Base de dados de cada inquilino **A localização** está incluída nas definições da aplicação da aplicação implantada correspondente.

Num ambiente de produção, normalmente cria-se um registo CNAME DNS para [*apontar um domínio*](../traffic-manager/traffic-manager-point-internet-domain.md) de internet da empresa para o URL do perfil do gestor de tráfego.


## <a name="explore-the-servers-and-tenant-databases"></a>Explore os servidores e bases de dados dos inquilinos

Vamos ver alguns dos recursos que foram implantados:

1. No [portal Azure,](https://portal.azure.com)navegue pela lista de grupos de recursos.
2. Devia ver os três grupos de recursos dos inquilinos.
3. Abra o grupo de recursos de **&lt;utilizador&gt; da asa-sa-fabrikam,** que contém os recursos para a implantação do Fabrikam Jazz Club.  O **&lt;fabrikamjazzclub-&gt; ** servidor de utilizadores contém a base de dados **do Fabrikamjazzclub.**

Cada base de dados de inquilinos é uma base de dados *autónoma* de 50 DTU.

## <a name="additional-resources"></a>Recursos adicionais

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](elastic-jobs-overview.md)
-->

- Para conhecer aplicações saaS multi-inquilinas, consulte padrões de [design para aplicações SaaS multi-inquilinos.](saas-tenancy-app-design-patterns.md)

 
## <a name="delete-resource-groups-to-stop-billing"></a>Eliminar grupos de recursos para parar a faturação ##

Quando terminar de utilizar a amostra, elimine todos os grupos de recursos que criou para parar a faturação associada.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> * Como implementar a aplicação autónoma de bilhetes SaaS.
> * Sobre os servidores e bases de dados que compõem a aplicação.
> * Como eliminar os recursos da amostra para parar a faturação relacionada.

Em seguida, experimente o tutorial [de Provision and Catalog](saas-standaloneapp-provision-and-catalog.md) no qual explorará o uso de um catálogo de inquilinos que permite uma série de cenários de inquilinos transversais, como gestão de esquemas e análise de inquilinos.
 

