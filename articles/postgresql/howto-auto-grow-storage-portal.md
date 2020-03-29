---
title: Armazenamento de cultivo automático - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode configurar o armazenamento de modo auto-crescer usando o portal Azure em Base de Dados Azure para PostgreSQL - Servidor Único
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5e4f9d68d02edf456394d4ce10b7b6af5f8643d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769239"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Armazenamento de cultivo automático usando o portal Azure em Base de Dados Azure para PostgreSQL - Servidor Único
Este artigo descreve como pode configurar uma Base de Dados Azure para o armazenamento do servidor PostgreSQL para crescer sem afetar a carga de trabalho.

Quando um servidor atinge o limite de armazenamento atribuído, o servidor é marcado como apenas para leitura. No entanto, se permitir o aumento automático do armazenamento, o armazenamento do servidor aumenta para acomodar os dados crescentes. Para servidores com armazenamento aprovisionado inferior a 100 GB, o tamanho de armazenamento provisionado é aumentado em 5 GB assim que o armazenamento gratuito é inferior ao maior de 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento gratuito é inferior a 5% do tamanho de armazenamento provisionado. [Limites](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) máximos de armazenamento conforme aqui especificado se aplicam.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia de como orientar, precisa de:
- Uma [base de dados Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Ativar o armazenamento de automóveis 

Siga estes passos para definir o armazenamento automático do servidor PostgreSQL:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor PostgreSQL.

2. Na página do servidor PostgreSQL, em **Definições,** clique no **nível** de preços para abrir a página do nível de preços.

3. Na secção **de crescimento automático,** selecione **Sim** para permitir o crescimento automático do armazenamento.

    ![Base de Dados Azure para PostgreSQL - Settings_Pricing_tier - Auto-crescimento](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Clique em **OK** para guardar as alterações.

5. Uma notificação confirmará que o crescimento automático foi ativado com sucesso.

    ![Base de Dados Azure para PostgreSQL - sucesso de crescimento automático](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)
