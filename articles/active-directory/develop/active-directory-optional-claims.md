---
title: Saiba como fornecer afirmações opcionais para a sua aplicação do Azure AD | Documentos da Microsoft
description: Um guia para adicionar afirmações personalizadas ou adicionais para os tokens SAML 2.0 e JSON Web Tokens (JWT) emitidos pelo Azure Active Directory.
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2019
ms.author: celested
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 253a5e247dbbea5fc7e0e556d8619328b43bff58
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59501064"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Como: Fornecer afirmações opcionais para a sua aplicação do Azure AD

Esta funcionalidade é utilizada por desenvolvedores de aplicativos para especificar quais as afirmações que desejam nos tokens enviados para a respetiva aplicação. Pode utilizar afirmações opcionais aos:

- Selecione afirmações adicionais para incluir nos tokens para a sua aplicação.
- Altere o comportamento de determinadas afirmações que retorna do Azure AD nos tokens.
- Adicionar e acessar as declarações personalizadas para a sua aplicação.

Para as listas de declarações padrão, consulte a [token de acesso](access-tokens.md) e [id_token](id-tokens.md) documentação de afirmações. 

Embora as afirmações opcionais são suportadas na v1.0 e v2.0 tokens de formato, os e SAML tokens, fornecem a maioria dos seus valores quando mudar de v1.0 para a versão 2.0. Um dos objetivos do [ponto final v2.0 do Azure AD](active-directory-appmodel-v2-overview.md) é tamanhos menores de token para garantir um desempenho ideal pelos clientes. Como resultado, várias afirmações incluídas anteriormente no acesso e tokens de ID já não estão presentes nos tokens de v2.0 e devem ser-lhe pedidas para especificamente numa base por aplicação.

**Tabela 1: Aplicabilidade**

| Tipo de Conta | Tokens v1.0 | Tokens v2.0  |
|--------------|---------------|----------------|
| Conta Microsoft pessoal  | N/A  | Suportadas|
| Conta do Azure AD      | Suportadas | Suportadas |

## <a name="v10-and-v20-optional-claims-set"></a>Definir v1.0 e V2.0 de afirmações opcionais

O conjunto de afirmações opcionais disponíveis por predefinição para as aplicações a utilizar estão listados abaixo. Para adicionar afirmações opcionais personalizadas para a sua aplicação, consulte [extensões de diretório](#configuring-directory-extension-optional-claims), abaixo. Ao adicionar afirmações para o **token de acesso**, isto vai aplicar aos tokens de acesso solicitados *para* a aplicação (uma API web), não as *por* o aplicativo. Isto garante que, independentemente do cliente aceder à sua API, os dados certos estão presentes no token de acesso que eles usam para autenticar a sua API.

> [!NOTE]
> A maioria dessas declarações pode ser incluída numa JWTs para v1.0 e v2.0 tokens, mas não os tokens SAML, exceto em que anotou na coluna de tipo de Token. As contas de consumidor suportam um subconjunto dessas declarações, marcado na coluna "Tipo de usuário".  Muitas das afirmações listadas não se aplicam aos utilizadores de consumidor (não tem nenhum inquilino, por isso `tenant_ctry` não tem nenhum valor).  

**Tabela 2: Conjunto de afirmações v1.0 e V2.0 opcional**

| Name                       |  Descrição   | Tipo de token | Tipo de Utilizador | Notas  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Hora quando o usuário autenticado pela última vez. A especificação de OpenID Connect consulte.| JWT        |           |  |
| `tenant_region_scope`      | Região do inquilino de recursos | JWT        |           | |
| `home_oid`                 | Para os utilizadores convidados, o ID de objeto do utilizador no inquilino principal do utilizador.| JWT        |           | |
| `sid`                      | ID de sessão utilizado para a fim de sessão utilizador por sessão. | JWT        |           |         |
| `platf`                    | Plataforma de dispositivos    | JWT        |           | Restringido aos dispositivos geridos que podem verificar o tipo de dispositivo.|
| `verified_primary_email`   | Origem PrimaryAuthoritativeEmail do utilizador      | JWT        |           |         |
| `verified_secondary_email` | Origem SecondaryAuthoritativeEmail do utilizador   | JWT        |           |        |
| `enfpolids`                | IDs de política imposta. Uma lista da política de IDs que foram consideradas para o utilizador atual. | JWT |  |  |
| `vnet`                     | Informações de especificador VNET. | JWT        |           |      |
| `fwd`                      | Endereço IP.| JWT    |   | Adiciona o endereço IPv4 original do cliente solicitante (quando dentro de uma VNET) |
| `ctry`                     | País do usuário | JWT |  | O Azure AD devolve o `ctry` afirmação opcional se estiver presente e o valor da afirmação é um código de país de duas letras padrão, como FR, JP, SZ e assim por diante. |
| `tenant_ctry`              | País do inquilino de recursos | JWT | | |
| `xms_pdl`          | Localização de dados preferencial   | JWT | | Geo de múltiplos inquilinos, este é o código de 3 letras que mostra a região geográfica, que o utilizador está no. Para obter mais informações, consulte a [do Azure AD Connect documentação sobre a localização de dados preferida](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Por exemplo: `APC` para a Ásia-Pacífico. |
| `xms_pl`                   | Idioma preferido do utilizador  | JWT ||O usuário do idioma preferencial, se definir. Origem do seu inquilino principal, em cenários de acesso de convidado. Formatado LL CC ("en-us"). |
| `xms_tpl`                  | Idioma preferencial do inquilino| JWT | | O inquilino de recursos do idioma preferencial, se definir. Formatado LL ("en"). |
| `ztdid`                    | Implantação zero-touch ID | JWT | | A identidade de dispositivo utilizada para [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | O e-mail endereçável para este utilizador, se o utilizador tiver um.  | JWT, SAML | MSA, AAD | Este valor está incluído por predefinição, se o utilizador é um convidado no inquilino.  Para os utilizadores geridos (aquelas dentro do inquilino), este deve ser solicitada por meio desta afirmação opcional ou, na versão 2.0 apenas, com o âmbito OpenID.  Para os utilizadores geridos, o endereço de e-mail tem de ser definido [portal de administração do Office](https://portal.office.com/adminportal/home#/users).|  
| `acct`             | Estado da conta de utilizadores no inquilino. | JWT, SAML | | Se o utilizador é membro do inquilino, o valor é `0`. Se forem um convidado, o valor é `1`. |
| `upn`                      | Declaração de UserPrincipalName. | JWT, SAML  |           | Embora esta afirmação é automaticamente incluída, pode especificá-lo como uma afirmação opcional para anexar propriedades adicionais para modificar seu comportamento no caso de utilizador convidado.  |

### <a name="v20-optional-claims"></a>Afirmações opcionais de v2.0

Essas declarações são sempre incluídas na v1.0 tokens de AD do Azure, mas não incluídas nos tokens de versão 2.0, a menos que o pedido. Essas declarações apenas são aplicáveis para JWTs (tokens de ID e Tokens de acesso). 

**Tabela 3: Só de v2.0 afirmações opcionais**

| Afirmação do JWT     | Name                            | Descrição                                | Notas |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Endereço IP                      | O endereço IP, o sessão iniciado a partir de cliente.   |       |
| `onprem_sid`  | Identificador de Segurança no Local |                                             |       |
| `pwd_exp`     | Hora de expiração de palavra-passe        | A datetime em que a palavra-passe expira. |       |
| `pwd_url`     | Alterar o URL da palavra-passe             | Um URL que o utilizador pode visitar para alterar a palavra-passe.   |   |
| `in_corp`     | Rede empresarial interior        | Sinais, se o cliente está a iniciar sessão da rede empresarial. Se não estiverem, a afirmação não está incluída.   |  Com a base dos [IPs fidedignos](../authentication/howto-mfa-mfasettings.md#trusted-ips) as definições de MFA.    |
| `nickname`    | Alcunha                        | Um nome adicional para o utilizador, separado do primeiro ou último nome. | 
| `family_name` | Apelido                       | Fornece o último nome, sobrenome ou nome de família do utilizador, conforme definido no objeto user. <br>"family_name": "Santos" | Suportado no AAD e MSA   |
| `given_name`  | Nome próprio                      | Fornece a primeira ou "fixados" nome do utilizador, conforme definido no objeto user.<br>"given_name": "Frank"                   | Suportado no AAD e MSA  |
| `upn`         | Nome Principal de Utilizador | Um identificador para o utilizador que pode ser utilizado com o parâmetro username_hint.  Não é um identificador duradouro para o utilizador e não deve ser usado para dados de chave. | Ver [propriedades adicionais](#additional-properties-of-optional-claims) abaixo para a configuração da afirmação. |
| `sid`         | ID de sessão                      | Identificador de sessão do GUID, utilizada para controlar a sessão de autenticação com MSA. | MSA.  Não será incluído para contas do Azure AD. | 


### <a name="additional-properties-of-optional-claims"></a>Propriedades adicionais de afirmações opcionais

Algumas afirmações opcionais podem ser configuradas para alterar a forma como a afirmação é devolvida. Estas propriedades adicionais são usadas para ajudar a migração das aplicações no local com as expectativas de dados diferentes (por exemplo, `include_externally_authenticated_upn_without_hash` ajuda com os clientes que não consegue processar marcas de hash (`#`) o UPN)

**Tabela 4: Valores para configurar afirmações opcionais**

| Nome da propriedade  | Nome de propriedade adicionais | Descrição |
|----------------|--------------------------|-------------|
| `upn`          |                          | Pode ser utilizado para as respostas SAML e JWT e para tokens v1.0 e v2.0. |
|                | `include_externally_authenticated_upn`  | Inclui o UPN, conforme armazenado no inquilino de recursos de convidado. Por exemplo, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Mesmo que acima, exceto que marca o hash (`#`) são substituídos por carateres de sublinhado (`_`), por exemplo `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Exemplo de propriedades adicionais

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

Este objeto OptionalClaims faz com que o token de ID devolvido ao cliente para incluir o upn de outro com o inquilino principal adicional e informações de inquilino de recursos. Isso só será alterado o `upn` afirmações no token, se o utilizador é um convidado no inquilino (que utiliza um IDP diferente para autenticação). 

## <a name="configuring-optional-claims"></a>Configurar afirmações opcionais

Pode configurar afirmações opcionais para a sua aplicação ao modificar o manifesto do aplicativo (consulte o exemplo abaixo). Para obter mais informações, consulte a [Noções básicas sobre o artigo de manifesto de aplicação do Azure AD](reference-app-manifest.md).

**Esquema de exemplo:**

```json
"optionalClaims":  
   {
       "idToken": [
             { 
                   "name": "auth_time", 
                   "essential": false
              }
        ],
 "accessToken": [ 
             {
                    "name": "ipaddr", 
                    "essential": false
              }
        ],
"saml2Token": [ 
              { 
                    "name": "upn", 
                    "essential": false
               },
               { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": false
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>Tipo de OptionalClaims

Declara as afirmações opcionais solicitadas por um aplicativo. Um aplicativo pode configurar afirmações opcionais a serem retornados em cada um dos três tipos de tokens (ID token, token, 2 de SAML token de acesso) pode receber do serviço de token de segurança. O aplicativo pode configurar um conjunto diferente de afirmações opcionais a serem retornados em cada tipo de token. A propriedade OptionalClaims da entidade de aplicativo é um objeto de OptionalClaims.

**Tabela 5: Propriedades do tipo de OptionalClaims**

| Name        | Tipo                       | Descrição                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Coleção (OptionalClaim) | As afirmações opcionais devolvidas no token JWT ID. |
| `accessToken` | Coleção (OptionalClaim) | As afirmações opcionais devolvido no token de acesso JWT. |
| `saml2Token`  | Coleção (OptionalClaim) | As afirmações opcionais devolvido no SAML token.   |

### <a name="optionalclaim-type"></a>Tipo de OptionalClaim

Contém uma afirmação opcional associada a uma aplicação ou um principal de serviço. As propriedades idToken accessToken e saml2Token do [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) tipo é uma coleção de OptionalClaim.
Se for suportado por uma declaração específica, também pode modificar o comportamento de OptionalClaim usando o campo de AdditionalProperties.

**Tabela 6: Propriedades do tipo de OptionalClaim**

| Name                 | Tipo                    | Descrição                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | O nome da afirmação opcional.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | A origem (objeto de diretório) da afirmação. Existem afirmações predefinidas e declarações definidas pelo utilizador a partir das propriedades de extensão. Se o valor de origem for nulo, a afirmação é uma afirmação opcional predefinida. Se o valor de origem for utilizador, o valor na propriedade nome é a propriedade de extensão do objeto de utilizador. |
| `essential`            | Edm.Boolean             | Se o valor for VERDADEIRO, a afirmação especificada pelo cliente é necessária para garantir uma experiência de autorização suave para a tarefa específica solicitada pelo utilizador final. O valor predefinido é false.                                                                                                             |
| `additionalProperties` | Coleção (EDM) | Propriedades adicionais da afirmação. Se existir uma propriedade nesta coleção, ele modifica o comportamento da afirmação opcional especificado na propriedade nome.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Configurar afirmações opcionais de extensão de diretório

Além do conjunto de afirmações opcionais standard, também pode configurar tokens para incluir as extensões de esquema do diretório. Para mais informações, veja [extensões de esquema do](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Esta funcionalidade é útil para anexar informações de utilizador adicionais que pode utilizar a aplicação – por exemplo, um identificador adicional ou a opção de configuração importantes que o usuário tiver definido. 

> [!Note]
> Extensões de esquema de diretório são um recurso exclusivo de AAD, portanto, se seu aplicativo manifesto pedidos de uma extensão personalizada e um utilizador MSA registos na sua aplicação, estas extensões não vão ser devolvidas. 

### <a name="directory-extension-formatting"></a>Formatação de extensão de diretório

Para atributos de extensão, utilize o nome completo da extensão (no formato: `extension_<appid>_<attributename>`) no manifesto do aplicativo. O `<appid>` tem de corresponder ao ID do aplicativo que solicita a afirmação. 

Dentro do JWT, essas declarações serão emitidas com o seguinte formato de nome: `extn.<attributename>`.

Dentro de SAML tokens, essas declarações serão emitidas com o formato URI seguinte: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="optional-claims-example"></a>Exemplo de afirmações opcionais

Nesta secção, pode examinar um cenário para ver como pode utilizar a funcionalidade de afirmações opcionais para a sua aplicação.
Existem várias opções disponíveis para atualização das propriedades na configuração de identidade de uma aplicação para ativar e configurar afirmações opcionais:
-   Pode modificar o manifesto do aplicativo. O exemplo abaixo irá utilizar este método para fazer a configuração. Leitura a [Noções básicas sobre o documento de manifesto de aplicação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) primeiro para obter uma introdução para o manifesto.
-   Também é possível escrever uma aplicação que utiliza a [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) para atualizar a sua aplicação. O [referência de tipo complexo e entidade](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) na referência do Graph API guia o pode ajudar a configurar as afirmações opcionais.

**Exemplo:** No exemplo abaixo, irá modificar o manifesto de um aplicativo para adicionar afirmações para acesso, o ID e o SAML se destina a aplicação de tokens.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Depois de autenticado, escolha o seu inquilino do Azure AD ao selecioná-la a partir do canto superior direito da página.
1. Selecione **registos das aplicações** do lado esquerdo.
1. Encontre a aplicação que pretende configurar afirmações opcionais na lista e clique no mesmo.
1. A partir da página da aplicação, clique em **manifesto** para abrir o editor de manifesto inline. 
1. Pode editar diretamente o manifesto usando esse editor. O manifesto segue o esquema para o [entidade de aplicativo](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)e o manifesto de uma vez guardado de formatos de automática. Serão adicionados novos elementos para o `OptionalClaims` propriedade.

      ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
      "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
      "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }
      ```
      Neste caso, as afirmações opcionais diferentes foram adicionadas para cada tipo de token que a aplicação pode receber. Os tokens de ID agora irão conter o UPN para utilizadores federados na forma completa (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Os tokens de acesso que outros clientes pedem para esta aplicação irão agora incluir a afirmação auth_time. Os tokens SAML agora irão conter a extensão de esquema de diretório skypeId (neste exemplo, o ID de aplicação para esta aplicação é ab603c56068041afb2f6832e2a17e237). Os tokens SAML irão expor o ID de Skype como `extension_skypeId`.

1. Quando tiver terminado a atualizar o manifesto, clique em **guardar** para guardar o manifesto

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as declarações padrão fornecidas pelo Azure AD.

- [Tokens de ID](id-tokens.md)
- [Tokens de acesso](access-tokens.md)
