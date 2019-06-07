---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/19/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 331886f01345aba576cd8f96f95077f9bbdae704
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754352"
---
Agora, pode utilizar a ferramenta Data Explorer no portal do Azure para criar uma base de dados e um contentor. 

1. Clique em **o Data Explorer** > **novo contentor**. 
    
    O **adicionar contentor** área é apresentada na extremidade direita, poderá ter de se deslocar para a direita para vê-lo.

    ![O portal do Azure Data Explorer, no painel Adicionar contentor](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. Na **adicionar contentor** página, introduza as definições para o novo contentor.

    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**ID da Base de Dados**|Tarefas|Designe a nova base de dados como *Tarefas*. Os nomes das bases de dados devem conter de 1 a 255 carateres e não podem conter /, \\, #, ?, ou um espaço à direita. Verifique os **débito de base de dados de aprovisionamento** opção, permite-lhe partilhar o débito aprovisionado para a base de dados em todos os contentores na base de dados. Esta opção também ajuda a com redução de custos. |
    |**Débito**|400|Deixe o débito com 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.| 
    |**ID de contentor**|Itens|Introduza *itens* como o nome para o novo contentor. IDs de contentor têm os mesmos requisitos como nomes de base de dados.|
    |**Chave de partição**| /categoria| O exemplo descrito neste artigo usa */category* como a chave de partição. Definir uma chave de partição permite que o Azure Cosmos DB para dimensionar a sua coleção para satisfazer as necessidades de armazenamento e débito da sua aplicação. Em geral, uma boa opção de chave de partição é aquele que tem uma vasta gama de valores distintos e os resultados numa distribuição uniforme de volume de armazenamento e a pedido em sua carga de trabalho. [Saiba mais sobre a criação de partições.](../articles/cosmos-db/partitioning-overview.md)|
    
    Além das configurações anteriores, pode adicionar opcionalmente **chaves exclusivas** do contentor. Vamos deixar o campo vazio neste exemplo. As chaves exclusivas oferecem aos programadores a capacidade de adicionar uma camada de integridade dos dados na base de dados. Ao criar uma política de chaves exclusivas durante a criação de um contentor, garantir a exclusividade de um ou mais valores por chave de partição. Para saber mais, consulte o artigo [Chaves exclusivas no Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Selecione **OK**. O Data Explorer mostra a nova base de dados e o contentor.

