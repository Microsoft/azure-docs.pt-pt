---
title: Exploração de dados e modelação com máquina de Virtual de ciência de dados
titleSuffix: Azure
description: Execute várias exploração de dados e a Modelagem de tarefas na máquina Virtual de ciência de dados.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.custom: seodec18
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: 725580a01c8dd60003cfc11910a3e5183624b1b1
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437548"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Dez coisas que pode fazer na máquina Virtual Windows dados ciência

Máquina Virtual de ciência de dados (DSVM) do Windows é um ambiente de desenvolvimento de ciência de dados poderoso que permite-lhe executar várias tarefas de exploração e modelação de dados. O ambiente já vem incorporado e agrupado com várias ferramentas de análise de dados populares que facilitam a começar rapidamente a análise no local, implementações na Cloud ou híbrida. A DSVM trabalha junto com vários serviços do Azure e é capaz de ler e processar dados que já são armazenados no Azure, no Azure SQL Data Warehouse, o Azure Data Lake, o armazenamento do Azure ou no Azure Cosmos DB. Também pode tirar partido outras ferramentas de análise, como o Azure Machine Learning e Azure Data Factory.

Neste artigo, aprenderá a usar sua DSVM para efetuar várias tarefas de ciência de dados e interagir com outros serviços do Azure. Aqui estão algumas das coisas que pode fazer na DSVM:

1. Explorar dados e desenvolver modelos localmente na DSVM usando o Microsoft ML Server, o Python
2. Utilizar um bloco de notas do Jupyter para fazer experiências com os seus dados num navegador com o Python 2, 3 do Python, R Microsoft uma versão de pronto empresarial do R concebida para desempenho
3. Implementar modelos criados com R e Python no Azure Machine Learning para aplicativos cliente possam aceder aos seus modelos usando uma interface de serviço da web simples
4. Administrar recursos do Azure através do portal do Azure ou o Powershell
5. Expandir o seu espaço de armazenamento e partilhar grandes conjuntos de dados / de código em toda a sua equipa através da criação de um armazenamento de ficheiros do Azure como uma unidade montável em sua DSVM
6. Compartilhar código com sua equipe com o GitHub e o repositório utilizando os pré-instaladas Git clientes - Git Bash, Git GUI de acesso.
7. Aceder a vários serviços do Azure e análise de dados, como o armazenamento de Blobs do Azure, Azure Data Lake, o Azure HDInsight (Hadoop), o Azure Cosmos DB, Azure SQL Data Warehouse e as bases de dados
8. Criar relatórios e dashboard através do Power BI Desktop pré-instalado em DSVM e implementá-las na cloud
9. Dimensionar dinamicamente o sua DSVM para satisfazer as necessidades do projeto
10. Instale as ferramentas adicionais na sua máquina virtual   

> [!NOTE]
> Os custos de utilização adicionais aplicam-se para muitos dos serviços de armazenamento e análise de dados adicionais apresentados neste artigo. Consulte a [preços do Azure](https://azure.microsoft.com/pricing/) página para obter detalhes.
> 
> 

**Pré-requisitos**

* Precisa de uma subscrição do Azure. Pode inscrever-se numa avaliação gratuita [aqui](https://azure.microsoft.com/free/).
* Estão disponíveis instruções para o aprovisionamento de uma Máquina Virtual de ciência de dados no portal do Azure em [criar uma máquina virtual](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).

## <a name="1-explore-data-and-develop-models-using-microsoft-ml-server-or-python"></a>1. Explorar dados e desenvolver modelos com o Microsoft ML Server ou Python
Pode usar as linguagens como R e Python para fazer sua análise de dados na DSVM.

Para R, pode utilizar um IDE, como o r Studio que pode ser encontrado no menu Iniciar ou a área de trabalho ou R Tools para Visual Studio. A Microsoft forneceu bibliotecas adicionais sobre o Open-origem/CRAN-R para permitir análises dimensionáveis e a capacidade de analisar dados maiores do que o tamanho de memória permitido ao fazer a análise em segmentos paralelo. 

Para o Python, pode utilizar um IDE, como o Visual Studio Community Edition, que tem as ferramentas Python para a extensão do Visual Studio (PTVS) pré-instalado. Por predefinição, apenas o Python 3.6, o ambiente de conda raiz está configurado no PTVS. Para habilitar Anaconda Python 2.7, terá de efetuar os passos seguintes:

* Criar ambientes personalizados para cada versão ao navegar até **ferramentas** -> **ferramentas do Python** -> **ambientes do Python** e, em seguida, clicando em " **+ Personalizado**"na edição de Comunidade do Visual Studio
* Forneça uma descrição e defina o caminho de prefixo de ambiente porque *c:\anaconda\envs\python2* para Anaconda Python 2.7
* Clique em **deteção automática** e, em seguida **aplicar** para guardar o ambiente.

Eis o aspeto de configuração do ambiente personalizado no Visual Studio.

![Captura de ecrã do Visual Studio com ferramentas Python para Visual Studio selecionado](./media/vm-do-ten-things/PTVSSetup.png)

Consulte a [documentação das PTVS](https://aka.ms/ptvsdocs) para obter mais detalhes sobre como criar ambientes do Python.

Agora são configuradas para criar um novo projeto de Python. Navegue para **arquivo** -> **New** -> **projeto** -> **Python** e selecione o tipo de Aplicação de Python que está a criar. Pode configurar o ambiente do Python para o projeto atual para a versão pretendida (Python 2.7 ou 3.6) ao clicar com o botão direito a **ambientes do Python**, ao selecionar **ambientes do Python Adicionar/remover**e, em seguida, Escolher o ambiente pretendido. Pode encontrar mais informações sobre como trabalhar com a PTVS no produto [documentação](https://aka.ms/ptvsdocs).

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Utilizar um bloco de notas do Jupyter para explorar e modelar os seus dados com o Python ou R
O bloco de notas do Jupyter é um ambiente poderoso que fornece um baseada no browser "IDE" para modelação e exploração de dados. Pode utilizar o Python 2, 3 de Python ou R (código-fonte aberto e o Microsoft R Server) num bloco de notas do Jupyter.

Para iniciar o bloco de notas do Jupyter, clique no ícone de menu de início / ícone de área de trabalho intitulada **bloco de notas do Jupyter**. No prompt de comando DSVM, também pode executar o comando ```jupyter notebook``` do diretório em que tiver blocos de notas existentes ou criar novos blocos de notas.  

Assim que tiver iniciado o Jupyter, deverá ver um diretório que contém alguns blocos de notas de exemplo que são pré-embalado em DSVM. Agora, pode:

* Clique para ver o código do bloco de notas.
* Executar cada célula, premindo **SHIFT-ENTER**.
* Executar o bloco de notas completo clicando em **célula** -> **executar**
* Criar um novo bloco de notas ao clicar no ícone de Jupyter (canto superior esquerdo) e, em seguida, em **New** botão à direita e, em seguida, escolha o idioma de bloco de notas (também conhecido como kernels).   

> [!NOTE]
> Atualmente o Python 2.7, 3.6 do Python, R, Julia e PySpark kernels no Jupyter são suportados. O kernel de R oferece suporte a programação em tanto código-fonte aberto R, bem como o Microsoft R. de elevado desempenho   
> 
> 

Assim que estiver no bloco de notas, pode explorar os seus dados, criar o modelo, testar o modelo ao utilizar bibliotecas.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. Criar modelos com o R ou Python e Operacionalize-os com o Azure Machine Learning
Depois de criado e validado o seu modelo a próxima etapa é normalmente para implantá-lo em produção. Isso permite que seus aplicativos cliente invocar as previsões do modelo num em tempo real ou numa base de modo de lote. O Azure Machine Learning fornece um mecanismo para operacionalizar um modelo criado em R ou Python.

Quando operacionalizar o seu modelo no Azure Machine Learning, um serviço web exposto que permite aos clientes fazer chamadas REST que passam parâmetros de entrada e recebem as previsões do modelo como saídas.   

> [!NOTE]
> Se não ainda inscreveram no Azure Machine Learning, pode obter uma área de trabalho gratuita ou uma área de trabalho padrão, visitando a [Azure Machine Learning Studio](https://studio.azureml.net/) home page e clicar em "Iniciar."   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Modelos de compilação e de Operacionalizar o Python
Aqui está um trecho de código desenvolvido num bloco de notas do Jupyter Python que cria um modelo simple usando a biblioteca SciKit-saiba.

```python
#IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

O método utilizado para implementar os seus modelos de python para o Azure Machine Learning inclui a previsão do modelo para uma função e faz a decoração com atributos fornecidos pela biblioteca de python do Azure Machine Learning pré-instaladas que denotam o Azure Machine Learning ID da área de trabalho, chave de API e os parâmetros de entrada e de retornados.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(int) #0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]
return clf.predict(inputArray)
```

Um cliente pode agora fazer chamadas para o serviço web. Há invólucros de conveniência que construir os pedidos de REST API. Aqui está um código de exemplo para consumir o serviço web.

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
> A biblioteca do Azure Machine Learning só é suportada atualmente no Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Modelos de compilação e de Operacionalizar R
Pode implementar modelos de R com base na máquina de Virtual de ciência de dados ou noutro local no Azure Machine Learning de forma semelhante a como isso é feito para Python. Eis os passos:

* criar um ficheiro Settings JSON para fornecer o seu ID de área de trabalho e autenticação do token 
* criar um invólucro para a função de prever o modelo.
* chamar ```publishWebService``` na biblioteca do Azure Machine Learning para passar o wrapper de função.  

Eis os fragmentos do procedimento e o código que podem ser utilizados para configurar, criar, publicar e consumir um modelo como um serviço web no Azure Machine Learning.

#### <a name="setup"></a>Configurar

* Criar um ficheiro Settings JSON num diretório chamado ```.azureml``` sob o diretório raiz e introduza os parâmetros da sua área de trabalho do Azure Machine Learning:

Settings estrutura de ficheiros:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Criar um modelo em R e publicá-la no Azure Machine Learning

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

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Consumir o modelo implementado no Azure Machine Learning
Para consumir o modelo de um aplicativo cliente, utilizamos a biblioteca do Azure Machine Learning para procurar o serviço web publicadas através do nome do `services` chamada à API para determinar o ponto final. Em seguida, basta chamar o `consume` de função e passar o quadro de dados a ser prevista.
O código a seguir é utilizado para consumir o modelo publicado como um serviço web Azure Machine Learning.

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# OK, try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Podem encontrar mais informações sobre a biblioteca do Azure Machine Learning R [aqui](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Administrar recursos do Azure através do portal do Azure ou o Powershell
A DSVM não só permite que crie a sua solução de análise localmente na máquina virtual, mas também permite-lhe aceder a recursos na cloud do Microsoft Azure. O Azure fornece vários computação, armazenamento, serviços de análise de dados e outros serviços que podem administrar e aceder a partir de sua DSVM.

Para administrar os recursos subscrição e na cloud do Azure pode utilizar o browser e apontar para o [portal do Azure](https://portal.azure.com). Também pode utilizar o Azure Powershell para administrar a sua subscrição do Azure e os recursos por meio de um script.
Pode executar o Azure Powershell a partir de um atalho na área de trabalho ou a partir do menu Iniciar intitulada "Microsoft Azure Powershell." Consulte a [documentação do Microsoft Azure Powershell](../../powershell-azure-resource-manager.md) para obter mais informações sobre como pode administrar a sua subscrição do Azure e os recursos através de scripts do Windows Powershell.

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. Expandir o seu espaço de armazenamento com um sistema de ficheiros partilhados
Os cientistas de dados podem partilhar grandes conjuntos de dados, código ou outros recursos dentro da equipe. A DSVM em si tem cerca de 45GB de espaço disponível. Para expandir o seu armazenamento, pode utilizar o serviço de ficheiros do Azure e a montá-la num ou mais instâncias DSVM ou aceder aos mesmos através de uma API REST.  Também pode utilizar [Portal do Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) ou utilize [Azure Powershell](../../virtual-machines/windows/attach-disk-ps.md) para adicionar discos de dados extra dedicado. 

> [!NOTE]
> O espaço máximo da partilha do serviço de ficheiros do Azure é de 5 TB e limite de tamanho de ficheiro individual é 1 TB. 
> 
> 

Pode utilizar o Azure Powershell para criar uma partilha de serviço de ficheiros do Azure. Aqui está o script a executar no Azure PowerShell para criar uma partilha de serviço de ficheiros do Azure.

```powershell
# Authenticate to Azure.
Connect-AzureRmAccount
# Select your subscription
Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
# Create a new resource group.
New-AzureRmResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you wish.
New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create a Azure File Service Share
$s = New-AzureStorageShare <<teamsharename>>
# Create a directory under the FIle share. You can give it any name
New-AzureStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzureStorageFile -Share $s
```

Agora que já criou uma partilha de ficheiros do Azure, pode montá-la em qualquer máquina virtual no Azure. É altamente recomendável que a VM está no mesmo centro de dados do Azure como a conta de armazenamento para evitar custos de transferência de dados e de latência. Aqui estão os comandos para montar a unidade no DSVM que pode executar no Azure Powershell.

```powershell
# Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Agora pode aceder a esta unidade como faria com qualquer unidade normal na VM.

## <a name="6-share-code-with-your-team-using-github"></a>6. Compartilhar código com sua equipe com o GitHub
O GitHub é um repositório de código onde pode encontrar muitos códigos de exemplo e origens de diversas ferramentas utilizando várias tecnologias partilhadas pela Comunidade do desenvolvedor. Ele usa o Git como a tecnologia para controlar e armazenar versões dos arquivos de código. O GitHub é também uma plataforma onde pode criar seu próprio repositório para armazenar o código compartilhado e a documentação da sua equipa, implementar o controle de versão e também controlar quem tem acesso para ver e contribuir com código. Visite o [páginas de ajuda do GitHub](https://help.github.com/) para obter mais informações sobre como utilizar o Git. Pode utilizar o GitHub como uma das formas de colaborar com sua equipe, utilize o código desenvolvido pela Comunidade e contribuir com código de volta para a Comunidade.

A DSVM já vem carregada com ferramentas de cliente na linha de comando como bem GUI para acessar o repositório do GitHub. A ferramenta da linha de comandos para trabalhar com o Git e GitHub denomina-se do Git Bash. Visual Studio instalado no DSVM tem as extensões de Git. Pode encontrar os ícones de arranque para essas ferramentas no menu Iniciar e a área de trabalho.

Para transferir o código a partir de um repositório do GitHub, utilize o ```git clone``` comando. Por exemplo, para transferir o repositório de ciência de dados publicado pela Microsoft para o diretório atual pode executar o seguinte comando assim que estiver no ```git-bash```.

    git clone https://github.com/Azure/DataScienceVM.git

No Visual Studio, pode fazer a mesma operação de clonagem. Captura de ecrã seguinte mostra como acessar o Git e GitHub ferramentas do Visual Studio.

![Captura de ecrã do Visual Studio com a ligação de GitHub apresentada](./media/vm-do-ten-things/VSGit.PNG)

Pode encontrar mais informações sobre como utilizar Git para trabalhar com o seu repositório do GitHub de vários recursos disponíveis no github.com. O [cábula](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) é uma referência útil.

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. Aceder a diversos serviços de dados e análise do Azure
### <a name="azure-blob"></a>Blob do Azure
BLOBs do Azure é um armazenamento de cloud fiável e económico para dados de grandes ou pequenos. Esta secção descreve como pode mover dados de Blobs do Azure e acessar dados armazenados num Blob do Azure.

**Pré-requisito**

* **Criar a sua conta de armazenamento de Blobs do Azure partir [portal do Azure](https://portal.azure.com).**

![Captura de ecrã do processo de criação de conta de armazenamento no portal do Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Confirme que a ferramenta de linha de comandos AzCopy pré-instaladas é encontrada em ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. O diretório que contém azcopy.exe já está na variável de ambiente PATH para evitar escrever o caminho completo do comando ao executar esta ferramenta. Para obter mais informações sobre a ferramenta de AzCopy, consulte [documentação do AzCopy](../../storage/common/storage-use-azcopy.md)
* Inicie a ferramenta do Explorador de armazenamento do Azure. Pode ser transferido a partir [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/). 

![Captura de ecrã do Explorador de armazenamento do Azure aceder a uma conta de armazenamento](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**Mova dados de VM para BLOBs do Azure: AzCopy**

Para mover dados entre os ficheiros locais e o armazenamento de BLOBs, pode utilizar o AzCopy na linha de comandos ou o PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Substitua **C:\myfolder** para o caminho onde está armazenado o ficheiro, **mystorageaccount** para o seu nome de conta de armazenamento de BLOBs, **mycontainer** para o nome do contentor, **chave de conta de armazenamento** à sua chave de acesso de armazenamento de Blobs. Pode encontrar as credenciais da conta de armazenamento [portal do Azure](https://portal.azure.com).

![Captura de ecrã das chaves de conta de armazenamento e informações de contentor no portal do Azure](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

Execute o comando do AzCopy no PowerShell ou a partir de um prompt de comando. Eis um exemplo de utilização do comando do AzCopy:

```powershell
# Copy *.sql from local machine to a Azure Blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from Azure Blob container to Local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Depois de executar o comando do AzCopy para copiar para um blob do Azure, verá que o ficheiro aparece no Explorador de armazenamento do Azure em breve.

![Captura de ecrã da conta de armazenamento, exibindo o ficheiro CSV carregado](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Mova dados de VM para BLOBs do Azure: Explorador de armazenamento do Azure**

Também pode carregar dados do ficheiro local na VM com o Explorador de armazenamento do Azure:

* Para carregar dados para um contentor, selecione o contentor de destino e clique nas **carregue** botão.![ Captura de ecrã do botão de carregamento no Explorador de armazenamento do Azure](./media/vm-do-ten-things/storage-accounts.png)
* Clique no **...**  à direita do **arquivos** caixa, selecione um ou vários ficheiros para carregar a partir do sistema de ficheiros e clique em **carregar** para começar a carregar os ficheiros.![ Captura de ecrã da caixa de diálogo de carregamento de ficheiros](./media/vm-do-ten-things/upload-files-to-blob.png)

**Ler dados de Blobs do Azure: Módulo de leitor do Machine Learning**

No Azure Machine Learning Studio, pode utilizar um **módulo importar dados** para ler dados a partir do blob.

![Captura de ecrã do módulo importar dados no Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Ler dados de Blobs do Azure: Python ODBC**

Pode usar **BlobService** biblioteca para ler os dados diretamente a partir do blob num programa de bloco de notas do Jupyter ou Python.

Primeiramente, importe os pacotes necessários:

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

Em seguida, plug-in as credenciais da sua conta do Blob do Azure e ler dados do Blob:

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

#unzipping downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

Os dados são lidos na como um quadro de dados:

![Captura de ecrã das primeiras 10 linhas de dados](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Armazenamento do Azure Data Lake é um repositório de hiperescala para cargas de trabalho de análise de macrodados e compatível com o Hadoop Distributed File System (HDFS). Ele funciona com o Hadoop, Spark e do Azure Data Lake Analytics. Nesta secção, irá aprender como pode mover dados para o Store do Azure Data Lake e executar a análise com o Azure Data Lake Analytics.

**Pré-requisito**

* Criar o Azure Data Lake Analytics na [portal do Azure](https://portal.azure.com).

![Captura de ecrã da criação do Data Lake Analytics a partir do portal do Azure](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* O **ferramentas do Azure Data Lake** na **Visual Studio** encontrada neste [ligação](https://www.microsoft.com/download/details.aspx?id=49504) já está instalado o Visual Studio Community Edition que está na máquina virtual. Depois de iniciar o Visual Studio e iniciar sessão na sua subscrição do Azure, deverá ver a sua conta de análise de dados do Azure e armazenamento no painel esquerdo do Visual Studio.

![Captura de ecrã de ferramentas do Data Lake no Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Mova dados de VM para o Data Lake: Azure Data Lake Explorer**

Pode usar **do Azure Data Lake Explorer** para carregar dados a partir de ficheiros locais na sua máquina Virtual para o armazenamento do Data Lake.

![Captura de ecrã de utilizar o Data Lake Explorer para carregar ficheiros](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Também pode criar um pipeline de dados para operacionalizar o movimento de dados de ou para o Azure Data Lake utilizando o [Factory(ADF) de dados do Azure](https://azure.microsoft.com/services/data-factory/). Consulte este [artigo](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) para orientá-lo pelos passos para criar os dados de pipelines.

**Ler dados de Blobs do Azure para o Data Lake: U-SQL**

Se os dados residem no armazenamento de Blobs do Azure, podem ler diretamente dados do blob de armazenamento do Azure na consulta de U-SQL. Antes de compor a consulta de U-SQL, certifique-se de que a conta de armazenamento de Blobs está ligada ao seu Azure Data Lake. Aceda a **portal do Azure**, localize o seu dashboard do Azure Data Lake Analytics, clique em **Adicionar origem de dados**, selecione o tipo de armazenamento **armazenamento do Azure** e conectar a sua conta de armazenamento do Azure Nome e a chave. Em seguida, é possível referenciar os dados armazenados na conta de armazenamento.

![Captura de ecrã da caixa de diálogo Adicionar origem de dados](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

No Visual Studio, pode ler os dados do armazenamento de BLOBs, fazer alguma manipulação de dados, engenharia de funcionalidades e os dados resultantes para o Azure Data Lake ou armazenamento de Blobs do Azure de saída. Quando referencia os dados no armazenamento de BLOBs, utilize **wasb: / /**; quando referencia os dados no Azure Data Lake, utilize **swbhdfs: / /**

![Captura de ecrã da consulta com a entrada WASB realçada](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

Pode usar as seguintes consultas de U-SQL no Visual Studio:

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

Após a sua consulta é enviada para o servidor, é apresentado um diagrama que mostra o estado de seu trabalho.

![Captura de ecrã do Estado da caixa de diálogo de tarefa](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**Consultar dados no Data Lake: U-SQL**

Depois do conjunto de dados é ingerido para o Azure Data Lake, pode utilizar [linguagem U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) para consultar e explorar os dados. Linguagem U-SQL é semelhante ao T-SQL, mas combina alguns recursos da linguagem c#, para que os utilizadores podem escrever módulos personalizados, funções definidas pelo utilizador e etc. Pode usar os scripts no passo anterior.

Depois da consulta foi submetida para o servidor, tripdata_summary. CSV pode ser encontrado em breve **do Azure Data Lake Explorer**, pode pré-visualizar os dados pelo botão direito do mouse o ficheiro.

![Captura de ecrã do ficheiro csv no Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

Para ver as informações de ficheiro:

![Captura de ecrã das informações de resumo do ficheiro](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Clusters do Hadoop HDInsight
O Azure HDInsight é um serviço gerido de Apache Hadoop, Spark, HBase e Storm na cloud. Pode trabalhar facilmente com clusters do HDInsight do Azure partir da máquina de virtual de ciência de dados.

**Pré-requisito**

* Criar a sua conta de armazenamento de Blobs do Azure partir [portal do Azure](https://portal.azure.com). Esta conta de armazenamento é utilizada para armazenar os dados para os clusters do HDInsight.

![Captura de ecrã da criação de HDInsight a partir do portal do Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Personalizar Clusters do Hadoop HDInsight do Azure de [portal do Azure](../team-data-science-process/customize-hadoop-cluster.md)
  
  * Ligar a conta de armazenamento criada com o seu cluster do HDInsight quando é criado. Esta conta de armazenamento é utilizada para aceder aos dados que podem ser processados dentro do cluster.

![Ligar à conta de armazenamento criada com o cluster do HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Ativar **acesso remoto** ao nó principal do cluster depois de criado. Lembre-se as credenciais de acesso remoto que especificar aqui à medida que irá precisar no procedimento subsequente.

![Ativar o acesso remoto para o cluster do HDInsight](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Crie uma área de trabalho do Azure Machine Learning. Suas experiências de Machine Learning são armazenadas nesta área de trabalho do Machine Learning. Selecione as opções realçadas no Portal, conforme mostrado na captura de ecrã seguinte:

![Criar um espaço de trabalho do Azure Machine Learning](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Em seguida, introduza os parâmetros para a área de trabalho

![Introduza os parâmetros de área de trabalho do Machine Learning](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Carregar dados com o IPython Notebook. Primeiro Importe pacotes necessários, plug-in de credenciais, criar uma BD na sua conta de armazenamento, em seguida, carregar dados para clusters HDI.

```python
#Import required Packages
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


#Create the connection to Hive using ODBC
SERVER_NAME='xxx.azurehdinsight.net'
DATABASE_NAME='nyctaxidb'
USERID='xxx'
PASSWORD='xxxx'
DB_DRIVER='Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor=connection.cursor()


#Create Hive database and tables
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


#Upload data from blob storage to HDI cluster
for i in range(1,13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
    cursor.execute(queryString)
```

* Em alternativa, pode seguir esta [passo a passo](../team-data-science-process/hive-walkthrough.md) para carregar dados de táxis de NYC para o cluster do HDI. Os principais passos incluem:
  
  * AzCopy: Transferir zipado CSV do blob público para sua pasta local
  * AzCopy: carregar descompactados CSVS da pasta local para o cluster do HDI
  * Inicie sessão no nó principal do cluster do Hadoop e preparar para a análise de dados exploratório

Depois dos dados são carregados para o cluster do HDI, pode verificar os dados no Explorador de armazenamento do Azure. E tem um nyctaxidb de base de dados criado no cluster do HDI.

**Exploração de dados: Consultas do Hive no Python**

Uma vez que os dados estão no cluster do Hadoop, pode utilizar o pacote de pyodbc para ligar a Clusters do Hadoop e consulta de base de dados utilizando o Hive para fazer a exploração e "feature Engineering". Pode ver as tabelas existentes que criámos no passo dos pré-requisitos.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString,connection)
```

![Ver tabelas existentes](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Vamos examinar o número de registos em cada mês e as frequências de colocado para ou não na tabela viagem:

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

![Desenho de número de registos de cada mês](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

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
results = pd.read_sql(queryString,connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![Desenho de frequências de sugestão](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Pode também computar a distância entre o local de retirada e a localização de redução e depois o compare com a distância de viagem.

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
results = pd.read_sql(queryString,connection)
results.head(5)
```

![Primeiras linhas da tabela de recolha e de redução](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                    'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance']<=100] #remove outliers
df = df.loc[df['direct_distance']<=100] #remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Desenho de distância de recolha/redução à distância de viagem](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Agora vamos preparar um conjunto de (1%) do objeto de amostragem para baixo de dados para a Modelagem. Pode usar esses dados no módulo de leitor de Machine Learning.

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

Agora inserir conteúdo da associação na tabela anterior interna

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

Depois de um tempo, pode ver que os dados terem sido carregados em clusters do Hadoop:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString,connection)
```

![Primeiras linhas de dados da tabela](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Ler dados a partir do HDI com o Machine Learning: módulo leitor**

Também pode utilizar o **leitor** módulo no Machine Learning Studio para acessar o banco de dados no cluster do Hadoop. Conecte-se as credenciais da sua HDI clusters e a conta de armazenamento do Azure para ativar a criação de modelos de machine learning com a base de dados em clusters HDI.

![Propriedades do módulo leitor](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

O conjunto de dados com a pontuação, em seguida, pode ser exibido:

![Ver conjunto de dados com a pontuação](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>O Azure SQL Data Warehouse e as bases de dados
O Azure SQL Data Warehouse é um armazém de dados elástico como um serviço com a experiência do SQL Server de classe empresarial.

Pode aprovisionar o seu armazém de dados SQL do Azure ao seguir as instruções fornecidas neste [artigo](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Depois de aprovisionar o seu armazém de dados SQL do Azure, pode utilizá-lo [passo a passo](../team-data-science-process/sqldw-walkthrough.md) para fazer o carregamento de dados, exploração e modelação com dados no SQL Data Warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
O Azure Cosmos DB é uma base de dados NoSQL na nuvem. Ele permite-lhe trabalhar com documentos, como JSON e permite-lhe armazenar e consultar os documentos.

Seguem-se os passos de por-cumpre para aceder ao Azure Cosmos DB da DSVM:

1. O SDK de Python do Azure Cosmos DB já está instalado no DSVM (executar ```pip install pydocumentdb --upgrade``` da linha de comandos para atualizar)
2. Criar uma conta do Azure Cosmos DB e bases de dados de [portal do Azure](https://portal.azure.com)
3. Transferir "Do Azure Cosmos DB Migration Tool" a partir [aqui](https://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) e extrair para um diretório à sua escolha
4. Importar dados JSON (dados volcano) armazenados num [blob público](https://cahandson.blob.core.windows.net/samples/volcano.json) para o Cosmos DB com parâmetros de comando a seguir para a ferramenta de migração (dtui.exe do diretório onde instalou a ferramenta de migração do Cosmos DB). Introduza a localização de origem e de destino com estes parâmetros:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Depois de importar os dados, pode ir para o Jupyter e abra o bloco de notas intitulado *DocumentDBSample* que contém o código do python para aceder ao Azure Cosmos DB e fazer algumas consulta básica. Pode saber mais sobre o Cosmos DB, visitando o serviço [página de documentação](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Criar relatórios e dashboard através do Power BI Desktop
É possível visualizar o ficheiro de JSON de Volcano do exemplo anterior do Cosmos DB no Power BI para obter informações visual sobre os dados. Os passos detalhados estão disponíveis no [artigo do Power BI](../../cosmos-db/powerbi-visualize.md). Eis os passos de alto nível:

1. Abra o Power BI Desktop e "obter dados." Especifique o URL como: https://cahandson.blob.core.windows.net/samples/volcano.json
2. Deverá ver os registos JSON importados como uma lista
3. Converter a lista a uma tabela para que Power BI possa trabalhar com o mesmo
4. Expanda as colunas ao clicar no ícone de expansão (aquele com o ícone "seta para a esquerda e uma seta para a direita" à direita da coluna)
5. Tenha em atenção que a localização é um campo de "Registo". Expanda o registo e selecionar apenas as coordenadas. Coordenada é uma coluna de lista
6. Adicionar uma nova coluna para converter a coluna de coordenadas de lista numa coluna de LatLong separada por vírgulas concatenar os dois elementos no campo de lista coordenada utilizando a fórmula ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Finalmente converter os ```Elevation``` coluna para Número Decimal e selecione o **fechar** e **aplicar**.

Em vez de anteriores passos, pode colar o código a seguir que scripts fora os passos utilizados no Advanced Editor no Power BI, que permite-lhe escrever as transformações de dados numa linguagem de consulta.

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

Agora tem os dados no seu modelo de dados do Power BI. Sua área de trabalho do Power BI deverá aparecer da seguinte forma:

![Ambiente de trabalho do Power BI](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Pode começar a criar relatórios e visualizações com o modelo de dados. Pode seguir os passos neste [artigo do Power BI](../../cosmos-db/powerbi-visualize.md#build-the-reports) para criar um relatório. O resultado é um relatório que é semelhante ao seguinte.

![Power BI Desktop vista de relatório - conector do Power BI](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. Dimensionar dinamicamente o sua DSVM para satisfazer as necessidades do projeto
Pode aumentar e diminuir a DSVM para satisfazer as necessidades do projeto. Se não precisar de utilizar a VM na noite ou fins de semana, pode simplesmente encerrar a VM a partir da [portal do Azure](https://portal.azure.com).

> [!NOTE]
> Ter custos de computação se utilizar apenas o botão de encerramento do sistema operativo na VM.  
> 
> 

Se tiver de lidar com algumas análises em grande escala e precisar de mais capacidade de CPU e/ou de memória e/ou de disco pode encontrar uma grande escolha dos tamanhos de VM em termos de núcleos de CPU, as instâncias baseadas em GPU para aprendizagem profunda, capacidade de memória e os tipos de disco (incluindo unidades de estado sólido) que atender às suas necessidades orçamentárias e de computação. A lista completa de VMs, juntamente com os preços de computação à hora está disponível na [preços de máquinas virtuais do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/) página.

Da mesma forma, se reduz a necessidade de capacidade de processamento de VM (por exemplo: mover uma carga de trabalho principal para um Hadoop ou um cluster do Spark), pode reduzir verticalmente o cluster a partir da [portal do Azure](https://portal.azure.com) e vai para as definições da sua instância VM. Eis uma captura de ecrã.

![Definições de instâncias VM](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. Instale as ferramentas adicionais na sua máquina virtual
Existem várias ferramentas pré-incorporadas na DSVM que pode resolver muitos do common data analytics precisa. Desta forma, poupa tempo ao evitar ter de instalar e configurar seus ambientes individualmente e poupar dinheiro ao pagar apenas pelos recursos que use.

Pode utilizar outros serviços de dados e análise do Azure com perfis criados neste artigo para melhorar o seu ambiente de análise. Em alguns casos, suas necessidades podem requerer ferramentas adicionais, incluindo algumas ferramentas de terceiros proprietárias. Tem acesso administrativo total na máquina virtual para instalar novas ferramentas de que precisa. Também pode instalar pacotes adicionais do Python e R que não estejam instalados previamente. Para Python pode utilizar ```conda``` ou ```pip```. Para R, pode utilizar o ```install.packages()``` no R consola ou usar o IDE e escolha "**pacotes** -> **pacotes de instalação...** ".

## <a name="summary"></a>Resumo
Estes são apenas algumas das coisas que pode fazer na máquina Virtual Microsoft dados ciência. Existem muitas mais coisas que pode fazer para que seja um ambiente de análise em vigor.

