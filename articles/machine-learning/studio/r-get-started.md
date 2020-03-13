---
title: Começando com R
titleSuffix: ML Studio (classic) - Azure
description: Utilize este tutorial de programação R para começar a usar a linguagem R com o Azure Machine Learning Studio (clássico) para criar uma solução de previsão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 1b347707b3c656bd692a29f0fd748c1503be4fb8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218002"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio-classic"></a>Começar com a linguagem de programação R no Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Introdução

Este tutorial ajuda-o a começar a estender o Azure Machine Learning Studio (clássico) utilizando a linguagem de programação R. Siga este tutorial de programação R para criar, testar e executar código R dentro do Studio (clássico). À medida que trabalha através do tutorial, criará uma solução de previsão completa utilizando a linguagem R em Estúdio (clássico).  

O Azure Machine Learning Studio (clássico) contém muitos poderosos módulos de aprendizagem automática e manipulação de dados. A poderosa linguagem R foi descrita como a língua franca da analítica. Felizmente, a análise e manipulação de dados em Studio (clássico) podem ser estendidas usando R. Esta combinação proporciona a escalabilidade e facilidade de implantação do Studio (clássico) com a flexibilidade e análise profunda de R.

### <a name="forecasting-and-the-dataset"></a>Previsão e conjunto de dados

A previsão é um método analítico amplamente utilizado e bastante útil. As utilizações comuns vão desde a previsão de vendas de itens sazonais, a determinação dos níveis ótimos de inventário, até a previsão de variáveis macroeconómicas. A previsão é normalmente feita com modelos de séries temporais.

Os dados da série time são dados em que os valores têm um índice de tempo. O índice de tempo pode ser regular, por exemplo, todos os meses ou cada minuto, ou irregular. Um modelo de série de tempo baseia-se em dados da série de tempo. A linguagem de programação R contém um quadro flexível e uma análise extensiva para dados de séries temporais.

Neste guia vamos trabalhar com a produção de lacticínios da Califórnia e os dados de preços. Estes dados incluem informações mensais sobre a produção de vários produtos lácteos e o preço da gordura leiteira, uma mercadoria de referência.

Os dados utilizados neste artigo, juntamente com scripts R, podem ser descarregados a partir de [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Os dados do ficheiro `cadairydata.csv` foram originalmente sintetizados a partir de informações disponíveis pela Universidade de Wisconsin em [https://dairymarkets.com](https://dairymarkets.com).

### <a name="organization"></a>Organização

Vamos progredir através de vários passos à medida que aprender a criar, testar e executar analítica e manipulação de dados R código R no ambiente Azure Machine Learning Studio (clássico).  

* Primeiro vamos explorar o básico de usar a linguagem R no ambiente Azure Machine Learning Studio (clássico).
* Em seguida, progredimos para discutir vários aspetos do I/O para dados, código R e gráficos no ambiente Azure Machine Learning Studio (clássico).
* Construiremos então a primeira parte da nossa solução de previsão, criando código para a limpeza e transformação de dados.
* Com os nossos dados preparados, vamos realizar uma análise das correlações entre várias variáveis no nosso conjunto de dados.
* Por último, criaremos um modelo sazonal de previsão de séries temporais para a produção de leite.

## <a id="mlstudio"></a>Interaja com a linguagem R no Machine Learning Studio (clássico)

Esta secção leva-o através de alguns fundamentos de interação com a linguagem de programação R no ambiente do Machine Learning Studio (clássico). A linguagem R fornece uma ferramenta poderosa para criar módulos de análise e manipulação de dados personalizados dentro do ambiente Azure Machine Learning Studio (clássico).

Vou usar o RStudio para desenvolver, testar e depurar código R em pequena escala. Este código é então cortado e colado num módulo [Execute R Script][execute-r-script] pronto a ser executado no Azure Machine Learning Studio (clássico).  

### <a name="the-execute-r-script-module"></a>O módulo execute R Script

Dentro do Machine Learning Studio (clássico), os scripts R são executados dentro do módulo [Execute R Script.][execute-r-script] Um exemplo do módulo [execute R Script][execute-r-script] no Machine Learning Studio (clássico) é mostrado na Figura 1.

 ![Linguagem de programação R: O módulo Execute R Script selecionado no Machine Learning Studio (clássico)](./media/r-quickstart/fig1.png)

*Figura 1. O ambiente do Estúdio de Aprendizagem automática (clássico) que mostra o módulo execute R Script selecionado.*

Referindo-se à Figura 1, vamos ver algumas das partes-chave do ambiente do Estúdio de Aprendizagem automática (clássico) para trabalhar com o módulo [Execute R Script.][execute-r-script]

* Os módulos da experiência são mostrados no painel central.
* A parte superior do painel direito contém uma janela para visualizar e editar os seus scripts R.  
* A parte inferior do painel direito mostra algumas propriedades do [Script Executar R][execute-r-script]. Pode visualizar os registos de erro e de saída selecionando os pontos apropriados deste painel.

Vamos, naturalmente, discutir o [Guião Executar R][execute-r-script] com mais detalhes no resto deste artigo.

Ao trabalhar com funções R complexas, recomendo que edite, teste e depura no RStudio. Como em qualquer desenvolvimento de software, estenda o seu código incrementalmente e teste-o em pequenos casos de teste simples. Em seguida, corte e colue as suas funções na janela de script R do módulo [Execute R Script.][execute-r-script] Esta abordagem permite-lhe aproveitar tanto o ambiente integrado de desenvolvimento do RStudio (IDE) como o poder do Azure Machine Learning Studio (clássico).  

#### <a name="execute-r-code"></a>Executar código R

Qualquer código R no módulo [Execute R Script][execute-r-script] executará quando executar a experiência selecionando o botão **Executar.** Quando a execução estiver concluída, aparecerá uma marca de verificação no ícone [do Script Execute R.][execute-r-script]

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Codificação defensiva R para Aprendizagem automática Azure

Se estiver a desenvolver um código R para, por exemplo, um serviço web utilizando o Azure Machine Learning Studio (clássico), deverá definitivamente planear como o seu código irá lidar com uma entrada de dados inesperada e exceções. Para manter a clareza, não incluí muito na forma de verificar ou de ser tratado na maior parte dos exemplos de código apresentados. No entanto, à medida que avançamos, darei-lhe vários exemplos de funções utilizando a capacidade de manipulação de exceção de R.  

Se precisar de um tratamento mais completo do manuseamento de exceções R, recomendo que leia as secções aplicáveis do livro por Wickham listadas abaixo em [Leitura Posterior](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Debug e teste R no Machine Learning Studio (clássico)

Para reiterar, recomendo-lhe que teste e depura o seu código R em pequena escala no RStudio. No entanto, existem casos em que terá de localizar problemas de código R no [próprio Script Execute R.][execute-r-script] Além disso, é uma boa prática verificar os seus resultados no Machine Learning Studio (clássico).

A saída da execução do seu código R e da plataforma Azure Machine Learning Studio (clássica) encontra-se principalmente em output.log. Algumas informações adicionais serão vistas em erro.log.  

Se ocorrer um erro no Machine Learning Studio (clássico) durante a execução do seu código R, o seu primeiro curso de ação deve ser olhar para o error.log. Este ficheiro pode conter mensagens de erro úteis para o ajudar a compreender e corrigir o seu erro. Para visualizar error.log, selecione **Ver registo** de erro no painel de **propriedades** para o Script [Executar R][execute-r-script] contendo o erro.

Por exemplo, executei o seguinte código R, com uma variável indefinida y, num módulo [execute R Script:][execute-r-script]

```R
x <- 1.0
z <- x + y
```

Este código não executa, resultando numa condição de erro. Selecionar **O registo** de erro do ver no painel de **propriedades** produz o visor mostrado na Figura 2.

  ![Mensagem de erro aparece](./media/r-quickstart/fig2.png)

*Figura 2. Mensagem de erro pop-up.*

Parece que precisamos de ver a saída.log para ver a mensagem de erro R. Selecione o [Script Executar R][execute-r-script] e, em seguida, selecione o item **'Ver's output.log** no painel de **propriedades** à direita. Abre-se uma nova janela do navegador, e vejo o seguinte.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Esta mensagem de erro não contém surpresas e identifica claramente o problema.

Para inspecionar o valor de qualquer objeto em R, pode imprimir estes valores no ficheiro output.log. As regras para examinar os valores dos objetos são essencialmente as mesmas que numa sessão R interativa. Por exemplo, se escrever um nome variável numa linha, o valor do objeto será impresso no ficheiro output.log.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Pacotes em Machine Learning Studio (clássico)

O estúdio vem com mais de 350 pacotes de linguagem R pré-instalados. Pode utilizar o seguinte código no módulo [Execute R Script][execute-r-script] para recuperar uma lista das embalagens pré-instaladas.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Se não entende a última linha deste código no momento, leia. No resto deste artigo discutiremos extensivamente a utilização de R no ambiente (clássico).

### <a name="introduction-to-rstudio"></a>Introdução ao RStudio

RStudio é um IDE amplamente utilizado para R. Vou usar o RStudio para editar, testar e depurar parte do código R utilizado neste guia. Uma vez testado e pronto o código R, pode simplesmente cortar e colar do editor do RStudio para um módulo de Script De Aprendizagem Automática (clássico) [Executar R][execute-r-script] Script.  

Se não tiver a linguagem de programação R instalada na sua máquina de ambiente de trabalho, recomendo que o faça agora. Os downloads gratuitos de linguagem R de código aberto estão disponíveis na Rede De Arquivo R (CRAN) em [https://www.r-project.org/](https://www.r-project.org/). Existem downloads disponíveis para Windows, Mac OS e Linux/UNIX. Escolha um espelho próximo e siga as instruções de descarregamento. Além disso, cran contém uma riqueza de pacotes úteis de análise e manipulação de dados.

Se é novo no RStudio, deve descarregar e instalar a versão para desktop. Pode encontrar os downloads do RStudio para Windows, Mac OS e Linux/UNIX em http://www.rstudio.com/products/RStudio/. Siga as instruções fornecidas para instalar o RStudio na sua máquina de ambiente de trabalho.  

Uma introdução tutorial ao RStudio está disponível na [Utilização do RStudio IDE.](https://support.rstudio.com/hc/sections/200107586-Using-RStudio)

Eu forneço algumas informações adicionais sobre a utilização do RStudio em [guia para a documentação](#appendixa) do RStudio abaixo.  

## <a id="scriptmodule"></a>Obtenha dados dentro e fora do módulo execute R Script

Nesta secção discutiremos como obtém dados dentro e fora do módulo [Execute R Script.][execute-r-script] Vamos rever como lidar com vários tipos de dados lidos dentro e fora do módulo [Execute R Script.][execute-r-script]

O código completo para esta secção está em [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data-in-machine-learning-studio-classic"></a>Carregue e verifique os dados no Estúdio de Aprendizagem automática (clássico)

#### <a id="loading"></a>Carregar o conjunto de dados

Começaremos por carregar o ficheiro **csdairydata.csv** no Azure Machine Learning Studio (clássico).

1. Inicie o seu ambiente Azure Machine Learning Studio (clássico).
1. Selecione **+ NOVO** na parte inferior esquerda do ecrã e selecione **Dataset**.
1. Selecione **a partir do Arquivo Local**e, em seguida, **navegue** para selecionar o ficheiro.
1. Certifique-se de que selecionou o **ficheiro Genérico CSV com cabeçalho (.csv)** como o tipo para o conjunto de dados.
1. Selecione a marca de verificação.
1. Depois de o conjunto de dados ter sido carregado, deverá ver o novo conjunto de dados selecionando o separador **Datasets.**  

#### <a name="create-an-experiment"></a>Criar uma experimentação

Agora que temos alguns dados no Machine Learning Studio (clássico), precisamos de criar uma experiência para fazer a análise.  

1. **Selecione + NOVO** na esquerda inferior e selecione **Experimento,** em seguida, **Experiência em Branco**.
1. Pode nomear a sua experiência selecionando e modificando, a **Experiência criada no ...** título no topo da página. Por exemplo, mudá-lo para **CA Dairy Analysis**.
1. À esquerda da página de experimentação, expanda os Conjuntos de **Dados Guardados,** e depois **os Meus Conjuntos**de Dados . Deve ver o **cadairydata.csv** que carregou anteriormente.
1. Arraste e deixe cair o conjunto de **dados csdairydata.csv** na experiência.
1. Na caixa de itens de **experiência de pesquisa** na parte superior do painel esquerdo, escreva script Executar [R][execute-r-script]. Verá o módulo aparecer na lista de pesquisa.
1. Arraste e deixe cair o módulo [execute R Script][execute-r-script] na sua palete.  
1. Ligue a saída do conjunto de **dados csdairydata.csv** à entrada mais à esquerda **(Dataset1**) do [Script Executar R][execute-r-script].
1. **Não se esqueça de selecionar 'Guardar'!**  

Neste momento, a sua experiência deve parecer algo como a Figura 3.

![A experiência de análise de lacticínios ca com dataset e execute módulo script R](./media/r-quickstart/fig3.png)

*Figura 3. A experiência de análise de lacticínios CA com dataset e execute o módulo script R.*

#### <a name="check-on-the-data"></a>Verifique os dados

Vamos ver os dados que carregamos na nossa experiência. Na experiência, selecione a saída do conjunto de **dados cadairydata.csv** e selecione **visualizar**. Devia ver algo como a Figura 4.  

![Resumo do conjunto de dados cadairydata.csv](./media/r-quickstart/fig4.png)

*Figura 4. Resumo do conjunto de dados cadairydata.csv.*

Neste ponto de vista, vemos muita informação útil. Podemos ver as primeiras linhas desse conjunto de dados. Se selecionarmos uma coluna, a secção estatísticas mostra mais informações sobre a coluna. Por exemplo, a linha Feature Type mostra-nos quais os tipos de dados Azure Machine Learning Studio (clássico) atribuídos à coluna. Ter um aspeto rápido como este é um bom cheque de sanidade antes de começarmos a fazer qualquer trabalho sério.

### <a name="first-r-script"></a>Primeiro script de R

Vamos criar um simples primeiro guião R para experimentar dentro do Azure Machine Learning Studio (clássico). Criei e testei o seguinte guião no RStudio.  

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

Agora preciso transferir este guião para o Azure Machine Learning Studio (clássico). Podia simplesmente cortar e colar. No entanto, neste caso, vou transferir o meu guião R através de um ficheiro zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Entrada de dados para o módulo execute R Script

Vamos ver as inputs do módulo [Execute R Script.][execute-r-script] Neste exemplo, vamos ler os dados lácteos da Califórnia no módulo [Execute R Script.][execute-r-script]  

Existem três possíveis inputs para o módulo [Execute R Script.][execute-r-script] Pode utilizar qualquer uma ou todas estas inputs, dependendo da sua aplicação. Também é perfeitamente razoável usar um script R que não tem nenhuma entrada.  

Vamos ver cada uma destas inputs, indo da esquerda para a direita. Pode ver os nomes de cada uma das inputs colocando o cursor sobre a entrada e lendo a dica da ferramenta.  

#### <a name="script-bundle"></a>Script Bundle

A entrada script bundle permite-lhe passar o conteúdo de um ficheiro zip para o módulo [Execute R Script.][execute-r-script] Pode utilizar um dos seguintes comandos para ler o conteúdo do ficheiro postal no seu código R.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> O Azure Machine Learning Studio (clássico) trata os ficheiros no fecho como se estivessem no src/diretório, pelo que tem de prepor os nomes dos seus ficheiros com este nome de diretório. Por exemplo, se o fecho contiver os ficheiros `yourfile.R` e `yourData.rdata` na raiz do fecho, abordará estes como `src/yourfile.R` e `src/yourData.rdata` ao utilizar `source` e `load`.

Já discutimos o carregamento de conjuntos de dados em [Carregar o conjunto de dados](#loading). Depois de ter criado e testado o script R mostrado na secção anterior, faça o seguinte:

1. Guarde o script R num . Ficheiro R. Chamo o meu ficheiro de guião de "enredo simples". R". Aqui está o conteúdo.

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

1. Crie um ficheiro zip e copie o seu script neste ficheiro zip. No Windows, pode clicar no ficheiro e selecionar **Enviar para**, e depois **pasta comprimido**. Isto criará um novo ficheiro zip contendo o "enredo simples. Ficheiro R".

1. Adicione o seu ficheiro aos **conjuntos** de dados no Azure Machine Learning Studio (clássico), especificando o tipo como **zip**. Deverá agora ver o ficheiro zip nos seus conjuntos de dados.

1. Arraste e deixe cair o ficheiro zip dos **conjuntos** de dados para a tela do **ESTÚDIO ML (clássico).**

1. Ligue a saída do ícone de **dados zip** à entrada do **Script Bundle** do módulo Execute [R Script.][execute-r-script]

1. Digite a função `source()` com o nome do ficheiro postal na janela de código para o módulo [Execute R Script.][execute-r-script] No meu caso, escrevi `source("src/simpleplot.R")`.  

1. Certifique-se de que seleciona **Guardar**.

Uma vez concluídos estes passos, o módulo [Execute R Script][execute-r-script] executará o script R no ficheiro zip quando a experiência for executada. Neste momento, a sua experiência deve parecer algo como a Figura 5.

![Experiência usando script R zipped](./media/r-quickstart/fig6.png)

*Figura 5. Experimente usando o script R com fecho.*

#### <a name="dataset1"></a>Dataset1

Pode passar uma tabela retangular de dados para o seu código R utilizando a entrada Dataset1. No nosso roteiro simples, a função `maml.mapInputPort(1)` lê os dados da porta 1. Estes dados são então atribuídos a um nome variável dataframe no seu código. No nosso simples guião, a primeira linha de código executa a atribuição.

```R
cadairydata <- maml.mapInputPort(1)
```

Execute a sua experiência selecionando o botão **Executar.** Quando a execução terminar, selecione o módulo [Execute R Script][execute-r-script] e, em seguida, selecione Ver o registo de **saída** no painel de propriedades. Uma nova página deve aparecer no seu navegador mostrando o conteúdo do ficheiro output.log. Quando se desliza para baixo, deve ver algo como o seguinte.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Mais abaixo na página está uma informação mais detalhada sobre as colunas, que se parecerão com as seguintes.

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

Estes resultados são principalmente como esperado, com 228 observações e 9 colunas no quadro de dados. Podemos ver os nomes das colunas, o tipo de dados R e uma amostra de cada coluna.

> [!NOTE]
> Esta mesma saída impressa está convenientemente disponível a partir da saída do Dispositivo R do módulo [Execute R Script.][execute-r-script] Discutiremos as saídas do módulo [Execute R Script][execute-r-script] na secção seguinte.  

#### <a name="dataset2"></a>Dataset2

O comportamento da entrada Dataset2 é idêntico ao do Dataset1. Utilizando esta entrada pode passar uma segunda tabela retangular de dados no seu código R. A função `maml.mapInputPort(2)`, com o argumento 2, é usada para passar estes dados.  

### <a name="execute-r-script-outputs"></a>Executar saídas de script R

#### <a name="output-a-dataframe"></a>Produção de um quadro de dados

Pode ser de saída do conteúdo de um quadro de dados R como uma tabela retangular através da porta De dados de resultados utilizando a função `maml.mapOutputPort()`. No nosso simples guião R este é realizado pela seguinte linha.

```
maml.mapOutputPort('cadairydata')
```

Depois de executar a experiência, selecione a porta de saída Do Resultado Dataset1 e, em seguida, **selecione Visualize**. Devia ver algo como a Figura 6.

![A visualização da produção dos dados dos lacticínios da Califórnia](./media/r-quickstart/fig7.png)

*Figura 6. A visualização da produção dos dados dos lacticínios da Califórnia.*

Esta saída parece idêntica à entrada, exatamente como esperávamos.  

### <a name="r-device-output"></a>Saída do dispositivo R

A saída do Dispositivo do módulo [Execute R Script][execute-r-script] contém mensagens e saída gráfica. Tanto as mensagens padrão de saída como as mensagens de erro padrão de R são enviadas para a porta de saída do Dispositivo R.  

Para visualizar a saída do Dispositivo R, selecione a porta e, em seguida, no **Visualize**. Vemos a saída padrão e o erro padrão do script R na Figura 7.

![Saída padrão e erro padrão da porta r dispositivo](./media/r-quickstart/fig8.png)

*Figura 7. Saída padrão e erro padrão da porta do dispositivo R.*

Percorrendo para baixo vemos a saída gráfica do nosso script R na Figura 8.  

![Saída gráfica da porta do dispositivo R](./media/r-quickstart/fig9.png)

*Figura 8. Saída gráfica da porta do dispositivo R.*  

## <a id="filtering"></a>Filtragem e transformação de dados

Nesta secção, realizaremos algumas operações básicas de filtragem e transformação de dados nos dados dos lacticínios da Califórnia. No final desta secção teremos dados num formato adequado para a construção de um modelo analítico.  

Mais especificamente, nesta secção vamos executar várias tarefas comuns de limpeza e transformação de dados: transformação de tipos, filtragem em quadros de dados, adição de novas colunas computadas e transformações de valor. Este pano de fundo deve ajudá-lo a lidar com as muitas variações encontradas nos problemas do mundo real.

O código R completo para esta secção está disponível em [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Transformações de tipo

Agora que podemos ler os dados lácteos da Califórnia no código R no módulo [Execute R Script,][execute-r-script] precisamos garantir que os dados nas colunas têm o tipo e formato pretendidos.  

R é uma linguagem dinamicamente dactilografada, o que significa que os tipos de dados são coagidos de um para o outro, conforme necessário. Os tipos de dados atómicos em R incluem numérico, lógico e caráter. O tipo de fator é usado para armazenar compactamente dados categóricos. Pode encontrar muito mais informação sobre os tipos de dados nas referências em [Ler](#appendixb) abaixo.

Quando os dados tabular são lidos em R a partir de uma fonte externa, é sempre uma boa ideia verificar os tipos resultantes nas colunas. Você pode querer uma coluna de tipo de caráter, mas em muitos casos isso vai aparecer como fator ou vice-versa. Noutros casos, uma coluna que se deve pensar que deve ser numérica é representada por dados de carácter, por exemplo, '1.23' em vez de 1,23 como número de ponto flutuante.  

Felizmente, é fácil converter um tipo para outro, desde que o mapeamento seja possível. Por exemplo, não se pode converter 'Nevada' num valor numérico, mas pode convertê-lo num fator (variável categórica). Como outro exemplo, pode converter um numérico 1 num éum num personagem '1' ou um fator.  

A sintaxe para qualquer uma destas conversões é simples: `as.datatype()`. Estas funções de conversão de tipos incluem as seguintes funções.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Olhando para os tipos de dados das colunas que inserimos na secção anterior: todas as colunas são de tipo numérico, com exceção da coluna com a etiqueta "Mês", que é de caráter tipo. Vamos converter isto num fator e testar os resultados.  

Apaguei a linha que criou a matriz de dispersão e adicionei uma linha que converte a coluna "Mês" num fator. Na minha experiência, vou cortar e colar o código R na janela de código do Módulo [de Script Execute R.][execute-r-script] Também pode atualizar o ficheiro zip e carregá-lo para o Azure Machine Learning Studio (clássico), mas isto dá vários passos.  

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

Vamos executar este código e olhar para o registo de saída para o script R. Os dados relevantes do registo são mostrados na Figura 9.

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

*Figura 9. Resumo do quadro de dados com uma variável fator.*

O tipo de Mês deve agora dizer '**Fator c/ 14 níveis**'. Este é um problema, uma vez que há apenas 12 meses por ano. Também pode verificar se o tipo em **Visualização** da porta Dedados do Resultado é '**Categórico**'.

O problema é que a coluna "Mês" não foi codificada sistematicamente. Em alguns casos, um mês chama-se abril e noutros é abreviado como abr. Podemos resolver este problema cortando a corda para 3 caracteres. A linha de código agora parece ser a seguinte:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Refaça a experiência e veja o registo de saída. Os resultados esperados são apresentados na Figura 10.  

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

*Figura 10. Resumo do quadro de dados com o número correto de níveis de fator.*

A nossa variável fator tem agora os 12 níveis desejados.

### <a name="basic-data-frame-filtering"></a>Filtragem de quadros de dados básicos

Os quadros de dados R suportam poderosas capacidades de filtragem. Os conjuntos de dados podem ser subconjuntos utilizando filtros lógicos em linhas ou colunas. Em muitos casos, serão necessários critérios complexos de filtro. As referências em [leitura posterior](#appendixb) contêm extensos exemplos de filtragem de quadros de dados.  

Há um pouco de filtragem que devemos fazer no nosso conjunto de dados. Se olhar para as colunas no quadro de dados cadairydata, verá duas colunas desnecessárias. A primeira coluna tem apenas um número de linha, o que não é muito útil. A segunda coluna, Ano.Mês, contém informações redundantes. Podemos facilmente excluir estas colunas utilizando o seguinte código R.

> [!NOTE]
> A partir de agora, nesta secção, vou mostrar-lhe o código adicional que estou adicionando no módulo [Execute R Script.][execute-r-script] Vou adicionar cada nova linha **antes** da função `str()`. Uso esta função para verificar os meus resultados no Azure Machine Learning Studio (clássico).

Adiciono a seguinte linha ao meu código R no módulo [Execute R Script.][execute-r-script]

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Faça este código na sua experiência e verifique o resultado do registo de saída. Estes resultados são mostrados na Figura 11.

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

*Figura 11. Resumo do quadro de dados com duas colunas removidas.*

Boas notícias! Temos os resultados esperados.

### <a name="add-a-new-column"></a>Adicione uma nova coluna

Para criar modelos de séries temporais será conveniente ter uma coluna que contenha os meses desde o início da série de horários. Criaremos uma nova coluna "Mês.Conde".

Para ajudar a organizar o código, criaremos a nossa primeira função simples, `num.month()`. Aplicaremos então esta função para criar uma nova coluna no quadro de dados. O novo código é o seguinte.

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

Agora faça a experiência atualizada e use o registo de saída para visualizar os resultados. Estes resultados são mostrados na Figura 12.

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

*Figura 12. Resumo do quadro de dados com a coluna adicional.*

Parece que está tudo a funcionar. Temos a nova coluna com os valores esperados no nosso dataframe.

### <a name="value-transformations"></a>Transformações de valor

Nesta secção vamos realizar algumas transformações simples sobre os valores em algumas das colunas do nosso dataframe. A linguagem R suporta transformações de valor quase arbitrárias. As referências em [leitura posterior](#appendixb) contêm exemplos extensos.

Se olharmos para os valores nos resumos do nosso dataframe, deve ver algo estranho aqui. É mais gelado do que leite produzido na Califórnia? Não, claro que não, como isto não faz sentido, porque este facto pode ser para alguns de nós, amantes de gelados. As unidades são diferentes. O preço está em unidades de libras americanas, o leite está em unidades de 1 M us pounds, o gelado está em unidades de 1.000 galões americanos, e o queijo cottage está em unidades de 1.000 libras americanas. Assumindo que o gelado pesa cerca de 6,5 libras por galão, podemos facilmente fazer a multiplicação para converter estes valores para que todos estejam em unidades iguais de 1.000 libras.

Para o nosso modelo de previsão utilizamos um modelo multiplicador para a tendência e ajuste sazonal destes dados. Uma transformação de registo permite-nos usar um modelo linear, simplificando este processo. Podemos aplicar a transformação do registo na mesma função em que o multiplicador é aplicado.

No seguinte código, defino uma nova função, `log.transform()`, e aplico-a às linhas que contêm os valores numéricos. A função R `Map()` é utilizada para aplicar a função `log.transform()` às colunas selecionadas do quadro de dados. `Map()` é semelhante a `apply()` mas permite mais de uma lista de argumentos para a função. Note que uma lista de multiplicadores fornece o segundo argumento à função `log.transform()`. A função `na.omit()` é usada como um pouco de limpeza para garantir que não temos valores em falta ou indefinidos no quadro de dados.

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

Há muita coisa a acontecer na função `log.transform()`. A maior parte deste código está a verificar potenciais problemas com os argumentos ou a lidar com exceções, que ainda podem surgir durante os cálculos. Apenas algumas linhas deste código realmente fazem os cálculos.

O objetivo da programação defensiva é evitar o insucesso de uma única função que impeça o processamento de continuar. Uma falha abrupta de uma análise de longa duração pode ser bastante frustrante para os utilizadores. Para evitar esta situação, devem ser escolhidos valores de devolução por defeito que limitarão os danos ao processamento a jusante. Também é produzida uma mensagem para alertar os utilizadores de que algo correu mal.

Se não estiver habituado à programação defensiva em R, todo este código pode parecer um pouco avassalador. Vou acompanhá-lo pelos degraus principais:

1. Um vetor de quatro mensagens é definido. Estas mensagens são usadas para comunicar informações sobre alguns dos possíveis erros e exceções que podem ocorrer com este código.
2. Devolvo um valor de NA para cada caso. Há muitas outras possibilidades que podem ter menos efeitos colaterais. Eu poderia devolver um vetor de zeros, ou o vetor de entrada original, por exemplo.
3. São efetuados controlos sobre os argumentos da função. Em cada caso, se for detetado um erro, um valor predefinido é devolvido e uma mensagem é produzida pela função `warning()`. Estou a usar `warning()` em vez de `stop()`, uma vez que esta acabará com a execução, exatamente o que estou a tentar evitar. Note-se que escrevi este código num estilo processual, pois neste caso uma abordagem funcional parecia complexa e obscura.
4. Os cálculos de registo são embrulhados em `tryCatch()` para que as exceções não causem uma paragem abrupta no processamento. Sem `tryCatch()` maioria dos erros levantados pelas funções R resultam num sinal de stop, o que faz isso mesmo.

Execute este código R na sua experiência e veja a saída impressa no ficheiro output.log. Verá agora os valores transformados das quatro colunas no registo, como mostra a Figura 13.

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

*Figura 13. Resumo dos valores transformados no quadro de dados.*

Vemos que os valores foram transformados. A produção de leite ultrapassa agora largamente todas as outras produções de produtos lácteos, recordando que estamos agora a olhar para uma escala de troncos.

Neste momento os nossos dados estão limpos e estamos prontos para alguma modelação. Olhando para o resumo de visualização para a saída do Resultado Dataset do nosso módulo [Execute R Script,][execute-r-script] verá que a coluna 'Mês' é 'Categórica' com 12 valores únicos, mais uma vez, tal como queríamos.

## <a id="timeseries"></a>Objetos da série de tempo e análise de correlação

Nesta secção vamos explorar alguns objetos básicos da série r e analisar as correlações entre algumas das variáveis. O nosso objetivo é obter um quadro de dados contendo a informação de correlação em pares em vários lags.

O código R completo para esta secção está em [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Objetos da série de tempo em R

Como já referido, as séries temporais são uma série de valores de dados indexados pelo tempo. Os objetos da série R são usados para criar e gerir o índice de tempo. Existem várias vantagens em usar objetos da série de tempo. Os objetos da série de tempo libertam-no dos muitos detalhes de gestão dos valores de índice da série de tempo que são encapsuados no objeto. Além disso, os objetos da série de tempo permitem-lhe utilizar os métodos de série seleções de muitos tempos para conspiração, impressão, modelação, etc.

A classe de séries temporais POSIXct é comumente usada e é relativamente simples. Esta classe de séries de tempo mede o tempo desde o início da época, 1 de janeiro de 1970. Usaremos objetos da série de tempo POSIXct neste exemplo. Outras classes de objetos de série r amplamente utilizadas incluem zoológico e xts, séries de tempo extensíveis.

### <a name="time-series-object-example"></a>Exemplo de objeto de série de tempo

Vamos começar com o nosso exemplo. Arraste e deixe cair um **novo** módulo [execute R Script][execute-r-script] na sua experiência. Ligue a porta de saída do Resultado Dataset1 do módulo execute [R Script][execute-r-script] existente à porta de entrada Dataset1 do novo módulo execute [R Script.][execute-r-script]

Tal como fiz nos primeiros exemplos, à medida que avançamos através do exemplo, em alguns pontos mostrarei apenas as linhas adicionais incrementais do código R em cada passo.  

#### <a name="reading-the-dataframe"></a>Ler o dataframe

Como primeiro passo, vamos ler num quadro de dados e certificar-nos de que obtemos os resultados esperados. O seguinte código deve fazer o trabalho.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Agora, executa a experiência. O registo da nova forma do Script Execute R deve parecer a Figura 14.

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

*Figura 14. Resumo do quadro de dados no módulo execute R Script.*

Estes dados são dos tipos e formato sinuosos. Note que a coluna 'Mês' é de fator tipo e tem o número esperado de níveis.

#### <a name="creating-a-time-series-object"></a>Criar um objeto de série de tempo

Precisamos adicionar um objeto de série de tempo ao nosso dataframe. Substitua o código atual pelo seguinte, que adiciona uma nova coluna de classe POSIXct.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Agora, verifique o registo. Deve parecer a Figura 15.

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

*Figura 15. Resumo do dataframe com um objeto de série de tempo.*

Podemos ver pelo resumo que a nova coluna é, de facto, de classe POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Explorar e transformar os dados

Vamos explorar algumas das variáveis neste conjunto de dados. Uma matriz de enredo disperso é uma boa maneira de produzir um olhar rápido. Estou a substituir a função `str()` no código R anterior pela seguinte linha.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Executa este código e veja o que acontece. O enredo produzido na porta do Dispositivo R deve parecer a Figura 16.

![Matriz de dispersão de variáveis selecionadas](./media/r-quickstart/fig17.png)

*Figura 16. Matriz de dispersão de variáveis selecionadas.*

Há uma estrutura estranha nas relações entre estas variáveis. Talvez isto decorra da evolução dos dados e do facto de não termos normalizado as variáveis.

### <a name="correlation-analysis"></a>Análise de correlação

Para realizar a análise da correlação, precisamos tanto de destendência e padronização das variáveis. Poderíamos simplesmente usar a função R `scale()`, que tanto centros como escala variáveis. Esta função pode muito bem correr mais rápido. No entanto, quero mostrar-lhe um exemplo de programação defensiva em R.

A função `ts.detrend()` mostrada abaixo executa ambas as operações. As duas linhas seguintes de código destendência os dados e, em seguida, padronizar os valores.

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

Há muita coisa a acontecer na função `ts.detrend()`. A maior parte deste código está a verificar potenciais problemas com os argumentos ou a lidar com exceções, que ainda podem surgir durante os cálculos. Apenas algumas linhas deste código realmente fazem os cálculos.

Já discutimos um exemplo de programação defensiva em transformações de valor. Ambos os blocos de cálculo estão embrulhados em `tryCatch()`. Para alguns erros faz sentido devolver o vetor de entrada original, e em outros casos, eu retome um vetor de zeros.  

Note que a regressão linear usada para destendência é uma regressão da série temporal. A variável do preditor é um objeto de série de tempo.  

Uma vez definida`ts.detrend()`, aplicamo-la às variáveis de interesse no nosso dataframe. Temos de coagir a lista resultante criada por `lapply()` ao dataframe utilizando `as.data.frame()`. Devido a aspetos defensivos de `ts.detrend()`, o não processamento de uma das variáveis não impedirá o processamento correto das outras.  

A linha final de código cria uma conspiração de dispersão em pares. Após a execução do código R, os resultados do lote de dispersão são mostrados na Figura 17.

![Pairwise scatterplot de séries de tempo destendências e padronizadas](./media/r-quickstart/fig18.png)

*Figura 17. Pairwise scatterplot de séries de tempo destendências e padronizadas.*

Pode comparar estes resultados com os apresentados na Figura 16. Com a tendência removida e as variáveis padronizadas, vemos muito menos estrutura nas relações entre estas variáveis.

O código para calcular as correlações como objetos R ccf é o seguinte.

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

A execução deste código produz o registo mostrado na Figura 18.

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

*Figura 18. Lista de objetos ccf da análise de correlação em pares.*

Há um valor de correlação para cada lag. Nenhum destes valores de correlação é grande o suficiente para ser significativo. Podemos, portanto, concluir que podemos modelar cada variável de forma independente.

### <a name="output-a-dataframe"></a>Produção de um quadro de dados
Calculamos as correlações em pares como uma lista de objetos R ccf. Isto apresenta um pequeno problema, uma vez que a porta de saída do Resultado Dataset requer realmente um dataframe. Além disso, o objeto ccf é em si uma lista e queremos apenas os valores no primeiro elemento desta lista, as correlações nos vários lags.

O código que se segue extrai os valores de lag da lista de objetos ccf, que são eles próprios listas.

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

A primeira linha de código é um pouco complicada, e alguma explicação pode ajudá-lo a compreendê-lo. Trabalhando de dentro para fora temos o seguinte:

1. O **[[** operador com o argumento **' 1**' seleciona o vetor de correlações nos lags do primeiro elemento da lista de objetos do CCF.
2. A função `do.call()` aplica a função `rbind()` sobre os elementos da lista de retornos por `lapply()`.
3. A função `data.frame()` coagia o resultado produzido por `do.call()` a um dataframe.

Note que os nomes das linhas estão numa coluna do quadro de dados. Ao fazê-lo, preserva os nomes das linhas quando são saídas do [Script Executar R][execute-r-script].

Executar o código produz a saída mostrada na Figura 19 quando **visualizar** a saída na porta de dados de resultados. Os nomes das fileiras estão na primeira coluna, como pretendido.

![Resultados da análise da correlação](./media/r-quickstart/fig20.png)

*Figura 19. Resultados da análise da correlação.*

## <a id="seasonalforecasting"></a>Exemplo da série temporal: previsão sazonal

Os nossos dados estão agora numa forma adequada para análise, e determinámos que não há correlações significativas entre as variáveis. Vamos seguir em frente e criar um modelo de previsão de séries temporais. Utilizando este modelo, vamos prever a produção de leite da Califórnia para os 12 meses de 2013.

O nosso modelo de previsão terá dois componentes, um componente de tendência e um componente sazonal. A previsão completa é o produto destes dois componentes. Este tipo de modelo é conhecido como um modelo multiplicador. A alternativa é um modelo aditivo. Já aplicámos uma transformação de registo às variáveis de interesse, o que torna esta análise tratável.

O código R completo para esta secção está em [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>Criação do dataframe para análise

Comece por adicionar um **novo** módulo [execute R Script][execute-r-script] à sua experiência. Ligue a saída do Conjunto de Dados de **Resultados** do módulo [execute R Script][execute-r-script] existente à entrada **dataset1** do novo módulo. O resultado deve parecer algo como a Figura 20.

![A experiência com o novo módulo Execute R Script adicionado](./media/r-quickstart/fig21.png)

*Figura 20. A experiência com o novo módulo Execute R Script adicionado.*

Tal como na análise de correlação que acabámos de completar, precisamos de adicionar uma coluna com um objeto de série de tempo POSIXct. O seguinte código fará isto.

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Execute este código e olhe para o registo. O resultado deve parecer a Figura 21.

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

*Figura 21. Um resumo do quadro de dados.*

Com este resultado, estamos prontos para iniciar a nossa análise.

### <a name="create-a-training-dataset"></a>Criar um conjunto de dados de formação

Com o dataframe construído, precisamos criar um conjunto de dados de treino. Estes dados incluirão todas as observações, exceto as últimas 12, do ano de 2013, que é o nosso conjunto de dados de teste. O código seguinte subpõe o quadro de dados e cria parcelas da produção de lacticínios e variáveis de preços. Em seguida, crio parcelas das quatro variáveis de produção e preço. Uma função anónima é usada para definir alguns aumentos para enredo, e depois iterar sobre a lista dos outros dois argumentos com `Map()`. Se está supor que um "for loop" teria funcionado bem aqui, tem razão. Mas, como r é uma linguagem funcional, estou a mostrar-lhe uma abordagem funcional.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Executar o código produz a série de parcelas da série de tempo a partir da saída do Dispositivo R mostrada na Figura 22. Note que o eixo do tempo está em unidades de datas, um bom benefício do método do enredo da série de tempo.

![Primeira série de tempos de produção de lacticínios da Califórnia e dados de preços](./media/r-quickstart/unnamed-chunk-161.png)

![Segunda série de tempo saques de produção de lacticínios da Califórnia e dados de preços](./media/r-quickstart/unnamed-chunk-162.png)

![Terceiro dos lotes da série de tempo saqueia os dados de produção de lacticínios da Califórnia e os preços](./media/r-quickstart/unnamed-chunk-163.png)

![Quarta série de tempos de produção de lacticínios da Califórnia e dados de preços](./media/r-quickstart/unnamed-chunk-164.png)

*Figura 22. Lotes de séries de tempo da produção de lacticínios da Califórnia e dados de preços.*

### <a name="a-trend-model"></a>Um modelo de tendência

Tendo criado um objeto de série de tempo e tendo olhado para os dados, vamos começar a construir um modelo de tendência para os dados de produção de leite da Califórnia. Podemos fazer isto com uma regressão da série temporal. No entanto, é claro no enredo que vamos precisar de mais do que um declive e intercetar para modelar com precisão a tendência observada nos dados de formação.

Dada a pequena escala dos dados, construirei o modelo para a tendência no RStudio e depois cortarei e colarei o modelo resultante no Azure Machine Learning Studio (clássico). O RStudio proporciona um ambiente interativo para este tipo de análise interativa.

Como primeira tentativa, tentarei uma regressão polinomial com poderes até 3. Existe o perigo real de se adaptar excessivamente a este tipo de modelos. Portanto, o melhor é evitar termos de alta ordem. A função `I()` inibe a interpretação do conteúdo (interpreta o conteúdo 'como está') e permite-lhe escrever uma função literalmente interpretada numa equação de regressão.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Isto gera o seguinte.

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

A partir dos valores P (`Pr(>|t|)`) nesta saída, podemos ver que o termo quadrado pode não ser significativo. Vou usar a função `update()` para modificar este modelo, deixando cair o termo quadrado.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Isto gera o seguinte.

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

Isto parece melhor. Todos os termos são significativos. No entanto, o valor 2e-16 é um valor predefinido, e não deve ser levado muito a sério.  

Como um teste de sanidade, vamos fazer um enredo de série de tempo dos dados de produção de lacticínios da Califórnia com a curva de tendência mostrada. Adicionei o seguinte código no Azure Machine Learning Studio (clássico) [Executar modelo De Script R][execute-r-script] (não RStudio) para criar o modelo e fazer um enredo. O resultado é mostrado na Figura 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Dados da produção de leite da Califórnia com modelo de tendência mostrado](./media/r-quickstart/unnamed-chunk-18.png)

*Figura 23. Dados de produção de leite da Califórnia com modelo de tendência mostrado.*

Parece que o modelo de tendência se encaixa bastante bem nos dados. Além disso, não parece haver provas de excesso de adaptação, como movimentos ímpares na curva do modelo.  

### <a name="seasonal-model"></a>Modelo sazonal

Com um modelo de tendência na mão, precisamos de continuar e incluir os efeitos sazonais. Usaremos o mês do ano como uma variável manequim no modelo linear para capturar o efeito mensal. Note que quando introduz variáveis de fator num modelo, a interceção não deve ser calculada. Se não o fizer, a fórmula está sobreespecificada e R deixará cair um dos fatores desejados, mas manterá o termo de interceção.

Como temos um modelo de tendência satisfatório, podemos usar a função `update()` para adicionar os novos termos ao modelo existente. O -1 na fórmula de atualização deixa cair o termo de interceção. Continuando no RStudio por enquanto:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Isto gera o seguinte.

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

Vemos que o modelo já não tem termo de interceção e tem 12 fatores de mês significativos. Isto é exatamente o que queríamos ver.

Vamos fazer outro enredo da série temporal dos dados da produção de lacticínios da Califórnia para ver como o modelo sazonal está a funcionar. Adicionei o seguinte código no Azure Machine Learning Studio (clássico) [Execute R Script][execute-r-script] para criar o modelo e fazer um enredo.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Executar este código no Azure Machine Learning Studio (clássico) produz o enredo mostrado na Figura 24.

![Produção de leite na Califórnia com modelo, incluindo efeitos sazonais](./media/r-quickstart/unnamed-chunk-20.png)

*Figura 24. Produção de leite na Califórnia com modelo, incluindo efeitos sazonais.*

O ajuste aos dados apresentados na Figura 24 é bastante encorajador. Tanto a tendência como o efeito sazonal (variação mensal) parecem razoáveis.

Como mais uma verificação do nosso modelo, vamos dar uma olhada nos residuais. O código seguinte calcula os valores previstos dos nossos dois modelos, calcula os residuais para o modelo sazonal, e depois traça estes residuais para os dados de formação.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

O enredo residual é mostrado na Figura 25.

![Residuais do modelo sazonal para os dados de formação](./media/r-quickstart/unnamed-chunk-21.png)

*Figura 25. Residuais do modelo sazonal para os dados de treino.*

Estes residuais parecem razoáveis. Não existe uma estrutura específica, exceto o efeito da recessão de 2008-2009, que o nosso modelo não representa particularmente bem.

O enredo mostrado na Figura 25 é útil para detetar quaisquer padrões dependentes do tempo nos residuais. A abordagem explícita da computação e da conspiração dos residuais que usei coloca os residuais em ordem temporal no enredo. Se, por outro lado, tivesse tramado `milk.lm$residuals`, o enredo não teria sido ordenado.

Também pode usar `plot.lm()` para produzir uma série de parcelas de diagnóstico.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Este código produz uma série de parcelas de diagnóstico mostradas na Figura 26.

![Primeiro de parcelas de diagnóstico para o modelo sazonal](./media/r-quickstart/unnamed-chunk-221.png)

![Segundo dos enredos de diagnóstico para o modelo sazonal](./media/r-quickstart/unnamed-chunk-222.png)

![Terceiro dos enredos de diagnóstico para o modelo sazonal](./media/r-quickstart/unnamed-chunk-223.png)

![Quarto dos enredos de diagnóstico para o modelo sazonal](./media/r-quickstart/unnamed-chunk-224.png)

*Figura 26. Planos de diagnóstico para o modelo sazonal.*

Há alguns pontos altamente influentes identificados nestes enredos, mas nada que cause grande preocupação. Além disso, podemos ver a partir do enredo Q-Q normal que os residuais estão perto de normalmente distribuídos, uma suposição importante para os modelos lineares.

### <a name="forecasting-and-model-evaluation"></a>Avaliação de previsão e modelo

Só há mais uma coisa a fazer para completar o nosso exemplo. Temos de calcular as previsões e medir o erro contra os dados reais. A nossa previsão será para os 12 meses de 2013. Podemos calcular uma medida de erro para esta previsão para os dados reais que não fazem parte do nosso conjunto de dados de formação. Além disso, podemos comparar o desempenho dos 18 anos de dados de formação com os 12 meses de dados de teste.  

Várias métricas são usadas para medir o desempenho dos modelos da série temporal. No nosso caso, usaremos o erro quadrado da raiz (RMS). A função seguinte calcula o erro RMS entre duas séries.  

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

Tal como na função `log.transform()` que discutimos na secção "Transformações de Valor", há bastante controlo de erros e código de recuperação de exceção nesta função. Os princípios utilizados são os mesmos. O trabalho é feito em dois lugares envoltos em `tryCatch()`. Primeiro, as séries de tempo são exponenciadas, uma vez que temos vindo a trabalhar com os registos dos valores. Em segundo lugar, o erro rMS real é calculado.  

Equipado com uma função para medir o erro rMS, vamos construir e obter um quadro de dados contendo os erros de RMS. Incluiremos termos apenas para o modelo de tendência e para o modelo completo com fatores sazonais. O seguinte código faz o trabalho utilizando os dois modelos lineares que construímos.

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

A execução deste código produz a saída mostrada na Figura 27 na porta de saída do Resultado Dataset.

![Comparação de erros de RMS para os modelos](./media/r-quickstart/fig26.png)

*Figura 27. Comparação de erros De RMS para os modelos.*

Destes resultados, vemos que adicionar os fatores sazonais ao modelo reduz significativamente o erro de RMS. Não muito surpreendentemente, o erro de RMS para os dados de treino é um pouco menos do que para a previsão.

## <a id="appendixa"></a>Guia para documentação rstudio

O RStudio está muito bem documentado. Aqui estão alguns links para as secções-chave da documentação rStudio para começar.

* **Criar projetos** - Pode organizar e gerir o seu código R em projetos utilizando o RStudio. Consulte [a utilização de Projetos](https://support.rstudio.com/hc/articles/200526207-Using-Projects) para mais detalhes. Recomendo que siga estas direções e crie um projeto para os exemplos de código R neste artigo.  
* **Editar e executar código R** - O RStudio fornece um ambiente integrado para a edição e execução do código R. Consulte [o Código de Edição e Execução](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) para obter mais detalhes.
* **Debugging** - RStudio inclui poderosas capacidades de depuração. Consulte [debugging com RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) para obter mais informações sobre estas funcionalidades. Para obter informações sobre funcionalidades de resolução de problemas de breakpoint, consulte [Breakpoint Troubleshooting](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting).

## <a id="appendixb"></a>Continuar a ler

Este tutorial de programação R cobre o básico do que precisa para usar a linguagem R com o Azure Machine Learning Studio (clássico). Se não estiver familiarizado com R, estão disponíveis duas apresentações no CRAN:

* [R para Principiantes](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) de Emmanuel Paradis é um bom lugar para começar.  
* [Uma Introdução a R](https://cran.r-project.org/doc/manuals/R-intro.html) por W. N. Venables et. al. vai para um pouco mais de profundidade.

Há muitos livros sobre R que podem ajudá-lo a começar. Aqui estão alguns que acho úteis:

* A **Arte de Programação R: Uma Visita ao Design de Software Estatístico** por Norman Matloff é uma excelente introdução à programação em R.  
* **R Livro de receitas** de Paul Teetor fornece uma abordagem de problema e solução para a utilização de R.  
* **R in Action** de Robert Kabacoff é outro livro introdutório útil. O [site quick R](https://www.statmethods.net/) companheiro é um recurso útil.
* **R Inferno** de Patrick Burns é um livro surpreendentemente humorístico que lida com uma série de tópicos complicados e difíceis que podem ser encontrados na programação em R. O livro está disponível gratuitamente no [R Inferno.](https://www.burns-stat.com/documents/books/the-r-inferno/)
* Se quiser mergulhar profundamente em tópicos avançados em R, veja o livro **Advanced R** de Hadley Wickham. A versão online deste livro está disponível gratuitamente em [http://adv-r.had.co.nz/. ](http://adv-r.had.co.nz/)

Um catálogo de pacotes de séries de tempo R pode ser encontrado na Visão de Tarefa cran: Análise de [séries de tempo](https://cran.r-project.org/web/views/TimeSeries.html). Para obter informações sobre pacotes específicos de objetos de série de tempo, deve consultar a documentação para esse pacote.

O livro **Introdução time series** com R de Paul Cowpertwait e Andrew Metcalfe fornece uma introdução ao uso de R para análise de séries de tempo. Muitos textos teóricos mais fornecem exemplos R.

Aqui estão alguns grandes recursos da Internet:

* DataCamp ensina R no conforto do seu navegador com aulas de vídeo e exercícios de codificação. Existem tutoriais interativos sobre as mais recentes técnicas e pacotes R. Tome o [tutorial R interativo](https://www.datacamp.com/courses/introduction-to-r)gratuito.
* [Aprenda programação R, O Guia Definitivo](https://www.programiz.com/r-programming) do Programiz.
* Um rápido [R Tutorial](https://www.cyclismo.org/tutorial/R/) de Kelly Black da Universidade Clarkson.
* Existem mais de 60 recursos R listados nos [recursos linguísticos Top R para melhorar as suas habilidades de dados.](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html)

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
