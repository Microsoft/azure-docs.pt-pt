---
title: Transformação dinâmica do fluxo de dados de mapeamento de Azure Data Factory
description: Dados dinâmicos de linhas para colunas usando Azure Data Factory mapeamento dinâmico do fluxo de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 0b68007f8c3383997f0d31888198af866d38b590
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178656"
---
# <a name="azure-data-factory-pivot-transformation"></a>Transformação dinâmica do Azure data factory
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use pivot no fluxo de dados do ADF como uma agregação em que uma ou mais colunas de Agrupamento têm seus valores de linha distintos transformados em colunas individuais. Essencialmente, você pode dinamizar valores de linha em novas colunas (transformar dados em metadados).

![Opções dinâmicas](media/data-flow/pivot1.png "pivô 1")

## <a name="group-by"></a>Agrupar por

![Opções dinâmicas](media/data-flow/pivot2.png "pivô 2")

Primeiro, defina as colunas que você deseja agrupar para a agregação dinâmica. Você pode definir mais de 1 coluna com o sinal de + ao lado da lista de colunas.

## <a name="pivot-key"></a>Chave dinâmica

![Opções dinâmicas](media/data-flow/pivot3.png "pivô 3")

A chave dinâmica é a coluna que o ADF dinamizará da linha para a coluna. Por padrão, cada valor exclusivo no conjunto de valores para esse campo será dinamizado para uma coluna. No entanto, opcionalmente, você pode inserir os valores do conjunto de um que você deseja dinamizar para valores de coluna. Esta é a coluna que determinará as novas colunas que serão criadas.

## <a name="pivoted-columns"></a>Colunas dinâmicas

![Opções dinâmicas](media/data-flow/pivot4.png "tabela dinâmica 4")

Por fim, você escolherá a agregação que deseja usar para os valores dinâmicos e como deseja que as colunas sejam exibidas na nova projeção de saída da transformação.

Adicional Você pode definir um padrão de nomenclatura com um prefixo, meio e sufixo a ser adicionado a cada novo nome de coluna dos valores de linha.

Por exemplo, dinamizar "vendas" por "Region" resultaria em novos valores de coluna de cada valor de vendas, ou seja, "25", "50", "1000", etc. No entanto, se você definir um valor de prefixo "Sales-", cada valor de coluna adicionará "Sales" ao início do valor.

![Opções dinâmicas](media/data-flow/pivot5.png "pivô 5")

Definir a organização da coluna como "normal" agrupará todas as colunas dinâmicas com seus valores agregados. A definição da organização das colunas como "lateral" será alternada entre a coluna e o valor.

### <a name="aggregation"></a>Agregação

Para definir a agregação que você deseja usar para os valores dinâmicos, clique no campo na parte inferior do painel colunas dinâmicas. Você entrará no construtor de expressões do fluxo de dados do ADF, onde poderá criar uma expressão de agregação e fornecer um nome de alias descritivo para os novos valores agregados.

Use a linguagem de expressão de fluxo de dados do ADF para descrever as transformações de coluna dinâmicas https://aka.ms/dataflowexpressions no construtor de expressões:.

## <a name="pivot-metadata"></a>Metadados dinâmicos

A transformação dinâmica produzirá novos nomes de coluna dinâmicos com base em seus dados de entrada. A chave dinâmica produz os valores para cada novo nome de coluna. Se você não especificar valores individuais e desejar criar nomes de coluna dinâmicos para cada valor exclusivo em sua chave dinâmica, a interface do usuário não exibirá os metadados em inspeção e não haverá nenhuma propagação de coluna para a transformação do coletor. Se você definir valores para a chave dinâmica, o ADF poderá determinar os novos nomes de coluna e esses nomes de coluna estarão disponíveis para você no mapeamento de inspeção e de coletor.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Gerar um novo modelo a partir de colunas dinâmicas

A tabela dinâmica gera novos nomes de coluna dinamicamente com base em valores de linha. Você pode transformar essas novas colunas em metadados que podem ser referenciados posteriormente no fluxo de dados. Para fazer isso, clique na guia Visualização de dados. Todas as novas colunas geradas pela transformação dinâmica aparecem com um ícone "descompasso" no cabeçalho da tabela. Clique no botão "mapear descompassos" para transformar essas novas colunas em metadados, tornando-as parte do modelo do fluxo de dados.

![Colunas dinâmicas](media/data-flow/newpivot1.png "Mapear colunas dinâmicas descompassos")

### <a name="landing-new-columns-in-sink"></a>Novas colunas de aterrissagem no coletor

Mesmo com nomes de coluna dinâmicas em pivô, você ainda pode coletar seus novos nomes de coluna e valores em seu repositório de destino. Basta definir "permitir descompasso de esquema" em nas configurações do coletor. Você não verá os novos nomes dinâmicos em seus metadados de coluna, mas a opção de descompasso de esquema permitirá que você pouse os dados.

### <a name="view-metadata-in-design-mode"></a>Exibir metadados no modo de design

Se você quiser exibir os novos nomes de coluna como metadados na inspeção e desejar ver as colunas propagadas explicitamente para a transformação do coletor, defina valores explícitos na guia chave dinâmica.

### <a name="how-to-rejoin-original-fields"></a>Como reassociar campos originais
A transformação dinâmica só projetará as colunas usadas na ação de agregação, agrupamento e dinâmica. Se você quiser incluir as outras colunas da etapa anterior em seu fluxo, use uma nova ramificação da etapa anterior e use o padrão de autojunção para conectar o fluxo com os metadados originais.

## <a name="next-steps"></a>Passos seguintes

Experimente a [transformação não dinâmica](data-flow-unpivot.md) para transformar valores de coluna em valores de linha. 
