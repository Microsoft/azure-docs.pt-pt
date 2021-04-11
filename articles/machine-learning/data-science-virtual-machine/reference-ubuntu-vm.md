---
title: 'Referência: Máquina Virtual Ubuntu Data Science'
titleSuffix: Azure Data Science Virtual Machine
description: Detalhes sobre ferramentas incluídas na Máquina Virtual de Ciência de Dados Ubuntu
author: gvashishtha
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: e9a55f72718d6ed5991f3d0f16323409bb0f699f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "101661074"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Referência: Máquina Virtual Ubuntu (Linux) Data Science

Veja abaixo uma lista de ferramentas disponíveis na sua Máquina Virtual Ubuntu Data Science. 

## <a name="deep-learning-libraries"></a>Bibliotecas de aprendizagem profunda

### <a name="cntk"></a>CNTK

O Microsoft Cognitive Toolkit é um kit de ferramentas de aprendizagem profunda de código aberto. As ligações python estão disponíveis nos ambientes raiz e py35 Conda. Também tem uma ferramenta de linha de comando (CNTK) que já está no caminho.

Os cadernos da Sample Python estão disponíveis no JupyterHub. Para executar uma amostra básica na linha de comando, executar os seguintes comandos na concha:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Para mais informações, consulte a secção CNTK do [GitHub](https://github.com/Microsoft/CNTK) e a [wiki CNTK.](https://github.com/Microsoft/CNTK/wiki)

### <a name="caffe"></a>Caffe

Caffe é um quadro de aprendizagem profunda do Centro de Visão e Aprendizagem de Berkeley. Está disponível em /opt/caffe. Pode encontrar exemplos em /opt/caffe/exemplos.

### <a name="caffe2"></a>Caffe2

Caffe2 é um quadro de aprendizagem profunda do Facebook que é construído no Caffe. Está disponível em Python 2.7 no ambiente de raiz conda. Para o ativar, executar o seguinte comando a partir da concha:

```bash
source /anaconda/bin/activate root
```

Alguns cadernos de exemplo estão disponíveis no JupyterHub.

### <a name="h2o"></a>H2O

H2O é uma plataforma de aprendizagem automática e análise preditiva rápida, em memória, distribuída. Um pacote Python é instalado tanto nos ambientes raiz como py35 Anaconda. Também está instalado um pacote R. 

Para abrir o H2O a partir da linha de comando, corra `java -jar /dsvm/tools/h2o/current/h2o.jar` . Existem várias [opções de linha de comando](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) que talvez queira configurar. Pode aceder à UI web Flow navegando `http://localhost:54321` para começar. Os cadernos de amostras também estão disponíveis no JupyterHub.

### <a name="keras"></a>Keras

Keras é uma rede neural de alto nível API em Python. Pode funcionar em cima de TensorFlow, Microsoft Cognitive Toolkit ou Theano. Está disponível nos ambientes raiz e py35 Python.

### <a name="mxnet"></a>MXNet

O MXNet é um quadro de aprendizagem profunda projetado tanto para a eficiência como para a flexibilidade. Tem encadernações R e Python incluídas no DSVM. Os cadernos de amostras estão incluídos no JupyterHub, e o código de amostra está disponível em /dsvm/samples/mxnet.

### <a name="nvidia-digits"></a>DÍGITOS NVIDIA

O Sistema de Formação GPU de Aprendizagem Profunda da NVIDIA, conhecido como DIGITS, é um sistema para simplificar tarefas comuns de aprendizagem profunda. Estas tarefas incluem a gestão de dados, a conceção e formação de redes neuronais em sistemas GPU e a monitorização do desempenho em tempo real com visualização avançada.

O DIGITS está disponível como um serviço chamado *dígitos*. Inicie o serviço e navegue `http://localhost:5000` para começar.

O DIGITS também é instalado como um módulo Python no ambiente raiz conda.

### <a name="tensorflow"></a>TensorFlow

TensorFlow é a biblioteca de aprendizagem profunda do Google. É uma biblioteca de software de código aberto para computação numérica usando gráficos de fluxo de dados. O TensorFlow está disponível no ambiente py35 Python, e alguns cadernos de amostras estão incluídos no JupyterHub.

### <a name="theano"></a>Theano

Theano é uma biblioteca python para uma computação numérica eficiente. Está disponível nos ambientes raiz e py35 Python. 

### <a name="torch"></a>Torch

Torch é um quadro de computação científica com um amplo suporte para algoritmos de aprendizagem automática. Está disponível em /dsvm/tools/tocha, e a **th** interactive session e o gestor de pacotes LuaRocks estão disponíveis na linha de comando. Exemplos estão disponíveis em /dsvm/samples/torch.

PyTorch também está disponível no ambiente raiz Anaconda. Os exemplos estão em /dsvm/samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

R é uma das línguas mais populares para análise de dados e machine learning. Se quiser utilizar R para a sua análise, o VM tem o Microsoft Machine Learning Server com a Microsoft R Open e a Biblioteca De Kernel de Matemática. A Biblioteca De Kernel de Matemática otimiza as operações matemáticas comuns em algoritmos analíticos. O Microsoft R Open é 100% compatível com o CRAN R, e qualquer uma das bibliotecas R publicadas na CRAN pode ser instalada no Microsoft R Open. 

O Machine Learning Server dá-lhe escala e operacionalização de modelos R em serviços web. Pode editar os seus programas R num dos editores predefinidos, como RStudio, vi ou Emacs. Se preferir usar o editor da Emacs, foi pré-instalado. O pacote Emacs ESS (Emacs Speaks Statistics) simplifica o trabalho com ficheiros R dentro do editor da Emacs.

Para abrir a consola R, introduz-se **R** na concha. Este comando leva-o a um ambiente interativo. Para desenvolver o seu programa R, normalmente usa um editor como Emacs ou vi, e depois execute os scripts dentro de R. Com o RStudio, você tem um IDE gráfico completo para desenvolver o seu programa R.

Há também um script R para você instalar os [pacotes Top 20 R,](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) se quiser. Pode executar este script depois de estar na interface interativa R. Como mencionado anteriormente, pode abrir essa interface introduzindo **R** na concha.  

## <a name="python"></a>Python

Anaconda Python está instalada com ambientes Python 2.7 e 3.5. O ambiente 2.7 é chamado _raiz_, e o ambiente 3.5 é chamado _py35_. Esta distribuição contém a base Python juntamente com cerca de 300 dos pacotes de matemática, engenharia e análise de dados mais populares.

O ambiente py35 é o padrão. Para ativar o ambiente raiz (2.7), utilize este comando:

```bash
source activate root
```

Para voltar a ativar o ambiente py35, utilize este comando:

```bash
source activate py35
```

Para invocar uma sessão interativa python, **introduza a pitão** na concha. 

Instale bibliotecas Python adicionais utilizando Conda ou pip. Para pip, ative primeiro o ambiente correto se não quiser o padrão:

```bash
source activate root
pip install <package>
```

Ou especifique o caminho completo para pip:

```bash
/anaconda/bin/pip install <package>
```

Para a Conda, deve sempre especificar o nome do ambiente (py35 ou raiz):

```bash
conda install <package> -n py35
```

Se estiver numa interface gráfica ou tiver o reencaminhamento X11, pode introduzir **o pycharm** para abrir o PyCharm Python IDE. Pode utilizar os editores de texto predefinidos. Além disso, você pode usar Spyder, um Python IDE que está agregado com as distribuições de Anaconda Python. Spyder precisa de um ambiente de trabalho gráfico ou de encaminhamento X11. O ambiente de trabalho gráfico tem um atalho para o Spyder.

## <a name="jupyter-notebook"></a>Caderno Jupyter

A distribuição da Anaconda também vem com um caderno Jupyter, um ambiente para partilhar código e análise. O caderno Jupyter é acedido através do JupyterHub. Faça sedundo usando o seu nome de utilizador e senha linux local.

O servidor de caderno Jupyter foi pré-configurado com núcleos Python 2, Python 3 e R. Utilize o ícone de ambiente de trabalho **Jupyter Notebook** para abrir o navegador e aceder ao servidor de portátil. Se estiver no VM via SSH ou no cliente X2Go, também pode aceder ao servidor de portátil Jupyter em `https://localhost:8000/` .

> [!NOTE]
> Continue se receber avisos de certificado.

Pode aceder ao servidor de portátil Jupyter a partir de qualquer anfitrião. Introduza **https:// \<VM DNS name or IP address\> :8000/**.

> [!NOTE]
> A porta 8000 é aberta na firewall por defeito quando o VM é a provisionado. 

Embalamos cadernos de amostras, um em Python e outro em R. Pode ver o link para as amostras na página inicial do portátil depois de autenticar no caderno Jupyter utilizando o seu nome de utilizador e senha linux local. Pode criar um novo caderno selecionando **New** e, em seguida, selecionando o núcleo linguístico apropriado. Se não vir o botão **Novo,** selecione o ícone **Jupyter** na parte superior esquerda para ir à página inicial do servidor de portátil.

## <a name="apache-spark-standalone"></a>Apache Spark autónomo

Um caso autónomo de Apache Spark está pré-instalado no Linux DSVM para ajudá-lo a desenvolver aplicações Spark localmente antes de testar e implantá-las em grandes clusters. 

Você pode executar programas PySpark através do núcleo jupyter. Quando abrir o Jupyter, selecione o botão **Novo** e deverá ver uma lista de núcleos disponíveis. **Spark - Python** é o núcleo PySpark que permite construir aplicações Spark usando a língua Python. Também pode usar um Python IDE como PyCharm ou Spyder para construir o seu programa Spark. 

Neste caso autónomo, a pilha spark funciona dentro do programa de clientes de chamada. Esta funcionalidade torna mais rápido e mais fácil resolver problemas, em comparação com o desenvolvimento de um cluster Spark.

Jupyter fornece uma amostra de caderno PySpark. Você pode encontrá-lo no diretório SparkML sob o diretório de casa da Jupyter ($HOME/cadernos/SparkML/pySpark). 

Se estiver a programar em R para spark, pode utilizar o Microsoft Machine Learning Server, o SparkR ou o sparklyr. 

Antes de executar um contexto de Faísca no Microsoft Machine Learning Server, precisa de fazer um passo de configuração único para ativar uma instância local de Hadoop HDFS e Yarn. Por predefinição, os serviços Hadoop são instalados, mas desativados no DSVM. Para o ativar, é necessário executar os seguintes comandos como raiz da primeira vez:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Pode parar os serviços relacionados com Hadoop quando não precisa deles ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` correndo.

O diretório /dsvm/samples/MRS fornece uma amostra que demonstra como desenvolver e testar o Microsoft Machine Learning Server num contexto remoto de Faísca (a instância de faísca autónoma no DSVM).

## <a name="ides-and-editors"></a>IDEs e editores

Você tem uma escolha de vários editores de código, incluindo vi/Vim, Emacs, PyCharm, RStudio e IntelliJ. 

PyCharm, RStudio e IntelliJ são editores gráficos. Para usá-los, tem de ser inscrito num ambiente de trabalho gráfico. Abre-as utilizando atalhos do menu de secretária e aplicações.

Vim e Emacs são editores baseados em texto. No Emacs, o pacote de complemento ESS facilita o trabalho com r dentro do editor da Emacs. Pode encontrar mais informações no site da [ESS.](https://ess.r-project.org/)

O LaTex é instalado através do pacote texlive, juntamente com um pacote de add-on Emacs chamado [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Este pacote simplifica a autoria dos seus documentos LaTex dentro do Emacs.  

## <a name="databases"></a>Bases de Dados

### <a name="graphical-sql-client"></a>Cliente gráfica sql

O SQuirrel SQL, um cliente graphical SQL, pode ligar-se a várias bases de dados (como o Microsoft SQL Server e o MySQL) e executar consultas SQL. Pode executar SQuirrel SQL a partir de uma sessão de desktop gráfica (através do cliente X2Go, por exemplo) utilizando um ícone de ambiente de trabalho. Ou pode executar o cliente usando o seguinte comando na concha:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Antes da primeira utilização, configurar os seus nomes de motoristas e pseudónimos de base de dados. Os condutores JDBC estão localizados em /usr/share/java/jdbcdrivers.

Para mais informações, consulte [SQuirrel SQL.](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Ferramentas de linha de comando para aceder ao Microsoft SQL Server

O pacote de controlador ODBC para SQL Server também vem com duas ferramentas de linha de comando:

- **bcp**: A ferramenta bcp copia dados entre uma instância do Microsoft SQL Server e um ficheiro de dados num formato especificado pelo utilizador. Pode utilizar a ferramenta bcp para importar um grande número de novas linhas nas tabelas do SQL Server ou para exportar dados de tabelas para ficheiros de dados. Para importar dados para uma tabela, deve utilizar um ficheiro de formato criado para essa tabela. Ou, deve entender a estrutura da tabela e os tipos de dados que são válidos para as suas colunas.

  Para mais informações, consulte ['Ligar' com o BCP.](/sql/connect/odbc/linux-mac/connecting-with-bcp)

- **sqlcmd**: Pode introduzir declarações Transact-SQL utilizando a ferramenta sqlcmd. Também pode introduzir procedimentos do sistema e ficheiros de script na solicitação de comando. Esta ferramenta utiliza ODBC para executar lotes Transact-SQL.

  Para obter mais informações, consulte [Ligar com sqlcmd.](/sql/connect/odbc/linux-mac/connecting-with-sqlcmd)

  > [!NOTE]
  > Existem algumas diferenças nesta ferramenta entre as plataformas Linux e Windows. Consulte a documentação para mais detalhes.

### <a name="database-access-libraries"></a>Bibliotecas de acesso a bases de dados

As bibliotecas estão disponíveis em R e Python para acesso à base de dados:

* Em R, pode utilizar o pacote RODBC ou o pacote dplyr para consultar ou executar declarações SQL no servidor de base de dados.
* Em Python, a biblioteca pyodbc fornece acesso de base de dados com ODBC como a camada subjacente.  

## <a name="azure-tools"></a>Ferramentas do Azure

As seguintes ferramentas Azure são instaladas no VM:

* **Azure CLI**: Pode utilizar a interface de linha de comando em Azure para criar e gerir recursos Azure através de comandos de concha. Para abrir as ferramentas Azure, **insira a ajuda azul**. Para mais informações, consulte a [página de documentação do Azure CLI](/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: Azure Storage Explorer é uma ferramenta gráfica que pode utilizar para navegar pelos objetos que armazenou na sua conta de armazenamento Azure, e para carregar e transferir dados de e para as bolhas Azure. Pode aceder ao Storage Explorer a partir do ícone do atalho do ambiente de trabalho. Também pode abri-lo a partir de uma solicitação de concha, introduzindo **StorageExplorer**. Você deve ser contratado por um cliente X2Go, ou ter X11 reencaminhamento.
* **Bibliotecas azul**: Seguem-se algumas das bibliotecas pré-instaladas.
  
  * **Python**: As bibliotecas relacionadas com o Azure em Python são *azul,* *azureml,* *pydocumentdb,* e *pyodbc.* Com as três primeiras bibliotecas, pode aceder aos serviços de armazenamento Azure, Azure Machine Learning e Azure Cosmos DB (uma base de dados NoSQL em Azure). A quarta biblioteca, pyodbc (juntamente com o controlador Microsoft ODBC para o SQL Server), permite o acesso ao SQL Server, Azure SQL Database e Azure Synapse Analytics de Python utilizando uma interface ODBC. Insira a **lista de pips** para ver todas as bibliotecas listadas. Certifique-se de executar este comando em ambos os ambientes Python 2.7 e 3.5.
  * **R**: As bibliotecas relacionadas com o Azure em R são AzureML e RODBC.
  * **Java**: A lista de bibliotecas Azure Java pode ser encontrada no diretório /dsvm/sdk/AzureSDKJava no VM. As principais bibliotecas são os pilotos de armazenamento e gestão Azure, Azure Cosmos DB e JDBC para o SQL Server.  

Pode aceder ao [portal Azure](https://portal.azure.com) a partir do navegador Firefox pré-instalado. No portal Azure, pode criar, gerir e monitorizar os recursos do Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço de nuvem totalmente gerido que lhe permite construir, implementar e partilhar soluções de análise preditivas. Pode construir as suas experiências e modelos no estúdio Azure Machine Learning (pré-visualização). Pode acessá-lo a partir de um navegador web na Máquina Virtual de Ciência de Dados, visitando o [Microsoft Azure Machine Learning.](https://ml.azure.com)

Depois de iniciar seduca no estúdio Azure Machine Learning, pode usar uma tela de experimentação para construir um fluxo lógico para os algoritmos de aprendizagem automática. Você também tem acesso a um caderno Jupyter que está hospedado no Azure Machine Learning e pode trabalhar perfeitamente com as experiências no estúdio Azure Machine Learning. 

Operacionalize os modelos de machine learning que construiu envolvendo-os numa interface de serviço web. A operacionalização de modelos de machine learning permite que os clientes escritos em qualquer língua invoquem previsões desses modelos. Para mais informações, consulte a [documentação de Machine Learning.](https://azure.microsoft.com/documentation/services/machine-learning/)

Também pode construir os seus modelos em R ou Python no VM e, em seguida, implantá-los em produção no Azure Machine Learning. Instalámos bibliotecas em R **(AzureML)** e Python **(azureml)** para permitir esta funcionalidade.

> [!NOTE]
> Estas instruções foram escritas para a versão Windows da Máquina Virtual de Ciência de Dados. Mas a informação fornecida sobre a implementação de modelos para a Azure Machine Learning é aplicável ao Linux VM.

## <a name="machine-learning-tools"></a>Ferramentas de aprendizagem automática

O VM vem com ferramentas de machine learning e algoritmos que foram pré-compilados e pré-instalados localmente. Incluem-se:

* **Vowpal Wabbit:** Um algoritmo de aprendizagem online rápido.
* **xgboost**: Uma ferramenta que fornece algoritmos de árvores otimizados e impulsionados.
* **Chocalho**: Uma ferramenta gráfica baseada em R para fácil exploração e modelação de dados.
* **Python**: Anaconda Python vem agregado com algoritmos de aprendizagem automática com bibliotecas como Scikit-learn. Pode instalar outras bibliotecas utilizando o `pip install` comando.
* **LightGBM**: Uma estrutura rápida, distribuída e de alto desempenho de gradiente com base em algoritmos de árvores de decisão.
* **R**: Uma rica biblioteca de funções de machine learning está disponível para as bibliotecas pré-instaladas inclui lm, glm, randomForest e rpart. Pode instalar outras bibliotecas executando este comando:

    ```r
    install.packages(<lib name>)
    ```

Aqui estão algumas informações adicionais sobre as três primeiras ferramentas de aprendizagem automática na lista.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit é um sistema de aprendizagem automática que utiliza técnicas como online, hashing, allreduce, reduções, learning2search, ative e interactive learning.

Para executar a ferramenta num exemplo básico, utilize os seguintes comandos:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Há outras demos maiores nesse diretório. Para mais informações sobre o Vowpal Wabbit, consulte [esta secção do GitHub](https://github.com/JohnLangford/vowpal_wabbit) e do [Wiki Vowpal Wabbit.](https://github.com/JohnLangford/vowpal_wabbit/wiki)

### <a name="xgboost"></a>xgboost

A biblioteca xgboost é projetada e otimizada para algoritmos impulsionados (árvore). O objetivo desta biblioteca é empurrar os limites de cálculo das máquinas para os extremos necessários para fornecer um impulso de árvores em larga escala que é escalável, portátil e preciso.

É fornecido como uma linha de comando e uma biblioteca R. Para utilizar esta biblioteca em R, pode iniciar uma sessão de R interativa (introduzindo **R** na concha) e carregar a biblioteca.

Aqui está um exemplo simples que pode executar num pedido R:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Para executar a linha de comando xgboost, aqui estão os comandos para correr na concha:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Um ficheiro .model é escrito para o diretório especificado. Pode encontrar informações sobre este exemplo de demonstração [no GitHub](https://github.com/dmlc/xgboost/tree/master/demo/CLI/binary_classification).

Para obter mais informações sobre o xgboost, consulte a [página de documentação xgboost](https://xgboost.readthedocs.org/en/latest/) e o seu [repositório GitHub.](https://github.com/dmlc/xgboost)

### <a name="rattle"></a>Rattle

O Chocalho (o **R** **A** nalytical **T** ool **T** o **L** ganha **E)** utiliza a exploração e modelação de dados baseadas em GUI. Apresenta resumos estatísticos e visuais de dados, transforma dados que podem ser facilmente modelados, constrói modelos não supervisionados e supervisionados a partir dos dados, apresenta o desempenho dos modelos graficamente, e marca novos conjuntos de dados. Também gera código R, replicando as operações na UI que podem ser executadas diretamente em R ou usadas como ponto de partida para uma análise mais aprofundada.

Para executar o Rattle, precisa de estar numa sessão de sessão de sessão gráfica. No terminal, introduza **R** para abrir o ambiente R. Na introdução r, insira os seguintes comandos:

```R
library(rattle)
rattle()
```

Agora uma interface gráfica abre com um conjunto de separadores. Utilize os seguintes passos de arranque rápido em Rattle para utilizar um conjunto de dados meteorológicos de amostra e construir um modelo. Em algumas das etapas, é solicitado que instale e carregue automaticamente alguns pacotes R necessários que ainda não se encontram no sistema.

> [!NOTE]
> Se não tiver acesso à instalação do pacote no diretório do sistema (o padrão), poderá ver um pedido na janela da consola R para instalar pacotes na sua biblioteca pessoal. **Responda** se vir estas indicações.

1. Selecione **Execute** (Executar).
1. Aparece uma caixa de diálogo, perguntando-lhe se pretende utilizar o conjunto de dados meteorológicos de exemplo. Selecione **Sim** para carregar o exemplo.
1. Selecione o **separador Modelo.**
1. Selecione **Executar** para construir uma árvore de decisão.
1. **Selecione Draw** para exibir a árvore de decisão.
1. Selecione a opção **Floresta** e selecione **Executar** para construir uma floresta aleatória.
1. Selecione o separador **Avaliar.**
1. Selecione a opção **Risco** e selecione **Executar** para exibir dois enredos de desempenho **de Risco (Cumulativo).**
1. Selecione o **separador Registar** para mostrar o código R gerado para as operações anteriores.
   (Devido a um bug na versão atual do Rattle, é necessário inserir um **#** personagem na frente da **Exportação deste registo** no texto do registo.)
1. Selecione o **botão Exportação** para guardar o ficheiro de script R nomeado *weather_script. R* para a pasta da casa.

Pode sair do Rattle e do R. Agora pode modificar o script R gerado. Ou, use o script como está, e execute-o a qualquer hora para repetir tudo o que foi feito dentro da UI Rattle. Especialmente para principiantes em R, esta é uma forma de fazer rapidamente análises e machine learning numa interface gráfica simples, enquanto gera código automaticamente em R para modificar ou aprender.

## <a name="next-steps"></a>Passos seguintes

Tem perguntas adicionais? Considere criar um [bilhete de apoio.](https://azure.microsoft.com/support/create-ticket/)