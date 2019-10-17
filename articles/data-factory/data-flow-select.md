---
title: Fluxo de dados de mapeamento de Azure Data Factory selecionar transformação
description: Fluxo de dados de mapeamento de Azure Data Factory selecionar transformação
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 6ef9712dd2fd6b8d53fd4ad2c3e07e1d6c8f1aec
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387203"
---
# <a name="mapping-data-flow-select-transformation"></a>Mapeando fluxo de dados selecionar transformação


Use essa transformação para a seletividade da coluna (reduzindo o número de colunas), colunas de alias e nomes de fluxo e reordenar colunas.

## <a name="how-to-use-select-transformation"></a>Como usar a transformação selecionar
A transformação selecionar permite que você faça o alias de um fluxo inteiro ou colunas nesse fluxo, atribua nomes diferentes (aliases) e, em seguida, referencie esses novos nomes posteriormente no fluxo de dados. Essa transformação é útil para cenários de auto-associação. A maneira de implementar uma autojunção no fluxo de dados do ADF é pegar um fluxo, ramificar a ti com "nova ramificação" e, imediatamente depois, adicionar uma transformação "Select". Esse fluxo agora terá um novo nome que você pode usar para ingressar novamente no fluxo original, criando uma auto-associação:

![Auto-associação](media/data-flow/selfjoin.png "Auto-associação")

No diagrama acima, a transformação selecionar está na parte superior. Este é um alias do fluxo original para "OrigSourceBatting". Na transformação junção realçada abaixo dela, você pode ver que usamos essa transmissão de alias Select como a junção à direita, permitindo que possamos fazer referência à mesma chave tanto no lado esquerdo & direito da junção interna.

Select também pode ser usado como uma forma de selecionar colunas de seu fluxo de dados. Por exemplo, se você tiver 6 colunas definidas em seu coletor, mas quiser apenas escolher um 3 específico para transformar e, em seguida, fluir para o coletor, poderá selecionar apenas os 3 usando a transformação selecionar.

![Selecionar transformação](media/data-flow/newselect1.png "Selecionar alias")

## <a name="options"></a>Opções
* A configuração padrão para "selecionar" é incluir todas as colunas de entrada e manter esses nomes originais. Você pode definir o alias do fluxo definindo o nome da transformação selecionar.
* Para alias de colunas individuais, desmarque "selecionar tudo" e use o mapeamento de coluna na parte inferior.
* Escolha ignorar duplicatas para eliminar colunas duplicadas dos metadados de entrada ou saída.

![Ignorar duplicatas](media/data-flow/select-skip-dup.png "Ignorar duplicatas")

* Quando você opta por ignorar duplicatas, os resultados ficarão visíveis na guia inspecionar. o ADF manterá a primeira ocorrência da coluna e você verá que cada ocorrência subsequente dessa mesma coluna foi removida do fluxo.

> [!NOTE]
> Para limpar as regras de mapeamento, pressione o botão **Redefinir** .

## <a name="mapping"></a>Correlação
Por padrão, a transformação selecionar mapeará automaticamente todas as colunas, que passarão por todas as colunas de entrada para o mesmo nome na saída. O nome do fluxo de saída definido em selecionar configurações definirá um novo nome de alias para o fluxo. Se você mantiver a seleção definir para mapa automático, poderá alias de todo o fluxo com todas as colunas iguais.

![Selecionar regras de transformação](media/data-flow/rule2.png "Mapeamento baseado em regras")

Se você quiser criar um alias, remover, renomear ou reordenar colunas, primeiro será necessário desligar o "mapa automático". Por padrão, você verá uma regra padrão inserida para você chamada "todas as colunas de entrada". Você pode deixar essa regra em vigor se pretende sempre permitir que todas as colunas de entrada sejam mapeadas para o mesmo nome em sua saída.

No entanto, se você quiser adicionar regras personalizadas, clique em "Adicionar mapeamento". O mapeamento de campo fornecerá uma lista de nomes de coluna de entrada e saída para mapear e alias. Escolha "mapeamento baseado em regras" para criar regras de correspondência de padrões.

## <a name="rule-based-mapping"></a>Mapeamento baseado em regras
Ao escolher o mapeamento baseado em regras, você está instruindo o ADF a avaliar sua expressão de correspondência para corresponder às regras de padrão de entrada e definir os nomes de campo de saída. Você pode adicionar qualquer combinação de mapeamentos com base em campo e em regra. Os nomes de campo são então gerados em tempo de execução pelo ADF com base nos metadados de entrada da origem. Você pode exibir os nomes dos campos gerados durante a depuração e usando o painel de visualização de dados.

Mais detalhes sobre a correspondência de padrões estão disponíveis na [documentação do padrão de coluna](concepts-data-flow-column-pattern.md).

## <a name="next-steps"></a>Passos seguintes
* Depois de usar Select para renomear, reordenar e alias de colunas, use a [transformação do coletor](data-flow-sink.md) para colocar seus dados em um repositório de dados.
