---
title: Aplicar Operação Matemática
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de operação de matemática aplicada no Azure Machine Learning para aplicar uma operação matemática aos valores das colunas num oleoduto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 3b2925a532d722598ccf16c001c9e2591aed1f2b
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584995"
---
# <a name="apply-math-operation"></a>Aplicar Operação Matemática

Este artigo descreve um módulo de Azure Machine Learning designer.

Utilize a Operação Matemática Aplicada para criar cálculos que são aplicados a colunas numéricas no conjunto de dados de entrada. 

As operações matemáticas incluem funções aritméticas, funções trigonométricas, funções de arredondamento e funções especiais usadas na ciência dos dados, tais como funções gama e erro.  

Depois de definir uma operação e executar o pipeline, os valores são adicionados ao seu conjunto de dados. Dependendo da configuração do módulo, pode:

+ Anexar os resultados ao seu conjunto de dados (útil ao verificar o resultado da operação).
+ Substitua os valores das colunas pelos novos valores calculados.
+ Gere uma nova coluna para obter resultados e não mostrar os dados originais. 

Procure a operação que precisa nestas categorias:  

- [Básica](#basic-math-operations)  
  
     As funções na categoria **Basic** podem ser utilizadas para manipular um único valor ou coluna de valores. Por exemplo, pode obter o valor absoluto de todos os números numa coluna, ou calcular a raiz quadrada de cada valor numa coluna.  
  
-   [Comparar](#comparison-operations)  
  
      As funções na categoria **Compare** são todas utilizadas para comparação: pode fazer uma comparação em pares dos valores em duas colunas, ou pode comparar cada valor numa coluna com uma constante especificada. Por exemplo, pode comparar colunas para determinar se os valores eram os mesmos em dois conjuntos de dados. Ou pode usar uma constante, como um valor máximo permitido, para descobrir os forasteiros numa coluna numérica.  
  
-   [Operações](#arithmetic-operations)  
  
     A categoria **Operações** inclui funções matemáticas básicas: adição, subtração, multiplicação e divisão. Pode trabalhar com colunas ou constantes. Por exemplo, pode adicionar o valor da Coluna A ao valor da Coluna B. Ou, pode subtrair uma constante, como uma média previamente calculada, de cada valor na Coluna A.  
  
-   [Arredondamento](#rounding-operations)  
  
     A categoria **arredondamento** inclui uma variedade de funções para a realização de operações como arredondamento, teto, piso e truncação a vários níveis de precisão. Pode especificar o nível de precisão tanto para os números decimais como para os números inteiros.  
  
-   [Especial](#special-math-functions)  
  
     A categoria **Especial** inclui funções matemáticas que são especialmente utilizadas na ciência dos dados, tais como integrais elípticos e a função de erro gaussiano.  
  
-   [Trigonométrico](#trigonometric-functions)  
  
     A categoria **trigonométrica** inclui todas as funções trigonométricas padrão. Por exemplo, pode converter radians em graus, ou funções de cálculo como tangente em radiais ou graus.
     Estas funções são insóplias, o que significa que tomam uma única coluna de valores como entrada, aplicam a função trigonométrica e devolvem uma coluna de valores como resultado. Certifique-se de que a coluna de entrada é o tipo adequado e contém o tipo correto de valores para o funcionamento especificado.   

## <a name="how-to-configure-apply-math-operation"></a>Como configurar aplicar operação matemática  

O módulo **de operação de matemática aplicada** requer um conjunto de dados que contenha pelo menos uma coluna contendo apenas números. Os números podem ser discretos ou contínuos, mas devem ser de um tipo de dado numérico, não de uma corda.

Pode aplicar a mesma operação a várias colunas numéricas, mas todas as colunas devem estar no mesmo conjunto de dados. 

Cada instância deste módulo pode efetuar apenas um tipo de operação de cada vez. Para realizar operações matemáticas complexas, poderá ser necessário acorrentar várias instâncias do módulo **de operação de matemática aplicada.**  
  
1.  Adicione o módulo **de operação de matemática ao** seu oleoduto.

1. Ligue um conjunto de dados que contém pelo menos uma coluna numérica.  

1.  Selecione uma ou mais colunas de origem para efetuar o cálculo.   
  
    - Qualquer coluna que escolha deve ser um tipo de dado numérico. 
    - O leque de dados deve ser válido para a operação matemática selecionada. Caso contrário, pode ocorrer um erro ou um resultado naN (não um número). Por exemplo, Ln(-1.0) é uma operação inválida e resulta num valor de `NaN` .
  
1.  Selecione **Categoria** para selecionar o **tipo** de operação matemática a realizar.
    
1. Escolha uma operação específica da lista desta categoria.
  
1.  Desa estalhe os parâmetros adicionais exigidos por cada tipo de operação.  
  
1.  Utilize a opção **modo saída** para indicar como pretende que a operação de matemática seja gerada: 

    - **Apêndice**. Todas as colunas utilizadas como entradas estão incluídas no conjunto de dados de saída, além de uma coluna adicional que contém os resultados da operação matemática.
    - **Em lugar.** Os valores nas colunas utilizadas como entradas são substituídos pelos novos valores calculados. 
    - **Resultado Só.** Uma única coluna é devolvida contendo os resultados da operação matemática.
  
1.  Envie o oleoduto.  
  
## <a name="results"></a>Resultados

Se gerar os resultados utilizando as opções **Append** ou **ResultOnly,** as rubricas de coluna do conjunto de dados devolvidos indicam a operação e as colunas utilizadas. Por exemplo, se compararmos duas colunas utilizando o operador **Equals,** os resultados seriam assim:  
  
-   **Igual (Col2_Col1)**, indicando que testou o Col2 contra o Col1.  
-   **É igual (Col2_$10)**, indicando que comparou a coluna 2 com a constante 10.  

Mesmo que utilize a opção **In place,** os dados de origem não são apagados ou alterados; a coluna no conjunto de dados original ainda está disponível no designer. Para visualizar os dados originais, pode ligar o módulo [Add Columns](add-columns.md) e juni-os à saída da **Operação Matemática Aplicada.**  
    
## <a name="basic-math-operations"></a>Operações matemáticas básicas 

As funções na categoria **Basic** geralmente tiram um único valor de uma coluna, executam a operação predefinida e devolvem um único valor. Para algumas funções, pode especificar uma constante ou uma coluna definida como um segundo argumento.  
  
 A Azure Machine Learning suporta as seguintes funções na categoria **Básico:**  

### <a name="abs"></a>Abs

Devolve o valor absoluto das colunas selecionadas.  
  
### <a name="atan2"></a>Atan2

Devolve uma tangente inversa de quatro quadrantes.  

Selecione as colunas que contêm as coordenadas de ponto. Para o segundo argumento, que corresponde à coordenada x, também pode especificar uma constante.  

Corresponde à função ATAN2 no MATLAB.  

### <a name="conj"></a>Rio Conj

Devolve o conjugado para os valores na coluna selecionada.  

### <a name="cuberoot"></a>CubeRoot

Calcula a raiz do cubo para os valores na coluna selecionada.  

### <a name="doublefactorial"></a>DuploFactorial  
 Calcula o duplo fatorial para valores na coluna selecionada. O duplo fatorial é uma extensão da função fatorial normal, e é denotado como `x!!` .  

### <a name="eps"></a>Eps

Devolve o tamanho da lacuna entre o valor atual e o próximo número de precisão dupla mais alto. Corresponde à função EPS no MATLAB.  
  
### <a name="exp"></a>Exp

Devoluções e elevado ao poder do valor na coluna selecionada. Esta função é a mesma da função Excel EXP.  

### <a name="exp2"></a>Exp2

Devolve a base-2 exponencial dos argumentos, resolvendo para y = x * 2<sup>t</sup> onde t é uma coluna de valores que contém expoentes.  

No  **conjunto coluna**, selecione a coluna que contém os valores exponente t.

Para **Exp2** pode especificar um segundo argumento x, que pode ser uma coluna de valores constante ou outra. No **segundo tipo de argumento,** indique se fornecerá o multiplicador x como constante, ou um valor numa coluna.  

Por exemplo, se selecionar uma coluna com os valores {0,1,2,3,4,5} tanto para o multiplicador como para o expoente, a função retorna {0, 2, 8, 24, 64 160).  

### <a name="expminus1"></a>ExpMinus1 

Devolve o expoente negativo para valores na coluna selecionada.  

### <a name="factorial"></a>Fatorial
Devolve o fatorial para valores na coluna selecionada.  

### <a name="hypotenuse"></a>Hipotenusa
Calcula a hipotenusa para um triângulo em que o comprimento de um lado é especificado como uma coluna de valores, e o comprimento do segundo lado é especificado como uma constante ou como duas colunas.  

### <a name="ln"></a>Ln

Devolve o logaritmo natural aos valores na coluna selecionada.  

### <a name="lnplus1"></a>Rio LnPlus1

Devolve o logaritmo natural mais um para os valores na coluna selecionada.  

### <a name="log"></a>Registo

Devolve o registo dos valores na coluna selecionada, dada a base especificada.  

Pode especificar a base (o segundo argumento) como constante ou selecionando outra coluna de valores.  

### <a name="log10"></a>Log10

Devolve os valores base 10 logaritm para a coluna selecionada.  

### <a name="log2"></a>Log2

Devolve os valores base 2 logaritm para a coluna selecionada.  

### <a name="nthroot"></a>NthRoot
Devolve a raiz nº do valor, utilizando um n que especifica.  

Selecione as colunas para as quais pretende calcular a raiz, utilizando a opção **ColumnSet.**  

No **segundo tipo de argumento,** selecione outra coluna que contenha a raiz ou especifique uma constante para usar como raiz.  

Se o segundo argumento for uma coluna, cada valor na coluna é usado como o valor de n para a linha correspondente. Se o segundo argumento for uma constante, digite o valor para n na caixa de texto de **segundo argumento.**
### <a name="pow"></a>Pow

Calcula X elevado à potência de Y para cada um dos valores na coluna selecionada.  

Em primeiro lugar, selecione as colunas que contêm a **base**, que deve ser uma boia, utilizando a opção **ColumnSet.**  

No **segundo tipo de argumento,** selecione a coluna que contém o expoente, ou especifique uma constante para usar como expoente.  

Se o segundo argumento for uma coluna, cada valor na coluna é usado como expoente para a linha correspondente. Se o segundo argumento for uma constante, digite o valor do expoente na caixa de texto do **segundo argumento.**  

### <a name="sqrt"></a>Sqrt

Devolve a raiz quadrada dos valores na coluna selecionada.  

### <a name="sqrtpi"></a>SqrtPi

Por cada valor na coluna selecionada, multiplica o valor por pi e, em seguida, devolve a raiz quadrada do resultado.  

### <a name="square"></a>Square

Quadrados os valores na coluna selecionada.  

## <a name="comparison-operations"></a>Operações de comparação  

Utilize as funções de comparação no designer Azure Machine Learning sempre que precisar de testar dois conjuntos de valores uns contra os outros. Por exemplo, num oleoduto poderá ser necessário fazer estas operações de comparação:  

- Avalie uma coluna de modelo de pontuação de probabilidades contra um valor limiar.
- Determinar se dois conjuntos de resultados são os mesmos. Para cada linha que seja diferente, adicione uma bandeira FALSE que pode ser usada para posterior processamento ou filtragem.  

### <a name="equalto"></a>IgualTo

Devoluções Verdadeiras se os valores forem os mesmos.  

### <a name="greaterthan"></a>GreaterThan

Devoluções Verdadeiras se os valores no **conjunto coluna** forem superiores à constante especificada, ou superiores aos valores correspondentes na coluna de comparação.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

Devoluções Verdadeira se os valores no **conjunto coluna** forem superiores ou iguais à constante especificada, ou superiores ou iguais aos valores correspondentes na coluna de comparação.  

### <a name="lessthan"></a>LessThan

Devoluções Verdadeiras se os valores no **conjunto coluna** forem inferiores à constante especificada, ou inferior aos valores correspondentes na coluna de comparação.  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

Devoluções Verdadeira se os valores no **conjunto coluna** forem inferiores ou iguais à constante especificada, ou inferior ou igual aos valores correspondentes na coluna de comparação.  

### <a name="notequalto"></a>NotEqualTo

Devoluções Verdadeiras se os valores no **conjunto coluna** não forem iguais à coluna constante ou de comparação, e retornar falsos se forem iguais.  

### <a name="pairmax"></a>PairMax

Devolve o valor que é maior — o valor na **Coluna definido** ou o valor na coluna constante ou de comparação.  

### <a name="pairmin"></a>PairMin

Devolve o valor que é menor — o valor no **conjunto coluna** ou o valor na coluna constante ou de comparação  
  
##  <a name="arithmetic-operations"></a>Operações aritméticas   

Inclui as operações aritméticas básicas: adição e subtração, divisão e multiplicação.  Como a maioria das operações são binárias, exigindo dois números, primeiro escolhe-se a operação e depois escolhe-se a coluna ou os números para usar no primeiro e segundo argumentos.

A ordem de divisão e subtração são as seguintes: 
- Subtrai(Arg1_Arg2) = Arg1 - Arg 2
- Divide (Arg1_Arg2) = Arg1 / Arg 2

A tabela a seguir mostra alguns exemplos
Operação|Num1|Num2|Coluna de resultados|Valor do resultado|
----|----|----|----|----
|Adição|1|5|Adicionar (Num2_Num1)| 6|
|Multiplicação|1|5|Múltiplos(Num2_Num1)|5|
|Subtração|5|1|Subtrai-Num2_Num1)|4|
|Subtração|0|1|Subtrai-Num2_Num1)|-1|
|Divisão|5|1|Divide(Num2_Num1)|5|
|Divisão|1|0|Divide(Num2_Num1)|Infinity|

### <a name="add"></a>Adicionar

Especifique as colunas de origem utilizando o **conjunto de colunas** e adicione aos valores um número especificado no **segundo argumento**.  

Para adicionar os valores em duas colunas, escolha uma coluna ou colunas utilizando **o conjunto coluna**, e, em seguida, escolha uma segunda coluna utilizando o segundo **argumento**.  

### <a name="divide"></a>Dividir

Divide os valores na **Coluna definidos** por uma constante ou pelos valores da coluna definidos no **Segundo argumento**.  Por outras palavras, escolhe-se primeiro o divisor, e depois o dividendo. O valor de saída é o quociente.

### <a name="multiply"></a>Multiplicar

Multiplica os valores na **Coluna definidos** pelos valores constantes ou de coluna especificados.  

### <a name="subtract"></a>Subtrair

Especificar a coluna de valores a operar (a *minuend),* escolhendo uma coluna diferente, utilizando a opção **de conjunto de colunas.** Em seguida, especifique o número para subtrair (o *subtrahend)* utilizando a lista de desistência do **segundo argumento.** Pode escolher uma constante ou coluna de valores.

##  <a name="rounding-operations"></a>Operações de arredondamento 

O designer de Aprendizagem automática Azure suporta uma variedade de operações de arredondamento. Para muitas operações, deve especificar a quantidade de precisão a utilizar ao arredondar. Pode utilizar um nível de precisão estática, especificado como constante, ou pode aplicar um valor de precisão dinâmico obtido a partir de uma coluna de valores.  

- Se utilizar uma constante, desajei o **Tipo de Precisão** a **Constante** e, em seguida, digite o número de dígitos como um número inteiro na caixa de texto **de precisão constante.** Se escrever um não-inteiro, o módulo não levanta um erro, mas os resultados podem ser inesperados.  

- Para utilizar um valor de precisão diferente para cada linha no conjunto de dados, defina **o Tipo de Precisão** para **o ColumnSet** e, em seguida, escolha a coluna que contém valores de precisão adequados.  

### <a name="ceiling"></a>Ceiling

Devolve o teto para os valores definidos na **Coluna**.  

### <a name="ceilingpower2"></a>TetoPower2

Devolve o teto ao quadrado para os valores definidos na **Coluna**.  

### <a name="floor"></a>Floor

Devolve o piso para os valores definidos na **Coluna,** à precisão especificada.  

### <a name="mod"></a>Mod

Devolve a parte fracionada dos valores definidos na **Coluna,** à precisão especificada.  

### <a name="quotient"></a>Quociente

Devolve a parte fracionada dos valores definidos na **Coluna,** à precisão especificada.  

### <a name="remainder"></a>Resto

Devolve o restante para os valores definidos na **Coluna**.  

### <a name="rounddigits"></a>RoundDigits

Devolve os valores na **Coluna definida,** arredondado pela regra 4/5 para o número especificado de dígitos.  

### <a name="rounddown"></a>RoundDown

Devolve os valores na **Coluna definida,** arredondada para o número especificado de dígitos.  

### <a name="roundup"></a>RoundUp

Devolve os valores na **Coluna definida,** arredondada até ao número especificado de dígitos.  

### <a name="toeven"></a>Toeven

Devolve os valores em **Conjunto coluna,** arredondado para o número total mais próximo e par.  

### <a name="toodd"></a>ToOdd

Devolve os valores em **Conjunto coluna,** arredondado para o número completo e ímpar mais próximo.  

### <a name="truncate"></a>Truncar

Truncates os valores na **Coluna definidos** removendo todos os dígitos não permitidos pela precisão especificada.  
  
## <a name="special-math-functions"></a>Funções matemáticas especiais

Esta categoria inclui funções matemáticas especializadas frequentemente utilizadas na ciência dos dados. Salvo indicação em contrário, a função é desinteressada e devolve o cálculo especificado para cada valor na coluna ou colunas selecionadas.  

### <a name="beta"></a>Beta

Devolve o valor da função beta da Euler.  

### <a name="ellipticintegrale"></a>ElípticaIntegrale
Devolve o valor da integral elíptica incompleta.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

Devolve o valor da integral elíptica completa (K).  

### <a name="erf"></a>Erf

Devolve o valor da função erro.  

A função de erro (também chamada de função de erro de Gauss) é uma função especial da forma sigmoide que é usada com probabilidade para descrever a difusão.  

### <a name="erfc"></a>Erfc

Devolve o valor da função de erro complementar.  

`Erfc` é definido como 1 – erf(x).  

### <a name="erfscaled"></a>ErfScaled

Devolve o valor da função de erro em escala.  

A versão em escala da função de erro pode ser utilizada para evitar a subfluxo aritmético.  

### <a name="erfinverse"></a>ErfInverse

Devolve o valor da `erf` função inversa.  

### <a name="exponentialintegralein"></a>Integraleina Exponencial

Devolve o valor da integral exponencial Ei.  

### <a name="gamma"></a>Gama

Devolve o valor da função gama.  

### <a name="gammaln"></a>Rio GammaLn

Devolve o logaritmo natural da função gama.  

### <a name="gammaregularizedp"></a>GamaRegularizedP

Devolve o valor da função gama regularizada incompleta.  

Esta função requer um segundo argumento, que pode ser fornecido quer como uma constante ou uma coluna de valores.  

### <a name="gammaregularizedpinverse"></a>GamaRegularizedPInverse

Devolve o valor da função gama gama incompleta regularizada inversa.  

Esta função requer um segundo argumento, que pode ser fornecido quer como uma constante ou uma coluna de valores.  

### <a name="gammaregularizedq"></a>GamaRegularizedQ  

Devolve o valor da função gama regularizada incompleta.  

Esta função requer um segundo argumento, que pode ser fornecido quer como uma constante ou uma coluna de valores.  

### <a name="gammaregularizedqinverse"></a>GamaRegularizedQInverse

Devolve o valor da função gama gama regularizada generalizada inversa.

Esta função requer um segundo argumento, que pode ser fornecido quer como uma constante ou uma coluna de valores.  

### <a name="polygamma"></a>Poligamma

Devolve o valor da função poligamma.  

Esta função requer um segundo argumento, que pode ser fornecido quer como uma constante ou uma coluna de valores.

##  <a name="trigonometric-functions"></a>Funções trigonométricas 

Esta categoria iInclusa a maioria das funções trigonométricas e invertidas importantes. Todas as funções trigonométricas são insusimétricas e não requerem argumentos adicionais.  

### <a name="acos"></a>Acos

Calcula a arccosina para os valores da coluna.  

### <a name="acosdegree"></a>AcosDegree

Calcula a arccosina dos valores da coluna, em graus.  

### <a name="acosh"></a>Rio Acosh

Calcula a arccosina hiperbólica dos valores da coluna.  

### <a name="acot"></a>Acot

Calcula o arcotangent dos valores da coluna.  

### <a name="acotdegrees"></a>AcotDegrees

Calcula o arcotangent dos valores da coluna, em graus.  

### <a name="acoth"></a>Acoth

Calcula o arccotangent hiperbólico dos valores da coluna.  

### <a name="acsc"></a>Acsc

Calcula o arccosecant dos valores da coluna.  

### <a name="acscdegrees"></a>AcscDegrees

Calcula o arccosecant dos valores da coluna, em graus.  
### <a name="asec"></a>Rio Asec

Calcula o arcos dos valores da coluna.  

### <a name="asecdegrees"></a>AsecDegrees

Calcula o arcos dos valores da coluna, em graus.  

### <a name="asech"></a>Asech

Calcula o arco hiperbólico dos valores da coluna.  

### <a name="asin"></a>Asin

Calcula o arco dos valores da coluna.  

### <a name="asindegrees"></a>AsinDegrees

Calcula o arco dos valores da coluna, em graus.  

### <a name="asinh"></a>Asinh

Calcula o arco hiperbólico para os valores da coluna.  

### <a name="atan"></a>Atan

Calcula o arctangent dos valores da coluna.  

### <a name="atandegrees"></a>AtanDegrees

Calcula o arctangent dos valores da coluna, em graus.  

### <a name="atanh"></a>Rio Atanh

Calcula o arctangent hiperbólico dos valores da coluna.  

### <a name="cos"></a>Cos

Calcula o cosine dos valores da coluna.  

### <a name="cosdegrees"></a>Rio CosDegrees

Calcula o cosine para os valores da coluna, em graus.  

### <a name="cosh"></a>Cosh

Calcula o cosine hiperbólico para os valores da coluna.  

### <a name="cot"></a>Cot

Calcula o cotangent para os valores da coluna.  

### <a name="cotdegrees"></a>CotDegrees

Calcula o cotangent para os valores da coluna, em graus.  

### <a name="coth"></a>Rio Coth
Calcula o cotangento hiperbólico para os valores da coluna.  

### <a name="csc"></a>Csc

Calcula o cosecant para os valores da coluna.  

### <a name="cscdegrees"></a>CscDegrees

Calcula o cosecant para os valores da coluna, em graus.  

### <a name="csch"></a>Estação Csch

Calcula o cosecant hiperbólico para os valores da coluna.  

### <a name="degreestoradians"></a>GrausToRadianos

Converte graus em radianos.  

### <a name="sec"></a>Seg

Calcula o secant dos valores da coluna.  

### <a name="asecdegrees"></a>aSecDegrees

Calcula o secant para os valores da coluna, em graus.  

### <a name="asech"></a>aSech

Calcula o secant hiperbólico dos valores da coluna.  

### <a name="sign"></a>Assinar

Devolve o sinal dos valores da coluna.  

### <a name="sin"></a>Sin

Calcula o seno dos valores da coluna.  

### <a name="sinc"></a>Sinc

Calcula o valor sine-cosine dos valores da coluna.  

### <a name="sindegrees"></a>SinDegrees

Calcula o sine para os valores da coluna, em graus.  

### <a name="sinh"></a>Sinh

Calcula o seno hiperbólico dos valores da coluna.  

### <a name="tan"></a>Tan

Calcula a tangente dos valores da coluna.  

### <a name="tandegrees"></a>TanDegrees

Calcula a tangente para o argumento, em graus.  

### <a name="tanh"></a>Tanh

Calcula a tangente hiperbólica dos valores da coluna.  
  
## <a name="technical-notes"></a>Notas técnicas

Tenha cuidado ao selecionar mais de uma coluna como segundo operador. Os resultados são fáceis de entender se a operação é simples, como adicionar uma constante a todas as colunas. 

Assuma que o seu conjunto de dados tem várias colunas e adiciona o conjunto de dados a si próprio. Nos resultados, cada coluna é adicionada a si mesma, da seguinte forma:  
  
|Num1|Num2|Num3|Adicionar (Num1_Num1)|Adicionar (Num2_Num2)|Adicionar (Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Se precisar de realizar cálculos mais complexos, pode acorrentar várias instâncias da **Operação Matemática Aplicada**. Por exemplo, pode adicionar duas colunas utilizando uma instância de **Operação Matemática Aplicada**, e depois usar outra instância de **Apply Math Operation** para dividir a soma por uma constante para obter a média.  
  
Em alternativa, utilize um dos seguintes módulos para fazer todos os cálculos de uma só vez, utilizando o script SQL, R ou Python:
 
+ [Executar Script R](execute-r-script.md)
+ [Executar Script do Python](execute-python-script.md)
+ [Aplicar Transformação SQL](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
