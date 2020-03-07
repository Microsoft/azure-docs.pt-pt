---
title: Saiba como garantir o acesso aos dados no Azure Cosmos DB
description: Conheça conceitos de controlo de acesso em Azure Cosmos DB, incluindo chaves principais, chaves de leitura, utilizadores e permissões.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 448b14168e85e75b7ed19e189600186ce11c2902
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395499"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Acesso seguro aos dados em Azure Cosmos DB

Este artigo fornece uma visão geral de garantir o acesso aos dados armazenados no [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

A Azure Cosmos DB utiliza dois tipos de chaves para autenticar os utilizadores e fornecer acesso aos seus dados e recursos. 

|Tipo de chave|Recursos|
|---|---|
|[Chaves principais](#master-keys) |Utilizados para recursos administrativos: contas de base de dados, bases de dados, utilizadores e permissões|
|[Fichas de recurso](#resource-tokens)|Utilizados para recursos de aplicação: contentores, documentos, anexos, procedimentos armazenados, gatilhos e UDFs|

<a id="master-keys"></a>

## <a name="master-keys"></a>Chaves principais

As chaves principais fornecem acesso a todos os recursos administrativos para a conta de base de dados. Chaves principais:

- Fornecer acesso a contas, bases de dados, utilizadores e permissões. 
- Não pode ser utilizado para fornecer acesso granular a recipientes e documentos.
- São criados durante a criação de uma conta.
- Pode ser regenerado a qualquer momento.

Cada conta é constituída por duas teclas Master: uma chave primária e uma chave secundária. O objetivo das teclas duplas é que possa regenerar ou rodar chaves, proporcionando acesso contínuo à sua conta e dados.

Além das duas chaves principais para a conta Cosmos DB, existem duas chaves só para leitura. Estas chaves só de leitura permitem a leitura de operações na conta. As chaves de leitura não dão acesso aos recursos de permissões de leitura.

As teclas principais, secundárias, apenas lidas e de leitura podem ser recuperadas e regeneradas através do portal Azure. Para obter instruções, consulte [Ver, copiar e regenerar as teclas](manage-with-cli.md#regenerate-account-key)de acesso .

![Controlo de acesso (IAM) no portal Azure - demonstrando segurança na base de dados NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

O processo de rotação da sua chave principal é simples. Navegue até ao portal Azure para recuperar a sua chave secundária e, em seguida, substitua a chave primária pela chave secundária na sua aplicação e, em seguida, rode a chave primária no portal Azure.

![Rotação de chave principal no portal Azure - demonstrando segurança na base de dados NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Amostra de código para usar uma chave principal

A amostra de código que se segue ilustra como usar um ponto final de conta Cosmos DB e chave master para instantaneamente um DocumentClient e criar uma base de dados.

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

## Fichas de recurso<a id="resource-tokens"></a>

Os tokens de recursos fornecem acesso aos recursos de aplicação dentro de uma base de dados. Fichas de recurso:

- Fornecer acesso a recipientes específicos, chaves de partição, documentos, anexos, procedimentos armazenados, gatilhos e UDFs.
- São criados quando um [utilizador](#users) recebe [permissões](#permissions) a um recurso específico.
- São recriados quando um recurso de permissão é acionado por POST, GET ou PUT call.
- Utilize um token de recurso hash especificamente construído para o utilizador, recurso e permissão.
- O tempo está ligado a um período de validade personalizável. O tempo válido padrão é de uma hora. Token lifetime, no entanto, pode ser explicitamente especificado, até um máximo de cinco horas.
- Fornecer uma alternativa segura para dar a chave principal.
- Permitir que os clientes leiam, escrevam e apaguem recursos na conta Cosmos DB de acordo com as permissões que lhes foram concedidas.

Você pode usar um token de recursos (criando utilizadores e permissões Cosmos DB) quando você quer fornecer acesso a recursos na sua conta Cosmos DB a um cliente que não pode ser confiado com a chave principal.  

Os tokens de recursos cosmos DB fornecem uma alternativa segura que permite aos clientes ler, escrever e apagar recursos na sua conta Cosmos DB de acordo com as permissões que concedeu, e sem necessidade de um mestre ou ler apenas a chave.

Aqui está um padrão de design típico pelo qual fichas de recursos podem ser solicitadas, geradas e entregues aos clientes:

1. Um serviço de nível médio é criado para servir uma aplicação móvel para partilhar fotos de utilizadores.
2. O serviço de nível médio possui a chave principal da conta Cosmos DB.
3. A aplicação fotográfica está instalada em dispositivos móveis de utilizador final.
4. No login, a aplicação fotográfica estabelece a identidade do utilizador com o serviço de nível médio. Este mecanismo de estabelecimento de identidade está puramente à altura da aplicação.
5. Uma vez estabelecida a identidade, o serviço de nível médio solicita permissões com base na identidade.
6. O serviço de nível médio envia um recurso para a aplicação do telefone.
7. A aplicação do telefone pode continuar a usar o token de recurso para aceder diretamente aos recursos da Cosmos DB com as permissões definidas pelo token de recurso e para o intervalo permitido pelo token de recursos.
8. Quando o token de recurso expirar, os pedidos subsequentes recebem uma exceção não autorizada de 401.  Neste momento, a aplicação telefónica restabelece a identidade e solicita um novo recurso.

    ![O fluxo de trabalho do recurso Azure Cosmos DB tokens](./media/secure-access-to-data/resourcekeyworkflow.png)

A geração e gestão de recursos é tratada pelas bibliotecas de clientes nativas da Cosmos DB; no entanto, se utilizar o REST, deve construir os cabeçalhos de pedido/autenticação. Para obter mais informações sobre a criação de cabeçalhos de autenticação para REST, consulte [O Controlo de Acesso nos Recursos DB cosmos](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) ou o código fonte para o nosso [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/AuthorizationHelper.cs) ou [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts).

Para um exemplo de um serviço de nível médio usado para gerar ou intermediar tokens de recursos, consulte a [aplicação ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

## Utilizadores<a id="users"></a>

Os utilizadores da Azure Cosmos DB estão associados a uma base de dados da Cosmos.  Cada base de dados pode conter zero ou mais utilizadores de Db Cosmos. A amostra de código que se segue mostra como criar um utilizador cosmos DB utilizando o [Azure Cosmos DB .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement).

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Cada utilizador da Cosmos DB tem um método ReadAsync() que pode ser usado para recuperar a lista de [permissões associadas](#permissions) ao utilizador.

## Permissões<a id="permissions"></a>

Um recurso de permissão está associado a um utilizador e atribuído no recipiente, bem como ao nível da chave de divisória. Cada utilizador pode conter zero ou mais permissões. Um recurso de permissão fornece acesso a um símbolo de segurança que o utilizador precisa ao tentar aceder a um recipiente ou dados específicos numa chave de partição específica. Existem dois níveis de acesso disponíveis que podem ser fornecidos por um recurso de permissão:

- Tudo: O utilizador tem permissão total sobre o recurso.
- Leia Também: O utilizador só pode ler o conteúdo do recurso, mas não pode efetuar a escrita, a atualização ou a eliminação de operações no recurso.

> [!NOTE]
> Para executar os procedimentos armazenados, o utilizador deve ter a permissão All no recipiente em que o procedimento armazenado será executado.

### <a name="code-sample-to-create-permission"></a>Amostra de código para criar permissão

A amostra de código que se segue mostra como criar um recurso de permissão, ler o sinal de recurso do recurso de permissão e associar as permissões com o [utilizador](#users) acima criado.

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>Amostra de código para ler permissão para utilizador

O seguinte código de snippet mostra como recuperar a permissão associada ao utilizador criada acima e instantaneamente um novo CosmosClient em nome do utilizador, com uma única chave de partição.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Adicione utilizadores e atribua funções

Para adicionar o acesso do leitor de conta Azure Cosmos DB à sua conta de utilizador, faça com que um proprietário de subscrição execute os seguintes passos no portal Azure.

1. Abra o portal Azure e selecione a sua conta Azure Cosmos DB.
2. Clique no separador controlo de **acesso (IAM)** e, em seguida, clique **em + Adicionar a atribuição de funções**.
3. No painel de atribuição de **funções Add,** na caixa **Role,** selecione **Cosmos DB Account Reader Role**.
4. No **acesso atribuído à caixa**, selecione **utilizador, grupo ou aplicação Azure AD**.
5. Selecione o utilizador, grupo ou aplicação no seu diretório a que deseja conceder acesso.  Pode pesquisar o diretório por nome de exibição, endereço de e-mail ou identificadores de objetos.
    O utilizador, grupo ou aplicação selecionados aparece na lista de membros selecionados.
6. Clique em **Guardar**.

A entidade pode agora ler os recursos da Azure Cosmos DB.

## <a name="delete-or-export-user-data"></a>Eliminar ou exportar dados dos utilizadores

O Azure Cosmos DB permite-lhe pesquisar, selecionar, modificar e eliminar quaisquer dados pessoais localizados na base de dados ou coleções. O Azure Cosmos DB fornece APIs para encontrar e apagar dados pessoais no entanto, é da sua responsabilidade usar as APIs e definir a lógica necessária para apagar os dados pessoais. Cada API multi-modelo (SQL, MongoDB, Gremlin, Cassandra, Table) fornece diferentes SDKs linguísticos que contêm métodos para pesquisar e eliminar dados pessoais. Também pode ativar a funcionalidade de [tempo para viver (TTL)](time-to-live.md) para eliminar os dados automaticamente após um período determinado, sem incorrer em qualquer custo adicional.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a segurança da base de dados cosmos, consulte a segurança da Base de [Dados Cosmos DB.](database-security.md)
- Para aprender a construir fichas de autorização da Azure Cosmos DB, consulte [o Access Control na Azure Cosmos DB Resources](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
- Amostras de gestão de utilizadores com utilizadores e permissões, [.NET SDK v3 amostras](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs) de gestão de utilizadores
