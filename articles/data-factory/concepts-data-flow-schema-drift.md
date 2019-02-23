---
title: Mapeamento de desvios de esquema de fluxo de dados do Azure Data Factory
description: Criar fluxos de dados resilientes no Azure Data Factory com os Descompassos de esquema
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 42fde2804c04b2449068d649e1c660d02e72edb1
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729360"
---
# <a name="mapping-data-flow-schema-drift"></a>Desvios de esquema de fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

O conceito de desvios de esquema é o caso em que as origens de muitas vezes mudam de metadados. Campos, colunas, tipos, etc. podem ser adicionadas, removidas ou alterado em tempo real. Sem tratamento para desvios de esquema, o fluxo de dados se torne vulnerável a alterações nas alterações da origem de dados a montante. Quando alterar a entrada de colunas e campos, padrões ETL típicos falharem porque eles tendem a ser vinculado a esses nomes de origem.

Para proteger contra os Descompassos de esquema, é importante ter os recursos numa ferramenta de fluxo de dados para permitir que, como engenheiro de dados, para:

* Definir origens com nomes de campo mutáveis, tipos de dados, valores e tamanhos
* Definir parâmetros de transformação que podem trabalhar com padrões de dados em vez de embutidos em campos e valores
* Definir as expressões que compreender os padrões para corresponder aos campos de entrada, em vez de usar campos nomeados

No Azure Data Factory fluxo de dados, nessas instalações são apresentadas através deste fluxo de trabalho:

* Escolher "Permitir que os Descompassos de esquema" na sua transformação de origem

<img src="media/data-flow/schemadrift001.png" width="400">

* Quando selecionar esta opção, todos os campos de entrada serão lido a partir da sua origem de cada execução de fluxo de dados e são passados para todo o fluxo para o Sink.

* Certifique-se utilizar "Automaticamente-mapa" para mapear todos os novos campos na transformação de Sink para que todos os novos campos obterem escolhido para cima e foi colocados no seu destino:

<img src="media/data-flow/automap.png" width="400">

* Tudo o que irá funcionar quando novos campos são introduzidos nesse cenário com um simples origem -> Sink (também conhecido como copiar) mapeamento.

* Para adicionar transformações no fluxo de trabalho que processa os descompassos de esquema, pode utilizar a correspondência de padrões de acordo com colunas por nome, tipo e valor.

* Clique em "Adicionar coluna padrão" na coluna derivada ou transformação de agregação se pretender criar uma transformação que entende "Descompassos de esquema".

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> Terá de tomar uma decisão de arquitetura do fluxo de dados para aceitar os descompassos de esquema em todo o fluxo. Ao fazê-lo, pode proteger contra alterações de esquema das origens. No entanto, perderá vinculação inicial de suas colunas e tipos em todo o fluxo de dados. O Azure Data Factory processa fluxos de desvios de esquema como fluxos de ligação tardia, portanto, quando cria as transformações, os nomes das colunas não estarão disponíveis para si nas exibições do esquema em todo o fluxo.

<img src="media/data-flow/taxidrift1.png" width="400">

O exemplo de demonstração de táxis de fluxo de dados, é um exemplo Descompassos de esquema no fluxo de dados na parte inferior, com a origem de TripFare. Na transformação de agregação, tenha em atenção que estamos usando o design de "padrão da coluna" para os campos de agregação. Em vez de colunas específicas de nomenclatura ou se está à procura de colunas por posição, partimos do princípio de que os dados podem alterar e poderão não aparecer na mesma ordem entre as execuções.

Neste exemplo de fluxo de dados do Azure Data Factory descompassos de esquema manipulação, criamos e agregação de que verifica a existência de colunas do tipo "double", sabendo que o domínio de dados contém os preços para cada viagem. Em seguida, podemos executar um cálculo de matemática agregado em todos os campos duplos na fonte, independentemente de onde a coluna que chegam e independentemente da coluna de nomenclatura.

A sintaxe de fluxo de dados do Azure Data Factory utiliza $$ para representar cada coluna correspondente do seu padrão de correspondência. Também pode comparar nos nomes das colunas através da pesquisa de cadeia de caracteres complexas e funções de expressão regular. Neste caso, vamos criar um novo nome de campo agregado com base em cada correspondência de um tipo 'double' da coluna e acrescentar o texto ```_total``` para cada um desses nomes de correspondência: 

```concat($$, '_total')```

Em seguida, iremos arredondar e somar os valores para cada uma dessas colunas correspondentes:

```round(sum ($$))```

Pode testar este horizontalmente com o exemplo de fluxo de dados do Azure Data Factory "Demonstração de táxis". Alterne a sessão de depuração com o botão de alternar da depuração no topo da superfície de design do fluxo de dados para que pode ver os resultados interativamente:

<img src="media/data-flow/taxidrift2.png" width="800">

