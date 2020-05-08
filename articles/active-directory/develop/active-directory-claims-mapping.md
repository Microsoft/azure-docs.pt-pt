---
title: Personalize as alegações de aplicativos de inquilino da Azure AD (PowerShell)
titleSuffix: Microsoft identity platform
description: Esta página descreve o Webe Ative Directory alega mapeamento.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: d8be2c8cc70db963252054a39cad558c4c1b5bd2
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871214"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Como: Personalizar reclamações emitidas em fichas para uma aplicação específica num inquilino (Pré-visualização)

> [!NOTE]
> Esta funcionalidade substitui e substitui a personalização de [reclamações](active-directory-saml-claims-customization.md) oferecida através do portal de hoje. Na mesma aplicação, se personalizar as reclamações utilizando o portal para além do método Graph/PowerShell detalhado neste documento, os tokens emitidos para essa aplicação ignorarão a configuração no portal. As configurações efetuadas através dos métodos detalhados neste documento não se refletirão no portal.

Esta funcionalidade é utilizada pelos administradores dos inquilinos para personalizar as reclamações emitidas em fichas para uma aplicação específica no seu inquilino. Pode utilizar políticas de mapeamento de reclamações para:

- Selecione quais reclamações estão incluídas em fichas.
- Criar tipos de reclamação que ainda não existem.
- Escolha ou altere a fonte de dados emitidos em reclamações específicas.

> [!NOTE]
> Esta capacidade está atualmente em pré-visualização pública. Esteja preparado para reverter ou remover todas as alterações. A funcionalidade está disponível em qualquer subscrição do Azure Ative Directory (Azure AD) durante a pré-visualização pública. No entanto, quando a funcionalidade se torna geralmente disponível, alguns aspetos da funcionalidade podem requerer uma subscrição premium Azure AD. Esta funcionalidade suporta a configuração de políticas de mapeamento de reclamações para protocolos WS-Fed, SAML, OAuth e OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Tipo de política de mapeamento de sinistros

Em Azure AD, um objeto **político** representa um conjunto de regras aplicadas em aplicações individuais ou em todas as aplicações de uma organização. Cada tipo de política tem uma estrutura única, com um conjunto de propriedades que são depois aplicadas a objetos aos quais são atribuídos.

Uma política de mapeamento de sinistros é um tipo de objeto **político** que modifica as reclamações emitidas em fichas emitidas para aplicações específicas.

## <a name="claim-sets"></a>Conjuntos de reclamações

Há certos conjuntos de alegações que definem como e quando são usadas em fichas.

| Conjunto de reclamações | Descrição |
|---|---|
| Conjunto de reivindicação do núcleo | Estão presentes em todos os símbolos, independentemente da política. Estas alegações também são consideradas restritas e não podem ser modificadas. |
| Conjunto de reclamações básicas | Inclui as reclamações que são emitidas por defeito para fichas (além do conjunto de reclamação principal). Pode omiti-lo ou modificar as reivindicações básicas utilizando as políticas de mapeamento de sinistros. |
| Conjunto de reclamações restritas | Não pode ser modificado usando a política. A fonte de dados não pode ser alterada, e nenhuma transformação é aplicada ao gerar estas alegações. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Quadro 1: Conjunto de reclamações restritas jSON Web Token (JWT)

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
| aperitivo |
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
| cSr |
| csr_type |
| dispositivo |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-mail |
| endpoint |
| enfpolidas |
| exp |
| expires_on |
| grant_type |
| gráfico |
| group_sids |
| grupos |
| grupos hasgroups |
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
| provedor de identidade |
| idp |
| in_corp |
| instância |
| ipaddr |
| isbrowserhostapp |
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
| maré |
| tokenAutologonEnabled |
| trustfordelegação |
| unique_name |
| upn |
| user_setting_sync_url |
| o nome de utilizador |
| uti |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Quadro 2: Conjunto de reclamações restritas SAML

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

## <a name="claims-mapping-policy-properties"></a>Propriedades políticas de mapeamento de sinistros

Para controlar as reclamações emitidas e de onde vêm os dados, utilize as propriedades de uma política de mapeamento de sinistros. Se uma política não for definida, o sistema emite fichas que incluem o conjunto de reclamações fundamentais, o conjunto de reclamações básicas e quaisquer [reclamações opcionais](active-directory-optional-claims.md) que o pedido tenha optado por receber.

### <a name="include-basic-claim-set"></a>Incluir conjunto básico de reclamação

**Cadeia:** Incluir O Conjunto de Reclamações Básicas

**Tipo de dados:** Boolean (Verdadeiro ou Falso)

**Resumo:** Esta propriedade determina se o conjunto de reclamações básicas está incluído em fichas afetadas por esta política.

- Se for definido como Verdadeiro, todas as reclamações no conjunto de reivindicações básicas são emitidas em fichas afetadas pela política. 
- Se definidos para Falso, as reclamações no conjunto de reclamações básicas não estão nos tokens, a menos que sejam adicionadas individualmente na propriedade de sinistros da mesma política.

> [!NOTE] 
> As reclamações no conjunto de reclamações principais estão presentes em cada token, independentemente do que esta propriedade está definida. 

### <a name="claims-schema"></a>Sinistros esquema

**Cadeia:** SinistrosSchema

**Tipo de dados:** JSON blob com uma ou mais entradas de esquema de reivindicação

**Resumo:** Esta propriedade define quais as reclamações presentes nas fichas afetadas pela apólice, para além do conjunto de reclamações básicas e do conjunto de reclamações fundamentais.
Para cada reclamação, é necessária uma entrada de esquemadefinido neste imóvel, é necessária determinada suposição. Especifique de onde vêm os dados ( Par de**Valor** ou **Fonte/ID),** e que afirmam que os dados são emitidos como (**Tipo de reclamação).**

### <a name="claim-schema-entry-elements"></a>Reclamar elementos de entrada de esquemas

**Valor:** O elemento Valor define um valor estático como os dados a emitir na reclamação.

**Par de fonte/id:** Os elementos Fonte e ID definem de onde os dados da reclamação são obtidos. 

Desloque o elemento Fonte a um dos seguintes valores: 

- "utilizador": Os dados da reclamação são uma propriedade no objeto utilizador. 
- "aplicação": Os dados da reclamação são um imóvel no principal de serviço de aplicação (cliente). 
- "recurso": Os dados da reclamação são uma propriedade no principal do serviço de recursos.
- "público": Os dados da reclamação são uma propriedade no principal de serviço que é o público do token (quer o cliente quer o principal do serviço de recursos).
- "empresa": Os dados da reclamação são um imóvel sobre o objeto da Empresa do Arrendatário de Recursos.
- "Transformação": Os dados da reclamação são de transformação de sinistros (ver a secção "Transformação de Sinistros" mais tarde neste artigo).

Se a fonte for transformação, o elemento **TransformationID** deve ser incluído nesta definição de reivindicação também.

O elemento ID identifica qual o imóvel da fonte que fornece o valor para a reclamação. A tabela seguinte lista os valores de ID válidos para cada valor da Fonte.

#### <a name="table-3-valid-id-values-per-source"></a>Quadro 3: Valores de ID válidos por fonte

| Origem | ID | Descrição |
|-----|-----|-----|
| Utilizador | surname | Nome de família |
| Utilizador | givenname | Nome Próprio |
| Utilizador | nome de exibição | Nome a Apresentar |
| Utilizador | objectide | ObjectID |
| Utilizador | correio | Endereço de E-mail |
| Utilizador | nome principal de utilizador | Nome Principal de Utilizador |
| Utilizador | departamento|Departamento|
| Utilizador | onpremisessamaccountname | No local, nome da conta SAM |
| Utilizador | netbiosname| Nome NetBios |
| Utilizador | nome dnsdomain | Nome de Domínio DNS |
| Utilizador | identificador de segurança no local | Identificador de segurança no local |
| Utilizador | nome da empresa| Nome da Organização |
| Utilizador | endereço de rua | Rua |
| Utilizador | código postal | Código Postal |
| Utilizador | preferredlanguange | Língua Preferida |
| Utilizador | onpremiseuserprincipalname | On-premiseUPN |
| Utilizador | apelido de correio | Apelido de Correio |
| Utilizador | extensãoatribuído1 | Atributo de extensão 1 |
| Utilizador | extensãoatribuído2 | Atributo de extensão 2 |
| Utilizador | extensãoatribui3 | Atributo de extensão 3 |
| Utilizador | extensãoatribuído4 | Atributo de extensão 4 |
| Utilizador | extensãoatribuído5 | Atributo de extensão 5 |
| Utilizador | extensãoatribui6 | Atributo de extensão 6 |
| Utilizador | extensãoatribuído7 | Atributo de extensão 7 |
| Utilizador | extensãoatribui8 | Atributo de extensão 8 |
| Utilizador | extensãoatribui9 | Atributo de extensão 9 |
| Utilizador | extensãoatribuído10 | Atributo de extensão 10 |
| Utilizador | extensãoatribuído11 | Atributo de extensão 11 |
| Utilizador | extensãoatribuído12 | Atributo de extensão 12 |
| Utilizador | extensãoatribuído13 | Atributo de extensão 13 |
| Utilizador | extensãoatribuído14 | Atributo de extensão 14 |
| Utilizador | extensãoatribuído15 | Atributo de extensão 15 |
| Utilizador | outro correio | Outros Correios |
| Utilizador | país | País |
| Utilizador | city | Localidade |
| Utilizador | state | Estado |
| Utilizador | título de emprego | Cargo |
| Utilizador | funcionário | ID de Empregado |
| Utilizador | facsimilephonenumber | Número de telefone facsímile |
| aplicação, recurso, público | nome de exibição | Nome a Apresentar |
| aplicação, recurso, público | objeto | ObjectID |
| aplicação, recurso, público | etiquetas | Etiqueta principal de serviço |
| Empresa | país inquilino | País do arrendatário |

**TransformationID:** O elemento TransformationID só deve ser fornecido se o elemento Fonte estiver definido para "transformação".

- Este elemento deve coincidir com o elemento ID da entrada de transformação na propriedade **ClaimsTransformation** que define como os dados desta reclamação são gerados.

**Tipo de reclamação:** Os elementos **JwtClaimType** e **SamlClaimType** definem a que alegam que esta inscrição de esquema de reclamação se refere.

- O JwtClaimType deve conter o nome da alegação a emitir em JWTs.
- O SamlClaimType deve conter o URI da alegação a emitir em fichas SAML.

> [!NOTE]
> Os nomes e URIs de reclamações no conjunto de reclamações restritas não podem ser utilizados para os elementos do tipo de reclamação. Para mais informações, consulte a secção "Exceções e restrições" mais tarde neste artigo.

### <a name="claims-transformation"></a>Transformação de afirmações

**Cadeia:** Transformação de Sinistros

**Tipo de dados:** Bolha JSON, com uma ou mais entradas de transformação 

**Resumo:** Utilize esta propriedade para aplicar transformações comuns a dados de origem, para gerar os dados de saída para sinistros especificados no Schema de Sinistros.

**ID:** Utilize o elemento ID para fazer referência a esta entrada de transformação na entrada TransformationID Claims Schema. Este valor deve ser único para cada entrada de transformação dentro desta política.

**Método de Transformação:** O elemento TransformationMethod identifica qual a operação realizada para gerar os dados para a reclamação.

Com base no método escolhido, espera-se um conjunto de inputs e saídas. Defina as inputs e saídas utilizando os elementos **InputClaims,** **InputParameters** e **OutputClaims.**

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Quadro 4: Métodos de transformação e inputs e saídas esperados

|Método de Transformação|Entrada esperada|Resultado esperado|Descrição|
|-----|-----|-----|-----|
|Associar|string1, string2, separador|saídaReclamada|Junta cordas de entrada utilizando um separador no meio. Por exemplo: string1:"foo@bar.com" string2:"sandbox", separador:"." resultafoo@bar.com.sandboxem saídaClaim:"|
|ExtractmailPrefix|correio|saídaReclamada|Extrai a parte local de um endereço de e-mail. Por exemplo: correio:"foo@bar.com" "resulta em saídaClaim:"foo". Se \@ não houver sinal, então a cadeia de entrada original é devolvida como está.|

**Créditos de entrada:** Utilize um elemento InputClaims para passar os dados de uma entrada de esquema de reclamação para uma transformação. Tem dois atributos: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** é unido com o elemento ID da entrada do esquema de reclamação para encontrar a reclamação de entrada apropriada. 
- **TransformationClaimType** é usado para dar um nome único a esta entrada. Este nome deve corresponder a uma das inputs esperadas para o método de transformação.

**InputParameters:** Utilize um elemento InputParameters para passar um valor constante a uma transformação. Tem dois atributos: **Valor** e **ID**.

- **Valor** é o valor real constante a ser passado.
- **O ID** é usado para dar um nome único à entrada. O nome deve corresponder a uma das inputs esperadas para o método de transformação.

**OutputClaims:** Utilize um elemento OutputClaims para manter os dados gerados por uma transformação e amarrá-los a uma entrada de esquema de reclamação. Tem dois atributos: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** é acompanhado pelo ID da entrada schema de reclamação para encontrar a reclamação de saída apropriada.
- **TransformationClaimType** é usado para dar um nome único à saída. O nome deve coincidir com uma das saídas esperadas para o método de transformação.

### <a name="exceptions-and-restrictions"></a>Exceções e restrições

**SAML NameID e UPN:** Os atributos a partir dos quais se fornecem os valores NameID e UPN, e as transformações de sinistros que são permitidas, são limitados. Consulte a tabela 5 e a tabela 6 para ver os valores permitidos.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Quadro 5: Atributos permitidos como fonte de dados para O NameID SAML

|Origem|ID|Descrição|
|-----|-----|-----|
| Utilizador | correio|Endereço de E-mail|
| Utilizador | nome principal de utilizador|Nome Principal de Utilizador|
| Utilizador | onpremisessamaccountname|Nas instalações do nome da conta Sam|
| Utilizador | funcionário|ID de Empregado|
| Utilizador | extensãoatribuído1 | Atributo de extensão 1 |
| Utilizador | extensãoatribuído2 | Atributo de extensão 2 |
| Utilizador | extensãoatribui3 | Atributo de extensão 3 |
| Utilizador | extensãoatribuído4 | Atributo de extensão 4 |
| Utilizador | extensãoatribuído5 | Atributo de extensão 5 |
| Utilizador | extensãoatribui6 | Atributo de extensão 6 |
| Utilizador | extensãoatribuído7 | Atributo de extensão 7 |
| Utilizador | extensãoatribui8 | Atributo de extensão 8 |
| Utilizador | extensãoatribui9 | Atributo de extensão 9 |
| Utilizador | extensãoatribuído10 | Atributo de extensão 10 |
| Utilizador | extensãoatribuído11 | Atributo de extensão 11 |
| Utilizador | extensãoatribuído12 | Atributo de extensão 12 |
| Utilizador | extensãoatribuído13 | Atributo de extensão 13 |
| Utilizador | extensãoatribuído14 | Atributo de extensão 14 |
| Utilizador | extensãoatribuído15 | Atributo de extensão 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Quadro 6: Métodos de transformação permitidos para o Nome De SAML

| Método de Transformação | Restrições |
| ----- | ----- |
| ExtractmailPrefix | Nenhum |
| Associar | O sufixo que está a ser associado deve ser um domínio verificado do inquilino de recursos. |

### <a name="custom-signing-key"></a>Chave de assinatura personalizada

Uma chave de assinatura personalizada deve ser atribuída ao principal objeto do serviço para que uma política de mapeamento de sinistros entre em vigor. Isto garante o reconhecimento de que os tokens foram modificados pelo criador da política de mapeamento de sinistros e protege as aplicações de políticas de mapeamento de sinistros criadas por intervenientes maliciosos. Para adicionar uma chave de assinatura personalizada, pode utilizar o `new-azureadapplicationkeycredential` cmdlet Azure PowerShell para criar uma credencial de chave simétrica para o seu objeto Aplicação. Para obter mais informações sobre este cmdlet Azure PowerShell, consulte [New-AzureADApplicationKeyCredential](https://docs.microsoft.com/powerShell/module/Azuread/New-AzureADApplicationKeyCredential?view=azureadps-2.0).

As aplicações que tenham o mapeamento de sinistros ativadas devem validar as suas chaves de assinatura simbólicas, anexando-se `appid={client_id}` aos seus pedidos de [metadados OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Abaixo está o formato do documento de metadados OpenID Connect que deve utilizar: 

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>Cenários de inquilinos cruzados

As políticas de mapeamento de sinistros não se aplicam aos utilizadores convidados. Se um utilizador convidado tentar aceder a uma aplicação com uma política de mapeamento de sinistros atribuída ao seu diretor de serviço, o token predefinido é emitido (a apólice não tem efeito).

## <a name="claims-mapping-policy-assignment"></a>Atribuição de política de mapeamento de sinistros

As políticas de mapeamento de sinistros só podem ser atribuídas aos objetos principais de serviço.

### <a name="example-claims-mapping-policies"></a>Exemplo reivindica políticas de mapeamento

Em Azure AD, muitos cenários são possíveis quando você pode personalizar reclamações emitidas em tokens para diretores de serviço específicos. Nesta secção, percorremos alguns cenários comuns que podem ajudá-lo a entender como usar o tipo de política de mapeamento de reclamações.

#### <a name="prerequisites"></a>Pré-requisitos

Nos seguintes exemplos, cria, atualiza, liga e elimina políticas para os diretores de serviços. Se você é novo em Azure AD, recomendamos que você [aprenda sobre como obter um inquilino Azure AD](quickstart-create-new-tenant.md) antes de prosseguir com estes exemplos.

Para começar, faça os seguintes passos:

1. Descarregue o mais recente lançamento público de [pré-visualização do Módulo PowerShell AzurShell.](https://www.powershellgallery.com/packages/AzureADPreview)
1. Execute o comando Connect para iniciar sessão na sua conta de administração Azure AD. Execute este comando sempre que iniciar uma nova sessão.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Para ver todas as políticas que foram criadas na sua organização, executar o seguinte comando. Recomendamos que dirija este comando após a maioria das operações nos seguintes cenários, para verificar se as suas políticas estão a ser criadas como esperado.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política para omitir as alegações básicas dos tokens emitidos a um diretor de serviços

Neste exemplo, cria-se uma política que remove a reivindicação básica definida a partir de fichas emitidas para os principais de serviços ligados.

1. Crie uma política de mapeamento de reivindicações. Esta política, ligada a princípios de serviço específicos, remove a reivindicação básica definida a partir de fichas.
   1. Para criar a política, executar este comando: 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Para ver a sua nova política, e para obter a política ObjectId, executar o seguinte comando:
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribua a apólice ao seu diretor de serviço. Também precisa de obter o ObjectId do seu diretor de serviço.
   1. Para ver todos os diretores de serviço da sua organização, pode [consultar a Microsoft Graph API](/graph/traverse-the-graph). Ou, no [Microsoft Graph Explorer,](https://developer.microsoft.com/graph/graph-explorer)inscreva-se na sua conta Azure AD.
   2. Quando tiver o ObjectId do seu diretor de serviço, execute o seguinte comando:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política para incluir o EmployeeID e o TenantCountry como reclamações em fichas emitidas a um diretor de serviços

Neste exemplo, cria-se uma política que adiciona o EmployeeID e o TenantCountry aos tokens emitidos aos principais de serviços ligados. O EmployeeID é emitido como o tipo de reclamação de nome em ambas as fichas SAML e JWTs. O TenantCountry é emitido como o tipo de reivindicação do país em tokens SAML e JWTs. Neste exemplo, continuamos a incluir as alegações básicas estabelecidas nos símbolos.

1. Crie uma política de mapeamento de reivindicações. Esta política, ligada a princípios de serviço específicos, adiciona as reivindicações do EmployeeID e do TenantCountry aos tokens.
   1. Para criar a política, executar o seguinte comando:  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Para ver a sua nova política, e para obter a política ObjectId, executar o seguinte comando:
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. Atribua a apólice ao seu diretor de serviço. Também precisa de obter o ObjectId do seu diretor de serviço. 
   1. Para ver todos os diretores de serviço da sua organização, pode [consultar a Microsoft Graph API](/graph/traverse-the-graph). Ou, no [Microsoft Graph Explorer,](https://developer.microsoft.com/graph/graph-explorer)inscreva-se na sua conta Azure AD.
   2. Quando tiver o ObjectId do seu diretor de serviço, execute o seguinte comando:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política que utilize uma transformação de sinistros em fichas emitidas a um diretor de serviços

Neste exemplo, cria-se uma política que emite uma reivindicação personalizada "JoinedData" a JWTs emitida sorveros de serviço ligados. Esta alegação contém um valor criado ao juntar os dados armazenados no atributo extensão1 no objeto do utilizador com ".sandbox". Neste exemplo, excluímos as reivindicações básicas estabelecidas nos símbolos.

1. Crie uma política de mapeamento de reivindicações. Esta política, ligada a princípios de serviço específicos, adiciona as reivindicações do EmployeeID e do TenantCountry aos tokens.
   1. Para criar a política, executar o seguinte comando:
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Para ver a sua nova política, e para obter a política ObjectId, executar o seguinte comando: 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribua a apólice ao seu diretor de serviço. Também precisa de obter o ObjectId do seu diretor de serviço. 
   1. Para ver todos os diretores de serviço da sua organização, pode [consultar a Microsoft Graph API](/graph/traverse-the-graph). Ou, no [Microsoft Graph Explorer,](https://developer.microsoft.com/graph/graph-explorer)inscreva-se na sua conta Azure AD.
   2. Quando tiver o ObjectId do seu diretor de serviço, execute o seguinte comando: 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Consulte também

Para aprender a personalizar as reclamações emitidas no token SAML através do portal Azure, consulte [Como: Personalizar as reclamações emitidas no token SAML para aplicações empresariais](active-directory-saml-claims-customization.md)
