---
title: Cópia em massa de arquivos para banco de dados
description: Saiba como usar um modelo de solução para copiar dados em massa de Azure Data Lake Storage Gen2 para o Azure Synapse Analytics/banco de dados SQL do Azure.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: ae250c7d15801789ad22955845cfa535ed91f2c1
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921145"
---
# <a name="bulk-copy-from-files-to-database"></a>Cópia em massa de arquivos para banco de dados

Este artigo descreve um modelo de solução que você pode usar para copiar dados em massa de Azure Data Lake Storage Gen2 para o Azure Synapse Analytics/banco de dados SQL do Azure.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo recupera arquivos de origem Azure Data Lake Storage Gen2. Em seguida, ele itera em cada arquivo na origem e copia o arquivo para o armazenamento de dados de destino. 

Atualmente, esse modelo só dá suporte à cópia de dados no formato **DelimitedText** . Os arquivos em outros formatos de dados também podem ser recuperados do armazenamento de dados de origem, mas não podem ser copiados para o armazenamento de dados de destino.  

O modelo contém três atividades:
- A atividade **obter metadados** recupera arquivos de Azure data Lake Storage Gen2 e os transmite para a atividade *foreach* subsequente.
- A atividade **foreach** Obtém os arquivos da atividade *obter metadados* e itera cada arquivo na atividade de *cópia* .
- A atividade de **cópia** reside na atividade *foreach* para copiar cada arquivo do repositório de dados de origem para o armazenamento de dados de destino.

O modelo define os dois parâmetros a seguir:
- *SourceContainer* é o caminho do contêiner raiz para o qual os dados são copiados em seu Azure data Lake Storage Gen2. 
- *SourceDirectory* é o caminho do diretório no contêiner raiz no qual os dados são copiados no seu Azure data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá para o modelo **cópia em massa de arquivos para banco de dados** . Crie uma **nova** conexão com o repositório Gen2 de origem. Lembre-se de que "GetMetadataDataset" e "SourceDataset" são referências à mesma conexão do seu repositório de arquivos de origem.

    ![Criar uma nova conexão com o armazenamento de dados de origem](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Crie uma **nova** conexão com o armazenamento de dados do coletor para o qual você está copiando dados.

    ![Criar uma nova conexão com o armazenamento de dados do coletor](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Selecione **Utilizar este modelo**.

    ![Utilizar este modelo](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Você verá um pipeline criado conforme mostrado no exemplo a seguir:

    ![Examinar o pipeline](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Se você escolheu o **Azure Synapse Analytics (anteriormente conhecido como SQL DW)** como o destino de dados na **etapa 2** mencionado acima, você deve inserir uma conexão com o armazenamento de BLOBs do Azure para preparo, conforme exigido pelo SQL data warehouse polybase. Como mostra a captura de tela a seguir, o modelo gerará automaticamente um *caminho de armazenamento* para o armazenamento de BLOBs. Verifique se o contêiner foi criado após a execução do pipeline.
        
    ![Configuração do polybase](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Selecione **depurar**, insira os **parâmetros**e, em seguida, selecione **concluir**.

    ![Clique em * * Depurar * *](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Quando a execução do pipeline for concluída com êxito, você verá resultados semelhantes ao exemplo a seguir:

    ![Examinar o resultado](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Data Factory](introduction.md)