---
title: Configurar declarações de grupo para aplicativos com Azure Active Directory | Microsoft Docs
description: Informações sobre como configurar declarações de grupo para uso com o Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 3cb53656adb1dbeb5e5597d02edfe5be4dbec6a8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170481"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Configurar declarações de grupo para aplicativos com Azure Active Directory (visualização pública)

Azure Active Directory pode fornecer informações de associação de grupo de usuários em tokens para uso em aplicativos.  Há suporte para dois padrões principais:

- Grupos identificados por seu atributo de OID (identificador de objeto Azure Active Directory) (disponível para o público)
- Grupos identificados por atributos sAMAccountName ou GroupId para grupos e usuários sincronizados com Active Directory (AD) (visualização pública)

> [!IMPORTANT]
> Há várias advertências a serem observadas para essa funcionalidade de visualização:
>
>- O suporte para o uso de atributos sAMAccountName e SID (identificador de segurança) sincronizados do local foi projetado para habilitar a movimentação de aplicativos existentes do AD FS e de outros provedores de identidade. Os grupos gerenciados no Azure AD não contêm os atributos necessários para emitir essas declarações.
>- Em organizações maiores, o número de grupos de que um usuário é membro pode exceder o limite que Azure Active Directory será adicionado a um token. 150 grupos para um token SAML e 200 para um JWT. Isso pode levar a resultados imprevisíveis. Se esse for um problema potencial, recomendamos testar e, se necessário, aguardando até adicionarmos aprimoramentos para permitir que você restrinja as declarações aos grupos relevantes para o aplicativo.  
>- Para o novo desenvolvimento de aplicativos, ou em casos em que o aplicativo pode ser configurado para ele e onde o suporte a grupos aninhados não é necessário, é recomendável que a autorização no aplicativo seja baseada em funções de aplicativo em vez de grupos.  Isso limita a quantidade de informações que precisam entrar no token, é mais seguro e separa a atribuição de usuário da configuração do aplicativo.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Declarações de grupo para aplicativos migrando do AD FS e de outros provedores de identidade

Muitos aplicativos configurados para autenticar com AD FS dependem de informações de associação de grupo na forma de atributos de grupo do Windows AD.   Esses atributos são o sAMAccountName do grupo, que pode ser qualificado por nome de domínio ou o identificador de segurança do grupo do Windows (GroupId).  Quando o aplicativo é federado com AD FS, AD FS usa a função TokenGroups para recuperar as associações de grupo para o usuário.

Para corresponder ao token que um aplicativo receberia de AD FS, as declarações de grupo e função podem ser emitidas contendo o sAMAccountName qualificado pelo domínio em vez da objectID de Azure Active Directory do grupo.

Os formatos com suporte para declarações de grupo são:

- **ObjectID do grupo de Azure Active Directory** (Disponível para todos os grupos)
- **sAMAccountName** (Disponível para grupos sincronizados do Active Directory)
- **NetbiosDomain\sAMAccountName** (Disponível para grupos sincronizados do Active Directory)
- **DNSDomainName\sAMAccountName** (Disponível para grupos sincronizados do Active Directory)
- **Identificador de segurança do grupo local** (Disponível para grupos sincronizados do Active Directory)

> [!NOTE]
> os atributos sAMAccountName e SID do grupo local só estão disponíveis em objetos de grupo sincronizados a partir de Active Directory.   Eles não estão disponíveis em grupos criados no Azure Active Directory ou no office365.   Os aplicativos configurados no Azure Active Directory para serem sincronizados com atributos de grupo locais os obtêm somente para grupos sincronizados.

## <a name="options-for-applications-to-consume-group-information"></a>Opções para que os aplicativos consumam informações de grupo

Uma maneira de os aplicativos obterem informações de grupo é chamar o ponto de extremidade de grupos de gráficos para recuperar a associação de grupo para o usuário autenticado. Essa chamada garante que todos os grupos dos quais um usuário é membro estejam disponíveis mesmo quando há um grande número de grupos envolvidos e o aplicativo precisa enumerar todos os grupos dos quais o usuário é membro.  A enumeração de grupo, em seguida, é independente das limitações de tamanho do token.

No entanto, se um aplicativo existente já espera consumir informações de grupo por meio de declarações no token que recebe, Azure Active Directory pode ser configurado com várias opções de declarações diferentes para atender às necessidades do aplicativo.  Considere as seguintes opções:

- Ao usar a associação de grupo para fins de autorização no aplicativo, é preferível usar o ObjectID do grupo, que é imutável e exclusivo em Azure Active Directory e está disponível para todos os grupos.
- Se estiver usando o sAMAccountName do grupo local para autorização, use nomes qualificados do domínio;  Há menos chances de situações decorrentes dos nomes conflitantes. o sAMAccountName por si só pode ser exclusivo dentro de um domínio Active Directory, mas se mais de um domínio Active Directory for sincronizado com um locatário Azure Active Directory, haverá a possibilidade de que mais de um grupo tenha o mesmo nome.
- Considere o uso de [funções de aplicativo](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) para fornecer uma camada de indireção entre a associação de grupo e o aplicativo.   Em seguida, o aplicativo faz decisões de autorização interna com base em no token.
- Se o aplicativo estiver configurado para obter atributos de grupo que são sincronizados de Active Directory e um grupo não contiver esses atributos, ele não será incluído nas declarações.
- As declarações de grupo em tokens incluem grupos aninhados.   Se um usuário for membro de GroupB e GroupB for um membro de GroupA, as declarações de grupo para o usuário conterão GroupA e GroupB. Para organizações com uso intensivo de grupos aninhados e usuários com um grande número de associações de grupo, o número de grupos listados no token pode aumentar o tamanho do token.   Azure Active Directory limita o número de grupos que emitirá em um token para 150 para asserções SAML e 200 para JWT para evitar que tokens fiquem muito grandes.  Se um usuário for membro de um número maior de grupos do que o limite, os grupos serão emitidos junto com um link para o ponto de extremidade do grafo para obter informações sobre o grupo.

> Pré-requisitos para usar atributos de grupo sincronizados do Active Directory:   Os grupos devem ser sincronizados Active Directory usando Azure AD Connect.

Há duas etapas para configurar Azure Active Directory para emitir nomes de grupo para grupos de Active Directory.

1. **Sincronizar nomes de grupo de Active Directory** Antes que Azure Active Directory possa emitir os nomes de grupo ou o SID do grupo local em declarações de grupo ou função, os atributos necessários precisam ser sincronizados do Active Directory.  Você deve estar executando Azure AD Connect versão 1.2.70 ou posterior.   Antes da versão 1.2.70 Azure AD Connect sincronizará os objetos de grupo de Active Directory, mas não incluirá os atributos de nome de grupo necessários por padrão.  Você deve atualizar para a versão atual.

2. **Configurar o registro do aplicativo no Azure Active Directory para incluir declarações de grupo em tokens** As declarações de grupo podem ser configuradas na seção aplicativos empresariais do portal para uma galeria ou aplicativo de SSO do SAML não Galeria, ou usando o manifesto do aplicativo na seção registros do aplicativo.  Para configurar as declarações de grupo no manifesto do aplicativo, consulte "Configurando o Azure Active Directory registro de aplicativo para atributos de grupo" abaixo.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>Configurar declarações de grupo para aplicativos SAML usando a configuração de SSO

Para configurar declarações de grupo para um aplicativo SAML de galeria ou não Galeria, abra aplicativos empresariais, clique no aplicativo na lista e selecione configuração de logon único.

Selecione o ícone de edição ao lado de "grupos retornados no token"

![interface do usuário de declarações](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Use os botões de opção para selecionar quais grupos devem ser incluídos no token

![interface do usuário de declarações](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Seleção | Descrição |
|----------|-------------|
| **Todos os grupos** | Emite grupos de segurança e listas de distribuição.   Ele também faz com que as funções de diretório que o usuário está atribuído sejam emitidas em uma declaração de ' wids ' e qualquer função de aplicativo à qual o usuário seja emitido na declaração de funções. |
| **Grupos de segurança** | Emite grupos de segurança dos quais o usuário é membro na declaração de grupos |
| **Listas de distribuição** | Emite listas de distribuição para as quais o usuário é membro |
| **Funções de diretório** | Se as funções de diretório atribuídas ao usuário forem emitidas como uma declaração ' wids ' (a declaração de grupos não será emitida) |

Por exemplo, para emitir todos os grupos de segurança dos quais o usuário é membro, selecione grupos de segurança

![interface do usuário de declarações](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Para emitir grupos usando Active Directory atributos sincronizados de Active Directory em vez de objectIDs do Azure AD, selecione o formato necessário na lista suspensa.  Isso substitui a ID de objeto nas declarações por valores de cadeia de caracteres que contêm nomes de grupo.   Somente os grupos sincronizados do Active Directory serão incluídos nas declarações.

![interface do usuário de declarações](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

### <a name="advanced-options"></a>Opções avançadas

A maneira como as declarações de grupo são emitidas pode ser modificada pelas configurações em opções avançadas

Personalize o nome da declaração de Grupo:  Se selecionado, um tipo de declaração diferente pode ser especificado para declarações de grupo.   Insira o tipo de declaração no campo nome e o namespace opcional para a declaração no campo namespace.

![interface do usuário de declarações](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Alguns aplicativos exigem que as informações de associação de grupo apareçam na declaração ' role '. Opcionalmente, você pode emitir os grupos do usuário como funções, marcando a caixa ' emitir grupos de declarações de função '.

![interface do usuário de declarações](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Se a opção para emitir dados de grupo como funções for usada, somente os grupos aparecerão na declaração de função.  Qualquer função de aplicativo à qual o usuário está atribuído não aparecerá na declaração de função.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Configurar o registro de aplicativo do Azure AD para atributos de grupo

As declarações de grupo também podem ser configuradas na seção [declarações opcionais](../../active-directory/develop/active-directory-optional-claims.md) do [manifesto do aplicativo](../../active-directory/develop/reference-app-manifest.md).

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

### <a name="examples"></a>Exemplos

Emitir grupos como nomes de grupo nos tokens de acesso OAuth no formato dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Para emitir nomes de grupo a serem retornados no formato netbiosDomain\samAccountName como a declaração de funções nos tokens de ID SAML e OIDC:

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

## <a name="next-steps"></a>Passos seguintes

[O que é a identidade híbrida?](whatis-hybrid-identity.md)
