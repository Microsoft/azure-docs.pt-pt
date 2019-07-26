---
title: Tipos de resolução de conflito e políticas de resolução com várias regiões de gravação no Azure Cosmos DB
description: Este artigo descreve as categorias de conflito e as políticas de resolução de conflitos no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 45b7257f67be8ba5c134717d73488916056b7a7d
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384208"
---
# <a name="conflict-types-and-resolution-policies"></a>Tipos de conflito e políticas de resolução

Os conflitos e as políticas de resolução de conflitos serão aplicáveis se sua conta de Azure Cosmos DB estiver configurada com várias regiões de gravação.

Para contas do Azure Cosmos configuradas com várias regiões de gravação, podem ocorrer conflitos de atualização quando gravadores atualizam simultaneamente o mesmo item em várias regiões. Os conflitos de atualização podem ser dos três tipos a seguir:

* **Inserir conflitos**: Esses conflitos podem ocorrer quando um aplicativo insere simultaneamente dois ou mais itens com o mesmo índice exclusivo em duas ou mais regiões. Por exemplo, esse conflito pode ocorrer com uma propriedade de ID.

* **Substituir conflitos**: Esses conflitos podem ocorrer quando um aplicativo atualiza o mesmo item simultaneamente em duas ou mais regiões.

* **Excluir conflitos**: Esses conflitos podem ocorrer quando um aplicativo exclui simultaneamente um item em uma região e o atualiza em outra região.

## <a name="conflict-resolution-policies"></a>Políticas de resolução de conflitos

O Azure Cosmos DB oferece um mecanismo flexível orientado por políticas para resolver conflitos de gravação. Você pode selecionar entre duas políticas de resolução de conflitos em um contêiner Cosmos do Azure:

- **Última gravação vence (LWW)** : Essa política de resolução, por padrão, usa uma propriedade Timestamp definida pelo sistema. Ele é baseado no protocolo de relógio de sincronização de tempo. Se você usar a API do SQL, poderá especificar qualquer outra propriedade numérica personalizada (por exemplo, sua própria noção de um carimbo de data/hora) a ser usada na resolução de conflitos. Uma propriedade numérica personalizada também é conhecida como o caminho de *resolução de conflito*. 

  Se dois ou mais itens entrarem em conflito nas operações de inserção ou substituição, o item com o valor mais alto para o caminho de resolução de conflitos se tornará o vencedor. O sistema determinará o vencedor se vários itens tiverem o mesmo valor numérico para o caminho de resolução de conflitos. Todas as regiões têm garantia de convergência para um único vencedor e terminam com a mesma versão do item confirmado. Quando são envolvidos conflitos de exclusão, a versão excluída sempre vence em conflitos de inserção ou substituição. Esse resultado ocorre independentemente do valor do caminho de resolução de conflito.

  > [!NOTE]
  > A última gravação vence é a política de resolução de conflitos padrão. Ele está disponível para as seguintes APIs: SQL, MongoDB, Cassandra, Gremlin e Table.

  Para saber mais, veja [exemplos que usam políticas de resolução de conflitos do LWW](how-to-manage-conflicts.md).

- **Personalizado**: Essa política de resolução é projetada para semântica definida pelo aplicativo para a reconciliação de conflitos. Ao definir essa política no contêiner Cosmos do Azure, você também precisará registrar um *procedimento armazenado*de mesclagem. Esse procedimento é invocado automaticamente quando os conflitos são detectados em uma transação de banco de dados no servidor. O sistema fornece exatamente garantir uma vez para a execução de um procedimento de mesclagem como parte do protocolo de compromisso.  

  Se você configurar o contêiner com a opção de resolução personalizada e não conseguir registrar um procedimento de mesclagem no contêiner ou o procedimento de mesclagem lançar uma exceção em tempo de execução, os conflitos serão gravados no *feed de conflitos*. Em seguida, seu aplicativo precisa resolver manualmente os conflitos no feed de conflitos. Para saber mais, consulte [exemplos de como usar a política de resolução personalizada e como usar o feed de conflitos](how-to-manage-conflicts.md).

  > [!NOTE]
  > A política de resolução de conflito personalizada está disponível somente para contas da API do SQL.

## <a name="next-steps"></a>Passos Seguintes

Saiba como configurar políticas de resolução de conflitos:

* [Como configurar vários mestres em seus aplicativos](how-to-multi-master.md)
* [Como gerenciar políticas de resolução de conflitos](how-to-manage-conflicts.md)
* [Como ler a partir do feed de conflitos](how-to-manage-conflicts.md#read-from-conflict-feed)
