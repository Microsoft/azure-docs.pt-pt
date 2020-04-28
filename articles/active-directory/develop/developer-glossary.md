---
title: Glossário de desenvolvedor de plataforma de identidade da Microsoft Azure
description: Uma lista de termos para conceitos e funcionalidades de desenvolvedor de plataformas de identidade da Microsoft comumente usados.
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
ms.openlocfilehash: 9709cd3b6036b384fd9212a522c191d0695b9bb4
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82161729"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Glossário de desenvolvedor de plataforma de identidade microsoft

Este artigo contém definições para alguns dos conceitos e terminologia fundamentais do desenvolvedor, que são úteis quando aprendem sobre o desenvolvimento de aplicações usando a plataforma de identidade da Microsoft.

## <a name="access-token"></a>ficha de acesso

Um tipo de [ficha](#security-token) de segurança emitida por um servidor de [autorização,](#authorization-server)e utilizada por uma [aplicação de cliente](#client-application) para aceder a um servidor de recursos [protegidos.](#resource-server) Tipicamente sob a forma de um [Token Web JSON (JWT),][JWT]o símbolo incorpora a autorização concedida ao cliente pelo proprietário do [recurso,](#resource-owner)para um nível de acesso solicitado. O símbolo contém todas as [reclamações](#claim) aplicáveis sobre o assunto, permitindo que a aplicação do cliente o utilize como forma de credencial ao aceder a um determinado recurso. Isto também elimina a necessidade de o proprietário do recurso expor credenciais ao cliente.

As fichas de acesso só são válidas por um curto período de tempo e não podem ser revogadas. Um servidor de autorização também pode emitir um [token de atualização](#refresh-token) quando o token de acesso for emitido. As fichas de atualização são normalmente fornecidas apenas a aplicações confidenciais do cliente.

As fichas de acesso são por vezes referidas como "User+App" ou "App-Only", dependendo das credenciais representadas. Por exemplo, quando uma aplicação de cliente utiliza o:

* Bolsa de [autorização "código](#authorization-grant)de autorização", o utilizador final autentica primeiro como proprietário de recursos, delegando autorização ao cliente para aceder ao recurso. O cliente autentica-se depois ao obter o sinal de acesso. O símbolo pode por vezes ser referido mais especificamente como um símbolo "User+App", uma vez que representa tanto o utilizador que autorizou a aplicação do cliente, como a aplicação.
* Bolsa de [autorização "credenciais](#authorization-grant)de cliente", o cliente fornece a autenticação exclusiva, funcionando sem a autenticação/autorização do proprietário do recurso, pelo que o símbolo pode por vezes ser referido como um símbolo "App-Only".

Consulte a [plataforma de identidade da Microsoft Token Reference][AAD-Tokens-Claims] para obter mais detalhes.

## <a name="application-id-client-id"></a>ID de aplicação (ID do cliente)

O identificador único Azure AD emite para um registo de aplicação que identifica uma aplicação específica e as configurações associadas. Esta identificação da aplicação[(ID do cliente)](https://tools.ietf.org/html/rfc6749#page-15)é utilizada na realização de pedidos de autenticação e é fornecida às bibliotecas de autenticação em tempo de desenvolvimento. O ID da aplicação (ID do cliente) não é um segredo.

## <a name="application-manifest"></a>manifesto de aplicação

Uma funcionalidade fornecida pelo [portal Azure,][AZURE-portal]que produz uma representação JSON da configuração de identidade da aplicação, utilizada como mecanismo de atualização das suas entidades associadas de [Aplicação][Graph-App-Resource] e [Serviços Principais.][Graph-Sp-Resource] Consulte [o manifesto de candidatura do Azure Ative Directory][AAD-App-Manifest] para obter mais detalhes.

## <a name="application-object"></a>objeto de aplicação

Ao registar/atualizar uma aplicação no [portal Azure,][AZURE-portal]o portal cria/atualiza tanto um objeto de aplicação como um [objeto principal](#service-principal-object) de serviço correspondente para esse inquilino. O objeto de aplicação *define* a configuração de identidade da aplicação a nível global (em todos os inquilinos onde tem acesso), fornecendo um modelo a partir do qual o seu(s) principal objeto de serviço correspondente é *derivado* para uso local no tempo de funcionamento (em um inquilino específico).

Para mais informações, consulte [Objetos Principais][AAD-App-SP-Objects]de Aplicação e Serviço .

## <a name="application-registration"></a>registo de aplicação

Para permitir a integração de um pedido de integração e delegação de funções de Identidade e Gestão de Acesso à Azure AD, deve ser registado com um [inquilino](#tenant)da AD Azure. Ao registar a sua aplicação no Azure AD, está a fornecer uma configuração de identidade para a sua aplicação, permitindo-lhe integrar-se com o Azure AD e utilizar funcionalidades como:

* Gestão robusta do Sign-On Único utilizando a Gestão de Identidade aD Azure e a implementação do protocolo [OpenID Connect][OpenIDConnect]
* Acesso intermediado a [recursos protegidos](#resource-server) por [aplicações de clientes,](#client-application)via servidor de [autorização](#authorization-server) OAuth 2.0
* [Quadro de consentimento](#consent) para gerir o acesso dos clientes a recursos protegidos, com base na autorização do proprietário de recursos.

Consulte a Integração de [aplicações com o Diretório Ativo Azure][AAD-Integrating-Apps] para mais detalhes.

## <a name="authentication"></a>autenticação

O ato de desafiar uma parte por credenciais legítimas, fornecendo as bases para a criação de um diretor de segurança para ser usado para o controlo de identidade e acesso. Durante uma concessão de [autorização OAuth2,](#authorization-grant) por exemplo, a parte autenticada está a preencher o papel de proprietário de [recursos](#resource-owner) ou aplicação de [cliente,](#client-application)dependendo da subvenção utilizada.

## <a name="authorization"></a>autorização

O ato de conceder a um diretor de segurança autenticado permissão para fazer algo. Existem dois casos de utilização principais no modelo de programação do Azure AD:

* Durante um fluxo de concessão de [autorização OAuth2:](#authorization-grant) quando o proprietário do [recurso](#resource-owner) concede autorização à aplicação do [cliente,](#client-application)permitindo ao cliente aceder aos recursos do proprietário do recurso.
* Durante o acesso de recursos pelo cliente: conforme implementado pelo servidor de [recursos,](#resource-server)utilizando os valores de [reclamação](#claim) presentes no [token](#access-token) de acesso para tomar decisões de controlo de acesso com base neles.

## <a name="authorization-code"></a>código de autorização

Um "token" de curta duração fornecido a um pedido de [cliente](#client-application) pelo ponto final de [autorização,](#authorization-endpoint)como parte do fluxo de "código de autorização", uma das quatro [autorizações](#authorization-grant)de autorização da OAuth2. O código é devolvido à aplicação do cliente em resposta à autenticação de um proprietário de [recursos,](#resource-owner)indicando que o proprietário do recurso delegou autorização para aceder aos recursos solicitados. Como parte do fluxo, o código é posteriormente redimido para um [sinal de acesso](#access-token).

## <a name="authorization-endpoint"></a>ponto final de autorização

Um dos pontos finais implementados pelo servidor de [autorização,](#authorization-server)utilizado para interagir com o proprietário do [recurso](#resource-owner) para fornecer uma [concessão](#authorization-grant) de autorização durante um fluxo de autorização OAuth2. Dependendo do fluxo de concessão de autorização utilizado, a subvenção efetiva fornecida pode variar, incluindo um código de [autorização](#authorization-code) ou um símbolo de [segurança.](#security-token)

Consulte os tipos de [concessão][OAuth2-AuthZ-Grant-Types] de autorização da especificação OAuth2 e as secções [de ponto final][OAuth2-AuthZ-Endpoint] de autorização e a [especificação OpenIDConnect][OpenIDConnect-AuthZ-Endpoint] para obter mais detalhes.

## <a name="authorization-grant"></a>concessão de autorização

Uma credencial que representa a [autorização](#authorization) [do proprietário do recurso](#resource-owner) para aceder aos seus recursos protegidos, concedida a uma [aplicação do cliente.](#client-application) Uma aplicação de cliente pode utilizar um dos quatro tipos de [subvenção definidos pelo Quadro de Autorização da OAuth2][OAuth2-AuthZ-Grant-Types] para obter uma subvenção, dependendo do tipo/requisitos do cliente: "concessão de código de autorização", "concessão de credenciais de cliente", "subvenção implícita" e "concessão de credenciais de senha do proprietário de recursos". A credencial devolvida ao cliente é ou um símbolo de [acesso,](#access-token)ou um código de [autorização](#authorization-code) (trocado posteriormente por um token de acesso), dependendo do tipo de concessão de autorização utilizada.

## <a name="authorization-server"></a>servidor de autorização

Tal como definido pelo Quadro de [Autorização oAuth2,][OAuth2-Role-Def]o servidor responsável pela emissão de fichas de acesso ao [cliente](#client-application) após autenticar com sucesso o proprietário do [recurso](#resource-owner) e obter a sua autorização. Uma [aplicação de cliente](#client-application) interage com o servidor de autorização em tempo de execução através da sua [autorização](#authorization-endpoint) e pontos finais [simbólicos,](#token-endpoint) de acordo com as [subvenções](#authorization-grant)de autorização definidas OAuth2 .

No caso da integração da aplicação da plataforma de identidade microsoft, a plataforma de identidade da Microsoft implementa a função de servidor de autorização para aplicações AD Azure e APIs de serviço microsoft, por exemplo [APIs][Microsoft-Graph]do Microsoft Graph .

## <a name="claim"></a>reivindicação

Um [token](#security-token) de segurança contém reclamações, que fornecem afirmações sobre uma entidade (como uma [aplicação de cliente](#client-application) ou proprietário de [recursos](#resource-owner)) a outra entidade (como o servidor de [recursos).](#resource-server) As reclamações são pares de nome/valor que transmitem factos sobre o assunto simbólico (por exemplo, o diretor de segurança que foi autenticado pelo servidor de [autorização).](#authorization-server) As reclamações presentes num dado sinal é dependente de várias variáveis, incluindo o tipo de ficha, o tipo de credencial utilizada para autenticar o sujeito, a configuração da aplicação, etc.

Consulte a referência simbólica da [plataforma de identidade da Microsoft][AAD-Tokens-Claims] para obter mais detalhes.

## <a name="client-application"></a>aplicação cliente

Tal como definido pelo Quadro de [Autorização da OAuth2,][OAuth2-Role-Def]uma aplicação que efetue pedidos de recursos protegidos em nome do proprietário do [recurso.](#resource-owner) O termo "cliente" não implica quaisquer características particulares de implementação de hardware (por exemplo, se a aplicação executa num servidor, num ambiente de trabalho ou noutros dispositivos).

Um pedido de cliente solicita [autorização](#authorization) de um proprietário de recursos para participar num fluxo de concessão de [autorização OAuth2,](#authorization-grant) e pode aceder a APIs/dados em nome do proprietário do recurso. O Quadro de Autorização da OAuth2 [define dois tipos de clientes,][OAuth2-Client-Types]"confidenciais" e "públicos", com base na capacidade do cliente de manter a confidencialidade das suas credenciais. As aplicações podem implementar um [cliente web (confidencial)](#web-client) que funciona num servidor web, um [cliente nativo (público)](#native-client) instalado num dispositivo, ou um [cliente baseado em utilizador-agente (público)](#user-agent-based-client) que funciona no navegador de um dispositivo.

## <a name="consent"></a>consentimento

O processo de um [proprietário](#resource-owner) de recursos que concede autorização a uma [aplicação de cliente,](#client-application)para aceder a recursos protegidos sob [permissões específicas,](#permissions)em nome do proprietário do recurso. Dependendo das permissões solicitadas pelo cliente, será solicitado a um administrador ou utilizador o consentimento para permitir o acesso à sua organização/dados individuais, respectivamente. Note que, num cenário [de multi-inquilinos,](#multi-tenant-application) o diretor de [serviço](#service-principal-object) da aplicação também é registado no inquilino do utilizador que consente.

Consulte o [quadro de consentimento](consent-framework.md) para mais informações.

## <a name="id-token"></a>Ficha de identificação

Um [token](#security-token) de segurança [OpenID Connect][OpenIDConnect-ID-Token] fornecido pelo ponto final de autorização de um servidor de [autorização,](#authorization-server) [authorization endpoint](#authorization-endpoint)que contém [reclamações](#claim) relativas à autenticação de um proprietário de [recursos](#resource-owner)de utilizador final . Como um símbolo de acesso, as fichas de identificação também são representadas como um [JSON Web Token (JWT)][JWT]assinado digitalmente. No entanto, ao contrário de um símbolo de acesso, as alegações de um símbolo de identificação não são utilizadas para fins relacionados com o acesso aos recursos e especificamente no controlo de acesso.

Consulte a referência simbólica da [plataforma de identidade da Microsoft][AAD-Tokens-Claims] para obter mais detalhes.

## <a name="microsoft-identity-platform"></a>Plataforma de identidade da Microsoft

A plataforma de identidades da Microsoft é a evolução da plataforma para programadores e do serviço de identidades do Azure Active Directory (Azure AD). Permite que os programadores compilem aplicações que iniciam sessão em todas as identidades da Microsoft, obtenham tokens para chamar o Microsoft Graph, outras APIs da Microsoft ou APIs compiladas pelos programadores. É uma plataforma completa que consiste num serviço de autenticação, bibliotecas, registo e configuração de aplicações, documentação completa do desenvolvedor, amostras de código e outros conteúdos de desenvolvedor. A plataforma de identidades da Microsoft suporta protocolos norma da indústria, tais como OAuth 2.0 e o OpenID Connect. Consulte [a plataforma de identidade da Microsoft](about-microsoft-identity-platform.md) para obter mais detalhes.

## <a name="multi-tenant-application"></a>pedido de multi-inquilino

Uma classe de aplicação que permite o login e [o consentimento](#consent) dos utilizadores aprovisionados em qualquer [inquilino](#tenant)da AD Azure, incluindo inquilinos que não aquele em que o cliente está registado. As aplicações [de clientes nativos](#native-client) são multi-inquilinos por defeito, enquanto as aplicações de recursos web e [recursos web/API](#resource-server) têm a capacidade de selecionar entre um único ou multi-inquilino. [web client](#web-client) Em contrapartida, uma aplicação web registada como arrendatária única só permitiria inscrições a partir de contas de utilizador previstas no mesmo inquilino que aquela em que o pedido está registado.

Consulte como iniciar sessão em qualquer utilizador da [AD Azure utilizando o padrão de aplicação multi-inquilino][AAD-Multi-Tenant-Overview] para mais detalhes.

## <a name="native-client"></a>cliente nativo

Um tipo de [aplicação](#client-application) de cliente que é instalada de forma nativa num dispositivo. Uma vez que todo o código é executado num dispositivo, é considerado um cliente "público" devido à sua incapacidade de armazenar credenciais de forma privada/confidencial. Consulte os tipos e perfis de [clientes Da OAuth2][OAuth2-Client-Types] para obter mais detalhes.

## <a name="permissions"></a>permissões

Uma [aplicação de cliente](#client-application) obtém acesso a um servidor de [recursos](#resource-server) declarando pedidos de permissão. Estão disponíveis dois tipos:

* As permissões "delegadas", que especificam o acesso [baseado no âmbito](#scopes) utilizando a autorização delegada do proprietário de [recursos](#resource-owner)assinados, são apresentadas ao recurso em tempo de execução como [alegações "scp"](#claim) no sinal de [acesso](#access-token)do cliente.
* As permissões de "aplicação", que especificam o acesso [baseado em funções](#roles) utilizando as credenciais/identidade da aplicação do cliente, são apresentadas ao recurso em tempo de execução como alegações de ["papéis"](#claim) no sinal de acesso do cliente.

Também surgem durante o processo de [consentimento,](#consent) dando ao administrador ou proprietário de recursos a oportunidade de conceder/negar o acesso ao cliente aos recursos no seu inquilino.

Os pedidos de permissão estão configurados na página de **permissões da API** para uma aplicação no [portal Azure,][AZURE-portal]selecionando as "Permissões Delegadas" e "Permissões de Aplicação" (esta última requer a destitução na função Global Admin). Como um [cliente público](#client-application) não pode manter credenciais de forma segura, só pode solicitar permissões delegadas, enquanto um [cliente confidencial](#client-application) tem a capacidade de solicitar permissões delegadas e de aplicação. O objeto de [aplicação](#application-object) do cliente armazena as permissões declaradas na sua [propriedade necessáriaResourceAccess.][Graph-App-Resource]

## <a name="refresh-token"></a>ficha refrescante

Um tipo de ficha de [segurança](#security-token) emitida por um servidor de [autorização,](#authorization-server)e usada por uma [aplicação de cliente](#client-application) para solicitar um novo sinal de [acesso](#access-token) antes do termo do token de acesso. Tipicamente sob a forma de um [Token Web JSON (JWT)][JWT].

Ao contrário dos tokens de acesso, os tokens de atualização podem ser revogados. Se uma aplicação de cliente tentar solicitar um novo sinal de acesso usando um token de atualização que foi revogado, o servidor de autorização negará o pedido, e a aplicação do cliente deixará de ter permissão para aceder ao servidor de [recursos](#resource-server) em nome do proprietário do [recurso.](#resource-owner)

## <a name="resource-owner"></a>proprietário de recursos

Tal como definido pelo Quadro de [Autorização da OAuth2,][OAuth2-Role-Def]uma entidade capaz de conceder acesso a um recurso protegido. Quando o proprietário do recurso é uma pessoa, é referido como um utilizador final. Por exemplo, quando uma [aplicação de cliente](#client-application) quer aceder à caixa de correio de um utilizador através da [API do Microsoft Graph,][Microsoft-Graph]requer autorização do proprietário do recurso da caixa de correio.

## <a name="resource-server"></a>servidor de recursos

Tal como definido pelo Quadro de [Autorização OAuth2,][OAuth2-Role-Def]um servidor que acolhe recursos protegidos, capazes de aceitar e responder a pedidos de recursos protegidos por [aplicações de clientes](#client-application) que apresentam um sinal de [acesso.](#access-token) Também conhecido como servidor de recursos protegidos, ou aplicação de recursos.

Um servidor de recursos expõe APIs e impõe o acesso aos seus recursos protegidos através de [âmbitos](#scopes) e [funções,](#roles)utilizando o Quadro de Autorização OAuth 2.0. Exemplos incluem a [Microsoft Graph API][Microsoft-Graph] que fornece acesso aos dados do inquilino Azure AD, e o Office 365 APIs que fornecem acesso a dados como correio e calendário.

Tal como uma aplicação de cliente, a configuração de identidade da aplicação de recursos é estabelecida através do [registo](#application-registration) num inquilino da AD Azure, fornecendo tanto o objeto principal de aplicação como de serviço. Algumas APIs fornecidas pela Microsoft, como a Microsoft Graph API, têm os principais de serviçopré-registados disponibilizados em todos os inquilinos durante o provisionamento.

## <a name="roles"></a>funções

Tal como [os âmbitos,](#scopes)as funções fornecem uma forma de um servidor de [recursos](#resource-server) governar o acesso aos seus recursos protegidos. Existem dois tipos: uma função de "utilizador" implementa o controlo de acesso baseado em funções para utilizadores/grupos que exigem o acesso ao recurso, enquanto uma função de "aplicação" implementa o mesmo para [aplicações de clientes](#client-application) que requerem acesso.

As funções são cordas definidas por recursos (por exemplo, "Aprovador de despesas", "Read-only", "Directory.ReadWrite.All"), geridas no [portal Azure][AZURE-portal] através do manifesto de [aplicação](#application-manifest)do recurso, e armazenadas na [propriedade appRoles][Graph-Sp-Resource]do recurso. O portal Azure também é utilizado para atribuir aos utilizadores funções de "utilizador" e configurar [permissões](#permissions) de aplicação do cliente para aceder a uma função de "aplicação".

Para uma discussão detalhada das funções de aplicação expostas pela API do Microsoft Graph, consulte os Scopes de [Permissão DaPi graph][Graph-Perm-Scopes]. Para um exemplo de implementação passo a passo, consulte [Gerir o acesso utilizando o RBAC e o portal Azure][AAD-RBAC].

## <a name="scopes"></a>âmbitos

Tal como [as funções,](#roles)os âmbitos fornecem uma forma de um servidor de [recursos](#resource-server) governar o acesso aos seus recursos protegidos. Os âmbitos são utilizados para implementar o controlo de acesso [baseado no âmbito,][OAuth2-Access-Token-Scopes] para uma [aplicação do cliente](#client-application) que foi dada acesso delegado ao recurso pelo seu proprietário.

Os âmbitos são cordas definidas por recursos (por exemplo"Mail.Read", "Directory.ReadWrite.All"), geridas no [portal Azure][AZURE-portal] através do manifesto de [aplicação](#application-manifest)do recurso , e armazenadas na [propriedade oauth2Permissions][Graph-Sp-Resource]do recurso. O portal Azure também é utilizado para configurar [permissões delegadas](#permissions) de aplicação de clientes para aceder a um âmbito.

Uma convenção de nomeação de boas práticas é usar um formato de "recurso.operation.constraint". Para uma discussão detalhada dos âmbitos expostos pela Microsoft Graph API, consulte o [Graph API Permission Scopes][Graph-Perm-Scopes]. Para os âmbitos expostos pelos serviços do Office 365, consulte o [Office 365 API permissions reference][O365-Perm-Ref].

## <a name="security-token"></a>símbolo de segurança

Um documento assinado contendo reclamações, tais como um token OAuth2 ou uma afirmação SAML 2.0. Para uma [bolsa](#authorization-grant)de autorização OAuth2 , um [token](#access-token) de acesso (OAuth2), [token de atualização](#refresh-token), e um [Token ID](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) são tipos de fichas de segurança, todas elas implementadas como um [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>objeto principal de serviço

Ao registar/atualizar uma aplicação no [portal Azure,][AZURE-portal]o portal cria/atualiza tanto um objeto de [aplicação](#application-object) como um objeto principal de serviço correspondente para esse inquilino. O objeto de aplicação *define* a configuração de identidade da aplicação a nível global (em todos os inquilinos onde a aplicação associada foi concedida acesso), e é o modelo a partir do qual o seu principal objeto de serviço correspondente é *derivado* para uso local no tempo de execução (em um inquilino específico).

Para mais informações, consulte [Objetos Principais][AAD-App-SP-Objects]de Aplicação e Serviço .

## <a name="sign-in"></a>sign-in

O processo de [aplicação](#client-application) de um cliente que inicie a autenticação do utilizador final e a captura do estado conexo, com o objetivo de adquirir um [token](#security-token) de segurança e de analisar a sessão de candidatura a esse estado. O Estado pode incluir artefactos como informações de perfil do utilizador e informações derivadas de alegações simbólicas.

A função de inscrição de uma aplicação é normalmente utilizada para implementar um único sinal (SSO). Pode também ser precedido por uma função de "inscrição", como ponto de entrada para um utilizador final ter acesso a uma aplicação (após o primeiro início de inscrição). A função de inscrição é utilizada para recolher e persistir estado adicional específico do utilizador, podendo necessitar do [consentimento do utilizador](#consent).

## <a name="sign-out"></a>de fim de sessão

O processo de não autenticação de um utilizador final, desvinculando o estado de utilizador associado à sessão de [aplicação](#client-application) do cliente durante o [login](#sign-in)

## <a name="tenant"></a>inquilino

Um exemplo de um diretório Azure AD é referido como um inquilino da AD Azure. Fornece várias funcionalidades, incluindo:

* um serviço de registo para aplicações integradas
* autenticação das contas de utilizador e das aplicações registadas
* Pontos finais REST necessários para apoiar vários protocolos, incluindo o OAuth2 e o SAML, incluindo o ponto final de [autorização,](#authorization-endpoint)o [ponto final simbólico](#token-endpoint) e o ponto final "comum" utilizado por [aplicações multi-arrendatárias.](#multi-tenant-application)

Os inquilinos da Azure AD são criados/associados com assinaturas Azure e Office 365 durante a inscrição, fornecendo funcionalidades de Gestão de Acesso & identidade para a subscrição. Os administradores de subscrição do Azure também podem criar inquilinos adicionais da AD Azure através do portal Azure. Veja como obter um inquilino do [Azure Ative Directory][AAD-How-To-Tenant] para obter detalhes sobre as várias formas de acesso a um inquilino. Veja como as [subscrições do Azure estão associadas ao Azure Ative Directory][AAD-How-Subscriptions-Assoc] para mais detalhes sobre a relação entre subscrições e um inquilino da AD Azure.

## <a name="token-endpoint"></a>ponto final simbólico

Um dos pontos finais implementados pelo servidor de [autorização](#authorization-server) para suportar [as subvenções](#authorization-grant)de autorização da OAuth2. Dependendo da subvenção, pode ser usado para adquirir um [token](#access-token) de acesso (e ficha de "atualização" relacionada) a um [cliente](#client-application), ou [ficha de identificação](#id-token) quando usado com o protocolo [OpenID Connect.][OpenIDConnect]

## <a name="user-agent-based-client"></a>Cliente baseado em utilizador-agente

Um tipo de [aplicação](#client-application) de cliente que descarrega código a partir de um servidor web e executa dentro de um utilizador-agente (por exemplo, um navegador web), como uma aplicação de uma página única (SPA). Uma vez que todo o código é executado num dispositivo, é considerado um cliente "público" devido à sua incapacidade de armazenar credenciais de forma privada/confidencial. Para mais informações, consulte os tipos e perfis de [clientes Da OAuth2.][OAuth2-Client-Types]

## <a name="user-principal"></a>diretor de utilizadores

Semelhante à forma como um objeto principal de serviço é usado para representar uma instância de aplicação, um objeto principal do utilizador é outro tipo de diretor de segurança, que representa um utilizador. O tipo de recurso do [Utilizador][Graph-User-Resource] do Microsoft Graph define o esquema para um objeto de utilizador, incluindo propriedades relacionadas com o utilizador, tais como primeiro e último nome, nome principal do utilizador, membro da função de diretório, etc. Isto fornece a configuração de identidade do utilizador para a AD Azure estabelecer um diretor de utilizador no tempo de execução. O utilizador principal é utilizado para representar um utilizador autenticado para o Single Sign-On, delegação de [consentimento](#consent) de gravação, tomada de decisões de controlo de acesso, etc.

## <a name="web-client"></a>cliente web

Um tipo de [aplicação](#client-application) de cliente que executa todo o código num servidor web, e capaz de funcionar como um cliente "confidencial", armazenando de forma segura as suas credenciais no servidor. Para mais informações, consulte os tipos e perfis de [clientes Da OAuth2.][OAuth2-Client-Types]

## <a name="next-steps"></a>Passos seguintes

O [Guia do Desenvolvedor da plataforma de identidade microsoft][AAD-Dev-Guide] é a página de aterragem a utilizar para todos os tópicos relacionados com o desenvolvimento da plataforma de identidade da Microsoft, incluindo uma visão geral da integração de [aplicações][AAD-How-To-Integrate] e os fundamentos da autenticação da [plataforma de identidade microsoft e cenários de autenticação suportados.][AAD-Auth-Scenarios] Também pode encontrar amostras de código & tutoriais sobre como se levantar e funcionar rapidamente no [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Utilize a secção de comentários seguintes para fornecer feedback e ajudar a aperfeiçoar e moldar este conteúdo, incluindo pedidos de novas definições ou atualização das existentes!

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
