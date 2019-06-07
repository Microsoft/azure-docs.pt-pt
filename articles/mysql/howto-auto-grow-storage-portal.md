---
title: Automática aumentar o armazenamento na base de dados do Azure para MySQL com o portal do Azure
description: Este artigo descreve como pode permitir automática aumentar o armazenamento da base de dados do Azure para MySQL através do portal do Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5343475f38dd5389d6b0e266ff7167925cd38d71
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676792"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Automática aumentar o armazenamento na base de dados do Azure para MySQL com o portal do Azure
Este artigo descreve como pode configurar uma base de dados do Azure para armazenamento do servidor MySQL cresça sem afetar a carga de trabalho.

Quando um servidor atinge o limite de armazenamento atribuído, o servidor é marcado como só de leitura. No entanto, se ativar o aumento automático de armazenamento, o armazenamento de servidor aumenta para acomodar os dados de cada vez mais. Para servidores com menos do que 100 GB aprovisionado de armazenamento, o tamanho de armazenamento aprovisionado é aumentado em 5 GB, assim que o armazenamento livre é inferior o maior de 1 GB ou 10% do armazenamento aprovisionado. Para servidores com mais de 100 GB de armazenamento de aprovisionamento, o tamanho de armazenamento aprovisionado é aumentado por 5% quando o espaço de armazenamento livre é inferior a 5% do tamanho do armazenamento aprovisionado. Armazenamento máximo limita conforme especificado [aqui](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) aplicam-se.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de procedimentos, terá de:
- Um [base de dados do Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Ativar automática de armazenamento crescer 

Siga estas etapas para configurar o MySQL crescer automática de armazenamento do servidor:

1. Na [portal do Azure](https://portal.azure.com/), selecione a sua base de dados do Azure para o servidor MySQL.

2. Na página do servidor MySQL, sob **configurações** , clique em **escalão de preço** para abrir a página de escalão de preço.

3. Na secção de aumento automático, selecione **Sim** para ativar a crescer automática de armazenamento.

    ![Base de dados do Azure para MySQL - Settings_Pricing_tier - aumento automático](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Clique em **OK** para guardar as alterações.

5. Uma notificação confirma aumentar esse automática foi ativada com êxito.

    ![Base de dados do Azure para MySQL - êxito de aumento automático](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [como criar alertas em métricas](howto-alert-on-metric.md).
