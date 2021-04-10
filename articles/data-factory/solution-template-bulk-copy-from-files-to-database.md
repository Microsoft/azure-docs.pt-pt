---
title: Cópia em massa de ficheiros para base de dados
description: Saiba como utilizar um modelo de solução para copiar dados a granel de Azure Data Lake Storage Gen2 a Azure Synapse Analytics / Azure SQL Database.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 5f68c7b33a26bc8c01f3f413943351ff8d61b380
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376126"
---
# <a name="bulk-copy-from-files-to-database"></a>Cópia em massa de ficheiros para base de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve um modelo de solução que pode usar para copiar dados a granel de Azure Data Lake Storage Gen2 a Azure Synapse Analytics / Azure SQL Database.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo recupera ficheiros da fonte gen2 de armazenamento de dados do Azure Data Lake. Em seguida, itera sobre cada ficheiro na fonte e copia o ficheiro para a loja de dados de destino. 

Atualmente, este modelo apenas suporta a cópia de dados no formato **DelimitedText.** Os ficheiros em outros formatos de dados também podem ser recuperados na loja de dados de origem, mas não podem ser copiados para a loja de dados de destino.  

O modelo contém três atividades:
- Obtenha a atividade **de metadados** que recupera ficheiros do Azure Data Lake Storage Gen2 e passa-os para a atividade subsequente *do ForEach.*
- A atividade **forEach** obtém ficheiros da atividade *Get Metadata* e itera cada ficheiro para a atividade *Copy.*
- **A** atividade de cópia reside na atividade *forEach* para copiar cada ficheiro da loja de dados de origem para a loja de dados de destino.

O modelo define os dois parâmetros seguintes:
- *SourceContainer* é a via do recipiente raiz onde os dados são copiados no seu Azure Data Lake Storage Gen2. 
- *SourceDirectory* é o caminho do diretório sob o recipiente raiz onde os dados são copiados no seu Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Aceda à cópia em massa de ficheiros para o modelo **de base de dados.** Crie uma **nova** ligação à loja de origem Gen2. Esteja ciente de que "GetMetadataDataset" e "SourceDataset" são referências à mesma ligação da sua loja de ficheiros de origem.

    ![Criar uma nova ligação à loja de dados de origem](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Crie uma **nova** ligação à loja de dados da pia para a qual está a copiar dados.

    ![Criar uma nova ligação à loja de dados da pia](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Selecione **Utilize este modelo.**

    ![Utilizar este modelo](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Veria um oleoduto criado como mostrado no seguinte exemplo:

    ![Reveja o oleoduto](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Se escolheu **a Azure Synapse Analytics** como o destino de dados no **passo 2** acima mencionado, deve introduzir uma ligação com o armazenamento Azure Blob para a realização, conforme exigido pela Azure Synapse Analytics Polybase. Como mostra a imagem que se segue, o modelo gerará automaticamente um *Caminho de Armazenamento* para o seu armazenamento Blob. Verifique se o recipiente foi criado após o funcionado do gasoduto.
        
    ![Definição de base poli-base](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Selecione **Debug,** introduza os **parâmetros** e, em seguida, selecione **Terminar**.

    ![Clique em **Debug**](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Quando o gasoduto estiver concluído com sucesso, veria resultados semelhantes ao seguinte exemplo:

    ![Reveja o resultado](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Data Factory](introduction.md)