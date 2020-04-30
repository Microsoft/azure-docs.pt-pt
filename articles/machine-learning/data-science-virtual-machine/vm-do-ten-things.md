---
title: Explore dados e modelo sintetmente no Windows
titleSuffix: Azure Data Science Virtual Machine
description: Execute tarefas de exploração e modelação de dados na Máquina Virtual de Ciência de Dados do Windows.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6574ccd7616ac1442b1c8c69b6218431ad912ab3
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204224"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Dez coisas que pode fazer na Máquina Virtual de Ciência de Dados do Windows

A Máquina Virtual de Ciência de Dados do Windows (DSVM) é um poderoso ambiente de desenvolvimento de ciência de dados onde você pode realizar tarefas de exploração de dados e modelação. O ambiente já vem construído e agregado com várias ferramentas populares de análise de dados que facilitam o início com a sua análise para implementações no local, nuvem ou híbrida. 

O DSVM trabalha em estreita colaboração com os serviços azure. Pode ler e processar dados que já estão armazenados no Azure, no Azure SQL Data Warehouse, Azure Data Lake, Azure Storage ou Azure Cosmos DB. Também pode tirar partido de outras ferramentas de análise, como o Azure Machine Learning e a Azure Data Factory.

Neste artigo, você aprenderá a usar o seu DSVM para executar tarefas de ciência de dados e interagir com outros serviços Azure. Aqui estão algumas das coisas que pode fazer no DSVM:

- Explore dados e desenvolva modelos localmente no DSVM utilizando o Microsoft Machine Learning Server e python.
- Utilize um portátil Jupyter para experimentar os seus dados num browser utilizando Python 2, Python 3 e Microsoft R. (o Microsoft R é uma versão de R pronta para a empresa projetada para o desempenho.)
- Implemente modelos construídos através de R e Python no Azure Machine Learning para que as aplicações do cliente possam aceder aos seus modelos utilizando uma simples interface de serviço web.
- Administrar os seus recursos Azure utilizando o portal Azure ou powerShell.
- Estenda o seu espaço de armazenamento e partilhe conjuntos/códigoem em larga escala em toda a sua equipa, criando uma partilha de Ficheiros Azure como uma unidade montável no seu DSVM.
- Partilhe código com a sua equipa utilizando o GitHub. Aceda ao seu repositório utilizando os clientes Git pré-instalados: Git Bash e Git GUI.
- Aceda a dados azure e serviços de análise como o armazenamento Azure Blob, O Lago de Dados Azure, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse e Azure SQL Database.
- Construa relatórios e um dashboard utilizando a instância de ambiente de trabalho Power BI que está pré-instalada no DSVM e implante-as na nuvem.
- Escala dinamicamente o seu DSVM para atender às necessidades do seu projeto.
- Instale ferramentas adicionais na sua máquina virtual.   

> [!NOTE]
> Taxas adicionais de utilização aplicam-se a muitos dos serviços de armazenamento e análise de dados listados neste artigo. Para mais detalhes, consulte a página [de preços do Azure.](https://azure.microsoft.com/pricing/)
> 
> 

## <a name="prerequisites"></a>Pré-requisitos

* Precisa de uma assinatura Azure. Pode [inscrever-se para um julgamento gratuito.](https://azure.microsoft.com/free/)
* Instruções para o fornecimento de uma Máquina Virtual de Ciência de Dados no portal Azure estão disponíveis na [Criação](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)de uma máquina virtual .


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Explore dados e desenvolva modelos com o Microsoft Machine Learning Server
Pode utilizar idiomas como R e Python para fazer a análise de dados no DSVM.

Para R, você pode usar um IDE como RStudio que pode ser encontrado no menu inicial ou no ambiente de trabalho. Ou pode utilizar Ferramentas R para Estúdio Visual. A Microsoft forneceu bibliotecas adicionais em cima do CRAN R de código aberto para permitir análises escaláveis e a capacidade de analisar dados maiores do que o tamanho da memória permitido em análises paralelas em pedaços. 

Para python, você pode usar um IDE como Visual Studio Community Edition, que tem a extensão Python Tools for Visual Studio (PTVS) pré-instalada. Por padrão, apenas python 3.6, o ambiente raiz Conda, está configurado no PTVS. Para permitir a Anaconda Python 2.7, tome os seguintes passos:

1. Crie ambientes personalizados para cada versão, indo para **Tools** > **Python Tools** > **Python Environments,** e, em seguida, selecionando **+ Personalizado** na Edição Comunitária do Estúdio Visual.
1. Dê uma descrição e desloque o caminho de prefixo ambiental como **c:\anaconda\envs\python2** para Anaconda Python 2.7.
1. Selecione **Deteção automática** > **Aplicar** para salvar o ambiente.

Consulte a documentação do [PTVS](https://aka.ms/ptvsdocs) para obter mais detalhes sobre como criar ambientes Python.

Agora estás preparado para criar um novo projeto Python. Vá ao **Arquivo** > **Novo** > **Projeto** > **Python** e selecione o tipo de aplicação Python que está a construir. Pode definir o ambiente Python para o projeto atual para a versão desejada (Python 2.7 ou 3.6) clicando em **ambientes Python** e, em seguida, selecionando **Ambientes De Python Add/Remove**. Pode encontrar mais informações sobre o trabalho com o PTVS na documentação do [produto.](https://aka.ms/ptvsdocs)

## <a name="use-jupyter-notebooks"></a>Utilizar blocos de notas do Jupyter
O Jupyter Notebook fornece um IDE baseado no navegador para a exploração e modelação de dados. Pode utilizar Python 2, Python 3 ou R (fonte aberta e Microsoft R Server) num bloco de notas jupyter.

Para iniciar o Caderno Jupyter, selecione o ícone **do Caderno Jupyter** no menu **Iniciar** ou no ambiente de trabalho. No pedido de comando DSVM, também ```jupyter notebook``` pode executar o comando a partir do diretório onde tem cadernos existentes ou onde pretende criar novos cadernos.  

Depois de iniciar o Jupyter, deve ver um diretório que contém alguns cadernos de exemplo que são pré-embalados no DSVM. Agora pode:

* Selecione o caderno para ver o código.
* Executar cada célula selecionando Shift+Enter.
* Execute todo o caderno selecionando **Cell** > **Run**.
* Crie um novo caderno selecionando o ícone Jupyter (canto superior esquerdo), selecionando **o** novo botão à direita e, em seguida, escolhendo a linguagem do portátil (também conhecida como kernels).   

> [!NOTE]
> Atualmente, python 2.7, Python 3.6, R, Julia e pySpark kernels em Jupyter são apoiados. O núcleo R suporta a programação tanto em R de código aberto como no Microsoft R.   
> 
> 

Quando estiver no caderno, pode explorar os seus dados, construir o modelo e testar o modelo utilizando a sua escolha de bibliotecas.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Treine e implemente modelos utilizando o Azure Machine Learning
Depois de ter construído e validado o seu modelo, o próximo passo é normalmente implantá-lo em produção. Este passo permite que as aplicações do seu cliente invoquem as previsões do modelo numa base em tempo real ou numa base de modo de lote. A Azure Machine Learning fornece um mecanismo para operacionalizar um modelo construído em R ou Python.

Quando operacionaliza o seu modelo em Azure Machine Learning, um serviço web é exposto. Permite que os clientes façam chamadas REST que passem em parâmetros de entrada e recebam previsões do modelo como saídas.

### <a name="build-and-operationalize-python-models"></a>Construir e operacionalizar modelos Python
Aqui está um pedaço de código desenvolvido em um caderno Python Jupyter que constrói um modelo simples usando a biblioteca scikit-learn:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

O método utilizado para implantar os seus modelos Python para o Azure Machine Learning envolve a previsão do modelo numa função e decora-o com atributos fornecidos pela biblioteca Azure Machine Learning Python pré-instalada. Os atributos denotam o seu ID do espaço de trabalho Azure Machine Learning, a chave API e os parâmetros de entrada e devolução.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Um cliente pode agora fazer chamadas para o serviço web. Os invólucros de conveniência constroem os pedidos da API REST. Aqui está o código da amostra para consumir o serviço web:

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> Atualmente, a biblioteca Azure Machine Learning é suportada apenas em Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Construir e operacionalizar modelos R
Você pode implementar modelos R construídos na Máquina Virtual da Ciência de Dados ou em qualquer outro lugar em Azure Machine Learning de uma forma semelhante à forma como é feito para Python. Eis os passos:

1. Crie um ficheiro definições.json para fornecer o seu ID do espaço de trabalho e ficha de autenticação. 
2. Escreva um invólucro para a função de previsão do modelo.
3. Ligue ```publishWebService``` para a biblioteca azure machine learning para passar no invólucro de função.  

Utilize o seguinte procedimento e códigos para configurar, construir, publicar e consumir um modelo como serviço web em Azure Machine Learning.

#### <a name="set-up"></a>Configurar

Crie um ficheiro definições.json ```.azureml``` sob um diretório chamado sob o seu diretório. Introduza os parâmetros do seu espaço de trabalho Azure Machine Learning.

Aqui está a estrutura de ficheiros definições.json:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Construa um modelo em R e publique-o no Azure Machine Learning

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Consumir o modelo implantado na Aprendizagem automática de Azure
Para consumir o modelo a partir de uma aplicação de cliente, utilize a biblioteca Azure Machine Learning para procurar o serviço web publicado pelo nome. Utilize `services` a chamada DaPI para determinar o ponto final. Depois basta ligar `consume` para a função e passar o quadro de dados para ser previsto.

Utilize o seguinte código para consumir o modelo publicado como um serviço web Azure Machine Learning:

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Consulte mais informações sobre [pacotes R no Estúdio de Aprendizagem Automática](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning).

## <a name="manage-azure-resources"></a>Gerir os recursos do Azure
O DSVM não permite apenas construir a sua solução de análise localmente na máquina virtual. Também lhe permite aceder a serviços na plataforma azure cloud. O Azure fornece vários serviços de computação, armazenamento, análise de dados e outros serviços que pode administrar e aceder a partir do seu DSVM.

Para administrar a sua subscrição Azure e recursos na nuvem, tem duas opções:
+ Use o seu navegador e vá ao [portal Azure.](https://portal.azure.com)

+ Utilize scripts PowerShell. Executar o Azure PowerShell a partir de um atalho no ambiente de trabalho ou no menu **Iniciar.** Consulte a documentação do [Microsoft Azure PowerShell](../../powershell-azure-resource-manager.md) para obter mais detalhes. 

## <a name="extend-storage-by-using-shared-file-systems"></a>Alargar o armazenamento utilizando sistemas de ficheiros partilhados
Os cientistas de dados podem partilhar grandes conjuntos de dados, código ou outros recursos dentro da equipa. O DSVM tem cerca de 45 GB de espaço disponível. Para alargar o seu armazenamento, pode utilizar ficheiros Azure e montá-lo em uma ou mais instâncias DSVM ou acessá-lo através de uma API REST. Também pode utilizar o [portal Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) ou utilizar o [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) para adicionar discos de dados extra dedicados. 

> [!NOTE]
> O espaço máximo na partilha de Ficheiros Azure é de 5 TB. O limite de tamanho para cada ficheiro é de 1 TB. 

Pode utilizar este script no Azure PowerShell para criar uma partilha de Ficheiros Azure:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Agora que criou uma partilha de Ficheiros Azure, pode montá-la em qualquer máquina virtual em Azure. Recomendamos que coloque o VM no mesmo datacenter Azure que a conta de armazenamento, para evitar taxas de latência e transferência de dados. Aqui estão os comandos Azure PowerShell para montar a unidade no DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Agora pode aceder a esta unidade como faria com qualquer unidade normal no VM.

## <a name="share-code-in-github"></a>Código de partilha no GitHub
GitHub é um repositório de código onde você pode encontrar amostras de código e fontes para várias ferramentas usando tecnologias partilhadas pela comunidade desenvolvedora. Usa o Git como tecnologia para rastrear e armazenar versões dos ficheiros de código. O GitHub é também uma plataforma onde pode criar o seu próprio repositório para armazenar o código e documentação partilhados da sua equipa, implementar o controlo da versão e controlar quem tem acesso a visualização e contribuir com código. 

Visite as páginas de [ajuda gitHub](https://help.github.com/) para obter mais informações sobre a utilização de Git. Você pode usar o GitHub como uma das formas de colaborar com a sua equipa, usar o código desenvolvido pela comunidade e contribuir com o código de volta para a comunidade.

O DSVM vem carregado com ferramentas de cliente na linha de comando e no GUI para aceder ao repositório GitHub. A ferramenta de linha de comando que trabalha com Git e GitHub chama-se Git Bash. O Visual Studio está instalado no DSVM e tem as extensões Git. Pode encontrar ícones para estas ferramentas no menu **Iniciar** e no ambiente de trabalho.

Para descarregar código a partir de um repositório GitHub, utiliza o ```git clone``` comando. Por exemplo, para descarregar o repositório de ciência de dados publicado pela Microsoft no atual diretório, pode executar o seguinte comando em Git Bash:

    git clone https://github.com/Azure/DataScienceVM.git

No Estúdio Visual, podes fazer a mesma operação de clone. A seguinte imagem mostra como aceder às ferramentas Git e GitHub no Estúdio Visual:

![Screenshot do Estúdio Visual com a ligação GitHub exibida](./media/vm-do-ten-things/VSGit.PNG)

Pode encontrar mais informações sobre o uso do Git para trabalhar com o seu repositório GitHub a partir de recursos disponíveis na github.com. A folha de [batota](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) é uma referência útil.

## <a name="access-azure-data-and-analytics-services"></a>Aceder aos serviços de dados e análise sinuosos do Azure
### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure
O armazenamento azure Blob é um serviço de armazenamento de nuvem fiável e económico para dados grandes e pequenos. Esta secção descreve como pode mover dados para o armazenamento blob e aceder aos dados armazenados numa bolha Azure.

#### <a name="prerequisites"></a>Pré-requisitos

* Crie a sua conta de armazenamento Azure Blob a partir do [portal Azure.](https://portal.azure.com)

   ![Screenshot do processo de criação de conta de armazenamento no portal Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Confirme se a ferramenta AzCopy da linha ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```de comando está pré-instalada: . O diretório que contém azcopy.exe já está na variável ambiente PATH, para que possa evitar escrever o caminho de comando completo ao executar esta ferramenta. Para obter mais informações sobre a ferramenta AzCopy, consulte a [documentação AzCopy](../../storage/common/storage-use-azcopy.md).
* Inicie a ferramenta Azure Storage Explorer. Pode descarregá-lo a partir da página web do [Storage Explorer](https://storageexplorer.com/). 

   ![Screenshot do Azure Storage Explorer a aceder a uma conta de armazenamento](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Mover dados de um VM para uma bolha Azure: AzCopy

Para mover dados entre os seus ficheiros locais e o armazenamento blob, pode utilizar o AzCopy na linha de comando ou no PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Substitua **c:\myfolder** pelo caminho onde o seu ficheiro está armazenado, **conta mystorage** com o nome da sua conta de armazenamento Blob, **mycontainer** com o nome do recipiente e **chave de conta** de armazenamento com a sua chave de acesso ao armazenamento Blob. Pode encontrar as credenciais da sua conta de armazenamento no [portal Azure.](https://portal.azure.com)

Executar o comando AzCopy no PowerShell ou a partir de um pedido de comando. Aqui está um exemplo de utilização do comando AzCopy:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Depois de executar o comando AzCopy para copiar para uma bolha Azure, o seu ficheiro aparecerá no Azure Storage Explorer.

![Screenshot da conta de armazenamento, exibindo o ficheiro CSV carregado](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Mova dados de um VM para um blob Azure: Azure Storage Explorer

Também pode fazer o upload de dados do ficheiro local no seu VM utilizando o Azure Storage Explorer:

* Para fazer o upload dos dados para um recipiente, selecione o recipiente-alvo e selecione o botão **Upload.** ![Screenshot do botão de upload no Azure Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* Selecione a elipsis **(...)** à direita da caixa **Ficheiros,** selecione um ou vários ficheiros para fazer o upload do sistema de ficheiros e selecione **Enviar para** começar a carregar os ficheiros. ![Screenshot da caixa de diálogo de ficheiros upload](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Leia os dados de um blob Azure: Módulo de leitor de Machine Learning

No Azure Machine Learning Studio, pode utilizar o módulo De dados de importação para ler dados da sua bolha.

![Screenshot do módulo de dados de importação no Estúdio de Aprendizagem automática](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Leia os dados de uma bolha Azure: Python ODBC

Você pode usar a biblioteca BlobService para ler dados diretamente de uma bolha em um caderno Jupyter ou em um programa Python.

Em primeiro lugar, importar os pacotes necessários:

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

Em seguida, ligue as credenciais da sua conta de armazenamento Blob e leia os dados da bolha:

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

Os dados são lidos como um quadro de dados:

![Screenshot das primeiras 10 linhas de dados](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
O Armazenamento do Lago Azure Data é um repositório de hiperescala para grandes cargas de trabalho de análise de dados e é compatível com o Sistema de Ficheiros Distribuídos hadoop (HDFS). Funciona com Hadoop, Spark e Azure Data Lake Analytics. Nesta secção, você vai aprender como você pode mover dados para O Armazenamento de Lagos De Dados Azure e executar análises usando Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Pré-requisitos

* Crie a sua instância Azure Data Lake Analytics no [portal Azure.](https://portal.azure.com)

   ![Screenshot de criar uma instância de Data Lake Analytics a partir do portal Azure](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* O [plug-in Azure Data Lake e Stream Analytics para o Visual Studio plug-in](https://www.microsoft.com/download/details.aspx?id=49504) já está instalado na Visual Studio Community Edition na máquina virtual. Depois de iniciar o Visual Studio e iniciar a sua subscrição azure, deverá ver a sua conta Azure Data Analytics e armazenamento no painel esquerdo do Visual Studio.

   ![Screenshot do plug-in para ferramentas data lake no Estúdio Visual](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Mova dados de um VM para Data Lake: Azure Data Lake Explorer

Pode utilizar o Azure Data Lake Explorer para [fazer o upload de dados dos ficheiros locais da sua máquina virtual para](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)data lake storage .

Também pode construir um pipeline de dados para operacionalizar o seu movimento de dados de ou para o Lago de Dados Azure utilizando a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). [Este artigo](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) guia-o através dos passos para construir os gasodutos de dados.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Leia os dados de uma bolha Azure para Data Lake: U-SQL

Se os seus dados residirem no armazenamento do Azure Blob, pode ler diretamente os dados de uma bolha Azure numa consulta U-SQL. Antes de compor a sua consulta U-SQL, certifique-se de que a sua conta de armazenamento Blob está ligada à sua instância do Lago de Dados Azure. Vá ao portal Azure, encontre o seu painel De dados Do lago Azur, selecione Adicionar Fonte de **Dados,** selecione um tipo de armazenamento de **Armazenamento Azure,** e ligue o nome e a chave da sua conta de armazenamento Azure. Em seguida, pode fazer referência aos dados armazenados na conta de armazenamento.

![Screenshot da caixa de diálogo Adicionar Fonte de Dados](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

No Estúdio Visual, pode ler dados do armazenamento blob, manipular dados, características de engenharia e enviar os dados resultantes para o armazenamento azure Data Lake ou Azure Blob. Quando fizer referência aos dados no armazenamento blob, utilize **wasb://**. Quando se refere aos dados no Lago de Dados Azure, utilize **swbhdfs://**.

Pode utilizar as seguintes consultas U-SQL no Estúdio Visual:

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

Depois de a sua consulta ser submetida ao servidor, um diagrama mostra o estado do seu trabalho.

![Screenshot do diagrama do estado do trabalho](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Dados de consulta em Data Lake: U-SQL

Depois do conjunto de dados ser ingerido no Lago de Dados Azure, pode usar [a linguagem U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) para consultar e explorar os dados. O idioma U-SQL é semelhante ao T-SQL, mas combina algumas funcionalidades de C# para que os utilizadores possam escrever módulos personalizados e funções definidas pelo utilizador. Pode utilizar os scripts no passo anterior.

Depois de a consulta ser submetida ao servidor, tripdata_summary. CSV aparece no Azure Data Lake Explorer. Pode pré-visualizar os dados clicando no ficheiro.

![Screenshot do ficheiro CSV no Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

As informações do ficheiro aparecem:

![Screenshot da informação sumária do ficheiro](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Aglomerados hadoop HDInsight
Azure HDInsight é um serviço de Apache Hadoop, Spark, HBase e Storm gerido na nuvem. Pode trabalhar facilmente com clusters Azure HDInsight da Máquina Virtual da Ciência dos Dados.

#### <a name="prerequisites"></a>Pré-requisitos

* Crie a sua conta de armazenamento Azure Blob a partir do [portal Azure.](https://portal.azure.com) Esta conta de armazenamento é utilizada para armazenar dados para clusters HDInsight.

   ![Screenshot de criar uma conta de armazenamento a partir do portal Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Personalize os clusters Hadoop Azure HDInsight do [portal Azure](../team-data-science-process/customize-hadoop-cluster.md).
  
   Ligue a conta de armazenamento criada com o seu cluster HDInsight quando for criada. Esta conta de armazenamento é utilizada para aceder a dados que podem ser processados dentro do cluster.

   ![Seleções para ligar a conta de armazenamento criada com um cluster HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Ative o acesso do Remote Desktop ao nó da cabeça do cluster depois de criado. Lembre-se das credenciais de acesso remoto que especifica aqui, porque vai precisar delas no procedimento subsequente.

   ![Botão de ambiente de trabalho remoto para permitir o acesso remoto ao cluster HDInsight](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Criar um espaço de trabalho azure machine learning. As suas experiências de Machine Learning estão armazenadas neste espaço de trabalho de Machine Learning. Selecione as opções destacadas no portal, como mostra a seguinte imagem:

   ![Criar um espaço de trabalho azure machine learning](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Introduza os parâmetros para o seu espaço de trabalho.

   ![Introduza parâmetros do espaço de trabalho machine learning](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Faça upload de dados utilizando o IPython Notebook. Importar pacotes necessários, ligar credenciais, criar uma base de dados na sua conta de armazenamento e, em seguida, carregar dados em clusters DeHDi.

```python
# Import required packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

Em alternativa, pode seguir [este walkthrough](../team-data-science-process/hive-walkthrough.md) para fazer o upload dos dados do TÁXI de NYC para o cluster HDI. Os principais passos incluem:
  
* Utilize o AzCopy para descarregar CSVs zipped da bolha pública para a sua pasta local.
* Utilize o AzCopy para fazer o upload de CSVs sem fecho da pasta local para um cluster HDI.
* Inicie sessão no nó da cabeça do cluster Hadoop e prepare-se para análise de dados exploratórios.

Depois de os dados terem sido carregados no cluster HDI, pode verificar os seus dados no Azure Storage Explorer. E a base de dados de Nyctaxidb foi criada no cluster HDI.

#### <a name="data-exploration-hive-queries-in-python"></a>Exploração de dados: Consultas de Colmeia em Python

Como os dados estão num cluster Hadoop, você pode usar o pacote pyodbc para ligar a clusters Hadoop e bases de dados de consulta usando hive para fazer exploração e engenharia de recursos. Pode ver as tabelas existentes que criou no passo pré-requisito.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Ver tabelas existentes](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Vejamos o número de registos em cada mês e as frequências de gorjetaou ou não na mesa de viagem:

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![Lote de número de registos em cada mês](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![Lote de frequências de gorjeta](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Também pode calcular a distância entre a localização da recolha e o local de entrega e, em seguida, compará-la com a distância de viagem.

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString, connection)
results.head(5)
```

![Filas superiores da mesa de recolha e entrega](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Lote de distância de recolha/descolagem para distância de viagem](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Agora vamos preparar um conjunto de dados (1 por cento) para modelação. Pode utilizar estes dados no módulo de leitor de Machine Learning.

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

Agora insira o conteúdo da junta na tabela interna anterior.

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

Passado algum tempo, pode ver que os dados foram carregados em aglomerados hadoop:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Primeiras filas de dados da tabela](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Armazém de dados Azure SQL e bases de dados
O Azure SQL Data Warehouse é um armazém de dados elástico como um serviço com uma experiência de Servidor SQL de classe empresarial.

Pode fornecer o seu armazém de dados Azure SQL seguindo as instruções [deste artigo](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Depois de fornecer o seu armazém de dados SQL, pode utilizar [este walkthrough](../team-data-science-process/sqldw-walkthrough.md) para fazer upload de dados, exploração e modelação utilizando dados dentro do armazém de dados SQL.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB é uma base de dados noSQL na nuvem. Pode usá-lo para trabalhar com documentos como jSON, e para armazenar e consultar os documentos.

Utilize as seguintes medidas pré-requisitos para aceder ao Azure Cosmos DB a partir do DSVM:

1. O Azure Cosmos DB Python SDK já está instalado no DSVM. Para atualizá-lo, corra ```pip install pydocumentdb --upgrade``` a partir de um pedido de comando.
2. Crie uma conta Azure Cosmos DB e base de dados do [portal Azure.](https://portal.azure.com)
3. Descarregue a Ferramenta de Migração de Dados Do Azure Cosmos DB do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) e extraa para um diretório à sua escolha.
4. Importar dados jSON (dados de vulcão) armazenados numa [bolha pública](https://https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) em Azure Cosmos DB com os seguintes parâmetros de comando para a ferramenta de migração. (Use dtui.exe do diretório onde instalou a Ferramenta de Migração de Dados Azure Cosmos DB.) Insira a origem e a localização do alvo com estes parâmetros:
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Depois de importar os dados, pode ir ao Jupyter e abrir o caderno intitulado *DocumentDBSample*. Contém código Python para aceder ao Azure Cosmos DB e fazer algumas consultas básicas. Pode saber mais sobre o Azure Cosmos DB visitando a página de [documentação](https://docs.microsoft.com/azure/cosmos-db/)do serviço.

## <a name="use-power-bi-reports-and-dashboards"></a>Use relatórios e dashboards Power BI 
Pode visualizar o ficheiro Volcano JSON do exemplo anterior do Azure Cosmos DB no Power BI Desktop para obter informações visuais sobre os dados. Estão disponíveis passos detalhados no [artigo Power BI](../../cosmos-db/powerbi-visualize.md). Aqui estão os passos de alto nível:

1. Abra o Power BI Desktop e selecione **Obter Dados**. Especifique `https://cahandson.blob.core.windows.net/samples/volcano.json`o URL como: .
2. Devia ver os registos da JSON importados como uma lista. Converta a lista numa tabela para que o Power BI possa trabalhar com ela.
4. Expanda as colunas selecionando o ícone de expansão (seta).
5. Reparem que a localização é um campo **de gravação.** Expanda o disco e selecione apenas as coordenadas. **A coordenada** é uma coluna de listas.
6. Adicione uma nova coluna para converter a coluna de coordenadas da lista numa coluna **LatLong** separada pela vírposta. Concatena os dois elementos no campo da ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```lista de coordenadas utilizando a fórmula .
7. Converta a coluna **Elevação** para decimal e selecione os botões **Close** and **Apply.**

Em vez de passos anteriores, pode colar o seguinte código. Ele scripts fora os passos usados no Editor Avançado em Power BI para escrever as transformações de dados em uma linguagem de consulta.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

Tem agora os dados no seu modelo de dados Power BI. A sua instância de Ambiente de Trabalho Power BI deve aparecer da seguinte forma:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Pode começar a construir relatórios e visualizações utilizando o modelo de dados. Pode seguir os passos [deste artigo do Power BI](../../cosmos-db/powerbi-visualize.md#build-the-reports) para construir um relatório.

## <a name="scale-the-dsvm-dynamically"></a>Escala dSVM dinamicamente 
Pode escalar para cima e para baixo o DSVM para atender às necessidades do seu projeto. Se não precisar de usar o VM à noite ou aos fins de semana, pode desligar o VM do [portal Azure.](https://portal.azure.com)

> [!NOTE]
> Incorre em cargas de cálculo se utilizar apenas o botão de paragem do sistema operativo no VM.  
> 
> 

Pode ser necessário lidar com alguma análise em larga escala e precisar de mais CPU, memória ou capacidade de disco. Em caso afirmativo, pode encontrar uma escolha de tamanhos VM em termos de núcleos de CPU, instâncias baseadas em GPU para aprendizagem profunda, capacidade de memória e tipos de discos (incluindo unidades de estado sólido) que vão ao encontro das suas necessidades computacionais e orçamentais. A lista completa de VMs, juntamente com os seus preços de cálculo de hora a hora, está disponível na página de preços das [Máquinas Virtuais Azure.](https://azure.microsoft.com/pricing/details/virtual-machines/)

Da mesma forma, a sua necessidade de capacidade de processamento de VM pode diminuir. (Por exemplo: mudou uma grande carga de trabalho para um aglomerado hadoop ou spark.) Em seguida, pode reduzir o cluster a partir do [portal Azure](https://portal.azure.com) e ir para as definições da sua instância VM. 

## <a name="add-more-tools"></a>Adicione mais ferramentas
As ferramentas pré-incorporadas no DSVM podem responder a muitas necessidades comuns de análise de dados. Isto poupa-lhe tempo porque não tem de instalar e configurar os seus ambientes um a um. Também poupa dinheiro, porque paga apenas pelos recursos que utiliza.

Pode utilizar outros dados e serviços de análise azure perfilados neste artigo para melhorar o seu ambiente de análise. Em alguns casos, você pode precisar de ferramentas adicionais, incluindo algumas ferramentas de parceiro proprietário. Tem acesso administrativo total à máquina virtual para instalar novas ferramentas de que necessita. Também pode instalar pacotes adicionais em Python e R que não estejam pré-instalados. Para python, você ```conda``` pode ```pip```usar ou . Para R, pode ```install.packages()``` utilizar na consola R, ou utilizar o IDE e selecionar **pacotes** > de instalação de**pacotes**.

## <a name="deep-learning"></a>Aprendizagem aprofundada

Além das amostras baseadas em quadros, você pode obter um conjunto de walkthroughs abrangentes que foram validados no DSVM. Estes passadiços ajudam-no a iniciar o seu desenvolvimento de aplicações de aprendizagem profunda em domínios como imagem e compreensão de texto/linguagem.   


- [Funcionamento de redes neurais em diferentes estruturas](https://github.com/ilkarman/DeepLearningFrameworks): Este walkthrough mostra como migrar código de uma estrutura para outra. Também demonstra como comparar modelos e desempenho em tempo de execução em todos os quadros. 

- [Um guia de como construir uma solução de ponta a ponta para detetar produtos dentro](https://github.com/Azure/cortana-intelligence-product-detection-from-images)de imagens : A deteção de imagem é uma técnica que pode localizar e classificar objetos dentro das imagens. Esta tecnologia tem o potencial de trazer enormes recompensas em muitos domínios empresariais da vida real. Por exemplo, os retalhistas podem usar esta técnica para determinar que produto um cliente pegou na prateleira. Esta informação, por sua vez, ajuda as lojas a gerir o inventário do produto. 

- [Aprendizagem profunda para áudio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Este tutorial mostra como treinar um modelo de aprendizagem profunda para deteção de eventos áudio no conjunto de dados de [sons urbanos](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Também fornece uma visão geral de como trabalhar com dados áudio.

- [Classificação de documentos de texto](https://github.com/anargyri/lstm_han): Este walkthrough demonstra como construir e treinar duas arquiteturas de rede neural: Rede de Atenção Hierárquica e Rede de Memória de Longo Prazo (LSTM). Estas redes neurais utilizam a API Keras para uma aprendizagem profunda para classificar documentos de texto. Keras é uma frente para três dos mais populares quadros de deep learning: Microsoft Cognitive Toolkit, TensorFlow e Theano.

## <a name="summary"></a>Resumo
Este artigo descreveu algumas das coisas que pode fazer na Máquina Virtual da Ciência dos Dados da Microsoft. Há muitas mais coisas que pode fazer para tornar o DSVM um ambiente de análise eficaz.

