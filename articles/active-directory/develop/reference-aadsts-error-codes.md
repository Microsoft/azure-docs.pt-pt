---
title: Azure Active Directory códigos de erro de autenticação e autorização | Microsoft Docs
description: Saiba mais sobre os códigos de erro AADSTS retornados do STS (serviço de token de segurança) do Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 261fe2142fc3bc45625b5d088a46ad92c34222db
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193182"
---
# <a name="authentication-and-authorization-error-codes"></a>Códigos de erro de autenticação e autorização

Procurando informações sobre os códigos de erro AADSTS que são retornados do STS (serviço de token de segurança) do Azure Active Directory (Azure AD)? Leia este documento para encontrar descrições de erro AADSTS, correções e algumas soluções alternativas sugeridas.

> [!NOTE]
> Essas informações são preliminares e estão sujeitas a alterações. Tem uma pergunta ou não consegue encontrar o que está procurando? Crie um problema do GitHub ou consulte [Opções de suporte e ajuda para os desenvolvedores](active-directory-develop-help-support.md) aprenderem sobre outras maneiras que você pode obter ajuda e suporte.
>
> Esta documentação é fornecida para diretrizes de desenvolvedor e administrador, mas nunca deve ser usada pelo próprio cliente. Os códigos de erro estão sujeitos a alterações a qualquer momento para fornecer mensagens de erro mais granulares que se destinam a ajudar o desenvolvedor durante a criação de seu aplicativo. Os aplicativos que usam uma dependência de texto ou números de código de erro serão quebrados ao longo do tempo.

## <a name="lookup-current-error-code-information"></a>Pesquisar informações do código de erro atual
Os códigos de erro e as mensagens estão sujeitos a alterações.  Para obter as informações mais recentes, dê uma olhada na página [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) para encontrar descrições de erro AADSTS, correções e algumas soluções alternativas sugeridas.  

Pesquise na parte numérica do código de erro retornado.  Por exemplo, se você recebeu o código de erro "AADSTS16000", faça uma pesquisa em [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) para "16000".  Você também pode vincular diretamente a um erro específico adicionando o número de código de erro à URL: [https://login.microsoftonline.com/error?code=16000](https://login.microsoftonline.com/error?code=16000).

## <a name="aadsts-error-codes"></a>Códigos de erro AADSTS

| Erro | Descrição |
|---|---|
| AADSTS16000 | SelectUserAccount-essa é uma interrupção gerada pelo AD do Azure, que resulta na interface do usuário que permite que ele selecione entre várias sessões de SSO válidas. Esse erro é bastante comum e pode ser retornado para o aplicativo se `prompt=none` for especificado. |
| AADSTS16001 | UserAccountSelectionInvalid – você verá esse erro se o usuário clicar em um bloco que a sessão selecionar lógica tenha rejeitado. Quando disparado, esse erro permite que o usuário se recupere escolhendo uma lista atualizada de blocos/sessões ou escolhendo outra conta. Esse erro pode ocorrer devido a um defeito de código ou condição de corrida. |
| AADSTS16002 | AppSessionSelectionInvalid-o requisito de SID especificado pelo aplicativo não foi atendido.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant-indica que o usuário não foi explicitamente adicionado ao locatário. |
| AADSTS17003 | CredentialKeyProvisioningFailed-o Azure AD não pode provisionar a chave de usuário. |
| AADSTS20001 | WsFedSignInResponseError – há um problema com seu provedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS20012 | WsFedMessageInvalid – há um problema com seu provedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS20033 | FedMetadataInvalidTenantName – há um problema com seu provedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS40008 | OAuth2IdPUnretryableServerError – há um problema com seu provedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError – há um problema com seu provedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS40010 | OAuth2IdPRetryableServerError – há um problema com seu provedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError – há um problema com seu provedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS50000 | TokenIssuanceError – há um problema com o serviço de entrada. [Crie um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para resolver este problema. |
| AADSTS50001 | InvalidResource-o recurso está desabilitado ou não existe. Verifique o código do aplicativo para garantir que você especificou a URL de recurso exata para o recurso que você está tentando acessar.  |
| AADSTS50002 | Falha na NotAllowedTenant de entrada devido a um acesso de proxy restrito no locatário. Se for sua própria política de locatário, você poderá alterar suas configurações de locatário restrito para corrigir esse problema. |
| AADSTS50003 | Falha na MissingSigningKey de entrada devido a uma chave de assinatura ou certificado ausente. Isso pode ser porque não havia chave de assinatura configurada no aplicativo. Veja as resoluções descritas em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Se você ainda vir problemas, entre em contato com o proprietário do aplicativo ou com um administrador de aplicativos. |
| AADSTS50005 | DevicePolicyError-o usuário tentou fazer logon em um dispositivo de uma plataforma que atualmente não tem suporte por meio da política de acesso condicional. |
| AADSTS50006 | InvalidSignature-falha na verificação de assinatura devido a uma assinatura inválida. |
| AADSTS50007 | PartnerEncryptionCertificateMissing-o certificado de criptografia do parceiro não foi encontrado para este aplicativo. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com a Microsoft para corrigir isso. |
| AADSTS50008 | InvalidSamlToken-a Asserção SAML está ausente ou configurada incorretamente no token. Contacte o seu fornecedor de federação. |
| AADSTS50010 | AudienceUriValidationFailed-a validação de URI de público-alvo para o aplicativo falhou porque nenhum público de token foi configurado. |
| AADSTS50011 | InvalidReplyTo-o endereço de resposta está ausente, configurado incorretamente ou não corresponde aos endereços de resposta configurados para o aplicativo.  Como uma resolução, certifique-se de adicionar esse endereço de resposta ausente ao aplicativo Azure Active Directory ou a alguém com as permissões para gerenciar seu aplicativo no Active Directory fazer isso para você.|
| AADSTS50012 | AuthenticationFailed-a autenticação falhou por um dos seguintes motivos:<ul><li>O nome da entidade do certificado de autenticação não é autorizado</li><li>Uma política de autoridade confiável correspondente não foi encontrada para o nome de entidade autorizado</li><li>A cadeia de certificados não é válida</li><li>O certificado de autenticação não é válido</li><li>A política não está configurada no locatário</li><li>A impressão digital do certificado de autenticação não é autorizada</li><li>A asserção do cliente contém uma assinatura inválida</li></ul> |
| AADSTS50013 | A InvalidAssertion-Assertion é inválida devido a vários motivos: o emissor do token não corresponde à versão da API em seu intervalo de tempo válido-o token de atualização malformado-mal-sucedido na asserção não é um token de atualização primário. |
| AADSTS50014 | GuestUserInPendingState-o resgate do usuário está em um estado pendente. A conta de usuário convidado ainda não foi totalmente criada. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState-o usuário requer o consentimento legal do grupo de idade. |
| AADSTS50017 | CertificateValidationFailed-falha na validação da certificação, motivos pelos seguintes motivos:<ul><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>Não foi possível encontrar o CrlSegment esperado</li><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>O ponto de distribuição de Delta CRL está configurado sem um ponto de distribuição de CRL correspondente</li><li>Não é possível recuperar os segmentos de CRL válidos devido a um problema de tempo limite</li><li>Não é possível transferir a CRL</li></ul>Contacte o administrador do inquilino. |
| AADSTS50020 | UserUnauthorized-os usuários não são autorizados a chamar esse ponto de extremidade. |
| AADSTS50027 | InvalidJwtToken-token JWT inválido devido aos seguintes motivos:<ul><li>Não contém a afirmação nonce, subafirmação</li><li>erro de correspondência do identificador de requerente</li><li>afirmação duplicada nas afirmações idToken</li><li>emissor inesperado</li><li>audiência inesperada</li><li>não está dentro do intervalo de tempo válido </li><li>o formato do token não é adequado</li><li>a verificação de assinatura falhou no token de ID externo do emissor</li></ul> |
| AADSTS50029 | URI inválido: o nome de domínio contém carateres inválidos. Contacte o administrador do inquilino. |
| AADSTS50032 | WeakRsaKey-indica a tentativa de usuário errôneo de usar uma chave RSA fraca. |
| AADSTS50033 | RetryableError-indica um erro transitório não relacionado às operações de banco de dados. |
| AADSTS50034 | UserAccountNotFound-para entrar neste aplicativo, a conta deve ser adicionada ao diretório. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt-o Salt necessário para gerar um identificador de par está ausente em princípio. Contacte o administrador do inquilino. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer-requerente não corresponde à declaração do emissor na asserção do cliente. Contacte o administrador do inquilino. |
| AADSTS50049 | NoSuchInstanceForDiscovery-instância desconhecida ou inválida. |
| AADSTS50050 | MalformedDiscoveryRequest-a solicitação está malformada. |
| AADSTS50053 | IdsLocked-a conta está bloqueada porque o usuário tentou entrar muitas vezes com uma ID de usuário ou senha incorreta. |
| AADSTS50055 | InvalidPasswordExpiredPassword-a senha expirou. |
| AADSTS50056 | Senha inválida ou nula-a senha não existe no repositório para este usuário. |
| AADSTS50057 | Userdisableed-a conta de usuário está desabilitada. A conta foi desativada por um administrador. |
| AADSTS50058 | UserInformationNotProvided-isso significa que um usuário não está conectado. Esse é um erro comum que é esperado quando um usuário não está autenticado e ainda não entrou.</br>Se esse erro for incentivado em um contexto de SSO em que o usuário entrou anteriormente, isso significa que a sessão de SSO não foi encontrada ou inválida.</br>Esse erro pode ser retornado para o aplicativo se prompt = nenhum for especificado. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided – informações de identificação de locatário não foram encontradas na solicitação ou implícitas por quaisquer credenciais fornecidas. O usuário pode entrar em contato com o administrador do locatário para ajudar a resolver o problema. |
| AADSTS50061 | SignoutInvalidRequest-a solicitação de saída é inválida. |
| AADSTS50064 | Falha na validação de credencial CredentialAuthenticationError em nome de usuário ou senha. |
| AADSTS50068 | SignoutInitiatorNotParticipant-a saída falhou. O aplicativo que iniciou o logon não é um participante da sessão atual. |
| AADSTS50070 | SignoutUnknownSessionIdentifier-a saída falhou. A solicitação de saída especificou um identificador de nome que não correspondeu a sessão (ões) existente (s). |
| AADSTS50071 | SignoutMessageExpired-a solicitação de logout expirou. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt-o usuário precisa se registrar para a autenticação de dois fatores (interativa). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt-a autenticação forte é necessária e o usuário não passou no desafio de MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired – devido a uma alteração de configuração feita pelo administrador ou porque você moveu para um novo local, o usuário deve usar a autenticação multifator para acessar o recurso. Tente novamente com uma nova solicitação de autorização para o recurso. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired – devido a uma alteração de configuração feita pelo administrador ou porque o usuário moveu para um novo local, o usuário precisa usar a autenticação multifator. |
| AADSTS50085 | O token de atualização precisa de início de sessão de IDP social. Pedir ao utilizador que repita o início de sessão com nome de utilizador e a palavra-passe |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError-o serviço está temporariamente indisponível. Tente novamente. |
| AADSTS50089 | O fluxo de token expirou - falha na autenticação. Peça ao usuário para tentar entrar novamente com o nome de usuário-senha. |
| AADSTS50097 | DeviceAuthenticationRequired-a autenticação do dispositivo é necessária. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized-a assinatura JWT é inválida. |
| AADSTS50105 | EntitlementGrantsNotFound-o usuário conectado não está atribuído a uma função para o aplicativo conectado. Atribua o usuário ao aplicativo. Para obter mais informações:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri-o objeto de realm de Federação solicitado não existe. Contacte o administrador do inquilino. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat-problema com o cabeçalho JWT. Contacte o administrador do inquilino. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter-a transformação de declarações contém um parâmetro de entrada inválido. Contacte o administrador de inquilinos para atualizar a política. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt-a conexão foi interrompida devido a uma redefinição de senha ou entrada de registro de senha. |
| AADSTS50126 | InvalidUserNameOrPassword-erro ao validar as credenciais devido a nome de usuário ou senha inválido. |
| AADSTS50127 | BrokerAppNotInstalled-o usuário precisa instalar um aplicativo agente para obter acesso a esse conteúdo. |
| AADSTS50128 | Nome de domínio inválido-nenhuma informação de identificação de locatário encontrada na solicitação ou implícita por qualquer credencial fornecida. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined-o ingresso no local de trabalho é necessário para registrar o dispositivo. |
| AADSTS50131 | ConditionalAccessFailed-indica vários erros de acesso condicional, como estado de dispositivo defeituoso do Windows, solicitação bloqueada devido a atividades suspeitas, política de acesso ou decisões de política de segurança. |
| AADSTS50132 | SsoArtifactInvalidOrExpired-a sessão não é válida devido à expiração da senha ou à alteração de senha recente. |
| AADSTS50133 | SsoArtifactRevoked-a sessão não é válida devido à expiração da senha ou à alteração de senha recente. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter-incorreto data center. Para autorizar uma solicitação que foi iniciada por um aplicativo no fluxo do dispositivo OAuth 2,0, a parte de autorização deve estar na mesma data center em que reside a solicitação original. |
| AADSTS50135 | PasswordChangeCompromisedPassword-a alteração de senha é necessária devido ao risco da conta. |
| AADSTS50136 | RedirectMsaSessionToApp-sessão MSA única detectada. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken-a sessão é inválida devido a um token de atualização externa ausente. |
| AADSTS50140 | KmsiInterrupt-esse erro ocorreu devido à interrupção "Mantenha-me conectado" quando o usuário estava entrando. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o ID de Correlação, o ID do Pedido e o código de Erro para obter mais detalhes. |
| AADSTS50143 | Incompatibilidade de sessão-a sessão é inválida porque o locatário do usuário não corresponde à dica de domínio devido a um recurso diferente.  [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com ID de correlação, ID de solicitação e código de erro para obter mais detalhes. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword-a senha de Active Directory do usuário expirou. Gerar uma nova senha para o usuário ou fazer com que o usuário use a ferramenta de redefinição de autoatendimento para redefinir sua senha. |
| AADSTS50146 | MissingCustomSigningKey-este aplicativo deve ser configurado com uma chave de assinatura específica do aplicativo. Ou não está configurada com uma chave destas ou a chave expirou ou ainda não é válida. |
| AADSTS50147 | MissingCodeChallenge-o tamanho do parâmetro de desafio de código não é válido. |
| AADSTS50155 | DeviceAuthenticationFailed-falha na autenticação do dispositivo para este usuário. |
| AADSTS50158 | ExternalSecurityChallenge-o desafio de segurança externa não foi atendido. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration-as declarações enviadas pelo provedor externo não são suficientes ou faltam uma declaração solicitada para o provedor externo. |
| AADSTS50166 | ExternalClaimsProviderThrottled-falha ao enviar a solicitação para o provedor de declarações. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired-o cliente é capaz de obter um token SSO por meio da extensão de contas do Windows 10, mas o token não foi encontrado na solicitação ou o token fornecido expirou. |
| AADSTS50169 | InvalidRequestBadRealm-o realm não é um realm configurado do namespace de serviço atual. |
| AADSTS50170 | MissingExternalClaimsProviderMapping-o mapeamento de controles externos está ausente. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers-o desafio externo não tem suporte para usuários de passagem. |
| AADSTS50178 | Não há suporte para o controle SessionControlNotSupportedForPassthroughUsers-Session para usuários de passagem. |
| AADSTS50180 | A autenticação WindowsIntegratedAuthMissing integrada do Windows é necessária. Ativar o inquilino para SSO Totalmente Integrado. |
| AADSTS50187 | DeviceInformationNotProvided-o serviço não pôde executar a autenticação do dispositivo. |
| AADSTS51000 | RequiredFeatureNotEnabled-o recurso está desabilitado. |
| AADSTS51001 | DomainHintMustbePresent-a dica de domínio deve estar presente com o identificador de segurança local ou com o UPN local. |
| AADSTS51004 | UserAccountNotInDirectory-a conta de usuário não existe no diretório. |
| AADSTS51005 | TemporaryRedirect-equivalente ao status de HTTP 307, que indica que as informações solicitadas estão localizadas no URI especificado no cabeçalho de local. Quando você receber esse status, siga o cabeçalho de local associado à resposta. Quando o método de solicitação original era POST, a solicitação redirecionada também usará o método POST. |
| AADSTS51006 | A autenticação ForceReauthDueToInsufficientAuth integrada do Windows é necessária. Usuário conectado usando um token de sessão que não tem a declaração de autenticação integrada do Windows. Solicite que o usuário faça logon novamente. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn-o usuário não forneceu consentimento para acesso aos recursos do LinkedIn. |
| AADSTS53000 | DeviceNotCompliant-a política de acesso condicional requer um dispositivo em conformidade e o dispositivo não é compatível. O usuário deve registrar seu dispositivo com um provedor de MDM aprovado, como o Intune. |
| AADSTS53001 | DeviceNotDomainJoined-a política de acesso condicional requer um dispositivo ingressado no domínio e o dispositivo não está ingressado no domínio. Fazer com que o usuário use um dispositivo ingressado no domínio. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp-o aplicativo usado não é um aplicativo aprovado para acesso condicional. O usuário precisa usar um dos aplicativos da lista de aplicativos aprovados para usar a fim de obter acesso. |
| AADSTS53003 | BlockedByConditionalAccess-o acesso foi bloqueado pelas políticas de acesso condicional. A política de acesso não permite a emissão de tokens. |
| AADSTS53004 | ProofUpBlockedDueToRisk-o usuário precisa concluir o processo de registro da autenticação multifator antes de acessar esse conteúdo. O utilizador deve registar-se na autenticação multifator. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist-o usuário ou administrador não consentiu usar o aplicativo com a ID X. envie uma solicitação de autorização interativa para este usuário e recurso. |
| AADSTS65004 | UserDeclinedConsent-o usuário recusou o consentimento para acessar o aplicativo. Pedir ao utilizador que repita o início de sessão e que autorize o acesso à aplicação|
| AADSTS65005 | MisconfiguredApplication-a lista de acesso ao recurso necessária do aplicativo não contém aplicativos detectáveis pelo recurso ou o aplicativo cliente solicitou acesso ao recurso, que não foi especificado em sua lista de acesso de recursos necessária ou o serviço de grafo retornou inadequado solicitação ou recurso não encontrado. Se o aplicativo oferecer suporte a SAML, você poderá ter configurado o aplicativo com o identificador incorreto (entidade). Experimente a resolução indicada para SAML através da ligação abaixo: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant-falha na autenticação. O token de atualização não é válido. O erro pode ser devido aos seguintes motivos:<ul><li>O cabeçalho de associação de token está vazio</li><li>O hash de associação de token não corresponde</li></ul> |
| AADSTS70001 | UnauthorizedClient-o aplicativo está desabilitado. |
| AADSTS70002 | InvalidClient-erro ao validar as credenciais. O client_secret especificado não corresponde ao valor esperado para este cliente. Corrija o client_secret e tente novamente. Para obter mais informações, consulte [usar o código de autorização para solicitar um token de acesso](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType-o aplicativo retornou um tipo de concessão sem suporte. |
| AADSTS70004 | InvalidRedirectUri-o aplicativo retornou um URI de redirecionamento inválido. O endereço de redirecionamento especificado pelo cliente não corresponde a nenhum dos endereços configurados ou a nenhum dos endereços na lista de aprovações OIDC. |
| AADSTS70005 | UnsupportedResponseType-o aplicativo retornou um tipo de resposta sem suporte devido aos seguintes motivos:<ul><li>o tipo de resposta ' token ' não está habilitado para o aplicativo</li><li>o tipo de resposta “id_token” requer o âmbito “OpenID”; contém um valor de parâmetro de OAuth não suportado no wctx codificado</li></ul> |
| AADSTS70007 | UnsupportedResponseMode-o aplicativo retornou um valor sem suporte de `response_mode` ao solicitar um token.  |
| AADSTS70008 | ExpiredOrRevokedGrant-o token de atualização expirou devido à inatividade. O token foi emitido em XXX e estava inativo por um determinado período de tempo. |
| AADSTS70011 | InvalidScope-o escopo solicitado pelo aplicativo é inválido. |
| AADSTS70012 | MsaServerError-ocorreu um erro de servidor durante a autenticação de um usuário do MSA (consumidor). Tente novamente. Se ele continuar falhando, [abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending-erro de fluxo de dispositivo OAuth 2,0. A autorização está pendente. O dispositivo tentará sondar a solicitação novamente. |
| AADSTS70018 | BadVerificationCode-código de verificação inválido devido à digitação do usuário no código de usuário errado para o fluxo de código do dispositivo. A autorização não foi aprovada. |
| AADSTS70019 | CodeExpired-código de verificação expirado. Peça ao usuário que tente novamente a entrada. |
| AADSTS75001 | BindingSerializationError-ocorreu um erro durante a associação de mensagens SAML. |
| AADSTS75003 | UnsupportedBindingError-o aplicativo retornou um erro relacionado à associação sem suporte (a resposta do protocolo SAML não pode ser enviada por meio de associações diferentes de HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid-o Azure AD não dá suporte à solicitação SAML enviada pelo aplicativo para SSO. |
| AADSTS75008 | RequestDeniedError-a solicitação do aplicativo foi negada porque a solicitação SAML tinha um destino inesperado. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims – o método de autenticação pelo qual o usuário autenticado com o serviço não corresponde ao método de autenticação solicitado. |
| AADSTS75016 | A solicitação de autenticação Saml2AuthenticationRequestInvalidNameIDPolicy-SAML2 tem NameIdPolicy inválida. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable-o agente de autenticação não pode se conectar ao Active Directory. Verifique se os servidores de agente são membros da mesma floresta do AD que os usuários cujas senhas precisam ser validadas e se podem se conectar ao Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout-a solicitação de validação de senha atingiu o tempo limite. Verifique se Active Directory está disponível e respondendo a solicitações dos agentes. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException-ocorreu um erro desconhecido ao processar a resposta do agente de autenticação. Repita a solicitação. Se ele continuar falhando, [abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem-o agente de autenticação não pode validar a senha do usuário. Verifique os logs do agente para obter mais informações e verifique se Active Directory está funcionando conforme o esperado. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException-o agente de autenticação não pode descriptografar a senha. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours-os usuários tentaram fazer logon fora dos horários permitidos (isso é especificado no AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew-não foi possível concluir a tentativa de autenticação devido à distorção de tempo entre a máquina que executa o agente de autenticação e o AD. Corrigir problemas de sincronização de horário. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated-falha na tentativa de autenticação Kerberos. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported-não há suporte para o pacote de autenticação. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader-nenhum cabeçalho de autorização foi encontrado. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn-o locatário não está habilitado para SSO contínuo. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat-não é possível validar o tíquete Kerberos do usuário. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid-SSO contínuo falhou porque o tíquete Kerberos do usuário expirou ou é inválido. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed-não é possível localizar o objeto de usuário com base nas informações no tíquete Kerberos do usuário. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn-o usuário que está tentando entrar no Azure AD é diferente do usuário conectado ao dispositivo. |
| AADSTS90002 | InvalidTenantName-o nome do locatário não foi encontrado no repositório de dados. Verifique se você tem a ID de locatário correta. |
| AADSTS90004 | InvalidRequestFormat-a solicitação não está formatada corretamente. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements-não é possível concluir a solicitação. A solicitação não é válida porque o identificador e a dica de logon não podem ser usados juntos.  |
| AADSTS90006 | ExternalServerRetryableError-o serviço está temporariamente indisponível.|
| AADSTS90007 | InvalidSessionId-solicitação inadequada. A ID de sessão passada não pode ser analisada. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission-o usuário ou administrador não consentiu para usar o aplicativo. No mínimo, o aplicativo requer acesso ao Azure AD especificando a permissão de entrada e leitura do perfil de usuário. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier-o aplicativo está solicitando um token para si mesmo. Esse cenário só terá suporte se o recurso especificado estiver usando a ID de aplicativo baseada em GUID. |
| AADSTS90010 | Sem suporte-não é possível criar o algoritmo. |
| AADSTS90012 | RequestTimeout-o tempo limite solicitado expirou. |
| AADSTS90013 | InvalidUserInput-a entrada do usuário não é válida. |
| AADSTS90014 | MissingRequiredField-esse código de erro pode aparecer em vários casos em que um campo esperado não está presente na credencial. |
| AADSTS90015 | QueryStringTooLong-a cadeia de caracteres de consulta é muito longa. |
| AADSTS90016 | MissingRequiredClaim-o token de acesso não é válido. A declaração necessária está ausente. |
| AADSTS90019 | MissingTenantRealm-o Azure AD não pôde determinar o identificador do locatário a partir da solicitação. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat-o formato de nome da entidade de segurança não é válido ou não atende ao formato de `name[/host][@realm]` esperado. O nome da entidade de segurança é obrigatório, o host e o realm são opcionais e podem ser definidos como NULL. |
| AADSTS90023 | InvalidRequest-a solicitação de serviço de autenticação não é válida. |
| AADSTS9002313 | InvalidRequest-a solicitação está malformada ou é inválida. -O problema aqui é porque houve algo errado com a solicitação para um determinado ponto de extremidade. A sugestão para esse problema é obter um rastreamento Fiddler do erro que está ocorrendo e procurando ver se a solicitação está realmente formatada ou não corretamente. |
| AADSTS90024 | RequestBudgetExceededError-ocorreu um erro transitório. Tente novamente. |
| AADSTS90033 | MsodsServiceUnavailable-o MSODS (Microsoft Online Directory Service) não está disponível. |
| AADSTS90036 | MsodsServiceUnretryableFailure-um erro inesperado e sem nova tentativa do serviço WCF hospedado por MSODS ocorreu. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro. |
| AADSTS90038 | NationalCloudTenantRedirection-o locatário ' Y ' especificado pertence à nuvem nacional ' X '. A instância de nuvem atual ' Z ' não faz a Federação com X. Um erro de redirecionamento de nuvem é retornado. |
| AADSTS90043 | NationalCloudAuthCodeRedirection-o recurso está desabilitado. |
| AADSTS90051 | InvalidNationalCloudId-o identificador de nuvem nacional contém um identificador de nuvem inválido. |
| AADSTS90055 | TenantThrottlingError-há muitas solicitações de entrada. Essa exceção é lançada para locatários bloqueados. |
| AADSTS90056 | BadResourceRequest-para resgatar o código de um token de acesso, o aplicativo deve enviar uma solicitação POST para o ponto de extremidade `/token`. Além disso, antes de isso, você deve fornecer um código de autorização e enviá-lo na solicitação POST para o ponto de extremidade `/token`. Consulte este artigo para obter uma visão geral do fluxo de código de autorização do OAuth 2,0: [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). Direcione o usuário para o ponto de extremidade `/authorize`, que retornará um authorization_code. Ao lançar uma solicitação para o ponto de extremidade `/token`, o usuário Obtém o token de acesso. Faça logon na portal do Azure e marque **registros de aplicativo pontos de extremidade de >** para confirmar que os dois pontos de extremidade foram configurados corretamente. |
| AADSTS90072 | PassThroughUserMfaError-a conta externa com a qual o usuário se conecta não existe no locatário ao qual ele se conectou; Portanto, o usuário não pode atender aos requisitos de MFA para o locatário. A conta deve ser adicionada primeiro como um usuário externo no locatário. Saia e entre com uma conta de usuário diferente do Azure AD. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid-ocorreu um erro quando o serviço tentou processar uma mensagem do WS-Federation. A mensagem não é válida. |
| AADSTS90082 | OrgIdWsFederationNotSupported-a política de autenticação selecionada para a solicitação não tem suporte no momento. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed-as contas de convidado não são permitidas para este site. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed-o serviço não pode emitir um token porque o objeto da empresa ainda não foi provisionado. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired-o token do usuário está expirado. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed-ocorreu um erro ao criar a mensagem de WS-Federation a partir do URI. |
| AADSTS90090 | GraphRetryableError-o serviço está temporariamente indisponível. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized-grafo retornado com um código de erro proibido para a solicitação. |
| AADSTS90094 | AdminConsentRequired-o consentimento do administrador é necessário. |
| AADSTS90100 | InvalidRequestParameter-o parâmetro está vazio ou não é válido. |
| AADSTS901002 | AADSTS901002: não há suporte para o parâmetro de solicitação ' Resource '. |
| AADSTS90101 | InvalidEmailAddress-os dados fornecidos não são um endereço de email válido. O endereço de email deve estar no formato `someone@example.com`. |
| AADSTS90102 | InvalidUriParameter-o valor deve ser um URI absoluto válido. |
| AADSTS90107 | InvalidXml-a solicitação não é válida. Verifique se os dados não têm caracteres inválidos.|
| AADSTS90114 | InvalidExpiryDate-o carimbo de data/hora de expiração do token em massa fará com que um token expirado seja emitido. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode-o código do usuário é nulo ou está vazio.|
| AADSTS90120 | InvalidDeviceFlowRequest-a solicitação já foi autorizada ou recusada. |
| AADSTS90121 | InvalidEmptyRequest-solicitação vazia inválida.|
| AADSTS90123 | IdentityProviderAccessDenied-o token não pode ser emitido porque a identidade ou o provedor de emissão de declaração negou a solicitação. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported-não há suporte para o recurso nos pontos de extremidade `/common` ou `/consumers`. Em vez disso, use o ponto de extremidade específico do `/organizations` ou do locatário. |
| AADSTS90125 | DebugModeEnrollTenantNotFound-o usuário não está no sistema. Certifique-se de que você inseriu o nome de usuário corretamente. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred-não há suporte para o tipo de usuário neste ponto de extremidade. O sistema não pode inferir o locatário do usuário do nome de usuário. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported-não há suporte para o aplicativo nos pontos de extremidade `/common` ou `/consumers`. Em vez disso, use o ponto de extremidade específico do `/organizations` ou do locatário. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated-ocorreu um erro sem nova tentativa.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound-a entidade de usuário não tem a chave de ID do NGC configurada. |
| AADSTS130005 | NgcInvalidSignature-a assinatura de chave de NGC verificada falhou.|
| AADSTS130006 | NgcTransportKeyNotFound-a chave de transporte do NGC não está configurada no dispositivo. |
| AADSTS130007 | NgcDeviceIsDisabled-o dispositivo está desabilitado. |
| AADSTS130008 | NgcDeviceIsNotFound-o dispositivo referenciado pela chave NGC não foi encontrado. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce-nonce de solicitação não fornecida. |
| AADSTS140001 | InvalidSessionKey-a chave de sessão não é válida.|
| AADSTS165900 | InvalidApiRequest-solicitação inválida. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion-não há suporte para a versão do Chrome WebView. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource-o recurso não está configurado para aceitar tokens somente de dispositivo. |
| AADSTS240001 | BulkAADJTokenUnauthorized-o usuário não está autorizado a registrar dispositivos no Azure AD. |
| AADSTS240002 | RequiredClaimIsMissing-a id_token não pode ser usada como concessão de `urn:ietf:params:oauth:grant-type:jwt-bearer`.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy-o administrador de locatários configurou uma política de segurança que bloqueia essa solicitação. Verifique as políticas de segurança definidas no nível de locatário para determinar se sua solicitação atende aos requisitos de política. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest-o aplicativo não foi encontrado no diretório/locatário. Isso pode acontecer se o aplicativo não tiver sido instalado pelo administrador do locatário ou consentido por qualquer usuário no locatário. Você pode ter configurado incorretamente o valor do identificador para o aplicativo ou enviado sua solicitação de autenticação para o locatário incorreto. |
| AADSTS700020 | InteractionRequired-a concessão de acesso requer interação. |
| AADSTS700022 | InvalidMultipleResourcesScope-o valor fornecido para o escopo do parâmetro de entrada não é válido porque contém mais de um recurso. |
| AADSTS700023 | InvalidResourcelessScope-o valor fornecido para o escopo do parâmetro de entrada não é válido ao solicitar um token de acesso. |
| AADSTS1000000 | UserNotBoundError-a API de ligação exige que o usuário do Azure AD também se autentique com um IDP externo, o que ainda não aconteceu. |
| AADSTS1000002 | BindCompleteInterruptError-a associação foi concluída com êxito, mas o usuário deve ser informado. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled-o aplicativo está desabilitado. |

## <a name="next-steps"></a>Passos seguintes

* Tem uma pergunta ou não consegue encontrar o que está procurando? Crie um problema do GitHub ou consulte [Opções de suporte e ajuda para os desenvolvedores](active-directory-develop-help-support.md) aprenderem sobre outras maneiras que você pode obter ajuda e suporte.
