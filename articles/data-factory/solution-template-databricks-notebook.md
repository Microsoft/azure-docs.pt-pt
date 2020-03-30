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
ms.openlocfilehash: 9a05b09f958d741fa56c586fbc7f5c5908dbbce6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384386"
---
# <a name="transformation-with-azure-databricks"></a>Transformação com tijolos de dados Azure

Neste tutorial, cria-se um pipeline de ponta a ponta que contém as atividades de **Validação,** **Cópia**e **Caderno** na Azure Data Factory.

- **A validação** garante que o seu conjunto de dados de origem está pronto para consumo a jusante antes de desencadear o trabalho de cópia e análise.

- **Os dados** de cópia duplicam o conjunto de dados de origem para o armazenamento do lavatório, que é montado como DBFS no caderno Azure Databricks. Desta forma, o conjunto de dados pode ser diretamente consumido pela Spark.

- **O notebook** aciona o caderno Databricks que transforma o conjunto de dados. Também adiciona o conjunto de dados a uma pasta processada ou ao Azure SQL Data Warehouse.

Para a simplicidade, o modelo neste tutorial não cria um gatilho programado. Pode adicionar um, se necessário.

![Diagrama do oleoduto](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta de armazenamento azure `sinkdata` Blob com um recipiente chamado para ser usado como pia.

  Tome nota do nome da conta de armazenamento, nome do recipiente e chave de acesso. Você precisará destes valores mais tarde no modelo.

- Um espaço de trabalho Azure Databricks.

## <a name="import-a-notebook-for-transformation"></a>Importar um caderno para a transformação

Para importar um caderno **de transformação** para o seu espaço de trabalho Databricks:

1. Inscreva-se no espaço de trabalho do Seu Azure Databricks e, em seguida, selecione **Import**.
       ![Comando de menu para](media/solution-template-Databricks-notebook/import-notebook.png) importar um espaço de trabalho O seu percurso no espaço de trabalho pode ser diferente do mostrado, mas lembre-se dele para mais tarde.
1. Selecione **Importar a partir de: URL**. Na caixa de `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`texto, introduza .

   ![Seleções para importar um caderno](media/solution-template-Databricks-notebook/import-from-url.png)

1. Agora vamos atualizar o caderno **Transformation** com as suas informações de ligação de armazenamento.

   No caderno importado, vá ao **comando 5** como mostrado no seguinte código.

   - `<storage name>`Substitua `<access key>` e com as suas próprias informações de ligação de armazenamento.
   - Utilize a conta `sinkdata` de armazenamento com o recipiente.

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

1. Gere um símbolo de acesso de **Databricks** para data factory para aceder a Databricks.
   1. No espaço de trabalho dos Databricks, selecione o ícone do perfil do utilizador na parte superior direita.
   1. Selecione **as definições do utilizador**.
    ![Comando de menu para definições do utilizador](media/solution-template-Databricks-notebook/user-setting.png)
   1. Selecione **Generate New Token** sob o separador **Access Tokens.**
   1. Selecione **Generate**.

    ![Botão "Gerar"](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Guarde o sinal de acesso* para posterior utilização na criação de um serviço ligado aos Databricks. O sinal de acesso `dapi32db32cbb4w6eee18b7d87e45exxxxxx`parece algo como.

## <a name="how-to-use-this-template"></a>Como usar este modelo

1. Vá ao modelo **de Transformação com Tijolos** de Dados Azure e crie novos serviços ligados para seguir ligações.

   ![Definição de conexões](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Source Blob Connection** - para aceder aos dados de origem.

       Para este exercício, pode utilizar o armazenamento de bolhas públicas que contém os ficheiros de origem. Consulte a seguinte imagem para a configuração. Utilize o seguinte **URL SAS** para ligar ao armazenamento de origem (acesso só para leitura):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Seleções para método de autenticação e URL SAS](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Destination Blob Connection** - para armazenar os dados copiados.

       Na nova janela **de serviço ligada,** selecione a sua bolha de armazenamento de pia.

       ![Bolha de armazenamento de pia como um novo serviço ligado](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** - para ligar ao cluster Databricks.

        Crie um serviço ligado aos Databricks utilizando a chave de acesso que gerou anteriormente. Pode optar por selecionar um *cluster interativo* se tiver um. Este exemplo utiliza a opção **new job cluster.**

        ![Seleções para ligação ao cluster](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Selecione **Utilize este modelo**. Verá um oleoduto criado.

    ![Criar um pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Introdução e configuração do gasoduto

No novo pipeline, a maioria das definições são configuradas automaticamente com valores predefinidos. Reveja as configurações do seu pipeline e efaça as alterações necessárias.

1. Na bandeira **de disponibilidade**da atividade de **validação,** `SourceAvailabilityDataset` verifique se o valor de **dataset** de origem está definido para o que criou anteriormente.

   ![Valor do conjunto de dados de origem](media/solution-template-Databricks-notebook/validation-settings.png)

1. No **ficheiro-a-bolha**da atividade de **dados da Cópia,** verifique os separadores **Source** e **Sink.** Mude as definições se necessário.

   - **Separador fonte** Separador ![Fonte separador Fonte](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **Separador apiador Aba** ![Pia](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. Na **Transformação**da Atividade **do Caderno,** reveja e atualize os caminhos e configurações conforme necessário.

   O serviço ligado aos **databricks** deve ser pré-povoado com ![o valor de um passo anterior, como mostra: Valor povoado para o serviço ligado a Databricks](media/solution-template-Databricks-notebook/notebook-activity.png)

   Para verificar as definições do **Caderno:**
  
    1. Selecione o separador **Definições.** Para o **caminho do Caderno,** verifique se o caminho predefinido está correto. Pode ser necessário navegar e escolher o caminho correto do caderno.

       ![Caminho do caderno](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Expanda o seletor de **parâmetros base** e verifique se os parâmetros correspondem ao que é mostrado na seguinte imagem. Estes parâmetros são passados para o caderno Databricks da Data Factory.

       ![Parâmetros de base](media/solution-template-Databricks-notebook/base-parameters.png)

1. Verifique se os **parâmetros** do gasoduto correspondem ![ao que é mostrado na seguinte imagem: Parâmetros do gasoduto](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Ligue-se aos seus conjuntos de dados.

   - **SourceAvailabilityDataset** - para verificar se os dados de origem estão disponíveis.

     ![Seleções para serviço ligado e caminho de ficheiro para SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** - para aceder aos dados de origem.

       ![Seleções para serviço ligado e caminho de ficheiro para SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** - para copiar os dados para o local de destino da pia. Utilize os seguintes valores:

     - **Serviço ligado**`sinkBlob_LS`, criado num passo anterior. - 

     - **Caminho de** - `sinkdata/staged_sink`arquivo .

       ![Seleções para serviço ligado e caminho de ficheiro saca para DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Selecione **Debug** para executar o gasoduto. Pode encontrar o link para registos databricks para registos de Spark mais detalhados.

    ![Link para registos de Databricks a partir da saída](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Também pode verificar o ficheiro de dados utilizando o Azure Storage Explorer.

    > [!NOTE]
    > Para correlacionar com o pipeline Data Factory, este exemplo anexa o ID de execução do gasoduto da fábrica de dados para a pasta de saída. Isto ajuda a acompanhar os ficheiros gerados por cada execução.
    > ![ID de execução de gasoduto anexado](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Data Factory](introduction.md)
