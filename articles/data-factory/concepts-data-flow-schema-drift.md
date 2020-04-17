---
title: Deriva de schema no fluxo de dados de mapeamento
description: Construir fluxos de dados resilientes na Fábrica de Dados Azure com A Deriva schema
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 4c510cae7ba5a952e6dc7f7cb7ae7591fe801560
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461298"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Deriva de schema no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A deriva de Schema é o caso em que as suas fontes mudam frequentemente de metadados. Campos, colunas e. Sem manusear a deriva do esquema, o fluxo de dados torna-se vulnerável a alterações de fonte de dados a montante. Os padrões Típicos de ETL falham quando as colunas de entrada e os campos mudam porque tendem a estar ligados a esses nomes de origem.

Para proteger contra a deriva de esquemas, é importante ter as instalações numa ferramenta de fluxo de dados para permitir que você, como Engenheiro de Dados, a:

* Defina fontes que tenham nomes de campo mutáveis, tipos de dados, valores e tamanhos
* Defina parâmetros de transformação que podem funcionar com padrões de dados em vez de campos e valores codificados
* Defina expressões que compreendam padrões que correspondam aos campos de entrada, em vez de usar campos nomeados

A Azure Data Factory suporta de forma nativa esquemas flexíveis que mudam da execução para a execução para que possa construir uma lógica genérica de transformação de dados sem a necessidade de recompilar os seus fluxos de dados.

Você precisa tomar uma decisão arquitetônica no seu fluxo de dados para aceitar a deriva de esquemas em todo o seu fluxo. Quando o fizer, pode proteger-se contra alterações de esquemas a partir das fontes. No entanto, perderá a ligação precoce das suas colunas e tipos em todo o seu fluxo de dados. A Azure Data Factory trata os fluxos de deriva de esquemas como fluxos de ligação tardia, por isso, quando constrói as suas transformações, os nomes de colunas à deriva não estarão disponíveis nas vistas do esquema durante todo o fluxo.

Este vídeo fornece uma introdução a algumas das soluções complexas que você pode construir facilmente em ADF com a funcionalidade de deriva de esquemas do fluxo de dados. Neste exemplo, construímos padrões reutilizáveis baseados em schemas de base de dados flexíveis:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Deriva de schema na fonte

As colunas que entram no fluxo de dados da sua definição de origem são definidas como "derivadas" quando não estão presentes na sua projeção de origem. Pode ver a sua projeção de origem a partir do separador de projeção na transformação de origem. Quando selecionar um conjunto de dados para a sua fonte, a ADF retirará automaticamente o esquema do conjunto de dados e criará um projeto a partir dessa definição de esquema de conjunto de dados.

Numa transformação de origem, a deriva do esquema é definida como colunas de leitura que não são definidas o seu esquema de conjunto de dados. Para permitir a deriva do esquema, verifique **Permitir a deriva do esquema** na sua transformação de origem.

![Fonte de deriva de Schema](media/data-flow/schemadrift001.png "Fonte de deriva de Schema")

Quando a deriva do esquema é ativada, todos os campos de entrada são lidos a partir da sua fonte durante a execução e passam por todo o fluxo para a Pia. Por padrão, todas as colunas recentemente detetadas, conhecidas como *colunas derivadas,* chegam como um tipo de dados de cadeia. Se desejar que o seu fluxo de dados inferir automaticamente tipos de dados de colunas derivadas, verifique os tipos de **colunas à deriva infer** nas definições de origem.

## <a name="schema-drift-in-sink"></a>Deriva de schema na pia

Numa transformação afundada, o esquema drift é quando se escreve colunas adicionais em cima do que é definido no esquema de dados da pia. Para permitir a deriva do esquema, verifique **Permitir a deriva do esquema** na sua transformação da pia.

![Pia de deriva de Schema](media/data-flow/schemadrift002.png "Pia de deriva de Schema")

Se a deriva do esquema estiver ativada, **certifique-se** de que o slider de mapeamento automático no separador Mapping está ligado. Com este slider ligado, todas as colunas de entrada estão escritas para o seu destino. Caso contrário, deve utilizar mapeamento baseado em regras para escrever colunas derivadas.

![Mapeamento auto da pia](media/data-flow/automap.png "Mapeamento auto da pia")

## <a name="transforming-drifted-columns"></a>Transformando colunas derivadas

Quando o fluxo de dados tiver colunas à deriva, pode acessá-las nas suas transformações com os seguintes métodos:

* Utilize `byPosition` as `byName` e expressões para referir explicitamente uma coluna pelo nome ou número de posição.
* Adicione um padrão de coluna numa Coluna Derivada ou transformação agregada para combinar com qualquer combinação de nome, fluxo, posição ou tipo
* Adicione mapeamento baseado em regras numa transformação Select ou Sink para combinar colunas derivadas a pseudónimos de colunas através de um padrão

Para obter mais informações sobre como implementar padrões de colunas, consulte os padrões da Coluna no fluxo de [dados de mapeamento](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Map e stão a desviar colunas de ação rápida

Para referência explícita de colunas derivadas, pode rapidamente gerar mapeamentos para estas colunas através de uma visualização rápida de dados. Assim que o [modo de depuração](concepts-data-flow-debug-mode.md) estiver ligado, vá ao separador 'Pré-visualização de dados' e clique em **Refresh** para obter uma pré-visualização de dados. Se a fábrica de dados detetar que existem colunas derivadas, pode clicar no **Map Drifted** e gerar uma coluna derivada que lhe permite fazer referência a todas as colunas derivadas em visões de esquemas a jusante.

![Mapa à deriva](media/data-flow/mapdrifted1.png "Mapa à deriva")

Na transformação da Coluna Derivada gerada, cada coluna derivada é mapeada para o seu nome e tipo de dados detetados. Na pré-visualização de dados acima, a coluna 'movieId' é detetada como um inteiro. Depois do **Map Drifted** ser clicado, o `toInteger(byName('movieId'))` movieId é definido na Coluna Derivada como e incluído em vistas de esquemaema em transformações a jusante.

![Mapa à deriva](media/data-flow/mapdrifted2.png "Mapa à deriva")

## <a name="next-steps"></a>Passos seguintes
Na Linguagem de Expressão de Fluxo de [Dados,](data-flow-expression-functions.md)encontrará instalações adicionais para padrões de colunas e deriva de esquemas, incluindo "byName" e "byPosition".
