---
title: Descrição geral de tokens - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre os tokens utilizados no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ac3c2132fc28d9813a9322898f79c7cdfffa12d7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64681899"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Descrição geral de tokens no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD) B2C emite vários tipos de tokens de segurança, à medida que processa cada [fluxo de autenticação](active-directory-b2c-apps.md). Este documento descreve o formato, características de segurança e conteúdo de cada tipo de token.

## <a name="token-types"></a>Tipos de token

O Azure AD B2C suporta o [protocolos OAuth 2.0 e OpenID Connect](active-directory-b2c-reference-protocols.md), que faz uso de tokens para autenticação e acesso seguro aos recursos. Todos os tokens utilizados no Azure AD B2C são [tokens de web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) que contêm declarações de informações sobre o portador e o assunto do token.

Os tokens seguintes são utilizados na comunicação com o Azure AD B2C:

- *Token de ID* -um JWT que contém as afirmações que pode utilizar para identificar os utilizadores na sua aplicação. Este token é enviada de forma segura em pedidos de HTTP para a comunicação entre dois componentes do mesmo aplicativo ou serviço. Pode utilizar as declarações no token de ID como quiser. Elas são comumente usadas para exibir informações de conta ou para tomar decisões de controlo de acesso num aplicativo. Tokens de ID tem sessão iniciadas, mas não estão encriptados. Quando a sua aplicação ou a API recebe um token de ID, ele deve validar a assinatura para provar que o token é autêntico. Também deve validar a sua aplicação ou API algumas declarações no token para provar que é válido. Consoante os requisitos de cenário, as afirmações validadas por um aplicativo podem variar, mas o aplicativo deve executar algumas validações de afirmação comuns em todos os cenários.
- *Token de acesso* -um JWT que contém as afirmações que pode utilizar para identificar as permissões concedidas às suas APIs. Tokens de acesso tem sessão iniciados, mas eles não estão encriptados. Tokens de acesso são utilizados para fornecer acesso aos servidores APIs e recursos.  Quando a API recebe um token de acesso, ele deve validar a assinatura para provar que o token é autêntico. A API também deve validar algumas declarações no token para provar que é válido. Consoante os requisitos de cenário, as afirmações validadas por um aplicativo podem variar, mas o aplicativo deve executar algumas validações de afirmação comuns em todos os cenários.
- *Atualizar token* -tokens de atualização são utilizados para adquirir novos tokens de ID e tokens num fluxo de OAuth 2.0 de acesso. Eles fornecem a sua aplicação com acesso de longo prazo para recursos em nome dos utilizadores sem a necessidade de interação com esses utilizadores. Tokens de atualização são opacos para seu aplicativo. São emitidos pelo Azure AD B2C e podem ser inspecionadas e interpretados apenas pelo Azure AD B2C. Eles são vida útil longa, mas seu aplicativo não deve ser escrito com a expectativa de que um token de atualização terá duração de um período de tempo específico. Tokens de atualização podem ser invalidadas a qualquer momento para uma variedade de motivos. A única forma para a sua aplicação saber se um token de atualização é válido é tentar resgatá-lo ao fazer um pedido de token do Azure AD B2C. Quando utilizar um token de atualização para um novo token, receberá um novo token de atualização na resposta de token. Guarde o novo token de atualização. Ele substitui o token de atualização que utilizou anteriormente no pedido. Esta ação ajuda a garantir que seus tokens de atualização mantêm-se válidas durante mais tempo. 

## <a name="endpoints"></a>Pontos Finais

R [registado aplicação](tutorial-register-applications.md) recebe tokens e comunica com o Azure AD B2C ao enviar pedidos para estes pontos finais:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Tokens de segurança que seu aplicativo receba do Azure AD B2C podem ser provenientes da `/authorize` ou `/token` pontos de extremidade. Quando os tokens de ID são adquiridos do `/authorize` ponto de extremidade, eles são efetuados utilizando o [fluxo implícito](active-directory-b2c-reference-spa.md), que é utilizado frequentemente para os utilizadores a iniciar sessão para aplicativos web baseados em javascript. Quando os tokens de ID são adquiridos do `/token` ponto de extremidade, eles são efetuados utilizando o [fluxo de código confidenciais](active-directory-b2c-reference-oidc.md), que mantém o token ocultado a partir do browser.

## <a name="claims"></a>Afirmações

Quando utilizar o Azure AD B2C, terá um controle refinado sobre o conteúdo dos seus tokens. Pode configurar [fluxos de utilizador](active-directory-b2c-reference-policies.md) e [políticas personalizadas](active-directory-b2c-overview-custom.md) para enviar determinados conjuntos de dados de utilizador em afirmações que são necessárias para a sua aplicação. Essas declarações podem incluir propriedades padrão, como **displayName** e **emailAddress**. Seus aplicativos podem usar essas declarações de forma a autenticar os utilizadores e pedidos. 

As afirmações nos tokens de ID não são devolvidas em qualquer ordem específica. Novas declarações podem ser introduzidas nos tokens de ID em qualquer altura. Seu aplicativo não deve interromper conforme novas declarações são introduzidas. Também pode incluir [atributos de utilizador personalizada](active-directory-b2c-reference-custom-attr.md) em suas declarações.

A tabela seguinte lista as afirmações que pode esperar nos tokens de identidade e acesso tokens emitidos pelo Azure AD B2C.

| Name | Afirmação | Valor de exemplo | Descrição |
| ---- | ----- | ------------- | ----------- |
| Audiência | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica o destinatário do token. Para o Azure AD B2C, o público-alvo é a ID da aplicação. Seu aplicativo deve validar este valor e rejeitar o token, caso não seja correspondente. Público-alvo é sinônimo de recursos. |
| Emissor | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica o serviço de token de segurança (STS) que constrói e devolve o token. Ele também identifica o diretório em que o utilizador foi autenticado. Seu aplicativo deve validar a afirmação do emissor para se certificar de que o token provém o ponto final adequado. |
| Emitida em | `iat` | `1438535543` | O tempo em que o token foi emitido, representado na hora "Epoch". |
| Hora de expiração | `exp` | `1438539443` | O tempo em que o token se torna inválido, representado na hora "Epoch". A aplicação deve utilizar esta afirmação para verificar a validade da duração do token. |
| Não antes | `nbf` | `1438535543` | A hora em que o token se torna válido, representado na hora "Epoch". Desta vez, normalmente, é o mesmo que o tempo que o token foi emitido. A aplicação deve utilizar esta afirmação para verificar a validade da duração do token. |
| Version | `ver` | `1.0` | A versão do token de ID, conforme definido pelo Azure AD B2C. |
| Hash de código | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de código incluído num token de ID apenas quando o token for emitido, juntamente com um código de autorização de OAuth 2.0. Um hash de código pode ser utilizado para validar a autenticidade de um código de autorização. Para obter mais informações sobre como efetuar esta validação, consulte a [especificação do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hash de token de acesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Hash de token de acesso incluído num token de ID apenas quando o token for emitido, juntamente com um token de acesso de OAuth 2.0. Um hash de token de acesso pode ser utilizado para validar a autenticidade de um token de acesso. Para obter mais informações sobre como efetuar esta validação, consulte o [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Valor de uso único | `nonce` | `12345` | Um valor de uso único é uma estratégia utilizada para mitigar ataques de repetição de token. A aplicação, pode especificar um valor de uso único numa solicitação de autorização utilizando a `nonce` parâmetro de consulta. O valor que fornecer no pedido é emitido sem modificações no `nonce` de afirmação de apenas um token de ID. Esta afirmação permite que a aplicação para verificar o valor com o valor especificado no pedido. Seu aplicativo deve executar essa validação durante o processo de validação do token de ID. |
| Subject | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | O principal sobre o qual o token declara informações, como o utilizador de um aplicativo. Este valor é imutável e não pode ser reatribuído ou reutilizado. Ele pode ser usado para realizar verificações de autorização com segurança, por exemplo, quando o token é utilizado para aceder a um recurso. Por predefinição, a afirmação do requerente é preenchida com o ID de objeto do utilizador no diretório. |
| Referência de classe de contexto de autenticação | `acr` | Não aplicável | Utilizado apenas com as políticas mais antigas. |
| Política de estrutura de confiança | `tfp` | `b2c_1_signupsignin1` | O nome da política que foi utilizado para adquirir o token de ID. |
| Tempo de autenticação | `auth_time` | `1438535543` | O tempo em que um usuário inserir, pela última vez, credenciais, representado na hora "Epoch". |
| Âmbito | `scp` | `Read`| As permissões concedidas para o recurso para um token de acesso. Permissões concedidas múltiplos são separadas por um espaço. |
| Terceiros autorizados | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | O **ID da aplicação** da aplicação cliente que a solicitação. |

## <a name="configuration"></a>Configuração

As seguintes propriedades são utilizadas para [gerir durações de tokens de segurança](configure-tokens.md) emitido pelo Azure AD B2C:

- **Duração (minutos) de token de acesso & ID** – o tempo de vida do token de portador OAuth 2.0 utilizado para obter acesso a um recurso protegido. A predefinição é 60 minutos. O mínimo (inclusive) é de 5 minutos. O máximo (inclusive) é 1440 minutos.

- **Duração do token atualizado (dias)** – o período de tempo máximo antes do qual um token de atualização pode ser utilizado para adquirir um token de ID ou o acesso de novo. O período de tempo também aborda de que adquirir um novo token de atualização, se seu aplicativo tiver sido concedido a `offline_access` âmbito. A predefinição é 14 dias. O mínimo (inclusive) é um dia. O máximo (inclusive) é de 90 dias.

- **Duração janela deslizante do token atualizado (dias)** – após este tempo período decorrer o utilizador é forçado a autenticar, independentemente do período de validade mais recente do token atualizado adquirido pela aplicação. Só pode ser fornecido se o parâmetro estiver definido como **limitado**. Tem de ser maior ou igual a **duração do token de atualização (dias)** valor. Se o parâmetro estiver definido como **Unbounded**, não é possível fornecer um valor específico. A predefinição é 90 dias. O mínimo (inclusive) é um dia. O máximo (inclusive) é a 365 dias.

Os seguintes casos de utilização estão ativados com estas propriedades:

- Permitir que um utilizador manter sessão iniciada para um aplicativo móvel indefinidamente, desde que o utilizador está continuamente ativo no aplicativo. Pode definir **atualização token duração da janela deslizante (dias)** ao **Unbounded** no seu fluxo de início de sessão do utilizador.
- Cumprir os requisitos de conformidade de segurança de seu setor e definindo as durações de token de acesso adequado.

Estas definições não estão disponíveis para fluxos de utilizador de reposição de palavra-passe. 

## <a name="compatibility"></a>Compatibilidade

As seguintes propriedades são utilizadas para [gerenciar a compatibilidade de token](configure-tokens.md):

- **Afirmação do emissor (iss)** -esta propriedade identifica o inquilino do Azure AD B2C que emitiu o token. O valor predefinido é `https://<domain>/{B2C tenant GUID}/v2.0/`. O valor de `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` inclui IDs de inquilino do Azure AD B2C e o fluxo de utilizador que foi utilizado no pedido de token. Se a sua aplicação ou a biblioteca tem o Azure AD B2C para estar em conformidade com o [especificação do OpenID Connect 1.0 da deteção](https://openid.net/specs/openid-connect-discovery-1_0.html), utilize este valor.

- **Afirmação do requerente** -esta propriedade identifica a entidade para o qual o token declara informações. O valor predefinido é **ObjectID**, que preenche a `sub` de afirmações no token com o ID de objeto do utilizador. O valor de **nepodporuje** é fornecido apenas para fins de compatibilidade. Recomenda-se que mude para **ObjectID** assim que possível.

- **Que representa a ID de política de afirmações** -esta propriedade identifica o tipo de afirmação na qual o nome de política utilizado no pedido de token é preenchido. O valor predefinido é `tfp`. O valor de `acr` é fornecido apenas para fins de compatibilidade.

## <a name="pass-through"></a>Pass-through

Quando é iniciado um percurso do utilizador, o Azure AD B2C recebe um token de acesso de um fornecedor de identidade. O Azure AD B2C utiliza esse token para obter informações sobre o utilizador. [Ative uma afirmação no seu fluxo de utilizador](idp-pass-through-user-flow.md) ou [definir uma afirmação na sua política personalizada](idp-pass-through-custom.md) para transmitir o token por meio para os aplicativos que Registre-se no Azure AD B2C. A aplicação tem de utilizar um [fluxo de utilizador do v2](user-flow-versions.md) para tirar partido das passando o token como uma afirmação.

O Azure AD B2C atualmente suporta apenas passando o token de acesso de fornecedores de identidade do OAuth 2.0, que incluem o Facebook e Google. Para todos os outros fornecedores de identidade, a afirmação é devolvida em branco. 

## <a name="validation"></a>Validação

Para validar um token, o aplicativo deve verificar a assinatura e de declarações do token. Muitas bibliotecas de código-fonte aberto estão disponíveis para validar JWTs, dependendo do seu idioma preferencial. Recomenda-se que explorar essas opções em vez de implementar sua própria lógica de validação.

### <a name="validate-signature"></a>Validar a assinatura

Um JWT contém três segmentos, um *cabeçalho*, uma *corpo*e um *assinatura*. O segmento de assinatura pode ser utilizado para validar a autenticidade do token, de modo a que pode ser considerado fidedigno pelo seu aplicativo. O Azure AD B2C tokens são assinados com algoritmos de criptografia assimétrica de norma da indústria, como RSA 256. 

O cabeçalho do token contém informações sobre o método de encriptação e a chave utilizada para assinar o token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

O valor do **alg** afirmação é o algoritmo que foi utilizado para assinar o token. O valor do **criança** afirmação é a chave pública que foi utilizada para assinar o token. Em qualquer momento, do Azure AD B2C podem iniciar sessão um token ao utilizar qualquer um de um conjunto de pares de chaves públicas-privadas. O Azure AD B2C gira o conjunto possível de chaves periodicamente. Seu aplicativo deve ser escrito para lidar com essas alterações principais automaticamente. Uma frequência razoável para procurar atualizações para as chaves públicas usadas pelo Azure AD B2C é a cada 24 horas.

O Azure AD B2C tem um ponto final de metadados OpenID Connect. Utilizar este ponto final, as aplicações podem solicitar informações sobre o Azure AD B2C no tempo de execução. Estas informações incluem pontos finais, conteúdo de token e chaves de assinatura de tokens. O seu inquilino do Azure AD B2C contém um documento de metadados JSON para cada política. O documento de metadados é um objeto JSON que contém várias partes úteis de informações. Contém os metadados **jwks_uri**, que dá a localização do conjunto de chaves públicas que são utilizados para assinar os tokens. Que a localização é fornecida aqui, mas ele do melhor ao obter a localização dinamicamente usando o documento de metadados e de análise **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
O documento JSON localizado neste URL contém todas as informações de chaves públicas em uso num momento específico. A aplicação pode utilizar o `kid` no cabeçalho do JWT para selecionar a chave pública no documento JSON que é utilizado para assinar um determinado token de afirmação. Em seguida, ele pode realizar validação da assinatura usando a chave pública correta e o algoritmo indicado.

O documento de metadados para o `B2C_1_signupsignin1` política no `contoso.onmicrosoft.com` inquilino está localizado em:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Para determinar qual a política foi utilizada para assinar um token (e onde ir para os metadados do pedido), tem duas opções. Em primeiro lugar, o nome da política está incluído no `acr` de afirmações no token. Pode analisar afirmações fora do corpo do JWT por base-64 decodificar o corpo e a cadeia de caracteres do JSON que resulta ao anular a serialização. O `acr` afirmação é o nome da política que foi utilizado para emitir o token. A outra opção é codificar a política no valor do `state` parâmetro quando emitir o pedido e, em seguida, decodificá-la para determinar qual a política foi utilizada. Qualquer um dos métodos é válido.

Uma descrição de como realizar a validação de assinatura está fora do escopo deste documento. Muitas bibliotecas de código-fonte aberto estão disponíveis para o ajudar a validar um token.

### <a name="validate-claims"></a>Validar as reclamações

Quando seus aplicativos ou a API recebe um token de ID, ele também deve executar diversas verificações contra as afirmações no token de ID. As seguintes declarações devem ser verificadas:

- **público-alvo** -verifica se o token de ID destinava-se a ser dado ao seu aplicativo.
- **não antes** e **hora de expiração** -verifica se o token de ID se ainda não tiver expirado.
- **emissor** -verifica se o token foi emitido para seu aplicativo pelo Azure AD B2C.
- **valor de uso único** -uma estratégia de mitigação de ataques de repetição de token.

Para obter uma lista completa de validações seu aplicativo deve executar, consulte a [especificação do OpenID Connect](https://openid.net).  

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como [utilizar tokens de acesso](active-directory-b2c-access-tokens.md).

