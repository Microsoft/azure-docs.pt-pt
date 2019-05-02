---
title: O que é o Azure Active Directory B2C? | Microsoft Docs
description: Saiba mais sobre como criar e gerir experiências de identidade, como o início de sessão inscrição e gestão de perfis na sua aplicação com o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 33c6f9e06439ebeb17608aa374532ac4f7a020cf
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701363"
---
# <a name="what-is-azure-active-directory-b2c"></a>O que é o Azure Active Directory B2C?

O Azure Active Directory (Azure AD) B2C é um serviço de gestão de identidades de empresa-consumidor. Este serviço permite-lhe personalizar e controlar como os utilizadores com segurança interagem com a web, área de trabalho, aplicações móveis ou de página única. Com o Azure AD B2C, os utilizadores podem inscrever-se, iniciar sessão, repor palavras-passe e editar perfis de. O Azure AD B2C implementa uma forma dos protocolos de OpenID Connect e OAuth 2.0. A chave importante na implementação desses protocolos é os tokens de segurança e suas declarações que permitem fornecer acesso seguro aos recursos.

R *percurso do utilizador* é um pedido que especifique uma política, que controla o comportamento de como o utilizador e a sua aplicação interagem com o Azure AD B2C. Dois caminhos estão disponíveis para que possa definir jornadas de utilizador no Azure AD B2C. 

Se for um programador de aplicações com ou sem conhecimentos de identidade, pode optar por definir fluxos de utilizador de identidade comuns com o portal do Azure. Se é um profissional de identidade, integrador de sistemas, consultor, ou numa equipe interna de identidade, se sente confortável com OpenID Connect fluxos e compreende fornecedores de identidade e autenticação baseada em afirmações, pode escolher políticas personalizadas com base em XML.

Antes de começar a definir um percurso do utilizador, terá de criar um inquilino do Azure AD B2C e registar a aplicação e a API no inquilino. Depois de concluir estas tarefas, pode começar a utilizar a definição de um percurso do utilizador com fluxos de utilizador ou políticas personalizadas. Também, opcionalmente, pode adicionar ou alterar os fornecedores de identidade ou personalizar a forma como a jornada experiências de utilizador.

## <a name="protocols-and-tokens"></a>Protocolos e tokens

O Azure AD B2C suporta o [protocolos OpenID Connect e OAuth 2.0](active-directory-b2c-reference-protocols.md) para jornadas de utilizador. Na implementação do OpenID Connect do Azure AD B2C, a sua aplicação começa o percurso do utilizador através da emissão de pedidos de autenticação para o Azure AD B2C. 

O resultado de um pedido para o Azure AD B2C é um token de segurança, tal como um [token de ID ou o token de acesso](active-directory-b2c-reference-tokens.md). Este token de segurança define a identidade do utilizador. Tokens são recebidos a partir de pontos finais do Azure AD B2C, por exemplo, um `/token` ou `/authorize` ponto final. Destes tokens, pode acessar afirmações que podem ser utilizadas para validar uma identidade e permitir o acesso proteger os recursos.

## <a name="tenants-and-applications"></a>Inquilinos e aplicações

No Azure AD B2C, um *inquilino* representa a sua organização e é um diretório de utilizadores. Cada inquilino do Azure AD B2C é distinto e independente dos outros inquilinos do Azure AD B2C. Pode já ter um inquilino do Azure Active Directory, o inquilino do Azure AD B2C é um inquilino diferente. Um inquilino contém informações sobre os utilizadores que tiver optado por utilizar a aplicação. Por exemplo, palavras-passe, dados de perfil e permissões. Para obter mais informações, consulte [Tutorial: Criar um inquilino do Azure Active Directory B2C](tutorial-create-tenant.md).

Antes de configurar a sua aplicação para utilizar o Azure AD B2C, terá primeiro de registá-lo no inquilino com o portal do Azure. O processo de registo recolhe e atribui valores à sua aplicação. Esses valores incluem um ID de aplicação que identifica exclusivamente o aplicativo e um URI de redirecionamento que é utilizada para direcionar as respostas de volta para a aplicação.

A interação de cada aplicação segue um padrão de alto nível semelhante:

1. A aplicação direciona o utilizador executar uma política.
2. O utilizador conclui a política de acordo com a sua definição.
3. O aplicativo recebe um token.
4. A aplicação utiliza o token para tentar aceder a um recurso.
5. O servidor de recurso valida o token para verificar que pode ser concedido acesso.
6. A aplicação atualiza periodicamente o token.

Para registar uma aplicação web, conclua os passos no [Tutorial: Registar uma aplicação para ativar a inscrição e início de sessão com o Azure AD B2C](tutorial-register-applications.md). Também pode [adicionar uma web de aplicação de API com o seu inquilino do Azure Active Directory B2C](add-web-application.md) ou [adicionar uma aplicação cliente nativa com o seu inquilino do Azure Active Directory B2C](add-native-application.md).

## <a name="user-journeys"></a>Jornadas de utilizador

A política no percurso do utilizador pode ser definida como um [fluxo de utilizador](active-directory-b2c-reference-policies.md) ou uma [política personalizada](active-directory-b2c-overview-custom.md). Predefinidos, tais como os fluxos de utilizador para tarefas mais comuns de identidade, inscrição, início de sessão e edição de perfis, estão disponíveis no portal do Azure.

Jornadas de utilizador permitem-lhe controlar os comportamentos configurando as seguintes definições:

- Contas de redes sociais que o utilizador utiliza para se inscrever para a aplicação
- Dados recolhidos do usuário como nome próprio ou código postal
- Multi-Factor Authentication
- Aspeto e funcionalidade de páginas
- Informações devolvidas à aplicação

As políticas personalizadas são ficheiros de configuração que definem o comportamento do [arquitetura de experiências de identidade](trustframeworkpolicy.md) no seu inquilino do Azure AD B2C. O Framework de experiência de identidade é a plataforma subjacente, que estabelece a fidedignidade de múltiplos intervenientes e conclua os passos no percurso do utilizador. 

As políticas personalizadas podem ser alteradas para realizar muitas tarefas. Uma política personalizada é representada como um ou vários ficheiros com formatação XML que fazem referência entre si numa cadeia hierárquica. R [pacote de iniciante](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) está disponível para as políticas personalizadas ativar tarefas comuns de identidade. 

As políticas personalizadas ou fluxos de utilizador de diferentes tipos são utilizados no seu inquilino do Azure AD B2C, conforme necessário e podem ser reutilizados em todas as aplicações. Essa flexibilidade permite-lhe definir e modificar experiências de identidade do utilizador mínima com ou sem alterações ao seu código. As políticas são utilizadas ao adicionar um parâmetro de consulta especial a pedidos de autenticação HTTP. Para criar sua própria política personalizada, veja [introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="identity-providers"></a>Fornecedores de identidade 

Nas suas aplicações, pode querer permitir que os utilizadores iniciem sessão com diferentes provedores de identidade. Uma *fornecedor de identidade* cria, mantém e gerencia as informações de identidade, fornecendo serviços de autenticação para aplicações. Pode adicionar fornecedores de identidade que são suportadas pelo Azure AD B2C no portal do Azure. 

Geralmente usa apenas um fornecedor de identidade em seu aplicativo, mas tem a opção para adicionar mais. Para configurar um fornecedor de identidade no seu inquilino do Azure AD B2C, primeiro crie uma aplicação no site de desenvolvedor do fornecedor de identidade e, em seguida, registe o identificador da aplicação ou o identificador de cliente e a palavra-passe ou o cliente secreta do fornecedor de identidade aplicações que criar. Em seguida, este identificador e palavra-passe são utilizados para configurar a sua aplicação. 

Os seguintes artigos descrevem os passos para adicionar alguns dos fornecedores de identidade comum para fluxos de utilizador:

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Conta Microsoft](active-directory-b2c-setup-msa-app.md)

Os seguintes artigos descrevem os passos para adicionar alguns dos fornecedores de identidade comum para as políticas personalizadas:
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Conta Microsoft](active-directory-b2c-custom-setup-msa-idp.md)

Para obter mais informações, consulte [Tutorial: Adicionar fornecedores de identidade às suas aplicações no Azure Active Directory B2C](tutorial-add-identity-providers.md).


## <a name="page-customization"></a>Personalização de página

A maioria do conteúdo HTML e CSS que é apresentada aos clientes num percurso do utilizador é controlável. Ao utilizar a personalização de página, pode personalizar o aspeto e funcionalidade de qualquer fluxo de utilizador ou de política personalizado. Pode manter a consistência visual e de marca entre a sua aplicação e o Azure AD B2C através desta funcionalidade de personalização. 

O Azure AD B2C executa o código no navegador do usuário e usa uma abordagem moderna chamada Cross-Origin Resource Sharing (CORS). Em primeiro lugar, especifique um URL numa política com conteúdo HTML personalizado. O Azure AD B2C mescla os elementos de interface do usuário com o conteúdo HTML que são carregados a partir do URL e, em seguida, exibe a página para o usuário.

Pode enviar parâmetros para o Azure AD B2C numa cadeia de consulta. Ao transmitir o parâmetro para o ponto final HTML, o conteúdo da página é alterado dinamicamente. Por exemplo, alterar a imagem de fundo da página de inscrição ou início de sessão com base num parâmetro que passar de seu aplicativo web ou móvel.

Para personalizar as páginas num fluxo de utilizador, veja [Tutorial: Personalizar a interface de experiências de utilizador no Azure Active Directory B2C](tutorial-customize-ui.md). Para personalizar páginas numa política personalizada, veja [personalizar a interface de utilizador da sua aplicação utilizando uma política personalizada no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md) ou [configurar a interface do Usuário com conteúdo dinâmico ao utilizar políticas personalizadas no Azure Do Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="developer-resources"></a>Recursos para programadores

### <a name="client-applications"></a>Aplicações de cliente

Tem a opção de aplicações para [iOS](active-directory-b2c-devquickstarts-ios.md), [Android](active-directory-b2c-devquickstarts-android.md)e o .NET, entre outros. O Azure AD B2C permite estas ações ao proteger as identidades de utilizador ao mesmo tempo.

Se for um programador de aplicações web do ASP.NET, configure a sua aplicação para autenticar contas utilizando os passos no [Tutorial: Ativar uma aplicação web autenticar com contas através do Azure AD B2C](active-directory-b2c-tutorials-web-app.md).

Se for um desenvolvedor de aplicativo de desktop, configure a sua aplicação para autenticar contas utilizando os passos no [Tutorial: Ativar um aplicativo de desktop para se autenticar com contas através do Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Se for um desenvolvedor de aplicativo de página única com node. js, configure a sua aplicação para autenticar contas utilizando os passos no [Tutorial: Ativar uma aplicação de página única para autenticar com contas através do Azure AD B2C](active-directory-b2c-tutorials-spa.md).

### <a name="apis"></a>APIs
Se precisam de seus aplicativos web ou de cliente chamar as APIs, pode configurar o acesso seguro a esses recursos no Azure AD B2C.

Se for um programador de aplicações web do ASP.NET, configure a sua aplicação para chamar uma API protegida utilizando os passos no [Tutorial: Conceder acesso a uma API através do Azure Active Directory B2C de web do ASP.NET](active-directory-b2c-tutorials-web-api.md).

Se for um desenvolvedor de aplicativo de desktop, configure a sua aplicação para chamar uma API protegida utilizando os passos no [Tutorial: Conceder acesso a uma API da web de node. js a partir de uma aplicação de ambiente de trabalho através do Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app-webapi.md).

Se for um desenvolvedor de aplicativo de página única com node. js, configure a sua aplicação para autenticar contas utilizando os passos no [Tutorial: Conceder acesso a uma API web ASP.NET Core a partir de uma aplicação de página única com o Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md).

### <a name="javascript"></a>JavaScript

Pode adicionar o seu próprio código do lado do cliente JavaScript às suas aplicações no Azure AD B2C. Para configurar o JavaScript em seu aplicativo, define um [contrato da página](page-contract.md) e ative [JavaScript](javascript-samples.md) nos seus fluxos de utilizador ou as políticas personalizadas.

### <a name="user-accounts"></a>Contas de utilizador

Muitas tarefas de gestão comuns do inquilino precisam ser executadas por meio de programação. Um principal exemplo é a gestão de utilizadores. Poderá ter de migrar um arquivo de utilizador existente a um inquilino do Azure AD B2C. Pode querer alojar o registo de utilizador na sua própria página e criar contas de utilizador no seu diretório do Azure AD B2C em segundo plano. Esses tipos de tarefas exigem a capacidade de criar, ler, atualizar e eliminar contas de utilizador. Pode realizar estas tarefas, utilizando o [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

## <a name="next-steps"></a>Passos Seguintes

Inicie a configuração da sua aplicação para a experiência de inscrição e de início de sessão ao avançar para o tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Criar um inquilino do Azure Active Directory B2C](tutorial-create-tenant.md)
