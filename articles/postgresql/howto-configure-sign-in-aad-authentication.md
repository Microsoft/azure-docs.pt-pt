---
title: Utilizar o Diretório Ativo Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba como configurar o Diretório Ativo Azure (AAD) para autenticação com base de dados Azure para PostgreSQL - Servidor Único
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f5588503825281f407ddbbc2c1c57cd94a9c7ee6
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804712"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Utilizar o Diretório Ativo Azure para autenticação com postgreSQL

Este artigo irá acompanhá-lo através dos passos como configurar o acesso ao Diretório Ativo Azure com a Base de Dados Azure para PostgreSQL, e como se conectar usando um token Azure AD.

> [!IMPORTANT]
> A autenticação Azure AD para a Base de Dados Azure para PostgreSQL encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="setting-the-azure-ad-admin-user"></a>Definição do utilizador azure AD Admin

Apenas os utilizadores de administradores da AD Azure podem criar/ativar os utilizadores para a autenticação baseada em AD Azure. Recomendamos que não utilize o administrador da AD Azure para operações regulares de base de dados, uma vez que tem permissões elevadas de utilizador (por exemplo, CREATEDB).

Para definir o administrador da AD Azure (pode utilizar um utilizador ou um grupo), siga os seguintes passos

1. No portal Azure, selecione a instância de Base de Dados Azure para PostgreSQL que pretende ativar para AD Azure.
2. Em Definições, selecione Ative Directory Admin:

![definir administrador de anúncios azure][2]

3. Selecione um utilizador AD Azure válido no inquilino do cliente para administrador da Azure AD.

> [!IMPORTANT]
> Ao definir o administrador, um novo utilizador é adicionado à Base de Dados Azure para o servidor PostgreSQL com permissões completas do administrador. O utilizador da Azure AD Admin na Base de Dados `azure_ad_admin`Azure para PostgreSQL terá o papel .

Apenas um administrador Azure AD pode ser criado por servidor PostgreSQL e a seleção de outro irá substituir o administrador Azure AD existente configurado para o servidor. Pode especificar um grupo DeD Azure em vez de um utilizador individual ter vários administradores. Note que irá então assinar com o nome de grupo para fins administrativos.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Ligação à Base de Dados Azure para PostgreSQL utilizando AD Azure

O diagrama de alto nível que se segue resume o fluxo de trabalho da utilização da autenticação Azure AD com base de dados Azure para PostgreSQL:

![fluxo de autenticação][1]

Desenhamos a integração da AD Azure para trabalhar com ferramentas postgresQL comuns como o psql, que não são conscientes da AD Azure e apenas suportam especificar o nome de utilizador e a palavra-passe quando se conectam ao PostgreSQL. Passamos o token Azure AD como a palavra-passe como mostrado na imagem acima.

Testámos atualmente os seguintes clientes:

- linha de comando psql (utilize a variável PGPASSWORD para passar o símbolo, ver abaixo)
- Estúdio de Dados Azure (utilizando a extensão PostgreSQL)
- Outros clientes baseados em libpq (por exemplo, quadros comuns de aplicação e ORMs)

> [!NOTE]
> Tenha em atenção que a utilização do token Azure AD com pgAdmin não é atualmente suportada, uma vez que tem uma limitação codificada de 256 caracteres para senhas (que o token excede).

Estes são os passos que um utilizador/aplicação terá de fazer autenticar com a Azure AD descrita abaixo:

### <a name="step-1-authenticate-with-azure-ad"></a>Passo 1: Autenticar com AD Azure

Certifique-se de que tem o [Azure CLI instalado](/cli/azure/install-azure-cli).

Invoque a ferramenta Azure CLI para autenticar com AD Azure. Requer que forneça o seu ID de utilizador da AD Azure e a palavra-passe.

```azurecli-interactive
az login
```

Este comando lançará uma janela de navegador para a página de autenticação da AD Azure.

> [!NOTE]
> Também pode utilizar a Azure Cloud Shell para realizar estes passos.
> Tenha em atenção que ao recuperar o token de acesso azure AD `az login` na Casca de Nuvem Azure terá de ligar e iniciar sessão (na janela separada com um código). Depois do sinal `get-access-token` no comando funcionará como esperado.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Passo 2: Ficha de acesso a AD Da Recuperação Azure

Invoque a ferramenta Azure CLI para adquirir um token de acesso para o utilizador autenticado Azure AD a partir do passo 1 para aceder à Base de Dados Azure para PostgreSQL.

Exemplo (para Nuvem Pública):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

O valor acima referido deve ser especificado exatamente como indicado. Para outras nuvens, o valor do recurso pode ser procurado usando:

```azurecli-interactive
az cloud show
```

Para a versão 2.0.71 do Azure CLI e posteriormente, o comando pode ser especificado na seguinte versão mais conveniente para todas as nuvens:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

Após a autenticação ser bem sucedida, a Azure AD devolverá um sinal de acesso:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

O símbolo é uma cadeia Base 64 que codifica todas as informações sobre o utilizador autenticado, e que é direcionada para a Base de Dados Azure para o serviço PostgreSQL.

> [!NOTE]
> A validade do token de acesso é entre 5 minutos e 60 minutos. Recomendamos que obtenha o token de acesso antes de iniciar o login na Base de Dados Azure para PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Passo 3: Utilize o símbolo como senha para iniciar sessão com postgreSQL

Ao ligar, tem de utilizar o token de acesso como senha de utilizador do PostgreSQL.

Ao utilizar `psql` o cliente da linha de comando, `PGPASSWORD` o sinal de acesso precisa de ser `psql` passado através da variável ambiental, uma vez que o token de acesso excede o comprimento da palavra-passe que pode ser aceite diretamente:

Exemplo do Windows:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Exemplo linux/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Agora pode iniciar uma ligação com a Base de Dados Azure para PostgreSQL como normalmente faria:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Agora está autenticado no seu servidor PostgreSQL utilizando a autenticação Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Criação de utilizadores de Anúncios Azure na Base de Dados Azure para PostgreSQL

Para adicionar um utilizador Azure AD à sua Base de Dados Azure para base de dados PostgreSQL, execute os seguintes passos após a ligação (ver secção posterior sobre como ligar):

1. Em primeiro lugar, certifique-se de que o utilizador `<user>@yourtenant.onmicrosoft.com` da AD Azure é um utilizador válido no inquilino Da Azure AD.
2. Inscreva-se na sua Base de Dados Azure para a instância PostgreSQL como utilizador do Anúncio AD Azure.
3. Criar `<user>@yourtenant.onmicrosoft.com` papel na Base de Dados Azure para PostgreSQL.
4. Faça `<user>@yourtenant.onmicrosoft.com` um membro do papel azure_ad_user. Isto só deve ser dado aos utilizadores da Azure AD.

**Exemplo:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> A autenticação de um utilizador através do Azure AD não dá ao utilizador quaisquer permissões para aceder a objetos dentro da Base de Dados Azure para base de dados PostgreSQL. Deve conceder manualmente ao utilizador as permissões necessárias.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Criação de grupos de AD Azure na Base de Dados Azure para PostgreSQL

Para permitir um grupo DeA Azure para acesso à sua base de dados, utilize o mesmo mecanismo que para os utilizadores, mas em vez disso especifique o nome de grupo:

**Exemplo:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Ao iniciar sessão, os membros do grupo usarão as suas fichas de acesso pessoal, mas assinarão com o nome de grupo especificado como nome de utilizador.

## <a name="token-validation"></a>Validação de Fichas

A autenticação Azure AD na Base de Dados Azure para postgreSQL garante que o utilizador existe no servidor PostgreSQL, e verifica a validade do token validando o conteúdo do símbolo. São executadas as seguintes etapas de validação simbólica:

- Token é assinado pela Azure AD e não foi adulterado
- Token foi emitida pela Azure AD para o inquilino associado ao servidor
- Token não expirou
- Token é para a Base de Dados Azure para recurso PostgreSQL (e não outro recurso Azure)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Utilizadores postgresql existentes para autenticação baseada em Azure AD

Pode ativar a autenticação adato de AD Azure para os utilizadores existentes. Há dois casos a considerar:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Caso 1: O nome de utilizador PostgreSQL corresponde ao nome principal do utilizador da AD Azure

No caso improvável de os utilizadores existentes já corresponderem aos nomes `azure_ad_user` de utilizadores da AD Azure, pode conceder-lhes a função de forma a permitir a autenticação da AD Azure:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Agora poderão iniciar sessão com credenciais de AD Azure em vez de utilizarem a sua senha de utilizador postgresQL previamente configurada.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Caso 2: O nome de utilizador PostgreSQL é diferente do nome principal do utilizador da AD Azure

Se um utilizador do PostgreSQL não existir no Azure AD ou tiver um nome de utilizador diferente, pode utilizar grupos AD Azure para autenticar como este utilizador PostgreSQL. Pode migrar a base de dados azure existente para utilizadores postgreSQL para a AD Azure através da criação de um grupo Azure AD com um nome que corresponda ao utilizador PostgreSQL e, em seguida, concedendo o papel azure_ad_user ao utilizador postgresQL existente:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Isto pressupõe que criou um grupo "DBReadUser" no seu Anúncio Azure. Os utilizadores pertencentes a esse grupo poderão agora inscrever-se na base de dados como este utilizador.

## <a name="next-steps"></a>Passos seguintes

* Reveja os conceitos globais para [autenticação de Diretório Ativo Azure com Base de Dados Azure para PostgreSQL - Servidor Único](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
