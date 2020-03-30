---
title: Gerir base de dados Azure para postgreSQL - portal Azure
description: Saiba como gerir uma Base de Dados Azure para servidor PostgreSQL a partir do portal Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: bcddd64afca29ac9fdd5d284fc8f809ff9e2477d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535694"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Gerir uma Base de Dados Azure para servidor PostgreSQL utilizando o portal Azure
Este artigo mostra-lhe como gerir a sua Base de Dados Azure para servidores PostgreSQL. As tarefas de gestão incluem a análise e o dimensionamento de armazenamento, o reset da palavra-passe de administrador e os detalhes do servidor de visualização.

## <a name="sign-in"></a>Iniciar sessão
Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Criar um servidor
Visite o [quickstart](quickstart-create-server-database-portal.md) para aprender a criar e começar com uma Base de Dados Azure para servidor PostgreSQL.

## <a name="scale-compute-and-storage"></a>Cálculo de escala e armazenamento

Após a criação do servidor, pode escalar entre os níveis De Propósito Geral e Memória Otimizados à medida que as suas necessidades mudam. Também pode escalar a computação e a memória aumentando ou diminuindo os vCores. O armazenamento pode ser dimensionado (no entanto, não é possível reduzir o armazenamento).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Escala entre propósito geral e níveis otimizados de memória

Pode escalar de Propósito Geral para Memória Otimizada e vice-versa. A alteração de e para o nível Básico após a criação do servidor não é suportada. 

1. Selecione o seu servidor no portal Azure. Selecione **o nível**de preços, localizado na secção **Definições.**

2. Selecione **Propósito Geral** ou **Memória Otimizada,** dependendo do que está a escalonar. 

    ![mudança de preços-nível](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > Mudar de nível provoca o reinício do servidor.

4. Selecione **OK** para guardar alterações.


### <a name="scale-vcores-up-or-down"></a>Escala vCores para cima ou para baixo

1. Selecione o seu servidor no portal Azure. Selecione **o nível**de preços, localizado na secção **Definições.**

2. Altere a definição **vCore** movendo o slider para o valor desejado.

    ![escala-computação](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > A escalação de vCores provoca o reinício do servidor.

3. Selecione **OK** para guardar alterações.


### <a name="scale-storage-up"></a>Armazenamento em escala para cima

1. Selecione o seu servidor no portal Azure. Selecione **o nível**de preços, localizado na secção **Definições.**

2. Altere a definição de **Armazenamento** movendo o slider até ao valor desejado.

    ![armazenamento em escala](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > O armazenamento não pode ser reduzido.

3. Selecione **OK** para guardar alterações.


## <a name="update-admin-password"></a>Atualizar a palavra-passe do administrador
Pode alterar a palavra-passe do administrador utilizando o portal Azure.

1. Selecione o seu servidor no portal Azure. Na janela **'Visão geral'** selecione **a palavra-passe De reset**.

   ![descrição geral](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Introduza uma nova senha e confirme a senha. A caixa de texto irá instruí-lo sobre os requisitos de complexidade da palavra-passe.

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Selecione **OK** para guardar a nova senha.


## <a name="delete-a-server"></a>Eliminar um servidor

Pode eliminar o seu servidor se já não precisar. 

1. Selecione o seu servidor no portal Azure. Na janela **'Visão Geral'** selecione **Delete**.

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Digite o nome do servidor na caixa de entrada para confirmar que este é o servidor que pretende eliminar.

    ![confirmar-eliminar](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > Apagar um servidor é irreversível.

3. Selecione **Eliminar**.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [backups e restauro](howto-restore-server-portal.md) do servidor
- Saiba mais sobre opções de [afinação e monitorização na Base de Dados Azure para PostgreSQL](concepts-monitoring.md)
