---
title: Publicar aplicações em redes separadas através de grupos de conector - Azure AD
description: Abrange como criar e gerir grupos de conectores em Procuração de Aplicação AD Azure.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22fa1de0a0e3bb91480212381e07b17875bf0bf4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74275570"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicar aplicações em redes e locais separados utilizando grupos de conector

Os clientes utilizam a Procuração de Aplicação da Azure AD para mais e mais cenários e aplicações. Por isso, tornámos o App Proxy ainda mais flexível, permitindo mais topologies. Pode criar grupos de conectores Proxy de aplicação para que possa atribuir conectores específicos para servir aplicações específicas. Esta capacidade dá-lhe mais controlo e formas de otimizar a implementação do Proxy de Aplicação.

Cada conector Proxy de Aplicação é atribuído a um grupo de conector. Todos os conectores que pertencem ao mesmo grupo de conectores funcionam como uma unidade separada para alta disponibilidade e equilíbrio de carga. Todos os conectores pertencem a um grupo de conectores. Se não criar grupos, todos os seus conectores estão num grupo predefinido. A sua administração pode criar novos grupos e atribuir-lhes conectores no portal Azure.

Todas as aplicações são atribuídas a um grupo de conector. Se não criar grupos, todas as suas aplicações são atribuídas a um grupo predefinido. Mas se organizar os seus conectores em grupos, pode definir cada aplicação para trabalhar com um grupo de conectores específico. Neste caso, apenas os conectores desse grupo servem o pedido mediante pedido. Esta funcionalidade é útil se as suas aplicações estiverem alojadas em diferentes locais. Pode criar grupos de conectores com base na localização, para que as aplicações sejam sempre servidas por conectores que estejam fisicamente próximos deles.

> [!TIP]
> Se tiver uma grande implementação de Proxy de Aplicação, não atribua nenhuma aplicação ao grupo de conector predefinido. Dessa forma, os novos conectores não recebem tráfego vivo até que os atribua a um grupo de conectores ativos. Esta configuração também permite colocar conectores num modo inativo, deslocando-os de volta para o grupo predefinido, para que possa efetuar a manutenção sem afetar os utilizadores.

## <a name="prerequisites"></a>Pré-requisitos

Para agrupar os seus conectores, tem de se certificar de que [instalou vários conectores](application-proxy-add-on-premises-application.md). Quando instala um novo conector, junta-se automaticamente ao grupo de conector **Predefinido.**

## <a name="create-connector-groups"></a>Criar grupos de conector

Use estes passos para criar o número de grupos de conector que quiser.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Azure Ative Directory** > **Enterprise aplica procuração** > **Application proxy**de aplicações .
1. Selecione **Novo grupo de conector**. Aparece a lâmina do Grupo New Connector.

   ![Mostra o ecrã para selecionar um novo grupo de conector](./media/application-proxy-connector-groups/new-group.png)

1. Dê um nome ao seu novo grupo de conectores e, em seguida, use o menu dropdown para selecionar quais conectores pertencem a este grupo.
1. Selecione **Guardar**.

## <a name="assign-applications-to-your-connector-groups"></a>Atribuir aplicações aos seus grupos de conectores

Utilize estes passos para cada aplicação que publicou com a Application Proxy. Pode atribuir uma aplicação a um grupo de conector quando a publicar pela primeira vez, ou pode utilizar estes passos para alterar a atribuição sempre que quiser.

1. A partir do painel de gestão para o seu diretório, selecione **aplicações** > Enterprise**Todas as aplicações** > aplicação que pretende atribuir a um grupo de conector > **Procuração de Aplicações**.
1. Utilize o menu de dropdown do **Grupo Connector** para selecionar o grupo que pretende utilizar.
1. Selecione **Guardar** para aplicar a alteração.

## <a name="use-cases-for-connector-groups"></a>Utilize casos para grupos de conectores

Os grupos de conector são úteis para vários cenários, incluindo:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Sites com múltiplos datacenters interligados

Muitas organizações têm uma série de datacenters interligados. Neste caso, pretende manter o máximo de tráfego possível dentro do datacenter porque as ligações interpartidárias de datacenter são caras e lentas. Pode implantar conectores em cada datacenter para servir apenas as aplicações que residem dentro do datacenter. Esta abordagem minimiza as ligações inter-datacenter e proporciona uma experiência totalmente transparente aos seus utilizadores.

### <a name="applications-installed-on-isolated-networks"></a>Aplicações instaladas em redes isoladas

As aplicações podem ser alojadas em redes que não fazem parte da rede principal corporativa. Pode utilizar grupos de conectores para instalar conectores dedicados em redes isoladas para também isolar aplicações na rede. Isto geralmente acontece quando um fornecedor de terceiros mantém uma aplicação específica para a sua organização.

Os grupos de conectores permitem instalar conectores dedicados para as redes que publicam apenas aplicações específicas, tornando mais fácil e seguro subcontratar a gestão de aplicações a fornecedores de terceiros.

### <a name="applications-installed-on-iaas"></a>Aplicações instaladas no IaaS

Para aplicações instaladas no IaaS para acesso à nuvem, os grupos de conector fornecem um serviço comum para garantir o acesso a todas as aplicações. Os grupos de conector não criam dependência adicional da sua rede corporativa, nem fragmentam a experiência da aplicação. Os conectores podem ser instalados em todos os datacenters de nuvem e servir apenas aplicações que residem nessa rede. Pode instalar vários conectores para obter uma elevada disponibilidade.

Veja como exemplo uma organização que tem várias máquinas virtuais ligadas à sua própria rede virtual IaaS. Para permitir que os colaboradores utilizem estas aplicações, estas redes privadas estão ligadas à rede corporativa utilizando VPN site-to-site. Isto proporciona uma boa experiência para os colaboradores que estão localizados no local. Mas, pode não ser ideal para funcionários remotos, porque requer uma infraestrutura adicional no local para direcionar o acesso, como pode ver no diagrama abaixo:

![Diagrama que ilustra a rede Azure AD IaaS](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Com os grupos de conector De Procuração de Aplicação AD Azure, pode ativar um serviço comum para garantir o acesso a todas as aplicações sem criar dependência adicional da sua rede corporativa:

![Fornecedores de nuvem de ad iaaS azure](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Multi-floresta – diferentes grupos de conector para cada floresta

A maioria dos clientes que implementaram o Application Proxy estão a usar as suas capacidades de inscrição única (SSO) executando a Delegação Limitada kerberos (KCD). Para isso, as máquinas do conector precisam de ser unidas a um domínio que possa delegar os utilizadores na aplicação. A KCD suporta capacidades interflorestais. Mas para empresas que têm ambientes multiflorestais distintos sem confiança entre elas, um único conector não pode ser usado para todas as florestas. 

Neste caso, conectores específicos podem ser implantados por floresta, e definidos para servir aplicações que foram publicadas para servir apenas os utilizadores dessa floresta específica. Cada grupo de conector representa uma floresta diferente. Embora o inquilino e a maior parte da experiência sejam unificadas para todas as florestas, os utilizadores podem ser atribuídos às suas aplicações florestais utilizando grupos De AD Azure.

### <a name="disaster-recovery-sites"></a>Locais de recuperação de desastres

Existem duas abordagens diferentes que você pode tomar com um site de recuperação de desastres (DR), dependendo de como os seus sites são implementados:

* Se o seu site DR for construído em modo ativo-activo onde é exatamente como o site principal e tem as mesmas definições de networking e AD, pode criar os conectores no site DR no mesmo grupo de conectores que o site principal. Isto permite que a AD Azure detete falhas para si.
* Se o seu site DR estiver separado do site principal, pode criar um grupo de conector diferente no site DR, e ou 1) tiver aplicações de backup ou 2) desviar manualmente a aplicação existente para o grupo de conector DR, conforme necessário.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Servir várias empresas de um único inquilino

Existem muitas formas diferentes de implementar um modelo em que um único prestador de serviços implementa e mantém serviços relacionados com a Azure AD para várias empresas. Os grupos de conectores ajudam o administrador a segregar os conectores e aplicações em diferentes grupos. Uma das formas, que é adequada para pequenas empresas, é ter um único inquilino Da Azure, enquanto as diferentes empresas têm o seu próprio nome de domínio e redes. Isto também é verdade para cenários e situações de M&A em que uma única divisão de TI serve várias empresas por razões regulamentares ou empresariais.

## <a name="sample-configurations"></a>Configurações de amostras

Alguns exemplos que pode implementar, incluem os seguintes grupos de conector.

### <a name="default-configuration--no-use-for-connector-groups"></a>Configuração predefinida – não utilização para grupos de conectores

Se não utilizar grupos de conector, a sua configuração seria assim:

![Exemplo Azure AD No Connector Groups](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Esta configuração é suficiente para pequenas implementações e testes. Também funcionará bem se a sua organização tiver uma topologia de rede plana.

### <a name="default-configuration-and-an-isolated-network"></a>Configuração padrão e uma rede isolada

Esta configuração é uma evolução da padrão, na qual existe uma aplicação específica que funciona numa rede isolada, como a rede virtual IaaS:

![Exemplo Azure AD No Connector Groups e uma rede isolada](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Configuração recomendada - vários grupos específicos e um grupo padrão para ocioso

A configuração recomendada para organizações grandes e complexas é ter o grupo de conector padrão como um grupo que não serve nenhuma aplicação e é usado para conectores inativos ou recém-instalados. Todas as aplicações são servidas usando grupos de conector personalizados. Isto permite toda a complexidade dos cenários acima descritos.

No exemplo abaixo, a empresa tem dois datacenters, A e B, com dois conectores que servem cada site. Cada site tem aplicações diferentes que funcionam nele.

![Exemplo de empresa com 2 datacenters e 2 conectores](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Passos seguintes

* [Compreender os conectores de procuração de aplicação da AD Azure](application-proxy-connectors.md)
* [Ativar o início de sessão único](what-is-single-sign-on.md)
