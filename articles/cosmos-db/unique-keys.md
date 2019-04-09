---
title: Utilizar chaves exclusivas no Azure Cosmos DB
description: Saiba como utilizar chaves exclusivas na sua base de dados do Cosmos do Azure
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.reviewer: sngun
ms.openlocfilehash: 3c5e8a2c85898175772dc353258e77fc8e0a74f2
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263235"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Restrições de chave exclusivas no Azure Cosmos DB

Chaves exclusivas adicionarem uma camada de integridade dos dados para um contentor do Cosmos do Azure. Criar uma política de chaves exclusivas quando criar um contentor do Cosmos do Azure. Com as chaves exclusivas, certificar-se de que um ou mais valores dentro de uma partição lógica é exclusivo. Também pode garantir a exclusividade por [chave de partição](partition-data.md). 

Depois de criar um contentor com uma política de chaves exclusivas, é evitada a criação de um novo ou uma atualização de um item existente, resultando num duplicado dentro de uma partição lógica, conforme especificado pela restrição de chave exclusiva. A chave de partição combinada com a chave exclusiva garante a exclusividade de um item no âmbito do contentor.

Por exemplo, considere um contentor do Cosmos do Azure com o endereço de e-mail como a restrição de chave exclusivo e `CompanyID` como a chave de partição. Quando configurar o endereço de e-mail do utilizador com uma chave exclusiva, cada item tem um endereço de e-mail exclusivo dentro de um determinado `CompanyID`. Não não possível criar dois itens com endereços de e-mail duplicados e com o mesmo valor de chave de partição. 

Para criar itens com a mensagem de e-mail mesmo endereço, mas não o mesmo nome, sobrenome e endereço de e-mail, adicionar mais caminhos para a política de chaves exclusivas. Em vez de criar uma chave exclusiva com base no endereço de e-mail, apenas, também pode criar uma chave exclusiva com uma combinação do nome próprio, apelido e endereço de e-mail. Esta chave é conhecida como uma chave exclusiva composta. Neste caso, cada combinação exclusiva dos três valores dentro de um determinado `CompanyID` é permitido. 

Por exemplo, o contentor pode conter itens com os seguintes valores, onde cada item honra a restrição de chave exclusiva.

|CompanyID|Nome Próprio|Apelido|Endereço de e-mail|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Se tentar inserir outro item com as combinações listadas na tabela anterior, receberá um erro. O erro indica que a restrição de chave exclusiva não cumprida. Recebê-las `Resource with specified ID or name already exists` ou `Resource with specified ID, name, or unique index already exists` como uma mensagem de retorna. 

## <a name="define-a-unique-key"></a>Definir uma chave exclusiva

Pode definir chaves exclusivas apenas quando é criar um contentor do Cosmos do Azure. Uma chave exclusiva é confinada a uma partição lógica. No exemplo anterior, se dividir o contentor com base no CEP, acaba com itens duplicados em cada partição lógica. Quando criar chaves exclusivas, considere as seguintes propriedades:

* Não é possível atualizar um contentor existente para utilizar uma chave exclusiva diferente. Em outras palavras, é criado um contentor com uma política de chaves exclusivas, a política não pode ser alterada.

* Para definir uma chave exclusiva para um contentor existente, crie um novo contentor com a restrição de chave exclusiva. Utilize a ferramenta de migração de dados apropriados para mover os dados do contêiner existente para o novo contentor. Para contentores SQL, utilize o [ferramenta de migração de dados](import-data.md) para mover dados. Para contentores do MongoDB, utilize [mongoimport.exe ou mongorestore.exe](mongodb-migrate.md) para mover dados.

* Uma política de chaves exclusivas pode ter um máximo de 16 valores de caminho. Por exemplo, os valores podem ser `/firstName`, `/lastName`, e `/address/zipCode`. Cada política de chaves exclusivas pode ter um máximo de 10 restrições de chave exclusivas ou combinações. Os caminhos de combinado para cada restrição de índice exclusivo não pode exceder 60 bytes. No exemplo anterior, nome próprio, apelido e endereço de e-mail em conjunto são uma restrição. Esta restrição utiliza 3 fora os 16 caminhos possíveis.

* Quando um contentor tem uma política de chaves exclusivas [unidade de pedido (RU)](request-units.md) custos para criar, atualizar e eliminar um item são um pouco mais alta.

* Chaves exclusivas dispersas não são suportadas. Se faltam alguns valores de caminho exclusivo, eles são tratados como valores nulos, o que faça parte da restrição de exclusividade. Por esse motivo, pode haver apenas um único item com um valor nulo para satisfazer esta restrição.

* Nomes de chave exclusivos diferenciam maiúsculas de minúsculas. Por exemplo, considere um contentor com a restrição de chave exclusiva definida como `/address/zipcode`. Se os dados tiverem um campo chamado `ZipCode`, do Azure Cosmos DB, insere "null" como uma chave exclusiva porque `zipcode` não é igual `ZipCode`. Devido a este maiúsculas e minúsculas, todos os outros registos com ZipCode não não possível inserir uma vez que o duplicado "null" viola a restrição de chave exclusiva.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [partições lógicas](partition-data.md).
