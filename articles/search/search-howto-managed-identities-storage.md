---
title: Criar uma ligação a uma conta de armazenamento usando uma identidade gerida
titleSuffix: Azure Cognitive Search
description: Saiba como configurar uma ligação indexante a uma conta de Armazenamento Azure usando uma identidade gerida
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: f26ca04955dfa854a8ee17b7aa255a6ed991b8df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358376"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity"></a>Configurar uma ligação a uma conta do Armazenamento do Microsoft Azure através de uma identidade gerida

Esta página descreve como configurar uma ligação indexante a uma conta de armazenamento Azure usando uma identidade gerida em vez de fornecer credenciais na cadeia de ligação de objetos de fonte de dados.

Antes de aprender mais sobre esta funcionalidade, recomenda-se que tenha uma compreensão do que é um indexante e como configurar um indexante para a sua fonte de dados. Mais informações podem ser encontradas nos seguintes links:
* [Descrição geral do Indexador](search-indexer-overview.md)
* [Indexador Azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Indexador Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexador de tabela azure](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>Configurar a ligação

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - Ligue a identidade gerida atribuída pelo sistema

Quando uma identidade gerida atribuída ao sistema está ativada, a Azure cria uma identidade para o seu serviço de pesquisa que pode ser usado para autenticar outros serviços Azure dentro do mesmo inquilino e subscrição. Em seguida, pode utilizar esta identidade em atribuições de controlo de acesso baseado em funções Azure (Azure RBAC) que permitem o acesso aos dados durante a indexação.

![Ligue o sistema atribuído identidade gerida](./media/search-managed-identities/turn-on-system-assigned-identity.png "Ligue o sistema atribuído identidade gerida")

Depois de **selecionar, verá** um ID de objeto que foi atribuído ao seu serviço de pesquisa.

![ID do Objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID do Objeto")
 
### <a name="2---add-a-role-assignment"></a>2 - Adicionar uma atribuição de função

Neste passo, você dará ao seu serviço de Pesquisa Cognitiva Azure permissão para ler dados da sua conta de armazenamento.

1. No portal Azure, navegue para a conta de Armazenamento que contém os dados que pretende indexar.
2. Selecione **Controlo de acesso (IAM)**
3. **Selecione Adicionar** e adicionar a **atribuição de função**

    ![Adicionar atribuição de função](./media/search-managed-identities/add-role-assignment-storage.png "Adicionar atribuição de função")

4. Selecione as funções adequadas com base no tipo de conta de armazenamento que gostaria de indexar:
    1. O armazenamento Azure Blob requer que adicione o seu serviço de pesquisa à função **de Leitor de Dados de Armazenamento Blob.**
    1. A Azure Data Lake Storage Gen2 requer que adicione o seu serviço de pesquisa à função **de Leitor de Dados de Armazenamento Blob.**
    1. O armazenamento da tabela Azure requer que adicione o seu serviço de pesquisa à função **Reader e Data Access.**
5.  Deixar **Atribuir acesso a** utilizador, grupo ou principal de serviço **Azure**
6.  Procure o seu serviço de pesquisa, selecione-o e, em seguida, **selecione Guardar**

    Exemplo para o armazenamento de Azure Blob e Azure Data Lake Storage Gen2:

    ![Adicionar atribuição de função de leitor de dados blob de armazenamento](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Adicionar atribuição de função de leitor de dados blob de armazenamento")

    Exemplo para o armazenamento da mesa Azure:

    ![Adicionar atribuição de função de leitor e acesso a dados](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Adicionar atribuição de função de leitor e acesso a dados")

### <a name="3---create-the-data-source"></a>3 - Criar a fonte de dados

A [API REST,](/rest/api/searchservice/create-data-source)o portal Azure e o [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) suportam a cadeia de ligação de identidade gerida. Abaixo está um exemplo de como criar uma fonte de dados para indexar dados a partir de uma conta de armazenamento usando a [API REST](/rest/api/searchservice/create-data-source) e uma cadeia de conexão de identidade gerida. O formato de cadeia de ligação de identidade gerida é o mesmo para a API REST, .NET SDK e o portal Azure.

Ao indexar a partir de uma conta de armazenamento, a fonte de dados deve ter as seguintes propriedades necessárias:

* **nome** é o nome único da fonte de dados dentro do seu serviço de pesquisa.
* **tipo**
    * Armazenamento Azure Blob: `azureblob`
    * Armazenamento da mesa Azure: `azuretable`
    * Azure Data Lake Storage Gen2: **tipo** será fornecido assim que se inscrever para a pré-visualização usando [este formulário](https://aka.ms/azure-cognitive-search/mi-preview-request).
* **credenciais**
    * Ao utilizar uma identidade gerida para autenticar, o formato **de credenciais** é diferente do que quando não se utiliza uma identidade gerida. Aqui irá fornecer um ResourceId que não tem chave de conta ou senha. O ResourceId deve incluir o ID de subscrição da conta de armazenamento, o grupo de recursos da conta de armazenamento e o nome da conta de armazenamento.
    * Formato de identidade gerido: 
        * *ResourceId=/subscrições/**o seu ID** de subscrição /grupos de recursos/ o nome do grupo de **recursos**/fornecedores/Microsoft.Storage/storageAcounts/ o nome da sua conta **de armazenamento**/;*
* **O recipiente** especifica um recipiente ou nome de mesa na sua conta de armazenamento. Por predefinição, todas as bolhas dentro do recipiente são recuperáveis. Se quiser apenas indexar bolhas num determinado diretório virtual, pode especificar esse diretório utilizando o parâmetro de **consulta** opcional.

Exemplo de como criar um objeto de origem de dados blob utilizando a [API REST](/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

### <a name="4---create-the-index"></a>4 - Criar o índice

O índice especifica os campos num documento, atributos e outras construções que moldam a experiência de pesquisa.

Aqui está como criar um índice com um `content` campo pesquisável para armazenar o texto extraído de bolhas:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

Para obter mais informações sobre a criação de índices, consulte [Criar Índice](/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 - Criar o indexador

Um indexante conecta uma fonte de dados com um índice de pesquisa de alvo, e fornece um horário para automatizar a atualização de dados.

Uma vez criado o índice e a fonte de dados, está pronto para criar o indexante.

Definição de indexante de exemplo para um indexador blob:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Este indexante será executado de duas em duas horas (o intervalo de horário está definido para "PT2H"). Para executar um indexante a cada 30 minutos, desajuste o intervalo para "PT30M". O intervalo suportado mais curto é de 5 minutos. O horário é opcional - se omitido, um indexante funciona apenas uma vez quando é criado. No entanto, pode executar um indexante a qualquer momento.   

Para obter mais detalhes sobre a API do Indexante Create, consulte [Create Indexer](/rest/api/searchservice/create-indexer).

Para obter mais informações sobre a definição de horários de indexantes, consulte [Como agendar indexadores para a Pesquisa Cognitiva do Azure](search-howto-schedule-indexers.md).

## <a name="accessing-secure-data-in-storage-accounts"></a>Aceder a dados seguros em contas de armazenamento

As contas de armazenamento Azure podem ser mais protegidas usando firewalls e redes virtuais. Se pretender indexar o conteúdo a partir de uma conta de armazenamento de bolhas ou de uma conta de armazenamento do Data Lake Gen2 que esteja protegida através de uma firewall ou rede virtual, siga as instruções para [aceder aos dados nas contas de armazenamento de forma segura através de uma exceção de serviço fidedigna](search-indexer-howto-access-trusted-service-exception.md).

## <a name="see-also"></a>Ver também

Saiba mais sobre os indexantes de armazenamento Azure:

* [Indexador Azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Indexador Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexador de tabela azure](search-howto-indexing-azure-tables.md)