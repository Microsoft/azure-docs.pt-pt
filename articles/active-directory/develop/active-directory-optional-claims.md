---
title: Fornecer reclamações opcionais às aplicações da AD Azure
titleSuffix: Microsoft identity platform
description: Como adicionar reclamações personalizadas ou adicionais às fichas SAML 2.0 e JSON Web Tokens (JWT) emitidas pelo Azure Ative Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/22/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 92b3e12cc078326e98df5f42e36fcaddd56bf0c6
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993700"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Como: Fornecer reclamações opcionais à sua aplicação Azure AD

Os desenvolvedores de aplicações podem utilizar reclamações opcionais nas suas aplicações Azure AD para especificar quais as reclamações que pretendem em fichas enviadas para a sua aplicação.

Pode utilizar reclamações opcionais para:

- Selecione reclamações adicionais para incluir em fichas para a sua aplicação.
- Mude o comportamento de certas alegações que a AD Azure devolve em fichas.
- Adicione e aceda a reclamações personalizadas para a sua aplicação.

Para as listas de reclamações padrão, consulte a [documentação](access-tokens.md) de acesso e [id_token](id-tokens.md) reclamações.

Embora as reclamações opcionais sejam suportadas tanto em fichas de formato v1.0 como v2.0, bem como em fichas SAML, fornecem a maior parte do seu valor ao passar de v1.0 para v2.0. Um dos objetivos do ponto final da [plataforma de identidade v2.0 Microsoft](active-directory-appmodel-v2-overview.md) é tamanhos de token mais pequenos para garantir o melhor desempenho dos clientes. Como resultado, várias alegações anteriormente incluídas nos tokens de acesso e identificação já não estão presentes em fichas v2.0 e devem ser solicitadas especificamente por aplicação.

**Quadro 1: Aplicabilidade**

| Tipo de conta               | v1.0 fichas | fichas v2.0 |
|----------------------------|-------------|-------------|
| Conta Pessoal da Microsoft | N/D         | Suportado   |
| Conta do Azure AD           | Suportado   | Suportado   |

## <a name="v10-and-v20-optional-claims-set"></a>v1.0 e v2.0 conjunto de reclamações opcionais

O conjunto de reclamações opcionais disponíveis por padrão para aplicações a utilizar estão listados abaixo. Para adicionar reclamações opcionais personalizadas para a sua aplicação, consulte [Extensões de Diretório,](#configuring-directory-extension-optional-claims)abaixo. Ao adicionar reclamações ao token de **acesso,** as reclamações aplicam-se aos tokens de acesso solicitados *para* a aplicação (uma API web), não reclamações solicitadas *pelo* pedido. Não importa como o cliente aceda à sua API, os dados certos estão presentes no sinal de acesso que é usado para autenticar contra a sua API.

> [!NOTE]
> A maioria destas reclamações pode ser incluída em JWTs para fichas v1.0 e v2.0, mas não tokens SAML, exceto quando anotado na coluna Token Type. As contas de consumo suportam um subconjunto destas reclamações, marcado na coluna "Tipo de Utilizador".  Muitas das reclamações enumeradas não se aplicam aos utilizadores de consumo (não têm inquilino, pelo que `tenant_ctry` não têm qualquer valor).

**Quadro 2: v1.0 e v2.0 conjunto de reclamação opcional**

| Name                       |  Descrição   | Tipo token | Tipo de utilizador | Notas  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Hora da última autenticação do utilizador. Consulte a especificação OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Região do inquilino de recursos | JWT        |           | |
| `home_oid`                 | Para os utilizadores convidados, o id do objeto do utilizador no inquilino do utilizador.| JWT        |           | |
| `sid`                      | ID da sessão, utilizado para a inscrição do utilizador por sessão. | JWT        |  Contas ad si e azure.   |         |
| `platf`                    | Plataforma de dispositivo    | JWT        |           | Restrito a dispositivos geridos que podem verificar o tipo de dispositivo.|
| `verified_primary_email`   | Proveniente do Email PrimaryAuthoritativeEmail do utilizador      | JWT        |           |         |
| `verified_secondary_email` | Proveniente do Email Secundário do Utilizador   | JWT        |           |        |
| `enfpolids`                | Identificação política forçada. Uma lista das iDs de política que foram avaliadas para o utilizador atual. | JWT |  |  |
| `vnet`                     | Informação do especificador VNET. | JWT        |           |      |
| `fwd`                      | Endereço IP.| JWT    |   | Adiciona o endereço IPv4 original do cliente que solicita (quando dentro de um VNET) |
| `ctry`                     | País/região do utilizador | JWT |  | A Azure AD devolve a `ctry` reclamação opcional se estiver presente e o valor da reclamação for um código padrão de duas letras país/região, como FR, JP, SZ, e assim por diante. |
| `tenant_ctry`              | País/região do arrendatário de recursos | JWT | | |
| `xms_pdl`             | Localização de dados preferenciais   | JWT | | Para os inquilinos Multi-Geo, a localização de dados preferida é o código de três letras que mostra a região geográfica em que o utilizador se encontra. Para mais informações, consulte a [documentação Azure AD Connect sobre](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation)a localização de dados preferido .<br/>Por exemplo: para a `APC` Ásia-Pacífico. |
| `xms_pl`                   | Idioma preferido do utilizador  | JWT ||O idioma preferido do utilizador, se definido. Oriundo do inquilino da casa, em cenários de acesso aos hóspedes. Formato LL-CC ("en-us"). |
| `xms_tpl`                  | Língua preferida do inquilino| JWT | | A linguagem preferida do inquilino de recursos, se definido. Formato LL ("en"). |
| `ztdid`                    | ID de implementação de zero toque | JWT | | A identidade do dispositivo utilizada para [o Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | O e-mail endereçado para este utilizador, se o utilizador tiver um.  | JWT | MSA, Azure AD | Este valor é incluído por padrão se o utilizador for um hóspede no inquilino.  Para utilizadores geridos (os utilizadores no interior do arrendatário), deve ser solicitado através desta reclamação opcional ou, apenas em v2.0, com o âmbito OpenID.  Para utilizadores geridos, o endereço de e-mail deve ser definido no [portal de administração](https://portal.office.com/adminportal/home#/users)do Office .|
| `groups`| Formatação opcional para sinistros de grupo |JWT| |Utilizado em conjunto com a definição de Reivindicações de Grupono manifesto de [candidatura,](reference-app-manifest.md)que também deve ser definido. Para mais detalhes consulte [as reivindicações do Grupo](#configuring-groups-optional-claims) abaixo. Para mais informações sobre reivindicações de grupo, consulte [Como configurar as reivindicações](../hybrid/how-to-connect-fed-group-claims.md) do grupo
| `acct`                | Estado da conta dos utilizadores no inquilino. | JWT | | Se o utilizador for membro do inquilino, o valor é `0` . Se são hóspedes, o valor `1` é. |
| `upn`                      | Reivindicação userPrincipalName. | JWT  |           | Embora esta alegação esteja automaticamente incluída, pode especificá-la como uma alegação opcional de anexar propriedades adicionais para modificar o seu comportamento no caso do utilizador convidado.  |

## <a name="v20-specific-optional-claims-set"></a>v2.0 conjunto de reclamações opcionais específicas

Estas reclamações estão sempre incluídas em fichas v1.0 Azure AD, mas não incluídas em fichas v2.0, a menos que solicitado. Estas reclamações são apenas aplicáveis para JWTs (fichas de identificação e fichas de acesso).

**Quadro 3: v2.0 reclamações opcionais apenas**

| JWT Claim     | Name                            | Descrição                                | Notas |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Endereço IP                      | O endereço IP do cliente que o cliente registou.   |       |
| `onprem_sid`  | Identificador de segurança no local |                                             |       |
| `pwd_exp`     | Hora de Expiração da Palavra-Passe        | A data em que a palavra-passe expira. |       |
| `pwd_url`     | Alterar URL de palavra-passe             | Um URL que o utilizador pode visitar para alterar a sua palavra-passe.   |   |
| `in_corp`     | Dentro da Rede da Empresa        | Sinaliza se o cliente está a fazer login na rede corporativa. Se não estiverem, a reclamação não está incluída.   |  Baseado nas definições [de IPs confiáveis](../authentication/howto-mfa-mfasettings.md#trusted-ips) no MFA.    |
| `family_name` | Apelido                       | Fornece o último nome, sobrenome ou nome de família do utilizador, conforme definido no objeto do utilizador. <br>"family_name":"Miller" | Apoiado em MSA e Azure AD. Requer o `profile` alcance.   |
| `given_name`  | Nome próprio                      | Fornece o primeiro ou "dado" nome do utilizador, conforme definido no objeto do utilizador.<br>"given_name": "Frank"                   | Suportado em MSA e Azure AD.  Requer o `profile` alcance. |
| `upn`         | Nome Principal de Utilizador | Um identificador para o utilizador que pode ser utilizado com o parâmetro username_hint.  Não é um identificador durável para o utilizador e não deve ser utilizado para dados chave. | Consulte [propriedades adicionais](#additional-properties-of-optional-claims) abaixo para configurar a reclamação. Requer o `profile` alcance.|

### <a name="additional-properties-of-optional-claims"></a>Propriedades adicionais de reclamações opcionais

Algumas reclamações opcionais podem ser configuradas para alterar a forma como a reclamação é devolvida. Estas propriedades adicionais são usadas principalmente para ajudar a migração de aplicações no local com diferentes expectativas de dados (por exemplo, `include_externally_authenticated_upn_without_hash` ajuda com clientes que não conseguem lidar com marcas de hash `#` () na UPN)

**Quadro 4: Valores para configurar reclamações opcionais**

| Nome da propriedade  | Nome adicional de propriedade | Descrição |
|----------------|--------------------------|-------------|
| `upn`          |                          | Pode ser usado tanto para respostas SAML como JWT, e para fichas v1.0 e v2.0. |
|                | `include_externally_authenticated_upn`  | Inclui o hóspede UPN como armazenado no inquilino de recursos. Por exemplo, `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | O mesmo que acima, exceto que as marcas de hash `#` () são substituídas por sublinhados ( `_` ), por exemplo`foo_hometenant.com_EXT_@resourcetenant.com` |

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

Este objeto OptionalClaims faz com que o token de ID devolvido ao cliente inclua uma reclamação upn com o inquilino adicional e informações de inquilino de recursos. A `upn` reclamação só é alterada no token se o utilizador for hóspede no inquilino (que utiliza um IDP diferente para autenticação).

## <a name="configuring-optional-claims"></a>Configurar reclamações opcionais

> [!IMPORTANT]
> As fichas de acesso são **sempre** geradas usando o manifesto do recurso, não o cliente.  Assim, no pedido `...scope=https://graph.microsoft.com/user.read...` o recurso é a API do Microsoft Graph.  Assim, o token de acesso é criado usando o manifesto Microsoft Graph API, e não o manifesto do cliente.  Alterar o manifesto para a sua aplicação nunca fará com que os tokens para que a API do Microsoft Graph pareça diferente.  Para validar que as suas `accessToken` alterações estão em vigor, solicite um sinal para a sua aplicação e não outra aplicação.

Pode configurar reclamações opcionais para a sua aplicação através do UI ou do manifesto de aplicação.

1. Vá ao [portal Azure.](https://portal.azure.com) Procure e selecione **Azure Active Directory**.
1. A partir da secção **Gerir,** selecione registos de **Aplicações**.
1. Selecione a aplicação que pretende configurar reclamações opcionais na lista.

**Configurar reclamações opcionais através da UI:**

[![Mostra como configurar reclamações opcionais usando a UI](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. A partir da secção **Gerir,** selecione **configuração Token**.
1. **Selecione Adicionar reclamação opcional**.
1. Selecione o tipo de ficha que pretende configurar.
1. Selecione as reclamações opcionais a adicionar.
1. Selecione **Adicionar**.

**Configurar reclamações opcionais através do manifesto de candidatura:**

[![Mostra como configurar reclamações opcionais usando o manifesto da aplicação](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. A partir da secção **Gerir,** selecione **Manifesto**. Abre-se um editor de manifesto baseado na web, permitindo-lhe editar o manifesto. Opcionalmente, pode selecionar **Transferir**, editar o manifesto localmente e, em seguida, utilizar **Carregar** para o reaplicar à aplicação. Para obter mais informações sobre o manifesto de candidatura, consulte o artigo manifesto de candidatura da [AD Azure.](reference-app-manifest.md)

    A seguinte inscrição manifesto de aplicação adiciona as reclamações opcionais auth_time, ipaddr e upn opcionais para ID, acesso e fichas SAML.

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

2. Quando terminar, selecione **Guardar**. Agora, as reclamações opcionais especificadas serão incluídas nos tokens para a sua aplicação.

### <a name="optionalclaims-type"></a>Tipo OpcionaisReclamações

Declara os pedidos opcionais solicitados por um pedido. Uma aplicação pode configurar pedidos opcionais a serem devolvidos em cada um dos três tipos de fichas (ficha de identificação, ficha de acesso, ficha SAML 2) que pode receber do serviço de fichas de segurança. A aplicação pode configurar um conjunto diferente de reclamações opcionais a serem devolvidas em cada tipo de token. A propriedade OptionalClaims da entidade Aplicação é um objeto OptionalClaims.

**Quadro 5: Propriedades do tipo OptionalClaims**

| Name          | Tipo                       | Descrição                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Recolha (Reclamação Opcional) | As reclamações opcionais devolvidas no símbolo de identificação jWT.     |
| `accessToken` | Recolha (Reclamação Opcional) | As reclamações opcionais devolvidas no símbolo de acesso JWT. |
| `saml2Token`  | Recolha (Reclamação Opcional) | As reclamações opcionais devolvidas no símbolo SAML.       |

### <a name="optionalclaim-type"></a>Tipo opcional DeReclamação

Contém uma reclamação opcional associada a uma aplicação ou a um diretor de serviço. As propriedades idToken, accessToken e saml2Token do tipo [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) é uma coleção de OptionalClaim.
Se suportado por uma reclamação específica, também pode modificar o comportamento da Reclamação Opcional utilizando o campo AdicionalProperties.

**Quadro 6: Propriedades do tipo opcionalReclamação**

| Name                   | Tipo                    | Descrição                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | O nome da reclamação opcional.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | A fonte (objeto de diretório) da reclamação. Existem reclamações predefinidas e reclamações definidas pelo utilizador a partir de propriedades de extensão. Se o valor de origem for nulo, a reclamação é uma reclamação opcional predefinida. Se o valor de origem for utilizador, o valor na propriedade do nome é a propriedade de extensão do objeto do utilizador. |
| `essential`            | Edm.Boolean             | Se o valor for verdadeiro, a reclamação especificada pelo cliente é necessária para garantir uma experiência de autorização suave para a tarefa específica solicitada pelo utilizador final. O valor predefinido é false.                                                                                                                 |
| `additionalProperties` | Coleção (Edm.String) | Propriedades adicionais da reclamação. Se existir uma propriedade nesta coleção, modifica o comportamento da reclamação opcional especificada na propriedade do nome.                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>Configurar pedidos opcionais de extensão de diretório

Além do conjunto de reclamações opcionais padrão, também pode configurar fichas para incluir extensões. Para mais informações, consulte [a documentação de extensão do Microsoft Graph .](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0)

O schema e as extensões abertas não são suportados por reclamações opcionais, apenas pelas extensões de diretório de estilo AAD-Graph. Esta funcionalidade é útil para anexar informações adicionais do utilizador que a sua aplicação pode utilizar – por exemplo, um identificador adicional ou uma opção de configuração importante que o utilizador definiu. Veja a parte inferior desta página, por exemplo.

> [!NOTE]
> As extensões de esquema de diretório são uma funcionalidade só para a AD Azure. Se o manifesto da sua aplicação solicitar uma extensão personalizada e um utilizador da MSA entrar na sua aplicação, estas extensões não serão devolvidas.

### <a name="directory-extension-formatting"></a>Formatação de extensão do diretório

Ao configurar as reclamações opcionais de extensão do diretório utilizando o manifesto de aplicação, utilize o nome completo da extensão (no formato: `extension_<appid>_<attributename>` ). O `<appid>` deve coincidir com a identificação do pedido que solicita a reclamação.

Dentro do JWT, estas reclamações serão emitidas com o seguinte formato de nome: `extn.<attributename>` .

Dentro das fichas SAML, estas alegações serão emitidas com o seguinte formato URI:`http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Configuração de reivindicações opcionais de grupos

   > [!NOTE]
   > A capacidade de emitir nomes de grupo para utilizadores e grupos sincronizados a partir do local é A Pré-Visualização Pública.

Esta secção cobre as opções de configuração sob pedidos opcionais para alterar os atributos do grupo utilizados nas reivindicações do grupo padrão do objectID do grupo padrão para atributos sincronizados a partir do Windows Ative Directory no local. Pode configurar os grupos de reclamações opcionais para a sua aplicação através do UI ou do manifesto de aplicação.

> [!IMPORTANT]
> Para mais detalhes, incluindo ressalvas importantes para a visualização pública de reivindicações de grupo a partir de atributos no local, consulte as alegações do [grupo Configure para aplicações com a AD Azure](../hybrid/how-to-connect-fed-group-claims.md).

**Configurar os grupos de reclamações opcionais através da UI:**

1. Inscreva-se no [portal Azure](https://portal.azure.com)
1. Depois de autenticar, escolha o seu inquilino Azure AD selecionando-o a partir do canto superior direito da página
1. Selecione **Azure Ative Directory** a partir do menu à esquerda
1. Na secção **Gerir,** selecione registos de **Aplicativos**
1. Selecione a aplicação que pretende configurar reclamações opcionais na lista
1. Sob a secção **Gerir,** selecione **configuração Token**
1. Selecione **Adicionar grupos**
1. Selecione os tipos de grupo para devolução (**Todos os Grupos,** **SecurityGroup,** ou **Roledeta de Direção**). A opção **Todos os Grupos** inclui **SecurityGroup**, **DirectoryRole**e **DistributionList**
1. Opcional: selecione as propriedades específicas do tipo token para modificar o valor de reivindicação dos grupos para conter nas instalações ou para alterar o tipo de reclamação para uma função
1. Selecione **Guardar**

**Configurar os grupos de reclamações opcionais através do manifesto de candidatura:**

1. Inscreva-se no [portal Azure](https://portal.azure.com)
1. Depois de autenticar, escolha o seu inquilino Azure AD selecionando-o a partir do canto superior direito da página
1. Selecione **Azure Ative Directory** a partir do menu à esquerda
1. Selecione a aplicação que pretende configurar reclamações opcionais na lista
1. Sob a secção **Gerir,** selecione **Manifesto**
1. Adicione a seguinte entrada utilizando o editor manifesto:

   Os valores válidos são:

   - "All" (esta opção inclui SecurityGroup, DirectoryRole e DistributionList)
   - "Grupo de Segurança"
   - "Roleta de Diretório"

   Por exemplo:

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   Por padrão, os Objetos de Grupo serão emitidos no valor de reclamação do grupo.  Para modificar o valor de reclamação para conter nos atributos do grupo de instalações, ou para alterar o tipo de reclamação para a função, utilize a configuração OptionalClaims da seguinte forma:

1. Definir as alegações opcionais de configuração do nome do grupo.

   Se pretender que os grupos no token contenham os atributos do grupo AD nas instalações na secção de reclamações opcionais especifiquem a que tipo token reclamação opcional deve ser aplicada, o nome da reclamação opcional solicitada e quaisquer propriedades adicionais desejadas.  Vários tipos de fichas podem ser listados:

   - idToken para o token id OIDC
   - acessoToken para o token de acesso OAuth
   - Saml2Token para fichas SAML.

   > [!NOTE]
   > O tipo Saml2Token aplica-se tanto às fichas do formato SAML1.1 como ao formato SAML2.0

   Para cada tipo simbólico relevante, modifique os grupos alegando utilizar a secção OptionalClaims no manifesto. O esquema OptionalClaims é o seguinte:

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
   | **fonte:** | Não usado. Omite ou especifique nulo |
   | **essencial:** | Não usado. Omite ou especifique falso |
   | **propriedades adicionais:** | Lista de propriedades adicionais.  Opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   Adicionalmente são necessários apenas um dos "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Se mais do que um estiver presente, o primeiro é usado e qualquer outro ignorado.

   Algumas aplicações requerem informações de grupo sobre o utilizador na reivindicação de funções.  Para alterar o tipo de reclamação para de um grupo reivindicar para uma reivindicação de papel, adicione "emit_as_roles" a propriedades adicionais.  Os valores do grupo serão emitidos na reivindicação do papel.

   > [!NOTE]
   > Se "emit_as_roles" for utilizada, quaisquer Funções de Aplicação configuradas por o utilizador ser atribuído não aparecerão na alegação de funções

**Exemplos:**

1) Emitem grupos como nomes de grupo em tokens de acesso OAuth no formato dnsDomainName\sAMAccountName

    **Configuração ui:**

    [![Mostra como configurar reclamações opcionais usando a UI](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **Inscrição manifesto de inscrição:**

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

2) Emita nomes de grupo a serem devolvidos no formato netbiosDomain\sAMAccountName como as funções reivindicam em Tokens de ID SAML e OIDC

    **Configuração ui:**

    [![Mostra como configurar reclamações opcionais usando a UI](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Inscrição manifesto de inscrição:**

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
Existem várias opções disponíveis para atualizar as propriedades na configuração de identidade de uma aplicação para ativar e configurar reclamações opcionais:

- Pode utilizar o UI de **configuração token** (ver exemplo abaixo)
- Pode utilizar o **Manifesto** (ver exemplo abaixo). Leia primeiro o documento manifesto de [apresentação da aplicação DaD Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) para uma introdução ao manifesto.
- Também é possível escrever uma aplicação que utiliza a [API](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) do Microsoft Graph para atualizar a sua aplicação. O tipo [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) no guia de referência DaPi do Microsoft Graph pode ajudá-lo a configurar as reclamações opcionais.

**Exemplo:**

No exemplo abaixo, utilizará o UI e o **Manifesto** de **configuração Token** para adicionar reclamações opcionais aos tokens de acesso, ID e SAML destinados à sua aplicação. Serão adicionadas diferentes reclamações opcionais a cada tipo de ficha que a aplicação pode receber:

- As fichas de identificação irão agora conter a UPN para utilizadores federados na forma completa `<upn>_<homedomain>#EXT#@<resourcedomain>` ( ).
- Os tokens de acesso que outros clientes pedem para este pedido agora incluirão a reivindicação auth_time
- As fichas SAML agora contêm a extensão do esquema do diretório skypeId (neste exemplo, o ID da aplicação para esta aplicação é ab603c56068041afb2f6832e2a17e237). As fichas SAML exporão o ID skype como `extension_skypeId` .

**Configuração ui:**

1. Inscreva-se no [portal Azure](https://portal.azure.com)

1. Depois de autenticar, escolha o seu inquilino Azure AD selecionando-o a partir do canto superior direito da página.

1. Selecione **Azure Ative Directory** a partir do menu à esquerda.

1. Na secção **Gerir,** selecione registos de **aplicações**.

1. Encontre a aplicação que pretende configurar reclamações opcionais na lista e selecione-as.

1. Na secção **Gerir,** selecione **a configuração token**.

1. **Selecione Adicionar pedido opcional,** selecione o tipo de **ficha de identificação,** selecione **upn** da lista de reclamações e, em seguida, **selecione Adicionar**.

1. **Selecione Adicionar pedido opcional,** selecione o tipo de ficha **de acesso,** selecione **auth_time** da lista de reclamações e, em seguida, selecione **Adicionar**.

1. A partir do ecrã de visão geral da Configuração Token, selecione o ícone do lápis ao lado de **cima,** selecione o toggle **autenticado externamente** e, em seguida, selecione **Save**.

1. **Selecione Adicionar pedido opcional**, selecione o tipo de token **SAML,** selecione **extn.skypeID** da lista de reclamações (apenas aplicável se tiver criado um objeto de utilizador Azure AD chamado skypeID), e, em seguida, selecione **Adicionar**.

    [![Mostra como configurar reclamações opcionais usando a UI](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Configuração manifesta:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Depois de autenticar, escolha o seu inquilino Azure AD selecionando-o a partir do canto superior direito da página.
1. Selecione **Azure Ative Directory** a partir do menu à esquerda.
1. Encontre a aplicação que pretende configurar reclamações opcionais na lista e selecione-as.
1. Na secção **Gerir,** selecione **Manifesto** para abrir o editor de manifesto inline.
1. Pode editar diretamente o manifesto usando este editor. O manifesto segue o esquema para a [entidade Aplicação,](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)e forma automaticamente o manifesto uma vez guardado. Novos elementos serão adicionados à `OptionalClaims` propriedade.

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

1. Quando terminar de atualizar o manifesto, selecione **Save** para salvar o manifesto.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre as reclamações padrão fornecidas pela Azure AD.

- [Tokens de ID](id-tokens.md)
- [Tokens de acesso](access-tokens.md)
