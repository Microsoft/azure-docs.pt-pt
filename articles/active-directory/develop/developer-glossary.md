---
title: Glossário de programador do Microsoft identity platform | Azure
description: Uma lista de termos de conceitos de programador do Microsoft identity platform frequentemente utilizado e as funcionalidades.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c989b690e9537dcaaf3710996474a1b8b99826b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962749"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Glossário de programador do Microsoft identity platform

Este artigo contém definições para alguns dos principais conceitos de programador e terminologia, que são úteis durante o aprendizado sobre o desenvolvimento de aplicativos com a plataforma de identidade da Microsoft.

## <a name="access-token"></a>token de acesso

Um tipo de [token de segurança](#security-token) emitido por uma [servidor de autorização](#authorization-server)e utilizado por um [aplicação de cliente](#client-application) para poder aceder a um [recurso servidorprotegido](#resource-server). Normalmente na forma de um [JSON Web Token (JWT)][JWT], o token incorpora a autorização concedida ao cliente pelo [proprietário do recurso](#resource-owner), para um nível de acesso. O token contém todos os aplicável [afirmações](#claim) sobre o assunto, permitindo que a aplicação de cliente para utilizá-la como uma forma de credencial ao aceder a um determinado recurso. Isso também elimina a necessidade do proprietário do recurso para expor as credenciais para o cliente.

Tokens de acesso são, às vezes, referidos como "Utilizador + aplicação" ou "Só de aplicação", consoante as credenciais que está a ser representadas. Por exemplo, quando uma aplicação cliente utiliza a:

* [Concessão de autorização de "Código de autorização"](#authorization-grant), o utilizador final autentica pela primeira vez como o proprietário do recurso, a delegação de autorização para o cliente para aceder ao recurso. O cliente é autenticado posteriormente ao obter o token de acesso. O token pode às vezes, referido mais especificamente, como um token de "Utilizador + aplicação", pois ele representa o utilizador que autorizou a aplicação de cliente e o aplicativo.
* [Concessão de autorização de "Credenciais de cliente"](#authorization-grant), o cliente fornece a autenticação do única, a funcionar sem autenticação/autorização do-proprietário do recurso, para que o token, às vezes, pode ser referido como um token "Só de aplicação".

Ver [plataforma de identidade do Microsoft referência de Token] [ AAD-Tokens-Claims] para obter mais detalhes.

## <a name="application-id-client-id"></a>ID da aplicação (ID de cliente)

Os problemas de identificador exclusivo do Azure AD para um registo de aplicação que identifica uma aplicação específica e as configurações associadas. Este ID de aplicação ([ID de cliente](https://tools.ietf.org/html/rfc6749#page-15)) é utilizado quando executar a autenticação de pedidos e é fornecido para as bibliotecas de autenticação no tempo de desenvolvimento. O ID da aplicação (ID de cliente) não é um segredo.

## <a name="application-manifest"></a>manifesto da aplicação

Um recurso fornecido pelos [portal do Azure][AZURE-portal], que produz uma representação JSON de configuração de identidade da aplicação, utilizada como um mecanismo para a atualização está associado [ Aplicativo] [ AAD-Graph-App-Entity] e [ServicePrincipal] [ AAD-Graph-Sp-Entity] entidades. Ver [compreender o manifesto da aplicação do Azure Active Directory] [ AAD-App-Manifest] para obter mais detalhes.

## <a name="application-object"></a>objeto de aplicativo

Quando Registre-se/atualizar uma aplicação no [portal do Azure][AZURE-portal], o portal cria/atualizações de um objeto de aplicativo e odpovídající [objeto principal do serviço](#service-principal-object) para esse inquilino. O objeto de aplicativo *define* a aplicação da configuração de identidade globalmente (em todos os inquilinos onde tem acesso), fornecendo um modelo a partir dos quais são seus objetos de principal de serviço correspondente  *derivado* para utilizar localmente no tempo de execução (num inquilino específico).

Para obter mais informações, consulte [objetos de Principal de serviço de aplicação e][AAD-App-SP-Objects].

## <a name="application-registration"></a>Registo de aplicação

Para permitir que um aplicativo integrar com e delegar funções de gestão de acesso e identidade para o Azure AD, tem de ser registado com um Azure AD [inquilino](#tenant). Quando registar a sua aplicação com o Azure AD, está a fornecer uma configuração de identidade para a sua aplicação, permitindo-lhe integrar com o Azure AD e usar recursos como:

* Gestão robusta de início de sessão único com o Azure AD Identity Management e [OpenID Connect] [ OpenIDConnect] implementação de protocolo
* Mediadas acesso ao [recursos protegidos](#resource-server) por [aplicativos cliente](#client-application), através do OAuth 2.0 [servidor de autorização](#authorization-server)
* [Estrutura do consentimento](#consent) para gerir o acesso de cliente para recursos protegidos, com base na autorização de proprietário do recurso.

Ver [integrar aplicações com o Azure Active Directory] [ AAD-Integrating-Apps] para obter mais detalhes.

## <a name="authentication"></a>autenticação

O ato de desafiador uma entidade para credenciais legítimas, fornecendo a base para a criação de uma entidade de segurança a ser utilizado para controlo de acesso e identidade. Durante uma [concessão de autorização de OAuth2](#authorization-grant) por exemplo, a autenticação de terceiros é preencher a função do [proprietário do recurso](#resource-owner) ou [aplicação cliente](#client-application), consoante o concessão utilizado.

## <a name="authorization"></a>Autorização

O ato de conceder uma permissão de principal de segurança autenticado para fazer algo. Existem dois casos de utilização principais no modelo de programação do Azure AD:

* Durante uma [concessão de autorização de OAuth2](#authorization-grant) fluxo: quando o [proprietário do recurso](#resource-owner) concede autorização para o [aplicação de cliente](#client-application), permitindo ao cliente aceder ao recurso recursos do proprietário.
* Durante o acesso a recursos pelo cliente: conforme implementado pela [servidor do recurso](#resource-server), utilizando o [afirmação](#claim) valores presentes no [token de acesso](#access-token) para tomar decisões de controlo de acesso baseie-los.

## <a name="authorization-code"></a>código de autorização

Um resumo de duração "token" fornecido para uma [aplicação de cliente](#client-application) pela [ponto final de autorização](#authorization-endpoint), como parte do fluxo de "código de autorização", uma do OAuth2 quatro [concedeaautorização](#authorization-grant). O código é devolvido para a aplicação de cliente em resposta a autenticação de um [proprietário do recurso](#resource-owner), que indica que o proprietário do recurso tem autorização para acessar os recursos solicitados delegada. Como parte do fluxo, o código mais tarde é trocado por um [token de acesso](#access-token).

## <a name="authorization-endpoint"></a>ponto final de autorização

Um dos pontos de extremidade implementados pelos [servidor de autorização](#authorization-server), utilizado para interagir com o [proprietário do recurso](#resource-owner) a fim de fornecer um [concessão de autorização](#authorization-grant) durante um OAuth2 fluxo de concessão de autorização. Consoante o fluxo de concessão de autorização utilizado, a concessão real, fornecida pode variar, incluindo uma [código de autorização](#authorization-code) ou [token de segurança](#security-token).

Consulte a especificação OAuth2 [tipos de concessão de autorização] [ OAuth2-AuthZ-Grant-Types] e [ponto final de autorização] [ OAuth2-AuthZ-Endpoint] secções e o [OpenIDConnect especificação] [ OpenIDConnect-AuthZ-Endpoint] para obter mais detalhes.

## <a name="authorization-grant"></a>concessão de autorização

Uma credencial que representa a [do proprietário do recurso](#resource-owner) [autorização](#authorization) para aceder os recursos protegidos, concedidos a um [aplicação de cliente](#client-application). Uma aplicação cliente pode utilizar um da [conceder quatro tipos definidos pela estrutura de autorização de OAuth2] [ OAuth2-AuthZ-Grant-Types] para obter uma concessão, dependendo do tipo/requisitos do cliente: "concessão do código de autorização", "cliente concessão de credenciais","concessão implícita"e"credenciais de palavra-passe de proprietário do recurso concedem". A credencial devolvida ao cliente é um [token de acesso](#access-token), ou uma [código de autorização](#authorization-code) (trocado mais tarde por um token de acesso), dependendo do tipo de concessão de autorização utilizado.

## <a name="authorization-server"></a>servidor de autorização

Conforme definido pela [Framework de autorização de OAuth2][OAuth2-Role-Def], o servidor responsável pela emissão acesso tokens para o [cliente](#client-application) após a autenticação bem-sucedida a [proprietário do recurso](#resource-owner) e obter a respetiva autorização. Uma [aplicação de cliente](#client-application) interage com o servidor de autorização em tempo de execução por meio de seu [autorização](#authorization-endpoint) e [token](#token-endpoint) pontos finais, de acordo com o OAuth2 definido [concessões de autorização](#authorization-grant).

No caso de integração de aplicações do Microsoft identity platform, plataforma de identidade da Microsoft implementa a função de servidor de autorização para aplicações do Azure AD e o serviço Microsoft APIs, por exemplo [Microsoft Graph APIs] [Microsoft-Graph].

## <a name="claim"></a>afirmação

Uma [token de segurança](#security-token) contém afirmações, que fornecem asserções cerca de uma entidade (como um [aplicação de cliente](#client-application) ou [proprietário do recurso](#resource-owner)) a outra entidade (como o [servidor do recurso](#resource-server)). As declarações são pares nome/valor que fatos sobre o assunto do token de reencaminhamento (por exemplo, a entidade de segurança que foi autenticada pela [servidor de autorização](#authorization-server)). As ações presentes num determinado token dependem de várias variáveis, incluindo o tipo de token, o tipo de credencial utilizada para autenticar o assunto, a configuração da aplicação, etc.

Ver [referência de token de plataforma do Microsoft identity] [ AAD-Tokens-Claims] para obter mais detalhes.

## <a name="client-application"></a>Aplicação de cliente

Conforme definido pela [Framework de autorização de OAuth2][OAuth2-Role-Def], pedidos de recursos em nome de protegidos de um aplicativo que faz com que o [proprietário do recurso](#resource-owner). O termo "cliente" não implica qualquer características de implementação de hardware específico (por exemplo, se o aplicativo é executado num servidor, uma área de trabalho ou outros dispositivos).

Uma aplicação cliente solicita [autorização](#authorization) de um proprietário do recurso para participar de um [concessão de autorização de OAuth2](#authorization-grant) fluxo e pode aceder a APIs/dados em nome do proprietário do recurso. A estrutura de autorização de OAuth2 [define dois tipos de clientes][OAuth2-Client-Types], "Confidencial" e "public", com base na capacidade do cliente de manter a confidencialidade das suas credenciais. As aplicações podem implementar um [(confidencial) de cliente web](#web-client) que é executada num servidor web, uma [cliente nativo (público)](#native-client) instaladas num dispositivo, ou um [baseada no utilizador-agente de cliente (público)](#user-agent-based-client)que é executada no browser do dispositivo.

## <a name="consent"></a>Consentimento

O processo de um [proprietário do recurso](#resource-owner) conceder autorização para um [aplicação de cliente](#client-application), para aceder a recursos protegidos sob específicas [permissões](#permissions), em nome do proprietário do recurso. Consoante as permissões solicitadas pelo cliente, um administrador ou usuário será solicitado o consentimento permitir o acesso aos dados da organização/indivíduo, respetivamente. Tenha em atenção que numa [multi-inquilino](#multi-tenant-application) cenário, o aplicativo [principal de serviço](#service-principal-object) também são armazenadas no inquilino do usuário que consente isso.

Ver [consentimento framework](consent-framework.md) para obter mais informações.

## <a name="id-token"></a>Token de ID

Uma [OpenID Connect] [ OpenIDConnect-ID-Token] [token de segurança](#security-token) fornecida por um [do servidor de autorização](#authorization-server) [dopontofinaldeautorização](#authorization-endpoint), que contém [afirmações](#claim) relativas para a autenticação de um utilizador final [proprietário do recurso](#resource-owner). Como um token de acesso, os tokens de ID também são representados como uma assinado digitalmente [JSON Web Token (JWT)][JWT]. Ao contrário de um token de acesso no entanto, afirmações um token de ID não é utilizado para fins relacionados com o acesso a recursos e controlo de acesso especificamente.

Ver [referência de token de plataforma do Microsoft identity] [ AAD-Tokens-Claims] para obter mais detalhes.

## <a name="microsoft-identity-platform"></a>Plataforma de identidade da Microsoft

A plataforma de identidade da Microsoft é uma evolução da plataforma para programadores e do serviço de identidade do Azure Active Directory (Azure AD). Permite que os programadores compilem aplicações que iniciam sessão em todas as identidades da Microsoft, obtenham tokens para chamar o Microsoft Graph, outras APIs da Microsoft ou APIs compiladas pelos programadores. É uma plataforma completa que consiste num serviço de autenticação, bibliotecas, registo de aplicação e configuração, documentação de programador completa, exemplos de código e outros conteúdos programador. A plataforma de identidade da Microsoft suporta protocolos padrão da indústria, tais como OAuth 2.0 e OpenID Connect. Ver [plataforma de identidade da Microsoft sobre](about-microsoft-identity-platform.md) para obter mais detalhes.

## <a name="multi-tenant-application"></a>aplicação multi-inquilino

Uma classe de aplicativo que permite o início de sessão e [consentimento](#consent) por utilizadores aprovisionados no qualquer Azure AD [inquilino](#tenant), incluindo inquilinos diferente em que o cliente está registado. [Cliente nativo](#native-client) os aplicativos são multi-inquilino por predefinição, enquanto [cliente web](#web-client) e [recursos/API web](#resource-server) aplicativos têm a capacidade de selecionar entre único ou vários inquilinos. Por outro lado, um aplicativo web registado como inquilino único, devem permitir que inícios de sessão de contas de utilizador aprovisionados no mesmo inquilino que aquele onde a aplicação fica registada.

Ver [como iniciar sessão a qualquer utilizador do Azure AD utilizando o padrão de aplicação multi-inquilino] [ AAD-Multi-Tenant-Overview] para obter mais detalhes.

## <a name="native-client"></a>Cliente nativo

Um tipo de [aplicação de cliente](#client-application) que é instalado nativamente num dispositivo. Uma vez que todo o código é executado num dispositivo, é considerado um cliente "public" devido a sua incapacidade de armazenar as credenciais em privado/confidencialmente. Ver [tipos de cliente de OAuth2 e perfis] [ OAuth2-Client-Types] para obter mais detalhes.

## <a name="permissions"></a>permissões

A [aplicação de cliente](#client-application) obtiver acesso a um [servidor recursos](#resource-server) declarando solicitações de permissão. Dois tipos estão disponíveis:

* "Delegado" permissões, que especificarem [com base no âmbito](#scopes) aceder com autorização delegada da sessão iniciada [proprietário do recurso](#resource-owner), são apresentados para o recurso em tempo de execução como ["scp" afirmações](#claim) no cliente do [token de acesso](#access-token).
* Permissões de "Aplicação", que especificarem [baseado em funções](#roles) aceder a utilizar credenciais/identidade a aplicação de cliente, são apresentados para o recurso em tempo de execução como [afirmações de "funções"](#claim) do cliente token de acesso.

Eles também surgir durante a [consentimento](#consent) processo, dando o administrador ou proprietário do recurso a oportunidade para conceder/negar o acesso de cliente para recursos no seu inquilino.

Solicitações de permissão são configuradas no "Aplicativos" / separador "Definições" a [portal do Azure][AZURE-portal], sob "Permissões obrigatórias", selecionando o "Permissões delegadas" e "a aplicação pretendida Permissões"(esse último requer a associação a função de Administrador Global). Porque uma [cliente público](#client-application) segura não é possível manter as credenciais, ele só pode solicitar permissões delegadas, enquanto um [cliente confidencial](#client-application) tem a capacidade de solicitar o delegado e de aplicações permissões. O cliente [objeto application](#application-object) armazena as permissões declaradas no seu [propriedade requiredResourceAccess][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>proprietário do recurso

Conforme definido pela [Framework de autorização de OAuth2][OAuth2-Role-Def], uma entidade com capacidade de conceder acesso a um recurso protegido. Quando o proprietário do recurso é uma pessoa, é referido como um utilizador final. Por exemplo, quando um [aplicação de cliente](#client-application) quiser acessar a caixa de correio de um usuário por meio da [Microsoft Graph API][Microsoft-Graph], requer permissão do proprietário do recurso do caixa de correio.

## <a name="resource-server"></a>servidor de recurso

Conforme definido pela [Framework de autorização de OAuth2][OAuth2-Role-Def], protegida de um servidor que os anfitriões protegidos recursos, capazes de aceitar e responder a pedidos de recursos por [cliente aplicativos](#client-application) que apresentem um [token de acesso](#access-token). Também conhecido como um servidor de recurso protegido, ou aplicação de recurso.

Um servidor de recurso expõe as APIs e impõe o acesso aos seus recursos protegidos através de [âmbitos](#scopes) e [funções](#roles), usando a estrutura de autorização do OAuth 2.0. Os exemplos incluem o Azure AD Graph API, que fornece acesso a dados de inquilino do Azure AD e as APIs do Office 365 que fornecem acesso a dados, como email e calendário. Ambas as opções também estão acessíveis através da [API do Microsoft Graph][Microsoft-Graph].

Assim como um aplicativo de cliente, a configuração de identidade da aplicação de recurso é estabelecida através de [registo](#application-registration) no inquilino do Azure AD, fornecendo a aplicação e o objeto principal do serviço. Algumas APIs disponibilizadas pela Microsoft, como o Azure AD Graph API, registou previamente disponibilizados em todos os inquilinos durante o aprovisionamento de principais de serviço.

## <a name="roles"></a>funções

Como [âmbitos](#scopes), as funções fornecem uma forma de um [servidor recursos](#resource-server) para governar o acesso a seus recursos protegidos. Existem dois tipos: uma função de "usuário" implementa o controlo de acesso baseado em funções para os utilizadores/grupos que necessitam de acesso ao recurso, enquanto uma função de "aplicativo" implementa o mesmo para [aplicativos cliente](#client-application) que necessitam de acesso.

Funções são definidas pelo recurso cadeias de caracteres (por exemplo "aprovador da despesa", "Só de leitura", "Directory.ReadWrite.All"), gerido no [portal do Azure] [ AZURE-portal] por meio do recurso [aplicação manifesto](#application-manifest)e armazenados do recurso [propriedade appRoles][AAD-Graph-Sp-Entity]. O portal do Azure também é utilizado para atribuir utilizadores a funções de "user" e configurar cliente [permissões de aplicação](#permissions) para aceder a uma função de "aplicação".

Para uma discussão detalhada sobre as funções de aplicação exposta pelo Azure AD Graph API, consulte [âmbitos de permissão de API do Graph][AAD-Graph-Perm-Scopes]. Para obter um exemplo de implementação passo a passo, consulte [gerir o acesso com RBAC e o portal do Azure][AAD-RBAC].

## <a name="scopes"></a>âmbitos

Como [funções](#roles), âmbitos de fornecem uma forma de um [servidor recursos](#resource-server) para governar o acesso a seus recursos protegidos. Âmbitos são usados para implementar [com base no âmbito] [ OAuth2-Access-Token-Scopes] o controlo de acesso, para um [aplicação de cliente](#client-application) que tenha recebido acesso delegado para o recurso pelo respetivo proprietário.

Âmbitos são definidos pelo recurso cadeias de caracteres (por exemplo "Mail.Read", "Directory.ReadWrite.All"), geridos no [portal do Azure] [ AZURE-portal] por meio do recurso [manifesto de aplicação](#application-manifest)e armazenados no recurso da [propriedade oauth2Permissions][AAD-Graph-Sp-Entity]. O portal do Azure também é utilizado para configurar a aplicação cliente [permissões delegadas](#permissions) para um âmbito de acesso.

É uma convenção de nomenclatura de prática recomendada, utilize um formato de "resource.operation.constraint". Para um debate detalhado dos âmbitos expostos pelo Azure AD Graph API, consulte [âmbitos de permissão de API do Graph][AAD-Graph-Perm-Scopes]. Para âmbitos expostos pelo serviços do Office 365, consulte [referência de permissões de API do Office 365][O365-Perm-Ref].

## <a name="security-token"></a>token de segurança

Um documento assinado que contêm afirmações, por exemplo, um OAuth2 token ou uma asserção de SAML 2.0. Para um OAuth2 [concessão de autorização](#authorization-grant), uma [token de acesso](#access-token) (OAuth2) e um [Token de ID](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) são tipos de tokens de segurança, que são implementados como um [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>objeto do principal de serviço

Quando Registre-se/atualizar uma aplicação no [portal do Azure][AZURE-portal], o portal cria/atualizações ambos um [objeto application](#application-object) e um objeto principal de serviço correspondente para esse inquilino. O objeto de aplicativo *define* a configuração da aplicação identidade globalmente (em todos os inquilinos em que o aplicativo associado tenha sido concedido acesso), e é o modelo a partir do qual seu principal de serviço correspondente objeto (s) é *derivado* para utilizar localmente no tempo de execução (num inquilino específico).

Para obter mais informações, consulte [objetos de Principal de serviço de aplicação e][AAD-App-SP-Objects].

## <a name="sign-in"></a>iniciar sessão

O processo de um [aplicação de cliente](#client-application) iniciar autenticação do utilizador final e a captura de relacionados com o estado, para efeitos de aquisição de um [token de segurança](#security-token) e a sessão do aplicativo para esse Estado de controlo de âmbito. Pode incluir artefactos, tais como informações de perfil do usuário e informações derivam de afirmações de token de estado.

A função de início de sessão de um aplicativo é normalmente utilizada para implementar o início de sessão único (SSO). Ele poderá também ser precedido por uma função de "inscrição", como o ponto de entrada para um utilizador final obter acesso a um aplicativo (após o primeiro início de sessão). A função de inscrição é utilizada para recolher e persistir o estado adicional específico do usuário e podem exigir [consentimento do utilizador](#consent).

## <a name="sign-out"></a>de fim de sessão

O processo de unauthenticating um utilizador final, desanexar o estado do utilizador associada a [aplicação de cliente](#client-application) sessão durante [início de sessão](#sign-in)

## <a name="tenant"></a>tenant

Uma instância de um diretório do Azure AD é referida como um inquilino do Azure AD. Ele fornece vários recursos, incluindo:

* um serviço de registo para aplicações integradas
* autenticação de contas de usuário e aplicativos registrados
* Pontos finais REST necessários para suportar vários protocolos, incluindo o OAuth2 e o SAML, incluindo o [ponto final de autorização](#authorization-endpoint), [ponto final do token](#token-endpoint) e o ponto de final de "common" utilizado pelo [ aplicativos de multilocação](#multi-tenant-application).

Inquilinos do Azure AD são criados/associadas com subscrições do Azure e o Office 365 durante a inscrição, fornecendo identidade e gestão de acesso a recursos da subscrição. Os administradores de subscrição do Azure também podem criar mais inquilinos do Azure AD através do portal do Azure. Ver [como obter um inquilino do Azure Active Directory] [ AAD-How-To-Tenant] para obter detalhes sobre as várias formas, pode obter acesso a um inquilino. Ver [subscrições do Azure como estão associadas com o Azure Active Directory] [ AAD-How-Subscriptions-Assoc] para obter detalhes sobre a relação entre subscrições e um inquilino do Azure AD.

## <a name="token-endpoint"></a>ponto final do token

Um dos pontos de extremidade implementados pelos [servidor de autorização](#authorization-server) para suportar o OAuth2 [concessões de autorização](#authorization-grant). Consoante a concessão, ele pode ser utilizado para adquirir uma [token de acesso](#access-token) (e de token "Atualizar" relacionados) para um [cliente](#client-application), ou [token de ID](#id-token) quando utilizado com o [OpenID Ligue-se] [ OpenIDConnect] protocolo.

## <a name="user-agent-based-client"></a>Baseada no utilizador-agente de cliente

Um tipo de [aplicação de cliente](#client-application) que transfere o código de um servidor web e seja executada dentro de um agente de utilizador (por exemplo, um navegador da web), tal como uma aplicação de página única (SPA). Uma vez que todo o código é executado num dispositivo, é considerado um cliente "public" devido a sua incapacidade de armazenar as credenciais em privado/confidencialmente. Para obter mais informações, consulte [tipos de cliente de OAuth2 e perfis][OAuth2-Client-Types].

## <a name="user-principal"></a>principal de utilizador

Semelhante à forma como um objeto principal de serviço é utilizado para representar uma instância de aplicação, um objeto principal de utilizador é outro tipo de entidade de segurança, que representa um utilizador. O Azure AD Graph [entidade User] [ AAD-Graph-User-Entity] define o esquema para um objeto de utilizador, incluindo propriedades relacionadas ao usuário, como o nome próprio e apelido, nome principal de utilizador, associação de função de diretório, etc. Isso fornece a configuração de identidade de utilizador para o Azure AD estabelecer um principal de utilizador em tempo de execução. O principal de utilizador é utilizado para representar um utilizador autenticado para o início de sessão único, gravar [consentimento](#consent) delegação, tomar decisões de controlo de acesso, etc.

## <a name="web-client"></a>cliente Web

Um tipo de [aplicação de cliente](#client-application) que executa o código todos os num servidor web e capaz de funcione como um cliente "Confidencial" armazenando com segurança as suas credenciais no servidor. Para obter mais informações, consulte [tipos de cliente de OAuth2 e perfis][OAuth2-Client-Types].

## <a name="next-steps"></a>Passos Seguintes

O [Guia do programador do Microsoft identity platform] [ AAD-Dev-Guide] é a página de destino a utilizar para todos os Microsoft plataforma relacionados ao desenvolvimento tópicos de identidade, incluindo uma descrição geral de [aplicação integração] [ AAD-How-To-Integrate] e as noções básicas de [autenticação de plataforma de identidade do Microsoft e cenários de autenticação suportados][AAD-Auth-Scenarios]. Também pode encontrar exemplos de códigos e tutoriais sobre como obter a trabalhar rapidamente diante [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Utilize a seguinte secção de comentários para fornecer comentários e ajudar a refinar e moldar este conteúdo, incluindo pedidos de existência de novas definições ou atualizar os existentes!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
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
