---
title: 'Criar redes neuronais personalizadas com Net #'
titleSuffix: Azure Machine Learning Studio
description: 'Guia de sintaxe para a linguagem de especificação de redes neurais Net #. Saiba como criar modelos de rede neural personalizada no Azure Machine Learning Studio.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: 13ec97a8356bb24fbbc2098f1249ae8fa5b6e3ce
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877099"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning-studio"></a>Guia para a linguagem de especificação Net # rede neural para o Azure Machine Learning Studio

NET # é uma linguagem desenvolvida pela Microsoft, que é utilizada para definir arquiteturas de rede neural complexas, como redes neurais profundas ou convolutions de dimensões arbitrárias. Pode usar as estruturas complexas para melhorar o aprendizado nos dados, como a imagem, vídeo ou áudio.

Pode usar uma especificação de arquitetura do Net # desses contextos:

+ Todos os módulos de redes neuronais no Microsoft Azure Machine Learning Studio: [Várias classes rede Neural](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [rede Neural de classe dois](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network), e [regressão de rede Neural](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Funções de rede neural no Microsoft ML Server: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) e [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)para a linguagem R, e [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) para Python.


Este artigo descreve os conceitos básicos e a sintaxe necessária para desenvolver uma rede neural personalizada usando Net #:

+ Requisitos de rede neural e como definir os componentes principais
+ A sintaxe e as palavras-chave da linguagem de especificação Net #
+ Exemplos de redes neuronais personalizadas criadas com Net #



## <a name="neural-network-basics"></a>Noções básicas de rede neural

Uma estrutura de rede neural é composta por nós que estão organizados em camadas e ligações ponderadas (ou bordas) entre os nós. As ligações são direcionais e cada ligação tem um nó de origem e um nó de destino.

Cada camada trainable (um oculta ou uma camada de saída) tem um ou mais **pacotes de conexão**. Um pacote de ligação é constituída por uma camada de origem e de uma especificação das ligações a partir dessa camada de origem. Todas as conexões num determinado pacote compartilham camadas de origem e de destino. Em Net #, um pacote de ligação é considerado como pertencendo à camada de destino do pacote.

NET # oferece suporte a vários tipos de ligação de pacotes, o que lhe permite personalizar as entradas de forma são mapeados para camadas ocultas e mapeados para as saídas.

O padrão ou o pacote padrão é uma **pacote completo**, em que cada nó na camada de origem que está ligado a todos os nós na camada de destino.

Além disso, Net # suporta os seguintes quatro tipos de pacotes de ligação avançada:

+ **Filtrado pacotes**. Pode definir um predicado utilizando as localizações de nó de camada de origem e o nó de camada de destino. Nós estão conectados sempre que o predicado é True.

+ **Os pacotes de convolucionais**. Pode definir vizinhanças pequeno de nós na camada de origem. Cada nó na camada de destino está ligado a um ambiente de nós na camada de origem.

+ **Agrupamento de pacotes** e **pacotes de normalização de resposta**. Estes são semelhantes aos pacotes convolucionais em que o utilizador define vizinhanças pequeno de nós na camada de origem. A diferença é que os pesos das bordas nesses pacotes não são trainable. Em vez disso, uma função predefinida é aplicada para os valores de nó de origem para determinar o valor do nó de destino.


## <a name="supported-customizations"></a>Personalizações suportadas

A arquitetura de modelos de rede neural que criar no Azure Machine Learning Studio pode ser personalizada amplamente utilizando Net #. Pode:

+ Criar camadas ocultas e controlar o número de nós em cada camada.
+ Especifique como camadas devem ser ligados entre si.
+ Defina as estruturas de conectividade especiais, como convolutions e peso da partilha de pacotes.
+ Especifique as funções de ativação diferentes.

Para obter detalhes sobre a sintaxe de linguagem de especificação, consulte [especificação da estrutura](#Structure-specifications).

Para obter exemplos de definição de redes neurais para algumas tarefas, desde a simplex para complexos, de aprendizagem comuns, consulte [exemplos](#Examples-of-Net#-usage).

## <a name="general-requirements"></a>Requisitos gerais

+ Tem de existir camada de saída exatamente uma, pelo menos uma camada de entrada e zero ou mais camadas ocultas.
+ Cada camada tem um número fixo de nós, conceitualmente organizados numa matriz retangular de dimensões arbitrárias.
+ Camadas de entrada não tem associados treinados parâmetros e representam o ponto em que os dados de instância entra na rede.
+ (As camadas ocultas e de saída) de camadas trainable associou parâmetros preparados, conhecidos como pesos e tendências.
+ Os nós de origem e de destino devem estar em camadas separadas.
+ Ligações têm de ser acíclicos; em outras palavras, não pode ser uma cadeia de ligações que leva novamente para o nó de origem inicial.
+ A camada de saída não pode ser uma camada de origem de um pacote de ligação.

## <a name="structure-specifications"></a>Especificações de estrutura

Uma especificação de estrutura de rede neural é composta por três secções: os **declaração constante**, o **declaração de camada**, o **declaração de ligação**. Também há opcional **partilhar declaração** secção. As secções podem ser especificadas por qualquer ordem.

## <a name="constant-declaration"></a>Declaração de constante

Uma declaração constante é opcional. Ele fornece um meio para definir os valores usados em outros lugares na definição de rede neural. A instrução de declaração é composta por um identificador seguido por um sinal de igual e uma expressão de valor.

Por exemplo, a instrução a seguir define uma constante `x`:

`Const X = 28;`

Para definir dois ou mais constantes em simultâneo, coloque os nomes de identificador e os valores entre chaves e, separadas por ponto e vírgula. Por exemplo:

`Const { X = 28; Y = 4; }`

O lado direito da expressão cada atribuição pode ser um número inteiro, um número real, um valor booleano (True ou False) ou uma expressão matemática. Por exemplo:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Declaração de camada

A declaração de camada é necessária. Define o tamanho e a origem da camada, incluindo seus pacotes de conexão e atributos. A instrução de declaração inicia com o nome da camada (de entrada, oculto ou de saída), seguido as dimensões da camada (uma cadeia de identificação de números inteiros positivos). Por exemplo:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ O produto das dimensões é o número de nós na camada. Neste exemplo, existem duas dimensões [5,20], que significa que existem 100 nós na camada.
+ As camadas podem ser declaradas em qualquer ordem, com uma exceção: Se mais do que uma camada de entrada é definida, a ordem em que elas são declaradas tem de corresponder a ordem dos recursos nos dados de entrada.

Para especificar que o número de nós numa camada de determinado automaticamente, utilize o `auto` palavra-chave. O `auto` palavra-chave tem efeitos diferentes, consoante a camada:

+ Numa declaração de camada de entrada, o número de nós é o número de recursos nos dados de entrada.
+ Numa declaração de camada oculta, o número de nós é o número especificado pelo valor do parâmetro para **número de nós ocultos**.
+ Numa declaração de camada de saída, o número de nós é 2 para classificação de duas classes, 1 para regressão e igual ao número de nós de saída para a classificação multiclasses.

Por exemplo, a seguinte definição de rede permite que o tamanho de todas as camadas para ser determinado automaticamente:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

Uma declaração de camada para uma camada trainable (as camadas ocultas ou de saída), opcionalmente, pode incluir a função de saída (também chamada de uma função de ativação), que assume a predefinição **sigmoide** para modelos de classificação, e  **linear** para modelos de regressão. Mesmo que utilize a predefinição, pode declare explicitamente a função de ativação, se assim o desejar por motivos de clareza.

São suportadas as seguintes funções de saída:

+ sigmoid
+ Linear
+ softmax
+ rlinear
+ Quadrado
+ sqrt
+ srlinear
+ Abs
+ tanh
+ brlinear

Por exemplo, a seguinte declaração utiliza a **softmax** função:

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Declaração de ligação

Imediatamente depois de definir a camada trainable, deve declarar ligações entre as camadas que definiu. A declaração de pacote de ligação começa com a palavra-chave `from`, seguido pelo nome da camada de origem do pacote e o tipo de pacote de ligação para criar.

Atualmente, são suportados cinco tipos de pacotes de ligação:

+ **Total** pacotes, indicados pela palavra-chave `all`
+ **Filtrado** pacotes, indicados pela palavra-chave `where`, seguido de uma expressão de predicado
+ **Convolucionais** pacotes, indicados pela palavra-chave `convolve`, seguido pelos atributos de convolution
+ **Agrupamento** pacotes, indicados pelas palavras-chave **máx. de conjunto** ou **significa agrupamento**
+ **Normalização de resposta** pacotes, indicados pela palavra-chave **norma de resposta**

## <a name="full-bundles"></a>Pacotes de completos

Um pacote de ligação completo inclui uma ligação de cada nó na camada de origem para cada nó na camada de destino. Este é o tipo de ligação de rede padrão.

## <a name="filtered-bundles"></a>Pacotes filtrados

Uma especificação de pacote de ligação filtrada inclui um muito predicado, sintaticamente, expresso como um C# expressão lambda. O exemplo seguinte define dois pacotes filtrados:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ No predicado para `ByRow`, `s` é um parâmetro que representa um índice na matriz de nós de camada de entrada, retangular `Pixels`, e `d` é um parâmetro que representa um índice na matriz de nós da camada oculta, `ByRow`. O tipo de ambos `s` e `d` é uma cadeia de identificação de números inteiros de comprimento dois. Conceitualmente, `s` varia ao longo de todos os pares de números inteiros com `0 <= s[0] < 10` e `0 <= s[1] < 20`, e `d` varia ao longo de todos os pares de números inteiros, com `0 <= d[0] < 10` e `0 <= d[1] < 12`.

+ No lado direito da expressão de predicado, existe uma condição. Neste exemplo, para cada valor de `s` e `d` , de modo a que a condição for True, existe um limite de nó de camada de origem para o nó de camada de destino. Portanto, esta expressão de filtro indica que o pacote inclui uma ligação de nó definido pela `s` para o nó definido pelo `d` em todos os casos em que [0] é igual a d [0].

Opcionalmente, pode especificar um conjunto de pesos para um pacote filtrado. O valor para o **pesos** atributo tem de ser uma cadeia de identificação de ponto de valores com um comprimento que corresponde ao número de ligações definidos pelo pacote de vírgula flutuante. Por predefinição, os pesos são gerados aleatoriamente.

Valores de peso são agrupados pelo índice do nó de destino. Ou seja, se o primeiro nó de destino está ligado a nós de origem de K, o primeiro `K` elementos do **pesos** cadeias de identificação são os pesos para o primeiro nó de destino, na ordem de índice de origem. O mesmo aplica-se para os restantes nós de destino.

É possível especificar pesos diretamente como valores de constantes. Por exemplo, se aprendeu os pesos anteriormente, pode especificá-las como constantes utilizando esta sintaxe:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Pacotes de convolucionais

Quando os dados de treinamento têm uma estrutura homogénea, convolucionais ligações são utilizadas frequentemente para saber mais recursos de alto nível dos dados. Por exemplo, na imagem, dados de áudio ou vídeos, dimensionalidade geográfico ou temporal podem ser bastante uniforme.

Os pacotes de convolucionais empregam retangular **kernels** que são slid por meio das dimensões. Essencialmente, cada kernel define um conjunto de pesos aplicadas no locais vizinhanças, denominados **aplicações de kernel**. Cada aplicativo de kernel corresponde a um nó na camada de origem, o que é conhecida como a **nó central**. Os pesos de um kernel são partilhados entre muitas ligações. Num pacote convolucional, cada kernel é retangular e todas as aplicações de kernel têm o mesmo tamanho.

Os pacotes de convolucionais suportam os seguintes atributos:

**InputShape** define à dimensionalidade da camada de origem para os fins deste pacote convolucionais. O valor tem de ser uma cadeia de identificação de números inteiros positivos. O produto dos inteiros deve ser igual ao número de nós na camada de origem, mas caso contrário, não precisa de corresponda à dimensionalidade declarada para a camada de origem. O comprimento desta tupla torna-se a **arity** valor para o pacote de convolucional. Normalmente, arity refere-se ao número de argumentos ou operandos que pode efetuar uma função.

Para definir a forma e localizações dos kernels, utilize os atributos **KernelShape**, **Stride**, **preenchimento**, **LowerPad**e o  **UpperPad**:

+ **KernelShape**: define o à dimensionalidade do cada kernel para o pacote de convolucional (obrigatório). O valor tem de ser uma cadeia de identificação de números inteiros positivos com um comprimento igual a arity do pacote. Cada componente desta cadeia de identificação deve ser não maior do que o componente correspondente do **InputShape**.

+ **STRIDE**: (opcional) define os tamanhos de passo deslizante de convolution (tamanho de uma etapa para cada dimensão), que é a distância entre os nós centrais. O valor tem de ser uma cadeia de identificação de números inteiros positivos com um comprimento que é o arity do pacote. Cada componente desta cadeia de identificação deve ser não maior do que o componente correspondente do **KernelShape**. O valor predefinido é uma cadeia de identificação com todos os componentes iguais a um.

+ **Partilha**: (opcional) define o peso de partilha para cada dimensão do convolution. O valor pode ser um valor booleano único ou uma cadeia de identificação de valores booleanos com um comprimento que é o arity do pacote. Um valor booleano único é expandido para ser uma cadeia de identificação do comprimento correta com todos os componentes iguais ao valor especificado. O valor predefinido é uma cadeia de identificação que consiste em todos os valores verdadeiros.

+ **MapCount**: (opcional) define o número de funcionalidade mapeia para o pacote de convolucional. O valor pode ser um único número inteiro ou uma cadeia de identificação de números inteiros positivos com um comprimento que é o arity do pacote. Um só valor inteiro é expandido para ser uma cadeia de identificação do comprimento correta com os componentes primeiro iguais ao valor especificado e todos os componentes restantes igual a um. O valor predefinido é um. O número total de mapas de recursos é o produto dos componentes da tupla. O cálculo deste número total entre os componentes determina como os valores de mapa de recursos são agrupados em nós de destino.

+ **Pesos**: (opcional) define os pesos iniciais para o pacote. O valor tem de ser uma cadeia de identificação de vírgula flutuante valores de ponto com um comprimento que é o número de vezes que kernels o número de pesos por kernel, conforme definido neste artigo. As ponderações predefinidas são geradas aleatoriamente.

Existem dois conjuntos de propriedades que controlam o preenchimento, as propriedades que está a ser mutuamente exclusivas:

+ **Preenchimento**: (opcional) determina se a entrada deve ser preenchida usando um **esquema de preenchimento predefinido**. O valor pode ser um valor booleano único, ou pode ser uma cadeia de identificação de valores booleanos com um comprimento que é o arity do pacote.

    Um valor booleano único é expandido para ser uma cadeia de identificação do comprimento correta com todos os componentes iguais ao valor especificado.

    Se o valor para uma dimensão for True, a origem logicamente será preenchida nessa dimensão com células com valor de zero para oferecer suporte a aplicativos de kernel adicionais, que os nós de centrais dos kernels primeiros e últimos essa dimensão são os nós de primeiros e últimos em que dimensão na camada de origem. Portanto, o número de nós "fictícios" em cada dimensão é determinado automaticamente, para que se ajuste exatamente `(InputShape[d] - 1) / Stride[d] + 1` kernels a camada de origem preenchido.

    Se o valor para uma dimensão for False, os kernels são definidos para que o número de nós em cada lado que são deixados de lado é o mesmo (até uma diferença de 1). O valor predefinido deste atributo é uma cadeia de identificação com todos os componentes iguais a False.

+ **UpperPad** e **LowerPad**: (opcional) forneça mais controle sobre a quantidade de preenchimento para utilizar. **Importante:** Estes atributos podem ser definido se e apenas se o **preenchimento** propriedade acima é ***não*** definido. Os valores devem ser cadeias de identificação com valor de número inteiro com comprimentos de serem arity do pacote. Quando esses atributos são especificados, nós "fictícios" são adicionadas até as extremidades superiores e inferior de cada dimensão da camada de entrada. O número de nós adicionados até as extremidades superiores e inferior em cada dimensão é determinado pelo **LowerPad**[i] e **UpperPad**[i], respetivamente.

    Para garantir que os kernels correspondem apenas em nós de "reais" e não a nós de "fictícios", as condições seguintes têm de ser cumpridas:
      - Cada componente do **LowerPad** tem de ser estritamente menor que `KernelShape[d]/2`.
      - Cada componente do **UpperPad** tem de ter mais de `KernelShape[d]/2`.
      - O valor predefinido desses atributos é uma cadeia de identificação com todos os componentes iguais a 0.

    A definição **preenchimento** = true permite tanto preenchimento conforme necessário, para manter o "center" do kernel do dentro de "real" de entrada. Isto altera a matemática um pouco para computação o tamanho de saída. Em geral, o tamanho de saída *1!d* é computado como `D = (I - K) / S + 1`, em que `I` é o tamanho de entrada, `K` é o tamanho de kernel `S` é o stride, e `/` é a divisão de número inteiro (redondo na direção de 0 ). Se definir UpperPad = [1, 1], o tamanho de entrada `I` é, efetivamente, 29 e, portanto, `D = (29 - 5) / 2 + 1 = 13`. No entanto, quando **preenchimento** = true, essencialmente `I` obtém diminuí a cópia de segurança por `K - 1`; assim, `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Ao especificar valores para **UpperPad** e **LowerPad** obtém muito mais controle sobre o preenchimento do que se acabou de definir **preenchimento** = true.

Para obter mais informações sobre redes convolucionais e seus aplicativos, veja estes artigos:

+ [http://deeplearning.net/tutorial/lenet.html](http://deeplearning.net/tutorial/lenet.html)
+ [http://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)
+ [http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)

## <a name="pooling-bundles"></a>Agrupamento de pacotes

R **agrupamento de pacote** aplica-se de geometria semelhante à conectividade convolucional, mas utiliza funções predefinidas para os valores de nó de origem para derivar o valor do nó de destino. Por este motivo, os pacotes de pooling não tem nenhum Estado trainable (pesos ou tendências). Os pacotes de pooling suportam todos os atributos convolucionais, exceto **partilha**, **MapCount**, e **pesos**.

Normalmente, os kernels resumidos por unidades de pooling adjacentes não se sobrepõem. Se o Stride [d] é igual a KernelShape [d] em cada dimensão, a camada de obteve é a tradicional camada pooling local, que normalmente é empregada em redes neurais convolucionais. Cada nó de destino calcula o número máximo ou a média das atividades do seu kernel na camada de origem.

O exemplo a seguir ilustra um pacote de pooling:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ Arity do pacote é 3: ou seja, o comprimento das tuplas `InputShape`, `KernelShape`, e `Stride`.
+ O número de nós na camada de origem é `5 * 24 * 24 = 2880`.
+ Esta é uma camada de pooling local tradicional porque **KernelShape** e **Stride** são iguais.
+ O número de nós na camada de destino é `5 * 12 * 12 = 1440`.

Para obter mais informações sobre camadas de pooling, veja estes artigos:

+ [http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Secção 3.4)
+ [http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Pacotes de normalização de resposta

**Normalização de resposta** é um esquema de normalização local que foi introduzido pela primeira vez por Geoffrey Hinton, e outros, o documento [ImageNet classificação com redes Neurais Convolucionais profundas](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

Normalização de resposta é utilizada para ajudar a generalização em redes neurais. Quando um neurônio está a ser ativada num nível muito alto de ativação, uma camada de normalização de resposta local suprime o nível de ativação dos neurônios ao redor. Isso é feito com três parâmetros (`α`, `β`, e `k`) e uma estrutura convolucional (ou Vizinhança forma). Cada neurônio da camada de destino **y** corresponde a um neurônio **x** na camada de origem. O nível de ativação de **y** é fornecido pela seguinte fórmula, onde `f` é o nível de ativação de um neurônio, e `Nx` é o kernel (ou o conjunto que contém os neurônios tem cerca de **x**), conforme definido pela estrutura convolucional seguinte:

![fórmula para a estrutura convolucional](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Os pacotes de normalização de resposta suportam todos os atributos convolucionais, exceto **partilha**, **MapCount**, e **pesos**.

+ Se o kernel contém neurônios no mesmo mapa como ***x***, o esquema de normalização é referido como **mesmo mapear normalização**. Para definir o mesmo normalização de mapa, a coordenada primeiro na **InputShape** tem de ter o valor 1.

+ Se o kernel contém neurônios na mesma posição que espaciais ***x***, mas os neurônios são outros Maps, o esquema de normalização é chamado **mapeia na normalização**. Este tipo de normalização de resposta implementa uma forma de inhibition lateral inspirado pelo tipo encontrado no neurônios real, a criação de concorrência para níveis de ativação grande entre as saídas de neurônio computadas em mapas diferentes. Para definir em normalização de mapas, a coordenada primeiro tem de ser um número inteiro maior que um e não superior ao número de mapas, e o restante das coordenadas tem de ter o valor 1.

Uma vez que os pacotes de normalização de resposta aplicam-se uma função predefinida para valores de nó de origem para determinar o valor do nó de destino, eles têm sem estado trainable (pesos ou tendências).

> [!NOTE]
> Os nós na camada de destino correspondem aos neurônios que são nós de centrais dos kernels. Por exemplo, se `KernelShape[d]` for ímpar, em seguida, `KernelShape[d]/2` corresponde ao nó de central kernel. Se `KernelShape[d]` for par, o nó central é em `KernelShape[d]/2 - 1`. Por conseguinte, se `Padding[d]` é False, o primeiro e último `KernelShape[d]/2` nós não têm nós correspondentes na camada de destino. Para evitar esta situação, defina **preenchimento** como [true, true,..., true].

Além dos quatro atributos descritos anteriormente, os pacotes de normalização de resposta também suportam os seguintes atributos:

+ **Alpha**: (obrigatória) Especifica um valor de ponto flutuante que corresponde ao `α` na fórmula anterior.
+ **Beta**: (obrigatória) Especifica um valor de ponto flutuante que corresponde ao `β` na fórmula anterior.
+ **Deslocamento**: (opcional) Especifica um valor de ponto flutuante que corresponde ao `k` na fórmula anterior. Ele é predefinido para 1.

O exemplo seguinte define um pacote de normalização de resposta utilizando esses atributos:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ A camada de origem inclui cinco mapas, cada um com dimensão aof 12 x 12, totalizando em nós de 1440.
+ O valor de **KernelShape** indica que se trata de uma camada de normalização mesmo mapa, em que toda a Vizinhança é um retângulo de 3x3.
+ O valor predefinido **preenchimento** for False, portanto, a camada de destino tem apenas 10 nós em cada dimensão. Para incluir um nó na camada de destino que corresponde a todos os nós na camada de origem, adicione preenchimento = [' true ', ' true ', ' true ']; e alterar o tamanho de RN1 [5, 12, 12].

## <a name="share-declaration"></a>Declaração de partilha

NET #, opcionalmente, compatível com a definição múltiplos pacotes com pesos partilhados. Os pesos de quaisquer dois pacotes podem ser partilhados se suas estruturas são os mesmos. A seguinte sintaxe define os pacotes com pesos partilhados:

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

Por exemplo, a seguinte declaração de partilha Especifica os nomes de camada, que indica que os pesos e tendências devem ser partilhadas:

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

+ As funcionalidades de entrada são particionadas em duas camadas de entrada tamanho iguais.
+ As camadas ocultas, em seguida, funcionalidades de nível superior em duas camadas de entrada de computação.
+ A declaração de partilha Especifica que *H1* e *H2* devem ser computados da mesma forma de suas respectivas entradas.

Em alternativa, isto pode ser especificado com duas declarações de partilha separadas da seguinte forma:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

Pode usar a forma apenas quando as camadas de contenham um único pacote. Em geral, partilha é possível apenas quando a estrutura relevante é idêntica, o que significa que têm o mesmo tamanho, mesma geometria convolucional e assim por diante.

## <a name="examples-of-net-usage"></a>Exemplos de utilização do Net #

Esta seção fornece alguns exemplos de como pode usar Net # para adicionar camadas ocultas, definir a forma que camadas ocultas interagirem com outras camadas e criar as redes convolucionais.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Defina uma rede neural personalizada simples: Exemplo de "Olá, mundo"

Neste exemplo simples demonstra como criar um modelo de rede neural que tem uma única camada oculta.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

O exemplo ilustra alguns comandos básicos da seguinte forma:

+ A primeira linha define a camada de entrada (com o nome `Data`). Quando utiliza o `auto` palavra-chave, a rede neural inclui automaticamente todas as colunas de funcionalidade nos exemplos de entrada.
+ A segunda linha cria camada oculta. O nome `H` é atribuído à camada oculta, que tem 200 nós. Esta camada é totalmente conectada à camada de entrada.
+ A terceira linha define a camada de saída (com o nome `Out`), que contém 10 nós de saída. Se a rede neural é utilizada para classificação, existe um nó de saída por classe. A palavra-chave **sigmoide** indica que a função de saída é aplicada para a camada de saída.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definir várias camadas ocultas: exemplo de visão do computador

O exemplo seguinte demonstra como definir uma rede neural um pouco mais complexa, com várias camadas ocultas personalizadas.

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
+ O `Pixels` camada é uma camada de origem para dois pacotes de ligação, com camadas de destino, `ByRow` e `ByCol`.
+ As camadas `Gather` e `Result` são as camadas de destino em múltiplos pacotes de ligação.
+ A camada de saída `Result`, é uma camada de destino em dois pacotes de conexão, e outra com o segundo nível camada oculta `Gather` como uma camada de destino e a outra com a camada de entrada `MetaData` como uma camada de destino.
+ As camadas ocultas, `ByRow` e `ByCol`, especifique conectividade filtrada com expressões de predicado. Mais precisamente, o nó `ByRow` em [x, y] está ligado a nós no `Pixels` que tem o primeiro índice coordenar igual para o nó do x de coordenadas, primeiro. Da mesma forma, o nó `ByCol` em [x, y] está ligado a nós no `Pixels` que tenha o segundo índice coordenar dentro de um nó do y de coordenadas, segundo.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definir uma rede convolucional para classificação multiclasses: exemplo de reconhecimento de dígitos

A definição da seguinte rede foi concebida para reconhecer números e ilustra algumas técnicas avançadas para a personalização de uma rede neural.

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

+ A estrutura tiver uma única camada de entrada, `Image`.
+ A palavra-chave `convolve` indica que as camadas com o nome `Conv1` e `Conv2` são convolucionais camadas. Cada um dessas declarações de camada é seguida por uma lista dos atributos convolution.
+ O valor líquido tem um terceiro oculto camada, `Hid3`, que é totalmente conectado aos segunda camada oculta, `Conv2`.
+ A camada de saída `Digit`, está ligado apenas à terceira camada oculta, `Hid3`. A palavra-chave `all` indica que a camada de saída é totalmente conectada aos `Hid3`.
+ Arity do convolution é a terceira: o comprimento das tuplas `InputShape`, `KernelShape`, `Stride, and `partilha ".
+ O número de pesos por kernel é `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`. Ou `26 * 50 = 1300`.
+ Pode calcular os nós em cada camada oculta da seguinte forma:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ O número total de nós pode ser calculado utilizando a dimensionalidade declarada da camada, [50, 5, 5], da seguinte forma: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Uma vez `Sharing[d]` for False apenas para `d == 0`, o número de kernels é `MapCount * NodeCount\[0] = 10 * 5 = 50`.

## <a name="acknowledgements"></a>Confirmações

O idioma do Net # para personalizar a arquitetura de redes neurais foi desenvolvido na Microsoft por Shon Katzenberger (Arquiteto, Machine Learning) e Alexey Kamenev (engenheiro de Software, Microsoft Research). É utilizada internamente para projetos e aplicações que vão desde a deteção de imagem, para análise de texto machine learning. Para obter mais informações, consulte [redes Neurais para no Azure Machine Learning studio - introdução ao Net #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
