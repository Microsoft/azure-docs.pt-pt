---
title: Programador de plataformas de identidade da Microsoft | Rio Azure
description: Uma lista de termos para conceitos e funcionalidades de desenvolvimento de plataformas de identidade da Microsoft comumente utilizados.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.openlocfilehash: 10df22b46d37336d0ed506e4ee30c203e904cd3f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305893"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Glossário de desenvolvimento de plataforma de identidade da Microsoft

Este artigo contém definições para alguns dos principais conceitos de desenvolvedores e terminologia, que são úteis quando se aprende sobre o desenvolvimento de aplicações usando a plataforma de identidade microsoft.

## <a name="access-token"></a>token de acesso

Um tipo de ficha de [segurança](#security-token) emitida por um [servidor de autorização](#authorization-server), e utilizada por uma [aplicação do cliente](#client-application) para aceder a um servidor de recursos [protegidos](#resource-server). Tipicamente sob a forma de um [JSON Web Token (JWT),][JWT]o símbolo incorpora a autorização concedida ao cliente pelo proprietário do [recurso,](#resource-owner)para um nível de acesso solicitado. O token contém todas as [reclamações](#claim) aplicáveis sobre o assunto, permitindo que a aplicação do cliente a utilize como forma de credencial ao aceder a um determinado recurso. Isto também elimina a necessidade de o proprietário de recursos expor credenciais ao cliente.

As fichas de acesso são válidas apenas por um curto período de tempo e não podem ser revogadas. Um servidor de autorização também pode emitir um [token de atualização](#refresh-token) quando o token de acesso é emitido. Os tokens de atualização são normalmente fornecidos apenas a aplicações confidenciais do cliente.

Os tokens de acesso são por vezes referidos como "User+App" ou "App-Only", dependendo das credenciais que estão representadas. Por exemplo, quando uma aplicação de cliente utiliza o:

* [Concessão de autorização "Código de Autorização",](#authorization-grant)o utilizador final autentica primeiro como titular do recurso, delegando autorização ao cliente para aceder ao recurso. O cliente autentica-se depois ao obter o token de acesso. O token pode por vezes ser referido mais especificamente como um token "User+App", uma vez que representa tanto o utilizador que autorizou a aplicação do cliente, como a aplicação.
* ["Concessão de autorização de credenciais](#authorization-grant)de cliente", o cliente fornece a única autenticação, funcionando sem a autenticação/autorização do titular do recurso, pelo que o token pode por vezes ser referido como um símbolo "App-Only".

Consulte a [plataforma de identidade da Microsoft Token Reference][AAD-Tokens-Claims] para obter mais detalhes.

## <a name="application-id-client-id"></a>ID de aplicação (ID do cliente)

O identificador único Azure AD emite um registo de candidatura que identifica uma aplicação específica e as configurações associadas. Este ID de aplicação ([ID do cliente)](https://tools.ietf.org/html/rfc6749#page-15)é utilizado na realização de pedidos de autenticação e é fornecido às bibliotecas de autenticação em tempo de desenvolvimento. O ID da aplicação (identificação do cliente) não é um segredo.

## <a name="application-manifest"></a>manifesto de aplicação

Uma funcionalidade fornecida pelo [portal Azure][AZURE-portal], que produz uma representação JSON da configuração de identidade da aplicação, utilizada como mecanismo de atualização das suas entidades associadas [de Aplicação][Graph-App-Resource] e [ServiçoPrincipal.][Graph-Sp-Resource] Consulte [a compreensão da aplicação Azure Ative Directory para][AAD-App-Manifest] obter mais detalhes.

## <a name="application-object"></a>objeto de aplicação

Quando regista/atualiza uma aplicação no [portal Azure,][AZURE-portal]o portal cria/atualiza tanto um objeto de aplicação como um [objeto principal](#service-principal-object) de serviço correspondente para esse inquilino. O objeto de aplicação define a configuração de identidade da aplicação globalmente (em todos os inquilinos onde tem acesso), fornecendo um modelo a partir do qual os seus *objetos* principais de serviço correspondentes são *derivados* para uso local no tempo de execução (em um inquilino específico).

Para mais informações, consulte [Os Objetos Principais de Aplicação e Serviço.][AAD-App-SP-Objects]

## <a name="application-registration"></a>registo de aplicação

Para permitir a integração de uma candidatura e delegar funções de Gestão de Identidade e Acesso à AZure AD, deve ser registada junto de um [inquilino](#tenant)da AD Azure. Quando regista a sua candidatura com Azure AD, está a fornecer uma configuração de identidade para a sua aplicação, permitindo-lhe integrar-se com a Azure AD e utilizar funcionalidades como:

* Gestão robusta de Sign-On única utilizando a gestão de identidade Azure AD e implementação do protocolo [OpenID Connect][OpenIDConnect]
* Acesso intermediado a [recursos protegidos](#resource-server) por [aplicações de clientes,](#client-application)via servidor de [autorização](#authorization-server) OAuth 2.0
* [Quadro de consentimento](#consent) para gerir o acesso do cliente a recursos protegidos, com base na autorização do proprietário de recursos.

Consulte [as aplicações de integração com o Azure Ative Directory][AAD-Integrating-Apps] para obter mais detalhes.

## <a name="authentication"></a>autenticação

O ato de desafiar uma parte por credenciais legítimas, fornecendo as bases para a criação de um princípio de segurança a ser usado para o controlo de identidade e acesso. Durante uma [autorização OAuth2,](#authorization-grant) por exemplo, a autenticação da parte está a preencher o papel de titular de [recursos](#resource-owner) ou de pedido de [cliente,](#client-application)dependendo da subvenção utilizada.

## <a name="authorization"></a>autorização

O ato de conceder permissão a um chefe de segurança autenticado para fazer algo. Existem dois casos de utilização principais no modelo de programação do Azure AD:

* Durante um fluxo [de autorização OAuth2:](#authorization-grant) quando o titular do [recurso](#resource-owner) concede autorização ao pedido do [cliente,](#client-application)permitindo ao cliente aceder aos recursos do titular do recurso.
* Durante o acesso ao recurso pelo cliente: conforme implementado pelo servidor de [recursos,](#resource-server)utilizando os valores [de reclamação](#claim) presentes no [token](#access-token) de acesso para tomar decisões de controlo de acesso com base nos mesmos.

## <a name="authorization-code"></a>código de autorização

Um "token" de curta duração fornecido a um pedido de [cliente](#client-application) pelo ponto final de [autorização,](#authorization-endpoint)como parte do fluxo de "código de autorização", uma das quatro [subvenções de autorização](#authorization-grant)da OAuth2. O código é devolvido à aplicação do cliente em resposta à autenticação de um titular de [recursos,](#resource-owner)indicando que o titular do recurso delegou autorização para aceder aos recursos solicitados. Como parte do fluxo, o código é posteriormente reembolsado para um [token de acesso](#access-token).

## <a name="authorization-endpoint"></a>ponto final de autorização

Um dos pontos finais implementados pelo [servidor de autorização,](#authorization-server)utilizado para interagir com o titular do [recurso,](#resource-owner) a fim de fornecer uma [concessão de autorização](#authorization-grant) durante um fluxo de autorização OAuth2. Dependendo do fluxo de concessão de autorização utilizado, a subvenção efetiva fornecida pode variar, incluindo um [código de autorização](#authorization-code) ou um símbolo de [segurança](#security-token).

Consulte os tipos de [concessão][OAuth2-AuthZ-Grant-Types] de autorização da OAuth2 e as secções [de ponto final][OAuth2-AuthZ-Endpoint] de autorização e a [especificação OpenIDConnect][OpenIDConnect-AuthZ-Endpoint] para obter mais detalhes.

## <a name="authorization-grant"></a>concessão de autorização

Uma credencial que representa a [autorização](#authorization) [do titular do recurso](#resource-owner) para aceder aos seus recursos protegidos, concedida a uma aplicação do [cliente.](#client-application) Um pedido de cliente pode utilizar um dos quatro tipos de [subvenção definidos pelo Quadro de Autorização da OAuth2][OAuth2-AuthZ-Grant-Types] para obter uma subvenção, dependendo do tipo/requisitos do cliente: "concessão de código de autorização", "concessão de credenciais de cliente", "concessão implícita" e "concessão de credenciais de senha do proprietário de recursos". A credencial devolvida ao cliente é ou um token de [acesso,](#access-token)ou um [código de autorização](#authorization-code) (trocado posteriormente por um token de acesso), dependendo do tipo de subsídio de autorização utilizado.

## <a name="authorization-server"></a>servidor de autorização

Tal como definido pelo [Quadro de Autorização da OAuth2,][OAuth2-Role-Def]o servidor responsável pela emissão de fichas de acesso ao [cliente](#client-application) após autenticar com sucesso o titular do [recurso](#resource-owner) e obter a sua autorização. Uma [aplicação de cliente](#client-application) interage com o servidor de autorização em tempo de execução através da sua [autorização](#authorization-endpoint) e pontos finais [simbólicos,](#token-endpoint) de acordo com as [subvenções de autorização definidas](#authorization-grant)pela OAuth2 .

No caso da integração da aplicação da plataforma de identidade da Microsoft, a plataforma de identidade da Microsoft implementa o papel do servidor de autorização para aplicações AD Azure e APIs de serviço microsoft, por [exemplo, APIs de gráficos da Microsoft][Microsoft-Graph].

## <a name="claim"></a>reivindicação

Um [token de segurança](#security-token) contém reclamações, que fornecem afirmações sobre uma entidade (como uma [aplicação](#client-application) do cliente ou [proprietário de recursos)](#resource-owner)a outra entidade (como o [servidor de recursos).](#resource-server) As reclamações são pares de nome/valor que transmitem factos sobre o sujeito simbólico (por exemplo, o principal de segurança que foi autenticado pelo [servidor de autorização).](#authorization-server) As reclamações presentes num dado token dependem de várias variáveis, incluindo o tipo de token, o tipo de credencial utilizada para autenticar o sujeito, a configuração da aplicação, etc.

Consulte a referência simbólica da [plataforma de identidade][AAD-Tokens-Claims] da Microsoft para obter mais detalhes.

## <a name="client-application"></a>aplicação de cliente

Tal como definido pelo [Quadro de Autorização da OAuth2,][OAuth2-Role-Def]um pedido que faz pedidos de recursos protegidos em nome do proprietário do [recurso.](#resource-owner) O termo "cliente" não implica características específicas de implementação de hardware (por exemplo, se a aplicação executa num servidor, num ambiente de trabalho ou noutros dispositivos).

Um pedido de cliente solicita [autorização](#authorization) de um proprietário de recursos para participar num fluxo [de concessão de autorização OAuth2,](#authorization-grant) e pode aceder a APIs/dados em nome do proprietário do recurso. O Quadro de Autorização da OAuth2 [define dois tipos de clientes][OAuth2-Client-Types], "confidenciais" e "públicos", com base na capacidade do cliente de manter a confidencialidade das suas credenciais. As aplicações podem implementar um [cliente web (confidencial)](#web-client) que funciona num servidor web, um [cliente nativo (público)](#native-client) instalado num dispositivo, ou um [cliente baseado em agente de utilizador (público)](#user-agent-based-client) que funciona no navegador de um dispositivo.

## <a name="consent"></a>consentimento

O processo de um titular de [recursos](#resource-owner) que concede autorização a uma [aplicação](#client-application)do cliente, para aceder a recursos protegidos sob [permissões específicas,](#permissions)em nome do proprietário do recurso. Dependendo das permissões solicitadas pelo cliente, será solicitado consentimento a um administrador ou utilizador para permitir o acesso à sua organização/dados individuais, respectivamente. Note-se, num cenário [multi-inquilino,](#multi-tenant-application) o diretor de [serviço](#service-principal-object) da aplicação também é registado no arrendatário do utilizador que consente.

Consulte [o quadro de consentimento](consent-framework.md) para mais informações.

## <a name="id-token"></a>Ficha de ID

Um [token de segurança](#security-token) [OpenID Connect][OpenIDConnect-ID-Token] fornecido pelo ponto final de autorização de um servidor de [autorização](#authorization-server) [](#authorization-endpoint), que contém [reclamações relativas](#claim) à autenticação de um proprietário de [recursos](#resource-owner)do utilizador final . Como um token de acesso, os tokens de ID também são representados como um [JSON Web Token (JWT)][JWT]assinado digitalmente. No entanto, ao contrário de um token de acesso, as alegações de um token de identificação não são usadas para fins relacionados com o acesso a recursos e especificamente o controlo de acesso.

Consulte a referência simbólica da [plataforma de identidade][AAD-Tokens-Claims] da Microsoft para obter mais detalhes.

## <a name="microsoft-identity-platform"></a>Plataforma de identidades da Microsoft

A plataforma de identidade da Microsoft é uma evolução do serviço de identidade e programador Azure Ative Directory (Azure AD). Permite que os programadores compilem aplicações que iniciam sessão em todas as identidades da Microsoft, obtenham tokens para chamar o Microsoft Graph, outras APIs da Microsoft ou APIs compiladas pelos programadores. É uma plataforma completa que consiste num serviço de autenticação, bibliotecas, registo de aplicações e configuração, documentação completa do desenvolvedor, amostras de código e outros conteúdos do desenvolvedor. A plataforma de identidades da Microsoft suporta protocolos norma da indústria, tais como OAuth 2.0 e o OpenID Connect.

## <a name="multi-tenant-application"></a>aplicação multi-inquilino

Uma classe de aplicação que permite iniciar sação e [consentimento](#consent) pelos utilizadores a provisionados em qualquer [inquilino](#tenant)AZure AD , incluindo inquilinos que não aquele em que o cliente está registado. As aplicações [de clientes nativos](#native-client) são multi-arrendantes por padrão, enquanto as aplicações [de cliente web](#web-client) e web [recursos/API](#resource-server) têm a capacidade de selecionar entre um único ou multi-inquilino. Em contrapartida, uma aplicação web registada como inquilino único, só permitiria a entrada de contas de utilizador previstas no mesmo inquilino que a que o pedido está registado.

Veja [como iniciar sedutar em qualquer utilizador Azure AD usando o padrão de aplicação de vários inquilinos][AAD-Multi-Tenant-Overview] para mais detalhes.

## <a name="native-client"></a>cliente nativo

Um tipo de [aplicação](#client-application) de cliente que é instalada de forma nativa num dispositivo. Uma vez que todo o código é executado num dispositivo, é considerado um cliente "público" devido à sua incapacidade de armazenar credenciais privadas/confidenciais. Consulte [os tipos e perfis de clientes da OAuth2][OAuth2-Client-Types] para obter mais detalhes.

## <a name="permissions"></a>permissões

Uma [aplicação](#client-application) de cliente obtém acesso a um [servidor de recursos](#resource-server) declarando pedidos de permissão. Estão disponíveis dois tipos:

* As permissões "delegadas", que especificam o acesso [baseado](#scopes) no âmbito utilizando a autorização delegada do titular do [recurso,](#resource-owner)são apresentadas ao recurso em tempo de execução como [alegações "scp"](#claim) no [token](#access-token)de acesso do cliente.
* As permissões de "aplicação", que especificam o acesso [baseado em funções](#roles) utilizando as credenciais/identidade da aplicação do cliente, são apresentadas ao recurso em tempo de execução como [alegações de "funções"](#claim) no token de acesso do cliente.

Também surgem durante o processo [de consentimento,](#consent) dando ao administrador ou proprietário de recursos a oportunidade de conceder/negar o acesso do cliente aos recursos no seu inquilino.

Os pedidos de permissão são configurados na página **de permissões** da API para uma aplicação no [portal Azure,][AZURE-portal]selecionando as "Permissões Delegadas" e "Permissões de Aplicação" (esta última requer adesão à função Administração Global). Como um [cliente público](#client-application) não consegue manter as credenciais de forma segura, só pode solicitar permissões delegadas, enquanto um [cliente confidencial](#client-application) tem a capacidade de solicitar permissões delegadas e de candidatura. O objeto de [aplicação](#application-object) do cliente armazena as permissões declaradas na [sua propriedade requeridaResourceAccess.][Graph-App-Resource]

## <a name="refresh-token"></a>token refresh

Um tipo de ficha de [segurança](#security-token) emitida por um [servidor de autorização](#authorization-server), e usada por uma [aplicação](#client-application) do cliente para solicitar um novo [token de acesso](#access-token) antes do fim do token de acesso. Tipicamente sob a forma de um [JSON Web Token (JWT)][JWT].

Ao contrário dos tokens de acesso, os tokens de renovação podem ser revogados. Se uma aplicação do cliente tentar solicitar um novo token de acesso utilizando um token de atualização que foi revogado, o servidor de autorização negará o pedido, e a aplicação do cliente deixará de ter permissão para aceder ao servidor de [recursos](#resource-server) em nome do proprietário do [recurso.](#resource-owner)

## <a name="resource-owner"></a>proprietário de recursos

Tal como definido pelo [Quadro de Autorização da OAuth2,][OAuth2-Role-Def]uma entidade capaz de conceder acesso a um recurso protegido. Quando o titular do recurso é uma pessoa, é referido como um utilizador final. Por exemplo, quando uma [aplicação](#client-application) de cliente quer aceder à caixa de correio de um utilizador através da [Microsoft Graph API,][Microsoft-Graph]requer autorização do titular do recurso da caixa de correio.

## <a name="resource-server"></a>servidor de recursos

Conforme definido pelo [Quadro de Autorização da OAuth2,][OAuth2-Role-Def]um servidor que acolhe recursos protegidos, capaz de aceitar e responder a pedidos de recursos protegidos por [aplicações de clientes](#client-application) que apresentam um [token de acesso](#access-token). Também conhecido como um servidor de recursos protegido, ou aplicação de recursos.

Um servidor de recursos expõe APIs e impõe o acesso aos seus recursos protegidos através de [âmbitos](#scopes) e [funções,](#roles)utilizando o Quadro de Autorização OAuth 2.0. Exemplos incluem a API do [Microsoft Graph][Microsoft-Graph] que fornece acesso aos dados do inquilino AZure AD, e as APIs microsoft 365 que fornecem acesso a dados como correio e calendário.

Tal como uma aplicação de cliente, a configuração de identidade da aplicação de recursos é estabelecida através do [registo](#application-registration) de um inquilino AZure AD, fornecendo tanto o objeto principal de aplicação como de serviço. Algumas APIs fornecidas pela Microsoft, como a Microsoft Graph API, têm principais de serviço pré-registados disponibilizados em todos os inquilinos durante o fornecimento.

## <a name="roles"></a>funções

Tal como [os âmbitos,](#scopes)as funções fornecem uma forma de um [servidor de recursos](#resource-server) governar o acesso aos seus recursos protegidos. Existem dois tipos: uma função de "utilizador" implementa o controlo de acesso baseado em funções para utilizadores/grupos que exigem acesso ao recurso, enquanto uma função de "aplicação" implementa o mesmo para aplicações de [clientes](#client-application) que requerem acesso.

As funções são cordas definidas por recursos (por exemplo" "Aprovador de Despesas", "Read-only", "Diretório.ReadWrite.All"), geridas no [portal Azure][AZURE-portal] através do manifesto de [aplicação](#application-manifest)do recurso, e armazenadas na [propriedade appRoles][Graph-Sp-Resource]do recurso. O portal Azure também é utilizado para atribuir utilizadores a funções de "utilizador" e configurar [permissões de aplicação](#permissions) do cliente para aceder a uma função de "aplicação".

Para uma discussão detalhada das funções de aplicação expostas pela Microsoft Graph API, consulte [os Âmbitos de Permisse da API do gráfico][Graph-Perm-Scopes]. Para obter um exemplo de implementação passo a passo, consulte [adicionar ou remover atribuições de funções Azure utilizando o portal Azure][AAD-RBAC].

## <a name="scopes"></a>âmbitos

Tal como [as funções,](#roles)os âmbitos fornecem uma forma de um [servidor de recursos](#resource-server) governar o acesso aos seus recursos protegidos. Os âmbitos são utilizados para implementar o controlo de acesso [baseado no âmbito,][OAuth2-Access-Token-Scopes] para uma [aplicação](#client-application) do cliente que foi dada acesso delegado ao recurso pelo seu proprietário.

Os âmbitos são cordas definidas por recursos (por exemplo"Mail.Read", "Diretório.ReadWrite.All"), geridas no [portal Azure][AZURE-portal] através do manifesto de [aplicação](#application-manifest)do recurso, e armazenadas na propriedade de [oauth2Permissions][Graph-Sp-Resource]do recurso. O portal Azure também é utilizado para configurar a aplicação do cliente [delegada permissões](#permissions) para aceder a um âmbito.

Uma convenção de nomeação de boas práticas é usar um formato "resource.operation.constraint". Para uma discussão detalhada dos âmbitos expostos pela Microsoft Graph API, consulte [os Âmbitos de Permissões da API do gráfico][Graph-Perm-Scopes]. Para os âmbitos expostos pelos serviços microsoft 365, consulte [a referência de permissões API da Microsoft 365][O365-Perm-Ref].

## <a name="security-token"></a>símbolo de segurança

Um documento assinado contendo reclamações, como uma token OAuth2 ou uma afirmação SAML 2.0. Para uma [concessão](#authorization-grant)de autorização OAuth2 , um [token](#access-token) de acesso (OAuth2), [refresh token](#refresh-token), e um [ID Token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) são tipos de fichas de segurança, todas elas implementadas como um [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>objeto principal de serviço

Quando regista/atualiza uma aplicação no [portal Azure,][AZURE-portal]o portal cria/atualiza tanto um objeto de [aplicação](#application-object) como um objeto principal de serviço correspondente para esse inquilino. O objeto de aplicação define globalmente a configuração de identidade da aplicação (em todos os inquilinos onde a aplicação associada foi concedida), e é o modelo a partir do qual os seus *objetos* principais de serviço correspondentes são *derivados* para uso local no horário de funcionamento (em um inquilino específico).

Para mais informações, consulte [Os Objetos Principais de Aplicação e Serviço.][AAD-App-SP-Objects]

## <a name="sign-in"></a>sign-in

O processo de uma [aplicação](#client-application) do cliente que inicia a autenticação do utilizador final e a captura do estado relacionado, com o objetivo de adquirir um [token](#security-token) de segurança e de digitalizar a sessão de aplicação para esse estado. O Estado pode incluir artefactos como informações sobre o perfil do utilizador e informações derivadas de alegações simbólicas.

A função de inscrição de uma aplicação é normalmente utilizada para implementar um único sign-on (SSO). Pode também ser precedido por uma função de "inscrição", como ponto de entrada para um utilizador final ter acesso a uma aplicação (após a primeira entrada). A função de inscrição é utilizada para recolher e persistir um estado adicional específico do utilizador, podendo exigir o [consentimento do utilizador](#consent).

## <a name="sign-out"></a>de fim de sessão

O processo de não autenticação de um utilizador final, desvinculando o estado de utilizador associado à sessão de [aplicação](#client-application) do cliente durante o [login](#sign-in)

## <a name="tenant"></a>inquilino

Um caso de diretório AD Azure é referido como um inquilino da AD Azure. Fornece várias funcionalidades, incluindo:

* um serviço de registo para aplicações integradas
* autenticação de contas de utilizador e aplicações registadas
* Pontos finais REST necessários para apoiar vários protocolos, incluindo a OAuth2 e a SAML, incluindo o ponto final de [autorização,](#authorization-endpoint)o [ponto final simbólico](#token-endpoint) e o ponto final "comum" utilizado pelas [aplicações de vários inquilinos.](#multi-tenant-application)

Os inquilinos da AZure AD são criados/associados com subscrições Azure e Microsoft 365 durante a inscrição, fornecendo funcionalidades de Gestão de Acesso de Identidade & para a subscrição. Os administradores de subscrição da Azure também podem criar inquilinos AZure AD adicionais através do portal Azure. Veja [como obter um inquilino do Azure Ative Directory][AAD-How-To-Tenant] para obter detalhes sobre as várias formas de acesso a um inquilino. Consulte [Associate ou adicione uma subscrição Azure ao seu inquilino Azure Ative Directory][AAD-How-Subscriptions-Assoc] para obter detalhes sobre a relação entre as subscrições e um inquilino AD Azure, e para instruções sobre como associar ou adicionar uma subscrição a um inquilino AZure AD.

## <a name="token-endpoint"></a>ponto final simbólico

Um dos pontos finais implementados pelo [servidor de autorização](#authorization-server) para suportar as [subvenções de autorização](#authorization-grant)da OAuth2 . Dependendo da concessão, pode ser usado para adquirir um [token](#access-token) de acesso (e ficha de "refresh" relacionado) a um [cliente,](#client-application)ou [ficha de ID](#id-token) quando usado com o protocolo [OpenID Connect.][OpenIDConnect]

## <a name="user-agent-based-client"></a>Cliente baseado em agente de utilizador

Um tipo de [aplicação](#client-application) de cliente que descarrega código a partir de um servidor web e executa dentro de um agente de utilizador (por exemplo, um navegador web), como uma aplicação de uma página (SPA). Uma vez que todo o código é executado num dispositivo, é considerado um cliente "público" devido à sua incapacidade de armazenar credenciais privadas/confidenciais. Para obter mais informações, consulte [os tipos e perfis de clientes da OAuth2.][OAuth2-Client-Types]

## <a name="user-principal"></a>utilizadores principal

Semelhante à forma como um objeto principal de serviço é usado para representar uma instância de aplicação, um objeto principal do utilizador é outro tipo de principal de segurança, que representa um utilizador. O tipo [de recurso do Utilizador do][Graph-User-Resource] Microsoft Graph define o esquema para um objeto de utilizador, incluindo propriedades relacionadas com o utilizador, tais como o primeiro e o apelido, o nome principal do utilizador, a adesão à função de diretório, etc. Isto fornece a configuração de identidade do utilizador para a Azure AD para estabelecer um principal utilizador no tempo de execução. O titular do utilizador é utilizado para representar um utilizador autenticado para o Sign-On único, para registar a delegação de [consentimento,](#consent) tomar decisões de controlo de acesso, etc.

## <a name="web-client"></a>cliente web

Um tipo de [aplicação](#client-application) de cliente que executa todo o código num servidor web e é capaz de funcionar como um cliente "confidencial", armazenando de forma segura as suas credenciais no servidor. Para obter mais informações, consulte [os tipos e perfis de clientes da OAuth2.][OAuth2-Client-Types]

## <a name="next-steps"></a>Passos seguintes

O [Guia do Programador da plataforma de identidade da Microsoft][AAD-Dev-Guide] é a página de aterragem para todos os tópicos relacionados com o desenvolvimento da plataforma de identidade da Microsoft, incluindo uma visão geral da integração de [aplicações][AAD-How-To-Integrate] e os fundamentos da autenticação da plataforma de identidade da Microsoft e [cenários de autenticação suportados.][AAD-Auth-Scenarios] Também pode encontrar amostras de código & tutoriais sobre como se levantar e correr rapidamente no [GitHub.](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=)

Utilize a seguinte secção de comentários para fornecer feedback e ajudar a aperfeiçoar e moldar este conteúdo, incluindo pedidos de novas definições ou atualização dos existentes!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal?view=graph-rest-beta&preserve-view=true
[Graph-User-Resource]: /graph/api/resources/user
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/rfc7519
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
