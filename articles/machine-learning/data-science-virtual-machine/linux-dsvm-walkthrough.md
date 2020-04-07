---
title: Explore Linux
titleSuffix: Azure Data Science Virtual Machine
description: Aprenda a completar várias tarefas comuns de ciência de dados utilizando a Máquina Virtual de Ciência de Dados Linux.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 1298d5fe1d7407e836f454c2130a913dde6f8eec
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755140"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Ciência de dados com uma máquina virtual linux data science em Azure

Este walkthrough mostra-lhe como completar várias tarefas comuns de ciência de dados usando a Máquina Virtual de Ciência de Dados Linux (DSVM). O Linux DSVM é uma imagem virtual da máquina disponível no Azure que é pré-instalada com uma coleção de ferramentas comumente usadas para análise de dados e machine learning. Os principais componentes do software são itens na [Provision the Linux Data Science Virtual Machine](linux-dsvm-intro.md). A imagem DSVM facilita o início da ciência dos dados em minutos, sem ter de instalar e configurar cada uma das ferramentas individualmente. Pode facilmente aumentar o DSVM se necessário, e pode detê-lo quando não estiver a ser utilizado. O recurso DSVM é elástico e rentável.

As tarefas de ciência de dados demonstradas neste walkthrough seguem os passos delineados no Que é o Processo de [Ciência de Dados da Equipa?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) O Processo de Ciência de Dados da Equipa é uma abordagem sistemática da ciência dos dados que ajuda equipas de cientistas de dados a colaborar eficazmente ao longo do ciclo de vida da construção de aplicações inteligentes. O processo de ciência dos dados também fornece um quadro iterativo para a ciência dos dados que pode ser seguido por um indivíduo.

Neste walkthrough, analisamos o conjunto de dados da [base de spam.](https://archive.ics.uci.edu/ml/datasets/spambase) Spambase é um conjunto de e-mails marcados quer por spam quer por presunto (não spam). A base de spam também contém algumas estatísticas sobre o conteúdo dos e-mails. Falamos das estatísticas mais tarde na passagem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar um Linux DSVM, deve ter os seguintes pré-requisitos:

* **Assinatura Azure.** Para obter uma subscrição Azure, consulte [Criar a sua conta Azure gratuita hoje](https://azure.microsoft.com/free/).
* [**Máquina virtual linux data science**](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Para obter informações sobre o fornecimento da máquina virtual, consulte [Provision the Linux Data Science Virtual Machine](linux-dsvm-intro.md).
* [**X2Go**](https://wiki.x2go.org/doku.php) instalado no seu computador com uma sessão de XFCE aberta. Para mais informações, consulte [Instalar e configurar o cliente X2Go](linux-dsvm-intro.md#x2go).
* Para uma experiência de scrolling mais suave, no navegador web Firefox `gfx.xrender.enabled` da `about:config`DSVM, altere a bandeira em . [Saiba mais](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Considere também `mousewheel.enable_pixel_scrolling` `False`definir para . [Saiba mais](https://support.mozilla.org/questions/981140).
* **Conta de Aprendizagem automática Azure.** Se ainda não tem uma, inscreva-se para uma nova conta na página inicial do [Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning//).

## <a name="download-the-spambase-dataset"></a>Descarregue o conjunto de dados da base de spam

O conjunto de dados da base de [spam](https://archive.ics.uci.edu/ml/datasets/spambase) é um conjunto relativamente pequeno de dados que contém 4.601 exemplos. O conjunto de dados é um tamanho conveniente para demonstrar algumas das principais características do DSVM porque mantém os requisitos de recursos modestos.

> [!NOTE]
> Esta passagem foi criada usando um D2 v2 Linux DSVM (Ubuntu 18.04 Edition). Você pode usar um DSVM deste tamanho para completar os procedimentos que são demonstrados nesta passagem.

Se precisar de mais espaço de armazenamento, pode criar discos adicionais e anexá-los ao seu DSVM. Os discos utilizam o armazenamento azure persistente, pelo que os seus dados são preservados mesmo que o servidor seja reprovisionado devido à redimensionamento ou seja desligado. Para adicionar um disco e fixá-lo ao seu DSVM, complete os passos em [Adicionar um disco a um VM Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Os passos para adicionar um disco utilizam o Azure CLI, que já está instalado no DSVM. Pode completar os passos inteiramente a partir do próprio DSVM. Outra opção para aumentar o armazenamento é utilizar [ficheiros Azure](../../storage/files/storage-how-to-use-files-linux.md).

Para descarregar os dados, abra uma janela terminal e, em seguida, executar este comando:

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

O ficheiro descarregado não tem uma linha de cabeçalho. Vamos criar outro ficheiro que tenha um cabeçalho. Executar este comando para criar um ficheiro com os cabeçalhos apropriados:

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

Em seguida, concatena os dois ficheiros juntos:

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

O conjunto de dados tem vários tipos de estatísticas para cada e-mail:

* Colunas **como\_\_palavra freq WORD** indicam a percentagem de palavras no e-mail que *correspondem*word . Por exemplo, se a **\_palavra freq\_make** é **1**, então 1% de todas as palavras no e-mail foram *tornadas*.
* Colunas como **o char\_\_freq CHAR** indicam a percentagem de todos os caracteres no e-mail que são *CHAR*.
* **o\_\_comprimento\_mais longo** do capital é o comprimento mais longo de uma sequência de letras maiúsculas.
* **a\_\_média\_** de comprimento de execução de capital é o comprimento médio de todas as sequências de letras maiúsculas.
* **o\_\_comprimento\_total** do capital é o comprimento total de todas as sequências de letras maiúsculas.
* **o spam** indica se o e-mail foi considerado spam ou não (1 = spam, 0 = não spam).

## <a name="explore-the-dataset-by-using-r-open"></a>Explore o conjunto de dados usando o R Open

Vamos examinar os dados e fazer alguma aprendizagem de máquinabásica usando R. O DSVM vem com [o Microsoft R Open](https://mran.revolutionanalytics.com/open/) pré-instalado. As bibliotecas matemáticas multirfiosas na versão pré-instalada de R oferecem melhor desempenho do que versões de rosca única. O R Open também proporciona reprodutibilidade através de uma imagem instantânea do repositório do pacote CRAN.

Para obter cópias das amostras de código que são usadas neste walkthrough, use git para clonar o repositório Azure-Machine-Learning-Data-Science. Git é pré-instalado no DSVM. Na linha de comando git, corra:

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

Abra uma janela terminal e inicie uma nova sessão R na consola interativa R. Também pode utilizar o RStudio, que está pré-instalado no DSVM.

Importar os dados e criar o ambiente:

```R
data <- read.csv("spambaseHeaders.data")
set.seed(123)
```

Para ver estatísticas sumárias sobre cada coluna:

```R
summary(data)
```

Para uma visão diferente dos dados:

```R
str(data)
```

Esta visão mostra-lhe o tipo de cada variável e os primeiros valores no conjunto de dados.

A coluna de **spam** foi lida como uma inteiro, mas na verdade é uma variável categórica (ou fator). Para definir o seu tipo:

```R
data$spam <- as.factor(data$spam)
```

Para fazer algumas análises exploratórias, use o pacote [ggplot2,](https://ggplot2.tidyverse.org/) uma popular biblioteca de grafismo para R que está pré-instalada no DSVM. Com base nos dados sumários apresentados anteriormente, temos estatísticas sumárias sobre a frequência do caráter do ponto de exclamação. Vamos traçar essas frequências aqui executando os seguintes comandos:

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Porque a barra zero está a distorcer o enredo, vamos eliminá-lo:

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Há uma densidade não trivial acima de 1 que parece interessante. Vamos ver apenas os dados:

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Em seguida, divida-o por spam versus presunto:

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

Estes exemplos devem ajudá-lo a fazer enredos semelhantes e explorar dados nas outras colunas.

## <a name="train-and-test-a-machine-learning-model"></a>Treine e teste um modelo de aprendizagem automática

Vamos formar um par de modelos de machine learning para classificar os e-mails no conjunto de dados como contendo spam ou presunto. Nesta secção, treinamos um modelo de árvore de decisão e um modelo florestal aleatório. Depois, testamos a precisão das previsões.

> [!NOTE]
> O pacote *rpart* (Recursive Partitioning and Regression Trees) utilizado no seguinte código já está instalado no DSVM.

Primeiro, vamos dividir o conjunto de dados em conjuntos de treino e conjuntos de teste:

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

Em seguida, crie uma árvore de decisão para classificar os e-mails:

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

Aqui está o resultado:

![Um diagrama da árvore de decisão que é criada](./media/linux-dsvm-walkthrough/decision-tree.png)

Para determinar o seu bom desempenho no conjunto de treino, utilize o seguinte código:

```R
trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Para determinar o seu bom desempenho no conjunto de testes:

```R
testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Vamos também tentar um modelo de floresta aleatória. Florestas aleatórias treinam uma infinidade de árvores de decisão e produzem uma classe que é o modo das classificações de todas as árvores de decisão individuais. Eles fornecem uma abordagem de aprendizagem automática mais poderosa porque corrigem para a tendência de um modelo de árvore de decisão para sobreajustar um conjunto de dados de treino.

```R
require(randomForest)
trainVars <- setdiff(colnames(data), 'spam')
model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Tutoriais e passeios de aprendizagem profunda

Para além das amostras baseadas em quadros, é também fornecido um conjunto de walkthroughs abrangentes. Estes passadiços ajudam-no a iniciar o seu desenvolvimento de aplicações de aprendizagem profunda em domínios como imagem e compreensão de texto/linguagem.

- [Executar redes neurais em diferentes estruturas](https://github.com/ilkarman/DeepLearningFrameworks): Um passe abrangente que mostra como migrar código de uma estrutura para outra. Também demonstra como comparar o desempenho do modelo e do tempo de execução entre as estruturas. 

- [Um guia de como construir uma solução de ponta a ponta para detetar produtos dentro](https://github.com/Azure/cortana-intelligence-product-detection-from-images)de imagens : A deteção de imagem é uma técnica que pode localizar e classificar objetos dentro das imagens. A tecnologia tem o potencial de trazer enormes recompensas em muitos domínios empresariais da vida real. Por exemplo, os retalhistas podem usar esta técnica para determinar que produto um cliente pegou na prateleira. Esta informação, por sua vez, ajuda as lojas a gerir o inventário do produto. 

- [Aprendizagem profunda para áudio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Este tutorial mostra como treinar um modelo de aprendizagem profunda para deteção de eventos áudio no conjunto de dados de [sons urbanos](https://urbansounddataset.weebly.com/). O tutorial fornece uma visão geral de como trabalhar com dados áudio.

- [Classificação de documentos de texto](https://github.com/anargyri/lstm_han): Este passadiço demonstra como construir e treinar duas diferentes arquiteturas de rede neural: Rede de Atenção Hierárquica e Memória de Longo Prazo (LSTM). Estas redes neurais utilizam a API Keras para uma aprendizagem profunda para classificar documentos de texto. Keras é uma frente para três dos mais populares quadros de aprendizagem profunda: Microsoft Cognitive Toolkit, TensorFlow e Theano.

## <a name="other-tools"></a>Outras ferramentas

As restantes secções mostram-lhe como utilizar algumas das ferramentas instaladas no DSVM linux. Discutimos estas ferramentas:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL e SQuirreL SQL
* Armazém de dados do servidor SQL

### <a name="xgboost"></a>XGBoost

[O XGBoost](https://xgboost.readthedocs.org/en/latest/) proporciona uma implementação rápida e precisa da árvore.

```R
require(xgboost)
data <- read.csv("spambaseHeaders.data")
set.seed(123)

rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)

bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

pred <- predict(bst, data.matrix(testSet[, 0:57]))
accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
print(paste("test accuracy = ", accuracy))
```

XGBoost também pode ligar de Python ou uma linha de comando.

### <a name="python"></a>Python

Para o desenvolvimento da Python, as distribuições Anaconda Python 3.5 e 2.7 estão instaladas no DSVM.

> [!NOTE]
> A distribuição da Anaconda inclui a [Conda.](https://conda.pydata.org/docs/index.html) Você pode usar a Conda para criar ambientes Python personalizados que tenham versões ou pacotes diferentes instalados neles.

Vamos ler em alguns dos conjuntos de dados da base de spam e classificar os e-mails com máquinas vetoriais de suporte em Scikit-learn:

```Python
import pandas
from sklearn import svm
data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
X = data.ix[:, 0:57]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Para fazer previsões:

```Python
clf.predict(X.ix[0:20, :])
```

Para demonstrar como publicar um ponto final de Aprendizagem automática Azure, vamos fazer um modelo mais básico. Usaremos as três variáveis que usamos quando publicamos o modelo R mais cedo:

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Para publicar o modelo à Azure Machine Learning:

```Python
# Publish the model.
workspace_id = "<workspace-id>"
workspace_token = "<workspace-token>"
from azureml import services
@services.publish(workspace_id, workspace_token)
@services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
@services.returns(int) # 0 or 1
def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
    inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
    return clf.predict(inputArray)

# Get some info about the resulting model.
predictSpam.service.url
predictSpam.service.api_key

# Call the model
predictSpam.service(1, 1, 1)
```

> [!NOTE]
> Esta opção está disponível apenas para Python 2.7. Ainda não é suportado na Python 3.5. Para correr, utilize **/anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub

A distribuição da Anaconda no DSVM vem com um Caderno Jupyter, um ambiente transversal para a partilha de código e análise python, R ou Julia. O Caderno Jupyter é acedido através do JupyterHub. Insere-se utilizando o nome de utilizador\<e senha de utilizador do\>Linux local em https:// nome DNS DNS ou endereço IP :8000/. Todos os ficheiros de configuração para JupyterHub são encontrados em /etc/jupyterhub.

> [!NOTE]
> Para utilizar o Gestor de `pip` Pacotes Python (através do comando) a partir de um Caderno Jupyter no núcleo atual, utilize este comando na célula de código:
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Para utilizar o instalador Conda (através do `conda` comando) a partir de um Caderno Jupyter no núcleo atual, utilize este comando numa célula de código:
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Vários cadernos de amostras já estão instalados no DSVM:

* Amostra de cadernos Python:
  * [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Caderno da amostra R:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> A língua Julia também está disponível a partir da linha de comando no Linux DSVM.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) *(R* *A*nalytical *T*ool *T*o *L*earn *E*asily) é uma ferramenta gráfica R para a mineração de dados. Rattle tem uma interface intuitiva que facilita a carga, exploração e transformação de dados, e para construir e avaliar modelos. [Rattle: A Data Mining GUI for R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) fornece um walkthrough que demonstra as características de Rattle.

Instale e inicie o Rattle executando estes comandos:

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> Não precisas de instalar o Rattle no DSVM. No entanto, pode ser solicitado a instalar pacotes adicionais quando o Rattle abrir.

Rattle usa uma interface baseada em separadores. A maioria dos separadores corresponde a passos no Processo de Ciência de Dados da [Equipa](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), como carregar dados ou explorar dados. O processo de ciência de dados flui da esquerda para a direita através dos separadores. O último separador contém um registo dos comandos R que foram dirigidos por Rattle.

Para carregar e configurar o conjunto de dados:

1. Para carregar o ficheiro, selecione o separador **Dados.**
1. Escolha o seletor ao lado do Nome de **Ficheiro**, e, em seguida, selecione **spambaseHeaders.data**.
1. Para carregar o ficheiro. selecionar **Executar**. Deve ver um resumo de cada coluna, incluindo o seu tipo de dados identificado; seja uma entrada, um alvo ou outro tipo de variável; e o número de valores únicos.
1. O Chocalho identificou corretamente a coluna de **spam** como o alvo. Selecione a coluna **de spam** e, em seguida, detete o Tipo de **Dados do Alvo** para **Categórico**.

Para explorar os dados:

1. Selecione o separador **Explore.**
1. Para ver informações sobre os tipos variáveis e algumas estatísticas sumárias, selecione **Executar Resumo** > **.**
1. Para ver outros tipos de estatísticas sobre cada variável, selecione outras opções, como **Descrever** ou **Básicos**.

Também pode usar o separador **Explore** para gerar enredos perspicazes. Para traçar um histograma dos dados:

1. Selecione **Distribuições**.
1. Para **word_freq_remove** e **word_freq_you,** selecione **Histogram**.
1. Selecione **Executar**. Você deve ver ambos os enredos de densidade em uma única janela de gráfico, onde é claro que a palavra _que você_ aparece muito mais frequentemente em e-mails do que _remover_.

Os enredos da **Correlation** também são interessantes. Para criar um enredo:

1. Para **tipo,** selecione **Correlation**.
1. Selecione **Executar**.
1. Rattle avisa que recomenda um máximo de 40 variáveis. Selecione **Sim** para ver o enredo.

Há algumas correlações interessantes que surgem: a _tecnologia_ está fortemente correlacionada com _HP_ e _laboratórios,_ por exemplo. Também está fortemente correlacionado com _650_ porque o código de área dos dadores de conjunto de dados é de 650.

Os valores numéricos para as correlações entre palavras estão disponíveis na janela **Explore.** É interessante notar, por exemplo, que a _tecnologia_ está negativamente correlacionada com _o seu_ _dinheiro._

O chocalho pode transformar o conjunto de dados para lidar com algumas questões comuns. Por exemplo, pode reformular funcionalidades, imputar valores em falta, lidar com outliers e remover variáveis ou observações que tenham dados em falta. O chocalho também pode identificar regras de associação entre observações e variáveis. Estes separadores não estão cobertos por esta passagem introdutória.

O chocalho também pode executar a análise do cluster. Vamos excluir algumas funcionalidades para facilitar a leitura da saída. No separador **Dados,** **selecione Ignore** ao lado de cada uma das variáveis, exceto estes 10 itens:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

Volte ao separador **Cluster.** Selecione **KMeans**, e, em seguida, coloque **o número de clusters** para **4**. Selecione **Executar**. Os resultados são apresentados na janela de saída. Um cluster tem alta frequência de _George_ e _HP_, e é provavelmente um e-mail de negóciolegítimo.

Para construir um modelo básico de aprendizagem de máquinas de árvores:

1. Selecione o separador **Modelo,**
1. Para o **Tipo,** selecione **Árvore**.
1. Selecione **Executar** para exibir a árvore em forma de texto na janela de saída.
1. Selecione o botão **Draw** para visualizar uma versão gráfica. A árvore de decisão é semelhante à árvore que obtivemos anteriormente usando o rpart.

Uma característica útil do Rattle é a sua capacidade de executar vários métodos de aprendizagem automática e avaliá-los rapidamente. Aqui estão os passos:

1. Para **Tipo,** selecione **All**.
1. Selecione **Executar**.
1. Quando o Rattle terminar a corrida, pode selecionar qualquer valor **tipo,** como **o SVM,** e ver os resultados.
1. Também pode comparar o desempenho dos modelos no conjunto de validação utilizando o separador **Avaliar.** Por exemplo, a seleção **error Matrix** mostra-lhe a matriz de confusão, erro geral e erro de classe médio para cada modelo no conjunto de validação. Também pode traçar curvas ROC, executar análises de sensibilidade e fazer outros tipos de avaliações de modelos.

Quando terminar os modelos de construção, selecione o separador **Log** para visualizar o código R que foi executado pela Rattle durante a sua sessão. Pode selecionar o botão **Export** para o guardar.

> [!NOTE]
> A libertação atual de Rattle contém um bug. Para modificar o script ou utilizá-lo para repetir **#** os seus passos mais tarde, deve inserir um personagem em frente à *Exportação deste registo...* no texto do registo.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL e SQuirreL SQL

O DSVM vem com postgreSQL instalado. PostgreSQL é uma base de dados relacional sofisticada e de código aberto. Esta secção mostra-lhe como carregar o conjunto de dados da base de spam no PostgreSQL e, em seguida, questioná-lo.

Antes de poder carregar os dados, tem de permitir a autenticação de palavra-passe do local. Numa linha de comandos, execute:

```Bash
sudo gedit /var/lib/pgsql/data/pg_hba.conf
```

Perto da parte inferior do ficheiro de config estão várias linhas que detalham as ligações permitidas:

```
# "local" is only for Unix domain socket connections:
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

Altere a linha de **ligações locais IPv4** para utilizar **md5** em vez de **identificador**, para que possamos iniciar sessão usando um nome de utilizador e senha:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

Em seguida, reinicie o serviço PostgreSQL:

```Bash
sudo systemctl restart postgresql
```

Para lançar o *psql* (um terminal interativo para postgresQL) como utilizador de postgres incorporados, execute este comando:

```Bash
sudo -u postgres psql
```

Crie uma nova conta de utilizador utilizando o nome de utilizador da conta Linux que usou para iniciar sessão. Criar uma senha:

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

Faça login no PSQL:

```Bash
psql
```

Importar os dados para uma nova base de dados:

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

Agora, vamos explorar os dados e executar algumas consultas usando o SQuirreL SQL, uma ferramenta gráfica que você pode usar para interagir com bases de dados através de um controlador JDBC.

Para começar, no menu **aplicações,** abra o SQuirreL SQL. Para configurar o condutor:

1. Selecione**Controladores de visualização do** **Windows** > .
1. Clique à direita **PostgreSQL** e selecione **Modificar o Controlador**.
1. Selecione **extra class path** > **add**.
1. Para **nome de ficheiro**, insira **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**.
1. Selecione **Open** (Abrir).
1. Selecione **Lista de Condutores**. Para **nome de classe**, selecione **org.postgresql.Driver**, e, em seguida, selecione **OK**.

Para configurar a ligação ao servidor local:

1. Selecione **Windows** > **View Aliases.**
1. Selecione o **+** botão para criar um novo pseudónimo. Para obter o novo nome, introduza a **base de dados de spam**. 
1. Para **driver**, selecione **PostgreSQL**.
1. Delineie o URL para **jdbc:postgresql://localhost/spam**.
1. Introduza o nome de utilizador e a palavra-passe.
1. Selecione **OK**.
1. Para abrir a janela **de ligação,** clique duas vezes no pseudónimo da base de dados de **spam.**
1. Selecione **Ligar**.

Para fazer algumas perguntas:

1. Selecione o **separador SQL.**
1. Na caixa de consulta na parte superior do **separador SQL,** introduza uma consulta básica, como `SELECT * from data;`.
1. Prima Ctrl+Enter para executar a consulta. Por predefinição, o SQuirreL SQL devolve as primeiras 100 linhas da sua consulta.

Há muitas mais consultas que pode fazer para explorar estes dados. Por exemplo, como é que a frequência da palavra *difere* entre spam e presunto?

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

Ou, quais são as características do e-mail que frequentemente contêm *3d?*

```SQL
SELECT * from data order by word_freq_3d desc;
```

A maioria dos e-mails que têm uma alta ocorrência de *3d* aparentemente são spam. Esta informação pode ser útil para a construção de um modelo preditivo para classificar e-mails.

Se pretender fazer machine learning utilizando dados armazenados numa base de dados PostgreSQL, considere utilizar [o MADlib](https://madlib.incubator.apache.org/).

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

O Azure SQL Data Warehouse é uma base de dados baseada em nuvem e escala que pode processar volumes maciços de dados, tanto relacionais como não relacionais. Para mais informações, consulte o armazém de [dados Azure SQL?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Para ligar ao armazém de dados e criar a tabela, executar o seguinte comando a partir de um pedido de comando:

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

No aviso sqlcmd, executar este comando:

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

Copiar os dados utilizando o BCP:

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> O ficheiro descarregado contém terminações de linha ao estilo do Windows. A ferramenta bcp espera finais de linha ao estilo Unix. Use a bandeira -r para dizer ao BCP.

Em seguida, consulta utilizando sqlcmd:

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

Também pode consultar utilizando sQuirreL SQL. Siga passos semelhantes ao PostgreSQL utilizando o controlador SQL Server JDBC. O condutor JDBC está na pasta /usr/share/java/jdbcdrivers/sqljdbc42.jar.

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral dos artigos que o percorrem através das tarefas que compõem o processo de ciência de dados em Azure, consulte o Processo de Ciência de [Dados da Equipa](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Para uma descrição de walkthroughs de ponta a ponta que demonstram os passos no Processo de Ciência de Dados da equipa para cenários específicos, consulte os [walkthroughs do Processo](../team-data-science-process/walkthroughs.md)de Ciência de Dados da Equipa . Os passadiços também ilustram como combinar ferramentas e serviços de nuvem e no local num fluxo de trabalho ou oleoduto para criar uma aplicação inteligente.
