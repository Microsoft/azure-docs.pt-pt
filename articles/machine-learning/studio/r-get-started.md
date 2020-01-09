---
title: Introdução ao R
titleSuffix: ML Studio (classic) - Azure
description: Use este tutorial de programação R para começar a usar a linguagem R com Azure Machine Learning Studio (clássico) para criar uma solução de previsão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 81e232e37e437c4fa9d23a49a720b88511423905
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427563"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio-classic"></a>Introdução à linguagem de programação R no Azure Machine Learning Studio (clássico)

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Introdução

Este tutorial ajuda você a começar a estender Azure Machine Learning Studio (clássico) usando a linguagem de programação R. Siga este tutorial de programação de R para criar, testar e executar o código R no estúdio (clássico). Ao trabalhar com o tutorial, você criará uma solução de previsão completa usando a linguagem R no Studio (clássico).  

Azure Machine Learning Studio (clássico) contém muitos módulos poderosos de aprendizado de máquina e de manipulação de dados. A linguagem R poderosa foi descrita como língua franca da análise. Felizmente, a análise e a manipulação de dados no Studio (clássico) podem ser estendidas usando o R. Essa combinação fornece a escalabilidade e a facilidade de implantação do estúdio (clássico) com a flexibilidade e análise profunda de R.

### <a name="forecasting-and-the-dataset"></a>Previsão e o conjunto de conjuntos

A previsão é um método analítico amplamente empregado e bastante útil. Os usos comuns variam de prever as vendas de itens sazonais, determinando os níveis de estoque ideais, para prever as variáveis de macroeconômicas. Normalmente, a previsão é feita com modelos de série temporal.

Os dados de série temporal são dados nos quais os valores têm um índice de tempo. O índice de tempo pode ser regular, por exemplo, todos os meses ou a cada minuto, ou irregular. Um modelo de série temporal é baseado em dados de série temporal. A linguagem de programação R contém uma estrutura flexível e análises extensivas para dados de série temporal.

Neste guia, vamos trabalhar com os dados de preço e de produção de laticínios da Califórnia. Esses dados incluem informações mensais sobre a produção de vários produtos de laticínios e o preço do leite Fat, uma mercadoria de benchmark.

Os dados usados neste artigo, juntamente com os scripts do R, podem ser baixados de [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Os dados no arquivo `cadairydata.csv` foram originalmente sintetizados a partir das informações disponíveis na Universidade de Wisconsin em [https://dairymarkets.com](https://dairymarkets.com).

### <a name="organization"></a>Organização

Avançaremos por várias etapas à medida que você aprender a criar, testar e executar o código de R de análise e manipulação de dados no ambiente Azure Machine Learning Studio (clássico).  

* Primeiro, exploraremos as noções básicas do uso da linguagem R no ambiente Azure Machine Learning Studio (clássico).
* Em seguida, vamos progredir para discutir vários aspectos de e/s para dados, código R e gráficos no ambiente Azure Machine Learning Studio (clássico).
* Em seguida, criaremos a primeira parte de nossa solução de previsão criando código para limpeza e transformação de dados.
* Com nossos dados preparados, realizaremos uma análise das correlações entre várias das variáveis em nosso conjunto.
* Por fim, criaremos um modelo de previsão de série temporal sazonal para leite de produção.

## <a id="mlstudio"></a>Interagir com a linguagem R no Machine Learning Studio (clássico)

Esta seção orienta você por algumas noções básicas de interação com a linguagem de programação R no ambiente Machine Learning Studio (clássico). A linguagem R fornece uma poderosa ferramenta para criar módulos de análise e manipulação de dados personalizados dentro do ambiente Azure Machine Learning Studio (clássico).

Usarei o RStudio para desenvolver, testar e depurar o código R em uma pequena escala. Esse código é, então, recortado e colado em um módulo [Executar script R][execute-r-script] pronto para ser executado no Azure Machine Learning Studio (clássico).  

### <a name="the-execute-r-script-module"></a>O módulo executar script R

Dentro de Machine Learning Studio (clássico), os scripts do R são executados no módulo [Executar script R][execute-r-script] . Um exemplo do módulo [Executar script R][execute-r-script] no Machine Learning Studio (clássico) é mostrado na Figura 1.

 ![Linguagem de programação r: o módulo executar script R selecionado no Machine Learning Studio (clássico)](./media/r-quickstart/fig1.png)

*Figura 1. O ambiente Machine Learning Studio (clássico) mostrando o módulo executar script R selecionado.*

Fazendo referência à figura 1, vamos dar uma olhada em algumas das principais partes do ambiente Machine Learning Studio (clássico) para trabalhar com o módulo [Executar script R][execute-r-script] .

* Os módulos no experimento são mostrados no painel central.
* A parte superior do painel direito contém uma janela para exibir e editar os scripts do R.  
* A parte inferior do painel direito mostra algumas propriedades do [Executar script R][execute-r-script]. Você pode exibir os logs de erro e saída selecionando os pontos adequados deste painel.

Certamente, vamos discutir o [script R execute][execute-r-script] com mais detalhes no restante deste artigo.

Ao trabalhar com funções de R complexas, recomendo que você edite, teste e depure em RStudio. Assim como acontece com qualquer desenvolvimento de software, estenda o código incrementalmente e teste-o em pequenos casos de teste simples. Em seguida, recorte e cole suas funções na janela script R do módulo [Executar script r][execute-r-script] . Essa abordagem permite que você aproveite o IDE (ambiente de desenvolvimento integrado) do RStudio e o poder do Azure Machine Learning Studio (clássico).  

#### <a name="execute-r-code"></a>Executar código R

Qualquer código R no módulo [Executar script r][execute-r-script] será executado quando você executar o experimento selecionando o botão **executar** . Quando a execução for concluída, uma marca de seleção será exibida no ícone [Executar script R][execute-r-script] .

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Codificação de R defensiva para Azure Machine Learning

Se você estiver desenvolvendo código R para, digamos, um serviço Web usando Azure Machine Learning Studio (clássico), você deve, definitivamente, planejar como seu código lidará com uma entrada de dados inesperada e exceções. Para manter a clareza, não incluí muito no modo de verificação ou tratamento de exceções na maioria dos exemplos de código mostrados. No entanto, à medida que prosseguirei, darei vários exemplos de funções usando a funcionalidade de manipulação de exceção do R.  

Se você precisar de um tratamento mais completo da manipulação de exceção de R, recomendo que leia as seções aplicáveis do livro por Wickham listadas abaixo em [leituras adicionais](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Depurar e testar R em Machine Learning Studio (clássico)

Para reiterar, recomendo que você teste e depure seu código R em uma pequena escala em RStudio. No entanto, há casos em que você precisará rastrear problemas de código R no [script executar r][execute-r-script] em si. Além disso, é uma prática recomendada verificar os resultados em Machine Learning Studio (clássico).

A saída da execução do código R e da plataforma Azure Machine Learning Studio (clássica) é encontrada principalmente em Output. log. Algumas informações adicionais serão vistas em Error. log.  

Se ocorrer um erro no Machine Learning Studio (clássico) ao executar o código do R, seu primeiro curso de ação deve ser examinar Error. log. Esse arquivo pode conter mensagens de erro úteis para ajudá-lo a entender e corrigir o erro. Para exibir Error. log, selecione **Exibir log de erros** no **painel Propriedades** para [executar o script R][execute-r-script] que contém o erro.

Por exemplo, executei o seguinte código R, com uma variável y indefinida, em um módulo [Executar script r][execute-r-script] :

```R
x <- 1.0
z <- x + y
```

Esse código não é executado, resultando em uma condição de erro. Selecionar **Exibir log de erros** no **painel Propriedades** produz a exibição mostrada na Figura 2.

  ![Pop-up de mensagem de erro](./media/r-quickstart/fig2.png)

*Figura 2. Pop-up de mensagem de erro.*

Parece que precisamos examinar o output. log para ver a mensagem de erro do R. Selecione [Executar script R][execute-r-script] e, em seguida, selecione o item **Exibir saída. log** no **painel Propriedades** à direita. Uma nova janela do navegador é aberta e vejo o seguinte.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Essa mensagem de erro não contém surpresas e identifica claramente o problema.

Para inspecionar o valor de qualquer objeto em R, você pode imprimir esses valores no arquivo output. log. As regras para examinar os valores de objeto são essencialmente as mesmas que em uma sessão interativa de R. Por exemplo, se você digitar um nome de variável em uma linha, o valor do objeto será impresso no arquivo output. log.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Pacotes no Machine Learning Studio (clássico)

O estúdio vem com mais de 350 pacotes de linguagem R pré-instalados. Você pode usar o código a seguir no módulo [Executar script R][execute-r-script] para recuperar uma lista dos pacotes pré-instalados.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Se você não entender a última linha desse código no momento, continue lendo. No restante deste artigo, discutiremos extensivamente o uso do R no ambiente Studio (clássico).

### <a name="introduction-to-rstudio"></a>Introdução ao RStudio

RStudio é um IDE amplamente usado para R. Usarei o RStudio para editar, testar e depurar alguns dos códigos R usados neste guia. Depois que o código R é testado e pronto, você pode simplesmente recortar e colar do editor RStudio em um módulo de [execução de script R][execute-r-script] Machine Learning Studio (clássico).  

Se você não tiver a linguagem de programação R instalada em seu computador desktop, recomendo que você faça isso agora. Downloads gratuitos de linguagem R de software livre estão disponíveis na CRAN (rede de arquivos de R) abrangente em [https://www.r-project.org/](https://www.r-project.org/). Há downloads disponíveis para Windows, Mac OS e Linux/UNIX. Escolha um espelho próximo e siga as instruções de download. Além disso, o CRAN contém uma infinidade de pacotes úteis de análise e manipulação de dados.

Se você for novo no RStudio, baixe e instale a versão da área de trabalho. Você pode encontrar os downloads do RStudio para Windows, Mac OS e Linux/UNIX em http://www.rstudio.com/products/RStudio/. Siga as instruções fornecidas para instalar o RStudio no computador desktop.  

Um tutorial de introdução ao RStudio está disponível em [usando o IDE do RStudio](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Eu fornece algumas informações adicionais sobre como usar o RStudio no [guia para a documentação do RStudio](#appendixa) abaixo.  

## <a id="scriptmodule"></a>Obter dados dentro e fora do módulo executar script R

Nesta seção, discutiremos como você obtém dados dentro e fora do módulo [Executar script R][execute-r-script] . Examinaremos como lidar com vários tipos de dados lidos dentro e fora do módulo [Executar script R][execute-r-script] .

O código completo para esta seção está em [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data-in-machine-learning-studio-classic"></a>Carregar e verificar dados em Machine Learning Studio (clássico)

#### <a id="loading"></a>Carregar o conjunto de um

Vamos começar carregando o arquivo **csdairydata. csv** em Azure Machine Learning Studio (clássico).

1. Inicie seu ambiente de Azure Machine Learning Studio (clássico).
1. Selecione **+ novo** na parte inferior esquerda da tela e selecione **conjunto**de espaço.
1. Selecione **do arquivo local**e, em seguida, **navegue** para selecionar o arquivo.
1. Verifique se você selecionou o **arquivo CSV genérico com o cabeçalho (. csv)** como o tipo do conjunto de um.
1. Selecione a marca de seleção.
1. Depois que o conjunto de um for carregado, você deverá ver o novo conjunto de DataSet selecionando a guia **DataSets** .  

#### <a name="create-an-experiment"></a>Criar uma experimentação

Agora que temos alguns dados em Machine Learning Studio (clássico), precisamos criar um experimento para fazer a análise.  

1. Selecione **+ novo** na parte inferior esquerda e selecione **experimento**e **experimento em branco**.
1. Você pode nomear seu experimento selecionando e modificando o **experimento criado em...** título na parte superior da página. Por exemplo, alterá-lo para **análise de laticínios de CA**.
1. À esquerda da página experimento, expanda **DataSets salvos**e, em seguida, **meus conjuntos de valores**. Você deve ver o **cadairydata. csv** que você carregou anteriormente.
1. Arraste e solte o **conjunto de csdairydata. csv** no experimento.
1. Na caixa **Pesquisar itens de teste** na parte superior do painel esquerdo, digite [Executar script R][execute-r-script]. Você verá o módulo aparecer na lista de pesquisa.
1. Arraste e solte o módulo [Executar script R][execute-r-script] em seu palete.  
1. Conecte a saída do conjunto de dados **csdairydata. csv** à entrada mais à esquerda (**dataSet1**) de [Executar script R][execute-r-script].
1. **Não se esqueça de selecionar ' salvar '!**  

Neste ponto, seu experimento deve ser semelhante ao da Figura 3.

![O teste de análise de laticínios de CA com o conjunto de módulos e o módulo executar script R](./media/r-quickstart/fig3.png)

*Figura 3. O teste de análise de laticínios de CA com o conjunto de módulos e o módulo executar script R.*

#### <a name="check-on-the-data"></a>Verificar nos dados

Vamos dar uma olhada nos dados que carregamos em nosso experimento. No experimento, selecione a saída do conjunto de **cadairydata. csv** e selecione **Visualizar**. Você deve ver algo como a Figura 4.  

![Resumo do conjunto de cadairydata. csv](./media/r-quickstart/fig4.png)

*Figura 4. Resumo do conjunto de cadairydata. csv.*

Nessa exibição, vemos muitas informações úteis. Podemos ver as primeiras várias linhas desse conjunto de registros. Se selecionarmos uma coluna, a seção estatísticas mostrará mais informações sobre a coluna. Por exemplo, a linha tipo de recurso mostra quais tipos de dados Azure Machine Learning Studio (clássico) atribuídos à coluna. Ter uma aparência rápida como essa é uma boa verificação de sanidade antes de começarmos a fazer qualquer trabalho sério.

### <a name="first-r-script"></a>Primeiro script de R

Vamos criar um primeiro script R simples para fazer experimentos em Azure Machine Learning Studio (clássico). Criei e testei o script a seguir em RStudio.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Agora preciso transferir esse script para Azure Machine Learning Studio (clássico). Eu poderia simplesmente recortar e colar. No entanto, nesse caso, vou transferir meu script R por meio de um arquivo zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Entrada de dados para o módulo executar script R

Vamos dar uma olhada nas entradas para o módulo [Executar script R][execute-r-script] . Neste exemplo, vamos ler os dados de laticínios da Califórnia no módulo [Executar script R][execute-r-script] .  

Há três entradas possíveis para o módulo [Executar script R][execute-r-script] . Você pode usar qualquer uma ou todas essas entradas, dependendo do seu aplicativo. Também é perfeitamente razoável usar um script R que não exige nenhuma entrada.  

Vamos examinar cada uma dessas entradas, indo da esquerda para a direita. Você pode ver os nomes de cada uma das entradas colocando o cursor sobre a entrada e lendo a dica de ferramenta.  

#### <a name="script-bundle"></a>Pacote de script

A entrada do pacote de script permite que você passe o conteúdo de um arquivo zip para o módulo [Executar script R][execute-r-script] . Você pode usar um dos comandos a seguir para ler o conteúdo do arquivo zip em seu código R.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Azure Machine Learning Studio (clássico) trata os arquivos no zip como se eles estivessem no diretório src/, portanto, você precisa prefixar os nomes de arquivo com esse nome de diretório. Por exemplo, se o zip contiver os arquivos `yourfile.R` e `yourData.rdata` na raiz do zip, você os trataria como `src/yourfile.R` e `src/yourData.rdata` ao usar `source` e `load`.

Já discutimos o carregamento de DataSets em [Load the DataSet](#loading). Depois de criar e testar o script R mostrado na seção anterior, faça o seguinte:

1. Salve o script R em um. Arquivo do R. Chamo meu arquivo de script "simpleplot. R ". Aqui está o conteúdo.

   ```R
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Crie um arquivo zip e copie o script para esse arquivo zip. No Windows, você pode clicar com o botão direito do mouse no arquivo e selecionar **Enviar para**e, em seguida, **pasta compactada**. Isso criará um novo arquivo ZIP contendo o "simpleplot. R "arquivo.

1. Adicione o arquivo aos **conjuntos de valores** em Azure Machine Learning Studio (clássico), especificando o tipo como **zip**. Agora você deve ver o arquivo zip em seus conjuntos de valores.

1. Arraste e solte o arquivo zip de **conjuntos de valores** na **tela ml Studio (clássica)** .

1. Conecte a saída do ícone de **dados zip** à entrada do **pacote de script** do módulo [Executar script R][execute-r-script] .

1. Digite a função `source()` com o nome do arquivo zip na janela de código do módulo [Executar script R][execute-r-script] . No meu caso, digitei `source("src/simpleplot.R")`.  

1. Certifique-se de selecionar **salvar**.

Depois que essas etapas forem concluídas, o módulo [Executar script r][execute-r-script] executará o script r no arquivo zip quando o experimento for executado. Neste ponto, seu experimento deve ser semelhante ao da Figura 5.

![Experimente usando o script de R compactado](./media/r-quickstart/fig6.png)

*Figura 5. Experimente usando o script de R compactado.*

#### <a name="dataset1"></a>DataSet1

Você pode passar uma tabela retangular de dados para o código R usando a entrada dataSet1. Em nosso script simples, a função `maml.mapInputPort(1)` lê os dados da porta 1. Esses dados são então atribuídos a um nome de variável de dataframe em seu código. Em nosso script simples, a primeira linha de código executa a atribuição.

```R
cadairydata <- maml.mapInputPort(1)
```

Execute o experimento selecionando o botão **executar** . Quando a execução for concluída, selecione o módulo [Executar script R][execute-r-script] e, em seguida, selecione **Exibir log de saída** no painel Propriedades. Uma nova página deve aparecer em seu navegador mostrando o conteúdo do arquivo output. log. Ao rolar para baixo, você verá algo semelhante ao seguinte.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

O mais distante da página é uma informação mais detalhada sobre as colunas, que será semelhante ao seguinte.

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

Esses resultados são, na maioria das vezes, como esperado, com 228 observações e 9 colunas no dataframe. Podemos ver os nomes das colunas, o tipo de dados do R e um exemplo de cada coluna.

> [!NOTE]
> Essa mesma saída impressa está convenientemente disponível na saída do dispositivo R do módulo [Executar script r][execute-r-script] . Discutiremos as saídas do módulo [Executar script R][execute-r-script] na próxima seção.  

#### <a name="dataset2"></a>Dataset2

O comportamento da entrada Dataset2 é idêntico ao do dataSet1. Usando essa entrada, você pode passar uma segunda tabela retangular de dados para o código R. A função `maml.mapInputPort(2)`, com o argumento 2, é usada para passar esses dados.  

### <a name="execute-r-script-outputs"></a>Executar saídas de script R

#### <a name="output-a-dataframe"></a>Saída de um dataframe

Você pode gerar o conteúdo de um dataframe do R como uma tabela retangular por meio da porta dataSet1 do resultado usando a função `maml.mapOutputPort()`. Em nosso script R simples, isso é executado pela linha a seguir.

```
maml.mapOutputPort('cadairydata')
```

Depois de executar o experimento, selecione a porta de saída dataSet1 do resultado e, em seguida, selecione **Visualizar**. Você deve ver algo como a Figura 6.

![A visualização da saída dos dados de laticínios da Califórnia](./media/r-quickstart/fig7.png)

*Figura 6. A visualização da saída dos dados de laticínios da Califórnia.*

Essa saída parece idêntica à entrada, exatamente como esperávamos.  

### <a name="r-device-output"></a>Saída do dispositivo R

A saída do dispositivo do módulo [Executar script R][execute-r-script] contém mensagens e saída de gráficos. A saída padrão e as mensagens de erro padrão do R são enviadas para a porta de saída do dispositivo R.  

Para exibir a saída do dispositivo R, selecione a porta e, em seguida, em **Visualizar**. Vemos a saída padrão e o erro padrão do script R na Figura 7.

![Saída padrão e erro padrão da porta do dispositivo R](./media/r-quickstart/fig8.png)

*Figura 7. Saída padrão e erro padrão da porta do dispositivo R.*

Rolando para baixo vemos a saída de gráficos do nosso script R na Figura 8.  

![Saída de gráficos da porta do dispositivo R](./media/r-quickstart/fig9.png)

*Figura 8. Saída de gráficos da porta do dispositivo R.*  

## <a id="filtering"></a>Filtragem e transformação de dados

Nesta seção, executaremos algumas operações básicas de filtragem e transformação de dados nos dados de laticínios da Califórnia. Ao final desta seção, teremos dados em um formato adequado para criar um modelo analítico.  

Mais especificamente, nesta seção, executaremos várias tarefas comuns de limpeza e transformação de dados: transformação de tipo, filtragem em dataframes, adição de novas colunas computadas e transformações de valor. Esse plano de fundo deve ajudá-lo a lidar com as diversas variações encontradas em problemas do mundo real.

O código R completo para esta seção está disponível em [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Transformações de tipo

Agora que podemos ler os dados de laticínios da Califórnia no código R no módulo [Executar script r][execute-r-script] , precisamos garantir que os dados nas colunas tenham o tipo e o formato pretendidos.  

R é uma linguagem de tipo dinâmico, o que significa que os tipos de dados são impostos de um para outro, conforme necessário. Os tipos de dados atômicos em R incluem Numeric, Logical e Character. O tipo de fator é usado para armazenar compactação de dados categóricos. Você pode encontrar muito mais informações sobre os tipos de dados nas referências em [leituras adicionais](#appendixb) abaixo.

Quando dados tabulares são lidos em R de uma fonte externa, é sempre uma boa ideia verificar os tipos resultantes nas colunas. Talvez você queira uma coluna do tipo caractere, mas em muitos casos isso será exibido como fator ou vice-versa. Em outros casos, uma coluna que você imagina deve ser numérica é representada por dados de caractere, por exemplo, ' 1,23 ' em vez de 1,23 como um número de ponto flutuante.  

Felizmente, é fácil converter um tipo em outro, desde que o mapeamento seja possível. Por exemplo, não é possível converter "Nevada" em um valor numérico, mas você pode convertê-lo em um fator (variável categórica). Como outro exemplo, você pode converter um numérico 1 em um caractere ' 1 ' ou um fator.  

A sintaxe para qualquer uma dessas conversões é simples: `as.datatype()`. Essas funções de conversão de tipo incluem o seguinte.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Observando os tipos de dados das colunas que inserimos na seção anterior: todas as colunas são do tipo Numeric, exceto pela coluna rotulada ' month ', que é do tipo Character. Vamos converter isso em um fator e testar os resultados.  

Excluí a linha que criou a matriz dispersão e adicionei uma linha convertendo a coluna ' month ' a um fator. No meu experimento, vou apenas recortar e colar o código R na janela de código do módulo [Executar script r][execute-r-script] . Você também pode atualizar o arquivo zip e carregá-lo para Azure Machine Learning Studio (clássico), mas isso requer várias etapas.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Vamos executar esse código e examinar o log de saída do script R. Os dados relevantes do log são mostrados na Figura 9.

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

*Figura 9. Resumo do dataframe com uma variável de fator.*

O tipo de mês agora deve dizer "**fator c/14 níveis**". Isso é um problema, pois há apenas 12 meses no ano. Você também pode verificar para ver se o tipo em **Visualização** da porta do conjunto de resultados de resultado é '**categórico**'.

O problema é que a coluna ' month ' não foi codificada sistematicamente. Em alguns casos, um mês é chamado de abril e, em outros, é abreviado como abr. Podemos resolver esse problema cortando a cadeia de caracteres para 3 caracteres. A linha de código agora é semelhante ao seguinte:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Execute novamente o experimento e exiba o log de saída. Os resultados esperados são mostrados na Figura 10.  

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

*Figura 10. Resumo do dataframe com o número correto de níveis de fator.*

Nossa variável de fator agora tem os 12 níveis desejados.

### <a name="basic-data-frame-filtering"></a>Filtragem de quadros de dados básicos

Os dataframes do R oferecem suporte a recursos de filtragem avançados. Os conjuntos de valores podem ser subconjuntos usando filtros lógicos em linhas ou colunas. Em muitos casos, os critérios de filtro complexos serão necessários. As referências em [leitura adicional](#appendixb) abaixo contêm exemplos extensos de filtragem de quadros de molduras.  

Há um pouco de filtragem que devemos fazer em nosso conjunto de nós. Se você examinar as colunas no dataframe do cadairydata, verá duas colunas desnecessárias. A primeira coluna mantém apenas um número de linha, o que não é muito útil. A segunda coluna, year. Month, contém informações redundantes. Podemos excluir essas colunas facilmente usando o código R a seguir.

> [!NOTE]
> De agora em diante nesta seção, mostrarei apenas o código adicional que estou adicionando ao módulo [Executar script R][execute-r-script] . Adicionarei cada nova linha **antes** da função `str()`. Eu uso essa função para verificar meus resultados em Azure Machine Learning Studio (clássico).

Adiciono a linha a seguir ao meu código R no módulo [Executar script r][execute-r-script] .

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Execute este código em seu experimento e verifique o resultado do log de saída. Esses resultados são mostrados na Figura 11.

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

*Figura 11. Resumo do dataframe com duas colunas removidas.*

Boas notícias! Obtemos os resultados esperados.

### <a name="add-a-new-column"></a>Adicionar uma nova coluna

Para criar modelos de série temporal, será conveniente ter uma coluna que contenha os meses desde o início da série temporal. Criaremos uma nova coluna ' month. Count '.

Para ajudar a organizar o código, vamos criar nossa primeira função simples, `num.month()`. Em seguida, aplicaremos essa função para criar uma nova coluna no dataframe. O novo código é o seguinte.

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

Agora, execute o experimento atualizado e use o log de saída para exibir os resultados. Esses resultados são mostrados na Figura 12.

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

*Figura 12. Resumo do dataframe com a coluna adicional.*

Parece que tudo está funcionando. Temos a nova coluna com os valores esperados em nosso dataframe.

### <a name="value-transformations"></a>Transformações de valor

Nesta seção, executaremos algumas transformações simples nos valores de algumas das colunas de nosso dataframe. A linguagem R dá suporte a transformações de valor quase arbitrárias. As referências em [leitura adicional](#appendixb) abaixo contêm exemplos extensivos.

Se você examinar os valores nos resumos de nosso dataframe, verá algo estranho aqui. Mais sorvete do que leite produzido na Califórnia? Não, claro que não, como isso não faz sentido, ele pode ser de alguns dos nós de sorvete dos EUA amantes. As unidades são diferentes. O preço está em unidades de libras dos EUA, leite está em unidades de 1 M libras, sorvete é em unidades de 1.000 galões dos EUA e Cottage queijo está em unidades de 1.000 libras. Supondo que as sorvetes de gelo pesam cerca de 6,5 libras por galão, podemos facilmente fazer a multiplicação para converter esses valores, de modo que eles fiquem todos em unidades iguais de 1.000 libras.

Para nosso modelo de previsão, usamos um modelo de multiplicativa para tendência e ajuste sazonal desses dados. Uma transformação de log nos permite usar um modelo linear, simplificando esse processo. Podemos aplicar a transformação log na mesma função em que o multiplicador é aplicado.

No código a seguir, defino uma nova função, `log.transform()`e a aplicamos às linhas que contêm os valores numéricos. A função `Map()` do R é usada para aplicar a função `log.transform()` às colunas selecionadas do dataframe. `Map()` é semelhante a `apply()`, mas permite mais de uma lista de argumentos para a função. Observe que uma lista de multiplicadores fornece o segundo argumento para a função `log.transform()`. A função `na.omit()` é usada como um pouco de limpeza para garantir que não tenhamos valores ausentes ou indefinidos no dataframe.

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

Há um pouco acontecendo na função `log.transform()`. A maior parte desse código está verificando possíveis problemas com os argumentos ou lidando com exceções, que ainda podem surgir durante os cálculos. Apenas algumas linhas desse código realmente fazem os cálculos.

O objetivo da programação defensiva é evitar a falha de uma única função que impeça o processamento de continuar. Uma falha abrupta de uma análise de execução longa pode ser bem frustrante para os usuários. Para evitar essa situação, os valores de retorno padrão devem ser escolhidos para limitar os danos ao processamento de downstream. Uma mensagem também é produzida para alertar os usuários de que algo deu errado.

Se você não for usado para a programação defensiva em R, todo esse código pode parecer um pouco difícil. Vou orientá-lo pelas principais etapas:

1. Um vetor de quatro mensagens é definido. Essas mensagens são usadas para comunicar informações sobre alguns dos possíveis erros e exceções que podem ocorrer com esse código.
2. Retorno um valor de NA para cada caso. Há muitas outras possibilidades que podem ter menos efeitos colaterais. Eu poderia retornar um vetor de zeros ou o vetor de entrada original, por exemplo.
3. As verificações são executadas nos argumentos para a função. Em cada caso, se um erro for detectado, um valor padrão será retornado e uma mensagem será produzida pela função `warning()`. Estou usando `warning()` em vez de `stop()`, pois o último terminará a execução, exatamente o que estou tentando evitar. Observe que escrevi esse código em um estilo de procedimento, como nesse caso uma abordagem funcional parecia complexa e obscura.
4. Os cálculos de log são encapsulados em `tryCatch()` para que as exceções não causem uma interrupção abrupta no processamento. Sem `tryCatch()` a maioria dos erros gerados pelas funções do R resultam em um sinal de parada, o que faz exatamente isso.

Execute este código R em seu experimento e examine a saída impressa no arquivo output. log. Agora, você verá os valores transformados das quatro colunas no log, como mostra a Figura 13.

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

*Figura 13. Resumo dos valores transformados no dataframe.*

Vemos que os valores foram transformados. A produção de leite agora excede muito a todos os outros laticínios de produção de produtos, recuperando que agora estamos examinando uma escala de log.

Neste ponto, nossos dados são limpos e estamos prontos para alguma modelagem. Examinando o resumo de visualização da saída do conjunto de resultados de resultado do nosso módulo [Executar script R][execute-r-script] , você verá que a coluna ' month ' é ' categórica ' com 12 valores exclusivos, novamente, exatamente como queremos.

## <a id="timeseries"></a>Objetos de série temporal e análise de correlação

Nesta seção, exploraremos alguns objetos básicos de série temporal do R e analisaremos as correlações entre algumas das variáveis. Nossa meta é gerar um dataframe que contenha as informações de correlação emparelhadas a vários atrasos.

O código R completo para esta seção está em [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Objetos de série temporal em R

Como já mencionado, a série temporal é uma série de valores de dados indexados por tempo. Os objetos de série temporal do R são usados para criar e gerenciar o índice de tempo. Há várias vantagens em usar objetos de série temporal. Os objetos de série temporal liberam você dos muitos detalhes do gerenciamento dos valores de índice de série temporal que são encapsulados no objeto. Além disso, os objetos de série temporal permitem que você use os vários métodos de série temporal para plotar, imprimir, modelar, etc.

A classe de série temporal POSIXct é comumente usada e é relativamente simples. Essa classe de série temporal mede o tempo desde o início da época, 1º de janeiro de 1970. Usaremos os objetos de série temporal POSIXct neste exemplo. Outras classes de objeto de série de tempo do R amplamente usadas incluem Zoo e XTS, série temporal extensível.

### <a name="time-series-object-example"></a>Exemplo de objeto de série temporal

Vamos começar com nosso exemplo. Arraste e solte um **novo** módulo [Executar script R][execute-r-script] em seu experimento. Conecte a porta de saída do dataSet1 de resultado do módulo [Executar script r][execute-r-script] existente à porta de entrada dataSet1 do novo módulo [Executar script r][execute-r-script] .

Como fiz para os primeiros exemplos, à medida que avançamos pelo exemplo, em alguns pontos mostrarei apenas as linhas adicionais incrementais do código R em cada etapa.  

#### <a name="reading-the-dataframe"></a>Lendo o dataframe

Como uma primeira etapa, vamos ler um dataframe e verificar se obtemos os resultados esperados. O código a seguir deve fazer o trabalho.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Agora, execute o experimento. O log da nova forma executar script R deve ser semelhante à figura 14.

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

*Figura 14. Resumo do dataframe no módulo executar script R.*

Esses dados são dos tipos e formato esperados. Observe que a coluna ' month ' é do tipo factor e tem o número esperado de níveis.

#### <a name="creating-a-time-series-object"></a>Criando um objeto de série temporal

Precisamos adicionar um objeto de série temporal ao nosso dataframe. Substitua o código atual pelo seguinte, que adiciona uma nova coluna da classe POSIXct.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Agora, verifique o log. Ele deve ser semelhante à figura 15.

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

*Figura 15. Resumo do dataframe com um objeto de série temporal.*

Podemos ver no resumo que a nova coluna está na verdade da classe POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Explorando e transformando os dados

Vamos explorar algumas das variáveis neste DataSet. Uma matriz dispersão é uma boa maneira de produzir uma visão rápida. Estou substituindo a função `str()` no código R anterior pela linha a seguir.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Execute este código e veja o que acontece. A plotagem produzida na porta do dispositivo R deve ser parecida com a Figura 16.

![Dispersão matriz de variáveis selecionadas](./media/r-quickstart/fig17.png)

*Figura 16. Dispersão matriz de variáveis selecionadas.*

Há uma estrutura de aparência estranha nas relações entre essas variáveis. Talvez isso surja de tendências nos dados e do fato de não ter padronizado as variáveis.

### <a name="correlation-analysis"></a>Análise de correlação

Para executar a análise de correlação, precisamos fazer a subtendência e padronizar as variáveis. Poderíamos simplesmente usar a função `scale()` do R, que centraliza e dimensiona as variáveis. Essa função pode ser bem executada mais rapidamente. No entanto, quero mostrar um exemplo de programa de defesa em R.

A função `ts.detrend()` mostrada abaixo executa ambas as operações. As duas linhas de código a seguir destendênciam os dados e, em seguida, padronizam os valores.

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

Há um pouco acontecendo na função `ts.detrend()`. A maior parte desse código está verificando possíveis problemas com os argumentos ou lidando com exceções, que ainda podem surgir durante os cálculos. Apenas algumas linhas desse código realmente fazem os cálculos.

Já discutimos um exemplo de programação defensiva em transformações de valor. Ambos os blocos de computação são encapsulados em `tryCatch()`. Para alguns erros, faz sentido retornar o vetor de entrada original e, em outros casos, retorno um vetor de zeros.  

Observe que a regressão linear usada para a subtendência é uma regressão de série temporal. A variável de previsão é um objeto de série temporal.  

Quando `ts.detrend()` é definido, podemos aplicá-lo às variáveis de interesse em nosso dataframe. Devemos forçar a lista resultante criada por `lapply()` para dataframe de dados usando `as.data.frame()`. Devido aos aspectos defensivas da `ts.detrend()`, a falha ao processar uma das variáveis não impedirá o processamento correto dos outros.  

A linha final do código cria um dispersão emparelhado. Depois de executar o código R, os resultados do dispersão são mostrados na figura 17.

![Dispersão emparelhados de série temporal padronizada e de tendência](./media/r-quickstart/fig18.png)

*Figura 17. Dispersão emparelhados de série temporal padronizada e de tendência.*

Você pode comparar esses resultados com os mostrados na Figura 16. Com a tendência removida e as variáveis padronizadas, vemos muito menos estrutura nas relações entre essas variáveis.

O código para computar as correlações como objetos R CCF é o seguinte.

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

A execução desse código produz o log mostrado na Figura 18.

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

*Figura 18. Lista de objetos CCF da análise de correlação emparelhada.*

Há um valor de correlação para cada retardo. Nenhum desses valores de correlação é grande o suficiente para ser significativo. Portanto, podemos concluir que podemos modelar cada variável de forma independente.

### <a name="output-a-dataframe"></a>Saída de um dataframe
Calculamos as correlações emparelhadas como uma lista de objetos CCF do R. Isso apresenta um pouco de problema, pois a porta de saída do conjunto de resultados de resultado realmente requer um dataframe. Além disso, o objeto CCF é, por si só, uma lista e queremos apenas os valores no primeiro elemento dessa lista, as correlações com os vários atrasos.

O código a seguir extrai os valores de retardo da lista de objetos CCF, que estão em suas listas.

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
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

A primeira linha de código é um pouco complicada e algumas explicações podem ajudá-lo a entender isso. Trabalhando de dentro para fora, temos o seguinte:

1. O operador ' **[[** ' com o argumento '**1**' seleciona o vetor de correlações no retardo do primeiro elemento da lista de objetos CCF.
2. A função `do.call()` aplica a função `rbind()` sobre os elementos da lista retornados por `lapply()`.
3. A função `data.frame()` impõe o resultado produzido por `do.call()` para um dataframe.

Observe que os nomes de linha estão em uma coluna do dataframe. Isso preserva os nomes de linha quando eles são gerados a partir do [script R de execução][execute-r-script].

A execução do código produz a saída mostrada na Figura 19 ao **Visualizar** a saída na porta do conjunto de resultados. Os nomes de linha estão na primeira coluna, conforme pretendido.

![Saída de resultados da análise de correlação](./media/r-quickstart/fig20.png)

*Figura 19. Resultados da saída da análise de correlação.*

## <a id="seasonalforecasting"></a>Exemplo de série temporal: previsão sazonal

Agora, nossos dados estão em um formato adequado para análise e determinamos que não há correlações significativas entre as variáveis. Vamos continuar e criar um modelo de previsão de série temporal. Usando esse modelo, vamos prever a produção de leite da Califórnia pelos 12 meses de 2013.

Nosso modelo de previsão terá dois componentes, um componente de tendência e um componente sazonal. A previsão completa é o produto desses dois componentes. Esse tipo de modelo é conhecido como modelo multiplicativa. A alternativa é um modelo aditivo. Já aplicamos uma transformação de log às variáveis de interesse, o que torna essa análise manejável.

O código R completo para esta seção está em [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>Criando o dataframe para análise

Comece adicionando um **novo** módulo [Executar script R][execute-r-script] ao seu experimento. Conecte a saída do conjunto de dados de **resultado** do módulo [Executar script R][execute-r-script] existente à entrada **dataSet1** do novo módulo. O resultado deve ser semelhante ao da figura 20.

![O experimento com o novo módulo executar script R adicionado](./media/r-quickstart/fig21.png)

*Figura 20. O experimento com o novo módulo executar script R adicionado.*

Assim como acontece com a análise de correlação que acabamos de concluir, precisamos adicionar uma coluna com um objeto de série temporal POSIXct. O código a seguir fará exatamente isso.

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Execute este código e examine o log. O resultado deve ser semelhante à figura 21.

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

*Figura 21. Um resumo do dataframe.*

Com esse resultado, estamos prontos para iniciar nossa análise.

### <a name="create-a-training-dataset"></a>Criar um conjunto de um DataSet de treinamento

Com o dataframe construído, precisamos criar um conjunto de um DataSet de treinamento. Esses dados incluirão todas as observações, exceto as últimas 12, do ano 2013, que é nosso conjunto de dados de teste. O código a seguir subdefine o dataframe e cria plotagens das variáveis de produção e preço de laticínios. Em seguida, crio plotagens das quatro variáveis de produção e preço. Uma função anônima é usada para definir alguns aumentos para a plotagem e, em seguida, iterar na lista dos outros dois argumentos com `Map()`. Se estiver pensando que um loop for já funcionou bem, você estará correto. Mas, como R é uma linguagem funcional, estou mostrando uma abordagem funcional.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

A execução do código produz a série de plotagens de série temporal da saída do dispositivo R mostrada na Figura 22. Observe que o eixo de tempo está em unidades de datas, uma boa vantagem do método gráfico de série temporal.

![Primeira das plotagens de série temporal dos dados de produção e de preço da Califórnia de laticínios](./media/r-quickstart/unnamed-chunk-161.png)

![Segunda das plotagens de série temporal de dados de produção e de preço de laticínios da Califórnia](./media/r-quickstart/unnamed-chunk-162.png)

![Terceira das plotagens de série temporal de dados de produção e de preço de laticínios da Califórnia](./media/r-quickstart/unnamed-chunk-163.png)

![Quarto de gráficos de série temporal de dados de produção e de preço de laticínios da Califórnia](./media/r-quickstart/unnamed-chunk-164.png)

*Figura 22. Gráficos de série temporal de dados de produção e de preço de laticínios da Califórnia.*

### <a name="a-trend-model"></a>Um modelo de tendência

Depois de criar um objeto de série temporal e ter tido uma olhada nos dados, vamos começar a construir um modelo de tendência para os dados de produção do leite da Califórnia. Podemos fazer isso com uma regressão de série temporal. No entanto, fica claro do gráfico que precisaremos de mais do que uma inclinação e interceptar para modelar com precisão a tendência observada nos dados de treinamento.

Considerando a pequena escala dos dados, criarei o modelo de tendência em RStudio e, em seguida, Recortarei e colarei o modelo resultante em Azure Machine Learning Studio (clássico). O RStudio fornece um ambiente interativo para esse tipo de análise interativa.

Como uma primeira tentativa, tentarei uma regressão polinomial com potência de até 3. Há um perigo real de sobreajuste desses tipos de modelos. Portanto, é melhor evitar os termos de ordem superior. A função `I()` inibe a interpretação do conteúdo (interpreta o conteúdo ' como está ') e permite que você escreva uma função literalmente interpretada em uma equação de regressão.

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

A partir de valores P (`Pr(>|t|)`) nessa saída, podemos ver que o termo quadrado pode não ser significativo. Usarei a função `update()` para modificar esse modelo descartando o termo quadrado.

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

Isso parece melhor. Todos os termos são significativos. No entanto, o valor de 2e-16 é um valor padrão e não deve ser levado muito a sério.  

Como um teste de sanidade, vamos fazer uma plotagem de série temporal dos dados de produção de laticínios da Califórnia com a curva de tendência mostrada. Adicionei o seguinte código na Azure Machine Learning Studio (clássico) modelo [Executar script R][execute-r-script] (não RStudio) para criar o modelo e fazer uma plotagem. O resultado é mostrado na Figura 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Dados de produção de leite da Califórnia com o modelo de tendência mostrado](./media/r-quickstart/unnamed-chunk-18.png)

*Figura 23. Dados de produção de leite da Califórnia com o modelo de tendência mostrado.*

Parece que o modelo de tendência se ajusta bem aos dados. Além disso, não parece haver evidências de sobreajuste, como ondulações estranhas na curva de modelo.  

### <a name="seasonal-model"></a>Modelo sazonal

Com um modelo de tendência em mãos, precisamos pressionar e incluir os efeitos sazonais. Usaremos o mês do ano como uma variável fictícia no modelo linear para capturar o efeito de mês a mês. Observe que quando você introduz variáveis de fator em um modelo, a interceptação não deve ser computada. Se você não fizer isso, a fórmula será mais especificada e o R removerá um dos fatores desejados, mas manterá o termo de interceptação.

Como temos um modelo de tendência satisfatório, podemos usar a função `update()` para adicionar os novos termos ao modelo existente. O-1 na fórmula de atualização descarta o termo de interceptação. Continuando em RStudio por enquanto:

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

Vemos que o modelo não tem mais um termo de interceptação e tem 12 fatores de mês significativos. Isso é exatamente o que queríamos ver.

Vamos fazer outro gráfico de série temporal dos dados de produção de laticínios da Califórnia para ver como o modelo sazonal está funcionando. Adicionei o código a seguir no Azure Machine Learning Studio (clássico) [Executar script R][execute-r-script] para criar o modelo e fazer uma plotagem.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

A execução desse código no Azure Machine Learning Studio (clássico) produz a plotagem mostrada na Figura 24.

![Produção de leite da Califórnia com modelo, incluindo efeitos sazonais](./media/r-quickstart/unnamed-chunk-20.png)

*Figura 24. Produção de leite da Califórnia com modelo, incluindo efeitos sazonais.*

O ajuste aos dados mostrados na Figura 24 é, em vez disso, incentivando. A tendência e o efeito sazonal (variação mensal) parecem razoáveis.

Como outra verificação em nosso modelo, vamos dar uma olhada nos resíduos. O código a seguir computa os valores previstos de nossos dois modelos, computa os resíduos para o modelo sazonal e, em seguida, plota esses resíduos para os dados de treinamento.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

O gráfico residual é mostrado na figura 25.

![Resíduos do modelo sazonal para os dados de treinamento](./media/r-quickstart/unnamed-chunk-21.png)

*Figura 25. Resíduos do modelo sazonal para os dados de treinamento.*

Esses resíduos parecem razoáveis. Não há nenhuma estrutura específica, exceto o efeito do 2008-2009 crise econômica, que o nosso modelo não conta muito bem.

A plotagem mostrada na figura 25 é útil para detectar quaisquer padrões dependentes de tempo nos resíduos. A abordagem explícita de computação e de plotagem dos resíduos que usei coloca os resíduos na ordem de tempo no gráfico. Se, por outro lado, eu tivesse plotado `milk.lm$residuals`, a plotagem não estaria na ordem de tempo.

Você também pode usar `plot.lm()` para produzir uma série de gráficos de diagnóstico.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Esse código produz uma série de gráficos de diagnósticos mostrados na Figura 26.

![Primeira das plotagens de diagnóstico para o modelo sazonal](./media/r-quickstart/unnamed-chunk-221.png)

![Segunda das plotagens de diagnóstico para o modelo sazonal](./media/r-quickstart/unnamed-chunk-222.png)

![Terceira das plotagens de diagnóstico para o modelo sazonal](./media/r-quickstart/unnamed-chunk-223.png)

![Quarta das plotagens de diagnóstico para o modelo sazonal](./media/r-quickstart/unnamed-chunk-224.png)

*Figura 26. Gráficos de diagnóstico para o modelo sazonal.*

Há alguns pontos altamente influentes identificados nessas plotagens, mas nada para causar uma grande preocupação. Além disso, podemos ver no gráfico Q-Q normal que os resíduos estão próximos de serem distribuídos normalmente, uma suposição importante para modelos lineares.

### <a name="forecasting-and-model-evaluation"></a>Previsão e avaliação de modelo

Há apenas mais uma coisa a fazer para concluir nosso exemplo. Precisamos calcular previsões e medir o erro em relação aos dados reais. Nossa previsão será por 12 meses de 2013. Podemos computar uma medida de erro para essa previsão para os dados reais que não fazem parte do nosso conjunto de dado de treinamento. Além disso, podemos comparar o desempenho em 18 anos de dados de treinamento para os 12 meses de dados de teste.  

Várias métricas são usadas para medir o desempenho dos modelos de série temporal. Em nosso caso, usaremos o erro de RMS (quadrado médio). A função a seguir computa o erro do RMS entre duas séries.  

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

Assim como acontece com a função `log.transform()` que discutimos na seção "transformações de valor", há muita verificação de erro e código de recuperação de exceção nessa função. Os princípios empregados são os mesmos. O trabalho é feito em dois locais dispostos em `tryCatch()`. Primeiro, a série temporal é exponenciação, já que estamos trabalhando com os logs dos valores. Em segundo lugar, o erro real do RMS é computado.  

Equipado com uma função para medir o erro do RMS, vamos criar e gerar um dataframe contendo os erros do RMS. Incluiremos termos para o modelo de tendência sozinho e o modelo completo com fatores sazonais. O código a seguir faz o trabalho usando os dois modelos lineares que construímos.

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
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

A execução desse código produz a saída mostrada na Figura 27 na porta de saída do conjunto de resultados.

![Comparação de erros do RMS para os modelos](./media/r-quickstart/fig26.png)

*Figura 27. Comparação de erros do RMS para os modelos.*

A partir desses resultados, vemos que a adição de fatores sazonais ao modelo reduz significativamente o erro do RMS. Não é surpreendentemente que o erro do RMS para os dados de treinamento seja um pouco menor do que para a previsão.

## <a id="appendixa"></a>Guia para a documentação do RStudio

RStudio é bem documentado. Aqui estão alguns links para as seções principais da documentação do RStudio para ajudá-lo a começar.

* **Criando projetos** – você pode organizar e gerenciar seu código R em projetos usando o RStudio. Consulte [usando projetos](https://support.rstudio.com/hc/articles/200526207-Using-Projects) para obter detalhes. Recomendo que você siga estas instruções e crie um projeto para os exemplos de código R neste artigo.  
* **Editando e executando o código r** -RStudio fornece um ambiente integrado para edição e execução do código r. Consulte [edição e execução de código](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) para obter detalhes.
* O **Debugging** -RStudio inclui recursos avançados de depuração. Consulte [Depurando com RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) para obter mais informações sobre esses recursos. Para obter informações sobre os recursos de solução de problemas de ponto de interrupção, consulte [pontos de solução de problemas](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)

## <a id="appendixb"></a>Leitura adicional

Este tutorial de programação R aborda as noções básicas do que você precisa para usar a linguagem R com Azure Machine Learning Studio (clássico). Se você não estiver familiarizado com o R, duas introduções estarão disponíveis em CRAN:

* [R para iniciantes](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) de Emmanuel Paradis é um bom lugar para começar.  
* [Uma introdução ao R](https://cran.r-project.org/doc/manuals/R-intro.html) por W. N. Venables et. &. apresenta um pouco mais de profundidade.

Há muitos livros sobre R que podem ajudá-lo a começar. Aqui estão alguns que acho úteis:

* A **arte da programação de R: um tour pelo design de software estatístico** por Norman Matloff é uma excelente introdução à programação em R.  
* O **r Cookbook** de Paul Teetor fornece um problema e uma abordagem de solução para o uso do R.  
* **R em ação** por Robert Kabacoff é outro livro introdutório útil. O [site de R rápido](https://www.statmethods.net/) do Companion é um recurso útil.
* **R inferno** by Patrick queimaduras é um livro surpreendentemente bem-humorada que lida com vários tópicos complicados e difíceis que podem ser encontrados durante a programação em R. O livro está disponível gratuitamente no [inferno do R](https://www.burns-stat.com/documents/books/the-r-inferno/).
* Se você quiser aprofundar-se nos tópicos avançados em R, veja o livro **Advanced R** por Hadley Wickham. A versão online deste livro está disponível gratuitamente em [http://adv-r.had.co.nz/](http://adv-r.had.co.nz/).

Um catálogo de pacotes de série temporal do R pode ser encontrado na [exibição de tarefa Cran: análise de série temporal](https://cran.r-project.org/web/views/TimeSeries.html). Para obter informações sobre pacotes de objeto de série temporal específicos, consulte a documentação do pacote.

A **série de tempo introdutório** de livros com r por Paul Cowpertwait e Andrew Metcalfe fornece uma introdução ao uso do R para análise de série temporal. Muitos outros textos teóricos fornecem exemplos de R.

Aqui estão alguns ótimos recursos da Internet:

* O datacamp ensina R no conforto do seu navegador com lições em vídeo e exercícios de código. Há tutoriais interativos sobre as técnicas e os pacotes mais recentes do R. Faça o [tutorial de R interativo](https://www.datacamp.com/courses/introduction-to-r)gratuito.
* [Aprenda a programação de R, o guia definitivo](https://www.programiz.com/r-programming) da Programiz.
* Um rápido [tutorial de R](https://www.cyclismo.org/tutorial/R/) por Kelly preto da Clarkson University.
* Há mais de 60 de recursos de R listados em [recursos de linguagem r superiores para melhorar suas habilidades de dados](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html).

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
