---
title: Experiências de usuário final para aplicações - Azure Active Directory | Documentos da Microsoft
description: Azure Active Directory (Azure AD) fornece várias formas de personalizáveis para implementar aplicações para utilizadores finais em sua organização.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: celested
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e00ecde5550b5ef4f1698fa879a039dee8aad12
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181729"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Experiências de utilizador final para as aplicações no Azure Active Directory
Azure Active Directory (Azure AD) fornece várias formas de personalizáveis para implementar aplicações para utilizadores finais em sua organização:

* Painel de acesso do Azure AD
* Iniciador de aplicações do Office 365
* Início de sessão direto em aplicações federadas
* Ligações avançadas para aplicações federadas, baseadas em palavras-passe ou existentes

Qual método (s) optar por implementar na sua organização é a seu critério.

## <a name="azure-ad-access-panel"></a>Painel de acesso do Azure AD
Painel de acesso em https://myapps.microsoft.com é um portal baseado na web que permite que um utilizador final com uma conta organizacional no Azure Active Directory para ver e aplicativos baseados na nuvem de lançamento para a qual tenham sido concedido acesso pelo administrador do Azure AD. Se for um utilizador final com [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), também pode utilizar as capacidades de gestão de grupos self-service através do painel de acesso.

![Painel de acesso do Azure AD](media/what-is-single-sign-on/azure-ad-access-panel.png)

O painel de acesso separado no portal do Azure e não requer que os utilizadores tenham uma subscrição do Azure ou uma subscrição do Office 365.

Para obter mais informações sobre o painel de acesso do Azure AD, consulte a [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Iniciador de aplicações do Office 365
Para organizações que implantaram o Office 365, o aplicações atribuídas a utilizadores através do Azure AD também serão apresentadas no portal do Office 365 em [ https://portal.office.com/myapps ](https://portal.office.com/myapps). Isto torna mais fácil e conveniente para os utilizadores numa organização para iniciar as aplicações sem ter de utilizar um portal segundo e é a solução de início de aplicação recomendada para organizações que utilizam o Office 365.

![Portal do Office 365](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Para obter mais informações sobre o iniciador de aplicações do Office 365, consulte [ter a aplicação aparece no iniciador de aplicações do Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Início de sessão direto em aplicações federadas
Mais aplicações federadas que suportam SAML 2.0, WS-Federation ou OpenID conectam também suporte a capacidade dos utilizadores iniciar a aplicação e, em seguida, é feita através do Azure AD ao redirecionamento automático ou ao clicar numa ligação para iniciar sessão. Isso é conhecido como fornecedor de serviços-iniciada pelo início de sessão e mais aplicações federadas na Galeria de aplicações do Azure AD suportam esta (consulte a documentação ligada a partir do Assistente de configuração de início de sessão único da aplicação no portal do Azure para obter detalhes).

![](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Ligações de início de sessão em diretas
O Azure AD também suporta únicas início de sessão em ligações diretas para aplicativos individuais que oferecem suporte baseado em palavra-passe de início de sessão único, ligado início de sessão único e qualquer outra forma de início de sessão único federado.

Estas ligações são especificamente elaboradas URLs que enviam um utilizador através do processo de início de sessão do AD do Azure para uma aplicação específica sem a necessidade do lançamento de utilizador-los a partir do Azure AD aceder ao painel ou do Office 365. Estes **URLs de acesso de utilizador** pode ser encontrado em Propriedades de aplicativos empresariais disponíveis em Azure Active Directory no portal do Azure.

Estas ligações podem ser copiadas e coladas em qualquer lugar que deseja fornecer uma ligação de início de sessão à aplicação selecionada. Isto pode ser num e-mail ou em qualquer portal baseado na web personalizado que configurou para o acesso de aplicação do utilizador. Eis um exemplo de um URL do Azure AD direto único início de sessão do Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Semelhante a URLs específicos da organização para o painel de acesso, pode personalizar ainda mais essa URL adicionando um dos domínios verificados ou Active Directory para o seu diretório depois do domínio myapps.microsoft.com. Isso garante que qualquer corporativa é carregada imediatamente na página de início de sessão sem o utilizador tenha de introduzir o ID de utilizador pela primeira vez:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Quando um utilizador autorizado clica em um desses links específico do aplicativo, eles primeiro ver seus organizacional início de sessão na página (partindo do princípio de que não estejam assinados) e após o início de sessão são redirecionados para a sua aplicação sem parar no painel de acesso pela primeira vez. Se o utilizador está em falta a pré-requisitos para aceder à aplicação, como a extensão de browser de início de sessão único baseado em palavra-passe, a ligação pedirá ao utilizador para instalar a extensão em falta. O URL da ligação também permanece constante se a configuração única início de sessão para a aplicação for alterada.

Estas ligações utilizam os mesmos mecanismos de controlo de acesso, como o painel de acesso e o Office 365, e apenas esses utilizadores ou grupos a quem foi concedidos ao aplicativo no portal do Azure será capazes de autenticar com êxito. No entanto, qualquer utilizador que não está autorizado, verá uma mensagem que explica o que não foi concedidos acesso e recebem uma ligação para carregar o painel de acesso para ver as aplicações disponíveis para os quais têm acesso.

## <a name="next-steps"></a>Passos Seguintes
Para planos de implantação, consulte [planos de implantação do Azure Active Directory](../fundamentals/active-directory-deployment-plans.md)
