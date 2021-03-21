---
title: Deriva de Schema no fluxo de dados de mapeamento
description: Construir fluxos de dados resilientes na fábrica de dados Azure com Schema Drift
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 11ddb2f40ee56b51c5ecbae11465093abb8e4feb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027487"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Deriva de Schema no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A deriva de Schema é o caso em que as suas fontes mudam frequentemente de metadados. Campos, colunas e, tipos podem ser adicionados, removidos ou alterados na mosca. Sem manusear a deriva de esquema, o seu fluxo de dados torna-se vulnerável a alterações na fonte de dados a montante. Os padrões típicos de ETL falham quando as colunas e os campos de entrada mudam porque tendem a estar ligados a esses nomes de origem.

Para proteger contra a deriva de esquemas, é importante ter as instalações numa ferramenta de fluxo de dados para permitir que você, como engenheiro de dados,:

* Definir fontes que tenham nomes de campo mutáveis, tipos de dados, valores e tamanhos
* Definir parâmetros de transformação que podem trabalhar com padrões de dados em vez de campos e valores codificados
* Defina expressões que compreendam padrões que correspondam aos campos de entrada, em vez de usar campos nomeados

A Azure Data Factory suporta de forma nativa esquemas flexíveis que mudam de execução para execução para que possa construir uma lógica genérica de transformação de dados sem a necessidade de recompiler os seus fluxos de dados.

Você precisa tomar uma decisão arquitetónica no seu fluxo de dados para aceitar a deriva de esquemas em todo o seu fluxo. Quando o fizeres, podes proteger-te contra as alterações de esquema das fontes. No entanto, perderá a ligação antecipada das suas colunas e tipos ao longo do fluxo de dados. A Azure Data Factory trata os fluxos de deriva de schema como fluxos de ligação tardia, por isso, quando você constrói as suas transformações, os nomes de colunas derivadas não estarão disponíveis para você nas vistas do esquema em todo o fluxo.

Este vídeo fornece uma introdução a algumas das soluções complexas que você pode construir facilmente em ADF com a funcionalidade de deriva de esquema de fluxo de dados. Neste exemplo, construímos padrões reutilizáveis com base em esquemas flexíveis de base de dados:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Deriva de Schema na fonte

As colunas que entram no seu fluxo de dados a partir da sua definição de origem são definidas como "derivadas" quando não estão presentes na sua projeção de origem. Pode ver a sua projeção de origem a partir do separador de projeção na transformação da fonte. Quando selecionar um conjunto de dados para a sua origem, a ADF retirará automaticamente o esquema do conjunto de dados e criará uma projeção a partir dessa definição de esquema de conjunto de dados.

Numa transformação de fonte, a deriva de esquema é definida como colunas de leitura que não são definidas o seu esquema de conjunto de dados. Para ativar a deriva do esquema, verifique **Deixe o esquema derivar** na sua transformação de origem.

![Fonte de deriva de Schema](media/data-flow/schemadrift001.png "Fonte de deriva de Schema")

Quando a deriva do esquema está ativada, todos os campos de entrada são lidos a partir da sua fonte durante a execução e passam por todo o fluxo para a Pia. Por padrão, todas as colunas recentemente detetadas, conhecidas como *colunas derivadas,* chegam como um tipo de dados de cadeia. Se desejar que o fluxo de dados infera automaticamente tipos de dados de colunas derivadas, verifique **os tipos de colunas derivadas infer** nas definições de origem.

## <a name="schema-drift-in-sink"></a>Schema drift na pia

Numa transformação de sumidouro, a deriva do esquema é quando se escreve colunas adicionais em cima do que é definido no esquema de dados da pia. Para ativar a deriva do esquema, verifique **Deixe o esquema derivar** na sua transformação do lavatório.

![Pia de deriva de Schema](media/data-flow/schemadrift002.png "Pia de deriva de Schema")

Se a deriva de esquema estiver ativada, **certifique-se de** que o slider de mapeamento automático no separador 'Mapeamento' está ligado. Com este slider ligado, todas as colunas de entrada são escritas para o seu destino. Caso contrário, deve utilizar o mapeamento baseado em regras para escrever colunas derivadas.

![Mapeamento automático de pia](media/data-flow/automap.png "Mapeamento automático de pia")

## <a name="transforming-drifted-columns"></a>Transformando colunas à deriva

Quando o seu fluxo de dados tiver colunas à deriva, pode acessá-las nas suas transformações com os seguintes métodos:

* Utilize as `byPosition` e `byName` expressões para fazer referência explícita a uma coluna pelo nome ou número de posição.
* Adicione um padrão de coluna numa coluna derivada ou transformação agregada para corresponder a qualquer combinação de nome, fluxo, posição, origem ou tipo
* Adicione mapeamento baseado em regras numa transformação select ou sink para combinar colunas derivadas com pseudónimos de colunas através de um padrão

Para obter mais informações sobre como implementar padrões de colunas, consulte [os padrões da Coluna no fluxo de dados de mapeamento](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Mapa de colunas derivadas ação rápida

Para referenciar explicitamente colunas derivadas, pode gerar rapidamente mapeamentos para estas colunas através de uma ação rápida de pré-visualização de dados. Assim que [o modo de depurar](concepts-data-flow-debug-mode.md) estiver ligado, vá ao separador Des preview de Dados e clique em **Atualizar** para obter uma pré-visualização de dados. Se a fábrica de dados detetar que as colunas derivadas existem, pode clicar em **Map Drifted** e gerar uma coluna derivada que lhe permite fazer referência a todas as colunas derivadas em vistas de esquema a jusante.

![A screenshot mostra o separador de pré-visualização de dados com o Mapa à deriva chamado.](media/data-flow/mapdrifted1.png "Mapa à deriva")

Na transformação gerada da Coluna Derivada, cada coluna derivada é mapeada para o seu nome e tipo de dados detetados. Na pré-visualização de dados acima, a coluna 'movieId' é detetada como um inteiro. Depois de **Map Drifted** ser clicado, o movieId é definido na Coluna Derivada como `toInteger(byName('movieId'))` e incluído em vistas de esquema em transformações a jusante.

![A screenshot mostra o separador Definições da Coluna Derivada.](media/data-flow/mapdrifted2.png "Mapa à deriva")

## <a name="next-steps"></a>Passos seguintes
Na Linguagem de Expressão de [Fluxo de Dados,](data-flow-expression-functions.md)encontrará instalações adicionais para padrões de colunas e deriva de esquema, incluindo "byName" e "byPosition".
