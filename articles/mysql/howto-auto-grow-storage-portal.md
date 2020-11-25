---
title: Armazenamento de crescimento automático - Portal Azure - Base de Dados Azure para MySQL
description: Este artigo descreve como pode permitir o armazenamento de crescimento automático para Azure Database para MySQL usando o portal Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 9355c2b2c780b6ccd63100e576850fe5566db3cc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998605"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Armazenamento de crescimento automático na Base de Dados Azure para o MySQL utilizando o portal Azure
Este artigo descreve como pode configurar uma Base de Dados Azure para o armazenamento do servidor MySQL crescer sem afetar a carga de trabalho.

Quando um servidor atinge o limite de armazenamento atribuído, o servidor é marcado como apenas para leitura. No entanto, se permitir o crescimento automático do armazenamento, o armazenamento do servidor aumenta para acomodar os dados crescentes. Para servidores com armazenamento com menos de 100 GB, o tamanho de armazenamento provisionado é aumentado em 5 GB assim que o armazenamento gratuito for inferior a 1 GB ou 10% do armazenamento a provisionado. Para servidores com mais de 100 GB de armazenamento a provisionado, o tamanho de armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento gratuito é inferior a 5% do tamanho de armazenamento provisionado. Os limites máximos de armazenamento especificados [aqui](./concepts-pricing-tiers.md#storage) aplicam-se.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Permitir o crescimento de automóveis de armazenamento 

Siga estes passos para definir o crescimento automático do armazenamento do servidor MySQL:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MySQL.

2. Na página do servidor MySQL, em posição **De Definições,** clique no **nível de preços** para abrir a página de nível de preços.

3. Na secção de crescimento automático, selecione **Sim** para permitir o crescimento automático do armazenamento.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Base de Dados Azure para MySQL - Settings_Pricing_tier - Crescimento automático":::

4. Clique em **OK** para guardar as alterações.

5. Uma notificação confirmará que o crescimento de automóveis foi ativado com sucesso.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-success.png" alt-text="Azure Database for MySQL - sucesso de crescimento automático":::

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)