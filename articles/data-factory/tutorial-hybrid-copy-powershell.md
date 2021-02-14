---
title: Copie os dados do SQL Server para o armazenamento blob usando o PowerShell
description: Saiba como copiar dados de um arquivo de dados no local para a cloud do Azure mediante a utilização de um integration runtime autoalojado no Azure Data Factory.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019, devx-track-azurepowershell
ms.date: 01/22/2018
ms.openlocfilehash: fc709147c61152bc85db96a059501a12c40a9332
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391223"
---
# <a name="tutorial-copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>Tutorial: Copiar dados de uma base de dados do SQL Server para o armazenamento do Azure Blob

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, você usa a Azure PowerShell para criar um pipeline de fábrica de dados que copia dados de uma base de dados do SQL Server para o armazenamento de Azure Blob. Vai criar e utilizar um runtime de integração autoalojado, que move dados entre arquivos de dados no local e na cloud.

> [!NOTE]
> Este artigo não disponibiliza uma introdução detalhada do serviço Data Factory. Para obter mais informações, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um integration runtime autoalojado.
> * Criar serviços ligados do SQL Server e do Armazenamento do Azure.
> * Criar conjuntos de dados do SQL Server e dos Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="azure-subscription"></a>Subscrição do Azure
Antes de começar, se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Funções do Azure
Para criar casos de fábrica de dados, a conta de utilizador que utiliza para iniciar sessão no Azure deve ser atribuída a uma função *de Contribuinte* ou *Proprietário* ou deve ser *administradora* da subscrição Azure.

Para ver as permissões que a sua subscrição tem, aceda ao portal do Azure, selecione o seu nome de utilizador, no canto superior direito, e selecione **Permissões**. Se tiver acesso a várias subscrições, selecione a subscrição apropriada. Para obter instruções de amostra sobre a adição de um utilizador a uma função, consulte as atribuições de [funções Azure utilizando o artigo do portal Azure.](../role-based-access-control/role-assignments-portal.md)

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
Neste tutorial, utiliza uma base de dados SQL Server como uma loja de dados *de origem.* O pipeline na fábrica de dados que cria neste tutorial copia dados desta base de dados sql Server (fonte) para armazenamento Azure Blob (pia). Vai criar uma tabela com o nome **emp** na sua base de dados do SQL Server e insira duas entradas de exemplo na mesma.

1. Inicie o SQL Server Management Studio. Se ainda não estiver instalado no seu computador, aceda a [Transferir o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Utilize as suas credenciais para se ligar à sua instância do SQL Server.

1. Crie uma base de dados de exemplo. Na vista de árvore, clique com o botão direito do rato em **Bases de Dados** e selecione **Nova Base de Dados**.

1. Na janela **Nova Base de Dados**, introduza um nome para a base de dados e selecione **OK**.

1. Para criar a tabela **emp** e inserir alguns dados de exemplo na mesma, execute o script de consulta seguinte na base de dados. Na vista de árvore, clique com o botão direito do rato na base de dados que criou e selecione **Nova Consulta**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```


### <a name="azure-storage-account"></a>Conta de armazenamento do Azure
Neste tutorial, vai utilizar uma Conta de Armazenamento do Azure de fins gerais (mais concretamente, o Armazenamento de Blobs do Azure) como arquivo de dados de destino/sink. Se não tiver uma conta de armazenamento do Azure para fins gerais, veja [Criar uma conta de armazenamento](../storage/common/storage-account-create.md). O pipeline na fábrica de dados que cria neste tutorial copia dados da base de dados sql Server (fonte) para este armazenamento Azure Blob (pia). 

#### <a name="get-storage-account-name-and-account-key"></a>Obter o nome e a chave da conta de armazenamento
Utilize o nome e a chave da sua conta de armazenamento do Azure neste tutorial. Obtenha o nome e a chave da sua conta de armazenamento da seguinte forma:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com o seu nome de utilizador e sua palavra-passe do Azure.

1. No painel do lado esquerdo, selecione **Mais Serviços**, utilize a palavra-chave **Armazenamento** para filtrar e selecione **Contas de Armazenamento**.

    ![Procurar contas de armazenamento](media/doc-common-process/search-storage-account.png)

1. Na lista das contas de armazenamento, filtre para encontrar a sua conta de armazenamento (se necessário) e selecione-a.

1. Na janela **Conta de armazenamento**, selecione **Chaves de acesso**.

1. Nas caixas **Nome da conta de armazenamento** e **key1**, copie os valores e cole-os no Bloco de notas ou noutro editor, para utilizar mais adiante no tutorial.

#### <a name="create-the-adftutorial-container"></a>Criar o contentor adftutorial
Nesta secção, vai criar um contentor de blobs com o nome **adftutorial** no seu armazenamento de Blobs do Azure.

1. Na janela **Conta de armazenamento**, mude para a **Descrição Geral** e selecione **Blobs**.

    ![Selecionar a opção Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Na janela **Serviço de Blob**, selecione **Contentor**.

1. Na janela **Novo Contentor**, na caixa **Nome**, introduza **adftutorial** e selecione **OK**.

    ![Introduzir o nome do contentor](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Na lista de contentores, clique em **adftutorial**.  

1. Mantenha a janela do **recipiente** aberta para **o adftutorial.** Vai utilizá-la para verificar o resultado no final deste tutorial. O Data Fabric cria automaticamente a pasta de saída neste contentor, pelo que não precisa de a criar.


### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Instalar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Instale a versão mais recente do Azure PowerShell, se ainda não a tiver no seu computador. Para obter informações detalhadas, veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="log-in-to-powershell"></a>Iniciar sessão no PowerShell

1. Inicie o PowerShell no seu computador e mantenha-o aberto até concluir este tutorial de início rápido. Se o fechar e reabrir, terá de executar os comandos novamente.

1. Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe do Azure que utiliza para iniciar sessão no portal do Azure:

    ```powershell
    Connect-AzAccount
    ```        

1. Se tiver várias subscrições do Azure, execute o comando seguinte selecionar aquela com que pretende trabalhar. Substitua **a SubscriptionId** pelo ID da sua subscrição Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Defina uma variável para o nome do grupo de recursos que vai utilizar mais tarde nos comandos do PowerShell. Copie o texto do comando seguinte para o PowerShell, especifique um nome para o [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) (entre aspas duplas, como, por exemplo, `"adfrg"`) e execute o comando. 
   
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

1. Para criar o grupo de recursos do Azure, execute o comando abaixo:

    ```powershell
    New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    Se o grupo de recursos já existir, pode não substituí-lo. Atribua outro valor à variável `$resourceGroupName` e execute novamente o comando.

1. Defina uma variável para o nome da fábrica de dados que possa utilizar em comandos do PowerShell mais tarde. O nome tem de começar com uma letra ou um número e só pode conter letras, números e o caráter de travessão (-).

    > [!IMPORTANT]
    >  Atualize o nome da fábrica de dados com um que seja globalmente exclusivo. Por exemplo, ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

1. Defina uma variável para a localização da fábrica de dados:

    ```powershell
    $location = "East US"
    ```  

1. Para criar a fábrica de dados, execute o cmdlet `Set-AzDataFactoryV2`:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName
    ```

> [!NOTE]
>
> * O nome da fábrica de dados tem de ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Para criar instâncias de fábricas de dados, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ter atribuída a função *contribuidor* ou *proprietário* ou ser *administradora* da subscrição do Azure.
> * Para obter uma lista de regiões do Azure em que o Data Factory está atualmente disponível, selecione as regiões que lhe interessam na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (Azure HDInsight, etc.) que a fábrica de dados utiliza podem estar noutras regiões.
>
>

## <a name="create-a-self-hosted-integration-runtime"></a>Criar um integration runtime autoalojado

Nesta secção, vai criar um integration runtime autoalojado e vai associá-lo a um computador no local com a base de dados do SQL Server. O integration runtime autoalojado é o componente que copia os dados da base de dados SQL Server no seu computador para o armazenamento de Blobs do Azure.

1. Crie uma variável para o nome do runtime de integração. Utilize um nome exclusivo e aponte-o. Vai utilizá-lo mais tarde no tutorial.

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

1. Criar um integration runtime autoalojado.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```

    Segue-se o resultado do exemplo:

    ```console
    Name              : ADFTutorialIR
    Type              : SelfHosted
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Description       : selfhosted IR description
    Id                : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Para obter o estado do integration runtime criado, execute o comando seguinte:

    ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
    ```

    Segue-se o resultado do exemplo:

    ```console
    State                     : NeedRegistration
    Version                   :
    CreateTime                : 9/10/2019 3:24:09 AM
    AutoUpdate                : On
    ScheduledUpdateDate       :
    UpdateDelayOffset         :
    LocalTimeZoneOffset       :
    InternalChannelEncryption :
    Capabilities              : {}
    ServiceUrls               : {eu.frontend.clouddatahub.net}
    Nodes                     : {}
    Links                     : {}
    Name                      : <Integration Runtime name>
    Type                      : SelfHosted
    ResourceGroupName         : <resourceGroup name>
    DataFactoryName           : <dataFactory name>
    Description               : selfhosted IR description
    Id                        : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Para obter as *chaves de autenticação* para registar o integration runtime autoalojado no serviço Data Factory na cloud, execute o comando seguinte. Copie uma das chaves (excluindo as aspas) para registar o integration runtime autoalojado que vai instalar no computador no passo seguinte:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
    ```

    Segue-se o resultado do exemplo:

    ```json
    {
        "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
        "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
    }
    ```

## <a name="install-the-integration-runtime"></a>Instalar o integration runtime
1. Transfira o [Integration Runtime do Azure Data Factory](https://www.microsoft.com/download/details.aspx?id=39717) num computador Windows local e execute a instalação.

1. No assistente **Bem-vindo à Configuração do Microsoft Integration Runtime**, selecione **Seguinte**.  

1. Na janea **Contrato de Licença do Utilizador Final**, aceite os termos e o contrato de licença e selecione **Seguinte**.

1. Na janela **Pasta de Destino**, selecione **Seguinte**.

1. Na janela **Pronto para instalar o Microsoft Integration Runtime**, selecione **Instalar**.

1. No assistente **Configuração do Microsoft Integration Runtime Concluída**, selecione **Concluir**.

1. Na janela **Registar o Integration Runtime (Autoalojado)**, cole a chave que guardou na secção anterior e selecione **Registar**.

    ![Registar o integration runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

1. Na janela do **nó do novo número de integração (auto-hospedada),** selecione **Finish**.

    ![Janela Novo Nó do Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

 1. Quando o integration runtime autoalojado for registado com êxito, é apresentada a mensagem seguinte:

    ![Registado com êxito](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

1. Na janela **Registar Integration Runtime (Autoalojado)**, selecione **Configuration Manager**.

1. Quando o nó for ligado ao serviço cloud, é apresentada a página seguinte:

    ![O nó está ligado](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

1. Teste a conectividade à base de dados do SQL Server, fazendo o seguinte:

    a. Na janela do **Configuration Manager**, mude para o separador **Diagnósticos**.

    b. Na caixa **Tipo de origem de dados**, selecione **SqlServer**.

    c. Introduza o nome do servidor.

    d. Introduza o nome da base de dados.

    e. Selecione o modo de autenticação.

    f. Introduza o nome de utilizador.

    exemplo, Introduza a palavra-passe que está associado ao nome de utilizador.

    h. Para confirmar que o integration runtime se consegue ligar ao SQL Server selecione **Testar**.  
    ![Conectividade conseguiu](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)

    Se a ligação for bem-sucedida, é apresentada uma marca de verificação verde. Caso contrário, receberá uma mensagem de erro associada à falha. Corrija os problemas e confirme que o integration runtime se consegue ligar à sua instância do SQL Server.

    Aponte todos os valores anteriores para utilizar mais adiante neste tutorial.

## <a name="create-linked-services"></a>Criar serviços ligados
Para ligar os seus arquivos de dados e serviços de computação à fábrica de dados, crie serviços ligados na mesma. Neste tutorial, ligue a sua conta de armazenamento Azure e a instância SQL Server à loja de dados. Os serviços ligados têm as informações de ligação que o serviço do Data Factory utiliza no runtime para se ligar aos mesmos.

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Criar um serviço ligado do Armazenamento do Azure (destino/sink)
Neste passo, vai ligar a sua conta de armazenamento do Azure à fábrica de dados.

1. Crie um ficheiro JSON com o nome *AzureStorageLinkedService.json* na pasta *C:\ADFv2Tutorial* com o código seguinte. Se a pasta *ADFv2Tutorial* ainda não existir, crie-a.  

    > [!IMPORTANT]
    > Antes de guardar o ficheiro, substitua \<accountName> e pelo nome e chave da sua conta de armazenamento \<accountKey> Azure. Estas informações foram apontadas na secção [Pré-requisitos](#get-storage-account-name-and-account-key).

   ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
   ```

1. No PowerShell, mude para a pasta *C:\ADFv2Tutorial*.
   ```powershell
   Set-Location 'C:\ADFv2Tutorial'    
   ```

1. Para criar o serviço ligado AzureStorageLinkedService, execute o cmdlet `Set-AzDataFactoryV2LinkedService`:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Eis uma saída de exemplo:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroup name>
    DataFactoryName   : <dataFactory name>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

    Se receber o erro "Ficheiro não encontrado", execute o comando `dir` para confirmar que existe. Se o nome de ficheiro tiver a extensão *.txt* (por exemplo, AzureStorageLinkedService.json.txt), remove-a e execute novamente o comando do PowerShell.

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Criar e encriptar um serviço ligado do SQL Server (origem)
Neste passo, liga a sua instância SQL Server à fábrica de dados.

1. Crie um ficheiro JSON com o nom *SqlServerLinkedService.json* na pasta *C:\ADFv2Tutorial*, mediante o código seguinte:

    > [!IMPORTANT]
    > Selecione a secção que tem como base a autenticação que utiliza para se ligar ao SQL Server.

    **Ligar com a Autenticação SQL (sa):**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  

            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<serverName>;initial catalog=<databaseName>;user id=<userName>;password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name> ",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
   ```    

    **Utilizar a autenticação do Windows:**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  

            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<serverName>;initial catalog=<databaseName>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Selecione a secção que tem como base a autenticação que utiliza para se ligar à sua instância do SQL Server.
    > - **\<integration runtime name>** Substitua-o pelo nome do seu tempo de funcionação de integração.
    > - Antes de guardar o ficheiro, **\<servername>** substitua, , e pelos **\<databasename>** **\<username>** **\<password>** valores da sua instância SQL Server.
    > - Se precisar de utilizar um caráter de barra invertida (\\) no nome da sua conta de utilizador ou no nome do seu servidor, utilize o caráter de escape (\\) como prefixo. Por exemplo, use *mydomain \\ \\ myuser*.

1. Para encriptar os dados confidenciais (nome de utilizador, palavra-passe, etc.), execute o cmdlet `New-AzDataFactoryV2LinkedServiceEncryptedCredential`.  
    Esta encriptação garante que as credenciais são encriptadas com a interface DPAPI (Data Protection Application Programming Interface). As credenciais encriptadas são armazenadas loclamente no nó do runtime de integração autoalojado (computador local). O payload de saída pode ser redirecionado para outro ficheiro JSON (neste caso, *encryptedLinkedService.json*) que contém credenciais encriptadas.

   ```powershell
   New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

1. Execute o comando seguinte, que cria EncryptedSqlServerLinkedService:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados de entrada e saída. Representam dados de entrada e saída para a operação de cópia, que copia dados da base de dados do SQL Server para o armazenamento de Azure Blob.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Criar um conjunto de dados para a base de dados SQL de origem
Neste passo, vai definir um conjunto de dados que representa os dados na instância da base de dados do SQL Server. O conjunto de dados é do tipo SqlServerTable. Faz referência ao serviço ligado SQL Server que criou no passo anterior. O serviço ligado tem as informações de ligação que o serviço do Data Factory utiliza para se ligar à sua instância do SQL Server no runtime. Este conjunto de dados especifica a tabela SQL na base de dados que contém os dados. Neste tutorial, a tabela **emp** contém a origem de dados.

1. Crie um ficheiro JSON com o nome *SqlServerDataset.json* na pasta *C:\ADFv2Tutorial*, com o código seguinte:  
    ```json
    {  
        "name":"SqlServerDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"EncryptedSqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  

            ],
            "type":"SqlServerTable",
            "schema":[  

            ],
            "typeProperties":{  
                "schema":"dbo",
                "table":"emp"
            }
        }
    }
    ```

1. Para criar o conjunto de dados SqlServerDataset, execute o cmdlet `Set-AzDataFactoryV2Dataset`.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Segue-se o resultado do exemplo:

    ```console
    DatasetName       : SqlServerDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Criar um conjunto de dados para o armazenamento de Blobs do Azure (sink)
Neste passo, vai definir um conjunto de dados que representa os dados que vão ser copiados para o armazenamento de Blobs do Azure. O conjunto de dados é do tipo AzureBlob. Faz referência ao serviço ligado Armazenamento do Azure que criou anteriormente neste tutorial.

O serviço ligado tem as informações de ligação que a fábrica de dados utiliza no runtime para se ligar à sua conta de armazenamento do Azure. Este conjunto de dados especifica a pasta no armazenamento do Azure para o qual os dados são copiados a partir da base de dados SQL Server. Neste tutorial, a pasta é *adftutorial/fromonprem*, em que `adftutorial` é o contentor de blobs e `fromonprem` é a pasta.

1. Crie um ficheiro JSON com o nome *AzureBlobDataset.json* na pasta *C:\ADFv2Tutorial*, com o código seguinte:

    ```json
    {  
        "name":"AzureBlobDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureStorageLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  

            ],
            "type":"DelimitedText",
            "typeProperties":{  
                "location":{  
                    "type":"AzureBlobStorageLocation",
                    "folderPath":"fromonprem",
                    "container":"adftutorial"
                },
                "columnDelimiter":",",
                "escapeChar":"\\",
                "quoteChar":"\""
            },
            "schema":[  

            ]
        },
        "type":"Microsoft.DataFactory/factories/datasets"
    }
    ```

1. Para criar o conjunto de dados AzureBlobDataset, execute o cmdlet `Set-AzDataFactoryV2Dataset`.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Segue-se o resultado do exemplo:

    ```console
    DatasetName       : AzureBlobDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.DelimitedTextDataset
    ```

## <a name="create-a-pipeline"></a>Criar um pipeline
Neste tutorial, vai criar um pipeline com uma atividade de cópia. A atividade de cópia utiliza SqlServerDataset como o conjunto de dados de entrada e AzureBlobDataset como o conjunto de dados de saída. O tipo de origem está definido como *SqlSource* e o tipo de sink como *BlobSink*.

1. Crie um ficheiro JSON com o nome *SqlServerToBlobPipeline.json* na pasta *C:\ADFv2Tutorial*, com o código seguinte:

    ```json
    {  
        "name":"SqlServerToBlobPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"CopySqlServerToAzureBlobActivity",
                    "type":"Copy",
                    "dependsOn":[  

                    ],
                    "policy":{  
                        "timeout":"7.00:00:00",
                        "retry":0,
                        "retryIntervalInSeconds":30,
                        "secureOutput":false,
                        "secureInput":false
                    },
                    "userProperties":[  

                    ],
                    "typeProperties":{  
                        "source":{  
                            "type":"SqlServerSource"
                        },
                        "sink":{  
                            "type":"DelimitedTextSink",
                            "storeSettings":{  
                                "type":"AzureBlobStorageWriteSettings"
                            },
                            "formatSettings":{  
                                "type":"DelimitedTextWriteSettings",
                                "quoteAllText":true,
                                "fileExtension":".txt"
                            }
                        },
                        "enableStaging":false
                    },
                    "inputs":[  
                        {  
                            "referenceName":"SqlServerDataset",
                            "type":"DatasetReference"
                        }
                    ],
                    "outputs":[  
                        {  
                            "referenceName":"AzureBlobDataset",
                            "type":"DatasetReference"
                        }
                    ]
                }
            ],
            "annotations":[  

            ]
        }
    }
    ```

1. Para criar o pipeline SQLServerToBlobPipeline, execute o cmdlet `Set-AzDataFactoryV2Pipeline`.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Segue-se o resultado do exemplo:

    ```console
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline
Inicie uma execução de pipeline para o pipeline SQLServerToBlobPipeline e capture o ID de execução do pipeline para monitorização futura.

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Para verificar continuamente o estado de execução do pipeline SQLServerToBlobPipeline, execute o script seguinte no PowerShell e imprima o resultado final:

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Eis o resultado da execução de exemplo:

    ```console
    ResourceGroupName    : <resourceGroupName>
    DataFactoryName      : <dataFactoryName>
    ActivityRunId        : 24af7cf6-efca-4a95-931d-067c5c921c25
    ActivityName         : CopySqlServerToAzureBlobActivity
    ActivityType         : Copy
    PipelineRunId        : 7b538846-fd4e-409c-99ef-2475329f5729
    PipelineName         : SQLServerToBlobPipeline
    Input                : {source, sink, enableStaging}
    Output               : {dataRead, dataWritten, filesWritten, sourcePeakConnections...}
    LinkedServiceName    :
    ActivityRunStart     : 9/11/2019 7:10:37 AM
    ActivityRunEnd       : 9/11/2019 7:10:58 AM
    DurationInMs         : 21094
    Status               : Succeeded
    Error                : {errorCode, message, failureType, target}
    AdditionalProperties : {[retryAttempt, ], [iterationHash, ], [userProperties, {}], [recoveryStatus, None]...}
    ```

1. Pode executar o comando seguinte para obter o ID de execução do pipeline SQLServerToBlobPipeline e verificar o resultado da execução de atividade detalhado:

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Eis o resultado da execução de exemplo:

    ```json
    {  
        "dataRead":36,
        "dataWritten":32,
        "filesWritten":1,
        "sourcePeakConnections":1,
        "sinkPeakConnections":1,
        "rowsRead":2,
        "rowsCopied":2,
        "copyDuration":18,
        "throughput":0.01,
        "errors":[  

        ],
        "effectiveIntegrationRuntime":"ADFTutorialIR",
        "usedParallelCopies":1,
        "executionDetails":[  
            {  
                "source":{  
                    "type":"SqlServer"
                },
                "sink":{  
                    "type":"AzureBlobStorage",
                    "region":"CentralUS"
                },
                "status":"Succeeded",
                "start":"2019-09-11T07:10:38.2342905Z",
                "duration":18,
                "usedParallelCopies":1,
                "detailedDurations":{  
                    "queuingDuration":6,
                    "timeToFirstByte":0,
                    "transferDuration":5
                }
            }
        ]
    }
    ```

## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída com o nome *fromonprem* no contentor de blobs `adftutorial`. Confirme que consegue ver o ficheiro *dbo.emp.txt* na pasta de saída.

1. No portal do Azure, na janela do contentor **adftutorial**, selecione **Atualizar** para ver a pasta de saída.
1. Selecione `fromonprem` na lista de pastas.
1. Confirme que vê um ficheiro com o nome `dbo.emp.txt`.

    ![Ficheiro de saída](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Passos seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização no amazenamento de Bobs do Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um integration runtime autoalojado.
> * Criar serviços ligados do SQL Server e do Armazenamento do Azure.
> * Criar conjuntos de dados do SQL Server e dos Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

Para obter uma lista de lojas de dados suportadas pela Data Factory, consulte [lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Para aprender a copiar dados em massa de uma origem para um destino, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy.md)