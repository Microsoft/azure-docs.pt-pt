---
title: Dados de movimento - Gateway de Gestão de Dados
description: Crie uma porta de dados para mover dados entre as instalações e a nuvem. Utilize o Gateway de Gestão de Dados na Azure Data Factory para mover os seus dados.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 53fce1744ccbf4289b2415e926e084c90d708a13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380292"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Mover dados entre fontes no local e a nuvem com Data Management Gateway
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte os dados de [cópia entre as instalações e a nuvem utilizando a Data Factory.](../tutorial-hybrid-copy-powershell.md)

Este artigo fornece uma visão geral da integração de dados entre lojas de dados no local e lojas de dados em nuvem usando data factory. Baseia-se no artigo de Atividades de [Movimento de Dados](data-factory-data-movement-activities.md) e noutros artigos de conceitos fundamentais da fábrica de dados: [conjuntos de dados](data-factory-create-datasets.md) e [oleodutos.](data-factory-create-pipelines.md)

## <a name="data-management-gateway"></a>Data Management Gateway
Tem de instalar o Data Management Gateway na sua máquina no local para permitir a deslocação de dados de/para uma loja de dados no local. O gateway pode ser instalado na mesma máquina que a loja de dados ou numa máquina diferente, desde que o gateway possa ligar-se à loja de dados.

> [!IMPORTANT]
> Consulte o artigo [do Data Management Gateway](data-factory-data-management-gateway.md) para obter mais informações sobre o Data Management Gateway.

A seguinte passagem mostra como criar uma fábrica de dados com um oleoduto que transfere dados de uma base de dados **do SQL Server** no local para um armazenamento de bolhas Azure. Como parte das instruções, vai instalar e configurar o Data Management Gateway no seu computador.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Walkthrough: copiar dados no local para cloud
Neste passeio, faça os seguintes passos:

1. Criar uma fábrica de dados.
2. Criar uma porta de entrada de gestão de dados.
3. Criar serviços ligados para lojas de dados de origem e afundar.
4. Crie conjuntos de dados para representar dados de entrada e saída.
5. Criar um pipeline com uma atividade de cópia para mover os dados.

## <a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial
Antes de iniciar esta passagem, deve ter os seguintes pré-requisitos:

* **Assinatura Azure**.  Se não tiver uma subscrição, pode criar uma conta gratuita em apenas alguns minutos. Consulte o artigo [Free Trial](https://azure.microsoft.com/pricing/free-trial/) para mais detalhes.
* **Conta de Armazenamento Azure**. Você usa o armazenamento de bolhas como uma loja de dados **de destino/pia** neste tutorial. se não tiver uma conta de armazenamento Azure, consulte o artigo de [conta de armazenamento Criar um](../../storage/common/storage-account-create.md) artigo de conta de armazenamento para etapas para criar uma.
* **Sql Server**. Utiliza uma base de dados SQL Server como uma loja de dados **de origem** neste tutorial.

## <a name="create-data-factory"></a>Criar fábrica de dados
Neste passo, você usa o portal Azure para criar uma instância da Azure Data Factory chamada **ADFTutorialOnPremDF**.

1. Faça login no [portal Azure](https://portal.azure.com).
2. Clique **em Criar um recurso,** clique em Inteligência + **análise,** e clique em **Data Factory**.

   ![Novo -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Na página de **nova fábrica de dados,** insira **a ADFTutorialOnPremDF** para o nome.

    ![Adicionar ao Startboard](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: O **nome da fábrica de dados "ADFTutorialOnPremDF" não está disponível,** altere o nome da fábrica de dados (por exemplo, o seu nomeADFTutorialOnPremDF) e tente criar novamente. Utilize este nome no lugar da ADFTutorialOnPremDF enquanto executa os passos restantes neste tutorial.
   >
   > O nome da fábrica de dados pode ser registado como nome **DNS** no futuro e, assim, tornar-se visível publicamente.
   >
   >
4. Selecione a **Subscrição do Azure** onde pretende que seja criada a fábrica de dados.
5. Selecione o **grupo de recursos** existente ou crie um grupo de recursos. Para o tutorial, crie um grupo de recursos **chamado: ADFTutorialResourceGroup**.
6. Clique em **Criar** na nova página de **fábrica de dados.**

   > [!IMPORTANT]
   > Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ao nível do grupo de recursos/subscrição.
   >
   >
7. Após a criação estar concluída, vê a página **da Data Factory** como mostrado na seguinte imagem:

   ![Página inicial da fábrica de dados](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Criar porta de entrada
1. Na página data **factory,** clique em **Autor e implemente** azulejo para lançar o **Editor** para a fábrica de dados.

    ![Mosaico Criar e Implementar](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. No Editor de Fábrica de Dados, clique **em ... Mais** na barra de ferramentas e, em seguida, clique em **Novo gateway de dados**. Em alternativa, pode clicar à direita em **Data Gateways** na vista da árvore e clicar em **Novo gateway de dados**.

   ![Novo portal de dados na barra de ferramentas](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Na página **Criar,** insira **a via adftutorial para** o **nome,** e clique **em OK**.     

    ![Criar página gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > Nesta passagem, cria-se o gateway lógico com apenas um nó (máquina Windows no local). Pode escalar uma porta de gestão de dados associando várias máquinas no local com o gateway. Pode aumentar o número de trabalhos de movimento de dados que podem funcionar simultaneamente num nó. Esta funcionalidade também está disponível para um gateway lógico com um único nó. Consulte [a porta de gestão de dados de escala no artigo da Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) para obter mais detalhes.  
4. Na página **Configurar,** clique **em Instalar diretamente neste computador**. Esta ação descarrega o pacote de instalação para o gateway, instala, configura e regista o gateway no computador.  

   > [!NOTE]
   > Utilize o Internet Explorer ou um navegador web compatível com o Microsoft ClickOnce.
   >
   > Se estiver a utilizar o Chrome, vá à loja web do [Chrome,](https://chrome.google.com/webstore/)procure com a palavra-chave "ClickOnce", escolha uma das extensões ClickOnce e instale-a.
   >
   > Faça o mesmo para o Firefox (instale o add-in). Clique no botão **Menu Aberto** na barra de **ferramentas (três linhas horizontais** no canto superior direito), clique **em Add-ons,** procure com a palavra-chave "ClickOnce", escolha uma das extensões ClickOnce e instale-a.    
   >
   >

    ![Gateway - Página de configuração](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Desta forma é a maneira mais fácil (um clique) de descarregar, instalar, configurar e registar o gateway num único passo. Pode ver que a **aplicação Microsoft Data Management Gateway Configuration Manager** está instalada no seu computador. Também pode encontrar o **ConfigManager.exe** executável na pasta: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    Também pode descarregar e instalar o gateway manualmente utilizando os links desta página e registá-lo utilizando a chave mostrada na caixa de texto **NEW KEY.**

    Consulte o artigo [data Management Gateway](data-factory-data-management-gateway.md) para obter todos os detalhes sobre o gateway.

   > [!NOTE]
   > Tem de ser administrador no computador local para instalar e configurar o Gateway de Gestão de Dados com sucesso. Pode adicionar utilizadores adicionais ao grupo local windows **dos Utilizadores de Gateway de Gestão de Dados.** Os membros deste grupo podem utilizar a ferramenta Data Management Gateway Configuration Manager para configurar o gateway.
   >
   >
5. Aguarde alguns minutos ou aguarde até ver a seguinte mensagem de notificação:

    ![Instalação Gateway bem sucedida](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Lance **a aplicação do Gestor de Configuração gateway de gestão de dados** no seu computador. Na janela **'Pesquisa',** digite **Data Management Gateway** para aceder a este utilitário. Também pode encontrar o **ConfigManager.exe** executável na pasta: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

    ![Gestor de configuração gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Confirme que vê `adftutorialgateway is connected to the cloud service` a mensagem. A barra de estado dos visores inferiores Ligada ao **serviço de nuvem** juntamente com uma marca de **verificação verde**.

    No separador **Casa,** também pode fazer as seguintes operações:

   * **Registe** um portal com uma chave do portal Azure utilizando o botão Registar.
   * **Pare** o serviço de anfitrião gateway de gestão de dados que funciona na sua máquina de gateway.
   * **Agendar atualizações** a instalar a uma hora específica do dia.
   * Ver quando o portal foi **atualizado pela última vez.**
   * Especifique a hora em que pode ser instalada uma atualização do gateway.
8. Mude para o **separador Definições.** O certificado especificado na secção **Certificado** é utilizado para encriptar/desencriptar credenciais para a loja de dados no local que especifica no portal. (opcional) Clique **em Alterar** para utilizar o seu próprio certificado. Por predefinição, o gateway utiliza o certificado que é gerado automaticamente pelo serviço Data Factory.

    ![Configuração do certificado gateway](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Também pode fazer as seguintes ações no **separador Definições:**

   * Ver ou exportar o certificado utilizado pelo gateway.
   * Altere o ponto final HTTPS utilizado pelo gateway.    
   * Desaprova um representante HTTP para ser utilizado pelo gateway.     
9. (opcional) Mude para o separador **Diagnóstico,** verifique a opção **de registo verboso Enable** se pretender ativar a verificação de registo verboso que pode utilizar para resolver problemas com o gateway. As informações de registo podem ser encontradas no **Visualizador de Eventos** no nó de Gateway de Gestão de Dados de **Aplicações e Serviços.**  ->  

    ![Separador Diagnóstico](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Também pode executar as seguintes ações no separador **Diagnóstico:**

   * Utilize a secção **de Ligação** de Teste para uma fonte de dados no local utilizando o gateway.
   * Clique **em Ver Registos** para ver o registo gateway de gestão de dados numa janela do Visualizador de Eventos.
   * Clique **em Enviar Registos** para carregar um ficheiro zip com registos dos últimos sete dias para a Microsoft para facilitar a resolução de problemas dos seus problemas.
10. No separador Diagnóstico, na secção **De Ligação de Teste,** selecione **SqlServer** para o tipo de loja de dados, introduza o nome do servidor de base de **dados,** nome da base de dados, especifique o tipo de autenticação, introduza o nome de utilizador e a palavra-passe e clique em **Testar** para testar se o gateway pode ligar-se à base de dados.
11. Mude para o navegador web e, no **portal Azure,** clique em **OK** na página **Configure** e, em seguida, na página **New data gateway.**
12. Você deve ver **adftutorialgateway** sob **data gateways** na vista da árvore à esquerda.  Se clicar nele, deverá ver o JSON associado.

## <a name="create-linked-services"></a>Criar serviços ligados
Neste passo, cria dois serviços ligados: **AzureStorageLinkedService** e **SqlServerLinkedService**. O **SqlServerLinkedService** liga uma base de dados do SQL Server e o serviço ligado **AzureStorageLinkedService** liga uma loja de blob Azure à fábrica de dados. Cria-se um pipeline mais tarde nesta passagem que copia dados da base de dados do SQL Server para a loja de blobS Azure.

#### <a name="add-a-linked-service-to-a-sql-server-database"></a>Adicione um serviço ligado a uma base de dados do SQL Server
1. No Editor de **Fábrica de Dados,** clique em **Nova loja de dados** na barra de ferramentas e selecione **SQL Server**.

   ![Novo serviço ligado ao SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. No editor da **JSON** à direita, faça os seguintes passos:

   1. Para o **portal De gatewayName**, especifique **adftutorialgateway**.    
   2. Na **ligação,** faça os seguintes passos:    

      1. Para **o nome de servidor,** insira o nome do servidor que acolhe a base de dados do SQL Server.
      2. Para **o nome de base de dados,** insira o nome da base de dados.
      3. Clique em **Encriptar** o botão na barra de ferramentas. Vê a aplicação do Gestor de Credenciais.

         ![Aplicação do Gestor de Credenciais](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. Na caixa de diálogo **de definições de credenciais,** especifique o tipo de autenticação, o nome de utilizador e a palavra-passe, e clique **em OK**. Se a ligação for bem sucedida, as credenciais encriptadas são armazenadas no JSON e a caixa de diálogo fecha.
      5. Feche o separador de navegador vazio que lançou a caixa de diálogo se não estiver automaticamente fechada e volte ao separador com o portal Azure.

         Na máquina de gateway, estas credenciais são **encriptadas** utilizando um certificado que o serviço data factory possui. Se pretender utilizar o certificado que está associado ao Gateway de Gestão de Dados, consulte as credenciais de Definição com segurança.    
   3. Clique em **Implementar** na barra de comando para implementar o serviço ligado ao SQL Server. Você deve ver o serviço ligado na vista da árvore.

      ![Serviço ligado ao SERVIDOR SQL na vista da árvore](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Adicione um serviço ligado para uma conta de armazenamento Azure
1. No Editor de **Fábrica de Dados,** clique em **Nova loja de dados** na barra de comando e clique no **armazenamento do Azure**.
2. Insira o nome da sua conta de armazenamento Azure para o **nome da Conta**.
3. Introduza a chave para a sua conta de armazenamento Azure para a **tecla Conta**.
4. Clique **em Implementar** para implementar o **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar os conjuntos de dados de entrada e de saída que representam os dados de entrada e de saída da operação de cópia (base de dados do SQL Server no local => armazenamento de blobs do Azure). Antes de criar os conjuntos de dados, execute os seguintes passos (os passos detalhados são apresentados a seguir à lista):

* Crie uma tabela com o nome **emp** na Base de Dados do SQL Server que adicionou como um serviço ligado à fábrica de dados e insira algumas entradas de exemplo na tabela.
* Crie um contentor de blobs com o nome **adftutorial** na conta de armazenamento de blobs do Azure que adicionou como um serviço ligado à fábrica de dados.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Prepare o Servidor SQL no local para o tutorial
1. Na base de dados especificada para o serviço de ligação SQL Server **(SqlServerLinkedService),** utilize o seguinte script SQL para criar a tabela **emp** na base de dados.

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

1. No Editor de **Fábrica de Dados,** **clique... Mais,** clique em **Novo conjunto de dados** na barra de comando e clique na **tabela SQL Server**.
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

   * **tipo** é definido para **SqlServerTable**.
   * **tableName** está definido para **emp**.
   * **linkedServiceName** está definido para **SqlServerLinkedService** (você tinha criado este serviço ligado mais cedo neste walkthrough.).
   * Para um conjunto de dados de entrada que não seja gerado por outro oleoduto na Azure Data Factory, tem de ser definido **de forma externa** à **verdade**. Denota que os dados de entrada são produzidos externos ao serviço Azure Data Factory. Pode especificar opcionalmente quaisquer políticas de dados externas utilizando o elemento **externalData** na secção **Política.**    

   Consulte [os dados de/para o SQL Server](data-factory-sqlserver-connector.md) para obter mais detalhes sobre as propriedades do JSON.
3. Clique em **Implementar** na barra de comando para implementar o conjunto de dados.  

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída

1. No Editor de **Fábrica de Dados,** clique em **Novo conjunto de dados** na barra de comando e clique no **armazenamento de Azure Blob**.
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

   * **tipo** é definido para **AzureBlob**.
   * **linkedServiceName** está definido para **AzureStorageLinkedService** (criou este serviço ligado no Passo 2).
   * **a pastaA** **adftutorial/outfromonpremdf** onde outfromonpremdf é a pasta no recipiente adftutorial. Crie o recipiente **adftutorial** se já não existir.
   * A **disponibilidade** está definida como **de hora a hora** (**frequência** definida como **hora** e **intervalo** definido como **1**).  O serviço Data Factory gera uma fatia de dados de saída a cada hora na tabela **emp** na Base de Dados Azure SQL.

   Se não especificar um **nome de ficheiro** para uma tabela de **saída,** os ficheiros gerados na **pastaPata** são nomeados no seguinte `Data.<Guid>.txt` formato: (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Para definir **folderPath** e **fileName** de forma dinâmica com base no tempo **SliceStart**, utilize a propriedade partitionedBy. No exemplo seguinte, o folderPath utiliza o Ano, o Mês e o Dia do SliceStart (hora de início do setor a ser processado) e o fileName utiliza a Hora do SliceStart. Por exemplo, se está a ser produzido um setor para 2014-10-20T08:00:00, folderName está definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName está definido como 08.csv.

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

    Consulte [os dados de/para o Armazenamento Azure Blob](data-factory-azure-blob-connector.md) para obter mais informações sobre as propriedades da JSON.
3. Clique em **Implementar** na barra de comando para implementar o conjunto de dados. Confirme que vê ambos os conjuntos de dados na vista da árvore.  

## <a name="create-pipeline"></a>Criar pipeline
Neste passo, cria-se um **pipeline** com uma **Atividade de Cópia** que utiliza o **EmpOnPremSQLTable** como entrada e **OutputBlobTable** como saída.

1. No Data Factory Editor, **clique... Mais**, e clique em **Novo oleoduto.**
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

   * Na secção de atividades, existe apenas atividade cujo **tipo** está definido para **Copiar.**
   * **A entrada** para a atividade está definida para **EmpOnPremSQLTable** e a **saída** para a atividade está definida para **OutputBlobTable**.
   * Na secção **typeProperties,** **o SqlSource** é especificado como o **tipo de origem** e **o BlobSink** é especificado como o **tipo de pia**.
   * A consulta SQL `select * from emp` é especificada para a propriedade **sqlReaderQuery** da **SqlSource**.

   Tanto o datetime de início como de fim têm de estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O tempo **end** é opcional, mas iremos utilizá-lo neste tutorial.

   Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9/9/9999** como o valor da propriedade **end**.

   Está a definir a duração em que as fatias de dados são processadas com base nas propriedades **disponibilidade** que foram definidas para cada conjunto de dados da Azure Data Factory.

   Existem 24 setores de dados no exemplo, uma vez que estes são produzidos de hora a hora.        
3. Clique em **Implementar** na barra de comando para implantar o conjunto de dados (a tabela é um conjunto de dados retangular). Confirme se o gasoduto aparece na vista da árvore sob o nó **pipelines.**  
4. Agora, clique em **X** duas vezes para fechar a página para voltar à página **data factory** para o **ADFTutorialOnPremDF**.

**Parabéns!** Criou com sucesso uma fábrica de dados Azure, serviços ligados, conjuntos de dados e um oleoduto e agendou o oleoduto.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Ver a fábrica de dados numa Vista de Diagrama
1. No **portal Azure,** clique em azulejo **do Diagrama** na página inicial para a fábrica de dados **ADFTutorialOnPremDF.** :

    ![Ligação diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Deverá ver um diagrama semelhante à seguinte imagem:

    ![Vista de Diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Pode ampliar, ampliar, ampliar para 100%, fazer zoom para encaixar, posicionar automaticamente os oleodutos e conjuntos de dados e mostrar informações de linhagem (destaques a montante e a jusante de itens selecionados).  Pode clicar duas vezes num objeto (conjunto de dados de entrada/saída ou pipeline) para ver propriedades para o mesmo.

## <a name="monitor-pipeline"></a>Monitorizar o pipeline
Neste passo, você usa o portal Azure para monitorizar o que se passa numa fábrica de dados Azure. Também pode utilizar os cmdlets PowerShell para monitorizar os conjuntos de dados e pipelines. Para obter mais informações sobre a monitorização, consulte [Monitor e Gerencie os Gasodutos](data-factory-monitor-manage-pipelines.md).

1. No diagrama, clique duas vezes **em EmpOnPremSQLTable**.  

    ![Fatias empOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Note que todas as fatias de dados estão em estado **de pronto** porque a duração do gasoduto (hora de início até ao fim do tempo) está no passado. É também porque inseriu os dados na base de dados do SQL Server e está sempre lá. Confirme se não aparecem fatias na secção **de fatias de problema** na parte inferior. Para ver todas as fatias, clique em **Ver Mais** na parte inferior da lista de fatias.
3. Agora, na página **Datasets,** clique **em OutputBlobTable**.

    ![Fatias OputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Clique em qualquer fatia de dados da lista e deverá ver a página **Data Slice.** Vês que a atividade corre para a fatia. Normalmente, só se vê uma atividade.  

    ![Lâmina de fatia de dados](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Se o setor não estiver no estado **Pronto**, pode ver os setores a montante que não estão no estado Pronto e estão a impedir a execução do setor atual na lista **Setores a montante que não estão prontos**.
5. Clique na **atividade executada** a partir da lista na parte inferior para ver detalhes **de execução da atividade**.

   ![Página de detalhes de execução de atividades](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Veria informações como a produção, a duração e o gateway usado para transferir os dados.
6. Clique em **X** para fechar todas as páginas até
7. voltar à página inicial para o **ADFTutorialOnPremDF**.
8. (opcional) Clique em **Pipelines,** clique em **ADFTutorialOnPremDF,** e faça através de tabelas de entrada **(Consumida)** ou conjuntos de dados de saída **(Produzido).**
9. Utilize ferramentas como o [Microsoft Storage Explorer](https://storageexplorer.com/) para verificar se uma bolha/ficheiro é criada por cada hora.

   ![Explorador de Armazenamento do Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Passos seguintes
* Consulte o artigo [do Data Management Gateway](data-factory-data-management-gateway.md) para obter todos os detalhes sobre o Gateway de Gestão de Dados.
* Consulte [os dados de Cópia de Azure Blob para Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para saber como usar a Copy Activity para mover dados de uma loja de dados de origem para uma loja de dados de lavatórios.
