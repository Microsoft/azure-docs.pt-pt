---
title: Descompasso de esquema no fluxo de dados de mapeamento | Azure Data Factory
description: Criar fluxos de dados resilientes em Azure Data Factory com descompasso de esquema
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 1d6560613294584c77f002e2380065d64ea143f7
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387958"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Descompasso de esquema no fluxo de dados de mapeamento



A descompasso de esquema é o caso em que suas fontes geralmente alteram metadados. Campos, colunas e tipos podem ser adicionados, removidos ou alterados em tempo real. Sem lidar com descompasso de esquema, seu fluxo de dados se torna vulnerável a alterações de fonte de dados upstream. Padrões de ETL típicos falham quando colunas e campos de entrada são alterados porque tendem a ser vinculados a esses nomes de origem.

Para se proteger contra descompasso de esquema, é importante ter as instalações em uma ferramenta de fluxo de dados para permitir que você, como engenheiro de dados,:

* Definir fontes com nomes de campo mutáveis, tipos de dados, valores e tamanhos
* Definir parâmetros de transformação que podem funcionar com padrões de dados em vez de valores e campos embutidos em código
* Definir expressões que entendem padrões para corresponder campos de entrada, em vez de usar campos nomeados

Azure Data Factory nativamente dá suporte a esquemas flexíveis que mudam da execução para a execução para que você possa criar uma lógica de transformação de dados genérica sem a necessidade de recompilar os fluxos de dados.

Você precisa tomar uma decisão arquitetônica em seu fluxo de dados para aceitar descompasso de esquema em todo o fluxo. Ao fazer isso, você pode proteger contra alterações de esquema das fontes. No entanto, você perderá a ligação antecipada de suas colunas e tipos em todo o fluxo de dados. Azure Data Factory trata os fluxos de descompasso de esquema como fluxos de ligação tardia, portanto, quando você cria suas transformações, os nomes de coluna desfeitos não estarão disponíveis para você nas exibições de esquema em todo o fluxo.

## <a name="schema-drift-in-source"></a>Descompasso de esquema na origem

Em uma transformação de origem, a descompasso de esquema é definida como uma leitura de colunas que não definem seu esquema de conjunto de linhas. Para habilitar a descompasso de esquema, marque **permitir descompasso de esquema** em sua transformação de origem.

![Origem de descompasso de esquema](media/data-flow/schemadrift001.png "Origem de descompasso de esquema")

Quando a descompasso de esquema está habilitada, todos os campos de entrada são lidos de sua origem durante a execução e passados por todo o fluxo para o coletor. Por padrão, todas as colunas recentemente detectadas, conhecidas como *colunas descompassos*, chegam como um tipo de dados de cadeia de caracteres. Se você quiser que o fluxo de dados infira automaticamente os tipos de dados de colunas desfeitas, marque **inferir tipos de coluna** desfeitas em suas configurações de origem.

## <a name="schema-drift-in-sink"></a>Descompasso de esquema no coletor

Em uma transformação de coletor, o descompasso de esquema é quando você grava colunas adicionais sobre o que é definido no esquema de dados do coletor. Para habilitar a descompasso de esquema, marque **permitir descompasso de esquema** na transformação do coletor.

![Coletor de descompasso de esquema](media/data-flow/schemadrift002.png "Coletor de descompasso de esquema")

Se a descompasso de esquema estiver habilitada, verifique se o controle deslizante de **mapeamento automático** na guia mapeamento está ativado. Com esse controle deslizante ativado, todas as colunas de entrada são gravadas no destino. Caso contrário, você deve usar o mapeamento baseado em regras para gravar colunas descompassos.

![Mapeamento automático do coletor](media/data-flow/automap.png "Mapeamento automático do coletor")

## <a name="transforming-drifted-columns"></a>Transformando colunas descompassos

Quando o fluxo de dados tiver colunas descompassos, você poderá acessá-las em suas transformações com os seguintes métodos:

* Use as expressões `byPosition` e `byName` para referenciar explicitamente uma coluna por nome ou número de posição.
* Adicionar um padrão de coluna em uma coluna derivada ou transformação de agregação para corresponder a qualquer combinação de nome, fluxo, posição ou tipo
* Adicionar mapeamento baseado em regra em uma transformação SELECT ou Sink para corresponder colunas perrespondidas a aliases de colunas por meio de um padrão

Para obter mais informações sobre como implementar padrões de coluna, consulte [padrões de coluna no fluxo de dados de mapeamento](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Ação rápida mapear colunas descompassos

Para referenciar explicitamente colunas descompassos, você pode gerar rapidamente mapeamentos para essas colunas por meio de uma ação rápida de visualização de dados. Depois que o [modo de depuração](concepts-data-flow-debug-mode.md) estiver ativado, vá para a guia Visualização de dados e clique em **Atualizar** para buscar uma visualização de dados. Se data factory detectar que as colunas descompassos existem, você poderá clicar em **mapear descompasso** e gerar uma coluna derivada que permite que você referencie todas as colunas descompassos em exibições de esquema downstream.

![Mapa descompasso](media/data-flow/mapdrifted1.png "Mapa descompasso")

Na transformação coluna derivada gerada, cada coluna descompasso é mapeada para seu nome e tipo de dados detectados. Na visualização de dados acima, a coluna ' MovieID ' é detectada como um inteiro. Depois que o **mapa** é clicado, o MovieID é definido na coluna derivada como `toInteger(byName('movieId'))` e incluído em exibições de esquema em transformações de downstream.

![Mapa descompasso](media/data-flow/mapdrifted2.png "Mapa descompasso")

## <a name="next-steps"></a>Passos seguintes
Na [linguagem de expressão de fluxo de dados](data-flow-expression-functions.md), você encontrará recursos adicionais para padrões de coluna e descompasso de esquema, incluindo "byName" e "byPosition".
