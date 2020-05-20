---
title: Criar uma ligação a uma base de dados Azure SQL utilizando uma identidade gerida (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Saiba como configurar uma ligação indexante a uma base de dados Azure SQL utilizando uma identidade gerida (pré-visualização)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 87389651707a3bdcc18ae7eb03b88681b5303c4d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664803"
---
# <a name="set-up-an-indexer-connection-to-an-azure-sql-database-using-a-managed-identity-preview"></a>Criar uma ligação indexante a uma base de dados Azure SQL utilizando uma identidade gerida (pré-visualização)

> [!IMPORTANT] 
> O suporte para a criação de uma ligação a uma fonte de dados utilizando uma identidade gerida encontra-se atualmente numa pré-visualização pública fechada. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção.
> Pode solicitar acesso à pré-visualização preenchendo [este formulário](https://aka.ms/azure-cognitive-search/mi-preview-request).

Esta página descreve como configurar uma ligação indexante a uma base de dados Azure SQL usando uma identidade gerida em vez de fornecer credenciais na cadeia de ligação de objetos de origem de dados.

Antes de saber mais sobre esta funcionalidade, recomenda-se que tenha uma compreensão do que é um indexante e de como configurar um indexador para a sua fonte de dados. Mais informações podem ser encontradas nos seguintes links:
* [Descrição geral do Indexador](search-indexer-overview.md)
* [Indexador do Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Configurar uma ligação usando uma identidade gerida

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - Ativar a identidade gerida atribuída pelo sistema

Quando uma identidade gerida atribuída ao sistema é ativada, o Azure cria uma identidade para o seu serviço de pesquisa que pode ser usado para autenticar outros serviços Azure dentro do mesmo inquilino e subscrição. Em seguida, pode utilizar esta identidade em atribuições de controlo de acesso (RBAC) baseadas em papéis que permitem o acesso aos dados durante a indexação.

![Ativar o sistema atribuído identidade gerida](./media/search-managed-identities/turn-on-system-assigned-identity.png "Ativar o sistema atribuído identidade gerida")

Depois de **selecionar,** verá um ID do Objeto que foi atribuído ao seu serviço de pesquisa.

![ID de objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID de objeto")
 
### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2 - Provision Azure Ative Directory Admin para SQL Server

Ao ligar-se à base de dados no próximo passo, terá de se conectar com uma conta Azure Ative Directory (Azure AD) que tenha acesso à base de dados para que o seu serviço de pesquisa tenha permissão para aceder à base de dados.

Siga as instruções [aqui](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) para dar à sua conta Azure AD acesso à base de dados.

### <a name="3---assign-the-search-service-permissions"></a>3 - Atribuir as permissões do serviço de pesquisa

Siga os passos abaixo para atribuir a permissão do serviço de pesquisa para ler a base de dados.

1. Ligue-se ao Estúdio Visual

    ![Ligue-se ao Estúdio Visual](./media/search-managed-identities/connect-with-visual-studio.png "Ligue-se ao Estúdio Visual")

2. Autenticar com a sua conta Azure AD

    ![Autenticar](./media/search-managed-identities/visual-studio-authentication.png "Autenticar")

3. Executar os seguintes comandos:

    Inclua os suportes em torno do seu nome de serviço de pesquisa.
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![Nova consulta](./media/search-managed-identities/visual-studio-new-query.png "Nova consulta")

    ![Executar consulta](./media/search-managed-identities/visual-studio-execute-query.png "Executar consulta")

>[!NOTE]
> Se a identidade do serviço de pesquisa do passo 1 for alterada após completar este passo, então deve remover a adesão à função e remover o utilizador na base de dados SQL, em seguida, adicionar as permissões novamente, completando novamente o passo 3.
> A remoção da função e do utilizador pode ser realizada executando os seguintes comandos:
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4 - Adicionar uma atribuição de funções

Neste passo, você dará ao seu serviço de Pesquisa Cognitiva Azure permissão para ler dados do seu Servidor SQL.

1. No portal Azure navegue para a página do Servidor Azure SQL.
2. Selecione **controlo de acesso (IAM)**
3. **Selecione Adicionar** e adicionar **a atribuição de funções**

    ![Adicionar atribuição de função](./media/search-managed-identities/add-role-assignment-sql-server.png "Adicionar atribuição de função")

4. Selecione a função de **Leitor** apropriada.
5. Deixe **a atribuição de acesso ao** utilizador, grupo ou diretor de serviço da **Azure AD**
6. Procure o seu serviço de pesquisa, selecione-o e, em seguida, selecione **Guardar**

    ![Adicionar atribuição de papel do leitor](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Adicionar atribuição de papel do leitor")

### <a name="5---create-the-data-source"></a>5 - Criar a fonte de dados

Ao indexar a partir de uma base de dados SQL, a fonte de dados deve ter as seguintes propriedades necessárias:

* **o nome** é o nome único da fonte de dados dentro do seu serviço de pesquisa.
* **tipo** é`azuresql`
* **credenciais**
    * Quando se utiliza uma identidade gerida para autenticar, o formato **de credenciais** é diferente do que quando não se utiliza uma identidade manhosa. Aqui irá fornecer um nome de Catálogo Inicial ou Base de Dados e um ResourceId que não tem chave de conta ou senha. O ResourceId deve incluir o ID de subscrição da base de dados Azure SQL, o grupo de recursos da base de dados SQL e o nome da base de dados SQL. 
    * Formato de cadeia de ligação de identidade gerido:
        * *Catálogo Inicial. Base de dados=**nome da base de dados;** ResourceId=/subscrições/ o id /recurso De**subscriçãoGroup/****o nome do grupo de recursos**/fornecedores/Microsoft.Sql/servidores/ o seu nome de Servidor**SQL**/; Tempo de tempo de**ligação= tempo de tempo de ligação;***
* **o recipiente** especifica o nome da tabela ou vista que gostaria de indexar.

Exemplo de como criar um objeto de origem de dados Azure SQL utilizando a [API REST:](https://docs.microsoft.com/rest/api/searchservice/create-data-source)

```
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

O portal Azure e o [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) também suportam a cadeia de ligação de identidades geridas. O portal Azure requer uma bandeira de características que lhe será fornecida ao inscrever-se para a pré-visualização utilizando o link na parte superior desta página. 

### <a name="6---create-the-index"></a>6 - Criar o índice

O índice especifica os campos num documento, atributos e outras construções que moldam a experiência de pesquisa.

Aqui está como criar um índice com um campo pesquisável: `booktitle`   

```
POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
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

Para mais informações sobre a criação de índices, consulte [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="7---create-the-indexer"></a>7 - Criar o indexador

Um indexante liga uma fonte de dados a um índice de pesquisa de alvo, e fornece um horário para automatizar a atualização de dados.

Uma vez criado o índice e a fonte de dados, está pronto para criar o indexante.

Definição indexante de exemplo para um indexador Azure SQL:

```
POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Este indexante funcionará de duas em duas horas (o intervalo de horário está definido para "PT2H"). Para executar um indexante a cada 30 minutos, detete o intervalo para "PT30M". O intervalo suportado mais curto é de 5 minutos. O horário é opcional - se omitido, um indexante funciona apenas uma vez quando é criado. No entanto, pode executar um indexer a qualquer momento.   

Para mais detalhes sobre a API Create Indexer, consulte [create indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre a definição de horários indexantes, consulte [como agendar indexadores para pesquisa cognitiva azure](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver um erro quando o indexante tenta ligar-se à fonte de dados que diz que o cliente não está autorizado a aceder ao servidor, veja os [erros comuns](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting)do indexante .

## <a name="see-also"></a>Ver também

Saiba mais sobre o indexador Azure SQL:
* [Indexador do Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
