---
title: Gerir o servidor - Portal Azure - Base de Dados Azure para o MySQL Flexible Server
description: Saiba como gerir uma Base de Dados Azure para o servidor flexível MySQL a partir do portal Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7a01863b3a0c29e94550be67ca957655cff32660
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90937330"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Gerir uma Base de Dados Azure para o MySQL - Servidor flexível (Pré-visualização) utilizando o portal Azure


> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este artigo mostra-lhe como gerir a sua Base de Dados Azure para servidores flexíveis MySQL (Pré-visualização). As tarefas de gestão incluem o escalonamento de cálculo e armazenamento, a palavra-passe do administrador do servidor de repouso e a eliminação do seu servidor.

## <a name="sign-in"></a>Iniciar sessão
Inicie sessão no [portal do Azure](https://portal.azure.com). Aceda ao seu recurso de servidor flexível no portal Azure.

## <a name="scale-compute-and-storage"></a>Cálculo e armazenamento em escala

Após a criação do servidor, pode escalar entre os [vários níveis de preços à](https://azure.microsoft.com/pricing/details/mysql/) medida que as suas necessidades mudam. Também pode aumentar ou reduzir verticalmente a computação e a memória ao aumentar ou diminuir os vCores.

1. Selecione o seu servidor no portal Azure. **Selecione Compute + Armazenamento,** localizado na secção **Definições.**

2. Pode alterar o **Compute Tier**, **vCore**, **Storage** para escalar o servidor utilizando um nível de cálculo superior ou escalar dentro do mesmo nível, aumentando o armazenamento ou vCores para um valor que deseja.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="servidor flexível de armazenamento de escala":::

   > [!Important]
   > - O armazenamento não pode ser reduzido.
   > - O escalonamento vCores provoca o reinício do servidor.

3. Selecione **OK** para guardar alterações.

## <a name="reset-admin-password"></a>Redefinir a palavra-passe de administração

Pode alterar a palavra-passe da função do administrador utilizando o portal Azure.

1. Selecione o seu servidor no portal Azure. Na janela **'Vista Geral',** selecione **Redefinir a palavra-passe**.

2. Introduza uma nova senha e confirme a senha. A caixa de texto irá instruí-lo sobre os requisitos de complexidade da palavra-passe.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="redefinir a sua palavra-passe para servidor flexível":::

3. **Selecione Guardar** para guardar a nova palavra-passe.

## <a name="delete-a-server"></a>Excluir um servidor

Pode eliminar o seu servidor se já não precisar dele.

1. Selecione o seu servidor no portal Azure. Na janela **'Vista Geral'** selecione **Eliminar**.

2. Digite o nome do servidor na caixa de entrada para confirmar que pretende eliminar o servidor.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="eliminar o servidor flexível":::

   > [!NOTE]
   > Apagar um servidor é irreversível.

3. Selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos
- [Saiba como iniciar ou parar um servidor](how-to-stop-start-server-portal.md)
- [Saiba como restaurar um servidor](how-to-restore-server-portal.md)
- [Resolver problemas de ligação](how-to-troubleshoot-common-connection-issues.md)

