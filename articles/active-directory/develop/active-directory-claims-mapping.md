---
title: Personalize as reclamações de aplicativos de inquilino Azure AD (PowerShell)
titleSuffix: Microsoft identity platform
description: Esta página descreve o Azure Ative Directory alega mapeamento.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 4c7474b001284286ed589f6b7995db6bc7fd50af
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075071"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Como: Personalizar reclamações emitidas em fichas para uma aplicação específica num inquilino (Preview)

> [!NOTE]
> Esta funcionalidade substitui e substitui a personalização de [reclamações](active-directory-saml-claims-customization.md) oferecida através do portal de hoje. Na mesma aplicação, se personalizar as reclamações utilizando o portal para além do método Gráfico/PowerShell detalhado neste documento, os tokens emitidos para essa aplicação ignorarão a configuração no portal. As configurações es feitas através dos métodos detalhados neste documento não serão refletidas no portal.

Esta funcionalidade é utilizada pelos administradores inquilinos para personalizar as reclamações emitidas em fichas para uma aplicação específica no seu inquilino. Pode utilizar políticas de mapeamento de reclamações para:

- Selecione quais as reclamações incluídas em fichas.
- Crie tipos de reclamação que já não existem.
- Escolha ou altere a fonte de dados emitidos em reclamações específicas.

> [!NOTE]
> Esta capacidade encontra-se atualmente em visualização pública. Esteja preparado para reverter ou remover todas as alterações. A funcionalidade está disponível em qualquer subscrição do Azure Ative Directory (Azure AD) durante a pré-visualização pública. No entanto, quando a funcionalidade se torna geralmente disponível, alguns aspetos da funcionalidade podem necessitar de uma subscrição premium AZURE. Esta funcionalidade suporta a configuração de políticas de mapeamento de reclamações para os protocolos WS-Fed, SAML, OAuth e OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Tipo de política de mapeamento de reclamações

Em Azure AD, um objeto **de política** representa um conjunto de regras aplicadas em aplicações individuais ou em todas as aplicações de uma organização. Cada tipo de política tem uma estrutura única, com um conjunto de propriedades que são então aplicadas a objetos aos quais são atribuídos.

Uma política de mapeamento de reclamações é um tipo de objeto **de política** que modifica as alegações emitidas em fichas emitidas para aplicações específicas.

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
| User | surname | Nome da família |
| User | givenname | Nome Próprio |
| User | nome de exibição | Nome a Apresentar |
| User | objectid | ObjectID |
| User | correio | Endereço de E-mail |
| User | nome do utilizadorprincipal | Nome Principal de Utilizador |
| User | departamento|Departamento|
| User | nome onpremisessamaccountname | Nome da conta SAM no local |
| User | nome netbiosname| Nome NetBios |
| User | dnsdomainme | Nome de Domínio DNS |
| User | onpremisesecurityidentifier | Identificador de Segurança no local |
| User | nome de empresa| Nome da Organização |
| User | streetaddress | Rua |
| User | código postal | Código Postal |
| User | preferencialmente língua | Língua Preferida |
| User | onpremisesuserprincipalname | UPN no local |
| User | nome de mailnickname | Apelido de correio |
| User | extensãotribuição1 | Atributo de extensão 1 |
| User | extensãotribuiu2 | Atributo de extensão 2 |
| User | extensãotribuité3 | Atributo de extensão 3 |
| User | extensãotribuição4 | Atributo de extensão 4 |
| User | extensãotribuição5 | Atributo de extensão 5 |
| User | extensãotribuiu6 | Atributo de extensão 6 |
| User | extensãotribuição7 | Atributo de extensão 7 |
| User | extensãotribute8 | Atributo de extensão 8 |
| User | extensãotribute9 | Atributo de extensão 9 |
| User | extensãotribuição10 | Atributo de extensão 10 |
| User | extensãotribuição11 | Atributo de extensão 11 |
| User | extensãotribuição12 | Atributo de extensão 12 |
| User | extensãotribuição13 | Atributo de extensão 13 |
| User | extensãotribuição14 | Atributo de extensão 14 |
| User | extensãotribuição15 | Atributo de extensão 15 |
| User | outromail | Outros Correios |
| User | país | País/Região |
| User | city | City |
| User | state | Estado |
| User | cargo | Cargo |
| User | empregado | ID de colaborador |
| User | facsimiletelephonenumber | Número de telefone facsímia |
| User | entidades designadas | lista de funções da App atribuídas ao utilizador|
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
| User | correio|Endereço de E-mail|
| User | nome do utilizadorprincipal|Nome Principal de Utilizador|
| User | nome onpremisessamaccountname|No Nome da Conta Sam das Instalações|
| User | empregado|ID de colaborador|
| User | extensãotribuição1 | Atributo de extensão 1 |
| User | extensãotribuiu2 | Atributo de extensão 2 |
| User | extensãotribuité3 | Atributo de extensão 3 |
| User | extensãotribuição4 | Atributo de extensão 4 |
| User | extensãotribuição5 | Atributo de extensão 5 |
| User | extensãotribuiu6 | Atributo de extensão 6 |
| User | extensãotribuição7 | Atributo de extensão 7 |
| User | extensãotribute8 | Atributo de extensão 8 |
| User | extensãotribute9 | Atributo de extensão 9 |
| User | extensãotribuição10 | Atributo de extensão 10 |
| User | extensãotribuição11 | Atributo de extensão 11 |
| User | extensãotribuição12 | Atributo de extensão 12 |
| User | extensãotribuição13 | Atributo de extensão 13 |
| User | extensãotribuição14 | Atributo de extensão 14 |
| User | extensãotribuição15 | Atributo de extensão 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Quadro 6: Métodos de transformação permitidos para o NOME SAMID

| TransformaçãoMethod | Restrições |
| ----- | ----- |
| ExtratoMailPrefixo | Nenhum |
| Participar | O sufixo que está a ser associado deve ser um domínio verificado do inquilino de recursos. |

### <a name="cross-tenant-scenarios"></a>Cenários de inquilinos cruzados

As políticas de mapeamento de sinistros não se aplicam aos utilizadores convidados. Se um utilizador convidado tentar aceder a uma aplicação com uma política de mapeamento de reclamações atribuída ao seu principal de serviço, o token padrão é emitido (a apólice não tem efeito).

## <a name="claims-mapping-policy-assignment"></a>Reclamações de atribuição de política de mapeamento

As políticas de mapeamento de sinistros só podem ser atribuídas a objetos principais de serviço.

### <a name="example-claims-mapping-policies"></a>Exemplo reivindica políticas de mapeamento

No Azure AD, muitos cenários são possíveis quando você pode personalizar reclamações emitidas em fichas para diretores de serviço específicos. Nesta secção, passamos por alguns cenários comuns que podem ajudá-lo a compreender como usar o tipo de política de mapeamento de sinistros.

Ao criar uma política de mapeamento de reclamações, também pode emitir uma reclamação a partir de um atributo de extensão de esquema de diretório em fichas. Utilize *extensionID* para o atributo de extensão em vez de *ID* no `ClaimsSchema` elemento.  Para obter mais informações sobre os atributos de extensão, consulte [utilizando atributos de extensão de esquema de diretório](active-directory-schema-extensions.md).

#### <a name="prerequisites"></a>Pré-requisitos

Nos exemplos seguintes, cria, atualiza, liga e elimina políticas para os principais serviços. Se você é novo no AZure AD, recomendamos que você [aprenda sobre como obter um inquilino AZure AD](quickstart-create-new-tenant.md) antes de prosseguir com estes exemplos.

Para começar, faça os seguintes passos:

1. Descarregue o mais recente lançamento público do [Módulo Ad PowerShell Azure.](https://www.powershellgallery.com/packages/AzureADPreview)
1. Executar o comando 'Ligar' para iniciar súm na sua conta de administração Azure AD. Executar este comando cada vez que começar uma nova sessão.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Para ver todas as políticas que foram criadas na sua organização, executar o seguinte comando. Recomendamos que comande este comando após a maioria das operações nos seguintes cenários, para verificar se as suas políticas estão a ser criadas como esperado.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política para omitir as alegações básicas de fichas emitidas a um comitente de serviço

Neste exemplo, cria-se uma política que elimina a alegação básica definida de fichas emitidas a princípios de serviços ligados.

1. Crie uma política de mapeamento de reclamações. Esta política, ligada a princípios de serviço específicos, elimina o conjunto de alegações básicas dos tokens.
   1. Para criar a política, executar este comando:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Para ver a sua nova política, e para obter a política ObjectId, executar o seguinte comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribua a apólice ao seu diretor de serviço. Também precisa de obter o ObjectId do seu principal de serviço.
   1. Para ver todos os principais de serviço da sua organização, pode [consultar a Microsoft Graph API](/graph/traverse-the-graph). Ou, no [Microsoft Graph Explorer,](https://developer.microsoft.com/graph/graph-explorer)inscreva-se na sua conta AZure AD.
   2. Quando tiver o ObjectId do seu principal de serviço, executar o seguinte comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política para incluir o StaffID e o TenantCountry como reclamações em fichas emitidas a um diretor de serviços

Neste exemplo, cria-se uma política que adiciona o StaffID e o TenantCountry aos tokens emitidos aos principais de serviços ligados. O EmployeeID é emitido como o tipo de reclamação de nome tanto em fichas SAML como JWTs. O TenantCountry é emitido como o tipo de reivindicação país/região em fichas SAML e JWTs. Neste exemplo, continuamos a incluir as alegações básicas fixadas nos tokens.

1. Crie uma política de mapeamento de reclamações. Esta política, ligada a princípios de serviço específicos, adiciona o StaffID e o TenantCountry reclama a tokens.
   1. Para criar a apólice, executar o seguinte comando:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Para ver a sua nova política, e para obter a política ObjectId, executar o seguinte comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribua a apólice ao seu diretor de serviço. Também precisa de obter o ObjectId do seu principal de serviço.
   1. Para ver todos os principais de serviço da sua organização, pode [consultar a Microsoft Graph API](/graph/traverse-the-graph). Ou, no [Microsoft Graph Explorer,](https://developer.microsoft.com/graph/graph-explorer)inscreva-se na sua conta AZure AD.
   2. Quando tiver o ObjectId do seu principal de serviço, executar o seguinte comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política que utilize uma transformação de sinistros em fichas emitidas a um diretor de serviços

Neste exemplo, cria-se uma política que emite uma reivindicação personalizada "JoinedData" a JWTs emitida aos principais de serviços ligados. Esta alegação contém um valor criado juntando os dados armazenados no atributo extensionattribute1 no objeto do utilizador com ".sandbox". Neste exemplo, excluímos as alegações básicas fixadas nos tokens.

1. Crie uma política de mapeamento de reclamações. Esta política, ligada a princípios de serviço específicos, adiciona o StaffID e o TenantCountry reclama a tokens.
   1. Para criar a apólice, executar o seguinte comando:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Para ver a sua nova política, e para obter a política ObjectId, executar o seguinte comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribua a apólice ao seu diretor de serviço. Também precisa de obter o ObjectId do seu principal de serviço.
   1. Para ver todos os principais de serviço da sua organização, pode [consultar a Microsoft Graph API](/graph/traverse-the-graph). Ou, no [Microsoft Graph Explorer,](https://developer.microsoft.com/graph/graph-explorer)inscreva-se na sua conta AZure AD.
   2. Quando tiver o ObjectId do seu principal de serviço, executar o seguinte comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>Considerações de segurança

As aplicações que recebem fichas baseiam-se no facto de os valores de reclamação serem emitidos de forma autoritária pela Azure AD e não poderem ser adulterados. No entanto, quando modificar o conteúdo do token através de políticas de mapeamento de sinistros, estes pressupostos podem deixar de estar corretos. As aplicações devem reconhecer explicitamente que os tokens foram modificados pelo criador da política de mapeamento de sinistros para se protegerem das políticas de mapeamento de sinistros criadas por atores mal-intencionados. Isto pode ser feito das seguintes formas:

- Configurar uma chave de assinatura personalizada
- Atualizar o manifesto de aplicação para aceitar reclamações mapeadas.
 
Sem isso, a Azure AD devolverá um [ `AADSTS50146` código de erro](reference-aadsts-error-codes.md#aadsts-error-codes).

### <a name="custom-signing-key"></a>Chave de assinatura personalizada

Para adicionar uma chave de assinatura personalizada ao objeto principal de serviço, pode utilizar o cmdlet Azure PowerShell [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) para criar uma credencial chave de certificado para o seu objeto Aplicação.

As aplicações que tenham pedido mapeamento de sinistros habilitadas devem validar as suas chaves de assinatura simbólicas, anexando `appid={client_id}` os seus [pedidos de metadados OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Abaixo está o formato do documento de metadados OpenID Connect que deve utilizar:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Atualizar o manifesto de aplicação

Em alternativa, pode definir o `acceptMappedClaims` imóvel `true` no manifesto de [aplicação.](reference-app-manifest.md) Como documentado no [tipo de recurso apiApplication,](/graph/api/resources/apiapplication#properties)isto permite que uma aplicação utilize mapeamento de sinistros sem especificar uma chave de assinatura personalizada.

Isto requer que o público simbólico solicitado utilize um nome de domínio verificado do seu inquilino AZure AD, o que significa que deve garantir a definição `Application ID URI` do (representado pelo `identifierUris` manifesto de aplicação) por exemplo para `https://contoso.com/my-api` ou (simplesmente usando o nome de inquilino padrão) `https://contoso.onmicrosoft.com/my-api` .

Se não estiver a utilizar um domínio verificado, o Azure AD devolverá um código de `AADSTS501461` erro com a mensagem *"AcceptMappedClaims é suportado apenas para um público simbólico que corresponda à aplicação GUID ou a uma audiência dentro dos domínios verificados do inquilino. Ou altera o identificador de recursos ou usa uma chave de assinatura específica para aplicações."*

## <a name="see-also"></a>Ver também

- Para saber como personalizar as reclamações emitidas no token SAML através do portal Azure, consulte [Como: Personalizar reclamações emitidas no token SAML para aplicações empresariais](active-directory-saml-claims-customization.md)
- Para saber mais sobre os atributos de extensão, consulte [utilizando atributos de extensão de esquema de diretório em sinistros](active-directory-schema-extensions.md).
