---
title: Mapeando fluxo de dados Lookup Transformation
description: Azure Data Factory mapeando fluxo de dados Lookup Transformation
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/26/2020
ms.openlocfilehash: 2216e1bf058eef486dbfefba24d52bdc6bdb232f
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164683"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory mapeando fluxo de dados Lookup Transformation

Utilize o Lookup para adicionar dados de referência de outra fonte ao seu Fluxo de Dados. A transformação do Lookup requer uma fonte definida que aponta para a sua tabela de referência e fósforos em campos-chave.

![Transformação de lookup](media/data-flow/lookup1.png "Pesquisa")

Selecione os principais campos que pretende combinar entre os campos de fluxo de entrada e os campos a partir da fonte de referência. Primeiro deve ter criado uma nova fonte na tela de design de Fluxo de Dados para usar como o lado direito para a procura.

Quando os fósforos forem encontrados, as linhas e colunas resultantes da fonte de referência serão adicionadas ao fluxo de dados. Pode escolher quais os campos de interesse que deseja incluir na sua Pia no final do seu Fluxo de Dados. Em alternativa, utilize uma transformação Select seguindo o seu Lookup para podar a lista de campo para manter apenas os campos de ambos os fluxos que gostaria de reter.

A transformação lookup executa o equivalente a uma união exterior esquerda. Então, verás todas as filas da tua fonte esquerda combinadas com fósforos do teu lado direito. Se tiver vários valores correspondentes na sua aparência, ou se quiser personalizar a expressão de lookup, é preferível mudar para uma transformação de Join e usar uma cruz de união. Isto evitará eventuais erros de produto cartesiano na execução.

## <a name="match--no-match"></a>Jogo / Sem jogo

Após a sua transformação de Lookup, pode utilizar transformações subsequentes para inspecionar os resultados de cada linha de jogo, utilizando a função de expressão `isMatch()` para fazer escolhas adicionais na sua lógica com base no facto de o Lookup ter ou não resultado numa correspondência de linha ou não.

![Padrão de procura](media/data-flow/lookup111.png "Padrão de procura")

Depois de utilizar a transformação de Lookup, pode adicionar uma transformação de Divisão Condicional dividindo-se na função ```isMatch()```. No exemplo acima, as linhas correspondentes passam pelo fluxo superior e as linhas não correspondentes fluem através do fluxo de ```NoMatch```.

## <a name="first-or-last-value"></a>Primeiro ou último valor

A Transformação de Procura é implementada como uma união exterior à esquerda. Quando tiver vários fósforos do seu Lookup, pode querer reduzir as múltiplas linhas partidas, escolhendo a primeira linha partida, a última partida ou qualquer linha aleatória.

### <a name="option-1"></a>opção 1

![Lookup de linha única](media/data-flow/singlerowlookup.png "Lookup de fila única")

* Combine várias linhas: Deixe em branco para devolver o jogo de uma linha única
* Jogo em: Selecione primeiro, último ou qualquer jogo
* Condições de classificação: Se selecionar primeiro ou último, a ADF requer que os seus dados sejam encomendados de modo a que haja lógica por trás da primeira e da última

> [!NOTE]
> Utilize apenas a primeira ou última opção no seu seletor de fila única se precisar de controlar o valor para trazer de volta da sua procura. A utilização de "quaisquer" ou de várias linhas funcionará mais rapidamente.

### <a name="option-2"></a>Opção 2

Também pode fazê-lo usando uma transformação agregada após o seu Lookup. Neste caso, uma transformação agregada chamada ```PickFirst``` é usada para escolher o primeiro valor dos jogos de procuração.

![Agregado de lookup](media/data-flow/lookup333.png "Agregado de lookup")

![Olhar primeiro](media/data-flow/lookup444.png "Olhar primeiro")

## <a name="optimizations"></a>Otimizações

Na Fábrica de Dados, os Fluxos de Dados executam em ambientes de Faísca saltados. Se o seu conjunto de dados puder encaixar no espaço de memória do nó do trabalhador, podemos otimizar o seu desempenho de Lookup.

![Broadcast Join](media/data-flow/broadcast.png "Broadcast Join")

### <a name="broadcast-join"></a>A difusão junta-se

Selecione A emissão do lado esquerdo e/ou direito junta-se para solicitar à ADF que empurre todo o conjunto de dados de ambos os lados da relação Lookup para a memória. Para conjuntos de dados mais pequenos, isto pode melhorar muito o seu desempenho de procura.

### <a name="data-partitioning"></a>Criação de partições de dados

Também pode especificar a partilha dos seus dados selecionando "set Partitioning" no separador Otimize da transformação de Lookup para criar conjuntos de dados que possam encaixar melhor na memória por trabalhador.

## <a name="next-steps"></a>Passos seguintes

* As transformações [de adesão](data-flow-join.md) e [existem](data-flow-exists.md) executam tarefas semelhantes nos fluxos de dados de mapeamento da ADF. Olhe para as transformações a seguir.
* Utilize uma [Divisão Condicional](data-flow-conditional-split.md) com ```isMatch()``` para dividir linhas em valores correspondentes e não correspondentes
