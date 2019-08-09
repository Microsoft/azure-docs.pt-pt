---
title: Migrar usuários com identidades sociais no Azure Active Directory B2C | Microsoft Docs
description: Discuta os principais conceitos sobre a migração de usuários com identidades sociais em Azure AD B2C usando API do Graph.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 0117a0881422584e3cb949661b1d58cd0257cf67
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853854"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Migrar os utilizadores com identidades sociais
Quando você planeja migrar seu provedor de identidade para Azure AD B2C, talvez também seja necessário migrar usuários com identidades sociais. Este artigo explica como migrar contas de identidades sociais existentes, como: Contas do Facebook, LinkedIn, Microsoft e Google para Azure AD B2C. Este artigo também se aplica a identidades federadas, no entanto, essas migrações são menos comuns. Para o restante deste artigo, considere qualquer coisa que se aplique a contas sociais também se aplique a outros tipos de contas federadas.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo é uma continuação do artigo de migração de usuário e se concentra na migração de identidade social. Antes de começar, leia a [migração do usuário](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Introdução à migração de identidades sociais

* No Azure ad B2C, os nomes de entrada de **contas locais** (nome de usuário ou endereço de email) são `signInNames` armazenados na coleção no registro de usuário. O `signInNames` contém um ou mais `signInName` registros que especificam os nomes de entrada para o usuário. Cada nome de entrada deve ser exclusivo em todo o locatário.

* **As identidades das contas sociais** são `userIdentities` armazenadas na coleção. A entrada especifica o `issuer` (nome do provedor de identidade), como Facebook.com `issuerUserId`e o, que é um identificador de usuário exclusivo para o emissor. O `userIdentities` atributo contém um ou mais registros UserIdentity que especificam o tipo de conta social e o identificador de usuário exclusivo do provedor de identidade social.

* **Combine a conta local com a identidade social**. Como mencionado, os nomes de entrada da conta local e as identidades de conta social são armazenados em atributos diferentes. `signInNames`é usado para a conta local, `userIdentities` enquanto é usado para contas sociais. Uma única conta de Azure AD B2C pode ser apenas uma conta local, somente conta social ou combinar uma conta local com uma ou mais identidades sociais em um registro de usuário. Esse comportamento permite que você gerencie uma única conta, enquanto um usuário pode entrar com as credenciais de conta local ou com as identidades sociais.

* `UserIdentity`Tipo-contém informações sobre a identidade de um usuário de conta social em um locatário Azure AD B2C:
  * `issuer`A representação de cadeia de caracteres do provedor de identidade que emitiu o identificador de usuário, como facebook.com.
  * `issuerUserId`O identificador de usuário exclusivo usado pelo provedor de identidade social no formato codificado em base64.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* Dependendo do provedor de identidade, a **ID de usuário do emissor** é um valor exclusivo para um determinado usuário por aplicativo ou conta de desenvolvimento. Configure a política de Azure AD B2C com a mesma ID de aplicativo que foi atribuída anteriormente pelo provedor social ou outro aplicativo dentro da mesma conta de desenvolvimento.

## <a name="use-graph-api-to-migrate-users"></a>Usar API do Graph para migrar usuários
Você cria a conta de usuário Azure AD B2C por meio do [API do Graph](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Para se comunicar com o API do Graph, primeiro você deve ter uma conta de serviço com privilégios administrativos. No Azure AD, você registra um aplicativo e autenticação no Azure AD. As credenciais do aplicativo são a ID do aplicativo e o segredo do aplicativo. O aplicativo atua como ele mesmo, não como um usuário, para chamar o API do Graph. Siga as instruções na etapa 1 no artigo [migração de usuário](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) .

## <a name="required-properties"></a>Propriedades necessárias
A lista a seguir mostra as propriedades que são necessárias quando você cria um usuário.
* **accountEnabled** -verdadeiro
* **DisplayName** -o nome a ser exibido no catálogo de endereços do usuário.
* **passwordProfile** -o perfil de senha para o usuário. 

> [!NOTE]
> Para contas sociais somente (sem credenciais de conta local), você ainda deve especificar a senha. Azure AD B2C ignora a senha que você especificar para contas sociais.

* **userPrincipalName** -o nome principal do usuáriosomeuser@contoso.com(). O nome principal do usuário deve conter um dos domínios verificados para o locatário. Para especificar o UPN, gere o novo valor de GUID, concatene `@` com e o nome do locatário.
* **mailNickname** -o alias de email do usuário. Esse valor pode ser a mesma ID que você usa para o userPrincipalName. 
* **signInNames** -um ou mais registros de SignInName que especificam os nomes de entrada para o usuário. Cada nome de entrada deve ser exclusivo em toda a empresa/locatário. Somente para conta social, essa propriedade pode ser deixada vazia.
* **useridentities** -um ou mais registros UserIdentity que especificam o tipo de conta social e o identificador de usuário exclusivo do provedor de identidade social.
* adicional **otherMails** – somente para conta social, endereços de email do usuário 

Para obter mais informações, consulte: [Referência de API do Graph](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrar conta social (somente)
Para criar apenas uma conta social, sem credenciais de conta local, envie uma solicitação HTTPS POST para API do Graph. O corpo da solicitação contém as propriedades do usuário da conta social a ser criada. No mínimo, você deve especificar as propriedades necessárias. 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Envie os seguintes dados de formulário: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>Migrar conta social com conta local
Para criar uma conta local combinada com identidades sociais, envie uma solicitação HTTPS POST para a API do Graph. O corpo da solicitação contém as propriedades do usuário da conta social para criar, incluindo o nome de entrada da conta local. No mínimo, você deve especificar as propriedades necessárias. 

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Enviar dados do formulário a seguir: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
### <a name="how-can-i-know-the-issuer-name"></a>Como saber o nome do emissor?
O nome do emissor, ou o nome do provedor de identidade, é configurado em sua política. Se você não souber o valor a ser especificado `issuer`em, siga este procedimento:
1. Entrar com uma das contas sociais
2. No token JWT, copie o `sub` valor. O `sub` geralmente contém a ID de objeto do usuário em Azure ad B2C. Ou de portal do Azure, abra as propriedades do usuário e copie a ID do objeto.
3. Abrir o [Explorador do Azure ad Graph](https://graphexplorer.azurewebsites.net)
4. Entre com seu administrador.
5. Execute a seguinte solicitação GET. Substitua userobjectid pela ID de usuário que você copiou. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Localize o `userIdentities` elemento dentro do retorno de JSON de Azure ad B2C.
7. Adicional Talvez você também queira decodificar o `issuerUserId` valor.

> [!NOTE]
> Use uma conta de administrador de locatário B2C que seja local para o locatário B2C. A sintaxe do nome da admin@tenant-name.onmicrosoft.comconta é.

### <a name="is-it-possible-to-add-a-social-identity-to-an-existing-user"></a>É possível adicionar uma identidade social a um usuário existente?
Sim. Você pode adicionar a identidade social depois que a conta de Azure AD B2C tiver sido criada (independentemente de ser uma conta local ou social ou uma combinação delas). Execute uma solicitação de PATCH HTTPS. Substitua userobjectid pela ID de usuário que você deseja atualizar. 

**PATCH** DO https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Enviar dados do formulário a seguir: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>É possível adicionar várias identidades sociais?
Sim. Você pode adicionar várias identidades sociais para uma única conta de Azure AD B2C. Executar solicitação de PATCH HTTPS. Substitua o userobjectid pela ID de usuário. 

**PATCH** DO https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Enviar dados do formulário a seguir: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>Adicional Exemplo de aplicativo de migração de usuário
[Baixe e execute o aplicativo de exemplo v2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). O aplicativo de exemplo v2 usa um arquivo JSON que contém dados de usuário fictícios, incluindo: conta local, conta social e identidades sociais locais & em uma única conta.  Para editar o arquivo JSON, abra a `AADB2C.UserMigration.sln` solução do Visual Studio. No projeto, abra o `UsersData.json` arquivo. `AADB2C.UserMigration` O arquivo contém uma lista de entidades de usuário. Cada entidade de usuário tem as seguintes propriedades:
* **signInName** -para conta local, endereço de email para entrar
* **DisplayName** -nome de exibição do usuário
* **FirstName** -nome do usuário
* **LastName** – sobrenome do usuário
* **senha** do Para conta local, a senha do usuário (pode estar vazia)
* **emissor** -para conta social, o nome do provedor de identidade
* **issuerUserId** – para conta social, o identificador de usuário exclusivo usado pelo provedor de identidade social. O valor deve estar em texto não criptografado. O aplicativo de exemplo codifica esse valor para Base64.
* **email** Somente para conta social (não combinada), endereço de email do usuário

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> Se você não atualizar o arquivo UsersData. JSON no exemplo com seus dados, poderá entrar com as credenciais de conta local de exemplo, mas não com os exemplos de conta social. Para migrar suas contas sociais, forneça dados reais.

Para obter mais informações, como usar o aplicativo de exemplo, [consulte Azure Active Directory B2C: Migração de usuário](active-directory-b2c-user-migration.md)
