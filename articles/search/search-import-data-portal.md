---
title: Importar dados para um índice de pesquisa utilizando o portal Azure
titleSuffix: Azure Cognitive Search
description: Saiba como utilizar o assistente de dados de importação no portal Azure para rastreio de dados azure da Cosmos DB, armazenamento blob, armazenamento de mesa, Base de Dados SQL e Servidor SQL em VMs Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0ed2bd7f1e03d8d5fa11f7e76010d087605f0fe1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75460695"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Importar assistente de dados para pesquisa cognitiva azure

O portal Azure fornece um assistente de **dados de importação** no painel de pesquisa cognitiva Azure para prototipagem e carregamento de um índice. Este artigo cobre vantagens e limitações de utilização do assistente, inputs e saídas, e algumas informações de utilização. Para obter orientação prática ao pisar o assistente utilizando dados de amostra incorporados, consulte o [índice Create a Azure Cognitive Search utilizando o portal Azure](search-get-started-portal.md) quickstart.

As operações que este assistente executa incluem:

1 - Ligar a uma fonte de dados Azure suportada.

2 - Criar um esquema de índice, inferido por dados de origem de amostragem.

3 - Opcionalmente, adicione enriquecimentos de IA para extrair ou gerar conteúdo e estrutura.

4 - Executar o assistente para criar objetos, dados de importação, definir um horário e outras opções de configuração.

O assistente produz uma série de objetos que são guardados no seu serviço de pesquisa, aos quais pode aceder programaticamente ou noutras ferramentas.

## <a name="advantages-and-limitations"></a>Vantagens e limitações

Antes de escrever qualquer código, pode utilizar o assistente para prototipagem e teste de prova de conceito. O assistente conecta-se a fontes de dados externas, recolhe os dados para criar um índice inicial e, em seguida, importa os dados como documentos JSON em um índice de Pesquisa Cognitiva Azure. 

A amostragem é o processo pelo qual um esquema de índice é inferido e tem algumas limitações. Quando a fonte de dados é criada, o assistente recolhe uma amostra de documentos para decidir que colunas fazem parte da fonte de dados. Nem todos os ficheiros são lidos, pois isto pode potencialmente levar horas para fontes de dados muito grandes. Dada uma seleção de documentos, os metadados de origem, como o nome de campo ou o tipo, são usados para criar uma coleção de campos num esquema de índice. Dependendo da complexidade dos dados de origem, poderá ser necessário editar o esquema inicial para obter precisão, ou alargá-lo para a completude. Pode fazer as suas alterações na página de definição de índice.

No geral, as vantagens de usar o assistente são claras: desde que os requisitos sejam cumpridos, pode protótipo de um índice de consulta dentro de minutos. Algumas das complexidades da indexação, como o fornecimento de dados como documentos JSON, são tratadas pelo assistente.

As limitações conhecidas são resumidas da seguinte forma:

+ O assistente não suporta a iteração ou a reutilização. Cada passe através do assistente cria uma nova configuração de índice, skillset e indexante. Apenas as fontes de dados podem ser persistidas e reutilizadas dentro do assistente. Para editar ou refinar outros objetos, tem de utilizar as APIs rest ou .NET SDK para recuperar e modificar as estruturas.

+ O conteúdo de origem deve residir numa fonte de dados suportada do Azure.

+ A amostragem é sobre um subconjunto de dados de origem. Para grandes fontes de dados, é possível que o assistente perca campos. Pode ser necessário estender o esquema, ou corrigir os tipos de dados inferidos, se a amostragem for insuficiente.

+ O enriquecimento de IA, como exposto no portal, limita-se a algumas habilidades incorporadas. 

+ Uma loja de [conhecimentos,](knowledge-store-concept-intro.md)que pode ser criada pelo assistente, está limitada a algumas projeções predefinidas. Se quiser guardar documentos enriquecidos criados pelo assistente, o recipiente e as mesas blob vêm com nomes e estrutura predefinidos.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Entrada de fonte de dados

O assistente de **dados de Importação** conecta-se a uma fonte de dados externa utilizando a lógica interna fornecida pelos indexadores de Pesquisa Cognitiva Azure, que estão equipados para a amostrar a fonte, ler metadados, decifrar documentos para ler conteúdo e estrutura, e serializar conteúdos como JSON para posterior importação para Pesquisa Cognitiva Azure.

Só pode importar a partir de uma única tabela, vista de base de dados ou estrutura de dados equivalente, no entanto a estrutura pode incluir subestruturas hierárquicas ou aninhadas. Para mais informações, consulte [Como modelar tipos complexos](search-howto-complex-data-types.md).

Deve criar esta única tabela ou vista antes de executar o assistente, e deve conter conteúdo. Por razões óbvias, não faz sentido executar o assistente de **dados da Importação** numa fonte de dados vazias.

|  Seleção | Descrição |
| ---------- | ----------- |
| **Origem de dados existente** |Se já tem indexadores definidos no seu serviço de pesquisa, pode ter uma definição de fonte de dados existente que pode reutilizar. Na Pesquisa Cognitiva Azure, os objetos de origem de dados são usados apenas por indexadores. Pode criar um objeto de origem de dados programáticamente ou através do assistente de **dados da Importação** e reutilizá-los conforme necessário.|
| **Amostras**| A Azure Cognitive Search fornece duas fontes de dados de amostraincorporadas que são usadas em tutoriais e quickstarts: uma base de dados SQL imobiliária e uma base de dados hotéis alojada no Cosmos DB. Para um passeio com base na amostra hotéis, consulte o [Criar um índice no portal Azure](search-get-started-portal.md) quickstart. |
| [**Base de Dados SQL do Azure**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |Podem ser especificados um nome do serviço, credenciais para um utilizador de base de dados com permissão de leitura e um nome de base de dados na página ou através de uma cadeia de ligação do ADO.NET. Escolha a opção de cadeia de ligação para ver ou personalizar propriedades. <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção.|
| **SQL Server numa VM do Azure** |Especifique um nome de serviço totalmente qualificado, identificação do utilizador e palavra de passe e base de dados como uma cadeia de ligação. Para utilizar esta origem de dados, deve ter instalado anteriormente um certificado no arquivo local que encripta a ligação. Para obter instruções, consulte a [ligação SQL VM à Pesquisa Cognitiva Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Os requisitos incluem a conta, a base de dados e a coleção. Todos os documentos na coleção serão incluídos no índice remissivo. Pode definir uma consulta para achatar ou filtrar o conjunto de linhas, ou deixar a consulta em branco. Não é necessária uma consulta neste assistente.|
| [**Armazenamento Azure Blob**](search-howto-indexing-azure-blob-storage.md) |Os requisitos incluem a conta de armazenamento e um contentor. Opcionalmente, se os nomes de blob seguem uma convenção de nomenclatura virtual para fins de agrupamento, pode especificar a porção do diretório virtual do nome como uma pasta no contentor. Consulte [Armazenamento de Blobs de Indexação](search-howto-indexing-azure-blob-storage.md) para obter mais informações. |
| [**Table Storage do Azure**](search-howto-indexing-azure-tables.md) |Os requisitos incluem a conta de armazenamento e um nome de tabela. Opcionalmente, pode especificar uma consulta para obter um subconjunto de tabelas. Consulte [Armazenamento de Tabelas de Indexação](search-howto-indexing-azure-tables.md) para obter mais informações. |

## <a name="wizard-output"></a>Saída de assistente

Nos bastidores, o feiticeiro cria, configura e invoca os seguintes objetos. Depois de o assistente correr, pode encontrar a sua saída nas páginas do portal. A página de visão geral do seu serviço tem listas de índices, indexadores, fontes de dados e habilidades. As definições de índice podem ser vistas em plena JSON no portal. Para outras definições, pode utilizar a [API REST](https://docs.microsoft.com/rest/api/searchservice/) para obter objetos específicos.

| Objeto | Descrição | 
|--------|-------------|
| [Fonte de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Persiste informações de ligação a dados de origem, incluindo credenciais. Um objeto de origem de dados é usado exclusivamente com indexadores. | 
| [Índice](https://docs.microsoft.com/rest/api/searchservice/create-index) | Estrutura de dados físicos utilizada para pesquisa completa de texto e outras consultas. | 
| [Skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | Um conjunto completo de instruções para manipular, transformar e moldar conteúdo, incluindo analisar e extrair informações de ficheiros de imagem. Com exceção de estruturas muito simples e limitadas, inclui uma referência a um recurso dos Serviços Cognitivos que fornece enriquecimento. Opcionalmente, também pode conter uma definição de loja de conhecimento.  | 
| [Indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | Um objeto de configuração que especifica uma fonte de dados, índice de destino, um conjunto de habilidades opcionais, horário opcional e configurações de configuração opcionais para a entrega de erros e codificação base-64. |


## <a name="how-to-start-the-wizard"></a>Como iniciar o assistente

O assistente de dados de importação é iniciado a partir da barra de comando na página de visão geral do serviço.

1. No [portal Azure,](https://portal.azure.com)abra a página de serviço de pesquisa a partir do painel de instrumentos ou encontre o [seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na lista de serviços.

2. Na página de visão geral do serviço no topo, clique em **dados de importação**.

   ![Comando de dados de importação no portal](./media/search-import-data-portal/import-data-cmd2.png "Inicie o assistente de dados de importação")

Também pode lançar **dados de Importação** de outros serviços Azure, incluindo Azure Cosmos DB, Azure SQL Database e Armazenamento Azure Blob. Procure **adicionar pesquisa cognitiva Azure** no painel de navegação à esquerda na página de visão geral do serviço.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Como editar ou terminar um esquema de índice no assistente

O assistente gera um índice incompleto, que será preenchido com documentos obtidos a partir da fonte de dados de entrada. Para um índice funcional, certifique-se de que tem os seguintes elementos definidos.

1. A lista de campo está completa? Adicione novos campos que a amostragem falhou e remova qualquer que não acrescente valor a uma experiência de pesquisa ou que não seja usado numa expressão de [filtro](search-query-odata-filter.md) ou num [perfil de pontuação](index-add-scoring-profiles.md).

1. O tipo de dados é apropriado para os dados que chegam? A Pesquisa Cognitiva Azure suporta os tipos de dados do [modelo de dados da entidade (EDM).](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Para os dados do Azure SQL, existe um gráfico de [mapeamento](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) que estabelece valores equivalentes. Para mais informações, consulte [mapeamentos e transformações](search-indexer-field-mappings.md)de campo.

1. Tem um campo que sirva de *chave?* Este campo deve ser Edm.string e deve identificar um documento exclusivamente. Para dados relacionais, pode ser mapeado para uma chave primária. Para as bolhas, pode `metadata-storage-path`ser o. Se os valores de campo incluem espaços ou traços, deve definir a opção Chave de **Código Base-64** no passo **Criar um Indexer,** em **opções Avançadas,** para suprimir a verificação de validação destes caracteres.

1. Defina atributos para determinar como esse campo é usado num índice. 

   Leve o seu tempo com este passo porque os atributos determinam a expressão física dos campos no índice. Se quiser mudar de atributos mais tarde, mesmo programáticamente, terá quase sempre de cair e reconstruir o índice. Os atributos centrais como **Searchable** e **Remediável** têm um [impacto negligenciável no armazenamento.](search-what-is-an-index.md#index-size) Ativar filtros e utilizar sugestões aumentam os requisitos de armazenamento. 
   
   + **A pesquisa** permite-lhe uma pesquisa completa. Todos os campos utilizados em consultas de forma livre ou em expressões de consulta devem ter este atributo. São criados índices invertidos para cada campo que marca como **Pesquisável**.

   + **Recuperável** devolve o campo nos resultados da pesquisa. Todos os campos que forneçam conteúdo aos resultados de pesquisa devem ter este atributo. A definição deste campo não afeta sensivelmente o tamanho do índice.

   + **Filtravel** permite que o campo seja referenciado em expressões de filtro. Todos os campos utilizados numa **expressão $filter** devem ter este atributo. As expressões de filtro são para correspondências exatas. Como as cordas de texto permanecem intactas, é necessário armazenamento adicional para acomodar o conteúdo verbatim.

   + **A facetable** permite o campo para navegação facetada. Apenas os campos também marcados como **Filterable** podem ser marcados como **Facetable**.

   + **A triagem** permite que o campo seja usado de uma espécie. Todos os campos utilizados numa expressão **$Orderby** devem ter este atributo.

1. Precisa de [análise séxica?](search-lucene-query-architecture.md#stage-2-lexical-analysis) Para campos edm.string que são **Pesquisáveis,** pode definir um **Analisador** se quiser indexação e consulta melhoradas da linguagem. 

   O padrão é *Standard Lucene,* mas você poderia escolher *o Microsoft English* se quisesse usar o analisador da Microsoft para processamento lexical avançado, como a resolução de substantivos irregulares e formulários verbos. Apenas os analisadores linguísticos podem ser especificados no portal. A utilização de um analisador personalizado ou de um analisador não linguístico como a Palavra-Chave, o Padrão, e assim por diante, deve ser feito programáticamente. Para mais informações sobre os analisadores, consulte [Adicionar analisadores](search-language-support.md)de linguagem.

1. Precisa de uma funcionalidade de tipo na forma de resultados autocompletos ou sugeridos? Selecione o **Suggester,** a caixa de verificação, para permitir [sugestões](index-add-suggesters.md) de consulta de tipo e completar automaticamente em campos selecionados. Os sugestionadores adicionam ao número de termos tokenizados no seu índice, e assim consomem mais armazenamento.


## <a name="next-steps"></a>Passos seguintes

A melhor maneira de entender os benefícios e limitações do assistente é passar por ele. O seguinte arranque rápido guia-o em cada passo.

> [!div class="nextstepaction"]
> [Crie um índice de pesquisa cognitiva Azure usando o portal Azure](search-get-started-portal.md)