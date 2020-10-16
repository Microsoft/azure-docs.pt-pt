---
title: Operações de escala - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Este artigo descreve como executar operações de escala na Base de Dados Azure para PostgreSQL através do portal Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 1542bba53b51ffdf2129953a81e5d13975ade434
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939247"
---
# <a name="scale-operations-in-flexible-server"></a>Operações de escala em Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Este artigo fornece medidas para realizar operações de escala para computação e armazenamento. Poderá alterar os seus níveis de cálculo entre SKUs reluníveis, fins gerais e memória otimizados, incluindo a escolha do número de vCores adequados para executar a sua aplicação. Também pode aumentar o seu armazenamento. Os IOPS esperados são apresentados com base no nível de cálculo, vCores e na capacidade de armazenamento. A estimativa de custos também é mostrada com base na sua seleção.

> [!IMPORTANT]
> Não pode reduzir o armazenamento.

## <a name="pre-requisites"></a>Pré-requisitos

Para completar este guia, precisa:

-   Deve ter uma base de dados Azure para PostgreSQL - Servidor Flexível. O mesmo procedimento também é aplicável para servidor flexível configurado com redundância de zona.
> [!IMPORTANT]
> Quando configurado com alta disponibilidade, não é possível escolher SKU rebentado. Durante a operação de escala, o standby é primeiro dimensionado para o tamanho desejado, o servidor primário é falhado por cima, e o primário é dimensionado. 

## <a name="scaling-the-compute-tier-and-size"></a>Escalonamento do nível e tamanho do cálculo

Siga estes passos para escolher o nível de cálculo.
 
1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor flexível de onde pretende restaurar a cópia de segurança.

2.  Clique **em Compute+storage**.

3.  É apresentada uma página com as definições atuais.
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="visão compute+armazenamento":::

4.  Pode escolher a classe computacional entre níveis de limpeza, finalidade geral e memória otimizados.
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="visão compute+armazenamento":::


5.  Se estiver bem com os vCores padrão e os tamanhos de memória, pode saltar o passo seguinte.

6.  Se quiser alterar o número de vCores, pode clicar no drop-down do tamanho do **Compute** e clicar no número desejado de vCores/Memória da lista.
    
    - Nível computável burstable: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="visão compute+armazenamento":::

    - Nível de cálculo para fins gerais: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="visão compute+armazenamento":::

    - Nível de computação otimizado da :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="visão compute+armazenamento":::

7.  Clique em **Guardar**. 
8.  Verá uma mensagem de confirmação. Clique **em OK** se quiser prosseguir. 
9.  Uma notificação sobre a operação de escala em curso.


## <a name="scaling-storage-size"></a>Tamanho do armazenamento de escala

Siga estes passos para aumentar o seu tamanho de armazenamento.

1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor flexível para o qual pretende aumentar o tamanho do armazenamento.
2.  Clique **em Compute+storage**.

3.  É apresentada uma página com as definições atuais.
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="visão compute+armazenamento":::
4.  O **tamanho do armazenamento de** campo em GiB com uma barra de diapositivos é mostrado com o tamanho atual.

5.  Deslize a barra para o tamanho desejado. É indicado o número de PIOS correspondente. O IOPS depende do nível e tamanho do cálculo. A informação sobre os custos também é mostrada. 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="visão compute+armazenamento":::

6.  Se for bom com o tamanho do armazenamento, clique em **Guardar**. 
7.  Verá uma mensagem de confirmação. Clique **em OK** se quiser prosseguir. 
8.  Uma notificação sobre a operação de escala em curso.

## <a name="next-steps"></a>Passos seguintes

-   Conheça a [continuidade do negócio](./concepts-business-continuity.md)
-   Saiba mais sobre [a alta disponibilidade](./concepts-high-availability.md)
-   Saiba mais [sobre backup e recuperação](./concepts-backup-restore.md)
