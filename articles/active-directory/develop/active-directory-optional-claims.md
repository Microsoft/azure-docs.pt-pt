---
title: Fornecer declarações opcionais para aplicativos do Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Como adicionar declarações personalizadas ou adicionais aos tokens SAML 2,0 e JSON Web tokens (JWT) emitidos pelo Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/08/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcaf347eb91f8777b56bb2ea4d26985b2d75f645
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967248"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Como: fornecer declarações opcionais para seu aplicativo do Azure AD

Os desenvolvedores de aplicativos podem usar declarações opcionais em seus aplicativos do Azure AD para especificar quais declarações eles desejam em tokens enviados para seu aplicativo. 

Você pode usar declarações opcionais para:

- Selecione declarações adicionais para incluir nos tokens para seu aplicativo.
- Altere o comportamento de determinadas declarações que o Azure AD retorna em tokens.
- Adicione e acesse declarações personalizadas para seu aplicativo.

Para obter as listas de declarações padrão, consulte o [token de acesso](access-tokens.md) e a documentação de declarações de [id_token](id-tokens.md) . 

Embora as declarações opcionais tenham suporte nos tokens de formato v 1.0 e v 2.0, bem como nos tokens SAML, elas fornecem a maior parte de seu valor ao mudar de v 1.0 para v 2.0. Um dos objetivos do ponto de [extremidade da plataforma de identidade da Microsoft v 2.0](active-directory-appmodel-v2-overview.md) é um tamanho de token menor para garantir o desempenho ideal dos clientes. Como resultado, várias declarações anteriormente incluídas nos tokens de acesso e de ID não estão mais presentes em tokens v 2.0 e devem ser solicitadas especificamente por aplicativo.

**Tabela 1: aplicabilidade**

| Tipo de Conta | tokens v 1.0 | tokens v 2.0  |
|--------------|---------------|----------------|
| conta Microsoft pessoais  | N/A  | Suportadas |
| Conta do Azure AD      | Suportadas | Suportadas |

## <a name="v10-and-v20-optional-claims-set"></a>conjunto de declarações opcionais v 1.0 e v 2.0

O conjunto de declarações opcionais disponíveis por padrão para aplicativos a serem usados está listado abaixo. Para adicionar declarações opcionais personalizadas para seu aplicativo, consulte [extensões de diretório](#configuring-directory-extension-optional-claims), abaixo. Ao adicionar declarações ao **token de acesso**, isso se aplicará aos tokens de acesso solicitados *para* o aplicativo (uma API da Web), não aqueles *pelo* aplicativo. Isso garante que, independentemente do cliente que acessa sua API, os dados corretos estejam presentes no token de acesso que usam para se autenticar em sua API.

> [!NOTE]
> A maioria dessas declarações pode ser incluída no JWTs para tokens v 1.0 e v 2.0, mas não tokens SAML, exceto quando indicado na coluna tipo de token. As contas de consumidor dão suporte a um subconjunto dessas declarações, marcadas na coluna "tipo de usuário".  Muitas das declarações listadas não se aplicam aos usuários do consumidor (não têm locatários, portanto `tenant_ctry` não tem valor).  

**Tabela 2: conjunto de declarações opcionais v 1.0 e v 2.0**

| Nome                       |  Descrição   | Tipo de token | Tipo de usuário | Notas  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Hora em que o usuário foi autenticado pela última vez. Consulte especificação do OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Região do locatário do recurso | JWT        |           | |
| `home_oid`                 | Para usuários convidados, a ID de objeto do usuário no locatário inicial do usuário.| JWT        |           | |
| `sid`                      | ID da sessão, usada para sair do usuário por sessão. | JWT        |  Contas pessoais e do Azure AD.   |         |
| `platf`                    | Plataforma de dispositivo    | JWT        |           | Restrito a dispositivos gerenciados que podem verificar o tipo de dispositivo.|
| `verified_primary_email`   | Originado do PrimaryAuthoritativeEmail do usuário      | JWT        |           |         |
| `verified_secondary_email` | Originado do SecondaryAuthoritativeEmail do usuário   | JWT        |           |        |
| `enfpolids`                | IDs de política imposta. Uma lista das IDs de política que foram avaliadas para o usuário atual. | JWT |  |  |
| `vnet`                     | Informações do especificador de VNET. | JWT        |           |      |
| `fwd`                      | Endereço IP.| JWT    |   | Adiciona o endereço IPv4 original do cliente solicitante (quando dentro de uma VNET) |
| `ctry`                     | País do usuário | JWT |  | O Azure AD retorna a declaração opcional `ctry` se ela estiver presente e o valor da declaração for um código de país de duas letras padrão, como FR, JP, SZ e assim por diante. |
| `tenant_ctry`              | País do locatário do recurso | JWT | | |
| `xms_pdl`          | Local de dados preferencial   | JWT | | Para locatários de várias regiões geográficas, esse é o código de três letras que mostra a região geográfica em que o usuário está. Para obter mais informações, consulte a [documentação de Azure ad Connect sobre o local de dados preferencial](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Por exemplo: `APC` para Pacífico Asiático. |
| `xms_pl`                   | Idioma preferencial do usuário  | JWT ||O idioma preferencial do usuário, se definido. Originado de seu locatário inicial, em cenários de acesso de convidado. Formatado LL-CC ("en-US"). |
| `xms_tpl`                  | Idioma preferencial do locatário| JWT | | O idioma preferencial do locatário do recurso, se definido. Format LL ("en"). |
| `ztdid`                    | ID de implantação de toque zero | JWT | | A identidade do dispositivo usada para o [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | O email endereçável para esse usuário, se o usuário tiver um.  | JWT, SAML | MSA, Azure AD | Esse valor será incluído por padrão se o usuário for um convidado no locatário.  Para usuários gerenciados (aqueles dentro do locatário), ele deve ser solicitado por essa declaração opcional ou, somente no v 2.0, com o escopo de OpenID.  Para usuários gerenciados, o endereço de email deve ser definido no [portal de administração do Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Formatação opcional para declarações de grupo |JWT, SAML| |Usado em conjunto com a configuração GroupMembershipClaims no [manifesto do aplicativo](reference-app-manifest.md), que também deve ser definido. Para obter detalhes, consulte [declarações de grupo](#configuring-groups-optional-claims) abaixo. Para obter mais informações sobre declarações de grupo, consulte [como configurar declarações de grupo](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | O status da conta de usuários no locatário. | JWT, SAML | | Se o usuário for um membro do locatário, o valor será `0`. Se forem convidados, o valor será `1`. |
| `upn`                      | Declaração UserPrincipalName. | JWT, SAML  |           | Embora essa declaração seja incluída automaticamente, você pode especificá-la como uma declaração opcional para anexar propriedades adicionais para modificar seu comportamento no caso do usuário convidado.  |

## <a name="v20-specific-optional-claims-set"></a>v 2.0-conjunto de declarações opcionais específicas

Essas declarações são sempre incluídas em tokens do Azure AD v 1.0, mas não são incluídas em tokens v 2.0, a menos que solicitado. Essas declarações são aplicáveis somente para JWTs (tokens de ID e tokens de acesso). 

**Tabela 3: v 2.0-apenas declarações opcionais**

| Declaração de JWT     | Nome                            | Descrição                                | Notas |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Endereço IP                      | O endereço IP do qual o cliente fez logon.   |       |
| `onprem_sid`  | Identificador de segurança local |                                             |       |
| `pwd_exp`     | Hora de Expiração da Palavra-Passe        | O DateTime no qual a senha expira. |       |
| `pwd_url`     | Alterar URL da senha             | Uma URL que o usuário pode visitar para alterar sua senha.   |   |
| `in_corp`     | Dentro da Rede da Empresa        | Sinaliza se o cliente está fazendo logon da rede corporativa. Se não forem, a declaração não será incluída.   |  Com base nas configurações de [IPs confiáveis](../authentication/howto-mfa-mfasettings.md#trusted-ips) no MFA.    |
| `nickname`    | Alcunha                        | Um nome adicional para o usuário, separado do nome ou sobrenome. | 
| `family_name` | Apelido                       | Fornece o sobrenome, sobrenome ou nome de família do usuário, conforme definido no objeto de usuário. <br>"family_name": "Miller" | Com suporte no MSA e no Azure AD   |
| `given_name`  | Nome próprio                      | Fornece o primeiro ou "dado" nome do usuário, conforme definido no objeto de usuário.<br>"given_name": "Frank"                   | Com suporte no MSA e no Azure AD  |
| `upn`         | Nome Principal de Utilizador | Um identificador para o usuário que pode ser usado com o parâmetro username_hint.  Não é um identificador durável para o usuário e não deve ser usado para dados de chave. | Consulte [as propriedades adicionais](#additional-properties-of-optional-claims) abaixo para obter a configuração da declaração. |

### <a name="additional-properties-of-optional-claims"></a>Propriedades adicionais de declarações opcionais

Algumas declarações opcionais podem ser configuradas para alterar a maneira como a declaração é retornada. Essas propriedades adicionais são usadas principalmente para ajudar na migração de aplicativos locais com expectativas de dados diferentes (por exemplo, `include_externally_authenticated_upn_without_hash` ajuda com os clientes que não podem tratar as marcas de hash (`#`) no UPN)

**Tabela 4: valores para configurar declarações opcionais**

| Nome da propriedade  | Nome de propriedade adicional | Descrição |
|----------------|--------------------------|-------------|
| `upn`          |                          | Pode ser usado para as respostas SAML e JWT e para tokens v 1.0 e v 2.0. |
|                | `include_externally_authenticated_upn`  | Inclui o UPN convidado como armazenado no locatário do recurso. Por exemplo, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | O mesmo que acima, exceto que as marcas de hash (`#`) são substituídas por sublinhados (`_`), por exemplo `foo_hometenant.com_EXT_@resourcetenant.com` |

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

Esse objeto OptionalClaims faz com que o token de ID retornado ao cliente inclua outro UPN com as informações adicionais de locatário de recurso e locatário doméstico. Isso só alterará a declaração de `upn` no token se o usuário for um convidado no locatário (que usa um IDP diferente para autenticação). 

## <a name="configuring-optional-claims"></a>Configurando declarações opcionais

> [!IMPORTANT]
> Os tokens de acesso **sempre** são gerados usando o manifesto do recurso, não o cliente.  Portanto, na solicitação `...scope=https://graph.microsoft.com/user.read...` o recurso é grafo.  Assim, o token de acesso é criado usando o manifesto do grafo, não o manifesto do cliente.  Alterar o manifesto para seu aplicativo nunca fará com que os tokens do Graph pareçam diferentes.  Para validar que as alterações de `accessToken` estão em vigor, solicite um token para seu aplicativo, não para outro aplicativo.  

Você pode configurar declarações opcionais para seu aplicativo por meio da interface do usuário ou do manifesto do aplicativo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Depois de autenticado, escolha seu locatário do Azure AD selecionando-o no canto superior direito da página.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Na seção **gerenciar** , selecione **registros de aplicativo**.
1. Selecione o aplicativo para o qual você deseja configurar declarações opcionais na lista.

**Configurando declarações opcionais por meio da interface do usuário:**

[![mostra como configurar declarações opcionais usando a interface do usuário](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Na seção **gerenciar** , selecione **configuração de token (versão prévia)** .
2. Selecione **Adicionar declaração opcional**.
3. Selecione o tipo de token que você deseja configurar.
4. Selecione as declarações opcionais a serem adicionadas.
5. Clique em **Adicionar**.

**Configurando declarações opcionais por meio do manifesto do aplicativo:**

[![mostra como configurar declarações opcionais usando o manifesto do aplicativo](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Na seção **gerenciar** , selecione **manifesto**. Um editor de manifesto baseado na Web é aberto, permitindo que você edite o manifesto. Opcionalmente, pode selecionar **Transferir**, editar o manifesto localmente e, em seguida, utilizar **Carregar** para o reaplicar à aplicação. Para obter mais informações sobre o manifesto do aplicativo, consulte o [artigo noções básicas sobre o manifesto do aplicativo do Azure ad](reference-app-manifest.md).

    A seguinte entrada de manifesto do aplicativo adiciona as declarações opcionais auth_time, IPADDR e UPN aos tokens ID, Access e SAML.

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

2. When finished, click **Save**. Now the specified optional claims will be included in the tokens for your application.    


### OptionalClaims type

Declares the optional claims requested by an application. An application can configure optional claims to be returned in each of three types of tokens (ID token, access token, SAML 2 token) it can receive from the security token service. The application can configure a different set of optional claims to be returned in each token type. The OptionalClaims property of the Application entity is an OptionalClaims object.

**Table 5: OptionalClaims type properties**

| Name        | Type                       | Description                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Collection (OptionalClaim) | The optional claims returned in the JWT ID token. |
| `accessToken` | Collection (OptionalClaim) | The optional claims returned in the JWT access token. |
| `saml2Token`  | Collection (OptionalClaim) | The optional claims returned in the SAML token.   |

### OptionalClaim type

Contains an optional claim associated with an application or a service principal. The idToken, accessToken, and saml2Token properties of the [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) type is a collection of OptionalClaim.
If supported by a specific claim, you can also modify the behavior of the OptionalClaim using the AdditionalProperties field.

**Table 6: OptionalClaim type properties**

| Name                 | Type                    | Description                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | The name of the optional claim.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | The source (directory object) of the claim. There are predefined claims and user-defined claims from extension properties. If the source value is null, the claim is a predefined optional claim. If the source value is user, the value in the name property is the extension property from the user object. |
| `essential`            | Edm.Boolean             | If the value is true, the claim specified by the client is necessary to ensure a smooth authorization experience for the specific task requested by the end user. The default value is false.                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | Additional properties of the claim. If a property exists in this collection, it modifies the behavior of the optional claim specified in the name property.                                                                                                                                               |
## Configuring directory extension optional claims

In addition to the standard optional claims set, you can also configure tokens to include directory schema extensions. For more info, see [Directory schema extensions](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). This feature is useful for attaching additional user information that your app can use – for example, an additional identifier or important configuration option that the user has set. See the bottom of this page for an example.

> [!NOTE]
> - Directory schema extensions are an Azure AD-only feature, so if your application manifest requests a custom extension and an MSA user logs into your app, these extensions will not be returned.
> - Azure AD optional claims only work with Azure AD Graph extensions and do not work with Microsoft Graph directory extensions. Both APIs require the `Directory.ReadWriteAll` permission, which can only be consented by admins.

### Directory extension formatting

When configuring directory extension optional claims using the application manifest, use the full name of the extension (in the format: `extension_<appid>_<attributename>`). The `<appid>` must match the ID of the application requesting the claim. 

Within the JWT, these claims will be emitted with the following name format:  `extn.<attributename>`.

Within the SAML tokens, these claims will be emitted with the following URI format: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## Configuring groups optional claims

   > [!NOTE]
   > The ability to emit group names for users and groups synced from on-premises is Public Preview.

This section covers the configuration options under optional claims for changing the group attributes used in group claims from the default group objectID to attributes synced from on-premises Windows Active Directory. You can configure groups optional claims for your application through the UI or application manifest.

> [!IMPORTANT]
> See [Configure group claims for applications with Azure AD](../hybrid/how-to-connect-fed-group-claims.md) for more details including important caveats for the public preview of group claims from on-premises attributes.

**Configuring groups optional claims through the UI:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Under the **Manage** section, select **App registrations**
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Token configuration (preview)**
2. Select **Add groups claim**
3. Select the group types to return (**All Groups**, **SecurityGroup** or **DirectoryRole**). The **All Groups** option includes **SecurityGroup**, **DirectoryRole** and **DistributionList**
4. Optional: click on the specific token type properties to modify the groups claim value to contain on premises group attributes or to change the claim type to a role
5. Click **Save**

**Configuring groups optional claims through the application manifest:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Manifest**
3. Add the following entry using the manifest editor:

   The valid values are:

   - "All" (this option includes SecurityGroup, DirectoryRole and DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   For example:

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

   | Esquema de declarações opcional | Valor |
   |----------|-------------|
   | **name:** | Deve ser "grupos" |
   | **original** | Não utilizado. Omitir ou especificar nulo |
   | **essential:** | Não utilizado. Omitir ou especificar false |
   | **additionalProperties:** | Lista de propriedades adicionais.  As opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   Somente um de "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" é necessário em um adicional.  Se mais de um estiver presente, o primeiro será usado e todos os outros serão ignorados.

   Alguns aplicativos exigem informações de grupo sobre o usuário na declaração de função.  Para alterar o tipo de declaração para de uma declaração de grupo para uma declaração de função, adicione "emit_as_roles" a propriedades adicionais.  Os valores de grupo serão emitidos na declaração de função.

   > [!NOTE]
   > Se "emit_as_roles" for usado, as funções de aplicativo configuradas que o usuário está atribuído não aparecerão na declaração de função

**Exemplos:**

1) Emitir grupos como nomes de grupo nos tokens de acesso OAuth no formato dnsDomainName\sAMAccountName

    
    **Configuração da interface do usuário:**

    [![mostra como configurar declarações opcionais usando a interface do usuário](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Entrada do manifesto do aplicativo:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Emitir nomes de grupos a serem retornados no formato netbiosDomain\sAMAccountName como a declaração de funções em tokens de ID SAML e OIDC

    **Configuração da interface do usuário:**

    [![mostra como configurar declarações opcionais usando a interface do usuário](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Entrada do manifesto do aplicativo:**
    
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
    ``` 
     

## <a name="optional-claims-example"></a>Exemplo de declarações opcionais

Nesta seção, você pode percorrer um cenário para ver como você pode usar o recurso de declarações opcionais para seu aplicativo.
Há várias opções disponíveis para atualizar as propriedades na configuração de identidade de um aplicativo para habilitar e configurar declarações opcionais:
-    Você pode usar a interface do usuário de **configuração de token (versão prévia)** (Veja o exemplo abaixo)
-    Você pode usar o **manifesto** (Veja o exemplo abaixo). Leia o [documento noções básicas sobre o manifesto do aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) primeiro para obter uma introdução ao manifesto.
-   Também é possível escrever um aplicativo que usa o [API do Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) para atualizar seu aplicativo. A [entidade e a referência de tipo complexo](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) no guia de referência de API do Graph podem ajudá-lo a configurar as declarações opcionais.

**Exemplo:** No exemplo a seguir, você usará a interface do usuário de **configuração do token (versão prévia)** e o **manifesto** para adicionar declarações opcionais aos tokens de acesso, ID e SAML destinados ao seu aplicativo. Diferentes declarações opcionais serão adicionadas a cada tipo de token que o aplicativo pode receber:
-    Agora, os tokens de ID conterão o UPN para usuários federados no formato completo (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
-    Os tokens de acesso que outros clientes solicitam para esse aplicativo agora incluirão a declaração de auth_time
-    Os tokens SAML agora conterão a extensão de esquema de diretório do SkypeID (neste exemplo, a ID do aplicativo para esse aplicativo é ab603c56068041afb2f6832e2a17e237). Os tokens SAML vão expor a ID do Skype como `extension_skypeId`.

**Configuração da interface do usuário:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com)

1. Depois de autenticado, escolha seu locatário do Azure AD selecionando-o no canto superior direito da página.

1. Selecione **Azure Active Directory** no menu à esquerda.

1. Na seção **gerenciar** , selecione **registros de aplicativo**.

1. Localize o aplicativo para o qual você deseja configurar declarações opcionais na lista e clique nele.

1. Na seção **gerenciar** , clique em **configuração de token (versão prévia)** .

1. Selecione **Adicionar declaração opcional**, selecione o tipo de token de **ID** , selecione **UPN** na lista de declarações e clique em **Adicionar**.

1. Selecione **Adicionar declaração opcional**, selecione o tipo de token de **acesso** , selecione **auth_time** na lista de declarações e clique em **Adicionar**.

1. Na tela Visão geral da configuração do token, clique no ícone de lápis ao lado de **UPN**, clique na alternância **autenticada externamente** e, em seguida, clique em **salvar**.

1. Selecione **Adicionar declaração opcional**, selecione o tipo de token **SAML** , selecione **Extn. SkypeID** na lista de declarações (aplicável somente se você tiver criado um objeto de usuário do Azure AD chamado SkypeID) e, em seguida, clique em **Adicionar**.

    [![mostra como configurar declarações opcionais usando a interface do usuário](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Configuração do manifesto:**
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Depois de autenticado, escolha seu locatário do Azure AD selecionando-o no canto superior direito da página.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Localize o aplicativo para o qual você deseja configurar declarações opcionais na lista e clique nele.
1. Na seção **gerenciar** , clique em **manifesto** para abrir o editor de manifesto embutido.
1. Você pode editar o manifesto diretamente usando este editor. O manifesto segue o esquema para a [entidade de aplicativo]. (https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) e formata automaticamente o manifesto depois de salvo. Novos elementos serão adicionados à propriedade `OptionalClaims`.

    ```json
            "optionalClaims": {
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
        ``` 


1. When you're finished updating the manifest, click **Save** to save the manifest.

## Next steps

Learn more about the standard claims provided by Azure AD.

- [ID tokens](id-tokens.md)
- [Access tokens](access-tokens.md)
