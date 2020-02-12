---
title: 'Partição e exemplo: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo partição e exemplo no Azure Machine Learning para executar a amostragem em um conjunto de informações ou para criar partições a partir de seu conjunto de informações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 772c16dc292d8bce4b927c9c2ce3ff6ee0ed399d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152130"
---
# <a name="partition-and-sample-module"></a>Módulo de partição e exemplo

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para executar a amostragem em um conjunto de um ou para criar partições do conjunto de seus.

A amostragem é uma ferramenta importante no aprendizado de máquina, pois permite reduzir o tamanho de um conjunto de informações mantendo a mesma proporção de valores. Este módulo dá suporte a várias tarefas relacionadas que são importantes no aprendizado de máquina: 

- Dividir seus dados em várias subseções do mesmo tamanho. 

    Você pode usar as partições para validação cruzada ou para atribuir casos a grupos aleatórios.

- Separar dados em grupos e, em seguida, trabalhar com dados de um grupo específico. 

    Depois de atribuir aleatoriamente casos a grupos diferentes, talvez seja necessário modificar os recursos associados a apenas um grupo.

- Exemplos. 

    Você pode extrair uma porcentagem dos dados, aplicar a amostragem aleatória ou escolher uma coluna a ser usada para balancear o conjunto e executar a amostragem de sobreratificação em seus valores.

- Criando um conjunto de um DataSet menor para teste. 

    Se tiver muitos dados, poderá querer utilizar apenas as primeiras linhas *n* durante a configuração do pipeline e, em seguida, mudar para utilizar o conjunto de dados completo quando construir o seu modelo. Você também pode usar a amostragem para criar um conjunto de um DataSet menor para uso no desenvolvimento.

## <a name="configure-partition-and-sample"></a>Configurar partição e exemplo

Esse módulo dá suporte a vários métodos para dividir seus dados em partições ou para amostragem. Escolha o método primeiro e, em seguida, defina as opções adicionais exigidas pelo método.

- Head
- Amostragem
- Atribuir a dobras
- Selecionar dobra

### <a name="get-top-n-rows-from-a-dataset"></a>Obter as N primeiras linhas de um conjunto de registros

Utilize este modo para obter apenas as primeiras linhas *n.* Essa opção será útil se você quiser testar um pipeline em um pequeno número de linhas e não precisar que os dados sejam balanceados ou amostrados de qualquer forma.

1. Adicione o módulo **de partilha e amostra** ao seu pipeline na interface e ligue o conjunto de dados.  

2. **Modo de partilha ou amostra**: Desloque esta opção para **cabeça**.

3. **Número de linhas para selecionar:** Digite o número de linhas para devolver.

    O número de linhas especificado deve ser um inteiro não negativo. Se o número de linhas selecionadas for maior do que o número de linhas no conjunto de registros, o conjunto de um inteiro será retornado.

4. Executar o pipeline.

O módulo gera um único conjunto de registros contendo apenas o número especificado de linhas. As linhas são sempre lidas na parte superior do conjunto de registros.

### <a name="create-a-sample-of-data"></a>Criar uma amostra de dados

Essa opção dá suporte à amostragem aleatória simples ou à amostragem aleatória de sobreratificação. Isso será útil se você quiser criar um conjunto de um DataSet de exemplo menor para teste.

1. Adicione o módulo **de partilha e amostra** ao seu pipeline e ligue o conjunto de dados.

2. **Modo de partilha ou amostra:** Desloque isto para **A Amostragem**.

3. **Taxa de amostragem**: Digite um valor entre 0 e 1. Esse valor especifica a porcentagem de linhas do conjunto de registros de origem que devem ser incluídas no conjunto de resultados de saída.

    Por exemplo, se quiser apenas metade do conjunto de dados original, escreva `0.5` indique que a taxa de amostragem deve ser de 50%.

    As linhas do conjunto de dados de entrada são embaralhadas e colocadas seletivamente no conjunto de dados de saída, de acordo com a razão especificada.

4. **Sementes aleatórias para amostragem**: Opcionalmente, digite uma nota para usar como um valor de semente.

    Essa opção será importante se você quiser que as linhas sejam divididas da mesma maneira toda vez. O valor padrão é 0, o que significa que uma semente inicial é gerada com base no relógio do sistema. Isso pode levar a resultados ligeiramente diferentes cada vez que você executar o pipeline.

5. **Divisão estratificada para amostragem**: Selecione esta opção se for importante que as linhas do conjunto de dados sejam divididas uniformemente por alguma coluna chave antes de provar.

    Para a **coluna de teclas estratificação para amostragem,** selecione uma única coluna de *estratos* para utilizar ao dividir o conjunto de dados. As linhas no conjunto de registros são então divididas da seguinte maneira:

    1. Todas as linhas de entrada são agrupadas (desratificadas) pelos valores na coluna Strata especificada.

    2. As linhas são embaralhadas dentro de cada grupo.

    3. Cada grupo é adicionado seletivamente ao conjunto de resultados de saída para atender à razão especificada.


6. Executar o pipeline.

    Com essa opção, o módulo gera um único conjunto de dados que contém uma amostragem representativa dos dados. A parte restante, não amostrada do conjunto de resultados não é a saída. 

## <a name="split-data-into-partitions"></a>Dividir dados em partições

Use esta opção quando você quiser dividir o conjunto de dados em subconjuntos deles. Essa opção também é útil quando você deseja criar um número personalizado de dobras para validação cruzada ou para dividir linhas em vários grupos.

1. Adicione o módulo **de partilha e amostra** ao seu pipeline e ligue o conjunto de dados.

2. Para **o modo de partilha ou amostra,** selecione Atribuir a **dobras**.

3. **Utilize a substituição na divisória**: Selecione esta opção se pretender que a linha amostrada seja reposta na piscina de linhas para potencial reutilização. Como resultado, a mesma linha pode ser atribuída a várias dobras.

    Se você não usar a substituição (a opção padrão), a linha de amostra não será colocada de volta no pool de linhas para reutilização potencial. Como resultado, cada linha pode ser atribuída a apenas uma dobra.

4. **Divisão aleatória**: Selecione esta opção se pretender que as linhas sejam atribuídas aleatoriamente a dobras.

    Se você não selecionar essa opção, as linhas serão atribuídas a dobras usando o método Round Robin.

5. **Semente aleatória**: Opcionalmente, digite uma prótese para usar como valor de semente. Essa opção será importante se você quiser que as linhas sejam divididas da mesma maneira toda vez. Caso contrário, o valor padrão de 0 significa que uma semente de início aleatório será usada.

6. **Especifique o método de partição**: Indique como pretende que os dados sejam distribuídos por cada partição, utilizando estas opções:

    - **Partição uniformemente**: Utilize esta opção para colocar um número igual de linhas em cada partição. Para especificar o número de divisórias de saída, digite um número inteiro no **número de dobras especificada para dividir uniformemente em** caixa de texto.

    - **Partição com proporções personalizadas**: Utilize esta opção para especificar o tamanho de cada divisória como uma lista separada da víramida.

        Por exemplo, se pretender criar três divisórias, com a primeira partição contendo 50% dos dados, e as restantes duas divisórias que contêm 25% dos dados, clique na Lista de proporções separadas por caixa de texto **vírina** e digite estes números: `.5, .25, .25`

        A soma de todos os tamanhos de partição deve somar exatamente 1.

        - Se introduzir números que somam **menos de 1**, é criada uma partição extra para manter as restantes linhas. Por exemplo, se você digitar os valores 0,2 e 3, será criada uma terceira partição que contém a porcentagem restante de 50% de todas as linhas.

        - Se introduzir números que somam mais de **1**, um erro é levantado quando executa o gasoduto.

7. **Divisão estratificada**: Selecione esta opção se quiser que as linhas sejam estratificadas quando divididas e, em seguida, escolha a coluna de _estratos_.

8. Executar o pipeline.

    Com essa opção, o módulo gera vários conjuntos de resultados, particionados usando as regras que você especificou.

### <a name="use-data-from-a-predefined-partition"></a>Usar dados de uma partição predefinida  

Essa opção é usada quando você divide um conjunto de um DataSet em várias partições e agora deseja carregar cada partição por vez para análise ou processamento posterior.

1. Adicione o módulo **de partição e amostra** ao gasoduto.

2. Conecte-o à saída de um caso anterior de **Partição e Amostra**. Esta instância deve ter usado a opção **Atribuir a Folds** para gerar um número de divisórias.

3. **Modo de partição ou amostra**: Selecione **Pick Fold**.

4. **Especifique qual a dobra a amostrar**a partir de : Selecione uma divisória para utilizar digitando o seu índice. Índices de partição são baseados em 1. Por exemplo, se você dividiu o conjunto de um em três partes, as partições teriam os índices 1, 2 e 3.

    Se você digitar um valor de índice inválido, um erro de tempo de design será gerado: "Error 0018: DataSet contém dados inválidos".

    Além de agrupar o conjunto de um por dobras, você pode separar o conjunto de um em dois grupos: uma dobra de destino e todo o resto. Para isso, digite o índice de uma única dobra e, em seguida, selecione a opção, **Escolha o complemento da dobra selecionada,** para obter tudo menos os dados na dobra especificada.

5. Se estiver a trabalhar com várias divisórias, deve adicionar instâncias adicionais do módulo **de partilha e amostra** para manusear cada partição.

    Por exemplo, digamos que os pacientes particionados anteriormente em quatro dobras usando a idade. Para trabalhar com cada dobra individual, precisa de quatro cópias do módulo **Partition and Sample** e, em cada uma, selecione uma dobra diferente, como mostrado abaixo. Não é correto utilizar a saída de **Atribuir a dobrar** diretamente.  

    [![Partição e amostra](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Executar o pipeline.

    Com essa opção, o módulo gera um único conjunto de registros contendo apenas as linhas atribuídas a essa dobra.

> [!NOTE]
>  Você não pode exibir as designações de dobra diretamente; Eles estão presentes apenas nos metadados.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 