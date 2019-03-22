---
title: Personalizar afirmações emitidas nos tokens para uma aplicação específica no inquilino do Azure AD (pré-visualização pública)
description: Esta página descreve o mapeamento de afirmações do Azure Active Directory.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.reviewer: paulgarn, hirsin, jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: af00c311b406a0bae285a00f0d537ce9bae3aa7e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102126"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Como: Personalizar afirmações emitidas nos tokens de um aplicativo específico num inquilino (pré-visualização)

> [!NOTE]
> Esta funcionalidade substitui e prevalece sobre o [personalização de afirmações](active-directory-saml-claims-customization.md) oferecidos através do portal hoje em dia. No mesmo aplicativo, se personalizar afirmações com o portal além do método de gráfico/PowerShell detalhado neste documento, tokens emitidos para que o aplicativo irá ignorar a configuração no portal do. As configurações feitas por meio de métodos detalhados neste documento não serão refletidas no portal.

Esta funcionalidade é usada por administradores de inquilinos para personalizar afirmações emitidas nos tokens para uma aplicação específica no seu inquilino. Pode utilizar políticas de mapeamento de afirmações para:

- Selecione quais declarações serão incluídas nos tokens.
- Crie tipos de afirmação que ainda não existam.
- Escolha ou alterar a origem de dados emitidos nas afirmações específicas.

> [!NOTE]
> Esta capacidade está atualmente em pré-visualização pública. Esteja preparado para reverter ou remover todas as alterações. A funcionalidade está disponível em qualquer subscrição do Azure Active Directory (Azure AD) durante a pré-visualização pública. No entanto, quando a funcionalidade torna-se disponível em geral, alguns aspetos da funcionalidade podem exigir uma subscrição do Azure AD premium. Esta funcionalidade suporta configurar políticas de mapeamento de afirmações para os protocolos WS-Fed, SAML, OAuth e OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Tipo de política de mapeamento de afirmações

No Azure AD, uma **política** objeto representa um conjunto de regras de imposto em aplicativos individuais ou em todos os aplicativos numa organização. Cada tipo de política tem uma estrutura exclusiva, com um conjunto de propriedades que, em seguida, são aplicadas a objetos para o qual são atribuídos.

A política de mapeamento é um tipo de afirmações **política** objeto que modifica as declarações emitidas nos tokens emitidos para aplicações específicas.

## <a name="claim-sets"></a>Conjuntos de afirmações

Existem alguns conjuntos de declarações que definem como e quando eles são usados nos tokens.

| Conjunto de afirmações | Descrição |
|---|---|
| Conjunto de afirmações de núcleo | Estão presentes em cada token, independentemente da política. Essas declarações também são consideradas restrito e não podem ser modificadas. |
| Conjunto de afirmações básico | Inclui as afirmações que são emitidas por padrão para tokens (além de conjunto de afirmações core). Pode omitir ou modificar afirmações básicas com as políticas de mapeamento das afirmações. |
| Conjunto de afirmações restrito | Não é possível modificar a política a utilizar. Não é possível alterar a origem de dados, e nenhuma transformação é aplicada ao gerar essas declarações. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabela 1: Conjunto de afirmações de restrito de JSON Web Token (JWT)

|Tipo de afirmação (nome)|
| ----- |
|_claim_names|
|_claim_sources|
|access_token|
|account_type|
|acr|
|ator|
|actortoken|
|aio|
|altsecid|
|amr|
|app_chain|
|app_displayname|
|app_res|
|appctx|
|appctxsender|
|AppID|
|appidacr|
|asserção|
|at_hash|
|aud|
|auth_data|
|auth_time|
|authorization_code|
|azp|
|azpacr|
|c_hash|
|ca_enf|
|cc|
|cert_token_use|
|client_id|
|cloud_graph_host_name|
|cloud_instance_name|
|cnf|
|Código|
|controlos|
|credential_keys|
|csr|
|csr_type|
|DeviceID|
|dns_names|
|domain_dns_name|
|domain_netbios_name|
|e_exp|
|e-mail|
|endpoint|
|enfpolids|
|exp|
|expires_on|
|grant_type|
|gráfico|
|group_sids|
|grupos|
|hasgroups|
|hash_alg|
|home_oid|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`|
|iat|
|identityprovider|
|idp|
|in_corp|
|instância|
|ipaddr|
|isbrowserhostedapp|
|ISS|
|jwk|
|key_id|
|key_type|
|mam_compliance_url|
|mam_enrollment_url|
|mam_terms_of_use_url|
|mdm_compliance_url|
|mdm_enrollment_url|
|mdm_terms_of_use_url|
|nameid|
|nbf|
|netbios_name|
|Valor de uso único|
|OID|
|on_prem_id|
|onprem_sam_account_name|
|onprem_sid|
|openid2_id|
|palavra-passe|
|platf|
|polids|
|pop_jwk|
|preferred_username|
|previous_refresh_token|
|primary_sid|
|PUID|
|pwd_exp|
|pwd_url|
|redirect_uri|
|refresh_token|
|refreshtoken|
|request_nonce|
|Recurso|
|função|
|funções|
|scope|
|scp|
|SID|
|assinatura|
|signin_state|
|src1|
|src2|
|sub|
|tbid|
|tenant_display_name|
|tenant_region_scope|
|thumbnail_photo|
|NIF|
|tokenAutologonEnabled|
|trustedfordelegation|
|unique_name|
|upn|
|user_setting_sync_url|
|o nome de utilizador|
|uti|
|ver|
|verified_primary_email|
|verified_secondary_email|
|wids|
|win_ver|

### <a name="table-2-saml-restricted-claim-set"></a>Tabela 2: Conjunto de afirmações de restrito de SAML

|Tipo de afirmação (URI)|
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1] `|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Propriedades da política de mapeamento de afirmações

Para controlar quais as afirmações são emitidas e de onde vêm os dados, use as propriedades de uma política de mapeamento das afirmações. Se não for definida uma política, os tokens de problemas do sistema que incluem o núcleo de afirmação conjunto, o conjunto de afirmações básica e quaisquer [afirmações opcionais](active-directory-optional-claims.md) que o aplicativo tenha optado por receber.

### <a name="include-basic-claim-set"></a>Incluir o conjunto de afirmações básico

**Cadeia de caracteres:** IncludeBasicClaimSet

**Tipo de dados:** Valor booleano (True ou False)

**Resumo:** Esta propriedade determina se o conjunto de afirmações básica está incluído nos tokens afetados por esta política. 

- Se definido como True, todas as afirmações no conjunto de afirmação básicas é emitido nos tokens afetados pela política. 
- Se definido como False, afirmações no conjunto de afirmações básica de não está em tokens, a menos que eles são adicionados individualmente na propriedade de esquema de afirmações da mesma política.

> [!NOTE] 
> Afirmações no conjunto de afirmações de núcleo estão presentes em cada token, independentemente do que esta propriedade é definida como. 

### <a name="claims-schema"></a>Esquema de afirmações

**Cadeia de caracteres:** ClaimsSchema

**Tipo de dados:** BLOBs JSON com uma ou mais entradas de esquema de afirmação

**Resumo:** Esta propriedade define que afirmações estão presentes nos tokens de afetados pela política, além do conjunto de afirmações básica e o conjunto de declarações de núcleo.
Para cada entrada de esquema de afirmação definida nessa propriedade, são necessárias determinadas informações. Especifique os dados proveniência (**valor** ou **par de ID de origem/**), e que os dados de afirmação é emitida em (**tipo de afirmação**).

### <a name="claim-schema-entry-elements"></a>Elementos de entrada do esquema de afirmação

**Valor:** O elemento de valor define um valor estático, como os dados sejam emitidos na afirmação.

**Par de origem/ID:** Os elementos de origem e o ID da definem onde os dados da declaração são obtidos a partir. 

Defina o elemento de origem para um dos seguintes valores: 

- "user": Os dados na afirmação são uma propriedade no objeto User. 
- "aplicação": Os dados na afirmação são uma propriedade sobre o principal de serviço de aplicação (cliente). 
- "resource": Os dados na afirmação são uma propriedade sobre o principal de serviço de recursos.
- "audience": Os dados na afirmação são uma propriedade no principal de serviço que é o público-alvo do token (o cliente ou o recurso principal de serviço).
- "empresa": Os dados na afirmação são uma propriedade no objeto do inquilino de recursos da empresa.
- "transformation": Os dados da declaração são de transformação de declarações (consulte a secção "Transformação de afirmações" mais adiante neste artigo).

Se a origem estiver a transformação, o **TransformationID** elemento têm de ser incluído nesta definição de afirmação também.

O elemento de ID identifica qual propriedade de origem fornece o valor para a afirmação. A tabela seguinte lista os valores de ID válido para cada valor de origem.

#### <a name="table-3-valid-id-values-per-source"></a>Tabela 3: Valores de ID válidos por origem

|Origem|ID|Descrição|
|-----|-----|-----|
|Utilizador|Apelido|Nome de família|
|Utilizador|givenName|Nome Próprio|
|Utilizador|DisplayName|Nome a Apresentar|
|Utilizador|ObjectId|ObjectID|
|Utilizador|mail|Endereço de E-mail|
|Utilizador|userprincipalname|Nome Principal de Utilizador|
|Utilizador|Departamento|Departamento|
|Utilizador|onpremisessamaccountname|No nome da conta Sam no local|
|Utilizador|netbiosname|Nome NetBios|
|Utilizador|dnsdomainname|Nome de domínio de DNS|
|Utilizador|onpremisesecurityidentifier|Identificador de segurança no local|
|Utilizador|companyname|Nome da Organização|
|Utilizador|streetaddress|Morada|
|Utilizador|código postal|Código Postal|
|Utilizador|preferredlanguange|Idioma Preferencial|
|Utilizador|onpremisesuserprincipalname|UPN no local|
|Utilizador|mailNickname|Alcunha de Correio|
|Utilizador|extensionattribute1|Atributo de extensão 1|
|Utilizador|extensionattribute2|Atributo de extensão 2|
|Utilizador|extensionattribute3|Atributo de extensão 3|
|Utilizador|extensionattribute4|Atributo de extensão 4|
|Utilizador|extensionattribute5|Atributo de extensão 5|
|Utilizador|extensionattribute6|Atributo de extensão 6|
|Utilizador|extensionattribute7|Atributo de extensão 7|
|Utilizador|extensionattribute8|Atributo de extensão 8|
|Utilizador|extensionattribute9|Atributo de extensão 9|
|Utilizador|extensionattribute10|Atributo de extensão 10|
|Utilizador|extensionattribute11|Atributo de extensão 11|
|Utilizador|extensionattribute12|Atributo de extensão 12|
|Utilizador|extensionattribute13|Atributo de extensão 13|
|Utilizador|extensionattribute14|Atributo de extensão 14|
|Utilizador|extensionattribute15|Atributo de extensão 15|
|Utilizador|othermail|Outro correio|
|Utilizador|País|País|
|Utilizador|city|Localidade|
|Utilizador|state|Estado|
|Utilizador|jobtitle|Cargo|
|Utilizador|employeeid|ID do Empregado|
|Utilizador|facsimiletelephonenumber|Número de telefone de fax|
|aplicativo, recurso, público-alvo|DisplayName|Nome a Apresentar|
|aplicativo, recurso, público-alvo|objeto|ObjectID|
|aplicativo, recurso, público-alvo|etiquetas|Etiqueta de Principal de serviço|
|Empresa|tenantcountry|País do inquilino|

**TransformationID:** Tem de fornecer o elemento de TransformationID apenas se o elemento de origem está definido como "transformação".

- Este elemento tem de corresponder ao elemento de ID da entrada de transformação no **ClaimsTransformation** propriedade que define a forma como os dados para esta afirmação são gerados.

**Tipo de afirmação:** O **JwtClaimType** e **SamlClaimType** elementos definem que afirmação esta entrada de esquema de afirmação refere-se a.

- O JwtClaimType tem de conter o nome da afirmação a ser emitida no JWTs.
- O SamlClaimType tem de conter o URI da afirmação a ser emitida em SAML tokens.

> [!NOTE]
> Os nomes e URIs de afirmações no conjunto de afirmação restrito não podem servir para os elementos de tipo de afirmação. Para obter mais informações, consulte a secção "Exceções e restrições" neste artigo.

### <a name="claims-transformation"></a>Transformação de afirmações

**Cadeia de caracteres:** ClaimsTransformation

**Tipo de dados:** BLOBs JSON, com uma ou mais entradas de transformação 

**Resumo:** Use essa propriedade para aplicar transformações comuns aos dados de origem, para gerar os dados de saída para afirmações especificados no esquema de afirmações.

**ID:** Utilize o elemento de ID para fazer referência a esta entrada de transformação na entrada TransformationID afirmações esquema. Este valor tem de ser exclusivo para cada entrada de transformação dentro desta política.

**TransformationMethod:** O elemento de TransformationMethod identifica qual operação é executada para gerar os dados para a afirmação.

Com base no método escolhido, é esperado um conjunto de entradas e saídas. Definir as entradas e saídas ao utilizar o **InputClaims**, **InputParameters** e **OutputClaims** elementos.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabela 4: Métodos de transformação e esperadas entradas e saídas

|TransformationMethod|Entrada esperada|Resultado esperado|Descrição|
|-----|-----|-----|-----|
|Associar|string1, string2, separator|outputClaim|Associações de cadeias de caracteres de entrada utilizando um separador entre. Por exemplo: string1: "foo@bar.com", string2: "sandbox", o separador: "." resulta na outputClaim: "foo@bar.com.sandbox"|
|ExtractMailPrefix|mail|outputClaim|Extrai a parte local de um endereço de e-mail. Por exemplo: correio: "foo@bar.com" resulta na outputClaim: "foo". Se não \@ início de sessão está presente, em seguida, a cadeia de entrada original é retornada como está.|

**InputClaims:** Utilize um elemento de InputClaims para passar os dados a partir de uma entrada de esquema de afirmação para uma transformação. Ele possui dois atributos: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** está associado com o elemento de ID da entrada de esquema de afirmação para encontrar a afirmação de entrada apropriada. 
- **TransformationClaimType** é usada para indicar um nome exclusivo para esta entrada. Este nome tem de corresponder a uma das entradas esperadas para o método de transformação.

**InputParameters:** Utilize um elemento de InputParameters para passar um valor constante para uma transformação. Ele possui dois atributos: **Valor** e **ID**.

- **Valor** é o valor da constante real a serem passados.
- **ID** usada para dar um nome exclusivo para a entrada. O nome tem de corresponder a uma das entradas esperadas para o método de transformação.

**OutputClaims:** Utilize um elemento de OutputClaims para manter os dados gerados por uma transformação e vinculá-lo a uma entrada de esquema de afirmação. Ele possui dois atributos: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** está associado com o ID da entrada de esquema de afirmação para encontrar a afirmação de saída adequados.
- **TransformationClaimType** usada para dar um nome exclusivo para a saída. O nome tem de corresponder a um das saídas esperadas para o método de transformação.

### <a name="exceptions-and-restrictions"></a>Exceções e restrições

**NameID SAML e UPN:** Os atributos a partir do qual origem os valores de NameID e de UPN e as transformações de afirmações que são permitidas, são limitados. Consulte a tabela 5 e a tabela 6 para ver os valores permitidos.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabela 5: Atributos permitidos como uma origem de dados para NameID de SAML

|Origem|ID|Descrição|
|-----|-----|-----|
|Utilizador|mail|Endereço de E-mail|
|Utilizador|userprincipalname|Nome Principal de Utilizador|
|Utilizador|onpremisessamaccountname|No nome da conta Sam no local|
|Utilizador|employeeid|ID do Empregado|
|Utilizador|extensionattribute1|Atributo de extensão 1|
|Utilizador|extensionattribute2|Atributo de extensão 2|
|Utilizador|extensionattribute3|Atributo de extensão 3|
|Utilizador|extensionattribute4|Atributo de extensão 4|
|Utilizador|extensionattribute5|Atributo de extensão 5|
|Utilizador|extensionattribute6|Atributo de extensão 6|
|Utilizador|extensionattribute7|Atributo de extensão 7|
|Utilizador|extensionattribute8|Atributo de extensão 8|
|Utilizador|extensionattribute9|Atributo de extensão 9|
|Utilizador|extensionattribute10|Atributo de extensão 10|
|Utilizador|extensionattribute11|Atributo de extensão 11|
|Utilizador|extensionattribute12|Atributo de extensão 12|
|Utilizador|extensionattribute13|Atributo de extensão 13|
|Utilizador|extensionattribute14|Atributo de extensão 14|
|Utilizador|extensionattribute15|Atributo de extensão 15|

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabela 6: Métodos de transformação permitidos para NameID de SAML

|TransformationMethod|Restrições|
| ----- | ----- |
|ExtractMailPrefix|Nenhuma|
|Associar|O sufixo estiver associado ao tem de ser um domínio verificado do inquilino de recursos.|

### <a name="custom-signing-key"></a>Chave de assinatura de personalizado

Uma chave de assinatura personalizada tem de ser atribuída ao objeto do principal de serviço para uma política de mapeamento de afirmações entrar em vigor. Todos os tokens emitidos e que tenham sido afetados pela política são assinados com a chave de assinatura personalizada e aplicações devem ser configuradas para aceitar tokens assinados com a chave de assinatura. Isto garante que os tokens foram modificadas pelo criador das afirmações de política de mapeamento e protege as aplicações de políticas criadas pelo atores maliciosos de mapeamento de afirmações de confirmação.

### <a name="cross-tenant-scenarios"></a>Cenários de entre inquilinos

Políticas de mapeamento das afirmações não se aplicam aos utilizadores convidados. Se tentar aceder a uma aplicação com uma política atribuída ao seu principal de serviço de mapeamento das afirmações de um utilizador convidado, o token padrão é emitido (a política não tem qualquer efeito).

## <a name="claims-mapping-policy-assignment"></a>Atribuição de política de mapeamento de afirmações

Políticas de mapeamento de afirmações só podem ser atribuídas para objetos de principal de serviço.

### <a name="example-claims-mapping-policies"></a>Políticas de mapeamento de afirmações de exemplo

No Azure AD, muitos cenários são possíveis quando pode personalizar afirmações emitidas nos tokens para principais de serviço específico. Nesta secção, vamos analisar alguns cenários comuns que podem ajudá-lo a compreender como utilizar as declarações de tipo de política de mapeamento.

#### <a name="prerequisites"></a>Pré-requisitos

Nos exemplos a seguir, criar, atualizar, ligar e eliminar políticas para principais de serviço. Se estiver familiarizado com o Azure AD, recomendamos que [Saiba mais sobre como obter um inquilino do Azure AD](quickstart-create-new-tenant.md) antes de continuar com estes exemplos.

Para começar, siga os passos abaixo:

1. Baixe a versão mais recente [versão de pré-visualização pública do módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Execute o comando Connect para iniciar sessão na sua conta de administrador do Azure AD. Execute este comando sempre que iniciar uma nova sessão.
    
     ``` powershell
    Connect-AzureAD -Confirm
    
    ```
1. Para ver todas as políticas que foram criadas na sua organização, execute o seguinte comando. Recomendamos que execute este comando após a maioria das operações nos seguintes cenários, para verificar que as suas políticas estão a ser criadas, conforme o esperado.
   
    ``` powershell
        Get-AzureADPolicy
    
    ```
   #### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma diretiva para omitir as declarações básicas de tokens emitidos para um principal de serviço.
   Neste exemplo, vai criar uma política que remove o conjunto de afirmações de básica de tokens emitidos para principais de serviço ligado.

1. Crie uma política de mapeamento das afirmações. Esta política, principais de serviço ligado para específico, remove a conjunto de tokens de afirmações básica.
   1. Para criar a política, execute este comando: 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Para ver a nova política e para obter a política ObjectId, execute o seguinte comando:
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribua a política para o seu principal de serviço. Também terá de obter o ObjectId do seu serviço principal. 
   1. Para ver os principais de serviço da sua organização, pode consultar o Microsoft Graph. Em alternativa, no Explorador do Azure AD Graph, iniciar sessão na sua conta do Azure AD.
   2. Quando tiver o ObjectId do seu principal de serviço, execute o seguinte comando:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política para incluir o EmployeeID e TenantCountry como afirmações nos tokens emitidos para um principal de serviço

Neste exemplo, vai criar uma política que adiciona os EmployeeID e TenantCountry a tokens emitidos para principais de serviço ligado. O campo IDdeEmpregado é emitido como o tipo de afirmação de nome em SAML tokens e JWTs. O TenantCountry é emitida como o tipo de afirmação de país em SAML tokens e JWTs. Neste exemplo, podemos continuar a incluir as declarações básicas definidas nos tokens.

1. Crie uma política de mapeamento das afirmações. Esta política, ligada para principais de serviço específico, adiciona as afirmações EmployeeID e TenantCountry para tokens.
   1. Para criar a política, execute este comando:  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Para ver a nova política e para obter a política ObjectId, execute o seguinte comando:
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. Atribua a política para o seu principal de serviço. Também terá de obter o ObjectId do seu serviço principal. 
   1. Para ver os principais de serviço da sua organização, pode consultar o Microsoft Graph. Em alternativa, no Explorador do Azure AD Graph, iniciar sessão na sua conta do Azure AD.
   2. Quando tiver o ObjectId do seu principal de serviço, execute o seguinte comando:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política que utiliza uma transformação de afirmações nos tokens emitidos para um principal de serviço

Neste exemplo, vai criar uma política que emite uma afirmação personalizada "JoinedData" ao JWTs emitido para principais de serviço ligado. Esta afirmação contém um valor que criou ao associar os dados armazenados no atributo extensionattribute1 no objeto do usuário com ".sandbox". Neste exemplo, excluímos as declarações básicas definidas nos tokens.

1. Crie uma política de mapeamento das afirmações. Esta política, ligada para principais de serviço específico, adiciona as afirmações EmployeeID e TenantCountry para tokens.
   1. Para criar a política, execute este comando: 
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy" 
      ```
    
   2. Para ver a nova política e para obter a política ObjectId, execute o seguinte comando: 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribua a política para o seu principal de serviço. Também terá de obter o ObjectId do seu serviço principal. 
   1. Para ver os principais de serviço da sua organização, pode consultar o Microsoft Graph. Em alternativa, no Explorador do Azure AD Graph, iniciar sessão na sua conta do Azure AD.
   2. Quando tiver o ObjectId do seu principal de serviço, execute o seguinte comando: 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```
