---
title: 'Início rápido: Criar um Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Configurar e criar uma máquina de Virtual de ciência de dados no Azure para análise e machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 02/22/2019
ms.openlocfilehash: ac4b9b4d32d05083ceabd41207243eb483648baa
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278600"
---
# <a name="quickstart-set-up-a-windows-data-science-virtual-machine-on-azure"></a>Início rápido: Configurar um Máquina Virtual de Ciência de Dados do Windows no Azure

O Microsoft Windows Máquina Virtual de Ciência de Dados (DSVM) é uma imagem de máquina virtual (VM) do Windows Server 2016 no Azure. Ele vem pré-instalado e configurado com ferramentas para análise de dados e aprendizado de máquina.

## <a name="included-data-science-tools"></a>Ferramentas de ciência de dados incluídas

As seguintes ferramentas estão incluídas em um DSVM:

* SDK do Python para o [serviço de Azure Machine Learning](../index.yml).
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer edition.
* Distribuição do anaconda Python.
* Bloco de notas do Jupyter com o R, Python e PySpark kernels.
* Comunidade do Microsoft Visual Studio.
* Do Microsoft Power BI Desktop.
* Edição do Microsoft SQL Server 2017 Developer.
* Uma instância do Apache Spark autónoma para o local de desenvolvimento e teste.
* [JuliaPro](https://juliacomputing.com/products/juliapro.html).
* Machine learning e dados de ferramentas de análise:
  * Estruturas de aprendizado profundo: As estruturas de ia [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), MXNet e KERAS estão incluídas na VM.
  * [Wabbit Vowpal](https://github.com/JohnLangford/vowpal_wabbit): Uma rápida sistema machine learning que oferece suporte a técnicas como hash online, allreduce, reduções, learning2search e aprendizagem interativa e Active Directory.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Uma ferramenta que fornece a implementação de árvore precisos e de elevada.
  * [Rattle](https://togaware.com/rattle/). A ferramenta analítica de R que você começou com a análise de dados e o aprendizado de máquina em R. Ele inclui a exploração de dados baseada em GUI e a modelagem com a geração automática de código R.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/): Extração de dados visual e aprendizagem automática software em Java.
  * [Análise do Apache](https://drill.apache.org/): Um sem esquema motor de consulta SQL para Apache Hadoop, NoSQL e armazenamento na cloud. Ele oferece suporte a interfaces ODBC e JDBC para consultar o NoSQL e de ficheiros a partir de ferramentas de BI padrão, como o Power BI, o Microsoft Excel e o Tableau.
* Bibliotecas em R e Python para utilizam no Azure Machine Learning e outros serviços do Azure.
* Git, incluindo git bash, para trabalhar com repositórios de código-fonte como GitHub e Azure DevOps. O Git fornece várias ferramentas de linha de comando populares do Linux que são acessíveis tanto no git bash quanto em um prompt de comando. Os exemplos são awk, PO, perl, grep, find, wget e curl.
* Ferramentas e editores de desenvolvimento (RStudio, PyCharm).

### <a name="about-data-science"></a>Sobre a ciência de dados

Ciência de dados envolve a iteração numa seqüência de tarefas:

1. Localizar, carregar e processar previamente os dados.
1. Criar e testar modelos.
1. Implemente os modelos para consumo em aplicações inteligentes.

Os cientistas de dados usar várias ferramentas para essas tarefas. Pode ser demorado para encontrar as versões apropriadas de software e, em seguida, transferir e instalá-los. O DSVM poupa tempo ao fornecer uma imagem pronta para uso que pode ser provisionada no Azure, com várias ferramentas populares instaladas e configuradas.

O DSVM Jump-inicia seu projeto de análise. Você pode trabalhar em tarefas em várias linguagens, incluindo R, Python, SQL e C#. Visual Studio fornece um ambiente fácil de usar de desenvolvimento integrado (IDE) para desenvolver e testar seu código. O SDK do Azure está incluído na VM, para que você possa criar seus aplicativos usando serviços na plataforma de nuvem da Microsoft.

Não há encargos de software para esta imagem de DSVM. Paga apenas as tarifas de utilização do Azure. Eles dependem do tamanho da máquina virtual que você provisiona. Para obter mais informações, consulte a [página máquina virtual de ciência de dados](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows).

### <a name="other-dsvm-versions"></a>Outras versões do DSVM

* Uma [Ubuntu](dsvm-ubuntu-intro.md) imagem. Ele tem ferramentas semelhantes às do DSVM, além de algumas estruturas de aprendizado profundo.
* R [Linux CentOS](linux-dsvm-intro.md) imagem.
* O [edition do Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) da máquina de Virtual de ciência de dados. Algumas ferramentas estão disponíveis apenas na edição Windows Server 2016. Caso contrário, este artigo também se aplica para a edição do Windows Server 2012.

## <a name="prerequisite"></a>Pré-requisito

Para criar uma máquina de Virtual de ciência de dados do Microsoft, tem de ter uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Criar seu DSVM

Para criar uma instância de DSVM:

1. Vá para a lista de máquinas virtuais no [portal do Azure](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Você pode ser solicitado a entrar em sua conta do Azure se ainda não tiver entrado.
1. Selecione o botão **criar** na parte inferior.

   ![Listagem de máquinas virtuais na portal do Azure, com o botão criar](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Insira as informações a seguir para configurar cada uma das etapas mostradas no painel direito da captura de tela:

   1. **Noções básicas**:
      * **Nome**: Insira o nome do DSVM.
      * **Tipo de disco da VM**: Selecione **SSD** ou **HDD**. Para uma instância de GPU NC_v1 como com base em K80 NVIDIA Tesla, escolha **HDD** como o tipo de disco.
      * **Nome de usuário**: Insira a ID da conta do administrador.
      * **Senha**: Insira a senha da conta do administrador.
      * **Subscrição**: Se você tiver mais de uma assinatura, selecione aquela em que a máquina será criada e cobrada.
      * **Grupo de recursos**: Crie um novo grupo ou use um existente.
      * **Local**: Selecione o datacenter mais apropriado. Para acesso mais rápido à rede, é o datacenter que tem a maior parte dos seus dados ou está mais próximo de sua localização física.
   1. **Tamanho**: Selecione o tipo de servidor que atende aos seus requisitos funcionais e restrições de custo. Para mais opções de tamanhos de VM, selecione **ver tudo**.
   1. **Definições**:  
      * **Utilizar discos geridos**. Escolher **gerida** se pretender que o Azure para gerir os discos da VM. Caso contrário, tem de especificar uma conta de armazenamento nova ou existente.  
      * **Outros parâmetros**. Pode utilizar os valores predefinidos. Se você quiser usar valores não padrão, passe o mouse sobre o link informativo para obter ajuda sobre os campos específicos.
   1. **Resumo**: Certifique-se de que todas as informações que introduziu estão corretas. Selecione **Criar**.

> [!NOTE]
> * A VM não incorre em nenhum encargo adicional além do custo de computação para o tamanho do servidor que você escolheu na etapa de **tamanho** .
> * O provisionamento leva de 10 a 20 minutos. Você pode exibir o status de sua VM no portal do Azure.

## <a name="access-the-dsvm"></a>Acessar o DSVM

Depois que a VM for criada e provisionada, você poderá acessá-la por meio de uma conexão de área de trabalho remota. Use as credenciais da conta de administrador que você configurou na etapa **básico** da criação de uma máquina virtual. 

Está pronto para começar a utilizar as ferramentas que são instaladas e configuradas na VM. Muitas das ferramentas podem ser acessadas por meio de blocos do menu **Iniciar** e ícones da área de trabalho.

Você também pode anexar um DSVM ao Azure Notebooks para executar blocos de anotações do Jupyter na VM e ignorar as limitações da camada de serviço gratuita. Para obter mais informações, consulte [gerenciar e configurar projetos de blocos de anotações](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

<a name="tools"></a>

## <a name="tools-installed-on-the-dvsm"></a>Ferramentas instaladas no DVSM

Nas seções a seguir, saiba mais sobre as ferramentas que vêm instaladas no Máquina Virtual de Ciência de Dados.

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Você pode usar o Microsoft Enterprise Library para sua análise porque o Machine Learning Server Developer Edition está instalado na VM. Anteriormente conhecido como Microsoft R Server, Machine Learning Server é uma plataforma de análise amplamente implantável. Ele é escalonável e tem suporte comercial.

Machine Learning Server suporta várias estatísticas de macrodados, modelação preditiva e machine learning tarefas. Ele oferece suporte a gama completa de análise: exploração, análise, visualização e modelação. Ao utilizar e alargar o R e Python de código-fonte aberto, o Machine Learning Server é compatível com scripts R e Python e funções. Também é compatível com CRAN, pip e pacotes de Conda para analisar os dados à escala empresarial.

Machine Learning Server resolve as limitações de memória do R de código aberto ao adicionar paralelo e processamento em partes de dados. Isso significa que você pode executar a análise em dados muito maiores do que o que se ajusta à memória principal. 

O Visual Studio Community está incluído na VM. Ele tem as ferramentas do R para o Visual Studio e as extensões Ferramentas Python para Visual Studio (PTVS) que fornecem um IDE completo para trabalhar com R ou Python. Também fornecemos outros IDEs como o [RStudio](https://www.rstudio.com) e o [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) na VM.

### <a name="python"></a>Python

Para o desenvolvimento usando Python, as distribuições do Anaconda Python 2,7 e 3,6 estão instaladas. Nessas distribuições tem o Python base juntamente com aproximadamente 300 dos pacotes de análises de dados, engenharia e matemática mais populares. Você pode usar o PTVS, que é instalado no Visual Studio Community 2017. Ou você pode usar um dos IDEs agrupados com Anaconda, como IDLE ou Spyder. Pesquise e abra um desses pacotes (chave do logotipo do Windows + S).

> [!NOTE]
> Para apontar as ferramentas Python para Visual Studio em Anaconda Python 2.7, terá de criar ambientes personalizados para cada versão. Para definir esses caminhos de ambiente na Comunidade do Visual Studio 2017, vá para **ferramentas** > **Python ferramentas** > do Python**ambientes**. Em seguida, selecione **+ personalizado**.

O Anaconda Python 3,6 está instalado em C:\Anaconda. O Anaconda Python 2,7 está instalado em C:\Anaconda\envs\python2. Para obter etapas detalhadas, consulte a [documentação do PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>O bloco de notas do Jupyter

A distribuição Anaconda também vem com o Jupyter Notebook, um ambiente para compartilhar código e análise. O servidor de bloco de notas do Jupyter está pré-configurada com kernels Python 2.7 Python 3.x, PySpark, Julia e R. Para iniciar o servidor Jupyter e abrir o navegador para acessar o servidor de notebook, use o ícone de área de trabalho **Jupyter Notebook** .

Podemos empacotar vários blocos de notas de exemplo em Python e R. Depois de acessar o Jupyter, os blocos de notas mostram como trabalhar com as seguintes tecnologias:

* Machine Learning Server
* SQL Server Serviços de Machine Learning, análise no banco de dados
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Outras tecnologias do Azure

Você pode encontrar o link para os exemplos no bloco de anotações home page depois de se autenticar no Jupyter Notebook usando a senha que você criou anteriormente.

### <a name="visual-studio-community-2017"></a>Comunidade do Visual Studio 2017

O DSVM inclui o Visual Studio Community. Esta é uma versão gratuita do IDE popular da Microsoft que você pode usar para fins de avaliação e pequenas equipes. Consulte os [termos de licença para software Microsoft](https://www.visualstudio.com/support/legal/mt171547).

Abra o Visual Studio usando o ícone da área de trabalho ou o menu **Iniciar** . Pesquise por programas (chave do logotipo do Windows + S), seguido pelo **Visual Studio**. A partir daí, você pode criar projetos em linguagens como C#, Python, R e node. js. Plug-ins instalados torna conveniente para trabalhar com os serviços do Azure seguintes:

* Catálogo de Dados do Azure
* Azure HDInsight para Hadoop e Spark
* Azure Data Lake

Um plug-in chamado Azure Machine Learning para Visual Studio Code também se integra ao Azure Machine Learning e o ajuda a criar rapidamente aplicativos de ia.

> [!NOTE]
> Poderá receber uma mensagem que o período de avaliação expirou. Introduza as credenciais da conta Microsoft. Ou crie uma nova conta gratuita para obter acesso ao Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>Edição do SQL Server 2017 Developer

O DSVM vem com uma versão de desenvolvedor do SQL Server 2017 com Serviços de Machine Learning. Essa edição de SQL Server vem em R ou Python e pode executar a análise no banco de dados. 

Serviços de Machine Learning oferece uma plataforma para desenvolver e implementar aplicações inteligentes. Pode utilizar estas linguagens e muitos pacotes da Comunidade para criar modelos e gerar previsões de indisponibilidade para os seus dados do SQL Server. Pode manter analytics próximas dos dados, porque os serviços do Machine Learning, na base de dados, integra-se o R e Python idiomas dentro do SQL Server. Essa integração elimina o custo e os riscos de segurança associados ao movimento de dados.

> [!NOTE]
> A edição do SQL Server Developer é apenas para fins de desenvolvimento e teste. Precisa de uma licença para executá-lo em produção.

Você pode acessar SQL Server abrindo Microsoft SQL Server Management Studio. O nome da VM é populado como **nome do servidor**. Utilize a autenticação do Windows quando iniciar sessão como administrador no Windows. Quando estiver no SQL Server Management Studio, pode criar outros utilizadores, criar bases de dados, importar dados e executar consultas SQL.

Para habilitar a análise no banco de dados usando SQL Server Serviços de Machine Learning, execute o seguinte comando como uma ação única no SQL Server Management Studio depois de entrar como o administrador do servidor:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Substitua `%COMPUTERNAME%` pelo nome da VM.

### <a name="azure"></a>Azure

Várias ferramentas do Azure são instaladas na VM:

* Um atalho de desktop direciona-o para a documentação do SDK do Azure.
* Use AzCopy para copiar dados para dentro e fora da sua conta de armazenamento do Azure. Para ver a utilização, introduza **Azcopy** no prompt de comando.
* Use Gerenciador de Armazenamento do Azure para navegar pelos objetos que você armazena em sua conta de armazenamento do Azure. Ele também copia dados de e para o armazenamento do Azure. Para acessar essa ferramenta, insira **Gerenciador de armazenamento** no campo de **pesquisa** . Ou encontrá-lo sobre o Windows **iniciar** menu.
* AdlCopy copia dados para Azure Data Lake. Para ver a utilização, introduza **adlcopy** numa linha de comandos.
* A ferramenta dtui copia dados de e para Azure Cosmos DB, um banco de dado NoSQL na nuvem. Introduza **dtui** numa linha de comandos.
* O Integration Runtime copia dados entre fontes de dados locais e a nuvem. Ele é usado em ferramentas como o Azure Data Factory.
* Use Azure PowerShell para administrar os recursos do Azure na linguagem de script do PowerShell. Também é instalado na sua VM.

### <a name="power-bi"></a>Power BI

O DSVM vem com o Power BI Desktop instalado para ajudá-lo a criar painéis e visualizações. Utilize esta ferramenta para extrair os dados de diferentes origens, para criar os seus dashboards e relatórios e publique-os para a cloud. Para obter mais informações, consulte o [site Power bi](https://powerbi.microsoft.com). Você pode encontrar Power BI Desktop no menu **Iniciar** .

> [!NOTE]
> Precisa de uma conta do Microsoft Office 365 para aceder ao Power BI.

### <a name="azure-machine-learning-sdk-for-python"></a>SDK do Azure Machine Learning para Python

Cientistas de dados e desenvolvedores de ia usam o SDK Azure Machine Learning para Python para compilar e executar fluxos de trabalho de aprendizado de máquina com o [serviço Azure Machine Learning](../service/overview-what-is-azure-ml.md). Você pode interagir com o serviço em notebooks Jupyter ou outro IDE Python usando estruturas de código-fonte aberto, como TensorFlow e scikit-learn.

O SDK de Python está pré-instalado na máquina Virtual Microsoft dados ciência. Para começar a usar o SDK do Python, consulte [usar o Python para começar com Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

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
| Microsoft Machine Learning Server (autônomo) para Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Instância R padrão, Machine Learning Server (autônomo) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL Server Serviços de Machine Learning diretório na instância do banco de dados | C:\Program Files\Microsoft SQL Server\MSSQL14. MSSQLSERVER |
| Diversas ferramentas | C:\dsvm\tools |

> [!NOTE]
> Na edição do Windows Server 2012 do DSVM e do Windows Server 2016 Edition antes de março de 2018, o ambiente Anaconda padrão é Python 2,7. O ambiente secundário é Python 3,5, localizado em C:\Anaconda\envs\py35.

## <a name="next-steps"></a>Passos Seguintes

* Explore as ferramentas no DSVM abrindo o menu **Iniciar** .
* Saiba mais sobre o serviço de Azure Machine Learning lendo [o que é o serviço de Azure Machine Learning?](../service/overview-what-is-azure-ml.md) e experimentando [tutoriais](../index.yml).
* No explorador de arquivos, navegue até C:\Program Programas\microsoft\ml Server\R_SERVER\library\RevoScaleR\demoScripts para obter exemplos que usam a biblioteca RevoScaleR em R que dá suporte à análise de dados em escala empresarial. 
* Leia o artigo [dez coisas que você pode fazer no máquina virtual de ciência de dados](https://aka.ms/dsvmtenthings).
* Aprenda a criar soluções de análise de ponto-a-ponto sistematicamente utilizando o [Team Data Science Process](../team-data-science-process/index.yml).
* Visite o [Galeria de IA do Azure](https://gallery.cortanaintelligence.com) para machine learning e os dados analytics de exemplo que utilizam o Azure Machine Learning e os dados relacionados de serviços no Azure. Também fornecemos um ícone para essa galeria no menu **Iniciar** e na área de trabalho da máquina virtual.
