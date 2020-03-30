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
ms.openlocfilehash: 9bc5be37f3892186233fac197c08066dbfacb43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77780600"
---
Agora pode utilizar a ferramenta Data Explorer no portal Azure para criar uma base de dados e um recipiente. 

1. Selecione **Data Explorer** > **Novo Recipiente**. 
    
    A área **add container** é exibida na extrema-direita, pode ser necessário rolar para a direita para vê-lo.

    ![O Data Explorer no portal do Azure, painel Adicionar Contentor](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. Na página **'Adicionar',** introduza as definições para o novo recipiente.

    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**ID da Base de Dados**|Tarefas|Designe a nova base de dados como *Tarefas*. Os nomes da base de dados devem conter `/, \\, #, ?`de 1 a 255 caracteres, e não podem conter, ou um espaço de fuga. Consulte a opção de entrada da base de **dados Provision,** permite-lhe partilhar a entrada disponibilizada na base de dados em todos os recipientes da base de dados. Esta opção também ajuda na poupança de custos. |
    |**Débito**|400|Deixe a entrada em 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.| 
    |**ID do contentor**|Itens|Introduza *itens* como o nome do seu novo recipiente. Os IDs dos contentores têm os mesmos requisitos em termos de carateres que os nomes das bases de dados.|
    |**Chave de partição**| /categoria| A amostra descrita neste artigo *utiliza/categoria* como chave de partição.|
    
    Além das definições anteriores, pode adicionar **opcionalmente chaves Únicas** para o recipiente. Vamos deixar o campo vazio neste exemplo. As chaves exclusivas oferecem aos programadores a capacidade de adicionar uma camada de integridade dos dados na base de dados. Ao criar uma política chave única ao criar um recipiente, você garante a singularidade de um ou mais valores por chave de partição. Para saber mais, consulte o artigo [Chaves exclusivas no Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Selecione **OK**. O Data Explorer mostra a base de dados e o contentor novos.