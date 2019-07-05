---
title: 'Execute R Script: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de executar Script R no serviço Azure Machine Learning para executar o código R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bfddcd3db4825ea1875474aa16544aa15412bdea
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518056"
---
# <a name="execute-r-script"></a>Executar Script R

Este artigo descreve como utilizar o **executar Script do R** módulo para executar código de R em sua experiência de visual interface.

Com o R, pode realizar tarefas que não são atualmente suportadas pelo módulos existentes, tais como: 
- Criar transformações de dados personalizadas
- Utilize as suas próprias métricas para avaliar previsões
- Criar modelos com algoritmos que não são implementados como módulos de autónomo na visual interface

## <a name="r-version-support"></a>Suporte para a versão R

A interface visual de serviço do Azure Machine Learning utiliza a distribuição de CRAN (Comprehensive R Archive Network) de R. A versão usada no momento é a CRAN 3.5.1.

## <a name="supported-r-packages"></a>Pacotes de R suportados

O ambiente de R é pré-instaladas com mais de 100 pacotes. Para obter uma lista completa, consulte a secção [pacotes de Pre-installed R](#pre-installed-r-packages).

Também pode adicionar o código a seguir a qualquer **executar Script do R** módulo e para ver os pacotes instalados.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalar pacotes de R
Para instalar pacotes de R adicionais, utilize o `install.packages()` método. Certifique-se de que especifique o repositório CRAN. Pacotes são instalados para cada **executar Script R** módulo e não são partilhadas entre si **executar Script do R** módulos.

Este exemplo mostra como instalar o jardim zoológico:
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

## <a name="how-to-configure-execute-r-script"></a>Como configurar a executar Script do R

O **executar Script do R** módulo contém o código de exemplo que pode utilizar como ponto de partida. Para configurar o **executar Script do R** módulo, fornecem um conjunto de entradas e o código seja executado.

![Módulo de R](media/module/execute-r-script.png)

Conjuntos de dados armazenados na visual interface são automaticamente convertidos para um quadro de dados de R quando carregada com este módulo.

1.  Adicionar a **executar Script do R** módulo à sua experimentação.

    > [!NOTE]
    > Todos os dados passados para o **executar Script R** módulo é convertido para o R `data.frame` formato.

1. Ligue-se quaisquer entradas necessárias para o script. Entradas são opcionais e podem incluir dados e código de R adicional.

    * **Dataset1**: A primeira entrada como de referência `dataframe1`. O conjunto de dados de entrada tem de ser formatado como um CSV, TSV, ARFF ou pode se conectar um conjunto de dados do Azure Machine Learning.

    * **Dataset2**: A segunda entrada como de referência `dataframe2`. Este conjunto de dados também tem de ser formatado como um CSV, TSV, arquivo ARFF, ou como um conjunto de dados do Azure Machine Learning.

    * **Pacote do script**: A terceira entrada aceita arquivos ZIP. O arquivo zipado pode conter vários ficheiros e de vários tipos de ficheiro.

1. Na **R script** caixa de texto, escreva ou cole o script de R válido.

    Para o ajudar a começar a utilizar, o **R Script** caixa de texto é previamente preenchida com código de exemplo, o qual pode editar ou substituir.

    * O script tem de conter uma função chamada `azureml_main`, que é o ponto de entrada para este módulo.

    * A função de ponto de entrada pode conter até dois argumentos de entrada: `Param<dataframe1>` e `Param<dataframe2>`
 
    > [!NOTE]
    >  Código R já existente poderá ter de pequenas alterações para ser executado numa experiência de visual interface. Por exemplo, dados de entrada que fornecer no formato CSV devem ser explicitamente convertidos para um conjunto de dados antes de poder utilizá-lo em seu código. Tipos de dados e na coluna usados na linguagem R também são diferentes de algumas formas dos tipos de dados e na coluna usados na interface do visual.

1.  **Seed Aleatório**: Escreva um valor para utilizar dentro do ambiente de R como o valor de seed aleatório. Este parâmetro é equivalente a chamar `set.seed(value)` no código de R.  

1. Execute a experimentação.  

## <a name="results"></a>Resultados

O **executar Script do R** módulos podem retornar várias saídas, mas tem de ser fornecidos como quadros de dados do R. Quadros de dados são automaticamente convertidos para conjuntos de dados de visual interface para compatibilidade com outros módulos.

Mensagens Standard e erros de R são devolvidos ao registo do módulo.

## <a name="sample-scripts"></a>Scripts de exemplo

Existem várias formas que pode expandir a sua experiência com o script de R personalizado.  Esta seção fornece o código de exemplo para tarefas comuns.


### <a name="add-r-script-as-an-input"></a>Adicionar o R script como entrada

O **executar Script do R** módulo suporta ficheiros de script do R arbitrários como entradas. Para tal, tem de ser carregados para a área de trabalho como parte do ficheiro ZIP.

1. Para carregar um ficheiro ZIP que contém o código de R para sua área de trabalho, clique em **New**, clique em **conjunto de dados**e, em seguida, selecione **partir do ficheiro local** e o **ficheiro Zip**opção.  

1. Certifique-se de que o arquivo zipado está disponível na **conjuntos de dados guardado** lista.

1.  Ligar o conjunto de dados para o **pacote de Script** porta de entrada.

1. Todos os ficheiros que estão contidos no ficheiro ZIP estão disponíveis durante o tempo de execução de experimentação. 

    Se o ficheiro de pacote de script continha uma estrutura de diretório, a estrutura é preservada. No entanto, deve alterar seu código preceder o diretório **. / Script agrupar** ao caminho.

### <a name="process-data"></a>Processar dados

O exemplo a seguir mostra como dimensionar e normalizar dados de entrada:

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

### <a name="read-a-zip-file-as-input"></a>Ler um arquivo ZIP como entrada

Este exemplo mostra como utilizar um conjunto de dados num arquivo ZIP como entrada para o **executar Script do R** módulo.

1. Crie o ficheiro de dados no formato CSV e dê-lhe o nome "mydatafile.csv".
1. Crie um ficheiro ZIP e adicione o ficheiro CSV para o arquivo.
1. Carregar o arquivo zipado para sua área de trabalho do Azure Machine Learning. 
1. Ligar o conjunto de dados resultante para o **ScriptBundle** entrada de sua **executar Script do R** módulo.
1. Usando o seguinte código para ler os dados do CSV a partir do arquivo zipado.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replicar linhas

Este exemplo mostra como replicar registos positivos num conjunto de dados para equilibrar o exemplo:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Passar objetos de R entre módulos de executar o Script de R

Pode passar objetos de R entre instâncias do **executar Script do R** módulo utilizando o mecanismo de serialização interno. Este exemplo assume que deseja mover o objeto de R com o nome `A` entre as duas **executar Script do R** módulos.

1. Adicionar o primeiro **executar Script R** módulo para a sua experimentação e escreva o seguinte código no **R Script** caixa de texto para criar um objeto serializado `A` como uma coluna no módulo de saída de tabela de dados:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    A conversão explícita para o tipo de número inteiro é feita porque a função de serialização produz dados no R `Raw` formato, o que não é suportado pela visual interface.

1. Adicionar uma segunda instância dos **executar Script do R** módulo e ligá-la para a porta de saída do módulo anterior.

1. Escreva o seguinte código no **R Script** caixa de texto para extrair o objeto `A` da tabela de dados de entrada. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Pacotes de R previamente instaladas

A lista atual dos pacotes de R pré-instaladas disponíveis para uso:

|              |            | 
|--------------|------------| 
| Pacote      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| Base         | 3.5.1      | 
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
| Classe        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| Cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| ColorSpace   | 1.4-1      | 
| Compilador     | 3.5.1      | 
| crayon       | 1.3.4      | 
| Curl         | 3.3        | 
| data.table   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| avaliar     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| Foreach      | 1.4.4      | 
| externa      | 0.8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| elementos genéricos     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| cola         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| gráficos     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grelha         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| João        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iteradores    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| Etiquetagem     | 0.3        | 
| reticulado      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| Markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matriz       | 1.2-17     | 
| Métodos      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| MIME         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| Munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| paralelo     | 3.5.1      | 
| Pilar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| Progresso     | 1.2.2      | 
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
| receitas      | 0.1.5      | 
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
| escalas       | 1.0.0      | 
| selectr      | 0.4-1      | 
| Espaciais      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| Estatísticas        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| sobrevivência     | 2.44-1.1   | 
| sys.          | 3.2        | 
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
| bigodes      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| jardim zoológico          | 1.8-6      | 

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 