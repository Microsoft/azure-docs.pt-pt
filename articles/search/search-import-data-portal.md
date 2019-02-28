---
title: Importar dados para um índice de pesquisa através do portal do Azure - Azure Search
description: Saiba como utilizar o Assistente para importar dados no portal do Azure para pesquisar dados do Azure no Cosmos DB, armazenamento de BLOBs, armazenamento de tabelas, base de dados SQL e SQL Server em VMs do Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: fcb1e4f32608a1c83b653984dfa066da38e7c451
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960766"
---
# <a name="import-data-wizard-for-azure-search"></a>Assistente para importar dados para o Azure Search

O Portal do Azure fornece um assistente de **Importação de dados** no dashboard do Azure Search para carregar dados para um índice. Em segundo plano, o assistente configura e invoca um *origem de dados*, *índice*, e *indexador* -automatizando vários passos do processo de indexação: 

* Liga-se a uma origem de dados externos na mesma subscrição do Azure.
* Opcionalmente, integra o processamento de reconhecimento ótico de carateres ou linguagem natural para extrair texto de dados não estruturados.
* Gera um índice com base em amostragem de dados e metadados da origem de dados externo.
* Pesquisa a origem de dados para o conteúdo pesquisável, serialização e carregar documentos JSON para o índice.

O assistente não é possível ligar a um índice predefinido ou executar um indexador existente, mas no assistente, pode configurar um novo índice ou o indexador para suportar a estrutura e comportamentos, que precisa.

Novo na Azure Search? Siga os passos a [início rápido: Importar, indexar e consultar com ferramentas do portal](search-get-started-portal.md) a testarem importação e a indexação usando **importar dados** e o conjunto de dados de exemplo de realestate incorporada.

## <a name="start-importing-data"></a>Iniciar a importação de dados

Esta secção explica como iniciar o assistente e fornece uma visão geral de cada passo.

1. Na [portal do Azure](https://portal.azure.com), abra a página do serviço de pesquisa a partir do dashboard ou [encontrar o seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na lista de serviços.

2. Na página de descrição geral do serviço na parte superior, clique em **importar dados**.

   ![Importar o comando de dados no portal](./media/search-import-data-portal/import-data-cmd2.png "iniciar o Assistente de importação de dados")

   > [!NOTE]
   > Pode inicializar **importar dados** de outros serviços do Azure, incluindo o Azure Cosmos DB, base de dados do Azure SQL e armazenamento de Blobs do Azure. Procure **adicionar Azure Search** no painel de navegação à esquerda na página de descrição geral do serviço.

3. O assistente abre **ligar aos seus dados**, onde pode escolher uma origem de dados externa a utilizar para esta importação. Há várias coisas a saber sobre este passo, por isso, certifique-se de que lê a [entradas de origem de dados](#data-source-inputs) secção para obter mais detalhes.

   ![Assistente de importação de dados no portal](./media/search-import-data-portal/import-data-wizard-startup.png "Assistente para importar dados para o Azure Search")

4. Em seguida, há **adicionar a pesquisa cognitiva**, no caso de que pretende incluir o reconhecimento ótico de carateres (OCR) de texto em arquivos de imagem ou análise de texto em dados não estruturados. Algoritmos de IA dos serviços cognitivos são extraídos para esta tarefa. Existem duas partes para este passo:
  
   Primeiro, [anexar um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md) para um conjunto de capacidades do Azure Search.
  
   Em segundo lugar, escolha quais possível de ia para incluir o conjunto de capacidades. Para uma demonstração passo a passo, consulte esta [guia de introdução](cognitive-search-quickstart-blob.md).

   Se apenas quiser importar dados, ignore este passo e vá diretamente para a definição do índice.

5. Em seguida, há **personalizar o índice de destino**, onde pode aceitar ou modificar o esquema de índice apresentado no assistente. O assistente infere os campos e tipos de dados, dados de amostragem e ler os metadados da origem de dados externo.

   Para cada campo [verificar os atributos de índice](#index-definition) para ativar comportamentos específicos. Se não selecionar todos os atributos, o índice não será utilizável. 

6. Em seguida, há **criar um indexador**, que é um produto deste assistente. Um indexador é um crawler que extrai dados pesquisáveis e metadados a partir de uma origem de dados do Azure externos. Ao selecionar a origem de dados e anexar a conjuntos de habilidades (opcionais) e um índice, que tenha a foi configurar um indexador à medida que percorre cada etapa do assistente.

   Dê um nome ao indexador e clique em **submeter** para iniciar o processo de importação. 

Pode monitorizar a indexação no portal clicando o indexador no **indexadores** lista. Enquanto os documentos são carregados, a contagem do documento irá aumentar para o índice que definiu. Por vezes, demora alguns minutos para a página do portal escolher as atualizações mais recentes.

O índice está pronto para consultar assim que o primeiro documento é carregado. Pode usar [Explorador de pesquisa](search-explorer.md) para esta tarefa.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Entradas de origem de dados

O **importar dados** assistente cria um objeto de origem de dados persistente especificar as informações de ligação a uma origem de dados externa. O objeto de origem de dados é usado exclusivamente com o [indexadores](search-indexer-overview.md) e podem ser criadas para as seguintes origens de dados: 

* [SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Armazenamento de tabelas do Azure](search-howto-indexing-azure-tables.md) (não suportado para [pesquisa cognitiva](cognitive-search-concept-intro.md) pipelines)

Um conjunto de dados bidimensional é uma entrada necessária. Só é possível importar a partir de uma única tabela, de uma vista de base de dados ou de uma estrutura de dados equivalente. 

Deve criar essa estrutura de dados antes de executar o assistente e tem de ter conteúdo. Não execute o **importar dados** assistente numa origem de dados vazia.

|  Seleção | Descrição |
| ---------- | ----------- |
| **Origem de dados existente** |Se já tiver indexadores definidos no seu serviço de pesquisa, pode selecionar uma definição de origem de dados para outra importação existente. No Azure Search, os objetos de origem de dados só são utilizados pelo indexadores. Pode criar um objeto de origem de dados por meio de programação ou através da **importar dados** assistente.|
| **Amostras**| O Azure Search aloja gratuito público SQL do Azure bases de dados que pode utilizar para saber mais sobre pedidos de importação e a consulta no Azure Search. Consulte [início rápido: Importar, indexar e consultar com ferramentas do portal](search-get-started-portal.md) para obter instruções. |
| **Base de Dados SQL do Azure** |Podem ser especificados um nome do serviço, credenciais para um utilizador de base de dados com permissão de leitura e um nome de base de dados na página ou através de uma cadeia de ligação do ADO.NET. Escolha a opção de cadeia de ligação para ver ou personalizar propriedades. <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção. |
| **SQL Server numa VM do Azure** |Especifique um nome completamente qualificado, o ID de utilizador e a palavra-passe e a base de dados como uma cadeia de ligação. Para utilizar esta origem de dados, deve ter instalado anteriormente um certificado no arquivo local que encripta a ligação. Para obter instruções, consulte [Ligação da VM do SQL para o Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção. |
| **BD do Cosmos** |Os requisitos incluem a conta, a base de dados e a coleção. Todos os documentos na coleção serão incluídos no índice remissivo. Pode definir uma consulta para nivelamento ou filtrar o conjunto de linhas ou deixe a consulta em branco. Uma consulta não é necessária neste assistente.|
| **Armazenamento de Blobs do Azure** |Os requisitos incluem a conta de armazenamento e um contentor. Opcionalmente, se os nomes de blob seguem uma convenção de nomenclatura virtual para fins de agrupamento, pode especificar a porção do diretório virtual do nome como uma pasta no contentor. Consulte [Armazenamento de Blobs de Indexação](search-howto-indexing-azure-blob-storage.md) para obter mais informações. |
| **Table Storage do Azure** |Os requisitos incluem a conta de armazenamento e um nome de tabela. Opcionalmente, pode especificar uma consulta para obter um subconjunto de tabelas. Consulte [Armazenamento de Tabelas de Indexação](search-howto-indexing-azure-tables.md) para obter mais informações. |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Atributos de índice

O **importar dados** assistente gera um índice, que será preenchido com documentos obtidos a partir da origem de dados de entrada. 

Para um índice funcional, certifique-se de que tem os seguintes elementos definidos.

1. Um campo pode ser marcado como uma **chave**, que é utilizado para identificar exclusivamente cada documento. O **chave** tem de ser *EDM*. 

   Se os valores de campo incluem espaços ou traços, tem de definir o **a chave de codificação Base-64** opção a **criar um indexador** passo, em **opções avançadas**, para suprimir o verificação de validação para estes carateres.

1. Definir atributos de índice para cada campo. Não se selecionar nenhum atributo, o índice é essencialmente vazio, exceto para o campo de chave necessário. No mínimo, escolha um ou mais destes atributos para cada campo.
   
   + **Recuperável** devolve o campo nos resultados da pesquisa. Todos os campos que fornece conteúdo para os resultados da pesquisa tem de ter este atributo. Definir este campo não appreciably afeta o tamanho de índice.
   + **Filtrável** permite que o campo seja referenciado em expressões de filtro. Cada campo utilizado um **$filter** expressão tem de ter este atributo. Expressões de filtro destinam-se a correspondências exatas. Porque cadeias de texto permanecem intactas, é necessário armazenamento adicional para acomodar o conteúdo textual.
   + **Pesquisável** permite a pesquisa em texto completo. Cada campo utilizado em consultas de forma livre ou em expressões de consulta tem de ter este atributo. Invertida índices são criados para cada campo marcados como **pesquisável**.

1. Opcionalmente, defina estes atributos conforme necessário:

   + **Ordenável** permite que o campo a ser utilizado numa consulta. Cada campo utilizado uma **$Orderby** expressão tem de ter este atributo.
   + **Facetável** ativa o campo para navegação por facetas. Apenas os campos também marcados como **filtrável** pode ser marcado como **Facetáveis**.

1. Definir um **Analyzer** se pretender que a linguagem avançada de indexação e consulta. A predefinição é *padrão Lucene* mas pode escolher *Microsoft English* se quiser utilizar o analisador da Microsoft para processamento de léxico avançado, como resolução irregulares formulários substantivo e verbo.

   + Selecione **pesquisável** para ativar a **Analyzer** lista.
   + Escolha um analisador fornecido na lista. 
   
   Apenas podem ser especificados, de momento, analisadores de idiomas. Ao utilizar um analisador personalizado ou um analisador que não seja de idioma como Palavra-chave, Padrão e assim sucessivamente, necessitará um código. Para obter mais informações sobre analisadores, consulte [criar um índice para documentos em vários idiomas](search-language-support.md).

1. Selecione o **Sugestor** a caixa de verificação para ativar as sugestões de consulta antecipada nos campos de selecionados.


## <a name="next-steps"></a>Passos Seguintes
Reveja estas ligações para saber mais sobre indexadores:

* [Indexação da Base de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexação do Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Indexação do Armazenamento de Blobs](search-howto-indexing-azure-blob-storage.md)
* [Indexação do Armazenamento de Tabelas](search-howto-indexing-azure-tables.md)