---
title: Importar dados para um índice de pesquisa usando o portal Azure
titleSuffix: Azure Cognitive Search
description: Saiba como utilizar o assistente de dados de importação no portal Azure para rastrear dados do Azure a partir de Cosmos DB, armazenamento de blob, armazenamento de mesas, Base de Dados SQL, SQL Managed Instance e SQL Server em VMs Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7cff009d5d1e187e8d0330fadca530b57b3e3d21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935216"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Assistente de dados de importação para Azure Cognitive Search

O portal Azure fornece um assistente **de dados de importação** no painel de pesquisa cognitiva Azure para prototipagem e carregamento de um índice. Este artigo cobre vantagens e limitações de utilização do assistente, entradas e saídas, bem como algumas informações de utilização. Para obter orientações práticas ao passar pelo assistente utilizando dados de amostra incorporadas, consulte o índice de Pesquisa Cognitiva Azure utilizando o quickstart [do portal Azure.](search-get-started-portal.md)

As operações que este assistente executa incluem:

1 - Ligue-se a uma fonte de dados Azure suportada.

2 - Criar um esquema de índice, inferido por dados de origem de amostragem.

3 - Opcionalmente, adicione enriquecimentos de IA para extrair ou gerar conteúdo e estrutura.

4 - Executar o assistente para criar objetos, importar dados, definir um horário e outras opções de configuração.

O assistente produz uma série de objetos que são guardados no seu serviço de pesquisa, aos quais pode aceder programaticamente ou noutras ferramentas.

## <a name="advantages-and-limitations"></a>Vantagens e limitações

Antes de escrever qualquer código, pode utilizar o assistente para prototipagem e testes de prova de conceito. O assistente conecta-se a fontes de dados externas, analisa os dados para criar um índice inicial e, em seguida, importa os dados como documentos JSON num índice na Azure Cognitive Search. 

A amostragem é o processo pelo qual um esquema de índice é deduzido e tem algumas limitações. Quando a fonte de dados é criada, o assistente escolhe uma amostra de documentos para decidir que colunas fazem parte da fonte de dados. Nem todos os ficheiros são lidos, uma vez que isto pode levar horas para fontes de dados muito grandes. Dada a seleção de documentos, os metadados de origem, como o nome de campo ou o tipo, são usados para criar uma recolha de campos num esquema de índice. Dependendo da complexidade dos dados de origem, poderá ser necessário editar o esquema inicial para maior precisão ou alargá-lo para a sua pôr em ordem. Pode fazer as alterações na página de definição de índice.

No geral, as vantagens de utilizar o assistente são claras: desde que os requisitos sejam cumpridos, pode protótipo de um índice consultado dentro de minutos. Algumas das complexidades da indexação, tais como o fornecimento de dados como documentos JSON, são tratadas pelo assistente.

As limitações conhecidas são resumidas da seguinte forma:

+ O assistente não suporta iteração nem reutilização. Cada passagem através do assistente cria uma nova configuração de índice, skillset e indexante. Apenas as fontes de dados podem ser persistidos e reutilizadas dentro do assistente. Para editar ou refinar outros objetos, tem de utilizar as APIs REST ou .NET SDK para recuperar e modificar as estruturas.

+ O conteúdo da fonte deve residir numa fonte de dados Azure suportada.

+ A amostragem é sobre um subconjunto de dados de origem. Para grandes fontes de dados, é possível para o assistente perder campos. Pode ser necessário estender o esquema, ou corrigir os tipos de dados inferidos, se a amostragem for insuficiente.

+ O enriquecimento de IA, tal como exposto no portal, limita-se a algumas habilidades incorporadas. 

+ Uma [loja de conhecimentos](knowledge-store-concept-intro.md), que pode ser criada pelo assistente, limita-se a algumas projeções padrão. Se pretender guardar documentos enriquecidos criados pelo assistente, o recipiente e as tabelas blob vêm com nomes e estrutura padrão.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Entrada de fonte de dados

O assistente **de dados de importação** conecta-se a uma fonte de dados externa usando a lógica interna fornecida pelos indexadores de Pesquisa Cognitiva Azure, que estão equipados para amostrar a fonte, ler metadados, decifrar documentos para ler conteúdo e estrutura, e serializar conteúdos como JSON para posterior importação para Azure Cognitive Search.

Só é possível importar a partir de uma única tabela, vista de base de dados ou estrutura de dados equivalente, no entanto a estrutura pode incluir subestruturas hierárquicas ou aninhadas. Para obter mais informações, consulte [Como modelar tipos complexos.](search-howto-complex-data-types.md)

Deve criar esta única tabela ou visualização antes de executar o assistente, e deve conter conteúdo. Por razões óbvias, não faz sentido executar o assistente **de dados de Importação** numa fonte de dados vazia.

|  Seleção | Descrição |
| ---------- | ----------- |
| **Origem de dados existente** |Se já tiver indexantes definidos no seu serviço de pesquisa, poderá ter uma definição de fonte de dados existente que pode reutilizar. Na Pesquisa Cognitiva Azure, os objetos de origem de dados são utilizados apenas por indexadores. Pode criar um objeto de origem de dados programáticamente ou através do assistente **de dados De importação** e reutilizá-los conforme necessário.|
| **Amostras**| A Azure Cognitive Search fornece duas fontes de dados de amostras incorporadas que são usadas em tutoriais e quickstarts: uma base de dados SQL imobiliária e uma base de dados de Hotéis hospedada em Cosmos DB. Para um passeio com base na amostra de Hotéis, consulte o [Create um índice no quickstart do portal Azure.](search-get-started-portal.md) |
| [**Base de Dados Azure SQL ou SqL Caso Gerido**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |Podem ser especificados um nome do serviço, credenciais para um utilizador de base de dados com permissão de leitura e um nome de base de dados na página ou através de uma cadeia de ligação do ADO.NET. Escolha a opção de cadeia de ligação para ver ou personalizar propriedades. <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção.|
| **SQL Server numa VM do Azure** |Especifique um nome de serviço totalmente qualificado, ID do utilizador e senha e base de dados como uma cadeia de ligação. Para utilizar esta origem de dados, deve ter instalado anteriormente um certificado no arquivo local que encripta a ligação. Para obter instruções, consulte [a ligação SQL VM à Pesquisa Cognitiva Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Os requisitos incluem a conta, a base de dados e a coleção. Todos os documentos na coleção serão incluídos no índice remissivo. Pode definir uma consulta para achatar ou filtrar o conjunto de linhas, ou deixar a consulta em branco. Não é necessária uma consulta neste assistente.|
| [**Armazenamento Azure Blob**](search-howto-indexing-azure-blob-storage.md) |Os requisitos incluem a conta de armazenamento e um contentor. Opcionalmente, se os nomes de blob seguem uma convenção de nomenclatura virtual para fins de agrupamento, pode especificar a porção do diretório virtual do nome como uma pasta no contentor. Consulte [Armazenamento de Blobs de Indexação](search-howto-indexing-azure-blob-storage.md) para obter mais informações. |
| [**Armazenamento de mesa Azure**](search-howto-indexing-azure-tables.md) |Os requisitos incluem a conta de armazenamento e um nome de tabela. Opcionalmente, pode especificar uma consulta para obter um subconjunto de tabelas. Consulte [Armazenamento de Tabelas de Indexação](search-howto-indexing-azure-tables.md) para obter mais informações. |

## <a name="wizard-output"></a>Saída do assistente

Nos bastidores, o assistente cria, configura e invoca os seguintes objetos. Depois de o assistente ser executado, pode encontrar a sua saída nas páginas do portal. A página geral do seu serviço tem listas de índices, indexadores, fontes de dados e skillsets. As definições de índice podem ser vistas em JSON completo no portal. Para outras definições, pode utilizar a [API REST](/rest/api/searchservice/) para obter objetos específicos.

| Objeto | Description | 
|--------|-------------|
| [Origem de dados](/rest/api/searchservice/create-data-source)  | Persiste a informação de ligação aos dados de origem, incluindo credenciais. Um objeto de origem de dados é utilizado exclusivamente com indexadores. | 
| [Índice](/rest/api/searchservice/create-index) | Estrutura de dados físicos utilizada para pesquisa completa de texto e outras consultas. | 
| [Skillset](/rest/api/searchservice/create-skillset) | Um conjunto completo de instruções para manipular, transformar e moldar conteúdos, incluindo analisar e extrair informações de ficheiros de imagem. Com exceção de estruturas muito simples e limitadas, inclui uma referência a um recurso de Serviços Cognitivos que proporciona enriquecimento. Opcionalmente, pode também conter uma definição de loja de conhecimento.  | 
| [Indexador](/rest/api/searchservice/create-indexer)  | Um objeto de configuração especificando uma fonte de dados, índice de destino, um skillset opcional, agenda opcional e configurações de configuração opcional para entrega de erros e codificação base-64. |


## <a name="how-to-start-the-wizard"></a>Como começar o feiticeiro

O assistente de dados De importação é iniciado a partir da barra de comando na página de visão geral do serviço.

1. No [portal Azure,](https://portal.azure.com)abra a página de serviço de pesquisa do painel de instrumentos ou encontre o [seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na lista de serviços.

2. Na página geral do serviço no topo, clique nos **dados de importação**.

   ![Comando de dados de importação no portal](./media/search-import-data-portal/import-data-cmd2.png "Inicie o assistente de dados de importação")

Também pode lançar **dados** de importação de outros serviços Azure, incluindo Azure Cosmos DB, Azure SQL Database, SQL Managed Instance e armazenamento Azure Blob. Procure **add Azure Cognitive Search** no painel de navegação à esquerda na página de visão geral do serviço.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Como editar ou terminar um esquema de índice no assistente

O assistente gera um índice incompleto, que será preenchido com documentos obtidos a partir da fonte de dados de entrada. Para um índice funcional, certifique-se de que tem os seguintes elementos definidos.

1. A lista de campo está completa? Adicione novos campos que a amostragem falhou e remova quaisquer que não acrescentem valor a uma experiência de pesquisa ou que não sejam utilizados numa [expressão de filtro](search-query-odata-filter.md) ou perfil de [pontuação](index-add-scoring-profiles.md).

1. O tipo de dados é adequado para os dados de entrada? A Azure Cognitive Search suporta os tipos de dados do modelo de dados da [entidade (EDM).](/rest/api/searchservice/supported-data-types) Para os dados do Azure SQL, existe [um gráfico de mapeamento](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) que define valores equivalentes. Para mais informações, consulte [mapeamentos e transformações de campo.](search-indexer-field-mappings.md)

1. Tem um campo que possa servir de *chave?* Este campo deve ser Edm.string e deve identificar um documento de forma única. Para dados relacionais, pode ser mapeado para uma chave primária. Para as bolhas, pode ser `metadata-storage-path` a. Se os valores de campo incluir espaços ou traços, deve definir a opção **Chave de Codificação Base-64** no passo **Criar um Indexador,** em **opções Avançadas,** para suprimir o controlo de validação destes caracteres.

1. Defina atributos para determinar como esse campo é usado num índice. 

   Leve o seu tempo com este passo porque os atributos determinam a expressão física dos campos no índice. Se quiser alterar atributos mais tarde, mesmo programáticamente, terá quase sempre de largar e reconstruir o índice. Atributos **fundamentais** como Searchable e **Retrievable** têm um [impacto negligenciável no armazenamento.](search-what-is-an-index.md#index-size) Ativar filtros e utilizar sugestivos aumenta os requisitos de armazenamento. 
   
   + **Pesmável** permite a pesquisa completa. Todos os campos utilizados em consultas de forma gratuita ou em expressões de consulta devem ter este atributo. Os índices invertidos são criados para cada campo que marca como **Pescável.**

   + **Recuperável** devolve o campo em resultados de pesquisa. Para todos os campos que nos nídricos forneça os resultados da sua pesquisa. A definição deste campo não afeta sensivelmente o tamanho do índice.

   + **Filtrado** permite que o campo seja referenciado em expressões de filtro. Todos os campos utilizados numa expressão **$filter**  devem ter este atributo. Expressões de filtro são para fósforos exatos. Como as cordas de texto permanecem intactas, é necessário um armazenamento adicional para acomodar o conteúdo verbatim.

   + **Facetable** permite o campo para navegação facetada. Apenas os campos marcados como **Filtrados** podem ser marcados como **Facetable**.

   + **Ordenado** permite que o campo seja usado numa espécie. Todos os campos utilizados numa expressão **$Orderby** devem ter este atributo.

1. Precisa de [análise lexical?](search-lucene-query-architecture.md#stage-2-lexical-analysis) Para os campos Edm.string que são **pesmáveis,** pode definir um **Analisador** se quiser indexação e consulta melhoradas da linguagem. 

   O padrão é *Standard Lucene,* mas pode escolher *o Microsoft English* se quiser utilizar o analisador da Microsoft para processamento lexical avançado, como a resolução de substantivos irregulares e formulários de verbo. Apenas os analisadores linguísticos podem ser especificados no portal. A utilização de um analisador personalizado ou de um analisador não-linguístico como palavra-chave, padrão, etc., deve ser feito programáticamente. Para obter mais informações sobre os analisadores, consulte [os analisadores de linguagem Add](search-language-support.md).

1. Precisa de uma funcionalidade de typeahead sob a forma de resultados autocompletos ou sugeridos? Selecione o **Suggester** a caixa de verificação para permitir [sugestões de consulta de tipa e precontativar automaticamente](index-add-suggesters.md) em campos selecionados. Os sugestivos adicionam ao número de termos simbólicos no seu índice e consomem assim mais armazenamento.


## <a name="next-steps"></a>Passos seguintes

A melhor maneira de entender os benefícios e limitações do feiticeiro é ultrapassá-lo. O seguinte quickstart guia-o através de cada passo.

> [!div class="nextstepaction"]
> [Criar um índice de Pesquisa Cognitiva Azure utilizando o portal Azure](search-get-started-portal.md)