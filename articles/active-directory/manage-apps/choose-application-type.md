---
title: Como escolher o tipo de aplicação a utilizar ao adicionar uma aplicação | Documentos da Microsoft
description: Compreender os tipos suportados de aplicativos pode integrar com o Azure AD e as opções de configuração relacionados
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13b0c03f03012058616dba21536cfc3f292fff4b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091516"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Escolher o tipo de aplicação ao adicionar uma aplicação no Azure Active Directory
Saiba mais sobre os quatro tipos de aplicativos, que pode adicionar ao Azure Active Directory (Azure AD). Quando estiver a adicionar uma aplicação no Azure Active Directory, será solicitado a escolher um do tipo de quatro aplicação. 

## <a name="what-are-the-types-of-applications"></a>Quais são os tipos de aplicativos?

O Azure AD suporta quatro tipos de aplicativo principal que podem ser adicionados com o **Add** funcionalidade disponível em **aplicações empresariais**. Estas incluem:

-   **Aplicações de galeria do Azure AD** – uma aplicação que foi pré-integradas para início de sessão único com o Azure AD.

-   **Aplicações de Proxy de aplicação** – uma aplicação em execução no seu ambiente no local que deseja fornecer seguro início de sessão único para externamente.

-   **Personalizada aplicativos** – uma aplicação que sua organização deseja desenvolver na plataforma de desenvolvimento de aplicativo de AD do Azure, mas que talvez ainda não existe.

-   **Aplicações não à galeria** – traga os seus próprios aplicativos! Qualquer ligação web que desejar, qualquer aplicativo que processa um campo de nome de utilizador e palavra-passe, oferece suporte a protocolos SAML ou OpenID Connect ou suporta SCIM que deseja integrar para início de sessão único com o Azure AD.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Funcionalidades e capacidades suportadas pelos tipos de aplicação

As seguintes funcionalidades são suportadas por qualquer um dos tipos de quatro aplicação anteriores no Azure AD:

-   **Início Rápido** – comece a trabalhar com uma aplicação rapidamente ao seguir [passos de implementação simples](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

-   **Gestão de propriedades gerais** – obter um [deeplink direto](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) a uma aplicação, [personalizar a imagem corporativa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) de um aplicativo, ou [desativar a aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) para todos os utilizadores.

-   **Gestão de utilizadores e grupos** – [atribuir](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) ou [remover](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) utilizadores e grupos a uma aplicação e, opcionalmente, atribua as funções de aplicação específica estes utilizadores e grupos têm acesso a

-   **Acesso de aplicações self-service** – permitir que os utilizadores pedir [acesso da aplicação self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para uma aplicação a partir de seus painéis de acesso de aplicação ou pela adição de uma aplicação diretamente ou [ associar um grupo self-service ativado](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), opcionalmente, exigir a aprovação de negócios ao longo do caminho

-   **Registos de início de sessão** – veja [todos os inícios de sessão a uma aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins), ou todas as suas aplicações

-   **Registos de auditoria** – veja [detalhadas de registos de auditoria sobre alterações uma aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), ou todas as suas aplicações

-   **Acesso condicional e baseado no risco** – defina poderosas [regras de acesso com base na condição](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) que são aplicadas quando os utilizadores tentam iniciar sessão numa aplicação específica

-   **Vista de permissões** – ver qualquer um da [permissões de OAuth2](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) um aplicativo tem acesso a no seu diretório de um único local

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Início de sessão único e o aprovisionamento de modos suportados por tipos específicos de aplicativos

A tabela seguinte descreve os diferentes únicos início de sessão e aprovisionamento modos suportados por cada um dos tipos de aplicativo anteriores. Pode utilizar esta tabela para ajudá-lo a compreender que aplicação terá de adicionar para oferecer suporte a um objetivo específico.

  ![Tabela de tipos de aplicação](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Como escolher um modo de início de sessão único

Seguem-se a suportados **início de sessão único** modos para aplicações do Azure AD.

- **O Azure AD início de sessão único desativado** – escolha do Azure AD início de sessão único desativado **modo de início de sessão único** se ainda não estiver pronto para integrar esta aplicação com o início de sessão único com o Azure AD, ou são simplesmente testá-lo horizontalmente

- **Ligado logon** – escolha o [ligado início de sessão](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) **modo de início de sessão único** se tiver uma aplicação que já está ligada com uma única início de sessão na solução existente, ou se quiser apenas publicar uma ligação simple para os seus utilizadores no respetivo [painel de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) ou [iniciador de aplicações do Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

- **Com base em palavra-passe de início de sessão** – escolha o [baseado em palavra-passe de início de sessão](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) **modo de início de sessão único** se seu aplicativo processa um campo de nome de utilizador e palavra-passe HTML e pretende armazenar esse nome de utilizador e palavra-passe de forma segura a ser reproduzido para a aplicação mais tarde

- **Baseado em SAML logon** – escolha o [baseado em SAML logon](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) início de sessão único no modo, se o aplicativo dá suporte os protocolos SAML ou OpenID Connect ou, se pretender ser capaz de mapear os utilizadores a funções de aplicação específica, com base em regras define em suas declarações SAML *

  >[!NOTE]
  >Esta opção não está disponível quando o proxy de aplicações está configurado para uma aplicação.
  >
  >

- **Com base no cabeçalho de início de sessão** – Escolha esta [com base no cabeçalho de início de sessão](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) único-modo de sessão se tiver uma aplicação com o PingAccess que suporta a autenticação baseada em cabeçalho de HTTP que deseja executar o início de sessão único para 

  >[!NOTE]
  >Esta opção só está disponível quando o proxy de aplicações e o PingAccess está configurado para uma aplicação.
  >
  >

- **Autenticação Windows integrada** – escolha o [autenticação integrada do Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) início de sessão único no modo ao expor um aplicativo de WIA no local que pretende efetuar o início de sessão único para 

  >[!NOTE]
  >Esta opção só está disponível quando o proxy de aplicações está configurado para uma aplicação.
  >
  >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Modos de início de sessão únicos para aplicações com programação personalizada

Aplicativos que tenha personalizadas desenvolvidas ao longo de aplicação personalizada desenvolvida experiência também suporte adicional único início de sessão modos listados não tenha sido anteriormente, que incluem:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) com base no início de sessão

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) com base no início de sessão

-   [WS-Federation 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) com base no início de sessão

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) com base no início de sessão

Leitura a [Guia do programador do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) para saber mais sobre como criar uma aplicação personalizada desenvolvida, que oferece suporte a esses modos de início de sessão únicos.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Como definir único início de sessão em modo um aplicativo

Para definir um aplicativo **início de sessão único** modo, siga estas instruções:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação para o qual pretende configurar início de sessão único.

7. Assim que o aplicativo é carregado, clique em **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

## <a name="how-to-choose-a-provisioning-mode"></a>Como escolher um modo de aprovisionamento

- **Aprovisionamento manual** – escolha o [Manual](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) modo de aprovisionamento, se tiver contas existentes, ou se pretende gerir contas para este aplicativo fora do Azure AD.

- **Aprovisionamento automático** – escolha o [automática](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **modo de aprovisionamento** se pretender ativar com base na API de aprovisionamento automático e/ou anular o aprovisionamento de contas de utilizador para esta aplicação 

  >[!NOTE]
  >Esta opção só está disponível para aplicativos dentro de **em destaque** categoria da [Galeria de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal).
  >
  >

- **Aprovisionamento automático baseado em SCIM** – utilize [aprovisionamento automático baseado em SCIM](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) se a aplicação suporta o protocolo SCIM para detectar alterações aos utilizadores e grupos, que são emitidos automaticamente para que as alterações qualquer aplicativo integrado no Azure AD 

  >[!NOTE]
  >Esta opção não estiver listada como um modo de aprovisionamento específico, mas está ativada por predefinição para todas as aplicações que são integradas no Azure AD.
  >
  >

## <a name="how-to-set-an-applications-provisioning-mode"></a>Como definir um aplicativo do modo de aprovisionamento

Para definir um aplicativo **aprovisionamento** modo, siga estas instruções:

Para definir um aplicativo **início de sessão único** modo, siga estas instruções:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação para o qual pretende configurar o aprovisionamento.

7. Assim que o aplicativo é carregado, clique em **aprovisionamento** no menu de navegação do lado esquerdo da aplicação.

## <a name="next-steps"></a>Passos Seguintes
[Managing Applications with Azure Active Directory](what-is-application-management.md) (Gerir Aplicações com o Azure Active Directory)
