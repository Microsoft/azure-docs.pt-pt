---
title: Indexação no Azure Cosmos DB
description: Compreenda como a indexação funciona no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: ecf53251020ce1b639a5bf8da65f5d31ff699db9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265700"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexação no Azure Cosmos DB - descrição geral

O Azure Cosmos DB é uma base de dados sem esquema e permite-lhe reanalisa rapidamente a sua aplicação sem ter de lidar com a gestão de índices ou esquemas. Por predefinição, o Azure Cosmos DB indexa automaticamente todos os itens no seu contentor sem necessidade de esquema ou índices secundários dos desenvolvedores.

## <a name="items-as-trees"></a>Itens como árvores

Ao projetar itens num contentor como documentos JSON e que representa-los como árvores, Azure Cosmos DB normaliza a estrutura e os valores de instância em itens para o conceito unificador de um **dinamicamente codificada a estrutura de caminho** . Essa representação, cada etiqueta num documento JSON, que inclui os nomes das propriedades e seus valores, torna-se um nó da árvore. Nos detalhes da árvore de contenham os valores reais e os nós intermediários contêm as informações de esquema. A imagem seguinte representa as árvores criadas para dois itens (1 e 2) no contentor do Azure Cosmos:

![Representação em árvore para dois itens diferentes num contentor do Cosmos do Azure](./media/index-overview/indexing-as-tree.png)

Um nó de raiz de pseudo-autenticação é criado um elemento principal para os nós reais correspondente para as etiquetas no documento JSON por baixo. As estruturas de dados aninhado unidade a hierarquia na árvore. Intermediários nós artificiais rotulados com valores numéricos (por exemplo, 0, 1,...) são empregados para representar as enumerações e índices de matriz.

## <a name="index-paths"></a>Caminhos de índice

O Azure Cosmos DB projetos itens num contentor do Azure Cosmos como documentos JSON e o índice como árvores. Em seguida, pode otimizar as políticas de índice para caminhos de dentro da árvore. Pode optar por incluir ou excluir caminhos da indexação. Isso pode oferecer um desempenho melhorado de escrita e diminuir o armazenamento de índice para cenários em que os padrões de consulta são conhecidos em frente. Para obter mais informações, consulte [caminhos de índice](index-paths.md).

## <a name="indexing-under-the-hood"></a>Indexação: Nos bastidores

Aplica-se de base de dados do Azure Cosmos *indexação automática* aos dados, onde cada caminho numa árvore é indexado, a menos que configure para excluir os caminhos de determinados.

Emprega de base de dados do Azure Cosmos invertido estrutura de dados do índice para armazenar as informações de cada item e para facilitar a representação eficiente para consultas. A árvore de índice é um documento que é construído de acordo com a União de todas as árvores que representa os itens individuais num contentor. A árvore de índice aumenta ao longo do tempo, à medida que novos itens são adicionados ou itens existentes são atualizados no contentor. Ao contrário de indexação do banco de dados relacional, Azure Cosmos DB não reinicie a indexação do zero, como novos campos são introduzidos. Novos itens são adicionados para a estrutura de índice existente. 

Cada nó da árvore de índice é uma entrada de índice que contém os valores de etiqueta e a posição, chamados de *termo*e os IDs dos itens, chamados a *postagens*. Os lançamentos em chaves de saída (por exemplo {1,2}) na figura índice invertida correspondem aos itens tais como *Document1* e *Document2* que contém o valor de etiqueta determinado. Uma implicação importante de tratar as etiquetas de esquema e os valores de instância de maneira uniforme é que tudo o que é fornecido dentro de um índice grande. Um valor de instância que ainda está em folhas não se repita, pode ter diferentes funções em itens, com as etiquetas de esquema diferente, mas é o mesmo valor. A imagem seguinte mostra a indexação invertida para dois itens diferentes:

![Indexação nos bastidores, invertido índice](./media/index-overview/inverted-index.png)

> [!NOTE]
> O índice invertido pode ter um aspeto semelhante para as estruturas de indexação usadas num mecanismo de pesquisa no domínio de obtenção de informações. Com esse método, o Azure Cosmos DB permite-lhe pesquisar o banco de dados para qualquer item, independentemente de sua estrutura de esquema.

Para o caminho normalizado, o índice codifica o caminho de encaminhamento todo o caminho da raiz para o valor, juntamente com as informações de tipo do valor. O caminho e o valor são codificados para fornecer vários tipos de indexação, como o intervalo, geográfico, etc. A codificação de valor foi concebida para fornecer o valor exclusivo ou de uma composição de um conjunto de caminhos.

## <a name="querying-with-indexes"></a>Consultar com índices

O índice invertido permite que uma consulta identificar os documentos que correspondem ao predicado de consulta rapidamente. Ao tratar o esquema e os valores de instância uniformemente em termos de caminhos, o índice invertido também é uma árvore. Portanto, o índice e os resultados podem ser serializados para um documento JSON válido e retornados como próprios documentos, conforme forem retornados na representação de árvore. Este método permite recursing sobre os resultados de consulta adicionais. A imagem seguinte ilustra um exemplo de indexação numa consulta ponto:  

![Exemplo de consulta de ponto](./media/index-overview/index-point-query.png)

Para uma consulta de intervalo *GermanTax* é um [função definida pelo utilizador](stored-procedures-triggers-udfs.md#udfs) executado como parte do processamento de consultas. A função definida pelo utilizador é qualquer função de JavaScript registada, que pode fornecer a lógica de programação avançada integrada a consulta. A imagem seguinte ilustra um exemplo de uma consulta de intervalo de indexação:

![Exemplo de consulta de intervalo](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>Passos Seguintes

Leia mais sobre indexação nos seguintes artigos:

- [Política de indexação](index-policy.md)
- [Tipos de índice](index-types.md)
- [Caminhos de índice](index-paths.md)
- [Como gerir a política de indexação](how-to-manage-indexing-policy.md)
