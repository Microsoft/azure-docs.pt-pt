---
title: 'Referência: Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Detalhes sobre ferramentas incluídas na Máquina Virtual de Ciência de Dados ubuntu
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 830668a78929a5e6a7e131ade5c62b81e6d725c3
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631371"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Referência: Ubuntu (Linux) Data Science Virtual Machine

Veja abaixo uma lista de ferramentas disponíveis na sua Máquina Virtual de Ciência de Dados Ubuntu. 

## <a name="deep-learning-libraries"></a>Bibliotecas de aprendizagem profunda

### <a name="cntk"></a>CNTK

O Microsoft Cognitive Toolkit é um conjunto de ferramentas de aprendizagem profunda de código aberto. As encadernações python estão disponíveis nos ambientes raiz e py35 Conda. Também tem uma ferramenta de linha de comando (CNTK) que já está no caminho.

Os cadernos Da amostra Python estão disponíveis no JupyterHub. Para executar uma amostra básica na linha de comando, executar os seguintes comandos na concha:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Para mais informações, consulte a secção CNTK do [GitHub](https://github.com/Microsoft/CNTK) e o [wiki CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="caffe"></a>Caffe

Caffe é um quadro de aprendizagem profunda do Centro de Visão e Aprendizagem de Berkeley. Está disponível em /opt/caffe. Pode encontrar exemplos em /opt/caffe/exemplos.

### <a name="caffe2"></a>Caffe2

Caffe2 é um quadro de aprendizagem profunda do Facebook que é construído em Caffe. Está disponível em Python 2.7 no ambiente raiz do Conda. Para o ativar, execute o seguinte comando a partir da concha:

```bash
source /anaconda/bin/activate root
```

Alguns cadernos de exemplo estão disponíveis no JupyterHub.

### <a name="h2o"></a>H2O

O H2O é uma plataforma rápida, em memória, distribuída de machine learning e análise preditiva. Um pacote Python é instalado tanto nos ambientes de raiz como de py35 Anaconda. Também está instalado um pacote R. 

Para abrir o H2O da `java -jar /dsvm/tools/h2o/current/h2o.jar`linha de comando, corra . Existem várias [opções de linha de comando](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) que talvez queira configurar. Você pode aceder ao Flow web http://localhost:54321 UI navegando para começar. Os cadernos de amostras também estão disponíveis no JupyterHub.

### <a name="keras"></a>Keras

Keras é uma rede neural de alto nível API em Python. Pode funcionar em cima de TensorFlow, Microsoft Cognitive Toolkit ou Theano. Está disponível nos ambientes de raiz e pitão py35.

### <a name="mxnet"></a>MXNet

O MXNet é um quadro de aprendizagem profunda projetado tanto para a eficiência como para a flexibilidade. Tem encadernações R e Python incluídas no DSVM. Os cadernos de amostras estão incluídos no JupyterHub, e o código da amostra está disponível em /dsvm/samples/mxnet.

### <a name="nvidia-digits"></a>Dígitos nvidia

O Sistema de Formação GPU de Aprendizagem Profunda nVIDIA, conhecido como DIGITS, é um sistema para simplificar tarefas comuns de aprendizagem profunda. Estas tarefas incluem a gestão de dados, conceção e formação de redes neurais em sistemas DEGPE, e monitorização do desempenho em tempo real com visualização avançada.

O DIGITS está disponível como um serviço chamado *dígitos*. Inicie o serviço e http://localhost:5000 navegue para começar.

O DIGITS também está instalado como módulo Python no ambiente raiz do Conda.

### <a name="tensorflow"></a>TensorFlow

TensorFlow é a biblioteca de aprendizagem profunda da Google. É uma biblioteca de software de código aberto para computação numérica usando gráficos de fluxo de dados. O TensorFlow está disponível no ambiente py35 Python, e alguns cadernos de amostraestão incluídos no JupyterHub.

### <a name="theano"></a>Theano

Theano é uma biblioteca Python para uma computação numérica eficiente. Está disponível nos ambientes de raiz e pitão py35. 

### <a name="torch"></a>Torch

Torch é um quadro de computação científica com amplo suporte para algoritmos de aprendizagem automática. Está disponível em /dsvm/tools/torch, e **a** sessão interativa e o gestor de pacotes LuaRocks estão disponíveis na linha de comando. Exemplos estão disponíveis em /dsvm/samples/torch.

PyTorch também está disponível no ambiente raiz Anaconda. Exemplos estão em /dsvm/samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

R é uma das línguas mais populares para análise de dados e machine learning. Se quiser utilizar R para a sua análise, o VM tem o Microsoft Machine Learning Server com a Microsoft R Open e a Math Kernel Library. A Biblioteca Math Kernel otimiza as operações matemáticas comuns em algoritmos analíticos. O Microsoft R Open é 100% compatível com cran R, e qualquer uma das bibliotecas R publicadas em CRAN pode ser instalada no Microsoft R Open. 

O Machine Learning Server dá-lhe escala e operacionalização de modelos R em serviços web. Pode editar os seus programas R num dos editores padrão, como RStudio, vi ou Emacs. Se preferir usar o editor da Emacs, foi pré-instalado. O pacote Emacs ESS (Emacs Speaks Statistics) simplifica o trabalho com ficheiros R dentro do editor da Emacs.

Para abrir a consola R, introduza **R** na concha. Este comando leva-o a um ambiente interativo. Para desenvolver o seu programa R, você normalmente usa um editor como Emacs ou vi, e depois executa os scripts dentro de R. Com o RStudio, tem um IDE gráfico completo para desenvolver o seu programa R.

Há também um script R para instalar os [pacotes Top 20 R,](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) se quiser. Pode executar este guião depois de estar na interface interativa R. Como mencionado anteriormente, pode abrir essa interface inserindo **R** na concha.  

## <a name="python"></a>Python

A Anaconda Python está instalada com ambientes Python 2.7 e 3.5. O ambiente 2.7 chama-se _raiz_, e o ambiente 3.5 chama-se _py35_. Esta distribuição contém a base Python juntamente com cerca de 300 dos pacotes de matemática, engenharia e análise de dados mais populares.

O ambiente py35 é o padrão. Para ativar o ambiente raiz (2.7), utilize este comando:

```bash
source activate root
```

Para voltar a ativar o ambiente py35, utilize este comando:

```bash
source activate py35
```

Para invocar uma sessão interativa python, entre **python** na concha. 

Instale bibliotecas python adicionais utilizando Conda ou pip. Para o pip, ative primeiro o ambiente correto se não quiser o padrão:

```bash
source activate root
pip install <package>
```

Ou especificar o caminho completo para pip:

```bash
/anaconda/bin/pip install <package>
```

Para a Conda, deve sempre especificar o nome do ambiente (py35 ou raiz):

```bash
conda install <package> -n py35
```

Se estiver numa interface gráfica ou tiver um encaminhamento X11, pode entrar no **pycharm** para abrir o PyCharm Python IDE. Pode utilizar os editores de texto predefinidos. Além disso, podeusar Spyder, um Python IDE que está agrupado com distribuições de Anaconda Python. Spyder precisa de um ambiente de trabalho gráfico ou de um encaminhamento X11. O ambiente de trabalho gráfico tem um atalho para Spyder.

## <a name="jupyter-notebook"></a>Bloco de notas do Jupyter

A distribuição da Anaconda também vem com um caderno Jupyter, um ambiente para partilhar código e análise. O caderno Jupyter é acedido através do JupyterHub. Inscreveu-se usando o seu nome de utilizador e senha linux locais.

O servidor de caderno sapateiro jupyter foi pré-configurado com python 2, Python 3 e núcleos R. Utilize o ícone do ambiente de trabalho **do Jupyter Notebook** para abrir o navegador e aceder ao servidor de portátil. Se estiver no VM via SSH ou no cliente X2Go, também pode [https://localhost:8000/](https://localhost:8000/)aceder ao servidor de portátil Jupyter em .

> [!NOTE]
> Continue se receber algum aviso de certificado.

Pode aceder ao servidor de cadernos Jupyter a partir de qualquer anfitrião. Introduza **https:// nome DNS VM\<ou endereço\>IP :8000/**.

> [!NOTE]
> A porta 8000 é aberta na firewall por padrão quando o VM é provisionado. 

Temos cadernos de amostras embalados, um em Python e outro em R. Pode ver o link para as amostras na página inicial do caderno depois de autenticar o caderno Jupyter utilizando o seu nome de utilizador e senha linux locais. Pode criar um novo caderno selecionando **Novo,** e, em seguida, selecionando o núcleo de linguagem apropriado. Se não vir o botão **Novo,** selecione o ícone **Jupyter** na parte superior esquerda para ir à página inicial do servidor de cadernos.

## <a name="apache-spark-standalone"></a>Faísca Apache autónoma

Um exemplo autónomo de Apache Spark é pré-instalado no Linux DSVM para ajudá-lo a desenvolver aplicações Spark localmente antes de testá-las e implantá-las em grandes clusters. 

Podes executar programas pySpark através do núcleo de Jupyter. Quando abrir o Jupyter, selecione **o** novo botão e deverá ver uma lista de núcleos disponíveis. **Spark - Python** é o núcleo PySpark que permite construir aplicações Spark usando a linguagem Python. Você também pode usar um Python IDE como PyCharm ou Spyder para construir o seu programa Spark. 

Neste caso autónomo, a pilha de faíscas funciona dentro do programa de clientes chamados. Esta funcionalidade torna mais rápido e fácil resolver problemas, em comparação com o desenvolvimento de um cluster Spark.

Jupyter fornece um caderno PySpark. Pode encontrá-lo no diretório SparkML sob o diretório inicial de Jupyter ($HOME/cadernos/SparkML/pySpark). 

Se estiver a programar em R para Spark, pode utilizar o Microsoft Machine Learning Server, O SparkR ou o Sparklyr. 

Antes de funcionar num contexto spark no Microsoft Machine Learning Server, precisa de fazer um passo de configuração único para permitir um nó único local Hadoop HDFS e Yarn. Por predefinição, os serviços Hadoop são instalados, mas desativados no DSVM. Para o ativar, é necessário executar os seguintes comandos como raiz da primeira vez:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Pode parar os serviços relacionados com Hadoop quando ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```não precisa deles funcionando.

O diretório /dsvm/samples/MRS fornece uma amostra que demonstra como desenvolver e testar o Microsoft Machine Learning Server num contexto remoto de Spark (a instância de Spark autónoma no DSVM).

## <a name="ides-and-editors"></a>IDEs e editores

Você tem uma escolha de vários editores de código, incluindo vi/Vim, Emacs, PyCharm, RStudio e IntelliJ. 

PyCharm, RStudio e IntelliJ são editores gráficos. Para usá-los, precisa de ser inscrito num ambiente de trabalho gráfico. Abre-as utilizando atalhos de menu de desktop e aplicação.

Vim e Emacs são editores baseados em texto. No Emacs, o pacote de complemento ESS facilita o trabalho com r dentro do editor da Emacs. Pode encontrar mais informações no site da [ESS.](https://ess.r-project.org/)

O LaTex é instalado através do pacote texlive, juntamente com um pacote de add-on Emacs chamado [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Este pacote simplifica a autoria dos seus documentos LaTex dentro da Emacs.  

## <a name="databases"></a>Bases de Dados

### <a name="graphical-sql-client"></a>Cliente SQL gráfico

O SQuirrel SQL, um cliente SQL gráfico, pode ligar-se a várias bases de dados (como o Microsoft SQL Server e o MySQL) e executar consultas SQL. Pode executar o SQuirrel SQL a partir de uma sessão gráfica de ambiente de trabalho (através do cliente X2Go, por exemplo) utilizando um ícone de ambiente de trabalho. Ou pode executar o cliente usando o seguinte comando na concha:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Antes da primeira utilização, instale os seus controladores e pseudónimos de base de dados. Os condutores da JDBC estão localizados em /usr/share/java/jdbcdrivers.

Para mais informações, consulte [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Ferramentas de linha de comando para aceder ao Microsoft SQL Server

O pacote de controlador ODBC para OQL Server também vem com duas ferramentas de linha de comando:

- **bcp**: A ferramenta bcp copia dados entre uma instância do Microsoft SQL Server e um ficheiro de dados num formato especificado pelo utilizador. Pode utilizar a ferramenta bcp para importar um grande número de novas linhas em tabelas do SQL Server ou para exportar dados de tabelas para ficheiros de dados. Para importar dados numa tabela, deve utilizar um ficheiro de formato criado para essa tabela. Ou, deve compreender a estrutura da tabela e os tipos de dados que são válidos para as suas colunas.

  Para mais informações, consulte [Connecting com o BCP](https://msdn.microsoft.com/library/hh568446.aspx).

- **sqlcmd:** Pode introduzir declarações Transact-SQL utilizando a ferramenta sqlcmd. Também pode introduzir procedimentos do sistema e ficheiros de script no pedido de comando. Esta ferramenta utiliza a ODBC para executar lotes Transact-SQL.

  Para mais informações, consulte [A Ligação com sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Existem algumas diferenças nesta ferramenta entre as plataformas Linux e Windows. Consulte a documentação para mais detalhes.

### <a name="database-access-libraries"></a>Bibliotecas de acesso a bases de dados

As bibliotecas estão disponíveis em R e Python para acesso à base de dados:

* Em R, pode utilizar o pacote RODBC ou o pacote dplyr para consultar ou executar declarações SQL no servidor de base de dados.
* Em Python, a biblioteca pyodbc fornece acesso à base de dados com a ODBC como camada subjacente.  

## <a name="azure-tools"></a>Ferramentas Azure

As seguintes ferramentas Azure são instaladas no VM:

* **Azure CLI:** Pode utilizar a interface de linha de comando em Azure para criar e gerir os recursos azure através de comandos de conchas. Para abrir as ferramentas Azure, insira **a ajuda azure.** Para mais informações, consulte a página de [documentação do Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: Azure Storage Explorer é uma ferramenta gráfica que pode usar para navegar através dos objetos que guardou na sua conta de armazenamento Azure, e para carregar e transferir dados de e para as bolhas Azure. Pode aceder ao Storage Explorer a partir do ícone de atalho de secretária. Também pode abri-lo a partir de um pedido de concha, entrando no **StorageExplorer**. Você deve ser contratado por um cliente X2Go, ou ter o encaminhamento X11 configurado.
* **Bibliotecas Azure**: Seguem-se algumas das bibliotecas pré-instaladas.
  
  * **Python**: As bibliotecas relacionadas com o Azure em Python são *azure,* *azureml,* *pydocumentdb*e *pyodbc*. Com as três primeiras bibliotecas, pode aceder aos serviços de armazenamento Azure, Azure Machine Learning e Azure Cosmos DB (uma base de dados NoSQL no Azure). A quarta biblioteca, pyodbc (juntamente com o controlador Microsoft ODBC para o SQL Server), permite o acesso ao SQL Server, Azure SQL Database e Azure SQL Data Warehouse da Python utilizando uma interface ODBC. Insira a lista de **pips** para ver todas as bibliotecas listadas. Certifique-se de dirigir este comando nos ambientes Python 2.7 e 3.5.
  * **R**: As bibliotecas relacionadas com o Azure em R são AzureML e RODBC.
  * **Java**: A lista de bibliotecas Azure Java pode ser encontrada no diretório /dsvm/sdk/AzureSDKJava no VM. As principais bibliotecas são os condutores de armazenamento e gestão Azure, Azure Cosmos DB e JDBC para o SQL Server.  

Pode aceder ao [portal Azure](https://portal.azure.com) a partir do navegador Firefox pré-instalado. No portal Azure, pode criar, gerir e monitorizar os recursos do Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço de cloud totalmente gerido que lhe permite construir, implementar e partilhar soluções de análise preditivas. Pode construir as suas experiências e modelos no estúdio Azure Machine Learning (pré-visualização). Você pode acessá-lo a partir de um navegador web na Máquina Virtual data Science visitando [o Microsoft Azure Machine Learning](https://ml.azure.com).

Depois de iniciar sessão no estúdio Azure Machine Learning, pode usar uma tela de experimentação para construir um fluxo lógico para os algoritmos de aprendizagem automática. Você também tem acesso a um caderno Jupyter que está hospedado no Azure Machine Learning e pode trabalhar perfeitamente com as experiências no estúdio Azure Machine Learning. 

Operacionalize os modelos de aprendizagem automática que construiu envolvendo-os numa interface de serviço web. A operacionalização de modelos de aprendizagem automática permite que os clientes escritos em qualquer idioma invoquem previsões desses modelos. Para mais informações, consulte a documentação do [Machine Learning.](https://azure.microsoft.com/documentation/services/machine-learning/)

Também pode construir os seus modelos em R ou Python no VM e, em seguida, implantá-los em produção em Azure Machine Learning. Instalámos bibliotecas em R (**AzureML)** e Python **(azureml)** para ativar esta funcionalidade.

Para obter informações sobre como implementar modelos em R e Python no Azure Machine Learning, veja [dez coisas que pode fazer na Máquina Virtual](vm-do-ten-things.md)da Ciência dos Dados .

> [!NOTE]
> Estas instruções foram escritas para a versão Windows da Máquina Virtual da Ciência dos Dados. Mas a informação ali fornecida sobre a implementação de modelos para o Azure Machine Learning é aplicável ao VM Linux.

## <a name="machine-learning-tools"></a>Ferramentas de aprendizagem automática

O VM vem com ferramentas de aprendizagem automática e algoritmos que foram pré-compilados e pré-instalados localmente. Estas incluem:

* **Vowpal Wabbit**: Um algoritmo de aprendizagem online rápido.
* **xgboost**: Uma ferramenta que fornece algoritmos de árvore otimizados e impulsionados.
* **Chocalho**: Uma ferramenta gráfica baseada em R para fácil exploração e modelação de dados.
* **Python**: Anaconda Python vem agregada com algoritmos de aprendizagem automática com bibliotecas como scikit-learn. Pode instalar outras bibliotecas utilizando o `pip install` comando.
* **LightGBM**: Um quadro de reforço rápido, distribuído e de alto desempenho baseado em algoritmos de árvores de decisão.
* **R**: Uma rica biblioteca de funções de machine learning está disponível para R. Bibliotecas pré-instaladas incluem Lm, glm, randomForest e rpart. Pode instalar outras bibliotecas executando este comando:
  
        install.packages(<lib name>)

Aqui estão algumas informações adicionais sobre as três primeiras ferramentas de machine learning na lista.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

A Vowpal Wabbit é um sistema de machine learning que utiliza técnicas como online, hashing, allreduce, reduções, learning2search, ative e interactive learning.

Para executar a ferramenta com um exemplo básico, utilize os seguintes comandos:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Há outras demonstrações maiores nesse diretório. Para mais informações sobre a Vowpal Wabbit, consulte [esta secção do GitHub](https://github.com/JohnLangford/vowpal_wabbit) e do [Wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>xgboost

A biblioteca xgboost é projetada e otimizada para algoritmos impulsionados (árvore). O objetivo desta biblioteca é empurrar os limites de computação das máquinas para os extremos necessários para fornecer um aumento de árvores em larga escala que seja escalável, portátil e preciso.

É fornecido como uma linha de comando e uma biblioteca R. Para utilizar esta biblioteca em R, pode iniciar uma sessão r interativa (entrando em **R** na concha) e carregar a biblioteca.

Aqui está um simples exemplo de que pode correr num pedido r:

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

Um ficheiro .modelo é escrito para o diretório especificado. Pode encontrar informações sobre este exemplo de demonstração [no GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Para obter mais informações sobre o xgboost, consulte a página de [documentação xgboost](https://xgboost.readthedocs.org/en/latest/) e o seu [repositório GitHub](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle (o **R** **A**nalytical **T**ool **T**o **L**earn **E**asily) usa a exploração e modelação de dados baseados em GUI. Apresenta resumos estatísticos e visuais de dados, transforma dados que podem ser facilmente modelados, constrói modelos não supervisionados e supervisionados a partir dos dados, apresenta o desempenho dos modelos graficamente e marca novos conjuntos de dados. Também gera código R, replicando as operações na UI que podem ser executadas diretamente em R ou usadas como ponto de partida para uma análise mais aprofundada.

Para executar o Rattle, tens de estar numa sessão gráfica de sessão de sessão de sessão de sessão de sessão de sessão de sessão de sessão de desktop. No terminal, introduza **R** para abrir o ambiente R. No pedido R, insira os seguintes comandos:

```R
library(rattle)
rattle()
```

Agora uma interface gráfica abre com um conjunto de separadores. Utilize os seguintes passos de arranque rápido em Rattle para utilizar um conjunto de dados meteorológicos de amostra e construir um modelo. Em alguns dos passos, é-lhe pedido que instale e carregue automaticamente alguns pacotes R necessários que ainda não estão no sistema.

> [!NOTE]
> Se não tiver acesso à instalação do pacote no diretório do sistema (o predefinido), poderá ver uma solicitação na janela da consola R para instalar pacotes na sua biblioteca pessoal. Responda **se** vir estas indicações.

1. Selecione **Executar**.
1. Aparece uma caixa de diálogo, perguntando-lhe se pretende utilizar o conjunto de dados meteorológicos de exemplo. Selecione **Sim** para carregar o exemplo.
1. Selecione o separador **Modelo.**
1. Selecione **Executar** para construir uma árvore de decisão.
1. Selecione **Draw** para exibir a árvore de decisão.
1. Selecione a opção **Floresta** e selecione **Executar** para construir uma floresta aleatória.
1. Selecione o separador **Avaliar.**
1. Selecione a opção **Risco** e selecione **Executar** para exibir dois lotes de desempenho **de risco (cumulativo).**
1. Selecione o separador **Log** para mostrar o código R gerado para as operações anteriores.
   (Devido a um bug no lançamento atual do **#** Rattle, é necessário inserir um personagem em frente à **Exportação deste registo** no texto do registo.)
1. Selecione o botão **Export** para guardar o ficheiro r denominado *weather_script. R* para a pasta inicial.

Pode sair de Rattle e R. Agora pode modificar o script R gerado. Ou, use o guião como está, e execute-o a qualquer momento para repetir tudo o que foi feito dentro da UI rattle. Especialmente para principiantes em R, esta é uma forma de fazer rapidamente análise e machine learning em uma interface gráfica simples, enquanto gera automaticamente código em R para modificar ou aprender.

## <a name="next-steps"></a>Passos seguintes

Tem perguntas adicionais? Considere criar um bilhete de [apoio.](https://azure.microsoft.com/support/create-ticket/)