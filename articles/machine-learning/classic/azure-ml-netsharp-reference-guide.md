---
title: 'ML Studio (clássico): Redes neurais personalizadas Net # - Azure'
description: Guia de sintaxe para o idioma de especificação de redes neurais Net#. Aprenda a criar modelos de rede neural personalizados no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: a36eb21f681aec1cfc52a000b60bdbc30cab0633
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302797"
---
# <a name="guide-to-net-neural-network-specification-language-for-machine-learning-studio-classic"></a>Guia para net# linguagem de especificação de rede neural para Machine Learning Studio (clássico)

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Net# é uma linguagem desenvolvida pela Microsoft que é usada para definir arquiteturas complexas de rede neural, tais como redes neuronais profundas ou convoluções de dimensões arbitrárias. Pode utilizar estruturas complexas para melhorar a aprendizagem de dados como imagem, vídeo ou áudio.

Pode utilizar uma especificação de arquitetura Net# nestes contextos:

+ Todos os módulos de rede neural no Microsoft Azure Machine Learning Studio (clássico): [Rede Neural Multiclass, Rede](/azure/machine-learning/studio-module-reference/multiclass-neural-network)Neural de [Duas Classes](/azure/machine-learning/studio-module-reference/two-class-neural-network)e [Regressão da Rede Neural](/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Funciona a rede neural no Microsoft ML Server: [NeuralNet](/machine-learning-server/r-reference/microsoftml/neuralnet) e [rxNeuralNet](/machine-learning-server/r-reference/microsoftml/rxneuralnet)para o idioma R, e [rx_neural_network](/machine-learning-server/python-reference/microsoftml/rx-neural-network) para Python.


Este artigo descreve os conceitos básicos e a sintaxe necessária para desenvolver uma rede neural personalizada usando Net#:

+ Requisitos de rede neural e como definir os componentes primários
+ A sintaxe e as palavras-chave do idioma de especificação Net#
+ Exemplos de redes neuronais personalizadas criadas através da Net #



## <a name="neural-network-basics"></a>Básicos da rede neural

Uma estrutura de rede neural consiste em nós que são organizados em camadas, e ligações ponderadas (ou bordas) entre os nós. As ligações são direcionais, e cada ligação tem um nó de origem e um nó de destino.

Cada camada treinável (uma camada oculta ou uma camada de saída) tem um ou mais **pacotes de conexão**. Um pacote de conexão consiste numa camada de origem e uma especificação das ligações dessa camada de origem. Todas as ligações em um determinado pacote partilham fonte e camadas de destino. Em Net#, um pacote de conexão é considerado como pertencente à camada de destino do pacote.

Net# suporta vários tipos de pacotes de conexão, que permitem personalizar a forma como as entradas são mapeadas para camadas ocultas e mapeadas para as saídas.

O pacote padrão ou padrão é um **pacote completo,** no qual cada nó na camada de origem está ligado a cada nó na camada de destino.

Além disso, a Net# suporta os seguintes quatro tipos de pacotes avançados de conexão:

+ **Pacotes filtrados.** Pode definir um predicado utilizando as localizações do nó da camada de origem e do nó da camada de destino. Os nós são ligados sempre que o predicado é verdadeiro.

+ **Pacotes convolucionais.** Você pode definir pequenos bairros de nós na camada de origem. Cada nó na camada de destino está ligado a um bairro de nós na camada de origem.

+ **Pacotes de agrupamentos** e **pacotes de normalização de resposta**. Estes são semelhantes aos pacotes convolucionales na medida em que o utilizador define pequenos bairros de nós na camada de origem. A diferença é que os pesos das bordas nestes pacotes não são treináveis. Em vez disso, uma função predefinida é aplicada aos valores do nó de origem para determinar o valor do nó de destino.


## <a name="supported-customizations"></a>Supported customizations (Personalizações avançadas)

A arquitetura dos modelos de rede neural que cria no Azure Machine Learning Studio (clássico) pode ser extensivamente personalizada usando net#. Pode:

+ Crie camadas ocultas e controle o número de nós em cada camada.
+ Especifique como as camadas devem ser ligadas entre si.
+ Defina estruturas de conectividade especiais, tais como convoluções e pacotes de partilha de peso.
+ Especificar diferentes funções de ativação.

Para obter mais informações sobre a sintaxe do linguístico da especificação, consulte [a Especificação da Estrutura](#structure-specifications).

Por exemplo, a definição de redes neurais para algumas tarefas comuns de aprendizagem automática, do simplex ao complexo, ver [Exemplos](#examples-of-net-usage).

## <a name="general-requirements"></a>Requisitos gerais

+ Deve haver exatamente uma camada de saída, pelo menos uma camada de entrada, e zero ou mais camadas escondidas.
+ Cada camada tem um número fixo de nós, conceptualmente dispostos numa matriz retangular de dimensões arbitrárias.
+ As camadas de entrada não têm parâmetros treinados associados e representam o ponto em que os dados de caso entram na rede.
+ Camadas treináveis (as camadas ocultas e de saída) têm parâmetros treinados associados, conhecidos como pesos e enviesamentos.
+ Os nós de origem e destino devem estar em camadas separadas.
+ As ligações devem ser acíclicas; por outras palavras, não pode haver uma cadeia de ligações que conduza ao nó de origem inicial.
+ A camada de saída não pode ser uma camada de origem de um pacote de conexão.

## <a name="structure-specifications"></a>Especificações da estrutura

Uma especificação da estrutura da rede neural é composta por três secções: a **declaração constante,** a declaração de **camada,** a declaração de **ligação**. Há também uma secção de **declaração de ações** opcional. As secções podem ser especificadas em qualquer ordem.

## <a name="constant-declaration"></a>Declaração constante

Uma declaração constante é opcional. Fornece um meio para definir valores usados em outros lugares na definição da rede neural. A declaração é constituída por um identificador seguido de um sinal igual e de uma expressão de valor.

Por exemplo, a seguinte declaração define uma `x` constante:

`Const X = 28;`

Para definir duas ou mais constantes simultaneamente, encerra os nomes e valores do identificador em aparelhos e separa-os utilizando pontos-e-vírgulas. Por exemplo:

`Const { X = 28; Y = 4; }`

O lado direito de cada expressão de atribuição pode ser um número inteiro, um número real, um valor booleano (verdadeiro ou falso), ou uma expressão matemática. Por exemplo:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Declaração de camada

A declaração da camada é necessária. Define o tamanho e a fonte da camada, incluindo os seus pacotes de conexão e atributos. A declaração começa com o nome da camada (entrada, oculta ou saída), seguida das dimensões da camada (um tuple de inteiros positivos). Por exemplo:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ O produto das dimensões é o número de nós na camada. Neste exemplo, existem duas dimensões [5,20], o que significa que há 100 nós na camada.
+ As camadas podem ser declaradas por qualquer ordem, com uma exceção: Se for definida mais de uma camada de entrada, a ordem na qual são declaradas deve corresponder à ordem das características nos dados de entrada.

Para especificar que o número de nós numa camada é determinado automaticamente, utilize a `auto` palavra-chave. A `auto` palavra-chave tem efeitos diferentes, dependendo da camada:

+ Numa declaração de camada de entrada, o número de nós é o número de funcionalidades nos dados de entrada.
+ Numa declaração de camada oculta, o número de nós é o número especificado pelo valor do parâmetro para o **Número de nós ocultos**.
+ Numa declaração sobre a camada de saída, o número de nós é de 2 para classificação de duas classes, 1 para regressão e igual ao número de nós de saída para classificação multiclasse.

Por exemplo, a seguinte definição de rede permite determinar automaticamente o tamanho de todas as camadas:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

Uma declaração de camada para uma camada treinável (as camadas ocultas ou de saída) pode opcionalmente incluir a função de saída (também chamada de função de ativação), que predefine o **sigmoid** para os modelos de classificação, e **linear** para modelos de regressão. Mesmo que utilize o padrão, pode indicar explicitamente a função de ativação, se desejar clareza.

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

Imediatamente após definir a camada treinável, deve declarar ligações entre as camadas que definiu. A declaração do pacote de ligação começa com a palavra-chave, `from` seguida do nome da camada de origem do pacote e do tipo de pacote de conexão para criar.

Atualmente, cinco tipos de pacotes de conexão são suportados:

+ **Pacotes completos,** indicados pela palavra-chave `all`
+ **Pacotes filtrados,** indicados pela palavra-chave, `where` seguidos de uma expressão predicado
+ **Pacotes convolucionais,** indicados pela `convolve` palavra-chave, seguidos pelos atributos de convolução
+ **Pacotes de piscina,** indicados pelas palavras-chave **no máximo piscina** ou piscina **média**
+ **Pacotes** de normalização de resposta, indicados pela norma de **resposta** de palavras-chave

## <a name="full-bundles"></a>Pacotes completos

Um pacote de conexão completo inclui uma ligação de cada nó na camada de origem a cada nó na camada de destino. Este é o tipo de ligação de rede padrão.

## <a name="filtered-bundles"></a>Pacotes filtrados

Uma especificação filtrada do pacote de conexão inclui um predicado, expresso sintaticamente, muito parecido com uma expressão de lambda C#. O exemplo a seguir define dois pacotes filtrados:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ No predicado para `ByRow` , é um parâmetro que representa um índice na matriz `s` retangular de nós da camada de entrada, `Pixels` e é um parâmetro que representa um índice na matriz de nós da camada `d` escondida, `ByRow` . O tipo de ambos `s` e `d` é um tuple de inteiros de comprimento dois. Conceptualmente, `s` varia todos os pares de inteiros com `0 <= s[0] < 10` `0 <= s[1] < 20` e, e varia sobre todos os pares `d` de inteiros, com `0 <= d[0] < 10` e `0 <= d[1] < 12` .

+ No lado direito da expressão predicado, há uma condição. Neste exemplo, por cada valor `s` e de tal forma que a condição é `d` Verdadeira, há uma borda do nó da camada de origem para o nó da camada de destino. Assim, esta expressão do filtro indica que o feixe inclui uma ligação do nó definido pelo `s` nó definido por em todos os `d` casos em que s[0] é igual a d[0].

Opcionalmente, pode especificar um conjunto de pesos para um pacote filtrado. O valor para o atributo Pesos deve ser um tuple de **valores** de ponto flutuante com um comprimento que corresponda ao número de ligações definidas pelo feixe. Por padrão, os pesos são gerados aleatoriamente.

Os valores de peso são agrupados pelo índice de nó de destino. Ou seja, se o primeiro nó de destino estiver ligado aos nós de origem K, os `K` primeiros elementos do **tuple Weights** são os pesos para o primeiro nó de destino, na ordem do índice de origem. O mesmo se aplica aos restantes nós de destino.

É possível especificar os pesos diretamente como valores constantes. Por exemplo, se tiver aprendido os pesos anteriormente, pode especificá-los como constantes usando esta sintaxe:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Pacotes convolucionais

Quando os dados de treino têm uma estrutura homogénea, as ligações convolucionais são geralmente usadas para aprender características de alto nível dos dados. Por exemplo, em dados de imagem, áudio ou vídeo, a dimensionalidade espacial ou temporal pode ser bastante uniforme.

Os feixes convolucionais empregam **núcleos** retangulares que são deslizantes através das dimensões. Essencialmente, cada núcleo define um conjunto de pesos aplicados nas vizinhanças locais, referidos como **aplicações de núcleo.** Cada aplicação de núcleo corresponde a um nó na camada de origem, que é referido como o **nó central**. Os pesos de um núcleo são partilhados entre muitas ligações. Num feixe convolucional, cada núcleo é retangular e todas as aplicações de núcleo são do mesmo tamanho.

Os pacotes convolucionais suportam os seguintes atributos:

**InputShape** define a dimensionalidade da camada de origem para os fins deste pacote convolucional. O valor deve ser uma amostra de inteiros positivos. O produto dos inteiros deve ser igual ao número de nós na camada de origem, mas, caso contrário, não precisa de corresponder à dimensionalidade declarada para a camada de origem. O comprimento deste tuple torna-se o valor **da aridade** para o feixe convolucional. Tipicamente aridade refere-se ao número de argumentos ou operands que uma função pode tomar.

Para definir a forma e a localização dos núcleos, utilize os atributos **KernelShape,** **Stride,** **Estofamento,** **LowerPad** e **UpperPad:**

+ **KernelShape**: (obrigatório) Define a dimensionalidade de cada núcleo para o feixe convolucional. O valor deve ser uma amostra de inteiros positivos com um comprimento que seja igual à aridade do feixe. Cada componente deste tuple não deve ser maior do que o componente correspondente do **InputShape**.

+ **Passo**: (opcional) Define os tamanhos do passo deslizante da convolução (um passo para cada dimensão), que é a distância entre os nós centrais. O valor deve ser uma amostra de inteiros positivos com um comprimento que é a aridade do feixe. Cada componente deste tuple não deve ser maior do que o componente correspondente da **KernelShape**. O valor predefinido é um tuple com todos os componentes iguais a um.

+ **Partilha:**(opcional) Define a partilha de peso para cada dimensão da convolução. O valor pode ser um único valor Boolean ou um tuple de valores booleanos com um comprimento que é a aridade do pacote. Um único valor Boolean é estendido para ser um tuple do comprimento correto com todos os componentes iguais ao valor especificado. O valor padrão é uma tuple que consiste em todos os valores verdadeiros.

+ **MapCount**: (opcional) Define o número de mapas de funcionalidades para o pacote convolucional. O valor pode ser um único inteiro positivo ou um tuple de inteiros positivos com um comprimento que é a aridade do feixe. Um único valor inteiro é estendido para ser um tuple do comprimento correto com os primeiros componentes iguais ao valor especificado e todos os restantes componentes iguais a um. O valor predefinido é um. O número total de mapas de características é o produto dos componentes do tuple. O factoring deste número total através dos componentes determina como os valores do mapa de funcionalidades são agrupados nos nós de destino.

+ **Pesos**: (opcional) Define os pesos iniciais para o feixe. O valor deve ser um tuple de valores de ponto flutuante com um comprimento que é o número de núcleos vezes o número de pesos por núcleo, tal como definido mais tarde neste artigo. Os pesos predefinidos são gerados aleatoriamente.

Existem dois conjuntos de propriedades que controlam o acolchoamento, sendo as propriedades mutuamente exclusivas:

+ **Estofamento**: (opcional) Determina se a entrada deve ser acolchoada utilizando um **esquema de enchimento predefinido**. O valor pode ser um único valor Boolean, ou pode ser um tuple de valores booleanos com um comprimento que é a aridade do pacote.

    Um único valor Boolean é estendido para ser um tuple do comprimento correto com todos os componentes iguais ao valor especificado.

    Se o valor para uma dimensão for Verdadeiro, a fonte é logicamente acolchoada nessa dimensão com células de valor zero para suportar aplicações adicionais de núcleo, de tal forma que os nós centrais do primeiro e último núcleo nessa dimensão são os primeiros e últimos nós nessa dimensão na camada de origem. Assim, o número de nós "manequim" em cada dimensão é determinado automaticamente, para encaixar exatamente `(InputShape[d] - 1) / Stride[d] + 1` em núcleos na camada de origem acolchoada.

    Se o valor para uma dimensão for Falso, os núcleos são definidos de modo a que o número de nós de cada lado que são deixados de fora seja o mesmo (até uma diferença de 1). O valor predefinido deste atributo é um tuple com todos os componentes iguais ao Falso.

+ **UpperPad** e **LowerPad**: (opcional) Forneça um maior controlo sobre a quantidade de estofos a utilizar. **Importante:** Estes atributos podem ser definidos se e somente se a propriedade **de enchimento** acima não for **_not_*definida. Os valores devem ser tuples de valor inteiro com comprimentos que são a aridade do feixe. Quando estes atributos são especificados, os nós "manequim" são adicionados às extremidades inferior e superior de cada dimensão da camada de entrada. O número de nós adicionados às extremidades inferior e superior de cada dimensão é determinado por _* LowerPad**[i] e **UpperPad**[i] respectivamente.

    Para garantir que os miolos correspondem apenas aos nóns "reais" e não aos nóns "manequim", devem ser satisfeitas as seguintes condições:
  - Cada componente do **LowerPad** deve ser estritamente inferior `KernelShape[d]/2` .
  - Cada componente do **UpperPad** não deve ser maior do que `KernelShape[d]/2` .
  - O valor padrão destes atributos é um tuple com todos os componentes iguais a 0.

    O **estofamento** de definição = verdadeiro permite o preenchimento necessário para manter o "centro" do núcleo dentro da entrada "real". Isto muda um pouco a matemática para calcular o tamanho da saída. Geralmente, o tamanho de saída *D* é calculado como `D = (I - K) / S + 1` , onde é o tamanho da `I` entrada, é o tamanho do `K` núcleo, é `S` o passo, e `/` é divisão inteiro (redondo para zero). Se definir UpperPad = [1, 1], o tamanho da entrada `I` é efetivamente 29, e assim `D = (29 - 5) / 2 + 1 = 13` . . No entanto, quando **o estofamento** = verdadeiro, `I` essencialmente é aumentado `K - 1` por; `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14` Ao especificar valores para **UpperPad** e **LowerPad** obtém muito mais controlo sobre o acolchoado do que se apenas definir **o acolchoado** = verdadeiro.

Para obter mais informações sobre redes convolucionais e suas aplicações, consulte estes artigos:

+ [http://d2l.ai/chapter_convolutional-neural-networks/lenet.html ](http://d2l.ai/chapter_convolutional-neural-networks/lenet.html )
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Pacotes de piscina

Um **pacote de pooling** aplica geometria semelhante à conectividade convolucional, mas utiliza funções predefinidas para obter valores de nó para obter o valor do nó de destino. Assim, os feixes de agrupamento não têm estado treinável (pesos ou enviesamentos). Os pacotes de pooling suportam todos os atributos convolucionais, exceto **partilha,** **MapCount** e **Pesos.**

Normalmente, os núcleos resumidos por unidades de agrupamento adjacentes não se sobrepõem. Se o Stride[d] for igual ao KernelShape[d] em cada dimensão, a camada obtida é a camada tradicional de agrupamento local, que é geralmente utilizada em redes neuronais convolucionais. Cada nó de destino calcula o máximo ou a média das atividades do seu núcleo na camada de origem.

O exemplo a seguir ilustra um pacote de piscina:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ A aridade do feixe é 3: isto é, o comprimento das tuples, `InputShape` `KernelShape` e `Stride` .
+ O número de nós na camada de origem é `5 * 24 * 24 = 2880` .
+ Esta é uma camada tradicional de piscina local porque **KernelShape** e **Stride** são iguais.
+ O número de nós na camada de destino é `5 * 12 * 12 = 1440` .

Para obter mais informações sobre as camadas de agrupamento, consulte estes artigos:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Secção 3.4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Pacotes de normalização de resposta

**A normalização da resposta** é um esquema de normalização local que foi introduzido pela primeira vez por Geoffrey Hinton, et al, no artigo [ImageNet Classification com Deep Convolutional Neural Networks](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

A normalização da resposta é utilizada para ajudar a generalização nas redes neurais. Quando um neurónio está a disparar a um nível de ativação muito elevado, uma camada de normalização de resposta local suprime o nível de ativação dos neurónios circundantes. Isto é feito usando três parâmetros `α` (, `β` e ) e uma `k` estrutura convolucional (ou forma de bairro). Cada neurónio na camada de destino **y** corresponde a um neurónio **x** na camada de origem. O nível de ativação do **y** é dado pela seguinte fórmula, onde `f` está o nível de ativação de um neurónio, e `Nx` é o núcleo (ou o conjunto que contém os neurónios na vizinhança de **x),** tal como definido pela seguinte estrutura convolucional:

![fórmula para estrutura convolucional](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Os pacotes de normalização de resposta suportam todos os atributos convolucionais, exceto **partilha,** **MapCount** e **Pesos.**

+ Se o núcleo contiver neurónios no mesmo mapa **_que x_*_, o esquema de normalização é referido como _* mesma normalização do mapa**. Para definir a mesma normalização do mapa, a primeira coordenada no **InputShape** deve ter o valor 1.

+ Se o núcleo contiver neurónios na mesma posição espacial que **_x_*_, mas os neurónios estão em outros mapas, o esquema de normalização é chamado de _* através da normalização dos mapas**. Este tipo de normalização de resposta implementa uma forma de inibição lateral inspirada no tipo encontrado em neurónios reais, criando competição para grandes níveis de ativação entre as saídas de neurónios computadas em diferentes mapas. Para definir entre mapas a normalização, a primeira coordenada deve ser um número inteiro maior do que um e não maior do que o número de mapas, e o resto das coordenadas deve ter o valor 1.

Como os pacotes de normalização de resposta aplicam uma função predefinida aos valores do nó de origem para determinar o valor do nó de destino, não têm estado treinável (pesos ou enviesamentos).

> [!NOTE]
> Os nós na camada de destino correspondem aos neurónios que são os nós centrais dos núcleos. Por exemplo, se `KernelShape[d]` for estranho, então `KernelShape[d]/2` corresponde ao nó central do núcleo. Se `KernelShape[d]` estiver em patamar, o nó central está em `KernelShape[d]/2 - 1` . Portanto, se `Padding[d]` for Falso, o primeiro e os `KernelShape[d]/2` últimos nós não têm nós correspondentes na camada de destino. Para evitar esta situação, defina **o estofamento** como [verdadeiro, verdadeiro, ..., verdadeiro].

Além dos quatro atributos descritos anteriormente, os pacotes de normalização de resposta também suportam os seguintes atributos:

+ **Alpha**: (obrigatório) Especifica um valor de ponto flutuante que corresponde `α` à fórmula anterior.
+ **Beta**: (obrigatório) Especifica um valor de ponto flutuante que corresponde `β` à fórmula anterior.
+ **Offset**: (opcional) Especifica um valor de ponto flutuante que corresponde `k` à fórmula anterior. Falha em 1.

O exemplo a seguir define um pacote de normalização de resposta utilizando estes atributos:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ A camada de origem inclui cinco mapas, cada um com uma dimensão aof de 12x12, totalizando em 1440 nós.
+ O valor do **KernelShape** indica que esta é uma mesma camada de normalização do mapa, onde o bairro é um retângulo 3x3.
+ O valor padrão do **estofamento** é falso, pelo que a camada de destino tem apenas 10 nós em cada dimensão. Para incluir um nó na camada de destino que corresponde a cada nó na camada de origem, adicione o estofamento = [verdadeiro, verdadeiro, verdadeiro]; e alterar o tamanho de RN1 para [5, 12, 12].

## <a name="share-declaration"></a>Declaração de ações

Net# suporta opcionalmente a definição de múltiplos pacotes com pesos partilhados. Os pesos de qualquer dois pacotes podem ser partilhados se as suas estruturas forem as mesmas. A seguinte sintaxe define pacotes com pesos partilhados:

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

Por exemplo, a seguinte declaração de partilha especifica os nomes das camadas, indicando que tanto os pesos como os enviesamentos devem ser partilhados:

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
+ A declaração de ações especifica que *h1* e *H2* devem ser calculados da mesma forma a partir das respetivas entradas.

Em alternativa, esta poderia ser especificada com duas declarações de ações separadas da seguinte forma:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

Só pode utilizar o formulário curto quando as camadas contêm um único feixe. Em geral, a partilha só é possível quando a estrutura relevante é idêntica, o que significa que têm o mesmo tamanho, a mesma geometria convolucional, e assim por diante.

## <a name="examples-of-net-usage"></a>Exemplos de utilização da Net#

Esta secção fornece alguns exemplos de como pode usar o Net# para adicionar camadas escondidas, definir a forma como as camadas ocultas interagem com outras camadas e construir redes convolucionais.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Defina uma simples rede neural personalizada: exemplo "Hello World"

Este exemplo simples demonstra como criar um modelo de rede neural que tenha uma única camada escondida.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

O exemplo ilustra alguns comandos básicos da seguinte forma:

+ A primeira linha define a camada de entrada `Data` (nomeada). Quando utiliza a  `auto` palavra-chave, a rede neural inclui automaticamente todas as colunas de funcionalidades nos exemplos de entrada.
+ A segunda linha cria a camada escondida. O nome `H` é atribuído à camada escondida, que tem 200 nós. Esta camada está totalmente ligada à camada de entrada.
+ A terceira linha define a camada de saída `Out` (nomeada), que contém 10 nós de saída. Se a rede neural for utilizada para a classificação, existe um nó de saída por classe. O **sigmoide da** palavra-chave indica que a função de saída é aplicada na camada de saída.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definir várias camadas ocultas: exemplo de visão computacional

O exemplo a seguir demonstra como definir uma rede neural ligeiramente mais complexa, com múltiplas camadas personalizadas escondidas.

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

+ A estrutura tem duas camadas de entrada, `Pixels` `MetaData` e.
+ A `Pixels` camada é uma camada de origem para dois pacotes de conexão, com camadas de destino, e `ByRow` `ByCol` .
+ As camadas `Gather` e `Result` são camadas de destino em pacotes de conexão múltipla.
+ A camada de `Result` saída, é uma camada de destino em dois pacotes de conexão; uma com a camada escondida de segundo nível `Gather` como camada de destino, e a outra com a camada de entrada `MetaData` como camada de destino.
+ As camadas ocultas `ByRow` `ByCol` e, especificam a conectividade filtrada utilizando expressões predicados. Mais precisamente, o nó em `ByRow` [x, y] está ligado aos nós em `Pixels` que têm a primeira coordenada de índice igual à primeira coordenada do nó, x. Da mesma forma, o nó em `ByCol` [x, y] está ligado aos nós em `Pixels` que têm a segunda coordenada de índice dentro de uma das segundas coordenadas do nó, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definir uma rede convolucional para classificação multiclasse: exemplo de reconhecimento de dígitos

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

+ A estrutura tem uma única camada de entrada, `Image` .
+ A palavra-chave `convolve` indica que as camadas nomeadas `Conv1` e são `Conv2` camadas convolucionais. Cada uma destas declarações de camada é seguida por uma lista dos atributos de convolução.
+ A rede tem uma terceira camada escondida, `Hid3` que está totalmente ligada à segunda camada escondida, `Conv2` .
+ A camada de `Digit` saída, está ligada apenas à terceira camada escondida, `Hid3` . A palavra-chave `all` indica que a camada de saída está totalmente ligada a `Hid3` .
+ A aridade da convolução é três: o comprimento dos tuples, `InputShape` `KernelShape` e `Stride` `Sharing` .
+ O número de pesos por núcleo é `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26` . Ou `26 * 50 = 1300` ou.
+ Pode calcular os nós em cada camada escondida da seguinte forma:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ O número total de nós pode ser calculado utilizando a dimensionalidade declarada da camada, [50, 5, 5], da seguinte forma: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Porque `Sharing[d]` é falso apenas para , o número `d == 0` de núcleos é `MapCount * NodeCount\[0] = 10 * 5 = 50` .

## <a name="acknowledgements"></a>Agradecimentos

A linguagem Net# para personalizar a arquitetura das redes neurais foi desenvolvida na Microsoft por Shon Katzenberger (Architect, Machine Learning) e Alexey Kamenev (Software Engineer, Microsoft Research). É utilizado internamente para projetos de machine learning e aplicações que vão desde a deteção de imagem até análise de texto. Para mais informações, consulte [Neural Nets no estúdio Azure Machine Learning - Introdução à Net#](/archive/blogs/machinelearning/neural-nets-in-azure-ml-introduction-to-net)