---
title: Publicação de aplicações em redes separadas e localizações com grupos de conexão no Proxy de aplicação do Azure AD | Documentos da Microsoft
description: Aborda como criar e gerir grupos de conectores no Proxy de aplicações do Azure AD.
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
ms.openlocfilehash: c22d44b02b3cc25c855361cab17132c46fa04794
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783706"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicar aplicações em redes separadas e localizações através de grupos de conectores

Os clientes utilizam o Proxy de aplicações do Azure AD para cada vez mais cenários e aplicativos. Então, fizemos do Proxy de aplicações ainda mais flexível, permitindo que mais de topologias. Pode criar grupos de conector de Proxy de aplicações para que pode atribuir conectores específicos para servir aplicativos específicos. Esta capacidade fornece mais controle e maneiras de otimizar a sua implementação do Proxy de aplicações. 

Cada conector de Proxy de aplicações é atribuído a um grupo de conectores. Todos os conectores que pertencem ao mesmo grupo de conector atuam como uma unidade separada para elevada disponibilidade e balanceamento de carga. Todos os conectores pertencerem a um grupo de conectores. Se não criar grupos, em seguida, todos os seus conectores estão num grupo predefinido. O administrador pode criar novos grupos e atribuir-lhes conectores no portal do Azure. 

Todas as aplicações são atribuídas a um grupo de conectores. Se não criar grupos, em seguida, todas as suas aplicações são atribuídas a um grupo predefinido. Mas se organizar os seus conectores em grupos, pode definir cada aplicativo para trabalhar com um grupo conector específico. Neste caso, apenas os conectores nesse grupo de servem a aplicação mediante pedido. Esta funcionalidade é útil se as aplicações são alojadas em localizações diferentes. Pode criar grupos de conectores com base na localização, para que os aplicativos são sempre servidos por conectores que estão fisicamente próximos-los.

>[!TIP] 
>Se tiver uma grande implantação de Proxy de aplicações, não atribua todas as aplicações para o grupo do conector de predefinição. Dessa forma, os novos conectores não recebam qualquer tráfego em direto até que a atribuição a um grupo do conector do Active Directory. Esta configuração também permite-lhe colocar conectores num modo inativo por movê-los de volta para o grupo predefinido, para que pode executar a manutenção sem afetar os seus utilizadores.

## <a name="prerequisites"></a>Pré-requisitos
Para os conectores de grupo, terá de certificar-se de que [instalados vários conectores](application-proxy-add-on-premises-application.md). Quando instala um novo conector, é automaticamente associado a **predefinido** grupo do conector.

## <a name="create-connector-groups"></a>Criar grupos de conectores
Utilize estes passos para criar grupos de conectores tantas quanto quiser. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **do Azure Active Directory** > **aplicações empresariais** > **proxy de aplicações**.
2. Selecione **novo grupo conector**. No painel do novo grupo conector aparece.

   ![Selecione o novo grupo conector](./media/application-proxy-connector-groups/new-group.png)

3. Dê um nome ao seu novo grupo do conector e, em seguida, utilize o menu pendente para selecionar quais conectores pertencem neste grupo.
4. Selecione **Guardar**.

## <a name="assign-applications-to-your-connector-groups"></a>Atribuir aplicações aos seus grupos de conector
Utilize estes passos para cada aplicação que tenham publicado com o Proxy de aplicações. Pode atribuir uma aplicação para um grupo de conectores quando publicá-lo pela primeira vez, ou pode utilizar estes passos para alterar a atribuição de sempre que quiser.   

1. A partir do dashboard de gestão para o seu diretório, selecione **aplicações empresariais** > **todas as aplicações** > a aplicação que pretende atribuir a um grupo do conector > **Proxy de aplicações**.
2. Utilizar o **grupo de conectores** menu pendente para selecionar o grupo que pretende que a aplicação a utilizar.
3. Selecione **guardar** para aplicar a alteração.

## <a name="use-cases-for-connector-groups"></a>Casos de utilização para grupos de conectores 

Grupos de conexão são úteis para vários cenários, incluindo:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Sites com vários datacenters interconectados

Muitas organizações têm um número de datacenters interconectados. Neste caso, pretende manter como grande parte do tráfego no Centro de dados possível, porque as ligações em vários datacenters são dispendiosos e lenta. Pode implementar conectores em cada datacenter para servir apenas as aplicações que residem no Centro de dados. Essa abordagem minimiza as ligações em vários datacenters e fornece uma experiência totalmente transparente para os seus utilizadores.

### <a name="applications-installed-on-isolated-networks"></a>Aplicativos instalados em redes isoladas

Aplicativos podem ser hospedados em redes que não fazem parte da rede corporativa principal. Pode utilizar grupos de conectores para instalar os conectores dedicados em redes isoladas também isolar aplicativos à rede. Normalmente, isto acontece quando um fornecedor de terceiros mantém uma aplicação específica para a sua organização. 

Grupos de conectores permitem que instalar os conectores dedicados para essas redes que publicar apenas aplicativos específicos, tornando mais fácil e seguro para terceirizar a gestão de aplicações para fornecedores de terceiros.

### <a name="applications-installed-on-iaas"></a>Aplicativos instalados em IaaS 

Para aplicativos instalados no IaaS para acesso à nuvem, os grupos de conector fornecem um serviço comum para proteger o acesso a todas as aplicações. Grupos de conectores não criar dependência adicional na sua rede empresarial ou fragmentados a experiência da aplicação. Os conectores podem ser instalados em todos os datacenters de cloud e servem apenas as aplicações que residem na rede. Pode instalar vários conectores para assegurar elevada disponibilidade.

Veja como um exemplo de uma organização que tenha várias máquinas de virtuais ligadas à sua própria IaaS alojados de rede virtual. Para permitir que os funcionários usem estas aplicações, estas redes privadas estão ligadas à rede empresarial através de VPN de site a site. Isso fornece uma boa experiência para os funcionários que estão localizados no local. Mas, talvez não seja ideal para os empregados remotos, porque requer mais infraestrutura no local para encaminhar o acesso, como pode ver no diagrama abaixo:

![Rede de IaaS do AzureAD](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Com os grupos de conector de Proxy de aplicações do Azure AD, pode ativar a um serviço comum proteger o acesso a todas as aplicações sem a criação de dependência adicional na sua rede empresarial:

![Fornecedores de Cloud de vários de IaaS do AzureAD](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Várias florestas – grupos de conectores diferentes para cada floresta

A maioria dos clientes que tenham implementado o Proxy de aplicações estão a utilizar suas capacidades de início de sessão único (SSO) através de Kerberos Constrained Delegation (KCD). Para atingir esse objetivo, máquinas do conector tem de ser associado a um domínio que pode delegar os utilizadores no sentido da aplicação. KCD oferece suporte a recursos de múltiplas florestas. Mas, para as empresas que têm ambientes de várias florestas distintas sem qualquer fidedignidade entre eles, um único conector não pode ser utilizado para todas as florestas. 

Neste caso, conectores específicos podem ser implementados por floresta e definidos para servir aplicativos que foram publicados para servir apenas os utilizadores nessa floresta específica. Cada grupo de conectores representa uma floresta diferente. Embora o inquilino e a maioria da experiência é unificada para todas as florestas, os utilizadores podem ser atribuídos às respetivas aplicações de floresta utilizando grupos do Azure AD.
 
### <a name="disaster-recovery-sites"></a>Sites de recuperação após desastre

Existem duas abordagens diferentes, que pode tomar com um site de recuperação (DR) após desastre, dependendo de como os sites são implementados:

* Se o seu site de DR baseia-se no modo ativo-ativo onde é exatamente como o site principal e tem o mesmo sistema de rede e as definições do AD, pode criar os conectores no site de DR no mesmo grupo de conector, como o site principal. Isto permite ao Azure AD detetar as ativações pós-falha por si.
* Se seu site de DR é diferente do site principal, pode criar um grupo de conectores diferentes no site de DR, e um 1) tiver aplicativos de backup ou desviar 2) manualmente a aplicação existente para o grupo do conector de DR, conforme necessário.
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>Servir várias empresas a partir de um único inquilino

Existem várias formas diferentes de implementar um modelo no qual um único provedor de serviços implementa e mantém do Azure AD relacionados com serviços para várias empresas. Grupos de conectores ajudam o administrador de separar os conectores e aplicações em grupos diferentes. Uma forma, o que é adequada para pequenas empresas, é ter um único Azure AD, enquanto as empresas diferentes têm seu próprio nome de domínio e redes de inquilino. Isto também se aplica para cenários de M & r e situações em que um único divisão de TI da serve várias empresas por motivos de regulamentação ou comerciais. 

## <a name="sample-configurations"></a>Configurações de exemplo

Alguns exemplos que pode implementar, incluem os seguintes grupos de conector.
 
### <a name="default-configuration--no-use-for-connector-groups"></a>Configuração predefinida – não utilize para grupos de conectores

Se não utilizar grupos de conexão, a configuração teria o seguinte aspeto:

![AzureAD não existem grupos de conector](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)
 
Esta configuração é suficiente para testes e Implantações pequenas. Ela também funcionará bem se sua organização tiver uma topologia de rede simples.
 
### <a name="default-configuration-and-an-isolated-network"></a>Configuração predefinida e uma rede isolada

Esta configuração é uma evolução do predefinido, em que é uma aplicação específica que é executado numa rede isolada, tais como a rede virtual do IaaS: 

![AzureAD não existem grupos de conector](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Configuração – recomendada para vários grupos específicos e um grupo padrão para inativo

A configuração recomendada para organizações grandes e complexas é fazer com o grupo do conector padrão como um grupo que não atender a todas as aplicações e é utilizado para os conectores de inatividade ou recentemente instalados. Todos os aplicativos são atendidos a utilização de grupos de conector personalizado. Isto permite que todos os a complexidade dos cenários descritos acima.

No exemplo abaixo, a empresa tem dois centros de dados, A e B, com dois conectores que servem de cada site. Cada site tem diferentes aplicativos executados nele. 

![AzureAD não existem grupos de conector](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>Passos Seguintes

* [Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-connectors.md)
* [Ativar o início de sessão único](what-is-single-sign-on.md)


