---
title: Publicar aplicativos em redes separadas através de grupos de conector - Azure AD
description: Abrange como criar e gerir grupos de conectores no Azure AD Application Proxy.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: be3e99ca57957e1975313fed8609533f4a65b102
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764728"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicar aplicações em redes e locais separados utilizando grupos de conector

Os clientes utilizam o Proxy de Aplicação da Azure AD para cada vez mais cenários e aplicações. Por isso, tornamos o App Proxy ainda mais flexível, permitindo mais topologias. Pode criar grupos de conector de procuração de aplicação para que possa atribuir conectores específicos para servir aplicações específicas. Esta capacidade dá-lhe mais controlo e formas de otimizar a implementação do Seu Application Proxy.

Cada conector Proxy de aplicação é atribuído a um grupo de conector. Todos os conectores que pertencem ao mesmo grupo de conector atuam como uma unidade separada para uma alta disponibilidade e equilíbrio de carga. Todos os conectores pertencem a um grupo de conector. Se não criar grupos, todos os seus conectores estão num grupo predefinido. O seu administrador pode criar novos grupos e atribuir-lhes conectores no portal Azure.

Todas as aplicações são atribuídas a um grupo de conector. Se não criar grupos, todas as suas aplicações são atribuídas a um grupo predefinido. Mas se organizar os seus conectores em grupos, pode definir cada aplicação para trabalhar com um grupo de conector específico. Neste caso, apenas os conectores desse grupo servem o pedido mediante solicitação. Esta funcionalidade é útil se as suas aplicações estiverem hospedadas em diferentes locais. Pode criar grupos de conector com base na localização, para que as aplicações sejam sempre servidas por conectores que estejam fisicamente próximos deles.

> [!TIP]
> Se tiver uma grande implementação de Procuração de Aplicação, não atribua nenhuma aplicação ao grupo de conector predefinido. Desta forma, os novos conectores não recebem tráfego vivo até atribuí-los a um grupo de conector ativo. Esta configuração também permite colocar os conectores num modo de marcha lenta, movendo-os de volta para o grupo predefinido, para que possa realizar a manutenção sem afetar os seus utilizadores.

## <a name="prerequisites"></a>Pré-requisitos

Para agrupar os seus conectores, tem de se certificar de que [instalou vários conectores](application-proxy-add-on-premises-application.md). Quando instala um novo conector, junta-se automaticamente ao grupo de conector **Predefinido.**

## <a name="create-connector-groups"></a>Criar grupos de conector

Utilize estes passos para criar os grupos de conector que quiser.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Azure Ative Directory**  >  **Enterprise Application**  >  **Proxy**.
1. Selecione **novo grupo de conector**. Aparece a lâmina do novo grupo de conector.

   ![Mostra o ecrã para selecionar um novo grupo de conector](./media/application-proxy-connector-groups/new-group.png)

1. Dê um nome ao seu novo grupo de conector e, em seguida, utilize o menu suspenso para selecionar quais os conectores que pertencem a este grupo.
1. Selecione **Guardar**.

## <a name="assign-applications-to-your-connector-groups"></a>Atribuir aplicações aos seus grupos de conector

Utilize estes passos para cada aplicação que publicou com o Application Proxy. Pode atribuir uma aplicação a um grupo de conector quando a publicar pela primeira vez, ou pode usar estes passos para alterar a atribuição sempre que quiser.

1. A partir do painel de gestão do seu diretório, selecione **aplicações Enterprise**  >  **Todas as aplicações** > a aplicação que pretende atribuir a um grupo de conector > **Application Proxy**.
1. Utilize o menu suspenso **do Grupo Connector** para selecionar o grupo que pretende que a aplicação utilize.
1. **Selecione Guardar** para aplicar a alteração.

## <a name="use-cases-for-connector-groups"></a>Utilizar casos para grupos de conector

Os grupos de conector são úteis para vários cenários, incluindo:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Sites com vários centros de dados interligados

Muitas organizações têm uma série de datacenters interligados. Neste caso, pretende manter o máximo de tráfego possível dentro do datacenter porque as ligações cross-datacenter são caras e lentas. Pode implantar conectores em cada datacenter para servir apenas as aplicações que residem dentro do datacenter. Esta abordagem minimiza as ligações cross-datacenter e proporciona uma experiência inteiramente transparente aos seus utilizadores.

### <a name="applications-installed-on-isolated-networks"></a>Aplicações instaladas em redes isoladas

As aplicações podem ser hospedadas em redes que não fazem parte da rede corporativa principal. Pode utilizar grupos de conector para instalar conectores dedicados em redes isoladas para isolar também as aplicações na rede. Isto geralmente acontece quando um fornecedor de terceiros mantém uma aplicação específica para a sua organização.

Os grupos de conector permitem instalar conectores dedicados para as redes que publicam apenas aplicações específicas, tornando mais fácil e seguro a gestão de aplicações subcontratadas a fornecedores de terceiros.

### <a name="applications-installed-on-iaas"></a>Aplicações instaladas no IaaS

Para aplicações instaladas no IaaS para acesso à nuvem, os grupos de conector fornecem um serviço comum para garantir o acesso a todas as aplicações. Os grupos de conector não criam dependência adicional na sua rede corporativa, nem fragmentam a experiência da aplicação. Os conectores podem ser instalados em todos os datacenters em nuvem e servem apenas aplicações que residem nessa rede. Pode instalar vários conectores para obter uma elevada disponibilidade.

Veja-se como exemplo uma organização que tem várias máquinas virtuais ligadas à sua própria rede virtual iaaS. Para permitir que os colaboradores utilizem estas aplicações, estas redes privadas estão ligadas à rede corporativa utilizando a VPN site-to-site. Isto proporciona uma boa experiência para os colaboradores que estão localizados no local. Mas, pode não ser ideal para funcionários remotos, porque requer infraestruturas adicionais no local para o acesso à rota, como pode ver no diagrama abaixo:

![Diagrama que ilustra a rede Azure AD IaaS](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Com grupos de conector proxy de aplicação AD Azure, pode permitir que um serviço comum garanta o acesso a todas as aplicações sem criar dependência adicional na sua rede corporativa:

![Azure AD IaaS Múltiplos Fornecedores de Nuvem](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Multi-floresta – diferentes grupos de conector para cada floresta

A maioria dos clientes que implementaram o Application Proxy estão a usar as suas capacidades de assinatura única (SSO) executando a Delegação Restrita Kerberos (KCD). Para isso, as máquinas do conector precisam de ser unidas a um domínio que possa delegar os utilizadores na aplicação. O KCD suporta capacidades florestais cruzadas. Mas para as empresas que têm ambientes multi-florestais distintos sem confiança entre elas, um único conector não pode ser utilizado para todas as florestas. 

Neste caso, podem ser implantados conectores específicos por floresta, e definidos para servir aplicações que foram publicadas para servir apenas os utilizadores dessa floresta específica. Cada grupo de conector representa uma floresta diferente. Enquanto o inquilino e a maior parte da experiência são unificadas para todas as florestas, os utilizadores podem ser atribuídos às suas aplicações florestais usando grupos AZure AD.

### <a name="disaster-recovery-sites"></a>Locais de recuperação de desastres

Existem duas abordagens diferentes que pode tomar com um site de recuperação de desastres (DR), dependendo da forma como os seus sites são implementados:

* Se o seu site DR for construído em modo ativo, onde é exatamente igual ao site principal e tiver as mesmas definições de rede e AD, pode criar os conectores no site DR no mesmo grupo de conector que o local principal. Isto permite que a Azure AD detete falhas para si.
* Se o seu site DR estiver separado do site principal, pode criar um grupo de conector diferente no site DR, e ou 1) ter aplicações de backup ou 2) desviar manualmente a aplicação existente para o grupo de conector DR, conforme necessário.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Servir várias empresas de um único inquilino

Existem muitas maneiras diferentes de implementar um modelo no qual um único prestador de serviços implementa e mantém serviços relacionados com a AD Azure para várias empresas. Os grupos de conector ajudam o administrador a segregar os conectores e aplicações em diferentes grupos. Uma das formas, que é adequada para pequenas empresas, é ter um único inquilino Azure AD, enquanto as diferentes empresas têm o seu próprio nome de domínio e redes. Isto também é verdade para os cenários e situações de M&A em que uma única divisão de TI serve várias empresas por razões regulamentares ou empresariais.

## <a name="sample-configurations"></a>Configurações de amostras

Alguns exemplos que pode implementar incluem os seguintes grupos de conector.

### <a name="default-configuration--no-use-for-connector-groups"></a>Configuração padrão – não há uso para grupos de conector

Se não utilizar grupos de conector, a sua configuração seria assim:

![Exemplo Azure Ad Sem Grupos de Conector](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Esta configuração é suficiente para pequenas implementações e testes. Também funcionará bem se a sua organização tiver uma topologia de rede plana.

### <a name="default-configuration-and-an-isolated-network"></a>Configuração padrão e uma rede isolada

Esta configuração é uma evolução do padrão, na qual existe uma aplicação específica que funciona numa rede isolada como a rede virtual IaaS:

![Exemplo Azure AD Sem Grupos de Conector e uma rede isolada](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Configuração recomendada - vários grupos específicos e um grupo padrão para ocioso

A configuração recomendada para organizações grandes e complexas é ter o grupo de conector padrão como um grupo que não serve nenhuma aplicação e é usado para conectores inativos ou recém-instalados. Todas as aplicações são servidas utilizando grupos de conector personalizados. Isto permite toda a complexidade dos cenários acima descritos.

No exemplo abaixo, a empresa tem dois datacenters, A e B, com dois conectores que servem cada site. Cada site tem diferentes aplicações que funcionam nele.

![Exemplo de empresa com 2 datacenters e 2 conectores](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Passos seguintes

* [Compreenda os conectores Proxy de aplicação AD Azure](application-proxy-connectors.md)
* [Ativar o início de sessão único](what-is-single-sign-on.md)
