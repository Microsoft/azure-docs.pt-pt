---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 076afcfc8abf4e90d3ccedbb26653656032e59be
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85115397"
---
Pode agora utilizar a ferramenta Data Explorer no portal Azure para criar uma base de dados e um recipiente. 

1. Selecione **data explorer**  >  **novo recipiente**. 
    
    A área **do Recipiente Adicionar** é apresentada na extrema direita, pode ser necessário deslocar-se para a frente para o ver.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png" alt-text="O Data Explorer no portal do Azure, painel Adicionar Contentor":::

2. Na página **do recipiente Adicionar,** introduza as definições para o novo recipiente.

    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**ID da Base de Dados**|Tarefas|Designe a nova base de dados como *Tarefas*. Os nomes da base de dados devem conter de 1 a 255 caracteres, não podendo conter `/, \\, #, ?` , ou um espaço de fuga. Consulte a opção **de produção da base de dados Provision,** que lhe permite partilhar o produto que está previsto na base de dados em todos os contentores da base de dados. Esta opção também ajuda na poupança de custos. |
    |**Débito**|400|Deixe a produção a 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar verticalmente o débito mais tarde.| 
    |**ID do Contentor**|Itens|*Insira os Itens* como o nome do seu novo recipiente. Os IDs dos contentores têm os mesmos requisitos em termos de carateres que os nomes das bases de dados.|
    |**Chave de partição**| /categoria| A amostra descrita neste artigo *utiliza/categoria* como chave de partição.|
    
    Além das definições anteriores, pode opcionalmente adicionar **teclas Únicas** para o recipiente. Vamos deixar o campo vazio neste exemplo. As chaves exclusivas oferecem aos programadores a capacidade de adicionar uma camada de integridade dos dados na base de dados. Ao criar uma política chave única ao criar um recipiente, garante a singularidade de um ou mais valores por chave de partição. Para saber mais, consulte o artigo [Chaves exclusivas no Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Selecione **OK**. O Data Explorer mostra a base de dados e o contentor novos.