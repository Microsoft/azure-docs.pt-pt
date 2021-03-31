---
title: Erros e exceções (MSAL Android) | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, Acesso Condicional e reclama desafios em aplicações MSAL Android.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: 4185206e92a78c2684ba1690f03700ef2532cc5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761385"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>Lidar com exceções e erros no MSAL para Android

As exceções na Biblioteca de Autenticação da Microsoft (MSAL) destinam-se a ajudar os desenvolvedores de aplicações a resolver em dificuldades a sua aplicação. As mensagens de exceção não são localizadas.

Ao processar exceções e erros, pode utilizar o próprio tipo de exceção e o código de erro para distinguir entre exceções.  Para obter uma lista de códigos de erro, consulte [códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md).

Durante a experiência de entrada, poderá encontrar erros sobre consentimentos, acesso condicional (MFA, Gestão de Dispositivos, restrições baseadas na localização), emissão e resgate de fichas e propriedades do utilizador.


|Classe de erro | Cadeia de causa/erro| Como lidar |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`: O token de atualização utilizado para resgatar o token de acesso é inválido, expirado ou revogado. Esta exceção pode ser devido a uma mudança de senha. </li><li>`NO_TOKENS_FOUND`: O token de acesso não existe e não se pode encontrar nenhum token de atualização para resgatar o token de acesso.</li> <li>Passo necessário<ul><li>MFA</li><li>Reclamações em falta</li></ul></li><li>Bloqueado por Acesso Condicional (por exemplo, instalação [de corretor de autenticação](./msal-android-single-sign-on.md) necessária)</li><li>`NO_ACCOUNT_FOUND`: Não há conta disponível na cache para autenticação silenciosa.</li></ul> |Ligue `acquireToken()` para solicitar ao utilizador que introduza o seu nome de utilizador e senha e, possivelmente, consentir e efetuar a autenticação de vários fatores.|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: O utilizador ou o servidor não aceitaram todos os âmbitos. O servidor pode recusar um âmbito se o âmbito solicitado não for suportado, não reconhecido ou não suportado por uma determinada conta. </li></ul>| O desenvolvedor deve decidir se continua a autenticação com os âmbitos concedidos ou se termina o processo de autenticação. Opção de reenviar o pedido de aquisição apenas para os âmbitos concedidos e fornecer pistas para as quais foram concedidas permissões através da passagem `silentParametersForGrantedScopes` e da `acquireTokenSilent` chamada. |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`: Este pedido não tem um parâmetro necessário, inclui um parâmetro inválido, inclui um parâmetro mais de uma vez, ou está mal formado. </li><li>`SERVICE_NOT_AVAILABLE`: Representa códigos de erro 500/503/506 devido à avaria do serviço. </li><li>`UNAUTHORIZED_REQUEST`: O cliente não está autorizado a solicitar um código de autorização.</li><li>`ACCESS_DENIED`: O titular do recurso ou o servidor de autorização negaram o pedido.</li><li>`INVALID_INSTANCE`: `AuthorityMetadata` validação falhada</li><li>`UNKNOWN_ERROR`: O pedido ao servidor falhou, mas não houve erro e `error_description` é devolvido do serviço.</li><ul>| Esta classe de exceção representa erros ao comunicar com o serviço, pode ser dos pontos finais autorizados ou simbólicos. A MSAL lê o erro e error_description a partir da resposta do servidor. Geralmente, estes erros são resolvidos através da fixação de configurações de aplicações, quer em código quer no portal de registo de aplicações. Raramente uma falha de serviço pode desencadear este aviso, que só pode ser atenuado esperando que o serviço recupere.  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`: Encontram-se múltiplas entradas de cache e o sdk não consegue identificar o acesso correto ou a atualização da ficha da cache. Esta exceção geralmente indica um erro no sdk para armazenar fichas ou que a autoridade não é fornecida no pedido silencioso e são encontradas várias fichas correspondentes. </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: Não existe rede ativa disponível no aparelho. </li><li>`JSON_PARSE_FAILURE`: O sdk não analisou o formato JSON.</li><li>`IO_ERROR`: `IOException` aconteceu, pode ser um erro de dispositivo ou de rede. </li><li>`MALFORMED_URL`: A url está mal formada. Provavelmente causado ao construir o pedido de auth, autoridade ou redirecionar uri. </li><li>`UNSUPPORTED_ENCODING`: A codificação não é suportada pelo aparelho. </li><li>`NO_SUCH_ALGORITHM`: O algoritmo utilizado para gerar o desafio [PKCE](https://tools.ietf.org/html/rfc7636) não é suportado. </li><li>`INVALID_JWT`: `JWT` devolvido pelo servidor não é válido ou está vazio ou mal formado. </li><li>`STATE_MISMATCH`: O Estado de autorização não corresponde ao Estado no pedido de autorização. Para pedidos de autorização, o SDK verificará o estado devolvido do redirecionamento e o enviado no pedido. </li><li>`UNSUPPORTED_URL`: Url não suportado, não pode realizar validação de autoridade ADFS. </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: A autoridade não é apoiada para validação de autoridade. O SDK apoia as autoridades B2C, mas não apoia a validação da autoridade B2C. Só o anfitrião bem conhecido será apoiado. </li><li>`CHROME_NOT_INSTALLED`: O Chrome não está instalado no aparelho. O Sdk utiliza o separador chrome personalizado para pedidos de autorização, caso esteja disponível, e irá recorrer ao navegador Chrome. </li><li>`USER_MISMATCH`: O utilizador fornecido no pedido de ficha de aquisição não corresponde ao utilizador devolvido do servidor.</li></ul>|Esta classe de exceção representa erros gerais que são locais para a biblioteca. Estas exceções podem ser tratadas corrigindo o pedido.|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: O utilizador iniciou o fluxo interativo e antes de receber fichas de volta cancelaram o pedido. </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`: A autoridade deve ser especificada para `acquireTokenSilent` .</li></ul>|Estes erros podem ser atenuados pelo desenvolvedor corrigindo argumentos e garantindo atividade para auth interativo, callback de conclusão, âmbitos e uma conta com um ID válido foram fornecidos.|


## <a name="catching-errors"></a>Erros de captura

O seguinte corte de código mostra um exemplo de erros de captura no caso de `acquireToken` chamadas silenciosas.

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [iniciar sessão no MSAL para Android.](msal-logging-android.md)