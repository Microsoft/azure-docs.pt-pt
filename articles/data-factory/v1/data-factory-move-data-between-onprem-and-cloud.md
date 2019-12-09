---
title: Mover Gerenciamento de Dados gateway de dados
description: Configure um gateway de dados para mover dados entre o local e a nuvem. Use Gerenciamento de Dados gateway no Azure Data Factory para mover seus dados.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 039a19f38da4e651ee35fe60ba2b95a40cf890b0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931911"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Mover dados entre fontes locais e a nuvem com Gerenciamento de Dados gateway
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [copiar dados entre o local e a nuvem usando data Factory](../tutorial-hybrid-copy-powershell.md).

Este artigo fornece uma visão geral da integração de dados entre armazenamentos de dados locais e armazenamentos de dados de nuvem usando o Data Factory. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) e em outros artigos data Factory conceitos principais: [conjuntos](data-factory-create-datasets.md) e [pipelines](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Data Management Gateway
Você deve instalar Gerenciamento de Dados gateway em seu computador local para habilitar a movimentação de dados de/para um armazenamento de dados local. O gateway pode ser instalado no mesmo computador que o armazenamento de dados ou em um computador diferente, contanto que o gateway possa se conectar ao armazenamento de dados.

> [!IMPORTANT]
> Consulte o artigo [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) para obter detalhes sobre o gateway de gerenciamento de dados. 

A instrução a seguir mostra como criar um data factory com um pipeline que move dados de um banco de **SQL Server** local para um armazenamento de BLOBs do Azure. Como parte das instruções, vai instalar e configurar o Data Management Gateway no seu computador.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Walkthrough: copiar dados locais para a nuvem
Neste passo a passos, você executa as seguintes etapas: 

1. Criar uma fábrica de dados.
2. Crie um gateway de gerenciamento de dados. 
3. Crie serviços vinculados para armazenamentos de dados de origem e de coletor.
4. Crie DataSets para representar dados de entrada e saída.
5. Criar um pipeline com uma atividade de cópia para mover os dados.

## <a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial
Antes de iniciar este passo a passos, você deve ter os seguintes pré-requisitos:

* **Subscrição do Azure**.  Se não tiver uma subscrição, pode criar uma conta gratuita em apenas alguns minutos. Consulte o artigo [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) para obter detalhes.
* **Conta de armazenamento do Azure**. Você usa o armazenamento de BLOBs como um armazenamento de dados de **destino/coletor** neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) para obter os passos para criar uma.
* **SQL Server**. Neste tutorial, vai utilizar uma base de dados do SQL Server no local como um arquivo de dados de **origem**. 

## <a name="create-data-factory"></a>Criar fábrica de dados
Nesta etapa, você usa o portal do Azure para criar uma instância de Azure Data Factory chamada **ADFTutorialOnPremDF**.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **criar um recurso**, clique em **inteligência + análise**e clique em **Data Factory**.

   ![Novo -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Na página **novo data Factory** , digite **ADFTutorialOnPremDF** para o nome.

    ![Adicionar ao quadro inicial](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > O nome do Azure Data Factory deve ser globalmente exclusivo. Se você receber o erro: o **nome do data Factory "ADFTutorialOnPremDF" não está disponível**, altere o nome do data Factory (por exemplo, yournameADFTutorialOnPremDF) e tente criar novamente. Use esse nome no lugar de ADFTutorialOnPremDF ao executar as etapas restantes neste tutorial.
   >
   > O nome do data factory pode ser registrado como um nome **DNS** no futuro e, portanto, se torna publicamente visível.
   >
   >
4. Selecione a **Subscrição do Azure** onde pretende que seja criada a fábrica de dados.
5. Selecione o **grupo de recursos** existente ou crie um grupo de recursos. Para o tutorial, crie um grupo de recursos chamado: **ADFTutorialResourceGroup**.
6. Clique em **criar** na página **novo data Factory** .

   > [!IMPORTANT]
   > Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ao nível do grupo de recursos/subscrição.
   >
   >
7. Após a conclusão da criação, você verá a página **Data Factory** , conforme mostrado na imagem a seguir:

   ![Página inicial do Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Criar gateway
1. Na página **Data Factory** , clique no bloco **criar e implantar** para iniciar o **Editor** para o data Factory.

    ![Mosaico Criar e Implementar](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. No editor de Data Factory, clique em **... Mais** na barra de ferramentas e clique em **novo gateway de dados**. Como alternativa, você pode clicar com o botão direito do mouse em **gateways de dados** no modo de exibição de árvore e clicar em **novo gateway de dados**.

   ![Novo gateway de dados na barra de ferramentas](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Na página **criar** , digite **adftutorialgateway** para o **nome**e clique em **OK**.     

    ![Página Criar gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > Neste tutorial, você cria o gateway lógico com apenas um nó (computador Windows local). Você pode escalar horizontalmente um gateway de gerenciamento de dados associando vários computadores locais ao gateway. Você pode escalar verticalmente aumentando o número de trabalhos de movimentação de dados que podem ser executados simultaneamente em um nó. Esse recurso também está disponível para um gateway lógico com um único nó. Consulte [dimensionar o gateway de gerenciamento de dados no artigo Azure data Factory](data-factory-data-management-gateway-high-availability-scalability.md) para obter detalhes.  
4. Na página **Configurar** , clique em **instalar diretamente neste computador**. Essa ação baixa o pacote de instalação para o gateway, instala, configura e registra o gateway no computador.  

   > [!NOTE]
   > Use o Internet Explorer ou um navegador da Web compatível com o Microsoft ClickOnce.
   >
   > Se estiver a utilizar o Chrome, aceda à [Web Store do Chrome](https://chrome.google.com/webstore/), pesquise com a palavra-chave "ClickOnce", escolha uma das extensões de ClickOnce e instale-a.
   >
   > Faça o mesmo para o Firefox (instalar suplemento). Clique no botão **abrir menu** na barra de ferramentas (**três linhas horizontais** no canto superior direito), clique em **Complementos**, pesquise com a palavra-chave "ClickOnce", escolha uma das extensões do ClickOnce e instale-a.    
   >
   >

    ![Página de configuração de gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Dessa forma, é a maneira mais fácil (com um clique) baixar, instalar, configurar e registrar o gateway em uma única etapa. Você pode ver o aplicativo **Microsoft gerenciamento de dados Gateway Configuration Manager** está instalado no seu computador. Você também pode encontrar o executável **configmanager. exe** na pasta: **C:\Program Files\Microsoft gerenciamento de dados Gateway\2.0\Shared**.

    Você também pode baixar e instalar o gateway manualmente usando os links nesta página e registrá-lo usando a chave mostrada na caixa de texto **nova chave** .

    Consulte o artigo [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) para obter todos os detalhes sobre o gateway.

   > [!NOTE]
   > Você deve ser um administrador no computador local para instalar e configurar o gateway de Gerenciamento de Dados com êxito. Você pode adicionar outros usuários ao grupo local do Windows **usuários do gateway de gerenciamento de dados** . Os membros desse grupo podem usar a ferramenta de Configuration Manager de Gerenciamento de Dados gateway para configurar o gateway.
   >
   >
5. Aguarde alguns minutos ou aguarde até que você veja a seguinte mensagem de notificação:

    ![Instalação do gateway bem-sucedida](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Inicie o **Gerenciamento de dados Gateway Configuration Manager** aplicativo no seu computador. Na janela de **pesquisa** , digite **Gerenciamento de dados gateway** para acessar esse utilitário. Você também pode encontrar o executável **configmanager. exe** na pasta: **c:\arquivos de Programas\Microsoft gerenciamento de dados Gateway\2.0\Shared**

    ![Configuration Manager de gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Confirme que você vê `adftutorialgateway is connected to the cloud service` mensagem. A barra de status a parte inferior é exibida **conectada ao serviço de nuvem** junto com uma **marca de seleção verde**.

    Na guia **início** , você também pode realizar as seguintes operações:

   * **Registre** um gateway com uma chave do portal do Azure usando o botão registrar.
   * **Pare** o serviço de host gerenciamento de dados gateway em execução no computador do gateway.
   * **Agendar atualizações** a serem instaladas em uma hora específica do dia.
   * Exibir quando o gateway foi **atualizado pela última vez**.
   * Especifique a hora em que uma atualização para o gateway pode ser instalada.
8. Alterne para a guia **configurações** . O certificado especificado na seção **certificado** é usado para criptografar/descriptografar credenciais para o armazenamento de dados local que você especificar no Portal. adicional Clique em **alterar** para usar seu próprio certificado. Por padrão, o gateway usa o certificado que é gerado automaticamente pelo serviço de Data Factory.

    ![Configuração do certificado do gateway](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Você também pode executar as seguintes ações na guia **configurações** :

   * Exiba ou exporte o certificado que está sendo usado pelo Gateway.
   * Altere o ponto de extremidade HTTPS usado pelo Gateway.    
   * Defina um proxy HTTP a ser usado pelo Gateway.     
9. adicional Alterne para a guia **diagnóstico** , marque a opção **habilitar log detalhado** se desejar habilitar o log detalhado que você pode usar para solucionar quaisquer problemas com o gateway. As informações de log podem ser encontradas em **Visualizador de eventos** em **logs de aplicativos e serviços** -> nó de **Gateway gerenciamento de dados** .

    ![Separador Diagnóstico](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Você também pode executar as seguintes ações na guia **diagnóstico** :

   * Use a seção **testar conexão** para uma fonte de dados local usando o gateway.
   * Clique em **Exibir logs** para ver o log do gerenciamento de dados gateway em uma janela Visualizador de eventos.
   * Clique em **enviar logs** para carregar um arquivo zip com logs dos últimos sete dias para a Microsoft para facilitar a solução de problemas.
10. Na guia **diagnóstico** , na seção **testar conexão** , selecione **SqlServer** para o tipo de armazenamento de dados, insira o nome do servidor de banco de dados, o nome do banco de dado, especifique o tipo de autenticação, insira o nome de usuário e a senha e clique em **testar** para testar se o gateway pode se conectar ao banco de dados.
11. Alterne para o navegador da Web e, na **portal do Azure**, clique em **OK** na página **Configurar** e, em seguida, na página **novo gateway de dados** .
12. Você deve ver **adftutorialgateway** em **gateways de dados** no modo de exibição de árvore à esquerda.  Se você clicar nele, deverá ver o JSON associado.

## <a name="create-linked-services"></a>Criar serviços ligados
Nesta etapa, você cria dois serviços vinculados: **AzureStorageLinkedService** e **SqlServerLinkedService**. O **SqlServerLinkedService** vincula um banco de dados local SQL Server e o serviço vinculado **AzureStorageLinkedService** vincula um repositório de blob do Azure ao data Factory. Você criará um pipeline mais adiante neste guia de explicação que copia dados do banco de SQL Server local para o repositório de blob do Azure.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Adicionar um serviço vinculado a um banco de dados SQL Server local
1. No **Editor de data Factory**, clique em **novo armazenamento de dados** na barra de ferramentas e selecione **SQL Server**.

   ![Novo serviço vinculado SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. No **Editor de JSON** à direita, execute as seguintes etapas:

   1. Para o **GATEWAYNAME**, especifique **adftutorialgateway**.    
   2. Em **ConnectionString**, execute as seguintes etapas:    

      1. Para **ServerName**, insira o nome do servidor que hospeda o banco de dados SQL Server.
      2. Para **DatabaseName**, insira o nome do banco de dados.
      3. Clique no botão **criptografar** na barra de ferramentas. Você verá o aplicativo Gerenciador de credenciais.

         ![Aplicativo Gerenciador de credenciais](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. Na caixa de diálogo **configurações de credenciais** , especifique o tipo de autenticação, o nome de usuário e a senha e clique em **OK**. Se a conexão for bem-sucedida, as credenciais criptografadas serão armazenadas no JSON e a caixa de diálogo será fechada.
      5. Feche a guia vazia do navegador que iniciou a caixa de diálogo se ela não for fechada automaticamente e volte para a guia com a portal do Azure.

         No computador do gateway, essas credenciais são **criptografadas** usando um certificado que o serviço data Factory possui. Se você quiser usar o certificado associado ao gateway de Gerenciamento de Dados em vez disso, consulte definir credenciais com segurança.    
   3. Clique em **implantar** na barra de comandos para implantar o serviço vinculado SQL Server. Você deve ver o serviço vinculado no modo de exibição de árvore.

      ![SQL Server serviço vinculado no modo de exibição de árvore](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Adicionar um serviço vinculado para uma conta de armazenamento do Azure
1. No **Editor de data Factory**, clique em **novo armazenamento de dados** na barra de comandos e clique em **armazenamento do Azure**.
2. Insira o nome da sua conta de armazenamento do Azure para o **nome da conta**.
3. Insira a chave para sua conta de armazenamento do Azure para a **chave de conta**.
4. Clique em **implantar** para implantar o **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar os conjuntos de dados de entrada e de saída que representam os dados de entrada e de saída da operação de cópia (base de dados do SQL Server no local => armazenamento de blobs do Azure). Antes de criar os conjuntos de dados, execute os seguintes passos (os passos detalhados são apresentados a seguir à lista):

* Crie uma tabela com o nome **emp** na Base de Dados do SQL Server que adicionou como um serviço ligado à fábrica de dados e insira algumas entradas de exemplo na tabela.
* Crie um contentor de blobs com o nome **adftutorial** na conta de armazenamento de blobs do Azure que adicionou como um serviço ligado à fábrica de dados.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Preparar SQL Server locais para o tutorial
1. Na base de dados que especificou para o serviço ligado do SQL Server no local (**SqlServerLinkedService**), utilize o seguinte script SQL para criar a tabela **emp** na base de dados.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Insira alguns exemplos na tabela:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada

1. No **Editor de data Factory**, clique em **... Mais**, clique em **novo conjunto** de informações na barra de comandos e clique em **SQL Server tabela**.
2. Substitua o JSON no painel direito pelo seguinte texto:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```     
   Tenha em atenção os seguintes pontos:

   * o **tipo** é definido como **sqlservertable**.
   * **TableName** é definido como **EMP**.
   * **linkedServiceName** é definido como **SqlServerLinkedService** (você criou esse serviço vinculado anteriormente neste passo a passo.).
   * Para um conjunto de dados de entrada que não é gerado por outro pipeline no Azure Data Factory, você deve definir **external** como **true**. Ele denota que os dados de entrada são produzidos externos ao serviço de Azure Data Factory. Opcionalmente, você pode especificar qualquer política de dados externa usando o elemento **externalData** na seção de **política** .    

   Consulte [mover dados para/de SQL Server](data-factory-sqlserver-connector.md) para obter detalhes sobre as propriedades JSON.
3. Clique em **implantar** na barra de comandos para implantar o conjunto de um.  

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída

1. No **Editor de data Factory**, clique em **novo conjunto de novos** na barra de comandos e clique em armazenamento de **BLOBs do Azure**.
2. Substitua o JSON no painel direito pelo seguinte texto:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Tenha em atenção os seguintes pontos:

   * **Type** é definido como **AzureBlob**.
   * **linkedServiceName** é definido como **AzureStorageLinkedService** (você criou esse serviço vinculado na etapa 2).
   * **FolderPath** é definido como **adftutorial/outfromonpremdf** , em que outfromonpremdf é a pasta no contêiner adftutorial. Crie o contêiner **adftutorial** se ele ainda não existir.
   * A **disponibilidade** está definida como **de hora a hora** (**frequência** definida como **hora** e **intervalo** definido como **1**).  O serviço de Data Factory gera uma fatia de dados de saída a cada hora na tabela **EMP** no banco de dado SQL do Azure.

   Se você não especificar um **nome de arquivo** para uma **tabela de saída**, os arquivos gerados no **FolderPath** serão nomeados no seguinte formato: `Data.<Guid>.txt` (por exemplo: Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt).

   Para definir **FolderPath** e **filename** dinamicamente com base no tempo de **SliceStart** , use a propriedade partitionedBy. No exemplo seguinte, o folderPath utiliza o Ano, o Mês e o Dia do SliceStart (hora de início do setor a ser processado) e o fileName utiliza a Hora do SliceStart. Por exemplo, se está a ser produzido um setor para 2014-10-20T08:00:00, folderName está definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName está definido como 08.csv.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    Consulte [mover dados de/para o armazenamento de BLOBs do Azure](data-factory-azure-blob-connector.md) para obter detalhes sobre as propriedades JSON.
3. Clique em **implantar** na barra de comandos para implantar o conjunto de um. Confirme que você vê os dois conjuntos de valores no modo de exibição de árvore.  

## <a name="create-pipeline"></a>Criar pipeline
Nesta etapa, você cria um **pipeline** com uma **atividade de cópia** que usa **EmpOnPremSQLTable** como entrada e **OutputBlobTable** como saída.

1. No editor de Data Factory, clique em **... Mais**e clique em **novo pipeline**.
2. Substitua o JSON no painel direito pelo seguinte texto:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > Substitua o valor da propriedade **start** com o dia atual e o valor **end** com o dia seguinte.
   >
   >

   Tenha em atenção os seguintes pontos:

   * Na seção atividades, há apenas a atividade cujo **tipo** está definido como **copiar**.
   * A **entrada** para a atividade é definida como **EmpOnPremSQLTable** e a **saída** para a atividade é definida como **OutputBlobTable**.
   * Na seção **typeproperties** , o **sqlsource** é especificado como o **tipo de origem** e **BlobSink** é especificado como o **tipo de coletor**.
   * A `select * from emp` de consulta SQL é especificada para a propriedade **sqlReaderQuery** de **sqlsource**.

   Tanto o datetime de início como de fim têm de estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O tempo **end** é opcional, mas iremos utilizá-lo neste tutorial.

   Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9/9/9999** como o valor da propriedade **end**.

   Você está definindo a duração da hora em que as fatias de dados são processadas com base nas propriedades de **disponibilidade** que foram definidas para cada conjunto de Azure data Factory.

   Existem 24 setores de dados no exemplo, uma vez que estes são produzidos de hora a hora.        
3. Clique em **implantar** na barra de comandos para implantar o conjunto de os (tabela é um conjunto de um DataSet retangular). Confirme se o pipeline aparece no modo de exibição de árvore no nó **pipelines** .  
4. Agora, clique em **X** duas vezes para fechar a página e voltar para a página de **Data Factory** para o **ADFTutorialOnPremDF**.

**Parabéns!** Você criou com êxito um data factory do Azure, serviços vinculados, conjuntos de valores e um pipeline e agendou o pipeline.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Ver a fábrica de dados numa Vista de Diagrama
1. No **portal do Azure**, clique no bloco **diagrama** na home page para a data Factory **ADFTutorialOnPremDF** . :

    ![Link de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Deverá ver um diagrama semelhante à seguinte imagem:

    ![Vista de Diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Você pode ampliar, reduzir, aplicar zoom a 100%, ajustar o zoom, posicionar pipelines e conjuntos de dados automaticamente e mostrar informações de linhagem (realça itens upstream e downstream dos itens selecionados).  Você pode clicar duas vezes em um objeto (conjunto de dados de entrada/saída ou Pipeline) para ver as propriedades dele.

## <a name="monitor-pipeline"></a>Monitorizar o pipeline
Neste passo, irá utilizar o portal do Azure para monitorizar os acontecimentos de uma fábrica de dados do Azure. Também pode utilizar os cmdlets PowerShell para monitorizar os conjuntos de dados e pipelines. Para obter detalhes sobre o monitoramento, consulte [monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md).

1. No diagrama, clique duas vezes em **EmpOnPremSQLTable**.  

    ![Fatias EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Observe que todas as fatias de dados estão no estado **pronto** porque a duração do pipeline (hora de início até a hora de término) está no passado. Também é porque você inseriu os dados no banco de dado SQL Server e está lá o tempo todo. Confirme se nenhuma fatia aparece na seção **fatias com problema** na parte inferior. Para exibir todas as fatias, clique em **Ver mais** na parte inferior da lista de fatias.
3. Agora, na página **conjuntos** de itens, clique em **OutputBlobTable**.

    ![Fatias OputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Clique em qualquer fatia de dados da lista e você deverá ver a página **fatia de dados** . Você vê as execuções de atividade para a fatia. Você vê apenas uma execução de atividade normalmente.  

    ![Folha fatia de dados](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Se o setor não estiver no estado **Pronto**, pode ver os setores a montante que não estão no estado Pronto e estão a impedir a execução do setor atual na lista **Setores a montante que não estão prontos**.
5. Clique na **atividade executar** da lista na parte inferior para ver os **detalhes da execução da atividade**.

   ![Página detalhes da execução da atividade](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Você veria informações como taxa de transferência, duração e o gateway usado para transferir os dados.
6. Clique no **X** para fechar todas as páginas até que você
7. Volte para a home page para o **ADFTutorialOnPremDF**.
8. adicional Clique em **pipelines**, clique em **ADFTutorialOnPremDF**e faça drill-through de tabelas de entrada (**consumidas**) ou conjuntos de dados de saída (**produzidos**).
9. Use ferramentas como [o Microsoft Gerenciador de armazenamento](https://storageexplorer.com/) para verificar se um blob/arquivo é criado para cada hora.

   ![Explorador do Armazenamento do Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Passos seguintes
* Consulte o artigo [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) para obter todos os detalhes sobre o gateway de gerenciamento de dados.
* Consulte [copiar dados do blob do Azure para o SQL do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para saber mais sobre como usar a atividade de cópia para mover dados de um armazenamento de dados de origem para um armazenamento de dados de coletor.
