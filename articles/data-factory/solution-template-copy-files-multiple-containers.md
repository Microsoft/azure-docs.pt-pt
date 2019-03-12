---
title: Copiar ficheiros de vários contentores através do Azure Data Factory | Documentos da Microsoft
description: Saiba como utilizar um modelo de solução para copiar ficheiros de vários contentores através do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: a52729adf8d6df3f4e44e561b45b854db433628c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543428"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copiar ficheiros de vários contentores com o Azure Data Factory

Este artigo descreve um modelo de solução que pode utilizar para copiar ficheiros de vários contentores entre arquivos de ficheiros. Por exemplo, pode utilizá-lo para migrar sua o data lake do AWS S3 para o Azure Data Lake Store. Em alternativa, poderia usar o modelo para replicar tudo de uma conta de armazenamento de Blobs do Azure para outra.

> [!NOTE]
> Se pretender copiar ficheiros de um único contentor, é mais eficiente para utilizar o [ferramenta de cópia de dados](copy-data-tool.md) para criar um pipeline com uma atividade de cópia única. O modelo neste artigo é mais do que precisa para esse cenário simple.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo enumera os contentores de seu arquivo de armazenamento de origem. Em seguida, copia esses contentores para o arquivo de destino.

O modelo contém três atividades:
- **GetMetadata** analisa o arquivo de armazenamento de origem e obtém a lista de contentores.
- **ForEach** obtém a lista de contentor a partir de **GetMetadata** atividade e, em seguida, itera sobre a lista e transmite o cada contentor para a atividade de cópia.
- **Cópia** copia cada contentor a partir da loja de armazenamento de origem para o arquivo de destino.

O modelo define dois parâmetros:
- *SourceFilePath* é o caminho do seu arquivo de origem de dados, onde pode obter uma lista dos contentores. Na maioria dos casos, o caminho é o diretório de raiz, que contém várias pastas de contentor. O valor padrão desse parâmetro é `/`.
- *DestinationFilePath* é o caminho onde os ficheiros serão copiados no seu arquivo de destino. O valor padrão desse parâmetro é `/`.

## <a name="how-to-use-this-solution-template"></a>Como utilizar este modelo de solução

1. Vá para o **copiar vários contentores de arquivos entre arquivos de ficheiro** modelo. Criar uma **New** ligação ao seu arquivo de armazenamento de origem. O arquivo de armazenamento de origem é onde pretende copiar ficheiros de vários contentores de.

    ![Criar uma nova ligação para a origem](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Criar uma **New** ligação ao seu arquivo de armazenamento de destino.

    ![Criar uma nova ligação para o destino](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selecione **Utilize este modelo**.

    ![Utilizar este modelo](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Verá o pipeline, como no exemplo seguinte:

    ![Mostrar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selecione **depurar**, introduza o **parâmetros**e, em seguida, selecione **concluir**.

    ![Executar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Reveja o resultado.

    ![O resultado da revisão](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Passos Seguintes

- [Cópia em massa de uma base de dados através de uma tabela de controle com o Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Copiar ficheiros de vários contentores com o Azure Data Factory](solution-template-copy-files-multiple-containers.md)