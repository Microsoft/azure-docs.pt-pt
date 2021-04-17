---
title: Restaurar uma piscina SQL dedicada existente
description: Como orientar para restaurar uma piscina SQL dedicada existente.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f2fb6f809794781559683907a806e6d30ca9bed6
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567983"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Restaurar uma piscina SQL dedicada existente

Neste artigo, você aprende a restaurar uma piscina SQL dedicada existente em Azure Synapse Analytics usando o portal Azure e o Synapse Studio. Este artigo aplica-se tanto a restauros como a geo-restauros. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Restaurar uma piscina SQL dedicada existente através do Estúdio Synapse

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue para o seu espaço de trabalho synapse. 
3. Under Getting -> Open Synapse Studio, **selecione Open**.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. No painel de navegação da mão esquerda, selecione **Dados**.
5. **Selecione Gerir piscinas**. 
6. **Selecione + Novo** para criar uma nova piscina SQL dedicada. 
7. No separador Definições Adicionais, selecione um Ponto de Restauro para restaurar. 

    Se quiser realizar um geo-restauro, selecione o espaço de trabalho e a piscina SQL dedicada que pretende recuperar. 

8. Selecione pontos de **restauro automáticos** ou **pontos de restauro definidos pelo utilizador**. 

    ![Pontos de restauro](../media/sql-pools/restore-point.PNG)

    Se a piscina SQL dedicada não tiver pontos de restauro automáticos, aguarde algumas horas ou crie um ponto de restauro definido pelo utilizador antes de restaurar. Para User-Defined Restaurar Pontos, selecione um existente ou crie um novo.

    Se estiver a restaurar uma geo-cópia de segurança, basta selecionar o espaço de trabalho localizado na região de origem e a piscina de SQL dedicada que pretende restaurar. 

9. Selecione **Rever + Criar**.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Restaurar uma piscina SQL dedicada existente através do portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue até à piscina dedicada SQL a partir daí restaurada.
3. Na parte superior da lâmina overview, **selecione Restaurar**.

    ![ Descrição geral do Restauro](../media/sql-pools/restore-sqlpool-01.png)

4. Selecione pontos de **restauro automáticos** ou **pontos de restauro definidos pelo utilizador**. 

    Se a piscina SQL dedicada não tiver pontos de restauro automáticos, aguarde algumas horas ou crie um ponto de restauro definido pelo utilizador antes de restaurar. 

    Se quiser realizar um geo-restauro, selecione o espaço de trabalho e a piscina SQL dedicada que pretende recuperar. 

5. Selecione **Rever + Criar**.

## <a name="next-steps"></a>Passos Seguintes

- [Criar um ponto de restauro](sqlpool-create-restore-point.md)
