---
title: Transformar dados com o Databricks no Azure Data Factory | Documentos da Microsoft
description: Saiba como utilizar um modelo de solução para transformar dados com um bloco de notas do Databricks no Azure Data Factory.
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
ms.openlocfilehash: 54b440ee76fe36a83284b8ce769bb31012781a35
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295764"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Transformar dados com o Databricks no Azure Data Factory

Neste tutorial, vai criar um pipeline ponto a ponto que contém **Lookup**, **cópia**, e **bloco de notas do Databricks** atividades no Data Factory.

-   **Pesquisa** ou atividade GetMetadata é utilizada para garantir que o conjunto de dados de origem está pronto para consumo downstream, antes de acionar a tarefa de cópia e análise.

-   **Atividade de cópia** copia o ficheiro de origem / conjunto de dados para o armazenamento de sink. O armazenamento de sink é montado como DBFS no bloco de notas do Databricks, para que o conjunto de dados pode ser diretamente consumido pelo Spark.

-   **Atividade do Databricks notebook** aciona o bloco de notas do Databricks que transforma o conjunto de dados e adiciona-o para uma pasta processada / SQL DW.

Para simplificar este modelo, o modelo não cria um acionador agendado. Pode adicioná-lo se necessário.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Pré-requisitos

1.  Criar uma **conta de armazenamento de BLOBs** e um contentor chamado `sinkdata` a utilizar como **sink**. Conserve o **nome da conta de armazenamento**, **nome do contentor**, e **chave de acesso**, uma vez que são referenciados mais tarde no modelo.

2.  Certifique-se de que tem um **área de trabalho do Azure Databricks** ou criar um novo.

1.  **Importar o bloco de notas de ETL**. Importar o abaixo transformação bloco de notas para a área de trabalho do Databricks. (Não tem de estar na mesma localização, conforme mostrado a seguir, mas lembre-se o caminho que escolher para mais tarde.) Importe o bloco de notas do URL ao introduzir este URL no campo de URL: `https://DataFactorylabstaging1.blob.core.windows.net/share/Transformations.html`. Selecione **importação**.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Agora vamos atualizar o **transformação** bloco de notas com seu **informações de ligação de armazenamento** (chave de acesso e o nome). Aceda a **comando 5** no importados bloco de notas acima, substitua-o com o abaixo trecho de código depois de substituir os valores realçados. Certifique-se de que esta conta é a mesma conta de armazenamento criada anteriormente e contém o `sinkdata` contentor.

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

1.  Gerar um **token de acesso do Databricks** do Data Factory acessar o Databricks. **Guarde o token de acesso** para utilização posterior na criação de um Databricks de serviço, o que se parece com algo como 'dapi32db32cbb4w6eee18b7d87e45exxxxxx' ligado

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Criar serviços ligados e conjuntos de dados

1.  Criar novo **serviços ligados** na IU do Data Factory, acedendo a *serviços ligados de ligações + novo*

    1.  **Origem** – para aceder aos dados de origem. Pode utilizar o armazenamento de BLOBs público, que contém os ficheiros de origem para este exemplo.

        Selecione **armazenamento de BLOBs**, utilize o abaixo **URI de SAS** para ligar ao armazenamento de origem (acesso só de leitura).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Sink** – para copiar dados em.

        Selecione um armazenamento criado no pré-requisito 1, no serviço ligado de sink.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** – para ligar ao cluster do Databricks

        Crie um serviço ligado do Databricks utilizando a chave de acesso gerada no 2.c pré-requisitos. Se tiver uma *cluster interativo*, pode selecionar que. (Este exemplo utiliza a *novo cluster de trabalhos* opção.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  Criar **conjuntos de dados**

    1.  Crie **'sourceAvailability_Dataset'** para verificar se a origem de dados está disponível

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Conjunto de dados de origem –** para copiar os dados de origem (através de cópia binária)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Conjunto de dados de sink** – para copiar para o sink / localização de destino

        1.  Serviço - ligado Selecione 'sinkBlob_LS"criado no 1.b

        2.  Caminho do ficheiro - ' sinkdata/staged_sink'

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Criar atividades

1.  Criar uma atividade de pesquisa '**sinalizador Availability**' para efetuar uma verificação de disponibilidade de origem (pesquisa ou GetMetadata pode ser utilizado). Selecione "sourceAvailability_Dataset" criado no 2.a.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Criar uma atividade de cópia '**blob de arquivo**"para copiar o conjunto de dados de origem para o sink. Neste caso, os dados são o arquivo binário. Referência a seguir capturas de tela para configurações de origem e sink na atividade de cópia.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Definir **parâmetros do pipeline**

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Criar um **atividade do Databricks**

    Selecione o serviço ligado criado num passo anterior.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Configurar o **definições**. Crie **parâmetros de Base** conforme mostrado na captura de ecrã e criar parâmetros a serem passados para o bloco de notas do Databricks da fábrica de dados. Procurar e **selecionar** a **caminho do notebook correto** carregados **pré-requisito 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Execute o pipeline**. Pode encontrar o link para registos de Databricks para registos do Spark mais detalhados.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Também pode verificar o ficheiro de dados com o Explorador de armazenamento. (Para correlacionar com execuções de pipeline do Data Factory, este exemplo acrescenta a execução da fábrica de dados para a pasta de saída de pipeline. Desta forma pode controlar novamente os arquivos gerados por meio de cada execução.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Azure Data Factory](introduction.md)
