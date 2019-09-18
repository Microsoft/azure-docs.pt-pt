---
title: Como escolher o tipo de aplicativo a ser usado ao adicionar um aplicativo | Microsoft Docs
description: Entender os tipos de aplicativos com suporte que você pode integrar ao Azure AD e suas opções de configuração relacionadas
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: d5bd2397c345a4f670bde343f751cd69f825ecb9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056066"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Escolhendo o tipo de aplicativo ao adicionar um aplicativo no Azure Active Directory

Saiba mais sobre os quatro tipos de aplicativos que você pode adicionar ao Azure Active Directory (Azure AD). Quando você estiver adicionando um aplicativo no Azure Active Directory, será solicitado que você escolha um dos quatro tipos de aplicativo.

## <a name="what-are-the-types-of-applications"></a>Quais são os tipos de aplicativos?

O Azure AD dá suporte a quatro tipos principais de aplicativos que você pode adicionar usando o recurso **Adicionar** encontrado em **aplicativos empresariais**. Estas incluem:

- **Aplicativos da galeria do Azure ad** – um aplicativo que foi previamente integrado para logon único com o Azure AD.

- **Aplicativos de proxy de aplicativo** – um aplicativo em execução no seu ambiente local para o qual você deseja fornecer logon único seguro externamente.

- **Aplicativos desenvolvidos personalizados** – um aplicativo que sua organização deseja desenvolver na plataforma de desenvolvimento de aplicativos do Azure AD, mas que talvez ainda não exista.

- **Aplicativos que não são da Galeria** – traga seus próprios aplicativos! Qualquer link da Web desejado, ou qualquer aplicativo que renderiza um campo de nome de usuário e senha, dá suporte a protocolos SAML ou OpenID Connect, ou dá suporte a SCIM que você deseja integrar para logon único com o Azure AD.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Recursos e funcionalidades com suporte nos tipos de aplicativos

Os recursos a seguir têm suporte de qualquer um dos quatro tipos de aplicativos anteriores no Azure AD:

- **Início rápido** – comece a usar um aplicativo rapidamente seguindo [as etapas de implantação simples](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

- **Gerenciamento de propriedades gerais** – obtenha um [link profundo direto](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) para um aplicativo, [Personalize a identidade visual](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) de um aplicativo ou [desabilite o aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) para todos os usuários.

- **Gerenciamento de usuário e grupo** – [atribua](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) ou [remova](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) usuários e grupos de um aplicativo e, opcionalmente, atribua as funções de aplicativo específicas a que esses usuários e grupos têm acesso

- **Acesso a aplicativos de autoatendimento** – permita que os usuários solicitem [acesso de aplicativo de autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) a um aplicativo de seus painéis de acesso do aplicativo, seja adicionando um aplicativo diretamente ou [unindo um grupo habilitado para autoatendimento ](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), opcionalmente exigindo a aprovação dos negócios ao longo do caminho

- **Logs de entrada** – consulte [todas as entradas para um aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)ou todos os seus aplicativos

- **Logs de auditoria** – consulte [logs de auditoria detalhados sobre modificações em um aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)ou para todos os seus aplicativos

- **Acesso condicional e baseado em risco** – defina [regras de acesso baseadas em condição](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) poderosas que são impostas quando os usuários tentam entrar em um aplicativo específico

- **Exibição de permissões** – exiba qualquer uma das [permissões OAuth2](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) que um aplicativo tem acesso em seu diretório em um único local

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Modos de logon único e provisionamento com suporte de tipos de aplicativos específicos

A tabela a seguir descreve os diferentes modos de logon único e de provisionamento com suporte em cada um dos tipos de aplicativos anteriores. Você pode usar esta tabela para ajudá-lo a entender qual aplicativo você precisa adicionar para dar suporte a uma meta específica.

  ![Tabela Diferentes modos de provisionamento e SSO com suporte de cada tipo de aplicativo](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Como escolher um modo de logon único

A seguir estão os modos de **logon único** com suporte para aplicativos do Azure AD.

- **Logon único do Azure ad desabilitado** – escolha o **modo de logon** único desabilitado de logon único do Azure ad se você ainda não estiver pronto para integrar esse aplicativo com o logon único com o Azure ad ou simplesmente testando-o

- **Logon vinculado** – escolha o modo de logon **único** de [logon vinculado](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) se você tiver um aplicativo que já está conectado a uma solução de logon único existente ou se você quiser apenas publicar um link simples para seus usuários em seu [ Painel de acesso do aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) ou [iniciador de aplicativo do Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

- **Logon baseado em senha** – escolha o modo de logon **único** [logon baseado em senha](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) se seu aplicativo renderizar um campo de nome de usuário e senha HTML e você quiser armazenar esse nome de usuário e senha com segurança para serem reproduzidos no aplicativo mais tarde

- **Logon baseado em SAML** – escolha o modo de logon único de [logon baseado em SAML](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) se seu aplicativo der suporte aos protocolos SAML ou OpenID Connect ou se você quiser mapear usuários para funções de aplicativo específicas com base nas regras que você definir em seu SAML declarações

  >[!NOTE]
  >Essa opção não está disponível quando o proxy de aplicativo está configurado para um aplicativo.

- **Logon baseado em cabeçalho** – escolha este modo de logon único de [logon baseado em cabeçalho](application-proxy-configure-single-sign-on-with-ping-access.md) se você tiver um aplicativo usando PingAccess que dá suporte à autenticação baseada em cabeçalho HTTP no qual você deseja executar logon único

  >[!NOTE]
  >Essa opção só estará disponível quando o proxy de aplicativo e o PingAccess estiverem configurados para um aplicativo.

- **Autenticação integrada do Windows** – escolha o modo de logon único da [autenticação integrada do Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) ao expor um aplicativo WIA local no qual você deseja executar o logon único

  >[!NOTE]
  >Essa opção só estará disponível quando o proxy de aplicativo estiver configurado para um aplicativo.

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Modos de logon único para aplicativos desenvolvidos de modo personalizado

Os aplicativos personalizados desenvolvidos por meio da experiência de aplicativo desenvolvida de modo personalizado também dão suporte a modos de logon único adicionais não listados anteriormente, que incluem:

- Logon baseado em [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)

- Logon baseado em [OpenID connect 1,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)

- Logon baseado em [WS-Federation 1,2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)

- Logon baseado em [SAML 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)

Leia o [Guia do desenvolvedor Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) para saber mais sobre como criar um aplicativo desenvolvido de forma personalizada que dê suporte a esses modos de logon único.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Como definir o modo de logon único de um aplicativo

Para definir o modo de logon único de um aplicativo, siga estas instruções:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou coadministrador **.**
1. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.
1. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.
1. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.
1. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

1. Selecione o aplicativo para o qual você deseja configurar o logon único.
1. Depois que o aplicativo for carregado, clique em **logon único** no menu de navegação esquerdo do aplicativo.

## <a name="how-to-choose-a-provisioning-mode"></a>Como escolher um modo de provisionamento

- **Provisionamento manual** – escolha o modo de provisionamento [manual](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) se você tiver contas existentes ou desejar gerenciar contas para este aplicativo fora do Azure AD.

- **Provisionamento automático** – escolha o modo de [](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **provisionamento** automático se desejar habilitar o provisionamento automático baseado em API e/ou desprovisionar as contas de usuário para este aplicativo 

- **Provisionamento automático baseado em scim** – use o [provisionamento automático baseado em scim](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) se seu aplicativo der suporte ao protocolo scim para detectar alterações em usuários e grupos, que são emitidos automaticamente para alterações em qualquer aplicativo integrado com o Azure AD 

  >[!NOTE]
  >Essa opção não está listada como um modo de provisionamento específico, mas está habilitada por padrão para todos os aplicativos integrados ao Azure AD.

## <a name="how-to-set-an-applications-provisioning-mode"></a>Como definir o modo de provisionamento de um aplicativo

Para definir o modo de **provisionamento** de um aplicativo, siga estas instruções:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou coadministrador **.**
1. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.
1. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.
1. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.
1. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

1. Selecione o aplicativo para o qual você deseja configurar o provisionamento.
1. Depois que o aplicativo for carregado, clique em **provisionamento** no menu de navegação esquerdo do aplicativo.

## <a name="next-steps"></a>Passos Seguintes

[Managing Applications with Azure Active Directory](what-is-application-management.md) (Gerir Aplicações com o Azure Active Directory)
