---
title: Mover ficheiros entre armazenamento baseado em ficheiros
description: Aprenda a usar um modelo de solução para mover ficheiros entre armazenamento baseado em ficheiros utilizando a Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: b3165daa06ed975df9ccb677699d3ceb449327ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74941948"
---
# <a name="move-files-with-azure-data-factory"></a>Mova ficheiros com a Azure Data Factory

Este artigo descreve um modelo de solução que pode utilizar para mover ficheiros de uma pasta para outra entre lojas baseadas em ficheiros. Um dos cenários comuns de utilização deste modelo: Os ficheiros são continuamente deixados cair numa pasta de aterragem da sua loja de origem. Ao criar um gatilho de horário, o gasoduto ADF pode periodicamente mover esses ficheiros da fonte para a loja de destino.  A forma como o pipeline ADF consegue "ficheiros em movimento" é obter os ficheiros da pasta de aterragem, copiando cada um deles para outra pasta na loja de destino e, em seguida, apagando os mesmos ficheiros da pasta de aterragem na loja fonte.

> [!NOTE]
> Esteja ciente de que este modelo foi concebido para mover ficheiros em vez de mover pastas.  Se pretender mover a pasta alterando o conjunto de dados para a tornar apenas uma pasta e, em seguida, utilizar a atividade da cópia e eliminar a atividade para se referir ao mesmo conjunto de dados que representa uma pasta, tem de ter muito cuidado. É porque tem de se certificar de que não haverá novos ficheiros que chegam à pasta entre a operação de cópia e a eliminação da operação. Se houver novos ficheiros que chegam à pasta no momento em que a sua atividade de cópia acabou de completar a função de cópia, mas a atividade de Eliminar não foi encarada, é possível que a atividade do Delete apague este novo ficheiro de chegada que NÃO foi copiado para o destino ainda, apagando toda a pasta.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo obtém os ficheiros da sua loja baseada em ficheiros de origem. Em seguida, move cada um deles para a loja de destino.

O modelo contém cinco atividades:
- **GetMetadata obtém** a lista de objetos, incluindo os ficheiros e subpastas da sua pasta na loja fonte. Não recuperará os objetos de forma recursiva. 
- **Filtrar** a lista de objetos da atividade **getMetadata** para selecionar apenas os ficheiros. 
- **ForEach** obtém a lista de ficheiros da atividade do **Filtro** e, em seguida, iterates sobre a lista e passa cada ficheiro para a atividade copy e eliminar atividade.
- **Copie** um ficheiro da fonte para a loja de destino.
- **Eliminar apaga** o mesmo ficheiro da loja fonte.

O modelo define quatro parâmetros:
- *SourceStore_Location* é o caminho da pasta da sua loja de origem onde pretende mover ficheiros. 
- *SourceStore_Directory* é o caminho da subpasta da sua loja de origem onde pretende mover ficheiros.
- *DestinationStore_Location* é o caminho das pastas da sua loja de destino para onde pretende mover ficheiros. 
- *DestinationStore_Directory* é o caminho da subpasta da sua loja de destino para onde pretende mover ficheiros.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá ao modelo **de ficheiros Move.** Selecione a ligação existente ou crie uma **nova** ligação à sua loja de ficheiros de origem onde pretenda mover ficheiros. Esteja ciente de que **DataSource_Folder** e **DataSource_File** são referência à mesma ligação da sua loja de ficheiros fonte.

    ![Criar uma nova ligação com a fonte](media/solution-template-move-files/move-files1.png)

2. Selecione a ligação existente ou crie uma **nova** ligação à sua loja de ficheiros de destino para onde pretende mover ficheiros.

    ![Criar uma nova ligação ao destino](media/solution-template-move-files/move-files2.png)

3. Selecione Utilize este separador de **modelo.**
    
4. Verá o oleoduto, como no seguinte exemplo:

    ![Mostre o oleoduto](media/solution-template-move-files/move-files4.png)

5. Selecione **Debug,** introduza os **Parâmetros**e, em seguida, selecione **Terminar**.   Os parâmetros são o caminho da pasta para onde pretende mover ficheiros e o caminho da pasta para onde pretende mover ficheiros. 

    ![Executar o pipeline](media/solution-template-move-files/move-files5.png)

6. Reveja o resultado.

    ![Reveja o resultado](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Passos seguintes

- [Copiar novos e alterados ficheiros por LastModifiedDate com fábrica de dados Azure](solution-template-copy-new-files-lastmodifieddate.md)

- [Copiar ficheiros de vários contentores com fábrica de dados Azure](solution-template-copy-files-multiple-containers.md)