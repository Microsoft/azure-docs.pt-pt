---
title: Configure reivindicações do grupo para candidaturas com Diretório Ativo Azure [ Microsoft Docs
description: Informações sobre como configurar pedidos de grupo para uso com AD Azure.
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
ms.openlocfilehash: 6a89c5e3fb84f797d9ad7f81626fb7185ce3e076
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854134"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>Configure reivindicações de grupo para candidaturas com Diretório Ativo Azure

O Azure Ative Directory pode fornecer informações de membros do grupo de utilizadores em tokens para utilização dentro das aplicações.  São suportados dois padrões principais:

- Grupos identificados pelo seu identificador de objetos de diretório Ativo Azure (OID)
- Grupos identificados por sAMAccountName ou atributos GroupSID para grupos e utilizadores sincronizados de Diretório Ativo (AD)

> [!IMPORTANT]
> Há uma série de ressalvas a notar para esta funcionalidade:
>
>- O suporte à utilização de atributos sAMAccountName e identificador de segurança (SID) sincronizados a partir de instalações foi concebido para permitir a deslocação de aplicações existentes a partir de AD FS e outros fornecedores de identidade. Os grupos geridos em Azure AD não contêm os atributos necessários para emitir estas alegações.
>- Em organizações maiores o número de grupos de que um utilizador é membro pode exceder o limite que o Azure Ative Directory irá adicionar a um símbolo. 150 grupos para um símbolo SAML, e 200 para um JWT. Isto pode levar a resultados imprevisíveis. Se os seus utilizadores tiverem um grande número de membros do grupo, recomendamos a utilização da opção de restringir os grupos emitidos em reclamações aos grupos relevantes para a aplicação.  
>- Para o desenvolvimento de novas aplicações, ou nos casos em que a aplicação possa ser configurada para a sua configuração, e quando não for necessário o suporte do grupo aninhado, recomendamos que a autorização na aplicação seja baseada em funções de aplicação e não em grupos.  Isto limita a quantidade de informação que precisa de entrar no símbolo, é mais segura e separa a atribuição do utilizador da configuração da aplicação.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Pedidos de grupo para pedidos migradores de AD FS e outros fornecedores de identidade

Muitas aplicações configuradas para autenticar com AD FS dependem de informações de membros do grupo sob a forma de atributos do grupo AD windows.   Estes atributos são o grupo sAMAccountName, que pode ser qualificado nome de domínio, ou o Identificador de Segurança do Grupo Windows (GroupSID).  Quando a aplicação é federada com AD FS, a AD FS utiliza a função TokenGroups para recuperar os membros do grupo para o utilizador.

Uma aplicação que foi transferida da AD FS precisa de reclamações no mesmo formato. As alegações de grupo e de funções podem ser emitidas do Azure Ative Directory contendo o domínio qualificado sAMAccountName ou o GroupSID sincronizado do Ative Directory em vez do objectid do Diretório Ativo Azure do grupo.

Os formatos suportados para reivindicações de grupo são:

- Grupo de **Diretório Ativo Azure ObjectId** (Disponível para todos os grupos)
- **sAMAccountName** (Disponível para grupos sincronizados a partir de Diretório Ativo)
- **NetbiosDomain\sAMAccountName** (Disponível para grupos sincronizados a partir de Diretório Ativo)
- **DNSDomainName\sAMAccountName** (Disponível para grupos sincronizados a partir de Diretório Ativo)
- **No Identificador** de Segurança do Grupo De Instalações (Disponível para grupos sincronizados de Diretório Ativo)

> [!NOTE]
> Os atributos sid do SAMAccountName e on No Local group sid só estão disponíveis em objetos do Grupo sincronizados a partir do Diretório Ativo.   Não estão disponíveis em grupos criados no Azure Ative Directory ou office365.   As aplicações configuradas no Diretório Ativo Azure para obter atributos de grupo sincronizados no local recebem-nas apenas para grupos sincronizados.

## <a name="options-for-applications-to-consume-group-information"></a>Opções para aplicações para consumir informação de grupo

As aplicações podem chamar o ponto final dos grupos MS Graph para obter informações de grupo para o utilizador autenticado. Esta chamada garante que todos os grupos de que um utilizador é membro estão disponíveis mesmo quando há um grande número de grupos envolvidos.  A enumeração do grupo é então independente das limitações do tamanho do token.

No entanto, se uma aplicação existente espera consumir informações de grupo através de sinistros, o Azure Ative Directory pode ser configurado com vários formatos de sinistros diferentes.  Considere as seguintes opções:

- Ao utilizar a adesão ao grupo para efeitos de autorização de pedido, é preferível utilizar o Group ObjectID. O Group ObjectID é imutável e único no Diretório Ativo Azure e está disponível para todos os grupos.
- Se utilizar o grupo no local sAMAccountName para autorização, utilize nomes qualificados de domínio;  há menos hipóteses de os nomes entrarem em confronto. O sAMAccountName pode ser único dentro de um domínio de Diretório Ativo, mas se mais de um domínio de Diretório Ativo for sincronizado com um inquilino do Diretório Ativo Azure, existe a possibilidade de mais de um grupo ter o mesmo nome.
- Considere utilizar funções de [aplicação](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) para fornecer uma camada de inorientação entre a adesão ao grupo e a aplicação.   O pedido toma então decisões de autorização interna com base em amêijoas de funções no símbolo.
- Se a aplicação estiver configurada para obter atributos de grupo sincronizados do Ative Directory e um Grupo não contiver esses atributos, não será incluído nas reclamações.
- As alegações do grupo em fichas incluem grupos aninhados, exceto quando utilizam a opção de restringir as reivindicações do grupo aos grupos afetados ao pedido.  Se um utilizador for membro do GrupoB e o GroupB for membro do GroupA, então as alegações do grupo para o utilizador conterão tanto o GrupoA como o GroupB. Quando os utilizadores de uma organização têm um grande número de membros do grupo, o número de grupos listados no token pode aumentar o tamanho do símbolo.  O Azure Ative Directory limita o número de grupos que emitirá num token a 150 para afirmações da SAML e 200 para a JWT.  Se um utilizador for membro de um maior número de grupos, os grupos são omitidos e uma ligação ao ponto final do Gráfico para obter informações de grupo é incluída.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Pré-requisitos para a utilização de atributos do Grupo sincronizados a partir do Diretório Ativo

As reclamações de membros do grupo podem ser emitidas em fichas para qualquer grupo se utilizar o formato ObjectId. Para utilizar as reivindicações de grupo em outros formatos que não o grupo ObjectId, os grupos devem ser sincronizados a partir do Ative Directory utilizando o Azure AD Connect.

Existem dois passos para configurar o Diretório Ativo do Azure para emitir nomes de grupo para Grupos De Diretório Ativo.

1. **Sincronizar nomes de grupo sincronia do Ative Directory** Antes que o Azure Ative Directory possa emitir os nomes do grupo ou nas instalações do grupo SID em reivindicações de grupo ou de funções, os atributos necessários precisam de ser sincronizados a partir do Ative Directory.  Deve estar a executar a versão 1.2.70 do Azure AD Connect ou mais tarde.   As versões anteriores do Azure AD Connect do que 1.2.70 sincronizarão os objetos do grupo a partir do Ative Directory, mas não incluirão os atributos de nome de grupo necessários.  Atualize para a versão atual.

2. **Configure o registo de candidatura no Diretório Ativo azure para incluir reclamações de grupo em fichas** As reivindicações do grupo podem ser configuradas na secção aplicações empresariais do portal, ou utilizando o Manifesto de Aplicação na secção registos de aplicações.  Para configurar as reclamações do grupo no manifesto de aplicação, consulte "Configurar o Registo de Aplicação de Diretório Ativo Azure para atributos de grupo" abaixo.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Adicione alegações de grupo para aplicações SAML utilizando a configuração SSO

Para configurar as Reclamações de Grupo para uma aplicação SAML de galeria ou não-galeria, abra **as Aplicações Empresariais,** clique na aplicação na lista, selecione **a configuração Single Sign On**, e, em seguida, selecione **Atributos de utilizador & Reivindicações**.

Clique em **Adicionar uma reivindicação de grupo**  

![reivindica uI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Utilize os botões de rádio para selecionar quais os grupos que devem ser incluídos no símbolo

![reivindica uI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Seleção | Descrição |
|----------|-------------|
| **Todos os grupos** | Emite grupos de segurança e listas de distribuição e funções.  |
| **Grupos de segurança** | Emite grupos de segurança que o utilizador é membro dos grupos alegam |
| **Funções de diretório** | Se o utilizador for atribuído funções de diretório, são emitidas como uma alegação de 'wids' (os grupos alegam que não serão emitidos) |
| **Grupos atribuídos ao pedido** | Emite apenas os grupos que são explicitamente atribuídos à aplicação e o utilizador é membro da |

Por exemplo, para emitir todos os Grupos de Segurança o utilizador é membro de, selecione Grupos de Segurança

![reivindica uI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Para emitir grupos utilizando atributos Ative Directory sincronizados a partir de Ative Directory em vez de objetos De AD Azure selecionam o formato necessário a partir da queda. Apenas grupos sincronizados do Ative Directory serão incluídos nas reclamações.

![reivindica uI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Para emitir apenas grupos atribuídos ao pedido, selecione **Grupos Atribuídos ao pedido**

![reivindica uI](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Os grupos atribuídos ao pedido serão incluídos no símbolo.  Outros grupos de que o utilizador é membro será omitido.  Com esta opção, os grupos aninhados não estão incluídos e o utilizador deve ser um membro direto do grupo atribuído à aplicação.

Para alterar os grupos atribuídos à aplicação, selecione a aplicação a partir da lista de **Aplicações empresariais** e, em seguida, clique em **Utilizadores e Grupos** a partir do menu de navegação à esquerda da aplicação.

Consulte o documento [Atribuir um utilizador ou grupo a uma aplicação empresarial](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) para obter detalhes sobre a gestão da atribuição do grupo às aplicações.

### <a name="advanced-options"></a>Opções avançadas

A forma como as reivindicações do grupo são emitidas pode ser modificada pelas definições em opções Avançadas

Personalize o nome da reivindicação do grupo: Se selecionado, um tipo de reclamação diferente pode ser especificado para reclamações de grupo.   Introduza o tipo de reclamação no campo Nome e o espaço de nome opcional para a reclamação no campo namespace.

![reivindica uI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Algumas aplicações exigem que as informações sobre a adesão ao grupo apareçam na alegação de "papel". Pode emitir opcionalmente os grupos do utilizador como funções, verificando a caixa "Emit groups a role claims".

![reivindica uI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Se for utilizada a opção de emitir dados de grupo como funções, apenas os grupos aparecerão na alegação de funções.  Quaisquer funções de aplicação atribuídas pelo utilizador não constam da alegação de funções.

### <a name="edit-the-group-claims-configuration"></a>Editar a configuração de reivindicações do grupo

Uma vez adicionada a configuração de reclamação de grupo à configuração de Atributos de Utilizador & Reivindicações, a opção de adicionar uma reclamação de grupo será acinzentada.  Para alterar a configuração da reclamação do grupo clique na reclamação do grupo na lista de **reclamações adicionais.**

![reivindica uI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Configure o Registo de Aplicação AD Azure para atributos de grupo

As reivindicações do grupo também podem ser configuradas na secção [de Reclamações Opcionais](../../active-directory/develop/active-directory-optional-claims.md) do Manifesto de [Aplicação](../../active-directory/develop/reference-app-manifest.md).

1. No portal ->Diretório Ativo Azure -> Registos de aplicações->Selecione >>Manifesto

2. Ativar reclamações de membros do grupo alterando o grupoMembershipClaim

Os valores válidos são:

| Seleção | Descrição |
|----------|-------------|
| **"Todos"** | Emite grupos de segurança, listas de distribuição e funções |
| **"Grupo de Segurança"** | Emite grupos de segurança que o utilizador é membro dos grupos alegam |
| **"Roledeta de Diretório** | Se o utilizador for atribuído funções de diretório, são emitidas como uma alegação de 'wids' (os grupos alegam que não serão emitidos) |
| **"ApplicationGroup** | Emite apenas os grupos que são explicitamente atribuídos à aplicação e o utilizador é membro da |

   Por exemplo:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Por padrão, os Objetos de Grupo serão emitidos no valor de reclamação do grupo.  Para modificar o valor de reclamação para conter nos atributos do grupo de instalações, ou para alterar o tipo de reclamação para a função, utilize a configuração OptionalClaims da seguinte forma:

3. Definir as alegações opcionais de configuração do nome do grupo.

   Se pretender que os grupos do token contenham os atributos do grupo AD no local, especifique a que tipo simbólico deve ser aplicado na secção de reclamações opcionais.  Vários tipos de fichas podem ser listados:

   - idToken para o token id OIDC
   - acessoToken para o token de acesso OAuth/OIDC
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

   | Reclamações Opcionais Schema | Valor |
   |----------|-------------|
   | **nome:** | Devem ser "grupos" |
   | **fonte:** | Não usado. Omite ou especifique nulo |
   | **essencial:** | Não usado. Omite ou especifique falso |
   | **propriedades adicionais:** | Lista de propriedades adicionais.  Opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   Adicionalmente são necessários apenas um dos "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Se mais do que um estiver presente, o primeiro é usado e qualquer outro ignorado.

   Algumas aplicações requerem informações de grupo sobre o utilizador na reivindicação de funções.  Para alterar o tipo de reclamação para de um grupo reivindicar para uma reivindicação de papel, adicione "emit_as_roles" a propriedades adicionais.  Os valores do grupo serão emitidos na reivindicação do papel.

   > [!NOTE]
   > Se "emit_as_roles" for utilizada, quaisquer Funções de Aplicação configuradas por o utilizador ser atribuído não aparecerão na alegação de funções

### <a name="examples"></a>Exemplos

Emitem grupos como nomes de grupo em tokens de acesso OAuth no formato dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Para emitir nomes de grupo a devolver no formato netbiosDomain\samAccountName como as funções reivindicam em Tokens de ID SAML e OIDC:

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

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

[Configurar afirmações de função](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
