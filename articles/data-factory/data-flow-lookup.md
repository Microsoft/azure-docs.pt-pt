---
title: Transformação de procura no fluxo de dados de mapeamento
description: Dados de referência de outra fonte usando a transformação de procura no fluxo de dados de mapeamento.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/19/2021
ms.openlocfilehash: b8754742c572a8dbc1f55c64e47bec640d757d65
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739373"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Transformação de procura no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação de procura para obter dados de referência de outra fonte num fluxo de fluxo de dados. A transformação de procura anexa colunas de dados combinados com os dados de origem.

Uma transformação de procura é semelhante a uma união exterior esquerda. Todas as linhas do fluxo primário existirão no fluxo de saída com colunas adicionais do fluxo de procuração.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xsVT]

## <a name="configuration"></a>Configuração

![A screenshot mostra o separador definições de Configuração do Lookup com as etiquetas descritas no texto seguinte.](media/data-flow/lookup1.png "Pesquisa")

**Fluxo primário:** O fluxo de dados que chega. Este fluxo é equivalente ao lado esquerdo de uma junta.

**Fluxo de procura:** Os dados que estão anexados ao fluxo primário. Quais dados são adicionados é determinado pelas condições de procura. Este fluxo é equivalente ao lado direito de uma união.

**Combine várias linhas:** Se ativado, uma linha com várias partidas na corrente primária retornará várias linhas. Caso contrário, apenas uma linha será devolvida com base na condição 'Match on'.

**Jogo em:** Só é visível se não for selecionada 'Match multiple rows'. Escolha se corresponde em qualquer linha, no primeiro jogo ou no último jogo. Recomenda-se qualquer linha, uma vez que executa o mais rápido. Se a primeira linha ou a última linha forem selecionadas, será necessário especificar as condições de classificação.

**Condições de procura:** Escolha quais colunas combinar. Se a condição de igualdade for cumprida, então as linhas serão consideradas compatíveis. Hover e selecione 'Coluna Computada' para extrair um valor utilizando a linguagem de [expressão de fluxo de dados](data-flow-expression-functions.md).

Todas as colunas de ambos os fluxos estão incluídas nos dados de saída. Para deixar cair colunas duplicadas ou indesejadas, adicione uma [transformação selecionada](data-flow-select.md) após a sua transformação de procura. As colunas também podem ser largadas ou renomeadas numa transformação de pia.

### <a name="non-equi-joins"></a>Não-equi junta-se

Para utilizar um operador condicional como não é igual (!=) ou superior a (>) nas suas condições de procuração, altere o recuo do operador entre as duas colunas. As juntas não-equi requerem que pelo menos um dos dois streams seja transmitido através da radiodifusão **fixa** no **separador Otimize.**

![Não-equi lookup](media/data-flow/non-equi-lookup.png "Não-equi lookup")

## <a name="analyzing-matched-rows"></a>Análise de linhas combinadas

Após a sua transformação de lookup, a função `isMatch()` pode ser usada para ver se o lookup corresponde a linhas individuais.

![Padrão de procura](media/data-flow/lookup111.png "Padrão de procura")

Um exemplo deste padrão é usar a transformação de divisão condicional para dividir a `isMatch()` função. No exemplo acima, as linhas correspondentes passam pelo fluxo superior e as linhas não correspondentes fluem através do ```NoMatch``` fluxo.

## <a name="testing-lookup-conditions"></a>Condições de análise de testes

Ao testar a transformação de procura com pré-visualização de dados no modo depuror, utilize um pequeno conjunto de dados conhecidos. Ao recolher amostras de um conjunto de dados grande, não é possível prever quais linhas e teclas serão lidas para testes. O resultado não é determinístico, o que significa que as suas condições de união podem não devolver quaisquer partidas.

## <a name="broadcast-optimization"></a>Otimização de transmissão

![Transmissão Aderir](media/data-flow/broadcast.png "Transmissão Aderir")

Em juntas, procura e transformação existente, se um ou ambos os fluxos de dados se encaixarem na memória do nó do trabalhador, pode otimizar o desempenho permitindo a **radiodifusão.** Por predefinição, o motor de faísca decidirá automaticamente se transmite ou não um dos lados. Para escolher manualmente qual lado transmitir, selecione **Fixo**.

Não é aconselhável desativar a transmissão através da opção **Off,** a menos que as suas junções estejam a ter erros de tempo.

## <a name="cached-lookup"></a>Lookup em cache

Se estiver a fazer várias análises menores na mesma fonte, uma pia em cache e uma procura talvez um caso de melhor uso do que a transformação de procura. Exemplos comuns em que um lavatório de cache pode ser melhor estão à procura de um valor máximo numa loja de dados e códigos de erro correspondentes a uma base de dados de mensagens de erro. Para mais informações, saiba sobre [lavatórios](data-flow-sink.md#cache-sink) de cache e [pesquisas em cache.](concepts-data-flow-expression-builder.md#cached-lookup)

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Exemplo

![A screenshot mostra o separador definições de Configuração do Lookup para o seguinte código.](media/data-flow/lookup-dsl-example.png "Pesquisa")

O script de fluxo de dados para a configuração de procura acima está no corte de código abaixo.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
Passos seguintes

* A [união](data-flow-join.md) e as transformações [existentes](data-flow-exists.md) tanto tomam em entradas de fluxo múltiplo
* Utilize uma [transformação de divisão condicional](data-flow-conditional-split.md) com ```isMatch()``` linhas divididas em valores correspondentes e não correspondentes
