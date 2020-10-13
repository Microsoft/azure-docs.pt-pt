---
title: Saiba como garantir o acesso aos dados no Azure Cosmos DB
description: Saiba mais sobre conceitos de controlo de acesso em Azure Cosmos DB, incluindo chaves primárias, chaves só de leitura, utilizadores e permissões.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 30444523bfc26fc0f4eb410957bcc9ee46aff725
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760874"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Acesso seguro aos dados no Azure Cosmos DB

Este artigo fornece uma visão geral de garantir o acesso aos dados armazenados no [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

A Azure Cosmos DB utiliza dois tipos de chaves para autenticar os utilizadores e fornecer acesso aos seus dados e recursos. 

|Tipo de chave|Recursos|
|---|---|
|[Chaves primárias](#primary-keys) |Utilizado para recursos administrativos: contas de base de dados, bases de dados, utilizadores e permissões|
|[Fichas de recursos](#resource-tokens)|Utilizados para recursos de aplicação: contentores, documentos, anexos, procedimentos armazenados, gatilhos e UDFs|

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Chaves primárias

As chaves primárias fornecem acesso a todos os recursos administrativos da conta de base de dados. Chaves primárias:

- Fornecer acesso a contas, bases de dados, utilizadores e permissões. 
- Não é possível utilizar acesso granular a contentores e documentos.
- São criados durante a criação de uma conta.
- Pode ser regenerado a qualquer momento.

Cada conta é composta por duas chaves primárias: uma chave primária e uma chave secundária. O objetivo das chaves duplas é que possa regenerar, ou rolar chaves, proporcionando acesso contínuo à sua conta e dados.

Além das duas chaves primárias da conta cosmos DB, existem duas chaves só de leitura. Estas chaves só de leitura permitem a leitura das operações na conta. As teclas só de leitura não fornecem acesso aos recursos de permissões de leitura.

As chaves primárias, secundárias, lidas apenas e as teclas primárias de leitura podem ser recuperadas e regeneradas usando o portal Azure. Para obter instruções, consulte [ver, copiar e regenerar as teclas de acesso](manage-with-cli.md#regenerate-account-key).

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Controlo de acesso (IAM) no portal Azure - demonstrando segurança na base de dados NoSQL":::

### <a name="key-rotation"></a>Rotação de chaves<a id="key-rotation"></a>

O processo de rotação da sua chave primária é simples. 

1. Navegue até ao portal Azure para recuperar a sua chave secundária.
2. Substitua a sua chave primária pela sua chave secundária na sua aplicação. Certifique-se de que todos os clientes da Cosmos DB em todas as implementações são prontamente reiniciados e começarão a usar a chave atualizada.
3. Rode a chave primária no portal Azure.
4. Validar as novas obras da chave primária contra todos os recursos. O processo de rotação da chave pode demorar qualquer coisa de menos de um minuto a horas, dependendo do tamanho da conta Cosmos DB.
5. Substitua a tecla secundária pela nova chave primária.

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Controlo de acesso (IAM) no portal Azure - demonstrando segurança na base de dados NoSQL" border="false":::

### <a name="code-sample-to-use-a-primary-key"></a>Amostra de código para usar uma chave primária

A seguinte amostra de código ilustra como usar um ponto final de conta Cosmos DB e chave primária para instantaneaizar um DocumentoClient e criar uma base de dados:

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

A seguinte amostra de código ilustra como usar o ponto final da conta DB Azure Cosmos e a chave primária para instantaneaizar um `CosmosClient` objeto:

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="resource-tokens"></a>Fichas de recursos <a id="resource-tokens"></a>

Os tokens de recursos fornecem acesso aos recursos da aplicação dentro de uma base de dados. Fichas de recursos:

- Fornecer acesso a recipientes específicos, chaves de partição, documentos, anexos, procedimentos armazenados, gatilhos e UDFs.
- São criados quando um [utilizador](#users) recebe [permissões](#permissions) a um recurso específico.
- São recriados quando um recurso de permissão é agido por CORREIO, GET ou PUT call.
- Utilize um token de recurso de haxixe especificamente construído para o utilizador, recurso e permissão.
- O tempo está ligado a um período de validade personalizável. O tempo de tempo válido predefinido é de uma hora. No entanto, a vida útil simbólica pode ser explicitamente especificada, até um máximo de cinco horas.
- Fornecer uma alternativa segura para dar a chave primária.
- Permitir que os clientes leiam, escrevam e apaguem recursos na conta do Cosmos DB de acordo com as permissões que lhes foram concedidas.

Pode utilizar um token de recursos (criando utilizadores e permissões Cosmos DB) quando pretender fornecer acesso a recursos na sua conta Cosmos DB a um cliente que não pode ser confiado à chave principal.  

Os tokens de recursos da Cosmos DB fornecem uma alternativa segura que permite aos clientes ler, escrever e apagar recursos na sua conta Cosmos DB de acordo com as permissões que concedeu, e sem necessidade de uma chave primária ou apenas lida.

Aqui está um padrão de design típico pelo qual tokens de recursos podem ser solicitados, gerados e entregues aos clientes:

1. É criado um serviço de nível médio para servir uma aplicação móvel para partilhar fotos do utilizador.
2. O serviço de nível médio possui a chave primária da conta Cosmos DB.
3. A aplicação fotográfica está instalada em dispositivos móveis de utilizador final.
4. No login, a aplicação de fotos estabelece a identidade do utilizador com o serviço de nível médio. Este mecanismo de estabelecimento identitário está puramente à altura da aplicação.
5. Uma vez estabelecida a identidade, o serviço de nível médio solicita permissões com base na identidade.
6. O serviço de nível médio envia um token de recurso de volta para a aplicação do telefone.
7. A aplicação do telefone pode continuar a usar o token de recursos para aceder diretamente aos recursos da Cosmos DB com as permissões definidas pelo token de recursos e para o intervalo permitido pelo token de recursos.
8. Quando o token de recurso expira, os pedidos subsequentes recebem uma exceção não autorizada 401.  Neste momento, a aplicação telefónica restabelece a identidade e solicita um novo token de recursos.

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Controlo de acesso (IAM) no portal Azure - demonstrando segurança na base de dados NoSQL" border="false":::

A geração e gestão de token de recursos é gerida pelas bibliotecas de clientes nativos da Cosmos DB; no entanto, se utilizar REST deve construir os cabeçalhos pedido/autenticação. Para obter mais informações sobre a criação de cabeçalhos de autenticação para REST, consulte [o Controlo de Acesso nos Recursos DB da Cosmos](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) ou o código fonte para o nosso [SDK .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/Authorization/AuthorizationHelper.cs) ou [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts).

Para um exemplo de um serviço de nível médio utilizado para gerar ou intermediar fichas de recursos, consulte a [aplicação ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

## <a name="users"></a>Utilizadores<a id="users"></a>

Os utilizadores DB da Azure Cosmos estão associados a uma base de dados cosmos.  Cada base de dados pode conter zero ou mais utilizadores de Cosmos DB. A seguinte amostra de código mostra como criar um utilizador cosmos DB usando o [Azure Cosmos DB .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement).

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Cada utilizador da Cosmos DB tem um método ReadAsync que pode ser usado para recuperar a lista de [permissões associadas](#permissions) ao utilizador.

## <a name="permissions"></a>Permissões<a id="permissions"></a>

Um recurso de permissão está associado a um utilizador e atribuído ao recipiente, bem como ao nível da chave de partição. Cada utilizador pode conter zero ou mais permissões. Um recurso de permissão fornece acesso a um símbolo de segurança de que o utilizador necessita ao tentar aceder a um recipiente ou dados específicos numa chave de partição específica. Existem dois níveis de acesso disponíveis que podem ser fornecidos por um recurso de permissão:

- Tudo: O utilizador tem total permissão sobre o recurso.
- Leia: O utilizador só pode ler o conteúdo do recurso mas não pode executar a escrita, atualização ou apagar operações no recurso.

> [!NOTE]
> Para executar os procedimentos armazenados, o utilizador deve ter a permissão de Todas as permissões no recipiente em que o procedimento armazenado será executado.

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

### <a name="code-sample-to-read-permission-for-user"></a>Amostra de código para ler permissão para o utilizador

O seguinte corte de código mostra como recuperar a permissão associada ao utilizador acima criada e instantaneamente um novo CosmosClient em nome do utilizador, traçado para uma única chave de partição.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Adicione utilizadores e atribua funções

Para adicionar o acesso do leitor de conta Azure Cosmos dB à sua conta de utilizador, tenha um proprietário de subscrição a realizar os seguintes passos no portal Azure.

1. Abra o portal Azure e selecione a sua conta DB Azure Cosmos.
2. Clique no separador **Controlo de Acesso (IAM)** e, em seguida, clique  **em + Adicionar a atribuição de função**.
3. No painel de atribuição de **funções Add,** na caixa **Role,** selecione **Cosmos DB Account Reader Role**.
4. No **acesso à caixa ,** selecione **utilizador AD Azure, grupo ou aplicação**.
5. Selecione o utilizador, grupo ou aplicação no seu diretório ao qual deseja conceder acesso.  Pode pesquisar o diretório pelo nome de exibição, endereço de e-mail ou identificadores de objetos.
    O utilizador, grupo ou aplicação selecionados aparece na lista de membros selecionados.
6. Clique em **Guardar**.

A entidade pode agora ler os recursos da Azure Cosmos DB.

## <a name="delete-or-export-user-data"></a>Eliminar ou exportar dados dos utilizadores

O Azure Cosmos DB permite-lhe pesquisar, selecionar, modificar e eliminar quaisquer dados pessoais localizados na base de dados ou recolhas. A Azure Cosmos DB fornece APIs para encontrar e apagar dados pessoais no entanto, é sua responsabilidade usar as APIs e definir a lógica necessária para apagar os dados pessoais. Cada API multi-modelo (SQL, MongoDB, Gremlin, Cassandra, Table) fornece diferentes SDKs linguísticos que contêm métodos para pesquisar e eliminar dados pessoais. Também pode ativar a [funcionalidade de tempo de vida (TTL)](time-to-live.md) para apagar automaticamente os dados após um período especificado, sem incorrer em qualquer custo adicional.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a segurança da base de dados [cosmos, consulte a segurança da Cosmos DB Database](database-security.md).
- Para aprender a construir fichas de autorização DB da Azure Cosmos, consulte [o Controlo de Acesso na Azure Cosmos DB Resources](/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
- Amostras de gestão de utilizadores com utilizadores e permissões, [.NET SDK v3 amostras de gestão de utilizadores](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
