---
title: Configure pedidos de pedidos de candidaturas com a Azure Ative Directory | Microsoft Docs
description: Informações sobre como configurar pedidos de grupo para uso com Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: bef5942707c1ded22ba82bdb0d945b9fdb23fffa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349355"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>Configure pedidos de pedidos de candidaturas com a Azure Ative Directory

O Azure Ative Directory pode fornecer informações de membros do grupo de utilizadores em fichas para utilização dentro das aplicações.  Dois padrões principais são suportados:

- Grupos identificados pelo seu atributo de identificador de objetos Azure Ative (OID)
- Grupos identificados por sAMAccountName ou atributos GroupSID para grupos sincronizados de Diretório Ativo (AD) e utilizadores

> [!IMPORTANT]
> Há uma série de ressalvas a notar para esta funcionalidade:
>
>- O suporte à utilização de atributos sAMAccountName e identificador de segurança (SID) sincronizados a partir do local foi concebido para permitir a deslocação de aplicações existentes de FS AD e outros fornecedores de identidade. Os grupos geridos em Azure AD não contêm os atributos necessários para emitir estas alegações.
>- Em organizações maiores, o número de grupos de que um utilizador é membro pode exceder o limite que o Azure Ative Directory irá adicionar a um token. 150 grupos para um símbolo SAML, e 200 para um JWT. Isto pode levar a resultados imprevisíveis. Se os seus utilizadores tiverem um grande número de membros do grupo, recomendamos usar a opção de restringir os grupos emitidos em reclamações aos grupos relevantes para a aplicação.  
>- Para o desenvolvimento de novas aplicações, ou nos casos em que a aplicação pode ser configurada para a aplicação, e onde não é necessário o suporte de grupo aninhado, recomendamos que a autorização na app se baseie em funções de aplicação e não em grupos.  Isto limita a quantidade de informação que precisa de ir para o token, é mais segura, e separa a atribuição do utilizador da configuração da aplicação.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Pedidos de pedidos de pedidos migratórios de FS AD e outros fornecedores de identidade

Muitas aplicações configuradas para autenticar com FS AD dependem de informações de membros do grupo sob a forma de atributos do grupo AD do Windows.   Estes atributos são o nome sAMAccountName do grupo, que pode ser qualificado pelo nome de domínio, ou o Identificador de Segurança do Grupo Windows (GroupSID).  Quando a aplicação é federada com FS AD, a AD FS utiliza a função TokenGroups para recuperar os membros do grupo para o utilizador.

Uma aplicação que foi transferida da AD FS precisa de reclamações no mesmo formato. As alegações de grupo e de função podem ser emitidas a partir do Azure Ative Directory contendo o domínio qualificado sAMAccountName ou o GroupSID sincronizado a partir do Ative Directory em vez do objectID do Azure Ative Directory do grupo.

Os formatos suportados para reclamações em grupo são:

- **Azure Ative Directory Group ObjectId** (Disponível para todos os grupos)
- **sAMAccountName** (Disponível para grupos sincronizados a partir do Diretório Ativo)
- **NetbiosDomain\sAMAccountName** (Disponível para grupos sincronizados a partir do Diretório Ativo)
- **DNSDomainName\sAMAccountName** (Disponível para grupos sincronizados a partir do Diretório Ativo)
- **No Identificador de Segurança do Grupo de Instalações** (Disponível para grupos sincronizados a partir do Diretório Ativo)

> [!NOTE]
> sAMAccountName e As premissas Os atributos SID do Grupo SID só estão disponíveis em objetos do Grupo sincronizados a partir do Ative Directory.   Não estão disponíveis em grupos criados no Azure Ative Directory ou no Office365.   As aplicações configuradas no Azure Ative Directory para obter atributos de grupo sincronizados no local obtêm-nas apenas para grupos sincronizados.

## <a name="options-for-applications-to-consume-group-information"></a>Opções para aplicações para consumir informações de grupo

As aplicações podem ligar para o ponto final dos grupos MS Graph para obter informações de grupo para o utilizador autenticado. Esta chamada garante que todos os grupos de que um utilizador é membro estão disponíveis mesmo quando há um grande número de grupos envolvidos.  A enumeração em grupo é então independente das limitações de tamanho simbólico.

No entanto, se uma aplicação existente espera consumir informações de grupo através de sinistros, o Azure Ative Directory pode ser configurado com uma série de diferentes formatos de reclamações.  Considere as seguintes opções:

- Ao utilizar a filiação do grupo para efeitos de autorização de inscrição, é preferível utilizar o Grupo ObjectID. O Grupo ObjectID é imutável e único no Azure Ative Directory e está disponível para todos os grupos.
- Se utilizar o grupo no local sAMAccountName para autorização, utilize nomes qualificados de domínio;  há menos hipóteses de os nomes colidirem. sAMAccountName pode ser único dentro de um domínio de Diretório Ativo, mas se mais de um domínio de Diretório Ativo for sincronizado com um inquilino do Azure Ative Directory existe a possibilidade de mais de um grupo ter o mesmo nome.
- Considere usar [as Funções de Aplicação](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) para fornecer uma camada de indireção entre a filiação do grupo e a aplicação.   O pedido toma então decisões de autorização interna com base em reivindicações de papel no token.
- Se a aplicação estiver configurada para obter atributos de grupo sincronizados com o Ative Directory e um Grupo não contiver esses atributos, não será incluído nas alegações.
- As reclamações do grupo em tokens incluem grupos aninhados, exceto quando se utiliza a opção de restringir as reclamações do grupo aos grupos atribuídos à aplicação.  Se um utilizador for membro do GrupoB e o GrupoB for membro do GrupoA, então as alegações do grupo para o utilizador conterão tanto o GrupoA como o GrupoB. Quando os utilizadores de uma organização têm um grande número de membros do grupo, o número de grupos listados no símbolo pode aumentar o tamanho do símbolo.  O Azure Ative Directory limita o número de grupos que emitirá num token a 150 para afirmações SAML e 200 para o JWT.  Se um utilizador for membro de um maior número de grupos, os grupos são omitidos e um link para o ponto final do Gráfico para obter informações de grupo é incluído.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Pré-requisitos para a utilização de atributos do Grupo sincronizados a partir do Ative Directory

Se utilizar o formato ObjectId, poderá emitir as afirmações de associações de grupos em tokens para qualquer grupo. Para utilizar as reclamações de grupo em outros formatos que não o grupo ObjectId, os grupos devem ser sincronizados a partir do Ative Directory utilizando o Azure AD Connect.

Existem dois passos para configurar o Azure Ative Directory para emitir nomes de grupo para grupos de diretórios ativos.

1. **Sincronizar nomes de grupo do Ative Directory** Antes de o Azure Ative Directory poder emitir os nomes do grupo ou no grupo de instalações SID em reivindicações de grupo ou de função, os atributos necessários precisam de ser sincronizados a partir do Ative Directory.  Deve estar a executar a versão 1.2.70 ou posterior do Azure AD Connect.   Versões anteriores do Azure AD Connect do que 1.2.70 sincronizarão os objetos de grupo do Ative Directory, mas não incluirão os atributos de nome de grupo necessários.  Upgrade para a versão atual.

2. **Configure o registo de pedidos no Azure Ative Directy para incluir reclamações de grupo em fichas** As reclamações do grupo podem ser configuradas na secção aplicações empresariais do portal, ou utilizando o Manifesto de Aplicação na secção Registos de Candidaturas.  Para configurar as reclamações do grupo no manifesto de aplicação ver "Configurar o Registo de Pedido de Diretório Ativo Azure para atributos de grupo" abaixo.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Adicionar pedidos de grupo a fichas para aplicações SAML usando a configuração SSO

Para configurar reclamações de grupo para uma aplicação SAML de galeria ou não galeria, abra **as aplicações da Empresa,** clique na aplicação na lista, selecione **Single Sign On configuration**, e, em seguida, selecione **Atributos de Utilizador & Reclamações**.

Clique em **Adicionar uma reivindicação de grupo**  

![Screenshot que mostra a página "User Atributos & Claims" com "Adicionar uma reivindicação de grupo" selecionada.](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Utilize os botões de rádio para selecionar quais os grupos que devem ser incluídos no token

![Screenshot que mostra a janela "Group Claims" com "Grupos de segurança" selecionados.](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Seleção | Descrição |
|----------|-------------|
| **Todos os grupos** | Emite grupos de segurança e listas de distribuição e funções.  |
| **Grupos de segurança** | Emite grupos de segurança do utilizador é um membro dos grupos alegam |
| **Funções de diretório** | Se o utilizador tiver funções de diretório atribuídas, são emitidas como uma alegação de 'wids' (os grupos alegam que não serão emitidos) |
| **Grupos atribuídos à aplicação** | Emite apenas os grupos que são explicitamente atribuídos à aplicação e o utilizador é membro da |

Por exemplo, para emitir todos os Grupos de Segurança o utilizador é membro de, selecione Grupos de Segurança

![Screenshot que mostra a janela "Group Claims" com "Grupos de segurança" selecionados e o menu suspenso "Source attribute" aberto.](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Para emitir grupos que utilizem atributos ative directory sincronizados a partir do Ative Directory em vez de Azure AD objectIDs selecione o formato necessário a partir do drop-down. Apenas grupos sincronizados do Ative Directory serão incluídos nas reclamações.

![Screenshot que mostra o menu suspenso "Source attribute" aberto.](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Para emitir apenas grupos atribuídos à aplicação, selecione **Grupos Atribuídos à aplicação**

![Screenshot que mostra a janela "Group Claims" com "Grupos atribuídos à aplicação" selecionados.](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Os grupos designados para a aplicação serão incluídos no token.  Outros grupos de que o utilizador é membro serão omitidos.  Com esta opção não estão incluídos grupos aninhados e o utilizador deve ser um membro direto do grupo designado para a aplicação.

Para alterar os grupos atribuídos à aplicação, selecione a aplicação da lista **de Aplicações Empresariais** e, em seguida, clique em **Utilizadores e Grupos** a partir do menu de navegação à esquerda da aplicação.

Consulte o documento [Atribua um utilizador ou grupo a uma aplicação empresarial](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) para obter detalhes sobre a gestão da atribuição de grupos às aplicações.

### <a name="advanced-options"></a>Opções avançadas

A forma como as alegações de grupo são emitidas pode ser modificada pelas definições em opções avançadas

Personalize o nome da reclamação do grupo: Se selecionado, um tipo de reclamação diferente pode ser especificado para reclamações de grupo.   Introduza o tipo de reclamação no campo Nome e o espaço de nome opcional para a reclamação no campo do espaço de nome.

![Screenshot que mostra a secção "Opções Avançadas" com "Personalizar o nome da reivindicação do grupo" selecionado e "Nome" e "Namespace" introduzidos.](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Algumas aplicações exigem que as informações de adesão ao grupo apareçam na alegação de "papel". Pode, opcionalmente, emitir os grupos do utilizador como funções, verificando a caixa 'Emit groups a role claims'.

![Screenshot que mostra a secção "Opções Avançadas" com "Personalizar o nome da reivindicação do grupo" e "Emit groups as role claims" selecionadas.](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Se a opção de emitir dados de grupo como funções for utilizada, apenas grupos aparecerão na reivindicação de funções.  Quaisquer Funções de Aplicação que o utilizador seja atribuído não aparecerão na reivindicação de funções.

### <a name="edit-the-group-claims-configuration"></a>Editar a configuração de reclamações do grupo

Uma vez adicionada uma configuração de reclamação de grupo à configuração "Atributos & Reclamações do Utilizador", a opção de adicionar uma alegação de grupo será acinzentado.  Para alterar a configuração de reclamação de grupo clique na reclamação de grupo na lista **de reclamações adicionais.**

![reclama uI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Configurar o Registo de Pedidos AD Azure para atributos de grupo

As reclamações do grupo também podem ser configuradas na secção [de Reclamações Opcionais](../../active-directory/develop/active-directory-optional-claims.md) do [Manifesto de Aplicação.](../../active-directory/develop/reference-app-manifest.md)

1. No portal ->Azure Ative Directory -> Registos de candidatura->Manifesto Select Application->

2. Permitir pedidos de adesão ao grupo alterando o grupoMembershipClaim

Os valores válidos são:

| Seleção | Descrição |
|----------|-------------|
| **"Todos"** | Emite grupos de segurança, listas de distribuição e funções |
| **"SecurityGroup"** | Emite grupos de segurança do utilizador é um membro dos grupos alegam |
| **"DirectoryRole"** | Se o utilizador tiver funções de diretório atribuídas, são emitidas como uma alegação de 'wids' (os grupos alegam que não serão emitidos) |
| **"ApplicationGroup"** | Emite apenas os grupos que são explicitamente atribuídos à aplicação e o utilizador é membro da |
| **"Nenhum"** | Nenhum grupo é devolvido. (Não é desíotoro caso-sentido para que nenhum funcione tão bem e possa ser definido diretamente no manifesto de aplicação.) |

   Por exemplo:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Por predefinição, os ObjectIDs do grupo serão emitidos no valor de reclamação do grupo.  Para modificar o valor de reclamação para conter nos atributos do grupo de instalações, ou para alterar o tipo de reclamação para a função, utilize a configuração OptionalClaims da seguinte forma:

3. Definir pedidos opcionais de configuração do nome de grupo.

   Se pretender que os grupos no token contenham os atributos do grupo AD nas instalações, especifique a que sinal de token pedido opcional deve ser aplicado na secção de reclamações opcionais.  Vários tipos de token podem ser listados:

   - idToken para o token OIDC ID
   - accessToken para o token de acesso OAuth/OIDC
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

   | Esquema de Reclamações Opcionais | Valor |
   |----------|-------------|
   | **nome:** | Devem ser "grupos" |
   | **Fonte:** | Não usado. Omitir ou especificar nulo |
   | **essencial:** | Não usado. Omitir ou especificar falso |
   | **Adicionalidades:** | Lista de propriedades adicionais.  As opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   Em outros Bens, é necessário apenas um dos "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Se mais de um está presente, o primeiro é usado e quaisquer outros ignorados.

   Algumas aplicações requerem informações de grupo sobre o utilizador na reivindicação de funções.  Para alterar o tipo de reclamação para de uma reivindicação de grupo para uma reivindicação de papel, adicione "emit_as_roles" a propriedades adicionais.  Os valores do grupo serão emitidos na reivindicação do papel.

   > [!NOTE]
   > Se for utilizada "emit_as_roles" quaisquer Funções de Aplicação configuradas que o utilizador seja atribuído não aparecerão na alegação de função

### <a name="examples"></a>Exemplos

Emit grupos como nomes de grupo em tokens de acesso OAuth em dnsDomainName\SAMAccountName formato

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Para emitir nomes de grupo a serem devolvidos no formato netbiosDomain\samAccountName como as funções reivindicam em tokens de ID SAML e OIDC:

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

- [Adicionar autorização usando grupos & grupos reclamam a uma aplicação web core ASP.NET (amostra de código)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md)
- [Atribuir um utilizador ou grupo a uma aplicação empresarial](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)
- [Configurar afirmações de função](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
