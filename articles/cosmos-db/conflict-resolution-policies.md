---
title: Tipos de resolução de conflitos e políticas de resolução em Azure Cosmos DB
description: Este artigo descreve as categorias de conflitos e as políticas de resolução de conflitos na Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ba55d88de3a5a4087db30613b22a7d2441de9be1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334383"
---
# <a name="conflict-types-and-resolution-policies-when-using-multiple-write-regions"></a>Tipos de conflitos e políticas de resolução ao utilizar várias regiões de escrita
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Os conflitos e as políticas de resolução de conflitos são aplicáveis se a sua conta DB Azure Cosmos estiver configurada com várias regiões de escrita.

Para as contas de Azure Cosmos configuradas com várias regiões de escrita, os conflitos de atualização podem ocorrer quando os escritores atualizam simultaneamente o mesmo item em várias regiões. Os conflitos de atualização podem ser dos seguintes três tipos:

* **Inserir conflitos**: Estes conflitos podem ocorrer quando uma aplicação insere simultaneamente dois ou mais itens com o mesmo índice único em duas ou mais regiões. Por exemplo, este conflito pode ocorrer com uma propriedade de ID.

* **Substituir conflitos**: Estes conflitos podem ocorrer quando uma aplicação atualiza o mesmo item simultaneamente em duas ou mais regiões.

* **Eliminar conflitos**: Estes conflitos podem ocorrer quando uma aplicação elimina simultaneamente um item numa região e atualiza-o noutra região.

## <a name="conflict-resolution-policies"></a>Políticas de resolução de conflitos

A Azure Cosmos DB oferece um mecanismo flexível orientado pela política para resolver conflitos de escrita. Pode selecionar entre duas políticas de resolução de conflitos num contentor Azure Cosmos:

* **Últimas Vitórias de Escrita (LWW)**: Esta política de resolução, por padrão, utiliza uma propriedade de timetamp definida pelo sistema. Baseia-se no protocolo do relógio de sincronização temporal. Se utilizar a API SQL, pode especificar qualquer outra propriedade numérica personalizada (por exemplo, a sua própria noção de um timetamp) para ser usada para a resolução de conflitos. Uma propriedade numérica personalizada também é referida como o caminho de *resolução de conflitos.* 

  Se dois ou mais itens entrar em conflito na inserção ou substituição de operações, o item com o valor mais elevado para a rota de resolução de conflitos torna-se o vencedor. O sistema determina o vencedor se vários itens itens tiverem o mesmo valor numérico para o caminho de resolução de conflitos. Todas as regiões convergirão para um único vencedor e acabarão com a mesma versão do item comprometido. Quando se apagam conflitos, a versão eliminada vence sempre quer insira quer substitua conflitos. Este resultado ocorre independentemente do valor da via de resolução de conflitos.

  > [!NOTE]
  > Last Write Wins é a política de resolução de conflitos padrão e usa o tempo para `_ts` as seguintes APIs: SQL, MongoDB, Cassandra, Gremlin e Table. Propriedade numérica personalizada está disponível apenas para API SQL.

  Para saber mais, consulte [exemplos que utilizam políticas de resolução de conflitos LWW.](how-to-manage-conflicts.md)

* **Costume**: Esta política de resolução destina-se à semântica definida pela aplicação para a reconciliação de conflitos. Ao definir esta política no seu contentor Azure Cosmos, também precisa de registar um *procedimento armazenado de fusão*. Este procedimento é automaticamente invocado quando os conflitos são detetados numa transação de base de dados no servidor. O sistema fornece exatamente uma garantia única para a execução de um procedimento de fusão como parte do protocolo de compromisso.  

  Se configurar o seu recipiente com a opção de resolução personalizada, e não registar um procedimento de fusão no contentor ou o procedimento de fusão abrir uma exceção no tempo de funcionamento, os conflitos são escritos para o *feed de conflitos*. A sua aplicação precisa então de resolver manualmente os conflitos nos conflitos. Para saber mais, veja [exemplos de como utilizar a política de resolução personalizada e como utilizar os conflitos alimentados.](how-to-manage-conflicts.md)

  > [!NOTE]
  > A política de resolução de conflitos personalizada está disponível apenas para contas API SQL.

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar políticas de resolução de conflitos:

* [Como configurar várias regiões de escrita para as suas aplicações](how-to-multi-master.md)
* [Como gerir políticas de resolução de conflitos](how-to-manage-conflicts.md)
* [Como ler a partir dos conflitos alimentam-se](how-to-manage-conflicts.md#read-from-conflict-feed)
