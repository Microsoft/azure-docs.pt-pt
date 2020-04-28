---
title: Mover dados - Gateway de Gestão de Dados
description: Criar uma porta de dados para mover dados entre as instalações e a nuvem. Utilize gateway de gestão de dados na Azure Data Factory para mover os seus dados.
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
ms.openlocfilehash: be797f76988c924503e11b6f66cce899b515e3a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75982203"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Mova dados entre as fontes no local e a nuvem com Gateway de Gestão de Dados
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [os dados de cópia sinuoso entre as instalações e a nuvem utilizando](../tutorial-hybrid-copy-powershell.md)a Data Factory .

Este artigo fornece uma visão geral da integração de dados entre lojas de dados no local e lojas de dados em nuvem usando data factory. Baseia-se no artigo de [Atividades](data-factory-data-movement-activities.md) de Movimento de Dados e outros conceitos fundamentais da fábrica de dados: [conjuntos](data-factory-create-datasets.md) de dados e [oleodutos.](data-factory-create-pipelines.md)

## <a name="data-management-gateway"></a>Data Management Gateway
Tem de instalar o Portal de Gestão de Dados na sua máquina no local para permitir a deslocação de dados de/para uma loja de dados no local. O gateway pode ser instalado na mesma máquina que o armazenamento de dados ou numa máquina diferente, desde que o portal possa ligar-se ao armazenamento de dados.

> [!IMPORTANT]
> Consulte o artigo gateway de [gestão](data-factory-data-management-gateway.md) de dados para obter detalhes sobre gateway de gestão de dados.

A seguinte passagem mostra como criar uma fábrica de dados com um pipeline que transfere dados de uma base de dados **do SQL Server** no local para um armazenamento de blob Azure. Como parte das instruções, vai instalar e configurar o Data Management Gateway no seu computador.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Walkthrough: copiar dados no local para cloud
Nesta caminhada você faz os seguintes passos:

1. Criar uma fábrica de dados.
2. Criar um portal de gestão de dados.
3. Criar serviços ligados para lojas de dados de origem e afundar.
4. Crie conjuntos de dados para representar dados de entrada e de saída.
5. Criar um pipeline com uma atividade de cópia para mover os dados.

## <a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial
Antes de iniciar esta passagem, deve ter os seguintes pré-requisitos:

* **Assinatura Azure.**  Se não tiver uma subscrição, pode criar uma conta gratuita em apenas alguns minutos. Consulte o artigo [Do Teste Gratuito](https://azure.microsoft.com/pricing/free-trial/) para mais detalhes.
* Conta de **Armazenamento Azure.** Você usa o armazenamento de blob como uma loja de dados **de destino/pia** neste tutorial. se não tiver uma conta de armazenamento Azure, consulte o artigo de [conta de armazenamento Criar uma conta](../../storage/common/storage-account-create.md) de armazenamento para passos para criar uma.
* **Servidor SQL**. Neste tutorial, vai utilizar uma base de dados do SQL Server no local como um arquivo de dados de **origem**.

## <a name="create-data-factory"></a>Criar fábrica de dados
Neste passo, utiliza-se o portal Azure para criar uma instância azure data factory chamada **ADFTutorialOnPremDF**.

1. Faça login no [portal Azure.](https://portal.azure.com)
2. Clique **em Criar um recurso,** clique em Inteligência + **análise,** e clique em **Data Factory**.

   ![Novo -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Na página da nova fábrica de **dados,** insira **a ADFTutorialOnPremDF** para o nome.

    ![Adicionar ao Startboard](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: O nome de **fábrica de dados "ADFTutorialOnPremDF" não está disponível,** altere o nome da fábrica de dados (por exemplo, o seu nomeADFTutorialOnPremDF) e tente criar novamente. Utilize este nome no lugar da ADFTutorialOnPremDF enquanto executa os passos restantes neste tutorial.
   >
   > O nome da fábrica de dados pode ser registado como um nome **DNS** no futuro e, portanto, tornar-se publicamente visível.
   >
   >
4. Selecione a **Subscrição do Azure** onde pretende que seja criada a fábrica de dados.
5. Selecione o **grupo de recursos** existente ou crie um grupo de recursos. Para o tutorial, crie um grupo de recursos chamado **ADFTutorialResourceGroup**.
6. Clique em **Criar** na página da nova fábrica de **dados.**

   > [!IMPORTANT]
   > Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ao nível do grupo de recursos/subscrição.
   >
   >
7. Após a criação estar completa, vê a página **Data Factory** como mostra a seguinte imagem:

   ![Página inicial da fábrica de dados](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Criar porta de entrada
1. Na página **Data Factory,** clique em **Autor e implante** azulejos para lançar o **Editor** para a fábrica de dados.

    ![Mosaico Criar e Implementar](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. No Editor da Fábrica de Dados, **clique... Mais** na barra de ferramentas e, em seguida, clique em **Novo portal de dados**. Em alternativa, pode clicar em **Gateways** de Dados na vista da árvore e clicar em **Novo portal de dados**.

   ![Nova porta de dados na barra de ferramentas](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Na página **Criar,** introduza **aadtutorialgateway** para o **nome**, e clique **OK**.     

    ![Criar página Gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > Nesta passagem, cria-se a porta de entrada lógica com apenas um nó (no local da máquina Windows). Pode escalar uma porta de gestão de dados associando várias máquinas no local com o portal. Pode aumentar o número de postos de trabalho no movimento de dados que podem funcionar simultaneamente num nó. Esta funcionalidade também está disponível para um portal lógico com um único nó. Consulte o portal de gestão de [dados de Escalana no](data-factory-data-management-gateway-high-availability-scalability.md) artigo da Azure Data Factory para mais detalhes.  
4. Na página **Configurar,** clique **em Instalar diretamente neste computador**. Esta ação descarrega o pacote de instalação para o gateway, instala, configura e regista a porta de entrada no computador.  

   > [!NOTE]
   > Utilize o Internet Explorer ou um navegador web compatível com o Microsoft ClickOnce.
   >
   > Se estiver a utilizar o Chrome, vá à loja web do [Chrome,](https://chrome.google.com/webstore/)procure com a palavra-chave "ClickOnce", escolha uma das extensões ClickOnce e instale-a.
   >
   > Faça o mesmo para o Firefox (instale o add-in). Clique no botão **Menu Aberto** na barra de ferramentas **(três linhas horizontais** no canto superior direito), clique em **Add-ons,** procure com a palavra-chave "ClickOnce", escolha uma das extensões ClickOnce e instale-a.    
   >
   >

    ![Gateway - Página de configuração](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Desta forma é a forma mais fácil (um clique) de descarregar, instalar, configurar e registar o gateway num único passo. Pode ver que a aplicação **microsoft Data Management Gateway Configuration Manager** está instalada no seu computador. Também pode encontrar o **ConfigManager.exe** executável na pasta: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    Também pode descarregar e instalar o gateway manualmente utilizando os links desta página e registá-lo utilizando a chave mostrada na caixa de texto **NEW KEY.**

    Consulte o artigo gateway de [Gestão](data-factory-data-management-gateway.md) de Dados para obter todos os detalhes sobre o gateway.

   > [!NOTE]
   > Deve ser administrador no computador local para instalar e configurar com sucesso o Portal de Gestão de Dados. Pode adicionar utilizadores adicionais ao grupo local windows de Gestão de **Dados dos Utilizadores.** Os membros deste grupo podem usar a ferramenta gestorde configuração do Gateway de Gestão de Dados para configurar o gateway.
   >
   >
5. Aguarde alguns minutos ou aguarde até ver a seguinte mensagem de notificação:

    ![Instalação gateway bem sucedida](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Lance a aplicação de **Gestor de Configuração** gateway de gestão de dados no seu computador. Na janela **de pesquisa,** digite gateway de **gestão** de dados para aceder a este utilitário. Também pode encontrar o **Executável ConfigManager.exe** na pasta: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

    ![Gestor de configuração gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Confirme que `adftutorialgateway is connected to the cloud service` vê mensagem. A barra de estado do fundo mostra **Ligada ao serviço de nuvem** juntamente com uma marca de **verificação verde**.

    No separador **Home,** também pode fazer as seguintes operações:

   * **Registe** uma porta de entrada com uma chave do portal Azure utilizando o botão Register.
   * **Pare** o serviço de hospedar gateway de gestão de dados em funcionamento na sua máquina de gateway.
   * **Agendar atualizações** a instalar numa hora específica do dia.
   * Veja quando o portal foi **atualizado pela última vez.**
   * Especifique o tempo em que pode ser instalada uma atualização do gateway.
8. Mude para o separador **Definições.** O certificado especificado na secção **Certificado** é utilizado para encriptar/desencriptar credenciais para a loja de dados no local que especifica no portal. (opcional) Clique em **Alterar** para utilizar o seu próprio certificado. Por padrão, o gateway utiliza o certificado que é gerado automaticamente pelo serviço Data Factory.

    ![Configuração do certificado gateway](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Também pode fazer as seguintes ações no separador **Definições:**

   * Ver ou exportar o certificado utilizado pelo portal.
   * Altere o ponto final HTTPS utilizado pelo portal.    
   * Detete um proxy HTTP para ser utilizado pelo portal.     
9. (opcional) Mude para o separador **Diagnósticos,** verifique a opção **de registo verbose Enable** se pretender ativar a exploração verbosa que pode utilizar para resolver problemas com o portal. As informações de registo podem ser encontradas no **Event Viewer** no âmbito do nó gateway de**Gestão** de Dados **de Aplicações e Serviços.** -> 

    ![Separador Diagnóstico](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Também pode realizar as seguintes ações no separador **Diagnósticos:**

   * Utilize a secção **de ligação** de teste a uma fonte de dados no local utilizando o gateway.
   * Clique em **Ver Registos** para ver o log gateway de gestão de dados numa janela do Espectador de Eventos.
   * Clique em **Enviar Registos** para fazer o upload de um ficheiro zip com registos dos últimos sete dias para a Microsoft para facilitar a resolução de problemas dos seus problemas.
10. No separador **Diagnóstico,** na secção **De Ligação** de Teste, selecione **SqlServer** para o tipo de loja de dados, introduza o nome do servidor de base de dados, nome da base de dados, especifique o tipo de autenticação, introduza o nome do utilizador e a palavra-passe e clique em **Testar** para testar se o gateway pode ligar-se à base de dados.
11. Mude para o navegador web e no **portal Azure,** clique **OK** na página **Configure** e, em seguida, na página de gateway de **dados novos.**
12. Você deve ver **adftutorialgateway** sob **Data Gateways** na vista da árvore à esquerda.  Se clicar nele, deve ver o JSON associado.

## <a name="create-linked-services"></a>Criar serviços ligados
Neste passo, cria dois serviços ligados: **AzureStorageLinkedService** e **SqlServerLinkedService**. O **SqlServerLinkedService** liga uma base de dados do SQL Server no local e o serviço ligado ao **AzureStorageLinkedService** liga uma loja de blob Azure à fábrica de dados. Cria-se um pipeline mais tarde neste walkthrough que copia dados da base de dados do SQL Server no local para a loja de blob Azure.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Adicione um serviço ligado a uma base de dados do SQL Server no local
1. No **Editor da Fábrica**de Dados, clique em **Nova loja** de dados na barra de ferramentas e selecione **SQL Server**.

   ![Novo serviço ligado ao Servidor SQL](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. No editor da **JSON** à direita, faça os seguintes passos:

   1. Para o **gatewayName,** especifique **adftutorialgateway**.    
   2. Na **ligaçãoString,** faça os seguintes passos:    

      1. Para o nome do **servidor,** introduza o nome do servidor que acolhe a base de dados do Servidor SQL.
      2. Para poder e não ser chamado a inserir um **nome**de base de dados.
      3. Clique no botão **'Encriptar'** na barra de ferramentas. Vê a aplicação do Gestor de Credenciais.

         ![Aplicação do Gestor de Credenciais](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. Na caixa de diálogo **Definição de Credenciais,** especifique o tipo de autenticação, o nome do utilizador e a palavra-passe e clique em **OK**. Se a ligação for bem sucedida, as credenciais encriptadas são armazenadas no JSON e a caixa de diálogo fecha.
      5. Feche o separador de navegador vazio que lançou a caixa de diálogo se não estiver automaticamente fechada e volte ao separador com o portal Azure.

         Na máquina gateway, estas credenciais são **encriptadas** utilizando um certificado que o serviço Data Factory possui. Se pretender utilizar o certificado associado ao Gateway de Gestão de Dados, consulte as credenciais set de forma segura.    
   3. Clique em **implementar** na barra de comando para implementar o serviço ligado ao Servidor SQL. Devia ver o serviço ligado na vista da árvore.

      ![Serviço ligado ao Servidor SQL na vista da árvore](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Adicione um serviço ligado para uma conta de armazenamento Azure
1. No **Editor da Fábrica**de Dados, clique em **Nova loja** de dados na barra de comando e clique no **armazenamento Azure**.
2. Insira o nome da sua conta de armazenamento Azure para o **nome da Conta**.
3. Introduza a chave para a sua conta de armazenamento Azure para a **chave Conta**.
4. Clique em **implementar** o **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar os conjuntos de dados de entrada e de saída que representam os dados de entrada e de saída da operação de cópia (base de dados do SQL Server no local => armazenamento de blobs do Azure). Antes de criar os conjuntos de dados, execute os seguintes passos (os passos detalhados são apresentados a seguir à lista):

* Crie uma tabela com o nome **emp** na Base de Dados do SQL Server que adicionou como um serviço ligado à fábrica de dados e insira algumas entradas de exemplo na tabela.
* Crie um contentor de blobs com o nome **adftutorial** na conta de armazenamento de blobs do Azure que adicionou como um serviço ligado à fábrica de dados.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Prepare o Servidor SQL no local para o tutorial
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

1. No **Editor da Fábrica**de Dados, **clique... Mais,** clique em **Novo conjunto** de dados na barra de comando e clique na **tabela SQL Server**.
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

   * **o tipo** é definido para **SqlServerTable**.
   * **o nome do quadro** está definido para **emp**.
   * **linkedServiceName** é definido para **SqlServerLinkedService** (criou este serviço ligado mais cedo nesta passagem.).
   * Para um conjunto de dados de entrada que não seja gerado por outro pipeline na Azure Data Factory, deve definir **o exterior** para **o verdadeiro**. Denota que os dados de entrada são produzidos externos ao serviço Azure Data Factory. Pode especificar opcionalmente quaisquer políticas externas de dados utilizando o elemento **dados externos** na secção **Política.**    

   Consulte [os dados de/para/do SQL Server](data-factory-sqlserver-connector.md) para obter detalhes sobre as propriedades da JSON.
3. Clique em **implementar** na barra de comando para implementar o conjunto de dados.  

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída

1. No **Editor da Fábrica**de Dados, clique em **Novo conjunto** de dados na barra de comando e clique no **armazenamento Azure Blob**.
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

   * **o tipo** é definido para **AzureBlob**.
   * **linkedServiceName** é definido para **AzureStorageLinkedService** (criou este serviço ligado no Passo 2).
   * **pastaO** caminho é definido para **adftutorial/outfromonpremdf** onde outfromonpremdf é a pasta no recipiente adftutorial. Crie o recipiente **adftutorial** se já não existir.
   * A **disponibilidade** está definida como **de hora a hora** (**frequência** definida como **hora** e **intervalo** definido como **1**).  O serviço Data Factory gera uma fatia de dados de saída a cada hora na tabela **emp** na Base de Dados Azure SQL.

   Se não especificar um nome de **ficheiro** para uma tabela de **saída,** os ficheiros gerados na **pastaPath** são nomeados no seguinte formato: `Data.<Guid>.txt` (por exemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

    Consulte [os dados de/para o Armazenamento de Blob Azure](data-factory-azure-blob-connector.md) para obter detalhes sobre as propriedades da JSON.
3. Clique em **implementar** na barra de comando para implementar o conjunto de dados. Confirme que vê ambos os conjuntos de dados na vista da árvore.  

## <a name="create-pipeline"></a>Criar pipeline
Neste passo, cria-se um **pipeline** com uma **Atividade de Cópia** que utiliza o **EmpOnPremSQLTable** como entrada e **OutputBlobTable** como saída.

1. Em Data Factory Editor, clique **... Mais,** e clique em **Novo oleoduto.**
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

   * Na secção de atividades, existe apenas atividade cujo **tipo** está definido para **Copiar**.
   * **A entrada** para a atividade está definida para **EmpOnPremSQLTable** e a **saída** para a atividade é definida para **OutputBlobTable**.
   * Na secção **TypeProperties,** o **SqlSource** é especificado como o tipo de **origem** e **blobSink** é especificado como o **tipo de pia**.
   * A consulta `select * from emp` SQL é especificada para a propriedade **sqlReaderQuery** de **SqlSource**.

   Tanto o datetime de início como de fim têm de estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O tempo **end** é opcional, mas iremos utilizá-lo neste tutorial.

   Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9/9/9999** como o valor da propriedade **end**.

   Está a definir a duração do tempo em que as fatias de dados são processadas com base nas propriedades **de Disponibilidade** que foram definidas para cada conjunto de dados da Azure Data Factory.

   Existem 24 setores de dados no exemplo, uma vez que estes são produzidos de hora a hora.        
3. Clique em **implementar** na barra de comando para implementar o conjunto de dados (a tabela é um conjunto de dados retangular). Confirme que o oleoduto aparece na vista da árvore sob o nó de **Pipelines.**  
4. Agora, clique em **X** duas vezes para fechar a página para voltar à página **data Factory** para o **ADFTutorialOnPremDF**.

**Parabéns!** Criou com sucesso uma fábrica de dados Azure, serviços ligados, conjuntos de dados e um oleoduto e programado o oleoduto.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Ver a fábrica de dados numa Vista de Diagrama
1. No **portal Azure,** clique em azulejo **diagrama** na página inicial da fábrica de dados **ADFTutorialOnPremDF.** :

    ![Ligação do diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Deverá ver um diagrama semelhante à seguinte imagem:

    ![Vista de Diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Pode ampliar, ampliar, zoom até 100%, zoom para encaixar, posicionar automaticamente os oleodutos e conjuntos de dados, e mostrar informações de linhagem (destaques a montante e a jusante de itens selecionados).  Pode clicar duas vezes num objeto (conjunto de dados de entrada/saída ou pipeline) para ver propriedades para o mesmo.

## <a name="monitor-pipeline"></a>Monitorizar o pipeline
Neste passo, irá utilizar o portal do Azure para monitorizar os acontecimentos de uma fábrica de dados do Azure. Também pode utilizar os cmdlets PowerShell para monitorizar os conjuntos de dados e pipelines. Para mais detalhes sobre a monitorização, consulte [monitor e gerenciamento de gasodutos](data-factory-monitor-manage-pipelines.md).

1. No diagrama, clique duas vezes **em EmpOnPremSQLTable**.  

    ![Fatias empOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Note que todos os dados cortados estão em estado **pronto** porque a duração do gasoduto (hora de início para o fim) é no passado. É também porque inseriu os dados na base de dados do SQL Server e está sempre lá. Confirme que não aparecem fatias na secção de **fatias problemáticas** na parte inferior. Para ver todas as fatias, clique em **Ver Mais** na parte inferior da lista de fatias.
3. Agora, na página **Datasets,** clique em **OutputBlobTable**.

    ![Fatias oputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Clique em qualquer fatia de dados da lista e deverá ver a página **Data Slice.** Vê-se que a atividade corre para a fatia. Normalmente só se vê uma atividade.  

    ![Lâmina de fatia de dados](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Se o setor não estiver no estado **Pronto**, pode ver os setores a montante que não estão no estado Pronto e estão a impedir a execução do setor atual na lista **Setores a montante que não estão prontos**.
5. Clique na **execução** da atividade a partir da lista na parte inferior para ver detalhes de **execução**de atividade .

   ![Página de Detalhes de Execução de Atividades](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Veria informações como a entrada, a duração e o portal utilizado para transferir os dados.
6. Clique em **X** para fechar todas as páginas até que
7. voltar à página inicial do **ADFTutorialOnPremDF**.
8. (opcional) Clique em **Pipelines,** clique em **ADFTutorialOnPremDF**e faça a broca através de tabelas de entrada **(consumidas**) ou de conjuntos de dados de saída **(Produzidos).**
9. Utilize ferramentas como o [Microsoft Storage Explorer](https://storageexplorer.com/) para verificar se é criada uma bolha/ficheiro durante cada hora.

   ![Explorador do Storage do Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Passos seguintes
* Consulte o artigo Gateway de [Gestão](data-factory-data-management-gateway.md) de Dados para obter todos os detalhes sobre o Gateway de Gestão de Dados.
* Consulte [os dados do Copy de Azure Blob para o Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para saber como utilizar a Copy Activity para mover dados de uma loja de dados de origem para uma loja de dados afundada.
