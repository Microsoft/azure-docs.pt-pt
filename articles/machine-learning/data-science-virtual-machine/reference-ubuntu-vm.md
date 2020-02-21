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
ms.openlocfilehash: 5c184e7f1dc828c3f9ff8d449d29ab3aaa4d1cf6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525826"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Referência: Ubuntu (Linux) Data Science Virtual Machine

Veja abaixo uma lista de ferramentas disponíveis na sua Máquina Virtual de Ciência de Dados Ubuntu. 

## <a name="deep-learning-libraries"></a>Bibliotecas de aprendizagem profunda

### <a name="cntk"></a>CNTK

O Microsoft Cognitive Toolkit é um conjunto de ferramentas de aprendizagem profunda de código aberto. Enlaces de Python estão disponíveis nos ambientes de Conda de raiz e py35. Também tem uma ferramenta de linha de comando (CNTK) que já está no caminho.

Blocos de notas de Python de exemplo estão disponíveis no JupyterHub. Para executar uma amostra básica na linha de comando, executar os seguintes comandos na concha:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Para mais informações, consulte a secção CNTK do [GitHub](https://github.com/Microsoft/CNTK) e o [wiki CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="caffe"></a>Caffe

Caffe é uma estrutura de aprendizagem profunda da visão de Berkeley e central de informações. Está disponível em /opt/caffe. Pode encontrar exemplos em /opt/caffe/exemplos.

### <a name="caffe2"></a>Caffe2

Caffe2 é uma estrutura de aprendizagem profunda do Facebook que se baseia em Caffe. Está disponível em Python 2.7 no ambiente raiz do Conda. Para o ativar, execute o seguinte comando a partir da concha:

```bash
source /anaconda/bin/activate root
```

Alguns blocos de notas de exemplo estão disponíveis no JupyterHub.

### <a name="h2o"></a>H2O

H2O é uma plataforma de Análise Preditiva e de aprendizagem rápida, dentro da memória, distribuída. Um pacote do Python está instalado em ambientes de Anaconda de raiz e py35. Também é instalado um pacote de R. 

Para abrir o H2O da linha de comando, execute `java -jar /dsvm/tools/h2o/current/h2o.jar`. Existem várias [opções de linha de comando](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) que talvez queira configurar. Você pode aceder ao Flow web UI navegando para http://localhost:54321 para começar. Blocos de notas de exemplo também estão disponíveis no JupyterHub.

### <a name="keras"></a>Keras

Keras é uma rede neural de alto nível API em Python. Pode funcionar em cima de TensorFlow, Microsoft Cognitive Toolkit ou Theano. Está disponível nos ambientes de raiz e pitão py35.

### <a name="mxnet"></a>MXNet

MXNet é uma estrutura de aprendizagem profunda destinada a eficiência e flexibilidade. Ele tem enlaces de R e Python incluídos na DSVM. Blocos de notas de exemplo estão incluídos no JupyterHub e código de exemplo está disponível no /dsvm/samples/mxnet.

### <a name="nvidia-digits"></a>NVIDIA DÍGITOS

O Sistema de Formação GPU de Aprendizagem Profunda nVIDIA, conhecido como DIGITS, é um sistema para simplificar tarefas comuns de aprendizagem profunda. Estas tarefas incluem a gestão de dados, conceção e formação de redes neurais em sistemas DEGPE, e monitorização do desempenho em tempo real com visualização avançada.

O DIGITS está disponível como um serviço chamado *dígitos*. Inicie o serviço e navegue para http://localhost:5000 para começar.

DÍGITOS também é instalado como um módulo de Python no ambiente de raiz de Conda.

### <a name="tensorflow"></a>TensorFlow

TensorFlow é a biblioteca de aprendizagem profunda da Google. É uma biblioteca de software de código aberto para computação numérica usando gráficos de fluxo de dados. TensorFlow está disponível no ambiente de Python py35 e alguns blocos de notas de exemplo estão incluídos no JupyterHub.

### <a name="theano"></a>Theano

Theano é uma biblioteca de Python para o cálculo numérica eficiente. Está disponível nos ambientes de raiz e pitão py35. 

### <a name="torch"></a>Torch

Maçarico é uma estrutura de computação científica com amplo suporte para algoritmos de machine learning. Está disponível em /dsvm/tools/torch, e **a** sessão interativa e o gestor de pacotes LuaRocks estão disponíveis na linha de comando. Exemplos estão disponíveis no /dsvm/samples/torch.

PyTorch também está disponível no ambiente de Anaconda de raiz. Os exemplos são em /dsvm/samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

R é uma das linguagens mais populares para análise de dados e o machine learning. Se quiser utilizar R para a sua análise, o VM tem o Microsoft Machine Learning Server com a Microsoft R Open e a Math Kernel Library. A Biblioteca Math Kernel otimiza as operações matemáticas comuns em algoritmos analíticos. O Microsoft R Open é 100% compatível com cran R, e qualquer uma das bibliotecas R publicadas em CRAN pode ser instalada no Microsoft R Open. 

O Machine Learning Server dá-lhe escala e operacionalização de modelos R em serviços web. Pode editar os seus programas de R em um dos editores de padrão, como o RStudio, vi ou Emacs. Se preferir utilizar o editor de Emacs, tem sido previamente instalada. O pacote Emacs ESS (Emacs Speaks Statistics) simplifica o trabalho com ficheiros R dentro do editor da Emacs.

Para abrir a consola R, introduza **R** na concha. Este comando leva-o a um ambiente interativo. Para desenvolver o seu programa R, você normalmente usa um editor como Emacs ou vi, e depois executa os scripts dentro de R. Com o RStudio, tem um IDE gráfico completo para desenvolver o seu programa R.

Há também um script R para instalar os [pacotes Top 20 R,](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) se quiser. Pode executar este guião depois de estar na interface interativa R. Como mencionado anteriormente, pode abrir essa interface inserindo **R** na concha.  

## <a name="python"></a>Python

Anaconda Python está instalado com o Python 2.7 e 3,5 ambientes. O ambiente 2.7 chama-se _raiz_, e o ambiente 3.5 chama-se _py35_. Essa distribuição contém o Python base juntamente com aproximadamente 300 dos pacotes de análises de dados, engenharia e matemática mais populares.

O ambiente de py35 é a predefinição. Para ativar o ambiente raiz (2.7), utilize este comando:

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

Em alternativa, especificar o caminho completo para do pip:

```bash
/anaconda/bin/pip install <package>
```

Para a Conda, deve sempre especificar o nome do ambiente (py35 ou raiz):

```bash
conda install <package> -n py35
```

Se estiver numa interface gráfica ou tiver um encaminhamento X11, pode entrar no **pycharm** para abrir o PyCharm Python IDE. Pode utilizar os editores de texto padrão. Além disso, podeusar Spyder, um Python IDE que está agrupado com distribuições de Anaconda Python. Spyder necessita de uma área de trabalho gráfica ou X11 reencaminhamento. O ambiente de trabalho gráfico tem um atalho para Spyder.

## <a name="jupyter-notebook"></a>Jupyter Notebook

A distribuição de Anaconda também vem com um bloco de notas do Jupyter, um ambiente para partilhar código e análise. O bloco de notas do Jupyter é acedido através do JupyterHub. Inscreveu-se usando o seu nome de utilizador e senha linux locais.

O servidor de bloco de notas do Jupyter foi previamente configurado com o Python 2, 3 de Python e kernels do R. Utilize o ícone do ambiente de trabalho **do Jupyter Notebook** para abrir o navegador e aceder ao servidor de portátil. Se estiver no VM via SSH ou no cliente X2Go, também pode aceder ao servidor de portátil Jupyter em [https://localhost:8000/](https://localhost:8000/).

> [!NOTE]
> Continue se obter quaisquer avisos de certificado.

Pode acessar o servidor de bloco de notas do Jupyter a partir de qualquer anfitrião. Introduza **https://\<nome DNS vM ou endereço IP\>:8000/** .

> [!NOTE]
> Porta 8000 está aberta na firewall por padrão quando a VM está aprovisionada. 

Temos cadernos de amostras embalados, um em Python e outro em R. Pode ver o link para as amostras na página inicial do caderno depois de autenticar o caderno Jupyter utilizando o seu nome de utilizador e senha linux locais. Pode criar um novo caderno selecionando **Novo,** e, em seguida, selecionando o núcleo de linguagem apropriado. Se não vir o botão **Novo,** selecione o ícone **Jupyter** na parte superior esquerda para ir à página inicial do servidor de cadernos.

## <a name="apache-spark-standalone"></a>Faísca Apache autónoma

Um exemplo autónomo de Apache Spark é pré-instalado no Linux DSVM para ajudá-lo a desenvolver aplicações Spark localmente antes de testá-las e implantá-las em grandes clusters. 

Pode executar programas PySpark por meio do kernel do Jupyter. Quando abrir o Jupyter, selecione **o** novo botão e deverá ver uma lista de núcleos disponíveis. **Spark - Python** é o núcleo PySpark que permite construir aplicações Spark usando a linguagem Python. Você também pode usar um Python IDE como PyCharm ou Spyder para construir o seu programa Spark. 

Neste caso autónomo, a pilha de faíscas funciona dentro do programa de clientes chamados. Esta funcionalidade torna mais rápido e fácil resolver problemas, em comparação com o desenvolvimento de um cluster Spark.

Jupyter fornece um caderno PySpark. Pode encontrá-lo no diretório SparkML sob o diretório inicial de Jupyter ($HOME/cadernos/SparkML/pySpark). 

Se estiver a programar em R para Spark, pode utilizar o Microsoft Machine Learning Server, O SparkR ou o Sparklyr. 

Antes de funcionar num contexto spark no Microsoft Machine Learning Server, precisa de fazer um passo de configuração único para permitir um nó único local Hadoop HDFS e Yarn. Por predefinição, os serviços do Hadoop são instalados mas desativados na DSVM. Para o ativar, é necessário executar os seguintes comandos como raiz da primeira vez:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Pode parar os serviços relacionados com Hadoop quando não precisa deles executando ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```.

O diretório /dsvm/samples/MRS fornece uma amostra que demonstra como desenvolver e testar o Microsoft Machine Learning Server num contexto remoto de Spark (a instância de Spark autónoma no DSVM).

## <a name="ides-and-editors"></a>IDEs e editores

Você tem uma escolha de vários editores de código, incluindo vi/Vim, Emacs, PyCharm, RStudio e IntelliJ. 

PyCharm, RStudio e IntelliJ são editores gráficos. Para usá-los, precisa de ser inscrito num ambiente de trabalho gráfico. Abre-as utilizando atalhos de menu de desktop e aplicação.

Vim e Emacs são editores baseados em texto. No Emacs, o pacote de complemento ESS facilita o trabalho com r dentro do editor da Emacs. Pode encontrar mais informações no site da [ESS.](https://ess.r-project.org/)

O LaTex é instalado através do pacote texlive, juntamente com um pacote de add-on Emacs chamado [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Este pacote simplifica a autoria dos seus documentos LaTex dentro da Emacs.  

## <a name="databases"></a>Bases de Dados

### <a name="graphical-sql-client"></a>Cliente SQL de gráfico

O SQuirrel SQL, um cliente SQL gráfico, pode ligar-se a várias bases de dados (como o Microsoft SQL Server e o MySQL) e executar consultas SQL. Pode executar o SQuirrel SQL a partir de uma sessão gráfica de ambiente de trabalho (através do cliente X2Go, por exemplo) utilizando um ícone de ambiente de trabalho. Ou pode executar o cliente usando o seguinte comando na concha:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Antes da primeira utilização, configurar os controladores e aliases de base de dados. Os condutores da JDBC estão localizados em /usr/share/java/jdbcdrivers.

Para mais informações, consulte [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Ferramentas da linha de comandos para acessar o Microsoft SQL Server

O pacote de controladores ODBC para o SQL Server também vem com duas ferramentas de linha de comandos:

- **bcp**: A ferramenta bcp copia dados entre uma instância do Microsoft SQL Server e um ficheiro de dados num formato especificado pelo utilizador. Pode utilizar a ferramenta bcp para importar um grande número de novas linhas em tabelas do SQL Server ou para exportar dados de tabelas para ficheiros de dados. Para importar dados numa tabela, deve utilizar um ficheiro de formato criado para essa tabela. Ou, deve compreender a estrutura da tabela e os tipos de dados que são válidos para as suas colunas.

  Para mais informações, consulte [Connecting com o BCP](https://msdn.microsoft.com/library/hh568446.aspx).

- **sqlcmd:** Pode introduzir declarações Transact-SQL utilizando a ferramenta sqlcmd. Também pode introduzir procedimentos do sistema e ficheiros de script no pedido de comando. Esta ferramenta utiliza a ODBC para executar lotes Transact-SQL.

  Para mais informações, consulte [A Ligação com sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Existem algumas diferenças nesta ferramenta entre as plataformas Linux e Windows. Consulte a documentação para obter detalhes.

### <a name="database-access-libraries"></a>Bibliotecas de acesso de base de dados

As bibliotecas estão disponíveis em R e Python para acesso à base de dados:

* Em R, pode utilizar o pacote RODBC ou o pacote dplyr para consultar ou executar declarações SQL no servidor de base de dados.
* Em Python, a biblioteca pyodbc fornece acesso à base de dados com a ODBC como camada subjacente.  

## <a name="azure-tools"></a>Ferramentas do Azure

As seguintes ferramentas do Azure são instaladas na VM:

* **Azure CLI:** Pode utilizar a interface de linha de comando em Azure para criar e gerir os recursos azure através de comandos de conchas. Para abrir as ferramentas Azure, insira **a ajuda azure.** Para mais informações, consulte a página de [documentação do Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: Azure Storage Explorer é uma ferramenta gráfica que pode usar para navegar através dos objetos que guardou na sua conta de armazenamento Azure, e para carregar e transferir dados de e para as bolhas Azure. Pode acessar o Explorador de armazenamento do ícone de atalho de desktop. Também pode abri-lo a partir de um pedido de concha, entrando no **StorageExplorer**. Você deve ser contratado por um cliente X2Go, ou ter o encaminhamento X11 configurado.
* **Bibliotecas Azure**: Seguem-se algumas das bibliotecas pré-instaladas.
  
  * **Python**: As bibliotecas relacionadas com o Azure em Python são *azure,* *azureml,* *pydocumentdb*e *pyodbc*. Com as três primeiras bibliotecas, pode aceder a serviços de armazenamento do Azure, Azure Machine Learning e Azure Cosmos DB (uma base de dados NoSQL no Azure). A biblioteca de quarta, pyodbc (juntamente com o Microsoft ODBC driver para SQL Server), permite o acesso ao SQL Server, SQL Database do Azure e Azure SQL Data Warehouse do Python, utilizando uma interface ODBC. Insira a lista de **pips** para ver todas as bibliotecas listadas. Certifique-se de que execute este comando no Python 2.7 e 3,5 ambientes.
  * **R**: As bibliotecas relacionadas com o Azure em R são AzureML e RODBC.
  * **Java**: A lista de bibliotecas Azure Java pode ser encontrada no diretório /dsvm/sdk/AzureSDKJava no VM. As bibliotecas de chave são do Azure armazenamento e gestão de APIs, do Azure Cosmos DB, JDBC controladores e para o SQL Server.  

Pode aceder ao [portal Azure](https://portal.azure.com) a partir do navegador Firefox pré-instalado. No portal do Azure, pode criar, gerir e monitorizar recursos do Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço cloud totalmente gerido que permite-lhe criar, implementar e partilhar soluções de Análise Preditiva. Pode construir as suas experiências e modelos do Azure Machine Learning Studio (clássico). Você pode acessá-lo a partir de um navegador web na Máquina Virtual data Science visitando [o Microsoft Azure Machine Learning](https://studio.azureml.net).

Depois de iniciar sessão no Azure Machine Learning Studio (clássico), pode usar uma tela de experimentação para construir um fluxo lógico para os algoritmos de aprendizagem automática. Você também tem acesso a um caderno Jupyter que está hospedado no Azure Machine Learning e pode trabalhar perfeitamente com as experiências no Azure Machine Learning Studio (clássico). 

Operacionalize os modelos de machine learning que criou, encapsulando-as numa interface de serviço da web. A operacionalização de modelos de aprendizagem automática permite que os clientes escritos em qualquer idioma invoquem previsões desses modelos. Para mais informações, consulte a documentação do [Machine Learning.](https://azure.microsoft.com/documentation/services/machine-learning/)

Também pode construir os seus modelos em R ou Python no VM e, em seguida, implantá-los em produção em Azure Machine Learning. Instalámos bibliotecas em R (**AzureML)** e Python **(azureml)** para ativar esta funcionalidade.

Para obter informações sobre como implementar modelos em R e Python no Azure Machine Learning, veja [dez coisas que pode fazer na Máquina Virtual](vm-do-ten-things.md)da Ciência dos Dados .

> [!NOTE]
> Estas instruções foram escritas para a versão Windows da Máquina Virtual da Ciência dos Dados. Mas as informações fornecidas sobre a implementação de modelos para o Azure Machine Learning é aplicável à VM do Linux.

## <a name="machine-learning-tools"></a>Ferramentas de aprendizagem de máquina

O VM vem com ferramentas de aprendizagem automática e algoritmos que foram pré-compilados e pré-instalados localmente. Estas incluem:

* **Vowpal Wabbit**: Um algoritmo de aprendizagem online rápido.
* **xgboost**: Uma ferramenta que fornece algoritmos de árvore otimizados e impulsionados.
* **Chocalho**: Uma ferramenta gráfica baseada em R para fácil exploração e modelação de dados.
* **Python**: Anaconda Python vem agregada com algoritmos de aprendizagem automática com bibliotecas como scikit-learn. Pode instalar outras bibliotecas utilizando o comando `pip install`.
* **LightGBM**: Um quadro de reforço rápido, distribuído e de alto desempenho baseado em algoritmos de árvores de decisão.
* **R**: Uma rica biblioteca de funções de machine learning está disponível para R. Bibliotecas pré-instaladas incluem Lm, glm, randomForest e rpart. Pode instalar outras bibliotecas executando este comando:
  
        install.packages(<lib name>)

Eis algumas informações adicionais sobre as ferramentas de três aprendizagem primeiro na lista.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit é um sistema que utiliza técnicas, como online, hash, allreduce, reduções, learning2search, Active Directory, de aprendizagem automática e a aprendizagem interativa.

Para executar a ferramenta com um exemplo básico, utilize os seguintes comandos:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Existem outras, maiores demonstrações nesse diretório. Para mais informações sobre a Vowpal Wabbit, consulte [esta secção do GitHub](https://github.com/JohnLangford/vowpal_wabbit) e do [Wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>xgboost

A biblioteca xgboost é projetada e otimizada para algoritmos impulsionados (árvore). O objetivo dessa biblioteca é emitir os limites de computação de máquinas para extremos precisava fornecer árvore em grande escala adaptativo que é dimensionável, portáteis e precisas.

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

Rattle (o **R** **A**nalytical **T**ool **T**o **L**earn **E**asily) usa a exploração e modelação de dados baseados em GUI. Ele Apresenta resumos de estatísticos e visual de dados, transformações de dados que podem ser modelados prontamente, cria modelos supervisionados e supervisionados dos dados, apresenta o desempenho dos modelos graficamente, e conjuntos de dados de novas pontuações. Ele também gera um código de R, replicar as operações na interface de Usuário que podem ser executadas diretamente no R ou utilizadas como um ponto de partida para análises posteriores.

Para executar Rattle, terá de estar numa gráfica desktop início de sessão. No terminal, introduza **R** para abrir o ambiente R. No prompt de R, introduza os seguintes comandos:

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
   (Devido a um bug no lançamento atual do Rattle, é necessário inserir um **#** carácter em frente à **Exportação deste registo** no texto do registo.)
1. Selecione o botão **Export** para guardar o ficheiro r denominado *weather_script. R* para a pasta inicial.

Pode sair de Rattle e R. Agora pode modificar o script R gerado. Ou, use o guião como está, e execute-o a qualquer momento para repetir tudo o que foi feito dentro da UI rattle. Especialmente para principiantes em R, esta é uma forma de fazer rapidamente análise e machine learning em uma interface gráfica simples, enquanto gera automaticamente código em R para modificar ou aprender.

## <a name="next-steps"></a>Passos seguintes

Tem perguntas adicionais? Considere criar um bilhete de [apoio.](https://azure.microsoft.com/support/create-ticket/)