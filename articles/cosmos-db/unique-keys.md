---
title: Use chaves únicas em Azure Cosmos DB
description: Saiba como definir e usar chaves únicas para uma base de dados Azure Cosmos. Este artigo também descreve como as teclas únicas adicionam uma camada de integridade de dados.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 9eb2b916bfe6c73a1535afb077b04fbb081dd5f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685725"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Constrangimentos fundamentais únicos no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Chaves únicas adicionam uma camada de integridade de dados a um recipiente Azure Cosmos. Cria-se uma política chave única quando se cria um recipiente Azure Cosmos. Com teclas únicas, certifique-se de que um ou mais valores dentro de uma divisória lógica são únicos. Também pode garantir a singularidade por [tecla de partição.](partitioning-overview.md)

Depois de criar um recipiente com uma política chave única, a criação de um novo ou uma atualização de um item existente que resulte numa duplicação dentro de uma partição lógica é impedida, conforme especificado pelo constrangimento chave único. A chave de partição combinada com a chave única garante a singularidade de um item no âmbito do recipiente.

Por exemplo, considere um recipiente Azure Cosmos `Email address` como o constrangimento chave único e como a chave de `CompanyID` partição. Ao configurar o endereço de e-mail do utilizador com uma chave única, cada item tem um endereço de e-mail único dentro de um `CompanyID` dado. Dois itens não podem ser criados com endereços de e-mail duplicados e com o mesmo valor de chave de partição. Na API SQL (Core) da Azure Cosmos DB, os itens são armazenados como valores JSON. Estes valores JSON são sensíveis a casos. Ao escolher um imóvel como uma chave única, pode inserir valores sensíveis a casos para essa propriedade. Por exemplo, Se tiver uma chave única definida na propriedade do nome, "Gaby" é diferente de "gaby" e pode inserir ambos no recipiente.

Para criar itens com o mesmo endereço de e-mail, mas não o mesmo nome, apelido e endereço de e-mail, adicione mais caminhos à política chave única. Em vez de criar uma chave única baseada apenas no endereço de e-mail, também pode criar uma chave única com uma combinação do primeiro nome, apelido e endereço de e-mail. Esta chave é conhecida como uma chave única composta. Neste caso, é permitida cada combinação única dos três valores dentro de um `CompanyID` dado. 

Por exemplo, o recipiente pode conter itens com os seguintes valores, onde cada item honra a restrição única da chave.

|EmpresaID|Nome próprio|Apelido|Endereço de e-mail|
|---|---|---|---|
|Contoso|Rio Gaby|Duperre|gaby@contoso.com |
|Contoso|Rio Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Rio Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Se tentar inserir outro item com as combinações listadas na tabela anterior, receberá um erro. O erro indica que a chave única não foi cumprida. Recebes `Resource with specified ID or name already exists` uma mensagem de `Resource with specified ID, name, or unique index already exists` retorno ou devolução. 

## <a name="define-a-unique-key"></a>Definir uma chave única

Só pode definir chaves únicas quando criar um recipiente Azure Cosmos. Uma chave única é traçada para uma divisória lógica. No exemplo anterior, se dividir o recipiente com base no código POSTAL, acaba por ter itens duplicados em cada partição lógica. Considere as seguintes propriedades quando criar teclas únicas:

* Não é possível atualizar um recipiente existente para utilizar uma chave única diferente. Por outras palavras, depois de um recipiente ser criado com uma política chave única, a política não pode ser alterada.

* Para definir uma chave única para um recipiente existente, crie um novo recipiente com o constrangimento chave único. Utilize a ferramenta de migração de dados adequada para mover os dados do recipiente existente para o novo recipiente. Para recipientes SQL, utilize a [ferramenta de migração de dados](import-data.md) para mover dados. Para os recipientes MongoDB, utilize [mongoimport.exe ou mongorestore.exe](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) para mover dados.

* Uma política chave única pode ter um máximo de 16 valores de caminho. Por exemplo, os valores podem ser `/firstName` `/lastName` , e `/address/zipCode` . Cada política chave única pode ter um máximo de 10 restrições ou combinações únicas. Os caminhos combinados para cada restrição de índice único não devem exceder 60 bytes. No exemplo anterior, o primeiro nome, apelido e endereço de e-mail juntos são um constrangimento. Esta restrição utiliza 3 dos 16 caminhos possíveis.

* Quando um recipiente tem uma política de chave única, os encargos [da Unidade de Pedido (RU)](request-units.md) para criar, atualizar e eliminar um item são ligeiramente mais elevados.

* As teclas únicas esparsas não são suportadas. Se alguns valores de caminho únicos estão faltando, eles são tratados como valores nulos, que participam na restrição de singularidade. Por esta razão, só pode haver um único item com um valor nulo para satisfazer esta restrição.

* Nomes-chave únicos são sensíveis a casos. Por exemplo, considere um recipiente com o constrangimento exclusivo da chave definida para `/address/zipcode` . Se os seus dados ímiam um campo chamado `ZipCode` , a Azure Cosmos DB insere "nulo" como a chave única, porque `zipcode` não é a mesma que `ZipCode` . Devido a este caso, todos os outros registos com ZipCode não podem ser inseridos porque o "nulo" duplicado viola o constrangimento exclusivo da chave.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [divisórias lógicas](partitioning-overview.md)
* Explore [como definir chaves únicas ao](how-to-define-unique-keys.md) criar um recipiente
