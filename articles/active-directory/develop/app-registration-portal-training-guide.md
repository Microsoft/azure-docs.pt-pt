---
title: Nova experiência de registos de aplicações do portal do Azure
titleSuffix: Microsoft identity platform
description: Uma introdução à nova experiência de registo da App no portal Azure
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 50c88dd1785bd9177219054fed3800ca725a5274
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154598"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>A nova experiência de registo de aplicativos do portal Azure

Existem [muitas melhorias](https://go.microsoft.com/fwlink/?linkid=2083908) na experiência de registos da nova App no portal Azure. Se estiver mais familiarizado com a experiência do portal de registo de aplicações (apps.dev.microsoft.com) para registar ou gerir aplicações convergentes, referida como a experiência antiga, este guia de formação vai começar a usar a nova experiência.

## <a name="whats-not-changing"></a>O que não está a mudar?

- As suas aplicações e configurações relacionadas podem ser encontradas como está na nova experiência. Não precisa de voltar a registar as aplicações e os utilizadores das suas aplicações não precisarão de voltar a inscrever-se.

    > [!NOTE]
    > Tem de iniciar sessão com a conta que usou para registar as candidaturas para as encontrar no portal Azure. Recomendamos que verifique se o utilizador assinado no portal Azure corresponde ao utilizador que foi assinado no portal de registo de aplicações, comparando o endereço de e-mail do seu perfil.
    >
    > Em alguns casos, especialmente quando se inscreveu na utilização de contas pessoais da Microsoft (por exemplo, Outlook, Live, Xbox, etc.) com um endereço de e-mail Azure AD, descobrimos que quando se vai ao portal Azure a partir da experiência antiga, assina-o numa conta diferente com o mesmo e-mail no seu inquilino DaD Azure. Se ainda acredita que faltam candidaturas, assine e inscreva-se na conta certa.

- As aplicações Live SDK criadas com contas pessoais da Microsoft ainda não são suportadas no portal Azure e continuarão a manter-se na experiência antiga num futuro próximo.

## <a name="key-changes"></a>Alterações-chave

-   Na experiência antiga, as aplicações foram registadas por padrão como aplicações convergentes que suportam todas as contas organizacionais (multitenant) bem como contas pessoais da Microsoft. Isto não poderia ser modificado através da experiência antiga, dificultando a criação de apps que suportassem apenas contas organizacionais (multiarrendatária ou inquilino único).
    A nova experiência permite-lhe registar aplicações que suportam todas essas opções. Saiba mais sobre os tipos de [aplicativos.](active-directory-v2-registration-portal.md)

-   Na nova experiência, se a sua conta pessoal da Microsoft também estiver num inquilino da AD Azure, você verá três separadores - todas as aplicações no inquilino, aplicações próprias no inquilino, bem como aplicações da sua conta pessoal. Portanto, se acredita que as aplicações registadas na sua conta pessoal da Microsoft estão em falta, verifique as **Aplicações a partir do seu** separador de conta pessoal.

-   Na nova experiência, pode facilmente alternar entre inquilinos navegando para o seu perfil e escolhendo diretório de switch.

## <a name="list-of-applications"></a>Lista de candidaturas

-   A nova lista de aplicações mostra aplicações que foram registadas através da experiência de registos de aplicações no portal Azure (aplicações que assinam apenas em contas AD Do Azure) bem como aplicações registadas no portal de registo de [aplicações](https://apps.dev.microsoft.com/) (aplicações que assinam tanto em Contas Azure AD como pessoais da Microsoft).

-   A nova lista de aplicações tem duas colunas adicionais: **Criadas na** coluna e **certificados &** coluna de segredos que mostra o estado (atual, expirando em breve ou expirado) de credenciais que foram registadas na app.

## <a name="new-app-registration"></a>Registo de nova aplicação

Na experiência antiga, para registar uma aplicação convergente, só foi obrigado a fornecer um Nome. As aplicações criadas foram registadas como aplicações convergentes que suportam todos os diretórios organizacionais (multitenant) bem como contas pessoais da Microsoft.  Isto não poderia ser modificado através da experiência antiga, dificultando a criação de apps que suportassem apenas contas organizacionais (multiarrendatária ou inquilino único). [Saiba mais sobre tipos de conta suportados](v2-supported-account-types.md)

Na nova experiência, deve fornecer um Nome para a app e escolher os tipos de conta Suportada. Você pode opcionalmente fornecer um URI redirecionado.
Se fornecer um URI redirecionado, terá de especificar se é web/público (nativo/móvel e ambiente de trabalho). Para obter mais informações sobre como registar uma aplicação utilizando a experiência de registos de novas aplicações, consulte [este arranque rápido](quickstart-register-app.md).

## <a name="app-management-page"></a>Página de gestão de aplicativos

A experiência antiga tinha uma única página de gestão de aplicações para aplicações convergentes com as seguintes secções: Propriedades, segredos de aplicação, plataformas, proprietários, permissões de gráficos da Microsoft, perfil e opções avançadas.

A nova experiência no portal Azure representa estas funcionalidades em páginas separadas. Aqui é onde você pode encontrar a funcionalidade equivalente:

-   Propriedades - Identificação de Nome e Aplicação está na página 'Visão Geral'.

-   Os Segredos de Aplicação estão na página de Certificados & segredos

-   A configuração das plataformas está na página de Autenticação

-   As permissões do Microsoft Graph estão na página de permissões da API juntamente com outras permissões

-   O perfil está na página de Branding

-   Opção avançada - Suporte Ao Vivo SDK está na página de Autenticação.

## <a name="application-secretscertificates--secrets"></a>Segredos de aplicação/Certificados & segredos

Na nova experiência, os **segredos** da aplicação foram renomeados para **Certificados & segredos.** Além disso, **as chaves públicas** são referidas como **Certificados** e **Senhas** são referidas como **segredos do Cliente**. Optámos por não trazer esta funcionalidade para a nova experiência por razões de segurança, pelo que já não se pode gerar um novo par de chaves.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Plataformas/Autenticação: URIs de resposta/redirecionamento
Na experiência antiga, uma aplicação tinha a secção De Plataformas para Web, native e Web API para configurar URLs Redirecionamento, URL de Logout e fluxo implícito.

Na nova experiência, os URLs de\'Resposta podem ser encontrados numa secção de Autenticação da aplicação. Além disso, são referidos como URIs redireccionadores e o formato para redirecionamento de URIs mudou. São obrigados a associar-se a um tipo de aplicação (web ou cliente público - mobile e desktop). [Mais informações](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

As APIs web são configuradas em Expor uma página API.

> [!NOTE]
> Experimente a nova experiência de definições de Autenticação onde pode configurar as definições para a sua aplicação com base na plataforma ou dispositivo que pretende atingir. [Mais informações](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Permissões/Permissões API do Microsoft Graph

-   Ao selecionar um API na experiência antiga, pode escolher apenas entre apis do Microsoft Graph. Na nova experiência, pode escolher entre muitas APIs da Microsoft, incluindo o Microsoft Graph, APIs da sua organização e as suas APIs, isto é apresentado em três separadores: Microsoft APIs, APIs que a minha organização utiliza, ou My APIs. A barra de pesquisa em APIs a minha organização usa pesquisas de separadores através de diretores de serviço no inquilino.

    > [!NOTE]
    > Não verá esta conta se o seu pedido não estiver associado a um inquilino. Para obter mais informações sobre como solicitar permissões utilizando a nova experiência, consulte [este arranque rápido](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md).

-   A velha experiência não tinha um botão de **permissões Grant.** Na nova experiência, há uma secção de consentimento grant com um botão de **consentimento de administrador grant** na secção de permissões API de uma aplicação. Apenas um administrador pode conceder o consentimento e este botão está habilitado apenas para administradores. Quando um administrador seleciona o botão de consentimento do **administrador Grant,** o consentimento do administrador é concedido a todas as permissões solicitadas.

## <a name="profile"></a>Perfil
Na experiência antiga, o Profile tinha logotipo, URL de página inicial, URL de serviço e configuração url de declaração de privacidade. Na nova experiência, estes podem ser encontrados na página branding.

## <a name="application-manifest"></a>Manifesto de aplicação
Na nova experiência, a página Manifesto permite-lhe editar e atualizar os atributos da app. Para mais informações, consulte [O manifesto da Aplicação.](reference-app-manifest.md)

## <a name="new-ui"></a>Nova UI
Há novos UI para propriedades que anteriormente só podiam ser definidas usando o editor manifesto ou a API, ou não existiam.

-   O fluxo implícito de subvenção (oauth2AllowImplicitFlow) pode ser encontrado na página de Autenticação. Ao contrário da experiência antiga, você pode ativar fichas de acesso ou fichas de identificação, ou ambos.

-   Os âmbitos definidos por esta API (oauth2Permissions) e aplicações autorizadas do cliente (pré-Aplicações autorizadas) podem ser configurados através da página Expor uma API. Para obter mais informações sobre como configurar uma aplicação para ser uma API web e expor permissões/âmbitos, consulte [este arranque rápido](quickstart-configure-app-expose-web-apis.md).

-   O domínio da editora (que é apresentado aos utilizadores no pedido de [consentimento da\'aplicação)](application-consent-experience.md)pode ser encontrado na página da lâmina branding. Para obter mais informações sobre como configurar um domínio de editor, consulte [este como .](howto-configure-publisher-domain.md)

## <a name="limitations"></a>Limitações

A nova experiência tem as seguintes limitações:

-   A nova experiência ainda não suporta inscrições de Aplicativos para inquilinos Do Azure AD B2C.

-   A nova experiência ainda não suporta aplicações Live SDK criadas com contas pessoais da Microsoft.

-   A alteração do valor das contas apoiadas não é suportada na UI. Você precisa usar o manifesto\'da aplicação a menos que você esteja trocando entre Azure AD single-tenant e multi-inquilino.

   > [!NOTE]
   > Se é um utilizador pessoal da conta da Microsoft no inquilino da AD Azure, e o administrador do inquilino tem acesso restrito ao portal Azure, poderá obter um acesso negado. No entanto, se passar pelo atalho digitando as inscrições da App na barra de pesquisa ou fixando-a, poderá aceder à nova experiência.
