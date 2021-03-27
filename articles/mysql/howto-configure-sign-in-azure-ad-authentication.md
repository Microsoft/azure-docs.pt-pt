---
title: Use diretório ativo Azure - Base de Dados Azure para MySQL
description: Saiba como configurar o Azure Ative Directory (Azure AD) para autenticação com Azure Database for MySQL
author: sunilagarwal
ms.author: sunila
ms.service: mysql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: 808c3589ba5b51b035ccc8165489c4d11203dd66
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612233"
---
# <a name="use-azure-active-directory-for-authentication-with-mysql"></a>Utilize o Diretório Ativo Azure para autenticação com o MySQL

Este artigo irá acompanhá-lo através dos passos como configurar o acesso do Azure Ative Directory com a Azure Database para o MySQL, e como se conectar usando um token AD Azure.

> [!IMPORTANT]
> A autenticação do Azure Ative Directory só está disponível para o MySQL 5.7 e mais recente.

## <a name="setting-the-azure-ad-admin-user"></a>Definição do utilizador Azure AD Admin

Apenas um utilizador Admin AD AD Azure pode criar/permitir aos utilizadores a autenticação baseada em Azure. Para criar um utilizador Azure AD Ad, siga os seguintes passos

1. No portal Azure, selecione a instância da Base de Dados Azure para o MySQL que pretende ativar para Azure AD.
2. Em Definições, selecione Ative Directory Admin:

![definir administrador de anúncios azure][2]

3. Selecione um utilizador Azure AD válido no inquilino do cliente para ser administrador da AD Azure.

> [!IMPORTANT]
> Ao definir o administrador, um novo utilizador é adicionado à Base de Dados Azure para o servidor MySQL com permissões completas de administrador.

Apenas um administrador AD AD Azure pode ser criado por servidor MySQL e a seleção de outro substituirá o admin AD Ading existente configurado para o servidor.

Depois de configurar o administrador, pode agora iniciar singso:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Ligação à Base de Dados Azure para o MySQL utilizando a Azure AD

O seguinte diagrama de alto nível resume o fluxo de trabalho da utilização da autenticação AD Azure com base de dados Azure para o MySQL:

![fluxo de autenticação][1]

Concebemos a integração AD AZure para trabalhar com ferramentas comuns do MySQL como o Mysql CLI, que não estão cientes do AZURE AD e apenas suportam especificar o nome de utilizador e a palavra-passe ao ligar-se ao MySQL. Passamos o token AD AZure como a palavra-passe como mostrado na imagem acima.

Atualmente testámos os seguintes clientes:

- MySQLWorkbench 
- Mysql CLI

Testámos também os controladores de aplicações mais comuns, pode ver detalhes no final desta página.

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

Invoque a ferramenta Azure CLI para adquirir um token de acesso para o utilizador autenticado Azure AD a partir do passo 1 para aceder à Base de Dados Azure para o MySQL.

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

O token é uma cadeia base 64 que codifica todas as informações sobre o utilizador autenticado, e que é direcionada para a Base de Dados Azure para o serviço MySQL.

> [!NOTE]
> A validade do sinal de acesso está entre 5 minutos e 60 minutos. Recomendamos que obtenha o token de acesso pouco antes de iniciar o login na Base de Dados Azure para o MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Passo 3: Use o token como senha para iniciar sessão com o MySQL

Ao ligar, precisa de utilizar o token de acesso como palavra-passe do utilizador MySQL. Ao utilizar clientes GUI como mySQLWorkbench, pode utilizar o método acima para recuperar o token. 

Ao utilizar o CLI, pode utilizar esta mão curta para ligar: 

**Exemplo (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Considerações importantes na ligação:

* `user@tenant.onmicrosoft.com` é o nome do utilizador ou grupo AZure AD que está a tentar ligar como
* Apecimos sempre o nome do servidor após o nome de utilizador/grupo AZure AD (por `@mydb` exemplo)
* Certifique-se de que utiliza a forma exata como o utilizador AD AD ou o nome de grupo do Azure está escrito
* O utilizador azure AD e os nomes de grupo são sensíveis a casos
* Ao ligar em grupo, utilize apenas o nome de grupo (por `GroupName@mydb` exemplo)
* Se o nome contiver espaços, use `\` antes de cada espaço para escapar

Note a definição "enable-cleartext-plugin" – é necessário utilizar uma configuração semelhante com outros clientes para garantir que o token é enviado para o servidor sem ser hashed.

Está agora autenticado no seu servidor MySQL utilizando a autenticação AZure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Criar utilizadores de AD Azure na Base de Dados Azure para o MySQL

Para adicionar um utilizador AD Azure à sua base de dados Azure para a base de dados MySQL, execute os seguintes passos após a ligação (ver secção posterior sobre como ligar):

1. Em primeiro lugar, certifique-se de que o utilizador Azure AD `<user>@yourtenant.onmicrosoft.com` é um utilizador válido no inquilino AZure AD.
2. Inscreva-se na sua Base de Dados Azure para o caso MySQL como utilizador do Azure AD Admin.
3. Crie o utilizador `<user>@yourtenant.onmicrosoft.com` na Base de Dados Azure para o MySQL.

**Exemplo:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Para nomes de utilizador que excedam 32 caracteres, recomenda-se que utilize um pseudónimo, para ser usado ao ligar: 

Exemplo:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Autenticar um utilizador através do Azure AD não dá ao utilizador quaisquer permissões para aceder a objetos dentro da Base de Dados Azure para a base de dados MySQL. Deve conceder ao utilizador as permissões necessárias manualmente.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Criar grupos AD Azure em Azure Database para MySQL

Para permitir o acesso a um grupo Azure AD para aceder à sua base de dados, utilize o mesmo mecanismo que os utilizadores, mas em vez disso especifique o nome do grupo:

**Exemplo:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Ao iniciar sessão, os membros do grupo utilizarão os seus tokens de acesso pessoal, mas assinarão com o nome de grupo especificado como nome de utilizador.

## <a name="token-validation"></a>Validação simbólica

A autenticação AZure AD na Base de Dados Azure para o MySQL garante que o utilizador existe no servidor MySQL, verificando a validade do token validando o conteúdo do token. São realizadas as seguintes etapas de validação simbólicas:

-   Token é assinado pela Azure AD e não foi adulterado
-   Token foi emitido pela Azure AD para o inquilino associado ao servidor
-   Token não expirou
-   Token é para a Base de Dados Azure para o recurso MySQL (e não outro recurso Azure)

## <a name="compatibility-with-application-drivers"></a>Compatibilidade com condutores de aplicações

A maioria dos controladores são suportados, no entanto certifique-se de usar as definições para enviar a palavra-passe em texto claro, para que o token seja enviado sem modificação.

* C/C++
  * libmysqlclient: Suportado
  * mysql-conector-c++: Suportado
* Java
  * Conector/J (mysql-connector-java): Suportado, deve utilizar `useSSL` a definição
* Python
  * Conector/Python: Suportado
* Ruby
  * mysql2: Suportado
* .NET
  * mysql-connector-net: Suportado, precisa adicionar plugin para mysql_clear_password
  * mysql-net/MySqlConnector: Suportado
* Node.js
  * mysqljs: Não suportado (não envia ficha em texto claro sem patch)
  * nó-mysql2: Suportado
* Perl
  * DBD::mysql: Suportado
  * Net::MySQL: Não suportado
* Go
  * go-sql-driver: Suportado, adicionar `?tls=true&allowCleartextPasswords=true` à cadeia de conexão

## <a name="next-steps"></a>Passos seguintes

* Reveja os conceitos globais de [autenticação do Azure Ative Directory com Azure Database for MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
