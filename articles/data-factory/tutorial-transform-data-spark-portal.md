---
title: Transformar dados com o Spark no Azure Data Factory | Microsoft Docs
description: Este tutorial fornece instruções passo a passo para transformar dados através de uma atividade do Spark no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/10/2018
ms.author: douglasl
ms.openlocfilehash: 0f8b2d3040a6fb5aeaa03a9508fbf17393152a9f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449534"
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Transformar dados na cloud através de uma atividade do Spark no Azure Data Factory
Neste tutorial, irá utilizar o portal do Azure para criar um pipeline do Azure Data Factory. Este pipeline transforma dados através de uma atividade do Spark e de um serviço ligado do Azure HDInsight a pedido. 

Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar um pipeline que utiliza uma atividade do Spark.
> * Acionar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Conta de armazenamento do Azure**. Tem de criar um script Python e um ficheiro de entrada, e carregá-los para o Armazenamento do Azure. A saída do programa Spark é armazenada nesta conta de armazenamento. O cluster do Spark a pedido utiliza a mesma conta de armazenamento como o respetivo armazenamento primário.  

> [!NOTE]
> O HdInsight suporta apenas contas de armazenamento para fins gerais com o escalão standard. Confirme que a conta não é uma conta de armazenamento apenas de blobs ou premium.

* **Azure PowerShell**. Siga as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-Az-ps).


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Carregar o script Python para a conta de armazenamento de Blobs
1. Crie um ficheiro Python com o nome **WordCount_Spark.py** com o seguinte conteúdo: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
1. Substitua *&lt;storageAccountName&gt;* pelo nome da sua conta de armazenamento do Azure. Em seguida, guarde o ficheiro. 
1. No armazenamento de Blobs do Azure, crie um contentor com o nome **adftutorial**, caso ainda não exista. 
1. Crie uma pasta com o nome **spark**.
1. Crie uma subpasta com o nome **script** na pasta **spark**. 
1. Carregue o ficheiro **WordCount_Spark.py** para a subpasta **script**. 


### <a name="upload-the-input-file"></a>Carregue o ficheiro de entrada
1. Crie um ficheiro com o nome **minecraftstory.txt** com algum texto. O programa Spark conta o número de palavras neste texto. 
1. Crie uma subpasta com o nome **inputfiles** na pasta **spark**. 
1. Carregue o ficheiro **minecraftstory.txt** para a subpasta **inputfiles**. 

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. Selecione **Novo** no menu da esquerda, selecione **Dados + Análise** e, em seguida, selecione **Data Factory**. 
   
   ![Seleção do Data Factory no painel "Novo"](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
1. No painel **Nova fábrica de dados**, introduza **ADFTutorialDataFactory** em **Nome**. 
      
   ![Painel "Nova fábrica de dados"](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se vir o seguinte erro, altere o nome da fábrica de dados. (Por exemplo, utilize **&lt;oseunome&gt;ADFTutorialDataFactory**). Para regras de nomenclatura de artefactos do Data Factory, veja o artigo [Data Factory – Regras de Nomenclatura](naming-rules.md).
  
   ![Erro quando um nome não está disponível](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
1. Em **Subscrição**, selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados. 
1. Em **Grupo de Recursos**, efetue um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
   - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
   Alguns dos passos deste início rápido pressupõem que utiliza o nome **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
1. Em **Versão**, selecione **V2**.
1. Em **Localização**, selecione a localização para a fábrica de dados. 

   Para obter uma lista de regiões do Azure em que a fábrica de dados está atualmente disponível, selecione as regiões que lhe interessam, na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (como o Armazenamento do Azure e a Base de Dados SQL do Azure) e as computações (como o HDInsight) utilizados pelo Data Factory podem estar noutras regiões.

1. Selecione **Criar**.

1. Após concluir a criação, verá a página **Fábrica de dados**. Selecione o mosaico **Criar e Monitorizar** para iniciar a aplicação de IU do Azure Data Factory num separador à parte.

    ![Home page da fábrica de dados, com o mosaico "Criar e Monitorizar"](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)

## <a name="create-linked-services"></a>Criar serviços ligados
Nesta secção, vai criar dois serviços ligados: 
    
- Um **serviço ligado do Armazenamento do Azure** que liga uma conta de armazenamento do Azure à fábrica de dados. Este armazenamento é utilizado pelo cluster do HDInsight a pedido. Também contém o script Spark que vai ser executado. 
- Um **serviço ligado do HDInsight a pedido**. O Azure Data Factory cria automaticamente um cluster do HDInsight e executa o programa Spark. Em seguida, elimina o cluster do HDInsight depois de o cluster estar inativo durante um período de tempo pré-configurado. 

### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure

1. Na página **Introdução**, mude para o separador **Editar**, no painel esquerdo. 

   ![Página “Vamos começar”](./media/tutorial-transform-data-spark-portal/get-started-page.png)

1. Selecione **Ligações**, na parte inferior da janela, e selecione **+ Novo**. 

   ![Botões para criar uma nova ligação](./media/tutorial-transform-data-spark-portal/new-connection.png)
1. Na janela **Novo Serviço Ligado**, selecione **Arquivo de Dados** > **Armazenamento de Blobs do Azure** e, em seguida, **Continuar**. 

   ![Selecionar o mosaico “Armazenamento de Blobs do Azure”](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
1. Para **Nome da conta de armazenamento**, selecione o nome na lista e, em seguida, selecione **Guardar**. 

   ![Caixa para especificar o nome da conta de armazenamento](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Crie um serviço ligado do HDInsight a pedido

1. Selecione o botão **+ Novo** novamente para criar outro serviço ligado. 
1. Na janela **Novo Serviço Ligado**, selecione **Computação** > **Azure HDInsight** e, em seguida, **Continuar**. 

   ![Selecionar o mosaico "Azure HDInsight"](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
1. Na janela **Novo Serviço Ligado**, conclua os passos abaixo: 

   a. Para **Nome**, introduza **AzureHDInsightLinkedService**.
   
   b. Para **Tipo**, confirme se está selecionado **HDInsight a pedido**.
   
   c. Para **Serviço Ligado do Armazenamento do Azure**, selecione **AzureStorage1**. Criou este serviço ligado anteriormente. Se utilizou um nome diferente, especifique o nome correto aqui. 
   
   d. Para **Tipo de cluster**, selecione **spark**.
   
   e. Para **ID do principal de serviço**, introduza o ID do principal de serviço que tem permissão para criar um cluster do HDInsight. 
   
      Este principal de serviço tem de ser membro da função de Contribuinte da subscrição ou do grupo de recursos no qual o cluster é criado. Para obter mais informações, veja [Criar uma aplicação e um principal de serviço do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md).
   
   f. Para **Chave do principal de serviço**, introduza a chave. 
   
   g. Para **Grupo de recursos**, selecione o mesmo grupo de recursos que utilizou ao criar a fábrica de dados. O cluster do Spark é criado neste grupo de recursos. 
   
   h. Expandir **tipo de SO**.
   
   i. Introduza um nome para o **Nome do utilizador do cluster**. 
   
   j. Introduza a **Palavra-passe do cluster** do utilizador. 
   
   k. Selecione **Concluir**. 

   ![Definições do serviço ligado do HDInsight](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> O Azure HDInsight limita o número total de núcleos que pode utilizar em cada região do Azure que suporta. Para o serviço ligado do HDInsight a pedido, o cluster do HDInsight é criado na mesma localização do Armazenamento do Azure utilizado como armazenamento principal. Certifique-se de que tem um número suficiente de quotas de núcleo para o cluster ser criado com êxito. Para obter mais informações, veja [Configurar clusters no HDInsight com o Hadoop, Spark, Kafka e muito mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione o botão **+** (mais) e, em seguida, selecione **Pipeline** no menu.

   ![Botões para criar um novo pipeline](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
1. Na caixa de ferramentas **Atividades**, expanda **HDInsight**. Arraste a atividade do **Spark** da caixa de ferramentas **Atividades** para a superfície de desenho do pipeline. 

   ![Arrastar a atividade do Spark](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
1. Nas propriedades da janela de atividade do **Spark** na parte inferior, conclua os seguintes passos: 

   a. Mude para o separador **HDI Cluster**.
   
   b. Selecione **AzureHDInsightLinkedService**, (criado no procedimento anterior). 
        
   ![Especificar um serviço ligado do HDInsight](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
1. Mude para o separador **Script/Jar** e conclua os seguintes passos: 

   a. Para **Serviço Ligado da Tarefa**, selecione **AzureStorage1**.
   
   b. Selecione **Procurar no Armazenamento**.

   ![Especificar o script Spark no separador "Script/Jar"](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
   
   c. Navegue para a pasta **adftutorial/spark/script**, selecione **WordCount_Spark.py** e, em seguida, selecione **Concluir**.      

1. Para validar o pipeline, selecione o botão **Validar** na barra de ferramentas. Selecione o botão **>>** (seta para a direita) para fechar a janela de validação. 
    
   ![Botão "Validar"](./media/tutorial-transform-data-spark-portal/validate-button.png)
1. Selecione **Publicar Tudo**. A IU do Data Factory publica as entidades (serviços ligados e pipeline) no serviço Azure Data Factory. 
    
   ![Botão "Publicar Tudo"](./media/tutorial-transform-data-spark-portal/publish-button.png)


## <a name="trigger-a-pipeline-run"></a>Acionar uma execução de pipeline
Selecione **Acionar** na barra de ferramentas e, em seguida, selecione **Acionar Agora**. 

![Botões "Acionar" e "Acionar Agora"](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Mude para o separador **Monitorizar**. Confirme que vê uma execução de pipeline. Demora aproximadamente de 20 minutos para criar um cluster do Spark. 
   
1. Selecione **Atualizar** periodicamente para verificar o estado da execução do pipeline. 

   ![Separador para monitorizar as execuções do pipeline, com o botão "Atualizar"](./media/tutorial-transform-data-spark-portal/monitor-tab.png)

1. Para ver as execuções de atividades associadas à execução do pipeline, selecione **Ver Execuções de Atividades**, na coluna **Ações**.

   ![Estado da execução do pipeline](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png) 

   Pode selecionar a ligação **Pipelines**, na parte superior, para regressar à vista de execuções do pipeline.

   ![Vista "Execuções de Atividades"](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verificar a saída
Verifique se o ficheiro de saída é criado na pasta spark/otuputfiles/wordcount do contentor adftutorial. 

![Localização do ficheiro de saída](./media/tutorial-transform-data-spark-portal/verity-output.png)

O ficheiro deve ter cada palavra do ficheiro de texto de entrada e o número de vezes que a palavra apareceu no ficheiro. Por exemplo: 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Passos Seguintes
O pipeline, neste exemplo, transforma dados através de uma atividade do Spark e de um serviço ligado do HDInsight a pedido. Aprendeu a: 

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar um pipeline que utiliza uma atividade do Spark.
> * Acionar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

Para aprender a transformar dados ao executar o script Hive num cluster do Azure HDInsight numa rede virtual, avance para o tutorial seguinte: 

> [!div class="nextstepaction"]
> [Tutorial: Transformar dados com o Hive na rede Virtual do Azure](tutorial-transform-data-hive-virtual-network-portal.md).





