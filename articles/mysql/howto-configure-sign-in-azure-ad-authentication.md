---
title: Utilizar o Diretório Ativo Azure - Base de Dados Azure para MySQL
description: Saiba como configurar o Azure Ative Directory (Azure AD) para autenticação com base de dados Azure para MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 1fa34deaa12400a164602d38b6b2d349a64850c6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652248"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Utilize o Diretório Ativo Azure para autenticação com o MySQL

Este artigo irá acompanhá-lo através dos passos como configurar o acesso ao Diretório Ativo Azure com a Base de Dados Azure para o MySQL, e como se conectar usando um token Azure AD.

## <a name="setting-the-azure-ad-admin-user"></a>Definição do utilizador azure AD Admin

Apenas um utilizador da AD Admin Azure pode criar/ativar os utilizadores para a autenticação baseada em AD Azure. Para criar e azure AD Admin utilizador, por favor siga os seguintes passos

1. No portal Azure, selecione a instância de Base de Dados Azure para MySQL que pretende ativar para AD Azure.
2. Em Definições, selecione Ative Directory Admin:

![definir administrador de anúncios azure][2]

3. Selecione um utilizador AD Azure válido no inquilino do cliente para administrador da Azure AD.

> [!IMPORTANT]
> Ao definir o administrador, um novo utilizador é adicionado à Base de Dados Azure para o servidor MySQL com permissões completas do administrador.

Apenas um administrador Azure AD pode ser criado por servidor MySQL e a seleção de outro irá substituir o atual administrador azure AD configurado para o servidor.

Numa versão futura, apoiaremos a especificação de um grupo Azure AD em vez de um utilizador individual ter vários administradores, no entanto, atualmente ainda não é suportado.

Depois de configurar o administrador, pode agora iniciar o seu insessão:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Ligação à Base de Dados Azure para MySQL utilizando AD Azure

O diagrama de alto nível que se segue resume o fluxo de trabalho da utilização da autenticação Azure AD com base de dados Azure para MySQL:

![fluxo de autenticação][1]

Desenhamos a integração da AD Azure para trabalhar com ferramentas mysql comuns como o mysql CLI, que não são conscientes do Azure AD e apenas suportam especificar o nome de utilizador e a palavra-passe quando se conectam ao MySQL. Passamos o token Azure AD como a palavra-passe como mostrado na imagem acima.

Testámos atualmente os seguintes clientes:

- Bancada MySQLWorkbench 
- Mysql CLI

Testámos também os controladores de aplicações mais comuns, pode ver detalhes no final desta página.

Estes são os passos que um utilizador/aplicação terá de fazer autenticar com a Azure AD descrita abaixo:

### <a name="step-1-authenticate-with-azure-ad"></a>Passo 1: Autenticar com AD Azure

Certifique-se de que tem o [Azure CLI instalado](/cli/azure/install-azure-cli).

Invoque a ferramenta Azure CLI para autenticar com AD Azure. Requer que forneça o seu ID de utilizador da AD Azure e a palavra-passe.

```
az login
```

Este comando lançará uma janela de navegador para a página de autenticação da AD Azure.

> [!NOTE]
> Também pode utilizar a Azure Cloud Shell para realizar estes passos.
> Tenha em atenção que ao recuperar o token de acesso azure AD na Casca de Nuvem Azure terá de ligar `az login` e iniciar sessão (na janela separada com um código). Depois do sinal no `get-access-token` comando funcionará como esperado.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Passo 2: Ficha de acesso a AD Da Recuperação Azure

Invoque a ferramenta Azure CLI para adquirir um token de acesso para o utilizador autenticado Azure AD a partir do passo 1 para aceder à Base de Dados Azure para MySQL.

Exemplo (para Nuvem Pública):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

O valor acima referido deve ser especificado exatamente como indicado. Para outras nuvens, o valor do recurso pode ser procurado usando:

```shell
az cloud show
```

Para a versão 2.0.71 do Azure CLI e posteriormente, o comando pode ser especificado na seguinte versão mais conveniente para todas as nuvens:

```shell
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

O símbolo é uma cadeia Base 64 que codifica todas as informações sobre o utilizador autenticado, e que é direcionada para a Base de Dados Azure para o serviço MySQL.

> [!NOTE]
> A validade do token de acesso é entre 5 minutos e 60 minutos. Recomendamos que obtenha o sinal de acesso antes de iniciar o login na Base de Dados Azure para MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Passo 3: Utilize o símbolo como senha para iniciar sessão com o MySQL

Ao ligar, tem de utilizar o token de acesso como senha de utilizador MySQL. Ao utilizar clientes GUI como o MySQLWorkbench, pode utilizar o método acima para recuperar o símbolo. 

Ao utilizar o CLI, pode utilizar esta mão curta para ligar: 

**Exemplo (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Note a definição de "enable-cleartext-plugin" – é necessário utilizar uma configuração semelhante com outros clientes para se certificar de que o token é enviado para o servidor sem ser hashed.

Agora está autenticado no seu servidor MySQL utilizando a autenticação Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Criação de utilizadores de Anúncios Azure na Base de Dados Azure para mySQL

Para adicionar um utilizador Azure AD à sua base de dados Azure para base de dados MySQL, execute os seguintes passos após a ligação (ver secção posterior sobre como ligar):

1. Em primeiro lugar, certifique-se de que o utilizador da AD Azure `<user>@yourtenant.onmicrosoft.com` é um utilizador válido no inquilino Da Azure AD.
2. Inscreva-se na sua Base de Dados Azure para a instância MySQL como utilizador do Anúncio AD Azure.
3. Crie o utilizador na Base de `<user>@yourtenant.onmicrosoft.com` Dados Azure para o MySQL.

**Exemplo:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Para nomes de utilizador que excedam 32 caracteres, recomenda-se que utilize um pseudónimo para ser utilizado ao ligar: 

Exemplo:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> A autenticação de um utilizador através do Azure AD não dá ao utilizador quaisquer permissões para aceder a objetos dentro da base de dados Azure para base de dados MySQL. Deve conceder manualmente ao utilizador as permissões necessárias.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Criação de grupos de AD Azure em Base de Dados Azure para MySQL

Para permitir um grupo DeA Azure para acesso à sua base de dados, utilize o mesmo mecanismo que para os utilizadores, mas em vez disso especifique o nome de grupo:

**Exemplo:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Ao iniciar sessão, os membros do grupo usarão as suas fichas de acesso pessoal, mas assinarão com o nome de grupo especificado como nome de utilizador.

## <a name="token-validation"></a>Validação de Fichas

A autenticação Azure AD na Base de Dados Azure para o MySQL garante que o utilizador existe no servidor MySQL, e verifica a validade do token validando o conteúdo do símbolo. São executadas as seguintes etapas de validação simbólica:

-   Token é assinado pela Azure AD e não foi adulterado
-   Token foi emitida pela Azure AD para o inquilino associado ao servidor
-   Token não expirou
-   Token é para a Base de Dados Azure para o recurso MySQL (e não outro recurso Azure)

## <a name="compatibility-with-application-drivers"></a>Compatibilidade com os condutores de candidaturas

A maioria dos condutores são suportados, no entanto certifique-se de usar as definições para enviar a palavra-passe em texto claro, para que o token seja enviado sem modificação.

* C/C++
  * libmysqlclient: Suportado
  * mysql-connector-c++: Suportado
* Java
  * Conector/J (mysql-connector-java): Suportado, deve utilizar `useSSL` a definição
* Python
  * Conector/Python: Suportado
* Ruby
  * mysql2: Suportado
* .NET
  * mysql-conector-net: Suportado, precisa adicionar plugin para mysql_clear_password
  * mysql-net/MySqlConnector: Suportado
* Node.js
  * mysqljs: Não suportado (não envia token em texto claro sem patch)
  * nó-mysql2: Suportado
* Perl
  * DBD::mysql: Suportado
  * Net::MySQL: Não suportado
* Ir
  * go-sql-driver: Suportado, adicione `?tls=true&allowCleartextPasswords=true` à cadeia de ligação

## <a name="next-steps"></a>Passos seguintes

* Reveja os conceitos globais para [autenticação de Diretório Ativo Azure com Base de Dados Azure para MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
