---
title: Nova experiência de registos de aplicativos em Azure AD B2C
description: Uma introdução à nova experiência de registo da App em Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e1d76c5ef1f003fe9e01b866343ef7de7ab4166
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92214928"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>A nova experiência de registos de aplicações para o Azure Ative Directory B2C

A nova experiência **[de registos de aplicações](https://aka.ms/b2cappregistrations)** para o Azure Ative Directory B2C (Azure AD B2C) está agora disponível em geral. Se está mais familiarizado com a experiência **applications** para registar candidaturas para Azure AD B2C, aqui referida como a "experiência do legado", este guia vai começar a usar a nova experiência.

## <a name="overview"></a>Descrição geral
Anteriormente, teve de gerir as suas aplicações viradas para o consumidor Azure AD B2C separadamente das restantes aplicações utilizando a experiência do legado. Isso significou diferentes experiências de criação de aplicações em diferentes lugares em Azure.

A nova experiência mostra todos os registos de aplicações AZure AD B2C e registos de aplicações AD AZure num só local e fornece uma forma consistente de os gerir. Desde a criação de uma aplicação virada para o cliente até à gestão de uma aplicação com permissões microsoft Graph para gestão de recursos, basta aprender uma maneira de fazer as coisas.

Você pode chegar à nova experiência navegando para **inscrições** da App em um inquilino Azure AD B2C tanto do **Azure AD B2C** como dos serviços **de Diretório Ativo Azure** no portal Azure.

A experiência de registos de aplicações Azure AD B2C baseia-se na experiência geral de Registo de [Aplicações](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) para qualquer inquilino AD Azure, mas é personalizada para inquilinos Azure AD B2C.

## <a name="whats-not-changing"></a>O que não está a mudar?
- As suas aplicações e configurações relacionadas podem ser encontradas como está na nova experiência. Não precisa de voltar a registar as aplicações e os utilizadores das suas aplicações não precisarão de voltar a entrar.

> [!NOTE]
> Para visualizar todas as suas aplicações previamente criadas, navegue para a lâmina **de registos** da App e selecione o separador **Todas as aplicações.** Isto irá exibir aplicações criadas na experiência do legado, na nova experiência e nas criadas no serviço AZure AD.

## <a name="key-new-features"></a>Principais novidades

-   Uma **lista de aplicações unificadas** mostra todas as suas aplicações que autenticam com Azure AD B2C e AD AZure em um lugar conveniente. Além disso, pode aproveitar as funcionalidades já disponíveis para aplicações AZure AD, incluindo a **Criada na** data, Certificados & estado dos **segredos,** barra de pesquisa e muito mais.

-   **O registo combinado** de aplicações permite-lhe registar rapidamente uma aplicação, quer se trate de uma aplicação virada para o cliente ou de uma aplicação para aceder ao Microsoft Graph.

- O painel **endpoints** permite identificar rapidamente os pontos finais relevantes para o seu cenário, incluindo a configuração de ligação OpenID, metadados SAML, Microsoft Graph API e [pontos finais de fluxo de utilizador OAuth 2.0](tokens-overview.md#endpoints).

- **Permissões API** e **Expor uma API** fornecem um âmbito, permissão e gestão de consentimento mais extensos. Agora também pode atribuir permissões de MS Graph e Azure AD Graph a uma aplicação.

-   **Proprietários** e **Manifesto** já estão disponíveis para aplicações que autenticam com Azure AD B2C. Pode adicionar os proprietários para as suas inscrições e editar diretamente propriedades de aplicações [utilizando o editor manifesto.](../active-directory/develop/reference-app-manifest.md)


## <a name="new-supported-account-types"></a>Novos tipos de conta suportados

Na nova experiência, selecione um tipo de conta de suporte a partir das seguintes opções:
- Contas apenas neste diretório organizacional
- Contas em qualquer diretório organizacional (Qualquer diretório AD Azure – Multitenant)
- Contas em qualquer fornecedor de identidade ou diretório organizacional (para autenticar utilizadores com fluxos de utilizador)

Para entender os diferentes tipos de conta, **selecione Ajude-me** a escolher na experiência de criação.

Na experiência do legado, as aplicações foram sempre criadas como aplicações viradas para o cliente. Para essas aplicações, o tipo de conta é definido **para Contas em qualquer fornecedor de identidade ou diretório organizacional (para autenticar utilizadores com fluxos de utilizador)**.
> [!NOTE]
> Esta opção é necessária para poder executar os fluxos de utilizador Azure AD B2C para autenticar os utilizadores para esta aplicação. Saiba [como registar uma aplicação para utilização com fluxos de utilizador.](tutorial-register-applications.md)

Também pode utilizar esta opção para utilizar o Azure AD B2C como prestador de serviços SAML. [Saiba mais](identity-provider-adfs2016-custom.md).

## <a name="applications-for-devops-scenarios"></a>Aplicações para cenários de DevOps
Pode utilizar os outros tipos de conta para criar uma aplicação para gerir os seus cenários de DevOps, como usar o Microsoft Graph para carregar políticas do Quadro de Experiência de Identidade ou de utilizadores de fornecimento. Saiba [como registar uma aplicação do Microsoft Graph para gerir os recursos Azure AD B2C](microsoft-graph-get-started.md).

Pode não ver todas as permissões do Microsoft Graph, porque muitas destas permissões não se aplicam aos utilizadores de consumidores Azure B2C. [Leia mais sobre a gestão de utilizadores usando o Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>Consentimento de administração e offline_access+âmbitos abertos
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

O âmbito **aberto** é necessário para que o Azure AD B2C possa inscrever os utilizadores numa aplicação. O **âmbito offline_access** é necessário para emitir fichas de atualização para um utilizador. Estes âmbitos foram previamente adicionados e dado consentimento administrativo por padrão. Agora, pode facilmente adicionar permissões para estes âmbitos durante o processo de criação, garantindo que o **consentimento da administração Grant para a opção de permissão de abertura e offline_access** é selecionada. Caso contrário, as permissões do Microsoft Graph podem ser adicionadas com o consentimento de administração nas definições de **permissões API** para uma aplicação existente.

Saiba mais sobre [permissões e consentimento.](../active-directory/develop/v2-permissions-and-consent.md)

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Plataformas/Autenticação: URLs de resposta/URIs de redirecionamento
Na experiência do legado, os vários tipos de plataformas foram geridos em **Propriedades** como urls de resposta para aplicações web/APIs e Redirecionamento URI para clientes nativos. Os "clientes nativos" também são conhecidos como "clientes públicos" e incluem aplicações para iOS, macOS, Android e outros tipos de aplicações móveis e desktop.

Na nova experiência, os URLs de resposta e uris de redirecionamento são ambos referidos como URIs de redirecionamento e podem ser encontrados na secção de Autenticação de uma **aplicação.** Os registos de aplicações não se limitam a ser uma aplicação web ou uma aplicação nativa. Pode utilizar o mesmo registo de aplicações para todos estes tipos de plataformas registando os respetivos URIs de redirecionamento.

Os URIs de redirecionamento são necessários para serem associados a um tipo de aplicação, web ou público (mobile e desktop). [Saiba mais sobre redirecionar URIs](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

As plataformas **iOS/macOS** e **Android** são um tipo de cliente público. Eles fornecem uma maneira fácil de configurar aplicações iOS/macOS ou Android com URIs redirecionador correspondente para uso com MSAL. Saiba mais sobre [as opções de configuração de aplicação.](../active-directory/develop/msal-client-applications.md)


## <a name="application-certificates--secrets"></a>Certificados de inscrição & segredos

Na nova experiência, em vez de **Keys,** usa a lâmina **de segredos & certificados** para gerir certificados e segredos. Os certificados & segredos permitem que as aplicações se identifiquem ao serviço de autenticação quando recebem fichas num local web endereçado (utilizando um esquema HTTPS). Recomendamos a utilização de um certificado em vez de um segredo de cliente para cenários de credenciais de clientes quando autenticar contra a Azure AD. Os certificados não podem ser usados para autenticar contra a Azure AD B2C.


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Características não aplicáveis nos inquilinos Azure AD B2C
As seguintes capacidades de registo de aplicações AD AD não são aplicáveis ou disponíveis em inquilinos Azure AD B2C:
- **Funções e administradores** - Não disponível para Azure AD B2C.
- **Branding** - A personalização UI/UX está configurada na experiência **de marca da Empresa** ou como parte de um fluxo de utilizador. Aprenda a [personalizar a interface do utilizador no Azure Ative Directory B2C](customize-ui-overview.md).
- **Verificação de domínio de** editor - A sua aplicação está registada em *.onmicrosoft.com*, o que não é um domínio verificado. Além disso, o domínio da editora é usado principalmente para conceder o consentimento do utilizador, o que não se aplica às aplicações AZURE AD B2C para a autenticação do utilizador. [Saiba mais sobre o domínio da editora.](https://docs.microsoft.com/azure/active-directory/develop/howto-configure-publisher-domain)
- **Configuração token** - O token é configurado como parte de um fluxo de utilizador em vez de uma aplicação.
- A experiência **Quickstarts** não está atualmente disponível para inquilinos Azure AD B2C.
<!-- - The **Integration assistant** blade is currently not available for Azure AD B2C tenants. -->

## <a name="limitations"></a>Limitações
A nova experiência tem as seguintes limitações:
- Neste momento, o Azure AD B2C não diferencia entre ser capaz de emitir tokens de acesso ou ID para fluxos implícitos; ambos os tipos de fichas estão disponíveis para fluxo de subvenção implícito se a opção **de fichas de ID** for selecionada na lâmina **de autenticação.**
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- Alterar o valor das contas suportadas não é suportado na UI. Você precisará usar o manifesto da aplicação, a menos que você esteja trocando entre Azure AD single-tenant e multi-inquilino.

## <a name="next-steps"></a>Passos seguintes

Para começar com a nova experiência de registo de aplicações:
* Saiba [como registar uma aplicação web.](tutorial-register-applications.md)
* Saiba [como registar uma API web.](add-web-api-application.md)
* Saiba [como registar uma aplicação de cliente nativo.](add-native-application.md)
* Saiba [como registar uma aplicação do Microsoft Graph para gerir os recursos Azure AD B2C](microsoft-graph-get-started.md).
* Saiba [como utilizar o Azure AD B2C como Fornecedor de Serviços SAML.](identity-provider-adfs2016-custom.md)
* Saiba mais sobre [os tipos de aplicações.](application-types.md)
