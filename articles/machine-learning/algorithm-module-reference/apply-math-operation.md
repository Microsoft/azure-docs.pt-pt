---
title: Aplicar Operação Matemática
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de Operação Matemática Aplicada em Aprendizagem automática Azure para aplicar uma operação matemática aos valores das colunas num pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2b4d6939aa1db4b8321c792898ed421c0d16cc99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456748"
---
# <a name="apply-math-operation"></a>Aplicar Operação Matemática

Este artigo descreve um módulo de designer de Machine Learning Azure (pré-visualização).

Utilize a Operação Matemática aplicada para criar cálculos que sejam aplicados a colunas numéricas no conjunto de dados de entrada. 

As operações matemáticas apoiadas incluem funções aritméticas comuns, tais como multiplicação e divisão, funções trigonométricas, uma variedade de funções de arredondamento, e funções especiais usadas na ciência dos dados, tais como funções gama e erro.  

Depois de definir uma operação e executar o gasoduto, os valores são adicionados ao seu conjunto de dados. Dependendo da configuração do módulo, pode:

+ Anexar os resultados ao seu conjunto de dados. Isto é particularmente útil quando está a verificar o resultado da operação.
+ Substitua os valores das colunas por valores novos e calculados.
+ Gere uma nova coluna para resultados e não mostre os dados originais. 

Procure a operação que necessita nestas categorias:  

- [Básico](#basic-math-operations)  
  
     As funções na categoria **Basic** podem ser utilizadas para manipular um único valor ou coluna de valores. Por exemplo, você pode obter o valor absoluto de todos os números em uma coluna, ou calcular a raiz quadrada de cada valor em uma coluna.  
  
-   [Comparar](#comparison-operations)  
  
      As funções na categoria **Compare** são todas usadas para comparação: pode fazer uma comparação em pares dos valores em duas colunas, ou pode comparar cada valor numa coluna com uma constante especificada. Por exemplo, pode comparar colunas para determinar se os valores eram os mesmos em dois conjuntos de dados. Ou, pode usar uma constante, como um valor máximo permitido, para descobrir fora de uma coluna numérica.  
  
-   [Operações](#arithmetic-operations)  
  
     A categoria **Operações** inclui as funções matemáticas básicas: adição, subtração, multiplicação e divisão. Pode trabalhar com colunas ou constantes. Por exemplo, pode adicionar o valor na Coluna A ao valor da Coluna B. Ou, pode subtrair uma constante, como uma média previamente calculada, de cada valor na Coluna A.  
  
-   [Arredondamento](#rounding-operations)  
  
     A categoria **Rounding** inclui uma variedade de funções para a realização de operações como arredondamento, teto, chão e truncação a vários níveis de precisão. Pode especificar o nível de precisão tanto para números decimais como para números inteiros.  
  
-   [Especial](#special-math-functions)  
  
     A categoria **Especial** inclui funções matemáticas que são especialmente utilizadas na ciência dos dados, tais como os integrais elípticos e a função de erro gaussiano.  
  
-   [Trigonométrico](#trigonometric-functions)  
  
     A categoria **trigonométrica** inclui todas as funções trigonométricas padrão. Por exemplo, pode converter radianos em graus, ou calcular funções como tangentes em radianos ou graus.
     Estas funções são não-secundárias, o que significa que tomam uma única coluna de valores como entrada, aplicam a função trigonométrica e devolvem uma coluna de valores como resultado.  Por isso, tem de se certificar de que a coluna de entrada é do tipo adequado e contém o tipo certo de valores para o funcionamento especificado.   

## <a name="how-to-configure-apply-math-operation"></a>Como configurar a Operação Matemática  

O módulo **de operação de matemática aplicar** requer um conjunto de dados que contenha pelo menos uma coluna contendo apenas números. Os números podem ser discretos ou contínuos, mas devem ser de um tipo de dados numéricos, não de uma cadeia.

Pode aplicar a mesma operação a múltiplas colunas numéricas, mas todas as colunas devem estar no mesmo conjunto de dados. 

Cada instância deste módulo só pode realizar um tipo de funcionamento de cada vez. Para realizar operações matemáticas complexas, poderá ser necessário acorrentar várias instâncias do módulo **de Operação Matemática Aplicada.**  
  
1.  Adicione o módulo **de Operação Matemática Aplicar** ao seu oleoduto.

1. Ligue um conjunto de dados que contenha pelo menos uma coluna numérica.  

1.  Selecione uma ou mais colunas de origem sobre as quais efetuar o cálculo.   
  
    - Qualquer coluna que escolha deve ser um tipo de dados numéricos. 
    - O leque de dados deve ser válido para a operação matemática selecionada. Caso contrário, pode ocorrer um erro ou um resultado nan (não um número). Por exemplo, Ln (-1.0) é uma operação inválida e resulta num valor de `NaN`.
  
1.  Clique na **categoria** para selecionar o **tipo** de operação matemática para realizar.
    
1. Escolha uma operação específica da lista nessa categoria.
  
1.  Defina parâmetros adicionais exigidos por cada tipo de funcionamento.  
  
1.  Utilize a opção **modo saída** para indicar como pretende que a operação matemática seja gerada: 

    - **Apêndice**. Todas as colunas utilizadas como entradas estão incluídas no conjunto de dados de saída, mais uma coluna adicional é anexada que contém os resultados da operação matemática.
    - **No local.** Os valores nas colunas utilizadas como inputs são substituídos por novos valores calculados. 
    - **Resultados apenas**. Uma única coluna é devolvida contendo os resultados da operação matemática.
  
1.  Submeta o oleoduto.  
  
## <a name="results"></a>Resultados

Se gerar os resultados utilizando as opções **Apêndice** ou **ResultadoS,** as rubricas da coluna do conjunto de dados devolvidos indicam o funcionamento e as colunas utilizadas. Por exemplo, se comparar duas colunas usando o operador **Equals,** os resultados serão assim:  
  
-   **Igual a Col2_Col1,** indicando que testou Col2 contra Col1.  
-   **Iguala (Col2_$10)**, indicando que comparou a coluna 2 com a constante 10.  

Mesmo que utilize a opção **Inplace,** os dados de origem não são eliminados ou alterados; a coluna no conjunto de dados original ainda está disponível no designer. Para visualizar os dados originais, pode ligar o módulo [Adicionar Colunas](add-columns.md) e juntá-lo à saída da **Operação Matemática de Aplicação**.  
    
## <a name="basic-math-operations"></a>Operações matemáticas básicas 

As funções na categoria **Basic** geralmente têm um único valor de uma coluna, executam a operação predefinida e devolvem um único valor. Para algumas funções, pode especificar uma constante ou um conjunto de colunas como um segundo argumento.  
  
 O Azure Machine Learning suporta as seguintes funções na categoria **Basic:**  

### <a name="abs"></a>Abs

Devolve o valor absoluto das colunas selecionadas.  
  
### <a name="atan2"></a>Atan2

Devolve uma tangente inversa de quatro quadrantes.  

Selecione as colunas que contêm as coordenadas de ponto. Para o segundo argumento, que corresponde à coordenada x, também pode especificar uma constante.  

Corresponde à função ATAN2 no Matlab.  

### <a name="conj"></a>Rio Conj

Devolve o conjugado para os valores na coluna selecionada.  

### <a name="cuberoot"></a>Raiz de Cubo

Calcula a raiz do cubo para os valores na coluna selecionada.  

### <a name="doublefactorial"></a>DoubleFactorial  
 Calcula o fatorial duplo para valores na coluna selecionada. O fatorial duplo é uma extensão da função fatorial normal, e é denotado como x!!.  

### <a name="eps"></a>Eps

Devolve o tamanho do intervalo entre o valor atual e o número de dupla precisão mais elevado. Corresponde à função EPS no Matlab.  
  
### <a name="exp"></a>Exp

Devoluções e elevadas ao poder do valor na coluna selecionada. Isto é o mesmo que a função Excel EXP.  

### <a name="exp2"></a>Exp2

Devolve a base-2 exponencial dos argumentos, resolvendo para y = x * 2<sup>t</sup> onde t é uma coluna de valores contendo expoentes.  

No **conjunto coluna,** selecione a coluna que contém os valores expoentes t.

Para **exp2** pode especificar um segundo argumento x, que pode ser uma constante ou outra coluna de valores. No segundo tipo de **argumento,** indique se fornecerá o multiplicador x como uma constante, ou um valor numa coluna.  

Por exemplo, se selecionar uma {0,1,2,3,4,5} coluna com os valores tanto para o multiplicador como para o expoente, a função retorna {0, 2, 8, 24, 64 160).  

### <a name="expminus1"></a>ExpMinus1 

Devolve o expoente negativo para valores na coluna selecionada.  

### <a name="factorial"></a>Fatorial
Devolve o fatorial para valores na coluna selecionada.  

### <a name="hypotenuse"></a>Hipotenusa
Calcula a hipotenusa para um triângulo em que o comprimento de um lado é especificado como uma coluna de valores, e o comprimento do segundo lado é especificado como uma constante ou como duas colunas.  

### <a name="ln"></a>Ln

Devolve o logarithm natural para os valores na coluna selecionada.  

### <a name="lnplus1"></a>LnPlus1

Devolve o logarithm natural mais um para os valores na coluna selecionada.  

### <a name="log"></a>Registar

Devolve o registo dos valores na coluna selecionada, dada a base especificada.  

Pode especificar a base (o segundo argumento) quer como constante, quer selecionando outra coluna de valores.  

### <a name="log10"></a>Log10

Devolve os valores base 10 de logarithm para a coluna selecionada.  

### <a name="log2"></a>Log2

Devolve os valores base 2 logarithm para a coluna selecionada.  

### <a name="nthroot"></a>NthRoot
Devolve a raiz do valor, utilizando um n que especifica.  

Selecione as colunas para as quais pretende calcular a raiz utilizando a opção **ColumnSet.**  

No **segundo tipo de argumento,** selecione outra coluna que contenha a raiz ou especifique uma constante para usar como raiz.  

Se o segundo argumento for uma coluna, cada valor na coluna é utilizado como valor de n para a linha correspondente. Se o segundo argumento for uma constante, digite o valor para n na segunda caixa de texto de **argumento.**
### <a name="pow"></a>Pow

Calcula X elevado ao poder de Y para cada um dos valores na coluna selecionada.  

Em primeiro lugar, selecione as colunas que contêm a **base,** que deve ser uma boia, utilizando a opção **ColumnSet.**  

No **segundo tipo de argumento,** selecione a coluna que contém o expoente, ou especifique uma constante para usar como expoente.  

Se o segundo argumento for uma coluna, cada valor na coluna é utilizado como expoente para a linha correspondente. Se o segundo argumento for uma constante, digite o valor para o expoente na segunda caixa de texto de **argumento.**  

### <a name="sqrt"></a>Sqrt

Devolve a raiz quadrada dos valores na coluna selecionada.  

### <a name="sqrtpi"></a>SqrtPi

Para cada valor na coluna selecionada, multiplica o valor por pi e, em seguida, devolve a raiz quadrada do resultado.  

### <a name="square"></a>Square

Quadra os valores na coluna selecionada.  

## <a name="comparison-operations"></a>Operações de comparação  

Utilize as funções de comparação no designer de machine learning Azure sempre que precisar de testar dois conjuntos de valores uns contra os outros. Por exemplo, num oleoduto pode ser necessário fazer estas operações de comparação:  

- Avalie um modelo de pontuação de probabilidade contra um valor limiar.
- Determine se dois conjuntos de resultados são os mesmos. Para cada linha diferente, adicione uma bandeira FALSA que pode ser usada para posterior processamento ou filtragem.  

### <a name="equalto"></a>Igualado

Devoluções Verdadeiras se os valores forem os mesmos.  

### <a name="greaterthan"></a>GreaterThan

Devoluções Verdadeiras se os valores em **conjunto de Coluna** forem superiores à constante especificada, ou superiores aos valores correspondentes na coluna de comparação.  

### <a name="greaterthanorequalto"></a>MaiorthanorEqualTo

Devoluções Verdadeiras se os valores em **conjunto de Coluna** saem maiores ou iguais à constante especificada, ou superior ou igual aos valores correspondentes na coluna de comparação.  

### <a name="lessthan"></a>LessThan

Devoluções Verdadeiras se os valores em **conjunto de Coluna** forem inferiores à constante especificada, ou menos do que os valores correspondentes na coluna de comparação.  
  
### <a name="lessthanorequalto"></a>LessthanorEqualTo

Devoluções Verdadeiras se os valores em **conjunto de Coluna** forem inferiores ou iguais à constante especificada, ou inferior ou igual aos valores correspondentes na coluna de comparação.  

### <a name="notequalto"></a>Não Igualado

Devoluções Verdadeiras se os valores em **conjunto de Coluna** não forem iguais à coluna constante ou de comparação, e devoluções Falsas se forem iguais.  

### <a name="pairmax"></a>PairMax

Devolve o valor que é maior — o valor no **conjunto de Colunas** ou o valor na coluna constante ou de comparação.  

### <a name="pairmin"></a>PairMin

Devolve o valor menor — o valor no **conjunto de Colunas** ou o valor na coluna constante ou de comparação  
  
##  <a name="arithmetic-operations"></a>Operações aritméticas   

Inclui as operações aritméticas básicas: adição e subtração, divisão e multiplicação.  Como a maioria das operações são binárias, requerendo dois números, primeiro escolhe-se a operação e, em seguida, escolhe-se a coluna ou os números para usar no primeiro e segundo argumentos.

A ordem em que escolhe as colunas para divisão e subtração pode parecer contraintuitiva; no entanto, para facilitar a compreensão dos resultados, a rubrica da coluna fornece o nome de funcionamento e a ordem em que as colunas foram utilizadas.

Operação|Número 1|Nº 2|Coluna de resultados|Valor do resultado|
----|----|----|----|----
|Adição|1|5|Adicionar (Num2_Num1)| 4|
|Multiplicação|1|5|Múltiplos (Num2_Num1)|5|
|Subtração|1|5|Subtrair(Num2_Num1)|4|
|Subtração|0|1|Subtrair(Num2_Num1)|0|
|Divisão|1|5|Dividir(Num2_Num1)|5|
|Divisão|0|1|Dividir(Num2_Num1)|Infinity|

### <a name="add"></a>Adicionar

Especifique as colunas de origem utilizando o conjunto de **colunas**e, em seguida, adicione a esses valores um número especificado no **segundo argumento**.  

Para adicionar os valores em duas colunas, escolha uma coluna ou colunas utilizando o conjunto de **colunas,** e, em seguida, escolha uma segunda coluna utilizando o **segundo argumento**.  

### <a name="divide"></a>Dividir

Divide os valores em **Coluna definidos** por uma constante ou pelos valores da coluna definidos no **segundo argumento**.  Por outras palavras, escolhes o divisor primeiro, e depois o dividendo. O valor de saída é o quociente.

### <a name="multiply"></a>Multiplicar

Multiplica os valores em **Coluna definidos** pelos valores constantes ou colunas especificados.  

### <a name="subtract"></a>Subtrair

Especifique a coluna de valores a operar (o *minuend),* escolhendo uma coluna diferente, utilizando a opção conjunto de **colunas.** Em seguida, especifique o número para subtrair (o *subtrahend*) utilizando a lista de abandono de **argumentos de segundo argumento.** Pode escolher uma constante ou coluna de valores.

##  <a name="rounding-operations"></a>Operações de arredondamento 

O designer azure machine learning apoia uma variedade de operações de arredondamento. Para muitas operações, deve especificar a quantidade de precisão a utilizar ao arredondar. Pode utilizar um nível de precisão estática, especificado como uma constante, ou pode aplicar um valor de precisão dinâmico obtido a partir de uma coluna de valores.  

- Se utilizar uma constante, coloque o **Tipo de Precisão** em **Constante** e, em seguida, digite o número de dígitos como um inteiro na caixa de texto **De Precisão Constante.** Se escrever um não-inteiro, o módulo não levanta um erro, mas os resultados podem ser inesperados.  

- Para utilizar um valor de precisão diferente para cada linha no seu conjunto de dados, desloque o **Tipo de Precisão** para **o ColumnSet**e, em seguida, escolha a coluna que contenha valores de precisão adequados.  

### <a name="ceiling"></a>Ceiling

Devolve o teto dos valores em **conjunto**coluna .  

### <a name="ceilingpower2"></a>TetoPower2

Devolve o teto quadrado para os valores em **conjunto coluna**.  

### <a name="floor"></a>Floor

Devolve o piso para os valores em **conjunto coluna,** à precisão especificada.  

### <a name="mod"></a>Mod

Devolve a parte fracionada dos valores em **conjunto**coluna, à precisão especificada.  

### <a name="quotient"></a>Quociente

Devolve a parte fracionada dos valores em **conjunto**coluna, à precisão especificada.  

### <a name="remainder"></a>Restante

Devolve o restante para os valores em **conjunto coluna**.  

### <a name="rounddigits"></a>RoundDigits

Devolve os valores em **conjunto de Colunas,** arredondados pela regra 4/5 ao número especificado de dígitos.  

### <a name="rounddown"></a>RoundDown

Devolve os valores em **conjunto de Colunas,** arredondados para o número especificado de dígitos.  

### <a name="roundup"></a>RoundUp

Devolve os valores em **conjunto de Colunas,** arredondados até ao número especificado de dígitos.  

### <a name="toeven"></a>Toeven

Devolve os valores em **conjunto coluna,** arredondado para o número inteiro e uniforme mais próximo.  

### <a name="toodd"></a>Toodd

Devolve os valores em **conjunto coluna,** arredondado para o número inteiro e ímpar mais próximo.  

### <a name="truncate"></a>Truncar

Trunca os valores em **Coluna definidos** removendo todos os dígitos não permitidos pela precisão especificada.  
  
## <a name="special-math-functions"></a>Funções matemáticas especiais

Esta categoria inclui funções matemáticas especializadas frequentemente utilizadas na ciência dos dados. Salvo indicação em contrário, a função é desabitada e devolve o cálculo especificado para cada valor na coluna ou colunas selecionadas.  

### <a name="beta"></a>Beta

Devolve o valor da função beta de Euler.  

### <a name="ellipticintegrale"></a>ElípticaIntegrale
Devolve o valor da integral elíptica incompleta.  
  

### <a name="ellipticintegralk"></a>ElípticoIntegralK

Devolve o valor da integral elíptica completa (K).  

### <a name="erf"></a>Erf

Devolve o valor da função de erro.  

A função de erro (também chamada função de erro de Gauss) é uma função especial da forma sigmoide que é usada com probabilidade para descrever a difusão.  

### <a name="erfc"></a>Erfc

Devolve o valor da função de erro complementar.  

Erfc é definido como 1 – erf(x).  

### <a name="erfscaled"></a>ErfScaled

Devolve o valor da função de erro dimensionada.  

A versão em escala da função de erro pode ser utilizada para evitar o subfluxo aritmético.  

### <a name="erfinverse"></a>ErfInverse

Devolve o valor da função erf inversa.  

### <a name="exponentialintegralein"></a>ExponencialIntegralein

Devolve o valor da ei integral exponencial.  

### <a name="gamma"></a>Gama

Devolve o valor da função gama.  

### <a name="gammaln"></a>GammaLn

Devolve o logarithm natural da função gama.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Devolve o valor da função gama incompleta regularizada.  

Esta função requer um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Devolve o valor da função gama incompleta inversa.  

Esta função requer um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

Devolve o valor da função gama incompleta regularizada.  

Esta função requer um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Devolve o valor da função gama incompleta generalizada e incompleta.

Esta função requer um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.  

### <a name="polygamma"></a>PoliGamma

Devolve o valor da função poligâmica.  

Esta função requer um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.

##  <a name="trigonometric-functions"></a>Funções trigonométricas 

Esta categoria iInclui a maioria das importantes funções trigonométricas e inversas trigonométricas. Todas as funções trigonométricas são não-secundárias e não requerem argumentos adicionais.  

### <a name="acos"></a>Acos

Calcula a arccosina para os valores da coluna.  

### <a name="acosdegree"></a>AcosDegree

Calcula a arccosina dos valores da coluna, em graus.  

### <a name="acosh"></a>Rio Acosh

Calcula a arccosina hiperbólica dos valores da coluna.  

### <a name="acot"></a>Acot

Calcula o arquitangente dos valores da coluna.  

### <a name="acotdegrees"></a>AcotDegrees

Calcula o arquitangente dos valores da coluna, em graus.  

### <a name="acoth"></a>Rio Acoth

Calcula o arccotangent hiperbólico dos valores da coluna.  

### <a name="acsc"></a>Acsc

Calcula o arccosecante dos valores da coluna.  

### <a name="acscdegrees"></a>AcscDegrees

Calcula o arccosecante dos valores da coluna, em graus.  
### <a name="asec"></a>Asec

Calcula o arcsecante dos valores da coluna.  

### <a name="asecdegrees"></a>AsecDegrees

Calcula o arcsecante dos valores da coluna, em graus.  

### <a name="asech"></a>Rio Asech

Calcula o arcsecante hiperbólico dos valores da coluna.  

### <a name="asin"></a>Asin

Calcula o arco dos valores da coluna.  

### <a name="asindegrees"></a>AsinDegrees

Calcula o arco dos valores da coluna, em graus.  

### <a name="asinh"></a>Asinh

Calcula o arco hiperbólico para os valores da coluna.  

### <a name="atan"></a>Atan

Calcula o arquitangente dos valores da coluna.  

### <a name="atandegrees"></a>AtanDegrees

Calcula o arctangente dos valores da coluna, em graus.  

### <a name="atanh"></a>Atanh

Calcula o arctangente hiperbólico dos valores da coluna.  

### <a name="cos"></a>Cos

Calcula o cosseno dos valores da coluna.  

### <a name="cosdegrees"></a>CosDegrees

Calcula o cosseno para os valores da coluna, em graus.  

### <a name="cosh"></a>Cosh

Calcula a cosina hiperbólica para os valores da coluna.  

### <a name="cot"></a>Cot

Calcula a cotangente para os valores da coluna.  

### <a name="cotdegrees"></a>CotDegrees

Calcula a cotangente para os valores da coluna, em graus.  

### <a name="coth"></a>Coth
Calcula a cotangente hiperbólica para os valores da coluna.  

### <a name="csc"></a>CSC

Calcula o cosecante para os valores da coluna.  

### <a name="cscdegrees"></a>CscDegrees

Calcula o cosecante para os valores da coluna, em graus.  

### <a name="csch"></a>Rio Csch

Calcula o cosecante hiperbólico para os valores da coluna.  

### <a name="degreestoradians"></a>GrausToRadians

Converte graus em radianos.  

### <a name="sec"></a>Sec

Calcula o secante dos valores da coluna.  

### <a name="asecdegrees"></a>aSecDegrees

Calcula o secante para os valores da coluna, em graus.  

### <a name="asech"></a>aSech

Calcula o secante hiperbólico dos valores da coluna.  

### <a name="sign"></a>Assinar

Devolve o sinal dos valores da coluna.  

### <a name="sin"></a>Sin

Calcula o seno dos valores da coluna.  

### <a name="sinc"></a>Sinc

Calcula o valor sine-cosseno dos valores da coluna.  

### <a name="sindegrees"></a>SinDegrees

Calcula o seno para os valores da coluna, em graus.  

### <a name="sinh"></a>Sinh

Calcula o seno hiperbólico dos valores da coluna.  

### <a name="tan"></a>Tan

Calcula a tangente dos valores da coluna.  

### <a name="tandegrees"></a>TanDegrees

Calcula a tangente para o argumento, em graus.  

### <a name="tanh"></a>Tanh

Calcula a tangente hiperbólica dos valores da coluna.  
  
## <a name="technical-notes"></a>Notas técnicas

Tenha cuidado quando selecionar mais de uma coluna como segundo operador. Os resultados são fáceis de entender se a operação é simples, como por exemplo adicionar uma constante a todas as colunas. 

Assuma que o seu conjunto de dados tem várias colunas, e adiciona o conjunto de dados para si mesmo. Nos resultados, cada coluna é adicionada a si mesma, da seguinte forma:  
  
|Número 1|Nº 2|Num3|Adicionar (Num1_Num1)|Adicionar (Num2_Num2)|Adicionar (Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Se precisar de realizar cálculos mais complexos, pode acorrentar várias instâncias de **Aplicação da Operação Matemática**. Por exemplo, pode adicionar duas colunas utilizando uma instância de **Aplicação da Operação Matemática**, e depois utilizar outra instância de **Aplicação da Operação Matemática** para dividir a soma por uma constante para obter a média.  
  
Em alternativa, utilize um dos seguintes módulos para fazer todos os cálculos ao mesmo tempo, utilizando o script SQL, R ou Python:
 
+ [Executar Script R](execute-r-script.md)
+ [Executar Script do Python](execute-python-script.md)
+ [Aplicar Transformação SQL](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
