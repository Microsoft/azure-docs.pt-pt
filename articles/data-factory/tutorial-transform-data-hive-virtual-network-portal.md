---
title: Transformar dados com o Hive na Rede Virtual do Azure | Microsoft Docs
description: Este tutorial fornece instruções passo a passo para transformar dados através da atividade do Hive no Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/04/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 9cea3e7494ee81638923cbcaff9f1b82d08a1ad1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58085036"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Transformar dados na Rede Virtual do Azure com a atividade do Hive no Azure Data Factory
Neste tutorial, vai utilizar o portal do Azure para criar um pipeline do Data Factory que transforma os dados com a Atividade do Hive num cluster HDInsight que se encontra numa Rede Virtual do Azure (VNet). Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar um integration runtime autoalojado
> * Criar os serviços ligados Armazenamento do Azure e Azure HDInsight
> * Criar um pipeline com a atividade Hive.
> * Acionar uma execução de pipeline.
> * Monitorizar a execução do pipeline. 
> * Verificar a saída

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Conta de Armazenamento do Azure**. Tem de criar um script do Hive e carregá-lo para o armazenamento do Azure. A saída do script do Hive é armazenada nesta conta de armazenamento. Neste exemplo, o cluster HDInsight utiliza esta conta de Armazenamento do Azure como armazenamento primário. 
- **Rede Virtual do Azure.** Se não tiver uma rede virtual do Azure, crie-a seguindo [estas instruções](../virtual-network/quick-create-portal.md). Neste exemplo, o HDInsight está numa Rede Virtual do Azure. Eis um exemplo de configuração da Rede Virtual do Azure. 

    ![Criar a rede virtual](media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png)
- **Cluster HDInsight.** Criar um cluster do HDInsight e associe-o para a rede virtual que criou no passo anterior ao seguir este artigo: [Expandir HDInsight do Azure com uma rede Virtual do Azure](../hdinsight/hdinsight-extend-hadoop-virtual-network.md). Eis um exemplo de configuração do HDInsight numa rede virtual. 

    ![HDInsight numa rede virtual](media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png)
- **Azure PowerShell**. Siga as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-Az-ps).
- **Uma máquina virtual**. Crie uma máquina virtual do Azure e associe-a à mesma rede virtual que contém o cluster HDInsight. Para obter mais detalhes, veja [Como criar máquinas virtuais](../virtual-network/quick-create-portal.md#create-virtual-machines). 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Carregar o script do Hive para uma conta de Armazenamento de Blobs

1. Crie um ficheiro SQL do Hive com o nome **hivescript.hql** com o seguinte conteúdo:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. No Armazenamento de Blobs do Azure, crie um contentor com o nome **adftutorial**, caso ainda não exista.
3. Crie uma pasta com o nome **hiverscripts**.
4. Carregue o ficheiro **hivescript.hql** para a sub-pasta **hivescripts**.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).    
2. Clique em **Novo** no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png)
3. Na página **Nova fábrica de dados**, introduza **ADFTutorialHiveFactory** no **nome**. 
      
     ![Página Nova fábrica de dados](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se receber o seguinte erro, altere o nome da fábrica de dados (por exemplo, oseunomeMyAzureSsisDataFactory) e tente criá-la novamente. Veja o artigo [Data Factory – Naming Rules](naming-rules.md) (Data Factory – Regras de Nomenclatura) para obter as regras de nomenclatura dos artefactos do Data Factory.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Selecione a sua subscrição do **Azure** na qual pretende criar a fábrica de dados. 
4. No **Grupo de Recursos**, siga um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
   - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
     Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2** para a **versão**.
5. Selecione a **localização** da fábrica de dados. Só são mostradas na lista as localizações que são suportadas para a criação de fábricas de dados.
6. Selecione **Afixar ao dashboard**.     
7. Clique em **Criar**.
8. No dashboard, verá o mosaico seguinte com o estado: **Implementar fábrica de dados**. 

     ![Mosaico “implementar a fábrica de dados”](media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png)
9. Depois de concluída a criação, vai ver a página **Data Factory**, conforme mostrado na imagem.
   
    ![Home page da fábrica de dados](./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png)
10. Clique em **Criar e Monitorizar** para iniciar a interface de utilizador (IU) do Data Factory num separador à parte.
11. Na página **introdução**, mude para o separador **Editar**, no painel do lado esquerdo, conforme mostrado na imagem abaixo: 

    ![Separador Editar](./media/tutorial-transform-data-using-hive-in-vnet-portal/get-started-page.png)

## <a name="create-a-self-hosted-integration-runtime"></a>Criar um integration runtime autoalojado
Uma vez que o cluster do Hadoop está dentro de uma rede virtual, tem de instalar um runtime de integração (IR) autoalojado nessa rede virtual. Nesta secção, vai criar uma VM nova, associá-la à mesma rede virtual e instalar o IR autoalojado nesta. O IR autoalojado permite ao serviço Data Factory enviar pedidos de processamento para um serviço de computação, como o HDInsight, dentro de uma rede virtual. Também lhe permite mover dados de e para os arquivos de dados que estejam dentro de uma rede virtual do Azure. Utilize um IR autoalojado quando o arquivo de dados ou a computação também está num ambiente no local. 

1. Na IU do Azure Data Factory, clique em **Ligações**, na parte inferior da janela, mude para o separador **Runtimes de Integração** e clique no botão **+ Novo**, na barra de ferramentas. 

   ![Menu novo runtime de integração](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png)
2. Na janela **Configuração do Runtime de Integração**, selecione a opção **Realizar movimento de dados e enviar atividades para computações externas** e clique em **Seguinte**. 

   ![Selecionar a opção Realizar movimento de dados e enviar atividades](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png)
3. Selecione **Rede Privada** e clique em **Seguinte**.
    
   ![Selecionar rede privada](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png)
4. Introduza **MySelfHostedIR** em **Nome** e clique em **Seguinte**. 

   ![Especificar o nome do runtime de integração](./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png) 
5. Copie a **chave de autenticação** do runtime de integração, clicando no botão para copiar, e guarde-a. Mantenha a janela aberta. Esta chave vai ser utilizada para registar o IR instalado numa máquina virtual. 

   ![Copiar a chave de autenticação](./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png)

### <a name="install-ir-on-a-virtual-machine"></a>Instalar o IR numa máquina virtual

1. Na VM do Azure, transfira o [integration runtime autoalojado](https://www.microsoft.com/download/details.aspx?id=39717). Utilize a **chave de Autenticação** que obteve no passo anterior para registar manualmente o runtime de integração autoalojado. 

    ![Registar o integration runtime](media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png)

2. Quando o integration runtime autoalojado for registado com êxito, verá a mensagem seguinte. 
   
    ![Registado com êxito](media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png)
3. Clique em **Iniciar Configuration Manager**. Quando o nó for ligado ao serviço cloud, verá a página seguinte: 
   
    ![O nó está ligado](media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png)

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>IR autoalojado na IU do Azure Data Factory

1. Na **IU do Azure Data Factory**, deverá ver o nome da VM autoalojada e o respetivo estado.

   ![Nós autoalojados existentes](./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png)
2. Clique em **Concluir** para fechar a janela **Configuração do Runtime de Integração**. Verá o IR autoalojado na lista de runtimes de integração.

   ![IR autoalojado na lista](./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png)


## <a name="create-linked-services"></a>Criar serviços ligados

Nesta secção, vai criar e implementar dois Serviços Ligados:
- Um **Serviço Ligado do Armazenamento do Azure** que liga uma conta de Armazenamento do Azure à fábrica de dados. Este armazenamento é o armazenamento primário utilizado pelo cluster HDInsight. Neste caso, vai utilizar esta conta de Armazenamento do Azure para guardar o script do Hive e a saída do script.
- Um **Serviço Ligado do HDInsight**. O Azure Data Factory submete o script do Hive a este cluster HDInsight para execução.

### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure

1. Mude para o separador **Serviços Ligados** e clique em **Novo**.

   ![Botão Novo serviço ligado](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. Na janela **Novo Serviço Ligado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**. 

   ![Selecionar Armazenamento de Blobs do Azure](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png)
3. Na janela **Novo Serviço Ligado**, siga os passos abaixo:

    1. Introduza **AzureStorageLinkedService** em **Nome**.
    2. Selecione **MySelfHostedIR** em **Ligar através do runtime de integração**.
    3. Selecione a sua conta de Armazenamento do Azure em **Nome da conta de armazenamento**. 
    4. Para testar a ligação à conta de armazenamento, clique em **Testar ligação**.
    5. Clique em **Guardar**.
   
        ![Especificar a conta de Armazenamento de Blobs do Azure](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png)

### <a name="create-hdinsight-linked-service"></a>Criar serviço ligado do HDInsight

1. Clique novamente em **novo** para criar outro serviço ligado. 
    
   ![Botão Novo serviço ligado](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. Mude para o separador **Computação**, selecione **Azure HDInsight** e clique em **Continuar**.

    ![Selecione Azure HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png)
3. Na janela **Novo Serviço Ligado**, siga os passos abaixo:

    1. Introduza **AzureHDInsightLinkedService** em **Nome**.
    2. Selecione **Trazer o seu próprio HDInsight**. 
    3. Selecione o cluster do HDInsight em **Cluster do Hdi**. 
    4. Introduza o **nome de utilizador** do cluster do HDInsight.
    5. Introduza a **palavra-passe** do utilizador. 
    
        ![Definições do Azure HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png)

Este artigo pressupõe que tem acesso ao cluster através da Internet. Por exemplo, que pode ligar ao cluster em `https://clustername.azurehdinsight.net`. Este endereço utiliza o gateway público, que não está disponível se tiver utilizado grupos de segurança de rede (NSGs) ou rotas definidas pelo utilizador (UDRs) para restringir o acesso a partir da Internet. Para que o Data Factory consiga submeter tarefas ao cluster HDInsight na Rede Virtual do Azure, precisa de configurar a Rede Virtual do Azure de forma a que o URL possa ser resolvido para o endereço IP privado do gateway utilizado pelo HDInsight.

1. A partir do portal do Azure, abra a Rede Virtual onde o HDInsight se encontra. Abra a interface de rede com o nome começado por `nic-gateway-0`. Tome nota do endereço IP privado. Por exemplo, 10.6.0.15. 
2. Se a sua Rede Virtual do Azure tiver um servidor DNS, atualize o registo DNS de forma a que o URL do cluster HDInsight `https://<clustername>.azurehdinsight.net` possa ser resolvido para `10.6.0.15`. Se não tiver um servidor DNS na sua Rede Virtual do Azure, pode solucionar temporariamente este problema ao editar o ficheiro de anfitriões (C:\Windows\System32\drivers\etc) de todas as VMs registadas como nós de runtime de integração autoalojado, adicionando uma entrada semelhante a: 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>Criar um pipeline 
Neste passo, vai criar um novo pipeline com uma atividade do Hive. A atividade executa o script do Hive para devolver dados de uma tabela de exemplo e guardá-los no caminho que definiu.

Tenha em atenção os seguintes pontos:

- **scriptPath** aponta para o caminho do script do Hive na Conta de Armazenamento do Azure que utilizou para MyStorageLinkedService. O caminho é sensível a maiúsculas e minúsculas.
- **Output** é um argumento utilizado no script do Hive. Utilize o formato `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` para apontá-lo para uma pasta existente no seu Armazenamento do Azure. O caminho é sensível a maiúsculas e minúsculas. 

1. Na IU do Data Factory, clique em **+ (mais)**, no painel do lado esquerdo, e clique em **Pipeline**. 

    ![Menu Novo pipeline](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png)
2. Na caixa de ferramentas **Atividades**, expanda **HDInsight** e arraste e largue a atividade **Hive** na superfície de desenho do pipeline. 

    ![Arraste e largar a atividade Hive](./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png)
3. Na janela de propriedades, mude para o separador **Cluster do HDI** e selecione **AzureHDInsightLinkedService** em **Serviço Ligado do HDInsight**.

    ![Selecionar serviço ligado do HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png)
4. Mude para o separador **Scripts** e siga os passos abaixo: 

    1. Selecione **AzureStorageLinkedService** em **Serviço Ligado de Script**. 
    2. Em **Caminho do Ficheiro**, clique em **Procurar no Armazenamento**. 
 
        ![Procurar no armazenamento](./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png)
    3. Na janela **Escolher um ficheiro ou pasta**, navegue para a pasta **hivescripts** do contentor **adftutorial**, selecione **hivescript.hql** e clique em **Concluir**.  
        
        ![Escolher ficheiro ou pasta](./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png) 
    4. Confirme que vê **adftutorial/hivescripts/hivescript.hql** em **Caminho do Ficheiro**.

        ![Definições do script](./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png)
    5. No separador **Script**, expanda a secção **Avançadas**. 
    6. Clique em **Preencher automaticamente a partir do script** em **Parâmetros**. 
    7. Introduza o valor para o parâmetro **Saída** no formato `wasb://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`. Por exemplo: `wasb://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`.
 
        ![Argumentos do script](./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png)
1. Para publicar os artefactos no Data Factory, clique em **Publicar**.

    ![Publicar](./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Acionar uma execução de pipeline

1. Em primeiro lugar, clique no botão **Validar**, na barra de ferramentas, para validar o pipeline. Clique na **seta para a direita (>>)** para fechar a **Saída da Validação do Pipeline**. 

    ![Validar o pipeline](./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png) 
2. Para acionar uma execução de pipeline, clique em Acionar, na barra de ferramentas, e clique em Acionar Agora. 

    ![Acionar agora](./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Mude para o separador **Monitorizar**, no lado esquerdo. Verá uma execução de pipeline na lista **Execuções de Pipeline**. 

    ![Monitorizar execuções de pipeline](./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png)
2. Para atualizar a lista, clique em **Atualizar**.
4. Para ver as execuções de atividades associadas às execução de pipeline, clique em **Ver Execuções de Atividades**, na coluna **Ação**. As outras ligações de ação servem para parar/voltar a executar o pipeline. 

    ![Ver execuções de atividades](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png)
5. Vai ver apenas uma execução de atividade, porque só existe uma atividade no pipeline do tipo **HDInsightHive**. Para regressar à vista anterior, clique na ligação **Pipelines**, na parte superior.

    ![Execuções de atividade](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png)
6. Confirme que vê um ficheiro de saída em **outputfolder** do contentor **adftutorial**. 

    ![Ficheiro de saída](./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png)

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, executou os passos seguintes: 

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar um integration runtime autoalojado
> * Criar os serviços ligados Armazenamento do Azure e Azure HDInsight
> * Criar um pipeline com a atividade Hive.
> * Acionar uma execução de pipeline.
> * Monitorizar a execução do pipeline. 
> * Verificar a saída

Avance para o tutorial seguinte para saber como transformar dados através de um cluster do Spark no Azure:

> [!div class="nextstepaction"]
>[Ramificação e encadeamento de um fluxo de controlo no Data Factory](tutorial-control-flow-portal.md)



