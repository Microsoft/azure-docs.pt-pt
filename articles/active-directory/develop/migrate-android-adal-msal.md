---
title: Guia de migração DaAL para Android  ADAL) Azure
description: Saiba como migrar a sua aplicação Android (ADAL) Da Biblioteca de Autenticação ativa do Azure para a Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084052"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Guia de migração DaAL para Android

Este artigo destaca as alterações que precisa de fazer para migrar uma aplicação que utiliza a Biblioteca de Autenticação de Diretórios Ativos Azure (ADAL) para utilizar a Biblioteca de Autenticação da Microsoft (MSAL).

## <a name="difference-highlights"></a>Destaques da diferença

A ADAL trabalha com o Diretório Ativo Azure v1.0 endpoint. A Microsoft Authentication Library (MSAL) trabalha com a plataforma de identidade Microsoft, anteriormente conhecida como O Ponto Final do Diretório Ativo Azure v2.0. A plataforma de identidade da Microsoft difere do Azure Ative Directory v1.0 na medida em que:

Suportes:
  - Identidade Organizacional (Diretório Ativo Azure)
  - Identidades não-organizacionais como Outlook.com, Xbox Live, e assim por diante
  - (apenas B2C) Login federado com Google, Facebook, Twitter e Amazon

- As normas são compatíveis com:
  - OAuth v2.0
  - OpenID Connect (OIDC)

A API pública mSAL introduz mudanças importantes, incluindo:

- Um novo modelo de acesso a fichas:
  - A ADAL fornece acesso a fichas através do `AuthenticationContext`, que representa o servidor. A MSAL dá acesso a fichas através do `PublicClientApplication`, que representa o cliente. Os desenvolvedores de clientes não precisam de criar um novo `PublicClientApplication` exemplo para todas as Autoridades com as quais precisam interagir. É necessária apenas um `PublicClientApplication` configuração.
  - Suporte para solicitar fichas de acesso utilizando âmbitos para além de identificadores de recursos.
  - Suporte para consentimento incremental. Os desenvolvedores podem solicitar âmbitos à medida que o utilizador acede cada vez mais funcionalidades na aplicação, incluindo as que não estão incluídas durante o registo da aplicação.
  - As autoridades já não são validadas no tempo de execução. Em vez disso, o desenvolvedor declara uma lista de "autoridades conhecidas" durante o desenvolvimento.
- Alterações da API simbólica:
  - Na ADAL, `AcquireToken()` primeiro faz um pedido silencioso. Se não o fizer, faz um pedido interativo. Este comportamento resultou em alguns desenvolvedores confiarem apenas em `AcquireToken`, o que resultou em que o utilizador foi inesperadamente solicitado para credenciais às vezes. A MSAL exige que os desenvolvedores sejam intencionais sobre quando o utilizador recebe um pedido de UI.
    - `AcquireTokenSilent` sempre resulta num pedido silencioso que tenha sucesso ou falhe.
    - `AcquireToken` sempre resulta num pedido que leva o utilizador através de UI.
- A MSAL suporta o sessão a partir de um navegador padrão ou de uma visão web incorporada:
  - Por padrão, o navegador predefinido no dispositivo é utilizado. Isto permite à MSAL utilizar o estado de autenticação (cookies) que já pode estar presente para uma ou mais contas assinadas. Caso não exista nenhum estado de autenticação, autenticação durante autorização via MSAL resulta em estado de autenticação (cookies) criados em benefício de outras aplicações web que serão utilizadas no mesmo navegador.
- Novo modelo de exceção:
  - As exceções definem mais claramente o tipo de erro que ocorreu e o que o desenvolvedor precisa de fazer para o resolver.
- A MSAL suporta objetos de parâmetros para chamadas `AcquireToken` e `AcquireTokenSilent`.
- A MSAL suporta a configuração declarativa para:
  - ID do cliente, Redirecionamento URI.
  - Incorporado vs Navegador Padrão
  - Autoridades
  - Definições http tais como tempo de leitura e ligação

## <a name="your-app-registration-and-migration-to-msal"></a>Registo da sua aplicação e migração para mSAL

Não precisa de alterar o registo de aplicações existente para utilizar o MSAL. Se pretender usufruir de um consentimento incremental/progressivo, poderá ter de rever o registo para identificar os âmbitos específicos que pretende solicitar de forma incremental. Mais informações sobre os âmbitos e o consentimento incremental seguem-se.

No registo da sua aplicação no portal, verá um separador de **permissões API.** Isto fornece uma lista das APIs e permissões (âmbitos) a que a sua aplicação está configurada para solicitar acesso. Também mostra uma lista dos nomes de âmbito associados a cada permissão da API.

### <a name="user-consent"></a>Consentimento do utilizador

Com a ADAL e o ponto final da AAD v1, o consentimento do utilizador aos recursos que possuem foi concedido na primeira utilização. Com a MSAL e a plataforma de identidade da Microsoft, o consentimento pode ser solicitado incrementalmente. O consentimento incremental é útil para permissões que um utilizador pode considerar um privilégio elevado, ou pode questionar se não for fornecido com uma explicação clara do porquê da permissão ser necessária. No ADAL, essas permissões podem ter resultado no abandono da sessão do utilizador na sua aplicação.

> [!TIP]
> Recomendamos a utilização de consentimento incremental em cenários em que necessita de fornecer contexto adicional ao seu utilizador sobre o porquê da sua aplicação necessitar de uma permissão.

### <a name="admin-consent"></a>Consentimento do administrador

Os administradores da organização podem consentir em permissões que o seu pedido requer em nome de todos os membros da sua organização. Algumas organizações apenas permitem que os administradores consintam com os pedidos. O consentimento do Administrador requer que inclua todas as permissões e âmbitos da API utilizados pela sua aplicação no registo da sua aplicação.

> [!TIP]
> Mesmo que possa solicitar um âmbito usando o MSAL para algo que não esteja incluído no registo da sua aplicação, recomendamos que atualize o registo da sua aplicação para incluir todos os recursos e âmbitos a que um utilizador possa conceder permissão.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migração de IDs de recursos para âmbitos

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Autenticar e solicitar autorização para todas as permissões na primeira utilização

Se está a usar a ADAL e não precisa de usar o consentimento incremental, a forma mais simples de começar a usar o MSAL é fazer um pedido de `acquireToken` usando o novo objeto `AcquireTokenParameter` e definir o valor de ID do recurso.

> [!CAUTION]
> Não é possível definir ambos os âmbitos e uma identificação de recursos. Tentar definir ambos resultará numa `IllegalArgumentException`.

 Isto resultará no mesmo comportamento v1 que é usado. Todas as permissões solicitadas no registo da sua aplicação são solicitadas ao utilizador durante a sua primeira interação.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Autenticar e solicitar permissões apenas se necessário

Para tirar partido do consentimento incremental, faça uma lista de permissões (âmbitos) que a sua aplicação utiliza a partir do registo da sua aplicação, e organize-as em duas listas com base em:

- Quais os âmbitos que pretende solicitar durante a primeira interação do utilizador com a sua aplicação durante o início de sessão.
- As permissões que estão associadas a uma importante funcionalidade da sua aplicação que também terá de explicar ao utilizador.

Depois de organizar os âmbitos, organize cada lista pelo qual recurso (API) pretende solicitar um sinal. Assim como quaisquer outros âmbitos que deseje que o utilizador autorize ao mesmo tempo.

O objeto de parâmetros utilizado para fazer o seu pedido aos suportes MSAL:

- `Scope`: A lista de âmbitos que pretende solicitar autorização e receber um sinal de acesso.
- `ExtraScopesToConsent`: Uma lista adicional de âmbitos que pretende solicitar autorização enquanto solicita um sinal de acesso para outro recurso. Esta lista de âmbitos permite-lhe minimizar o número de vezes que necessita para solicitar a autorização do utilizador. O que significa menos autorização de utilizador ou pedidos de consentimento.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migrar da AutenticaçãoContexto para PublicClientApplications

### <a name="constructing-publicclientapplication"></a>Construção de PublicClientApplication

Quando se usa a MSAL, faz-se uma `PublicClientApplication`. Este objeto modela a sua identidade de aplicação e é usado para fazer pedidos a uma ou mais autoridades. Com este objeto irá configurar a identidade do seu cliente, redirecionar o URI, autoridade padrão, se utilizar o navegador do dispositivo vs. visualização web incorporada, o nível de registo, e muito mais.

Pode configurar este objeto de forma declarativa com a JSON, que fornece como ficheiro ou armazena como recurso dentro da sua APK.

Embora este objeto não seja um singleton, internamente utiliza `Executors` partilhadas para pedidos interativos e silenciosos.

### <a name="business-to-business"></a>Negócios para Negócios

Na ADAL, todas as organizações a quem solicitam fichas de acesso requerem uma instância separada da `AuthenticationContext`. Na MSAL, isto já não é um requisito. Pode especificar a autoridade a partir da qual pretende solicitar um sinal como parte do seu pedido silencioso ou interativo.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migrar da validação da autoridade para autoridades conhecidas

A MSAL não tem uma bandeira que permita ou desative a validação da autoridade. A validação da autoridade é uma funcionalidade no ADAL, e nas primeiras libertações do MSAL, que impede o seu código de solicitar fichas de uma autoridade potencialmente maliciosa. A MSAL agora recupera uma lista de autoridades conhecidas pela Microsoft e funde essa lista com as autoridades que especificou na sua configuração.

> [!TIP]
> Se é um utilizador do Azure Business to Consumer (B2C), isto significa que já não tem de desativar a validação da autoridade. Em vez disso, inclua cada uma das suas políticas de Azure AD B2C suportadas como autoridades na sua configuração MSAL.

Se tentar usar uma autoridade que não é conhecida da Microsoft, e não está incluída na sua configuração, terá um `UnknownAuthorityException`.

### <a name="logging"></a>Registo
Agora pode configurar declarativamente a exploração madeireira como parte da sua configuração, assim:

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>Migrar de UserInfo para Conta

Na ADAL, o `AuthenticationResult` fornece um `UserInfo` objeto usado para recuperar informações sobre a conta autenticada. O termo "utilizador", que significava um agente humano ou de software, foi aplicado de uma forma que dificultava a comunicação de que algumas aplicações suportam um único utilizador (seja um agente humano ou de software) que tem várias contas.

Considere uma conta bancária. Pode ter mais de uma conta em mais de uma instituição financeira. Ao abrir uma conta, você (o utilizador) são emitidas credenciais, como um cartão multibanco e PIN, que são usados para aceder ao seu saldo, pagamentos de conta, e assim por diante, para cada conta. Essas credenciais só podem ser utilizadas na instituição financeira que as emitiu.

Por analogia, como contas numa instituição financeira, as contas na plataforma de identidade da Microsoft são acedidas usando credenciais. Essas credenciais estão registadas ou emitidas pela Microsoft. Ou pela Microsoft em nome de uma organização.

Onde a plataforma identitária da Microsoft difere de uma instituição financeira, nesta analogia, é que a plataforma de identidade da Microsoft fornece um quadro que permite a um utilizador usar uma conta, e as suas credenciais associadas, para aceder a recursos que pertencem vários indivíduos e organizações. Isto é como poder usar um cartão emitido por um banco, em mais uma instituição financeira. Isto funciona porque todas as organizações em questão estão a usar a plataforma de identidade da Microsoft, que permite que uma conta seja usada em várias organizações. Segue-se um exemplo:

Sam trabalha para Contoso.com mas gere máquinas virtuais Azure que pertencem a Fabrikam.com. Para o Sam gerir as máquinas virtuais do Fabrikam, ele precisa de ser autorizado a aceder-lhes. Este acesso pode ser concedido adicionando a conta de Sam à Fabrikam.com, e concedendo à sua conta um papel que lhe permite trabalhar com as máquinas virtuais. Isto seria feito com o portal Azure.

Adicionar a conta Contoso.com de Sam como membro da Fabrikam.com resultaria na criação de um novo recorde no Azure Ative Directory de Fabrikam.com para Sam. O registo de Sam no Azure Ative Directory é conhecido como um objeto de utilizador. Neste caso, esse objeto de utilizador apontaria para o objeto de utilizador do Sam em Contoso.com. O objeto de utilizador fabrikam de Sam é a representação local de Sam, e seria usado para armazenar informações sobre a conta associada a Sam no contexto de Fabrikam.com. Em Contoso.com, o título de Sam é Consultor Sénior de DevOps. Em Fabrikam, o título de Sam é Empreiteiro-Virtual Machines. Em Contoso.com, Sam não é responsável, nem autorizado, a gerir máquinas virtuais. Em Fabrikam.com, é a sua única função profissional. No entanto, Sam ainda só tem um conjunto de credenciais para acompanhar, que são as credenciais emitidas por Contoso.com.

Uma vez feita uma chamada `acquireToken` bem sucedida, você verá uma referência a um objeto `IAccount` que pode ser usado em pedidos posteriores `acquireTokenSilent`.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Se tiver uma aplicação que aceda a reclamações sobre uma conta de cada um dos inquilinos em que a conta está representada, pode lançar objetos `IAccount` para `IMultiTenantAccount`. Esta interface fornece um mapa de `ITenantProfiles`, chave-chave por ID do arrendatário, que lhe permite aceder às reclamações que pertencem à conta em cada um dos inquilinos de quem solicitou um sinal, em relação à conta corrente.

As reivindicações na raiz do `IAccount` e `IMultiTenantAccount` contêm sempre as reivindicações do arrendatário. Se ainda não fez um pedido para um símbolo dentro do inquilino da casa, esta coleção estará vazia.

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

Na ADAL, há um tipo de exceção, `AuthenticationException`, que inclui um método para recuperar o valor `ADALError` enum.
Na MSAL, há uma hierarquia de exceções, e cada um tem o seu próprio conjunto de códigos de erro específicos associados.

Lista de Exceções à MSAL

|Exceção  | Descrição  |
|---------|---------|
| `MsalException`     | Exceção verificada por defeito lançada pela MSAL.  |
| `MsalClientException`     | Atirado se o erro for do lado do cliente. |
| `MsalArgumentException`     | Lançado se um ou mais argumentos de inputs forem inválidos. |
| `MsalClientException`     | Atirado se o erro for do lado do cliente. |
| `MsalServiceException`     | Lançado se o erro for do lado do servidor. |
| `MsalUserCancelException`     | Lançado se o utilizador cancelar o fluxo de autenticação.  |
| `MsalUiRequiredException`     | Jogado se o símbolo não puder ser refrescado silenciosamente.  |
| `MsalDeclinedScopeException`     | Lançado se um ou mais âmbitos solicitados fossem recusados pelo servidor.  |
| `MsalIntuneAppProtectionPolicyRequiredException` | Lançado se o recurso tiver uma política de proteção do MAMCA ativada. |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError para MsalException ErrorCode

### <a name="adal-logging-to-msal-logging"></a>Exploração madeireira ADAL à Exploração Madeireira MSAL

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
