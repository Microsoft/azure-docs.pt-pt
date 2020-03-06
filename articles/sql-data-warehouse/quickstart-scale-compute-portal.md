---
title: 'Quickstart: Scale compute - Portal Azure '
description: Computação em escala na piscina SQL no portal Azure. Aumente horizontalmente a computação para obter um melhor desempenho ou dimensione a computação novamente para reduzir os custos.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7463849223fdf81466237c7d0c912763988e80e6
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200358"
---
# <a name="quickstart-scale-compute-in-azure-synapse-analytics-sql-pool-in-the-azure-portal"></a>Quickstart: Scale compute in Azure Synapse Analytics SQL pool in the Azure portal

Computação em escala na piscina SQL no portal Azure. [Dimensionar a computação](sql-data-warehouse-manage-compute-overview.md) para um melhor desempenho ou a escalar a computação novamente para reduzir os custos. 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Você pode escalar um pool SQL que você já tem, ou usar [Quickstart: criar e conectar - portal](create-data-warehouse-portal.md) para criar uma piscina SQL chamada **mySampleDataWarehouse**.  Este início rápido dimensiona **mySampleDataWarehouse**.

>[!Note]
>A sua piscina SQL deve estar on-line à escala. 

## <a name="scale-compute"></a>Dimensionar computação

Os recursos de computação de piscina SQL podem ser dimensionados através do aumento ou diminuição das unidades de armazém de dados. O quickstart [criar e ligar - portal] quickstart (create-data-warehouse-portal.md) criou o **mySampleDataWarehouse** e ininicializou-o com 400 DWUs. Os seguintes passos ajustam as DWUs para **mySampleDataWarehouse**.

Para alterar as unidades do data warehouse:

1. Clique em **Azure Synapse Analytics (anteriormente SQL DW)** na página esquerda do portal Azure.
2. Selecione **mySampleDataWarehouse** da página **Azure Synapse Analytics (anteriormente SQL DW).** A piscina SQL abre.
3. Clique em **Escalar**.

    ![Clique em Escalar](media/quickstart-scale-compute-portal/click-scale.png)

2. No painel Escalar, mova o controlo de deslize para a esquerda ou direita para alterar a definição de DWU. Em seguida, selecione a escala.

    ![Mova o Controlo de Deslize](media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Passos seguintes
Agora aprendeu a escalar a computação para piscina SQL. Para saber mais sobre o pool SQL, continue ao tutorial para carregar dados.

> [!div class="nextstepaction"]
>[Carregue os dados no pool SQL](load-data-from-azure-blob-storage-using-polybase.md)
