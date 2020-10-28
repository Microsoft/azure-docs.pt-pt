---
title: Tutorial saas de inquilino único
description: Implemente e explore uma aplicação SaaS de inquilino único autónoma, que utiliza a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/07/2018
ms.openlocfilehash: 896f4edb1e94c1eca06b046382727a5042375e3b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793284"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Implementar e explorar uma aplicação autónoma de inquilino único que utiliza a Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você implementa e explora a aplicação de amostras Wingtip Tickets SaaS desenvolvida usando a aplicação autónoma, ou app-per-inquilino, padrão.  A aplicação destina-se a mostrar características da Base de Dados Azure SQL que simplificam cenários SaaS multi-inquilinos.

A aplicação autónoma ou o padrão app-por-inquilino implementa uma instância de aplicação para cada inquilino.  Cada aplicação é configurada para um inquilino específico e implantada num grupo de recursos Azure separado. Várias instâncias do pedido são previstas para fornecer uma solução multi-inquilino. Este padrão é mais adequado para números menores, de inquilinos onde o isolamento dos inquilinos é uma prioridade máxima. A Azure tem programas parceiros que permitem a implantação de recursos na subscrição de um inquilino e geridos por um prestador de serviços em nome do arrendatário. 

Neste tutorial, você colocará três aplicações autónomas para três inquilinos na sua assinatura Azure.  Tem acesso total para explorar e trabalhar com os componentes de aplicação individuais.

O código fonte de aplicação e os scripts de gestão estão disponíveis no [WingtipTicketsSSa-StandaloneApp GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) repo. A aplicação foi criada com o Visual Studio 2015 e não abre e compila com sucesso no Visual Studio 2019 sem atualizar.


Neste tutorial, ficará a saber:

> [!div class="checklist"]
> * Como implementar a Aplicação Autónoma de Bilhetes Wingtip SaaS.
> * Onde obter o código fonte de aplicação e scripts de gestão.
> * Sobre os servidores e bases de dados que compõem a aplicação.

Serão lançados tutoriais adicionais. Eles permitir-lhe-ão explorar uma série de cenários de gestão com base neste padrão de aplicação.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Implementar a aplicação de ingressos saas de ponta saas

Implementar a aplicação para os três inquilinos fornecidos:

1. Clique em cada botão **Azul Implementar para Azul** para abrir o modelo de implementação no portal [Azure](https://portal.azure.com). Cada modelo requer dois valores de parâmetro; um nome para um novo grupo de recursos, e um nome de utilizador que distingue esta implementação de outras implementações da app. O passo seguinte fornece detalhes para a definição destes valores.

   **Sala de Concertos Contoso**   
   [![Imagem mostrando um botão marcado "Implementar para Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-contoso)

   **Dogwood Dojo**   
   [![Imagem mostrando um botão marcado "Implementar para Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-dogwood)

   **Clube de Jazz de Fabrikam**   
   [![Imagem mostrando um botão marcado "Implementar para Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-fabrikam)
 
2. Introduza os valores dos parâmetros necessários para cada implantação.

    > [!IMPORTANT]
    > Algumas firewalls de autenticação e servidores estão intencionalmente despromocuradas para fins de demonstração. **Criar um novo grupo de recursos** para cada implementação de aplicação.  Não utilize um grupo de recursos existente. Não utilize esta aplicação, nem quaisquer recursos que crie, para produção. Elimine todos os grupos de recursos quando terminar com as aplicações para parar a faturação relacionada.

    É melhor usar apenas letras minúsculas, números e hífens nos nomes dos seus recursos.
    * Para **o grupo de recursos,** selecione Criar novo e, em seguida, fornecer um nome minúsculo para o grupo de recursos. **wingtip-sa- \<venueName\> - \<user\>** é o padrão recomendado.  Para \<venueName\> , substituir o nome do local sem espaços. Para \<user\> , substituir o valor do utilizador a partir de baixo.  Com este padrão, os nomes de grupos de recursos podem ser *wingtip-sa-contosoconcerthall-af1* , *wingtip-sa-dogwooddojo-af1,* *wingtip-sa-fabrikamjazzclub-af1* .
    * Selecione uma **localização** da lista de drop-down.

    * Para **o Utilizador** - Recomendamos um curto valor de utilizador, como as suas iniciais mais um dígito: por exemplo, *af1* .


3. **Implementar a aplicação** .

    * Clique para concordar com os termos e condições.
    * Clique em **Comprar** .

4. Monitorize o estado das três implementações clicando em **Notificações** (o ícone da campainha à direita da caixa de pesquisa). A implementação das aplicações demora cerca de cinco minutos.


## <a name="run-the-applications"></a>Executar as aplicações

A aplicação apresenta locais que acolhem eventos.  Os locais são os inquilinos da candidatura. Cada local recebe um site personalizado para listar os seus eventos e vender bilhetes. Os locais incluem salas de concertos, clubes de jazz e clubes de esportes. Na amostra, o tipo de local determina a fotografia de fundo mostrada no site do local.   No modelo de aplicações autónomas, cada local tem uma instância de aplicação separada com a sua própria Base de Dados Azure SQL autónoma.

1. Abra a página de eventos para cada um dos três inquilinos em separados separados separados do navegador:

   - http://events.contosoconcerthall.&lt;user &gt; .trafficmanager.net
   - http://events.dogwooddojo.&lt;user &gt; .trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;user &gt; .trafficmanager.net

     (Em cada URL, substitua &lt; o utilizador pelo valor de utilizador da sua &gt; implantação.)

   ![Eventos](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Para controlar a distribuição de pedidos de entrada, a aplicação utiliza [*o Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md). Cada instância de aplicação específica para o inquilino inclui o nome do inquilino como parte do nome de domínio no URL. Todos os URLs do inquilino incluem o seu valor específico **do Utilizador.** Os URLs seguem o seguinte formato:
- http://events.&lt;nome local &gt; . &lt; utilizador &gt; .trafficmanager.net

A base de dados de cada inquilino **A localização** está incluída nas definições de aplicações da aplicação correspondente.

Num ambiente de produção, normalmente cria-se um registo DE DNS CNAME para [*apontar um domínio*](../../traffic-manager/traffic-manager-point-internet-domain.md) de internet da empresa para o URL do perfil do gestor de tráfego.


## <a name="explore-the-servers-and-tenant-databases"></a>Explore os servidores e bases de dados de inquilinos

Vejamos alguns dos recursos que foram mobilizados:

1. No [portal Azure,](https://portal.azure.com)consulte a lista de grupos de recursos.
2. Devia ver os três grupos de inquilinos.
3. Abra o grupo de recursos de **&lt; &gt; utilizador wingtip-sa-fabrikam,** que contém os recursos para a implantação do Fabrikam Jazz Club.  O **servidor de &lt; &gt; utilizador fabrikamjazzclub** contém a base de **dados fabrikamjazzclub.**

Cada base de dados de inquilinos é uma base de dados *autónoma* de 50 DTU.

## <a name="additional-resources"></a>Recursos adicionais

<!--
* Additional [tutorials that build on the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](elastic-pool-overview.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](./elastic-jobs-overview.md)
-->

- Para saber mais sobre aplicações SaaS multi-arrendatários, consulte [padrões de design para aplicações SaaS multi-arrendatários.](saas-tenancy-app-design-patterns.md)

 
## <a name="delete-resource-groups-to-stop-billing"></a>Eliminar grupos de recursos para parar a faturação ##

Quando terminar de utilizar a amostra, elimine todos os grupos de recursos criados para parar a faturação associada.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> * Como implementar a Aplicação Autónoma de Bilhetes Wingtip SaaS.
> * Sobre os servidores e bases de dados que compõem a aplicação.
> * Como eliminar recursos de amostra para parar a faturação relacionada.

Em seguida, experimente o tutorial [de Provisão e Catálogo](saas-standaloneapp-provision-and-catalog.md) no qual irá explorar o uso de um catálogo de inquilinos que permite uma série de cenários de inquilinos transversais, como gestão de esquemas e análise de inquilinos.
