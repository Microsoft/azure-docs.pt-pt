---
title: Fornecer reclamações opcionais para aplicações AD Azure
titleSuffix: Microsoft identity platform
description: Como adicionar reclamações personalizadas ou adicionais aos tokens SAML 2.0 e JSON Web Tokens (JWT) emitidos pela Azure Ative Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: f751c45b12ec2c8f6f09080b01b24f59af1fc0d0
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85478336"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Como: Fornecer reclamações opcionais à sua app AD Azure

Os desenvolvedores de aplicações podem usar reclamações opcionais nas suas aplicações AZure AD para especificar quais as alegações que pretendem em fichas enviadas para a sua aplicação.

Pode utilizar reclamações opcionais para:

- Selecione reclamações adicionais para incluir em fichas para a sua aplicação.
- Alterar o comportamento de certas alegações de que a Azure AD regressa em fichas.
- Adicione e aceda reclamações personalizadas para a sua aplicação.

Para as listas de reclamações padrão, consulte o [token de acesso](access-tokens.md) e [id_token](id-tokens.md) documentação de reclamações.

Embora as reclamações opcionais sejam suportadas em fichas de formato v1.0 e v2.0, bem como em fichas SAML, fornecem a maior parte do seu valor ao passar de v1.0 para v2.0. Um dos objetivos do ponto final da [plataforma de identidade V2.0 microsoft](active-directory-appmodel-v2-overview.md) é tamanhos de token mais pequenos para garantir o melhor desempenho dos clientes. Como resultado, várias reclamações anteriormente incluídas no acesso e fichas de identificação já não estão presentes em fichas v2.0 e devem ser solicitadas especificamente por aplicação.

**Quadro 1: Aplicabilidade**

| Tipo de Conta               | v1.0 fichas | v2.0 fichas |
|----------------------------|-------------|-------------|
| Conta pessoal da Microsoft | N/D         | Suportado   |
| Conta do Azure AD           | Suportado   | Suportado   |

## <a name="v10-and-v20-optional-claims-set"></a>v1.0 e v2.0 conjunto de reclamações opcionais

O conjunto de reclamações opcionais disponíveis por padrão para aplicações a utilizar estão listados abaixo. Para adicionar reclamações opcionais personalizadas para a sua aplicação, consulte [Extensões do Diretório,](#configuring-directory-extension-optional-claims)abaixo. Ao adicionar reclamações ao **token de acesso,** as reclamações aplicam-se aos tokens de acesso solicitados *para* a aplicação (uma API web), não reclamações solicitadas *pela* aplicação. Não importa como o cliente acede à sua API, os dados certos estão presentes no token de acesso que é usado para autenticar contra a sua API.

> [!NOTE]
> A maioria destas reclamações pode ser incluída em JWTs para tokens v1.0 e v2.0, mas não fichas SAML, exceto quando anotadas na coluna Token Type. As contas dos consumidores suportam um subconjunto destas reclamações, marcado na coluna "Tipo de Utilizador".  Muitas das reclamações listadas não se aplicam aos utilizadores de consumidores (não têm inquilino, pelo `tenant_ctry` que não têm qualquer valor).

**Quadro 2: v1.0 e v2.0 conjunto de reclamações opcionais**

| Name                       |  Descrição   | Tipo token | Tipo de utilizador | Notas  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Hora em que o utilizador foi autenticado pela última vez. Consulte a especificação openID connect.| JWT        |           |  |
| `tenant_region_scope`      | Região do inquilino de recursos | JWT        |           | |
| `home_oid`                 | Para os utilizadores convidados, o iD do objeto do utilizador no inquilino do utilizador.| JWT        |           | |
| `sid`                      | ID de sessão, usado para a sessão de sessão de sessão. | JWT        |  Contas pessoais e azure AD.   |         |
| `platf`                    | Plataforma de dispositivo    | JWT        |           | Restrito a dispositivos geridos que possam verificar o tipo de dispositivo.|
| `verified_primary_email`   | Proveniente do Correio da AustráliaRritativa Primária do utilizador      | JWT        |           |         |
| `verified_secondary_email` | Proveniente do Correio Secundário de Autoritária do Utilizador   | JWT        |           |        |
| `enfpolids`                | Identificações de política forçadas. Uma lista dos IDs de política que foram avaliados para o utilizador atual. | JWT |  |  |
| `vnet`                     | Informações do especificador VNET. | JWT        |           |      |
| `fwd`                      | Endereço IP.| JWT    |   | Adiciona o endereço IPv4 original do cliente que solicita (quando dentro de um VNET) |
| `ctry`                     | País/região do utilizador | JWT |  | A Azure AD devolve a `ctry` reclamação opcional se estiver presente e o valor da reclamação for um código padrão de dois letras país/região, como FR, JP, SZ, e assim por diante. |
| `tenant_ctry`              | País/região do arrendatário de recursos | JWT | | |
| `xms_pdl`             | Localização de dados preferenciais   | JWT | | Para os inquilinos multi-Geo, a localização de dados preferida é o código de três letras que mostra a região geográfica em que o utilizador se encontra. Para obter mais informações, consulte a documentação do [Azure AD Connect sobre a localização de dados preferenciais.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation)<br/>Por exemplo: `APC` para a Ásia-Pacífico. |
| `xms_pl`                   | Linguagem preferida do utilizador  | JWT ||O idioma preferido do utilizador, se for definido. Proveniente do inquilino de casa, em cenários de acesso a hóspedes. LL-CC formatado ("en-us"). |
| `xms_tpl`                  | Língua preferida do inquilino| JWT | | A linguagem preferida do inquilino de recursos, se definido. LL formatado ("en"). |
| `ztdid`                    | ID de implementação de toque zero | JWT | | A identidade do dispositivo utilizada para [o Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | O e-mail endereçada para este utilizador, se o utilizador tiver um.  | JWT | MSA, Azure AD | Este valor é incluído por padrão se o utilizador for um hóspede no inquilino.  Para os utilizadores geridos (os utilizadores dentro do arrendatário), este deve ser solicitado através desta reclamação opcional ou, apenas em v2.0, com o âmbito OpenID.  Para os utilizadores geridos, o endereço de e-mail deve ser definido no [portal de administração do Office](https://portal.office.com/adminportal/home#/users).|
| `groups`| Formatação opcional para reclamações em grupo |JWT| |Utilizado em conjunto com o GrupoMembershipClas definindo no manifesto de [aplicação,](reference-app-manifest.md)que também deve ser definido. Para mais detalhes consulte [as reclamações do Grupo](#configuring-groups-optional-claims) abaixo. Para obter mais informações sobre reclamações em grupo, consulte [Como configurar as reclamações do grupo](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`                | Estado da conta dos utilizadores no inquilino | JWT | | Se o utilizador for membro do arrendatário, o valor é `0` . Se são um hóspede, o valor `1` é. |
| `upn`                      | UserPrincipalName | JWT  |           | Embora esta alegação seja automaticamente incluída, pode especirá-la como uma reivindicação opcional para anexar propriedades adicionais para modificar o seu comportamento no caso do utilizador convidado.  |
| `idtyp`                    | Tipo token   | Fichas de acesso JWT | Especial: apenas em fichas de acesso só para aplicações |  Valor é `app` quando o token é um símbolo apenas de aplicação. Esta é a forma mais precisa de uma API determinar se um token é um token de aplicação ou um token app+user.|

## <a name="v20-specific-optional-claims-set"></a>v2.0 conjunto de reclamações opcionais específicas

Estas reclamações estão sempre incluídas em fichas AD v1.0 Azure, mas não incluídas em fichas v2.0, a menos que solicitado. Estas reclamações só são aplicáveis aos JWTs (fichas de ID e tokens de acesso).

**Quadro 3: v2.0 apenas pedidos opcionais**

| Reivindicação JWT     | Name                            | Descrição                                | Notas |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Endereço IP                      | O endereço IP do cliente iniciou a sua sessão.   |       |
| `onprem_sid`  | Identificador de Segurança no Local |                                             |       |
| `pwd_exp`     | Hora de Expiração da Palavra-Passe        | A data em que a palavra-passe expira. |       |
| `pwd_url`     | Alterar URL de palavra-passe             | Um URL que o utilizador pode visitar para alterar a sua palavra-passe.   |   |
| `in_corp`     | Dentro da Rede da Empresa        | Sinaliza se o cliente está a fazer login na rede corporativa. Se não estiverem, a reclamação não está incluída.   |  Baseado nas [definições de IPs confiáveis](../authentication/howto-mfa-mfasettings.md#trusted-ips) no MFA.    |
| `family_name` | Apelido                       | Fornece o sobrenome, apelido ou nome de família do utilizador, conforme definido no objeto do utilizador. <br>"family_name":"Miller" | Suportado em MSA e Azure AD. Requer o `profile` alcance.   |
| `given_name`  | Nome próprio                      | Fornece o primeiro nome ou "dado" do utilizador, conforme definido no objeto do utilizador.<br>"given_name": "Frank"                   | Suportado em MSA e Azure AD .  Requer o `profile` alcance. |
| `upn`         | Nome Principal de Utilizador | Um identificador para o utilizador que pode ser utilizado com o parâmetro username_hint.  Não é um identificador durável para o utilizador e não deve ser utilizado para os dados-chave. | Consulte [propriedades adicionais](#additional-properties-of-optional-claims) abaixo para configuração da reclamação. Requer o `profile` alcance.|

### <a name="additional-properties-of-optional-claims"></a>Propriedades adicionais de créditos opcionais

Algumas reclamações opcionais podem ser configuradas para alterar a forma como a reclamação é devolvida. Estas propriedades adicionais são usadas principalmente para ajudar a migração de aplicações no local com diferentes expectativas de dados (por exemplo, `include_externally_authenticated_upn_without_hash` ajuda com clientes que não conseguem lidar com marcas de haxixe `#` () na UPN)

**Quadro 4: Valores para configurar reclamações opcionais**

| Nome da propriedade  | Nome de propriedade adicional | Descrição |
|----------------|--------------------------|-------------|
| `upn`          |                          | Pode ser usado tanto para respostas SAML como JWT, e para tokens v1.0 e v2.0. |
|                | `include_externally_authenticated_upn`  | Inclui o hóspede UPN como armazenado no inquilino de recursos. Por exemplo, `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | O mesmo que acima, exceto que as marcas de haxixe são `#` substituídas por sublinhados ( `_` ), por exemplo`foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Exemplo de propriedades adicionais

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

Este objeto OpcionalClaims faz com que o símbolo de identificação devolvido ao cliente para incluir uma reclamação de upn com o inquilino adicional e informações de inquilino de recursos. A `upn` reclamação só é alterada no token se o utilizador for um hóspede no arrendatário (que utiliza um IDP diferente para autenticação).

## <a name="configuring-optional-claims"></a>Configurar pedidos opcionais

> [!IMPORTANT]
> Os tokens de acesso são **sempre** gerados usando o manifesto do recurso, não o cliente.  Assim, no `...scope=https://graph.microsoft.com/user.read...` pedido, o recurso é a Microsoft Graph API.  Assim, o token de acesso é criado usando o manifesto API do Gráfico da Microsoft, e não o manifesto do cliente.  Alterar o manifesto da sua aplicação nunca fará com que os tokens para a API do Microsoft Graph pareçam diferentes.  Para validar que as suas `accessToken` alterações estão em vigor, solicite um token para a sua aplicação, e não outra app.

Pode configurar reclamações opcionais para a sua aplicação através do UI ou manifesto de aplicação.

1. Vá ao [portal Azure.](https://portal.azure.com) Procure e selecione **Azure Active Directory**.
1. A partir da secção **Gerir,** selecione **registos de Aplicações.**
1. Selecione a aplicação para a inscrição que pretende configurar pedidos opcionais na lista.

**Configurar pedidos opcionais através da UI:**

[![Mostra como configurar reclamações opcionais usando o UI](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. A partir da secção **Gerir,** selecione **a configuração Token**.
1. **Selecione Adicionar reclamação opcional**.
1. Selecione o tipo de símbolo que deseja configurar.
1. Selecione as reclamações opcionais a adicionar.
1. Selecione **Adicionar**.

**Configurar reclamações opcionais através do manifesto de aplicação:**

[![Mostra como configurar reclamações opcionais usando o manifesto da aplicação](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Na secção **Gerir,** selecione **Manifesto**. Um editor manifesto baseado na web abre, permitindo-lhe editar o manifesto. Opcionalmente, pode selecionar **Transferir**, editar o manifesto localmente e, em seguida, utilizar **Carregar** para o reaplicar à aplicação. Para obter mais informações sobre o manifesto de aplicação, consulte o [artigo manifesto de aplicação AZure AD](reference-app-manifest.md).

    A seguinte aplicação manifesto de entrada adiciona o auth_time, ipaddr e upn pedidos opcionais para iD, acesso e fichas SAML.

    ```json
    "optionalClaims": {
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

2. Quando terminar, **selecione Save**. Agora, as reclamações opcionais especificadas serão incluídas nos tokens para a sua aplicação.

### <a name="optionalclaims-type"></a>Opcional Tipo Desempresa

Declara os pedidos facultativos solicitados por um pedido. Uma aplicação pode configurar pedidos opcionais para serem devolvidos em cada um dos três tipos de fichas (ficha de ID, token de acesso, ficha SAML 2) que pode receber do serviço de ficha de segurança. A aplicação pode configurar um conjunto diferente de pedidos opcionais a serem devolvidos em cada tipo de token. O opcionalClaims propriedade da entidade Aplicação é um objeto OpcionalClaims.

**Quadro 5: OpcionaisClaims tipo propriedades**

| Name          | Tipo                       | Descrição                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Coleção (OpcionalClaim) | As reclamações opcionais devolvidas no token JWT ID.     |
| `accessToken` | Coleção (OpcionalClaim) | As reclamações opcionais devolvidas no token de acesso JWT. |
| `saml2Token`  | Coleção (OpcionalClaim) | As reclamações opcionais devolvidas no token SAML.       |

### <a name="optionalclaim-type"></a>Opcional TipoClaim

Contém uma reclamação opcional associada a uma aplicação ou a um principal de serviço. As propriedades idToken, accessToken e saml2Token do tipo [OpcionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) é uma coleção de OpcionalClaim.
Se suportado por uma reclamação específica, também pode modificar o comportamento do OpcionalClaim utilizando o campo Deproperties Adicionais.

**Quadro 6: Propriedades do tipo OpcionalClaim**

| Name                   | Tipo                    | Descrição                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | O nome da reclamação opcional.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | A fonte (objeto de diretório) da reclamação. Existem reclamações predefinidas e reclamações definidas pelo utilizador a partir de propriedades de extensão. Se o valor de origem for nulo, o pedido é uma reclamação opcional predefinida. Se o valor de origem for o utilizador, o valor na propriedade do nome é a propriedade de extensão do objeto do utilizador. |
| `essential`            | Edm.Boolean             | Se o valor for verdadeiro, a reclamação especificada pelo cliente é necessária para garantir uma experiência de autorização suave para a tarefa específica solicitada pelo utilizador final. O valor predefinido é false.                                                                                                                 |
| `additionalProperties` | Coleção (Edm.String) | Propriedades adicionais da reclamação. Se existir um imóvel nesta coleção, modifica o comportamento da reclamação opcional especificada na propriedade do nome.                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>Configurar pedidos opcionais de extensão de diretório

Além do conjunto de reclamações opcionais padrão, também pode configurar fichas para incluir extensões. Para obter mais informações, consulte [a extensão do Microsoft GraphProperty documentação](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0).

As extensões de esquema e abertas não são suportadas por reclamações opcionais, apenas as extensões de diretório de estilo AAD-Graph. Esta funcionalidade é útil para anexar informações adicionais do utilizador que a sua aplicação pode utilizar – por exemplo, um identificador adicional ou uma opção de configuração importante que o utilizador definiu. Consulte a parte inferior desta página para dar um exemplo.

> [!NOTE]
> As extensões de esquema de diretório são uma característica Azure AD apenas. Se o manifesto da sua aplicação solicitar uma extensão personalizada e um utilizador MSA entrar na sua aplicação, estas extensões não serão devolvidas.

### <a name="directory-extension-formatting"></a>Formatação de extensão de diretório

Ao configurar pedidos opcionais de extensão de diretório utilizando o manifesto de aplicação, utilize o nome completo da extensão (no formato: `extension_<appid>_<attributename>` ). O `<appid>` deve coincidir com a identificação do pedido de reclamação.

Dentro do JWT, estas alegações serão emitidas com o seguinte formato de nome: `extn.<attributename>` .

Dentro dos tokens SAML, estas alegações serão emitidas com o seguinte formato URI:`http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Configurar reivindicações opcionais de grupos

   > [!NOTE]
   > A capacidade de emitir nomes de grupo para utilizadores e grupos sincronizados a partir do local é visualização pública.

Esta secção abrange as opções de configuração em pedidos opcionais para alterar os atributos do grupo utilizados nas reclamações do grupo padrão do objectID do grupo padrão para atributos sincronizados a partir do Windows Ative Directy. Pode configurar pedidos opcionais de grupos para a sua aplicação através do UI ou manifesto de aplicação.

> [!IMPORTANT]
> Para obter mais detalhes, incluindo ressalvas importantes para a visualização pública de reclamações de grupos de atributos no local, consulte as reclamações do [grupo Configure para aplicações com Azure AD](../hybrid/how-to-connect-fed-group-claims.md).

**Configurar reivindicações opcionais de grupos através da UI:**

1. Inscreva-se no [portal Azure](https://portal.azure.com)
1. Depois de autenticar, escolha o seu inquilino Azure AD selecionando-o a partir do canto superior direito da página
1. Selecione **Azure Ative Directory** a partir do menu à esquerda
1. Na secção **Gerir,** selecione **registos de Aplicações**
1. Selecione a aplicação para a inscrição que pretende configurar pedidos opcionais na lista
1. Na secção **Gerir,** selecione **a configuração token**
1. Selecione **adicionar grupos reivindicação**
1. Selecione os tipos de grupo para devolver **(Todos os grupos,** **Grupo de Segurança,** ou **Diretório).** A opção **All Groups** inclui **SecurityGroup,** **DirectyRole**e **DistributionList**
1. Opcional: selecione as propriedades específicas do tipo símbolo para modificar o valor de reivindicação dos grupos para conter nos atributos do grupo de instalações ou para alterar o tipo de reclamação para uma função
1. Selecione **Guardar**

**Configurar pedidos opcionais de grupos através do manifesto de aplicação:**

1. Inscreva-se no [portal Azure](https://portal.azure.com)
1. Depois de autenticar, escolha o seu inquilino Azure AD selecionando-o a partir do canto superior direito da página
1. Selecione **Azure Ative Directory** a partir do menu à esquerda
1. Selecione a aplicação para a inscrição que pretende configurar pedidos opcionais na lista
1. Na secção **Gerir,** selecione **Manifesto**
1. Adicione a seguinte entrada utilizando o editor manifesto:

   Os valores válidos são:

   - "All" (esta opção inclui SecurityGroup, DirectyRole e DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   Por exemplo:

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   Por predefinição, os ObjectIDs do grupo serão emitidos no valor de reclamação do grupo.  Para modificar o valor de reclamação para conter nos atributos do grupo de instalações, ou para alterar o tipo de reclamação para a função, utilize a configuração OptionalClaims da seguinte forma:

1. Definir pedidos opcionais de configuração do nome de grupo.

   Se pretender que os grupos no token contenham os atributos do grupo AD nas instalações na secção de reclamações opcionais, especifique a que sinal de token pedido deve ser aplicado, o nome da reclamação opcional solicitada e quaisquer propriedades adicionais desejadas.  Vários tipos de token podem ser listados:

   - idToken para o token OIDC ID
   - accessToken para o token de acesso OAuth
   - Saml2Token para fichas SAML.

   > [!NOTE]
   > O tipo Saml2Token aplica-se tanto a fichas de formato SAML1.1 como SAML2.0

   Para cada tipo de token relevante, modifique os grupos que pretendem utilizar a secção OpcionalClaims no manifesto. O esquema OpcionalClaims é o seguinte:

    ```json
    {
        "name": "groups",
        "source": null,
        "essential": false,
        "additionalProperties": []
    }
    ```

   | Esquema de reclamações opcionais | Valor |
   |----------|-------------|
   | **nome:** | Devem ser "grupos" |
   | **Fonte:** | Não usado. Omitir ou especificar nulo |
   | **essencial:** | Não usado. Omitir ou especificar falso |
   | **Adicionalidades:** | Lista de propriedades adicionais.  As opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   Em outros Bens, é necessário apenas um dos "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Se mais de um está presente, o primeiro é usado e quaisquer outros ignorados.

   Algumas aplicações requerem informações de grupo sobre o utilizador na reivindicação de funções.  Para alterar o tipo de reclamação para de uma reivindicação de grupo para uma reivindicação de papel, adicione "emit_as_roles" a propriedades adicionais.  Os valores do grupo serão emitidos na reivindicação do papel.

   > [!NOTE]
   > Se for utilizada "emit_as_roles" quaisquer Funções de Aplicação configuradas que o utilizador seja atribuído não aparecerão na alegação de função

**Exemplos:**

1) Emit grupos como nomes de grupo em tokens de acesso OAuth em dnsDomainName\sAMAccountName formato

    **Configuração de UI:**

    [![Mostra como configurar reclamações opcionais usando o UI](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **Inscrição manifesto de aplicação:**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) Emit nomes de grupo a serem devolvidos em formato netbiosDomain\sAMAccountName como as funções reivindicam em TOKens DE ID SAML e OIDC

    **Configuração de UI:**

    [![Mostra como configurar reclamações opcionais usando o UI](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Inscrição manifesto de aplicação:**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>Exemplo de reclamações opcionais

Nesta secção, pode percorrer um cenário para ver como pode utilizar a funcionalidade de reclamações opcionais para a sua aplicação.
Existem várias opções disponíveis para atualizar as propriedades na configuração de identidade de uma aplicação para permitir e configurar reclamações opcionais:

- Pode utilizar a **UI de configuração token** (ver exemplo abaixo)
- Pode utilizar o **Manifesto** (ver exemplo abaixo). Leia o [Documento manifesto de aplicação Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) primeiro para uma introdução ao manifesto.
- Também é possível escrever uma aplicação que utilize a API do [Microsoft Graph](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) para atualizar a sua aplicação. O tipo [OpcionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) no guia de referência da Microsoft Graph API pode ajudá-lo a configurar as reclamações opcionais.

**Exemplo:**

No exemplo abaixo, utilizará a **configuração Token** UI e **Manifesto** para adicionar pedidos opcionais aos tokens de acesso, ID e SAML destinados à sua aplicação. Serão adicionadas diferentes reclamações opcionais a cada tipo de símbolo que a aplicação pode receber:

- Os tokens de ID passarão a conter a UPN para utilizadores federados na forma completa ( `<upn>_<homedomain>#EXT#@<resourcedomain>` ).
- Os tokens de acesso que outros clientes solicitam para esta aplicação passarão a incluir a reclamação auth_time
- Os tokens SAML passarão a conter a extensão do esquema do diretório skypeId (neste exemplo, o ID da aplicação para esta aplicação é ab603c56068041afb2f6832e2a17e237). Os tokens SAML vão expor o ID skype como `extension_skypeId` .

**Configuração de UI:**

1. Inscreva-se no [portal Azure](https://portal.azure.com)

1. Depois de autenticar, escolha o seu inquilino Azure AD selecionando-o no canto superior direito da página.

1. Selecione **Azure Ative Directory** a partir do menu da mão esquerda.

1. Na secção **Gerir,** selecione **registos de Aplicações.**

1. Encontre a aplicação que pretende configurar reclamações opcionais para a lista e selecione-a.

1. Na secção **Gerir,** selecione **a configuração Token**.

1. **Selecione Adicionar reclamação opcional**, selecione o tipo de símbolo de **ID,** selecione **upn** da lista de reclamações e, em seguida, selecione **Adicionar**.

1. **Selecione Adicionar reclamação opcional**, selecione o tipo de token de **acesso,** selecione **auth_time** da lista de reclamações e, em seguida, selecione **Adicionar**.

1. A partir do ecrã geral da configuração token, selecione o ícone do lápis ao lado **do upn**, selecione o toggle **autenticado externamente** e, em seguida, selecione **Guardar**.

1. **Selecione Adicionar a reclamação opcional**, selecione o tipo de token **SAML,** selecione **extn.skypeID** da lista de reclamações (apenas aplicável se tiver criado um objeto de utilizador Azure AD chamado skypeID) e, em seguida, selecione **Add**.

    [![Mostra como configurar reclamações opcionais usando o UI](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Configuração manifesta:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Depois de autenticar, escolha o seu inquilino Azure AD selecionando-o no canto superior direito da página.
1. Selecione **Azure Ative Directory** a partir do menu da mão esquerda.
1. Encontre a aplicação que pretende configurar reclamações opcionais para a lista e selecione-a.
1. Na secção **Gerir,** selecione **Manifesto** para abrir o editor manifesto inline.
1. Pode editar diretamente o manifesto usando este editor. O manifesto segue o esquema para a [entidade Aplicação,](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)e automaticamente forma o manifesto uma vez guardado. Novos elementos serão adicionados à `OptionalClaims` propriedade.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
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

1. Quando terminar de atualizar o manifesto, **selecione Guardar** para guardar o manifesto.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as reclamações padrão fornecidas pela Azure AD.

- [Tokens de ID](id-tokens.md)
- [Tokens de acesso](access-tokens.md)
