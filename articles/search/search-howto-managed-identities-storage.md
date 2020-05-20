---
title: Criar uma ligação a uma conta de armazenamento utilizando uma identidade gerida (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Saiba como configurar uma ligação indexante a uma conta de Armazenamento Azure utilizando uma identidade gerida (pré-visualização)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 1fa9581f1a7968a1bfd6df0fb82383dd45e70f54
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664607"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity-preview"></a>Criar uma ligação a uma conta de Armazenamento Azure utilizando uma identidade gerida (pré-visualização)

> [!IMPORTANT] 
> O suporte para a criação de uma ligação a uma fonte de dados utilizando uma identidade gerida encontra-se atualmente numa pré-visualização pública fechada. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção.
> Pode solicitar acesso à pré-visualização preenchendo [este formulário](https://aka.ms/azure-cognitive-search/mi-preview-request).

Esta página descreve como configurar uma ligação indexante a uma conta de armazenamento Azure usando uma identidade gerida em vez de fornecer credenciais na cadeia de ligação de objetos de origem de dados.

Antes de saber mais sobre esta funcionalidade, recomenda-se que tenha uma compreensão do que é um indexante e de como configurar um indexador para a sua fonte de dados. Mais informações podem ser encontradas nos seguintes links:
* [Descrição geral do Indexador](search-indexer-overview.md)
* [Indexador Azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Indexador de armazenamento de lagos Azure Data Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexador de tabela sinuosa](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>Configurar a ligação

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - Ativar a identidade gerida atribuída pelo sistema

Quando uma identidade gerida atribuída ao sistema é ativada, o Azure cria uma identidade para o seu serviço de pesquisa que pode ser usado para autenticar outros serviços Azure dentro do mesmo inquilino e subscrição. Em seguida, pode utilizar esta identidade em atribuições de controlo de acesso (RBAC) baseadas em papéis que permitem o acesso aos dados durante a indexação.

![Ativar o sistema atribuído identidade gerida](./media/search-managed-identities/turn-on-system-assigned-identity.png "Ativar o sistema atribuído identidade gerida")

Depois de **selecionar,** verá um ID do Objeto que foi atribuído ao seu serviço de pesquisa.

![ID de objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID de objeto")
 
### <a name="2---add-a-role-assignment"></a>2 - Adicionar uma atribuição de funções

Neste passo, você dará ao seu serviço de Pesquisa Cognitiva Azure permissão para ler dados da sua conta de armazenamento.

1. No portal Azure, navegue para a conta de Armazenamento que contenha os dados que gostaria de indexar.
2. Selecione **controlo de acesso (IAM)**
3. **Selecione Adicionar** e adicionar **a atribuição de funções**

    ![Adicionar atribuição de função](./media/search-managed-identities/add-role-assignment-storage.png "Adicionar atribuição de função")

4. Selecione as funções adequadas com base no tipo de conta de armazenamento que gostaria de indexar:
    1. O armazenamento azure Blob requer que adicione o seu serviço de pesquisa às funções **de Leitor e Data Access** e Storage **Blob Data Reader.**
    1. O Azure Data Lake Storage Gen2 requer que adicione o seu serviço de pesquisa às funções **de Leitor e Data Access** e Storage **Blob Data Reader.**
    1. O armazenamento da Mesa Azure requer que adicione o seu serviço de pesquisa apenas à função **Reader e Data Access.**
5.  Deixe **a atribuição de acesso ao** utilizador, grupo ou diretor de serviço da **Azure AD**
6.  Procure o seu serviço de pesquisa, selecione-o e, em seguida, selecione **Guardar**

    ![Adicionar atribuição de função de leitor e acesso a dados](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Adicionar atribuição de função de leitor e acesso a dados")

Note que ao ligar-se ao armazenamento de blob Azure e ao Armazenamento do Lago Azure Data Gen2, deve também adicionar a atribuição de função de Leitor de **Dados blob** de armazenamento.

![Adicionar atribuição de função de leitor de dados blob de armazenamento](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Adicionar atribuição de função de leitor de dados blob de armazenamento")

### <a name="3---create-the-data-source"></a>3 - Criar a fonte de dados

Ao indexar a partir de uma conta de armazenamento, a fonte de dados deve ter as seguintes propriedades necessárias:

* **o nome** é o nome único da fonte de dados dentro do seu serviço de pesquisa.
* **tipo**
    * Armazenamento Azure Blob:`azureblob`
    * Armazenamento de mesa azul:`azuretable`
    * Armazenamento de lagos Azure Data Gen2: **o tipo** será fornecido assim que se inscrever para a pré-visualização usando [este formulário](https://aka.ms/azure-cognitive-search/mi-preview-request).
* **credenciais**
    * Quando se utiliza uma identidade gerida para autenticar, o formato **de credenciais** é diferente do que quando não se utiliza uma identidade gerida. Aqui irá fornecer um ResourceId que não tem chave de conta ou senha. O ResourceId deve incluir o ID de subscrição da conta de armazenamento, o grupo de recursos da conta de armazenamento e o nome da conta de armazenamento.
    * Formato de identidade gerido: 
        * *ResourceId=/subscrições/ o id /recurso De**subscriçãoGroup/****o nome do grupo de recursos**/fornecedores/Microsoft.StorageAccounts/ o seu nome de conta de**armazenamento**/;*
* **o recipiente** especifica um recipiente ou nome de mesa na sua conta de armazenamento. Por defeito, todas as bolhas dentro do recipiente são recuperáveis. Se quiser indexar bolhas num determinado diretório virtual, pode especificar esse diretório utilizando o parâmetro de **consulta** opcional.

Exemplo de como criar um objeto de origem de dados blob utilizando a [API REST:](https://docs.microsoft.com/rest/api/searchservice/create-data-source)

```
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

O portal Azure e o [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) também suportam a cadeia de ligação de identidades geridas. O portal Azure requer uma bandeira de características que lhe será fornecida ao inscrever-se para a pré-visualização utilizando o link na parte superior desta página. 

### <a name="4---create-the-index"></a>4 - Criar o índice

O índice especifica os campos num documento, atributos e outras construções que moldam a experiência de pesquisa.

Aqui está como criar um índice com um campo pesquisável `content` para armazenar o texto extraído de bolhas:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Para mais informações sobre a criação de índices, consulte [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 - Criar o indexador

Um indexante liga uma fonte de dados a um índice de pesquisa de alvo, e fornece um horário para automatizar a atualização de dados.

Uma vez criado o índice e a fonte de dados, está pronto para criar o indexante.

Definição de indexante de exemplo para um indexante blob:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexante funcionará de duas em duas horas (o intervalo de horário está definido para "PT2H"). Para executar um indexante a cada 30 minutos, detete o intervalo para "PT30M". O intervalo suportado mais curto é de 5 minutos. O horário é opcional - se omitido, um indexante funciona apenas uma vez quando é criado. No entanto, pode executar um indexer a qualquer momento.   

Para mais detalhes sobre a API Create Indexer, consulte [create indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre a definição de horários indexantes, consulte [como agendar indexadores para pesquisa cognitiva azure](search-howto-schedule-indexers.md).

## <a name="see-also"></a>Ver também

Saiba mais sobre os indexadores de armazenamento azure:
* [Indexador Azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Indexador de armazenamento de lagos Azure Data Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexador de tabela sinuosa](search-howto-indexing-azure-tables.md)
