---
title: Configurar e entrar com o Azure AD para o banco de dados do Azure para PostgreSQL-servidor único
description: Saiba mais sobre como configurar o Azure Active Directory para autenticação com o banco de dados do Azure para PostgreSQL-servidor único
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d5abfe4cc6aa0679d8009343fa24c1059700bb79
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516035"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Usar Azure Active Directory para autenticação com PostgreSQL

Este artigo explicará as etapas de como configurar o acesso a Azure Active Directory com o banco de dados do Azure para PostgreSQL e como se conectar usando um token do Azure AD.

> [!IMPORTANT]
> A autenticação do Azure AD para o banco de dados do Azure para PostgreSQL está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Configurando o usuário administrador do Azure AD

Somente um usuário administrador do Azure AD pode criar/habilitar usuários para autenticação baseada no Azure AD. Para criar o e o usuário administrador do Azure AD, siga as etapas a seguir

1. Na portal do Azure, selecione a instância do banco de dados do Azure para PostgreSQL que você deseja habilitar para o Azure AD.
2. Em configurações, selecione administrador de Active Directory:

![definir administrador do Azure AD][2]

3. Selecione um usuário válido do Azure AD no locatário do cliente para ser administrador do Azure AD.

> [!IMPORTANT]
> Ao definir o administrador, um novo usuário é adicionado ao banco de dados do Azure para servidor PostgreSQL com permissões de administrador completo. O usuário administrador do Azure AD no banco de dados do Azure para PostgreSQL terá a função `azure_ad_admin`.

Somente um administrador do Azure AD pode ser criado por servidor PostgreSQL e a seleção de outro substituirá o administrador do Azure AD existente configurado para o servidor. Você pode especificar um grupo do Azure AD em vez de um usuário individual para ter vários administradores. Observe que, em seguida, você entrará com o nome do grupo para fins de administração.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Criando usuários do Azure AD no banco de dados do Azure para PostgreSQL

Para adicionar um usuário do Azure AD ao banco de dados do Azure para PostgreSQL, execute as etapas a seguir após a conexão (consulte a seção mais adiante sobre como se conectar):

1. Primeiro, verifique se o usuário do Azure AD `<user>@yourtenant.onmicrosoft.com` é um usuário válido no locatário do Azure AD.
2. Entre na instância do banco de dados do Azure para PostgreSQL como o usuário administrador do Azure AD.
3. Criar `<user>@yourtenant.onmicrosoft.com` de função no banco de dados do Azure para PostgreSQL.
4. Torne `<user>@yourtenant.onmicrosoft.com` um membro da função azure_ad_user. Isso só deve ser dado aos usuários do Azure AD.

**Exemplo:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> A autenticação de um usuário por meio do Azure AD não concede ao usuário nenhuma permissão para acessar objetos no banco de dados do Azure para PostgreSQL. Você deve conceder ao usuário as permissões necessárias manualmente.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Criando grupos do Azure AD no banco de dados do Azure para PostgreSQL

Para habilitar um grupo do Azure AD para acesso ao seu banco de dados, use o mesmo mecanismo para os usuários, mas em vez disso, especifique o nome do Grupo:

**Exemplo:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Ao fazer logon, os membros do grupo usarão seus tokens de acesso pessoal, mas assinarão com o nome do grupo especificado como o nome de usuário.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Conectando-se ao banco de dados do Azure para PostgreSQL usando o Azure AD

O diagrama de alto nível a seguir resume o fluxo de trabalho do uso da autenticação do Azure AD com o banco de dados do Azure para PostgreSQL:

![Fluxo de autenticação][1]

Criamos a integração do Azure AD para trabalhar com ferramentas PostgreSQL comuns, como psql, que não reconhecem o Azure AD e só dão suporte à especificação de nome de usuário e senha ao se conectar ao PostgreSQL. Passamos o token do Azure AD como a senha, conforme mostrado na imagem acima.

No momento, testamos os seguintes clientes:

- linha de comando do psql (utilize a variável PGPASSWORD para passar o token, veja abaixo)
- Azure Data Studio (usando a extensão PostgreSQL)
- Outros clientes baseados em libpq (por exemplo, estruturas de aplicativo comuns e ORMs)

> [!NOTE]
> Lembre-se de que o uso do token do Azure AD com pgAdmin não tem suporte no momento, pois ele tem uma limitação embutida de 256 caracteres para senhas (que o token excede).

Estas são as etapas que um usuário/aplicativo precisará fazer a autenticação com o Azure AD descrito abaixo:

### <a name="step-1-authenticate-with-azure-ad"></a>Etapa 1: autenticar com o Azure AD

Verifique se você tem o [CLI do Azure instalado](/cli/azure/install-azure-cli).

Invoque a ferramenta de CLI do Azure para autenticar com o Azure AD. Ele exige que você forneça a ID de usuário e a senha do Azure AD.

```
az login
```

Esse comando abrirá uma janela do navegador na página de autenticação do Azure AD.

> [!NOTE]
> Você também pode usar Azure Cloud Shell para executar essas etapas.
> Lembre-se de que, ao recuperar o token de acesso do Azure AD no Azure Cloud Shell você precisará chamar explicitamente `az login` e entrar novamente (na janela separada com um código). Depois disso, o comando `get-access-token` funcionará conforme o esperado.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Etapa 2: recuperar o token de acesso do Azure AD

Invoque a ferramenta de CLI do Azure para adquirir um token de acesso para o usuário autenticado do Azure AD da etapa 1 para acessar o banco de dados do Azure para PostgreSQL.

Exemplo (para nuvem pública):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

O valor do recurso acima deve ser especificado exatamente como mostrado. Para outras nuvens, o valor do recurso pode ser pesquisado usando:

```shell
az cloud show
```

Para CLI do Azure versão 2.0.71 e posterior, o comando pode ser especificado na seguinte versão mais conveniente para todas as nuvens:

```shell
az account get-access-token --resource-type oss-rdbms
```

Depois que a autenticação for bem-sucedida, o Azure AD retornará um token de acesso:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

O token é uma cadeia de caracteres base 64 que codifica todas as informações sobre o usuário autenticado e que é direcionado para o serviço banco de dados do Azure para PostgreSQL.

> [!NOTE]
> A validade do token de acesso é de qualquer lugar entre 5 minutos e 60 minutos. Recomendamos que você obtenha o token de acesso logo antes de iniciar o logon no banco de dados do Azure para PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Etapa 3: usar o token como senha para fazer logon com PostgreSQL

Ao se conectar, você precisa usar o token de acesso como a senha de usuário do PostgreSQL.

Ao usar o cliente de linha de comando `psql`, o token de acesso precisa ser passado por meio da variável de ambiente `PGPASSWORD`, já que o token de acesso excede o comprimento da senha que `psql` pode aceitar diretamente:

Exemplo do Windows:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Exemplo de Linux/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Agora você pode iniciar uma conexão com o banco de dados do Azure para PostgreSQL como faria normalmente:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres"
```

Agora você está autenticado no servidor PostgreSQL usando a autenticação do Azure AD.

## <a name="token-validation"></a>Validação de token

A autenticação do Azure AD no banco de dados do Azure para PostgreSQL garante que o usuário exista no servidor PostgreSQL e verifica a validade do token Validando o conteúdo do token. As seguintes etapas de validação de token são executadas:

-   O token é assinado pelo Azure AD e não foi violado
-   O token foi emitido pelo Azure AD para o locatário associado ao servidor
-   O token não expirou
-   O token é para o recurso de banco de dados do Azure para PostgreSQL (e não outro recurso do Azure)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migrando usuários do PostgreSQL existentes para a autenticação baseada no Azure AD

Você pode habilitar a autenticação do Azure AD para usuários existentes. Há dois casos a serem considerados:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Caso 1: o nome de usuário PostgreSQL corresponde ao nome UPN do Azure AD

No caso improvável de que os usuários existentes já correspondam aos nomes de usuário do Azure AD, você pode conceder à função `azure_ad_user` para eles a fim de habilitá-los para a autenticação do Azure AD:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Agora, eles poderão entrar com as credenciais do Azure AD em vez de usar sua senha de usuário do PostgreSQL configurada anteriormente.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Caso 2: o nome de usuário PostgreSQL é diferente do nome UPN do Azure AD

Se um usuário PostgreSQL não existir no Azure AD ou tiver um nome de usuário diferente, você poderá usar os grupos do Azure AD para se autenticar como este usuário PostgreSQL. Você pode migrar os usuários existentes do banco de dados do Azure para PostgreSQL para o Azure AD criando um grupo do Azure AD com um nome que corresponda ao usuário PostgreSQL e, em seguida, concedendo a função azure_ad_user ao usuário PostgreSQL existente:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Isso pressupõe que você criou um grupo "DBReadUser" no Azure AD. Os usuários que pertencem a esse grupo agora poderão entrar no banco de dados como esse usuário.

## <a name="next-steps"></a>Passos seguintes

* Examine os conceitos gerais para [Azure Active Directory autenticação com o banco de dados do Azure para PostgreSQL-servidor único](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
