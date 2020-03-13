---
title: Autenticação na plataforma de identidade da Microsoft Azure
description: Conheça os fundamentos da autenticação na plataforma de identidade da Microsoft (v2.0).
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
ms.openlocfilehash: 3ec965318da1361454b4a6bb78ed7147562b5fea
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79138523"
---
# <a name="authentication-basics"></a>Noções básicas sobre autenticação

## <a name="what-is-authentication"></a>O que é a autenticação

Este artigo abrange muitos dos conceitos de autenticação que você precisa entender para criar aplicações web protegidas, APIs web ou aplicações que chamam APIs web protegidos. Se vir um termo que não conhece, experimente o nosso [glossário](developer-glossary.md) ou os [nossos vídeos](identity-videos.md) da plataforma de identidade Microsoft que cobrem conceitos básicos.

**A autenticação** é o processo de provar que é quem diz ser. Por vezes, o termo é abreviado como AuthN.

**Autorização** é o ato de conceder uma autorização autenticada para fazer algo. Especifica quais os dados a que pode aceder e o que pode fazer com esses dados. Por vezes, o termo é abreviado como AuthZ.

Em vez de criar aplicações que cada um mantenha o seu próprio nome de utilizador e informações de palavra-passe, que incorre num elevado fardo administrativo quando precisa de adicionar ou remover utilizadores através de várias aplicações, as aplicações podem delegar essa responsabilidade num fornecedor de identidade centralizado.

O Azure Ative Directory (Azure AD) é um fornecedor de identidade centralizado na nuvem. Delegar a autenticação e autorização permite que cenários como políticas de Acesso Condicional que exijam que um utilizador esteja num local específico, a utilização da autenticação multifactor, bem como permitir que um utilizador assine uma vez e depois seja automaticamente assinou em todas as aplicações web que partilham o mesmo diretório centralizado. Esta capacidade é referida como Single Sign On (SSO).

Um fornecedor de identidade centralizado é ainda mais importante para apps que têm utilizadores localizados em todo o mundo que não necessariamente se inscrevem na rede da empresa. A Azure AD autentica os utilizadores e fornece fichas de acesso. Um [sinal de acesso](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token) é um símbolo de segurança que é emitido por um servidor de autorização. Contém informações sobre o utilizador e a aplicação para a qual se destina o símbolo; que podem ser usados para aceder a APIs web e outros recursos protegidos.

A plataforma de identidade da Microsoft simplifica a autenticação para os desenvolvedores de aplicações, fornecendo identidade como serviço, com suporte para protocolos padrão da indústria, como [OAuth 2.0](https://oauth.net/2/) e [OpenID Connect,](https://openid.net/connect/)bem como bibliotecas de código aberto para diferentes plataformas para ajudá-lo a começar a codificar rapidamente. Permite que os desenvolvedores construam aplicações que assinam todas as identidades da Microsoft, obter fichas para ligar para o [Microsoft Graph](https://developer.microsoft.com/graph/), outras APIs da Microsoft ou APIs que os desenvolvedores construíram. Para mais informações, consulte a [evolution of Microsoft identity platform](about-microsoft-identity-platform.md).

### <a name="tenants"></a>Inquilinos

Um fornecedor de identidade na nuvem serve muitas organizações. Para manter os utilizadores de diferentes organizações separadas, a Azure AD é dividida em inquilinos, com um inquilino por organização.

Os inquilinos acompanham os utilizadores e as suas aplicações associadas. A plataforma de identidade da Microsoft também suporta utilizadores que se inscrevam com contas pessoais da Microsoft.

A Azure AD também fornece o Azure Ative Directory B2C para que as organizações possam inscrever utilizadores, tipicamente clientes, usando identidades sociais como uma conta google. Para mais informações, consulte a [documentação Do Diretório Ativo Azure B2C](https://docs.microsoft.com/azure/active-directory-b2c) .

### <a name="security-tokens"></a>Fichas de segurança

Os tokens de segurança contêm informações sobre utilizadores e aplicações. A Azure AD utiliza fichas baseadas em JSON (JWTs) que contêm reclamações.

Uma reclamação fornece afirmações sobre uma entidade, como uma [aplicação de cliente](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application) ou proprietário de [recursos,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner)a outra entidade, como um servidor de [recursos.](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server)

As alegações são pares de nome/valor que transmitem factos sobre o assunto simbólico. Por exemplo, uma reclamação pode conter factos sobre o diretor de segurança que foi autenticado pelo servidor de [autorização](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server). As alegações presentes num dado sinal ão de penderá de muitas coisas, incluindo o tipo de ficha, o tipo de credencial usada para autenticar o sujeito, a configuração da aplicação, e assim por diante.

As aplicações podem utilizar reclamações para várias tarefas, tais como:

* Validando o símbolo
* Identificando o inquilino do sujeito simbólico
* Exibindo informações do utilizador
* Determinação da autorização do sujeito

Uma reivindicação consiste em pares de valor-chave que fornecem informações como:

* Servidor de Token de Segurança que gerou o símbolo
* Data em que o símbolo foi gerado
* Objeto (como o utilizador-- exceto para daemons)
* Público, que é a app para a qual o token foi gerado
* App (o cliente) que pediu o símbolo. No caso das aplicações web, este pode ser o mesmo que o público

Para obter informações mais detalhadas sobre a reclamação, consulte [fichas](access-tokens.md) de acesso e [fichas de identificação](id-tokens.md).

Cabe à aplicação para a qual o token foi gerado, à aplicação web que assinou no utilizador, ou à Web API ser chamada, para validar o token. O símbolo é assinado pelo Security Token Server (STS) com uma chave privada. O STS publica a chave pública correspondente. Para validar um símbolo, a aplicação verifica a assinatura utilizando a chave pública STS para validar que a assinatura foi criada usando a chave privada.

Os tokens só são válidos por um período limitado de tempo. Normalmente, o STS fornece um par de fichas: um sinal de acesso para aceder à aplicação ou recurso protegido, e um token refrescante usado para refrescar o token de acesso quando o token de acesso está perto de expirar.

As fichas de acesso são passadas para uma API web como o símbolo do portador no cabeçalho `Authorization`. Uma aplicação pode fornecer um token de atualização para o STS, e se o acesso do utilizador à aplicação não foi revogado, receberá de volta um novo token de acesso e um novo token de atualização. É assim que o cenário de alguém deixar a empresa é tratado. Quando o STS receber o token de atualização, não emitirá outro sinal de acesso válido se o utilizador deixar de ser autorizado.

## <a name="application-model"></a>Modelo de aplicação

As aplicações podem inscrever-se nos próprios utilizadores ou delegar o sessão a um fornecedor de identidade. Consulte fluxos de [autenticação e cenários](authentication-flows-app-scenarios.md) de aplicações para conhecer cenários de entrada suportados pela Azure AD.

Para que um fornecedor de identidade saiba que um utilizador tem acesso a uma determinada aplicação, tanto o utilizador como a aplicação devem ser registados junto do fornecedor de identidade. Ao registar a sua aplicação no Azure AD, está a fornecer uma configuração de identidade para a sua aplicação que lhe permite integrar-se com a AD Azure. Registar a aplicação também lhe permite:

* Personalize a marca da sua aplicação no diálogo de início de sessão. Isto é importante porque esta é a primeira experiência que um utilizador terá com a sua aplicação.
* Decida se pretende deixar que os utilizadores assinem apenas se pertencerem à sua organização. Este é um único pedido de inquilino. Ou permitir que os utilizadores assinem usando qualquer trabalho ou conta escolar. Esta é uma aplicação multi-inquilino. Também pode permitir contas pessoais da Microsoft, ou uma conta social do LinkedIn, Google, e assim por diante.
* Solicite permissões de âmbito de pedidos. Por exemplo, pode solicitar o âmbito "user.read", que concede permissão para ler o perfil do utilizador inscrito.
* Defina os âmbitos que definem o acesso à sua Web API. Normalmente, quando uma aplicação quer aceder à sua API, terá de solicitar permissões aos âmbitos que define.
* Partilhe um segredo com a Azure AD que comprove a identidade da app para a Azure AD.  Isto é relevante no caso em que a app é uma aplicação confidencial do cliente. Uma aplicação confidencial do cliente é uma aplicação que pode manter credenciais de forma segura. Exigem um servidor de backend de confiança para armazenar as credenciais.

Uma vez registada, a aplicação receberá um identificador único que a app partilha com a Azure AD quando solicitar fichas. Se a aplicação for uma [aplicação confidencial](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application)do cliente, também partilhará o segredo ou a chave pública*-dependendo se foram usados certificados ou segredos.

A plataforma identitária da Microsoft representa aplicações utilizando um modelo que cumpre duas funções principais:

* Identifique a app pelos protocolos de autenticação que suporta
* Fornecer todos os identificadores, URLs, segredos e informações relacionadas que são necessárias para autenticar

A plataforma de identidade da Microsoft:

* Detém todos os dados necessários para suportar a autenticação em tempo de execução
* Detém todos os dados para decidir quais os recursos que uma app pode precisar de aceder, e em que circunstâncias um determinado pedido deve ser cumprido
* Fornece infraestrutura para implementação de oprovisionamento de aplicações dentro do inquilino do desenvolvedor de aplicações, e a qualquer outro inquilino da AD Azure
* Lida com o consentimento do utilizador durante o tempo de pedido simbólico e facilita o fornecimento dinâmico de aplicações entre inquilinos

O consentimento é o processo de um proprietário de recursos que concede autorização para um pedido de cliente para aceder a recursos protegidos, sob permissões específicas, em nome do proprietário do recurso. A plataforma de identidade da Microsoft:

* Permite aos utilizadores e administradores conceder ou negar dinamicamente o consentimento da aplicação para aceder a recursos em seu nome.
* Por fim, permite aos administradores decidir que aplicações estão autorizados a fazer, que utilizadores podem utilizar aplicações específicas e de que forma são acedidos os recursos de diretório.

Na plataforma de identidade da Microsoft, um objeto de [aplicação](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object) descreve uma aplicação. No momento da implementação, a plataforma de identidade da Microsoft utiliza o objeto de aplicação como um plano para criar um principal de [serviço](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object), o que representa uma instância concreta de uma aplicação dentro de um diretório ou inquilino. O diretor de serviço define o que a app pode realmente fazer num diretório de destino específico, que pode usá-la, a que recursos tem acesso, e assim por diante. A plataforma de identidade da Microsoft cria um principal de serviço a partir de um objeto de aplicação através **do consentimento**.

O diagrama seguinte mostra um fluxo simplificado de plataforma de identidade da Microsoft impulsionado pelo consentimento. Mostra dois inquilinos: A e B. Tenant A é o dono da candidatura. O inquilino B está a instantaneamente a aplicação através de um diretor de serviço.  

![Fluxo de aprovisionamento simplificado orientado por consentimento](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Neste fluxo de aprovisionamento:

1. Um utilizador do inquilino B tenta iniciar sessão com a app, o ponto final de autorização solicita um sinal para a aplicação.
1. As credenciais do utilizador são adquiridas e verificadas para autenticação.
1. O utilizador é solicitado a dar consentimento para que a app obtenha acesso ao inquilino B.
1. A plataforma de identidade da Microsoft utiliza o objeto de aplicação no inquilino A como uma planta para criar um diretor de serviço no inquilino B.
1. O utilizador recebe o token solicitado.

Pode repetir este processo para inquilinos adicionais. Inquilino A mantém a planta para a app (objeto de aplicação). Os utilizadores e administradores de todos os outros inquilinos onde a app é dada o consentimento mantêm o controlo sobre o que a aplicação é permitida a fazer através do objeto principal de serviço correspondente em cada inquilino. Para mais informações, consulte [os objetos principais da Aplicação e do serviço na plataforma de identidade da Microsoft](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Fluxo de entrada de aplicativo web com Azure AD

Quando um utilizador navega no navegador para uma aplicação web, acontece o seguinte:

* A aplicação web determina se o utilizador é autenticado.
* Se o utilizador não for autenticado, a aplicação web delega à Azure AD para assinar no utilizador. Esse sinal de inscrição estará em conformidade com a política da organização, o que pode significar pedir ao utilizador que introduza as suas credenciais, utilizando a autenticação de vários fatores, ou não utilizando uma palavra-passe (por exemplo, utilizando o Windows Hello).
* Pede-se ao utilizador que consinta no acesso de que a aplicação do cliente necessita. É por isso que as aplicações de clientes precisam de ser registadas na Azure AD, para que a Azure AD possa fornecer fichas que representem o acesso que o utilizador consentiu.

Quando o utilizador tiver autenticado com sucesso:

* A Azure AD envia um token para a aplicação web.
* Um cookie é guardado, associado ao domínio da Azure AD, que contém a identidade do utilizador no frasco de cookies do navegador. Da próxima vez que uma aplicação usar o navegador para navegar até ao ponto final da autorização da AD Azure, o navegador apresenta o cookie para que o utilizador não tenha de voltar a fazer sessão. É também assim que o SSO é alcançado. O cookie é produzido pela Azure AD e só pode ser entendido pela Azure AD.
* A aplicação web valida então o símbolo. Se a validação for bem sucedida, a aplicação web exibe a página protegida e guarda um cookie de sessão no frasco de cookies do navegador. Quando o utilizador navega para outra página, a aplicação web sabe que o utilizador é autenticado com base no cookie da sessão.

O diagrama de sequência seguinte resume esta interação:

![processo de autenticação de aplicações web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Como uma aplicação web determina se o utilizador é autenticado

Os desenvolvedores de aplicações web podem indicar se todas ou apenas determinadas páginas requerem autenticação. Por exemplo, em ASP.NET/ASP.NET Core, isto é feito adicionando o atributo `[Authorize]` às ações do controlador. 

Este atributo faz com que ASP.NET verifique a presença de um cookie de sessão contendo a identidade do utilizador. Se um cookie não estiver presente, ASP.NET redireciona a autenticação para o fornecedor de identidade especificado. Se o fornecedor de identidade for Azure AD, a aplicação web redireciona a autenticação para `https://login.microsoftonline.com`, que apresenta um diálogo de entrada.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Como uma aplicação web delega o início de sessão no Azure AD e obtém um símbolo

A autenticação do utilizador acontece através do browser. O protocolo OpenID utiliza mensagens padrão de protocolo HTTP.
* A aplicação web envia um HTTP 302 (redirecionamento) para o navegador para utilizar o Azure AD.
* Quando o utilizador é autenticado, o Azure AD envia o token para a aplicação web utilizando um redirecionamento através do navegador.
* O redirecionamento é fornecido pela aplicação web sob a forma de um URI redirecionado. Este URI redirecionado está registado no objeto de aplicação Azure AD. Pode haver vários URIs redirecionados porque a aplicação pode ser implementada em vários URLs. Assim, a aplicação web também terá de especificar o redirecionamento uRi para usar.
* A Azure AD verifica que o URI redirecionado enviado pela aplicação web é um dos URIs de redirecionamento registados para a aplicação.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Fluxo de entrada de aplicativos de desktop e aplicativo móvel com AD Azure

O fluxo acima descrito aplica-se, com ligeiras diferenças, às aplicações de desktop e mobile.

As aplicações de desktop e mobile podem usar um controlo Web incorporado, ou um navegador de sistema, para autenticação. O diagrama seguinte mostra como uma aplicação de desktop ou móvel utiliza a biblioteca de autenticação da Microsoft (MSAL) para adquirir fichas de acesso e chamadas apis web.

![Aplicativo de desktop como parece ser](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

A MSAL usa um navegador para obter fichas. Tal como acontece com as aplicações web, a autenticação é delegada no Azure AD.

Uma vez que o Azure AD guarda o mesmo cookie de identidade no navegador como faz para aplicações web, se a aplicação nativa ou móvel utilizar o navegador do sistema, receberá imediatamente o SSO com a aplicação web correspondente.

Por padrão, a MSAL utiliza o navegador do sistema. A exceção são as aplicações de ambiente de trabalho .NET Framework onde é utilizado um controlo incorporado para proporcionar uma experiência de utilizador mais integrada.

## <a name="next-steps"></a>Passos seguintes

* Consulte o [glossário](developer-glossary.md) de desenvolvedor da plataforma de identidade microsoft para se familiarizar com termos comuns.
* Consulte fluxos de [autenticação e cenários](authentication-flows-app-scenarios.md) de aplicações para saber mais sobre outros cenários para autenticar utilizadores suportados pela plataforma de identidade da Microsoft.
* Consulte [as bibliotecas MSAL](msal-overview.md) para conhecer as bibliotecas da Microsoft que o ajudam a desenvolver aplicações que trabalham com contas Microsoft, contas AD Azure e utilizadores de Azure AD B2C num único modelo de programação simplificado.
* Consulte o [Serviço de Aplicações Integrado com](/azure/app-service/configure-authentication-provider-aad) a plataforma de identidade da Microsoft para aprender a configurar a autenticação para a sua aplicação App Service.
