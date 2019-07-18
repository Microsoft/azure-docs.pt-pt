---
title: O que é o Azure Active Directory B2C? | Microsoft Docs
description: Saiba como criar e gerenciar experiências de identidade, como entrada de inscrição e gerenciamento de perfil em seu aplicativo usando Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ca9d8a8373bd73d527862864d436319eb45b5f48
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227175"
---
# <a name="what-is-azure-active-directory-b2c"></a>O que é o Azure Active Directory B2C?

O Azure Active Directory (Azure AD) B2C é um serviço de gerenciamento de identidade entre consumidores. Esse serviço permite que você personalize e controle como os usuários interagem com segurança com seus aplicativos Web, de área de trabalho, móveis ou de página única. Usando Azure AD B2C, os usuários podem se inscrever, entrar, redefinir senhas e editar perfis. Azure AD B2C implementa uma forma do OpenID Connect e dos protocolos OAuth 2,0. A chave importante na implementação desses protocolos são os tokens de segurança e suas declarações que permitem que você forneça acesso seguro aos recursos.

Uma *jornada do usuário* é uma solicitação que especifica uma política, que controla o comportamento de como o usuário e seu aplicativo interagem com Azure ad B2C. Dois caminhos estão disponíveis para a definição de percursos do usuário no Azure AD B2C.

Se você for um desenvolvedor de aplicativos com ou sem conhecimento de identidade, poderá optar por definir fluxos de usuário de identidade comum usando o portal do Azure. Se você for um profissional de identidade, integrador de sistemas, consultor ou em uma equipe de identidade interna, estiver familiarizado com os fluxos do OpenID Connect e entender os provedores de identidade e a autenticação baseada em declarações, você poderá escolher políticas personalizadas baseadas em XML.

Antes de começar a definir um percurso do usuário, você precisa criar um locatário Azure AD B2C e registrar seu aplicativo e a API no locatário. Depois de concluir essas tarefas, você pode começar a definir uma jornada do usuário com fluxos de usuário ou políticas personalizadas. Opcionalmente, você também pode adicionar ou alterar os provedores de identidade ou personalizar a forma com que o usuário passa a jornada.

## <a name="protocols-and-tokens"></a>Protocolos e tokens

O Azure AD B2C dá suporte ao [OpenID Connect e aos protocolos OAuth 2,0](active-directory-b2c-reference-protocols.md) para viagens do usuário. Na implementação do OpenID Connect do Azure AD B2C, a sua aplicação começa o percurso do utilizador através da emissão de pedidos de autenticação para o Azure AD B2C.

O resultado de uma solicitação para Azure AD B2C é um token de segurança, como um token de [ID ou token de acesso](active-directory-b2c-reference-tokens.md). Esse token de segurança define a identidade do usuário. Os tokens são recebidos de pontos de extremidade de Azure ad B2C, `/token` como `/authorize` um ou Endpoint. A partir desses tokens, você pode acessar declarações que podem ser usadas para validar uma identidade e permitir o acesso a recursos seguros.

## <a name="tenants-and-applications"></a>Locatários e aplicativos

No Azure AD B2C, um *locatário* representa sua organização e é um diretório de usuários. Cada inquilino do Azure AD B2C é distinto e independente dos outros inquilinos do Azure AD B2C. Talvez você já tenha um locatário Azure Active Directory, o locatário Azure AD B2C é um locatário diferente. Um locatário contém informações sobre os usuários que se inscreveram para usar seu aplicativo. Por exemplo, palavras-passe, dados de perfil e permissões. Para obter mais informações, [consulte Tutorial: Criar um locatário](tutorial-create-tenant.md)Azure Active Directory B2C.

Antes de configurar seu aplicativo para usar Azure AD B2C, primeiro você precisa registrá-lo no locatário usando o portal do Azure. O processo de registo recolhe e atribui valores à sua aplicação. Esses valores incluem uma ID de aplicativo que identifica exclusivamente o aplicativo e um URI de redirecionamento que é usado para direcionar as respostas de volta para o aplicativo.

A interação de cada aplicação segue um padrão de alto nível semelhante:

1. O aplicativo direciona o usuário para executar uma política.
2. O utilizador conclui a política de acordo com a sua definição.
3. O aplicativo recebe um token.
4. O aplicativo usa o token para tentar acessar um recurso.
5. O servidor de recursos valida o token para verificar se o acesso pode ser concedido.
6. O aplicativo atualiza periodicamente o token.

Para registrar um aplicativo Web, conclua as etapas [no tutorial: Registre um aplicativo para habilitar a inscrição e a entrada usando Azure AD B2C](tutorial-register-applications.md). Você também pode [Adicionar um aplicativo de API Web ao seu locatário Azure Active Directory B2C](add-web-application.md) ou [Adicionar um aplicativo cliente nativo ao seu locatário Azure Active Directory B2C](add-native-application.md).

## <a name="user-journeys"></a>Viagens do usuário

A política em uma jornada do usuário pode ser definida como um [fluxo de usuário](active-directory-b2c-reference-policies.md) ou uma [política personalizada](active-directory-b2c-overview-custom.md). Os fluxos de usuário predefinidos para as tarefas de identidade mais comuns, como inscrição, entrada e edição de perfil, estão disponíveis no portal do Azure.

As viagens do usuário permitem controlar comportamentos definindo as seguintes configurações:

- Contas sociais que o usuário usa para se inscrever no aplicativo
- Dados coletados do usuário, como primeiro nome ou CEP
- Multi-Factor Authentication
- Aparência das páginas
- Informações devolvidas à aplicação

Políticas personalizadas são arquivos de configuração que definem o comportamento da [estrutura de experiência de identidade](trustframeworkpolicy.md) em seu locatário Azure ad B2C. A estrutura de experiência de identidade é a plataforma subjacente que estabelece a relação de confiança de várias partes e conclui as etapas em um percurso do usuário.

As políticas personalizadas podem ser alteradas para realizar muitas tarefas. Uma política personalizada é representada como um ou vários ficheiros com formatação XML que fazem referência entre si numa cadeia hierárquica. Um [pacote de início](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) está disponível para políticas personalizadas para habilitar tarefas de identidade comuns.

Políticas personalizadas ou fluxos de usuário de diferentes tipos são usados em seu locatário Azure AD B2C conforme necessário e podem ser reutilizados entre aplicativos. Essa flexibilidade permite que você defina e modifique experiências de identidade do usuário com mínimo ou nenhuma alteração no seu código. As políticas são utilizadas ao adicionar um parâmetro de consulta especial a pedidos de autenticação HTTP. Para criar sua própria política personalizada, consulte Introdução [às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="identity-providers"></a>Fornecedores de identidade

Em seus aplicativos, talvez você queira permitir que os usuários entrem com diferentes provedores de identidade. Um *provedor de identidade* cria, mantém e gerencia informações de identidade ao fornecer serviços de autenticação aos aplicativos. Você pode adicionar provedores de identidade que têm suporte pelo Azure AD B2C usando o portal do Azure.

Normalmente, você usa apenas um provedor de identidade em seu aplicativo, mas tem a opção de adicionar mais. Para configurar um provedor de identidade em seu locatário do Azure AD B2C, primeiro crie um aplicativo no site do desenvolvedor do provedor de identidade e, em seguida, registre o identificador do aplicativo ou o identificador do cliente e a senha ou o segredo do cliente do provedor de identidade aplicativo que você cria. Em seguida, este identificador e palavra-passe são utilizados para configurar a sua aplicação.

Os artigos a seguir descrevem as etapas para adicionar alguns dos provedores de identidade comuns aos fluxos de usuário:

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Conta Microsoft](active-directory-b2c-setup-msa-app.md)

Os artigos a seguir descrevem as etapas para adicionar alguns dos provedores de identidade comuns a políticas personalizadas:
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Conta Microsoft](active-directory-b2c-custom-setup-msa-idp.md)

Para obter mais informações, [consulte Tutorial: Adicione provedores de identidade aos seus aplicativos no](tutorial-add-identity-providers.md)Azure Active Directory B2C.


## <a name="page-customization"></a>Personalização de página

A maior parte do conteúdo HTML e CSS que é apresentada aos clientes em uma jornada do usuário é controlável. Usando a personalização de página, você pode personalizar a aparência de qualquer política personalizada ou fluxo de usuário. Pode manter a consistência visual e de marca entre a sua aplicação e o Azure AD B2C através desta funcionalidade de personalização.

Azure AD B2C executa o código no navegador do usuário e usa uma abordagem moderna chamada CORS (compartilhamento de recursos entre origens). Em primeiro lugar, especifique um URL numa política com conteúdo HTML personalizado. Azure AD B2C mescla os elementos da interface do usuário com o conteúdo HTML que é carregado de sua URL e, em seguida, exibe a página para o usuário.

Pode enviar parâmetros para o Azure AD B2C numa cadeia de consulta. Ao transmitir o parâmetro para o ponto final HTML, o conteúdo da página é alterado dinamicamente. Por exemplo, você pode alterar a imagem de plano de fundo na página de inscrição ou entrada com base em um parâmetro que você passa de seu aplicativo Web ou móvel.

Para personalizar páginas em um fluxo de usuário, [consulte Tutorial: Personalize a interface de experiências do usuário no](tutorial-customize-ui.md)Azure Active Directory B2C. Para personalizar as páginas em uma política personalizada, consulte [Personalizar a interface do usuário do seu aplicativo usando uma política personalizada em Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md) ou [Configurar a interface com o conteúdo dinâmico usando políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="developer-resources"></a>Recursos para desenvolvedores

### <a name="client-applications"></a>Aplicações de cliente

Você tem a opção de aplicativos para [Ios](active-directory-b2c-devquickstarts-ios.md), [Android](active-directory-b2c-devquickstarts-android.md)e .net, entre outros. Azure AD B2C habilita essas ações enquanto protege suas identidades de usuário ao mesmo tempo.

Se você for um desenvolvedor de aplicativos Web ASP.net, configure seu aplicativo para autenticar contas usando as etapas [no tutorial: Habilite um aplicativo Web para autenticar com contas](active-directory-b2c-tutorials-web-app.md)usando Azure ad B2C.

Se você for um desenvolvedor de aplicativos de área de trabalho, configure seu aplicativo para autenticar contas [usando as etapas no tutorial: Habilite um aplicativo de área de trabalho para autenticar com contas usando Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Se você for um desenvolvedor de aplicativo de página única usando o Node. js, configure seu aplicativo para autenticar contas usando as [etapas no tutorial: Habilite um aplicativo de página única para autenticar com contas](active-directory-b2c-tutorials-spa.md)usando Azure ad B2C.

### <a name="apis"></a>APIs
Se os aplicativos cliente ou Web precisarem chamar APIs, você poderá configurar o acesso seguro a esses recursos no Azure AD B2C.

Se você for um desenvolvedor de aplicativos Web ASP.net, configure seu aplicativo para chamar uma API protegida usando as etapas no [tutorial: Conceder acesso a uma API Web do ASP.NET usando](active-directory-b2c-tutorials-web-api.md)Azure Active Directory B2C.

Se você for um desenvolvedor de aplicativos de área de trabalho, configure seu aplicativo para chamar uma API protegida usando [as etapas no tutorial: Conceder acesso a uma API Web node. js de um aplicativo de desktop usando](active-directory-b2c-tutorials-desktop-app-webapi.md)Azure Active Directory B2C.

Se você for um desenvolvedor de aplicativo de página única usando o Node. js, configure seu aplicativo para autenticar contas usando as [etapas no tutorial: Conceder acesso a uma API da Web do ASP.NET Core de um aplicativo de página única](active-directory-b2c-tutorials-spa-webapi.md)usando Azure Active Directory B2C.

### <a name="javascript"></a>JavaScript

Você pode adicionar seu próprio código do lado do cliente JavaScript a seus aplicativos no Azure AD B2C. Para configurar o JavaScript em seu aplicativo, você define um [layout de página](page-layout.md) e habilita o [JavaScript](javascript-samples.md) em seus fluxos de usuário ou políticas personalizadas.

### <a name="user-accounts"></a>Contas de utilizador

Muitas tarefas comuns de gerenciamento de locatários precisam ser executadas programaticamente. Um exemplo principal é o gerenciamento de usuários. Talvez seja necessário migrar um repositório de usuários existente para um locatário Azure AD B2C. Talvez você queira hospedar o registro de usuário em sua própria página e criar contas de usuário em seu diretório Azure AD B2C nos bastidores. Esses tipos de tarefas exigem a capacidade de criar, ler, atualizar e excluir contas de usuário. Você pode executar essas tarefas usando o [API do Graph do Azure ad](active-directory-b2c-devquickstarts-graph-dotnet.md).

## <a name="next-steps"></a>Passos Seguintes

Inicie a configuração da sua aplicação para a experiência de inscrição e de início de sessão ao avançar para o tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Criar um locatário Azure Active Directory B2C](tutorial-create-tenant.md)
