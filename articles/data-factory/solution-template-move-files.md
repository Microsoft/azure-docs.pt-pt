---
title: Mover ficheiros entre o armazenamento baseado em ficheiros
description: Aprenda a usar um modelo de solução para mover ficheiros entre o armazenamento baseado em ficheiros utilizando a Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: c88f2d25046ee017fccd2cee6e951be72d4dda91
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361949"
---
# <a name="move-files-with-azure-data-factory"></a>Mover ficheiros com a Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A atividade de cópia ADF tem suporte incorporado no cenário de "mover" ao copiar ficheiros binários entre lojas de armazenamento.  A forma de o permitir é definir "deleteFilesAfterCompletion" como verdadeiro na atividade de cópia. Ao fazê-lo, a atividade de cópia eliminará ficheiros da loja de fontes de dados após a conclusão do trabalho. 

Este artigo descreve um modelo de solução como outra abordagem alavancando o fluxo de controlo flexível ADF mais a atividade de cópia e eliminando a atividade para alcançar o mesmo cenário. Um dos cenários comuns de utilização deste modelo: Os ficheiros são continuamente deixados para uma pasta de aterragem da sua loja de origem. Ao criar um gatilho de horário, o gasoduto ADF pode mover periodicamente esses ficheiros da fonte para a loja de destino.  A forma como o gasoduto ADF consegue "ficheiros em movimento" é obter os ficheiros da pasta de aterragem, copiando cada um deles para outra pasta na loja de destino e, em seguida, eliminando os mesmos ficheiros da pasta de aterragem na loja de origem.

> [!NOTE]
> Tenha em atenção que este modelo foi concebido para mover ficheiros em vez de mover pastas.  Se pretender mover a pasta alterando o conjunto de dados para que contenha apenas um caminho de pasta e, em seguida, utilizar a atividade da cópia e eliminar a atividade para fazer referência ao mesmo conjunto de dados que representa uma pasta, tem de ter muito cuidado. É porque tem de se certificar de que não haverá novos ficheiros a chegar à pasta entre a operação de cópia e a eliminação da operação. Se houver novos ficheiros a chegar à pasta no momento em que a sua atividade de cópia acabou de completar a função de cópia, mas a atividade Delete não foi encarada, é possível que a atividade Desaclamar este novo ficheiro que não tenha sido copiado para o destino, eliminando ainda toda a pasta.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo obtém os ficheiros da sua loja baseada em ficheiros de origem. Em seguida, move cada um deles para a loja de destino.

O modelo contém cinco atividades:
- **A GetMetadata** obtém a lista de objetos, incluindo os ficheiros e sub-dobradores da sua pasta na loja de origem. Não recuperará os objetos de forma recorrente. 
- **Filtrar** a lista de objetos da atividade **GetMetadata** para selecionar apenas os ficheiros. 
- **ForEach** obtém a lista de ficheiros da atividade **do Filtro** e, em seguida, itera sobre a lista e passa cada ficheiro para a atividade Copy e Apagar.
- **Copie** um ficheiro da fonte para a loja de destino.
- **Eliminar** elimina o mesmo ficheiro da loja de origem.

O modelo define quatro parâmetros:
- *SourceStore_Location* é o caminho da pasta da sua loja de origem de onde pretende mover ficheiros. 
- *SourceStore_Directory* é o caminho da sub-dobradeira da sua loja de origem de onde pretende mover ficheiros.
- *DestinationStore_Location* é o caminho da pasta da sua loja de destino para onde pretende mover ficheiros. 
- *DestinationStore_Directory* é o caminho da sub-dobradeira da sua loja de destino para onde pretende mover ficheiros.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá ao modelo **de ficheiros Move.** Selecione a ligação existente ou crie uma **nova** ligação à sua loja de ficheiros de origem de onde pretende mover ficheiros. Esteja ciente de que **DataSource_Folder** e **DataSource_File** referem-se à mesma ligação da sua loja de ficheiros de origem.

    ![Criar uma nova ligação à fonte](media/solution-template-move-files/move-files1.png)

2. Selecione a ligação existente ou crie uma **nova** ligação à sua loja de ficheiros de destino para onde pretende mover ficheiros.

    ![Criar uma nova ligação ao destino](media/solution-template-move-files/move-files2.png)

3. Selecione **Utilize este separador de modelo.**
    
4. Verá o oleoduto, como no seguinte exemplo:

    ![Mostrar o oleoduto](media/solution-template-move-files/move-files4.png)

5. Selecione **Debug,** introduza os **parâmetros** e, em seguida, selecione **Terminar**.   Os parâmetros são o caminho da pasta para onde pretende mover ficheiros e o caminho da pasta para onde pretende mover ficheiros. 

    ![Executar o pipeline](media/solution-template-move-files/move-files5.png)

6. Reveja o resultado.

    ![Reveja o resultado](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Passos seguintes

- [Copie ficheiros novos e alterados por LastModifiedDate com Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Copiar ficheiros de vários contentores com Azure Data Factory](solution-template-copy-files-multiple-containers.md)