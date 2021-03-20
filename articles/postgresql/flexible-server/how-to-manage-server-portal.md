---
title: Gerir o servidor - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Saiba como gerir uma Base de Dados Azure para PostgreSQL - Servidor Flexível a partir do portal Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc
ms.openlocfilehash: 1ac418d855696138341115412dc7e2601d4cf3a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91961413"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Gerir uma base de dados de Azure para PostgreSQL - Servidor Flexível utilizando o portal Azure

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Este artigo mostra-lhe como gerir a sua Base de Dados Azure para PostgreSQL - Servidor Flexível. As tarefas de gestão incluem o dimensionamento de cálculo e armazenamento, o reset da palavra-passe de administração e a visualização de detalhes do servidor.

## <a name="sign-in"></a>Iniciar sessão

Inicie sessão no [portal do Azure](https://portal.azure.com). Aceda ao seu recurso de servidor flexível no portal Azure.

## <a name="scale-compute-and-storage"></a>Cálculo e armazenamento em escala

Após a criação do servidor, pode escalar entre os [vários níveis de preços à](https://azure.microsoft.com/pricing/details/postgresql/) medida que as suas necessidades mudam. Também pode aumentar ou reduzir verticalmente a computação e a memória ao aumentar ou diminuir os vCores.

> [!NOTE]
> O armazenamento não pode ser reduzido para um valor mais baixo.

1. Selecione o seu servidor no portal Azure. **Selecione Compute + Armazenamento,** localizado na secção **Definições.**
2. Pode alterar o **Compute Tier** , **vCore**, **Storage** para escalar o servidor utilizando um nível de cálculo superior ou escalar dentro do mesmo nível, aumentando o armazenamento ou vCores para o valor pretendido.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="servidor flexível de armazenamento de escala":::

> [!Important]
> - O armazenamento não pode ser reduzido.
> - O escalonamento vCores provoca o reinício do servidor.

3. Selecione **OK** para guardar alterações.

## <a name="reset-admin-password"></a>Redefinir a palavra-passe de administração

Pode alterar a palavra-passe da função do administrador utilizando o portal Azure.

1. Selecione o seu servidor no portal Azure. Na janela **'Vista Geral'** **selecione redefinir a palavra-passe**.
2. Introduza uma nova senha e confirme a senha. A caixa de texto irá instruí-lo sobre os requisitos de complexidade da palavra-passe.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="redefinir a sua palavra-passe para servidor flexível":::

3. **Selecione Guardar** para guardar a nova palavra-passe.

## <a name="delete-a-server"></a>Excluir um servidor

Pode eliminar o seu servidor se já não precisar dele.

1. Selecione o seu servidor no portal Azure. Na janela **'Vista Geral'** selecione **Eliminar**.
2. Digite o nome do servidor na caixa de entrada para confirmar que pretende eliminar o servidor.

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="eliminar o servidor flexível":::

   > [!IMPORTANT]
   > Apagar um servidor é irreversível.

  > [!div class="mx-imgBorder"]
  > ![eliminar o servidor flexível](./media/howto-manage-server-portal/delete-server.png)  

3. Selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

- [Compreender backup e restaurar conceitos](concepts-backup-restore.md)
- [Sintonize e monitorize o servidor](concepts-monitoring.md)
