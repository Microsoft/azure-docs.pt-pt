---
title: Introdução ao R
titleSuffix: Azure Machine Learning Studio
description: Utilize este tutorial de programação R para começar a utilizar a linguagem R com o Azure Machine Learning Studio para criar uma solução de previsão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 307636b019b53c8457c65261824adbf8e52ca0a9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57875520"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio"></a>Introdução ao R linguagem no Azure Machine Learning Studio de programação

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Introdução

Este tutorial ajuda-o a começar a expandir o Azure Machine Learning Studio através da utilização da linguagem de programação R. Siga este tutorial de programação R para criar, testar e executar o código de R no Studio. À medida que trabalha por meio do tutorial, irá criar uma solução completa de previsão usando a linguagem R no Studio.  

Microsoft Azure Machine Learning Studio contém muitos módulos de manipulação de aprendizagem e dados máquina poderosas. A poderosa linguagem de R já foi amplamente descrita a língua franca da análise. Felizmente, a manipulação de dados e análises no Studio pode ser estendida com o R. Esta combinação fornece a escalabilidade e a facilidade de implantação do Studio com a flexibilidade e a análise profunda de R.

### <a name="forecasting-and-the-dataset"></a>Previsão e o conjunto de dados

Previsão é um método de análise amplamente empregado e bastante útil. Comum utiliza o intervalo de previsão de vendas de itens sazonais, determinar os níveis de estoque ideal, para prever macroeconomic variáveis. Previsão é geralmente feita com modelos de série de tempo.

Dados de séries temporais são os dados em que os valores têm um índice de tempo. O índice de tempo pode ser normal, por exemplo, todos os meses ou a cada minuto, ou irregular. Um modelo de série de tempo é baseado em dados de séries de tempo. A linguagem de programação R contém uma estrutura flexível e a análise abrangente para os dados de séries de tempo.

Neste guia, será trabalhar com a produção de dairy Califórnia e dados de preços. Estes dados incluem informações mensais sobre a produção de vários produtos dairy e o preço de fat milk, uma mercadoria de benchmark.

Os dados utilizados neste artigo, juntamente com R scripts, podem ser transferidos a partir [MachineLearningSamples-blocos de notas/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Os dados no ficheiro `cadairydata.csv` foi originalmente sintetizadas das informações disponíveis da Universidade do Wisconsin na [ https://dairymarkets.com ](https://dairymarkets.com).

### <a name="organization"></a>Organização

Que será avançamos vários passos à medida que aprende a criar, testar e executar o código de R de manipulação de dados e análises no ambiente do Azure Machine Learning Studio.  

* Primeiro, vamos explorar as noções básicas de utilizar a linguagem R no ambiente do Azure Machine Learning Studio.
* Em seguida, vamos avançar para discutindo diversos aspectos de e/s de dados, o código R e gráficos no ambiente do Azure Machine Learning Studio.
* Em seguida, podemos irá construir a primeira parte da nossa solução de previsão através da criação de código de limpeza de dados e transformação.
* Com os nossos dados preparados, iremos efetuar uma análise das correlações entre vários das variáveis no nosso conjunto de dados.
* Por fim, vamos criar um modelo de previsão de série de tempo sazonais para produção milk.

## <a id="mlstudio"></a>Interagir com a linguagem R no Machine Learning Studio

Esta secção orienta-o por algumas noções básicas de interação com a linguagem de programação R no ambiente de Machine Learning Studio. A linguagem R fornece uma ferramenta poderosa para criar análises personalizados e módulos de manipulação de dados dentro do ambiente do Azure Machine Learning Studio.

Irá utilizar o r Studio para desenvolver, testar e depurar o código de R em pequena escala. Esse código é, em seguida, operações de cortar e colar para um [executar Script R] [ execute-r-script] módulo no Machine Learning Studio pronto para ser executado.  

### <a name="the-execute-r-script-module"></a>O módulo de executar o Script de R

No Machine Learning Studio, os R scripts são executados dentro do [executar Script R] [ execute-r-script] módulo. Um exemplo do [executar Script R] [ execute-r-script] módulo no Machine Learning Studio é mostrado na figura 1.

 ![Linguagem de programação R: O módulo de executar o Script de R selecionado no Machine Learning Studio](./media/r-quickstart/fig1.png)

*Figura 1. O ambiente de Machine Learning Studio, que mostra o módulo de executar o Script de R selecionado.*

Consultando a figura 1, vamos examinar algumas das principais partes do ambiente de Machine Learning Studio para trabalhar com o [executar Script R] [ execute-r-script] módulo.

* Os módulos na experimentação são apresentados no painel central.
* A parte superior do painel da direita contém uma janela para ver e editar os scripts R.  
* A parte inferior do painel direito mostra algumas propriedades do [executar Script R][execute-r-script]. Pode ver os registos de erros e de saída ao selecionar os pontos apropriados neste painel.

É claro, abordaremos os [executar Script R] [ execute-r-script] mais detalhadamente no resto deste artigo.

Ao trabalhar com funções de R complexas, recomendo que edita, testar e depurar no RStudio. Tal como acontece com qualquer desenvolvimento de software, estenda o código de forma incremental e testá-la no pequeno casos de teste simples. Em seguida, corte e cole as suas funções na janela de script do R do [executar Script R] [ execute-r-script] módulo. Esta abordagem permite-lhe tirar partido do ambiente de desenvolvimento integrado (IDE) de r Studio e o poder do Azure Machine Learning Studio.  

#### <a name="execute-r-code"></a>Executar o código de R

Qualquer código de R no [executar Script R] [ execute-r-script] módulo será executado quando executar a experimentação, selecionando o **executar** botão. Quando tiver concluído a execução, uma marca de verificação aparecerá no [executar Script R] [ execute-r-script] ícone.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Codificação de R defensiva para o Azure Machine Learning

Se estiver a desenvolver o código de R para, digamos, um serviço web com o Azure Machine Learning Studio, deve planejar, definitivamente, como o seu código será lidar com uma entrada de dados inesperados e exceções. Para manter a clareza, eu não incluí parecem muito com a verificação ou na maioria dos exemplos de código mostrados de manipulação de exceção. No entanto, à medida que avança vou fornecer vários exemplos de funções ao utilizar a capacidade de processamento de exceção do R.  

Se precisar de um tratamento mais completo de manipulação de exceção do R, recomendo que leia as secções aplicáveis do livro por Wickham listado abaixo na [leitura adicional](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio"></a>Depurar e testar o R no Machine Learning Studio

Para reiterar, é recomendável testar e depurar seu código de R em pequena escala no RStudio. No entanto, existem casos em que precisará rastrear problemas de código de R no [executar Script R] [ execute-r-script] em si. Além disso, é recomendável verificar os resultados no Machine Learning Studio.

Saída da execução do seu código de R e na plataforma do Azure Machine Learning Studio encontra-se principalmente em output.log. Algumas informações adicionais serão vistas no error.log.  

Se ocorrer um erro durante a execução de seu código de R no Machine Learning Studio, o primeiro método de ação deve ser examinar error.log. Este ficheiro pode conter mensagens de erro útil para ajudar a compreender e corrigir o erro. Para ver error.log, selecione **registo de erros do vista** sobre o **painel de propriedades** para o [executar Script do R] [ execute-r-script] que contém o erro.

Por exemplo, executei o seguinte código de R, com uma variável y não definido num [executar Script R] [ execute-r-script] módulo:

```R
x <- 1.0
z <- x + y
```

Esse código não consegue executar, resultando numa condição de erro. Selecionando **registo de erros do vista** sobre o **painel de propriedades** produz a exibição mostrada na figura 2.

  ![Mensagem de erro pop-up](./media/r-quickstart/fig2.png)

*Figura 2. Mensagem de erro pop-up.*

Parece que temos de procurar em output.log para ver a mensagem de erro de R. Selecione o [executar Script R] [ execute-r-script] e, em seguida, selecione o **ver output.log** item no **painel de propriedades** à direita. Abre uma nova janela do browser e vejo o seguinte.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Esta mensagem de erro contém sem surpresas e identifique claramente o problema.

Para inspecionar o valor de qualquer objeto no R, é possível imprimir esses valores para o ficheiro de output.log. As regras para examinar os valores de objeto são essencialmente os mesmos que de uma sessão de R interativa. Por exemplo, se digitar um nome de variável numa linha, o valor do objeto serão impressos para o ficheiro de output.log.  

#### <a name="packages-in-machine-learning-studio"></a>Pacotes no Machine Learning Studio

Studio vem com mais de 350 pacotes de idioma do R pré-instalados. Pode utilizar o seguinte código no [executar Script R] [ execute-r-script] módulo para obter uma lista dos pacotes pré-instalados.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Se não sabe a última linha desse código no momento, continue a ler. O restante deste artigo, extensivamente Abordaremos através da linguagem R no ambiente do Studio.

### <a name="introduction-to-rstudio"></a>Introdução ao r Studio

R Studio é um IDE amplamente usado para R. Irá utilizar o r Studio para editar, testar e depurar algum código R utilizado neste guia. Assim que o código de R é testada e pronto, pode simplesmente recortar e colar no editor de RStudio para um Machine Learning Studio [executar Script R] [ execute-r-script] módulo.  

Se não tiver a linguagem de programação R instalada no seu computador desktop, recomendo que fazer isso agora. Downloads gratuitos da linguagem R de código-fonte aberto estão disponíveis no abrangente R Archive rede (CRAN) em [ https://www.r-project.org/ ](https://www.r-project.org/). Downloads estão disponíveis para Windows, Macos e Linux/UNIX. Escolha um espelho próximos e siga as instruções de download. Além disso, CRAN contém uma grande quantidade de pacotes de manipulação de dados e análise úteis.

Se estiver familiarizado com o r Studio, deve transferir e instalar a versão de área de trabalho. Pode encontrar o RStudio downloads para Windows, Macos e Linux/UNIX em http://www.rstudio.com/products/RStudio/. Siga as orientações fornecidas para instalar o RStudio no seu computador desktop.  

Uma introdução tutorial rstudio está disponível em [usando o IDE de r Studio](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Posso fornecer algumas informações adicionais sobre como utilizar o r Studio na [guia de documentação do RStudio](#appendixa) abaixo.  

## <a id="scriptmodule"></a>Obter entrada e saída, o módulo de executar o Script de R

Nesta seção, abordaremos como obter dados para dentro e fora do [executar Script R] [ execute-r-script] módulo. Vamos rever como lidar com vários tipos de dados de leitura para dentro e fora do [executar Script R] [ execute-r-script] módulo.

O código completo para esta secção está no [MachineLearningSamples-blocos de notas/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data-in-machine-learning-studio"></a>Carregar e verificar os dados no Machine Learning Studio

#### <a id="loading"></a>Carregar o conjunto de dados

Começamos por carregar os **csdairydata.csv** ficheiro para o Azure Machine Learning Studio.

1. Inicie o seu ambiente do Azure Machine Learning Studio.
1. Selecione **+ novo** na parte inferior esquerda da sua tela e selecione **conjunto de dados**.
1. Selecione **do ficheiro Local**e, em seguida **procurar** para selecionar o ficheiro.
1. Certifique-se de que selecionou **ficheiro CSV genérico com cabeçalho (. csv)** como o tipo do conjunto de dados.
1. Selecione a marca de verificação.
1. Depois do conjunto de dados ter sido carregado, deverá ver o novo conjunto de dados ao selecionar o **conjuntos de dados** separador.  

#### <a name="create-an-experiment"></a>Criar uma experimentação

Agora que temos alguns dados no Machine Learning Studio, é necessário criar uma experimentação para fazer a análise.  

1. Selecione **+ novo** em inferior à esquerda e selecione **experimentação**, em seguida, **experimentação em branco**.
1. Pode nomear sua experimentação ao selecionar e modificar, o **experimentação criado em...**  título na parte superior da página. Por exemplo, alterando-a para **AC em Dairy análise**.
1. No lado esquerdo da página de experimentação, expanda **conjuntos de dados guardado**e, em seguida **conjuntos de dados de meu**. Deverá ver o **cadairydata.csv** que carregado anteriormente.
1. Arraste e largue os **conjunto de dados de csdairydata.csv** para a experimentação.
1. Na **itens de experimentação de pesquisa** caixa na parte superior do painel esquerdo, tipo [executar Script do R][execute-r-script]. Verá o módulo de aparecer na lista de pesquisa.
1. Arraste e largue os [executar Script R] [ execute-r-script] módulo no seu palete.  
1. Ligue a saída do **conjunto de dados de csdairydata.csv** à entrada da esquerda (**Dataset1**) da [executar Script do R][execute-r-script].
1. **Não se esqueça de selecionar "Guardar"!**  

Neste momento sua experimentação deve ter um aspeto semelhante à figura 3.

![A análise de em Dairy de AC de experimentação com o conjunto de dados e o módulo de executar o Script de R](./media/r-quickstart/fig3.png)

*Figura 3. Experimente a análise de em Dairy de AC com o conjunto de dados e o módulo de executar o Script de R.*

#### <a name="check-on-the-data"></a>Verificar os dados

Vamos dar uma olhada no dados que carregássemos na nossa experiência. Na experimentação, selecione o resultado do **conjunto de dados de cadairydata.csv** e selecione **visualizar**. Deverá ver algo semelhante à figura 4.  

![Resumo do conjunto de dados cadairydata.csv](./media/r-quickstart/fig4.png)

*Figura 4. Resumo do conjunto de dados cadairydata.csv.*

Nesta vista, podemos ver muitas informações úteis. Podemos ver as primeiras várias linhas desse conjunto de dados. Se selecionarmos uma coluna, a seção de estatísticas mostra mais informações sobre a coluna. Por exemplo, a linha de tipo de funcionalidade nos mostra os tipos de dados do Azure Machine Learning Studio atribuído para a coluna. Ter uma vista rápida como essa é uma verificação da funcionalidade boa antes de começar a fazer qualquer trabalho sério.

### <a name="first-r-script"></a>Primeiro script de R

Vamos criar um script do R primeiro simples para experimentar no Azure Machine Learning Studio. Eu criou e testou o seguinte script no RStudio.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio
maml.mapOutputPort('cadairydata')
```

Agora, preciso transferir este script para o Azure Machine Learning Studio. Eu poderia simplesmente cortar e colar. No entanto, neste caso, posso transferir o meu script de R por meio de um arquivo zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Entrada de dados para o módulo de executar o Script de R

Vamos dar uma olhada nas entradas para o [executar Script R] [ execute-r-script] módulo. Neste exemplo vamos ler os dados de dairy Califórnia para o [executar Script R] [ execute-r-script] módulo.  

Existem três entradas possíveis para o [executar Script R] [ execute-r-script] módulo. Pode usar qualquer uma ou todas estas entradas, dependendo do seu aplicativo. Também é perfeitamente razoável para usar um script R que não precisa de entrada em todos os.  

Vamos examinar cada uma dessas entradas, indo da esquerda para a direita. Pode ver os nomes de cada uma das entradas ao colocar o cursor sobre a entrada e a descrição de leitura.  

#### <a name="script-bundle"></a>Pacote de script

O pacote de Script permite que passe o conteúdo de um ficheiro zip para de entrada [executar Script R] [ execute-r-script] módulo. Pode utilizar um dos seguintes comandos para ler o conteúdo do ficheiro zip para o seu código de R.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> O Azure Machine Learning Studio trata arquivos zip, como se eles estão no src / diretório, por isso terá de prefixo seus nomes de ficheiro com este nome de diretório. Por exemplo, se o zip contém os ficheiros `yourfile.R` e `yourData.rdata` na raiz do zip, teria de abordar como `src/yourfile.R` e `src/yourData.rdata` ao utilizar `source` e `load`.

Já abordamos a carregar conjuntos de dados no [carregar o conjunto de dados](#loading). Depois de ter criado e testado o script de R mostrado na secção anterior, faça o seguinte:

1. Guarde o script de R num. Ficheiro de R. Chamo meu arquivo de script "simpleplot. R". Aqui está o conteúdo.

   ```R
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio
   maml.mapOutputPort('cadairydata')
   ```

1. Crie um ficheiro zip e copiar o script para este ficheiro zip. No Windows, o ficheiro com o botão direito e selecione **enviar para o**e, em seguida **pasta compactados**. Esta ação irá criar um novo ficheiro zip que contém "simpleplot. Ficheiro de R".

1. Adicionar o ficheiro para o **conjuntos de dados** no Machine Learning Studio, especificando o tipo como **zip**. Agora, deve ver o ficheiro zip em seus conjuntos de dados.

1. Arraste e largue o ficheiro zip do **conjuntos de dados** para o **tela do ML Studio**.

1. Ligue a saída dos **zip dados** ícone para o **Script pacote** entrada do [executar Script do R] [ execute-r-script] módulo.

1. Tipo de `source()` função com seu nome de ficheiro zip para a janela de código para o [executar Script do R] [ execute-r-script] módulo. No meu caso digitei `source("src/simpleplot.R")`.  

1. Certifique-se de que seleciona **guardar**.

Assim que estas etapas forem concluídas, o [executar Script R] [ execute-r-script] módulo executará o script R no ficheiro zip quando a experimentação é executada. Neste momento sua experimentação deve ter um aspeto semelhante à figura 5.

![Utilizar o script de R zipado de experimentação](./media/r-quickstart/fig6.png)

*Figura 5. Experimente utilizar o script de R zipado.*

#### <a name="dataset1"></a>Dataset1

Pode passar uma tabela retangular de dados ao seu código de R ao utilizar a entrada de Dataset1. Em nosso script simple a `maml.mapInputPort(1)` função lê os dados de 1 de porta. Estes dados, em seguida, são atribuídos a um nome de variável de pacote de dados no seu código. Em nosso script simple, a primeira linha do código realiza a atribuição.

```R
cadairydata <- maml.mapInputPort(1)
```

Executar a experimentação, selecionando o **executar** botão. Quando a execução estiver concluída, selecione o [executar Script R] [ execute-r-script] módulo e selecione **log de saída do modo de exibição** no painel de propriedades. Uma nova página deverá aparecer no seu browser, que mostra o conteúdo do arquivo output.log. Quando rolar para baixo deverá ver algo semelhante ao seguinte.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Mais abaixo na página é que informações mais detalhadas sobre as colunas que terá uma aparência semelhante ao seguinte.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput]
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput]
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
    [ModuleOutput]
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput]
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput]
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput]
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

Esses resultados são principalmente como esperado, com 228 observações e 9 colunas no pacote de dados. Podemos ver os nomes das colunas, o tipo de dados de R e um exemplo de cada coluna.

> [!NOTE]
> Esta mesma saída impressa é convenientemente disponível da saída do dispositivo de R a [executar Script R] [ execute-r-script] módulo. Abordaremos as saídas do [executar Script R] [ execute-r-script] módulo na próxima seção.  

#### <a name="dataset2"></a>Dataset2

O comportamento da entrada Dataset2 é idêntico do Dataset1. Com esta entrada pode passar uma segunda tabela retangular de dados no seu código de R. A função `maml.mapInputPort(2)`, com o argumento 2, é utilizado para transmitir esses dados.  

### <a name="execute-r-script-outputs"></a>Executar R Script saídas

#### <a name="output-a-dataframe"></a>Um pacote de dados de saída

O utilizador pode apresentar o conteúdo de um pacote de R de dados como uma tabela retangular pela porta Dataset1 de resultado utilizando o `maml.mapOutputPort()` função. Em nosso script de R simple isso é executado a seguinte linha.

```
maml.mapOutputPort('cadairydata')
```

Depois de executar a experimentação, selecione a porta de saída do resultado Dataset1 e, em seguida, selecione **Visualize**. Deverá ver algo semelhante a figura 6.

![A visualização da saída dos dados dairy Califórnia](./media/r-quickstart/fig7.png)

*Figura 6. A visualização da saída dos dados dairy da Califórnia.*

Esta saída é idêntica à entrada, exatamente como era esperado.  

### <a name="r-device-output"></a>Saída de dispositivo do R

A saída de dispositivo do [executar Script R] [ execute-r-script] módulo contém elementos gráficos e as mensagens de saída. Ambas as mensagens de saída e o erro padrão padrão de R são enviadas para a porta de saída de dispositivo do R.  

Para ver o resultado de dispositivo do R, selecione a porta e, em seguida, no **Visualize**. Podemos ver a saída padrão e o erro padrão de scripts R na figura 7.

![Saída padrão e o erro padrão da porta do dispositivo de R](./media/r-quickstart/fig8.png)

*Figura 7. Saída padrão e o erro padrão da porta do dispositivo de R.*

Deslocar para baixo, consulte a saída de gráficos do nosso script de R na figura 8.  

![Saída de gráficos da porta do dispositivo de R](./media/r-quickstart/fig9.png)

*Figura 8. Gráficos de saída da porta do dispositivo de R.*  

## <a id="filtering"></a>Filtragem de dados e transformação

Nesta secção, iremos efetuar alguns dados básicos, filtragem e operações de transformação nos dados dairy da Califórnia. No final desta secção teremos dados num formato adequado para a criação de um modelo de análise.  

Mais especificamente, nesta secção, iremos efetuar várias tarefas comuns de dados de limpeza e transformação: tipo de transformação, filtragem de pacotes, adicionar novas colunas calculadas e transformações de valor. Este plano de fundo deve ajudar a lidar com muitas variações encontradas em problemas do mundo real.

O código de R completado para esta secção está disponível no [MachineLearningSamples-blocos de notas/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Transformações de tipo

Agora que estamos pode ler os dados de dairy Califórnia para o código de R no [executar Script R] [ execute-r-script] módulo, é necessário garantir que os dados nas colunas tem o tipo pretendido e o formato.  

R é uma linguagem de tipada dinâmico, o que significa que os tipos de dados são forçados de um para outro conforme necessário. Os tipos de dados atômica no R incluem numéricos, lógico e caráter. O tipo de fator é utilizado para armazenar compactly dados categóricos. Pode encontrar muito mais informações sobre tipos de dados nas referências no [leitura adicional](#appendixb) abaixo.

Quando os dados de tabela é lido no R a partir de uma origem externa, é sempre uma boa idéia verificar os tipos resultantes nas colunas. Pode desejar uma coluna de caracteres de tipo, mas em muitos casos isso irá aparecer como fator ou vice versa. Em outros casos, uma coluna que acha que deve ser numérica é representada por dados de caracteres, por exemplo número do ponto de "1.23" em vez de encontrar 1.23 como flutuante.  

Felizmente, é fácil converter um tipo para outro, desde que o mapeamento é possível. Por exemplo, não é possível converter 'Nevada' num valor numérico, mas pode convertê-la para um fator (variável categórica). Como outro exemplo, pode converter um 1 numérico num caractere '1' ou um fator.  

A sintaxe para qualquer um dessas conversões é simple: `as.datatype()`. Estas funções de conversão de tipo incluem o seguinte:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Observando os tipos de dados das colunas de entrada, na secção anterior: todas as colunas são do tipo numérico, exceto para a coluna intitulada "Mês", que é de caractere de tipo. Vamos converter isso num fator e testar os resultados.  

Posso ter eliminado a linha que criou a matriz de gráfico de dispersão e adicionou uma linha de converter a coluna 'Mês' para um fator. Na minha experiência, apenas serão recortar e colar o código R na janela de código do [executar Script R] [ execute-r-script] módulo. Também pode atualizar o ficheiro zip e carregue-o para o Azure Machine Learning Studio, mas esta ação demora vários passos.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio
maml.mapOutputPort('cadairydata')
```

Vamos executar esse código e analise o registo de saída para o script R. Os dados relevantes do log são mostrados na figura 9.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 9. Resumo do pacote de dados com uma variável de fator.*

O tipo para o mês deverá agora indicar '**fator com 14 níveis**'. Este é um problema, uma vez que existem apenas 12 meses do ano. Pode também verificar que o tipo de **Visualize** do conjunto de dados de resultado da porta é '**Categórico**'.

O problema é que não tenha sido codificada sistematicamente a coluna "Mês". Em alguns casos, um mês é chamado Abril e em outros é abreviado como Abril. Nós podemos resolver esse problema ao cortar a cadeia de caracteres para 3 carateres. Agora é a linha de código semelhante ao seguinte:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Executar novamente a experimentação e ver o registo de saída. Os resultados esperados são mostrados na figura 10.  

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 10. Resumo de pacote com o número correto de níveis de fator de dados.*

Agora, nossa variável de fator tem 12 níveis pretendidos.

### <a name="basic-data-frame-filtering"></a>Filtragem de quadro de dados básicos

Pacotes de R suportam recursos avançados de filtragem. Conjuntos de dados podem ser subsetted utilizando filtros lógicos em linhas ou colunas. Em muitos casos, os critérios de filtragem complexa será necessários. As referências no [leitura adicional](#appendixb) abaixo contêm exemplos extensivos de filtragem de pacotes.  

É um pouco de filtragem devemos fazer no nosso conjunto de dados. Se examinar as colunas no pacote de dados cadairydata, verá duas colunas desnecessárias. A primeira coluna contém apenas um número de linha, que não é muito útil. A segunda coluna, Year.Month, contém informações redundantes. Pode facilmente EXCLUÍMOS estas colunas com o seguinte código de R.

> [!NOTE]
> Daqui em diante nesta seção, vou apenas mostrar o código adicional que estou adicionando no [executar Script R] [ execute-r-script] módulo. Vou adicionar cada nova linha **antes de** o `str()` função. Posso utilizar esta função para verificar os resultados no Azure Machine Learning Studio.

Posso adicionar a seguinte linha ao meu código de R no [executar Script R] [ execute-r-script] módulo.

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Executar esse código na sua experimentação e verificar o resultado do log de saída. Esses resultados são mostrados na figura 11.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 11. Resumo do pacote de dados com duas colunas removidas.*

Boas notícias! Recebemos os resultados esperados.

### <a name="add-a-new-column"></a>Adicionar uma nova coluna

Para criar modelos de série de tempo, será conveniente ter uma coluna que contém os meses desde o início da série de tempo. Iremos criar uma nova coluna 'Month.Count'.

Para ajudar a organizar o código, vamos criar nossa primeira função simple, `num.month()`. Serão aplicadas, em seguida, esta função para criar uma nova coluna no pacote de dados. Segue-se o novo código.

```R
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Agora, execute a experimentação atualizada e utilizar o registo de saída para ver os resultados. Esses resultados são mostrados na figura 12.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 12. Resumo do pacote de dados com a coluna adicional.*

Parece que está tudo a funcionar. Temos a nova coluna com os valores esperados no nosso pacote de dados.

### <a name="value-transformations"></a>Transformações de valor

Nesta secção, iremos efetuar algumas transformações simples nos valores de algumas das colunas de nosso pacote de dados. A linguagem R oferece suporte a transformações de valor quase arbitrário. As referências no [leitura adicional](#appendixb) abaixo contêm exemplos abrangentes.

Se examinar os valores em resumos de nosso pacote de dados deverá ver algo estranho aqui. É mais ice cream que milk produzido na Califórnia? Não, certamente não, como isso não faz sentido, sad como esse fato podem ser para alguns de nós amantes de sorvete. As unidades são diferentes. O preço é, em unidades de nós, libras, milk é em unidades de 1 milhão libras dos EUA, ice cream é em unidades de 1.000 galões-nos e cottage queijo está em unidades de 1.000 E.U.A. libras. Partindo do princípio de sorvete examina cerca de 6.5 libras por litro, podemos facilmente fazer a multiplicação para converter esses valores para que eles estão todos em unidades iguais de libras 1000.

Para o nosso modelo de previsão vamos utilizar um modelo de multiplicadora de tendência e ajuste sazonal destes dados. Uma transformação de log nos permite usar um modelo linear, simplificar esse processo. É possível aplicar a transformação de log na mesma função onde o multiplicador será aplicado.

No código a seguir, eu defino uma nova função, `log.transform()`e aplicá-la para as linhas que contêm os valores numéricos. O R `Map()` função é usada para aplicar o `log.transform()` função para as colunas selecionadas do pacote de dados. `Map()` é semelhante ao `apply()` , mas permite mais do que uma lista de argumentos para a função. Tenha em atenção que uma lista de multiplicadores fornece o segundo argumento para o `log.transform()` função. O `na.omit()` a função é utilizada como um pouco de limpeza para se certificar de que não têm valores em falta ou não definidos no pacote de dados.

```R
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warningmessage to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

Há uma pouco estejam a acontecer no `log.transform()` função. A maioria desse código está a verificar para potenciais problemas com os argumentos ou lidar com exceções, o que ainda podem surgir durante as computações. Apenas algumas linhas desse código, na verdade, fazem os cálculos.

O objetivo a programação de defesa é impedir a falha de uma única função que impede o processamento de continuar. Uma reinicialização abrupta falha de uma análise de longa execução pode ser bastante frustrante para os utilizadores. Para evitar esta situação, os valores de retorno de predefinido tem de escolher qual limitará danos processamento a jusante. Uma mensagem também é produzida para alertar os usuários que algo deu errado.

Se não forem utilizados para programação defensiva em R, todo esse código pode parecer um pouco sobrecarregado. Eu irá guiá-lo pelos passos principais:

1. É definido um vetor de quatro mensagens. Estas mensagens são utilizadas para comunicar informações sobre alguns dos possíveis erros e exceções que podem ocorrer com esse código.
2. Devolve o valor para cada caso. Há muitas outras possibilidades que podem ter menos de lado de efeitos. Eu poderia retornar um vetor de zeros ou o vetor de entrada original, por exemplo.
3. Verificações são executadas nos argumentos da função. Em cada caso, se for detetado um erro, é devolvido um valor predefinido e uma mensagem é produzida pelo `warning()` função. Estou usando `warning()` vez `stop()` como a última opção irá terminar a execução, exatamente o que estou a tentar evitar. Observe que eu escrevi esse código num estilo de procedimento, como nesse caso uma abordagem funcional pareceu complexo e obscuro.
4. Os cálculos de registo serão encapsulados num wrapper `tryCatch()` para que as exceções não causará uma paralisação abrupta para processamento. Sem `tryCatch()` a maioria dos erros gerados pelo resultado de funções de R num sinal de paragem, que faz exatamente isso.

Executar esse código de R na sua experimentação e dar uma olhada na saída impressa no ficheiro output.log. Verá agora os valores transformados das quatro colunas no registo, conforme mostrado na figura 13.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 13. Resumo dos valores transformados o pacote de dados.*

Podemos ver que os valores transformados. Agora a produção de milk excede muito todos os outra produtos dairy de produção, relembramos que estamos agora estão procurando numa escala logarítmica.

Neste momento é limpo nossos dados e estamos prontos para alguma modelagem. Olhando para a visualização de resumo para a saída do conjunto de dados do resultado de nossa [executar Script R] [ execute-r-script] módulo, verá a coluna 'Mês' é 'Categórico' com 12 valores exclusivos, novamente, tal como Gostaríamos.

## <a id="timeseries"></a>Objetos de série de tempo e análise de correlação

Nesta secção, iremos explorar alguns objetos de série de tempo do R básicos e analisar as correlações entre algumas das variáveis. Nosso objetivo é um pacote de dados que contém as informações de correlação pairwise em vários lags de saída.

O código de R completado para esta secção está em [MachineLearningSamples-blocos de notas/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Objetos de série de tempo em R

Como já mencionado, tempo de série são uma série de valores de dados indexados por hora. Objetos de série de tempo de R são usados para criar e gerir o índice de tempo. Existem diversas vantagens em usar objetos de série de tempo. Objetos de série de tempo libertá-lo de muitos detalhes de gerir os valores de índice de séries de tempo que são encapsulados no objeto. Além disso, objetos de série de tempo que use vários métodos de série de tempo para desenhar, impressão, modelagem, etc.

A classe de série de tempo de POSIXct é normalmente utilizada e é relativamente simples. Esta série de tempo de tempo de medidas de classe desde o início de "Epoch", 1 de Janeiro de 1970. Nós usaremos objetos de série de tempo de POSIXct neste exemplo. Outras classes de objeto de série de tempo da R amplamente usados incluem zoo e xts, séries de tempo extensível.

### <a name="time-series-object-example"></a>Exemplo de objeto de série de tempo

Vamos começar com o nosso exemplo. Arrastar e soltar uma **novos** [executar Script do R] [ execute-r-script] módulo na sua experimentação. Ligue a porta de saída do resultado Dataset1 de existente [executar Script R] [ execute-r-script] porta da nova de entrada do módulo para a Dataset1 [executar Script do R] [ execute-r-script] módulo.

Como fiz para os primeiros exemplos, à medida que avançamos o exemplo, em alguns momentos, irá mostrar apenas as incrementais linhas adicionais de código de R em cada passo.  

#### <a name="reading-the-dataframe"></a>Ler o pacote de dados

Como primeiro passo, vamos de leitura de um pacote de dados e garantir que recebemos os resultados esperados. O código a seguir deve fazer o trabalho.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Agora, execute a experimentação. O registo da nova forma de executar o Script de R deverá ser semelhante figura 14.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*Figura 14. Resumo do pacote de dados no módulo executar Script R.*

Estes dados são dos tipos esperados e formato. Tenha em atenção que a coluna 'Mês' é do fator de tipo e tem o número esperado de níveis.

#### <a name="creating-a-time-series-object"></a>Criar um objeto de série de tempo

É necessário adicionar um objeto de série de tempo para nosso pacote de dados. Substitua o código atual com o seguinte, que adiciona uma nova coluna da classe POSIXct.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Agora, verifique o registo. Deve ser semelhante a figura 15.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figura 15. Resumo do pacote de dados com um objeto de série de tempo.*

Podemos ver no resumo do que a nova coluna é, na verdade, da classe POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Explorar e transformar os dados

Vamos explorar algumas das variáveis este conjunto de dados. Uma matriz de gráfico de dispersão é uma boa maneira de produzir uma vista rápida. Eu estou substituindo o `str()` função no código anterior R com a seguinte linha.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Executar esse código e ver o que acontece. O desenho produzido nas portas R dispositivo deverá ser semelhante a figura 16.

![Matriz de gráfico de dispersão das variáveis selecionadas](./media/r-quickstart/fig17.png)

*Figura 16. Matriz de gráfico de dispersão das variáveis selecionadas.*

Existe alguma estrutura estranha as relações entre essas variáveis. Talvez isso surge de tendências de dados e do fato de não padronizaram as variáveis.

### <a name="correlation-analysis"></a>Análise de correlação

Para executar a análise de correlação, precisamos de remover a atribuição de tendência e padronizar as variáveis. Poderíamos simplesmente usar o R `scale()` função, que centraliza tanto dimensiona variáveis. Esta função também pode executar mais rapidamente. No entanto, eu quero mostrar um exemplo de programação de defesa em R.

O `ts.detrend()` função abaixo executa ambas estas operações. As seguintes duas linhas de código anular os dados de tendência e, em seguida, normalizar os valores.

```R
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

Há uma pouco estejam a acontecer no `ts.detrend()` função. A maioria desse código está a verificar para potenciais problemas com os argumentos ou lidar com exceções, o que ainda podem surgir durante as computações. Apenas algumas linhas desse código, na verdade, fazem os cálculos.

Já abordamos a um exemplo de programação de defesa nas transformações de valor. Ambos os blocos de computação serão encapsulados num wrapper `tryCatch()`. Para alguns erros faz sentido para retornar o vetor de entrada original e, em outros casos, posso retornar um vetor de zeros.  

Observe que a regressão linear utilizada para anular a fins de tendência é uma regressão da série de tempo. A variável de previsão é um objeto de série de tempo.  

Uma vez `ts.detrend()` é definido Aplicamos as variáveis de interesse no nosso pacote de dados. Podemos deve forçar a lista resultante criada pelo `lapply()` para dados dataframe utilizando `as.data.frame()`. Devido a defensivo aspectos do `ts.detrend()`, Falha ao processar uma das variáveis não impedirá o processamento correto dos outros.  

A linha final do código cria um gráfico de dispersão emparelhado. Depois de executar o código R, os resultados do gráfico de dispersão são mostrados na figura 17.

![Gráfico de dispersão Pairwise de série de tempo de anular trended e padronizado](./media/r-quickstart/fig18.png)

*Figura 17. Gráfico de dispersão Pairwise de série de tempo de anular trended e normalizada.*

Pode comparar esses resultados às mostradas na figura 16. Com a tendência removido e as variáveis padronizadas, vemos muito menos estrutura nas relações entre essas variáveis.

Segue-se o código para calcular as correlações como objetos ccf de R.

```R
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

Executar esse código produz o registo de mostrado na figura 18.

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*Figura 18. Lista de ccf objetos desde a análise de correlação emparelhadas.*

Existe um valor de correlação para cada atraso. Nenhum desses valores de correlação é suficientemente grande para ser significativo. Pode, por isso, concluímos que podemos modelar cada variável de forma independente.

### <a name="output-a-dataframe"></a>Um pacote de dados de saída
Podemos ter calculadas as correlações emparelhadas como uma lista de objetos de ccf de R. Isso apresenta um pouco de um problema à medida que a porta de saída do conjunto de dados de resultado realmente necessita de um pacote de dados. Além disso, o objeto de ccf em si é uma lista e Queremos que apenas os valores no primeiro elemento dessa lista, as correlações nos lags vários.

O código a seguir extrai os valores de atraso da lista de objetos de ccf, que são elas mesmas listas.

```R
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation data frame and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

A primeira linha do código é um pouco complicada e explicações podem ajudá-lo a compreendê-lo. Trabalhar a partir de dentro para fora, temos o seguinte:

1. O '**[[**'operador com o argumento'**1**' seleciona o vetor de correlações nos lags o primeiro elemento da lista de objetos de ccf.
2. O `do.call()` função aplica-se a `rbind()` função sobre os elementos da lista devolve por `lapply()`.
3. O `data.frame()` função coerces o resultado gerado pela `do.call()` para um dataframe.

Tenha em atenção que os nomes de linha estão numa coluna do pacote de dados. Fazer assim preserva a linha nomes quando eles são a saída do [executar Script R][execute-r-script].

A execução do código produz a saída mostrada na figura 19 quando eu **Visualize** a saída nas portas de conjunto de dados do resultado. Os nomes de linha são na primeira coluna, conforme pretendido.

![Saída de resultados de análise de correlação](./media/r-quickstart/fig20.png)

*Figura 19. Resultados de análise de correlação de saída.*

## <a id="seasonalforecasting"></a>Exemplo de série de tempo: previsão sazonais

Nossos dados estão agora num formato adequado para análise e Determinámos que não há nenhum significativas correlações entre as variáveis. Vamos prosseguir e criar um modelo de previsão de séries de tempo. Usando esse modelo, irá prever a produção de milk Califórnia durante os 12 meses de 2013.

O nosso modelo de previsão terá dois componentes, um componente de tendência e um componente sazonal. A previsão de conclua é o produto desses dois componentes. Este tipo de modelo é conhecido como um modelo multiplicadora. A alternativa é um modelo suplementar. Já estamos tiver aplicado uma transformação de log para as variáveis de interesse, o que faz esta análise tractable.

O código de R completado para esta secção está em [MachineLearningSamples-blocos de notas/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>Criar o pacote de dados para análise

Comece adicionando uma **novos** [executar Script do R] [ execute-r-script] módulo à sua experimentação. Ligar o **conjunto de dados de resultado** saída de existente [executar Script do R] [ execute-r-script] módulo para o **Dataset1** entrada do módulo novo. O resultado deve ser algo semelhante a figura 20.

![A experimentação com o novo módulo de executar o Script de R adicionado](./media/r-quickstart/fig21.png)

*Figura 20. A experimentação com o novo módulo de executar o Script de R adicionado.*

Como com a análise de correlação que acabou de concluir, é necessário adicionar uma coluna com um objeto de série de tempo de POSIXct. O código a seguir será fazer exatamente isso.

```R
# If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Executar esse código e analise o registo. O resultado deverá ser semelhante a figura 21.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figura 21. Um resumo do pacote de dados.*

Com este resultado, está pronto para começar a nossa análise.

### <a name="create-a-training-dataset"></a>Criar um conjunto de dados de treinamento

Com o pacote de dados construído, precisamos criar um conjunto de dados de treinamento. Estes dados incluirá todas as observações, exceto as últimas 12, do ano de 2013, que é o nosso conjunto de dados de teste. O seguinte código subconjuntos o pacote de dados e cria os gráficos das variáveis de produção e preço dairy. Eu, em seguida, criar gráficos de produção de quatro e variáveis de preços. Uma função anônima é usada para definir alguns aumenta para desenho e, em seguida, iterar sobre a lista dos outros dois argumentos com `Map()`. Se estiver pensando que numa para loop teria funcionado bem aqui, acertou. No entanto, uma vez que o R é uma linguagem funcional que estou mostrando a uma abordagem funcional.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

A execução do código produz a série de tempo série desenha a partir da saída de dispositivo R mostrada na figura 22. Observe que o eixo de tempo é em unidades de datas, uma bela vantagem do tempo do método de desenho de série.

![Primeiro de gráficos de séries de tempo de dados de produção e preço dairy no Califórnia](./media/r-quickstart/unnamed-chunk-161.png)

![Segundo de gráficos de séries de tempo de dados de produção e preço dairy no Califórnia](./media/r-quickstart/unnamed-chunk-162.png)

![Terceiro de gráficos de séries de tempo de dados de produção e preço dairy no Califórnia](./media/r-quickstart/unnamed-chunk-163.png)

![Quarto de gráficos de séries de tempo de dados de produção e preço dairy no Califórnia](./media/r-quickstart/unnamed-chunk-164.png)

*Figura 22. Gráficos de séries de tempo de produção dairy da Califórnia e dados de preços.*

### <a name="a-trend-model"></a>Um modelo de tendência

Vamos ter criado um objeto de série de tempo e ter tinha a ver os dados, começar a construir um modelo de tendência para os dados de produção de milk da Califórnia. Podemos fazer isso com uma regressão da série de tempo. No entanto, é claro a partir do desenho que irá precisar de mais do que uma situação perigosa e interceptar para modelar com precisão a tendência observada nos dados de treinamento.

Tendo em conta a pequena escala dos dados, vou criar o modelo de tendência no r Studio e, em seguida, cortar e colar do modelo resultante para o Azure Machine Learning Studio. R Studio fornece um ambiente interativo para este tipo de análises interativas.

Como uma primeira tentativa, tento uma regressão polynomial com potências até 3. Existe um perigo real de excesso que se ajusta a esses tipos de modelos. Por conseguinte, é melhor evitar os termos de ordem elevada. O `I()` função inibe a interpretação do conteúdo (interpreta o conteúdo "como está") e permite-lhe escrever uma função literalmente interpretada numa equação de regressão.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Isso gera o seguinte.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12667 -0.02730  0.00236  0.02943  0.10586
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

Entre os valores de P (`Pr(>|t|)`) neste dados de saída, podemos ver que o termo ao quadrado pode não ser significativo. Usarei o `update()` função para modificar esse modelo ao remover o termo ao quadrado.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Isso gera o seguinte.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12597 -0.02659  0.00185  0.02963  0.10696
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

Isso parece melhor. Todos os termos são significativos. No entanto, o valor de 2e 16 é um valor predefinido e não deve ser tomado demasiado a sério.  

Como um teste de sanidade, vamos criar um desenho de série de tempo dos dados de produção dairy Califórnia com a curva de tendência mostrada. Adicionei o seguinte código no Azure Machine Learning Studio [executar Script R] [ execute-r-script] modelo (não RStudio) para criar o modelo e faz um desenho. O resultado é mostrado na figura 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Dados de produção da Califórnia milk com o modelo de tendência mostrado](./media/r-quickstart/unnamed-chunk-18.png)

*Figura 23. Dados de produção do milk Califórnia com o modelo de tendência mostrado.*

Parece que o modelo de tendência se encaixa muito bem os dados. Além disso, não parece ser prova de ajuste excessivo, como o ímpar wiggles na curva do modelo.  

### <a name="seasonal-model"></a>Modelo sazonal

Com um modelo de tendência em mãos, precisamos de push no e incluem os efeitos sazonais. Nós usaremos o mês do ano como uma variável fictícia no modelo linear para capturar o efeito de mês a mês. Tenha em atenção que quando introduz variáveis fator para um modelo, a intercepção deve não ser computada. Se fizer isso, a fórmula é excessivamente especificada e R irá remover um dos fatores desejados, mas manter o termo de intercepção.

Uma vez que temos um modelo de tendência satisfatória, podemos usar o `update()` função para adicionar os novos termos para o modelo existente. A -1 na fórmula atualização ignora o termo de interceptação. Continuar no r Studio para o momento em que:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Isso gera o seguinte.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.06879 -0.01693  0.00346  0.01543  0.08726
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,    Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

Podemos ver que o modelo já não tem um termo de intercepção de colunas e tem 12 fatores de mês significativo. Isso é exatamente o que queríamos ver.

Vamos fazer outro gráfico de série de tempo dos dados de produção dairy Califórnia para ver a eficiência com que o modelo sazonal está a funcionar. Adicionei o seguinte código no Azure Machine Learning Studio [executar Script R] [ execute-r-script] para criar o modelo e faz um desenho.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Executar esse código no Azure Machine Learning Studio produz o desenho mostrado na figura 24.

![Produção de milk Califórnia com o modelo, incluindo efeitos sazonais](./media/r-quickstart/unnamed-chunk-20.png)

*Figura 24. Produção de milk Califórnia com o modelo, incluindo efeitos sazonais.*

O que se ajusta aos dados mostrados na figura 24 é bastante animadora. As tendências e o efeito sazonal (variação mensal) parecer razoáveis.

Como outra verificação no nosso modelo, vamos dar uma olhada nos residuals. O código a seguir calcula os valores previstos de nossos dois modelos, computa os residuals para o modelo sazonal e desenha, em seguida, estes residuals dos dados de treinamento.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

O desenho residual é mostrado na figura 25.

![Residuals do modelo sazonal para os dados de treinamento](./media/r-quickstart/unnamed-chunk-21.png)

*Figura 25. Residuals do modelo sazonal para os dados de treinamento.*

Estes residuals parecer razoáveis. Não há nenhuma estrutura específica, exceto o efeito da recessão 2008 2009, o que o nosso modelo não dá conta particular.

O desenho mostrado na figura 25 é útil para detectar padrões qualquer dependente de tempo nos residuals. A abordagem explícita de computação e desenhar os residuals usei coloca os residuals na ordem de tempo em que o desenho. Se, por outro lado, eu tinha desenhados `milk.lm$residuals`, o desenho não teria sido ordem cronológica.

Também pode utilizar `plot.lm()` para produzir uma série de gráficos de diagnóstico.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Esse código produz uma série de gráficos diagnóstico mostrado na figura 26.

![Primeiro de gráficos de diagnóstico para o modelo sazonal](./media/r-quickstart/unnamed-chunk-221.png)

![Segundo, de gráficos de diagnóstico para o modelo sazonal](./media/r-quickstart/unnamed-chunk-222.png)

![Terceiro de gráficos de diagnóstico para o modelo sazonal](./media/r-quickstart/unnamed-chunk-223.png)

![Quarto de gráficos de diagnóstico para o modelo sazonal](./media/r-quickstart/unnamed-chunk-224.png)

*Figura 26. Desenha diagnóstico para o modelo sazonal.*

Há alguns pontos influentes altamente identificados nestes gráficos, mas nada a fazer com que a grande preocupação. Além disso, podemos ver no desenho de Normal P-Q que o residuals estão próximos normalmente distribuídas, uma declaração importante para os modelos de lineares.

### <a name="forecasting-and-model-evaluation"></a>Avaliação de previsão e modelo

Há mais apenas uma coisa a fazer para concluir o nosso exemplo. Precisamos de previsões de computação e medir o erro contra os dados reais. Nossa previsão será durante os 12 meses de 2013. Podemos poderá computar uma medida de erro para este previsão para os dados reais que não faz parte do nosso conjunto de dados de treinamento. Além disso, podemos comparar o desempenho de 18 anos de dados de treinamento para os 12 meses de dados de teste.  

Várias métricas são utilizadas para medir o desempenho dos modelos de série de tempo. No nosso caso, nós usaremos o erro de média quadrática de raiz (RMS). A seguinte função calcula o erro de RMS entre duas séries.  

```R
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Tal como acontece com o `log.transform()` função discutimos na secção "Valor transformações", há uma grande de código de recuperação de exceção e a verificação de erro nesta função. Os princípios empregados são os mesmos. O trabalho é realizado em dois locais encapsulados em `tryCatch()`. Em primeiro lugar, a série de tempo é exponentiated, uma vez que temos estado a trabalhar com os registos dos valores. Em segundo lugar, o erro de RMS real é calculado.  

Equipado com uma função para medir o erro de RMS, vamos criar de saída e um pacote de dados que contém os erros de RMS. Iremos também incluir termos para o modelo de tendência autónomo e o modelo completo com fatores sazonais. O código a seguir faz o trabalho usando os dois modelos lineares construímos.

```R
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio
maml.mapOutputPort('RMS.df')
```

Executar esse código produz a saída mostrada na figura 27 na porta de saída do conjunto de dados do resultado.

![Comparação de erros de RMS para os modelos](./media/r-quickstart/fig26.png)

*Figura 27. Comparação de erros de RMS para os modelos.*

Esses resultados, podemos ver que o adicionar os fatores sazonais ao modelo reduz o erro de RMS significativamente. Obviamente, o erro do RMS para os dados de treinamento é um pouco menor que para a previsão.

## <a id="appendixa"></a>Guia de documentação do RStudio

RStudio está muito bem documentado. Seguem-se algumas ligações para as secções principais da documentação do RStudio para começar.

* **Criação de projetos** -é possível organizar e gerir o seu código de R em projetos através do RStudio. Ver [projetos usando](https://support.rstudio.com/hc/articles/200526207-Using-Projects) para obter detalhes. Recomendo que siga essas diretrizes e cria um projeto para os exemplos de código de R neste artigo.  
* **Editar e executar o código R** -r Studio fornece um ambiente integrado para edição e a execução de código R. Ver [edição e executar código](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) para obter detalhes.
* **Depuração** -RStudio inclui capacidades poderosas de depuração. Ver [depuração com o r Studio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) para obter mais informações sobre estas funcionalidades. Para obter informações sobre recursos de resolução de problemas de ponto de interrupção, consulte [resolução de problemas de ponto de interrupção](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting).

## <a id="appendixb"></a>Leitura adicional

Este tutorial de programação R abrange as noções básicas sobre o que precisa de utilizar a linguagem R com o Azure Machine Learning Studio. Se não estiver familiarizado com o R, dois introduções estão disponíveis na CRAN:

* [R para iniciantes](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) por Emmanuel Paradis é um bom lugar para começar.  
* [Uma introdução ao R](https://cran.r-project.org/doc/manuals/R-intro.html) por W. n. Venables et. al. entra num pouco mais profundidade.

Existem muitos livros em R que pode ajudá-lo a começar a utilizar. Aqui estão alguns que considero útil:

* O **arte de programação R: Um Tour de Design estatísticas de Software** por Norman Matloff é uma excelente introdução à programação em R.  
* **Manual de R** por Paul Teetor fornece uma abordagem de problema e a solução para utilizar o R.  
* **R em ação** por Robert Kabacoff é outro livro introdutório úteis. O complemento [Web site do R rápida](https://www.statmethods.net/) é um recurso útil.
* **R Inferno** por Patrick Burns é um livro surpreendentemente bem-humorada que lida com um número de tópicos complicados e difícil que podem ser encontrados ao programar em R. O livro está disponível gratuitamente em [o Inferno de R](http://www.burns-stat.com/documents/books/the-r-inferno/).
* Se quiser uma descrição aprofundada tópicos avançados em R, dê uma olhada no livro **R avançadas** por Hadley Wickham. A versão online deste livro está disponível gratuitamente em [ http://adv-r.had.co.nz/ ](http://adv-r.had.co.nz/).

Um catálogo de pacotes de série de tempo de R pode ser encontrado no [CRAN vista da tarefa: Análise de série de tempo](https://cran.r-project.org/web/views/TimeSeries.html). Para obter informações sobre a hora específica pacotes de objeto de série, consulte a documentação desse pacote.

O livro **introdutório séries de tempo** com R por Paul Cowpertwait e Andrew Metcalfe fornece uma introdução ao utilizar o R para análise de série de tempo. Muitos textos mais teóricos fornecem exemplos de R.

Aqui estão alguns ótimos recursos de internet:

* DataCamp ensina R no conforto do seu browser com lições de vídeo e exercícios de codificação. Há tutoriais interativos sobre as técnicas de R mais recentes e os pacotes. Tirar o gratuito [tutorial interativo do R](https://www.datacamp.com/courses/introduction-to-r).
* [Aprender programação R, o guia definitivo](https://www.programiz.com/r-programming) de Programiz.
* Uma rápida [R Tutorial](https://www.cyclismo.org/tutorial/R/) por preto Kelly da computação pela Universidade Clarkson.
* Existem ao longo de 60 R recursos listados em [recursos de linguagem R da parte superior para melhorar suas habilidades de dados](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html).

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
