---
title: Transformação com o Azure Databricks
description: Aprenda a usar um modelo de solução para transformar dados utilizando um caderno Databricks na Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: ee663423071458605f37f07293693dbc91f592bb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362119"
---
# <a name="transformation-with-azure-databricks"></a>Transformação com o Azure Databricks

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, cria-se um pipeline de ponta a ponta que contém as **atividades** de **Validação,** Cópia e **Portátil** na Azure Data Factory.

- **A validação** garante que o seu conjunto de dados de origem está pronto para consumo a jusante antes de desencadear o trabalho de cópia e análise.

- **Os dados** de cópia duplicam o conjunto de dados de origem para o armazenamento da pia, que é montado como DBFS no caderno Azure Databricks. Desta forma, o conjunto de dados pode ser diretamente consumido pela Spark.

- **O notebook** aciona o caderno Databricks que transforma o conjunto de dados. Adiciona também o conjunto de dados a uma pasta processada ou a Azure Azure Synapse Analytics.

Para simplificar, o modelo neste tutorial não cria um gatilho programado. Pode adicionar um, se necessário.

![Diagrama do oleoduto](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta de armazenamento Azure Blob com um recipiente chamado `sinkdata` para ser usado como pia.

  Tome nota do nome da conta de armazenamento, nome do recipiente e chave de acesso. Você vai precisar destes valores mais tarde no modelo.

- Um espaço de trabalho da Azure Databricks.

## <a name="import-a-notebook-for-transformation"></a>Importar um caderno para a transformação

Para importar um caderno **de transformação** para o seu espaço de trabalho Databricks:

1. Inscreva-se no seu espaço de trabalho Azure Databricks e, em seguida, selecione **Import**.
       ![Menu comando para importar um espaço de trabalho ](media/solution-template-Databricks-notebook/import-notebook.png) O seu caminho de espaço de trabalho pode ser diferente do mostrado, mas lembre-se dele para mais tarde.
1. Selecione **Importar a partir de: URL**. Na caixa de texto, insira `https://adflabstaging1.blob.core.windows.net/share/Transformations.html` .

   ![Seleções para importar um caderno](media/solution-template-Databricks-notebook/import-from-url.png)

1. Agora vamos atualizar o caderno **de transformação** com as informações de ligação de armazenamento.

   No caderno importado, vá ao **comando 5,** como mostrado no seguinte corte de código.

   - Substitua `<storage name>` e `<access key>` por informações de ligação de armazenamento próprias.
   - Utilize a conta de armazenamento com o `sinkdata` recipiente.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Gere um **token de acesso databricks para** data factory para aceder a Databricks.
   1. No seu espaço de trabalho Databricks, selecione o ícone do perfil do utilizador no canto superior direito.
   1. Selecione **as definições do utilizador**.
    ![Comando do menu para definições de utilizador](media/solution-template-Databricks-notebook/user-setting.png)
   1. **Selecione Gerar Novo Token** no separador **Tokens de acesso.**
   1. **Selecione Gerar**.

    ![Botão "Gerar"](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Guarde o token de acesso para* utilização posterior na criação de um serviço ligado a Databricks. O símbolo de acesso parece algo `dapi32db32cbb4w6eee18b7d87e45exxxxxx` como.

## <a name="how-to-use-this-template"></a>Como usar este modelo

1. Vá ao **modelo Transformation with Azure Databricks** e crie novos serviços ligados para seguir ligações.

   ![Definição de ligações](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Source Blob Connection** - para aceder aos dados de origem.

       Para este exercício, pode utilizar o armazenamento de bolhas públicas que contém os ficheiros de origem. Consulte a seguinte imagem para a configuração. Utilize o seguinte **URL SAS** para ligar ao armazenamento de origem (acesso apenas de leitura):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Seleções para método de autenticação e URL SAS](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Destination Blob Connection** - para armazenar os dados copiados.

       Na janela **de serviço new linked,** selecione a sua bolha de armazenamento de pia.

       ![Bolha de armazenamento de pia como um novo serviço ligado](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** - para ligar ao cluster Databricks.

        Crie um serviço ligado a Databricks utilizando a chave de acesso que gerou anteriormente. Pode optar por selecionar um *cluster interativo* se tiver um. Este exemplo utiliza a nova opção **de cluster de emprego.**

        ![Seleções para ligação ao cluster](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Selecione **Utilize este modelo.** Verá um oleoduto criado.

    ![Criar um pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Introdução e configuração do gasoduto

No novo oleoduto, a maioria das definições são configuradas automaticamente com valores predefinidos. Reveja as configurações do seu pipeline e faça as alterações necessárias.

1. No **pavilhão de disponibilidade** da atividade de **validação,** verifique se o valor do **Conjunto de Dados** de origem está definido para `SourceAvailabilityDataset` o que criou anteriormente.

   ![Valor do conjunto de dados de origem](media/solution-template-Databricks-notebook/validation-settings.png)

1. No ficheiro de atividade **de dados** **copy-to-blob,** verifique os separadores **'Fonte** e **Sink'.** Alterar as definições, se necessário.

   - **Separador de** ![ origem Separador de Origem](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **Separador de** pia ![ Tab Sink](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. Na **transformação** da atividade do **Caderno,** reveja e atualize os caminhos e configurações conforme necessário.

   **O serviço ligado aos dados** deve ser pré-povoado com o valor de um passo anterior, como mostrado: ![ Valor povoado para o serviço ligado databricks](media/solution-template-Databricks-notebook/notebook-activity.png)

   Para verificar as definições do **Caderno:**
  
    1. Selecione o **separador Definições.** Para **o caminho do portátil,** verifique se o caminho predefinido está correto. Pode ser necessário navegar e escolher o caminho correto do caderno.

       ![Caminho do caderno](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Expanda o seletor **de parâmetros base** e verifique se os parâmetros correspondem ao que é mostrado na imagem seguinte. Estes parâmetros são passados para o caderno Databricks da Data Factory.

       ![Parâmetros de base](media/solution-template-Databricks-notebook/base-parameters.png)

1. Verifique se os **parâmetros** do gasoduto correspondem ao que é mostrado na seguinte imagem: ![ Parâmetros de pipeline](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Ligue-se aos seus conjuntos de dados.

    >[!NOTE]
    >Nos conjuntos de dados abaixo, o caminho do ficheiro foi automaticamente especificado no modelo. Se forem necessárias alterações, certifique-se de que especifica o caminho tanto para o **contentor** como para o **diretório** em caso de erro de ligação.

   - **SourceAvailabilityDataset** - para verificar se os dados de origem estão disponíveis.

     ![Seleções para serviços ligados e caminho de ficheiro para SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** - para aceder aos dados de origem.

       ![Seleções para serviços ligados e caminho de ficheiro para SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** - para copiar os dados para o local do destino da pia. Utilize os seguintes valores:

     - **Serviço ligado**  -  `sinkBlob_LS` , criado em passo anterior.

     - **Caminho do arquivo**  -  `sinkdata/staged_sink` .

       ![Seleções para serviços ligados e caminho de ficheiro para DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Selecione **Debug** para executar o oleoduto. Pode encontrar a ligação com os registos databricks para registos de faíscas mais detalhados.

    ![Link para databricks registos a partir da saída](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Também pode verificar o ficheiro de dados utilizando o Azure Storage Explorer.

    > [!NOTE]
    > Para se correlacionar com o gasoduto Data Factory, este exemplo anexa o ID de funcionação do gasoduto da fábrica de dados para a pasta de saída. Isto ajuda a acompanhar os ficheiros gerados por cada execução.
    > ![ID de execução de gasoduto anexado](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Data Factory](introduction.md)
