---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 37cdb6b466417add8dae69464304ce2f32247c8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95562282"
---
Agora pode utilizar a ferramenta Data Explorer no portal Azure para criar um API da Azure Cosmos DB para a base de dados e contentor do MongoDB. 

1. Selecione **data explorer**  >  **novo recipiente**. 
    
    A área **do Recipiente Adicionar** é apresentada na extrema direita, pode ser necessário deslocar-se para a frente para o ver.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png" alt-text="O Data Explorer no portal do Azure, painel Adicionar Contentor":::

2. Na página **do recipiente Adicionar,** introduza as definições para o novo recipiente.

    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**ID da Base de Dados**|db|*Insira o db* como o nome da nova base de dados. Os nomes da base de dados devem conter de 1 a 255 caracteres, não podendo conter `/, \\, #, ?` , ou um espaço de fuga. Consulte a opção **de produção da base de dados Provision,** que lhe permite partilhar o produto que está previsto na base de dados em todos os contentores da base de dados. Esta opção também ajuda na poupança de custos. |
    |**Débito**|400|Deixe a produção a 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar verticalmente o débito mais tarde. Também pode escolher [o modo Autoscale,](../articles/cosmos-db/provision-throughput-autoscale.md)que lhe dará uma gama de RU/s que irão aumentar e diminuir de forma dinâmica, conforme necessário.| 
    |**ID da Coleção**|coll|Insira `coll` como o nome do seu novo recipiente. Os IDs dos contentores têm os mesmos requisitos em termos de carateres que os nomes das bases de dados.|
    |**Capacidade de armazenamento**|Fixo (10GB)|Introduza *Fixo (10GB)* para esta aplicação. Se selecionar *Unlimited,* terá de criar um `Shard Key` , que todos os itens inseridos exigirão.|
    |**Chave de fragmento**| /_id| A amostra descrita neste artigo não utiliza uma chave de fragmentos, por isso, definindo-a para  */_id* utilizará o campo de ID gerado automaticamente como tecla de fragmento. Saiba mais sobre o fragmento, também conhecido como partição, em [Partition in Azure Cosmos DB](../articles/cosmos-db/partitioning-overview.md)|
        
    Selecione **OK**. O Data Explorer mostra a base de dados e o contentor novos.