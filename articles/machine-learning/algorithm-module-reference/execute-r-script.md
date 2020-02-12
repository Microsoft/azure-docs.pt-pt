---
title: 'Executar script R: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo executar script R no Azure Machine Learning para executar o código R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 4991fe9fb06d0a49da3e0404f0362c85f15571af
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152300"
---
# <a name="execute-r-script"></a>Executar Script R

Este artigo descreve como utilizar o módulo **Execute R Script** para executar o código R no seu pipeline de design de machine learning Azure (pré-visualização).

Com o R, você pode executar tarefas que atualmente não são suportadas por módulos existentes, como: 
- Criar transformações de dados personalizadas
- Use suas próprias métricas para avaliar previsões
- Crie modelos usando algoritmos que não são implementados como módulos autônomos no designer

## <a name="r-version-support"></a>Suporte à versão do R

O designer de Azure Machine Learning usa a distribuição CRAN (rede de arquivos R abrangente) de R. A versão atualmente usada é CRAN 3.5.1.

## <a name="supported-r-packages"></a>Pacotes R suportados

O ambiente de R é pré-instalado com mais de 100 pacotes. Para obter uma lista completa, consulte as [embalagens R pré-instaladas](#pre-installed-r-packages).

Também pode adicionar o seguinte código a qualquer módulo **execute R Script** e ver as embalagens instaladas.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalar pacotes de R
Para instalar pacotes R adicionais, utilize o método `install.packages()`. Certifique-se de especificar o repositório CRAN. Os pacotes são instalados para cada módulo **execute R Script,** e não são partilhados em outros módulos **execute R Script.**

Este exemplo mostra como instalar o Zoo:
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
  > Verifique se o pacote já existe antes de instalá-lo para evitar a repetição da instalação. Como `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` acima do código da amostra. Repetir a instalação pode causar tempo limite de solicitação de serviço Web.     

## <a name="how-to-configure-execute-r-script"></a>Como configurar o executar script R

O módulo **Execute R Script** contém um código de amostra que pode utilizar como ponto de partida. Para configurar o módulo **Execute R Script,** forneça um conjunto de inputs e código para executar.

![Módulo R](media/module/execute-r-script.png)

Os conjuntos de dados armazenados no designer são convertidos automaticamente em um quadro do R data quando carregados com esse módulo.

1.  Adicione o módulo **Execute R Script** ao seu pipeline.

  

1. Conecte as entradas necessárias ao script. As entradas são opcionais e podem incluir dados e código de R adicional.

    * **Dataset1**: Consulte a primeira entrada como `dataframe1`. O conjunto de dados de entrada deve ser formatado como CSV, TSV, ARFF ou você pode conectar um conjunto de dados Azure Machine Learning.

    * **Conjunto de dados2**: Consulte a segunda entrada como `dataframe2`. Esse conjunto de e também deve ser formatado como um arquivo CSV, TSV, ARFF ou como um conjunto de Azure Machine Learning.

    * **Script Bundle**: A terceira entrada aceita ficheiros ZIP. O arquivo compactado pode conter vários arquivos e vários tipos de arquivo.

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

 * O script deve conter uma função chamada `azureml_main`, que é o ponto de entrada deste módulo.

 * A função do ponto de entrada pode conter até dois argumentos de entrada: `Param<dataframe1>` e `Param<dataframe2>`
 
   > [!NOTE]
    > Os dados transmitidos para o módulo **Execute R Script** são referenciados como `dataframe1` e `dataframe2`, que é diferente do designer de Machine Learning Azure (a referência do designer como `dataset1`, `dataset2`). Verifique se os dados de entrada estão referneced corretamente em seu script.  
 
    > [!NOTE]
    >  O código R existente pode precisar de alterações secundárias para ser executado em um pipeline de designer. Por exemplo, os dados de entrada que você fornecer no formato CSV devem ser explicitamente convertidos em um DataSet antes que você possa usá-lo em seu código. Os tipos de dados e colunas usados na linguagem R também são diferentes de algumas maneiras dos tipos de dados e colunas usados no designer.

1.  **Semente aleatória**: Digite um valor a utilizar dentro do ambiente R como o valor de sementes aleatórias. Este parâmetro equivale a chamar `set.seed(value)` em código R.  

1. Executar o pipeline.  

## <a name="results"></a>Resultados

Os módulos **execute R Script** podem devolver várias saídas, mas devem ser fornecidos como quadros de dados R. Os quadros de dados são convertidos automaticamente em conjuntos de dados no designer para compatibilidade com outros módulos.

Mensagens e erros padrão do R são retornados para o log do módulo.

## <a name="sample-scripts"></a>Scripts de exemplo

Há várias maneiras pelas quais você pode estender seu pipeline usando o script R personalizado.  Esta seção fornece código de exemplo para tarefas comuns.


### <a name="add-r-script-as-an-input"></a>Adicionar script R como uma entrada

O módulo **Execute R Script** suporta ficheiros de script R arbitrários como inputs. Para fazer isso, eles devem ser carregados em seu espaço de trabalho como parte do arquivo ZIP.

1. Para fazer o upload de um ficheiro ZIP contendo código R para o seu espaço de trabalho, clique em **Novo,** clique em **Dataset,** e depois selecione **A partir do ficheiro local** e da opção de ficheiro **Zip.**  

1. Verifique se o ficheiro zipped está disponível na lista de Conjuntos de **Dados Guardados.**

1.  Ligue o conjunto de dados à porta de entrada script **Bundle.**

1. Todos os arquivos contidos no arquivo ZIP estão disponíveis durante o tempo de execução do pipeline. 

    Se o arquivo de pacote de script contiver uma estrutura de diretório, a estrutura será preservada. No entanto, deve alterar o seu código para preparar o diretório **./Script Bundle** para o caminho.

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

Esta amostra mostra como utilizar um conjunto de dados num ficheiro ZIP como entrada para o módulo **Execute R Script.**

1. Crie o arquivo de dados no formato CSV e nomeie-o como "mydatafile. csv".
1. Crie um arquivo ZIP e adicione o arquivo CSV ao arquivo morto.
1. Carregue o arquivo compactado em seu espaço de trabalho Azure Machine Learning. 
1. Ligue o conjunto de dados resultante à entrada **scriptBundle** do módulo **Execute R Script.**
1. Usando o código a seguir para ler os dados CSV do arquivo compactado.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replicar linhas

Este exemplo mostra como replicar registros positivos em um DataSet para balancear o exemplo:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Passar objetos R entre executar módulos de script R

Pode passar objetos R entre as instâncias do módulo **Execute R Script** utilizando o mecanismo de serialização interna. Este exemplo pressupõe que pretende mover o objeto R denominado `A` entre dois módulos **execute R Script.**

1. Adicione o primeiro módulo **execute R Script** ao seu pipeline e escreva o seguinte código na caixa de texto r **script** para criar um objeto serializado `A` como uma coluna na tabela de dados de saída do módulo:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    A conversão explícita para o tipo inteiro é feita porque a função de serialização produz dados no formato R `Raw`, que não é suportado pelo designer.

1. Adicione uma segunda instância do módulo **Execute R Script** e ligue-o à porta de saída do módulo anterior.

1. Digite o seguinte código na caixa de texto **r script** para extrair objeto `A` da tabela de dados de entrada. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Pacotes de R pré-instalados

A lista atual de pacotes de R pré-instalados disponíveis para uso:

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
| vassoura        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| guarda-celcela   | 1.1.0      | 
| Classe        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipe        | 0.6.0      | 
| aglomerado      | 2.0.7-1    | 
| códigos    | 0.2-16     | 
| espaço para cores   | 1.4-1      | 
| compilador     | 3.5.1      | 
| lápis de cor       | 1.3.4      | 
| Caracol         | 3.3        | 
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
| foreach      | 1.4.4      | 
| estrangeiro      | 0,8-71     | 
| vinculação           | 1.3.1      | 
| gdata        | 2.18.0     | 
| genéricos     | 0.0.2      | 
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
| refúgio        | 2.1.0      | 
| mais alto        | 0.8        | 
| hms          | 0.4.2      | 
| ferramentas html    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0,9-9      | 
| iteradores    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| malha        | 1.23       | 
| rotulagem     | 0.3        | 
| rede      | 0,20-38    | 
| lava         | 1.6.5      | 
| preguiçosa     | 0.2.2      | 
| lubridade    | 1.7.4      | 
| magrittr     | 1.5        | 
| marcação     | 1          | 
| MASSA         | 7.3-51.4   | 
| Matriz       | 1.2-17     | 
| métodos      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mímica         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelador       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| Abre-nis      | 1.4        | 
| paralelo     | 3.5.1      | 
| pilar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| unidades bonitas  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progresso     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| floresta aleatória | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| leitura        | 1.3.1      | 
| readxl       | 1.3.1      | 
| receitas      | 0.1.5      | 
| desforra      | 1.0.1      | 
| reprex       | 0.3.0      | 
| remodelação2     | 1.4.3      | 
| reticulado   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| escalas       | 1.0.0      | 
| selectr      | 0.4-1      | 
| espacial      | 7.3-11     | 
| splines      | 3.5.1      | 
| PRAÇA QUADRADOS      | 2017.10-1  | 
| estatísticas        | 3.5.1      | 
| estatísticas4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| sobrevivência     | 2,44-1.1   | 
| sys          | 3,2        | 
| tcltk        | 3.5.1      | 
| tíbide       | 2.1.3      | 
| arrumação        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| arrumação verso    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| ferramentas        | 3.5.1      | 
| tseries      | 0,10 a 47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utilitários        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| bigode      | 0.3-2      | 
| murcho        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoológico          | 1.8-6      | 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
