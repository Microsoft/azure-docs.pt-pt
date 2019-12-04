---
title: Aumento automático do armazenamento-portal do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como você pode habilitar o armazenamento de crescimento automático para o banco de dados do Azure para MySQL usando o portal do Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 7be3e7e1a0d2e48b37c319f67e3b382f825469df
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770616"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Crescimento automático do armazenamento no banco de dados do Azure para MySQL usando o portal do Azure
Este artigo descreve como você pode configurar um armazenamento de banco de dados do Azure para servidor MySQL para crescer sem afetar a carga de trabalho.

Quando um servidor atinge o limite de armazenamento alocado, o servidor é marcado como somente leitura. No entanto, se você habilitar o crescimento automático do armazenamento, o armazenamento do servidor aumentará para acomodar os dados em crescimento. Para servidores com menos de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado aumenta em 5 GB assim que o armazenamento livre está abaixo do maior que 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado aumenta em 5% quando o espaço livre de armazenamento está abaixo de 5% do tamanho de armazenamento provisionado. Os limites de armazenamento máximos conforme especificado [aqui](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) se aplicam.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa de:
- Um [banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Habilitar crescimento automático de armazenamento 

Siga estas etapas para definir o crescimento automático do armazenamento do servidor MySQL:

1. Na [portal do Azure](https://portal.azure.com/), selecione seu banco de dados do Azure para servidor MySQL existente.

2. Na página do servidor MySQL, em título de **configurações** , clique em **tipo de preço** para abrir a página tipo de preço.

3. Na seção crescimento automático, selecione **Sim** para habilitar o crescimento automático do armazenamento.

    ![Banco de dados do Azure para MySQL-Settings_Pricing_tier-aumento automático](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Clique em **OK** para guardar as alterações.

5. Uma notificação confirmará que o crescimento automático foi habilitado com êxito.

    ![Banco de dados do Azure para MySQL-êxito no crescimento automático](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [como criar alertas sobre métricas](howto-alert-on-metric.md).
