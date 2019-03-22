---
title: Tutorial de SaaS de inquilino único - base de dados SQL do Azure | Documentos da Microsoft
description: Implementar e explorar uma aplicação de SaaS de inquilino único autônomo, que utiliza a base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: 4dbf53df4d3f34e80757f9575981b4b053587d97
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012661"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Implementar e explorar uma aplicação de inquilino único autónoma que utiliza a base de dados do Azure SQL

Neste tutorial, implementar e explorar a aplicação de exemplo Wingtip Tickets SaaS desenvolvida com o padrão de aplicativo autônomo ou aplicação por inquilino.  A aplicação foi concebida para demonstrar as funcionalidades da base de dados do Azure SQL que simplificam a ativar cenários de SaaS de multi-inquilino.

O aplicativo autônomo ou um padrão de aplicação por inquilino implementa uma instância de aplicação para cada inquilino.  Cada aplicativo é configurado para um inquilino específico e implementado num grupo de recursos do Azure. Várias instâncias do aplicativo são aprovisionadas para fornecer uma solução de multi-inquilino. Este padrão é mais adequado para um número menor, de onde o isolamento de inquilino é uma prioridade de inquilinos. O Azure tem programas de parceria que permitem que os recursos a serem implantados na subscrição de um inquilino e geridos por um fornecedor de serviços em nome do inquilino. 

Neste tutorial, irá implementar três aplicativos autônomos para três inquilinos na sua subscrição do Azure.  Tem acesso total para explorar e trabalhar com os componentes de aplicativos individuais.

Os scripts de código e gestão da origem de aplicação estão disponíveis no [WingtipTicketsSaaS StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) repositório do GitHub. A aplicação foi criada com o Visual Studio 2015 e não com êxito abra e compilar no Visual Studio 2017 sem atualizar.


Neste tutorial, ficará a saber:

> [!div class="checklist"]
> * Como implementar a aplicação Wingtip Tickets SaaS autónomo.
> * Onde obter o código de origem do aplicativo e os scripts de gestão.
> * Sobre os servidores e bases de dados que constituem a aplicação.

Tutoriais adicionais serão lançadas. Eles permitirá que explore uma gama de cenários de gestão com base neste padrão de aplicação.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Implementar a aplicação de SaaS autónomo de bilhetes Wingtip

Implemente a aplicação para os três inquilinos de fornecido:

1. Clique em cada azul **implementar no Azure** botão para abrir o modelo de implementação no [portal do Azure](https://portal.azure.com). Cada modelo requer dois valores de parâmetros; um nome para um novo grupo de recursos e um nome de utilizador que distingue esta implementação a partir de outras implementações da aplicação. O passo seguinte fornece detalhes para definir esses valores.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contoso Concert Hall**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Dogwood Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. Introduza os valores dos parâmetros necessários para cada implementação.

    > [!IMPORTANT]
    > Alguns firewalls de autenticação e o servidor são intencionalmente não seguras para fins de demonstração. **Criar um novo grupo de recursos** para cada implementação de aplicação.  Não utilize um grupo de recursos existente. Não utilize esta aplicação nem quaisquer recursos por que ela criados para produção. Elimine todos os grupos de recursos quando tiver terminado com os aplicativos para parar a faturação relacionada.

    É melhor usar apenas letras minúsculas, números e hífenes no seus nomes de recursos.
    * Para **grupo de recursos**, selecione Criar novo e, em seguida, forneça um nome em minúsculas para o grupo de recursos. **a Wingtip-sa -\<venueName\>-\<utilizador\>**  é o padrão recomendado.  Para \<venueName\>, substitua o nome de local, sem espaços. Para \<utilizador\>, substitua o valor de utilizador de abaixo.  Com esse padrão, os nomes de grupo de recursos podem ser *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Selecione um **localização** na lista pendente.

    * Para **usuário** -recomendamos um valor de utilizador curto, como suas iniciais e um dígito: por exemplo, *af1*.


3. **Implemente a aplicação**.

    * Clique para aceitar os termos e condições.
    * Clique em **Comprar**.

4. Monitorizar o estado de todas as implementações de três clicando **notificações** (o ícone de campainha à direita da caixa de pesquisa). Implementar as aplicações demora cerca de cinco minutos.


## <a name="run-the-applications"></a>Executar as aplicações

A aplicação apresenta os locais que alojam os eventos.  Os locais são inquilinos do aplicativo. Cada local obtém um web site personalizado para listar os seus eventos e vender bilhetes. Tipos de local incluem espetáculos, clubes de jazz e clubes desportivos. No exemplo, o tipo de local determina a fotografia de plano de fundo mostrada no site da web do local.   No modelo de aplicação autónoma, cada local tem uma instância de aplicativo separado com sua própria base de dados SQL autónoma.

1. Abra a página de eventos para cada um dos três inquilinos no separadores de browser separados:

   - http://events.contosoconcerthall.&lt;user&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;user&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;user&gt;.trafficmanager.net

     (No cada URL, substitua &lt;utilizador&gt; com valor de utilizador da sua implementação.)

   ![Eventos](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Para controlar a distribuição de pedidos recebidos, a aplicação utiliza [ *Gestor de tráfego do Azure*](../traffic-manager/traffic-manager-overview.md). Cada instância de aplicação específico de inquilino inclui o nome de inquilino como parte do nome do domínio no URL. Todo o inquilino URLs incluem sua específicas **utilizador** valor. Os URLs seguem o formato seguinte:
- http://events.&lt;venuename&gt;.&lt;user&gt;.trafficmanager.net

Base de dados de cada inquilino **localização** está incluído nas definições da aplicação da aplicação implementada correspondente.

Ambiente de produção, normalmente vai criar um registo CNAME DNS [ *apontar um domínio de internet da empresa* ](../traffic-manager/traffic-manager-point-internet-domain.md) para o URL do perfil do Gestor de tráfego.


## <a name="explore-the-servers-and-tenant-databases"></a>Explorar os servidores e bases de dados de inquilinos

Vamos examinar alguns dos recursos que foram implementados:

1. Na [portal do Azure](https://portal.azure.com), navegue para a lista de grupos de recursos.
2. Deverá ver os grupos de recursos de três inquilino.
3. Abra o **wingtip-sa-fabrikam -&lt;usuário&gt;**  grupo de recursos que contém os recursos para a implementação da Fabrikam Jazz Club.  O **fabrikamjazzclub -&lt;usuário&gt;**  servidor contém o **fabrikamjazzclub** base de dados.

Cada base de dados do inquilino é uma DTU 50 *autónomo* base de dados.

## <a name="additional-resources"></a>Recursos adicionais

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](sql-database-elastic-jobs-overview.md)
-->

- Para saber mais sobre aplicações SaaS multi-inquilino, veja [padrões de Design para aplicações SaaS multi-inquilino](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Eliminar grupos de recursos para parar a faturação ##

Quando tiver terminado com o exemplo, elimine todos os grupos de recursos que criou para parar a faturação associada.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> * Como implementar a aplicação Wingtip Tickets SaaS autónomo.
> * Sobre os servidores e bases de dados que constituem a aplicação.
> * Como eliminar recursos de exemplo para parar a faturação relacionada.

Em seguida, tente o [aprovisionar e catalogar](saas-standaloneapp-provision-and-catalog.md) tutorial que irá explorar a utilização de um catálogo de inquilinos que permite a uma variedade de cenários entre inquilinos, tais como análises de gestão e de inquilino do esquema.
 

