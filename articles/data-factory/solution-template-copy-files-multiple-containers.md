---
title: Copiar ficheiros de vários contentores
description: Aprenda a usar um modelo de solução para copiar ficheiros de vários contentores utilizando a Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: ec7af1e81e0b295491420597636c8443f4d36512
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376093"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>Copiar várias pastas com Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve um modelo de solução que pode utilizar múltiplas atividades de cópia para copiar contentores ou pastas entre lojas baseadas em ficheiros, onde cada atividade de cópia deve copiar um único recipiente ou pasta. 

> [!NOTE]
> Se pretender copiar ficheiros de um único recipiente, é mais eficiente utilizar a [Ferramenta de Dados de Cópia](copy-data-tool.md) para criar um pipeline com uma única atividade de cópia. O modelo deste artigo é mais do que você precisa para este cenário simples.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo enumera as pastas de uma determinada pasta dos pais na sua loja de armazenamento de origem. Em seguida, copia cada uma das pastas para a loja de destino.

O modelo contém três atividades:
- **A GetMetadata** digitaliza a sua loja de armazenamento de origem e obtém a lista de sub-dobras de uma determinada pasta dos pais.
- **ForEach** obtém a lista de sub-dobradizas da atividade **GetMetadata** e, em seguida, iterates sobre a lista e passa cada pasta para a atividade Copy.
- **Copie** cada pasta da loja de armazenamento de origem para a loja de destino.

O modelo define os seguintes parâmetros:
- *SourceFileFolder* é parte do caminho da pasta principal da sua loja de fontes de dados: *SourceFileFolder/SourceFileDirectory,* onde pode obter uma lista das sub-dobradeiras. 
- *SourceFileDirectory* faz parte do caminho da pasta principal da sua loja de fontes de dados: *SourceFileFolder/SourceFileDirectory,* onde pode obter uma lista das sub-dobradeiras. 
- *DestinationFileFolder* faz parte do caminho da pasta principal: *DestinationFileFolder/DestinationFileDirectory* onde os ficheiros serão copiados para a sua loja de destino. 
- *DestinationFileDirect é* parte do caminho da pasta principal: *DestinationFileFolder/DestinationFileDirectory* onde os ficheiros serão copiados para a sua loja de destino. 

Se pretender copiar vários recipientes sob pastas de raiz entre armazéns, pode inserir os quatro parâmetros como */* . Ao fazê-lo, irá replicar tudo entre lojas de armazenamento.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Aceda ao **copy vários contentores de ficheiros entre** o modelo de Lojas de Ficheiros. Crie uma **nova** ligação ao seu armazém de origem. A loja de armazenamento de origem é onde pretende copiar ficheiros de vários contentores.

    ![Criar uma nova ligação à fonte](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Crie uma **nova** ligação à sua loja de armazenamento de destino.

    ![Criar uma nova ligação ao destino](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selecione **Utilize este modelo.**

    ![Utilizar este modelo](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Verá o oleoduto, como no seguinte exemplo:

    ![Mostrar o oleoduto](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selecione **Debug,** introduza os **parâmetros** e, em seguida, selecione **Terminar**.

    ![Executar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Reveja o resultado.

    ![Reveja o resultado](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Passos seguintes

- [Cópia a granel de uma base de dados usando uma tabela de controlo com Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Copiar ficheiros de vários contentores com Azure Data Factory](solution-template-copy-files-multiple-containers.md)
