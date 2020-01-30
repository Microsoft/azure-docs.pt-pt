---
title: Migrar os utilizadores com identidades sociais
titleSuffix: Azure AD B2C
description: Revisão dos conceitos fundamentais relativos à migração de utilizadores com identidades sociais para Azure AD B2C utilizando a API do Gráfico.
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
ms.openlocfilehash: 96bb4ef11b960a517054009f14f18b7af23fac14
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848840"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Diretório Ativo Azure B2C: Utilizadores migram com identidades sociais
Quando planeia migrar o seu fornecedor de identidade para o Azure AD B2C, também poderá ser necessário migrar utilizadores com identidades sociais. Este artigo explica como migrar as contas de identidade seletivas existentes, tais como: Facebook, LinkedIn, Microsoft e Google contas para Azure AD B2C. Este artigo aplica-se também às identidades federadas, no entanto estas migrações são menos comuns. Para o resto deste artigo, considere tudo o que se aplica às contas sociais para aplicar também a outros tipos de contas federadas.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo é uma continuação do artigo sobre migração de utilizadores, e centra-se na migração de identidade social. Antes de começar, leia [a migração dos utilizadores.](user-migration.md)

## <a name="social-identities-migration-introduction"></a>Introdução da migração de identidades sociais

* No Azure AD B2C, os nomes de registo de **contas locais** (nome de utilizador ou endereço de e-mail) são armazenados na recolha `signInNames` no registo do utilizador. O `signInNames` contém um ou mais registos `signInName` que especificam os nomes de inscrição para o utilizador. Cada nome de inscrição deve ser único em todo o inquilino.

* **As identidades das contas sociais** estão armazenadas na recolha `userIdentities`. A entrada especifica o `issuer` (nome do fornecedor de identidade) como facebook.com e o `issuerUserId`, que é um identificador único do utilizador para o emitente. O atributo `userIdentities` contém um ou mais registos de Identidade de Utilizador que especificam o tipo de conta social e o identificador único do utilizador do fornecedor de identidade social.

* **Combine a conta local com a identidade social.** Como mencionado, os nomes de inscrição de conta local e identidades de conta social são armazenados em diferentes atributos. `signInNames` é usado para conta local, enquanto `userIdentities` é usado para contas sociais. Uma única conta Azure AD AD B2C pode ser apenas uma conta local, apenas uma conta social, ou combinar uma conta local com uma ou mais identidades sociais num registo de utilizador. Este comportamento permite-lhe gerir uma única conta, enquanto um utilizador pode iniciar sessão com a credencial da conta local ou com as identidades sociais.

* `UserIdentity` Tipo - Contém informações sobre a identidade de um utilizador de conta social num inquilino Azure AD B2C:
  * `issuer` A representação de cadeias do fornecedor de identidade que emitiu o identificador de utilizador, como facebook.com.
  * `issuerUserId` O identificador de utilizador único utilizado pelo fornecedor de identidade social em formato codificado pela Base64.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* Dependendo do fornecedor de identidade, o ID do **utilizador emitente** é um valor único para um determinado utilizador por conta de desenvolvimento ou aplicação. Configure a política Azure AD B2C com o mesmo ID de aplicação que foi previamente atribuído pelo prestador social ou outra aplicação dentro da mesma conta de desenvolvimento.

## <a name="use-graph-api-to-migrate-users"></a>Use a API do Gráfico para migrar utilizadores
Cria a conta de utilizador Azure AD B2C através da [API do Gráfico](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet).
Para comunicar com a API graph, primeiro deve ter uma conta de serviço com privilégios administrativos. Em Azure AD, registe uma aplicação e autenticação na Azure AD. As credenciais de aplicação são id de aplicação e segredo de aplicação. A aplicação funciona por si só, não como utilizador, para chamar a API do Gráfico. Siga as instruções do passo 1 do artigo migração do [Utilizador.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration)

## <a name="required-properties"></a>Propriedades necessárias
A lista que se segue mostra as propriedades que são necessárias quando cria um utilizador.
* **contaEnabled** - verdadeiro
* **displayName** - O nome a apresentar na lista de endereços para o utilizador.
* **passwordPerfil** - O perfil de palavra-passe para o utilizador.

> [!NOTE]
> Apenas para contas sociais (sem credenciais de conta local), ainda deve especificar a palavra-passe. O Azure AD B2C ignora a palavra-passe que especifica para contas sociais.

* **userprincipalname** - O nome principal do utilizador (someuser@contoso.com). O nome principal do utilizador deve conter um dos domínios verificados para o inquilino. Para especificar a UPN, gere um novo valor GUID, concatena com `@` e o nome do seu inquilino.
* **mailNickname** - O pseudónimo de correio para o utilizador. Este valor pode ser o mesmo ID que utiliza para o nome principal do utilizador.
* **SignInNames** - Um ou mais registos signInName que especificam os nomes de inscrição para o utilizador. Cada nome de inscrição deve ser único em toda a empresa/inquilino. Apenas para a conta social, esta propriedade pode ser deixada vazia.
* **userIdentidades** - Um ou mais registos de Identidade de Utilizador que especificam o tipo de conta social e o identificador de utilizador único do fornecedor de identidade social.
* [opcional] **outrosMails** - Apenas para conta social, os endereços de e-mail do utilizador

Para mais informações, consulte: [Referência da API do gráfico](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Conta social de migração (apenas)
Para criar apenas conta social, sem credenciais de conta local, envie um pedido HTTPS POST para a API graph. O organismo de pedido contém as propriedades do utilizador da conta social para criar. No mínimo, deve especificar as propriedades necessárias.


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
Para criar uma conta local combinada com identidades sociais, envie um pedido HTTPS POST para a API graph. O organismo de pedido contém as propriedades do utilizador da conta social para criar, incluindo o sinal em nome da conta local. No mínimo, deve especificar as propriedades necessárias.

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Submeter os seguintes dados de formulário:

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
### <a name="how-can-i-know-the-issuer-name"></a>Como posso saber o nome do emitente?
O nome do emitente, ou o nome do fornecedor de identidade, está configurado na sua política. Se não sabe o valor a especificar em `issuer`, siga este procedimento:
1. Inscreva-se numa das contas sociais
2. A partir do símbolo JWT, copie o valor `sub`. O `sub` geralmente contém o ID do objeto do utilizador em Azure AD B2C. Ou do portal Azure, abra as propriedades do utilizador e copie o ID do objeto.
3. Explorador de [gráficos ad azure](https://graphexplorer.azurewebsites.net)
4. Inscreva-se com o seu administrador.
5. Corra seguindo o pedido get. Substitua o utilizadorObjectId pelo ID do utilizador copiado. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Localize o elemento `userIdentities` dentro do retorno JSON a partir de Azure AD B2C.
7. [Opcional] Também pode querer descodificar o valor `issuerUserId`.

> [!NOTE]
> Utilize uma conta de administrador de inquilinoB2C que seja local para o inquilino B2C. A sintaxe do nome da conta está admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-a-social-identity-to-an-existing-user"></a>É possível adicionar uma identidade social a um utilizador existente?
Sim. Pode adicionar a identidade social após a criação da conta Azure AD B2C (seja uma conta local ou social, ou uma combinação da mesmas). Executar um pedido HTTPS PATCH. Substitua o utilizadorObjectId pelo ID do utilizador que pretende atualizar.

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Submeter os seguintes dados de formulário:

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

### <a name="is-it-possible-to-add-multiple-social-identities"></a>É possível adicionar múltiplas identidades sociais?
Sim. Pode adicionar múltiplas identidades sociais para uma única conta Azure AD B2C. Executar pedido https patch. Substitua o utilizador ObjectId pelo ID do utilizador.

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Submeter os seguintes dados de formulário:

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

## <a name="optional-user-migration-application-sample"></a>[Opcional] Amostra de aplicação de migração do utilizador
[Descarregue e execute a aplicação de amostraV2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). A aplicação de amostraV2 utiliza um ficheiro JSON que contém dados de utilizadores manequim, incluindo: conta local, conta social e identidades locais e sociais numa única conta.  Para editar o ficheiro JSON, abra a solução `AADB2C.UserMigration.sln` Visual Studio. No projeto `AADB2C.UserMigration`, abra o ficheiro `UsersData.json`. O ficheiro contém uma lista de entidades utilizadoras. Cada entidade utilizadora tem as seguintes propriedades:
* **signInName** - Para conta local, endereço de e-mail para iniciar sessão
* nome do **visualização** - nome de exibição do utilizador
* **primeiro nome** - primeiro nome do utilizador
* **apelido** - apelido do utilizador
* **senha** Para a conta local, a palavra-passe do utilizador (pode estar vazia)
* **emitente** - Para conta social, o nome do fornecedor de identidade
* **emitenteUserId** - Para conta social, o identificador único do utilizador utilizado pelo fornecedor de identidade social. O valor deve estar em texto claro. A aplicação de amostracodifica este valor para base64.
* **e-mail** Apenas para conta social (não combinada), endereço de e-mail do utilizador

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
> Se não atualizar o ficheiro UsersData.json na amostra com os seus dados, pode iniciar sessão com as credenciais da conta local da amostra, mas não com os exemplos da conta social. Para migrar as suas contas sociais, forneça dados reais.

Para mais informações, como utilizar a aplicação de amostras, consulte [Azure Ative Directory B2C: Migração](user-migration.md) de utilizadores
