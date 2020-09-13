---
title: Nova experiência de registos de aplicações do portal do Azure
titleSuffix: Microsoft identity platform
description: Uma introdução à nova experiência de registo de aplicações no portal Azure
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 13242a41e1d10b0df031bf10fd646d9ec3cf47c3
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89437773"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>A nova experiência de registo de aplicações do portal Azure

Existem muitas melhorias na experiência de [inscrições](https://go.microsoft.com/fwlink/?linkid=2083908) da nova App no portal Azure. Se estiver mais familiarizado com o portal de registo de aplicações (apps.dev.microsoft.com) para registo ou gestão de aplicações, aqui referida como a "experiência antiga", este guia vai começar a usar a nova experiência.

## <a name="whats-not-changing"></a>O que não está a mudar?

- As suas aplicações e configurações relacionadas podem ser encontradas como está na nova experiência. Não precisa de voltar a registar as aplicações e os utilizadores das suas aplicações não precisarão de voltar a entrar.

    > [!NOTE]
    > Tem de iniciar sação com a conta que usou para registar as candidaturas para as encontrar no portal Azure. Recomendamos que verifique se o utilizador assinado no portal Azure corresponde ao utilizador que foi assinado no portal de registo da Aplicação, comparando o endereço de e-mail do seu perfil.
    >
    > Em alguns casos, especialmente quando você assina usando contas pessoais da Microsoft (por exemplo, Outlook, Live, Xbox, etc.) com um endereço de e-mail AD AZure, descobrimos que quando você vai para o portal Azure a partir da experiência antiga, ele assina-o em uma conta diferente com o mesmo e-mail no seu inquilino AZURE AD. Se ainda acredita que faltam as suas candidaturas, assine e inscreva-se com a conta certa.

- As aplicações Live SDK criadas com contas pessoais da Microsoft ainda não são suportadas no portal Azure e continuarão a manter-se na experiência antiga num futuro próximo.

## <a name="key-changes"></a>Alterações-chave

-   Na experiência antiga, as aplicações foram registadas por padrão como aplicações *convergentes* - aplicações que suportam todas as contas organizacionais (multitenantes) e contas pessoais da Microsoft. Isto não poderia ser modificado através da experiência antiga, dificultando a criação de apps que suportassem apenas contas organizacionais (multitenantes ou inquilinos individuais).
    A nova experiência permite registar aplicações que suportam todas essas opções. [Saiba mais sobre os tipos de aplicativos.](active-directory-v2-registration-portal.md)

-   Na nova experiência, se a sua conta pessoal da Microsoft também estiver num inquilino Azure AD, verá três separadores- todas as aplicações no arrendatário, aplicações próprias no arrendatário, bem como aplicações a partir da sua conta pessoal. Portanto, se acredita que faltam aplicações registadas na sua conta pessoal da Microsoft, verifique as **Aplicações a partir do separador da sua conta pessoal.**

-   Na nova experiência, você pode facilmente alternar entre inquilinos navegando para o seu perfil e escolhendo o diretório da Switch.

## <a name="list-of-applications"></a>Lista de candidaturas

-   A nova lista de aplicações mostra aplicações que foram registadas através da experiência de registos de aplicações antigas no portal Azure (aplicações que assinam apenas nas contas AD do Azure) bem como aplicações registadas no portal de [registo de aplicações](https://apps.dev.microsoft.com/) (aplicações que assinam tanto nas contas AZURE AD como nas contas pessoais da Microsoft).

-   A nova lista de aplicações tem duas colunas adicionais: **Criada na** coluna e certificados & coluna **de segredos** que mostra o estado (atual, expirando em breve ou expirado) de credenciais que foram registadas na app.

## <a name="new-app-registration"></a>Registo de nova aplicação

Na experiência antiga, para registar uma app só foi obrigado a fornecer um Nome. As aplicações que foram criadas foram registadas como aplicações *convergentes* - aplicações que suportam todos os diretórios organizacionais (multitenant) e contas pessoais da Microsoft.  Isto não poderia ser modificado através da experiência antiga, dificultando a criação de apps que suportassem apenas contas organizacionais (individuais ou multi-arrendantes). [Saiba mais sobre tipos de conta suportados](v2-supported-account-types.md)

Na nova experiência, deve fornecer um Nome para a aplicação e escolher os tipos de conta suportado. Pode opcionalmente fornecer um URI de redirecionamento.
Se fornecer um URI de redirecionamento, terá de especificar se é web/público (nativo/móvel e ambiente de trabalho). Para obter mais informações sobre como registar uma aplicação utilizando a experiência de registos de novas aplicações, consulte [este quickstart](quickstart-register-app.md).

## <a name="app-management-page"></a>Página de gestão de aplicativos

A experiência antiga tinha uma única página de gestão de aplicações para apps com as seguintes secções: Propriedades, Segredos de Aplicação, Plataformas, Proprietários, Permissões de Gráficos da Microsoft, Perfil e Opções Avançadas.

A nova experiência no portal Azure apresenta estas funcionalidades em páginas separadas. Aqui é onde você pode encontrar a funcionalidade equivalente:

- Propriedades - Nome e ID da aplicação está na página 'Visão Geral'.
- Os Segredos de Aplicação está na página de segredos & certificados
- A configuração das plataformas está na página de Autenticação
- As permissões do Microsoft Graph estão na página de permissões da API juntamente com outras permissões
- O perfil está na página de Branding
- Opção avançada - Suporte SDK ao vivo está na página autenticação.

## <a name="application-secretscertificates--secrets"></a>Segredos de aplicação/Certificados & segredos

Na nova experiência, os **segredos da aplicação** foram renomeados para **Certificados & segredos.** Além disso, **as chaves públicas** são referidas como **Certificados** e **Palavras-Passe** são referidas como **segredos do Cliente.** Optámos por não trazer esta funcionalidade para a nova experiência por razões de segurança, pelo que já não é possível gerar um novo par de chaves.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Plataformas/Autenticação: URLs de resposta/URIs de redirecionamento
Na experiência antiga, uma aplicação tinha secções de Plataformas para Web, native e Web API para configurar URLs de redirecionamento, URL de logout e fluxo implícito.

Na nova experiência, os URLs de resposta podem ser encontrados numa \' secção de autenticação de aplicações. Além disso, são referidos como URIs de redirecionamento e o formato para URIs de redirecionamento mudou. São obrigados a ser associados a um tipo de aplicação (web ou cliente público - mobile e desktop). [Saiba mais](quickstart-register-app.md#add-a-redirect-uri)

As APIs web estão configuradas na página Expor uma API.

> [!NOTE]
> Experimente a experiência das novas definições de autenticação onde pode configurar as definições para a sua aplicação com base na plataforma ou dispositivo que pretende direcionar. [Saiba mais](quickstart-register-app.md#configure-platform-settings)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Permissões microsoft Graph/API

-   Ao selecionar uma API na experiência antiga, pode escolher apenas a partir de APIs de Gráficos da Microsoft. Na nova experiência, pode escolher entre muitas APIs da Microsoft, incluindo Microsoft Graph, APIs da sua organização e as suas APIs, esta é apresentada em três separadores: APIs da Microsoft, APIs que a minha organização utiliza, ou As Minhas APIs. A barra de pesquisa em APIs a minha organização usa pesquisas de separadores através de diretores de serviço no inquilino.

    > [!NOTE]
    > Não verá este separador se o seu pedido não estiver associado a um inquilino. Para obter mais informações sobre como solicitar permissões usando a nova experiência, consulte [este quickstart](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md).

-   A velha experiência não tinha um botão **de permissões grant.** Na nova experiência, há uma secção de consentimento grant com um botão **de consentimento de administração Grant** na secção de permissões API de uma aplicação. Apenas um administrador pode conceder o consentimento e este botão está ativado apenas para administradores. Quando um administrador seleciona o botão **de consentimento administrativo Grant,** o consentimento administrativo é concedido a todas as permissões solicitadas.

## <a name="profile"></a>Perfil
Na experiência antiga, o Profile tinha logotipo, URL de página inicial, URL de Serviço e configuração de URL de Declaração de Privacidade. Na nova experiência, estes podem ser encontrados na página de Branding.

## <a name="application-manifest"></a>Manifesto de aplicação
Na nova experiência, a página Manifesto permite editar e atualizar os atributos da app. Para mais informações, consulte [o manifesto de aplicação.](reference-app-manifest.md)

## <a name="new-ui"></a>Nova UI
Há um novo UI para propriedades que anteriormente só podiam ser definidas usando o editor manifesto ou a API, ou não existiam.

-   O fluxo de subvenção implícita (oauth2AllowImplicitFlow) pode ser encontrado na página de Autenticação. Ao contrário da experiência antiga, você pode ativar o acesso a fichas ou fichas de identificação, ou ambos.

-   Os âmbitos definidos por esta API (oauth2Permissions) e aplicações de clientes autorizadas (aplicações pré-autorizadas) podem ser configurados através da página Expor uma API. Para obter mais informações sobre como configurar uma aplicação para ser uma API web e expor permissões/âmbitos, consulte [este arranque rápido](quickstart-configure-app-expose-web-apis.md).

-   O domínio do editor (que é apresentado aos utilizadores no [pedido de consentimento \' da aplicação)](application-consent-experience.md)pode ser encontrado na página de Branding. Para obter mais informações sobre como configurar um domínio de editor, consulte [este como fazê-lo.](howto-configure-publisher-domain.md)

## <a name="limitations"></a>Limitações

A nova experiência tem as seguintes limitações:

-   A nova experiência ainda não suporta inscrições de App para inquilinos Azure AD B2C.

-   A nova experiência ainda não suporta aplicações Live SDK criadas com contas pessoais da Microsoft.

-   A alteração do valor das contas suportadas não é suportada na UI. Você precisa usar o manifesto da aplicação a menos que você \' esteja trocando entre Azure AD single-tenant e multi-inquilino.

   > [!NOTE]
   > Se você é um utilizador pessoal da conta da Microsoft no inquilino Azure AD, e o administrador inquilino tem acesso restrito ao portal Azure, você pode obter um acesso negado. No entanto, se você vier através do atalho digitando registos de App na barra de pesquisa ou fixando-o, você será capaz de aceder à nova experiência.

## <a name="next-steps"></a>Próximos passos

Para começar com a nova experiência de registo de aplicações, consulte [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft.](quickstart-register-app.md)
