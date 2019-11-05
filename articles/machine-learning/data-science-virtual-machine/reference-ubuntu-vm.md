---
title: 'Referência: Ubuntu DSVM'
description: Detalhes sobre as ferramentas incluídas no Ubuntu Máquina Virtual de Ciência de Dados
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 97072f1a17f2b0adbe96afae3263dc84aff0f30f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497635"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Referência: Ubuntu (Linux) Máquina Virtual de Ciência de Dados

Veja abaixo uma lista de ferramentas disponíveis em sua Máquina Virtual de Ciência de Dados do Ubuntu. 

## <a name="deep-learning-libraries"></a>Bibliotecas de aprendizado profundo

### <a name="cntk"></a>CNTK

O Microsoft Cognitive Toolkit é um kit de ferramentas de aprendizado profundo de código aberto. As associações do Python estão disponíveis nos ambientes raiz e py35 Conda. Ele também tem uma ferramenta de linha de comando (CNTK) que já está no caminho.

Os notebooks Python de exemplo estão disponíveis em JupyterHub. Para executar um exemplo básico na linha de comando, execute os seguintes comandos no Shell:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Para obter mais informações, consulte a seção CNTK do [GitHub](https://github.com/Microsoft/CNTK) e o [wiki do CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="caffe"></a>Caffe

O Caffe é uma estrutura de aprendizado profundo na visão da Berkeley e no centro de aprendizado. Ele está disponível em/opt/Caffe. Você pode encontrar exemplos em/opt/Caffe/examples.

### <a name="caffe2"></a>Caffe2

O Caffe2 é uma estrutura de aprendizado profundo do Facebook que se baseia no Caffe. Ele está disponível no Python 2,7 no ambiente raiz do Conda. Para ativá-lo, execute o seguinte comando no Shell:

```bash
source /anaconda/bin/activate root
```

Alguns blocos de anotações de exemplo estão disponíveis em JupyterHub.

### <a name="h2o"></a>H2O

O H2O é uma plataforma rápida, na memória, de aprendizado de máquina distribuída e de análise preditiva. Um pacote do Python é instalado nos ambientes raiz e py35 Anaconda. Um pacote R também é instalado. 

Para abrir o H2O na linha de comando, execute `java -jar /dsvm/tools/h2o/current/h2o.jar`. Há várias [Opções de linha de comando](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) que você pode desejar configurar. Você pode acessar a interface do usuário da Web do Flow navegando até http://localhost:54321 para começar. Os blocos de anotações de exemplo também estão disponíveis em JupyterHub.

### <a name="keras"></a>Keras

Keras é uma API de rede neural de alto nível no Python. Ele pode ser executado sobre TensorFlow, Microsoft Cognitive Toolkit ou Theano. Ele está disponível nos ambientes raiz e py35 Python.

### <a name="mxnet"></a>MXNet

O MXNet é uma estrutura de aprendizado profundo projetada para eficiência e flexibilidade. Ele tem associações de R e Python incluídas no DSVM. Os blocos de anotações de exemplo estão incluídos no JupyterHub e o código de exemplo está disponível em/dsvm/Samples/mxnet.

### <a name="nvidia-digits"></a>DÍGITOS NVIDIA

O sistema de treinamento de GPU de aprendizado profundo NVIDIA, conhecido como dígitos, é um sistema para simplificar tarefas comuns de aprendizado profundo. Essas tarefas incluem o gerenciamento de dados, o design e o treinamento de redes neurais em sistemas de GPU e o monitoramento do desempenho em tempo real com visualização avançada.

Os dígitos estão disponíveis como um serviço chamado *dígitos*. Inicie o serviço e navegue até http://localhost:5000 para começar.

Os dígitos também são instalados como um módulo python no ambiente raiz do Conda.

### <a name="tensorflow"></a>TensorFlow

TensorFlow é a biblioteca de aprendizado profundo do Google. É uma biblioteca de software livre para computação numérica usando grafos de fluxo de dados. O TensorFlow está disponível no ambiente py35 Python e alguns blocos de anotações de exemplo estão incluídos no JupyterHub.

### <a name="theano"></a>Theano

Theano é uma biblioteca do Python para computação numérica eficiente. Ele está disponível nos ambientes raiz e py35 Python. 

### <a name="torch"></a>Torch

O Torch é uma estrutura de computação científica com amplo suporte para algoritmos de aprendizado de máquina. Ele está disponível em/dsvm/Tools/Torch e a sessão interativa **th** e o Gerenciador de pacotes LuaRocks estão disponíveis na linha de comando. Os exemplos estão disponíveis em/dsvm/Samples/Torch.

O PyTorch também está disponível no ambiente Anaconda raiz. Os exemplos estão em/dsvm/Samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

R é uma das linguagens mais populares para análise de dados e aprendizado de máquina. Se você quiser usar o R para sua análise, a VM terá Microsoft Machine Learning Server com a biblioteca de kernel Open e Math do Microsoft R. A biblioteca de kernel matemática otimiza as operações matemáticas comuns em algoritmos analíticos. O Microsoft R Open é de 100% compatível com o CRAN R, e qualquer uma das bibliotecas de R publicadas no CRAN pode ser instalada no Microsoft R Open. 

Machine Learning Server oferece dimensionamento e operacionalização de modelos de R em serviços Web. Você pode editar seus programas do R em um dos editores padrão, como RStudio, vi ou Emacs. Se você preferir usar o editor Emacs, ele foi pré-instalado. O pacote Emacs ESS (estatísticas) simplifica o trabalho com arquivos R no editor Emacs.

Para abrir o console do R, insira **R** no Shell. Esse comando leva você a um ambiente interativo. Para desenvolver seu programa R, você normalmente usa um editor como Emacs ou vi e, em seguida, executa os scripts no R. Com o RStudio, você tem um IDE gráfico completo para desenvolver seu programa R.

Também há um script R para instalar os [20 principais pacotes de r](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) , se desejar. Você pode executar esse script depois de estar na interface interativa do R. Conforme mencionado anteriormente, você pode abrir essa interface inserindo **R** no Shell.  

## <a name="python"></a>Python

O Anaconda Python é instalado com ambientes Python 2,7 e 3,5. O ambiente 2,7 é chamado de _raiz_e o ambiente 3,5 é chamado de _py35_. Essa distribuição contém o Python base junto com cerca de 300 dos pacotes de matemática, engenharia e análise de dados mais populares.

O ambiente py35 é o padrão. Para ativar o ambiente raiz (2,7), use este comando:

```bash
source activate root
```

Para ativar o ambiente py35 novamente, use este comando:

```bash
source activate py35
```

Para invocar uma sessão interativa do Python, insira **Python** no Shell. 

Instale bibliotecas Python adicionais usando Conda ou Pip. Para Pip, ative primeiro o ambiente correto se você não quiser o padrão:

```bash
source activate root
pip install <package>
```

Ou especifique o caminho completo para Pip:

```bash
/anaconda/bin/pip install <package>
```

Para Conda, você sempre deve especificar o nome do ambiente (py35 ou root):

```bash
conda install <package> -n py35
```

Se você estiver em uma interface gráfica ou tiver o encaminhamento X11 configurado, você pode inserir **pycharm** para abrir o IDE Python pycharm. Você pode usar os editores de texto padrão. Além disso, você pode usar o Spyder, um IDE do Python que é agrupado com distribuições do Anaconda Python. O Spyder precisa de uma área de trabalho gráfica ou um encaminhamento X11. A área de trabalho gráfica tem um atalho para Spyder.

## <a name="jupyter-notebook"></a>Jupyter Notebook

A distribuição Anaconda também vem com um notebook Jupyter, um ambiente para compartilhar código e análise. O notebook Jupyter é acessado por meio do JupyterHub. Você entra usando seu nome de usuário e senha do Linux local.

O servidor do Jupyter Notebook foi pré-configurado com os kernels do Python 2, Python 3 e R. Use o ícone de área de trabalho **Jupyter Notebook** para abrir o navegador e acessar o servidor de notebook. Se você estiver na VM via SSH ou o cliente X2Go, também poderá acessar o servidor do Jupyter notebook em [https://localhost:8000/](https://localhost:8000/).

> [!NOTE]
> Continue se você receber avisos de certificado.

Você pode acessar o servidor do Jupyter notebook de qualquer host. Insira **https://\<nome DNS da VM ou endereço IP\>: 8000/** .

> [!NOTE]
> A porta 8000 é aberta no firewall por padrão quando a VM é provisionada. 

Nós empacotamos blocos de anotações de exemplo, um em Python e outro em R. Você pode ver o link para os exemplos no bloco de anotações home page depois de se autenticar no notebook Jupyter usando seu nome de usuário e senha do Linux local. Você pode criar um novo bloco de anotações selecionando **novo**e, em seguida, selecionando o kernel de idioma apropriado. Se você não vir o botão **novo** , selecione o ícone de **Jupyter** no canto superior esquerdo para ir para a home page do servidor de notebook.

## <a name="apache-spark-standalone"></a>Apache Spark autônomo

Uma instância autônoma do Apache Spark é pré-instalado no DSVM do Linux para ajudá-lo a desenvolver aplicativos Spark localmente antes de testá-los e implantá-los em clusters grandes. 

Você pode executar programas do PySpark por meio do kernel do Jupyter. Ao abrir o Jupyter, selecione o botão **novo** e você verá uma lista de kernels disponíveis. **Spark-Python** é o kernel PySpark que permite criar aplicativos Spark usando a linguagem Python. Você também pode usar um IDE do Python como PyCharm ou Spyder para criar seu programa Spark. 

Nessa instância autônoma, a pilha do Spark é executada dentro do programa cliente de chamada. Esse recurso torna mais rápido e fácil solucionar problemas, em comparação com o desenvolvimento em um cluster Spark.

O Jupyter fornece um exemplo de notebook PySpark. Você pode encontrá-lo no diretório SparkML sob o diretório base de Jupyter ($HOME/notebooks/SparkML/pySpark). 

Se você estiver programando em R para Spark, poderá usar Microsoft Machine Learning Server, Sparkr ou sparklyr. 

Antes de executar o em um contexto do Spark no Microsoft Machine Learning Server, você precisa fazer uma etapa de configuração única para habilitar um HDFS e uma instância yarn do Hadoop de nó único local. Por padrão, os serviços do Hadoop são instalados, mas desabilitados no DSVM. Para habilitá-lo, você precisa executar os seguintes comandos como raiz na primeira vez:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Você pode interromper os serviços relacionados ao Hadoop quando não precisar deles executando ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```.

O diretório/dsvm/samples/MRS fornece um exemplo que demonstra como desenvolver e testar Microsoft Machine Learning Server em um contexto do Spark remoto (a instância do Spark autônoma no DSVM).

## <a name="ides-and-editors"></a>IDEs e editores

Você tem a opção de vários editores de código, incluindo vi/vim, Emacs, PyCharm, RStudio e IntelliJ. 

PyCharm, RStudio e IntelliJ são editores gráficos. Para usá-los, você precisa estar conectado a uma área de trabalho gráfica. Você os abre usando atalhos de menu de desktop e de aplicativo.

O vim e o Emacs são editores baseados em texto. No Emacs, o pacote complementar do ESS facilita o trabalho com R no editor Emacs. Você pode encontrar mais informações sobre o [site do ESS](https://ess.r-project.org/).

O LaTex é instalado por meio do pacote TeXlive, junto com um pacote de complemento Emacs chamado [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Este pacote simplifica a criação de seus documentos do LaTex no Emacs.  

## <a name="databases"></a>Bases de Dados

### <a name="graphical-sql-client"></a>Cliente SQL gráfico

SQuirrel SQL, um cliente SQL gráfico, pode se conectar a vários bancos de dados (como Microsoft SQL Server e MySQL) e executar consultas SQL. Você pode executar o SQuirrel SQL de uma sessão de área de trabalho gráfica (por meio do cliente X2Go, por exemplo) usando um ícone de área de trabalho. Ou você pode executar o cliente usando o seguinte comando no Shell:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Antes da primeira utilização, configure seus drivers e aliases de banco de dados. Os drivers JDBC estão localizados em/usr/share/java/jdbcdrivers.

Para obter mais informações, consulte [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Ferramentas de linha de comando para acessar Microsoft SQL Server

O pacote de driver ODBC para SQL Server também vem com duas ferramentas de linha de comando:

- **bcp**: a ferramenta bcp copia dados em massa entre uma instância do Microsoft SQL Server e um arquivo de dados em um formato especificado pelo usuário. Você pode usar a ferramenta bcp para importar grandes números de novas linhas para SQL Server tabelas ou para exportar dados de tabelas para arquivos de dados. Para importar dados para uma tabela, você deve usar um arquivo de formato criado para essa tabela. Ou você deve entender a estrutura da tabela e os tipos de dados que são válidos para suas colunas.

  Para obter mais informações, consulte [conectando-se com o bcp](https://msdn.microsoft.com/library/hh568446.aspx).

- **sqlcmd**: você pode inserir instruções TRANSACT-SQL usando a ferramenta sqlcmd. Você também pode inserir os procedimentos do sistema e os arquivos de script no prompt de comando. Essa ferramenta usa o ODBC para executar lotes do Transact-SQL.

  Para obter mais informações, consulte [conectando-se com o sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Há algumas diferenças nessa ferramenta entre as plataformas Linux e Windows. Consulte a documentação para obter detalhes.

### <a name="database-access-libraries"></a>Bibliotecas de acesso ao banco de dados

As bibliotecas estão disponíveis em R e Python para acesso ao banco de dados:

* No R, você pode usar o pacote RODBC ou o pacote dplyr para consultar ou executar instruções SQL no servidor de banco de dados.
* No Python, a biblioteca pyodbc fornece acesso ao banco de dados com ODBC como a camada subjacente.  

## <a name="azure-tools"></a>Ferramentas do Azure

As seguintes ferramentas do Azure estão instaladas na VM:

* **CLI do Azure**: você pode usar a interface de linha de comando no Azure para criar e gerenciar recursos do Azure por meio de comandos do Shell. Para abrir as ferramentas do Azure, insira a **ajuda do Azure**. Para obter mais informações, consulte a [página de documentação do CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Gerenciador de armazenamento do Azure**: Gerenciador de armazenamento do Azure é uma ferramenta gráfica que você pode usar para navegar pelos objetos que você armazenou em sua conta de armazenamento do Azure e para carregar e baixar dados de e para BLOBs do Azure. Você pode acessar Gerenciador de Armazenamento no ícone de atalho da área de trabalho. Você também pode abri-lo em um prompt de shell digitando **StorageExplorer**. Você deve estar conectado de um cliente X2Go ou ter a configuração de encaminhamento X11.
* **Bibliotecas do Azure**: Veja a seguir algumas das bibliotecas pré-instaladas.
  
  * **Python**: as bibliotecas relacionadas ao Azure no Python são *Azure*, *azureml*, *pydocumentdb*e *pyodbc*. Com as três primeiras bibliotecas, você pode acessar os serviços de armazenamento do Azure, Azure Machine Learning e Azure Cosmos DB (um banco de dados NoSQL no Azure). A quarta biblioteca, pyodbc (juntamente com o Microsoft ODBC driver for SQL Server), permite o acesso ao SQL Server, ao banco de dados SQL do Azure e ao Azure SQL Data Warehouse do Python usando uma interface ODBC. Insira **lista de Pip** para ver todas as bibliotecas listadas. Certifique-se de executar esse comando nos ambientes Python 2,7 e 3,5.
  * **R**: as bibliotecas relacionadas ao Azure em R são AZUREML e RODBC.
  * **Java**: a lista de bibliotecas Java do Azure pode ser encontrada no diretório/DSVM/SDK/AZURESDKJAVA na VM. As bibliotecas principais são APIs de armazenamento e gerenciamento do Azure, Azure Cosmos DB e drivers JDBC para SQL Server.  

Você pode acessar o [portal do Azure](https://portal.azure.com) do navegador Firefox pré-instalado. No portal do Azure, você pode criar, gerenciar e monitorar recursos do Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning é um serviço de nuvem totalmente gerenciado que permite criar, implantar e compartilhar soluções de análise preditiva. Você pode criar seus experimentos e modelos de Azure Machine Learning Studio (clássico). Você pode acessá-lo em um navegador da Web no Máquina Virtual de Ciência de Dados visitando [Microsoft Azure Machine Learning](https://studio.azureml.net).

Depois de entrar no Azure Machine Learning Studio (clássico), você pode usar uma tela de experimentação para criar um fluxo lógico para os algoritmos de aprendizado de máquina. Você também tem acesso a um Jupyter notebook que está hospedado no Azure Machine Learning e pode trabalhar de forma direta com os experimentos em Azure Machine Learning Studio (clássico). 

Operacionalize os modelos de aprendizado de máquina que você criou encapsulando-os em uma interface de serviço Web. A operacionalização dos modelos de aprendizado de máquina permite que os clientes escritos em qualquer linguagem invoquem previsões desses modelos. Para obter mais informações, consulte a [documentação do Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Você também pode criar seus modelos em R ou Python na VM e, em seguida, implantá-los em produção no Azure Machine Learning. Instalamos bibliotecas em R (**azureml**) e Python (**azureml**) para habilitar essa funcionalidade.

Para obter informações sobre como implantar modelos em R e Python no Azure Machine Learning, consulte [dez coisas que você pode fazer no máquina virtual de ciência de dados](vm-do-ten-things.md).

> [!NOTE]
> Essas instruções foram escritas para a versão do Windows do Máquina Virtual de Ciência de Dados. Mas as informações fornecidas nessa implantação de modelos para Azure Machine Learning são aplicáveis à VM do Linux.

## <a name="machine-learning-tools"></a>Ferramentas de Machine Learning

A VM vem com ferramentas e algoritmos de aprendizado de máquina que foram pré-compilados e pré-instalados localmente. As regiões incluem:

* **Vowpal Wabbit**: um algoritmo de aprendizado online rápido.
* **xgboost**: uma ferramenta que fornece algoritmos de árvore aprimorados e otimizados.
* **Rattle**: uma ferramenta gráfica baseada em R para facilitar a exploração e a modelagem de dados.
* **Python**: Anaconda Python vem em conjunto com algoritmos de aprendizado de máquina com bibliotecas como Scikit-learn. Você pode instalar outras bibliotecas usando o comando `pip install`.
* **LightGBM**: um gradiente rápido, distribuído e de alto desempenho que aumenta a estrutura com base em algoritmos de árvore de decisão.
* **R**: uma biblioteca avançada de funções de aprendizado de máquina está disponível para R. as bibliotecas pré-instaladas incluem LM, GLM, randomForest e rpart. Você pode instalar outras bibliotecas executando este comando:
  
        install.packages(<lib name>)

Aqui estão algumas informações adicionais sobre as três primeiras ferramentas de aprendizado de máquina na lista.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit é um sistema de aprendizado de máquina que usa técnicas como online, hashing, onreduz, reduções, learning2search, ativo e aprendizado interativo.

Para executar a ferramenta em um exemplo básico, use os seguintes comandos:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Há outras demonstrações maiores nesse diretório. Para obter mais informações sobre o Vowpal Wabbit, consulte [esta seção do GitHub](https://github.com/JohnLangford/vowpal_wabbit) e o [wiki do Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>xgboost

A biblioteca xgboost é projetada e otimizada para algoritmos aumentados (árvore). O objetivo dessa biblioteca é enviar por push os limites de computação de máquinas para os extremos necessários para fornecer aumento de árvore em larga escala que é escalonável, portátil e preciso.

Ele é fornecido como uma linha de comando e uma biblioteca do R. Para usar essa biblioteca em R, você pode iniciar uma sessão interativa do R (inserindo **R** no Shell) e carregar a biblioteca.

Veja um exemplo simples que pode ser executado em um prompt do R:

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

Para executar a linha de comando xgboost, aqui estão os comandos a serem executados no Shell:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Um arquivo. Model é gravado no diretório especificado. Você pode encontrar informações sobre este exemplo [de demonstração no GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Para obter mais informações sobre o xgboost, consulte a [página de documentação do xgboost](https://xgboost.readthedocs.org/en/latest/) e seu repositório do [GitHub](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle (o **R** **a**njunto **t**ool **t**o **L**ganhe **e**Earn) usa modelagem e exploração de dados baseados em GUI. Ele apresenta resumos estatísticos e visuais de dados, transforma os dados que podem ser modelados prontamente, compila modelos não supervisionados e supervisionados dos dados, apresenta o desempenho dos modelos graficamente e pontua novos conjuntos de dados. Ele também gera código R, replicando as operações na interface do usuário que podem ser executadas diretamente no R ou usadas como um ponto de partida para análise posterior.

Para executar o Rattle, você precisa estar em uma sessão de entrada de área de trabalho gráfica. No terminal, insira **R** para abrir o ambiente do r. No prompt do R, digite os seguintes comandos:

```R
library(rattle)
rattle()
```

Agora, uma interface gráfica é aberta com um conjunto de guias. Use as etapas de início rápido a seguir no Rattle para usar um conjunto de dados meteorológico de exemplo e criar um modelo. Em algumas das etapas, você será solicitado a instalar e carregar automaticamente alguns pacotes de R necessários que ainda não estão no sistema.

> [!NOTE]
> Se você não tiver acesso para instalar o pacote no diretório do sistema (o padrão), você poderá ver um prompt na janela do console do R para instalar pacotes em sua biblioteca pessoal. Responda **y** se você vir esses prompts.

1. Selecione **Executar**.
1. Uma caixa de diálogo é exibida, perguntando se você deseja usar o conjunto de dados meteorológicos de exemplo. Selecione **Sim** para carregar o exemplo.
1. Selecione a guia **modelo** .
1. Selecione **executar** para criar uma árvore de decisão.
1. Selecione **desenhar** para exibir a árvore de decisão.
1. Selecione a opção de **floresta** e selecione **executar** para criar uma floresta aleatória.
1. Selecione a guia **avaliar** .
1. Selecione a opção **risco** e selecione **executar** para exibir duas plotagens de desempenho **de risco (cumulativo)** .
1. Selecione a guia **log** para mostrar o código R gerado para as operações anteriores.
   (Devido a um bug na versão atual do Rattle, você precisa inserir um caractere **#** na frente de **Exportar esse log** no texto do log.)
1. Selecione o botão **Exportar** para salvar o arquivo de script R chamado *weather_script. R* para a pasta base.

Você pode sair do Rattle e do R. Agora você pode modificar o script R gerado. Ou então, use o script como ele está e execute-o a qualquer momento para repetir tudo o que foi feito na interface do usuário do amRattle. Especialmente para iniciantes em R, essa é uma maneira de fazer análises e aprendizado de máquina rapidamente em uma interface gráfica simples, ao mesmo tempo em que gera automaticamente código em R para modificar ou aprender.

## <a name="next-steps"></a>Passos seguintes

Tem mais dúvidas? Considere criar um [tíquete de suporte](https://azure.microsoft.com/support/create-ticket/).