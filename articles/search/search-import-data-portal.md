---
title: Importar dados para um índice de pesquisa usando portal do Azure
titleSuffix: Azure Cognitive Search
description: Saiba como usar o assistente de importação de dados no portal do Azure para rastrear dados do Azure de Cosmos DB, armazenamento de BLOBs, armazenamento de tabelas, banco de dados SQL e SQL Server em VMs do Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: df7d0fde05c974ef4cec739236a3ac0aebd63ecc
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534538"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Assistente de importação de dados para o Azure Pesquisa Cognitiva

O portal do Azure fornece um assistente de **importação de dados** no painel de pesquisa cognitiva do Azure para criação de protótipos e carregamento de um índice. Este artigo aborda as vantagens e limitações do uso do assistente, entradas e saídas e algumas informações de uso. Para obter orientações práticas sobre como percorrer o assistente usando dados de exemplo internos, consulte [criar um índice de pesquisa cognitiva do Azure usando o guia de início rápido do portal do Azure](search-get-started-portal.md) .

As operações que esse assistente executa incluem:

1-conectar-se a uma fonte de dados do Azure com suporte.

2-crie um esquema de índice, inferido por dados de origem de amostragem.

3-opcionalmente, adicione aprimoramentos de ia para extrair ou gerar conteúdo e estrutura.

4-Execute o assistente para criar objetos, importar dados, definir uma agenda e outras opções de configuração.

O assistente gera vários objetos que são salvos em seu serviço de pesquisa, que podem ser acessados programaticamente ou em outras ferramentas.

## <a name="advantages-and-limitations"></a>Vantagens e limitações

Antes de escrever qualquer código, você pode usar o assistente para a criação de protótipos e teste de prova de conceito. O assistente se conecta a fontes de dados externas, faz amostras dos dados para criar um índice inicial e, em seguida, importa os dados como documentos JSON para um índice no Azure Pesquisa Cognitiva. 

A amostragem é o processo pelo qual um esquema de índice é inferido e tem algumas limitações. Quando a fonte de dados é criada, o assistente escolhe uma amostra de documentos para decidir quais colunas fazem parte da fonte de dados. Nem todos os arquivos são lidos, pois isso pode levar horas para fontes de dados muito grandes. Dada uma seleção de documentos, os metadados de origem, como nome de campo ou tipo, são usados para criar uma coleção de campos em um esquema de índice. Dependendo da complexidade dos dados de origem, talvez seja necessário editar o esquema inicial para obter precisão ou estendê-lo para fins de integridade. Você pode fazer suas alterações embutidas na página definição de índice.

Em geral, as vantagens de usar o assistente são claras: contanto que os requisitos sejam atendidos, você pode protótipo de um índice passível de consulta em minutos. Algumas das complexidades da indexação, como fornecer dados como documentos JSON, são manipuladas pelo assistente.

Limitações conhecidas são resumidas da seguinte maneira:

+ O assistente não oferece suporte à iteração nem à reutilização. Cada passagem do assistente cria uma nova configuração de índice, habilidades e indexador. Somente as fontes de dados podem ser persistidas e reutilizadas no assistente. Para editar ou refinar outros objetos, você precisa usar as APIs REST ou o SDK do .NET para recuperar e modificar as estruturas.

+ O conteúdo de origem deve residir em uma fonte de dados do Azure com suporte.

+ A amostragem é sobre um subconjunto de dados de origem. Para fontes de dados grandes, é possível que o assistente perca campos. Talvez seja necessário estender o esquema ou corrigir os tipos de dados inferidos, se a amostragem for insuficiente.

+ O enriquecimento de ia, como exposto no portal, está limitado a algumas habilidades internas. 

+ Uma [loja de conhecimento](knowledge-store-concept-intro.md), que pode ser criada pelo assistente, é limitada a algumas projeções padrão. Se você quiser salvar documentos aprimorados criados pelo assistente, o contêiner de BLOBs e as tabelas serão fornecidos com os nomes e a estrutura padrão.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Entrada da fonte de dados

O assistente de **importação de dados** conecta-se a uma fonte de dados externa usando a lógica interna fornecida pelos indexadores do Azure pesquisa cognitiva, que são equipados para obter amostras da origem, ler metadados, quebrar documentos para ler conteúdo e estrutura e serializar o conteúdo como JSON para importação subsequente para o Azure pesquisa cognitiva.

Você só pode importar de uma única tabela, exibição de banco de dados ou estrutura de dado equivalente, no entanto, a estrutura pode incluir subestruturas hierárquicas ou aninhadas. Para obter mais informações, consulte [como modelar tipos complexos](search-howto-complex-data-types.md).

Você deve criar essa tabela ou exibição única antes de executar o assistente e ela deve conter conteúdo. Por motivos óbvios, não faz sentido executar o assistente de **importação de dados** em uma fonte de dados vazia.

|  Seleção | Descrição |
| ---------- | ----------- |
| **Origem de dados existente** |Se você já tiver indexadores definidos em seu serviço de pesquisa, talvez tenha uma definição de fonte de dados existente que possa ser reutilizada. No Azure Pesquisa Cognitiva, os objetos de fonte de dados são usados apenas por indexadores. Você pode criar um objeto de fonte de dados programaticamente ou por meio do assistente de **importação de dados** e reutilizá-los conforme necessário.|
| **Amostras**| O Azure Pesquisa Cognitiva fornece duas fontes de dados de exemplo internas que são usadas em tutoriais e guias de início rápido: um banco dados SQL de imóveis e um banco de dados de hotéis hospedados no Cosmos DB. Para obter uma orientação com base no exemplo de hotéis, consulte [criar um índice no guia de](search-get-started-portal.md) início rápido portal do Azure. |
| [**Banco de dados SQL do Azure**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |Podem ser especificados um nome do serviço, credenciais para um utilizador de base de dados com permissão de leitura e um nome de base de dados na página ou através de uma cadeia de ligação do ADO.NET. Escolha a opção de cadeia de ligação para ver ou personalizar propriedades. <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção.|
| **SQL Server numa VM do Azure** |Especifique um nome de serviço totalmente qualificado, uma ID de usuário e uma senha e um banco de dados como uma cadeia de conexão. Para utilizar esta origem de dados, deve ter instalado anteriormente um certificado no arquivo local que encripta a ligação. Para obter instruções, consulte [conexão de VM do SQL para o Azure pesquisa cognitiva](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Os requisitos incluem a conta, a base de dados e a coleção. Todos os documentos na coleção serão incluídos no índice remissivo. Você pode definir uma consulta para achatar ou filtrar o conjunto de linhas ou deixar a consulta em branco. Uma consulta não é necessária neste assistente.|
| [**Armazenamento de BLOBs do Azure**](search-howto-indexing-azure-blob-storage.md) |Os requisitos incluem a conta de armazenamento e um contentor. Opcionalmente, se os nomes de blob seguem uma convenção de nomenclatura virtual para fins de agrupamento, pode especificar a porção do diretório virtual do nome como uma pasta no contentor. Consulte [Armazenamento de Blobs de Indexação](search-howto-indexing-azure-blob-storage.md) para obter mais informações. |
| [**Armazenamento de tabelas do Azure**](search-howto-indexing-azure-tables.md) |Os requisitos incluem a conta de armazenamento e um nome de tabela. Opcionalmente, pode especificar uma consulta para obter um subconjunto de tabelas. Consulte [Armazenamento de Tabelas de Indexação](search-howto-indexing-azure-tables.md) para obter mais informações. |

## <a name="wizard-output"></a>Saída do assistente

Nos bastidores, o assistente cria, configura e invoca os objetos a seguir. Depois que o assistente for executado, você poderá encontrar sua saída nas páginas do Portal. A página Visão geral do seu serviço tem listas de índices, indexadores, fontes de dados e habilidades. As definições de índice podem ser exibidas em JSON completo no Portal. Para outras definições, você pode usar a [API REST](https://docs.microsoft.com/rest/api/searchservice/) para obter objetos específicos.

| Objeto | Descrição | 
|--------|-------------|
| [Origem de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Persiste informações de conexão para dados de origem, incluindo credenciais. Um objeto de fonte de dados é usado exclusivamente com indexadores. | 
| [Índice](https://docs.microsoft.com/rest/api/searchservice/create-index) | Estrutura de dados física usada para pesquisa de texto completo e outras consultas. | 
| [Qualificações](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | Um conjunto completo de instruções para manipular, transformar e formatar conteúdo, incluindo a análise e extração de informações de arquivos de imagem. Exceto por estruturas muito simples e limitadas, ele inclui uma referência a um recurso de serviços cognitivas que fornece enriquecimento. Opcionalmente, ele também pode conter uma definição de repositório de conhecimento.  | 
| [Indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | Um objeto de configuração que especifica uma fonte de dados, um índice de destino, um conjunto de qualificações opcional, um agendamento opcional e definições de configuração opcionais para a transmissão de erro e a codificação de base 64. |


## <a name="how-to-start-the-wizard"></a>Como iniciar o assistente

O assistente de importação de dados é iniciado na barra de comandos na página Visão geral do serviço.

1. Na [portal do Azure](https://portal.azure.com), abra a página Serviço de pesquisa no painel ou [Localize seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na lista de serviços.

2. Na página Visão geral do serviço na parte superior, clique em **importar dados**.

   ![Comando importar dados no portal](./media/search-import-data-portal/import-data-cmd2.png "Iniciar o assistente de importação de dados")

Você também pode iniciar a **importação de dados** de outros serviços do Azure, incluindo Azure Cosmos DB, banco de dados SQL do Azure e armazenamento de BLOBs do Azure. Procure **adicionar pesquisa cognitiva do Azure** no painel de navegação esquerdo na página Visão geral do serviço.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Como editar ou concluir um esquema de índice no assistente

O assistente gera um índice incompleto, que será preenchido com documentos obtidos da fonte de dados de entrada. Para um índice funcional, verifique se você tem os seguintes elementos definidos.

1. A lista de campos está concluída? Adicione novos campos que amostragem são perdidos e remova qualquer um que não adicione valor a uma experiência de pesquisa ou que não seja usado em uma [expressão de filtro](search-query-odata-filter.md) ou [perfil de Pontuação](index-add-scoring-profiles.md).

1. O tipo de dados é apropriado para os dados de entrada? O Azure Pesquisa Cognitiva dá suporte aos [tipos de dados do EDM (modelo de dados de entidade)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types). Para dados SQL do Azure, há um [gráfico de mapeamento](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) que apresenta valores equivalentes. Para obter mais informações, consulte [mapeamentos de campo e transformações](search-indexer-field-mappings.md).

1. Você tem um campo que pode servir como *chave*? Esse campo deve ser EDM. String e deve identificar um documento exclusivamente. Para dados relacionais, ele pode ser mapeado para uma chave primária. Para BLOBs, pode ser a `metadata-storage-path`. Se os valores de campo incluírem espaços ou traços, você deverá definir a opção de **chave de codificação base-64** na etapa **criar um indexador** , em **Opções avançadas**, para suprimir a verificação de validação para esses caracteres.

1. Defina atributos para determinar como esse campo é usado em um índice. 

   Reserve seu tempo com esta etapa porque os atributos determinam a expressão física dos campos no índice. Se desejar alterar atributos posteriormente, até mesmo programaticamente, você quase sempre precisará remover e recompilar o índice. Os atributos principais, como **pesquisáveis** e **recuperáveis** , têm um [impacto insignificante no armazenamento](search-what-is-an-index.md#storage-implications). Habilitar filtros e usar sugestores aumentam os requisitos de armazenamento. 
   
   + **Pesquisável** habilita a pesquisa de texto completo. Cada campo usado em consultas de forma livre ou em expressões de consulta deve ter esse atributo. Os índices invertidos são criados para cada campo que você marca como **pesquisável**.

   + **Recuperável** retorna o campo nos resultados da pesquisa. Cada campo que fornece conteúdo aos resultados da pesquisa deve ter esse atributo. A definição deste campo não apreciavelmente o tamanho do índice de efeito.

   + **Filtrável** permite que o campo seja referenciado em expressões de filtro. Cada campo usado em uma expressão de **$Filter** deve ter esse atributo. Expressões de filtro são para correspondências exatas. Como as cadeias de caracteres de texto permanecem intactas, é necessário armazenamento adicional para acomodar o conteúdo textual.

   + A **facetable** habilita o campo para navegação facetada. Somente os campos marcados como **filtráveis** podem ser marcados como **facetable**.

   + **Classificável** permite que o campo seja usado em uma classificação. Cada campo usado em uma expressão de **$OrderBy** deve ter esse atributo.

1. Você precisa de uma [análise lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis)? Para campos EDM. String que são **pesquisáveis**, você pode definir um **analisador** se quiser indexação e consulta com maior idioma. 

   O padrão é *Lucene padrão* , mas você pode escolher *Microsoft English* se quisesse usar o analisador da Microsoft para processamento lexical avançado, como resolver formulários irregulares de substantivo e de verbo. Somente analisadores de idioma podem ser especificados no Portal. Usar um analisador personalizado ou um analisador que não seja de linguagem, como palavra-chave, padrão e assim por diante, deve ser feito programaticamente. Para obter mais informações sobre analisadores, consulte [Adicionar analisadores de idioma](search-language-support.md).

1. Você precisa da funcionalidade typeahead na forma de preenchimento automático ou de resultados sugeridos? Selecione a caixa de seleção **Sugestor** para habilitar [sugestões de consulta typeahead e preenchimento automático](index-add-suggesters.md) em campos selecionados. Os sugestores adicionam o número de termos com token no índice e, portanto, consomem mais armazenamento.


## <a name="next-steps"></a>Passos seguintes

A melhor maneira de entender os benefícios e as limitações do assistente é percorrer isso. O guia de início rápido a seguir orienta você em cada etapa.

> [!div class="nextstepaction"]
> [Criar um índice de Pesquisa Cognitiva do Azure usando o portal do Azure](search-get-started-portal.md)