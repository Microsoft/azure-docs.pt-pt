---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 8d12645bd302a7ea808f13c07e2a72f66846bad7
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85121301"
---
Agora pode utilizar a ferramenta Data Explorer no portal Azure para criar um API da Azure Cosmos DB para a base de dados e contentor do MongoDB. 

1. Selecione **data explorer**  >  **novo recipiente**. 
    
    A área **do Recipiente Adicionar** é apresentada na extrema direita, pode ser necessário deslocar-se para a frente para o ver.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png" alt-text="O Data Explorer no portal do Azure, painel Adicionar Contentor":::

2. Na página **do recipiente Adicionar,** introduza as definições para o novo recipiente.

    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**ID da Base de Dados**|db|*Insira o db* como o nome da nova base de dados. Os nomes da base de dados devem conter de 1 a 255 caracteres, não podendo conter `/, \\, #, ?` , ou um espaço de fuga. Consulte a opção **de produção da base de dados Provision,** que lhe permite partilhar o produto que está previsto na base de dados em todos os contentores da base de dados. Esta opção também ajuda na poupança de custos. |
    |**Débito**|400|Deixe a produção a 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde. Também pode escolher [o modo Autoscale,](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale)que lhe dará uma gama de RU/s que irão aumentar e diminuir de forma dinâmica, conforme necessário.| 
    |**ID da Coleção**|coll|Insira `coll` como o nome do seu novo recipiente. Os IDs dos contentores têm os mesmos requisitos em termos de carateres que os nomes das bases de dados.|
    |**Capacidade de armazenamento**|Fixo (10GB)|Introduza *Fixo (10GB)* para esta aplicação. Se selecionar *Unlimited,* terá de criar um `Shard Key` , que todos os itens inseridos exigirão.|
    |**Chave de fragmento**| /_id| A amostra descrita neste artigo não utiliza uma chave de fragmentos, por isso, definindo-a para */_id* utilizará o campo de ID gerado automaticamente como tecla de fragmento. Saiba mais sobre o fragmento, também conhecido como partição, em [Partition in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)|
        
    Selecione **OK**. O Data Explorer mostra a base de dados e o contentor novos.
