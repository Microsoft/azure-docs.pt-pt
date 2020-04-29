---
title: Copiar ficheiros de vários contentores
description: Aprenda a usar um modelo de solução para copiar ficheiros de vários contentores utilizando a Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: 383b70bbb02e7a200c7ec0a994f7cf11e9b9520e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414823"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copiar ficheiros de vários contentores com fábrica de dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve um modelo de solução que pode utilizar para copiar ficheiros de vários contentores entre lojas de ficheiros. Por exemplo, pode usá-lo para migrar o seu lago de dados de AWS S3 para a Azure Data Lake Store. Ou, você poderia usar o modelo para replicar tudo de uma conta de armazenamento Azure Blob para outra.

> [!NOTE]
> Se pretender copiar ficheiros a partir de um único recipiente, é mais eficiente utilizar a Ferramenta de Dados de [Cópia](copy-data-tool.md) para criar um pipeline com uma única atividade de cópia. O modelo neste artigo é mais do que você precisa para esse cenário simples.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo enumera os recipientes do seu armazém de origem. Em seguida, copia os contentores para a loja de destino.

O modelo contém três atividades:
- **GetMetadata** verifica o seu armazém de origem e obtém a lista de contentores.
- **ForEach** obtém a lista de contentores da atividade **GetMetadata** e, em seguida, iterates sobre a lista e passa cada recipiente para a atividade Copy.
- **Copie** cada recipiente do armazém de origem para a loja de destino.

O modelo define os seguintes parâmetros:
- *SourceFileFolder* é o caminho da pasta da sua loja de fonte de dados, onde pode obter uma lista dos recipientes. O caminho é o diretório raiz, que contém várias pastas de recipientes. O valor padrão deste `sourcefolder`parâmetro é .
- *SourceFileDirectory* é o caminho da subpasta sob o diretório raiz da sua loja de fonte de dados. O valor padrão deste `subfolder`parâmetro é .
- *DestinationFileFolder* é o caminho da pasta para onde os ficheiros serão copiados na sua loja de destino. O valor padrão deste `destinationfolder`parâmetro é .
- *DestinationFileDirectory* é o caminho da subpasta para onde os ficheiros serão copiados na sua loja de destino. O valor padrão deste `subfolder`parâmetro é .

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá ao **copy vários ficheiros contentores entre** o modelo de Lojas de Arquivo. Crie uma **nova** ligação com o seu armazém de origem. O armazém de origem é onde pretende copiar ficheiros de vários contentores.

    ![Criar uma nova ligação com a fonte](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Crie uma **nova** ligação à sua loja de armazenamento de destino.

    ![Criar uma nova ligação ao destino](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selecione **Utilize este modelo**.

    ![Utilizar este modelo](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Verá o oleoduto, como no seguinte exemplo:

    ![Mostre o oleoduto](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selecione **Debug,** introduza os **Parâmetros**e, em seguida, selecione **Terminar**.

    ![Executar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Reveja o resultado.

    ![Reveja o resultado](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Passos seguintes

- [Cópia a granel de uma base de dados utilizando uma tabela de controlo com a Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Copiar ficheiros de vários contentores com fábrica de dados Azure](solution-template-copy-files-multiple-containers.md)
