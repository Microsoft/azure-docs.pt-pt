---
title: Autenticação AD AD & códigos de erro de autorização
description: Saiba mais sobre os códigos de erro AADSTS que são devolvidos do serviço de fichas de segurança Azure AD (STS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 59ed397f3bd54a324f8fa375a3ea870860c31142
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410934"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Códigos de erro da Autenticação e autorização do Azure AD

Procurando informações sobre os códigos de erro AADSTS que são devolvidos do serviço de fichas de segurança Azure Ative (Azure AD) (STS)? Leia este documento para encontrar descrições de erros, correções e algumas soluções de solução sugeridas.

> [!NOTE]
> Estas informações são preliminares e estão sujeitas a alterações. Tem uma pergunta ou não consegue encontrar o que procura? Crie um problema do GitHub ou veja [opções de Suporte e ajuda para que os desenvolvedores](./developer-support-help-options.md) aprendam sobre outras formas de obter ajuda e suporte.
>
> Esta documentação é fornecida para orientação de desenvolvedores e administradores, mas nunca deve ser usada pelo próprio cliente. Os códigos de erro estão sujeitos a alterações a qualquer momento, a fim de fornecer mais mensagens de erro granulares que se destinam a ajudar o desenvolvedor enquanto constrói a sua aplicação. As aplicações que assumirem uma dependência dos números de código de texto ou de erro serão quebradas ao longo do tempo.

## <a name="handling-error-codes-in-your-application"></a>Manusear códigos de erro na sua aplicação

[A especificação OAuth2.0](https://tools.ietf.org/html/rfc6749#section-5.2) fornece orientações sobre como lidar com erros durante a autenticação utilizando a `error` parte da resposta de erro. 

Aqui está uma resposta de erro de amostra:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://example.contoso.com/activity.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7", 
  "error_uri":"https://login.microsoftonline.com/error?code=70011"
}
```

| Parâmetro         | Descrição    |
|-------------------|----------------|
| `error`       | Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem e deve ser usado para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. Nunca utilize este campo para reagir a um erro no seu código. |
| `error_codes` | Uma lista de códigos de erro específicos do STS que podem ajudar no diagnóstico.  |
| `timestamp`   | A hora em que ocorreu o erro. |
| `trace_id`    | Um identificador único para o pedido que pode ajudar no diagnóstico. |
| `correlation_id` | Um identificador único para o pedido que pode ajudar no diagnóstico em todos os componentes. |
| `error_uri` |  Um link para a página de procura de erro com informações adicionais sobre o erro.  Isto é apenas para uso do desenvolvedor, não o apresente aos utilizadores.  Apenas presente quando o sistema de procura de erros tiver informações adicionais sobre o erro - nem todos os erros têm informações adicionais fornecidas.|

O `error` campo tem vários valores possíveis - reveja as ligações de documentação do protocolo e as especificações de OAuth 2.0 para saber mais sobre erros específicos (por exemplo, `authorization_pending` no fluxo de código do [dispositivo](v2-oauth2-device-code.md)) e como reagir a eles.  Alguns comuns estão listados aqui:

| Código de Erro         | Description        | Ação do Cliente    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Erro de protocolo, como um parâmetro exigido em falta. | Corrija e reenvia o pedido.|
| `invalid_grant`    | Parte do material de autenticação (código auth, token de atualização, token de acesso, desafio PKCE) era inválido, inparável, em falta ou de outra forma inutilizável | Tente um novo pedido para o `/authorize` ponto final para obter um novo código de autorização.  Considere rever e validar o uso da aplicação nos protocolos. |
| `unauthorized_client` | O cliente autenticado não está autorizado a usar este tipo de concessão de autorização. | Isto ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino AZure AD do utilizador. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| `invalid_client` | A autenticação do cliente falhou.  | As credenciais do cliente não são válidas. Para corrigir, o administrador de aplicação atualiza as credenciais.   |
| `unsupported_grant_type` | O servidor de autorização não suporta o tipo de concessão de autorização. | Altere o tipo de subvenção no pedido. Este tipo de erro só deve ocorrer durante o desenvolvimento e ser detetado durante os testes iniciais. |
| `invalid_resource` | O recurso alvo é inválido porque não existe, a Azure AD não pode encontrá-lo, ou não está corretamente configurado. | Isto indica que o recurso, se existir, não foi configurado no arrendatário. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD.  Durante o desenvolvimento, isto geralmente indica um inquilino de teste de configuração incorreta ou um erro no nome do âmbito solicitado. |
| `interaction_required` | O pedido requer interação do utilizador. Por exemplo, é necessário um passo adicional de autenticação. | Reda o pedido com o mesmo recurso, interativamente, para que o utilizador possa completar todos os desafios necessários.  |
| `temporarily_unavailable` | O servidor está temporariamente demasiado ocupado para lidar com o pedido. | Recandidutar o pedido. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a uma condição temporária. |

## <a name="lookup-current-error-code-information"></a>Procurar informações de código de erro corrente
Os códigos de erro e as mensagens estão sujeitos a alterações.  Para obter as informações mais recentes, veja a [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) página para encontrar descrições de erros, correções e algumas soluções sugeridas.  

Por exemplo, se recebeu o código de erro "AADSTS50058" então faça uma pesquisa [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) em "50058".  Também pode ligar diretamente a um erro específico adicionando o número de código de erro ao URL: [https://login.microsoftonline.com/error?code=50058](https://login.microsoftonline.com/error?code=50058) .

## <a name="aadsts-error-codes"></a>Códigos de erro AADSTS

| Erro | Descrição |
|---|---|
| AADSTS16000 | SelectUserAccount - Esta é uma interrupção lançada pelo Azure AD, que resulta em UI que permite ao utilizador selecionar entre várias sessões SSO válidas. Este erro é bastante comum e pode ser devolvido à aplicação se `prompt=none` for especificado. |
| AADSTS16001 | UserAccountSelectionInvalid - Verá este erro se o utilizador clicar num azulejo que a lógica de seleção de sessão rejeitou. Quando desencadeado, este erro permite ao utilizador recuperar através da recolha de uma lista atualizada de azulejos/sessões ou escolhendo outra conta. Este erro pode ocorrer devido a um defeito de código ou condição de raça. |
| AADSTS16002 | AppSessionSelectionInvalid - O requisito SID especificado na aplicação não foi cumprido.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant - Indica que o utilizador não foi explicitamente adicionado ao inquilino. |
| AADSTS17003 | CredencialKeyProvisioningFailed - Azure AD não pode providenciar a chave do utilizador. |
| AADSTS20001 | WsFedSignInResponseError - Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema. |
| AADSTS20012 | WsFedMessageInvalid - Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema. |
| AADSTS20033 | FedMetadataInvalidTenantName - Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema. |
| AADSTS40008 | OAuth2IdPUnretryableServerError - Há um problema com o seu Fornecedor de Identidade Federado. Contacte o seu IDP para resolver este problema. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema. |
| AADSTS40010 | OAuth2IdPRetryableServerError - Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema. |
| AADSTS50000 | TokenIssuanceError - Há um problema com o serviço de inscrição. [Crie um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para resolver este problema. |
| AADSTS50001 | InvalidResource - O recurso é desativado ou não existe. Verifique o código da sua aplicação para garantir que especificou o URL de recurso exato para o recurso a que está a tentar aceder.  |
| AADSTS50002 | NotAllowedTenant - A inscrição falhou devido a um acesso restrito por procuração ao inquilino. Se for a sua própria política de inquilino, pode alterar as definições de restrição do inquilino para corrigir este problema. |
| AADSTS50003 | MissingSigningKey - O sessão falhou devido à falta de uma chave ou certificado de assinatura. Isto pode ser porque não havia nenhuma chave de assinatura configurada na app. Confira as resoluções delineadas em [.. /gestão de apps/aplicação-sign-in-problem-federated-sso-gallery.md#certificate-or-key-not-configurado](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#certificate-or-key-not-configured). Se ainda vir problemas, contacte o proprietário da aplicação ou um administrador de aplicações. |
| AADSTS50005 | DevicePolicyError - O utilizador tentou iniciar sessão num dispositivo a partir de uma plataforma que não é suportada através da política de Acesso Condicional. |
| AADSTS50006 | InvalidSignature - A verificação de assinatura falhou devido a uma assinatura inválida. |
| AADSTS50007 | PartnerEncryptionCertificateMissing - O certificado de encriptação do parceiro não foi encontrado para esta aplicação. [Abra um bilhete de apoio](../fundamentals/active-directory-troubleshooting-support-howto.md) com a Microsoft para que isto seja corrigido. |
| AADSTS50008 | InvalidSamlToken - Afirmação SAML está em falta ou mal configurada no token. Contacte o seu fornecedor de federação. |
| AADSTS50010 | AudienceUriValidationFailed - Validação URI do público para a aplicação falhou uma vez que nenhum público simbólico foi configurado. |
| AADSTS50011 | InvalidReplyTo - Falta o endereço de resposta, configurado de forma errada ou não corresponde aos endereços de resposta configurados para a app.  Como resolução, certifique-se de adicionar este endereço de resposta em falta ao pedido do Diretório Ativo Azure ou que alguém com as permissões para gerir a sua aplicação no Ative Directory o faça por si.|
| AADSTS50012 | AutenticaçãoFaida - A autenticação falhou por uma das seguintes razões:<ul><li>O nome do sujeito do certificado de assinatura não está autorizado</li><li>Não foi encontrada uma política de autoridade de confiança correspondente para o nome do sujeito autorizado.</li><li>A cadeia de certificados não é válida</li><li>O certificado de assinatura não é válido</li><li>A política não está configurada no inquilino.</li><li>A impressão digital do certificado de assinatura não é autorizada</li><li>A afirmação do cliente contém uma assinatura inválida</li></ul> |
| AADSTS50013 | InvalidAssertion - A afirmação é inválida por várias razões - O emitente de token não corresponde à versão api dentro do seu intervalo de tempo válido - expirado -mal formado - Refresh token na afirmação não é um token de atualização primária. |
| AADSTS50014 | GuestUserInPendingState - A redenção do utilizador encontra-se num estado pendente. A conta de utilizador do hóspede ainda não foi totalmente criada. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState - O utilizador requer o consentimento legal do grupo etário. |
| AADSTS50017 | CertificadoSValidationFailed - Validação de certificação falhou, razões pelas seguintes razões:<ul><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>Não foi possível encontrar o CrlSegment esperado</li><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>O ponto de distribuição de Delta CRL está configurado sem um ponto de distribuição de CRL correspondente</li><li>Não é possível recuperar segmentos de CRL válidos por causa de um problema de tempo limite</li><li>Não é possível transferir a CRL</li></ul>Contacte o administrador do inquilino. |
| AADSTS50020 | UserUnautized - Os utilizadores não estão autorizados a ligar para este ponto final. |
| AADSTS50027 | InvalidJwtToken - Token JWT inválido devido às seguintes razões:<ul><li>Não contém a afirmação nonce, subafirmação</li><li>erro de correspondência do identificador de requerente</li><li>afirmação duplicada nas afirmações idToken</li><li>emissor inesperado</li><li>audiência inesperada</li><li>não está dentro do intervalo de tempo válido </li><li>o formato do token não é adequado</li><li>a verificação de assinatura falhou no token de ID externo do emissor</li></ul> |
| AADSTS50029 | URI inválido: o nome de domínio contém carateres inválidos. Contacte o administrador do inquilino. |
| AADSTS50032 | WeakRsaKey - Indica a tentativa errada do utilizador de utilizar uma chave RSA fraca. |
| AADSTS50033 | RetripableError - Indica um erro transitório não relacionado com as operações da base de dados. |
| AADSTS50034 | UserAccountNotFound - Para iniciar sação nesta aplicação, a conta deve ser adicionada ao diretório. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt - O sal necessário para gerar um identificador de pares está em falta em princípio. Contacte o administrador do inquilino. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - Assunto desajuste da reclamação do emitente na afirmação do cliente. Contacte o administrador do inquilino. |
| AADSTS50049 | NoSuchInstanceForDiscovery - Caso desconhecido ou inválido. |
| AADSTS50050 | MalformedDiscoveryRequest - O pedido está mal formado. |
| AADSTS50053 | IdsLock - A conta está bloqueada porque o utilizador tentou fazer sedudas demasiadas vezes com um ID ou senha de utilizador incorreto. |
| AADSTS5005 | InvalidPasswordExpiredPassword - A palavra-passe está expirada. |
| AADSTS50056 | Palavra-passe inválida ou nula -A palavra-passe não existe na loja para este utilizador. |
| AADSTS50057 | UserDisabled - A conta de utilizador está desativada. A conta foi desativada por um administrador. |
| AADSTS50058 | UserInformationNotProvided - Isto significa que um utilizador não está inscrito. Este é um erro comum que se espera quando um utilizador é não autenticado e ainda não assinou.</br>Se este erro for encorajado num contexto SSO em que o utilizador já assinou, isto significa que a sessão SSO não foi considerada ou inválida.</br>Este erro pode ser devolvido à aplicação se não for especificado o pedido. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - Informações de identificação de inquilinos não foram encontradas no pedido ou implícitas por quaisquer credenciais fornecidas. O utilizador pode contactar o administrador do arrendatário para ajudar a resolver o problema. |
| AADSTS50061 | SignoutInvalidRequest - O pedido de inscrição é inválido. |
| AADSTS50064 | CredencialAuthenticationError - A validação credencial no nome de utilizador ou palavra-passe falhou. |
| AADSTS50068 | SignoutInitiatorNotParticipant - A assinatura falhou. A aplicação que iniciou o signo não é um participante na sessão atual. |
| AADSTS50070 | SignoutUnknownSessionIdentifier - Sign out falhou. O pedido de sinalização especificava um identificador de nome que não correspondia à sessão(s) existente. |
| AADSTS50071 | SignoutMessageExpired - O pedido de logout expirou. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - O utilizador precisa de se inscrever para a autenticação do segundo fator (interativo). |
| AADSTS50074 | É necessária autenticação forte do UtilizadorStrongAuthAuthAuthNRequiredInterrupt - É necessária uma autenticação forte e o utilizador não passou no desafio MFA. |
| AADSTS50076 | UserStrongAuthLiientAuthNRequired - Devido a uma alteração de configuração feita pela administração, ou porque se mudou para um novo local, o utilizador deve utilizar a autenticação multi-factor para aceder ao recurso. Reda o pedido de autorização para o recurso. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - Devido a uma alteração de configuração feita pelo administrador, ou porque o utilizador se mudou para um novo local, o utilizador é obrigado a utilizar a autenticação de vários fatores. |
| AADSTS50085 | O token de atualização precisa de início de sessão de IDP social. Pedir ao utilizador que repita o início de sessão com nome de utilizador e a palavra-passe |
| AADSTS50086 | SasNonRetryryableError |
| AADSTS50087 | SasRetryableError - O serviço está temporariamente indisponível. Tente novamente. |
| AADSTS50089 | O fluxo de token expirou - falha na autenticação. Faça com que o utilizador tente iniciar sessão de autógrafos novamente com o nome de utilizador -password. |
| AADSTS50097 | Dispositivo É necessária autenticação do dispositivo- É necessária a autenticação do dispositivo. |
| AADSTS50099 | PKeyAuthInvalidJwtUnautized - A assinatura JWT é inválida. |
| AADSTS50105 | EntitlementGrantsNotFound - O utilizador assinado não está atribuído a uma função para a aplicação assinada. Atribua o utilizador à aplicação. Para mais informações:[.. /gestão de apps/aplicação-sign-in-problem-federated-sso-gallery.md#user-not-assigned-a-role](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri - O objeto do reino da federação solicitado não existe. Contacte o administrador do inquilino. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - Emissão com cabeçalho JWT. Contacte o administrador do inquilino. |
| AADSTS50124 | SinistrosTransformaçãoInvalidInputParameter - Sinistros A transformação contém um parâmetro de entrada inválido. Contacte o administrador de inquilinos para atualizar a política. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - O sprotedósico foi interrompido devido a um reset de palavra-passe ou entrada no registo de passwords. |
| AADSTS50126 | InvalidUserNameOrPassword - Erro validando credenciais devido a nome de utilizador ou senha inválido. |
| AADSTS50127 | BrokerAppNotIed - O utilizador precisa de instalar uma aplicação de corretor para ter acesso a este conteúdo. |
| AADSTS50128 | Nome de domínio inválido - Nenhuma informação de identificação do inquilino encontrada no pedido ou implícita por quaisquer credenciais fornecidas. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined - A ação no local de trabalho é necessária para registar o dispositivo. |
| AADSTS50131 | CondicionalAccessFailed - Indica vários erros de Acesso Condicional, tais como mau estado do dispositivo Windows, pedido bloqueado devido a atividade suspeita, política de acesso ou decisões de política de segurança. |
| AADSTS50132 | SsoArtifactInvalidOrExpired - A sessão não é válida devido à expiração da palavra-passe ou à recente alteração da palavra-passe. |
| AADSTS50133 | SsoArtifactRevoked - A sessão não é válida devido à expiração da palavra-passe ou à recente alteração da palavra-passe. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter - Centro de dados errado. Para autorizar um pedido iniciado por uma app no fluxo do dispositivo OAuth 2.0, a parte autor deve estar no mesmo centro de dados onde o pedido original reside. |
| AADSTS50135 | PasswordChangeCompromisedPassword - A alteração da palavra-passe é necessária devido ao risco de conta. |
| AADSTS50136 | Reorientar OToApp desativado - Sessão MSA única detetada. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken - A sessão é inválida devido a uma atualização externa em falta. |
| AADSTS50140 | KmsiInterrupt - Este erro ocorreu devido à interrupção "Mantenha-me assinado" quando o utilizador estava a iniciar a sessão. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o ID de Correlação, o ID do Pedido e o código de Erro para obter mais detalhes. |
| AADSTS50143 | Erro de correspondência de sessão: a sessão é inválida porque o inquilino do utilizador não corresponde à sugestão de domínio devido a um recurso ser diferente. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o ID de Correlação, o ID do Pedido e o código de Erro para obter mais detalhes. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - A palavra-passe do Diretor Ativo do Utilizador expirou. Gere uma nova palavra-passe para o utilizador ou faz com que o utilizador utilize a ferramenta de reset de autosserviço para redefinir a sua palavra-passe. |
| AADSTS50146 | MissingCustomSigningKey - Esta aplicação é necessária para ser configurada com uma chave de assinatura específica da aplicação. Ou não está configurada com uma chave destas ou a chave expirou ou ainda não é válida. |
| AADSTS50147 | MissingCodeChallenge - O tamanho do parâmetro de desafio de código não é válido. |
| AADSTS50155 | DeviceAuthenticationFailed - A autenticação do dispositivo falhou para este utilizador. |
| AADSTS50158 | ExternalSecurityChallenge - Desafio de segurança externa não foi satisfeito. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - As reclamações enviadas por fornecedores externos não são suficientes ou a reclamação em falta solicitada ao fornecedor externo. |
| AADSTS50166 | ExternalClaimsProviderThrottled - Não enviou o pedido ao fornecedor de sinistros. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - O cliente é capaz de obter um token SSO através da extensão de Contas Windows 10, mas o token não foi encontrado no pedido ou o token fornecido expirou. |
| AADSTS50169 | InvalidRequestBadRealm - O reino não é um reino configurado do espaço de nome de serviço atual. |
| AADSTS50170 | MissingExternalClaimsProviderMapping - Falta mapeamento de controlos externos. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - O desafio externo não é suportado para os utilizadores passthrough. |
| AADSTS50178 | SessionControlNotSupportForPassthroughUsers - O controlo de sessão não é suportado para os utilizadores passthrough. |
| AADSTS50180 | WindowsIntegratedAuthMissing - É necessária a autenticação integrada do Windows. Ativar o inquilino para SSO Totalmente Integrado. |
| AADSTS50187 | DeviceInformationNotProvided - O serviço falhou na autenticação do dispositivo. |
| AADSTS50196 | LoopDetected - Foi detetado um laço de cliente. Verifique a lógica da aplicação para garantir que o caching token é implementado, e que as condições de erro são manuseadas corretamente.  A aplicação fez demasiados pedidos em demasiado curto espaço de tempo, indicando que se encontra em estado de avaria ou que está a solicitar abusivamente fichas. |
| AADSTS50197 | Identificações contraditórias - O utilizador não foi encontrado. Tente entrar de novo. |
| AADSTS50199 | CmsiInterrupt - Por razões de segurança, a confirmação do utilizador é necessária para este pedido.  Porque este é um erro de "interaction_required", o cliente deve fazer auth interativo.  Isto ocorre porque um webview do sistema foi usado para solicitar um token para uma aplicação nativa - o utilizador deve ser solicitado a perguntar se esta era realmente a app que pretendiam assinar. Para evitar este pedido, o URI de redirecionamento deve fazer parte da seguinte lista de segurança: <br />http://<br />https://<br />msauth://(apenas iOS)<br />msauthv2://(apenas iOS)<br />chrome-extension:// (apenas para desktop Chrome) |
| AADSTS51000 | ObrigatórioFeatureNotEnabled - A função está desativada. |
| AADSTS51001 | DomainHintMustbePresent - Dica de domínio deve estar presente com o identificador de segurança no local ou no local UPN. |
| AADSTS51004 | UserAccountNotInDirect - A conta de utilizador não existe no diretório. |
| AADSTS51005 | TemporárioRedirect - Equivalente ao estado HTTP 307, o que indica que as informações solicitadas estão localizadas no URI especificado no cabeçalho de localização. Quando receber este estado, siga o cabeçalho de localização associado à resposta. Quando o método de pedido original foi POST, o pedido redirecionado também utilizará o método POST. |
| AADSTS51006 | É necessária a autenticação do ForceReauthDueToInsufficientAuth - Autenticação integrada do Windows. O utilizador iniciou sessão com um token de sessão que está a faltar à reclamação de autenticação integrada do Windows. Solicite ao utilizador que faça o login novamente. |
| AADSTS52004 | DelegaçãoDoEsNotExistForLinkedIn - O utilizador não forneceu consentimento para o acesso aos recursos do LinkedIn. |
| AADSTS53000 | A política de Acesso Condicional do Dispositivo Não Existe requer um dispositivo conforme e o dispositivo não está em conformidade. O utilizador tem de inscrever o dispositivo num fornecedor MDM aprovado, como o Intune. |
| AADSTS53001 | DeviceNotDomainJoined - Conditional Access policy requer um dispositivo de união de domínio, e o dispositivo não está unido ao domínio. Faça com que o utilizador utilize um dispositivo de união de domínio. |
| AADSTS53002 | AplicaçãoUsedIsNotAnApprovedAppedAppedApp - A aplicação utilizada não é uma aplicação aprovada para acesso condicional. O utilizador precisa de utilizar uma das aplicações da lista de aplicações aprovadas para ter acesso. |
| AADSTS53003 | BlockedByConditionalAccess - O acesso foi bloqueado por políticas de Acesso Condicional. A política de acesso não permite a emissão de fichas. |
| AADSTS53004 | ProofUpBlockedDueToRisk - O utilizador precisa de completar o processo de registo de autenticação multi-factor antes de aceder a este conteúdo. O utilizador deve registar-se na autenticação multifator. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegaçãoDoesNotExist - O utilizador ou administrador não consentiu em utilizar o pedido com o ID X. Envie um pedido de autorização interativa para este utilizador e recurso. |
| AADSTS65004 | UserDeclinedConsent - O utilizador recusou-se a consentir no acesso à aplicação. Pedir ao utilizador que repita o início de sessão e que autorize o acesso à aplicação|
| AADSTS65005 | Configuração erradaApplication - A lista de acesso a recursos exigida pela aplicação não contém aplicações detetáveis pelo recurso ou a aplicação do cliente solicitou acesso a recursos, o que não foi especificado na sua lista de acesso a recursos necessários ou o serviço Graph devolveu mau pedido ou recurso não encontrado. Se a aplicação suportar o SAML, poderá ter configurado a aplicação com o identificador errado (Entidade). Experimente a resolução listada para a SAML utilizando o link abaixo: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](../manage-apps/application-sign-in-problem-federated-sso-gallery.md?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS650052 | A aplicação precisa de acesso a um serviço `(\"{name}\")` que a sua organização não `\"{organization}\"` subscreveu ou ativou. Contacte o seu Administrador DE TI para rever a configuração das suas subscrições de serviço. |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - A autenticação falhou. O token de atualização não é válido. O erro pode dever-se às seguintes razões:<ul><li>Cabeçalho de encadernação token está vazio</li><li>O haxixe de ligação token não corresponde</li></ul> |
| AADSTS70001 | Não autorizado - A aplicação está desativada. |
| AADSTS70002 | InvalidAção - Erro de validação das credenciais. A client_secret especificada não corresponde ao valor esperado para este cliente. Corrija o client_secret e tente de novo. Para obter mais informações, consulte [utilizar o código de autorização para solicitar um token de acesso.](v2-oauth2-auth-code-flow.md#request-an-access-token) |
| AADSTS70003 | Não suportadoGrantType - A aplicação devolveu um tipo de subvenção não suportado. |
| AADSTS70004 | InvalidRedirectUri - A aplicação devolveu um URI de redirecionamento inválido. O endereço de redirecionamento especificado pelo cliente não corresponde a nenhum dos endereços configurados ou a nenhum dos endereços na lista de aprovações OIDC. |
| AADSTS70005 | Não suportadoResponseType - A aplicação devolveu um tipo de resposta não suportado devido às seguintes razões:<ul><li>o tipo de resposta 'token' não está ativado para a aplicação</li><li>o tipo de resposta “id_token” requer o âmbito “OpenID”; contém um valor de parâmetro de OAuth não suportado no wctx codificado</li></ul> |
| AADSTS70007 | Não suportadaResponseMode - A aplicação devolveu um valor não suportado `response_mode` ao solicitar um token.  |
| AADSTS70008 | ExpiradoOrRevokedGrant - O token de atualização expirou devido à inatividade. O token foi emitido em XXX e esteve inativo por um certo período de tempo. |
| AADSTS70011 | InvalidScope - O âmbito solicitado pela aplicação é inválido. |
| AADSTS70012 | MsaServerError - Ocorreu um erro de servidor durante a autenticação de um utilizador MSA (consumidor). Tente novamente. Se continuar a falhar, [abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AutorizaçãoSPending - Erro de fluxo do dispositivo OAuth 2.0. A autorização está pendente. O dispositivo vai voltar a sondar o pedido. |
| AADSTS70018 | BadVerificationCode - Código de verificação inválido devido à dactilografia do Utilizador no código do utilizador errado para o fluxo de código do dispositivo. A autorização não é aprovada. |
| AADSTS70019 | CódigoEx expirado - Código de verificação expirado. Peça ao utilizador que volte a tentar iniciar sessão. |
| AADSTS75001 | BindingSerializationError - Ocorreu um erro durante a ligação da mensagem SAML. |
| AADSTS75003 | Não suportadaBindingError - A aplicação devolveu um erro relacionado com a ligação não suportada (a resposta do protocolo SAML não pode ser enviada através de encadernações diferentes do HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid - Azure AD não suporta o pedido SAML enviado pela app para SSO. |
| AADSTS7500514 | Não foi encontrado um tipo de resposta SAML suportado. Os tipos de resposta suportados são 'Resposta' (em XML namespace 'urn:oasis:names:tc:SAML:2.0:protocol') ou 'Assertion' (em XML namespace 'urn:oasis:names:tc:SAML:2.0:assertion'). Erro de aplicação - o desenvolvedor tratará deste erro.|
| AADSTS75008 | RequestDeniedError - O pedido da app foi negado uma vez que o pedido da SAML tinha um destino inesperado. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - O método de autenticação pelo qual o utilizador autenticado com o serviço não corresponde ao método de autenticação solicitado. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - SAML2 Authentication Request has invalid NameIdPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable - O Agente de Autenticação não consegue ligar-se ao Ative Directory. Certifique-se de que os servidores de agentes são membros da mesma floresta de AD que os utilizadores cujas palavras-passe precisam de ser validadas e que são capazes de se conectar ao Ative Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout - Pedido de validação de palavras-passe cronometrado. Certifique-se de que o Ative Directory está disponível e responde aos pedidos dos agentes. |
| AADSTS80005 | OnPremisePasswordValidatorUnprevisívelWebException - Ocorreu um erro desconhecido durante o processamento da resposta do Agente de Autenticação. Recandidutar o pedido. Se continuar a falhar, [abra um bilhete de apoio](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - O Agente de Autenticação não consegue validar a palavra-passe do utilizador. Verifique os registos do agente para obter mais informações e verifique se o Ative Directory está a funcionar como esperado. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException - O Agente de Autenticação não consegue desencriptar a palavra-passe. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours - Os utilizadores tentaram iniciar sessão fora das horas permitidas (isto é especificado em AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew - A tentativa de autenticação não pôde ser concluída devido a uma distorção temporal entre a máquina que executa o agente de autenticação e a AD. Corrija problemas de sincronização de tempo. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Tentativa de autenticação Kerberos falhou. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - O pacote de autenticação não é suportado. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - Não foi encontrado um cabeçalho de autorização. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - O inquilino não está habilitado para SSO sem emenda. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - Incapaz de validar o bilhete Kerberos do utilizador. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - Seamless SSO falhou porque o bilhete Kerberos do utilizador expirou ou é inválido. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - Incapaz de encontrar objetos de utilizador com base em informações no bilhete Kerberos do utilizador. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - O utilizador que tenta entrar no Azure AD é diferente do utilizador que assinou no dispositivo. |
| AADSTS90002 | InvalidTenantName - O nome do inquilino não foi encontrado na loja de dados. Verifique se tem a identificação correta do inquilino. |
| AADSTS90004 | InvalidRequestFormat - O pedido não está devidamente formatado. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements - Incapaz de completar o pedido. O pedido não é válido porque o identificador e a dica de login não podem ser usados juntos.  |
| AADSTS90006 | ExternalServerRetryableError - O serviço está temporariamente indisponível.|
| AADSTS90007 | InvalidSessionId - Mau pedido. A identificação da sessão aprovada não pode ser analisada. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission - O utilizador ou administrador não consentiu em utilizar a aplicação. No mínimo, a aplicação requer acesso ao Azure AD especificando a permissão de inscrição e leitura do perfil do utilizador. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier - A aplicação está a solicitar um símbolo para si próprio. Este cenário só é suportado se o recurso especificado estiver a utilizar o ID de aplicação baseado no GUID. |
| AADSTS90010 | NotSupported - Incapaz de criar o algoritmo. |
| AADSTS90012 | RequestTimeout - O pedido já está esgotado. |
| AADSTS90013 | InvalidUserInput - A entrada do utilizador não é válida. |
| AADSTS90014 | MissingRequiredField - Este código de erro pode aparecer em vários casos quando um campo esperado não está presente na credencial. |
| AADSTS90015 | QueryStringTooLong - A cadeia de consulta é demasiado comprida. |
| AADSTS90016 | MissingRequiredClaim - O sinal de acesso não é válido. A reivindicação necessária está em falta. |
| AADSTS90019 | MissingTenantRealm - A Azure AD não foi capaz de determinar o identificador do inquilino do pedido. |
| AADSTS90022 | AutenticadoInvalidPrincipalNameFormat - O formato principal não é válido ou não cumpre o `name[/host][@realm]` formato esperado. O nome principal é necessário, hospedeiro e reino são opcionais e podem ser definidos como nulos. |
| AADSTS90023 | InvalidRequest - O pedido de serviço de autenticação não é válido. |
| AADSTS9002313 | InvalidRequest - O pedido é mal formado ou inválido. - (EN) A questão aqui é porque havia algo de errado com o pedido a um determinado ponto final. A sugestão para esta questão é obter um traço de violino do erro que ocorre e ver se o pedido está realmente devidamente formatado ou não. |
| AADSTS90024 | RequestBudgetExceededError - Ocorreu um erro transitório. Tente novamente. |
| AADSTS90033 | MsodsServiceUn disponível - O Serviço de Diretório Online da Microsoft (MSODS) não está disponível. |
| AADSTS90036 | MsodsServiceUnretryableFailure - Ocorreu um erro inesperado e não retripável do serviço WCF hospedado pelo MSODS. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro. |
| AADSTS90038 | NationalCloudTenantRedirection - O inquilino especificado 'Y' pertence à Nuvem Nacional 'X'. A atual instância de nuvem 'Z' não federa com X. Um erro de redirecionamento de nuvem é devolvido. |
| AADSTS90043 | NationalCloudAuthCodeRedirection - A funcionalidade está desativada. |
| AADSTS90051 | InvalidNationalCloudId - O identificador de nuvem nacional contém um identificador de nuvem inválido. |
| AADSTS90055 | TenantThrottlingError - Há muitos pedidos de entrada. Esta exceção é lançada para inquilinos bloqueados. |
| AADSTS90056 | BadResourceRequest - Para resgatar o código para um token de acesso, a aplicação deve enviar um pedido de POST para o `/token` ponto final. Além disso, antes disso, deverá fornecer um código de autorização e enviá-lo no pedido DOM para o `/token` ponto final. Consulte este artigo para obter uma visão geral do fluxo de código de autorização OAuth 2.0: [.. /azuread-dev/v1-protocols-oauth-code.md](../azuread-dev/v1-protocols-oauth-code.md). Direcione o utilizador para o `/authorize` ponto final, que devolverá uma authorization_code. Ao publicar um pedido no `/token` ponto final, o utilizador obtém o token de acesso. Faça login no portal Azure e verifique **as inscrições da App > Endpoints** para confirmar que os dois pontos finais foram configurados corretamente. |
| AADSTS90072 | PassThroughUserMfaError - A conta externa com a qual o utilizador assina não existe no arrendatário em que se inscreveu; para que o utilizador não possa satisfazer os requisitos de MFA para o inquilino. Este erro também pode ocorrer se os utilizadores estiverem sincronizados, mas existe uma incompatibilidade no atributo ImmutableID (sourceAnchor) entre o Ative Directory e o Ad Azure. A conta deve ser adicionada como utilizador externo no inquilino primeiro. Inscreva-se e inscreva-se com uma conta de utilizador AZure AD diferente. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid - Ocorreu um erro quando o serviço tentou processar uma mensagem WS-Federation. A mensagem não é válida. |
| AADSTS90082 | OrgIdWsFederationNotSupported - A política de autenticação selecionada para o pedido não é suportada atualmente. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed - As contas de hóspedes não são permitidas para este site. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed - O serviço não é capaz de emitir um token porque o objeto da empresa ainda não foi aprovisionado. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired - O token da DA do utilizador está expirado. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed - Ocorreu um erro ao criar a mensagem WS-Federation do URI. |
| AADSTS90090 | GraphRetryableError - O serviço está temporariamente indisponível. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnautized - Gráfico devolvido com um código de erro proibido para o pedido. |
| AADSTS90094 | AdminConsentRequired - Consentimento do administrador é necessário. |
| AADSTS900382 | O Cliente Confidencial não é suportado no pedido da Cross Cloud. |
| AADSTS90099 | A aplicação '{appId}' ({appName}) não foi autorizada no inquilino '{tenant}'. As candidaturas devem ser autorizadas a aceder ao cliente inquilino antes que os administradores delegados do parceiro possam usá-las. Fornecer pré-consentimento ou executar a API do Centro de Parceiros apropriado para autorizar o pedido. |
| AADSTS90100 | InvalidRequestParameter - O parâmetro está vazio ou não é válido. |
| AADSTS901002 | AADSTS901002: O parâmetro de pedido de "recursos" não é suportado. |
| AADSTS90101 | InvalidEmailAddress - Os dados fornecidos não são um endereço de e-mail válido. O endereço de e-mail deve estar no formato `someone@example.com` . |
| AADSTS90102 | InvalidUriParameter - O valor deve ser um URI absoluto válido. |
| AADSTS90107 | InvalidXml - O pedido não é válido. Certifique-se de que os seus dados não têm caracteres inválidos.|
| AADSTS90114 | InvalidExpiryDate - O prazo de validade do token a granel fará com que um token expirado seja emitido. |
| AADSTS90117 | InádutoRequestInput |
| AADSTS90119 | InvalidUserCode - O código do utilizador é nulo ou vazio.|
| AADSTS90120 | InvalidDeviceFlowRequest - O pedido já foi autorizado ou recusado. |
| AADSTS90121 | InvalidEmptyRequest - Pedido inválido vazio.|
| AADSTS90123 | IdentidadeProviderAccessDenied - O token não pode ser emitido porque o fornecedor de identidade ou emissão de reclamações negou o pedido. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported - O recurso não é suportado sobre os `/common` `/consumers` pontos finais ou finais. Em vez disso, utilize o `/organizations` ponto final específico do inquilino ou do inquilino. |
| AADSTS90125 | DebugModeEnrollTenantNotFound - O utilizador não está no sistema. Certifique-se de que introduziu corretamente o nome do utilizador. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred - O tipo de utilizador não é suportado neste ponto final. O sistema não pode inferir o inquilino do utilizador a partir do nome de utilizador. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported - A aplicação não é suportada sobre os `/common` `/consumers` pontos finais ou finais. Em vez disso, utilize o `/organizations` ponto final específico do inquilino ou do inquilino. |
| AADSTS120000 | PasswordChangeIncorrectSFrword |
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
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound - O diretor do utilizador não tem a chave de ID NGC configurada. |
| AADSTS130005 | NgcInvalidSignature - Assinatura chave NGC verificada falhou.|
| AADSTS130006 | NgcTransportKeyNotFound - A chave de transporte NGC não está configurada no dispositivo. |
| AADSTS130007 | NgcDeviceIsDisabled - O dispositivo está desativado. |
| AADSTS130008 | NgcDeviceIsNotFound - O dispositivo referenciado pela tecla NGC não foi encontrado. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce - Pedido não é fornecido. |
| AADSTS140001 | InvalidSessionKey - A tecla de sessão não é válida.|
| AADSTS165900 | InvalidApiRequest - Pedido inválido. |
| AADSTS220450 | Não suportado,AndroidWebViewVersion - A versão Chrome WebView não é suportada. |
| AADSTS220501 | Carga invalidaCrldown |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - O recurso não está configurado para aceitar fichas apenas para dispositivos. |
| AADSTS240001 | BulkAADJTokenUnautized - O utilizador não está autorizado a registar dispositivos em Azure AD. |
| AADSTS240002 | RequeridoClaimIsMissing - O id_token não pode ser usado como `urn:ietf:params:oauth:grant-type:jwt-bearer` subvenção.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy - O administrador do inquilino configura uma política de segurança que bloqueia este pedido. Verifique as políticas de segurança definidas ao nível do inquilino para determinar se o pedido cumpre os requisitos da política. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest - O pedido não foi encontrado no diretório/inquilino. Este erro poderá ocorrer se a aplicação não tiver sido instalada pelo administrador do inquilino ou não tiver sido permitida por qualquer utilizador do inquilino. Poderá ter configurado incorretamente o valor do identificador da aplicação ou enviado o pedido de autenticação para o inquilino errado. |
| AADSTS700020 | InteractionRequired - A bolsa de acesso requer interação. |
| AADSTS700022 | InvalidMultipleResourcesScope - O valor fornecido para o âmbito do parâmetro de entrada não é válido porque contém mais do que um recurso. |
| AADSTS700023 | InvalidResourcelessScope - O valor fornecido para o âmbito do parâmetro de entrada não é válido quando se solicita um token de acesso. |
| AADSTS7000215 | O segredo do cliente inválido é fornecido. Erro do desenvolvedor - a aplicação está a tentar iniciar sing sem os parâmetros de autenticação necessários ou corretos.|
| AADSTS700022 | InvalidClientSecretExpiredKeysProvided - As chaves secretas fornecidas pelo cliente estão caducadas. Visite o portal Azure para criar novas chaves para a sua aplicação, ou considere usar credenciais de certificado para maior segurança: [https://aka.ms/certCreds](https://aka.ms/certCreds) |
| AADSTS700005 | InvalidGrantRedeemAgainstWrongTenant - O Código de Autorização Fornecido destina-se a ser utilizado contra outro inquilino, assim rejeitado. O Código de Autorização OAuth2 deve ser reembolsado contra o mesmo inquilino para o qual foi adquirido (/comum ou /{tenant-ID} conforme apropriado) |
| AADSTS1000000 | UserNotBoundError - A API Bind exige que o utilizador AZure AD também autentica com um IDP externo, o que ainda não aconteceu. |
| AADSTS100002 | BindCompleteInterruptError - A ligação foi concluída com sucesso, mas o utilizador deve ser informado. |
| AADSTS7000112 | Não autorizadoClientApplicationDabled - A aplicação está desativada. |
| AADSTS7000114| A aplicação 'appIdentifier' não está autorizada a fazer a aplicação em nome de chamadas.|
| AADSTS7500529 | O valor 'SAMLId-Guid' não é um ID SAML válido - A azure AD utiliza este atributo para preencher o atributo InResponseTo da resposta devolvida. O ID não deve começar com um número, por isso uma estratégia comum é preparar uma corda como "id" para a representação de cordas de um GUID. Por exemplo, id6c1c178c16666687be4aaf5e482730 é um ID válido. |

## <a name="next-steps"></a>Passos seguintes

* Tem uma pergunta ou não consegue encontrar o que procura? Crie um problema do GitHub ou veja [opções de Suporte e ajuda para que os desenvolvedores](./developer-support-help-options.md) aprendam sobre outras formas de obter ajuda e suporte.
