---
title: Use chaves únicas em Azure Cosmos DB
description: Aprenda a definir e usar chaves únicas para uma base de dados Azure Cosmos. Este artigo também descreve como as chaves únicas adicionam uma camada de integridade de dados.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: f234579c6fb2b6f1bc0cd518b87ea69fae30093a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74869838"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Principais constrangimentos únicos em Azure Cosmos DB

As teclas únicas adicionam uma camada de integridade de dados a um recipiente Azure Cosmos. Cria-se uma política-chave única quando se cria um recipiente Azure Cosmos. Com chaves únicas, certifique-se de que um ou mais valores dentro de uma divisória lógica são únicos. Também pode garantir a singularidade por [chave de partição.](partition-data.md)

Depois de criar um recipiente com uma política chave única, a criação de um novo ou uma atualização de um item existente que resulte numa duplicação dentro de uma divisória lógica é evitada, conforme especificado pela restrição chave única. A chave de partição combinada com a chave única garante a singularidade de um item no âmbito do recipiente.

Por exemplo, considere um recipiente Azure Cosmos com `CompanyID` endereço de e-mail como o constrangimento chave único e como a chave de partição. Quando configura o endereço de e-mail do utilizador com uma chave única, `CompanyID`cada item tem um endereço de e-mail único dentro de um dado . Dois itens não podem ser criados com endereços de e-mail duplicados e com o mesmo valor-chave de partição. 

Para criar itens com o mesmo endereço de e-mail, mas não o mesmo nome, apelido e endereço de e-mail, adicione mais caminhos à política chave única. Em vez de criar uma chave única com base apenas no endereço de e-mail, também pode criar uma chave única com uma combinação do primeiro nome, apelido e endereço de e-mail. Esta chave é conhecida como uma chave única composta. Neste caso, cada combinação única dos `CompanyID` três valores dentro de um dado é permitida. 

Por exemplo, o recipiente pode conter itens com os seguintes valores, onde cada item honra a restrição de chave única.

|CompanyID|Nome próprio|Apelido|Endereço de e-mail|
|---|---|---|---|
|Contoso|Gaby|Rio Duperre|gaby@contoso.com |
|Contoso|Gaby|Rio Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Rio Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Rio Duperre|gaby@fabrikam.com|
|Fabrkam|   |Rio Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Se tentar inserir outro item com as combinações listadas na tabela anterior, receberá um erro. O erro indica que a chave única não foi cumprida. Recebes `Resource with specified ID or name already exists` ou `Resource with specified ID, name, or unique index already exists` como mensagem de regresso. 

## <a name="define-a-unique-key"></a>Defina uma chave única

Só pode definir chaves únicas quando criar um recipiente Azure Cosmos. Uma chave única é traçada para uma partição lógica. No exemplo anterior, se dividir o recipiente com base no código ZIP, acaba com itens duplicados em cada partição lógica. Considere as seguintes propriedades quando criar chaves únicas:

* Não é possível atualizar um recipiente existente para utilizar uma chave única diferente. Por outras palavras, depois de um contentor ser criado com uma política-chave única, a política não pode ser alterada.

* Para definir uma chave única para um recipiente existente, crie um novo recipiente com a restrição de chave única. Utilize a ferramenta adequada de migração de dados para mover os dados do contentor existente para o novo recipiente. Para os recipientes SQL, utilize a [ferramenta Data Migration](import-data.md) para mover dados. Para os recipientes MongoDB, utilize [mongoimport.exe ou mongorestore.exe](mongodb-migrate.md) para mover dados.

* Uma política-chave única pode ter um máximo de 16 valores de percurso. Por exemplo, os `/firstName`valores podem ser, `/lastName`e `/address/zipCode`. Cada política-chave única pode ter um máximo de 10 restrições ou combinações únicas. Os caminhos combinados para cada restrição de índice único não devem exceder 60 bytes. No exemplo anterior, o primeiro nome, o apelido e o endereço de e-mail juntos são uma restrição. Esta restrição utiliza 3 dos 16 caminhos possíveis.

* Quando um recipiente tem uma política chave única, as taxas da [Unidade de Pedido (RU)](request-units.md) para criar, atualizar e apagar um item são ligeiramente mais altas.

* Chaves únicas escassas não são suportadas. Se faltarem alguns valores de caminho únicos, são tratados como valores nulos, que participam na restrição de singularidade. Por esta razão, só pode haver um único item com um valor nulo para satisfazer esta restrição.

* Nomes-chave únicos são sensíveis a casos. Por exemplo, considere um recipiente com `/address/zipcode`a chave única definida para . Se os seus dados `ZipCode`têm um campo chamado , o Azure `zipcode` Cosmos DB insere "nulo" como a chave única porque não é o mesmo que `ZipCode`. Devido a este caso de sensibilidade, todos os outros registos com ZipCode não podem ser inseridos porque o duplicado "nulo" viola a restrição de chave única.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [divisórias lógicas](partition-data.md)
* Explore [como definir chaves únicas](how-to-define-unique-keys.md) ao criar um recipiente
