---
title: Visão geral técnica e de características - Azure Ative Directory B2C
description: Uma introdução aprofundada às funcionalidades e tecnologias no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fef192ab273a0e53958cda315932f91b427a5d7d
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108388"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Visão geral técnica e de características do Azure Ative Directory B2C

Acompanhante do [About Azure Ative Directory B2C,](overview.md)este artigo proporciona uma introdução mais aprofundada ao serviço. Discutidos aqui são os recursos primários com que trabalha no serviço, as suas funcionalidades e como estes lhe permitem proporcionar uma experiência de identidade totalmente personalizada para os seus clientes nas suas aplicações.

## <a name="azure-ad-b2c-tenant"></a>Inquilino Azure AD B2C

No Azure Ative Directory B2C (Azure AD B2C), um *inquilino* representa a sua organização e é um diretório de utilizadores. Cada inquilino do Azure AD B2C é distinto e independente dos outros inquilinos do Azure AD B2C. Um inquilino Azure AD B2C é diferente de um inquilino do Azure Ative Directory, que você já pode ter.

Os recursos primários com que trabalha num inquilino Azure AD B2C são:

* **Diretório** - O *diretório* é onde o Azure AD B2C armazena as credenciais e dados de perfil dos seus utilizadores, bem como os registos da sua aplicação.
* **Registos de aplicações** - Regista as suas aplicações web, móveis e nativas com Azure AD B2C para permitir a gestão de identidade. Além disso, quaisquer APIs que queira proteger com Azure AD B2C.
* **Fluxos de utilizador** e **políticas personalizadas** - As experiências de identidade incorporadas (fluxos de utilizador) e totalmente personalizáveis (políticas personalizadas) para as suas aplicações.
  * Utilize *fluxos de utilizador* para uma configuração rápida e ativação de tarefas de identidade comuns como iniciar sessão, iniciar sessão e editar perfis.
  * Utilize *políticas personalizadas* para permitir experiências de utilização não só para as tarefas de identidade comuns, mas também para criar suporte para fluxos de trabalho de identidade complexos exclusivos da sua organização, clientes, colaboradores, parceiros e cidadãos.
* **Fornecedores de identidade** - Configurações da Federação para:
  * *Fornecedores* de identidade social como Facebook, LinkedIn ou Twitter que pretende apoiar nas suas aplicações.
  * *Fornecedores de* identidade externos que suportam protocolos de identidade padrão como OAuth 2.0, OpenID Connect, e muito mais.
  * *Contas locais* que permitem aos utilizadores iniciar scontabilidade e iniciar scontabilidade com um nome de utilizador (ou endereço de e-mail ou outro ID) e senha.
* **Chaves** - Adicione e gere chaves de encriptação para assinar e validar fichas, segredos do cliente, certificados e senhas.

Um inquilino Azure AD B2C é o primeiro recurso que você precisa criar para começar com Azure AD B2C. Saiba como em [Tutorial: Crie um inquilino Azure Ative Directory B2C](tutorial-create-tenant.md).

## <a name="accounts-in-azure-ad-b2c"></a>Contas em Azure AD B2C

Azure AD B2C define vários tipos de contas de utilizador. O Azure Ative Directory, o Azure Ative Directory B2B e o Azure Ative Directory B2C partilham estes tipos de conta.

* **Conta de trabalho** - Os utilizadores com contas de trabalho podem gerir recursos num inquilino, e com uma função de administrador, também podem gerir inquilinos. Os utilizadores com contas de trabalho podem criar novas contas de consumo, redefinir palavras-passe, bloquear/desbloquear contas e definir permissões ou atribuir uma conta a um grupo de segurança.
* **Conta de hóspedes** - Utilizadores externos que convida ao seu inquilino como hóspedes. Um cenário típico para convidar um utilizador convidado para o seu inquilino Azure AD B2C é partilhar responsabilidades administrativas.
* **Conta de consumo** - Contas de consumo são as contas criadas no seu diretório Azure AD B2C quando os utilizadores completam a viagem de utilizador de inscrição numa aplicação que registou no seu inquilino.

![Página de gestão de utilizadores Azure AD B2C no portal Azure](media/technical-overview/portal-01-users.png)<br/>*Figura: Diretório de utilizadores dentro de um inquilino Azure AD B2C no portal Azure*

### <a name="consumer-accounts"></a>Contas de consumo

Com uma conta *de consumo,* os utilizadores podem iniciar scontabilidade nas aplicações que garantiu com o Azure AD B2C. Os utilizadores com contas de consumo não podem, no entanto, aceder aos recursos da Azure, por exemplo, ao portal Azure.

Uma conta de consumo pode ser associada a estes tipos de identidade:

* **Identidade local,** com o nome de utilizador e senha armazenados localmente no diretório Azure AD B2C. Muitas vezes nos referimos a estas identidades como "contas locais".
* **Identidades sociais** ou **empresariais,** onde a identidade do utilizador é gerida por um fornecedor de identidade federado como Facebook, Microsoft, ADFS ou Salesforce.

Um utilizador com uma conta de consumo pode iniciar súmis com múltiplas identidades, por exemplo, nome de utilizador, e-mail, ID do funcionário, ID do governo, entre outros. Uma única conta pode ter múltiplas identidades, tanto locais como sociais.

![Identidades das contas de consumo](media/technical-overview/identities.png)<br/>*Figura: Uma única conta de consumo com múltiplas identidades em Azure AD B2C*

O Azure AD B2C permite-lhe gerir atributos comuns de perfis de conta de consumo como nome de exibição, apelido, nome dado, cidade, entre outros. Também pode estender o esquema Azure AD para armazenar informações adicionais sobre os seus utilizadores. Por exemplo, o seu país/região ou residência, língua preferida e preferências como se querem subscrever uma newsletter ou ativar a autenticação de vários fatores.

Saiba mais sobre os tipos de conta de utilizador em Azure AD B2C em [visão geral das contas de utilizador em Azure Ative Directory B2C](user-overview.md).

## <a name="external-identity-providers"></a>Fornecedores de identidade externos

Pode configurar o Azure AD B2C para permitir que os utilizadores entrem na sua aplicação com credenciais de fornecedores externos de identidade social ou empresarial (IdP). O Azure AD B2C suporta fornecedores de identidade externos como os protocolos Facebook, Microsoft, Google, Twitter e qualquer fornecedor de identidade que suporte protocolos OAuth 1.0, OAuth 2.0, OpenID Connect e SAML.

![Fornecedores de identidade externos](media/technical-overview/external-idps.png)

Com a federação externa de fornecedores de identidade, pode oferecer aos seus consumidores a capacidade de assinar com as suas contas sociais ou empresariais existentes, sem ter de criar uma nova conta apenas para a sua aplicação.

Na página de inscrição ou inscrição, o Azure AD B2C apresenta uma lista de fornecedores de identidade externos que o utilizador pode escolher para iniciar sação. Assim que selecionarem um dos fornecedores de identidade externos, são levados (redirecionados) para o website do fornecedor selecionado para completar o sinal em processo. Depois de o utilizador se ter apresentado com sucesso, são devolvidos ao Azure AD B2C para autenticação da conta na sua aplicação.

![Exemplo de acesso móvel com uma conta social (Facebook)](media/technical-overview/external-idp.png)

Para ver como adicionar fornecedores de identidade no Azure AD B2C, consulte [Tutorial: Adicione fornecedores de identidade às suas aplicações no Azure Ative Directory B2C](tutorial-add-identity-providers.md).

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Experiências de identidade: fluxos de utilizador ou políticas personalizadas

O quadro político extensível do Azure AD B2C é a sua força central. As políticas descrevem as experiências de identidade dos seus utilizadores, tais como inscrição, inscrição e edição de perfis.

No Azure AD B2C, existem dois caminhos primários que pode seguir para proporcionar estas experiências de identidade: fluxos de utilizador e políticas personalizadas.

* **Os fluxos de utilizador** são políticas pré-indefinidas, incorporadas e configuráveis que fornecemos para que possa criar experiências de inscrição, inscrição e edição de políticas em minutos.

* **As políticas personalizadas** permitem-lhe criar as suas próprias viagens de utilizador para cenários complexos de experiência de identidade.

Tanto os fluxos de utilizador como as políticas personalizadas são alimentados pelo *Identity Experience Framework*, o motor de orquestração política do Azure AD B2C.

### <a name="user-flow"></a>Fluxo de utilizador

Para ajudá-lo a configurar rapidamente as tarefas de identidade mais comuns, o portal Azure inclui várias políticas predefinidas e configuráveis chamadas *fluxos de utilizador.*

Pode configurar definições de fluxo de utilizador como estas para controlar comportamentos de experiência de identidade nas suas aplicações:

* Tipos de conta utilizados para o s-in, como contas sociais como um Facebook, ou contas locais que usam um endereço de e-mail e senha para iniciar sação
* Atributos a recolher junto do consumidor, tais como o primeiro nome, código postal ou país/região de residência
* Autenticação de vários fatores Azure Ad (MFA)
* Personalização da interface de utilizador
* Conjunto de reclamações num token que a sua aplicação recebe depois de o utilizador completar o fluxo do utilizador
* Gestão de sessões
* ... e mais.

Os cenários de identidade mais comuns para a maioria das aplicações móveis, web e de uma página podem ser definidos e implementados eficazmente com os fluxos dos utilizadores. Recomendamos que utilize os fluxos de utilizador incorporados, a menos que tenha cenários complexos de viagem de utilizador que exijam a flexibilidade total das políticas personalizadas.

Saiba mais sobre os fluxos de utilizadores nos [fluxos de utilizador no Azure Ative Directory B2C](user-flow-overview.md).

### <a name="custom-policy"></a>Política personalizada

As políticas personalizadas desbloqueiam o acesso ao pleno poder do motor de orquestração Do Quadro de Experiência de Identidade (IEF). Com as políticas personalizadas, pode aproveitar o IEF para construir quase qualquer experiência de autenticação, registo do utilizador ou edição de perfis que possa imaginar.

O Quadro de Experiência de Identidade dá-lhe a capacidade de construir viagens de utilizador com qualquer combinação de passos. Por exemplo:

* Federate com outros fornecedores de identidade
* Desafios da autenticação de múltiplos fatores de primeira e de terceiros (MFA)
* Recolher qualquer entrada de utilizador
* Integre-se com sistemas externos utilizando a comunicação REST API

Cada viagem de utilizador é definida por uma política, e pode construir quantas ou poucas políticas que precisar para permitir a melhor experiência do utilizador para a sua organização.

![Diagrama mostrando um exemplo de uma viagem complexa de utilizador possibilitada pelo IEF](media/technical-overview/custom-policy.png)

Uma política personalizada é definida por vários ficheiros XML que se referem uns aos outros numa cadeia hierárquica. Os elementos XML definem o esquema de reclamações, as transformações de reclamações, as definições de conteúdo, os fornecedores de sinistros, os perfis técnicos, as etapas de orquestração de jornadas de utilizador e outros aspetos da experiência de identidade.

A poderosa flexibilidade das políticas personalizadas é mais adequada para quando é necessário construir cenários de identidade complexos. Os desenvolvedores que configuram políticas personalizadas devem definir as relações fidedignas com cuidado para incluir pontos finais de metadados, definições exatas de troca de alegações e configurar segredos, chaves e certificados, conforme necessário por cada fornecedor de identidade.

Saiba mais sobre políticas [personalizadas em políticas personalizadas no Azure Ative Directory B2C](custom-policy-overview.md).

## <a name="protocols-and-tokens"></a>Protocolos e fichas

- Para aplicações, o Azure AD B2C suporta os [protocolos](connect-with-saml-service-providers.md) [OAuth 2.0](protocols-overview.md), [OpenID Connect](openid-connect.md)e SAML para viagens de utilizador. A sua aplicação inicia a jornada do utilizador emitindo pedidos de autenticação ao Azure AD B2C. O resultado de um pedido ao Azure AD B2C é um símbolo de segurança, como um [token de ID, token de acesso](tokens-overview.md)ou ficha SAML. Este símbolo de segurança define a identidade do utilizador dentro da aplicação.

- Para identidades externas, a Azure AD B2C apoia a federação com quaisquer fornecedores de identidade OAuth 1.0, OAuth 2.0, OpenID Connect e SAML.

O seguinte diagrama mostra como o Azure AD B2C pode comunicar utilizando uma variedade de protocolos dentro do mesmo fluxo de autenticação:

![Diagrama de aplicação de cliente baseada em OIDC federando com um IdP baseado em SAML](media/technical-overview/protocols.png)

1. A aplicação da parte de gestão inicia um pedido de autorização ao Azure AD B2C utilizando o OpenID Connect.
1. Quando um utilizador da aplicação opta por assinar através de um fornecedor de identidade externo que utiliza o protocolo SAML, o Azure AD B2C invoca o protocolo SAML para comunicar com esse fornecedor de identidade.
1. Depois de o utilizador concluir a operação de inscrição com o fornecedor de identidade externo, o Azure AD B2C devolve o token à aplicação do partido que conta com o OpenID Connect.

## <a name="application-integration"></a>Integração de aplicações

Quando um utilizador quer iniciar sação na sua aplicação, seja uma aplicação web, móvel, desktop ou de uma página única (SPA), a aplicação inicia um pedido de autorização a um ponto final fornecido por políticas de fluxo ou personalizados. O fluxo do utilizador ou a política personalizada define e controla a experiência do utilizador. Quando completam um fluxo de utilizador, por exemplo, o fluxo *de inscrição ou de entrada,* o Azure AD B2C gera um token e, em seguida, redireciona o utilizador de volta para a sua aplicação.

![Aplicativo móvel com setas mostrando fluxo entre a página de entrada de Azure AD B2C](media/technical-overview/app-integration.png)

Várias aplicações podem usar o mesmo fluxo de utilizador ou política personalizada. Uma única aplicação pode utilizar vários fluxos de utilizador ou políticas personalizadas.

Por exemplo, para iniciar sôm numa aplicação, a aplicação utiliza a *inscrição ou o sinal no* fluxo do utilizador. Depois de o utilizador ter assinado, poderá querer editar o seu perfil, pelo que a aplicação inicia outro pedido de autorização, desta vez utilizando o fluxo de utilizador editar o *perfil.*

## <a name="seamless-user-experiences"></a>Experiências de utilizador sem emenda

No Azure AD B2C, pode criar as experiências de identidade dos seus utilizadores para que as páginas que lhes são mostradas se misturem perfeitamente com o aspeto e a sensação da sua marca. Obtém o controlo quase total dos conteúdos HTML e CSS apresentados aos seus utilizadores quando procedem através das viagens de identidade da sua aplicação. Com esta flexibilidade, pode manter a consistência de marca e visual entre a sua aplicação e a Azure AD B2C.

![Screenshots da página de inscrição personalizada da marca](media/technical-overview/seamless-ux.png)

Para obter informações sobre a personalização da UI, consulte sobre a personalização da [interface do utilizador no Azure Ative Directory B2C](customize-ui-with-html.md).

## <a name="localization"></a>Localização

A personalização de idiomas no Azure AD B2C permite-lhe acomodar diferentes idiomas de acordo com as necessidades do seu cliente. A Microsoft fornece as traduções para 36 idiomas, mas também pode fornecer as suas próprias traduções para qualquer idioma. Mesmo que a sua experiência seja fornecida apenas para um único idioma, pode personalizar qualquer texto nas páginas.

![Três páginas de inscrição que mostram texto de UI em diferentes línguas](media/technical-overview/localization.png)

Veja como funciona a localização na [personalização linguística no Azure Ative Directory B2C](language-customization.md).

## <a name="add-your-own-business-logic"></a>Adicionar a sua própria lógica de negócio

Se optar por utilizar políticas personalizadas, pode integrar-se com uma API RESTful numa viagem de utilizador para adicionar a sua própria lógica de negócio à viagem. Por exemplo, o Azure AD B2C pode trocar dados com um serviço RESTful para:

* Exiba mensagens de erro personalizadas para o utilizador.
* Valide a entrada do utilizador para evitar que os dados mal formados persistam no diretório do utilizador. Por exemplo, pode modificar os dados introduzidos pelo utilizador, como capitalizar o seu primeiro nome se os introduzirem em todas as minúsculas.
* Enriqueça os dados dos utilizadores integrando-se ainda mais com a sua aplicação de linha de negócio corporativa.
* Utilizando chamadas RESTful, pode enviar notificações push, atualizar bases de dados corporativas, executar um processo de migração de utilizadores, gerir permissões, bases de dados de auditoria e muito mais.

Os programas de fidelização são outro cenário possibilitado pelo apoio do Azure AD B2C para chamar APIs de REST. Por exemplo, o seu serviço RESTful pode receber o endereço de e-mail de um utilizador, consultar a base de dados do seu cliente e, em seguida, devolver o número de fidelização do utilizador ao Azure AD B2C. Os dados de devolução podem ser armazenados na conta de diretório do utilizador em Azure AD B2C, depois ser avaliados em etapas subsequentes na apólice, ou ser incluídos no token de acesso.

![Integração de linha de negócios numa aplicação móvel](media/technical-overview/lob-integration.png)

Pode adicionar uma chamada de API REST a qualquer passo da jornada do utilizador definida por uma política personalizada. Por exemplo, pode chamar uma API DE REPOUSO:

* Durante o s-in, pouco antes de Azure AD B2C validar as credenciais
* Imediatamente após o sin-in
* Antes de Azure AD B2C cria uma nova conta no diretório
* Depois de Azure AD B2C criar uma nova conta no diretório
* Antes de Azure AD B2C emitir um token de acesso

Para ver como utilizar políticas personalizadas para integração RESTful API em Azure AD B2C, consulte [as trocas de reclamações da API do REST na sua política personalizada Azure AD B2C.](custom-policy-rest-api-intro.md)

## <a name="protect-customer-identities"></a>Proteger as identidades dos clientes

O Azure AD B2C cumpre os compromissos de segurança, privacidade e outros compromissos descritos no [Microsoft Azure Trust Center](https://www.microsoft.com/trustcenter/cloudservices/azure).

As sessões são modeladas como dados encriptados, com a chave de desencriptação conhecida apenas pelo Serviço de Token de Segurança Ad B2C Adure. É utilizado um algoritmo de encriptação forte, AES-192. Todos os caminhos de comunicação estão protegidos com TLS para confidencialidade e integridade. O nosso Serviço de Token de Segurança utiliza um certificado de Validação Alargada (EV) para TLS. Em geral, o Serviço de Token de Segurança atenua os ataques de scripts de sítios cruzados (XSS) não tornando a entrada não fidedusquisa.

![Diagrama de dados seguros em trânsito e em repouso](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Acesso aos dados dos utilizadores

Os inquilinos da Azure AD B2C partilham muitas características com os inquilinos do Azure Ative Directory usados para empregados e parceiros. Os aspetos partilhados incluem mecanismos de visualização de funções administrativas, atribuição de funções e atividades de auditoria.

Pode atribuir funções para controlar quem pode executar determinadas ações administrativas em Azure AD B2C, incluindo:

* Criar e gerir todos os aspetos dos fluxos de utilizadores
* Criar e gerir o esquema de atributos disponíveis para todos os fluxos de utilizador
* Configure os fornecedores de identidade para utilização na federação direta
* Criar e gerir políticas-quadro de confiança no Quadro de Experiência de Identidade (políticas personalizadas)
* Gerir segredos para a federação e encriptação no Quadro de Experiência de Identidade (políticas personalizadas)

Para obter mais informações sobre as funções de Azure AD, incluindo o suporte de funções de administração Azure AD B2C, consulte [permissões de função de administrador no Diretório Ativo Azure](../active-directory/roles/permissions-reference.md).

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

A autenticação multi-factor AD B2C (MFA) ajuda a salvaguardar o acesso a dados e aplicações, mantendo a simplicidade para os seus utilizadores. Fornece segurança adicional exigindo uma segunda forma de autenticação, e oferece uma autenticação forte, oferecendo uma gama de métodos de autenticação fáceis de usar. Os seus utilizadores podem ou não ser desafiados para MFA com base nas decisões de configuração que pode tomar como administrador.

Ver como ativar o MFA nos fluxos de utilizador em [Ativar a autenticação de vários fatores no Azure Ative Directory B2C](multi-factor-authentication.md).

### <a name="smart-account-lockout"></a>Bloqueio de conta inteligente

Para evitar tentativas de adivinhação por palavra-passe bruta, o Azure AD B2C utiliza uma estratégia sofisticada para bloquear contas com base no IP do pedido, as palavras-passe inseridas e vários outros fatores. A duração do bloqueio é automaticamente aumentada com base no risco e no número de tentativas.

![Bloqueio inteligente da conta](media/technical-overview/smart-lockout1.png)

Para obter mais informações sobre a gestão das definições de proteção de passwords, consulte [Gerir ameaças a recursos e dados no Azure Ative Directory B2C](threat-management.md).

### <a name="password-complexity"></a>Complexidade da palavra-passe

Durante o reposição de inscrições ou de password, os seus utilizadores devem fornecer uma palavra-passe que cumpra as regras de complexidade. Por padrão, o Azure AD B2C aplica uma política de senha forte. O Azure AD B2C também fornece opções de configuração para especificar os requisitos de complexidade das palavras-passe que os seus clientes utilizam.

Pode configurar os requisitos de complexidade da palavra-passe tanto nos [fluxos de utilizador](password-complexity.md) como nas políticas [personalizadas.](password-complexity.md)

## <a name="auditing-and-logs"></a>Auditoria e registos

A Azure AD B2C emite registos de auditoria contendo informações de atividade sobre os seus recursos, fichas emitidas e acesso ao administrador. Pode utilizar estes registos de auditoria para compreender a atividade da plataforma e diagnosticar problemas. As entradas de registo de auditoria estão disponíveis logo após a atividade que gerou o evento ocorrer.

Num registo de auditoria, disponível para o seu inquilino Azure AD B2C ou para um determinado utilizador, pode encontrar informações que incluam:

* Atividades relativas à autorização de um utilizador para aceder aos recursos B2C (por exemplo, um administrador que aceda a uma lista de políticas B2C)
* Atividades relacionadas com atributos de diretório recuperados quando um administrador assina na utilização do portal Azure
* Criar, ler, atualizar e eliminar operações (CRUD) em aplicações B2C
* Operações CRUD em chaves armazenadas num recipiente de chave B2C
* Operações CRUD sobre recursos B2C (por exemplo, políticas e fornecedores de identidade)
* Validação das credenciais de utilizador e emissão simbólica

![Registo de auditoria individual do utilizador mostrado no portal Azure](media/technical-overview/audit-log.png)

Para obter informações adicionais sobre os registos de auditoria, consulte os registos de auditoria do [Azure AD B2C](view-audit-logs.md).

### <a name="usage-insights"></a>Insights de utilização

O Azure AD B2C permite-lhe descobrir quando as pessoas se inscrevem ou se inscrevem na sua aplicação web, onde os seus utilizadores estão localizados, e que navegadores e sistemas operativos utilizam. Ao integrar o Azure Application Insights no Azure AD B2C utilizando políticas personalizadas, pode obter informações sobre como as pessoas se inscrevem, se inscrevem, reiniciam a sua palavra-passe ou editam o seu perfil. Com esse conhecimento, pode tomar decisões baseadas em dados para os seus próximos ciclos de desenvolvimento.

Saiba mais sobre análises de utilização em [Track user behavior in Azure Ative Directory B2C using Application Insights](analytics-with-application-insights.md).

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma visão mais profunda sobre as características e aspetos técnicos do Azure Ative Directory B2C, começa com o nosso [tutorial para criar um inquilino Azure Ative Directory B2C.](tutorial-create-tenant.md)
