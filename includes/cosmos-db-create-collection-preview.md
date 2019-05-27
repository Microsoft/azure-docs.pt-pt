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
ms.openlocfilehash: c3cbfda674abaeea1adf35c3ee0d2b5ddf6b2f84
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66153755"
---
Agora, pode utilizar a ferramenta Data Explorer no portal do Azure para criar uma base de dados e uma coleção. 

1. Clique em **Explorador de Dados** > **Nova Coleção**. 
    
    A área **Adicionar Coleção** é apresentada na extremidade direita, pelo que poderá ter de se deslocar para a direita para vê-la.

    ![O painel Data Explorer no portal do Azure, Adicionar Coleção](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. Na página **Adicionar coleção**, introduza as definições para a nova coleção.

    Definição|Valor sugerido|Descrição
    ---|---|---
    Id da base de dados|*Tarefas*|Designe a nova base de dados como *Tarefas*. Os nomes das bases de dados devem conter de 1 a 255 carateres e não podem conter /, \\, #, ?, ou um espaço à direita.
    ID da coleção|*itens*|Denomine a nova coleção como *Itens*. Os IDs das coleções têm os mesmos requisitos em termos de carateres do que os nomes das bases de dados.
    Aprovisionar débito de base de dados|Deixar em branco|O Azure Cosmos DB pode aprovisionar o débito no nível de base de dados (todas as coleções numa base de dados partilham o mesmo débito) ou ao nível da coleção. Deixe em branco para aprovisionar a taxa de transferência ao nível da coleção para esta coleção específica.
    Capacidade de armazenamento|*Ilimitado*|Escolha a capacidade de armazenamento **ilimitado**. 
    Chave de partição|*/category*|Introduza "/ categoria" como a chave de partição. Definir uma chave de partição permite que o Azure Cosmos DB para dimensionar a sua coleção para satisfazer as necessidades de armazenamento e débito da sua aplicação. Em geral, uma boa opção de chave de partição é aquele que tem uma vasta gama de valores distintos e os resultados numa distribuição uniforme de volume de armazenamento e a pedido em sua carga de trabalho. [Saiba mais sobre a criação de partições.](../articles/cosmos-db/partitioning-overview.md)
    Débito|*400 RU/s*|Altere o débito para 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde. 
    
    Além das definições anteriores, pode adicionar opcionalmente **Chaves exclusivas** para a coleção. Vamos deixar o campo vazio neste exemplo. As chaves exclusivas oferecem aos programadores a capacidade de adicionar uma camada de integridade dos dados na base de dados. Ao criar uma política de chaves exclusivas durante a criação de uma coleção, está a assegurar a exclusividade de um ou mais valores por chave de partição. Para saber mais, consulte o artigo [Chaves exclusivas no Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Clique em **OK**.

    O Data Explorer mostra a base de dados e a coleção novas.

    ![O Data Explorer do portal do Azure a mostrar a base de dados e a coleção novas](./media/cosmos-db-create-collection/azure-cosmos-db-data-explorer-preview.png)