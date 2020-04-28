---
title: Tipos de resolução de conflitos e políticas de resolução em Azure Cosmos DB
description: Este artigo descreve as categorias de conflitos e as políticas de resolução de conflitos em Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: c3b1cf01cbaa8de8ec33bbf9c19ee991ae898d58
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82182757"
---
# <a name="conflict-types-and-resolution-policies"></a>Tipos de conflito e políticas de resolução

As políticas de conflitos e resolução de conflitos são aplicáveis se a sua conta Azure Cosmos DB estiver configurada com várias regiões de escrita.

Para as contas da Azure Cosmos configuradas com várias regiões de escrita, os conflitos de atualização podem ocorrer quando os escritores atualizam simultaneamente o mesmo item em várias regiões. Os conflitos de atualização podem ser dos seguintes três tipos:

* **Insira conflitos**: Estes conflitos podem ocorrer quando uma aplicação insere simultaneamente dois ou mais itens com o mesmo índice único em duas ou mais regiões. Por exemplo, este conflito pode ocorrer com uma propriedade de identificação.

* **Substituição de conflitos**: Estes conflitos podem ocorrer quando uma aplicação atualiza o mesmo item simultaneamente em duas ou mais regiões.

* **Eliminar conflitos**: Estes conflitos podem ocorrer quando uma aplicação simultaneamente elimina um item numa região e o atualiza noutra região.

## <a name="conflict-resolution-policies"></a>Políticas de resolução de conflitos

A Azure Cosmos DB oferece um mecanismo flexível orientado para a política para resolver conflitos de escrita. Pode selecionar entre duas políticas de resolução de conflitos num contentor Azure Cosmos:

* **Last Write Wins (LWW)**: Esta política de resolução, por padrão, utiliza uma propriedade de carimbo de tempo definido pelo sistema. Baseia-se no protocolo do relógio de sincronização do tempo. Se utilizar a API SQL, pode especificar qualquer outra propriedade numérica personalizada (por exemplo, a sua própria noção de carimbo de tempo) para ser usada para resolução de conflitos. Uma propriedade numérica personalizada também é referida como o caminho de resolução de *conflitos.* 

  Se dois ou mais itens entram em conflito na inserção ou substituição de operações, o item com o valor mais elevado para a trajetória de resolução de conflitos torna-se o vencedor. O sistema determina o vencedor se vários itens têm o mesmo valor numérico para o caminho da resolução de conflitos. Todas as regiões têm a garantia de convergir para um único vencedor e acabar com a mesma versão do item comprometido. Quando se trata de eliminar conflitos, a versão eliminada ganha sempre por inserir ou substituir conflitos. Este resultado ocorre independentemente do valor da trajetória de resolução de conflitos.

  > [!NOTE]
  > Last Write Wins é a política de `_ts` resolução de conflitos padrão e usa o carimbo de tempo para as seguintes APIs: SQL, MongoDB, Cassandra, Gremlin e Table. A propriedade numérica personalizada está disponível apenas para SQL API.

  Para saber mais, consulte exemplos que utilizam políticas de [resolução de conflitos LWW](how-to-manage-conflicts.md).

* **Costume**: Esta política de resolução destina-se a uma semântica definida pela aplicação para a reconciliação de conflitos. Quando definir esta política no seu recipiente Azure Cosmos, também precisa de registar um *procedimento de fusão armazenado.* Este procedimento é automaticamente invocado quando os conflitos são detetados no âmbito de uma transação de base de dados no servidor. O sistema fornece exatamente uma vez garantia para a execução de um procedimento de fusão como parte do protocolo de compromisso.  

  Se configurar o seu recipiente com a opção de resolução personalizada, e não registar um procedimento de fusão no recipiente ou o procedimento de fusão lançar uma exceção no tempo de execução, os conflitos são escritos para a alimentação de *conflitos*. A sua aplicação precisa então de resolver manualmente os conflitos nos conflitos que se alimentam. Para saber mais, consulte exemplos de como utilizar a política de [resolução personalizada e como utilizar os alimentos para conflitos](how-to-manage-conflicts.md).

  > [!NOTE]
  > A política de resolução de conflitos personalizada está disponível apenas para contas SQL API.

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar políticas de resolução de conflitos:

* [Como configurar o multi-master nas suas aplicações](how-to-multi-master.md)
* [Como gerir políticas de resolução de conflitos](how-to-manage-conflicts.md)
* [Como ler a partir dos conflitos alimentam-se](how-to-manage-conflicts.md#read-from-conflict-feed)
