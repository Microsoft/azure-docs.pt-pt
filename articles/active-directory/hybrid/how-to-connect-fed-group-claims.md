---
title: Configurar afirmações de grupo para aplicações com o Azure Active Directory | Documentos da Microsoft
description: Informações sobre como configurar afirmações de grupo para utilização com o Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.component: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 12ccb4978a8cfbaa7dede8d0093c78da05295fec
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410014"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Configurar afirmações de grupo para aplicações com o Azure Active Directory (pré-visualização pública)

O Azure Active Directory pode fornecer um informações de associação de grupo de utilizadores em tokens para utilização nas aplicações.  São suportados dois padrões principais:

- Grupos identificados pelo respetivo identificador de objeto (OID) (disponível em geral) do Azure Active Directory
- Grupos identificados pelo SAMAccountName ou GroupSID para Active Directory (AD) sincronizado a grupos e utilizadores (pré-visualização pública)

> [!Note]
> Suporte para utilização de nomes e os identificadores de segurança (SIDs) foi concebido para permitir a mover aplicativos existentes do AD FS.    Grupos geridos no Azure AD não contêm os atributos necessários para emitir essas declarações.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-idps"></a>Afirmações de grupo para aplicações de migrar do AD FS e outros IDPs

Muitos aplicativos que estão configurados para autenticar com o AD FS dependem de informações de associação de grupo na forma de atributos de grupo do AD do Windows.   Esses atributos são o grupo de SAMAccountName, que pode ser o nome qualificado por domínio ou o SID de grupo do Windows.  Quando o aplicativo está Federado com o AD FS, o AD FS utiliza a função de TokenGroups para recuperar as associações de grupo para o utilizador.

Para fazer corresponder o token é que recebe uma aplicação do AD FS, afirmações de grupo e a função podem ser emitidas que contém o domínio qualificado SAMAccountName, em vez de objectID do grupo do Azure Active Directory.

Formatos com suporte para afirmações de grupo são:

- **O Azure Active Directory GroupObjectId de diretório** (disponível para todos os grupos)
- **SAMAccountName** (disponível para grupos sincronizados a partir do Active Directory)
- **NetbiosDomain\samAccountName** (disponível para grupos sincronizados a partir do Active Directory)
- **DNSDomainName\samAccountName** (disponível para grupos sincronizados a partir do Active Directory)

> [!NOTE]
> Atributos SAMAccountName e OnPremisesGroupSID só estão disponíveis em objetos de grupo sincronizados a partir do Active Directory.   Não estão disponíveis em grupos criados no Azure Active Directory ou Office 365.   Aplicativos que dependem de atributos de grupo locais obtém-los para apenas grupos sincronizados.

## <a name="options-for-applications-to-consume-group-information"></a>Opções para aplicações consumir informações do grupo

Uma forma para aplicativos para obter informações de grupo é chamar o ponto de extremidade de grupos do gráfico para obter a associação de grupo para o usuário autenticado. Esta chamada garante que todos os grupos de que um utilizador é membro do estão disponíveis mesmo quando há um grande número de grupos de envolvidos e o aplicativo precisa enumerar todos os grupos que o utilizador é membro do.  Enumeração de grupo, em seguida, é independente das limitações de tamanho de token.

No entanto, se um aplicativo existente já espera usar informações de grupo através de afirmações no token que recebe, Azure Active Directory pode ser configurado com um número de opções de afirmações diferentes para atender às necessidades do aplicativo.  Considere as seguintes opções:

- Ao usar a associação de grupo para um objetivo de autorização da aplicação (se a associação de grupo é obtida do token ou de gráfico), é preferível utilizar o ObjectID de grupo, que é imutável e exclusivo no Azure Active Directory e está disponível para todos os grupos .
- Se utilizar o grupo de SAMAccountName para autorização, utilize nomes de domínio qualificado;  tem menos probabilidade de resultantes de situações havia conflito de nomes. SAMAccountName no seu próprio pode ser exclusivo dentro de um domínio do Active Directory, mas se mais de um domínio do Active Directory é sincronizado com um inquilino do Azure Active Directory é a possibilidade de mais de um grupo ter o mesmo nome.
- Considere a utilização [funções de aplicação](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) para fornecer uma camada de indireção entre a associação de grupo e a aplicação.   Em seguida, o aplicativo toma decisões de autorização interno com base em clams de função no token.
- Se a aplicação está configurada para obter os atributos de grupo que são sincronizados a partir do Active Directory e um grupo não contém esses atributos que não ser incluído nas afirmações.
- Afirmações de grupo nos tokens incluem grupos aninhados.   Se um utilizador é membro de GroupB e GroupB é um membro de GroupA, as afirmações de grupo para o utilizador irão conter GroupA e GroupB. Para organizações com uso intensivo de grupos aninhados e os utilizadores com um grande número de associações de grupo, o número de grupos listados no token pode aumentar o tamanho do token.   O Azure Active Directory limita o número de grupos, que ele emite um token de 150 para asserções SAML e 200 para JWT.

> Pré-requisitos para a utilização de atributos de grupo sincronizados do Active Directory:   Os grupos têm de ser sincronizados a partir do Active Directory com o Azure AD Connect.

Existem dois passos para configurar o Azure Active Directory para emitir nomes de grupo para grupos do Active Directory.

1. **Sincronizar os nomes de grupos do Active Directory** antes do Azure Active Directory pode emitir os nomes de grupo ou no grupo local no grupo ou função de afirmações de SID, os atributos necessários tem de ser sincronizadas do Active Directory.  Tem de estar a executar o Azure AD Connect versão 1.2.70 ou posterior.   Antes da versão 1.2.70 do Azure AD Connect irá sincronizar os objetos de th de grupo do Active Directory, mas não inclui os atributos de nome de grupo necessárias por predefinição.  Deve atualizar para a versão atual.

2. **Configurar o registo de aplicação no Azure Active Directory para incluir declarações de grupo nos tokens** afirmações de grupo podem ser configurado na secção de aplicações empresariais do portal para um aplicativo de galeria ou não à Galeria SAML SSO, ou na secção de registos de aplicação a utilizar o manifesto do aplicativo.  Para configurar afirmações de grupo no aplicativo manifesto, consulte "Configurando o registo da aplicação do, Active Directory e do Azure, para atributos de grupo" abaixo.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>Configurar afirmações de grupo para aplicações de SAML com a configuração de SSO

Para configurar afirmações de grupo para um aplicativo de galeria ou não à Galeria SAML, abrir aplicações empresariais, clique na aplicação na lista e selecione a configuração de início de sessão único.

Selecione o ícone de edição ao lado de "Grupos devolvidas num token"

![afirmações da interface do Usuário](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Utilize os botões de opção para selecionar a quais grupos devem ser incluídos no token

![afirmações da interface do Usuário](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Seleção | Descrição |
|----------|-------------|
| **Todos os grupos** | Emite a grupos de segurança e a distribuição de lista.   Ele também faz com que as funções de diretório é atribuída ao utilizador a ser emitida numa afirmação de "wids" e de quaisquer funções de aplicação que é atribuída ao utilizador a ser emitida na afirmação de funções. |
| **Grupos de segurança** | Emite a grupos de segurança, que o utilizador é membro na afirmação grupos |
| **Lista de distribuição** | Emite listas de distribuição, que o utilizador é membro de |
| **Função de diretório** | Se o utilizador tem atribuída a funções de diretório são emitidos como um wids afirmação (grupos de afirmação não será emitida) |

Por exemplo, para emitir todos os grupos de segurança que o utilizador é membro do, selecione os grupos de segurança

![afirmações da interface do Usuário](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

### <a name="advanced-options"></a>Opções avançadas

A forma como as declarações de grupo são emitidas pode ser modificado pelas definições na secção de opções avançadas

Personalize o nome da afirmação de grupo:  Se selecionado, um tipo de afirmação diferente pode ser especificado para declarações de grupo.   Introduza o tipo de afirmação no campo de nome e namespace opcional para a declaração no campo de espaço de nomes.

![afirmações da interface do Usuário](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Para emitir os grupos do Active Directory a utilizar atributos em vez do Azure AD objectIDs marque a caixa 'Return grupos como nomes, em vez de IDs de' em selecione o formato da lista pendente.  Esta ação substitui o ID de objeto nas afirmações com valores de cadeia de caracteres que contém nomes de grupo.   Apenas os grupos sincronizados a partir do Active Directory serão incluídos nas afirmações.

![afirmações da interface do Usuário](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Algumas aplicações necessitam das informações de associação de grupo apareça na afirmação "função". Opcionalmente, pode emitir grupos do utilizador como funções marcando a caixa "Emitir afirmações de uma função de grupos".

![afirmações da interface do Usuário](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Se for utilizada a opção para emitir dados de grupo como funções, grupos só serão apresentada na declaração de função.  Quaisquer funções de aplicação que é atribuída ao utilizador não aparecerão na declaração de função.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Configurar o registo de aplicação do AD do Azure para atributos de grupo

Afirmações de grupo também podem ser configuradas no [afirmações opcionais](../../active-directory/develop/active-directory-optional-claims.md) secção a [manifesto de aplicativo](../../active-directory/develop/reference-app-manifest.md).

 1. No portal -> Azure Active Directory -> aplicação registos -> selecione aplicação -> manifesto

 2. Ativar afirmações de associação de grupo, alterando o groupMembershipClaim

    Os valores válidos são:

    - "Tudo"
    - "SecurityGroup"
    - "DistributionList"
    - "DirectoryRole"

    Por exemplo:

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

    Por predefinição que ObjectIds grupo será emitido no grupo de valor de afirmação.  Para modificar o valor de afirmação para conter nos atributos de grupo local ou para alterar o tipo de afirmação a função, utilize a configuração de OptionalClaims da seguinte forma:

 3. Conjunto de afirmações opcionais de configuração do nome de grupo.

    Se quiser grupos no token para conter no local, atributos de grupo do AD na secção afirmações opcionais especificam qual afirmação opcional do tipo de token deve ser aplicada, o nome da afirmação opcional solicitada e as propriedades adicionais assim o desejar.  Vários tipos de tokens podem ser listados:

    - idToken para o token de ID de OIDC
    - accessToken para o token de acesso de OAuth/OIDC
    - Saml2Token para SAML tokens.

    > [!NOTE]
    > O tipo de Saml2Token aplica-se a SAML1.1 e SAML2.0 tokens de formato

    Para cada tipo de token relevante, modifique a declaração de grupos para utilizar a secção de OptionalClaims no manifesto. O esquema de OptionalClaims é o seguinte:

 ```json
 {
    "name": "groups",
    "source": null,
    "essential": false,
    "additionalProperties": []
 }
 ```

 | Esquema de afirmações opcionais | Value |
 |----------|-------------|
 | **name:** | Tem de ser "grupos" |
 | **Origem:** | Não utilizado. Omitir ou especifique null |
 | **essential:** | Não utilizado. Omitir ou especifique false |
 | **additionalProperties:** | Lista de propriedades adicionais.  As opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

 No additionalProperties apenas um dos "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" são necessários.  Se existir mais do que um, o primeiro é utilizado e quaisquer outros ignorados.

 Algumas aplicações necessitam de informações do grupo sobre o utilizador na declaração de função.  Para alterar o tipo de afirmação a partir de um grupo de afirmação para uma declaração role, adicione "emit_as_roles" às propriedades adicionais.  Os valores de grupo serão emitidos na declaração de função.

 > [!NOTE]
 > Se for utilizado "emit_as_roles" quaisquer funções de aplicação configurado que é atribuída ao utilizador irá não aparecem na declaração de função

### <a name="examples"></a>Exemplos

Emitir grupos como nomes de grupo nos tokens de acesso de OAuth no formato de dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Emitir nomes de grupo seja retornado em formato de netbiosDomain\samAccountName como as funções de afirmações no SAML e Tokens de ID de OIDC:

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

## <a name="next-steps"></a>Passos Seguintes

[O que é a identidade híbrida?](whatis-hybrid-identity.md)