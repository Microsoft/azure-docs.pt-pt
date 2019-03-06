---
title: Criar uma máquina de Virtual de ciência de dados do Linux do CentOS
titleSuffix: Azure
description: Configurar e criar uma máquina de Virtual de ciência de dados do Linux no Azure para fazer a análise e machine learning.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 58f4d7be530aa3c7e0071a2f0d80bc30ebd5cf25
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440918"
---
# <a name="provision-a-linux-centos-data-science-virtual-machine-on-azure"></a>Aprovisionar uma máquina de Virtual de ciência de dados de CentOS do Linux no Azure

A máquina de Virtual de ciência de dados do Linux é uma baseada em CentOS máquina virtual do Azure que vem com uma coleção de ferramentas pré-instaladas. Essas ferramentas são frequentemente utilizadas para a realização de análises de dados e machine learning. Os componentes principais programas de software incluídos são:

* Sistema operativo: Distribuição de Linux CentOS.
* Microsoft R Server Developer Edition
* Distribuição da Python anaconda (versões 2.7 e 3.5), incluindo bibliotecas de análise de dados populares
* JuliaPro - uma distribuição organizada da linguagem Julia com bibliotecas de análise de dados e científicas populares
* Instância autónoma Spark e Hadoop (HDFS, Yarn) de nó único
* JupyterHub - um servidor de bloco de notas Jupyter multiutilizador com suporte R, Python, PySpark, Julia kernels
* Explorador do Storage do Azure
* Interface de linha de comandos do Azure (CLI) para gerir recursos do Azure
* PostgresSQL base de dados
* Ferramentas de aprendizagem de máquina
  * [Conjunto de ferramentas cognitivos](https://github.com/Microsoft/CNTK): Uma aprendizagem profunda Kit de ferramentas de software da Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): Um sistema de aprendizagem rápida técnicas como online, o hash, allreduce, reduções, learning2search, Active Directory e interativa de suporte de aprendizado.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Uma ferramenta, fornecendo a implementação de árvore precisos e de elevada.
  * [Rattle](https://togaware.com/rattle/) (a ferramenta de análise de R para saber mais facilmente): Uma ferramenta que simplifica a introdução à análise de dados e aprendizagem automática em R fácil, com a exploração de dados baseado na GUI e modelagem com a geração automática de código R.
* SDK do Azure em Java, Python, node. js, Ruby, PHP
* Bibliotecas em R e Python para utilizam no Azure Machine Learning e outros serviços do Azure
* Ferramentas de desenvolvimento e editores (RStudio, PyCharm, IntelliJ, Emacs, gedit, vi.)


Fazer ciência de dados envolve a iteração numa seqüência de tarefas:

1. Encontrar, para carregar e processar previamente os dados
1. Criar e testar modelos
1. Implementação de modelos para consumo em aplicações inteligentes

Os cientistas de dados usar várias ferramentas para concluir estas tarefas. Ele pode ser muito demorado para encontrar as versões apropriadas do software e, em seguida, para transferir, compilar e instalar estas versões.

A Máquina Virtual de ciência de dados de Linux podem facilitar essa carga substancialmente. Utilize-o para iniciar o projeto da análise. Permite-lhe trabalhar em tarefas em várias linguagens, incluindo o R, Python, SQL, Java e C++. Eclipse fornece um IDE para desenvolver e testar seu código que é fácil de usar. O SDK do Azure incluídos na VM permite-lhe criar seus aplicativos com vários serviços no Linux para a plataforma de cloud da Microsoft. Além disso, terá acesso a outras linguagens como Ruby, Perl, PHP e node. js, que também são instalados previamente.

Existem não existem custos de software para esta imagem VM de ciência de dados. Paga apenas as tarifas de utilização de hardware do Azure são avaliadas com base no tamanho da máquina virtual que for aprovisionado com a imagem de VM. Obter mais detalhes sobre as tarifas de computação podem ser encontrados no [página de lista de VM no Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Outras versões da máquina de Virtual de ciência de dados
Uma [Ubuntu](dsvm-ubuntu-intro.md) imagem também está disponível, com muitas das mesmas ferramentas que a imagem do CentOS mais estruturas de aprendizagem profunda. R [Windows](provision-vm.md) imagem também está disponível.

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder criar uma máquina de Virtual de ciência de dados do Linux, tem de ter o seguinte:

* **Uma subscrição do Azure**: Para obter uma, veja [avaliação gratuita do Azure obter](https://azure.microsoft.com/free/).
* **Uma conta de armazenamento do Azure**: Para criar um, veja [criar uma conta de armazenamento do Azure](../../storage/common/storage-quickstart-create-account.md). Em alternativa, se não pretender utilizar uma conta existente, a conta de armazenamento pode ser criada como parte do processo de criar a VM.

## <a name="create-your-linux-data-science-virtual-machine"></a>Criar a sua máquina de Virtual de ciência de dados do Linux
Eis os passos para criar uma instância da Máquina Virtual Linux Data Science:

1. Navegue para a máquina virtual listagem sobre o [portal do Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
1. Clique em **Create** (na parte inferior) para abrir o assistente.![ Configurar-data--vm de ciência](./media/linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
1. As secções seguintes fornecem as entradas para cada um dos passos do assistente (enumerado à direita da figura anterior) utilizadas para criar a máquina de Virtual de ciência de dados do Microsoft. Seguem-se as entradas necessárias para configurar cada um destes passos:
   
   a. **Noções básicas**:
   
   * **Nome**: Nome do seu servidor de ciência de dados que está a criar.
   * **Nome de utilizador**: Primeiro início de sessão na conta ID.
   * **palavra-passe**: Primeira conta palavra-passe (pode usar chave pública SSH em vez da palavra-passe).
   * **Subscrição**: Se tiver mais de uma subscrição, selecione aquele no qual a máquina está a ser criado e faturadas. Tem de ter privilégios de criação de recursos para esta subscrição.
   * **Grupo de recursos**: Pode criar um novo ou utilizar um grupo existente.
   * **Localização**: Selecione o Centro de dados mais adequada. Normalmente, é o Centro de dados que tem a maior parte dos seus dados, ou mais se aproxima-se à sua localização física para acesso de rede mais rápido.
   
   b. **Tamanho**:
   
   * Selecione um dos tipos de servidor que se adequa ao requisito funcional e restrições de custo. Selecione **ver tudo** para ver mais opções de tamanhos de VM.
   
   c. **Definições**:
   
   * **Tipo de disco**: Escolher **Premium** se preferir uma unidade de estado sólido (SSD). Caso contrário, escolha **padrão**.
   * **Conta de armazenamento**: Pode criar uma nova conta de armazenamento do Azure na sua subscrição ou utilize um já existente na mesma localização que foi selecionado na **Noções básicas** passo do assistente.
   * **Outros parâmetros**: Na maioria dos casos, simplesmente usar os valores predefinidos. A serem considerados valores não predefinidos, coloque o cursor sobre a ligação informativa para obter ajuda sobre campos específicos.
   
   d. **Resumo**:
   
   * Certifique-se de que todas as informações que introduziu estão corretas.
   
   e. **Comprar**:
   
   * Para iniciar o aprovisionamento, clique em **comprar**. Receberá uma ligação para os termos da transação. A VM não tem quaisquer custos adicionais para além de computação para o tamanho de servidor que selecionou no **tamanho** passo.

O aprovisionamento deve demorar cerca de 10 a 20 minutos. O estado do provisionamento é apresentado no portal do Azure.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Como acessar a máquina de Virtual de ciência de dados do Linux
Depois da VM é criada, pode iniciar sessão na mesma ao utilizar SSH. Utilize as credenciais da conta que criou no **Noções básicas** secção do passo 3 para a interface do shell de texto. No Windows, pode transferir uma ferramenta de cliente SSH, como o [Putty](http://www.putty.org). Se preferir um ambiente de trabalho gráfico (sistema de Windows X), pode utilizar o Putty de reencaminhamento de X11 ou instalar o cliente de X2Go.

> [!NOTE]
> O cliente de X2Go efetuada significativamente melhor do que em testes de reencaminhamento de X11. Recomendamos que utilize o cliente de X2Go para uma interface gráfica de área de trabalho.
> 
> 

## <a name="installing-and-configuring-x2go-client"></a>Instalar e configurar o cliente de X2Go
A VM do Linux já está a ser aprovisionado com o servidor de X2Go e pronto para aceitar ligações de cliente. Para ligar-se na área de trabalho de gráfico de VM do Linux, faça o seguinte no cliente:

1. Transferir e instalar o cliente de X2Go para a sua plataforma de cliente do [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
1. Executar o cliente de X2Go e selecione **nova sessão**. Ele abre uma janela de configuração com vários separadores. Introduza os seguintes parâmetros de configuração:
   * **Separador de sessão**:
     * **Host**: O nome de anfitrião ou endereço IP da sua VM de ciência de dados do Linux.
     * **início de sessão**: Nome de utilizador na VM do Linux.
     * **SSH porta**: Deixe-o em 22, o valor predefinido.
     * **Tipo de sessão**: Altere o valor para XFCE. Atualmente, a VM com Linux só suporta XFCE desktop.
   * **Guia de suporte de dados**: Se não precisar de utilizar o suporte para som e impressão do cliente, pode desativá-las.
   * **Pastas compartilhadas**: Se quiser diretórios das suas máquinas de cliente montadas na VM do Linux, adicione os diretórios de máquina de cliente que pretende partilhar com a VM neste separador.

Depois de iniciar sessão na VM com o cliente SSH ou de uma área de trabalho gráfica do XFCE por meio do cliente de X2Go, está pronto para começar a utilizar as ferramentas que são instaladas e configuradas na VM. No XFCE, pode ver atalhos no menu aplicações e ícones para muitas das ferramentas.

## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Ferramentas instaladas numa máquina Virtual Linux Data Science
### <a name="microsoft-r-server"></a>Microsoft R Server
R é uma das linguagens mais populares para análise de dados e o machine learning. Se pretender utilizar o R para a sua análise, a VM tem o Microsoft R Server (MRS) com o Microsoft R Open (MRO) e a biblioteca de Kernel da matemática (MKL). O MKL otimiza operações matemáticas comuns em algoritmos de análises. MRO é 100 por cento compatível com R do CRAN e qualquer uma das bibliotecas publicadas na CRAN R pode ser instalado na MRO. MRS dá-lhe o dimensionamento e a operacionalização de modelos de R em serviços web. Pode editar os seus programas de R em um dos editores de padrão, como o RStudio, vi, Emacs ou gedit. Se estiver a utilizar o editor de Emacs, tenha em atenção que o Emacs pacotes EÇO (Emacs participa como Palestrante as estatísticas), que simplifica a trabalhar com arquivos de R no editor de Emacs, tem sido previamente instalada.

Para iniciar R da consola, basta digitar **R** no shell. Isto leva-o para um ambiente interativo. Para desenvolver o seu programa de R, normalmente, utilize um editor como Emacs ou vi ou gedit e, em seguida, execute os scripts do R. Com o r Studio, tem um ambiente de IDE gráfico completas para desenvolver o seu programa de R.

Também existe um script R para a instalação a [pacotes de R de 20 principais](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) se desejar. Este script pode ser executado depois de se na interface interativa R, que pode ser introduzida (conforme mencionado), escrevendo **R** no shell.  

### <a name="python"></a>Python
Para o desenvolvimento com o Python, distribuição de Anaconda Python 2.7 e 3.5 foi instalada. Essa distribuição contém o Python base juntamente com aproximadamente 300 dos pacotes de análises de dados, engenharia e matemática mais populares. Pode utilizar os editores de texto padrão. Além disso, pode usar Spyder, um IDE de Python que é fornecida com distribuições Anaconda Python. Spyder necessita de uma área de trabalho gráfica ou X11 reencaminhamento. Um atalho para Spyder é fornecido na área de trabalho gráfico.

Como temos o Python 2.7 e 3.5, terá de ativar a versão Python pretendida (ambiente de conda) que deseja trabalhar na sessão atual. O processo de ativação define a variável de caminho para a versão pretendida do Python.

Para ativar o ambiente de conda Python 2.7, execute o seguinte comando a partir da shell:

    source /anaconda/bin/activate root

Python 2.7 é instalado em *bin/anaconda/*.

Para ativar o ambiente de conda Python 3.5, execute o seguinte a partir da shell:

    source /anaconda/bin/activate py35


Python 3.5 está instalado no */anaconda/envs/py35/bin*.

Para invocar uma sessão interativa do Python, basta escrever **python** no shell. Se estiver numa interface gráfica ou ter o conjunto de cópia de segurança de reencaminhamento de X11, pode digitar **pycharm** para iniciar o IDE de Python PyCharm.

Para instalar bibliotecas adicionais do Python, tem de ser executado ```conda``` ou ```pip``` comando em sudo e forneça o caminho completo do Gestor de pacotes de Python (conda ou pip) para instalar para o ambiente de Python correto. Por exemplo:

    sudo /anaconda/bin/pip install <package> #pip for Python 2.7
    sudo /anaconda/envs/py35/bin/pip install <package> #pip for Python 3.5
    sudo /anaconda/bin/conda install [-n py27] <package> #conda for Python 2.7, default behavior
    sudo /anaconda/bin/conda install -n py35 <package> #conda for Python 3.5


### <a name="jupyter-notebook"></a>Jupyter Notebook
A distribuição de Anaconda também vem com um bloco de notas do Jupyter, um ambiente para partilhar código e análise. O bloco de notas do Jupyter é acedido através do JupyterHub. Iniciar sessão com o seu nome de utilizador local do Linux e a palavra-passe.

O servidor de bloco de notas do Jupyter foi previamente configurado com o Python 2, 3 de Python e kernels do R. Há um ícone de área de trabalho com o nome "Bloco de notas Jupyter" para iniciar o browser para aceder ao servidor de bloco de notas. Se estiver na VM através do cliente de SSH ou X2Go, também pode visitar [ https://localhost:8000/ ](https://localhost:8000/) para aceder ao servidor de bloco de notas do Jupyter.

> [!NOTE]
> Continue se obter quaisquer avisos de certificado.
> 
> 

Pode acessar o servidor de bloco de notas do Jupyter a partir de qualquer anfitrião. Basta digitar *https://\<nome da VM de DNS ou endereço IP\>: 8000 /*

> [!NOTE]
> Porta 8000 está aberta na firewall por padrão quando a VM está aprovisionada.
> 
> 

Podemos ter compactado blocos de notas de exemplo – Python num e outro em R. Pode ver a ligação para os exemplos na home page do bloco de notas depois de autenticar para o bloco de notas do Jupyter com o seu nome de utilizador local do Linux e a palavra-passe. Pode criar um novo bloco de notas, selecionando **New**e, em seguida, o kernel de idioma apropriado. Se não vir a **New** botão a **Jupyter** ícone no canto superior esquerdo para ir para a home page do servidor do bloco de notas.

### <a name="apache-spark-standalone"></a>Autónomo do Apache Spark 
Uma instância autónoma do Apache Spark é pré-instalado na DSVM do Linux para o ajudar a desenvolver aplicações do Spark localmente primeiro antes de testar e implantar em grandes clusters. Pode executar programas PySpark por meio do kernel do Jupyter. Quando abrir o Jupyter e clique nas **New** botão, deverá ver uma lista de kernels disponíveis. O "Spark – Python" é o kernel do PySpark que lhe permite criar Spark aplicações utilizando a linguagem Python. Também pode utilizar um IDE de Python como PyCharm ou Spyder para criar programa Spark. Já que, esta é uma instância autónoma, a pilha de Spark é executado dentro do programa de cliente chamada. Isto torna mais rápido e fácil de solucionar problemas em comparação com o desenvolvimento de um cluster do Spark. 

Um bloco de notas do PySpark de exemplo é fornecido no Jupyter, que pode encontrar no diretório "SparkML" sob o diretório raiz do Jupyter ($HOME/blocos de notas/SparkML/pySpark). 

Se estiver de programação R para o Spark, pode utilizar o Microsoft R Server, SparkR ou sparklyr. 

Antes de executar no contexto do Spark no Microsoft R Server, terá de realizar uma uma vez que o passo de configuração para ativar um único nó HDFS do Hadoop e Yarn a instância local. Por predefinição, os serviços do Hadoop são instalados mas desativados na DSVM. Para ativá-lo, terá de executar os seguintes comandos como raiz na primeira vez:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

É possível parar o Hadoop relacionados com serviços quando não precisar, executando ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` um exemplo que demonstra como desenvolver e testar MRS no contexto de Spark remoto (que é a instância de Spark autónoma no DSVM) é fornecido e está disponível no `/dsvm/samples/MRS` diretório. 

### <a name="ides-and-editors"></a>IDEs e editores
Tem uma opção de vários editores de códigos. Isto inclui vi/VIM, Emacs, gEdit, PyCharm, o r Studio, Eclipse e IntelliJ. gEdit, Eclipse, IntelliJ, RStudio e PyCharm são editores gráficas e tem de ser iniciada para uma área de trabalho gráfica para utilizá-los. Esses editores tem o ambiente de trabalho e a aplicação atalhos no menu para iniciá-los.

**VIM** e **Emacs** são editores baseados em texto. No Emacs, podemos ter instalado um pacote adicional chamado Emacs participa como Palestrante estatísticas (eitos de acesso) que facilita o trabalho com R no editor de Emacs. Obter mais informações podem ser encontradas em [IMIR](http://ess.r-project.org/).

**Eclipse** é a abertura de origem, IDE extensível que suporta vários idiomas. A edição de programadores do Java é a instância instalada na VM. Plug-ins estão disponíveis para várias linguagens populares, que podem ser instaladas para estender o ambiente. Além disso, temos um plug-in instalado no Eclipse, chamado **Azure Toolkit para Eclipse**. Permite-lhe criar, desenvolver, testar e implementar aplicações do Azure utilizando o ambiente de desenvolvimento do Eclipse que oferece suporte a linguagens como Java. Há também uma **Azure SDK para Java** que permita o acesso aos diferentes serviços do Azure de dentro de um ambiente de Java. Obter mais informações sobre o Azure toolkit para Eclipse podem ser encontradas em [Azure Toolkit para Eclipse](../../azure-toolkit-for-eclipse.md).

**LaTex** é instalado através do pacote de texlive juntamente com um suplemento de Emacs [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) pacote, que simplifica a criação de seus documentos LaTex dentro de Emacs.  

### <a name="databases"></a>Bases de Dados
#### <a name="postgres"></a>Postgres
A base de dados do código-fonte aberto **Postgres** está disponível na VM, com os serviços em execução e initdb já foi concluída. Ainda precisa de criar bases de dados e os utilizadores. Para obter mais informações, consulte a [Postgres documentação](https://www.postgresql.org/docs/).  

#### <a name="graphical-sql-client"></a>Cliente SQL de gráfico
**SQuirrel SQL**, um cliente SQL gráfico, foi fornecido para ligar a bases de dados diferentes (por exemplo, o Microsoft SQL Server, o Postgres e o MySQL) e para executar consultas SQL. Pode executá-lo a partir de uma sessão de ambiente de trabalho gráfica (com o cliente de X2Go, por exemplo). Para invocar SQuirrel SQL, pode iniciá-lo do ícone na área de trabalho ou execute o seguinte comando no shell.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Antes da primeira utilização, configurar os controladores e aliases de base de dados. Os drivers JDBC estão localizados em:

*/usr/share/java/jdbcdrivers*

Para obter mais informações, consulte [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Ferramentas da linha de comandos para acessar o Microsoft SQL Server
O pacote de controladores ODBC para o SQL Server também vem com duas ferramentas de linha de comandos:

**bcp**: O volume de utilitário bcp copia dados entre uma instância do Microsoft SQL Server e um arquivo de dados num formato especificado pelo utilizador. O utilitário bcp. exe pode ser utilizado para importar um grande número de novas linhas para tabelas do SQL Server ou para exportar os dados para fora das tabelas para ficheiros de dados. Para importar dados para uma tabela, tem de utilizar um ficheiro de formato criado para a tabela ou compreender a estrutura da tabela e os tipos de dados que são válidos para as colunas.

Para obter mais informações, consulte [ligar com o bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**sqlcmd**: Pode inserir instruções Transact-SQL com o utilitário sqlcmd, bem como procedimentos de sistema e arquivos de script no prompt de comando. Esse utilitário utiliza ODBC para executar lotes do Transact-SQL.

Para obter mais informações, consulte [ligar com sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Existem algumas diferenças nesse utilitário entre plataformas Linux e Windows. Consulte a documentação para obter detalhes.
> 
> 

#### <a name="database-access-libraries"></a>Bibliotecas de acesso de base de dados
Bibliotecas estão disponíveis em R e Python para acessar bancos de dados.

* Em R, o **RODBC** pacote ou **dplyr** pacote permite-lhe consultar ou executar instruções SQL no servidor de base de dados.
* Em Python, o **pyodbc** biblioteca fornece acesso de base de dados com o ODBC, como a camada subjacente.  

Para o acesso **Postgres**:

* A partir de r: Utilizar o pacote **RPostgreSQL**.
* Do Python: Utilize o **psycopg2** biblioteca.

### <a name="azure-tools"></a>Ferramentas do Azure
As seguintes ferramentas do Azure são instaladas na VM:

* **Interface de linha de comandos do Azure**: A CLI do Azure permite-lhe criar e gerir recursos do Azure através de comandos da shell. Para invocar as ferramentas do Azure, basta escrever **do azure ajuda**. Para obter mais informações, consulte a [página de documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Explorador de armazenamento do Microsoft Azure**: Explorador de armazenamento do Microsoft Azure é uma ferramenta gráfica que é utilizada para percorrer os objetos que tem armazenados na sua conta de armazenamento do Azure, e para carregar e transferir dados para e de blobs do Azure. Pode acessar o Explorador de armazenamento do ícone de atalho de desktop. Pode invocá-lo num prompt do shell, escrevendo **StorageExplorer**. Tem de iniciar sessão a partir de um cliente X2Go ou tem o conjunto de cópia de segurança de reencaminhamento de X11.
* **Bibliotecas do Azure**: Seguem-se algumas das bibliotecas pré-instaladas.
  
  * **Python**: As bibliotecas relacionados com o Azure em Python que está instalados estão **azure**, **azureml**, **pydocumentdb**, e **pyodbc**. Com as três primeiras bibliotecas, pode aceder a serviços de armazenamento do Azure, Azure Machine Learning e Azure Cosmos DB (uma base de dados NoSQL no Azure). A biblioteca de quarta, pyodbc (juntamente com o Microsoft ODBC driver para SQL Server), permite o acesso ao SQL Server, SQL Database do Azure e Azure SQL Data Warehouse do Python, utilizando uma interface ODBC. Introduza **lista de pip** para ver todas as bibliotecas listadas. Certifique-se de que execute este comando no Python 2.7 e 3,5 ambientes.
  * **R**: As relacionados com o Azure, as bibliotecas no R instalados estão **AzureML** e **RODBC**.
  * **Java**: A lista de bibliotecas Java do Azure pode ser encontrada no diretório **/dsvm/sdk/AzureSDKJava** na VM. As bibliotecas de chave são do Azure armazenamento e gestão de APIs, do Azure Cosmos DB, JDBC controladores e para o SQL Server.  

Pode aceder a [portal do Azure](https://portal.azure.com) do navegador Firefox pré-instaladas. No portal do Azure, pode criar, gerir e monitorizar recursos do Azure.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O Azure Machine Learning é um serviço cloud totalmente gerido que permite-lhe criar, implementar e partilhar soluções de Análise Preditiva. Criar suas experimentações e modelos a partir do Azure Machine Learning Studio. Pode ser acedido a partir de um navegador da web na máquina de virtual de ciência de dados ao visitar [Microsoft Azure Machine Learning](https://studio.azureml.net).

Depois de iniciar sessão Azure Machine Learning Studio, tem acesso a uma tela de experimentação onde pode criar um fluxo lógico para os algoritmos de machine learning. Também tem acesso a um bloco de notas do Jupyter alojado no Azure Machine Learning e pode trabalhar de forma totalmente integrada com as experimentações no Machine Learning Studio. Operacionalize os modelos de machine learning que criou, encapsulando-as numa interface de serviço da web. Isto permite aos clientes escritas em qualquer linguagem invocar predições a partir de modelos de machine learning. Para obter mais informações, consulte a [documentação do Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Pode também criar os seus modelos em R ou Python na VM e, em seguida, implementá-la em produção no Azure Machine Learning. Podemos instalar bibliotecas no R (**AzureML**) e Python (**azureml**) para ativar esta funcionalidade.

Para obter informações sobre como implementar os modelos em R e Python no Azure Machine Learning, consulte [dez coisas que pode fazer na máquina Virtual de ciência de dados](vm-do-ten-things.md) (em particular, a secção "Criar modelos com R ou Python e Operacionalize-os com Azure Machine Learning").

> [!NOTE]
> Estas instruções destinam-se para a versão do Windows da VM de ciência de dados. Mas as informações fornecidas sobre a implementação de modelos para o Azure Machine Learning é aplicável à VM do Linux.
> 
> 

### <a name="machine-learning-tools"></a>Ferramentas de aprendizagem de máquina
A VM vem com alguns de machine learning ferramentas e algoritmos que foram pré-compilados e previamente instalados localmente. Estas incluem:

* **Microsoft Cognitive Toolkit** : Um Kit de ferramentas de aprendizagem profunda.
* **Vowpal Wabbit**: Um algoritmo de aprendizagem online rapidamente.
* **xgboost**: Uma ferramenta que fornece otimizado, algoritmos de árvore elevada.
* **Python**: Anaconda Python vem agrupado com algoritmos de machine learning com bibliotecas como Scikit-saiba. Pode instalar outras bibliotecas com o `pip install` comando.
* **R**: Uma biblioteca completa de funções do machine learning está disponível para R. Algumas das bibliotecas que vêm pré-instaladas são lm, glm, randomForest, rpart. Outras bibliotecas podem ser instaladas ao executar:
  
        install.packages(<lib name>)

Eis algumas informações adicionais sobre as ferramentas de três aprendizagem primeiro na lista.

#### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
Este é uma código-fonte aberto, Kit de ferramentas de aprendizagem profunda. Ele é uma ferramenta da linha de comandos (cntk) e já está a ser o caminho.

Para executar um exemplo básico, execute os seguintes comandos no shell:

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

Para obter mais informações, consulte a secção CNTK [GitHub](https://github.com/Microsoft/CNTK)e o [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit
Vowpal Wabbit é um sistema que utiliza técnicas, como online, hash, allreduce, reduções, learning2search, Active Directory, de aprendizagem automática e a aprendizagem interativa.

Para executar a ferramenta num exemplo básico, faça o seguinte:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Existem outras, maiores demonstrações nesse diretório. Para obter mais informações sobre VW, consulte [nesta secção do GitHub](https://github.com/JohnLangford/vowpal_wabbit)e o [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Esta é uma biblioteca que foi concebida e otimizada para algoritmos de elevada (árvore). O objetivo dessa biblioteca é emitir os limites de computação de máquinas para extremos precisava fornecer árvore em grande escala adaptativo que é dimensionável, portáteis e precisas.

Ele é fornecido como uma linha de comando, bem como uma biblioteca de R.

Para utilizar esta biblioteca no R, pode iniciar uma sessão de R interativa (apenas digitando **R** no shell) e carregar a biblioteca.

Eis um exemplo simples que pode executar no prompt de R:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Para executar a linha de comandos xgboost, aqui estão os comandos para executar no shell:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


Um arquivo de .model é gravado no diretório especificado. Pode encontrar informações sobre este exemplo de demonstração [no GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Para obter mais informações sobre xgboost, consulte a [página de documentação xgboost](https://xgboost.readthedocs.org/en/latest/)e a respetiva [repositório do GitHub](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle
Rattle (a **R** **uma**nalíticos **T**ool **T**s **L**ganhar **E** asily) utiliza dados baseados em GUI exploração e modelação. Ele Apresenta resumos de estatísticos e visual de dados, transformações de dados que podem ser modelados prontamente, cria modelos supervisionados e supervisionados dos dados, apresenta o desempenho dos modelos graficamente, e conjuntos de dados de novas pontuações. Ele também gera um código de R, replicar as operações na interface de Usuário que podem ser executadas diretamente no R ou utilizadas como um ponto de partida para análises posteriores.

Para executar Rattle, terá de estar numa gráfica desktop início de sessão. No terminal, escreva ```R``` para introduzir o ambiente de R. No prompt de R, introduza os seguintes comandos:

    library(rattle)
    rattle()

Agora uma interface gráfica abre-se com um conjunto de guias. Aqui estão os passos de início rápido em Rattle necessária para utilizar um conjunto de dados de Meteorologia de exemplo e criar um modelo. Em alguns dos passos abaixo, é solicitado a instalar e carregar alguns pacotes R necessários que não estão já no sistema automaticamente.

> [!NOTE]
> Se não tiver acesso para instalar o pacote no diretório do sistema (predefinição), pode ver uma mensagem na janela da consola de R para instalar pacotes para sua biblioteca pessoal. Resposta *y* se vir que esses prompts.
> 
> 

1. Clique em **Executar**.
1. Uma caixa de diálogo aparece, que lhe pergunta se gostaria de usar o conjunto de dados de Meteorologia de exemplo. Clique em **Sim** para carregar o exemplo.
1. Clique nas **modelo** separador.
1. Clique em **Execute** para criar uma árvore de decisão.
1. Clique em **desenhar** para apresentar a árvore de decisões.
1. Clique nas **floresta** botão de opção e clique em **Execute** para criar uma floresta aleatória.
1. Clique nas **Evaluate** separador.
1. Clique nas **risco** botão de opção e clique em **Execute** para apresentar os dois gráficos de desempenho de risco (Cumulative).
1. Clique nas **Log** separador para mostrar o código de gerar R para as operações anteriores.
   (Devido a um erro na versão atual do Rattle, precisa inserir um *#* caractere à frente do *exportar este registo...*  no texto do registo.)
1. Clique nas **exportar** botão para guardar o ficheiro de script R com o nome *weather_script. R* para a pasta raiz.

Pode sair Rattle e R. Agora pode modificar o script R gerado ou utilizá-lo, pois é executá-lo em qualquer altura para repetir a tudo o que foi feito na interface de Usuário Rattle. Especialmente para iniciantes em R, esta é uma forma fácil de fazer uma análise e machine learning numa interface gráfica simple, ao gerar automaticamente o código no R para modificar e/ou Saiba mais rapidamente.

## <a name="next-steps"></a>Passos Seguintes
Eis como pode continuar a sua aprendizagem e a exploração:

* O [ciência de dados numa máquina Virtual Linux Data Science](linux-dsvm-walkthrough.md) instruções mostram como realizar várias tarefas de ciência de dados comuns com a VM de ciência de dados do Linux aprovisionados aqui. 
* Explore as várias ferramentas de ciência de dados na VM de ciência de dados por experimentar as ferramentas descritas neste artigo. Também pode executar *dsvm-mais-info* no shell na máquina virtual para uma introdução básica e ponteiros para obter mais informações sobre as ferramentas instaladas na VM.  
* Aprenda a criar soluções de análise de ponto-a-ponto sistematicamente utilizando o [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
* Visite o [galeria do Cortana Analytics](http://gallery.cortanaanalytics.com) para machine learning e os dados analytics de exemplo que utilizam o Cortana Analytics Suite.

