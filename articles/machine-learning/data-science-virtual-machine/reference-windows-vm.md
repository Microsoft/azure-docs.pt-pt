---
title: 'Referência: DSVM do Windows'
description: Detalhes sobre as ferramentas incluídas no Windows VM de Ciência de Dados
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: be4397de477891f2a698c9f7dcb131da79479529
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200023"
---
# <a name="reference-windows-data-science-virtual-machine"></a>Referência: Máquina Virtual de Ciência de Dados do Windows

Veja abaixo uma lista de ferramentas disponíveis no seu Máquina Virtual de Ciência de Dados do Windows. 

## <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Você pode usar o Microsoft Enterprise Library para sua análise porque o Machine Learning Server Developer Edition está instalado na VM. Anteriormente conhecido como Microsoft R Server, Machine Learning Server é uma plataforma de análise amplamente implantável. Ele é escalonável e tem suporte comercial.

Machine Learning Server suporta várias estatísticas de macrodados, modelação preditiva e machine learning tarefas. Ele oferece suporte a gama completa de análise: exploração, análise, visualização e modelação. Ao utilizar e alargar o R e Python de código-fonte aberto, o Machine Learning Server é compatível com scripts R e Python e funções. Também é compatível com CRAN, pip e pacotes de Conda para analisar os dados à escala empresarial.

Machine Learning Server resolve as limitações de memória do R de código aberto ao adicionar paralelo e processamento em partes de dados. Isso significa que você pode executar a análise em dados muito maiores do que o que se ajusta à memória principal. 

O Visual Studio Community está incluído na VM. Ele tem as ferramentas do R para o Visual Studio e as extensões Ferramentas Python para Visual Studio (PTVS) que fornecem um IDE completo para trabalhar com R ou Python. Também fornecemos outros IDEs como o [RStudio](https://www.rstudio.com) e o [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) na VM.

## <a name="python"></a>Python

Para o desenvolvimento usando Python, as distribuições do Anaconda Python 2,7 e 3,6 estão instaladas. Nessas distribuições tem o Python base juntamente com aproximadamente 300 dos pacotes de análises de dados, engenharia e matemática mais populares. Você pode usar o PTVS, que é instalado no Visual Studio Community 2017. Ou você pode usar um dos IDEs agrupados com Anaconda, como IDLE ou Spyder. Pesquise e abra um desses pacotes (chave do logotipo do Windows + S).

> [!NOTE]
> Para apontar as ferramentas Python para Visual Studio em Anaconda Python 2.7, terá de criar ambientes personalizados para cada versão. Para definir esses caminhos de ambiente na Comunidade do Visual Studio 2017, vá para **ferramentas** > **Python ferramentas** > do Python**ambientes**. Em seguida, selecione **+ personalizado**.

O Anaconda Python 3,6 está instalado em C:\Anaconda. O Anaconda Python 2,7 está instalado em C:\Anaconda\envs\python2. Para obter etapas detalhadas, consulte a [documentação do PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

## <a name="the-jupyter-notebook"></a>O bloco de notas do Jupyter

A distribuição Anaconda também vem com o Jupyter Notebook, um ambiente para compartilhar código e análise. O servidor de bloco de notas do Jupyter está pré-configurada com kernels Python 2.7 Python 3.x, PySpark, Julia e R. Para iniciar o servidor Jupyter e abrir o navegador para acessar o servidor de notebook, use o ícone de área de trabalho **Jupyter Notebook** .

Podemos empacotar vários blocos de notas de exemplo em Python e R. Depois de acessar o Jupyter, os blocos de notas mostram como trabalhar com as seguintes tecnologias:

* Machine Learning Server
* SQL Server Serviços de Machine Learning, análise no banco de dados
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Outras tecnologias do Azure

Você pode encontrar o link para os exemplos no bloco de anotações home page depois de se autenticar no Jupyter Notebook usando a senha que você criou anteriormente.

## <a name="visual-studio-community-2017"></a>Comunidade do Visual Studio 2017

O DSVM inclui o Visual Studio Community. Esta é uma versão gratuita do IDE popular da Microsoft que você pode usar para fins de avaliação e pequenas equipes. Consulte os [termos de licença para software Microsoft](https://www.visualstudio.com/support/legal/mt171547).

Abra o Visual Studio usando o ícone da área de trabalho ou o menu **Iniciar** . Pesquise por programas (chave do logotipo do Windows + S), seguido pelo **Visual Studio**. A partir daí, você pode criar projetos em linguagens como C#, Python, R e node. js. Plug-ins instalados torna conveniente para trabalhar com os serviços do Azure seguintes:

* Catálogo de Dados do Azure
* Azure HDInsight para Hadoop e Spark
* Azure Data Lake

Um plug-in chamado Azure Machine Learning para Visual Studio Code também se integra ao Azure Machine Learning e o ajuda a criar rapidamente aplicativos de ia.

> [!NOTE]
> Poderá receber uma mensagem que o período de avaliação expirou. Introduza as credenciais da conta Microsoft. Ou crie uma nova conta gratuita para obter acesso ao Visual Studio Community.

## <a name="sql-server-2017-developer-edition"></a>Edição do SQL Server 2017 Developer

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

## <a name="azure"></a>Azure

Várias ferramentas do Azure são instaladas na VM:

* Um atalho de desktop direciona-o para a documentação do SDK do Azure.
* Use AzCopy para copiar dados para dentro e fora da sua conta de armazenamento do Azure. Para ver a utilização, introduza **Azcopy** no prompt de comando.
* Use Gerenciador de Armazenamento do Azure para navegar pelos objetos que você armazena em sua conta de armazenamento do Azure. Ele também copia dados de e para o armazenamento do Azure. Para acessar essa ferramenta, insira **Gerenciador de armazenamento** no campo de **pesquisa** . Ou encontrá-lo sobre o Windows **iniciar** menu.
* AdlCopy copia dados para Azure Data Lake. Para ver a utilização, introduza **adlcopy** numa linha de comandos.
* A ferramenta dtui copia dados de e para Azure Cosmos DB, um banco de dado NoSQL na nuvem. Introduza **dtui** numa linha de comandos.
* O Integration Runtime copia dados entre fontes de dados locais e a nuvem. Ele é usado em ferramentas como o Azure Data Factory.
* Use Azure PowerShell para administrar os recursos do Azure na linguagem de script do PowerShell. Também é instalado na sua VM.

## <a name="power-bi"></a>Power BI

O DSVM vem com o Power BI Desktop instalado para ajudá-lo a criar painéis e visualizações. Utilize esta ferramenta para extrair os dados de diferentes origens, para criar os seus dashboards e relatórios e publique-os para a cloud. Para obter mais informações, consulte o [site Power bi](https://powerbi.microsoft.com). Você pode encontrar Power BI Desktop no menu **Iniciar** .

> [!NOTE]
> Precisa de uma conta do Microsoft Office 365 para aceder ao Power BI.

## <a name="azure-machine-learning-sdk-for-python"></a>SDK do Azure Machine Learning para Python

Cientistas de dados e desenvolvedores de ia usam o SDK Azure Machine Learning para Python para compilar e executar fluxos de trabalho de aprendizado de máquina com o [serviço Azure Machine Learning](../service/overview-what-is-azure-ml.md). Você pode interagir com o serviço em notebooks Jupyter ou outro IDE Python usando estruturas de código-fonte aberto, como TensorFlow e scikit-learn.

O SDK de Python está pré-instalado na máquina Virtual Microsoft dados ciência. Para começar a usar o SDK do Python, consulte [usar o Python para começar com Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

## <a name="more-microsoft-development-tools"></a>Mais ferramentas de desenvolvimento da Microsoft

Você pode usar o [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) para localizar e baixar outras ferramentas de desenvolvimento da Microsoft. Também há um atalho para a ferramenta na área de trabalho do Microsoft Máquina Virtual de Ciência de Dados.  

## <a name="important-directories-on-the-virtual-machine"></a>Diretórios importantes na máquina virtual

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

## <a name="next-steps"></a>Passos seguintes

Tem mais dúvidas? Considere criar um [tíquete de suporte](https://azure.microsoft.com/support/create-ticket/).
