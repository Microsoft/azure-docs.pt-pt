---
title: Saiba como fornecer declarações opcionais para seu aplicativo do Azure AD | Microsoft Docs
description: Um guia para adicionar declarações personalizadas ou adicionais aos tokens SAML 2,0 e JSON Web tokens (JWT) emitidos pelo Azure Active Directory.
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/03/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6e097df21051019495b3bf9bb6c83cb3dba03c8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835342"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Como: Fornecer declarações opcionais para seu aplicativo do Azure AD

Os desenvolvedores de aplicativos podem usar declarações opcionais em seus aplicativos do Azure AD para especificar quais declarações eles desejam em tokens enviados para seu aplicativo. 

Você pode usar declarações opcionais para:

- Selecione declarações adicionais para incluir nos tokens para seu aplicativo.
- Altere o comportamento de determinadas declarações que o Azure AD retorna em tokens.
- Adicione e acesse declarações personalizadas para seu aplicativo.

Para obter as listas de declarações padrão, consulte o [token de acesso](access-tokens.md) e a documentação de declarações do [id_token](id-tokens.md) . 

Embora as declarações opcionais tenham suporte nos tokens de formato v 1.0 e v 2.0, bem como nos tokens SAML, elas fornecem a maior parte de seu valor ao mudar de v 1.0 para v 2.0. Um dos objetivos do ponto de [extremidade da plataforma de identidade da Microsoft v 2.0](active-directory-appmodel-v2-overview.md) é um tamanho de token menor para garantir o desempenho ideal dos clientes. Como resultado, várias declarações anteriormente incluídas nos tokens de acesso e de ID não estão mais presentes em tokens v 2.0 e devem ser solicitadas especificamente por aplicativo.

**Tabela 1: Aplicabilidade**

| Tipo de Conta | tokens v 1.0 | tokens v 2.0  |
|--------------|---------------|----------------|
| conta Microsoft pessoais  | N/A  | Suportadas |
| Conta do Azure AD      | Suportadas | Suportadas |

## <a name="v10-and-v20-optional-claims-set"></a>conjunto de declarações opcionais v 1.0 e v 2.0

O conjunto de declarações opcionais disponíveis por padrão para aplicativos a serem usados está listado abaixo. Para adicionar declarações opcionais personalizadas para seu aplicativo, consulte [extensões de diretório](#configuring-directory-extension-optional-claims), abaixo. Ao adicionar declarações ao **token de acesso**, isso se aplicará aos tokens de acesso solicitados *para* o aplicativo (uma API da Web), não aqueles *pelo* aplicativo. Isso garante que, independentemente do cliente que acessa sua API, os dados corretos estejam presentes no token de acesso que usam para se autenticar em sua API.

> [!NOTE]
> A maioria dessas declarações pode ser incluída no JWTs para tokens v 1.0 e v 2.0, mas não tokens SAML, exceto quando indicado na coluna tipo de token. As contas de consumidor dão suporte a um subconjunto dessas declarações, marcadas na coluna "tipo de usuário".  Muitas das declarações listadas não se aplicam aos usuários do consumidor (não têm nenhum locatário `tenant_ctry` , portanto, não tem valor).  

**Tabela 2: conjunto de declarações opcionais v 1.0 e v 2.0**

| Nome                       |  Descrição   | Tipo de token | Tipo de Utilizador | Notas  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Hora em que o usuário foi autenticado pela última vez. Consulte especificação do OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Região do locatário do recurso | JWT        |           | |
| `home_oid`                 | Para usuários convidados, a ID de objeto do usuário no locatário inicial do usuário.| JWT        |           | |
| `sid`                      | ID da sessão, usada para sair do usuário por sessão. | JWT        |  Contas pessoais e do Azure AD.   |         |
| `platf`                    | Plataforma de dispositivos    | JWT        |           | Restrito a dispositivos gerenciados que podem verificar o tipo de dispositivo.|
| `verified_primary_email`   | Originado do PrimaryAuthoritativeEmail do usuário      | JWT        |           |         |
| `verified_secondary_email` | Originado do SecondaryAuthoritativeEmail do usuário   | JWT        |           |        |
| `enfpolids`                | IDs de política imposta. Uma lista das IDs de política que foram avaliadas para o usuário atual. | JWT |  |  |
| `vnet`                     | Informações do especificador de VNET. | JWT        |           |      |
| `fwd`                      | Endereço IP.| JWT    |   | Adiciona o endereço IPv4 original do cliente solicitante (quando dentro de uma VNET) |
| `ctry`                     | País do usuário | JWT |  | O Azure ad retorna `ctry` a declaração opcional se ela estiver presente e o valor da declaração for um código de país de duas letras padrão, como fr, JP, sz e assim por diante. |
| `tenant_ctry`              | País do locatário do recurso | JWT | | |
| `xms_pdl`          | Local de dados preferencial   | JWT | | Para locatários de várias regiões geográficas, esse é o código de três letras que mostra a região geográfica em que o usuário está. Para obter mais informações, consulte a [documentação de Azure ad Connect sobre o local de dados preferencial](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Por exemplo: `APC` para Pacífico Asiático. |
| `xms_pl`                   | Idioma preferencial do usuário  | JWT ||O idioma preferencial do usuário, se definido. Originado de seu locatário inicial, em cenários de acesso de convidado. Formatado LL-CC ("en-US"). |
| `xms_tpl`                  | Idioma preferencial do locatário| JWT | | O idioma preferencial do locatário do recurso, se definido. Format LL ("en"). |
| `ztdid`                    | ID de implantação de toque zero | JWT | | A identidade do dispositivo usada para o [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | O email endereçável para esse usuário, se o usuário tiver um.  | JWT, SAML | MSA, Azure AD | Esse valor será incluído por padrão se o usuário for um convidado no locatário.  Para usuários gerenciados (aqueles dentro do locatário), ele deve ser solicitado por essa declaração opcional ou, somente no v 2.0, com o escopo de OpenID.  Para usuários gerenciados, o endereço de email deve ser definido no [portal de administração do Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Formatação opcional para declarações de grupo |JWT, SAML| |Usado em conjunto com a configuração GroupMembershipClaims no [manifesto do aplicativo](reference-app-manifest.md), que também deve ser definido. Para obter detalhes, consulte [declarações de grupo](#Configuring-group-optional claims) abaixo. Para obter mais informações sobre declarações de grupo, consulte [como configurar declarações de grupo](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | O status da conta de usuários no locatário. | JWT, SAML | | Se o usuário for um membro do locatário, o valor será `0`. Se forem convidados, o valor será `1`. |
| `upn`                      | Declaração UserPrincipalName. | JWT, SAML  |           | Embora essa declaração seja incluída automaticamente, você pode especificá-la como uma declaração opcional para anexar propriedades adicionais para modificar seu comportamento no caso do usuário convidado.  |

### <a name="v20-optional-claims"></a>declarações opcionais v 2.0

Essas declarações são sempre incluídas em tokens do Azure AD v 1.0, mas não são incluídas em tokens v 2.0, a menos que solicitado. Essas declarações são aplicáveis somente para JWTs (tokens de ID e tokens de acesso). 

**Tabela 3: v 2.0-apenas declarações opcionais**

| Declaração de JWT     | Nome                            | Descrição                                | Notas |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Endereço IP                      | O endereço IP do qual o cliente fez logon.   |       |
| `onprem_sid`  | Identificador de Segurança no Local |                                             |       |
| `pwd_exp`     | Tempo de expiração da senha        | O DateTime no qual a senha expira. |       |
| `pwd_url`     | Alterar URL da senha             | Uma URL que o usuário pode visitar para alterar sua senha.   |   |
| `in_corp`     | Dentro da rede corporativa        | Sinaliza se o cliente está fazendo logon da rede corporativa. Se não forem, a declaração não será incluída.   |  Com base nas configurações de [IPs confiáveis](../authentication/howto-mfa-mfasettings.md#trusted-ips) no MFA.    |
| `nickname`    | Alcunha                        | Um nome adicional para o usuário, separado do nome ou sobrenome. | 
| `family_name` | Apelido                       | Fornece o sobrenome, sobrenome ou nome de família do usuário, conforme definido no objeto de usuário. <br>"family_name": "Miller" | Com suporte no MSA e no Azure AD   |
| `given_name`  | Nome próprio                      | Fornece o primeiro ou "dado" nome do usuário, conforme definido no objeto de usuário.<br>"given_name": Frank                   | Com suporte no MSA e no Azure AD  |
| `upn`         | Nome Principal de utilizador | Um identificador para o usuário que pode ser usado com o parâmetro username_hint.  Não é um identificador durável para o usuário e não deve ser usado para dados de chave. | Consulte [as propriedades adicionais](#additional-properties-of-optional-claims) abaixo para obter a configuração da declaração. |

### <a name="additional-properties-of-optional-claims"></a>Propriedades adicionais de declarações opcionais

Algumas declarações opcionais podem ser configuradas para alterar a maneira como a declaração é retornada. Essas propriedades adicionais são usadas principalmente para ajudar na migração de aplicativos locais com expectativas de dados diferentes (por exemplo, `include_externally_authenticated_upn_without_hash` ajuda com clientes que não podem lidar com marcas`#`de hash () no UPN)

**Tabela 4: Valores para configurar declarações opcionais**

| Nome da propriedade  | Nome de propriedade adicional | Descrição |
|----------------|--------------------------|-------------|
| `upn`          |                          | Pode ser usado para as respostas SAML e JWT e para tokens v 1.0 e v 2.0. |
|                | `include_externally_authenticated_upn`  | Inclui o UPN convidado como armazenado no locatário do recurso. Por exemplo, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | O mesmo que acima, exceto que as marcas de`#`hash () são substituídas por sublinhados (`_`), por exemplo`foo_hometenant.com_EXT_@resourcetenant.com` |

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

Esse objeto OptionalClaims faz com que o token de ID retornado ao cliente inclua outro UPN com as informações adicionais de locatário de recurso e locatário doméstico. Isso só alterará a `upn` declaração no token se o usuário for um convidado no locatário (que usa um IDP diferente para autenticação). 

## <a name="configuring-optional-claims"></a>Configurando declarações opcionais

Você pode configurar declarações opcionais para seu aplicativo modificando o manifesto do aplicativo (Veja o exemplo abaixo). Para obter mais informações, consulte o [artigo noções básicas sobre o manifesto de aplicativo do Azure ad](reference-app-manifest.md).

> [!IMPORTANT]
> Os tokens de acesso **sempre** são gerados usando o manifesto do recurso, não o cliente.  Portanto, na solicitação `...scope=https://graph.microsoft.com/user.read...` , o recurso é grafo.  Assim, o token de acesso é criado usando o manifesto do grafo, não o manifesto do cliente.  Alterar o manifesto para seu aplicativo nunca fará com que os tokens do Graph pareçam diferentes.  Para validar que `accessToken` as alterações estão em vigor, solicite um token para seu aplicativo, não para outro aplicativo.  

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

Declara as declarações opcionais solicitadas por um aplicativo. Um aplicativo pode configurar declarações opcionais a serem retornadas em cada um dos três tipos de tokens (token de ID, token de acesso, token SAML 2) que pode receber do serviço de token de segurança. O aplicativo pode configurar um conjunto diferente de declarações opcionais a serem retornadas em cada tipo de token. A propriedade OptionalClaims da entidade de aplicativo é um objeto OptionalClaims.

**Tabela 5: Propriedades do tipo OptionalClaims**

| Nome        | Tipo                       | Descrição                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de ID JWT. |
| `accessToken` | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de acesso JWT. |
| `saml2Token`  | Coleção (OptionalClaim) | As declarações opcionais retornadas no token SAML.   |

### <a name="optionalclaim-type"></a>Tipo de OptionalClaim

Contém uma declaração opcional associada a um aplicativo ou a uma entidade de serviço. As propriedades token, accessToken e saml2Token do tipo [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) é uma coleção de OptionalClaim.
Se houver suporte para uma declaração específica, você também poderá modificar o comportamento do OptionalClaim usando o campo AdditionalProperties.

**Tabela 6: Propriedades do tipo OptionalClaim**

| Nome                 | Tipo                    | Descrição                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | O nome da declaração opcional.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | A origem (objeto de diretório) da declaração. Há declarações predefinidas e declarações definidas pelo usuário das propriedades de extensão. Se o valor de origem for nulo, a declaração será uma declaração opcional predefinida. Se o valor de origem for User, o valor na propriedade Name será a propriedade de extensão do objeto user. |
| `essential`            | Edm.Boolean             | Se o valor for true, a declaração especificada pelo cliente será necessária para garantir uma experiência de autorização suave para a tarefa específica solicitada pelo usuário final. O valor predefinido é false.                                                                                                             |
| `additionalProperties` | Coleção (EDM. String) | Propriedades adicionais da declaração. Se uma propriedade existir nessa coleção, ela modificará o comportamento da declaração opcional especificada na propriedade Name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Configurando declarações opcionais de extensão de diretório

Além do conjunto de declarações opcionais padrão, você também pode configurar tokens para incluir extensões de esquema de diretório. Para obter mais informações, consulte [extensões de esquema de diretório](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Esse recurso é útil para anexar informações de usuário adicionais que seu aplicativo pode usar, por exemplo, um identificador adicional ou uma opção de configuração importante que o usuário definiu. 

> [!Note]
> - As extensões de esquema de diretório são um recurso somente do Azure AD, portanto, se o manifesto do aplicativo solicitar uma extensão personalizada e um usuário da MSA fizer logon em seu aplicativo, essas extensões não serão retornadas.
> - As declarações opcionais do Azure AD funcionam apenas com a extensão do Azure AD e não funcionam com a extensão de diretório Microsoft Graph. Ambas as APIs exigem `Directory.ReadWriteAll` a permissão, que só pode ser consentida por administradores.

### <a name="directory-extension-formatting"></a>Formatação de extensão de diretório

Para atributos de extensão, use o nome completo da extensão (no formato: `extension_<appid>_<attributename>`) no manifesto do aplicativo. O `<appid>` deve corresponder à ID do aplicativo que está solicitando a declaração. 

Dentro do JWT, essas declarações serão emitidas com o seguinte formato de nome: `extn.<attributename>`.

Dentro dos tokens SAML, essas declarações serão emitidas com o seguinte formato de URI:`http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-group-optional-claims"></a>Configurando declarações opcionais de grupo

   > [!NOTE]
   > A capacidade de emitir nomes de grupo para usuários e grupos sincronizados a partir do local é visualização pública.

Esta seção aborda as opções de configuração em declarações opcionais para alterar os atributos de grupo usados em declarações de grupo do objectID do grupo padrão para atributos sincronizados do Windows Active Directory local.

> [!IMPORTANT]
> Consulte [Configurar declarações de grupo para aplicativos com o Azure ad](../hybrid/how-to-connect-fed-group-claims.md) para obter mais detalhes, incluindo advertências importantes para a visualização pública de declarações de grupo de atributos locais.

1. No portal-> Azure Active Directory-> registros do aplicativo-> selecione o manifesto do aplicativo >

2. Habilitar declarações de associação de grupo alterando o groupMembershipClaim

   Os valores válidos são:

   - Os
   - SecurityGroup
   - DistributionList
   - DirectoryRole

   Por exemplo:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Por padrão, ObjectIDs de grupo serão emitidas no valor de declaração de grupo.  Para modificar o valor da declaração para conter os atributos do grupo local ou para alterar o tipo de declaração para função, use a configuração OptionalClaims da seguinte maneira:

3. Definir declarações opcionais de configuração de nome de grupo.

   Se você quiser que os grupos no token contenham os atributos do grupo do AD local na seção declarações opcionais, especifique a qual tipo de token a declaração opcional deve ser aplicada, o nome da declaração opcional solicitada e todas as propriedades adicionais desejadas.  Vários tipos de token podem ser listados:

   - Token para o token de ID de OIDC
   - accessToken para o token de acesso do OAuth/OIDC
   - Saml2Token para tokens SAML.

   > [!NOTE]
   > O tipo Saml2Token aplica-se aos tokens de formato SAML 1.1 e SAML 2.0

   Para cada tipo de token relevante, modifique a declaração de grupos para usar a seção OptionalClaims no manifesto. O esquema OptionalClaims é o seguinte:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Esquema de declarações opcional | Value |
   |----------|-------------|
   | **name:** | Deve ser "grupos" |
   | **original** | Não usado. Omitir ou especificar nulo |
   | **essential:** | Não usado. Omitir ou especificar false |
   | **additionalProperties:** | Lista de propriedades adicionais.  As opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   Em AdditionalProperties, apenas um de "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" são necessários.  Se mais de um estiver presente, o primeiro será usado e todos os outros serão ignorados.

   Alguns aplicativos exigem informações de grupo sobre o usuário na declaração de função.  Para alterar o tipo de declaração para de uma declaração de grupo para uma declaração de função, adicione "emit_as_roles" a propriedades adicionais.  Os valores de grupo serão emitidos na declaração de função.

   > [!NOTE]
   > Se "emit_as_roles" for usado, qualquer função de aplicativo configurada que o usuário está atribuído não aparecerá na declaração de função

**Exemplos:** Emitir grupos como nomes de grupo nos tokens de acesso OAuth no formato dnsDomainName\sAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Para emitir nomes de grupo a serem retornados no formato netbiosDomain\sAMAccountName como a declaração de funções nos tokens de ID SAML e OIDC:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }

 ```

## <a name="optional-claims-example"></a>Exemplo de declarações opcionais

Nesta seção, você pode percorrer um cenário para ver como você pode usar o recurso de declarações opcionais para seu aplicativo.
Há várias opções disponíveis para atualizar as propriedades na configuração de identidade de um aplicativo para habilitar e configurar declarações opcionais:
-   Você pode modificar o manifesto do aplicativo. O exemplo a seguir usará esse método para fazer a configuração. Leia o [documento noções básicas sobre o manifesto do aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) primeiro para obter uma introdução ao manifesto.
-   Também é possível escrever um aplicativo que usa o [API do Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) para atualizar seu aplicativo. A [entidade e a referência de tipo complexo](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) no guia de referência de API do Graph podem ajudá-lo a configurar as declarações opcionais.

**Example:** No exemplo a seguir, você modificará o manifesto de um aplicativo para adicionar declarações a tokens de acesso, ID e SAML destinados ao aplicativo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Depois de autenticado, escolha seu locatário do Azure AD selecionando-o no canto superior direito da página.
1. Selecione **registros de aplicativo** do lado esquerdo.
1. Localize o aplicativo para o qual você deseja configurar declarações opcionais na lista e clique nele.
1. Na página do aplicativo, clique em **manifesto** para abrir o editor de manifesto embutido. 
1. Você pode editar o manifesto diretamente usando este editor. O manifesto segue o esquema para a [entidade de aplicativo](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)e formata automaticamente o manifesto depois de salvo. Novos elementos serão adicionados à `OptionalClaims` propriedade.

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

    Nesse caso, diferentes declarações opcionais foram adicionadas a cada tipo de token que o aplicativo pode receber. Agora, os tokens de ID conterão o UPN para usuários federados no formato`<upn>_<homedomain>#EXT#@<resourcedomain>`completo (). Os tokens de acesso que outros clientes solicitam para esse aplicativo agora incluirão a declaração auth_time. Os tokens SAML agora conterão a extensão de esquema de diretório do SkypeID (neste exemplo, a ID do aplicativo para esse aplicativo é ab603c56068041afb2f6832e2a17e237). Os tokens SAML vão expor a ID do `extension_skypeId`Skype como.

1. Quando tiver terminado de atualizar o manifesto, clique em **salvar** para salvar o manifesto

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as declarações padrão fornecidas pelo Azure AD.

- [Tokens de ID](id-tokens.md)
- [Tokens de acesso](access-tokens.md)
