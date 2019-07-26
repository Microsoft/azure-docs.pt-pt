---
title: Usar chaves exclusivas no Azure Cosmos DB
description: Saiba como usar chaves exclusivas em seu banco de dados Cosmos do Azure
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: e5b8eb4d5334eb198ff6699897c56b516ded069e
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467568"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Restrições de chave exclusivas no Azure Cosmos DB

Chaves exclusivas adicionam uma camada de integridade de dados a um contêiner Cosmos do Azure. Você cria uma política de chave exclusiva ao criar um contêiner Cosmos do Azure. Com chaves exclusivas, você garante que um ou mais valores em uma partição lógica sejam exclusivos. Você também pode garantir a exclusividade por [chave de partição](partition-data.md). 

Depois de criar um contêiner com uma política de chave exclusiva, a criação de uma nova ou de uma atualização de um item existente, resultando em uma duplicata dentro de uma partição lógica, é impedida, conforme especificado pela restrição UNIQUE KEY. A chave de partição combinada com a chave exclusiva garante a exclusividade de um item dentro do escopo do contêiner.

Por exemplo, considere um contêiner Cosmos do Azure com endereço de email como a restrição de `CompanyID` chave exclusiva e como a chave de partição. Quando você configura o endereço de email do usuário com uma chave exclusiva, cada item tem um endereço de email exclusivo em `CompanyID`um determinado. Dois itens não podem ser criados com endereços de email duplicados e com o mesmo valor de chave de partição. 

Para criar itens com o mesmo endereço de email, mas não com o mesmo nome, sobrenome e endereço de email, adicione mais caminhos à política de chave exclusiva. Em vez de criar uma chave exclusiva com base apenas no endereço de email, você também pode criar uma chave exclusiva com uma combinação de nome, sobrenome e endereço de email. Essa chave é conhecida como uma chave exclusiva composta. Nesse caso, cada combinação exclusiva dos três valores em um determinado `CompanyID` é permitida. 

Por exemplo, o contêiner pode conter itens com os valores a seguir, onde cada item honra a restrição UNIQUE KEY.

|CompanyID|Nome próprio|Apelido|Endereço de e-mail|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Se você tentar inserir outro item com as combinações listadas na tabela anterior, receberá um erro. O erro indica que a restrição de chave exclusiva não foi atendida. Você recebe `Resource with specified ID or name already exists` ou `Resource with specified ID, name, or unique index already exists` como uma mensagem de retorno. 

## <a name="define-a-unique-key"></a>Definir uma chave exclusiva

Você só pode definir chaves exclusivas ao criar um contêiner Cosmos do Azure. Uma chave exclusiva tem como escopo uma partição lógica. No exemplo anterior, se você particionar o contêiner com base no CEP, acabará com itens duplicados em cada partição lógica. Considere as seguintes propriedades ao criar chaves exclusivas:

* Você não pode atualizar um contêiner existente para usar uma chave exclusiva diferente. Em outras palavras, depois que um contêiner é criado com uma política de chave exclusiva, a política não pode ser alterada.

* Para definir uma chave exclusiva para um contêiner existente, crie um novo contêiner com a restrição de chave exclusiva. Use a ferramenta de migração de dados apropriada para mover os dados do contêiner existente para o novo contêiner. Para contêineres SQL, use a [ferramenta de migração de dados](import-data.md) para mover dados. Para contêineres do MongoDB, use [mongoimport. exe ou mongorestore. exe](mongodb-migrate.md) para mover dados.

* Uma política de chave exclusiva pode ter um máximo de 16 valores de caminho. Por exemplo, os valores podem ser `/firstName`, `/lastName`e `/address/zipCode`. Cada política de chave exclusiva pode ter, no máximo, 10 restrições de chave exclusivas ou combinações. Os caminhos combinados para cada restrição de índice exclusivo não devem exceder 60 bytes. No exemplo anterior, nome, sobrenome e endereço de email juntos são uma restrição. Essa restrição usa 3 dos 16 caminhos possíveis.

* Quando um contêiner tem uma política de chave exclusiva, os encargos da [unidade de solicitação (ru)](request-units.md) para criar, atualizar e excluir um item são um pouco mais altos.

* Não há suporte para chaves exclusivas esparsas. Se alguns valores de caminho exclusivos estiverem ausentes, eles serão tratados como valores nulos, que fazem parte da restrição de exclusividade. Por esse motivo, pode haver apenas um único item com um valor nulo para atender a essa restrição.

* Os nomes de chave exclusivos diferenciam maiúsculas de minúsculas. Por exemplo, considere um contêiner com a restrição UNIQUE KEY definida como `/address/zipcode`. Se os dados tiverem um campo chamado `ZipCode`, Azure Cosmos DB inserirá "NULL" como a chave exclusiva `zipcode` porque não é o `ZipCode`mesmo que. Por causa dessa distinção de maiúsculas e minúsculas, todos os outros registros com ZipCode não podem ser inseridos porque a duplicação "NULL" viola a restrição UNIQUE KEY.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [partições lógicas](partition-data.md)
* Explore [como definir chaves exclusivas](how-to-define-unique-keys.md) ao criar um contêiner
