---
title: Armazenamento de crescimento automático - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode configurar o armazenamento de crescimento automático usando o portal Azure na Base de Dados Azure para PostgreSQL - Servidor Único
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: d49047e9b3a65da90f4a5ac9e4b3cb1e32bd59a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489987"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Armazenamento de crescimento automático utilizando o portal Azure na Base de Dados Azure para PostgreSQL - Servidor Único
Este artigo descreve como pode configurar uma Base de Dados Azure para o armazenamento de servidores PostgreSQL crescer sem afetar a carga de trabalho.

Quando um servidor atinge o limite de armazenamento atribuído, o servidor é marcado como apenas para leitura. No entanto, se permitir o crescimento automático do armazenamento, o armazenamento do servidor aumenta para acomodar os dados crescentes. Para servidores com armazenamento com menos de 100 GB, o tamanho de armazenamento provisionado é aumentado em 5 GB assim que o armazenamento gratuito for inferior a 1 GB ou 10% do armazenamento a provisionado. Para servidores com mais de 100 GB de armazenamento a provisionado, o tamanho de armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento gratuito é inferior a 5% do tamanho de armazenamento provisionado. Os limites máximos de armazenamento especificados [aqui](./concepts-pricing-tiers.md#storage) aplicam-se.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Permitir o crescimento de automóveis de armazenamento 

Siga estes passos para definir o armazenamento do servidor PostgreSQL crescer automaticamente:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor PostgreSQL.

2. Na página do servidor PostgreSQL, em **Definições,** clique no **nível de preços** para abrir a página do nível de preços.

3. Na secção **de crescimento automático,** selecione **Sim** para permitir o crescimento automático do armazenamento.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Base de Dados Azure para PostgreSQL - Settings_Pricing_tier - Crescimento automático":::

4. Clique em **OK** para guardar as alterações.

5. Uma notificação confirmará que o crescimento de automóveis foi ativado com sucesso.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Azure Database for PostgreSQL - sucesso de crescimento automático":::

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)