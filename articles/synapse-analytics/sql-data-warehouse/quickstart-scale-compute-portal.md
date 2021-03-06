---
title: 'Quickstart: Cálculo de escala para piscina Sinaapse SQL (portal Azure)'
description: Pode escalar o cálculo para o pool Synapse SQL (data warehouse) utilizando o portal Azure.
services: synapse-analytics
author: Antvgski
ms.author: anvang
manager: craigg
ms.reviewer: jrasnick
ms.date: 04/28/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: aa339274a1ff68764fa5573d9c031e84f290e57c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534226"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Quickstart: Computação em escala para piscina Sinaapse SQL com o portal Azure

Pode escalar o cálculo para o pool Synapse SQL (data warehouse) utilizando o portal Azure. [Dimensionar a computação](sql-data-warehouse-manage-compute-overview.md) para um melhor desempenho ou a escalar a computação novamente para reduzir os custos. 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Você pode escalar uma piscina SQL que você já tem ou usar [Quickstart: criar e ligar - portal](create-data-warehouse-portal.md) para criar uma piscina SQL chamada **mySampleDataWarehouse**. Este início rápido dimensiona **mySampleDataWarehouse**.

>[!IMPORTANT] 
>A sua piscina SQL deve estar online à escala. 

## <a name="scale-compute"></a>Dimensionar computação

Os recursos de cálculo de piscina SQL podem ser dimensionados aumentando ou diminuindo unidades de armazém de dados. O [Quickstart: criar e ligar - portal](create-data-warehouse-portal.md) criou o **mySampleDataWarehouse** e iniciaisizou-o com 400 DWUs. Os seguintes passos ajustam as DWUs para **mySampleDataWarehouse**.

Para alterar as unidades do data warehouse:

1. Clique em **Azure Synapse Analytics (anteriormente SQL DW)** na página esquerda do portal Azure.
2. Selecione **mySampleDataWarehouse** a partir da página **Azure Synapse Analytics (anteriormente SQL DW).** A piscina SQL abre.
3. Clique em **Escalar**.

    ![Clique em Escalar](./media/quickstart-scale-compute-portal/click-scale.png)

2. No painel Escalar, mova o controlo de deslize para a esquerda ou direita para alterar a definição de DWU. Em seguida, selecione a escala.

    ![Mova o Controlo de Deslize](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a piscina SQL, continue os dados de Carga no tutorial [da piscina SQL.](./load-data-from-azure-blob-storage-using-copy.md)
