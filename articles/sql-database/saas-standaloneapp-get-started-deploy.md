---
title: Tutorial de SaaS de locatário único
description: Implante e explore um aplicativo SaaS autônomo de locatário único, que usa o banco de dados SQL do Azure.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822123"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Implantar e explorar um aplicativo autônomo de locatário único que usa o banco de dados SQL do Azure

Neste tutorial, você implanta e explora o aplicativo de exemplo do Wingtip tickets SaaS desenvolvido usando o aplicativo autônomo ou o padrão de aplicativo por locatário.  O aplicativo foi projetado para demonstrar recursos do banco de dados SQL do Azure que simplificam a habilitação de cenários de SaaS multilocatário.

O aplicativo autônomo ou o padrão de aplicativo por locatário implanta uma instância de aplicativo para cada locatário.  Cada aplicativo é configurado para um locatário específico e implantado em um grupo de recursos do Azure separado. Várias instâncias do aplicativo são provisionadas para fornecer uma solução multilocatário. Esse padrão é mais adequado para números menores, de locatários em que o isolamento de locatário é uma prioridade mais alta. O Azure tem programas de parceiro que permitem que os recursos sejam implantados na assinatura de um locatário e gerenciados por um provedor de serviços em nome do locatário. 

Neste tutorial, você implantará três aplicativos autônomos para três locatários em sua assinatura do Azure.  Você tem acesso completo para explorar e trabalhar com os componentes individuais do aplicativo.

O código-fonte do aplicativo e os scripts de gerenciamento estão disponíveis no repositório GitHub [repositório wingtipticketssaas-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) . O aplicativo foi criado usando o Visual Studio 2015 e não é aberto e compilado com êxito no Visual Studio 2019 sem atualização.


Neste tutorial, ficará a saber:

> [!div class="checklist"]
> * Como implantar o aplicativo autônomo SaaS Wingtip tickets.
> * Onde obter o código-fonte do aplicativo e os scripts de gerenciamento.
> * Sobre os servidores e bancos de dados que compõem o aplicativo.

TUTORIAIS adicionais serão lançados. Eles permitirão que você explore uma variedade de cenários de gerenciamento com base nesse padrão de aplicativo.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Implantar o aplicativo autônomo SaaS Wingtip tickets

Implante o aplicativo para os três locatários fornecidos:

1. Clique em cada botão azul **implantar no Azure** para abrir o modelo de implantação no [portal do Azure](https://portal.azure.com). Cada modelo requer dois valores de parâmetro; um nome para um novo grupo de recursos e um nome de usuário que distingue essa implantação de outras implantações do aplicativo. A próxima etapa fornece detalhes para definir esses valores.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; da **contoso Concert Hall**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **dojo dogwood**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. Insira os valores de parâmetro necessários para cada implantação.

    > [!IMPORTANT]
    > Alguns firewalls de autenticação e de servidor são intencionalmente desprotegidos para fins de demonstração. **Crie um novo grupo de recursos** para cada implantação de aplicativo.  Não use um grupo de recursos existente. Não use este aplicativo ou todos os recursos que ele cria, para produção. Exclua todos os grupos de recursos quando terminar de fazer com que os aplicativos interrompam a cobrança relacionada.

    É melhor usar apenas letras minúsculas, números e hifens em seus nomes de recursos.
    * Para **grupo de recursos**, selecione criar novo e forneça um nome em minúsculas para o grupo de recursos. **Wingtip-SA-\<foroname\>-\<\>de usuário** é o padrão recomendado.  Para \<\>LocalName, substitua o nome do local sem espaços. Para \<\>de usuário, substitua o valor do usuário abaixo.  Com esse padrão, os nomes de grupo de recursos podem ser *Wingtip-SA-contosoconcerthall-AF1*, *Wingtip-SA-dogwooddojo-AF1*, *Wingtip-SA-fabrikamjazzclub-AF1*.
    * Selecione um **local** na lista suspensa.

    * Para o **usuário** -recomendamos um valor curto de usuário, como suas iniciais mais um dígito: por exemplo, *AF1*.


3. **Implemente a aplicação**.

    * Clique para concordar com os termos e condições.
    * Clique em **Comprar**.

4. Monitore o status de todas as três implantações clicando em **notificações** (o ícone de sino à direita da caixa de pesquisa). A implantação dos aplicativos leva cerca de cinco minutos.


## <a name="run-the-applications"></a>Executar as aplicações

O aplicativo apresenta locais que hospedam eventos.  Os locais são os locatários do aplicativo. Cada local Obtém um site personalizado para listar seus eventos e vender ingressos. Os tipos de local incluem corredores de concerto, clubes de jazz e clubes esportivos. No exemplo, o tipo de local determina a fotografia de plano de fundo mostrada no site do local.   No modelo de aplicativo autônomo, cada local tem uma instância de aplicativo separada com seu próprio banco de dados SQL autônomo.

1. Abra a página de eventos para cada um dos três locatários em guias separadas do navegador:

   - http://events.contosoconcerthall.&lt; usuário&gt;. trafficmanager.net
   - http://events.dogwooddojo.&lt; usuário&gt;. trafficmanager.net
   - http://events.fabrikamjazzclub.&lt; usuário&gt;. trafficmanager.net

     (Em cada URL, substitua &lt;&gt; de usuário pelo valor de usuário da implantação.)

   ![Eventos](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Para controlar a distribuição de solicitações de entrada, o aplicativo usa o [*Gerenciador de tráfego do Azure*](../traffic-manager/traffic-manager-overview.md). Cada instância de aplicativo específica do locatário inclui o nome do locatário como parte do nome de domínio na URL. Todas as URLs de locatário incluem seu valor de **usuário** específico. As URLs seguem o seguinte formato:
- http://events.&lt; LocalName&gt;.&lt;usuário&gt;. trafficmanager.net

O **local** do banco de dados de cada locatário é incluído nas configurações de aplicativo do aplicativo implantado correspondente.

Em um ambiente de produção, normalmente você cria um registro DNS CNAME para [*apontar um domínio de Internet da empresa*](../traffic-manager/traffic-manager-point-internet-domain.md) para a URL do perfil do Gerenciador de tráfego.


## <a name="explore-the-servers-and-tenant-databases"></a>Explorar os servidores e bancos de dados de locatário

Vamos examinar alguns dos recursos que foram implantados:

1. Na [portal do Azure](https://portal.azure.com), navegue até a lista de grupos de recursos.
2. Você deve ver os três grupos de recursos de locatário.
3. Abra o grupo de recursos do **usuário Wingtip-SA-Fabrikam-&lt;&gt;** , que contém os recursos para a implantação do Fabrikam Jazz Club.  O servidor de **&gt;de usuário fabrikamjazzclub&lt;** contém o banco de dados **fabrikamjazzclub** .

Cada banco de dados de locatário é um banco de dados *autônomo* de DTU 50.

## <a name="additional-resources"></a>Recursos adicionais

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](elastic-jobs-overview.md)
-->

- Para saber mais sobre aplicativos SaaS multilocatários, consulte [padrões de design para aplicativos SaaS multilocatários](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Excluir grupos de recursos para interromper a cobrança ##

Quando terminar de usar o exemplo, exclua todos os grupos de recursos que você criou para interromper a cobrança associada.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> * Como implantar o aplicativo autônomo SaaS Wingtip tickets.
> * Sobre os servidores e bancos de dados que compõem o aplicativo.
> * Como excluir recursos de exemplo para interromper a cobrança relacionada.

Em seguida, experimente o tutorial de [provisionamento e catálogo](saas-standaloneapp-provision-and-catalog.md) no qual você explorará o uso de um catálogo de locatários que permite uma variedade de cenários entre locatários, como gerenciamento de esquema e análise de locatário.
 

