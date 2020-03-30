---
title: 'Execute o script R: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Execute R Script em Azure Machine Learning para executar o código R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: f038293b48956ac89314e426df3f5dc491954df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064208"
---
# <a name="execute-r-script"></a>Executar Script R

Este artigo descreve como utilizar o módulo **Execute R Script** para executar o código R no seu pipeline de design de machine learning Azure (pré-visualização).

Com R, pode executar tarefas que não são suportadas atualmente por módulos existentes, tais como: 
- Criar transformações personalizadas de dados
- Use as suas próprias métricas para avaliar previsões
- Construa modelos usando algoritmos que não são implementados como módulos autónomos no designer

## <a name="r-version-support"></a>Suporte de versão R

O designer de machine learning azure usa a distribuição CRAN (Comprehensive R Archive Network) de R. A versão atualmente utilizada é CRAN 3.5.1.

## <a name="supported-r-packages"></a>Supported R packages (Pacotes R suportados)

O ambiente R é pré-instalado com mais de 100 embalagens. Para obter uma lista completa, consulte as [embalagens R pré-instaladas](#pre-installed-r-packages).

Também pode adicionar o seguinte código a qualquer módulo **execute R Script** e ver as embalagens instaladas.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalação de pacotes R
Para instalar pacotes R `install.packages()` adicionais, utilize o método. Certifique-se de especificar o repositório CRAN. Os pacotes são instalados para cada módulo **execute R Script,** e não são partilhados em outros módulos **execute R Script.**

Esta amostra mostra como instalar o Zoo:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
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
  > Verifique se a embalagem já existe antes de a instalar para evitar a instalação repetida. Como `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` no código de amostra acima. A instalação repetida pode causar tempo de tempo de pedido de serviço web.     

## <a name="upload-files"></a>Carregar ficheiros
O **Script Execute R** suporta o upload de ficheiros utilizando o Azure Machine Learning R SDK.

O exemplo que se segue mostra como carregar um ficheiro de imagem no **Script Executar R:**
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
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

Após a execução do gasoduto, pode pré-visualizar a imagem no painel direito do módulo

> [!div class="mx-imgBorder"]
> ![Imagem carregada](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Como configurar o Execute R Script

O módulo **Execute R Script** contém um código de amostra que pode utilizar como ponto de partida. Para configurar o módulo **Execute R Script,** forneça um conjunto de inputs e código para executar.

![Módulo R](media/module/execute-r-script.png)

Os conjuntos de dados armazenados no designer são automaticamente convertidos para um quadro de dados R quando carregados com este módulo.

1.  Adicione o módulo **Execute R Script** ao seu pipeline.

  

1. Ligue as inputs necessárias pelo script. As inputs são opcionais e podem incluir dados e código R adicional.

    * **Dataset1**: Consulte a `dataframe1`primeira entrada como . O conjunto de dados de entrada deve ser formatado como CSV, TSV, ARFF ou pode ligar um conjunto de dados de Aprendizagem automática Azure.

    * **Conjunto de dados2**: Consulte `dataframe2`a segunda entrada como . Este conjunto de dados também deve ser formatado como um ficheiro CSV, TSV, ARFF ou como um conjunto de dados de Aprendizagem automática Azure.

    * **Script Bundle**: A terceira entrada aceita ficheiros ZIP. O ficheiro com fecho pode conter vários ficheiros e vários tipos de ficheiros.

1. Na caixa de texto do **script R,** escreva ou cola script R válido.

    Para ajudá-lo a começar, a caixa de texto **R Script** é pré-povoada com código de amostra, que pode editar ou substituir.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main
    # which is the entry point for this module.

    # The entry point function can contain up to two input arguments:
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a zip file is connected to the third input port, it is
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

 * O script deve conter `azureml_main`uma função denominada , que é o ponto de entrada deste módulo.

 * A função do ponto de entrada pode `Param<dataframe1>` conter até dois argumentos de entrada: e`Param<dataframe2>`
 
   > [!NOTE]
    > Os dados transmitidos para o módulo `dataframe1` Execute `dataframe2` **R Script** são referenciados como e , `dataset1` `dataset2`o que é diferente do designer de Machine Learning Azure (a referência do designer como , ). Verifique se os dados de entrada estão corretamente referenciados no seu script.  
 
    > [!NOTE]
    >  O código R existente pode necessitar de pequenas alterações para ser executado num pipeline de design. Por exemplo, os dados de entrada que fornece no formato CSV devem ser explicitamente convertidos para um conjunto de dados antes de poder utilizá-lo no seu código. Os tipos de dados e colunas utilizados na língua R também diferem de algumas formas dos tipos de dados e colunas utilizados no designer.

1.  **Semente aleatória**: Digite um valor a utilizar dentro do ambiente R como o valor de sementes aleatórias. Este parâmetro equivale a `set.seed(value)` chamar o código R.  

1. Submeta o oleoduto.  

## <a name="results"></a>Resultados

Os módulos **execute R Script** podem devolver várias saídas, mas devem ser fornecidos como quadros de dados R. Os quadros de dados são automaticamente convertidos em conjuntos de dados no designer para compatibilidade com outros módulos.

As mensagens padrão e os erros de R são devolvidos ao registo do módulo.

Se precisar de imprimir resultados no script R, pode encontrar os resultados impressos no **separador 70_driver_log** em **saídas+logs** no painel certo do módulo.

## <a name="sample-scripts"></a>Scripts de exemplo

Existem muitas maneiras de estender o seu pipeline usando script R personalizado.  Esta secção fornece um código de amostra para tarefas comuns.


### <a name="add-r-script-as-an-input"></a>Adicione o script R como entrada

O módulo **Execute R Script** suporta ficheiros de script R arbitrários como inputs. Para tal, devem ser enviados para o seu espaço de trabalho como parte do ficheiro ZIP.

1. Para fazer o upload de um ficheiro ZIP contendo código R para o seu espaço de trabalho, vá à página de ativos **do Datasets,** clique em **Criar dataset,** e depois selecione **A partir do ficheiro local** e da opção de tipo de dataset **de ficheiro.**  

1. Verifique se o ficheiro zipped está disponível na lista **My Datasets** na categoria **Datasets** na árvore do módulo esquerdo.

1.  Ligue o conjunto de dados à porta de entrada script **Bundle.**

1. Todos os ficheiros contidos no ficheiro ZIP estão disponíveis durante o tempo de execução do gasoduto. 

    Se o ficheiro do script contivesse uma estrutura de diretório, a estrutura é preservada. No entanto, deve alterar o seu código para preparar o diretório **./Script Bundle** para o caminho.

### <a name="process-data"></a>Processar dados

A amostra que se segue mostra como escalar e normalizar os dados de entrada:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Leia um ficheiro ZIP como entrada

Esta amostra mostra como utilizar um conjunto de dados num ficheiro ZIP como entrada para o módulo **Execute R Script.**

1. Crie o ficheiro de dados em formato CSV e nomeie-o "mydatafile.csv".
1. Crie um ficheiro ZIP e adicione o ficheiro CSV ao arquivo.
1. Faça o upload do ficheiro com fecho para o seu espaço de trabalho azure Machine Learning. 
1. Ligue o conjunto de dados resultante à entrada **scriptBundle** do módulo **Execute R Script.**
1. Utilizando o seguinte código para ler os dados do CSV a partir do ficheiro zipped.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Linhas de replicação

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Passar objetos R entre módulos de script Execute R

Pode passar objetos R entre as instâncias do módulo **Execute R Script** utilizando o mecanismo de serialização interna. Este exemplo pressupõe que pretende mover `A` o objeto R nomeado entre dois módulos **execute R Script.**

1. Adicione o primeiro módulo **execute R Script** ao seu pipeline e escreva o seguinte código na caixa de texto r **script** para criar um objeto `A` serializado como uma coluna na tabela de dados de saída do módulo:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    A conversão explícita para o tipo inteiro é feita porque a `Raw` função de serialização produz dados no formato R, que não é suportado pelo designer.

1. Adicione uma segunda instância do módulo **Execute R Script** e ligue-o à porta de saída do módulo anterior.

1. Digite o seguinte código na caixa `A` de texto r **script** para extrair objeto da tabela de dados de entrada. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Pacotes R pré-instalados

A lista atual de Pacotes R pré-instalados disponível para utilização:

|              |            | 
|--------------|------------| 
| Pacote      | Versão    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| niciar         | 1.3-22     | 
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
| avaliar     | 0.14       | 
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
| highr        | 0,8        | 
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
| paralelo     | 3.5.1      | 
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
| sys          | 3,2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| ferramentas        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
