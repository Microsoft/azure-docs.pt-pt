---
title: Gerenciar servidor-portal do Azure-banco de dados do Azure para MySQL
description: Saiba como gerenciar um servidor de banco de dados do Azure para MySQL do portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 553234410a7b8b798d26f0e2fee6132a2602b1cc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770514"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Gerenciar um servidor de banco de dados do Azure para MySQL usando o portal do Azure
Este artigo mostra como gerenciar seu banco de dados afire para servidores MySQL. As tarefas de gerenciamento incluem dimensionamento de computação e armazenamento, redefinição de senha de administrador e detalhes do servidor de exibição.

## <a name="sign-in"></a>Iniciar sessão
Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Criar um servidor
Visite o guia de [início rápido](quickstart-create-mysql-server-database-using-azure-portal.md) para saber como criar e começar a usar um banco de dados do Azure para servidor MySQL.

## <a name="scale-compute-and-storage"></a>Dimensionar a computação e o armazenamento

Após a criação do servidor, você pode dimensionar entre as camadas de Uso Geral e com otimização de memória à medida que suas necessidades mudam. Você também pode dimensionar a computação e a memória aumentando ou diminuindo o vCores. O armazenamento pode ser dimensionado verticalmente (no entanto, você não pode dimensionar o armazenamento verticalmente).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Dimensionar entre as camadas de Uso Geral e com otimização de memória

Você pode dimensionar de Uso Geral para a memória otimizada e vice-versa. Não há suporte para a alteração de e para a camada básica após a criação do servidor. 

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Selecione **uso geral** ou **memória otimizada**, dependendo do que você está dimensionando. 

    ![alterar-camada de preços](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > A alteração de camadas causa uma reinicialização do servidor.

4. Selecione **OK** para salvar as alterações.


### <a name="scale-vcores-up-or-down"></a>Dimensionar vCores para cima ou para baixo

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Altere a configuração de **vCore** movendo o controle deslizante para o valor desejado.

    ![escala-computação](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > O dimensionamento de vCores causa uma reinicialização do servidor.

3. Selecione **OK** para salvar as alterações.


### <a name="scale-storage-up"></a>Escalar o armazenamento verticalmente

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Altere a configuração de **armazenamento** movendo o controle deslizante para cima até o valor desejado.

    ![armazenamento em escala](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > O armazenamento não pode ser reduzido verticalmente.

3. Selecione **OK** para salvar as alterações.


## <a name="update-admin-password"></a>Atualizar senha do administrador
Você pode alterar a senha da função de administrador usando o portal do Azure.

1. Selecione o servidor na portal do Azure. Na janela **visão geral** , selecione **Redefinir senha**.

   ![descrição geral](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Insira uma nova senha e confirme a senha. A caixa de texto solicitará os requisitos de complexidade de senha.

   ![Redefinir senha](./media/howto-create-manage-server-portal/reset-password.png)

3. Selecione **OK** para salvar a nova senha.


## <a name="delete-a-server"></a>Excluir um servidor

Você pode excluir o servidor se não precisar mais dele. 

1. Selecione o servidor na portal do Azure. Na janela **visão geral** , selecione **excluir**.

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Digite o nome do servidor na caixa de entrada para confirmar que este é o servidor que você deseja excluir.

    ![confirmar-excluir](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > A exclusão de um servidor é irreversível.

3. Selecione **Eliminar**.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [backups e restauração do servidor](howto-restore-server-portal.md)
- Saiba mais sobre [as opções de ajuste e monitoramento no banco de dados do Azure para MySQL](concepts-monitoring.md)