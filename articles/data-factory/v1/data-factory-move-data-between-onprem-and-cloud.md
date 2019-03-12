---
title: Mover dados - Data Management Gateway | Documentos da Microsoft
description: Configure um gateway de dados para mover dados entre locais e na cloud. Utilize o Data Management Gateway no Azure Data Factory para mover seus dados.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 94c464733d731505cde0327ce9a295d3b4ab68de
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538702"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Mover dados entre origens no local e a nuvem com o Data Management Gateway
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [copiar dados entre locais e na cloud com o Data Factory](../tutorial-hybrid-copy-powershell.md).

Este artigo fornece uma descrição geral da integração de dados entre arquivos de dados no local e os arquivos de dados na cloud com o Data Factory. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo e outros artigos de conceitos de núcleo de fábrica de dados: [conjuntos de dados](data-factory-create-datasets.md) e [pipelines](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Data Management Gateway
Tem de instalar o Data Management Gateway no seu computador no local para ativar a mover dados de/para um arquivo de dados no local. O gateway pode ser instalado no mesmo computador que o arquivo de dados ou num computador diferente, desde que o gateway consiga estabelecer ligação ao arquivo de dados.

> [!IMPORTANT]
> Ver [Data Management Gateway](data-factory-data-management-gateway.md) artigo para obter detalhes sobre o Data Management Gateway. 

A instrução a seguir mostra como criar uma fábrica de dados com um pipeline que move os dados no local **SQL Server** base de dados para um armazenamento de Blobs do Azure. Como parte das instruções, vai instalar e configurar o Data Management Gateway no seu computador.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Passo a passo: copiar dados no local para a cloud
Nestas instruções, siga os passos abaixo: 

1. Criar uma fábrica de dados.
2. Crie um gateway de gestão de dados. 
3. Crie serviços ligados para arquivos de dados de origem e sink.
4. Crie conjuntos de dados para representar os dados de entrada e saídos.
5. Criar um pipeline com uma atividade de cópia para mover os dados.

## <a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial
Antes de iniciar estas instruções, tem de ter os seguintes pré-requisitos:

* **Subscrição do Azure**.  Se não tiver uma subscrição, pode criar uma conta gratuita em apenas alguns minutos. Consulte a [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) artigo para obter detalhes.
* **Conta de armazenamento do Azure**. Utilize o armazenamento de BLOBs como um **destino/sink** do arquivo de dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) para obter os passos para criar uma.
* **SQL Server**. Neste tutorial, vai utilizar uma base de dados do SQL Server no local como um arquivo de dados de **origem**. 

## <a name="create-data-factory"></a>Criar fábrica de dados
Neste passo, vai utilizar o portal do Azure para criar uma instância do Azure Data Factory com o nome **ADFTutorialOnPremDF**.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **criar um recurso**, clique em **inteligência + análise**e clique em **Data Factory**.

   ![Novo -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Na **nova fábrica de dados** página, introduza **ADFTutorialOnPremDF** para o nome.

    ![Adicionar ao Startboard](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: **Nome da fábrica de dados "ADFTutorialOnPremDF" não está disponível**, altere o nome da fábrica de dados (por exemplo, yournameADFTutorialOnPremDF) e tente criar novamente. Utilize este nome em vez de ADFTutorialOnPremDF ao realizar os passos restantes neste tutorial.
   >
   > O nome da fábrica de dados pode ser registado como um **DNS** nome no futuro e, por conseguinte, ficar publicamente visível.
   >
   >
4. Selecione a **Subscrição do Azure** onde pretende que seja criada a fábrica de dados.
5. Selecione o **grupo de recursos** existente ou crie um grupo de recursos. Para este tutorial, crie um grupo de recursos com o nome: **ADFTutorialResourceGroup**.
6. Clique em **Create** sobre o **nova fábrica de dados** página.

   > [!IMPORTANT]
   > Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ao nível do grupo de recursos/subscrição.
   >
   >
7. Depois de concluída a criação, consulte a **fábrica de dados** página conforme mostrado na imagem seguinte:

   ![Página inicial da fábrica de dados](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Criar gateway
1. Na **Data Factory** página, clique em **autor e implementar** mosaico para iniciar o **Editor** da fábrica de dados.

    ![Mosaico Criar e Implementar](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. No Editor do Data Factory, clique em **... Obter mais** na barra de ferramentas e clique em **novo gateway de dados**. Em alternativa, pode clicar **Gateways de dados** na vista de árvore e clique em **novo gateway de dados**.

   ![Novo gateway de dados na barra de ferramentas](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Na **Create** página, introduza **adftutorialgateway** para o **nome**e clique em **OK**.     

    ![Criar a página de Gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > Nestas instruções, vai criar o gateway lógico com apenas um nó (máquina do Windows no local). Pode aumentar horizontalmente um gateway de gestão de dados através da associação de várias máquinas no local com o gateway. Pode dimensionar até ao aumentar o número de tarefas de movimento de dados que podem ser executadas simultaneamente num nó. Esse recurso também está disponível para um gateway lógico com um único nó. Ver [gateway de gestão de dados de dimensionamento no Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) artigo para obter detalhes.  
4. Na **configurar** página, clique em **instalar diretamente neste computador**. Esta ação transfere o pacote de instalação para o gateway, instala, configura e registra o gateway no computador.  

   > [!NOTE]
   > Utilize o Internet Explorer ou um navegador da web compatível com ClickOnce da Microsoft.
   >
   > Se estiver a utilizar o Chrome, aceda à [Web Store do Chrome](https://chrome.google.com/webstore/), pesquise com a palavra-chave "ClickOnce", escolha uma das extensões de ClickOnce e instale-a.
   >
   > Fazer o mesmo no Firefox (instalação suplemento). Clique em **Abrir Menu** na barra de ferramentas (**três linhas horizontais** no canto superior direito), clique em **suplementos**, pesquise com uma palavra-chave "ClickOnce", escolha uma do Extensões de ClickOnce e instale-o.    
   >
   >

    ![Gateway - Configurar página](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Dessa maneira é a maneira mais fácil (um clique) para transferir, instalar, configurar e registar o gateway numa única etapa. Pode ver o **o Gestor de configuração do Microsoft Data Management Gateway** aplicação está instalada no seu computador. Também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    Também pode transferir e instalar o gateway manualmente ao utilizar as ligações nesta página e registá-lo com a chave mostrada os **nova chave** caixa de texto.

    Ver [Data Management Gateway](data-factory-data-management-gateway.md) artigo para todos os detalhes sobre o gateway.

   > [!NOTE]
   > Tem de ser um administrador no computador local para instalar e configurar o Data Management Gateway com êxito. Pode adicionar utilizadores adicionais para o **utilizadores de Gateway de gestão de dados** grupo local do Windows. Os membros deste grupo podem utilizar a ferramenta de Gestor de configuração do Data Management Gateway para configurar o gateway.
   >
   >
5. Aguarde alguns minutos ou aguarde até ver a seguinte mensagem de notificação:

    ![Instalação do gateway com êxito](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Inicie **Gestor de configuração do Data Management Gateway** aplicação no seu computador. Na **pesquisa** janela, escreva **Data Management Gateway** para acessar esse utilitário. Também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

    ![Gestor de configuração do gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Confirme que vê `adftutorialgateway is connected to the cloud service` mensagem. O estado mostra a parte inferior da barra **ligado ao serviço cloud** juntamente com um **marca de verificação verde**.

    Sobre o **home page** separador, também pode fazer as seguintes operações:

   * **Registar** um gateway com uma chave a partir do portal do Azure através do botão Registre-se.
   * **Parar** o serviço anfitrião do Data Management Gateway em execução no seu computador de gateway.
   * **Agendar atualizações** para ser instalado num momento específico do dia.
   * Ver quando o gateway foi **última atualização**.
   * Especifique a hora em que uma atualização para o gateway pode ser instalada.
8. Mudar para o separador **Definições**. O certificado especificado no **certificado** seção é usada para as credenciais para o arquivo de dados no local que especificou no portal de encriptação/desencriptação. (opcional) Clique em **alteração** para utilizar o seu próprio certificado em vez disso. Por predefinição, o gateway utiliza o certificado que é gerado automaticamente pelo serviço Data Factory.

    ![Configuração de certificado do gateway](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Também pode fazer as seguintes ações no **definições** separador:

   * Ver ou exportar o certificado que está a ser utilizado pelo gateway.
   * Altere o ponto de final HTTPS utilizado pelo gateway.    
   * Defina um proxy de HTTP a ser utilizado pelo gateway.     
9. (opcional) Mudar para o **diagnóstico** separador, verifique o **ativar o registo verboso** opção se pretender ativar o registo verboso que pode utilizar para resolver quaisquer problemas com o gateway. As informações de registo podem ser encontradas na **Visualizador de eventos** sob **Applications and Services Logs** -> **Data Management Gateway** nó.

    ![Separador Diagnóstico](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Também pode efetuar as seguintes ações na **diagnóstico** separador:

   * Uso **Testar ligação** secção para uma origem de dados no local com o gateway.
   * Clique em **ver registos** para ver o registo do Gateway de gestão de dados numa janela de Visualizador de eventos.
   * Clique em **enviar registos** para carregar um ficheiro zip com os registos dos últimos sete dias para a Microsoft para facilitar a resolução de problemas dos seus problemas.
10. Sobre o **diagnóstico** separador a **Testar ligação** secção, selecione **SqlServer** para o tipo de arquivo de dados, introduza o nome do servidor de base de dados, o nome da base de dados, Especifique o tipo de autenticação, introduza o nome de utilizador e palavra-passe e clique em **testar** para testar se o gateway consiga estabelecer ligação à base de dados.
11. Mude para o navegador da web e, no **portal do Azure**, clique em **OK** no **configurar** página e, em seguida, no **novo gateway de dados** página.
12. Verá **adftutorialgateway** sob **Gateways de dados** na vista de árvore à esquerda.  Se clicar nele, deverá ver o JSON associado.

## <a name="create-linked-services"></a>Criar serviços ligados
Neste passo, vai criar dois serviços ligados: **AzureStorageLinkedService** e **SqlServerLinkedService**. O **SqlServerLinkedService** liga uma base de dados do SQL Server no local e o **AzureStorageLinkedService** os serviços ligados ligam um armazenamento de Blobs do Azure à fábrica de dados. Irá criar um pipeline mais tarde nestas instruções, que copia dados da base de dados do SQL Server no local para o armazenamento de Blobs do Azure.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Adicionar um serviço ligado a uma base de dados do SQL Server no local
1. Na **Editor do Data Factory**, clique em **novo arquivo de dados** na barra de ferramentas e selecione **do SQL Server**.

   ![Novo serviço ligado do SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. Na **editor de JSON** à direita, siga os passos abaixo:

   1. Para o **gatewayName**, especifique **adftutorialgateway**.    
   2. Na **connectionString**, siga os passos abaixo:    

      1. Para **servername**, introduza o nome do servidor que aloja a base de dados do SQL Server.
      2. Para **databasename**, introduza o nome da base de dados.
      3. Clique em **Encrypt** botão na barra de ferramentas. Verá a aplicação do Gestor de credenciais.

         ![Aplicação do Gestor de credenciais](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. Na **definição de credenciais** caixa de diálogo, especifique o tipo de autenticação, nome de utilizador e palavra-passe e clique em **OK**. Se a ligação for bem-sucedida, as credenciais encriptadas são armazenadas no JSON e fecha a caixa de diálogo.
      5. Feche o separador do browser vazia que iniciou a caixa de diálogo se não estiver fechado automaticamente e voltar para o separador com o portal do Azure.

         No computador gateway, estas credenciais são **encriptados** utilizando um certificado que o serviço Data Factory é proprietário. Se pretender utilizar o certificado que estão associado com o Data Management Gateway em vez disso, consulte Definir credenciais de forma segura.    
   3. Clique em **Deploy** na barra de comandos para implementar o SQL Server de serviço ligado. Deverá ver o serviço ligado na vista de árvore.

      ![Serviço ligado do SQL Server na vista de árvore](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Adicionar um serviço ligado para uma conta de armazenamento do Azure
1. Na **Editor do Data Factory**, clique em **novo arquivo de dados** barra de comandos e clique em **armazenamento do Azure**.
2. Introduza o nome da sua conta de armazenamento do Azure para o **nome da conta**.
3. Introduza a chave para a sua conta de armazenamento do Azure para o **chave de conta**.
4. Clique em **Deploy** para implementar a **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar os conjuntos de dados de entrada e de saída que representam os dados de entrada e de saída da operação de cópia (base de dados do SQL Server no local => armazenamento de blobs do Azure). Antes de criar os conjuntos de dados, execute os seguintes passos (os passos detalhados são apresentados a seguir à lista):

* Crie uma tabela com o nome **emp** na Base de Dados do SQL Server que adicionou como um serviço ligado à fábrica de dados e insira algumas entradas de exemplo na tabela.
* Crie um contentor de blobs com o nome **adftutorial** na conta de armazenamento de blobs do Azure que adicionou como um serviço ligado à fábrica de dados.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Preparar o SQL Server no local para o tutorial
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

1. No **Data Factory Editor**, clique em **... Obter mais**, clique em **novo conjunto de dados** na barra de comandos e clique em **tabela do SQL Server**.
2. Substitua o JSON no painel da direita com o seguinte texto:

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

   * **tipo** está definido como **SqlServerTable**.
   * **tableName** está definido como **emp**.
   * **linkedServiceName** está definido como **SqlServerLinkedService** (criou este serviço ligado anteriormente neste passo a passo.).
   * Para um conjunto de dados entrado que não forem gerado pelo outro pipeline no Azure Data Factory, tem de definir **externo** ao **verdadeiro**. Ele denota que os dados de entrada são produzidos externos ao serviço Azure Data Factory. Opcionalmente, pode especificar todas as políticas de dados externos com o **externalData** elemento no **política** secção.    

   Ver [mover dados de/para o SQL Server](data-factory-sqlserver-connector.md) para obter detalhes sobre as propriedades JSON.
3. Clique em **Deploy** na barra de comandos para implementar o conjunto de dados.  

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída

1. Na **Editor do Data Factory**, clique em **novo conjunto de dados** na barra de comandos e clique em **armazenamento de Blobs do Azure**.
2. Substitua o JSON no painel da direita com o seguinte texto:

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

   * **tipo** está definido como **AzureBlob**.
   * **linkedServiceName** está definido como **AzureStorageLinkedService** (criou este serviço ligado no passo 2).
   * **folderPath** está definido como **adftutorial/outfromonpremdf** onde outfromonpremdf é a pasta no contentor adftutorial. Criar a **adftutorial** contentor se ainda não exista.
   * A **disponibilidade** está definida como **de hora a hora** (**frequência** definida como **hora** e **intervalo** definido como **1**).  O serviço Data Factory gera um setor de dados de saída de cada hora na **emp** tabela na base de dados SQL do Azure.

   Se não especificar um **fileName** para um **tabela de saída**, os ficheiros gerados no **folderPath** são denominados no seguinte formato: Dados. <Guid>. txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Para definir **folderPath** e **fileName** dinamicamente com base no **SliceStart** tempo, utilize a propriedade partitionedBy. No exemplo seguinte, o folderPath utiliza o Ano, o Mês e o Dia do SliceStart (hora de início do setor a ser processado) e o fileName utiliza a Hora do SliceStart. Por exemplo, se está a ser produzido um setor para 2014-10-20T08:00:00, folderName está definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName está definido como 08.csv.

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

    Ver [mover dados de/para o armazenamento de Blobs do Azure](data-factory-azure-blob-connector.md) para obter detalhes sobre as propriedades JSON.
3. Clique em **Deploy** na barra de comandos para implementar o conjunto de dados. Confirme que vê ambos os conjuntos de dados na vista de árvore.  

## <a name="create-pipeline"></a>Criar pipeline
Neste passo, vai criar um **pipeline** com um **atividade de cópia** que utiliza **EmpOnPremSQLTable** como entrada e **OutputBlobTable** como saída.

1. No Editor do Data Factory, clique em **... Mais** e clique em **Novo pipeline**.
2. Substitua o JSON no painel da direita com o seguinte texto:    

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

   * Na secção Atividades, existe apenas atividade cuja **tipo** está definida como **cópia**.
   * **Entrada** a atividade está definida como **EmpOnPremSQLTable** e **saída** a atividade está definida como **OutputBlobTable**.
   * Na **typeProperties** secção, **SqlSource** está especificado como o **tipo de origem** e * * BlobSink * * é especificado como o **tipo de sink**.
   * Consulta SQL `select * from emp` é especificado para o **sqlReaderQuery** propriedade **SqlSource**.

   Tanto o datetime de início como de fim têm de estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O tempo **end** é opcional, mas iremos utilizá-lo neste tutorial.

   Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9/9/9999** como o valor da propriedade **end**.

   Está a definir a duração de tempo em que os setores de dados são processados com base na **disponibilidade** propriedades que foram definidas para cada conjunto de dados do Azure Data Factory.

   Existem 24 setores de dados no exemplo, uma vez que estes são produzidos de hora a hora.        
3. Clique em **Deploy** na barra de comandos para implementar o conjunto de dados (tabela é um conjunto de dados retangular). Confirme que o pipeline que aparece na vista de árvore em **Pipelines** nó.  
4. Agora, clique em **X** duas vezes para fechar a página para voltar para o **Data Factory** página para o **ADFTutorialOnPremDF**.

**Parabéns!** Criar a uma fábrica de dados do Azure, serviços ligados, conjuntos de dados e um pipeline com êxito e agendou o pipeline.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Ver a fábrica de dados numa Vista de Diagrama
1. Na **portal do Azure**, clique em **diagrama** mosaico na home page para o **ADFTutorialOnPremDF** fábrica de dados. :

    ![Ligação de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Deverá ver um diagrama semelhante à seguinte imagem:

    ![Vista de Diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Pode ampliar, reduzir, ampliar para 100%, zoom ajustar automaticamente posicionar os pipelines e conjuntos de dados e mostrar informações de linhagem (realça os itens a montante e a jusante dos itens selecionados).  Pode fazer duplo clique de um objeto (conjunto de dados de entrada/saída ou pipeline) para ver as propriedades do mesmo.

## <a name="monitor-pipeline"></a>Monitorizar o pipeline
Neste passo, irá utilizar o portal do Azure para monitorizar os acontecimentos de uma fábrica de dados do Azure. Também pode utilizar os cmdlets PowerShell para monitorizar os conjuntos de dados e pipelines. Para obter detalhes sobre a monitorização, consulte [monitorizar e gerir Pipelines](data-factory-monitor-manage-pipelines.md).

1. No diagrama, faça duplo clique em **EmpOnPremSQLTable**.  

    ![Setores de EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Tenha em atenção que todos os dados corta estão **pronto** porque a duração do pipeline (hora de início para a hora de fim) está no passado de estado. Também é uma vez que introduziu os dados na base de dados do SQL Server e lá está o tempo todo. Confirme que não existem setores na **setores do problema** seção na parte inferior. Para ver todos os setores, clique em **ver mais** na parte inferior da lista de setores.
3. Agora, no **conjuntos de dados** página, clique em **OutputBlobTable**.

    ![Setores de OputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Clique em qualquer setor de dados da lista e deverá ver o **setor de dados** página. Vai ver execuções de atividade para o setor. Pode ver apenas uma atividade executar normalmente.  

    ![Painel de setor de dados](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Se o setor não estiver no estado **Pronto**, pode ver os setores a montante que não estão no estado Pronto e estão a impedir a execução do setor atual na lista **Setores a montante que não estão prontos**.
5. Clique nas **execução da atividade** da lista na parte inferior para ver **detalhes da execução da atividade**.

   ![Página de detalhes da execução de atividade](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Irá ver informações como o débito, a duração e o gateway utilizado para transferir os dados.
6. Clique em **X** para fechar todas as páginas até
7. voltar à home page para o **ADFTutorialOnPremDF**.
8. (opcional) Clique em **Pipelines**, clique em **ADFTutorialOnPremDF**e fazer uma busca tabelas de entrada (**consumida**) ou conjuntos de dados de saída (**produzido**).
9. Utilize ferramentas como [Explorador de armazenamento do Microsoft](https://storageexplorer.com/) para verificar se um ficheiro/blob é criado para cada hora.

   ![Explorador do Armazenamento do Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Passos Seguintes
* Ver [Data Management Gateway](data-factory-data-management-gateway.md) artigo para todos os detalhes sobre o Data Management Gateway.
* Ver [copiar dados de Blobs do Azure para o Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para saber mais sobre como utilizar a atividade de cópia para mover dados de um arquivo de dados de origem para um arquivo de dados de sink.
