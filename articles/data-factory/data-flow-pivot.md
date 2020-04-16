---
title: Mapeando a transformação do eixo do fluxo de dados
description: Dados de pivô de linhas a colunas usando azure data factory mapeando dados fluxo de dados Transformação pivot
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a2276f5714db427586dbd56027e51c167b8c604f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413601"
---
# <a name="azure-data-factory-pivot-transformation"></a>Transformação do pivô da fábrica de dados Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Utilize o Pivô no Fluxo de Dados ADF como uma agregação onde uma ou mais colunas de agrupamento têm os seus valores distintos de linha transformados em colunas individuais. Essencialmente, pode girar os valores da linha em novas colunas (transformar dados em metadados).

![Opções de pivô](media/data-flow/pivot1.png "pivô 1")

## <a name="group-by"></a>Agrupar por

![Opções de pivô](media/data-flow/pivot2.png "pivô 2")

Primeiro, detete as colunas que deseja agrupar para a sua agregação de pivô. Pode definir mais de 1 coluna aqui com o sinal + ao lado da lista de colunas.

## <a name="pivot-key"></a>Chave de pivô

![Opções de pivô](media/data-flow/pivot3.png "pivô 3")

A Chave Pivot é a coluna que a ADF irá girar de linha em coluna. Por predefinição, cada valor único no conjunto de dados para este campo girará para uma coluna. No entanto, pode introduzir opcionalmente os valores a partir do conjunto de dados que pretende orientar para valores de coluna. Esta é a coluna que determinará as novas colunas que serão criadas.

## <a name="pivoted-columns"></a>Colunas pivotadas

![Opções de pivô](media/data-flow/pivot4.png "pivô 4")

Por último, escolherá a agregação que deseja utilizar para os valores mais girados e como gostaria que as colunas fossem exibidas na nova projeção de saída da transformação.

(Opcional) Pode definir um padrão de nomeação com prefixo, meio e sufixo a ser adicionado a cada novo nome de coluna a partir dos valores da linha.

Por exemplo, a aposta em "Vendas" por "Região" resultaria em novos valores de coluna saem de cada valor de venda, ou é, "25", "50", "1000", etc. No entanto, se definir um prefixo de valor de "Vendas", cada valor de coluna adicionaria "Sales-" ao início do valor.

![Opções de pivô](media/data-flow/pivot5.png "pivô 5")

A definição do Arranjo da Coluna para "Normal" juntará todas as colunas pivotadas com os seus valores agregados. A definição do arranjo das colunas para "Lateral" alternará entre coluna e valor.

### <a name="aggregation"></a>Agregação

Para definir a agregação que deseja utilizar para os valores de rotação, clique no campo na parte inferior do painel de Colunas Pivotadas. Você vai entrar no construtor de expressão De fluxo de dados ADF onde você pode construir uma expressão de agregação e fornecer um nome descritivo para os seus novos valores agregados.

Utilize a Linguagem de Expressão de Fluxo de Dados ADF https://aka.ms/dataflowexpressionspara descrever as transformações da coluna pivotada no Construtor de Expressão: .

## <a name="pivot-metadata"></a>Metadados de pivô

A transformação pivot produzirá novos nomes de colunas dinâmicos com base nos seus dados de entrada. A Chave Pivot produz os valores para cada novo nome de coluna. Se não especificar valores individuais e pretender criar nomes dinâmicos de colunas para cada valor único na sua Chave Pivot, então o UI não apresentará os metadados em Inspeção e não haverá propagação de colunas para a transformação do Lavatório. Se definir valores para a Chave Pivot, então a ADF pode determinar os novos nomes de colunas e esses nomes de colunas estarão disponíveis para si no mapeamento De Inspeção e Sink.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Gerar um novo modelo a partir de colunas dinâmicas

O pivô gera novos nomes de colunas dinamicamente baseados nos valores das linhas. Pode transformar essas novas colunas em metadados que podem ser referenciados mais tarde no fluxo de dados. Para isso, clique no separador 'Pré-visualização de dados'. Todas as novas colunas geradas pela transformação do pivô aparecem com um ícone "drifted" no cabeçalho da mesa. Clique no botão "Map drifted" para transformar essas novas colunas em metadados, tornando-as parte do modelo do fluxo de dados.

![Colunas Pivot](media/data-flow/newpivot1.png "Colunas de pivô derivadas do mapa")

### <a name="landing-new-columns-in-sink"></a>Aterragem de novas colunas em Sink

Mesmo com nomes dinâmicos de colunas em Pivot, ainda pode afundar os seus novos nomes e valores de coluna saqueado na sua loja de destino. Basta definir "Allow Schema Drift" nas definições do lavatório. Não verá os novos nomes dinâmicos nos metadados da sua coluna, mas a opção de deriva de esquemas permitirá que você aterre os dados.

### <a name="view-metadata-in-design-mode"></a>Ver metadados no modo de design

Se desejar ver os novos nomes de colunas como metadados em Inspecionar e pretender ver as colunas propagarem-se explicitamente à transformação do Sink, então deite valores explícitos no separador Chave Pivot.

### <a name="how-to-rejoin-original-fields"></a>Como voltar a juntar-se aos campos originais
A transformação pivot só projetará as colunas utilizadas na agregação, agrupamento e ação de pivô. Se desejar incluir as outras colunas do passo anterior no seu fluxo, utilize um Novo Ramo a partir do passo anterior e utilize o padrão de auto-união para ligar o fluxo aos metadados originais.

## <a name="next-steps"></a>Passos seguintes

Experimente a [transformação despivot](data-flow-unpivot.md) para transformar os valores da coluna em valores de linha. 
