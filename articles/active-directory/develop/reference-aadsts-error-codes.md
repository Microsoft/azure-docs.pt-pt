---
title: Azure códigos de erro autenticação e autorização do Active Directory | Documentos da Microsoft
description: Saiba mais sobre os códigos de erro AADSTS retornados do serviço de token de segurança do Azure AD (STS).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 02/13/2019
ms.author: celested
ms.reviewer: hirsin, justhu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ff37184cd8789c5408d02a427080db86de00b7d
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295458"
---
# <a name="authentication-and-authorization-error-codes"></a>Códigos de erro de autenticação e autorização

À procura de informações sobre os códigos de erro AADSTS retornados do serviço de token de segurança do Azure Active Directory (Azure AD) (STS)? Ler este documento para localizar AADSTS descrições de erro, correções e algumas sugerem de soluções alternativas.

> [!NOTE]
> Estas informações são preliminares e estão sujeitas a alterações. Tem uma pergunta ou não é possível localizar o que está procurando? Criar um problema do GitHub ou veja [opções de ajuda e suporte para os desenvolvedores](active-directory-develop-help-support.md) para saber mais sobre outras formas pode obter ajuda e suporte.
>
> Esta documentação é fornecida para o desenvolvedor e orientações de administração, mas nunca deve ser utilizada pelo cliente em si. Códigos de erro estão sujeitos a alterações em qualquer altura, a fim de fornecer mais granulares mensagens de erro que têm a finalidade de ajudar o desenvolvedor ao criar a sua aplicação. Os aplicativos que levam uma dependência em números de código de erro ou de texto será quebrados ao longo do tempo.  

## <a name="aadsts-error-codes"></a>Códigos de erro AADSTS

| Erro | Descrição |
|---|---|
| AADSTS16000 | SelectUserAccount - trata-se de uma interrupção emitida pelo Azure AD, o que resulta na interface do Usuário que permita ao usuário selecionar dentre várias sessões SSO válidos. Este erro é bastante comum e podem ser devolvido para o aplicativo se `prompt=none` está especificado. |
| AADSTS16001 | UserAccountSelectionInvalid - verá este erro se o usuário clica num mosaico que rejeitou a lógica de selecione de sessão. Quando acionado, este erro permite ao utilizador recuperar por escolha de uma lista atualizada de mosaicos/sessões ou ao selecionar outra conta. Este erro pode ocorrer devido a uma condição de defeitos ou corrida de código. |
| AADSTS16002 | Não foi cumprido AppSessionSelectionInvalid - o requisito de SID especificado de aplicação.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant - indica que o utilizador não tenha sido explicitamente adicionado ao inquilino. |
| AADSTS17003 | CredentialKeyProvisioningFailed - o do Azure AD não é possível aprovisionar a chave do utilizador. |
| AADSTS20001 | WsFedSignInResponseError - existe um problema com o seu fornecedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS20012 | WsFedMessageInvalid - existe um problema com o seu fornecedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS20033 | FedMetadataInvalidTenantName - existe um problema com o seu fornecedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS40008 | OAuth2IdPUnretryableServerError - existe um problema com o seu fornecedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - existe um problema com o seu fornecedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS40010 | OAuth2IdPRetryableServerError - existe um problema com o seu fornecedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - existe um problema com o seu fornecedor de identidade federada. Contacte o seu IDP para resolver este problema. |
| AADSTS50000 | TokenIssuanceError - existe um problema com o serviço de início de sessão. [Crie um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para resolver este problema. |
| AADSTS50001 | InvalidResource - o recurso está desabilitado ou não existe. Verifique o código da sua aplicação para se certificar de que especificou o URL de recurso exatamente para o recurso que está a tentar aceder.  |
| AADSTS50002 | NotAllowedTenant - início de sessão falhou devido a um acesso restrito de proxy no inquilino. Se estiver em sua própria política de inquilino, pode alterar as definições de inquilino restrito para corrigir este problema. |
| AADSTS50003 | MissingSigningKey - início de sessão falhou devido a um certificado ou chave de assinatura em falta. Isso poderá ser porque não havia nenhuma chave de assinatura configurado na aplicação. Veja as resoluções descritas em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Se continuar a ver problemas, contacte o proprietário da aplicação ou um administrador de aplicações. |
| AADSTS50005 | DevicePolicyError - o utilizador tentou iniciar sessão dispositivo a partir de uma plataforma que não é atualmente suportada através da política de acesso condicional. |
| AADSTS50006 | InvalidSignature - verificação da assinatura falhou devido a uma assinatura inválida. |
| AADSTS50007 | PartnerEncryptionCertificateMissing - não foi encontrado o certificado de encriptação do parceiro para esta aplicação. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com a Microsoft para obter isso. |
| AADSTS50008 | InvalidSamlToken - asserção de SAML está em falta ou incorrectamente configurado no token. Contacte o seu fornecedor de federação. |
| AADSTS50010 | AudienceUriValidationFailed - público-alvo URI falha na validação da aplicação, uma vez que foram configuradas sem audiências de token. |
| AADSTS50011 | InvalidReplyTo - o endereço de resposta está em faltando, configurada incorretamente, ou não corresponde ao endereço de resposta configurado para a aplicação. Experimente a resolução indicada em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Se continuar a ver problemas, contacte o proprietário da aplicação ou o administrador de aplicações. |
| AADSTS50012 | Falha na autenticação de AuthenticationFailed - para um dos seguintes motivos:<ul><li>O nome do requerente do certificado de assinatura não está autorizado</li><li>Uma política de autoridade fidedigna correspondente não foi encontrada para o nome do requerente autorizados</li><li>A cadeia de certificados não é válida</li><li>O certificado de assinatura não é válido</li><li>Política não está configurada no inquilino</li><li>Thumbprint do certificado de assinatura não está autorizado</li><li>Asserção de cliente contém uma assinatura inválida</li></ul> |
| AADSTS50013 | InvalidAssertion - asserção é inválido devido a vários motivos: O emissor de tokens não corresponde à api versão dentro de seu tempo válido no intervalo - expirado token de atualização - com formato incorreto - na asserção não é um token de atualização principal. |
| AADSTS50014 | GuestUserInPendingState - resgate do utilizador está num estado pendente. A conta de utilizador convidado não é totalmente criada ainda. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState - o utilizador necessita de consentimento de grupo etário legal. |
| AADSTS50017 | CertificateValidationFailed - certificação Falha ao validar razões pelos seguintes motivos:<ul><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>Não foi possível encontrar o CrlSegment esperado</li><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>O ponto de distribuição de Delta CRL está configurado sem um ponto de distribuição de CRL correspondente</li><li>Não é possível obter os segmentos CRL válidos devido a um problema de tempo limite</li><li>Não é possível transferir a CRL</li></ul>Contacte o administrador do inquilino. |
| AADSTS50020 | UserUnauthorized - os utilizadores não estão autorizados a chamar este ponto final. |
| AADSTS50027 | InvalidJwtToken - token JWT inválido pelos seguintes motivos:<ul><li>Não contém a afirmação nonce, subafirmação</li><li>erro de correspondência do identificador de requerente</li><li>afirmação duplicada nas afirmações idToken</li><li>emissor inesperado</li><li>audiência inesperada</li><li>não está dentro do intervalo de tempo válido </li><li>o formato do token não é adequado</li><li>a verificação de assinatura falhou no token de ID externo do emissor</li></ul> |
| AADSTS50029 | URI inválido: o nome de domínio contém carateres inválidos. Contacte o administrador do inquilino. |
| AADSTS50032 | WeakRsaKey - indica a tentativa de utilizador incorretas que tenham para utilizar uma chave RSA fraca. |
| AADSTS50033 | RetryableError - indica um erro transitório não relacionadas com as operações de base de dados. |
| AADSTS50034 | UserAccountNotFound - para iniciar sessão nesta aplicação, a conta tem de ser adicionada ao diretório. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt - salt necessário para gerar um identificador pairwise está em falta no princípio. Contacte o administrador do inquilino. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - incompatibilidades de assunto emissor de afirmações na asserção de cliente. Contacte o administrador do inquilino. |
| AADSTS50049 | NoSuchInstanceForDiscovery - desconhecido ou inválido instância. |
| AADSTS50050 | MalformedDiscoveryRequest - o pedido tem um formato incorreto. |
| AADSTS50053 | IdsLocked - a conta está bloqueada porque o utilizador tentou iniciar sessão demasiadas vezes com um ID de utilizador incorretas ou a palavra-passe. |
| AADSTS50055 | InvalidPasswordExpiredPassword - a palavra-passe expirou. |
| AADSTS50056 | Palavra-passe inválido ou nulo-palavra-passe não existe no arquivo para este utilizador. |
| AADSTS50057 | UserDisabled - a conta de utilizador está desativada. A conta foi desativada por um administrador. |
| AADSTS50058 | UserInformationNotProvided - isso significa que um utilizador não estiver conectado. Este é um erro comum que é esperado quando um usuário não está autenticado e ainda não tem sessão iniciada no.</br>Se este erro é encorajado num contexto SSO em que o utilizador iniciou sessão anteriormente no, isso significa que a sessão SSO foi ou não foi encontrado ou é inválido.</br>Este erro pode ser devolvido ao aplicativo, se a linha de comandos = não for especificado nenhum. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - informações de identificação de inquilino não foi encontrada uma solicitação explícita ou implícita por quaisquer credenciais fornecidas. O utilizador pode contactar o administrador de inquilino para o ajudar a resolver o problema. |
| AADSTS50061 | SignoutInvalidRequest - o pedido de fim de sessão é inválido. |
| AADSTS50064 | CredentialAuthenticationError - validação de credenciais em nome de utilizador ou palavra-passe falhou. |
| AADSTS50068 | SignoutInitiatorNotParticipant - fim de sessão falhou. A aplicação que iniciou a fim de sessão não é um participante na sessão atual. |
| AADSTS50070 | SignoutUnknownSessionIdentifier - fim de sessão falhou. O pedido de fim de sessão especificado um identificador de nome que não correspondeu a sessões existentes. |
| AADSTS50071 | SignoutMessageExpired - o pedido de terminar a sessão expirou. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - utilizador tem de se inscrever para autenticação de segundo fator (interativa). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - é necessária a autenticação forte e o utilizador não passou na submissão da MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - devido a uma alteração de configuração efetuadas pelo administrador, ou porque moveu para uma nova localização, o utilizador tem de utilizar a autenticação multifator para aceder ao recurso. Tente novamente com um novo pedido de autorizar para o recurso. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - devido a uma alteração de configuração efetuadas pelo administrador, ou porque o utilizador é movido para uma nova localização, é pedido ao utilizador para utilizar a autenticação multifator. |
| AADSTS50085 | O token de atualização precisa de início de sessão de IDP social. Pedir ao utilizador que repita o início de sessão com nome de utilizador e a palavra-passe |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError - o serviço está temporariamente indisponível. Tente novamente. |
| AADSTS50089 | O fluxo de token expirou - falha na autenticação. O utilizador tentar iniciar sessão novamente com o nome de utilizador-palavra-passe. |
| AADSTS50097 | DeviceAuthenticationRequired - é necessária autenticação de dispositivo. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized - a assinatura JWT é inválida. |
| AADSTS50105 | EntitlementGrantsNotFound - o utilizador com sessão iniciada não está atribuído a uma função para a aplicação com sessão iniciada. Atribua o utilizador para a aplicação. Para obter mais informações:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri - o objeto de realm de Federação solicitado não existe. Contacte o administrador do inquilino. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - problema com o cabeçalho do JWT. Contacte o administrador do inquilino. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - transformação de declarações contém parâmetro de entrada inválido. Contacte o administrador de inquilinos para atualizar a política. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - início de sessão foi interrompido devido a uma reposição de palavra-passe ou a entrada de registo de palavra-passe. |
| AADSTS50126 | InvalidUserNameOrPassword - erro ao validar as credenciais devido a nome de utilizador inválido ou a palavra-passe. |
| AADSTS50127 | BrokerAppNotInstalled - utilizador tem de instalar uma aplicação de mediação para obter acesso a este conteúdo. |
| AADSTS50128 | Nome de domínio inválido - não existem informações de identificação de inquilino encontrado na solicitação explícita ou implícita por quaisquer credenciais fornecidas. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined - associação à área de trabalho é necessária para registar o dispositivo. |
| AADSTS50131 | ConditionalAccessFailed - indica vários erros de acesso condicional, como o mau estado de dispositivo de Windows, pedido bloqueado devido a atividade suspeita, a política de acesso ou a decisões de política de segurança. |
| AADSTS50132 | SsoArtifactInvalidOrExpired - a sessão não é válida devido à expiração de palavra-passe ou a alteração de palavra-passe recentes. |
| AADSTS50133 | SsoArtifactRevoked - a sessão não é válida devido à expiração de palavra-passe ou a alteração de palavra-passe recentes. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter - Centro de dados incorreto. Para autorizar um pedido que foi iniciado por uma aplicação no fluxo de dispositivo do OAuth 2.0, a parte de autorização deve estar no mesmo centro de dados onde reside a solicitação original. |
| AADSTS50135 | PasswordChangeCompromisedPassword - alteração de palavra-passe é necessário devido ao risco de conta. |
| AADSTS50136 | RedirectMsaSessionToApp – foi detetada uma sessão única MSA. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken - a sessão é inválida devido a um token de atualização externos em falta. |
| AADSTS50140 | KmsiInterrupt - este erro ocorreu devido à interrupção "Manter sessão iniciada" quando o utilizador foi iniciar sessão. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o ID de Correlação, o ID do Pedido e o código de Erro para obter mais detalhes. |
| AADSTS50143 | Erro de correspondência de sessão - sessão é inválida porque o inquilino do utilizador não coincide com a sugestão de domínio devido a recursos diferentes.  [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o código de ID de correlação, o ID do pedido e o erro para obter mais detalhes. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - palavra-passe do utilizador do Active Directory expirou. Gerar uma nova palavra-passe para o utilizador ou o utilizador utilize a ferramenta de reposição de self-service para repor a palavra-passe. |
| AADSTS50146 | MissingCustomSigningKey - esta aplicação é necessária para ser configurado com uma chave de assinatura de aplicações específicas. Ou não está configurada com uma chave destas ou a chave expirou ou ainda não é válida. |
| AADSTS50147 | MissingCodeChallenge - o tamanho do parâmetro de desafio de código não é válido. |
| AADSTS50155 | DeviceAuthenticationFailed - autenticação do dispositivo falhou para este utilizador. |
| AADSTS50158 | Não foi cumprida ExternalSecurityChallenge - desafio de segurança externas. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - declarações enviadas por fornecedor externo não é suficiente ou em falta afirmação pediu para fornecedor externo. |
| AADSTS50166 | ExternalClaimsProviderThrottled - Falha ao enviar o pedido para o fornecedor de afirmações. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - o cliente é capaz de obter um token SSO através da extensão de contas do Windows 10, mas o token não foi encontrado no pedido ou o token fornecido expirou. |
| AADSTS50169 | InvalidRequestBadRealm - o realm não é um realm configurado do espaço de nomes de serviço atual. |
| AADSTS50170 | MissingExternalClaimsProviderMapping - os controles externos mapeamento está em falta. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - desafio externo não é suportada para os utilizadores de pass-through. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers - controlo de sessão não é suportada para os utilizadores de pass-through. |
| AADSTS50180 | É necessário WindowsIntegratedAuthMissing - autenticação integrada do Windows. Ativar o inquilino para SSO Totalmente Integrado. |
| AADSTS50187 | DeviceInformationNotProvided - o serviço não conseguiu efetuar a autenticação do dispositivo. |
| AADSTS51000 | RequiredFeatureNotEnabled - a funcionalidade está desativada. |
| AADSTS51001 | DomainHintMustbePresent - a sugestão de domínio tem de estar presente com o identificador de segurança no local ou o UPN no local. |
| AADSTS51004 | UserAccountNotInDirectory - a conta de utilizador não existe no diretório. |
| AADSTS51005 | TemporaryRedirect - equivalente ao estado HTTP 307, que indica que as informações pedidas estão localizadas no URI especificado no cabeçalho location. Quando receber este estado, siga o cabeçalho de localização associado com a resposta. Quando o método de pedido original foi POST, o pedido redirecionado também irá utilizar o método POST. |
| AADSTS51006 | É necessário ForceReauthDueToInsufficientAuth - autenticação integrada do Windows. Utilizador iniciado sessão com um token de sessão que está em falta a afirmação de autenticação integrada do Windows. Pedir ao utilizador para iniciar sessão novamente. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - o utilizador não forneceu consentimento para acesso aos recursos do LinkedIn. |
| AADSTS53000 | DeviceNotCompliant - política de acesso condicional requer um dispositivo em conformidade e o dispositivo não está em conformidade. O utilizador tem de inscrever o dispositivo com um fornecedor de MDM aprovado, como o Intune. |
| AADSTS53001 | DeviceNotDomainJoined - política de acesso condicional requer um dispositivo associado ao domínio e o dispositivo não estiver associado a um domínio. Tem do utilizador utilize um domínio associado ao dispositivo. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp - a aplicação utilizada não é uma aplicação aprovada para o acesso condicional. Utilizador tem de utilizar uma das aplicações na lista de aplicações aprovadas para utilizar para obter acesso. |
| AADSTS53003 | BlockedByConditionalAccess - acesso foi bloqueado pelas políticas de acesso condicional. A política de acesso não permite a emissão de token. |
| AADSTS53004 | ProofUpBlockedDueToRisk - utilizador tem de concluir o processo de registo de autenticação multifator antes de aceder a este conteúdo. O utilizador deve registar-se na autenticação multifator. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist - o utilizador ou o administrador não permitiu utilizar a aplicação com o ID X. envie um pedido de autorização interativo para este utilizador e o recurso. |
| AADSTS65004 | UserDeclinedConsent - o utilizador recusou-se dar consentimento para aceder à aplicação. Pedir ao utilizador que repita o início de sessão e que autorize o acesso à aplicação|
| AADSTS65005 | MisconfiguredApplication - a aplicação é necessário lista de acesso de recursos não contém aplicações Detetáveis pelo recurso ou a aplicação cliente pediu acesso a recursos, o que não foi especificado na sua lista de acesso de recurso necessário ou o serviço Graph devolvida incorreto pedido ou recurso não foi encontrado. Se a aplicação suporta SAML, poderá ter configurado a aplicação com o identificador (entidade) errado. Experimente a resolução indicada para SAML através da ligação abaixo: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - falha na autenticação. O token de atualização não é válido. Erro pode ser devido ao seguinte:<ul><li>Cabeçalho de associação de token está vazio</li><li>Não corresponde ao hash de token de enlace</li></ul> |
| AADSTS70001 | UnauthorizedClient - a aplicação está desativada. |
| AADSTS70002 | InvalidClient - erro ao validar as credenciais. O client_secret especificado não corresponde ao valor esperado para este cliente. Corrija o client_secret e tente novamente. Para mais informações, veja [utilizar o código de autorização para pedir um token de acesso](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType - a aplicação devolveu um tipo de concessão não suportado. |
| AADSTS70004 | InvalidRedirectUri - a aplicação devolveu um URI de redirecionamento inválida. O endereço de redirecionamento especificado pelo cliente não corresponde a nenhum dos endereços configurados ou a nenhum dos endereços na lista de aprovações OIDC. |
| AADSTS70005 | UnsupportedResponseType - a aplicação devolveu um tipo de resposta não suportado devido ao seguinte:<ul><li>tipo de resposta "token" não está ativado para a aplicação</li><li>o tipo de resposta “id_token” requer o âmbito “OpenID”; contém um valor de parâmetro de OAuth não suportado no wctx codificado</li></ul> |
| AADSTS70007 | UnsupportedResponseMode - a aplicação devolveu um valor não suportado de `response_mode` quando solicitar um token.  |
| AADSTS70008 | O token de atualização expirou ExpiredOrRevokedGrant - devido a inatividade. O token foi emitido em XXX e estivesse inativo para um determinado período de tempo. |
| AADSTS70011 | InvalidScope - âmbito pedido pela aplicação é inválido. |
| AADSTS70012 | MsaServerError - Ocorreu um erro de servidor ao autenticar um utilizador MSA (consumidor). Tente novamente. Se continuar a falhar, [abrir um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending - erro de fluxo de dispositivo do OAuth 2.0. Autorização está pendente. O dispositivo tentará novamente o pedido de consulta. |
| AADSTS70018 | BadVerificationCode - código de verificação inválida devido ao usuário digitando-se no código do usuário errado para o fluxo de código de dispositivo. Autorização não está aprovada. |
| AADSTS70019 | CodeExpired - código de verificação expirou. Peça ao utilizador repita o início de sessão. |
| AADSTS75001 | BindingSerializationError - Ocorreu um erro durante a vinculação de mensagem SAML. |
| AADSTS75003 | UnsupportedBindingError - a aplicação devolveu um erro relacionado com a ligação não suportada (resposta do protocolo SAML não pode ser enviada através de ligações que não seja o HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid - Azure AD não suporta o pedido SAML enviado pela aplicação para SSO. |
| AADSTS75008 | RequestDeniedError - o pedido a partir da aplicação foi negado, uma vez que o pedido SAML tinha um destino inesperado. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - o método de autenticação através do qual o utilizador autenticado com o serviço não corresponde ao pedido de método de autenticação. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - pedido de autenticação SAML2 tem NameIdPolicy inválido. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable - o agente de autenticação não conseguiu ligar ao Active Directory. Certifique-se de que os servidores de agente são membros da mesma floresta do AD os utilizadores cujas palavras-passe tem de ser validado e podem ligar ao Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout - pedido de validação da palavra-passe foi excedido. Certifique-se de que os do Active Directory está disponível e está a responder aos pedidos dos agentes. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException - Ocorreu um erro desconhecido ao processar a resposta do agente de autenticação. Repita o pedido. Se continuar a falhar, [abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - o agente de autenticação não conseguiu validar a palavra-passe do utilizador. Verifique os registos do agente para obter mais informações e certifique-se de que do Active Directory está a funcionar conforme esperado. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException - o agente de autenticação é não é possível desencriptar a palavra-passe. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours - os utilizadores tentaram iniciar sessão fora permitidos horas (isto é especificado no AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew - a tentativa de autenticação não foi possível concluir devido ao tempo distorção entre a máquina com o agente de autenticação e o AD. Hora de corrigir problemas de sincronização. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - falha na tentativa de autenticação de Kerberos. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - o pacote de autenticação não é suportada. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - não foi encontrado nenhum cabeçalho de autorização. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - o inquilino não está ativada para SSO totalmente integrado. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - não é possível validar a permissão Kerberos do utilizador. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - o SSO integrado falhou porque a permissão Kerberos do utilizador expirou ou é inválido. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - não é possível encontrar o objeto de utilizador com base nas informações na permissão Kerberos do utilizador. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - o utilizador tentar iniciar sessão com o Azure AD é diferente do utilizador com sessão iniciado no dispositivo. |
| AADSTS90002 | InvalidTenantName - o nome do inquilino não foi encontrado no arquivo de dados. Certifique-se de que tem o ID de inquilino correto. |
| AADSTS90004 | InvalidRequestFormat - o pedido não está formatado corretamente. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements - não é possível concluir o pedido. O pedido não é válido porque o identificador e sugestão de início de sessão não podem ser utilizados em conjunto.  |
| AADSTS90006 | ExternalServerRetryableError - o serviço está temporariamente indisponível.|
| AADSTS90007 | InvalidSessionId - pedido incorreto. Não é possível analisar o ID de sessão com êxito. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission - o utilizador ou administrador não tiver dado consentimento para utilizar a aplicação. No mínimo, o aplicativo requer acesso ao Azure AD, especificando o início de sessão e permissão de perfil de utilizador de leitura. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier - a aplicação está a solicitar um token para si mesmo. Este cenário é suportado apenas se o recurso especificado está a utilizar o ID da aplicação com base no GUID. |
| AADSTS90010 | NotSupported - não é possível criar o algoritmo. |
| AADSTS90012 | RequestTimeout - o pedido foi excedido. |
| AADSTS90013 | InvalidUserInput - a entrada do usuário não é válida. |
| AADSTS90014 | MissingRequiredField - este código de erro pode aparecer em vários casos, quando um campo esperado não está presente na credencial. |
| AADSTS90015 | QueryStringTooLong - a cadeia de consulta é demasiado longa. |
| AADSTS90016 | MissingRequiredClaim - não o token de acesso é válido. A afirmação necessária está em falta. |
| AADSTS90019 | MissingTenantRealm - Azure AD não foi possível determinar o identificador do inquilino no pedido. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat - o formato de nome principal não é válido ou não cumpre a esperada `name[/host][@realm]` formato. O nome do principal não é necessário, o anfitrião e o realm são opcionais e podem ser definidos como nulo. |
| AADSTS90023 | InvalidRequest - o pedido de serviço de autenticação não é válido. |
| AADSTS90024 | RequestBudgetExceededError - Ocorreu um erro transitório. Tente novamente. |
| AADSTS90033 | MsodsServiceUnavailable - o Microsoft Online Directory Service (MSODS) não está disponível. |
| AADSTS90036 | MsodsServiceUnretryableFailure - Ocorreu um erro inesperado, não repetição do serviço WCF hospedado pelo MSODS. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro. |
| AADSTS90038 | NationalCloudTenantRedirection - o inquilino especificado, 'Y' pertence a Cloud nacional 'X'. Instância de 'Z' não federar com o X da cloud atual. É devolvido um erro de redirecionamento de cloud. |
| AADSTS90043 | NationalCloudAuthCodeRedirection - a funcionalidade está desativada. |
| AADSTS90051 | InvalidNationalCloudId - o identificador de cloud nacional contém um identificador de cloud inválido. |
| AADSTS90055 | TenantThrottlingError - existem demasiados pedidos recebidos. Essa exceção é lançada para inquilinos bloqueados. |
| AADSTS90056 | BadResourceRequest - para resgatar o código para um token de acesso, a aplicação deve enviar um pedido POST para o `/token` ponto final. Além disso, antes disso, deve fornecer um código de autorização e enviá-lo no pedido POST para o `/token` ponto final. Veja este artigo para obter uma descrição geral do fluxo de código de autorização de OAuth 2.0: [ https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). Direcione o utilizador para o `/authorize` ponto final, que retornará um authorization_code. Através da publicação destacada de um pedido para o `/token` ponto final, o utilizador obtém o token de acesso. Inicie sessão no portal do Azure e verifique **registos de aplicações > pontos finais** para confirmar que os dois pontos de extremidade foram configurados corretamente. |
| AADSTS90072 | PassThroughUserMfaError - conta externa que o utilizador inicia sessão com não existe no inquilino que eles tem sessão iniciados no; portanto, o utilizador não é possível satisfazer os requisitos de MFA para o inquilino. A conta tem de ser adicionada como um utilizador externo no inquilino pela primeira vez. Termine sessão e inicie sessão com um Azure diferente conta de utilizador do AD. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid - Ocorreu um erro quando o serviço tentou processar uma mensagem de WS-Federation. A mensagem não é válida. |
| AADSTS90082 | OrgIdWsFederationNotSupported - a política de autenticação selecionado para o pedido não é atualmente suportada. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed - contas de convidado não são permitidas para este site. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed - o serviço não consegue emitir um token, porque o objeto de empresa ainda não foi aprovisionado. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired - o token do usuário DA expirou. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed - Ocorreu um erro ao criar a mensagem de WS-Federation de URI. |
| AADSTS90090 | GraphRetryableError - o serviço está temporariamente indisponível. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - Graph devolveu com um código de erro proibido para o pedido. |
| AADSTS90094 | AdminConsentRequired - o consentimento de administrador é necessário. |
| AADSTS90100 | InvalidRequestParameter - o parâmetro está vazio ou não é válido. |
| AADSTS90101 | InvalidEmailAddress - os dados fornecidos não não um endereço de e-mail válido. O endereço de e-mail tem de estar no formato `someone@example.com`. |
| AADSTS90102 | InvalidUriParameter - o valor tem de ser um URI absoluto válido. |
| AADSTS90107 | InvalidXml - o pedido não é válido. Certifique-se de que os dados não tem carateres inválidos.|
| AADSTS90114 | InvalidExpiryDate - o carimbo de hora de expiração do token em massa fará com que um token expirado a ser emitido. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode - o código de utilizador é nulo ou estar vazio.|
| AADSTS90120 | InvalidDeviceFlowRequest - o pedido já foi autorizado ou recusado. |
| AADSTS90121 | InvalidEmptyRequest - pedido vazio inválido.|
| AADSTS90123 | IdentityProviderAccessDenied - não é possível emitir o token porque o fornecedor de identidade ou afirmação de emissão negou o pedido. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported - o recurso não é suportado através da `/common` ou `/consumers` pontos de extremidade. Utilize o `/organizations` ou ponto de extremidade específico de inquilino em vez disso. |
| AADSTS90125 | DebugModeEnrollTenantNotFound - o utilizador não está no sistema. Certifique-se de que introduziu corretamente o nome de utilizador. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred - o tipo de utilizador não é suportada neste ponto final. O sistema não é possível inferir o inquilino do utilizador do nome de utilizador. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported - a aplicação não é suportada através da `/common` ou `/consumers` pontos de extremidade. Utilize o `/organizations` ou ponto de extremidade específico de inquilino em vez disso. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated - Ocorreu um erro sem repetição.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound - o principal de utilizador não tem a chave de ID de NGC configurada. |
| AADSTS130005 | Falha de NgcInvalidSignature - assinatura da chave NGC verificado.|
| AADSTS130006 | NgcTransportKeyNotFound - a chave de transporte NGC não está configurado no dispositivo. |
| AADSTS130007 | NgcDeviceIsDisabled - o dispositivo está desativada. |
| AADSTS130008 | NgcDeviceIsNotFound - o dispositivo referenciado pela chave NGC não foi encontrado. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce - nonce do pedido não foi fornecido. |
| AADSTS140001 | InvalidSessionKey - a chave de sessão não é válida.|
| AADSTS165900 | InvalidApiRequest - pedido inválido. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion - a versão de WebView do Chrome não é suportada. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - o recurso não está configurado para aceitar tokens de dispositivo. |
| AADSTS240001 | BulkAADJTokenUnauthorized - o utilizador não está autorizado a registar dispositivos no Azure AD. |
| AADSTS240002 | RequiredClaimIsMissing - não é possível utilizar o id_token como `urn:ietf:params:oauth:grant-type:jwt-bearer` conceder.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy - o administrador de inquilino tiver configurado uma política de segurança que impede este pedido. Verifique as políticas de segurança que estão definidas no nível do inquilino para determinar se o seu pedido cumpre os requisitos da política. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest - a aplicação não foi encontrada no diretório/inquilino. Isto pode acontecer se a aplicação não foi instalada pelo administrador do inquilino ou permitida por qualquer utilizador no inquilino. Poderá ter configurado incorretamente o valor do identificador para a aplicação ou enviado o pedido de autenticação para o inquilino errado. |
| AADSTS700020 | InteractionRequired - a concessão de acesso requer interação. |
| AADSTS700022 | InvalidMultipleResourcesScope - o valor fornecido para o âmbito de parâmetro de entrada não é válido porque contém mais do que um recurso. |
| AADSTS700023 | InvalidResourcelessScope - o valor fornecido para o âmbito de parâmetro de entrada não é válido quando solicitar um token de acesso. |
| AADSTS1000000 | UserNotBoundError - a API de vincular exige que o utilizador do Azure AD também autenticar com um IDP externo, o que ainda não ocorreu. |
| AADSTS1000002 | BindCompleteInterruptError - a ligação foi concluída com êxito, mas o utilizador tem de ser informado. |

## <a name="next-steps"></a>Passos Seguintes

* Tem uma pergunta ou não é possível localizar o que está procurando? Criar um problema do GitHub ou veja [opções de ajuda e suporte para os desenvolvedores](active-directory-develop-help-support.md) para saber mais sobre outras formas pode obter ajuda e suporte.
