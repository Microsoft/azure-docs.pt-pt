---
title: Saiba como utilizar a máquina de Virtual de ciência de dados do Linux
titleSuffix: Azure
description: Como realizar várias tarefas de ciência de dados comuns com a VM de ciência de dados do Linux.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.custom: seodec18
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: gokuma
ms.openlocfilehash: 6e8883870cc0f035df5122e91449f04203836218
ms.sourcegitcommit: 5e4ca656baf3c7d370ab3c0fbad0278aa2c9f1e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319470"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Ciência de dados com uma Data Science Máquina Virtual do Linux no Azure
Estas instruções mostram como realizar várias tarefas de ciência de dados comuns com a VM de ciência de dados do Linux. A Máquina Virtual de ciência de dados de Linux (DSVM) é uma imagem de máquina virtual disponível no Azure que está pré-instalado com uma coleção de ferramentas utilizadas para análise de dados e machine learning. Os componentes de software importantes estão descritos a [aprovisionar a máquina de Virtual de ciência de dados do Linux](linux-dsvm-intro.md) tópico. A imagem de VM torna mais fácil começar a fazer ciência de dados em minutos, sem ter de instalar e configurar cada uma das ferramentas individualmente. Pode facilmente aumentar verticalmente a VM, se necessário e pará-la quando não está em utilização. Portanto, este recurso é económico e flexível.

As tarefas de ciência de dados demonstradas nestas instruções siga os passos descritos no [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview). Este processo fornece uma abordagem sistemática para ciência de dados que permite às equipes de cientistas de dados e colaborar de maneira eficiente ao longo do ciclo de vida da criação de aplicativos inteligentes. O processo de ciência de dados também fornece uma estrutura iterativa para ciência de dados que pode ser seguida por uma pessoa.

Vamos analisar o [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) conjunto de dados nestas instruções. Este é um conjunto de mensagens de e-mail que estão marcados como spam ou presunto! (ou seja, não são spam), e também contém algumas estatísticas sobre o conteúdo dos e-mails. As estatísticas incluídas são discutidas na próxima, mas uma secção.

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder utilizar uma máquina de Virtual de ciência de dados do Linux, tem de ter o seguinte:

* Uma **subscrição do Azure**. Se ainda não tiver uma, veja [crie hoje a sua conta gratuita do Azure](https://azure.microsoft.com/free/).
* R [ **VM de ciência de dados do Linux**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Para obter informações sobre esta VM de aprovisionamento, consulte [aprovisionar a máquina de Virtual de ciência de dados do Linux](linux-dsvm-intro.md).
* [X2Go](https://wiki.x2go.org/doku.php) instalado no seu computador e abrir uma sessão XFCE. Para obter informações sobre como instalar e configurar uma **cliente X2Go**, consulte [instalar e configurar o cliente de X2Go](linux-dsvm-intro.md#installing-and-configuring-x2go-client).
* Para uma experiência de rolagem mais suave, alterne o sinalizador de gfx.xrender.enabled no sobre: configuração no navegador FireFox de VMs. [Veja mais aqui. ](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Considere também a alternar *mousewheel.enable_pixel_scrolling* como False. [Instruções aqui.](https://support.mozilla.org/en-US/questions/981140)
* Uma **AzureML conta**. Se ainda não tiver uma, inscreva-se um novo aos [home page do AzureML](https://studio.azureml.net/). Existe um escalão de utilização gratuita para o ajudar a começar a utilizar.

## <a name="download-the-spambase-dataset"></a>Transferir o conjunto de dados spambase
O [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) conjunto de dados é um relativamente pequeno conjunto de dados que contém apenas 4601 exemplos. Este é um tamanho conveniente usar ao demonstrar que alguns dos principais recursos da VM de ciência de dados à medida que mantém os requisitos de recursos modestos.

> [!NOTE]
> Este passo a passo foi criada no D2 v2 com tamanho dados ciência máquinas virtuais do Linux (CentOS Edition). Este tamanho DSVM é capaz de processar os procedimentos nestas instruções.
>
>

Se precisar de mais espaço de armazenamento, pode criar discos adicionais e anexe-os a sua VM. Esses discos utilizam persistente armazenamento do Azure, para que os seus dados são preservados, mesmo quando o servidor é reaprovisionado devido a redimensionamento ou é encerrado. Para adicionar um disco e anexá-lo à sua VM, siga as instruções em [adicionar um disco a uma VM do Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Estes passos utilizam a Interface de linha de comandos do Azure (CLI) do Azure, que já está instalado no DSVM. Portanto, estes procedimentos podem ser feitos totalmente a partir da própria VM. Outra opção para aumentar o armazenamento é usar [ficheiros do Azure](../../storage/files/storage-how-to-use-files-linux.md).

Para transferir os dados, abra uma janela de terminal e execute este comando:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

O ficheiro transferido não tem uma linha de cabeçalho, então, vamos criar outro ficheiro que tenha um cabeçalho. Execute este comando para criar um ficheiro com os cabeçalhos apropriados:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Em seguida, concatenar os dois arquivos, juntamente com o comando:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

O conjunto de dados tem vários tipos de estatísticas sobre cada e-mail:

* Colunas como ***word\_freq\_WORD*** indica a percentagem de palavras no e-mail que corresponde ao *WORD*. Por exemplo, se *word\_freq\_tornar* é 1, em seguida, 1% de todas as palavras no e-mail foram *tornar*.
* Colunas como ***char\_freq\_CHAR*** indica a percentagem de todos os caracteres no e-mail que foram *CHAR*.
* ***capital\_execute\_comprimento\_mais longo*** é o período mais longo de uma sequência de letras maiúsculas.
* ***capital\_execute\_comprimento\_médio*** é a duração média de todas as sequências de letras maiúsculas.
* ***capital\_execute\_comprimento\_total*** é o comprimento total de todas as sequências de letras maiúsculas.
* ***spam*** indica se o e-mail foi considerado spam ou não (1 = spam, 0 = não spam).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Explorar o conjunto de dados com o Microsoft R Open
Vamos examinar os dados e fazer algumas básico de machine learning com R. A VM de ciência de dados vem com [Microsoft R Open](https://mran.revolutionanalytics.com/open/) pré-instalado. As bibliotecas matemáticas com múltiplos threads nesta versão do R oferecem melhor desempenho do que várias versões de thread único. Microsoft R Open também fornece reprodutibilidade através da utilização de um instantâneo do repositório de pacotes CRAN.

Para obter cópias dos exemplos de código utilizados nestas instruções, clone o **Azure-Machine-Learning-ciência de dados** repositório utilizando o git, que é previamente instalada na VM. A partir da linha de comandos do git, execute:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Abra uma janela de terminal e iniciar uma nova sessão de R com o console interativo de R ou utilizar o r Studio pré-instalado na máquina.


Para importar os dados e configurar o ambiente, execute:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Para ver estatísticas de resumo sobre cada coluna:

    summary(data)

Para obter uma exibição diferente dos dados:

    str(data)

Isso mostra o tipo de cada variável e os primeiro alguns valores no conjunto de dados.

O *spam* coluna foi lido como um número inteiro, mas é realmente um categóricos variável (ou fator). Para definir o tipo:

    data$spam <- as.factor(data$spam)

Para fazer algumas análises exploratórias, utilize o [ggplot2](https://ggplot2.tidyverse.org/) empacotar, uma biblioteca de gráfico popular para R que já está instalado na VM. Nota, a partir dos dados de resumos apresentada anteriormente, o que temos estatísticas de resumo na frequência do caráter de ponto de exclamação. Vamos desenhar esses frequências com os seguintes comandos:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Uma vez que a barra de zero é distorcer o desenho, vamos nos livrar dele:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Há uma densidade não trivial acima 1 que me parece interessante. Vamos examinar apenas esses dados:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Em seguida, dividi-la por presunto vs de spam:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Estes exemplos devem permitir-lhe efetuar gráficos semelhante de outras colunas para explorar os dados contidos neles.

## <a name="train-and-test-an-ml-model"></a>Treinar e testar um modelo de ML
Agora vamos dar formação alguns modelos de machine learning para classificar os e-mails no conjunto de dados como contendo span ou presunto!. Vamos preparar um modelo de árvore de decisão e um modelo de floresta aleatório nesta secção e, em seguida, testar sua precisão de suas previsões.

> [!NOTE]
> O pacote de rpart (criação de partições de recursiva e árvores de regressão) utilizado no código a seguir já está instalado na VM de ciência de dados.
>
>

Primeiro, vamos dividir o conjunto de dados em conjuntos de formação e teste:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

E, em seguida, crie uma árvore de decisão para classificar os e-mails.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Este é o resultado:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

Para determinar a eficiência com que ele executa no conjunto de treinamento, utilize o seguinte código:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Para determinar a eficiência com que ele executa o conjunto de teste:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Vamos também experimentar um modelo de floresta aleatório. Florestas aleatórias treinar uma infinidade de árvores de decisões e uma classe que é o modo das classificações a partir de todas as árvores de decisão individual de saída. Eles fornecem uma mais poderosas de aprendizagem automática abordagem à medida que eles corrigir a tendência de um modelo de árvore de decisão de overfit um conjunto de dados de treinamento.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio"></a>Implementar um modelo do Azure Machine Learning Studio
[O Azure Machine Learning Studio](https://studio.azureml.net/) é um serviço cloud que torna mais fácil criar e implementar modelos de Análise Preditiva. Um dos recursos interessantes do Azure Machine Learning studio é sua capacidade de publicar qualquer função R como um serviço web. O pacote do Azure Machine Learning studio R torna a implantação fácil fazê-lo diretamente a partir de nossa sessão de R em DSVM.

Para implementar o código de árvore de decisão da seção anterior, terá de iniciar sessão no Azure Machine Learning Studio. Terá do ID de área de trabalho e um token de autorização para iniciar sessão. Para localizar estes valores e inicializar as variáveis do Azure Machine Learning com eles:

Selecione **definições** no menu do lado esquerdo. Tenha em atenção sua **ID da área de trabalho**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Selecione **Tokens de autorização** no menu de sobrecarga e nota sua **primário Token de autorização**.![ 3](./media/linux-dsvm-walkthrough/workspace-token.png)

Carga de **AzureML** do pacote e, em seguida, definir os valores das variáveis com o seu ID de token e área de trabalho na sua sessão de R em DSVM:

    if(!require("AzureML")) install.packages("AzureML")
    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Vamos simplificar o modelo para fazer esta demonstração mais fácil de implementar. Escolher as três variáveis na árvore de decisões mais próxima para a raiz e crie uma nova árvore usando apenas essas três variáveis:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Precisamos de uma função de predição que usa os recursos como entrada e retorna os valores previstos:

    predictSpam <- function(newdata) {
      predictDF <- predict(model.rpart, newdata = newdata)
      return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }


Publicar a função de predictSpam AzureML utilizando o **publishWebService** função:

    spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)


Esta função aceita a **predictSpam** funcionar, cria um serviço da web com o nome **spamWebService** com definidos entradas e saídas e devolve informações sobre o novo ponto final.

Ver detalhes do serviço mais recente web publicadas, incluindo o respetivo ponto final de API e acessar chaves com o comando:

    s<-tail(services(ws, name = "spamWebService"), 1)
    ep <- endpoints(ws,s)
    ep

Para experimentá-lo no primeiro conjunto de 10 linhas do teste:

    consume(ep, smallTestSet[1:10, ])


## <a name="use-other-tools-available"></a>Utilizar outras ferramentas disponíveis
As seções restantes mostram como usar algumas das ferramentas instaladas na VM de ciência de dados do Linux. Eis a lista de ferramentas mencionadas:

* XGBoost
* Python
* Jupyterhub
* Rattle
* PostgreSQL & Squirrel SQL
* Armazém de dados do SQL Server

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) é uma ferramenta que fornece uma implementação de árvore precisos e de elevada.

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

XGBoost também pode chamar a partir de python ou uma linha de comando.

## <a name="python"></a>Python
Para o desenvolvimento com o Python, foram instaladas as distribuições de Anaconda Python 2.7 e 3.5 DSVM.

> [!NOTE]
> Inclui a distribuição de Anaconda [Conda](https://conda.pydata.org/docs/index.html), que podem ser utilizadas para criar ambientes personalizados para o Python que têm versões diferentes e/ou os pacotes instalados nos mesmos.
>
>

Vamos ler em alguns do conjunto de dados spambase e classificar os e-mails com as máquinas de vetores de suporte no scikit-Saiba mais:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para fazer previsões:

    clf.predict(X.ix[0:20, :])

Para mostrar como publicar um ponto de final do AzureML, vamos criar um modelo mais simples as três variáveis como fizemos após a publicação anteriormente o modelo de R.

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para publicar o modelo no AzureML:

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

> [!NOTE]
> Isso só está disponível para o python 2.7 e ainda não é suportado no 3.5. Executar com **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub
A distribuição de Anaconda na DSVM vem com um bloco de notas do Jupyter, um ambiente de várias plataformas para compartilhar o código de Python, R ou Julia e análise. O bloco de notas do Jupyter é acedido através do JupyterHub. Iniciar sessão com o seu nome de utilizador local do Linux e a palavra-passe no ***https://\<nome da VM de DNS ou endereço IP\>: 8000 /***. Todos os ficheiros de configuração para JupyterHub encontram-se no diretório **nomedeanfitrião jupyterhub**.

> [!NOTE]
> Utilizar o Gestor de pacotes do Python (através do `pip` comando) de um bloco de notas do Jupyter no kernel do atual, o comando seguinte pode ser utilizado na célula de código, por exemplo:
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> 
> 
> [!NOTE]
> Para utilizar o instalador de Conda (via o `conda` comando) de um bloco de notas do Jupyter no kernel do atual, o comando seguinte pode ser utilizado na célula de código, por exemplo:
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Vários blocos de notas de exemplo já estão instalados na VM:

* Consulte a [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) para um bloco de notas de Python de exemplo.
* Ver [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) para obter um exemplo **R** bloco de notas.
* Consulte a [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) para outro exemplo **Python** bloco de notas.

> [!NOTE]
> A linguagem Julia também está disponível a partir da linha de comandos na VM de ciência de dados do Linux.
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (o R analítico ferramenta para saber mais facilmente) é uma ferramenta gráfica de R para extração de dados. Ele tem uma interface intuitiva que torna mais fácil carregar, explorar e transformar dados e criar e avaliar modelos.  O artigo [Rattle: Uma GUI de mineração de dados para R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) fornece um passo a passo que demonstra as funcionalidades do mesmo.

Instalar e iniciar Rattle com os seguintes comandos:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Não é necessária a instalação em DSVM. Mas Rattle poderá pedir-lhe para instalar pacotes adicionais quando ele é carregado.
>
>

Rattle usa uma interface baseada em guias. A maioria das guias corresponde aos passos a [processo de ciência de dados](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), como carregamento de dados ou ao explorá-lo. O processo de ciência de dados flui da esquerda para direita nos separadores. Mas a última guia contém um registo dos comandos executados pelo Rattle R.

Para carregar e configurar o conjunto de dados:

* Para carregar o ficheiro, selecione o **dados** separador, em seguida,
* Escolha o Seletor de junto a **Filename** e escolha **spambaseHeaders.data**.
* Ao carregar o ficheiro. Selecione **Execute** na linha superior dos botões. Deverá ver um resumo de cada coluna, incluindo o respetivo tipo de dados identificados, quer se trate de uma entrada, um destino ou outro tipo de variável e o número de valores exclusivos.
* Rattle corretamente identificou os **spam** coluna como o destino. Selecione a coluna de spam, em seguida, configure as **tipo de dados de destino** ao **Categoric**.

Para explorar os dados:

* Selecione o **explorar** separador.
* Clique em **resumo**, em seguida, **Execute**, para ver algumas informações sobre os tipos de variáveis e algumas estatísticas de resumo.
* Para ver outros tipos de estatísticas sobre cada variável, selecione outras opções, como **Descrição** ou **Noções básicas**.

O **explorar** separador também permite gerar vários gráficos informativos. Para desenhar um histograma dos dados:

* Selecione **distribuições**.
* Verifique **histograma** para **word_freq_remove** e **word_freq_you**.
* Selecione **Executar**. Deverá ver ambos os gráficos de densidade numa janela de gráficos único, onde fica claro que a palavra "you" parece muito mais freqüência nos e-mails que "Remover".

Os gráficos de correlação também são interessantes. Para criar um:

* Escolher **correlação** como o **tipo**, em seguida,
* Selecione **Executar**.
* Rattle avisa-o de que ele recomenda um máximo de 40 variáveis. Selecione **Sim** para ver o desenho.

Existem alguns correlações interessantes que surgem: "tecnologia" vivamente é correlacionada com "HP" e "laboratórios", por exemplo. É também vivamente correlacionada com o "650", porque o código de área de doadores o conjunto de dados é 650.

Os valores numéricos para as correlações entre as palavras estão disponíveis na janela de explorar. É interessante para nota, por exemplo, que "tecnologia" negativamente está correlacionada com "seu" e "dinheiro".

Rattle pode transformar o conjunto de dados para lidar com alguns problemas comuns. Por exemplo, ele permite-lhe rescale funcionalidades, impute valores em falta, lidar com valores atípicos e remover variáveis ou observações com dados em falta. Rattle também pode identificar as regras de associação entre as observações e/ou variáveis. Esses guias estão fora do âmbito para este passo a passo introdutória.

Rattle também pode executar análises de cluster. Vamos excluir alguns recursos para facilitar a leitura de saída. Sobre o **dados** separador, escolha **ignorar** junto a cada uma das variáveis, exceto estes dez itens:

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

Em seguida, volte à **Cluster** separador, escolha **KMeans**e defina o *número de clusters* para 4. Em seguida, **executar**. Os resultados são exibidos na janela de saída. Um cluster tem alta frequência de "george" e "hp" e é provavelmente um e-mail de negócios legítima.

Para criar um modelo de aprendizagem de máquina de árvore de decisão simples sobre:

* Selecione o **modelo** separador,
* Escolher **árvore** como o **tipo**.
* Selecione **Execute** para apresentar a árvore no formato de texto na janela de saída.
* Selecione o **desenhar** botão para ver uma versão gráfica. Isso é bastante semelhante à árvore que obtivemos anteriormente usando *rpart*.

Um dos recursos interessantes de Rattle é sua capacidade de executar vários métodos de aprendizado de máquina e rapidamente compará-las. Este é o procedimento:

* Escolher **todos os** para o **tipo**.
* Selecione **Executar**.
* Depois de concluir pode clicar em qualquer único **tipo**, como **SVM**e veja os resultados.
* Também pode comparar o desempenho dos modelos de sobre a validação definida com o **Evaluate** separador. Por exemplo, o **erro matriz** seleção mostra-lhe a matriz de confusão, o erro geral e o erro de classe média para cada modelo no conjunto de validação.
* Também pode traçar curvas de cor MULTICLASSE, executar uma análise de sensibilidade e fazer outros tipos de avaliações de modelo.

Quando tiver terminado de criação de modelos, selecione o **Log** separador para ver o código de R são executado por Rattle durante a sessão. Pode selecionar o **exportar** botão para guardá-lo.

> [!NOTE]
> Há um bug na versão atual do Rattle. Para modificar o script ou utilizá-lo a repetir os passos mais tarde, tem de inserir um caractere # à frente do *exportar este registo...*  no texto do registo.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL & Squirrel SQL
A DSVM vem com o PostgreSQL instalado. PostgreSQL é um banco de dados relacional sofisticado e código-fonte aberto. Esta secção mostra como carregar nosso conjunto de dados de spam para o PostgreSQL e, em seguida, consultá-los.

Antes de carregar os dados, terá de permitir a autenticação de palavra-passe do localhost. No prompt de comando:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Perto da parte inferior do ficheiro de configuração são várias linhas que detalham as conexões permitidas:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Altere a linha de "IPv4 local ligações" para utilizar o md5 em vez de ident, portanto, o pode fazer logon usando um nome de utilizador e palavra-passe:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

E reinicie o serviço de postgres:

    sudo systemctl restart postgresql

Para iniciar o psql, um terminal interativo para o PostgreSQL, como o utilizador postgres incorporada, execute o seguinte comando a partir de uma linha:

    sudo -u postgres psql

Crie uma nova conta de utilizador, com o mesmo nome de utilizador que a conta de Linux que tem atualmente sessão iniciada como e conceda-lhe uma palavra-passe:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Em seguida, inicie sessão no psql como o utilizador:

    psql

E importar os dados para uma nova base de dados:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Agora, vamos explorar os dados e executar algumas consultas usando **Squirrel SQL**, uma ferramenta gráfica que permite-lhe interagir com bases de dados por meio de um controlador JDBC.

Para começar, inicie o Squirrel SQL no menu de aplicações. Para configurar o controlador:

* Selecione **Windows**, em seguida, **ver controladores**.
* Com o botão direito no **PostgreSQL** e selecione **modificar Driver**.
* Selecione **Extra classe caminho**, em seguida, **adicionar**.
* Introduza ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** para o **nome do ficheiro** e
* Selecione **Open** (Abrir).
* Escolha a lista de Drivers, em seguida, selecione **org.postgresql.Driver** na **nome da classe**e selecione **OK**.

Para configurar a ligação ao servidor local:

* Selecione **Windows**, em seguida, **ver os Aliases.**
* Escolha o **+** para verificar um novo alias.
* Atribua o nome *base de dados de Spam*, escolha **PostgreSQL** no **Driver** pendente.
* Definir o URL *jdbc:postgresql://localhost/spam*.
* Introduza o seu *nome de utilizador* e *palavra-passe*.
* Clique em **OK**.
* Para abrir o **ligação** janela, clique duas vezes o ***base de dados de Spam*** alias.
* Selecione **Ligar**.

Para executar algumas consultas:

* Selecione o **SQL** separador.
* Introduza uma consulta simples como `SELECT * from data;` na caixa de texto de consulta na parte superior do separador SQL.
* Prima **Ctrl-Enter** executá-lo. Por predefinição o Squirrel SQL devolve as primeiras 100 linhas da sua consulta.

Existem muitas consultas mais que é possível executar para explorar esses dados. Por exemplo, como é que a frequência da palavra *tornar* diferem entre spam e de presunto!?

    SELECT avg(word_freq_make), spam from data group by spam;

Ou quais são as características de e-mail que contêm frequentemente *3d*?

    SELECT * from data order by word_freq_3d desc;

A maioria das mensagens de e-mail que têm uma elevada ocorrência de *3d* são aparentemente de spam, poderia ser um recurso útil para a criação de um modelo preditivo para classificar os e-mails.

Se quiser fazer o machine learning com dados armazenados num banco de dados do PostgreSQL, considere utilizar [MADlib](https://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>Armazém de dados do SQL Server
O Azure SQL Data Warehouse é uma base de dados de escalabilidade horizontal, baseada na nuvem, capaz de processar grandes volumes de dados, tanto relacionais como não relacionais. Para obter mais informações, consulte [o que é o Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Para ligar ao armazém de dados e criar a tabela, execute o seguinte comando numa linha de comandos:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Em seguida, na linha de comandos sqlcmd:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Copiar dados com o bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> As fins de linha no ficheiro descarregado são o estilo do Windows, mas o bcp espera estilo UNIX, portanto, precisamos dizer que bcp com o sinalizador - r.
>
>

E consultar com sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Também pode consultar com o Squirrel SQL. Siga os passos semelhantes para o PostgreSQL, com o Microsoft MSSQL Server JDBC Driver, que pode ser encontrado na ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Passos Seguintes
Para uma descrição geral dos tópicos que lhe mostram as tarefas que compõem o processo de ciência de dados no Azure, consulte [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Para obter uma descrição de outras instruções ponto a ponto que demonstram os passos no Team Data Science Process para cenários específicos, consulte [instruções passo a passo do processo de ciência de dados de equipa](../team-data-science-process/walkthroughs.md). A instruções passo a passo também mostram como combinar as ferramentas de nuvem e no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente.
