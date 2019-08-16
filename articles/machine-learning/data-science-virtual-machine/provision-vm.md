---
title: Criar uma máquina de Virtual de ciência de dados do Windows
titleSuffix: Azure
description: Configurar e criar uma máquina de Virtual de ciência de dados no Azure para análise e machine learning.
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: 488dc7db01bd865268e143b68cdaccd989010912
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534931"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>Provisionar uma Máquina Virtual de Ciência de Dados do Windows no Azure

O Microsoft Windows Máquina Virtual de Ciência de Dados (DSVM) é uma imagem de máquina virtual (VM) do Windows Server 2016 no Azure que vem pré-instalado e configurada com ferramentas para análise de dados e aprendizado de máquina.

## <a name="included-data-science-tools"></a>Ferramentas de ciência de dados incluídas

As seguintes ferramentas estão incluídas em um DSVM:

* [Serviço de Azure Machine Learning](../service/index.yml) SDK do Python
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Distribuição do anaconda Python
* Jupyter Notebook com kernels R, Python e PySpark
* Microsoft Visual Studio Community
* Power BI Desktop da Microsoft
* Microsoft SQL Server Developer Edition 2017
* Uma instância de Apache Spark autônoma para desenvolvimento e teste locais
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning e dados de ferramentas de análise:
  * Estruturas de aprendizado profundo-um conjunto rico de estruturas de ia está incluído na VM: [Microsoft cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet e Keras
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) – um sistema de aprendizado de máquina rápido que dá suporte a técnicas como hash online, onreduz, reduções, learning2search e aprendizado ativo e interativo
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) – uma ferramenta que fornece implementação de árvore aumentada rápida e precisa
  * [Rattle](https://togaware.com/rattle/) -a ferramenta analítica de r que você começou com a análise de dados e o aprendizado de máquina em R. Ele inclui a exploração de dados baseada em GUI e a modelagem com a geração automática de código R.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/) – Visual Data Mining e software de Machine Learning em Java
  * [Apache Drill](https://drill.apache.org/) -um mecanismo de consulta SQL sem esquema para Apache Hadoop, NoSQL e armazenamento em nuvem. Ele oferece suporte a interfaces ODBC e JDBC para consultar o NoSQL e de ficheiros a partir de ferramentas de BI padrão, como o Power BI, o Microsoft Excel e o Tableau.
* Bibliotecas em R e Python para utilizam no Azure Machine Learning e outros serviços do Azure
* Git, incluindo o Git Bash, trabalhar com repositórios de código de origem que incluem o GitHub e do Azure DevOps. Git fornece várias Linux da linha de comandos utilitários populares que são acessíveis no Git Bash e de um prompt de comando. Os exemplos são awk, PO, perl, grep, find, wget e curl.
* Ferramentas e editores de desenvolvimento (RStudio, PyCharm)

### <a name="about-data-science"></a>Sobre a ciência de dados

Ciência de dados envolve a iteração numa seqüência de tarefas:

1. Localizar, carregar e pré-processar dados
1. Modelos de compilação e teste
1. Implantar os modelos de consumo em aplicativos inteligentes

Os cientistas de dados usar várias ferramentas para essas tarefas. Pode ser demorado para encontrar as versões apropriadas de software e, em seguida, transferir e instalá-los. O DSVM poupa tempo ao fornecer uma imagem pronta para uso que pode ser provisionada no Azure com várias ferramentas populares instaladas e configuradas.

O DSVM Jump-inicia seu projeto de análise. Pode trabalhar nas tarefas em várias linguagens, incluindo o R, Python, SQL e c#. Visual Studio fornece um ambiente fácil de usar de desenvolvimento integrado (IDE) para desenvolver e testar seu código. O SDK do Azure está incluído na VM para que você possa criar seus aplicativos usando vários serviços na plataforma de nuvem da Microsoft.

Existem não existem custos de software para esta imagem VM de ciência de dados. Paga apenas as tarifas de utilização do Azure. Consoante o tamanho da máquina virtual, aprovisionar. Obter mais detalhes sobre as tarifas de computação estão no **os detalhes dos preços** secção sobre o [máquina de Virtual de ciência de dados](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) página.

### <a name="other-dsvm-versions"></a>Outras versões do DSVM

* Uma [Ubuntu](dsvm-ubuntu-intro.md) imagem. Ele tem muitas ferramentas similares a DSVM mais algumas estruturas de aprendizagem de profunda adicionais.
* R [Linux CentOS](linux-dsvm-intro.md) imagem.
* O [edition do Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) da máquina de Virtual de ciência de dados. Algumas ferramentas estão disponíveis apenas na edição Windows Server 2016. Caso contrário, este artigo também se aplica para a edição do Windows Server 2012.

## <a name="prerequisite"></a>Pré-requisito

Para criar uma máquina de Virtual de ciência de dados do Microsoft, tem de ter uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Criar seu DSVM

Para criar uma instância de DSVM:

1. Vá para a lista de máquinas virtuais no [portal do Azure](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Você pode ser solicitado a entrar em sua conta do Azure se você ainda não tiver entrado.
1. Selecione o botão **criar** na parte inferior.

   ![Configurar-data--vm de ciência](./media/provision-vm/configure-data-science-virtual-machine.png)

1. É necessário inserir as seguintes informações para configurar cada uma das etapas mostradas no painel direito da captura de tela:

   1. **Noções básicas**:
      * **Nome**: o nome do DSVM
      * **Tipo de disco de VM**: **SSD** ou **HDD**. Para uma instância de NC_v1 GPU como NVidia Tesla K80 com base, escolha **HDD** como o tipo de disco.
      * **Nome de usuário**: a ID da conta do administrador
      * **Senha**: a senha da conta de administrador
      * **Assinatura**: Se tiver mais de uma subscrição, selecione aquele no qual a máquina está a ser criado e faturadas.
      * **Grupo de Recursos**. Pode criar um novo ou utilizar um grupo existente.
      * **Localização**. Selecione o Centro de dados mais adequada. Para acesso de rede mais rápido, é o Centro de dados que tem a maior parte dos seus dados ou mais se aproxima-se à sua localização física.
   1. **Tamanho**: Selecione um dos tipos de servidor que cumpra os requisitos funcionais e restrições de custo. Para mais opções de tamanhos de VM, selecione **ver tudo**.
   1. **Definições**:  
      * **Utilizar discos geridos**. Escolher **gerida** se pretender que o Azure para gerir os discos da VM. Caso contrário, tem de especificar uma conta de armazenamento nova ou existente.  
      * **Outros parâmetros**. Pode utilizar os valores predefinidos. Se você quiser usar valores não padrão, passe o mouse sobre o link informativo para obter ajuda sobre os campos específicos.
   1. **Resumo**: Certifique-se de que todas as informações que introduziu estão corretas. Selecione **Criar**.

> [!NOTE]
> * A VM não incorre em nenhum encargo adicional além do custo de computação para o tamanho do servidor que você escolheu na etapa **tamanho** .
> * O aprovisionamento demora cerca de 10 a 20 minutos. Você pode exibir o status de sua VM no portal do Azure.

## <a name="how-to-access-the-dsvm"></a>Como acessar o DSVM

Depois da VM é criada e aprovisionada, pode ambiente de trabalho remoto para o mesmo com as credenciais de conta de administrador que configurou no anterior **Noções básicas** secção. Está pronto para começar a utilizar as ferramentas que são instaladas e configuradas na VM. Muitas das ferramentas podem ser acessadas por meio de blocos do menu iniciar e ícones da área de trabalho.

Você também pode anexar um VM de Ciência de Dados ao Azure Notebooks para executar blocos de anotações do Jupyter na VM e ignorar as limitações da camada de serviço gratuita. Para obter mais informações, consulte [gerenciar e configurar projetos de blocos de anotações-camada de computação](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Ferramentas instaladas numa máquina Virtual Microsoft Data Science

Saiba mais sobre as ferramentas que vêm instaladas no DSVM:

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Pode utilizar o Microsoft Enterprise Library para dimensionável R ou Python para a sua análise porque o Machine Learning Server Developer edition está instalado na VM. Anteriormente conhecido como Microsoft R Server, Machine Learning Server é uma plataforma de análise de classe empresarial amplamente implantável. Ele está disponível para R e Python. Ele também é escalonável, com suporte comercial e seguro.

Machine Learning Server suporta várias estatísticas de macrodados, modelação preditiva e machine learning tarefas. Ele oferece suporte a gama completa de análise: exploração, análise, visualização e modelação. Ao utilizar e alargar o R e Python de código-fonte aberto, o Machine Learning Server é compatível com scripts R e Python e funções. Também é compatível com CRAN, pip e pacotes de Conda para analisar os dados à escala empresarial.

Machine Learning Server resolve as limitações de memória do R de código aberto ao adicionar paralelo e processamento em partes de dados. Isso significa que você pode executar a análise em dados muito maiores do que o que se ajusta à memória principal. O Visual Studio Community está incluído na VM. Ele tem as ferramentas do R para o Visual Studio e as extensões Ferramentas Python para Visual Studio (PTVS) que fornecem um IDE completo para trabalhar com R ou Python. Também fornecemos como outros IDEs [RStudio](https://www.rstudio.com) e [edição de Comunidade PyCharm](https://www.jetbrains.com/pycharm/) na VM.

### <a name="python"></a>Python

Para o desenvolvimento usando Python, as distribuições do Anaconda Python 2,7 e 3,6 estão instaladas. Nessas distribuições tem o Python base juntamente com aproximadamente 300 dos pacotes de análises de dados, engenharia e matemática mais populares. Pode usar PTVS, que são instaladas no Visual Studio Community 2017. Ou pode usar um dos IDEs agrupados com Anaconda como INATIVA ou Spyder. Procure e inicie um desses pacotes (Win + S).

> [!NOTE]
> Para apontar as ferramentas Python para Visual Studio em Anaconda Python 2.7, terá de criar ambientes personalizados para cada versão. Para definir esses caminhos de ambiente no Visual Studio 2017 Community, navegue para **ferramentas** > **ferramentas do Python** > **ambientes do Python**. Em seguida, selecione **+ personalizado**.

Anaconda Python 3.6 é instalado em **C:\Anaconda**. Anaconda Python 2.7 é instalado em **c:\Anaconda\envs\python2**. Para obter passos detalhados, consulte [documentação das PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>O bloco de notas do Jupyter

Distribuição de anaconda também vem com o bloco de notas do Jupyter, um ambiente para partilhar código e análise. O servidor de bloco de notas do Jupyter está pré-configurada com kernels Python 2.7 Python 3.x, PySpark, Julia e R. Para iniciar o servidor Jupyter e iniciar o navegador para acessar o servidor de notebook, use o ícone de área de trabalho **Jupyter Notebook** .

Podemos empacotar vários blocos de notas de exemplo em Python e R. Depois de acessar o Jupyter, os blocos de notas mostram como trabalhar com as seguintes tecnologias:

* Machine Learning Server
* SQL Server Serviços de Machine Learning, análise no banco de dados
* Python
* Kit de ferramentas cognitivas da Microsoft
* TensorFlow
* Outras tecnologias do Azure

Você pode encontrar o link para os exemplos no bloco de anotações home page depois de se autenticar no Jupyter Notebook usando a senha que você criou em uma etapa anterior.

### <a name="visual-studio-community-2017"></a>Comunidade do Visual Studio 2017

O DSVM inclui o Visual Studio Community. Esta é uma versão gratuita do IDE popular da Microsoft que você pode usar para fins de avaliação e pequenas equipes. Consulte a [termos de licenciamento](https://www.visualstudio.com/support/legal/mt171547).

Abra o Visual Studio usando o ícone da área de trabalho ou o menu **Iniciar** . Procurar programas (Win + S), seguido **Visual Studio**. A partir daí, pode criar projetos em linguagens como c#, Python, R e node. js. Plug-ins instalados torna conveniente para trabalhar com os serviços do Azure seguintes:

* Catálogo de Dados do Azure
* O Azure HDInsight Hadoop e Spark
* Azure Data Lake

Também há um plug-in chamado **Azure Machine Learning para Visual Studio Code** que se integra perfeitamente ao Azure Machine Learning e ajuda você a criar rapidamente aplicativos de ia.

> [!NOTE]
> Poderá receber uma mensagem que o período de avaliação expirou. Introduza as credenciais da conta Microsoft. Ou crie uma nova conta gratuita para obter acesso ao Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>Edição do SQL Server 2017 Developer

O DSVM vem com uma versão de desenvolvedor do SQL Server 2017 com Serviços de Machine Learning. Essa edição de SQL Server vem em R ou Python e pode executar a análise no banco de dados. Serviços de Machine Learning oferece uma plataforma para desenvolver e implementar aplicações inteligentes. Pode utilizar estas linguagens e muitos pacotes da Comunidade para criar modelos e gerar previsões de indisponibilidade para os seus dados do SQL Server. Pode manter analytics próximas dos dados, porque os serviços do Machine Learning, na base de dados, integra-se o R e Python idiomas dentro do SQL Server. Essa integração elimina o custo e os riscos de segurança associados ao movimento de dados.

> [!NOTE]
> A edição do SQL Server Developer é apenas para fins de desenvolvimento e teste. Precisa de uma licença para executá-lo em produção.

Pode acessar o SQL Server, iniciando o Microsoft SQL Server Management Studio. O nome da VM é preenchido como o **nome do servidor**. Utilize a autenticação do Windows quando iniciar sessão como administrador no Windows. Quando estiver no SQL Server Management Studio, pode criar outros utilizadores, criar bases de dados, importar dados e executar consultas SQL.

Para ativar a análise de na base de dados utilizando os serviços de Machine Learning do SQL, execute o seguinte comando como uma ação única no SQL Server Management Studio depois de iniciar sessão como administrador do servidor:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Substitua `%COMPUTERNAME%` pelo nome da VM.

### <a name="azure"></a>Azure

Várias ferramentas do Azure são instaladas na VM:

* Um atalho de desktop direciona-o para a documentação do SDK do Azure.
* Use **AzCopy** para copiar dados para dentro e fora da sua conta de armazenamento do Azure. Para ver a utilização, introduza **Azcopy** no prompt de comando.
* Uso **Explorador de armazenamento do Azure** para procurar objetos que armazena na sua conta de armazenamento do Azure. Ele também copia dados de e para o armazenamento do Azure. Para acessar essa ferramenta, insira **Gerenciador de armazenamento** no campo de **pesquisa** . Ou localize-o no menu **Iniciar** do Windows.
* **Adlcopy** copia dados para Azure data Lake. Para ver a utilização, introduza **adlcopy** numa linha de comandos.
* **dtui** copia dados de e para Azure Cosmos DB, um banco de dado NoSQL na nuvem. Introduza **dtui** numa linha de comandos.
* **Azure Data Factory Integration Runtime** copia dados entre fontes de dados locais e a nuvem. Ele é usado em ferramentas como o Azure Data Factory.
* Use **Microsoft Azure PowerShell** para administrar os recursos do Azure na linguagem de script do PowerShell. Também é instalado na sua VM.

### <a name="power-bi"></a>Power BI

O DSVM vem com o **Power bi desktop** instalado para ajudá-lo a criar painéis e visualizações. Utilize esta ferramenta para extrair os dados de diferentes origens, para criar os seus dashboards e relatórios e publique-os para a cloud. Para obter mais informações, consulte a [Power BI](https://powerbi.microsoft.com) site. Você pode encontrar Power BI Desktop no menu **Iniciar** .

> [!NOTE]
> Precisa de uma conta do Microsoft Office 365 para aceder ao Power BI.

### <a name="azure-machine-learning-service-python-sdk"></a>Serviço de Machine Learning do Azure Python SDK

Cientistas de dados e desenvolvedores de ia usam o SDK Azure Machine Learning para Python para compilar e executar fluxos de trabalho de aprendizado de máquina com o [serviço Azure Machine Learning](../service/overview-what-is-azure-ml.md). Você pode interagir com o serviço em notebooks Jupyter ou outro IDE Python usando estruturas de código-fonte aberto, como TensorFlow e scikit-learn.

Para começar a utilizar o SDK de Python, veja [utilizar o Python para começar a utilizar com o Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

O SDK de Python está pré-instalado na máquina Virtual Microsoft dados ciência.

## <a name="more-microsoft-development-tools"></a>Mais ferramentas de desenvolvimento da Microsoft

Você pode usar o [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) para localizar e baixar outras ferramentas de desenvolvimento da Microsoft. Também há um atalho para a ferramenta na área de trabalho do Microsoft Máquina Virtual de Ciência de Dados.  

## <a name="important-directories-on-the-vm"></a>Diretórios importantes na VM

| Item | Diretório |
| --- | --- |
| Configurações de servidor do bloco de notas do Jupyter | C:\ProgramData\jupyter |
| Diretório de raiz de exemplos do bloco de notas do Jupyter | C:\dsvm\notebooks e c:\Users\\< nome\>de usuário \notebooks |
| Outros exemplos | C:\dsvm\samples |
| Anaconda, padrão: Python 3,6 | C:\Anaconda |
| Ambiente do anaconda Python 2.7 | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Python de servidor (autónomo) | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Instância predefinida do R, Machine Learning Server (autónomo) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| Diretório de instância na base de dados de serviços de Machine Learning do SQL | C:\Program Files\Microsoft SQL Server\MSSQL14. MSSQLSERVER |
| Diversas ferramentas | C:\dsvm\tools |

> [!NOTE]
> A edição do Windows Server 2012 da edição DSVM e Windows Server 2016 antes de Março de 2018, o ambiente de Anaconda predefinido é Python 2.7. O ambiente secundário é Python 3,5, localizado em **C:\Anaconda\envs\py35**.

## <a name="next-steps"></a>Passos Seguintes

* Explore as ferramentas na VM de ciência de dados ao selecionar o **iniciar** menu.
* Saiba mais sobre o serviço Azure Machine Learning lendo [o que é o serviço Azure Machine Learning?](../service/overview-what-is-azure-ml.md) e experimentar o [inícios rápidos e tutoriais](../service/index.yml) que estão disponíveis.
* No explorador de arquivos, navegue até **C:\Program Programas\microsoft\ml Server\R_SERVER\library\RevoScaleR\demoScripts** para obter exemplos que usam a biblioteca RevoScaleR em R que dá suporte à análise de dados na escala empresarial.  
* Leia o artigo [dez coisas que pode fazer na máquina Virtual de ciência de dados](https://aka.ms/dsvmtenthings).
* Aprenda a criar soluções de análise de ponto-a-ponto sistematicamente utilizando o [Team Data Science Process](../team-data-science-process/index.yml).
* Visite o [Galeria de IA do Azure](https://gallery.cortanaintelligence.com) para machine learning e os dados analytics de exemplo que utilizam o Azure Machine Learning e os dados relacionados de serviços no Azure. Também fornecemos um ícone para esta galeria sobre o **iniciar** menu e o ambiente de trabalho da máquina virtual.
