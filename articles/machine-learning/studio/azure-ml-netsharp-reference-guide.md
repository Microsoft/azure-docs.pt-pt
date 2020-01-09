---
title: 'Redes neurais personalizadas net #'
titleSuffix: ML Studio (classic) - Azure
description: 'Guia de sintaxe para a linguagem de especificação de redes neurais net #. Saiba como criar modelos de rede neural personalizados no Azure Machine Learning Studio (clássico).'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: 295cac883e7c84158fd9d2a2b7e9780dfe6c64d6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427673"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning-studio-classic"></a>Guia para a linguagem de especificação de rede neural net # para Azure Machine Learning Studio (clássico)

NET # é uma linguagem desenvolvida pela Microsoft que é usada para definir arquiteturas de rede neurais complexas, como redes neurais profundas ou convolução de dimensões arbitrárias. Você pode usar estruturas complexas para melhorar o aprendizado de dados como imagem, vídeo ou áudio.

Você pode usar uma especificação de arquitetura net # nestes contextos:

+ Todos os módulos de rede neural no Microsoft Azure Machine Learning Studio (clássico): [rede neural multiclasse](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [rede neural de duas classes](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network)e [regressão de rede neural](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ As funções de rede neural no Microsoft ML Server: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) e [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)para a linguagem R e [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) para Python.


Este artigo descreve os conceitos básicos e a sintaxe necessária para desenvolver uma rede neural personalizada usando net #:

+ Requisitos de rede neural e como definir os componentes principais
+ A sintaxe e as palavras-chave da linguagem de especificação net #
+ Exemplos de redes neurais personalizadas criadas usando net #



## <a name="neural-network-basics"></a>Noções básicas de rede neural

Uma estrutura de rede neural consiste em nós que são organizados em camadas e conexões ponderadas (ou bordas) entre os nós. As conexões são direcionais e cada conexão tem um nó de origem e um nó de destino.

Cada camada de treinamento (uma camada oculta ou de saída) tem um ou mais **grupos de conexão**. Um grupo de conexões consiste em uma camada de origem e uma especificação das conexões dessa camada de origem. Todas as conexões em um determinado grupo compartilham camadas de origem e de destino. Em net #, um grupo de conexões é considerado como pertencente à camada de destino do pacote.

NET # dá suporte a vários tipos de grupos de conexão, que permitem que você personalize a maneira como as entradas são mapeadas para camadas ocultas e mapeadas para as saídas.

O pacote padrão ou padrão é um **grupo completo**, no qual cada nó na camada de origem é conectado a cada nó na camada de destino.

Além disso, net # dá suporte aos quatro tipos de grupos de conexão avançados a seguir:

+ **Pacotes filtrados**. Você pode definir um predicado usando os locais do nó da camada de origem e o nó da camada de destino. Os nós são conectados sempre que o predicado é true.

+ **Grupos de convolução**. Você pode definir pequenos bairros de nós na camada de origem. Cada nó na camada de destino é conectado a uma vizinhança de nós na camada de origem.

+ Agrupamentos de **pools** e de **normalização de resposta**. Eles são semelhantes aos grupos de convolução, pois o usuário define pequenos bairros de nós na camada de origem. A diferença é que os pesos das bordas desses grupos não são treináveis. Em vez disso, uma função predefinida é aplicada aos valores de nó de origem para determinar o valor do nó de destino.


## <a name="supported-customizations"></a>Supported customizations (Personalizações avançadas)

A arquitetura dos modelos de rede neural que você cria no Azure Machine Learning Studio (clássico) pode ser amplamente personalizada com o uso de net #. Pode:

+ Crie camadas ocultas e controle o número de nós em cada camada.
+ Especifique como as camadas devem ser conectadas entre si.
+ Defina estruturas de conectividade especiais, como convolução e agrupamentos de compartilhamento de peso.
+ Especifique funções de ativação diferentes.

Para obter detalhes sobre a sintaxe da linguagem de especificação, consulte [especificação de estrutura](#structure-specifications).

Para obter exemplos de como definir redes neurais para algumas tarefas comuns de aprendizado de máquina, de simplex a complexo, consulte [exemplos](#examples-of-net-usage).

## <a name="general-requirements"></a>Requisitos gerais

+ Deve haver exatamente uma camada de saída, pelo menos uma camada de entrada e zero ou mais camadas ocultas.
+ Cada camada tem um número fixo de nós, organizada conceitualmente em uma matriz retangular de dimensões arbitrárias.
+ As camadas de entrada não têm parâmetros treinados associados e representam o ponto em que os dados da instância entram na rede.
+ As camadas treináveis (as camadas ocultas e de saída) têm parâmetros treinados associados, conhecidos como pesos e tendências.
+ Os nós de origem e de destino devem estar em camadas separadas.
+ As conexões devem ser acíclico; em outras palavras, não pode haver uma cadeia de conexões levando de volta ao nó de origem inicial.
+ A camada de saída não pode ser uma camada de origem de um grupo de conexões.

## <a name="structure-specifications"></a>Especificações da estrutura

Uma especificação de estrutura de rede neural é composta de três seções: a **declaração de constante**, a declaração de **camada**, a **declaração de conexão**. Também há uma seção opcional de **declaração de compartilhamento** . As seções podem ser especificadas em qualquer ordem.

## <a name="constant-declaration"></a>Declaração de constante

Uma declaração de constante é opcional. Ele fornece um meio para definir valores usados em outro lugar na definição de rede neural. A instrução de declaração consiste em um identificador seguido por um sinal de igual e uma expressão de valor.

Por exemplo, a instrução a seguir define uma constante `x`:

`Const X = 28;`

Para definir duas ou mais constantes simultaneamente, coloque os nomes e valores dos identificadores entre chaves e separe-os usando ponto e vírgula. Por exemplo:

`Const { X = 28; Y = 4; }`

O lado direito de cada expressão de atribuição pode ser um inteiro, um número real, um valor booliano (true ou false) ou uma expressão matemática. Por exemplo:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Declaração de camada

A declaração de camada é necessária. Ele define o tamanho e a origem da camada, incluindo seus pacotes de conexão e atributos. A instrução de declaração começa com o nome da camada (entrada, oculta ou saída), seguida pelas dimensões da camada (uma tupla de inteiros positivos). Por exemplo:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ O produto das dimensões é o número de nós na camada. Neste exemplo, há duas dimensões [5, 20], o que significa que há 100 nós na camada.
+ As camadas podem ser declaradas em qualquer ordem, com uma exceção: se mais de uma camada de entrada for definida, a ordem na qual elas são declaradas deve corresponder à ordem dos recursos nos dados de entrada.

Para especificar que o número de nós em uma camada seja determinado automaticamente, use a palavra-chave `auto`. A palavra-chave `auto` tem efeitos diferentes, dependendo da camada:

+ Em uma declaração de camada de entrada, o número de nós é o número de recursos nos dados de entrada.
+ Em uma declaração de camada oculta, o número de nós é o número especificado pelo valor do parâmetro para o **número de nós ocultos**.
+ Em uma declaração de camada de saída, o número de nós é 2 para classificação de duas classes, 1 para regressão e igual ao número de nós de saída para classificação multiclasse.

Por exemplo, a seguinte definição de rede permite que o tamanho de todas as camadas seja determinado automaticamente:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

Uma declaração de camada para uma camada de treinamento (as camadas ocultas ou de saída) pode, opcionalmente, incluir a função de saída (também chamada de função de ativação), que usa como padrão **sigmoide** para modelos de classificação e **linear** para modelos de regressão. Mesmo que você use o padrão, você pode declarar explicitamente a função de ativação, se desejado para fins de clareza.

Há suporte para as seguintes funções de saída:

+ sigmoid
+ linear
+ softmax
+ rlinear
+ quadrado
+ sqrt
+ srlinear
+ ABS
+ Tanh
+ brlinear

Por exemplo, a declaração a seguir usa a função **softmax** :

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Declaração de conexão

Imediatamente após definir a camada de treinamento, você deve declarar conexões entre as camadas que você definiu. A declaração do grupo de conexões começa com a palavra-chave `from`, seguida pelo nome da camada de origem do grupo e o tipo de grupo de conexão a ser criado.

Atualmente, há suporte para cinco tipos de grupos de conexão:

+ Grupos **completos** , indicados pela palavra-chave `all`
+ Pacotes **filtrados** , indicados pela palavra-chave `where`, seguido por uma expressão de predicado
+ Grupos de **convolução** , indicados pela palavra-chave `convolve`, seguidos pelos atributos de convolução
+ Agrupamentos de **pools** , indicados pelas palavras-chave **Max pool** ou **Mean pool**
+ Grupos de **normalização de resposta** , indicados pela **norma de resposta** de palavra-chave

## <a name="full-bundles"></a>Grupos completos

Um grupo de conexão completa inclui uma conexão de cada nó na camada de origem para cada nó na camada de destino. Esse é o tipo de conexão de rede padrão.

## <a name="filtered-bundles"></a>Pacotes filtrados

Uma especificação de pacote de conexão filtrada inclui um predicado, expresso sintaticamente C# , de forma semelhante a uma expressão lambda. O exemplo a seguir define dois grupos filtrados:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ No predicado para `ByRow`, `s` é um parâmetro que representa um índice na matriz retangular de nós da camada de entrada, `Pixels`e `d` é um parâmetro que representa um índice na matriz de nós da camada oculta, `ByRow`. O tipo de `s` e `d` é uma tupla de inteiros de comprimento dois. Conceitualmente, `s` intervalos em todos os pares de inteiros com `0 <= s[0] < 10` e `0 <= s[1] < 20`e `d` intervalos em todos os pares de inteiros, com `0 <= d[0] < 10` e `0 <= d[1] < 12`.

+ No lado direito da expressão de predicado, há uma condição. Neste exemplo, para cada valor de `s` e `d` de modo que a condição seja verdadeira, há uma borda do nó da camada de origem para o nó da camada de destino. Portanto, essa expressão de filtro indica que o pacote inclui uma conexão do nó definido por `s` para o nó definido por `d` em todos os casos em que s [0] é igual a d [0].

Opcionalmente, você pode especificar um conjunto de pesos para um pacote filtrado. O valor do atributo **Weights** deve ser uma tupla de valores de ponto flutuante com um comprimento que corresponda ao número de conexões definido pelo pacote. Por padrão, os pesos são gerados aleatoriamente.

Os valores de peso são agrupados pelo índice do nó de destino. Ou seja, se o primeiro nó de destino estiver conectado a nós de origem K, os primeiros `K` elementos da tupla de **pesos** serão os pesos para o primeiro nó de destino, na ordem de índice de origem. O mesmo se aplica aos nós de destino restantes.

É possível especificar pesos diretamente como valores constantes. Por exemplo, se você aprendeu os pesos anteriormente, você pode especificá-los como constantes usando essa sintaxe:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Grupos de convolução

Quando os dados de treinamento têm uma estrutura homogênea, as conexões de convolução são normalmente usadas para aprender recursos de alto nível dos dados. Por exemplo, em dados de imagem, áudio ou vídeo, a dimensionalidade espacial ou temporal pode ser razoavelmente uniforme.

Os pacotes de convolução empregam **kernels** retangulares que são sliddos por meio das dimensões. Essencialmente, cada kernel define um conjunto de pesos aplicado em vizinhanças locais, chamados de **aplicativos kernel**. Cada aplicativo de kernel corresponde a um nó na camada de origem, que é chamado de **nó central**. Os pesos de um kernel são compartilhados entre várias conexões. Em um pacote de convolução, cada kernel é retangular e todos os aplicativos de kernel têm o mesmo tamanho.

Os grupos de convolução dão suporte aos seguintes atributos:

**InputShape** define a dimensionalidade da camada de origem para os fins deste pacote de convolução. O valor deve ser uma tupla de inteiros positivos. O produto dos inteiros deve ser igual ao número de nós na camada de origem, mas, caso contrário, ele não precisa corresponder à dimensionalidade declarada para a camada de origem. O comprimento dessa tupla se torna o valor de **aridade** para o grupo de convolução. Normalmente, arity refere-se ao número de argumentos ou operandos que uma função pode tomar.

Para definir a forma e os locais dos kernels, use os atributos **KernelShape**, **Stride**, **Padding**, **LowerPad**e **UpperPad**:

+ **KernelShape**: (obrigatório) define a dimensionalidade de cada kernel para o grupo de convolução. O valor deve ser uma tupla de inteiros positivos com um comprimento igual ao arity do pacote. Cada componente desta tupla não deve ser maior que o componente correspondente do **InputShape**.

+ **Stride**: (opcional) define os tamanhos de etapa deslizantes da convolução (um tamanho de etapa para cada dimensão), que é a distância entre os nós centrais. O valor deve ser uma tupla de inteiros positivos com um comprimento que seja a arity do pacote. Cada componente desta tupla não deve ser maior que o componente correspondente do **KernelShape**. O valor padrão é uma tupla com todos os componentes iguais a um.

+ **Compartilhamento**: (opcional) define o compartilhamento de peso para cada dimensão da convolução. O valor pode ser um único valor booliano ou uma tupla de valores Boolianos com um comprimento que seja a arity do pacote. Um único valor booliano é estendido para ser uma tupla do comprimento correto com todos os componentes iguais ao valor especificado. O valor padrão é uma tupla que consiste em todos os valores verdadeiros.

+ **MapCount**: (opcional) define o número de mapas de recursos para o grupo de convolução. O valor pode ser um único inteiro positivo ou uma tupla de inteiros positivos com um comprimento que seja a arity do pacote. Um único valor inteiro é estendido para ser uma tupla do comprimento correto com os primeiros componentes iguais ao valor especificado e a todos os componentes restantes iguais a um. O valor padrão é um. O número total de mapas de recursos é o produto dos componentes da tupla. A fatoração desse número total entre os componentes determina como os valores do mapa de recursos são agrupados nos nós de destino.

+ **Pesos**: (opcional) define os pesos iniciais para o pacote. O valor deve ser uma tupla de valores de ponto flutuante com um comprimento que seja o número de kernels vezes o número de pesos por kernel, conforme definido posteriormente neste artigo. Os pesos padrão são gerados aleatoriamente.

Há dois conjuntos de propriedades que controlam o preenchimento, as propriedades que são mutuamente exclusivas:

+ **Padding**: (opcional) determina se a entrada deve ser preenchida usando um **esquema de preenchimento padrão**. O valor pode ser um único valor booliano ou pode ser uma tupla de valores Boolianos com um comprimento que seja a arity do pacote.

    Um único valor booliano é estendido para ser uma tupla do comprimento correto com todos os componentes iguais ao valor especificado.

    Se o valor de uma dimensão for true, a origem será preenchida logicamente nessa dimensão com células com valor zero para dar suporte a aplicativos kernel adicionais, de modo que os nós centrais do primeiro e do último kernels nessa dimensão sejam os primeiros e os últimos nós dimensão na camada de origem. Assim, o número de nós "fictícios" em cada dimensão é determinado automaticamente, para ajustar exatamente `(InputShape[d] - 1) / Stride[d] + 1` kernels na camada de origem preenchida.

    Se o valor de uma dimensão for false, os kernels serão definidos de forma que o número de nós de cada lado que são deixados de fora seja o mesmo (até uma diferença de 1). O valor padrão desse atributo é uma tupla com todos os componentes iguais a false.

+ **UpperPad** e **LowerPad**: (opcional) fornecem maior controle sobre a quantidade de preenchimento a ser usada. **Importante:** Esses atributos podem ser definidos se e somente se a propriedade **Padding** acima ***não*** estiver definida. Os valores devem ser tuplas com valor inteiro com comprimentos que sejam a arity do pacote. Quando esses atributos são especificados, os nós "fictícios" são adicionados às extremidades inferior e superior de cada dimensão da camada de entrada. O número de nós adicionados às extremidades inferior e superior em cada dimensão é determinado por **LowerPad**[i] e **UpperPad**[i] respectivamente.

    Para garantir que os kernels correspondam apenas a nós "reais" e não a nós "fictícios", as seguintes condições devem ser atendidas:
  - Cada componente de **LowerPad** deve ser estritamente menor que `KernelShape[d]/2`.
  - Cada componente de **UpperPad** não deve ser maior que `KernelShape[d]/2`.
  - O valor padrão desses atributos é uma tupla com todos os componentes iguais a 0.

    A configuração **Padding** = true permite tanto preenchimento quanto necessário para manter o "centro" do kernel dentro da entrada "real". Isso altera o cálculo um pouco para computar o tamanho da saída. Em geral, o tamanho de saída *D* é calculado como `D = (I - K) / S + 1`, em que `I` é o tamanho de entrada, `K` é o tamanho do kernel, `S` é o stride e `/` é a divisão de inteiro (arredondada para zero). Se você definir UpperPad = [1, 1], o tamanho de entrada `I` será efetivamente 29 e, portanto, `D = (29 - 5) / 2 + 1 = 13`. No entanto, quando **Padding** = true, essencialmente `I` é aumentado por `K - 1`; Portanto `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Ao especificar valores para **UpperPad** e **LowerPad** , você obtém muito mais controle sobre o preenchimento do que se apenas definir **Padding** = true.

Para obter mais informações sobre redes de revolução e seus aplicativos, consulte estes artigos:

+ [http://deeplearning.net/tutorial/lenet.html](http://deeplearning.net/tutorial/lenet.html)
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Agrupamentos de pools

Um **pacote de Pooling** aplica uma geometria semelhante à conectividade de convolução, mas usa funções predefinidas para valores de nó de origem para derivar o valor do nó de destino. Portanto, os grupos de Pooling não têm estado treinado (pesos ou tendências). Os grupos de Pooling dão suporte a todos os atributos de convolução, exceto **compartilhamento**, **MapCount**e **pesos**.

Normalmente, os kernels resumidos por unidades de Pooling adjacentes não se sobrepõem. Se Stride [d] for igual a KernelShape [d] em cada dimensão, a camada obtida será a camada de pool local tradicional, que normalmente é empregada em redes neurais de convolução. Cada nó de destino computa o máximo ou a média das atividades de seu kernel na camada de origem.

O exemplo a seguir ilustra um pacote de Pooling:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ A arity do pacote é 3: ou seja, o comprimento das tuplas `InputShape`, `KernelShape`e `Stride`.
+ O número de nós na camada de origem é `5 * 24 * 24 = 2880`.
+ Essa é uma camada de Pooling local tradicional porque **KernelShape** e **Stride** são iguais.
+ O número de nós na camada de destino é `5 * 12 * 12 = 1440`.

Para obter mais informações sobre camadas de pool, consulte estes artigos:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (seção 3,4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Grupos de normalização de resposta

A **normalização de resposta** é um esquema de normalização local que foi introduzido pela primeira vez por Geoffrey Hinton, et al, no papel [ImageNet classificação com redes neurais de convolução profundas](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

A normalização de resposta é usada para auxiliar a generalização em redes neurais. Quando um neurônio está sendo acionado em um nível de ativação muito alto, uma camada de normalização de resposta local suprime o nível de ativação dos neurônios circundantes. Isso é feito usando três parâmetros (`α`, `β`e `k`) e uma estrutura de convolução (ou forma de vizinhança). Cada neurônio na camada de destino **y** corresponde a um neurônio **x** na camada de origem. O nível de ativação de **y** é fornecido pela fórmula a seguir, em que `f` é o nível de ativação de um neurônio e `Nx` é o kernel (ou o conjunto que contém os neurônios na vizinhança de **x**), conforme definido pela seguinte estrutura de convolução:

![fórmula para estrutura de convolução](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Os grupos de normalização de resposta dão suporte a todos os atributos de convolução, exceto **compartilhamento**, **MapCount**e **pesos**.

+ Se o kernel contiver neurônios no mesmo mapa que ***x***, o esquema de normalização será chamado de **normalização de mapa**. Para definir a normalização de mapa, a primeira coordenada em **InputShape** deve ter o valor 1.

+ Se o kernel contiver neurônios na mesma posição espacial que ***x***, mas os neurônios estiverem em outros mapas, o esquema de normalização será chamado na **normalização de mapas**. Esse tipo de normalização de resposta implementa uma forma de inibição lateral inspirada pelo tipo encontrado em neurônios reais, criando competição para grandes níveis de ativação entre as saídas de neurônio computadas em mapas diferentes. Para definir a normalização de mapas, a primeira coordenada deve ser um número inteiro maior que um e não maior que o número de mapas, e o restante das coordenadas deve ter o valor 1.

Como os grupos de normalização de resposta aplicam uma função predefinida aos valores de nó de origem para determinar o valor do nó de destino, eles não têm estado treinado (pesos ou tendências).

> [!NOTE]
> Os nós na camada de destino correspondem a neurônios que são os nós centrais dos kernels. Por exemplo, se `KernelShape[d]` for ímpar, `KernelShape[d]/2` corresponderá ao nó do kernel central. Se `KernelShape[d]` for par, o nó central estará em `KernelShape[d]/2 - 1`. Portanto, se `Padding[d]` for false, o primeiro e o último `KernelShape[d]/2` nós não terão nós correspondentes na camada de destino. Para evitar essa situação, defina **Padding** como [true, true,..., true].

Além dos quatro atributos descritos anteriormente, os grupos de normalização de resposta também dão suporte aos seguintes atributos:

+ **Alfa**: (obrigatório) especifica um valor de ponto flutuante que corresponde a `α` na fórmula anterior.
+ **Beta**: (obrigatório) especifica um valor de ponto flutuante que corresponde a `β` na fórmula anterior.
+ **Offset**: (opcional) especifica um valor de ponto flutuante que corresponde a `k` na fórmula anterior. O padrão é 1.

O exemplo a seguir define um pacote de normalização de resposta usando estes atributos:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ A camada de origem inclui cinco mapas, cada um com a dimensão AoF de 12x12, totalizando em 1440 nós.
+ O valor de **KernelShape** indica que se trata de uma camada de normalização de mapa, em que a vizinhança é um retângulo de 3x3.
+ O valor padrão de **Padding** é false, portanto, a camada de destino tem apenas 10 nós em cada dimensão. Para incluir um nó na camada de destino que corresponde a cada nó na camada de origem, adicione Padding = [true, true, true]; e altere o tamanho de RN1 para [5, 12, 12].

## <a name="share-declaration"></a>Declaração de compartilhamento

O net # opcionalmente dá suporte à definição de vários pacotes com pesos compartilhados. Os pesos de quaisquer dois pacotes podem ser compartilhados se suas estruturas forem as mesmas. A sintaxe a seguir define os pacotes com pesos compartilhados:

```Net#
share-declaration:
  share    {    layer-list    }
  share    {    bundle-list    }
  share    {    bias-list    }

  layer-list:
    layer-name    ,    layer-name
    layer-list    ,    layer-name

  bundle-list:
    bundle-spec    ,    bundle-spec
    bundle-list    ,    bundle-spec

  bundle-spec:
    layer-name    =>     layer-name

  bias-list:
    bias-spec    ,    bias-spec
    bias-list    ,    bias-spec

  bias-spec:
    1    =>    layer-name

  layer-name:
    identifier
```

Por exemplo, a seguinte declaração de compartilhamento especifica os nomes de camada, indicando que os pesos e as tendências devem ser compartilhados:

```Net#
Const {
  InputSize = 37;
  HiddenSize = 50;
  }
input {
  Data1 [InputSize];
  Data2 [InputSize];
  }
hidden {
  H1 [HiddenSize] from Data1 all;
  H2 [HiddenSize] from Data2 all;
  }
output Result [2] {
  from H1 all;
  from H2 all;
  }
share { H1, H2 } // share both weights and biases
```

+ Os recursos de entrada são particionados em duas camadas de entrada de tamanho igual.
+ As camadas ocultas computam recursos de nível superior nas duas camadas de entrada.
+ A declaração de compartilhamento especifica que *H1* e *H2* devem ser computados da mesma maneira de suas respectivas entradas.

Como alternativa, isso pode ser especificado com duas declarações de compartilhamento separadas da seguinte maneira:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

Você pode usar a forma abreviada somente quando as camadas contiverem um único pacote. Em geral, o compartilhamento só é possível quando a estrutura relevante é idêntica, o que significa que eles têm o mesmo tamanho, a mesma geometria de convolução e assim por diante.

## <a name="examples-of-net-usage"></a>Exemplos de uso de NET #

Esta seção fornece alguns exemplos de como você pode usar net # para adicionar camadas ocultas, definir a maneira como as camadas ocultas interagem com outras camadas e criar redes de convolução.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definir uma rede neural personalizada simples: exemplo "Olá, Mundo"

Este exemplo simples demonstra como criar um modelo de rede neural que tenha uma única camada oculta.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

O exemplo ilustra alguns comandos básicos da seguinte maneira:

+ A primeira linha define a camada de entrada (chamada `Data`). Quando você usa a palavra-chave `auto`, a rede neural inclui automaticamente todas as colunas de recursos nos exemplos de entrada.
+ A segunda linha cria a camada oculta. O nome `H` é atribuído à camada oculta, que tem 200 nós. Essa camada está totalmente conectada à camada de entrada.
+ A terceira linha define a camada de saída (chamada `Out`), que contém 10 nós de saída. Se a rede neural for usada para classificação, haverá um nó de saída por classe. A palavra-chave **sigmoide** indica que a função de saída é aplicada à camada de saída.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definir várias camadas ocultas: exemplo de visão computacional

O exemplo a seguir demonstra como definir uma rede neural um pouco mais complexa, com várias camadas ocultas personalizadas.

```Net#
// Define the input layers
input Pixels [10, 20];
input MetaData [7];

// Define the first two hidden layers, using data only from the Pixels input
hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
hidden Gather [100]
{
from ByRow all;
from ByCol all;
}

// Define the output layer and its sources
output Result [10]
{
from Gather all;
from MetaData all;
}
```

Este exemplo ilustra vários recursos da linguagem de especificação de redes neurais:

+ A estrutura tem duas camadas de entrada, `Pixels` e `MetaData`.
+ A camada de `Pixels` é uma camada de origem para dois grupos de conexão, com camadas de destino, `ByRow` e `ByCol`.
+ As camadas `Gather` e `Result` são camadas de destino em vários grupos de conexões.
+ A camada de saída, `Result`, é uma camada de destino em dois grupos de conexão; uma com a camada oculta de segundo nível `Gather` como uma camada de destino e a outra com a camada de entrada `MetaData` como uma camada de destino.
+ As camadas ocultas, `ByRow` e `ByCol`, especificam a conectividade filtrada usando expressões de predicado. Mais precisamente, o nó em `ByRow` em [x, y] é conectado aos nós em `Pixels` que têm a primeira coordenada de índice igual à primeira coordenada do nó, x. Da mesma forma, o nó em `ByCol` em [x, y] é conectado aos nós em `Pixels` que têm a segunda coordenada de índice dentro de uma da segunda coordenada do nó, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definir uma rede de convolução para classificação multiclasse: exemplo de reconhecimento de dígitos

A definição da rede a seguir foi projetada para reconhecer números e ilustra algumas técnicas avançadas para personalizar uma rede neural.

```Net#
input Image [29, 29];
hidden Conv1 [5, 13, 13] from Image convolve
  {
  InputShape  = [29, 29];
  KernelShape = [ 5,  5];
  Stride      = [ 2,  2];
  MapCount    = 5;
  }
hidden Conv2 [50, 5, 5]
from Conv1 convolve
  {
  InputShape  = [ 5, 13, 13];
  KernelShape = [ 1,  5,  5];
  Stride      = [ 1,  2,  2];
  Sharing     = [false, true, true];
  MapCount    = 10;
  }
hidden Hid3 [100] from Conv2 all;
output Digit [10] from Hid3 all;
```

+ A estrutura tem uma única camada de entrada, `Image`.
+ A palavra-chave `convolve` indica que as camadas nomeadas `Conv1` e `Conv2` são camadas de convolução. Cada uma dessas declarações de camada é seguida por uma lista dos atributos de convolução.
+ A rede tem uma terceira camada oculta, `Hid3`, que está totalmente conectada à segunda camada oculta, `Conv2`.
+ A camada de saída, `Digit`, é conectada somente à terceira camada oculta, `Hid3`. A palavra-chave `all` indica que a camada de saída está totalmente conectada a `Hid3`.
+ A arity da convolução é três: o comprimento das tuplas `InputShape`, `KernelShape`, `Stride`e `Sharing`.
+ O número de pesos por kernel é `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`. Ou `26 * 50 = 1300`.
+ Você pode calcular os nós em cada camada oculta da seguinte maneira:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ O número total de nós pode ser calculado usando a dimensionalidade declarada da camada, [50, 5, 5], da seguinte maneira: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Como `Sharing[d]` é falso somente para `d == 0`, o número de kernels é `MapCount * NodeCount\[0] = 10 * 5 = 50`.

## <a name="acknowledgements"></a>Confirmações

A linguagem net # para personalizar a arquitetura de redes neurais foi desenvolvida na Microsoft por Shon Katzenberger (arquiteto, Machine Learning) e Alexey Kamenev (engenheiro de software, Microsoft Research). Ele é usado internamente para projetos e aplicativos de aprendizado de máquina, desde detecção de imagem até análise de texto. Para obter mais informações, consulte [redes neurais no Azure Machine Learning Studio-introdução ao net #](https://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
