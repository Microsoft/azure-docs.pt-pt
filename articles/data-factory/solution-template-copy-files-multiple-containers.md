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
ms.openlocfilehash: 73560c49e10ab96c934d4dd3cea9395093a26420
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82629053"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copiar ficheiros de vários contentores com Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve um modelo de solução que pode usar para copiar ficheiros de vários contentores entre lojas de ficheiros. Por exemplo, pode usá-lo para migrar o seu lago de dados de AWS S3 para Azure Data Lake Store. Ou, você poderia usar o modelo para replicar tudo de uma conta de armazenamento Azure Blob para outra.

> [!NOTE]
> Se pretender copiar ficheiros de um único recipiente, é mais eficiente utilizar a [Ferramenta de Dados de Cópia](copy-data-tool.md) para criar um pipeline com uma única atividade de cópia. O modelo deste artigo é mais do que você precisa para este cenário simples.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo enumera os recipientes da sua loja de armazenamento de origem. Em seguida, copia esses contentores para a loja de destino.

O modelo contém três atividades:
- **A GetMetadata** digitaliza o seu armazém de origem e recebe a lista de contentores.
- **ForEach** obtém a lista de contentores da atividade **GetMetadata** e, em seguida, itera sobre a lista e passa cada recipiente para a atividade Copy.
- **Copie** cada recipiente da loja de armazenamento de origem para a loja de destino.

O modelo define os seguintes parâmetros:
- *SourceFileFolder* é o caminho da pasta da sua loja de fontes de dados, onde pode obter uma lista dos recipientes. O caminho é o diretório de raiz, que contém várias pastas de recipiente. O valor predefinido deste parâmetro é `sourcefolder` .
- *SourceFileDirectory* é o caminho subfolder sob o diretório raiz da sua loja de fontes de dados. O valor predefinido deste parâmetro é `subfolder` .
- *DestinationFileFolder* é o caminho da pasta onde os ficheiros serão copiados na sua loja de destino. O valor predefinido deste parâmetro é `destinationfolder` .
- *DestinationFileDirectory* é o caminho da sub-dobradeira onde os ficheiros serão copiados na sua loja de destino. O valor predefinido deste parâmetro é `subfolder` .

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Aceda ao **copy vários contentores de ficheiros entre** o modelo de Lojas de Ficheiros. Crie uma **nova** ligação ao seu armazém de origem. A loja de armazenamento de origem é onde pretende copiar ficheiros de vários contentores.

    ![Criar uma nova ligação à fonte](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Crie uma **nova** ligação à sua loja de armazenamento de destino.

    ![Criar uma nova ligação ao destino](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selecione **Utilize este modelo.**

    ![Utilizar este modelo](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Verá o oleoduto, como no seguinte exemplo:

    ![Mostrar o oleoduto](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selecione **Debug,** introduza os **parâmetros**e, em seguida, selecione **Terminar**.

    ![Executar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Reveja o resultado.

    ![Reveja o resultado](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Passos seguintes

- [Cópia a granel de uma base de dados usando uma tabela de controlo com Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Copiar ficheiros de vários contentores com Azure Data Factory](solution-template-copy-files-multiple-containers.md)
