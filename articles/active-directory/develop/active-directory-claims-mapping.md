---
title: Personalizar declarações emitidas em tokens para um aplicativo específico em um locatário do Azure AD (visualização pública)
description: Esta página descreve o mapeamento de declarações Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e923cde3cfcffe594226f6b8b665053d1fc584f6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324995"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Como: Personalizar declarações emitidas em tokens para um aplicativo específico em um locatário (versão prévia)

> [!NOTE]
> Esse recurso substituirá e substituirá a [personalização de declarações](active-directory-saml-claims-customization.md) oferecida pelo portal hoje. No mesmo aplicativo, se você personalizar as declarações usando o portal além do método de grafo/PowerShell detalhado neste documento, os tokens emitidos para esse aplicativo ignorarão a configuração no Portal. As configurações feitas por meio dos métodos detalhados neste documento não serão refletidas no Portal.

Esse recurso é usado por administradores de locatários para personalizar as declarações emitidas em tokens para um aplicativo específico em seu locatário. Você pode usar políticas de mapeamento de declarações para:

- Selecione quais declarações são incluídas nos tokens.
- Criar tipos de declaração que ainda não existem.
- Escolha ou altere a origem dos dados emitidos em declarações específicas.

> [!NOTE]
> Esse recurso está atualmente em visualização pública. Esteja preparado para reverter ou remover todas as alterações. O recurso está disponível em qualquer assinatura do Azure Active Directory (AD do Azure) durante a visualização pública. No entanto, quando o recurso se torna disponível de forma geral, alguns aspectos do recurso podem exigir uma assinatura do Azure AD Premium. Esse recurso dá suporte à configuração de políticas de mapeamento de declaração para protocolos WS-enalimentado, SAML, OAuth e OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Tipo de política de mapeamento de declarações

No Azure AD, um objeto de **política** representa um conjunto de regras impostas em aplicativos individuais ou em todos os aplicativos em uma organização. Cada tipo de política tem uma estrutura exclusiva, com um conjunto de propriedades que são aplicadas a objetos aos quais elas são atribuídas.

Uma política de mapeamento de declarações é um tipo de objeto de **política** que modifica as declarações emitidas em tokens emitidos para aplicativos específicos.

## <a name="claim-sets"></a>Conjuntos de declarações

Há determinados conjuntos de declarações que definem como e quando são usados em tokens.

| Conjunto de declarações | Descrição |
|---|---|
| Conjunto de declarações de núcleo | Estão presentes em todos os tokens, independentemente da política. Essas declarações também são consideradas restritas e não podem ser modificadas. |
| Conjunto de declarações básico | Inclui as declarações emitidas por padrão para tokens (além do conjunto de declarações de núcleo). Você pode omitir ou modificar as declarações básicas usando as políticas de mapeamento de declarações. |
| Conjunto de declarações restrita | Não é possível modificar usando a política. A fonte de dados não pode ser alterada e nenhuma transformação é aplicada ao gerar essas declarações. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabela 1: Conjunto de declarações restritas de JWT (token Web JSON)

| Tipo de declaração (nome) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| actor |
| actortoken |
| AIO |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| appid |
| appidacr |
| assertion |
| at_hash |
| AUD |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| CC |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| CNF |
| code |
| controls |
| credential_keys |
| CSR |
| csr_type |
| deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| email |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| graph |
| group_sids |
| groups |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| IAT |
| identityprovider |
| IDP |
| in_corp |
| instance |
| ipaddr |
| isbrowserhostedapp |
| iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| NameID |
| NBF |
| netbios_name |
| nonce |
| oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| platf |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| role |
| roles |
| scope |
| scp |
| sid |
| signature |
| signin_state |
| src1 |
| src2 |
| projeto |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| sufixo |
| user_setting_sync_url |
| username |
| uti |
| versão |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabela 2: Conjunto de declarações restritas SAML

| Tipo de declaração (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
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

## <a name="claims-mapping-policy-properties"></a>Propriedades da política de mapeamento de declarações

Para controlar quais declarações são emitidas e de onde vêm os dados, use as propriedades de uma política de mapeamento de declarações. Se uma política não for definida, o sistema emitirá tokens que incluem o conjunto de declarações de núcleo, o conjunto de declarações básico e quaisquer [declarações opcionais](active-directory-optional-claims.md) que o aplicativo optou por receber.

### <a name="include-basic-claim-set"></a>Incluir conjunto de declarações básicas

**Strings** IncludeBasicClaimSet

**Tipo de dados:** Booliano (verdadeiro ou falso)

**Resumo** Esta propriedade determina se o conjunto de declarações básico está incluído nos tokens afetados por essa política.

- Se definido como true, todas as declarações no conjunto de declarações básico são emitidas em tokens afetados pela política. 
- Se definido como false, as declarações no conjunto de declarações básico não estão nos tokens, a menos que sejam adicionadas individualmente na propriedade de esquema de declarações da mesma política.

> [!NOTE] 
> As declarações no conjunto de declarações de núcleo estão presentes em todos os tokens, independentemente do que essa propriedade está definida como. 

### <a name="claims-schema"></a>Esquema de declarações

**Strings** ClaimsSchema

**Tipo de dados:** Blob JSON com uma ou mais entradas de esquema de declaração

**Resumo** Essa propriedade define quais declarações estão presentes nos tokens afetados pela política, além do conjunto de declarações básico e do conjunto de declarações de núcleo.
Para cada entrada de esquema de declaração definida nessa propriedade, determinadas informações são necessárias. Especifique de onde os dados serão provenientes (par**valor** ou **origem/ID**) e quais declarações os dados são emitidos como (**tipo de declaração**).

### <a name="claim-schema-entry-elements"></a>Elementos de entrada do esquema de declaração

**Valor** O elemento Value define um valor estático como os dados a serem emitidos na declaração.

**Par de origem/ID:** Os elementos Source e ID definem de onde os dados na declaração são originados. 

Defina o elemento de origem como um dos seguintes valores: 

- "usuário": Os dados na declaração são uma propriedade no objeto de usuário. 
- "aplicativo": Os dados na declaração são uma propriedade na entidade de serviço do aplicativo (cliente). 
- "recurso": Os dados na declaração são uma propriedade na entidade de serviço de recurso.
- "público": Os dados na declaração são uma propriedade na entidade de serviço que é o público do token (a entidade de serviço de cliente ou recurso).
- "empresa": Os dados na declaração são uma propriedade no objeto da empresa do locatário do recurso.
- "transformation": Os dados na declaração são de transformação de declarações (consulte a seção "transformação de declarações" mais adiante neste artigo).

Se a origem for transformação, o **elemento** Transformation também deverá ser incluído nessa definição de declaração.

O elemento ID identifica qual propriedade na fonte fornece o valor para a declaração. A tabela a seguir lista os valores de ID válido para cada valor de origem.

#### <a name="table-3-valid-id-values-per-source"></a>Tabela 3: Valores de ID válidos por origem

| Source | id | Descrição |
|-----|-----|-----|
| Utilizador | Apelido | Nome da família |
| Utilizador | givenName | Nome Próprio |
| Utilizador | displayName | Nome a Apresentar |
| Utilizador | ObjectID | ObjectID |
| Utilizador | mail | Endereço de E-mail |
| Utilizador | userprincipalname | Nome Principal de utilizador |
| Utilizador | Departamento|Departamento|
| Utilizador | onpremisessamaccountname | Nome da conta SAM local |
| Utilizador | NetBiosName| Nome NetBios |
| Utilizador | dnsdomainname | Nome de domínio DNS |
| Utilizador | onpremisesecurityidentifier | Identificador de segurança local |
| Utilizador | CompanyName| Nome da organização |
| Utilizador | StreetAddress | Morada |
| Utilizador | PostalCode | Código postal |
| Utilizador | preferredlanguange | Idioma preferencial |
| Utilizador | onpremisesuserprincipalname | UPN local |
| Utilizador | mailNickname | Apelido de email |
| Utilizador | extensionAttribute1 | Atributo de extensão 1 |
| Utilizador | extensionAttribute2 | Atributo de extensão 2 |
| Utilizador | extensionattribute3 | Atributo de extensão 3 |
| Utilizador | extensionattribute4 | Atributo de extensão 4 |
| Utilizador | extensionattribute5 | Atributo de extensão 5 |
| Utilizador | extensionattribute6 | Atributo de extensão 6 |
| Utilizador | extensionattribute7 | Atributo de extensão 7 |
| Utilizador | extensionattribute8 | Atributo de extensão 8 |
| Utilizador | extensionattribute9 | Atributo de extensão 9 |
| Utilizador | extensionattribute10 | Atributo de extensão 10 |
| Utilizador | extensionattribute11 | Atributo de extensão 11 |
| Utilizador | extensionattribute12 | Atributo de extensão 12 |
| Utilizador | extensionattribute13 | Atributo de extensão 13 |
| Utilizador | extensionAttribute14 | Atributo de extensão 14 |
| Utilizador | extensionAttribute15 | Atributo de extensão 15 |
| Utilizador | othermail | Outros emails |
| Utilizador | país | Country |
| Utilizador | city | City |
| Utilizador | state | Estado |
| Utilizador | JobTitle | Cargo |
| Utilizador | employeeid | ID do funcionário |
| Utilizador | facsimileTelephoneNumber | Número de telefone do fax |
| aplicativo, recurso, público-alvo | displayName | Nome a Apresentar |
| aplicativo, recurso, público-alvo | com objeto | ObjectID |
| aplicativo, recurso, público-alvo | etiquetas | Marca da entidade de serviço |
| Empresa | tenantcountry | País do locatário |

**TransformationID** O elemento Transformation deve ser fornecido somente se o elemento Source estiver definido como "Transformation".

- Esse elemento deve corresponder ao elemento ID da entrada de transformação na propriedade **ClaimsTransformation** que define como os dados para essa declaração são gerados.

**Tipo de declaração:** Os elementos **JwtClaimType** e **SamlClaimType** definem a qual declaração essa entrada de esquema de declaração se refere.

- O JwtClaimType deve conter o nome da declaração a ser emitida em JWTs.
- O SamlClaimType deve conter o URI da declaração a ser emitida em tokens SAML.

> [!NOTE]
> Os nomes e URIs de declarações no conjunto de declarações restritos não podem ser usados para os elementos de tipo de declaração. Para obter mais informações, consulte a seção "exceções e restrições" mais adiante neste artigo.

### <a name="claims-transformation"></a>Transformação de afirmações

**Strings** ClaimsTransformation

**Tipo de dados:** Blob JSON, com uma ou mais entradas de transformação 

**Resumo** Use essa propriedade para aplicar transformações comuns aos dados de origem, para gerar os dados de saída para declarações especificadas no esquema de declarações.

**ID:** Use o elemento ID para fazer referência a essa entrada de transformação na entrada de esquema de declarações Transformation. Esse valor deve ser exclusivo para cada entrada de transformação nessa política.

**TransformationMethod** O elemento TransformationMethod identifica qual operação é executada para gerar os dados para a declaração.

Com base no método escolhido, um conjunto de entradas e saídas é esperado. Defina as entradas e saídas usando os elementos **InputClaims**, **InputParameters** e **OutputClaims** .

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabela 4: Métodos de transformação e entradas e saídas esperadas

|TransformationMethod|Entrada esperada|Saída esperada|Descrição|
|-----|-----|-----|-----|
|Associar|Seqüência1, seqüência2, separador|outputClaim|Une cadeias de caracteres de entrada usando um separador entre. Por exemplo: seqüência1: "foo@bar.com", seqüência2: "sandbox", separador: "." resulta em outputClaimfoo@bar.com.sandbox: ""|
|ExtractMailPrefix|mail|outputClaim|Extrai a parte local de um endereço de email. Por exemplo: mail: "foo@bar.com" resulta em outputClaim: "foo". Se nenhum \@ sinal estiver presente, a cadeia de caracteres de entrada original será retornada como está.|

**InputClaims** Use um elemento InputClaims para passar os dados de uma entrada de esquema de declaração para uma transformação. Ele tem dois atributos: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** é unida ao elemento ID da entrada de esquema de declaração para localizar a declaração de entrada apropriada. 
- **TransformationClaimType** é usado para atribuir um nome exclusivo a essa entrada. Esse nome deve corresponder a uma das entradas esperadas para o método de transformação.

**InputParameters** Use um elemento InputParameters para passar um valor constante para uma transformação. Ele tem dois atributos: **Valor** e **ID**.

- **Valor** é o valor constante real a ser passado.
- A **ID** é usada para atribuir um nome exclusivo à entrada. O nome deve corresponder a uma das entradas esperadas para o método de transformação.

**OutputClaims:** Use um elemento OutputClaims para armazenar os dados gerados por uma transformação e vinculá-los a uma entrada de esquema de declaração. Ele tem dois atributos: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** é unida à ID da entrada de esquema de declaração para localizar a declaração de saída apropriada.
- **TransformationClaimType** é usado para fornecer um nome exclusivo para a saída. O nome deve corresponder a uma das saídas esperadas para o método de transformação.

### <a name="exceptions-and-restrictions"></a>Exceções e restrições

**NameID e UPN SAML:** Os atributos dos quais você origina os valores NameID e UPN e as transformações de declarações permitidas são limitados. Consulte a tabela 5 e a tabela 6 para ver os valores permitidos.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabela 5: Atributos permitidos como uma fonte de dados para NameID SAML

|Source|id|Descrição|
|-----|-----|-----|
| Utilizador | mail|Endereço de E-mail|
| Utilizador | userprincipalname|Nome Principal de utilizador|
| Utilizador | onpremisessamaccountname|Nome da conta Sam local|
| Utilizador | employeeid|ID do funcionário|
| Utilizador | extensionAttribute1 | Atributo de extensão 1 |
| Utilizador | extensionAttribute2 | Atributo de extensão 2 |
| Utilizador | extensionattribute3 | Atributo de extensão 3 |
| Utilizador | extensionattribute4 | Atributo de extensão 4 |
| Utilizador | extensionattribute5 | Atributo de extensão 5 |
| Utilizador | extensionattribute6 | Atributo de extensão 6 |
| Utilizador | extensionattribute7 | Atributo de extensão 7 |
| Utilizador | extensionattribute8 | Atributo de extensão 8 |
| Utilizador | extensionattribute9 | Atributo de extensão 9 |
| Utilizador | extensionattribute10 | Atributo de extensão 10 |
| Utilizador | extensionattribute11 | Atributo de extensão 11 |
| Utilizador | extensionattribute12 | Atributo de extensão 12 |
| Utilizador | extensionattribute13 | Atributo de extensão 13 |
| Utilizador | extensionAttribute14 | Atributo de extensão 14 |
| Utilizador | extensionAttribute15 | Atributo de extensão 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabela 6: Métodos de transformação permitidos para NameID SAML

| TransformationMethod | Restrições |
| ----- | ----- |
| ExtractMailPrefix | Nenhuma |
| Associar | O sufixo que está sendo Unido deve ser um domínio verificado do locatário do recurso. |

### <a name="custom-signing-key"></a>Chave de assinatura personalizada

Uma chave de assinatura personalizada deve ser atribuída ao objeto de entidade de serviço para que uma política de mapeamento de declarações entre em vigor. Isso garante a confirmação de que os tokens foram modificados pelo criador da política de mapeamento de declarações e protege aplicativos de políticas de mapeamento de declarações criadas por atores mal-intencionados.  Os aplicativos que têm o mapeamento de declarações habilitado devem verificar um URI especial para suas chaves de assinatura `appid={client_id}` de token anexando a suas [solicitações de metadados do OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document).  

### <a name="cross-tenant-scenarios"></a>Cenários entre locatários

As políticas de mapeamento de declarações não se aplicam a usuários convidados. Se um usuário convidado tentar acessar um aplicativo com uma política de mapeamento de declarações atribuída à sua entidade de serviço, o token padrão será emitido (a política não terá efeito).

## <a name="claims-mapping-policy-assignment"></a>Atribuição de política de mapeamento de declarações

As políticas de mapeamento de declarações só podem ser atribuídas a objetos de entidade de serviço.

### <a name="example-claims-mapping-policies"></a>Exemplo de políticas de mapeamento de declarações

No Azure AD, muitos cenários são possíveis quando você pode personalizar declarações emitidas em tokens para entidades de serviço específicas. Nesta seção, percorremos alguns cenários comuns que podem ajudá-lo a entender como usar o tipo de política de mapeamento de declarações.

#### <a name="prerequisites"></a>Pré-requisitos

Nos exemplos a seguir, você cria, atualiza, vincula e exclui políticas para entidades de serviço. Se você for novo no Azure AD, recomendamos que [saiba como obter um locatário do Azure ad](quickstart-create-new-tenant.md) antes de prosseguir com esses exemplos.

Para começar, execute as seguintes etapas:

1. Baixe a versão mais recente da [Visualização pública do módulo do Azure ad PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Execute o comando Connect para entrar em sua conta de administrador do Azure AD. Execute esse comando toda vez que iniciar uma nova sessão.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Para ver todas as políticas que foram criadas em sua organização, execute o comando a seguir. Recomendamos que você execute esse comando após a maioria das operações nos cenários a seguir, para verificar se as políticas estão sendo criadas conforme o esperado.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política para omitir as declarações básicas de tokens emitidos para uma entidade de serviço

Neste exemplo, você cria uma política que remove o conjunto de declarações básicas de tokens emitidos para entidades de serviço vinculadas.

1. Crie uma política de mapeamento de declarações. Essa política, vinculada a entidades de serviço específicas, remove o conjunto de declarações básicas de tokens.
   1. Para criar a política, execute este comando: 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Para ver sua nova política e obter o ObjectId da política, execute o seguinte comando:
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribua a política à entidade de serviço. Você também precisa obter o ObjectId da entidade de serviço.
   1. Para ver todas as entidades de serviço de sua organização, você pode consultar Microsoft Graph. Ou, no explorador do Graph do Azure AD, entre na sua conta do Azure AD.
   2. Quando você tiver o ObjectId da entidade de serviço, execute o seguinte comando:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política para incluir EmployeeID e TenantCountry como declarações em tokens emitidos para uma entidade de serviço

Neste exemplo, você cria uma política que adiciona o EmployeeID e o TenantCountry a tokens emitidos para entidades de serviço vinculadas. O EmployeeID é emitido como o tipo de declaração de nome em tokens SAML e JWTs. O TenantCountry é emitido como o tipo de declaração country nos tokens SAML e JWTs. Neste exemplo, continuamos a incluir o conjunto de declarações básicas nos tokens.

1. Crie uma política de mapeamento de declarações. Essa política, vinculada a entidades de serviço específicas, adiciona as declarações EmployeeID e TenantCountry a tokens.
   1. Para criar a política, execute o seguinte comando:  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Para ver sua nova política e obter o ObjectId da política, execute o seguinte comando:
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. Atribua a política à entidade de serviço. Você também precisa obter o ObjectId da entidade de serviço. 
   1. Para ver todas as entidades de serviço de sua organização, você pode consultar Microsoft Graph. Ou, no explorador do Graph do Azure AD, entre na sua conta do Azure AD.
   2. Quando você tiver o ObjectId da entidade de serviço, execute o seguinte comando:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política que usa uma transformação de declarações em tokens emitidos para uma entidade de serviço

Neste exemplo, você cria uma política que emite uma declaração personalizada "JoinedData" para JWTs emitida para entidades de serviço vinculadas. Essa declaração contém um valor criado unindo os dados armazenados no atributo extensionAttribute1 no objeto de usuário com ". sandbox". Neste exemplo, excluimos o conjunto de declarações básicas nos tokens.

1. Crie uma política de mapeamento de declarações. Essa política, vinculada a entidades de serviço específicas, adiciona as declarações EmployeeID e TenantCountry a tokens.
   1. Para criar a política, execute o seguinte comando:
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Para ver sua nova política e obter o ObjectId da política, execute o seguinte comando: 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribua a política à entidade de serviço. Você também precisa obter o ObjectId da entidade de serviço. 
   1. Para ver todas as entidades de serviço de sua organização, você pode consultar Microsoft Graph. Ou, no explorador do Graph do Azure AD, entre na sua conta do Azure AD.
   2. Quando você tiver o ObjectId da entidade de serviço, execute o seguinte comando: 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Consulte também

Para saber como personalizar as declarações emitidas no token SAML por meio do portal do Azure, [consulte Como: Personalizar declarações emitidas no token SAML para aplicativos empresariais](active-directory-saml-claims-customization.md)