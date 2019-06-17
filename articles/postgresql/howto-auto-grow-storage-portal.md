---
title: Automática aumentar o armazenamento no portal do Azure na base de dados do Azure para PostgreSQL - servidor único
description: Este artigo descreve como pode permitir automática aumentar o armazenamento no portal do Azure na base de dados do Azure para PostegreSQL - servidor único
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9f88fe3e8a30dd80c5331bd6c8ea7aec401c6fb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676762"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Automática aumentar o armazenamento no portal do Azure na base de dados do Azure para PostgreSQL - servidor único
Este artigo descreve como pode configurar uma base de dados do Azure para armazenamento de servidor PostgreSQL cresça sem afetar a carga de trabalho.

Quando um servidor atinge o limite de armazenamento atribuído, o servidor é marcado como só de leitura. No entanto, se ativar o aumento automático de armazenamento, o armazenamento de servidor aumenta para acomodar os dados de cada vez mais. Para servidores com menos do que 100 GB aprovisionado de armazenamento, o tamanho de armazenamento aprovisionado é aumentado em 5 GB, assim que o armazenamento livre é inferior o maior de 1 GB ou 10% do armazenamento aprovisionado. Para servidores com mais de 100 GB de armazenamento de aprovisionamento, o tamanho de armazenamento aprovisionado é aumentado por 5% quando o espaço de armazenamento livre é inferior a 5% do tamanho do armazenamento aprovisionado. Armazenamento máximo limita conforme especificado [aqui](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) aplicam-se.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de procedimentos, terá de:
- Um [base de dados do Azure para o servidor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Ativar automática de armazenamento crescer 

Siga estas etapas para configurar o PostgreSQL crescer automática de armazenamento do servidor:

1. Na [portal do Azure](https://portal.azure.com/), selecione a sua base de dados do Azure para o servidor PostgreSQL.

2. Na página do servidor PostgreSQL, sob **configurações**, clique em **escalão de preço** para abrir a página de escalão de preço.

3. Na **aumento automático** secção, selecione **Sim** para ativar a crescer automática de armazenamento.

    ![Base de dados do Azure para PostgreSQL - Settings_Pricing_tier - aumento automático](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Clique em **OK** para guardar as alterações.

5. Uma notificação confirma aumentar esse automática foi ativada com êxito.

    ![Base de dados do Azure para PostgreSQL - êxito de aumento automático](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [como criar alertas em métricas](howto-alert-on-metric.md).
