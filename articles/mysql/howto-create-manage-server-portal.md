---
title: Gerir o servidor - Portal Azure - Base de Dados Azure para o MySQL
description: Saiba como gerir uma Base de Dados Azure para o servidor MySQL a partir do portal Azure.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/26/2021
ms.openlocfilehash: 83876f77e0d7ffc0ae20bc5a545c1f18f53f4a8f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897990"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Gerir uma base de dados Azure para servidor MySQL utilizando o portal Azure

Este artigo mostra-lhe como gerir a sua Base de Dados Azure para servidores MySQL. As tarefas de gestão incluem o dimensionamento de cálculo e armazenamento, o reset da palavra-passe de administração e a visualização de detalhes do servidor.

> [!NOTE]
> Este artigo contém referências ao termo _escravo_, um termo que a Microsoft já não utiliza. Quando o termo for removido do software, vamos removê-lo deste artigo.
>

## <a name="sign-in"></a>Iniciar sessão

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Criar um servidor

Visite o [quickstart](quickstart-create-mysql-server-database-using-azure-portal.md) para aprender a criar e começar com uma Base de Dados Azure para o servidor MySQL.

## <a name="scale-compute-and-storage"></a>Cálculo e armazenamento em escala

Após a criação do servidor, pode escalar entre os níveis de Finalidade Geral e Memória Otimizados à medida que as suas necessidades mudam. Também pode escalar o cálculo e a memória aumentando ou diminuindo os vCores. O armazenamento pode ser dimensionado (no entanto, não é possível reduzir o armazenamento).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Escala entre os níveis otimizados de propósito geral e memória

Pode escalar de Final geral para memória Otimizada e vice-versa. A alteração de e para o nível Básico após a criação do servidor não é suportada.

1. Selecione o seu servidor no portal Azure. Selecione **o nível de preços,** localizado na secção **Definições.**

2. Selecione **Final Geral** ou **Memória Otimizada,** dependendo do que está a escalar.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Screenshot do portal Azure para escolher o nível básico, geral ou otimizado de memória na Base de Dados Azure para o MySQL":::

   > [!NOTE]
   > A mudança de camadas provoca o reinício do servidor.

3. Selecione **OK** para guardar alterações.

### <a name="scale-vcores-up-or-down"></a>Escala vCores para cima ou para baixo

1. Selecione o seu servidor no portal Azure. Selecione **o nível de preços,** localizado na secção **Definições.**

2. Altere a definição **vCore** movendo o slider para o valor pretendido.

    :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Screenshot do portal Azure para escolher a opção vCore na Base de Dados Azure para o MySQL":::

    > [!NOTE]
    > O escalonamento vCores provoca o reinício do servidor.

3. Selecione **OK** para guardar alterações.

### <a name="scale-storage-up"></a>Armazenamento em escala

1. Selecione o seu servidor no portal Azure. Selecione **o nível de preços,** localizado na secção **Definições.**

2. Altere a definição **de Armazenamento** movendo o deslizador para o valor pretendido.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Screenshot do portal Azure para escolher a escala de armazenamento na Base de Dados Azure para o MySQL":::

   > [!NOTE]
   > O armazenamento não pode ser reduzido.

3. Selecione **OK** para guardar alterações.

## <a name="update-admin-password"></a>Atualizar senha de administração

Pode alterar a palavra-passe da função do administrador utilizando o portal Azure.

1. Selecione o seu servidor no portal Azure. Na janela **'Vista Geral'** **selecione redefinir a palavra-passe**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Screenshot do portal Azure para redefinir a palavra-passe na Base de Dados Azure para o MySQL":::

2. Introduza uma nova senha e confirme a senha. A caixa de texto irá instruí-lo sobre os requisitos de complexidade da palavra-passe.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Screenshot do portal Azure para redefinir a sua palavra-passe e guardar na Base de Dados Azure para o MySQL":::

3. Selecione **OK** para guardar a nova senha.
 

> [!IMPORTANT]
> A redefinição da palavra-passe de administração do servidor irá redefinir automaticamente os privilégios de administração do servidor para o predefinimento. Considere redefinir a palavra-passe de administração do servidor se revogou acidentalmente um ou mais privilégios de administração do servidor.
   
> [!NOTE]
> O utilizador de administração do servidor tem os seguintes privilégios por predefinição: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, CREATE USER, EVENT, TRIGGER

## <a name="delete-a-server"></a>Excluir um servidor

Pode eliminar o seu servidor se já não precisar dele.

1. Selecione o seu servidor no portal Azure. Na janela **'Vista Geral'** selecione **Eliminar**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Screenshot do portal Azure para eliminar o servidor na Base de Dados Azure para o MySQL":::

2. Digite o nome do servidor na caixa de entrada para confirmar que este é o servidor que pretende eliminar.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Screenshot do portal Azure para confirmar o servidor apagar na Base de Dados Azure para o MySQL":::

   > [!NOTE]
   > Apagar um servidor é irreversível.

3. Selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

- Saiba mais [sobre backups e restauro do servidor](howto-restore-server-portal.md)
- Saiba mais sobre [afinação e monitorização em Azure Database for MySQL](concepts-monitoring.md)
