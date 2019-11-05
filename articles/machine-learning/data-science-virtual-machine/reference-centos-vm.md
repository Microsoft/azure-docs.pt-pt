---
title: 'Referência: CentOS DSVM'
description: Detalhes sobre as ferramentas incluídas no Máquina Virtual de Ciência de Dados CentOS
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 0f71a8af2f7d2cfbfe43c0cfcc84cc7c08109c32
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493712"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Referência: CentOS (Linux) Máquina Virtual de Ciência de Dados

O Linux Máquina Virtual de Ciência de Dados (DSVM) é uma máquina virtual do Azure baseada em CentOS. O DSVM do Linux vem com uma coleção de ferramentas preinstaladas que você pode usar para análise de dados e aprendizado de máquina. 

Os principais componentes de software incluídos em um DSVM do Linux são:

* Sistema operacional de distribuição CentOS do Linux.
* Microsoft Machine Learning Server.
* Distribuição do Anaconda Python (versões 3,5 e 2,7), incluindo bibliotecas de análise de dados populares.
* JuliaPro, uma distribuição organizada da linguagem Julia e as bibliotecas de análises de dados e científicas populares.
* Instância autônoma do Spark e Hadoop de nó único (HDFS, YARN).
* JupyterHub, um servidor de notebook Jupyter multiusuário que dá suporte a kernels R, Python, PySpark e Julia.
* Gerenciador de Armazenamento do Azure.
* CLI do Azure, a interface de linha de comando do Azure para gerenciar recursos do Azure.
* Banco de dados PostgresSQL.
* Ferramentas de Machine Learning:
  * [Microsoft cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), um kit de ferramentas de software de aprendizado profundo da Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), um sistema de aprendizado de máquina rápido que dá suporte a técnicas como online, hash, onreduz, reduções, learning2search, ativo e aprendizado interativo.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), uma ferramenta que fornece implementação de árvore aumentada rápida e precisa.
  * [Rattle](https://togaware.com/rattle/), uma ferramenta que facilita a análise de dados e o aprendizado de máquina em R. O Rattle oferece a exploração de dados baseada em GUI e a modelagem usando a geração automática de código R.
* SDK do Azure em Java, Python, Node. js, Ruby e PHP.
* Bibliotecas em R e Python para usar em Azure Machine Learning e outros serviços do Azure.
* Ferramentas e editores de desenvolvimento (RStudio, PyCharm, IntelliJ, Emacs, gedit, vi).

A ciência de dados envolve a iteração em uma sequência de tarefas:

1. Localizar, carregar e pré-processar dados.
1. Criar e testar modelos.
1. Implante os modelos de consumo em aplicativos inteligentes.

Os cientistas de dados usam várias ferramentas para concluir essas tarefas. Pode ser demorado encontrar as versões corretas do software e, em seguida, baixar, compilar e instalar o software.

O DSVM do Linux pode aliviar essa carga substancialmente. Use o DSVM do Linux para iniciar seu projeto de análise. O DSVM do Linux ajuda você a trabalhar em tarefas em várias linguagens, incluindo R, Python, SQL, C++Java e. O Eclipse fornece um IDE fácil de usar para desenvolver e testar seu código. O SDK do Azure, incluído no DSVM, ajuda você a criar seus aplicativos usando vários serviços no Linux para a plataforma de nuvem da Microsoft. Outras linguagens são pré-instalados, incluindo Ruby, Perl, PHP e node. js.

Não há encargos de software para a imagem DSVM. Você paga apenas as tarifas de uso de hardware do Azure que são avaliadas com base no tamanho da máquina virtual que você provisiona com a imagem DSVM. Para obter mais informações sobre as taxas de computação, consulte a [listagem de máquina virtual de ciência de dados para Linux (CentOS)](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) no Azure Marketplace.


## <a name="machine-learning-server"></a>Machine Learning Server

R é uma das linguagens mais populares para análise de dados e aprendizado de máquina. Se você quiser usar o R para sua análise, o DSVM terá Machine Learning Server com a biblioteca de kernel Open e Math do Microsoft R. A biblioteca de kernel de matemática otimiza operações matemáticas comuns em algoritmos analíticos. O r Open é totalmente compatível com o CRAN R. Qualquer uma das bibliotecas de R publicadas no CRAN pode ser instalada em R Open. 

Você pode usar Machine Learning Server para dimensionar e colocar em operação modelos de R em serviços Web. Você pode editar seus programas do R em um dos editores padrão, como RStudio, vi ou Emacs. O editor Emacs é pré-instalado no DSVM. O pacote Emacs ESS (estatísticas) simplifica o trabalho com arquivos R no editor Emacs.

Para abrir o console do R, no Shell, digite **R**. Esse comando leva você a um ambiente interativo. Para desenvolver seu programa R, você normalmente usa um editor como Emacs ou vi e, em seguida, executa os scripts em R. o RStudio oferece um IDE gráfico completo para o desenvolvimento de seu programa R.

Um script R que você pode usar para instalar os [20 pacotes de r principais](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) está incluído no DSVM. Você pode executar esse script quando estiver na interface interativa do R. Conforme mencionado anteriormente, para abrir essa interface, no Shell, digite **R**.  

## <a name="python"></a>Python

O Anaconda Python é instalado com os ambientes Python 3,5 e 2,7. O ambiente 2,7 é chamado de _raiz_ e o ambiente 3,5 é chamado de _py35_. Essa distribuição contém o Python base junto com cerca de 300 dos pacotes de matemática, engenharia e análise de dados mais populares.

O ambiente py35 é o padrão. Para ativar o ambiente raiz (2,7), use este comando:

```bash
source activate root
```

Para ativar o ambiente py35 novamente, use este comando:

```bash
source activate py35
```

Para invocar uma sessão interativa do Python, no Shell, digite **Python**. 

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

## <a name="jupyter-notebook"></a>Bloco de Notas do Jupyter

A distribuição Anaconda também vem com um Jupyter Notebook, um ambiente para compartilhar código e análise. Acesse o Jupyter Notebook por meio do JupyterHub. Você entra usando seu nome de usuário e senha do Linux local.

O servidor Jupyter Notebook é pré-configurado com os kernels Python 2, Python 3 e R. Use o ícone de área de trabalho **Jupyter Notebook** para abrir o navegador e acessar o Jupyter Notebook Server. Se você acessar o DSVM via SSH ou o cliente do X2Go, também poderá acessar o servidor de Jupyter Notebook em https:\//localhost: 8000/.

> [!NOTE]
> Continue se você receber avisos de certificado.

Você pode acessar o servidor do Jupyter notebook de qualquer host. Insira **https:\//\<nome DNS DSVM ou endereço IP\>: 8000/** .

> [!NOTE]
> A porta 8000 é aberta no firewall por padrão quando o DSVM é provisionado. 

A Microsoft empacota os notebooks de exemplo, um em Python e outro em R. Você pode ver o link para os exemplos no home page de Jupyter Notebook depois de autenticar o Jupyter Notebook usando seu nome de usuário e senha do Linux local. Para criar um novo bloco de anotações, selecione **novo**e, em seguida, selecione o kernel de idioma que você deseja usar. Se você não vir o botão **novo** , selecione o ícone de **Jupyter** no canto superior esquerdo para ir para a home page do servidor de notebook.

## <a name="spark-standalone"></a>Spark autônomo 

Uma instância do modo autônomo do Spark é pré-instalado no DSVM do Linux para ajudá-lo a desenvolver aplicativos Spark localmente antes de testá-los e implantá-los em clusters grandes. 

Você pode executar programas do PySpark por meio do kernel do Jupyter. Ao abrir o Jupyter, selecione o botão **novo** e você verá uma lista de kernels disponíveis. **Spark-Python** é o kernel PySpark que permite criar aplicativos Spark usando a linguagem Python. Você também pode usar um IDE do Python como PyCharm ou Spyder para criar seu programa Spark. 

Nessa instância autônoma, a pilha do Spark é executada no programa cliente de chamada. Esse recurso torna mais rápido e fácil solucionar problemas em comparação com o desenvolvimento em um cluster Spark.

O Jupyter fornece um exemplo de notebook PySpark. Você pode encontrá-lo no diretório SparkML sob o diretório base de Jupyter ($HOME/notebooks/SparkML/pySpark). 

Se você estiver programando em R para Spark, poderá usar Machine Learning Server, Sparkr ou sparklyr. 

Antes de executar o em um contexto do Spark no Machine Learning Server, você deve fazer uma etapa de configuração única para habilitar um HDFS e uma instância YARN do Hadoop de nó único local. Por padrão, os serviços do Hadoop são instalados, mas desabilitados no DSVM. Para habilitar os serviços do Hadoop, execute os seguintes comandos como raiz na primeira vez:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Você pode interromper os serviços relacionados ao Hadoop quando não precisar deles executando `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`.

O diretório/dsvm/samples/MRS fornece um exemplo que demonstra como desenvolver e testar Machine Learning Server em um contexto do Spark remoto (a instância do Spark autônoma no DSVM).

## <a name="ides-and-editors"></a>IDEs e editores

Você pode escolher entre vários editores de código, incluindo vi/VIM, Emacs, gedit, PyCharm, RStudio, Eclipse, LaTeX e IntelliJ. 

* gedit, Eclipse, IntelliJ, R Studio e PyCharm são editores gráficos. Para usá-los, você deve estar conectado a uma área de trabalho gráfica. Você os abre usando atalhos de menu de desktop e de aplicativo.

* O vim e o Emacs são editores baseados em texto. No Emacs, o pacote complementar do ESS facilita o trabalho com o R no editor Emacs. Você pode encontrar mais informações sobre o [site do ESS](https://ess.r-project.org/).

* O eclipse é um IDE de código aberto e extensível que dá suporte a vários idiomas. O Eclipse IDE para desenvolvedores de Java é a versão instalada no DSVM. Você pode instalar plug-ins para várias linguagens populares para estender o ambiente. 

  O plug-in Azure Toolkit for Eclipse é instalado com o eclipse no DSVM. Você pode usar Azure Toolkit for Eclipse para criar, desenvolver, testar e implantar aplicativos do Azure usando o ambiente de desenvolvimento do eclipse que oferece suporte a linguagens como Java.

  O SDK do Azure para Java também é instalado com o Azure Toolkit for Eclipse no DSVM. O SDK do Azure para Java fornece acesso a diferentes serviços do Azure de dentro de um ambiente Java. 
  
  Para obter mais informações, consulte [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* O LaTeX é instalado por meio do pacote TeXlive, junto com um pacote de complemento Emacs chamado [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Este pacote simplifica a criação de seus documentos do LaTeX no Emacs. 

## <a name="databases"></a>Bases de Dados

O DSVM do Linux fornece acesso a várias ferramentas de linha de comando e banco de dados.

### <a name="postgressql"></a>PostgresSQL

O banco de dados de software livre PostgresSQL está disponível no DSVM, com serviços em execução e initdb concluído. Você deve criar bancos de dados e usuários. Para obter mais informações, consulte a [documentação do PostgresSQL](https://www.postgresql.org/docs/).  

### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL é um cliente SQL gráfico que pode se conectar a vários bancos de dados (incluindo SQL Server, PostgresSQL e MySQL) e executar consultas SQL. Você pode executar o SQuirreL SQL de uma sessão de área de trabalho gráfica (por meio do cliente X2Go, por exemplo) usando um ícone de área de trabalho. Ou você pode executar o cliente usando o seguinte comando no Shell:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Antes da primeira utilização, configure seus drivers e aliases de banco de dados. Os drivers JDBC estão localizados em/usr/share/java/jdbcdrivers.

Para obter mais informações, consulte [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-sql-server"></a>Ferramentas de linha de comando para acessar SQL Server

O pacote de driver ODBC para SQL Server também vem com duas ferramentas de linha de comando:

* **bcp**: a ferramenta bcp copia dados em massa entre uma instância do SQL Server e um arquivo de dados em um formato especificado pelo usuário. Você pode usar a ferramenta bcp para importar grandes números de novas linhas em SQL Server tabelas ou para exportar dados de tabelas para arquivos de dados. Para importar dados para uma tabela, você deve usar um arquivo de formato criado para essa tabela. Ou você deve entender a estrutura da tabela e os tipos de dados que são válidos para suas colunas.

  Para obter mais informações, consulte [conectar-se com bcp](https://msdn.microsoft.com/library/hh568446.aspx).

* **sqlcmd**: você pode usar o utilitário sqlcmd para inserir instruções TRANSACT-SQL, procedimentos do sistema e arquivos de script no prompt de comando. O utilitário sqlcmd usa o ODBC para executar lotes Transact-SQL.

  Para obter mais informações, consulte [conectar-se com o sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Há algumas diferenças nessa ferramenta entre as plataformas Linux e Windows. Consulte a documentação para obter detalhes.

### <a name="database-access-libraries"></a>Bibliotecas de acesso ao banco de dados

As bibliotecas de acesso ao banco de dados estão disponíveis em R e Python:

* No R, você pode usar o pacote RODBC ou o pacote dplyr para consultar ou executar instruções SQL no servidor de banco de dados.
* No Python, a biblioteca pyodbc fornece acesso ao banco de dados com ODBC como a camada subjacente.

## <a name="azure-tools"></a>Ferramentas do Azure

As seguintes ferramentas do Azure estão instaladas no DSVM:

* **CLI do Azure**: você pode usar a interface de linha de comando no Azure para criar e gerenciar recursos do Azure por meio de comandos do Shell. Para abrir as ferramentas do Azure, insira a **ajuda do Azure**. Para obter mais informações, consulte a [página de documentação do CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Gerenciador de armazenamento do Azure**: Gerenciador de armazenamento do Azure é uma ferramenta gráfica que você pode usar para navegar pelos objetos que você armazenou em sua conta de armazenamento do Azure e para carregar e baixar dados de e para BLOBs do Azure. Você pode acessar Gerenciador de Armazenamento no ícone de atalho da área de trabalho. Você também pode abri-lo em um prompt de shell digitando **StorageExplorer**. Você deve estar conectado de um cliente X2Go ou ter a configuração de encaminhamento X11.
* **Bibliotecas do Azure**: as seguintes bibliotecas são pré-instalados no DSVM:
  
  * **Python**: as bibliotecas relacionadas ao Azure no Python são *Azure*, *azureml*, *pydocumentdb*e *pyodbc*. Com as três primeiras bibliotecas, você pode acessar os serviços de armazenamento do Azure, Azure Machine Learning e Azure Cosmos DB (um banco de dados NoSQL no Azure). A quarta biblioteca, pyodbc (juntamente com o Microsoft ODBC driver for SQL Server), permite o acesso ao SQL Server, ao banco de dados SQL do Azure e ao Azure SQL Data Warehouse do Python usando uma interface ODBC. Insira **lista de Pip** para ver todas as bibliotecas listadas. Certifique-se de executar esse comando nos ambientes Python 2,7 e 3,5.
  * **R**: as bibliotecas relacionadas ao Azure em R são AZUREML e RODBC.
  * **Java**: a lista de bibliotecas Java do Azure pode ser encontrada no diretório/DSVM/SDK/AZURESDKJAVA no dsvm. As bibliotecas principais são APIs de armazenamento e gerenciamento do Azure, Azure Cosmos DB e drivers JDBC para SQL Server.  

Você pode acessar o [portal do Azure](https://portal.azure.com) do navegador Firefox pré-instalado. No portal do Azure, você pode criar, gerenciar e monitorar recursos do Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning é um serviço de nuvem totalmente gerenciado que você pode usar para criar, implantar e compartilhar soluções de análise preditiva. Você cria seus experimentos e modelos a partir do Azure Machine Learning Studio (clássico). Para acessar Azure Machine Learning de um navegador da Web no DSVM, vá para o [Microsoft Azure Machine Learning](https://studio.azureml.net).

Depois de entrar no Azure Machine Learning Studio (clássico), você pode usar uma tela de experimentação para criar um fluxo lógico para os algoritmos de aprendizado de máquina. Você também tem acesso a uma Jupyter Notebook hospedada no Azure Machine Learning. O notebook pode funcionar diretamente com os experimentos em Azure Machine Learning Studio (clássico). 

Operacionalize os modelos de aprendizado de máquina que você cria encapsulando-os em uma interface de serviço Web. A operacionalização dos modelos de aprendizado de máquina permite que os clientes escritos em qualquer linguagem invoquem previsões desses modelos. Para obter mais informações, consulte a [documentação do Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Você também pode criar seus modelos em R ou Python no DSVM e implantá-los em produção em Azure Machine Learning. A Microsoft instalou bibliotecas em R (**azureml**) e Python (**azureml**) para dar suporte a essa funcionalidade.

Para obter informações sobre como implantar modelos em R e Python para Azure Machine Learning, consulte [dez coisas que você pode fazer no máquina virtual de ciência de dados](vm-do-ten-things.md).

> [!NOTE]
> As instruções de [dez coisas que você pode fazer no máquina virtual de ciência de dados](vm-do-ten-things.md) foram escritas para a versão do Windows do DSVM. No entanto, as informações sobre a implantação de modelos para Azure Machine Learning também se aplicam ao DSVM do Linux.

## <a name="machine-learning-tools"></a>Ferramentas de Machine Learning

O DSVM vem com algumas ferramentas e algoritmos de aprendizado de máquina que são pré-compilados e pré-instalados localmente. As regiões incluem:

* **Microsoft cognitive Toolkit**: um kit de ferramentas de aprendizado profundo.
* **Vowpal Wabbit**: um algoritmo de aprendizado online rápido.
* **XGBoost**: uma ferramenta que fornece algoritmos de árvore aprimorados e otimizados.
* **Python**: Anaconda Python vem em conjunto com algoritmos de aprendizado de máquina com bibliotecas como Scikit-learn. Você pode instalar outras bibliotecas usando o comando `pip install`.
* **R**: uma biblioteca avançada de funções de aprendizado de máquina está disponível para R. as bibliotecas pré-instaladas incluem LM, GLM, randomForest e rpart. Você pode instalar outras bibliotecas executando `install.packages(<lib name>)`.

Microsoft Cognitive Toolkit, Vowpal wabbit e XGBoost são discutidos mais detalhadamente nas próximas seções.

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit é um kit de ferramentas de aprendizado profundo de software livre. É uma ferramenta de linha de comando (CNTK) e já está no caminho.

Para executar um exemplo básico, execute os seguintes comandos no Shell:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Para obter mais informações, consulte o [repositório do GITHUB CNTK](https://github.com/Microsoft/CNTK) e o [wiki do CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit é um sistema de aprendizado de máquina que usa técnicas como online, hashing, onreduz, reduções, learning2search, ativo e aprendizado interativo.

Para executar a ferramenta em um exemplo básico, execute os seguintes comandos:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

O diretório de demonstração do Vowpal Wabbit inclui outras demonstrações mais amplas. Para obter mais informações sobre Vowpal Wabbit, consulte o [repositório GitHub Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) e o [wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>XGBoost

A biblioteca XGBoost é projetada e otimizada para algoritmos aumentados (árvore). O objetivo da biblioteca XGBoost é enviar por push os limites de computação de máquinas para os extremos necessários para fornecer aumento de árvore em larga escala que é escalonável, portátil e preciso.

XGBoost é fornecido como uma linha de comando e como uma biblioteca do R.

Para usar a biblioteca XGBoost em R, inicie uma sessão interativa do R (no Shell, digite **R**) e, em seguida, carregue a biblioteca.

Veja um exemplo simples que pode ser executado no prompt do R:

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

Para executar a linha de comando XGBoost, execute estes comandos no Shell:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Um arquivo. Model é gravado no diretório especificado. Para obter informações sobre este exemplo de demonstração no GitHub, consulte [classificação binária](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Para obter mais informações sobre XGBoost, consulte a [documentação do XGBoost](https://xgboost.readthedocs.org/en/latest/) e o [repositório GitHub do XGBoost](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle (*R* *A*njunto *t*ool *t*o *L*ganhe *e*Earn) usa modelagem e exploração de dados baseados em GUI. Rattle
- Apresenta resumos estatísticos e visuais de dados.
- Transforma os dados que podem ser modelados prontamente.
- Compila modelos não supervisionados e supervisionados dos dados.
- Apresenta o desempenho dos modelos graficamente.
- Pontua novos conjuntos de dados.
- Gera o código R.
- Replica operações na interface do usuário que podem ser executadas diretamente no R ou usadas como um ponto de partida para mais análise.

Para executar o Rattle, você deve estar conectado a uma sessão de área de trabalho gráfica. Em um terminal, insira **R** para abrir o ambiente do r. No prompt do R, digite os seguintes comandos:

```R
library(rattle)
rattle()
```

Uma interface gráfica que tem um conjunto de guias é aberta. Use as etapas de início rápido a seguir no Rattle para usar um conjunto de dados meteorológico de exemplo e criar um modelo. Em algumas das etapas, você será solicitado a instalar e carregar automaticamente alguns pacotes de R necessários que ainda não estão no sistema.

> [!NOTE]
> Se você não tiver permissões para instalar o pacote no diretório do sistema (o padrão), você poderá ver um prompt na janela do console do R para instalar pacotes em sua biblioteca pessoal. Se você vir esses prompts, digite **y**.

1. Selecione **Executar**.
1. Uma caixa de diálogo solicita que você carregue o conjunto de dados meteorológicos de exemplo. Selecione **Sim** para carregar o exemplo.
1. Selecione a guia **modelo** .
1. Selecione **executar** para criar uma árvore de decisão.
1. Selecione **desenhar** para exibir a árvore de decisão.
1. Selecione a opção **floresta** e, em seguida, selecione **executar** para criar uma floresta aleatória.
1. Selecione a guia **avaliar** .
1. Selecione a opção **risco** e, em seguida, selecione **executar** para exibir duas plotagens de desempenho **de risco (cumulativo)** .
1. Selecione a guia **log** para mostrar o código R gerado para as operações anteriores. (Devido a um bug na versão atual do Rattle, você deve inserir um caractere **#** na frente de **Exportar esse log** no texto do log.)
1. Selecione o botão **Exportar** para salvar o arquivo de script R chamado *weather_script. R* para a pasta base.

Você pode sair do Rattle e do R. Agora você pode modificar o script R gerado. Ou então, use o script como ele está e execute-o a qualquer momento para repetir tudo o que foi feito na interface do usuário do amRattle. Especialmente para iniciantes em R, essa é uma maneira de fazer análises e aprendizado de máquina rapidamente em uma interface gráfica simples, ao mesmo tempo em que gera automaticamente código em R para modificar ou aprender.

## <a name="next-steps"></a>Passos seguintes

Tem mais dúvidas? Considere criar um [tíquete de suporte](https://azure.microsoft.com/support/create-ticket/).