---
title: 'Início rápido: Escalar horizontalmente a computação no Azure SQL Data Warehouse-portal do Azure | Microsoft Docs'
description: Dimensionar a computação do Azure SQL Data Warehouse no portal do Azure. Dimensionar a computação para um melhor desempenho ou a escalar a computação novamente para reduzir os custos.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 4197d7a4724199f8c42fc09a6d1ed18af46cc4f8
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575355"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Início rápido: Dimensionar a computação no Azure SQL Data Warehouse no portal do Azure

Dimensionar a computação do Azure SQL Data Warehouse no portal do Azure. [Dimensionar a computação](sql-data-warehouse-manage-compute-overview.md) para um melhor desempenho ou a escalar a computação novamente para reduzir os custos. 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Você pode dimensionar um data warehouse que você já tem ou usar o [início rápido: criar e conectar – portal](create-data-warehouse-portal.md) para criar um data warehouse chamado **mySampleDataWarehouse**.  Este início rápido dimensiona **mySampleDataWarehouse**.

>[!Note]
>Seu data warehouse deve estar online para ser dimensionado. 

## <a name="scale-compute"></a>Dimensionar computação

SQL Data Warehouse recursos de computação podem ser dimensionados aumentando ou diminuindo as unidades de data warehouse. O início rápido do [Create and Connect-portal] (Create-data-warehouse-portal.md) criou **mySampleDataWarehouse** e o inicializou com 400 DWUs. Os seguintes passos ajustam as DWUs para **mySampleDataWarehouse**.

Para alterar as unidades do data warehouse:

1. Clique em **Armazéns de dados SQL** na página da esquerda do portal do Azure.
2. Selecione **mySampleDataWarehouse** na página de **Armazéns de dados SQL**. O armazém de dados é aberto.
3. Clique em **Escalar**.

    ![Clique em Escalar](media/quickstart-scale-compute-portal/click-scale.png)

2. No painel Escalar, mova o controlo de deslize para a esquerda ou direita para alterar a definição de DWU.

    ![Mova o Controlo de Deslize](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **sim** para confirmar ou **não** para cancelar.

    ![Clicar em Guardar](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>Passos Seguintes
Agora você aprendeu a dimensionar a computação para seu data warehouse. Para saber mais sobre o Azure SQL Data Warehouse, avance para o tutorial para carregar dados.

> [!div class="nextstepaction"]
>[Carregar dados em um SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
