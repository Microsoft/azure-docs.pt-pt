---
title: 'Partição e amostras: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de partição e amostras no serviço Azure Machine Learning para executar a amostragem num conjunto de dados ou para criar partições do seu conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 72f9e11e3582d804eecc7479ea079276564bd12f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029284"
---
# <a name="partition-and-sample-module"></a>Módulo de partição e amostras

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para executar a amostragem num conjunto de dados ou para criar partições do seu conjunto de dados.

A amostragem é uma ferramenta importante no machine learning porque permite-lhe reduzir o tamanho de um conjunto de dados, mantendo a mesma proporção de valores. Este módulo suporta várias tarefas relacionadas que são importantes no machine learning: 

- Dividir os seus dados nas subsecções várias do mesmo tamanho. 

    Poderá utilizar as partições para a validação cruzada, ou atribuir casos a grupos aleatórios.

- Separação de dados em grupos e, em seguida, trabalhar com dados a partir de um grupo específico. 

    Depois de atribuir aleatoriamente casos para diferentes grupos, poderá ter de modificar as funcionalidades que estão associadas apenas um grupo.

- Amostragem. 

    Pode extrair uma percentagem dos dados, aplicar a amostragem aleatória ou escolha uma coluna a utilizar para o conjunto de dados de balanceamento e executar a amostragem stratified em seus valores.

- A criação de um conjunto de dados mais pequeno para fins de teste. 

    Se tiver uma grande quantidade de dados, pode querer utilizar apenas os primeiros *n* linhas ao configurar a experimentação e, em seguida, mude para o conjunto de dados completo a utilizar quando criar o seu modelo. Também pode utilizar a amostragem para criar o conjunto de dados mais pequeno do s para uso em desenvolvimento.

## <a name="configure-partition-and-sample"></a>Configurar a partição e amostras

Este módulo suporta vários métodos para dividir os seus dados em partições ou para a amostragem. Escolher o método primeiro e, em seguida, o conjunto de opções adicionais necessários pelo método.

- Head
- Amostragem
- Atribuir a subconjuntos
- Escolha a subconjuntos de validação

### <a name="get-top-n-rows-from-a-dataset"></a>Obter linhas de TOP N a partir de um conjunto de dados

Utilize este modo para obter apenas os primeiros *n* linhas. Esta opção é útil se desejar testar uma experimentação num pequeno número de linhas e não precisar dos dados para ser balanceada ou objeto de amostragem de qualquer forma.

1. Adicionar a **partição e amostras** módulo à sua experimentação na interface e ligue-se o conjunto de dados.  

2. **Modo de partição ou exemplo**: Defina esta opção como **Head**.

3. **Número de linhas para selecionar**: Escreva o número de linhas a devolver.

    O número de linhas que especificar tem de ser um número inteiro não negativo. Se o número de linhas selecionadas é maior do que o número de linhas no conjunto de dados, é devolvido o conjunto de dados inteiro.

4. Execute a experimentação.

O módulo produz um único conjunto de dados que contém apenas o número especificado de linhas. As linhas sempre são lidas a partir da parte superior do conjunto de dados.

### <a name="create-a-sample-of-data"></a>Criar um exemplo de dados

Esta opção suporta amostragem aleatória simple ou stratified amostragem aleatória. Isto é útil se pretender criar um conjunto de dados de amostra representativa menor para fins de teste.

1. Adicionar a **partição e amostras** módulo à sua experimentação e ligue-se o conjunto de dados.

2. **Modo de partição ou exemplo**: Defina esta opção como **amostragem**.

3. **Taxa de amostragem**: Escreva um valor entre 0 e 1. Este valor Especifica a percentagem de linhas do conjunto de dados origem que devem ser incluídas no conjunto de dados de saída.

    Por exemplo, se quiser apenas metade do conjunto de dados original, digite `0.5` para indicar que a taxa de amostragem deve ser 50%.

    As linhas do conjunto de dados de entrada são combinadas de forma aleatória e colocar o conjunto de dados de saída, seletivamente, de acordo com a proporção especificada.

4. **Seed aleatório para a amostragem**: Opcionalmente, escreva um número inteiro para utilizar como um valor de seed.

    Esta opção é importante se pretender que as linhas a ser divididos da mesma forma cada vez. O valor predefinido é 0, o que significa que uma semente inicial é gerada com base no relógio do sistema. Isso pode levar a resultados ligeiramente diferentes sempre que executar a experimentação.

5. **Divisão stratified amostragem**: Selecione esta opção se for importante que as linhas no conjunto de dados devem ser divididas uniformemente por alguns coluna chave antes de amostragem.

    Para **coluna de chaves de Stratification para amostragem**, selecione uma única *coluna strata* a utilizar ao dividir o conjunto de dados. As linhas no conjunto de dados, em seguida, são divididas da seguinte forma:

    1. Todas as linhas de entrada são agrupadas (stratified) pelos valores na coluna strata especificado.

    2. Linhas são combinadas de forma aleatória em cada grupo.

    3. Cada grupo seletivamente é adicionado ao conjunto de dados de saída para satisfazer a proporção especificada.


6. Execute a experimentação.

    Com esta opção, o módulo produz um único conjunto de dados que contém uma parcela representativa dos dados. A parte restante, unsampled do conjunto de dados não é de saída. 

## <a name="split-data-into-partitions"></a>Dividir os dados em partições

Utilize esta opção quando pretender dividir o conjunto de dados em subconjuntos de dados. Esta opção também é útil quando desejar criar um número personalizado de subconjuntos de validação cruzada de ou para dividir linhas em vários grupos.

1. Adicionar a **partição e amostras** módulo à sua experimentação e ligue-se o conjunto de dados.

2. Para **partição ou o modo de exemplo**, selecione **atribuir a subconjuntos**.

3. **Utilizar a substituição no particionamento**: Selecione esta opção se pretender que a linha de amostragem para ser colocada para trás no conjunto de linhas para potencial reutilização. Como resultado, a mesma linha forem atribuída aos diversos subconjuntos.

    Se não utilizar a substituição (a opção predefinida), a linha de amostragem não é colocada novamente em conjunto de linhas para potencial reutilização. Como resultado, cada linha pode ser atribuída a apenas uma dobra.

4. **Divisão aleatório**:  Selecione esta opção se pretender que as linhas a ser atribuídos aleatoriamente a subconjuntos.

    Se não selecionar esta opção, as linhas são atribuídas a subconjuntos usando o método de round robin.

5. **Seed aleatório**: Opcionalmente, escreva um número inteiro para utilizar como o valor de seed. Esta opção é importante se pretender que as linhas a ser divididos da mesma forma cada vez. Caso contrário, o valor predefinido de 0 significa que uma aleatória a partir de semente será utilizada.

6. **Especificar o método particionador**: Indica como pretende que dados sejam distribuídos de acordo ser com para cada partição, com estas opções:

    - **Particionar uniformemente**: Utilize esta opção para colocar um número igual de linhas em cada partição. Para especificar o número de partições de saída, escreva um número inteiro no **especifique o número de subconjuntos de dividir uniformemente em** caixa de texto.

    - **Partição com proporções personalizadas**: Utilize esta opção para especificar o tamanho de cada partição como uma lista separada por vírgulas.

        Por exemplo, se quiser criar três partições, com a primeira partição que contém a 50% dos dados e as duas partições restantes cada 25% que contém os dados, clique a **lista de proporções separados por vírgula** caixa de texto, e Escreva esses números: `.5, .25, .25`

        A soma de todos os tamanhos de partição tem de adicionar até 1 exatamente.

        - Se introduzir números que adicionam até **menos de 1**, uma partição extra é criada para conter as linhas restantes. Por exemplo, se digitar a partição de valores.2 e.3, um terceiro é criada-que mantém os restantes 50 por cento de todas as linhas.

        - Se introduzir números que adicionam até **mais do que 1**, será gerado um erro ao executar a experimentação.

7. **Divisão stratified**: Selecione esta opção se pretender que as linhas a ser stratified quando dividir e, em seguida, escolha o _coluna de strata_.

8. Execute a experimentação.

    Com esta opção, o módulo produz vários conjuntos de dados, particionados usando as regras que especificou.

### <a name="use-data-from-a-predefined-partition"></a>Utilizar dados de uma partição predefinida  

Esta opção é utilizada quando o utilizador ter dividido um conjunto de dados em várias partições e agora pretende carregar cada partição por sua vez para análise adicional ou processamento.

1. Adicionar a **partição e amostras** módulo para a experimentação.

2. Ligue-o para a saída de uma instância anterior do **partição e amostras**. Essa instância tem de ter utilizado o **atribuir a subconjuntos** opção para gerar um número de partições.

3. **Modo de partição ou exemplo**: Selecione **escolher subconjuntos de validação**.

4. **Especifique que dobrar a amostra de**: Selecione uma partição a utilizar ao escrever o seu índice. Índices de partição são baseados em 1. Por exemplo, se o conjunto de dados é dividido em três partes, as partições teria os índices 1, 2 e 3.

    Se digitar um valor de índice inválido, é gerado um erro de tempo de design: "Erro 0018: Conjunto de dados contém dados inválidos."

    Para além de agrupamento do conjunto de dados por subconjuntos, pode separar o conjunto de dados em dois grupos: uma dobra de destino e tudo o resto. Para fazê-lo, escreva o índice de um único subconjuntos de validação e, em seguida, selecione a opção **escolher complemento de subconjuntos de validação selecionado**, para fazer com que tudo, mas os dados em subconjuntos de validação especificado.

5. Se estiver a trabalhar com várias partições, tem de adicionar instâncias adicionais do **partição e amostras** módulo para lidar com cada partição.

    Por exemplo, digamos pacientes anteriormente particionados em quatro subconjuntos com a idade. Para trabalhar com cada subconjuntos de validação individuais, terá quatro cópias da **partição e amostras** módulo, e em cada uma, selecionar uma dobra diferente, conforme mostrado abaixo. Não é correto utilizar o **atribuir a subconjuntos** diretamente de saída.  

    [![Partição e amostras](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Execute a experimentação.

    Com esta opção, o módulo produz um único conjunto de dados que contém apenas as linhas atribuídas a esse subconjuntos de validação.

> [!NOTE]
>  Não é possível ver as designações de subconjuntos de validação diretamente; eles estão presentes apenas nos metadados.

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 