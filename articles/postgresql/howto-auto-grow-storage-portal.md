---
title: Aumento automático do armazenamento-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como você pode configurar o crescimento automático do armazenamento usando o portal do Azure no banco de dados do Azure para PostgreSQL-servidor único
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5e4f9d68d02edf456394d4ce10b7b6af5f8643d9
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769239"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Crescimento automático do armazenamento usando o portal do Azure no banco de dados do Azure para PostgreSQL-servidor único
Este artigo descreve como você pode configurar um banco de dados do Azure para armazenamento de servidor PostgreSQL para crescer sem afetar a carga de trabalho.

Quando um servidor atinge o limite de armazenamento alocado, o servidor é marcado como somente leitura. No entanto, se você habilitar o crescimento automático do armazenamento, o armazenamento do servidor aumentará para acomodar os dados em crescimento. Para servidores com menos de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado aumenta em 5 GB assim que o armazenamento livre está abaixo do maior que 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado aumenta em 5% quando o espaço livre de armazenamento está abaixo de 5% do tamanho de armazenamento provisionado. Os limites de armazenamento máximos conforme especificado [aqui](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) se aplicam.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa de:
- Um [banco de dados do Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Habilitar crescimento automático de armazenamento 

Siga estas etapas para definir o crescimento automático do armazenamento do servidor PostgreSQL:

1. No [portal do Azure](https://portal.azure.com/), selecione o servidor do banco de dados do Azure para PostgreSQL existente.

2. Na página do servidor PostgreSQL, em **configurações**, clique em **tipo de preço** para abrir a página tipo de preço.

3. Na seção **crescimento automático** , selecione **Sim** para habilitar o crescimento automático do armazenamento.

    ![Banco de dados do Azure para PostgreSQL-Settings_Pricing_tier-aumento automático](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Clique em **OK** para guardar as alterações.

5. Uma notificação confirmará que o crescimento automático foi habilitado com êxito.

    ![Banco de dados do Azure para PostgreSQL-êxito no crescimento automático](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [como criar alertas sobre métricas](howto-alert-on-metric.md).
