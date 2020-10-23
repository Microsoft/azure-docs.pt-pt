---
title: Armazenamento de crescimento automático - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como pode permitir o armazenamento de crescimento automático para Azure Database para MariaDB usando o portal Azure
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 56bc6c0e7a294f80e946a2eb0c84e5d0bd49d0f5
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426071"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Armazenamento de crescimento automático na Base de Dados Azure para MariaDB usando o portal Azure
Este artigo descreve como pode configurar uma Base de Dados Azure para o armazenamento do servidor MariaDB crescer sem afetar a carga de trabalho.

Quando um servidor atinge o limite de armazenamento atribuído, o servidor é marcado como apenas para leitura. No entanto, se permitir o crescimento automático do armazenamento, o armazenamento do servidor aumenta para acomodar os dados crescentes. Para servidores com armazenamento com menos de 100 GB, o tamanho de armazenamento provisionado é aumentado em 5 GB assim que o armazenamento gratuito for inferior a 1 GB ou 10% do armazenamento a provisionado. Para servidores com mais de 100 GB de armazenamento a provisionado, o tamanho de armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento gratuito é inferior a 5% do tamanho de armazenamento provisionado. Os limites máximos de armazenamento especificados [aqui](concepts-pricing-tiers.md#storage) aplicam-se.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para servidor MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Permitir o crescimento de automóveis de armazenamento 

Siga estes passos para definir o crescimento automático do armazenamento do servidor MariaDB:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MariaDB.

2. Na página do servidor MariaDB, em posição **Definições,** clique no **nível de preços** para abrir a página do nível de preços.

3. Na secção de crescimento automático, selecione **Sim** para permitir o crescimento automático do armazenamento.

    ![Azure Database for MariaDB - Settings_Pricing_tier - Auto-crescimento](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Clique em **OK** para guardar as alterações.

5. Uma notificação confirmará que o crescimento de automóveis foi ativado com sucesso.

    ![Azure Database for MariaDB - sucesso de crescimento automático](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-metric.md)
