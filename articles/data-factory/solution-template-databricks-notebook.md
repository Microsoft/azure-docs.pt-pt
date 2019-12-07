---
title: Transformar dados usando o databricks
description: Saiba como usar um modelo de solução para transformar dados usando um bloco de anotações do databricks no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 5b39e354d503910d20141ce19c625eb79b4a7353
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891002"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Transformar dados usando o databricks no Azure Data Factory

Neste tutorial, você cria um pipeline de ponta a ponta contendo atividades de **pesquisa**, **cópia**e **bloco de anotações do databricks** no data Factory.

-   A atividade **Lookup** ou GetMetadata é usada para garantir que o conjunto de fonte de origem esteja pronto para o consumo downstream, antes de disparar o trabalho de cópia e de análise.

-   A **atividade de cópia** copia o arquivo/conjunto de arquivos de origem para o armazenamento do coletor. O armazenamento do coletor é montado como DBFS no notebook databricks para que o conjunto de um possa ser diretamente consumido pelo Spark.

-   A **atividade do databricks Notebook** dispara o bloco de anotações do databricks que transforma o conjunto de e adiciona-o a uma pasta processada/SQL DW.

Para manter esse modelo simples, o modelo não cria um gatilho agendado. Você pode adicionar isso, se necessário.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Pré-requisitos

1.  Crie uma **conta de armazenamento de BLOBs** e um contêiner chamado `sinkdata` a ser usado como **coletor**. Anote o **nome da conta de armazenamento**, o **nome do contêiner**e a chave de **acesso**, pois eles são referenciados posteriormente no modelo.

2.  Verifique se você tem um **espaço de trabalho Azure Databricks** ou crie um novo.

1.  **Importe o bloco de anotações para ETL**. Importe o bloco de anotações de transformação abaixo para o espaço de trabalho do databricks. (Não precisa estar no mesmo local que está abaixo, mas lembre-se do caminho que você escolher para mais tarde). Importe o bloco de anotações da URL a seguir digitando esta URL no campo URL: `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`. Selecione **Import** (Importar).

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Agora, vamos atualizar o bloco de anotações de **transformação** com suas **informações de conexão de armazenamento** (nome e tecla de acesso). Vá para o **comando 5** no bloco de anotações importado acima, substitua-o pelo trecho de código abaixo depois de substituir os valores realçados. Verifique se essa conta é a mesma conta de armazenamento criada anteriormente e contém o contêiner `sinkdata`.

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

1.  Gere um **token de acesso do databricks** para data Factory acessar o databricks. **Salve o token de acesso** para uso posterior na criação de um serviço vinculado do databricks, que se parece com ' dapi32db32cbb4w6eee18b7d87e45exxxxxx '

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Criar serviços vinculados e conjuntos de itens

1.  Criar novos **Serviços vinculados** na interface do usuário do data Factory acessando *conexões serviços vinculados + novo*

    1.  **Fonte** – para acessar dados de origem. Você pode usar o armazenamento de blob público que contém os arquivos de origem para este exemplo.

        Selecione **armazenamento de BLOBs**, use o **URI de SAS** abaixo para se conectar ao armazenamento de origem (acesso somente leitura).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Coletor** – para copiar dados para o.

        Selecione um armazenamento criado no pré-requisito 1, no serviço vinculado do coletor.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** – para se conectar ao cluster do databricks

        Crie um serviço vinculado do databricks usando a chave de acesso gerada no pré-requisito 2. c. Se você tiver um *cluster interativo*, poderá selecioná-lo. (Este exemplo usa a *nova* opção de cluster de trabalho.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  Criar **conjuntos** de os

    1.  Criar **' sourceAvailability_Dataset '** para verificar se os dados de origem estão disponíveis

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **DataSet de origem –** para copiar os dados de origem (usando cópia binária)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Conjunto** de coleta do coletor – para copiar no local do coletor/destino

        1.  Serviço vinculado-selecione ' sinkBlob_LS ' criado em 1. b

        2.  Caminho do arquivo-' SinkData/staged_sink '

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Criar atividades

1.  Criar uma atividade de pesquisa '**sinalizador de disponibilidade**' para fazer uma verificação de disponibilidade de origem (Lookup ou GetMetadata pode ser usado). Selecione ' sourceAvailability_Dataset ' criado em 2. a.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Crie uma atividade de cópia '**file-to-blob**' para copiar o conjunto de código da origem para o coletor. Nesse caso, os dados são um arquivo binário. Referencie as capturas de tela abaixo para configurações de origem e de coletor na atividade de cópia.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Definir **parâmetros de pipeline**

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Criar uma **atividade do databricks**

    Selecione o serviço vinculado criado em uma etapa anterior.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Defina as **configurações**. Crie **parâmetros de base** , conforme mostrado na captura de tela, e crie parâmetros a serem passados para o databricks notebook a partir de data Factory. Procure e **selecione** o **caminho de bloco de anotações correto** carregado no **pré-requisito 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Execute o pipeline**. Você pode encontrar links para logs do databricks para logs do Spark mais detalhados.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Você também pode verificar o arquivo de dados usando o Gerenciador de armazenamento. (Para correlacionar com Data Factory pipeline é executado, este exemplo acrescenta a ID de execução de pipeline de data factory à pasta de saída. Dessa forma, você pode rastrear os arquivos gerados por cada execução.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Data Factory](introduction.md)
