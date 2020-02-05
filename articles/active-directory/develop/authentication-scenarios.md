---
title: Autenticação na plataforma Microsoft Identity | Azure
description: Saiba mais sobre os conceitos básicos de autenticação na plataforma Microsoft Identity (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 841ff610509c0d580ff8dca3a9fc14b816d56f1c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983227"
---
# <a name="authentication-basics"></a>Noções básicas sobre autenticação

## <a name="what-is-authentication"></a>O que é autenticação

Este artigo aborda muitos dos conceitos de autenticação que você precisará entender para criar aplicativos Web protegidos, APIs da Web ou aplicativos que chamam APIs da Web protegidas. Se vir um termo que não conhece, experimente o nosso (glossário)[glossary.md].

A **autenticação** é o processo de provar que você é quem diz que está. Por vezes, o termo é abreviado como AuthN.

A **autorização** é o ato de conceder uma permissão de parte autenticada para fazer algo. Ele especifica quais dados você tem permissão para acessar e o que você pode fazer com esses dados. Por vezes, o termo é abreviado como AuthZ.

Em vez de criar aplicativos que mantêm suas próprias informações de nome de usuário e senha, o que provoca uma alta carga administrativa quando você precisa adicionar ou remover usuários em vários aplicativos, os aplicativos podem delegar essa responsabilidade a um provedor de identidade centralizado.

O Azure Active Directory (AD do Azure) é um provedor de identidade centralizado na nuvem. A delegação de autenticação e autorização para ela permite cenários como políticas de acesso condicional que exigem que um usuário esteja em um local específico, o uso da autenticação multifator, bem como a habilitação de um usuário a entrar uma vez e ser automaticamente conectado a todos os aplicativos Web que compartilham o mesmo diretório centralizado. Esse recurso é conhecido como SSO (logon único).

Um provedor de identidade centralizado é ainda mais importante para aplicativos que têm usuários localizados em todo o mundo que não necessariamente se conectam da rede da empresa. O Azure AD autentica usuários e fornece tokens de acesso. Um [sinal de acesso](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token) é um símbolo de segurança que é emitido por um servidor de autorização. Contém informações sobre o utilizador e a aplicação para a qual se destina o símbolo; que podem ser usados para aceder a APIs web e outros recursos protegidos.

A plataforma de identidade da Microsoft simplifica a autenticação para os desenvolvedores de aplicações, fornecendo identidade como serviço, com suporte para protocolos padrão da indústria, como [OAuth 2.0](https://oauth.net/2/) e [OpenID Connect,](https://openid.net/connect/)bem como bibliotecas de código aberto para diferentes plataformas para ajudá-lo a começar a codificar rapidamente. Permite que os desenvolvedores construam aplicações que assinam todas as identidades da Microsoft, obter fichas para ligar para o [Microsoft Graph](https://developer.microsoft.com/graph/), outras APIs da Microsoft ou APIs que os desenvolvedores construíram. Para obter mais informações, consulte [evolução da plataforma de identidade da Microsoft](about-microsoft-identity-platform.md).

### <a name="tenants"></a>locatários

Um provedor de identidade de nuvem atende A muitas organizações. Para manter os usuários de diferentes organizações separadas, o Azure AD é particionado em locatários, com um locatário por organização.

Os locatários controlam os usuários e seus aplicativos associados. A plataforma Microsoft Identity também oferece suporte a usuários que se conectam com contas pessoais da Microsoft.

A Azure AD também fornece o Azure Ative Directory B2C para que as organizações possam inscrever utilizadores, tipicamente clientes, usando identidades sociais como uma conta google. Para obter mais informações, consulte a [documentação do Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c) .

### <a name="security-tokens"></a>Fichas de segurança

Os tokens de segurança contêm informações sobre usuários e aplicativos. O Azure AD usa tokens baseados em JSON (JWTs) que contêm declarações.

Uma reclamação fornece afirmações sobre uma entidade, como uma [aplicação de cliente](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application) ou proprietário de [recursos,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner)a outra entidade, como um servidor de [recursos).](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server)

As alegações são pares de nome/valor que transmitem factos sobre o assunto simbólico. Por exemplo, uma reclamação pode conter factos sobre o diretor de segurança que foi autenticado pelo servidor de [autorização).](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server) As alegações presentes num dado sinal ão de penderá de muitas coisas, incluindo o tipo de ficha, o tipo de credencial usada para autenticar o sujeito, a configuração da aplicação, e assim por diante.

Os aplicativos podem usar declarações para várias tarefas, como:

* Validando o token
* Identificando o inquilino do sujeito simbólico
* Exibindo informações do usuário
* Determinando a autorização da entidade

Uma reivindicação consiste em pares de valor-chave que fornecem informações como:

* Servidor de Token de Segurança que gerou o símbolo
* Data em que o símbolo foi gerado
* Objeto (como o utilizador-- exceto para daemons)
* Público, que é a app para a qual o token foi gerado
* App (o cliente) que pediu o símbolo. No caso das aplicações web, este pode ser o mesmo que o público

Para obter informações mais detalhadas sobre a reclamação, consulte [fichas](access-tokens.md) de acesso e [fichas de identificação](id-tokens.md).

Cabe ao aplicativo para o qual o token foi gerado, o aplicativo Web que conectou o usuário ou a API Web que está sendo chamada, para validar o token. O token é assinado pelo servidor de token de segurança (STS) com uma chave privada. O STS publica a chave pública correspondente. Para validar um token, o aplicativo verifica a assinatura usando a chave pública do STS para validar que a assinatura foi criada usando a chave privada.

Os tokens são válidos somente por uma quantidade limitada de tempo. Normalmente, o STS fornece um par de tokens: um token de acesso para acessar o aplicativo ou recurso protegido e um token de atualização usado para atualizar o token de acesso quando o token de acesso está perto de expirar.

Tokens de acesso são passados para uma API da Web como o token de portador no cabeçalho `Authorization`. Um aplicativo pode fornecer um token de atualização para o STS e, se o acesso do usuário ao aplicativo não tiver sido revogado, ele obterá um novo token de acesso e um novo token de atualização. É assim que o cenário de alguém que sai da empresa é tratado. Quando o STS receber o token de atualização, ele não emitirá outro token de acesso válido se o usuário não estiver mais autorizado.

## <a name="application-model"></a>Modelo de aplicação

Os aplicativos podem conectar os próprios usuários ou delegar a entrada para um provedor de identidade. Consulte [fluxos de autenticação e cenários de aplicativo](authentication-flows-app-scenarios.md) para saber mais sobre cenários de entrada com suporte do Azure AD.

Para que um provedor de identidade saiba que um usuário tem acesso a um aplicativo específico, o usuário e o aplicativo devem ser registrados com o provedor de identidade. Ao registrar seu aplicativo com o Azure AD, você está fornecendo uma configuração de identidade para seu aplicativo que permite que ele se integre ao Azure AD. Registar a aplicação também lhe permite:

* Personalize a identidade visual do seu aplicativo na caixa de diálogo de entrada. Isso é importante porque essa é a primeira experiência que um usuário terá com seu aplicativo.
* Decida se deseja permitir que os usuários se conectem somente se eles pertencem à sua organização. Este é um aplicativo de locatário único. Ou permitir que os usuários entrem usando qualquer conta corporativa ou de estudante. Este é um aplicativo multilocatário. Você também pode permitir contas pessoais da Microsoft ou uma conta social do LinkedIn, do Google e assim por diante.
* permissões de escopo de solicitação. Por exemplo, você pode solicitar o escopo "User. Read", que concede permissão para ler o perfil do usuário conectado.
* Defina escopos que definem o acesso à sua API Web. Normalmente, quando um aplicativo deseja acessar sua API, ele precisa solicitar permissões para os escopos que você definir.
* Compartilhe um segredo com o Azure AD que comprova a identidade do aplicativo para o Azure AD.  Isso é relevante no caso em que o aplicativo é um aplicativo cliente confidencial. Um aplicativo cliente confidencial é um aplicativo que pode conter credenciais com segurança. Eles exigem um servidor de back-end confiável para armazenar as credenciais.

Uma vez registada, a aplicação receberá um identificador único que a app partilha com a Azure AD quando solicitar fichas. Se a aplicação for uma [aplicação confidencial](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application)do cliente, também partilhará o segredo ou a chave pública*-dependendo se foram usados certificados ou segredos.

A plataforma de identidade da Microsoft representa aplicativos que usam um modelo que atende a duas funções principais:

* Identifique a app pelos protocolos de autenticação que suporta
* Fornecer todos os identificadores, URLs, segredos e informações relacionadas que são necessárias para autenticar

A plataforma Microsoft Identity:

* Detém todos os dados necessários para suportar a autenticação em tempo de execução
* Detém todos os dados para decidir quais os recursos que uma app pode precisar de aceder, e em que circunstâncias um determinado pedido deve ser cumprido
* Fornece infraestrutura para implementação de oprovisionamento de aplicações dentro do inquilino do desenvolvedor de aplicações, e a qualquer outro inquilino da AD Azure
* Manipula o consentimento do usuário durante o tempo de solicitação de token e facilita o provisionamento dinâmico de aplicativos entre locatários

Consentimento é o processo de um proprietário de recurso que concede autorização para um aplicativo cliente acessar recursos protegidos, sob permissões específicas, em nome do proprietário do recurso. A plataforma Microsoft Identity:

* Permite aos utilizadores e administradores conceder ou negar dinamicamente o consentimento da aplicação para aceder a recursos em seu nome.
* Por fim, permite aos administradores decidir que aplicações estão autorizados a fazer, que utilizadores podem utilizar aplicações específicas e de que forma são acedidos os recursos de diretório.

Na plataforma de identidade da Microsoft, um objeto de [aplicação](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object) descreve uma aplicação. No momento da implantação, a plataforma de identidade da Microsoft usa o objeto Application como um plano gráfico para criar uma [entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object), que representa uma instância concreta de um aplicativo em um diretório ou locatário. A entidade de serviço define o que o aplicativo pode realmente fazer em um diretório de destino específico, que pode usá-lo, a quais recursos ele tem acesso e assim por diante. A plataforma de identidade da Microsoft cria uma entidade de serviço de um objeto de aplicativo por meio de **consentimento**.

O diagrama a seguir mostra um fluxo simplificado de provisionamento da plataforma Microsoft Identity orientado por consentimento. Mostra dois inquilinos: A e B. Tenant A é o dono da candidatura. O locatário B está instanciando o aplicativo por meio de uma entidade de serviço.  

![Fluxo de aprovisionamento simplificado orientado por consentimento](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Neste fluxo de aprovisionamento:

1. Um usuário do locatário B tenta entrar com o aplicativo, o ponto de extremidade de autorização solicita um token para o aplicativo.
1. As credenciais do usuário são adquiridas e verificadas quanto à autenticação.
1. O usuário é solicitado a fornecer consentimento para que o aplicativo tenha acesso ao locatário B.
1. A plataforma de identidade da Microsoft usa o objeto Application no locatário A como um plano gráfico para criar uma entidade de serviço no locatário B.
1. O usuário recebe o token solicitado.

Você pode repetir esse processo para locatários adicionais. O locatário A retém o plano gráfico para o aplicativo (objeto de aplicativo). Usuários e administradores de todos os outros locatários em que o aplicativo recebe consentimento, mantêm o controle sobre o que o aplicativo tem permissão para fazer por meio do objeto de entidade de serviço correspondente em cada locatário. Para obter mais informações, consulte [objetos de aplicativo e entidade de serviço na plataforma Microsoft Identity](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Fluxo de entrada do aplicativo Web com o Azure AD

Quando um usuário navega no navegador para um aplicativo Web, acontece o seguinte:

* O aplicativo Web determina se o usuário está autenticado.
* Se o usuário não for autenticado, o aplicativo Web delegará ao Azure AD para conectar o usuário. Essa entrada será compatível com a política da organização, o que pode significar solicitar que o usuário insira suas credenciais, usando a autenticação multifator ou não usando uma senha (por exemplo, usando o Windows Hello).
* O usuário será solicitado a consentir o acesso de que o aplicativo cliente precisa. É por isso que os aplicativos cliente precisam ser registrados com o Azure AD, para que o Azure AD possa fornecer tokens que representem o acesso ao qual o usuário tenha consentido.

Quando o usuário foi autenticado com êxito:

* O Azure AD envia um token para o aplicativo Web.
* Um cookie é salvo, associado ao domínio do AD do Azure, que contém a identidade do usuário no jar do cookie do navegador. Na próxima vez que um aplicativo usar o navegador para navegar até o ponto de extremidade de autorização do Azure AD, o navegador apresentará o cookie para que o usuário não precise entrar novamente. Isso também é a maneira como o SSO é obtido. O cookie é produzido pelo Azure AD e só pode ser compreendido pelo Azure AD.
* Em seguida, o aplicativo Web valida o token. Se a validação for realizada com sucesso, o aplicativo Web exibirá a página protegida e salvará um cookie de sessão no jar do cookie do navegador. Quando o usuário navega para outra página, o aplicativo Web sabe que o usuário é autenticado com base no cookie de sessão.

O diagrama de sequência a seguir resume essa interação:

![processo de autenticação do aplicativo Web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Como um aplicativo Web determina se o usuário é autenticado

Os desenvolvedores de aplicativos Web podem indicar se todas ou apenas determinadas páginas exigem autenticação. Por exemplo, em ASP.NET/ASP.NET Core, isto é feito adicionando o atributo `[Authorize]` às ações do controlador. 

Esse atributo faz com que o ASP.NET Verifique a presença de um cookie de sessão que contém a identidade do usuário. Se um cookie não estiver presente, o ASP.NET redirecionará a autenticação para o provedor de identidade especificado. Se o fornecedor de identidade for Azure AD, a aplicação web redireciona a autenticação para `https://login.microsoftonline.com`, que apresenta um diálogo de entrada.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Como um aplicativo Web delega a entrada ao Azure AD e Obtém um token

A autenticação do usuário ocorre por meio do navegador. O protocolo OpenID usa mensagens de protocolo HTTP padrão.
* O aplicativo Web envia um HTTP 302 (redirecionamento) para o navegador para usar o Azure AD.
* Quando o usuário é autenticado, o Azure AD envia o token para o aplicativo Web usando um redirecionamento por meio do navegador.
* O redirecionamento é fornecido pelo aplicativo Web na forma de um URI de redirecionamento. Esse URI de redirecionamento é registrado com o objeto de aplicativo do Azure AD. Pode haver vários URIs de redirecionamento porque o aplicativo pode ser implantado em várias URLs. Portanto, o aplicativo Web também precisará especificar o URi de redirecionamento a ser usado.
* O Azure AD verifica se o URI de redirecionamento enviado pelo aplicativo Web é um dos URIs de redirecionamento registrados para o aplicativo.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Fluxo de entrada do aplicativo móvel e desktop com o Azure AD

O fluxo descrito acima se aplica, com pequenas diferenças, a desktops e aplicativos móveis.

Aplicativos móveis e de desktop podem usar um controle da Web inserido ou um navegador do sistema para autenticação. O diagrama a seguir mostra como um desktop ou aplicativo móvel usa a MSAL (biblioteca de autenticação da Microsoft) para adquirir tokens de acesso e chamar APIs da Web.

![Aplicativo de desktop como parece ser](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

A MSAL usa um navegador para obter fichas. Tal como acontece com as aplicações web, a autenticação é delegada no Azure AD.

Como o Azure AD salva o mesmo cookie de identidade no navegador, como ele faz para aplicativos Web, se o aplicativo nativo ou móvel usar o navegador do sistema, ele receberá imediatamente o SSO com o aplicativo Web correspondente.

Por padrão, a MSAL utiliza o navegador do sistema. A exceção são as aplicações de ambiente de trabalho .NET Framework onde é utilizado um controlo incorporado para proporcionar uma experiência de utilizador mais integrada.

## <a name="next-steps"></a>Passos seguintes

* Consulte o [Glossário de desenvolvedor da plataforma de identidade da Microsoft](developer-glossary.md) para se familiarizar com os termos comuns.
* Consulte [fluxos de autenticação e cenários de aplicativo](authentication-flows-app-scenarios.md) para saber mais sobre outros cenários de autenticação de usuários com suporte na plataforma de identidade da Microsoft.
* Consulte [bibliotecas do MSAL](msal-overview.md) para saber mais sobre as bibliotecas da Microsoft que ajudam você a desenvolver aplicativos que funcionam com contas da Microsoft, contas do Azure AD e Azure ad B2C usuários em um modelo de programação único e simplificado.
* Consulte [integrar o serviço de aplicativo com a plataforma de identidade da Microsoft](/azure/app-service/configure-authentication-provider-aad) para saber como configurar a autenticação para seu aplicativo do serviço de aplicativo.
