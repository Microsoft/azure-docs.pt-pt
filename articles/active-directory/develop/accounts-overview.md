---
title: Contas da plataforma de identidade microsoft & perfis de inquilinos no Android | Rio Azure
description: Uma visão geral da plataforma de identidade da Microsoft conta para o Android
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
ms.custom: aaddev, devx-track-java
ms.reviewer: shoatman
ms.openlocfilehash: fac66e8f82ea4c04e866b28fed5f8d0860ab81ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755977"
---
# <a name="accounts--tenant-profiles-android"></a>Contas e perfis de inquilino (Android)

Este artigo fornece uma visão geral do que é uma `account` coisa na plataforma de identidade da Microsoft.

A API da Biblioteca de Autenticação da Microsoft (MSAL) substitui o *utilizador* do termo pela *conta* de prazo . Uma das razões é que um utilizador (humano ou agente de software) pode ter, ou pode usar, várias contas. Estas contas podem estar na própria organização do utilizador e/ou noutras organizações de que o utilizador é membro.

Uma conta na plataforma de identidade da Microsoft consiste em:

- Um identificador único.  
- Uma ou mais credenciais utilizadas para demonstrar a propriedade/controlo da conta.
- Um ou mais perfis compostos por atributos como:
  - Imagem, Nome Dado, Nome da Família, Título, Localização do Escritório
- Uma conta tem uma fonte de autoridade ou sistema de registo. Este é o sistema onde a conta é criada e onde as credenciais associadas a essa conta são armazenadas. Em sistemas multi-inquilinos como a plataforma de identidade da Microsoft, o sistema de registo é o `tenant` local onde a conta foi criada. Este inquilino também é referido como `home tenant` o .
- As contas na plataforma de identidade da Microsoft têm os seguintes sistemas de registo:
  - Diretório Ativo Azure, incluindo Azure Ative Directory B2C.
  - Conta Microsoft (Live).
- As contas de sistemas de registo fora da plataforma de identidade da Microsoft estão representadas na plataforma de identidade da Microsoft, incluindo:
  - identidades de diretórios ligados às instalações (Windows Server Ative Directory)
  - identidades externas do LinkedIn, GitHub, e assim por diante.
  Nestes casos, uma conta possui um sistema de origem de registo e um sistema de registo dentro da plataforma de identidade da Microsoft.
- A plataforma de identidade da Microsoft permite que uma conta seja usada para aceder a recursos pertencentes a várias organizações (inquilinos do Azure Ative Directory).
  - Para registar que uma conta de um sistema de registo (AAD Tenant A) tem acesso a um recurso em outro sistema de registo (AAD Tenant B), a conta deve ser representada no arrendatário onde o recurso é definido. Isto é feito através da criação de um registo local da conta a partir do sistema A no sistema B.
  - Este registo local, que é a representação da conta, está ligado à conta original.
  - A MSAL expõe este registo local como `Tenant Profile` um .
  - O Perfil do Inquilino pode ter diferentes atributos que sejam adequados ao contexto local, tais como Título de Emprego, Localização do Escritório, Informações de Contacto, etc.
- Como uma conta pode estar presente em um ou mais inquilinos, uma conta pode ter mais do que um perfil.

> [!NOTE]
> A MSAL trata o sistema de conta microsoft (Live, MSA) como outro inquilino dentro da plataforma de identidade da Microsoft. O id do inquilino do inquilino da conta da Microsoft é: `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Diagrama de visão geral da conta

![Diagrama de visão geral da conta](./media/accounts-overview/accounts-overview.svg)

No diagrama acima:

- A conta `bob@contoso.com` é criada no Windows Server Ative Directory (sistema de registo de origem no local).
- A conta `tom@live.com` é criada no inquilino da conta da Microsoft.
- `bob@contoso.com` tem acesso a pelo menos um recurso nos seguintes inquilinos do Azure Ative Directory:
  - contoso.com (sistema de registo em nuvem - ligado ao sistema de registo no local)
  - fabrikam.com
  - woodgrovebank.com
  - Um perfil de inquilino para `bob@contoso.com` existir em cada um destes inquilinos.
- `tom@live.com` tem acesso a recursos nos seguintes inquilinos da Microsoft:
  - contoso.com
  - fabrikam.com
  - Um perfil de inquilino para `tom@live.com` existir em cada um destes inquilinos.
- A informação sobre o Tom e o Bob noutros inquilinos pode diferir da que está no sistema de registos. Podem diferir por atributos como O título de Emprego, Localização do Escritório, e assim por diante. Podem ser membros de grupos e/ou funções dentro de cada organização (Azure Ative Directory Tenant). Referimo-nos a esta informação como bob@contoso.com perfil de inquilino.

No diagrama, bob@contoso.com e ter acesso a recursos em tom@live.com diferentes inquilinos do Azure Ative Directory. Para mais informações, consulte [os utilizadores de colaboração do Add Azure Ative Directory B2B no portal Azure](../external-identities/add-users-administrator.md).

## <a name="accounts-and-single-sign-on-sso"></a>Contas e sso único (SSO)

O cache de token MSAL armazena um *único token de atualização* por conta. Esse token de atualização pode ser usado para solicitar silenciosamente tokens de acesso de vários inquilinos da plataforma de identidade microsoft. Quando um corretor é instalado num dispositivo, a conta é gerida pelo corretor, e o sign-on único em todo o dispositivo torna-se possível.

> [!IMPORTANT]
> A conta business to Consumer (B2C) e o comportamento de token de atualização diferem do resto da plataforma de identidade da Microsoft. Para mais informações, consulte [as Políticas B2C & Contas.](#b2c-policies--accounts)

## <a name="account-identifiers"></a>Identificadores de conta

A identificação da conta MSAL não é uma identificação de objeto de conta. Não é para ser analisado e/ou confiado para transmitir qualquer outra coisa que não a singularidade dentro da plataforma de identidade da Microsoft.

Para compatibilidade com a Biblioteca de Autenticação AD Azure (ADAL), e para facilitar a migração de ADAL para MSAL, a MSAL pode procurar contas utilizando qualquer identificador válido para a conta disponível na cache MSAL.  Por exemplo, o seguinte irá sempre recuperar o mesmo objeto de conta tom@live.com porque cada um dos identificadores é válido:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Aceder a reclamações sobre uma conta

Além de solicitar um token de acesso, a MSAL também pede sempre um sinal de identificação de cada inquilino. Fá-lo sempre solicitando os seguintes âmbitos:

- openid
- perfil

O símbolo de identificação contém uma lista de reclamações. `Claims` são pares de nome/valor sobre a conta, e são usados para fazer o pedido.

Como mencionado anteriormente, cada inquilino onde exista uma conta pode armazenar diferentes informações sobre a conta, incluindo, mas não se limitando a atributos como: título de emprego, localização de escritório, e assim por diante.

Embora uma conta possa ser um membro ou hóspede em várias organizações, a MSAL não consulta um serviço para obter uma lista dos inquilinos de que a conta é membro. Em vez disso, a MSAL constrói uma lista de inquilinos em que a conta está presente, em resultado de pedidos simbólicos que foram feitos.

As reclamações expostas no objeto da conta são sempre as reclamações da "autoridade do "inquilino doméstico"/{autoridade} para uma conta. Se essa conta não foi usada para solicitar um sinal para o seu inquilino de casa, a MSAL não pode fornecer reclamações através do objeto da conta.  Por exemplo:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Para ver uma lista de reclamações disponíveis no objeto da conta, consulte [as reclamações num id_token](./id-tokens.md#claims-in-an-id_token)

> [!TIP]
> Para incluir reclamações adicionais no seu id_token, consulte a documentação de reclamações opcionais em [Como: Fornecer reclamações opcionais à sua app AZure AD](./active-directory-optional-claims.md)

### <a name="access-tenant-profile-claims"></a>Acesso reclamações de perfil do inquilino

Para aceder a reclamações sobre uma conta tal como aparecem noutros inquilinos, primeiro tem de lançar o seu objeto de conta. `IMultiTenantAccount` Todas as contas podem ser multi-arrendatários, mas o número de perfis de inquilinos disponíveis via MSAL baseia-se nos inquilinos que você solicitou fichas de usar a conta corrente.  Por exemplo:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>Políticas B2C & contas

Os tokens de atualização para uma conta não são partilhados nas políticas de B2C. Como resultado, um único sinal de fichas não é possível. Isto não significa que uma única inscrição não é possível. Significa que um único sign-on tem de usar uma experiência interativa na qual um cookie está disponível para permitir um único s-on.

Isto também significa que, no caso da MSAL, se adquirir fichas utilizando diferentes políticas B2C, estas são tratadas como contas separadas - cada uma com o seu próprio identificador. Se quiser utilizar uma conta para solicitar uma ficha de `acquireTokenSilent` utilização, terá de selecionar a conta na lista de contas que corresponde à política que está a utilizar com o pedido de token. Por exemplo:

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