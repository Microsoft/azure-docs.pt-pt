---
title: Criar uma máquina de Virtual de ciência de dados do Ubuntu Linux
titleSuffix: Azure
description: Configurar e criar um Data Science Virtual Machine para Linux (Ubuntu) no Azure para fazer a análise e machine learning.
services: machine-learning
documentationcenter: ''
author: vijetajo
ms.author: vijetaj
manager: cgronlun
ms.custom: seodec18
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/16/2018
ms.openlocfilehash: 8a19f414f31c307111edad876ed973ff4027d907
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68591925"
---
# <a name="provision-the-data-science-virtual-machine-for-linux-ubuntu"></a>Aprovisionar a máquina de Virtual de ciência de dados para Linux (Ubuntu)

A máquina de Virtual de ciência de dados para Linux é uma imagem de máquina virtual baseada em Ubuntu que torna mais fácil começar a utilizar com o machine learning, incluindo a aprendizagem profunda, no Azure. Ferramentas de aprendizagem profunda incluem:

* [Caffe](https://caffe.berkeleyvision.org/): Uma estrutura de aprendizado profundo criada para velocidade, expressividade e modularidade
* [Caffe2](https://github.com/caffe2/caffe2): Uma versão de plataforma cruzada do Caffe
* [Microsoft cognitive Toolkit](https://github.com/Microsoft/CNTK): Um kit de ferramentas de software de aprendizado profundo da Microsoft Research
* [H2O](https://www.h2o.ai/): Uma plataforma de Big Data de software livre e uma interface gráfica do usuário
* [Keras](https://keras.io/): Uma API de rede neural de alto nível no Python para TensorFlow, Microsoft Cognitive Toolkit e Theano
* [MXNet](https://mxnet.io/): Uma biblioteca de aprendizado profundo flexível e eficiente com muitas associações de linguagem
* [DÍGITOS NVIDIA](https://developer.nvidia.com/digits): Um sistema gráfico que simplifica tarefas comuns de aprendizado profundo
* [PyTorch](https://pytorch.org/): Uma biblioteca Python de alto nível com suporte para redes dinâmicas
* [TensorFlow](https://www.tensorflow.org/): Uma biblioteca de código-fonte aberto para a inteligência de máquina do Google
* [Theano](http://deeplearning.net/software/theano/): Uma biblioteca do Python para definir, otimizar e avaliar com eficiência expressões matemáticas que envolvem matrizes multidimensionais
* [Torch](http://torch.ch/): Uma estrutura de computação científica com amplo suporte para algoritmos de aprendizado de máquina
* CUDA, cuDNN e os controladores NVIDIA
* Muitos blocos de notas do Jupyter de exemplo

Todas as bibliotecas são as versões GPU, embora também são executadas na CPU.

A máquina de Virtual de ciência de dados para Linux também contém ferramentas populares para desenvolvimento e de ciência de atividades de dados, incluindo:

* Microsoft R Server Developer Edition com o Microsoft R Open
* Distribuição da Python anaconda (versões 2.7 e 3.5), incluindo bibliotecas de análise de dados populares
* JuliaPro - uma distribuição organizada da linguagem Julia com bibliotecas de análise de dados e científicas populares
* Instância autónoma Spark e Hadoop (HDFS, Yarn) de nó único
* JupyterHub - um servidor de bloco de notas Jupyter multiutilizador com suporte R, Python, PySpark, Julia kernels
* Explorador do Storage do Azure
* Interface de linha de comandos do Azure (CLI) para gerir recursos do Azure
* Ferramentas de aprendizagem de máquina
  * [Wabbit Vowpal](https://github.com/JohnLangford/vowpal_wabbit): Um sistema de aprendizado de máquina rápido com suporte a técnicas como online, hash, onreduz, reduções, learning2search, ativo e aprendizado interativo
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Uma ferramenta que fornece implementação de árvore aumentada rápida e precisa
  * [Rattle](https://togaware.com/rattle/): Uma ferramenta gráfica que facilita a introdução à análise de dados e ao aprendizado de máquina em R
  * [LightGBM](https://github.com/Microsoft/LightGBM): Uma estrutura de aumento de gradiente rápida, distribuída e de alto desempenho
* SDK do Azure em Java, Python, node. js, Ruby, PHP
* Bibliotecas em R e Python para utilizam no Azure Machine Learning e outros serviços do Azure
* Ferramentas de desenvolvimento e editores (RStudio, PyCharm, IntelliJ, Emacs, vim)

Fazer ciência de dados envolve a iteração numa seqüência de tarefas:

1. Encontrar, para carregar e processar previamente os dados
1. Criar e testar modelos
1. Implementação de modelos para consumo em aplicações inteligentes

Os cientistas de dados usar várias ferramentas para concluir estas tarefas. Ele pode ser muito demorado para encontrar as versões apropriadas do software e, em seguida, para transferir, compilar e instalar estas versões.

A máquina de Virtual de ciência de dados para Linux pode facilitar essa carga substancialmente. Utilize-o para iniciar o projeto da análise. Permite-lhe trabalhar em tarefas em várias linguagens, incluindo o R, Python, SQL, Java e C++. O SDK do Azure incluídos na VM permite-lhe criar seus aplicativos com vários serviços no Linux para a plataforma de cloud da Microsoft. Além disso, terá acesso a outras linguagens como Ruby, Perl, PHP e node. js, que também são instalados previamente.

Existem não existem custos de software para esta imagem VM de ciência de dados. Paga apenas as tarifas de utilização de hardware do Azure são avaliadas com base no tamanho da máquina virtual que for aprovisionado. Obter mais detalhes sobre as tarifas de computação podem ser encontrados no [página de lista de VM no Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Outras versões da máquina de Virtual de ciência de dados

R [CentOS](linux-dsvm-intro.md) imagem também está disponível, com muitas das mesmas ferramentas, como a imagem de Ubuntu. R [Windows](provision-vm.md) imagem também está disponível.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder criar uma máquina de Virtual de ciência de dados para Linux, tem de ter uma subscrição do Azure. Para obter uma, veja [avaliação gratuita do Azure obter](https://azure.microsoft.com/free/).

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Criar a sua máquina de Virtual de ciência de dados para Linux

Eis os passos para criar uma instância de máquina de Virtual de ciência de dados para Linux:

1. Navegue para a máquina virtual listagem sobre o [portal do Azure](https://portal.azure.com/#create/microsoft-dsvm.linux-data-science-vm-ubuntulinuxdsvmubuntu). Você pode ser solicitado a entrar em sua conta do Azure se você ainda não tiver entrado. 
1. Clique em **Create** (na parte inferior) para abrir o assistente.![ Configurar-data--vm de ciência](./media/dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
1. As secções seguintes fornecem as entradas para cada um dos passos do assistente (enumerado à direita da figura anterior) utilizadas para criar a máquina de Virtual de ciência de dados do Microsoft. Seguem-se as entradas necessárias para configurar cada um destes passos:

   a. **Noções básicas**:

   * **Nome**: Nome do seu servidor de ciência de dados que você está criando.
   * **Tipo de disco da VM**: Escolha **SSD Premium** se preferir uma unidade de estado sólido (SSD). Caso contrário, escolha **Standard HDD**. 
   * **Nome de usuário**: ID de entrada da primeira conta.
   * **Senha**: Senha da primeira conta (você pode usar a chave pública SSH em vez de senha).
   * **Assinatura**: Se tiver mais de uma subscrição, selecione aquele no qual a máquina está a ser criado e faturadas. Tem de ter privilégios de criação de recursos para esta subscrição.
   * **Grupo de recursos**: Pode criar um novo ou utilizar um grupo existente.
   * **Local**: Selecione o data center mais apropriado. Normalmente, é o Centro de dados que tem a maior parte dos seus dados, ou mais se aproxima-se à sua localização física para acesso de rede mais rápido.

   b. **Tamanho**:

   * Selecione um dos tipos de servidor que se adequa ao requisito funcional e restrições de custo. Selecione uma VM de classe NC ou ND para instâncias de VM baseadas em GPU. O [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) página apresenta uma lista de regiões com GPUs.

   c. **Definições**:

   * Na maioria dos casos, pode usar apenas os valores predefinidos. A serem considerados valores não predefinidos, coloque o cursor sobre a ligação informativa para obter ajuda sobre campos específicos.

   d. **Resumo**:

   * Certifique-se de que todas as informações que introduziu estão corretas. É fornecida uma ligação para os termos de utilização. A VM não tem quaisquer custos adicionais para além de computação para o tamanho de servidor que selecionou no **tamanho** passo. Para iniciar o aprovisionamento, clique em **criar**. 

O aprovisionamento, deve demorar cerca de 5 minutos. O estado do provisionamento é apresentado no portal do Azure.

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>Como acessar a máquina de Virtual de ciência de dados para Linux

Pode acessar a DSVM em Ubuntu com três métodos:

1. SSH para sessões de terminal
1. X2Go para sessões de gráficos
1. JupyterHub e JupyterLab para blocos de notas do Jupyter

Você também pode anexar um VM de Ciência de Dados ao Azure Notebooks para executar blocos de anotações do Jupyter na VM e ignorar as limitações da camada de serviço gratuita. Para obter mais informações, consulte [gerenciar e configurar projetos de blocos de anotações-camada de computação](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

Depois da VM é criada, pode iniciar sessão na mesma ao utilizar SSH. Utilize as credenciais da conta que criou no **Noções básicas** secção do passo 3 para a interface do shell de texto. No Windows, pode transferir uma ferramenta de cliente SSH, como o [Putty](https://www.putty.org). Se preferir um ambiente de trabalho gráfico (sistema de Windows X), pode utilizar o Putty de reencaminhamento de X11 ou instalar o cliente de X2Go.

> [!NOTE]
> O cliente de X2Go executada melhor do que em testes de reencaminhamento de X11. Recomendamos que utilize o cliente de X2Go para uma interface gráfica de área de trabalho.

### <a name="x2go"></a>X2Go

A VM do Linux já está a ser aprovisionado com o servidor de X2Go e pronto para aceitar ligações de cliente. Para ligar-se na área de trabalho de gráfico de VM do Linux, execute o seguinte procedimento no cliente:

1. Transferir e instalar o cliente de X2Go para a sua plataforma de cliente do [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Executar o cliente de X2Go e selecione **nova sessão**. Ele abre uma janela de configuração com vários separadores. Introduza os seguintes parâmetros de configuração:
   * **Separador de sessão**:
     * **Host**: O nome do host ou endereço IP do seu VM de Ciência de Dados Linux.
     * **Logon**: Nome de usuário na VM Linux.
     * **Porta SSH**: Deixe em 22, o valor padrão.
     * **Tipo de sessão**: Altere o valor para XFCE. Atualmente, a VM com Linux só suporta XFCE desktop.
   * **Guia mídia**: Você pode desativar o suporte a som e a impressão do cliente se não precisar usá-los.
   * **Pastas**compartilhadas: Se você quiser diretórios de seus computadores cliente montados na VM Linux, adicione os diretórios de computador cliente que você deseja compartilhar com a VM nessa guia.

Depois de iniciar sessão na VM com o cliente SSH ou de uma área de trabalho gráfica do XFCE por meio do cliente de X2Go, está pronto para começar a utilizar as ferramentas que são instaladas e configuradas na VM. No XFCE, pode ver atalhos no menu aplicações e ícones para muitas das ferramentas.

### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub e JupyterLab

Executa a DSVM em Ubuntu [JupyterHub](https://github.com/jupyterhub/jupyterhub), um servidor de Jupyter de vários utilizador. Para se conectar, navegue até https\/:/Your-VM-IP: 8000 em seu laptop ou desktop, insira o nome de usuário e a senha que você usou para criar a VM e entre. Muitos blocos de notas de exemplo estão disponíveis para procurar e experimentar.

JupyterLab, a próxima geração de blocos de notas do Jupyter e JupyterHub, também está disponível. Para acessá-lo, entre no JupyterHub e navegue até a URL https:\//Your-VM-IP: 8000/User/Your-username/Lab. Você pode definir JupyterLab como o servidor de notebook padrão adicionando essa linha a */etc/jupyterhub/jupyterhub_config.py*:

```python
c.Spawner.default_url = '/lab'
```

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>Ferramentas instaladas na máquina de Virtual de ciência de dados para Linux

### <a name="deep-learning-libraries"></a>Bibliotecas de aprendizagem profunda

#### <a name="cntk"></a>CNTK

O Microsoft Cognitive Toolkit é uma código-fonte aberto, Kit de ferramentas de aprendizagem profunda. Enlaces de Python estão disponíveis nos ambientes de Conda de raiz e py35. Ele também tem uma ferramenta da linha de comandos (cntk) que já se encontra no caminho.

Blocos de notas de Python de exemplo estão disponíveis no JupyterHub. Para executar um exemplo básico na linha de comando, execute os seguintes comandos no Shell:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Para obter mais informações, consulte a secção CNTK [GitHub](https://github.com/Microsoft/CNTK)e o [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="caffe"></a>Caffe

Caffe é uma estrutura de aprendizagem profunda da visão de Berkeley e central de informações. Está disponível no /opt/caffe. Exemplos podem ser encontrados no /opt/caffe/examples.

#### <a name="caffe2"></a>Caffe2

Caffe2 é uma estrutura de aprendizagem profunda do Facebook que se baseia em Caffe. Está disponível no Python 2.7 no ambiente de raiz de Conda. Para ativá-lo, execute o seguinte comando no Shell:

```bash
source /anaconda/bin/activate root
```

Alguns blocos de notas de exemplo estão disponíveis no JupyterHub.

#### <a name="h2o"></a>H2O

H2O é uma plataforma de Análise Preditiva e de aprendizagem rápida, dentro da memória, distribuída. Um pacote do Python está instalado em ambientes de Anaconda de raiz e py35. Também é instalado um pacote de R. Para iniciar o H2O a partir da linha de comandos, execute `java -jar /dsvm/tools/h2o/current/h2o.jar`; existem vários [opções de linha de comandos](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) que pretende configurar. A IU do Flow Web podem ser acedido ao navegar para http://localhost:54321 para começar a utilizar. Blocos de notas de exemplo também estão disponíveis no JupyterHub.

#### <a name="keras"></a>Keras

Keras é uma API de rede neural de alto nível no Python que é capaz de ser executada sobre TensorFlow, Microsoft Cognitive Toolkit ou Theano. Está disponível nos ambientes de Python de raiz e py35.

#### <a name="mxnet"></a>MXNet

MXNet é uma estrutura de aprendizagem profunda destinada a eficiência e flexibilidade. Ele tem enlaces de R e Python incluídos na DSVM. Blocos de notas de exemplo estão incluídos no JupyterHub e código de exemplo está disponível no /dsvm/samples/mxnet.

#### <a name="nvidia-digits"></a>NVIDIA DÍGITOS

NVIDIA Deep Learning GPU treinamento sistema, conhecido como dígitos, é um sistema para simplificar tarefas comuns de aprendizagem profunda, como gerenciamento de dados, design e as redes neurais treinamento em sistemas GPU e monitorização do desempenho em tempo real com visualização avançada.

DÍGITOS está disponível como um serviço, chamado dígitos. Inicie o serviço e navegue até http://localhost:5000 para começar a utilizar.

DÍGITOS também é instalado como um módulo de Python no ambiente de raiz de Conda.

#### <a name="tensorflow"></a>TensorFlow

TensorFlow é a biblioteca de aprendizagem profunda da Google. É uma biblioteca de software livre para computação numérica usando grafos de fluxo de dados. TensorFlow está disponível no ambiente de Python py35 e alguns blocos de notas de exemplo estão incluídos no JupyterHub.

#### <a name="theano"></a>Theano

Theano é uma biblioteca de Python para o cálculo numérica eficiente. Está disponível nos ambientes de Python de raiz e py35. 

#### <a name="torch"></a>Torch

Maçarico é uma estrutura de computação científica com amplo suporte para algoritmos de machine learning. Está disponível no /dsvm/tools/torch e a sessão interativa de th e o Gestor de pacotes de luarocks estão disponíveis na linha de comandos. Exemplos estão disponíveis no /dsvm/samples/torch.

PyTorch também está disponível no ambiente de Anaconda de raiz. Os exemplos são em /dsvm/samples/pytorch.

### <a name="microsoft-r-server"></a>Microsoft R Server

R é uma das linguagens mais populares para análise de dados e o machine learning. Se pretender utilizar o R para a sua análise, a VM tem o Microsoft R Server (MRS) com o Microsoft R Open (MRO) e a biblioteca de Kernel da matemática (MKL). O MKL otimiza operações matemáticas comuns em algoritmos de análises. MRO é 100 por cento compatível com R do CRAN e qualquer uma das bibliotecas publicadas na CRAN R pode ser instalado na MRO. MRS dá-lhe o dimensionamento e a operacionalização de modelos de R em serviços web. Pode editar os seus programas de R em um dos editores de padrão, como o RStudio, vi ou Emacs. Se preferir utilizar o editor de Emacs, tem sido previamente instalada. O pacote de Emacs eitos de acesso (Emacs participa como Palestrante estatísticas) simplifica o trabalho com arquivos de R no editor de Emacs.

Para iniciar R da consola, basta digitar **R** no shell. Esse comando leva você a um ambiente interativo. Para desenvolver o seu programa de R, normalmente, utilize um editor como Emacs ou vi e, em seguida, execute os scripts do R. Com o r Studio, tem um ambiente de IDE gráfico completas para desenvolver o seu programa de R.

Também existe um script R para a instalação a [pacotes de R de 20 principais](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) se desejar. Este script pode ser executado depois de se na interface interativa R, que pode ser introduzida (conforme mencionado), escrevendo **R** no shell.  

### <a name="python"></a>Python

Anaconda Python está instalado com o Python 2.7 e 3,5 ambientes. Denomina-se o ambiente de 2,7 _raiz_, e o ambiente de 3,5 é chamado _py35_. Essa distribuição contém o Python base juntamente com aproximadamente 300 dos pacotes de análises de dados, engenharia e matemática mais populares.

O ambiente de py35 é a predefinição. Para ativar o ambiente de raiz (2.7):

```bash
source activate root
```

Para ativar o ambiente de py35 novamente:

```bash
source activate py35
```

Para invocar uma sessão interativa do Python, basta escrever **python** no shell. 

Instalar bibliotecas adicionais do Python usando ```conda``` ou ```pip``` . Para o pip, ative o ambiente correto primeiro se não pretender que o padrão:

```bash
source activate root
pip install <package>
```

Em alternativa, especificar o caminho completo para do pip:

```bash
/anaconda/bin/pip install <package>
```

Para conda, deve sempre especificar o nome do ambiente (_py35_ ou _raiz_):

```bash
conda install <package> -n py35
```

Se estiver numa interface gráfica ou ter o conjunto de cópia de segurança de reencaminhamento de X11, pode digitar **pycharm** para iniciar o IDE de Python PyCharm. Pode utilizar os editores de texto padrão. Além disso, pode usar Spyder, um IDE de Python que é fornecida com distribuições Anaconda Python. Spyder necessita de uma área de trabalho gráfica ou X11 reencaminhamento. Um atalho para Spyder é fornecido na área de trabalho gráfico.

### <a name="jupyter-notebook"></a>Jupyter Notebook

A distribuição de Anaconda também vem com um bloco de notas do Jupyter, um ambiente para partilhar código e análise. O bloco de notas do Jupyter é acedido através do JupyterHub. Iniciar sessão com o seu nome de utilizador local do Linux e a palavra-passe.

O servidor de bloco de notas do Jupyter foi previamente configurado com o Python 2, 3 de Python e kernels do R. Há um ícone de área de trabalho com o nome "Bloco de notas Jupyter" para iniciar o browser para aceder ao servidor de bloco de notas. Se estiver na VM através do cliente de SSH ou X2Go, também pode visitar [ https://localhost:8000/ ](https://localhost:8000/) para aceder ao servidor de bloco de notas do Jupyter.

> [!NOTE]
> Continue se obter quaisquer avisos de certificado.

Pode acessar o servidor de bloco de notas do Jupyter a partir de qualquer anfitrião. Basta digitar *https://\<nome da VM de DNS ou endereço IP\>: 8000 /*

> [!NOTE]
> Porta 8000 está aberta na firewall por padrão quando a VM está aprovisionada. 

Podemos ter compactado blocos de notas de exemplo – Python num e outro em R. Pode ver a ligação para os exemplos na home page do bloco de notas depois de autenticar para o bloco de notas do Jupyter com o seu nome de utilizador local do Linux e a palavra-passe. Pode criar um novo bloco de notas, selecionando **New**e, em seguida, o kernel de idioma apropriado. Se não vir a **New** botão a **Jupyter** ícone no canto superior esquerdo para ir para a home page do servidor do bloco de notas.

### <a name="apache-spark-standalone"></a>Autónomo do Apache Spark

Uma instância autónoma do Apache Spark é pré-instalado na DSVM do Linux para o ajudar a desenvolver aplicações do Spark localmente primeiro antes de testar e implantar em grandes clusters. Pode executar programas PySpark por meio do kernel do Jupyter. Quando abre o Jupyter, clique nas **New** botão e deverá ver uma lista de kernels disponíveis. O "Spark – Python" é o kernel do PySpark que lhe permite criar Spark aplicações utilizando a linguagem Python. Você também pode usar um IDE do Python como PyCharm ou Spyder para criar seu programa Spark. Nessa instância autônoma, a pilha do Spark é executada dentro do programa cliente de chamada, o que torna mais rápido e fácil solucionar problemas em comparação ao desenvolvimento em um cluster Spark.

Um bloco de notas do PySpark de exemplo é fornecido no Jupyter, que pode encontrar no diretório "SparkML" sob o diretório raiz do Jupyter ($HOME/blocos de notas/SparkML/pySpark). 

Se você estiver programando em R para Spark, poderá usar Microsoft R Server, Sparkr ou sparklyr. 

Antes de executar no contexto do Spark no Microsoft R Server, terá de realizar uma uma vez que o passo de configuração para ativar um único nó HDFS do Hadoop e Yarn a instância local. Por predefinição, os serviços do Hadoop são instalados mas desativados na DSVM. Para ativá-lo, terá de executar os seguintes comandos como raiz na primeira vez:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Você pode interromper os serviços relacionados do Hadoop quando não precisar deles executando```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```

Um exemplo que demonstra como desenvolver e testar o Sra no contexto remoto do Spark (que é a instância autônoma do Spark no DSVM) é fornecido e está disponível no diretório */dsvm/Samples/Mrs* .

### <a name="ides-and-editors"></a>IDEs e editores

Você tem a opção de vários editores de código, incluindo vi/VIM, Emacs, PyCharm, RStudio e IntelliJ. IntelliJ, RStudio PyCharm são editores gráficas e tem de ser iniciada para uma área de trabalho gráfica para utilizá-los. Esses editores tem o ambiente de trabalho e a aplicação atalhos no menu para iniciá-los.

**VIM** e **Emacs** são editores baseados em texto. No Emacs, podemos ter instalado um pacote adicional chamado Emacs participa como Palestrante estatísticas (eitos de acesso) que facilita o trabalho com R no editor de Emacs. Obter mais informações podem ser encontradas em [IMIR](https://ess.r-project.org/).

**LaTex** é instalado através do pacote de texlive juntamente com um suplemento de Emacs [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) pacote, que simplifica a criação de seus documentos LaTex dentro de Emacs.  

### <a name="databases"></a>Bases de Dados

#### <a name="graphical-sql-client"></a>Cliente SQL de gráfico

**SQuirrel SQL**, um cliente SQL gráfico, foi fornecido para ligar a bases de dados diferentes (por exemplo, o Microsoft SQL Server e o MySQL) e para executar consultas SQL. Você pode executar o SQuirrel SQL de uma sessão de área de trabalho gráfica (usando o cliente X2Go, por exemplo) usando um ícone de área de trabalho ou usando o seguinte comando no Shell:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Antes da primeira utilização, configurar os controladores e aliases de base de dados. Os drivers JDBC estão localizados em:

*/usr/share/java/jdbcdrivers*

Para obter mais informações, consulte [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Ferramentas da linha de comandos para acessar o Microsoft SQL Server

O pacote de controladores ODBC para o SQL Server também vem com duas ferramentas de linha de comandos:

**bcp**: O utilitário bcp copia dados em massa entre uma instância do Microsoft SQL Server e um arquivo de dados em um formato especificado pelo usuário. O utilitário bcp. exe pode ser utilizado para importar um grande número de novas linhas para tabelas do SQL Server ou para exportar os dados para fora das tabelas para ficheiros de dados. Para importar dados para uma tabela, tem de utilizar um ficheiro de formato criado para a tabela ou compreender a estrutura da tabela e os tipos de dados que são válidos para as colunas.

Para obter mais informações, consulte [ligar com o bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**sqlcmd**: Você pode inserir instruções Transact-SQL com o utilitário sqlcmd, bem como procedimentos do sistema e arquivos de script no prompt de comando. Esse utilitário utiliza ODBC para executar lotes do Transact-SQL.

Para obter mais informações, consulte [ligar com sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Existem algumas diferenças nesse utilitário entre plataformas Linux e Windows. Consulte a documentação para obter detalhes.

#### <a name="database-access-libraries"></a>Bibliotecas de acesso de base de dados

Bibliotecas estão disponíveis em R e Python para acessar bancos de dados.

* Em R, o **RODBC** pacote ou **dplyr** pacote permite-lhe consultar ou executar instruções SQL no servidor de base de dados.
* Em Python, o **pyodbc** biblioteca fornece acesso de base de dados com o ODBC, como a camada subjacente.  

### <a name="azure-tools"></a>Ferramentas do Azure

As seguintes ferramentas do Azure são instaladas na VM:

* **Interface de linha de comando do Azure**: O CLI do Azure permite que você crie e gerencie recursos do Azure por meio de comandos do Shell. Para invocar as ferramentas do Azure, basta escrever **do azure ajuda**. Para obter mais informações, consulte a [página de documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Gerenciador de armazenamento do Microsoft Azure**: Gerenciador de Armazenamento do Microsoft Azure é uma ferramenta gráfica usada para navegar pelos objetos que você armazenou em sua conta de armazenamento do Azure e para carregar e baixar dados de e para BLOBs do Azure. Pode acessar o Explorador de armazenamento do ícone de atalho de desktop. Pode invocá-lo num prompt do shell, escrevendo **StorageExplorer**. Você deve estar conectado de um cliente X2Go ou ter a configuração de encaminhamento X11.
* **Bibliotecas do Azure**: A seguir estão algumas das bibliotecas pré-instaladas.
  
  * **Python**: As bibliotecas relacionadas ao Azure no Python instaladas são **Azure**, **azureml**, **pydocumentdb**e **pyodbc**. Com as três primeiras bibliotecas, pode aceder a serviços de armazenamento do Azure, Azure Machine Learning e Azure Cosmos DB (uma base de dados NoSQL no Azure). A biblioteca de quarta, pyodbc (juntamente com o Microsoft ODBC driver para SQL Server), permite o acesso ao SQL Server, SQL Database do Azure e Azure SQL Data Warehouse do Python, utilizando uma interface ODBC. Introduza **lista de pip** para ver todas as bibliotecas listadas. Certifique-se de que execute este comando no Python 2.7 e 3,5 ambientes.
  * **R**: As bibliotecas relacionadas ao Azure no R que estão instaladas são **AzureML** e **RODBC**.
  * **Java**: A lista de bibliotecas Java do Azure pode ser encontrada no diretório **/dsvm/SDK/AzureSDKJava** na VM. As bibliotecas de chave são do Azure armazenamento e gestão de APIs, do Azure Cosmos DB, JDBC controladores e para o SQL Server.  

Pode aceder a [portal do Azure](https://portal.azure.com) do navegador Firefox pré-instaladas. No portal do Azure, pode criar, gerir e monitorizar recursos do Azure.

### <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço cloud totalmente gerido que permite-lhe criar, implementar e partilhar soluções de Análise Preditiva. Criar suas experimentações e modelos a partir do Azure Machine Learning Studio. Pode ser acedido a partir de um navegador da web na máquina de virtual de ciência de dados ao visitar [Microsoft Azure Machine Learning](https://studio.azureml.net).

Depois de iniciar sessão Azure Machine Learning Studio, tem acesso a uma tela de experimentação onde pode criar um fluxo lógico para os algoritmos de machine learning. Também tem acesso a um bloco de notas do Jupyter alojado no Azure Machine Learning e pode trabalhar de forma totalmente integrada com as experimentações no Machine Learning Studio. Operacionalize os modelos de machine learning que criou, encapsulando-as numa interface de serviço da web. A operacionalização dos modelos de aprendizado de máquina permite que os clientes escritos em qualquer linguagem invoquem previsões desses modelos. Para obter mais informações, consulte a [documentação do Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Pode também criar os seus modelos em R ou Python na VM e, em seguida, implementá-la em produção no Azure Machine Learning. Podemos instalar bibliotecas no R (**AzureML**) e Python (**azureml**) para ativar esta funcionalidade.

Para obter informações sobre como implementar os modelos em R e Python no Azure Machine Learning, consulte [dez coisas que pode fazer na máquina Virtual de ciência de dados](vm-do-ten-things.md) (em particular, a secção "Criar modelos com R ou Python e Operacionalize-os com Azure Machine Learning").

> [!NOTE]
> Estas instruções destinam-se para a versão do Windows da VM de ciência de dados. Mas as informações fornecidas sobre a implementação de modelos para o Azure Machine Learning é aplicável à VM do Linux.

### <a name="machine-learning-tools"></a>Ferramentas de aprendizagem de máquina

A VM vem com alguns de machine learning ferramentas e algoritmos que foram pré-compilados e previamente instalados localmente. Estas incluem:

* **Wabbit Vowpal**: Um algoritmo de aprendizado online rápido.
* **xgboost**: Uma ferramenta que fornece algoritmos de árvore aumentados e otimizados.
* **Rattle**: Uma ferramenta gráfica baseada em R para facilitar a exploração e a modelagem de dados.
* **Python**: O Anaconda Python vem em conjunto com algoritmos de aprendizado de máquina com bibliotecas como Scikit-learn. Pode instalar outras bibliotecas com o `pip install` comando.
* **LightGBM**: Um gradiente rápido, distribuído e de alto desempenho que aumenta a estrutura com base em algoritmos de árvore de decisão.
* **R**: Uma biblioteca avançada de funções de aprendizado de máquina está disponível para o R. Algumas das bibliotecas pré-instaladas são LM, GLM, randomForest, rpart. Outras bibliotecas podem ser instaladas ao executar:
  
        install.packages(<lib name>)

Eis algumas informações adicionais sobre as ferramentas de três aprendizagem primeiro na lista.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit é um sistema que utiliza técnicas, como online, hash, allreduce, reduções, learning2search, Active Directory, de aprendizagem automática e a aprendizagem interativa.

Para executar a ferramenta em um exemplo básico, use os seguintes comandos:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Existem outras, maiores demonstrações nesse diretório. Para obter mais informações sobre VW, consulte [nesta secção do GitHub](https://github.com/JohnLangford/vowpal_wabbit)e o [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost

Esta é uma biblioteca que foi concebida e otimizada para algoritmos de elevada (árvore). O objetivo dessa biblioteca é emitir os limites de computação de máquinas para extremos precisava fornecer árvore em grande escala adaptativo que é dimensionável, portáteis e precisas.

Ele é fornecido como uma linha de comando, bem como uma biblioteca de R.

Para utilizar esta biblioteca no R, pode iniciar uma sessão de R interativa (apenas digitando **R** no shell) e carregar a biblioteca.

Eis um exemplo simples que pode executar no prompt de R:

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

Para executar a linha de comandos xgboost, aqui estão os comandos para executar no shell:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Um arquivo de .model é gravado no diretório especificado. Pode encontrar informações sobre este exemplo de demonstração [no GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Para obter mais informações sobre xgboost, consulte a [página de documentação xgboost](https://xgboost.readthedocs.org/en/latest/)e a respetiva [repositório do GitHub](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle

Rattle (a **R** **uma**nalíticos **T**ool **T**s **L**ganhar **E** asily) utiliza dados baseados em GUI exploração e modelação. Ele Apresenta resumos de estatísticos e visual de dados, transformações de dados que podem ser modelados prontamente, cria modelos supervisionados e supervisionados dos dados, apresenta o desempenho dos modelos graficamente, e conjuntos de dados de novas pontuações. Ele também gera um código de R, replicar as operações na interface de Usuário que podem ser executadas diretamente no R ou utilizadas como um ponto de partida para análises posteriores.

Para executar Rattle, terá de estar numa gráfica desktop início de sessão. No terminal, escreva ```R``` para introduzir o ambiente de R. No prompt de R, introduza os seguintes comandos:

```R
library(rattle)
rattle()
```

Agora uma interface gráfica abre-se com um conjunto de guias. Aqui estão as etapas de início rápido em Rattle necessárias para usar um conjunto de dados meteorológico de exemplo e criar um modelo. Em alguns dos passos abaixo, é solicitado a instalar e carregar alguns pacotes R necessários que não estão já no sistema automaticamente.

> [!NOTE]
> Se não tiver acesso para instalar o pacote no diretório do sistema (predefinição), pode ver uma mensagem na janela da consola de R para instalar pacotes para sua biblioteca pessoal. Resposta *y* se vir que esses prompts.

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

* O [ciência de dados na máquina de Virtual de ciência de dados para Linux](linux-dsvm-walkthrough.md) instruções mostram como realizar várias tarefas de ciência de dados comuns com a VM de ciência de dados do Linux aprovisionados aqui. 
* Explore as várias ferramentas de ciência de dados na VM de ciência de dados por experimentar as ferramentas descritas neste artigo. Também pode executar *dsvm-mais-info* no shell na máquina virtual para uma introdução básica e ponteiros para obter mais informações sobre as ferramentas instaladas na VM.  
* Aprenda a criar soluções de análise de ponto-a-ponto sistematicamente utilizando o [Team Data Science Process](https://aka.ms/tdsp).
* Visite o [Galeria de IA do Azure](https://gallery.azure.ai/) para machine learning e os dados analytics exemplos que utilizam os serviços de IA do Azure.
