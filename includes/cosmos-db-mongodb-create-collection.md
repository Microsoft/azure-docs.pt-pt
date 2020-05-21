---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 55236e3083ded0e8e07afd406e87f61f53451e9d
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701696"
---
Agora pode utilizar a ferramenta Data Explorer no portal Azure para criar uma API do Azure Cosmos DB para base de dados e contentor MongoDB. 

1. Selecione **Data Explorer**  >  **Novo Recipiente**. 
    
    A área **add container** é exibida na extrema-direita, pode ser necessário rolar para a direita para vê-lo.

    ![O Data Explorer no portal do Azure, painel Adicionar Contentor](./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png)

2. Na página **'Adicionar',** introduza as definições para o novo recipiente.

    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**ID da Base de Dados**|db|*Introduza* db como o nome da nova base de dados. Os nomes da base de dados devem conter de 1 a 255 caracteres, e não podem `/, \\, #, ?` conter, ou um espaço de fuga. Consulte a opção de entrada da base de **dados Provision,** permite-lhe partilhar a entrada disponibilizada na base de dados em todos os recipientes da base de dados. Esta opção também ajuda na poupança de custos. |
    |**Débito**|400|Deixe a entrada em 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde. Também pode escolher o [modo Autopilot](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autopilot), que lhe dará uma gama de RU/s que irá aumentar e diminuir dinamicamente conforme necessário.| 
    |**ID da Coleção**|col|Insira o *coll* como o nome do seu novo recipiente. Os IDs dos contentores têm os mesmos requisitos em termos de carateres que os nomes das bases de dados.|
    |**Capacidade de armazenamento**|Fixo (10GB)|Insira *Fixo (10GB)* para esta aplicação. Se selecionar *Ilimitado,* terá de criar um `Shard Key` , que todos os itens inseridos exigirão.|
    |**Chave do fragmento**| /_id| A amostra descrita neste artigo não utiliza uma Chave de Fragmento, pelo que a definição para */_id* utilizará o campo de identificação gerado automaticamente como chave de fragmentos. Saiba mais sobre sharding, também conhecido como partição, em [Partição em Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)|
        
    Selecione **OK**. O Data Explorer mostra a base de dados e o contentor novos.