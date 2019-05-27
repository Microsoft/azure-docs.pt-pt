---
title: Saiba como proteger o acesso aos dados no Azure Cosmos DB
description: Saiba mais sobre conceitos de controlo de acesso no Azure Cosmos DB, incluindo o mestre de chaves, chaves só de leitura, os utilizadores e permissões.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 6ccb9afb528edf67b29c781217ca1de97b81e017
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969212"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Acesso seguro a dados no Azure Cosmos DB

Este artigo fornece uma descrição geral da proteção de acesso a dados armazenados no [do Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

O Azure Cosmos DB utiliza dois tipos de chaves para autenticar os utilizadores e fornecer acesso aos seus dados e recursos. 

|Tipo de chave|Recursos|
|---|---|
|[Mestre de chaves](#master-keys) |Utilizado para recursos administrativos: contas, bases de dados, os utilizadores e permissões de base de dados|
|[Tokens de recursos](#resource-tokens)|Utilizado para os recursos de aplicação: contentores, documentos, anexos, procedimentos armazenados, acionadores e UDFs|

<a id="master-keys"></a>

## <a name="master-keys"></a>Mestre de chaves 

Chaves mestras fornecem acesso aos todos os administrativos recursos para a conta de base de dados. Mestre de chaves:  
- Fornece acesso a contas, bases de dados, os utilizadores e permissões. 
- Não pode ser utilizado para fornecer acesso granular aos contentores e documentos.
- São criados durante a criação de uma conta.
- Pode ser gerado novamente em qualquer altura.

Cada conta consiste em duas de mestre de chaves: uma chave primária e chave secundária. A finalidade das chaves duplas é, de modo a que pode voltar a gerar ou implementar chaves, fornecendo acesso contínuo a sua conta e dados. 

Além de duas chaves mestra para a conta do Cosmos DB, existem duas chaves só de leitura. Essas chaves só de leitura permitem apenas operações de leitura na conta. Chaves só de leitura não concede acesso a recursos de permissões de leitura.

Primária, secundária, só de leitura e chaves mestras de leitura e escrita podem ser obtidas e gerada novamente com o portal do Azure. Para obter instruções, consulte [ver, copiar e chaves de acesso de voltar a gerar](manage-with-cli.md#regenerate-account-key).

![Controlo de acesso (IAM) no portal do Azure - demonstração da segurança de base de dados NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

O processo de efetuar a rotação da chave mestra é simple. Navegue para o portal do Azure para obter a chave secundária, em seguida, substitua a chave primária com a chave secundária em seu aplicativo, em seguida, girar a chave primária no portal do Azure.

![Rotação de chave mestra no portal do Azure - demonstração da segurança de base de dados NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Exemplo de código para utilizar uma chave mestra

O exemplo de código a seguir ilustra como utilizar um ponto final da conta do Cosmos DB e a chave mestra para instanciar um DocumentClient e criar uma base de dados. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Tokens de recursos

Tokens de recursos fornecem acesso aos recursos do aplicativo dentro de uma base de dados. Tokens de recursos:
- Fornece acesso a contentores específicos, as chaves de partição, documentos, anexos, procedimentos armazenados, acionadores e UDFs.
- São criados quando um [usuário](#users) é concedido [permissões](#permissions) a um recurso específico.
- São recriadas quando um recurso de permissão é agiu pela chamada POST, GET ou PUT.
- Utilize um token de recurso de hash construído especificamente para o utilizador, o recurso e a permissão.
- São vinculado com um período de validade personalizável de tempo. O intervalo de tempo válido do padrão é de uma hora. Duração do token, no entanto, pode ser especificada explicitamente, até um máximo de cinco horas.
- Fornecem uma alternativa segura para fornecer a chave mestra. 
- Permitir que os clientes de ler, escrever e eliminar recursos na conta do Cosmos DB, de acordo com as permissões que tem sido concedidos.

Pode utilizar um token de recurso (ao criar o Cosmos DB utilizadores e permissões) que deseja fornecer acesso a recursos na sua conta do Cosmos DB para um cliente que não são de confiança com a chave mestra.  

Tokens de recurso do cosmos DB fornecem uma alternativa de segura que permite aos clientes ler, escrever e eliminar recursos na sua conta do Cosmos DB, de acordo com as permissões que tenha concedido e sem a necessidade de um principal ou a chave só de leitura.

Este é um padrão de design típico no qual os tokens de recursos podem ser solicitados, gerados e entregues aos clientes:

1. Um serviço de escalão médio é configurado para atender a um aplicativo móvel de compartilhar fotos do usuário. 
2. O serviço de escalão médio possui a chave mestra da conta do Cosmos DB.
3. O aplicativo de fotos que é instalado em dispositivos móveis do utilizador final. 
4. No início de sessão, o aplicativo de fotos que estabelece a identidade do utilizador com o serviço de escalão médio. Esse mecanismo de estabelecimento de identidade é puramente até o aplicativo.
5. Assim que a identidade é estabelecida, o serviço de escalão médio pedidos de permissões com base na identidade.
6. O serviço de escalão médio envia um token de recursos para a aplicação de telefone.
7. A aplicação de telefone pode continuar a utilizar o token de recurso para acessar diretamente os recursos do Cosmos DB com as permissões definidas pelo token de recursos e para o intervalo permitido pelo token de recursos. 
8. Quando expira o token de recursos, as solicitações subseqüentes recebem uma exceção não autorizado 401.  Neste momento, a aplicação de telefone novamente estabelece a identidade e pede um token de recurso novo.

    ![Fluxo de trabalho de tokens de recursos do Azure Cosmos DB](./media/secure-access-to-data/resourcekeyworkflow.png)

Gestão e de geração de tokens de recurso é processado pelas bibliotecas de cliente do Cosmos DB nativas; No entanto, se usar o REST, deve construir os cabeçalhos de pedido/autenticação. Para obter mais informações sobre a criação de cabeçalhos de autenticação para o REST, consulte [controlo de acesso em recursos do Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) ou o [código-fonte para nossos SDKs](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Para obter um exemplo de um serviço de camada média utilizado para gerar ou Mediador de tokens de recursos, consulte a [ResourceTokenBroker aplicação](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Utilizadores
Os utilizadores do cosmos DB estão associados uma base de dados do Cosmos DB.  Cada base de dados pode conter zero ou mais utilizadores do Cosmos DB.  O exemplo de código seguinte mostra como criar um recurso de utilizador do Cosmos DB.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Cada utilizador do Cosmos DB tem uma propriedade de PermissionsLink que pode ser utilizada para obter a lista de [permissões](#permissions) associados ao utilizador.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Permissões
Um recurso de permissão do Cosmos DB está associado um utilizador do Cosmos DB.  Cada utilizador pode conter zero ou mais permissões do Cosmos DB.  Um recurso de permissão fornece acesso a um token de segurança que o usuário precisa ao tentar aceder a um recurso de aplicação específica.
Existem dois níveis de acesso disponíveis que podem ser fornecidos por um recurso de permissão:

* Todos os: O utilizador tem permissão total no recurso.
* Leitura: O utilizador só pode ler o conteúdo do recurso, mas não é possível efetuar a escrita, atualização ou operações de eliminação do recurso.

> [!NOTE]
> Para executar o Cosmos DB o utilizador tem de ter a permissão All no contentor no qual o procedimento armazenado será executado de procedimentos armazenados.
> 
> 

### <a name="code-sample-to-create-permission"></a>Exemplo de código para criar a permissão

O exemplo de código seguinte mostra como criar um recurso de permissão, o token de recursos do recurso de permissão de leitura e associar as permissões com o [utilizador](#users) criada acima.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = UriFactory.CreateDocumentCollectionUri("db", "collection"),
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Se especificar uma chave de partição para a sua coleção, em seguida, a permissão para coleção, documentos e de recursos de anexo também tem de incluir ResourcePartitionKey além do ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Exemplo de código para ler as permissões de utilizador

Para obter facilmente a permissão de todos os recursos associados a um utilizador específico, o Cosmos DB disponibilizam uma permissão de feed para cada objeto de utilizador.  O fragmento de código seguinte mostra como obter a permissão associada ao utilizador que criou acima, construir uma lista de permissão e instanciar um novo DocumentClient em nome do utilizador.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="add-users-and-assign-roles"></a>Adicionar utilizadores e atribuir funções

Para adicionar o acesso de leitor de conta do Azure Cosmos DB à sua conta de utilizador, ter um proprietário de subscrição, execute os seguintes passos no portal do Azure.

1. Abra o portal do Azure e selecione a sua conta do Azure Cosmos DB.
2. Clique nas **controlo de acesso (IAM)** separador e, em seguida, clique em **+ adicionar atribuição de função**.
3. Na **adicionar atribuição de função** painel, na **função** caixa, selecione **função de leitor de conta do Cosmos DB**.
4. Na **atribuir acesso a caixa**, selecione **utilizador do Azure AD, grupo ou aplicação**.
5. Selecione o utilizador, grupo ou aplicação no seu diretório ao qual pretende conceder acesso.  Pode procurar o diretório por nome a apresentar, endereço de e-mail ou identificadores de objetos.
    O utilizador selecionado, grupo ou aplicação é apresentada na lista de membros selecionados.
6. Clique em **Guardar**.

A entidade já pode ler os recursos do Azure Cosmos DB.

## <a name="delete-or-export-user-data"></a>Eliminar ou exportar dados de utilizador
O Azure Cosmos DB permite-lhe pesquisar, selecione, modificar e eliminar quaisquer dados pessoais, localizados na base de dados ou coleções. O Azure Cosmos DB fornece APIs para localizar e eliminar os dados pessoais no entanto, é sua responsabilidade usar as APIs e definir a lógica necessária para apagar os dados pessoais. Cada API com vários modelo (SQL, MongoDB, Gremlin, Cassandra, tabela) fornece SDKs que contêm métodos para procurar e eliminar os dados pessoais de idioma diferente. Também pode ativar a [altura de live (TTL)](time-to-live.md) funcionalidade para eliminar os dados automaticamente após um período especificado, sem incorrer em quaisquer custos adicionais.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre a segurança de base de dados do Cosmos DB, veja [do Cosmos DB: Segurança de base de dados](database-security.md).
* Para saber como construir tokens de autorização do Azure Cosmos DB, veja [controlo de acesso em recursos do Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
