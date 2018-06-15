---
title: 'Início Rápido: Dimensionar a computação no Azure SQL Data Warehouse - Portal do Azure| Microsoft Docs'
description: Dimensionar a computação do Azure SQL Data Warehouse no portal do Azure. Dimensionar a computação para um melhor desempenho ou a escalar a computação novamente para reduzir os custos.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.component: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: d707b9a4b7b683e75bb9313dae2611fd59dbb155
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182464"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Início Rápido: Dimensionar a computação do Azure SQL Data Warehouse no portal do Azure

Dimensionar a computação do Azure SQL Data Warehouse no portal do Azure. [Dimensionar a computação](sql-data-warehouse-manage-compute-overview.md) para um melhor desempenho ou a escalar a computação novamente para reduzir os custos. 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Pode dimensionar um armazém de dados que já tem ou utilizar o [Início Rápido: criar e Ligar - portal](create-data-warehouse-portal.md) para criar um armazém de dados com o nome **mySampleDataWarehouse**.  Este início rápido dimensiona **mySampleDataWarehouse**.

## <a name="scale-compute"></a>Dimensionar computação

No SQL Data Warehouse, pode aumentar ou diminuir os recursos de computação ao ajustar unidades do data warehouse. O [Criar e Ligar - portal](create-data-warehouse-portal.md) criou **mySampleDataWarehouse** e inicializou-o com 400 DWUs. Os seguintes passos ajustam as DWUs para **mySampleDataWarehouse**.

Para alterar as unidades do data warehouse:

1. Clique em **Armazéns de dados SQL** na página da esquerda do portal do Azure.
2. Selecione **mySampleDataWarehouse** na página de **Armazéns de dados SQL**. O armazém de dados é aberto.
3. Clique em **Escalar**.

    ![Clique em Escalar](media/quickstart-scale-compute-portal/click-scale.png)

2. No painel Escalar, mova o controlo de deslize para a esquerda ou direita para alterar a definição de DWU.

    ![Mova o Controlo de Deslize](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **sim** para confirmar ou **não** para cancelar.

    ![Clicar em Guardar](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>Passos seguintes
Agora já aprendeu como dimensionar a computação para o seu armazém de dados. Para saber mais sobre o Azure SQL Data Warehouse, avance para o tutorial para carregar dados.

> [!div class="nextstepaction"]
>[Carregar dados para o SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
