---
title: Visão geral técnica e de recursos-Azure Active Directory B2C
description: Uma introdução detalhada aos recursos e às tecnologias no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4d45e4c79f46061ca177858fd517153fb5f29c41
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123827"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Visão geral técnica e de recursos do Azure Active Directory B2C

Um complemento do [Azure Active Directory B2C](active-directory-b2c-overview.md), este artigo fornece uma introdução mais aprofundada ao serviço. Discutimos aqui os principais recursos com os quais você trabalha no serviço, seus recursos e como eles permitem que você forneça uma experiência de identidade totalmente personalizada para seus clientes em seus aplicativos.

## <a name="azure-ad-b2c-tenant"></a>Azure AD B2C locatário

No Azure Active Directory B2C (Azure AD B2C), um *locatário* representa sua organização e é um diretório de usuários. Cada inquilino do Azure AD B2C é distinto e independente dos outros inquilinos do Azure AD B2C. Um locatário Azure AD B2C é diferente de um locatário Azure Active Directory, que talvez você já tenha.

Os principais recursos com os quais você trabalha em um locatário Azure AD B2C são:

* **Diretório** -o *diretório* é onde Azure ad B2C armazena as credenciais de seus usuários e os dados de perfil, bem como os registros do aplicativo.
* **Registros de aplicativo** -você registra seus aplicativos Web, móveis e nativos com Azure ad B2C para habilitar o gerenciamento de identidade. Além disso, todas as APIs que você deseja proteger com Azure AD B2C.
* **Fluxos de usuário** e **políticas personalizadas** – as experiências de identidade internas (fluxos de usuário) e totalmente personalizáveis (políticas personalizadas) para seus aplicativos.
  * Use *fluxos de usuário* para configuração rápida e habilitação de tarefas de identidade comuns, como inscrever, entrar e edição de perfil.
  * Use *políticas personalizadas* para habilitar experiências de usuário não apenas para tarefas comuns de identidade, mas também para a criação de suporte para fluxos de trabalho de identidade complexos exclusivos para sua organização, clientes, funcionários, parceiros e cidadãos.
* **Provedores de identidade** – configurações de Federação para:
  * Provedores de identidade *social* , como Facebook, LinkedIn ou Twitter, que você deseja dar suporte em seus aplicativos.
  * Provedores de identidade *externos* que dão suporte a protocolos de identidade padrão como OAuth 2,0, OpenID Connect e muito mais.
  * Contas *locais* que permitem que os usuários se inscrevam e entrem com um nome de usuário (ou endereço de email ou outra ID) e senha.
* **Chaves** – adicione e gerencie chaves de criptografia para assinar e validar tokens.

Um locatário Azure AD B2C é o primeiro recurso que você precisa criar para começar a usar Azure AD B2C. Saiba como no [tutorial: Criar um locatário](tutorial-create-tenant.md)Azure Active Directory B2C.

## <a name="accounts-in-azure-ad-b2c"></a>Contas no Azure AD B2C

Azure AD B2C define vários tipos de contas de usuário. Azure Active Directory, Azure Active Directory B2B e Azure Active Directory B2C compartilham esses tipos de conta.

* **Conta corporativa** – os usuários com contas corporativas podem gerenciar recursos em um locatário e, com uma função de administrador, também podem gerenciar locatários. Usuários com contas de trabalho podem criar novas contas de consumidor, redefinir senhas, bloquear/desbloquear contas e definir permissões ou atribuir uma conta a um grupo de segurança.
* **Conta de convidado** -usuários externos que você convida para seu locatário como convidados. Um cenário típico para convidar um usuário convidado para seu locatário de Azure AD B2C é compartilhar responsabilidades de administração.
* **Conta do consumidor** -as contas de consumidor são as contas criadas no diretório Azure ad B2C quando os usuários concluem o percurso do usuário de inscrição em um aplicativo que você registrou em seu locatário.

![Azure AD B2C página de gerenciamento de usuário no portal do Azure](media/technical-overview/portal-01-users.png)<br/>*Figuras Diretório de usuário dentro de um locatário de Azure AD B2C no portal do Azure*

### <a name="consumer-accounts"></a>Contas de consumidor

Com uma conta de *consumidor* , os usuários podem entrar nos aplicativos que você protegeu com Azure ad B2C. Os usuários com contas de consumidor não podem, no entanto, acessar recursos do Azure, por exemplo, o portal do Azure.

Uma conta de consumidor pode ser associada a esses tipos de identidade:

* Identidade **local** , com o nome de usuário e a senha armazenados localmente no diretório Azure ad B2C. Geralmente, nos referimos a essas identidades como "contas locais".
* Identidades **sociais** ou **corporativas** , nas quais a identidade do usuário é gerenciada por um provedor de identidade federado, como Facebook, Microsoft, ADFS ou Salesforce.

Um usuário com uma conta de consumidor pode entrar com várias identidades, por exemplo, nome de usuário, email, ID do funcionário, ID do governo e outros. Uma única conta pode ter várias identidades locais e sociais.

![Identidades da conta do consumidor](media/technical-overview/identities.png)<br/>*Figuras Uma única conta de consumidor com várias identidades no Azure AD B2C*

Azure AD B2C permite que você gerencie atributos comuns de perfis de conta de consumidor como nome de exibição, sobrenome, nome, cidade e outros. Você também pode estender o esquema do Azure AD para armazenar informações adicionais sobre seus usuários. Por exemplo, seu país ou residência, idioma preferencial e preferências, como se desejam assinar um boletim informativo ou habilitar a autenticação multifator.

Saiba mais sobre os tipos de conta de usuário em Azure AD B2C em [visão geral das contas de usuário no Azure Active Directory B2C](user-overview.md).

## <a name="external-identity-providers"></a>Fornecedores de identidade externos

Você pode configurar Azure AD B2C para permitir que os usuários entrem em seu aplicativo com credenciais de IdP (provedores de identidade empresarial) ou sociais externos. O Azure AD B2C dá suporte a provedores de identidade externos, como Facebook, conta Microsoft, Google, Twitter e qualquer provedor de identidade que ofereça suporte a protocolos OAuth 1,0, OAuth 2,0, OpenID Connect, SAML ou WS-Federation.

![Fornecedores de identidade externos](media/technical-overview/external-idps.png)

Com a Federação do provedor de identidade externa, você pode oferecer aos consumidores a capacidade de entrar com suas contas sociais ou corporativas existentes, sem precisar criar uma nova conta apenas para seu aplicativo.

Na página inscrever-se ou entrar, Azure AD B2C apresenta uma lista de provedores de identidade externos que o usuário pode escolher para entrar. Depois de selecionar um dos provedores de identidade externos, eles são utilizados (redirecionados) para o site do provedor selecionado para concluir o processo de entrada. Depois que o usuário entrar com êxito, ele será retornado para Azure AD B2C para autenticação da conta em seu aplicativo.

![Exemplo de conexão móvel com uma conta social (Facebook)](media/technical-overview/external-idp.png)

Para ver como adicionar provedores de identidade no Azure ad B2C, consulte [tutorial: Adicione provedores de identidade aos seus aplicativos no](tutorial-add-identity-providers.md)Azure Active Directory B2C.

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Experiências de identidade: fluxos de usuário ou políticas personalizadas

A estrutura de política extensível do Azure AD B2C é sua força principal. As políticas descrevem as experiências de identidade de seus usuários, como inscrever-se, entrar e edição de perfil.

Em Azure AD B2C, há dois caminhos principais que você pode tomar para fornecer essas experiências de identidade: fluxos de usuário e políticas personalizadas.

* Os **fluxos de usuário** são políticas predefinidas, internas e configuráveis que fornecemos para que você possa criar experiências de inscrição, entrada e edição de política em minutos.

* **As políticas personalizadas** permitem que você crie seus próprios percursos de usuário para cenários de experiência de identidade complexos.

Os fluxos de usuário e as políticas personalizadas são alimentados pela *estrutura de experiência de identidade*, pelo mecanismo de orquestração de política do Azure ad B2C.

### <a name="user-flow"></a>Fluxo de utilizador

Para ajudá-lo a configurar rapidamente as tarefas de identidade mais comuns, a portal do Azure inclui várias políticas predefinidas e configuráveis chamadas de *fluxos de usuário*.

Você pode definir configurações de fluxo de usuário como essas para controlar os comportamentos de experiência de identidade em seus aplicativos:

* Tipos de conta usados para entrar, como contas sociais, como um Facebook, ou contas locais que usam um endereço de email e senha para entrar
* Atributos a serem coletados do consumidor, como primeiro nome, CEP ou país de residência
* MFA (autenticação multifator do Azure)
* Personalização da interface do usuário
* Conjunto de declarações em um token que seu aplicativo recebe depois que o usuário conclui o fluxo do usuário
* Gestão de sessões
* ... e muito mais.

Os cenários de identidade mais comuns para a maioria dos aplicativos móveis, da Web e de página única podem ser definidos e implementados com eficiência com fluxos de usuário. Recomendamos que você use os fluxos de usuário internos, a menos que você tenha cenários de percurso de usuário complexos que exigem a flexibilidade total das políticas personalizadas.

Saiba mais sobre fluxos de usuário em [fluxos de usuário no Azure Active Directory B2C](active-directory-b2c-reference-policies.md).

### <a name="custom-policy"></a>Política personalizada

As políticas personalizadas desbloqueiam o acesso a todo o poder do mecanismo de orquestração do IEF (Identity Experience Framework). Com as políticas personalizadas, você pode aproveitar o IEF para criar praticamente qualquer autenticação, registro de usuário ou experiência de edição de perfil que você possa imaginar.

A estrutura de experiência de identidade oferece a capacidade de construir percursos do usuário com qualquer combinação de etapas. Por exemplo:

* Federar com outros provedores de identidade
* Desafios de MFA (autenticação multifator) do primeiro e de terceiros
* Coletar qualquer entrada do usuário
* Integre com sistemas externos usando a comunicação da API REST

Cada percurso desse usuário é definido por uma política, e você pode criar tantas políticas quanto precisar para habilitar a melhor experiência de usuário para sua organização.

![Diagrama mostrando um exemplo de uma jornada de usuário complexo habilitada pelo IEF](media/technical-overview/custom-policy.png)

Uma política personalizada é definida por vários arquivos XML que fazem referência entre si em uma cadeia hierárquica. Os elementos XML definem o esquema de declarações, transformações de declarações, definições de conteúdo, provedores de declarações, perfis técnicos, etapas de orquestração de jornada do usuário e outros aspectos da experiência de identidade.

A poderosa flexibilidade das políticas personalizadas é mais adequada para quando você precisa criar cenários de identidade complexos. Os desenvolvedores que configuram políticas personalizadas devem definir as relações confiáveis com detalhes cuidadosos para incluir pontos de extremidade de metadados, definições exatas de troca de declarações e configurar segredos, chaves e certificados conforme a necessidade de cada provedor de identidade.

Saiba mais sobre políticas personalizadas em [políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-overview-custom.md).

## <a name="protocols-and-tokens"></a>Protocolos e tokens

O Azure AD B2C dá suporte ao [OpenID Connect e aos protocolos OAuth 2,0](active-directory-b2c-reference-protocols.md) para viagens do usuário. Na implementação do OpenID Connect do Azure AD B2C, a sua aplicação começa o percurso do utilizador através da emissão de pedidos de autenticação para o Azure AD B2C.

O resultado de uma solicitação para Azure AD B2C é um token de segurança, como um token de [ID ou token de acesso](active-directory-b2c-reference-tokens.md). Esse token de segurança define a identidade do usuário. Os tokens são recebidos de pontos de extremidade de `/token` Azure ad B2C `/authorize` como o ou o Endpoint. Com esses tokens, você pode acessar declarações que podem ser usadas para validar uma identidade e permitir o acesso a recursos seguros.

Para identidades externas, o Azure AD B2C dá suporte à Federação com um provedor de identidade OAuth 1,0, OAuth 2,0, OpenID Connect, SAML e WS-alimentado.

![Diagrama de aplicativo cliente baseado em OIDC federado com um IdP baseado em SAML](media/technical-overview/protocols.png)

O diagrama anterior mostra como Azure AD B2C pode se comunicar usando uma variedade de protocolos dentro do mesmo fluxo de autenticação:

1. O aplicativo de terceira parte confiável inicia uma solicitação de autorização para Azure AD B2C usando o OpenID Connect.
1. Quando um usuário do aplicativo opta por entrar usando um provedor de identidade externo que usa o protocolo SAML, Azure AD B2C invoca o protocolo SAML para se comunicar com esse provedor de identidade.
1. Depois que o usuário concluir a operação de entrada com o provedor de identidade externo, Azure AD B2C retornará o token para o aplicativo de terceira parte confiável usando o OpenID Connect.

## <a name="application-integration"></a>Integração de aplicações

Quando um usuário deseja entrar em seu aplicativo, seja um aplicativo Web, móvel, de área de trabalho ou de página única (SPA), o aplicativo inicia uma solicitação de autorização para um fluxo de usuário ou um ponto de extremidade personalizado fornecido pela política. O fluxo do usuário ou a política personalizada define e controla a experiência do usuário. Quando eles concluem um fluxo de usuário, por exemplo, o fluxo de *inscrição ou de entrada* , Azure ad B2C gera um token e redireciona o usuário de volta para seu aplicativo.

![Aplicativo móvel com setas mostrando o fluxo entre Azure AD B2C página de entrada](media/technical-overview/app-integration.png)

Vários aplicativos podem usar o mesmo fluxo de usuário ou política personalizada. Um único aplicativo pode usar vários fluxos de usuário ou políticas personalizadas.

Por exemplo, para entrar em um aplicativo, o aplicativo usa o fluxo de usuário de *inscrição ou de entrada* . Depois que o usuário tiver entrado, talvez queira editar seu perfil, para que o aplicativo inicie outra solicitação de autorização, desta vez usando o fluxo de usuário de *edição de perfil* .

## <a name="seamless-user-experiences"></a>Experiências de usuário contínuos

Em Azure AD B2C, você pode criar experiências de identidade dos usuários para que as páginas que eles são mostradas mesclem-se diretamente com a aparência de sua marca. Você Obtém um controle quase total do conteúdo HTML e CSS apresentado aos seus usuários quando eles passam pelas viagens de identidade do aplicativo. Com essa flexibilidade, você pode manter a consistência visual e de marca entre seu aplicativo e Azure AD B2C.

![Capturas de tela da página de entrada de inscrição personalizada da marca](media/technical-overview/seamless-ux.png)

Para obter informações sobre a personalização da interface do usuário, consulte [sobre a personalização da interface dos usuários no Azure Active Directory B2C](customize-ui-overview.md).

## <a name="localization"></a>Localização

A personalização de idiomas no Azure AD B2C permite que você acomode diferentes idiomas para atender às suas necessidades de clientes. A Microsoft fornece as traduções para 36 idiomas, mas você também pode fornecer suas próprias traduções para qualquer idioma. Mesmo que sua experiência seja fornecida apenas para um único idioma, você pode personalizar qualquer texto nas páginas.

![Três páginas de entrada de inscrição mostrando o texto da interface do usuário em idiomas diferentes](media/technical-overview/localization.png)

Veja como a localização funciona na [personalização da linguagem no Azure Active Directory B2C](active-directory-b2c-reference-language-customization.md).

## <a name="add-your-own-business-logic"></a>Adicionar a sua própria lógica de negócio

Se você optar por usar políticas personalizadas, poderá integrar com uma API RESTful em um percurso do usuário para adicionar sua própria lógica de negócios à jornada. Por exemplo, Azure AD B2C pode trocar dados com um serviço RESTful para:

* Exibir mensagens de erro amigáveis personalizadas.
* Valide a entrada do usuário para evitar que dados malformados persistam no seu diretório de usuário. Por exemplo, você pode modificar os dados inseridos pelo usuário, como colocar o primeiro nome em maiúsculas, se eles o inserirem em minúsculas.
* Enriquecer os dados do usuário integrando-os com seu aplicativo de linha de negócios corporativo.
* Usando chamadas RESTful, você pode enviar notificações por push, atualizar bancos de dados corporativos, executar um processo de migração de usuário, gerenciar permissões, auditar bancos de dados e muito mais.

Os programas de fidelidade são outro cenário habilitado pelo suporte do Azure AD B2C para chamar APIs REST. Por exemplo, o serviço RESTful pode receber o endereço de email de um usuário, consultar o banco de dados do cliente e retornar o número de fidelidade do usuário para Azure AD B2C. Os dados de retorno podem ser armazenados na conta de diretório do usuário no Azure AD B2C, então são mais avaliados nas etapas subsequentes na política ou incluídos no token de acesso.

![Integração de linha de negócios em um aplicativo móvel](media/technical-overview/lob-integration.png)

Você pode adicionar uma chamada à API REST em qualquer etapa na jornada do usuário definida por uma política personalizada. Por exemplo, você pode chamar uma API REST:

* Durante a entrada, logo antes de Azure AD B2C valida as credenciais
* Imediatamente após a entrada
* Antes que Azure AD B2C crie uma nova conta no diretório
* Depois que Azure AD B2C cria uma nova conta no diretório
* Antes de Azure AD B2C emite um token de acesso

Para saber como usar políticas personalizadas para integração de API RESTful no Azure AD B2C, consulte [integrar as trocas de declarações da API REST no percurso do usuário Azure ad B2C](active-directory-b2c-custom-rest-api-netfw.md).

## <a name="protect-customer-identities"></a>Proteger identidades do cliente

Azure AD B2C está em conformidade com a segurança, a privacidade e outros compromissos descritos na [central de confiabilidade Microsoft Azure](https://www.microsoft.com/trustcenter/cloudservices/azure).

As sessões são modeladas como dados criptografados, com a chave de descriptografia conhecida somente pelo serviço de token de segurança Azure AD B2C. Um algoritmo de criptografia forte, AES-192, é usado. Todos os caminhos de comunicação são protegidos com TLS para confidencialidade e integridade. Nosso serviço de token de segurança usa um certificado EV (validação estendida) para TLS. Em geral, o serviço de token de segurança atenua ataques XSS (script entre sites) não processando a entrada não confiável.

![Diagrama de dados seguros em trânsito e em repouso](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Acesso aos dados do usuário

Azure AD B2C locatários compartilham muitas características com locatários empresariais Azure Active Directory usados para funcionários e parceiros. Aspectos compartilhados incluem mecanismos para exibição de funções administrativas, atribuição de funções e atividades de auditoria.

Você pode atribuir funções para controlar quem pode executar determinadas ações administrativas no Azure AD B2C, incluindo:

* Criar e gerenciar todos os aspectos de fluxos de usuário
* Criar e gerenciar o esquema de atributo disponível para todos os fluxos de usuário
* Configurar provedores de identidade para uso na Federação direta
* Criar e gerenciar políticas de estrutura confiável na estrutura de experiência de identidade (políticas personalizadas)
* Gerenciar segredos para Federação e criptografia na Identity Experience Framework (políticas personalizadas)

Para obter mais informações sobre as funções do Azure AD, incluindo suporte à função de administração de Azure AD B2C, consulte [permissões de função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

Azure AD B2C a autenticação multifator (MFA) ajuda a proteger o acesso a dados e aplicativos, mantendo a simplicidade para os usuários. Ele fornece segurança adicional exigindo uma segunda forma de autenticação e fornece autenticação forte oferecendo uma variedade de métodos de autenticação fáceis de usar. Os usuários podem ou não ser desafiados pela MFA com base nas decisões de configuração que você pode fazer como administrador.

Consulte Como habilitar a MFA em fluxos de usuário em [habilitar a autenticação multifator no Azure Active Directory B2C](active-directory-b2c-reference-mfa.md).

### <a name="smart-account-lockout"></a>Bloqueio de conta inteligente

Para evitar tentativas de adivinhação de senha de força bruta, o Azure AD B2C usa uma estratégia sofisticada para bloquear contas com base no IP da solicitação, as senhas inseridas e vários outros fatores. A duração do bloqueio é aumentada automaticamente com base no risco e no número de tentativas.

![Bloqueio inteligente de conta](media/technical-overview/smart-lockout1.png)

Para obter mais informações sobre como gerenciar configurações de proteção de senha, consulte [gerenciar ameaças a recursos e dados no Azure Active Directory B2C](active-directory-b2c-reference-threat-management.md).

### <a name="password-complexity"></a>Complexidade de palavra-passe

Durante a inscrição ou a redefinição de senha, os usuários devem fornecer uma senha que atenda às regras de complexidade. Por padrão, Azure AD B2C impõe uma política de senha forte. Azure AD B2C também fornece opções de configuração para especificar os requisitos de complexidade das senhas que seus clientes usam.

Você pode configurar os requisitos de complexidade de senha em [fluxos de usuário](active-directory-b2c-reference-password-complexity.md) e [políticas personalizadas](active-directory-b2c-reference-password-complexity-custom.md).

## <a name="auditing-and-logs"></a>Auditoria e logs

Azure AD B2C emite logs de auditoria contendo informações de atividade sobre seus recursos, tokens emitidos e acesso de administrador. Você pode usar esses logs de auditoria para entender a atividade da plataforma e diagnosticar problemas. As entradas do log de auditoria estarão disponíveis logo depois que a atividade que gerou o evento ocorrer.

Em um log de auditoria, que está disponível para seu locatário Azure AD B2C ou para um usuário específico, você pode encontrar informações, incluindo:

* Atividades relacionadas à autorização de um usuário para acessar os recursos do B2C (por exemplo, um administrador acessando uma lista de políticas B2C)
* Atividades relacionadas a atributos de diretório recuperados quando um administrador entra usando o portal do Azure
* Operações CRUD (criar, ler, atualizar e excluir) em aplicativos B2C
* Operações CRUD em chaves armazenadas em um contêiner de chave B2C
* Operações CRUD em recursos do B2C (por exemplo, políticas e provedores de identidade)
* Validação de credenciais de usuário e emissão de token

![Log de auditoria de usuário individual mostrado no portal do Azure](media/technical-overview/audit-log.png)

Para obter detalhes adicionais sobre logs de auditoria, consulte [Acessando Azure ad B2C logs de auditoria](active-directory-b2c-reference-audit-logs.md).

### <a name="usage-insights"></a>Informações de uso

Azure AD B2C permite que você descubra quando as pessoas se inscrevem ou entram em seu aplicativo Web, onde os usuários estão localizados e quais navegadores e sistemas operacionais eles usam. Ao integrar as informações de Aplicativo Azure no Azure AD B2C usando políticas personalizadas, você pode obter informações sobre como as pessoas se inscrevem, entram, redefinem sua senha ou editam seu perfil. Com esse conhecimento, você pode tomar decisões controladas por dados para seus futuros ciclos de desenvolvimento.

Saiba mais sobre a análise de uso em [acompanhar o comportamento do usuário no Azure Active Directory B2C usando Application insights](active-directory-b2c-custom-guide-eventlogger-appins.md).

## <a name="next-steps"></a>Passos seguintes

Agora que você tem uma visão mais profunda dos recursos e aspectos técnicos do Azure Active Directory B2C, comece a usar o serviço criando um locatário B2C:

> [!div class="nextstepaction"]
> [Tutorial: Criar um locatário Azure Active Directory B2C >](tutorial-create-tenant.md)