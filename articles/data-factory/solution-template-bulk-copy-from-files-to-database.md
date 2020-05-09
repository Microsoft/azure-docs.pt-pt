---
title: Cópia em massa de ficheiros para base de dados
description: Saiba como utilizar um modelo de solução para copiar dados a granel do Azure Data Lake Storage Gen2 para Azure Synapse Analytics / Azure SQL Database.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 468bd838237e076aacb9dee0ccacfdcc1ea940af
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629121"
---
# <a name="bulk-copy-from-files-to-database"></a>Cópia em massa de ficheiros para base de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve um modelo de solução que pode utilizar para copiar dados a granel do Azure Data Lake Storage Gen2 para Azure Synapse Analytics / Azure SQL Database.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo recupera ficheiros da fonte do Azure Data Lake Storage Gen2. Em seguida, iterates sobre cada ficheiro na fonte e copia o ficheiro para a loja de dados de destino. 

Atualmente este modelo apenas suporta copiar dados em formato **DelimitedText.** Os ficheiros em outros formatos de dados também podem ser recuperados a partir da loja de dados de origem, mas não podem ser copiados para a loja de dados de destino.  

O modelo contém três atividades:
- Obtenha a atividade de **Metadados** que recupera ficheiros do Azure Data Lake Storage Gen2, e passa-os para a atividade subsequente de *ForEach.*
- **ForEach** activity obtém ficheiros da atividade *Get Metadata* e iterates cada ficheiro para a atividade *Copy.*
- **A** atividade de cópia reside na atividade *forEach* para copiar cada ficheiro da loja de dados de origem para a loja de dados de destino.

O modelo define os dois parâmetros seguintes:
- *SourceContainer* é o caminho do recipiente de raiz onde os dados são copiados no seu Azure Data Lake Storage Gen2. 
- *SourceDirectory* é o caminho do diretório sob o recipiente raiz onde os dados são copiados no seu Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá para a **cópia a granel de Ficheiros para** modelo de base de dados. Crie uma **nova** ligação à loja source Gen2. Esteja ciente de que "GetMetadataDataset" e "SourceDataset" são referências à mesma ligação da sua loja de ficheiros fonte.

    ![Criar uma nova ligação à loja de dados de origem](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Crie uma **nova** ligação à loja de dados da pia para a qual está a copiar dados.

    ![Criar uma nova ligação à loja de dados do lavatório](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Selecione **Utilize este modelo**.

    ![Utilizar este modelo](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Veria um gasoduto criado como mostrado no seguinte exemplo:

    ![Reveja o oleoduto](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Se escolheu o **Azure Synapse Analytics (anteriormente SQL DW)** como destino de dados no **passo 2** acima mencionado, deve introduzir uma ligação ao armazenamento Azure Blob para encenação, conforme exigido pela SQL Data Warehouse Polybase. Como mostra a seguinte imagem, o modelo gerará automaticamente um *Caminho de Armazenamento* para o seu armazenamento Blob. Verifique se o recipiente foi criado após a execução do gasoduto.
        
    ![Definição de polibase](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Selecione **Debug,** introduza os **Parâmetros**e, em seguida, selecione **Terminar**.

    ![Clique em **Debug**](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Quando o gasoduto termina com sucesso, veria resultados semelhantes aos seguintes exemplos:

    ![Reveja o resultado](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Data Factory](introduction.md)