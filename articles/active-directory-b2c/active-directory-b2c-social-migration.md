---
title: Migrar os utilizadores com identidades sociais no Azure Active Directory B2C | Documentos da Microsoft
description: Fale sobre conceitos-chave na migração de utilizadores com identidades sociais para o Azure AD B2C com a Graph API.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a1ecc4de9475e735cd17286826c1d8cea05904ab
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089357"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Migrar os utilizadores com identidades sociais
Quando planeia migrar o seu fornecedor de identidade para o Azure AD B2C, também terá de migrar os utilizadores com identidades sociais. Este artigo explica como migrar as contas de identidades das redes sociais existentes, tais como: Contas do Facebook, LinkedIn, Microsoft e Google para o Azure AD B2C. Este artigo também se aplica a identidades federadas, no entanto, essas migrações são menos comuns.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo é uma continuação do artigo de migração de utilizador e se concentra na migração de identidade de redes sociais. Antes de começar, leia [migração de utilizador](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Introdução de migração de identidades sociais

* No Azure AD B2C, **contas locais** início de sessão os nomes (utilizador nome ou endereço de e-mail) são armazenados no `signInNames` coleção no registo de utilizador. O `signInNames` contém um ou mais `signInName` registos que especifique os nomes de início de sessão do utilizador. Cada nome de início de sessão tem de ser exclusivo entre o inquilino.

* **As contas sociais** identidades são armazenadas no `userIdentities` coleção. Especifica a entrada a `issuer` (nome do fornecedor de identidade), como facebook.com e o `issuerUserId`, que é um identificador de utilizador exclusivo para o emissor. O `userIdentities` atributo contém um ou mais registos de UserIdentity que especificam o tipo de conta de redes sociais e o identificador de utilizador exclusivo do fornecedor de identidade de redes sociais.

* **Combinar conta local com a identidade de redes sociais**. Conforme mencionado, os nomes de início de sessão de conta local e as identidades de contas de redes sociais são armazenados em atributos diferentes. `signInNames` é utilizado para a conta local, enquanto `userIdentities` para a conta de redes sociais. Uma única conta do Azure AD B2C, pode ser uma conta local apenas, apenas a conta de redes sociais ou combinar uma conta local com a identidade de redes sociais num registo de um utilizador. Este comportamento permite-lhe gerir uma conta única, embora um utilizador pode iniciar sessão com o credential(s) de conta local ou com as identidades sociais.

* `UserIdentity` Tipo – contém informações sobre a identidade de um utilizador de conta de redes sociais num inquilino do Azure AD B2C:
  * `issuer` A representação de cadeia de caracteres do fornecedor de identidade que emitiu o identificador de utilizador, como facebook.com.
  * `issuerUserId` O identificador de utilizador exclusivo utilizado pelo fornecedor de identidade social no formato base64.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* Dependendo do fornecedor de identidade, o **ID de utilizador de redes sociais** é um valor exclusivo para um determinado usuário por conta do aplicativo ou desenvolvimento. Configure a política do Azure AD B2C com o mesmo ID de aplicação que foi anteriormente atribuído pelo fornecedor de redes sociais. Ou outra aplicação dentro da mesma conta de desenvolvimento.

## <a name="use-graph-api-to-migrate-users"></a>Utilizar a Graph API para migrar utilizadores
Criar a conta de utilizador do Azure AD B2C através de [Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Para comunicar com a Graph API, primeiro tem de ter uma conta de serviço com privilégios administrativos. No Azure AD, pode registar uma aplicação e a autenticação para o Azure AD. As credenciais são ID da aplicação e segredo da aplicação. O aplicativo age em nome próprio, não como um utilizador, para chamar a API de gráfico. Siga as instruções no passo 1 [migração de utilizador](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) artigo.

## <a name="required-properties"></a>Propriedades necessárias
A lista seguinte mostra as propriedades que são necessárias quando cria um utilizador.
* **accountEnabled** - true
* **displayName** -o nome a apresentar no livro de endereços para o utilizador.
* **passwordProfile** -o perfil de palavra-passe do utilizador. 

> [!NOTE]
> Para a conta de redes sociais apenas (sem credenciais de conta local), ainda tem de especificar a palavra-passe. O Azure AD B2C ignora a palavra-passe que especificou para contas de redes sociais.

* **userPrincipalName** -o nome principal de utilizador (someuser@contoso.com). O nome principal de utilizador tem de conter um dos domínios verificados do inquilino. Para especificar o UPN, gerar novo valor GUID, concatenate com `@` e o nome do seu inquilino.
* **mailNickname** -o alias de correio do utilizador. Este valor pode ser o mesmo ID que utilizar para userPrincipalName. 
* **signInNames** -um ou mais registos de SignInName que especifique os nomes de início de sessão do utilizador. Cada nome de início de sessão tem de ser exclusivo em toda o empresa/inquilino. Para a conta de redes sociais apenas, esta propriedade pode ser deixada vazia.
* **userIdentities** -um ou mais registos de UserIdentity que especificam o social conta tipo e o identificador de utilizador exclusivo do fornecedor de identidade de redes sociais.
* [opcional] **otherMails** – para a conta de redes sociais apenas, endereços de e-mail do utilizador 

Para obter mais informações, consulte: [Referência da Graph API](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrar a conta de rede social (apenas)
Para criar conta de redes sociais apenas, sem as credenciais da conta local. Envie pedido POST de HTTPS para o Graph API. O corpo do pedido contém as propriedades do utilizador para criar conta de redes sociais. No mínimo, tem de especificar as propriedades necessárias. 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Os seguintes dados de formulário de submissão: 

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
## <a name="migrate-social-account-with-local-account"></a>Migrar a conta de redes sociais com a conta local
Para criar uma conta local combinada com identidades sociais. Envie pedido POST de HTTPS para o Graph API. O corpo do pedido contém as propriedades do utilizador para criar conta de redes sociais. No mínimo, tem de especificar as propriedades necessárias. 

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Após os dados do formulário de submissão: 

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
### <a name="how-can-i-know-the-issuer-name"></a>Como posso saber o nome do emissor?
O nome do emissor, ou o nome do fornecedor de identidade, está configurado na sua política. Se não souber o valor a especificar no `issuer`, siga este procedimento:
1. Inicie sessão com uma das contas de redes sociais
2. Do JWT token, copie o `sub` valor. O `sub` geralmente contém o ID de objeto do utilizador no Azure AD B2C. Ou a partir do portal do Azure, abra as propriedades de usuário e copie o ID de objeto.
3. Abra [do Azure AD Graph](https://graphexplorer.azurewebsites.net)
4. Inicie sessão com o administrador.
5. Execute o seguinte pedido GET. Substitua o userObjectId com o ID de utilizador que copiou. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Localize o `userIdentities` elemento dentro do retorno JSON do Azure AD B2C.
7. [Opcional] Também queira decodificar o `issuerUserId` valor.

> [!NOTE]
> Utilize uma conta de administrador de inquilino de B2C que seja local para o inquilino do B2C. A sintaxe do nome de conta é admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>É possível adicionar a identidade de redes sociais para uma conta local existente?
Sim. Depois de criar a conta local, é possível adicionar a identidade de redes sociais. Execute o pedido de PATCH de HTTPS. Substitua o userObjectId com o ID de utilizador que pretende atualizar. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Após os dados do formulário de submissão: 

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
Sim. Pode adicionar múltiplas identidades sociais para uma única conta do Azure AD B2C. Execute o pedido de PATCH de HTTPS. Substitua o userObjectId com o ID de utilizador. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Após os dados do formulário de submissão: 

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

## <a name="optional-user-migration-application-sample"></a>[Opcional] Exemplo de aplicação de migração de utilizador
[Transfira e execute a aplicação de exemplo V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). O exemplo de aplicação V2 utiliza um ficheiro JSON que contém dados de utilizador fictício, incluindo: conta local, conta de redes sociais e identidades de locais e redes sociais numa única conta.  Para editar o ficheiro JSON, abra o `AADB2C.UserMigration.sln` solução do Visual Studio. Na `AADB2C.UserMigration` projeto, abra o `UsersData.json` ficheiro. O ficheiro contém uma lista de entidades de utilizador. Cada entidade de utilizador tem as seguintes propriedades:
* **signInName** – para a conta local, o endereço de email para início de sessão
* **displayName** -nome a apresentar do utilizador
* **firstName** -nome próprio do utilizador
* **lastName** -apelido do utilizador
* **palavra-passe** para a conta local, a senha do usuário (pode estar vazio)
* **emissor** – para a conta de redes sociais, o nome do fornecedor de identidade
* **issuerUserId** – para a conta de redes sociais, o identificador de utilizador exclusivo utilizado pelo fornecedor de identidade de redes sociais. O valor deve ser em texto não criptografado. A aplicação de exemplo codifica este valor para base64.
* **e-mail** para do conta de redes sociais apenas (não combinado), utilizador endereço de e-mail

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
> Se não os atualizar o ficheiro de UsersData.json no exemplo com os seus dados, que pode iniciar sessão com as credenciais de conta local de exemplo, mas não com os exemplos de conta de redes sociais. Para migrar as suas contas de redes sociais, fornece dados reais.

Para obter mais informações, como utilizar a aplicação de exemplo, consulte [Azure Active Directory B2C: Migração de utilizador](active-directory-b2c-user-migration.md)
