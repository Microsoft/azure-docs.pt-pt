---
title: Saiba como proteger o acesso aos dados no Azure Cosmos DB
description: Saiba mais sobre os conceitos de controle de acesso no Azure Cosmos DB, incluindo chaves mestras, chaves somente leitura, usuários e permissões.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: ec32dfbe9aac13a9b3c08922b73b9a23d668d744
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73152282"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Proteger o acesso aos dados no Azure Cosmos DB

Este artigo fornece uma visão geral de como proteger o acesso aos dados armazenados no [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos DB usa dois tipos de chaves para autenticar usuários e fornecer acesso a seus dados e recursos. 

|Tipo de chave|Recursos|
|---|---|
|[Chaves mestras](#master-keys) |Usado para recursos administrativos: contas, bancos de dados, usuários e permissões.|
|[Tokens de recurso](#resource-tokens)|Usado para recursos de aplicativo: contêineres, documentos, anexos, procedimentos armazenados, gatilhos e UDFs|

<a id="master-keys"></a>

## <a name="master-keys"></a>Chaves mestras 

As chaves mestras fornecem acesso a todos os recursos administrativos da conta do banco de dados. Chaves mestras:  
- Forneça acesso a contas, bancos de dados, usuários e permissões. 
- Não pode ser usado para fornecer acesso granular a contêineres e documentos.
- São criados durante a criação de uma conta.
- Pode ser regenerado a qualquer momento.

Cada conta consiste em duas chaves mestras: uma chave primária e uma chave secundária. A finalidade das chaves duplas é que você pode regenerar ou lançar chaves, fornecendo acesso contínuo à sua conta e aos dados. 

Além das duas chaves mestras para a conta de Cosmos DB, há duas chaves somente leitura. Essas chaves somente leitura permitem apenas operações de leitura na conta. Chaves somente leitura não fornecem acesso a recursos de permissões de leitura.

As chaves mestras primária, secundária, somente leitura e de leitura/gravação podem ser recuperadas e geradas novamente usando o portal do Azure. Para obter instruções, consulte [Exibir, copiar e regenerar chaves de acesso](manage-with-cli.md#regenerate-account-key).

![Controle de acesso (IAM) no portal do Azure-demonstrando a segurança do banco de dados NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

O processo de rotação de sua chave mestra é simples. Navegue até o portal do Azure para recuperar a chave secundária e substitua a chave primária pela sua chave secundária em seu aplicativo e, em seguida, gire a chave primária na portal do Azure.

![Rotação de chave mestra no portal do Azure-demonstrando a segurança do banco de dados NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Exemplo de código para usar uma chave mestra

O exemplo de código a seguir ilustra como usar um ponto de extremidade de conta Cosmos DB e uma chave mestra para instanciar um DocumentClient e criar um banco de dados. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Tokens de recurso

Os tokens de recurso fornecem acesso aos recursos do aplicativo em um banco de dados. Tokens de recurso:
- Forneça acesso a contêineres específicos, chaves de partição, documentos, anexos, procedimentos armazenados, gatilhos e UDFs.
- São criados quando um [usuário](#users) recebe [permissões](#permissions) para um recurso específico.
- São recriadas quando um recurso de permissão é acionado pela chamada POST, GET ou PUT.
- Use um token de recurso de hash especificamente construído para o usuário, recurso e permissão.
- Os tempos são associados a um período de validade personalizável. O TimeSpan válido padrão é de uma hora. O tempo de vida do token, no entanto, pode ser especificado explicitamente, até um máximo de cinco horas.
- Forneça uma alternativa segura para fornecer a chave mestra. 
- Permitir que os clientes leiam, gravem e excluam recursos na conta de Cosmos DB de acordo com as permissões concedidas.

Você pode usar um token de recurso (criando Cosmos DB usuários e permissões) quando desejar fornecer acesso aos recursos em sua conta de Cosmos DB para um cliente que não pode ser confiável com a chave mestra.  

Cosmos DB tokens de recurso fornecem uma alternativa segura que permite que os clientes leiam, gravem e excluam recursos em sua conta de Cosmos DB de acordo com as permissões que você concedeu e sem a necessidade de uma chave mestra ou somente leitura.

Este é um padrão de design típico no qual tokens de recurso podem ser solicitados, gerados e entregues aos clientes:

1. Um serviço de camada intermediária é configurado para atender a um aplicativo móvel para compartilhar fotos do usuário. 
2. O serviço de camada intermediária possui a chave mestra da conta de Cosmos DB.
3. O aplicativo de fotos é instalado em dispositivos móveis do usuário final. 
4. No logon, o aplicativo de fotos estabelece a identidade do usuário com o serviço de camada intermediária. Esse mecanismo de estabelecimento de identidade é puramente para o aplicativo.
5. Depois que a identidade é estabelecida, o serviço de camada intermediária solicita permissões com base na identidade.
6. O serviço de camada intermediária envia um token de recurso de volta para o aplicativo de telefone.
7. O aplicativo de telefone pode continuar a usar o token de recurso para acessar diretamente Cosmos DB recursos com as permissões definidas pelo token de recurso e para o intervalo permitido pelo token de recurso. 
8. Quando o token de recurso expira, as solicitações subsequentes recebem uma exceção de 401 não autorizado.  Neste ponto, o aplicativo de telefone restabelece a identidade e solicita um novo token de recurso.

    ![Fluxo de trabalho de tokens de recurso Azure Cosmos DB](./media/secure-access-to-data/resourcekeyworkflow.png)

A geração e o gerenciamento de tokens de recursos são tratados pelas bibliotecas de cliente do Cosmos DB nativo; no entanto, se você usar o REST, deverá construir os cabeçalhos de solicitação/autenticação. Para obter mais informações sobre como criar cabeçalhos de autenticação para REST, consulte [controle de acesso em Cosmos DB recursos](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) ou o [código-fonte para nossos SDKs](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Para obter um exemplo de um serviço de camada intermediária usado para gerar ou tokens de recurso do agente, consulte o [aplicativo ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Utilizadores
Cosmos DB usuários estão associados a um banco de dados Cosmos.  Cada banco de dados pode conter zero ou mais Cosmos DB usuários.  O exemplo de código a seguir mostra como criar um Cosmos DB recurso de usuário.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Cada usuário Cosmos DB tem uma propriedade PermissionsLink que pode ser usada para recuperar a lista de [permissões](#permissions) associadas ao usuário.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Permissões
Um Cosmos DB recurso de permissão está associado a um usuário Cosmos DB.  Cada usuário pode conter zero ou mais permissões de Cosmos DB.  Um recurso de permissão fornece acesso a um token de segurança que o usuário precisa ao tentar acessar um recurso de aplicativo específico.
Há dois níveis de acesso disponíveis que podem ser fornecidos por um recurso de permissão:

* Todos: o usuário tem permissão total no recurso.
* Leitura: o usuário só pode ler o conteúdo do recurso, mas não pode executar operações de gravação, atualização ou exclusão no recurso.

> [!NOTE]
> Para executar Cosmos DB procedimentos armazenados, o usuário deve ter a permissão All no contêiner no qual o procedimento armazenado será executado.
> 
> 

### <a name="code-sample-to-create-permission"></a>Exemplo de código para criar permissão

O exemplo de código a seguir mostra como criar um recurso de permissão, ler o token de recurso do recurso de permissão e associar as permissões com o [usuário](#users) criado acima.

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

Se você tiver especificado uma chave de partição para sua coleção, a permissão para coleta, documento e recursos de anexo também deverá incluir o ResourcePartitionKey além do ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Exemplo de código para ler permissões para o usuário

Para obter facilmente todos os recursos de permissão associados a um usuário específico, o Cosmos DB disponibiliza um feed de permissão para cada objeto de usuário.  O trecho de código a seguir mostra como recuperar a permissão associada ao usuário criado acima, construir uma lista de permissões e criar uma instância de um novo DocumentClient em nome do usuário.

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

## <a name="add-users-and-assign-roles"></a>Adicionar usuários e atribuir funções

Para adicionar Azure Cosmos DB acesso de leitor de conta à sua conta de usuário, faça com que o proprietário da assinatura execute as etapas a seguir na portal do Azure.

1. Abra o portal do Azure e selecione sua conta de Azure Cosmos DB.
2. Clique na guia **controle de acesso (iam)** e, em seguida, clique em **+ Adicionar atribuição de função**.
3. No painel **Adicionar atribuição de função** , na caixa **função** , selecione **Cosmos DB função de leitor de conta**.
4. Na **caixa atribuir acesso a**, selecione **usuário, grupo ou aplicativo do Azure ad**.
5. Selecione o usuário, o grupo ou o aplicativo no diretório ao qual você deseja conceder acesso.  Você pode pesquisar o diretório por nome de exibição, endereço de email ou identificadores de objeto.
    O usuário, grupo ou aplicativo selecionado aparece na lista Membros selecionados.
6. Clique em **Guardar**.

A entidade agora pode ler Azure Cosmos DB recursos.

## <a name="delete-or-export-user-data"></a>Excluir ou exportar dados do usuário
Azure Cosmos DB permite pesquisar, selecionar, modificar e excluir qualquer dado pessoal localizado no banco de dados ou em coleções. O Azure Cosmos DB fornece APIs para localizar e excluir dados pessoais no entanto, é sua responsabilidade usar as APIs e definir a lógica necessária para apagar os dados pessoais. Cada API multimodelo (SQL, MongoDB, Gremlin, Cassandra, tabela) fornece diferentes SDKs de linguagem que contêm métodos para pesquisar e excluir dados pessoais. Você também pode habilitar o recurso [TTL (vida útil)](time-to-live.md) para excluir dados automaticamente após um período especificado, sem incorrer em nenhum custo adicional.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a segurança do banco de dados Cosmos, consulte [Cosmos DB: segurança do banco de dados](database-security.md).
* Para saber como construir Azure Cosmos DB tokens de autorização, consulte [controle de acesso em Azure Cosmos DB recursos](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
