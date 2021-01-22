---
title: Gerir servidor - Portal Azure - Base de Dados Azure para MariaDB
description: Saiba como gerir uma Base de Dados Azure para servidor MariaDB a partir do portal Azure.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: b492e04629f23c991c1c5d69dd5e2c472676d37a
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664406"
---
# <a name="manage-an-azure-database-for-mariadb-server-using-the-azure-portal"></a>Gerir uma base de dados Azure para servidor MariaDB utilizando o portal Azure
Este artigo mostra-lhe como gerir a sua Base de Dados Azure para servidores MariaDB. As tarefas de gestão incluem o dimensionamento de cálculo e armazenamento, o reset da palavra-passe de administração e a visualização de detalhes do servidor.

## <a name="sign-in"></a>Iniciar sessão
Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Criar um servidor
Visite o [quickstart](quickstart-create-mariadb-server-database-using-azure-portal.md) para aprender a criar e começar com uma Base de Dados Azure para o servidor MariaDB.

## <a name="scale-compute-and-storage"></a>Cálculo e armazenamento em escala

Após a criação do servidor, pode escalar entre os níveis de Finalidade Geral e Memória Otimizados à medida que as suas necessidades mudam. Também pode escalar o cálculo e a memória aumentando ou diminuindo os vCores. O armazenamento pode ser dimensionado (no entanto, não é possível reduzir o armazenamento).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Escala entre os níveis otimizados de propósito geral e memória

Pode escalar de Final geral para memória Otimizada e vice-versa. A alteração de e para o nível Básico após a criação do servidor não é suportada. 

1. Selecione o seu servidor no portal Azure. Selecione **o nível de preços,** localizado na secção **Definições.**

2. Selecione **Final Geral** ou **Memória Otimizada,** dependendo do que está a escalar. 

    ![O Screenshot mostra o portal Azure com o nível de preços selecionado e um valor de Memória Otimizada selecionada.](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > A mudança de camadas provoca o reinício do servidor.

4. Selecione **OK** para guardar alterações.


### <a name="scale-vcores-up-or-down"></a>Escala vCores para cima ou para baixo

1. Selecione o seu servidor no portal Azure. Selecione **o nível de preços,** localizado na secção **Definições.**

2. Altere a definição **vCore** movendo o slider para o valor pretendido.

    ![cálculo em escala](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > O escalonamento vCores provoca o reinício do servidor.

3. Selecione **OK** para guardar alterações.


### <a name="scale-storage-up"></a>Armazenamento em escala

1. Selecione o seu servidor no portal Azure. Selecione **o nível de preços,** localizado na secção **Definições.**

2. Altere a definição **de Armazenamento** movendo o deslizador para o valor pretendido.

    ![armazenamento em escala](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > O armazenamento não pode ser reduzido.

3. Selecione **OK** para guardar alterações.


## <a name="update-admin-password"></a>Atualizar senha de administração
Pode alterar a palavra-passe da função do administrador utilizando o portal Azure.

1. Selecione o seu servidor no portal Azure. Na janela **'Vista Geral'** **selecione redefinir a palavra-passe**.

   ![descrição geral](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Introduza uma nova senha e confirme a senha. A caixa de texto irá instruí-lo sobre os requisitos de complexidade da palavra-passe.

   ![A screenshot mostra a caixa de diálogo de palavra-passe redefinir com palavra-passe e confirmar a palavra-passe.](./media/howto-create-manage-server-portal/reset-password.png)

3. Selecione **OK** para guardar a nova senha.


## <a name="delete-a-server"></a>Excluir um servidor

Pode eliminar o seu servidor se já não precisar dele. 

1. Selecione o seu servidor no portal Azure. Na janela **'Vista Geral'** selecione **Eliminar**.

    ![eliminação](./media/howto-create-manage-server-portal/overview-delete.png)

2. Digite o nome do servidor na caixa de entrada para confirmar que este é o servidor que pretende eliminar.

    ![A screenshot mostra um diálogo que verifica se pretende eliminar uma base de dados, o que é irreversível.](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > Apagar um servidor é irreversível.

3. Selecione **Eliminar**.


## <a name="next-steps"></a>Próximos passos
- Saiba mais [sobre backups e restauro do servidor](howto-restore-server-portal.md)
- Saiba mais sobre [opções de afinação e monitorização na Base de Dados Azure para MariaDB](concepts-monitoring.md)
