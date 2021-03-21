---
title: Criar uma ligação à Base de Dados Azure SQL utilizando uma identidade gerida
titleSuffix: Azure Cognitive Search
description: Saiba como configurar uma ligação indexante à Base de Dados Azure SQL utilizando uma identidade gerida
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b940da2cf754e7e1cac91df6b517ecebe55e8c40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358427"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity"></a>Configurar uma ligação indexante à Base de Dados Azure SQL utilizando uma identidade gerida

Esta página descreve como configurar uma ligação indexante à Base de Dados Azure SQL usando uma identidade gerida em vez de fornecer credenciais na cadeia de ligação de objetos de fonte de dados.

Antes de aprender mais sobre esta funcionalidade, recomenda-se que tenha uma compreensão do que é um indexante e como configurar um indexante para a sua fonte de dados. Mais informações podem ser encontradas nos seguintes links:

* [Descrição geral do Indexador](search-indexer-overview.md)
* [Indexador do Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Criar uma ligação usando uma identidade gerida

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - Ligue a identidade gerida atribuída pelo sistema

Quando uma identidade gerida atribuída ao sistema está ativada, a Azure cria uma identidade para o seu serviço de pesquisa que pode ser usado para autenticar outros serviços Azure dentro do mesmo inquilino e subscrição. Em seguida, pode utilizar esta identidade em atribuições de controlo de acesso baseado em funções Azure (Azure RBAC) que permitem o acesso aos dados durante a indexação.

![Ligue o sistema atribuído identidade gerida](./media/search-managed-identities/turn-on-system-assigned-identity.png "Ligue o sistema atribuído identidade gerida")

Depois de **selecionar, verá** um ID de objeto que foi atribuído ao seu serviço de pesquisa.

![ID do Objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID do Objeto")

### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2 - Provision Azure Ative Directory Admin for SQL Server

Ao ligar-se à base de dados no passo seguinte, terá de se ligar a uma conta Azure Ative Directory (Azure AD) que tenha acesso à base de dados de forma a dar permissão ao seu serviço de pesquisa para aceder à base de dados.

Siga as instruções [aqui](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-database) para dar acesso à base de dados da sua conta Azure AD.

### <a name="3---assign-the-search-service-permissions"></a>3 - Atribuir as permissões do serviço de pesquisa

Siga os passos abaixo para atribuir a permissão do serviço de pesquisa para ler a base de dados.

1. Ligue-se ao Estúdio Visual

    ![Ligue-se ao Estúdio Visual](./media/search-managed-identities/connect-with-visual-studio.png "Ligue-se ao Estúdio Visual")

2. Autenticar com a sua conta AZure AD

    ![Autenticar](./media/search-managed-identities/visual-studio-authentication.png "Autenticar")

3. Execute os seguintes comandos:

    Inclua os suportes em torno do nome do seu serviço de pesquisa.
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![Nova consulta](./media/search-managed-identities/visual-studio-new-query.png "Nova consulta")

    ![Executar consulta](./media/search-managed-identities/visual-studio-execute-query.png "Executar consulta")

>[!NOTE]
> Se a identidade do serviço de pesquisa do passo 1 for alterada após completar este passo, então deve remover a função e remover o utilizador na base de dados SQL, em seguida, adicione novamente as permissões completando o passo 3 novamente.
> A remoção da função e do utilizador pode ser realizada executando os seguintes comandos:
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4 - Adicionar uma atribuição de função

Neste passo, você dará ao seu serviço de Pesquisa Cognitiva Azure permissão para ler dados do seu SqL Server.

1. No portal Azure navegue para a sua página do Servidor Azure SQL.
2. Selecione **Controlo de acesso (IAM)**
3. **Selecione Adicionar** e adicionar a **atribuição de função**

    ![Adicionar atribuição de função](./media/search-managed-identities/add-role-assignment-sql-server.png "Adicionar atribuição de função")

4. Selecione a função **de Leitor** apropriado.
5. Deixar **Atribuir acesso a** utilizador, grupo ou principal de serviço **Azure**
6. Procure o seu serviço de pesquisa, selecione-o e, em seguida, **selecione Guardar**

    ![Adicionar atribuição de função de leitor](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Adicionar atribuição de função de leitor")

### <a name="5---create-the-data-source"></a>5 - Criar a fonte de dados

A [API REST,](/rest/api/searchservice/create-data-source)o portal Azure e o [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) suportam a cadeia de ligação de identidade gerida. Abaixo está um exemplo de como criar uma fonte de dados para indexar dados a partir de uma Base de Dados Azure SQL usando a [API REST](/rest/api/searchservice/create-data-source) e uma cadeia de conexão de identidade gerida. O formato de cadeia de ligação de identidade gerida é o mesmo para a API REST, .NET SDK e o portal Azure.

Ao criar uma fonte de dados utilizando a [API REST,](/rest/api/searchservice/create-data-source)a fonte de dados deve ter as seguintes propriedades necessárias:

* **nome** é o nome único da fonte de dados dentro do seu serviço de pesquisa.
* **tipo** é `azuresql`
* **credenciais**
    * Ao utilizar uma identidade gerida para autenticar, o formato **de credenciais** é diferente do que quando não se usa uma identidade manged. Aqui irá fornecer um nome de Catálogo Inicial ou Base de Dados e um ResourceId que não tenha chave de conta ou senha. O ResourceId deve incluir o ID de subscrição da Base de Dados Azure SQL, o grupo de recursos da Base de Dados SQL e o nome da base de dados SQL. 
    * Formato de cadeia de ligação de identidade gerido:
        * *Catálogo Inicial| Base de **dados= nome da base de dados;** ResourceId=/subscrições/**o seu ID** de subscrição /grupos de recursos/ o nome do grupo de **recursos**/fornecedores/Microsoft.Sql/servers/ o seu **nome SQL Server**/; Intervalo de tempo de **ligação= tempo de intervalo de ligação**;*
* **o recipiente** especifica o nome da tabela ou vista que gostaria de indexar.

Exemplo de como criar um objeto de origem de dados Azure SQL utilizando a [API REST](/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

### <a name="6---create-the-index"></a>6 - Criar o índice

O índice especifica os campos num documento, atributos e outras construções que moldam a experiência de pesquisa.

Aqui está como criar um índice com um `booktitle` campo pesquisável:   

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Para obter mais informações sobre a criação de índices, consulte [Criar Índice](/rest/api/searchservice/create-index)

### <a name="7---create-the-indexer"></a>7 - Criar o indexador

Um indexante conecta uma fonte de dados com um índice de pesquisa de alvo, e fornece um horário para automatizar a atualização de dados.

Uma vez criado o índice e a fonte de dados, está pronto para criar o indexante.

Definição de indexante de exemplo para um indexador Azure SQL:

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Este indexante será executado de duas em duas horas (o intervalo de horário está definido para "PT2H"). Para executar um indexante a cada 30 minutos, desajuste o intervalo para "PT30M". O intervalo suportado mais curto é de 5 minutos. O horário é opcional - se omitido, um indexante funciona apenas uma vez quando é criado. No entanto, pode executar um indexante a qualquer momento.   

Para obter mais detalhes sobre a API do Indexante Create, consulte [Create Indexer](/rest/api/searchservice/create-indexer).

Para obter mais informações sobre a definição de horários de indexantes, consulte [Como agendar indexadores para a Pesquisa Cognitiva do Azure](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver um erro quando o indexante tenta ligar-se à fonte de dados que diz que o cliente não está autorizado a aceder ao servidor, dê uma olhada nos [erros comuns](./search-indexer-troubleshooting.md)do indexante .

## <a name="see-also"></a>Ver também

Saiba mais sobre o indexante Azure SQL:
* [Indexador do Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)