---
title: As contas da plataforma de identidade da Microsoft & perfis de inquilinos no Android / Azure
description: Uma visão geral das contas da plataforma de identidade da Microsoft para Android
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: d0497ad68e7b29e6d8c83dd860ba8f509e229579
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77611879"
---
# <a name="accounts--tenant-profiles-android"></a>Contas e perfis de inquilino (Android)

Este artigo fornece uma visão `account` geral do que é um na plataforma de identidade da Microsoft.

A Microsoft Authentication Library (MSAL) API substitui o *utilizador* termo pela *conta*de termo . Uma das razões é que um utilizador (agente humano ou de software) pode ter, ou pode usar, várias contas. Estas contas podem estar na própria organização do utilizador e/ou noutras organizações de que o utilizador é membro.

Uma conta na plataforma de identidade da Microsoft consiste em:

- Um identificador único.  
- Uma ou mais credenciais usadas para demonstrar a propriedade/controlo da conta.
- Um ou mais perfis constituídos por atributos como:
  - Imagem, nome dado, nome da família, título, localização do escritório
- Uma conta tem uma fonte de autoridade ou sistema de registo. Este é o sistema onde a conta é criada e onde as credenciais associadas a essa conta são armazenadas. Em sistemas multi-inquilinos como a plataforma de `tenant` identidade da Microsoft, o sistema de registo é o local onde a conta foi criada. Este inquilino também é `home tenant`referido como o .
- As contas na plataforma de identidade da Microsoft têm os seguintes sistemas de registo:
  - Diretório Azure Ative, incluindo o Diretório Ativo Azure B2C.
  - Conta Microsoft (Live).
- As contas de sistemas de registo fora da plataforma de identidade da Microsoft estão representadas dentro da plataforma de identidade da Microsoft, incluindo:
  - identidades de diretórios ligados no local (Diretório Ativo do Servidor do Windows)
  - identidades externas do LinkedIn, GitHub, e assim por diante.
  Nestes casos, uma conta tem um sistema de origem de registo e um sistema de registo dentro da plataforma de identidade da Microsoft.
- A plataforma de identidade da Microsoft permite que uma conta seja usada para aceder a recursos pertencentes a várias organizações (inquilinos do Azure Ative Directory).
  - Para registar que uma conta de um sistema de registo (AAD Tenant A) tem acesso a um recurso noutro sistema de registo (AAD Tenant B), a conta deve estar representada no arrendatário onde o recurso é definido. Isto é feito através da criação de um registo local da conta a partir do sistema A no sistema B.
  - Este registo local, que é a representação da conta, está vinculado à conta original.
  - A MSAL expõe este `Tenant Profile`registo local como um .
  - O Perfil do Inquilino pode ter diferentes atributos adequados ao contexto local, tais como Título de Emprego, Localização do Escritório, Informações de Contacto, etc.
- Como uma conta pode estar presente em um ou mais inquilinos, uma conta pode ter mais do que um perfil.

> [!NOTE]
> A MSAL trata o sistema de conta da Microsoft (Live, MSA) como outro inquilino dentro da plataforma de identidade da Microsoft. A identificação do inquilino do inquilino da conta da Microsoft é:`9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Diagrama de visão geral da conta

![Diagrama de visão geral da conta](./media/accounts-overview/accounts-overview.svg)

No diagrama acima:

- A `bob@contoso.com` conta é criada no Diretório Ativo do Windows Server (sistema de registo de origem no local).
- A `tom@live.com` conta é criada no inquilino da conta da Microsoft.
- `bob@contoso.com`tem acesso a pelo menos um recurso nos seguintes inquilinos do Diretório Ativo Azure:
  - contoso.com (sistema de registo em nuvem - ligado ao sistema de registo no local)
  - fabrikam.com
  - woodgrovebank.com
  - Um perfil `bob@contoso.com` de inquilino existe em cada um destes inquilinos.
- `tom@live.com`tem acesso a recursos nos seguintes inquilinos da Microsoft:
  - contoso.com
  - fabrikam.com
  - Um perfil `tom@live.com` de inquilino existe em cada um destes inquilinos.
- Informações sobre Tom e Bob em outros inquilinos podem diferir disso no sistema de registo. Podem diferir por atributos como título de trabalho, Localização do Escritório, e assim por diante. Podem ser membros de grupos e/ou funções dentro de cada organização (Azure Ative Directory Tenant). Referimo-nos a bob@contoso.com esta informação como perfil de inquilino.

No bob@contoso.com diagrama, tom@live.com e tem acesso a recursos em diferentes inquilinos do Azure Ative Directory. Para mais informações, consulte os utilizadores de colaboração Add [Azure Ative Directory B2B no portal Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

## <a name="accounts-and-single-sign-on-sso"></a>Contas e inscrição única (SSO)

O cache de token MSAL armazena um *único token de atualização* por conta. Este token de atualização pode ser usado para solicitar silenciosamente fichas de acesso de vários inquilinos da plataforma de identidade da Microsoft. Quando um corretor é instalado num dispositivo, a conta é gerida pelo corretor e o único sinal de inscrição em todo o dispositivo torna-se possível.

> [!IMPORTANT]
> A conta Business to Consumer (B2C) e o comportamento de token de atualização diferem do resto da plataforma de identidade da Microsoft. Para mais informações, consulte [as Políticas B2C & Contas](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Identificadores de conta

O ID da conta MSAL não é um id de objeto de conta. Não se destina a ser analisado e/ou invocado para transmitir algo que não seja a singularidade dentro da plataforma de identidade da Microsoft.

Para compatibilidade com a Biblioteca de Autenticação AD Azure (ADAL), e para facilitar a migração da ADAL para a MSAL, a MSAL pode pesquisar contas utilizando qualquer identificador válido para a conta disponível na cache MSAL.  Por exemplo, o seguinte irá sempre tom@live.com recuperar o mesmo objeto de conta porque cada um dos identificadores é válido:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Acesso a reclamações sobre uma conta

Além de solicitar um sinal de acesso, a MSAL também pede sempre um sinal de identificação de cada inquilino. Fá-lo solicitando sempre os seguintes âmbitos:

- openid
- perfil

O símbolo de identificação contém uma lista de reclamações. `Claims`são pares de nome/valor sobre a conta, e são usados para fazer o pedido.

Como mencionado anteriormente, cada inquilino onde existe uma conta pode armazenar informações diferentes sobre a conta, incluindo, mas não se limitando a atributos como: título de emprego, localização do escritório, e assim por diante.

Embora uma conta possa ser um membro ou hóspede em várias organizações, a MSAL não consulta um serviço para obter uma lista dos inquilinos da conta que é membro. Em vez disso, a MSAL constrói uma lista de inquilinos em que a conta está presente, em resultado de pedidos simbólicos que foram feitos.

As reclamações expostas no objeto da conta são sempre as reclamações do 'inquilino da casa'/autoridade{} para uma conta. Se essa conta não foi usada para solicitar um sinal para o seu inquilino, a MSAL não pode fornecer reclamações através do objeto da conta.  Por exemplo:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Para ver uma lista de reclamações disponíveis no objeto da conta, consulte [as reclamações num id_token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> Para incluir reclamações adicionais no seu id_token, consulte a documentação opcional de reclamações em [Como: Fornecer reclamações opcionais à sua aplicação Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)

### <a name="access-tenant-profile-claims"></a>Aceder a reclamações de perfil de inquilino

Para aceder a reclamações sobre uma conta tal como aparecem noutros `IMultiTenantAccount`inquilinos, primeiro é necessário lançar o seu objeto de conta a . Todas as contas podem ser multi-inquilinos, mas o número de perfis de inquilinos disponíveis via MSAL baseia-se nos inquilinos que solicitou fichas de utilização da conta corrente.  Por exemplo:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>As políticas b2C & contas

Os tokens de atualização para uma conta não são partilhados nas políticas b2C. Como resultado, um único sinal usando fichas não é possível. Isto não significa que a única inscrição não seja possível. Significa que o único sinal tem de utilizar uma experiência interativa na qual um cookie está disponível para permitir uma única inscrição.

Isto significa também que, no caso da MSAL, se adquirir fichas utilizando diferentes políticas B2C, estas são tratadas como contas separadas - cada uma com o seu próprio identificador. Se quiser utilizar uma conta para solicitar `acquireTokenSilent`a utilização de um token, então terá de selecionar a conta a partir da lista de contas que corresponda à política que está a usar com o pedido simbólico. Por exemplo:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```
