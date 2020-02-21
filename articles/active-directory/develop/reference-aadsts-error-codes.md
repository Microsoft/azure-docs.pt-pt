---
title: Códigos de erro de autenticação e autorização da AD azure
description: Conheça os códigos de erro AADSTS que são devolvidos do serviço de token de segurança Azure AD (STS).
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
ms.date: 02/19/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: ba5af060a02e8525320f005b5d1c80534c5ca4ea
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77483929"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Códigos de erro de autorização e autenticação do Azure AD

À procura de informações sobre os códigos de erro AADSTS que são devolvidos do serviço de token de segurança Azure Ative Directory (Azure AD) Leia este documento para encontrar descrições de erros, correções e algumas soluções alternativas sugeridas.

> [!NOTE]
> Esta informação é preliminar e sujeita a alterações. Tem uma pergunta ou não consegue encontrar o que procura? Crie um problema gitHub ou veja [o Suporte e ajude as opções para os desenvolvedores](active-directory-develop-help-support.md) aprenderem sobre outras formas de obter ajuda e suporte.
>
> Esta documentação está prevista para orientação de desenvolvedor e administração, mas nunca deve ser utilizada pelo próprio cliente. Os códigos de erro estão sujeitos a alterações a qualquer momento, a fim de fornecer mensagens de erro mais granulares que se destinam a ajudar o desenvolvedor enquanto constrói a sua aplicação. As aplicações que assumam uma dependência dos números de código de texto ou de erro serão quebradas ao longo do tempo.

## <a name="lookup-current-error-code-information"></a>Ver iphone informações de código de erro atuais
Códigos de erro e mensagens estão sujeitos a alterações.  Para obter as informações mais recentes, dê uma olhada na página [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) para encontrar descrições de erros AADSTS, correções e algumas soluções alternativas sugeridas.  

Procure na parte numérica do código de erro devolvido.  Por exemplo, se recebeu o código de erro "AADSTS16000" então faça uma pesquisa no [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) por "16000".  Também pode ligar diretamente a um erro específico adicionando o número de código de erro ao URL: [https://login.microsoftonline.com/error?code=16000](https://login.microsoftonline.com/error?code=16000).

## <a name="aadsts-error-codes"></a>Códigos de erro AADSTS

| Erro | Descrição |
|---|---|
| AADSTS16000 | SelectUserAccount - Esta é uma interrupção lançada pelo Azure AD, que resulta em UI que permite ao utilizador selecionar entre várias sessões sem soválidas válidas. Este erro é bastante comum e pode ser devolvido à aplicação se for especificado `prompt=none`. |
| AADSTS16001 | UserAccountSelectionInvalid - Verá este erro se o utilizador clicar num azulejo que a lógica de seleção da sessão rejeitou. Quando desencadeado, este erro permite ao utilizador recuperar escolhendo de uma lista atualizada de azulejos/sessões, ou escolhendo outra conta. Este erro pode ocorrer devido a um defeito de código ou condição de corrida. |
| AADSTS16002 | AppSessionSelectionInvalid - O requisito SID especificado por aplicações não foi cumprido.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant - Indica que o utilizador não foi explicitamente adicionado ao inquilino. |
| AADSTS17003 | CredentialKeyProvisionfailed - Azure AD não pode fornecer a chave de utilizador. |
| AADSTS20001 | WsFedSignInResponseError - Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema. |
| AADSTS20012 | WsFedMessageInvalid - Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema. |
| AADSTS20033 | FedMetadataInvalidTenantName - Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema. |
| AADSTS40008 | OAuth2IdPUnretryableServerError - Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - Existe um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema. |
| AADSTS40010 | OAuth2IdPRetryableServerErrorError - Existe um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - Existe um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema. |
| AADSTS50000 | TokenIssuanceError - Há um problema com o serviço de inscrição. [Crie um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para resolver este problema. |
| AADSTS50001 | InvalidResource - O recurso está desativado ou não existe. Verifique o código da sua aplicação para garantir que especificou o URL de recursos exato para o recurso a que está a tentar aceder.  |
| AADSTS50002 | NotAllowedTenant - O registo falhou devido a um acesso restrito ao arrendatário. Se for a sua própria política de inquilinos, pode alterar as suas configurações restritas de inquilino para resolver este problema. |
| AADSTS50003 | MissingSigningKey - O inserição falhou devido a uma chave de assinatura ou certificado em falta. Isto pode acontecer porque não havia nenhuma chave de assinatura configurada na aplicação. Veja as resoluções descritas em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Se ainda vir problemas, contacte o proprietário da aplicação ou um administrador de aplicações. |
| AADSTS50005 | DevicePolicyError - O utilizador tentou iniciar sessão num dispositivo a partir de uma plataforma que não é suportada atualmente através da política de Acesso Condicional. |
| AADSTS50006 | Assinatura inválida - Verificação de assinatura falhou por causa de uma assinatura inválida. |
| AADSTS50007 | PartnerEncryptionCertificateMissing - O certificado de encriptação do parceiro não foi encontrado para esta aplicação. [Abra um bilhete](../fundamentals/active-directory-troubleshooting-support-howto.md) de suporte com a Microsoft para obter isto arranjado. |
| AADSTS50008 | InvalidSamlToken - A afirmação saml está em falta ou mal configurada no token. Contacte o seu fornecedor de federação. |
| AADSTS50010 | AudienceUriValidaçãoFailed - A validação do PÚBLICO URI para a aplicação falhou uma vez que nenhum público simbólico foi configurado. |
| AADSTS50011 | Resposta inválida - O endereço de resposta está em falta, mal configurado ou não corresponde aos endereços de resposta configurados para a aplicação.  Como resolução, certifique-se de adicionar este endereço de resposta em falta ao pedido de Diretório Ativo Azure ou ter alguém com as permissões para gerir a sua aplicação no Ative Directory faça isso por si.|
| AADSTS50012 | AutenticaçãoFalha - A autenticação falhou por uma das seguintes razões:<ul><li>O nome do sujeito do certificado de assinatura não está autorizado</li><li>Uma política de autoridade fidedigna correspondente não foi encontrada para o nome do assunto autorizado</li><li>A cadeia de certificados não é válida</li><li>O certificado de assinatura não é válido</li><li>A política não está configurada no inquilino</li><li>A impressão digital do certificado de assinatura não está autorizada</li><li>Afirmação do cliente contém uma assinatura inválida</li></ul> |
| AADSTS50013 | InvalidA - Afirmação é inválida por várias razões - O emitente simbólico não corresponde à versão api dentro do seu intervalo de tempo válido - expirado - Token refresh na afirmação não é um token de atualização primária. |
| AADSTS50014 | GuestUserInPendingState - O resgate do utilizador encontra-se em estado pendente. A conta de utilizador convidado ainda não está totalmente criada. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState - O utilizador requer o consentimento legal da faixa etária. |
| AADSTS50017 | Validação de CertificadoSFailed - Validação de certificação falhou, razões para as seguintes razões:<ul><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>Não foi possível encontrar o CrlSegment esperado</li><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>O ponto de distribuição de Delta CRL está configurado sem um ponto de distribuição de CRL correspondente</li><li>Incapaz de recuperar segmentos CRL válidos por causa de um problema de timeout</li><li>Não é possível transferir a CRL</li></ul>Contacte o administrador do inquilino. |
| AADSTS50020 | UserUnauthorized - Os utilizadores não estão autorizados a ligar para este ponto final. |
| AADSTS50027 | InvalidJwtToken - Ficha JWT inválida devido às seguintes razões:<ul><li>Não contém a afirmação nonce, subafirmação</li><li>erro de correspondência do identificador de requerente</li><li>afirmação duplicada nas afirmações idToken</li><li>emissor inesperado</li><li>audiência inesperada</li><li>não está dentro do intervalo de tempo válido </li><li>o formato do token não é adequado</li><li>a verificação de assinatura falhou no token de ID externo do emissor</li></ul> |
| AADSTS50029 | URI inválido: o nome de domínio contém carateres inválidos. Contacte o administrador do inquilino. |
| AADSTS50032 | WeakRsaKey - Indica a tentativa errada do utilizador de utilizar uma tecla RSA fraca. |
| AADSTS50033 | Error retryable - Indica um erro transitório não relacionado com as operações da base de dados. |
| AADSTS50034 | UserAccountNotFound - Para iniciar sessão nesta aplicação, a conta deve ser adicionada ao diretório. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt - O sal necessário para gerar um identificador em pares está em falta em princípio. Contacte o administrador do inquilino. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - Incompatibilidades de assunto sujeitantes reivindicação do Emitente na afirmação do cliente. Contacte o administrador do inquilino. |
| AADSTS50049 | NoSuchInstanceForDiscovery - Instância desconhecida ou inválida. |
| AADSTS50050 | MalformadoDiscoveryRequest - O pedido está mal formado. |
| AADSTS50053 | IdsLocked - A conta está bloqueada porque o utilizador tentou iniciar sessão demasiadas vezes com um ID ou senha de utilizador incorreto. |
| AADSTS50055 | Palavra-passe InvalidPasswordExpired - A palavra-passe está caducada. |
| AADSTS50056 | Palavra-passe inválida ou nula -A palavra-passe não existe para este utilizador. |
| AADSTS50057 | UserDisabled - A conta de utilizador está desativada. A conta foi desativada por um administrador. |
| AADSTS50058 | UserInformationNotProvided - Isto significa que um utilizador não está inscrito. Este é um erro comum que é esperado quando um utilizador não é autenticado e ainda não assinou.</br>Se este erro for encorajado num contexto SSO em que o utilizador tenha assinado previamente, isto significa que a sessão SSO não foi encontrada ou inválida.</br>Este erro pode ser devolvido à aplicação se for especificado prompt=nenhum. |
| AADSTS50059 | Informações fornecidas por Faltade InquilinoRealmAndNoUserInformationProvided - As informações de identificação do inquilino não foram encontradas no pedido nem implícitas por quaisquer credenciais fornecidas. O utilizador pode contactar o administrador do inquilino para ajudar a resolver o problema. |
| AADSTS50061 | SignoutInvalidRequest - O pedido de inscrição é inválido. |
| AADSTS50064 | CredencialAuthenticationError - Validação credencial no nome de utilizador ou palavra-passe falhou. |
| AADSTS50068 | SignoutInitiatorNotParticipant - Signout falhou. A aplicação que iniciou a inscrição não é participante na sessão atual. |
| AADSTS50070 | SignoutUnknownSessionIdentifier - Signout falhou. O pedido de sinalização especificou um identificador de nome que não correspondia às sessões existentes. |
| AADSTS50071 | SignoutMessageExpired - O pedido de logout expirou. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - O utilizador precisa de se inscrever para autenticação de segundo fator (interativo). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - É necessária uma autenticação forte e o utilizador não passou no desafio MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - Devido a uma mudança de configuração feita pelo administrador, ou porque se mudou para um novo local, o utilizador deve usar a autenticação de vários fatores para aceder ao recurso. Retry com um novo pedido de autorização para o recurso. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - Devido a uma alteração de configuração feita pelo administrador, ou porque o utilizador se mudou para um novo local, o utilizador é obrigado a usar a autenticação de vários fatores. |
| AADSTS50085 | O token de atualização precisa de início de sessão de IDP social. Pedir ao utilizador que repita o início de sessão com nome de utilizador e a palavra-passe |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError - O serviço está temporariamente indisponível. Tente novamente. |
| AADSTS50089 | O fluxo de token expirou - falha na autenticação. Peça ao utilizador que tente fazer o sessão novamente com o nome de utilizador -password. |
| AADSTS50097 | Autenticação do DispositivoNecessária - É necessária a autenticação do dispositivo. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized - A assinatura JWT é inválida. |
| AADSTS50105 | EntitlementGrantsNotFound - O utilizador assinado não é atribuído a uma função para o assinado na aplicação. Designar o utilizador para a aplicação. Para mais informações:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri - O objeto de reino da federação solicitado não existe. Contacte o administrador do inquilino. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - Emita com cabeçalho JWT. Contacte o administrador do inquilino. |
| AADSTS50124 | Transformação InvalidInvalidInputParameter - Transformação de Sinistros contém parâmetro de entrada inválido. Contacte o administrador de inquilinos para atualizar a política. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - O registo foi interrompido devido a um reset de palavra-passe ou à entrada de registo de passwords. |
| AADSTS50126 | InvalidUserNameOrPassword - Error validando credenciais devido a nome de utilizador ou palavra-passe inválidos. |
| AADSTS50127 | BrokerAppNotInstalado - O utilizador precisa de instalar uma aplicação de corretor para ter acesso a este conteúdo. |
| AADSTS50128 | Nome de domínio inválido - Nenhuma informação de identificação do inquilino encontrada no pedido ou implícita por quaisquer credenciais fornecidas. |
| AADSTS50129 | DispositivoIsNotWorkplaceJoined - A adesão ao local de trabalho é necessária para registar o dispositivo. |
| AADSTS50131 | ConditionalAccessFailed - Indica vários erros de Acesso Condicional, tais como mau estado do dispositivo Windows, pedido bloqueado devido a atividade suspeita, política de acesso ou decisões de política de segurança. |
| AADSTS50132 | SsoArtifactInvalidOrExpired - A sessão não é válida devido à expiração da palavra-passe ou à recente alteração da palavra-passe. |
| AADSTS50133 | SsoArtifactRevogad - A sessão não é válida devido à expiração da palavra-passe ou à recente alteração da palavra-passe. |
| AADSTS50134 | DeviceFlowAuthorizeWrongWrongDatacenter - Centro de dados errado. Para autorizar um pedido que foi iniciado por uma aplicação no fluxo de dispositivos OAuth 2.0, a parte autorizada deve estar no mesmo centro de dados onde o pedido original reside. |
| AADSTS50135 | PasswordChangeCompromisedPassword - A alteração da palavra-passe é necessária devido ao risco de conta. |
| AADSTS50136 | RedirecionamentoSSessionToApp - Sessão Única de MSA detetada. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken - A sessão é inválida devido a um token externo de atualização em falta. |
| AADSTS50140 | KmsiInterrupt - Este erro ocorreu devido à interrupção de "Mantenha-me inscrito" quando o utilizador estava a iniciar sessão. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o ID de Correlação, o ID do Pedido e o código de Erro para obter mais detalhes. |
| AADSTS50143 | Sessão de sino - A sessão é inválida porque o inquilino utilizador não corresponde à sugestão de domínio devido a diferentes recursos.  [Abra um bilhete](../fundamentals/active-directory-troubleshooting-support-howto.md) de suporte com Id correlation, Id de Pedido e código de erro para obter mais detalhes. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - A palavra-passe do Diretor Ativo do utilizador expirou. Gere uma nova palavra-passe para o utilizador ou pede ao utilizador que utilize a ferramenta de reset de autosserviço para redefinir a sua palavra-passe. |
| AADSTS50146 | MissingCustomSigningKey - Esta aplicação é necessária para ser configurada com uma chave de assinatura específica da aplicação. Ou não está configurada com uma chave destas ou a chave expirou ou ainda não é válida. |
| AADSTS50147 | MissingCodeChallenge - O tamanho do parâmetro de desafio do código não é válido. |
| AADSTS50155 | Autenticação do DispositivoFalha - A autenticação do dispositivo falhou para este utilizador. |
| AADSTS50158 | ExternalSecurityChallenge - Desafio de segurança externa não foi satisfeito. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - As reclamações enviadas por fornecedor externo não são suficientes ou a reclamação em falta solicitada ao fornecedor externo. |
| AADSTS50166 | ExternalClaimsProviderThrottled - Não enviou o pedido ao prestador de sinistros. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - O cliente é capaz de obter um token SSO através da extensão de Contas Windows 10, mas o token não foi encontrado no pedido ou o token fornecido expirou. |
| AADSTS50169 | InvalidRequestBadRealm - O reino não é um reino configurado do atual espaço de nome de serviço. |
| AADSTS50170 | FaltaexternalExternalClaimsProviderMapping - O mapeamento de controlos externos está em falta. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - O desafio externo não é suportado para os utilizadores passthrough. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers - O controlo de sessão não é suportado para utilizadores passthrough. |
| AADSTS50180 | WindowsIntegratedAuthMissing - É necessária a autenticação integrada do Windows. Ativar o inquilino para SSO Totalmente Integrado. |
| AADSTS50187 | Informações de dispositivoNãoFornecidos - O serviço falhou na autenticação do dispositivo. |
| AADSTS50196 | LoopDetected - Foi detetado um ciclo de cliente. Verifique a lógica da aplicação para garantir que o cache token é implementado e que as condições de erro são manuseadas corretamente.  A aplicação fez demasiados pedidos em muito curto prazo, indicando que se encontra em estado defeituoso ou que está a solicitar abusivamente fichas. |
| AADSTS50199 | CmsiInterrupt - Por razões de segurança, a confirmação do utilizador é necessária para este pedido.  Como este é um erro "interaction_required", o cliente deve fazer auth interativo.  Isto ocorre porque um webview do sistema foi usado para solicitar um símbolo para uma aplicação nativa - o utilizador deve ser solicitado a perguntar se esta era realmente a app que pretendia assinar.|
| AADSTS51000 | RequeridoFeatureNotEnabled - A funcionalidade está desativada. |
| AADSTS51001 | DomainHintMustbePresent - A sugestão de domínio deve estar presente com o identificador de segurança no local ou no local UPN. |
| AADSTS51004 | UserAccountNotInDirectory - A conta de utilizador não existe no diretório. |
| AADSTS51005 | TemporaryRedirect - Equivalente ao estatuto HTTP 307, o que indica que as informações solicitadas estão localizadas no URI especificado no cabeçalho de localização. Quando receber este estatuto, siga o cabeçalho de localização associado à resposta. Quando o método de pedido original foi POST, o pedido redirecionado também utilizará o método POST. |
| AADSTS51006 | ForceReauthDueToInOuth - É necessária a autenticação integrada do Windows. O utilizador registou um token de sessão que está a faltar à reivindicação de autenticação integrada do Windows. Peça ao utilizador que faça o login novamente. |
| AADSTS52004 | DelegaçãoNãoExistForLinkedIn - O utilizador não autorizou o acesso aos recursos do LinkedIn. |
| AADSTS53000 | DispositivoNotCompliant - A política de acesso condicional requer um dispositivo compatível e o dispositivo não está em conformidade. O utilizador deve inscrever o seu dispositivo com um fornecedor de MDM aprovado como o Intune. |
| AADSTS53001 | DispositivoNotDomainJoined - A política de acesso condicional requer um dispositivo de domínio unido, e o dispositivo não é unido ao domínio. Mande o utilizador utilizar um dispositivo de domínio unido. |
| AADSTS53002 | AplicaçõesUsedIsNotAnApprovedApp - A aplicação utilizada não é uma aplicação aprovada para acesso condicional. O utilizador precisa de utilizar uma das aplicações da lista de aplicações aprovadas para ter acesso. |
| AADSTS53003 | BlockedByConditionalAccess - O acesso foi bloqueado pelas políticas de Acesso Condicional. A política de acesso não permite a emissão simbólica. |
| AADSTS53004 | ProofUpBlockedDueToRisk - O utilizador precisa de completar o processo de registo de autenticação de vários fatores antes de aceder a este conteúdo. O utilizador deve registar-se na autenticação multifator. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegaçãoNãoExist - O utilizador ou administrador não consentiu em utilizar a aplicação com o ID X. Envie um pedido de autorização interativa para este utilizador e recurso. |
| AADSTS65004 | UserDeclindConsent - O utilizador recusou-se a consentir em aceder à aplicação. Pedir ao utilizador que repita o início de sessão e que autorize o acesso à aplicação|
| AADSTS65005 | Aplicação mal configurada - A lista de acesso a recursos exigida não contém aplicações detetáveis pelo recurso ou a aplicação do cliente solicitou acesso a recursos, o que não foi especificado na sua lista de acesso a recursos exigido ou serviço gráfico devolvido mal pedido ou recurso não encontrado. Se a aplicação suportar o SAML, pode ter configurado a aplicação com o identificador errado (Entidade). Experimente a resolução indicada para SAML através da ligação abaixo: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - A autenticação falhou. O token de refresco não é válido. O erro pode dever-se às seguintes razões:<ul><li>Cabeçalho de encadernação de token está vazio</li><li>Token binding hash não corresponde</li></ul> |
| AADSTS70001 | Cliente não autorizado - A aplicação está desativada. |
| AADSTS70002 | InvalidClient - Erro validando as credenciais. O client_secret especificado não corresponde ao valor esperado para este cliente. Corrija o client_secret e tente de novo. Para mais informações, consulte [Utilize o código de autorização para solicitar um sinal de acesso](v2-oauth2-auth-code-flow.md#request-an-access-token). |
| AADSTS70003 | GrantType não suportado - A aplicação devolveu um tipo de subvenção não suportada. |
| AADSTS70004 | InvalidRedirectUri - A aplicação devolveu um URI redirecionado inválido. O endereço de redirecionamento especificado pelo cliente não corresponde a nenhum dos endereços configurados ou a nenhum dos endereços na lista de aprovações OIDC. |
| AADSTS70005 | Resposta Não suportadaType - A aplicação devolveu um tipo de resposta não suportado devido às seguintes razões:<ul><li>o tipo de resposta 'token' não está ativado para a aplicação</li><li>o tipo de resposta “id_token” requer o âmbito “OpenID”; contém um valor de parâmetro de OAuth não suportado no wctx codificado</li></ul> |
| AADSTS70007 | Não suportadoResponseMode - A aplicação devolveu um valor não suportado de `response_mode` ao solicitar um símbolo.  |
| AADSTS70008 | ExpiradOrRevokedGrant - O token de atualização expirou devido à inatividade. O símbolo foi emitido em XXX e esteve inativo por um certo período de tempo. |
| AADSTS70011 | InvalidScope - O âmbito solicitado pela aplicação é inválido. |
| AADSTS70012 | MsaServerError - Ocorreu um erro do servidor ao autenticar um utilizador MSA (consumidor). Tente novamente. Se continuar a falhar, [abra um bilhete](../fundamentals/active-directory-troubleshooting-support-howto.md) de apoio |
| AADSTS70016 | Autorizações Gastos - Erro de fluxo do dispositivo OAuth 2.0. A autorização está pendente. O dispositivo irá voltar a tentar fazer sondagens ao pedido. |
| AADSTS70018 | BadVerificationCode - Código de verificação inválido devido à digitação do utilizador no código de utilizador errado para o fluxo de código do dispositivo. A autorização não está aprovada. |
| AADSTS70019 | CodeExpired - Código de verificação expirado. Peça ao utilizador que tente novamente o sessão. |
| AADSTS75001 | BindingSerializationError - Ocorreu um erro durante a ligação de mensagens SAML. |
| AADSTS75003 | Erro de Encadernação Não Suportado - A aplicação devolveu um erro relacionado com a encadernação não suportada (a resposta do protocolo SAML não pode ser enviada através de encadernações que não http POST). |
| AADSTS75005 | Saml2MessageInvalid - Azure AD não suporta o pedido SAML enviado pela aplicação para SSO. |
| AADSTS75008 | RequestDeniedError - O pedido da app foi negado uma vez que o pedido da SAML tinha um destino inesperado. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - O método de autenticação pelo qual o utilizador autenticado com o serviço não corresponde ao método de autenticação solicitado. |
| AADSTS75016 | Saml2AutenticaçãoRequestInvalidNameIDPolicy - Pedido de Autenticação SAML2 tem Inválido NameIdPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotDisponível - O Agente de Autenticação não pode ligar-se ao Diretório Ativo. Certifique-se de que os servidores de agentes são membros da mesma floresta AD que os utilizadores cujas palavras-passe precisam de ser validadas e que são capazes de se conectar ao Ative Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout - Pedido de validação de palavra-passe cronometrado. Certifique-se de que o Diretório Ativo está disponível e responde aos pedidos dos agentes. |
| AADSTS80005 | OnPremisePasswordValidatorImprevisívelWebException - Ocorreu um erro desconhecido durante o processamento da resposta do Agente de Autenticação. Tente o pedido. Se continuar a falhar, [abra um bilhete](../fundamentals/active-directory-troubleshooting-support-howto.md) de apoio para obter mais detalhes sobre o erro. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - O Agente de Autenticação não consegue validar a palavra-passe do utilizador. Verifique os registos do agente para obter mais informações e verifique se o Diretório Ativo está a funcionar como esperado. |
| AADSTS80010 | OnPremisePasswordValidaçãoValidaçãoException - O Agente de Autenticação não consegue desencriptar a palavra-passe. |
| AADSTS80012 | OnPremisePasswordValidaÇãoAccountLogonInvalidHours - Os utilizadores tentaram iniciar sessão fora das horas permitidas (isto é especificado em AD). |
| AADSTS80013 | OnPremisePasswordValidaTimeSkew - A tentativa de autenticação não pôde ser concluída devido ao desvio de tempo entre a máquina que executa o agente de autenticação e a AD. Corrija problemas de sincronização de tempo. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Tentativa de autenticação Kerberos falhou. |
| AADSTS81005 | DesktopSsoAutenticaçãoPacoteNotSupported - O pacote de autenticação não é suportado. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - Não foi encontrado um cabeçalho de autorização. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - O inquilino não está ativado para SSO Sem Emenda. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - Incapaz de validar o bilhete Kerberos do utilizador. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - Seamless SSO falhou porque o bilhete Kerberos do utilizador expirou ou é inválido. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - Incapaz de encontrar objeto de utilizador com base em informações no bilhete Kerberos do utilizador. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - O utilizador que tenta iniciar sessão no Azure AD é diferente do utilizador assinado no dispositivo. |
| AADSTS90002 | InvalidTenantName - O nome do inquilino não foi encontrado na loja de dados. Verifique se tem a identificação correta do inquilino. |
| AADSTS90004 | InvalidRequestFormat - O pedido não está devidamente formatado. |
| AADSTS90005 | Pedido inválido com requisitos múltiplos - Incapaz de completar o pedido. O pedido não é válido porque o identificador e a dica de login não podem ser usados juntos.  |
| AADSTS90006 | ExternalServerRetryableError - O serviço está temporariamente indisponível.|
| AADSTS90007 | InvalidSessionId - Mau pedido. A identificação da sessão aprovada não pode ser analisada. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission - O utilizador ou administrador não consentiu em utilizar a aplicação. No mínimo, a aplicação requer acesso à AD Azure especificando a autorização de entrada e leitura do perfil do utilizador. |
| AADSTS90009 | TokenForItselfMissingSameAppIdentifier - A aplicação está a solicitar um sinal próprio para si. Este cenário só é suportado se o recurso especificado estiver a utilizar o ID de aplicação baseado em GUID. |
| AADSTS90010 | NotSupported - Incapaz de criar o algoritmo. |
| AADSTS90012 | RequestTimeout - O pedido tem esgotado. |
| AADSTS90013 | InvalidUserInput - A entrada do utilizador não é válida. |
| AADSTS90014 | MissingRequiredField - Este código de erro pode aparecer em vários casos quando um campo esperado não está presente na credencial. |
| AADSTS90015 | ConsultaStringTooLong - A corda de consulta é muito longa. |
| AADSTS90016 | MissingRequiredClaim - O token de acesso não é válido. A reivindicação necessária está desaparecida. |
| AADSTS90019 | MissingTenantRealm - A AD Azure não conseguiu determinar o identificador de inquilino do pedido. |
| AADSTS90022 | AutenticadoInvalidAMainNameFormat - O formato de nome principal não é válido, ou não cumpre o formato `name[/host][@realm]` esperado. O nome principal é necessário, o hospedeiro e o reino são opcionais e podem ser definidos como nulos. |
| AADSTS90023 | Pedido inválido - O pedido de serviço de autenticação não é válido. |
| AADSTS9002313 | Pedido inválido - Pedido é mal formado ou inválido. - (EN) A questão aqui é porque havia algo de errado com o pedido a um certo ponto final. A sugestão para esta questão é obter um traço de violino do erro que ocorre e procurar ver se o pedido está realmente devidamente formatado ou não. |
| AADSTS90024 | RequestBudgetExceededError - Ocorreu um erro transitório. Tente novamente. |
| AADSTS90033 | MsodsServiceIn - O Serviço de Diretórios Online da Microsoft (MSODS) não está disponível. |
| AADSTS90036 | MsodsServiceUnretryableFailure - Ocorreu um erro inesperado e não retível do serviço WCF hospedado pelo MSODS. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro. |
| AADSTS90038 | NationalCloudTenantRedirection - O inquilino especificado 'Y' pertence à Nuvem Nacional 'X'. A atual instância de nuvem 'Z' não federa com X. Um erro de redirecionamento de nuvem é devolvido. |
| AADSTS90043 | NationalCloudAuthCodeRedirection - A funcionalidade está desativada. |
| AADSTS90051 | InvalidNationalCloudId - O identificador nacional de nuvem contém um identificador de nuvem inválido. |
| AADSTS90055 | TenantThrottlingError - Há muitos pedidos de entrada. Esta exceção é lançada para inquilinos bloqueados. |
| AADSTS90056 | BadResourceRequest - Para resgatar o código para um token de acesso, a aplicação deve enviar um pedido POST para o `/token` ponto final. Além disso, antes disso, deve fornecer um código de autorização e enviá-lo no pedido do POST para o `/token` ponto final. Consulte este artigo para uma visão geral do fluxo de código de autorização OAuth 2.0: [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). Direcione o utilizador para o ponto final `/authorize`, que devolverá uma authorization_code. Ao publicar um pedido para o `/token` ponto final, o utilizador obtém o sinal de acesso. Inicie sessão no portal Azure e verifique as inscrições da **App > Endpoints** para confirmar que os dois pontos finais foram configurados corretamente. |
| AADSTS90072 | PassThroughUserMfaError - A conta externa com a qual o utilizador faz o sinal não existe no inquilino em que se inscreveu; para que o utilizador não possa satisfazer os requisitos de MFA para o inquilino. A conta deve ser adicionada como utilizador externo no inquilino primeiro. Inscreva-se e inscreva-se numa conta de utilizador azure AD diferente. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid - Ocorreu um erro quando o serviço tentou processar uma mensagem wS-Federação. A mensagem não é válida. |
| AADSTS90082 | OrgIdWsFederationNotSupported - A política de autenticação selecionada para o pedido não é suportada atualmente. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed - As contas de hóspedes não são permitidas para este site. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed - O serviço não pode emitir um token porque o objeto da empresa ainda não foi provisionado. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired - O token da DA do utilizador expirou. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed - Ocorreu um erro ao criar a mensagem WS-Federation a partir do URI. |
| AADSTS90090 | GraphRetryableError - O serviço está temporariamente indisponível. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - Gráfico devolvido com um código de erro proibido para o pedido. |
| AADSTS90094 | AdminConsentRequired - O consentimento do administrador é necessário. |
| AADSTS900382 | O Cliente Confidencial não é suportado no pedido da Cross Cloud. |
| AADSTS90100 | InvalidRequestParameter - O parâmetro está vazio ou não válido. |
| AADSTS901002 | AADSTS901002: O parâmetro de pedido de "recurso" não é suportado. |
| AADSTS90101 | InvalidEmailAddress - Os dados fornecidos não são um endereço de e-mail válido. O endereço de e-mail deve estar no formato `someone@example.com`. |
| AADSTS90102 | InvalidUriParameter - O valor deve ser um URI absoluto válido. |
| AADSTS90107 | InvalidXml - O pedido não é válido. Certifique-se de que os seus dados não têm caracteres inválidos.|
| AADSTS90114 | InvalidExpiryDate - A marca ção a granel fará com que seja emitida uma ficha caducada. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode - O código de utilizador é nulo ou vazio.|
| AADSTS90120 | InvalidDeviceFlowRequest - O pedido já estava autorizado ou recusado. |
| AADSTS90121 | Pedido vazio inválido - Pedido vazio inválido.|
| AADSTS90123 | IdentityProviderAccessNegado - O token não pode ser emitido porque o fornecedor de identidade ou reclamação negou o pedido. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported - O recurso não é suportado sobre os pontos finais `/common` ou `/consumers`. Utilize o ponto final `/organizations` ou específico do inquilino. |
| AADSTS90125 | DebugModeEnrollTenantNotFound - O utilizador não está no sistema. Certifique-se de que introduziu corretamente o nome do utilizador. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred - O tipo de utilizador não é suportado neste ponto final. O sistema não pode inferir o inquilino do utilizador a partir do nome de utilizador. |
| AADSTS90130 | Não ConvergedAppV2GlobalEndpointNotSupported - A aplicação não é suportada sobre os pontos finais `/common` ou `/consumers`. Utilize o ponto final `/organizations` ou específico do inquilino. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated - Ocorreu um erro não retável.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordNão cumprea política |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound - O diretor do utilizador não tem a chave DEID NGC configurada. |
| AADSTS130005 | NgcInvalidSignature - assinatura de chave NGC verificada falhou.|
| AADSTS130006 | NgcTransportKeyNotFound - A chave de transporte NGC não está configurada no dispositivo. |
| AADSTS130007 | NgcDeviceIsDisabled - O dispositivo está desativado. |
| AADSTS130008 | NgcDeviceIsNotFound - O dispositivo referenciado pela chave NGC não foi encontrado. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce - Pedido nonce não é fornecido. |
| AADSTS140001 | InvalidSessionKey - A chave da sessão não é válida.|
| AADSTS165900 | InvalidApiRequest - Pedido inválido. |
| AADSTS220450 | AndroidWebViewVersion não suportado - A versão Chrome WebView não é suportada. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DispositivoOnlyTokensNotSupportedByResource - O recurso não está configurado para aceitar fichas apenas para dispositivos. |
| AADSTS240001 | BulkAADJTokenUnauthorized - O utilizador não está autorizado a registar dispositivos em Azure AD. |
| AADSTS240002 | Reclamações Não São Exigidas - O id_token não pode ser usado como subsídio de `urn:ietf:params:oauth:grant-type:jwt-bearer`.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy - O administrador do inquilino configurou uma política de segurança que bloqueia este pedido. Verifique as políticas de segurança definidas no nível do inquilino para determinar se o seu pedido satisfaz os requisitos de política. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest - O pedido não foi encontrado no diretório/inquilino. Isto pode acontecer se o pedido não tiver sido instalado pelo administrador do inquilino ou consentir com qualquer utilizador do arrendatário. Pode ter configurado mal o valor do identificador para o pedido ou enviado o seu pedido de autenticação ao inquilino errado. |
| AADSTS700020 | InteracçãoRequired - A bolsa de acesso requer interação. |
| AADSTS700022 | InvalidMultipleResourcesScope - O valor fornecido para o intervalo do parâmetro de entrada não é válido porque contém mais de um recurso. |
| AADSTS700023 | InvalidResourcelessScope - O valor fornecido para o intervalo do parâmetro de entrada não é válido quando se pede um token de acesso. |
| AADSTS1000000 | UserNotBoundError - O Bind API exige que o utilizador da AD Azure também autentique com um IDP externo, o que ainda não aconteceu. |
| AADSTS1000002 | BindCompleteInterruptError - A ligação concluída com sucesso, mas o utilizador deve ser informado. |
| AADStS7000112 | Aplicação de Cliento Não AutorizadoDisabled - A aplicação está desativada. |

## <a name="next-steps"></a>Passos Seguintes

* Tem uma pergunta ou não consegue encontrar o que procura? Crie um problema gitHub ou veja [o Suporte e ajude as opções para os desenvolvedores](active-directory-develop-help-support.md) aprenderem sobre outras formas de obter ajuda e suporte.
