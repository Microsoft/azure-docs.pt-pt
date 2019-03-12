---
title: Criar a primeira fábrica de dados (Visual Studio) | Microsoft Docs
description: Neste tutorial, irá criar um exemplo de pipeline do Azure Data Factory com o Visual Studio.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 7398c0c9-7a03-4628-94b3-f2aaef4a72c5
ms.service: data-factory
ms.workload: data-services
ms.custom: vs-azure
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: d5100a3da9b00f5c9a1defee1e4b9280528fd2d8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543020"
---
# <a name="tutorial-create-a-data-factory-by-using-visual-studio"></a>Tutorial: Criar uma fábrica de dados com o Visual Studio
> [!div class="op_single_selector" title="Tools/SDKs"]
> * [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [início rápido: Criar uma fábrica de dados com o Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

Este tutorial mostra-lhe como criar uma fábrica de dados do Azure com o Visual Studio. Cria um projeto do Visual Studio com o modelo de projeto do Data Factory, define entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) no formato JSON e, em seguida, publica/implementa estas entidades para a cloud. 

O pipeline neste tutorial tem uma atividade: **Atividade do Hive do HDInsight**. Esta atividade executa um script de ramo de registo num cluster do Azure HDInsight que transforma os dados de entrada para produzir os dados de saída. O pipeline está agendado para ser executado uma vez por mês entre as horas de início e de fim especificadas. 

> [!NOTE]
> Este tutorial não mostra como copiar dados com o Azure Data Factory. Para obter um tutorial sobre como copiar dados com o Azure Data Factory, veja [Tutorial: Copiar dados de armazenamento de BLOBs para base de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Um pipeline pode ter mais de uma atividade. Além disso, pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra. Para obter mais informações, veja [scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) (agendamento e execução no Data Factory).


## <a name="walkthrough-create-and-publish-data-factory-entities"></a>Descrição Passo a Passo: Criar e publicar entidades do Data Factory
Veja a seguir os passos que deve executar como parte destas instruções:

1. Criar dois serviços ligados: **AzureStorageLinkedService1** e **HDInsightOnDemandLinkedService1**. 
   
    Neste tutorial, ambos os dados de entrada e de saída para a atividade de ramo de registo estão no mesmo Armazenamento de Blobs do Azure. Utiliza um cluster de HDInsight a pedido para processar os dados de entrada existentes para produzir os dados de saída. O cluster de HDInsight a pedido é criado automaticamente por si pelo Azure Data Factory no runtime quando os dados de entrada estão prontos para serem processados. Precisa de ligar os arquivos de dados ou computações à fábrica de dados para que o serviço do Data Factory se possa ligar aos mesmos no runtime. Por conseguinte, liga a Conta de Armazenamento do Azure à fábrica de dados com o AzureStorageLinkedService1 e liga um cluster do HDInsight a pedido com o HDInsightOnDemandLinkedService1. Ao publicar especifica o nome da fábrica de dados a ser criada ou uma fábrica de dados existente.  
2. Crie dois conjuntos de dados: **InputDataset** e **OutputDataset**, que representam os dados de entrada/saída armazenados no armazenamento de Blobs do Azure. 
   
    Estas definições do conjunto de dados referem-se ao serviço ligado de Armazenamento do Azure que criou no passo anterior. Para o InputDataset, especifique o contentor de blobs (adfgetstarted) e a pasta (inptutdata) que contém um blob com os dados de entrada. Para o OutputDataset, especifique o contentor de blobs (adfgetstarted) e a pasta (partitioneddata) que contém os dados de saída. Especifique também outras propriedades, tais como a estrutura, a disponibilidade e a política.
3. Crie um pipeline com o nome **MyFirstPipeline**. 
  
    Nestas instruções, o pipeline tem apenas uma atividade: **Atividade do Hive do HDInsight**. Esta atividade transforma os dados de entrada para produzir os dados de saída ao executar um script de ramo de registo num cluster do HDInsight a pedido. Para saber mais sobre a atividade de ramo de registo, consulte [Hive Activity (Atividade de Ramo de Registo)](data-factory-hive-activity.md) 
4. Crie uma fábrica de dados com o nome **DataFactoryUsingVS**. Implemente a fábrica de dados e todas as entidades do Data Factory (serviços ligados, tabelas e o pipeline).
5. Depois de publicar, utilize os painéis do portal do Azure e a Aplicação de Monitorização & Gestão para monitorizar o pipeline. 
  
### <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Leia o artigo [Descrição Geral do Tutorial](data-factory-build-your-first-pipeline.md) e conclua os passos de **pré-requisitos**. Também pode selecionar a opção **Overview and prerequisites (Descrição geral e pré-requisitos)** na lista pendente na parte superior para mudar para o artigo. Depois de concluir os pré-requisitos, regresse a este artigo ao selecionar a opção **Visual Studio** na lista pendente.
2. Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ao nível do grupo de recursos/subscrição.  
3. Tem de ter o seguinte instalado no computador:
   * Visual Studio 2013 ou Visual Studio 2015
   * Transfira o Azure SDK para o Visual Studio 2013 ou Visual Studio 2015. Navegue até à [Página de Transferências do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na secção **.NET**.
   * Transferir o plug-in mais recente do Azure Data Factory para Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Também pode atualizar o plug-in, efetuando os seguintes passos: No menu, clique em **ferramentas** -> **extensões e atualizações** -> **Online** -> **galeria do Visual Studio**  ->  **Ferramentas do Microsoft Azure Data Factory para Visual Studio** -> **atualização**.

Agora, vamos utilizar o Visual Studio para criar uma fábrica de dados do Azure.

### <a name="create-visual-studio-project"></a>Criar projeto do Visual Studio
1. Inicie o **Visual Studio 2013** ou **Visual Studio 2015**. Clique em **Ficheiro**, aponte para **Novo** e, em seguida, clique em **Projeto**. A caixa de diálogo **Novo Projeto** deve aparecer.  
2. Na caixa de diálogo **Novo Projeto**, selecione o modelo **DataFactory** e clique em **Projeto do Data Factory Vazio**.   

    ![Caixa de diálogo Novo Projeto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)
3. Introduza um **nome** para o projeto, a **localização** e um nome para a **solução** e, em seguida, clique em **OK**.

    ![Explorador de Soluções](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### <a name="create-linked-services"></a>Criar serviços ligados
Neste passo, vai criar dois serviços ligados: **O armazenamento do Azure** e **HDInsight a pedido**. 

O serviço ligado do Armazenamento do Azure liga a sua conta de Armazenamento de Azure à fábrica de dados, fornecendo as informações de ligação. O serviço do Data Factory utiliza a cadeia de ligação da definição de serviço ligado para ligar ao armazenamento do Azure no runtime. Este armazenamento contém dados de entrada e de saída para o pipeline, e o ficheiro de script de ramo de registo utilizado pela atividade de ramo de registo. 

Com o serviço ligado do HDInsight a pedido, o cluster do HDInsight é criado automaticamente no runtime quando os dados de entrada estão prontos para serem processados. O cluster é eliminado depois de ter sido processado e ficado inativo pelo período de tempo especificado. 

> [!NOTE]
> Cria uma fábrica de dados ao especificar o nome e as definições no momento da publicação da sua solução do Data Factory.

#### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
1. Clique com o botão direito do rato em **Serviços Ligados** no explorador de soluções, aponte para **Adicionar** e, em seguida, clique em **Novo Item**.      
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **Serviço Ligado do Storage do Azure** na lista e clique em **Adicionar**.
    ![Serviço Ligado do Armazenamento do Azure](./media/data-factory-build-your-first-pipeline-using-vs/new-azure-storage-linked-service.png)
3. Substitua `<accountname>` e `<accountkey>` pelo nome da sua conta de armazenamento do Azure e pela respetiva chave. Para saber como obter a sua chave de acesso ao armazenamento, veja as informações sobre como ver, copiar e regenerar as chaves de acesso de armazenamento em [Gerir a conta do Armazenamento](../../storage/common/storage-account-manage.md#access-keys).
    ![Serviço Ligado do Armazenamento do Azure](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)
4. Guarde o ficheiro **AzureStorageLinkedService1.json**.

#### <a name="create-azure-hdinsight-linked-service"></a>Criar o serviço ligado do Azure HDInsight
1. No **Explorador de Soluções**, clique com o botão direito do rato em **Serviços Ligados**, aponte para **Adicionar** e clique em **Novo Item**.
2. Selecione **Serviço Ligado do HDInsight a Pedido** e clique em **Adicionar**.
3. Substitua o **JSON** pelo seguinte JSON:

     ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
        "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService1"
            }
        }
    }
    ```

    A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

    Propriedade | Descrição
    -------- | ----------- 
    ClusterSize | Especifica o tamanho do cluster HDInsight Hadoop.
    TimeToLive | Especifica o tempo de inatividade do cluster do HDInsight, antes de ser eliminado.
    linkedServiceName | Especifica a conta de armazenamento que é utilizada para armazenar os registos que são gerados pelo cluster do HDInsight Hadoop. 

    > [!IMPORTANT]
    > O cluster do HDInsight cria um **contentor predefinido** no armazenamento de blobs especificado no JSON (linkedServiceName). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Este comportamento é propositado. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que é processado um setor, exceto se houver um cluster em direto (timeToLive). O cluster é eliminado automaticamente quando o processamento é concluído.
    > 
    > À medida que são processados mais setores, verá muitos contentores no armazenamento de blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: `adf<yourdatafactoryname>-<linkedservicename>-datetimestamp`. Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](https://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.

    Para obter mais informações sobre as propriedades JSON, consulte o artigo [Compute linked services (Serviços ligados de computação)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
4. Guarde o ficheiro **HDInsightOnDemandLinkedService1.json**.

### <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, irá criar conjuntos de dados para representar os dados de entrada e de saída do processamento do Hive. Estes conjuntos de dados referem-se ao **AzureStorageLinkedService1** que criou anteriormente neste tutorial. O serviço ligado aponta para uma Conta de armazenamento do Azure e os conjuntos de dados especificam um contentor, uma pasta, um nome de ficheiro no armazenamento que contém os dados de entrada e de saída.   

#### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
1. No **Explorador de Soluções**, clique com o botão direito do rato em **Tabelas**, aponte para **Adicionar** e clique em **Novo Item**.
2. Selecione **Blob do Azure** na lista, altere o nome do ficheiro para **InputDataSet.json** e clique em **Adicionar**.
3. Substitua o **JSON** no editor pelo seguinte fragmento JSON:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Este fragmento JSON define um conjunto de dados denominado **AzureBlobInput**, que representa os dados de entrada da atividade do ramo de registo no pipeline. Especifica que os dados de entrada estão localizados no contentor de blobs denominado `adfgetstarted` e na pasta denominada `inputdata`.

    A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

    Propriedade | Descrição |
    -------- | ----------- |
    tipo |O tipo de propriedade é definido para **AzureBlob**, uma vez que os dados estão contidos no Armazenamento de Blobs do Azure.
    linkedServiceName | Refere-se ao AzureStorageLinkedService1 que criou anteriormente.
    fileName |Esta propriedade é opcional. Se omitir esta propriedade, serão escolhidos todos os ficheiros em folderPath. Neste caso, apenas o input.log é processado.
    tipo | Os ficheiros de registo estão no formato de texto, pelo que vamos utilizar TextFormat. |
    columnDelimiter | As colunas nos ficheiros de registo são delimitadas por vírgula (`,`)
    frequência/intervalo | A frequência definida para o Mês, sendo o intervalo 1, o que significa que os setores de entrada estão disponíveis mensalmente.
    externo | Esta propriedade é definida como verdadeira se os dados de entrada para a atividade não forem gerados pelo pipeline. Esta propriedade só é especificada em conjuntos de dados de entrada. Para o conjunto de dados de entrada da primeira atividade, defina-a sempre como verdadeira.
4. Guarde o ficheiro **InputDataset.json**.

#### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Agora, irá criar o conjunto de dados de saída para representar os dados de saída armazenados no Armazenamento de blobs do Azure.

1. No **Explorador de Soluções**, clique com o botão direito do rato em **Tabelas**, aponte para **Adicionar** e clique em **Novo Item**.
2. Selecione **Blob do Azure** na lista, altere o nome do ficheiro para **OutputDataset.json** e clique em **Adicionar**.
3. Substitua o **JSON** no editor pelo seguinte JSON:
    
    ```json
    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            }
        }
    }
    ```
    O fragmento JSON define um conjunto de dados denominado **AzureBlobOutput**, que representa os dados de saída produzidos pela atividade do ramo de registo no pipeline. Especifica que os dados de saída são produzidos pela atividade do ramo de registo que se encontra no contentor de blobs denominado `adfgetstarted` e na pasta denominada `partitioneddata`. 
    
    A secção **disponibilidade** especifica que o conjunto de dados de saída é produzido mensalmente. O conjunto de dados de saída controla a agenda do pipeline. O pipeline é executado mensalmente entre as horas de início e de fim. 

    Veja a secção **Criar o conjunto de dados de entrada** para obter as descrições destas propriedades. Não defina a propriedade externa num conjunto de dados de saída, uma vez que o conjunto de dados é produzido pelo pipeline.
4. Guarde o ficheiro **OutputDataset.json**.

### <a name="create-pipeline"></a>Criar pipeline
Criou com o serviço ligado de Armazenamento do Azure e conjuntos de dados de entrada e de saída até ao momento. Agora irá criar um pipeline com uma atividade **HDInsightHive**. A **entrada** da atividade do ramo de registo está definida como **AzureBlobInput** e a **saída** está definida como **AzureBlobOutput**. Um setor de um conjunto de dados de entrada está disponível mensalmente (frequência: Mês, intervalo: 1), e o setor de saída também é produzido mensalmente. 

1. No **Explorador de Soluções**, clique com o botão direito do rato em **Pipelines**, aponte para **Adicionar** e clique em **Novo Item.**
2. Selecione **Pipeline de Transformação do Hive** na lista e clique em **Adicionar**.
3. Substitua o **JSON** pelo seguinte fragmento:

    > [!IMPORTANT]
    > Substitua `<storageaccountname>` pelo nome de sua conta de armazenamento.

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService1",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    > [!IMPORTANT]
    > Substitua `<storageaccountname>` pelo nome de sua conta de armazenamento.

    O fragmento JSON define um pipeline que consiste numa única atividade (atividade de Ramo de Registo). Esta atividade executa um script de Ramo de Registo para processar dados de entrada num cluster do HDInsight a pedido para produzir os dados de saída. Na secção de atividades do pipeline JSON, verá apenas uma atividade na matriz com o tipo definido como **HDInsightHive**. 

    Nas propriedades do tipo que são específicas para a atividade do Ramo de Registo do HDInsight, tem de especificar o serviço ligado de Armazenamento do Azure que tem o ficheiro de script do ramo de registo, o caminho para o ficheiro de script e parâmetros para o ficheiro de script. 

    O ficheiro do script do Hive **partitionweblogs.hql** é armazenado na conta de armazenamento do Azure (especificado pelo scriptLinkedService) e na pasta `script` no contentor `adfgetstarted`.

    A secção `defines` é utilizada para especificar as definições do tempo de execução que são transmitidas ao script do Hive como valores de configuração do Hive (por exemplo, `${hiveconf:inputtable}` e `${hiveconf:partitionedtable})`).

    As propriedades **início** e **fim** do pipeline especificam o período ativo do pipeline. Configurou o conjunto de dados para ser produzido mensalmente, por conseguinte, é produzido apenas uma setor pelo pipeline (porque o mês é igual nas datas de início e de fim).

    No JSON de atividade, especifique que o Script de ramo de registo é executado na computação especificada pelo **linkedServiceName** – **HDInsightOnDemandLinkedService**.
4. Guarde o ficheiro **HiveActivity1.json**.

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>Adicione partitionweblogs.hql e input.log como uma dependência
1. Clique com o botão direito do rato em **Dependências** na janela **Explorador de Soluções**, aponte para **Adicionar** e clique em **Item Existente**.  
2. Aceda a **C:\ADFGettingStarted**, selecione os ficheiros **partitionweblogs.hql** e **input.log** e clique em **Adicionar**. Criou estes dois ficheiros como parte dos pré-requisitos da [Tutorial Overview (Descrição Geral do Tutorial)](data-factory-build-your-first-pipeline.md).

Quando publicar a solução no próximo passo, o ficheiro **partitionweblogs.hql** será carregado na pasta de **scripts** no contentor de blobs `adfgetstarted`.   

### <a name="publishdeploy-data-factory-entities"></a>Publicar/implementar entidades do Data Factory
Neste passo, publica as entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) no seu projeto para o serviço do Azure Data Factory. No processo de publicação, especifica o nome da sua fábrica de dados. 

1. Clique com o botão direito do rato no Explorador de Soluções e, em seguida, clique em **Publicar**.
2. Se vir a caixa de diálogo **Iniciar sessão na sua conta Microsoft**, introduza as credenciais da conta com a subscrição do Azure e clique em **iniciar sessão**.
3. Deverá ver a seguinte caixa de diálogo:

   ![Caixa de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
4. Na página **Configurar fábrica de dados**, execute os seguintes passos:

    ![Publicar - novas definições da fábrica de dados](media/data-factory-build-your-first-pipeline-using-vs/publish-new-data-factory.png)

   1. Selecione a opção **Criar Nova Fábrica de Dados**.
   2. Introduza um **nome** exclusivo para a fábrica de dados. Por exemplo: **DataFactoryUsingVS09152016**. O nome tem de ser globalmente exclusivo.
   3. Selecione a subscrição correta no campo **Subscrição**. 
        > [!IMPORTANT]
        > Se não vir nenhuma subscrição, verifique se iniciou sessão com uma conta de administrador ou coadministrador da subscrição.
   4. Selecione o **grupo de recursos** para criar a fábrica de dados.
   5. Selecione a **região** da fábrica de dados.
   6. Clique em **Seguinte** para mudar para a página **Publicar Itens**. (Prima **Tecla de Tabulação** para sair do campo Nome se o botão **Seguinte** estiver desativado.)

    > [!IMPORTANT]
    > Se receber o erro **Nome "DataFactoryUsingVS" não disponível para a fábrica de dados** aquando da publicação, altere o nome (por exemplo, seunomeDataFactoryUsingVS). Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.   
1. Na página **Publicar Itens**, confirme que todas as entidades dos Data Factories estão selecionadas e clique em **Seguinte** para mudar para a página **Resumo**.

    ![Página Publicar itens](media/data-factory-build-your-first-pipeline-using-vs/publish-items-page.png)     
2. Reveja o resumo e clique em **Seguinte** para iniciar o processo de implementação e ver o **Estado da Implementação**.

    ![Página de resumo](media/data-factory-build-your-first-pipeline-using-vs/summary-page.png)
3. Na página **Estado da Implementação**, deve ver o estado do processo de implementação. Clique em Concluir após a conclusão da implementação.

Pontos importantes para ter em atenção:

- Se receber o erro: **Esta subscrição não está registada para utilizar o espaço de nomes DataFactory**, efetue um dos seguintes procedimentos e tente publicar novamente:
    - No Azure PowerShell, execute o seguinte comando para registar o fornecedor do Data Factory.
        ```PowerShell   
        Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
        ```
        Pode executar o seguinte comando para confirmar que o fornecedor do Data Factory está registado.

        ```PowerShell
        Get-AzResourceProvider
        ```
    - Inicie sessão com a subscrição do Azure no [Portal do Azure](https://portal.azure.com) e navegue até um painel do Data Factory ou crie uma fábrica de dados no Portal do Azure. Esta ação regista automaticamente o fornecedor por si.
- O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.
- Para criar instâncias do Data Factory, tem de ser um administrador ou coadministrador da subscrição do Azure

### <a name="monitor-pipeline"></a>Monitorizar o pipeline
Neste passo, monitoriza o pipeline com a Vista de Diagrama da fábrica de dados. 

#### <a name="monitor-pipeline-using-diagram-view"></a>Monitorizar o pipeline com a Vista de Diagrama
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/) e execute os seguintes passos:
   1. Clique em **Mais serviços** e em **Fábricas de dados**.
       
        ![Procurar fábricas de dados](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png)
   2. Selecione o nome da sua fábrica de dados (por exemplo: **DataFactoryUsingVS09152016**) na lista de fábricas de dados.
   
       ![Selecionar a fábrica de dados](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
2. Na home page da fábrica de dados, clique em **Diagrama**.

    ![Mosaico do diagrama](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
3. Na Vista de Diagrama, verá uma descrição geral dos pipelines e dos conjuntos de dados utilizados neste tutorial.

    ![Vista de Diagrama](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png)
4. Para ver todas as atividades no pipeline, clique com o botão direito do rato no pipeline no diagrama e, em seguida, clique em Abrir Pipeline.

    ![Menu Abrir pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
5. Confirme se vê a atividade HDInsightHive no pipeline.

    ![Vista Abrir pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Para navegar de volta para a vista anterior, clique em **Fábrica de dados** no menu de navegação, na parte superior.
6. Na **Vista de Diagrama**, faça duplo clique no conjunto de dados **AzureBlobInput**. Verifique se o setor se encontra no estado **Pronto**. Poderá demorar alguns minutos até o setor aparecer no estado Pronto. Se tal não acontecer ao fim de um certo tempo, verifique se colocou o ficheiro de entrada (input.log) no contentor (`adfgetstarted`) e na pasta (`inputdata`) adequados. E certifique-se de que a propriedade **externa** no conjunto de dados de entrada está definida como **verdadeira**. 

   ![Setor de entrada no estado pronto](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
7. Clique em **X** para fechar o painel **AzureBlobInput**.
8. Na **Vista de Diagrama**, faça duplo clique no conjunto de dados **AzureBlobOutput**. Verá o setor que está atualmente a ser processado.

   ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Quando o processamento terminar, verá o setor no estado **Pronto**.

   > [!IMPORTANT]
   > A criação de um cluster do HDInsight a pedido demora, por norma, algum tempo (cerca de 20 minutos). Assim, prevê-se que o pipeline demore **aproximadamente 30 minutos** a processar o setor.  
   
    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)    
10. Quando o setor estiver no estado **Pronto**, verifique a pasta `partitioneddata` no contentor `adfgetstarted` do seu armazenamento de blobs para os dados de saída.  

    ![dados de saída](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. Clique no setor para ver os respetivos detalhes no painel **Setor de dados**.

    ![Detalhes do setor de dados](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Clique numa atividade em execução na **Lista de execuções de atividades** para ver detalhes sobre a execução de uma atividade (atividade do Hive no nosso cenário) numa janela **Detalhes de execução da atividade**. 
  
    ![Detalhes da execução da atividade](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)    

    Nos ficheiros de registo, pode ver a consulta do Hive que foi executada e as informações sobre o estado. Estes registos são úteis para resolver eventuais problemas.  

Consulte [Monitor datasets and pipeline (Monitorizar os conjuntos de dados e o pipeline)](data-factory-monitor-manage-pipelines.md) para obter instruções sobre como utilizar o Portal do Azure para monitorizar o pipeline e os conjuntos de dados que criou neste tutorial.

#### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorizar o pipeline com a Aplicação de Monitorização e Gestão
Pode ainda utilizar a aplicação de Monitorização e Gestão para monitorizar os seus pipelines. Para obter detalhes sobre a utilização desta aplicação, veja [Monitor and manage Azure Data Factory pipelines using Monitoring and Management App (Monitorizar e gerir pipelines do Azure Data Factory com a Aplicação de Monitorização e Gestão)](data-factory-monitor-manage-app.md).

1. Clique no mosaico Monitorizar e Gerir.

    ![Mosaico Monitorizar e Gerir](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png)
2. Deverá ver a aplicação de Monitorização e Gestão. Altere a **Hora de início** e a **Hora de fim** para corresponder às horas de início (01-04-2016 00:00) e de fim (02-04-2016 00:00) do seu pipeline e clique em **Aplicar**.

    ![Aplicação Monitorizar e Gerir](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png)
3. Para ver mais detalhes sobre uma janela de atividade, selecione-a na **lista Janelas de Atividade** para ver os respetivos detalhes.
    ![Detalhes da janela de atividade](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)

> [!IMPORTANT]
> O ficheiro de entrada é eliminado quando o setor é processado com êxito. Por conseguinte, se pretender voltar a executar o setor ou repetir o tutorial, carregue o ficheiro de entrada (input.log) na pasta `inputdata` do contentor `adfgetstarted`.

### <a name="additional-notes"></a>Notas adicionais
- Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Por exemplo, uma Atividade de Cópia para copiar dados de uma origem para um arquivo de dados de destino e uma Atividade Hive do HDInsight para executar um script de Hive para transformar dados de entrada. Veja [Arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) relativamente a todas as origens e sinks que a Atividade de Cópia suporta. Veja [Compute linked services (Serviços ligados de computação)](data-factory-compute-linked-services.md) para obter a lista dos serviços de computação que o Data Factory suporta.
- Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Veja [Arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) relativamente a todas as origens e sinks que a Atividade de Cópia suporta. Veja [serviços ligados de computação](data-factory-compute-linked-services.md) para obter a lista dos serviços de computação que o Data Factory suporta e as [atividades de transformação](data-factory-data-transformation-activities.md) que pode executar nos mesmos.
- Consulte [Move data from/to Azure Blob (Mover dados de/para Blob do Azure)](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter detalhes sobre as propriedades JSON utilizadas na definição de serviço ligada ao Armazenamento do Azure.
- Também pode utilizar o seu próprio cluster do HDInsight em vez de utilizar um cluster do HDInsight a pedido. Veja [Compute Linked Services (Serviços Ligados de Computação)](data-factory-compute-linked-services.md) para obter detalhes.
-  O Data Factory cria um cluster do HDInsight **baseado no Linux** com o JSON precedente. Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.
- O cluster do HDInsight cria um **contentor predefinido** no armazenamento de blobs especificado no JSON (linkedServiceName). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Este comportamento é propositado. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que é processado um setor, exceto se houver um cluster em direto (timeToLive). O cluster é eliminado automaticamente quando o processamento é concluído.
    
    À medida que são processados mais setores, verá muitos contentores no armazenamento de blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](https://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.
- Atualmente, o conjunto de dados de saída é o que pauta a agenda, pelo que deve criar um conjunto de dados de saída, mesmo que a atividade não produza dados. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. 
- Este tutorial não mostra como copiar dados com o Azure Data Factory. Para obter um tutorial sobre como copiar dados com o Azure Data Factory, veja [Tutorial: Copiar dados de armazenamento de BLOBs para base de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="use-server-explorer-to-view-data-factories"></a>Utilize o Explorador de Servidores para ver as fábricas de dados
1. No **Visual Studio**, clique em **Ver** no menu e, em seguida, clique em **Explorador de Servidores**.
2. Na janela do Explorador de Servidores, expanda **Azure** e **Data Factory**. Se vir **Iniciar Sessão no Visual Studio**, introduza a **conta** associada à sua subscrição do Azure e clique em **Continuar**. Introduza a **palavra-passe** e clique em **Iniciar sessão**. O Visual Studio tenta obter informações sobre todas as fábricas de dados do Azure na sua subscrição. Verá o estado desta operação na janela **Lista de Tarefas do Data Factory**.

    ![Explorador de Servidores](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Pode clicar com o botão direito do rato numa fábrica de dados e selecionar **Exportar Data Factory para Novo Projeto** para criar um projeto do Visual Studio com base numa fábrica de dados existente.

    ![Exportar a fábrica de dados](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## <a name="update-data-factory-tools-for-visual-studio"></a>Atualizar as ferramentas do Data Factory para Visual Studio
Para atualizar as ferramentas do Azure Data Factory para Visual Studio, realize os seguintes passos:

1. Clique em **Ferramentas** no menu e selecione **Extensões e Atualizações**.
2. Selecione **Atualizações** no painel esquerdo e, em seguida, selecione **Galeria do Visual Studio**.
3. Selecione **Ferramentas do Azure Data Factory para Visual Studio** e clique em **Atualizar**. Se não vir esta entrada, significa que já tem a versão mais recente das ferramentas.

## <a name="use-configuration-files"></a>Utilizar os ficheiros de configuração
Pode utilizar os ficheiros de configuração no Visual Studio para configurar as propriedades diferentes para os serviços ligados/tabelas/pipelines para cada ambiente.

Considere a seguinte definição JSON para um serviço ligado do Storage do Azure. Para especificar **connectionString** com outros valores para accountname e accountkey com base no ambiente (Programação/Teste/Produção) para o qual está a implementar entidades do Data Factory. Pode conseguir este comportamento, utilizando um ficheiro de configuração diferente para cada ambiente.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Adicionar um ficheiro de configuração
Adicione um ficheiro de configuração para cada ambiente realizando os seguintes passos:   

1. Com o botão direito do rato, clique no projeto do Data Factory na solução do Visual Studio, aponte para **Adicionar** e clique em **Novo item**.
2. Selecione **Configuração** na lista de modelos instalados à esquerda, selecione **Ficheiro de Configuração**, introduza um **nome** para o ficheiro de configuração e clique em **Adicionar**.

    ![Adicionar o ficheiro de configuração](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Adicione os parâmetros de configuração e os respetivos valores no seguinte formato:

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<Azure sql server name>.database.windows.net,1433;Database=<Azure Sql database>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    Este exemplo configura a propriedade connectionString de um serviço ligado do Storage do Azure e de um serviço ligado do Azure SQL. Tenha em atenção que a sintaxe para especificar o nome é [JsonPath](https://goessner.net/articles/JsonPath/).   

    Se JSON tiver uma propriedade com uma matriz de valores, conforme apresentado no seguinte código:  

    ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    Configure as propriedades conforme apresentado no seguinte ficheiro de configuração (utilize indexação baseada em zero):

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Nomes de propriedade com espaços
Se um nome de propriedade tiver espaços, utilize parênteses retos, conforme ilustrado no exemplo seguinte (nome do servidor da Base de dados):

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Implementar a solução com uma configuração
Quando publicar entidades do Azure Data Factory no VS, pode especificar a configuração que pretende utilizar para essa operação de publicação.

Para publicar entidades num projeto do Azure Data Factory com o ficheiro de configuração:   

1. Clique com o botão direito do rato no projeto do Data Factory e clique em **Publicar** para ver a caixa de diálogo **Publicar Itens**.
2. Selecione uma fábrica de dados existente ou especifique valores para criar uma fábrica de dados na página **Configurar fábrica de dados** e clique em **Seguinte**.   
3. Na página **Publicar Itens**: verá uma lista pendente com configurações disponíveis para o campo **Selecionar Configuração da Implementação**.

    ![Selecionar o ficheiro de configuração](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Selecione o **ficheiro de configuração** que pretende utilizar e clique em **Seguinte**.
5. Confirme se vê o nome do ficheiro JSON na página **Resumo** e clique em **Seguinte**.
6. Clique em **Concluir** depois de ter concluído a operação de implementação.

Aquando da implementação, os valores do ficheiro de configuração são utilizados para definir valores para as propriedades dos ficheiros JSON antes de as entidades serem implementadas no serviço do Azure Data Factory.   

## <a name="use-azure-key-vault"></a>Utilizar o Azure Key Vault
Não é aconselhável e frequentemente contra a política de segurança entregar dados confidenciais, como cadeias de ligação para o repositório de código. Consulte a amostra [Publicação Segura do ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFSecurePublish) no GitHub para saber mais sobre o armazenamento e utilização de informações confidenciais no Azure Key Vault ao publicar entidades do Data Factory. A extensão Publicação Segura para o Visual Studio permite que os segredos sejam armazenados no Key Vault e sejam especificadas apenas referências aos mesmos nas configurações de serviços/implementações ligados. Estas referências são convertidas ao publicar entidades do Data Factory no Azure. Estes ficheiros podem então ser consolidados para o repositório de origem, sem expor quaisquer segredos.

## <a name="summary"></a>Resumo
Neste tutorial, criou uma fábrica de dados do Azure para processar dados executando o Script de ramo de registo num cluster de hadoop do HDInsight. Utilizou o Editor do Data Factory no portal do Azure para realizar os seguintes passos:  

1. Criou uma **fábrica de dados** do Azure.
2. Criar dois **serviços ligados**:
   1. O serviço ligado do **Storage do Azure** para ligar o seu armazenamento de blobs do Azure que contém ficheiros de entrada/saída da fábrica de dados.
   2. O serviço ligado do **Azure HDInsight** a pedido para ligar um cluster de Hadoop do HDInsight a pedido à fábrica de dados. O Azure Data Factory cria um cluster de Hadoop do HDInsight para processar os dados de entrada e produzir dados de saída.
3. Criar dois **conjuntos de dados**, que descrevem os dados de entrada e de saída da atividade do HDInsight Hive no pipeline.
4. Criar um **pipeline** com uma atividade do **Ramo de Registo do HDInsight**.  

## <a name="next-steps"></a>Próximos Passos
Neste artigo, criou um pipeline com uma atividade de transformação (Atividade do HDInsight) que executa um Script de ramo de registo num cluster do HDInsight a pedido. Para ver como utilizar uma atividade de cópia para copiar dados de um Blob do Azure para SQL do Azure, consulte [Tutorial: Copiar dados de um Azure blob para o Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Consulte [Scheduling and execution in Data Factory (Agendamento e execução no Data Factory)](data-factory-scheduling-and-execution.md) para obter informações detalhadas. 


## <a name="see-also"></a>Consultar Também
| Tópico | Descrição |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e como os utilizar para construir fluxos de dados condicionados por dados para o seu cenário ou empresa. |
| [Conjuntos de dados](data-factory-create-datasets.md) |Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory. |
| [Atividades de Transformação de Dados](data-factory-data-transformation-activities.md) |Este artigo fornece uma lista de atividades de transformação de dados (por exemplo, a transformação do Ramo de registo do HDInsight que utilizou neste tutorial) suportada pelo Azure Data Factory. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. |
