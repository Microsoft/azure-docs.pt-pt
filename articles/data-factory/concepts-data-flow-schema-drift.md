---
title: Descompasso de esquema de fluxo de dados de mapeamento Azure Data Factory
description: Criar fluxos de dados resilientes em Azure Data Factory com descompasso de esquema
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 562daa024985a546ffb49c4da11eace3bc81a659
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314808"
---
# <a name="mapping-data-flow-schema-drift"></a>Mapeando descompasso de esquema de fluxo de dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

O conceito de descompasso de esquema é o caso em que suas fontes geralmente alteram os metadados. Campos, colunas, tipos, etc. podem ser adicionados, removidos ou alterados em tempo real. Sem lidar com descompasso de esquema, seu fluxo de dados se torna vulnerável a alterações nas alterações de fonte de dados upstream. Quando as colunas e os campos de entrada são alterados, os padrões de ETL típicos falham porque tendem a ser vinculados a esses nomes de origem.

Para se proteger contra descompasso de esquema, é importante ter as instalações em uma ferramenta de fluxo de dados para permitir que você, como engenheiro de dados,:

* Definir fontes com nomes de campo mutáveis, tipos de dados, valores e tamanhos
* Definir parâmetros de transformação que podem funcionar com padrões de dados em vez de valores e campos embutidos em código
* Definir expressões que entendem padrões para corresponder campos de entrada, em vez de usar campos nomeados

## <a name="how-to-implement-schema-drift-in-adf-mapping-data-flows"></a>Como implementar a descompasso de esquema em fluxos de dados de mapeamento do ADF
O ADF nativamente dá suporte a esquemas flexíveis que mudam da execução para a execução para que você possa criar uma lógica de transformação de dados genérica sem a necessidade de recompilar os fluxos de dados.

* Escolha "permitir descompasso de esquema" em sua transformação de origem

<img src="media/data-flow/schemadrift001.png" width="400">

* Quando você tiver selecionado essa opção, todos os campos de entrada serão lidos de sua origem em cada execução de fluxo de dados e serão passados por todo o fluxo para o coletor.

* Todas as colunas recentemente detectadas (colunas descompassos) chegarão como tipo de dados de cadeia de caracteres por padrão. Em sua transformação de origem, escolha "inferir tipos de coluna desfeitas" se desejar que o ADF inferir automaticamente os tipos de dados da origem.

* Certifique-se de usar "mapa automático" para mapear todos os novos campos na transformação do coletor para que todos os novos campos sejam selecionados e descarregou em seu destino e defina "permitir descompasso de esquema" no coletor também.

<img src="media/data-flow/automap.png" width="400">

* Tudo funcionará quando novos campos forem introduzidos nesse cenário com um mapeamento simples do coletor de > de origem (cópia).

* Para adicionar transformações nesse fluxo de trabalho que lida com descompasso de esquema, você pode usar a correspondência de padrões para corresponder colunas por nome, tipo e valor.

* Clique em "Adicionar padrão de coluna" na coluna derivada ou transformação agregada se desejar criar uma transformação que entenda "descompasso de esquema".

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> Você precisa tomar uma decisão arquitetônica em seu fluxo de dados para aceitar descompasso de esquema em todo o fluxo. Ao fazer isso, você pode proteger contra alterações de esquema das fontes. No entanto, você perderá a ligação antecipada de suas colunas e tipos em todo o fluxo de dados. Azure Data Factory trata os fluxos de descompasso de esquema como fluxos de ligação tardia, portanto, quando você cria suas transformações, os nomes de coluna não estarão disponíveis para você nas exibições de esquema em todo o fluxo.

<img src="media/data-flow/taxidrift1.png" width="400">

No fluxo de dados de exemplo de demonstração de táxi, há uma descompasso de esquema de exemplo no fluxo de dados inferior com a origem TripFare. Na transformação Agregação, observe que estamos usando o design "padrão de coluna" para os campos de agregação. Em vez de nomear colunas específicas ou procurar colunas por posição, presumimos que os dados possam ser alterados e não apareçam na mesma ordem entre as execuções.

Neste exemplo de Azure Data Factory tratamento de descompasso de esquema de fluxo de dados, criamos e agregamos que examinam as colunas do tipo ' Double ', sabendo que o domínio de dados contém preços para cada viagem. Em seguida, podemos executar um cálculo de matemática agregado em todos os campos duplos na origem, independentemente de onde a coluna chega e independentemente da nomenclatura da coluna.

A sintaxe de fluxo de dados Azure Data Factory usa $ $ para representar cada coluna correspondente do seu padrão correspondente. Você também pode corresponder a nomes de coluna usando funções de expressão regular e de pesquisa de cadeia de caracteres complexa. Nesse caso, vamos criar um novo nome de campo agregado com base em cada correspondência de um tipo "Double" de coluna e acrescentar o texto ```_total``` a cada um desses nomes correspondentes: 

```concat($$, '_total')```

Em seguida, arredondaremos e somaremos os valores para cada uma das colunas correspondentes:

```round(sum ($$))```

Você pode testá-lo com o Azure Data Factory exemplo de fluxo de dados "demonstração de táxi". Alterne para a sessão de depuração usando a alternância de depuração na parte superior da superfície de design do fluxo de dados para que você possa ver os resultados interativamente:

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>Acessar novas colunas downstream
Quando você gera novas colunas com padrões de coluna, você pode acessar essas novas colunas posteriormente em suas transformações de fluxo de dados com estes métodos:

* Use "byPosition" para identificar as novas colunas por número de posição.
* Use "byName" para identificar as novas colunas por seu nome.
* Em padrões de coluna, use "Name", "Stream", "position" ou "Type" ou qualquer combinação deles para corresponder a novas colunas.

## <a name="next-steps"></a>Passos Seguintes
Na [linguagem de expressão de fluxo de dados](data-flow-expression-functions.md) , você encontrará recursos adicionais para padrões de coluna e descompasso de esquema, incluindo "byName" e "byPosition".
