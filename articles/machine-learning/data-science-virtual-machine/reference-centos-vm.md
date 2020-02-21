---
title: 'Referência: Máquina Virtual de Ciência de Dados CentOS'
titleSuffix: Azure Data Science Virtual Machine
description: Detalhes sobre ferramentas incluídas na Máquina Virtual de Ciência de Dados CentOS
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: db49a9f5e0e6675d93cb58d6af9c92fac21e8b74
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525843"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Referência: CentOS (Linux) Data Science Virtual Machine

A Máquina Virtual linux Data Science (DSVM) é uma máquina virtual Azure baseada em CentOS. O Linux DSVM vem com uma coleção de ferramentas pré-instaladas que pode utilizar para análise de dados e aprendizagem automática. 

Os principais componentes do software incluídos num DSVM Linux são:

* Sistema operativo de distribuição Linux CentOS.
* Microsoft Machine Learning Server.
* Distribuição anaconda Python (versões 3.5 e 2.7), incluindo bibliotecas populares de análise de dados.
* JuliaPro, uma distribuição com curadoria da língua julia e bibliotecas populares de análise científica e de dados.
* Instância autónoma de faísca e Hadoop de nó único (HDFS, Arn).
* JupyterHub, um servidor de caderno jupyter multisuser que suporta Núcleos R, Python, PySpark e Julia.
* Explorador de Armazenamento Azure.
* Azure CLI, a interface de linha de comando Azure para gestão de recursos Azure.
* Base de dados PostgresSQL.
* Ferramentas de aprendizagem automática:
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), um kit de ferramentas de software de aprendizagem profunda da Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), um sistema de aprendizagem automática rápida que suporta técnicas como online, hashing, allreduce, reduções, learning2search, ative e interactive learning.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), uma ferramenta que fornece implementação rápida e precisa de árvores reforçadas.
  * [Rattle](https://togaware.com/rattle/), uma ferramenta que facilita o arranque com análise de dados e machine learning em R. O Rattle oferece tanto a exploração e modelação de dados baseados em GUI utilizando a geração automática de códigoS R.
* Azure SDK em Java, Python, Node.js, Ruby e PHP.
* Bibliotecas em R e Python para utilizar em Azure Machine Learning e outros serviços Azure.
* Ferramentas e editores de desenvolvimento (RStudio, PyCharm, IntelliJ, Emacs, gedit, vi).

Ciência de dados envolve a iteração numa seqüência de tarefas:

1. Localizar, carregar e pré-processar dados.
1. Criar e testar modelos.
1. Implemente os modelos para consumo em aplicações inteligentes.

Os cientistas de dados usar várias ferramentas para concluir estas tarefas. Pode ser demorado encontrar as versões corretas do software e, em seguida, descarregar, compilar e instalar o software.

O Linux DSVM pode aliviar substancialmente este fardo. Use o Linux DSVM para iniciar o seu projeto de análise. O Linux DSVM ajuda-o a trabalhar em tarefas em várias línguas, C++incluindo R, Python, SQL, Java e . O Eclipse fornece um IDE fácil de usar para desenvolver e testar o seu código. O Azure SDK, incluído no DSVM, ajuda-o a construir as suas aplicações utilizando vários serviços no Linux para a plataforma cloud da Microsoft. Outras línguas são pré-instaladas, incluindo Ruby, Perl, PHP e Node.js.

Não há taxas de software para a imagem DSVM. Paga apenas as taxas de utilização de hardware Azure que são avaliadas com base no tamanho da máquina virtual que você fornecer com a imagem DSVM. Para obter mais informações sobre as taxas de cálculo, consulte a lista geminada da [Data Science Virtual Machine for Linux (CentOS)](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) no Azure Marketplace.


## <a name="machine-learning-server"></a>Servidor de aprendizagem automática

R é uma das linguagens mais populares para análise de dados e o machine learning. Se quiser utilizar R para a sua análise, o DSVM tem o Servidor de Aprendizagem Automática com a Microsoft R Open e a Math Kernel Library. A Biblioteca Math Kernel otimiza operações matemáticas comuns em algoritmos analíticos. R Open é totalmente compatível com CRAN R. Qualquer uma das bibliotecas R publicadas em CRAN pode ser instalada no R Open. 

Pode utilizar o Machine Learning Server para escalar e operacionalizar os modelos R em serviços web. Pode editar os seus programas de R em um dos editores de padrão, como o RStudio, vi ou Emacs. O editor da Emacs está pré-instalado no DSVM. O pacote Emacs ESS (Emacs Speaks Statistics) simplifica o trabalho com ficheiros R no editor da Emacs.

Para abrir a consola R, na concha, introduza **R**. Este comando leva-o a um ambiente interativo. Para desenvolver o seu programa R, você normalmente usa um editor como Emacs ou vi, e depois executa os scripts em R. RStudio oferece um IDE gráfico completo para desenvolver o seu programa R.

Um script R que pode utilizar para instalar os [20 melhores pacotes R](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) está incluído no DSVM. Pode executar este guião quando estiver na interface interativa R. Como mencionado anteriormente, para abrir essa interface, na concha, **entrar**r .  

## <a name="python"></a>Python

A Naconda Python está instalada com os ambientes Python 3.5 e 2.7. O ambiente 2.7 chama-se _raiz_ e o ambiente 3.5 chama-se _py35_. Essa distribuição contém o Python base juntamente com aproximadamente 300 dos pacotes de análises de dados, engenharia e matemática mais populares.

O ambiente de py35 é a predefinição. Para ativar o ambiente raiz (2.7), utilize este comando:

```bash
source activate root
```

Para voltar a ativar o ambiente py35, utilize este comando:

```bash
source activate py35
```

Para invocar uma sessão interativa python, na concha, entre **python.** 

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

## <a name="jupyter-notebook"></a>Bloco de Notas do Jupyter

A distribuição da Anaconda também vem com um Caderno Jupyter, um ambiente para partilhar código e análise. Aceda ao Caderno Jupyter através do JupyterHub. Inscreveu-se usando o seu nome de utilizador e senha linux locais.

O servidor Jupyter Notebook é pré-configurado com python 2, Python 3 e núcleos R. Utilize o ícone de ambiente de trabalho **jupyter Notebook** para abrir o navegador e aceder ao servidor Jupyter Notebook. Se aceder ao DSVM via SSH ou ao cliente X2Go, também pode aceder ao servidor Jupyter Notebook em https:\//localhost:8000/.

> [!NOTE]
> Continue se obter quaisquer avisos de certificado.

Pode acessar o servidor de bloco de notas do Jupyter a partir de qualquer anfitrião. **Insira https:\//\<nome dNS dNS ou endereço IP\>:8000/** .

> [!NOTE]
> A porta 8000 é aberta na firewall por padrão quando o DSVM é provisionado. 

A Microsoft tem cadernos de amostras embalados, um em Python e outro em R. Pode ver o link para as amostras na página inicial do Jupyter Notebook depois de autenticar o Caderno Jupyter utilizando o seu nome de utilizador e senha linux locais. Para criar um novo caderno, selecione **New**, e, em seguida, selecione o núcleo de linguagem que pretende utilizar. Se não vir o botão **Novo,** selecione o ícone **Jupyter** na parte superior esquerda para ir à página inicial do servidor de cadernos.

## <a name="spark-standalone"></a>Faísca Autónoma 

Uma instância do modo Spark Autónomo está pré-instalada no DSVM linux para ajudá-lo a desenvolver aplicações Spark localmente antes de testá-las e implantá-las em grandes clusters. 

Pode executar programas PySpark por meio do kernel do Jupyter. Quando abrir o Jupyter, selecione **o** novo botão e deverá ver uma lista de núcleos disponíveis. **Spark - Python** é o núcleo PySpark que permite construir aplicações Spark usando a linguagem Python. Você também pode usar um Python IDE como PyCharm ou Spyder para construir o seu programa Spark. 

Neste caso autónomo, a pilha de faíscas corre no programa de clientes chamados. Esta funcionalidade torna mais rápido e fácil resolver problemas em comparação com o desenvolvimento de um cluster Spark.

Jupyter fornece um caderno PySpark. Pode encontrá-lo no diretório SparkML sob o diretório inicial de Jupyter ($HOME/cadernos/SparkML/pySpark). 

Se estiver a programar em R para Spark, pode utilizar o Machine Learning Server, o SparkR ou o Sparklyr. 

Antes de correr num contexto de Spark no Machine Learning Server, deve fazer um passo de configuração único para permitir um nó único local Hadoop HDFS e yARN. Por predefinição, os serviços do Hadoop são instalados mas desativados na DSVM. Para ativar os serviços Hadoop, execute os seguintes comandos como raiz da primeira vez:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Pode parar os serviços relacionados com Hadoop quando não precisa deles executando `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`.

O diretório /dsvm/samples/MRS fornece uma amostra que demonstra como desenvolver e testar o Servidor de Aprendizagem automática num contexto remoto de Spark (a instância de Faísca autónoma no DSVM).

## <a name="ides-and-editors"></a>IDEs e editores

Pode escolher entre vários editores de código, incluindo vi/VIM, Emacs, gedit, PyCharm, RStudio, Eclipse, LaTeX e IntelliJ. 

* gedit, Eclipse, IntelliJ, R Studio e PyCharm são editores gráficos. Para usá-los, tem de ser inscrito num ambiente de trabalho gráfico. Abre-as utilizando atalhos de menu de desktop e aplicação.

* Vim e Emacs são editores baseados em texto. No Emacs, o pacote de complemento ESS facilita o trabalho com r no editor da Emacs. Pode encontrar mais informações no site da [ESS.](https://ess.r-project.org/)

* Eclipse é um IDE extensível de código aberto que suporta várias línguas. Eclipse IDE para Desenvolvedores java é a versão instalada no DSVM. Pode instalar plug-ins para várias línguas populares para alargar o ambiente. 

  O Kit de Ferramentas Azure para plug-in Eclipse está instalado com eclipse no DSVM. Você pode usar o Kit de Ferramentas Azure para eclipse para criar, desenvolver, testar e implementar aplicações Azure usando o ambiente de desenvolvimento eclipse que suporta línguas como Java.

  O Azure SDK para Java também está instalado com o Kit de Ferramentas Azure para Eclipse no DSVM. O Azure SDK para Java dá-lhe acesso a diferentes serviços Azure de dentro de um ambiente Java. 
  
  Para mais informações, consulte [o Kit de Ferramentas Azure para Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* O LaTeX está instalado através do pacote texlive, juntamente com um pacote de add-on Emacs chamado [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Este pacote simplifica a autoria dos seus documentos LaTeX em Emacs. 

## <a name="databases"></a>Bases de Dados

O Linux DSVM dá-lhe acesso a várias ferramentas de base de dados e linha de comando.

### <a name="postgressql"></a>PostgresSQL

A base de dados de código aberto PostgresSQL está disponível no DSVM, com serviços em execução e initdb concluídos. Tem de criar bases de dados e utilizadores. Para mais informações, consulte a [documentação PostgresSQL](https://www.postgresql.org/docs/).  

### <a name="squirrel-sql"></a>SQuirreL SQL

O SQuirreL SQL é um cliente SQL gráfico que pode ligar-se a várias bases de dados (incluindo O Servidor SQL, PostgresSQL e MySQL) e executar consultas SQL. Pode executar o SQuirreL SQL a partir de uma sessão gráfica de ambiente de trabalho (através do cliente X2Go, por exemplo) utilizando um ícone de ambiente de trabalho. Ou pode executar o cliente usando o seguinte comando na concha:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Antes da primeira utilização, configurar os controladores e aliases de base de dados. Os condutores da JDBC estão localizados em /usr/share/java/jdbcdrivers.

Para mais informações, consulte [SQuirreL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-sql-server"></a>Ferramentas de linha de comando para aceder ao Servidor SQL

O pacote de controladores ODBC para o SQL Server também vem com duas ferramentas de linha de comandos:

* **bcp**: A ferramenta BCP copia dados em massa entre uma instância do SQL Server e um ficheiro de dados num formato especificado pelo utilizador. Pode utilizar a ferramenta bcp para importar um grande número de novas linhas em tabelas do SQL Server ou para exportar dados de tabelas para ficheiros de dados. Para importar dados numa tabela, deve utilizar um ficheiro de formato criado para essa tabela. Ou, deve compreender a estrutura da tabela e os tipos de dados que são válidos para as suas colunas.

  Para mais informações, consulte [Connect com o BCP.](https://msdn.microsoft.com/library/hh568446.aspx)

* **sqlcmd:** Pode utilizar o utilitário sqlcmd para introduzir declarações Transact-SQL, procedimentos do sistema e ficheiros de script no pedido de comando. O utilitário sqlcmd usa a ODBC para executar lotes Transact-SQL.

  Para mais informações, consulte [Connect com sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Existem algumas diferenças nesta ferramenta entre as plataformas Linux e Windows. Consulte a documentação para obter detalhes.

### <a name="database-access-libraries"></a>Bibliotecas de acesso de base de dados

As bibliotecas para acesso à base de dados estão disponíveis em R e Python:

* Em R, pode utilizar o pacote RODBC ou o pacote dplyr para consultar ou executar declarações SQL no servidor de base de dados.
* Em Python, a biblioteca pyodbc fornece acesso à base de dados com a ODBC como camada subjacente.

## <a name="azure-tools"></a>Ferramentas do Azure

As seguintes ferramentas Azure são instaladas no DSVM:

* **Azure CLI:** Pode utilizar a interface de linha de comando em Azure para criar e gerir os recursos azure através de comandos de conchas. Para abrir as ferramentas Azure, insira **a ajuda azure.** Para mais informações, consulte a página de [documentação do Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: Azure Storage Explorer é uma ferramenta gráfica que pode usar para navegar através dos objetos que guardou na sua conta de armazenamento Azure, e para carregar e transferir dados de e para as bolhas Azure. Pode acessar o Explorador de armazenamento do ícone de atalho de desktop. Também pode abri-lo a partir de um pedido de concha, entrando no **StorageExplorer**. Você deve ser contratado por um cliente X2Go ou ter o encaminhamento X11 configurado.
* **Bibliotecas Azure**: As seguintes bibliotecas são pré-instaladas no DSVM:
  
  * **Python**: As bibliotecas relacionadas com o Azure em Python são *azure,* *azureml,* *pydocumentdb*e *pyodbc*. Com as três primeiras bibliotecas, pode aceder a serviços de armazenamento do Azure, Azure Machine Learning e Azure Cosmos DB (uma base de dados NoSQL no Azure). A biblioteca de quarta, pyodbc (juntamente com o Microsoft ODBC driver para SQL Server), permite o acesso ao SQL Server, SQL Database do Azure e Azure SQL Data Warehouse do Python, utilizando uma interface ODBC. Insira a lista de **pips** para ver todas as bibliotecas listadas. Certifique-se de que execute este comando no Python 2.7 e 3,5 ambientes.
  * **R**: As bibliotecas relacionadas com o Azure em R são AzureML e RODBC.
  * **Java**: A lista das bibliotecas Azure Java pode ser encontrada no diretório /dsvm/sdk/AzureSDKJava no DSVM. As bibliotecas de chave são do Azure armazenamento e gestão de APIs, do Azure Cosmos DB, JDBC controladores e para o SQL Server.  

Pode aceder ao [portal Azure](https://portal.azure.com) a partir do navegador Firefox pré-instalado. No portal Azure, pode criar, gerir e monitorizar os recursos do Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço de nuvem totalmente gerido que pode usar para construir, implementar e partilhar soluções de análise preditivas. Constrói as suas experiências e modelos a partir do Azure Machine Learning Studio (clássico). Para aceder ao Azure Machine Learning a partir de um navegador web no DSVM, vá ao [Microsoft Azure Machine Learning](https://studio.azureml.net).

Depois de iniciar sessão no Azure Machine Learning Studio (clássico), pode usar uma tela de experimentação para construir um fluxo lógico para os algoritmos de aprendizagem automática. Você também tem acesso a um Caderno Jupyter que está hospedado em Azure Machine Learning. O caderno pode funcionar perfeitamente com as experiências no Azure Machine Learning Studio (clássico). 

Operacionalize os modelos de aprendizagem automática que constrói envolvendo-os numa interface de serviço web. A operacionalização de modelos de aprendizagem automática permite que os clientes escritos em qualquer idioma invoquem previsões desses modelos. Para mais informações, consulte a documentação do [Machine Learning.](https://azure.microsoft.com/documentation/services/machine-learning/)

Também pode construir os seus modelos em R ou Python no DSVM e, em seguida, implantá-los em produção em Azure Machine Learning. A Microsoft instalou bibliotecas em R (**AzureML)** e Python **(azureml)** para suportar esta funcionalidade.

Para obter informações sobre como implementar modelos em R e Python para Azure Machine Learning, veja [dez coisas que pode fazer na Máquina Virtual](vm-do-ten-things.md)da Ciência dos Dados .

> [!NOTE]
> As instruções em [dez coisas que pode fazer na Máquina Virtual](vm-do-ten-things.md) da Ciência dos Dados foram escritas para a versão Windows do DSVM. No entanto, a informação sobre a implementação de modelos para o Azure Machine Learning também se aplica ao Linux DSVM.

## <a name="machine-learning-tools"></a>Ferramentas de aprendizagem de máquina

O DSVM vem com algumas ferramentas de aprendizagem automática e algoritmos que são pré-compilados e pré-instalados localmente. Estas incluem:

* **Microsoft Cognitive Toolkit**: Um kit de ferramentas de aprendizagem profunda.
* **Vowpal Wabbit**: Um algoritmo de aprendizagem online rápido.
* **XGBoost**: Uma ferramenta que fornece algoritmos de árvores otimizados e impulsionados.
* **Python**: Anaconda Python vem agregada com algoritmos de aprendizagem automática com bibliotecas como scikit-learn. Pode instalar outras bibliotecas utilizando o comando `pip install`.
* **R**: Uma rica biblioteca de funções de machine learning está disponível para R. Bibliotecas pré-instaladas incluem Lm, glm, randomForest e rpart. Pode instalar outras bibliotecas executando `install.packages(<lib name>)`.

Microsoft Cognitive Toolkit, Vowpal Wabbit e XGBoost são discutidos com mais detalhes nas próximas secções.

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

O Microsoft Cognitive Toolkit é um conjunto de ferramentas de aprendizagem profunda de código aberto. É uma ferramenta de linha de comando (CNTK) e já está no PATH.

Para executar uma amostra básica, executar os seguintes comandos na concha:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Para mais informações, consulte o [repositório GitHub CNTK](https://github.com/Microsoft/CNTK) e o [wiki CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

A Vowpal Wabbit é um sistema de machine learning que utiliza técnicas como online, hashing, allreduce, reduções, learning2search, ative e interactive learning.

Para executar a ferramenta num exemplo básico, executar os seguintes comandos:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

O diretório de demonstração da Vowpal Wabbit inclui outras demonstrações maiores. Para mais informações sobre a Vowpal Wabbit, consulte o [repositório GitHub Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) e o [Wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>XGBoost

A biblioteca XGBoost é projetada e otimizada para algoritmos (árvore) impulsionados. O objetivo da biblioteca XGBoost é empurrar os limites de computação das máquinas para os extremos necessários para fornecer um aumento de árvores em larga escala que é escalável, portátil e preciso.

O XGBoost é fornecido como uma linha de comando e como uma biblioteca R.

Para utilizar a biblioteca XGBoost em R, inicie uma sessão r interativa (na concha, introduza **R)** e, em seguida, carregue a biblioteca.

Aqui está um exemplo simples que pode executar no pedido R:

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

Para executar a linha de comando XGBoost, execute estes comandos na concha:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Um ficheiro .modelo é escrito para o diretório especificado. Para obter informações sobre este exemplo de demonstração no GitHub, consulte [classificação binária](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Para mais informações sobre o XGBoost, consulte a [documentação XGBoost](https://xgboost.readthedocs.org/en/latest/) e o [repositório XGBoost GitHub](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle (*R* *A*nalytical *T*ool *T*o *L*earn *E*asily) usa a exploração e modelação de dados baseados em GUI. Chocalho:
- Apresenta resumos estatísticos e visuais de dados.
- Transforma dados que podem ser facilmente modelados.
- Constrói modelos não supervisionados e supervisionados a partir de dados.
- Apresenta o desempenho dos modelos graficamente.
- Marca novos conjuntos de dados.
- Gera código R.
- Replica operações na UI que podem ser executadas diretamente em R ou usadas como ponto de partida para mais análises.

Para executar o Rattle, deve sessão gráfica. Num terminal, introduza **R** para abrir o ambiente R. No prompt de R, introduza os seguintes comandos:

```R
library(rattle)
rattle()
```

Uma interface gráfica que tem um conjunto de separadores abre. Utilize os seguintes passos de arranque rápido em Rattle para utilizar um conjunto de dados meteorológicos de amostra e construir um modelo. Em alguns dos passos, é-lhe pedido que instale e carregue automaticamente alguns pacotes R necessários que ainda não estão no sistema.

> [!NOTE]
> Se não tiver permissão para instalar o pacote no diretório do sistema (o padrão), poderá ver uma solicitação na janela da consola R para instalar pacotes na sua biblioteca pessoal. Se vir estas indicações, entre **y**.

1. Selecione **Executar**.
1. Uma caixa de diálogo leva-o a carregar o conjunto de dados meteorológicos exemplo. Selecione **Sim** para carregar o exemplo.
1. Selecione o separador **Modelo.**
1. Selecione **Executar** para construir uma árvore de decisão.
1. Selecione **Draw** para exibir a árvore de decisão.
1. Selecione a opção **Floresta** e, em seguida, selecione **Executar** para construir uma floresta aleatória.
1. Selecione o separador **Avaliar.**
1. Selecione a opção **Risco** e, em seguida, selecione **Executar** para exibir dois lotes de desempenho **de risco (cumulativo).**
1. Selecione o separador **Log** para mostrar o código R gerado para as operações anteriores. (Devido a um bug na versão atual do Rattle, deve inserir um **#** carácter em frente à **Exportação deste registo** no texto do registo.)
1. Selecione o botão **Export** para guardar o ficheiro r denominado *weather_script. R* para a pasta inicial.

Pode sair de Rattle e R. Agora pode modificar o script R gerado. Ou, use o guião como está, e execute-o a qualquer momento para repetir tudo o que foi feito na UI rattle. Especialmente para principiantes em R, esta é uma forma de fazer rapidamente análise e machine learning em uma interface gráfica simples, enquanto gera automaticamente código em R para modificar ou para aprender.

## <a name="next-steps"></a>Passos seguintes

Tem perguntas adicionais? Considere criar um bilhete de [apoio.](https://azure.microsoft.com/support/create-ticket/)