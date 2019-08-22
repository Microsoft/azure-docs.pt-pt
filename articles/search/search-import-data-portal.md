---
title: Importar dados para um índice de pesquisa usando portal do Azure-Azure Search
description: Saiba como usar o assistente de importação de dados no portal do Azure para rastrear dados do Azure de Cosmos DB, armazenamento de BLOBs, armazenamento de tabelas, banco de dados SQL e SQL Server em VMs do Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: e784cbf351bd062712e0fd66332799907a3bcae8
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648259"
---
# <a name="import-data-wizard-for-azure-search"></a>Assistente de importação de dados para Azure Search

O Portal do Azure fornece um assistente de **Importação de dados** no dashboard do Azure Search para carregar dados para um índice. Nos bastidores, o assistente configura e invoca uma *fonte de dados*, um *índice*e um *indexador* – automatizando várias etapas do processo de indexação: 

* Conecta-se a uma fonte de dados externa na mesma assinatura do Azure.
* Opcionalmente, integra o reconhecimento óptico de caracteres ou o processamento de idioma natural para extrair texto de dados não estruturados.
* Gera um índice com base na amostragem de dados e nos metadados da fonte de dados externa.
* Rastreia a fonte de dados para conteúdo pesquisável, serializando e carregando documentos JSON no índice.

O assistente não pode se conectar a um índice predefinido ou executar um indexador existente, mas, no assistente, você pode configurar um novo índice ou indexador para dar suporte à estrutura e aos comportamentos necessários.

Novo na Azure Search? Percorra o [início rápido: Importe, indexe e consulte usando ferramentas](search-get-started-portal.md) do portal para testar a importação e a indexação usando dados de **importação** e o conjunto de dados de exemplo interno de imóveis.

## <a name="start-importing-data"></a>Iniciar importação de dados

Esta seção explica como iniciar o assistente e fornece uma visão geral de alto nível de cada etapa.

1. Na [portal do Azure](https://portal.azure.com), abra a página Serviço de pesquisa no painel ou [Localize seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na lista de serviços.

2. Na página Visão geral do serviço na parte superior, clique em **importar dados**.

   ![Comando importar dados no portal](./media/search-import-data-portal/import-data-cmd2.png "Iniciar o assistente de importação de dados")

   > [!NOTE]
   > Você pode iniciar a **importação de dados** de outros serviços do Azure, incluindo Azure Cosmos DB, banco de dados SQL do Azure e armazenamento de BLOBs do Azure. Procure **adicionar Azure Search** no painel de navegação esquerdo na página Visão geral do serviço.

3. O assistente é aberto para **se conectar aos seus dados**, onde você pode escolher uma fonte de dados externa a ser usada para essa importação. Há várias coisas a saber sobre esta etapa. portanto, leia a seção entradas da [fonte de dados](#data-source-inputs) para obter mais detalhes.

   ![Assistente de importação de dados no portal](./media/search-import-data-portal/import-data-wizard-startup.png "Assistente de importação de dados para Azure Search")

4. Em seguida, **adicione a pesquisa cognitiva**, caso você queira incluir o OCR (reconhecimento óptico de caracteres) de texto em arquivos de imagem ou a análise de texto em dados não estruturados. Os algoritmos de ia dos serviços cognitivas são recebidos para essa tarefa. Há duas partes nesta etapa:
  
   Primeiro, [anexe um recurso de serviços cognitivas](cognitive-search-attach-cognitive-services.md) a um Azure Search o skillset.
  
   Em segundo lugar, escolha os aprimoramentos de ia a serem incluídos no contextset. Para obter uma demonstração de instruções, consulte este guia de [início rápido](cognitive-search-quickstart-blob.md).

   Se você apenas deseja importar dados, pule esta etapa e vá diretamente para a definição de índice.

5. Em seguida, é possível **Personalizar o índice de destino**, no qual você pode aceitar ou modificar o esquema de índice apresentado no assistente. O assistente infere os campos e os tipos de dados por meio de amostragem e leitura de metadados da fonte de dados externa.

   Para cada campo, [Verifique os atributos de índice](#index-definition) para habilitar comportamentos específicos. Se você não selecionar nenhum atributo, o índice não poderá ser usado. 

6. Em seguida, **crie um indexador**, que é um produto deste assistente. Um indexador é um rastreador que extrai dados pesquisáveis e metadados de uma fonte de dados externa do Azure. Selecionando a fonte de dados e anexando habilidades (opcional) e um índice, você configurou um indexador conforme percorre cada etapa do assistente.

   Dê um nome ao indexador e clique em **Enviar** para iniciar o processo de importação. 

Você pode monitorar a indexação no portal clicando no indexador na lista **indexadores** . Enquanto os documentos são carregados, a contagem do documento irá aumentar para o índice que definiu. Por vezes, demora alguns minutos para a página do portal escolher as atualizações mais recentes.

O índice está pronto para ser consultado assim que o primeiro documento é carregado. Você pode usar o [Search Explorer](search-explorer.md) para esta tarefa.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Entradas da fonte de dados

O assistente de **importação de dados** cria um objeto de fonte de dados persistente especificando informações de conexão para uma fonte de dados externa. O objeto de fonte de dados é usado [](search-indexer-overview.md) exclusivamente com indexadores e pode ser criado para as seguintes fontes de dados: 

* [SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Armazenamento de tabelas do Azure](search-howto-indexing-azure-tables.md) (sem suporte para pipelines de [pesquisa cognitiva](cognitive-search-concept-intro.md) )

Você só pode importar de uma única tabela, exibição de banco de dados ou estrutura de dado equivalente, no entanto, a estrutura pode incluir subestruturas hierárquicas ou aninhadas. Para obter mais informações, consulte [como modelar tipos complexos](search-howto-complex-data-types.md).

Você deve criar essa estrutura de dados antes de executar o assistente e ela deve conter conteúdo. Não execute o assistente de **importação de dados** em uma fonte de dados vazia.

|  Seleção | Descrição |
| ---------- | ----------- |
| **Origem de dados existente** |Se já tiver indexadores definidos no seu serviço de pesquisa, pode selecionar uma definição de origem de dados para outra importação existente. No Azure Search, os objetos de fonte de dados são usados apenas por indexadores. Você pode criar um objeto de fonte de dados programaticamente ou por meio do assistente de **importação de dados** .|
| **Amostras**| O Azure Search hospeda um banco de dados SQL global do Azure gratuito que você pode usar para saber mais sobre como importar e consultar solicitações no Azure Search. Consulte [início rápido: Importar, indexar e consultar usando ferramentas](search-get-started-portal.md) do portal para obter instruções. |
| **Base de Dados SQL do Azure** |Podem ser especificados um nome do serviço, credenciais para um utilizador de base de dados com permissão de leitura e um nome de base de dados na página ou através de uma cadeia de ligação do ADO.NET. Escolha a opção de cadeia de ligação para ver ou personalizar propriedades. <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção. |
| **SQL Server numa VM do Azure** |Especifique um nome de serviço totalmente qualificado, uma ID de usuário e uma senha e um banco de dados como uma cadeia de conexão. Para utilizar esta origem de dados, deve ter instalado anteriormente um certificado no arquivo local que encripta a ligação. Para obter instruções, consulte [Ligação da VM do SQL para o Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>A tabela ou vista que fornece o conjunto de linhas deve ser especificada na página. Esta opção é apresentada após a ligação ser bem sucedida, apresentando uma lista pendente para que possa fazer uma seleção. |
| **BD do Cosmos** |Os requisitos incluem a conta, a base de dados e a coleção. Todos os documentos na coleção serão incluídos no índice remissivo. Você pode definir uma consulta para achatar ou filtrar o conjunto de linhas ou deixar a consulta em branco. Uma consulta não é necessária neste assistente.|
| **Armazenamento de Blobs do Azure** |Os requisitos incluem a conta de armazenamento e um contentor. Opcionalmente, se os nomes de blob seguem uma convenção de nomenclatura virtual para fins de agrupamento, pode especificar a porção do diretório virtual do nome como uma pasta no contentor. Consulte [Armazenamento de Blobs de Indexação](search-howto-indexing-azure-blob-storage.md) para obter mais informações. |
| **Table Storage do Azure** |Os requisitos incluem a conta de armazenamento e um nome de tabela. Opcionalmente, pode especificar uma consulta para obter um subconjunto de tabelas. Consulte [Armazenamento de Tabelas de Indexação](search-howto-indexing-azure-tables.md) para obter mais informações. |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Atributos de índice

O assistente de **importação de dados** gera um índice, que será preenchido com documentos obtidos da fonte de dados de entrada. 

Para um índice funcional, verifique se você tem os seguintes elementos definidos.

1. Um campo deve ser marcado como uma **chave**, que é usada para identificar exclusivamente cada documento. A **chave** deve ser *EDM. String*. 

   Se os valores de campo incluírem espaços ou traços, você deverá definir a opção de **chave de codificação base-64** na etapa **criar um indexador** , em **Opções avançadas**, para suprimir a verificação de validação para esses caracteres.

1. Defina atributos de índice para cada campo. Se você não selecionar nenhum atributo, o índice ficará essencialmente vazio, exceto para o campo de chave necessário. No mínimo, escolha um ou mais desses atributos para cada campo.
   
   + **Recuperável** retorna o campo nos resultados da pesquisa. Cada campo que fornece conteúdo aos resultados da pesquisa deve ter esse atributo. A definição deste campo não apreciavelmente o tamanho do índice de efeito.
   + **Filtrável** permite que o campo seja referenciado em expressões de filtro. Cada campo usado em uma expressão de **$Filter** deve ter esse atributo. Expressões de filtro são para correspondências exatas. Como as cadeias de caracteres de texto permanecem intactas, é necessário armazenamento adicional para acomodar o conteúdo textual.
   + **Pesquisável** habilita a pesquisa de texto completo. Cada campo usado em consultas de forma livre ou em expressões de consulta deve ter esse atributo. Os índices invertidos são criados para cada campo que vocêmarca como pesquisável.

1. Opcionalmente, defina esses atributos conforme necessário:

   + **Classificável** permite que o campo seja usado em uma classificação. Cada campo usado em uma expressão de **$OrderBy** deve ter esse atributo.
   + A facetable habilita o campo para navegação facetada. Somente os campos marcados como **filtráveis** podem ser marcados como **facetable**.

1. Defina um **analisador** se você quiser indexação e consulta com melhor idioma. O padrão é *Lucene padrão* , mas você pode escolher *Microsoft English* se quisesse usar o analisador da Microsoft para processamento lexical avançado, como resolver formulários irregulares de substantivo e de verbo.

   + Selecione **pesquisável** para habilitar a lista do analisador.
   + Escolha um analisador fornecido na lista. 
   
   Apenas podem ser especificados, de momento, analisadores de idiomas. Ao utilizar um analisador personalizado ou um analisador que não seja de idioma como Palavra-chave, Padrão e assim sucessivamente, necessitará um código. Para obter mais informações sobre analisadores, consulte [criar um índice para documentos em vários idiomas](search-language-support.md).

1. Marque a caixa de seleção Sugestor para habilitar sugestões de consulta de digitação antecipada nos campos selecionados.


## <a name="next-steps"></a>Passos Seguintes
Reveja estas ligações para saber mais sobre indexadores:

* [Indexação da Base de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexação do Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Indexação do Armazenamento de Blobs](search-howto-indexing-azure-blob-storage.md)
* [Indexação do Armazenamento de Tabelas](search-howto-indexing-azure-tables.md)