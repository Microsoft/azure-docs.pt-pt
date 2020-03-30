---
title: Transformação de procura no fluxo de dados de mapeamento
description: Dados de referência de outra fonte utilizando a transformação de procura no fluxo de dados de mapeamento.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 78c6c1363af011a90865770d88c0037e50e958c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240424"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Transformação de procura no fluxo de dados de mapeamento

Utilize a transformação de procura ção para dados de referência de outra fonte num fluxo de fluxo de dados. A transformação de procura anexa colunas de dados combinados aos dados de origem.

Uma transformação de lookup é semelhante a uma união exterior esquerda. Todas as linhas do fluxo primário existirão no fluxo de saída com colunas adicionais do fluxo de procura. 

## <a name="configuration"></a>Configuração

![Transformação de lookup](media/data-flow/lookup1.png "Pesquisa")

**Fluxo primário:** O fluxo de dados que está a chegar. Este fluxo é equivalente ao lado esquerdo de uma join.

**Fluxo de olhar:** Os dados que estão anexados ao fluxo primário. Quais os dados adicionados são determinados pelas condições de procura. Este fluxo é equivalente ao lado direito de uma join.

**Corresponder a várias linhas:** Se ativado, uma linha com vários fósforos no fluxo primário devolverá várias linhas. Caso contrário, apenas uma única linha será devolvida com base na condição 'Match on'.

**Jogo em:** Só é visível se estiver ativado 'Corresponder várias linhas'. Escolha se combina em qualquer linha, no primeiro jogo ou no último jogo. Qualquer linha é recomendada à medida que executa o mais rápido. Se a primeira fila ou última fila forem selecionadas, será necessário especificar as condições de classificação.

**Condições de procura:** Escolha quais colunas combinar. Se a condição de igualdade for satisfeita, as linhas serão consideradas compatíveis. Pairar e selecionar 'Coluna Computada' para extrair um valor utilizando a linguagem de expressão do fluxo de [dados](data-flow-expression-functions.md).

A transformação da procura só suporta jogos de igualdade. Para personalizar a expressão de procura para incluir outros operadores, como maiores do que, é aconselhável usar uma [cruz junta na transformação de join](data-flow-join.md#custom-cross-join). Uma junta cruzada evitará eventuais erros de produto cartesiano na execução.

Todas as colunas de ambos os fluxos estão incluídas nos dados de saída. Para deixar cair colunas duplicadas ou indesejadas, adicione uma [transformação selecionada](data-flow-select.md) após a sua transformação de lookup. As colunas também podem ser largadas ou renomeadas numa transformação de pia.

## <a name="analyzing-matched-rows"></a>Analisar linhas partidas

Após a sua transformação `isMatch()` de procuração, a função pode ser usada para ver se a procura corresponde a linhas individuais.

![Padrão de procura](media/data-flow/lookup111.png "Padrão de procura")

Um exemplo deste padrão é usar a transformação de divisão condicional para dividir a `isMatch()` função. No exemplo acima, as linhas correspondentes passam pelo fluxo superior ```NoMatch``` e as linhas não correspondentes fluem através do fluxo.

## <a name="testing-lookup-conditions"></a>Testar condições de procura

Ao testar a transformação da procura com pré-visualização de dados no modo de depuração, utilize um pequeno conjunto de dados conhecidos. Ao recolher amostras de um grande conjunto de dados, não é possível prever quais as linhas e chaves que serão lidas para testes. O resultado não é determinístico, o que significa que as suas condições de adesão podem não devolver quaisquer fósforos.

## <a name="broadcast-optimization"></a>Otimização de transmissão

Na Azure Data Factory os fluxos de dados de mapeamento executam em ambientes de faíscas escaldados. Se o seu conjunto de dados puder encaixar no espaço de memória do nó do trabalhador, o seu desempenho de procura pode ser otimizado permitindo a transmissão.

![Broadcast Join](media/data-flow/broadcast.png "Broadcast Join")

Permitir a radiodifusão empurra todo o conjunto de dados para a memória. Para conjuntos de dados mais pequenos que contenham apenas alguns milhares de linhas, a radiodifusão pode melhorar consideravelmente o seu desempenho de procura. Para grandes conjuntos de dados, esta opção pode levar a uma exceção fora da memória.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Exemplo

![Transformação de lookup](media/data-flow/lookup-dsl-example.png "Pesquisa")

O script de fluxo de dados para a configuração de procura acima está no código abaixo.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
Passos seguintes

* A [união](data-flow-join.md) e [existem](data-flow-exists.md) transformações tanto tomar em várias inputs de fluxo
* Utilize uma [transformação de divisão condicional](data-flow-conditional-split.md) com ```isMatch()``` linhas divididas em valores correspondentes e não correspondentes
