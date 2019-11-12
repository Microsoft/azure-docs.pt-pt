---
title: Explore o Linux
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como concluir várias tarefas comuns de ciência de dados usando o Máquina Virtual de Ciência de Dados do Linux.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 07/16/2018
ms.openlocfilehash: b073c4244d2a7abc7c2c066c3fad036f0caa5faa
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929551"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Ciência de dados com uma Máquina Virtual de Ciência de Dados do Linux no Azure

Este tutorial mostra como concluir várias tarefas comuns de ciência de dados usando o Máquina Virtual de Ciência de Dados do Linux (DSVM). O DSVM do Linux é uma imagem de máquina virtual disponível no Azure pré-instalado com uma coleção de ferramentas comumente usadas para análise de dados e aprendizado de máquina. Os principais componentes de software são discriminados para [provisionar o máquina virtual de ciência de dados do Linux](linux-dsvm-intro.md). A imagem DSVM facilita a introdução de ciência de dados em minutos, sem a necessidade de instalar e configurar cada uma das ferramentas individualmente. Você pode dimensionar facilmente o DSVM, se necessário, e pode interrompê-lo quando não estiver em uso. O recurso DSVM é elástico e econômico.

As tarefas de ciência de dados demonstradas neste passo a passos seguem as etapas descritas em [o que é o processo de ciência de dados de equipe?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) O processo de ciência de dados de equipe é uma abordagem sistemática para a ciência de dados que ajuda as equipes de cientistas de dados a colaborarem com eficiência no ciclo de vida da criação de aplicativos inteligentes. O processo de ciência de dados também fornece uma estrutura iterativa para ciência de dados que pode ser seguida por um indivíduo.

Neste tutorial, analisamos o conjunto de [baseado em spam](https://archive.ics.uci.edu/ml/datasets/spambase) . Baseado em spam é um conjunto de emails que são marcados como spam ou Ham (não spam). O baseado em spam também contém algumas estatísticas sobre o conteúdo dos emails. Falaremos sobre as estatísticas posteriormente no passo a passo.

## <a name="prerequisites"></a>Pré-requisitos

Para poder usar um DSVM do Linux, você deve ter os seguintes pré-requisitos:

* **Subscrição do Azure**. Para obter uma assinatura do Azure, consulte [criar sua conta gratuita do Azure hoje mesmo](https://azure.microsoft.com/free/).
* [**Máquina virtual de ciência de dados Linux**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Para obter informações sobre como provisionar a máquina virtual, consulte [provisionar o Linux máquina virtual de ciência de dados](linux-dsvm-intro.md).
* [**X2Go**](https://wiki.x2go.org/doku.php) instalado em seu computador com uma sessão aberta do Xfce. Para obter mais informações, consulte [instalar e configurar o cliente X2Go](linux-dsvm-intro.md#x2go).
* Para uma experiência de rolagem mais suave, no navegador da Web Firefox do DSVM, alterne o sinalizador `gfx.xrender.enabled` em `about:config`. [Saiba mais](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Além disso, considere definir `mousewheel.enable_pixel_scrolling` como `False`. [Saiba mais](https://support.mozilla.org/questions/981140).
* **Azure Machine Learning conta**. Se você ainda não tiver uma, Inscreva-se para uma nova conta no [Azure Machine Learning Home Page](https://azure.microsoft.com/free/services/machine-learning//).

## <a name="download-the-spambase-dataset"></a>Baixar o conjunto de baseado em spam

O conjunto de dados [baseado em spam](https://archive.ics.uci.edu/ml/datasets/spambase) é um conjunto relativamente pequeno de data que contém 4.601 exemplos. O conjunto de datas é um tamanho conveniente para demonstrar alguns dos principais recursos do DSVM, pois ele mantém os requisitos de recursos modestos.

> [!NOTE]
> Este tutorial foi criado com o uso de um DSVM Linux de um tamanho D2 v2 (edição CentOS). Você pode usar um DSVM esse tamanho para concluir os procedimentos que são demonstrados neste passo a passos.

Se você precisar de mais espaço de armazenamento, poderá criar discos adicionais e anexá-los ao seu DSVM. Os discos usam o armazenamento persistente do Azure, de modo que seus dados serão preservados mesmo se o servidor for reprovisionado devido ao redimensionamento ou desligamento. Para adicionar um disco e anexá-lo ao seu DSVM, conclua as etapas em [Adicionar um disco a uma VM do Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). As etapas para adicionar um disco usam o CLI do Azure, que já está instalado no DSVM. Você pode concluir as etapas inteiramente do próprio DSVM. Outra opção para aumentar o armazenamento é usar [os arquivos do Azure](../../storage/files/storage-how-to-use-files-linux.md).

Para baixar os dados, abra uma janela de terminal e, em seguida, execute este comando:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

O arquivo baixado não tem uma linha de cabeçalho. Vamos criar outro arquivo que tenha um cabeçalho. Execute este comando para criar um arquivo com os cabeçalhos apropriados:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Em seguida, concatene os dois arquivos juntos:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

O conjunto de os vários tipos de estatísticas para cada email:

* Colunas como o **word\_freq\__Word_**  indicam a porcentagem de palavras no email que correspondem à *palavra*. Por exemplo, se o **word\_freq\_Make** for **1**, então 1% de todas as palavras no email foram *feitas*.
* Colunas como **char\_freq\__Char_**  indicam a porcentagem de todos os caracteres no email que são *Char*.
* o comprimento da\_de tempo de **\_de capital\_mais longo** é o comprimento mais longo de uma sequência de letras maiúsculas.
* o **comprimento da\_de tempo de\_de capital\_média** é o comprimento médio de todas as sequências de letras maiúsculas.
* o **tamanho da\_de tempo de\_de capital\_total** é o comprimento total de todas as sequências de letras maiúsculas.
* **spam** indica se o email foi considerado spam ou não (1 = spam, 0 = não spam).

## <a name="explore-the-dataset-by-using-r-open"></a>Explorar o conjunto de um usando o R Open

Vamos examinar os dados e fazer um aprendizado de máquina básico usando R. O DSVM vem com o [Microsoft R Open](https://mran.revolutionanalytics.com/open/) pré-instalado. As bibliotecas de matemática multi-threaded na versão pré-instalada do R oferecem melhor desempenho do que as versões de thread único. O R Open também fornece reprodução por meio de um instantâneo do repositório de pacotes CRAN.

Para obter cópias dos exemplos de código que são usados neste passo a passos, use o Git para clonar o repositório Azure-Machine-Learning-data-ciência. O Git é pré-instalado no DSVM. Na linha de comando do git, execute:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Abra uma janela do terminal e inicie uma nova sessão do R no console interativo do R. Você também pode usar o RStudio, que é pré-instalado no DSVM.

Para importar os dados e configurar o ambiente:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Para ver as estatísticas de resumo sobre cada coluna:

    summary(data)

Para uma exibição diferente dos dados:

    str(data)

Essa exibição mostra o tipo de cada variável e os primeiros valores no conjunto de texto.

A coluna de **spam** foi lida como um inteiro, mas ela é, na verdade, uma variável categórica (ou fator). Para definir seu tipo:

    data$spam <- as.factor(data$spam)

Para fazer alguma análise exploratório, use o pacote [ggplot2](https://ggplot2.tidyverse.org/) , uma biblioteca de gráficos popular para o R pré-instalado no DSVM. Com base nos dados de resumo exibidos anteriormente, temos estatísticas resumidas sobre a frequência do caractere de ponto de exclamação. Vamos plotar essas frequências executando os seguintes comandos:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Como a barra zero está distorcendo a plotagem, vamos eliminá-la:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Há uma densidade não trivial acima de 1 que parece interessante. Vamos examinar apenas os dados:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Em seguida, divida-o por spam versus Ham:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Esses exemplos devem ajudá-lo a fazer gráficos semelhantes e explorar dados nas outras colunas.

## <a name="train-and-test-a-machine-learning-model"></a>Treinar e testar um modelo de aprendizado de máquina

Vamos treinar alguns modelos de aprendizado de máquina para classificar os emails no conjunto de informações como contendo spam ou Ham. Nesta seção, treinamos um modelo de árvore de decisão e um modelo de floresta aleatória. Em seguida, testamos a exatidão das previsões.

> [!NOTE]
> O pacote *rpart* (árvores recursivas de particionamento e regressão) usado no código a seguir já está instalado no DSVM.

Primeiro, vamos dividir o conjunto de um em conjuntos de treinamento e conjuntos de testes:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Em seguida, crie uma árvore de decisão para classificar os emails:

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Este é o resultado:

![Um diagrama da árvore de decisão que é criada](./media/linux-dsvm-walkthrough/decision-tree.png)

Para determinar como ele é executado no conjunto de treinamento, use o seguinte código:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Para determinar como ele é executado no conjunto de teste:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Vamos experimentar também um modelo de floresta aleatório. As florestas aleatórias treinam uma infinidade de árvores de decisão e geram uma classe que é o modo das classificações de todas as árvores de decisão individuais. Elas fornecem uma abordagem de aprendizado de máquina mais potente, pois elas estão corretas para a tendência de um modelo de árvore de decisão para sobreajustar um conjunto de informações de treinamento.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio-classic"></a>Implantar um modelo para Azure Machine Learning Studio (clássico)

[Azure Machine Learning Studio (clássico)](https://studio.azureml.net/) é um serviço de nuvem que torna mais fácil criar e implantar modelos de análise preditiva. Um recurso interessante da versão clássica do Azure Machine Learning Studio é sua capacidade de publicar qualquer função do R como um serviço Web. O pacote R Azure Machine Learning Studio facilita a implantação, diretamente da sessão do R no DSVM.

Para implantar o código da árvore de decisão da seção anterior, entre no Azure Machine Learning Studio (clássico). Você precisa da ID do espaço de trabalho e de um token de autorização para entrar. Para localizar esses valores e inicializar as variáveis de Azure Machine Learning com elas, conclua estas etapas:

1. No menu à esquerda, selecione **configurações**. Observe o valor da **ID do espaço de trabalho**.

   ![A ID do espaço de trabalho Azure Machine Learning Studio](./media/linux-dsvm-walkthrough/workspace-id.png)

1. Selecione a guia **tokens de autorização** . Observe o valor do **token de autorização primário**.

   ![O token de autorização primária Azure Machine Learning Studio](./media/linux-dsvm-walkthrough/workspace-token.png)
1. Carregue o pacote do **AzureML** e defina os valores das variáveis com o token e a ID do espaço de trabalho em sua sessão do R no DSVM:

        if(!require("AzureML")) install.packages("AzureML")
        require(AzureML)
        wsAuth = "<authorization-token>"
        wsID = "<workspace-id>"

1. Vamos simplificar o modelo para facilitar a implementação dessa demonstração. Escolha as três variáveis na árvore de decisão mais próxima da raiz e crie uma nova árvore usando apenas essas três variáveis:

        colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
        smallTrainSet <- trainSet[, colNames]
        smallTestSet <- testSet[, colNames]
        model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

1. Precisamos de uma função de previsão que usa os recursos como uma entrada e retorna os valores previstos:

        predictSpam <- function(newdata) {
        predictDF <- predict(model.rpart, newdata = newdata)
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
        }


1. Publique a função **predictSpam** em AzureML usando a função **publishWebService** :

        spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)

1. Essa função usa a função **predictSpam** , cria um serviço Web chamado **spamWebService** que tem entradas e saídas definidas e, em seguida, retorna informações sobre o novo ponto de extremidade.

    Use este comando para exibir detalhes do serviço Web publicado mais recente, incluindo seu ponto de extremidade de API e chaves de acesso:

        s<-tail(services(ws, name = "spamWebService"), 1)
        ep <- endpoints(ws,s)
        ep

1. Para experimentar nas primeiras 10 linhas do conjunto de teste:

        consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Tutoriais de aprendizado aprofundado e orientações

Além dos exemplos baseados em estrutura, um conjunto de orientações abrangentes também é fornecido. Essas orientações ajudam a recomeçar seu desenvolvimento de aplicativos de aprendizado profundo em domínios como reconhecimento de imagem e texto/linguagem.

- [Executando redes neurais em diferentes estruturas](https://github.com/ilkarman/DeepLearningFrameworks): uma explicação abrangente que mostra como migrar o código de uma estrutura para outra. Ele também demonstra como comparar o desempenho do modelo e do tempo de execução entre as estruturas. 

- [Um guia de instruções para a criação de uma solução de ponta a ponta para detectar produtos em imagens](https://github.com/Azure/cortana-intelligence-product-detection-from-images): A detecção de imagem é uma técnica que pode localizar e classificar objetos dentro de imagens. A tecnologia tem o potencial de trazer enormes recompensas em muitos domínios de negócios da vida real. Por exemplo, os varejistas podem usar essa técnica para determinar qual produto um cliente pegou da prateleira. Essa informação, por sua vez, ajuda a armazenar gerenciar o inventário de produtos. 

- [Aprendizado profundo de áudio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Este tutorial mostra como treinar um modelo de aprendizado profundo para detecção de eventos de áudio no conjunto de informações de [sons urbanas](https://urbansounddataset.weebly.com/). O tutorial fornece uma visão geral de como trabalhar com dados de áudio.

- [Classificação de documentos de texto](https://github.com/anargyri/lstm_han): Este tutorial demonstra como criar e treinar duas arquiteturas de rede neural diferentes: rede de atenção hierárquica e memória de longo prazo (LSTM). Essas redes neurais usam a API Keras para aprendizado profundo para classificar documentos de texto. O Keras é um front-end para três das estruturas mais populares de aprendizado profundo: Microsoft Cognitive Toolkit, TensorFlow e Theano.

## <a name="other-tools"></a>Outras ferramentas

As seções restantes mostram como usar algumas das ferramentas instaladas no DSVM do Linux. Discutimos essas ferramentas:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL e SQuirreL SQL
* SQL Server data warehouse

### <a name="xgboost"></a>XGBoost

O [XGBoost](https://xgboost.readthedocs.org/en/latest/) fornece uma implementação de árvore aumentada rápida e precisa.

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

XGBoost também pode chamar do Python ou de uma linha de comando.

### <a name="python"></a>Python

Para o desenvolvimento em Python, as distribuições Anaconda Python 3,5 e 2,7 estão instaladas no DSVM.

> [!NOTE]
> A distribuição Anaconda inclui [Conda](https://conda.pydata.org/docs/index.html). Você pode usar o Conda para criar ambientes de Python personalizados que têm diferentes versões ou pacotes instalados neles.

Vamos ler alguns dos conjuntos de baseado em spam e classificar os emails com as máquinas de vetor de suporte no Scikit-Learn:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para fazer previsões:

    clf.predict(X.ix[0:20, :])

Para demonstrar como publicar um ponto de extremidade Azure Machine Learning, vamos criar um modelo mais básico. Usaremos as três variáveis que usamos quando publicamos o modelo de R anteriormente:

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para publicar o modelo para Azure Machine Learning:

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
> Essa opção está disponível somente para Python 2,7. Ainda não há suporte para o Python 3,5. Para executar, use **/Anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub

A distribuição Anaconda no DSVM vem com um Jupyter Notebook, um ambiente de plataforma cruzada para compartilhar código e análise Python, R ou Julia. O Jupyter Notebook é acessado por meio de JupyterHub. Você entra usando seu nome de usuário e senha do Linux local em https://\<DSVM nome DNS ou endereço IP\>: 8000/. Todos os arquivos de configuração para JupyterHub são encontrados em/etc/jupyterhub.

> [!NOTE]
> Para usar o Gerenciador de pacotes do Python (por meio do comando `pip`) de um Jupyter Notebook no kernel atual, use este comando na célula de código:
>
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Para usar o instalador Conda (por meio do comando `conda`) de um Jupyter Notebook no kernel atual, use este comando em uma célula de código:
>
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Vários blocos de anotações de exemplo já estão instalados no DSVM:

* Notebooks Python de exemplo:
  * [IntroToJupyterPython. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Exemplo de bloco de anotações R:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> A linguagem Julia também está disponível na linha de comando no DSVM do Linux.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A*njunto *t*ool *t*o *L*ganhe *E*Earn) é uma ferramenta gráfica de R para Data Mining. O Rattle tem uma interface intuitiva que torna mais fácil carregar, explorar e transformar dados e criar e avaliar modelos. [Rattle: uma GUI de mineração de dados para R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) fornece instruções que demonstram os recursos do Rattle.

Instale e inicie o Rattle executando estes comandos:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Você não precisa instalar o Rattle no DSVM. No entanto, você pode ser solicitado a instalar pacotes adicionais quando o Rattle for aberto.

O Rattle usa uma interface baseada em guias. A maioria das guias corresponde às etapas no [processo de ciência de dados de equipe](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), como carregar dados ou explorar dados. O processo de ciência de dados flui da esquerda para a direita pelas guias. A última guia contém um log dos comandos do R que foram executados pelo Rattle.

Para carregar e configurar o conjunto de os:

1. Para carregar o arquivo, selecione a guia **dados** .
1. Escolha o seletor ao lado de **filename**e, em seguida, selecione **spambaseHeaders. Data**.
1. Para carregar o arquivo. Selecione **executar**. Você deve ver um resumo de cada coluna, incluindo seu tipo de dados identificado; Se é uma entrada, um destino ou outro tipo de variável; e o número de valores exclusivos.
1. O Rattle identificou corretamente a coluna de **spam** como o destino. Selecione a coluna **spam** e defina o tipo de **dados de destino** como **categórico**.

Para explorar os dados:

1. Selecione a guia **explorar** .
1. Para ver informações sobre os tipos de variáveis e algumas estatísticas de resumo, selecione **resumo** > **executar**.
1. Para exibir outros tipos de estatísticas sobre cada variável, selecione outras opções, como **descrever** ou **noções básicas**.

Você também pode usar a guia **explorar** para gerar plotagens criteriosas. Para plotar um histograma dos dados:

1. Selecione **distribuições**.
1. Para **word_freq_remove** e **Word_freq_you**, selecione **histograma**.
1. Selecione **Executar**. Você deve ver ambas as plotagens de densidade em uma única janela do Graph, onde fica claro que a palavra _que_ aparece com muito mais frequência em emails do que _remover_.

As plotagens de **correlação** também são interessantes. Para criar uma plotagem:

1. Para **tipo**, selecione **correlação**.
1. Selecione **Executar**.
1. O Rattle avisa que ele recomenda um máximo de 40 variáveis. Selecione **Sim** para exibir o gráfico.

Há algumas correlações interessantes que surgem: a _tecnologia_ está altamente correlacionada à _HP_ e aos _laboratórios_, por exemplo. Ele também está altamente correlacionado a _650_ porque o código de área dos doadores do conjunto de um é 650.

Os valores numéricos para as correlações entre palavras estão disponíveis na janela **explorar** . É interessante observar, por exemplo, que a _tecnologia_ está correlacionada negativamente ao _seu_ e ao _dinheiro_.

O Rattle pode transformar o conjunto de um para lidar com alguns problemas comuns. Por exemplo, ele pode redimensionar recursos, imputar valores ausentes, lidar com exceções e remover variáveis ou observações com dados ausentes. Rattle também pode identificar regras de associação entre observações e variáveis. Essas guias não são abordadas neste passo a passos introdutório.

Rattle também pode executar a análise de cluster. Vamos excluir alguns recursos para facilitar a leitura da saída. Na guia **dados** , selecione **ignorar** ao lado de cada uma das variáveis, exceto estes 10 itens:

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

Retorne à guia **cluster** . Selecione **KMeans**e, em seguida, defina **número de clusters** como **4**. Selecione **Executar**. Os resultados são exibidos na janela saída. Um cluster tem alta frequência de _George_ e _HP_e, provavelmente, é um email comercial legítimo.

Para criar um modelo básico de aprendizado de máquina de árvore de decisão:

1. Selecione a guia **modelo** ,
1. Para o **tipo**, selecione **árvore**.
1. Selecione **executar** para exibir a árvore no formato de texto na janela saída.
1. Selecione o botão **desenhar** para exibir uma versão gráfica. A árvore de decisão é semelhante à árvore obtida anteriormente usando rpart.

Um recurso útil do Rattle é sua capacidade de executar vários métodos de aprendizado de máquina e avaliá-los rapidamente. Aqui estão as etapas:

1. Para **tipo**, selecione **tudo**.
1. Selecione **Executar**.
1. Quando o Rattle terminar de ser executado, você poderá selecionar qualquer valor de **tipo** , como **SVM**, e exibir os resultados.
1. Você também pode comparar o desempenho dos modelos no conjunto de validação usando a guia **avaliar** . Por exemplo, a seleção **matriz de erros** mostra a matriz de confusão, o erro geral e o erro de classe média para cada modelo no conjunto de validação. Você também pode plotar curvas ROC, executar análise de sensibilidade e fazer outros tipos de avaliações de modelo.

Quando terminar de criar modelos, selecione a guia **log** para exibir o código R que foi executado pelo Rattle durante a sessão. Você pode selecionar o botão **Exportar** para salvá-lo.

> [!NOTE]
> A versão atual do Rattle contém um bug. Para modificar o script ou usá-lo para repetir as etapas mais tarde, você deve inserir um caractere de **#** na frente de *Exportar este log...* no texto do log.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL e SQuirreL SQL

O DSVM vem com o PostgreSQL instalado. PostgreSQL é um banco de dados relacional sofisticado e de código aberto. Esta seção mostra como carregar o conjunto de baseado em spam no PostgreSQL e, em seguida, consultá-lo.

Antes de poder carregar os dados, você deve permitir a autenticação de senha do localhost. Numa linha de comandos, execute:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Próximo à parte inferior do arquivo de configuração há várias linhas que detalham as conexões permitidas:

    # "local" is only for Unix domain socket connections:
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Altere a linha de **conexões locais IPv4** para usar **MD5** em vez de **ident**, para que possamos fazer logon usando um nome de usuário e senha:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Em seguida, reinicie o serviço PostgreSQL:

    sudo systemctl restart postgresql

Para iniciar o *psql* (um terminal interativo para PostgreSQL) como o usuário Postgres interno, execute este comando:

    sudo -u postgres psql

Crie uma nova conta de usuário usando o nome de usuários da conta do Linux que você usou para fazer logon. Criar uma senha:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Faça logon no psql:

    psql

Importe os dados para um novo banco de dado:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Agora, vamos explorar os dados e executar algumas consultas usando o SQuirreL SQL, uma ferramenta gráfica que pode ser usada para interagir com bancos de dados por meio de um driver JDBC.

Para começar, no menu **aplicativos** , abra SQuirreL SQL. Para configurar o driver:

1. Selecione Drivers de **exibição**do **Windows** > .
1. Clique com o botão direito do mouse em **PostgreSQL** e selecione **Modificar driver**.
1. Selecione **caminho de classe Extra** > **Adicionar**.
1. Para **nome do arquivo**, insira **/usr/share/java/jdbcdrivers/PostgreSQL-9.4.1208.jre6.jar**.
1. Selecione **Open** (Abrir).
1. Selecione **listar drivers**. Para **nome da classe**, selecione **org. PostgreSQL. Driver**e, em seguida, selecione **OK**.

Para configurar a conexão com o servidor local:

1. Selecione aliases de **exibição** do **Windows** > .
1. Selecione o botão **+** para criar um novo alias. Para o novo nome de alias, insira **banco de dados de spam**. 
1. Para **Driver**, selecione **PostgreSQL**.
1. Defina a URL para **JDBC: PostgreSQL://localhost/spam**.
1. Insira seu nome de usuário e senha.
1. Selecione **OK**.
1. Para abrir a janela de **conexão** , clique duas vezes no alias do **banco de dados de spam** .
1. Selecione **Ligar**.

Para executar algumas consultas:

1. Selecione a guia **SQL** .
1. Na caixa de consulta na parte superior da guia **SQL** , insira uma consulta básica, como `SELECT * from data;`.
1. Pressione Ctrl + Enter para executar a consulta. Por padrão, SQuirreL SQL retorna as primeiras 100 linhas de sua consulta.

Há muitas outras consultas que você pode executar para explorar esses dados. Por exemplo, como a frequência *da palavra é* diferente entre spam e Ham?

    SELECT avg(word_freq_make), spam from data group by spam;

Ou, quais são as características do email que frequentemente contêm *3D*?

    SELECT * from data order by word_freq_3d desc;

A maioria dos emails que têm uma grande ocorrência de *3D* aparentemente é spam. Essas informações podem ser úteis para criar um modelo de previsão para classificar emails.

Se você quiser fazer aprendizado de máquina usando dados armazenados em um banco de dados PostgreSQL, considere o uso de [MADlib](https://madlib.incubator.apache.org/).

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

O SQL Data Warehouse do Azure é um banco de dados baseado em nuvem e escalável que pode processar volumes maciços e não relacionais. Para obter mais informações, consulte [o que é o Azure SQL data warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Para se conectar ao data warehouse e criar a tabela, execute o seguinte comando em um prompt de comando:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

No prompt do sqlcmd, execute este comando:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Copie os dados usando bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> O arquivo baixado contém terminações de linha no estilo do Windows. A ferramenta bcp espera as terminações de linha no estilo UNIX. Use o sinalizador-r para informar o bcp.

Em seguida, consulte usando sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Você também pode consultar usando SQuirreL SQL. Siga as etapas semelhantes ao PostgreSQL usando o driver JDBC SQL Server. O driver JDBC está na pasta/usr/share/java/jdbcdrivers/sqljdbc42.jar

## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral dos artigos que orientam você pelas tarefas que compõem o processo de ciência de dados no Azure, consulte [processo de ciência de dados de equipe](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Para obter uma descrição de orientações de ponta a ponta que demonstram as etapas no processo de ciência de dados de equipe para cenários específicos, consulte [passo a](../team-data-science-process/walkthroughs.md)passos do processo de ciência de dados de equipe. Os passo a passos também ilustram como combinar serviços e ferramentas de nuvem e locais em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente.
