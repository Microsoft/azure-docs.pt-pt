---
title: Glossário do desenvolvedor da plataforma de identidade da Microsoft | Azure
description: Uma lista de termos para conceitos e recursos de desenvolvedor da plataforma de identidade do Microsoft comumente usados.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb4deaf3d8fdc0347058b0af2079aebbd4cb22e5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424551"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Glossário do desenvolvedor da plataforma de identidade da Microsoft

Este artigo contém definições para alguns dos principais conceitos de desenvolvedor e terminologia, que são úteis ao aprender sobre o desenvolvimento de aplicativos usando a plataforma de identidade da Microsoft.

## <a name="access-token"></a>token de acesso

Um tipo de [token de segurança](#security-token) emitido por [um servidor de autorização](#authorization-server)e usado por um [aplicativo cliente](#client-application) para acessar um [servidor de recursos protegido](#resource-server). Normalmente, na forma de um [JSON Web token (JWT)][JWT], o token incorpora a autorização concedida ao cliente pelo [proprietário do recurso](#resource-owner), para um nível de acesso solicitado. O token contém todas as [declarações](#claim) aplicáveis sobre o assunto, permitindo que o aplicativo cliente o use como uma forma de credencial ao acessar um determinado recurso. Isso também elimina a necessidade de o proprietário do recurso expor as credenciais para o cliente.

Os tokens de acesso às vezes são chamados de "usuário + aplicativo" ou "somente aplicativo", dependendo das credenciais que estão sendo representadas. Por exemplo, quando um aplicativo cliente usa o:

* [Concessão de autorização de "código de autorização"](#authorization-grant), o usuário final é autenticado primeiro como o proprietário do recurso, delegando a autorização ao cliente para acessar o recurso. O cliente é autenticado posteriormente ao obter o token de acesso. Às vezes, o token pode ser referenciado mais especificamente como um token "usuário + aplicativo", pois representa o usuário que autorizou o aplicativo cliente e o aplicativo.
* [Concessão de autorização de "credenciais de cliente"](#authorization-grant), o cliente fornece a autenticação exclusiva, funcionando sem a autenticação/autorização do proprietário do recurso, portanto, o token pode, às vezes, ser referido como um token "somente do aplicativo".

Consulte [referência de token da plataforma de identidade da Microsoft][AAD-Tokens-Claims] para obter mais detalhes.

## <a name="application-id-client-id"></a>ID do aplicativo (ID do cliente)

O identificador exclusivo do Azure AD emite para um registro de aplicativo que identifica um aplicativo específico e as configurações associadas. Essa ID do aplicativo ([ID do cliente](https://tools.ietf.org/html/rfc6749#page-15)) é usada ao executar solicitações de autenticação e é fornecida para as bibliotecas de autenticação em tempo de desenvolvimento. A ID do aplicativo (ID do cliente) não é um segredo.

## <a name="application-manifest"></a>manifesto do aplicativo

Um recurso fornecido pelo [portal do Azure][AZURE-portal], que produz uma representação JSON da configuração de identidade do aplicativo, usado como um mecanismo para atualizar seu [aplicativo][Graph-App-Resource] associado e entidades de [entidade de segurança][Graph-Sp-Resource] . Consulte [noções básicas sobre o manifesto do aplicativo Azure Active Directory][AAD-App-Manifest] para obter mais detalhes.

## <a name="application-object"></a>objeto de aplicativo

Quando você registra/atualiza um aplicativo no [portal do Azure][AZURE-portal], o portal cria/atualiza um objeto de aplicativo e um objeto de [entidade de serviço](#service-principal-object) correspondente para esse locatário. O objeto Application *define* a configuração de identidade do aplicativo globalmente (em todos os locatários onde ele tem acesso), fornecendo um modelo do qual seus objetos de entidade de serviço correspondentes são *derivados* para uso localmente em tempo de execução (em um locatário específico).

Para obter mais informações, consulte [objetos de entidade de serviço e de aplicativo][AAD-App-SP-Objects].

## <a name="application-registration"></a>registo de aplicação

Para permitir que um aplicativo integre e delegue funções de gerenciamento de identidade e acesso ao Azure AD, ele deve ser registrado com um [locatário](#tenant)do Azure AD. Ao registrar seu aplicativo com o Azure AD, você está fornecendo uma configuração de identidade para seu aplicativo, permitindo que ele se integre ao Azure AD e use recursos como:

* Gerenciamento robusto de logon único usando o gerenciamento de identidade do Azure AD e a implementação do protocolo [OpenID Connect][OpenIDConnect]
* Acesso orientado a [recursos protegidos](#resource-server) por [aplicativos cliente](#client-application), por meio [do servidor de autorização](#authorization-server) OAuth 2,0
* [Estrutura de consentimento](#consent) para gerenciar o acesso do cliente a recursos protegidos, com base na autorização do proprietário do recurso.

Consulte [integrando aplicativos com Azure Active Directory][AAD-Integrating-Apps] para obter mais detalhes.

## <a name="authentication"></a>autenticação

O ato de desafiador para credenciais legítimas, fornecendo a base para a criação de uma entidade de segurança a ser usada para controle de acesso e identidade. Durante uma [concessão de autorização OAuth2](#authorization-grant) por exemplo, a autenticação de terceiros está preenchendo a função do [proprietário do recurso](#resource-owner) ou do [aplicativo cliente](#client-application), dependendo da concessão usada.

## <a name="authorization"></a>authorization

O ato de conceder uma entidade de segurança autenticada permissão para fazer algo. Existem dois casos de utilização principais no modelo de programação do Azure AD:

* Durante um fluxo de [concessão de autorização OAuth2](#authorization-grant) : quando o [proprietário do recurso](#resource-owner) concede autorização ao [aplicativo cliente](#client-application), permitindo que o cliente acesse os recursos do proprietário do recurso.
* Durante o acesso a recursos pelo cliente: conforme implementado pelo [servidor de recursos](#resource-server), usando os valores de [declaração](#claim) presentes no [token de acesso](#access-token) para tomar decisões de controle de acesso com base neles.

## <a name="authorization-code"></a>código de autorização

Um "token" de curta duração fornecido a um [aplicativo cliente](#client-application) pelo [ponto de extremidade de autorização](#authorization-endpoint), como parte do fluxo de "código de autorização", uma das quatro [concessões de autorização](#authorization-grant)de OAuth2. O código é retornado para o aplicativo cliente em resposta à autenticação de um [proprietário de recurso](#resource-owner), indicando que o proprietário do recurso tem autorização delegada para acessar os recursos solicitados. Como parte do fluxo, o código é resgatado posteriormente para um [token de acesso](#access-token).

## <a name="authorization-endpoint"></a>ponto de extremidade de autorização

Um dos pontos de extremidade implementados pelo [servidor de autorização](#authorization-server), usado para interagir com o [proprietário do recurso](#resource-owner) a fim de fornecer uma concessão de [autorização](#authorization-grant) durante um fluxo de concessão de autorização de OAuth2. Dependendo do fluxo de concessão de autorização usado, a concessão real fornecida pode variar, incluindo um [código de autorização](#authorization-code) ou [token de segurança](#security-token).

Consulte os [tipos de concessão de autorização][OAuth2-AuthZ-Grant-Types] da especificação de OAuth2 e as seções de ponto de extremidade de [autorização][OAuth2-AuthZ-Endpoint] e a [especificação de OpenIDConnect][OpenIDConnect-AuthZ-Endpoint] para obter mais detalhes.

## <a name="authorization-grant"></a>concessão de autorização

Uma credencial que representa a [autorização](#authorization) [do proprietário do recurso](#resource-owner) para acessar seus recursos protegidos, concedida a um [aplicativo cliente](#client-application). Um aplicativo cliente pode usar um dos [quatro tipos de concessão definidos pela estrutura de autorização OAuth2][OAuth2-AuthZ-Grant-Types] para obter uma concessão, dependendo do tipo/requisitos do cliente: "concessão de código de autorização", "concessão de credenciais de cliente", "concessão implícita" e "concessão de credenciais de senha do proprietário do recurso". A credencial retornada ao cliente é um [token de acesso](#access-token)ou um [código de autorização](#authorization-code) (trocado posteriormente por um token de acesso), dependendo do tipo de concessão de autorização usado.

## <a name="authorization-server"></a>servidor de autorização

Conforme definido pela [estrutura de autorização OAuth2][OAuth2-Role-Def], o servidor responsável por emitir tokens de acesso ao [cliente](#client-application) depois de autenticar com êxito o [proprietário do recurso](#resource-owner) e obter sua autorização. Um [aplicativo cliente](#client-application) interage com o servidor de autorização em tempo de execução por meio de seus pontos de extremidade de [autorização](#authorization-endpoint) e [token](#token-endpoint) , de acordo com as [concessões de autorização](#authorization-grant)definidas pelo OAuth2.

No caso da integração de aplicativos da plataforma de identidade da Microsoft, a plataforma de identidade da Microsoft implementa a função de servidor de autorização para aplicativos do Azure AD e APIs de serviço da Microsoft, por exemplo [Microsoft Graph APIs][Microsoft-Graph].

## <a name="claim"></a>enção

Um [token de segurança](#security-token) contém declarações, que fornecem asserções sobre uma entidade (como um [aplicativo cliente](#client-application) ou [proprietário do recurso](#resource-owner)) para outra entidade (como o [servidor de recursos](#resource-server)). As declarações são pares de nome/valor que retransmitem fatos sobre o assunto do token (por exemplo, a entidade de segurança que foi autenticada pelo [servidor de autorização](#authorization-server)). As declarações presentes em um determinado token dependem de várias variáveis, incluindo o tipo de token, o tipo de credencial usado para autenticar o assunto, a configuração do aplicativo, etc.

Consulte [referência de token da plataforma de identidade da Microsoft][AAD-Tokens-Claims] para obter mais detalhes.

## <a name="client-application"></a>aplicativo cliente

Conforme definido pela [estrutura de autorização OAuth2][OAuth2-Role-Def], um aplicativo que faz solicitações de recursos protegidos em nome do [proprietário do recurso](#resource-owner). O termo "cliente" não implica em nenhuma característica de implementação de hardware específica (por exemplo, se o aplicativo é executado em um servidor, em uma área de trabalho ou em outros dispositivos).

Um aplicativo cliente solicita [autorização](#authorization) de um proprietário de recurso para participar de um fluxo de [concessão de autorização OAuth2](#authorization-grant) e pode acessar APIs/dados em nome do proprietário do recurso. A estrutura de autorização OAuth2 [define dois tipos de clientes][OAuth2-Client-Types], "confidencial" e "público", com base na capacidade do cliente de manter a confidencialidade de suas credenciais. Os aplicativos podem implementar um [cliente Web (confidencial)](#web-client) que é executado em um servidor Web, um [cliente nativo (público)](#native-client) instalado em um dispositivo ou um cliente baseado em [agente de usuário (público)](#user-agent-based-client) que é executado no navegador de um dispositivo.

## <a name="consent"></a>Consentimento

O processo de um [proprietário de recurso](#resource-owner) que concede autorização a um [aplicativo cliente](#client-application), para acessar recursos protegidos sob [permissões](#permissions)específicas, em nome do proprietário do recurso. Dependendo das permissões solicitadas pelo cliente, um administrador ou usuário será solicitado a fornecer consentimento para permitir o acesso a seus dados da organização/individuais, respectivamente. Observe que, em um cenário [multilocatário](#multi-tenant-application) , a entidade de [serviço](#service-principal-object) do aplicativo também é registrada no locatário do usuário que está consentindo.

Consulte a [estrutura de consentimento](consent-framework.md) para obter mais informações.

## <a name="id-token"></a>Token de ID

Um [token de segurança](#security-token) do [OpenID Connect][OpenIDConnect-ID-Token] fornecido por um [ponto de extremidade de autorização](#authorization-endpoint) [do servidor de autorização](#authorization-server) , que contém [declarações](#claim) referentes à autenticação de um [proprietário de recurso](#resource-owner)do usuário final. Como um token de acesso, os tokens de ID também são representados como um [JWT (token Web JSON)][JWT]assinado digitalmente. Ao contrário de um token de acesso, no entanto, as declarações de um token de ID não são usadas para fins relacionados ao acesso a recursos e especificamente ao controle de acesso.

Consulte [referência de token da plataforma de identidade da Microsoft][AAD-Tokens-Claims] para obter mais detalhes.

## <a name="microsoft-identity-platform"></a>Plataforma de identidades da Microsoft

A plataforma de identidade da Microsoft é uma evolução da plataforma para programadores e do serviço de identidade do Azure Active Directory (Azure AD). Permite que os programadores compilem aplicações que iniciam sessão em todas as identidades da Microsoft, obtenham tokens para chamar o Microsoft Graph, outras APIs da Microsoft ou APIs compiladas pelos programadores. É uma plataforma completa que consiste em um serviço de autenticação, bibliotecas, registro e configuração de aplicativos, documentação completa para desenvolvedores, exemplos de código e outros conteúdos de desenvolvedores. A plataforma de identidades da Microsoft suporta protocolos norma da indústria, tais como OAuth 2.0 e o OpenID Connect. Consulte [sobre a plataforma de identidade da Microsoft](about-microsoft-identity-platform.md) para obter mais detalhes.

## <a name="multi-tenant-application"></a>aplicativo multilocatário

Uma classe de aplicativo que habilita a entrada e o [consentimento](#consent) por usuários provisionados em qualquer [locatário](#tenant)do Azure AD, incluindo locatários diferentes daquele em que o cliente está registrado. Os aplicativos [cliente nativos](#native-client) são multilocatários por padrão, enquanto os aplicativos Web [Client](#web-client) e [Web Resource/API](#resource-server) têm a capacidade de selecionar entre um único ou vários locatários. Por outro lado, um aplicativo Web registrado como um único locatário permitiria apenas entradas de contas de usuário provisionadas no mesmo locatário como aquele em que o aplicativo está registrado.

Consulte [como entrar em qualquer usuário do Azure ad usando o padrão de aplicativo multilocatário][AAD-Multi-Tenant-Overview] para obter mais detalhes.

## <a name="native-client"></a>cliente nativo

Um tipo de [aplicativo cliente](#client-application) que é instalado nativamente em um dispositivo. Como todo o código é executado em um dispositivo, ele é considerado um cliente "público" devido à sua incapacidade de armazenar credenciais de forma privada/confidencial. Consulte [perfis e tipos de cliente do OAuth2][OAuth2-Client-Types] para obter mais detalhes.

## <a name="permissions"></a>permissões

Um [aplicativo cliente](#client-application) obtém acesso a um [servidor de recursos](#resource-server) declarando solicitações de permissão. Dois tipos estão disponíveis:

* Permissões "delegadas", que especificam o acesso [baseado em escopo](#scopes) usando a autorização delegada do [proprietário do recurso](#resource-owner)conectado, são apresentadas ao recurso em tempo de execução como [declarações "scp"](#claim) no [token de acesso](#access-token)do cliente.
* As permissões de "aplicativo", que especificam o acesso [baseado em função](#roles) usando as credenciais/identidade do aplicativo cliente, são apresentadas ao recurso em tempo de execução como [declarações de "funções"](#claim) no token de acesso do cliente.

Elas também são apresentadas durante o processo de [consentimento](#consent) , dando ao administrador ou ao proprietário do recurso a oportunidade de conceder/negar o acesso do cliente aos recursos em seu locatário.

As solicitações de permissão são configuradas na página **permissões de API** para um aplicativo no [portal do Azure][AZURE-portal], selecionando as "permissões delegadas" e "permissões de aplicativo" desejadas (a última opção requer associação na função de administrador global). Como um [cliente público](#client-application) não pode manter as credenciais com segurança, ele só pode solicitar permissões delegadas, enquanto um [cliente confidencial](#client-application) tem a capacidade de solicitar permissões delegadas e de aplicativo. O objeto de [aplicativo](#application-object) do cliente armazena as permissões declaradas em sua [Propriedade requiredResourceAccess][Graph-App-Resource].

## <a name="resource-owner"></a>proprietário do recurso

Conforme definido pela [estrutura de autorização OAuth2][OAuth2-Role-Def], uma entidade capaz de conceder acesso a um recurso protegido. Quando o proprietário do recurso é uma pessoa, ele é conhecido como um usuário final. Por exemplo, quando um [aplicativo cliente](#client-application) deseja acessar a caixa de correio de um usuário por meio da [API Microsoft Graph][Microsoft-Graph], ele requer permissão do proprietário do recurso da caixa de correio.

## <a name="resource-server"></a>servidor de recursos

Conforme definido pela [estrutura de autorização OAuth2][OAuth2-Role-Def], um servidor que hospeda recursos protegidos, capaz de aceitar e responder a solicitações de recursos protegidos por [aplicativos cliente](#client-application) que apresentam um [token de acesso](#access-token). Também conhecido como um servidor de recursos protegido ou aplicativo de recurso.

Um servidor de recursos expõe APIs e impõe o acesso a seus recursos protegidos por meio de [escopos](#scopes) e [funções](#roles), usando a estrutura de autorização OAuth 2,0. Os exemplos incluem a [API Microsoft Graph][Microsoft-Graph] que fornece acesso aos dados de locatário do Azure AD e as APIs do Office 365 que fornecem acesso a dados como email e calendário. 

Assim como um aplicativo cliente, a configuração de identidade do aplicativo de recurso é estabelecida por meio do [registro](#application-registration) em um locatário do Azure AD, fornecendo o aplicativo e o objeto de entidade de serviço. Algumas APIs fornecidas pela Microsoft, como a API de Microsoft Graph, têm entidades de serviço previamente registradas disponibilizadas em todos os locatários durante o provisionamento.

## <a name="roles"></a>funções

Como os [escopos](#scopes), as funções fornecem uma maneira para um [servidor de recursos](#resource-server) controlar o acesso aos seus recursos protegidos. Há dois tipos: uma função de "usuário" implementa o controle de acesso baseado em função para usuários/grupos que exigem acesso ao recurso, enquanto uma função de "aplicativo" implementa o mesmo para [aplicativos cliente](#client-application) que exigem acesso.

As funções são cadeias de caracteres definidas pelo recurso (por exemplo, "Aprovador de despesas", "somente leitura", "Directory. ReadWrite. All"), gerenciadas no [portal do Azure][AZURE-portal] por meio do [manifesto do aplicativo](#application-manifest)do recurso e armazenadas na [Propriedade appRoles][Graph-Sp-Resource]do recurso. O portal do Azure também é usado para atribuir usuários a funções de "usuário" e configurar [permissões de aplicativo](#permissions) cliente para acessar uma função de "aplicativo".

Para obter uma discussão detalhada sobre as funções de aplicativo expostas pela API de Microsoft Graph, consulte [API do Graph escopos de permissão][Graph-Perm-Scopes]. Para obter um exemplo de implementação passo a passo, consulte [gerenciar o acesso usando o RBAC e o portal do Azure][AAD-RBAC].

## <a name="scopes"></a>âmbitos

Como as [funções](#roles), os escopos fornecem uma maneira para um [servidor de recursos](#resource-server) controlar o acesso aos seus recursos protegidos. Os escopos são usados para implementar o controle de acesso [baseado em escopo][OAuth2-Access-Token-Scopes] , para um [aplicativo cliente](#client-application) que recebeu acesso delegado ao recurso por seu proprietário.

Os escopos são cadeias de caracteres definidas pelo recurso (por exemplo, "mail. Read", "Directory. ReadWrite. All"), gerenciados no [portal do Azure][AZURE-portal] por meio do [manifesto do aplicativo](#application-manifest)do recurso e armazenados na [Propriedade oauth2Permissions][Graph-Sp-Resource]do recurso. O portal do Azure também é usado para configurar [permissões delegadas](#permissions) do aplicativo cliente para acessar um escopo.

Uma Convenção de nomenclatura de prática recomendada é usar um formato "Resource. Operation. Constraint". Para obter uma discussão detalhada dos escopos expostos pela API Microsoft Graph, consulte [API do Graph escopos de permissão][Graph-Perm-Scopes]. Para escopos expostos pelos serviços do Office 365, consulte [referência de permissões de API do office 365][O365-Perm-Ref].

## <a name="security-token"></a>token de segurança

Um documento assinado que contém declarações, como um token OAuth2 ou Asserção SAML 2,0. Para uma [concessão de autorização](#authorization-grant)OAuth2, um [token de acesso](#access-token) (OAuth2) e um [token de ID](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) são tipos de tokens de segurança, ambos são implementados como um [JWT (token Web JSON)][JWT].

## <a name="service-principal-object"></a>objeto de entidade de serviço

Quando você registra/atualiza um aplicativo no [portal do Azure][AZURE-portal], o portal cria/atualiza um objeto de [aplicativo](#application-object) e um objeto de entidade de serviço correspondente para esse locatário. O objeto Application *define* a configuração de identidade do aplicativo globalmente (em todos os locatários onde o aplicativo associado recebeu acesso) e é o modelo do qual seus objetos de entidade de serviço correspondentes são *derivados* para uso localmente em tempo de execução (em um locatário específico).

Para obter mais informações, consulte [objetos de entidade de serviço e de aplicativo][AAD-App-SP-Objects].

## <a name="sign-in"></a>iniciar sessão

O processo de um [aplicativo cliente](#client-application) que inicia a autenticação do usuário final e captura o estado relacionado, com a finalidade de adquirir um [token de segurança](#security-token) e definir o escopo da sessão do aplicativo para esse estado. O estado pode incluir artefatos, como informações de perfil do usuário, e informações derivadas de declarações de token.

A função de entrada de um aplicativo normalmente é usada para implementar SSO (logon único). Ele também pode ser precedido por uma função de "inscrição", como o ponto de entrada para um usuário final obter acesso a um aplicativo (na primeira entrada). A função de inscrição é usada para coletar e manter o estado adicional específico para o usuário e pode exigir [consentimento do usuário](#consent).

## <a name="sign-out"></a>de fim de sessão

O processo de desautenticação de um usuário final, desanexando o estado do usuário associado à sessão do [aplicativo cliente](#client-application) durante a [entrada](#sign-in)

## <a name="tenant"></a>tenant

Uma instância de um diretório do Azure AD é conhecida como um locatário do Azure AD. Ele fornece vários recursos, incluindo:

* um serviço de registro para aplicativos integrados
* autenticação de contas de usuário e aplicativos registrados
* Os pontos de extremidade REST são necessários para dar suporte a vários protocolos, incluindo OAuth2 e SAML, incluindo o [Endpoint de autorização](#authorization-endpoint), o ponto de [extremidade do token](#token-endpoint) e o ponto de extremidade "comum" usado por [aplicativos multilocatários](#multi-tenant-application).

Os locatários do Azure AD são criados/associados às assinaturas do Azure e do Office 365 durante a inscrição, fornecendo Gerenciamento de Identidades e Acesso recursos para a assinatura. Os administradores de assinatura do Azure também podem criar locatários adicionais do Azure AD por meio do portal do Azure. Consulte [como obter um locatário Azure Active Directory][AAD-How-To-Tenant] para obter detalhes sobre as várias maneiras pelas quais você pode obter acesso a um locatário. Veja [como as assinaturas do Azure estão associadas a Azure Active Directory][AAD-How-Subscriptions-Assoc] para obter detalhes sobre a relação entre assinaturas e um locatário do Azure AD.

## <a name="token-endpoint"></a>ponto de extremidade do token

Um dos pontos de extremidade implementados pelo [servidor de autorização](#authorization-server) para dar suporte a [concessões de autorização](#authorization-grant)OAuth2. Dependendo da concessão, ele pode ser usado para adquirir um token de [acesso](#access-token) (e um token de "atualização" relacionado) para um [cliente](#client-application)ou [token de ID](#id-token) quando usado com o protocolo [OpenID Connect][OpenIDConnect] .

## <a name="user-agent-based-client"></a>Cliente baseado em agente do usuário

Um tipo de [aplicativo cliente](#client-application) que baixa o código de um servidor Web e é executado dentro de um agente de usuário (por exemplo, um navegador da Web), como um aplicativo de página única (Spa). Como todo o código é executado em um dispositivo, ele é considerado um cliente "público" devido à sua incapacidade de armazenar credenciais de forma privada/confidencial. Para obter mais informações, consulte [perfis e tipos de cliente do OAuth2][OAuth2-Client-Types].

## <a name="user-principal"></a>entidade de usuário

Semelhante ao modo como um objeto de entidade de serviço é usado para representar uma instância de aplicativo, um objeto de entidade de usuário é outro tipo de entidade de segurança, que representa um usuário. O [tipo de recurso de usuário][Graph-User-Resource] Microsoft Graph define o esquema para um objeto de usuário, incluindo propriedades relacionadas ao usuário, como nome e sobrenome, nome principal de usuário, associação de função de diretório, etc. Isso fornece a configuração de identidade do usuário para o Azure AD estabelecer uma entidade de usuário em tempo de execução. A entidade de usuário é usada para representar um usuário autenticado para logon único, registrar a delegação de [consentimento](#consent) , tomar decisões de controle de acesso etc.

## <a name="web-client"></a>cliente Web

Um tipo de [aplicativo cliente](#client-application) que executa todo o código em um servidor Web e é capaz de funcionar como um cliente "confidencial" armazenando com segurança suas credenciais no servidor. Para obter mais informações, consulte [perfis e tipos de cliente do OAuth2][OAuth2-Client-Types].

## <a name="next-steps"></a>Passos seguintes

O [Guia do desenvolvedor da plataforma de identidade da Microsoft][AAD-Dev-Guide] é a página de aterrissagem a ser usada para todos os tópicos relacionados ao desenvolvimento da plataforma de identidade da Microsoft, incluindo uma visão geral da [integração de aplicativos][AAD-How-To-Integrate] e os conceitos básicos da [autenticação da plataforma Microsoft Identity e dos cenários de autenticação com suporte][AAD-Auth-Scenarios]. Você também pode encontrar exemplos de código & tutoriais sobre como entrar em funcionamento rapidamente no [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Use a seção de comentários a seguir para fornecer comentários e ajudar a refinar e formatar esse conteúdo, incluindo solicitações de novas definições ou atualização de existentes!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal?view=graph-rest-beta
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
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
