---
title: Guia de migração da ADAL para o Android Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como migrar a sua aplicação Para Autenticação ativa do Azure Ative Directory (ADAL) para a Biblioteca de Autenticação do Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 10/14/2020
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 178d3896fe8d063855a734f3f0fe6c489b0ec1fc
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97651978"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Guia de migração ADAL para MSAL para Android

Este artigo destaca as alterações que precisa de fazer para migrar uma aplicação que utiliza a Biblioteca de Autenticação ativa do Azure Ative (ADAL) para utilizar a Biblioteca de Autenticação do Microsoft (MSAL).

## <a name="difference-highlights"></a>Destaques da diferença

A ADAL trabalha com o Azure Ative Directory v1.0 endpoint. A Microsoft Authentication Library (MSAL) trabalha com a plataforma de identidade da Microsoft, anteriormente conhecida como o ponto final do Azure Ative Directory v2.0. A plataforma de identidade da Microsoft difere do Azure Ative Directory v1.0 na medida em que:

Suportes:
  - Identidade Organizacional (Diretório Ativo Azure)
  - Identidades não organizacionais como Outlook.com, Xbox Live, e assim por diante
  - (apenas Azure AD B2C) Login federado com Google, Facebook, Twitter e Amazon

- As normas são compatíveis com:
  - OAuth v2.0
  - Ligação OpenID (OIDC)

A API pública MSAL introduz mudanças importantes, incluindo:

- Um novo modelo de acesso a fichas:
  - A ADAL fornece acesso a fichas através do `AuthenticationContext` , que representa o servidor. A MSAL dá acesso a fichas através do `PublicClientApplication` , que representa o cliente. Os desenvolvedores de clientes não precisam de criar um novo `PublicClientApplication` exemplo para cada Autoridade com a qual precisam interagir. Apenas é necessária uma `PublicClientApplication` configuração.
  - Suporte para solicitar tokens de acesso utilizando âmbitos para além de identificadores de recursos.
  - Suporte para consentimento incremental. Os desenvolvedores podem solicitar âmbitos à medida que o utilizador acede a cada vez mais funcionalidades na aplicação, incluindo as que não estão incluídas durante o registo da app.
  - As autoridades já não são validadas a tempo de fuga. Em vez disso, o desenvolvedor declara uma lista de "autoridades conhecidas" durante o desenvolvimento.
- Alterações da API token:
  - Em ADAL, `AcquireToken()` primeiro faz um pedido silencioso. Se não o fizer, faz um pedido interativo. Este comportamento resultou em alguns desenvolvedores que se basearam apenas em `AcquireToken` , o que levou o utilizador a ser inesperadamente solicitado para credenciais em alguns momentos. A MSAL exige que os desenvolvedores sejam intencionais sobre quando o utilizador recebe uma solicitação de UI.
    - `AcquireTokenSilent` resulta sempre num pedido silencioso que ou sucede ou falha.
    - `AcquireToken` resulta sempre num pedido que solicita ao utilizador via UI.
- O MSAL suporta o sômin de um navegador predefinido ou de uma vista web incorporada:
  - Por predefinição, o navegador predefinido do dispositivo é utilizado. Isto permite à MSAL utilizar o estado de autenticação (cookies) que já podem estar presentes para uma ou mais contas assinadas. Se não existir nenhum estado de autenticação, a autenticação durante a autorização via MSAL resulta na criação do estado de autenticação (cookies) em benefício de outras aplicações web que serão utilizadas no mesmo navegador.
- Novo modelo de exceção:
  - As exceções definem mais claramente o tipo de erro que ocorreu e o que o desenvolvedor precisa de fazer para o resolver.
- O MSAL suporta objetos `AcquireToken` de parâmetros e `AcquireTokenSilent` chamadas.
- A MSAL suporta a configuração declarativa para:
  - Identificação do cliente, redirecione a URI.
  - Inserida vs Navegador Padrão
  - Autoridades
  - Definições HTTP como tempo de leitura e de ligação

## <a name="your-app-registration-and-migration-to-msal"></a>O registo da sua aplicação e migração para a MSAL

Não precisa de alterar o registo de aplicações existente para utilizar o MSAL. Se quiser tirar partido do consentimento incremental/progressivo, poderá ter de rever o registo para identificar os âmbitos específicos que pretende solicitar incrementalmente. Seguem-se mais informações sobre âmbitos e consentimento incremental.

No registo da sua aplicação no portal, verá um separador **de permissões da API.** Isto fornece uma lista das APIs e permissões (âmbitos) a que a sua aplicação está atualmente configurada para solicitar acesso. Também mostra uma lista dos nomes de âmbito associados a cada permissão API.

### <a name="user-consent"></a>Consentimento do utilizador

Com a ADAL e o ponto final Azure AD v1, o consentimento do utilizador para os recursos que possuem foi concedido na primeira utilização. Com o MSAL e a plataforma de identidade da Microsoft, o consentimento pode ser solicitado incrementalmente. O consentimento incremental é útil para permissões que um utilizador pode considerar de alto privilégio, ou pode de outra forma questionar se não for fornecida uma explicação clara do porquê da permissão ser necessária. No ADAL, estas permissões podem ter resultado no abandono do utilizador na sua aplicação.

> [!TIP]
> Recomendamos o uso do consentimento incremental em cenários em que necessita de fornecer contexto adicional ao seu utilizador sobre o motivo pelo qual a sua aplicação necessita de uma permissão.

### <a name="admin-consent"></a>Consentimento do administrador

Os administradores da organização podem consentir com permissões que a sua candidatura requer em nome de todos os membros da sua organização. Algumas organizações apenas permitem que os administradores consintam nas aplicações. O consentimento da administração requer que inclua todas as permissões e âmbitos da API utilizados pela sua aplicação no registo da sua aplicação.

> [!TIP]
> Mesmo que possa solicitar um âmbito usando o MSAL para algo que não está incluído no registo da sua aplicação, recomendamos que atualize o registo da sua aplicação para incluir todos os recursos e âmbitos a que um utilizador possa conceder permissão.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migração de IDs de recursos para âmbitos

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Autenticar e solicitar autorização para todas as permissões na primeira utilização

Se está a usar o ADAL e não precisa de usar o consentimento incremental, a forma mais simples de começar a usar o MSAL é fazer um `acquireToken` pedido usando o novo `AcquireTokenParameter` objeto e definir o valor de ID do recurso.

> [!CAUTION]
> Não é possível definir ambos os âmbitos e uma identificação de recursos. Tentar definir ambos resultará em `IllegalArgumentException` .

Isto resultará no mesmo comportamento v1 que é utilizado. Todas as permissões solicitadas no registo da sua aplicação são solicitadas ao utilizador durante a sua primeira interação.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Autenticar e solicitar permissões apenas conforme necessário

Para tirar partido do consentimento incremental, faça uma lista de permissões (âmbitos) que a sua aplicação utiliza a partir do registo da sua aplicação e organize-as em duas listas com base em:

- Quais os âmbitos que pretende solicitar durante a primeira interação do utilizador com a sua aplicação durante a sinserção.
- As permissões que estão associadas a uma característica importante da sua app que também terá de explicar ao utilizador.

Uma vez organizados os âmbitos, organize cada lista através do qual o recurso (API) pretende solicitar um símbolo. Bem como quaisquer outros âmbitos que deseje que o utilizador autorize ao mesmo tempo.

O objeto de parâmetros utilizado para fazer o seu pedido aos suportes MSAL:

- `Scope`: A lista de âmbitos para os quais pretende solicitar autorização e receber um token de acesso.
- `ExtraScopesToConsent`: Uma lista adicional de âmbitos para os quais pretende solicitar autorização enquanto está a solicitar um sinal de acesso para outro recurso. Esta lista de âmbitos permite-lhe minimizar o número de vezes que necessita para solicitar autorização do utilizador. O que significa menos pedidos de autorização ou consentimento do utilizador.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migrar de AutenticaçãoContexto para PublicClientApplicações

### <a name="constructing-publicclientapplication"></a>Construção de Divulgação deClient Público

Quando se usa o MSAL, `PublicClientApplication` instantaneamente. Este objeto modela a sua identidade de aplicação e é usado para fazer pedidos a uma ou mais autoridades. Com este objeto irá configurar a identidade do seu cliente, redirecionar URI, autoridade padrão, se usar o navegador do dispositivo vs. vista web incorporada, o nível de registo, e muito mais.

Pode configurar declarativamente este objeto com JSON, que fornece como ficheiro ou armazena como recurso dentro da sua APK.

Embora este objeto não seja um singleton, internamente usa partilhado `Executors` para pedidos interativos e silenciosos.

### <a name="business-to-business"></a>Negócios para Negócios

Em ADAL, todas as organizações a que solicitam fichas de acesso requerem uma instância separada do `AuthenticationContext` . Na MSAL, isto já não é um requisito. Pode especificar a autoridade a partir da qual pretende solicitar um token como parte do seu pedido silencioso ou interativo.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migrar da validação da autoridade para as autoridades conhecidas

A MSAL não tem uma bandeira para permitir ou desativar a validação da autoridade. A validação da autoridade é uma característica na ADAL, e nas primeiras libertações da MSAL, que impede o seu código de solicitar fichas a uma autoridade potencialmente maliciosa. A MSAL recupera agora uma lista de autoridades conhecidas da Microsoft e funde essa lista com as autoridades que especificou na sua configuração.

> [!TIP]
> Se é um utilizador do Azure Business to Consumer (B2C), isto significa que já não tem de desativar a validação da autoridade. Em vez disso, inclua cada uma das suas políticas Azure AD B2C apoiadas como autoridades na sua configuração MSAL.

Se tentar usar uma autoridade que não é do conhecimento da Microsoft, e não está incluída na sua configuração, receberá uma `UnknownAuthorityException` .

### <a name="logging"></a>Registo
Pode agora configurar declarativamente o registo registado como parte da sua configuração, como esta:

```json
"logging": {
  "pii_enabled": false,
  "log_level": "WARNING",
  "logcat_enabled": true
}
```

## <a name="migrate-from-userinfo-to-account"></a>Migrar do UserInfo para a Conta

Em ADAL, o `AuthenticationResult` objeto fornece um objeto usado para recuperar `UserInfo` informações sobre a conta autenticada. O termo "utilizador", que significava um agente humano ou de software, foi aplicado de uma forma que dificultou a comunicação de que algumas aplicações suportam um único utilizador (seja um agente humano ou de software) que tenha várias contas.

Considere uma conta bancária. Pode ter mais de uma conta em mais de uma instituição financeira. Quando abre uma conta, você (o utilizador) é emitida credenciais, como um Cartão multibanco & PIN, que são usadas para aceder ao seu saldo, pagamentos de faturas, e assim por diante, para cada conta. Estas credenciais só podem ser utilizadas na instituição financeira que as emitiu.

Por analogia, como contas numa instituição financeira, as contas na plataforma de identidade da Microsoft são acedidas com recurso a credenciais. Estas credenciais estão registadas ou emitidas pela Microsoft. Ou pela Microsoft em nome de uma organização.

Quando a plataforma de identidade da Microsoft difere de uma instituição financeira, nesta analogia, é que a plataforma de identidade da Microsoft fornece um quadro que permite ao utilizador utilizar uma conta, e as suas credenciais associadas, para aceder a recursos que pertencem a vários indivíduos e organizações. Isto é como poder usar um cartão emitido por um banco, em mais uma instituição financeira. Isto funciona porque todas as organizações em questão estão a usar a plataforma de identidade da Microsoft, que permite que uma conta seja usada em várias organizações. Eis um exemplo:

Sam trabalha para Contoso.com mas gere máquinas virtuais Azure que pertencem a Fabrikam.com. Para o Sam gerir as máquinas virtuais do Fabrikam, tem de ser autorizado a aceder-lhes. Este acesso pode ser concedido adicionando a conta de Sam a Fabrikam.com, e concedendo à sua conta um papel que lhe permite trabalhar com as máquinas virtuais. Isto seria feito com o portal Azure.

Adicionar a conta de Contoso.com de Sam como membro da Fabrikam.com resultaria na criação de um novo recorde em Fabrikam.com's Azure Ative Directory for Sam. O registo de Sam no Azure Ative Directory é conhecido como um objeto de utilizador. Neste caso, esse objeto do utilizador apontaria para o objeto do utilizador do Sam em Contoso.com. O objeto de utilizador de Sam Fabrikam é a representação local de Sam, e seria usado para armazenar informações sobre a conta associada a Sam no contexto de Fabrikam.com. Em Contoso.com, o título de Sam é Consultor Sénior de DevOps. Em Fabrikam, o título de Sam é Contractor-Virtual Máquinas. Em Contoso.com, a Sam não é responsável, nem autorizado, para gerir máquinas virtuais. Em Fabrikam.com, é a sua única função de trabalho. No entanto, Sam ainda só tem um conjunto de credenciais para acompanhar, que são as credenciais emitidas pela Contoso.com.

Uma vez feita uma chamada bem `acquireToken` sucedida, você verá uma referência a um `IAccount` objeto que pode ser usado em `acquireTokenSilent` pedidos posteriores.

### <a name="imultitenantaccount"></a>Contagem IMultiTenant

Se tiver uma aplicação que aceda a reclamações sobre uma conta de cada um dos inquilinos em que a conta está representada, pode lançar `IAccount` objetos para `IMultiTenantAccount` . Esta interface fornece um mapa `ITenantProfiles` de, chavedo pelo ID do inquilino, que lhe permite aceder às reclamações que pertencem à conta em cada um dos inquilinos a que solicitou um token de, relativamente à conta corrente.

As reclamações na raiz do `IAccount` e contêm sempre as `IMultiTenantAccount` reclamações do arrendatário. Se ainda não fez um pedido de um token dentro do inquilino, esta coleção estará vazia.

## <a name="other-changes"></a>Outras alterações

### <a name="use-the-new-authenticationcallback"></a>Utilize o novo AutenticaçãoCallback

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}
```

## <a name="migrate-to-the-new-exceptions"></a>Migrar para as novas exceções

Em ADAL, há um tipo de exceção, `AuthenticationException` que inclui um método para recuperar o valor `ADALError` enum.
No MSAL, há uma hierarquia de exceções, e cada um tem o seu próprio conjunto de códigos de erro específicos associados.

| Exceção                                        | Descrição                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| `MsalArgumentException`                          | Lançados se um ou mais argumentos de entradas forem inválidos.                 |
| `MsalClientException`                            | Lançado se o erro for do lado do cliente.                                 |
| `MsalDeclinedScopeException`                     | Lançado se um ou mais âmbitos solicitados fossem recusados pelo servidor. |
| `MsalException`                                  | Exceção verificada por defeito lançada pela MSAL.                           |
| `MsalIntuneAppProtectionPolicyRequiredException` | Lançado se o recurso tiver a política de proteção MAMCA ativada.         |
| `MsalServiceException`                           | Lançado se o erro for o lado do servidor.                                 |
| `MsalUiRequiredException`                        | Jogado se o símbolo não puder ser refrescado silenciosamente.                    |
| `MsalUserCancelException`                        | Lançado se o utilizador cancelar o fluxo de autenticação.                |

### <a name="adalerror-to-msalexception-translation"></a>Tradução ADALError para MsalException

| Se estás a apanhar estes erros no ADAL...  | ... apanhem estas exceções msal:                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| *Sem ADALError equivalente* | `MsalArgumentException`                          |
| <ul><li>`ADALError.ANDROIDKEYSTORE_FAILED`<li>`ADALError.AUTH_FAILED_USER_MISMATCH`<li>`ADALError.DECRYPTION_FAILED`<li>`ADALError.DEVELOPER_AUTHORITY_CAN_NOT_BE_VALIDED`<li>`ADALError.EVELOPER_AUTHORITY_IS_NOT_VALID_INSTANCE`<li>`ADALError.DEVELOPER_AUTHORITY_IS_NOT_VALID_URL`<li>`ADALError.DEVICE_CONNECTION_IS_NOT_AVAILABLE`<li>`ADALError.DEVICE_NO_SUCH_ALGORITHM`<li>`ADALError.ENCODING_IS_NOT_SUPPORTED`<li>`ADALError.ENCRYPTION_ERROR`<li>`ADALError.IO_EXCEPTION`<li>`ADALError.JSON_PARSE_ERROR`<li>`ADALError.NO_NETWORK_CONNECTION_POWER_OPTIMIZATION`<li>`ADALError.SOCKET_TIMEOUT_EXCEPTION`</ul> | `MsalClientException`                            |
| *Sem ADALError equivalente* | `MsalDeclinedScopeException`                     |
| <ul><li>`ADALError.APP_PACKAGE_NAME_NOT_FOUND`<li>`ADALError.BROKER_APP_VERIFICATION_FAILED`<li>`ADALError.PACKAGE_NAME_NOT_FOUND`</ul> | `MsalException`                                  |
| *Sem ADALError equivalente* | `MsalIntuneAppProtectionPolicyRequiredException` |
| <ul><li>`ADALError.SERVER_ERROR`<li>`ADALError.SERVER_INVALID_REQUEST`</ul> | `MsalServiceException`                           |
| <ul><li>`ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED` | `MsalUiRequiredException`</ul>                        |
| *Sem ADALError equivalente* | `MsalUserCancelException`                        |

### <a name="adal-logging-to-msal-logging"></a>Registo ADAL para ADSAL Logging

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```
