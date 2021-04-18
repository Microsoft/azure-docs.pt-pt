---
title: Política de mapeamento de reclamações
titleSuffix: Microsoft identity platform
description: Conheça o tipo de política de mapeamento de reclamações, que é usado para modificar as reclamações emitidas em fichas emitidas para aplicações específicas.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: reference
ms.date: 04/16/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 56e855bafa70360711f3e30a7c4527091af7b34c
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601243"
---
# <a name="claims-mapping-policy-type"></a>Tipo de política de mapeamento de reclamações

Em Azure AD, um objeto **de política** representa um conjunto de regras aplicadas em aplicações individuais ou em todas as aplicações de uma organização. Cada tipo de política tem uma estrutura única, com um conjunto de propriedades que são então aplicadas a objetos aos quais são atribuídos.

Uma política de mapeamento de reclamações é um tipo de objeto **de política** que modifica [as alegações emitidas em fichas emitidas](active-directory-claims-mapping.md) para aplicações específicas.

## <a name="claim-sets"></a>Conjuntos de afirmações

Há certos conjuntos de afirmações que definem como e quando são usadas em fichas.

| Conjunto de reclamações | Description |
|---|---|
| Conjunto de reclamação do núcleo | Estão presentes em todos os símbolos, independentemente da política. Estas alegações também são consideradas restritas, e não podem ser modificadas. |
| Conjunto básico de reclamação | Inclui as reclamações que são emitidas por padrão para tokens (além do conjunto de reclamações principais). Pode omitir ou modificar reclamações básicas utilizando as políticas de mapeamento de reclamações. |
| Conjunto de reclamações restrito | Não pode ser modificado usando a política. A fonte de dados não pode ser alterada e nenhuma transformação é aplicada ao gerar estas alegações. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Quadro 1: Conjunto de reclamações restrita json Web Token (JWT)

| Tipo de reclamação (nome) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| ator |
| actortoken |
| aio |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| appid |
| appidacr |
| afirmação |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| cnf |
| code |
| controlos |
| credential_keys |
| csr |
| csr_type |
| deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-mail |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| gráfico |
| group_sids |
| grupos |
| temgrupos |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| identitário |
| idp |
| in_corp |
| exemplo |
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
| nameid |
| nbf |
| netbios_name |
| nonce |
| oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| palavra-passe |
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
| recurso |
| papel |
| funções |
| scope |
| scp |
| sid |
| assinatura |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| nome de utilizador |
| uti |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Quadro 2: Conjunto de reclamações restritas DA SAML

| Tipo de reclamação (URI) |
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

## <a name="claims-mapping-policy-properties"></a>Propriedades de política de mapeamento de reclamações

Para controlar as reclamações emitidas e de onde os dados provêm, utilize as propriedades de uma política de mapeamento de reclamações. Se uma política não for definida, o sistema emite fichas que incluem o conjunto de reclamações fundamentais, o conjunto de alegações básicas, e quaisquer [alegações opcionais](active-directory-optional-claims.md) que a aplicação tenha optado por receber.

> [!NOTE]
> As reclamações no conjunto de reclamações fundamentais estão presentes em cada token, independentemente do que esta propriedade está definida.

### <a name="include-basic-claim-set"></a>Incluir conjunto de reclamações básicas

**Corda:** IncluirBasicClaimSet

**Tipo de dados:** Boolean (Verdadeiro ou Falso)

**Resumo:** Esta propriedade determina se o conjunto de reclamações básicas está incluído em fichas afetadas por esta política.

- Se for definido para True, todas as reclamações no conjunto de alegações básicas são emitidas em fichas afetadas pela apólice.
- Se definidos como Falsos, as reclamações no conjunto de reclamações básicas não estão nos tokens, a menos que sejam adicionadas individualmente na propriedade do esquema de reclamações da mesma apólice.



### <a name="claims-schema"></a>Esquema de reclamações

**Corda:** Sinistrosschema

**Tipo de dados:** Bolha JSON com uma ou mais entradas de esquema de reclamação

**Resumo:** Este imóvel define quais as reclamações que estão presentes nos tokens afetados pela apólice, além do conjunto de reclamações básicas e do conjunto de reclamações fundamentais.
Para cada entrada de esquema de reclamação definida neste imóvel, é necessária determinadas informações. Especifique de onde os dados provêm **(Valor**, **par de fonte/ID**, ou **par Source/ExtensionID**), e que alegam que os dados são emitidos como **(Tipo de Reclamação).**

### <a name="claim-schema-entry-elements"></a>Elementos de entrada de esquema de reclamação

**Valor:** O elemento Valor define um valor estático como os dados a emitir na reclamação.

**Par de origem/ID:** Os elementos De Origem e ID definem de onde provêm os dados da reclamação.

**Par Source/ExtensionID:** Os elementos Source e ExtensionID definem o atributo de extensão do esquema de diretório de onde os dados da reclamação são provenientes. Para obter mais informações, consulte [utilizando atributos de extensão de esquema de diretório em sinistros](active-directory-schema-extensions.md).

Desaguise o elemento Fonte a um dos seguintes valores:

- "Utilizador": Os dados da reclamação são uma propriedade no objeto do Utilizador.
- "aplicação": Os dados da reclamação são um imóvel no principal do serviço de aplicação (cliente).
- "recurso": Os dados da reclamação são um imóvel no principal do serviço de recursos.
- "público": Os dados da reclamação são um imóvel no diretor de serviço que é o público do token (quer o cliente quer o principal do serviço de recursos).
- "Empresa": Os dados da reclamação são um imóvel sobre o objeto da Empresa do arrendatário de recursos.
- "transformação": Os dados da reclamação são provenientes da transformação de sinistros (ver secção "Transformação de Reclamações" mais tarde neste artigo).

Se a fonte for transformação, o elemento **TransformationID** também deve ser incluído nesta definição de reclamação.

O elemento ID identifica qual o imóvel na fonte que fornece o valor para a reclamação. A tabela que se segue lista os valores de ID válidos para cada valor de Fonte.

#### <a name="table-3-valid-id-values-per-source"></a>Quadro 3: Valores de identificação válidos por fonte

| Origem | ID | Description |
|-----|-----|-----|
| Utilizador | surname | Nome da família |
| Utilizador | givenname | Nome Próprio |
| Utilizador | nome de exibição | Nome a Apresentar |
| Utilizador | objectid | ObjectID |
| Utilizador | correio | Endereço de E-mail |
| Utilizador | nome do utilizadorprincipal | Nome Principal de Utilizador |
| Utilizador | departamento|Departamento|
| Utilizador | nome onpremisessamaccountname | Nome da conta SAM no local |
| Utilizador | nome netbiosname| Nome NetBios |
| Utilizador | dnsdomainme | Nome de Domínio DNS |
| Utilizador | onpremisesecurityidentifier | Identificador de Segurança no local |
| Utilizador | nome de empresa| Nome da Organização |
| Utilizador | streetaddress | Rua |
| Utilizador | código postal | Código Postal |
| Utilizador | preferencialmente língua | Língua Preferida |
| Utilizador | onpremisesuserprincipalname | UPN no local |
| Utilizador | nome de mailnickname | Apelido de correio |
| Utilizador | extensãotribuição1 | Atributo de extensão 1 |
| Utilizador | extensãotribuiu2 | Atributo de extensão 2 |
| Utilizador | extensãotribuité3 | Atributo de extensão 3 |
| Utilizador | extensãotribuição4 | Atributo de extensão 4 |
| Utilizador | extensãotribuição5 | Atributo de extensão 5 |
| Utilizador | extensãotribuiu6 | Atributo de extensão 6 |
| Utilizador | extensãotribuição7 | Atributo de extensão 7 |
| Utilizador | extensãotribute8 | Atributo de extensão 8 |
| Utilizador | extensãotribute9 | Atributo de extensão 9 |
| Utilizador | extensãotribuição10 | Atributo de extensão 10 |
| Utilizador | extensãotribuição11 | Atributo de extensão 11 |
| Utilizador | extensãotribuição12 | Atributo de extensão 12 |
| Utilizador | extensãotribuição13 | Atributo de extensão 13 |
| Utilizador | extensãotribuição14 | Atributo de extensão 14 |
| Utilizador | extensãotribuição15 | Atributo de extensão 15 |
| Utilizador | outromail | Outros Correios |
| Utilizador | país | País/Região |
| Utilizador | city | City |
| Utilizador | state | Estado |
| Utilizador | cargo | Cargo |
| Utilizador | empregado | ID de colaborador |
| Utilizador | facsimiletelephonenumber | Número de telefone facsímia |
| Utilizador | entidades designadas | lista de funções da App atribuídas ao utilizador|
| aplicação, recurso, audiência | nome de exibição | Nome a Apresentar |
| aplicação, recurso, audiência | objectid | ObjectID |
| aplicação, recurso, audiência | etiquetas | Etiqueta principal de serviço |
| Empresa | país inquilino | País/região do arrendatário |

**TransformationID:** O elemento TransformationID só deve ser fornecido se o elemento Fonte estiver definido para "transformação".

- Este elemento deve corresponder ao elemento de ID da entrada de transformação na propriedade **ClaimsTransformation** que define como os dados para esta alegação são gerados.

**Tipo de reclamação:** Os elementos **JwtClaimType** e **SamlClaimType** definem a que alegação esta entrada de esquema de alegação se refere.

- O JwtClaimType deve conter o nome da reclamação a emitir em JWTs.
- O SamlClaimType deve conter o URI da alegação a emitir em fichas SAML.

* **no atributo PremisesUserPrincipalName:** Ao utilizar um ID Alternativo, o utilizador no local atribui o NomePrincipal é sincronizado com o atributo AZure AD no NomePrincipal DoMises. Este atributo só está disponível quando o ID alternativo está configurado, mas também está disponível através da MS Graph Beta: https://graph.microsoft.com/beta/me/ .

> [!NOTE]
> Os nomes e URIs de reclamações no conjunto de reclamações restrito não podem ser utilizados para os elementos do tipo de reclamação. Para mais informações, consulte a secção "Exceções e restrições" mais tarde neste artigo.

### <a name="claims-transformation"></a>Transformação de afirmações

**Corda:** Transferência de Reclamações

**Tipo de dados:** Bolha JSON, com uma ou mais entradas de transformação

**Resumo:** Utilize esta propriedade para aplicar transformações comuns aos dados de origem, para gerar os dados de saída para sinistros especificados no Esquema de Reclamações.

**ID:** Utilize o elemento ID para fazer referência a esta entrada de transformação na entrada do Esquema de Reclamações TransformationID. Este valor deve ser único para cada entrada de transformação dentro desta política.

**TransformationMethod:** O elemento TransformationMethod identifica qual a operação realizada para gerar os dados para a reclamação.

Com base no método escolhido, espera-se um conjunto de entradas e saídas. Defina as entradas e saídas utilizando os **elementos InputClaims,** **InputParameters** e **OutputClaims.**

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Quadro 4: Métodos de transformação e entradas e saídas esperadas

|TransformaçãoMethod|Entrada esperada|Resultado esperado|Description|
|-----|-----|-----|-----|
|Participar|string1, string2, separador|outputClaim|Junta cordas de entrada utilizando um separador no meio. Por exemplo: string1:" foo@bar.com " " string2:"sandbox", separador:"." resulta em outputClaim:" foo@bar.com.sandbox|
|ExtratoMailPrefixo|E-mail ou UPN|corda extraída|ExtensãoTribu 1-15 ou quaisquer outras extensões de Esquema que estejam a armazenar um valor de endereço DE UPN ou e-mail para o utilizador, por johndoe@contoso.com exemplo. Extrai a parte local de um endereço de e-mail. Por exemplo: mail:" foo@bar.com " resulta em saídaClaim:"foo". Se não \@ houver sinal, a cadeia de entrada original é devolvida como está.|

**InputClaims:** Utilize um elemento InputClaims para passar os dados de uma entrada de esquema de reclamação para uma transformação. Tem dois atributos: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** é acompanhado com elemento de ID da entrada de esquema de reclamação para encontrar a alegação de entrada apropriada.
- **TransformationClaimType** é usado para dar um nome único a esta entrada. Este nome deve corresponder a uma das entradas esperadas para o método de transformação.

**InputParameters:** Utilize um elemento InputParameters para passar um valor constante a uma transformação. Tem dois atributos: **Valor** e **ID.**

- **Valor** é o valor constante real a ser passado.
- **ID** é usado para dar um nome único à entrada. O nome deve corresponder a uma das entradas esperadas para o método de transformação.

**ResultadosClaims:** Utilize um elemento OutputClaims para reter os dados gerados por uma transformação e amarrá-lo a uma entrada de esquema de reclamação. Tem dois atributos: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** é acompanhado com o ID da entrada do esquema de reclamação para encontrar a reclamação de saída apropriada.
- **TransformationClaimType** é usado para dar um nome único à saída. O nome deve corresponder a uma das saídas esperadas para o método de transformação.

### <a name="exceptions-and-restrictions"></a>Exceções e restrições

**SAML NameID e UPN:** Os atributos a partir dos quais você fornece os valores NameID e UPN, e as transformações de sinistros que são permitidos, são limitados. Consulte a tabela 5 e a tabela 6 para ver os valores permitidos.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Quadro 5: Atributos permitidos como fonte de dados para SAML NameID

|Origem|ID|Description|
|-----|-----|-----|
| Utilizador | correio|Endereço de E-mail|
| Utilizador | nome do utilizadorprincipal|Nome Principal de Utilizador|
| Utilizador | nome onpremisessamaccountname|No Nome da Conta Sam das Instalações|
| Utilizador | empregado|ID de colaborador|
| Utilizador | extensãotribuição1 | Atributo de extensão 1 |
| Utilizador | extensãotribuiu2 | Atributo de extensão 2 |
| Utilizador | extensãotribuité3 | Atributo de extensão 3 |
| Utilizador | extensãotribuição4 | Atributo de extensão 4 |
| Utilizador | extensãotribuição5 | Atributo de extensão 5 |
| Utilizador | extensãotribuiu6 | Atributo de extensão 6 |
| Utilizador | extensãotribuição7 | Atributo de extensão 7 |
| Utilizador | extensãotribute8 | Atributo de extensão 8 |
| Utilizador | extensãotribute9 | Atributo de extensão 9 |
| Utilizador | extensãotribuição10 | Atributo de extensão 10 |
| Utilizador | extensãotribuição11 | Atributo de extensão 11 |
| Utilizador | extensãotribuição12 | Atributo de extensão 12 |
| Utilizador | extensãotribuição13 | Atributo de extensão 13 |
| Utilizador | extensãotribuição14 | Atributo de extensão 14 |
| Utilizador | extensãotribuição15 | Atributo de extensão 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Quadro 6: Métodos de transformação permitidos para o NOME SAMID

| TransformaçãoMethod | Restrições |
| ----- | ----- |
| ExtratoMailPrefixo | Nenhum |
| Participar | O sufixo que está a ser associado deve ser um domínio verificado do inquilino de recursos. |

### <a name="cross-tenant-scenarios"></a>Cenários de inquilinos cruzados

As políticas de mapeamento de sinistros não se aplicam aos utilizadores convidados. Se um utilizador convidado tentar aceder a uma aplicação com uma política de mapeamento de reclamações atribuída ao seu principal de serviço, o token padrão é emitido (a apólice não tem efeito).


## <a name="next-steps"></a>Passos seguintes

- Para saber como personalizar as reclamações emitidas em fichas para uma aplicação específica no seu inquilino usando o PowerShell, consulte [Como: Personalizar reclamações emitidas em fichas para uma aplicação específica num inquilino (Preview)](active-directory-claims-mapping.md)
- Para saber como personalizar as reclamações emitidas no token SAML através do portal Azure, consulte [Como: Personalizar reclamações emitidas no token SAML para aplicações empresariais](active-directory-saml-claims-customization.md)
- Para saber mais sobre os atributos de extensão, consulte [utilizando atributos de extensão de esquema de diretório em sinistros](active-directory-schema-extensions.md).
