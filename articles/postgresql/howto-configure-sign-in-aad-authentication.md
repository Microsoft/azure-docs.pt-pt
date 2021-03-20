---
title: Utilize o Azure Ative Directory - Azure Database for PostgreSQL - Single Server
description: Saiba como configurar o Azure Ative Directory (AAD) para autenticação com Base de Dados Azure para PostgreSQL - Servidor Único
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: 178c339f6f47569160a9a748794678c610f35734
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87171640"
---
# <a name="use-azure-active-directory-for-authentication-with-postgresql"></a>Utilize o Diretório Ativo Azure para autenticação com PostgreSQL

Este artigo irá acompanhá-lo através dos passos como configurar o acesso do Azure Ative Directory com a Azure Database for PostgreSQL, e como conectar-se usando um token AD Azure.

## <a name="setting-the-azure-ad-admin-user"></a>Definição do utilizador Azure AD Admin

Apenas os utilizadores de administrador a AD do Azure podem criar/permitir a autenticação baseada em Azure. Recomendamos que não utilize o administrador AZure AD para operações regulares de base de dados, uma vez que tem permissões elevadas de utilizador (por exemplo, CREATEDB).

Para definir o administrador AD Azure (pode utilizar um utilizador ou um grupo), siga os seguintes passos

1. No portal Azure, selecione a instância da Base de Dados Azure para PostgreSQL que pretende ativar para Azure AD.
2. Em Definições, selecione Ative Directory Admin:

![definir administrador de anúncios azure][2]

3. Selecione um utilizador Azure AD válido no inquilino do cliente para ser administrador da AD Azure.

> [!IMPORTANT]
> Ao definir o administrador, um novo utilizador é adicionado à Base de Dados Azure para o servidor PostgreSQL com permissões completas de administrador. O utilizador Azure AD Admin na Base de Dados Azure para PostgreSQL terá o papel `azure_ad_admin` .

Apenas um administrador AD AD Azure pode ser criado por servidor PostgreSQL e a seleção de outro substituirá a admin AD Admin existente configurada para o servidor. Pode especificar um grupo AD Azure em vez de um utilizador individual para ter vários administradores. Note que irá então iniciar sômsim com o nome do grupo para fins de administração.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Ligação à Base de Dados de Azure para PostgreSQL utilizando Azure AD

O seguinte diagrama de alto nível resume o fluxo de trabalho da utilização da autenticação AD Azure com base de dados Azure para PostgreSQL:

![fluxo de autenticação][1]

Concebemos a integração AD do Azure para trabalhar com ferramentas postgresQL comuns como o psql, que não estão cientes do Azure AD e apenas suportam especificar o nome de utilizador e a palavra-passe ao ligar-se ao PostgreSQL. Passamos o token AD AZure como a palavra-passe como mostrado na imagem acima.

Atualmente testámos os seguintes clientes:

- linha de comando psql (utilizar a variável PGPASSWORD para passar o token, ver abaixo)
- Azure Data Studio (utilizando a extensão PostgreSQL)
- Outros clientes baseados em libpq (por exemplo, quadros de aplicação comuns e ORMs)

> [!NOTE]
> Tenha em atenção que a utilização do token AD Azure com pgAdmin não está suportada, uma vez que tem uma limitação codificada por 256 caracteres para senhas (que o token excede).

Estes são os passos que um utilizador/aplicação terá de fazer autenticar com Azure AD descrito abaixo:

### <a name="prerequisites"></a>Pré-requisitos

Pode seguir em Azure Cloud Shell, um Azure VM ou na sua máquina local. Certifique-se de que tem o [Azure CLI instalado](/cli/azure/install-azure-cli).

### <a name="step-1-authenticate-with-azure-ad"></a>Passo 1: Autenticar com Azure AD

Comece por autenticar com Azure AD utilizando a ferramenta Azure CLI. Este passo não é necessário na Azure Cloud Shell.

```
az login
```

O comando lançará uma janela do navegador para a página de autenticação AZure AD. Requer que dê o seu ID de utilizador Azure E a palavra-passe.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Passo 2: Recuperar o sinal de acesso a AD do AD

Invoque a ferramenta Azure CLI para adquirir um token de acesso para o utilizador autenticado Azure AD a partir do passo 1 para aceder à Base de Dados Azure para PostgreSQL.

Exemplo (para Nuvem Pública):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

O valor do recurso acima referido deve ser especificado exatamente como mostrado. Para outras nuvens, o valor do recurso pode ser analisado utilizando:

```azurecli-interactive
az cloud show
```

Para a versão 2.0.71 do CLI Azure e posteriormente, o comando pode ser especificado na seguinte versão mais conveniente para todas as nuvens:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

Após o sucesso da autenticação, a Azure AD devolverá um token de acesso:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

O token é uma cadeia base 64 que codifica todas as informações sobre o utilizador autenticado, e que é direcionada para a Base de Dados Azure para o serviço PostgreSQL.

> [!NOTE]
> A validade do sinal de acesso está entre 5 minutos e 60 minutos. Recomendamos que obtenha o token de acesso pouco antes de iniciar o login na Base de Dados Azure para PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Passo 3: Use o token como senha para iniciar sessão com PostgreSQL

Ao ligar, precisa de utilizar o token de acesso como palavra-passe do utilizador PostgreSQL.

Ao utilizar o cliente da `psql` linha de comando, o token de acesso precisa de ser passado através da `PGPASSWORD` variável ambiental, uma vez que o token de acesso excede o comprimento da palavra-passe que `psql` pode aceitar diretamente:

Exemplo do Windows:

```cmd
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

```PowerShell
$env:PGPASSWORD='<copy/pasted TOKEN value from step 2>'
```

Linux/macOS Exemplo:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Agora pode iniciar uma ligação com a Base de Dados Azure para PostgreSQL como normalmente faria:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Considerações importantes na ligação:

* `user@tenant.onmicrosoft.com` é o nome do utilizador ou grupo AZure AD que está a tentar ligar como
* Apecimos sempre o nome do servidor após o nome de utilizador/grupo AZure AD (por `@mydb` exemplo)
* Certifique-se de que utiliza a forma exata como o utilizador AD AD ou o nome de grupo do Azure está escrito
* O utilizador azure AD e os nomes de grupo são sensíveis a casos
* Ao ligar em grupo, utilize apenas o nome de grupo (por `GroupName@mydb` exemplo)
* Se o nome contiver espaços, use `\` antes de cada espaço para escapar

Está agora autenticado no seu servidor PostgreSQL utilizando a autenticação AZure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Criação de utilizadores de AD Azure na Base de Dados Azure para PostgreSQL

Para adicionar um utilizador AD Azure à sua base de dados Azure para a base de dados PostgreSQL, execute os seguintes passos após a ligação (ver secção posterior sobre como ligar):

1. Em primeiro lugar, certifique-se de que o utilizador Azure AD `<user>@yourtenant.onmicrosoft.com` é um utilizador válido no inquilino AZure AD.
2. Inscreva-se na sua Base de Dados Azure para o caso PostgreSQL como utilizador do Azure AD Admin.
3. Criar papel `<user>@yourtenant.onmicrosoft.com` na Base de Dados Azure para PostgreSQL.
4. Faça `<user>@yourtenant.onmicrosoft.com` um membro do papel azure_ad_user. Isto só deve ser dado aos utilizadores da AD Azure.

**Exemplo:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Autenticar um utilizador através do Azure AD não dá ao utilizador quaisquer permissões para aceder a objetos dentro da base de dados Azure para base de dados PostgreSQL. Deve conceder ao utilizador as permissões necessárias manualmente.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Criar grupos AD Azure em Base de Dados Azure para PostgreSQL

Para permitir o acesso a um grupo Azure AD para aceder à sua base de dados, utilize o mesmo mecanismo que os utilizadores, mas em vez disso especifique o nome do grupo:

**Exemplo:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Ao iniciar sessão, os membros do grupo utilizarão os seus tokens de acesso pessoal, mas assinarão com o nome de grupo especificado como nome de utilizador.

## <a name="token-validation"></a>Validação simbólica

A autenticação AD AD em Azure Database for PostgreSQL garante que o utilizador existe no servidor PostgreSQL, verificando a validade do token validando o conteúdo do token. São realizadas as seguintes etapas de validação simbólicas:

- Token é assinado pela Azure AD e não foi adulterado
- Token foi emitido pela Azure AD para o inquilino associado ao servidor
- Token não expirou
- Token é para a Base de Dados Azure para recurso PostgreSQL (e não outro recurso Azure)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migração de utilizadores postgreSQL existentes para a autenticação baseada em Azure AD

Pode ativar a autenticação Azure AD para os utilizadores existentes. Há dois casos a considerar:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Caso 1: O nome de utilizador postgreSQL corresponde ao nome principal do utilizador Azure AD

No caso improvável de os seus utilizadores já corresponderem aos nomes de utilizador da AD Azure, pode `azure_ad_user` conceder-lhes a função de forma a permitir-lhes a autenticação Azure AD:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Agora poderão iniciar seduções com credenciais AD AZure em vez de usar a sua senha de utilizador postgreSQL previamente configurada.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Caso 2: O nome de utilizador postgreSQL é diferente do Nome Principal do Utilizador Azure AD

Se um utilizador postgreSQL não existir em AD ou tiver um nome de utilizador diferente, pode utilizar grupos AD Azure para autenticar como utilizador do PostgreSQL. Pode migrar a Base de Dados Azure existente para utilizadores postgresQL para Azure AD, criando um grupo AD Azure com um nome que corresponda ao utilizador PostgreSQL e, em seguida, concedendo papel azure_ad_user ao utilizador pós-greSQL existente:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Isto pressupõe que criou um grupo "DBReadUser" no seu AD Azure. Os utilizadores pertencentes a esse grupo poderão agora iniciar susso na base de dados como utilizador.

## <a name="next-steps"></a>Passos seguintes

* Reveja os conceitos globais de [autenticação do Azure Ative Directory com Azure Database for PostgreSQL - Single Server](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
