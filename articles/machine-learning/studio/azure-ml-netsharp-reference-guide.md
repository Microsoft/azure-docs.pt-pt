---
title: Redes neuronais personalizadas Net#
titleSuffix: ML Studio (classic) - Azure
description: Guia de sintaxe para a linguagem de especificação das redes neurais Net# Saiba como criar modelos de rede neural personalizados no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: c1912e670a9cf1c178b58cefbd33171f15be2483
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79218255"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning-studio-classic"></a>Guia para a linguagem de especificação da rede neural Net# para o Estúdio de Aprendizagem automática Azure (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Net# é uma linguagem desenvolvida pela Microsoft que é usada para definir arquiteturas complexas da rede neural, tais como redes neuronais profundas ou convoluções de dimensões arbitrárias. Pode utilizar estruturas complexas para melhorar a aprendizagem de dados como imagem, vídeo ou áudio.

Pode utilizar uma especificação de arquitetura Net# nestes contextos:

+ Todos os módulos de rede neural no Microsoft Azure Machine Learning Studio (clássico): [Rede Neural Multiclasse,](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network) [Rede Neural de Duas Classes](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network)e [Regressão](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression) da Rede Neural
+ Funções de rede neural no Microsoft ML Server: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) e [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)para o idioma R e [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) para Python.


Este artigo descreve os conceitos básicos e a sintaxe necessárias para desenvolver uma rede neural personalizada usando net#:

+ Requisitos da rede neural e como definir os componentes primários
+ A sintaxe e as palavras-chave da linguagem de especificação Net#
+ Exemplos de redes neurais personalizadas criadas usando a Net #



## <a name="neural-network-basics"></a>Básicos da rede neural

Uma estrutura de rede neural consiste em nós organizados em camadas, e ligações ponderadas (ou bordas) entre os nós. As ligações são direcionais, e cada ligação tem um nó de origem e um nó de destino.

Cada camada treinável (uma camada escondida ou de saída) tem um ou mais feixes de **ligação**. Um feixe de ligação consiste numa camada de origem e numa especificação das ligações a partir dessa camada de origem. Todas as ligações em um dado pacote partilham fonte e camadas de destino. Em Net#, um pacote de ligação é considerado como pertencente à camada de destino do pacote.

Net# suporta vários tipos de pacotes de conexão, que permitem personalizar a forma como as inputs são mapeadas para camadas escondidas e mapeadas para as saídas.

O pacote padrão ou padrão é um **pacote completo,** no qual cada nó na camada de origem está ligado a cada nó da camada de destino.

Adicionalmente, a Net# suporta os seguintes quatro tipos de pacotes avançados de ligação:

+ **Pacotes filtrados.** Pode definir um predicado utilizando as localizações do nó de camada de origem e o nó de camada de destino. Os nódosos estão ligados sempre que o predicado é verdadeiro.

+ **Pacotes convolucionais.** Você pode definir pequenos bairros de nósna camada de origem. Cada nó na camada de destino está ligado a um bairro de nós na camada de origem.

+ **Conjuntos** de pacotes e **pacotes de normalização**de resposta. Estes são semelhantes a pacotes convolucionais na medida em que o utilizador define pequenos bairros de nó na camada de origem. A diferença é que os pesos das bordas nestes pacotes não são treináveis. Em vez disso, aplica-se uma função predefinida aos valores do nó de origem para determinar o valor do nó de destino.


## <a name="supported-customizations"></a>Supported customizations (Personalizações avançadas)

A arquitetura dos modelos de rede neural que cria no Azure Machine Learning Studio (clássico) pode ser extensivamente personalizada usando o Net#. Pode:

+ Crie camadas escondidas e controle o número de nós em cada camada.
+ Especifique como as camadas devem ser ligadas umas às outras.
+ Defina estruturas especiais de conectividade, tais como convoluções e pacotes de partilha de peso.
+ Especifique diferentes funções de ativação.

Para mais detalhes sobre a sintaxe da linguagem especificada, consulte a [Especificação](#structure-specifications)da Estrutura .

Por exemplo, a definição de redes neurais para algumas tarefas comuns de aprendizagem automática, do simplex ao complexo, ver [Exemplos](#examples-of-net-usage).

## <a name="general-requirements"></a>Requisitos gerais

+ Deve haver exatamente uma camada de saída, pelo menos uma camada de entrada, e camadas nulas de zero ou mais.
+ Cada camada tem um número fixo de nós, conceptualmente dispostos numa matriz retangular de dimensões arbitrárias.
+ As camadas de entrada não têm parâmetros treinados associados e representam o ponto em que os dados de instância entram na rede.
+ Camadas treináveis (as camadas ocultas e de saída) têm parâmetros treinados associados, conhecidos como pesos e preconceitos.
+ Os nódosos de origem e destino devem estar em camadas separadas.
+ As ligações devem ser acíclicas; por outras palavras, não pode haver uma cadeia de ligações que levem ao nó de origem inicial.
+ A camada de saída não pode ser uma camada fonte de um feixe de ligação.

## <a name="structure-specifications"></a>Especificações estruturais

Uma especificação da estrutura da rede neural é composta por três secções: a **declaração constante,** a **declaração de camadas,** a declaração **de ligação.** Há também uma secção de declaração de **ações** opcional. As secções podem ser especificadas em qualquer ordem.

## <a name="constant-declaration"></a>Declaração constante

Uma declaração constante é opcional. Fornece um meio para definir valores utilizados em outros lugares na definição da rede neural. A declaração consiste num identificador seguido de um sinal igual e de uma expressão de valor.

Por exemplo, a seguinte afirmação define uma constante: `x`

`Const X = 28;`

Para definir duas ou mais constantes simultaneamente, encerre os nomes e valores identificadores em aparelhos e separe-os utilizando pontos evículos. Por exemplo:

`Const { X = 28; Y = 4; }`

O lado direito de cada expressão de atribuição pode ser um inteiro, um número real, um valor booleano (verdadeiro ou falso), ou uma expressão matemática. Por exemplo:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Declaração de camada

A declaração da camada é necessária. Define o tamanho e a fonte da camada, incluindo os seus feixes de ligação e atributos. A declaração começa com o nome da camada (entrada, oculta ou saída), seguida das dimensões da camada (uma túnica de inteiros positivos). Por exemplo:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ O produto das dimensões é o número de nós na camada. Neste exemplo, existem duas dimensões [5,20], o que significa que há 100 nós na camada.
+ As camadas podem ser declaradas em qualquer ordem, com uma exceção: Se for definida mais de uma camada de entrada, a ordem em que são declaradas deve corresponder à ordem das características nos dados de entrada.

Para especificar que o número de nós numa camada é `auto` determinado automaticamente, utilize a palavra-chave. A `auto` palavra-chave tem efeitos diferentes, dependendo da camada:

+ Numa declaração de camada de entrada, o número de nós é o número de funcionalidades nos dados de entrada.
+ Numa declaração de camada escondida, o número de nós é o número especificado pelo valor do parâmetro para **o número de nós escondidos**.
+ Numa declaração de camada de saída, o número de nós é 2 para classificação de duas classes, 1 para regressão, e igual ao número de nós de saída para classificação multiclasse.

Por exemplo, a seguinte definição de rede permite determinar automaticamente o tamanho de todas as camadas:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

Uma declaração de camada para uma camada treinável (as camadas ocultas ou de saída) pode incluir opcionalmente a função de saída (também chamada de função de ativação), que se incorre em **sigmoid** para modelos de classificação, e **linear** para modelos de regressão. Mesmo que utilize o padrão, pode explicitamente indicar a função de ativação, se desejar para clareza.

As seguintes funções de saída são suportadas:

+ sigmoide
+ linear
+ softmax
+ rlinear
+ quadrado
+ sqrt
+ srlinear
+ abdominais
+ tanh
+ brlinear

Por exemplo, a seguinte declaração utiliza a função **softmax:**

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Declaração de ligação

Imediatamente após a definição da camada treinável, deve declarar ligações entre as camadas que definiu. A declaração do pacote de `from`ligação começa com a palavra-chave, seguida pelo nome da camada de origem do pacote e pelo tipo de pacote de ligação para criar.

Atualmente, cinco tipos de pacotes de ligação são suportados:

+ **Pacotes completos,** indicados pela palavra-chave`all`
+ Pacotes **filtrados,** indicados `where`pela palavra-chave, seguidos de uma expressão predicada
+ Pacotes **convolucionais,** indicados `convolve`pela palavra-chave, seguidos pelos atributos de convolução
+ **Pacotes de agrupamento,** indicados pelas palavras-chave **max pool** ou **piscina média**
+ **Pacotes** de normalização de resposta, indicados pela norma de **resposta** da palavra-chave

## <a name="full-bundles"></a>Pacotes completos

Um pacote completo de ligação inclui uma ligação de cada nó na camada de origem a cada nó na camada de destino. Este é o tipo de ligação de rede padrão.

## <a name="filtered-bundles"></a>Pacotes filtrados

Uma especificação de feixe de ligação filtrada inclui um predicado, expresso sintáticamente, muito parecido com uma expressão c# lambda. O exemplo que se segue define dois pacotes filtrados:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ No predicado `ByRow` `s` para, é um parâmetro que representa um índice na matriz retangular `Pixels`de `d` nós da camada de entrada, e é um parâmetro `ByRow`que representa um índice na matriz de nós da camada escondida, . O tipo `s` de `d` ambos e é uma túnica de inteiros de comprimento dois. Conceptualmente, `s` varia sobre todos os pares de `0 <= s[0] < 10` `0 <= s[1] < 20`inteiros `d` com e, e varia sobre todos `0 <= d[0] < 10` `0 <= d[1] < 12`os pares de inteiros, com e .

+ No lado direito da expressão predicada, há uma condição. Neste exemplo, por cada `s` `d` valor e de tal forma que a condição é verdadeira, há uma borda desde o nó da camada de origem até ao nó da camada de destino. Assim, esta expressão do filtro indica que o feixe `s` inclui uma ligação do nó definido pelo nó definido por `d` em todos os casos em que o s[0] é igual a d[0].

Opcionalmente, pode especificar um conjunto de pesos para um pacote filtrado. O valor para o atributo dos **Pesos** deve ser uma tuple de valores de ponto flutuante com um comprimento que corresponda ao número de ligações definidas pelo pacote. Por padrão, os pesos são gerados aleatoriamente.

Os valores de peso são agrupados pelo índice de nó de destino. Ou seja, se o nó de destino está ligado aos `K` nós de origem K, os primeiros elementos da tuple **Weights** são os pesos para o primeiro nó de destino, em ordem de índice fonte. O mesmo se aplica aos restantes nódosos de destino.

É possível especificar os pesos diretamente como valores constantes. Por exemplo, se tiver aprendido os pesos anteriormente, pode especificá-los como constantes usando esta sintaxe:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Pacotes convolucionais

Quando os dados de formação têm uma estrutura homogénea, as ligações convolucionais são comumente usadas para aprender características de alto nível dos dados. Por exemplo, em dados de imagem, áudio ou vídeo, a dimensionalidade espacial ou temporal pode ser bastante uniforme.

Os feixes de convolução empregam **núcleos** retangulares que são deslizados através das dimensões. Essencialmente, cada núcleo define um conjunto de pesos aplicados nos bairros locais, referidos como **aplicações de kernel.** Cada aplicação de núcleo corresponde a um nó na camada de origem, que é referida como nó **central**. Os pesos de um núcleo são partilhados entre muitas conexões. Num feixe convolucional, cada núcleo é retangular e todas as aplicações de kernel têm o mesmo tamanho.

Os feixes de convolução suportam os seguintes atributos:

**A InputShape** define a dimensionalidade da camada de origem para efeitos deste feixe convolucional. O valor deve ser uma tuuma de inteiros positivos. O produto dos inteiros deve igualar o número de nós na camada de origem, mas, caso contrário, não precisa de corresponder à dimensionalidade declarada para a camada de origem. O comprimento desta tuple torna-se o valor **ardade** para o feixe convolucional. Tipicamente arity refere-se ao número de argumentos ou operands que uma função pode tomar.

Para definir a forma e os locais dos núcleos, utilize os atributos **KernelShape,** **Stride,** **Acolchoamento,** **LowerPad**e **UpperPad:**

+ **KernelShape**: (necessário) Define a dimensionalidade de cada núcleo para o feixe convolucional. O valor deve ser uma tuuma de inteiros positivos com um comprimento que seja igual à aridade do pacote. Cada componente desta tuple não deve ser superior ao componente correspondente do **InputShape**.

+ **Passo**: (opcional) Define os tamanhos de passo deslizantes da convolução (tamanho de um passo para cada dimensão), ou seja, a distância entre os nós centrais. O valor deve ser uma tuuma de inteiros positivos com um comprimento que seja a aridade do pacote. Cada componente desta tuple não deve ser superior ao componente correspondente da **KernelShape**. O valor padrão é uma tuple com todos os componentes iguais a um.

+ **Partilha**: (opcional) Define a partilha de peso para cada dimensão da convolução. O valor pode ser um único valor booleano ou uma tuple de valores booleanos com um comprimento que é a aridade do pacote. Um único valor Boolean é estendido para ser uma tuple do comprimento correto com todos os componentes iguais ao valor especificado. O valor padrão é uma tuple que consiste em todos os valores verdadeiros.

+ **MapCount**: (opcional) Define o número de mapas de funcionalidades para o pacote convolucional. O valor pode ser um único inteiro positivo ou uma tuple de inteiros positivos com um comprimento que é a aridade do pacote. Um único valor inteiro é estendido para ser uma tuple do comprimento correto com os primeiros componentes iguais ao valor especificado e todos os componentes restantes iguais a um. O valor padrão é um. O número total de mapas de recursos é o produto dos componentes da tuple. A factoragem deste número total através dos componentes determina como os valores do mapa de características estão agrupados nos nós de destino.

+ **Pesos**: (opcional) Define os pesos iniciais para o pacote. O valor deve ser uma tuuma de valores de ponto flutuante com um comprimento que seja o número de núcleos vezes o número de pesos por núcleo, tal como definido mais tarde neste artigo. Os pesos predefinidos são gerados aleatoriamente.

Existem dois conjuntos de propriedades que controlam o enchimento, sendo as propriedades mutuamente exclusivas:

+ **Enchimento**: (opcional) Determina se a entrada deve ser acolchoada utilizando um regime de **enchimento predefinido**. O valor pode ser um único valor booleano, ou pode ser uma tuple de valores booleanos com um comprimento que é a aridade do pacote.

    Um único valor Boolean é estendido para ser uma tuple do comprimento correto com todos os componentes iguais ao valor especificado.

    Se o valor para uma dimensão for verdadeiro, a fonte é logicamente acolchoada nessa dimensão com células de valor zero para suportar aplicações adicionais de núcleo, de modo que os nós centrais do primeiro e último núcleo nessa dimensão são os primeiros e últimos nós nessa dimensão na camada. Assim, o número de nós "bonecos" em cada dimensão é `(InputShape[d] - 1) / Stride[d] + 1` determinado automaticamente, para encaixar exatamente os núcleos na camada de origem acolchoada.

    Se o valor para uma dimensão for Falso, os núcleos são definidos de modo a que o número de nós de cada lado que ficam de fora seja o mesmo (até uma diferença de 1). O valor padrão deste atributo é um tuple com todos os componentes iguais ao Falso.

+ **UpperPad** e **LowerPad**: (opcional) Fornecer um maior controlo sobre a quantidade de estofamento a utilizar. **Importante:** Estes atributos podem ser definidos se e apenas se a propriedade **de Acolchoamento** acima ***não*** for definida. Os valores devem ser tuples de valor inteiro com comprimentos que são a aridade do pacote. Quando estes atributos são especificados, os nós "bonecos" são adicionados às extremidades inferiores e superiores de cada dimensão da camada de entrada. O número de nós adicionados às extremidades inferiores e superiores em cada dimensão é determinado por **LowerPad**[i] e **UpperPad**[i] respectivamente.

    Para garantir que os grãos correspondam apenas a nós "reais" e não a nós "bonecos", devem ser satisfeitas as seguintes condições:
  - Cada componente do **LowerPad** deve `KernelShape[d]/2`ser estritamente inferior a .
  - Cada componente do **UpperPad** não `KernelShape[d]/2`deve ser maior do que .
  - O valor padrão destes atributos é um tuple com todos os componentes iguais a 0.

    A definição **Do acolchoamento** = verdadeiro permite o acolchoamento que for necessário para manter o "centro" do núcleo dentro da entrada "real". Isto muda um pouco a matemática para calcular o tamanho da saída. Geralmente, o tamanho *de* saída `D = (I - K) / S + 1`D `I` é calculado como, onde é o tamanho da entrada, `K` é o tamanho do núcleo, `S` é o passo, e `/` é divisão de inteiro (redonda para zero). Se definir o UpperPad = [1, 1], o tamanho `I` da `D = (29 - 5) / 2 + 1 = 13`entrada é efetivamente 29, e assim . No entanto, quando **o acolchoado** = verdadeiro, essencialmente `I` é atropelado por; `K - 1` daí. `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14` Ao especificar valores para **O Pad superior** e **LowerPad** obtém-se muito mais controlo sobre o acolchoado do que se apenas definir **o acolchoamento** = verdadeiro.

Para obter mais informações sobre redes convolucionais e suas aplicações, consulte estes artigos:

+ [http://deeplearning.net/tutorial/lenet.html](http://deeplearning.net/tutorial/lenet.html)
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Pacotes de agrupamento

Um conjunto de **agrupamentos** aplica geometria semelhante à conectividade convolucional, mas usa funções predefinidas para obter valores de nó para obter o valor do nó de destino. Assim, os pacotes de agrupamento não têm estado treinável (pesos ou enviesamentos). Os pacotes de agrupamento suportam todos os atributos convolucionais exceto **A Partilha,** **MapCount**e **Weights**.

Tipicamente, os núcleos resumidos por unidades de agrupamento adjacentes não se sobrepõem. Se stride[d] for igual a KernelShape[d] em cada dimensão, a camada obtida é a camada tradicional de agrupamento local, que é geralmente utilizada em redes neuronais convolucionais. Cada nó de destino calcula o máximo ou a média das atividades do seu núcleo na camada de origem.

O exemplo que se segue ilustra um pacote de agrupamentos:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ A aridade do pacote é 3: isto é, `InputShape`o `KernelShape`comprimento `Stride`dos tupículos, e .
+ O número de nós na camada `5 * 24 * 24 = 2880`de origem é .
+ Esta é uma camada tradicional de piscina local porque **KernelShape** e **Stride** são iguais.
+ O número de nós na camada `5 * 12 * 12 = 1440`de destino é .

Para obter mais informações sobre camadas de agrupamento, consulte estes artigos:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf)(Secção 3.4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Pacotes de normalização de resposta

**A normalização** da resposta é um esquema de normalização local que foi introduzido pela primeira vez por Geoffrey Hinton, et al, no artigo [ImageNet Classification with Deep Convolutional Neural Networks](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

A normalização da resposta é utilizada para ajudar à generalização das redes neurais. Quando um neurónio dispara a um nível de ativação muito elevado, uma camada de normalização de resposta local suprime o nível de ativação dos neurónios circundantes. Isto é feito utilizando três`α` `β`parâmetros `k`( e ) e uma estrutura convolucional (ou forma de bairro). Cada neurónio na camada de destino **y** corresponde a um neurónio **x** na camada de origem. O nível **y** de ativação de y `f` é dado pela seguinte `Nx` fórmula, onde está o nível de ativação de um neurónio, e é o núcleo (ou o conjunto que contém os neurónios na vizinhança de **x),** como definido pela seguinte estrutura convolucional:

![fórmula para estrutura convolucional](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Os pacotes de normalização da resposta suportam todos os atributos convolucionais, exceto **partilha,** **MapCount,** e **Pesos.**

+ Se o núcleo contiver neurónios no mesmo mapa que ***x,*** o esquema de normalização é referido como **a normalização do mesmo mapa.** Para definir a normalização do mesmo mapa, a primeira coordenada em **InputShape** deve ter o valor 1.

+ Se o núcleo contiver neurónios na mesma posição espacial que ***x,*** mas os neurónios estão noutros mapas, o esquema de normalização é chamado através de **mapas de normalização.** Este tipo de normalização de resposta implementa uma forma de inibição lateral inspirada no tipo encontrado em neurónios reais, criando concorrência para grandes níveis de ativação entre as saídas de neurónios computadas em diferentes mapas. Para definir através da normalização dos mapas, a primeira coordenada deve ser um inteiro maior do que um e não maior do que o número de mapas, e o resto das coordenadas deve ter o valor 1.

Como os feixes de normalização da resposta aplicam uma função predefinida para obter valores do nó para determinar o valor do nó de destino, eles não têm estado treinável (pesos ou enviesamentos).

> [!NOTE]
> Os nós na camada de destino correspondem a neurónios que são os nós centrais dos núcleos. Por exemplo, `KernelShape[d]` se for `KernelShape[d]/2` estranho, então corresponde ao nó de kernel central. Se `KernelShape[d]` for mesmo, o nó `KernelShape[d]/2 - 1`central está em . Portanto, se `Padding[d]` for Falso, o `KernelShape[d]/2` primeiro e o último nós não têm nódosos correspondentes na camada de destino. Para evitar esta situação, defina **o acolchoado** como [verdadeiro, verdadeiro, ..., verdadeiro].

Além dos quatro atributos descritos anteriormente, os pacotes de normalização da resposta também suportam os seguintes atributos:

+ **Alfa**: (obrigatório) Especifica um valor de ponto `α` flutuante que corresponda à fórmula anterior.
+ **Beta**: (obrigatório) Especifica um valor de ponto `β` flutuante que corresponde à fórmula anterior.
+ **Offset**: (opcional) Especifica um valor de `k` ponto flutuante que corresponde à fórmula anterior. Falha para 1.

O exemplo seguinte define um pacote de normalização de resposta utilizando estes atributos:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ A camada de origem inclui cinco mapas, cada um com dimensão aof de 12x12, totalizando 1440 nós.
+ O valor da **KernelShape** indica que esta é uma mesma camada de normalização do mapa, onde o bairro é um retângulo 3x3.
+ O valor padrão do **Acolchoado** é Falso, pelo que a camada de destino tem apenas 10 nós em cada dimensão. Para incluir um nó na camada de destino que corresponde a cada nó na camada de origem, adicione Acolchoamento = [verdadeiro, verdadeiro, verdadeiro]; e alterar o tamanho de RN1 para [5, 12, 12].

## <a name="share-declaration"></a>Declaração de partilha

Net# suporta opcionalmente a definição de múltiplos pacotes com pesos partilhados. Os pesos de dois pacotes podem ser partilhados se as suas estruturas forem iguais. A seguinte sintaxe define pacotes com pesos partilhados:

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

Por exemplo, a seguinte declaração de partilha especifica os nomes da camada, indicando que tanto os pesos como os enviesamentos devem ser partilhados:

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

+ As características de entrada são divididas em duas camadas de entrada de tamanho igual.
+ As camadas ocultas calculam então características de nível mais elevado nas duas camadas de entrada.
+ A declaração de ações especifica que *h1* e *H2* devem ser calculados da mesma forma a partir dos respetivos contributos.

Em alternativa, esta poderia ser especificada com duas declarações de ações separadas da seguinte forma:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

Só é possível utilizar o formulário curto quando as camadas contiverem um único pacote. Em geral, a partilha só é possível quando a estrutura relevante é idêntica, o que significa que têm o mesmo tamanho, a mesma geometria convolucional, e assim por diante.

## <a name="examples-of-net-usage"></a>Exemplos de utilização da Net#

Esta secção fornece alguns exemplos de como você pode usar Net# para adicionar camadas escondidas, definir a forma como camadas escondidas interagem com outras camadas, e construir redes convolucionais.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Defina uma simples rede neural personalizada: exemplo "Hello World"

Este exemplo simples demonstra como criar um modelo de rede neural que tem uma única camada escondida.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

O exemplo ilustra alguns comandos básicos da seguinte forma:

+ A primeira linha define a camada `Data`de entrada (nomeada). Quando utiliza `auto` a palavra-chave, a rede neural inclui automaticamente todas as colunas de características nos exemplos de entrada.
+ A segunda linha cria a camada escondida. O `H` nome é atribuído à camada escondida, que tem 200 nós. Esta camada está totalmente ligada à camada de entrada.
+ A terceira linha define a `Out`camada de saída (nomeada), que contém 10 nóóis de saída. Se a rede neural for utilizada para a classificação, existe um nó de saída por classe. O **sigmoid** da palavra-chave indica que a função de saída é aplicada à camada de saída.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Defina várias camadas ocultas: exemplo de visão computacional

O exemplo que se segue demonstra como definir uma rede neural um pouco mais complexa, com múltiplas camadas ocultas personalizadas.

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

Este exemplo ilustra várias características da linguagem de especificação das redes neurais:

+ A estrutura tem duas `Pixels` camadas de entrada, e `MetaData`.
+ A `Pixels` camada é uma camada de origem para `ByRow` dois `ByCol`feixes de conexão, com camadas de destino, e .
+ As `Gather` camadas e `Result` são camadas de destino em vários feixes de conexão.
+ A camada `Result`de saída, é uma camada de destino em dois feixes de conexão; um com a camada `Gather` oculta do segundo nível como camada `MetaData` de destino, e o outro com a camada de entrada como camada de destino.
+ As camadas `ByRow` `ByCol`escondidas e, especifique a conectividade filtrada utilizando expressões predicadas. Mais precisamente, o `ByRow` nó em [x, y] está ligado `Pixels` aos nós em que têm a primeira coordenada de índice igual à primeira coordenada do nó, x. Da mesma forma, `ByCol` o nó em [x, y] está `Pixels` ligado aos nós em que têm a segunda coordenada de índice dentro de uma das segundas coordenadas do nó, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Defina uma rede convolucional para classificação multiclasse: exemplo de reconhecimento de dígitos

A definição da seguinte rede é projetada para reconhecer números, e ilustra algumas técnicas avançadas para personalizar uma rede neural.

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

+ A estrutura tem uma única `Image`camada de entrada, .
+ A palavra-chave `convolve` indica `Conv1` que `Conv2` as camadas nomeadas e são camadas convolucionais. Cada uma destas declarações de camadas é seguida por uma lista dos atributos de convolução.
+ A rede tem uma `Hid3`terceira camada escondida, que está `Conv2`totalmente ligada à segunda camada escondida, .
+ A camada `Digit`de saída, está ligada apenas à terceira camada escondida, `Hid3`. A palavra-chave `all` indica que a `Hid3`camada de saída está totalmente ligada a .
+ A aridade da convolução é três: o `InputShape`comprimento `KernelShape` `Stride`dos `Sharing`tupículos, , e .
+ O número de pesos por `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`núcleo é . Ou. `26 * 50 = 1300`
+ Pode calcular os nós em cada camada escondida da seguinte forma:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ O número total de nós pode ser calculado utilizando a dimensionalidade declarada da camada, [50, 5, 5], da seguinte forma:`MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Porque `Sharing[d]` é falso `d == 0`apenas para, o `MapCount * NodeCount\[0] = 10 * 5 = 50`número de núcleos é .

## <a name="acknowledgements"></a>Agradecimentos

A linguagem Net# para personalizar a arquitetura das redes neurais foi desenvolvida na Microsoft por Shon Katzenberger (Arquiteto, Machine Learning) e Alexey Kamenev (Engenheiro de Software, Microsoft Research). É utilizado internamente para projetos de machine learning e aplicações que vão desde a deteção de imagem até à análise de texto. Para mais informações, consulte [Redes Neurais no estúdio Azure Machine Learning - Introdução à Net#](https://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
