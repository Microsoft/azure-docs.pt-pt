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
ms.openlocfilehash: 1bc2c3a17aef232df184926dca5f70eac61b03ac
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698769"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Como: fornecer declarações opcionais para seu aplicativo do Azure AD

Os desenvolvedores de aplicações podem utilizar reclamações opcionais nas suas aplicações Azure AD para especificar quais as reclamações que pretendem em fichas enviadas para a sua aplicação. 

Você pode usar declarações opcionais para:

- Selecione declarações adicionais para incluir nos tokens para seu aplicativo.
- Altere o comportamento de determinadas declarações que o Azure AD retorna em tokens.
- Adicione e acesse declarações personalizadas para seu aplicativo.

Para as listas de reclamações padrão, consulte a [documentação](access-tokens.md) de acesso e [id_token](id-tokens.md) reclamações. 

Embora as declarações opcionais tenham suporte nos tokens de formato v 1.0 e v 2.0, bem como nos tokens SAML, elas fornecem a maior parte de seu valor ao mudar de v 1.0 para v 2.0. Um dos objetivos do ponto de [extremidade da plataforma de identidade da Microsoft v 2.0](active-directory-appmodel-v2-overview.md) é um tamanho de token menor para garantir o desempenho ideal dos clientes. Como resultado, várias declarações anteriormente incluídas nos tokens de acesso e de ID não estão mais presentes em tokens v 2.0 e devem ser solicitadas especificamente por aplicativo.

**Tabela 1: aplicabilidade**

| Tipo de Conta | tokens v 1.0 | tokens v 2.0  |
|--------------|---------------|----------------|
| conta Microsoft pessoais  | N/A  | Suportadas |
| Conta do Azure AD      | Suportadas | Suportadas |

## <a name="v10-and-v20-optional-claims-set"></a>conjunto de declarações opcionais v 1.0 e v 2.0

O conjunto de declarações opcionais disponíveis por padrão para aplicativos a serem usados está listado abaixo. Para adicionar declarações opcionais personalizadas para seu aplicativo, consulte [extensões de diretório](#configuring-directory-extension-optional-claims), abaixo. Ao adicionar declarações ao **token de acesso**, as declarações se aplicam aos tokens de acesso solicitados *para* o aplicativo (uma API da Web), não as declarações solicitadas *pelo* aplicativo. Não importa como o cliente acessa sua API, os dados corretos estão presentes no token de acesso que é usado para autenticar em sua API.

> [!NOTE]
> A maioria dessas declarações pode ser incluída no JWTs para tokens v 1.0 e v 2.0, mas não tokens SAML, exceto quando indicado na coluna tipo de token. As contas de consumidor dão suporte a um subconjunto dessas declarações, marcadas na coluna "tipo de usuário".  Muitas das reclamações enumeradas não se aplicam aos utilizadores de consumo (não têm inquilino, pelo que `tenant_ctry` não tem valor).  

**Tabela 2: conjunto de declarações opcionais v 1.0 e v 2.0**

| Nome                       |  Descrição   | Tipo de token | Tipo de usuário | Notas  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Hora em que o usuário foi autenticado pela última vez. Consulte especificação do OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Região do locatário do recurso | JWT        |           | |
| `home_oid`                 | Para usuários convidados, a ID de objeto do usuário no locatário inicial do usuário.| JWT        |           | |
| `sid`                      | ID da sessão, usada para saída do usuário por sessão. | JWT        |  Contas pessoais e do Azure AD.   |         |
| `platf`                    | Plataforma de dispositivo    | JWT        |           | Restrito a dispositivos gerenciados que podem verificar o tipo de dispositivo.|
| `verified_primary_email`   | Originado do PrimaryAuthoritativeEmail do usuário      | JWT        |           |         |
| `verified_secondary_email` | Originado do SecondaryAuthoritativeEmail do usuário   | JWT        |           |        |
| `enfpolids`                | IDs de política imposta. Uma lista das IDs de política que foram avaliadas para o usuário atual. | JWT |  |  |
| `vnet`                     | Informações do especificador de VNET. | JWT        |           |      |
| `fwd`                      | Endereço IP.| JWT    |   | Adiciona o endereço IPv4 original do cliente solicitante (quando dentro de uma VNET) |
| `ctry`                     | País do usuário | JWT |  | O Azure AD retorna a declaração opcional `ctry` se ela estiver presente e o valor da declaração for um código de país de duas letras padrão, como FR, JP, SZ e assim por diante. |
| `tenant_ctry`              | País do locatário do recurso | JWT | | |
| `xms_pdl`          | Local de dados preferencial   | JWT | | Para locatários de várias regiões geográficas, o local de dados preferencial é o código de três letras mostrando a região geográfica em que o usuário está. Para obter mais informações, consulte a [documentação de Azure ad Connect sobre o local de dados preferencial](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Por exemplo: `APC` para Pacífico Asiático. |
| `xms_pl`                   | Idioma preferencial do usuário  | JWT ||O idioma preferencial do usuário, se definido. Originado de seu locatário inicial, em cenários de acesso de convidado. Formatado LL-CC ("en-US"). |
| `xms_tpl`                  | Idioma preferencial do locatário| JWT | | O idioma preferencial do locatário do recurso, se definido. Format LL ("en"). |
| `ztdid`                    | ID de implantação de toque zero | JWT | | A identidade do dispositivo usada para o [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | O email endereçável para esse usuário, se o usuário tiver um.  | JWT, SAML | MSA, Azure AD | Esse valor será incluído por padrão se o usuário for um convidado no locatário.  Para usuários gerenciados (os usuários dentro do locatário), ele deve ser solicitado por essa declaração opcional ou, somente no v 2.0, com o escopo de OpenID.  Para usuários gerenciados, o endereço de email deve ser definido no [portal de administração do Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Formatação opcional para declarações de grupo |JWT, SAML| |Usado em conjunto com a configuração GroupMembershipClaims no [manifesto do aplicativo](reference-app-manifest.md), que também deve ser definido. Para obter detalhes, consulte [declarações de grupo](#configuring-groups-optional-claims) abaixo. Para obter mais informações sobre declarações de grupo, consulte [como configurar declarações de grupo](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | O status da conta de usuários no locatário. | JWT, SAML | | Se o usuário for um membro do locatário, o valor será `0`. Se forem convidados, o valor será `1`. |
| `upn`                      | Declaração UserPrincipalName. | JWT, SAML  |           | Embora essa declaração seja incluída automaticamente, você pode especificá-la como uma declaração opcional para anexar propriedades adicionais para modificar seu comportamento no caso do usuário convidado.  |

## <a name="v20-specific-optional-claims-set"></a>v2.0 conjunto de reclamações opcionais específicas

Essas declarações são sempre incluídas em tokens do Azure AD v 1.0, mas não são incluídas em tokens v 2.0, a menos que solicitado. Essas declarações são aplicáveis somente para JWTs (tokens de ID e tokens de acesso). 

**Tabela 3: v 2.0-apenas declarações opcionais**

| Declaração de JWT     | Nome                            | Descrição                                | Notas |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Endereço IP                      | O endereço IP do qual o cliente fez logon.   |       |
| `onprem_sid`  | Identificador de segurança local |                                             |       |
| `pwd_exp`     | Hora de Expiração da Palavra-Passe        | O DateTime no qual a senha expira. |       |
| `pwd_url`     | Alterar URL da senha             | Uma URL que o usuário pode visitar para alterar sua senha.   |   |
| `in_corp`     | Dentro da Rede da Empresa        | Sinaliza se o cliente está fazendo logon da rede corporativa. Se não forem, a declaração não será incluída.   |  Com base nas configurações de [IPs confiáveis](../authentication/howto-mfa-mfasettings.md#trusted-ips) no MFA.    |
| `nickname`    | Alcunha                        | Um nome adicional para o usuário. O apelido é separado de First ou Last Name. | 
| `family_name` | Apelido                       | Fornece o sobrenome, sobrenome ou nome de família do usuário, conforme definido no objeto de usuário. <br>"family_name":"Miller" | Com suporte no MSA e no Azure AD   |
| `given_name`  | Nome próprio                      | Fornece o primeiro ou "dado" nome do usuário, conforme definido no objeto de usuário.<br>"given_name": "Frank"                   | Com suporte no MSA e no Azure AD  |
| `upn`         | Nome Principal de Utilizador | Um identificador para o utilizador que pode ser utilizado com o parâmetro username_hint.  Não é um identificador durável para o usuário e não deve ser usado para dados de chave. | Consulte [as propriedades adicionais](#additional-properties-of-optional-claims) abaixo para obter a configuração da declaração. |

### <a name="additional-properties-of-optional-claims"></a>Propriedades adicionais de declarações opcionais

Algumas declarações opcionais podem ser configuradas para alterar a maneira como a declaração é retornada. Estas propriedades adicionais são usadas principalmente para ajudar a migrar de aplicações no local com diferentes expectativas de dados (por exemplo, `include_externally_authenticated_upn_without_hash` ajuda com clientes que não conseguem lidar com marcas de hash (`#`) na UPN)

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

Esse objeto OptionalClaims faz com que o token de ID retornado ao cliente inclua outro UPN com as informações adicionais de locatário de recurso e locatário doméstico. A declaração de `upn` só será alterada no token se o usuário for um convidado no locatário (que usa um IDP diferente para autenticação). 

## <a name="configuring-optional-claims"></a>Configurando declarações opcionais

> [!IMPORTANT]
> Os tokens de acesso **sempre** são gerados usando o manifesto do recurso, não o cliente.  Portanto, na solicitação `...scope=https://graph.microsoft.com/user.read...` o recurso é grafo.  Assim, o token de acesso é criado usando o manifesto do grafo, não o manifesto do cliente.  Alterar o manifesto para seu aplicativo nunca fará com que os tokens do Graph pareçam diferentes.  Para validar que as alterações de `accessToken` estão em vigor, solicite um token para seu aplicativo, não para outro aplicativo.  

Pode configurar reclamações opcionais para a sua aplicação através do UI ou do manifesto de aplicação.

1. Aceda ao [Portal do Azure](https://portal.azure.com). Procure e selecione **Azure Active Directory**.
1. A partir da secção **Gerir,** selecione registos de **Aplicações**.
1. Selecione a aplicação que pretende configurar reclamações opcionais na lista.

**Configurar reclamações opcionais através da UI:**

[![Mostra como configurar reclamações opcionais usando a UI](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. A partir da secção **Gerir,** selecione **configuração token (pré-visualização)** .
2. **Selecione Adicionar reclamação opcional**.
3. Selecione o tipo de ficha que pretende configurar.
4. Selecione as reclamações opcionais a adicionar.
5. Clique em **Adicionar**.

**Configurar reclamações opcionais através do manifesto de candidatura:**

[![Mostra como configurar reclamações opcionais usando o manifesto da aplicação](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. A partir da secção **Gerir,** selecione **Manifesto**. Abre-se um editor de manifesto baseado na web, permitindo-lhe editar o manifesto. Opcionalmente, pode selecionar **Transferir**, editar o manifesto localmente e, em seguida, utilizar **Carregar** para o reaplicar à aplicação. Para obter mais informações sobre o manifesto de candidatura, consulte o artigo manifesto de candidatura da [AD Azure.](reference-app-manifest.md)

    A seguinte inscrição manifesto de aplicação adiciona as reclamações opcionais auth_time, ipaddr e upn opcionais para ID, acesso e fichas SAML.

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

2. Quando terminar, clique em **Guardar**. Agora, as declarações opcionais especificadas serão incluídas nos tokens para seu aplicativo.    


### <a name="optionalclaims-type"></a>Tipo de OptionalClaims

Declara as declarações opcionais solicitadas por um aplicativo. Um aplicativo pode configurar declarações opcionais a serem retornadas em cada um dos três tipos de tokens (token de ID, token de acesso, token SAML 2) que pode receber do serviço de token de segurança. O aplicativo pode configurar um conjunto diferente de declarações opcionais a serem retornadas em cada tipo de token. A propriedade OptionalClaims da entidade de aplicativo é um objeto OptionalClaims.

**Tabela 5: propriedades do tipo OptionalClaims**

| Nome        | Tipo                       | Descrição                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de ID JWT. |
| `accessToken` | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de acesso JWT. |
| `saml2Token`  | Coleção (OptionalClaim) | As declarações opcionais retornadas no token SAML.   |

### <a name="optionalclaim-type"></a>Tipo de OptionalClaim

Contém uma declaração opcional associada a um aplicativo ou a uma entidade de serviço. As propriedades token, accessToken e saml2Token do tipo [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) é uma coleção de OptionalClaim.
Se houver suporte para uma declaração específica, você também poderá modificar o comportamento do OptionalClaim usando o campo AdditionalProperties.

**Tabela 6: propriedades do tipo OptionalClaim**

| Nome                 | Tipo                    | Descrição                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | O nome da declaração opcional.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | A origem (objeto de diretório) da declaração. Há declarações predefinidas e declarações definidas pelo usuário das propriedades de extensão. Se o valor de origem for nulo, a declaração será uma declaração opcional predefinida. Se o valor de origem for User, o valor na propriedade Name será a propriedade de extensão do objeto user. |
| `essential`            | Edm.Boolean             | Se o valor for true, a declaração especificada pelo cliente será necessária para garantir uma experiência de autorização suave para a tarefa específica solicitada pelo usuário final. O valor predefinido é false.                                                                                                             |
| `additionalProperties` | Coleção (EDM. String) | Propriedades adicionais da declaração. Se uma propriedade existir nessa coleção, ela modificará o comportamento da declaração opcional especificada na propriedade Name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Configurando declarações opcionais de extensão de diretório

Além do conjunto de declarações opcionais padrão, você também pode configurar tokens para incluir extensões. Para obter mais informações, consulte [Adicionar dados personalizados a recursos usando extensões](https://docs.microsoft.com/graph/extensibility-overview). Esse recurso é útil para anexar informações de usuário adicionais que seu aplicativo pode usar, por exemplo, um identificador adicional ou uma opção de configuração importante que o usuário definiu. Consulte a parte inferior desta página para obter um exemplo.

> [!NOTE]
> - As extensões de esquema de diretório são um recurso somente do Azure AD, portanto, se o manifesto do aplicativo solicitar uma extensão personalizada e um usuário da MSA fizer logon em seu aplicativo, essas extensões não serão retornadas.

### <a name="directory-extension-formatting"></a>Formatação de extensão de diretório

Ao configurar as reclamações opcionais de extensão do diretório utilizando o manifesto de aplicação, utilize o nome completo da extensão (no formato: `extension_<appid>_<attributename>`). O `<appid>` deve corresponder à ID do aplicativo que está solicitando a declaração. 

Dentro do JWT, essas declarações serão emitidas com o seguinte formato de nome: `extn.<attributename>`.

Dentro dos tokens SAML, essas declarações serão emitidas com o seguinte formato de URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Configurando declarações opcionais de grupos

   > [!NOTE]
   > A capacidade de emitir nomes de grupo para usuários e grupos sincronizados a partir do local é visualização pública.

Esta seção aborda as opções de configuração em declarações opcionais para alterar os atributos de grupo usados em declarações de grupo do objectID do grupo padrão para atributos sincronizados do Windows Active Directory local. Você pode configurar declarações opcionais de grupos para seu aplicativo por meio da interface do usuário ou do manifesto do aplicativo.

> [!IMPORTANT]
> Para obter mais detalhes, incluindo advertências importantes para a visualização pública de declarações de grupo de atributos locais, consulte [Configurar declarações de grupo para aplicativos com o Azure ad](../hybrid/how-to-connect-fed-group-claims.md).

**Configurar declarações opcionais de grupos por meio da interface do usuário:**
1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Depois de autenticado, escolha seu locatário do Azure AD selecionando-o no canto superior direito da página
1. Selecione **Azure Active Directory** no menu à esquerda
1. Na seção **gerenciar** , selecione **registros de aplicativo**
1. Selecione o aplicativo para o qual você deseja configurar declarações opcionais na lista
1. Na seção **gerenciar** , selecione **configuração de token (versão prévia)**
2. Selecione **Adicionar declaração de grupos**
3. Selecione os tipos de grupo a serem retornados (**todos os grupos, grupo**de **segurança**ou **DirectoryRole**). A opção **todos os grupos** inclui o grupo de **segurança**, **DirectoryRole**e **DistributionList**
4. Opcional: clique nas propriedades do tipo de token específico para modificar o valor da declaração grupos para conter os atributos do grupo local ou para alterar o tipo de declaração para uma função
5. Clicar em **Guardar**

**Configurar declarações opcionais de grupos por meio do manifesto do aplicativo:**
1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Depois de autenticado, escolha seu locatário do Azure AD selecionando-o no canto superior direito da página
1. Selecione **Azure Active Directory** no menu à esquerda
1. Selecione o aplicativo para o qual você deseja configurar declarações opcionais na lista
1. Na seção **gerenciar** , selecione **manifesto**
3. Adicione a seguinte entrada usando o editor de manifesto:

   Os valores válidos são:

   - "All" (essa opção inclui o The Securitylist, DirectoryRole e DistributionList)
   - SecurityGroup
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

   | Esquema de declarações opcional | Valor |
   |----------|-------------|
   | **name:** | Deve ser "grupos" |
   | **original** | Não utilizado. Omitir ou especificar nulo |
   | **essential:** | Não utilizado. Omitir ou especificar false |
   | **additionalProperties:** | Lista de propriedades adicionais.  Opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   Adicionalmente são necessários apenas um dos "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Se mais de um estiver presente, o primeiro será usado e todos os outros serão ignorados.

   Alguns aplicativos exigem informações de grupo sobre o usuário na declaração de função.  Para alterar o tipo de reclamação para de um grupo reivindicar para uma reivindicação de papel, adicione "emit_as_roles" a propriedades adicionais.  Os valores de grupo serão emitidos na declaração de função.

   > [!NOTE]
   > Se "emit_as_roles" for utilizada, quaisquer Funções de Aplicação configuradas por o utilizador ser atribuído não aparecerão na alegação de funções

**Exemplos:**

1) Emitem grupos como nomes de grupo em tokens de acesso OAuth no formato dnsDomainName\sAMAccountName

    
    **Configuração ui:**

    [![Mostra como configurar reclamações opcionais usando a UI](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Inscrição manifesto de inscrição:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Emita nomes de grupo a serem devolvidos no formato netbiosDomain\sAMAccountName como as funções reivindicam em Tokens de ID SAML e OIDC

    **Configuração ui:**

    [![Mostra como configurar reclamações opcionais usando a UI](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Inscrição manifesto de inscrição:**
    
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
-    Pode utilizar a **configuração Token (pré-visualização)** UI (ver exemplo abaixo)
-    Pode utilizar o **Manifesto** (ver exemplo abaixo). Leia o [documento noções básicas sobre o manifesto do aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) primeiro para obter uma introdução ao manifesto.
-   Também é possível escrever um aplicativo que usa o [API do Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) para atualizar seu aplicativo. O tipo [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) no guia de referência API do Graph pode ajudá-lo a configurar as declarações opcionais.

**Exemplo:** No exemplo abaixo, utilizará a **configuração Token (pré-visualização)** UI e **Manifesto** para adicionar reclamações opcionais aos tokens de acesso, ID e SAML destinados à sua aplicação. Serão adicionadas diferentes reclamações opcionais a cada tipo de ficha que a aplicação pode receber:
-    As fichas de identificação irão agora conter a UPN para utilizadores federados na forma completa (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
-    Os tokens de acesso que outros clientes pedem para este pedido agora incluirão a reivindicação auth_time
-    Os tokens SAML agora conterão a extensão de esquema de diretório do SkypeID (neste exemplo, a ID do aplicativo para esse aplicativo é ab603c56068041afb2f6832e2a17e237). As fichas SAML exporão o ID skype como `extension_skypeId`.

**Configuração ui:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com)

1. Depois de autenticado, escolha seu locatário do Azure AD selecionando-o no canto superior direito da página.

1. Selecione **Azure Active Directory** no menu à esquerda.

1. Na secção **Gerir,** selecione registos de **aplicações**.

1. Localize o aplicativo para o qual você deseja configurar declarações opcionais na lista e clique nele.

1. Na secção **Gerir,** clique na **configuração token (pré-visualização)** .

1. Selecione **Adicionar declaração opcional**, selecione o tipo de token de **ID** , selecione **UPN** na lista de declarações e clique em **Adicionar**.

1. **Selecione Adicionar pedido opcional,** selecione o tipo de ficha de **acesso,** selecione **auth_time** da lista de reclamações e, em seguida, clique em **Adicionar**.

1. Na tela Visão geral da configuração do token, clique no ícone de lápis ao lado de **UPN**, clique na alternância **autenticada externamente** e, em seguida, clique em **salvar**.

1. Selecione **Adicionar declaração opcional**, selecione o tipo de token **SAML** , selecione **Extn. SkypeID** na lista de declarações (aplicável somente se você tiver criado um objeto de usuário do Azure AD chamado SkypeID) e, em seguida, clique em **Adicionar**.

    [![Mostra como configurar reclamações opcionais usando a UI](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Configuração manifesta:**
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Depois de autenticado, escolha seu locatário do Azure AD selecionando-o no canto superior direito da página.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Localize o aplicativo para o qual você deseja configurar declarações opcionais na lista e clique nele.
1. Na secção **Gerir,** clique em **Manifestar** para abrir o editor de manifesto inline.
1. Você pode editar o manifesto diretamente usando este editor. O manifesto segue o esquema para a [entidade de aplicativo](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)e formata automaticamente o manifesto depois de salvo. Novos elementos serão adicionados à propriedade `OptionalClaims`.

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
