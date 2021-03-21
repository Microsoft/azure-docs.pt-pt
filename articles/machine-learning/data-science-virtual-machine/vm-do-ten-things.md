---
title: Explore dados e modelo no Windows
titleSuffix: Azure Data Science Virtual Machine
description: Execute tarefas de exploração e modelação de dados na Máquina Virtual da Ciência dos Dados do Windows.
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: c44c40f2ddab53cf0beb5ecd48127d126fdb4333
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101657405"
---
# <a name="data-science-with-a-windows-data-science-virtual-machine"></a>Ciência de dados com uma máquina virtual de ciência de dados do Windows

A Máquina Virtual de Ciência de Dados do Windows (DSVM) é um poderoso ambiente de desenvolvimento de ciência de dados onde pode realizar tarefas de exploração e modelação de dados. O ambiente vem já construído e agregado com várias ferramentas populares de análise de dados que facilitam o arranque com a sua análise para implementações no local, nuvem ou híbrido. 

A DSVM trabalha em estreita colaboração com os serviços da Azure. Pode ler e processar dados que já estão armazenados no Azure, em Azure Synapse (anteriormente SQL DW),Azure Data Lake, Azure Storage, ou Azure Cosmos DB. Também pode tirar partido de outras ferramentas de análise, como a Azure Machine Learning.

Neste artigo, você vai aprender a usar o seu DSVM para executar tarefas de ciência de dados e interagir com outros serviços Azure. Aqui estão algumas das coisas que pode fazer no DSVM:

- Use um Jupyter Notebook para experimentar os seus dados num browser utilizando Python 2, Python 3 e Microsoft R. (o Microsoft R é uma versão de R pronta para a empresa projetada para o desempenho.)
- Explore dados e desenvolva modelos localmente no DSVM utilizando o Microsoft Machine Learning Server e o Python.
- Administrar os seus recursos Azure utilizando o portal Azure ou o PowerShell.
- Estenda o seu espaço de armazenamento e partilhe conjuntos de dados/códigos em larga escala em toda a sua equipa, criando uma partilha de Ficheiros Azure como uma unidade montável no seu DSVM.
- Partilhe código com a sua equipa utilizando o GitHub. Aceda ao seu repositório utilizando os clientes Git pré-instalados: Git Bash e Git GUI.
- Acesso Azure dados e serviços de análise como a Azure Blob storage, Azure Cosmos DB, Azure Synapse (anteriormente SQL DW) e Azure SQL Database.
- Construa relatórios e um dashboard utilizando a instância power BI desktop que está pré-instalada no DSVM e implemente-os na nuvem.

- Instale ferramentas adicionais na sua máquina virtual.   

> [!NOTE]
> Taxas de utilização adicionais aplicam-se a muitos dos serviços de armazenamento de dados e análise listados neste artigo. Para mais detalhes, consulte a página [de preços do Azure.](https://azure.microsoft.com/pricing/)
> 
> 

## <a name="prerequisites"></a>Pré-requisitos

* Precisa de uma assinatura Azure. Pode [inscrever-se para um julgamento gratuito.](https://azure.microsoft.com/free/)
* As instruções para o fornecimento de uma máquina virtual de ciência de dados no portal Azure estão disponíveis na [Criação de uma máquina virtual](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="use-jupyter-notebooks"></a>Utilizar Blocos de Notas do Jupyter
O Jupyter Notebook fornece um IDE baseado no navegador para a exploração e modelação de dados. Pode utilizar Python 2, Python 3 ou R (ambas de código aberto e Servidor Microsoft R) num Bloco de Notas Jupyter.

Para iniciar o Bloco de Notas Jupyter, selecione o ícone **Jupyter Notebook** no menu **Iniciar** ou no ambiente de trabalho. No pedido de comando DSVM, também pode executar o comando ```jupyter notebook``` a partir do diretório onde tem cadernos existentes ou onde pretende criar novos cadernos.  

Depois de iniciar o Jupyter, navegue para o `/notebooks` diretório, por exemplo, cadernos que são pré-embalados no DSVM. Agora pode:

* Selecione o caderno para ver o código.
* Executar cada célula selecionando Shift+Enter.
* Executar todo o caderno selecionando **Cell**  >  **Run**.
* Crie um novo caderno selecionando o ícone Jupyter (canto superior esquerdo), selecionando o botão **Novo** à direita e, em seguida, escolhendo o idioma do portátil (também conhecido como kernels).   

> [!NOTE]
> Atualmente, os núcleos Python 2.7, Python 3.6, R, Julia e PySpark em Jupyter são apoiados. O kernel R suporta a programação tanto em open-source R como microsoft R.   
> 
> 

Quando estiver no caderno, pode explorar os seus dados, construir o modelo e testar o modelo utilizando a sua escolha de bibliotecas.

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Explore dados e desenvolva modelos com o Microsoft Machine Learning Server
Pode utilizar idiomas como R e Python para fazer a análise de dados diretamente no DSVM.

Para R, pode utilizar um IDE como o RStudio que pode ser encontrado no menu inicial ou no ambiente de trabalho. Ou pode usar ferramentas R para estúdio visual. A Microsoft forneceu bibliotecas adicionais em cima do CRAN R de código aberto para permitir análises escaláveis e a capacidade de analisar dados maiores do que o tamanho da memória permitido em análises paralelas. 

Para python, você pode usar um IDE como Visual Studio Community Edition, que tem a extensão Python Tools for Visual Studio (PTVS) pré-instalada. Por padrão, apenas Python 3.6, o ambiente conda raiz, está configurado em PTVS. Para ativar a Anaconda Python 2.7, tome os seguintes passos:

1. Crie ambientes personalizados para cada versão indo para **Ferramentas**  >  **Python Python**  >  **Ambientes,** e, em seguida, selecione + **Custom** in Visual Studio Community Edition.
1. Dê uma descrição e estabeleça o caminho do prefixo do ambiente como **c:\anaconda\envs\python2** para Anaconda Python 2.7.
1. Selecione **Deteção automática**  >  **Aplicar** para salvar o ambiente.

Consulte a documentação do [PTVS](/visualstudio/python/) para obter mais detalhes sobre como criar ambientes Python.

Agora está preparado para criar um novo projeto Python. Vá ao **Arquivo**  >  **Novo**  >  **Projeto**  >  **Python** e selecione o tipo de aplicação Python que está a construir. Pode definir o ambiente Python para o projeto atual para a versão desejada (Python 2.7 ou 3.6) clicando à direita **em ambientes Python** e, em seguida, selecionando **Ambientes Add/Remove Python**. Pode encontrar mais informações sobre o trabalho com o PTVS na documentação do [produto.](/visualstudio/python/)



## <a name="manage-azure-resources"></a>Gerir os recursos do Azure
O DSVM não permite apenas construir a sua solução de análise localmente na máquina virtual. Também permite aceder a serviços na plataforma cloud Azure. O Azure fornece vários serviços de computação, armazenamento, análise de dados e outros serviços que pode administrar e aceder a partir do seu DSVM.

Para administrar a sua subscrição Azure e recursos em nuvem, tem duas opções:
+ Use o seu navegador e vá ao [portal Azure.](https://portal.azure.com)

+ Use scripts PowerShell. Executar Azure PowerShell a partir de um atalho no ambiente de trabalho ou a partir do menu **Iniciar.** Consulte a documentação do [Microsoft Azure PowerShell](../../azure-resource-manager/management/manage-resources-powershell.md) para obter todos os detalhes. 

## <a name="extend-storage-by-using-shared-file-systems"></a>Alargar o armazenamento utilizando sistemas de ficheiros partilhados
Os cientistas de dados podem partilhar grandes conjuntos de dados, código ou outros recursos dentro da equipa. O DSVM tem cerca de 45 GB de espaço disponível. Para prolongar o seu armazenamento, pode utilizar ficheiros Azure e montá-lo em uma ou mais instâncias DSVM ou acede-lo através de uma API REST. Também pode utilizar o [portal Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) ou utilizar [o Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) para adicionar discos de dados extra dedicados. 

> [!NOTE]
> O espaço máximo na partilha de Ficheiros Azure é de 5 TB. O limite de tamanho para cada ficheiro é 1 TB. 

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

Agora que criou uma partilha de Ficheiros Azure, pode montá-lo em qualquer máquina virtual em Azure. Recomendamos que coloque o VM no mesmo datacenter Azure que a conta de armazenamento, para evitar encargos de latência e transferência de dados. Aqui estão os comandos Azure PowerShell para montar a unidade no DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Agora pode aceder a esta unidade como faria com qualquer unidade normal no VM.

## <a name="share-code-in-github"></a>Código de partilha no GitHub
O GitHub é um repositório de código onde pode encontrar amostras de código e fontes para várias ferramentas utilizando tecnologias partilhadas pela comunidade desenvolvedora. Usa o Git como tecnologia para rastrear e armazenar versões dos ficheiros de código. O GitHub é também uma plataforma onde pode criar o seu próprio repositório para armazenar o código e documentação partilhados da sua equipa, implementar o controlo de versão e controlar quem tem acesso a ver e contribuir com código. 

Visite as páginas de ajuda do [GitHub](https://help.github.com/) para obter mais informações sobre a utilização do Git. Você pode usar o GitHub como uma das formas de colaborar com a sua equipa, usar código desenvolvido pela comunidade, e contribuir com o código de volta para a comunidade.

O DSVM vem carregado com ferramentas de cliente na linha de comando e no GUI para aceder ao repositório GitHub. A ferramenta de linha de comando que funciona com Git e GitHub chama-se Git Bash. O Visual Studio está instalado no DSVM e tem as extensões Git. Pode encontrar ícones para estas ferramentas no menu **Iniciar** e no ambiente de trabalho.

Para descarregar código a partir de um repositório GitHub, use o ```git clone``` comando. Por exemplo, para transferir o repositório de ciência de dados publicado pela Microsoft para o diretório atual, pode executar o seguinte comando em Git Bash:

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

No Visual Studio, podes fazer a mesma operação de clone. A imagem que se segue mostra como aceder às ferramentas Git e GitHub no Visual Studio:

![Screenshot do Estúdio Visual com a ligação GitHub exibida](./media/vm-do-ten-things/VSGit.png)

Você pode encontrar mais informações sobre a utilização de Git para trabalhar com o seu repositório GitHub a partir de recursos disponíveis em github.com. A [folha de batota](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) é uma referência útil.

## <a name="access-azure-data-and-analytics-services"></a>Acesso Azure dados e serviços de análise
### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure
O armazenamento Azure Blob é um serviço de armazenamento de nuvem fiável e económico para dados grandes e pequenos. Esta secção descreve como pode mover dados para o armazenamento blob e aceder aos dados armazenados numa bolha Azure.

#### <a name="prerequisites"></a>Pré-requisitos

* Crie a sua conta de armazenamento Azure Blob a partir do [portal Azure](https://portal.azure.com).

   ![Screenshot do processo de criação de conta de armazenamento no portal Azure](./media/vm-do-ten-things/create-azure-blob.png)

* Confirme se a ferramenta AzCopy da linha de comando está pré-instalada: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` . O diretório que contém azcopy.exe já está na variável ambiente PATH, para que possa evitar digitar o caminho de comando completo ao executar esta ferramenta. Para obter mais informações sobre a ferramenta AzCopy, consulte a [documentação AzCopy](../../storage/common/storage-use-azcopy-v10.md).
* Inicie a ferramenta Azure Storage Explorer. Pode descarregá-lo na [página do Storage Explorer.](https://storageexplorer.com/) 

   ![Screenshot do Azure Storage Explorer a aceder a uma conta de armazenamento](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Mover dados de um VM para uma bolha Azure: AzCopy

Para mover dados entre os seus ficheiros locais e o armazenamento Blob, pode utilizar o AzCopy na linha de comando ou no PowerShell:

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

Substitua **c:\myfolder** pelo caminho onde o seu ficheiro está armazenado, **contagem de mystorage** com o nome da sua conta de armazenamento Blob, **mycontainer** com o nome do recipiente e **chave de conta de armazenamento** com a sua chave de acesso de armazenamento Blob. Pode encontrar as suas credenciais de conta de armazenamento no [portal Azure.](https://portal.azure.com)

Executar o comando AzCopy em PowerShell ou a partir de um pedido de comando. Aqui está um exemplo de utilização do comando AzCopy:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Depois de executar o comando AzCopy para copiar para uma bolha Azure, o seu ficheiro aparecerá no Azure Storage Explorer.

![Screenshot da conta de armazenamento, exibindo o ficheiro CSV carregado](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Mover dados de um VM para uma bolha Azure: Azure Storage Explorer

Também pode fazer o upload de dados a partir do ficheiro local no seu VM utilizando o Azure Storage Explorer:

* Para enviar dados para um recipiente, selecione o recipiente-alvo e selecione o botão **Upload.** ![ Screenshot do botão de upload no Azure Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* Selecione a elipse **(...**) para a direita da caixa **Ficheiros,** selecione um ou vários ficheiros para carregar a partir do sistema de ficheiros e selecione **upload** para começar a carregar os ficheiros. ![ Screenshot da caixa de diálogo de ficheiros upload](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Leia os dados de uma bolha de Azure: Python ODBC

Pode utilizar a biblioteca BlobService para ler dados diretamente de uma bolha num Bloco de Notas do Jupyter ou num programa Python.

Em primeiro lugar, importar as embalagens necessárias:

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

Em seguida, ligue as suas credenciais de conta de armazenamento Blob e leia os dados da bolha:

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

![Screenshot das primeiras 10 linhas de dados](./media/vm-do-ten-things/IPNB_data_readin.png)


### <a name="azure-synapse-analytics-and-databases"></a>Azure Synapse Analytics e bases de dados
A Azure Synapse Analytics é um armazém de dados elástico como um serviço com uma experiência de sql server de classe empresarial.

Pode providenciar a Azure Synapse Analytics seguindo as instruções [deste artigo.](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) Depois de providenciar a Azure Synapse Analytics, pode utilizar [este walkthrough](../team-data-science-process/sqldw-walkthrough.md) para fazer upload, exploração e modelação de dados utilizando dados dentro do Azure Synapse Analytics.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB é uma base de dados NoSQL na nuvem. Você pode usá-lo para trabalhar com documentos como JSON, e para armazenar e consultar os documentos.

Utilize os seguintes passos pré-requisitos para aceder ao Azure Cosmos DB a partir do DSVM:

1. O Azure Cosmos DB Python SDK já está instalado no DSVM. Para atualizá-lo, corra ```pip install pydocumentdb --upgrade``` a partir de um pedido de comando.
2. Crie uma conta DB Azure Cosmos e base de dados a partir do [portal Azure](https://portal.azure.com).
3. Descarregue a Ferramenta de Migração de Dados DB Azure Cosmos do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) e extraia para um diretório à sua escolha.
4. Importar dados JSON (dados do vulcão) armazenados numa [bolha pública](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) em Azure Cosmos DB com os seguintes parâmetros de comando para a ferramenta de migração. (Utilize dtui.exe do diretório onde instalou a Ferramenta de Migração de Dados Azure Cosmos DB.) Insira a origem e a localização do alvo com estes parâmetros:
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Depois de importar os dados, pode ir ao Jupyter e abrir o caderno intitulado *DocumentDBSample*. Contém código Python para aceder a Azure Cosmos DB e fazer algumas consultas básicas. Você pode saber mais sobre Azure Cosmos DB visitando a página de [documentação](../../cosmos-db/index.yml)do serviço .

## <a name="use-power-bi-reports-and-dashboards"></a>Use relatórios e dashboards power bi 
Pode visualizar o ficheiro Vulcão JSON a partir do exemplo DB do Azure Cosmos anterior no Power BI Desktop para obter informações visuais sobre os dados. Estão disponíveis passos detalhados no artigo do [Power BI](../../cosmos-db/powerbi-visualize.md). Aqui estão os passos de alto nível:

1. Abra o Power BI Desktop e selecione **Obter Dados**. Especificar o URL como: `https://cahandson.blob.core.windows.net/samples/volcano.json` .
2. Devia ver os registos da JSON importados como uma lista. Converta a lista numa tabela para que o Power BI possa trabalhar com ela.
4. Expanda as colunas selecionando o ícone de expansão (seta).
5. Note que a localização é um campo **record.** Expanda o registo e selecione apenas as coordenadas. **Coordenada** é uma coluna de lista.
6. Adicione uma nova coluna para converter a coluna de coordenadas de lista numa coluna **LatLong** separada por vírgula. Concatenate os dois elementos no campo da lista de coordenadas utilizando a fórmula ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` .
7. Converta a coluna **Elevação** em decimal e selecione os botões **Fechar** e **Aplicar.**

Em vez de passos anteriores, pode colar o seguinte código. Ele scripts out os passos usados no Editor Avançado no Power BI para escrever as transformações de dados em uma linguagem de consulta.

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

Tem agora os dados no seu modelo de dados Power BI. A sua instância de ambiente de trabalho Power BI deve aparecer da seguinte forma:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Pode começar a construir relatórios e visualizações utilizando o modelo de dados. Pode seguir os passos [deste artigo do Power BI](../../cosmos-db/powerbi-visualize.md#build-the-reports) para construir um relatório.

## <a name="scale-the-dsvm-dynamically"></a>Dimensione o DSVM dinamicamente 
Você pode escalar para cima e para baixo o DSVM para atender às necessidades do seu projeto. Se não precisar de utilizar o VM à noite ou aos fins de semana, pode desligar o VM do [portal Azure](https://portal.azure.com).

> [!NOTE]
> Incorre em cargas de cálculo se utilizar apenas o botão de paragem para o sistema operativo no VM. Em vez disso, deverá negociar o seu DSVM utilizando o portal Azure ou cloud Shell.
> 
> 

Pode precisar de alguma análise em larga escala e precisar de mais CPU, memória ou capacidade de disco. Em caso afirmativo, pode encontrar uma escolha de tamanhos VM em termos de núcleos de CPU, instâncias baseadas em GPU para aprendizagem profunda, capacidade de memória e tipos de discos (incluindo unidades de estado sólido) que atendam às suas necessidades computacional e orçamental. A lista completa de VMs, juntamente com o seu preço de computação horária, está disponível na página de preços das [Máquinas Virtuais Azure.](https://azure.microsoft.com/pricing/details/virtual-machines/)


## <a name="add-more-tools"></a>Adicione mais ferramentas
As ferramentas pré-construídas no DSVM podem responder a muitas necessidades comuns de análise de dados. Isto poupa-lhe tempo porque não precisa de instalar e configurar os seus ambientes um a um. Também te poupa dinheiro, porque só pagas os recursos que usas.

Pode utilizar outros serviços de dados e análises da Azure perfilado neste artigo para melhorar o seu ambiente de análise. Em alguns casos, poderá precisar de ferramentas adicionais, incluindo algumas ferramentas de parceiros proprietários. Tem acesso administrativo total na máquina virtual para instalar novas ferramentas de que necessita. Também pode instalar pacotes adicionais em Python e R que não estejam pré-instalados. Para Python, você pode usar qualquer um ```conda``` ou ```pip``` . Para R, pode utilizar ```install.packages()``` na consola R ou utilizar o IDE e selecionar Pacotes de Instalação de   >  **Pacotes**.

## <a name="deep-learning"></a>Aprendizagem aprofundada

Além das amostras baseadas em quadros, você pode obter um conjunto de walkthroughs abrangentes que foram validados no DSVM. Estas imagens ajudam-no a iniciar o seu desenvolvimento de aplicações de aprendizagem profunda em domínios como a compreensão de imagem e texto/linguagem.   


- [Executando redes neuronais em diferentes estruturas](https://github.com/ilkarman/DeepLearningFrameworks): Este walkthrough mostra como migrar código de uma estrutura para outra. Também demonstra como comparar modelos e desempenho de tempo de execução entre quadros. 

- [Um guia de como construir uma solução de ponta a ponta para detetar produtos dentro de imagens](https://github.com/Azure/cortana-intelligence-product-detection-from-images): A deteção de imagem é uma técnica que pode localizar e classificar objetos dentro das imagens. Esta tecnologia tem o potencial de trazer enormes recompensas em muitos domínios de negócios da vida real. Por exemplo, os retalhistas podem utilizar esta técnica para determinar que produto um cliente recolheu da prateleira. Esta informação, por sua vez, ajuda as lojas a gerir o inventário do produto. 

- [Aprendizagem profunda para áudio](/archive/blogs/machinelearning/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure): Este tutorial mostra como treinar um modelo de aprendizagem profunda para deteção de eventos áudio no [conjunto de dados de sons urbanos.](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) Também fornece uma visão geral de como trabalhar com dados áudio.

- [Classificação de documentos de texto](https://github.com/anargyri/lstm_han): Este walkthrough demonstra como construir e formar duas arquiteturas de rede neural: Rede hierárquica de atenção e rede de memória de longo prazo (LSTM). Estas redes neurais utilizam a API keras para uma aprendizagem profunda para classificar documentos de texto. 

## <a name="summary"></a>Resumo
Este artigo descreveu algumas das coisas que pode fazer na Máquina Virtual da Ciência dos Dados da Microsoft. Há muitas mais coisas que pode fazer para tornar o DSVM um ambiente de análise eficaz.
