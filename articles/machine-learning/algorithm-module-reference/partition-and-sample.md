---
title: 'Partição e exemplo: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo partição e exemplo no Azure Machine Learning para executar a amostragem em um conjunto de informações ou para criar partições a partir de seu conjunto de informações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 659dcae0bee0fe1015ba6225797c31bad438013f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497716"
---
# <a name="partition-and-sample-module"></a>Módulo de partição e exemplo

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para executar a amostragem em um conjunto de um ou para criar partições do conjunto de seus.

A amostragem é uma ferramenta importante no aprendizado de máquina, pois permite reduzir o tamanho de um conjunto de informações mantendo a mesma proporção de valores. Este módulo dá suporte a várias tarefas relacionadas que são importantes no aprendizado de máquina: 

- Dividir seus dados em várias subseções do mesmo tamanho. 

    Você pode usar as partições para validação cruzada ou para atribuir casos a grupos aleatórios.

- Separar dados em grupos e, em seguida, trabalhar com dados de um grupo específico. 

    Depois de atribuir aleatoriamente casos a grupos diferentes, talvez seja necessário modificar os recursos associados a apenas um grupo.

- Exemplos. 

    Você pode extrair uma porcentagem dos dados, aplicar a amostragem aleatória ou escolher uma coluna a ser usada para balancear o conjunto e executar a amostragem de sobreratificação em seus valores.

- Criando um conjunto de um DataSet menor para teste. 

    Se você tiver muitos dados, talvez queira usar apenas as primeiras *n* linhas ao configurar o pipeline e, em seguida, alternar para usando o conjunto de dados completo ao criar seu modelo. Você também pode usar a amostragem para criar um conjunto de um DataSet menor para uso no desenvolvimento.

## <a name="configure-partition-and-sample"></a>Configurar partição e exemplo

Esse módulo dá suporte a vários métodos para dividir seus dados em partições ou para amostragem. Escolha o método primeiro e, em seguida, defina as opções adicionais exigidas pelo método.

- Head
- Amostragem
- Atribuir a dobras
- Selecionar dobra

### <a name="get-top-n-rows-from-a-dataset"></a>Obter as N primeiras linhas de um conjunto de registros

Use este modo para obter apenas as primeiras *n* linhas. Essa opção será útil se você quiser testar um pipeline em um pequeno número de linhas e não precisar que os dados sejam balanceados ou amostrados de qualquer forma.

1. Adicione a **partição e** o módulo de exemplo ao seu pipeline na interface e conecte o conjunto de espaço.  

2. **Modo de partição ou de exemplo**: defina essa opção como **Head**.

3. **Número de linhas a serem selecionadas**: digite o número de linhas a serem retornadas.

    O número de linhas especificado deve ser um inteiro não negativo. Se o número de linhas selecionadas for maior do que o número de linhas no conjunto de registros, o conjunto de um inteiro será retornado.

4. Executar o pipeline.

O módulo gera um único conjunto de registros contendo apenas o número especificado de linhas. As linhas são sempre lidas na parte superior do conjunto de registros.

### <a name="create-a-sample-of-data"></a>Criar uma amostra de dados

Essa opção dá suporte à amostragem aleatória simples ou à amostragem aleatória de sobreratificação. Isso será útil se você quiser criar um conjunto de um DataSet de exemplo menor para teste.

1. Adicione a **partição e** o módulo de exemplo ao seu pipeline e conecte o conjunto de espaço.

2. **Modo de partição ou de exemplo**: Defina isso para **amostragem**.

3. **Taxa de amostragem**: digite um valor entre 0 e 1. Esse valor especifica a porcentagem de linhas do conjunto de registros de origem que devem ser incluídas no conjunto de resultados de saída.

    Por exemplo, se você quiser apenas metade do conjunto de texto original, digite `0.5` para indicar que a taxa de amostragem deve ser 50%.

    As linhas do conjunto de dados de entrada são embaralhadas e colocadas seletivamente no conjunto de dados de saída, de acordo com a razão especificada.

4. **Semente aleatória para amostragem**: opcionalmente, digite um inteiro para usar como um valor de semente.

    Essa opção será importante se você quiser que as linhas sejam divididas da mesma maneira toda vez. O valor padrão é 0, o que significa que uma semente inicial é gerada com base no relógio do sistema. Isso pode levar a resultados ligeiramente diferentes cada vez que você executar o pipeline.

5. **Divisão de sobreratificação para amostragem**: Selecione esta opção se for importante que as linhas no conjunto de registros devam ser divididas uniformemente por alguma coluna de chave antes da amostragem.

    Para a **coluna de chave estratificação para amostragem**, selecione uma única *coluna Strata* para usar ao dividir o conjunto de os. As linhas no conjunto de registros são então divididas da seguinte maneira:

    1. Todas as linhas de entrada são agrupadas (desratificadas) pelos valores na coluna Strata especificada.

    2. As linhas são embaralhadas dentro de cada grupo.

    3. Cada grupo é adicionado seletivamente ao conjunto de resultados de saída para atender à razão especificada.


6. Executar o pipeline.

    Com essa opção, o módulo gera um único conjunto de dados que contém uma amostragem representativa dos dados. A parte restante, não amostrada do conjunto de resultados não é a saída. 

## <a name="split-data-into-partitions"></a>Dividir dados em partições

Use esta opção quando você quiser dividir o conjunto de dados em subconjuntos deles. Essa opção também é útil quando você deseja criar um número personalizado de dobras para validação cruzada ou para dividir linhas em vários grupos.

1. Adicione a **partição e** o módulo de exemplo ao seu pipeline e conecte o conjunto de espaço.

2. Para o **modo de partição ou de exemplo**, selecione **atribuir a dobras**.

3. **Usar substituição no particionamento**: Selecione esta opção se desejar que a linha de amostra seja colocada de volta no pool de linhas para reutilização em potencial. Como resultado, a mesma linha pode ser atribuída a várias dobras.

    Se você não usar a substituição (a opção padrão), a linha de amostra não será colocada de volta no pool de linhas para reutilização potencial. Como resultado, cada linha pode ser atribuída a apenas uma dobra.

4. **Divisão aleatória**: Selecione esta opção se desejar que as linhas sejam atribuídas aleatoriamente a dobras.

    Se você não selecionar essa opção, as linhas serão atribuídas a dobras usando o método Round Robin.

5. **Semente aleatória**: opcionalmente, digite um inteiro para usar como o valor de semente. Essa opção será importante se você quiser que as linhas sejam divididas da mesma maneira toda vez. Caso contrário, o valor padrão de 0 significa que uma semente de início aleatório será usada.

6. **Especifique o método do particionador**: indique como você deseja que os dados sejam distribuídos para cada partição, usando estas opções:

    - **Particionar uniformemente**: Use essa opção para inserir um número igual de linhas em cada partição. Para especificar o número de partições de saída, digite um número inteiro na caixa de texto **especificar número de dobras a serem divididas uniformemente** .

    - **Partição com proporções personalizadas**: Use essa opção para especificar o tamanho de cada partição como uma lista separada por vírgulas.

        Por exemplo, se você quiser criar três partições, com a primeira partição contendo 50% dos dados e as duas partições restantes, cada uma contendo 25% dos dados, clique na caixa **de texto lista de proporções separadas por vírgula** e digite estes números: @no_ _t_1_`.5, .25, .25`

        A soma de todos os tamanhos de partição deve somar exatamente 1.

        - Se você inserir números que se somam a **menos de 1**, uma partição extra será criada para manter as linhas restantes. Por exemplo, se você digitar os valores 0,2 e 3, será criada uma terceira partição que contém a porcentagem restante de 50% de todas as linhas.

        - Se você inserir números que somam **mais de 1**, um erro será gerado quando você executar o pipeline.

7. **Divisão de desratificação**: Selecione esta opção se desejar que as linhas sejam desratificadas quando divididas e, em seguida, escolha a _coluna Strata_.

8. Executar o pipeline.

    Com essa opção, o módulo gera vários conjuntos de resultados, particionados usando as regras que você especificou.

### <a name="use-data-from-a-predefined-partition"></a>Usar dados de uma partição predefinida  

Essa opção é usada quando você divide um conjunto de um DataSet em várias partições e agora deseja carregar cada partição por vez para análise ou processamento posterior.

1. Adicione a **partição e** o módulo de exemplo ao pipeline.

2. Conecte-o à saída de uma instância anterior de **partição e exemplo**. Essa instância deve ter usado a opção **atribuir a dobras** para gerar um número de partições.

3. **Modo de partição ou de exemplo**: selecione Selecionar **dobra**.

4. **Especifique a qual dobra deve ser amostrada**: selecione uma partição a ser usada digitando seu índice. Índices de partição são baseados em 1. Por exemplo, se você dividiu o conjunto de um em três partes, as partições teriam os índices 1, 2 e 3.

    Se você digitar um valor de índice inválido, um erro de tempo de design será gerado: "Error 0018: DataSet contém dados inválidos".

    Além de agrupar o conjunto de um por dobras, você pode separar o conjunto de um em dois grupos: uma dobra de destino e todo o resto. Para fazer isso, digite o índice de uma única dobra e, em seguida, selecione a opção **escolher complemento da dobra selecionada**para obter tudo, exceto os dados na dobra especificada.

5. Se você estiver trabalhando com várias partições, deverá adicionar mais instâncias do módulo **partição e exemplo** para lidar com cada partição.

    Por exemplo, digamos que os pacientes particionados anteriormente em quatro dobras usando a idade. Para trabalhar com cada dobra individual, você precisa de quatro cópias da **partição e** do módulo de exemplo e, em cada uma delas, selecione uma dobra diferente, conforme mostrado abaixo. Não está correto usar a saída **atribuir para partições** diretamente.  

    [![partição e exemplo](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Executar o pipeline.

    Com essa opção, o módulo gera um único conjunto de registros contendo apenas as linhas atribuídas a essa dobra.

> [!NOTE]
>  Você não pode exibir as designações de dobra diretamente; Eles estão presentes apenas nos metadados.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 