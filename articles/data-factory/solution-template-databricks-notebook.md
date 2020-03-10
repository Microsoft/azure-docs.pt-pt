---
title: Transformação com tijolos de dados Azure
description: Aprenda a usar um modelo de solução para transformar dados utilizando um caderno databricks na Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: e771bc152ab50f907a8f2ad384e887c00d3f627a
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933944"
---
# <a name="transformation-with-azure-databricks"></a>Transformação com tijolos de dados Azure

Neste tutorial, cria-se um pipeline de ponta a ponta contendo **validação,** **copy**e atividades **de caderno** na Fábrica de Dados.

-   A atividade de **validação** é utilizada para garantir que o conjunto de dados de origem está pronto para consumo a jusante, antes de desencadear o trabalho de cópia e análise.

-   **Copiar** a atividade copia o ficheiro/conjunto de dados de origem para o armazenamento do lavatório. O armazenamento do lavatório é montado como DBFS no caderno Databricks para que o conjunto de dados possa ser diretamente consumido pela Spark.

-   A atividade do **Notebook Databricks** aciona o caderno Databricks que transforma o conjunto de dados e adiciona-o a uma pasta processada/ SQL DW.

Para manter este modelo simples, o modelo não cria um gatilho programado. Pode adicioná-lo se necessário.

![1](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Pré-requisitos

1. Crie uma conta de **armazenamento de bolhas** e um recipiente chamado `sinkdata` a ser usado como **pia**. Mantenha uma nota do nome da conta de **armazenamento,** nome do **recipiente,** e chave de **acesso,** uma vez que são referenciados posteriormente no modelo.

2. Certifique-se de que tem um espaço de **trabalho Azure Databricks** ou crie um novo.

3. **Importar o caderno para a transformação.** 
    1. Nos seus Databricks Azure, referência a imagens para importar um caderno **de transformação** para o espaço de trabalho databricks. Não tem de estar no mesmo local que abaixo, mas lembre-se do caminho que escolhe u seguir.
   
       ![2](media/solution-template-Databricks-notebook/import-notebook.png)    
    
    1. Selecione "Import from: **URL**", e introduza o seguinte URL na caixa de texto:
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/import-from-url.png)    

4. Agora vamos atualizar o caderno **Transformation** com as suas informações de ligação de armazenamento. Vá ao **comando 5** (como mostrado no código abaixo) no caderno importado acima, e substitua `<storage name>`e `<access key>` com as suas próprias informações de ligação de armazenamento. Certifique-se de que esta conta é a mesma conta de armazenamento criada anteriormente e contém o recipiente `sinkdata`.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

5.  Gere um símbolo de acesso de **Databricks** para data factory para aceder a Databricks. **Guarde o sinal** de acesso para posterior utilização na criação de um serviço ligado aos Databricks, que se parece com 'dapi32db32cbb4w6ee18b7d87e45exxxxxx'.

    ![4](media/solution-template-Databricks-notebook/user-setting.png)

    ![5](media/solution-template-Databricks-notebook/generate-new-token.png)

## <a name="how-to-use-this-template"></a>Como usar este modelo

1.  Ir para a Transformação com o modelo de Tijolos de **Dados Azure.** Criar novos serviços ligados para seguir ligações. 
    
    ![Definição de conexões](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  **Source Blob Connection** – para aceder aos dados de origem. 
        
        Pode utilizar o armazenamento de bolhas públicas contendo os ficheiros de origem para esta amostra. Imagem de referência para configuração. Utilize abaixo **o URL SAS** para ligar ao armazenamento de origem (acesso só para leitura): 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/source-blob-connection.png)

    1.  **Destination Blob Connection** – para copiar dados. 
        
        No serviço ligado à pia, selecione um armazenamento criado no **Pré-requisito** 1.

        ![7](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    1.  **Azure Databricks** – para a ligação ao cluster Databricks.

        Crie um serviço ligado aos Databricks utilizando a chave de acesso gerada no **Pré-requisito** 2.c. Se tiver um *cluster interativo,* pode selecionar isso. (Este exemplo utiliza a nova opção de *cluster de emprego.)*

        ![8](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Selecione **Utilize este modelo**e verá um pipeline criado como mostrado abaixo:
    
    ![Criar um pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>Introdução e configuração do gasoduto

No novo pipeline criado, a maioria das definições foram configuradas automaticamente com valores predefinidos. Confira as configurações e a atualização sempre que necessário para se adequar às suas próprias definições. Para mais detalhes, pode verificar abaixo instruções e imagens para referência.

1.  Uma **bandeira de disponibilidade** de atividade de validação é criada para fazer uma verificação de disponibilidade de origem. *SourceAvailabilityDataset* criado em etapa anterior é selecionado como Dataset.

    ![12](media/solution-template-Databricks-notebook/validation-settings.png)

1.  É criado um **ficheiro-a-bolha** de atividade de cópia para copiar o conjunto de dados de origem para afundar. Consulte as imagens abaixo para obter configurações de origem e pia na atividade da cópia.

    ![13](media/solution-template-Databricks-notebook/copy-source-settings.png)

    ![14](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1.  É criada uma **transformação** de atividade por Caderno e o serviço ligado criado em etapaanterior é selecionado.
    ![16](media/solution-template-Databricks-notebook/notebook-activity.png)

     1. Selecione o separador **Definições.** Para o *caminho do Caderno,* o modelo define um caminho por defeito. Poderá ser necessário navegar e selecionar o caminho de portátil correto enviado no **Pré-requisito** 2. 

         ![17](media/solution-template-Databricks-notebook/notebook-settings.png)
    
     1. Confira os *Parâmetros base criados* como mostrado na imagem. Devem ser passados para o caderno Databricks da Data Factory. 

         ![Parâmetros de base](media/solution-template-Databricks-notebook/base-parameters.png)

1.  **Os parâmetros do gasoduto** são definidos como abaixo.

    ![15](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Configuração de conjuntos de dados.
    1.  **SourceAvailabilityDataset** foi criado para verificar se os dados de origem estão disponíveis.

        ![9](media/solution-template-Databricks-notebook/source-availability-dataset.png)

    1.  **SourceFilesDataset** - para copiar os dados de origem.

        ![10](media/solution-template-Databricks-notebook/source-file-dataset.png)

    1.  **DestinationFilesDataset** – para copiar para o local de afundar/destino.

        1.  Serviço ligado - *sinkBlob_LS* criado em etapas anteriores.

        2.  Caminho de arquivo - *sinkdata/staged_sink*.

            ![11](media/solution-template-Databricks-notebook/destination-dataset.png)


1.  Selecione **Debug** para executar o gasoduto. Pode encontrar ligação com registos de Databricks para registos de Spark mais detalhados.

    ![18](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Também pode verificar o ficheiro de dados utilizando o explorador de armazenamento. (Para correlacionar com o pipeline Data Factory, este exemplo anexa o ID de execução do gasoduto da fábrica de dados para a pasta de saída. Desta forma, pode rastrear os ficheiros gerados através de cada execução.)

    ![19](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Azure Data Factory](introduction.md)
