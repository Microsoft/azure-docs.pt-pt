---
title: Visão geral técnica e de funcionalidade - Diretório Ativo Azure B2C
description: Uma introdução aprofundada às funcionalidades e tecnologias no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d57bf7fa6d56c1704a78219f8a0af1182ce8a955
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739104"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Visão geral técnica e característica do Diretório Ativo Azure B2C

Acompanhante do [About Azure Ative Directory B2C,](overview.md)este artigo fornece uma introdução mais aprofundada ao serviço. Aqui são discutidos os principais recursos com que trabalha no serviço, as suas funcionalidades e como estes permitem fornecer uma experiência de identidade totalmente personalizada para os seus clientes nas suas aplicações.

## <a name="azure-ad-b2c-tenant"></a>Inquilino Azure AD B2C

No Azure Ative Directory B2C (Azure AD B2C), um *inquilino* representa a sua organização e é um diretório de utilizadores. Cada inquilino do Azure AD B2C é distinto e independente dos outros inquilinos do Azure AD B2C. Um inquilino Azure AD B2C é diferente de um inquilino azure Ative Directory, que você pode já ter.

Os recursos primários com que trabalha num inquilino Azure AD B2C são:

* **Diretório** - O *diretório* é onde o Azure AD B2C armazena as credenciais e dados de perfil dos seus utilizadores, bem como os registos de aplicação.
* **Registos de aplicações** - Registe as suas aplicações web, móveis e nativas com o Azure AD B2C para permitir a gestão da identidade. Além disso, quaisquer APIs que queira proteger com Azure AD B2C.
* **Fluxos** de utilizadores e **políticas personalizadas** - As experiências de identidade incorporadas (fluxos de utilizador) e totalmente personalizáveis (políticas personalizadas) para as suas aplicações.
  * Utilize *fluxos de utilizador* para uma configuração rápida e habilitação de tarefas de identidade comuns, como iniciar sessão, iniciar sessão e edição de perfis.
  * Utilize *políticas personalizadas* para permitir experiências de utilizador não só para as tarefas comuns de identidade, mas também para criar suporte para fluxos de trabalho de identidade complexos exclusivos da sua organização, clientes, colaboradores, parceiros e cidadãos.
* **Fornecedores de identidade** - Definições da Federação para:
  * *Fornecedores* de identidade social como facebook, LinkedIn ou Twitter que pretende apoiar nas suas aplicações.
  * *Fornecedores* de identidade externos que suportam protocolos de identidade padrão como OAuth 2.0, OpenID Connect, e muito mais.
  * *Contas locais* que permitem aos utilizadores inscreverem-se e iniciarem sessão com um nome de utilizador (ou endereço de e-mail ou outro ID) e senha.
* **Chaves** - Adicione e gereas chaves de encriptação para a assinatura e validação de fichas.

Um inquilino Azure AD B2C é o primeiro recurso que precisa para começar com o Azure AD B2C. Saiba como em [Tutorial: Crie um inquilino Azure Ative Directory B2C.](tutorial-create-tenant.md)

## <a name="accounts-in-azure-ad-b2c"></a>Contas em Azure AD B2C

O Azure AD B2C define vários tipos de contas de utilizador. O Azure Ative Directory, o Azure Ative Directory B2B e o Azure Ative Directory B2C partilham estes tipos de conta.

* **Conta** de trabalho - Os utilizadores com contas de trabalho podem gerir recursos num inquilino, e com uma função de administrador, também podem gerir os inquilinos. Os utilizadores com contas de trabalho podem criar novas contas de consumo, redefinir palavras-passe, bloquear/desbloquear contas e definir permissões ou atribuir uma conta a um grupo de segurança.
* **Conta de hóspedes** - Utilizadores externos convida para o seu inquilino como hóspedes. Um cenário típico para convidar um utilizador convidado para o seu inquilino Azure AD B2C é partilhar responsabilidades administrativas.
* **Conta** de consumo - As contas de consumo são as contas criadas no seu diretório Azure AD B2C quando os utilizadores completam a viagem de utilizador de inscrição numa aplicação que registou no seu inquilino.

![Página de gestão de utilizadores do Azure AD B2C no portal Azure](media/technical-overview/portal-01-users.png)<br/>*Figura: Diretório de utilizadores dentro de um inquilino Azure AD B2C no portal Azure*

### <a name="consumer-accounts"></a>Contas de consumo

Com uma conta de *consumo,* os utilizadores podem iniciar sessão nas aplicações que garantiu com o Azure AD B2C. Os utilizadores com contas de consumo não podem, no entanto, aceder aos recursos do Azure, por exemplo, ao portal Azure.

Uma conta de consumo pode ser associada a estes tipos de identidade:

* **Identidade local,** com o nome de utilizador e senha armazenados localmente no diretório Azure AD B2C. Muitas vezes referimo-nos a estas identidades como "contas locais".
* **Identidades sociais** ou **empresariais,** onde a identidade do utilizador é gerida por um fornecedor de identidade federado como Facebook, Microsoft, ADFS ou Salesforce.

Um utilizador com uma conta de consumo pode iniciar sessão com múltiplas identidades, por exemplo nome de utilizador, e-mail, ID do empregado, ID do governo, entre outros. Uma única conta pode ter múltiplas identidades, tanto locais como sociais.

![Identidades da conta de consumo](media/technical-overview/identities.png)<br/>*Figura: Uma única conta de consumidor com múltiplas identidades em Azure AD B2C*

O Azure AD B2C permite-lhe gerir atributos comuns de perfis de conta de consumo como nome de exibição, apelido, nome próprio, cidade e outros. Também pode estender o esquema Azure AD para armazenar informações adicionais sobre os seus utilizadores. Por exemplo, o seu país/região ou residência, a linguagem preferida e as preferências como se querem subscrever uma newsletter ou permitir a autenticação de vários fatores.

Saiba mais sobre os tipos de conta de utilizador em Azure AD B2C em [visão geral das contas dos utilizadores no Diretório Ativo Azure B2C](user-overview.md).

## <a name="external-identity-providers"></a>Fornecedores de identidade externos

Pode configurar o Azure AD B2C para permitir que os utilizadores inscrevam a sua aplicação com credenciais de fornecedores de identidade social ou empresarial externos (IDP). O Azure AD B2C suporta fornecedores de identidade externos como os protocolos Facebook, Microsoft, Google, Twitter e qualquer fornecedor de identidade que suporte os protocolos OAuth 1.0, OAuth 2.0, OpenID Connect, SAML ou WS-Federation.

![Fornecedores de identidade externos](media/technical-overview/external-idps.png)

Com a federação externa de fornecedores de identidade, pode oferecer aos seus consumidores a possibilidade de se inscreverem nas suas contas sociais ou empresariais existentes, sem ter de criar uma nova conta apenas para a sua aplicação.

Na página de inscrição ou inscrição, o Azure AD B2C apresenta uma lista de fornecedores de identidade externos que o utilizador pode escolher para iniciar sessão. Uma vez que selecionam um dos fornecedores de identidade externos, são levados (redirecionados) para o website do fornecedor selecionado para completar o sinal em processo. Depois de o utilizador ter conseguido fazer o início, são devolvidos ao Azure AD B2C para autenticação da conta na sua aplicação.

![Exemplo de inscrição móvel com uma conta social (Facebook)](media/technical-overview/external-idp.png)

Para ver como adicionar fornecedores de identidade no Azure AD B2C, consulte [Tutorial: Adicione fornecedores de identidade às suas aplicações no Azure Ative Directory B2C](tutorial-add-identity-providers.md).

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Experiências de identidade: fluxos de utilizadores ou políticas personalizadas

O quadro político extensível do Azure AD B2C é a sua força central. As políticas descrevem as experiências de identidade dos seus utilizadores, tais como inscrever-se, iniciar sessão e edição de perfis.

No Azure AD B2C, existem dois caminhos primários que pode tomar para proporcionar estas experiências de identidade: fluxos de utilizadores e políticas personalizadas.

* **Os fluxos** de utilizadores são políticas predefinidas, incorporadas e configuráveis que fornecemos para que possa criar experiências de inscrição, inscrição e edição de políticas em minutos.

* **As políticas personalizadas** permitem-lhe criar as suas próprias viagens de utilizador para cenários complexos de experiência de identidade.

Tanto os fluxos de utilizadores como as políticas personalizadas são alimentados pelo Quadro de *Experiência de Identidade*, motor de orquestração política do Azure AD B2C.

### <a name="user-flow"></a>Fluxo de utilizador

Para ajudá-lo a configurar rapidamente as tarefas de identidade mais comuns, o portal Azure inclui várias políticas predefinidas e configuráveis chamadas *fluxos de utilizadores.*

Pode configurar configurações de fluxo do utilizador como estas para controlar comportamentos de experiência de identidade nas suas aplicações:

* Tipos de conta utilizados para iniciar sessão, tais como contas sociais como um Facebook, ou contas locais que usam um endereço de e-mail e senha para iniciar sessão
* Atributos a recolher junto do consumidor, tais como o primeiro nome, código postal ou país/região de residência
* Azure Multi-Factor Authentication (MFA)
* Personalização da interface do utilizador
* Conjunto de reclamações num símbolo que a sua aplicação recebe após o utilizador completar o fluxo do utilizador
* Gestão de sessões
* ... e mais.

Os cenários de identidade mais comuns para a maioria das aplicações móveis, web e de uma página única podem ser definidos e implementados eficazmente com fluxos de utilizadores. Recomendamos que utilize os fluxos de utilizadores incorporados, a menos que tenha cenários complexos de viagem de utilizador que requerem toda a flexibilidade das políticas personalizadas.

Saiba mais sobre os fluxos de utilizadores nos [fluxos de utilizadores no Diretório Ativo Azure B2C](user-flow-overview.md).

### <a name="custom-policy"></a>Política personalizada

As políticas personalizadas desbloqueiam o acesso ao poder total do motor de orquestração Identity Experience Framework (IEF). Com políticas personalizadas, pode aproveitar o IEF para construir quase qualquer autenticação, registo de utilizador ou experiência de edição de perfis que possa imaginar.

O Quadro de Experiência de Identidade dá-lhe a capacidade de construir viagens de utilizador com qualquer combinação de passos. Por exemplo:

* Federado com outros fornecedores de identidade
* Desafios de autenticação multifactor (MFA) de primeiro e terceiros
* Recolher qualquer entrada do utilizador
* Integrar com sistemas externos utilizando a comunicação REST API

Cada viagem de utilizador é definida por uma política, e você pode construir quantas ou poucas políticas você precisa para permitir a melhor experiência de utilizador para a sua organização.

![Diagrama mostrando um exemplo de uma viagem complexa do utilizador ativada pelo IEF](media/technical-overview/custom-policy.png)

Uma política personalizada é definida por vários ficheiros XML que se referem uns aos outros numa cadeia hierárquica. Os elementos XML definem as alegações de esquema, transformações de sinistros, definições de conteúdo, fornecedores de sinistros, perfis técnicos, passos de orquestração de viagens de utilizador e outros aspetos da experiência de identidade.

A poderosa flexibilidade das políticas personalizadas é mais adequada para quando você precisa construir cenários de identidade complexos. Os desenvolvedores que configuram políticas personalizadas devem definir as relações fidedignas em detalhes cuidadosos para incluir pontos finais de metadados, definições exatas de troca de reclamações e configurar segredos, chaves e certificados conforme necessário por cada fornecedor de identidade.

Saiba mais sobre políticas personalizadas [em políticas personalizadas no Diretório Ativo Azure B2C](custom-policy-overview.md).

## <a name="protocols-and-tokens"></a>Protocolos e fichas

O Azure AD B2C suporta os [protocolos OpenID Connect e OAuth 2.0](protocols-overview.md) para viagens de utilizador. Na implementação do OpenID Connect do Azure AD B2C, a sua aplicação começa o percurso do utilizador através da emissão de pedidos de autenticação para o Azure AD B2C.

O resultado de um pedido ao Azure AD B2C é um símbolo de segurança, como um símbolo de [identificação ou ficha](tokens-overview.md)de acesso . Este símbolo de segurança define a identidade do utilizador. Os tokens são recebidos de pontos finais Azure AD B2C como o `/token` ponto final ou o ponto `/authorize` final. Com estas fichas, pode aceder a reclamações que podem ser usadas para validar uma identidade e permitir o acesso a recursos seguros.

Para identidades externas, o Azure AD B2C apoia a federação com qualquer Fornecedor de identidade OAuth 1.0, OAuth 2.0, OpenID Connect, SAML e WS-Fed.

![Diagrama de aplicação de cliente baseada em OIDC federating com um IDP baseado em SAML](media/technical-overview/protocols.png)

O diagrama anterior mostra como o Azure AD B2C pode comunicar utilizando uma variedade de protocolos dentro do mesmo fluxo de autenticação:

1. A aplicação da parte de contagem inicia um pedido de autorização ao Azure AD B2C utilizando o OpenID Connect.
1. Quando um utilizador da aplicação opta por assinar com um fornecedor de identidade externo que utiliza o protocolo SAML, o Azure AD B2C invoca o protocolo SAML para comunicar com esse fornecedor de identidade.
1. Depois de o utilizador concluir a operação de início de sessão com o fornecedor de identidade externo, o Azure AD B2C devolve o token à aplicação da parte que depende utilizando o OpenID Connect.

## <a name="application-integration"></a>Integração de aplicações

Quando um utilizador quer iniciar sessão na sua aplicação, seja uma aplicação web, móvel, de secretária ou de uma única página (SPA), a aplicação inicia um pedido de autorização para um ponto final fornecido pelo utilizador ou personalizado. O fluxo do utilizador ou a política personalizada definem e controla a experiência do utilizador. Quando completam um fluxo de utilizador, por exemplo, o fluxo *de inscrição ou de entrada,* o Azure AD B2C gera um símbolo e, em seguida, redireciona o utilizador de volta para a sua aplicação.

![Aplicativo móvel com setas mostrando fluxo entre a página de sinalização Azure AD B2C](media/technical-overview/app-integration.png)

Várias aplicações podem usar o mesmo fluxo de utilizador ou política personalizada. Uma única aplicação pode usar vários fluxos de utilizador ou políticas personalizadas.

Por exemplo, para iniciar sessão numa aplicação, a aplicação utiliza o *sinal de inscrição ou de inscrição no* fluxo do utilizador. Depois de o utilizador ter assinado o seu contrato, poderá querer editar o seu perfil, pelo que a aplicação inicia outro pedido de autorização, desta vez utilizando o fluxo de utilizador de edição de *perfil.*

## <a name="seamless-user-experiences"></a>Experiências de utilizador sem emenda

No Azure AD B2C, pode criar as experiências de identidade dos seus utilizadores para que as páginas que são mostradas se misturem perfeitamente com o aspeto e a sensação da sua marca. Obtém o controlo quase total dos conteúdos HTML e CSS apresentados aos seus utilizadores quando estes procedem através das viagens de identidade da sua aplicação. Com esta flexibilidade, pode manter a consistência da marca e visual entre a sua aplicação e o Azure AD B2C.

![Screenshots da página de inscrição personalizada da marca](media/technical-overview/seamless-ux.png)

Para obter informações sobre personalização ui, consulte sobre a personalização da [interface do utilizador no Diretório Ativo Azure B2C](customize-ui-overview.md).

## <a name="localization"></a>Localização

A personalização linguística em Azure AD B2C permite-lhe acomodar diferentes idiomas de acordo com as necessidades do seu cliente. A Microsoft fornece as traduções para 36 idiomas, mas também pode fornecer as suas próprias traduções para qualquer idioma. Mesmo que a sua experiência seja fornecida apenas para uma única língua, pode personalizar qualquer texto nas páginas.

![Três páginas de inscrição que mostram texto ui em diferentes idiomas](media/technical-overview/localization.png)

Veja como funciona a localização na personalização linguística [no Diretório Ativo Azure B2C](user-flow-language-customization.md).

## <a name="add-your-own-business-logic"></a>Adicionar a sua própria lógica de negócio

Se optar por utilizar políticas personalizadas, pode integrar-se com uma API RESTful numa viagem de utilizador para adicionar a sua própria lógica de negócio à viagem. Por exemplo, o Azure AD B2C pode trocar dados com um serviço RESTful para:

* Exiba mensagens de erro personalizadas para o utilizador.
* Valide a entrada do utilizador para evitar que os dados mal formados persistam no seu diretório de utilizador. Por exemplo, pode modificar os dados introduzidos pelo utilizador, tais como capitalizar o primeiro nome se os introduzidos em todas as minúsculas.
* Enriqueça os dados dos utilizadores integrando-se ainda mais com a sua aplicação de linha de negócio corporativa.
* Utilizando chamadas RESTful, pode enviar notificações push, atualizar bases de dados corporativas, executar um processo de migração de utilizadores, gerir permissões, auditar bases de dados e muito mais.

Os programas de fidelização são outro cenário possibilitado pelo apoio do Azure AD B2C para chamar APIs de REST. Por exemplo, o seu serviço RESTful pode receber o endereço de e-mail de um utilizador, consultar a sua base de dados de clientes e, em seguida, devolver o número de fidelização do utilizador ao Azure AD B2C. Os dados de devolução podem ser armazenados na conta de diretório do utilizador no Azure AD B2C, depois ser avaliados em etapas subsequentes da apólice, ou ser incluídos no token de acesso.

![Integração de linha de negócio numa aplicação móvel](media/technical-overview/lob-integration.png)

Pode adicionar uma chamada REST API em qualquer passo da viagem de utilizador definida por uma política personalizada. Por exemplo, pode chamar uma API REST:

* Durante o início de sessão, pouco antes do Azure AD B2C validar as credenciais
* Imediatamente após o inscreveu-se
* Antes do Azure AD B2C criar uma nova conta no diretório
* Depois do Azure AD B2C cria uma nova conta no diretório
* Antes de Azure AD B2C emitir um sinal de acesso

Para ver como utilizar políticas personalizadas para integração restful API em Azure AD B2C, consulte [Integrar rest API reclama trocas de reclamações na sua política personalizada Azure AD B2C.](custom-policy-rest-api-intro.md)

## <a name="protect-customer-identities"></a>Proteger as identidades dos clientes

O Azure AD B2C cumpre a segurança, privacidade e outros compromissos descritos no [Microsoft Azure Trust Center.](https://www.microsoft.com/trustcenter/cloudservices/azure)

As sessões são modeladas como dados encriptados, com a chave de desencriptação conhecida apenas pelo Serviço de Token de Segurança Azure AD B2C. É usado um algoritmo de encriptação forte, AES-192. Todos os caminhos de comunicação estão protegidos com TLS para confidencialidade e integridade. O nosso Serviço de Token de Segurança utiliza um certificado de Validação Estendida (EV) para TLS. Em geral, o Serviço de Token de Segurança atenua os ataques de scripts transversais (XSS) ao não renderindo uma entrada não confiável.

![Diagrama de dados seguros em trânsito e em repouso](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Acesso aos dados dos utilizadores

Os inquilinos da Azure AD B2C partilham muitas características com os inquilinos da empresa Azure Ative Directory utilizados para colaboradores e parceiros. Os aspetos partilhados incluem mecanismos de visualização de funções administrativas, atribuição de funções e atividades de auditoria.

Pode atribuir funções ao controlo de quem pode executar determinadas ações administrativas no Azure AD B2C, incluindo:

* Criar e gerir todos os aspetos dos fluxos de utilizadores
* Criar e gerir o esquema de atributo disponível para todos os fluxos de utilizadores
* Configurar fornecedores de identidade para uso na federação direta
* Criar e gerir políticas-quadro de confiança no Quadro de Experiência seleção de Identidade (políticas personalizadas)
* Gerir segredos para a federação e encriptação no Quadro de Experiência de Identidade (políticas personalizadas)

Para obter mais informações sobre as funções da Azure AD, incluindo o suporte à administração Do AD B2C, consulte [as permissões de funções do Administrador no Diretório Ativo do Azure](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

A autenticação multifactor Azure AD B2C (MFA) ajuda a salvaguardar o acesso a dados e aplicações, mantendo a simplicidade para os seus utilizadores. Proporciona segurança adicional exigindo uma segunda forma de autenticação, e oferece autenticação forte, oferecendo uma gama de métodos de autenticação fáceis de usar. Os seus utilizadores podem ou não ser contestados para o MFA com base em decisões de configuração que pode tomar como administrador.

Veja como ativar o MFA nos fluxos de utilizadores em Ativação de [autenticação multifactor no Diretório Ativo Azure B2C](custom-policy-multi-factor-authentication.md).

### <a name="smart-account-lockout"></a>Bloqueio de conta inteligente

Para evitar tentativas de adivinhação de senhas de força bruta, o Azure AD B2C utiliza uma estratégia sofisticada para bloquear contas com base no IP do pedido, as palavras-passe inseridas e vários outros fatores. A duração do bloqueio é automaticamente aumentada com base no risco e no número de tentativas.

![Bloqueio inteligente da conta](media/technical-overview/smart-lockout1.png)

Para obter mais informações sobre a gestão das definições de proteção de [passwords, consulte Gerir ameaças a recursos e dados no Diretório Ativo Azure B2C](threat-management.md).

### <a name="password-complexity"></a>Complexidade da palavra-passe

Durante o reset de inscrição ou de senha, os seus utilizadores devem fornecer uma palavra-passe que cumpra as regras de complexidade. Por predefinição, o Azure AD B2C aplica uma forte política de senhas. O Azure AD B2C também fornece opções de configuração para especificar os requisitos de complexidade das palavras-passe que os seus clientes utilizam.

Pode configurar requisitos de complexidade de palavras-passe tanto nos [fluxos](user-flow-password-complexity.md) do utilizador como nas [políticas personalizadas.](custom-policy-password-complexity.md)

## <a name="auditing-and-logs"></a>Auditoria e registos

O Azure AD B2C emite registos de auditoria contendo informações de atividade sobre os seus recursos, fichas emitidas e acesso a administradores. Pode utilizar estes registos de auditoria para compreender a atividade da plataforma e diagnosticar problemas. As entradas de registo de auditoria estão disponíveis logo após a atividade que gerou o evento ocorrer.

Num registo de auditoria, disponível para o seu inquilino Azure AD B2C ou para um determinado utilizador, pode encontrar informações que incluem:

* Atividades relativas à autorização de um utilizador para aceder aos recursos B2C (por exemplo, um administrador que aceda a uma lista de políticas B2C)
* Atividades relacionadas com atributos de diretório recuperados quando um administrador assina na utilização do portal Azure
* Criar, ler, atualizar e eliminar operações (CRUD) em aplicações B2C
* Operações CRUD em chaves armazenadas num recipiente-chave B2C
* Operações crud sobre recursos B2C (por exemplo, políticas e fornecedores de identidade)
* Validação das credenciais de utilizador e emissão simbólica

![Registo de auditoria individual do utilizador mostrado no portal Azure](media/technical-overview/audit-log.png)

Para obter mais detalhes sobre os registos de auditoria, consulte aceder aos registos de [auditoria do Azure AD B2C](view-audit-logs.md).

### <a name="usage-insights"></a>Insights de utilização

O Azure AD B2C permite-lhe descobrir quando as pessoas se inscrevem ou se inscrevem na sua aplicação web, onde estão localizados os seus utilizadores, e quais os navegadores e sistemas operativos que utilizam. Ao integrar os Insights de Aplicação Azure no Azure AD B2C utilizando políticas personalizadas, pode obter informações sobre como as pessoas se inscrevem, inscrevem, redefiniram a sua palavra-passe ou editaram o seu perfil. Com esse conhecimento, pode tomar decisões baseadas em dados para os seus próximos ciclos de desenvolvimento.

Saiba mais sobre a análise de utilização no [comportamento do utilizador da Pista no Diretório Ativo Do Azure B2C utilizando insights](analytics-with-application-insights.md)de aplicação .

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma visão mais profunda das características e aspetos técnicos do Azure Ative Directory B2C, inicie-se com o serviço criando um inquilino B2C:

> [!div class="nextstepaction"]
> [Tutorial: Criar um inquilino Azure Ative Directory B2C >](tutorial-create-tenant.md)