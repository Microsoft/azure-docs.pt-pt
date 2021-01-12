---
title: 'Executar Script R: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Execut R Script no Azure Machine Learning designer para executar código R personalizado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/17/2020
ms.openlocfilehash: bdd7fd8e19bf2de6d0b3c6b2edd4515771fae237
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119028"
---
# <a name="execute-r-script-module"></a>Executar módulo de script R

Este artigo descreve como usar o módulo Execut R Script para executar código R no seu pipeline de designer de aprendizagem de máquinas Azure.

Com R, pode fazer tarefas que não são suportadas por módulos existentes, tais como: 
- Criar transformações de dados personalizadas
- Use as suas próprias métricas para avaliar previsões
- Construa modelos usando algoritmos que não são implementados como módulos autónomos no designer

## <a name="r-version-support"></a>Suporte de versão R

O designer de aprendizagem automática Azure utiliza a distribuição cran (Comprehensive R Archive Network) de R. A versão atualmente utilizada é CRAN 3.5.1.

## <a name="supported-r-packages"></a>Supported R packages (Pacotes R suportados)

O ambiente R está pré-instalado com mais de 100 pacotes. Para obter uma lista completa, consulte a secção [Pacotes R pré-instalados](#preinstalled-r-packages).

Também pode adicionar o seguinte código a qualquer módulo executo R Script, para ver as embalagens instaladas.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
> [!NOTE]
> Se o seu pipeline contiver vários módulos executo R Script que precisam de pacotes que não estão na lista pré-instalada, instale as embalagens em cada módulo. 

## <a name="installing-r-packages"></a>Instalação de pacotes R
Para instalar pacotes R adicionais, utilize o `install.packages()` método. As encomendas são instaladas para cada módulo executo R Script. Não são partilhados em outros módulos executo R Script.

> [!NOTE]
> **Não** é recomendado instalar o pacote R a partir do pacote de scripts. É recomendado instalar pacotes diretamente no editor de scripts.
> Especifique o repositório CRAN quando estiver a instalar pacotes, tais como `install.packages("zoo",repos = "http://cran.us.r-project.org")` .

> [!WARNING]
> O módulo Excute R Script não suporta a instalação de pacotes que requerem compilação nativa, como `qdap` pacote que requer JAVA e pacote que requer `drc` C++. Isto porque este módulo é executado num ambiente pré-instalado com permissão de não administração.
> Não instale pacotes pré-construídos sobre/para Windows, uma vez que os módulos de design estão em funcionamento no Ubuntu. Para verificar se um pacote está pré-construído nas janelas, pode ir ao [CRAN](https://cran.r-project.org/) e pesquisar o seu pacote, descarregar um ficheiro binário de acordo com o seu SISTEMA, e verificar **Built:** parte no ficheiro **DESCRI.** Segue-se um exemplo: :::image type="content" source="media/module/r-package-description.png" alt-text="descrição do pacote R" lightbox="media/module/r-package-page.png":::

Esta amostra mostra como instalar o Jardim Zoológico:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
 > [!NOTE]
 > Antes de instalar uma embalagem, verifique se já existe para não repetir uma instalação. As instalações repetidas podem causar pedidos de serviço web para o intervalo.     

## <a name="access-to-registered-dataset"></a>Acesso ao conjunto de dados registado

Pode consultar o seguinte código de amostra para aceder aos [conjuntos de dados registados](../how-to-create-register-datasets.md) no seu espaço de trabalho:

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  run = get_current_run()
  ws = run$experiment$workspace
  dataset = azureml$core$dataset$Dataset$get_by_name(ws, "YOUR DATASET NAME")
  dataframe2 <- dataset$to_pandas_dataframe()
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="uploading-files"></a>Upload de ficheiros
O módulo Execut R Script suporta o upload de ficheiros utilizando o Azure Machine Learning R SDK.

A amostra que se segue mostra como fazer o upload de um ficheiro de imagem no Executo R Script:
```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

Depois de terminado o curso do gasoduto, pode visualizar a imagem no painel direito do módulo.

> [!div class="mx-imgBorder"]
> ![Pré-visualização da imagem carregada](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Como configurar executar executar o script R

O módulo executo R Script contém o código de amostra como ponto de partida.

![Diagrama de entradas para um módulo R](media/module/execute-r-script.png)

Os conjuntos de dados armazenados no designer são automaticamente convertidos para uma estrutura de dados R quando carregados com este módulo.

1.  Adicione o módulo **de script Execute R** ao seu pipeline.  

1. Conecte todas as entradas que o script precise. As entradas são opcionais e podem incluir dados e código R adicional.

    * **Dataset1**: Referenciar a primeira entrada como `dataframe1` . O conjunto de dados de entrada deve ser formatado como um ficheiro CSV, TSV ou ARFF. Ou pode ligar um conjunto de dados de aprendizagem de máquinas Azure.

    * **Dataset2**: Referenciar a segunda entrada como `dataframe2` . Este conjunto de dados também deve ser formatado como um ficheiro CSV, TSV ou ARFF, ou como um conjunto de dados de Aprendizagem automática Azure.

    * **Script Bundle**: A terceira entrada aceita ficheiros .zip. Um ficheiro com fecho pode conter vários ficheiros e vários tipos de ficheiros.

1. Na caixa de texto do **script R,** escreva ou cole um script R válido.

    > [!NOTE]
    > Tenha cuidado ao escrever o seu guião. Certifique-se de que não existem erros de sintaxe, tais como a utilização de variáveis não declaradas ou de módulos ou funções não declarados. Preste uma atenção extra à lista de pacotes pré-instalados no final deste artigo. Para utilizar pacotes que não estão listados, instale-os no seu script. Um exemplo é `install.packages("zoo",repos = "http://cran.us.r-project.org")`.
    
    Para ajudá-lo a começar, a caixa de texto **R Script** é pré-preparada com código de amostra, que pode editar ou substituir.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main,
    # which is the entry point for this module.

    # Note that functions dependent on the X11 library,
    # such as "View," are not supported because the X11 library
    # is not preinstalled.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a .zip file is connected to the third input port, it's
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

    A função ponto de entrada deve ter os argumentos de entrada `Param<dataframe1>` `Param<dataframe2>` e, mesmo quando estes argumentos não são utilizados na função.

    > [!NOTE]
    > Os dados transmitidos para o módulo Execut R Script são referenciados como `dataframe1` `dataframe2` e, que é diferente do designer de Aprendizagem automática Azure (a referência do designer como `dataset1` , `dataset2` ). Certifique-se de que os dados de entrada são referenciados corretamente no seu script.  
 
    > [!NOTE]
    > O código R existente pode necessitar de pequenas alterações para ser executado num oleoduto de design. Por exemplo, os dados de entrada que fornece no formato CSV devem ser explicitamente convertidos para um conjunto de dados antes de poder usá-lo no seu código. Os tipos de dados e colunas utilizados na língua R também diferem de algumas formas dos tipos de dados e colunas utilizados no designer.

1. Se o seu script for maior que 16 KB, utilize a porta **script Bundle** para evitar erros como *o CommandLine excede o limite de 16597 caracteres*. 
    
    1. Embrulhe o script e outros recursos personalizados para um ficheiro zip.
    1. Faça o upload do ficheiro zip como um **conjunto de dados de ficheiro** para o estúdio. 
    1. Arraste o módulo de conjunto de dados da lista *datasets* no painel do módulo esquerdo na página de autoria do designer. 
    1. Ligue o módulo de conjunto de dados à porta do Pacote de **Scripts** do módulo **executo R Script.**
    
    Segue-se o código de amostra para consumir o script no pacote de scripts:

    ```R
    azureml_main <- function(dataframe1, dataframe2){
    # Source the custom R script: my_script.R
    source("./Script Bundle/my_script.R")

    # Use the function that defined in my_script.R
    dataframe1 <- my_func(dataframe1)

    sample <- readLines("./Script Bundle/my_sample.txt")
    return (list(dataset1=dataframe1, dataset2=data.frame("Sample"=sample)))
    }
    ```

1.  Para **Sementes Aleatórias,** insira um valor a utilizar no ambiente R como valor de sementes aleatórias. Este parâmetro equivale a chamar `set.seed(value)` o código R.  

1. Envie o oleoduto.  

## <a name="results"></a>Resultados

Executar os módulos R Script podem devolver várias saídas, mas devem ser fornecidos como quadros de dados R. O designer converte automaticamente quadros de dados em conjuntos de dados para compatibilidade com outros módulos.

Mensagens padrão e erros de R são devolvidos ao registo do módulo.

Se precisar de imprimir resultados no script R, pode encontrar os resultados impressos em **70_driver_log** no separador **Saídas+logs** no painel direito do módulo.

## <a name="sample-scripts"></a>Scripts de exemplo

Existem muitas maneiras de estender o seu pipeline usando scripts R personalizados. Esta secção fornece um código de amostra para tarefas comuns.


### <a name="add-an-r-script-as-an-input"></a>Adicione um script R como entrada

O módulo Execut R Script suporta ficheiros de script r arbitrários como entradas. Para usá-las, tem de enviá-las para o seu espaço de trabalho como parte do ficheiro .zip.

1. Para fazer o upload de um ficheiro .zip que contenha código R para o seu espaço de trabalho, aceda à página de ativos **datasets.** Selecione **Criar conjunto de dados** e, em seguida, selecione **A partir do ficheiro local** e da opção do tipo de conjunto de dados **de ficheiro.**  

1. Verifique se o ficheiro com fecho aparece nos **meus conjuntos de dados** na categoria **Datasets** na árvore do módulo esquerdo.

1.  Ligue o conjunto de dados à porta de entrada **do Pacote script.**

1. Todos os ficheiros do ficheiro .zip estão disponíveis durante o tempo de funcionamento do gasoduto. 

    Se o ficheiro do pacote de scripts contiver uma estrutura de diretório, a estrutura é preservada. Mas tem de alterar o seu código para preparar o **diretório ./Script Bundle** para o caminho.

### <a name="process-data"></a>Processar dados

A amostra que se segue mostra como escalar e normalizar os dados de entrada:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a .zip file is connected to the third input port, it's
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # Find the maximum and minimum values of the width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # Calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # Apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Leia um ficheiro de .zip como entrada

Esta amostra mostra como utilizar um conjunto de dados num ficheiro .zip como entrada para o módulo executo R Script.

1. Crie o ficheiro de dados em formato CSV e nomeie-o **mydatafile.csv**.
1. Crie um ficheiro .zip e adicione o ficheiro CSV ao arquivo.
1. Faça o upload do ficheiro com fecho para o seu espaço de trabalho Azure Machine Learning. 
1. Ligue o conjunto de dados resultante à entrada **ScriptBundle** do seu módulo **executo R Script.**
1. Utilize o seguinte código para ler os dados do CSV a partir do ficheiro zipped.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replicar linhas

Esta amostra mostra como replicar registos positivos num conjunto de dados para equilibrar a amostra:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Passe objetos R entre os módulos executo R Script

Pode passar objetos R entre instâncias do módulo Execut R Script utilizando o mecanismo de serialização interna. Este exemplo pressupõe que pretende mover o objeto R nomeado `A` entre dois módulos executar script R.

1. Adicione o primeiro módulo **executo R script** ao seu pipeline. Em seguida, introduza o seguinte código na caixa de texto **do Script R** para criar um objeto serializado como coluna na tabela de `A` dados de saída do módulo:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    A conversão explícita para o tipo inteiro é feita porque a função de serialização produz dados no `Raw` formato R, que o designer não suporta.

1. Adicione uma segunda instância do módulo **Execut R Script** e conecte-o à porta de saída do módulo anterior.

1. Digite o seguinte código na caixa de texto **do Script R** para extrair objeto da tabela de `A` dados de entrada. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="preinstalled-r-packages"></a>Pacotes R pré-instalados

Os seguintes pacotes R pré-instalados estão atualmente disponíveis:

| Pacote      | Versão    | 
|--------------|------------| 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| acento circunflexo        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| classe        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| conjuntos de dados     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0,14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0.8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| genéricos     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| gráficos     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| labeling     | 0.3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matriz       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| tools        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.