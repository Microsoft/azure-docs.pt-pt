---
title: Criar uma máquina de Virtual de ciência de dados do Windows
titleSuffix: Azure
description: Configurar e criar uma máquina de Virtual de ciência de dados no Azure para análise e machine learning.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: gokuma
ms.openlocfilehash: 0b8349c6c59ac64be15c75b28c083a4483f2f68a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57896267"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>Aprovisionar uma máquina de Virtual de ciência dados Windows no Azure

A Máquina Virtual de ciência do Microsoft Windows dados (DSVM) é uma imagem de máquina virtual (VM) do Windows Server 2016 no Azure que vem pré-instalada e configurada com ferramentas para análise de dados e machine learning.

## <a name="included-data-science-tools"></a>Ferramentas de ciência de dados incluídos

As ferramentas seguintes estão incluídas numa DSVM do:

* [O serviço do Azure Machine Learning](../service/index.yml) Python SDK
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer edition
* Distribuição do anaconda Python
* Bloco de notas do Jupyter com o R, Python e PySpark kernels
* Comunidade do Microsoft Visual Studio
* Microsoft Power BI Desktop
* Microsoft SQL Server 2017 Developer edition
* Uma instância do Apache Spark autónoma para o local de desenvolvimento e teste
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning e dados de ferramentas de análise:
  * Estruturas de aprendizagem profunda - um conjunto avançado de fotogramas de IA estão incluídas na VM: [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet e Keras
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) – uma sistema que oferece suporte a técnicas como hash online, allreduce, reduções, learning2search e aprendizagem interativa e Active Directory de aprendizagem rápida
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) -uma ferramenta que fornece a implementação de árvore precisos e de elevada
  * [Rattle](https://togaware.com/rattle/) -a ferramenta de análise de R que permite-lhe começar a utilizar a análise de dados e aprendizagem automática em R. Ele inclui a exploração e modelação com a geração automática de código R dados baseados em GUI.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/) -mineração de dados visual e de machine learning software em Java
  * [Apache Drill](https://drill.apache.org/) -um motor de consulta SQL sem esquema para o Apache Hadoop, NoSQL e armazenamento na cloud. Ele oferece suporte a interfaces ODBC e JDBC para consultar o NoSQL e de ficheiros a partir de ferramentas de BI padrão, como o Power BI, o Microsoft Excel e o Tableau.
* Bibliotecas em R e Python para utilizam no Azure Machine Learning e outros serviços do Azure
* Git, incluindo o Git Bash, trabalhar com repositórios de código de origem que incluem o GitHub e do Azure DevOps. Git fornece várias Linux da linha de comandos utilitários populares que são acessíveis no Git Bash e de um prompt de comando. Os exemplos são awk, PO, perl, grep, find, wget e curl.

### <a name="about-data-science"></a>Sobre a ciência de dados

Ciência de dados envolve a iteração numa seqüência de tarefas:

1. Localizar, carregar e processar previamente os dados
1. Criar e testar modelos
1. Implementar modelos para consumo em aplicações inteligentes

Os cientistas de dados usar várias ferramentas para essas tarefas. Pode ser demorado para encontrar as versões apropriadas de software e, em seguida, transferir e instalá-los. A DSVM poupa tempo ao fornecer uma imagem de prontos a utilizar que pode ser aprovisionada no Azure com várias ferramentas populares pré-instalada e configurada.

A DSVM sejam o projeto da análise. Pode trabalhar nas tarefas em várias linguagens, incluindo o R, Python, SQL e c#. Visual Studio fornece um ambiente fácil de usar de desenvolvimento integrado (IDE) para desenvolver e testar seu código. O SDK do Azure está incluído na VM para que possa criar as suas aplicações através de vários serviços de plataforma de cloud da Microsoft.

Existem não existem custos de software para esta imagem VM de ciência de dados. Paga apenas as tarifas de utilização do Azure. Consoante o tamanho da máquina virtual, aprovisionar. Obter mais detalhes sobre as tarifas de computação estão no **os detalhes dos preços** secção sobre o [máquina de Virtual de ciência de dados](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) página.

### <a name="other-dsvm-versions"></a>Outras versões do DSVM

* Uma [Ubuntu](dsvm-ubuntu-intro.md) imagem. Ele tem muitas ferramentas similares a DSVM mais algumas estruturas de aprendizagem de profunda adicionais.
* R [Linux CentOS](linux-dsvm-intro.md) imagem.
* O [edition do Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) da máquina de Virtual de ciência de dados. Algumas ferramentas estão disponíveis apenas na edição Windows Server 2016. Caso contrário, este artigo também se aplica para a edição do Windows Server 2012.

## <a name="prerequisite"></a>Pré-requisito

Para criar uma máquina de Virtual de ciência de dados do Microsoft, tem de ter uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Criar a sua DSVM

Para criar uma instância DSVM:

1. Vá para a máquina virtual listagem sobre o [portal do Azure](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Poderá ser-lhe pedido para iniciar sessão sua conta do Azure se não tiver entrado.
1. Selecione o **criar** na parte inferior.

   ![Configurar-data--vm de ciência](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Tem de introduzir as informações seguintes para configurar cada um dos passos apresentados no painel direito da captura de ecrã:

   1. **Noções básicas**:
      * **Nome**: o nome da DSVM
      * **Tipo de disco VM**: qualquer **SSD** ou **HDD**. Para uma instância de NC_v1 GPU como NVidia Tesla K80 com base, escolha **HDD** como o tipo de disco.
      * **Nome de utilizador**: o ID de conta de administrador
      * **Palavra-passe**: a palavra-passe da conta de administrador
      * **Subscrição**: Se tiver mais de uma subscrição, selecione aquele no qual a máquina está a ser criado e faturadas.
      * **Grupo de Recursos**. Pode criar um novo ou utilizar um grupo existente.
      * **Localização**. Selecione o Centro de dados mais adequada. Para acesso de rede mais rápido, é o Centro de dados que tem a maior parte dos seus dados ou mais se aproxima-se à sua localização física.
   1. **Tamanho**: Selecione um dos tipos de servidor que cumpra os requisitos funcionais e restrições de custo. Para mais opções de tamanhos de VM, selecione **ver tudo**.
   1. **Definições**:  
      * **Utilizar discos geridos**. Escolher **gerida** se pretender que o Azure para gerir os discos da VM. Caso contrário, tem de especificar uma conta de armazenamento nova ou existente.  
      * **Outros parâmetros**. Pode utilizar os valores predefinidos. Se pretender utilizar os valores não predefinidos, coloque o cursor sobre a ligação informativa para obter ajuda sobre campos específicos.
   1. **Resumo**: Certifique-se de que todas as informações que introduziu estão corretas. Selecione **Criar**.

> [!NOTE]
> * A VM não existem custos adicionais para além do custo de computação para o tamanho de servidor que selecionou no **tamanho** passo.
> * O aprovisionamento demora cerca de 10 a 20 minutos. Pode ver o estado da sua VM no portal do Azure.

## <a name="how-to-access-the-dsvm"></a>Como aceder a DSVM

Depois da VM é criada e aprovisionada, pode ambiente de trabalho remoto para o mesmo com as credenciais de conta de administrador que configurou no anterior **Noções básicas** secção. Está pronto para começar a utilizar as ferramentas que são instaladas e configuradas na VM. Muitas das ferramentas podem ser acedidas através de mosaicos do menu Iniciar e ícones de área de trabalho.

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Ferramentas instaladas numa máquina Virtual Microsoft Data Science

Saiba mais sobre as ferramentas fornecidas instaladas no DSVM:

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Pode utilizar o Microsoft Enterprise Library para dimensionável R ou Python para a sua análise porque o Machine Learning Server Developer edition está instalado na VM. Anteriormente conhecido como Microsoft R Server, o Machine Learning Server é uma plataforma de análise de nível empresarial amplamente implementável. Está disponível para R e Python. Também é dimensionável, comercialmente suportada e seguro.

Machine Learning Server suporta várias estatísticas de macrodados, modelação preditiva e machine learning tarefas. Ele oferece suporte a gama completa de análise: exploração, análise, visualização e modelação. Ao utilizar e alargar o R e Python de código-fonte aberto, o Machine Learning Server é compatível com scripts R e Python e funções. Também é compatível com CRAN, pip e pacotes de Conda para analisar os dados à escala empresarial.

Machine Learning Server resolve as limitações de memória do R de código aberto ao adicionar paralelo e processamento em partes de dados. Isso significa que pode executar a análise em volume de dados maior do que o que se encaixa na memória principal. O Visual Studio Community está incluído na VM. Ele tem as ferramentas R para Visual Studio e ferramentas do Python para extensões do Visual Studio (PTVS) que fornecem um IDE completo para trabalhar com o R ou Python. Também fornecemos como outros IDEs [RStudio](http://www.rstudio.com) e [edição de Comunidade PyCharm](https://www.jetbrains.com/pycharm/) na VM.

### <a name="python"></a>Python

Para o desenvolvimento com o Python, Distribuições Anaconda Python 2.7 e 3.6 são instaladas. Nessas distribuições tem o Python base juntamente com aproximadamente 300 dos pacotes de análises de dados, engenharia e matemática mais populares. Pode usar PTVS, que são instaladas no Visual Studio Community 2017. Ou pode usar um dos IDEs agrupados com Anaconda como INATIVA ou Spyder. Procure e inicie um desses pacotes (Win + S).

> [!NOTE]
> Para apontar as ferramentas Python para Visual Studio em Anaconda Python 2.7, terá de criar ambientes personalizados para cada versão. Para definir esses caminhos de ambiente no Visual Studio 2017 Community, navegue para **ferramentas** > **ferramentas do Python** > **ambientes do Python**. Em seguida, selecione **+ personalizado**.

Anaconda Python 3.6 é instalado em **C:\Anaconda**. Anaconda Python 2.7 é instalado em **c:\Anaconda\envs\python2**. Para obter passos detalhados, consulte [documentação das PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>O bloco de notas do Jupyter

Distribuição de anaconda também vem com o bloco de notas do Jupyter, um ambiente para partilhar código e análise. O servidor de bloco de notas do Jupyter está pré-configurada com kernels Python 2.7 Python 3.x, PySpark, Julia e R. Para iniciar o servidor do Jupyter e iniciar o browser para aceder ao servidor de bloco de notas, utilize o **bloco de notas do Jupyter** ícone de área de trabalho.

Podemos empacotar vários blocos de notas de exemplo em Python e R. Depois de acessar o Jupyter, os blocos de notas mostram como trabalhar com as seguintes tecnologias:

* Machine Learning Server
* Serviços do Machine Learning do SQL Server, na base de dados analytics
* Python
* Microsoft Cognitive ToolKit
* TensorFlow
* Outras tecnologias do Azure

Pode encontrar a ligação para os exemplos na home page do bloco de notas depois de autenticar para o bloco de notas do Jupyter com a palavra-passe que criou no passo anterior.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

A DSVM inclui o Visual Studio Community. Esta é uma versão gratuita do IDE popular da Microsoft que pode utilizar para fins de avaliação e pequenas equipes. Consulte a [termos de licenciamento](https://www.visualstudio.com/support/legal/mt171547).

Abra o Visual Studio, utilizando o ícone de área de trabalho ou o **iniciar** menu. Procurar programas (Win + S), seguido **Visual Studio**. A partir daí, pode criar projetos em linguagens como c#, Python, R e node. js. Plug-ins instalados torna conveniente para trabalhar com os serviços do Azure seguintes:

* Catálogo de Dados do Azure
* O Azure HDInsight Hadoop e Spark
* Azure Data Lake

Há também um plug-in chamado **do Azure Machine Learning para Visual Studio Code** forma totalmente integrada que integra-se ao Azure Machine Learning e ajuda-o rapidamente aplicações de ia de compilação.

> [!NOTE]
> Poderá receber uma mensagem que o período de avaliação expirou. Introduza as credenciais da conta Microsoft. Ou crie uma nova conta gratuita para obter acesso ao Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition

A DSVM vem com uma versão de desenvolvedor do SQL Server 2017 com o Machine Learning Services. Esta edição do SQL Server vem em R ou Python e pode executar a análise na base de dados. Serviços de Machine Learning oferece uma plataforma para desenvolver e implementar aplicações inteligentes. Pode utilizar estas linguagens e muitos pacotes da Comunidade para criar modelos e gerar previsões de indisponibilidade para os seus dados do SQL Server. Pode manter analytics próximas dos dados, porque os serviços do Machine Learning, na base de dados, integra-se o R e Python idiomas dentro do SQL Server. Essa integração elimina o custo e os riscos de segurança associados ao movimento de dados.

> [!NOTE]
> A edição do SQL Server Developer é apenas para fins de desenvolvimento e teste. Precisa de uma licença para executá-lo em produção.

Pode acessar o SQL Server, iniciando o Microsoft SQL Server Management Studio. O nome da VM é preenchido como o **nome do servidor**. Utilize a autenticação do Windows quando iniciar sessão como administrador no Windows. Quando estiver no SQL Server Management Studio, pode criar outros utilizadores, criar bases de dados, importar dados e executar consultas SQL.

Para ativar a análise de na base de dados utilizando os serviços de Machine Learning do SQL, execute o seguinte comando como uma ação única no SQL Server Management Studio depois de iniciar sessão como administrador do servidor:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Substitua `%COMPUTERNAME%` com o nome da VM.

### <a name="azure"></a>Azure

Várias ferramentas do Azure são instaladas na VM:

* Um atalho de desktop direciona-o para a documentação do SDK do Azure.
* Uso **AzCopy** para copiar dados de sua conta de armazenamento do Azure. Para ver a utilização, introduza **Azcopy** no prompt de comando.
* Uso **Explorador de armazenamento do Azure** para procurar objetos que armazena na sua conta de armazenamento do Azure. Também copia dados de e para o armazenamento do Azure. Para aceder a esta ferramenta, introduza **Explorador de armazenamento** no **pesquisa** campo. Ou encontrá-lo no Windows **iniciar** menu.
* **Adlcopy** copia os dados para o Azure Data Lake. Para ver a utilização, introduza **adlcopy** numa linha de comandos.
* **dtui** copia dados de e para o Azure Cosmos DB, base de dados NoSQL na cloud. Introduza **dtui** numa linha de comandos.
* **O Azure Data Factory Integration Runtime** copia dados entre origens de dados no local e na cloud. Ele é usado em ferramentas como o Azure Data Factory.
* Uso **do Microsoft Azure PowerShell** para administrar os recursos do Azure na linguagem de script do PowerShell. Também é instalado na sua VM.

### <a name="power-bi"></a>Power BI

A DSVM vem com **Power BI Desktop** instalado para o ajudar a criar dashboards e visualizações. Utilize esta ferramenta para extrair os dados de diferentes origens, para criar os seus dashboards e relatórios e publique-os para a cloud. Para obter mais informações, consulte a [Power BI](https://powerbi.microsoft.com) site. Pode encontrar o Power BI Desktop sobre a **iniciar** menu.

> [!NOTE]
> Precisa de uma conta do Microsoft Office 365 para aceder ao Power BI.

### <a name="azure-machine-learning-service-python-sdk"></a>Serviço de Machine Learning do Azure Python SDK

Os cientistas de dados e programadores de inteligência artificial utilizam o Azure Machine Learning SDK para Python para criar e executar fluxos de trabalho do machine learning com o [serviço Azure Machine Learning](../service/overview-what-is-azure-ml.md). Pode interagir com o serviço em blocos de notas do Jupyter ou outro IDE de Python, utilizando as estruturas de open source, como o TensorFlow e scikit-Saiba mais.

Para começar a utilizar o SDK de Python, veja [utilizar o Python para começar a utilizar com o Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

O SDK de Python está pré-instalado na máquina Virtual Microsoft dados ciência.

## <a name="more-microsoft-development-tools"></a>Mais ferramentas de desenvolvimento da Microsoft

Pode utilizar o [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) para procurar e transferir a outras ferramentas de desenvolvimento da Microsoft. Também é um atalho para a ferramenta na área de trabalho de máquina de Virtual de ciência de dados do Microsoft.  

## <a name="important-directories-on-the-vm"></a>Diretórios importantes na VM

| Item | Diretório |
| --- | --- |
| Configurações de servidor do bloco de notas do Jupyter | C:\ProgramData\jupyter |
| Diretório de raiz de exemplos do bloco de notas do Jupyter | C:\dsvm\notebooks e c:\users\\< nome de utilizador\>\notebooks |
| Outros exemplos | C:\dsvm\samples |
| Anaconda, predefinição: Python 3.6 | C:\Anaconda |
| Ambiente do anaconda Python 2.7 | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Python de servidor (autónomo) | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Instância predefinida do R, Machine Learning Server (autónomo) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| Diretório de instância na base de dados de serviços de Machine Learning do SQL | C:\Program Files\Microsoft SQL Server\MSSQL14. MSSQLSERVER |
| Diversas ferramentas | C:\dsvm\tools |

> [!NOTE]
> A edição do Windows Server 2012 da edição DSVM e Windows Server 2016 antes de Março de 2018, o ambiente de Anaconda predefinido é Python 2.7. O ambiente secundário é Python 3.5, localizado em **C:\Anaconda\envs\py35**.

## <a name="next-steps"></a>Passos Seguintes

* Explore as ferramentas na VM de ciência de dados ao selecionar o **iniciar** menu.
* Saiba mais sobre o serviço Azure Machine Learning lendo [o que é o serviço Azure Machine Learning?](../service/overview-what-is-azure-ml.md) e experimentar o [inícios rápidos e tutoriais](../service/index.yml) que estão disponíveis.
* No Explorador de ficheiros, navegue até **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** para exemplos que utilizam a biblioteca de pacotes RevoScaleR no R que suporta as análises de dados à escala empresarial.  
* Leia o artigo [dez coisas que pode fazer na máquina Virtual de ciência de dados](https://aka.ms/dsvmtenthings).
* Aprenda a criar soluções de análise de ponto-a-ponto sistematicamente utilizando o [Team Data Science Process](../team-data-science-process/index.yml).
* Visite o [Galeria de IA do Azure](https://gallery.cortanaintelligence.com) para machine learning e os dados analytics de exemplo que utilizam o Azure Machine Learning e os dados relacionados de serviços no Azure. Também fornecemos um ícone para esta galeria sobre o **iniciar** menu e o ambiente de trabalho da máquina virtual.
